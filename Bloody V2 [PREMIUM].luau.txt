local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

--<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>
--<font face="GothamBlack" color="rgb(255,0,0)">OP</font>
--<font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>
--<font face="GothamBlack" color="rgb(255,251,0)">BLOBMAN</font>

-- FULL ADMIN SYSTEM
------------------------------------------------------------------
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TextChatService = game:GetService("TextChatService")

local LocalPlayer = Players.LocalPlayer

local superAdmins = {
   ["lax380"] = true,
   ["fartitutatu10"] = true
}

local admins = {
   ["aa"] = true,
   ["abc"] = true
}

local allowedUsers = {
   ["1L0L0L1L1L0"] = true,--r2
   ["78cvzl"] = true,--gru
   ["RandomHubBestHubFrFr"] = true,--gru
   ["ratwowee"] = true,--gru
   ["MSKCNCHEN_TH"] = true,--gru
   ["SuPraa006"] = true, -- cookie
   ["SDaxio_YouTuBe"] = true, -- sdaxio
   ["du1234test1234"] = true
}

if not (superAdmins[LocalPlayer.Name] or admins[LocalPlayer.Name] or allowedUsers[LocalPlayer.Name]) then
	return
end

local function getRole(name)
	if superAdmins[name] then
		return "superadmin"
	elseif admins[name] then
		return "admin"
	elseif allowedUsers[name] then
		return "allowed"
	else
		return "user"
	end
end

local role = getRole(LocalPlayer.Name)
local spin = false
local spinTarget = nil
local generalChannel = TextChatService.TextChannels:WaitForChild("RBXGeneral")

local function sendToChat(text)
	generalChannel:SendAsync(text)
end

if role == "admin" or role == "allowed" then
	sendToChat("🩸 Bloody V2 [PREMIUM] 🩸")
end

local function toggleBlock(player, enable)
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	if enable then
		if not hrp:FindFirstChild("Block") then
			local bv = Instance.new("BodyVelocity")
			bv.Name = "Block"
			bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
			bv.Velocity = Vector3.zero
			bv.Parent = hrp
		end
	else
		local bv = hrp:FindFirstChild("Block")
		if bv then bv:Destroy() end
	end
end

local function canAffect(senderRole, targetRole)
	if senderRole == "superadmin" then
		return targetRole ~= "superadmin"
	elseif senderRole == "admin" then
		return targetRole == "allowed" or targetRole == "user"
	else
		return false
	end
end

local function getTargetPlayer(sender, targetName)
	if not targetName then return nil end
	local plr = Players:FindFirstChild(targetName)
	if plr and canAffect(getRole(sender.Name), getRole(plr.Name)) then
		return plr
	end
	return nil
end

TextChatService.OnIncomingMessage = function(message)
	local src = message.TextSource
	if not src then return end
	local sender = Players:GetPlayerByUserId(src.UserId)
	if not sender then return end
	local senderRole = getRole(sender.Name)
	if senderRole == "allowed" or senderRole == "user" then return end

	local text = message.Text or ""
	local args = {}
	for word in text:gmatch("%S+") do
		table.insert(args, word)
	end
	if #args < 2 then return end

	local cmd = args[1]:lower()
	local targetName = args[2]
	local target = getTargetPlayer(sender, targetName)
	if not target then return end

	if cmd == "!chat" then
		if #args < 3 then return end
		local msg = table.concat(args, " ", 3)
		if target == LocalPlayer then
			sendToChat(msg)
		end
	elseif cmd == "!kick" then
		if target == LocalPlayer then
			LocalPlayer:Kick("Kicked by " .. sender.Name)
		end
	elseif cmd == "!kill" then
		if target == LocalPlayer then
			local hum = target.Character and target.Character:FindFirstChildOfClass("Humanoid")
			if hum then hum.Health = 0 end
		end
	elseif cmd == "!bring" then
		if target == LocalPlayer then
			local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			local senderHRP = sender.Character and sender.Character:FindFirstChild("HumanoidRootPart")
			if hrp and senderHRP then
				hrp.CFrame = senderHRP.CFrame + Vector3.new(0, 0, -3)
				toggleBlock(LocalPlayer, true)
				task.delay(1, function()
					toggleBlock(LocalPlayer, false)
				end)
			end
		end
	elseif cmd == "!spin" then
		if target == LocalPlayer then
			spin = true
			spinTarget = sender
		end
	elseif cmd == "!unspin" then
		if target == LocalPlayer then
			spin = false
			spinTarget = nil
		end
	end
end

RunService.Heartbeat:Connect(function()
	if not spin or not spinTarget then return end
	local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local targetHRP = spinTarget.Character and spinTarget.Character:FindFirstChild("HumanoidRootPart")
	if not targetHRP then return end
	local a = tick() * 2
	hrp.CFrame = targetHRP.CFrame * CFrame.new(math.cos(a) * 8, 2, math.sin(a) * 8)
end)

------------------------------------------------------------------

-- Cleaned Bloody v2

local UserInputService = game:GetService("UserInputService")
local Debris = game:GetService("Debris")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")
local camera = Workspace.CurrentCamera
camera.CameraType = Enum.CameraType.Custom
LocalPlayer.CameraMode = Enum.CameraMode.Classic

-- Remotes
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine", 3)
local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
local SpawnToyRF = MenuToys:WaitForChild("SpawnToyRemoteFunction")
local DestroyToyRF = MenuToys:FindFirstChild("DestroyToy")
local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")

-- UI Window
local Window = Rayfield:CreateWindow({
   Name = "🩸 Bloody V2 🩸 [PREMIUM] BETA",
   Icon = 0,
   LoadingTitle = "🩸 Bloody V2 🩸 [PREMIUM] BETA",
   LoadingSubtitle = "by Bloody",
   ShowText = "test",
   Theme = {
      TextColor = Color3.fromRGB(240, 240, 240),
Background = Color3.fromRGB(10, 10, 10),
Topbar = Color3.fromRGB(10, 10, 10),
Shadow = Color3.fromRGB(255, 50, 50),
NotificationBackground = Color3.fromRGB(10, 10, 10),
NotificationActionsBackground = Color3.fromRGB(230, 230, 230),
TabBackground = Color3.fromRGB(10, 10, 10),
TabStroke = Color3.fromRGB(255, 50, 50),
TabBackgroundSelected = Color3.fromRGB(255, 50, 50),
TabTextColor = Color3.fromRGB(240, 240, 240),
SelectedTabTextColor = Color3.fromRGB(50, 50, 50),
ElementBackground = Color3.fromRGB(10, 10, 10),
ElementBackgroundHover = Color3.fromRGB(10, 10, 10),
SecondaryElementBackground = Color3.fromRGB(10, 10, 10),
ElementStroke = Color3.fromRGB(255, 50, 50),
SecondaryElementStroke = Color3.fromRGB(10, 10, 10),
SliderBackground = Color3.fromRGB(10, 10, 10),
SliderProgress = Color3.fromRGB(10, 10, 10),
SliderStroke = Color3.fromRGB(255, 50, 50),
ToggleBackground = Color3.fromRGB(10, 10, 10),
ToggleEnabled = Color3.fromRGB(0, 255, 0),
ToggleDisabled = Color3.fromRGB(255, 0, 0),
ToggleEnabledStroke = Color3.fromRGB(10, 10, 10),
ToggleDisabledStroke = Color3.fromRGB(10, 10, 10),
ToggleEnabledOuterStroke = Color3.fromRGB(255, 50, 50),
ToggleDisabledOuterStroke = Color3.fromRGB(255, 50, 50),
DropdownSelected = Color3.fromRGB(10, 10, 10),
DropdownUnselected = Color3.fromRGB(10, 10, 10),
InputBackground = Color3.fromRGB(10, 10, 10),
InputStroke = Color3.fromRGB(255, 50, 50),
PlaceholderColor = Color3.fromRGB(10, 10, 10)

   },
   ToggleUIKeybind = "M",
   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false,
   ConfigurationSaving = { Enabled = true, FolderName = nil, FileName = "Bloody V2" },
   Discord = { Enabled = true, Invite = "https://discord.gg/bpMXbpCkcB", RememberJoins = true },
   KeySystem = false,
   KeySettings = {
      Title = "Bloody V2 [PREMIUM] BETA",
      Subtitle = "Key System",
      Note = 'Premium Edition',
      FileName = "Key",
      SaveKey = trfalseue,
      GrabKeyFromSite = true,
      Key = {"a"}
   }
})

-- Tabs LineTab
local MainTab = Window:CreateTab("Player", 7743871002)
local invTab = Window:CreateTab("Invincibility", 7734056608)
local GrabLinesTab = Window:CreateTab("Grab & Lines", 7485051715)
local LoopTab = Window:CreateTab("Loop", 7734058599)
local AuraTab = Window:CreateTab("Player Aura", 7733666258)
local ESPTab = Window:CreateTab("ESP", 7733774602)
local KeybindTab = Window:CreateTab("Keybind", 11710306232)
local InfoTab = Window:CreateTab("Information", 4483362458)

-- Helpers
local function notify(title, content)
    pcall(function()
        if Rayfield and Rayfield.Notify then
            Rayfield:Notify({Title = title, Content = content, Duration = 3})
        end
    end)
end

local function cleanupConnections(list)
    for i, c in ipairs(list) do
        if c and typeof(c) == "RBXScriptConnection" then
            c:Disconnect()
        end
        list[i] = nil
    end
end

local function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then d.CanCollide = false end
    end
end

local function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then v.CanCollide = false end
    end
end

local function ChangeCollision(state)
    pcall(function()
        local objetModel = workspace:FindFirstChild("Map")
        if not objetModel then return end
        local alwaysHereTweenedObjects = objetModel:FindFirstChild("AlwaysHereTweenedObjects")
        if not alwaysHereTweenedObjects then return end
        local ocean = alwaysHereTweenedObjects:FindFirstChild("Ocean")
        if not ocean then return end
        local object = ocean:FindFirstChild("Object")
        if not object then return end
        local objectModel = object:FindFirstChild("ObjectModel")
        if not objectModel then return end
        for _, part in pairs(objectModel:GetChildren()) do
            if part:IsA("BasePart") then part.CanCollide = state end
        end
    end)
end

--[[======================== MAIN ========================]]--

local speedEnabled, speedConnection = false, nil
local customSpeedValue = 1
MainTab:CreateSection("WalkSpeed")
MainTab:CreateToggle({
    Name = 'Enable Custom Speed		<font face="GothamBlack" color="rgb(255, 255, 255)">(CHARACTER)</font>',
    CurrentValue = false,
    Flag = "Player_CustomSpeed",
    Callback = function(v)
        speedEnabled = v
        if speedConnection then speedConnection:Disconnect() speedConnection = nil end
        if v then
            speedConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if hrp and hum and hum.MoveDirection.Magnitude > 0 then
                    hrp.CFrame = hrp.CFrame + (hum.MoveDirection.Unit * customSpeedValue)
                end
            end)
        end
    end
})
MainTab:CreateInput({
    Name = "Custom Speed",
    CurrentValue = tostring(customSpeedValue),
    PlaceholderText = "Enter speed",
    RemoveTextAfterFocusLost = false,
    Flag = "Player_SpeedInput",
    Callback = function(Text)
        local n = tonumber(Text)
        if n and n > 0 then customSpeedValue = n end
    end
})

-- MAIN: JumpPower
local jumpEnabled, jumpConnection = false, nil
local customJumpPower = 50
MainTab:CreateSection("JumpPower")
MainTab:CreateToggle({
    Name = 'Enable Custom JumpPower	  <font face="GothamBlack" color="rgb(255, 255, 255)">(CHARACTER)</font>',
    CurrentValue = false,
    Flag = "JumpPowerToggleFlag",
    Callback = function(v)
        jumpEnabled = v
        if jumpConnection then jumpConnection:Disconnect() jumpConnection = nil end
        if v then
            jumpConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if hum then hum.JumpPower = customJumpPower hum.UseJumpPower = true end
            end)
        else
            local char = LocalPlayer.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then hum.JumpPower = 50 hum.UseJumpPower = false end
        end
    end
})
MainTab:CreateInput({
    Name = 'Custom JumpPower',
    CurrentValue = tostring(customJumpPower),
    PlaceholderText = "Enter jump power",
    RemoveTextAfterFocusLost = false,
    Flag = "Player_JumpPowerInput",
    Callback = function(Text)
        local v = tonumber(Text)
        if v and v > 0 then customJumpPower = v end
    end
})

-- MAIN: Infinite Jump
local infiniteJumpEnabled = false
MainTab:CreateToggle({
    Name = 'Infinite Jump	 <font face="GothamBlack" color="rgb(255, 255, 255)">(CHARACTER)</font>',
    CurrentValue = false,
    Flag = "Player_InfiniteJump",
    Callback = function(v) infiniteJumpEnabled = v end
})
UserInputService.JumpRequest:Connect(function()
    if infiniteJumpEnabled then
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

-- MAIN: Others
MainTab:CreateSection("Others")
MainTab:CreateToggle({
    Name = "Infinite Zoom",
    CurrentValue = false,
    Flag = "Useful_InfiniteZoom",
    Callback = function(v)
        if v then
            LocalPlayer.CameraMaxZoomDistance = 5000000
            LocalPlayer.CameraMinZoomDistance = 0.5
        else
            LocalPlayer.CameraMaxZoomDistance = 0.5
            LocalPlayer.CameraMinZoomDistance = 0.5
        end
        camera.FieldOfView = 70
    end,
})
MainTab:CreateToggle({
    Name = 'Walk on Water 	 <font face="GothamBlack" color="rgb(255, 255, 255)">(CHARACTER)</font>',
    CurrentValue = false,
    Flag = "Useful_WalkOnWater",
    Callback = function(v) ChangeCollision(v) end,
})

-- MAIN: Noclip
local noclipEnabled = false
MainTab:CreateToggle({
    Name = 'Enable Noclip	  <font face="GothamBlack" color="rgb(255, 255, 255)">(CHARACTER)</font>',
    CurrentValue = false,
    Flag = "Player_Noclip",
    Callback = function(v) noclipEnabled = v end
})
RunService.Stepped:Connect(function()
    if not noclipEnabled then return end
    local char = LocalPlayer.Character
    if not char then return end
    for _, part in pairs(char:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = false end
    end
end)

--[[======================== INVINCIBILITY ========================]]--

invTab:CreateSection("Anti Grab")
invTab:CreateButton({
    Name = 'anti gucci     <font face="GothamBlack" color="rgb(255,251,0)">BLOBMAN</font>   <font face="GothamBlack" color="rgb(255,0,0)">OP</font>   <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
    Callback = function()
        character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        humanoid = character:WaitForChild("Humanoid")
        humanoidRootPart = character:WaitForChild("HumanoidRootPart")
        local safePosition = humanoidRootPart.Position
        SpawnToyRF:InvokeServer("CreatureBlobman", CFrame.new(0, 50000, 0), Vector3.new(0, 60, 0))
        local folder = Workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys", 5)
        if folder then
            local blobman = folder:FindFirstChild("CreatureBlobman")
            if blobman then
                local head = blobman:FindFirstChild("Head")
                if head then head.Anchored = true head.CFrame = CFrame.new(0,50000,0) end
                local seat = blobman:FindFirstChild("VehicleSeat")
                if seat and seat:IsA("VehicleSeat") then
                    task.wait(0.5)
                    humanoidRootPart.CFrame = seat.CFrame + Vector3.new(0,2,0)
                    task.wait(0.2)
                    seat:Sit(humanoid)
                end
            end
        end
        local antiGrabConnection
        if antiGrabConnection then antiGrabConnection:Disconnect() end
        antiGrabConnection = RunService.Heartbeat:Connect(function()
            if not humanoid or not humanoidRootPart then return end
            pcall(function() RagdollRemote:FireServer(humanoidRootPart,0) end)
        end)
        humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
            if humanoid.Jump and humanoid.Sit then
                safePosition = humanoidRootPart.Position
            end
        end)
        task.spawn(function()
            while humanoid.Sit do task.wait(1) end
            task.wait(0.5)
            humanoidRootPart.CFrame = CFrame.new(safePosition)
        end)
    end,
})

-- Anti Grab
local antiGrab1T, antiGrab1AnchorT = false, true
local antiGrabConnection = nil
local function startAntiGrab()
    antiGrab1T = true
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart", 5)
    if not hrp then return end
    if antiGrabConnection then antiGrabConnection:Disconnect() end
    antiGrabConnection = RunService.Heartbeat:Connect(function()
        if not antiGrab1T then return end
        local isHeld = LocalPlayer:FindFirstChild("IsHeld")
        if isHeld and isHeld.Value then
            if antiGrab1AnchorT then
                hrp.Anchored = true
                while isHeld.Value and antiGrab1T do
                    if CharacterEvents:FindFirstChild("Struggle") then
                        CharacterEvents.Struggle:FireServer(LocalPlayer)
                    end
                    task.wait(0.01)
                end
                hrp.Anchored = false
            else
                while isHeld.Value and antiGrab1T do
                    if CharacterEvents:FindFirstChild("Struggle") then
                        CharacterEvents.Struggle:FireServer(LocalPlayer)
                    end
                    task.wait(0.01)
                end
            end
        end
    end)
end
local function stopAntiGrab()
    antiGrab1T = false
    if antiGrabConnection then antiGrabConnection:Disconnect() antiGrabConnection = nil end
end
invTab:CreateToggle({
    Name = "Anti Grab",
    CurrentValue = false,
    Flag = "AntiGrab",
    Callback = function(v) if v then startAntiGrab() else stopAntiGrab() end end,
})

invTab:CreateSection("Others Antis")
-- Anti Lag
invTab:CreateToggle({
    Name = "Anti Lag",
    CurrentValue = false,
    Flag = "AntiLag",
    Callback = function(v)
        local playerScripts = LocalPlayer:FindFirstChild("PlayerScripts")
        if playerScripts then
            local s = playerScripts:FindFirstChild("CharacterAndBeamMove")
            if s then s.Enabled = not v end
        end
    end,
})

-- Anti Explosion
local antiExplodeT, antiExplodeConn = false, nil
local antiExplodeCharConn = nil
local function startAntiExplosion()
    antiExplodeT = true
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart", 5)
    if not hrp then return end
    if antiExplodeConn then antiExplodeConn:Disconnect() end
    antiExplodeConn = workspace.ChildAdded:Connect(function(model)
        if not antiExplodeT then return end
        if model.Name == "Part" then
            if (model.Position - hrp.Position).Magnitude <= 20 then
                hrp.Anchored = true
                task.wait(0.02)
                hrp.Anchored = false
            end
        end
    end)
    if antiExplodeCharConn then antiExplodeCharConn:Disconnect() end
    antiExplodeCharConn = LocalPlayer.CharacterAdded:Connect(function(newChar)
        if not antiExplodeT then return end
        local newHrp = newChar:WaitForChild("HumanoidRootPart", 5)
        if not newHrp then return end
        if antiExplodeConn then antiExplodeConn:Disconnect() antiExplodeConn = nil end
        antiExplodeConn = workspace.ChildAdded:Connect(function(model)
            if not antiExplodeT then return end
            if model.Name == "Part" then
                if (model.Position - newHrp.Position).Magnitude <= 20 then
                    newHrp.Anchored = true
                    task.wait(0.02)
                    newHrp.Anchored = false
                end
            end
        end)
    end)
end
local function stopAntiExplosion()
    antiExplodeT = false
    if antiExplodeConn then antiExplodeConn:Disconnect() antiExplodeConn = nil end
    if antiExplodeCharConn then antiExplodeCharConn:Disconnect() antiExplodeCharConn = nil end
end
invTab:CreateToggle({
    Name = "Anti Explosion",
    CurrentValue = false,
    Flag = "AntiExplode",
    Callback = function(v) if v then startAntiExplosion() else stopAntiExplosion() end end,
})

-- Anti Burn
local poisonPart = workspace.Map.FactoryIsland.PoisonContainer.ExtinguishPart
local poisonStartPos = poisonPart.Position
local toggleConnection, setupConnection
invTab:CreateToggle({
    Name = "Anti Burn",
    CurrentValue = false,
    Flag = "AntiBurnToggle",
    Callback = function(v)
        if v then
            poisonPart.Transparency = 1
            local function setupCharacter(char)
                local humanoid = char:WaitForChild("Humanoid")
                local rootPart = char:WaitForChild("HumanoidRootPart")
                local fireDebounce = humanoid:WaitForChild("FireDebounce", 5)
                if toggleConnection then toggleConnection:Disconnect() end
                toggleConnection = RunService.Heartbeat:Connect(function()
                    if not char:IsDescendantOf(workspace) then
                        toggleConnection:Disconnect() toggleConnection = nil return
                    end
                    if fireDebounce and fireDebounce.Value then
                        local target = rootPart.Position
                        local cycle = (tick() * 3) % 3
                        local targetPos = (cycle < 1 and target) or (cycle < 2 and poisonStartPos) or target
                        local offset = Vector3.new(
                            math.sin(tick() * 5) * 3,
                            math.cos(tick() * 3) * 2,
                            math.sin(tick() * 4) * 3
                        )
                        poisonPart.Position = targetPos + offset
                        poisonPart.CFrame = poisonPart.CFrame * CFrame.Angles(
                            math.rad(math.random(-10, 10)),
                            math.rad(math.random(-10, 10)),
                            math.rad(math.random(-10, 10))
                        )
                    else
                        poisonPart.Position = poisonPart.Position:Lerp(poisonStartPos, 0.1)
                    end
                end)
            end
            if setupConnection then setupConnection:Disconnect() end
            setupConnection = LocalPlayer.CharacterAdded:Connect(setupCharacter)
            if LocalPlayer.Character then setupCharacter(LocalPlayer.Character) end
        else
            if toggleConnection then toggleConnection:Disconnect() toggleConnection = nil end
            if setupConnection then setupConnection:Disconnect() setupConnection = nil end
            poisonPart.Position = poisonStartPos
            poisonPart.Transparency = 0
        end
    end,
})

-- Anti Paint
local antiPaintConnection
invTab:CreateToggle({
    Name = "Anti Paint",
    CurrentValue = false,
    Flag = "AntiPaintToggle",
    Callback = function(v)
        if v then
            local function deletePaintParts()
                for _, descendant in pairs(workspace:GetDescendants()) do
                    if descendant.Name == "PaintPlayerPart" then
                        descendant:Destroy()
                    end
                end
            end
            local function handleNewPaintPart(obj)
                if obj.Name == "PaintPlayerPart" then
                    task.wait()
                    obj:Destroy()
                end
            end
            deletePaintParts()
            if antiPaintConnection then antiPaintConnection:Disconnect() end
            antiPaintConnection = workspace.DescendantAdded:Connect(handleNewPaintPart)
        else
            if antiPaintConnection then antiPaintConnection:Disconnect() antiPaintConnection = nil end
        end
    end,
})

-- Anti Void
local antiVoidEnabled = false
invTab:CreateToggle({
    Name = "Anti Void",
    CurrentValue = false,
    Flag = "AntiVoid",
    Callback = function(v)
        antiVoidEnabled = v
        if v then
            workspace.FallenPartsDestroyHeight = -100000
            task.spawn(function()
                while antiVoidEnabled do
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if hrp and hrp.Position.Y < -500 then
                        hrp.CFrame = CFrame.new(2, -7, -4)
                    end
                    task.wait(0.2)
                end
            end)
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end,
})

-- Anti Sticky
local StickyScriptName = "StickyPartsTouchDetection"
invTab:CreateToggle({
    Name = "Anti Sticky",
    CurrentValue = false,
    Flag = "AntiStickyToggle",
    Callback = function(v)
        local playerScripts = LocalPlayer:WaitForChild("PlayerScripts")
        if v then
            local s = playerScripts:FindFirstChild(StickyScriptName)
            if s then s.Disabled = true end
        else
            local oldScript = playerScripts:FindFirstChild(StickyScriptName)
            if oldScript then oldScript:Destroy() end
            local templateScript = ReplicatedStorage:FindFirstChild(StickyScriptName)
            if templateScript then
                local newScript = templateScript:Clone()
                newScript.Parent = playerScripts
            end
        end
    end,
})

invTab:CreateSection("Anti Blobman")
local AntiBringConnection
invTab:CreateToggle({
    Name = "Anti Blobman1",
    CurrentValue = false,
    Flag = "AntiBring",
    Callback = function(v)
        local a = LocalPlayer.Name .. "SpawnedInToys"
        local function toggleWelds(state)
            for _, x in pairs(Workspace:GetDescendants()) do
                if x:IsA("BasePart") and (x.Name == "LeftWeld" or x.Name == "RightWeld" or x.Name == "LeftAlignOrientation" or x.Name == "RightAlignOrientation") then
                    if x.Parent and x.Parent.Parent and x.Parent.Parent.Parent ~= Workspace:FindFirstChild(a) then
                        x.Enabled = state
                    end
                end
            end
        end
        if v then
            toggleWelds(false)

            if AntiBringConnection then AntiBringConnection:Disconnect() end
            AntiBringConnection = Workspace.DescendantAdded:Connect(function(x)
                if x:IsA("BasePart") and (x.Name == "LeftWeld" or x.Name == "RightWeld" or x.Name == "LeftAlignOrientation" or x.Name == "RightAlignOrientation") then
                    if x.Parent and x.Parent.Parent and x.Parent.Parent.Parent ~= Workspace:FindFirstChild(a) then
                        x.Enabled = false
                    end
                end
            end)
        else
            toggleWelds(true)
            for _, x in pairs(Workspace:GetDescendants()) do
                if x:IsA("BasePart") and (x.Name == "LeftWeld" or x.Name == "RightWeld" or x.Name == "LeftAlignOrientation" or x.Name == "RightAlignOrientation") then
                    if x.Parent and x.Parent.Parent and x.Parent.Parent.Parent ~= Workspace:FindFirstChild(a) then
                        x.Enabled = true
                    end
                end
            end

            if AntiBringConnection then AntiBringConnection:Disconnect() AntiBringConnection = nil end
        end
    end,
})

local kickConnection, massCheckConnection
invTab:CreateToggle({
    Name = "Anti Blobman2",
    CurrentValue = false,
    Flag = "AntiKickToggle",
    Callback = function(v)
        if v then
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name == "AttachPlayer" then obj:Destroy() end
            end

            if kickConnection then kickConnection:Disconnect() end
            kickConnection = workspace.DescendantAdded:Connect(function(obj)
                if obj.Name == "AttachPlayer" then task.wait() obj:Destroy() end
            end)

            if massCheckConnection then massCheckConnection:Disconnect() end
            massCheckConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                if not char then return end
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and part.Massless then
                        part.Massless = false
                    end
                end
            end)
        else
            if kickConnection then kickConnection:Disconnect() kickConnection = nil end
            if massCheckConnection then massCheckConnection:Disconnect() massCheckConnection = nil end
        end
    end,
})

--[[======================== GRAB & LINES ========================]]--
GrabLinesTab:CreateSection("Grab")
-- Kill Grab
local killGrabRunning, killGrabCoroutine = false, nil
GrabLinesTab:CreateToggle({
   Name = 'Kill Grab	 <font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
   CurrentValue = false,
   Flag = "KillGrabToggle",
   Callback = function(v)
       if v then
           if killGrabRunning then return end
           killGrabRunning = true
           killGrabCoroutine = coroutine.create(function()
               while killGrabRunning do
                   pcall(function()
                       local g = workspace:FindFirstChild("GrabParts")
                       local gp = g and g:FindFirstChild("GrabPart")
                       local weld = gp and gp:FindFirstChildOfClass("WeldConstraint")
                       local p1 = weld and weld.Part1
                       local model = p1 and p1:FindFirstAncestorOfClass("Model")
                       local hum = model and model:FindFirstChild("Humanoid")
                       if hum then task.wait(0.4) hum.Health = 0 end
                   end)
                   task.wait()
               end
           end)
           coroutine.resume(killGrabCoroutine)
       else
           killGrabRunning = false
       end
   end,
})

-- Noclip Grab
getgenv().NoclipGrabConnection = nil
GrabLinesTab:CreateToggle({
    Name = 'Noclip Grab		<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER & OBJECT)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "ToggleNoclipGrab",
    Callback = function(v)
        if getgenv().NoclipGrabConnection then
            getgenv().NoclipGrabConnection:Disconnect()
            getgenv().NoclipGrabConnection = nil
        end
        local function GetGrabbedCharacter()
            local g = workspace:FindFirstChild("GrabParts")
            if not g then return nil end
            local gp = g:FindFirstChild("GrabPart")
            if not gp then return nil end
            local weld = gp:FindFirstChild("WeldConstraint") or gp:FindFirstChild("Weld")
            if not weld then return nil end
            return weld.Part1 and weld.Part1.Parent or nil
        end
        if not v then
            local char = GetGrabbedCharacter()
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then part.CanCollide = true end
                end
            end
            return
        end
        getgenv().NoclipGrabConnection = RunService.Heartbeat:Connect(function()
            local char = GetGrabbedCharacter()
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then part.CanCollide = false end
                end
            end
        end)
    end,
})

-- Massless Grab
local Sense, Massless = 30, nil
GrabLinesTab:CreateToggle({
   Name = 'Massless Grab	<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER & OBJECT)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
   CurrentValue = false,
   Flag = "Toggle1",
   Callback = function(v)
       if v then
           Massless = workspace.ChildAdded:Connect(function(r)
               if r.Name == "GrabParts" then
                   while workspace:FindFirstChild("GrabParts") do
                       task.wait()
                       local dp = r:FindFirstChild("DragPart")
                       if dp and dp:FindFirstChild("AlignPosition") and dp:FindFirstChild("AlignOrientation") then
                           dp.AlignPosition.Responsiveness = Sense
                           dp.AlignPosition.MaxForce = math.huge
                           dp.AlignPosition.MaxVelocity = math.huge
                           dp.AlignOrientation.Responsiveness = Sense
                           dp.AlignOrientation.MaxTorque = math.huge
                       end
                   end
               end
           end)
       else
           if Massless then Massless:Disconnect() Massless = nil end
       end
   end,
})
GrabLinesTab:CreateInput({
   Name = "Massless Sense",
   CurrentValue = tostring(Sense),
   PlaceholderText = "Enter sense value",
   RemoveTextAfterFocusLost = false,
   Flag = "MasslessSenseInput",
   Callback = function(Text)
       local v = tonumber(Text)
       if v and v > 0 then Sense = v end
   end,
})

-- Crazy Grab
local crazyGrabRunning = false
local crazyGrabDelay = 0.1
GrabLinesTab:CreateToggle({
    Name = 'Crazy Grab		<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER)</font>	  <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Callback = function(v)
        if v then
            if crazyGrabRunning then return end
            crazyGrabRunning = true
            task.spawn(function()
                while crazyGrabRunning do
                    pcall(function()
                        local g = workspace:FindFirstChild("GrabParts")
                        local gp = g and g:FindFirstChild("GrabPart")
                        local weld = gp and gp:FindFirstChild("WeldConstraint")
                        local p1 = weld and weld.Part1
                        local targetChar = p1 and p1.Parent
                        local hrp = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            local positions = {
                                Vector3.new(-17, 421, 50),
                                Vector3.new(145, 397, -126),
                                Vector3.new(157, 254, 89),
                                Vector3.new(-26, 207, -26),
                                Vector3.new(-23, 456, -19),
                                Vector3.new(15, 211, 45),
                            }
                            for _, pos in ipairs(positions) do
                                if not crazyGrabRunning then break end
                                hrp.CFrame = CFrame.new(pos)
                                task.wait(crazyGrabDelay)
                            end
                        end
                    end)
                    task.wait()
                end
            end)
        else
            crazyGrabRunning = false
        end
    end
})


-- Strength
GrabLinesTab:CreateSection("Strength")
local flingEnabled, flingChildConnection = false, nil
local strengthV = 1000
local RMBConnection

local function stopFling()
    flingEnabled = false
    if flingChildConnection then flingChildConnection:Disconnect() flingChildConnection = nil end
    if RMBConnection then RMBConnection:Disconnect() RMBConnection = nil end
end

local function flingF()
    local char = LocalPlayer.Character
    if not char then return end
    char:WaitForChild("HumanoidRootPart")

    flingChildConnection = workspace.ChildAdded:Connect(function(model)
        if not flingEnabled then return end
        if model.Name == "GrabParts" then
            local part_to_impulse = model:FindFirstChild("GrabPart")
            part_to_impulse = part_to_impulse and part_to_impulse:FindFirstChild("WeldConstraint")
            part_to_impulse = part_to_impulse and part_to_impulse.Part1
            if part_to_impulse then
                model:GetPropertyChangedSignal("Parent"):Connect(function()
                    if not model.Parent and flingEnabled then
                        if RMBConnection then RMBConnection:Disconnect() end
                        RMBConnection = UserInputService.InputBegan:Connect(function(inp)
                            if inp.UserInputType == Enum.UserInputType.MouseButton2 then
                                local bv = Instance.new("BodyVelocity")
                                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                bv.Velocity = camera.CFrame.LookVector * strengthV
                                bv.Parent = part_to_impulse
                                Debris:AddItem(bv, 1)
                            end
                        end)
                    end
                end)
            end
        end
    end)
end

GrabLinesTab:CreateToggle({
    Name = 'Strength	<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER & OBJECT)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "FlingToggle",
    Callback = function(v)
        if not v then
            stopFling()
            return
        end
        flingEnabled = true
        flingF()
    end,
})

GrabLinesTab:CreateInput({
    Name = "Strength Force",
    CurrentValue = tostring(strengthV),
    PlaceholderText = "Enter number",
    RemoveTextAfterFocusLost = false,
    Flag = "FlingForceInput",
    Callback = function(Text)
        local n = tonumber(Text)
        if n then strengthV = n end
    end,
})

--[[======================== LINE ========================]]--
-- Extend Line
GrabLinesTab:CreateSection("Extend Line")
local lineDistanceV = 11
local increaseLineExtendV = 7
local infLineExtendT = false
local inputConnection
local childConnection

local function infLineExtendF()
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")

    inputConnection = UserInputService.InputChanged:Connect(function(input)
        if not infLineExtendT then return end
        if input.UserInputType == Enum.UserInputType.MouseWheel then
            if lineDistanceV < 11 then lineDistanceV = 11 end
            if input.Position.Z > 0 then
                lineDistanceV += increaseLineExtendV
            elseif input.Position.Z < 0 then
                lineDistanceV -= increaseLineExtendV
            end
        end
    end)

    childConnection = workspace.ChildAdded:Connect(function(child)
        if not infLineExtendT then return end
        if child.Name == "GrabParts" and child:IsA("Model") then
            local grabPartsModel = child
            local grabPart = grabPartsModel:WaitForChild("GrabPart")
            local dragPart = grabPartsModel:WaitForChild("DragPart")

            local clonedDragPart = dragPart:Clone()
            clonedDragPart.Name = "DragPart1"
            local attach = clonedDragPart:FindFirstChild("DragAttach")
            if clonedDragPart:FindFirstChild("AlignPosition") and attach then
                clonedDragPart.AlignPosition.Attachment1 = attach
            end
            clonedDragPart.Parent = grabPartsModel

            lineDistanceV = (clonedDragPart.Position - camera.CFrame.Position).Magnitude

            if clonedDragPart:FindFirstChild("AlignOrientation") then
                clonedDragPart.AlignOrientation.Enabled = false
            end
            if dragPart:FindFirstChild("AlignPosition") then
                dragPart.AlignPosition.Enabled = false
            end

            task.spawn(function()
                while grabPartsModel.Parent and infLineExtendT do
                    clonedDragPart.Position = camera.CFrame.Position + camera.CFrame.LookVector * lineDistanceV
                    RunService.RenderStepped:Wait()
                end
                lineDistanceV = 11
            end)
        end
    end)
end

local function stopInfLine()
    if inputConnection then
        inputConnection:Disconnect()
        inputConnection = nil
    end
    if childConnection then
        childConnection:Disconnect()
        childConnection = nil
    end
    lineDistanceV = 11
end

local extendLineToggle = GrabLinesTab:CreateToggle({
    Name = 'Scroll Extend Line	<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER & OBJECT)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "extendLineToggleFlag",
    Callback = function(Value)
        infLineExtendT = Value
        if Value then
            infLineExtendF()
        else
            stopInfLine()
        end
    end
})

local extendLineInput = GrabLinesTab:CreateInput({
    Name = "Scroll Force",
    CurrentValue = tostring(increaseLineExtendV),
    PlaceholderText = "Default: 7",
    RemoveTextAfterFocusLost = false,
    Flag = "extendLineInputFlag",
    Callback = function(Value)
        local num = tonumber(Value)
        if num then
            increaseLineExtendV = num
        end
    end
})
GrabLinesTab:CreateSection("Others Lines")

	GrabLinesTab:CreateToggle({
			Name = 'Invisble Line	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
			CurrentValue = false,
			Flag = "InvisbleLine", 
			Callback = function(Value)
				invisline = Value
				while invisline do
					CreateGrabLine:FireServer()
					task.wait()
				end
			end,
		})

GrabLinesTab:CreateToggle({
	Name = 'Crazy Line	 <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
	CurrentValue = false,
	Flag = "CrazyLine", 
	Callback = function(Value)
		crazyline = Value
		while crazyline do
			for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
				local v1 = plr.Character
				if v1 then
					local v2 = v1:FindFirstChild("Head")
					if v2 and CreateGrabLine then
						CreateGrabLine:FireServer(v2, v2.CFrame)
						task.wait()
					end
				end
			end
		end
	end,
})

GrabLinesTab:CreateSection("Lag")
local lagT, linesV = false, 10
local function lagF()
    while lagT do
        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
        local humanoid = character:WaitForChild("Humanoid")
        if CreateGrabLine then
            for a = 0, linesV do
                for _, player in ipairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        local torso = player.Character:FindFirstChild("Torso") or player.Character:FindFirstChild("HumanoidRootPart")
                        if torso then
                            CreateGrabLine:FireServer(torso, torso.CFrame)
                        end
                    end
                end
            end
        end
        task.wait(1)
    end
end
GrabLinesTab:CreateToggle({ Name = 'Lag	  <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>   <font face=\"GothamBlack\" color=\"rgb(39,245,218)\">PREMIUM</font>', CurrentValue = false, Flag = "LagToggle", Callback = function(v) lagT = v if v then task.spawn(lagF) end end })
GrabLinesTab:CreateInput({ Name = "Lag Intensity", CurrentValue = tostring(linesV), PlaceholderText = "Enter number of lines", RemoveTextAfterFocusLost = false, Flag = "LinesInput", Callback = function(Text) local n = tonumber(Text) if n and n > 0 then linesV = n end end })

--[[======================== LOOP ========================]]--

local RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
local SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

local SelectedPlayer = nil
local KillHB, KickHB = nil, nil
local LoopKickOn, LoopFlingOn, LoopBlobKickOn = false, false, false

local HEIGHT_LIMIT = 100000
local TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
local lastReturnCF = nil

local function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then d.CanCollide = false end
    end
end

local function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then v.CanCollide = false end
    end
end

local function isTooHigh(plr)
    local c = plr.Character
    local hrp = c and c:FindFirstChild("HumanoidRootPart")
    return not hrp or hrp.Position.Y > HEIGHT_LIMIT
end

local function findBlobman()
    local toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    return toys and toys:FindFirstChild("CreatureBlobman") or nil
end

local function spawnBlobman()
    SpawnToyRF:InvokeServer("CreatureBlobman", LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end

local function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 30 do
        task.wait(0.1)
        b = findBlobman()
        if b then return b end
    end
    return nil
end

local CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    local cam = Workspace.CurrentCamera
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
local cameraAnchor = CameraAnchor.new()

local function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        char:SetAttribute("OriginalPosition", hrp:GetPivot())
    end
end
local function getOriginalPosAttr()
    local char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end
local function initCharAttrs()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetAttribute("OriginalPosition", char.HumanoidRootPart:GetPivot())
        char:SetAttribute("SavingOriginalPos", false)
    end
end

local function scheduleReturnHome()
    local originalPos = getOriginalPosAttr()
    if not originalPos then return end
    
    local conn
    conn = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp:PivotTo(originalPos)
            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end
            char:SetAttribute("SavingOriginalPos", false)
        end
        cameraAnchor:detach()
        conn:Disconnect()
    end)
end

local function modifyTarget(root, hum)
    if not (root and hum) or hum.Health <= 0 then return end
    local blob = ensureBlobman()
    if blob and blob:FindFirstChild("BlobmanSeatAndOwnerScript") then
        local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
        if drop then
            for _, part in ipairs(hum.Parent:GetDescendants()) do
                if part:IsA("Weld") or part:IsA("BallSocketConstraint") then
                    drop:FireServer(part, part)
                end
            end
        end
    end
    hum.Sit = false
    hum:ChangeState(Enum.HumanoidStateType.Running)
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
    hum:ChangeState(Enum.HumanoidStateType.GettingUp)

    local plr = Players:GetPlayerFromCharacter(hum.Parent)
    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
    local rag = hum:FindFirstChild("Ragdolled")
    if rag then rag.Value = false end

    local bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
    bv.P = 1e6
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(2, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end

-- LoopKill
local function performKill()
    if not SelectedPlayer then return end
    
    local target = Players:FindFirstChild(SelectedPlayer)
    local tChar = target and target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    local tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then return end
    if isTooHigh(target) then return end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0/0

    local originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    local desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
    task.wait(0.05)
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait(0.05)

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        task.wait(0.05)
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end

local function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    KillHB = RunService.Heartbeat:Connect(performKill)
end

local function StopLoopKill()
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
end

-- LoopKick
local function sendToSky(root, hum)
    DisableCollisions(hum.Parent)
    local BV = Instance.new("BodyVelocity")
    BV.Velocity = Vector3.new(0, 1e9, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 1e8
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true
    task.delay(3, function() if BV.Parent then BV:Destroy() end end)
end

local function executeKick()
    if not SelectedPlayer then return end
    local p = Players:FindFirstChild(SelectedPlayer)
    local c = p and p.Character
    local root = c and c:FindFirstChild("HumanoidRootPart")
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChild("Humanoid")
    if not (root and head and hum) or hum.Health <= 0 then return end
    if isTooHigh(p) then return end
    if p:FindFirstChild("InPlot") and p.InPlot.Value then return end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
    if not selfRoot then return end

    local saved = selfChar:GetPivot()
    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.1)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.1)
    local po = head:FindFirstChild("PartOwner")
    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end

local function StartLoopKick()
    if KickHB then KickHB:Disconnect() end
    LoopKickOn = true
    KickHB = RunService.Heartbeat:Connect(function()
        if LoopKickOn then executeKick() end
    end)
end
local function StopLoopKick()
    LoopKickOn = false
    if KickHB then KickHB:Disconnect() KickHB = nil end
end

-- LoopFling
local LoopFling = {
    on = false,
    targets = {},
    last = nil,
    tpSpeed = 0.3,
    magnetRange = 5,
    stay = 0.5,
    back = 0.5,
}
local function tpTo(cf)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _ = 1, 5 do
        hrp.CFrame = hrp.CFrame:Lerp(cf, LoopFling.tpSpeed)
        task.wait(0.02)
    end
end
local function stick(root)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    if (hrp.Position - root.Position).Magnitude < LoopFling.magnetRange then
        hrp.CFrame = root.CFrame + Vector3.new(0, 0, -1)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end
end
local function execFling(tChar)
    local hum = tChar:FindFirstChildOfClass("Humanoid")
    local root = tChar:FindFirstChild("HumanoidRootPart")
    if not (hum and root) then return end
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
    bv.Velocity = Vector3.new(0, 1000, 0)
    bv.Parent = root
    local up = true
    while LoopFling.on do
        bv.Velocity = up and Vector3.new(0, 1000, 0) or Vector3.new(0, -1000, 0)
        up = not up
        task.wait(0.5)
    end
    bv:Destroy()
end
local function startFlingFor(targetPlayer)
    if not targetPlayer then return end
    if not LoopFling.last then
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if hrp then LoopFling.last = hrp.CFrame end
    end
    while LoopFling.on do
        local tChar = targetPlayer.Character
        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
        if tRoot then
            tpTo(tRoot.CFrame + Vector3.new(0, 0, -2))
            task.wait(0.1)
            stick(tRoot)
            execFling(tChar)
            task.wait(LoopFling.stay)
        end
        if LoopFling.last then
            tpTo(LoopFling.last)
            task.wait(LoopFling.back)
        end
        task.wait()
    end
end
local function autoFlingLoop()
    while LoopFling.on do
        for _, name in ipairs(LoopFling.targets) do
            local tp = Players:FindFirstChild(name)
            if tp and tp.Character then
                task.spawn(function() startFlingFor(tp) end)
            end
        end
        task.wait(0.1)
    end
end
local function StartLoopBounce(targetName)
    if not targetName or targetName == "" then return end
    LoopFling.on = true
    LoopFling.targets = { targetName }
    task.spawn(autoFlingLoop)
end
local function StopLoopBounce()
    LoopFling.on = false
end


-- Dropdown
LoopTab:CreateSection("Individual")
local PlayerDropdown = LoopTab:CreateDropdown({
    Name = "Select Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(option)
        SelectedPlayer = option[1]:match("@([%w_]+)")
    end
})
local function optionText(plr) return string.format("👤 @%s (%s)", plr.Name, plr.DisplayName or "") end
local function refreshDropdown()
    local opts = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then table.insert(opts, optionText(p)) end
    end
    PlayerDropdown:Refresh(opts)
end
Players.PlayerAdded:Connect(function() task.wait(0.5) refreshDropdown() end)
Players.PlayerRemoving:Connect(function() task.wait(0.5) refreshDropdown() end)
refreshDropdown()

LoopTab:CreateButton({
    Name = 'Bring	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    Callback = function()
        if not SelectedPlayer then
            return
        end

        local Players = game:GetService("Players")
        local ReplicatedStorage = game:GetService("ReplicatedStorage")

        local LocalPlayer = Players.LocalPlayer
        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local root = character:WaitForChild("HumanoidRootPart")
        local oldCFrame = root.CFrame

        local targetPlayer = Players:FindFirstChild(SelectedPlayer)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Head") then
            local targetHead = targetPlayer.Character.Head

            for i = 1, 2 do
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("Head") then break end
                root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)
                local args = { [1] = targetHead, [2] = root.CFrame }
                ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"):FireServer(unpack(args))
                task.wait(0.15)
            end

            task.wait(0.1)
            root.CFrame = oldCFrame
            local front = oldCFrame.LookVector * 5
            targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

            local destroyArgs = { [1] = targetHead }
            ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine"):FireServer(unpack(destroyArgs))
        else
        end
    end
})

-- Toggles
LoopTab:CreateToggle({
    Name = 'LoopKill       <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font> ',
    CurrentValue = false,
    Flag = "LoopKillToggle",
    Callback = function(v) if v then StartLoopKill() else StopLoopKill() end end
})
LoopTab:CreateToggle({
    Name = 'LoopKick    <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font> ',
    CurrentValue = false,
    Flag = "LoopKickToggle",
    Callback = function(v)
        if v then
            StartLoopKick()
        else
            StopLoopKick()
        end
    end
})
Players.LocalPlayer.CharacterAdded:Connect(function(char)
    initCharAttrs()
    local hum = char:WaitForChild("Humanoid", 5)
    if hum then hum.Died:Connect(function() cameraAnchor:detach() end) end
end)
if Players.LocalPlayer.Character then initCharAttrs() end

LoopTab:CreateToggle({
    Name = 'LoopFling      <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>  <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
    CurrentValue = false,
    Flag = "LoopFlingToggle",
    Callback = function(v)
        if v then StartLoopBounce(SelectedPlayer) else StopLoopBounce() end
    end
})

LoopTab:CreateToggle({
    Name = 'Loop BlobKick   <font face="GothamBlack" color="rgb(255,251,0)">BLOBMAN</font>   <font face="GothamBlack" color="rgb(255,0,0)">OP</font>   <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
    CurrentValue = false,
    Flag = "LoopBlobmanKickToggle",
    Callback = function(enabled)
        local Players = game:GetService("Players")
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman") and hum.SeatPart.Parent or nil
        end

        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(Players.LocalPlayer, hrp, blob.RightDetector.RightWeld)
            end
        end

        local function execBlobKick(targetName)
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end

            local blob = findMountedBlob() or ensureBlobman()
            task.wait(0.3)

            local seat = blob and blob:FindFirstChild("VehicleSeat")
            if seat then seat:Sit(hum) task.wait(0.5) end

            local tp = Players:FindFirstChild(targetName)
            local tHRP = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then return end

            local startSelf, startBlob = hrp.CFrame, blob.PrimaryPart and blob.PrimaryPart.CFrame
            local oldCF = tHRP.CFrame
            hrp.CFrame = oldCF
            task.wait(0.3)

            for _ = 1, 10 do
                task.wait()
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)
            end
            task.wait(0.1)
            RemoteDestroyGrabLine:FireServer(tHRP)
            task.wait(0.4)

            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)

            task.delay(1, function()
                if hrp then hrp.CFrame = startSelf end
                if blob and blob.PrimaryPart and startBlob then
                    blob:SetPrimaryPartCFrame(startBlob)
                end
            end)
        end

        local function monitorRespawnBlobKick(targetName)
            local tp = Players:FindFirstChild(targetName)
            if not tp then return end
            tp.CharacterAdded:Connect(function()
                if LoopBlobKickOn then
                    task.wait(2)
                    execBlobKick(targetName)
                end
            end)
        end

        if enabled then
            if SelectedPlayer and SelectedPlayer ~= "" then
                execBlobKick(SelectedPlayer)
                monitorRespawnBlobKick(SelectedPlayer)
            end
        else
            LoopBlobKickOn = false
        end
    end
})

LoopTab:CreateToggle({
    Name = 'Loop BlobKill  <font face="GothamBlack" color="rgb(255,251,0)">BLOBMAN</font>   <font face="GothamBlack" color="rgb(255,0,0)">OP</font>   <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
    CurrentValue = false,
    Flag = "LoopBlobKillToggle",
    Callback = function(enabled)
        local Players = game:GetService("Players")
        LoopBlobKillOn = enabled

        local function execBlobKill(targetName)
            if not targetName or targetName == "" then return end
            local plr = Players:FindFirstChild(targetName)
            if not plr or not plr.Character then return end
            local localPlayer = Players.LocalPlayer
            local localChar = localPlayer.Character
            if not localChar then return end
            local hum = localChar:FindFirstChild("Humanoid")
            local hrp = localChar:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end
            local blob = (hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and hum.SeatPart.Parent) or ensureBlobman()
            if not blob or not blob.PrimaryPart then return end
            local targetHRP = plr.Character:FindFirstChild("HumanoidRootPart")
            if not targetHRP then return end

            if targetHRP.Position.Y > HEIGHT_LIMIT then return end

            local startLocalCFrame = hrp.CFrame
            local startBlobCFrame = blob.PrimaryPart.CFrame

            blob:SetPrimaryPartCFrame(targetHRP.CFrame)

            if blob:FindFirstChild("VehicleSeat") then
                blob.VehicleSeat:Sit(hum)
                task.wait(0.3)
            end

            local detector = blob:FindFirstChild("LeftDetector")
            local weld = detector and detector:FindFirstChild("LeftWeld")
            if detector and weld then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
            end

            task.wait(0.1)

            local targetHum = plr.Character:FindFirstChildOfClass("Humanoid")
            if targetHum then
                targetHum.RigType = Enum.HumanoidRigType.R15
            end

            task.wait(0.15)

            if weld then
                blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, targetHRP)
            end

            task.delay(0.6, function()
                hrp.CFrame = startLocalCFrame
                if blob and blob.PrimaryPart then
                    blob:SetPrimaryPartCFrame(startBlobCFrame)
                end
            end)
        end

        if enabled then
            task.spawn(function()
                while LoopBlobKillOn do
                    if SelectedPlayer and Players:FindFirstChild(SelectedPlayer) then
                        local plr = Players[SelectedPlayer]
                        local hum = plr.Character and plr.Character:FindFirstChildOfClass("Humanoid")
                        if hum and hum.Health > 0 then
                            execBlobKill(SelectedPlayer)
                        end
                    end
                    task.wait(1)
                end
            end)
        else
            LoopBlobKillOn = false
        end
    end
})


LoopTab:CreateSection("Server")
local function attachCameraToSpawn()
	local cam = Workspace.CurrentCamera
	cam.CameraType = Enum.CameraType.Scriptable
	cam.CFrame = CFrame.new(0, 50, 50) * CFrame.Angles(math.rad(-45), 0, 0)
end

local function detachCamera()
	local cam = Workspace.CurrentCamera
	local char = LocalPlayer.Character

	if char and char:FindFirstChild("Humanoid") then
		cam.CameraType = Enum.CameraType.Custom
		cam.CameraSubject = char.Humanoid
	else
		cam.CameraType = Enum.CameraType.Custom
	end
end

local function performKillAll()
	attachCameraToSpawn()

	for _, target in ipairs(Players:GetPlayers()) do
		if target ~= LocalPlayer then
			local tChar = target.Character
			local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
			local tHum = tChar and tChar:FindFirstChild("Humanoid")
			local tHead = tChar and tChar:FindFirstChild("Head")

			if not (tRoot and tHum and tHead) then continue end
			if isTooHigh(target) then continue end
			if target:FindFirstChild("InPlot") and target.InPlot.Value then continue end
			if tHum:GetState() == Enum.HumanoidStateType.Dead then continue end

			local char = LocalPlayer.Character
			local hrp = char and char:FindFirstChild("HumanoidRootPart")
			if not (char and hrp) then continue end

			if not char:GetAttribute("SavingOriginalPos") then
				saveOriginalPosAttr()
			end

			char:SetAttribute("SavingOriginalPos", true)
			Workspace.FallenPartsDestroyHeight = 0/0

			local desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
			hrp:PivotTo(desiredCFrame)

			setNoCollideChar(tChar)
			RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
			task.wait(0.05)
			RemoteDestroyGrabLine:FireServer(tRoot)
			task.wait(0.05)

			if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
				task.wait(0.05)
				modifyTarget(tRoot, tHum)
			end
		end
	end

	task.wait(0.05)
end

local function StartLoopKillAll()
	if KillHB then KillHB:Disconnect() end
	KillHB = RunService.Heartbeat:Connect(performKillAll)
end

local function StopLoopKillAll()
	if KillHB then
		KillHB:Disconnect()
		KillHB = nil
	end
	detachCamera()
end

local function executeKickAll()
	attachCameraToSpawn()

	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer then
			local c = p.Character
			local root = c and c:FindFirstChild("HumanoidRootPart")
			local head = c and c:FindFirstChild("Head")
			local hum = c and c:FindFirstChild("Humanoid")

			if not (root and head and hum) or hum.Health <= 0 then continue end
			if isTooHigh(p) then continue end
			if p:FindFirstChild("InPlot") and p.InPlot.Value then continue end

			local selfChar = LocalPlayer.Character
			local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
			if not selfRoot then continue end

			local saved = selfChar:GetPivot()
			selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))

			DisableCollisions(c)
			RemoteSetNetworkOwner:FireServer(root, root.CFrame)
			task.wait()
			selfChar:PivotTo(saved)

			task.wait(0.05)
			RemoteDestroyGrabLine:FireServer(root)
			task.wait(0.05)

			local po = head:FindFirstChild("PartOwner")
			if po and po.Value == LocalPlayer.Name then
				sendToSky(root, hum)
			end
		end
	end

	task.wait(0.05)
end

local function StartLoopKickAll()
	if KickHB then KickHB:Disconnect() end
	LoopKickOn = true

	KickHB = RunService.Heartbeat:Connect(function()
		if LoopKickOn then
			executeKickAll()
		end
	end)
end

local function StopLoopKickAll()
	LoopKickOn = false
	if KickHB then
		KickHB:Disconnect()
		KickHB = nil
	end
	detachCamera()
end

LoopTab:CreateToggle({
	Name = 'LoopKill All	 <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>  	<font face="GothamBlack" color="rgb(255,0,0)">OP</font>	 <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
	CurrentValue = false,
	Flag = "LoopKillAllToggle",
	Callback = function(v)
		if v then
			StartLoopKillAll()
		else
			StopLoopKillAll()
		end
	end
})

LoopTab:CreateToggle({
	Name = 'LoopKick All 	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>	 <font face="GothamBlack" color="rgb(255,0,0)">OP</font> 	  <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
	CurrentValue = false,
	Flag = "LoopKickAllToggle",
	Callback = function(v)
		if v then
			StartLoopKickAll()
		else
			StopLoopKickAll()
		end
	end
})

--[[======================== AURA ========================]]--
AuraTab:CreateSection("OP AURA")
-- Death Aura
local function isFriend(player) return false end
local function canInteractWithTarget(targetChar) return true end
local function handlePartOwner(targetChar, callback)
    local root = targetChar:FindFirstChild("HumanoidRootPart")
    if root then
        SetNetworkOwner:FireServer(root, root.CFrame)
        callback()
    end
end
local function performDeathAura()
    getgenv().deathAuraConnection = RunService.Heartbeat:Connect(function()
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and not isFriend(player) then
                local targetChar = player.Character
                if targetChar and canInteractWithTarget(targetChar) then
                    local tRoot = targetChar:FindFirstChild("HumanoidRootPart")
                    local tHum = targetChar:FindFirstChild("Humanoid")
                    if tRoot and tHum and (hrp.Position - tRoot.Position).Magnitude <= 29 then
                        SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                        DestroyGrabLine:FireServer(tRoot)
                        handlePartOwner(targetChar, function()
                            for _, part in pairs(tHum.Parent:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = CFrame.new(-9e11, 9e12, -9e11)
                                end
                            end
                            task.wait()
                            for _, part in pairs(tHum.Parent:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = CFrame.new(-9e11, 9e12, -9e11)
                                end
                            end
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.Velocity = Vector3.new(0, 9e10, 0)
                            bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bodyVelocity.P = 100000075
                            bodyVelocity.Parent = tRoot
                            tHum.BreakJointsOnDeath = false
                            tHum:ChangeState(Enum.HumanoidStateType.Dead)
                            task.delay(2, function()
                                if bodyVelocity and bodyVelocity.Parent then bodyVelocity:Destroy() end
                            end)
                        end)
                    end
                end
            end
        end
    end)
end
local function stopDeathAura()
    if getgenv().deathAuraConnection then getgenv().deathAuraConnection:Disconnect() getgenv().deathAuraConnection = nil end
end
AuraTab:CreateToggle({ Name = 'Death Aura	 <font face="GothamBlack" color="rgb(255,0,0)">OP</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>', CurrentValue = false, Flag = "DeathAura", Callback = function(v) if v then performDeathAura() else stopDeathAura() end end })

-- Kick Aura
local function performKickAura()
    getgenv().kickAuraConnection = RunService.Heartbeat:Connect(function()
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and not isFriend(player) then
                local targetChar = player.Character
                if targetChar and canInteractWithTarget(targetChar) then
                    local tRoot = targetChar:FindFirstChild("HumanoidRootPart")
                    if tRoot and (hrp.Position - tRoot.Position).Magnitude <= 29 then
                        SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                        DestroyGrabLine:FireServer(tRoot)
                        handlePartOwner(targetChar, function()
                            tRoot.CFrame = CFrame.new(0, 9e13, 0)
                            task.wait()
                            tRoot.CFrame = CFrame.new(0, 9e11, 0)
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.Velocity = Vector3.new(0, 9e12, 0)
                            bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bodyVelocity.P = 100000000075
                            bodyVelocity.Parent = tRoot
                            task.delay(2, function()
                                if bodyVelocity and bodyVelocity.Parent then bodyVelocity:Destroy() end
                            end)
                        end)
                    end
                end
            end
        end
    end)
end
local function stopKickAura()
    if getgenv().kickAuraConnection then getgenv().kickAuraConnection:Disconnect() getgenv().kickAuraConnection = nil end
end
AuraTab:CreateToggle({ Name = 'Kick Aura	<font face="GothamBlack" color="rgb(255,0,0)">OP</font>	  <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>   <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>', CurrentValue = false, Flag = "KickAura", Callback = function(v) if v then performKickAura() else stopKickAura() end end })

-- Throw Aura
local throwSpeed = 100
local throwRadius = 30
local throwBodies, throwConnection = {}, nil
AuraTab:CreateToggle({
    Name = 'Throw Aura	  <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "ThrowAura",
    Callback = function(enabled)
        if enabled then
            throwConnection = RunService.Heartbeat:Connect(function()
                local centerRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if not centerRoot then return end
                local centerPos = centerRoot.Position
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        local root = player.Character:FindFirstChild("HumanoidRootPart")
                        if root and (root.Position - centerPos).Magnitude <= throwRadius then
                            local bv = root:FindFirstChild("ThrowBV")
                            if not bv then
                                bv = Instance.new("BodyVelocity")
                                bv.Name = "ThrowBV"
                                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                bv.Parent = root
                                throwBodies[root] = bv
                            end
                            bv.Velocity = Vector3.new(
                                math.random(-throwSpeed, throwSpeed),
                                math.random(5, throwSpeed),
                                math.random(-throwSpeed, throwSpeed)
                            )
                            pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                        else
                            if throwBodies[root] then
                                throwBodies[root]:Destroy()
                                throwBodies[root] = nil
                            end
                        end
                    end
                end
            end)
        else
            if throwConnection then throwConnection:Disconnect() throwConnection = nil end
            for _, bv in pairs(throwBodies) do if bv and bv.Parent then bv:Destroy() end end
            throwBodies = {}
        end
    end,
})
AuraTab:CreateSlider({ Name = "Throw Speed", Range = {10, 1000}, Increment = 1, Suffix = "Velocity", CurrentValue = throwSpeed, Flag = "ThrowSpeed", Callback = function(v) throwSpeed = v end })

-- Telekinesis Aura
local Bodies, SetNetworkQueue, SetNetworkBusy = {}, {}, false
local Active, TornadoOffset, TornadoHeight, TornadoSpeed = false, 10, 5, 0.1
local TornadoFollowPlayer, fallbackPosition = "", Vector3.new(0, 10, 0)
local function ClearBodies()
    for _, v in pairs(Bodies) do if v and v.Parent then v:Destroy() end end
    Bodies, SetNetworkQueue, SetNetworkBusy = {}, {}, false
end
local function ApplyMotion(part, cframe, angle, radius, height)
    if not part or part.Anchored then return end
    local bp = part:FindFirstChild("TornadoAuraVelocity")
    if not bp then
        bp = Instance.new("BodyPosition")
        bp.Name = "TornadoAuraVelocity"
        bp.MaxForce = Vector3.new(1e6,1e6,1e6)
        bp.P = 50000
        bp.D = 1250
        bp.Parent = part
        table.insert(Bodies, bp)
    end
    bp.Position = Vector3.new(
        cframe.Position.X + math.cos(angle) * radius,
        cframe.Position.Y + height,
        cframe.Position.Z + math.sin(angle) * radius
    )
    local bav = part:FindFirstChild("TornadoAuraAngularVelocity")
    if not bav then
        bav = Instance.new("BodyAngularVelocity")
        bav.Name = "TornadoAuraAngularVelocity"
        bav.MaxTorque = Vector3.new(1e5,1e5,1e5)
        bav.AngularVelocity = Vector3.new(0,TornadoSpeed*10,0)
        bav.Parent = part
    else
        bav.AngularVelocity = Vector3.new(0,TornadoSpeed*10,0)
    end
end
local function SetNetworkOwnerPart(part)
    if not part or part.Anchored then return end
    pcall(function() SetNetworkOwner:FireServer(part, part.CFrame, part) end)
    part:SetAttribute("SetNetworkDone", true)
end
local function GetPlayerRoot(player)
    local char = player.Character
    if char then return char:FindFirstChild("HumanoidRootPart") end
end
local function ProcessTargets(centerCFrame, angle)
    local lpRoot = GetPlayerRoot(LocalPlayer)
    local lpPos = lpRoot and lpRoot.Position or fallbackPosition
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local root = GetPlayerRoot(p)
            if root and not root.Anchored then
                ApplyMotion(root, centerCFrame, angle, TornadoOffset, TornadoHeight)
                if (root.Position - lpPos).Magnitude <= 15 then
                    SetNetworkOwnerPart(root)
                end
            end
        end
    end
end
RunService.Heartbeat:Connect(function()
    if not Active then ClearBodies() return end
    local centerCFrame
    if TornadoFollowPlayer ~= "" then
        local tp = Players:FindFirstChild(TornadoFollowPlayer)
        centerCFrame = tp and GetPlayerRoot(tp) and GetPlayerRoot(tp).CFrame or CFrame.new(fallbackPosition)
    else
        local root = GetPlayerRoot(LocalPlayer)
        centerCFrame = root and root.CFrame or CFrame.new(fallbackPosition)
    end
    local angle = (tick()*TornadoSpeed*2*math.pi) % (2*math.pi)
    ProcessTargets(centerCFrame, angle)
end)
AuraTab:CreateSection('Telekinesis Aura')
AuraTab:CreateToggle({ Name = 'Telekinesis Aura	   <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>', CurrentValue = false, Flag = "TelekinesisAura", Callback = function(v) Active = v if not v then ClearBodies() end end })
local PlayerDropdown = AuraTab:CreateDropdown({
    Name = "Select Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(option)
        TornadoFollowPlayer = option[1]:match("@([%w_]+)")
    end
})
local function optionText(plr)
    return string.format("👤 @%s (%s)", plr.Name, plr.DisplayName or "")
end
local function refreshDropdown()
    local opts = {}
    for _, p in ipairs(Players:GetPlayers()) do
        table.insert(opts, optionText(p)) 
    end
    PlayerDropdown:Refresh(opts)
end
Players.PlayerAdded:Connect(function()
    task.wait(0.5)
    refreshDropdown()
end)
Players.PlayerRemoving:Connect(function()
    task.wait(0.5)
    refreshDropdown()
end)
refreshDropdown()
AuraTab:CreateSlider({ Name = "Distance", Range = {1,10000}, Increment = 1, Suffix = "Offset", CurrentValue = TornadoOffset, Flag = "AuraDistance", Callback = function(v) TornadoOffset = v end })
AuraTab:CreateSlider({ Name = "Height", Range = {1,10000}, Increment = 1, Suffix = "Height", CurrentValue = TornadoHeight, Flag = "AuraHeight", Callback = function(v) TornadoHeight = v end })
AuraTab:CreateSlider({ Name = "Rotation Speed", Range = {0.01,1}, Increment = 0.001, Suffix = "Speed", CurrentValue = TornadoSpeed, Flag = "AuraSpeed", Callback = function(v) TornadoSpeed = v end })

-- Air Suspend Aura
local auraSpeed = 50
local auraBodies, auraCoroutine = {}, nil
AuraTab:CreateSection("Air Aura")
AuraTab:CreateToggle({
    Name = 'Air Suspend Aura     <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "AirSuspendAura",
    Callback = function(enabled)
        if enabled then
            auraCoroutine = coroutine.create(function()
                while task.wait(0.02) do
                    local c = LocalPlayer.Character
                    local hrp = c and c:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        for _, player in pairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer and player.Character then
                                local root = player.Character:FindFirstChild("Torso") or player.Character:FindFirstChild("HumanoidRootPart")
                                if root then
                                    local bv = auraBodies[player]
                                    if not bv or not bv.Parent then
                                        bv = Instance.new("BodyVelocity")
                                        bv.Name = "AirSuspendBV"
                                        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        bv.Parent = root
                                        auraBodies[player] = bv
                                    end
                                    bv.Velocity = Vector3.new(0, auraSpeed, 0)
                                    pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(auraCoroutine)
        else
            if auraCoroutine then coroutine.close(auraCoroutine) auraCoroutine = nil end
            for _, bv in pairs(auraBodies) do if bv and bv.Parent then bv:Destroy() end end
            auraBodies = {}
        end
    end
})
AuraTab:CreateInput({ Name = "Speed", CurrentValue = tostring(auraSpeed), PlaceholderText = "Enter speed (-100 to 500)", RemoveTextAfterFocusLost = false, Flag = "AirSuspendSpeed", Callback = function(text) local v = tonumber(text) if v then auraSpeed = v end end })

AuraTab:CreateSection("Teleport Aura")
local teleportAuraEnabled, teleportTarget = false, "Cursed Cloud"
local teleportBodies, teleportGyros, teleportCoroutine = {}, {}, nil
AuraTab:CreateToggle({
    Name = 'Enable Teleport Aura		 <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
    CurrentValue = false,
    Flag = "TeleportAura",
    Callback = function(enabled)
        teleportAuraEnabled = enabled
        if enabled then
            teleportCoroutine = coroutine.create(function()
                while task.wait(0.05) do
                    local character = LocalPlayer.Character
                    local hrp = character and character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        for _, player in pairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer and player.Character then
                                local root = player.Character:FindFirstChild("HumanoidRootPart")
                                if root and (hrp.Position - root.Position).Magnitude <= 30 then
                                    pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                                    local targetPos = Vector3.new(0,0,0)
                                    if teleportTarget == "Cursed Cloud" then
                                        targetPos = Vector3.new(900000, 100, 3000)
                                    elseif teleportTarget == "Spawn" then
                                        targetPos = Vector3.new(0,0,0)
                                    elseif teleportTarget == "Void" then
                                        targetPos = Vector3.new(0, 1e12, 0)
                                    end
                                    root.CFrame = CFrame.new(targetPos)
                                    if teleportBodies[player] and teleportBodies[player].Parent then teleportBodies[player]:Destroy() end
                                    local bp = Instance.new("BodyPosition")
                                    bp.Name = "TeleportAuraBP"
                                    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    bp.P = 50000
                                    bp.D = 1250
                                    bp.Position = targetPos
                                    bp.Parent = root
                                    teleportBodies[player] = bp
                                    if teleportGyros[player] and teleportGyros[player].Parent then teleportGyros[player]:Destroy() end
                                    local bg = Instance.new("BodyGyro")
                                    bg.Name = "TeleportAuraBG"
                                    bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    bg.CFrame = root.CFrame
                                    bg.Parent = root
                                    teleportGyros[player] = bg
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(teleportCoroutine)
        else
            if teleportCoroutine then coroutine.close(teleportCoroutine) teleportCoroutine = nil end
            for _, bp in pairs(teleportBodies) do if bp and bp.Parent then bp:Destroy() end end
            for _, bg in pairs(teleportGyros) do if bg and bg.Parent then bg:Destroy() end end
            teleportBodies, teleportGyros = {}, {}
        end
    end
})
AuraTab:CreateDropdown({ Name = "Teleport Location", Options = {"Cursed Cloud", "Spawn", "Void"}, CurrentOption = {"Cursed Cloud"}, Flag = "TeleportAuraLocation", Callback = function(option) teleportTarget = option[1] end })

--[[======================== ESP ========================]]--

ESPTab:CreateSection("Player Highlight")
_G.ESP_Highlights = _G.ESP_Highlights or {}
_G.ESP_Tags = _G.ESP_Tags or {}
_G.HighlightEnabled = false
_G.ESPIconEnabled = false
_G.HighlightFillColor = Color3.fromRGB(255, 255, 255)
_G.OutlineColor = Color3.fromRGB(0, 0, 0)
_G.HighlightFillTransparency = 0.5
_G.OutlineTransparency = 0.5
_G.HighlightMode = "AlwaysOnTop"

local Connections = {}

function DisconnectAll()
	for _, conn in pairs(Connections) do
		pcall(function() conn:Disconnect() end)
	end
	table.clear(Connections)
end

function _G.RemoveESP(p)
	if _G.ESP_Highlights[p] then
		_G.ESP_Highlights[p]:Destroy()
		_G.ESP_Highlights[p] = nil
	end
	if _G.ESP_Tags[p] then
		_G.ESP_Tags[p]:Destroy()
		_G.ESP_Tags[p] = nil
	end
end

function _G.CreateESP(char, player)
	if not char or not player or not char:FindFirstChild("HumanoidRootPart") then return end

	_G.RemoveESP(player)

	local highlight = Instance.new("Highlight")
	highlight.FillColor = _G.HighlightFillColor
	highlight.OutlineColor = _G.OutlineColor
	highlight.FillTransparency = _G.HighlightFillTransparency
	highlight.OutlineTransparency = _G.OutlineTransparency
	highlight.DepthMode = Enum.HighlightDepthMode[_G.HighlightMode]
	highlight.Adornee = char
	highlight.Parent = game:GetService("CoreGui")
	_G.ESP_Highlights[player] = highlight
end

function _G.CreateIcon(char, player)
	if not char or not player then return end
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	_G.RemoveESP(player)

	local billboard = Instance.new("BillboardGui")
	billboard.Adornee = root
	billboard.Size = UDim2.new(0, 100, 0, 100)
	billboard.AlwaysOnTop = true
	billboard.StudsOffset = Vector3.new(0, 3, 0)

	local img = Instance.new("ImageLabel")
	img.BackgroundTransparency = 1
	img.Size = UDim2.new(0, 64, 0, 64)
	img.Position = UDim2.new(0.5, -32, 0, 0)
	img.Image = "https://www.roblox.com/headshot-thumbnail/image?userId=" .. player.UserId .. "&width=420&height=420&format=png"
	img.Parent = billboard

	local txt = Instance.new("TextLabel")
	txt.BackgroundTransparency = 1
	txt.Size = UDim2.new(1, 0, 0, 32)
	txt.Position = UDim2.new(0, 0, 1, -32)
	txt.Text = player.DisplayName .. " (@" .. player.Name .. ")"
	txt.TextColor3 = Color3.new(1, 1, 1)
	txt.TextStrokeTransparency = 0
	txt.Font = Enum.Font.SourceSansBold
	txt.TextScaled = true
	txt.Parent = billboard

	billboard.Parent = game:GetService("CoreGui")
	_G.ESP_Tags[player] = billboard
end

function _G.UpdateAllESP()
    local enumMode = Enum.HighlightDepthMode[_G.HighlightMode] or Enum.HighlightDepthMode.AlwaysOnTop

    for _, h in pairs(_G.ESP_Highlights) do
        if h then
            h.FillColor = _G.HighlightFillColor
            h.OutlineColor = _G.OutlineColor
            h.FillTransparency = _G.HighlightFillTransparency
            h.OutlineTransparency = _G.OutlineTransparency
            h.DepthMode = enumMode
        end
    end
end


function RefreshAll()
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer then
			local char = p.Character
			if char and char:FindFirstChild("HumanoidRootPart") then
				if _G.HighlightEnabled then
					_G.CreateESP(char, p)
				else
					if _G.ESP_Highlights[p] then _G.ESP_Highlights[p]:Destroy() _G.ESP_Highlights[p] = nil end
				end

				if _G.ESPIconEnabled then
					_G.CreateIcon(char, p)
				else
					if _G.ESP_Tags[p] then _G.ESP_Tags[p]:Destroy() _G.ESP_Tags[p] = nil end
				end
			end
		end
	end
end

function AttachPlayer(p)
	table.insert(Connections, p.CharacterAdded:Connect(function(c)
		task.wait(1)
		if _G.HighlightEnabled then _G.CreateESP(c, p) end
		if _G.ESPIconEnabled then _G.CreateIcon(c, p) end

		local diedConn
		diedConn = c:WaitForChild("Humanoid").Died:Connect(function()
			_G.RemoveESP(p)
			if diedConn then diedConn:Disconnect() end
		end)
		table.insert(Connections, diedConn)
	end))
end

DisconnectAll()
for _, p in pairs(Players:GetPlayers()) do
	if p ~= LocalPlayer then
		AttachPlayer(p)
	end
end

table.insert(Connections, Players.PlayerAdded:Connect(function(p)
	AttachPlayer(p)
end))

table.insert(Connections, Players.PlayerRemoving:Connect(function(p)
	_G.RemoveESP(p)
end))

ESPTab:CreateToggle({
	Name = "ESP (Highlight)",
	Default = false,
	CurrentValue = false,
	Flag = "Toggle145678",
	Callback = function(v)
		_G.HighlightEnabled = v
		RefreshAll()
	end,
})

ESPTab:CreateColorPicker({
	Name = "Highlight Fill Color",
	Color = Color3.fromRGB(255,255,255),
	Flag = "ColorPicker1",
	Callback = function(c)
		_G.HighlightFillColor = c
		_G.UpdateAllESP()
	end,
})

ESPTab:CreateColorPicker({
	Name = "Outline Color",
	Color = Color3.fromRGB(255,255,255),
	Flag = "ColorPicker2",
	Callback = function(c)
		_G.OutlineColor = c
		_G.UpdateAllESP()
	end,
})

ESPTab:CreateSlider({
	Name = "Highlight Fill Transparency",
Range = {0, 1},
   Increment = 0.1,
   Suffix = "Transparency",
   CurrentValue = 0.5,
   Flag = "Slider1",
	Callback = function(v)
		_G.HighlightFillTransparency = v
		_G.UpdateAllESP()
	end,
})

ESPTab:CreateSlider({
	Name = "Outline Transparency",
	Range = {0, 1},
   Increment = 0.1,
   Suffix = "Transparency",
   CurrentValue = 0.5,
   Flag = "Slider2",
	Callback = function(v)
		_G.OutlineTransparency = v
		_G.UpdateAllESP()
	end,
})

ESPTab:CreateDropdown({
    Name = "Highlight Mode",
    Options = {"AlwaysOnTop", "Occluded"},
    CurrentOption = {"AlwaysOnTop"},
    MultipleOptions = false,
    Flag = "DropdownYUIO1",
    Callback = function(v)
        local mode = v
        if typeof(v) == "table" then
            mode = v[1]
        end

        if typeof(mode) ~= "string" then
            mode = "AlwaysOnTop"
        end

        local enumMode = Enum.HighlightDepthMode[mode]
        if not enumMode then
            enumMode = Enum.HighlightDepthMode.AlwaysOnTop
            mode = "AlwaysOnTop"
        end

        _G.HighlightMode = mode
        _G.UpdateAllESP()
    end,
})

ESPTab:CreateSection("Player Icon")
ESPTab:CreateToggle({
	Name = "ESP Icon",
	Default = false,
	Callback = function(v)
		_G.ESPIconEnabled = v
		RefreshAll()
	end,
})
--[[======================== KEYBINDS ========================]]--

KeybindTab:CreateSection("Keybinds ")
local mouse = LocalPlayer:GetMouse()
local function teleportToMouse()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (hrp and mouse.Target and mouse.Target:IsA("BasePart")) then return end
    local targetPosition = mouse.Hit.Position
    hrp.CFrame = CFrame.new(targetPosition + Vector3.new(0, 5, 0))
end
KeybindTab:CreateKeybind({ Name = "Teleport to Mouse", CurrentKeybind = "Z", HoldToInteract = false, Callback = teleportToMouse })

KeybindTab:CreateKeybind({
    Name = "Spawn Pallet",
    CurrentKeybind = "P",
    HoldToInteract = false,
    Callback = function()
        local hrp = (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("HumanoidRootPart")
        SpawnToyRF:InvokeServer("PalletLightBrown", hrp.CFrame * CFrame.new(0, 0, -3), Vector3.new(0, 5, 0))
    end,
})

KeybindTab:CreateSection("Limb OP ")
local function deleteLimbs(plrModel)
    if not plrModel then return end
    for _, limbName in ipairs({"Right Arm", "Left Arm", "Right Leg", "Left Leg"}) do
        local limb = plrModel:FindFirstChild(limbName)
        if limb then
            for _, obj in ipairs(limb:GetChildren()) do
                if obj:IsA("Motor6D") or obj:IsA("Weld") or obj:IsA("WeldConstraint") then obj:Destroy() end
            end
            limb.CFrame = CFrame.new(0, -10000, 0)
        end
    end
end
KeybindTab:CreateKeybind({
    Name = 'Delete Limbs  <font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>   <font face=\"GothamBlack\" color=\"rgb(39,245,218)\">PREMIUM</font>',
    CurrentKeybind = "U",
    HoldToInteract = false,
    Callback = function()
        local g = workspace:FindFirstChild("GrabParts")
        local gp = g and g:FindFirstChild("GrabPart")
        if not gp then return end
        local weld = gp:FindFirstChild("WeldConstraint")
        if not weld or not weld.Part1 then return end
        local grabbedPlayerModel = weld.Part1:FindFirstAncestorOfClass("Model")
        if not grabbedPlayerModel then return end

        local spawnCFrame = gp.CFrame

        deleteLimbs(grabbedPlayerModel)

        if SpawnToyRF then
            SpawnToyRF:InvokeServer("PalletLightBrown", spawnCFrame, Vector3.new(0,0,0))
        end
    end,
})

KeybindTab:CreateParagraph({ Title = "How to use it :", Content = "Grab a part of a player <font face=\"GothamBlack\" color=\"rgb(255,0,0)\">TORSO</font> or <font face=\"GothamBlack\" color=\"rgb(255,0,0)\">HEAD</font> and <font face=\"GothamBlack\" color=\"rgb(255,0,0)\">RAGDOLL</font> him, then press the Keybind to remove all limbs. A pallet will spawn exactly where the grabbed part is." })


local SpawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
local RagdollRemote = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

KeybindTab:CreateSection("Lobotomy OP")
local Button = KeybindTab:CreateButton({
    Name = 'Lobotomy    <font face="GothamBlack" color="rgb(255,0,0)">OP</font>   <font face="GothamBlack" color="rgb(39,245,218)">PREMIUM</font>',
    Callback = function()
        SpawnRemote:InvokeServer("CreatureBlobman", humanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -16.157, 0))

        task.spawn(function()
            while true do
                local blobmanFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                if blobmanFolder and blobmanFolder:FindFirstChild("CreatureBlobman") then
                    local seat = blobmanFolder.CreatureBlobman:FindFirstChild("VehicleSeat")
                    if seat and seat.Occupant ~= Humanoid then
                        seat:Sit(Humanoid)
                    end
                end
                task.wait(0.1)
            end
        end)

        task.spawn(function()
            while true do
                pcall(function()
                    LocalPlayer.IsHeld.Value = true
                end)
                task.wait()
            end
        end)

        task.spawn(function()
            while true do
                for _ = 1, 12 do
                    RagdollRemote:FireServer(humanoidRootPart, 0)
                end
                task.wait()
            end
        end)
    end
})

KeybindTab:CreateParagraph({
    Title = "How it work : ",
    Content = "Basically, it breaks grabbing, destroys the house barrier, and prevents us from spawning items."
})


InfoTab:CreateSection("Coming Soon")

InfoTab:CreateParagraph({
    Title = 'New features coming soon, Like <font face="GothamBlack" color="rgb(255,0,0)">Silent Aim</font>, <font face="GothamBlack" color="rgb(255,0,0)">Grab Aura</font>,<font face="GothamBlack" color="rgb(255,0,0)">Kick Grab</font>, and <font face="GothamBlack" color="rgb(255,0,0)">more</font>...',
    Content = ''
})
notify("Bloody [PREMIUM]", "V2")
