-- FTAP Reverse
-- https://t.me/ReverseFTAP

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local DISCORD = "https://discord.gg/pRDMSfkP6K"

local Config = {
	AntiFling = false,
	Fling = false,
	Jump = false,
	ESP = false,
	ESPBoxes = true,
	ESPNames = true,
	ESPDistance = true,
	ESPHealth = true,
	ESPTracers = true,
	Power = 1,
	Hoverboard = false,
}

local PowerNames = {
	"looks legit",
	"higher that legit",
	"very strong",
	" superman ahhh fling",
	"nearly max..",
	"super duper far",
}

pcall(function()
	local old = CoreGui:FindFirstChild("ZentrixScript")
	if old then
		old:Destroy()
	end
end)

local gui = Instance.new("ScreenGui")
gui.Name = "ZentrixScript"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = CoreGui

local main = Instance.new("Frame")
main.Name = "Frame"
main.Size = UDim2.fromOffset(320, 280)
main.Position = UDim2.new(0.05, 0, 0.25, 0)
main.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Parent = gui

Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)

local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(30, 20, 50)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(12, 12, 16)),
})
gradient.Rotation = 45
gradient.Parent = main

local title = Instance.new("TextLabel")
title.BackgroundTransparency = 1
title.Size = UDim2.new(1, -16, 0, 28)
title.Position = UDim2.fromOffset(12, 8)
title.Text = "Zentrix"
title.TextColor3 = Color3.fromRGB(230, 220, 255)
title.TextSize = 20
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = main

local credit = Instance.new("TextLabel")
credit.BackgroundTransparency = 1
credit.Size = UDim2.new(1, -16, 0, 16)
credit.Position = UDim2.fromOffset(12, 34)
credit.Text = "made by CabralDev"
credit.TextColor3 = Color3.fromRGB(160, 150, 180)
credit.TextSize = 12
credit.Font = Enum.Font.Gotham
credit.TextXAlignment = Enum.TextXAlignment.Left
title.ZIndex = 2
credit.Parent = main

local function makeButton(text, y)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(1, -24, 0, 28)
	button.Position = UDim2.new(0, 12, 0, y)
	button.BackgroundColor3 = Color3.fromRGB(40, 32, 60)
	button.BorderSizePixel = 0
	button.Text = text
	button.TextColor3 = Color3.fromRGB(235, 235, 245)
	button.TextSize = 14
	button.Font = Enum.Font.Gotham
	button.Parent = main
	Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)
	return button
end

local antiBtn = makeButton("Anti-Fling: OFF", 58)
local powerBtn = makeButton("Power: 1  (looks legit)", 92)
local espBtn = makeButton("esp disabled", 126)

local hint = Instance.new("TextLabel")
hint.BackgroundTransparency = 1
hint.Size = UDim2.new(1, -16, 0, 36)
hint.Position = UDim2.fromOffset(12, 162)
hint.Text = "T: Anti-Fling  |  F: Fling  |  H: Jump  |  R: ESP  |  X: Discord"
hint.TextColor3 = Color3.fromRGB(180, 170, 200)
hint.TextSize = 11
hint.Font = Enum.Font.Gotham
hint.TextWrapped = true
hint.Parent = main

local discordBtn = Instance.new("TextButton")
discordBtn.Size = UDim2.new(1, -24, 0, 36)
discordBtn.Position = UDim2.fromOffset(12, 204)
discordBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
discordBtn.BorderSizePixel = 0
discordBtn.Text = "Discord: https://discord.gg/pRDMSfkP6K (click to copy)"
discordBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
discordBtn.TextSize = 11
discordBtn.Font = Enum.Font.Gotham
discordBtn.TextWrapped = true
discordBtn.Parent = main
Instance.new("UICorner", discordBtn).CornerRadius = UDim.new(0, 6)

local features = Instance.new("TextLabel")
features.BackgroundTransparency = 1
features.Size = UDim2.new(1, -16, 0, 32)
features.Position = UDim2.fromOffset(12, 242)
features.Text = "- perfect anti-fling\n- 6 power levels\n- advanced esp with health\n- smooth performance"
features.TextColor3 = Color3.fromRGB(140, 130, 160)
features.TextSize = 10
features.Font = Enum.Font.Gotham
features.TextXAlignment = Enum.TextXAlignment.Left
features.Parent = main

local function notify(text)
	print("[Zentrix] " .. text)
end

local function hasHoverboard()
	local character = LocalPlayer.Character
	if not character then
		return false
	end
	for _, child in ipairs(character:GetDescendants()) do
		if child.Name:lower():find("board") or child.Name:lower():find("hover") then
			return true
		end
	end
	return false
end

local function setAntiFling(on)
	if on and hasHoverboard() then
		Config.AntiFling = false
		antiBtn.Text = "Anti-Fling: OFF (Board)"
		notify("cant use anti-fling with hoverboard")
		notify("anti-fling disabled - hoverboard detected")
		return
	end
	Config.AntiFling = on
	antiBtn.Text = on and "Anti-Fling: ON" or "Anti-Fling: OFF"
	notify(on and "anti-fling enabled" or "anti-fling disabled")
end

local function setPower(level)
	Config.Power = math.clamp(level, 1, 6)
	powerBtn.Text = "Power: " .. Config.Power .. "  (" .. PowerNames[Config.Power] .. ")"
	notify("power: " .. Config.Power)
end

local function setESP(on)
	Config.ESP = on
	espBtn.Text = on and "esp enabled" or "esp disabled"
	notify(on and "esp enabled" or "esp disabled")
end

antiBtn.MouseButton1Click:Connect(function()
	setAntiFling(not Config.AntiFling)
end)
powerBtn.MouseButton1Click:Connect(function()
	setPower(Config.Power % 6 + 1)
end)
espBtn.MouseButton1Click:Connect(function()
	setESP(not Config.ESP)
end)
discordBtn.MouseButton1Click:Connect(function()
	local copy = setclipboard or (syn and syn.write_clipboard)
	if copy then
		copy(DISCORD)
		notify("discord link copied!")
		notify("copied to clipboard!")
	end
end)

local function getRoot(player)
	local character = player.Character
	if not character then
		return nil
	end
	return character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("UpperTorso") or character:FindFirstChild("Torso")
end

local function getHumanoid(player)
	local character = player.Character
	return character and character:FindFirstChildOfClass("Humanoid")
end

local flingForces = {
	function(root)
		root.AssemblyLinearVelocity = Vector3.new(0, 80 * Config.Power, 0)
	end,
	function(root)
		root.AssemblyLinearVelocity = Camera.CFrame.LookVector * (120 * Config.Power) + Vector3.new(0, 40, 0)
	end,
	function(root)
		root.AssemblyAngularVelocity = Vector3.new(0, 80 * Config.Power, 0)
		root.AssemblyLinearVelocity = Vector3.new(0, 150 * Config.Power, 0)
	end,
	function(root)
		root.CFrame = root.CFrame + Vector3.new(0, 20 * Config.Power, 0)
		root.AssemblyLinearVelocity = Vector3.new(0, 200 * Config.Power, 0)
	end,
	function(root)
		root.AssemblyLinearVelocity = Vector3.new(math.random(-1, 1), 1, math.random(-1, 1)).Unit * (250 * Config.Power)
	end,
	function(root)
		root.CFrame = CFrame.new(root.Position + Vector3.new(0, 500, 0))
	end,
}

local function flingTarget(player)
	local root = getRoot(player)
	if not root then
		return
	end
	pcall(function()
		flingForces[Config.Power](root)
	end)
	notify("flinged with " .. Config.Power .. " power")
end

local function getClosest()
	local closest, dist = nil, 40
	local myRoot = getRoot(LocalPlayer)
	if not myRoot then
		return nil
	end
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			local root = getRoot(player)
			if root then
				local d = (root.Position - myRoot.Position).Magnitude
				if d < dist then
					closest = player
					dist = d
				end
			end
		end
	end
	return closest
end

UserInputService.InputBegan:Connect(function(input, processed)
	if processed then
		return
	end
	if input.KeyCode == Enum.KeyCode.T then
		setAntiFling(not Config.AntiFling)
	elseif input.KeyCode == Enum.KeyCode.F then
		Config.Fling = true
		local target = getClosest()
		if target then
			flingTarget(target)
		end
	elseif input.KeyCode == Enum.KeyCode.H then
		Config.Jump = true
		local humanoid = getHumanoid(LocalPlayer)
		if humanoid then
			humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
			notify("jump activated")
		end
	elseif input.KeyCode == Enum.KeyCode.R then
		setESP(not Config.ESP)
	elseif input.KeyCode == Enum.KeyCode.X then
		local copy = setclipboard or (syn and syn.write_clipboard)
		if copy then
			copy(DISCORD)
			notify("discord link copied!")
		end
	elseif input.KeyCode == Enum.KeyCode.Nine then
		setPower(Config.Power % 6 + 1)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.F then
		Config.Fling = false
	end
end)

local espFolder = Instance.new("Folder")
espFolder.Name = "ESP_"
espFolder.Parent = gui

local function clearESP()
	espFolder:ClearAllChildren()
end

local function drawESP()
	clearESP()
	if not Config.ESP then
		return
	end
	local myRoot = getRoot(LocalPlayer)
	Camera = Workspace.CurrentCamera
	if not Camera then
		return
	end
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			local character = player.Character
			local root = getRoot(player)
			local humanoid = getHumanoid(player)
			if character and root and humanoid then
				local screen, onScreen = Camera:WorldToViewportPoint(root.Position)
				if onScreen then
					local dist = myRoot and math.floor((root.Position - myRoot.Position).Magnitude) or 0
					if Config.ESPBoxes then
						local box = Instance.new("SelectionBox")
						box.Name = "BoundingBox"
						box.Adornee = character
						box.LineThickness = 0.03
						box.Color3 = Color3.fromRGB(170, 80, 255)
						box.Parent = espFolder
					end
					if Config.ESPNames or Config.ESPDistance or Config.ESPHealth then
						local billboard = Instance.new("BillboardGui")
						billboard.Name = "billboard"
						billboard.Adornee = root
						billboard.Size = UDim2.fromOffset(140, 48)
						billboard.StudsOffset = Vector3.new(0, 3.2, 0)
						billboard.AlwaysOnTop = true
						billboard.Parent = espFolder
						if Config.ESPNames then
							local nameLabel = Instance.new("TextLabel")
							nameLabel.Name = "nameLabel"
							nameLabel.BackgroundTransparency = 1
							nameLabel.Size = UDim2.new(1, 0, 0, 16)
							nameLabel.Text = player.DisplayName
							nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
							nameLabel.TextStrokeTransparency = 0.4
							nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
							nameLabel.Font = Enum.Font.GothamBold
							nameLabel.TextSize = 13
							nameLabel.Parent = billboard
						end
						if Config.ESPDistance then
							local distanceLabel = Instance.new("TextLabel")
							distanceLabel.Name = "distanceLabel"
							distanceLabel.BackgroundTransparency = 1
							distanceLabel.Size = UDim2.new(1, 0, 0, 14)
							distanceLabel.Position = UDim2.fromOffset(0, 16)
							distanceLabel.Text = dist .. "m"
							distanceLabel.TextColor3 = Color3.fromRGB(200, 200, 220)
							distanceLabel.Font = Enum.Font.Gotham
							distanceLabel.TextSize = 11
							distanceLabel.Parent = billboard
						end
						if Config.ESPHealth then
							local hp = math.floor(humanoid.Health)
							local maxHp = math.max(humanoid.MaxHealth, 1)
							local healthLabel = Instance.new("TextLabel")
							healthLabel.Name = "healthLabel"
							healthLabel.BackgroundTransparency = 1
							healthLabel.Size = UDim2.new(1, 0, 0, 14)
							healthLabel.Position = UDim2.fromOffset(0, 30)
							healthLabel.Text = hp .. " HP"
							healthLabel.TextColor3 = Color3.fromRGB(80, 255, 120)
							healthLabel.Font = Enum.Font.Gotham
							healthLabel.TextSize = 11
							healthLabel.Parent = billboard
							local healthBarBg = Instance.new("Frame")
							healthBarBg.Name = "healthBarBg"
							healthBarBg.Size = UDim2.new(1, -10, 0, 3)
							healthBarBg.Position = UDim2.new(0, 5, 1, -4)
							healthBarBg.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
							healthBarBg.BorderSizePixel = 0
							healthBarBg.Parent = billboard
							local healthBar = Instance.new("Frame")
							healthBar.Name = "healthBar"
							healthBar.Size = UDim2.new(math.clamp(hp / maxHp, 0, 1), 0, 1, 0)
							healthBar.BackgroundColor3 = Color3.fromRGB(80, 220, 100)
							healthBar.BorderSizePixel = 0
							healthBar.Parent = healthBarBg
						end
					end
					if Config.ESPTracers then
						local attach0 = Instance.new("Attachment")
						attach0.Name = "CameraAttachment"
						attach0.Parent = Camera
						local attach1 = Instance.new("Attachment")
						attach1.Name = "TracerAttachment"
						attach1.Parent = root
						local beam = Instance.new("Beam")
						beam.Name = "tracerBeam"
						beam.Attachment0 = attach0
						beam.Attachment1 = attach1
						beam.Width0 = 0.05
						beam.Width1 = 0.05
						beam.FaceCamera = true
						beam.Color = ColorSequence.new(Color3.fromRGB(170, 80, 255))
						beam.Parent = espFolder
					end
				end
			end
		end
	end
end

RunService.Heartbeat:Connect(function()
	if Config.AntiFling then
		if hasHoverboard() then
			setAntiFling(false)
			return
		end
		local root = getRoot(LocalPlayer)
		if root and root.AssemblyLinearVelocity.Magnitude > 120 then
			root.AssemblyLinearVelocity = Vector3.zero
			root.AssemblyAngularVelocity = Vector3.zero
		end
		local humanoid = getHumanoid(LocalPlayer)
		if humanoid then
			if humanoid.PlatformStand or humanoid.Sit then
				humanoid.PlatformStand = false
				humanoid.Sit = false
				humanoid.AutoRotate = true
				humanoid:ChangeState(Enum.HumanoidStateType.Running)
			end
		end
	end
end)

RunService.RenderStepped:Connect(function()
	pcall(drawESP)
end)

LocalPlayer.CharacterAdded:Connect(function()
	task.wait(0.5)
	if Config.AntiFling and hasHoverboard() then
		setAntiFling(false)
	end
end)

pcall(function()
	main.BackgroundTransparency = 1
	TweenService:Create(main, TweenInfo.new(0.45, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
		BackgroundTransparency = 0,
	}):Play()
end)

print("[Zentrix] loaded")
