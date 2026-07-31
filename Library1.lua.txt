local Library = {}
local TS = game:GetService("TweenService")
local UIS = game:GetService("UserInputService")
local RS = game:GetService("RunService")
local HS = game:GetService("HttpService")
local TxtS = game:GetService("TextService")
local CG = game:GetService("CoreGui")
local Plrs = game:GetService("Players")
local LP = Plrs.LocalPlayer
local Mouse = LP:GetMouse()
local Stats = game:GetService("Stats")

local CurrentFPS = 60
local Frames = 0
local LastTick = os.clock()

RS.Heartbeat:Connect(function()
    Frames = Frames + 1
    local Now = os.clock()
    if Now - LastTick >= 1 then
        CurrentFPS = Frames
        Frames = 0
        LastTick = Now
    end
end)

local NetworkStats = Stats:FindFirstChild("Network")
local ServerStats = NetworkStats and NetworkStats:FindFirstChild("ServerStatsItem")
local DataPing = ServerStats and ServerStats:FindFirstChild("Data Ping")

local KeyMap = {
    Zero = "0", One = "1", Two = "2", Three = "3", Four = "4", Five = "5", Six = "6", Seven = "7", Eight = "8", Nine = "9",
    KeypadZero = "0", KeypadOne = "1", KeypadTwo = "2", KeypadThree = "3", KeypadFour = "4", KeypadFive = "5", KeypadSix = "6", KeypadSeven = "7", KeypadEight = "8", KeypadNine = "9",
    LeftShift = "LShift", RightShift = "RShift", LeftControl = "LCtrl", RightControl = "RCtrl", LeftAlt = "LAlt", RightAlt = "RAlt",
    LeftBracket = "[", RightBracket = "]", Semicolon = ";", Quote = "'", BackSlash = "\\", Comma = ",", Period = ".", Slash = "/", Minus = "-", Equals = "=",
    Space = "Space", Tab = "Tab", Return = "Enter", Escape = "Esc", Backspace = "Bksp", Delete = "Del", Insert = "Ins",
    Home = "Home", End = "End", PageUp = "PgUp", PageDown = "PgDn", Up = "Up", Down = "Down", Left = "Left", Right = "Right",
    CapsLock = "Caps", NumLock = "Num"
}

Library.Flags = {}
Library.Items = {}
Library.TextObjects = {} 
Library.GradientObjects = {} 
Library.CornerObjects = {}
Library.KeybindFlags = {}
Library.IsLoadingConfig = false

Library.GlobalScale = 1.0
Library.ScaleObj = nil
Library.ScaleWrapper = nil

Library.ThemeObjects = {     
    Main = {},
    Second = {},
    Accent = {},
    ElementAccent = {},
    Text = {},
    TextDark = {},
    Toggles = {},
    TabLabels = {},
    Keybinds = {}
}
Library.WatermarkIslands = {}

Library.ThemeFolder = "SolarisUI-Themes"

local AvailableFonts = {
    "Gotham", "GothamBold", "SourceSans", "SourceSansBold", 
    "Oswald", "Roboto", "RobotoMono", "Sarpanch", "Code", "AmaticSC",
    "FredokaOne", "Jura", "Arcade", "SciFi", "Ubuntu", "Arial",
    "Cartoon", "Highway", "Bodoni", "Garamond", "Nunito"
}

Library.GlobalFont = Enum.Font.Gotham
Library.GlobalFontBold = Enum.Font.GothamBold
Library.GlobalCornerValue = 10

local Themes = {
    Default = {
        Main = Color3.fromRGB(25, 25, 30),
        Second = Color3.fromRGB(35, 35, 40),
        Accent = Color3.fromRGB(255, 255, 255),
        ElementAccent = Color3.fromRGB(0, 160, 255),
        GradientStart = Color3.fromRGB(255, 255, 255),
        GradientEnd = Color3.fromRGB(200, 200, 200),
        Text = Color3.fromRGB(255, 255, 255),
        TextDark = Color3.fromRGB(170, 170, 170),
        Error = Color3.fromRGB(255, 60, 60),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Blood = {
        Main = Color3.fromRGB(20, 15, 15),
        Second = Color3.fromRGB(30, 20, 20),
        Accent = Color3.fromRGB(220, 40, 40),
        ElementAccent = Color3.fromRGB(220, 40, 40),
        GradientStart = Color3.fromRGB(255, 0, 0),
        GradientEnd = Color3.fromRGB(150, 0, 0),
        Text = Color3.fromRGB(255, 240, 240),
        TextDark = Color3.fromRGB(170, 120, 120),
        Error = Color3.fromRGB(255, 0, 0),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Purple = {
        Main = Color3.fromRGB(20, 15, 25),
        Second = Color3.fromRGB(30, 25, 40),
        Accent = Color3.fromRGB(160, 80, 255),
        ElementAccent = Color3.fromRGB(160, 80, 255),
        GradientStart = Color3.fromRGB(140, 0, 255),
        GradientEnd = Color3.fromRGB(255, 0, 255),
        Text = Color3.fromRGB(240, 230, 255),
        TextDark = Color3.fromRGB(160, 140, 190),
        Error = Color3.fromRGB(255, 0, 100),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Abyss = {
        Main = Color3.fromRGB(20, 15, 25),
        Second = Color3.fromRGB(30, 25, 40),
        Accent = Color3.fromRGB(100, 0, 255),
        ElementAccent = Color3.fromRGB(100, 0, 255),
        GradientStart = Color3.fromRGB(80, 0, 200),
        GradientEnd = Color3.fromRGB(120, 50, 255),
        Text = Color3.fromRGB(240, 230, 255),
        TextDark = Color3.fromRGB(160, 140, 190),
        Error = Color3.fromRGB(255, 0, 100),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Ocean = {
        Main = Color3.fromRGB(15, 25, 35),
        Second = Color3.fromRGB(25, 35, 45),
        Accent = Color3.fromRGB(0, 255, 200),
        ElementAccent = Color3.fromRGB(0, 255, 200),
        GradientStart = Color3.fromRGB(0, 200, 255),
        GradientEnd = Color3.fromRGB(0, 255, 150),
        Text = Color3.fromRGB(220, 255, 255),
        TextDark = Color3.fromRGB(120, 170, 170),
        Error = Color3.fromRGB(255, 80, 80),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Toxic = {
        Main = Color3.fromRGB(10, 20, 10),
        Second = Color3.fromRGB(20, 30, 20),
        Accent = Color3.fromRGB(50, 255, 100),
        ElementAccent = Color3.fromRGB(50, 255, 100),
        GradientStart = Color3.fromRGB(0, 255, 0),
        GradientEnd = Color3.fromRGB(150, 255, 150),
        Text = Color3.fromRGB(220, 255, 220),
        TextDark = Color3.fromRGB(120, 170, 170),
        Error = Color3.fromRGB(255, 50, 50),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Sunrise = {
        Main = Color3.fromRGB(30, 20, 15),
        Second = Color3.fromRGB(40, 30, 25),
        Accent = Color3.fromRGB(255, 150, 0),
        ElementAccent = Color3.fromRGB(255, 150, 0),
        GradientStart = Color3.fromRGB(255, 100, 0),
        GradientEnd = Color3.fromRGB(255, 200, 0),
        Text = Color3.fromRGB(255, 240, 230),
        TextDark = Color3.fromRGB(170, 140, 120),
        Error = Color3.fromRGB(255, 0, 0),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Vaporwave = {
        Main = Color3.fromRGB(30, 20, 35),
        Second = Color3.fromRGB(45, 30, 50),
        Accent = Color3.fromRGB(255, 100, 200),
        ElementAccent = Color3.fromRGB(255, 100, 200),
        GradientStart = Color3.fromRGB(255, 0, 255),
        GradientEnd = Color3.fromRGB(0, 255, 255),
        Text = Color3.fromRGB(255, 230, 255),
        TextDark = Color3.fromRGB(170, 120, 170),
        Error = Color3.fromRGB(255, 50, 100),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Gold = {
        Main = Color3.fromRGB(25, 20, 10),
        Second = Color3.fromRGB(35, 30, 20),
        Accent = Color3.fromRGB(255, 200, 50),
        ElementAccent = Color3.fromRGB(255, 200, 50),
        GradientStart = Color3.fromRGB(255, 215, 0),
        GradientEnd = Color3.fromRGB(255, 150, 0),
        Text = Color3.fromRGB(255, 250, 220),
        TextDark = Color3.fromRGB(170, 160, 120),
        Error = Color3.fromRGB(255, 50, 50),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Orange = {
        Main = Color3.fromRGB(20, 20, 20),
        Second = Color3.fromRGB(35, 30, 25),
        Accent = Color3.fromRGB(218, 165, 32),
        ElementAccent = Color3.fromRGB(218, 165, 32),
        GradientStart = Color3.fromRGB(255, 215, 0),
        GradientEnd = Color3.fromRGB(184, 134, 11),
        Text = Color3.fromRGB(255, 250, 220),
        TextDark = Color3.fromRGB(170, 160, 120),
        Error = Color3.fromRGB(255, 50, 50),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Mint = {
        Main = Color3.fromRGB(20, 25, 25),
        Second = Color3.fromRGB(30, 35, 35),
        Accent = Color3.fromRGB(100, 255, 180),
        ElementAccent = Color3.fromRGB(100, 255, 180),
        GradientStart = Color3.fromRGB(50, 200, 120),
        GradientEnd = Color3.fromRGB(150, 255, 200),
        Text = Color3.fromRGB(230, 255, 240),
        TextDark = Color3.fromRGB(120, 160, 140),
        Error = Color3.fromRGB(255, 80, 80),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Night = {
        Main = Color3.fromRGB(10, 10, 20),
        Second = Color3.fromRGB(20, 20, 35),
        Accent = Color3.fromRGB(80, 120, 255),
        ElementAccent = Color3.fromRGB(80, 120, 255),
        GradientStart = Color3.fromRGB(50, 50, 255),
        GradientEnd = Color3.fromRGB(150, 150, 255),
        Text = Color3.fromRGB(230, 230, 255),
        TextDark = Color3.fromRGB(100, 120, 160),
        Error = Color3.fromRGB(255, 80, 80),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    },
    Void = {
        Main = Color3.fromRGB(12, 12, 12),
        Second = Color3.fromRGB(22, 22, 22),
        Accent = Color3.fromRGB(220, 220, 220),
        ElementAccent = Color3.fromRGB(220, 220, 220),
        GradientStart = Color3.fromRGB(150, 150, 150),
        GradientEnd = Color3.fromRGB(255, 255, 255),
        Text = Color3.fromRGB(240, 240, 240),
        TextDark = Color3.fromRGB(120, 120, 120),
        Error = Color3.fromRGB(255, 100, 100),
        Transparency = 0.25,
        HudTransparency = 0.5,
        ImageTransparency = 0,
        Font = "Gotham",
        Background = "",
        UiScale = 1.0
    }
}

local function forceLetterWrap(text)
    if not text then return "" end
    local chars = {}
    for _, code in utf8.codes(text) do
        table.insert(chars, utf8.char(code))
    end
    return table.concat(chars, utf8.char(0x200B))
end

local function formatSliderValue(val)
    local rounded = math.round(val * 1000) / 1000
    return tostring(rounded)
end

local function SetupAutoScroll(scrollingFrame, textLabel)
    local tweenX
    local function checkAndScroll()
        if tweenX then tweenX:Cancel() tweenX = nil end
        scrollingFrame.CanvasPosition = Vector2.new(0, 0)
        
        task.wait(0.1) 
        if not textLabel.Parent or not scrollingFrame.Parent then return end
        
        local textWidth = textLabel.TextBounds.X
        local windowWidth = scrollingFrame.AbsoluteWindowSize.X
        
        if textWidth > windowWidth and windowWidth > 0 then
            local excess = textWidth - windowWidth + 6
            local function loop()
                if not textLabel.Parent or not scrollingFrame.Parent then return end
                tweenX = TS:Create(scrollingFrame, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {CanvasPosition = Vector2.new(excess, 0)})
                tweenX:Play()
                tweenX.Completed:Connect(function(state)
                    if state == Enum.PlaybackState.Completed then
                        task.wait(1.0)
                        if not textLabel.Parent or not scrollingFrame.Parent then return end
                        tweenX = TS:Create(scrollingFrame, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {CanvasPosition = Vector2.new(0, 0)})
                        tweenX:Play()
                        tweenX.Completed:Connect(function(state2)
                            if state2 == Enum.PlaybackState.Completed then
                                task.wait(1.0)
                                loop()
                            end
                        end)
                    end
                end)
            end
            loop()
        end
    end

    textLabel:GetPropertyChangedSignal("Text"):Connect(checkAndScroll)
    scrollingFrame:GetPropertyChangedSignal("AbsoluteSize"):Connect(checkAndScroll)
    task.spawn(checkAndScroll)
end

local function UpdateScale(val)
    Library.GlobalScale = val
    if Library.ScaleObj then
        Library.ScaleObj.Scale = val
    end
    if Library.ScaleWrapper then
        Library.ScaleWrapper.Size = UDim2.new(1 / val, 0, 1 / val, -45 / val)
    end
end

local function GetGradientSeq(theme)
    local startC = theme.GradientStart or theme.Accent
    local endC = theme.GradientEnd or theme.Accent
    return ColorSequence.new{
        ColorSequenceKeypoint.new(0, startC), 
        ColorSequenceKeypoint.new(1, endC)
    }
end

for _, t in pairs(Themes) do
    if not t.Gradient then
        t.Gradient = GetGradientSeq(t)
    end
    if not t.ElementAccent then
        t.ElementAccent = t.Accent
    end
    if not t.Font then
        t.Font = "Gotham"
    end
    if not t.HudTransparency then
        t.HudTransparency = 0.5
    end
end

local function GetAssetId(id)
    if not id or id == "" then
        return ""
    end
    local str = tostring(id)
    if str:find("rbxasset://") or str:find("rbxthumb://") or str:find("rbxassetid://") then
        return str
    end
    local num = str:match("%d+")
    if num then
        return "rbxassetid://" .. num
    end
    return ""
end

local function SetImageAsync(instance, property, idStr)
    local parsedId = GetAssetId(idStr)
    if parsedId == "" then
        instance[property] = ""
        return
    end
    
    instance[property] = parsedId
    
    if parsedId:find("rbxassetid://") then
        task.spawn(function()
            local success, result = pcall(function()
                return game:GetObjects(parsedId)[1]
            end)
            if success and result and result:IsA("Decal") then
                if instance.Parent then
                    instance[property] = result.Texture
                end
            end
        end)
    end
end

local function GetTheme(cfg)
    if type(cfg) == "table" then 
        local mapped = {}
        mapped.Main = cfg["Main color"] or cfg.Main
        mapped.Second = cfg["SecondColor"] or cfg.Second
        mapped.Accent = cfg["AccentColor"] or cfg.Accent
        mapped.ElementAccent = cfg["ElementColor"] or cfg.ElementAccent
        mapped.Text = cfg["TextColor"] or cfg.Text
        mapped.GradientStart = cfg["GradientStart"] or mapped.Accent
        mapped.GradientEnd = cfg["GradientEnd"] or mapped.Accent
        mapped.UiScale = cfg["UiScale"] or cfg.UiScale or 1.0
        
        if cfg["BGTransparency"] then
            mapped.Transparency = tonumber(cfg["BGTransparency"]) / 100 
        else
            mapped.Transparency = cfg.Transparency
        end
        
        if cfg["HudTransparency"] then
            mapped.HudTransparency = tonumber(cfg["HudTransparency"]) / 100
        elseif cfg.HudTransparency then
            mapped.HudTransparency = cfg.HudTransparency
        else
            mapped.HudTransparency = 0.5
        end
        
        if cfg["ImageTransparency"] then
            mapped.ImageTransparency = tonumber(cfg["ImageTransparency"]) / 100
        else
            mapped.ImageTransparency = cfg.ImageTransparency
        end

        if cfg["BackgroundID"] then
            mapped.Background = GetAssetId(cfg["BackgroundID"])
        else 
            mapped.Background = GetAssetId(cfg.Background) 
        end

        if cfg["Font"] then
            mapped.Font = cfg["Font"]
        end

        if cfg["CornerRadius"] ~= nil then
            mapped.CornerRadius = cfg["CornerRadius"]
        end

        if not mapped.Main then mapped.Main = Themes.Default.Main end
        if not mapped.Second then mapped.Second = Themes.Default.Second end
        if not mapped.Accent then mapped.Accent = Themes.Default.Accent end
        if not mapped.ElementAccent then mapped.ElementAccent = mapped.Accent end
        if not mapped.Text then mapped.Text = Themes.Default.Text end
        if not mapped.TextDark then mapped.TextDark = Color3.fromRGB(170, 170, 170) end
        if not mapped.Error then mapped.Error = Color3.fromRGB(255, 60, 60) end
        if not mapped.GradientStart then mapped.GradientStart = mapped.Accent end
        if not mapped.GradientEnd then mapped.GradientEnd = mapped.Accent end
        
        mapped.Gradient = GetGradientSeq(mapped)
        return mapped
    end

    if isfile(Library.ThemeFolder .. "/" .. tostring(cfg) .. ".json") then
        local content = readfile(Library.ThemeFolder .. "/" .. tostring(cfg) .. ".json")
        local parsed = HS:JSONDecode(content)
        local restored = {}
        for k, v in pairs(parsed) do
            if type(v) == "table" and v.R then
                restored[k] = Color3.new(v.R, v.G, v.B)
            else
                restored[k] = v
            end
        end
        if restored.GradientStart and restored.GradientEnd then
            restored.Gradient = ColorSequence.new{
                ColorSequenceKeypoint.new(0, restored.GradientStart),
                ColorSequenceKeypoint.new(1, restored.GradientEnd)
            }
        else
            restored.GradientStart = restored.Accent
            restored.GradientEnd = restored.Accent
            restored.Gradient = ColorSequence.new{
                ColorSequenceKeypoint.new(0, restored.Accent),
                ColorSequenceKeypoint.new(1, restored.Accent)
            }
        end
        if not restored.ElementAccent then restored.ElementAccent = restored.Accent end
        if not restored.Font then restored.Font = "Gotham" end
        if not restored.TextDark then restored.TextDark = Color3.fromRGB(170, 170, 170) end
        if not restored.Error then restored.Error = Color3.fromRGB(255, 60, 60) end
        if restored.CornerRadius == nil then restored.CornerRadius = 10 end
        if restored.HudTransparency == nil then restored.HudTransparency = 0.5 end
        if restored.UiScale == nil then restored.UiScale = 1.0 end
        return restored
    end
    return Themes[cfg] or Themes.Default
end

local function Create(class, properties)
    local tag = properties.ThemeTag
    properties.ThemeTag = nil
    local instance = Instance.new(class)
    for k, v in pairs(properties) do
        instance[k] = v
    end
    if class == "TextLabel" or class == "TextButton" or class == "TextBox" then
        table.insert(Library.TextObjects, instance)
        if not properties.Font then
            instance.Font = Library.GlobalFont
        end
    end
    if tag and Library.ThemeObjects[tag] then
        table.insert(Library.ThemeObjects[tag], instance)
    end
    return instance
end

local function UpdateFonts(FontName)
    local NewFont = Enum.Font[FontName] or Enum.Font.Gotham
    Library.GlobalFont = NewFont
    local BoldName = FontName .. "Bold"
    if pcall(function() return Enum.Font[BoldName] end) then
        Library.GlobalFontBold = Enum.Font[BoldName]
    else
        Library.GlobalFontBold = NewFont
    end

    for _, obj in pairs(Library.TextObjects) do
        if obj and obj.Parent then
            local s = tostring(obj.Font)
            if s:find("Bold") then
                obj.Font = Library.GlobalFontBold
            else
                obj.Font = Library.GlobalFont
            end
        end
    end
end

local function UpdateThemeObjects()
    for i = #Library.ThemeObjects.Main, 1, -1 do
        local obj = Library.ThemeObjects.Main[i]
        if obj and obj.Parent then
            obj.BackgroundColor3 = Themes.Default.Main
        else
            table.remove(Library.ThemeObjects.Main, i)
        end
    end
    for i = #Library.ThemeObjects.Second, 1, -1 do
        local obj = Library.ThemeObjects.Second[i]
        if obj and obj.Parent then
            obj.BackgroundColor3 = Themes.Default.Second
        else
            table.remove(Library.ThemeObjects.Second, i)
        end
    end
    for i = #Library.ThemeObjects.Accent, 1, -1 do
        local obj = Library.ThemeObjects.Accent[i]
        if obj and obj.Parent then
            if obj:IsA("ImageLabel") or obj:IsA("ImageButton") then
                obj.ImageColor3 = Themes.Default.Accent
            elseif obj:IsA("UIStroke") then
                obj.Color = Themes.Default.Accent
            else
                obj.BackgroundColor3 = Themes.Default.Accent
            end
        else
            table.remove(Library.ThemeObjects.Accent, i)
        end
    end
    for i = #Library.ThemeObjects.ElementAccent, 1, -1 do
        local obj = Library.ThemeObjects.ElementAccent[i]
        if obj and obj.Parent then
            if obj:IsA("ImageLabel") then
                obj.ImageColor3 = Themes.Default.ElementAccent
            elseif obj:IsA("UIStroke") then
                obj.Color = Themes.Default.ElementAccent
            else
                obj.BackgroundColor3 = Themes.Default.ElementAccent
            end
        else
            table.remove(Library.ThemeObjects.ElementAccent, i)
        end
    end
    for i = #Library.ThemeObjects.Text, 1, -1 do
        local obj = Library.ThemeObjects.Text[i]
        if obj and obj.Parent then 
            if obj:IsA("TextLabel") or obj:IsA("TextBox") or obj:IsA("TextButton") then
                obj.TextColor3 = Themes.Default.Text 
            elseif obj:IsA("Frame") then
                obj.BackgroundColor3 = Themes.Default.Text
            end
        else
            table.remove(Library.ThemeObjects.Text, i)
        end
    end
    for i = #Library.ThemeObjects.TextDark, 1, -1 do
        local obj = Library.ThemeObjects.TextDark[i]
        if obj and obj.Parent then 
            if obj:IsA("ImageLabel") then
                obj.ImageColor3 = Themes.Default.TextDark
            elseif obj:IsA("Frame") then
                obj.BackgroundColor3 = Themes.Default.TextDark
            elseif obj:IsA("UIStroke") then
                obj.Color = Themes.Default.TextDark
            else
                obj.TextColor3 = Themes.Default.TextDark
            end
        else
            table.remove(Library.ThemeObjects.TextDark, i)
        end
    end
    for i = #Library.ThemeObjects.Toggles, 1, -1 do
        local t = Library.ThemeObjects.Toggles[i]
        if t.Box and t.Box.Parent and t.Stroke and t.Stroke.Parent and t.Square and t.Square.Parent then
            if t.State() then
                t.Stroke.Color = Themes.Default.ElementAccent
                t.Stroke.Transparency = 0
                t.Square.BackgroundColor3 = Themes.Default.ElementAccent
            else
                t.Stroke.Color = Themes.Default.TextDark
                t.Stroke.Transparency = 0.8
                t.Square.BackgroundColor3 = Themes.Default.TextDark
            end
        else
            table.remove(Library.ThemeObjects.Toggles, i)
        end
    end
    for i = #Library.ThemeObjects.TabLabels, 1, -1 do
        local tab = Library.ThemeObjects.TabLabels[i]
        if tab.Label and tab.Label.Parent then
            if tab.Btn.BackgroundTransparency < 0.8 then 
                tab.Label.TextColor3 = Themes.Default.Text 
                if tab.Icon then
                    tab.Icon.ImageColor3 = Themes.Default.ElementAccent
                end
            else 
                tab.Label.TextColor3 = Themes.Default.TextDark 
                if tab.Icon then
                    tab.Icon.ImageColor3 = Themes.Default.TextDark
                end
            end
        else
            table.remove(Library.ThemeObjects.TabLabels, i)
        end
    end
    for i = #Library.ThemeObjects.Keybinds, 1, -1 do
        local btn = Library.ThemeObjects.Keybinds[i]
        if btn and btn.Parent then
            if btn.Text == "..." then
                btn.TextColor3 = Themes.Default.Accent
            else
                btn.TextColor3 = Themes.Default.TextDark
            end
        else
            table.remove(Library.ThemeObjects.Keybinds, i)
        end
    end
end

local function UpdateGradients(newGradient)
    for _, grad in pairs(Library.GradientObjects) do
        if grad and grad.Parent then
            grad.Color = newGradient
        end
    end
end

local function UpdateCorners(val)
    Library.GlobalCornerValue = val
    for i = #Library.CornerObjects, 1, -1 do
        local obj = Library.CornerObjects[i]
        if obj.Corner and obj.Corner.Parent then
            obj.Corner.CornerRadius = UDim.new(0, math.floor(obj.BaseRadius * (val / 10)))
        else
            table.remove(Library.CornerObjects, i)
        end
    end
end

local function AddCorner(instance, radius)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, math.floor(radius * (Library.GlobalCornerValue / 10)))
    corner.Parent = instance
    table.insert(Library.CornerObjects, {Corner = corner, BaseRadius = radius})
    return corner
end

local function CreateDropShadow(parent, blurRadius, opacity)
    local Shadow = Create("ImageLabel", {
        Parent = parent,
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.fromScale(0.5, 0.5),
        Size = UDim2.new(1, blurRadius * 2, 1, blurRadius * 2),
        BackgroundTransparency = 1,
        Image = "rbxassetid://5554836806", 
        ImageColor3 = Color3.new(0, 0, 0),
        ImageTransparency = opacity or 0.5,
        ScaleType = Enum.ScaleType.Slice,
        SliceCenter = Rect.new(120, 120, 136, 136),
        ZIndex = (parent.ZIndex or 1) - 1
    })
    return Shadow
end

local function AddStroke(instance, theme)
    local stroke = Create("UIStroke", {
        Color = Color3.new(1, 1, 1),
        Thickness = 1.2,
        Transparency = 0.5,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
        Parent = instance
    })
    local gradient = Create("UIGradient", {
        Color = theme.Gradient,
        Rotation = 45,
        Parent = stroke
    })
    table.insert(Library.GradientObjects, gradient)
    return stroke
end

local function CreateRipple(btn, color)
    btn.ClipsDescendants = true
    btn.MouseButton1Click:Connect(function()
        local ripple = Create("ImageLabel", {
            Name = "Ripple",
            Parent = btn,
            Image = "rbxassetid://4743389506",
            ImageColor3 = color,
            BackgroundTransparency = 1,
            ImageTransparency = 0.5,
            Position = UDim2.new(0, Mouse.X - btn.AbsolutePosition.X, 0, Mouse.Y - btn.AbsolutePosition.Y),
            Size = UDim2.new(0, 0, 0, 0),
            ZIndex = 20,
            AnchorPoint = Vector2.new(0.5, 0.5)
        })
        local tween = TS:Create(ripple, TweenInfo.new(0.6, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0, 500, 0, 500), ImageTransparency = 1})
        tween:Play()
        tween.Completed:Connect(function()
            ripple:Destroy()
        end)
    end)
end

local function MakeDraggable(topbar, frame)
    local dragging, dragInput, dragStart, startPos
    topbar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    topbar.InputChanged:Connect(function(input) 
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end 
    end)
    UIS.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            TS:Create(frame, TweenInfo.new(0.08, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)}):Play()
        end
    end)
end

local function MakeResizable(handle, frame)
    local dragging, dragStart, startSize
    local MinSize = Vector2.new(500, 350)
    local MaxSize = Vector2.new(1000, 800)
    
    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startSize = frame.AbsoluteSize
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    
    UIS.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            local newX = math.clamp(startSize.X + delta.X, MinSize.X, MaxSize.X)
            local newY = math.clamp(startSize.Y + delta.Y, MinSize.Y, MaxSize.Y)
            TS:Create(frame, TweenInfo.new(0.08, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {Size = UDim2.fromOffset(newX, newY)}):Play()
        end
    end)
end

local function AttachHorizontalScroll(box, scrollingFrame, defaultAlignment)
    defaultAlignment = defaultAlignment or Enum.TextXAlignment.Left
    
    box:GetPropertyChangedSignal("TextBounds"):Connect(function()
        if scrollingFrame.AbsoluteWindowSize.X > 0 and box.TextBounds.X > scrollingFrame.AbsoluteWindowSize.X then
            box.TextXAlignment = Enum.TextXAlignment.Left
        else
            box.TextXAlignment = defaultAlignment
        end
    end)

    box:GetPropertyChangedSignal("CursorPosition"):Connect(function()
        if box:IsFocused() and box.CursorPosition > 0 then
            
            local textToCursor = string.sub(box.Text, 1, box.CursorPosition - 1)
            local size = TxtS:GetTextSize(textToCursor, box.TextSize, box.Font, Vector2.new(10000, 100))
            
            local visibleStart = scrollingFrame.CanvasPosition.X
            local visibleEnd = visibleStart + scrollingFrame.AbsoluteWindowSize.X
            
            local padding = 12
            if box.TextXAlignment == Enum.TextXAlignment.Left then
                if size.X > visibleEnd - padding then
                    TS:Create(scrollingFrame, TweenInfo.new(0.1), {CanvasPosition = Vector2.new(size.X - scrollingFrame.AbsoluteWindowSize.X + padding * 2, 0)}):Play()
                elseif size.X < visibleStart + padding then
                    TS:Create(scrollingFrame, TweenInfo.new(0.1), {CanvasPosition = Vector2.new(math.max(0, size.X - padding * 2), 0)}):Play()
                end
            end
        end
    end)
end

function Library:KeySystem(Settings)
    local Config = Settings or {}
    local Key = Config.Key
    if not Key or Key == "" then
        return
    end
    
    local LinkToCopy = tostring(Config.Link or "https://google.com")
    local SelectedTheme = GetTheme(Config.Theme)
    local Validated = false

    local ScreenGui = Create("ScreenGui", {
        Name = "KeyUI",
        ResetOnSpawn = false,
        DisplayOrder = 20000
    })
    
    if RS:IsStudio() then
        ScreenGui.Parent = LP:WaitForChild("PlayerGui")
    else
        pcall(function()
            ScreenGui.Parent = CG
        end)
        if not ScreenGui.Parent then
            ScreenGui.Parent = LP:WaitForChild("PlayerGui")
        end
    end

    local VP = workspace.CurrentCamera.ViewportSize
    local IsMobile = UIS.TouchEnabled and (VP.X < 850 or VP.Y < 600)
    local KeySizeX = IsMobile and math.min(VP.X - 40, 350) or 450
    local KeySizeY = IsMobile and math.min(VP.Y - 40, 220) or 260

    local KeyContainer = Create("Frame", {
        Parent = ScreenGui,
        Size = UDim2.fromOffset(0, 0),
        Position = UDim2.fromScale(0.5, 0.5),
        AnchorPoint = Vector2.new(0.5, 0.5),
        BackgroundTransparency = 1,
        ZIndex = 2
    })
    
    local Main = Create("Frame", {
        Parent = KeyContainer,
        Size = UDim2.fromScale(1, 1),
        BackgroundColor3 = SelectedTheme.Main,
        BackgroundTransparency = 0.05,
        ClipsDescendants = true
    })
    AddCorner(Main, 12)
    AddStroke(Main, SelectedTheme)
    
    local Shadow1 = CreateDropShadow(KeyContainer, 80, 0.3)
    local Shadow2 = CreateDropShadow(KeyContainer, 30, 0.4)
    Shadow1.ImageTransparency = 1
    Shadow2.ImageTransparency = 1

    TS:Create(KeyContainer, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.fromOffset(KeySizeX, KeySizeY)}):Play()
    TS:Create(Shadow1, TweenInfo.new(0.6), {ImageTransparency = 0.35}):Play()
    TS:Create(Shadow2, TweenInfo.new(0.6), {ImageTransparency = 0.4}):Play()
    
    local Content = Create("Frame", {
        Parent = Main,
        Size = UDim2.fromScale(1, 1),
        BackgroundTransparency = 1,
        ZIndex = 2
    })
    
    Create("TextLabel", {
        Parent = Content,
        Size = UDim2.new(1, 0, 0, 60),
        BackgroundTransparency = 1,
        Text = Config.Title or "Security Access",
        Font = Library.GlobalFontBold,
        TextColor3 = SelectedTheme.Text,
        TextSize = 24,
        ZIndex = 3
    })
    
    local InputBG = Create("ScrollingFrame", {
        Parent = Content,
        Size = UDim2.new(1, -60, 0, 48),
        Position = UDim2.new(0, 30, 0, 75),
        BackgroundColor3 = SelectedTheme.Second,
        BackgroundTransparency = 0.4,
        ZIndex = 3,
        ClipsDescendants = true,
        CanvasSize = UDim2.new(0, 0, 0, 0),
        ScrollBarThickness = 0,
        ScrollingDirection = Enum.ScrollingDirection.X,
        AutomaticCanvasSize = Enum.AutomaticSize.X
    })
    AddCorner(InputBG, 8)
    
    Create("UIPadding", {
        Parent = InputBG,
        PaddingLeft = UDim.new(0, 10),
        PaddingRight = UDim.new(0, 10)
    })
    
    local InputStroke = AddStroke(InputBG, SelectedTheme)
    InputStroke.Transparency = 0.8
    
    local Input = Create("TextBox", {
        Parent = InputBG,
        Size = UDim2.new(1, 0, 1, 0),
        Position = UDim2.new(0, 0, 0, 0),
        AutomaticSize = Enum.AutomaticSize.X,
        BackgroundTransparency = 1,
        Text = "",
        PlaceholderText = "Enter License Key...",
        TextColor3 = SelectedTheme.Text,
        Font = Library.GlobalFont,
        TextSize = 15,
        ZIndex = 4,
        TextWrapped = false,
        ClearTextOnFocus = false,
        TextXAlignment = Enum.TextXAlignment.Left
    })
    AttachHorizontalScroll(Input, InputBG, Enum.TextXAlignment.Left)

    Input.Focused:Connect(function()
        TS:Create(InputStroke, TweenInfo.new(0.3), {Transparency = 0}):Play()
    end)
    Input.FocusLost:Connect(function()
        TS:Create(InputStroke, TweenInfo.new(0.3), {Transparency = 0.8}):Play()
    end)

    local CheckColor = Color3.new(SelectedTheme.Accent.R * 0.8, SelectedTheme.Accent.G * 0.8, SelectedTheme.Accent.B * 0.8)
    
    local CheckBtn = Create("TextButton", {
        Parent = Content,
        Size = UDim2.new(0.5, -40, 0, 42),
        Position = UDim2.new(0, 30, 0, 145),
        BackgroundColor3 = CheckColor,
        BackgroundTransparency = 0.1,
        Text = "Verify Key",
        Font = Library.GlobalFontBold,
        TextColor3 = SelectedTheme.Main,
        TextSize = 14,
        ZIndex = 3
    })
    AddCorner(CheckBtn, 8)
    CreateRipple(CheckBtn, Color3.new(1, 1, 1))
    
    local GetKeyBtn = Create("TextButton", {
        Parent = Content,
        Size = UDim2.new(0.5, -40, 0, 42),
        Position = UDim2.new(0.5, 10, 0, 145),
        BackgroundColor3 = SelectedTheme.Second,
        BackgroundTransparency = 0.4,
        Text = "Get Key Link",
        Font = Library.GlobalFontBold,
        TextColor3 = SelectedTheme.Text,
        TextSize = 14,
        ZIndex = 3
    })
    AddCorner(GetKeyBtn, 8)
    CreateRipple(GetKeyBtn, SelectedTheme.Accent)
    AddStroke(GetKeyBtn, SelectedTheme).Transparency = 0.7
    
    local Status = Create("TextLabel", {
        Parent = Content,
        Size = UDim2.new(1, 0, 0, 20),
        Position = UDim2.new(0, 0, 1, -35),
        BackgroundTransparency = 1,
        Text = "Protected System",
        Font = Library.GlobalFont,
        TextColor3 = SelectedTheme.TextDark,
        TextSize = 13,
        ZIndex = 3
    })

    local function BtnHover(btn, isAccent)
        local hoverTween, leaveTween
        btn.MouseEnter:Connect(function()
            if leaveTween then leaveTween:Cancel() end
            hoverTween = TS:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = isAccent and 0 or 0.2})
            hoverTween:Play()
        end)
        btn.MouseLeave:Connect(function()
            if hoverTween then hoverTween:Cancel() end
            leaveTween = TS:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = isAccent and 0.1 or 0.4})
            leaveTween:Play()
        end)
    end
    BtnHover(CheckBtn, true)
    BtnHover(GetKeyBtn, false)

    GetKeyBtn.MouseButton1Click:Connect(function() 
        local success, err = pcall(function()
            if setclipboard then
                setclipboard(LinkToCopy)
                return true
            elseif toclipboard then
                toclipboard(LinkToCopy)
                return true
            elseif syn and syn.write_clipboard then
                syn.write_clipboard(LinkToCopy)
                return true
            elseif Clipboard and Clipboard.set then
                Clipboard.set(LinkToCopy)
                return true
            end
            return false
        end)
        if success then
            Status.Text = "Link copied to clipboard!"
            Status.TextColor3 = SelectedTheme.Accent
        else
            Status.Text = "Check Console (F9)"
            Status.TextColor3 = SelectedTheme.Error
        end
        task.wait(2)
        Status.Text = "Protected System"
        Status.TextColor3 = SelectedTheme.TextDark
    end)

    CheckBtn.MouseButton1Click:Connect(function()
        if Input.Text == Key then 
            TS:Create(KeyContainer, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.In), {Size = UDim2.fromOffset(0, 0)}):Play()
            TS:Create(Shadow1, TweenInfo.new(0.4), {ImageTransparency = 1}):Play()
            TS:Create(Shadow2, TweenInfo.new(0.4), {ImageTransparency = 1}):Play()
            task.wait(0.4)
            ScreenGui:Destroy()
            Validated = true
        else
            Status.Text = "Incorrect or Invalid Key!"
            Status.TextColor3 = SelectedTheme.Error
            InputStroke.Color = SelectedTheme.Error
            InputStroke.Transparency = 0
            
            local x = KeyContainer.Position.X.Scale
            local y = KeyContainer.Position.Y.Scale
            for i = 1, 5 do
                KeyContainer.Position = UDim2.fromScale(x + math.random(-1, 1) / 150, y)
                task.wait(0.04)
            end
            
            KeyContainer.Position = UDim2.fromScale(x, y)
            task.wait(1)
            
            InputStroke.Color = Color3.new(1, 1, 1)
            InputStroke.Transparency = 0.8
            Status.Text = "Protected System"
            Status.TextColor3 = SelectedTheme.TextDark
        end
    end)
    
    repeat
        task.wait()
    until Validated
end


function Library:CreateWindow(Settings)
    local Config = Settings or {}
    local Title = Config.Title or "UI"
    local SelectedTheme = GetTheme(Config.Theme)
    
    Library.GlobalCornerValue = Config.CornerRadius or SelectedTheme.CornerRadius or 10
    Library.GlobalScale = Config.UiScale or SelectedTheme.UiScale or 1.0

    Library.ConfigFolder = Config.ConfigFolder or "SolarisUI-Configs"
    if not isfolder(Library.ConfigFolder) then makefolder(Library.ConfigFolder) end
    if not isfolder(Library.ThemeFolder) then makefolder(Library.ThemeFolder) end

    Themes.Default.Main = SelectedTheme.Main
    Themes.Default.Second = SelectedTheme.Second
    Themes.Default.Accent = SelectedTheme.Accent
    Themes.Default.ElementAccent = SelectedTheme.ElementAccent
    Themes.Default.Text = SelectedTheme.Text
    Themes.Default.TextDark = SelectedTheme.TextDark
    Themes.Default.Error = SelectedTheme.Error
    Themes.Default.GradientStart = SelectedTheme.GradientStart
    Themes.Default.GradientEnd = SelectedTheme.GradientEnd
    Themes.Default.Gradient = SelectedTheme.Gradient
    Themes.Default.UiScale = SelectedTheme.UiScale or 1.0

    if SelectedTheme.Font then
        UpdateFonts(SelectedTheme.Font)
    end

    Library.ToggleKey = Config.ToggleKey or Enum.KeyCode.RightControl
    local WindowTrans = SelectedTheme.Transparency or Config.Transparency or 0.25 
    local ImageTrans = SelectedTheme.ImageTransparency or 0
    
    local WatermarkConfig = { Enabled = true, Title = true, User = true, FPS = true, Time = true, Ping = true }
    if type(Config.ShowWatermark) == "table" then
        for k, v in pairs(Config.ShowWatermark) do
            WatermarkConfig[k] = v
        end
    elseif Config.ShowWatermark == false then
        WatermarkConfig.Enabled = false
    end

    local CustomIconID = Config.CustomIcon

    local ScreenGui = Create("ScreenGui", {
        Name = "MainUI",
        ResetOnSpawn = false,
        DisplayOrder = 10000
    })
    
    if RS:IsStudio() then
        ScreenGui.Parent = LP:WaitForChild("PlayerGui")
    else
        pcall(function()
            ScreenGui.Parent = CG
        end)
        if not ScreenGui.Parent then
            ScreenGui.Parent = LP:WaitForChild("PlayerGui")
        end
    end

    function Library:GetThemes()
        local list = {}
        if isfolder(Library.ThemeFolder) then
            for _, file in pairs(listfiles(Library.ThemeFolder)) do
                if file:sub(-5) == ".json" then
                    table.insert(list, (file:match("([^/]+)%.json$") or file))
                end
            end
        end
        return list
    end
    
    function Library:GetConfigs()
        local list = {}
        if isfolder(Library.ConfigFolder) then
            for _, file in pairs(listfiles(Library.ConfigFolder)) do
                if file:sub(-5) == ".json" then
                    local success, decoded = pcall(function()
                        return HS:JSONDecode(readfile(file))
                    end)
                    if success and type(decoded) == "table" and decoded["Settings_Identifier"] == Title then
                        table.insert(list, file:match("([^/]+)%.json$") or file)
                    end
                end
            end
        end
        return list
    end

    function Library:SaveConfig(Name)
        local ConfigToSave = {}
        for flag, value in pairs(Library.Flags) do
            if string.sub(flag, 1, 9) ~= "Settings_" or Library.KeybindFlags[flag] or flag == "Settings_ToggleKey" then
                ConfigToSave[flag] = value
            end
        end
        ConfigToSave["Settings_Identifier"] = Title
        
        local success, json = pcall(function() return HS:JSONEncode(ConfigToSave) end)
        if success then
            local path = Library.ConfigFolder .. "/" .. Name .. ".json"
            writefile(path, json)
            Library:Notify({Title = "Config Saved", Content = "Successfully saved " .. Name, Duration = 3})
        else
            Library:Notify({Title = "Save Error", Content = "Failed to encode config data", Duration = 3})
        end
    end

    function Library:LoadConfig(Name)
        local path = Library.ConfigFolder .. "/" .. Name .. ".json"
        if isfile(path) then
            Library.IsLoadingConfig = true
            local success, err = pcall(function()
                local json = readfile(path)
                local data = HS:JSONDecode(json)
                for flag, value in pairs(data) do
                    if Library.Items[flag] then
                        pcall(function()
                            Library.Items[flag].Set(value)
                        end)
                    end
                end
            end)
            Library.IsLoadingConfig = false
            if success then
                Library:Notify({Title = "Config Loaded", Content = "Successfully loaded " .. Name, Duration = 3})
            else
                Library:Notify({Title = "Config Error", Content = "Corrupted JSON file", Duration = 3})
            end
        end
    end

    function Library:DeleteConfig(Name)
        local path = Library.ConfigFolder .. "/" .. Name .. ".json"
        if isfile(path) then
            delfile(path)
            Library:Notify({Title = "Config Deleted", Content = "Deleted " .. Name, Duration = 3})
        end
    end

    if WatermarkConfig.Enabled then
        local WatermarkContainer = Create("Frame", {
            Name = "WatermarkContainer",
            Parent = ScreenGui,
            Size = UDim2.new(0, 0, 0, 30),
            Position = UDim2.new(0.5, 0, -0.1, 0),
            AnchorPoint = Vector2.new(0.5, 0),
            BackgroundTransparency = 1,
            ZIndex = 5000
        })

        TS:Create(WatermarkContainer, TweenInfo.new(1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, 0, 0, 20)}):Play()

        local ItemsContainer = Create("Frame", {
            Parent = WatermarkContainer,
            Size = UDim2.fromScale(1, 1),
            BackgroundTransparency = 1,
            ZIndex = 5000
        })

        local WatermarkLayout = Create("UIListLayout", {
            Parent = ItemsContainer,
            FillDirection = Enum.FillDirection.Horizontal,
            HorizontalAlignment = Enum.HorizontalAlignment.Center,
            VerticalAlignment = Enum.VerticalAlignment.Center,
            Padding = UDim.new(0, 8),
            SortOrder = Enum.SortOrder.LayoutOrder
        })

        WatermarkLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            WatermarkContainer.Size = UDim2.new(0, WatermarkLayout.AbsoluteContentSize.X, 0, 30)
        end)

        local WmDragBtn = Create("TextButton", {
            Parent = WatermarkContainer,
            Size = UDim2.fromScale(1, 1),
            BackgroundTransparency = 1,
            Text = "",
            ZIndex = 5005
        })
        MakeDraggable(WmDragBtn, WatermarkContainer)

        local function CreateHudItem(order, iconId, textFunc)
            local Island = Create("Frame", {
                Parent = ItemsContainer,
                BackgroundColor3 = SelectedTheme.Main,
                BackgroundTransparency = SelectedTheme.HudTransparency or 0.5,
                Size = UDim2.new(0, 100, 1, 0),
                LayoutOrder = order,
                ZIndex = 5001,
                ThemeTag = "Main"
            })
            AddCorner(Island, 6)
            local stroke = AddStroke(Island, SelectedTheme)
            stroke.Transparency = 0.5
            table.insert(Library.WatermarkIslands, Island)

            local Inner = Create("Frame", {
                Parent = Island,
                Size = UDim2.fromScale(1, 1),
                BackgroundTransparency = 1
            })

            local Icon = Create("ImageLabel", {
                Parent = Inner,
                Size = UDim2.new(0, 16, 0, 16),
                Position = UDim2.new(0, 8, 0.5, -8),
                BackgroundTransparency = 1,
                ImageColor3 = SelectedTheme.Accent,
                ZIndex = 5002,
                ThemeTag = "Accent"
            })
            SetImageAsync(Icon, "Image", iconId)

            local Label = Create("TextLabel", {
                Parent = Inner,
                Size = UDim2.new(1, -32, 1, 0),
                Position = UDim2.new(0, 30, 0, 0),
                BackgroundTransparency = 1,
                Font = Library.GlobalFontBold,
                TextSize = 13,
                TextColor3 = SelectedTheme.Text,
                TextXAlignment = Enum.TextXAlignment.Left,
                Text = textFunc(),
                ZIndex = 5002,
                ThemeTag = "Text"
            })

            local function UpdateSize()
                local bounds = TxtS:GetTextSize(Label.Text, 13, Library.GlobalFontBold, Vector2.new(9999, 30))
                Island.Size = UDim2.new(0, bounds.X + 40, 1, 0)
            end

            Label:GetPropertyChangedSignal("Text"):Connect(UpdateSize)
            Label:GetPropertyChangedSignal("Font"):Connect(UpdateSize)
            UpdateSize()

            task.spawn(function()
                while Island.Parent do
                    local txt = textFunc()
                    if Label.Text ~= txt then
                        Label.Text = txt
                    end
                    task.wait(1)
                end
            end)
        end

        local Order = 1
        local titleIcon = (CustomIconID and CustomIconID ~= "") and CustomIconID or "rbxassetid://10884488899"
        
        if WatermarkConfig.Title then
            CreateHudItem(Order, titleIcon, function() return Title end)
            Order = Order + 1
        end
        if WatermarkConfig.User then
            CreateHudItem(Order, "rbxassetid://10884490076", function() return LP.DisplayName end)
            Order = Order + 1
        end
        if WatermarkConfig.FPS then
            CreateHudItem(Order, "rbxassetid://10884494953", function() return CurrentFPS .. " FPS" end)
            Order = Order + 1
        end
        if WatermarkConfig.Time then
            CreateHudItem(Order, "rbxassetid://10884491769", function() return os.date("%H:%M:%S") end)
            Order = Order + 1
        end
        if WatermarkConfig.Ping then
            CreateHudItem(Order, "rbxassetid://10884496263", function() 
                if DataPing then
                    return math.floor(DataPing:GetValue()) .. " ms"
                end
                return "0 ms"
            end)
            Order = Order + 1
        end
    end

    local NotifContainer = Create("Frame", {
        Parent = ScreenGui,
        Size = UDim2.new(0, 320, 1, -20),
        Position = UDim2.new(1, -340, 0, 50),
        BackgroundTransparency = 1,
        ZIndex = 10000
    })
    
    Create("UIListLayout", {
        Parent = NotifContainer,
        SortOrder = Enum.SortOrder.LayoutOrder,
        VerticalAlignment = Enum.VerticalAlignment.Top,
        Padding = UDim.new(0, 12)
    })

    function Library:Notify(Config)
        local Title = Config.Title or "Notification"
        local Content = Config.Content or "Message"
        local Duration = Config.Duration or 3
        
        if #Title > 30 then
            Title = string.sub(Title, 1, 27) .. "..."
        end
        
        local ImageUrl = Config.ImageID or "rbxassetid://3944703587"

        local currentCorner = Library.GlobalCornerValue
        local bottomOffset = -math.max(8, currentCorner - 2)
        local sidePadding = math.max(14, currentCorner + 4)

        local ContentSize = TxtS:GetTextSize(Content, 13, Library.GlobalFont, Vector2.new(230, 1000))
        local TotalHeight = math.max(70, 55 + ContentSize.Y) + math.max(0, currentCorner - 10)

        local NotifHolder = Create("Frame", {
            Parent = NotifContainer,
            Size = UDim2.new(1, 0, 0, 0),
            BackgroundTransparency = 1,
            ClipsDescendants = false 
        })
        
        local Frame = Create("Frame", {
            Parent = NotifHolder,
            Size = UDim2.fromScale(1, 1),
            BackgroundColor3 = SelectedTheme.Main,
            BackgroundTransparency = 1,
            ZIndex = 10001,
            ClipsDescendants = true,
            ThemeTag = "Main"
        })
        AddCorner(Frame, 10)
        
        local Stroke = AddStroke(Frame, SelectedTheme)
        Stroke.Transparency = 1 

        local Shadow = CreateDropShadow(NotifHolder, 45, 0)
        Shadow.ImageTransparency = 1
        
        local Icon = Create("ImageLabel", {
            Parent = Frame,
            Size = UDim2.new(0, 0, 0, 0),
            Position = UDim2.new(0, 31, 0, 31),
            BackgroundTransparency = 1,
            ImageColor3 = Color3.fromRGB(255, 255, 255),
            ZIndex = 10002,
            ImageTransparency = 1,
            Rotation = -15
        })
        SetImageAsync(Icon, "Image", ImageUrl)
        
        local TitleLabel = Create("TextLabel", {
            Parent = Frame,
            Size = UDim2.new(1, -64, 0, 20),
            Position = UDim2.new(0, 60, 0, 12),
            BackgroundTransparency = 1,
            Text = Title,
            Font = Library.GlobalFontBold,
            TextColor3 = SelectedTheme.Text,
            TextSize = 15,
            TextXAlignment = Enum.TextXAlignment.Left,
            ZIndex = 10002,
            TextTransparency = 1,
            ThemeTag = "Text"
        })
        
        local ContentLabel = Create("TextLabel", {
            Parent = Frame,
            Size = UDim2.new(1, -64, 1, -(34 + math.max(8, currentCorner - 2))),
            Position = UDim2.new(0, 60, 0, 34),
            BackgroundTransparency = 1,
            Text = Content,
            Font = Library.GlobalFont,
            TextColor3 = SelectedTheme.TextDark,
            TextSize = 13,
            TextXAlignment = Enum.TextXAlignment.Left,
            TextYAlignment = Enum.TextYAlignment.Top,
            ZIndex = 10002,
            TextWrapped = true,
            TextTransparency = 1,
            ThemeTag = "TextDark"
        })

        local BarBg = Create("Frame", {
            Parent = Frame,
            Size = UDim2.new(1, -sidePadding * 2, 0, 3),
            Position = UDim2.new(0, sidePadding, 1, bottomOffset),
            BackgroundColor3 = SelectedTheme.Second,
            BorderSizePixel = 0,
            ZIndex = 10002,
            BackgroundTransparency = 1,
            ThemeTag = "Second"
        })
        AddCorner(BarBg, 3)
        
        local Bar = Create("Frame", {
            Parent = BarBg,
            Size = UDim2.new(1, 0, 1, 0),
            BackgroundColor3 = SelectedTheme.ElementAccent,
            BorderSizePixel = 0,
            ZIndex = 10003,
            BackgroundTransparency = 1,
            ThemeTag = "ElementAccent"
        })
        AddCorner(Bar, 3)
        
        local InInfo = TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
        local LinearInfo = TweenInfo.new(0.6, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
        
        TS:Create(NotifHolder, InInfo, {Size = UDim2.new(1, 0, 0, TotalHeight)}):Play()
        TS:Create(Frame, LinearInfo, {BackgroundTransparency = SelectedTheme.HudTransparency or 0.5}):Play()
        TS:Create(Stroke, LinearInfo, {Transparency = 0.4}):Play()
        TS:Create(Shadow, LinearInfo, {ImageTransparency = 0.35}):Play()
        TS:Create(Icon, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 38, 0, 38), Position = UDim2.new(0, 12, 0, 12), ImageTransparency = 0, Rotation = 0}):Play()
        TS:Create(TitleLabel, LinearInfo, {TextTransparency = 0}):Play()
        TS:Create(ContentLabel, LinearInfo, {TextTransparency = 0}):Play()
        TS:Create(BarBg, LinearInfo, {BackgroundTransparency = 0.5}):Play()
        TS:Create(Bar, LinearInfo, {BackgroundTransparency = 0}):Play()
        
        local TimerTween = TS:Create(Bar, TweenInfo.new(Duration, Enum.EasingStyle.Linear), {Size = UDim2.new(0, 0, 1, 0)})
        TimerTween:Play()
        
        TimerTween.Completed:Connect(function()
            local OutInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In)
            TS:Create(NotifHolder, OutInfo, {Size = UDim2.new(1, 0, 0, 0)}):Play()
            TS:Create(Frame, OutInfo, {BackgroundTransparency = 1}):Play()
            TS:Create(Stroke, OutInfo, {Transparency = 1}):Play()
            TS:Create(Shadow, OutInfo, {ImageTransparency = 1}):Play()
            TS:Create(Icon, OutInfo, {ImageTransparency = 1, Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0, 31, 0, 31)}):Play()
            TS:Create(TitleLabel, OutInfo, {TextTransparency = 1}):Play()
            TS:Create(ContentLabel, OutInfo, {TextTransparency = 1}):Play()
            TS:Create(BarBg, OutInfo, {BackgroundTransparency = 1}):Play()
            TS:Create(Bar, OutInfo, {BackgroundTransparency = 1}):Play()
            task.delay(0.5, function()
                NotifHolder:Destroy()
            end)
        end)
    end

    local VP = workspace.CurrentCamera.ViewportSize
    local IsMobile = UIS.TouchEnabled and (VP.X < 850 or VP.Y < 600)
    local WindowSizeX = IsMobile and math.clamp(VP.X - 40, 300, 480) or 780
    local WindowSizeY = IsMobile and math.clamp(VP.Y - 40, 200, 320) or 540
    local WindowSize = UDim2.fromOffset(WindowSizeX, WindowSizeY)

    local WindowContainer = Create("Frame", {
        Name = "WindowContainer",
        Parent = ScreenGui,
        Size = UDim2.fromOffset(0, 0),
        Position = UDim2.fromScale(0.5, 0.5),
        AnchorPoint = Vector2.new(0.5, 0.5),
        BackgroundTransparency = 1,
        ZIndex = 1
    })
    
    local Main = Create("Frame", {
        Name = "Main",
        Parent = WindowContainer,
        Size = UDim2.fromScale(1, 1),
        BackgroundColor3 = SelectedTheme.Main,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ClipsDescendants = true,
        ZIndex = 2
    })
    
    local Shadow1 = CreateDropShadow(WindowContainer, 120, 0.35)
    local Shadow2 = CreateDropShadow(WindowContainer, 35, 0.4) 
    Shadow1.ImageTransparency = 1
    Shadow2.ImageTransparency = 1

    local MainBgImage = Create("ImageLabel", {
        Parent = Main,
        Size = UDim2.fromScale(1, 1),
        BackgroundTransparency = 1,
        ImageTransparency = ImageTrans,
        ScaleType = Enum.ScaleType.Crop,
        ZIndex = 1
    })
    SetImageAsync(MainBgImage, "Image", SelectedTheme.Background)
    AddCorner(MainBgImage, 12)
    
    local MainBgColor = Create("Frame", {
        Parent = Main,
        Size = UDim2.fromScale(1, 1),
        BackgroundColor3 = SelectedTheme.Main,
        BackgroundTransparency = WindowTrans,
        ZIndex = 2,
        ThemeTag = "Main"
    })
    AddCorner(MainBgColor, 12)

    AddCorner(Main, 12)
    AddStroke(Main, SelectedTheme)
    
    TS:Create(WindowContainer, TweenInfo.new(0.7, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = WindowSize}):Play()
    TS:Create(Shadow1, TweenInfo.new(0.7), {ImageTransparency = 0.35}):Play()
    TS:Create(Shadow2, TweenInfo.new(0.7), {ImageTransparency = 0.4}):Play()

    local ResizeBtn = Create("TextButton", {
        Parent = Main,
        Size = UDim2.fromOffset(25, 25),
        Position = UDim2.new(1, 0, 1, 0),
        AnchorPoint = Vector2.new(1, 1),
        BackgroundTransparency = 1,
        Text = "◢",
        Font = Library.GlobalFont,
        TextColor3 = SelectedTheme.TextDark,
        TextSize = 10,
        ZIndex = 2000,
        ThemeTag = "TextDark"
    })
    MakeResizable(ResizeBtn, WindowContainer)

    local TopBar = Create("Frame", {
        Parent = Main,
        Size = UDim2.new(1, 0, 0, 45),
        BackgroundTransparency = 1,
        ZIndex = 5
    })
    MakeDraggable(TopBar, WindowContainer)
    
    Create("Frame", {
        Parent = TopBar,
        Size = UDim2.new(1, 0, 0, 1),
        Position = UDim2.new(0, 0, 1, 0),
        BackgroundColor3 = SelectedTheme.TextDark,
        BackgroundTransparency = 0.8,
        BorderSizePixel = 0,
        ZIndex = 6,
        ThemeTag = "TextDark"
    })
    
    local TitleOffsetX = 18
    if CustomIconID and CustomIconID ~= "" then
        TitleOffsetX = 48
        local TopIcon = Create("ImageLabel", {
            Parent = TopBar,
            Size = UDim2.new(0, 26, 0, 26),
            Position = UDim2.new(0, 16, 0.5, -13),
            BackgroundTransparency = 1,
            ZIndex = 6
        })
        SetImageAsync(TopIcon, "Image", CustomIconID)
    end

    Create("TextLabel", {
        Parent = TopBar,
        Size = UDim2.new(1, -40, 1, 0),
        Position = UDim2.new(0, TitleOffsetX, 0, 0),
        BackgroundTransparency = 1,
        Text = Title,
        Font = Library.GlobalFontBold,
        TextColor3 = SelectedTheme.Text,
        TextSize = 16,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 6,
        ThemeTag = "Text"
    })
    
    local Close = Create("TextButton", {
        Parent = TopBar,
        Size = UDim2.new(0, 45, 1, 0),
        Position = UDim2.new(1, -45, 0, 0),
        BackgroundTransparency = 1,
        Text = "",
        ZIndex = 6
    })
    
    local Cross1 = Create("Frame", {
        Parent = Close,
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        Size = UDim2.new(0, 12, 0, 2),
        BackgroundColor3 = SelectedTheme.Text,
        Rotation = 45,
        ZIndex = 6,
        ThemeTag = "Text"
    })
    AddCorner(Cross1, 2)
    
    local Cross2 = Create("Frame", {
        Parent = Close,
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        Size = UDim2.new(0, 12, 0, 2),
        BackgroundColor3 = SelectedTheme.Text,
        Rotation = -45,
        ZIndex = 6,
        ThemeTag = "Text"
    })
    AddCorner(Cross2, 2)
    
    Close.MouseEnter:Connect(function() 
        TS:Create(Cross1, TweenInfo.new(0.2), {BackgroundColor3 = SelectedTheme.Error}):Play() 
        TS:Create(Cross2, TweenInfo.new(0.2), {BackgroundColor3 = SelectedTheme.Error}):Play() 
    end)
    Close.MouseLeave:Connect(function() 
        TS:Create(Cross1, TweenInfo.new(0.2), {BackgroundColor3 = SelectedTheme.Text}):Play() 
        TS:Create(Cross2, TweenInfo.new(0.2), {BackgroundColor3 = SelectedTheme.Text}):Play() 
    end)
    Close.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)

    Library.ScaleWrapper = Create("Frame", {
        Name = "ScaleWrapper",
        Parent = Main,
        Size = UDim2.new(1 / Library.GlobalScale, 0, 1 / Library.GlobalScale, -45 / Library.GlobalScale),
        Position = UDim2.new(0, 0, 0, 45),
        BackgroundTransparency = 1,
        ZIndex = 5
    })
    
    Library.ScaleObj = Create("UIScale", {
        Parent = Library.ScaleWrapper,
        Scale = Library.GlobalScale
    })

    local TabContainer = Create("ScrollingFrame", {
        Parent = Library.ScaleWrapper,
        Size = UDim2.new(0.22, 0, 1, -78),
        Position = UDim2.new(0.02, 0, 0, 15),
        BackgroundColor3 = SelectedTheme.Second,
        BackgroundTransparency = 0.5,
        ScrollBarThickness = 0,
        BorderSizePixel = 0,
        ZIndex = 5,
        ThemeTag = "Second",
        CanvasSize = UDim2.new(0, 0, 0, 0),
        AutomaticCanvasSize = Enum.AutomaticSize.Y
    })
    AddCorner(TabContainer, 8)

    local TabContainerStroke = AddStroke(TabContainer, SelectedTheme)
    TabContainerStroke.Transparency = 0.8
    Create("UIListLayout", {
        Parent = TabContainer,
        SortOrder = Enum.SortOrder.LayoutOrder,
        Padding = UDim.new(0, 6)
    })
    Create("UIPadding", {
        Parent = TabContainer,
        PaddingTop = UDim.new(0, 8),
        PaddingBottom = UDim.new(0, 8),
        PaddingLeft = UDim.new(0, 6),
        PaddingRight = UDim.new(0, 6)
    })

    local ProfileFrame = Create("Frame", {
        Parent = Library.ScaleWrapper,
        Size = UDim2.new(0.22, 0, 0, 40),
        Position = UDim2.new(0.02, 0, 1, -55),
        BackgroundColor3 = SelectedTheme.Second,
        BackgroundTransparency = 0.5,
        BorderSizePixel = 0,
        ZIndex = 5,
        ThemeTag = "Second"
    })
    AddCorner(ProfileFrame, 8)
    AddStroke(ProfileFrame, SelectedTheme).Transparency = 0.8

    local AvatarImg = "rbxasset://textures/ui/GuiImagePlaceholder.png"
    pcall(function()
        AvatarImg = Plrs:GetUserThumbnailAsync(LP.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size48x48)
    end)
    
    local Avatar = Create("ImageLabel", {
        Parent = ProfileFrame,
        Size = UDim2.new(0, 28, 0, 28),
        Position = UDim2.new(0, 6, 0.5, -14),
        BackgroundTransparency = 1,
        Image = AvatarImg,
        ZIndex = 6
    })
    AddCorner(Avatar, 100)
    
    Create("TextLabel", {
        Parent = ProfileFrame,
        Size = UDim2.new(1, -45, 0, 16),
        Position = UDim2.new(0, 40, 0, 5),
        BackgroundTransparency = 1,
        Text = LP.DisplayName,
        Font = Library.GlobalFontBold,
        TextColor3 = SelectedTheme.Text,
        TextSize = 13,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 6,
        TextTruncate = Enum.TextTruncate.AtEnd,
        ThemeTag = "Text"
    })
    Create("TextLabel", {
        Parent = ProfileFrame,
        Size = UDim2.new(1, -45, 0, 14),
        Position = UDim2.new(0, 40, 0, 20),
        BackgroundTransparency = 1,
        Text = "@" .. LP.Name,
        Font = Library.GlobalFont,
        TextColor3 = SelectedTheme.TextDark,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 6,
        TextTruncate = Enum.TextTruncate.AtEnd,
        ThemeTag = "TextDark"
    })

    local PageContainer = Create("Frame", {
        Parent = Library.ScaleWrapper,
        Size = UDim2.new(0.72, 0, 1, -30),
        Position = UDim2.new(0.26, 0, 0, 15),
        BackgroundTransparency = 1,
        ZIndex = 5,
        ClipsDescendants = true
    })

    local IsOpen, LastSize = true, WindowSize
    
    local function ToggleUI()
        IsOpen = not IsOpen 
        if IsOpen then 
            WindowContainer.Visible = true 
            TS:Create(WindowContainer, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = LastSize}):Play() 
            TS:Create(Shadow1, TweenInfo.new(0.6), {ImageTransparency = 0.35}):Play()
            TS:Create(Shadow2, TweenInfo.new(0.6), {ImageTransparency = 0.4}):Play()
        else 
            LastSize = WindowContainer.Size 
            local close = TS:Create(WindowContainer, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {Size = UDim2.new(0, 0, 0, 0)})
            close:Play() 
            TS:Create(Shadow1, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {ImageTransparency = 1}):Play()
            TS:Create(Shadow2, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {ImageTransparency = 1}):Play()
            close.Completed:Connect(function()
                if not IsOpen then
                    WindowContainer.Visible = false
                end
            end) 
        end
    end

    if UIS.TouchEnabled then
        local MobileBtn = Create("ImageButton", {
            Name = "MobileToggle",
            Parent = ScreenGui,
            Size = UDim2.fromOffset(50, 50),
            Position = UDim2.new(0.9, 0, 0.5, 0),
            BackgroundColor3 = SelectedTheme.Main,
            BackgroundTransparency = 0.1,
            BorderSizePixel = 0,
            Image = "rbxassetid://10884488899",
            ImageColor3 = SelectedTheme.Accent,
            ThemeTag = "Main"
        })
        AddCorner(MobileBtn, 14)
        AddStroke(MobileBtn, SelectedTheme)
        MakeDraggable(MobileBtn, MobileBtn)
        CreateDropShadow(MobileBtn, 35, 0.3)
        table.insert(Library.ThemeObjects.Accent, MobileBtn)
        
        MobileBtn.MouseButton1Click:Connect(ToggleUI)
    end

    UIS.InputBegan:Connect(function(input, gpe)
        if gpe or UIS:GetFocusedTextBox() then
            return
        end
        
        local currentToggle = Library.ToggleKey
        if Library.Flags["Settings_ToggleKey"] then
            local s, k = pcall(function() return Enum.KeyCode[Library.Flags["Settings_ToggleKey"]] end)
            if s and k then
                currentToggle = k
            end
        end

        if input.KeyCode == currentToggle then 
            ToggleUI()
        end
    end)
    
    local TabCount = 0
    local ActiveTab = nil
    local Funcs = {}

    function Funcs:CreateTab(TabName, Two_Column, ImageID)
        TabCount = TabCount + 1
        local MyIndex = TabCount
        local Tab = {}
        
        local HasIcon = (ImageID ~= nil and ImageID ~= "")
        
        local isSettings = (TabName == "UI Settings")
        local TabBtn = Create("TextButton", {
            Name = "TabBtn",
            Parent = TabContainer,
            Size = UDim2.new(1, 0, 0, 38),
            BackgroundColor3 = SelectedTheme.Main,
            BackgroundTransparency = 1,
            Text = "",
            AutoButtonColor = false,
            ZIndex = 6,
            LayoutOrder = isSettings and 99999 or MyIndex,
            ThemeTag = "Main"
        })
        AddCorner(TabBtn, 6)

        local TabLabel
        local ActiveLine
        local TabIcon

        if HasIcon then
            TabLabel = Create("TextLabel", {
                Name = "TabLabel",
                Parent = TabBtn,
                Size = UDim2.new(1, -44, 1, 0),
                Position = UDim2.new(0, 38, 0, 0),
                BackgroundTransparency = 1,
                Text = TabName,
                Font = Library.GlobalFontBold,
                TextColor3 = SelectedTheme.TextDark,
                TextSize = 14,
                TextXAlignment = Enum.TextXAlignment.Left,
                ZIndex = 7
            })
            
            TabIcon = Create("ImageLabel", {
                Name = "TabIcon",
                Parent = TabBtn,
                Size = UDim2.new(0, 22, 0, 22),
                Position = UDim2.new(0, 10, 0.5, -11),
                BackgroundTransparency = 1,
                ImageColor3 = SelectedTheme.TextDark,
                ZIndex = 7
            })
            SetImageAsync(TabIcon, "Image", ImageID)
            table.insert(Library.ThemeObjects.TabLabels, {Label = TabLabel, Btn = TabBtn, Icon = TabIcon})
        else
            TabLabel = Create("TextLabel", {
                Name = "TabLabel",
                Parent = TabBtn,
                Size = UDim2.new(1, -25, 1, 0),
                Position = UDim2.new(0, 20, 0, 0),
                BackgroundTransparency = 1,
                Text = TabName,
                Font = Library.GlobalFontBold,
                TextColor3 = SelectedTheme.TextDark,
                TextSize = 14,
                TextXAlignment = Enum.TextXAlignment.Left,
                ZIndex = 7
            })
            
            ActiveLine = Create("Frame", {
                Name = "ActiveLine",
                Parent = TabBtn,
                Size = UDim2.new(0, 3, 0, 0),
                Position = UDim2.new(0, 6, 0.5, 0),
                AnchorPoint = Vector2.new(0, 0.5),
                BackgroundColor3 = SelectedTheme.ElementAccent,
                BorderSizePixel = 0,
                ZIndex = 7,
                ThemeTag = "ElementAccent"
            })
            AddCorner(ActiveLine, 4)
            table.insert(Library.ThemeObjects.TabLabels, {Label = TabLabel, Btn = TabBtn})
        end

        local hoverTweenTab, leaveTweenTab
        TabBtn.MouseEnter:Connect(function()
            if ActiveTab and ActiveTab.Btn ~= TabBtn then
                if leaveTweenTab then leaveTweenTab:Cancel() end
                hoverTweenTab = TS:Create(TabBtn, TweenInfo.new(0.2), {BackgroundTransparency = 0.8})
                hoverTweenTab:Play()
            end
        end)
        TabBtn.MouseLeave:Connect(function()
            if ActiveTab and ActiveTab.Btn ~= TabBtn then
                if hoverTweenTab then hoverTweenTab:Cancel() end
                leaveTweenTab = TS:Create(TabBtn, TweenInfo.new(0.2), {BackgroundTransparency = 1})
                leaveTweenTab:Play()
            end
        end)

        local Page = Create("ScrollingFrame", {
            Parent = PageContainer,
            Size = UDim2.new(1, 0, 1, 0),
            BackgroundTransparency = 1,
            ScrollBarThickness = 3,
            ScrollBarImageColor3 = SelectedTheme.TextDark,
            BorderSizePixel = 0,
            Visible = false,
            CanvasSize = UDim2.new(0, 0, 0, 0),
            ZIndex = 6
        })
        
        local LeftCol, RightCol
        if Two_Column then
            local ColumnsHolder = Create("Frame", {
                Parent = Page,
                Size = UDim2.fromScale(1, 0),
                AutomaticSize = Enum.AutomaticSize.Y,
                BackgroundTransparency = 1
            })
            
            Create("UIListLayout", {
                Parent = ColumnsHolder,
                FillDirection = Enum.FillDirection.Horizontal,
                SortOrder = Enum.SortOrder.LayoutOrder,
                Padding = UDim.new(0, 10)
            })
            
            Create("UIPadding", {
                Parent = ColumnsHolder,
                PaddingTop = UDim.new(0, 2),
                PaddingLeft = UDim.new(0, 5),
                PaddingRight = UDim.new(0, 10),
                PaddingBottom = UDim.new(0, 15)
            })
            
            LeftCol = Create("Frame", {
                Parent = ColumnsHolder,
                Size = UDim2.new(0.5, -5, 0, 0),
                AutomaticSize = Enum.AutomaticSize.Y,
                BackgroundTransparency = 1,
                LayoutOrder = 1
            })
            
            local LeftLayout = Create("UIListLayout", {
                Parent = LeftCol,
                SortOrder = Enum.SortOrder.LayoutOrder,
                Padding = UDim.new(0, 10)
            })
            
            RightCol = Create("Frame", {
                Parent = ColumnsHolder,
                Size = UDim2.new(0.5, -5, 0, 0),
                AutomaticSize = Enum.AutomaticSize.Y,
                BackgroundTransparency = 1,
                LayoutOrder = 2
            })
            
            local RightLayout = Create("UIListLayout", {
                Parent = RightCol,
                SortOrder = Enum.SortOrder.LayoutOrder,
                Padding = UDim.new(0, 10)
            })
            
            local function UpdateCanvasSize()
                local maxH = math.max(LeftLayout.AbsoluteContentSize.Y, RightLayout.AbsoluteContentSize.Y)
                Page.CanvasSize = UDim2.new(0, 0, 0, maxH + 30)
            end
            
            LeftLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(UpdateCanvasSize)
            RightLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(UpdateCanvasSize)
        else
            local Layout = Create("UIListLayout", {
                Parent = Page,
                Padding = UDim.new(0, 8),
                SortOrder = Enum.SortOrder.LayoutOrder
            }) 
            
            Create("UIPadding", {
                Parent = Page,
                PaddingTop = UDim.new(0, 2),
                PaddingLeft = UDim.new(0, 5),
                PaddingRight = UDim.new(0, 12),
                PaddingBottom = UDim.new(0, 15)
            })
            
            Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
                Page.CanvasSize = UDim2.new(0, 0, 0, Layout.AbsoluteContentSize.Y + 30)
            end)
        end

        local function Activate()
            if ActiveTab and ActiveTab.Btn == TabBtn then
                return
            end
            
            local OldTab = ActiveTab
            local Direction = (OldTab and MyIndex > OldTab.Index) and "Down" or "Up"
            ActiveTab = {Btn = TabBtn, Page = Page, Index = MyIndex}
            
            for _, v in pairs(TabContainer:GetChildren()) do 
                if v:IsA("TextButton") then 
                    TS:Create(v, TweenInfo.new(0.3), {BackgroundTransparency = 1}):Play()
                    local lbl = v:FindFirstChild("TabLabel")
                    local line = v:FindFirstChild("ActiveLine")
                    local icon = v:FindFirstChild("TabIcon")
                    
                    if lbl then
                        local isIconTab = icon ~= nil
                        TS:Create(lbl, TweenInfo.new(0.3), {TextColor3 = SelectedTheme.TextDark, Position = UDim2.new(0, isIconTab and 38 or 20, 0, 0)}):Play()
                    end
                    if line then
                        TS:Create(line, TweenInfo.new(0.3), {Size = UDim2.new(0, 3, 0, 0)}):Play()
                    end
                    if icon then
                        TS:Create(icon, TweenInfo.new(0.3), {ImageColor3 = SelectedTheme.TextDark}):Play()
                    end
                end 
            end

            TS:Create(TabBtn, TweenInfo.new(0.3), {BackgroundTransparency = 0.5}):Play()
            TS:Create(TabLabel, TweenInfo.new(0.3), {TextColor3 = SelectedTheme.Text, Position = UDim2.new(0, HasIcon and 42 or 24, 0, 0)}):Play()
            
            if ActiveLine then
                TS:Create(ActiveLine, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 3, 0, 18)}):Play()
            end
            if TabIcon then
                TS:Create(TabIcon, TweenInfo.new(0.3), {ImageColor3 = SelectedTheme.ElementAccent}):Play()
            end

            if OldTab then
                local OldPage = OldTab.Page
                local OutPos = (Direction == "Down") and UDim2.new(0, 0, -1, 0) or UDim2.new(0, 0, 1, 0)
                TS:Create(OldPage, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = OutPos}):Play()
                task.delay(0.4, function()
                    if ActiveTab.Page ~= OldPage then
                        OldPage.Visible = false
                    end
                end)
            end
            
            Page.Visible = true
            Page.Position = (Direction == "Down") and UDim2.new(0, 0, 1, 0) or (Direction == "Up" and UDim2.new(0, 0, -1, 0) or UDim2.new(0, 0, 0, 0))
            if Direction ~= "None" then
                TS:Create(Page, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(0, 0, 0, 0)}):Play()
            end
        end
        
        TabBtn.MouseButton1Click:Connect(Activate)
        
        if MyIndex == 1 then
            Activate()
        end

        local function GetElements(TargetParent)
            local Elements = {}
            local ElementOrder = 0

            local function DefaultHover(btn)
                local hoverTween, leaveTween
                btn.MouseEnter:Connect(function()
                    if leaveTween then leaveTween:Cancel() end
                    hoverTween = TS:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.3})
                    hoverTween:Play()
                end)
                btn.MouseLeave:Connect(function()
                    if hoverTween then hoverTween:Cancel() end
                    leaveTween = TS:Create(btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.5})
                    leaveTween:Play()
                end)
            end

            function Elements:CreateLabel(Cfg)
                ElementOrder = ElementOrder + 1
                local Text = Cfg.Text or "Label"
                
                local Frame = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = Enum.AutomaticSize.Y,
                    BackgroundTransparency = 1,
                    LayoutOrder = ElementOrder,
                    ZIndex = 7
                })
                
                local Label = Create("TextLabel", {
                    Parent = Frame,
                    Size = UDim2.new(1, -24, 0, 0),
                    Position = UDim2.new(0, 12, 0, 0),
                    AutomaticSize = Enum.AutomaticSize.Y,
                    BackgroundTransparency = 1,
                    Text = forceLetterWrap(Text),
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 13,
                    TextWrapped = true,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    ThemeTag = "Text"
                })
                
                return {
                    Set = function(newText)
                        Label.Text = forceLetterWrap(newText)
                    end
                }
            end

            function Elements:CreateImage(Cfg)
                ElementOrder = ElementOrder + 1
                local Flag = Cfg.Flag or Cfg.Name
                local ImageId = Cfg.ImageID or ""
                local Height = Cfg.Height or 150
                
                local Frame = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, Height),
                    BackgroundTransparency = 1,
                    LayoutOrder = ElementOrder,
                    ZIndex = 7
                })
                
                local Img = Create("ImageLabel", {
                    Parent = Frame,
                    Size = UDim2.fromScale(1, 1),
                    BackgroundTransparency = 1,
                    ScaleType = Enum.ScaleType.Crop,
                    ZIndex = 8
                })
                SetImageAsync(Img, "Image", ImageId)
                AddCorner(Img, 8)
                
                local function Update(newId)
                    SetImageAsync(Img, "Image", newId)
                end
                
                if Flag then
                    Library.Items[Flag] = {
                        Set = Update
                    }
                end
                
                return {
                    Set = Update
                }
            end

            function Elements:CreateSection(Text)
                ElementOrder = ElementOrder + 1
                local Lab = Create("TextLabel", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 20),
                    BackgroundTransparency = 1,
                    Text = Text,
                    Font = Library.GlobalFontBold,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 13,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    LayoutOrder = ElementOrder,
                    ZIndex = 8,
                    ThemeTag = "Text"
                })
                Create("UIPadding", { Parent = Lab, PaddingLeft = UDim.new(0, 6) })
            end

            function Elements:CreateButton(Cfg)
                ElementOrder = ElementOrder + 1
                local Btn = Create("TextButton", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    Text = "",
                    AutoButtonColor = false,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Btn, 8)
                AddStroke(Btn, SelectedTheme).Transparency = 0.8

                local Lbl = Create("TextLabel", {
                    Parent = Btn,
                    Size = UDim2.fromScale(1, 1),
                    BackgroundTransparency = 1,
                    Text = Cfg.Name or "Button",
                    Font = Library.GlobalFontBold,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Center,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                CreateRipple(Btn, SelectedTheme.Accent)
                DefaultHover(Btn)
                
                Btn.MouseButton1Click:Connect(function()
                    TS:Create(Btn, TweenInfo.new(0.1), {Size = UDim2.new(1, -4, 0, 30), Position = UDim2.new(0, 2, 0, 2)}):Play()
                    task.wait(0.1)
                    TS:Create(Btn, TweenInfo.new(0.1), {Size = UDim2.new(1, 0, 0, 34), Position = UDim2.new(0, 0, 0, 0)}):Play()
                    if Cfg.Callback then
                        Cfg.Callback()
                    end
                end)
            end

            function Elements:CreateToggle(Cfg)
                ElementOrder = ElementOrder + 1
                local State = false
                local Flag = Cfg.Flag or Cfg.Name
                local Btn = Create("TextButton", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    Text = "",
                    AutoButtonColor = false,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Btn, 8)
                AddStroke(Btn, SelectedTheme).Transparency = 0.8

                local Lbl = Create("TextLabel", {
                    Parent = Btn,
                    Size = UDim2.new(1, -60, 1, 0),
                    Position = UDim2.new(0, 12, 0, 0),
                    BackgroundTransparency = 1,
                    Text = Cfg.Name or "Toggle",
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                DefaultHover(Btn)
                
                local Box = Create("Frame", {
                    Parent = Btn,
                    Size = UDim2.new(0, 20, 0, 20),
                    Position = UDim2.new(1, -32, 0.5, -10),
                    BackgroundColor3 = Color3.new(0, 0, 0),
                    BackgroundTransparency = 1,
                    ZIndex = 8
                })
                AddCorner(Box, 4)
                
                local BoxStroke = Create("UIStroke", {
                    Parent = Box, 
                    Color = SelectedTheme.TextDark, 
                    Transparency = 0.8, 
                    Thickness = 1
                })
                
                local InnerSquare = Create("Frame", {
                    Parent = Box,
                    Size = UDim2.fromScale(0, 0),
                    Position = UDim2.fromScale(0.5, 0.5),
                    AnchorPoint = Vector2.new(0.5, 0.5),
                    BackgroundColor3 = SelectedTheme.TextDark,
                    BackgroundTransparency = 1,
                    ZIndex = 9
                })
                AddCorner(InnerSquare, 2)

                table.insert(Library.ThemeObjects.Toggles, {Box = Box, Stroke = BoxStroke, Square = InnerSquare, State = function() return State end})
                
                local function UpdateState(val)
                    State = val
                    Library.Flags[Flag] = val
                    
                    TS:Create(BoxStroke, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
                        Color = State and SelectedTheme.ElementAccent or SelectedTheme.TextDark,
                        Transparency = State and 0 or 0.8
                    }):Play()
                    
                    TS:Create(InnerSquare, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
                        BackgroundColor3 = State and SelectedTheme.ElementAccent or SelectedTheme.TextDark,
                        Size = State and UDim2.fromScale(0.6, 0.6) or UDim2.fromScale(0, 0),
                        BackgroundTransparency = State and 0 or 1
                    }):Play()
                    
                    if Cfg.Callback then
                        Cfg.Callback(State)
                    end
                end
                
                if Cfg.Default then
                    UpdateState(Cfg.Default)
                else
                    Library.Flags[Flag] = false
                end

                Btn.MouseButton1Click:Connect(function()
                    UpdateState(not State)
                end)
                
                Library.Items[Flag] = {
                    Set = function(val)
                        if val == nil then return end
                        if State == val then return end
                        UpdateState(val)
                    end
                }
            end

            function Elements:CreateSlider(Cfg)
                ElementOrder = ElementOrder + 1
                local Min = Cfg.Min or 0
                local Max = Cfg.Max or 100
                local Val = Cfg.Default or Cfg.Min
                local Flag = Cfg.Flag or Cfg.Name
                Library.Flags[Flag] = Val
                
                local Frame = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 42),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Frame, 8)
                AddStroke(Frame, SelectedTheme).Transparency = 0.8

                local Lbl = Create("TextLabel", {
                    Parent = Frame,
                    Size = UDim2.new(1, -70, 0, 24),
                    Position = UDim2.new(0, 12, 0, 2),
                    BackgroundTransparency = 1,
                    Text = Cfg.Name,
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                local ValBg = Create("ScrollingFrame", {
                    Parent = Frame,
                    Size = UDim2.new(0, 48, 0, 20),
                    Position = UDim2.new(1, -60, 0, 4),
                    BackgroundColor3 = Color3.new(0, 0, 0),
                    BackgroundTransparency = 0.85,
                    ZIndex = 8,
                    ClipsDescendants = true,
                    CanvasSize = UDim2.new(0, 0, 0, 0),
                    ScrollBarThickness = 0,
                    ScrollingDirection = Enum.ScrollingDirection.X,
                    AutomaticCanvasSize = Enum.AutomaticSize.X
                })
                AddCorner(ValBg, 4)
                Create("UIPadding", { Parent = ValBg, PaddingLeft = UDim.new(0, 4), PaddingRight = UDim.new(0, 4) })

                local ValLbl = Create("TextBox", {
                    Parent = ValBg,
                    Size = UDim2.new(1, 0, 1, 0),
                    AutomaticSize = Enum.AutomaticSize.X,
                    BackgroundTransparency = 1,
                    Text = formatSliderValue(Val),
                    Font = Library.GlobalFontBold,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 12,
                    TextXAlignment = Enum.TextXAlignment.Center,
                    ZIndex = 9,
                    ThemeTag = "Text",
                    TextWrapped = false,
                    ClearTextOnFocus = false
                })
                AttachHorizontalScroll(ValLbl, ValBg, Enum.TextXAlignment.Center)
                SetupAutoScroll(ValBg, ValLbl)

                local SlideBar = Create("TextButton", {
                    Parent = Frame,
                    Size = UDim2.new(1, -24, 0, 6),
                    Position = UDim2.new(0, 12, 0, 28),
                    BackgroundColor3 = Color3.fromRGB(40, 40, 45),
                    Text = "",
                    AutoButtonColor = false,
                    ZIndex = 8
                })
                AddCorner(SlideBar, 10)
                
                local Fill = Create("Frame", {
                    Parent = SlideBar,
                    Size = UDim2.new((Val - Min) / (Max - Min), 0, 1, 0),
                    BackgroundColor3 = SelectedTheme.ElementAccent,
                    BorderSizePixel = 0,
                    ZIndex = 9,
                    ThemeTag = "ElementAccent"
                })
                AddCorner(Fill, 10)
                
                local Thumb = Create("Frame", {
                    Parent = Fill,
                    Size = UDim2.new(0, 14, 0, 14),
                    Position = UDim2.new(1, -7, 0.5, -7),
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    ZIndex = 10
                })
                AddCorner(Thumb, 10)
                
                local function Update(val)
                    val = math.clamp(val, Min, Max)
                    val = math.round(val * 1000) / 1000
                    Val = val
                    Library.Flags[Flag] = val
                    ValLbl.Text = formatSliderValue(Val)
                    TS:Create(Fill, TweenInfo.new(0.08, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {Size = UDim2.new((Val - Min) / (Max - Min), 0, 1, 0)}):Play()
                    if Cfg.Callback then
                        Cfg.Callback(Val)
                    end
                end
                
                if Cfg.Default then
                    Update(Cfg.Default)
                elseif Cfg.Callback then
                    task.spawn(Cfg.Callback, Val)
                end
                
                local Dragging = false
                
                local function DragUpdate(input)
                    local p = math.clamp((input.Position.X - SlideBar.AbsolutePosition.X) / SlideBar.AbsoluteSize.X, 0, 1)
                    local val = Min + ((Max - Min) * p)
                    Update(val)
                end
                
                SlideBar.InputBegan:Connect(function(i)
                    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                        Dragging = true
                        DragUpdate(i)
                        TS:Create(Thumb, TweenInfo.new(0.2), {Size = UDim2.new(0, 18, 0, 18), Position = UDim2.new(1, -9, 0.5, -9)}):Play()
                    end
                end)
                
                UIS.InputEnded:Connect(function(i)
                    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                        Dragging = false
                        TS:Create(Thumb, TweenInfo.new(0.2), {Size = UDim2.new(0, 14, 0, 14), Position = UDim2.new(1, -7, 0.5, -7)}):Play()
                    end
                end)
                
                UIS.InputChanged:Connect(function(i)
                    if Dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
                        DragUpdate(i)
                    end
                end)
                
                ValLbl.FocusLost:Connect(function()
                    local number = tonumber(ValLbl.Text)
                    if number then
                        Update(number)
                    else
                        ValLbl.Text = formatSliderValue(Val)
                    end
                end)

                Library.Items[Flag] = {Set = Update}
            end

            function Elements:CreateInput(Cfg)
                ElementOrder = ElementOrder + 1
                local Flag = Cfg.Flag or Cfg.Name
                local Frame = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Frame, 8)
                AddStroke(Frame, SelectedTheme).Transparency = 0.8

                local Lbl = Create("TextLabel", {
                    Parent = Frame,
                    Size = UDim2.new(1, -130, 1, 0),
                    Position = UDim2.new(0, 12, 0, 0),
                    BackgroundTransparency = 1,
                    Text = Cfg.Name,
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                local BoxContainer = Create("ScrollingFrame", { 
                    Parent = Frame,
                    Size = UDim2.new(0, 120, 0, 26),
                    Position = UDim2.new(1, -128, 0.5, -13), 
                    BackgroundColor3 = Color3.new(0, 0, 0),
                    BackgroundTransparency = 0.85,
                    ZIndex = 8, 
                    ClipsDescendants = true,
                    CanvasSize = UDim2.new(0, 0, 0, 0),
                    ScrollBarThickness = 0, 
                    ScrollingDirection = Enum.ScrollingDirection.X,
                    AutomaticCanvasSize = Enum.AutomaticSize.X 
                })
                AddCorner(BoxContainer, 6)
                Create("UIPadding", { Parent = BoxContainer, PaddingLeft = UDim.new(0, 8), PaddingRight = UDim.new(0, 8) })

                local BoxStroke = Create("UIStroke", {
                    Parent = BoxContainer, 
                    Color = SelectedTheme.TextDark, 
                    Transparency = 0.8, 
                    Thickness = 1, 
                    ApplyStrokeMode = Enum.ApplyStrokeMode.Border
                })
                table.insert(Library.ThemeObjects.TextDark, BoxStroke)

                local Box = Create("TextBox", {
                    Parent = BoxContainer,
                    Size = UDim2.new(1, 0, 1, 0),
                    AutomaticSize = Enum.AutomaticSize.X,
                    BackgroundTransparency = 1,
                    Text = Cfg.Default or "",
                    PlaceholderText = Cfg.Placeholder or "Type...",
                    TextColor3 = SelectedTheme.Text,
                    Font = Library.GlobalFont,
                    TextSize = 13,
                    ZIndex = 9,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ThemeTag = "Text",
                    TextWrapped = false,
                    ClearTextOnFocus = false
                })
                AttachHorizontalScroll(Box, BoxContainer, Enum.TextXAlignment.Left)

                Box.Focused:Connect(function()
                    TS:Create(BoxStroke, TweenInfo.new(0.3), {Color = SelectedTheme.ElementAccent, Transparency = 0}):Play()
                end)
                
                Box.FocusLost:Connect(function()
                    TS:Create(BoxStroke, TweenInfo.new(0.3), {Color = SelectedTheme.TextDark, Transparency = 0.8}):Play()
                end)

                local function Update(val)
                    Box.Text = val
                    if Cfg.Callback then
                        Cfg.Callback(val)
                    end
                end
                
                if Cfg.Default then
                    Library.Flags[Flag] = Cfg.Default
                    if Cfg.Callback then
                        task.spawn(Cfg.Callback, Cfg.Default)
                    end
                end
                
                Box.FocusLost:Connect(function()
                    Update(Box.Text)
                end)
                
                Library.Items[Flag] = {Set = Update}
            end

            function Elements:CreateDropdown(Cfg)
                ElementOrder = ElementOrder + 1
                local Flag = Cfg.Flag or Cfg.Name
                local Expanded = false
                local Selected = Cfg.Default
                local Options = Cfg.Items or {}
                
                local Drop = Create("TextButton", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    Text = "",
                    AutoButtonColor = false,
                    ClipsDescendants = true,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Drop, 8)
                AddStroke(Drop, SelectedTheme).Transparency = 0.8

                DefaultHover(Drop)
                
                local Title = Create("TextLabel", {
                    Parent = Drop,
                    Size = UDim2.new(1, -40, 0, 34),
                    Position = UDim2.new(0, 12, 0, 0),
                    BackgroundTransparency = 1,
                    Text = (Cfg.Name or "Dropdown") .. (Selected and " - " .. tostring(Selected) or ""),
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                local Arrow = Create("ImageLabel", {
                    Parent = Drop,
                    Size = UDim2.new(0, 16, 0, 16),
                    Position = UDim2.new(1, -28, 0, 9),
                    BackgroundTransparency = 1,
                    Image = "rbxassetid://6031091004",
                    ImageColor3 = SelectedTheme.TextDark,
                    ZIndex = 8
                })
                table.insert(Library.ThemeObjects.TextDark, Arrow)

                local Container = Create("ScrollingFrame", {
                    Parent = Drop,
                    Size = UDim2.new(1, 0, 0, 0),
                    Position = UDim2.new(0, 0, 0, 34),
                    BackgroundTransparency = 1,
                    ZIndex = 8,
                    ScrollBarThickness = 2,
                    ScrollBarImageColor3 = SelectedTheme.TextDark,
                    BorderSizePixel = 0,
                    CanvasSize = UDim2.new(0, 0, 0, 0)
                })
                
                Create("UIPadding", {
                    Parent = Container,
                    PaddingTop = UDim.new(0, 5),
                    PaddingBottom = UDim.new(0, 5),
                    PaddingLeft = UDim.new(0, 10),
                    PaddingRight = UDim.new(0, 10)
                })
                
                local ListLayout = Create("UIListLayout", { Parent = Container, Padding = UDim.new(0, 4) })
                
                local function Update(val) 
                    Selected = val 
                    Title.Text = (Cfg.Name or "Dropdown") .. " - " .. tostring(Selected) 
                    if Cfg.Callback then
                        Cfg.Callback(val)
                    end
                    Library.Flags[Flag] = val
                end

                local function RefreshList(newOptions)
                    Options = newOptions or Options
                    for _, v in pairs(Container:GetChildren()) do
                        if v:IsA("TextButton") then
                            v:Destroy()
                        end
                    end
                    
                    local found = false
                    for _, v in pairs(Options) do
                        if v == Selected then
                            found = true
                            break
                        end
                    end
                    
                    if found then
                        Title.Text = (Cfg.Name or "Dropdown") .. " - " .. tostring(Selected)
                    else
                        Selected = nil
                        Title.Text = Cfg.Name or "Dropdown"
                    end

                    for _, item in pairs(Options) do
                        local Btn = Create("TextButton", {
                            Parent = Container,
                            Size = UDim2.new(1, 0, 0, 28),
                            Position = UDim2.new(0, 0, 0, 0),
                            BackgroundColor3 = SelectedTheme.Main,
                            BackgroundTransparency = 0.8,
                            Text = tostring(item),
                            Font = Library.GlobalFont,
                            TextColor3 = SelectedTheme.TextDark,
                            TextSize = 13,
                            ZIndex = 9,
                            TextXAlignment = Enum.TextXAlignment.Center,
                            AutoButtonColor = false,
                            ThemeTag = "Main"
                        })
                        AddCorner(Btn, 6)
                        table.insert(Library.ThemeObjects.TextDark, Btn)

                        local hoverTweenBtn, leaveTweenBtn
                        Btn.MouseEnter:Connect(function()
                            if leaveTweenBtn then leaveTweenBtn:Cancel() end
                            hoverTweenBtn = TS:Create(Btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.2, TextColor3 = SelectedTheme.Text})
                            hoverTweenBtn:Play()
                        end)
                        Btn.MouseLeave:Connect(function()
                            if hoverTweenBtn then hoverTweenBind:Cancel() end
                            leaveTweenBtn = TS:Create(Btn, TweenInfo.new(0.2), {BackgroundTransparency = 0.8, TextColor3 = SelectedTheme.TextDark})
                            leaveTweenBtn:Play()
                        end)
                        Btn.MouseButton1Click:Connect(function()
                            Update(item)
                            Expanded = false
                            TS:Create(Drop, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(1, 0, 0, 34)}):Play()
                            TS:Create(Arrow, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Rotation = 0}):Play()
                        end)
                    end
                    Container.CanvasSize = UDim2.new(0, 0, 0, #Options * 32 + 10)
                end
                
                RefreshList(Options)
                
                if Cfg.Default and Cfg.Callback then
                    task.spawn(Cfg.Callback, Selected)
                end

                Drop.MouseButton1Click:Connect(function()
                    if Cfg.UpdateList then
                        RefreshList(Cfg.UpdateList())
                    end
                    Expanded = not Expanded
                    local H = Expanded and math.min(#Options * 32 + 44, 180) or 34
                    TS:Create(Drop, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(1, 0, 0, H)}):Play()
                    TS:Create(Arrow, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Rotation = Expanded and 180 or 0}):Play()
                    TS:Create(Container, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(1, 0, 1, -34)}):Play()
                end)
                
                Library.Items[Flag] = {
                    Set = Update,
                    Refresh = function(self, list) RefreshList(list) end,
                    Get = function() return Selected end
                }
                
                return Library.Items[Flag]
            end

            function Elements:CreateColorPicker(Cfg)
                ElementOrder = ElementOrder + 1
                local Flag = Cfg.Flag or Cfg.Name
                local Color = Cfg.Default or Color3.fromRGB(255, 255, 255)
                Library.Flags[Flag] = {R = Color.R, G = Color.G, B = Color.B}
                local IsExpanded = false
                
                local Wrapper = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    ClipsDescendants = true,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Wrapper, 8)
                AddStroke(Wrapper, SelectedTheme).Transparency = 0.8

                local TriggerBtn = Create("TextButton", { Parent = Wrapper, Size = UDim2.new(1, 0, 0, 34), BackgroundTransparency = 1, Text = "", ZIndex = 8 })
                
                local Title = Create("TextLabel", { 
                    Parent = TriggerBtn, 
                    Size = UDim2.new(1, -50, 1, 0), 
                    Position = UDim2.new(0, 12, 0, 0), 
                    BackgroundTransparency = 1, 
                    Text = Cfg.Name or "Color Picker", 
                    Font = Library.GlobalFont, 
                    TextColor3 = SelectedTheme.Text, 
                    TextSize = 14, 
                    TextXAlignment = Enum.TextXAlignment.Left, 
                    ZIndex = 9, 
                    TextTruncate = Enum.TextTruncate.AtEnd, 
                    ThemeTag = "Text" 
                })
                
                local PreviewContainer = Create("Frame", {
                    Parent = TriggerBtn,
                    Size = UDim2.new(0, 30, 0, 20),
                    Position = UDim2.new(1, -42, 0.5, -10),
                    BackgroundColor3 = Color3.new(0, 0, 0),
                    BackgroundTransparency = 0.85,
                    ZIndex = 9
                })
                AddCorner(PreviewContainer, 6)
                Create("UIStroke", {Parent = PreviewContainer, Color = SelectedTheme.TextDark, Transparency = 0.8, Thickness = 1})

                local Preview = Create("Frame", { Parent = PreviewContainer, Size = UDim2.new(1, -4, 1, -4), Position = UDim2.new(0, 2, 0, 2), BackgroundColor3 = Color, ZIndex = 10 })
                AddCorner(Preview, 4)
                
                local Container = Create("Frame", { Parent = Wrapper, Size = UDim2.new(1, 0, 0, 160), Position = UDim2.new(0, 0, 0, 34), BackgroundTransparency = 1, ZIndex = 8 })
                
                local BoxContainer = Create("Frame", { Parent = Container, Size = UDim2.new(0, 140, 1, 0), Position = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1, ZIndex = 9 })
                
                Create("TextLabel", { 
                    Parent = BoxContainer, 
                    Size = UDim2.new(1, 0, 0, 20), 
                    Position = UDim2.new(0, 12, 0, 5), 
                    BackgroundTransparency = 1, 
                    Text = "RGB / Hex", 
                    TextColor3 = SelectedTheme.TextDark, 
                    Font = Library.GlobalFontBold, 
                    TextSize = 12, 
                    TextXAlignment = Enum.TextXAlignment.Left, 
                    ZIndex = 10, 
                    ThemeTag = "TextDark" 
                })
                
                local function CreateCBox(parent, size, pos, txt)
                    local box = Create("TextBox", {
                        Parent = parent,
                        Size = size,
                        Position = pos,
                        Text = txt,
                        BackgroundColor3 = Color3.new(0, 0, 0),
                        BackgroundTransparency = 0.85,
                        TextColor3 = SelectedTheme.Text,
                        Font = Library.GlobalFont,
                        TextSize = 12,
                        ZIndex = 10,
                        ThemeTag = "Text"
                    })
                    AddCorner(box, 4)
                    local boxStroke = Create("UIStroke", {Parent = box, Color = SelectedTheme.TextDark, Transparency = 0.8, Thickness = 1})
                    table.insert(Library.ThemeObjects.TextDark, boxStroke)
                    return box
                end

                local RInput = CreateCBox(BoxContainer, UDim2.new(0, 36, 0, 28), UDim2.new(0, 12, 0, 30), math.floor(Color.R * 255))
                local GInput = CreateCBox(BoxContainer, UDim2.new(0, 36, 0, 28), UDim2.new(0, 54, 0, 30), math.floor(Color.G * 255))
                local BInput = CreateCBox(BoxContainer, UDim2.new(0, 36, 0, 28), UDim2.new(0, 96, 0, 30), math.floor(Color.B * 255))
                local HexInput = CreateCBox(BoxContainer, UDim2.new(0, 120, 0, 28), UDim2.new(0, 12, 0, 66), "#" .. Color:ToHex())
                
                local PalContainer = Create("Frame", { Parent = Container, Size = UDim2.new(1, -150, 1, -20), Position = UDim2.new(0, 140, 0, 10), BackgroundTransparency = 1, ZIndex = 9 })
                local h, s, v = Color:ToHSV()
                
                local SVMap = Create("ImageButton", {
                    Parent = PalContainer,
                    Size = UDim2.new(1, -15, 0, 115),
                    Position = UDim2.new(0, 0, 0, 0),
                    BackgroundColor3 = Color3.fromHSV(h, 1, 1),
                    Image = "rbxassetid://4155801252",
                    ZIndex = 10,
                    AutoButtonColor = false
                })
                AddCorner(SVMap, 6)
                
                local SVTrigger = Create("TextButton", { Parent = SVMap, Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", ZIndex = 100 })
                
                local SVCursor = Create("Frame", {
                    Parent = SVMap,
                    Size = UDim2.new(0, 8, 0, 8),
                    Position = UDim2.fromScale(s, 1 - v),
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    ZIndex = 12,
                    AnchorPoint = Vector2.new(0.5, 0.5)
                })
                AddCorner(SVCursor, 100)
                Create("UIStroke", {Parent = SVCursor, Color = Color3.new(0, 0, 0), Thickness = 1})
                
                local HueBar = Create("ImageButton", {
                    Parent = PalContainer,
                    Size = UDim2.new(1, -15, 0, 12),
                    Position = UDim2.new(0, 0, 0, 125),
                    BackgroundColor3 = Color3.new(1, 1, 1),
                    ZIndex = 10,
                    AutoButtonColor = false
                })
                AddCorner(HueBar, 6)
                Create("UIGradient", { 
                    Parent = HueBar, 
                    Color = ColorSequence.new{
                        ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)), 
                        ColorSequenceKeypoint.new(0.167, Color3.fromRGB(255, 255, 0)), 
                        ColorSequenceKeypoint.new(0.333, Color3.fromRGB(0, 255, 0)), 
                        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 255, 255)), 
                        ColorSequenceKeypoint.new(0.667, Color3.fromRGB(0, 0, 255)), 
                        ColorSequenceKeypoint.new(0.833, Color3.fromRGB(255, 0, 255)), 
                        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))
                    } 
                })
                
                local HueTrigger = Create("TextButton", { Parent = HueBar, Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", ZIndex = 100 })
                
                local HueCursor = Create("Frame", { 
                    Parent = HueBar, 
                    Size = UDim2.new(0, 4, 1, 4), 
                    Position = UDim2.fromScale(h, 0.5), 
                    AnchorPoint = Vector2.new(0.5, 0.5), 
                    BackgroundColor3 = Color3.new(1, 1, 1), 
                    ZIndex = 11 
                })
                AddCorner(HueCursor, 2)
                Create("UIStroke", {Parent = HueCursor, Color = Color3.new(0, 0, 0), Thickness = 1})
                
                TriggerBtn.MouseButton1Click:Connect(function()
                    IsExpanded = not IsExpanded
                    TS:Create(Wrapper, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = IsExpanded and UDim2.new(1, 0, 0, 194) or UDim2.new(1, 0, 0, 34)}):Play()
                end)

                local function Update(col)
                    Color = col
                    h, s, v = Color:ToHSV()
                    Preview.BackgroundColor3 = Color
                    SVMap.BackgroundColor3 = Color3.fromHSV(h, 1, 1)
                    SVCursor.Position = UDim2.fromScale(s, 1 - v)
                    HueCursor.Position = UDim2.fromScale(h, 0.5)
                    RInput.Text = math.floor(Color.R * 255)
                    GInput.Text = math.floor(Color.G * 255)
                    BInput.Text = math.floor(Color.B * 255)
                    HexInput.Text = "#" .. Color:ToHex()
                    Library.Flags[Flag] = {R = Color.R, G = Color.G, B = Color.B}
                    if Cfg.Callback then
                        Cfg.Callback(Color)
                    end
                end
                
                if Cfg.Default and Cfg.Callback then
                    task.spawn(Cfg.Callback, Color)
                end
                
                local function VisualUpdate(newH, newS, newV)
                    h = newH or h
                    s = newS or s
                    v = newV or v
                    Update(Color3.fromHSV(h, s, v))
                end
                
                RInput.FocusLost:Connect(function()
                    local r = tonumber(RInput.Text)
                    if r then
                        local c = Color3.fromRGB(math.clamp(r, 0, 255), Color.G * 255, Color.B * 255)
                        VisualUpdate(c:ToHSV())
                    end
                end)
                
                GInput.FocusLost:Connect(function()
                    local g = tonumber(GInput.Text)
                    if g then
                        local c = Color3.fromRGB(Color.R * 255, math.clamp(g, 0, 255), Color.B * 255)
                        VisualUpdate(c:ToHSV())
                    end
                end)
                
                BInput.FocusLost:Connect(function()
                    local b = tonumber(BInput.Text)
                    if b then
                        local c = Color3.fromRGB(Color.R * 255, Color.G * 255, math.clamp(b, 0, 255))
                        VisualUpdate(c:ToHSV())
                    end
                end)
                
                HexInput.FocusLost:Connect(function()
                    local success, c = pcall(function() return Color3.fromHex(HexInput.Text) end)
                    if success then
                        VisualUpdate(c:ToHSV())
                    end
                end)

                local draggingSV = false
                local draggingHue = false
                local dragConnection
                
                local function StartDragging()
                    if not dragConnection then
                        dragConnection = RS.RenderStepped:Connect(function()
                            if draggingSV then
                                VisualUpdate(nil, math.clamp((Mouse.X - SVMap.AbsolutePosition.X) / SVMap.AbsoluteSize.X, 0, 1), 1 - math.clamp((Mouse.Y - SVMap.AbsolutePosition.Y) / SVMap.AbsoluteSize.Y, 0, 1))
                            elseif draggingHue then
                                VisualUpdate(math.clamp((Mouse.X - HueBar.AbsolutePosition.X) / HueBar.AbsoluteSize.X, 0, 1), nil, nil)
                            end
                        end)
                    end
                end

                local function StopDragging()
                    if dragConnection then
                        dragConnection:Disconnect()
                        dragConnection = nil
                    end
                end

                SVTrigger.InputBegan:Connect(function(i)
                    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                        draggingSV = true
                        StartDragging()
                    end
                end)
                
                HueTrigger.InputBegan:Connect(function(i)
                    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                        draggingHue = true
                        StartDragging()
                    end
                end)
                
                UIS.InputEnded:Connect(function(i)
                    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                        draggingSV = false
                        draggingHue = false
                        StopDragging()
                    end
                end)
                
                Library.Items[Flag] = {
                    Set = function(t)
                        if type(t) == "table" then
                            Update(Color3.new(t.R, t.G, t.B))
                        else
                            Update(t)
                        end
                    end
                }
            end

            function Elements:CreateKeybind(Cfg)
                ElementOrder = ElementOrder + 1
                local Flag = Cfg.Flag or Cfg.Name
                local CurrentKey = Cfg.Default or Enum.KeyCode.RightControl
                Library.Flags[Flag] = CurrentKey.Name
                Library.KeybindFlags[Flag] = true
                local Binding = false
                
                local Frame = Create("Frame", {
                    Parent = TargetParent,
                    Size = UDim2.new(1, 0, 0, 34),
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    ZIndex = 7,
                    LayoutOrder = ElementOrder,
                    ThemeTag = "Second"
                })
                AddCorner(Frame, 8)
                AddStroke(Frame, SelectedTheme).Transparency = 0.8

                local TitleOffset = 12
                local VP_Key = workspace.CurrentCamera.ViewportSize
                local IsMobile = UIS.TouchEnabled and (VP_Key.X < 850 or VP_Key.Y < 600)
                local MobileCheckboxState = false
                local MobileFlag = Flag .. "_Mobile"
                Library.Flags[MobileFlag] = false
                
                local OnScreenBtn

                if IsMobile then
                    TitleOffset = 40
                    local MBox = Create("TextButton", {
                        Parent = Frame,
                        Size = UDim2.new(0, 20, 0, 20),
                        Position = UDim2.new(0, 12, 0.5, -10),
                        BackgroundColor3 = Color3.new(0, 0, 0),
                        BackgroundTransparency = 1,
                        Text = "",
                        ZIndex = 8
                    })
                    AddCorner(MBox, 4)
                    local MStroke = Create("UIStroke", {Parent = MBox, Color = SelectedTheme.TextDark, Transparency = 0.8, Thickness = 1})

                    local MSquare = Create("Frame", {
                        Parent = MBox,
                        Size = UDim2.fromScale(0, 0),
                        Position = UDim2.fromScale(0.5, 0.5),
                        AnchorPoint = Vector2.new(0.5, 0.5),
                        BackgroundColor3 = SelectedTheme.ElementAccent,
                        BackgroundTransparency = 1,
                        ZIndex = 9,
                        ThemeTag = "ElementAccent"
                    })
                    AddCorner(MSquare, 2)

                    local function UpdateMobileBtnState(val)
                        MobileCheckboxState = val
                        Library.Flags[MobileFlag] = val

                        TS:Create(MStroke, TweenInfo.new(0.3), {
                            Color = val and SelectedTheme.ElementAccent or SelectedTheme.TextDark,
                            Transparency = val and 0 or 0.8
                        }):Play()

                        TS:Create(MSquare, TweenInfo.new(0.3), {
                            Size = val and UDim2.fromScale(0.6, 0.6) or UDim2.fromScale(0, 0),
                            BackgroundTransparency = val and 0 or 1
                        }):Play()

                        if val then
                            if not OnScreenBtn then
                                OnScreenBtn = Create("TextButton", {
                                    Parent = ScreenGui,
                                    Size = UDim2.fromOffset(50, 50),
                                    Position = UDim2.new(0.8, 0, 0.8, 0),
                                    BackgroundColor3 = SelectedTheme.Main,
                                    BackgroundTransparency = SelectedTheme.HudTransparency or 0.5,
                                    Text = KeyMap[CurrentKey.Name] or CurrentKey.Name,
                                    Font = Library.GlobalFontBold,
                                    TextColor3 = SelectedTheme.Text,
                                    TextSize = 16,
                                    ZIndex = 10000,
                                    ThemeTag = "Main"
                                })
                                AddCorner(OnScreenBtn, 12)
                                AddStroke(OnScreenBtn, SelectedTheme).Transparency = 0.5
                                table.insert(Library.ThemeObjects.Text, OnScreenBtn)

                                local osDragging = false
                                local osInput, osPos, osFramePos
                                local dragDist = 0

                                OnScreenBtn.InputBegan:Connect(function(input)
                                    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                                        osDragging = true
                                        osPos = input.Position
                                        osFramePos = OnScreenBtn.Position
                                        dragDist = 0
                                        input.Changed:Connect(function()
                                            if input.UserInputState == Enum.UserInputState.End then
                                                osDragging = false
                                            end
                                        end)
                                    end
                                end)

                                OnScreenBtn.InputChanged:Connect(function(input)
                                    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
                                        osInput = input
                                    end
                                end)

                                UIS.InputChanged:Connect(function(input)
                                    if input == osInput and osDragging then
                                        local delta = input.Position - osPos
                                        dragDist = dragDist + delta.Magnitude
                                        OnScreenBtn.Position = UDim2.new(osFramePos.X.Scale, osFramePos.X.Offset + delta.X, osFramePos.Y.Scale, osFramePos.Y.Offset + delta.Y)
                                    end
                                end)

                                OnScreenBtn.MouseButton1Click:Connect(function()
                                    if dragDist < 10 then
                                        if Cfg.Callback then Cfg.Callback(CurrentKey) end
                                    end
                                end)
                            end
                            OnScreenBtn.Visible = true
                            OnScreenBtn.Text = KeyMap[CurrentKey.Name] or CurrentKey.Name
                        else
                            if OnScreenBtn then
                                OnScreenBtn.Visible = false
                            end
                        end
                    end

                    MBox.MouseButton1Click:Connect(function()
                        UpdateMobileBtnState(not MobileCheckboxState)
                    end)

                    Library.Items[MobileFlag] = {
                        Set = function(val)
                            if MobileCheckboxState ~= val then
                                UpdateMobileBtnState(val)
                            end
                        end
                    }
                end

                local Lbl = Create("TextLabel", {
                    Parent = Frame,
                    Size = UDim2.new(1, -110, 1, 0),
                    Position = UDim2.new(0, TitleOffset, 0, 0),
                    BackgroundTransparency = 1,
                    Text = Cfg.Name,
                    Font = Library.GlobalFont,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 8,
                    TextTruncate = Enum.TextTruncate.AtEnd,
                    ThemeTag = "Text"
                })
                
                local BindBtn = Create("TextButton", {
                    Parent = Frame,
                    Size = UDim2.new(0, 70, 0, 24),
                    Position = UDim2.new(1, -82, 0.5, -12),
                    BackgroundColor3 = Color3.new(0, 0, 0),
                    BackgroundTransparency = 0.85,
                    Text = KeyMap[CurrentKey.Name] or CurrentKey.Name,
                    Font = Library.GlobalFontBold,
                    TextColor3 = SelectedTheme.TextDark,
                    TextSize = 12,
                    ZIndex = 9,
                    TextTruncate = Enum.TextTruncate.AtEnd
                })
                AddCorner(BindBtn, 6)
                local BindStroke = Create("UIStroke", {Parent = BindBtn, Color = SelectedTheme.TextDark, Transparency = 0.8, Thickness = 1})
                
                table.insert(Library.ThemeObjects.Keybinds, BindBtn)
                table.insert(Library.ThemeObjects.TextDark, BindStroke)

                local function Update(newKey)
                    CurrentKey = newKey
                    Library.Flags[Flag] = newKey.Name
                    BindBtn.Text = KeyMap[newKey.Name] or newKey.Name
                    Binding = false
                    if OnScreenBtn then
                        OnScreenBtn.Text = KeyMap[newKey.Name] or newKey.Name
                    end
                end

                BindBtn.MouseButton1Click:Connect(function()
                    if Binding then return end
                    Binding = true
                    BindBtn.Text = "..."
                end)

                local hoverTweenBind, leaveTweenBind
                BindBtn.MouseEnter:Connect(function()
                    if leaveTweenBind then leaveTweenBind:Cancel() end
                    hoverTweenBind = TS:Create(BindBtn, TweenInfo.new(0.2), {TextColor3 = SelectedTheme.Text})
                    hoverTweenBind:Play()
                end)
                
                BindBtn.MouseLeave:Connect(function()
                    if not Binding then
                        if hoverTweenBind then hoverTweenBind:Cancel() end
                        leaveTweenBind = TS:Create(BindBtn, TweenInfo.new(0.2), {TextColor3 = SelectedTheme.TextDark})
                        leaveTweenBind:Play()
                    end
                end)
                
                -- Подключение InputBegan: срабатывает только если мы НЕ находимся в режиме бинда
                local Connection
                Connection = UIS.InputBegan:Connect(function(input, gpe) 
                    if not Frame.Parent then
                        Connection:Disconnect()
                        return
                    end 
                    if not Binding then 
                        if input.KeyCode == CurrentKey and not gpe and not UIS:GetFocusedTextBox() then 
                            if Library.IsLoadingConfig then return end
                            if Cfg.Callback then
                                Cfg.Callback(input.KeyCode)
                            end 
                        end 
                    end 
                end)

                -- Подключение InputEnded: срабатывает только для назначения новой клавиши (когда мы ее отпускаем)
                local Connection2
                Connection2 = UIS.InputEnded:Connect(function(input, gpe)
                    if not Frame.Parent then
                        Connection2:Disconnect()
                        return
                    end
                    if Binding then
                        if input.UserInputType == Enum.UserInputType.Keyboard and input.KeyCode ~= Enum.KeyCode.Unknown then
                            Update(input.KeyCode)
                        end
                    end
                end)
                
                Library.Items[Flag] = {
                    Set = function(keyName)
                        if Enum.KeyCode[keyName] then
                            Update(Enum.KeyCode[keyName])
                        end
                    end
                }
            end

            return Elements
        end

        local TabElements = GetElements(Page)

        if Two_Column then
            function TabElements:CreateBlock(BlockCfg)
                local BlockName = BlockCfg.Name or "Block"
                local Side = BlockCfg.Side or "Left"
                local TargetColumn = (Side == "Right" and RightCol) or LeftCol
                
                local BlockFrame = Create("Frame", {
                    Parent = TargetColumn,
                    Size = UDim2.new(1, 0, 0, 0),
                    AutomaticSize = Enum.AutomaticSize.Y,
                    BackgroundColor3 = SelectedTheme.Second,
                    BackgroundTransparency = 0.5,
                    ZIndex = 5,
                    ThemeTag = "Second"
                })
                AddCorner(BlockFrame, 8)

                local BlockStroke = Create("UIStroke", { Parent = BlockFrame, Color = Color3.new(1, 1, 1), Transparency = 0.7, Thickness = 1 })
                local BlockGrad = Create("UIGradient", { Parent = BlockStroke, Color = SelectedTheme.Gradient })
                table.insert(Library.GradientObjects, BlockGrad)
                
                Create("Frame", {
                    Parent = BlockFrame,
                    Size = UDim2.new(1, -24, 0, 1),
                    Position = UDim2.new(0, 12, 0, 36),
                    BackgroundColor3 = SelectedTheme.TextDark,
                    BackgroundTransparency = 0.8,
                    BorderSizePixel = 0,
                    ZIndex = 6,
                    ThemeTag = "TextDark"
                })
                
                local HasIcon = (BlockCfg.ImageID ~= nil and BlockCfg.ImageID ~= "")
                local TitleOffsetX = 12
                
                if HasIcon then
                    local BlockIcon = Create("ImageLabel", {
                        Parent = BlockFrame,
                        Size = UDim2.new(0, 18, 0, 18),
                        Position = UDim2.new(0, 12, 0, 9),
                        BackgroundTransparency = 1,
                        ImageColor3 = SelectedTheme.TextDark,
                        ZIndex = 6
                    })
                    SetImageAsync(BlockIcon, "Image", BlockCfg.ImageID)
                    TitleOffsetX = 36
                    table.insert(Library.ThemeObjects.TextDark, BlockIcon)
                end
                
                Create("TextLabel", {
                    Parent = BlockFrame,
                    Size = UDim2.new(1, -TitleOffsetX - 12, 0, 36),
                    Position = UDim2.new(0, TitleOffsetX, 0, 0),
                    BackgroundTransparency = 1,
                    Text = BlockName,
                    Font = Library.GlobalFontBold,
                    TextColor3 = SelectedTheme.Text,
                    TextSize = 14,
                    TextXAlignment = Enum.TextXAlignment.Left,
                    ZIndex = 6,
                    ThemeTag = "Text"
                })
                
                local BlockContent = Create("Frame", {
                    Parent = BlockFrame,
                    Size = UDim2.new(1, 0, 0, 0),
                    Position = UDim2.new(0, 0, 0, 42),
                    AutomaticSize = Enum.AutomaticSize.Y,
                    BackgroundTransparency = 1,
                    ZIndex = 5
                })
                
                Create("UIListLayout", { Parent = BlockContent, SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0, 6) })
                Create("UIPadding", { Parent = BlockContent, PaddingTop = UDim.new(0, 2), PaddingBottom = UDim.new(0, 10), PaddingLeft = UDim.new(0, 8), PaddingRight = UDim.new(0, 8) })
                
                return GetElements(BlockContent)
            end
        end
        
        return TabElements, Activate
    end

    local SettingsTab, OpenSettingsFunc = Funcs:CreateTab("UI Settings", true, "rbxassetid://7059346373")

    local AppBlock = SettingsTab:CreateBlock({Name = "Appearance", Side = "Left"})
    
    AppBlock:CreateDropdown({
        Name = "Font",
        Flag = "Settings_Font",
        Items = AvailableFonts,
        Default = Library.GlobalFont.Name,
        Callback = function(val)
            UpdateFonts(val)
        end
    })
    
    AppBlock:CreateSlider({
        Name = "Image Transparency",
        Flag = "Settings_ImageTrans",
        Min = 0,
        Max = 100,
        Default = math.floor(ImageTrans * 100),
        Callback = function(val)
            MainBgImage.ImageTransparency = val / 100
        end
    })
    
    AppBlock:CreateSlider({
        Name = "Background Transparency",
        Flag = "Settings_BgTrans",
        Min = 0,
        Max = 100,
        Default = math.floor(WindowTrans * 100),
        Callback = function(val)
            local t = val / 100
            MainBgColor.BackgroundTransparency = t
        end
    })
    
    AppBlock:CreateSlider({
        Name = "HUD Transparency",
        Flag = "Settings_HudTrans",
        Min = 0,
        Max = 100,
        Default = math.floor((SelectedTheme.HudTransparency or 0.5) * 100),
        Callback = function(val)
            local t = val / 100
            SelectedTheme.HudTransparency = t
            for _, obj in pairs(Library.WatermarkIslands) do
                if obj and obj.Parent then
                    obj.BackgroundTransparency = t
                end
            end
        end
    })

    AppBlock:CreateInput({
        Name = "Background ID",
        Flag = "Settings_BgImage",
        Placeholder = "ID...",
        Callback = function(val)
            val = GetAssetId(val)
            SetImageAsync(MainBgImage, "Image", val)
            SelectedTheme.Background = val
        end
    })

    AppBlock:CreateSlider({
        Name = "Corner Radius",
        Flag = "Settings_CornerRadius",
        Min = 0,
        Max = 25,
        Default = Library.GlobalCornerValue,
        Callback = function(val)
            UpdateCorners(val)
        end
    })

    AppBlock:CreateSlider({
        Name = "UI Scale",
        Flag = "Settings_UiScale",
        Min = 0.7,
        Max = 1.5,
        Default = Library.GlobalScale,
        Callback = function(val)
            val = math.round(val * 100) / 100
            UpdateScale(val)
        end
    })

    local KeyBlock = SettingsTab:CreateBlock({Name = "Keybinds", Side = "Left"})
    
    KeyBlock:CreateKeybind({
        Name = "Toggle UI",
        Flag = "Settings_ToggleKey",
        Default = Library.ToggleKey,
        Callback = function() end
    })

    local ConfigBlock = SettingsTab:CreateBlock({Name = "Config Manager", Side = "Left"})

    local ConfigNameInput = "MyConfig"
    ConfigBlock:CreateInput({
        Name = "Config Name",
        Flag = "Settings_CfgName",
        Default = "MyConfig",
        Callback = function(v)
            ConfigNameInput = v
        end
    })
    
    ConfigBlock:CreateButton({
        Name = "Save Config",
        Callback = function()
            Library:SaveConfig(ConfigNameInput)
        end
    })

    local LoadDropCfg = ConfigBlock:CreateDropdown({
        Name = "Load Config",
        Flag = "Settings_CfgLoad",
        Items = Library:GetConfigs(),
        UpdateList = function()
            return Library:GetConfigs()
        end,
        Callback = function(val)
            Library:LoadConfig(val)
        end
    })

    ConfigBlock:CreateButton({
        Name = "Delete Selected Config",
        Callback = function()
            local sel = LoadDropCfg:Get()
            if sel and isfile(Library.ConfigFolder .. "/" .. sel .. ".json") then
                Library:DeleteConfig(sel)
            end
        end
    })

    local ColorBlock = SettingsTab:CreateBlock({Name = "Theme Colors", Side = "Right"})
    
    ColorBlock:CreateColorPicker({
        Name = "MainColor",
        Flag = "Settings_MainColor",
        Default = SelectedTheme.Main,
        Callback = function(c)
            SelectedTheme.Main = c
            Themes.Default.Main = c
            UpdateThemeObjects()
        end
    }) 
    
    ColorBlock:CreateColorPicker({
        Name = "Second Color",
        Flag = "Settings_SecondColor",
        Default = SelectedTheme.Second,
        Callback = function(c)
            SelectedTheme.Second = c
            Themes.Default.Second = c
            UpdateThemeObjects()
        end
    })
    
    ColorBlock:CreateColorPicker({
        Name = "Element Color",
        Flag = "Settings_ElementColor",
        Default = SelectedTheme.ElementAccent,
        Callback = function(c)
            SelectedTheme.ElementAccent = c
            Themes.Default.ElementAccent = c
            UpdateThemeObjects()
        end
    })
    
    ColorBlock:CreateColorPicker({
        Name = "Text Color",
        Flag = "Settings_TextColor",
        Default = SelectedTheme.Text,
        Callback = function(c) 
            SelectedTheme.Text = c
            Themes.Default.Text = c
            UpdateThemeObjects()
        end
    })
    
    ColorBlock:CreateColorPicker({
        Name = "Gradient Start",
        Flag = "Settings_GradStart",
        Default = SelectedTheme.GradientStart or SelectedTheme.Accent,
        Callback = function(c)
            SelectedTheme.GradientStart = c
            SelectedTheme.Gradient = GetGradientSeq(SelectedTheme)
            UpdateGradients(SelectedTheme.Gradient)
        end
    })
    
    ColorBlock:CreateColorPicker({
        Name = "Gradient End",
        Flag = "Settings_GradEnd",
        Default = SelectedTheme.GradientEnd or SelectedTheme.Accent,
        Callback = function(c)
            SelectedTheme.GradientEnd = c
            SelectedTheme.Gradient = GetGradientSeq(SelectedTheme)
            UpdateGradients(SelectedTheme.Gradient)
        end
    })

    local ThemeBlock = SettingsTab:CreateBlock({Name = "Theme Manager", Side = "Right"})
    
    local ThemeNameInput = "MyTheme"
    ThemeBlock:CreateInput({
        Name = "Theme Name",
        Flag = "Settings_ThemeName",
        Default = "MyTheme",
        Callback = function(v)
            ThemeNameInput = v
        end
    })
    
    ThemeBlock:CreateButton({
        Name = "Save Theme",
        Callback = function()
            local ThemeConfig = {
                Main = {R = SelectedTheme.Main.R, G = SelectedTheme.Main.G, B = SelectedTheme.Main.B},
                Second = {R = SelectedTheme.Second.R, G = SelectedTheme.Second.G, B = SelectedTheme.Second.B},
                Accent = {R = SelectedTheme.Accent.R, G = SelectedTheme.Accent.G, B = SelectedTheme.Accent.B},
                ElementAccent = {R = SelectedTheme.ElementAccent.R, G = SelectedTheme.ElementAccent.G, B = SelectedTheme.ElementAccent.B},
                Text = {R = SelectedTheme.Text.R, G = SelectedTheme.Text.G, B = SelectedTheme.Text.B},
                TextDark = {R = SelectedTheme.TextDark.R, G = SelectedTheme.TextDark.G, B = SelectedTheme.TextDark.B},
                Error = {R = SelectedTheme.Error.R, G = SelectedTheme.Error.G, B = SelectedTheme.Error.B},
                GradientStart = {R = SelectedTheme.GradientStart.R, G = SelectedTheme.GradientStart.G, B = SelectedTheme.GradientStart.B},
                GradientEnd = {R = SelectedTheme.GradientEnd.R, G = SelectedTheme.GradientEnd.G, B = SelectedTheme.GradientEnd.B},
                Background = SelectedTheme.Background,
                Transparency = MainBgColor.BackgroundTransparency,
                HudTransparency = SelectedTheme.HudTransparency,
                ImageTransparency = MainBgImage.ImageTransparency,
                Font = Library.GlobalFont.Name,
                CornerRadius = Library.GlobalCornerValue,
                UiScale = Library.GlobalScale
            }
            writefile(Library.ThemeFolder .. "/" .. ThemeNameInput .. ".json", HS:JSONEncode(ThemeConfig))
            Library:Notify({Title = "Theme Saved", Content = "Saved as " .. ThemeNameInput})
        end
    })

    local LoadDrop = ThemeBlock:CreateDropdown({
        Name = "Load Theme",
        Flag = "Settings_ThemeLoad",
        Items = Library:GetThemes(),
        UpdateList = function()
            return Library:GetThemes()
        end,
        Callback = function(val)
            local NewTheme = GetTheme(val)
            MainBgColor.BackgroundColor3 = NewTheme.Main
            SetImageAsync(MainBgImage, "Image", NewTheme.Background or "")
            MainBgColor.BackgroundTransparency = NewTheme.Transparency or 0.25
            MainBgImage.ImageTransparency = NewTheme.ImageTransparency or 0
            
            SelectedTheme = NewTheme
            Themes.Default.Main = NewTheme.Main
            Themes.Default.Second = NewTheme.Second
            Themes.Default.Accent = NewTheme.Accent
            Themes.Default.ElementAccent = NewTheme.ElementAccent
            Themes.Default.Text = NewTheme.Text
            SelectedTheme.HudTransparency = NewTheme.HudTransparency or 0.5
            SelectedTheme.UiScale = NewTheme.UiScale or 1.0
            
            for _, island in pairs(Library.WatermarkIslands) do
                if island and island.Parent then
                    island.BackgroundTransparency = SelectedTheme.HudTransparency
                end
            end
            
            if NewTheme.Font then
                UpdateFonts(NewTheme.Font)
            end
            
            UpdateThemeObjects()
            UpdateGradients(SelectedTheme.Gradient)

            if Library.Items["Settings_MainColor"] then Library.Items["Settings_MainColor"].Set(NewTheme.Main) end
            if Library.Items["Settings_SecondColor"] then Library.Items["Settings_SecondColor"].Set(NewTheme.Second) end
            if Library.Items["Settings_ElementColor"] then Library.Items["Settings_ElementColor"].Set(NewTheme.ElementAccent) end
            if Library.Items["Settings_TextColor"] then Library.Items["Settings_TextColor"].Set(NewTheme.Text) end
            if Library.Items["Settings_GradStart"] then Library.Items["Settings_GradStart"].Set(NewTheme.GradientStart or NewTheme.Accent) end
            if Library.Items["Settings_GradEnd"] then Library.Items["Settings_GradEnd"].Set(NewTheme.GradientEnd or NewTheme.Accent) end
            if Library.Items["Settings_BgTrans"] then Library.Items["Settings_BgTrans"].Set(math.floor((NewTheme.Transparency or 0.25) * 100)) end
            if Library.Items["Settings_HudTrans"] then Library.Items["Settings_HudTrans"].Set(math.floor((SelectedTheme.HudTransparency) * 100)) end
            if Library.Items["Settings_ImageTrans"] then Library.Items["Settings_ImageTrans"].Set(math.floor((NewTheme.ImageTransparency or 0) * 100)) end
            if Library.Items["Settings_BgImage"] then Library.Items["Settings_BgImage"].Set(NewTheme.Background or "") end
            if Library.Items["Settings_Font"] and NewTheme.Font then Library.Items["Settings_Font"].Set(NewTheme.Font) end
            if Library.Items["Settings_CornerRadius"] then Library.Items["Settings_CornerRadius"].Set(NewTheme.CornerRadius or 10) end
            if Library.Items["Settings_UiScale"] then Library.Items["Settings_UiScale"].Set(NewTheme.UiScale or 1.0) end

            Library:Notify({Title = "Theme Loaded", Content = "Loaded " .. val})
        end
    })

    ThemeBlock:CreateButton({
        Name = "Delete Selected Theme",
        Callback = function()
            local selected = LoadDrop:Get()
            if selected and isfile(Library.ThemeFolder .. "/" .. selected .. ".json") then
                delfile(Library.ThemeFolder .. "/" .. selected .. ".json")
                Library:Notify({Title = "Theme Deleted", Content = "Deleted " .. selected})
            else
                Library:Notify({Title = "Error", Content = "Select a valid theme first"})
            end
        end
    })

    UpdateScale(Library.GlobalScale)

    return Funcs
end

return Library
