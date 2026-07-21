loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local ApiFTAP = loadstring(game:HttpGet("https://raw.githubusercontent.com/Oxwoey/FTAP-Module/refs/heads/main/Module/ModuleFTAP"))()

--[[
  Функция "Уничтожитель сервера" с опцией вайтлиста друзей.
  Эта функция предназначена для притягивания всех игроков (кроме, возможно, друзей)
  к локальному игроку с использованием механизма Blobman.

  Эта версия интегрирована в меню и использует его переменные состояния.
  Используйте с осторожностью, так как отсутствие ожидаемых объектов или ошибки
  при вызове FireServer могут вызвать остановку скрипта.
--]]
local isServerDestroyerEnabled = false
local isFriendsWhitelisted = false
local blobmans = {}
local activeConnections = {}

-- Функция для обновления списка Blobman'ов
local function updateBlobmans()
    blobmans = {}
    for _, folder in ipairs(workspace:GetChildren()) do
        if folder:IsA("Folder") and folder.Name:find("SpawnedInToys") then
            for _, blob in ipairs(folder:GetChildren()) do
                if blob:IsA("Model") and blob.Name == "CreatureBlobman" then
                    local root = blob:FindFirstChild("HumanoidRootPart")
                    if root then
                        local seatScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                        if seatScript then
                            table.insert(blobmans, {
                                model = blob,
                                root = root,
                                left = blob:FindFirstChild("LeftDetector"),
                                right = blob:FindFirstChild("RightDetector"),
                                leftWeld = blob:FindFirstChild("LeftDetector") and blob.LeftDetector:FindFirstChild("LeftWeld"),
                                rightWeld = blob:FindFirstChild("RightDetector") and blob.RightDetector:FindFirstChild("RightWeld"),
                                remote = seatScript:FindFirstChild("CreatureGrab")
                            })
                        end
                    end
                end
            end
        end
    end
    return #blobmans > 0
end

-- Вспомогательная функция для определения ближайшего Blobman'а
local function getClosestBlobman(targetPos, hand)
    local closest, minDist = nil, math.huge
    for _, blob in ipairs(blobmans) do
        local weld = hand == "Left" and blob.leftWeld or blob.rightWeld
        local detector = hand == "Left" and blob.left or blob.right
        if blob.remote and detector and weld then
            local dist = (blob.root.Position - targetPos).Magnitude
            if dist < minDist then
                closest = {remote = blob.remote, detector = detector, weld = weld}
                minDist = dist
            end
        end
    end
    return closest
end

local function processPlayer(player, hand, localPlayer)
    if not player or player == localPlayer then return false end
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return false end
    if isFriendsWhitelisted and localPlayer:IsFriendsWith(player.UserId) then return false end

    local root = player.Character.HumanoidRootPart
    local blob = getClosestBlobman(root.Position, hand)
    if not blob then return false end

    task.spawn(function()
	wait(0.5)
        local args = {blob.detector, root, blob.weld}
        pcall(function()
            blob.remote:FireServer(unpack(args))
        end)
    end)

    return true
end






local addedGui = {}
local conn = nil

local function isFriend(player)
	local localPlayer = game.Players.LocalPlayer
	return localPlayer and player and localPlayer:IsFriendsWith(player.UserId)
end

local function getClosestPartToPlayer(targetPlayer)
	local char = targetPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return nil end

	local root = char.HumanoidRootPart
	local closest = nil
	local minDist = math.huge

	for _, obj in pairs(workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PlayerCharacterLocationDetector" then
			local dist = (obj.Position - root.Position).Magnitude
			if dist < minDist then
				minDist = dist
				closest = obj
			end
		end
	end

	return closest
end

local function addLabel(part, whitelistFriends)
	if addedGui[part] then return end

	local localPlayer = game.Players.LocalPlayer
	if part == getClosestPartToPlayer(localPlayer) then return end

	for _, player in ipairs(game.Players:GetPlayers()) do
		if whitelistFriends and isFriend(player) then
			local closestToFriend = getClosestPartToPlayer(player)
			if part == closestToFriend then return end
		end
	end

	local gui = Instance.new("BillboardGui")
	gui.Name = "DetectorLabel"
	gui.Adornee = part
	gui.AlwaysOnTop = true
	gui.Size = UDim2.new(0, 150, 0, 40)
	gui.StudsOffset = Vector3.new(0, 3, 0)
	gui.Parent = part

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 1, 0)
	label.BackgroundTransparency = 1
	label.Text = "Player"
	label.Font = Enum.Font.SourceSansBold
	label.TextScaled = false
	label.TextSize = 18
	label.TextColor3 = Color3.new(0, 0, 0)
	label.TextStrokeTransparency = 0.3
	label.TextStrokeColor3 = Color3.new(1, 1, 1)
	label.Parent = gui

	addedGui[part] = gui
end

local function removeAllLabels()
	for _, gui in pairs(addedGui) do
		if gui then gui:Destroy() end
	end
	addedGui = {}
end

local function toggleServerDestroyer(enabled, whitelistFriends)
	for _, connection in pairs(activeConnections) do
		if typeof(connection) == "RBXScriptConnection" and connection.Connected then
			connection:Disconnect()
		end
	end
	activeConnections = {}

	isServerDestroyerEnabled = enabled
	isFriendsWhitelisted = whitelistFriends

	if isServerDestroyerEnabled then
		print("🧨 Server Destroyer Started. Whitelist Friends: " .. tostring(isFriendsWhitelisted))

		local localPlayer = game.Players.LocalPlayer
		if not localPlayer then
			warn("❌ Local player not found.")
			isServerDestroyerEnabled = false
			return
		end

		for _, part in pairs(workspace:GetDescendants()) do
			if part:IsA("BasePart") and part.Name == "PlayerCharacterLocationDetector" then
				addLabel(part, whitelistFriends)
			end
		end

		conn = workspace.DescendantAdded:Connect(function(obj)
			if obj:IsA("BasePart") and obj.Name == "PlayerCharacterLocationDetector" then
				addLabel(obj, whitelistFriends)
			end
		end)

		task.spawn(function()
			local playersAffectedCount = 0
			local countedBlackHoles = {}

			-- Отслеживание появления новых BlackHoleKick моделей
			task.spawn(function()
				while isServerDestroyerEnabled do
					for _, obj in ipairs(workspace:GetChildren()) do
						if obj:IsA("Model") and obj.Name == "BlackHoleKick" and not countedBlackHoles[obj] then
							countedBlackHoles[obj] = true
							playersAffectedCount += 1
							game.StarterGui:SetCore("SendNotification", {
								Title = "Player kicked",
								Text = "⚠️ BlackHoleKick detected!",
								Duration = 2
							})
						end
					end
					task.wait(0.5)
				end
			end)

			local blobmansFound = updateBlobmans()
			if not blobmansFound then
				warn("❌ No Blobman toys found. Make sure you have spawned them.")
				isServerDestroyerEnabled = false
				return
			end

			table.insert(activeConnections, workspace.ChildAdded:Connect(function(child)
				if child:IsA("Folder") and child.Name:find("SpawnedInToys") then
					task.wait(1)
					updateBlobmans()
				end
			end))

			while isServerDestroyerEnabled do
				local allPlayers = game.Players:GetPlayers()
				if #allPlayers <= 1 then
					task.wait(0.01)
					continue
				end

				if #blobmans == 0 then
					if not updateBlobmans() then
						task.wait(0.01)
						continue
					end
				end

				local groupLeft = {}
				local groupRight = {}

				for i, player in ipairs(allPlayers) do
					if player ~= localPlayer then
						if i % 2 == 0 then
							table.insert(groupLeft, player)
						else
							table.insert(groupRight, player)
						end
					end
				end

				for _, player in ipairs(groupLeft) do
					processPlayer(player, "Left", localPlayer)
					task.wait(0.01)
				end

				for _, player in ipairs(groupRight) do
					processPlayer(player, "Right", localPlayer)
					task.wait(0.01)
				end

				task.wait(0.01)
			end

			print("🛑 Server Destroyer Stopped. Players kicked: " .. playersAffectedCount)
		end)
	else
		print("🔕 Server Destroyer Stopping...")
		removeAllLabels()
		if conn then conn:Disconnect() conn = nil end
	end
end










local Window = Rayfield:CreateWindow({
   Name = "ZERO FTAP",
   Icon = 121938266516473, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "ZERO",
   LoadingSubtitle = "by Dolbaebs Team",
   Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = true,
      FolderName = "ZEROFTAP", -- Create a custom folder for your hub/game
      FileName = "ZERO"
   },

   Discord = {
      Enabled = true, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "j7bvWz4huW", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ ABCD would be ABCD
      RememberJoins = true -- Set this to false to make them join the discord every time they load it up
   },

   KeySystem = false, -- Set this to true to use our key system
   KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided", -- Use this to tell the user how to get a key
      FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = true, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
      Key = {"Hello"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})
local combatTab = Window:CreateTab("Combat", 4483362458) -- Title, Image
local defenseTab = Window:CreateTab("Defense", 4483362458) -- Title, Image
local lineTab = Window:CreateTab("Line", 4483362458)
local slidersTab = Window:CreateTab("Sliders to cnotrol target", 4483362458) -- Title, Image
local playerTab = Window:CreateTab("Player", 4483362458) -- Title, Image
local teleportTab = Window:CreateTab("Teleport", 4483362458) -- Title, Image
local keybindsTab = Window:CreateTab("Keybinds", 4483362458) -- Title, Image
local miscTab = Window:CreateTab("Misc", 4483362458) -- Title, Image
local blobmanTab = Window:CreateTab("Blobman", 4483362458) -- Title, Image
local aurasTab = Window:CreateTab("Auras", 4483362458) -- Title, Image
local autoTab = Window:CreateTab("Auto", 4483362458) -- Title, Image


    local function teleportToMouse()
        local player = game.Players.LocalPlayer
        local mouse = player:GetMouse()
        local character = player.Character
        local humanoid = character and character:FindFirstChild("Humanoid")
        local root = character and character:FindFirstChild("HumanoidRootPart")

        if not (root and humanoid and humanoid.Health > 0) then return end
        local targetPosition = mouse.Hit.Position
        local currentPosition = root.Position
        local maxDistance = 2000
        if mouse.Target and (targetPosition - currentPosition).Magnitude <= maxDistance then
            root.CFrame = CFrame.new(targetPosition + Vector3.new(0, 3, 0))
        end
    end

local Keybind = teleportTab:CreateKeybind({
   Name = "TP to mouse",
   CurrentKeybind = "T",
   HoldToInteract = false,
   Flag = "TPtomouse1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
   Callback = function(Keybind)
   teleportToMouse()
   end,
})

local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local paintPartsBackup = {}
local connections = {}

local function deleteAllPaintParts()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			local clone = obj:Clone()
			clone.Archivable = true
			paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
			obj:Destroy()
		end
	end
end

local function restorePaintParts()
	for _, data in pairs(paintPartsBackup) do
		if data.clone and data.parent then
			data.clone.Parent = data.parent
		end
	end
	paintPartsBackup = {}
end

local function watchNewPaintParts()
	table.insert(connections, Workspace.DescendantAdded:Connect(function(obj)
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			task.defer(function()
				if obj and obj.Parent then
					local clone = obj:Clone()
					clone.Archivable = true
					paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
					obj:Destroy()
				end
			end)
		end
	end))
end

local function disconnectWatchers()
	for _, conn in ipairs(connections) do
		if conn.Connected then conn:Disconnect() end
	end
	connections = {}
end

local function setTouchQuery(state)
	local char = Workspace:FindFirstChild(LocalPlayer.Name)
	if not char then return end
	for _, v in ipairs(char:GetChildren()) do
		if v:IsA("Part") or v:IsA("BasePart") then
			v.CanTouch = state
			v.CanQuery = state
		end
	end
end

defenseTab:CreateToggle({
	Name = "Delete PaintPlayerPart",
	CurrentValue = false,
	Flag = "PaintDeleteToggle",
	Callback = function(state)
		if state then
			deleteAllPaintParts()
			watchNewPaintParts()
			setTouchQuery(false)
		else
			restorePaintParts()
			disconnectWatchers()
			setTouchQuery(true)
		end
	end,
})


local waterCollisionToggle = miscTab:CreateToggle({
   Name = "Water Collision",
   CurrentValue = false,
   Flag = "WaterCollide",
   Callback = function(enabled)
      local oceanModel = workspace:FindFirstChild("Map")
         and workspace.Map:FindFirstChild("AlwaysHereTweenedObjects")
         and workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("Ocean")
         and workspace.Map.AlwaysHereTweenedObjects.Ocean:FindFirstChild("Object")
         and workspace.Map.AlwaysHereTweenedObjects.Ocean.Object:FindFirstChild("ObjectModel")

      if oceanModel then
         for _, child in ipairs(oceanModel:GetChildren()) do
            if child.Name == "Ocean" and child:IsA("BasePart") then
               child.CanCollide = enabled
            end
         end
      end
   end
})

local Keybind = keybindsTab:CreateKeybind({
   Name = "Anchor object",
   CurrentKeybind = "K",
   HoldToInteract = false,
   Flag = "Anchor",
   Callback = function()
      local original = workspace:FindFirstChild("GrabParts")
      if not original then
         warn("❌ GrabParts не найден.")
         return
      end

      local grabPart = original:FindFirstChild("GrabPart", true)
      if not grabPart or not grabPart:IsA("BasePart") then
         warn("❌ GrabPart не найден.")
         return
      end

      local wasCollide = grabPart.CanCollide
      grabPart.CanCollide = true
      task.wait(0.1)

      local targetModel = nil
      local touchingParts = grabPart:GetTouchingParts()

      if #touchingParts == 0 then
         warn("⚠️ GrabPart не касается ни одной части.")
         grabPart.CanCollide = wasCollide
         return
      end

      for _, part in ipairs(touchingParts) do
         if not part:IsDescendantOf(original) then
            local current = part
            while current and current ~= workspace do
               if current:IsA("Model") then
                  targetModel = current
                  break
               end
               current = current.Parent
            end
            if targetModel then break end
         end
      end

      grabPart.CanCollide = wasCollide

      if not targetModel then
         warn("❌ Модель для привязки не найдена.")
         return
      end

      if not targetModel.Parent then
         local found = false
         local connection
         connection = targetModel.AncestryChanged:Connect(function(_, parent)
            if parent then
               found = true
               connection:Disconnect()
            end
         end)

         local startTime = tick()
         while not found and tick() - startTime < 2 do
            task.wait(0.1)
         end

         if not found then
            warn("Не удалось дождаться появления модели в дереве.")
            return
         end
      end

      local existing = targetModel:FindFirstChild("CleanedGrabParts")
      if existing then
         local existingHighlight = targetModel:FindFirstChild("AnchorHighlight")
         if existingHighlight then
            existingHighlight:Destroy()
         end
         existing:Destroy()
         print("🧹 Старый CleanedGrabParts удалён из", targetModel.Name)
         return
      end

      local clone = original:Clone()
      clone.Name = "CleanedGrabParts"

      for _, desc in ipairs(clone:GetDescendants()) do
         if desc:IsA("BasePart") then
            desc.Transparency = 1
            desc.CanCollide = false

            local beam = desc:FindFirstChild("GrabBeam")
            if beam then beam:Destroy() end

            for _, sName in ipairs({"AttachSound1", "AttachSound", "BeamSound", "BeamSound1"}) do
               local sound = desc:FindFirstChild(sName)
               if sound then sound:Destroy() end
            end
         end
      end

      clone.Parent = targetModel

      local hl = Instance.new("Highlight")
      hl.Name = "AnchorHighlight"
      hl.FillColor = Color3.fromRGB(0, 85, 255)
      hl.FillTransparency = 0.4
      hl.OutlineColor = Color3.fromRGB(0, 170, 255)
      hl.OutlineTransparency = 0.7
      hl.Adornee = targetModel
      hl.Parent = targetModel

      local connection
      connection = game:GetService("RunService").Heartbeat:Connect(function()
         if not clone or not clone.Parent or not targetModel or not targetModel.Parent then
            if connection then connection:Disconnect() end
            return
         end

         if hl and hl.Parent then
            hl.Adornee = targetModel
         else
            connection:Disconnect()
         end
      end)

      print("Новый CleanedGrabParts и хайлайт добавлены в", targetModel:GetFullName())
   end
})

local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")

local speaker = Players.LocalPlayer

local function isR15(player)
    local humanoid = player.Character and player.Character:FindFirstChildWhichIsA("Humanoid")
    return humanoid and humanoid.RigType == Enum.HumanoidRigType.R15
end

miscTab:CreateToggle({
    Name = "Jerk Off Tool",
    CurrentValue = false,
    Flag = "JerkOffToggle",
    Callback = function(state)
        local humanoid = speaker.Character and speaker.Character:FindFirstChildWhichIsA("Humanoid")
        local backpack = speaker:FindFirstChildWhichIsA("Backpack")
        if not humanoid or not backpack then return end

        if state then
            -- Удаляем все старые "Jerk Off"
            for _, t in ipairs(backpack:GetChildren()) do
                if t:IsA("Tool") and t.Name == "Jerk Off" then
                    t:Destroy()
                end
            end

            -- Включаем инвентарь
            StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Backpack, true)

            local tool = Instance.new("Tool")
            tool.Name = "Jerk Off"
            tool.ToolTip = "in the stripped club. straight up \"jorking it\" . and by \"it\", haha, well. let's just say. My peanits."
            tool.RequiresHandle = false
            tool.Parent = backpack

            local jorkin = false
            local track = nil

            local function stop()
                jorkin = false
                if track then
                    track:Stop()
                    track = nil
                end
            end

            tool.Equipped:Connect(function() jorkin = true end)
            tool.Unequipped:Connect(stop)
            humanoid.Died:Connect(stop)

            task.spawn(function()
                while tool.Parent == backpack or tool.Parent == speaker.Character do
                    if not jorkin then task.wait() continue end

                    local isR = isR15(speaker)
                    if not track then
                        local anim = Instance.new("Animation")
                        anim.AnimationId = not isR and "rbxassetid://72042024" or "rbxassetid://698251653"
                        track = humanoid:LoadAnimation(anim)
                    end

                    track:Play()
                    track:AdjustSpeed(isR and 0.7 or 0.65)
                    track.TimePosition = 0.6
                    task.wait(0.1)

                    while track and track.TimePosition < (not isR and 0.65 or 0.7) do task.wait(0.1) end

                    if track then
                        track:Stop()
                        track = nil
                    end
                end
            end)
        else
            -- Выключаем
            for _, t in ipairs(backpack:GetChildren()) do
                if t:IsA("Tool") and t.Name == "Jerk Off" then
                    t:Destroy()
                end
            end
        end
    end,
})



-- Флаги для каждой ауры
local magneticAuraEnabled = false
local radiationAuraEnabled = false
local poisonAuraEnabled = false
local auraWhiteListEnabled = false

-- Добавление аур в aurasTab
aurasTab:CreateToggle({
    Name = "Magnetic Aura",
    CurrentValue = magneticAuraEnabled,
    Flag = "MagneticAura",
    Callback = function(state)
        magneticAuraEnabled = state
        ApiFTAP.MagneticAura(magneticAuraEnabled)
        print("Magnetic Aura: " .. tostring(magneticAuraEnabled))
    end,
})

aurasTab:CreateToggle({
    Name = "Radiation Aura",
    CurrentValue = radiationAuraEnabled,
    Flag = "RadiationAura",
    Callback = function(state)
        radiationAuraEnabled = state
        ApiFTAP.RadiationAura(radiationAuraEnabled)
        print("Radiation Aura: " .. tostring(radiationAuraEnabled))
    end,
})

aurasTab:CreateToggle({
    Name = "Poison Aura",
    CurrentValue = poisonAuraEnabled,
    Flag = "PoisonAura",
    Callback = function(state)
        poisonAuraEnabled = state
        ApiFTAP.PoisonAura(poisonAuraEnabled)
        print("Poison Aura: " .. tostring(poisonAuraEnabled))
    end,
})

aurasTab:CreateToggle({
    Name = "Aura Whitelist (Ignore Friends)",
    CurrentValue = auraWhiteListEnabled,
    Flag = "AuraWhiteList",
    Callback = function(state)
        auraWhiteListEnabled = state
        ApiFTAP.AuraWhiteList(auraWhiteListEnabled)
        print("Aura WhiteList: " .. tostring(auraWhiteListEnabled))
    end,
})

local antiGrabEnabled = false

-- Добавление антиграба в defenseTab
defenseTab:CreateToggle({
    Name = "Anti-Grab",
    CurrentValue = antiGrabEnabled,
    Flag = "AntiGrab",
    Callback = function(state)
        antiGrabEnabled = state
        ApiFTAP.AntiGrab(antiGrabEnabled)
        print("Anti-Grab: " .. tostring(antiGrabEnabled))
    end,
})

local toggleAntiBurn = false
local followConnection

defenseTab:CreateToggle({
	Name = "AntiBurn",
	CurrentValue = false,
	Flag = "AntiBurn",
	Callback = function(Value)
		toggleAntiBurn = Value

		if toggleAntiBurn then
			local Players = game:GetService("Players")
			local ReplicatedStorage = game:GetService("ReplicatedStorage")
			local RunService = game:GetService("RunService")
			local LocalPlayer = Players.LocalPlayer
			local SpawnToyRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
			local toyName = "FireExtinguisher"
			local toyModel = nil
			local toyPart = nil
			local folderName = LocalPlayer.Name.."SpawnedInToys"

			local function findOrSpawnToy()
				local folder = workspace:FindFirstChild(folderName)
				if folder then
					for _, obj in pairs(folder:GetChildren()) do
						if obj:IsA("Model") and obj.Name == toyName then
							toyPart = obj:FindFirstChild("ExtinguishPart")
							if toyPart then
								return obj
							end
						end
					end
				end

				local character = LocalPlayer.Character
				if not character then return nil end

				local root = character:FindFirstChild("HumanoidRootPart")
				if not root then return nil end

				local cframeInFront = root.CFrame * CFrame.new(3, 0, 0)
				local args = {
					toyName,
					cframeInFront,
					Vector3.new(0, 72.04, 0)
				}
				SpawnToyRemote:InvokeServer(unpack(args))
				return nil
			end

			local function disableCollision(model)
				for _, part in pairs(model:GetDescendants()) do
					if part:IsA("BasePart") then
						part.CanCollide = false
					end
				end
			end

			followConnection = RunService.Stepped:Connect(function()
				local character = LocalPlayer.Character
				if not character then return end

				local root = character:FindFirstChild("HumanoidRootPart")
				local humanoid = character:FindFirstChild("Humanoid")
				if not root or not humanoid then return end

				if not toyModel or not toyModel:IsDescendantOf(workspace) then
					toyModel = findOrSpawnToy()
				end

				if toyModel and toyPart then
					disableCollision(toyModel)

					local debounce = humanoid:FindFirstChild("FireDebounce")
					if debounce and debounce.Value == true then
						toyPart.CFrame = root.CFrame * CFrame.new(0, -5, 0)
					else
						toyPart.CFrame = root.CFrame * CFrame.new(0, -1000, 0)
					end
				end
			end)

			warn("AntiBurn активен")
		else
			if followConnection then
				followConnection:Disconnect()
				followConnection = nil
				warn("AntiBurn отключен")
			end
		end
	end
})

defenseTab:CreateToggle({
	Name = "AntiBlobman",
	CurrentValue = false,
	Flag = "AntiBlobman",
	Callback = function(Value)
		ApiFTAP.AntiBlobman(Value)
		if Value then
			warn("AntiBlobman активен")
		else
			warn("AntiBlobman отключен")
		end
	end
})

local UserInputService = game:GetService("UserInputService")
local Debris = game:GetService("Debris")

_G.strength = 400
local strengthConnection

local strengthSlider = combatTab:CreateSlider({
    Name = "Strength",
    Range = {300, 40000},
    Increment = 1,
    Suffix = "S",
    CurrentValue = _G.strength,
    Flag = "StrengthSlider",
    Callback = function(value)
        _G.strength = value
    end,
})

local strengthToggle = combatTab:CreateToggle({
    Name = "Strength",
    CurrentValue = false,
    Flag = "StrengthToggle",
    Callback = function(enabled)
        if enabled then
            strengthConnection = workspace.ChildAdded:Connect(function(model)
                if model.Name == "GrabParts" then
                    local partToImpulse = model.GrabPart.WeldConstraint.Part1
                    if partToImpulse then
                        local velocityObj = Instance.new("BodyVelocity", partToImpulse)
                        model:GetPropertyChangedSignal("Parent"):Connect(function()
                            if not model.Parent then
                                if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                                    velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * _G.strength
                                    Debris:AddItem(velocityObj, 1)
                                else
                                    velocityObj:Destroy()
                                end
                            end
                        end)
                    end
                end
            end)
        elseif strengthConnection then
            strengthConnection:Disconnect()
        end
    end,
})



local serverdestroyToggle = blobmanTab:CreateToggle({
   Name = "Server Destroy",
   CurrentValue = false,
   Flag = "Serverdestroyer", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
   Callback = function(Value)
   toggleServerDestroyer(Value, getgenv().ServerDestroyerWhitelist or false)
   end,
})

local whitelistToggle = blobmanTab:CreateToggle({
   Name = "Whitelist Friends",
   CurrentValue = false,
   Flag = "ServerWhitelist", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
   Callback = function(Value)
        getgenv().ServerDestroyerWhitelist = Value
   end,
})

local player = game:GetService("Players").LocalPlayer
local workspace = game:GetService("Workspace")

-- Храним уникальные идентификаторы использованных бананов
local usedBananas = {}

-- Задаём новый ЦФрейм для выкидывания банана очень далеко
local farCFrame = CFrame.new(10000, -10000, 10000) -- Очень далёкое место
local dropVector = Vector3.new(0, -20.34, 0)

-- Функция для получения уникального ID
local function generateUniqueId()
    return tostring(math.random(100000, 999999)) -- Генерируем случайный ID
end

-- ГЛАВНАЯ ПЕРЕМЕННАЯ СКРИПТА
_G.EAT_BANANAS = false  -- Глобальная переменная: true = съедаем бананы, false = не съедаем

-- Функция для обработки бананов
local function handleBanana(item)
    -- ГЛАВНАЯ ПРОВЕРКА - если переменная false, не едим бананы
    if not _G.EAT_BANANAS then
        return
    end

    -- Проверяем, есть ли уникальный ID для этого банана
    local bananaId = item:FindFirstChild("BananaId")
    if not bananaId then
        -- Если ID нет, создаём и сохраняем
        bananaId = Instance.new("StringValue")
        bananaId.Name = "BananaId"
        bananaId.Value = generateUniqueId() -- Присваиваем уникальный ID
        bananaId.Parent = item
    end

    -- Проверяем, использовался ли уже этот банан
    if not usedBananas[bananaId.Value] then
        usedBananas[bananaId.Value] = true -- Помечаем как использованный

        -- Применяем первый ремоут (схватывание банана)
        local holdPart = item:FindFirstChild("HoldPart")
        if holdPart then
            -- Проводим первый ремоут: съедаем банан
            local args1 = {item, player.Character}
            local success, err = pcall(function()
                holdPart:WaitForChild("HoldItemRemoteFunction"):InvokeServer(unpack(args1))
            end)
            if not success then
                warn("Ошибка при захвате банана: " .. err)
            end

            -- Применяем второй ремоут (выкидывание банана очень далеко)
            local args2 = {item, farCFrame, dropVector}
            local success2, err2 = pcall(function()
                holdPart:WaitForChild("DropItemRemoteFunction"):InvokeServer(unpack(args2))
            end)
            if not success2 then
                warn("Ошибка при выбрасывании банана: " .. err2)
            end
        end
    end
end

-- Функция для обработки папки с бананами
local function handleBananaFolder(folder)
    if not folder or not folder:IsA("Folder") then return end
    
    for _, item in pairs(folder:GetChildren()) do
        if item.Name == "FoodBanana" then
            handleBanana(item)
        end
    end
end

-- Функция для обработки всех папок в Workspace
local function scanWorkspace()
    for _, folder in pairs(workspace:GetChildren()) do
        if folder and string.find(folder.Name, "SpawnedInToys") then
            handleBananaFolder(folder)
        end
    end
end

-- Переменная для хранения цикла
local scanLoop = nil

-- Функция для запуска сканирования
local function startScanning()
    -- Если цикл уже запущен, останавливаем его
    if scanLoop then
        scanLoop:Disconnect()
        scanLoop = nil
    end
    
    -- Создаем новый цикл
    scanLoop = game:GetService("RunService").Heartbeat:Connect(function()
        scanWorkspace()
    end)
end

-- Функция для остановки сканирования
local function stopScanning()
    if scanLoop then
        scanLoop:Disconnect()
        scanLoop = nil
    end
end

-- Отслеживаем новые объекты
local childAddedConnection = nil

-- Функция для отслеживания новых объектов
local function startChildTracking()
    -- Если отслеживание уже запущено, останавливаем его
    if childAddedConnection then
        childAddedConnection:Disconnect()
        childAddedConnection = nil
    end
    
    -- Создаем новое отслеживание
    childAddedConnection = workspace.ChildAdded:Connect(function(child)
        if child and string.find(child.Name, "SpawnedInToys") then
            wait(0.1) -- Даем время для заполнения папки
            handleBananaFolder(child)
        end
    end)
end

-- Функция для остановки отслеживания
local function stopChildTracking()
    if childAddedConnection then
        childAddedConnection:Disconnect()
        childAddedConnection = nil
    end
end

-- Запускаем обе функции по умолчанию (так как _G.EAT_BANANAS = true)
startScanning()
startChildTracking()

-- Настройка переключателя в UI
local antiBananaToggle = defenseTab:CreateToggle({
   Name = "Anti Banana(Eat)",
   CurrentValue = false,
   Flag = "Anti Banana",
   Callback = function(Value)
       -- ИНВЕРТИРОВАННАЯ ЛОГИКА
       if Value then
           -- Переключатель ВКЛЮЧЕН - СЪЕДАЕМ бананы
           _G.EAT_BANANAS = true
           print("Anti Banana ВКЛ - бананы СЪЕДАЮТСЯ")
           
           -- Запускаем отслеживание
           startScanning()
           startChildTracking()
       else
           -- Переключатель ВЫКЛЮЧЕН - НЕ съедаем бананы
           _G.EAT_BANANAS = false
           print("Anti Banana ВЫКЛ - бананы НЕ съедаются")
           
           -- Останавливаем отслеживание
           stopScanning()
           stopChildTracking()
       end
   end,
})


miscTab:CreateToggle({
	Name = "🧨 Sink Legs Once (R6)",
	CurrentValue = false,
	Callback = function(state)
		if state then
			local player = game.Players.LocalPlayer
			local char = player.Character or player.CharacterAdded:Wait()
			local hum = char:WaitForChild("Humanoid")
			local replicated_storage = game:GetService("ReplicatedStorage")
			local ragdoll = replicated_storage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")
			local hrp = char:WaitForChild("HumanoidRootPart")

			-- Убрать hip height
			hum.HipHeight = 0

			-- Телепортнуть и отсоединить ноги
			local function sinkLeg(partName)
				local part = char:FindFirstChild(partName)
				if part then
					local joint = part:FindFirstChildWhichIsA("Motor6D")
					if joint then joint:Destroy() end
					part.CFrame = CFrame.new(hrp.Position + Vector3.new(5, -59999, 0))
				end
			end

			sinkLeg("Left Leg")
			sinkLeg("Right Leg")

			-- Ragdoll
			task.spawn(function()
				for i = 1, 10 do
					pcall(function()
						ragdoll:FireServer(hrp, 1)
					end)
					task.wait(0.05)
				end
			end)

			-- Спавн предмета рядом
			local spawnArgs = {
				"ChildrensTableBenchSmall",
				CFrame.new(hrp.Position + Vector3.new(3, 0, 0)),
				Vector3.new(0, 53.82, 0)
			}
			replicated_storage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"):InvokeServer(unpack(spawnArgs))
		end
	end
})






    local function setupAntiBarrier()
        local toggle = createToggle(otherTab, "Anti Barrier", 3, function(enabled)
            local plots = workspace:FindFirstChild("Plots")
            if not plots then return end
            for _, plot in ipairs(plots:GetChildren()) do
                local barrierModel = plot:FindFirstChild("Barrier")
                if barrierModel then
                    for _, part in ipairs(barrierModel:GetChildren()) do
                        if part:IsA("BasePart") and part.Name == "PlotBarrier" then
                            part.CanCollide = not enabled
                        end
                    end
                end
            end
        end)
    end


local Players = game:GetService("Players")
local player = Players.LocalPlayer

local antiLagRunning = false

defenseTab:CreateToggle({
    Name = "AntiLag",
    CurrentValue = false,
    Flag = "AntiLag",
    Callback = function(state)
        antiLagRunning = state

        if state then
            task.spawn(function()
                while antiLagRunning do
                    local scriptObj = player:FindFirstChild("PlayerScripts"):FindFirstChild("CharacterAndBeamMove")
                    if scriptObj and scriptObj:IsA("LocalScript") then
                        scriptObj.Disabled = true
                    end
                    task.wait(0.2)
                end
            end)
        else
            local scriptObj = player:FindFirstChild("PlayerScripts"):FindFirstChild("CharacterAndBeamMove")
            if scriptObj and scriptObj:IsA("LocalScript") then
                scriptObj.Disabled = false
            end
        end
    end
})


local replicated_storage = game:GetService("ReplicatedStorage")
local character_events = replicated_storage:WaitForChild("CharacterEvents")
local ragdoll = character_events:WaitForChild("RagdollRemote")

local ragdollRunning = false
local ragdollLoopThread

local function start_ragdoll()
    if ragdollRunning then return end
    ragdollRunning = true
    ragdollLoopThread = task.spawn(function()
        while ragdollRunning do
            pcall(function()
                local hrp = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    ragdoll:FireServer(hrp, 0)
                end
            end)
            task.wait(0.05)
        end
    end)
end

local function stop_ragdoll()
    ragdollRunning = false
end

-- super_antigrab
local function super_antisruchi()
    local player = game.Players.LocalPlayer
    local toys = workspace:FindFirstChild(player.Name.."SpawnedInToys")
    if not toys then return end

    for _, blob in pairs(toys:GetChildren()) do
        if blob.Name == "CreatureBlobman" then
            local detector = blob:FindFirstChild("RightDetector")
            local weld = detector and detector:FindFirstChild("RightWeld")
            local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
            if detector and weld and hrp then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, hrp, weld)
                replicated_storage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"):FireServer(
                    hrp,
                    CFrame.new(detector.Position)
                )
                -- Прыжок
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
                stop_ragdoll()
            end
        end
    end
end

-- UI
local antisruchiParagraph = defenseTab:CreateParagraph({Title = "Anti Sruchi guide", Content = "To activate Anti Sruchi (Gucci anti grab), press the first button (1), spawn Blobman, sit on it, and then press the second button (2)."})

defenseTab:CreateButton({
   Name = "1",
   Callback = function()
       start_ragdoll()
   end,
})

defenseTab:CreateButton({
   Name = "2",
   Callback = function()
       super_antisruchi()
   end,
})


miscTab:CreateToggle({
	Name = "Anti Barrier",
	CurrentValue = false,
	Flag = "AntiBarrier",
	Callback = function(enabled)
		local plots = workspace:FindFirstChild("Plots")
		if not plots then return end

		for _, plot in ipairs(plots:GetChildren()) do
			local barrierModel = plot:FindFirstChild("Barrier")
			if barrierModel then
				for _, part in ipairs(barrierModel:GetChildren()) do
					if part:IsA("BasePart") and part.Name == "PlotBarrier" then
						part.CanCollide = not enabled
					end
				end
			end
		end
	end,
})

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local blobmanTarget = nil
local dropdown

local function updatePlayerList()
	local playerList = {}
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= Players.LocalPlayer then
			table.insert(playerList, plr.Name)
		end
	end
	if dropdown then
		dropdown:Refresh(playerList)
	end
end

dropdown = teleportTab:CreateDropdown({
	Name = "Target Player",
	Options = {},
	CurrentOption = "",
	Flag = "TargetPlayerForBlobman",
	Callback = function(option)
		blobmanTarget = option
	end,
})

task.spawn(function()
	while true do
		updatePlayerList()
		task.wait(5)
	end
end)

teleportTab:CreateButton({
	Name = "Bring Target with Blobman",
	Callback = function()
		if not blobmanTarget or blobmanTarget == "" then
			warn("⚠️ Select a target first")
			return
		end

		local localPlayer = Players.LocalPlayer
		local targetPlayer = Players:FindFirstChild(blobmanTarget)
		if not targetPlayer or not targetPlayer.Character then
			warn("❌ Target not found")
			return
		end

		local targetHRP = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
		if not targetHRP then
			warn("❌ Target HRP not found")
			return
		end

		local path = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
		if not path then warn("❌ SpawnedInToys not found") return end

		local blobman = path:FindFirstChild("CreatureBlobman")
		if not blobman then warn("❌ Blobman not found") return end

		local detector = blobman:FindFirstChild("LeftDetector")
		local weld = detector and detector:FindFirstChild("LeftWeld")
		local remote = blobman:FindFirstChild("BlobmanSeatAndOwnerScript") and blobman.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")

		if not (detector and weld and remote) then
			warn("❌ Detector/Weld/Remote missing")
			return
		end

		local root = blobman:FindFirstChild("HumanoidRootPart")
		if root then
			root.CFrame = targetHRP.CFrame * CFrame.new(0, 0, -4)
		end

		task.wait(0.2)
		remote:FireServer(detector, targetHRP, weld)
	end
})



local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer
local playerCharacter = player.Character or player.CharacterAdded:Wait()
local toysFolder = workspace:WaitForChild(player.Name .. "SpawnedInToys")

local ragdollLoopRunning = false

local function ragdollAll()
    while ragdollLoopRunning do
        local success, err = pcall(function()
            if not toysFolder:FindFirstChild("FoodBanana") then
                local spawnArgs = {
                    "FoodBanana",
                    CFrame.new(-48.275676, -5.678343, -60.897686, -0.368124, 0.319957, -0.872990, 0, 0.938925, 0.344122, 0.929777, 0.126680, -0.345641),
                    Vector3.new(0, -111.6, 0)
                }
                ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"):InvokeServer(unpack(spawnArgs))
            end

            local banana = toysFolder:WaitForChild("FoodBanana")

            local holdArgs = { banana, playerCharacter }
            banana:WaitForChild("HoldPart"):WaitForChild("HoldItemRemoteFunction"):InvokeServer(unpack(holdArgs))

            local useArgs = { banana }
            ReplicatedStorage:WaitForChild("HoldEvents"):WaitForChild("Use"):FireServer(unpack(useArgs))

            task.wait(3)

            local dropArgs = {
                banana,
                CFrame.new(-39.596122, -8.533255, -64.548515),
                Vector3.new(0, -86.76, 0)
            }
            banana:WaitForChild("HoldPart"):WaitForChild("DropItemRemoteFunction"):InvokeServer(unpack(dropArgs))

            local bananaPeel
            for _, part in pairs(banana:GetChildren()) do
                if part.Name == "BananaPeel" and part:FindFirstChild("TouchInterest") then
                    part.Size = Vector3.new(10, 10, 10)
                    part.Transparency = 1
                    bananaPeel = part
                    break
                end
            end

            if not bananaPeel then return end

            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyPosition.Parent = banana.Main

            while ragdollLoopRunning do
                for _, p in pairs(Players:GetPlayers()) do
                    pcall(function()
                        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                            bananaPeel.Position = p.Character.HumanoidRootPart.Position
                            bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
                        end
                    end)
                end
                task.wait()
            end

            bodyPosition:Destroy()
        end)

        if not success then
            warn("Error in ragdollAll: " .. tostring(err))
        end

        task.wait(1)
    end
end

miscTab:CreateToggle({
    Name = "Banana Ragdoll Loop",
    CurrentValue = false,
    Flag = "BananaRagdoll",
    Callback = function(Value)
        ragdollLoopRunning = Value
        if Value then
            task.spawn(ragdollAll)
        end
    end
})

miscTab:CreateToggle({
    Name = "Unlock Third Person",
    CurrentValue = false,
    Flag = "ThirdPersonToggle",
    Callback = function(enabled)
        local player = game.Players.LocalPlayer
        if enabled then
            player.CameraMode = Enum.CameraMode.Classic
            player.CameraMaxZoomDistance = 1000
            player.CameraMinZoomDistance = 0.5
        else
            player.CameraMode = Enum.CameraMode.LockFirstPerson
            player.CameraMaxZoomDistance = 0.5
            player.CameraMinZoomDistance = 0.5
        end
    end,
})

local player = game:GetService("Players").LocalPlayer

function VoidGrabbedLimbs()
	print("Запустил VoidGrabbedLimbs")

	local character = player.Character or player.CharacterAdded:Wait()
	local grabModel = workspace:FindFirstChild("GrabParts")
	if not grabModel then 
		print("Нет GrabParts")
		return 
	end

	for _, grabPart in ipairs(grabModel:GetChildren()) do
		if grabPart:IsA("BasePart") then
			local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
			if weld and weld.Part1 then
				local targetChar = weld.Part1:FindFirstAncestorOfClass("Model")
				if targetChar and targetChar ~= character then
					print("Нашел модель с конечностями: ", targetChar.Name)

					local limbs = {
						targetChar:FindFirstChild("Left Arm"),
						targetChar:FindFirstChild("Right Arm"),
						targetChar:FindFirstChild("Left Leg"),
						targetChar:FindFirstChild("Right Leg")
					}

					local allLimbs = true
					for _, limb in ipairs(limbs) do
						if not (limb and limb:IsA("BasePart")) then
							allLimbs = false
							break
						end
					end

					if allLimbs then
						print("Телепортирую конечности!")
						for _, limb in ipairs(limbs) do
							limb.CFrame = CFrame.new(0, -59990, 0)
						end
					else
						print("Не все конечности найдены")
					end
				end
			end
		end
	end
end

local player = game:GetService("Players").LocalPlayer

local function teleportLimb(limbName)
	local character = player.Character or player.CharacterAdded:Wait()
	local grabModel = workspace:FindFirstChild("GrabParts")
	if not grabModel then return end

	for _, grabPart in ipairs(grabModel:GetChildren()) do
		if not grabPart:IsA("BasePart") then continue end
		local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
		if not (weld and weld.Part1) then continue end

		local targetChar = weld.Part1:FindFirstAncestorOfClass("Model")
		if targetChar and targetChar ~= character then
			local limb = targetChar:FindFirstChild(limbName)
			if limb and limb:IsA("BasePart") then
				limb.CFrame = CFrame.new(0, -59990, 0)
			end
			return
		end
	end
end


function VoidLeftArm() teleportLimb("Left Arm") end
function VoidRightArm() teleportLimb("Right Arm") end
function VoidLeftLeg() teleportLimb("Left Leg") end
function VoidRightLeg() teleportLimb("Right Leg") end


local destroyplayerKeybind = keybindsTab:CreateKeybind({
	Name = "Destroy Player(LeftArm)",
	CurrentKeybind = "C",
	HoldToInteract = false,
	Flag = "DestroyPlayer",
	Callback = function()
		print("Нажата клавиша для уничтожения игрока!")
		VoidLeftArm()
	end,
})

local destroyplayer1Keybind = keybindsTab:CreateKeybind({
	Name = "Destroy Player(RightArm)",
	CurrentKeybind = "V",
	HoldToInteract = false,
	Flag = "DestroyPlayer1",
	Callback = function()
		print("Нажата клавиша для уничтожения игрока!")
		VoidRightArm()
	end,
})

local destroyplayer2Keybind = keybindsTab:CreateKeybind({
	Name = "Destroy Player(LeftLeg)",
	CurrentKeybind = "B",
	HoldToInteract = false,
	Flag = "DestroyPlayer2",
	Callback = function()
		print("Нажата клавиша для уничтожения игрока!")
		VoidLeftLeg()
	end,
})

local destroyplayer3Keybind = keybindsTab:CreateKeybind({
	Name = "Destroy Player(RightLeg)",
	CurrentKeybind = "N",
	HoldToInteract = false,
	Flag = "DestroyPlayer3",
	Callback = function()
		print("Нажата клавиша для уничтожения игрока!")
		VoidRightLeg()
	end,
})

local destroyplayer4Keybind = keybindsTab:CreateKeybind({
	Name = "Destroy Player",
	CurrentKeybind = "Z",
	HoldToInteract = false,
	Flag = "DestroyPlayer4",
	Callback = function()
		print("Нажата клавиша для уничтожения игрока!")
		VoidGrabbedLimbs()
	end,
})

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local selectedTarget = nil
local dropdown

local function updatePlayerList()
	local playerList = {}
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= Players.LocalPlayer then
			table.insert(playerList, plr.Name)
		end
	end
	if dropdown then
		dropdown:Refresh(playerList)
	end
end

dropdown = teleportTab:CreateDropdown({
	Name = "Target Player",
	Options = {},
	CurrentOption = "",
	Flag = "TargetPlayer",
	Callback = function(option)
		selectedTarget = option[1]
	end,
})

task.spawn(function()
	while true do
		updatePlayerList()
		task.wait(10)
	end
end)

teleportTab:CreateButton({
	Name = "Bring Selected Player",
	Callback = function()
		if not selectedTarget or selectedTarget == "" then
			warn("Select a player first!")
			return
		end

		local localPlayer = Players.LocalPlayer
		local character = localPlayer.Character or localPlayer.CharacterAdded:Wait()
		local root = character:FindFirstChild("HumanoidRootPart")
		if not root then return end

		local targetPlayer = Players:FindFirstChild(selectedTarget)
		if not targetPlayer then
			warn("Player not found")
			return
		end

		local targetChar = targetPlayer.Character
		if not targetChar then
			warn("Target character not found")
			return
		end

		local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
		local targetHumanoid = targetChar:FindFirstChildOfClass("Humanoid")
		if not targetHRP or not targetHumanoid then
			warn("Target missing HRP or Humanoid")
			return
		end

		local originalCFrame = root.CFrame

		-- Телепортируем локального игрока рядом с целью (сбоку)
		root.CFrame = targetHRP.CFrame * CFrame.new(3, 0, 0)

		task.wait(0.1)

		-- Остановка движения цели
		targetHRP.Velocity = Vector3.zero
		targetHRP.RotVelocity = Vector3.zero

		for _, obj in pairs(targetHRP:GetChildren()) do
			if obj:IsA("BodyVelocity") or obj:IsA("BodyAngularVelocity") then
				obj:Destroy()
			end
		end

		-- Принудительно отключаем физику тела
		targetHumanoid.PlatformStand = true
		task.delay(0.3, function()
			-- Возврат к обычному состоянию
			targetHumanoid.PlatformStand = false
		end)

		task.wait(0.1)

		-- Смена владельца сети
		local setNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
		setNetworkOwner:FireServer(targetHRP, root.CFrame)

		task.wait(0.1)

		-- Телепорт локального игрока назад
		root.CFrame = originalCFrame

		-- Цель ставим перед локальным игроком
		targetHRP.CFrame = CFrame.new(
			originalCFrame.Position + originalCFrame.LookVector * 3,
			originalCFrame.Position
		)

		-- Удаление GrabLine
		local destroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
		destroyGrabLine:FireServer(targetHRP)
	end,
})







-- local Players = game:GetService("Players")
-- local ReplicatedStorage = game:GetService("ReplicatedStorage")
-- local RagdollRemote = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

-- local running = false
-- local grabThread

-- combatTab:CreateToggle({
-- 	Name = "Ragdoll Grab",
-- 	CurrentValue = false,
-- 	Flag = "RagdollGrabToggle",
-- 	Callback = function(state)
-- 		running = state

-- 		if state then
-- 			grabThread = task.spawn(function()
-- 				print("Ragdoll Grab активирован")
-- 				while running do
-- 					local grabParts = workspace:FindFirstChild("GrabParts") or workspace:WaitForChild("GrabParts", 5)
-- 					if not grabParts then task.wait(0.1) continue end

-- 					local grabPart = grabParts:FindFirstChild("GrabPart")
-- 					if not grabPart then task.wait(0.1) continue end

-- 					local weld = grabPart:FindFirstChildWhichIsA("WeldConstraint")
-- 					if not weld or not weld.Part1 then task.wait(0.1) continue end

-- 					local current = weld.Part1
-- 					local hrp = nil

-- 					while current and current.Parent do
-- 						if current:FindFirstChild("HumanoidRootPart") then
-- 							hrp = current:FindFirstChild("HumanoidRootPart")
-- 							break
-- 						end
-- 						current = current.Parent
-- 					end

-- 					if hrp then
-- 						pcall(function()
-- 							RagdollRemote:FireServer(hrp, 1)
-- 						end)
-- 					end

-- 					task.wait(0.1)
-- 				end
-- 			end)
-- 		else
-- 			print("Ragdoll Grab выключен")
-- 			if grabThread then
-- 				task.cancel(grabThread)
-- 				grabThread = nil
-- 			end
-- 		end
-- 	end
-- })


local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local localPlayer = Players.LocalPlayer
local mouse = localPlayer:GetMouse()

local holding = false
local loopConn = nil
local sliders = {}

local function getTargetedPlayer()
	local target = mouse.Target
	if not target then return nil end
	local model = target:FindFirstAncestorOfClass("Model")
	if model and Players:GetPlayerFromCharacter(model) then
		local plr = Players:GetPlayerFromCharacter(model)
		local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
		local myHrp = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
		if hrp and myHrp and (hrp.Position - myHrp.Position).Magnitude <= 50 then
			return plr
		end
	end
	return nil
end

local offsetValues = {
	LeftArm = {X = -2, Y = 0.5, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
	RightArm = {X = 2, Y = 0.5, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
	LeftLeg = {X = -0.5, Y = -2, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
	RightLeg = {X = 0.5, Y = -2, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
	Head = {X = 0, Y = 1.5, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
	Torso = {X = 0, Y = 0, Z = 0, RotX = 0, RotY = 0, RotZ = 0},
}

local function createSliders()
	if sliders.Created then return end
	sliders.Created = true

	local parts = {"LeftArm", "RightArm", "LeftLeg", "RightLeg", "Head", "Torso"}
	for _, part in ipairs(parts) do
		slidersTab:CreateSection(part .. " Position")
		sliders[part] = {}
		for _, axis in ipairs({"X", "Y", "Z"}) do
			sliders[part][axis] = slidersTab:CreateSlider({
				Name = part .. " " .. axis,
				Range = {-5, 5},
				Increment = 0.1,
				CurrentValue = offsetValues[part][axis],
				Callback = function(val)
					offsetValues[part][axis] = val
				end
			})
		end
		slidersTab:CreateSection(part .. " Rotation")
		for _, rot in ipairs({"RotX", "RotY", "RotZ"}) do
			sliders[part][rot] = slidersTab:CreateSlider({
				Name = part .. " " .. rot,
				Range = {-180, 180},
				Increment = 1,
				CurrentValue = offsetValues[part][rot],
				Callback = function(val)
					offsetValues[part][rot] = val
				end
			})
		end
	end
end

local function applyInvertedT(player)
	local char = player.Character
	if not char then return end

	local torso = char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
	local head = char:FindFirstChild("Head")
	local lArm = char:FindFirstChild("Left Arm")
	local rArm = char:FindFirstChild("Right Arm")
	local lLeg = char:FindFirstChild("Left Leg")
	local rLeg = char:FindFirstChild("Right Leg")

	if not torso or not head or not lArm or not rArm or not lLeg or not rLeg then return end

	for _, part in {head, lArm, rArm, lLeg, rLeg, torso} do
		part.Anchored = false
		part.CanCollide = false
		part.Massless = true
	end

	createSliders()

	if loopConn then loopConn:Disconnect() end

	loopConn = RunService.Heartbeat:Connect(function()
		local root = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
		if not root then return end

		local baseCF = root.CFrame * CFrame.new(0, -2, -4)

		local function apply(part, values)
			part.CFrame = baseCF
				* CFrame.new(values.X, values.Y, values.Z)
				* CFrame.Angles(
					math.rad(values.RotX),
					math.rad(values.RotY),
					math.rad(values.RotZ)
				)
		end

		apply(torso, offsetValues.Torso)
		apply(head, offsetValues.Head)
		apply(lArm, offsetValues.LeftArm)
		apply(rArm, offsetValues.RightArm)
		apply(lLeg, offsetValues.LeftLeg)
		apply(rLeg, offsetValues.RightLeg)

		local hrp = char:FindFirstChild("HumanoidRootPart")
		if hrp then
			ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"):FireServer(hrp, baseCF)
		end
	end)
end


keybindsTab:CreateKeybind({
	Name = "Transform to Inverted T",
	CurrentKeybind = "Q",
	HoldToInteract = false,
	Flag = "TransformKey",
	Callback = function()
		if holding then
			if loopConn then loopConn:Disconnect() loopConn = nil end
			holding = false
			return
		end

		local target = getTargetedPlayer()
		if target then
			holding = true
			applyInvertedT(target)
		end
	end,
})






local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CreateGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine")

local localPlayer = Players.LocalPlayer
local lagEnabled = false
local lineSpamEnabled = true
local lagSpeed = 0.1

lineTab:CreateSlider({
	Name = "Lag Speed",
	Range = {0.01, 1},
	Increment = 0.01,
	CurrentValue = lagSpeed,
	Flag = "LagSpeedSlider",
	Callback = function(val)
		lagSpeed = val
	end,
})

lineTab:CreateToggle({
	Name = "Enable Line Lag",
	CurrentValue = true,
	Flag = "LineSpamToggle",
	Callback = function(state)
		lineSpamEnabled = state
	end,
})

lineTab:CreateToggle({
	Name = "Server Lag",
	CurrentValue = false,
	Flag = "ServerLagToggle",
	Callback = function(state)
		lagEnabled = state

		if lagEnabled then
			task.spawn(function()
				while lagEnabled do
					if lineSpamEnabled then
						for _, player in ipairs(Players:GetPlayers()) do
							if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Head") then
								local head = player.Character.Head
								local cf = CFrame.new(
									head.Position + Vector3.new(0, -0.3, -0.5),
									head.Position + Vector3.new(0, 0, -1)
								)

								pcall(function()
									CreateGrabLine:FireServer(head, cf)
								end)
							end
						end
					end
					task.wait(lagSpeed)
				end
			end)
		end
	end
})


local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer

local killGrabT = nil

local function killGrabF()
	local char = localPlayer.Character or localPlayer.CharacterAdded:Wait()
	local hrp = char:WaitForChild("HumanoidRootPart")
	local hum = char:WaitForChild("Humanoid")

	workspace.ChildAdded:Connect(function(e)
		if e.Name == "GrabParts" and killGrabT == true and e:FindFirstChild("GrabPart") then
			local part1 = e.GrabPart:FindFirstChild("WeldConstraint") and e.GrabPart.WeldConstraint.Part1
			if part1 and part1.Parent and part1.Parent.Name ~= char.Name then
				local targetHumanoid = part1.Parent:FindFirstChildOfClass("Humanoid")
				if targetHumanoid then
					targetHumanoid.Health = 0
				end
			end
		end
	end)
end

combatTab:CreateToggle({
	Name = "Kill Grab",
	CurrentValue = false,
	Flag = "KillGrabParts",
	Callback = function(state)
		killGrabT = state
		if state == true then
			killGrabF()
		end
	end
})



-- local masslessgrab = true

-- local masslessgrabtoggle = combatTab:CreateToggle({
--    Name = "Massless Grab",
--    CurrentValue = false,
--    Flag = "MasslessGrab", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
--    Callback = function(state)
--    masslessgrab = state
--    end,
-- })








local ultragrabbb = false

combatTab:CreateToggle({
	Name = "Massless Grab",
	CurrentValue = false,
	Flag = "MasslessGrab",
	Callback = function(state)
		ultragrabbb = state
	end,
})

workspace.ChildAdded:Connect(function(model) -- neon
	if model.Name == "GrabParts" and ultragrabbb then
		local dragPart = model:FindFirstChild("DragPart")
		if dragPart then
			dragPart.Color = Color3.fromRGB(255, 0, 0)
			dragPart.Transparency = 1
			dragPart.Material = Enum.Material.Neon

			local ao = dragPart:FindFirstChild("AlignOrientation")
			local ap = dragPart:FindFirstChild("AlignPosition")

			if ao then
				ao.Responsiveness = 200
				ao.MaxTorque = math.huge
			end
			if ap then
				ap.MaxAxesForce = Vector3.new(math.huge, math.huge, math.huge)
				ap.MaxForce = math.huge
				ap.Responsiveness = 200
			end
		end
	end
end)





local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local deleteObject = keybindsTab:CreateKeybind({
	Name = "Удалить объект под прицелом",
	CurrentKeybind = "R",
	HoldToInteract = false,
	Flag = "DeleteLookedUnanchored",
	Callback = function()
		local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
		local hrp = character:FindFirstChild("HumanoidRootPart")
		if not hrp then return end

		local rayOrigin = Camera.CFrame.Position
		local rayDirection = Camera.CFrame.LookVector * 100

		local params = RaycastParams.new()
		params.FilterDescendantsInstances = {character}
		params.FilterType = Enum.RaycastFilterType.Blacklist

		local result = Workspace:Raycast(rayOrigin, rayDirection, params)

		if result then
			local part = result.Instance
			if part:IsA("BasePart") and not part.Anchored and (part.Position - hrp.Position).Magnitude <= 50 then
				local originalCFrame = part.CFrame

				-- Применить SetNetworkOwner
				ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"):FireServer(part, originalCFrame)

				-- Телепорт по X и немного вниз по Y
				part.CFrame = originalCFrame + Vector3.new(99999, 100, 0)
			end
		end
	end,
})














miscTab:CreateButton({
	Name = "Drop UFO",
	Callback = function()
		local playerName = game.Players.LocalPlayer.Name
		local blobman = workspace:WaitForChild(playerName .. "SpawnedInToys"):WaitForChild("CreatureBlobman")
		local ufoLaser = workspace.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel:GetChildren()[15]

		local args = {
			blobman:WaitForChild("LeftDetector"),
			ufoLaser,
			blobman:WaitForChild("LeftDetector"):WaitForChild("LeftWeld")
		}

		blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
	end
})



Rayfield:LoadConfiguration()