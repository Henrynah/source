
if workspace:FindFirstChild("TeleportToGround") then
	workspace:FindFirstChild("TeleportToGround").Parent = game.ReplicatedStorage:FindFirstChild("GameCorrectionEvents")
end

---/// -- CUSTOM FUNCTIONS
getgenv().isnetworkowner = function(part)
    if part.ReceiveAge == 0 and not part.Anchored then
        return true
    else
        return false
    end
end
---\\\ -- CUSTOM FUNCTIONS

local Players = game.Players
local Camera = game.Workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local ToyFolder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
local Mouse = LocalPlayer:GetMouse()
local ReplicatedStorage = game.ReplicatedStorage
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

--//
SelectedManipulation = "Explosion"
ToysToSpawn = 10
ShouldNetOwnDismounted = false
DismountToys = false
local SelectedToy = "Roller"
LagServerUsingPlayers = false
LagServerUsingMap = false
local ExtendGrabToggle = false
local ExtendedDistance = 10
local StrongerThrowToggle = false
local ThrowPower = 400
local KickGrabToggle = false
local KillGrabToggle = false
local NetOwnGrabToggle = false
local FlingGrabToggle = false
local KeepFlingGrabVelocityToggle = false
local NoclipGrabIgnorePlayers = true
local NoclipGrabToggle = false
ControlGrabPropertiesToggle = false
PrimaryAxisOnly = false
GrabResponsiveness = 40
GrabMaxForce = 600000
LagServer = false
GrabsPerSecond = 100
local SoftAnchor = false
local AntiBlobmanBring = false
local AntiLag = false
local AntiExplosion = false
local AntiAttacker = false
local BlackholeToggle = false
local BlackholeSpinningSpeed = 10
local BlackholeRingRadius = 50
ControlNPC = false
FollowingNPCs = false
NPCsWalkSpeed = 16
NPCsJumpPower = 50
FlingingNPCs = false
LoopTeleportingNPCs = false
TeleportInterval = 0.05
BangingNPCs = false
local CFrameSpeedAmmount = 1
local JumpPowerAmmount = 24
local GravityToggle = false
local BlobTarget = {}
local KillTargets = {}
local BlobmanLocation
local KickAura = false
local KickAuraType = "Float"
local KillAura = false
local FlingAura = false
local SpinAura = false
local SpeedAura = false
local JumpAura = false
local NetOwnAura = false
local DeleteAura = false
local SpeedAuraAmmount = 50
local JumpAuraAmmount = 100
FriendListCheck = true
FriendListAutoUpdate = false
FriendListUpdateSpeed = 60
WhitelistCheck = true
RemoveWhitelistedOnLeave = false
local BlobmanGrabSpeed = 0.025
local TimeDelay = 0.20
local AutoPair = true
local PianoSpamDelay = 0
local PianoTeleportDelay = 0.2
local DetectLag = true
local Alarm = true
local BreakAllBlobmen = false
local BreakBlobmenDelay = 0
local InvisLine = false
local DelayBeforeTeleportingToNextTarget = 0.2
local LoopKillTargetToggle = false
local LoopKickTargetToggle = false
local TargetKickType = "Float"
local TeleportBackAntiGrab = true
local AimbotFOV = 150
local ExtendMaxValue = 10
local ExtendIncrement = 2
local ExtendToDistanceOnGrab = false
local AntiAttackerType = "Kill"
local AntiAttackerKickType = "Float"
local RemovePlayerWhenLeaveTarget = true
local RemovePlayerWhenLeaveBlobman = true
local JunkData = ""

TargetPlayerList = {}
local TargetSelectedTarget
local TargetSelectedPlayer
BlobmanPlayerList = {}
local BlobmanSelectedTarget
local BlobmanSelectedPlayer
ExplosionPlayerList = {}
local ExplosionTarget
WhitelistPlayerList = {}
local WhitelistSelectedPlayer
local WhitelistSelectedWhitelisted
NPCsPlayerList = {}
local NPCsTarget
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--\\

local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

local OrionLib = {
	Elements = {},
	ThemeObjects = {},
	Connections = {},
	Flags = {},
	Themes = {
		Default = {
			Main = Color3.fromRGB(35, 15, 20),
			Second = Color3.fromRGB(25, 10, 15),
			Stroke = Color3.fromRGB(100, 40, 30),
			Divider = Color3.fromRGB(75, 30, 15),
			Text = Color3.fromRGB(255, 250, 200),
			TextDark = Color3.fromRGB(255, 205, 200)
		}
	},
	SelectedTheme = "Default",
	Folder = nil,
	SaveCfg = false
}

--Feather Icons https://github.com/evoincorp/lucideblox/tree/master/src/modules/util - Created by 7kayoh
local Icons = {}

local Success, Response = pcall(function()
	Icons = HttpService:JSONDecode(game:HttpGetAsync("https://raw.githubusercontent.com/evoincorp/lucideblox/master/src/modules/util/icons.json")).icons
end)

if not Success then
	warn("\nOrion Library - Failed to load Feather Icons. Error code: " .. Response .. "\n")
end	

local function GetIcon(IconName)
	if Icons[IconName] ~= nil then
		return Icons[IconName]
	else
		return nil
	end
end   

local Orion = Instance.new("ScreenGui")
Orion.Name = "Orion"
if syn then
	syn.protect_gui(Orion)
	Orion.Parent = game.CoreGui
else
	Orion.Parent = gethui() or game.CoreGui
end

if gethui then
	for _, Interface in ipairs(gethui():GetChildren()) do
		if Interface.Name == Orion.Name and Interface ~= Orion then
			Interface:Destroy()
		end
	end
else
	for _, Interface in ipairs(game.CoreGui:GetChildren()) do
		if Interface.Name == Orion.Name and Interface ~= Orion then
			Interface:Destroy()
		end
	end
end

function OrionLib:IsRunning()
	if gethui then
		return Orion.Parent == gethui()
	else
		return Orion.Parent == game:GetService("CoreGui")
	end

end

local function AddConnection(Signal, Function)
	if (not OrionLib:IsRunning()) then
		return
	end
	local SignalConnect = Signal:Connect(Function)
	table.insert(OrionLib.Connections, SignalConnect)
	return SignalConnect
end

task.spawn(function()
	while (OrionLib:IsRunning()) do
		wait()
	end

	for _, Connection in next, OrionLib.Connections do
		Connection:Disconnect()
	end
end)

local function MakeDraggable(DragPoint, Main)
	pcall(function()
		local Dragging, DragInput, MousePos, FramePos = false
		AddConnection(DragPoint.InputBegan, function(Input)
			if Input.UserInputType == Enum.UserInputType.MouseButton1 then
				Dragging = true
				MousePos = Input.Position
				FramePos = Main.Position

				Input.Changed:Connect(function()
					if Input.UserInputState == Enum.UserInputState.End then
						Dragging = false
					end
				end)
			end
		end)
		AddConnection(DragPoint.InputChanged, function(Input)
			if Input.UserInputType == Enum.UserInputType.MouseMovement then
				DragInput = Input
			end
		end)
		AddConnection(UserInputService.InputChanged, function(Input)
			if Input == DragInput and Dragging then
				local Delta = Input.Position - MousePos
				TweenService:Create(Main, TweenInfo.new(0.15, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Position  = UDim2.new(FramePos.X.Scale,FramePos.X.Offset + Delta.X, FramePos.Y.Scale, FramePos.Y.Offset + Delta.Y)}):Play()
			end
		end)
	end)
end    

local function Create(Name, Properties, Children)
	local Object = Instance.new(Name)
	for i, v in next, Properties or {} do
		Object[i] = v
	end
	for i, v in next, Children or {} do
		v.Parent = Object
	end
	return Object
end

local function CreateElement(ElementName, ElementFunction)
	OrionLib.Elements[ElementName] = function(...)
		return ElementFunction(...)
	end
end

local function MakeElement(ElementName, ...)
	local NewElement = OrionLib.Elements[ElementName](...)
	return NewElement
end

local function SetProps(Element, Props)
	table.foreach(Props, function(Property, Value)
		Element[Property] = Value
	end)
	return Element
end

local function SetChildren(Element, Children)
	table.foreach(Children, function(_, Child)
		Child.Parent = Element
	end)
	return Element
end

local function Round(Number, Factor)
	local Result = math.floor(Number/Factor + (math.sign(Number) * 0.5)) * Factor
	if Result < 0 then Result = Result + Factor end
	return Result
end

local function ReturnProperty(Object)
	if Object:IsA("Frame") or Object:IsA("TextButton") then
		return "BackgroundColor3"
	end 
	if Object:IsA("ScrollingFrame") then
		return "ScrollBarImageColor3"
	end 
	if Object:IsA("UIStroke") then
		return "Color"
	end 
	if Object:IsA("TextLabel") or Object:IsA("TextBox") then
		return "TextColor3"
	end   
	if Object:IsA("ImageLabel") or Object:IsA("ImageButton") then
		return "ImageColor3"
	end   
end

local function AddThemeObject(Object, Type)
	if not OrionLib.ThemeObjects[Type] then
		OrionLib.ThemeObjects[Type] = {}
	end    
	table.insert(OrionLib.ThemeObjects[Type], Object)
	Object[ReturnProperty(Object)] = OrionLib.Themes[OrionLib.SelectedTheme][Type]
	return Object
end    

local function SetTheme()
	for Name, Type in pairs(OrionLib.ThemeObjects) do
		for _, Object in pairs(Type) do
			Object[ReturnProperty(Object)] = OrionLib.Themes[OrionLib.SelectedTheme][Name]
		end    
	end    
end

local function PackColor(Color)
	return {R = Color.R * 255, G = Color.G * 255, B = Color.B * 255}
end    

local function UnpackColor(Color)
	return Color3.fromRGB(Color.R, Color.G, Color.B)
end

local function LoadCfg(Config)
	local Data = HttpService:JSONDecode(Config)
	table.foreach(Data, function(a,b)
		if OrionLib.Flags[a] then
			spawn(function() 
				if OrionLib.Flags[a].Type == "Colorpicker" then
					OrionLib.Flags[a]:Set(UnpackColor(b))
				else
					OrionLib.Flags[a]:Set(b)
				end    
			end)
		else
			warn("Orion Library Config Loader - Could not find ", a ,b)
		end
	end)
end

local function SaveCfg(Name)
	local Data = {}
	for i,v in pairs(OrionLib.Flags) do
		if v.Save then
			if v.Type == "Colorpicker" then
				Data[i] = PackColor(v.Value)
			else
				Data[i] = v.Value
			end
		end	
	end
	writefile(OrionLib.Folder .. "/" .. Name .. ".txt", tostring(HttpService:JSONEncode(Data)))
end

local WhitelistedMouse = {Enum.UserInputType.MouseButton1, Enum.UserInputType.MouseButton2,Enum.UserInputType.MouseButton3}
local BlacklistedKeys = {Enum.KeyCode.Unknown,Enum.KeyCode.W,Enum.KeyCode.A,Enum.KeyCode.S,Enum.KeyCode.D,Enum.KeyCode.Up,Enum.KeyCode.Left,Enum.KeyCode.Down,Enum.KeyCode.Right,Enum.KeyCode.Slash,Enum.KeyCode.Tab,Enum.KeyCode.Backspace,Enum.KeyCode.Escape}

local function CheckKey(Table, Key)
	for _, v in next, Table do
		if v == Key then
			return true
		end
	end
end

CreateElement("Corner", function(Scale, Offset)
	local Corner = Create("UICorner", {
		CornerRadius = UDim.new(Scale or 0, Offset or 10)
	})
	return Corner
end)

CreateElement("Stroke", function(Color, Thickness)
	local Stroke = Create("UIStroke", {
		Color = Color or Color3.fromRGB(255, 255, 255),
		Thickness = Thickness or 1
	})
	return Stroke
end)

CreateElement("List", function(Scale, Offset)
	local List = Create("UIListLayout", {
		SortOrder = Enum.SortOrder.LayoutOrder,
		Padding = UDim.new(Scale or 0, Offset or 0)
	})
	return List
end)

CreateElement("Padding", function(Bottom, Left, Right, Top)
	local Padding = Create("UIPadding", {
		PaddingBottom = UDim.new(0, Bottom or 4),
		PaddingLeft = UDim.new(0, Left or 4),
		PaddingRight = UDim.new(0, Right or 4),
		PaddingTop = UDim.new(0, Top or 4)
	})
	return Padding
end)

CreateElement("TFrame", function()
	local TFrame = Create("Frame", {
		BackgroundTransparency = 1
	})
	return TFrame
end)

CreateElement("Frame", function(Color)
	local Frame = Create("Frame", {
		BackgroundColor3 = Color or Color3.fromRGB(255, 255, 255),
		BorderSizePixel = 0
	})
	return Frame
end)

CreateElement("RoundFrame", function(Color, Scale, Offset)
	local Frame = Create("Frame", {
		BackgroundColor3 = Color or Color3.fromRGB(255, 255, 255),
		BorderSizePixel = 0
	}, {
		Create("UICorner", {
			CornerRadius = UDim.new(Scale, Offset)
		})
	})
	return Frame
end)

CreateElement("Button", function()
	local Button = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundTransparency = 1,
		BorderSizePixel = 0
	})
	return Button
end)

CreateElement("ScrollFrame", function(Color, Width)
	local ScrollFrame = Create("ScrollingFrame", {
		BackgroundTransparency = 1,
		MidImage = "rbxassetid://7445543667",
		BottomImage = "rbxassetid://7445543667",
		TopImage = "rbxassetid://7445543667",
		ScrollBarImageColor3 = Color,
		BorderSizePixel = 0,
		ScrollBarThickness = Width,
		CanvasSize = UDim2.new(0, 0, 0, 0)
	})
	return ScrollFrame
end)

CreateElement("Image", function(ImageID)
	local ImageNew = Create("ImageLabel", {
		Image = ImageID,
		BackgroundTransparency = 1
	})

	if GetIcon(ImageID) ~= nil then
		ImageNew.Image = GetIcon(ImageID)
	end	

	return ImageNew
end)

CreateElement("ImageButton", function(ImageID)
	local Image = Create("ImageButton", {
		Image = ImageID,
		BackgroundTransparency = 1
	})
	return Image
end)

CreateElement("Label", function(Text, TextSize, Transparency)
	local Label = Create("TextLabel", {
		Text = Text or "",
		TextColor3 = Color3.fromRGB(240, 240, 240),
		TextTransparency = Transparency or 0,
		TextSize = TextSize or 15,
		Font = Enum.Font.Gotham,
		RichText = true,
		BackgroundTransparency = 1,
		TextXAlignment = Enum.TextXAlignment.Left
	})
	return Label
end)

local NotificationHolder = SetProps(SetChildren(MakeElement("TFrame"), {
	SetProps(MakeElement("List"), {
		HorizontalAlignment = Enum.HorizontalAlignment.Center,
		SortOrder = Enum.SortOrder.LayoutOrder,
		VerticalAlignment = Enum.VerticalAlignment.Bottom,
		Padding = UDim.new(0, 5)
	})
}), {
	Position = UDim2.new(1, -25, 1, -25),
	Size = UDim2.new(0, 300, 1, -25),
	AnchorPoint = Vector2.new(1, 1),
	Parent = Orion
})

function OrionLib:MakeNotification(NotificationConfig)
	spawn(function()
		NotificationConfig.Name = NotificationConfig.Name or "Notification"
		NotificationConfig.Content = NotificationConfig.Content or "Test"
		NotificationConfig.Image = NotificationConfig.Image or "rbxassetid://4384403532"
		NotificationConfig.Time = NotificationConfig.Time or 15

		local NotificationParent = SetProps(MakeElement("TFrame"), {
			Size = UDim2.new(1, 0, 0, 0),
			AutomaticSize = Enum.AutomaticSize.Y,
			Parent = NotificationHolder
		})

		local NotificationFrame = SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(35, 15, 20), 0, 10), {
			Parent = NotificationParent, 
			Size = UDim2.new(1, 0, 0, 0),
			Position = UDim2.new(1, -55, 0, 0),
			BackgroundTransparency = 0,
			AutomaticSize = Enum.AutomaticSize.Y
		}), {
			MakeElement("Stroke", Color3.fromRGB(100, 40, 30), 1.2),
			MakeElement("Padding", 12, 12, 12, 12),
			SetProps(MakeElement("Image", NotificationConfig.Image), {
				Size = UDim2.new(0, 20, 0, 20),
				ImageColor3 = Color3.fromRGB(255, 250, 200),
				Name = "Icon"
			}),
			SetProps(MakeElement("Label", NotificationConfig.Name, 15), {
				Size = UDim2.new(1, -30, 0, 20),
				Position = UDim2.new(0, 30, 0, 0),
				Font = Enum.Font.GothamBold,
				Name = "Title"
			}),
			SetProps(MakeElement("Label", NotificationConfig.Content, 14), {
				Size = UDim2.new(1, 0, 0, 0),
				Position = UDim2.new(0, 0, 0, 25),
				Font = Enum.Font.GothamSemibold,
				Name = "Content",
				AutomaticSize = Enum.AutomaticSize.Y,
				TextColor3 = Color3.fromRGB(255, 250, 200),
				TextWrapped = true
			})
		})

		TweenService:Create(NotificationFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quint), {Position = UDim2.new(0, 0, 0, 0)}):Play()

		wait(NotificationConfig.Time - 0.88)
		TweenService:Create(NotificationFrame.Icon, TweenInfo.new(0.4, Enum.EasingStyle.Quint), {ImageTransparency = 1}):Play()
		TweenService:Create(NotificationFrame, TweenInfo.new(0.8, Enum.EasingStyle.Quint), {BackgroundTransparency = 0.6}):Play()
		wait(0.3)
		TweenService:Create(NotificationFrame.UIStroke, TweenInfo.new(0.6, Enum.EasingStyle.Quint), {Transparency = 0.9}):Play()
		TweenService:Create(NotificationFrame.Title, TweenInfo.new(0.6, Enum.EasingStyle.Quint), {TextTransparency = 0.4}):Play()
		TweenService:Create(NotificationFrame.Content, TweenInfo.new(0.6, Enum.EasingStyle.Quint), {TextTransparency = 0.5}):Play()
		wait(0.05)

		NotificationFrame:TweenPosition(UDim2.new(1, 20, 0, 0),'In','Quint',0.8,true)
		wait(1.35)
		NotificationFrame:Destroy()
	end)
end    

function OrionLib:Init()
	if OrionLib.SaveCfg then	
		pcall(function()
			if isfile(OrionLib.Folder .. "/" .. game.GameId .. ".txt") then
				LoadCfg(readfile(OrionLib.Folder .. "/" .. game.GameId .. ".txt"))
				OrionLib:MakeNotification({
					Name = "Configuration",
					Content = "Auto-loaded configuration for the game " .. game.GameId .. ".",
					Time = 5
				})
			end
		end)		
	end	
end	

function OrionLib:MakeWindow(WindowConfig)
	local FirstTab = true
	local Minimized = false
	local Loaded = false
	local UIHidden = false

	WindowConfig = WindowConfig or {}
	WindowConfig.Name = WindowConfig.Name or "Orion Library"
	WindowConfig.ConfigFolder = WindowConfig.ConfigFolder or WindowConfig.Name
	WindowConfig.SaveConfig = WindowConfig.SaveConfig or false
	WindowConfig.HidePremium = WindowConfig.HidePremium or false
	if WindowConfig.IntroEnabled == nil then
		WindowConfig.IntroEnabled = true
	end
	WindowConfig.IntroText = WindowConfig.IntroText or "Orion Library"
	WindowConfig.CloseCallback = WindowConfig.CloseCallback or function() end
	WindowConfig.ShowIcon = WindowConfig.ShowIcon or false
	WindowConfig.Icon = WindowConfig.Icon or "rbxassetid://8834748103"
	WindowConfig.IntroIcon = WindowConfig.IntroIcon or "rbxassetid://8834748103"
	OrionLib.Folder = WindowConfig.ConfigFolder
	OrionLib.SaveCfg = WindowConfig.SaveConfig

	if WindowConfig.SaveConfig then
		if not isfolder(WindowConfig.ConfigFolder) then
			makefolder(WindowConfig.ConfigFolder)
		end	
	end

	local TabHolder = AddThemeObject(SetChildren(SetProps(MakeElement("ScrollFrame", Color3.fromRGB(255, 255, 255), 4), {
		Size = UDim2.new(1, 0, 1, -50)
	}), {
		MakeElement("List"),
		MakeElement("Padding", 8, 0, 0, 8)
	}), "Divider")

	AddConnection(TabHolder.UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"), function()
		TabHolder.CanvasSize = UDim2.new(0, 0, 0, TabHolder.UIListLayout.AbsoluteContentSize.Y + 16)
	end)

	local CloseBtn = SetChildren(SetProps(MakeElement("Button"), {
		Size = UDim2.new(0.5, 0, 1, 0),
		Position = UDim2.new(0.5, 0, 0, 0),
		BackgroundTransparency = 1
	}), {
		AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://7072725342"), {
			Position = UDim2.new(0, 9, 0, 6),
			Size = UDim2.new(0, 18, 0, 18)
		}), "Text")
	})

	local MinimizeBtn = SetChildren(SetProps(MakeElement("Button"), {
		Size = UDim2.new(0.5, 0, 1, 0),
		BackgroundTransparency = 1
	}), {
		AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://7072719338"), {
			Position = UDim2.new(0, 9, 0, 6),
			Size = UDim2.new(0, 18, 0, 18),
			Name = "Ico"
		}), "Text")
	})

	local DragPoint = SetProps(MakeElement("TFrame"), {
		Size = UDim2.new(1, 0, 0, 50)
	})

	local WindowStuff = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 10), {
		Size = UDim2.new(0, 150, 1, -50),
		Position = UDim2.new(0, 0, 0, 50)
	}), {
		AddThemeObject(SetProps(MakeElement("Frame"), {
			Size = UDim2.new(1, 0, 0, 10),
			Position = UDim2.new(0, 0, 0, 0)
		}), "Second"), 
		AddThemeObject(SetProps(MakeElement("Frame"), {
			Size = UDim2.new(0, 10, 1, 0),
			Position = UDim2.new(1, -10, 0, 0)
		}), "Second"), 
		AddThemeObject(SetProps(MakeElement("Frame"), {
			Size = UDim2.new(0, 1, 1, 0),
			Position = UDim2.new(1, -1, 0, 0)
		}), "Stroke"), 
		TabHolder,
		SetChildren(SetProps(MakeElement("TFrame"), {
			Size = UDim2.new(1, 0, 0, 50),
			Position = UDim2.new(0, 0, 1, -50)
		}), {
			AddThemeObject(SetProps(MakeElement("Frame"), {
				Size = UDim2.new(1, 0, 0, 1)
			}), "Stroke"), 
			AddThemeObject(SetChildren(SetProps(MakeElement("Frame"), {
				AnchorPoint = Vector2.new(0, 0.5),
				Size = UDim2.new(0, 32, 0, 32),
				Position = UDim2.new(0, 10, 0.5, 0)
			}), {
				SetProps(MakeElement("Image", "https://www.roblox.com/headshot-thumbnail/image?userId=".. LocalPlayer.UserId .."&width=420&height=420&format=png"), {
					Size = UDim2.new(1, 0, 1, 0)
				}),
				AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://4031889928"), {
					Size = UDim2.new(1, 0, 1, 0),
				}), "Second"),
				MakeElement("Corner", 1)
			}), "Divider"),
			SetChildren(SetProps(MakeElement("TFrame"), {
				AnchorPoint = Vector2.new(0, 0.5),
				Size = UDim2.new(0, 32, 0, 32),
				Position = UDim2.new(0, 10, 0.5, 0)
			}), {
				AddThemeObject(MakeElement("Stroke"), "Stroke"),
				MakeElement("Corner", 1)
			}),
			AddThemeObject(SetProps(MakeElement("Label", LocalPlayer.DisplayName, WindowConfig.HidePremium and 14 or 13), {
				Size = UDim2.new(1, -60, 0, 13),
				Position = WindowConfig.HidePremium and UDim2.new(0, 50, 0, 19) or UDim2.new(0, 50, 0, 12),
				Font = Enum.Font.GothamBold,
				ClipsDescendants = true
			}), "Text"),
			AddThemeObject(SetProps(MakeElement("Label", "", 12), {
				Size = UDim2.new(1, -60, 0, 12),
				Position = UDim2.new(0, 50, 1, -25),
				Visible = not WindowConfig.HidePremium
			}), "TextDark")
		}),
	}), "Second")

	local WindowName = AddThemeObject(SetProps(MakeElement("Label", WindowConfig.Name, 14), {
		Size = UDim2.new(1, -30, 2, 0),
		Position = UDim2.new(0, 25, 0, -24),
		Font = Enum.Font.GothamBlack,
		TextSize = 20
	}), "Text")

	local WindowTopBarLine = AddThemeObject(SetProps(MakeElement("Frame"), {
		Size = UDim2.new(1, 0, 0, 1),
		Position = UDim2.new(0, 0, 1, -1)
	}), "Stroke")

	local MainWindow = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 10), {
		Parent = Orion,
		Position = UDim2.new(0.5, -307, 0.5, -172),
		Size = UDim2.new(0, 615, 0, 344),
		ClipsDescendants = true
	}), {
		--SetProps(MakeElement("Image", "rbxassetid://3523728077"), {
		--	AnchorPoint = Vector2.new(0.5, 0.5),
		--	Position = UDim2.new(0.5, 0, 0.5, 0),
		--	Size = UDim2.new(1, 80, 1, 320),
		--	ImageColor3 = Color3.fromRGB(33, 33, 33),
		--	ImageTransparency = 0.7
		--}),
		SetChildren(SetProps(MakeElement("TFrame"), {
			Size = UDim2.new(1, 0, 0, 50),
			Name = "TopBar"
		}), {
			WindowName,
			WindowTopBarLine,
			AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 7), {
				Size = UDim2.new(0, 70, 0, 30),
				Position = UDim2.new(1, -90, 0, 10)
			}), {
				AddThemeObject(MakeElement("Stroke"), "Stroke"),
				AddThemeObject(SetProps(MakeElement("Frame"), {
					Size = UDim2.new(0, 1, 1, 0),
					Position = UDim2.new(0.5, 0, 0, 0)
				}), "Stroke"), 
				CloseBtn,
				MinimizeBtn
			}), "Second"), 
		}),
		DragPoint,
		WindowStuff
	}), "Main")

	if WindowConfig.ShowIcon then
		WindowName.Position = UDim2.new(0, 50, 0, -24)
		local WindowIcon = SetProps(MakeElement("Image", WindowConfig.Icon), {
			Size = UDim2.new(0, 20, 0, 20),
			Position = UDim2.new(0, 25, 0, 15)
		})
		WindowIcon.Parent = MainWindow.TopBar
	end	

	MakeDraggable(DragPoint, MainWindow)

	AddConnection(CloseBtn.MouseButton1Up, function()
		MainWindow.Visible = false
		UIHidden = true
		OrionLib:MakeNotification({
			Name = "Interface Hidden",
			Content = "Tap RightShift to reopen the interface",
			Time = 5
		})
		WindowConfig.CloseCallback()
	end)

	AddConnection(UserInputService.InputBegan, function(Input)
		if Input.KeyCode == Enum.KeyCode.RightShift and UIHidden then
			MainWindow.Visible = true
		end
	end)

	AddConnection(MinimizeBtn.MouseButton1Up, function()
		if Minimized then
			TweenService:Create(MainWindow, TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Size = UDim2.new(0, 615, 0, 344)}):Play()
			MinimizeBtn.Ico.Image = "rbxassetid://7072719338"
			wait(.02)
			MainWindow.ClipsDescendants = false
			WindowStuff.Visible = true
			WindowTopBarLine.Visible = true
		else
			MainWindow.ClipsDescendants = true
			WindowTopBarLine.Visible = false
			MinimizeBtn.Ico.Image = "rbxassetid://7072720870"

			TweenService:Create(MainWindow, TweenInfo.new(0.5, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Size = UDim2.new(0, WindowName.TextBounds.X + 140, 0, 50)}):Play()
			wait(0.1)
			WindowStuff.Visible = false	
		end
		Minimized = not Minimized    
	end)

	local function LoadSequence()
		MainWindow.Visible = false
		local LoadSequenceLogo = SetProps(MakeElement("Image", WindowConfig.IntroIcon), {
			Parent = Orion,
			AnchorPoint = Vector2.new(0.5, 0.5),
			Position = UDim2.new(0.5, 0, 0.4, 0),
			Size = UDim2.new(0, 28, 0, 28),
			ImageColor3 = Color3.fromRGB(255, 255, 255),
			ImageTransparency = 1
		})

		local LoadSequenceText = SetProps(MakeElement("Label", WindowConfig.IntroText, 14), {
			Parent = Orion,
			Size = UDim2.new(1, 0, 1, 0),
			AnchorPoint = Vector2.new(0.5, 0.5),
			Position = UDim2.new(0.5, 19, 0.5, 0),
			TextXAlignment = Enum.TextXAlignment.Center,
			Font = Enum.Font.GothamBold,
			TextTransparency = 1
		})

		TweenService:Create(LoadSequenceLogo, TweenInfo.new(.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {ImageTransparency = 0, Position = UDim2.new(0.5, 0, 0.5, 0)}):Play()
		wait(0.8)
		TweenService:Create(LoadSequenceLogo, TweenInfo.new(.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, -(LoadSequenceText.TextBounds.X/2), 0.5, 0)}):Play()
		wait(0.3)
		TweenService:Create(LoadSequenceText, TweenInfo.new(.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {TextTransparency = 0}):Play()
		wait(2)
		TweenService:Create(LoadSequenceText, TweenInfo.new(.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {TextTransparency = 1}):Play()
		MainWindow.Visible = true
		LoadSequenceLogo:Destroy()
		LoadSequenceText:Destroy()
	end 

	if WindowConfig.IntroEnabled then
		LoadSequence()
	end	

	local TabFunction = {}
	function TabFunction:MakeTab(TabConfig)
		TabConfig = TabConfig or {}
		TabConfig.Name = TabConfig.Name or "Tab"
		TabConfig.Icon = TabConfig.Icon or ""
		TabConfig.PremiumOnly = TabConfig.PremiumOnly or false

		local TabFrame = SetChildren(SetProps(MakeElement("Button"), {
			Size = UDim2.new(1, 0, 0, 30),
			Parent = TabHolder
		}), {
			AddThemeObject(SetProps(MakeElement("Image", TabConfig.Icon), {
				AnchorPoint = Vector2.new(0, 0.5),
				Size = UDim2.new(0, 18, 0, 18),
				Position = UDim2.new(0, 10, 0.5, 0),
				ImageTransparency = 0.5,
				ImageColor3 = Color3.fromRGB(255, 250, 200),
				Name = "Ico"
			}), "Text"),
			AddThemeObject(SetProps(MakeElement("Label", TabConfig.Name, 14), {
				Size = UDim2.new(1, -35, 1, 0),
				Position = UDim2.new(0, 35, 0, 0),
				Font = Enum.Font.GothamSemibold,
				TextTransparency = 0.5,
				TextColor3 = Color3.fromRGB(255, 250, 200),
				Name = "Title"
			}), "Text")
		})

		if GetIcon(TabConfig.Icon) ~= nil then
			TabFrame.Ico.Image = GetIcon(TabConfig.Icon)
		end	

		local Container = AddThemeObject(SetChildren(SetProps(MakeElement("ScrollFrame", Color3.fromRGB(255, 255, 255), 5), {
			Size = UDim2.new(1, -150, 1, -50),
			Position = UDim2.new(0, 150, 0, 50),
			Parent = MainWindow,
			Visible = false,
			Name = "ItemContainer"
		}), {
			MakeElement("List", 0, 6),
			MakeElement("Padding", 15, 10, 10, 15)
		}), "Divider")

		AddConnection(Container.UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"), function()
			Container.CanvasSize = UDim2.new(0, 0, 0, Container.UIListLayout.AbsoluteContentSize.Y + 30)
		end)

		if FirstTab then
			FirstTab = false
			TabFrame.Ico.ImageTransparency = 0
			TabFrame.Title.TextTransparency = 0
			TabFrame.Title.Font = Enum.Font.GothamBlack
			Container.Visible = true
		end    

		AddConnection(TabFrame.MouseButton1Click, function()
			for _, Tab in next, TabHolder:GetChildren() do
				if Tab:IsA("TextButton") then
					Tab.Title.Font = Enum.Font.GothamSemibold
					TweenService:Create(Tab.Ico, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {ImageTransparency = 0.4}):Play()
					TweenService:Create(Tab.Title, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {TextTransparency = 0.4}):Play()
				end    
			end
			for _, ItemContainer in next, MainWindow:GetChildren() do
				if ItemContainer.Name == "ItemContainer" then
					ItemContainer.Visible = false
				end    
			end  
			TweenService:Create(TabFrame.Ico, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {ImageTransparency = 0}):Play()
			TweenService:Create(TabFrame.Title, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {TextTransparency = 0}):Play()
			TabFrame.Title.Font = Enum.Font.GothamBlack
			Container.Visible = true   
		end)

		local function GetElements(ItemParent)
			local ElementFunction = {}
			function ElementFunction:AddLabel(Text)
				local LabelFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 30),
					BackgroundTransparency = 0.7,
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", Text, 15), {
						Size = UDim2.new(1, -12, 1, 0),
						Position = UDim2.new(0, 12, 0, 0),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(MakeElement("Stroke"), "Stroke")
				}), "Second")

				local LabelFunction = {}
				function LabelFunction:Set(ToChange)
					LabelFrame.Content.Text = ToChange
				end
				return LabelFunction
			end
			function ElementFunction:AddParagraph(Text, Content)
				Text = Text or "Text"
				Content = Content or "Content"

				local ParagraphFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 30),
					BackgroundTransparency = 0.7,
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", Text, 15), {
						Size = UDim2.new(1, -12, 0, 14),
						Position = UDim2.new(0, 12, 0, 10),
						Font = Enum.Font.GothamBold,
						Name = "Title"
					}), "Text"),
					AddThemeObject(SetProps(MakeElement("Label", "", 13), {
						Size = UDim2.new(1, -24, 0, 0),
						Position = UDim2.new(0, 12, 0, 26),
						Font = Enum.Font.GothamSemibold,
						Name = "Content",
						TextWrapped = true
					}), "TextDark"),
					AddThemeObject(MakeElement("Stroke"), "Stroke")
				}), "Second")

				AddConnection(ParagraphFrame.Content:GetPropertyChangedSignal("Text"), function()
					ParagraphFrame.Content.Size = UDim2.new(1, -24, 0, ParagraphFrame.Content.TextBounds.Y)
					ParagraphFrame.Size = UDim2.new(1, 0, 0, ParagraphFrame.Content.TextBounds.Y + 35)
				end)

				ParagraphFrame.Content.Text = Content

				local ParagraphFunction = {}
				function ParagraphFunction:Set(ToChange)
					ParagraphFrame.Content.Text = ToChange
				end
				return ParagraphFunction
			end    
			function ElementFunction:AddButton(ButtonConfig)
				ButtonConfig = ButtonConfig or {}
				ButtonConfig.Name = ButtonConfig.Name or "Button"
				ButtonConfig.Callback = ButtonConfig.Callback or function() end
				ButtonConfig.Icon = ButtonConfig.Icon or "rbxassetid://3944703587"

				local Button = {}

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local ButtonFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 33),
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", ButtonConfig.Name, 15), {
						Size = UDim2.new(1, -12, 1, 0),
						Position = UDim2.new(0, 12, 0, 0),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(SetProps(MakeElement("Image", ButtonConfig.Icon), {
						Size = UDim2.new(0, 20, 0, 20),
						Position = UDim2.new(1, -30, 0, 7),
					}), "TextDark"),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					Click
				}), "Second")

				AddConnection(Click.MouseEnter, function()
					TweenService:Create(ButtonFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
				end)

				AddConnection(Click.MouseLeave, function()
					TweenService:Create(ButtonFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = OrionLib.Themes[OrionLib.SelectedTheme].Second}):Play()
				end)

				AddConnection(Click.MouseButton1Up, function()
					TweenService:Create(ButtonFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
					spawn(function()
						ButtonConfig.Callback()
					end)
				end)

				AddConnection(Click.MouseButton1Down, function()
					TweenService:Create(ButtonFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 6)}):Play()
				end)

				function Button:Set(ButtonText)
					ButtonFrame.Content.Text = ButtonText
				end	

				return Button
			end    
			function ElementFunction:AddToggle(ToggleConfig)
				ToggleConfig = ToggleConfig or {}
				ToggleConfig.Name = ToggleConfig.Name or "Toggle"
				ToggleConfig.Default = ToggleConfig.Default or false
				ToggleConfig.Callback = ToggleConfig.Callback or function() end
				ToggleConfig.Color = ToggleConfig.Color or Color3.fromRGB(255, 50, 100)
				ToggleConfig.Flag = ToggleConfig.Flag or nil
				ToggleConfig.Save = ToggleConfig.Save or false

				local Toggle = {Value = ToggleConfig.Default, Save = ToggleConfig.Save}

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local ToggleBox = SetChildren(SetProps(MakeElement("RoundFrame", ToggleConfig.Color, 0, 4), {
					Size = UDim2.new(0, 24, 0, 24),
					Position = UDim2.new(1, -24, 0.5, 0),
					AnchorPoint = Vector2.new(0.5, 0.5)
				}), {
					SetProps(MakeElement("Stroke"), {
						Color = ToggleConfig.Color,
						Name = "Stroke",
						Transparency = 0.5
					}),
					SetProps(MakeElement("Image", "rbxassetid://3944680095"), {
						Size = UDim2.new(0, 20, 0, 20),
						AnchorPoint = Vector2.new(0.5, 0.5),
						Position = UDim2.new(0.5, 0, 0.5, 0),
						ImageColor3 = Color3.fromRGB(255, 255, 255),
						Name = "Ico"
					}),
				})

				local ToggleFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 38),
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", ToggleConfig.Name, 15), {
						Size = UDim2.new(1, -12, 1, 0),
						Position = UDim2.new(0, 12, 0, 0),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					ToggleBox,
					Click
				}), "Second")

				function Toggle:Set(Value)
					Toggle.Value = Value
					TweenService:Create(ToggleBox, TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Toggle.Value and ToggleConfig.Color or OrionLib.Themes.Default.Divider}):Play()
					TweenService:Create(ToggleBox.Stroke, TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Color = Toggle.Value and ToggleConfig.Color or OrionLib.Themes.Default.Stroke}):Play()
					TweenService:Create(ToggleBox.Ico, TweenInfo.new(0.3, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {ImageTransparency = Toggle.Value and 0 or 1, Size = Toggle.Value and UDim2.new(0, 20, 0, 20) or UDim2.new(0, 8, 0, 8)}):Play()
					ToggleConfig.Callback(Toggle.Value)
				end    

				Toggle:Set(Toggle.Value)

				AddConnection(Click.MouseEnter, function()
					TweenService:Create(ToggleFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
				end)

				AddConnection(Click.MouseLeave, function()
					TweenService:Create(ToggleFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = OrionLib.Themes[OrionLib.SelectedTheme].Second}):Play()
				end)

				AddConnection(Click.MouseButton1Up, function()
					TweenService:Create(ToggleFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
					SaveCfg(game.GameId)
					Toggle:Set(not Toggle.Value)
				end)

				AddConnection(Click.MouseButton1Down, function()
					TweenService:Create(ToggleFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 6)}):Play()
				end)

				if ToggleConfig.Flag then
					OrionLib.Flags[ToggleConfig.Flag] = Toggle
				end	
				return Toggle
			end  
			function ElementFunction:AddSlider(SliderConfig)
				SliderConfig = SliderConfig or {}
				SliderConfig.Name = SliderConfig.Name or "Slider"
				SliderConfig.Min = SliderConfig.Min or 0
				SliderConfig.Max = SliderConfig.Max or 100
				SliderConfig.Increment = SliderConfig.Increment or 1
				SliderConfig.Default = SliderConfig.Default or 50
				SliderConfig.Callback = SliderConfig.Callback or function() end
				SliderConfig.ValueName = SliderConfig.ValueName or ""
				SliderConfig.Color = Color3.fromRGB(255, 50, 100)
				SliderConfig.Flag = SliderConfig.Flag or nil
				SliderConfig.Save = SliderConfig.Save or false

				local Slider = {Value = SliderConfig.Default, Save = SliderConfig.Save}
				local Dragging = false

				local SliderDrag = SetChildren(SetProps(MakeElement("RoundFrame", SliderConfig.Color, 0, 5), {
					Size = UDim2.new(0, 0, 1, 0),
					BackgroundTransparency = 0.3,
					ClipsDescendants = true
				}), {
					AddThemeObject(SetProps(MakeElement("Label", "value", 13), {
						Size = UDim2.new(1, -12, 0, 14),
						Position = UDim2.new(0, 12, 0, 6),
						Font = Enum.Font.GothamBold,
						Name = "Value",
						TextTransparency = 0
					}), "Text")
				})

				local SliderBar = SetChildren(SetProps(MakeElement("RoundFrame", SliderConfig.Color, 0, 5), {
					Size = UDim2.new(1, -24, 0, 26),
					Position = UDim2.new(0, 12, 0, 30),
					BackgroundTransparency = 0.9
				}), {
					SetProps(MakeElement("Stroke"), {
						Color = SliderConfig.Color
					}),
					AddThemeObject(SetProps(MakeElement("Label", "value", 13), {
						Size = UDim2.new(1, -12, 0, 14),
						Position = UDim2.new(0, 12, 0, 6),
						Font = Enum.Font.GothamBold,
						Name = "Value",
						TextTransparency = 0.8
					}), "Text"),
					SliderDrag
				})

				local SliderFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 4), {
					Size = UDim2.new(1, 0, 0, 65),
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", SliderConfig.Name, 15), {
						Size = UDim2.new(1, -12, 0, 14),
						Position = UDim2.new(0, 12, 0, 10),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					SliderBar
				}), "Second")

				SliderBar.InputBegan:Connect(function(Input)
					if Input.UserInputType == Enum.UserInputType.MouseButton1 then 
						Dragging = true 
					end 
				end)
				SliderBar.InputEnded:Connect(function(Input) 
					if Input.UserInputType == Enum.UserInputType.MouseButton1 then 
						Dragging = false 
					end 
				end)

				UserInputService.InputChanged:Connect(function(Input)
					if Dragging and Input.UserInputType == Enum.UserInputType.MouseMovement then 
						local SizeScale = math.clamp((Input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
						Slider:Set(SliderConfig.Min + ((SliderConfig.Max - SliderConfig.Min) * SizeScale)) 
						SaveCfg(game.GameId)
					end
				end)

				function Slider:Set(Value)
					self.Value = math.clamp(Round(Value, SliderConfig.Increment), SliderConfig.Min, SliderConfig.Max)
					TweenService:Create(SliderDrag,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Size = UDim2.fromScale((self.Value - SliderConfig.Min) / (SliderConfig.Max - SliderConfig.Min), 1)}):Play()
					SliderBar.Value.Text = tostring(self.Value) .. " " .. SliderConfig.ValueName
					SliderDrag.Value.Text = tostring(self.Value) .. " " .. SliderConfig.ValueName
					SliderConfig.Callback(self.Value)
				end      

				Slider:Set(Slider.Value)
				if SliderConfig.Flag then				
					OrionLib.Flags[SliderConfig.Flag] = Slider
				end
				return Slider
			end  
			function ElementFunction:AddDropdown(DropdownConfig)
				DropdownConfig = DropdownConfig or {}
				DropdownConfig.Name = DropdownConfig.Name or "Dropdown"
				DropdownConfig.Options = DropdownConfig.Options or {}
				DropdownConfig.Default = DropdownConfig.Default or ""
				DropdownConfig.Callback = DropdownConfig.Callback or function() end
				DropdownConfig.Flag = DropdownConfig.Flag or nil
				DropdownConfig.Save = DropdownConfig.Save or false

				local Dropdown = {Value = DropdownConfig.Default, Options = DropdownConfig.Options, Buttons = {}, Toggled = false, Type = "Dropdown", Save = DropdownConfig.Save}
				local MaxElements = 5

				if not table.find(Dropdown.Options, Dropdown.Value) then
					Dropdown.Value = "..."
				end

				local DropdownList = MakeElement("List")

				local DropdownContainer = AddThemeObject(SetProps(SetChildren(MakeElement("ScrollFrame", Color3.fromRGB(40, 40, 40), 4), {
					DropdownList
				}), {
					Parent = ItemParent,
					Position = UDim2.new(0, 0, 0, 38),
					Size = UDim2.new(1, 0, 1, -38),
					ClipsDescendants = true
				}), "Divider")

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local DropdownFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 38),
					Parent = ItemParent,
					ClipsDescendants = true
				}), {
					DropdownContainer,
					SetProps(SetChildren(MakeElement("TFrame"), {
						AddThemeObject(SetProps(MakeElement("Label", DropdownConfig.Name, 15), {
							Size = UDim2.new(1, -12, 1, 0),
							Position = UDim2.new(0, 12, 0, 0),
							Font = Enum.Font.GothamBold,
							Name = "Content"
						}), "Text"),
						AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://7072706796"), {
							Size = UDim2.new(0, 20, 0, 20),
							AnchorPoint = Vector2.new(0, 0.5),
							Position = UDim2.new(1, -30, 0.5, 0),
							ImageColor3 = Color3.fromRGB(240, 240, 240),
							Name = "Ico"
						}), "TextDark"),
						AddThemeObject(SetProps(MakeElement("Label", "Selected", 13), {
							Size = UDim2.new(1, -40, 1, 0),
							Font = Enum.Font.Gotham,
							Name = "Selected",
							TextXAlignment = Enum.TextXAlignment.Right
						}), "TextDark"),
						AddThemeObject(SetProps(MakeElement("Frame"), {
							Size = UDim2.new(1, 0, 0, 1),
							Position = UDim2.new(0, 0, 1, -1),
							Name = "Line",
							Visible = false
						}), "Stroke"), 
						Click
					}), {
						Size = UDim2.new(1, 0, 0, 38),
						ClipsDescendants = true,
						Name = "F"
					}),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					MakeElement("Corner")
				}), "Second")

				AddConnection(DropdownList:GetPropertyChangedSignal("AbsoluteContentSize"), function()
					DropdownContainer.CanvasSize = UDim2.new(0, 0, 0, DropdownList.AbsoluteContentSize.Y)
				end)  

				local function AddOptions(Options)
					for _, Option in pairs(Options) do
						local OptionBtn = AddThemeObject(SetProps(SetChildren(MakeElement("Button", Color3.fromRGB(40, 40, 40)), {
							MakeElement("Corner", 0, 6),
							AddThemeObject(SetProps(MakeElement("Label", Option, 13, 0.4), {
								Position = UDim2.new(0, 8, 0, 0),
								Size = UDim2.new(1, -8, 1, 0),
								Name = "Title"
							}), "Text")
						}), {
							Parent = DropdownContainer,
							Size = UDim2.new(1, 0, 0, 28),
							BackgroundTransparency = 1,
							ClipsDescendants = true
						}), "Divider")

						AddConnection(OptionBtn.MouseButton1Click, function()
							Dropdown:Set(Option)
							SaveCfg(game.GameId)
						end)

						Dropdown.Buttons[Option] = OptionBtn
					end
				end	

				function Dropdown:Refresh(Options, Delete)
					if Delete then
						for _,v in pairs(Dropdown.Buttons) do
							v:Destroy()
						end    
						table.clear(Dropdown.Options)
						table.clear(Dropdown.Buttons)
					end
					Dropdown.Options = Options
					AddOptions(Dropdown.Options)
				end  

				function Dropdown:Set(Value)
					if not table.find(Dropdown.Options, Value) then
						Dropdown.Value = "..."
						DropdownFrame.F.Selected.Text = Dropdown.Value
						for _, v in pairs(Dropdown.Buttons) do
							TweenService:Create(v,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 1}):Play()
							TweenService:Create(v.Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0.4}):Play()
						end	
						return
					end

					Dropdown.Value = Value
					DropdownFrame.F.Selected.Text = Dropdown.Value

					for _, v in pairs(Dropdown.Buttons) do
						TweenService:Create(v,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 1}):Play()
						TweenService:Create(v.Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0.4}):Play()
					end	
					TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 0}):Play()
					TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0}):Play()
					return DropdownConfig.Callback(Dropdown.Value)
				end

				AddConnection(Click.MouseButton1Click, function()
					Dropdown.Toggled = not Dropdown.Toggled
					DropdownFrame.F.Line.Visible = Dropdown.Toggled
					TweenService:Create(DropdownFrame.F.Ico,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Rotation = Dropdown.Toggled and 180 or 0}):Play()
					if #Dropdown.Options > MaxElements then
						TweenService:Create(DropdownFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Size = Dropdown.Toggled and UDim2.new(1, 0, 0, 38 + (MaxElements * 28)) or UDim2.new(1, 0, 0, 38)}):Play()
					else
						TweenService:Create(DropdownFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Size = Dropdown.Toggled and UDim2.new(1, 0, 0, DropdownList.AbsoluteContentSize.Y + 38) or UDim2.new(1, 0, 0, 38)}):Play()
					end
				end)

				Dropdown:Refresh(Dropdown.Options, false)
				Dropdown:Set(Dropdown.Value)
				if DropdownConfig.Flag then				
					OrionLib.Flags[DropdownConfig.Flag] = Dropdown
				end
				return Dropdown
			end
			function ElementFunction:AddBind(BindConfig)
				BindConfig.Name = BindConfig.Name or "Bind"
				BindConfig.Default = BindConfig.Default or Enum.KeyCode.Unknown
				BindConfig.Hold = BindConfig.Hold or false
				BindConfig.Callback = BindConfig.Callback or function() end
				BindConfig.Flag = BindConfig.Flag or nil
				BindConfig.Save = BindConfig.Save or false

				local Bind = {Value, Binding = false, Type = "Bind", Save = BindConfig.Save}
				local Holding = false

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local BindBox = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 4), {
					Size = UDim2.new(0, 24, 0, 24),
					Position = UDim2.new(1, -12, 0.5, 0),
					AnchorPoint = Vector2.new(1, 0.5)
				}), {
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					AddThemeObject(SetProps(MakeElement("Label", BindConfig.Name, 14), {
						Size = UDim2.new(1, 0, 1, 0),
						Font = Enum.Font.GothamBold,
						TextXAlignment = Enum.TextXAlignment.Center,
						Name = "Value"
					}), "Text")
				}), "Main")

				local BindFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 38),
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", BindConfig.Name, 15), {
						Size = UDim2.new(1, -12, 1, 0),
						Position = UDim2.new(0, 12, 0, 0),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					BindBox,
					Click
				}), "Second")

				AddConnection(BindBox.Value:GetPropertyChangedSignal("Text"), function()
					--BindBox.Size = UDim2.new(0, BindBox.Value.TextBounds.X + 16, 0, 24)
					TweenService:Create(BindBox, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Size = UDim2.new(0, BindBox.Value.TextBounds.X + 16, 0, 24)}):Play()
				end)

				AddConnection(Click.InputEnded, function(Input)
					if Input.UserInputType == Enum.UserInputType.MouseButton1 then
						if Bind.Binding then return end
						Bind.Binding = true
						BindBox.Value.Text = ""
					end
				end)

				AddConnection(UserInputService.InputBegan, function(Input)
					if UserInputService:GetFocusedTextBox() then return end
					if (Input.KeyCode.Name == Bind.Value or Input.UserInputType.Name == Bind.Value) and not Bind.Binding then
						if BindConfig.Hold then
							Holding = true
							BindConfig.Callback(Holding)
						else
							BindConfig.Callback()
						end
					elseif Bind.Binding then
						local Key
						pcall(function()
							if not CheckKey(BlacklistedKeys, Input.KeyCode) then
								Key = Input.KeyCode
							end
						end)
						pcall(function()
							if CheckKey(WhitelistedMouse, Input.UserInputType) and not Key then
								Key = Input.UserInputType
							end
						end)
						Key = Key or Bind.Value
						Bind:Set(Key)
						SaveCfg(game.GameId)
					end
				end)

				AddConnection(UserInputService.InputEnded, function(Input)
					if Input.KeyCode.Name == Bind.Value or Input.UserInputType.Name == Bind.Value then
						if BindConfig.Hold and Holding then
							Holding = false
							BindConfig.Callback(Holding)
						end
					end
				end)

				AddConnection(Click.MouseEnter, function()
					TweenService:Create(BindFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
				end)

				AddConnection(Click.MouseLeave, function()
					TweenService:Create(BindFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = OrionLib.Themes[OrionLib.SelectedTheme].Second}):Play()
				end)

				AddConnection(Click.MouseButton1Up, function()
					TweenService:Create(BindFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
				end)

				AddConnection(Click.MouseButton1Down, function()
					TweenService:Create(BindFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 6)}):Play()
				end)

				function Bind:Set(Key)
					Bind.Binding = false
					Bind.Value = Key or Bind.Value
					Bind.Value = Bind.Value.Name or Bind.Value
					BindBox.Value.Text = Bind.Value
				end

				Bind:Set(BindConfig.Default)
				if BindConfig.Flag then				
					OrionLib.Flags[BindConfig.Flag] = Bind
				end
				return Bind
			end  
			function ElementFunction:AddTextbox(TextboxConfig)
				TextboxConfig = TextboxConfig or {}
				TextboxConfig.Name = TextboxConfig.Name or "Textbox"
				TextboxConfig.Default = TextboxConfig.Default or ""
				TextboxConfig.TextDisappear = TextboxConfig.TextDisappear or false
				TextboxConfig.Callback = TextboxConfig.Callback or function() end

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local TextboxActual = AddThemeObject(Create("TextBox", {
					Size = UDim2.new(1, 0, 1, 0),
					BackgroundTransparency = 1,
					TextColor3 = Color3.fromRGB(255, 255, 255),
					PlaceholderColor3 = Color3.fromRGB(210,210,210),
					PlaceholderText = "Input",
					Font = Enum.Font.GothamSemibold,
					TextXAlignment = Enum.TextXAlignment.Center,
					TextSize = 14,
					ClearTextOnFocus = false
				}), "Text")

				local TextContainer = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 4), {
					Size = UDim2.new(0, 24, 0, 24),
					Position = UDim2.new(1, -12, 0.5, 0),
					AnchorPoint = Vector2.new(1, 0.5)
				}), {
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					TextboxActual
				}), "Main")


				local TextboxFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 38),
					Parent = ItemParent
				}), {
					AddThemeObject(SetProps(MakeElement("Label", TextboxConfig.Name, 15), {
						Size = UDim2.new(1, -12, 1, 0),
						Position = UDim2.new(0, 12, 0, 0),
						Font = Enum.Font.GothamBold,
						Name = "Content"
					}), "Text"),
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
					TextContainer,
					Click
				}), "Second")

				AddConnection(TextboxActual:GetPropertyChangedSignal("Text"), function()
					--TextContainer.Size = UDim2.new(0, TextboxActual.TextBounds.X + 16, 0, 24)
					TweenService:Create(TextContainer, TweenInfo.new(0.45, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Size = UDim2.new(0, TextboxActual.TextBounds.X + 16, 0, 24)}):Play()
				end)

				AddConnection(TextboxActual.FocusLost, function()
					TextboxConfig.Callback(TextboxActual.Text)
					if TextboxConfig.TextDisappear then
						TextboxActual.Text = ""
					end	
				end)

				TextboxActual.Text = TextboxConfig.Default

				AddConnection(Click.MouseEnter, function()
					TweenService:Create(TextboxFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
				end)

				AddConnection(Click.MouseLeave, function()
					TweenService:Create(TextboxFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = OrionLib.Themes[OrionLib.SelectedTheme].Second}):Play()
				end)

				AddConnection(Click.MouseButton1Up, function()
					TweenService:Create(TextboxFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 3, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 3)}):Play()
					TextboxActual:CaptureFocus()
				end)

				AddConnection(Click.MouseButton1Down, function()
					TweenService:Create(TextboxFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {BackgroundColor3 = Color3.fromRGB(OrionLib.Themes[OrionLib.SelectedTheme].Second.R * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.G * 255 + 6, OrionLib.Themes[OrionLib.SelectedTheme].Second.B * 255 + 6)}):Play()
				end)
			end 
			function ElementFunction:AddColorpicker(ColorpickerConfig)
				ColorpickerConfig = ColorpickerConfig or {}
				ColorpickerConfig.Name = ColorpickerConfig.Name or "Colorpicker"
				ColorpickerConfig.Default = ColorpickerConfig.Default or Color3.fromRGB(255,255,255)
				ColorpickerConfig.Callback = ColorpickerConfig.Callback or function() end
				ColorpickerConfig.Flag = ColorpickerConfig.Flag or nil
				ColorpickerConfig.Save = ColorpickerConfig.Save or false

				local ColorH, ColorS, ColorV = 1, 1, 1
				local Colorpicker = {Value = ColorpickerConfig.Default, Toggled = false, Type = "Colorpicker", Save = ColorpickerConfig.Save}

				local ColorSelection = Create("ImageLabel", {
					Size = UDim2.new(0, 18, 0, 18),
					Position = UDim2.new(select(3, Color3.toHSV(Colorpicker.Value))),
					ScaleType = Enum.ScaleType.Fit,
					AnchorPoint = Vector2.new(0.5, 0.5),
					BackgroundTransparency = 1,
					Image = "http://www.roblox.com/asset/?id=4805639000"
				})

				local HueSelection = Create("ImageLabel", {
					Size = UDim2.new(0, 18, 0, 18),
					Position = UDim2.new(0.5, 0, 1 - select(1, Color3.toHSV(Colorpicker.Value))),
					ScaleType = Enum.ScaleType.Fit,
					AnchorPoint = Vector2.new(0.5, 0.5),
					BackgroundTransparency = 1,
					Image = "http://www.roblox.com/asset/?id=4805639000"
				})

				local Color = Create("ImageLabel", {
					Size = UDim2.new(1, -25, 1, 0),
					Visible = false,
					Image = "rbxassetid://4155801252"
				}, {
					Create("UICorner", {CornerRadius = UDim.new(0, 5)}),
					ColorSelection
				})

				local Hue = Create("Frame", {
					Size = UDim2.new(0, 20, 1, 0),
					Position = UDim2.new(1, -20, 0, 0),
					Visible = false
				}, {
					Create("UIGradient", {Rotation = 270, Color = ColorSequence.new{ColorSequenceKeypoint.new(0.00, Color3.fromRGB(255, 0, 4)), ColorSequenceKeypoint.new(0.20, Color3.fromRGB(234, 255, 0)), ColorSequenceKeypoint.new(0.40, Color3.fromRGB(21, 255, 0)), ColorSequenceKeypoint.new(0.60, Color3.fromRGB(0, 255, 255)), ColorSequenceKeypoint.new(0.80, Color3.fromRGB(0, 17, 255)), ColorSequenceKeypoint.new(0.90, Color3.fromRGB(255, 0, 251)), ColorSequenceKeypoint.new(1.00, Color3.fromRGB(255, 0, 4))},}),
					Create("UICorner", {CornerRadius = UDim.new(0, 5)}),
					HueSelection
				})

				local ColorpickerContainer = Create("Frame", {
					Position = UDim2.new(0, 0, 0, 32),
					Size = UDim2.new(1, 0, 1, -32),
					BackgroundTransparency = 1,
					ClipsDescendants = true
				}, {
					Hue,
					Color,
					Create("UIPadding", {
						PaddingLeft = UDim.new(0, 35),
						PaddingRight = UDim.new(0, 35),
						PaddingBottom = UDim.new(0, 10),
						PaddingTop = UDim.new(0, 17)
					})
				})

				local Click = SetProps(MakeElement("Button"), {
					Size = UDim2.new(1, 0, 1, 0)
				})

				local ColorpickerBox = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 4), {
					Size = UDim2.new(0, 24, 0, 24),
					Position = UDim2.new(1, -12, 0.5, 0),
					AnchorPoint = Vector2.new(1, 0.5)
				}), {
					AddThemeObject(MakeElement("Stroke"), "Stroke")
				}), "Main")

				local ColorpickerFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
					Size = UDim2.new(1, 0, 0, 38),
					Parent = ItemParent
				}), {
					SetProps(SetChildren(MakeElement("TFrame"), {
						AddThemeObject(SetProps(MakeElement("Label", ColorpickerConfig.Name, 15), {
							Size = UDim2.new(1, -12, 1, 0),
							Position = UDim2.new(0, 12, 0, 0),
							Font = Enum.Font.GothamBold,
							Name = "Content"
						}), "Text"),
						ColorpickerBox,
						Click,
						AddThemeObject(SetProps(MakeElement("Frame"), {
							Size = UDim2.new(1, 0, 0, 1),
							Position = UDim2.new(0, 0, 1, -1),
							Name = "Line",
							Visible = false
						}), "Stroke"), 
					}), {
						Size = UDim2.new(1, 0, 0, 38),
						ClipsDescendants = true,
						Name = "F"
					}),
					ColorpickerContainer,
					AddThemeObject(MakeElement("Stroke"), "Stroke"),
				}), "Second")

				AddConnection(Click.MouseButton1Click, function()
					Colorpicker.Toggled = not Colorpicker.Toggled
					TweenService:Create(ColorpickerFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Size = Colorpicker.Toggled and UDim2.new(1, 0, 0, 148) or UDim2.new(1, 0, 0, 38)}):Play()
					Color.Visible = Colorpicker.Toggled
					Hue.Visible = Colorpicker.Toggled
					ColorpickerFrame.F.Line.Visible = Colorpicker.Toggled
				end)

				local function UpdateColorPicker()
					ColorpickerBox.BackgroundColor3 = Color3.fromHSV(ColorH, ColorS, ColorV)
					Color.BackgroundColor3 = Color3.fromHSV(ColorH, 1, 1)
					Colorpicker:Set(ColorpickerBox.BackgroundColor3)
					ColorpickerConfig.Callback(ColorpickerBox.BackgroundColor3)
					SaveCfg(game.GameId)
				end

				ColorH = 1 - (math.clamp(HueSelection.AbsolutePosition.Y - Hue.AbsolutePosition.Y, 0, Hue.AbsoluteSize.Y) / Hue.AbsoluteSize.Y)
				ColorS = (math.clamp(ColorSelection.AbsolutePosition.X - Color.AbsolutePosition.X, 0, Color.AbsoluteSize.X) / Color.AbsoluteSize.X)
				ColorV = 1 - (math.clamp(ColorSelection.AbsolutePosition.Y - Color.AbsolutePosition.Y, 0, Color.AbsoluteSize.Y) / Color.AbsoluteSize.Y)

				AddConnection(Color.InputBegan, function(input)
					if input.UserInputType == Enum.UserInputType.MouseButton1 then
						if ColorInput then
							ColorInput:Disconnect()
						end
						ColorInput = AddConnection(RunService.RenderStepped, function()
							local ColorX = (math.clamp(Mouse.X - Color.AbsolutePosition.X, 0, Color.AbsoluteSize.X) / Color.AbsoluteSize.X)
							local ColorY = (math.clamp(Mouse.Y - Color.AbsolutePosition.Y, 0, Color.AbsoluteSize.Y) / Color.AbsoluteSize.Y)
							ColorSelection.Position = UDim2.new(ColorX, 0, ColorY, 0)
							ColorS = ColorX
							ColorV = 1 - ColorY
							UpdateColorPicker()
						end)
					end
				end)

				AddConnection(Color.InputEnded, function(input)
					if input.UserInputType == Enum.UserInputType.MouseButton1 then
						if ColorInput then
							ColorInput:Disconnect()
						end
					end
				end)

				AddConnection(Hue.InputBegan, function(input)
					if input.UserInputType == Enum.UserInputType.MouseButton1 then
						if HueInput then
							HueInput:Disconnect()
						end;

						HueInput = AddConnection(RunService.RenderStepped, function()
							local HueY = (math.clamp(Mouse.Y - Hue.AbsolutePosition.Y, 0, Hue.AbsoluteSize.Y) / Hue.AbsoluteSize.Y)

							HueSelection.Position = UDim2.new(0.5, 0, HueY, 0)
							ColorH = 1 - HueY

							UpdateColorPicker()
						end)
					end
				end)

				AddConnection(Hue.InputEnded, function(input)
					if input.UserInputType == Enum.UserInputType.MouseButton1 then
						if HueInput then
							HueInput:Disconnect()
						end
					end
				end)

				function Colorpicker:Set(Value)
					Colorpicker.Value = Value
					ColorpickerBox.BackgroundColor3 = Colorpicker.Value
					ColorpickerConfig.Callback(Colorpicker.Value)
				end

				Colorpicker:Set(Colorpicker.Value)
				if ColorpickerConfig.Flag then				
					OrionLib.Flags[ColorpickerConfig.Flag] = Colorpicker
				end
				return Colorpicker
			end  
			return ElementFunction   
		end	

		local ElementFunction = {}

		function ElementFunction:AddSection(SectionConfig)
			SectionConfig.Name = SectionConfig.Name or "Section"

			local SectionFrame = SetChildren(SetProps(MakeElement("TFrame"), {
				Size = UDim2.new(1, 0, 0, 26),
				Parent = Container
			}), {
				AddThemeObject(SetProps(MakeElement("Label", SectionConfig.Name, 14), {
					Size = UDim2.new(1, -12, 0, 16),
					Position = UDim2.new(0, 0, 0, 3),
					Font = Enum.Font.GothamSemibold
				}), "TextDark"),
				SetChildren(SetProps(MakeElement("TFrame"), {
					AnchorPoint = Vector2.new(0, 0),
					Size = UDim2.new(1, 0, 1, -24),
					Position = UDim2.new(0, 0, 0, 23),
					Name = "Holder"
				}), {
					MakeElement("List", 0, 6)
				}),
			})

			AddConnection(SectionFrame.Holder.UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"), function()
				SectionFrame.Size = UDim2.new(1, 0, 0, SectionFrame.Holder.UIListLayout.AbsoluteContentSize.Y + 31)
				SectionFrame.Holder.Size = UDim2.new(1, 0, 0, SectionFrame.Holder.UIListLayout.AbsoluteContentSize.Y)
			end)

			local SectionFunction = {}
			for i, v in next, GetElements(SectionFrame.Holder) do
				SectionFunction[i] = v 
			end
			return SectionFunction
		end	

		for i, v in next, GetElements(Container) do
			ElementFunction[i] = v 
		end

		if TabConfig.PremiumOnly then
			for i, v in next, ElementFunction do
				ElementFunction[i] = function() end
			end    
			Container:FindFirstChild("UIListLayout"):Destroy()
			Container:FindFirstChild("UIPadding"):Destroy()
			SetChildren(SetProps(MakeElement("TFrame"), {
				Size = UDim2.new(1, 0, 1, 0),
				Parent = ItemParent
			}), {
				AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://3610239960"), {
					Size = UDim2.new(0, 18, 0, 18),
					Position = UDim2.new(0, 15, 0, 15),
					ImageTransparency = 0.4
				}), "Text"),
				AddThemeObject(SetProps(MakeElement("Label", "Unauthorised Access", 14), {
					Size = UDim2.new(1, -38, 0, 14),
					Position = UDim2.new(0, 38, 0, 18),
					TextTransparency = 0.4
				}), "Text"),
				AddThemeObject(SetProps(MakeElement("Image", "rbxassetid://4483345875"), {
					Size = UDim2.new(0, 56, 0, 56),
					Position = UDim2.new(0, 84, 0, 110),
				}), "Text"),
				AddThemeObject(SetProps(MakeElement("Label", "Premium Features", 14), {
					Size = UDim2.new(1, -150, 0, 14),
					Position = UDim2.new(0, 150, 0, 112),
					Font = Enum.Font.GothamBold
				}), "Text"),
				AddThemeObject(SetProps(MakeElement("Label", "This part of the script is locked to Sirius Premium users. Purchase Premium in the Discord server (discord.gg/sirius)", 12), {
					Size = UDim2.new(1, -200, 0, 14),
					Position = UDim2.new(0, 150, 0, 138),
					TextWrapped = true,
					TextTransparency = 0.4
				}), "Text")
			})
		end
		return ElementFunction   
	end  

	return TabFunction
end   
function OrionLib:Destroy()
	Orion:Destroy()
end
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
local TemplateBV = Instance.new("BodyVelocity")
TemplateBV.Parent = nil
TemplateBV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
TemplateBV.P = 1250

local FriendList = {}
local Whitelist = {}
--local ImmunityList = loadstring(game:HttpGet("cumsaur"))()

task.defer(function()
	for _, Plr in pairs(Players:GetPlayers()) do
		if LocalPlayer:IsFriendsWith(Plr.UserId) then
			table.insert(FriendList, Plr.Name)
		end
	end
end)

Players.PlayerAdded:Connect(function(AddedPlayer)
	if LocalPlayer:IsFriendsWith(AddedPlayer.UserId) then
		table.insert(FriendList, AddedPlayer.Name)
	end
end)
Players.PlayerRemoving:Connect(function(RemovedPlayer)
	local RemovedString =  RemovedPlayer.Name .. " { " .. RemovedPlayer.DisplayName .. " }"

	if RemoveWhitelistedOnLeave then
		for i, Whitelisted in pairs(Whitelist) do
			if RemovedString == Whitelisted then
				table.remove(Whitelist, i)
			end
		end
	end

	for i, Friended in pairs(FriendList) do
		if Friended == RemovedPlayer.Name then
			table.remove(FriendList, i)
		end
	end

end)
function IsFriend(Player: Player)
	for i, Friended in pairs(FriendList) do
		if Player.Name == Friended then
			return true
		end
	end
	return false
end
function IsInvincible(Player: Player)
	local FinalString =  Player.Name .. " { " .. Player.DisplayName .. " }"
	if WhitelistCheck then
		for _, Whitelisted in pairs(Whitelist) do
			if FinalString == Whitelisted then
				return true
			end
		end
	end
	if FriendListCheck then
		IsFriend(Player)
	end
	--local ImmunityList = loadstring()
	--for i, Immune in pairs(ImmunityList) do
	--	if Player.UserId == Immune then	
	--		return true
	--	end
	--end
	--local AdminsList = loadstring()
	--for _, Admin in pairs(ImmunityList) do
	--	if Player.UserId == Immune then	
	--		return true
	--	end
	--end
	return false
end

local Window = OrionLib:MakeWindow({
	Name = "Wexort: The Skid Liquidator | V0.1", 
	HidePremium = true,
	IntroEnabled = true,
	IntroText = "Wexort Hub. Made by 4art and Gree.", 
	SaveConfig = true, 
	ConfigFolder = "WexortHub" 
})
local MainTab = Window:MakeTab({
	Name = "Main",
	Icon = "rbxassetid://7733960981",
})
local AntiTab = Window:MakeTab({
	Name = "Antis",
	Icon = "rbxassetid://7734056608",
})
local TargetTab = Window:MakeTab({
	Name = "Target",
	Icon = "rbxassetid://7743872758",
})
local PlayerTab = Window:MakeTab({
	Name = "Player",
	Icon = "rbxassetid://7743875962",
})
local BlobTab = Window:MakeTab({
	Name = "Blobman",
	Icon = "rbxassetid://8997385940",
})
local TelekinesisTab = Window:MakeTab({
	Name = "Telekinesis",
	Icon = "rbxassetid://7733955740",
})
local ExplosionsTab = Window:MakeTab({
	Name = "Explosions",
	Icon =  "rbxassetid://7734042493",
})
local MiscTab = Window:MakeTab({
	Name = "Misc",
	Icon = "rbxassetid://7733917120",
})
local AuraTab = Window:MakeTab({
	Name = "Auras",
	Icon =  "rbxassetid://7733919881",
})
local BindsTab = Window:MakeTab({
	Name = "Keybinds",
	Icon = "rbxassetid://7734010488",
})
local WhitelistTab = Window:MakeTab({
	Name = "Whitelist",
	Icon = "rbxassetid://7743869612",
})
local SettingsTab = Window:MakeTab({
	Name = "Script Settings",
	Icon = "rbxassetid://7734053495",
})
MainServerLag = MainTab:AddSection({
	Name = "Server lagger"
})
MainOP = MainTab:AddSection({
	Name = "OP"
})
MainOther = MainTab:AddSection({
	Name = "Other"
})
GrabSettingsSection = MainTab:AddSection({
	Name = "Grab Settings"
})
MainBlobman = MainTab:AddSection({
	Name = "Blobmen Disability"
})
Invincibility = AntiTab:AddSection({
	Name = "Invincibility"
})
TargetsPlayersSection = TargetTab:AddSection({
	Name = "Add Targets"
})
TargetsTargetSection = TargetTab:AddSection({
	Name = "Remove Targets"
})
TargetSection = TargetTab:AddSection({
	Name = "Targets"
})
CharacterSection = PlayerTab:AddSection({
	Name = "Character"
})
CameraSection = PlayerTab:AddSection({
	Name = "Camera"
})
BlackholeSection = TelekinesisTab:AddSection({
	Name = "Black Hole"
})
NPCsSection = TelekinesisTab:AddSection({
	Name = "Not Playable Characters"
})
GrabPhysics = MiscTab:AddSection({
	Name = "Grab Physics"
})
GuiLoader = MiscTab:AddSection({
	Name = "GUI Loader"
})
Sound = MiscTab:AddSection({
	Name = "Sound"
})
Other = MiscTab:AddSection({
	Name = "Other"
})
Data = MiscTab:AddSection({
	Name = "Data"
})
BlobPlayersSection = BlobTab:AddSection({
	Name = "Add Targets"
})
BlobTargetsSection = BlobTab:AddSection({
	Name = "Remove Targets"
})
BlobSection = BlobTab:AddSection({
	Name = "Blobman"
})
OpAurasSection = AuraTab:AddSection({
	Name = "OP Auras"
})
FunAurasSection = AuraTab:AddSection({
	Name = "Fun Auras"
})
AuraMisc = AuraTab:AddSection({
	Name = "Misc"
})
FriendCheckSection = WhitelistTab:AddSection({
	Name = "Friend List"
})
WhitelistCheckSection = WhitelistTab:AddSection({
	Name = "Whitelist"
})
ScriptSettings = SettingsTab:AddSection({
	Name = "Settings"
})
Helpful = SettingsTab:AddSection({
	Name = "Helpful"
})
SnowballSection = ExplosionsTab:AddSection({
	Name = "Snowball"
})
BombLaser = ExplosionsTab:AddSection({
	Name = "Laser"
})
ExplodeBomb = ExplosionsTab:AddSection({
	Name = "Explode Bombs"
})
SpawnBombs = ExplosionsTab:AddSection({
	Name = "Spawn Bombs"
})
ToyDismounting = MiscTab:AddSection({
	Name = "Toy Dismounting"
})
function UpdateAndRefresh(DropdownToRefresh, Table)
	local NewTable = {}

	for _, Name in pairs(Table) do
		if typeof(Name) == "string" then
			table.insert(NewTable, Name)
		end
	end

	DropdownToRefresh:Refresh(NewTable, true)
end
function RemoveFromList(Dropdown, Table, Player)
	local List = Table
	local PlayerEntry = Player.Name .. " { " .. Player.DisplayName .. " }"
	local FoundValue = table.find(Table, PlayerEntry)
	if FoundValue then
		table.remove(Table, FoundValue)
		task.wait()
		UpdateAndRefresh(Dropdown, List)
	end
end

function StopVelocity(Part, Time)
	local BV = Instance.new("BodyVelocity")
	BV.Parent = Part
	BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
	BV.P = 1250
	BV.Velocity = Vector3.new(0, 0, 0)
	task.wait(Time)
	BV:Destroy()
end
local AntiVoid = false
local AntiVoidToggler = Invincibility:AddToggle({
	Name = "Anti-Void",
	Callback = function(Value)
		AntiVoid = Value
		if AntiVoid then
			workspace.FallenPartsDestroyHeight = -50000
		else
			workspace.FallenPartsDestroyHeight = -100
		end
	end
})
local AntiGrab = false
local AntiGrabToggler = Invincibility:AddToggle({
	Name = "Anti-Grab",
	Callback = function(Value)
		AntiGrab = Value
		local Conn
		local RS = game:GetService("ReplicatedStorage")
		local CE = RS:WaitForChild("CharacterEvents")
		local R = game:GetService("RunService")
		local BeingHeld = LocalPlayer:WaitForChild("IsHeld")
		local PlayerScripts = LocalPlayer:WaitForChild("PlayerScripts")

		local StruggleEvent = CE:WaitForChild("Struggle")

		Conn = BeingHeld.Changed:Connect(function(C)
			if C == true and AntiGrab then
				local Character = LocalPlayer.Character
				local HRP = Character:FindFirstChild("HumanoidRootPart")
				local BeforeGrabCFrame = HRP.CFrame

				if BeingHeld.Value then
					local Event;
					Event = R.RenderStepped:Connect(function()
						if BeingHeld.Value == true then
							if TeleportBackAntiGrab then
								HRP.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
								HRP.CFrame = BeforeGrabCFrame
							end
							StruggleEvent:FireServer()
						elseif BeingHeld.Value == false then
							if TeleportBackAntiGrab then
								HRP.CFrame = BeforeGrabCFrame
							end
							Event:Disconnect()
							task.wait(0.5)
							if TeleportBackAntiGrab then
								HRP.CFrame = BeforeGrabCFrame
							end
						end
					end)
				end
			end
		end)
		if not AntiGrab and Conn then
			Conn:Disconnect()
		end
	end
})
local AnticheatAntiGrab = false
local AnticheatAntiGrabToggler = Invincibility:AddToggle({
	Name = "Anticheat Anti-Grab",
	Callback = function(Value)
		AnticheatAntiGrab = Value
		local gamecorrections = game.ReplicatedStorage:FindFirstChild("GameCorrectionEvents")
		local tptoground = gamecorrections:FindFirstChild("TeleportToGround")
		local conn
		if AnticheatAntiGrab then
			if AutoPair then
				AntiVoidToggler:Set(true)
			end
			tptoground.Parent = workspace
			conn = LocalPlayer.IsHeld.Changed:Connect(function(C)
				if C == true then
					local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
					if character then
						local myHRP = character:FindFirstChild("HumanoidRootPart")
						if myHRP then
							local oldCF = myHRP.CFrame
							task.defer(function()
								while true do
									local args = {
										[1] = CFrame.new(100000000000,100000000000,100000000000),
										[2] = CFrame.new(100000000000,100000000000,100000000000),
										[3] = CFrame.new(100000000000,100000000000,100000000000),
										[4] = "high"
									}
									local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
									remote:FireServer(unpack(args))
									local args = {
										[1] = CFrame.new(100000000000,100000000000,100000000000),
										[2] = CFrame.new(100000000000,100000000000,100000000000),
										[3] = CFrame.new(100000000000,100000000000,100000000000),
										[4] = "medium"
									}
									local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
									remote:FireServer(unpack(args))
									local args = {
										[1] = CFrame.new(100000000000,100000000000,100000000000),
										[2] = CFrame.new(100000000000,100000000000,100000000000),
										[3] = CFrame.new(100000000000,100000000000,100000000000),
										[4] = "low"
									}
									local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
									remote:FireServer(unpack(args))
									local args = {
										[1] = CFrame.new(100000000000,100000000000,100000000000),
										[2] = CFrame.new(100000000000,100000000000,100000000000),
										[3] = CFrame.new(100000000000,100000000000,100000000000),
										[4] = "lowest"
									}
									local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
									remote:FireServer(unpack(args))
									if not LocalPlayer.IsHeld.Value then
										if TeleportBackAntiGrab then
											task.defer(function()
												for i = 1,5 do
													myHRP.AssemblyLinearVelocity = Vector3.new(0,0,0)
													myHRP.CFrame = oldCF
													wait()
												end
											end)
										end
										break
									end
									wait()
								end
							end)
						end
					end
				end
			end)
			if AutoPair then
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "This may fling you or get you killed. Paired with anti void for less chance of death.",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			else
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "This may fling you or get you killed. Pair with anti void to have less chance of death.",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			end
		else
			if conn then
				conn:Disconnect()
			end
			if workspace:FindFirstChild("TeleportToGround") then
				workspace:FindFirstChild("TeleportToGround").Parent = gamecorrections
			end
		end
	end
})
local TeleportBackWhenAntiGrabbedToggle = Invincibility:AddToggle({
	Name = "Teleport Back When Anti-Grabbed",
	Default = true,
	Callback = function(Value)
		TeleportBackAntiGrab = Value
	end
})
AntiKickGrab = false
local AKGToggler = Invincibility:AddToggle({
	Name = "Anti-Kick Grab",
	Callback = function(Value)
		AntiKickGrab = Value
		local conn
		if AntiKickGrab then
			local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
			if character then
				local HRP = character:FindFirstChild("HumanoidRootPart")
				if HRP then
					local FPP = HRP:FindFirstChild("FirePlayerPart")
					if FPP then
						conn = FPP.ChildAdded:Connect(function(child)
							if FPP.Name == "PartOwner" and AntiKickGrab then
								local args = {[1] = HRP, [2] = 0}
								game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
								AKGToggler:Set(true)
								OrionLib:MakeNotification({
									Name = "Note",
									Content = "Detected kick grab being used on you.",
									Image = "rbxassetid://4483345998",
									Time = 10
								})
							end
						end)
					end
				end
			end
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
AntiKickAura = false
local conn
Invincibility:AddToggle({
	Name = "Anti-Kick Aura",
	Callback = function(Value)
		AntiKickAura = Value
		task.defer(function()
			conn = RunService.RenderStepped:Connect(function()
				local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
				if character then
					local HRP = character:FindFirstChild("HumanoidRootPart")
					if HRP then
						if not isnetworkowner(HRP) then
							local args = {[1] = character:WaitForChild("HumanoidRootPart"), [2] = 0}
							game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
						end
					end
				end
			end)
		end)
		if AntiKickAura then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "This does not prevent the anticheat from kicking you out when you use fly hacks.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
local AntiLagToggle = Invincibility:AddToggle({
	Name = "Anti-Lag",
	Callback = function(Value)
		AntiLag = Value
		if AntiLag then
			OrionLib:MakeNotification({
				Name = "Warning",
				Content = "Other player's heads may not move and their grab lines will be invisible while anti lag is on.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
			game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = false
		else
			game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = true
		end
	end
})
local InvisLineToggle
InvisLineToggle = MainOther:AddToggle({
	Name = "Invisible Line",
	Callback = function(Value)
		InvisLineToggle = Value
		if InvisLineToggle then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "Your grab line will be visible for your client.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
		end
	end
})
workspace.ChildAdded:Connect(function(model)
	if model.Name == "GrabParts" and InvisLineToggle then
		game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("CreateGrabLine"):FireServer()
	end
end)
local AntiFire = false
Invincibility:AddToggle({
	Name = "Anti-Fire",
	Callback = function(Value)
		AntiFire = Value
		if AntiFire then
			task.defer(function()
				while true do
					if not AntiFire then break end
					local getHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
					local where = workspace.Map.FactoryIsland
					if getHRP and where then
						for _, v in pairs(where:GetChildren()) do
							if v:IsA("Model") and v.Name == "PoisonContainer" then
								local extinguishpart = v:FindFirstChild("ExtinguishPart")
								if extinguishpart then
									extinguishpart.CFrame = getHRP.CFrame
									extinguishpart.Transparency = 1
									extinguishpart.Size = Vector3.new(1, 4.5, 1)
									task.wait(0.15)
									extinguishpart.Size = Vector3.new(1, 1.5, 1)
									for _, Destroy in pairs(extinguishpart:GetChildren()) do
										if Destroy:IsA("Texture") then
											Destroy:Destroy()
										end	
									end
								end
							end
						end
					end
					task.wait(0.05)
				end
			end)
		end
	end
})
function TeleportAndKill(Target)
	local PlayerTarget = Target
	if PlayerTarget then
		if not PlayerTarget:WaitForChild("InPlot").Value then
			local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
			if Character then
				local HRP = Character:FindFirstChild("HumanoidRootPart")
				local BeforeTPCFrame = HRP.CFrame
				local TargetChar = PlayerTarget.Character
				local TargetHum = TargetChar:FindFirstChild("Humanoid")
				local TargetHRP = TargetChar:FindFirstChild("HumanoidRootPart")

				if TargetChar and TargetHum and TargetHRP and TargetHRP:FindFirstChild("FirePlayerPart") then
					task.defer(function()
						while wait() do
							if TargetHum:GetState() == Enum.HumanoidStateType.Dead then
								HRP.CFrame = BeforeTPCFrame
								break
							elseif TargetHum.Health > 0 then
								for i = 1,10 do
									local SetNetworkOwnerArgs = {[1] = TargetHRP:FindFirstChild("FirePlayerPart"), [2] = HRP.CFrame}
									HRP.CFrame = TargetHRP.CFrame
									TargetHum:ChangeState(Enum.HumanoidStateType.Dead, true)
									game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
									wait()
								end
							end
						end
					end)
				end
			end
		end
	end
end
function TeleportAndKick(Target)
	local PlayerTarget = Target
	if PlayerTarget then
		if not PlayerTarget:WaitForChild("InPlot").Value then
			local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
			if Character then
				local HRP = Character:FindFirstChild("HumanoidRootPart")
				local BeforeTPCFrame = HRP.CFrame
				local TargetChar = PlayerTarget.Character or PlayerTarget.CharacterAdded:Wait()
				local TargetHum = TargetChar:FindFirstChild("Humanoid")
				local TargetHRP = TargetChar:FindFirstChild("HumanoidRootPart")
				if TargetChar and TargetHum and TargetHRP and TargetHRP:FindFirstChild("FirePlayerPart") then
					local TargetFPP = TargetHRP.FirePlayerPart
					local FlyVelocity = TemplateBV:Clone()
					FlyVelocity.Parent = nil
					FlyVelocity.Velocity = Vector3.new(math.random(-25, 25), 75, math.random(-25, 25))
					FlyVelocity.Name = "FlyVelocity"
					task.defer(function()
						while wait() do
							if TargetHum.Health > 0 then
								if TargetKickType == "Float" then
									local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
									HRP.CFrame = TargetHRP.CFrame
									game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
									TargetHum:ChangeState(Enum.HumanoidStateType.Freefall)
									FlyVelocity.Parent = TargetFPP
									if TargetFPP:FindFirstChild("FlyVelocity") and isnetworkowner(TargetHRP) then
										HRP.CFrame = BeforeTPCFrame
										break
									end
								elseif TargetKickType == "Silent" then
									local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
									HRP.CFrame = TargetHRP.CFrame
									game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs)) 
									TargetHum:ChangeState(Enum.HumanoidStateType.Freefall)
									TargetHum.HipHeight = 2.6
									if isnetworkowner(TargetHRP) then
										HRP.CFrame = BeforeTPCFrame
										break
									end
								end
							end
						end
					end)
				end
			end
		end
	end
end
local TargetDropdown

function TargetAddToTargetList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if Player ~= LocalPlayer and not table.find(KillTargets, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(KillTargets, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdateAndRefresh(TargetDropdown, KillTargets)
	end
end

function TargetRemoveFromTargetList(Player)
	local PlayerName = Player.Name
	for i, v in ipairs(KillTargets) do
		if string.match(v, "^" .. PlayerName .. "%s") then
			table.remove(KillTargets, i)
			UpdateAndRefresh(TargetDropdown, KillTargets)
			break
		end
	end
end

local TargetPlayersDropdown = TargetsPlayersSection:AddDropdown({
	Name = "Players",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		TargetSelectedPlayer = Value
	end
})
TargetsPlayersSection:AddButton({
	Name = "Add To Targets List",
	Callback = function()
		local playerName = string.match(TargetSelectedPlayer, "^(%S+)%s")
		for _, Plr in pairs(Players:GetPlayers()) do
			if Plr.Name == playerName then
				for i, v in ipairs(TargetPlayerList) do
					if string.match(v, "^" .. playerName .. "%s") then
						table.remove(TargetPlayerList, i)
						UpdateAndRefresh(TargetPlayersDropdown, TargetPlayerList)
						break
					end
				end
				if not table.find(KillTargets, Plr.Name .. " { " .. Plr.DisplayName .. " }") then
					TargetAddToTargetList(Plr)
				end
				break
			end
		end
	end
})

TargetDropdown = TargetsTargetSection:AddDropdown({
	Name = "Targets",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		TargetSelectedTarget = Value
	end
})
TargetsTargetSection:AddButton({
	Name = "Remove From Targets List",
	Callback = function()
		local playerName = string.match(TargetSelectedTarget, "^(%S+)%s")
		TargetRemoveFromTargetList(game.Players:FindFirstChild(playerName))
	end
})

function TargetAddToPlrList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if Player ~= LocalPlayer and not table.find(TargetPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(TargetPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdateAndRefresh(TargetPlayersDropdown, TargetPlayerList)
	end
end

TargetsTargetSection:AddToggle({
	Name = "Auto Remove From Targets List On Leave",
	Default = true,
	Callback = function(Value)
		RemovePlayerWhenLeaveTarget = Value
	end
})
TargetSection:AddToggle({
	Name = "Loop Kill",
	Callback = function(Value)
		LoopKillTargetToggle = Value
		if LoopKillTargetToggle then
			task.defer(function()
				while LoopKillTargetToggle do
					for _, target in pairs(KillTargets) do
						local playerName = string.match(target, "^(%S+)%s")
						local PlayerTarget = Players:FindFirstChild(playerName)
						if PlayerTarget then
							TeleportAndKill(PlayerTarget)
							PlayerTarget.CharacterAdded:Connect(function(char)
								if LoopKillTargetToggle then
									TeleportAndKill(PlayerTarget)
								end
							end)
						end
						task.wait(DelayBeforeTeleportingToNextTarget)
					end
					wait()
				end
			end)
		end
	end
})
TargetSection:AddButton({
	Name = "Kill Once",
	Callback = function()
		for _, target in pairs(KillTargets) do
			local playerName = string.match(target, "^(%S+)%s")
			local PlayerTarget = Players:FindFirstChild(playerName)
			TeleportAndKill(PlayerTarget)
			task.wait(DelayBeforeTeleportingToNextTarget)
		end
	end
})

TargetSection:AddToggle({
	Name = "Loop Kick",
	Callback = function(Value)
		LoopKickTargetToggle = Value
		if LoopKickTargetToggle then
			task.defer(function()
				while LoopKickTargetToggle do
					for _, target in pairs(KillTargets) do
						local playerName = string.match(target, "^(%S+)%s")
						local PlayerTarget = Players:FindFirstChild(playerName)
						if PlayerTarget then
							TeleportAndKick(PlayerTarget)
							PlayerTarget.CharacterAdded:Connect(function(char)
								if LoopKickTargetToggle then
									TeleportAndKick(PlayerTarget)
								end
							end)
						end
						task.wait(DelayBeforeTeleportingToNextTarget)
					end
					wait()
				end
			end)
		end
	end
})

TargetSection:AddButton({
	Name = "Kick Once",
	Callback = function()
		for _, target in pairs(KillTargets) do
			local playerName = string.match(target, "^(%S+)%s")
			local PlayerTarget = Players:FindFirstChild(playerName)
			TeleportAndKick(PlayerTarget)
			task.wait(DelayBeforeTeleportingToNextTarget)
		end
	end
})
TargetSection:AddDropdown({
	Name = "Kick Type",
	Default = "Float",
	Options = {"Float","Silent"},
	Callback = function(Value)
		TargetKickType = Value
	end
})
TargetSection:AddSlider({
	Name = "Delay Before Teleporting To Next Target",
	Min = 0,
	Max = 1,
	Default = 0.20,
	Increment = 0.05,
	ValueName = "Delay",
	Callback = function(Value)
		DelayBeforeTeleportingToNextTarget = Value
	end  
})
function grab(Detector,blobTargetHRP,blobmanSeatAndOwnerScript,grabtype)
	if grabtype == "Left" then
		local BlobmanSeatAndOwnerScriptArgs = {     
			[1] = Detector,     
			[2] = blobTargetHRP,    
			[3] = Detector:FindFirstChild("LeftWeld"),
		}  
		blobmanSeatAndOwnerScript:WaitForChild("CreatureGrab"):FireServer(unpack(BlobmanSeatAndOwnerScriptArgs))
	elseif grabtype == "Right" then
		local BlobmanSeatAndOwnerScriptArgs = {     
			[1] = Detector,     
			[2] = blobTargetHRP,    
			[3] = Detector:FindFirstChild("RightWeld"),
		}  
		blobmanSeatAndOwnerScript:WaitForChild("CreatureGrab"):FireServer(unpack(BlobmanSeatAndOwnerScriptArgs))
	end
end
function ungrab(Detector,blobTargetHRP,blobmanSeatAndOwnerScript,ungrabtype)
	if ungrabtype == "Left" then
		local BlobmanSeatAndOwnerScriptArgs = {     
			[1] = Detector:FindFirstChild("LeftWeld"),     
			[2] = blobTargetHRP,   
		}  
		blobmanSeatAndOwnerScript:WaitForChild("CreatureDrop"):FireServer(unpack(BlobmanSeatAndOwnerScriptArgs))
	elseif ungrabtype == "Right" then
		local BlobmanSeatAndOwnerScriptArgs = {     
			[1] = Detector:FindFirstChild("RightWeld"),     
			[2] = blobTargetHRP
		}  
		blobmanSeatAndOwnerScript:WaitForChild("CreatureDrop"):FireServer(unpack(BlobmanSeatAndOwnerScriptArgs))
	end
end
local BlobmanDropdown
local function UpdatePlrList(Dropdown, Table)
	local NewList = {}

	for _, name in pairs(Table) do
		if typeof(name) == "string" then
			table.insert(NewList, name)
		end
	end

	Dropdown:Refresh(NewList, true)
end

function AddToTargetList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if Player ~= LocalPlayer and not table.find(BlobTarget, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(BlobTarget, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdatePlrList(BlobmanDropdown, BlobTarget)
	end
end

function RemoveFromTargetList(Player)
	local PlayerName = Player.Name
	for i, v in ipairs(BlobTarget) do
		if string.match(v, "^" .. PlayerName .. "%s") then
			table.remove(BlobTarget, i)
			UpdatePlrList(BlobmanDropdown, BlobTarget)
			break
		end
	end
end

BlobmanPlayerList = {}
local BlobmanSelectedTarget
local BlobmanSelectedPlayer

local BlobmanPlayersDropdown = BlobPlayersSection:AddDropdown({
	Name = "Players",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		BlobmanSelectedTarget = Value
	end
})

BlobPlayersSection:AddButton({
	Name = "Add To Blobman Loop",
	Callback = function()
		local playerName = string.match(BlobmanSelectedTarget, "^(%S+)%s")
		for _, Plr in pairs(Players:GetPlayers()) do
			if Plr.Name == playerName then
				for i, v in ipairs(BlobmanPlayerList) do
					if string.match(v, "^" .. playerName .. "%s") then
						table.remove(BlobmanPlayerList, i)
						UpdatePlrList(BlobmanPlayersDropdown, BlobmanPlayerList)
						break
					end
				end
				if not table.find(BlobTarget, Plr.Name .. " { " .. Plr.DisplayName .. " }") then
					AddToTargetList(Plr)
				end
				break
			end
		end
	end
})

BlobmanDropdown = BlobTargetsSection:AddDropdown({
	Name = "Blobman Targets",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		BlobmanSelectedPlayer = Value
	end
})
BlobTargetsSection:AddButton({
	Name = "Remove From Targets List",
	Callback = function()
		local playerName = string.match(BlobmanSelectedPlayer, "^(%S+)%s")
		RemoveFromTargetList(game.Players:FindFirstChild(playerName))
	end
})

function BlobmanAddToPlrList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if Player ~= LocalPlayer and not table.find(BlobmanPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(BlobmanPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdatePlrList(BlobmanPlayersDropdown, BlobmanPlayerList)
	end
end

function Bring(Blobman,Plr)
	if Plr.Character then
		local TargetHRP = Plr.Character:FindFirstChild("HumanoidRootPart")
		local BSAOS = Blobman.BlobmanSeatAndOwnerScript
		if TargetHRP and Blobman then
			grab(Blobman:FindFirstChild("RightDetector"),TargetHRP,BSAOS,"Right")
			task.wait(0.1)
			ungrab(Blobman:FindFirstChild("RightDetector"),TargetHRP,BSAOS,"Right")

			grab(Blobman:FindFirstChild("LeftDetector"),TargetHRP,BSAOS,"Left")
			task.wait(0.1)
			ungrab(Blobman:FindFirstChild("LeftDetector"),TargetHRP,BSAOS,"Left")
		end
	end
end
BlobTargetsSection:AddToggle({
	Name = "Auto Remove From Targets List On Leave",
	Default = true,
	Callback = function(Value)
		RemovePlayerWhenLeaveBlobman = Value
	end
})
BlobmanLoopKick = false
BlobSection:AddToggle({
	Name = "Loop",
	Callback = function(Value)
		BlobmanLoopKick = Value
		if not LocalPlayer:WaitForChild("InPlot").Value then
			BlobmanLocation = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
		else
			BlobmanLocation = workspace.PlotItems
		end
		task.defer(function()
			while wait(0.2) do
				if not BlobmanLoopKick then break end
				for _, Blobman in pairs(BlobmanLocation:GetDescendants()) do
					if Blobman.Name == "CreatureBlobman" then
						local seat = Blobman:FindFirstChild("VehicleSeat")
						if seat and seat.Occupant and seat.Occupant.Parent then
							if game.Players:GetPlayerFromCharacter(seat.Occupant.Parent) == LocalPlayer and BlobTarget then
								local BSAOS = Blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
								for _, target in pairs(BlobTarget) do
									local playerName = string.match(target, "^(%S+)%s")
									local PlayerTarget = game.Players:FindFirstChild(playerName)
									if PlayerTarget then
										local TargetHRP = PlayerTarget.Character:FindFirstChild("HumanoidRootPart")
										task.defer(function()
											if Blobman then
												grab(Blobman:FindFirstChild("LeftDetector"), TargetHRP, BSAOS, "Left")
												grab(Blobman:FindFirstChild("RightDetector"), TargetHRP, BSAOS, "Right")
												wait()
												grab(Blobman:FindFirstChild("LeftDetector"), TargetHRP, BSAOS, "Left")
												ungrab(Blobman:FindFirstChild("RightDetector"), TargetHRP, BSAOS, "Right")
												wait()
												ungrab(Blobman:FindFirstChild("LeftDetector"), TargetHRP, BSAOS, "Left")
												grab(Blobman:FindFirstChild("RightDetector"), TargetHRP, BSAOS, "Right")
												wait()
											end
										end)
									end
								end
							end
						end
					end
				end
			end
		end)
	end
})
BlobSection:AddButton({
	Name = "Bring",
	Callback = function()

		if not LocalPlayer:WaitForChild("InPlot").Value then
			BlobmanLocation = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
		else
			BlobmanLocation = workspace.PlotItems
		end
		for _,v in pairs(BlobmanLocation:GetDescendants()) do
			if v.Name == "CreatureBlobman" then
				local seat = v:FindFirstChild("VehicleSeat")
				if seat and seat.Occupant and seat.Occupant.Parent then
					if game.Players:GetPlayerFromCharacter(seat.Occupant.Parent) == LocalPlayer and BlobTarget then
						for _, target in pairs(BlobTarget) do
							local playerName = string.match(target, "^(%S+)%s")
							local BSAOS = v:FindFirstChild("BlobmanSeatAndOwnerScript")
							local PlayerTarget = game.Players:FindFirstChild(playerName)
							local TargetHRP = PlayerTarget.Character:FindFirstChild("HumanoidRootPart")
							task.defer(function()
								if v then
									Bring(v,PlayerTarget)
								end
							end)
							wait(0.2)
						end
					end
				end
			end
		end
	end
})
BlobSection:AddButton({
	Name = "Bring All",
	Callback = function(Value)

		if not LocalPlayer:WaitForChild("InPlot").Value then
			BlobmanLocation = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
		else
			BlobmanLocation = workspace.PlotItems
		end
		for _,Blobman in pairs(BlobmanLocation:GetDescendants()) do
			if Blobman.Name == "CreatureBlobman" then
				local seat = Blobman:FindFirstChild("VehicleSeat")
				if seat and seat.Occupant and seat.Occupant.Parent then
					if game.Players:GetPlayerFromCharacter(seat.Occupant.Parent) == LocalPlayer then
						for _,v in pairs(Players:GetPlayers()) do
							local char = v.Character
							if v ~= LocalPlayer and v and char and not IsInvincible(v) then
								Bring(Blobman,v)
							end
							wait(0.2)
						end
					end
				end
			end
		end
	end
})
BlobmanLoopKickAll = false
BlobSection:AddToggle({
	Name = "Loop Kick All",
	Callback = function(Value)
		BlobmanLoopKickAll = Value

		if not LocalPlayer:WaitForChild("InPlot").Value then
			BlobmanLocation = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
		else
			BlobmanLocation = workspace.PlotItems
		end
		task.defer(function()
			while true do
				if not BlobmanLoopKickAll then break end
				for _, Blobman in pairs(BlobmanLocation:GetDescendants()) do
					if Blobman.Name == "CreatureBlobman" then
						local seat = Blobman:FindFirstChild("VehicleSeat")
						if seat and seat.Occupant and seat.Occupant.Parent then
							local PlayerOccupant = game.Players:GetPlayerFromCharacter(seat.Occupant.Parent)
							if IsFriend(PlayerOccupant) or PlayerOccupant == LocalPlayer then
								for _,v in pairs(Players:GetPlayers()) do
									local char = v.Character
									if v ~= LocalPlayer and v and char and not IsInvincible(v) then
										local BSAOS = Blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
										local TargetHRP = char:FindFirstChild("HumanoidRootPart")
										if TargetHRP and Blobman then
											grab(Blobman:FindFirstChild("LeftDetector"),TargetHRP,BSAOS,"Left")
											grab(Blobman:FindFirstChild("RightDetector"),TargetHRP,BSAOS,"Right")
										end
									end
									task.wait(BlobmanGrabSpeed)
								end
							end
						end
					end
				end
				task.wait(TimeDelay)
			end
		end)
	end
})
BlobSection:AddSlider({
	Name = "Delay Before Next Loop",
	Min = 0.05,
	Max = 5,
	Default = 0.20,
	Increment = 0.05,
	ValueName = "Delay",
	Callback = function(Value)
		TimeDelay = Value
	end  
})
BlobSection:AddButton({
	Name = "Kick All Once",
	Callback = function()

		if not LocalPlayer:WaitForChild("InPlot").Value then
			BlobmanLocation = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
		else
			BlobmanLocation = workspace.PlotItems
		end
		for _,Blobman in pairs(BlobmanLocation:GetDescendants()) do
			if Blobman.Name == "CreatureBlobman" then
				local seat = Blobman:FindFirstChild("VehicleSeat")
				if seat and seat.Occupant and seat.Occupant.Parent then
					if game.Players:GetPlayerFromCharacter(seat.Occupant.Parent) == LocalPlayer then
						for _,v in pairs(Players:GetPlayers()) do
							local char = v.Character
							if v ~= LocalPlayer and v and char and not IsInvincible(v) then
								local BSAOS = Blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
								local TargetHRP = char:FindFirstChild("HumanoidRootPart")
								if TargetHRP and Blobman then
									grab(Blobman:FindFirstChild("LeftDetector"),TargetHRP,BSAOS,"Left")
									grab(Blobman:FindFirstChild("RightDetector"),TargetHRP,BSAOS,"Right")
								end
							end
							task.wait(BlobmanGrabSpeed)
						end
					end
				end
			end
		end
	end
})
BlobSection:AddSlider({
	Name = "Blobman Grab Speed",
	Min = 0.01,
	Max = 1,
	Default = 0.025,
	Increment = 0.001,
	ValueName = "Grab Speed",
	Callback = function(Value)
		BlobmanGrabSpeed = Value
	end  
})

function ImitateBlackHole(CorePos: Vector3, Part: Part, Type: string)
	if not Part:FindFirstChild("BlackholeVelocity") then
		if Part and CorePos and Type then
			if Type == "Ring" or Type == "Further Ring" or Type == "Farthest Ring" then
				local BV = TemplateBV:Clone()
				BV.Parent = Part
				BV.Name = "BlackholeVelocity"
				local Angle = math.random() * 5 * math.pi
				local Amplitude = 20
				local Frequency = math.sqrt(BlackholeSpinningSpeed) / 10
				local RadiusMultiplier = (Type == "Farthest Ring") and 1.5 or (Type == "Further Ring") and 1.25 or 1
				task.defer(function()
					while Part do
						if not BlackholeToggle then break end
						Angle = Angle + BlackholeSpinningSpeed / 100
						local NewPosition = CorePos + Vector3.new(math.cos(Angle) * BlackholeRingRadius * RadiusMultiplier, Amplitude * math.sin(tick() * 2 * math.pi * Frequency), math.sin(Angle) * BlackholeRingRadius * RadiusMultiplier)
						BV.Velocity = (NewPosition - Part.Position) * BlackholeSpinningSpeed 
						task.wait(0.025)
					end
				end)
			elseif Type == "Core" then
				local BV = TemplateBV:Clone()
				BV.Parent = Part
				BV.Name = "BlackholeVelocity"
				local Angle = math.random() * 5 * math.pi
				local Amplitude = 20
				local Frequency = math.sqrt(BlackholeSpinningSpeed) / 10
				task.defer(function()
					while Part do
						if not BlackholeToggle then break end
						Angle = Angle + BlackholeSpinningSpeed / 100
						local NewPosition = CorePos + Vector3.new(math.cos(Angle) * 5, Amplitude * math.sin(tick() * 2 * math.pi * Frequency), math.sin(Angle) * 5)
						BV.Velocity = (NewPosition - Part.Position) * 15
						task.wait(0.025)
					end
				end)
			end
		end
	end
end


BlackholeSection:AddToggle({
	Name = "Blackhole",
	Callback = function(Value)
		BlackholeToggle = Value
		if not BlackholeToggle then
			task.defer(function()
				task.wait(2.5)
				if not BlackholeToggle then
					for i, BlackholeVelocity in pairs(workspace:GetDescendants()) do
						if BlackholeVelocity.Name == "BlackholeVelocity" then 
							BlackholeVelocity:Destroy() 
						end
					end
				end
			end)
		end
	end
})
BlackholeSection:AddSlider({
	Name = "Ring Radius",
	Min = 0,
	Max = 250,
	Default = 50,
	Increment = 1,
	ValueName = "Radius",
	Callback = function(Value)
		BlackholeRingRadius = Value
	end  
})
BlackholeSection:AddSlider({
	Name = "Spinning Speed",
	Min = 0,
	Max = 15,
	Default = 10,
	Increment = 1,
	ValueName = "Speed",
	Callback = function(Value)
		BlackholeSpinningSpeed = Value
	end  
})

function GetAndSendToys(CorePos, Type)
	local Character = LocalPlayer.Character
	if Character then
		local HRP = Character:FindFirstChild("HumanoidRootPart")
		if HRP then
			for i, Toy in pairs(workspace:GetDescendants()) do
				if Toy:IsA("Model") and string.find(Toy.Parent.Name, "SpawnedInToys") then
					local PrimaryPart = Toy.PrimaryPart
					if PrimaryPart then
						if (PrimaryPart.Position - HRP.Position).Magnitude <= LocalPlayer:FindFirstChild("CurrentReach").Value then
							local args = {[1] = PrimaryPart, [2] = HRP.CFrame}
							game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(args))
							task.wait()
							ImitateBlackHole(CorePos, PrimaryPart, Type)
							game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(args))
						end
					end
				end
			end
		end
	end
end

BlackholeSection:AddButton({
	Name = "Send to Core",
	Callback = function()
		GetAndSendToys(Vector3.new(0, 100, 0), "Core")
	end    
})
BlackholeSection:AddButton({
	Name = "Send to Ring",
	Callback = function()
		GetAndSendToys(Vector3.new(0, 100, 0), "Ring")
	end    
})
BlackholeSection:AddButton({
	Name = "Send to Further Ring",
	Callback = function()
		GetAndSendToys(Vector3.new(0, 100, 0), "Further Ring")
	end
})
BlackholeSection:AddButton({
	Name = "Send to Farthest Ring",
	Callback = function()
		GetAndSendToys(Vector3.new(0, 100, 0), "Farthest Ring")
	end
})

NPCsSection:AddToggle({
	Name = "Control NPCs",
	Callback = function(Value)
		ControlNPC = Value
	end
})
local NPCsTargetDropdown = NPCsSection:AddDropdown({
	Name = "Select Target",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		NPCsTarget = Value
	end    
})
function NPCsAddToPlrList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if not table.find(NPCsPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(NPCsPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdateAndRefresh(NPCsTargetDropdown, NPCsPlayerList)
	end
end

function ControlNPCs(Type, Delay)
	if ControlNPC then
		task.defer(function()
			while task.wait(Delay) do
				if Type == "Follow" and not FollowingNPCs then break end
				if Type == "Fling" and not FlingingNPCs then break end
				if Type == "Teleport" and not LoopTeleportingNPCs then break end
				if Type == "Bang" and not BangingNPCs then break end
				for _, Plr in pairs(Players:GetPlayers()) do
					local PlrName = string.match(NPCsTarget, "^(%S+)%s")
					local PlrTarget = Players:FindFirstChild(PlrName)
					if PlrTarget and PlrTarget.Character then
						local TargetHRP = PlrTarget.Character:FindFirstChild("HumanoidRootPart")
						if TargetHRP then

							for i, MyDecoy in pairs(ToyFolder:GetChildren()) do
								if MyDecoy.Name == "YouDecoy" then
									local DecoyHumanoid = MyDecoy:FindFirstChild("Humanoid")
									local DecoyHRP = MyDecoy:FindFirstChild("HumanoidRootPart")
									if DecoyHumanoid and DecoyHRP then
										task.defer(function()

											if Type == "Follow" or Type == nil then
												DecoyHumanoid.WalkSpeed = NPCsWalkSpeed
												DecoyHumanoid.UseJumpPower = true
												DecoyHumanoid.JumpPower = NPCsJumpPower
												DecoyHumanoid:MoveTo(TargetHRP.Position)

											elseif Type == "Fling" then
												local Bambam = Instance.new("BodyAngularVelocity")
												Bambam.Name = "Gyatt"
												Bambam.Parent = DecoyHRP
												Bambam.MaxTorque = Vector3.new(0, math.huge, 0)
												Bambam.AngularVelocity = Vector3.new(0, 100000 ,0)
												Bambam.P = math.huge

											elseif Type == "Teleport" then
												DecoyHRP.CFrame = TargetHRP.CFrame
												StopVelocity(DecoyHRP, 0)

											elseif Type == "Bang" then
												if not DecoyHRP:FindFirstChild("BodyVelocity") then
													DecoyHRP.CFrame = TargetHRP.CFrame + -TargetHRP.CFrame.LookVector * 1.5
													DecoyHumanoid.Sit = true
													task.wait(0.05)
													DecoyHumanoid.Sit = false
													DecoyHRP.CFrame = TargetHRP.CFrame + -TargetHRP.CFrame.LookVector * 3.5
													task.wait(0.05)
												end

											end

										end)
									end
								end
							end
						end
					end
				end
			end
		end)
	end
end

NPCsSection:AddToggle({
	Name = "Make NPCs Follow Player",
	Callback = function(Value)
		FollowingNPCs = Value
		if FollowingNPCs then
			ControlNPCs("Follow", 0.05)
		end
	end
})
NPCsSection:AddSlider({
	Name = "NPCs Speed",
	Min = 0,
	Max = 100,
	Default = 16,
	Increment = 5,
	ValueName = "Speed",
	Callback = function(Value)
		NPCsWalkSpeed = Value
	end 
})
NPCsSection:AddSlider({
	Name = "NPCs Jump Power",
	Min = 0,
	Max = 500,
	Default = 50,
	Increment = 5,
	ValueName = "Power",
	Callback = function(Value)
		NPCsJumpPower = Value
	end 
})

NPCsSection:AddToggle({
	Name = "Make NPCs Teleport To Player",
	Callback = function(Value)
		LoopTeleportingNPCs = Value
		if LoopTeleportingNPCs then
			ControlNPCs("Teleport", 0.5)
		end
	end
})
NPCsSection:AddSlider({
	Name = "Telepoting Interval",
	Min = 0,
	Max = 5,
	Default = 0.05,
	Increment = 0.05,
	ValueName = "Interval",
	Callback = function(Value)
		TeleportInterval = Value
	end 
})

local BangingNPCsToggle = NPCsSection:AddToggle({
	Name = "Make NPCs Bang Player",
	Callback = function(Value)
		BangingNPCs = Value
		if BangingNPCs then
			ControlNPCs("Bang", 0.5)
		end
	end
})

NPCsSection:AddToggle({
	Name = "Make NPCs Fling",
	Callback = function(Value)
		FlingingNPCs = Value
		if FlingingNPCs then
			ControlNPCs("Fling", 0.5)
		end
	end
})

function start()
	local anchor = LocalPlayer.Character:WaitForChild("GrabbingScript")
	function grablock()
		if anchor then
			anchor.Enabled = false
			anchor.Enabled = true
			for _,v in pairs(workspace:GetChildren()) do 
				if v:IsA("Model") and v.Name == "GrabParts" then
					local beampart = v:FindFirstChild("BeamPart")
					local grabpart = v:FindFirstChild("GrabPart")
					if grabpart then
						grabpart.Transparency = 1
						if grabpart:FindFirstChild("BeamSound") then
							grabpart:FindFirstChild("BeamSound"):Destroy()
						end
					end
					if beampart then
						beampart:Destroy()
					end
				end
			end
		end
	end
	LocalPlayer.CharacterAdded:Connect(function()
		anchor = LocalPlayer.Character:WaitForChild("GrabbingScript")
	end)
	function deletelocks()
		for _,v in pairs(workspace:GetChildren()) do
			if v:IsA("Model") and v.Name == "GrabParts" then
				if v then
					v:Destroy()
				end
			end
		end
	end
	function hardenlock()
		for _,v in pairs(workspace:GetChildren()) do
			if v:IsA("Model") and v.Name == "GrabParts" then
				v:FindFirstChild("GrabPart").Anchored = not v:FindFirstChild("GrabPart").Anchored
			end
		end
	end
end
start()
BindsTab:AddBind({
	Name = "Click Teleport",
	Default = Enum.KeyCode.Z,
	Hold = false,
	Callback = function()
		if LocalPlayer.Character then
			local HRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			if HRP then
				HRP.CFrame = Mouse.Hit
				StopVelocity(HRP, 0)
			end
		end
	end
})
BindsTab:AddBind({
	Name = "Grab Lock Object",
	Default = Enum.KeyCode.M,
	Hold = false,
	Callback = function()
		if SoftAnchor then
			grablock()
		end
	end
})

BindsTab:AddBind({
	Name = "Harden Grab Locks",
	Default = Enum.KeyCode.H,
	Hold = false,
	Callback = function()
		if SoftAnchor then
			hardenlock()
		end
	end
})
BindsTab:AddBind({
	Name = "Delete Locks",
	Default = Enum.KeyCode.C,
	Hold = false,
	Callback = function()
		if SoftAnchor then
			deletelocks()
		end
	end
})
local WasOn
Invisibility = false
CharacterSection:AddToggle({
	Name = "Invisibility",
	Callback = function(Value)
		Invisibility = Value
		if Invisibility then
			if InvisLineToggle then
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "Invisibility, you can't be seen by anyone. Pair with invisible line to be truly invisible!",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			else
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "Invisibility, you can't be seen by anyone.",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			end
			if AutoPair then
				if not InvisLine then
					WasOn = true
					InvisLine = true
					InvisLineToggle:Set(true)
				end
			end
			task.defer(function()
				while true do
					if not Invisibility then break end
					if Invisibility then
						local args = {
							[1] = CFrame.new(-1000000,-1000000,-1000000),
							[2] = CFrame.new(-1000000,-1000000,-1000000),
							[3] = CFrame.new(-1000000,-1000000,-1000000),
							[4] = "high"
						}
						local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
						remote:FireServer(unpack(args))
						local args = {
							[1] = CFrame.new(-1000000,-1000000,-1000000),
							[2] = CFrame.new(-1000000,-1000000,-1000000),
							[3] = CFrame.new(-1000000,-1000000,-1000000),
							[4] = "medium"
						}
						local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
						remote:FireServer(unpack(args))
						local args = {
							[1] = CFrame.new(-1000000,-1000000,-1000000),
							[2] = CFrame.new(-1000000,-1000000,-1000000),
							[3] = CFrame.new(-1000000,-1000000,-1000000),
							[4] = "low"
						}
						local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
						remote:FireServer(unpack(args))
						local args = {
							[1] = CFrame.new(-1000000,-1000000,-1000000),
							[2] = CFrame.new(-1000000,-1000000,-1000000),
							[3] = CFrame.new(-1000000,-1000000,-1000000),
							[4] = "lowest"
						}
						local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("Look")
						remote:FireServer(unpack(args))
						local args = {
							[1] = "end"
						}
						local remote = game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("ChatTyping")
						remote:FireServer(unpack(args))
					end
					wait()
				end
			end)
		else
			if WasOn then
				WasOn = false
				InvisLine = false
				InvisLineToggle:Set(false)
			end
		end
	end
})
local FOVring
local AimbotToggle = false
local loop
local AimbotToggler = CameraSection:AddToggle({
	Name = "Aim-Lock",
	Default = false,
	Callback = function(Value)
		AimbotToggle = Value
		if AimbotToggle then
			local teamCheck = false
			local fov = AimbotFOV
			local Transparency
			local smoothing = 1

			local RunService = game:GetService("RunService")

			FOVring = Drawing.new("Circle")
			FOVring.Visible = true
			FOVring.Thickness = 1.5
			FOVring.Radius = fov
			FOVring.Transparency = 1
			FOVring.Color = Color3.fromRGB(50,255,50)
			FOVring.Position = workspace.CurrentCamera.ViewportSize / 2

			function CheckForPart(Parent: Instance, Name: string) 
				if Parent:FindFirstChild(Name) then 
					return true
				else
					return false
				end
			end
			function GetClosest(CameraCFrame: CFrame)
				local RayLookVector = Ray.new(CameraCFrame.Position, CameraCFrame.LookVector).Unit
				local Target = nil
				local Magnitude = math.huge
			
				for _, Plr in pairs(Players:GetPlayers()) do
					if Plr ~= LocalPlayer and Plr.Character then
						if CheckForPart(Plr.Character, "Torso") then
							local CurrentAimTarget: Part = Plr.Character:FindFirstChild("Torso")
							local MagBuf = (CurrentAimTarget.Position - RayLookVector:ClosestPoint(CurrentAimTarget.Position)).Magnitude
							if MagBuf < Magnitude then
								Magnitude = MagBuf
								Target = CurrentAimTarget
							end
						end
					end
				end
				return Target
			end

			loop = RunService.Heartbeat:Connect(function()
				local Pressed = UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2)
				local ViewportSize = Camera.ViewportSize/2

				if Pressed then
					local CurrentTargetPart = GetClosest(Camera.CFrame)
					if CurrentTargetPart then
						local ScreenPoint = Camera:WorldToScreenPoint(CurrentTargetPart.Position)
						local Vector2SP = Vector2.new(ScreenPoint.X, ScreenPoint.Y)
						if (Vector2SP - ViewportSize).Magnitude < FOVring.Radius then
							Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, CurrentTargetPart.Position), 1)
						end
					end
				end
			end)
		else
			if loop and FOVring then
				loop:Disconnect()
				FOVring:Remove()
			end
		end
	end
})

CameraSection:AddSlider({
	Name = "FOV Ring",
	Min = 0,
	Max = 500,
	Default = 150,
	Increment = 1,
	ValueName = "Value",
	Callback = function(Value)
		AimbotFOV = Value
		if FOVring then
			FOVring.Radius = Value
		end
	end
})
CameraSection:AddToggle({
	Name = 'FOV Ring Visible',
	Default = true,
	Callback = function(Value)
		if FOVring then
			FOVring.Visible = Value
		end
	end
})
CameraSection:AddSlider({
	Name = "FieldOfView",
	Min = 0,
	Max = 120,
	Default = 70,
	Increment = 1,
	ValueName = "Value",
	Callback = function(Value)
		Camera.FieldOfView = Value
	end
})
local CFrameSpeed = false
CharacterSection:AddToggle({
	Name = 'Enable Speed',
	Callback = function(Value)
		CFrameSpeed = Value
		task.defer(function()
			while CFrameSpeed do
				task.wait()
				if (LocalPlayer.Character) and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then 
					LocalPlayer.Character:PivotTo(LocalPlayer.Character:GetPivot() + LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid").MoveDirection /20 * CFrameSpeedAmmount)
				end
			end
		end)
	end
})

CharacterSection:AddSlider({
	Name = "Speed ammount",
	Min = 1,
	Max = 50,
	Default = 1,
	Increment = 1,
	ValueName = "Speed",
	Callback = function(Value)
		CFrameSpeedAmmount = Value
	end
})
local JumpPower = false
CharacterSection:AddToggle({
	Name = 'Enable JumpPower',
	Callback = function(Value)
		JumpPower = Value
		task.defer(function()
			while JumpPower do
				wait(0.1)
				if (LocalPlayer.Character) and LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid") then 
					LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid").JumpPower = JumpPowerAmmount
				end
			end
		end)
	end
})
CharacterSection:AddSlider({
	Name = "JumpPower ammount",
	Min = 0,
	Max = 1000,
	Default = 24,
	Increment = 1,
	ValueName = "JumpPower",
	Callback = function(Value)
		JumpPowerAmmount = Value
	end
})
CharacterSection:AddToggle({
	Name = 'Enable Gravity',
	Callback = function(Value)
		GravityToggle = Value
		if not GravityToggle then
			workspace.Gravity = 100
		end
	end
})
CharacterSection:AddSlider({
	Name = "Gravity Ammount",
	Min = 0,
	Max = 1000,
	Default = 196,
	Increment = 1,
	ValueName = "GravityAmmount",
	Callback = function(Value)
		if GravityToggle then
			workspace.Gravity = Value
		end
	end
})
local flyjump
InfJump = false
CharacterSection:AddToggle({
	Name = 'Infinite jump',
	Callback = function(Value)
		InfJump = Value
		if InfJump then
			flyjump = UserInputService.JumpRequest:Connect(function()
				LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
			end)
		else
			if flyjump then
				flyjump:Disconnect()
			end
		end
	end
})
GrabPhysics:AddToggle({
	Name = "Lock Grab",
	Callback = function(Value)
		SoftAnchor = Value
		if SoftAnchor then
			OrionLib:MakeNotification({
				Name = "Controls:",
				Content = "M to anchor grab, \n C to unanchor grab, \n H to harden anchor",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
		end
	end
})
function TeleportAndKickForAntiAttacker(Target)
	local PlayerTarget = Target
	if PlayerTarget and not IsInvincible(PlayerTarget) then
		local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
		if Character then
			local HRP = Character:FindFirstChild("HumanoidRootPart")
			local BeforeTPCFrame = HRP.CFrame
			local TargetChar = PlayerTarget.Character or PlayerTarget.CharacterAdded:Wait()
			local TargetHum = TargetChar:FindFirstChild("Humanoid")
			local TargetHRP = TargetChar:FindFirstChild("HumanoidRootPart")
			if TargetChar and TargetHum and TargetHRP and TargetHRP:FindFirstChild("FirePlayerPart") then
				local TargetFPP = TargetHRP:FindFirstChild("FirePlayerPart")
				HRP.CFrame = TargetHRP.CFrame
				if TargetFPP then
					local FlyVelocity = TemplateBV:Clone()
					FlyVelocity.Parent = nil
					FlyVelocity.Velocity = Vector3.new(math.random(-25, 25), 75, math.random(-25, 25))
					FlyVelocity.Name = "FlyVelocity"
					local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
					game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
					if AntiAttackerKickType == "Float" then
						FlyVelocity.Parent = TargetFPP
					elseif AntiAttackerKickType == "Silent" then
						TargetHum.HipHeight = 2.6
					end
				end
			end
			task.defer(function()
				task.wait(0.25)
				HRP.CFrame = BeforeTPCFrame
			end)
		end
	end
end
function TeleportAndKillForAntiAttacker(Target)
	local PlayerTarget = Target
	if PlayerTarget then
		local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
		if Character then
			local HRP = Character:FindFirstChild("HumanoidRootPart")
			local BeforeTPCFrame = HRP.CFrame
			local TargetChar = PlayerTarget.Character
			local TargetHum = TargetChar:FindFirstChild("Humanoid")
			local TargetHRP = TargetChar:FindFirstChild("HumanoidRootPart")

			if TargetChar and TargetHum and TargetHRP and TargetHRP:FindFirstChild("FirePlayerPart") then
				HRP.CFrame = TargetHRP.CFrame
				local SetNetworkOwnerArgs = {[1] = TargetHRP:FindFirstChild("FirePlayerPart"), [2] = HRP.CFrame}
				game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
				TargetHum:ChangeState(Enum.HumanoidStateType.Dead)
				HRP.CFrame = BeforeTPCFrame
			end
			task.defer(function()
				task.wait(0.2)
				HRP.CFrame = BeforeTPCFrame
			end)
		end
	end
end
function anti(Descendant)
	if Descendant:FindFirstChild("LeftDetector") and Descendant:FindFirstChild("RightDetector") then
		Descendant:WaitForChild("LeftDetector"):WaitForChild("LeftAlignOrientation").Enabled = false
		Descendant:WaitForChild("LeftDetector"):WaitForChild("LeftWeld").Enabled = false
		Descendant:WaitForChild("RightDetector"):WaitForChild("RightAlignOrientation").Enabled = false
		Descendant:WaitForChild("RightDetector"):WaitForChild("RightWeld").Enabled = false
	end
end
function revertanti(Descendant)
	if Descendant:FindFirstChild("LeftDetector") and Descendant:FindFirstChild("RightDetector") then
		Descendant:WaitForChild("LeftDetector"):WaitForChild("LeftAlignOrientation").Enabled = true
		Descendant:WaitForChild("LeftDetector"):WaitForChild("LeftWeld").Enabled = true
		Descendant:WaitForChild("RightDetector"):WaitForChild("RightAlignOrientation").Enabled = true
		Descendant:WaitForChild("RightDetector"):WaitForChild("RightWeld").Enabled = true
	end
end
function BreakSpecificBlobman(Blobman,Detector)
	if LocalPlayer.Character and Blobman then
		local HRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
		local Torso = LocalPlayer.Character:FindFirstChild("Torso")
		if HRP and Torso then
			local VS = Blobman:FindFirstChild("VehicleSeat")
			if VS then
				local SW = VS:FindFirstChild("SeatWeld")
				if SW and SW.Part1 then
					local TargetHRP = SW.Part1.Parent:FindFirstChild("HumanoidRootPart")
					if TargetHRP then
						if Detector:lower() == "left" then
							local Left = {[1] = Blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"), [2] = HRP}
							Blobman.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(unpack(Left))
							local Left = {
								[1] = Blobman:FindFirstChild("LeftDetector"),
								[2] = TargetHRP,
								[3] = Blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
							}
							Blobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(Left))
						elseif Detector:lower() == "right" then
							local Right = {[1] = Blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"), [2] = HRP}
							Blobman.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(unpack(Right))

							local Right = {
								[1] = Blobman:FindFirstChild("RightDetector"),
								[2] = TargetHRP,
								[3] = Blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
							}
							Blobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(Right))
						end
					end
				end
			end
		end
	end
end
local conn
local conn2
local conn3
local conn4
local AntiBlobman = false
Invincibility:AddToggle({
	Name = "Anti-Blobman",
	Callback = function(Value)
		AntiBlobman = Value
		if AntiBlobman then
			local IsHeld = LocalPlayer:FindFirstChild("IsHeld")
			conn2 = IsHeld:GetPropertyChangedSignal("Value"):Connect(function()
				if IsHeld.Value then
					task.defer(function()
						wait()
						for i = 1,15 do
							local args = {[1] = LocalPlayer.Character:WaitForChild("HumanoidRootPart"), [2] = 0}
							game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
							wait()
						end
					end)
				end
			end)
			local function Prank(v)
				conn3 = v.Changed:Connect(function(prop)
					if conn then
						conn:Disconnect()
					end
					if typeof(v.Attachment0) == "Instance" then
						if v.Attachment0:IsDescendantOf(LocalPlayer.Character) and AntiBlobman then
							if Players:GetPlayerFromCharacter(v.Attachment0:FindFirstAncestorWhichIsA("Model")) == LocalPlayer then
								if v.Parent.Parent then
									local MyChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
									if MyChar then
										local MyHRP = MyChar:FindFirstChild("HumanoidRootPart")
										if MyHRP then
											local oldCF = MyHRP.CFrame
											local IsHeld = LocalPlayer:FindFirstChild("IsHeld")
											if v.Parent and v.Parent.Name == "LeftDetector" then
												BreakSpecificBlobman(v.Parent.Parent,"left")
											end
											if v.Parent and v.Parent.Name == "RightDetector" then
												BreakSpecificBlobman(v.Parent.Parent,"right")
											end
											conn = IsHeld:GetPropertyChangedSignal("Value"):Connect(function()
												if not IsHeld.Value then
													MyHRP.CFrame = oldCF
												end
											end)
											game.ReplicatedStorage.CharacterEvents.Struggle:FireServer()
										end
									end
								end
							end
						end
					end
				end)
			end
			for _,v in pairs(workspace:GetDescendants()) do
				if v.Name == "CreatureBlobman" and not v:IsDescendantOf(workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")) and AntiBlobman then
					if v and v:FindFirstChild("LeftDetector") and v:FindFirstChild("RightDetector") and v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld") and v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld") then
						Prank(v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"))
						Prank(v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"))
					end
				end
			end
			conn4 = workspace.DescendantAdded:Connect(function(v)
				if v.Name == "CreatureBlobman" and not v:IsDescendantOf(workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")) and AntiBlobman then
					wait(0.1)
					if v and v:FindFirstChild("LeftDetector") and v:FindFirstChild("RightDetector") and v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld") and v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld") then
						Prank(v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"))
						Prank(v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"))
					end
				end
			end)
		else
			if conn then
				conn:Disconnect()
			end
			if conn2 then
				conn2:Disconnect()
			end
			if conn3 then
				conn3:Disconnect()
			end
			if conn4 then
				conn4:Disconnect()
			end
		end
	end
})
local AntiBlobBringToggle = Invincibility:AddToggle({
	Name = "Anti-Blobman Bring",
	Callback = function(Value)
		AntiBlobmanBring = Value
		if AntiBlobmanBring then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "This does not prevent you from getting kicked if you are not trying to escape blobman",
				Image = "rbxassetid://4483345998",
				Time = 3
			})

			for _, Descendant in pairs(workspace:GetDescendants()) do
				if Descendant.Name == "CreatureBlobman" and Descendant.Parent ~= ToyFolder then
					anti(Descendant)
				end
			end

			workspace.DescendantAdded:Connect(function(Descendant)
				if AntiBlobmanBring and Descendant.Name == "CreatureBlobman" and Descendant.Parent ~= ToyFolder then
					wait(0.1)
					anti(Descendant)
				end
			end)
		else
			for _, Descendant in pairs(workspace:GetDescendants()) do
				if Descendant.Name == "CreatureBlobman" and Descendant.Parent ~= ToyFolder then
					revertanti(Descendant)
				end
			end
		end
	end
})

function BreakBlobmen()
	local function sigma(folder)
		if folder ~= ToyFolder then
			if LocalPlayer.Character then
				local HRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
				local Torso = LocalPlayer.Character:FindFirstChild("Torso")
				if HRP and Torso then
					for i, Blobman in pairs(folder:GetChildren()) do
						if Blobman.Name == "CreatureBlobman" then
							local VS = Blobman:FindFirstChild("VehicleSeat")
							if VS then
								local SW = VS:FindFirstChild("SeatWeld")
								if SW and SW.Part1 then
									local TargetHRP = SW.Part1.Parent:FindFirstChild("HumanoidRootPart")
									if TargetHRP then

										local Right = {[1] = Blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"), [2] = HRP}
										Blobman.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(unpack(Right))
										local Left = {[1] = Blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"), [2] = HRP}
										Blobman.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(unpack(Left))

										local Right = {
											[1] = Blobman:FindFirstChild("RightDetector"),
											[2] = TargetHRP,
											[3] = Blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
										}
										Blobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(Right))
										local Left = {
											[1] = Blobman:FindFirstChild("LeftDetector"),
											[2] = TargetHRP,
											[3] = Blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
										}
										Blobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(Left))
									end
								end
							end
						end
					end
				end
			end
		end
	end

	for i, ToyFolder in pairs(workspace:GetChildren()) do
		sigma(ToyFolder)
	end

	for i, PlotItem in pairs(workspace.PlotItems:GetChildren()) do
		sigma(PlotItem)
	end
end

MainBlobman:AddToggle({
	Name = "Break All Blobmen",
	Callback = function(Value)
		BreakAllBlobmen = Value
		if BreakAllBlobmen then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "This prevents blobmen from grabbing players, it also prevents kick all from working.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
			task.defer(function()
				while wait(BreakBlobmenDelay) do
					if not BreakAllBlobmen then break end
					if BreakAllBlobmen then
						BreakBlobmen()
					end
				end
			end)
		end
	end
})
MainBlobman:AddSlider({
	Name = "Break Delay",
	Min = 0,
	Max = 0.5,
	Default = 0,
	Increment = 0.01,
	ValueName = "Delay",
	Callback = function(Value)
		BreakBlobmenDelay = Value
	end
})
local WasOn1
AntiBlobmanKick = false
Invincibility:AddToggle({
	Name = "Anti-Blobman Kick",
	Callback = function(Value)
		AntiBlobmanKick = Value
		if AntiBlobmanKick then
			if AutoPair and not AntiBlobmanBring then
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "This prevents blobmen from kicking you. Paired with anti blob bring to make you not get flung.",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			else
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "This prevents blobman script from kicking you.",
					Image = "rbxassetid://4483345998",
					Time = 5
				})
			end
			if AutoPair then
				if not AntiBlobmanBring then
					WasOn1 = true
					AntiBlobmanBring = true
					AntiBlobBringToggle:Set(true)
				end
			end
			local function Prank(v)
				v.Changed:Connect(function(prop)
					if typeof(v.Attachment0) == "Instance" then
						if v.Attachment0:IsDescendantOf(LocalPlayer.Character) and AntiBlobmanKick then
							if Players:GetPlayerFromCharacter(v.Attachment0:FindFirstAncestorWhichIsA("Model")) == LocalPlayer then
								if v.Parent.Parent then
									task.wait()
									if v.Parent and v.Parent.Name == "LeftDetector" then
										BreakSpecificBlobman(v.Parent.Parent,"left")
									end
									if v.Parent and v.Parent.Name == "RightDetector" then
										BreakSpecificBlobman(v.Parent.Parent,"left")
									end
								end
							end
						end
					end
				end)
			end
			for _,v in pairs(workspace:GetDescendants()) do
				if v.Name == "CreatureBlobman" and not v:IsDescendantOf(workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")) and AntiBlobmanKick then
					if v and v:FindFirstChild("LeftDetector") and v:FindFirstChild("RightDetector") and v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld") and v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld") then
						Prank(v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"))
						Prank(v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"))
					end
				end
			end
			workspace.DescendantAdded:Connect(function(v)
				if v.Name == "CreatureBlobman" and not v:IsDescendantOf(workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")) and AntiBlobmanKick then
					wait()
					if v and v:FindFirstChild("LeftDetector") and v:FindFirstChild("RightDetector") and v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld") and v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld") then
						Prank(v:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld"))
						Prank(v:FindFirstChild("RightDetector"):FindFirstChild("RightWeld"))
					end
				end
			end)
		else
			if WasOn1 then
				WasOn1 = false
				AntiBlobmanBring = false
				AntiBlobBringToggle:Set(false)
			end
		end
	end
})
AntiExplosion = false
local BombExplodeLoop
Invincibility:AddToggle({
	Name = "Anti-Explosion",
	Callback = function(Value)
		AntiExplosion = Value
		if AntiExplosion then
			BombExplodeLoop = workspace.ChildAdded:Connect(function(ExplosionPart)
				if LocalPlayer.Character and ExplosionPart:IsA("Part") and ExplosionPart:FindFirstChild("BillboardGui") then
					local HRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
					if HRP then
						if (HRP.Position - ExplosionPart.Position).Magnitude < 15 then
							StopVelocity(HRP, 1)
						end
					end
				end
			end)
		else
			if BombExplodeLoop then
				BombExplodeLoop:Disconnect()
			end
		end
	end
})
AntiAttacker = false
Invincibility:AddToggle({
	Name = "Anti-Attacker",
	Callback = function(Value)
		AntiAttacker = Value
		local conn
		if AntiAttacker then
			conn = workspace.DescendantAdded:Connect(function(Descendant)
				if Descendant.Name == "GrabParts" and AntiAttacker then
					task.wait()
					local GrabPart = Descendant:FindFirstChild("GrabPart")
					local WeldConstraint = GrabPart and GrabPart:FindFirstChild("WeldConstraint")
					if GrabPart and WeldConstraint then
						local Attacker = Players:GetPlayerFromCharacter(Descendant.Parent)
						if not IsInvincible(Attacker) then
							local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
							if character then
								if typeof(WeldConstraint.Part1) == "Instance" and WeldConstraint.Part1 == character:FindFirstChild("HumanoidRootPart").FirePlayerPart then
									local args = {[1] = character:WaitForChild("HumanoidRootPart"), [2] = 0}
									game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
								end
							end
							if WeldConstraint.Part1:IsDescendantOf(LocalPlayer.Character) then
								game.ReplicatedStorage.CharacterEvents.Struggle:FireServer()
								if AntiAttackerType == "Kill" then
									TeleportAndKillForAntiAttacker(Attacker)
								elseif AntiAttackerType == "Kick" then
									TeleportAndKickForAntiAttacker(Attacker)
								elseif AntiAttackerType == "Fling" then
									local TargetChar = Attacker.Character or Attacker.CharacterAdded:Wait()
									local TargetHRP = TargetChar:FindFirstChild("HumanoidRootPart")
									local MyChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
									local HRP = MyChar:FindFirstChild("HumanoidRootPart")
									local SetNetworkOwnerArgs = {[1] = TargetHRP, [2] = HRP.CFrame}
									game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
									if not TargetHRP:FindFirstChildWhichIsA("BodyVelocity") then
										local BV = Instance.new("BodyVelocity", TargetHRP)
										BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
										BV.P = 1250
										BV.Velocity = HRP.CFrame.LookVector * 125 + Vector3.new(0, 125, 0)
									end
								end
							end
						end
					else
						return
					end
				end
			end)
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
Invincibility:AddDropdown({
	Name = "Anti-Attacker Type",
	Default = "Kill",
	Options = {"Kill","Kick","Fling"},
	Callback = function(Value)
		AntiAttackerType = Value
	end
})
Invincibility:AddDropdown({
	Name = "Anti-Attacker Kick Type",
	Default = "Float",
	Options = {"Float","Silent"},
	Callback = function(Value)
		AntiAttackerKickType = Value
	end
})
local Pos
local TeleportTo = "Pink House"
LoopBypasser = false
local PinkHousePos = Vector3.new(-483.982, -7.350, -157.627)
local GreenHousePos = Vector3.new(-548.835, -7.350, 78.444)
local PurpleHousePos = Vector3.new(278.150, -7.000, 458.970)
local RedHousePos = Vector3.new(547.856, 123.338, -92.509)
local BlueHousePos = Vector3.new(502.095, 83.336, -355.156)
local conn9
Invincibility:AddToggle({
	Name = "Loop Bypasser",
	Callback = function(Value)
		LoopBypasser = Value
		if LoopBypasser then

			if workspace:FindFirstChild("TeleportToGround") then
				workspace:FindFirstChild("TeleportToGround").Parent = game.ReplicatedStorage:FindFirstChild("GameCorrectionEvents")
			end
			AntiGrabToggler:Set(true)
			AntiVoidToggler:Set(true)
			AnticheatAntiGrabToggler:Set(true)
			TeleportBackWhenAntiGrabbedToggle:Set(false)
			local function AntiLoop(PositionToTpTo)
				local MyChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
				if MyChar and LoopBypasser then
					while LoopBypasser do
						MyChar:PivotTo(CFrame.new(PositionToTpTo))
						wait()
					end
				end
			end
			if TeleportTo == "Pink House" then
				Pos = PinkHousePos
			elseif TeleportTo == "Green House" then
				Pos = GreenHousePos
			elseif TeleportTo == "Purple House" then
				Pos = PurpleHousePos
			elseif TeleportTo == "Red House" then
				Pos = RedHousePos
			elseif TeleportTo == "Blue House" then
				Pos = BlueHousePos
			end
			coroutine.wrap(AntiLoop)(Pos)

			conn9 = LocalPlayer.CharacterAdded:Connect(function()
				coroutine.wrap(AntiLoop)(Pos)
			end)
		else
			if conn9 then
				conn9:Disconnect()
			end
		end
	end
})
Invincibility:AddDropdown({
	Name = "Tp To",
	Default = "Pink House",
	Options = {"Pink House","Green House","Purple House","Red House","Blue House"},
	Callback = function(Value)
		TeleportTo = Value
	end
})
function LagServer(Type)
	task.defer(function()
		while task.wait(1/GrabsPerSecond) do
			if Type == "Players" then
				if not LagServerUsingPlayers then break end

				for i, Player in pairs(Players:GetPlayers()) do
					local Torso = Player.Character:FindFirstChild("Torso")
					if Torso then
						local args = {[1] = Torso, [2] = Torso.CFrame}
						game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine"):FireServer(unpack(args))
					end
				end

			elseif Type == "Map" then
				if not LagServerUsingMap then break end

				for i, Grass in pairs(workspace.Map.BaseGround:GetChildren()) do
					if Grass.Name == "Grass" then
						local args = {[1] = Grass, [2] = Grass.CFrame}
						game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine"):FireServer(unpack(args))
					end
				end

			end
		end
	end)
end

function DisableInvisLine()
	if InvisLine then
		OrionLib:MakeNotification({
			Name = "Warning",
			Content = "Your ping will increase in some time (Invis line has been toggled off because lag script won't work if it's on.)",
			Image = "rbxassetid://4483345998",
			Time = 7
		})
		InvisLine = false
		InvisLineToggle:Set(InvisLine)
	end
end

MainServerLag:AddToggle({
	Name = "Lag Server Using Players",
	Callback = function(Value)
		LagServerUsingPlayers = Value
		if LagServerUsingPlayers then
			DisableInvisLine()
			LagServer("Players")
		end
	end,
})
MainServerLag:AddToggle({
	Name = "Lag Server Using Map",
	Callback = function(Value)
		LagServerUsingMap = Value
		if LagServerUsingMap then
			DisableInvisLine()
			LagServer("Map")
		end
	end
})

MainServerLag:AddSlider({
	Name = "Grabs Per Second",
	Min = 5,
	Max = 1000,
	Default = 50,
	Increment = 1,
	ValueName = "Grabs Per Second",
	Callback = function(Value)
		GrabsPerSecond = Value
	end
})
local KGToggler = MainOP:AddToggle({
	Name = "Kick Grab",
	Callback = function(Value)
		KickGrabToggle = Value
		for _, v in pairs(Players:GetPlayers()) do
			local char = v.Character
			if char then
				local CharHRP = char:FindFirstChild("HumanoidRootPart")
				if CharHRP then
					local FPP = CharHRP:FindFirstChild("FirePlayerPart")
					if FPP then
						FPP.CollisionGroup = "nigger"
					end
				end 
			end
		end
		local function prank(on) 
			for _, v in pairs(Players:GetPlayers()) do
				local char = v.Character
				if char then
					local CharHRP = char:FindFirstChild("HumanoidRootPart")
					if CharHRP then
						local FPP = CharHRP:FindFirstChild("FirePlayerPart")
						if FPP then
							FPP.CanQuery = on
							if on then
								FPP.Size = Vector3.new(4.875, 6.625, 3.75)
							else
								FPP.Size = Vector3.new(2.875, 3.625, 1.75)
							end
						end
					end
				end
			end
		end

		prank(KickGrabToggle)
		workspace.DescendantAdded:Connect(function(v)
			if v and v:IsA("BasePart") and v.Name == "FirePlayerPart" then
				local character = v:FindFirstAncestorWhichIsA("Model")
				if character and Players:GetPlayerFromCharacter(character) then
					v.CollisionGroup = "nigger"
					v.CanQuery = KickGrabToggle
				end
			end
		end)
	end
})
local PlotGrab = false
local connt
MainOP:AddToggle({
	Name = "Plot Grab",
	Callback = function(Value)
		PlotGrab = Value
		local PlayersInPlots = workspace.PlotItems.PlayersInPlots
		if PlotGrab then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "You can only grab them with kick grab and you must be member/co-owner/owner to be able to grab them.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
			if AutoPair then
				KGToggler:Set(true)
			end
			PlayersInPlotsStorage = {}
			function deletescripts(v)
				for _, script in pairs(v:GetChildren()) do
					if script:IsA("Script") or script:IsA("LocalScript") then
						script:Destroy()
					end
				end
			end
			for _,v in pairs(PlayersInPlots:GetChildren()) do
				if v.Name ~= LocalPlayer.Name then
					v.Parent = workspace
				end
			end
			connt = PlayersInPlots.ChildAdded:Connect(function(v)
				wait(0.1)
				if v.Name ~= LocalPlayer.Name then
					v.Parent = workspace
				end
			end)
		else
			if connt then connt:Disconnect() end
		end
	end
})


local DistortSound = false
Sound:AddToggle({
	Name = "Distort sound",
	Callback = function(Value)
		DistortSound = Value
		if DistortSound then
			for i, Toy in pairs(ToyFolder:GetChildren()) do
				if Toy.Name == "MidiMaker" then
					for i, Key in pairs(Toy:GetChildren()) do
						local Sound = Key:FindFirstChildWhichIsA("Sound")
						if Sound and string.find(Sound.Name, "Sound") then
							local args = {[1] = Key, [2] = LocalPlayer.Character.HumanoidRootPart.CFrame}
							game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(args))
						end
					end
				end
			end
			OrionLib:MakeNotification({
				Name = "Warning:",
				Content = "This will increase your ping if you keep it on for a long time.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
		end
		task.defer(function()
			while DistortSound do
				if not DistortSound then break end
				for _, Toy in pairs(ToyFolder:GetChildren()) do
					if Toy.Name == "MusicKeyboard" then
						for i, PianoKey in pairs(Toy:GetChildren()) do
							if PianoKey:FindFirstChild("Sound1") then
								local args = {[1] = PianoKey, [2] = LocalPlayer.Character.HumanoidRootPart.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(args))
							end
						end
					end
				end
				task.wait(PianoSpamDelay)
			end
		end)
	end
})
Sound:AddSlider({
	Name = "Piano Spam Speed",
	Min = 0,
	Max = 1,
	Default = 0,
	Increment = 0.02,
	ValueName = "Delay",
	Callback = function(Value)
		PianoSpamDelay = Value
	end
})
TeleportPiano = false
Sound:AddToggle({
	Name = "Teleport Piano",
	Callback = function(Value)
		TeleportPiano = Value
		task.defer(function()
			while TeleportPiano do
				for _, Toy in pairs(ToyFolder:GetChildren()) do
					if Toy.Name == "MusicKeyboard" then
						for _, PianoKey in pairs(Toy:GetChildren()) do
							if PianoKey:IsA("BasePart") then
								PianoKey.CanCollide = false
							end
						end
					end
				end

				for _, Toy in pairs(ToyFolder:GetChildren()) do
					if Toy.Name == "MusicKeyboard" then
						for _, PianoKey in pairs(Toy:GetChildren()) do
							if PianoKey and PianoKey:IsA("BasePart") and LocalPlayer.Character then
								local hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
								if hrp then
									PianoKey.CFrame = hrp.CFrame + Vector3.new(0, -10, 0)
									PianoKey.AssemblyLinearVelocity = Vector3.new()
								end
							end
						end
					end
				end
				task.wait(PianoTeleportDelay)
			end
		end)
		if not TeleportPiano then
			for _, Toy in pairs(ToyFolder:GetChildren()) do
				if Toy.Name == "MusicKeyboard" then
					for _, PianoKey in pairs(Toy:GetChildren()) do
						if PianoKey:IsA("BasePart") then
							PianoKey.CanCollide = true
						end
					end
				end
			end
		end
	end
})

Sound:AddSlider({
	Name = "Piano Teleport Speed",
	Min = 0,
	Max = 0.5,
	Default = 0,
	Increment = 0.01,
	ValueName = "Delay",
	Callback = function(Value)
		PianoTeleportDelay = Value
	end
})
Sound:AddButton({
	Name = "Spawn Piano",
	Callback = function()
		local args = {
			[1] = "MusicKeyboard",
			[2] = LocalPlayer.Character.HumanoidRootPart.CFrame,
			[3] = Vector3.new(0, 69.51100158691406, 0)
		}

		game:GetService("ReplicatedStorage"):WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"):InvokeServer(unpack(args))
	end
})
local connect
function ControlGrab(Type, Toggle)
	for _,GrabParts in pairs(workspace:GetChildren()) do
		if GrabParts then
			local GrabPart = GrabParts:FindFirstChild("GrabPart")
			if GrabPart then
				local Character = LocalPlayer.Character
				local HRP = Character:FindFirstChild("HumanoidRootPart")
				if Character and HRP then
					local Target = GrabPart:FindFirstChild("WeldConstraint").Part1

					if Type == "Fling" then

						local BV = Instance.new("BodyVelocity", Target)
						BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
						BV.P = 1250

						local PlayerTarget = Players:GetPlayerFromCharacter(Target.Parent)
						if PlayerTarget then
							BV.Velocity = HRP.CFrame.LookVector * 175 + Vector3.new(0, 100, 0)
						else
							BV.Velocity = HRP.CFrame.LookVector * 500 + Vector3.new(0, 500, 0)
						end
						workspace.ChildRemoved:Connect(function(Child)
							if Child.Name == "GrabParts" and not KeepFlingGrabVelocityToggle then
								BV:Destroy()
							end
						end)
					elseif Type == "Noclip" then
						if not Target.Anchored then
							local TargetModel = Target:FindFirstAncestorWhichIsA("Model")
							if TargetModel then
								local PlayerTarget = Players:GetPlayerFromCharacter(TargetModel)
								if PlayerTarget then
									if not NoclipGrabIgnorePlayers then
										local TargetChar = TargetModel
										local TargetHum = TargetChar:FindFirstChild("Humanoid")
										if TargetHum and TargetHum.Health > 0 then
											wait(0.25)
											for _, BodyPart in pairs(TargetChar:GetDescendants()) do
												if BodyPart:IsA("BasePart") then
													if BodyPart.CanCollide then
														BodyPart.CanCollide = not Toggle
														BodyPart.Changed:Once(function(Property)
															if Property == "CanCollide" then
																BodyPart.CanCollide = not Toggle
															end
														end)
													end
												end
											end
											wait(0.25)
											local RagdolledValue = TargetHum.Ragdolled
											RagdolledValue:GetPropertyChangedSignal("Value"):Connect(function()
												if RagdolledValue.Value then
													wait()
													TargetChar:FindFirstChild("Left Arm"):FindFirstChild("RagdollLimbPart").CanCollide = false
													TargetChar:FindFirstChild("Right Arm"):FindFirstChild("RagdollLimbPart").CanCollide = false
													TargetChar:FindFirstChild("Left Leg"):FindFirstChild("RagdollLimbPart").CanCollide = false
													TargetChar:FindFirstChild("Right Leg"):FindFirstChild("RagdollLimbPart").CanCollide = false
												end
											end)
										end
									end
								else
									for _, Part in pairs(TargetModel:GetDescendants()) do
										if Part:IsA("BasePart") then
											Part.CanCollide = not Toggle
										end
									end
								end
							end
						end
					elseif Type == "Extend" then
						local ExtendVelocity = Instance.new("BodyVelocity", Target)
						ExtendVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
						ExtendVelocity.P = 1250
						if ExtendToDistanceOnGrab then
							ExtendedDistance = ExtendMaxValue
						else
							local distance = (LocalPlayer.Character:FindFirstChild("HumanoidRootPart").Position - GrabParts:FindFirstChild("DragPart").Position).Magnitude
							ExtendedDistance = distance
						end
						connect = UserInputService.InputChanged:Connect(function(input)
							if input.UserInputType == Enum.UserInputType.MouseWheel and ExtendGrabToggle then
								if input.Position.Z > 0 then
									ExtendedDistance = math.min(ExtendedDistance + ExtendIncrement, ExtendMaxValue)
								elseif input.Position.Z < 0 then
									ExtendedDistance = math.max(ExtendedDistance - ExtendIncrement, 0)
								end
							end
						end)

						if Toggle then
							task.defer(function()
								while task.wait(0.05) do
									if not ExtendGrabToggle or not workspace:FindFirstChild("GrabParts") then ExtendVelocity:Destroy() if connect then connect:Disconnect() end break end
									local Pos = Camera.CFrame.Position + Camera.CFrame.LookVector * ExtendedDistance
									ExtendVelocity.Velocity = (Pos - Target.Position) * 5
								end
							end)
						end
					elseif Type == "Mass" then
						local DragPart = GrabParts:WaitForChild("DragPart")
						local AP = DragPart.AlignPosition
						local AO = DragPart.AlignOrientation
						if Toggle then
							AP.MaxForce = GrabMaxForce
							AP.Responsiveness = GrabResponsiveness
							AO.MaxTorque = GrabMaxForce
							AO.Responsiveness = GrabResponsiveness
						else
							AP.MaxForce = 60000
							AP.Responsiveness = 40
							AO.MaxTorque = 600000
							AO.Responsiveness = 30
						end
						if PrimaryAxisOnly then
							AO.PrimaryAxisOnly = true
						else
							AO.PrimaryAxisOnly = false
						end
					else return end
				end
			end
		end
	end
end
local conn
GrabPhysics:AddToggle({
	Name = "Control Grab's Properties",
	Callback = function(Value)
		ControlGrabPropertiesToggle = Value
		if ControlGrabPropertiesToggle then
			ControlGrab("Mass", ControlGrabPropertiesToggle)
			conn = workspace.ChildAdded:Connect(function(model)
				task.wait()
				if model.Name == "GrabParts" then
					ControlGrab("Mass", ControlGrabPropertiesToggle)
				end
			end)
			OrionLib:MakeNotification({
				Name = "Hint:",
				Content = "Now your grab can be very strong.",
				Image = "rbxassetid://4483345998",
				Time = 3
			})
		end
	end
})
MaxInstancesToProcess = 2
WaitBeforeExplosion = 0.2
SliderSpacing = 35
Spacing = SliderSpacing
SyncedExplosions = true
distancefromlocalplayer = 20
AutoSpace = false
local folderName = LocalPlayer.Name .. "SpawnedInToys"

local folder = workspace:FindFirstChild(folderName)

local bombTypes = {
	"BallSnowball",
	"BombBalloon",
	"BombDarkMatter",
	"BombMissile",
	"FireworkMissile",
	"PresentBig",
	"PresentSmall",
}

local function DisableCollisions(model)
	for _, child in pairs(model:GetDescendants()) do
		if child:IsA("BasePart") then
			child.CanCollide = false
		end
	end
end
local function TeleportBombsToLine()
	local camera = workspace.CurrentCamera
	local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	local cameraCFrame = camera.CFrame
	local startPosition = cameraCFrame.Position + cameraCFrame.LookVector * distancefromlocalplayer
	local bombCount = 0

	for i, child in pairs(folder:GetChildren()) do
		if table.find(bombTypes, child.Name) and child:IsA("Model") then
			if bombCount >= MaxInstancesToProcess then
				break
			end
			if AutoSpace then
				if child.Name == "BombMissile" then
					Spacing = 35
				elseif child.Name == "BombDarkMatter" then
					Spacing = 20
				elseif child.Name == "FireworkMissile" then
					Spacing = 42
				elseif child.Name == "PresentBig" then
					Spacing = 12
				elseif child.Name == "PresentSmall" then
					Spacing = 6
				elseif child.Name == "BombBalloon" or child.Name == "BallSnowball" then
					Spacing = 8
				end
			end
			local positionInLine = startPosition + cameraCFrame.LookVector * (bombCount * Spacing)
			DisableCollisions(child)
			child:PivotTo(CFrame.new(positionInLine))
			bombCount = bombCount + 1
			if child and child.PrimaryPart and not child.PrimaryPart:FindFirstChildWhichIsA("BodyVelocity") then
				local BV = Instance.new("BodyVelocity")
				BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
				BV.Velocity = Vector3.new(0, 0, 0)
				BV.Parent = child.PrimaryPart
			end
			local function Explode()
				wait(WaitBeforeExplosion)
				local hitboxType = "PartHitDetector"

				if child.Name == "BallSnowball" then
					hitboxType = "SoundPart"
				elseif child.Name == "PresentBig" or child.Name == "PresentSmall" then
					hitboxType = "Box"
				elseif child.Name == "BombBalloon" then
					hitboxType = "Balloon"
				end

				local positionPart = child.PrimaryPart
				if positionPart then
					local args = {
						[1] = {
							["Hitbox"] = child:FindFirstChild(hitboxType),
							["Model"] = child,
							["PositionPart"] = positionPart,
						},
						[2] = positionPart.Position,
					}

					game:GetService("ReplicatedStorage").BombEvents.BombExplode:FireServer(unpack(args))
				end
			end
			if SyncedExplosions then
				task.defer(Explode)
			else
				Explode()
			end
		end
	end
end
local BombsCount = 0

local function calculateRange()
	local Spacing
	if AutoSpace then
		for i, child in pairs(folder:GetChildren()) do
			if table.find(bombTypes, child.Name) and child:IsA("Model") then
				if child.Name == "BombMissile" then
					Spacing = 35
				elseif child.Name == "BombDarkMatter" then
					Spacing = 20
				elseif child.Name == "FireworkMissile" then
					Spacing = 42
				elseif child.Name == "PresentBig" then
					Spacing = 12
				elseif child.Name == "PresentSmall" then
					Spacing = 6
				elseif child.Name == "BombBalloon" then
					Spacing = child.Balloon.Size.Magnitude * 1.5
				elseif child.Name == "BallSnowball" then
					Spacing = child.SoundPart.Size.Magnitude * 1.5
				end
				break
			end
		end
	else
		Spacing = SliderSpacing
	end

	if BombsCount and Spacing and distancefromlocalplayer then
		return math.min(BombsCount, MaxInstancesToProcess) * Spacing + distancefromlocalplayer
	else
		return 0
	end
end


local CalculateRangeLabel = BombLaser:AddLabel("Calculated Range: " .. calculateRange() .. " Studs")

local function updateRangeLabel()
	CalculateRangeLabel:Set("Calculated Range: " .. calculateRange() .. " Studs")
end

BombLaser:AddButton({
	Name = "Bomb Laser",
	Callback = function()
		TeleportBombsToLine()
	end
})

BombLaser:AddSlider({
	Name = "Bombs To Explode (Laser)",
	Min = 0,
	Max = 40,
	Default = 5,
	Increment = 1,
	ValueName = "Count",
	Callback = function(Value)
		MaxInstancesToProcess = Value
		updateRangeLabel()
	end
})

BombLaser:AddSlider({
	Name = "Bomb Spacing",
	Min = 0,
	Max = 50,
	Default = 35,
	Increment = 1,
	ValueName = "Spacing",
	Callback = function(Value)
		if not AutoSpace then
			SliderSpacing = Value
			updateRangeLabel()
		end
	end
})

BombLaser:AddSlider({
	Name = "Delay Before Explosion (In seconds.)",
	Min = 0.05,
	Max = 10,
	Default = 0.2,
	Increment = 0.05,
	ValueName = "Delay",
	Callback = function(Value)
		WaitBeforeExplosion = Value
	end
})
BombLaser:AddSlider({
	Name = "Distance From Yourself",
	Min = 0,
	Max = 35,
	Default = 20,
	Increment = 1,
	ValueName = "Distance",
	Callback = function(Value)
		distancefromlocalplayer = Value
		updateRangeLabel()
	end
})
BombLaser:AddToggle({
	Name = "Auto Space Bombs",
	Default = true,
	Callback = function(Value)
		AutoSpace = Value
		updateRangeLabel()
	end
})
BombsToSpawn = 5
SpawnBombs:AddSlider({
	Name = "Bombs To Spawn",
	Min = 0,
	Max = 40,
	Default = 5,
	Increment = 1,
	ValueName = "Ammount",
	Callback = function(Value)
		BombsToSpawn = Value
	end
})
AutoSpawnBombs = false
BombType = "BombMissile"
local conn
SpawnBombs:AddToggle({
	Name = "Auto Spawn Bombs",
	Default = false,
	Callback = function(Value)
		AutoSpawnBombs = Value
		if AutoSpawnBombs then
			AntiVoidToggler:Set(true)
			task.defer(function()
				while AutoSpawnBombs do
					if BombsCount < BombsToSpawn then
						local hrp = LocalPlayer.Character.HumanoidRootPart
						local spawnPosition = (hrp.Position-Vector3.new(0,-3,0)) - hrp.CFrame.RightVector * 5
						local args = {
							[1] = BombType,
							[2] = CFrame.new(spawnPosition, hrp.Position),
							[3] = Vector3.new(0, 50, 0)
						}
						task.defer(function()
							game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
						end)
						wait()
					else
						wait(0.1)
					end
				end
			end)
			conn = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys").ChildAdded:Connect(function(child)
				if child.Name == BombType then
					local hitboxType = "PartHitDetector"

					if child.Name == "BallSnowball" then
						hitboxType = "SoundPart"
					elseif child.Name == "PresentBig" or child.Name == "PresentSmall" then
						hitboxType = "Box"
					elseif child.Name == "BombBalloon" then
						hitboxType = "Balloon"
					end
					for i = 1,5 do
						local args = {child:FindFirstChild(hitboxType),game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame}
						game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(unpack(args))
						wait()
					end
					wait(0.1)
					DisableCollisions(child)
					if child and hitboxType then
						child:FindFirstChild(hitboxType).CanTouch = false
					end
					child:PivotTo(CFrame.new(0,-100 - math.random(0,50),-100 - math.random(0,50)))
					wait(0.5)
					if child and child.PrimaryPart and not child.PrimaryPart:FindFirstChildWhichIsA("BodyVelocity") then
						child.PrimaryPart.AssemblyLinearVelocity = Vector3.new()
						local BV = Instance.new("BodyVelocity")
						BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
						BV.Velocity = Vector3.new(0, 0, 0)
						BV.Parent = child.PrimaryPart
					end
				end
			end)
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
updateRangeLabel()
SpawnBombs:AddDropdown({
	Name = "Bomb Type",
	Default = "BombMissile",
	Options = {"BombMissile","BombDarkMatter","BombBalloon","FireworkMissile","PresentBig","PresentSmall",},
	Callback = function(Value)
		BombType = Value
	end
})
BombLaser:AddToggle({
	Name = "Sync Explosions",
	Default = true,
	Callback = function(Value)
		SyncedExplosions = Value
	end
})
for i, child in pairs(folder:GetChildren()) do
	if table.find(bombTypes, child.Name) and child:IsA("Model") then
		BombsCount = BombsCount + 1
		updateRangeLabel()
	end
end

folder.ChildAdded:Connect(function(child)
	if table.find(bombTypes, child.Name) and child:IsA("Model") then
		BombsCount = BombsCount + 1
		updateRangeLabel()
	end
end)

folder.ChildRemoved:Connect(function(child)
	if table.find(bombTypes, child.Name) and child:IsA("Model") then
		BombsCount = BombsCount - 1
		updateRangeLabel()
	end
end)
ShowPrediction = false

local predictionParts = {}

local function MakePart(player)
	local part = Instance.new("Part")
	part.Size = Vector3.new(1, 1, 1)
	part.Anchored = true
	part.CanCollide = false
	part.CanQuery = false
	part.CanTouch = false
	part.BrickColor = BrickColor.new("Bright red")
	part.Transparency = 1
	part.Name = "BombPredictionFor_" .. player.Name
	part.Parent = workspace

	local billboard = Instance.new("BillboardGui")
	billboard.Size = UDim2.new(20, 0, 20, 0)
	billboard.Adornee = part
	billboard.Parent = part

	local imageLabel = Instance.new("ImageLabel")
	imageLabel.Size = UDim2.new(1, 0, 1, 0)
	imageLabel.Image = "http://www.roblox.com/asset/?id=13011888817"
	imageLabel.BackgroundTransparency = 1
	imageLabel.ImageColor3 = Color3.fromRGB(255, 0, 4)
	imageLabel.Parent = billboard

	return part
end

local function Estimate(player, time)
	local character = player.Character
	if not character or not character:FindFirstChild("HumanoidRootPart") then
		return nil
	end
	local humanoidRootPart = character.HumanoidRootPart
	local currentPosition = humanoidRootPart.Position
	local currentVelocity = humanoidRootPart.Velocity
	local futurePosition = currentPosition + (currentVelocity * time)

	return futurePosition
end

local function predict(player)
	if not ShowPrediction then
		if predictionParts[player.UserId] then
			predictionParts[player.UserId]:Destroy()
			predictionParts[player.UserId] = nil
		end
		return
	end

	local part = MakePart(player)
	predictionParts[player.UserId] = part

	while true do
		if not ShowPrediction then
			if predictionParts[player.UserId] then
				predictionParts[player.UserId]:Destroy()
				predictionParts[player.UserId] = nil
			end
			return
		end

		local futurePosition = Estimate(player, WaitBeforeExplosion)
		if futurePosition then
			part.Position = futurePosition
		end
		task.wait()
	end
end

game.Players.PlayerRemoving:Connect(function(player)
	if predictionParts[player.UserId] then
		predictionParts[player.UserId]:Destroy()
		predictionParts[player.UserId] = nil
	end
end)

for _, player in pairs(game.Players:GetPlayers()) do
	if player ~= LocalPlayer then
		coroutine.wrap(predict)(player)
	end
end

game.Players.PlayerAdded:Connect(function(player)
	coroutine.wrap(predict)(player)
end)

BombLaser:AddToggle({
	Name = "Show Prediction",
	Default = false,
	Callback = function(Value)
		ShowPrediction = Value
		for _, player in pairs(game.Players:GetPlayers()) do
			if predictionParts[player.UserId] and not ShowPrediction then
				predictionParts[player.UserId]:Destroy()
				predictionParts[player.UserId] = nil
			elseif not predictionParts[player.UserId] and ShowPrediction then
				if player ~= LocalPlayer then
					coroutine.wrap(predict)(player)
				end
			end
		end
	end
})

ShouldDelayExplosion = true
BombExplosionDelay = 0.1
LoopExplodeBombs = false
BombExplosionMaxLimit = 5
SynchronizeExplosions = true


ExplodeBomb:AddButton({
	Name = "Explode",
	Callback = function()
		if ExplosionTarget then
			local playerName = string.match(ExplosionTarget, "^(%S+)%s")
			local player = Players:FindFirstChild(playerName)
			if player and not player.InPlot.Value then
				local char = player.Character or player.CharacterAdded:Wait()
				if char then
					local TargetHRP = char:FindFirstChild("HumanoidRootPart")
					if TargetHRP then
						local BombExplodeCount = 0
						local function getHitboxType(child)
							if child.Name == "BallSnowball" then
								return "SoundPart"
							elseif child.Name == "PresentBig" or child.Name == "PresentSmall" then
								return "Box"
							elseif child.Name == "BombBalloon" then
								return "Balloon"
							else
								return "PartHitDetector"
							end
						end

						if SynchronizeExplosions then
							task.defer(function()
								while SynchronizeExplosions do
									local explodedTypes = {}
									local bombsToExplode = {}
									for i, child in pairs(folder:GetChildren()) do
										if BombExplodeCount >= BombExplosionMaxLimit then
											return
										end
										if table.find(bombTypes, child.Name) and child:IsA("Model") then
											if not explodedTypes[child.Name] then
												local args = {
													[1] = {
														["Hitbox"] = child:FindFirstChild(getHitboxType(child)),
														["Model"] = child,
														["PositionPart"] = TargetHRP,
													},
													[2] = TargetHRP.Position,
												}
												table.insert(bombsToExplode, args)
												explodedTypes[child.Name] = true
												BombExplodeCount = BombExplodeCount + 1
											end
										end
									end

									for _, args in pairs(bombsToExplode) do
										game:GetService("ReplicatedStorage").BombEvents.BombExplode:FireServer(unpack(args))
									end

									if #bombsToExplode > 0 then
										wait(BombExplosionDelay)
									else
										break
									end
								end
							end)
						else
							for i, child in pairs(folder:GetChildren()) do
								if BombExplodeCount >= BombExplosionMaxLimit then
									return
								end
								if table.find(bombTypes, child.Name) and child:IsA("Model") then
									BombExplodeCount = BombExplodeCount + 1
									local args = {
										[1] = {
											["Hitbox"] = child:FindFirstChild(getHitboxType(child)),
											["Model"] = child,
											["PositionPart"] = TargetHRP,
										},
										[2] = TargetHRP.Position,
									}
									game:GetService("ReplicatedStorage").BombEvents.BombExplode:FireServer(unpack(args))
									if ShouldDelayExplosion then
										task.wait(BombExplosionDelay)
									end
								end
							end
						end
					end
				end
			end
		end
	end
})


LoopDelay = 0.05
ExplodeBomb:AddToggle({
	Name = "Loop Explode",
	Callback = function(Value)
		LoopExplodeBombs = Value
		if ExplosionTarget and LoopExplodeBombs then
			task.defer(function()
				while LoopExplodeBombs do
					local playerName = string.match(ExplosionTarget, "^(%S+)%s")
					local player = Players:FindFirstChild(playerName)
					if player and not player.InPlot.Value then
						local char = player.Character or player.CharacterAdded:Wait()
						if char then
							local TargetHRP = char:FindFirstChild("HumanoidRootPart")
							if TargetHRP then
								local function getHitboxType(child)
									if child.Name == "BallSnowball" then
										return "SoundPart"
									elseif child.Name == "PresentBig" or child.Name == "PresentSmall" then
										return "Box"
									elseif child.Name == "BombBalloon" then
										return "Balloon"
									else
										return "PartHitDetector"
									end
								end

								if SynchronizeExplosions then
									task.defer(function()
										while SynchronizeExplosions do
											local explodedTypes = {}
											local bombsToExplode = {}
											local BombExplodeCount = 0
											for i, child in pairs(folder:GetChildren()) do
												if BombExplodeCount >= BombExplosionMaxLimit then
													break
												end
												if table.find(bombTypes, child.Name) and child:IsA("Model") then
													if not explodedTypes[child.Name] then
														local args = {
															[1] = {
																["Hitbox"] = child:FindFirstChild(getHitboxType(child)),
																["Model"] = child,
																["PositionPart"] = TargetHRP,
															},
															[2] = TargetHRP.Position,
														}
														table.insert(bombsToExplode, args)
														explodedTypes[child.Name] = true
														BombExplodeCount = BombExplodeCount + 1
													end
												end
											end

											for _, args in pairs(bombsToExplode) do
												game:GetService("ReplicatedStorage").BombEvents.BombExplode:FireServer(unpack(args))
											end

											if #bombsToExplode > 0 then
												wait(BombExplosionDelay)
											else
												break
											end
										end
									end)
								else
									local BombExplodeCount = 0
									for i, child in pairs(folder:GetChildren()) do
										if BombExplodeCount >= BombExplosionMaxLimit then
											break
										end
										if table.find(bombTypes, child.Name) and child:IsA("Model") then
											BombExplodeCount = BombExplodeCount + 1
											local args = {
												[1] = {
													["Hitbox"] = child:FindFirstChild(getHitboxType(child)),
													["Model"] = child,
													["PositionPart"] = TargetHRP,
												},
												[2] = TargetHRP.Position,
											}
											game:GetService("ReplicatedStorage").BombEvents.BombExplode:FireServer(unpack(args))
											if ShouldDelayExplosion then
												task.wait(BombExplosionDelay)
											end
										end
									end
								end
							end
						end
					end
					wait(LoopDelay)
				end
			end)
		end
	end
})

local ExplosionTargetDropdown = ExplodeBomb:AddDropdown({
	Name = "Target",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		ExplosionTarget = Value
	end
})
ExplodeBomb:AddToggle({
	Name = "Synchronize Explosions",
	Default = false,
	Callback = function(Value)
		SynchronizeExplosions = Value
	end
})
ExplodeBomb:AddSlider({
	Name = "Bombs To Explode (Spam Explosion)",
	Min = 0,
	Max = 40,
	Default = 5,
	Increment = 1,
	ValueName = "Count",
	Callback = function(Value)
		BombExplosionMaxLimit = Value
	end
})
ExplodeBomb:AddSlider({
	Name = "Loop Delay",
	Min = 0,
	Max = 2,
	Default = 0.05,
	Increment = 0.025,
	ValueName = "Count",
	Callback = function(Value)
		LoopDelay = Value
	end
})
function ExplosionsAddToPlrList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if not table.find(ExplosionPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(ExplosionPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdateAndRefresh(ExplosionTargetDropdown, ExplosionPlayerList)
	end
end

ExplodeBomb:AddToggle({
	Name = "Should Delay Explosions",
	Default = true,
	Callback = function(Value)
		ShouldDelayExplosion = Value
	end
})
ExplodeBomb:AddSlider({
	Name = "Explosion Delay",
	Min = 0,
	Max = 5,
	Default = 0.1,
	Increment = 0.025,
	ValueName = "Count",
	Callback = function(Value)
		BombExplosionDelay = Value
	end
})
MaxSize = 17.15
GrowSnowballsToggler = true
SnowballSpawnCount = 20
local conn11
local conn13

SnowballSection:AddToggle({
	Name = "Spawn And Grow Snowballs",
	Callback = function(Value)
		GrowSnowballsToggler = Value
		if GrowSnowballsToggler then
			local myfoldername = LocalPlayer.Name.."SpawnedInToys"
			local myfolder = workspace:FindFirstChild(myfoldername)
			local AlreadyGrown = {}
			local SpawnCount = 0

			function checkSize(snowball)
				while GrowSnowballsToggler do 
					local snowSize = snowball:WaitForChild("SoundPart").Size
					if snowSize.X >= MaxSize and snowSize.Y >= MaxSize and snowSize.Z >= MaxSize and not AlreadyGrown[snowball:FindFirstChild("SoundPart")] and SpawnCount >= SnowballSpawnCount then
						snowball:FindFirstChild("SoundPart").CFrame = CFrame.new(Vector3.new(10000 + math.random(-200,200),99999 + math.random(-200,200),0 + math.random(-200,200)))
						snowball:FindFirstChild("SoundPart"):FindFirstChildWhichIsA("BodyVelocity")
						AlreadyGrown[snowball:FindFirstChild("SoundPart")] = true
						break
					end
					task.wait()
				end
			end

			function modify(snowball)
				local BV
				if snowball:FindFirstChild("SoundPart") then
					snowball:FindFirstChild("SoundPart").CanTouch = false
					snowball:FindFirstChild("SoundPart").CanCollide = false
					if not snowball:FindFirstChild("SoundPart"):FindFirstChildWhichIsA("BodyVelocity") then
						BV = Instance.new("BodyVelocity")
						BV.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
						BV.Parent = snowball:FindFirstChild("SoundPart")
					else
						BV = snowball:FindFirstChild("SoundPart"):FindFirstChildWhichIsA("BodyVelocity")
					end
				end
				local pos = snowball:WaitForChild("SoundPart").CFrame * CFrame.new(0, -snowball:WaitForChild("SoundPart").Size.Y / 5, 0)
				while GrowSnowballsToggler do
					if snowball and SpawnCount >= SnowballSpawnCount then
						local snowSize = snowball:WaitForChild("SoundPart").Size
						if not AlreadyGrown[snowball:FindFirstChild("SoundPart")] then
							snowball:FindFirstChild("SoundPart").CanTouch = false
							snowball:FindFirstChild("SoundPart").CanCollide = false
							if BV then
								BV.Velocity = Vector3.new(40 + snowSize.Y*2,0,0)
							end
							snowball:FindFirstChild("SoundPart").AssemblyAngularVelocity = Vector3.new()
							if not AlreadyGrown[snowball:FindFirstChild("SoundPart")] then
								snowball:WaitForChild("SoundPart").CFrame = pos
							end
							wait(0.25)
							if BV then
								BV.Velocity = Vector3.new(-40 - snowSize.Y*2,0,0)
							end
							snowball:FindFirstChild("SoundPart").AssemblyAngularVelocity = Vector3.new()
							wait(0.25)
						end
					else
						break
					end
					wait()
				end
			end

			function newSnowball(child)
				if child.Name == "BallSnowball" and GrowSnowballsToggler then
					coroutine.wrap(checkSize)(child)
					coroutine.wrap(modify)(child)
				end
			end

			task.defer(function()
				while GrowSnowballsToggler do
					if SpawnCount < SnowballSpawnCount then
						local hrp = LocalPlayer.Character.HumanoidRootPart
						local args = {
							[1] = "BallSnowball",
							[2] = CFrame.new(-154.528, -9.205041, -582.075),
							[3] = Vector3.new(0, 50, 0)
						}
						task.defer(function()
							game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
						end)
						wait()
					else
						wait(0.1)
					end
				end
			end)

			conn11 = myfolder.ChildAdded:Connect(function(child)
				if child.Name == "BallSnowball" then
					SpawnCount = SpawnCount + 1
					if child and child:FindFirstChild("SoundPart") then
						child:FindFirstChild("SoundPart").CanTouch = false
					end
					task.defer(function()
						for i = 1,5 do
							child:FindFirstChild("SoundPart").CanTouch = false
							local args = {child:FindFirstChild("SoundPart"),game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame}
							game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(unpack(args))
							wait()
						end
					end)
					wait(0.1)
					if child and child:FindFirstChild("SoundPart") then
						child:FindFirstChild("SoundPart").CanTouch = false
					end
					wait(0.1)
					if SpawnCount >= SnowballSpawnCount then
						for _, snowball in ipairs(myfolder:GetChildren()) do
							newSnowball(snowball)
						end
						return
					end
				end
			end)

			conn13 = myfolder.ChildRemoved:Connect(function(child)
				if child.Name == "BallSnowball" then
					SpawnCount = SpawnCount - 1
				end
			end)
		else
			if conn11 then
				conn11:Disconnect()
			end
			if conn13 then
				conn13:Disconnect()
			end
		end
	end
})

SnowballSection:AddSlider({
	Name = "Max Size",
	Min = 4,
	Max = 17.15,
	Default = 17.15,
	Increment = 0.15,
	ValueName = "Value",
	Callback = function(Value)
		MaxSize = Value
	end
})
SnowballSection:AddSlider({
	Name = "Snowball Spawn Count",
	Min = 1,
	Max = 40,
	Default = 20,
	Increment = 1,
	ValueName = "Value",
	Callback = function(Value)
		SnowballSpawnCount = Value
	end
})
GrabPhysics:AddSlider({
	Name = "Grab's Max Force",
	Min = 0,
	Max = 1000000000,
	Default = 600000,
	Increment = 10,
	ValueName = "GrabMaxForce",
	Callback = function(Value)
		GrabMaxForce = Value
		ControlGrab("Mass", ControlGrabPropertiesToggle)
	end
})
GrabPhysics:AddSlider({
	Name = "Grab's Responsiveness",
	Min = 0,
	Max = 200,
	Default = 40,
	Increment = 10,
	ValueName = "GrabResponsiveness",
	Callback = function(Value)
		GrabResponsiveness = Value
		ControlGrab("Mass", ControlGrabPropertiesToggle)
	end
})
local conn
GrabPhysics:AddToggle({
	Name = "Primary Axis Only",
	Callback = function(Value)
		PrimaryAxisOnly = Value
		if PrimaryAxisOnly then
			ControlGrab("Mass", ControlGrabPropertiesToggle)
			conn = workspace.ChildAdded:Connect(function(model)
				task.wait()
				if model.Name == "GrabParts" then
					ControlGrab("Mass", ControlGrabPropertiesToggle)
				end
			end)
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
GuiLoader:AddButton({
	Name = "Load Infinite Yield",
	Callback = function()
		loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
	end
})
GuiLoader:AddButton({
	Name = "Load BlizT Hub",
	Callback = function()
		loadstring(game:HttpGet("https://raw.githubusercontent.com/BlizTBr/scripts/main/FTAP.lua"))()
	end
})
Other:AddButton({
	Name = "Clean Plots",
	Callback = function()
		for i, PlotItem in pairs(workspace.PlotItems:GetDescendants()) do
			if PlotItem:IsA("Model") then
				if not game.Players:GetPlayerFromCharacter(PlotItem) then
					PlotItem:Destroy()
				end
			end
		end
	end
})
Other:AddButton({
	Name = "Anti Anti-Grab (Not Toggleable And Will Reset You)",
	Callback = function()
		game.ReplicatedStorage.GrabEvents.DestroyGrabLine:Destroy()
		game.ReplicatedStorage.GrabEvents.EndGrabEarly:Destroy()
		wait()
		local rem = Instance.new("RemoteEvent")
		rem.Name = "DestroyGrabLine"
		rem.Parent = game.ReplicatedStorage.GrabEvents
		local rem1 = Instance.new("RemoteEvent")
		rem1.Name = "EndGrabEarly"
		rem1.Parent = game.ReplicatedStorage.GrabEvents
		local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
		char:FindFirstChildWhichIsA("Humanoid").Health = 0
		OrionLib:MakeNotification({
			Name = "Note",
			Content = "You can grab them 2 seconds max.",
			Image = "rbxassetid://4483345998",
			Time = 3
		})
	end
})
RagdollTime = 5
Other:AddButton({
	Name = "Ragdoll Yourself",
	Callback = function()
		local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
		if character then
			local args = {[1] = character:WaitForChild("HumanoidRootPart"), [2] = RagdollTime}
			game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
		end
	end
})
Other:AddSlider({
	Name = "Ragdoll Time",
	Min = 0,
	Max = 60,
	Default = 5,
	Increment = 1,
	ValueName = "Time",
	Callback = function(Value)
		RagdollTime = Value
	end
})
local SpawnCounter = 0
ToyDismounting:AddDropdown({
	Name = "Toys",
	Default = "RollerGrayPurple",
	Options = {"RollerGrayPurple"},
	Callback = function(Value)
		SelectedToy = Value
		SpawnCounter = 0
		for _,v in pairs(ToyFolder:GetChildren()) do
			if v.Name == SelectedToy then
				SpawnCounter = SpawnCounter + 1
			end
		end
	end
})
ToyFolder.ChildAdded:Connect(function(v)
	if v.Name == SelectedToy then
		SpawnCounter = SpawnCounter + 1
	end
end)
ToyFolder.ChildRemoved:Connect(function(v)
	if v.Name == SelectedToy then
		SpawnCounter = SpawnCounter - 1
	end
end)
function gyattroller(what)
	local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	local Platform = what:FindFirstChild("SoundPart")
	if Platform then
		Platform.AssemblyAngularVelocity = Vector3.new(0,0,0) Platform.AssemblyLinearVelocity = Vector3.new(0,-0,0)
		for i = 1,5 do
			Platform.Orientation = Vector3.new(0, 0, -90)
			for _,thing in pairs(what:GetChildren()) do
				if thing:IsA("BasePart") then
					game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(thing,thing.CFrame)
				end
			end
			wait()
		end
		wait()
		Platform.CFrame = CFrame.new(-5560,-99.5,3120)
		repeat Platform.AssemblyAngularVelocity = Vector3.new(0,0,0) Platform.AssemblyLinearVelocity = Vector3.new(0,-1,0) task.wait() until not Platform:IsDescendantOf(workspace)
		for _,balls in pairs(what:GetChildren()) do
			if balls:IsA("BasePart") and balls:FindFirstChildWhichIsA("BallSocketConstraint") then
				if ShouldNetOwnDismounted then
					local shouldbreak = false
					task.defer(function() wait(5) shouldbreak = true end)
					task.defer(function()
						repeat
							game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(balls,balls.CFrame)
							if shouldbreak then
								break
							end
							wait()
						until isnetworkowner(balls)
					end)
				end
			end
		end
	end
end
ToyFolder.ChildAdded:Connect(function(child)
	wait(0.5)
	if child.Name == "RollerGrayPurple" and DismountToys then
		gyattroller(child)
	end
end)
ToyDismounting:AddToggle({
	Name = "Spawn And Dismount Toy",
	Callback = function(Value)
		DismountToys = Value
		if DismountToys then
			task.defer(function()
				while DismountToys do
					if SpawnCounter < ToysToSpawn then
						local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
						local hrp = mychar:FindFirstChild("HumanoidRootPart")
						local spawnPosition = (hrp.Position-Vector3.new(0,-3,0)) - hrp.CFrame.RightVector * 5
						local args = {
							[1] = SelectedToy,
							[2] = CFrame.new(spawnPosition, hrp.Position),
							[3] = Vector3.new(0, 50, 0)
						}
						task.defer(function()
							game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
						end)
						wait()
					else
						wait(0.1)
					end
				end
			end)
			for _,child in pairs(ToyFolder:GetChildren()) do
				if child.Name == "RollerGrayPurple" then
					gyattroller(child)
				end
			end
		end
	end
})
ToyDismounting:AddSlider({
	Name = "Toys To Spawn",
	Min = 0,
	Max = 40,
	Default = 10,
	Increment = 1,
	ValueName = "Value",
	Callback = function(Value)
		ToysToSpawn = Value
	end
})
ToyDismounting:AddToggle({
	Name = "Should Net Own Dismounted Part",
	Callback = function(Value)
		ShouldNetOwnDismounted = Value
	end
})
ToyDismounting:AddDropdown({
	Name = "Toy Manipulation",
	Default = "None",
	Options = {"None","Explosion","Teleport To Me","Teleport At Spawn","Small Ziga", "Medium Ziga", "Large Ziga","Spawn Platform"},
	Callback = function(Value)
		SelectedManipulation = Value
	end
})
function GetAllDismountedToys()
	local Toys = {}
	for _,v in pairs(ToyFolder:GetChildren()) do
		if v.Name == SelectedToy then
			for _,balls in pairs(v:GetChildren()) do
				if balls:IsA("BasePart") and balls:FindFirstChildWhichIsA("BallSocketConstraint") then
					table.insert(Toys,balls)
				end
			end
		end
	end
	return Toys
end
ExplosionForce = 100
Y = 100
local SmallPositions = {
	[1] = Vector3.new(-2, 9, 0),
	[2] = Vector3.new(0, 13, 0),
	[3] = Vector3.new(-2, 13, 0),
	[4] = Vector3.new(0, 5, 0),
	[5] = Vector3.new(0, 9, 0),
	[6] = Vector3.new(-4, 9, 0),
	[7] = Vector3.new(0, 7, 0),
	[8] = Vector3.new(-4, 5, 0),
	[9] = Vector3.new(-4, 13, 0),
	[10] = Vector3.new(0, 11, 0),
	[11] = Vector3.new(-4, 7, 0),
	[12] = Vector3.new(4, 5, 0),
	[13] = Vector3.new(2, 5, 0),
	[14] = Vector3.new(2, 9, 0),
	[15] = Vector3.new(4, 11, 0),
	[16] = Vector3.new(4, 9, 0),
	[17] = Vector3.new(4, 13, 0),
}
local MediumPositions = {
	[1] = Vector3.new(0, 7, 0),
	[2] = Vector3.new(-6, 7, 0),
	[3] = Vector3.new(0, 5, 0),
	[4] = Vector3.new(-4, 17, 0),
	[5] = Vector3.new(-4, 11, 0),
	[6] = Vector3.new(-2, 11, 0),
	[7] = Vector3.new(0, 11, 0),
	[8] = Vector3.new(0, 13, 0),
	[9] = Vector3.new(-6, 11, 0),
	[10] = Vector3.new(0, 17, 0),
	[11] = Vector3.new(-6, 9, 0),
	[12] = Vector3.new(0, 15, 0),
	[13] = Vector3.new(-6, 17, 0),
	[14] = Vector3.new(-6, 5, 0),
	[15] = Vector3.new(0, 11, 0),
	[16] = Vector3.new(0, 9, 0),
	[17] = Vector3.new(-2, 17, 0),
	[18] = Vector3.new(6, 15, 0),
	[19] = Vector3.new(2, 11, 0),
	[20] = Vector3.new(2, 5, 0),
	[21] = Vector3.new(6, 17, 0),
	[22] = Vector3.new(4, 5, 0),
	[23] = Vector3.new(6, 5, 0),
	[24] = Vector3.new(4, 11, 0),
	[25] = Vector3.new(6, 11, 0),
	[26] = Vector3.new(6, 13, 0),
}
local BigPositions = {
	[1] = Vector3.new(-4, 13, 0),
	[2] = Vector3.new(0, 21, 0),
	[3] = Vector3.new(-8, 5, 0),
	[4] = Vector3.new(0, 13, 0),
	[5] = Vector3.new(-2, 13, 0),
	[6] = Vector3.new(0, 7, 0),
	[7] = Vector3.new(-6, 21, 0),
	[8] = Vector3.new(-4, 21, 0),
	[9] = Vector3.new(0, 19, 0),
	[10] = Vector3.new(0, 15, 0),
	[11] = Vector3.new(-8, 11, 0),
	[12] = Vector3.new(0, 11, 0),
	[13] = Vector3.new(0, 17, 0),
	[14] = Vector3.new(-8, 7, 0),
	[15] = Vector3.new(-8, 21, 0),
	[16] = Vector3.new(-2, 21, 0),
	[17] = Vector3.new(0, 13, 0),
	[18] = Vector3.new(-8, 9, 0),
	[19] = Vector3.new(0, 5, 0),
	[20] = Vector3.new(0, 9, 0),
	[21] = Vector3.new(-6, 13, 0),
	[22] = Vector3.new(-8, 13, 0),
	[23] = Vector3.new(4, 13, 0),
	[24] = Vector3.new(8, 5, 0),
	[25] = Vector3.new(8, 15, 0),
	[26] = Vector3.new(8, 21, 0),
	[27] = Vector3.new(6, 5, 0),
	[28] = Vector3.new(2, 13, 0),
	[29] = Vector3.new(8, 13, 0),
	[30] = Vector3.new(8, 17, 0),
	[31] = Vector3.new(4, 5, 0),
	[32] = Vector3.new(2, 5, 0),
	[33] = Vector3.new(8, 19, 0),
	[34] = Vector3.new(6, 13, 0),
}
local function ApplyOffsets(balls, positions)
    for i, pos in ipairs(positions) do
        if i <= #balls then
            local newPos = workspace.SpawnLocation.Position + Vector3.new(pos.X, pos.Y + Y, pos.Z)
            balls[i].CFrame = CFrame.new(newPos)
			task.defer(function()
				wait(0.1)
				if not balls[i]:FindFirstChildWhichIsA("BodyVelocity") then
					local ThrowVelocity = TemplateBV:Clone()
					ThrowVelocity.Velocity = Vector3.new(0, 0, 0)
					ThrowVelocity.Parent = balls[i]
				end
			end)
            coroutine.wrap(StopVelocity)(balls[i], 0.05)
        end
    end
end


local function CreatePlatform(balls, rows, columns)
    local positions = {}
    for row = 1, rows do
        for col = 1, columns do
            local pos = Vector3.new(
                (col - 1) * 2,
                0,
                (row - 1) * 2
            )
            table.insert(positions, pos)
        end
    end
    ApplyOffsets(balls, positions)
end

PlatformLength = 8
PlatformWidth = 10
function ManipulateToys(ManipulationType)
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local toys = GetAllDismountedToys()
    
    for _, balls in pairs(toys) do
        if isnetworkowner(balls) then
            balls.Anchored = false
            local existingBV = balls:FindFirstChildWhichIsA("BodyVelocity")
            if existingBV then
                existingBV:Destroy()
                balls.AssemblyLinearVelocity = Vector3.new(
                    math.random(-10, 10),
                    math.random(-10, 10),
                    math.random(-10, 10)
                )
            end
            task.defer(function()
                wait()
                if ManipulationType == "None" then
                elseif ManipulationType == "Explosion" then
                    coroutine.wrap(StopVelocity)(balls, 0.1)
                    wait(0.1)
                    task.defer(function()
                        balls.CanCollide = false
                        wait()
                        balls.CanCollide = true
                    end)
                    balls.CFrame = CFrame.new(workspace.SpawnLocation.Position.X, Y, workspace.SpawnLocation.Position.Z)
                    balls.AssemblyLinearVelocity = Vector3.new(
                        math.random(-ExplosionForce, ExplosionForce),
                        math.random(-ExplosionForce, ExplosionForce),
                        math.random(-ExplosionForce, ExplosionForce)
                    )
                elseif ManipulationType == "Teleport To Me" then
                    balls.CFrame = mychar:FindFirstChild("HumanoidRootPart").CFrame
                    coroutine.wrap(StopVelocity)(balls, 0.1)
                elseif ManipulationType == "Teleport At Spawn" then
                    balls.CFrame = CFrame.new(workspace.SpawnLocation.Position.X, Y, workspace.SpawnLocation.Position.Z)
                    coroutine.wrap(StopVelocity)(balls, 0.1)
                elseif ManipulationType == "Small Ziga" then
                    ApplyOffsets(toys, SmallPositions)
                elseif ManipulationType == "Medium Ziga" then
                    ApplyOffsets(toys, MediumPositions)
                elseif ManipulationType == "Large Ziga" then
                    ApplyOffsets(toys, BigPositions)
                elseif ManipulationType == "Spawn Platform" then
                    CreatePlatform(toys, PlatformWidth, PlatformLength)
                end
            end)
        end
    end
end


ToyDismounting:AddButton({
    Name = "Manipulate Toys",
    Callback = function()
        ManipulateToys(SelectedManipulation)
    end
})
function UpdatePlatform()
    local toys = GetAllDismountedToys()
	for _, balls in pairs(toys) do
        if isnetworkowner(balls) then
			local existingBV = balls:FindFirstChildWhichIsA("BodyVelocity")
			if existingBV then
				existingBV:Destroy()
				balls.AssemblyLinearVelocity = Vector3.new(
					math.random(-10, 10),
					math.random(-10, 10),
					math.random(-10, 10)
				)
			end
		end
	end
    CreatePlatform(toys, PlatformWidth, PlatformLength)
end

ToyDismounting:AddSlider({
    Name = "Platform Width",
    Min = 0,
    Max = 10,
    Default = 10,
    Increment = 1,
    ValueName = "Value",
    Callback = function(Value)
        PlatformWidth = Value
		wait(0.2)
        UpdatePlatform()
    end
})

ToyDismounting:AddSlider({
    Name = "Platform Length",
    Min = 0,
    Max = 8,
    Default = 8,
    Increment = 1,
    ValueName = "Value",
    Callback = function(Value)
        PlatformLength = Value
		wait(0.2)
        UpdatePlatform()
    end
})
ToyDismounting:AddSlider({
	Name = "Explosion Force",
	Min = 0,
	Max = 500,
	Default = 100,
	Increment = 1,
	ValueName = "Force",
	Callback = function(Value)
		ExplosionForce = Value
	end
})
ToyDismounting:AddSlider({
	Name = "Teleport Height",
	Min = -50,
	Max = 2000,
	Default = 100,
	Increment = 1,
	ValueName = "Value",
	Callback = function(Value)
		Y = Value
	end
})
function GrabKill()
	for _, GrabParts in pairs(workspace:GetChildren()) do 
		if GrabParts.Name == "GrabParts" then
			local GrabPart = GrabParts:FindFirstChild("GrabPart")
			if GrabPart then
				local player = GrabPart:FindFirstChildWhichIsA("WeldConstraint")
				local char = player.Part1:FindFirstAncestorWhichIsA("Model")
				if char then
					local hum = char:FindFirstChildWhichIsA("Humanoid")
					if hum then
						hum:ChangeState(Enum.HumanoidStateType.Dead, true)
					end
				end
			end
		end
	end
end
function KillAllNetowned()
	for _,v in pairs(Players:GetPlayers()) do
		if v ~= LocalPlayer then
			local char = v.Character
			if char then
				local hum = char:FindFirstChildWhichIsA("Humanoid")
				if hum and hum.Health > 0 then
					hum:ChangeState(Enum.HumanoidStateType.Dead)
					task.wait()
					hum:ChangeState(Enum.HumanoidStateType.None)
				end
			end
		end
	end
end
BindsTab:AddBind({
	Name = "Net Own Nearing Toys",
	Default = Enum.KeyCode.G,
	Hold = false,
	Callback = function()
		local Character = LocalPlayer.Character
		if Character then
			local HRP = Character:FindFirstChild("HumanoidRootPart")
			if HRP then
				for i, Toy in pairs(workspace:GetDescendants()) do
					if Toy:IsA("Model") and string.find(Toy.Parent.Name, "SpawnedInToys") then
						local PrimaryPart = Toy.PrimaryPart
						if PrimaryPart then
							if (PrimaryPart.Position - HRP.Position).Magnitude <= LocalPlayer:FindFirstChild("CurrentReach").Value then
								local Args = {[1] = PrimaryPart, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(Args))
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("CreateGrabLine"):FireServer(unpack(Args))
							end
						end
					end
				end
			end
		end
	end
})
BindsTab:AddBind({
	Name = "Kill Network Owned Bind",
	Default = Enum.KeyCode.O,
	Hold = false,
	Callback = function()
		KillAllNetowned()
	end
})
BindsTab:AddBind({
	Name = "Bomb Laser Bind",
	Default = Enum.KeyCode.Nine,
	Hold = false,
	Callback = function()
		TeleportBombsToLine()
	end
})
BindsTab:AddBind({
	Name = "Kick Grab Toggle Bind",
	Default = Enum.KeyCode.One,
	Hold = false,
	Callback = function()
		KickGrabToggle = not KickGrabToggle
		KGToggler:Set(KickGrabToggle)
	end
})

AuraMisc:AddButton({
	Name = "Kill All Network Owned",
	Callback = function()
		KillAllNetowned()
	end
})
function Aura(Type, Delay)
	local Mouse = LocalPlayer:GetMouse()
	while task.wait(Delay) do
		if Type == "Kick" and not KickAura then break end
		if Type == "Kill" and not KillAura then break end
		if Type == "Fling" and not FlingAura then break end
		if Type == "Spin" and not SpinAura then break end
		if Type == "Speed" and not SpeedAura then break end
		if Type == "Jump" and not JumpAura then break end
		if Type == "NetOwn" and not NetOwnAura then break end
		if Type == "Delete" and not DeleteAura then break end
		for i, TargetPlr in pairs(Players:GetPlayers()) do
			if TargetPlr ~= LocalPlayer and not IsInvincible(TargetPlr) then
				local Character = LocalPlayer.Character
				local TargetChr = TargetPlr.Character
				if not LocalPlayer:WaitForChild("InPlot").Value and not TargetPlr:WaitForChild("InPlot").Value and Character and TargetChr then
					local Humanoid = Character:FindFirstChild("Humanoid")
					local HRP = Character:FindFirstChild("HumanoidRootPart")
					local TargetHum = TargetChr:FindFirstChild("Humanoid")
					local TargetHRP = TargetChr:FindFirstChild("HumanoidRootPart")
					if Humanoid and HRP and TargetHum and TargetHRP then
						local TargetFPP = TargetHRP:FindFirstChild("FirePlayerPart")
						if TargetFPP and (TargetHRP.Position - HRP.Position).Magnitude < LocalPlayer.CurrentReach.Value then

							if Type == "Kick" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								if KickAuraType == "Float" then
									if not TargetFPP:FindFirstChildWhichIsA("BodyVelocity") then
										local BV = Instance.new("BodyVelocity", TargetFPP)
										BV.MaxForce = Vector3.new(1000000, 1000000, 1000000)
										BV.P = 1250
										BV.Velocity = Vector3.new(math.random(-25, 25), 50, math.random(-25, 25))
									end
								elseif KickAuraType == "Silent" then
									TargetHum.HipHeight = 2.6
								end
							elseif Type == "Kill" and TargetHum:GetState() ~= Enum.HumanoidStateType.Dead then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								TargetHum:ChangeState(Enum.HumanoidStateType.Dead, true)
							elseif Type == "Fling" and TargetHum:GetState() ~= Enum.HumanoidStateType.Dead then
								local SetNetworkOwnerArgs = {[1] = TargetHRP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								if not TargetHRP:FindFirstChildWhichIsA("BodyVelocity") then
									local BV = Instance.new("BodyVelocity", TargetHRP)
									BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
									BV.P = 1250
									BV.Velocity = HRP.CFrame.LookVector * 175 + Vector3.new(0, 100, 0)
								end
							elseif Type == "Spin" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								if not TargetFPP:FindFirstChildWhichIsA("BodyAngularVelocity") then
									local BAV = Instance.new("BodyAngularVelocity", TargetFPP)
									BAV.P = 1250
									BAV.MaxTorque = Vector3.new(100000, 100000, 100000)
									BAV.AngularVelocity = -TargetHRP.CFrame.LookVector * 50
								end
							elseif Type == "Speed" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								TargetHum.WalkSpeed = SpeedAuraAmmount
							elseif Type == "Jump" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								TargetHum.UseJumpPower = true
								TargetHum.JumpPower = JumpAuraAmmount
							elseif Type == "NetOwn" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
							elseif Type == "Delete" then
								local SetNetworkOwnerArgs = {[1] = TargetFPP, [2] = HRP.CFrame}
								game.ReplicatedStorage:FindFirstChild("GrabEvents"):FindFirstChild("SetNetworkOwner"):FireServer(unpack(SetNetworkOwnerArgs))
								task.defer(function()
									wait(0.2)
									while wait(0.1) do
										if game.Players:GetPlayerFromCharacter(TargetHRP.Parent) then
											if not TargetHRP then break end
											TargetHRP:PivotTo(CFrame.new(Vector3.new(TargetHRP.Position.X,TargetHRP.Position.Y-15,TargetHRP.Position.Z)))
										end
									end
								end)
							end	
						end
					end
				end
			end
		end
	end
end
OpAurasSection:AddToggle({
	Name = "Kick Aura",
	Callback = function(Value)
		KickAura = Value
		if KickAura then
			task.defer(Aura,"Kick",0)
		end
	end
})
OpAurasSection:AddDropdown({
	Name = "Kick Type",
	Default = "Float",
	Options = {"Float","Silent"},
	Callback = function(Value)
		KickAuraType = Value
	end
})
OpAurasSection:AddToggle({
	Name = "Kill Aura",
	Callback = function(Value)
		KillAura = Value
		if KillAura then
			task.defer(Aura,"Kill",0)
		end
	end
})
OpAurasSection:AddToggle({
	Name = "Delete Aura",
	Callback = function(Value)
		DeleteAura = Value
		if DeleteAura  then
			task.defer(Aura,"Delete",0)
		end
	end
})
FunAurasSection:AddToggle({
	Name = "Network Owner Aura",
	Callback = function(Value)
		NetOwnAura = Value
		if NetOwnAura then
			task.defer(Aura,"NetOwn",0.1)
		end
	end
})
FunAurasSection:AddToggle({
	Name = "Fling Aura",
	Callback = function(Value)
		FlingAura = Value
		if FlingAura then
			task.defer(Aura,"Fling",0.1)
		end
	end
})
FunAurasSection:AddToggle({
	Name = "Spin Aura",
	Callback = function(Value)
		SpinAura = Value
		if SpinAura then
			task.defer(Aura,"Spin",0)
		end
	end
})
FunAurasSection:AddToggle({
	Name = "Speed Aura",
	Callback = function(Value)
		SpeedAura = Value
		if SpeedAura then
			task.defer(Aura,"Speed",0.2)
		end
	end
})
FunAurasSection:AddSlider({
	Name = "Speed Ammount",
	Min = 0,
	Max = 1000,
	Default = 50,
	Increment = 1,
	ValueName = "Speed",
	Callback = function(Value)
		SpeedAuraAmmount = Value
	end
})
FunAurasSection:AddToggle({
	Name = "Jump Aura",
	Callback = function(Value)
		JumpAura = Value
		if JumpAura then
			task.defer(Aura,"Jump",0.2)
		end
	end
})
FunAurasSection:AddSlider({
	Name = "Jump Power Ammount",
	Min = 0,
	Max = 1000,
	Default = 100,
	Increment = 1,
	ValueName = "JumpPower",
	Callback = function(Value)
		JumpAuraAmmount = Value
	end
})
local KillGToggle = MainOP:AddToggle({
	Name = "Kill Grab",
	Callback = function(Value)
		KillGrabToggle = Value
		if KillGrabToggle and AutoPair then
			OrionLib:MakeNotification({
				Name = "Note",
				Content = "Paired with kick grab to make the player your holding die instantly. (Turn off auto pair to auto pairing them).",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
			KGToggler:Set(true)
		end
	end
})
BindsTab:AddBind({
	Name = "Kill Grab Bind",
	Default = Enum.KeyCode.U,
	Hold = false,
	Callback = function()
		if KillGrabToggle then
			GrabKill()
		end
	end
})
gyattmaxxer = false
BindsTab:AddBind({
	Name = "Aim-Lock Toggle Bind",
	Default = Enum.KeyCode.V,
	Hold = false,
	Callback = function()
		gyattmaxxer = not gyattmaxxer
		AimbotToggler:Set(gyattmaxxer)
	end
})
BindsTab:AddBind({
	Name = "Kill Grab Toggle Bind",
	Default = Enum.KeyCode.K,
	Hold = false,
	Callback = function()
		KillGrabToggle = not KillGrabToggle
		KillGToggle:Set(KillGrabToggle)
	end
})

GrabSettingsSection:AddToggle({
	Name = "Extend Grab Line",
	Callback = function(Value)
		ExtendGrabToggle = Value
		if ExtendGrabToggle then
			ControlGrab("Extend", true)
		else
			if connect then
				connect:Disconnect()
			end
		end
	end
})
GrabSettingsSection:AddSlider({
	Name = "Extended Distance",
	Min = 0,
	Max = 100,
	Default = 10,
	Increment = 1,
	ValueName = "Studs",
	Callback = function(Value)
		ExtendedDistance = Value
		ExtendMaxValue = Value
	end
})
GrabSettingsSection:AddToggle({
	Name = "Extend To Distance On Grab",
	Callback = function(Value)
		ExtendToDistanceOnGrab = Value
	end
})
GrabSettingsSection:AddSlider({
	Name = "Extend Increment",
	Min = 0.1,
	Max = 10,
	Default = 2,
	Increment = 0.1,
	ValueName = "Value",
	Callback = function(Value)
		ExtendIncrement = Value
	end
})
GrabSettingsSection:AddToggle({
	Name = "Stronger Throw",
	Callback = function(Value)
		StrongerThrowToggle = Value
	end
})
GrabSettingsSection:AddSlider({
	Name = "Throw Power",
	Min = 0,
	Max = 1500,
	Default = 400,
	Increment = 10,
	ValueName = "Power",
	Callback = function(Value)
		ThrowPower = Value
	end    
})
MainOP:AddToggle({
	Name = "Fling Grab",
	Callback = function(Value)
		FlingGrabToggle = Value
		if FlingGrabToggle and workspace:FindFirstChild("GrabParts") then
			ControlGrab("Fling")
		end
	end
})
MainOther:AddToggle({
	Name = "Keep Fling Grab's Velocity",
	Callback = function(Value)
		KeepFlingGrabVelocityToggle = Value
	end
})
MainOther:AddToggle({
	Name = "Noclip Grab Ignore Players",
	Default = true,
	Callback = function(Value)
		NoclipGrabIgnorePlayers = Value
	end
})
PlotBarrierGrabable = false
MainOther:AddToggle({
	Name = "Make Plot Barrier Not Grabbable",
	Callback = function(Value)
		PlotBarrierGrabable = Value
		local function makeungrababble(path,bool)
			for _,v in pairs(path:GetDescendants()) do
				if v.Name == "PlotBarrier" then
					v.CanQuery = bool
					v.CanCollide = bool
					v.CanTouch = bool
				end
			end
		end
		if PlotBarrierGrabable then
			makeungrababble(workspace.Plots,false)
		else
			makeungrababble(workspace.Plots,true)
		end
	end
})
MainOP:AddToggle({
	Name = "Noclip Grab",
	Callback = function(Value)
		NoclipGrabToggle = Value
		local conn
		if NoclipGrabToggle then
			ControlGrab("Noclip", true)
			conn = workspace.ChildAdded:Connect(function(thing)
				if thing.Name == "GrabParts" then
					if NoclipGrabToggle then
						wait()
						ControlGrab("Noclip", true)
					end
				end
			end)
		else
			ControlGrab("Noclip", false)
			if conn then
				conn:Disconnect()
			end
		end
	end
})

workspace.ChildAdded:Connect(function(Child)
	if Child.Name == "GrabParts" then
		Child:WaitForChild("GrabPart"):WaitForChild("WeldConstraint")
		local Target = Child.GrabPart.WeldConstraint.Part1

		if ControlGrabPropertiesToggle then ControlGrab("Mass", true) end
		if NetOwnGrabToggle then ControlGrab("NetOwn", true) end
		if FlingGrabToggle then ControlGrab("Fling", true) end
		if NoclipGrabToggle then ControlGrab("Noclip", true) end
		if ExtendGrabToggle then ControlGrab("Extend", true) end

		Child:GetPropertyChangedSignal("Parent"):Connect(function()
			if StrongerThrowToggle then
				local ThrowVelocity = TemplateBV:Clone()
				if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
					ThrowVelocity.Velocity = Camera.CFrame.LookVector * ThrowPower
					ThrowVelocity.Parent = Target
					game:GetService("Debris"):AddItem(ThrowVelocity, 1)
				else
					ThrowVelocity:Destroy()
				end
			end
		end)
	end
end)

ScriptSettings:AddToggle({
	Name = "Auto Pair",
	Default = true,
	Callback = function(Value)
		AutoPair = Value
	end
})
task.defer(function()
	while true do
		if not AntiLag and DetectLag then
			local FPS = workspace:GetRealPhysicsFPS()
			if FPS <= 25 then
				OrionLib:MakeNotification({
					Name = "Note",
					Content = "lag script detected! turned on anti lag. (If this is a false positive then you can always disable anti lag in Antis or disable the detector in script settings.)",
					Image = "rbxassetid://4483345998",
					Time = 10
				})
				if Alarm then
					local PortalAlarm = Instance.new("Sound")
					PortalAlarm.Name = "Portal Alarm"
					PortalAlarm.PlaybackSpeed = 0.95
					PortalAlarm.Pitch = 0.95
					PortalAlarm.SoundId = "rbxassetid://549313210"
					PortalAlarm.Volume = 0.25
					PortalAlarm.Parent = workspace

					local count = 0
					local max = 2

					local function sigmasex()
						count = count + 1
						if count <= max then
							PortalAlarm:Play()
						else
							PortalAlarm:Destroy()
						end
					end

					PortalAlarm.Ended:Connect(sigmasex)
					PortalAlarm:Play()
				end
				AntiLagToggle:Set(true)
				if setfpscap then
					setfpscap(10000)
				end
			end
		end
		task.wait()
	end
end)
Helpful:AddToggle({
	Name = "Detect Lag Script",
	Default = true,
	Callback = function(Value)
		DetectLag = Value
	end
})
DetectKickGrab = false
Helpful:AddToggle({
	Name = "Detect Kick Grab",
	Default = true,
	Callback = function(Value)
		DetectKickGrab = Value
		local conn
		if DetectKickGrab then
			local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
			if character then
				local HRP = character:FindFirstChild("HumanoidRootPart")
				if HRP then
					local FPP = HRP:FindFirstChild("FirePlayerPart")
					if FPP then
						conn = FPP.ChildAdded:Connect(function(child)
							if FPP.Name == "PartOwner" and DetectKickGrab then
								local args = {[1] = HRP, [2] = 0}
								game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
								AKGToggler:Set(true)
								OrionLib:MakeNotification({
									Name = "Note",
									Content = "Detected kick grab being used on you.",
									Image = "rbxassetid://4483345998",
									Time = 10
								})
							end
						end)
					end
				end
			end
		else
			if conn then
				conn:Disconnect()
			end
		end
	end
})
Helpful:AddToggle({
	Name = "Alarm When Lag Is Detected",
	Default = true,
	Callback = function(Value)
		Alarm = Value
	end
})
SendJunkData = false
Data:AddToggle({
	Name = "Send Junk Data To Clients",
	Callback = function(Value)
		SendJunkData = Value
		if SendJunkData then
			task.defer(function()
				while true do
					if not SendJunkData then break end
					game.ReplicatedStorage:WaitForChild("GrabEvents").ExtendGrabLine:FireServer(JunkData)
					RunService.Heartbeat:Wait()
				end
			end)
			OrionLib:MakeNotification({
				Name = "Hint",
				Content = "Every client recieves this data. The data is sent very fast.",
				Image = "rbxassetid://4483345998",
				Time = 5
			})
		end
	end
})
Data:AddTextbox({
	Name = "Data",
	ClearTextOnFocus = false,
	Callback = function(Value)
		JunkData = Value
	end
})

FriendCheckSection:AddToggle({
	Name = "Friend List Check",
	Default = true,
	Callback = function(Value)
		FriendListCheck = Value
	end
})
FriendCheckSection:AddToggle({
	Name = "Friend List Auto Update",
	Default = false,
	Callback = function(Value)
		FriendListAutoUpdate = Value
		if FriendListAutoUpdate then
			while task.wait(FriendListUpdateSpeed) do
				if not FriendListAutoUpdate then break end
				FriendList = {}
				for i, Plr in pairs(Players:GetPlayers()) do
					if LocalPlayer:IsFriendsWith(Plr.UserId) then
						table.insert(FriendList, Plr.Name)
					end
				end
			end
		end
	end
})
FriendCheckSection:AddButton({
	Name = "Update Friend List",
	Callback = function()
		FriendList = {}
		for _, Plr in pairs(Players:GetPlayers()) do
			if LocalPlayer:IsFriendsWith(Plr.UserId) then
				table.insert(FriendList, Plr.Name)
			end
		end
	end
})
FriendCheckSection:AddSlider({
	Name = "Friend List Update Speed",
	Min = 5,
	Max = 300,
	Default = 60,
	Increment = 5,
	ValueName = "Seconds",
	Callback = function(Value)
		FriendListUpdateSpeed = Value
	end
})
local WhitelistWhitelistedDropdown

function WhitelistAddToPlrList(Player)
	local PlayerName = Player.Name
	local PlayerDisplayName = Player.DisplayName
	if not table.find(WhitelistPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }") then
		table.insert(WhitelistPlayerList, PlayerName .. " { " .. PlayerDisplayName .. " }")
		UpdateAndRefresh(WhitelistPlayersDropdown, WhitelistPlayerList)
	end
end
function WhitelistRemoveFromWhitelist(Player)
	local PlayerName = Player.Name
	for i, v in ipairs(Whitelist) do
		if string.match(v, "^" .. PlayerName .. "%s") then
			table.remove(Whitelist, i)
			UpdateAndRefresh(WhitelistWhitelistedDropdown, Whitelist)
			break
		end
	end
end

WhitelistPlayersDropdown = WhitelistCheckSection:AddDropdown({
	Name = "Player List",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		WhitelistSelectedPlayer = Value
	end
})
WhitelistCheckSection:AddButton({
	Name = "Add To Whitelist",
	Callback = function()
		local playerName = string.match(WhitelistSelectedPlayer, "^(%S+)%s")
		for _, Plr in pairs(Players:GetPlayers()) do
			if Plr.Name == playerName then
				for i, Whitelisted in ipairs(Whitelist) do
					if string.match(Whitelisted, "^" .. playerName .. "%s") then
						table.remove(WhitelistPlayerList, i)
						UpdateAndRefresh(WhitelistPlayersDropdown, WhitelistPlayerList)
						break
					end
				end
				if not table.find(Whitelist, Plr.Name .. " { " .. Plr.DisplayName .. " }") then
					table.insert(Whitelist, Plr.Name .. " { " .. Plr.DisplayName .. " }")
					UpdateAndRefresh(WhitelistWhitelistedDropdown, Whitelist)
				end
				break
			end
		end
	end
})
WhitelistWhitelistedDropdown = WhitelistCheckSection:AddDropdown({
	Name = "Whitelisted",
	Default = nil,
	Options = {nil},
	Callback = function(Value)
		WhitelistSelectedWhitelisted = Value
	end
})
WhitelistCheckSection:AddButton({
	Name = "Remove From Whitelist",
	Callback = function()
		local playerName = string.match(WhitelistSelectedWhitelisted, "^(%S+)%s")
		WhitelistRemoveFromWhitelist(game.Players:FindFirstChild(playerName)) 
	end
})
WhitelistCheckSection:AddToggle({
	Name = "Whitelist Check",
	Default = true,
	Callback = function(Value)
		WhitelistCheck = Value
	end
})
WhitelistCheckSection:AddButton({
	Name = "Clear Whitelist",
	Callback = function()
		Whitelist = {}
	end
})
WhitelistCheckSection:AddToggle({
	Name = "Remove Whitelisted On Leave",
	Default = true,
	Callback = function(Value)
		RemoveWhitelistedOnLeave = Value
	end
})

function RemoveFromAllLists(Player)
	RemoveFromList(TargetPlayersDropdown, TargetPlayerList, Player)
	RemoveFromList(BlobmanPlayersDropdown, BlobmanPlayerList, Player)
	RemoveFromList(ExplosionTargetDropdown, ExplosionPlayerList, Player)
	RemoveFromList(WhitelistPlayersDropdown, WhitelistPlayerList, Player)
	RemoveFromList(NPCsTargetDropdown, NPCsPlayerList, Player)
end
function AddToAllLists(Player)
	TargetAddToPlrList(Player)
	task.wait(0.125)
	BlobmanAddToPlrList(Player)
	task.wait(0.125)
	ExplosionsAddToPlrList(Player)
	task.wait(0.125)
	WhitelistAddToPlrList(Player)
	task.wait(0.125)
	NPCsAddToPlrList(Player)
end

task.defer(function()
	for _, Plr in pairs(Players:GetPlayers()) do 
		AddToAllLists(Plr)
		task.wait(0.075)
	end
end)

Players.PlayerAdded:Connect(function(AddedPlr)
	AddToAllLists(AddedPlr)
end)

Players.PlayerRemoving:Connect(function(RemovingPlayer)
	RemoveFromAllLists(RemovingPlayer)
end)

OrionLib:Init()
