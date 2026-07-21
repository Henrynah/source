local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Clipboard = setclipboard or toclipboard or function() end

local CORRECT_KEY = "LunxKey-9k41vb8xzwq62mjr7" 
local DISCORD_LINK = "discord.gg/AKFUwcbQT"

local Themes = {
["Scout"] = {Bg = Color3.fromRGB(35, 55, 95), Accent = Color3.fromRGB(60, 120, 240), Text = Color3.fromRGB(255, 255, 255)},
["PovHuso"] = {Bg = Color3.fromRGB(55, 30, 30), Accent = Color3.fromRGB(230, 45, 65), Text = Color3.fromRGB(255, 255, 255)},
["ApexTurka"] = {Bg = Color3.fromRGB(65, 65, 65), Accent = Color3.fromRGB(160, 160, 160), Text = Color3.fromRGB(255, 255, 255)},
["Mimi Typh"] = {Bg = Color3.fromRGB(45, 35, 25), Accent = Color3.fromRGB(188, 188, 188), Text = Color3.fromRGB(140, 90, 60)},
["g3oxq22kc"] = {Bg = Color3.fromRGB(45, 45, 45), Accent = Color3.fromRGB(188, 188, 188), Text = Color3.fromRGB(140, 140, 140)},
["CSG"] = {Bg = Color3.fromRGB(55, 30, 30), Accent = Color3.fromRGB(230, 45, 65), Text = Color3.fromRGB(255, 255, 255)}
}

_G.SelectedTheme = "CSG"

---------------------------------------------------------
local function UnlockMouse()
    UserInputService.MouseBehavior = Enum.MouseBehavior.Default
    UserInputService.MouseIconEnabled = true
end
UnlockMouse()

-- Fareyi ekranda tutmak için döngü (UI aç1kken kilitlenmesini önler)
local mouseLoop = task.spawn(function()
    while task.wait(0.2) do
        UnlockMouse()
    end
end)

---------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LunxKeySystem"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
if syn and syn.protect_gui then syn.protect_gui(ScreenGui) end
ScreenGui.Parent = game:GetService("CoreGui") or game:GetService("Players").LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 360, 0, 280)
MainFrame.Position = UDim2.new(0.5, -180, 0.5, -140)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

local UIStroke = Instance.new("UIStroke")
UIStroke.Thickness = 2
UIStroke.Color = Color3.fromRGB(111, 0, 255)
UIStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
UIStroke.Parent = MainFrame

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 45)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "LUNX SYSTEMS"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 18
Title.Parent = MainFrame

local KeyPage = Instance.new("Frame")
KeyPage.Size = UDim2.new(1, 0, 1, -45)
KeyPage.Position = UDim2.new(0, 0, 0, 45)
KeyPage.BackgroundTransparency = 1
KeyPage.Parent = MainFrame

local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(0, 280, 0, 40)
KeyInput.Position = UDim2.new(0.5, -140, 0.2, 0)
KeyInput.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
KeyInput.PlaceholderText = "Enter Security Key Here..."
KeyInput.Text = ""
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.TextSize = 14
KeyInput.Parent = KeyPage

local KeyCorner = Instance.new("UICorner")
KeyCorner.CornerRadius = UDim.new(0, 6)
KeyCorner.Parent = KeyInput

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1, 0, 0, 20)
StatusLabel.Position = UDim2.new(0, 0, 0.45, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = ""
StatusLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
StatusLabel.TextSize = 13
StatusLabel.Parent = KeyPage

local SubmitBtn = Instance.new("TextButton")
SubmitBtn.Size = UDim2.new(0, 130, 0, 35)
SubmitBtn.Position = UDim2.new(0.5, -145, 0.6, 0)
SubmitBtn.BackgroundColor3 = Color3.fromRGB(0, 162, 255)
SubmitBtn.Text = "Check Key"
SubmitBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
SubmitBtn.TextSize = 14
SubmitBtn.Parent = KeyPage

local SubmitCorner = Instance.new("UICorner")
SubmitCorner.CornerRadius = UDim.new(0, 6)
SubmitCorner.Parent = SubmitBtn

local DiscordBtn = Instance.new("TextButton")
DiscordBtn.Size = UDim2.new(0, 130, 0, 35)
DiscordBtn.Position = UDim2.new(0.5, 15, 0.6, 0)
DiscordBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
DiscordBtn.Text = "Copy Discord"
DiscordBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
DiscordBtn.TextSize = 14
DiscordBtn.Parent = KeyPage

local DiscordCorner = Instance.new("UICorner")
DiscordCorner.CornerRadius = UDim.new(0, 6)
DiscordCorner.Parent = DiscordBtn

local InfoLabel = Instance.new("TextLabel")
InfoLabel.Size = UDim2.new(1, 0, 0, 30)
InfoLabel.Position = UDim2.new(0, 0, 0.82, 0)
InfoLabel.BackgroundTransparency = 1
InfoLabel.Text = "You can only find the key in our Discord server."
InfoLabel.TextColor3 = Color3.fromRGB(160, 160, 160)
InfoLabel.TextSize = 12
InfoLabel.Parent = KeyPage

local ThemePage = Instance.new("Frame")
ThemePage.Size = UDim2.new(1, 0, 1, -45)
ThemePage.Position = UDim2.new(1, 0, 0, 45)
ThemePage.BackgroundTransparency = 1
ThemePage.Parent = MainFrame

local ThemeGrid = Instance.new("UIGridLayout")
ThemeGrid.FillDirection = Enum.FillDirection.Horizontal
ThemeGrid.HorizontalAlignment = Enum.HorizontalAlignment.Center
ThemeGrid.VerticalAlignment = Enum.VerticalAlignment.Center
ThemeGrid.CellPadding = UDim2.new(0, 8, 0, 0)
ThemeGrid.CellSize = UDim2.new(0, 90, 0, 35)
ThemeGrid.Parent = ThemePage

local LoadingPage = Instance.new("Frame")
LoadingPage.Size = UDim2.new(1, 0, 1, -45)
LoadingPage.Position = UDim2.new(1, 0, 0, 45)
LoadingPage.BackgroundTransparency = 1
LoadingPage.Parent = MainFrame

local LoadingLabel = Instance.new("TextLabel")
LoadingLabel.Size = UDim2.new(1, 0, 1, 0)
LoadingLabel.BackgroundTransparency = 1
LoadingLabel.Text = "Loading Theme Config..."
LoadingLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
LoadingLabel.TextSize = 16
LoadingLabel.Parent = LoadingPage


DiscordBtn.MouseButton1Click:Connect(function()
    Clipboard("discord.gg/e2x77rVbmZ")
    DiscordBtn.Text = "Copied Link!"
    task.wait(1.5)
    DiscordBtn.Text = "Copy Discord"
end)

SubmitBtn.MouseButton1Click:Connect(function()
    if KeyInput.Text == CORRECT_KEY then
        StatusLabel.TextColor3 = Color3.fromRGB(50, 255, 50)
        StatusLabel.Text = "Access Granted! Loading themes..."
        task.wait(1)
        
        KeyPage:Destroy()
        
        local resizeTween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 505, 0, 120),
            Position = UDim2.new(0.5, -252, 0.5, -60)
        })
        resizeTween:Play()
        
        ThemePage.Position = UDim2.new(0, 0, 0, 45)
        Title.Text = "SELECT A PRESET THEME"

        for themeName, colorData in pairs(Themes) do
            local TBtn = Instance.new("TextButton")
            TBtn.Name = themeName
            TBtn.BackgroundColor3 = colorData.Bg
            TBtn.Text = themeName
            TBtn.TextColor3 = colorData.Accent
            TBtn.TextSize = 12
            TBtn.Parent = ThemePage
            
            local TCorner = Instance.new("UICorner")
            TCorner.CornerRadius = UDim.new(0, 6)
            TCorner.Parent = TBtn
            
            local TStroke = Instance.new("UIStroke")
            TStroke.Thickness = 1
            TStroke.Color = colorData.Accent
            TStroke.Parent = TBtn
            
            TBtn.MouseButton1Click:Connect(function()
                _G.SelectedTheme = themeName
                ThemePage:Destroy()

                local normalizeTween = TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
                    Size = UDim2.new(0, 300, 0, 150),
                    Position = UDim2.new(0.5, -150, 0.5, -75)
                })
                normalizeTween:Play()
                
                MainFrame.BackgroundColor3 = colorData.Bg
                UIStroke.Color = colorData.Accent
                Title.TextColor3 = colorData.Text
                LoadingLabel.TextColor3 = colorData.Text
                Title.Text = "INITIALIZING"
                
                LoadingPage.Position = UDim2.new(0, 0, 0, 45)
                
                for i = 3, 1, -1 do
                    LoadingLabel.Text = "Loading..."
                    task.wait(1)
                end
                
                task.cancel(mouseLoop)
                ScreenGui:Destroy()
                
                _G.KeySystemVerified = true
            end)
        end
    else
        StatusLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
        StatusLabel.Text = "Invalid Key! Try again."
    end
end)

repeat task.wait(0.1) until _G.KeySystemVerified == true

print("Key verified successfully! Loaded Theme: " .. tostring(_G.SelectedTheme))
















--[[
	HUB UI LIBRARY (v2)
	--------------------
	Minecraft "silent client" tarz1: ekran1n sa üstünden sola doru
	s1ralanan ba1ms1z kategori panelleri. Her panel kendi içeriine göre
	otomatik büyür/küçülür. Dropdown/ToggleDropdown/ColorPicker aç1ld11nda
	her _eyin üstünde (global overlay) render edilir, hiçbir elementin
	arkas1nda kalmaz.

	K tu_u = tüm panelleri aç/kapat (watermark her zaman görünür kal1r)

	Bile_enler: Window, Toggle, Checkbox, Button, Slider, Dropdown,
	ToggleDropdown, ColorPicker, Keybind, Textbox, Label, Notification,
	Watermark, KeybindList, Flags, Example, CloseGui
]]

local Players          = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local RunService       = game:GetService("RunService")
local Stats             = game:GetService("Stats")
print("1")
local localPlayer = Players.LocalPlayer
local PlayerGui = localPlayer:WaitForChild("PlayerGui") 

--================================================================
-- THEME
--================================================================


--================================================================
-- D0NAM0K TEMA SEÇ0C0 (HAFIZADAN OKUR)
--================================================================

-- Tüm Tema Veritaban1
local ThemePresets = {
    ["Scout"] = {
        Background  = Color3.fromRGB(12, 18, 28),
        Panel       = Color3.fromRGB(18, 26, 40),
        Header      = Color3.fromRGB(24, 36, 54),
        Row         = Color3.fromRGB(22, 32, 48),
        RowHover    = Color3.fromRGB(32, 46, 68),
        Elevated    = Color3.fromRGB(36, 50, 75),
        Accent      = Color3.fromRGB(0, 180, 255),
        AccentDark  = Color3.fromRGB(0, 130, 200),
        Text        = Color3.fromRGB(255, 255, 255),
        SubText     = Color3.fromRGB(175, 200, 220),
        Stroke      = Color3.fromRGB(44, 62, 88),
        Green       = Color3.fromRGB(80, 210, 140),
        Red         = Color3.fromRGB(240, 100, 100),
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    },
    ["PovHuso"] = {
        Background  = Color3.fromRGB(15, 15, 15),
        Panel       = Color3.fromRGB(22, 22, 22),
        Header      = Color3.fromRGB(28, 28, 28),
        Row         = Color3.fromRGB(25, 25, 25),
        RowHover    = Color3.fromRGB(35, 35, 35),
        Elevated    = Color3.fromRGB(40, 40, 40),
        Accent      = Color3.fromRGB(220, 40, 40),
        AccentDark  = Color3.fromRGB(160, 25, 25),
        Text        = Color3.fromRGB(255, 255, 255),
        SubText     = Color3.fromRGB(180, 180, 180),
        Stroke      = Color3.fromRGB(45, 45, 45),
        Green       = Color3.fromRGB(40, 180, 100),
        Red         = Color3.fromRGB(220, 40, 40),
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    },
    ["ApexTurka"] = {
        Background  = Color3.fromRGB(14, 14, 14),
        Panel       = Color3.fromRGB(22, 22, 22),
        Header      = Color3.fromRGB(30, 30, 30),
        Row         = Color3.fromRGB(26, 26, 26),
        RowHover    = Color3.fromRGB(36, 36, 36),
        Elevated    = Color3.fromRGB(42, 42, 42),
        Accent      = Color3.fromRGB(255, 255, 255),
        AccentDark  = Color3.fromRGB(180, 180, 180),
        Text        = Color3.fromRGB(255, 255, 255),
        SubText     = Color3.fromRGB(150, 150, 150),
        Stroke      = Color3.fromRGB(45, 45, 45),
        Green       = Color3.fromRGB(120, 120, 120),
        Red         = Color3.fromRGB(60, 60, 60),
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    },
    ["CSG"] = {
        Background  = Color3.fromRGB(12, 12, 12),
        Panel       = Color3.fromRGB(18, 18, 18),
        Header      = Color3.fromRGB(24, 16, 16),
        Row         = Color3.fromRGB(22, 22, 22),
        RowHover    = Color3.fromRGB(32, 24, 24),
        Elevated    = Color3.fromRGB(35, 35, 35),
        Accent      = Color3.fromRGB(230, 35, 35),
        AccentDark  = Color3.fromRGB(160, 20, 20),
        Text        = Color3.fromRGB(235, 235, 235),
        SubText     = Color3.fromRGB(150, 140, 140),
        Stroke      = Color3.fromRGB(40, 32, 32),
        Green       = Color3.fromRGB(40, 180, 100),
        Red         = Color3.fromRGB(230, 35, 35),
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    },
    ["Mimi Typh"] = {
        Background  = Color3.fromRGB(24, 20, 18),
        Panel       = Color3.fromRGB(34, 28, 26),
        Header      = Color3.fromRGB(48, 38, 34),
        Row         = Color3.fromRGB(40, 32, 29),
        RowHover    = Color3.fromRGB(54, 44, 40),
        Elevated    = Color3.fromRGB(60, 50, 45),
        Accent      = Color3.fromRGB(176, 129, 93),
        AccentDark  = Color3.fromRGB(135, 94, 64),
        Text        = Color3.fromRGB(255, 255, 255),
        SubText     = Color3.fromRGB(215, 205, 195),
        Stroke      = Color3.fromRGB(74, 62, 57), 
        Green       = Color3.fromRGB(120, 190, 110),
        Red         = Color3.fromRGB(210, 100, 90),
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    },
    ["g3oxq22kc"] = {
        Background  = Color3.fromRGB(20, 20, 20),
        Panel       = Color3.fromRGB(30, 30, 30),
        Header      = Color3.fromRGB(42, 42, 42),
        Row         = Color3.fromRGB(35, 35, 35),
        RowHover    = Color3.fromRGB(48, 48, 48),
        Elevated    = Color3.fromRGB(55, 55, 55),
        Accent      = Color3.fromRGB(150, 150, 150),
        AccentDark  = Color3.fromRGB(100, 100, 100),
        Text        = Color3.fromRGB(255, 255, 255),
        SubText     = Color3.fromRGB(205, 205, 205),
        Stroke      = Color3.fromRGB(65, 65, 65), 
        Green       = Color3.fromRGB(110, 150, 110), 
        Red         = Color3.fromRGB(150, 110, 110), 
        Font        = Enum.Font.GothamMedium,
        FontBold    = Enum.Font.GothamBold,
    }
}

-- Haf1zadan seçilen temay1 çek, eer haf1za bo_sa varsay1lan olarak "CSG" yükle
local SelectedName = _G.SelectedTheme or "CSG"

-- Aktif tema tablosunu haf1zadaki isme göre e_itle
local Theme = ThemePresets[SelectedName] or ThemePresets["CSG"]


--[[


local Theme = { -- ApexTurka theme:
    Background  = Color3.fromRGB(14, 14, 14),
    Panel       = Color3.fromRGB(22, 22, 22),
    Header      = Color3.fromRGB(30, 30, 30),
    Row         = Color3.fromRGB(26, 26, 26),
    RowHover    = Color3.fromRGB(36, 36, 36),
    Elevated    = Color3.fromRGB(42, 42, 42),
    Accent      = Color3.fromRGB(255, 255, 255),
    AccentDark  = Color3.fromRGB(180, 180, 180),
    Text        = Color3.fromRGB(255, 255, 255),
    SubText     = Color3.fromRGB(150, 150, 150),
    Stroke      = Color3.fromRGB(45, 45, 45),
    Green       = Color3.fromRGB(120, 120, 120),
    Red         = Color3.fromRGB(60, 60, 60),
    Font        = Enum.Font.GothamMedium,
    FontBold    = Enum.Font.GothamBold,
}




local Theme = { -- CSG theme
    Background  = Color3.fromRGB(12, 12, 12),
    Panel       = Color3.fromRGB(18, 18, 18),
    Header      = Color3.fromRGB(24, 16, 16),
    Row         = Color3.fromRGB(22, 22, 22),
    RowHover    = Color3.fromRGB(32, 24, 24),
    Elevated    = Color3.fromRGB(35, 35, 35),
    Accent      = Color3.fromRGB(230, 35, 35),
    AccentDark  = Color3.fromRGB(160, 20, 20),
    Text        = Color3.fromRGB(235, 235, 235),
    SubText     = Color3.fromRGB(150, 140, 140),
    Stroke      = Color3.fromRGB(40, 32, 32),
    Green       = Color3.fromRGB(40, 180, 100),
    Red         = Color3.fromRGB(230, 35, 35),
    Font        = Enum.Font.GothamMedium,
    FontBold    = Enum.Font.GothamBold,
}


local Theme = { --Povhuso theme
    Background  = Color3.fromRGB(15, 15, 15),
    Panel       = Color3.fromRGB(22, 22, 22),
    Header      = Color3.fromRGB(28, 28, 28),
    Row         = Color3.fromRGB(25, 25, 25),
    RowHover    = Color3.fromRGB(35, 35, 35),
    Elevated    = Color3.fromRGB(40, 40, 40),
    Accent      = Color3.fromRGB(220, 40, 40),
    AccentDark  = Color3.fromRGB(160, 25, 25),
    Text        = Color3.fromRGB(255, 255, 255),
    SubText     = Color3.fromRGB(180, 180, 180),
    Stroke      = Color3.fromRGB(45, 45, 45),
    Green       = Color3.fromRGB(40, 180, 100),
    Red         = Color3.fromRGB(220, 40, 40),
    Font        = Enum.Font.GothamMedium,
    FontBold    = Enum.Font.GothamBold,
}






local Theme = { // Scout theme
    Background  = Color3.fromRGB(12, 18, 28),
    Panel       = Color3.fromRGB(18, 26, 40),
    Header      = Color3.fromRGB(24, 36, 54),
    Row         = Color3.fromRGB(22, 32, 48),
    RowHover    = Color3.fromRGB(32, 46, 68),
    Elevated    = Color3.fromRGB(36, 50, 75),
    Accent      = Color3.fromRGB(0, 180, 255),
    AccentDark  = Color3.fromRGB(0, 130, 200),
    Text        = Color3.fromRGB(255, 255, 255),
    SubText     = Color3.fromRGB(175, 200, 220),
    Stroke      = Color3.fromRGB(44, 62, 88),
    Green       = Color3.fromRGB(80, 210, 140),
    Red         = Color3.fromRGB(240, 100, 100),
    Font        = Enum.Font.GothamMedium,
    FontBold    = Enum.Font.GothamBold,
}



local Theme = { -- MIMI TYPH
    Background  = Color3.fromRGB(24, 20, 18),
    Panel       = Color3.fromRGB(34, 28, 26),
    Header      = Color3.fromRGB(48, 38, 34),
    Row         = Color3.fromRGB(40, 32, 29),
    RowHover    = Color3.fromRGB(54, 44, 40),
    Elevated    = Color3.fromRGB(60, 50, 45),
    Accent      = Color3.fromRGB(176, 129, 93),
    AccentDark  = Color3.fromRGB(135, 94, 64),
    Text        = Color3.fromRGB(255, 255, 255),
    SubText     = Color3.fromRGB(215, 205, 195),
    Stroke      = Color3.fromRGB(74, 62, 57), 
    Green       = Color3.fromRGB(120, 190, 110),
    Red         = Color3.fromRGB(210, 100, 90),
    Font        = Enum.Font.GothamMedium,
    FontBold    = Enum.Font.GothamBold,
}



]]

print("Lunx > ")
print("Lunx > Theme completed")
--================================================================
-- UTILITIES
--================================================================
local function Create(class, props, children)
	local inst = Instance.new(class)
	for k, v in pairs(props or {}) do
		inst[k] = v
	end
	for _, c in ipairs(children or {}) do
		c.Parent = inst
	end
	return inst
end

local function Corner(radius)
	return Create("UICorner", { CornerRadius = UDim.new(0, radius or 5) })
end

local function Stroke(color, thickness)
	return Create("UIStroke", { Color = color or Theme.Stroke, Thickness = thickness or 1 })
end

local function Tween(inst, props, time, style, dir)
	local tw = TweenService:Create(inst, TweenInfo.new(
		time or 0.15, style or Enum.EasingStyle.Quad, dir or Enum.EasingDirection.Out
	), props)
	tw:Play()
	return tw
end

-- Sat1rlara fare üstüne gelince/çekilince renk animasyonu ekler
local function AddHover(row, baseColor, hoverColor)
	baseColor = baseColor or Theme.Row
	hoverColor = hoverColor or Theme.RowHover
	row.MouseEnter:Connect(function() Tween(row, { BackgroundColor3 = hoverColor }, 0.12) end)
	row.MouseLeave:Connect(function() Tween(row, { BackgroundColor3 = baseColor }, 0.12) end)
end

-- Bir GuiObject'i (ve varsa UIStroke'unu) fade-in ile belirir hale getirir
local function FadeIn(inst, targetTransparency, time)
	local startT = inst.BackgroundTransparency
	inst.BackgroundTransparency = 1
	Tween(inst, { BackgroundTransparency = targetTransparency or startT }, time or 0.2)
	local st = inst:FindFirstChildOfClass("UIStroke")
	if st then
		local sTarget = st.Transparency
		st.Transparency = 1
		Tween(st, { Transparency = sTarget }, time or 0.2)
	end
end

local function MakeDraggable(dragHandle, target)
	local dragging, dragStart, startPos, dragInput
	dragHandle.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = target.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then dragging = false end
			end)
		end
	end)
	dragHandle.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
			dragInput = input
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			target.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
end

--================================================================
-- HUB
--================================================================
local Hub = {}
Hub.__index = Hub
Hub.Flags = {}
Hub.Connections = {}
Hub._KeybindEntries = {}
Hub.Panels = {}
Hub.GuiVisible = true

function Hub:CreateWindow(config)
	config = config or {}
	local Title       = config.Title or "Lunx Hub"
	local ToggleKey   = config.ToggleKey or Enum.KeyCode.K
	local ShowWatermark = config.Watermark ~= false
	self.PanelTransparency = config.PanelTransparency or 0.45 -- panel arka plan1 saydaml11 (0 = opak, 1 = tamamen görünmez)

	local ScreenGui = Create("ScreenGui", {
		Name = "Hub_" .. Title,
		ResetOnSpawn = false,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
		DisplayOrder = 10,
		Parent = PlayerGui,
	})
	self.ScreenGui = ScreenGui

	-- Panellerin oturduu katman (alt katman)
	local PanelLayer = Create("Frame", {
		Name = "PanelLayer",
		BackgroundTransparency = 1,
		Size = UDim2.fromScale(1, 1),
		ZIndex = 1,
		Parent = ScreenGui,
	})
	self.PanelLayer = PanelLayer

	-- Dropdown/ColorPicker gibi aç1l1r içeriklerin oturduu EN ÜST katman
	local OverlayCatcher = Create("TextButton", {
		Name = "OverlayCatcher",
		Text = "",
		BackgroundTransparency = 1,
		Size = UDim2.fromScale(1, 1),
		ZIndex = 999,
		Visible = false,
		Parent = ScreenGui,
	})
	self.OverlayCatcher = OverlayCatcher

	local Overlay = Create("Frame", {
		Name = "Overlay",
		BackgroundTransparency = 1,
		Size = UDim2.fromScale(1, 1),
		ZIndex = 1000,
		Parent = ScreenGui,
	})
	self.Overlay = Overlay

	OverlayCatcher.MouseButton1Click:Connect(function() self:CloseOverlay() end)

	-- Bildirimler
	local NotifyHolder = Create("Frame", {
		Name = "Notifications",
		AnchorPoint = Vector2.new(1, 1),
		Position = UDim2.new(1, -14, 1, -14),
		Size = UDim2.fromOffset(280, 0),
		BackgroundTransparency = 1,
		ZIndex = 500,
		Parent = ScreenGui,
	})
	Create("UIListLayout", {
		VerticalAlignment = Enum.VerticalAlignment.Bottom,
		HorizontalAlignment = Enum.HorizontalAlignment.Right,
		Padding = UDim.new(0, 8),
		SortOrder = Enum.SortOrder.LayoutOrder,
		Parent = NotifyHolder,
	})
	self.NotifyHolder = NotifyHolder

	-- Keybind listesi (aktif tu_lar) - sol altta
	local KeybindListFrame = Create("Frame", {
		Name = "KeybindList",
		AnchorPoint = Vector2.new(0, 1),
		Position = UDim2.new(0, 14, 1, -14),
		Size = UDim2.fromOffset(180, 0),
		BackgroundTransparency = 1,
		ZIndex = 500,
		Parent = ScreenGui,
	})
	Create("UIListLayout", {
		VerticalAlignment = Enum.VerticalAlignment.Bottom,
		Padding = UDim.new(0, 3),
		SortOrder = Enum.SortOrder.LayoutOrder,
		Parent = KeybindListFrame,
	})
	self.KeybindListFrame = KeybindListFrame

	-- K tu_u = tüm panelleri aç/kapat
	self._savedMouseBehavior = UserInputService.MouseBehavior
	self._savedMouseIconEnabled = UserInputService.MouseIconEnabled
	-- Hub varsay1lan olarak aç1k ba_lad11 için fareyi hemen serbest b1rak
	UserInputService.MouseBehavior = Enum.MouseBehavior.Default
	UserInputService.MouseIconEnabled = true

	local TweenService = game:GetService("TweenService")
    local Workspace = game:GetService("Workspace")
    local CurrentCamera = Workspace.CurrentCamera

    local RunService = game:GetService("RunService")


	UserInputService.InputBegan:Connect(function(input, gpe)
        if gpe then return end
        if input.KeyCode == ToggleKey then
            if not self then return end
            self.GuiVisible = not self.GuiVisible
            if type(self._AnimatePanelsVisible) == "function" then
                self:_AnimatePanelsVisible(self.GuiVisible)
            end
            if self.KeybindListFrame then
                self.KeybindListFrame.Visible = self.GuiVisible
            end
            if self.Mascot then
                -- PNG'nin alt1ndaki gizli bo_luu yok etmek için a_a1 itme miktar1 (Piksel cinsinden)
                -- Eer hâlâ havada kal1rsa bu 30 say1s1n1 büyüt, çok a_a1 inerse küçült.
                local Y_KAYMASI = 30 
                
                self.Mascot.AnchorPoint = Vector2.new(1, 1)
                self._mascotShownPos = UDim2.new(1, 0, 1, Y_KAYMASI)
                self._mascotHiddenPos = UDim2.new(1, 0, 1, self.Mascot.Size.Y.Offset + 50)
                
                local targetPos = self.GuiVisible and self._mascotShownPos or self._mascotHiddenPos
                local tweenInfo = TweenInfo.new(0.35, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
                local tween = TweenService:Create(self.Mascot, tweenInfo, {Position = targetPos})
                tween:Play()
            end
            if self.KeybindListFrame and self.KeybindListFrame:IsA("GuiObject") then
                pcall(function()
                    self.KeybindListFrame.Modal = self.GuiVisible
                end)
            end
            if self.GuiVisible then
                task.spawn(function()
                    while self.GuiVisible and task.wait() do
                        pcall(function()
                            UserInputService.MouseBehavior = Enum.MouseBehavior.Default
                            UserInputService.MouseIconEnabled = true
                        end)
                    end
                end)
            else
                pcall(function()
                    task.wait(0.02)
                    UserInputService.MouseBehavior = Enum.MouseBehavior.Default
                end)
            end
        end
    end)

	local Window = setmetatable({
		Hub = self,
		ScreenGui = ScreenGui,
		PanelLayer = PanelLayer,
		Tabs = {},
		_nextRight = 14, -- sa kenardan ba_lay1p sola doru ilerler
	}, { __index = self._WindowMeta })

	self.Window = Window

	if ShowWatermark then
		self:CreateWatermark({ Title = Title })
	end

	self:CreateMascot(config)

	return Window
end

-- Hub aç1l1nca sa alttan kayarak gelen saydam maskot.
-- Kendi görselini vermezsen (MascotImage bo_ b1rak1l1rsa), tamamen kod
-- içinde çizilen orijinal bir "blob" karakter kullan1l1r ve z1plar.
-- ÖNEML0: Roblox d1_ar1dan (http/github vb.) görsel linkini dorudan
-- gösteremez; kendi gif'ini kullanmak istersen kare kare sprite-sheet
-- haline getirip Roblox'a yükleyip ald11n "rbxassetid://..." deerini
-- MascotImage'a vermelisin.
function Hub:CreateMascot(config)
    local size = config.MascotSize or UDim2.fromOffset(100, 110)
    local transparency = config.MascotTransparency or 0.1
    
    -- Gelen linki kontrol etme mant11
    local rawImage = config.MascotImage
    local useImage = rawImage and rawImage ~= "" and rawImage ~= "rbxassetid://0"
    local finalImage = rawImage

    if useImage then
        -- Eer link "rbxassetid://" içermiyorsa ve sadece rakamlardan olu_muyorsa (yani d1_ balant1ysa)
        if not string.find(rawImage, "rbxassetid://") and not tonumber(rawImage) then
            -- Linkin sonundaki dosya ad1n1 ay1kla (Örn: "http://site.com/maskot.png" -> "maskot.png")
            local fileName = rawImage:match("([^/]+)$") or "mascot_custom.png"
            
            -- Executor fonksiyonlar1n1 kontrol et
            if writefile and readfile and isfile and getcustomasset then
                if not isfile(fileName) then
                    -- Eer dosya executor workspace'inde yoksa, internetten indir ve kaydet
                    local success, result = pcall(function()
                        return game:HttpGet(rawImage)
                    end)
                    if success and result then
                        writefile(fileName, result)
                    end
                end
                
                -- Eer dosya ba_ar1yla indi veya zaten varsa executor assetine çevir
                if isfile(fileName) then
                    finalImage = getcustomasset(fileName)
                else
                    useImage = false -- 0ndirme ba_ar1s1zsa orijinal karaktere dön
                end
            else
                -- Eer executor bu fonksiyonlar1 desteklemiyorsa mecbur orijinal karaktere dön
                useImage = false
            end
        elseif tonumber(rawImage) then
            -- Eer sadece düz ID girildiyse otomatik rbxassetid format1na çevir
            finalImage = "rbxassetid://" .. rawImage
        end
    end

    local Container = Create("Frame", {
        Name = "Mascot",
        BackgroundTransparency = 1,
        Size = size,
        AnchorPoint = Vector2.new(1, 1),
        ZIndex = 5,
        Parent = self.ScreenGui,
    })

    self._mascotShownPos = UDim2.new(1, -14, 1, -14)
    self._mascotHiddenPos = UDim2.new(1, -14, 1, size.Y.Offset + 40)
    Container.Position = self.GuiVisible and self._mascotShownPos or self._mascotHiddenPos
    self.Mascot = Container

    if useImage then
        local Mascot = Create("ImageLabel", {
            Image = finalImage, -- D1_ linkten çevrilen veya orijinal Roblox linki
            ImageTransparency = transparency,
            BackgroundTransparency = 1,
            Size = UDim2.fromScale(1, 1),
            ScaleType = Enum.ScaleType.Fit,
            ZIndex = 5,
            Parent = Container,
        })

        -- Sprite-sheet animasyon kontrolü
        if config.MascotFrames then
            local f = config.MascotFrames
            local sheetSize = f.SheetSize or Vector2.new(1024, 1024)
            local frameW = sheetSize.X / f.Columns
            local frameH = sheetSize.Y / f.Rows
            Mascot.ImageRectSize = Vector2.new(frameW, frameH)

            self.Connections.MascotAnim = task.spawn(function()
                local i = 0
                while Mascot.Parent do
                    local col = i % f.Columns
                    local row = math.floor(i / f.Columns) % f.Rows
                    Mascot.ImageRectOffset = Vector2.new(col * frameW, row * frameH)
                    i = (i + 1) % f.FrameCount
                    task.wait(1 / (f.FrameRate or 12))
                end
            end)
        end
    else
        -- OR0J0NAL vektör karakter: yuvarlak gövde + iki göz, sürekli z1plar
        local Body = Create("Frame", {
            Name = "Body",
            Size = UDim2.fromScale(0.7, 0.6),
            Position = UDim2.new(0.15, 0, 0.32, 0),
            BackgroundColor3 = Theme.Accent,
            BackgroundTransparency = transparency,
            ZIndex = 5,
            Parent = Container,
        }, { Corner(999) })

        Create("Frame", {
            Size = UDim2.fromOffset(9, 9),
            Position = UDim2.new(0.28, -4, 0.38, -4),
            BackgroundColor3 = Color3.new(1, 1, 1),
            BackgroundTransparency = transparency,
            ZIndex = 6,
            Parent = Body,
        }, { Corner(999) })

        Create("Frame", {
            Size = UDim2.fromOffset(9, 9),
            Position = UDim2.new(0.72, -4, 0.38, -4),
            BackgroundColor3 = Color3.new(1, 1, 1),
            BackgroundTransparency = transparency,
            ZIndex = 6,
            Parent = Body,
        }, { Corner(999) })

        -- gölge (yere deme hissi)
        local Shadow = Create("Frame", {
            Size = UDim2.new(0.5, 0, 0, 8),
            Position = UDim2.new(0.25, 0, 0.95, 0),
            BackgroundColor3 = Color3.new(0, 0, 0),
            BackgroundTransparency = 0.6,
            ZIndex = 4,
            Parent = Container,
        }, { Corner(999) })

        -- sürekli z1plama animasyonu
        self.Connections.MascotIdle = task.spawn(function()
            while Container.Parent do
                Tween(Body, { Position = UDim2.new(0.15, 0, 0.24, 0) }, 0.45, Enum.EasingStyle.Sine, Enum.EasingDirection.Out)
                Tween(Shadow, { Size = UDim2.new(0.4, 0, 0, 6) }, 0.45)
                task.wait(0.45)
                Tween(Body, { Position = UDim2.new(0.15, 0, 0.32, 0) }, 0.45, Enum.EasingStyle.Sine, Enum.EasingDirection.In)
                Tween(Shadow, { Size = UDim2.new(0.5, 0, 0, 8) }, 0.45)
                task.wait(0.45)
            end
        end)
    end

    return Container
end

-- Aç1k olan overlay (dropdown/colorpicker listesi) kapat1l1r (fade-out ile)
function Hub:CloseOverlay()
	local old = self._overlayContent
	self._overlayContent = nil
	if old then
		Tween(old, { BackgroundTransparency = 1 }, 0.12)
		local st = old:FindFirstChildOfClass("UIStroke")
		if st then Tween(st, { Transparency = 1 }, 0.12) end
		task.delay(0.12, function() old:Destroy() end)
	end
	if self._overlayCloseCallback then
		local cb = self._overlayCloseCallback
		self._overlayCloseCallback = nil
		cb()
	end
	self.OverlayCatcher.Visible = false
end

-- Bir anchor (buton) alt1na, HER ^EY0N ÜSTÜNDE aç1lan panel olu_turur (fade+slide ile)
function Hub:OpenOverlayAt(anchor, width, buildFn, onClose)
	self:CloseOverlay()
	self.OverlayCatcher.Visible = true

	local pos  = anchor.AbsolutePosition
	local size = anchor.AbsoluteSize
	local yPos = pos.Y + size.Y + 4

	local content = Create("Frame", {
		BackgroundColor3 = Theme.Elevated,
		Position = UDim2.fromOffset(pos.X, yPos - 6),
		Size = UDim2.fromOffset(width or size.X, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		BackgroundTransparency = 1,
		ZIndex = 1001,
		Parent = self.Overlay,
	}, { Corner(6), Stroke(Theme.Stroke, 1) })
	local st = content:FindFirstChildOfClass("UIStroke")
	if st then st.Transparency = 1 end

	buildFn(content)
	self._overlayContent = content
	self._overlayCloseCallback = onClose

	Tween(content, { Position = UDim2.fromOffset(pos.X, yPos), BackgroundTransparency = 0 }, 0.15)
	if st then Tween(st, { Transparency = 0 }, 0.15) end

	return content
end

-- Tüm panelleri fade + slide animasyonu ile açar/kapat1r (K tu_u)
function Hub:_AnimatePanelsVisible(visible)
	for _, panel in ipairs(self.Panels) do
		local header = panel:FindFirstChild("Header")
		if visible then
			panel.Visible = true
			panel.Position = UDim2.new(panel.Position.X.Scale, panel.Position.X.Offset, 0, 0)
			panel.BackgroundTransparency = 1
			if header then header.BackgroundTransparency = 1 end
			Tween(panel, {
				Position = UDim2.new(panel.Position.X.Scale, panel.Position.X.Offset, 0, 14),
				BackgroundTransparency = self.PanelTransparency,
			}, 0.22, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
			if header then
				Tween(header, { BackgroundTransparency = self.PanelTransparency }, 0.22)
			end
		else
			Tween(panel, {
				Position = UDim2.new(panel.Position.X.Scale, panel.Position.X.Offset, 0, 0),
				BackgroundTransparency = 1,
			}, 0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.In)
			if header then
				Tween(header, { BackgroundTransparency = 1 }, 0.18)
			end
			task.delay(0.18, function() panel.Visible = false end)
		end
	end
	if not visible then self:CloseOverlay() end
end

--================================================================
-- WINDOW (kategori panelleri = "tab")
--================================================================
Hub._WindowMeta = {}
local WindowMeta = Hub._WindowMeta
WindowMeta.__index = WindowMeta

function WindowMeta:CreateTab(name, config)
	config = config or {}
	local Window = self
	local Hub = Window.Hub
	local width = config.Width or 190

	-- Panel sadan sola doru yerle_ir
	local Panel = Create("Frame", {
		Name = name,
		BackgroundColor3 = Theme.Panel,
		BackgroundTransparency = Hub.PanelTransparency,
		Size = UDim2.fromOffset(width, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		Position = UDim2.new(1, -(Window._nextRight + width), 0, 14),
		ZIndex = 2,
		Parent = Window.PanelLayer,
	}, { Corner(6), Stroke() })
	Window._nextRight += width + 10

	local Header = Create("TextButton", {
		Name = "Header",
		Text = "  " .. name,
		Font = Theme.FontBold,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		BackgroundColor3 = Theme.Header,
		BackgroundTransparency = Hub.PanelTransparency,
		AutoButtonColor = false,
		Size = UDim2.new(1, 0, 0, 30),
		ZIndex = 2,
		Parent = Panel,
	}, { Corner(6) })
	-- header'1n alt kö_elerini kare yapan maske
	Create("Frame", {
		Size = UDim2.new(1, 0, 0, 8),
		Position = UDim2.new(0, 0, 1, -8),
		BackgroundColor3 = Theme.Header,
		BackgroundTransparency = Hub.PanelTransparency,
		BorderSizePixel = 0,
		ZIndex = 2,
		Parent = Header,
	})
	-- accent _erit (sol kenar)
	Create("Frame", {
		Size = UDim2.new(0, 3, 1, -8),
		Position = UDim2.fromOffset(0, 4),
		BackgroundColor3 = Theme.Accent,
		ZIndex = 2,
		Parent = Header,
	}, { Corner(2) })

	MakeDraggable(Header, Panel)

	local Body = Create("Frame", {
		Name = "Body",
		BackgroundTransparency = 1,
		Size = UDim2.new(1, 0, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		Position = UDim2.fromOffset(0, 30),
		ZIndex = 2,
		Parent = Panel,
	})
	Create("UIPadding", {
		PaddingTop = UDim.new(0, 6), PaddingBottom = UDim.new(0, 6),
		PaddingLeft = UDim.new(0, 6), PaddingRight = UDim.new(0, 6),
		Parent = Body,
	})
	Create("UIListLayout", {
		Padding = UDim.new(0, 4),
		SortOrder = Enum.SortOrder.LayoutOrder,
		Parent = Body,
	})

	table.insert(Hub.Panels, Panel)

	-- panel ilk olu_turulduunda fade-in ile belirir
	do
		local homeTransparency = Panel.BackgroundTransparency
		local headerHomeTransparency = Header.BackgroundTransparency
		Panel.BackgroundTransparency = 1
		Header.BackgroundTransparency = 1
		Tween(Panel, { BackgroundTransparency = homeTransparency }, 0.3)
		Tween(Header, { BackgroundTransparency = headerHomeTransparency }, 0.3)
	end

	local Tab = setmetatable({
		Window = Window,
		Hub = Hub,
		Panel = Panel,
		Page = Body,
		_order = 0,
	}, { __index = Hub._TabMeta })

	Window.Tabs[name] = Tab
	return Tab
end

--================================================================
-- ORTAK YARDIMCILAR
--================================================================
local ROW_HEIGHT = 30

local function RowLabel(parent, text, width)
	return Create("TextLabel", {
		Text = text,
		Font = Theme.Font,
		TextSize = 12,
		TextColor3 = Theme.Text,
		BackgroundTransparency = 1,
		Position = UDim2.fromOffset(8, 0),
		Size = UDim2.new(0, width or 100, 1, 0),
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		ZIndex = 3,
		Parent = parent,
	})
end

--================================================================
-- TAB (elementler)
--================================================================
Hub._TabMeta = {}
local TabMeta = Hub._TabMeta
TabMeta.__index = TabMeta

-- Elementlerin kod içinde çar1ld11 s1rayla dizilmesini salar
function TabMeta:_NextOrder()
	self._order = self._order + 1
	return self._order
end

-- Her yeni element eklendiinde hafif fade+slide ile beliriyor
local function AnimateRowIn(row)
	local homeT = row.BackgroundTransparency
	local homePos = row.Position
	row.BackgroundTransparency = 1
	row.Position = UDim2.new(homePos.X.Scale, homePos.X.Offset, homePos.Y.Scale, homePos.Y.Offset + 8)
	Tween(row, {
		BackgroundTransparency = homeT,
		Position = homePos,
	}, 0.22, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
end

-- 1) LABEL ------------------------------------------------------
function TabMeta:CreateLabel(text)
	local Row = Create("Frame", {
		BackgroundTransparency = 1,
		Size = UDim2.new(1, 0, 0, 20),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	})
	RowLabel(Row, text, 250)
	return Row
end

-- 2) BUTTON -------------------------------------------------------
function TabMeta:CreateButton(config)
	config = config or {}
	local Row = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Button", 130)
	Create("TextLabel", {
		Text = "¶",
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(20, ROW_HEIGHT),
		Position = UDim2.new(1, -26, 0, 0),
		ZIndex = 3,
		Parent = Row,
	})
	Row.MouseEnter:Connect(function() Tween(Row, { BackgroundColor3 = Theme.RowHover }, 0.1) end)
	Row.MouseLeave:Connect(function() Tween(Row, { BackgroundColor3 = Theme.Row }, 0.1) end)
	Row.MouseButton1Click:Connect(function()
		Tween(Row, { BackgroundColor3 = Theme.Accent }, 0.08)
		task.delay(0.1, function() Tween(Row, { BackgroundColor3 = Theme.Row }, 0.2) end)
		if config.Callback then
			local ok, err = pcall(config.Callback)
			if not ok then warn("[Hub] Button callback hata:", err) end
		end
	end)
	AnimateRowIn(Row)
	return Row
end

-- 3) TOGGLE -------------------------------------------------------
function TabMeta:CreateToggle(config)
	config = config or {}
	local Hub = self.Hub
	local state = config.Default or false

	local Row = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Toggle", 120)
	AddHover(Row)

	local Switch = Create("Frame", {
		Size = UDim2.fromOffset(34, 18),
		Position = UDim2.new(1, -42, 0.5, -9),
		BackgroundColor3 = state and Theme.Accent or Theme.Elevated,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(9) })
	local Dot = Create("Frame", {
		Size = UDim2.fromOffset(14, 14),
		Position = state and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7),
		BackgroundColor3 = Theme.Text,
		ZIndex = 3,
		Parent = Switch,
	}, { Corner(7) })

	local function apply()
		Tween(Switch, { BackgroundColor3 = state and Theme.Accent or Theme.Elevated }, 0.15)
		Tween(Dot, { Position = state and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7) }, 0.15)
		if config.Flag then Hub.Flags[config.Flag] = state end
		if config.Callback then
			local ok, err = pcall(config.Callback, state)
			if not ok then warn("[Hub] Toggle callback hata:", err) end
		end
	end

	Row.MouseButton1Click:Connect(function() state = not state apply() end)
	if config.Flag then Hub.Flags[config.Flag] = state end

	AnimateRowIn(Row)
	return { Set = function(_, v) state = v apply() end, Get = function() return state end }
end

-- 4) CHECKBOX -------------------------------------------------------
function TabMeta:CreateCheckbox(config)
	config = config or {}
	local Hub = self.Hub
	local state = config.Default or false

	local Row = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Checkbox", 130)
	AddHover(Row)

	local Box = Create("Frame", {
		Size = UDim2.fromOffset(18, 18),
		Position = UDim2.new(1, -30, 0.5, -9),
		BackgroundColor3 = Theme.Elevated,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(4), Stroke() })
	local Check = Create("TextLabel", {
		Text = "",
		Font = Theme.FontBold,
		TextSize = 13,
		TextColor3 = Theme.Text,
		BackgroundTransparency = 1,
		Size = UDim2.fromScale(1, 1),
		TextTransparency = state and 0 or 1,
		ZIndex = 3,
		Parent = Box,
	})

	local function apply()
		Tween(Box, { BackgroundColor3 = state and Theme.Accent or Theme.Elevated }, 0.15)
		Tween(Check, { TextTransparency = state and 0 or 1 }, 0.15)
		if config.Flag then Hub.Flags[config.Flag] = state end
		if config.Callback then
			local ok, err = pcall(config.Callback, state)
			if not ok then warn("[Hub] Checkbox callback hata:", err) end
		end
	end

	Row.MouseButton1Click:Connect(function() state = not state apply() end)
	if config.Flag then Hub.Flags[config.Flag] = state end

	AnimateRowIn(Row)
	return { Set = function(_, v) state = v apply() end, Get = function() return state end }
end

-- 5) SLIDER -------------------------------------------------------
function TabMeta:CreateSlider(config)
	config = config or {}
	local Hub = self.Hub
	local Min, Max = config.Min or 0, config.Max or 100
	local value = math.clamp(config.Default or Min, Min, Max)

	local Row = Create("Frame", {
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, 40),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Slider", 110)
	local ValueLabel = Create("TextLabel", {
		Text = tostring(value),
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(40, 18),
		Position = UDim2.new(1, -46, 0, 2),
		TextXAlignment = Enum.TextXAlignment.Right,
		ZIndex = 3,
		Parent = Row,
	})

	local Bar = Create("Frame", {
		Size = UDim2.new(1, -16, 0, 5),
		Position = UDim2.new(0, 8, 1, -12),
		BackgroundColor3 = Theme.Elevated,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(3) })
	local Fill = Create("Frame", {
		Size = UDim2.new((value - Min) / (Max - Min), 0, 1, 0),
		BackgroundColor3 = Theme.Accent,
		ZIndex = 3,
		Parent = Bar,
	}, { Corner(3) })

	local dragging = false
	local function setFromX(x)
		local rel = math.clamp((x - Bar.AbsolutePosition.X) / Bar.AbsoluteSize.X, 0, 1)
		value = math.floor(Min + (Max - Min) * rel)
		Fill.Size = UDim2.new(rel, 0, 1, 0)
		ValueLabel.Text = tostring(value)
		if config.Flag then Hub.Flags[config.Flag] = value end
		if config.Callback then
			local ok, err = pcall(config.Callback, value)
			if not ok then warn("[Hub] Slider callback hata:", err) end
		end
	end

	Bar.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			setFromX(input.Position.X)
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			setFromX(input.Position.X)
		end
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = false
		end
	end)

	if config.Flag then Hub.Flags[config.Flag] = value end

	AnimateRowIn(Row)
	return {
		Set = function(_, v) setFromX(Bar.AbsolutePosition.X + ((v - Min) / (Max - Min)) * Bar.AbsoluteSize.X) end,
		Get = function() return value end,
	}
end

-- 6) DROPDOWN (HER ^EY0N ÜSTÜNDE aç1l1r - global overlay) ---------
function TabMeta:CreateDropdown(config)
	config = config or {}
	local Hub = self.Hub
	local options = config.Options or {}
	local selected = config.Default or options[1]

	local Row = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Dropdown", 90)
	AddHover(Row)

	local SelectedLabel = Create("TextLabel", {
		Text = tostring(selected),
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(80, ROW_HEIGHT),
		Position = UDim2.new(1, -96, 0, 0),
		TextXAlignment = Enum.TextXAlignment.Right,
		TextTruncate = Enum.TextTruncate.AtEnd,
		ZIndex = 3,
		Parent = Row,
	})
	Create("TextLabel", {
		Text = "¼",
		Font = Theme.Font,
		TextSize = 9,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(14, ROW_HEIGHT),
		Position = UDim2.new(1, -16, 0, 0),
		ZIndex = 3,
		Parent = Row,
	})

	local function fireCallback()
		if config.Flag then Hub.Flags[config.Flag] = selected end
		if config.Callback then
			local ok, err = pcall(config.Callback, selected)
			if not ok then warn("[Hub] Dropdown callback hata:", err) end
		end
	end

	Row.MouseButton1Click:Connect(function()
		Hub:OpenOverlayAt(Row, Row.AbsoluteSize.X, function(content)
			Create("UIListLayout", { Parent = content })
			for _, opt in ipairs(options) do
				local OptBtn = Create("TextButton", {
					Text = "  " .. tostring(opt),
					Font = Theme.Font,
					TextSize = 12,
					TextColor3 = (opt == selected) and Theme.Accent or Theme.Text,
					TextXAlignment = Enum.TextXAlignment.Left,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 0, 26),
					ZIndex = 1002,
					Parent = content,
				})
				OptBtn.MouseEnter:Connect(function() OptBtn.BackgroundTransparency = 0.9 OptBtn.BackgroundColor3 = Theme.Accent end)
				OptBtn.MouseLeave:Connect(function() OptBtn.BackgroundTransparency = 1 end)
				OptBtn.MouseButton1Click:Connect(function()
					selected = opt
					SelectedLabel.Text = tostring(opt)
					fireCallback()
					Hub:CloseOverlay()
				end)
			end
		end)
	end)

	if config.Flag then Hub.Flags[config.Flag] = selected end

	AnimateRowIn(Row)
	return {
		Set = function(_, v) selected = v SelectedLabel.Text = tostring(v) end,
		Get = function() return selected end,
		Refresh = function(_, newOptions) options = newOptions end,
	}
end

-- 7) TOGGLEDROPDOWN (aç/kapa switch + tek seçim dropdown, ayn1 sat1rda) --
function TabMeta:CreateToggleDropdown(config)
	config = config or {}
	local Hub = self.Hub
	local options = config.Options or {}
	local selected = config.Default or options[1]
	local enabled = config.Enabled or false

	local Row = Create("Frame", {
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "ToggleDropdown", 74)
	AddHover(Row)

	local Switch = Create("Frame", {
		Size = UDim2.fromOffset(28, 16),
		Position = UDim2.new(0, 84, 0.5, -8),
		BackgroundColor3 = enabled and Theme.Accent or Theme.Elevated,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(8) })
	local Dot = Create("Frame", {
		Size = UDim2.fromOffset(12, 12),
		Position = enabled and UDim2.new(1, -14, 0.5, -6) or UDim2.new(0, 2, 0.5, -6),
		BackgroundColor3 = Theme.Text,
		ZIndex = 3,
		Parent = Switch,
	}, { Corner(6) })
	local SwitchBtn = Create("TextButton", {
		Text = "", BackgroundTransparency = 1,
		Size = UDim2.fromOffset(28, ROW_HEIGHT),
		Position = UDim2.new(0, 84, 0, 0),
		ZIndex = 4,
		Parent = Row,
	})

	local DropdownBtn = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundTransparency = 1,
		Size = UDim2.new(1, -122, 1, 0),
		Position = UDim2.fromOffset(120, 0),
		ZIndex = 3,
		Parent = Row,
	})
	local SelectedLabel = Create("TextLabel", {
		Text = tostring(selected),
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromScale(1, 1),
		TextXAlignment = Enum.TextXAlignment.Right,
		TextTruncate = Enum.TextTruncate.AtEnd,
		ZIndex = 3,
		Parent = DropdownBtn,
	})

	local function fireCallback()
		if config.Flag then Hub.Flags[config.Flag] = { Enabled = enabled, Value = selected } end
		if config.Callback then
			local ok, err = pcall(config.Callback, enabled, selected)
			if not ok then warn("[Hub] ToggleDropdown callback hata:", err) end
		end
	end

	SwitchBtn.MouseButton1Click:Connect(function()
		enabled = not enabled
		Tween(Switch, { BackgroundColor3 = enabled and Theme.Accent or Theme.Elevated }, 0.15)
		Tween(Dot, { Position = enabled and UDim2.new(1, -14, 0.5, -6) or UDim2.new(0, 2, 0.5, -6) }, 0.15)
		fireCallback()
	end)

	DropdownBtn.MouseButton1Click:Connect(function()
		if not enabled then return end
		Hub:OpenOverlayAt(Row, 140, function(content)
			Create("UIListLayout", { Parent = content })
			for _, opt in ipairs(options) do
				local OptBtn = Create("TextButton", {
					Text = "  " .. tostring(opt),
					Font = Theme.Font,
					TextSize = 12,
					TextColor3 = (opt == selected) and Theme.Accent or Theme.Text,
					TextXAlignment = Enum.TextXAlignment.Left,
					BackgroundTransparency = 1,
					Size = UDim2.new(1, 0, 0, 26),
					ZIndex = 1002,
					Parent = content,
				})
				OptBtn.MouseEnter:Connect(function() OptBtn.BackgroundTransparency = 0.9 OptBtn.BackgroundColor3 = Theme.Accent end)
				OptBtn.MouseLeave:Connect(function() OptBtn.BackgroundTransparency = 1 end)
				OptBtn.MouseButton1Click:Connect(function()
					selected = opt
					SelectedLabel.Text = tostring(opt)
					fireCallback()
					Hub:CloseOverlay()
				end)
			end
		end)
	end)

	if config.Flag then Hub.Flags[config.Flag] = { Enabled = enabled, Value = selected } end

	AnimateRowIn(Row)
	return {
		Set = function(_, v) selected = v SelectedLabel.Text = tostring(v) end,
		SetEnabled = function(_, e)
			enabled = e
			Switch.BackgroundColor3 = e and Theme.Accent or Theme.Elevated
			Dot.Position = e and UDim2.new(1, -14, 0.5, -6) or UDim2.new(0, 2, 0.5, -6)
		end,
		Get = function() return enabled, selected end,
		Refresh = function(_, newOptions) options = newOptions end,
	}
end

-- 8) COLORPICKER (HER ^EY0N ÜSTÜNDE aç1l1r) -------------------------
function TabMeta:CreateColorPicker(config)
	config = config or {}
	local Hub = self.Hub
	local color = config.Default or Color3.fromRGB(150, 100, 255)

	local Row = Create("TextButton", {
		Text = "",
		AutoButtonColor = false,
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Color", 130)
	AddHover(Row)

	local Preview = Create("Frame", {
		Size = UDim2.fromOffset(20, 20),
		Position = UDim2.new(1, -30, 0.5, -10),
		BackgroundColor3 = color,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(4), Stroke() })

	local function fireCallback()
		if config.Flag then Hub.Flags[config.Flag] = color end
		if config.Callback then
			local ok, err = pcall(config.Callback, color)
			if not ok then warn("[Hub] ColorPicker callback hata:", err) end
		end
	end

	Row.MouseButton1Click:Connect(function()
		Hub:OpenOverlayAt(Row, 170, function(content)
			Create("UIPadding", {
				PaddingTop = UDim.new(0, 10), PaddingBottom = UDim.new(0, 10),
				PaddingLeft = UDim.new(0, 10), PaddingRight = UDim.new(0, 10),
				Parent = content,
			})
			local HueBar = Create("Frame", {
				Size = UDim2.new(1, 0, 0, 16),
				ZIndex = 1002,
				Parent = content,
			}, { Corner(8) })
			Create("UIGradient", {
				Color = ColorSequence.new({
					ColorSequenceKeypoint.new(0, Color3.fromRGB(255,0,0)),
					ColorSequenceKeypoint.new(0.17, Color3.fromRGB(255,255,0)),
					ColorSequenceKeypoint.new(0.33, Color3.fromRGB(0,255,0)),
					ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0,255,255)),
					ColorSequenceKeypoint.new(0.67, Color3.fromRGB(0,0,255)),
					ColorSequenceKeypoint.new(0.83, Color3.fromRGB(255,0,255)),
					ColorSequenceKeypoint.new(1, Color3.fromRGB(255,0,0)),
				}),
				Parent = HueBar,
			})

			local dragging = false
			local function setFromX(x)
				local rel = math.clamp((x - HueBar.AbsolutePosition.X) / HueBar.AbsoluteSize.X, 0, 1)
				color = Color3.fromHSV(rel, 1, 1)
				Preview.BackgroundColor3 = color
				fireCallback()
			end
			HueBar.InputBegan:Connect(function(input)
				if input.UserInputType == Enum.UserInputType.MouseButton1 then
					dragging = true
					setFromX(input.Position.X)
				end
			end)
			UserInputService.InputChanged:Connect(function(input)
				if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
					setFromX(input.Position.X)
				end
			end)
			UserInputService.InputEnded:Connect(function(input)
				if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
			end)
		end)
	end)

	if config.Flag then Hub.Flags[config.Flag] = color end

	AnimateRowIn(Row)
	return { Set = function(_, c) color = c Preview.BackgroundColor3 = c end, Get = function() return color end }
end

-- 9) KEYBIND -------------------------------------------------------
function TabMeta:CreateKeybind(config)
	config = config or {}
	local Hub = self.Hub
	local bound = config.Default or Enum.KeyCode.Unknown
	local listening = false

	local Row = Create("Frame", {
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Keybind", 110)
	AddHover(Row)

	local KeyBtn = Create("TextButton", {
		Text = "[" .. bound.Name .. "]",
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(70, ROW_HEIGHT),
		Position = UDim2.new(1, -76, 0, 0),
		ZIndex = 3,
		Parent = Row,
	})

	KeyBtn.MouseButton1Click:Connect(function()
		listening = true
		KeyBtn.Text = "[...]"
	end)

	UserInputService.InputBegan:Connect(function(input, gpe)
		if listening and input.UserInputType == Enum.UserInputType.Keyboard then
			bound = input.KeyCode
			KeyBtn.Text = "[" .. bound.Name .. "]"
			listening = false
			if config.Flag then Hub.Flags[config.Flag] = bound end
		elseif not gpe and not listening and input.KeyCode == bound and Hub.GuiVisible ~= nil then
			if config.Callback then
				local ok, err = pcall(config.Callback)
				if not ok then warn("[Hub] Keybind callback hata:", err) end
			end
		end
	end)

	if config.Flag then Hub.Flags[config.Flag] = bound end

	if config.ShowInList ~= false then
		local Entry = Create("TextLabel", {
			Text = (config.Name or "Keybind") .. " [" .. bound.Name .. "]",
			Font = Theme.Font,
			TextSize = 11,
			TextColor3 = Theme.Text,
			BackgroundTransparency = 1,
			Size = UDim2.new(1, 0, 0, 16),
			TextXAlignment = Enum.TextXAlignment.Left,
			ZIndex = 500,
			Parent = Hub.KeybindListFrame,
		})
		table.insert(Hub._KeybindEntries, Entry)
	end

	AnimateRowIn(Row)
	return { Set = function(_, k) bound = k KeyBtn.Text = "[" .. k.Name .. "]" end, Get = function() return bound end }
end

-- 10) TEXTBOX -------------------------------------------------------
function TabMeta:CreateTextbox(config)
	config = config or {}
	local Hub = self.Hub

	local Row = Create("Frame", {
		BackgroundColor3 = Theme.Row,
		Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
		LayoutOrder = self:_NextOrder(),
		ZIndex = 3,
		Parent = self.Page,
	}, { Corner(4) })
	RowLabel(Row, config.Name or "Textbox", 80)
	AddHover(Row)

	local Box = Create("TextBox", {
		Text = config.Default or "",
		PlaceholderText = config.Placeholder or "...",
		Font = Theme.Font,
		TextSize = 11,
		TextColor3 = Theme.Text,
		PlaceholderColor3 = Theme.SubText,
		BackgroundColor3 = Theme.Elevated,
		Size = UDim2.fromOffset(90, 22),
		Position = UDim2.new(1, -98, 0.5, -11),
		ClearTextOnFocus = false,
		ZIndex = 3,
		Parent = Row,
	}, { Corner(4) })
	Create("UIPadding", { PaddingLeft = UDim.new(0, 6), Parent = Box })

	Box.FocusLost:Connect(function(enterPressed)
		if config.Flag then Hub.Flags[config.Flag] = Box.Text end
		if config.Callback then
			local ok, err = pcall(config.Callback, Box.Text, enterPressed)
			if not ok then warn("[Hub] Textbox callback hata:", err) end
		end
	end)

	if config.Flag then Hub.Flags[config.Flag] = Box.Text end

	AnimateRowIn(Row)
	return { Set = function(_, t) Box.Text = t end, Get = function() return Box.Text end }
end

--================================================================
-- 11) NOTIFICATION
--================================================================
function Hub:Notify(config)
	config = config or {}
	local Title = config.Title or "Bildirim"
	local Content = config.Content or ""
	local Duration = config.Duration or 4

	local Notif = Create("Frame", {
		BackgroundColor3 = Theme.Panel,
		Size = UDim2.new(1, 0, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		ZIndex = 500,
		Parent = self.NotifyHolder,
	}, { Corner(6), Stroke() })
	Create("UIPadding", {
		PaddingTop = UDim.new(0, 10), PaddingBottom = UDim.new(0, 10),
		PaddingLeft = UDim.new(0, 12), PaddingRight = UDim.new(0, 12),
		Parent = Notif,
	})
	Create("UIListLayout", { Padding = UDim.new(0, 2), Parent = Notif })

	Create("TextLabel", {
		Text = Title, Font = Theme.FontBold, TextSize = 13, TextColor3 = Theme.Text,
		BackgroundTransparency = 1, Size = UDim2.new(1, 0, 0, 18),
		TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 500, Parent = Notif,
	})
	Create("TextLabel", {
		Text = Content, Font = Theme.Font, TextSize = 12, TextColor3 = Theme.SubText,
		BackgroundTransparency = 1, Size = UDim2.new(1, 0, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y, TextWrapped = true,
		TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 500, Parent = Notif,
	})

	Notif.BackgroundTransparency = 1
	for _, c in ipairs(Notif:GetDescendants()) do
		if c:IsA("TextLabel") then c.TextTransparency = 1 end
	end
	Tween(Notif, { BackgroundTransparency = 0 }, 0.2)
	for _, c in ipairs(Notif:GetDescendants()) do
		if c:IsA("TextLabel") then Tween(c, { TextTransparency = 0 }, 0.2) end
	end

	task.delay(Duration, function()
		Tween(Notif, { BackgroundTransparency = 1 }, 0.3)
		for _, c in ipairs(Notif:GetDescendants()) do
			if c:IsA("TextLabel") then Tween(c, { TextTransparency = 1 }, 0.3) end
		end
		task.wait(0.3)
		Notif:Destroy()
	end)
end

--================================================================
-- 12) WATERMARK
--================================================================
function Hub:CreateWatermark(config)
	config = config or {}
	local Title = config.Title or "Hub"

	local Watermark = Create("Frame", {
		Name = "Watermark",
		Size = UDim2.fromOffset(0, 24),
		AutomaticSize = Enum.AutomaticSize.X,
		Position = UDim2.fromOffset(14, 14),
		BackgroundColor3 = Theme.Panel,
		ZIndex = 500,
		Parent = self.ScreenGui,
	}, { Corner(5), Stroke() })
	Create("UIPadding", { PaddingLeft = UDim.new(0, 10), PaddingRight = UDim.new(0, 10), Parent = Watermark })

	local Label = Create("TextLabel", {
		Text = Title, Font = Theme.FontBold, TextSize = 12, TextColor3 = Theme.Text,
		BackgroundTransparency = 1, Size = UDim2.fromScale(0, 1),
		AutomaticSize = Enum.AutomaticSize.X, ZIndex = 500, Parent = Watermark,
	})

	self.Watermark = Watermark
	self.WatermarkLabel = Label

	self.Connections.WatermarkLoop = RunService.Heartbeat:Connect(function()
		local fps = math.floor(1 / RunService.Heartbeat:Wait())
		local ping = 0
		local ok, pingStat = pcall(function()
			return Stats.Network.ServerStatsItem["Data Ping"]:GetValue()
		end)
		if ok then ping = math.floor(pingStat) end
		Label.Text = string.format("%s  |  %d fps  |  %d ms", Title, fps, ping)
	end)

	return Watermark
end

--================================================================
-- LOADING SCREEN (gerçek yükleme ekran1 - progress bar + spinner)
--================================================================
function Hub:ShowLoadingScreen(config)
	config = config or {}
	local Title = config.Title or "Yükleniyor"
	local SubTitle = config.SubTitle or ""

	local LoadingGui = Create("ScreenGui", {
		Name = "HubLoadingScreen",
		ResetOnSpawn = false,
		IgnoreGuiInset = true,
		DisplayOrder = 100,
		Parent = PlayerGui,
	})

	local Background = Create("Frame", {
		Size = UDim2.fromScale(1, 1),
		BackgroundColor3 = Theme.Background,
		ZIndex = 1,
		Parent = LoadingGui,
	})

	-- dönen spinner (halka)
	local Spinner = Create("Frame", {
		Size = UDim2.fromOffset(46, 46),
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, -70),
		BackgroundTransparency = 1,
		ZIndex = 2,
		Parent = Background,
	}, {
		Corner(999),
		Stroke(Theme.Accent, 4),
	})
	-- halkan1n bir k1sm1n1 _effaf yaparak "dönüyor" hissi veren maske
	local SpinnerStroke = Spinner:FindFirstChildOfClass("UIStroke")
	SpinnerStroke.Transparency = 0.35

	local Title1 = Create("TextLabel", {
		Text = Title,
		Font = Theme.FontBold,
		TextSize = 20,
		TextColor3 = Theme.Text,
		BackgroundTransparency = 1,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, -10),
		Size = UDim2.fromOffset(400, 26),
		ZIndex = 2,
		Parent = Background,
	})
	local Title2 = Create("TextLabel", {
		Text = SubTitle,
		Font = Theme.Font,
		TextSize = 13,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, 14),
		Size = UDim2.fromOffset(400, 20),
		ZIndex = 2,
		Parent = Background,
	})

	local BarBG = Create("Frame", {
		Size = UDim2.fromOffset(280, 6),
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, 44),
		BackgroundColor3 = Theme.Elevated,
		ZIndex = 2,
		Parent = Background,
	}, { Corner(3) })
	local Bar = Create("Frame", {
		Size = UDim2.new(0, 0, 1, 0),
		BackgroundColor3 = Theme.Accent,
		ZIndex = 2,
		Parent = BarBG,
	}, { Corner(3) })
	local PercentLabel = Create("TextLabel", {
		Text = "0%",
		Font = Theme.Font,
		TextSize = 12,
		TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, 64),
		Size = UDim2.fromOffset(280, 18),
		ZIndex = 2,
		Parent = Background,
	})

	-- spinner dönme animasyonu
	local spinning = true
	task.spawn(function()
		while spinning do
			Spinner.Rotation = (Spinner.Rotation + 5) % 360
			task.wait()
		end
	end)

	self.LoadingScreen = LoadingGui

	return {
		-- 0-100 aras1 ilerleme yüzdesi ayarlar
		SetProgress = function(_, pct, statusText)
			pct = math.clamp(pct, 0, 100)
			Tween(Bar, { Size = UDim2.new(pct / 100, 0, 1, 0) }, 0.2)
			PercentLabel.Text = math.floor(pct) .. "%"
			if statusText then Title2.Text = statusText end
		end,
		-- ekran1 kapat1r (fade-out)
		Hide = function(_, callback)
			spinning = false
			Tween(Background, { BackgroundTransparency = 1 }, 0.5)
			for _, c in ipairs(Background:GetDescendants()) do
				if c:IsA("TextLabel") then Tween(c, { TextTransparency = 1 }, 0.5) end
				if c:IsA("UIStroke") then Tween(c, { Transparency = 1 }, 0.5) end
			end
			task.wait(0.5)
			LoadingGui:Destroy()
			if callback then callback() end
		end,
	}
end

--================================================================
-- KEY SYSTEM (opsiyonel - hub çal1_madan önce anahtar sorar)
--================================================================
-- config.Keys = { "key1", "key2" }  -> herhangi biri girilirse geçer
-- config.Validate = function(key) return true/false end  -> kendi kontrolün
-- config.OnSuccess = function() ... end  -> doru key girilince çal1_1r
function Hub:ShowKeySystem(config)
	config = config or {}
	local validKeys = config.Keys or (config.Key and { config.Key })
	local ValidateFn = config.Validate

	local KeyGui = Create("ScreenGui", {
		Name = "HubKeySystem",
		ResetOnSpawn = false,
		IgnoreGuiInset = true,
		DisplayOrder = 90,
		Parent = PlayerGui,
	})

	local Background = Create("Frame", {
		Size = UDim2.fromScale(1, 1),
		BackgroundColor3 = Theme.Background,
		BackgroundTransparency = 1,
		ZIndex = 1,
		Parent = KeyGui,
	})
	Tween(Background, { BackgroundTransparency = 0.05 }, 0.35)

	-- Ta_1nabilir (draggable) dikdörtgen kutu
	local Box = Create("Frame", {
		Size = UDim2.fromOffset(320, 214),
		AnchorPoint = Vector2.new(0.5, 0.5),
		Position = UDim2.new(0.5, 0, 0.5, 20),
		BackgroundColor3 = Theme.Panel,
		BackgroundTransparency = 1,
		ZIndex = 2,
		Parent = Background,
	}, { Corner(8), Stroke() })
	Tween(Box, { Position = UDim2.new(0.5, 0, 0.5, 0), BackgroundTransparency = 0 }, 0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out)

	-- Üst bar: ba_l1k (sürükleme tutamac1) + kapatma (X) butonu
	local Header = Create("Frame", {
		Size = UDim2.new(1, 0, 0, 34),
		BackgroundColor3 = Theme.Header,
		ZIndex = 2,
		Parent = Box,
	}, { Corner(8) })
	Create("Frame", {
		Size = UDim2.new(1, 0, 0, 10),
		Position = UDim2.new(0, 0, 1, -10),
		BackgroundColor3 = Theme.Header,
		BorderSizePixel = 0,
		ZIndex = 2,
		Parent = Header,
	})
	Create("TextLabel", {
		Text = config.Title or "Key Giri_i",
		Font = Theme.FontBold, TextSize = 15, TextColor3 = Theme.Text,
		BackgroundTransparency = 1, Size = UDim2.new(1, -40, 1, 0),
		Position = UDim2.fromOffset(14, 0), TextXAlignment = Enum.TextXAlignment.Left,
		ZIndex = 2, Parent = Header,
	})

	-- Kapatma (X) butonu - çözemezse buradan ç1kabilir
	local CloseBtn = Create("TextButton", {
		Text = "×",
		Font = Theme.FontBold, TextSize = 20, TextColor3 = Theme.SubText,
		BackgroundTransparency = 1,
		Size = UDim2.fromOffset(34, 34),
		Position = UDim2.new(1, -34, 0, 0),
		ZIndex = 2, Parent = Header,
	})
	CloseBtn.MouseEnter:Connect(function() Tween(CloseBtn, { TextColor3 = Theme.Red }, 0.12) end)
	CloseBtn.MouseLeave:Connect(function() Tween(CloseBtn, { TextColor3 = Theme.SubText }, 0.12) end)
	CloseBtn.MouseButton1Click:Connect(function()
		Tween(Background, { BackgroundTransparency = 1 }, 0.25)
		Tween(Box, { BackgroundTransparency = 1 }, 0.25)
		task.wait(0.25)
		KeyGui:Destroy()
		if config.OnClose then
			local ok, err = pcall(config.OnClose)
			if not ok then warn("[Hub] KeySystem OnClose hata:", err) end
		end
	end)

	MakeDraggable(Header, Box)

	Create("TextLabel", {
		Text = config.SubTitle or "Devam etmek için key gir",
		Font = Theme.Font, TextSize = 12, TextColor3 = Theme.SubText,
		BackgroundTransparency = 1, Size = UDim2.new(1, 0, 0, 18),
		Position = UDim2.fromOffset(0, 44), ZIndex = 2, Parent = Box,
	})

	local Input = Create("TextBox", {
		PlaceholderText = "Key...",
		Text = "",
		Font = Theme.Font, TextSize = 13, TextColor3 = Theme.Text,
		PlaceholderColor3 = Theme.SubText,
		BackgroundColor3 = Theme.Elevated,
		Size = UDim2.new(1, -32, 0, 34), Position = UDim2.fromOffset(16, 68),
		ClearTextOnFocus = false, ZIndex = 2, Parent = Box,
	}, { Corner(5) })
	Create("UIPadding", { PaddingLeft = UDim.new(0, 10), Parent = Input })

	local ErrorLabel = Create("TextLabel", {
		Text = "",
		Font = Theme.Font, TextSize = 11, TextColor3 = Theme.Red,
		BackgroundTransparency = 1, Size = UDim2.new(1, -32, 0, 16),
		Position = UDim2.fromOffset(16, 106), ZIndex = 2, Parent = Box,
	})

	-- "Get Key" butonu - i_levini sen balayacaks1n (config.OnGetKey)
	local GetKeyBtn = Create("TextButton", {
		Text = "Get Key",
		AutoButtonColor = false,
		Font = Theme.FontBold, TextSize = 13, TextColor3 = Theme.Text,
		BackgroundColor3 = Theme.Elevated,
		Size = UDim2.new(1, -32, 0, 32), Position = UDim2.fromOffset(16, 126),
		ZIndex = 2, Parent = Box,
	}, { Corner(6), Stroke() })
	GetKeyBtn.MouseEnter:Connect(function() Tween(GetKeyBtn, { BackgroundColor3 = Theme.RowHover }, 0.1) end)
	GetKeyBtn.MouseLeave:Connect(function() Tween(GetKeyBtn, { BackgroundColor3 = Theme.Elevated }, 0.1) end)
	GetKeyBtn.MouseButton1Click:Connect(function()
		if config.OnGetKey then
			local ok, err = pcall(config.OnGetKey)
			if not ok then warn("[Hub] KeySystem OnGetKey hata:", err) end
		end
	end)

	local SubmitBtn = Create("TextButton", {
		Text = "Enter",
		AutoButtonColor = false,
		Font = Theme.FontBold, TextSize = 13, TextColor3 = Theme.Text,
		BackgroundColor3 = Theme.Accent,
		Size = UDim2.new(1, -32, 0, 36), Position = UDim2.fromOffset(16, 166),
		ZIndex = 2, Parent = Box,
	}, { Corner(6) })
	SubmitBtn.MouseEnter:Connect(function() Tween(SubmitBtn, { BackgroundColor3 = Theme.AccentDark or Theme.Accent }, 0.1) end)
	SubmitBtn.MouseLeave:Connect(function() Tween(SubmitBtn, { BackgroundColor3 = Theme.Accent }, 0.1) end)

	local function shake()
		local original = Box.Position
		for _, dx in ipairs({ 8, -8, 6, -6, 3, 0 }) do
			Tween(Box, { Position = UDim2.new(original.X.Scale, original.X.Offset + dx, original.Y.Scale, original.Y.Offset) }, 0.05)
			task.wait(0.05)
		end
		Box.Position = original
	end

	local function attemptValidate()
		local key = Input.Text
		local ok = false
		if ValidateFn then
			local success, result = pcall(ValidateFn, key)
			ok = success and result == true
		elseif validKeys then
			for _, k in ipairs(validKeys) do
				if k == key then ok = true break end
			end
		end

		if ok then
			ErrorLabel.Text = ""
			Tween(Background, { BackgroundTransparency = 1 }, 0.4)
			Tween(Box, { BackgroundTransparency = 1 }, 0.4)
			for _, c in ipairs(Box:GetDescendants()) do
				if c:IsA("TextLabel") or c:IsA("TextButton") then Tween(c, { TextTransparency = 1 }, 0.4) end
				if c:IsA("Frame") or c:IsA("TextBox") then Tween(c, { BackgroundTransparency = 1 }, 0.4) end
			end
			task.wait(0.4)
			KeyGui:Destroy()
			if config.OnSuccess then
				local okRun, err = pcall(config.OnSuccess)
				if not okRun then warn("[Hub] KeySystem OnSuccess hata:", err) end
			end
		else
			ErrorLabel.Text = "key is wrong"
			shake()
		end
	end

	SubmitBtn.MouseButton1Click:Connect(attemptValidate)
	Input.FocusLost:Connect(function(enterPressed)
		if enterPressed then attemptValidate() end
	end)

	return KeyGui
end


function Hub:CloseGui()
	for _, conn in pairs(self.Connections) do
		if typeof(conn) == "RBXScriptConnection" then conn:Disconnect() end
	end
	if self.ScreenGui then
		self.ScreenGui:Destroy()
	end
end

--================================================================
-- 15) EXAMPLE
--================================================================
function Hub:LoadExampleTab(Window)
	local Tab = Window:CreateTab("Example")

	Tab:CreateLabel("Tüm elementlerin örnei")
	Tab:CreateButton({ Name = "Button", Callback = function()
		Hub:Notify({ Title = "Button", Content = "Butona t1kland1!" })
	end })
	Tab:CreateToggle({ Name = "Toggle", Flag = "ExampleToggle" })
	Tab:CreateCheckbox({ Name = "Checkbox", Flag = "ExampleCheckbox" })
	Tab:CreateSlider({ Name = "Slider", Min = 0, Max = 100, Default = 50, Flag = "ExampleSlider" })
	Tab:CreateDropdown({ Name = "Dropdown", Options = { "Bir", "0ki", "Üç" }, Flag = "ExampleDropdown" })
	Tab:CreateToggleDropdown({ Name = "ToggleDD", Options = { "Mod A", "Mod B" }, Flag = "ExampleToggleDropdown" })
	Tab:CreateColorPicker({ Name = "Renk", Flag = "ExampleColor" })
	Tab:CreateKeybind({ Name = "Keybind", Default = Enum.KeyCode.G, Callback = function()
		Hub:Notify({ Title = "Keybind", Content = "G tu_una bas1ld1" })
	end })
	Tab:CreateTextbox({ Name = "Textbox", Placeholder = "yaz...", Flag = "ExampleText" })

	return Tab
end
print("Lunx > 1")
print("Lunx > 2")
print("Lunx > 3")
print("Lunx > 4")
print("Lunx > 5")
print("Lunx > 6")
print("Lunx > 7")
print("Lunx > 8")
print("Lunx > 9")
print("Lunx > 10")
print("Lunx > 11")
print("Lunx > 12")
print("Lunx > 13")
print("Lunx > 14")
print("Lunx > 15")
print("Lunx > 16")
print("Lunx > 17")
print("Lunx > 18")
print("Lunx > 19")
print("Lunx > 20")
print("Lunx > 21")
print("Lunx > Completed")
print("Lunx > Thank you using the LUNX SCRIPT :>")









print("Lunx > Assest loading.")









print("Lunx > ")
print(" ")
print("                                                                                                 ")
print("     W W     W    W W W   WW    W     W   WW   WW  W   ")
print("    TPPWQ     Q    QTPPWZW T]TPPPP]    Q     Q   QW  QZWT]   ")
print("    QQ     Q W QQ ZT] W    Q     Q   QTW Q ZT]    ")
print("    TPPQQ     QWQTPPQ  ZT]  ZPPPPQ    Q     Q   QQZWQ TW    ")
print("    Q  QWZTT]Q  Q   Q   Q    WZT]Q ZQT] W   ")
print("    ZP]  ZP]ZPPPPPP] ZPP]ZPP] ZP]  ZP]   ZP]   ZPPPPPP]    ZPPPPPP] ZPPPPP] ZP]  ZPPP]ZP]  ZP]   ")
print("                                                                                                 ")
print(" ")























-- 
--                                                                                                 
--     W W     W    W W W   WW    W     W   WW   WW  W   
--    TPPWQ     Q    QTPPWZW T]TPPPP]    Q     Q   QW  QZWT]   
--    QQ     Q W QQ ZT] W    Q     Q   QTW Q ZT]    
--    TPPQQ     QWQTPPQ  ZT]  ZPPPPQ    Q     Q   QQZWQ TW    
--    Q  QWZTT]Q  Q   Q   Q    WZT]Q ZQT] W   
--    ZP]  ZP]ZPPPPPP] ZPP]ZPP] ZP]  ZP]   ZP]   ZPPPPPP]    ZPPPPPP] ZPPPPP] ZP]  ZPPP]ZP]  ZP]   
--                                                                                                 
-- 














print("Lunx > 1x11")




local ReplicatedStorage = game:GetService("ReplicatedStorage")
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
local Player = localPlayer 
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
local LUNXCoroutine
local lighBitSpeedCoroutine
local lightbitpos = {}
local lightbitparts = {}
local lightbitcon
local lightbitcon2
local lightorbitcon
local bodyPositions = {}
local alignOrientations = {}
local skolko = "" 
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
local Player = game.Players.LocalPlayer
local U = loadstring(game:HttpGet("https://paste.ee/r/7X7NLEPB", true))()


print("Lunx > Lua its started!")

print("Lunx > A.lua")


--[[ U

local Utilities = {}
print("omg sigma")
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



return Utilities

]]




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
local LUNX 
local platforms = {}
local ownedToys = {}
local bombList = {}
_G.ToyToLoad = "BombMissile"
_G.MaxMissiles = 9
_G.LUNXDelay = _G.LUNXDelay or 0.1


print("Lunx > Settings")


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

print("Lunx > Settings")

local function getDescendantParts(descendantName)
    local parts = {}
    for _, descendant in ipairs(workspace.Map:GetDescendants()) do
        if descendant:IsA("Part") and descendant.Name == descendantName then
            table.insert(parts, descendant)
        end
    end
    return parts
end

print("Lunx > Settings")
local poisonHurtParts = getDescendantParts("PoisonHurtPart")
local paintPlayerParts = getDescendantParts("PaintPlayerPart")

print("Lunx > Settings")
local function updatePlayerList()
    playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(playerList, player.Name)
    end
end

print("Lunx > Settings")
local function onPlayerAdded(player)
    table.insert(playerList, player.Name)
end

print("Lunx > Settings")
local function onPlayerRemoving(player)
    for i, name in ipairs(playerList) do
        if name == player.Name then
            table.remove(playerList, i)
            break
        end
    end
end
-- Event balant1lar1 (onPlayerAdded ve onPlayerRemoving fonksiyonlar1n1n yukar1da tan1mland11n1 varsay1yoruz)
Players.PlayerAdded:Connect(onPlayerAdded)
Players.PlayerRemoving:Connect(onPlayerRemoving)

print("Lunx > Settings")
-- Oyuncaklar1 listeleme (ownedToys tablosunun yukar1da tan1mland11n1 varsay1yoruz)
local toysFolder = localPlayer:WaitForChild("PlayerGui")
    :WaitForChild("MenuGui")
    :WaitForChild("Menu")
    :WaitForChild("TabContents")
    :WaitForChild("Toys")
    :WaitForChild("Contents")

for _, v in ipairs(toysFolder:GetChildren()) do
    if v:IsA("GuiObject") and v.Name ~= "UIGridLayout" then
        ownedToys[v.Name] = true
    end
end

print("Lunx > Settings")
-- En yak1n oyuncuyu bulma fonksiyonu (Hatalar giderildi ve optimize edildi)
local function getNearestPlayer()
    local nearestPlayer = nil
    local nearestDistance = math.huge

    -- Kendi karakterimizin ve HumanoidRootPart'1m1z1n varl11n1 kontrol ediyoruz
    local localCharacter = localPlayer.Character
    local localHRP = localCharacter and localCharacter:FindFirstChild("HumanoidRootPart")

    if not localHRP then 
        return nil -- Karakterimiz henüz yüklenmediyse aramay1 pas geç
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character then
            local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
            if targetHRP then
                local distance = (localHRP.Position - targetHRP.Position).Magnitude
                if distance < nearestDistance then
                    nearestDistance = distance
                    nearestPlayer = player
                end
            end
        end
    end

    return nearestPlayer
end

print("Lunx > Settings")
local function cleanupConnections(connectionTable)
    for _, connection in ipairs(connectionTable) do
        connection:Disconnect()
    end
    connectionTable = {}
end

print("Lunx > Settings")

local function spawnItem(itemName, position, orientation)
    task.spawn(function()
        local cframe = CFrame.new(position)
        local rotation = Vector3.new(0, 90, 0)
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    end)
end

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings H")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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

print("Lunx > Settings")
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
        Rayfield:Notify({Title = "Error",Content = "No anchored parts found",Duration = 5,})
    else
        Rayfield:Notify({Title = "Success",Content = "Compiled "..#anchoredParts.." Toys together",Duration = 5,})
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
print("Lunx > Settings")

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
                                        print("yoyoyo set and ready")
                                    end
                                end
                            end
                        end
                    end)()
                end
            end
        end)
        task.wait(0.02)
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
                local myCharacter = localPlayer.Character 
                for _, player in pairs(Players:GetPlayers()) do
                    pcall(function()
                        if player.Character and player.Character ~= myCharacter and myCharacter:FindFirstChild("Head") then
                            if bananaPeel and player.Character:FindFirstChild("HumanoidRootPart") then
                                bananaPeel.Position = player.Character.HumanoidRootPart.Position
                                bodyPosition.Position = myCharacter.Head.Position + Vector3.new(0, 600, 0)
                                task.wait()
                            end
                        end
                    end)
                end   
                task.wait()
            end
        end)
        if not success then
            warn("Error in ragdollAll: " .. tostring(err))
        end
        task.wait()
    end
end

local function reloadMissile(bool)
    if bool then
        if not ownedToys[_G.ToyToLoad] then
            Rayfield:Notify({
                Title = "Missing toy",
                Content = "You do not own the ".._G.ToyToLoad.." toy.",
                Duration = 3,
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
                            local connection
                            connection = child.DescendantAdded:Connect(function(descendant)
                                if descendant.Name == "PartOwner" then
                                    if descendant.Value ~= localPlayer.Name then
                                        DestroyT(child)
                                        connection:Disconnect()
                                    end
                                end
                            end)
                            -- DÜZELT0LD0: Yaz1m hatas1 giderildi (connectio -> connection)
                            Debris:AddItem(connection, 60)
                            
                            if waiting and waiting.Value == localPlayer.Name then
                                for _, v in pairs(child:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.CanCollide = false
                                    end
                                end
                                child:SetPrimaryPartCFrame(CFrame.new(-72.9304581, -3.96906614, -265.543732))
                                task.wait(0.2)
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
                    -- DÜZELT0LD0: Tan1ms1z 'playerCharacter' yerine 'localPlayer.Character' kullan1ld1
                    local char = localPlayer.Character
                    if char and localPlayer.CanSpawnToy and localPlayer.CanSpawnToy.Value and #bombList < _G.MaxMissiles and char:FindFirstChild("Head") then
                        spawnItemCf(_G.ToyToLoad, char.Head.CFrame or char.HumanoidRootPart.CFrame)
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

print("Lunx > Settings")

local blobalter = 1
local function blobGrabPlayer(player, LUNX)
    if blobalter == 1 then
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {
                [1] = LUNX:FindFirstChild("LeftDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = LUNX:FindFirstChild("LeftDetector"):FindFirstChild("LeftWeld")
            }
            LUNX:WaitForChild("LUNXSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 2
        end
    else
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local args = {
                [1] = LUNX:FindFirstChild("RightDetector"),
                [2] = player.Character:FindFirstChild("HumanoidRootPart"),
                [3] = LUNX:FindFirstChild("RightDetector"):FindFirstChild("RightWeld")
            }
            LUNX:WaitForChild("LUNXSeatAndOwnerScript"):WaitForChild("CreatureGrab"):FireServer(unpack(args))
            blobalter = 1
        end
    end
end

print("Lunx > Settings")















-- ==========================================
-- GEREKL0 TÜM SERV0SLER VE GLOBAL TANIMLAR
-- ==========================================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Debris = game:GetService("Debris")
local Workspace = workspace

local localPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- Hile ayarlar1n1 ve anl1k durumlar1 (state) tutacak tablolar
local Settings = {
    PvP = {
        SilentAimEnabled = false,
        SilentAimStrength = 1000,
        SilentAimKeybindMode = false,
        SilentAimKeybindHeld = false,
        TriggerbotEnabled = false,
        LegitAimEnabled = false,
        LegitAimHolding = false,
        LegitAimSmooth = true,
        LegitAimSmoothness = 5,
        LegitAimMode = "Crosshair",
        LegitAimIgnoreFriends = true,
        LegitAimVisible = true,
        LegitAimUnsafe = true,
        LegitAimHitbox = "Body",
        LegitAimWorkOnNPC = false,
        DrawTargetEnabled = true, -- Hedefe çizgi çizme aktif mi?
        DrawTargetColor = Color3.fromRGB(255, 0, 0) -- Çizgi rengi (K1rm1z1)
    },
    Grab = {
        EnableThrowStrength = false,
        strength = 400,
        VoidGrab = false,
        SkyGrab = false,
        SpinGrab = false,
        FlingGrab = false,
        NoclipGrab = false
    },
    Telekinesis = {
        Enabled = false,
        grabToysFly = false,
        _feature = nil
    }
}

local State = {
    Root = nil,
    HRPs = {},
    CameraClone = Camera,
    CameraInitialized = true,
    ReachDistance = 500,
    LastGrabbedTarget = nil,
    connections = {},
    GrabMaintainConnections = {},
    noclipRunning = false,
    noclipTrackedParts = {}
}

-- ==========================================
-- DRAWING API - HEDEF ODAK Ç0ZG0S0 (Target Tracer)
-- ==========================================
local TargetTracer = Drawing.new("Line")
TargetTracer.Thickness = 2
TargetTracer.Transparency = 1
TargetTracer.Color = Settings.PvP.DrawTargetColor
TargetTracer.Visible = false

local function updateTracer(targetPart)
    if not Settings.PvP.DrawTargetEnabled or not targetPart then
        TargetTracer.Visible = false
        return
    end

    local screenPos, onScreen = Camera:WorldToViewportPoint(targetPart.Position)
    if onScreen then
        local Center = Camera.ViewportSize / 2
        TargetTracer.From = Vector2.new(Center.X, Center.Y) -- Ekran1n ortas1ndan ba_lar
        TargetTracer.To = Vector2.new(screenPos.X, screenPos.Y) -- Hedefe uzan1r
        TargetTracer.Color = Settings.PvP.DrawTargetColor
        TargetTracer.Visible = true
    else
        TargetTracer.Visible = false
    end
end

-- ==========================================
-- KOORD0NASYON VE ARKA PLAN DÖNGÜLER0
-- ==========================================
local function updatePvPCharacter(NewCharacter)
    if not (NewCharacter and NewCharacter:IsDescendantOf(workspace)) then return end
    State.Root = NewCharacter:WaitForChild("HumanoidRootPart", 3)
    if not State.Root then return end
    State.HRPs[State.Root] = nil
end

-- DÜZELT0LD0: LocalPlayer -> localPlayer yap1ld1
if localPlayer.Character then updatePvPCharacter(localPlayer.Character) end
localPlayer.CharacterAdded:Connect(updatePvPCharacter)

workspace.DescendantAdded:Connect(function(Descendant)
    if Descendant:IsA("BasePart") and Descendant.Name == "HumanoidRootPart" then
        if Descendant ~= State.Root then State.HRPs[Descendant] = true end
    end
end)
workspace.DescendantRemoving:Connect(function(Descendant)
    if State.HRPs[Descendant] then
        State.HRPs[Descendant] = nil
        if State.LastGrabbedTarget == Descendant then State.LastGrabbedTarget = nil end
    end
end)
for _, Descendant in workspace:GetDescendants() do
    if Descendant:IsA("BasePart") and Descendant.Name == "HumanoidRootPart" then
        if Descendant ~= State.Root then State.HRPs[Descendant] = true end
    end
end

-- ==========================================
-- SILENT AIM & CAMERA LOCK DÖNGÜSÜ
-- ==========================================
do
    local savedRotCF = nil
    local isLocking = false

    local function isRotateVisible()
        local ok, result = pcall(function()
            -- DÜZELT0LD0: LocalPlayer -> localPlayer yap1ld1
            local pg = localPlayer:FindFirstChild("PlayerGui")
            if not pg then return false end
            local cg = pg:FindFirstChild("ControlsGui")
            if not cg then return false end
            local pf = cg:FindFirstChild("PCFrame")
            if not pf then return false end
            local r = pf:FindFirstChild("Rotate")
            if not r then return false end
            return r.Visible
        end)
        return ok and result or false
    end

    RunService.RenderStepped:Connect(function()
        if not State.CameraInitialized or not State.CameraClone then return end

        local shouldLock = isRotateVisible()

        if shouldLock and not isLocking then
            isLocking = true
            savedRotCF = State.CameraClone.CFrame
        elseif not shouldLock and isLocking then
            isLocking = false
            savedRotCF = nil
        end

        if isLocking and savedRotCF then
            local pos = State.CameraClone.CFrame.Position
            State.CameraClone.CFrame = CFrame.new(pos, pos + savedRotCF.LookVector)
            UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
            return
        end

        if not Settings.PvP.SilentAimEnabled then
            Camera.CFrame = State.CameraClone.CFrame
            if not Settings.PvP.LegitAimEnabled then TargetTracer.Visible = false end
            return
        end

        if Settings.PvP.SilentAimKeybindMode then
            if not Settings.PvP.SilentAimKeybindHeld then
                Camera.CFrame = State.CameraClone.CFrame
                TargetTracer.Visible = false
                return
            end
        end

        local TargetCFrame = State.CameraClone.CFrame
        local currentTargetPart = nil

        if not workspace:FindFirstChild("GrabParts") then
            local Center = Camera.ViewportSize / 2
            local halfDiag = Center.Magnitude
            local AdjustedStrength = (Settings.PvP.SilentAimStrength / 200) * halfDiag
            local bestDist = math.huge
            local bestTarget = nil
            local bestAimPart = nil
            local bestPriority = math.huge
            local camPos = State.CameraClone.CFrame.Position

            local rayParams = RaycastParams.new()
            rayParams.FilterType = Enum.RaycastFilterType.Exclude
            -- DÜZELT0LD0: LocalPlayer -> localPlayer yap1ld1
            rayParams.FilterDescendantsInstances = { localPlayer.Character }

            local partPriority = {
                ["Right Leg"] = 1, ["Left Leg"] = 1, ["Right Arm"] = 1, ["Left Arm"] = 1,
                ["Torso"] = 2, ["HumanoidRootPart"] = 2,
                ["Head"] = 3,
            }
            local aimParts = {"Head", "Torso", "Right Arm", "Left Arm", "Right Leg", "Left Leg", "HumanoidRootPart"}

            for HRP in pairs(State.HRPs) do
                if HRP and HRP.Parent and HRP:IsDescendantOf(workspace) then
                    local model = HRP.Parent
                    local hum = model:FindFirstChildWhichIsA("Humanoid")
                    if hum and hum.Health > 0 then
                        local screenPos, onScreen = State.CameraClone:WorldToScreenPoint(HRP.Position)
                        if onScreen then
                            local mag = (HRP.Position - camPos).Magnitude
                            if mag <= State.ReachDistance then
                                local screenDist = (Vector2.new(screenPos.X, screenPos.Y) - Center).Magnitude
                                if screenDist <= AdjustedStrength then
                                    local foundPart = nil
                                    local foundPriority = math.huge
                                    local fallbackPart = nil
                                    for _, partName in ipairs(aimParts) do
                                        local part = model:FindFirstChild(partName)
                                        if part and part:IsA("BasePart") then
                                            local dir = (part.Position - camPos)
                                            local rayResult = workspace:Raycast(camPos, dir, rayParams)
                                            if rayResult and rayResult.Instance:IsDescendantOf(model) then
                                                local pri = partPriority[partName] or 2
                                                if pri < foundPriority then
                                                    foundPriority = pri
                                                    foundPart = part
                                                end
                                            elseif not fallbackPart then
                                                fallbackPart = part
                                            end
                                        end
                                    end
                                    if not foundPart and fallbackPart then
                                        foundPart = fallbackPart
                                        foundPriority = partPriority[fallbackPart.Name] or 2
                                    end
                                    if foundPart then
                                        if foundPriority < bestPriority or (foundPriority == bestPriority and screenDist < bestDist) then
                                            bestPriority = foundPriority
                                            bestDist = screenDist
                                            bestTarget = HRP
                                            bestAimPart = foundPart
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end

            if bestTarget and bestAimPart then
                local aimPos = bestAimPart.Position
                currentTargetPart = bestAimPart
                if TargetCFrame.LookVector:Dot((aimPos - TargetCFrame.Position).Unit) > 0 then
                    TargetCFrame = CFrame.new(TargetCFrame.Position, aimPos)
                end
            end
        end

        Camera.CFrame = TargetCFrame
        updateTracer(currentTargetPart)
    end)

    -- ==========================================
    -- TRIGGERBOT DÖNGÜSÜ
    -- ==========================================
    local tbRayParams = RaycastParams.new()
    tbRayParams.FilterType = Enum.RaycastFilterType.Exclude

    RunService.RenderStepped:Connect(function()
        if not Settings.PvP.TriggerbotEnabled then return end
        if workspace:FindFirstChild("GrabParts") then return end

        -- DÜZELT0LD0: LocalPlayer -> localPlayer yap1ld1
        local myChar = localPlayer.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myRoot then return end

        local cam = State.CameraClone or workspace.CurrentCamera
        if not cam then return end
        local camCF = cam.CFrame
        local origin = camCF.Position
        local look = camCF.LookVector
        local right = camCF.RightVector
        local up = camCF.UpVector

        tbRayParams.FilterDescendantsInstances = { myChar, workspace.Terrain }

        local hit = nil
        local offsets = {
            look,
            (look + right * 0.02).Unit, (look - right * 0.02).Unit,
            (look + up * 0.02).Unit, (look - up * 0.02).Unit,
            (look + right * 0.04 + up * 0.02).Unit, (look - right * 0.04 + up * 0.02).Unit,
            (look + right * 0.04 - up * 0.02).Unit, (look - right * 0.04 - up * 0.02).Unit,
        }
        for _, d in ipairs(offsets) do
            local r = workspace:Raycast(origin, d * 1000, tbRayParams)
            if r then hit = r break end
        end
        if not hit then return end

        local model = hit.Instance:FindFirstAncestorOfClass("Model")
        if not model or model == myChar then return end
        local hum = model:FindFirstChildOfClass("Humanoid")
        if not hum or hum.Health <= 0 then return end
        local root = model:FindFirstChild("HumanoidRootPart")
        if not root then return end
        -- DÜZELT0LD0: LocalPlayer -> localPlayer yap1ld1
        local hasGamepass = localPlayer:FindFirstChild("FartherReach") ~= nil
        if (myRoot.Position - root.Position).Magnitude > (hasGamepass and 30 or 20) then return end

        pcall(function() mouse1press() end)
        pcall(function() mouse1release() end)
    end)
end

-- ==========================================
-- LEGIT AIM MODULE
-- ==========================================
local LegitAimFeature = {}
LegitAimFeature.Connection = nil
LegitAimFeature.SelectedLimb = nil

function LegitAimFeature.getNearestPlayer()
    local myRoot = State.Root
    if not myRoot or not myRoot.Parent then
        local char = LocalPlayer.Character
        if char then myRoot = char:FindFirstChild("HumanoidRootPart") end
    end
    if not myRoot or not myRoot.Parent then return nil end
    local nearest = nil
    local bestScore = math.huge
    local cam = workspace.CurrentCamera
    if not cam then return nil end
    local screenCenter = cam.ViewportSize / 2
    for _, otherPlayer in ipairs(Players:GetPlayers()) do
        if otherPlayer ~= LocalPlayer then
            if Settings.PvP.LegitAimIgnoreFriends then
                local isFriend = false
                pcall(function() isFriend = LocalPlayer:IsFriendsWith(otherPlayer.UserId) end)
                if isFriend then continue end
            end
            local otherChar = otherPlayer.Character
            if otherChar then
                local otherRoot = otherChar:FindFirstChild("HumanoidRootPart")
                local otherHum = otherChar:FindFirstChildWhichIsA("Humanoid")
                if otherRoot and otherHum and otherHum.Health > 0 then
                    if Settings.PvP.LegitAimVisible then
                        local origin = myRoot.Position
                        local dir = (otherRoot.Position - origin)
                        local rp = RaycastParams.new()
                        rp.FilterType = Enum.RaycastFilterType.Exclude
                        local exclude = {}
                        if LocalPlayer.Character then table.insert(exclude, LocalPlayer.Character) end
                        if otherChar then table.insert(exclude, otherChar) end
                        rp.FilterDescendantsInstances = exclude
                        local ray = workspace:Raycast(origin, dir, rp)
                        if ray then continue end
                    end
                    local score
                    if Settings.PvP.LegitAimMode == "Crosshair" then
                        local screenPos, onScreen = cam:WorldToScreenPoint(otherRoot.Position)
                        local dx = screenPos.X - screenCenter.X
                        local dy = screenPos.Y - screenCenter.Y
                        score = math.sqrt(dx * dx + dy * dy)
                        if not onScreen then
                            score = score + 10000
                        end
                    else
                        score = (myRoot.Position - otherRoot.Position).Magnitude
                    end
                    if score < bestScore then
                        bestScore = score
                        nearest = otherChar
                    end
                end
            end
        end
    end
    if Settings.PvP.LegitAimWorkOnNPC then
        if not LegitAimFeature.npcCache or (tick() - (LegitAimFeature.npcCacheTime or 0)) > 1 then
            LegitAimFeature.npcCache = {}
            LegitAimFeature.npcCacheTime = tick()
            for _, p in ipairs(Players:GetPlayers()) do
                local inv = Workspace:FindFirstChild(p.Name .. "SpawnedInToys")
                if inv then
                    for _, obj in ipairs(inv:GetChildren()) do
                        if obj.Name == "YouDecoy" and obj:IsA("Model") then
                            table.insert(LegitAimFeature.npcCache, obj)
                        end
                    end
                end
            end
            local robloxians = Workspace:FindFirstChild("Robloxians")
            if robloxians then
                for _, obj in ipairs(robloxians:GetChildren()) do
                    if obj:IsA("Model") then
                        table.insert(LegitAimFeature.npcCache, obj)
                    end
                end
            end
        end
        for i = #LegitAimFeature.npcCache, 1, -1 do
            local npc = LegitAimFeature.npcCache[i]
            if not npc.Parent then
                table.remove(LegitAimFeature.npcCache, i)
            else
                local npcRoot = npc:FindFirstChild("HumanoidRootPart")
                    or npc:FindFirstChild("Head")
                    or npc:FindFirstChild("Torso")
                    or npc.PrimaryPart
                if npcRoot and (npcRoot.Position - myRoot.Position).Magnitude < 500 then
                    local score
                    if Settings.PvP.LegitAimMode == "Crosshair" then
                        local screenPos, onScreen = cam:WorldToScreenPoint(npcRoot.Position)
                        local dx = screenPos.X - screenCenter.X
                        local dy = screenPos.Y - screenCenter.Y
                        score = math.sqrt(dx * dx + dy * dy)
                        if not onScreen then score = score + 10000 end
                    else
                        score = (myRoot.Position - npcRoot.Position).Magnitude
                    end
                    if score < bestScore then
                        bestScore = score
                        nearest = npc
                    end
                end
            end
        end
    end
    return nearest
end

function LegitAimFeature.pickLimb(char)
    if not char then return nil end
    local cam = workspace.CurrentCamera
    if not cam then return nil end
    local screenCenter = cam.ViewportSize / 2
    local limbNames = {"Right Arm", "Left Arm", "HumanoidRootPart", "Left Leg", "Right Leg", "Head"}
    local r15Names = {"RightHand", "LeftHand", "HumanoidRootPart", "LeftFoot", "RightFoot", "Head"}
    local weights = {30, 25, 20, 10, 10, 5}
    local limbs = {}
    for i, name in ipairs(limbNames) do
        local part = char:FindFirstChild(name)
        if not part then
            part = char:FindFirstChild(r15Names[i])
        end
        if part then
            table.insert(limbs, {part = part, weight = weights[i]})
        end
    end
    if #limbs == 0 then
        return char:FindFirstChild("HumanoidRootPart")
    end
    if Settings.PvP.LegitAimMode == "Crosshair" then
        local bestLimb = nil
        local bestLimbScore = math.huge
        for _, l in ipairs(limbs) do
            local sp, onScreen = cam:WorldToScreenPoint(l.part.Position)
            local dx = sp.X - screenCenter.X
            local dy = sp.Y - screenCenter.Y
            local s = math.sqrt(dx * dx + dy * dy)
            if not onScreen then s = s + 10000 end
            if s < bestLimbScore then
                bestLimbScore = s
                bestLimb = l.part
            end
        end
        return bestLimb
    else
        local totalWeight = 0
        for _, l in ipairs(limbs) do totalWeight = totalWeight + l.weight end
        local roll = math.random() * totalWeight
        local cumulative = 0
        for _, l in ipairs(limbs) do
            cumulative = cumulative + l.weight
            if roll <= cumulative then
                return l.part
            end
        end
        return limbs[#limbs].part
    end
end

function LegitAimFeature.getAimTarget()
    local char = LegitAimFeature.getNearestPlayer()
    if not char then return nil end
    if not Settings.PvP.LegitAimUnsafe then
        local hitbox = Settings.PvP.LegitAimHitbox or "Body"
        local nameMap = {
            ["Head"] = {"Head"},
            ["Body"] = {"HumanoidRootPart"},
            ["Left Arm"] = {"Left Arm", "LeftHand"},
            ["Right Arm"] = {"Right Arm", "RightHand"},
            ["Left Leg"] = {"Left Leg", "LeftFoot"},
            ["Right Leg"] = {"Right Leg", "RightFoot"},
        }
        local names = nameMap[hitbox] or {"HumanoidRootPart"}
        for _, n in ipairs(names) do
            local part = char:FindFirstChild(n)
            if part then return part end
        end
        return char:FindFirstChild("HumanoidRootPart")
    end
    if LegitAimFeature.SelectedLimb and LegitAimFeature.SelectedLimb.Parent then
        return LegitAimFeature.SelectedLimb
    end
    LegitAimFeature.SelectedLimb = LegitAimFeature.pickLimb(char)
    return LegitAimFeature.SelectedLimb
end

function LegitAimFeature.Start()
    if LegitAimFeature.Connection then return end
    LegitAimFeature.Connection = RunService.RenderStepped:Connect(function()
        if not Settings.PvP.LegitAimEnabled then return end
        if not Settings.PvP.LegitAimHolding then
            LegitAimFeature.SelectedLimb = nil
            if not Settings.PvP.SilentAimEnabled then TargetTracer.Visible = false end
            return
        end
        local myRoot = State.Root
        if not myRoot or not myRoot.Parent then
            local char = LocalPlayer.Character
            if char then myRoot = char:FindFirstChild("HumanoidRootPart") end
        end
        if not myRoot or not myRoot.Parent then return end
        State.Root = myRoot
        local target = LegitAimFeature.getAimTarget()
        if target then
            local cam = workspace.CurrentCamera
            if not cam then return end
            local camPos = cam.CFrame.Position
            if Settings.PvP.LegitAimSmooth then
                local targetDir = (target.Position - camPos).Unit
                local currentLook = cam.CFrame.LookVector
                local factor = math.min(1, 2.5 / Settings.PvP.LegitAimSmoothness)
                local newLook = (currentLook + (targetDir - currentLook) * factor).Unit
                cam.CFrame = CFrame.lookAt(camPos, camPos + newLook)
            else
                cam.CFrame = CFrame.lookAt(camPos, target.Position)
            end
            updateTracer(target)
        else
            if not Settings.PvP.SilentAimEnabled then TargetTracer.Visible = false end
        end
    end)
end

function LegitAimFeature.Stop()
    if LegitAimFeature.Connection then
        LegitAimFeature.Connection:Disconnect()
        LegitAimFeature.Connection = nil
    end
end

LegitAimFeature.Start()

-- ==========================================
-- GRAB FEATURE (Yeni Gönderilen Modüller)
-- ==========================================
local GrabFeature = {}

function GrabFeature.onGrabPartAdded_ThrowStrength(model)
    if model.Name ~= "GrabParts" then return end
    if not Settings.Grab.EnableThrowStrength then return end
    task.wait(0.1)
    local grabPart = model:FindFirstChild("GrabPart")
    if not grabPart then return end
    local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
    if not weld or not weld.Part1 then return end
    local partToImpulse = weld.Part1
    if not partToImpulse:IsA("BasePart") then return end
    local conn
    conn = model:GetPropertyChangedSignal("Parent"):Connect(function()
        if not model.Parent then
            local lastInput = UserInputService:GetLastInputType()
            if lastInput == Enum.UserInputType.MouseButton2 or lastInput == Enum.UserInputType.Touch then
                if Settings.Grab.EnableThrowStrength then
                    local impulse = Instance.new("BodyVelocity")
                    impulse.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    impulse.Velocity = Workspace.CurrentCamera.CFrame.LookVector * Settings.Grab.strength
                    impulse.P = 2000
                    impulse.Parent = partToImpulse
                    Debris:AddItem(impulse, 0.08)
                end
            end
            if conn then conn:Disconnect() end
        end
    end)
end

function GrabFeature.enableStrength()
    Settings.Grab.EnableThrowStrength = true
    if State.connections.strength then State.connections.strength:Disconnect() end
    State.connections.strength = Workspace.ChildAdded:Connect(GrabFeature.onGrabPartAdded_ThrowStrength)
end

function GrabFeature.disableStrength()
    Settings.Grab.EnableThrowStrength = false
    if State.connections.strength then 
        State.connections.strength:Disconnect() 
        State.connections.strength = nil 
    end
end

function GrabFeature.maintainNetworkOwnership(grabModel, grabbedPart)
    if State.GrabMaintainConnections[grabModel] then
        State.GrabMaintainConnections[grabModel]:Disconnect()
        State.GrabMaintainConnections[grabModel] = nil
    end
    local needsContinuous = Settings.Grab.VoidGrab
    if not needsContinuous then return end
    local conn = RunService.Heartbeat:Connect(function()
        if not grabModel.Parent or not grabbedPart.Parent then
            if State.GrabMaintainConnections[grabModel] then
                State.GrabMaintainConnections[grabModel]:Disconnect()
                State.GrabMaintainConnections[grabModel] = nil
            end
            return
        end
        if Settings.Grab.VoidGrab then
            local bv = Instance.new("BodyVelocity")
            bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
            bv.Velocity = Vector3.new(0, 12000, 0)
            bv.Parent = grabbedPart
            Debris:AddItem(bv, 1.2)
        end
    end)
    State.GrabMaintainConnections[grabModel] = conn
end

function GrabFeature.onGrabPartsAdded_FTAP(model)
    if model.Name ~= "GrabParts" or not model:IsA("Model") then return end
    local grabPart = model:WaitForChild("GrabPart", 8)
    if not grabPart then return end
    local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
    if not weld or not weld.Part1 then return end
    local grabbedPart = weld.Part1
    if grabbedPart.Anchored then return end

    if Settings.Telekinesis.grabToysFly and Settings.Telekinesis.Enabled then
        local victimChar = grabbedPart.Parent
        local victimPlayer = victimChar and Players:GetPlayerFromCharacter(victimChar)
        if not victimPlayer and victimChar and victimChar:IsA("Model") then
            task.spawn(function()
                local TelekinesisFeature = Settings.Telekinesis._feature
                if TelekinesisFeature then
                    TelekinesisFeature.startOrbit(victimChar, true)
                end
            end)
        end
    end

    local victimChar2 = grabbedPart.Parent
    if not victimChar2 then return end
    local anySpecialEnabled = Settings.Grab.VoidGrab or Settings.Grab.NoclipGrab or Settings.Grab.SkyGrab or Settings.Grab.SpinGrab or Settings.Grab.FlingGrab
    if not anySpecialEnabled then return end
    task.wait(0.07)
    GrabFeature.maintainNetworkOwnership(model, grabbedPart)
    if Settings.Grab.VoidGrab then
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
        bv.Velocity = Vector3.new(0, 14000, 0)
        bv.Parent = grabbedPart
        Debris:AddItem(bv, 1.2)
        task.delay(2.0, function()
            if grabbedPart and grabbedPart.Parent then
                pcall(function() destroyGrabLineEvent:FireServer(grabbedPart) end)
            end
        end)
    end
    if Settings.Grab.SkyGrab then
        task.spawn(function()
            local victimChar = grabbedPart.Parent
            local victimPlayer = victimChar and Players:GetPlayerFromCharacter(victimChar)
            if not victimPlayer then return end
            while Settings.Grab.SkyGrab and model.Parent and grabbedPart.Parent do
                pcall(function()
                    local hrp = victimPlayer.Character and victimPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        hrp.AssemblyLinearVelocity = Vector3.new(0, 5000, 0)
                    end
                end)
                task.wait()
            end
        end)
    end
    if Settings.Grab.SpinGrab then
        task.spawn(function()
            local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
            if weld then weld:Destroy() end
            local bp = Instance.new("BodyPosition")
            bp.Name = "_SpinGrabFollow"
            bp.MaxForce = Vector3.one * math.huge
            bp.D = 500
            bp.P = 1e5
            bp.Position = grabbedPart.Position
            bp.Parent = grabbedPart
            local bav = Instance.new("BodyAngularVelocity")
            bav.Name = "_SpinGrabSpin"
            bav.AngularVelocity = Vector3.new(0, 1000, 0)
            bav.MaxTorque = Vector3.one * math.huge
            bav.Parent = grabbedPart
            while Settings.Grab.SpinGrab and model.Parent and grabbedPart.Parent do
                pcall(function()
                    bp.Position = grabPart.Position
                    grabbedPart.AssemblyLinearVelocity = Vector3.zero
                end)
                task.wait()
            end
            pcall(function()
                if bp and bp.Parent then bp:Destroy() end
                if bav and bav.Parent then bav:Destroy() end
            end)
        end)
    end
    if Settings.Grab.FlingGrab then
        pcall(function()
            local bv = Instance.new("BodyVelocity")
            bv.MaxForce = Vector3.one * math.huge
            bv.Velocity = Vector3.new(math.random(-100,100), 200, math.random(-100,100))
            bv.Parent = grabbedPart
            Debris:AddItem(bv, 1)
        end)
    end
end

function GrabFeature.noclipGrab()
    State.noclipRunning = true
    while State.noclipRunning do
        local grabbedThisFrame = {}
        local grabParts = Workspace:FindFirstChild("GrabParts")
        if grabParts then
            for _, gp in ipairs(grabParts:GetChildren()) do
                if gp.Name == "GrabPart" then
                    local weld = gp:FindFirstChildOfClass("WeldConstraint")
                    local part1 = weld and weld.Part1
                    if part1 then
                        local function processPart(p)
                            if p:IsA("BasePart") and not p.Anchored then
                                if State.noclipTrackedParts[p] == nil then
                                    State.noclipTrackedParts[p] = p.CanCollide
                                end
                                p.CanCollide = false
                                grabbedThisFrame[p] = true
                            end
                        end
                        processPart(part1)
                        local model = part1:FindFirstAncestorOfClass("Model")
                        if model and model ~= Workspace then
                            for _, desc in ipairs(model:GetDescendants()) do
                                processPart(desc)
                            end
                        end
                    end
                end
            end
        end
        for part, originalState in pairs(State.noclipTrackedParts) do
            if not grabbedThisFrame[part] then
                if part and part.Parent then
                    part.CanCollide = originalState
                end
                State.noclipTrackedParts[part] = nil
            end
        end
        task.wait()
    end
end


































local ReplicatedStorage = game:GetService("ReplicatedStorage")
-- TEMA 

local MascotPresets = {
    ["Scout"]      = "rbxassetid://125067064175903",
    ["Mimi Typh"]  = "https://raw.githubusercontent.com/LUNXCHEAT/00000000000000000000000000000000000000000000000000/refs/heads/main/mimityph.png",
    ["PovHuso"]    = "https://raw.githubusercontent.com/LUNXCHEAT/00000000000000000000000000000000000000000000000000/refs/heads/main/Kuzen.png", -- PovHuso için verdiin kuzen linki
    ["CSG"]        = "https://raw.githubusercontent.com/LUNXCHEAT/00000000000000000000000000000000000000000000000000/refs/heads/main/CSG.png",
    ["ApexTurka"]  = "https://raw.githubusercontent.com/LUNXCHEAT/00000000000000000000000000000000000000000000000000/refs/heads/main/Apexturka.png",
    ["g3oxq22kc"] = "https://raw.githubusercontent.com/LUNXCHEAT/00000000000000000000000000000000000000000000000000/refs/heads/main/bunt.png",
}

local SelectedName = _G.SelectedTheme or "CSG"

local SelectedMascot = MascotPresets[SelectedName] or MascotPresets["CSG"]
print("Lunx > Completed Hub")
print("Lunx > Using ", SelectedName, " Template")





local Window = Hub:CreateWindow({
    Title = "LUNX - Fling Things and People | v1.1.6 (BETA)",
    ToggleKey = Enum.KeyCode.K,
    PanelTransparency = 0.45,
    MascotImage = SelectedMascot, -- Buras1 art1k haf1zaya göre otomatik dei_iyor!
    MascotSize = UDim2.fromOffset(300, 310),
    MascotTransparency = 0.1
})









-- TAB DEFINITIONS
local homeTab = Window:CreateTab("Home")
local AimTab = Window:CreateTab("Aim Assist")
local GrabTab = Window:CreateTab("Grab Combat")
local PlayerTab = Window:CreateTab("Player")
local ObjectGrabTab = Window:CreateTab("Object Physics")
local DefanseTab = Window:CreateTab("Safety")
local FunTab = Window:CreateTab("Troll")

-- ==========================================
-- HOME TAB
-- ==========================================
homeTab:CreateLabel("LUNX UI")
homeTab:CreateLabel("Welcome to Lunx Cheat")

homeTab:CreateButton({
    Name = "Copy Server Link",
    Callback = function()
        setclipboard("discord.gg/fMdYZNMZWe")
    end,
})

-- ==========================================
-- AIM ASSIST TAB
-- ==========================================
AimTab:CreateLabel("Silent Aim Settings")

AimTab:CreateToggle({
    Name = "Silent Aim",
    Default = false,
    Flag = "SilentAimToggle",
    Callback = function(Value)
        Settings.PvP.SilentAimEnabled = Value
    end
})

AimTab:CreateSlider({
    Name = "Silent Aim Strength",
    Min = 10,
    Max = 1000,
    Default = 200,
    Flag = "SilentAimStrengthSlider",
    Callback = function(Value)
        Settings.PvP.SilentAimStrength = Value
    end
})

AimTab:CreateToggle({
    Name = "Silent Aim Keybind Mode",
    Default = false,
    Flag = "SilentAimKeyModeToggle",
    Callback = function(Value)
        Settings.PvP.SilentAimKeybindMode = Value
    end
})

AimTab:CreateKeybind({
    Name = "Silent Aim Keybind",
    Default = Enum.KeyCode.E,
    Callback = function()
        Settings.PvP.SilentAimKeybindHeld = not Settings.PvP.SilentAimKeybindHeld
    end
})

AimTab:CreateLabel("Legit Aim Settings")

AimTab:CreateToggle({
    Name = "Legit Aim",
    Default = false,
    Flag = "LegitAimToggle",
    Callback = function(Value)
        Settings.PvP.LegitAimEnabled = Value
        Settings.PvP.LegitAimHolding = Value
    end
})

AimTab:CreateToggle({
    Name = "Legit Smooth",
    Default = true,
    Flag = "LegitSmoothToggle",
    Callback = function(Value)
        Settings.PvP.LegitAimSmooth = Value
    end
})

AimTab:CreateSlider({
    Name = "Legit Smoothness Value",
    Min = 1,
    Max = 20,
    Default = 5,
    Flag = "LegitSmoothnessSlider",
    Callback = function(Value)
        Settings.PvP.LegitAimSmoothness = Value
    end
})

AimTab:CreateDropdown({
    Name = "Legit Target Mode",
    Options = {"Crosshair", "Distance"},
    Default = "Crosshair",
    Flag = "LegitModeDropdown",
    Callback = function(Value)
        Settings.PvP.LegitAimMode = Value
    end
})

AimTab:CreateDropdown({
    Name = "Legit Target Hitbox",
    Options = {"Head", "Body", "Left Arm", "Right Arm", "Left Leg", "Right Leg"},
    Default = "Body",
    Flag = "LegitHitboxDropdown",
    Callback = function(Value)
        Settings.PvP.LegitAimHitbox = Value
    end
})

AimTab:CreateToggle({
    Name = "Ignore Friends",
    Default = true,
    Flag = "LegitIgnoreFriendsToggle",
    Callback = function(Value)
        Settings.PvP.LegitAimIgnoreFriends = Value
    end
})

AimTab:CreateToggle({
    Name = "Wall Check",
    Default = true,
    Flag = "LegitWallCheckToggle",
    Callback = function(Value)
        Settings.PvP.LegitAimVisible = Value
    end
})

AimTab:CreateToggle({
    Name = "Target NPCs",
    Default = false,
    Flag = "LegitNPCToggle",
    Callback = function(Value)
        Settings.PvP.LegitAimWorkOnNPC = Value
    end
})

AimTab:CreateLabel("Visual Settings")

AimTab:CreateToggle({
    Name = "Draw Target Tracer",
    Default = true,
    Flag = "DrawAimTargetToggle",
    Callback = function(Value)
        Settings.PvP.DrawTargetEnabled = Value
        if not Value then TargetTracer.Visible = false end
    end
})

AimTab:CreateColorPicker({
    Name = "Draw Line Color",
    Default = Color3.fromRGB(255, 0, 0),
    Flag = "DrawLineColorPicker",
    Callback = function(Value)
        Settings.PvP.DrawTargetColor = Value
    end
})

AimTab:CreateLabel("Automation Settings")

AimTab:CreateToggle({
    Name = "Triggerbot",
    Default = false,
    Flag = "TriggerbotToggle",
    Callback = function(Value)
        Settings.PvP.TriggerbotEnabled = Value
    end
})


-- ==========================================
-- GRAB COMBAT TAB
-- ==========================================
_G.strength = 400

GrabTab:CreateLabel("Grab Power Settings")

GrabTab:CreateSlider({
    Name = "Strength Power",
    Min = 300,
    Max = 10000,
    Default = 1000,
    Flag = "StrengthSlider", 
    Callback = function(Value)
        Settings.Grab.strength = Value
        _G.strength = Value
    end,
})

GrabTab:CreateToggle({
    Name = "Strength Force",
    Default = false,
    Flag = "StrengthToggle",
    Callback = function(enabled)
        if enabled then
            GrabFeature.enableStrength()
        else
            GrabFeature.disableStrength()
        end
    end
})

GrabTab:CreateLabel("Grab Modes")

GrabTab:CreateToggle({
    Name = "Void Grab",
    Default = false,
    Flag = "VoidGrabToggle",
    Callback = function(enabled)
        Settings.Grab.VoidGrab = enabled
    end
})

GrabTab:CreateToggle({
    Name = "Sky Grab",
    Default = false,
    Flag = "SkyGrabToggle",
    Callback = function(enabled)
        Settings.Grab.SkyGrab = enabled
    end
})

GrabTab:CreateToggle({
    Name = "Spin Grab",
    Default = false,
    Flag = "SpinGrabToggle",
    Callback = function(enabled)
        Settings.Grab.SpinGrab = enabled
    end
})

GrabTab:CreateToggle({
    Name = "Fling Grab",
    Default = false,
    Flag = "FlingGrabToggle",
    Callback = function(enabled)
        Settings.Grab.FlingGrab = enabled
    end
})

GrabTab:CreateLabel("Custom Effects")

GrabTab:CreateToggle({
    Name = "Poison Grab",
    Default = false,
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

GrabTab:CreateToggle({
    Name = "Radioactive Grab",
    Default = false,
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

GrabTab:CreateToggle({
    Name = "Fire Grab",
    Default = false,
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

GrabTab:CreateToggle({
    Name = "Noclip Grab",
    Default = false,
    Flag = "NoclipGrab", 
    Callback = function(enabled)
        Settings.Grab.NoclipGrab = enabled
        if enabled then
            task.spawn(GrabFeature.noclipGrab)
        else
            State.noclipRunning = false
            for part, originalState in pairs(State.noclipTrackedParts) do
                if part and part.Parent then
                    part.CanCollide = originalState
                end
            end
            State.noclipTrackedParts = {}
        end
    end
})

GrabTab:CreateToggle({
    Name = "Kick Grab",
    Default = false,
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

GrabTab:CreateToggle({
    Name = "Kick Grab Anchor",
    Default = false,
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

GrabTab:CreateToggle({
    Name = "Fire All Players",
    Default = false,
    Flag = "FireAll", 
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

Workspace.ChildAdded:Connect(GrabFeature.onGrabPartsAdded_FTAP)


-- ==========================================
-- PLAYER TAB
-- ==========================================
PlayerTab:CreateToggle({
    Name = "Crouch Speed Modifier",
    Default = false,
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

PlayerTab:CreateSlider({
    Name = "Set Crouch Speed Limit",
    Min = 6,
    Max = 1000,
    Default = 300,
    Flag = "SetCrouchSpeedFlag", 
    Callback = function(Value)
        crouchWalkSpeed = Value
    end
})

PlayerTab:CreateToggle({
    Name = "Crouch Jump Power Modifier",
    Default = false,
    Flag = "CrouchJumpPower", 
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

PlayerTab:CreateSlider({
    Name = "Set Crouch Jump Limit",
    Min = 6,
    Max = 1000,
    Default = 300,
    Flag = "SetCrouchJumpPowerFlag", 
    Callback = function(Value)
        crouchJumpPower = Value
    end
})


-- ==========================================
-- OBJECT GRAB TAB
-- ==========================================
ObjectGrabTab:CreateToggle({
    Name = "Anchor Grabbed Objects",
    Default = false,
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

ObjectGrabTab:CreateButton({
    Name = "Unanchor All Parts",
    Callback = cleanupAnchoredParts
})

ObjectGrabTab:CreateButton({
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

ObjectGrabTab:CreateToggle({
    Name = "Auto Recover Dropped Parts",
    Default = false,
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

ObjectGrabTab:CreateButton({
    Name = "Unanchor Header Part",
    Callback = unanchorPrimaryPart
})


-- ==========================================
-- DEFENSE & SAFETY TAB
-- ==========================================
DefanseTab:CreateToggle({
    Name = "Anti Grab",
    Default = false,
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

DefanseTab:CreateToggle({
    Name = "Anti Kick Grab",
    Default = false,
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

DefanseTab:CreateToggle({
    Name = "Anti Explosion",
    Default = false,
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

DefanseTab:CreateLabel("Self Defense Settings")

DefanseTab:CreateToggle({
    Name = "Air Suspend Defense",
    Default = false,
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

DefanseTab:CreateToggle({
    Name = "Kick Silent Defense",
    Default = false,
    Flag = "KickSilentFlag", 
    Callback = function(enabled)

    end
})


-- ==========================================
-- TROLL TAB
-- ==========================================
FunTab:CreateTextbox({
    Name = "Coins Amount",
    Default = "",
    Placeholder = "Enter value",
    Flag = "Coin",
    Callback = function(Text)
        skolko = Text 
    end,
})

FunTab:CreateButton({
    Name = "Get Coins",
    Callback = function()
        local coinAmount = tonumber(skolko) or 0 
        game.Players.LocalPlayer.PlayerGui.MenuGui.TopRight.CoinsFrame.CoinsDisplay.Coins.Text = tostring(coinAmount)
    end,
})

FunTab:CreateLabel("Decoy Settings")

FunTab:CreateSlider({
    Name = "Offset",
    Min = 1,
    Max = 10,
    Default = 10,
    Flag = "OffsetFlag",
    Callback = function(Value)
        decoyOffset = Value
    end
})

FunTab:CreateTextbox({
    Name = "Circle Radius",
    Default = "",
    Placeholder = "Enter radius",
    Flag = "RadiusFlag",
    Callback = function(Value)
        circleRadius = tonumber(Value) or 10
    end,
})

FunTab:CreateButton({
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
        Hub:Notify({Title = "Notification",Content = "Got "..numDecoys.." units. Manually click each unit if they do not move", Duration = 6.5,})
    end
})

FunTab:CreateButton({
    Name = "Toggle Follow Mode",
    Callback = function()
        followMode = not followMode
    end,
})

FunTab:CreateButton({
    Name = "Disconnect Clones",
    Callback = cleanupConnections(connections)
})


-- ==========================================================
-- REQUIRED VARIABLES & SERVICES
-- ==========================================================
local ReplicatedStorage = game:GetService("ReplicatedStorage")

if not getgenv().Settings then
    getgenv().Settings = {}
end

if not getgenv().Settings.Misc then
    getgenv().Settings.Misc = {
        packetAmount = 50, -- A_1r1 yüksek olmamal1, ideal aral1k: 30 - 150
        packetLag = false
    }
end

local Settings = getgenv().Settings
local State = getgenv().State

if not State then
    getgenv().State = {
        packetLagConn = nil
    }
    State = getgenv().State
end

-- ==========================================
-- PACKET LAG SECTION (ADD UNDER TROLL TAB)
-- ==========================================
FunTab:CreateLabel("Packet Lag Settings")

FunTab:CreateSlider({
    Name = "Packet Amount",
    Min = 5,
    Max = 200, -- Kendi internetini çökertmemek için s1n1r1 dü_ürdük
    Default = 50,
    Flag = "PacketAmount",
    Callback = function(Value)
        Settings.Misc.packetAmount = Value
    end
})

FunTab:CreateToggle({
    Name = "Packet Lag",
    Default = false,
    Flag = "PacketLag",
    Callback = function(Value)
        Settings.Misc.packetLag = Value
        
        if Value then
            if State.packetLagConn then
                task.cancel(State.packetLagConn)
                State.packetLagConn = nil
            end
            
            State.packetLagConn = task.spawn(function()
                local GrabEvent = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("ExtendGrabLine")
                
                while Settings.Misc.packetLag do
                    -- E_zamanl1 (Asynchronous) paket dalgas1 göndererek 
                    -- senin yükleme (upload) kanal1n1 kilitlemeden sunucuya ula_t1r1r1z.
                    local amount = Settings.Misc.packetAmount or 50
                    
                    pcall(function()
                        for i = 1, amount do
                            if not Settings.Misc.packetLag then break end
                            -- Çok büyük string yerine orta boyutlu ama yorucu argümanlar gönderiyoruz
                            GrabEvent:FireServer("Balls_Lag_Active_Vector")
                        end
                    end)
                    
                    -- Kendi pinginin f1rlamamas1 için her dalga aras1nda çok k1sa bir bekleme (0.05 saniye) koyuyoruz.
                    -- Bu sayede senin giden paket kuyruun temizlenir ama sunucu sürekli yeni dalga al1r.
                    task.wait(0.05)
                end
            end)
        else
            if State.packetLagConn then
                task.cancel(State.packetLagConn)
                State.packetLagConn = nil
            end
        end
    end
})

-- ==========================================
-- SCRIPT LOAD COMPLETION
-- ==========================================
print("Lunx > Script completed")
print("Lunx:information > Press K to toggle the menu")

Hub:Notify({ Title = "Information", Content = "Press K to toggle the menu" })

return Hub
