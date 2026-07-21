local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
local Options = Library.Options
local Toggles = Library.Toggles
Library.ForceCheckbox = false
local Window = Library:CreateWindow({
	Title = "Ragalic client",
	Footer = "Ragalic client",
	NotifySide = "Right",
	ShowCustomCursor = true,
})
local Tabs = {
	Defense = Window:AddTab("defense", "shield"),
	Target = Window:AddTab("target", "crosshair"),
	Grab = Window:AddTab("grab", "hand"),
	Player = Window:AddTab("player", "user"),
	Misc = Window:AddTab("misc", "layers"),
	Build = Window:AddTab("build", "box"),
	Fun = Window:AddTab("fun", "smile"),
	Keybinds = Window:AddTab("keybinds", "keyboard"),
	Notifications = Window:AddTab("notifications", "bell"),
	Auras = Window:AddTab("auras", "sparkles"),
	["UI Settings"] = Window:AddTab("UI Settings", "settings")
}
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StarterGui = game:GetService("StarterGui")
local PS = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local R = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = workspace
local Player = PS.LocalPlayer
local Camera = Workspace.CurrentCamera
local CE = RS:WaitForChild("CharacterEvents", 10)
local BeingHeld = Player:WaitForChild("IsHeld", 10)
local StruggleEvent = CE and CE:WaitForChild("Struggle")

-- ===============================
-- MOBILE SUPPORT: virtual keys
-- ===============================
_G.MobileKeys = {W=false,A=false,S=false,D=false,Space=false,Ctrl=false}
_G.MobileThrow = false
local isMobile = UserInputService.TouchEnabled

local function notify(title, content, duration)
	Library:Notify({
		Title = title or "Notification",
		Description = content or "",
		Time = duration or 5,
	})
end
local function sendHubLoadedMessage()
	local message = " Owner Version | Ragalic client loaded. "
	local sent = false
	pcall(function()
		local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
		if chatEvents then
			local say = chatEvents:FindFirstChild("SayMessageRequest")
			if say and typeof(say.FireServer) == "function" then
				say:FireServer(message, "All")
				sent = true
			end
		end
	end)
	if not sent then
		pcall(function()
			StarterGui:SetCore("ChatMakeSystemMessage", {
				Text = message;
				Color = Color3.fromRGB(255, 170, 0);
				Font = Enum.Font.SourceSansBold;
				FontSize = Enum.FontSize.Size18;
			})
		end)
	end
end
task.spawn(function()
	task.wait(1)
	sendHubLoadedMessage()
end)
local paintPartsBackup = {}
local paintConnections = {}
local function deleteAllPaintParts()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			local clone = obj:Clone()
			clone.Archivable = true
			paintPartsBackup[obj:GetDebugId()] = {
				clone = clone,
				parent = obj.Parent
			}
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
	table.insert(paintConnections, Workspace.DescendantAdded:Connect(function(obj)
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			task.defer(function()
				if obj and obj.Parent then
					local clone = obj:Clone()
					clone.Archivable = true
					paintPartsBackup[obj:GetDebugId()] = {
						clone = clone,
						parent = obj.Parent
					}
					obj:Destroy()
				end
			end)
		end
	end))
end
local function disconnectWatchers()
	for _, conn in ipairs(paintConnections) do
		if conn.Connected then
			conn:Disconnect()
		end
	end
	paintConnections = {}
end
local function setTouchQuery(state)
	local char = Workspace:FindFirstChild(Player.Name)
	if not char then
		return
	end
	for _, v in ipairs(char:GetChildren()) do
		if v:IsA("Part") or v:IsA("BasePart") then
			v.CanTouch = state
			v.CanQuery = state
		end
	end
end
local antiGucciConnection
local safePosition
local restoreFrames = 0
local function spawnBlobman()
	local args = {
		[1] = "CreatureBlobman",
		[2] = CFrame.new(0, 5000000, 0),
		[3] = Vector3.new(0, 60, 0)
	}
	pcall(function()
		ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
	end)
	local folder = Workspace:WaitForChild(Player.Name .. "SpawnedInToys", 5)
	if folder and folder:FindFirstChild("CreatureBlobman") then
		local blob = folder.CreatureBlobman
		if blob:FindFirstChild("Head") then
			blob.Head.CFrame = CFrame.new(0, 50000, 0)
			blob.Head.Anchored = true
		end
		notify("Success", "Blobman Spawned!", 3)
	end
end
local function startAntiGucci()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePosition = rootPart.Position
	local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	local blob = folder and folder:FindFirstChild("CreatureBlobman")
	local seat = blob and blob:FindFirstChild("VehicleSeat")
	if not blob then
		spawnBlobman()
		task.wait(1)
		folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
		blob = folder and folder:FindFirstChild("CreatureBlobman")
		seat = blob and blob:FindFirstChild("VehicleSeat")
	end
	if seat and seat:IsA("VehicleSeat") then
		rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
		seat:Sit(humanoid)
	end
	humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
		if humanoid.Jump and humanoid.Sit then
			restoreFrames = 15
			safePosition = rootPart.Position
		end
	end)
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
	end
	antiGucciConnection = R.Heartbeat:Connect(function()
		if not rootPart or not humanoid then
			return
		end
		ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
		if restoreFrames > 0 then
			rootPart.CFrame = CFrame.new(safePosition)
			restoreFrames = restoreFrames - 1
		end
	end)
	task.spawn(function()
		while humanoid.Sit do
			task.wait(1)
		end
		task.wait(0.5)
		rootPart.CFrame = CFrame.new(safePosition)
	end)
end
local function stopAntiGucci()
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
		antiGucciConnection = nil
	end
	local blobFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
		blobFolder.CreatureBlobman:Destroy()
	end
end
local antiGucciConnectionTrain
local safePositionTrain
local restoreFramesTrain = 0
local function startAntiGucciTrain()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePositionTrain = rootPart.Position
	local folder = workspace.Map.AlwaysHereTweenedObjects
	local train = folder and folder:FindFirstChild("Train")
	local seat
	if train then
		for _, d in ipairs(train:GetDescendants()) do
			if d:IsA("Seat") then
				seat = d
				break
			end
		end
	end
	if seat then
		rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
		seat:Sit(humanoid)
	end
	humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
		if humanoid.Jump and humanoid.Sit then
			restoreFramesTrain = 15
			safePositionTrain = rootPart.Position
		end
	end)
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
	end
	antiGucciConnectionTrain = R.Heartbeat:Connect(function()
		if not rootPart or not humanoid then
			return
		end
		ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
		if restoreFramesTrain > 0 then
			rootPart.CFrame = CFrame.new(safePositionTrain)
			restoreFramesTrain = restoreFramesTrain - 1
		end
	end)
	task.spawn(function()
		while humanoid.Sit do
			task.wait(1)
		end
		task.wait(0.5)
		rootPart.CFrame = CFrame.new(safePositionTrain)
	end)
end
local function stopAntiGucciTrain()
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
		antiGucciConnectionTrain = nil
	end
	local trainFolder = workspace.Map.AlwaysHereTweenedObjects
	if trainFolder and trainFolder:FindFirstChild("Train") then
		ResetPlayer(game.Players.LocalPlayer)
	end
end
local DefenseGroup = Tabs.Defense:AddLeftGroupbox("Defense Main")
local DefenseExtra = Tabs.Defense:AddRightGroupbox("Extra Defense")
local antiGrabExplosionConn, antiGrabHeldConn, antiGrabStruggleConn, antiGrabHumConn, antiGrabAnchorConn
local antiGrabRootCF, antiGrabRootPos, antiGrabHardFreeze = nil, nil, false
local function antiGrabUnfreeze(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		hrp.Anchored = false
		if hrp:FindFirstChild("FreezeJoint") then
			hrp.FreezeJoint:Destroy()
		end
	end
	antiGrabHardFreeze = false
	if antiGrabAnchorConn then
		antiGrabAnchorConn:Disconnect()
		antiGrabAnchorConn = nil
	end
end
local function antiGrabFreezeInPlace(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	antiGrabRootCF = hrp.CFrame
	antiGrabRootPos = hrp.Position
	antiGrabHardFreeze = true
	if not hrp:FindFirstChild("FreezeJoint") then
		local align = Instance.new("AlignPosition")
		align.Name = "FreezeJoint"
		align.Mode = Enum.PositionAlignmentMode.OneAttachment
		align.MaxForce = 1e6
		align.MaxVelocity = 0
		align.Responsiveness = 200
		local att = Instance.new("Attachment", hrp)
		align.Attachment0 = att
		align.Position = antiGrabRootPos
		align.Parent = hrp
	end
	antiGrabAnchorConn = R.Heartbeat:Connect(function()
		if antiGrabHardFreeze and hrp then
			hrp.AssemblyLinearVelocity = Vector3.zero
			hrp.AssemblyAngularVelocity = Vector3.zero
			hrp.CFrame = antiGrabRootCF
		end
	end)
end
local function antiGrabReconnect()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	local fp = hrp:FindFirstChild("FirePlayerPart")
	if fp then
		fp:Destroy()
	end
	if antiGrabHumConn then
		antiGrabHumConn:Disconnect()
	end
	antiGrabHumConn = hum.Changed:Connect(function(p)
		if p == "Sit" and hum.Sit then
			if not (hum.SeatPart and tostring(hum.SeatPart.Parent) == "CreatureBlobman") then
				hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
				hum.Sit = false
			end
		end
	end)
end
local autoStruggleConn = nil
DefenseGroup:AddToggle("AntiGrabObsidian", {
	Text = "Anti Grab",
	Default = false,
	Callback = function(Value)
		local RunService = game:GetService("RunService")
		local ReplicatedStorage = game:GetService("ReplicatedStorage")
		local localPlayer = game:GetService("Players").LocalPlayer
		local Struggle = ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("Struggle")
		if Value then
			if autoStruggleConn then
				autoStruggleConn:Disconnect()
			end
			autoStruggleConn = RunService.Heartbeat:Connect(function()
				local character = localPlayer.Character
				if character and character:FindFirstChild("Head") then
					local head = character.Head
					if head:FindFirstChild("PartOwner") then
						task.spawn(function()
							if Struggle then
								Struggle:FireServer(localPlayer)
							end
							pcall(function()
								ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
							end)
							for _, part in pairs(character:GetChildren()) do
								if part:IsA("BasePart") then
									part.Anchored = true
								end
							end
							local isHeld = localPlayer:FindFirstChild("IsHeld")
							while isHeld and isHeld.Value do
								task.wait()
							end
							for _, part in pairs(character:GetChildren()) do
								if part:IsA("BasePart") then
									part.Anchored = false
								end
							end
						end)
					end
				end
			end)
		else
			if autoStruggleConn then
				autoStruggleConn:Disconnect()
				autoStruggleConn = nil
			end
			local char = localPlayer.Character
			if char then
				for _, part in pairs(char:GetChildren()) do
					if part:IsA("BasePart") then
						part.Anchored = false
					end
				end
			end
		end
	end
})
local antiBlob1T = false
local function antiBlob1F()
	antiBlob1T = true
	workspace.DescendantAdded:Connect(function(toy)
		if toy.Name == "CreatureBlobman" and antiBlob1T then
			toy.LeftDetector:Destroy()
			toy.RightDetector:Destroy()
		end
	end)
end
DefenseGroup:AddToggle("AntiBlobmanToggle", {
	Text = "Anti Blobman",
	Default = false,
	Callback = function(on)
		if on then
			antiBlob1F()
		else
			antiBlob1T = false
		end
	end
})
local antiExplodeT = false
local function antiExplodeF()
	antiExplodeT = true
	local char = Player.Character
	if not char then
		return
	end
	local hrp = char:WaitForChild("HumanoidRootPart")
	workspace.ChildAdded:Connect(function(model)
		if model.Name == "Part" and antiExplodeT then
			local mag = (model.Position - hrp.Position).Magnitude
			if mag <= 20 then
				hrp.Anchored = true
				wait(0.01)
				while char["Right Arm"].RagdollLimbPart.CanCollide do
					wait(0.001)
				end
				hrp.Anchored = false
			end
		end
	end)
end
DefenseGroup:AddToggle("AntiExplosionToggle", {
	Text = "Anti Explosion",
	Default = false,
	Callback = function(on)
		if on then
			antiExplodeF()
		else
			antiExplodeT = false
		end
	end
})
local hookBurnConn
local function hookBurn(char)
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	char.PrimaryPart = hrp
	if hookBurnConn then
		hookBurnConn:Disconnect()
	end
	hookBurnConn = hum.FireDebounce.Changed:Connect(function(isBurning)
		if isBurning then
			local me = char
			local oldCF = hrp.CFrame
			local plots = workspace:FindFirstChild("Plots")
			if plots and plots:FindFirstChild("Plot2") then
				local plot2 = plots.Plot2
				local barrier = plot2:FindFirstChild("Barrier")
				local pb = barrier and barrier:FindFirstChild("PlotBarrier")
				if pb and pb:IsA("BasePart") then
					local safeCF = pb.CFrame * CFrame.new(0, 6, 0)
					me:SetPrimaryPartCFrame(safeCF)
					task.wait(0.3)
					local firePart = me:FindFirstChild("FirePlayerPart", true)
					if firePart then
						for _, obj in ipairs(firePart:GetChildren()) do
							if obj:IsA("Sound") then
								obj:Stop()
							end
							if obj:IsA("Light") or obj:IsA("ParticleEmitter") then
								obj.Enabled = false
							end
						end
						if firePart:FindFirstChild("CanBurn") then
							firePart.CanBurn.Value = false
						end
						if hum:FindFirstChild("FireDebounce") then
							hum.FireDebounce.Value = false
						end
					end
					task.wait(0.6)
					if me and me.PrimaryPart then
						me:SetPrimaryPartCFrame(oldCF)
					end
				end
			end
		end
	end)
end
DefenseGroup:AddToggle("AntiBurnToggle", {
	Text = "Anti Burn",
	Default = false,
	Callback = function(on)
		if on then
			hookBurn(Player.Character)
		elseif hookBurnConn then
			hookBurnConn:Disconnect()
		end
	end
})
local antiVoidConn
local VOID_THRESHOLD = -50
local SAFE_HEIGHT = 100
DefenseGroup:AddToggle("AntiVoidToggle", {
	Text = "Anti Void",
	Default = false,
	Callback = function(on)
		if on then
			if antiVoidConn then
				antiVoidConn:Disconnect()
			end
			antiVoidConn = R.Heartbeat:Connect(function()
				local char = Player.Character
				if char and char.PrimaryPart then
					local pos = char.PrimaryPart.Position
					if pos.Y < VOID_THRESHOLD then
						local safePos = Vector3.new(pos.X, pos.Y + SAFE_HEIGHT, pos.Z)
						char:SetPrimaryPartCFrame(CFrame.new(safePos))
						char.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
					end
				end
			end)
		else
			if antiVoidConn then
				antiVoidConn:Disconnect()
				antiVoidConn = nil
			end
		end
	end
})
local antiStickyT = false
DefenseGroup:AddToggle("AntiStickyToggle", {
	Text = "Anti Sticky",
	Default = false,
	Callback = function(Value)
		antiStickyT = Value
		if Player.PlayerScripts:FindFirstChild("StickyPartsTouchDetection") then
			Player.PlayerScripts.StickyPartsTouchDetection.Disabled = Value
		end
	end,
})
local createGrabLineCopy, extendGrabLineCopy
local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
if grabFolder then
	local originalCreate = grabFolder:FindFirstChild("CreateGrabLine")
	local originalExtend = grabFolder:FindFirstChild("ExtendGrabLine")
	if originalCreate then
		createGrabLineCopy = originalCreate:Clone()
	end
	if originalExtend then
		extendGrabLineCopy = originalExtend:Clone()
	end
end
DefenseGroup:AddToggle("AntiLagToggle", {
	Text = "Anti Lag",
	Default = false,
	Callback = function(Value)
		if Value then
			local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
			if grabFolder then
				local create = grabFolder:FindFirstChild("CreateGrabLine")
				local extend = grabFolder:FindFirstChild("ExtendGrabLine")
				if create and create:IsA("RemoteEvent") then
					create:Destroy()
				end
				if extend and extend:IsA("RemoteEvent") then
					extend:Destroy()
				end
			end
			for _, v in ipairs(workspace:GetDescendants()) do
				if v:IsA("Beam") or v.Name:lower():find("line") then
					v:Destroy()
				end
			end
		else
			local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
			if grabFolder then
				if createGrabLineCopy and not grabFolder:FindFirstChild("CreateGrabLine") then
					local restoredCreate = createGrabLineCopy:Clone()
					restoredCreate.Parent = grabFolder
				end
				if extendGrabLineCopy and not grabFolder:FindFirstChild("ExtendGrabLine") then
					local restoredExtend = extendGrabLineCopy:Clone()
					restoredExtend.Parent = grabFolder
				end
			end
		end
	end,
})
DefenseExtra:AddToggle("PaintDeleteToggle", {
	Text = "Anti Paint",
	Default = false,
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
	end
})
local autoGucciActive = false
DefenseExtra:AddToggle("AutoGucciToggle", {
	Text = "Anti Gucci (Blobman)",
	Default = false,
	Callback = function(Value)
		autoGucciActive = Value
		if Value then
			startAntiGucci()
			notify("system", "auto gucci active (monitoring)", 3)
			task.spawn(function()
				while autoGucciActive do
					local toysFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
					local blobExists = toysFolder and toysFolder:FindFirstChild("CreatureBlobman")
					if not blobExists then
						stopAntiGucci()
						spawnBlobman()
						notify("System", "blobman lost", 3)
						local retries = 0
						repeat
							task.wait(0.2)
							retries = retries + 1
							toysFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
						until (toysFolder and toysFolder:FindFirstChild("CreatureBlobman")) or retries > 25 or not autoGucciActive
						if autoGucciActive and toysFolder and toysFolder:FindFirstChild("CreatureBlobman") then
							startAntiGucci()
							notify("System", "blobman restored.", 3)
						end
					end
					task.wait(0.5)
				end
			end)
		else
			autoGucciActive = false
			stopAntiGucci()
			notify("System", "auto gucci disabled.", 3)
		end
	end
})
local autoGucciActiveTrain = false
DefenseExtra:AddToggle("AutoGucciToggle", {
	Text = "Anti Gucci (Train)",
	Default = false,
	Callback = function(Value)
		autoGucciActiveTrain = Value
		if Value then
			startAntiGucciTrain()
			notify("system", "Gucci active (monitoring)", 3)
			task.spawn(function()
				while autoGucciActiveTrain do
					local trainFolder = workspace.Map.AlwaysHereTweenedObjects
					local trainExists = trainFolder and trainFolder:FindFirstChild("Train")
					if not trainExists then
						stopAntiGucciTrain()
						notify("System", "Train lost", 3)
						local retries = 0
						repeat
							task.wait(0.2)
							retries = retries + 1
							trainFolder = workspace.Map.AlwaysHereTweenedObjects
						until (trainFolder and trainFolder:FindFirstChild("Train")) or retries > 25 or not autoGucciActiveTrain
						if autoGucciActiveTrain and trainFolder and trainFolder:FindFirstChild("Train") then
							startAntiGucciTrain()
							notify("System", "Train restored.", 3)
						end
					end
					task.wait(0.5)
				end
			end)
		else
			autoGucciActiveTrain = false
			stopAntiGucciTrain()
			notify("System", "Gucci disabled.", 3)
		end
	end
})
local ToyList = {
	["Coconut"]="FoodCoconut",["Banana"]="FoodBanana",["Fries"]="FoodFrenchFries",
	["MeatStick"]="FoodMeatStick",["Poop"]="PoopPile",["Donut"]="FoodDonut",
	["Cake"]="FoodCakePink",["Burger"]="FoodHamburger",["Pizza"]="FoodPizzaCheese",
	["Hotdog"]="FoodHotdog",["Mushroom"]="FoodMushroomPoison",["Banjo"]="InstrumentGuitarBanjo",
	["Violin"]="InstrumentGuitarViolin",["Ukulele"]="InstrumentGuitarUkulele",
	["Sax"]="InstrumentWoodwindSaxophone",["Vuvuzela"]="InstrumentBrassVuvuzela",
	["Bongos"]="InstrumentDrumBongos",["Mic"]="InstrumentVoiceMicrophone",
	["Pepperoni"]="FoodPizzaPepperoni",["Piano"]="InstrumentPianoMelodica",
	["Bread"]="FoodBread",["Egg"]="FoodDippyEgg",["Mayo"]="FoodMayonnaise",
	["WhiteMug"]="CupMugWhite",["Ocarina"]="InstrumentWoodwindOcarina",
	["SparklePoop"]="PoopPileSparkle",["BrownMug"]="CupMugBrown",
	["Trumpet"]="InstrumentBrassTrumpet",["Snare"]="InstrumentDrumSnare",
}
local DropdownValues = {}
for shortName, _ in pairs(ToyList) do
	table.insert(DropdownValues, shortName)
end
table.sort(DropdownValues)
local SelectedToy = ToyList[DropdownValues[1]]
DefenseExtra:AddDropdown("AntiInputLagToy", {
	Text = "Input Lag Item",
	Values = DropdownValues,
	Default = 1,
	Callback = function(Value)
		SelectedToy = ToyList[Value]
	end
})
DefenseExtra:AddToggle("AntiInputLag", {
	Text = "Anti Input Lag",
	Default = false,
	Callback = function(Value)
		_G.AntiInputLag = Value
		if Value then
			task.spawn(function()
				local Players = game:GetService("Players")
				local ReplicatedStorage = game:GetService("ReplicatedStorage")
				local Workspace = game:GetService("Workspace")
				local RunService = game:GetService("RunService")
				local plr = Players.LocalPlayer
				local char = plr.Character or plr.CharacterAdded:Wait()
				local hrp = char:WaitForChild("HumanoidRootPart")
				local SpawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
				while _G.AntiInputLag do
					local toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
					if not toysFolder then
						task.wait(0.1)
						continue
					end
					local toy = toysFolder:FindFirstChild(SelectedToy)
					if not toy then
						pcall(function()
							SpawnRemote:InvokeServer(SelectedToy, hrp.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
						end)
						local t0 = tick()
						repeat
							RunService.Heartbeat:Wait()
							toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
							toy = toysFolder and toysFolder:FindFirstChild(SelectedToy)
						until toy or tick() - t0 > 1 or not _G.AntiInputLag
					end
					if toy and toy.Parent then
						local holdPart = toy:FindFirstChild("HoldPart")
						if holdPart then
							local holdingPlayer = holdPart:FindFirstChild("HoldingPlayer")
							holdingPlayer = holdingPlayer and holdingPlayer.Value
							if holdingPlayer and holdingPlayer ~= plr then
								pcall(function()
									holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 2000, 0), Vector3.zero)
								end)
								toy:Destroy()
							else
								pcall(function()
									holdPart.HoldItemRemoteFunction:InvokeServer(toy, char)
								end)
								task.wait(0.05)
								pcall(function()
									holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 2000, 0), Vector3.zero)
								end)
								task.wait(0.01)
							end
						end
					end
					RunService.Heartbeat:Wait()
				end
			end)
		end
	end
})
local tpActive = false
DefenseExtra:AddToggle("ShurikenAntiKick", {
	Text = "Anti Kick",
	Default = false,
	Callback = function(Value)
		_G.ShurikenAntiKick = Value
		local function ClearKunai()
			local plr = game.Players.LocalPlayer
			local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
			local destroyrem = game.ReplicatedStorage:FindFirstChild("MenuToys") and game.ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy")
			if inv and destroyrem then
				for _, v in pairs(inv:GetChildren()) do
					if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
						pcall(function()
							destroyrem:FireServer(v)
						end)
					end
				end
			end
		end
		if Value then
			task.spawn(function()
				local plr = game.Players.LocalPlayer
				local ReplicatedStorage = game:GetService("ReplicatedStorage")
				local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
				local stickyEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
				local spawnRemote = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
				local destroyrem = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
				local canSpawn = plr:WaitForChild("CanSpawnToy")
				local function getHRP()
					if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
						return plr.Character.HumanoidRootPart
					else
						local character = plr.CharacterAdded:Wait()
						return character:WaitForChild("HumanoidRootPart")
					end
				end
				local function CheckForHome()
					if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then
						return false
					end
					for _, v in pairs(workspace.Plots:GetChildren()) do
						local sign = v:FindFirstChild("PlotSign")
						local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
						if owners then
							for _, b in pairs(owners:GetChildren()) do
								if b.Value == plr.Name then
									local folder = workspace.PlotItems:FindFirstChild(v.Name)
									if folder then
										return true, folder
									end
								end
							end
						end
					end
					return false
				end
				local function StickKunai(kunai)
					if not kunai or not kunai:FindFirstChild("StickyPart") then return end
					local currentHRP = getHRP()
					if not currentHRP then return end
					if kunai:FindFirstChild("SoundPart") then
						if not kunai.SoundPart:FindFirstChild("PartOwner") or kunai.SoundPart.PartOwner.Value ~= plr.Name then
							setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
						end
					end
					local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart", 5)
					if firePart then
						stickyEvent:FireServer(kunai.StickyPart, firePart, CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90)))
					end
					for _, obj in pairs(kunai:GetChildren()) do
						if obj.Name == "Pyramid" then
							obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
							if not obj:FindFirstChild("Highlight") then
								local high = Instance.new("Highlight", obj)
								high.FillColor = Color3.fromRGB(0, 0, 0)
							end
						elseif obj.Name == "Main" then
							obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
							if not obj:FindFirstChild("Highlight") then
								local high = Instance.new("Highlight", obj)
								high.FillColor = Color3.fromRGB(255, 255, 255)
							end
						elseif obj:IsA("BasePart") then
							obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 1
						end
					end
				end
				local function SpawnToy(name)
					local t = tick()
					while not canSpawn.Value do
						if not _G.ShurikenAntiKick or tick() - t > 5 then return nil end
						task.wait(0.1)
					end
					local currentHRP = getHRP()
					if currentHRP then
						task.spawn(function()
							pcall(function()
								spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0))
							end)
						end)
					end
					local boolik, house = CheckForHome()
					local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
					if boolik and house then
						return house:WaitForChild(name, 2)
					elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and inv then
						return inv:WaitForChild(name, 2)
					end
					return nil
				end
				while _G.ShurikenAntiKick do
					task.wait(0.005)
					if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then continue end
					local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
					local kunai = inv and inv:FindFirstChild("NinjaShuriken")
					if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then
						local boolik, house = CheckForHome()
						if boolik and house and workspace.Plots:FindFirstChild(house.Name) then
							local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
							if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then
								kunai = SpawnToy("NinjaShuriken")
								if kunai == nil then continue end
								kunai.Name = "AntiKick"
								StickKunai(kunai)
							end
						end
					end
					if not kunai then
						if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then continue end
						kunai = SpawnToy("NinjaShuriken")
						if kunai == nil then continue end
						kunai.Name = "AntiKick"
						if not kunai then continue end
					end
					repeat
						if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then
							StickKunai(kunai)
							kunai.Name = "AntiKick"
						end
						task.wait(0.3)
					until not kunai or not _G.ShurikenAntiKick
						or not kunai:FindFirstChild("StickyPart")
						or kunai.StickyPart.CanTouch == false
						or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart")
						or not kunai:FindFirstChild("StickyPart")
						or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
					if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then
						ClearKunai()
					end
					pcall(function()
						repeat
							task.wait(0.05)
						until not _G.ShurikenAntiKick or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
						if not kunai or not kunai:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then
							ClearKunai()
						end
					end)
				end
			end)
		else
			_G.ShurikenAntiKick = false
			ClearKunai()
		end
	end
})
DefenseExtra:AddToggle("LoopTP", {
	Text = "Loop TP",
	Default = false,
	Callback = function(Value)
		tpActive = Value
		local char = Player.Character or Player.CharacterAdded:Wait()
		local hrp = char:WaitForChild("HumanoidRootPart")
		local hum = char:FindFirstChildOfClass("Humanoid")
		if Value then
			if hum then hum.PlatformStand = true end
			task.spawn(function()
				while tpActive and hrp do
					local x = math.random(-500, 500)
					local y = math.random(30, 480)
					local z = math.random(-500, 500)
					hrp.CFrame = CFrame.new(x, y, z)
					task.wait(0.03)
				end
			end)
		else
			if hum then hum.PlatformStand = false end
		end
	end,
})
local TargetGroup = Tabs.Target:AddLeftGroupbox("Target Interaction")
local BlobGroup = Tabs.Target:AddRightGroupbox("Blobman Kick")
local WhitelistGroup = Tabs.Target:AddRightGroupbox("whitelist")
local selectedKickPlayer = nil
local kickLoopEnabled = false
local kickLoopConnection = nil
local savedKickPos = nil
local currentKickTargetChar = nil
local function getPlayerList()
	local list = {}
	for _, plr in ipairs(PS:GetPlayers()) do
		if plr ~= Player then
			table.insert(list, plr.DisplayName .. " (" .. plr.Name .. ")")
		end
	end
	return list
end
local function getPlayerFromSelection(selection)
	if not selection then return nil end
	local username = selection:match("%((.-)%)")
	if username then return PS:FindFirstChild(username) end
	return nil
end
TargetGroup:AddDropdown("KickPlayerDropdown", {
	Values = getPlayerList(),
	Default = 1,
	Multi = false,
	Text = "select player for kick",
	Callback = function(Value)
		selectedKickPlayer = getPlayerFromSelection(Value)
	end,
})
TargetGroup:AddButton({
	Text = "refresh player list",
	Func = function()
		Options.KickPlayerDropdown:SetValues(getPlayerList())
		Options.KickPlayerDropdown:SetValue(nil)
		selectedKickPlayer = nil
	end
})
TargetGroup:AddToggle("LoopKickGrabToggle", {
	Text = "Kick (spam grab)",
	Default = false,
	Callback = function(on)
		kickLoopEnabled = on
		if not on then return end
		task.spawn(function()
			local RS = game:GetService("ReplicatedStorage")
			local RunService = game:GetService("RunService")
			local GE = RS:WaitForChild("GrabEvents")
			local myChar = Player.Character
			local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
			if not myRoot then Toggles.LoopKickGrabToggle:SetValue(false) return end
			local savedPos = myRoot.CFrame
			local dragging = false
			local grabStartTime = 0
			while kickLoopEnabled do
				local target = selectedKickPlayer
				if not target or not target.Parent then break end
				myChar = Player.Character
				myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
				if not myRoot then break end
				local tChar = target.Character
				local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
				local tHum = tChar and tChar:FindFirstChild("Humanoid")
				if tRoot and tHum and tHum.Health > 0 then
					tRoot.AssemblyLinearVelocity = Vector3.zero
					tRoot.AssemblyAngularVelocity = Vector3.zero
					tRoot.Velocity = Vector3.zero
					if not dragging then
						myRoot.CFrame = tRoot.CFrame
						myRoot.Velocity = Vector3.zero
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = true
							GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
						if grabStartTime == 0 then grabStartTime = tick() end
						if tick() - grabStartTime > 0.35 then
							dragging = true
							grabStartTime = 0
						end
					else
						myRoot.CFrame = savedPos
						myRoot.Velocity = Vector3.zero
						local lockPos = savedPos * CFrame.new(0, 17, 0)
						tRoot.CFrame = lockPos
						tRoot.Velocity = Vector3.zero
						tRoot.RotVelocity = Vector3.zero
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = false
							GE.SetNetworkOwner:FireServer(tRoot, lockPos)
							GE.DestroyGrabLine:FireServer(tRoot)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
					end
				else
					dragging = false
					grabStartTime = 0
				end
				RunService.Heartbeat:Wait()
			end
			if myRoot then
				myRoot.CFrame = savedPos
				myRoot.Velocity = Vector3.zero
			end
			kickLoopEnabled = false
			Toggles.LoopKickGrabToggle:SetValue(false)
		end)
	end
})
TargetGroup:AddToggle("RagdollSnowballKick", {
	Text = "Ragdoll Snowball",
	Default = false,
	Callback = function(on)
		local Players = game:GetService("Players")
		local RS = game:GetService("ReplicatedStorage")
		local Workspace = game:GetService("Workspace")
		local RunService = game:GetService("RunService")
		local Player = Players.LocalPlayer
		local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
		local ragdollEnabled = on
		task.spawn(function()
			while ragdollEnabled do
				local target = selectedKickPlayer
				if not target or not target.Parent then RunService.Heartbeat:Wait() continue end
				local tChar = target.Character
				local torso = tChar and (tChar:FindFirstChild("UpperTorso") or tChar:FindFirstChild("Torso"))
				if not torso then RunService.Heartbeat:Wait() continue end
				pcall(function()
					local offset = Vector3.new(math.random(-30,30)/100, math.random(-30,30)/100, math.random(-30,30)/100)
					local spawnCFrame = torso.CFrame * CFrame.new(offset)
					SpawnRemote:InvokeServer("BallSnowball", spawnCFrame, Vector3.zero)
				end)
				local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
				if folder then
					for _, snowball in pairs(folder:GetChildren()) do
						if snowball.Name == "BallSnowball" and snowball.Parent then
							local part = snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart")
							if part then
								local offset = Vector3.new(math.random(-30,30)/100, math.random(-30,30)/100, math.random(-30,30)/100)
								part.CFrame = torso.CFrame * CFrame.new(offset)
								part.AssemblyLinearVelocity = Vector3.zero
								part.AssemblyAngularVelocity = Vector3.zero
							end
						end
					end
				end
				RunService.Heartbeat:Wait()
			end
		end)
	end
})
TargetGroup:AddToggle("LoopKickGrabToggle", {
	Text = "Kick (ragdoll grab)",
	Default = false,
	Callback = function(on)
		kickLoopEnabled = on
		if not on then return end
		task.spawn(function()
			local Players = game:GetService("Players")
			local RS = game:GetService("ReplicatedStorage")
			local RunService = game:GetService("RunService")
			local Player = Players.LocalPlayer
			local GE = RS:WaitForChild("GrabEvents")
			local Character = Player.Character or Player.CharacterAdded:Wait()
			local myRoot = Character:FindFirstChild("HumanoidRootPart")
			if not myRoot then Toggles.LoopKickGrabToggle:SetValue(false) return end
			local savedPos = myRoot.CFrame
			local dragging = false
			local grabStart = 0
			while kickLoopEnabled do
				local target = selectedKickPlayer
				if not target or not target.Parent then break end
				local tChar = target.Character
				local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
				local tHum = tChar and tChar:FindFirstChild("Humanoid")
				if tRoot and tHum and tHum.Health > 0 then
					tRoot.AssemblyLinearVelocity = Vector3.zero
					tRoot.AssemblyAngularVelocity = Vector3.zero
					tRoot.Velocity = Vector3.zero
					if not dragging then
						myRoot.CFrame = tRoot.CFrame
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = true
							GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
						if grabStart == 0 then grabStart = tick() end
						if tick() - grabStart > 0.15 then
							dragging = true
							grabStart = 0
							myRoot.CFrame = savedPos
						end
					else
						local lockCFrame = CFrame.new(savedPos.Position + Vector3.new(0, 7, 0)) * CFrame.Angles(math.rad(math.random(-180,180)), math.rad(math.random(-180,180)), math.rad(math.random(-180,180)))
						tRoot.CFrame = tRoot.CFrame:Lerp(lockCFrame, 0.2)
						tRoot.Velocity = Vector3.zero
						tRoot.RotVelocity = Vector3.zero
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = false
							GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
							GE.DestroyGrabLine:FireServer(tRoot)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
					end
				else
					dragging = false
					grabStart = 0
				end
				RunService.Heartbeat:Wait()
			end
			if myRoot then myRoot.CFrame = savedPos; myRoot.Velocity = Vector3.zero end
			kickLoopEnabled = false
			Toggles.LoopKickGrabToggle:SetValue(false)
		end)
	end
})
local function IsRagdolled(hum)
	local r = hum and hum:FindFirstChild("Ragdolled")
	return r ~= nil and r.Value == true
end
TargetGroup:AddToggle("LoopKickGrabToggle", {
	Text = "Grab Troll (spam grab)",
	Default = false,
	Callback = function(on)
		kickLoopEnabled = on
		if not on then return end
		task.spawn(function()
			local RS = game:GetService("ReplicatedStorage")
			local RunService = game:GetService("RunService")
			local GE = RS:WaitForChild("GrabEvents")
			local myChar = Player.Character
			local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
			if not myRoot then Toggles.LoopKickGrabToggle:SetValue(false) return end
			local savedPos = myRoot.CFrame
			local dragging = false
			local grabStartTime = 0
			while kickLoopEnabled do
				local target = selectedKickPlayer
				if not target or not target.Parent then break end
				myChar = Player.Character
				myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
				if not myRoot then break end
				local tChar = target.Character
				local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
				local tHum = tChar and tChar:FindFirstChild("Humanoid")
				if tRoot and tHum and tHum.Health > 0 then
					tRoot.AssemblyLinearVelocity = Vector3.zero
					tRoot.AssemblyAngularVelocity = Vector3.zero
					tRoot.Velocity = Vector3.zero
					if not dragging then
						myRoot.CFrame = tRoot.CFrame
						myRoot.Velocity = Vector3.zero
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = true
							GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
						if grabStartTime == 0 then grabStartTime = tick()
						elseif tick() - grabStartTime > 0.35 then
							dragging = true
							grabStartTime = 0
						end
					else
						if not IsRagdolled(tHum) then
							dragging = false
							grabStartTime = 0
							pcall(function() GE.DestroyGrabLine:FireServer(tRoot) end)
							RunService.Heartbeat:Wait()
							continue
						end
						myRoot.CFrame = savedPos
						myRoot.Velocity = Vector3.zero
						local lockPos = savedPos * CFrame.new(0, 17, 0)
						tRoot.CFrame = lockPos
						tRoot.Velocity = Vector3.zero
						tRoot.RotVelocity = Vector3.zero
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = falseGE.SetNetworkOwner:FireServer(tRoot, lockPos)
							GE.DestroyGrabLine:FireServer(tRoot)
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
					end
				else
					dragging = false
					grabStartTime = 0
				end
				RunService.Heartbeat:Wait()
			end
			if myRoot then myRoot.CFrame = savedPos; myRoot.Velocity = Vector3.zero end
			kickLoopEnabled = false
			Toggles.LoopKickGrabToggle:SetValue(false)
		end)
	end
})
TargetGroup:AddToggle("LoopKickToggle", {
	Text = "Loop Kick (grab + blob)",
	Default = false,
	Callback = function(on)
		kickLoopEnabled = on
		local target = selectedKickPlayer
		if on and not target then
			if Toggles.LoopKickToggle then Toggles.LoopKickToggle:SetValue(false) end
			return
		end
		local char = Player.Character
		local hum = char and char:FindFirstChild("Humanoid")
		local seat = hum and hum.SeatPart
		if on and (not seat or seat.Parent.Name ~= "CreatureBlobman") then
			if Toggles.LoopKickToggle then Toggles.LoopKickToggle:SetValue(false) end
			return
		end
		if not on then kickLoopEnabled = false return end
		task.spawn(function()
			local RS = game:GetService("ReplicatedStorage")
			local GE = RS:WaitForChild("GrabEvents")
			local RunService = game:GetService("RunService")
			local blob = seat.Parent
			local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
			local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
			local CG = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
			local CD = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
			local R_Det = blob:FindFirstChild("RightDetector")
			local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld"))
			local SavedPos = blobRoot.CFrame
			local tChar = target.Character
			local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
			if tRoot and blobRoot then
				local bringStart = tick()
				while tick() - bringStart < 0.35 do
					if not kickLoopEnabled then break end
					blobRoot.CFrame = tRoot.CFrame
					blobRoot.Velocity = Vector3.zero
					pcall(function()
						if CG and R_Det then CG:FireServer(R_Det, tRoot, R_Weld) end
						GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						GE.SetNetworkOwner:FireServer(tRoot, blobRoot.CFrame)
					end)
					RunService.Heartbeat:Wait()
				end
				blobRoot.CFrame = SavedPos
				blobRoot.Velocity = Vector3.zero
				task.wait(0.05)
			end
			local packetTimer = 0
			while kickLoopEnabled do
				if not target or not target.Parent or not target.Character then break end
				local tChar = target.Character
				local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
				local tHum = tChar and tChar:FindFirstChild("Humanoid")
				if tRoot and tHum and tHum.Health > 0 and blobRoot then
					blobRoot.CFrame = SavedPos
					blobRoot.Velocity = Vector3.zero
					local lockPos = SavedPos * CFrame.new(0, 23, 0)
					tRoot.CFrame = lockPos
					tRoot.Velocity = Vector3.zero
					tRoot.RotVelocity = Vector3.zero
					if tick() - packetTimer > 0.05 then
						packetTimer = tick()
						pcall(function()
							tHum.PlatformStand = true
							tHum.Sit = true
							GE.SetNetworkOwner:FireServer(tRoot, lockPos)
							if R_Det then
								local weld = R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld")
								if weld then CD:FireServer(weld) end
							end
							GE.DestroyGrabLine:FireServer(tRoot)
							if R_Det then CG:FireServer(R_Det, tRoot, R_Weld) end
							GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
						end)
					end
				else
					blobRoot.CFrame = SavedPos
					blobRoot.Velocity = Vector3.zero
				end
				if not kickLoopEnabled then break end
				RunService.Heartbeat:Wait()
			end
			kickLoopEnabled = false
			if Toggles.LoopKickToggle then Toggles.LoopKickToggle:SetValue(false) end
			if blobRoot then blobRoot.CFrame = SavedPos; blobRoot.Velocity = Vector3.zero end
		end)
	end
})
local loopKickDualActive = false
TargetGroup:AddToggle("DualHandLoopKick", {
	Text = "Loop Kick",
	Default = false,
	Callback = function(on)
		loopKickDualActive = on
		if on then
			if not selectedKickPlayer then
				notify("Error", "Select target first", 3)
				Toggles.DualHandLoopKick:SetValue(false)
				return
			end
			task.spawn(function()
				local lastTargetCharDual = nil
				local bp = nil
				while loopKickDualActive do
					local target = selectedKickPlayer
					local char = Player.Character
					local hum = char and char:FindFirstChild("Humanoid")
					local seat = hum and hum.SeatPart
					if not seat or not target or not target.Parent then task.wait(0.5) continue end
					local seatParent = seat.Parent
					local grab = seatParent:FindFirstChild("BlobmanSeatAndOwnerScript") and seatParent.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
					local drop = seatParent:FindFirstChild("BlobmanSeatAndOwnerScript") and seatParent.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
					if not grab or not drop then task.wait(0.5) continue end
					local leftDet = seatParent:FindFirstChild("LeftDetector")
					local rightDet = seatParent:FindFirstChild("RightDetector")
					local leftWeld = leftDet and leftDet:FindFirstChild("LeftWeld")
					local rightWeld = rightDet and rightDet:FindFirstChild("RightWeld")
					local hrp = char:FindFirstChild("HumanoidRootPart")
					local targetChar = target.Character
					local targetHRP = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
					local targetHum = targetChar and targetChar:FindFirstChild("Humanoid")
					if targetHRP and targetHum and targetHum.Health > 0 then
						if targetChar ~= lastTargetCharDual then
							lastTargetCharDual = targetChar
							if bp then bp:Destroy(); bp = nil end
							if hrp then hrp.CFrame = targetHRP.CFrame * CFrame.new(0, 25, 0) end
							task.wait(0.2)
							grab:FireServer(leftDet, targetHRP, leftWeld)
							task.wait(0.3)
							drop:FireServer(leftWeld, targetHRP)
							task.wait(0.1)
							bp = Instance.new("BodyPosition")
							bp.Position = Vector3.new(0, 999999, 0)
							bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
							bp.Parent = targetHRP
							grab:FireServer(leftDet, targetHRP, leftWeld)
							task.wait(0.2)
							drop:FireServer(leftWeld, targetHRP)
						end
						grab:FireServer(leftDet, targetHRP, leftWeld)
						task.wait()
						drop:FireServer(leftWeld, targetHRP)
						task.wait()
						grab:FireServer(rightDet, targetHRP, rightWeld)
						task.wait()
						drop:FireServer(rightWeld, targetHRP)
						task.wait()
						grab:FireServer(leftDet, targetHRP, leftWeld)
						grab:FireServer(rightDet, targetHRP, rightWeld)
						task.wait()
						drop:FireServer(leftWeld, targetHRP)
						drop:FireServer(rightWeld, targetHRP)
						task.wait()
					else
						task.wait(0.1)
					end
				end
				if bp then bp:Destroy() end
			end)
		else
			loopKickDualActive = false
		end
	end
})
local playerFlingActive = false
local flingBAV = nil
local originalPos = nil
TargetGroup:AddToggle("PlayerFlingBtn", {
	Text = "Fling",
	Default = false,
	Callback = function(on)
		playerFlingActive = on
		if on then
			if not selectedKickPlayer then
				notify("System", "Select target first!", 3)
				Toggles.PlayerFlingBtn:SetValue(false)
				return
			end
			local RunService = game:GetService("RunService")
			local MyChar = Player.Character
			local MyRoot = MyChar and MyChar:FindFirstChild("HumanoidRootPart")
			if MyRoot then originalPos = MyRoot.CFrame end
			notify("Maestro", "Fling Mode Activated.", 3)
			task.spawn(function()
				while playerFlingActive do
					local target = selectedKickPlayer
					local char = Player.Character
					local hrp = char and char:FindFirstChild("HumanoidRootPart")
					local hum = char and char:FindFirstChild("Humanoid")
					if not hrp or not hum then task.wait(0.5) continue end
					if target and target.Parent then
						local tChar = target.Character
						local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
						local tHum = tChar and tChar:FindFirstChild("Humanoid")
						if tRoot and tHum and tHum.Health > 0 then
							if not flingBAV or flingBAV.Parent ~= hrp then
								if flingBAV then flingBAV:Destroy() end
								flingBAV = Instance.new("BodyAngularVelocity")
								flingBAV.Name = "MaestroSpin"
								flingBAV.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
								flingBAV.AngularVelocity = Vector3.new(0, 10000, 0)
								flingBAV.P = 10000
								flingBAV.Parent = hrp
							end
							for _, part in pairs(char:GetDescendants()) do
								if part:IsA("BasePart") then part.CanCollide = false end
							end
							local loop = RunService.Heartbeat:Connect(function()
								if not playerFlingActive or not tRoot or not tRoot.Parent then return end
								hrp.CFrame = tRoot.CFrame
								hrp.Velocity = Vector3.zero
							end)
							local startTime = tick()
							while tick() - startTime < 1.5 do
								if not playerFlingActive or not tRoot.Parent then break end
								task.wait(0.1)
							end
							if loop then loop:Disconnect() end
						else
							task.wait(0.2)
						end
					else
						playerFlingActive = false
						Toggles.PlayerFlingBtn:SetValue(false)
					end
					task.wait(0.1)
				end
				if flingBAV then flingBAV:Destroy(); flingBAV = nil end
				local char = Player.Character
				if char then
					for _, part in pairs(char:GetDescendants()) do
						if part:IsA("BasePart") then part.CanCollide = true end
					end
					local hrp = char:FindFirstChild("HumanoidRootPart")
					if hrp then
						hrp.RotVelocity = Vector3.zero
						hrp.Velocity = Vector3.zero
						if originalPos then hrp.CFrame = originalPos end
					end
				end
			end)
		else
			playerFlingActive = false
			if flingBAV then flingBAV:Destroy(); flingBAV = nil end
			local char = Player.Character
			local hrp = char and char:FindFirstChild("HumanoidRootPart")
			if hrp then hrp.RotVelocity = Vector3.zero; hrp.Velocity = Vector3.zero end
		end
	end
})

-- ===============================
-- AUTO SIT BLOBMAN [T] — PC + Mobile
-- ===============================
local function doAutoSitBlob()
	if not _G.AutoSitBlobT then return end
	local plr = game.Players.LocalPlayer
	local char = plr.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	local hum = char and char:FindFirstChild("Humanoid")
	if not hrp or not hum then return end
	local folderName = plr.Name .. "SpawnedInToys"
	local folder = workspace:FindFirstChild(folderName)
	local blob = folder and folder:FindFirstChild("CreatureBlobman")
	if not blob then
		task.spawn(function()
			pcall(function()
				game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.zero)
			end)
		end)
		if not folder then folder = workspace:WaitForChild(folderName, 5) end
		if folder then blob = folder:WaitForChild("CreatureBlobman", 5) end
	end
	if blob then
		local seat = blob:WaitForChild("VehicleSeat", 5)
		if seat then
			local t = tick()
			repeat
				if not hum.SeatPart then
					hrp.CFrame = seat.CFrame + Vector3.new(0, 1, 0)
					hrp.Velocity = Vector3.zero
					seat:Sit(hum)
				end
				game:GetService("RunService").Heartbeat:Wait()
			until hum.SeatPart == seat or tick() - t > 1.5
		end
	end
end

game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
	if not processed and input.KeyCode == Enum.KeyCode.T then
		doAutoSitBlob()
	end
end)

-- ===============================
-- BLOB FLY TOGGLE [R] — PC + Mobile
-- ===============================
local function doBlobFlyToggle()
	if blobMasterSwitch then
		blobFlyActive = not blobFlyActive
		if not blobFlyActive then
			if bvInstance then bvInstance:Destroy(); bvInstance = nil end
			if bgInstance then bgInstance:Destroy(); bgInstance = nil end
		end
	end
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.R then
		doBlobFlyToggle()
	end
end)

local function GetBlobRoot()
	local char = Player.Character
	local hum = char and char:FindFirstChild("Humanoid")
	if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
		return hum.SeatPart.Parent:FindFirstChild("HumanoidRootPart") or hum.SeatPart.Parent.PrimaryPart
	end
	local folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if folder then
		local blob = folder:FindFirstChild("CreatureBlobman")
		if blob then return blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart end
	end
	return nil
end

-- ===============================
-- BLOB FLY HEARTBEAT — PC + Mobile keys
-- ===============================
game:GetService("RunService").Heartbeat:Connect(function()
	if not blobFlyActive or not blobMasterSwitch then
		if bvInstance then bvInstance:Destroy(); bvInstance = nil end
		if bgInstance then bgInstance:Destroy(); bgInstance = nil end
		return
	end
	local root = GetBlobRoot()
	if root then
		if not root:FindFirstChild("BlobFlyVelocity") then
			bvInstance = Instance.new("BodyVelocity")
			bvInstance.Name = "BlobFlyVelocity"
			bvInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
			bvInstance.P = 10000
			bvInstance.Parent = root
		else
			bvInstance = root.BlobFlyVelocity
		end
		if not root:FindFirstChild("BlobFlyGyro") then
			bgInstance = Instance.new("BodyGyro")
			bgInstance.Name = "BlobFlyGyro"
			bgInstance.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
			bgInstance.P = 20000
			bgInstance.D = 100
			bgInstance.Parent = root
		else
			bgInstance = root.BlobFlyGyro
		end
		local cam = workspace.CurrentCamera
		local moveDir = Vector3.zero
		-- PC + MOBILE key check
		if UserInputService:IsKeyDown(Enum.KeyCode.W) or _G.MobileKeys.W then
			moveDir = moveDir + cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) or _G.MobileKeys.S then
			moveDir = moveDir - cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) or _G.MobileKeys.A then
			moveDir = moveDir - cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) or _G.MobileKeys.D then
			moveDir = moveDir + cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) or _G.MobileKeys.Space then
			moveDir = moveDir + Vector3.new(0, 1, 0)
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) or _G.MobileKeys.Ctrl then
			moveDir = moveDir - Vector3.new(0, 1, 0)
		end
		if bvInstance then bvInstance.Velocity = moveDir * blobFlySpeed end
		if bgInstance then bgInstance.CFrame = cam.CFrame end
	else
		if bvInstance then bvInstance:Destroy(); bvInstance = nil end
		if bgInstance then bgInstance:Destroy(); bgInstance = nil end
	end
end)

local DestroyGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
local DestroyTargetGucciActive = false
TargetGroup:AddToggle("DestroyTargetGucci", {
	Text = "Destroy Gucci (sit)",
	Default = false,
	Callback = function(Value)
		DestroyTargetGucciActive = Value
		if Value then
			if not selectedKickPlayer then
				notify("Error", "Error", 3)
				Toggles.DestroyTargetGucci:SetValue(false)
				return
			end
			local char = Player.Character
			local root = char and char:FindFirstChild("HumanoidRootPart")
			if not root then return end
			local SafeSpot = root.CFrame
			local RunService = game:GetService("RunService")
			local folderName = selectedKickPlayer.Name .. "SpawnedInToys"
			notify("System", "spawn toy " .. folderName, 3)
			task.spawn(function()
				while DestroyTargetGucciActive do
					if not selectedKickPlayer or not selectedKickPlayer.Parent then
						notify("System", "Activated", 3)
						DestroyTargetGucciActive = false
						Toggles.DestroyTargetGucci:SetValue(false)
						break
					end
					local toysFolder = workspace:FindFirstChild(folderName)
					if not toysFolder then
						task.wait(1)
					else
						local foundBlob = false
						for _, obj in ipairs(toysFolder:GetChildren()) do
							if not DestroyTargetGucciActive then break end
							if obj.Name == "CreatureBlobman" then
								foundBlob = true
								local seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
								if seat then
									local myChar = Player.Character
									local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
									local myHum = myChar and myChar:FindFirstChild("Humanoid")
									if myRoot and myHum then
										if myHum.SeatPart ~= seat then
											notify("Target", "target", 1)
											local magnetConnection
											magnetConnection = RunService.Stepped:Connect(function()
												if myRoot and seat then
													myRoot.CFrame = seat.CFrame
													myRoot.Velocity = Vector3.zero
													if obj.PrimaryPart then
														obj.PrimaryPart.Velocity = Vector3.zero
														obj.PrimaryPart.RotVelocity = Vector3.zero
													end
												end
											end)
											local sitStart = tick()
											while tick() - sitStart < 1 do
												if not DestroyTargetGucciActive then break end
												if myHum.SeatPart == seat then break end
												seat:Sit(myHum)
												task.wait()
											end
											if magnetConnection then magnetConnection:Disconnect() end
											if myHum.SeatPart == seat then
												task.wait(0.3)
												myHum.Sit = false
												myHum.Jump = true
												task.wait(0.05)
												myRoot.CFrame = SafeSpot
												myRoot.Velocity = Vector3.zero
												notify("Success", "gucci has removed", 1)
												task.wait(0.5)
											else
												myRoot.CFrame = SafeSpot
											end
										end
									end
								end
							end
						end
					end
					task.wait(1)
				end
			end)
		else
			DestroyTargetGucciActive = false
			notify("System", "remove Gucci off", 2)
		end
	end
})
TargetGroup:AddButton({
	Text = "bring",
	Func = function()
		if not selectedKickPlayer then return end
		local char = Player.Character
		local hum = char and char:FindFirstChild("Humanoid")
		local seat = hum and hum.SeatPart
		if not seat or seat.Parent.Name ~= "CreatureBlobman" then return end
		local blob = seat.Parent
		local blobRoot = blob:FindFirstChild("HumanoidRootPart")
		local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
		if not blobRoot or not scriptObj then return end
		local CG = scriptObj:FindFirstChild("CreatureGrab")
		local CD = scriptObj:FindFirstChild("CreatureDrop")
		local R_Det = blob:FindFirstChild("RightDetector")
		local R_Weld = R_Det and R_Det:FindFirstChild("RightWeld")
		local tChar = selectedKickPlayer.Character
		local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
		if not tRoot then return end
		local home = blobRoot.CFrame
		blobRoot.CFrame = tRoot.CFrame
		blobRoot.Velocity = Vector3.new()
		blobRoot.RotVelocity = Vector3.new()
		task.wait(0.3)
		pcall(function() CG:FireServer(R_Det, tRoot, R_Weld) end)
		task.wait(0.5)
		blobRoot.CFrame = home
		blobRoot.Velocity = Vector3.new()
		blobRoot.RotVelocity = Vector3.new()
		task.wait(0.05)
		for i = 1, 12 do
			tRoot.CFrame = home * CFrame.new(0, 3, 0)
			tRoot.Velocity = Vector3.new()
			tRoot.RotVelocity = Vector3.new()
			task.wait(0.03)
		end
		for i = 1, 8 do
			local weld = R_Det:FindFirstChild("RightWeld")
			if weld then pcall(function() CD:FireServer(weld) end) end
			task.wait(0.03)
		end
	end
})
local AllowedItems = {
	FoodHamburger=true,FoodCoconut=true,FoodPizzaCheese=true,FoodPizzaPepperoni=true,
	FoodHotdog=true,FoodMushroomPoison=true,FoodBread=true,FoodDippyEgg=true,
	FoodMayonnaise=true,FoodFrenchFries=true,FoodMeatStick=true,FoodDonut=true,
	FoodCakePink=true,InstrumentGuitarBanjo=true,InstrumentGuitarViolin=true,
	InstrumentGuitarUkulele=true,InstrumentWoodwindSaxophone=true,
	InstrumentWoodwindOcarina=true,InstrumentBrassVuvuzelaQwizik=true,
	InstrumentBrassTrumpet=true,InstrumentDrumBongos=true,InstrumentDrumSnare=true,
	InstrumentPianoMelodica=true,InstrumentVoiceMicrophone=true,
	CupMugWhite=true,CupMugBrown=true,PoopPile=true,PoopPileSparkle=true,
}
local antiAntiLagEnabled = false
TargetGroup:AddToggle("Remove AntiInputLag", {
	Text = "Remove Anti Input Lag",
	Default = false,
	Callback = function(on)
		antiAntiLagEnabled = on
		if not on then antiAntiLagEnabled = false return end
		task.spawn(function()
			local plr = game.Players.LocalPlayer
			local char = plr.Character
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if not hrp then return end
			local burgers = {}
			for _, v in ipairs(workspace:GetDescendants()) do
				if AllowedItems[v.Name] and v:IsA("Model") and v:FindFirstChild("HoldPart") then
					burgers[#burgers+1] = v
				end
			end
			workspace.DescendantAdded:Connect(function(obj)
				if AllowedItems[obj.Name] and obj:IsA("Model") then
					task.spawn(function()
						local hp = obj:WaitForChild("HoldPart", 3)
						if hp then burgers[#burgers+1] = obj end
					end)
				end
			end)
			while antiAntiLagEnabled do
				for i = #burgers, 1, -1 do
					local b = burgers[i]
					if not b or not b.Parent or not b:FindFirstChild("HoldPart") then
						table.remove(burgers, i)
					else
						local hp = b.HoldPart
						pcall(function() hp.HoldItemRemoteFunction:InvokeServer(b, char) end)
						task.wait()
						pcall(function()
							hp.DropItemRemoteFunction:InvokeServer(b, CFrame.new(hrp.Position + Vector3.new(0, -2000, 0)), Vector3.new(0,0,0))
						end)
					end
				end
				task.wait()
			end
		end)
	end
})
WhitelistGroup:AddDropdown("MultiWhitelist", {
	Values = getPlayerList(),
	Default = {},
	Multi = true,
	Text = "whitelist people",
})
WhitelistGroup:AddButton({
	Text = "refresh List",
	Func = function()
		Options.MultiWhitelist:SetValues(getPlayerList())
	end
})
local notifyActive = false
local notifyConnection = nil
WhitelistGroup:AddToggle("JoinedNotifyBtn", {
	Text = "Target Joined Notify",
	Default = false,
	Callback = function(on)
		notifyActive = on
		if on then
			notify("Radar", "Tracking has enable...", 3)
			if notifyConnection then notifyConnection:Disconnect() end
			notifyConnection = PS.PlayerAdded:Connect(function(newPlayer)
				if not notifyActive then return end
				local detected = false
				local reason = ""
				local whitelistTable = Options.MultiWhitelist.Value
				for nameString, isSelected in pairs(whitelistTable) do
					if isSelected then
						local actualName = nameString:match("%((.-)%)")
						if actualName == newPlayer.Name then
							detected = true
							reason = "[Whitelist]"
							break
						end
					end
				end
				if not detected and Options.KickPlayerDropdown and Options.KickPlayerDropdown.Value then
					local selection = Options.KickPlayerDropdown.Value
					local selectedName = selection:match("%((.-)%)")
					if selectedName and selectedName == newPlayer.Name then
						detected = true
						reason = "[Main Target]"
					end
				end
				if detected then
					notify("detected", reason .. " detected: " .. newPlayer.Name, 8)
					local sound = Instance.new("Sound", workspace)
					sound.SoundId = "rbxassetid://4590662766"
					sound.Volume = 2
					sound:Play()
					game:GetService("Debris"):AddItem(sound, 3)
				end
			end)
		else
			if notifyConnection then notifyConnection:Disconnect(); notifyConnection = nil end
			notify("Radar", "Tracking Disabled", 2)
		end
	end
})
local GrabGroup = Tabs.Grab:AddLeftGroupbox("Grab Customization")
_G.strength = 750
local strengthConnection
GrabGroup:AddSlider("ThrowPowerSlider", {
	Text = "Power",
	Default = 750,
	Min = 1,
	Max = 20000,
	Rounding = 0,
	Callback = function(value)
		_G.strength = value
	end
})
GrabGroup:AddToggle("ThrowStrengthToggle", {
	Text = "Strength",
	Default = false,
	Callback = function(enabled)
		if enabled then
			strengthConnection = workspace.ChildAdded:Connect(function(model)
				if model.Name == "GrabParts" then
					local partToImpulse = model.GrabPart.WeldConstraint.Part1
					if partToImpulse then
						local velocityObj = Instance.new("BodyVelocity", partToImpulse)
						model:GetPropertyChangedSignal("Parent"):Connect(function()
							if not model.Parent then
								-- PC: right click, Mobile: _G.MobileThrow
								if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 or _G.MobileThrow then
									velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
									velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * _G.strength
									game:GetService("Debris"):AddItem(velocityObj, 1)
									_G.MobileThrow = false
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
	end
})
local killGrabEnabled = false
local function killGrabFunction()
	workspace.ChildAdded:Connect(function(v)
		if v:IsA("Model") and v.Name == "GrabParts" and killGrabEnabled then
			task.wait(0.05)
			local grabPart = v:FindFirstChild("GrabPart")
			if grabPart and grabPart:FindFirstChild("WeldConstraint") then
				local part1 = grabPart.WeldConstraint.Part1
				if part1 and part1.Parent and part1.Parent ~= Player.Character then
					local targetChar = part1.Parent
					local targetHum = targetChar:FindFirstChildOfClass("Humanoid")
					if targetHum and targetChar then
						pcall(function()
							targetHum.Health = 0
							targetChar:BreakJoints()
						end)
					end
				end
			end
		end
	end)
end
killGrabFunction()
GrabGroup:AddToggle("KillGrabToggle", {
	Text = "Kill Grab",
	Default = false,
	Callback = function(Value)
		killGrabEnabled = Value
	end
})
local PlayerView = Tabs.Player:AddLeftGroupbox("View & Movement")
local PlayerESP = Tabs.Player:AddRightGroupbox("ESP")
local PlayerEnv = Tabs.Player:AddLeftGroupbox("Environment")
local PlayerPerf = Tabs.Player:AddRightGroupbox("Performance")
local function enableThirdPerson()
	Player.CameraMode = Enum.CameraMode.Classic
	Camera.CameraType = Enum.CameraType.Custom
	Camera.CameraSubject = Player.Character:WaitForChild("Humanoid")
	Player.CameraMaxZoomDistance = 16456456546
	Player.CameraMinZoomDistance = 0.5
end
local function disableThirdPerson()
	Player.CameraMode = Enum.CameraMode.LockFirstPerson
	Camera.CameraType = Enum.CameraType.Custom
	Camera.CameraSubject = Player.Character:WaitForChild("Humanoid")
	Player.CameraMaxZoomDistance = 0
	Player.CameraMinZoomDistance = 0
end
PlayerView:AddToggle("ThirdPersonToggle", {
	Text = "3rd Person View",
	Default = false,
	Callback = function(Value)
		if Value then enableThirdPerson() else disableThirdPerson() end
	end
})
local spinningConnection
local spinSpeed = 5
PlayerView:AddToggle("SpinToggle", {
	Text = "Spin Character",
	Default = false,
	Callback = function(Value)
		if Value then
			spinningConnection = R.Heartbeat:Connect(function()
				local character = Player.Character
				local root = character and character:FindFirstChild("HumanoidRootPart")
				if root then root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0) end
			end)
		else
			if spinningConnection then spinningConnection:Disconnect(); spinningConnection = nil end
		end
	end
})
PlayerView:AddSlider("SpinSpeed", {
	Text = "Spin Speed",
	Default = 5,
	Min = 1,
	Max = 50,
	Rounding = 0,
	Callback = function(Value)
		spinSpeed = Value
	end
})
local infJump = false
PlayerView:AddToggle("infJumpToggle", {
	Text = "Infinite Jump",
	Default = false,
	Callback = function(Value)
		infJump = Value
	end
})
UserInputService.JumpRequest:Connect(function()
	if infJump then
		local character = Player.Character
		if character and character:FindFirstChildOfClass("Humanoid") then
			character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end
end)
local espEnabled = false
local espBoxes = {}
local targetNames = {"partesp", "playercharacterlocationdetector"}
local function IsTarget(obj)
	if not obj:IsA("BasePart") then return false end
	for _, name in ipairs(targetNames) do
		if string.lower(obj.Name) == string.lower(name) then return true end
	end
	return false
end
local function AddBoxESP(obj)
	if espBoxes[obj] then return end
	local box = Instance.new("BoxHandleAdornment")
	box.Adornee = obj
	box.AlwaysOnTop = true
	box.ZIndex = 5
	box.Color3 = Color3.fromRGB(255,255,255)
	box.Transparency = 0.5
	box.Size = obj.Size
	box.Parent = game.CoreGui
	espBoxes[obj] = box
	obj.AncestryChanged:Connect(function(_, parent)
		if not parent and espBoxes[obj] then espBoxes[obj]:Destroy(); espBoxes[obj] = nil end
	end)
end
local function RemoveAllBoxes()
	for obj, box in pairs(espBoxes) do if box then box:Destroy() end end
	espBoxes = {}
end
local function Scan()
	for _, obj in ipairs(workspace:GetDescendants()) do
		if espEnabled and IsTarget(obj) then AddBoxESP(obj) end
	end
end
workspace.DescendantAdded:Connect(function(obj)
	if espEnabled and IsTarget(obj) then AddBoxESP(obj) end
end)
PlayerESP:AddToggle("BoxESPWhite", {
	Text = "PCLD View",
	Default = false,
	Callback = function(Value)
		espEnabled = Value
		if espEnabled then Scan() else RemoveAllBoxes() end
	end
})
PlayerESP:AddToggle("NicknameESP", {
	Text = "Nickname Esp",
	Default = false,
	Callback = function(Value)
		local function createESP(plr)
			if plr == Player then return end
			if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = plr.Character.HumanoidRootPart
				if hrp:FindFirstChild("NameESP") then return end
				local billboard = Instance.new("BillboardGui")
				billboard.Name = "NameESP"
				billboard.Adornee = hrp
				billboard.Size = UDim2.new(0,100,0,30)
				billboard.StudsOffset = Vector3.new(0,3,0)
				billboard.AlwaysOnTop = true
				billboard.Parent = hrp
				local textLabel = Instance.new("TextLabel")
				textLabel.Size = UDim2.new(1,0,1,0)
				textLabel.BackgroundTransparency = 1
				textLabel.Text = plr.Name
				textLabel.TextColor3 = Color3.fromRGB(255,255,255)
				textLabel.TextStrokeTransparency = 0
				textLabel.TextScaled = true
				textLabel.Parent = billboard
			end
		end
		if Value then
			for _, plr in pairs(PS:GetPlayers()) do
				createESP(plr)
				plr.CharacterAdded:Connect(function() createESP(plr) end)
			end
			PS.PlayerAdded:Connect(function(plr)
				plr.CharacterAdded:Connect(function() createESP(plr) end)
			end)
		else
			for _, plr in pairs(PS:GetPlayers()) do
				if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
					local hrp = plr.Character.HumanoidRootPart
					if hrp:FindFirstChild("NameESP") then hrp.NameESP:Destroy() end
				end
			end
		end
	end
})
local oldProperties = {}
PlayerPerf:AddButton({
	Text = "boost fps",
	Func = function()
		local Lighting = game:GetService("Lighting")
		for _, v in pairs(Workspace:GetDescendants()) do
			if v:IsA("BasePart") then
				if not oldProperties[v] then oldProperties[v] = {Material=v.Material,Reflectance=v.Reflectance,CastShadow=v.CastShadow} end
				v.Material = Enum.Material.Plastic; v.Reflectance = 0; v.CastShadow = false
			elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") then
				if not oldProperties[v] then oldProperties[v] = {Enabled=v.Enabled} end
				v.Enabled = false
			end
		end
		for _, plr in pairs(PS:GetPlayers()) do
			if plr.Character then
				for _, part in pairs(plr.Character:GetDescendants()) do
					if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
						if not oldProperties[part] then oldProperties[part] = {Material=part.Material,Reflectance=part.Reflectance,CastShadow=part.CastShadow} end
						part.Material = Enum.Material.Plastic; part.Reflectance = 0; part.CastShadow = false
					end
				end
			end
		end
		if not oldProperties["Lighting"] then oldProperties["Lighting"] = {GlobalShadows=Lighting.GlobalShadows,FogEnd=Lighting.FogEnd,Brightness=Lighting.Brightness} end
		Lighting.GlobalShadows = false; Lighting.FogEnd = 100000; Lighting.Brightness = 2
	end
})
PlayerPerf:AddButton({
	Text = "delete boost fps",
	Func = function()
		local Lighting = game:GetService("Lighting")
		for obj, props in pairs(oldProperties) do
			if typeof(obj) == "Instance" and obj.Parent then
				for prop, value in pairs(props) do obj[prop] = value end
			elseif obj == "Lighting" then
				for prop, value in pairs(props) do Lighting[prop] = value end
			end
		end
		oldProperties = {}
	end
})
local MiscGroup = Tabs.Misc:AddLeftGroupbox("Miscellaneous")
local mouse = Player:GetMouse()
local tpToolConn
local waterParts = {}
task.spawn(function()
	if workspace:FindFirstChild("Map") and workspace.Map:FindFirstChild("AlwaysHereTweenedObjects") then
		local oceanModel = workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
		for _, v in pairs(oceanModel:GetChildren()) do
			if v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("BasePart") or v:IsA("MeshPart") then
				table.insert(waterParts, {part=v, originalCollide=v.CanCollide})
			end
		end
	end
end)
MiscGroup:AddToggle("DreamyNightShaderToggle", {
	Text = "Dreamy Night Shader",
	Default = false,
	Callback = function(on)
		local Lighting = game:GetService("Lighting")
		if not _G.DreamyNightEffects then
			_G.DreamyNightEffects = {}
			local Blur = Instance.new("BlurEffect"); Blur.Size = 6; Blur.Enabled = false; Blur.Parent = Lighting
			local Bloom = Instance.new("BloomEffect"); Bloom.Intensity = 1.6; Bloom.Size = 90; Bloom.Threshold = 1.4; Bloom.Enabled = false; Bloom.Parent = Lighting
			local Color = Instance.new("ColorCorrectionEffect"); Color.Brightness = 0.15; Color.Contrast = -0.1; Color.Saturation = 0.25; Color.TintColor = Color3.fromRGB(210,220,255); Color.Enabled = false; Color.Parent = Lighting
			local SunRays = Instance.new("SunRaysEffect"); SunRays.Intensity = 0.05; SunRays.Spread = 0.6; SunRays.Enabled = false; SunRays.Parent = Lighting
			local Atmosphere = Instance.new("Atmosphere"); Atmosphere.Density = 0.45; Atmosphere.Offset = 0.1; Atmosphere.Color = Color3.fromRGB(180,190,255); Atmosphere.Decay = Color3.fromRGB(120,130,180); Atmosphere.Glare = 0.15; Atmosphere.Haze = 3; Atmosphere.Enabled = false; Atmosphere.Parent = Lighting
			_G.DreamyNightEffects = {Blur,Bloom,Color,SunRays,Atmosphere}
		end
		for _, effect in ipairs(_G.DreamyNightEffects) do effect.Enabled = on end
		if on then
			Lighting.ClockTime = 0.5; Lighting.GlobalShadows = false; Lighting.Brightness = 2; Lighting.EnvironmentDiffuseScale = 0.2; Lighting.EnvironmentSpecularScale = 0.1; Lighting.FogEnd = 200000
		end
	end
})
local Triggerbot = {
	Enabled = false, Connection = nil, canGrab = true, maxDistance = 20,
	preGrabDelay = 0.00001, postGrabDelay = 0.05, lastTarget = nil,
	lastHitTime = 0, targetMemoryDuration = 0.1, checkThrottle = 0.008, lastCheck = 0
}
local rayParams = RaycastParams.new()
rayParams.FilterType = Enum.RaycastFilterType.Exclude
task.spawn(function()
	local success, result = pcall(function() return RS.GamepassEvents.CheckForGamepass:InvokeServer(20837132) end)
	if success and result then Triggerbot.maxDistance = 29.3 end
end)
if RS:FindFirstChild("GamepassEvents") and RS.GamepassEvents:FindFirstChild("FurtherReachBoughtNotifier") then
	RS.GamepassEvents.FurtherReachBoughtNotifier.OnClientEvent:Connect(function()
		Triggerbot.maxDistance = 29.3
	end)
end
function Triggerbot:GetTarget()
	local c = Player.Character
	if not c or not c:FindFirstChild("HumanoidRootPart") then return end
	if Workspace:FindFirstChild("GrabParts") then return end
	local origin, dir = Camera.CFrame.Position, Camera.CFrame.LookVector
	rayParams.FilterDescendantsInstances = {c, Workspace.Terrain}
	local result = Workspace:Raycast(origin, dir * 1000, rayParams)
	if not result then
		local dirs = {dir, (dir + Vector3.new(0,0.075,0)).Unit, (dir - Vector3.new(0,0.075,0)).Unit}
		for _, d in ipairs(dirs) do
			result = Workspace:Raycast(origin, d * 1000, rayParams)
			if result then break end
		end
	end
	if not result then return end
	local hit = result.Instance
	local model = hit:FindFirstAncestorOfClass("Model")
	if not model or not model:FindFirstChildOfClass("Humanoid") or model == c then return end
	local hum = model:FindFirstChildOfClass("Humanoid")
	if hum.Health <= 0 then return end
	local root = model:FindFirstChild("HumanoidRootPart")
	if not root then return end
	local dist = (c.HumanoidRootPart.Position - root.Position).Magnitude
	if dist > self.maxDistance then return end
	return model
end
function Triggerbot:OnHeartbeat()
	if not self.Enabled or not self.canGrab then return end
	if UserInputService:GetFocusedTextBox() then return end
	if tick() - self.lastCheck < self.checkThrottle then return end
	self.lastCheck = tick()
	local t = self:GetTarget()
	if t then self.lastTarget = t; self.lastHitTime = tick()
	elseif self.lastTarget and tick() - self.lastHitTime > self.targetMemoryDuration then self.lastTarget = nil end
	local c = Player.Character
	local root = self.lastTarget and self.lastTarget:FindFirstChild("HumanoidRootPart")
	if not (self.lastTarget and c and c:FindFirstChild("HumanoidRootPart") and root) then return end
	if (c.HumanoidRootPart.Position - root.Position).Magnitude > self.maxDistance then self.lastTarget = nil return end
	if self.lastTarget then
		self.canGrab = false
		task.spawn(function()
			task.wait(self.preGrabDelay)
			pcall(mouse1press)
			local t0 = tick()
			repeat task.wait(0.02) until not Workspace:FindFirstChild("GrabParts") or tick() - t0 > 1.6
			task.wait(self.postGrabDelay)
			self.canGrab = true
			self.lastTarget = nil
		end)
	end
end
local PacketSpamAmount = 100
MiscGroup:AddSlider("PacketAmountSlider", {
	Text = "Packet Lag",
	Default = 100,
	Min = 10,
	Max = 5000,
	Rounding = 0,
	Callback = function(Value) PacketSpamAmount = Value end
})
MiscGroup:AddToggle("NoBarrierCollision", {
	Text = "Ignore House Barriers",
	Default = false,
	Callback = function(Value)
		local plots = workspace:FindFirstChild("Plots")
		if not plots then return end
		for _, plot in ipairs(plots:GetChildren()) do
			local barrier = plot:FindFirstChild("Barrier")
			if barrier then
				for _, obj in ipairs(barrier:GetDescendants()) do
					if obj:IsA("BasePart") then obj.CanCollide = not Value end
				end
			end
		end
	end
})
MiscGroup:AddToggle("PacketLagToggle", {
	Text = "Packet Lag",
	Default = false,
	Callback = function(Value)
		_G.PacketLagActive = Value
		if Value then
			task.spawn(function()
				for i, e in pairs(game.Players:GetPlayers()) do
					if e.Name == "MaybeFlashh" then return end
				end
				local RS = game:GetService("ReplicatedStorage")
				local GrabEvent = RS:WaitForChild("GrabEvents"):WaitForChild("ExtendGrabLine")
				while _G.PacketLagActive do
					pcall(function() GrabEvent:FireServer(string.rep("Balls Balls Balls Balls", PacketSpamAmount)) end)
					task.wait()
				end
			end)
		else
			_G.PacketLagActive = false
		end
	end
})
MiscGroup:AddToggle("AutoResetToggle", {
	Text = "Auto Reset",
	Default = false,
	Callback = function(on)
		autoResetEnabled = on
		if not on then autoResetEnabled = false return end
		task.spawn(function()
			local plr = game.Players.LocalPlayer
			while autoResetEnabled do
				local char = plr.Character
				local hum = char and char:FindFirstChild("Humanoid")
				if hum and hum.Health > 0 then hum.Health = 0 end
				task.wait(0.5)
			end
		end)
	end
})
MiscGroup:AddToggle("TriggerbotToggle", {
	Text = "Trigger Bot",
	Default = Triggerbot.Enabled,
	Callback = function(value)
		Triggerbot.Enabled = value
		if Triggerbot.Enabled and not Triggerbot.Connection then
			Triggerbot.Connection = R.Heartbeat:Connect(function() Triggerbot:OnHeartbeat() end)
		elseif not Triggerbot.Enabled and Triggerbot.Connection then
			Triggerbot.Connection:Disconnect(); Triggerbot.Connection = nil
		end
	end
})
MiscGroup:AddSlider("FOVSlider", {
	Text = "FOV",
	Default = 90,
	Min = 1,
	Max = 120,
	Rounding = 0,
	Suffix = "°",
	Callback = function(value) game.Workspace.CurrentCamera.FieldOfView = value end
})
local MenuGroup = Tabs["UI Settings"]:AddLeftGroupbox("Menu")
MenuGroup:AddButton("Unload", function() Library:Unload() end)
MenuGroup:AddLabel("Menu Keybind"):AddKeyPicker("MenuKeybind", {
	Default = "RightShift",
	NoUI = true,
	Text = "Menu keybind"
})
Library.ToggleKeybind = Options.MenuKeybind
ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({"MenuKeybind"})
ThemeManager:SetFolder("Ragalic client")
SaveManager:SetFolder("Ragalic client/Configs")
SaveManager:BuildConfigSection(Tabs["UI Settings"])
ThemeManager:ApplyToTab(Tabs["UI Settings"])
PS.PlayerAdded:Connect(function(plr)
	if plr:IsFriendsWith(Player.UserId) then
		notify("Notify friend", plr.Name .. " joined", 5)
	end
end)
local Players = game:GetService("Players")
local variants = {
	"BlackHole","Black_Hole","Blackhole","Black-Hole","BHole","BH","VoidHole","Void",
	"VoidSphere","DarkHole","DarkSphere","DarkOrb","GravityHole","GravityOrb","SpaceHole",
	"SpaceOrb","Singularity","SingularityOrb","EventHorizon","BlackSphere","Anomaly",
	"AnomalyHole","SupermassiveHole","QuantumHole"
}
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local SoundService = game:GetService("SoundService")
local LocalPlayer = Players.LocalPlayer
local function playKickSound()
	local s = Instance.new("Sound")
	s.SoundId = "rbxassetid://79150789336480"
	s.Volume = 5
	s.PlayOnRemove = true
	s.Parent = SoundService
	s:Destroy()
end
local function notifyKick(displayName, username)
	Library:Notify({Title = "Ragalic Client", Description = displayName .. " (" .. username .. ") has been kicked", Time = 6})
end
local function getClosestPlayer(pos)
	local closestPlr = nil
	local closestDist = math.huge
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character then
			local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
			if hrp then
				local dist = (hrp.Position - pos).Magnitude
				if dist < closestDist then closestDist = dist; closestPlr = plr end
			end
		end
	end
	return closestPlr
end
Workspace.ChildAdded:Connect(function(obj)
	if obj.Name == "BlackHoleKick" or obj.Name == "BlackHoleDetected" then
		task.wait(0.05)
		local pos
		if obj:IsA("BasePart") then pos = obj.Position
		elseif obj:IsA("Model") and obj.PrimaryPart then pos = obj.PrimaryPart.Position end
		if not pos then return end
		local plr = getClosestPlayer(pos)
		if not plr then return end
		playKickSound()
		notifyKick(plr.DisplayName, plr.Name)
	end
end)
local FanGroup = Tabs.Fun:AddLeftGroupbox("Troll")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local playJerkOffActive = false
local jerkOffAnimTrack = nil
local jerkOffAnimId = "rbxassetid://168268306"
local selectedKey = Enum.KeyCode.Q
local function startJerkOff()
	local plr = Players.LocalPlayer
	local char = plr.Character or plr.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then animator = Instance.new("Animator"); animator.Parent = hum end
	local anim = Instance.new("Animation")
	anim.AnimationId = jerkOffAnimId
	jerkOffAnimTrack = animator:LoadAnimation(anim)
	jerkOffAnimTrack.Priority = Enum.AnimationPriority.Action
	jerkOffAnimTrack:Play()
	task.spawn(function()
		while playJerkOffActive do
			task.wait(0.1)
			if jerkOffAnimTrack and jerkOffAnimTrack.IsPlaying then jerkOffAnimTrack.TimePosition = 0.3 end
		end
	end)
end
local function stopJerkOff()
	if jerkOffAnimTrack then jerkOffAnimTrack:Stop(); jerkOffAnimTrack = nil end
end
FanGroup:AddToggle("JerkOffToggle", {
	Text = "Jerk Off",
	Default = false,
	Callback = function(on)
		playJerkOffActive = on
		if on then startJerkOff() else stopJerkOff() end
	end
})
FanGroup:AddDropdown("JerkKey", {
	Text = "Toggle Key",
	Values = {"Q","E","R","T"},
	Default = 1,
	Callback = function(v) selectedKey = Enum.KeyCode[v] end
})
UserInputService.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == selectedKey then
		playJerkOffActive = not playJerkOffActive
		if playJerkOffActive then startJerkOff() else stopJerkOff() end
	end
end)
local AurasGroup = Tabs.Auras:AddLeftGroupbox("Auras")
local removeAntiKickAuraActive = false
local removeAntiKickAuraConnection = nil
local removeAntiKickRadius = 15
local useWhitelistRemoveAntiKick = true
AurasGroup:AddDropdown("RemoveAntiKickAuraRadiusDropdown", {
	Text = "Anti Kick Aura Radius",
	Values = {"10","12","14","16","18","20"},
	Default = "15",
	Callback = function(value) removeAntiKickRadius = tonumber(value) end
})
AurasGroup:AddToggle("RemoveAntiKickAuraWhitelistToggle", {
	Text = "Use Whitelist (Friends)",
	Default = true,
	Callback = function(on) useWhitelistRemoveAntiKick = on end
})
AurasGroup:AddToggle("RemoveAntiKickAuraToggle", {
	Text = "Remove Anti Kick Aura",
	Default = false,
	Callback = function(on)
		removeAntiKickAuraActive = on
		if not on then
			if removeAntiKickAuraConnection then removeAntiKickAuraConnection:Disconnect(); removeAntiKickAuraConnection = nil end
			return
		end
		task.spawn(function()
			local RS = game:GetService("ReplicatedStorage")
			local Players = game:GetService("Players")
			local RunService = game:GetService("RunService")
			local LocalPlayer = Players.LocalPlayer
			local GrabEvents = RS:WaitForChild("GrabEvents")
			local SetNetOwner = GrabEvents:WaitForChild("SetNetworkOwner")
			removeAntiKickAuraConnection = RunService.Heartbeat:Connect(function()
				local myChar = LocalPlayer.Character
				local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
				if not myRoot then return end
				for _, target in ipairs(Players:GetPlayers()) do
					if target ~= LocalPlayer then
						local tChar = target.Character
						local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
						if not tRoot then continue end
						if useWhitelistRemoveAntiKick and LocalPlayer:IsFriendsWith(target.UserId) then continue end
						if (tRoot.Position - myRoot.Position).Magnitude <= removeAntiKickRadius then
							local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
							if spawned then
								for _, toyName in ipairs({"NinjaKunai","NinjaShuriken","AntiKick"}) do
									local toy = spawned:FindFirstChild(toyName)
									if toy then
										local part = toy:FindFirstChild("SoundPart")
										if part then
											pcall(function() SetNetOwner:FireServer(part, part.CFrame) end)
											if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
												part.CFrame = CFrame.new(0, 1000, 0)
											end
										end
									end
								end
							end
						end
					end
				end
			end)
		end)
	end
})
local BuildGroup = Tabs.Build:AddLeftGroupbox("Build")
local heartHighRun = false
local heartConnection = nil
local heartToy = nil
BuildGroup:AddToggle("HeartSparklerBuild", {
	Text = "Heart",
	Default = false,
	Callback = function(Value)
		heartHighRun = Value
		local RS = game:GetService("ReplicatedStorage")
		local RunService = game:GetService("RunService")
		local Players = game:GetService("Players")
		local player = Players.LocalPlayer
		if Value then
			task.spawn(function()
				if not player.Character then return end
				local hrp = player.Character:FindFirstChild("HumanoidRootPart")
				if not hrp then return end
				pcall(function() RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("FireworkSparkler", hrp.CFrame * CFrame.new(0,50,0), Vector3.zero) end)
				local folder = workspace:WaitForChild(player.Name .. "SpawnedInToys", 5)
				if not folder then return end
				heartToy = folder:WaitForChild("FireworkSparkler", 5)
				if not heartToy then return end
				local part = heartToy:FindFirstChild("Handle") or heartToy:FindFirstChildWhichIsA("BasePart")
				if not part then return end
				task.wait(0.2)
				for _, v in ipairs(heartToy:GetDescendants()) do
					if v:IsA("BasePart") then v.Anchored = false; v.CanCollide = false; v.Massless = true end
				end
				part:BreakJoints()
				local bp = Instance.new("BodyPosition"); bp.MaxForce = Vector3.new(math.huge,math.huge,math.huge); bp.P = 20000; bp.D = 500; bp.Parent = part
				local bg = Instance.new("BodyGyro"); bg.MaxTorque = Vector3.new(math.huge,math.huge,math.huge); bg.P = 3000; bg.CFrame = CFrame.new(); bg.Parent = part
				local t = 0
				if heartConnection then heartConnection:Disconnect() end
				heartConnection = RunService.Heartbeat:Connect(function(dt)
					if not heartHighRun or not part or not part.Parent then
						if heartConnection then heartConnection:Disconnect() end
						if heartToy then pcall(function() heartToy:Destroy() end) end
						return
					end
					local char = player.Character
					local currentHrp = char and char:FindFirstChild("HumanoidRootPart")
					if not currentHrp then return end
					pcall(function() RS.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame) end)
					t = t + (8 * dt)
					local scale = 1.5
					local x = 16 * math.sin(t) ^ 3
					local y = 13 * math.cos(t) - 5 * math.cos(2*t) - 2 * math.cos(3*t) - math.cos(4*t)
					local relPos = Vector3.new(x*scale, (y*scale)+25, 3)
					bp.Position = currentHrp.CFrame:PointToWorldSpace(relPos)
					bg.CFrame = currentHrp.CFrame
				end)
			end)
		else
			if heartConnection then heartConnection:Disconnect(); heartConnection = nil end
			if heartToy then pcall(function() heartToy:Destroy() end); heartToy = nil end
		end
	end
})
local KeybindsGroup = Tabs.Keybinds:AddLeftGroupbox("Keybinds")
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()
local tpEnabled = true

-- ===============================
-- TELEPORT — PC: Mouse.Hit, Mobile: center screen raycast
-- ===============================
local function doTeleport()
	if not tpEnabled then return end
	local character = Player.Character
	local hrp = character and character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local targetPos
	if isMobile then
		local cam = workspace.CurrentCamera
		local viewSize = cam.ViewportSize
		local ray = cam:ViewportPointToRay(viewSize.X/2, viewSize.Y/2)
		local result = workspace:Raycast(ray.Origin, ray.Direction * 1000)
		if result then
			targetPos = result.Position
		else
			targetPos = hrp.Position + cam.CFrame.LookVector * 50
		end
	else
		targetPos = Mouse.Hit.Position
	end
	hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
end

KeybindsGroup:AddLabel("Teleport Tool"):AddKeyPicker("TPKeybind", {
	Default = "X",
	Text = "Teleport to Mouse",
	NoUI = false,
	Callback = function()
		doTeleport()
	end
})
local antiAntiKickActive = false
TargetGroup:AddToggle("DestroyAntiKickToggle", {
	Text = "Remove Anti Kick",
	Default = false,
	Callback = function(Value)
		antiAntiKickActive = Value
		if Value then
			task.spawn(function()
				local SetNetOwner = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner
				local LocalPlayer = game.Players.LocalPlayer
				local function invis_touch(part, cf) SetNetOwner:FireServer(part, cf) end
				local function CheckAndYeet(toy)
					local part = toy:FindFirstChild("SoundPart")
					if part then
						invis_touch(part, part.CFrame)
						if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
							part.CFrame = CFrame.new(0, 1000, 0)
						end
					end
				end
				while antiAntiKickActive do
					local target = selectedKickPlayer
					if target then
						local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
						if spawned then
							if spawned:FindFirstChild("NinjaKunai") then CheckAndYeet(spawned.NinjaKunai) end
							if spawned:FindFirstChild("NinjaShuriken") then CheckAndYeet(spawned.NinjaShuriken) end
							if spawned:FindFirstChild("AntiKick") then CheckAndYeet(spawned.AntiKick) end
						end
					end
					task.wait(0.1)
				end
			end)
		else
			antiAntiKickActive = false
		end
	end
})
local dualKickAuraEnabled = false
local dualKickAuraRadius = 20
local dualKickAuraWhitelist = true
local dualKickAuraConn
local Players = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local AurasGroup = Tabs.Auras:AddLeftGroupbox("Auras")
AurasGroup:AddDropdown("DualKickAuraRadius", {
	Text = "Dual Kick Aura Radius",
	Values = {"10","20","30","40","50"},
	Default = "20",
	Callback = function(v) dualKickAuraRadius = tonumber(v) end
})
AurasGroup:AddToggle("DualKickAuraWhitelist", {
	Text = "Whitelist Friends",
	Default = true,
	Callback = function(v) dualKickAuraWhitelist = v end
})
local function canKick(plr)
	if not dualKickAuraWhitelist then return true end
	return not LocalPlayer:IsFriendsWith(plr.UserId)
end
AurasGroup:AddToggle("DualHandKickAura", {
	Text = "Dual Hand Kick Aura",
	Default = false,
	Callback = function(on)
		dualKickAuraEnabled = on
		if dualKickAuraConn then dualKickAuraConn:Disconnect(); dualKickAuraConn = nil end
		if not on then return end
		local tickLimiter = 0
		local lastTargets = {}
		dualKickAuraConn = RunService.Heartbeat:Connect(function()
			if tick() - tickLimiter < 0.12 then return end
			tickLimiter = tick()
			local myChar = LocalPlayer.Character
			local hum = myChar and myChar:FindFirstChildOfClass("Humanoid")
			local seat = hum and hum.SeatPart
			local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
			if not (seat and myRoot) then return end
			local seatParent = seat.Parent
			local scriptFolder = seatParent and seatParent:FindFirstChild("BlobmanSeatAndOwnerScript")
			local grab = scriptFolder and scriptFolder:FindFirstChild("CreatureGrab")
			local drop = scriptFolder and scriptFolder:FindFirstChild("CreatureDrop")
			local leftDet = seatParent:FindFirstChild("LeftDetector")
			local rightDet = seatParent:FindFirstChild("RightDetector")
			local leftWeld = leftDet and leftDet:FindFirstChild("LeftWeld")
			local rightWeld = rightDet and rightDet:FindFirstChild("RightWeld")
			if not (grab and drop and leftDet and rightDet and leftWeld and rightWeld) then return end
			for _, plr in ipairs(Players:GetPlayers()) do
				if plr ~= LocalPlayer and plr.Character and canKick(plr) then
					local char = plr.Character
					local hrp = char:FindFirstChild("HumanoidRootPart")
					local hum2 = char:FindFirstChildOfClass("Humanoid")
					if hrp and hum2 and hum2.Health > 0 then
						local dist = (hrp.Position - myRoot.Position).Magnitude
						if dist <= dualKickAuraRadius then
							pcall(function()
								grab:FireServer(leftDet, hrp, leftWeld)
								task.wait(0.04)
								drop:FireServer(leftWeld, hrp)
								grab:FireServer(rightDet, hrp, rightWeld)
								task.wait(0.04)
								drop:FireServer(rightWeld, hrp)
								grab:FireServer(leftDet, hrp, leftWeld)
								grab:FireServer(rightDet, hrp, rightWeld)
								task.wait(0.03)
								drop:FireServer(leftWeld, hrp)
								drop:FireServer(rightWeld, hrp)
							end)
						end
					end
				end
			end
		end)
	end
})
local kickAura1Enabled = false
local kickAura1Radius = 20
local kickAura1Whitelist = true
local kickAura1Conn
local Players = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Player = Players.LocalPlayer
local AurasGroup = Tabs.Auras:AddLeftGroupbox("Auras")
AurasGroup:AddDropdown("KickAura1Radius", {
	Text = "Kick Aura 1 Hand Radius",
	Values = {"10","20","30","40","50"},
	Default = "20",
	Callback = function(v) kickAura1Radius = tonumber(v) end
})
AurasGroup:AddToggle("KickAura1Whitelist", {
	Text = "Whitelist Friends",
	Default = kickAura1Whitelist,
	Callback = function(v) kickAura1Whitelist = v end
})
AurasGroup:AddToggle("KickAura1Toggle", {
	Text = "Kick Aura 1 Hand (grab + blob)",
	Default = kickAura1Enabled,
	Callback = function(on)
		kickAura1Enabled = on
		if kickAura1Conn then kickAura1Conn:Disconnect(); kickAura1Conn = nil end
		if not on then return end
		kickAura1Conn = RunService.Heartbeat:Connect(function()
			local char = Player.Character
			local hum = char and char:FindFirstChild("Humanoid")
			local seat = hum and hum.SeatPart
			local root = char and char:FindFirstChild("HumanoidRootPart")
			if not (seat and root) then return end
			local blob = seat.Parent
			local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
			local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
			local CG = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
			local CD = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
			local R_Det = blob:FindFirstChild("RightDetector")
			local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld"))
			if not (CG and CD and R_Det and R_Weld and blobRoot) then return end
			local packetTimer = 0
			for _, plr in ipairs(Players:GetPlayers()) do
				if plr ~= Player and plr.Character and (not kickAura1Whitelist or not Player:IsFriendsWith(plr.UserId)) then
					local tChar = plr.Character
					local tRoot = tChar:FindFirstChild("HumanoidRootPart")
					local tHum = tChar:FindFirstChild("Humanoid")
					if tRoot and tHum and tHum.Health > 0 then
						local dist = (tRoot.Position - root.Position).Magnitude
						if dist <= kickAura1Radius then
							pcall(function()
								if tick() - packetTimer > 0.05 then
									packetTimer = tick()
																		local weld = R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld")
									if weld then
										CD:FireServer(weld)
										CG:FireServer(R_Det, tRoot, R_Weld)
									end
								end
							end)
						end
					end
				end
			end
		end)
	end
})
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local Player = Players.LocalPlayer
local FanGroup = Tabs.Fun:AddLeftGroupbox("Animations")
local animEnabled = false
local currentTrack = nil
local selectedAnimName = "Crazy"
local selectedKey = Enum.KeyCode.Q
local Animations = {
	["Crazy"]    = "rbxassetid://248263260",
	["Insane"]   = "rbxassetid://35654637",
	["Collapse"] = "rbxassetid://35154961",
	["Zombie"]   = "rbxassetid://33796059",
}
local function playAnimation()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then animator = Instance.new("Animator"); animator.Parent = hum end
	if currentTrack then currentTrack:Stop(); currentTrack = nil end
	local anim = Instance.new("Animation")
	anim.AnimationId = Animations[selectedAnimName]
	currentTrack = animator:LoadAnimation(anim)
	currentTrack.Priority = Enum.AnimationPriority.Action
	currentTrack.Looped = true
	currentTrack:Play()
	task.spawn(function()
		while animEnabled and currentTrack do
			if currentTrack.TimePosition > 0.9 then currentTrack.TimePosition = 0.3 end
			task.wait(0.05)
		end
	end)
end
local function stopAnimation()
	if currentTrack then currentTrack:Stop(); currentTrack = nil end
end
FanGroup:AddToggle("AnimToggle", {
	Text = "Play Animation",
	Default = false,
	Callback = function(on)
		animEnabled = on
		if on then playAnimation() else stopAnimation() end
	end
})
FanGroup:AddDropdown("AnimSelect", {
	Text = "Animation",
	Values = {"Crazy","Insane","Collapse","Zombie"},
	Default = 1,
	Callback = function(v)
		selectedAnimName = v
		if animEnabled then playAnimation() end
	end
})
FanGroup:AddDropdown("AnimKeybind", {
	Text = "Toggle Key",
	Values = {"Q","E","R","T","F","Z","X","C"},
	Default = 1,
	Callback = function(v) selectedKey = Enum.KeyCode[v] end
})
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local function getNearestBlobman(maxDist)
	local char = Player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local nearest, dist = nil, maxDist or 50
	for _, model in ipairs(workspace:GetDescendants()) do
		if model:IsA("Model") and model.Name == "CreatureBlobman" then
			local root = model:FindFirstChild("HumanoidRootPart") or model.PrimaryPart
			if root then
				local d = (root.Position - hrp.Position).Magnitude
				if d < dist then dist = d; nearest = model end
			end
		end
	end
	return nearest
end
local function SitOnBlobman()
	local char = Player.Character
	if not char then return end
	local hum = char:FindFirstChildOfClass("Humanoid")
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hum or not hrp then return end
	if hum.SeatPart then return end
	local blob = getNearestBlobman(40)
	if not blob then warn("Blobman not found nearby") return end
	local seat = blob:FindFirstChildWhichIsA("Seat", true) or blob:FindFirstChildWhichIsA("VehicleSeat", true)
	if not seat then warn("Blobman seat not found") return end
	hrp.CFrame = seat.CFrame * CFrame.new(0, 1.2, -1)
	task.wait(0.05)
	pcall(function() seat:Sit(hum) end)
end
KeybindsGroup:AddLabel("Blobman"):AddKeyPicker("SitBlobmanKey", {
	Default = "Z",
	Text = "Sit on nearest Blobman",
	NoUI = false,
	Callback = function() SitOnBlobman() end
})
FanGroup:AddToggle("FollowStare", {
	Text = "Follow & Stare",
	Default = false,
	Callback = function(on)
		follow = on
		task.spawn(function()
			local lp = game.Players.LocalPlayer
			while follow do
				local target = game.Players:GetPlayers()[math.random(#game.Players:GetPlayers())]
				if target ~= lp and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
					local hrp = lp.Character.HumanoidRootPart
					local thrp = target.Character.HumanoidRootPart
					hrp.CFrame = CFrame.new(thrp.Position + thrp.CFrame.LookVector * -2, thrp.Position)
				end
				task.wait(0.3)
			end
		end)
	end
})
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
FanGroup:AddToggle("FakeDeathToggle", {
	Text = "Fake Death",
	Default = false,
	Callback = function(on)
		local char = LocalPlayer.Character
		if not char then return end
		local hum = char:FindFirstChildOfClass("Humanoid")
		if not hum then return end
		if on then
			hum:ChangeState(Enum.HumanoidStateType.Physics)
			hum.PlatformStand = true
		else
			hum.PlatformStand = false
			hum:ChangeState(Enum.HumanoidStateType.GettingUp)
		end
	end
})
local fakeLagConn
FanGroup:AddToggle("FakeLagToggle", {
	Text = "Fake Lag",
	Default = false,
	Callback = function(on)
		if fakeLagConn then fakeLagConn:Disconnect(); fakeLagConn = nil end
		if not on then return end
		fakeLagConn = RunService.Heartbeat:Connect(function()
			local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			if not root then return end
			if math.random(1,5) == 1 then
				root.CFrame = root.CFrame * CFrame.new(math.random(-2,2)/10, 0, math.random(-2,2)/10)
			end
		end)
	end
})
local FanGroup = Tabs.Fun:AddLeftGroupbox("Troll")
local Players = game:GetService("Players")
local playBangActive = false
local bangAnimTrack = nil
local bangAnimId = "rbxassetid://148840371"
local bangSpeed = 10
local function startBang()
	local plr = Players.LocalPlayer
	local char = plr.Character or plr.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then animator = Instance.new("Animator"); animator.Parent = hum end
	local anim = Instance.new("Animation")
	anim.AnimationId = bangAnimId
	bangAnimTrack = animator:LoadAnimation(anim)
	bangAnimTrack.Priority = Enum.AnimationPriority.Action
	bangAnimTrack:Play()
	bangAnimTrack:AdjustSpeed(bangSpeed)
	task.spawn(function()
		while playBangActive do
			task.wait(0.1)
			if bangAnimTrack and bangAnimTrack.IsPlaying then bangAnimTrack.TimePosition = 0.1 end
		end
	end)
end
local function stopBang()
	if bangAnimTrack then bangAnimTrack:Stop(); bangAnimTrack = nil end
end
FanGroup:AddToggle("BangToggle", {
	Text = "Bang (Slow)",
	Default = false,
	Callback = function(on)
		playBangActive = on
		if on then startBang() else stopBang() end
	end
})
local FanGroup = Tabs.Fun:AddLeftGroupbox("Troll")
FanGroup:AddToggle("UFOShurikenStick", {
	Text = "Stick Shuriken to UFO",
	Default = false,
	Callback = function(state)
		if not state then return end
		local Players = game:GetService("Players")
		local RS = game:GetService("ReplicatedStorage")
		local LocalPlayer = Players.LocalPlayer
		local PlayerName = LocalPlayer.Name
		local StickyEvent = RS:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
		local SpawnRemote = RS.MenuToys:WaitForChild("SpawnToyRemoteFunction")
		local CanSpawn = LocalPlayer:WaitForChild("CanSpawnToy")
		local ToysFolder = workspace:WaitForChild(PlayerName .. "SpawnedInToys")
		local UFOs = {
			workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("InnerUFO"),
			workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("OuterUFO")
		}
		local function getHRP()
			if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				return LocalPlayer.Character.HumanoidRootPart
			end
			return LocalPlayer.CharacterAdded:Wait():WaitForChild("HumanoidRootPart")
		end
		task.spawn(function()
			for i = 1, 12 do
				local t = tick()
				while not CanSpawn.Value do
					if tick() - t > 5 then break end
					task.wait(0.1)
				end
				local hrp = getHRP()
				if hrp then
					pcall(function()
						SpawnRemote:InvokeServer("NinjaShuriken", hrp.CFrame * CFrame.new(0,10,15), Vector3.new())
					end)
				end
				task.wait(0.15)
			end
			task.wait(1)
			for _, Toy in ipairs(ToysFolder:GetChildren()) do
				if Toy.Name == "NinjaShuriken" and Toy:FindFirstChild("StickyPart") then
					for _, UFO in ipairs(UFOs) do
						if UFO and UFO:FindFirstChild("Object") and UFO.Object:FindFirstChild("ObjectModel") and UFO.Object.ObjectModel:FindFirstChild("Body") then
							StickyEvent:FireServer(Toy.StickyPart, UFO.Object.ObjectModel.Body, CFrame.new())
							local follow = UFO.Object:FindFirstChild("FollowThisPart")
							if follow then
								if follow:FindFirstChild("AlignOrientation") then follow.AlignOrientation.Enabled = false end
								if follow:FindFirstChild("AlignPosition") then follow.AlignPosition.Enabled = false end
							end
						end
					end
				end
			end
		end)
	end
})
GrabGroup:AddToggle("MassLessGrabToggle", {
	Text = "MassLess Grab",
	Default = false,
	Callback = function(Value)
		_G.MassLessGrab = Value
		if not _G.MassLessGrab then
			if _G.MLConn then _G.MLConn:Disconnect(); _G.MLConn = nil end
			return
		end
		if _G.MLConn then _G.MLConn:Disconnect(); _G.MLConn = nil end
		_G.MLSense = _G.MLSense or 200
		_G.MLConn = game:GetService("RunService").Heartbeat:Connect(function()
			if not _G.MassLessGrab then return end
			local gp = workspace:FindFirstChild("GrabParts")
			if not gp then return end
			local dp = gp:FindFirstChild("DragPart")
			if not dp then return end
			local ap = dp:FindFirstChild("AlignPosition")
			local ao = dp:FindFirstChild("AlignOrientation")
			if ap then ap.Responsiveness = _G.MLSense; ap.MaxForce = math.huge; ap.MaxVelocity = math.huge end
			if ao then ao.Responsiveness = _G.MLSense; ao.MaxTorque = math.huge end
		end)
	end
})

-- ===============================
-- MOBILE GUI — только если TouchEnabled
-- ===============================
if isMobile then
	local mobileGui = Instance.new("ScreenGui")
	mobileGui.Name = "RagalicMobileGUI"
	mobileGui.ResetOnSpawn = false
	mobileGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	mobileGui.Parent = game:GetService("CoreGui")

	-- ===============================
	-- HELPER: создание кнопки
	-- ===============================
	local function makeBtn(name, text, pos, size, parent)
		local btn = Instance.new("TextButton")
		btn.Name = name
		btn.Text = text
		btn.Size = size or UDim2.new(0, 60, 0, 60)
		btn.Position = pos
		btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		btn.BackgroundTransparency = 0.3
		btn.TextColor3 = Color3.fromRGB(255, 255, 255)
		btn.TextSize = 16
		btn.Font = Enum.Font.GothamBold
		btn.Parent = parent or mobileGui
		local corner = Instance.new("UICorner")
		corner.CornerRadius = UDim.new(0, 12)
		corner.Parent = btn
		local stroke = Instance.new("UIStroke")
		stroke.Color = Color3.fromRGB(80, 80, 80)
		stroke.Thickness = 1.5
		stroke.Parent = btn
		return btn
	end

	-- ===============================
	-- OPEN MENU BUTTON (top-left)
	-- ===============================
	local openBtn = makeBtn("OpenMenu", "☰", UDim2.new(0, 10, 0, 40), UDim2.new(0, 45, 0, 45))
	openBtn.TextSize = 22
	openBtn.MouseButton1Click:Connect(function()
		Library:ToggleUI()
	end)

	-- ===============================
	-- MOVEMENT PAD (bottom-left)
	-- ===============================
	local padFrame = Instance.new("Frame")
	padFrame.Name = "MovePad"
	padFrame.Size = UDim2.new(0, 190, 0, 190)
	padFrame.Position = UDim2.new(0, 10, 1, -200)
	padFrame.BackgroundTransparency = 1
	padFrame.Parent = mobileGui

	local dirs = {
		{name="W", text="W", pos=UDim2.new(0.5,-30,0,0), key="W"},
		{name="A", text="A", pos=UDim2.new(0,0,0.5,-30), key="A"},
		{name="S", text="S", pos=UDim2.new(0.5,-30,1,-60), key="S"},
		{name="D", text="D", pos=UDim2.new(1,-60,0.5,-30), key="D"},
	}
	for _, d in ipairs(dirs) do
		local b = makeBtn("Mob_"..d.name, d.text, d.pos, UDim2.new(0,60,0,60), padFrame)
		b.InputBegan:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.Touch then
				_G.MobileKeys[d.key] = true
				b.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
			end
		end)
		b.InputEnded:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.Touch then
				_G.MobileKeys[d.key] = false
				b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
			end
		end)
	end

	-- ===============================
	-- ACTION BUTTONS (bottom-right)
	-- ===============================
	local actionFrame = Instance.new("Frame")
	actionFrame.Name = "ActionBtns"
	actionFrame.Size = UDim2.new(0, 200, 0, 200)
	actionFrame.Position = UDim2.new(1, -210, 1, -210)
	actionFrame.BackgroundTransparency = 1
	actionFrame.Parent = mobileGui

	-- SPACE (Jump / Fly Up)
	local spaceBtn = makeBtn("Mob_Space", "▲", UDim2.new(0.5,-30,0,0), UDim2.new(0,60,0,60), actionFrame)
	spaceBtn.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.Touch then
			_G.MobileKeys.Space = true
			spaceBtn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
		end
	end)
	spaceBtn.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.Touch then
			_G.MobileKeys.Space = false
			spaceBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		end
	end)

	-- CTRL (Fly Down)
	local ctrlBtn = makeBtn("Mob_Ctrl", "▼", UDim2.new(0.5,-30,1,-60), UDim2.new(0,60,0,60), actionFrame)
	ctrlBtn.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.Touch then
			_G.MobileKeys.Ctrl = true
			ctrlBtn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
		end
	end)
	ctrlBtn.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.Touch then
			_G.MobileKeys.Ctrl = false
			ctrlBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		end
	end)

	-- THROW (for Strength grab)
	local throwBtn = makeBtn("Mob_Throw", "🎯", UDim2.new(1,-60,0.5,-30), UDim2.new(0,60,0,60), actionFrame)
	throwBtn.MouseButton1Click:Connect(function()
		_G.MobileThrow = true
		throwBtn.BackgroundColor3 = Color3.fromRGB(120, 40, 40)
		task.delay(0.3, function()
			throwBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		end)
	end)

	-- ===============================
	-- QUICK ACTIONS (right side, middle)
	-- ===============================
	local quickFrame = Instance.new("Frame")
	quickFrame.Name = "QuickActions"
	quickFrame.Size = UDim2.new(0, 55, 0, 280)
	quickFrame.Position = UDim2.new(1, -65, 0.5, -140)
	quickFrame.BackgroundTransparency = 1
	quickFrame.Parent = mobileGui

	-- TP (teleport to center of screen)
	local tpBtn = makeBtn("Mob_TP", "TP", UDim2.new(0,0,0,0), UDim2.new(1,0,0,50), quickFrame)
	tpBtn.MouseButton1Click:Connect(function()
		doTeleport()
		tpBtn.BackgroundColor3 = Color3.fromRGB(40, 100, 40)
		task.delay(0.3, function() tpBtn.BackgroundColor3 = Color3.fromRGB(30,30,30) end)
	end)

	-- SIT BLOB
	local sitBtn = makeBtn("Mob_Sit", "SIT", UDim2.new(0,0,0,55), UDim2.new(1,0,0,50), quickFrame)
	sitBtn.MouseButton1Click:Connect(function()
		doAutoSitBlob()
		sitBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 100)
		task.delay(0.3, function() sitBtn.BackgroundColor3 = Color3.fromRGB(30,30,30) end)
	end)

	-- BLOB FLY TOGGLE
	local flyBtn = makeBtn("Mob_Fly", "FLY", UDim2.new(0,0,0,110), UDim2.new(1,0,0,50), quickFrame)
	flyBtn.MouseButton1Click:Connect(function()
		doBlobFlyToggle()
		if blobFlyActive then
			flyBtn.BackgroundColor3 = Color3.fromRGB(40, 120, 40)
			flyBtn.Text = "FLY ✓"
		else
			flyBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
			flyBtn.Text = "FLY"
		end
	end)

	-- AUTO SIT BLOB (T key equivalent)
	local autoSitBtn = makeBtn("Mob_AutoSit", "T", UDim2.new(0,0,0,165), UDim2.new(1,0,0,50), quickFrame)
	autoSitBtn.MouseButton1Click:Connect(function()
		_G.AutoSitBlobT = true
		doAutoSitBlob()
		autoSitBtn.BackgroundColor3 = Color3.fromRGB(100, 80, 20)
		task.delay(0.5, function() autoSitBtn.BackgroundColor3 = Color3.fromRGB(30,30,30) end)
	end)

	-- R key equivalent
	local rBtn = makeBtn("Mob_R", "R", UDim2.new(0,0,0,220), UDim2.new(1,0,0,50), quickFrame)
	rBtn.MouseButton1Click:Connect(function()
		doBlobFlyToggle()
		if blobFlyActive then
			rBtn.BackgroundColor3 = Color3.fromRGB(40, 120, 40)
		else
			rBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
		end
	end)
end
