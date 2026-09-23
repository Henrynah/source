-- FTAP Reverse
-- https://t.me/ReverseFTAP

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local TextChatService = game:GetService("TextChatService")
local ReplicatedFirst = game:GetService("ReplicatedFirst")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()
local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
local Window = Rayfield:CreateWindow({
	Name = "VyzenHub V1",
	LoadingTitle = "VyzenHub",
	LoadingSubtitle = "Loading",
	ConfigurationSaving = {
		Enabled = true,
		FolderName = "VyzenHubV1",
		FileName = "VyzenV1Config",
	},
	Discord = {Enabled = false},
	KeySystem = false,
})
local Combat = Window:CreateTab("Combat", 4483362458)
local PlayerTab = Window:CreateTab("Player", 4483362458)
local Mass = Window:CreateTab("Mass", 4483362458)
local Protection = Window:CreateTab("Protection", 4483362458)
local Auras = Window:CreateTab("Auras", 4483362458)
local ESP = Window:CreateTab("ESP", 4483362458)
local Extras = Window:CreateTab("Extras", 4483362458)

local Config = {
	SilentAimEnabled = false,
	FOVRadius = 120,
	MaxDistance = 200,
	Hitbox = "Head",
	ShowFOV = false,
	WallCheck = true,
	WalkspeedEnabled = false,
	WalkspeedValue = 16,
	InfiniteJumpEnabled = false,
	JumpPower = 50,
	NoclipEnabled = false,
	StrengthEnabled = false,
	StrengthValue = 1,
	KillGrabEnabled = false,
	AntiGrabEnabled = false,
	AntiFlingEnabled = false,
	AntiRagdollAuto = false,
	LaunchAuraEnabled = false,
	TelekinesisAuraEnabled = false,
	DeathAuraEnabled = false,
	AuraRadius = 20,
	LagEnabled = false,
	LagMode = "Spam",
	LagIntensity = 1,
	NameESP = false,
	NameColor = Color3.fromRGB(255, 255, 255),
	NameSize = 14,
	ChamsEnabled = false,
	ChamsFillColor = Color3.fromRGB(255, 0, 0),
	ChamsOutlineColor = Color3.fromRGB(255, 255, 255),
	ChamsTransparency = 0.5,
	ProfilePictures = false,
	ProfileSize = 40,
	ESPMaxDistance = 500,
	LoopKillEnabled = false,
	LoopKillDelay = 0.5,
	LoopKillTarget = nil,
	KillTarget = nil,
	UseClone = false,
	CloneTransparency = 0.5,
}

local fovCircle
pcall(function()
	fovCircle = Drawing.new("Circle")
	fovCircle.Thickness = 1
	fovCircle.NumSides = 64
	fovCircle.Radius = Config.FOVRadius
	fovCircle.Filled = false
	fovCircle.Transparency = 1
	fovCircle.Color = Color3.fromRGB(255, 255, 255)
	fovCircle.Visible = false
end)

local function getCharacter(player)
	return player and player.Character
end

local function getHumanoid(player)
	local character = getCharacter(player)
	return character and character:FindFirstChildOfClass("Humanoid")
end

local function getRoot(player)
	local character = getCharacter(player)
	return character and (character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso"))
end

local function getHitPart(player)
	local character = getCharacter(player)
	if not character then
		return nil
	end
	return character:FindFirstChild(Config.Hitbox) or character:FindFirstChild("Head") or getRoot(player)
end

local function isVisible(part)
	if not Config.WallCheck or not part then
		return true
	end
	local origin = Camera.CFrame.Position
	local params = RaycastParams.new()
	params.FilterType = Enum.RaycastFilterType.Blacklist
	params.FilterDescendantsInstances = { LocalPlayer.Character, Camera }
	local result = Workspace:Raycast(origin, part.Position - origin, params)
	return not result or result.Instance:IsDescendantOf(part.Parent)
end

local function getClosest()
	local closest, closestDist = nil, Config.FOVRadius
	local mousePos = UserInputService:GetMouseLocation()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			local part = getHitPart(player)
			local humanoid = getHumanoid(player)
			if part and humanoid and humanoid.Health > 0 then
				local screen, onScreen = Camera:WorldToViewportPoint(part.Position)
				if onScreen then
					local dist = (Vector2.new(screen.X, screen.Y) - mousePos).Magnitude
					local worldDist = (part.Position - Camera.CFrame.Position).Magnitude
					if dist < closestDist and worldDist <= Config.MaxDistance and isVisible(part) then
						closest = player
						closestDist = dist
					end
				end
			end
		end
	end
	return closest
end

local playerNames = {"None"}
local function refreshPlayers()
	playerNames = {"None"}
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			table.insert(playerNames, player.Name)
		end
	end
end
refreshPlayers()

local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
local CharacterEvents = ReplicatedStorage:FindFirstChild("CharacterEvents")
local MenuToys = Workspace:FindFirstChild("MenuToys") or ReplicatedStorage:FindFirstChild("MenuToys")

Combat:CreateSection("Silent Aim")
Combat:CreateToggle({
	Name = "Enable Silent Aim",
	CurrentValue = false,
	Flag = "SilentAim",
	Callback = function(value)
		Config.SilentAimEnabled = value
		if fovCircle then
			fovCircle.Visible = value and Config.ShowFOV
		end
	end,
})
Combat:CreateToggle({
	Name = "Wall Check",
	CurrentValue = true,
	Flag = "WallCheck",
	Callback = function(value)
		Config.WallCheck = value
	end,
})
Combat:CreateToggle({
	Name = "Show FOV",
	CurrentValue = false,
	Flag = "ShowFOV",
	Callback = function(value)
		Config.ShowFOV = value
		if fovCircle then
			fovCircle.Visible = value and Config.SilentAimEnabled
		end
	end,
})
Combat:CreateSlider({
	Name = "FOV Size",
	Range = {20, 400},
	Increment = 1,
	Suffix = "",
	CurrentValue = 120,
	Flag = "FOVRadius",
	Callback = function(value)
		Config.FOVRadius = value
		if fovCircle then
			fovCircle.Radius = value
		end
	end,
})
Combat:CreateSlider({
	Name = "Lock Distance",
	Range = {10, 500},
	Increment = 1,
	Suffix = " studs",
	CurrentValue = 200,
	Flag = "MaxDistance",
	Callback = function(value)
		Config.MaxDistance = value
	end,
})
Combat:CreateDropdown({
	Name = "Hitbox",
	Options = {"Head", "HumanoidRootPart", "Torso"},
	CurrentOption = {"Head"},
	MultipleOptions = false,
	Flag = "Hitbox",
	Callback = function(option)
		Config.Hitbox = type(option) == "table" and option[1] or option
	end,
})

Combat:CreateSection("Strength")
Combat:CreateToggle({
	Name = "Super Strength",
	CurrentValue = false,
	Flag = "Strength",
	Callback = function(value)
		Config.StrengthEnabled = value
	end,
})
Combat:CreateSlider({
	Name = "Strength Power",
	Range = {1, 20},
	Increment = 1,
	Suffix = " power",
	CurrentValue = 1,
	Flag = "StrengthValue",
	Callback = function(value)
		Config.StrengthValue = value
	end,
})

Combat:CreateSection("Grab Features")
Combat:CreateToggle({
	Name = "Kill On Grab",
	CurrentValue = false,
	Flag = "KillGrab",
	Callback = function(value)
		Config.KillGrabEnabled = value
	end,
})

PlayerTab:CreateSection("Movement")
PlayerTab:CreateToggle({
	Name = "Speed Boost",
	CurrentValue = false,
	Flag = "Walkspeed",
	Callback = function(value)
		Config.WalkspeedEnabled = value
	end,
})
PlayerTab:CreateSlider({
	Name = "Speed Multiplier",
	Range = {16, 200},
	Increment = 1,
	Suffix = "x",
	CurrentValue = 16,
	Flag = "WalkspeedValue",
	Callback = function(value)
		Config.WalkspeedValue = value
	end,
})
PlayerTab:CreateToggle({
	Name = "Infinite Jump",
	CurrentValue = false,
	Flag = "InfiniteJump",
	Callback = function(value)
		Config.InfiniteJumpEnabled = value
	end,
})
PlayerTab:CreateSlider({
	Name = "Jump Power",
	Range = {50, 250},
	Increment = 1,
	CurrentValue = 50,
	Flag = "JumpPower",
	Callback = function(value)
		Config.JumpPower = value
	end,
})
PlayerTab:CreateToggle({
	Name = "Noclip",
	CurrentValue = false,
	Flag = "Noclip",
	Callback = function(value)
		Config.NoclipEnabled = value
	end,
})

Mass:CreateSection("Kill Everyone")
Mass:CreateButton({
	Name = "Kill All Players",
	Callback = function()
		Rayfield:Notify({ Title = "Kill All", Content = "Started", Duration = 3 })
		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= LocalPlayer then
				local humanoid = getHumanoid(player)
				if humanoid then
					pcall(function()
						humanoid.Health = 0
					end)
				end
			end
		end
	end,
})

local killDropdown
Mass:CreateSection("Kill Specific Player")
killDropdown = Mass:CreateDropdown({
	Name = "Select Player to Kill",
	Options = playerNames,
	CurrentOption = { "None" },
	MultipleOptions = false,
	Flag = "KillTarget",
	Callback = function(option)
		local name = type(option) == "table" and option[1] or option
		Config.KillTarget = name ~= "None" and Players:FindFirstChild(name) or nil
	end,
})
Mass:CreateButton({
	Name = "Refresh Player List",
	Callback = function()
		refreshPlayers()
		killDropdown:Refresh(playerNames)
		Rayfield:Notify({ Title = "Refreshed", Content = "Player list updated", Duration = 2 })
	end,
})
Mass:CreateButton({
	Name = "Kill Selected Player",
	Callback = function()
		if not Config.KillTarget then
			Rayfield:Notify({Title = "Error", Content = "Select a player first", Duration = 2})
			return
		end
		local humanoid = getHumanoid(Config.KillTarget)
		if humanoid then
			humanoid.Health = 0
			Rayfield:Notify({Title = "Kill Player", Content = "Killing " .. Config.KillTarget.Name, Duration = 2})
		else
			Rayfield:Notify({Title = "Error", Content = "Character not found", Duration = 2})
		end
	end,
})

local loopDropdown
Mass:CreateSection("Loop Kill Player")
loopDropdown = Mass:CreateDropdown({
	Name = "Select Player to Loop Kill",
	Options = playerNames,
	CurrentOption = {"None"},
	MultipleOptions = false,
	Flag = "LoopKillTarget",
	Callback = function(option)
		local name = type(option) == "table" and option[1] or option
		Config.LoopKillTarget = name ~= "None" and Players:FindFirstChild(name) or nil
	end,
})
Mass:CreateButton({
	Name = "Refresh Loop Kill List",
	Callback = function()
		refreshPlayers()
		loopDropdown:Refresh(playerNames)
		Rayfield:Notify({ Title = "List updated", Content = "Player list updated", Duration = 2 })
	end,
})
Mass:CreateToggle({
	Name = "Enable Loop Kill",
	CurrentValue = false,
	Flag = "LoopKill",
	Callback = function(value)
		Config.LoopKillEnabled = value
	end,
})
Mass:CreateSlider({
	Name = "Loop Kill Delay",
	Range = {0.1, 3},
	Increment = 0.1,
	CurrentValue = 0.5,
	Flag = "LoopKillDelay",
	Callback = function(value)
		Config.LoopKillDelay = value
	end,
})
Mass:CreateSlider({
	Name = "Clone Transparency",
	Range = {0, 1},
	Increment = 0.05,
	CurrentValue = 0.5,
	Flag = "CloneTransparency",
	Callback = function(value)
		Config.CloneTransparency = value
	end,
})

Mass:CreateSection("Kick Everyone")
Mass:CreateButton({
	Name = "Kick All (Blobman)",
	Callback = function()
		Rayfield:Notify({Title = "Kick All", Content = "Starting", Duration = 3})
		local toys = Workspace:FindFirstChild("Toys") or MenuToys
		if not toys then
			Rayfield:Notify({Title = "Error", Content = "Toys folder not found", Duration = 3})
			return
		end
		local spawnToy = ReplicatedStorage:FindFirstChild("SpawnToyRemoteFunction")
		pcall(function()
			if spawnToy then
				spawnToy:InvokeServer("CreatureBlobman")
			end
		end)
		Rayfield:Notify({Title = "Kick All Complete", Content = "Kicked players", Duration = 3})
	end,
})

Protection:CreateSection("Protection Features")
Protection:CreateToggle({
	Name = "Anti Grab",
	CurrentValue = false,
	Flag = "AntiGrab",
	Callback = function(value)
		Config.AntiGrabEnabled = value
	end,
})
Protection:CreateToggle({
	Name = "Anti Fling",
	CurrentValue = false,
	Flag = "AntiFling",
	Callback = function(value)
		Config.AntiFlingEnabled = value
	end,
})
Protection:CreateToggle({
	Name = "Auto Anti Ragdoll",
	CurrentValue = false,
	Flag = "AutoAntiRagdoll",
	Callback = function(value)
		Config.AntiRagdollAuto = value
	end,
})
Protection:CreateButton({
	Name = "Anti Ragdoll Once",
	Callback = function()
		local humanoid = getHumanoid(LocalPlayer)
		if humanoid then
			humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
			humanoid.Sit = false
			Rayfield:Notify({ Title = "Anti Ragdoll", Content = "Applied", Duration = 2 })
		end
	end,
})
Protection:CreateButton({
	Name = "Delete Void",
	Callback = function()
		pcall(function()
			Workspace.FallenPartsDestroyHeight = -50000
		end)
		Rayfield:Notify({ Title = "Void", Content = "Destroyed", Duration = 2 })
	end,
})

Auras:CreateSection("Combat Auras")
Auras:CreateToggle({
	Name = "Launch Aura",
	CurrentValue = false,
	Flag = "LaunchAura",
	Callback = function(value)
		Config.LaunchAuraEnabled = value
	end,
})
Auras:CreateToggle({
	Name = "Telekinesis Aura",
	CurrentValue = false,
	Flag = "TelekinesisAura",
	Callback = function(value)
		Config.TelekinesisAuraEnabled = value
	end,
})
Auras:CreateToggle({
	Name = "Death Aura",
	CurrentValue = false,
	Flag = "DeathAura",
	Callback = function(value)
		Config.DeathAuraEnabled = value
	end,
})
Auras:CreateSlider({
	Name = "Aura Radius",
	Range = {5, 80},
	Increment = 1,
	Suffix = " studs",
	CurrentValue = 20,
	Flag = "AuraRadius",
	Callback = function(value)
		Config.AuraRadius = value
	end,
})

ESP:CreateSection("Main ESP")
ESP:CreateToggle({
	Name = "Enable ESP",
	CurrentValue = false,
	Flag = "NameESP",
	Callback = function(value)
		Config.NameESP = value
	end,
})
ESP:CreateSlider({
	Name = "Max Distance",
	Range = {50, 2000},
	Increment = 10,
	Suffix = " studs",
	CurrentValue = 500,
	Flag = "ESPMaxDistance",
	Callback = function(value)
		Config.ESPMaxDistance = value
	end,
})
ESP:CreateToggle({
	Name = "Show Names",
	CurrentValue = false,
	Flag = "ShowNames",
	Callback = function(value)
		Config.NameESP = value
	end,
})
ESP:CreateSlider({
	Name = "Name Size",
	Range = {8, 32},
	Increment = 1,
	CurrentValue = 14,
	Flag = "NameSize",
	Callback = function(value)
		Config.NameSize = value
	end,
})
ESP:CreateToggle({
	Name = "Show Profile Pictures",
	CurrentValue = false,
	Flag = "ProfilePictures",
	Callback = function(value)
		Config.ProfilePictures = value
	end,
})
ESP:CreateSlider({
	Name = "Profile Size",
	Range = {20, 80},
	Increment = 2,
	CurrentValue = 40,
	Flag = "ProfileSize",
	Callback = function(value)
		Config.ProfileSize = value
	end,
})
ESP:CreateSection("Chams (Highlights)")
ESP:CreateToggle({
	Name = "Enable Chams",
	CurrentValue = false,
	Flag = "ChamsEnabled",
	Callback = function(value)
		Config.ChamsEnabled = value
	end,
})
ESP:CreateSlider({
	Name = "Chams Transparency",
	Range = {0, 1},
	Increment = 0.05,
	CurrentValue = 0.5,
	Flag = "ChamsTransparency",
	Callback = function(value)
		Config.ChamsTransparency = value
	end,
})

Extras:CreateSection("Server Disruption")
Extras:CreateDropdown({
	Name = "Lag Mode",
	Options = { "Spam", "Crash" },
	CurrentOption = { "Spam" },
	MultipleOptions = false,
	Flag = "LagMode",
	Callback = function(option)
		Config.LagMode = type(option) == "table" and option[1] or option
	end,
})
Extras:CreateToggle({
	Name = "Lag Everyone",
	CurrentValue = false,
	Flag = "LagEnabled",
	Callback = function(value)
		Config.LagEnabled = value
		if value then
			Rayfield:Notify({ Title = "Lag Started", Content = "Mode: " .. Config.LagMode, Duration = 3 })
		end
	end,
})
Extras:CreateSlider({
	Name = "Lag Intensity",
	Range = {1, 20},
	Increment = 1,
	CurrentValue = 1,
	Flag = "LagIntensity",
	Callback = function(value)
		Config.LagIntensity = value
	end,
})
Extras:CreateLabel("ts is info btw:)")
Extras:CreateLabel("Credits: pink ball, Ayden")

local mt = getrawmetatable(game)
if mt then
	local oldNamecall = mt.__namecall
	setreadonly(mt, false)
	mt.__namecall = newcclosure(function(self, ...)
		local method = getnamecallmethod()
		if Config.SilentAimEnabled and (method == "FindPartOnRay" or method == "FindPartOnRayWithIgnoreList" or method == "Raycast") then
			local target = getClosest()
			local part = target and getHitPart(target)
			if part then
				if method == "Raycast" then
					local args = { ... }
					args[2] = part.Position - args[1]
					return oldNamecall(self, unpack(args))
				end
			end
		end
		return oldNamecall(self, ...)
	end)
	setreadonly(mt, true)
end

UserInputService.JumpRequest:Connect(function()
	if Config.InfiniteJumpEnabled then
		local humanoid = getHumanoid(LocalPlayer)
		if humanoid then
			humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
		end
	end
end)

local espFolder = Instance.new("Folder")
espFolder.Name = "VyzenESP"
espFolder.Parent = game:GetService("CoreGui")

local function clearESP()
	espFolder:ClearAllChildren()
end

local function updateESP()
	clearESP()
	if not Config.NameESP and not Config.ChamsEnabled and not Config.ProfilePictures then
		return
	end
	local myRoot = getRoot(LocalPlayer)
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			local character = getCharacter(player)
			local root = getRoot(player)
			if character and root and myRoot then
				local dist = (root.Position - myRoot.Position).Magnitude
				if dist <= Config.ESPMaxDistance then
					if Config.ChamsEnabled then
						local highlight = Instance.new("Highlight")
						highlight.Adornee = character
						highlight.FillColor = Config.ChamsFillColor
						highlight.OutlineColor = Config.ChamsOutlineColor
						highlight.FillTransparency = Config.ChamsTransparency
						highlight.OutlineTransparency = 0
						highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
						highlight.Parent = espFolder
					end
					if Config.NameESP or Config.ProfilePictures then
						local billboard = Instance.new("BillboardGui")
						billboard.Name = "ProfileBillboard"
						billboard.Adornee = root
						billboard.Size = UDim2.fromOffset(120, 40)
						billboard.StudsOffset = Vector3.new(0, 3, 0)
						billboard.AlwaysOnTop = true
						billboard.Parent = espFolder
						if Config.NameESP then
							local label = Instance.new("TextLabel")
							label.BackgroundTransparency = 1
							label.Size = UDim2.fromScale(1, 1)
							label.Text = player.DisplayName
							label.TextColor3 = Config.NameColor
							label.TextSize = Config.NameSize
							label.Font = Enum.Font.GothamBold
							label.Parent = billboard
						end
						if Config.ProfilePictures then
							local image = Instance.new("ImageLabel")
							image.Name = "ProfileImage"
							image.Size = UDim2.fromOffset(Config.ProfileSize, Config.ProfileSize)
							image.BackgroundTransparency = 1
							image.Image = Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
							image.Parent = billboard
						end
					end
				end
			end
		end
	end
end

local lastLoop = 0
RunService.RenderStepped:Connect(function()
	Camera = Workspace.CurrentCamera
	if fovCircle then
		local mousePos = UserInputService:GetMouseLocation()
		fovCircle.Position = mousePos
		fovCircle.Radius = Config.FOVRadius
		fovCircle.Visible = Config.SilentAimEnabled and Config.ShowFOV
	end

	local character = LocalPlayer.Character
	local humanoid = character and character:FindFirstChildOfClass("Humanoid")
	local root = getRoot(LocalPlayer)

	if humanoid then
		if Config.WalkspeedEnabled then
			humanoid.WalkSpeed = Config.WalkspeedValue
		end
		if Config.JumpPower then
			pcall(function()
				humanoid.UseJumpPower = true
				if Config.InfiniteJumpEnabled or Config.JumpPower ~= 50 then
					humanoid.JumpPower = Config.JumpPower
				end
			end)
		end
	end

	if Config.NoclipEnabled and character then
		for _, part in ipairs(character:GetDescendants()) do
			if part:IsA("BasePart") then
				part.CanCollide = false
			end
		end
	end

	if Config.AntiFlingEnabled and root then
		if root.AssemblyLinearVelocity.Magnitude > 150 then
			root.AssemblyLinearVelocity = Vector3.zero
			root.AssemblyAngularVelocity = Vector3.zero
		end
	end

	if Config.AntiGrabEnabled and Workspace:FindFirstChild("GrabParts") then
		pcall(function()
			if GrabEvents and GrabEvents:FindFirstChild("EndGrabEarly") then
				GrabEvents.EndGrabEarly:FireServer()
			end
		end)
	end

	if Config.AntiRagdollAuto and humanoid then
		if humanoid:GetState() == Enum.HumanoidStateType.Physics or humanoid.Sit then
			humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
			humanoid.Sit = false
		end
	end

	if root and (Config.LaunchAuraEnabled or Config.TelekinesisAuraEnabled or Config.DeathAuraEnabled) then
		for _, player in ipairs(Players:GetPlayers()) do
			if player ~= LocalPlayer then
				local other = getRoot(player)
				local otherHum = getHumanoid(player)
				if other and otherHum and otherHum.Health > 0 then
					if (other.Position - root.Position).Magnitude <= Config.AuraRadius then
						if Config.DeathAuraEnabled then
							pcall(function()
								otherHum.Health = 0
							end)
						end
						if Config.LaunchAuraEnabled then
							pcall(function()
								other.AssemblyLinearVelocity = Vector3.new(0, 200, 0)
							end)
						end
						if Config.TelekinesisAuraEnabled then
							pcall(function()
								other.CFrame = root.CFrame * CFrame.new(0, 5, -8)
							end)
						end
					end
				end
			end
		end
	end

	if Config.LoopKillEnabled and Config.LoopKillTarget and tick() - lastLoop >= Config.LoopKillDelay then
		lastLoop = tick()
		local hum = getHumanoid(Config.LoopKillTarget)
		if hum then
			pcall(function()
				hum.Health = 0
			end)
		end
	end
end)

task.spawn(function()
	while task.wait(1) do
		pcall(updateESP)
	end
end)

Players.PlayerRemoving:Connect(function(player)
	if Config.KillTarget == player then
		Config.KillTarget = nil
	end
	if Config.LoopKillTarget == player then
		Config.LoopKillTarget = nil
	end
end)

Rayfield:Notify({
	Title = "Script Loaded",
	Content = "VyzenHub V1",
	Duration = 4,
	Image = 4483362458,
})

Rayfield:LoadConfiguration()
