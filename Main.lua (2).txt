
gethui = function()
	return game.CoreGui.RobloxGui
end


local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local Options = Library.Options
local Toggles = Library.Toggles

local TweenService = game:GetService("TweenService")
local Analytics = game:GetService("RbxAnalyticsService")
local UserInputService = game:GetService("UserInputService")
local TextChatService = game:GetService("TextChatService")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local rs = game:GetService("ReplicatedStorage")
local RF = game:GetService("ReplicatedFirst")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

local DestroyToy = rs.MenuToys.DestroyToy
local BombExplode = rs.BombEvents.BombExplode
local SetNetOwner = rs.GrabEvents.SetNetworkOwner
local CreateLine = rs.GrabEvents.CreateGrabLine
local DestroyLine = rs.GrabEvents.DestroyGrabLine
local SpawnToy = rs.MenuToys.SpawnToyRemoteFunction
local Struggle = rs.CharacterEvents.Struggle
local Ragdoll = rs.CharacterEvents.RagdollRemote
local StopVel = rs.GameCorrectionEvents.StopAllVelocity
local UpdLineColor = rs.DataEvents.UpdateLineColorsEvent
local StickyEvent = rs.PlayerEvents.StickyPartEvent

local jerkspeed = 0.1
local spinspeed = 10
local strength = 300
local offset = CFrame.new(0, 15, 0)
local PalletForRagdoll
local Seats = {}
local WhitelistEnabled = false

local Lines = 0
local Camera = workspace.CurrentCamera
local plr = Players.LocalPlayer
local Mouse = plr:GetMouse()
local cons = {}
local inv = workspace[plr.Name.."SpawnedInToys"]

local char = plr.Character
local HRP = char.HumanoidRootPart
local hum = char.Humanoid

plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)
	if c then
		HRP = c:FindFirstChild("HumanoidRootPart") or c:WaitForChild("HumanoidRootPart", 1)
		hum = c:FindFirstChild("Humanoid") or c:WaitForChild("Humanoid", 1)
		char = c
	end
end)

local function gblob()
	local char = plr.Character
	local hum = char:WaitForChild("Humanoid", 0.1)
    if hum and hum.SeatPart then
        if hum.SeatPart.Parent.Name == "CreatureBlobman" then
            return hum.SeatPart.Parent
        end
    end
end

local function disc(name)
    for i,v in cons do
        if i == name then
            v:Disconnect()
        end
    end
end

local function getplot()
    for i = 1, 5 do
        local plot = workspace.Plots:FindFirstChild("Plot"..i)
        local value = plot.PlotSign.ThisPlotsOwners:FindFirstChild("Value")
        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end

local function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end

local function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end
    local t
    local toyadded
    toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c
            toyadded:Disconnect()
        end
    end)
    task.spawn(function()
        SpawnToy:InvokeServer(
            toy,
            cf,
            Vector3.new(0,0,0)
        )
    end)
    local time = tick() + 1
    repeat task.wait() until t or tick() > time
    if t then
        return t
    else
        local plot = getplot()
        if plot then
            return workspace.PlotItems[plot.Name]:FindFirstChild(toy) or workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end

local function grab(obj)
    obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
end

local function drop(obj, cf)
    obj.HoldPart.DropItemRemoteFunction:InvokeServer(obj, cf, vector.create(0, 0, 0))
end

local function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(
        obj2.Position + obj2.Velocity *
        (game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue() / 1000) * 5
    )
end

local function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end

local function getname(v)
    return v:split(" ")[2]:split("(")[2]:split(")")[1]
end

local function HasProperty(obj, property)
    local ok = pcall(function() if obj[property] then end end)
    return ok
end

local admins = loadstring(game:HttpGet("https://raw.githubusercontent.com/Brovaky/Friendly/refs/heads/main/admins"))()

rs.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(...)
    local args = {...}
    local s = args[1]
    if s and typeof(args[2]) == "string" then
        if table.find(admins, s.Name) then
            local char = plr.Character
            local txt = string.split(args[2], " ")
            local nam = tostring(txt[2]):lower()
            if txt[1] == "!kill" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if char and char:FindFirstChild("Humanoid") then
                    char.Humanoid.Health = 0
                end
            elseif txt[1] == "!bring" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                local char1 = game.Players:FindFirstChild(s.Name).Character
                if char and char:FindFirstChild("HumanoidRootPart") and char1 and char1:FindFirstChild("HumanoidRootPart") then
                    char.HumanoidRootPart.CFrame = char1.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                end
            elseif txt[1] == "!kick" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr:Kick("Kicked by posral admin ("..game.Players[s.Name].DisplayName..")")
            elseif txt[1] == "!reveal" and s.Name ~= plr.Name then
                TextChatService.TextChannels.RBXGeneral:SendAsync("Ya Premium Nasral!")
            elseif txt[1] == "!report" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                while task.wait(1) do
                    TextChatService.TextChannels.RBXGeneral:SendAsync("i touch kids")
                end
            elseif txt[1] == "!lag" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr.PlayerScripts["[ExploitTest]FireAllRemotes"].Enabled = true
            elseif txt[1] == "!unlag" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                plr.PlayerScripts["[ExploitTest]FireAllRemotes"].Enabled = false
            elseif txt[1] == "!crash" and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                while true do end
            elseif txt[1] == "!on" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if not Toggles[txt[3]] then return end
                Toggles[txt[3]]:SetValue(true)
            elseif txt[1] == "!off" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                if not Toggles[txt[3]] then return end
                Toggles[txt[3]]:SetValue(false)
            elseif txt[1] == "!setfps" and txt[3] and (plr.Name:lower():find(nam) or plr.DisplayName:lower():find(nam) or nam == "all") then
                setfpscap(txt[3])
            end
        end
    end
end)
Window = Library:CreateWindow({
    SidebarCompacted = false,
    SearchbarSize = UDim2.fromScale(0.5, 1),
    Title = 'VTX_Hub',
    Footer = 'version: 1.0.0',
    IconSize = UDim2.fromOffset(40, 40),
    SidebarCompactWidth = 50,
    CornerRadius = 13,
    BackgroundImage = "rbxassetid://0"
})
local Tabs = {
	Main = Window:AddTab("Main"),
    Defence = Window:AddTab("Defence"),
    Visual = Window:AddTab("Visual"),
    Target = Window:AddTab("Target"),
    Server = Window:AddTab("Server"),
    Keybinds = Window:AddTab("Keybinds"),
    Whitelist = Window:AddTab("Whitelist"),
    Blacklist = Window:AddTab("Blacklist"), -- ← Adicionado
	["UI Settings"] = Window:AddTab("UI Settings"),
}

do
    local avatar = Tabs.Visual:AddLeftGroupbox("Just your avatar", "person-standing")

    local avatarview = avatar:AddViewport("Just your avatar", {
        Object = plr.Character,
        Camera = Instance.new("Camera"),
        Interactive = true,
        AutoFocus = true,
        Height = 400,
    })

    plr.CharacterAdded:Connect(function(chara)
        task.wait(1)
        avatarview:SetObject(chara:Clone())
    end)
end
do

local box = Tabs.Visual:AddRightGroupbox("Misc")

box:AddToggle("AntiKickEsp", {
    Text = "Anti Kick Esp",
    Default = false,
    Callback = function(v)
        if v then
            for _,pl in Players:GetPlayers() do
                if pl~=plr then 
                    for i,v in workspace[pl.Name.."SpawnedInToys"]:GetChildren() do
                        if v:FindFirstChild("StickyPart") then
                            local high = Instance.new("Highlight", v)
                            high.Adornee = v
                            if v.StickyPart.StickyWeld.Part1 then
                                high.FillColor = Color3.fromRGB(192, 0, 0)
                            else
                                high.FillColor = Color3.fromRGB(182, 0, 0)
                            end
                            v.StickyPart.StickyWeld:GetPropertyChangedSignal("Part1"):Connect(function()
                                if v.StickyPart.StickyWeld.Part1 then
                                    high.FillColor = Color3.fromRGB(192, 173, 0)
                                else
                                    high.FillColor = Color3.fromRGB(0, 194, 0)
                                end
                            end)
                        end
                    end
                    cons["antikickesp"..pl.Name] = workspace[pl.Name.."SpawnedInToys"].ChildAdded:Connect(function(v)
                        task.wait(0.4)
                        if v:FindFirstChild("StickyPart") then
                            local high = Instance.new("Highlight", v)
                            high.Adornee = v
                            if v.StickyPart.StickyWeld.Part1 then
                                high.FillColor = Color3.fromRGB(255, 0, 0)
                            else
                                high.FillColor = Color3.fromRGB(255, 0, 0)
                            end
                            v.StickyPart.StickyWeld:GetPropertyChangedSignal("Part1"):Connect(function()
                                if v.StickyPart.StickyWeld.Part1 then
                                    high.FillColor = Color3.fromRGB(255, 0, 0)
                                else
                                    high.FillColor = Color3.fromRGB(255, 0, 0)
                                end
                            end)
                        end
                    end)
                end
            end
        else
            for i,v in Players:GetPlayers() do
                if v~=plr then
                    if cons["antikickesp"..v.Name] then cons["antikickesp"..v.Name]:Disconnect() cons["antikickesp"..v.Name] = nil end
                    for i,v in workspace[v.Name.."SpawnedInToys"]:GetChildren() do
                        if v:FindFirstChild("StickyPart") then
                            if v.Parent:FindFirstChild("Highlight") then v.Parent.Highlight:Destroy() end
                        end
                    end
                end
            end
        end
    end
})
box:AddSlider("PCLDTransparency", {
    Text = "Transparency",
    Default = 0.6,
    Min = 0,
    Max = 1,
    Rounding = 1
})

box:AddToggle("ViewPCLD", {
    Text = "View PCLD(off>>on to update)",
    Default = false,
    Callback = function(v)
        if v then
            local trans = Options.PCLDTransparency.Value
            for i,v in pairs(workspace:GetChildren()) do
                if v.Name == "PlayerCharacterLocationDetector" then
                    v.Transparency = trans
                end
            end
            cons["viewpcld"] = workspace.ChildAdded:Connect(function(child)
                if child.Name == "PlayerCharacterLocationDetector" then
                    child.Transparency = trans
                end
            end)
        else
            if cons["viewpcld"] then cons["viewpcld"]:Disconnect() cons["viewpcld"] = nil end
            for i,v in pairs(workspace:GetChildren()) do
                if v.Name == "PlayerCharacterLocationDetector" then
                    v.Transparency = 1
                end
            end
        end
    end
})
local Players = game:GetService("Players")

local function AddESP(plr)
    if plr == Players.LocalPlayer then return end

    local function Apply(char)
        if char:FindFirstChild("VTX_ESP") then return end

        -- Highlight
        local hl = Instance.new("Highlight")
        hl.Name = "VTX_ESP"
        hl.FillTransparency = 1
        hl.OutlineTransparency = 0
        hl.OutlineColor = Color3.fromRGB(255,255,255)
        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        hl.Parent = char

        -- Nome
        local head = char:FindFirstChild("Head")
        if head then
            local bill = Instance.new("BillboardGui")
            bill.Name = "VTX_NameESP"
            bill.Size = UDim2.new(0, 200, 0, 40)
            bill.StudsOffset = Vector3.new(0, 2.5, 0)
            bill.AlwaysOnTop = true
            bill.Parent = head

            local txt = Instance.new("TextLabel")
            txt.Size = UDim2.new(0.5, 0, 0.5, 0)
            txt.BackgroundTransparency = 1
            txt.Text = plr.Name
            txt.TextColor3 = Color3.fromRGB(255,255,255)
            txt.TextStrokeTransparency = 0
            txt.TextScaled = true
            txt.Font = Enum.Font.SourceSansBold
            txt.Parent = bill
        end
    end

    if plr.Character then
        Apply(plr.Character)
    end

    plr.CharacterAdded:Connect(Apply)
end

local function RemoveESP()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character then
            local esp = plr.Character:FindFirstChild("VTX_ESP")
            if esp then esp:Destroy() end

            local head = plr.Character:FindFirstChild("Head")
            if head then
                local nameesp = head:FindFirstChild("VTX_NameESP")
                if nameesp then nameesp:Destroy() end
            end
        end
    end
end
box:AddToggle("PlayerESP", {
    Text = "Player ESP",
    Default = false,
    Callback = function(v)
        if v then
            for _, plr in ipairs(game.Players:GetPlayers()) do
                AddESP(plr)
            end
        else
            RemoveESP()
        end
    end
})
end

do
local box = Tabs.Defence:AddLeftGroupbox("Defence")
box:AddToggle("AntiGrab",{
    Text = "Anti Grab",
    Default = false,
    Callback = function(v)
        if v then
            antig = plr.IsHeld.Changed:Connect(function()
                if plr.IsHeld.Value then
                    local plrchar = plr.Character
                    Struggle:FireServer()
                    plrchar.HumanoidRootPart.Anchored = true
                    plrchar.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                    plrchar.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
                    repeat Struggle:FireServer() task.wait() until plr.IsHeld.Value ~= true
                    plrchar.HumanoidRootPart.Anchored = false
                end
            end)
        else
            antig:Disconnect()
        end
    end
})
-- ==================== MELHORES SHADERS 2026 (VTX_Hub) ====================
do
    local box = Tabs.Visual:AddRightGroupbox("Best Shaders 2026", "sparkles")

    local Lighting = game:GetService("Lighting")
    
    local original = {
        Brightness = Lighting.Brightness,
        Ambient = Lighting.Ambient,
        OutdoorAmbient = Lighting.OutdoorAmbient,
        ClockTime = Lighting.ClockTime,
        GlobalShadows = Lighting.GlobalShadows,
        FogEnd = Lighting.FogEnd,
        FogStart = Lighting.FogStart,
    }

    local effects = {}
    local currentShader = nil

    local function CreateEffects()
        effects.Bloom = Lighting:FindFirstChild("VTX_Bloom") or Instance.new("BloomEffect")
        effects.Color = Lighting:FindFirstChild("VTX_Color") or Instance.new("ColorCorrectionEffect")
        effects.Atmosphere = Lighting:FindFirstChild("VTX_Atmosphere") or Instance.new("Atmosphere")
        effects.DOF = Lighting:FindFirstChild("VTX_DOF") or Instance.new("DepthOfFieldEffect")
        effects.SunRays = Lighting:FindFirstChild("VTX_SunRays") or Instance.new("SunRaysEffect")

        for _, eff in pairs(effects) do
            eff.Name = "VTX_" .. eff.ClassName
            eff.Parent = Lighting
        end
    end

    local function DestroyAll()
        for _, eff in pairs(effects) do
            if eff and eff.Parent then eff:Destroy() end
        end
        effects = {}

        Lighting.Brightness = original.Brightness
        Lighting.Ambient = original.Ambient
        Lighting.OutdoorAmbient = original.OutdoorAmbient
        Lighting.ClockTime = original.ClockTime
        Lighting.GlobalShadows = original.GlobalShadows
        Lighting.FogEnd = original.FogEnd
        Lighting.FogStart = original.FogStart

        currentShader = nil
    end

    DestroyAll()

    -- ==================== PRESETS PREMIUM ====================

    local presets = {}

    presets.UltraRTX = function()
        CreateEffects()
        Lighting.Brightness = 2.85
        Lighting.ClockTime = 14.8
        Lighting.GlobalShadows = true
        Lighting.Ambient = Color3.fromRGB(95, 105, 125)
        Lighting.OutdoorAmbient = Color3.fromRGB(115, 125, 145)

        effects.Bloom.Intensity = 1.95
        effects.Bloom.Size = 28
        effects.Bloom.Threshold = 0.78

        effects.Color.Brightness = 0.09
        effects.Color.Contrast = 0.18
        effects.Color.Saturation = 0.92
        effects.Color.TintColor = Color3.fromRGB(225, 240, 255)

        effects.Atmosphere.Density = 0.23
        effects.Atmosphere.Offset = 0.25
        effects.Atmosphere.Color = Color3.fromRGB(160, 190, 255)
        effects.Atmosphere.Decay = Color3.fromRGB(110, 140, 235)
        effects.Atmosphere.Glare = 0.45
        effects.Atmosphere.Haze = 1.15

        effects.DOF.FocusDistance = 180
        effects.DOF.InFocusRadius = 140
        effects.DOF.NearIntensity = 0.02
        effects.DOF.FarIntensity = 0.06

        effects.SunRays.Intensity = 0.28
        effects.SunRays.Spread = 0.95
    end

    presets.CinematicRealistic = function()
        CreateEffects()
        Lighting.ClockTime = 15.5
        Lighting.Brightness = 3.4

        effects.Bloom.Intensity = 1.55
        effects.Bloom.Size = 24
        effects.Bloom.Threshold = 0.82

        effects.Color.Brightness = 0.06
        effects.Color.Contrast = 0.28
        effects.Color.Saturation = 0.88
        effects.Color.TintColor = Color3.fromRGB(255, 245, 235)

        effects.Atmosphere.Density = 0.19
        effects.Atmosphere.Haze = 0.75
        effects.Atmosphere.Glare = 0.3
    end

    presets.EtherealDream = function()
        CreateEffects()
        Lighting.ClockTime = 13.2
        Lighting.Brightness = 2.9        -- Reduzido
        Lighting.Ambient = Color3.fromRGB(130, 145, 170)

        effects.Bloom.Intensity = 1.75   -- Reduzido
        effects.Bloom.Size = 26
        effects.Bloom.Threshold = 0.72

        effects.Color.Brightness = 0.08
        effects.Color.Contrast = 0.15
        effects.Color.Saturation = 1.25
        effects.Color.TintColor = Color3.fromRGB(225, 235, 255)

        effects.Atmosphere.Density = 0.26
        effects.Atmosphere.Offset = 0.3
        effects.Atmosphere.Color = Color3.fromRGB(195, 215, 255)
        effects.Atmosphere.Decay = Color3.fromRGB(170, 200, 255)
        effects.Atmosphere.Glare = 0.4     -- Reduzido
        effects.Atmosphere.Haze = 1.25

        effects.SunRays.Intensity = 0.22   -- Reduzido
        effects.SunRays.Spread = 0.75      -- Sol menor
    end

    presets.DivineGoldenHour = function()
        CreateEffects()
        Lighting.ClockTime = 17.8
        Lighting.Brightness = 2.95         -- Reduzido

        effects.Bloom.Intensity = 1.65     -- Reduzido
        effects.Bloom.Size = 24
        effects.Bloom.Threshold = 0.75

        effects.Color.Brightness = 0.11
        effects.Color.Contrast = 0.20
        effects.Color.Saturation = 1.08
        effects.Color.TintColor = Color3.fromRGB(255, 190, 115)

        effects.Atmosphere.Density = 0.29
        effects.Atmosphere.Color = Color3.fromRGB(255, 165, 75)
        effects.Atmosphere.Decay = Color3.fromRGB(255, 135, 55)
        effects.Atmosphere.Haze = 1.0
        effects.Atmosphere.Glare = 0.42    -- Reduzido

        effects.SunRays.Intensity = 0.26   -- Reduzido
        effects.SunRays.Spread = 0.8       -- Sol menor
    end

    presets.Photorealistic = function()
        CreateEffects()
        Lighting.ClockTime = 14
        Lighting.Brightness = 2.65
        Lighting.GlobalShadows = true

        effects.Bloom.Intensity = 1.35
        effects.Bloom.Size = 22
        effects.Bloom.Threshold = 0.9

        effects.Color.Brightness = 0.04
        effects.Color.Contrast = 0.32
        effects.Color.Saturation = 0.78
        effects.Color.TintColor = Color3.fromRGB(245, 240, 235)

        effects.Atmosphere.Density = 0.145
        effects.Atmosphere.Offset = 0.1
        effects.Atmosphere.Haze = 0.6
        effects.Atmosphere.Glare = 0.25

        effects.DOF.FocusDistance = 250
        effects.DOF.InFocusRadius = 180
        effects.DOF.NearIntensity = 0.015
        effects.DOF.FarIntensity = 0.08
    end

    presets.CelestialBloom = function()
        CreateEffects()
        Lighting.ClockTime = 16.5
        Lighting.Brightness = 3.1          -- Reduzido

        effects.Bloom.Intensity = 1.95     -- Reduzido
        effects.Bloom.Size = 29
        effects.Bloom.Threshold = 0.68

        effects.Color.Brightness = 0.13
        effects.Color.Contrast = 0.16
        effects.Color.Saturation = 1.32
        effects.Color.TintColor = Color3.fromRGB(205, 230, 255)

        effects.Atmosphere.Density = 0.24
        effects.Atmosphere.Color = Color3.fromRGB(175, 205, 255)
        effects.Atmosphere.Decay = Color3.fromRGB(135, 180, 255)
        effects.Atmosphere.Glare = 0.48    -- Reduzido
        effects.Atmosphere.Haze = 1.35

        effects.SunRays.Intensity = 0.24   -- Reduzido
        effects.SunRays.Spread = 0.72      -- Sol menor
    end

    -- ==================== TOGGLES ====================

    local function ActivateShader(name)
        if currentShader == name then return end
        DestroyAll()
        presets[name]()
        currentShader = name
    end

    local toggles = {}

    toggles.UltraRTX = box:AddToggle("UltraRTX", { 
        Text = "Ultra RTX (Melhor Geral)", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("UltraRTX") Library:Notify("Ultra RTX Ativado 🔥", 4)
            elseif currentShader == "UltraRTX" then DestroyAll() end
        end})

    toggles.Cinematic = box:AddToggle("Cinematic", { 
        Text = "Cinematic Realistic", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("CinematicRealistic") Library:Notify("Cinematic Realistic Ativado", 4)
            elseif currentShader == "CinematicRealistic" then DestroyAll() end
        end})

    toggles.Ethereal = box:AddToggle("Ethereal", { 
        Text = "Ethereal Dream ✨", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("EtherealDream") Library:Notify("Ethereal Dream Ativado", 4)
            elseif currentShader == "EtherealDream" then DestroyAll() end
        end})

    toggles.GoldenHour = box:AddToggle("GoldenHour", { 
        Text = "Divine Golden Hour", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("DivineGoldenHour") Library:Notify("Divine Golden Hour Ativado", 4)
            elseif currentShader == "DivineGoldenHour" then DestroyAll() end
        end})

    toggles.Photorealistic = box:AddToggle("Photorealistic", { 
        Text = "Photorealistic", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("Photorealistic") Library:Notify("Photorealistic Ativado", 4)
            elseif currentShader == "Photorealistic" then DestroyAll() end
        end})

    toggles.Celestial = box:AddToggle("Celestial", { 
        Text = "Celestial Bloom", 
        Default = false, 
        Callback = function(v)
            if v then ActivateShader("CelestialBloom") Library:Notify("Celestial Bloom Ativado", 4)
            elseif currentShader == "CelestialBloom" then DestroyAll() end
        end})

    -- ==================== CONTROLES FINAIS ====================

    box:AddSlider("Brightness", {
        Text = "Global Brightness",
        Default = 1,
        Min = 0,
        Max = 5,
        Rounding = 1,
        Callback = function(v) Lighting.Brightness = v end
    })

    box:AddToggle("NoDOF", {
        Text = "Remover Embaçado (DOF)",
        Default = true,
        Callback = function(v)
            if effects.DOF then
                effects.DOF.NearIntensity = v and 0 or 0.35
                effects.DOF.FarIntensity = v and 0 or 0.55
            end
        end
    })

    box:AddButton("Reset All Shaders", function()
        DestroyAll()
        for _, toggle in pairs(toggles) do
            toggle:SetValue(false)
        end
        Library:Notify("Todos os shaders resetados", 3)
    end)
end

box:AddToggle("AutoReset", {
    Text = "Auto Reset",
    Default = false,
    Callback = function(v)
        if v then
            cons["AutoReset"] = rs.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    Library:Notify("Reset", 4)
                    hum:ChangeState("Dead")
                end
            end)
        else
            if cons["AutoReset"] then cons["AutoReset"]:Disconnect() end
        end
    end
})
Toggles.AutoReset:SetValue(true)
box:AddToggle("AntiInput", {
    Text = "Anti Input Lag",
    Default = false,
    Callback = function(Value)
        _G.AntiInputLag = Value
        if not Value then return end
        task.spawn(function()
            local plr = Players.LocalPlayer
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart")
            local SpawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
            local SelectedToyName = SelectedToy

            while _G.AntiInputLag do
                local toysFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if not toysFolder then
                    R.Heartbeat:Wait()
                    continue
                end

                local toy = toysFolder:FindFirstChild(SelectedToyName)
                if not toy then
                    pcall(function()
                        SpawnRemote:InvokeServer(SelectedToyName, hrp.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
                    end)
                    R.Heartbeat:Wait()
                else
                    local holdPart = toy:FindFirstChild("HoldPart")
                    if holdPart then
                        local holdingPlayer = holdPart:FindFirstChild("HoldingPlayer") and holdPart.HoldingPlayer.Value
                        if holdingPlayer and holdingPlayer ~= plr then
                            pcall(function()
                                holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 2000, 0), Vector3.zero)
                            end)
                            toy:Destroy()
                        else
                            while _G.AntiInputLag do
                                pcall(function()
                                    holdPart.HoldItemRemoteFunction:InvokeServer(toy, char)
                                    holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 5000, 0), Vector3.zero)
                                end)
                                if not toy or not toy.Parent or not toysFolder:FindFirstChild(SelectedToyName) then
                                    break
                                end
                               R.Heartbeat:Wait()
                            end
                        end
                    else
                        pcall(function()
                            if toy and toy.Parent then toy:Destroy() end
                            SpawnRemote:InvokeServer(SelectedToyName, hrp.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
                        end)
                        R.Heartbeat:Wait()
                    end
                end
            end
        end)
    end
})   

	local waterParts = {}
	task.spawn(function()
		if workspace:FindFirstChild("Map") and workspace.Map:FindFirstChild("AlwaysHereTweenedObjects") then
			local oceanModel = workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
			for _, v in pairs(oceanModel:GetChildren()) do
				if v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("BasePart") or v:IsA("MeshPart") then
				table.insert(waterParts, {part = v, originalCollide = v.CanCollide})
				end
			end
		end
	end)

box:AddToggle("AntiPaint", {
    Text = "Anti Paint",
    Default = false,
    Callback = function(v)
        if v then
			antipcon = workspace.DescendantAdded:Connect(function(d)
				if d.Name == "PaintPlayerPart" then
					task.wait(0.1)
					d:Destroy()
				end
			end)
			for i, v in pairs(workspace:GetDescendants()) do
				if v.Name == "PainPlayerPart" then
					v:Destroy()
				end
			end
		else
			if antipcon then
				antipcon:Disconnect()
			end
		end
    end
})
box:AddToggle("GucciTractor", {
    Text = "Gucci(Invisible)",
    Default = false,
    Callback = function(v)
        if v then
            local blobb
            pcall(function()
                local pal, pal2
                pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                    if c.Name == "TractorGreen" then
                        pal = c
                        task.wait()
                        pal2:Disconnect()
                        pal2 = nil
                    end
                end)
                spawn(function()
                    task.wait(0.2)
                    local mess = pal.ViewItemButton.NewMessage:Clone()
                    mess.Name = "Gucci2"
                    mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                    mess.Text = "Anti Gucci"
                    mess.Visible = true
                    mess.Parent = pal.ViewItemButton
                end)
            end)
            blobb = spawntoy("TractorGreen", HRP.CFrame * CFrame.new(5, 5, 20))
            blobb.Name = "tractorgucci"
            repeat task.wait() until blobb
            blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait()
            while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            sno(blobb.Part)
            task.wait(0.2)
            blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
        else
            DestroyToy:FireServer(inv.tractorgucci)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("GucciTrain", {
    Text = "Gucci(Train)",
    Default = false,
    Callback = function(v)
        if v then
            local pos = HRP.CFrame
            workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.Seat:Sit(hum)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait(0.1)
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait(0.1)
            HRP.CFrame = pos
        else
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("GucciBlobman", {
    Text = "Gucci(Blobman)",
    Default = false,
    Callback = function(v)
        if v then
            local blobb
            pcall(function()
                local pal, pal2
                pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                    if c.Name == "CreatureBlobman" then
                        pal = c
                        task.wait()
                        pal2:Disconnect()
                        pal2 = nil
                    end
                end)
                spawn(function()
                    task.wait(1)
                    local mess = pal.ViewItemButton.NewMessage:Clone()
                    mess.Name = "Gucci1"
                    mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                    mess.Text = "Anti Gucci"
                    mess.Visible = true
                    mess.Parent = pal.ViewItemButton
                end)
            end)
            blobb = spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
            repeat task.wait() until blobb
            blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
            task.spawn(function()
                local endTime = tick() + 3
                while tick() < endTime do
                    Ragdoll:FireServer(HRP, 0)
                    task.wait()
                end
            end)
            task.wait()
            while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait()
            blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
        else
            DestroyToy:FireServer(inv.CreatureBlobman)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})

box:AddToggle("AutoGucciBlob", {
    Text = "Auto Gucci(blobman)",
    Default = false,
    Callback = function(v)
        autogucci = v
        if v then
            local function gucci()
                if not autogucci then return end
                local blobb
                repeat task.wait() until plr.IsHeld.Value == false
                hum.Sit = true
                task.wait(0.1)
                hum.Sit = false
                pcall(function()
                    local pal, pal2
                    pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                        if c.Name == "CreatureBlobman" then
                            pal = c
                            task.wait()
                            pal2:Disconnect()
                            pal2 = nil
                        end
                    end)
                    spawn(function()
                        task.wait(0.1)
                        local mess = pal.ViewItemButton.NewMessage:Clone()
                        mess.Name = "Gucci1"
                        mess.TextColor3 = Color3.fromRGB(255, 255, 255)
                        mess.Text = "Anti Gucci"
                        mess.Visible = true
                        mess.Parent = pal.ViewItemButton
                    end)
                end)
                if inv:FindFirstChild("autogucci") then DestroyToy:FireServer(inv.autogucci) end
                blobb = spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                repeat task.wait() until blobb
                blobb.Name = "autogucci"
                blobb:WaitForChild("VehicleSeat", 3):Sit(plr.Character.Humanoid)
                task.spawn(function()
                    local endTime = tick() + 3
                    while tick() < endTime do
                        Ragdoll:FireServer(HRP, 0)
                        task.wait()
                    end
                end)
                cons["autogucci"] = blobb.Destroying:Once(function()
                    gucci()
                end)
                task.wait()
                while blobb.VehicleSeat.Occupant ~= plr.Character.Humanoid do task.wait() end
                plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                task.wait()
                repeat task.wait() sno(blobb.RightDetector) until blobb.Head:FindFirstChild("PartOwner")
                task.wait(0.1)
                blobb.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0)
            end
            task.spawn(function()
                while autogucci and task.wait(0.1) do
                    if hum:GetState() == Enum.HumanoidStateType.Dead or not isnetworkowner(HRP) or not inv:FindFirstChild("autogucci") or plr.IsHeld.Value then
                        gucci()
                    end
                end
            end)
        else
            if cons["autogucci"] then 
                cons["autogucci"]:Disconnect() 
                cons["autogucci"] = nil
            end
            DestroyToy:FireServer(inv.autogucci)
            for i = 1, 30 do
                hum.Sit = true
                task.wait()
                hum.Sit = false
            end
        end
    end
})
box:AddToggle("BreakPCLD", {
    Text = "Break PCLD",
    Default = false,

    Callback = function(Value)
        if Value then
            print("BREAK PCLD ativado")

            -- Seu código aqui
local resets = 0
local maxResets = 2
local player = game.Players.LocalPlayer

local function resetCharacter()
    if resets < maxResets then
        resets += 1
        
        task.wait(0.2) -- velocidade do reset
        
        if player.Character then
            player.Character:BreakJoints()
        end
    end
end

player.CharacterAdded:Connect(function()
    resetCharacter()
end)

resetCharacter()
local player = game.Players.LocalPlayer
local autoReset = false

player.CharacterAdded:Connect(function(character)
    if autoReset then
        autoReset = false
        return
    end

    autoReset = true

    task.wait(0.2)

    if character then
        character:BreakJoints()
    end
end)
        else
            print("Break Pcld desativado")
        end
    end
})

box:AddToggle("AntiKill", {
    Text = "Anti Loop Kill",
    Default = false,
    Callback = function(v)
        if v then
            cons["antiloopkill"] = plr.CharacterAdded:Connect(function(char)
                local hrp = char:WaitForChild("HumanoidRootPart")
                hrp.CFrame = CFrame.new(524.7039794921875, 93.71200561523438, -375.0409851074219)
            end)
        else
            disc("antiloopkill")
        end
    end
})

box:AddToggle("AntiKick", {
    Text = "Anti Kick",
    Default = false,
    Callback = function(v)
        task.wait(0.1)
        antikick = v
        if antikick then
            task.spawn(function()
                task.wait(0.1)
                if not inv:FindFirstChild("NinjaShuriken1") then
                    repeat task.wait() until plr.CanSpawnToy.Value
                    local shu
                    local part
                    local plot = getplot()
                    while antikick and task.wait() do
                        pcall(function()
                            local char = plr.Character
                            if not shu or not inv:FindFirstChild("NinjaShuriken1") and not workspace.PlotItems:FindFirstChild("NinjaShuriken1", true) then
                                print(1)
                                shu = spawntoy("NinjaShuriken", HRP.CFrame * CFrame.new(5, 10, 20))
                                shu.Name = "NinjaShuriken1"
                                part = shu:WaitForChild("StickyPart", 0.3)
                                sno(part)
                            end
                            if shu and shu:FindFirstChild("StickyPart") and shu.StickyPart:FindFirstChild("PartOwner") and shu.StickyPart:FindFirstChild("PartOwner").Value ~= plr.Name then
                                print(2)
                                sno(part)
                            end
                            if part and part:FindFirstChild("StickyWeld") and part.StickyWeld.Part1 ~= char.HumanoidRootPart.FirePlayerPart then
                                print(3)
                                sno(part)
                                StickyEvent:FireServer(part, char.HumanoidRootPart.FirePlayerPart, CFrame.new(0,0,0,1,0,0,0,0,-1,0,1,0))
                            end
                            task.wait(0.2)
                            if shu and shu:FindFirstChild("StickyPart") and (part.Position - HRP.Position).Magnitude > 30 then
                                print(4)
                                DestroyToy:FireServer(inv.NinjaShuriken1)
                                shu = spawntoy("NinjaShuriken", HRP.CFrame * CFrame.new(5, 10, 20))
                                shu.Name = "NinjaShuriken1"
                                sno(part)
                            end
                        end)
                    end
                end
            end)
        else
            if inv:FindFirstChild("NinjaShuriken1") then DestroyToy:FireServer(inv.NinjaShuriken1) end
        end
    end
})

box:AddToggle("LoopTp", {
    Text = "Loop Tp",
    Default = false,
    Callback = function(v)
        looptp = v
        local pos = HRP.CFrame
        if v then
            task.wait(0.1)
            task.spawn(function()
                while looptp and task.wait(0.05) do
                    HRP.CFrame = pos * CFrame.new(math.random(-1000, 1000), 0, math.random(-1000, 1000))
                    stvel(HRP)
                end
            end)
        else
            stvel(HRP)
            task.wait(0.1)
            HRP.CFrame = pos
        end
    end
})

box:AddToggle("AntiBurn", {
    Text = "Anti Burn",
    Default = false,
    Callback = function(v)
        if v then
			antiburn1 = plr.CharacterAdded:Connect(function(ch)
				if antiburn then
					antiburn:Disconnect()
                end
				antiburn = ch:WaitForChild("Humanoid", 0.5).FireDebounce.Changed:Connect(function()
					if ch:WaitForChild("Humanoid", 0.5).FireDebounce.Value == true then
                        local bar = workspace.Plots.Plot1.Barrier.PlotBarrier
                        local pos = bar.CFrame
						task.spawn(function()
							repeat task.wait() bar.CFrame = HRP.CFrame until not hum.FireDebounce.Value
						end)
                        task.wait(1)
                        ch:WaitForChild("Humanoid", 0.5).FireDebounce.Value = false
                        task.wait()
                        bar.CFrame = pos
                    end
                end)
            end)
            antiburn = plr.Character.Humanoid.FireDebounce.Changed:Connect(function()
                if plr.Character.Humanoid.FireDebounce.Value == true then
                    local bar = workspace.Plots.Plot1.Barrier.PlotBarrier
                    local pos = bar.CFrame
					task.spawn(function()
                    	repeat task.wait() bar.CFrame = HRP.CFrame until not hum.FireDebounce.Value
					end)
                    task.wait(1)
                    plr.Character.Humanoid.FireDebounce.Value = false
                    task.wait()
                    bar.CFrame = pos
                end
            end)
        else
            if antiburn then antiburn:Disconnect() end
            if antiburn1 then antiburn1:Disconnect() end
        end
    end
})
--vouta
local box = Tabs.Defence:AddRightGroupbox("More")
box:AddToggle("AntiBlobman", {
    Text = "Anti Blobman",
    Default = false,
    Callback = function(v)
        if v then
            for i,v in pairs(workspace:GetDescendants()) do
                if v.Name == "CreatureBlobman" and not v:IsDescendantOf(inv) then 
                    local rd, ld = v:FindFirstChild("RightDetector") or v:WaitForChild("RightDetector", 3), v:FindFirstChild("LeftDetector") or v:WaitForChild("LeftDetector", 3)
                    if rd and ld then 
                        rd.RightAlignOrientation.Enabled = false
                        rd.RightWeld.Enabled = false
                        ld.LeftAlignOrientation.Enabled = false
                        ld.LeftWeld.Enabled = false
                    end
                end
            end
            cons["antiblob"] = workspace.DescendantAdded:Connect(function(d)
                if d.Name == "CreatureBlobman" and (not inv or not d:IsDescendantOf(inv)) then 
                    local rd = d:FindFirstChild("RightDetector") or d:WaitForChild("RightDetector", 3)
                    local ld = d:FindFirstChild("LeftDetector") or d:WaitForChild("LeftDetector", 3)

                    if rd and ld then
                        local rao = rd:WaitForChild("RightAlignOrientation", 1)
                        local rw  = rd:WaitForChild("RightWeld", 1)
                        local lao = ld:WaitForChild("LeftAlignOrientation", 1)
                        local lw  = ld:WaitForChild("LeftWeld", 1)

                        if rao then rao.Enabled = false end
                        if rw  then rw.Enabled  = false end
                        if lao then lao.Enabled = false end
                        if lw  then lw.Enabled  = false end
                    end
                end
            end)
        else
            if cons["antiblob"] then cons["antiblob"]:Disconnect() end
        end
    end
})

box:AddButton("Delete Legs", function()
        if char:FindFirstChild("Left Leg") and char:FindFirstChild("Right Leg") then
            local ll = char:FindFirstChild("Left Leg")
            local rl = char:FindFirstChild("Right Leg")
            local void = workspace.FallenPartsDestroyHeight
            local pos = char.Torso.CFrame
            workspace.FallenPartsDestroyHeight = -100
            Ragdoll:FireServer(HRP, 2)
            task.wait(0.5)
            rl.CFrame = CFrame.new(0, -10000, 0)
            ll.CFrame = CFrame.new(0, -10000, 0)
            task.wait(0.3)
            char.Torso.CFrame = CFrame.new(0, -9970, 0)
            task.wait(0.5)
            char.Torso.CFrame = pos
            task.wait(0.5)
            workspace.FallenPartsDestroyHeight = void
            task.spawn(function()
                if not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") then
                    while task.wait() do
                        if plr.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                            char.Humanoid.HipHeight = 2
                        else
                            char.Humanoid.HipHeight = 0
                        end
                    end
                end
            end)
        end
    end
)

box:AddToggle("AntiLag", {
    Text = "Anti Lag",
    Default = false,
    Callback = function(v)
        Lines = 0
        plr.PlayerScripts.CharacterAndBeamMove.Enabled = not v
    end
})

local lagger
box:AddToggle("AutoAntiLag", {
    Text = "Auto Anti Lag",
    Default = false,
    Callback = function(v)
        autoantilag = v
        if v then
            task.spawn(function()
                while autoantilag and task.wait() do
                    if Lines > 1000 then
                        plr.PlayerScripts.CharacterAndBeamMove.Enabled = false
                        Library:Notify({
                            Title = "Auto Anti Lag Notify",
                            Description = lagger.Name.." Lagged Server",
                            Time = 6.5,
                        })
                        Lines = 0
                    end
                end
            end)
        else
            plr.PlayerScripts.CharacterAndBeamMove.Enabled = true
        end
    end
})
workspace.DescendantAdded:Connect(function(d)
    if d.Name == "GrabBeam" then
        Lines += 1
        lagger = d.Parent.Parent.Parent
    end
end)

box:AddToggle("AntiSticky", {
    Text = "Anti Sticky",
    Default = false,
    Callback = function(v)
        plr.PlayerScripts.StickyPartsTouchDetection.Enabled = not v
    end
})

box:AddToggle("AntiExplode", {
    Text = "Anti Explode",
    Default = false,
    Callback = function(v)
        if v then
            cons["antiexp"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "Part" then
                    if (c.Position - HRP.Position).Magnitude < 40 and plr.Character.Humanoid.Ragdolled.Value == true then
						HRP.Anchored = true
                        task.wait(0.01)
                        HRP.Anchored = false
                        stvel(HRP)
                        hum:ChangeState(Enum.HumanoidStateType.Running)
                    end
                end
            end)
		else
			if cons["antiexp"] then cons["antiexp"]:Disconnect() end
        end
    end
})

box:AddToggle("AntiVoid", {
    Text = "Anti Void",
    Default = false,
    Callback = function(v)
        if v then
            workspace.FallenPartsDestroyHeight = 0/0
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end
})
box:AddButton("Teleportes House", function()

    local Players = game:GetService("Players")
    local UIS = game:GetService("UserInputService")
    local plr = Players.LocalPlayer

    local existing = plr.PlayerGui:FindFirstChild("HouseTeleportUI")

    if existing then
        existing.Enabled = not existing.Enabled
        return
    end

    local locations = {
        ["Blue House"] = Vector3.new(510, 83, -339),
        ["China House"] = Vector3.new(554, 123, -72),
        ["Green House"] = Vector3.new(-535, -7, 93),
        ["Purple House"] = Vector3.new(250, -6, 463),
        ["Pink House"] = Vector3.new(-491, -7, -166)
    }

    local gui = Instance.new("ScreenGui")
    gui.Name = "HouseTeleportUI"
    gui.ResetOnSpawn = false
    gui.Parent = plr.PlayerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0,220,0,260)
    frame.Position = UDim2.new(0.05,0,0.2,0)
    frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local corner = Instance.new("UICorner")
    corner.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1,0,0,35)
    title.BackgroundColor3 = Color3.fromRGB(10,10,10)
    title.Text = "Teleportes House"
    title.TextColor3 = Color3.new(1,1,1)
    title.Font = Enum.Font.GothamBold
    title.TextSize = 15
    title.Parent = frame

    local titleCorner = Instance.new("UICorner")
    titleCorner.Parent = title

    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0,30,0,30)
    close.Position = UDim2.new(1,-35,0,2)
    close.BackgroundColor3 = Color3.fromRGB(170,0,0)
    close.Text = "X"
    close.TextColor3 = Color3.new(1,1,1)
    close.Font = Enum.Font.GothamBold
    close.TextSize = 14
    close.Parent = frame

    local closeCorner = Instance.new("UICorner")
    closeCorner.Parent = close

    close.MouseButton1Click:Connect(function()
        gui.Enabled = false
    end)

    local container = Instance.new("Frame")
    container.Size = UDim2.new(1,-10,1,-45)
    container.Position = UDim2.new(0,5,0,40)
    container.BackgroundTransparency = 1
    container.Parent = frame

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0,5)
    layout.Parent = container

    local function TP(pos)
        local char = plr.Character or plr.CharacterAdded:Wait()
        local hrp = char:WaitForChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(pos)
    end

    for name,pos in pairs(locations) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1,0,0,35)
        btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
        btn.TextColor3 = Color3.new(1,1,1)
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 14
        btn.Text = name
        btn.Parent = container

        local btnCorner = Instance.new("UICorner")
        btnCorner.Parent = btn

        btn.MouseButton1Click:Connect(function()
            TP(pos)
        end)
    end

    -- DRAG UI
    local dragging = false
    local dragInput
    local dragStart
    local startPos

    title.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
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

    title.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if dragging and input == dragInput then
            local delta = input.Position - dragStart

            frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    -- TAB abre/fecha
    UIS.InputBegan:Connect(function(input, gp)
        if gp then return end

        if input.KeyCode == Enum.KeyCode.Tab then
            gui.Enabled = not gui.Enabled
        end
    end)

end)

end

do

local box = Tabs.Main:AddLeftGroupbox("Combat")

box:AddToggle("SuperStrength", {
    Text = "Super Strength",
    Default = false,
    Callback = function(v)
        if v then
            local obj
            cons["supstrgetobj"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
                    if part then
                        local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
                        if weld then
                            obj = weld.Part1
                        end
                    end
                end
            end)
			cons["dplrobj"] = workspace.ChildRemoved:Connect(function(c)
				task.wait()
				if c.Name == "GrabParts" then
					obj = nil
				end
			end)
            cons["superstrength"] = UserInputService.InputBegan:Connect(function(inp)
                if inp.UserInputType == Enum.UserInputType.MouseButton2 then
                    if obj then
                        local bv = Instance.new("BodyVelocity", obj)
						bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bv.Velocity = Camera.CFrame.LookVector * strength
						obj = nil
					end
                end
            end)
        else
            if cons["supstrgetobj"] then cons["supstrgetobj"]:Disconnect() end
            if cons["superstrength"] then cons["superstrength"]:Disconnect() end
            if cons["dplrobj"] then cons["dplrobj"]:Disconnect() end
        end
    end
})

box:AddToggle("KillGrab", {
    Text = "Kill Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["KillGrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
                    if part then
                        local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
                        if weld and weld.Part1.Parent:FindFirstChild("HumanoidRootPart") then
                            weld.Part1.Parent.Humanoid:ChangeState("Dead")
                            task.wait(0.1)
                            DestroyLine:FireServer(weld.Part1)
                        end
                    end
                end
            end)
        else
            if cons["KillGrab"] then cons["KillGrab"]:Disconnect() end
        end
    end
})

box:AddToggle("MasslessGrab", {
    Text = "Massless Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["masslessgrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("DragPart") or c:WaitForChild("DragPart", 1)
                    if part then
                        local pos, ori = part:FindFirstChild("AlignPosition") or part:WaitForChild("AlignPosition", 1), part:FindFirstChild("AlignOrientation") or part:WaitForChild("AlignOrientation", 1)
                        if pos and ori then
                            pos.MaxAxesForce = Vector3.new(math.huge, math.huge, math.huge)
                            pos.MaxForce = math.huge
                            pos.Responsiveness = 200
                            ori.Responsiveness = 200
                            ori.MaxTorque = math.huge
                        end
                    end
                end
            end)
        else
            if cons["masslessgrab"] then cons["masslessgrab"]:Disconnect() end
        end
    end
})

box:AddToggle("SpinGrab", {
    Text = "Spin Grab",
    Default = false,
    Callback = function(v)
        spingrab = v
        if spingrab then
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart")

            cons["spingrabConnection"] = workspace.ChildAdded:Connect(function(e)
                if e.Name == "GrabParts" and e:FindFirstChild("GrabPart") then
                    local dragPart = workspace.GrabParts:FindFirstChild("DragPart")
                    if dragPart then
                        local ao = dragPart:FindFirstChild("AlignOrientation")
                        if ao then
                            ao:Destroy()
                        end
                    end
                    local part1 = e.GrabPart:FindFirstChild("WeldConstraint") and e.GrabPart.WeldConstraint.Part1
                    if part1 then
                        while workspace:FindFirstChild("GrabParts") and spingrab and task.wait() do
                            part1.AssemblyAngularVelocity = Vector3.new(0, spinspeed, 0)
                        end
                    end
                end
            end)
        else
            if cons["spingrabConnection"] then
                cons["spingrabConnection"]:Disconnect()
            end
        end
    end
})

box:AddToggle("RagdollGrab", {
    Text = "Ragdoll Grab",
    Default = false,
    Callback = function(v)
        if v then
            local pal, pal2
            pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
                if c.Name == "PalletLightBrown" then
                    pal = c
                    task.wait()
                    pal2:Disconnect()
                    pal2 = nil
                end
            end)
            local ragd = spawntoy("PalletLightBrown", HRP.CFrame * CFrame.new(5, 5, 20))
            local partt = ragd:WaitForChild("SoundPart", 0.1)
            ragd.Name = "ragdoll"
            spawn(function()
                task.wait(1)
                local ragdoll = pal.ViewItemButton.NewMessage:Clone()
                ragdoll.Name = "Ragdoll"
                ragdoll.TextColor3 = Color3.fromRGB(255, 255, 255)
                ragdoll.Text = "Ragdoll Grab"
                ragdoll.Visible = true
                ragdoll.Parent = pal.ViewItemButton
            end)
            repeat sno(partt) task.wait() until partt:FindFirstChild("PartOwner")
            partt.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)
            spawn(function()
                for i,v in pairs(ragd:GetDescendants()) do
                    if v:IsA("Part") then
                        v.Transparency = 1
                        v.CanCollide = false
                    end
                end
            end)
            cons["rgarab1"] = workspace.ChildAdded:Connect(function(c)
                if c.Name == "GrabParts" then
                    local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 3)
                    if part then
                        local obj = part.WeldConstraint.Part1
                        while workspace:FindFirstChild("GrabParts") and task.wait() do
                            if obj then
                                if obj.Parent and obj.Parent:FindFirstChild("HumanoidRootPart") and obj.Parent:FindFirstChild("Humanoid") and obj.Parent.Humanoid:FindFirstChild("Ragdolled") and obj.Parent.Humanoid.Ragdolled.Value == false then
                                    spawn(function()
                                        partt.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                                        partt.CFrame = obj.Parent.HumanoidRootPart.CFrame
                                        task.wait(0.05)
                                        partt.CFrame = CFrame.new(0, 1e9, 0)
                                    end)
                                end
                            end
                        end
                    end
                end
            end)
        else
            if cons["rgarab1"] then cons["rgarab1"]:Disconnect() end
            DestroyToy:FireServer(inv.ragdoll)
        end
    end
})

box:AddToggle("KickGrab", {
    Text = "Kick Grab",
    Default = false,
    Callback = function(v)
        if v then
            cons["KickGrab"] = workspace.ChildAdded:Connect(function(c)
                if c.Name ~= "GrabParts" then return end
                local GrabPart = c:WaitForChild("GrabPart", 0.1)
                task.wait(0.1)
                local part = GrabPart.WeldConstraint.Part1
                if game.Players:FindFirstChild(part.Parent.Name) then
                    while GrabPart and GrabPart.Parent do
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                        DestroyLine:FireServer(part)
                        RunService.RenderStepped:Wait()
                        SetNetOwner:FireServer(part, part.CFrame)
                    end
                end
            end)
        else
            if cons["KickGrab"] then cons["KickGrab"]:Disconnect() cons["KickGrab"] = nil end
        end
    end
})

box:AddLabel("Auras")
local KillAura, BangAura, FlingAura
box:AddToggle("KillAura", {
    Text = "Kill Aura",
    Default = false,
    Callback = function(v)
        KillAura = v
        if v then
            task.spawn(function()
                while KillAura and RunService.Heartbeat:Wait() do
                    for i,v in Players:GetPlayers() do
                        if v ~= plr then
                            if v.Character and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and (v.Character.HumanoidRootPart.Position - HRP.Position).Magnitude < 30 and not (WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                                sno(v.Character.HumanoidRootPart)
                                v.Character.Humanoid:ChangeState("Dead")
                                DestroyLine:FireServer(v.Character.HumanoidRootPart)
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Main:AddRightGroupbox("Settings", "wrench")

box:AddSlider("Strength", {
    Text = "Strength",
    Default = 300,
    Min = 300,
    Max = 40000,
    Rounding = 1,
    Callback = function(v)
        strength = v
    end
})

box:AddSlider("SpinSpeed", {
    Text = "Spin Speed",
    Default = 500,
    Min = 10,
    Max = 1000,
    Rounding = 1,
    Callback = function(v)
        spinspeed = v
    end
})

box:AddSlider("JerkSpeed", {
    Text = "Jerk Interval",
    Default = 0.1,
    Min = 0.01,
    Max = 1,
    Rounding = 11,
    Callback = function(v)
        jerkspeed = v
    end
})

box:AddLabel("Jerk Bind"):AddKeyPicker("JerkBind", {
    Default = "Q",
    NoUI = false,
    Text = "Jerk Bind"
})

end

do
local box = Tabs.Main:AddLeftGroupbox("Misc")

box:AddToggle("WaterWalk", {
    Text = "Water Walk",
    Default = false,
    Callback = function(v)
        for i,vv in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
            if vv.Name == "Ocean" then
                vv.CanCollide = v
            end
        end
    end
})

box:AddToggle("ThirdPerson", {
    Text = "Unlock Third Person",
    Default = false,
    Callback = function(v)
        local thirdp = v
        if v then
			plr.CameraMaxZoomDistance = 100000
			plr.CameraMode = Enum.CameraMode.Classic
            task.spawn(function()
                while thirdp and task.wait(0.1) do
                    local chara = plr.Character
                    if chara then
                        for i,v in pairs(chara:GetChildren()) do
                            if v:IsA("Part") and v.Name ~= "HumanoidRootPart" and v.Name ~= "CamPart" and HasProperty(v, "Transparency") then
                                v.Transparency = 0
                            end
                            if v:IsA("Accessory") and v.Name ~= "TypingKeyboardMyWorld" then
                                if v:FindFirstChildOfClass("Part") then
                                    v:FindFirstChildOfClass("Part").Transparency = 0
                                end
                            end
                        end
                    end
                end
            end)
        else
            plr.CameraMode = Enum.CameraMode.LockFirstPerson
        end
    end
})
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local plr = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ShiftLockEnabled = false
local ShiftLockKey = Enum.KeyCode.LeftShift -- tecla padrão

local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local ShiftLock = false

box:AddToggle("ShiftLock", {
    Text = "Shift Lock (recommended) ",
    Default = false,
    Callback = function(v)
        ShiftLock = v

        if not v then
            UIS.MouseBehavior = Enum.MouseBehavior.Default
        end
    end
})

RunService.RenderStepped:Connect(function()
    if ShiftLock then
        UIS.MouseBehavior = Enum.MouseBehavior.LockCenter
    end
end)
box:AddToggle("JerkOff", {
    Text = "Jerk Off",
    Default = false,
    Callback = function(v)
        if v then
            local anim = Instance.new("Animation")
            local JerkFlag = nil
            local timepos = nil

            local screenGui = Instance.new("ScreenGui", plr:WaitForChild("PlayerGui"))
            local jerk = Instance.new("TextLabel", screenGui)
            screenGui.ResetOnSpawn = false

            jerk.Size = UDim2.new(0.1, 0, 0.015, 0)
            jerk.Position = UDim2.new(0.458, 0, 0.477, 0)
            jerk.Text = 'Jerk'
            jerk.TextStrokeColor3 = Color3.new(0, 0, 0)
            jerk.BackgroundTransparency = 1
            jerk.TextScaled = true
            jerk.TextColor3 = Color3.new(255, 255, 255)
            jerk.TextStrokeTransparency = 0
            jerk.Visible = false

            local R6 = "rbxassetid://168268306"
            local R15 = "rbxassetid://698251653"

            cons["JerkTool"] = UserInputService.InputBegan:Connect(function(input, g)
                if g then return end
                if input.KeyCode == Enum.KeyCode[Options.JerkBind.Value] then
                    JerkFlag = not(JerkFlag)
                    jerk.Visible = JerkFlag
                    if not(JerkFlag) then jerkoff:Stop(); return end
                    animator = plr.Character:WaitForChild('Humanoid'):WaitForChild("Animator")
                    if plr.Character.Humanoid.RigType == Enum.HumanoidRigType.R6 then anim.AnimationId = R6 else anim.AnimationId = R15 end
                    if anim.AnimationId == R6 then timepos = 0.3 else timepos = 0.55 end
                    jerkoff = animator:LoadAnimation(anim)
                    jerkoff:Play()
                    while task.wait(jerkspeed) and JerkFlag do jerkoff.TimePosition = timepos end
                end
            end)
        else
            if cons["JerkTool"] then cons["JerkTool"]:Disconnect() end
            if jerkoff then jerkoff:Stop() end
            pcall(function()
                plr.PlayerGui:FindFirstChild("ScreenGui"):Destroy()
            end)
        end
    end
})

box:AddLabel("Add To Target List"):AddKeyPicker("AddToTargetList", {
    Default = "LeftAlt",
    NoUI = false,
    Text = "Add To Target List",
    Callback = function()
        local tar = Mouse.Target
        if tar and tar.Parent and game.Players:FindFirstChild(tar.Parent.Name) then
            local pl = game.Players[tar.Parent.Name]
            BlobmanTarget:SetValue(pl.DisplayName.." ("..pl.Name..")")
            GrabTarget:SetValue(pl.DisplayName.." ("..pl.Name..")")
            Library:Notify("New Target! "..pl.DisplayName.." ("..pl.Name..")", 4)
        end
    end
})

box:AddButton("Break Barrier", function()
    local pos = HRP.CFrame
    local t = tick()
    local burg = inv:FindFirstChild("FoodHamburger") or spawntoy("FoodHamburger", HRP.CFrame * CFrame.new(5,5,20))
    task.wait(0.1)
    grab(burg)
    HRP.CFrame = workspace.Waypoints.TudorHouse.CFrame
    task.wait(0.05)
    DestroyToy:FireServer(burg)
    HRP.CFrame = pos
end)

box:AddButton("Bring Train\n(You can use vfly in IY)", function()
    local pos = HRP.CFrame
    local burger = spawntoy("FoodHamburger", HRP.CFrame)
    repeat task.wait() until burger and burger:FindFirstChild("HoldPart")
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.Seat:Sit(hum)
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignPosition.Enabled = false
    workspace.Map.AlwaysHereTweenedObjects.Train.Object.FollowThisPart.AlignOrientation.Enabled = false
    task.wait(0.1)
    grab(burger)
    task.wait(0.1)
    DestroyToy:FireServer(inv.FoodHamburger)
    HRP.CFrame = pos * CFrame.new(0,5,0)
end)

box:AddButton("Ragdoll", function()
    Ragdoll:FireServer(HRP, 1)
end)


box:AddToggle("LoopRagdoll", {
    Text = "Loop Ragdoll",
    Default = false,
    Callback = function(v)
        loopragdoll = v
        if v then 
            task.spawn(function()
                while loopragdoll and task.wait(0.05) do
                    Ragdoll:FireServer(HRP, 0.5)
                end
            end)
        end
    end
})

box:AddToggle("AntiBarrier", {
    Text = "Anti Barrier",
    Default = false,
    Callback = function(val)
        for i,v in pairs(workspace.Plots:GetChildren()) do
            if v:FindFirstChild("Barrier") then
                for i,v in pairs(v.Barrier:GetChildren()) do
                    if v:IsA("Part") then
                        v.CanCollide = not val
                    end
                end
            end
        end
    end
})

box:AddToggle("KickNotify", {
    Text = "Kick Notify",
    Default = false,
    Callback = function(v)
        if v then
            cons["kicknotify"] = game.Players.PlayerRemoving:Connect(function(plrr)
                if workspace:FindFirstChild("BlackHoleKick") then
                    Library:Notify({
                        Title = "VTX_Hub",
                    
                        Description = plrr.Name.."("..plrr.DisplayName..")".." Got Kicked",
                        Time = 4,
                    })
                    workspace:FindFirstChild("BlackHoleKick").Name = plrr.Name.."KICK"
                end
            end)
        else
            if cons["kicknotify"] then cons["kicknotify"]:Disconnect() cons["kicknotify"] = nil end
        end
    end
})

end

do
    local box = Tabs.Main:AddRightGroupbox("Teleport")

    -- Toggle para ligar/desligar o efeito
    local EnableEffect = true
    
    box:AddToggle("EnableTPEffect", {
        Text = "Ativar Efeito de Teleporte",
        Default = true,
        Callback = function(Value)
            EnableEffect = Value
        end
    })

    box:AddLabel("TP To Mouse"):AddKeyPicker("TPTOMOUSE", {
        Default = "Z",
        Text = "Teleport To Mouse",
        Callback = function()
            if Mouse.Target then
                local targetCFrame = Mouse.Hit * CFrame.new(0, 3, 0)
                
                -- Só cria o efeito se estiver ativado
                if EnableEffect then
                    spawn(function()
                        createEndermanTeleportEffect(HRP.Position, targetCFrame.Position)
                    end)
                end
                
                -- Teleporte sempre acontece
                HRP.CFrame = targetCFrame
                stvel(HRP)
            end
        end
    })
end

-- ==================== EFEITO CLIENT-SIDE (SÓ QUEM TEM O SCRIPT VÊ) ====================
local TweenService = game:GetService("TweenService")
local Debris = game:GetService("Debris")

function createEndermanTeleportEffect(startPos, endPos)
    local numPearls = 3

    -- Partículas Enderman na saída
    spawn(function()
        local startParticle = Instance.new("ParticleEmitter")
        startParticle.Texture = "rbxassetid://243098098"
        startParticle.Color = ColorSequence.new(Color3.fromRGB(130, 0, 255), Color3.fromRGB(80, 0, 180))
        startParticle.Size = NumberSequence.new(0.6, 0.1)
        startParticle.Lifetime = NumberRange.new(0.7, 1.4)
        startParticle.Rate = 0
        startParticle.Speed = NumberRange.new(2, 7)
        startParticle.SpreadAngle = Vector2.new(60, 60)
        startParticle.Transparency = NumberSequence.new(0.3, 1)
        startParticle.Acceleration = Vector3.new(0, 3, 0)
        
        local attach = Instance.new("Attachment", workspace.Terrain)
        attach.Position = startPos
        startParticle.Parent = attach
        startParticle:Emit(60)
        Debris:AddItem(attach, 2)
    end)

    for i = 1, numPearls do
        spawn(function()
            local pearl = Instance.new("Part")
            pearl.Shape = Enum.PartType.Ball
            pearl.Size = Vector3.new(0.6, 0.6, 0.6)
            pearl.Color = Color3.fromRGB(170, 0, 255)
            pearl.Material = Enum.Material.Neon
            pearl.Transparency = 0.1
            pearl.Anchored = true
            pearl.CanCollide = false
            pearl.Position = startPos + Vector3.new(math.random(-3,3), math.random(2,5), math.random(-3,3))
            pearl.Parent = workspace

            local trail = Instance.new("Trail")
            trail.Color = ColorSequence.new(Color3.fromRGB(255, 80, 255), Color3.fromRGB(160, 0, 255))
            trail.Transparency = NumberSequence.new(0.2, 1)
            trail.Lifetime = 0.6
            trail.WidthScale = NumberSequence.new(0.9, 0)
            trail.Attachment0 = Instance.new("Attachment", pearl)
            trail.Attachment1 = Instance.new("Attachment", pearl)
            trail.Parent = pearl

            local distance = (endPos - pearl.Position).Magnitude
            local travelTime = math.clamp(distance / 220, 0.2, 0.5)

            local tween = TweenService:Create(pearl, TweenInfo.new(travelTime, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                Position = endPos + Vector3.new(0, 2.5, 0)
            })

            local sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://131057517"
            sound.Volume = 0.7
            sound.Parent = pearl
            sound:Play()

            tween:Play()

            tween.Completed:Connect(function()
                -- Partículas Enderman na chegada
                local arrivalParticle = Instance.new("ParticleEmitter")
                arrivalParticle.Texture = "rbxassetid://243098098"
                arrivalParticle.Color = ColorSequence.new(Color3.fromRGB(140, 0, 255), Color3.fromRGB(90, 0, 190))
                arrivalParticle.Size = NumberSequence.new(0.55, 0.1)
                arrivalParticle.Lifetime = NumberRange.new(0.8, 1.5)
                arrivalParticle.Rate = 0
                arrivalParticle.Speed = NumberRange.new(2, 8)
                arrivalParticle.SpreadAngle = Vector2.new(55, 55)
                arrivalParticle.Transparency = NumberSequence.new(0.3, 1)
                arrivalParticle.Acceleration = Vector3.new(0, 2, 0)
                
                local attach2 = Instance.new("Attachment", workspace.Terrain)
                attach2.Position = endPos
                arrivalParticle.Parent = attach2
                arrivalParticle:Emit(50)
                Debris:AddItem(attach2, 2)

                -- Blocos roxos (espalhando pros lados, curta distância)
                for f = 1, 20 do
                    spawn(function()
                        local block = Instance.new("Part")
                        block.Shape = Enum.PartType.Block
                        block.Size = Vector3.new(0.32, 0.32, 0.32)
                        block.Color = Color3.fromRGB(190, 0, 255)
                        block.Material = Enum.Material.Neon
                        block.Transparency = 0.15
                        block.Anchored = false
                        block.CanCollide = false
                        block.Position = endPos + Vector3.new(math.random(-2,2), math.random(0.5, 3), math.random(-2,2))
                        block.Parent = workspace

                        local bv = Instance.new("BodyVelocity")
                        bv.MaxForce = Vector3.new(4200, 4200, 4200)
                        bv.Velocity = Vector3.new(math.random(-18,18), math.random(5,13), math.random(-18,18))
                        bv.Parent = block

                        local bg = Instance.new("BodyAngularVelocity")
                        bg.AngularVelocity = Vector3.new(math.random(-5,5), math.random(-5,5), math.random(-5,5))
                        bg.MaxTorque = Vector3.new(600,600,600)
                        bg.Parent = block

                        Debris:AddItem(block, 1.1)
                        Debris:AddItem(bv, 0.9)
                    end)
                end

                local impact = Instance.new("Sound")
                impact.SoundId = "rbxassetid://131057517"
                impact.Volume = 0.85
                impact.PlaybackSpeed = 0.8
                impact.Parent = workspace
                impact:Play()
                Debris:AddItem(impact, 2)

                Debris:AddItem(pearl, 1)
            end)
        end)
    end
end


do
local box = Tabs.Target:AddRightGroupbox("Blobman")

local Sets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil
}

BlobmanTarget = box:AddDropdown("Target", {
    Text = "Target",
    Values = {"None"},
    Default = 1,
    Multi = false,
    Callback = function(v)
        Sets.Name = getname(v)
    end
})

box:AddDropdown("Method", {
    Text = "Method",
    Values = {"Lock Target1", "Lock Target2", "Super Lock", "Kill"},
    Default = 0,
    Multi = false,
    Callback = function(v)
        Sets.Method = v
    end
})

box:AddButton("Apply Method", function()
    local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
    if not tt then return end
    local Method = Sets.Method
    local Blob = hum and hum.SeatPart and hum.SeatPart.Parent
    if not Blob or Blob.Name ~= "CreatureBlobman" then return end
    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") then
        Sets.Char = tt.Character
        Sets.HRP = tt.Character.HumanoidRootPart
        if Method == "Lock Target1" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait(0.5)
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                    task.wait(0.5)
                    if isnetworkowner(Sets.HRP) then
                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    end
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CD:FireServer(Blob.RightDetector.RightWeld)
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
            end
        elseif Method == "Lock Target2" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait() 
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                    task.wait()
                    if isnetworkowner(Sets.HRP) then
                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    end
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CD:FireServer(Blob.RightDetector.RightWeld)
            end
        elseif Method == "Super Lock" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                    local pos = HRP.CFrame
                    HRP.CFrame = Sets.HRP.CFrame
                    repeat task.wait() 
                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                        CR:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(Sets.HRP)
                    HRP.CFrame = pos
                end
                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                CR:FireServer(Blob.RightDetector.RightWeld)
                tt.Character.Humanoid:ChangeState("Seated")
                if isnetworkowner(Sets.HRP) then
                    Sets.HRP.CFrame = Blob.RightDetector.CFrame
                    task.spawn(function()
                        for i,v in pairs(Sets.Char:GetChildren()) do
                            if v:IsA("Part") and v.Name ~= "Humanoid" then
                                v.CanCollide = false
                            end
                        end
                    end)
                end
            end
        elseif Method == "Kill" then
            if Blob and Blob.Name == "CreatureBlobman" then
                local CD,CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                local pos = HRP.CFrame
                if tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and tt.Character.Humanoid.Health ~= 0 then
                    Blob.HumanoidRootPart.CFrame = tt.Character.HumanoidRootPart.CFrame
                    task.wait(0.1)
                    repeat task.wait()
                        CG:FireServer(nil, tt.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                        CD:FireServer(Blob.RightDetector.RightWeld)
                    until isnetworkowner(tt.Character.HumanoidRootPart)
                    task.wait()
                    tt.Character.Humanoid:ChangeState("Dead")
                    stvel(HRP)
                    stvel(Blob.HumanoidRootPart)
                    Blob.HumanoidRootPart.CFrame = pos
                end
            end
        end
    end
end)

box:AddToggle("ApplyMethodBlob", {
    Text = "Loop Apply Method",
    Default = false,
    Callback = function(v)
        applymethod = v
        local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
        if not tt then return end
        if v then
            task.spawn(function()
                while applymethod and task.wait(Options.BlobDelay.Value) do
                    tt = Players:FindFirstChild(Sets.Name)
                    local Method = Sets.Method
                    local Blob = hum and hum.SeatPart and hum.SeatPart.Parent
                    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") then
                        Sets.Char = tt.Character
                        Sets.HRP = tt.Character.HumanoidRootPart
                        if Method == "Lock Target1" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                    task.wait()
                                    if isnetworkowner(Sets.HRP) then
                                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    end
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CD:FireServer(Blob.RightDetector.RightWeld)
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                            end
                        elseif Method == "Lock Target2" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                    task.wait()
                                    if isnetworkowner(Sets.HRP) then
                                        Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    end
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CD:FireServer(Blob.RightDetector.RightWeld)
                            end
                        elseif Method == "Super Lock" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CR, CD, CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureDrop, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    local pos = HRP.CFrame
                                    HRP.CFrame = Sets.HRP.CFrame
                                    repeat task.wait() 
                                        CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                        CR:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    HRP.CFrame = pos
                                end
                                CG:FireServer(Blob.RightDetector, Sets.HRP, Blob.RightDetector.RightWeld)
                                CR:FireServer(Blob.RightDetector.RightWeld)
                                tt.Character.Humanoid:ChangeState("Seated")
                                if isnetworkowner(Sets.HRP) then
                                    Sets.HRP.CFrame = Blob.RightDetector.CFrame
                                    task.spawn(function()
                                        for i,v in pairs(Sets.Char:GetChildren()) do
                                            if v:IsA("Part") and v.Name ~= "Humanoid" then
                                                v.CanCollide = false
                                            end
                                        end
                                    end)
                                end
                            end
                        elseif Method == "Kill" then
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local CD,CG = Blob.BlobmanSeatAndOwnerScript.CreatureRelease, Blob.BlobmanSeatAndOwnerScript.CreatureGrab
                                local pos = HRP.CFrame
                                if tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and tt.Character.Humanoid.Health ~= 0 then
                                    Blob.HumanoidRootPart.CFrame = tt.Character.HumanoidRootPart.CFrame
                                    task.wait(0.1)
                                    repeat task.wait()
                                        CG:FireServer(nil, tt.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                                        CD:FireServer(Blob.RightDetector.RightWeld)
                                    until isnetworkowner(tt.Character.HumanoidRootPart)
                                    task.wait()
                                    tt.Character.Humanoid:ChangeState("Dead")
                                    stvel(HRP)
                                    stvel(Blob.HumanoidRootPart)
                                    Blob.HumanoidRootPart.CFrame = pos
                                end
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Target:AddLeftGroupbox("No Blobman")

local Sets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil
}

GrabTarget = box:AddDropdown("Target", {
    Text = "Target",
    Values = {"None"},
    Default = 1,
    Multi = false,
    Callback = function(v)
        Sets.Name = getname(v)
    end
})

box:AddDropdown("Method", {
    Text = "Method",
    Values = {"Loop Grab(Kick)", "Loop Grab", "Kill"},
    Default = 0,
    Multi = false,
    Callback = function(v)
        Sets.Method = v
    end
})

box:AddToggle("ApplyMethodGrab", {
    Text = "Apply Method",
    Default = false,
    Callback = function(v)
        applymethod = v
        local tt = Sets.Name and Players:FindFirstChild(Sets.Name)
        local kickbp, kickbg
        if not tt then return end
        if v then
            task.spawn(function()
                while applymethod and RunService.RenderStepped:Wait() do
                    local pos = HRP.CFrame
                    tt = Players:FindFirstChild(Sets.Name)
                    local Method = Sets.Method
                    if not Method then return end
                    if tt and tt.Character and tt.Character:FindFirstChild("HumanoidRootPart") and HRP then
                        Sets.Char = tt.Character
                        Sets.HRP = tt.Character.HumanoidRootPart
                        if Method == "Loop Grab(Kick)" then
                            if Sets.Char.Parent ~= workspace then
                                local pos = HRP.CFrame
                                local blob = gblob() or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                                repeat task.wait() until blob and blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")
                                blob.Name = "cringe"
                                if blob then
                                    repeat task.wait() blob.VehicleSeat:Sit(hum) until hum.SeatPart == blob.VehicleSeat 
                                    local CG,CD = blob.BlobmanSeatAndOwnerScript.CreatureGrab, blob.BlobmanSeatAndOwnerScript.CreatureRelease
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.2)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.2)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.5)
                                end
                            end
                            if Sets.HRP and hum and HRP then
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                DestroyLine:FireServer(Sets.HRP)
                                RunService.RenderStepped:Wait()
                                SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                                if (Sets.HRP.Position - HRP.Position).Magnitude >= 29 and Sets.Char.Parent == workspace then
                                    task.wait(0.1)
                                    tp(HRP, Sets.HRP)
                                    task.wait(0.2)
                                    sno(Sets.HRP)
                                    task.wait()
                                    HRP.CFrame = pos
                                    task.wait(0.5)
                                    for i,v in pairs(Sets.Char:GetChildren()) do
                                        if v:IsA("Part") and v.Name ~= "Humanoid" then
                                            v.CFrame = pos * offset
                                        end
                                    end
                                end
                                if Sets.HRP.Position.Y < HRP.Position.Y + 4 and Sets.Char.Parent == workspace then
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.5)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(0, 1000, 0)
                                    end
                                end)
                                if not kickbp or kickbp.Parent ~= Sets.HRP then
                                    kickbp = Instance.new("BodyPosition")
                                    kickbp.Parent = Sets.HRP
                                    kickbp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 400
                                    kickbp.Position = HRP.Position + Vector3.new(0,5,0)
                                end
                                if not kickbg or kickbg.Parent ~= Sets.HRP then
                                    kickbg = Instance.new("BodyGyro")
                                    kickbg.Parent = Sets.HRP
                                    kickbg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 200
                                    kickbg.CFrame = CFrame.new(0, 0, 0)
                                end
                                task.spawn(function()
                                    for i,v in Sets.Char:GetChildren() do
                                        if HasProperty(v, "AssemblyLinearVelocity") then
                                            stvel(v)
                                            v.Velocity = Vector3.zero
                                        end
                                    end
                                end)
                                kickbp.Position = HRP.Position + Vector3.new(offset.X,offset.Y,offset.Z)
                            end
                        elseif Method == "Loop Grab" then
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace then
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 30 then
                                    stvel(HRP)
                                    HRP.CFrame = Sets.HRP.CFrame * CFrame.new(0,0,5)
                                    task.wait()
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * CFrame.new(0,15,0)
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.05)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(0, 1000, 0)
                                    end
                                end)
                                if Sets.Char.Head:FindFirstChild("PartOwner") then
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                sno(Sets.HRP)
                            end
                        elseif Method == "Kill" then
                            if Sets.Char.Parent ~= workspace then
                                local pos = HRP.CFrame
                                local blob = gblob() or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20))
                                repeat task.wait() until blob and blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")
                                blob.Name = "cringe"
                                if blob then
                                    repeat task.wait() blob.VehicleSeat:Sit(hum) until hum.SeatPart == blob.VehicleSeat 
                                    local CG,CD = blob.BlobmanSeatAndOwnerScript.CreatureGrab, blob.BlobmanSeatAndOwnerScript.CreatureRelease
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.2)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.2)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.5)
                                end
                            end
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace and Sets.Char.Humanoid.Health ~= 0 and Sets.Char.Torso:FindFirstChild("Neck") then
                                local pos = HRP.CFrame
                                HRP.CFrame = Sets.HRP.CFrame
                                repeat task.wait()
                                    sno(Sets.HRP)
                                until Sets.Char.Head:FindFirstChild("PartOwner") and isnetworkowner(Sets.HRP)
                                Sets.Char.Humanoid:ChangeState("Dead")
                                DestroyLine:FireServer(Sets.HRP)
                                HRP.CFrame = pos
                            end
                        elseif Method ~= "Loop Grab(Kick)" then
                            if Sets.HRP:FindFirstChild("BodyPosition") then Sets.HRP.BodyPosition:Destroy() end
                        end
                    end
                end
            end)
        else
            repeat task.wait() if Sets.HRP:FindFirstChild("BodyPosition") then Sets.HRP.BodyPosition:Destroy() end until not Sets.HRP:FindFirstChild("BodyPosition")
        end
    end
})
-- ==================== LOOP KICK BLOB (Grab + Blobman) ====================
do
    box:AddToggle("LoopKickBlob", {
        Text = "Loop Kick (Grab + Blob)",
        Default = false,
        Callback = function(on)
            if not on then return end

            local targetName = GrabTarget.Value or BlobmanTarget.Value
            if not targetName or targetName == "None" then
                Library:Notify("Selecione um alvo primeiro!", 3)
                Toggles.LoopKickBlob:SetValue(false)
                return
            end

            local target = Players:FindFirstChild(getname(targetName))
            if not target then
                Toggles.LoopKickBlob:SetValue(false)
                return
            end

            local char = plr.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local seat = hum and hum.SeatPart

            if not seat or seat.Parent.Name ~= "CreatureBlobman" then
                Library:Notify("Sente no Blobman primeiro!", 3)
                Toggles.LoopKickBlob:SetValue(false)
                return
            end

            task.spawn(function()
                local blob = seat.Parent
                local blobRoot = blob:FindFirstChild("HumanoidRootPart")
                local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                local CG = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
                local CD = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
                local R_Det = blob:FindFirstChild("RightDetector")
                local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld"))
                local SavedPos = blobRoot.CFrame

                local tChar = target.Character
                local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum = tChar and tChar:FindFirstChild("Humanoid")

                if tRoot and blobRoot then
                    -- Bring inicial
                    for i = 1, 15 do
                        if not Toggles.LoopKickBlob.Value then break end
                        blobRoot.CFrame = tRoot.CFrame
                        pcall(function()
                            if CG and R_Det then 
                                CG:FireServer(R_Det, tRoot, R_Weld) 
                            end
                            DestroyLine:FireServer(tRoot)
                            SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                        end)
                        task.wait()
                    end
                    blobRoot.CFrame = SavedPos
                end

                local packetTimer = 0
                while Toggles.LoopKickBlob.Value do
                    tChar = target.Character
                    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    tHum = tChar and tChar:FindFirstChild("Humanoid")

                    if tRoot and tHum and tHum.Health > 0 and blobRoot then
                        blobRoot.CFrame = SavedPos
                        tRoot.CFrame = SavedPos * CFrame.new(0, 23, 0)

                        if tick() - packetTimer > 0.012 then
                            packetTimer = tick()
                            pcall(function()
                                tHum.PlatformStand = true
                                DestroyLine:FireServer(tRoot)
                                SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                if R_Det and CD then CD:FireServer(R_Det.RightWeld or R_Det:FindFirstChildWhichIsA("Weld")) end
                                if R_Det and CG then CG:FireServer(R_Det, tRoot, R_Weld) end
                            end)
                        end
                    end
                    RunService.Heartbeat:Wait()
                end

                -- Cleanup
                if blobRoot then 
                    blobRoot.CFrame = SavedPos 
                    blobRoot.Velocity = Vector3.zero 
                end
            end)
        end
    })
end
-- ==================== REMOVE ANTI INPUT LAG (Instantâneo por Alvo) ====================
do
    local box = Tabs.Target:AddRightGroupbox("Anti Input Lag Target")

    local AllowedItems = {
        FoodHamburger = true, FoodCoconut = true, FoodPizzaCheese = true,
        FoodPizzaPepperoni = true, FoodHotdog = true, FoodMushroomPoison = true,
        FoodBread = true, FoodDippyEgg = true, FoodMayonnaise = true,
        FoodFrenchFries = true, FoodMeatStick = true, FoodDonut = true,
        FoodCakePink = true, FoodBanana = true,
        InstrumentGuitarBanjo = true, InstrumentGuitarViolin = true,
        InstrumentGuitarUkulele = true, InstrumentWoodwindSaxophone = true,
        InstrumentWoodwindOcarina = true, InstrumentBrassVuvuzelaQwizik = true,
        InstrumentBrassTrumpet = true, InstrumentDrumBongos = true,
        InstrumentDrumSnare = true, InstrumentPianoMelodica = true,
        InstrumentVoiceMicrophone = true,
        CupMugWhite = true, CupMugBrown = true,
        PoopPile = true, PoopPileSparkle = true,
    }

    local SelectedAntiLagTarget = box:AddDropdown("AntiLagTarget", {
        Text = "Alvo Anti Input Lag",
        Values = {"None"},
        Default = "None",
        Multi = false,
    })

    box:AddToggle("RemoveAntiInputLagTarget", {
        Text = "Remove Anti Input Lag",
        Default = false,
        Callback = function(state)
            if not state then return end

            task.spawn(function()
                local targetName = SelectedAntiLagTarget.Value
                if targetName == "None" then
                    Library:Notify("Selecione um jogador!", 4)
                    Toggles.RemoveAntiInputLagTarget:SetValue(false)
                    return
                end

                local target = Players:FindFirstChild(getname(targetName))
                if not target then
                    Library:Notify("Alvo não encontrado!", 4)
                    Toggles.RemoveAntiInputLagTarget:SetValue(false)
                    return
                end

                Library:Notify("Anti Input Lag on: " .. target.DisplayName, 5)

                local connections = {}

                local function ProcessItem(item)
                    if not item or not AllowedItems[item.Name] or not item:FindFirstChild("HoldPart") then return end
                    
                    local holdPart = item.HoldPart
                    pcall(function()
                        holdPart.HoldItemRemoteFunction:InvokeServer(item, plr.Character)
                    end)
                    task.wait(0.025)
                    pcall(function()
                        holdPart.DropItemRemoteFunction:InvokeServer(item, 
                            CFrame.new(0, -3000, 0), Vector3.zero)
                    end)
                end

                -- Monitora itens já existentes + novos spawns
                local function StartMonitoring()
                    local folderName = target.Name .. "SpawnedInToys"
                    local folder = workspace:FindFirstChild(folderName)

                    if folder then
                        -- Processa itens atuais
                        for _, item in ipairs(folder:GetChildren()) do
                            ProcessItem(item)
                        end

                        -- Detecta novos itens instantaneamente
                        connections[#connections+1] = folder.ChildAdded:Connect(function(item)
                            task.wait(0.05) -- pequeno delay para o item carregar
                            ProcessItem(item)
                        end)
                    end
                end

                StartMonitoring()

                -- Atualiza monitor quando o target respawna ou muda de personagem
                connections[#connections+1] = target.CharacterAdded:Connect(function()
                    task.wait(1)
                    StartMonitoring()
                end)

                -- Loop de segurança (caso algum item escape)
                while Toggles.RemoveAntiInputLagTarget.Value do
                    StartMonitoring()
                    task.wait(2)
                end

                -- Cleanup
                for _, conn in ipairs(connections) do
                    pcall(function() conn:Disconnect() end)
                end
            end)
        end
    })

    -- Atualiza lista de jogadores
    local function UpdateTargets()
        local list = {"None"}
        for _, pl in Players:GetPlayers() do
            if pl ~= plr then
                table.insert(list, pl.DisplayName .. " (" .. pl.Name .. ")")
            end
        end
        SelectedAntiLagTarget:SetValues(list)
    end

    Players.PlayerAdded:Connect(UpdateTargets)
    Players.PlayerRemoving:Connect(UpdateTargets)
    UpdateTargets()
end

local GucciAtivado = false
local loopConnection = nil

-- ==================== BOTÃO PARA ATIVAR ====================
box:AddButton("Activate remove gucci", function()
    if GucciAtivado then 
        print("Remove Gucci it's already activated") 
        return 
    end
    
    GucciAtivado = true
    print("Remove gucci on")

    loopConnection = task.spawn(function()
        while GucciAtivado do
            local Seat = Seats[Sets.Name]
            
            if Seat and hum and HRP then
                -- Força sentar
                Seat:Sit(hum)
                
                -- Reseta velocidade e posição
                task.wait(0.03)
                stvel(HRP)
                HRP.CFrame = HRP.CFrame  -- mantém a posição atual
            end
            
            task.wait(0.15) -- Quanto menor, mais forte (mas pode dar lag)
        end
    end)
end)

-- ==================== BOTÃO PARA DESATIVAR ====================
box:AddButton("Disable remove gucci", function()
    if not GucciAtivado then 
        print("Remove gucci already deactivated") 
        return 
    end
    
    GucciAtivado = false
    print("Remove gucci off")
    
    if loopConnection then
        task.cancel(loopConnection)
        loopConnection = nil
    end
end)
box:AddButton("Void TP", function()

    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local hrp = character:WaitForChild("HumanoidRootPart")

    local pos = Vector3.new(6935.58, -557.95, 14922.55)

    -- Verifica se a plataforma já existe
    local plataforma = workspace:FindFirstChild("PlataformaNeonPermanente")

    if not plataforma then
        plataforma = Instance.new("Part")
        plataforma.Name = "PlataformaNeonPermanente"
        plataforma.Size = Vector3.new(4000, 3, 4000)
        plataforma.Position = pos - Vector3.new(0, 5, 0)
        plataforma.Anchored = true
        plataforma.CanCollide = true
        plataforma.Transparency = 0
        plataforma.Color = Color3.fromRGB(255, 255, 255)
        plataforma.Material = Enum.Material.Neon
        plataforma.Parent = workspace
    end

    hrp.CFrame = CFrame.new(pos)

end)

end

do
local box = Tabs.Target:AddLeftGroupbox("Settings", "wrench")
box:AddLabel("Change Offset\n(Only for loop grabs)", false)
local x,y,z
box:AddSlider("Offset", {
    Text = "X",
    Default = -4,9,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        x = v
        offset = CFrame.new(v or 0, y or 0, z or 0)
    end
})
box:AddSlider("Offset", {
    Text = "Y",
    Default = 13,5,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        y = v
        offset = CFrame.new(x or 0, v, z or 0)
    end
})
box:AddSlider("Offset", {
    Text = "Z",
    Default = 0,
    Min = -20,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        z = v
        offset = CFrame.new(x or 0, y or 0, v)
    end
})

box:AddToggle("EnableRagdoll", {
    Text = "Enable Ragdoll Target",
    Default = false,
    Callback = function(v)
        if v then
            local function spawnragdoll()
                PalletForRagdoll = spawntoy("PalletLightBrown", HRP.CFrame * CFrame.new(0, 10, 20))
                repeat task.wait() until PalletForRagdoll and PalletForRagdoll:FindFirstChild("SoundPart")
                repeat task.wait() sno(PalletForRagdoll.SoundPart) until PalletForRagdoll.SoundPart:FindFirstChild("PartOwner")
                PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1e9, 0)
                for i,v in pairs(PalletForRagdoll:GetChildren()) do
                    if v:IsA("Part") then
                        v.CanCollide = false
                        v.CanQuery = false
                        v.Transparency = 1
                    end
                end
                PalletForRagdoll.Name = "PalletForRagdoll"
                cons["PalletDestroying"] = PalletForRagdoll.Destroying:Once(function()
                    spawnragdoll()
                end)
            end
            spawnragdoll()
        else
            if cons["PalletDestroying"] then
                cons["PalletDestroying"]:Disconnect()
                cons["PalletDestroying"] = nil
            end
            if inv:FindFirstChild("PalletForRagdoll") then
                DestroyToy:FireServer(inv.PalletForRagdoll)
            end
        end
    end
})

box:AddToggle("EnableGrabAntiKick", {
    Text = "Enable Anti Anti Kick",
    Default = false,
    Callback = function(v)
        if v then
            task.spawn(function()
                while Toggles.EnableGrabAntiKick.Value and RunService.RenderStepped:Wait() do
                    if not GrabTarget.Value then return end
                    local tt = Players:FindFirstChild(getname(GrabTarget.Value))
                    if not tt then return end
                    for i,v in pairs(workspace[tt.Name.."SpawnedInToys"]:GetChildren()) do
                        if v:FindFirstChild("StickyPart") and (v.StickyPart.Position - HRP.Position).Magnitude < 30 then
                            sno(v.StickyPart)
                            if v.StickyPart:FindFirstChild("PartOwner") and v.StickyPart.PartOwner.Value == plr.Name then
                                v.StickyPart.CFrame = CFrame.new(0, 0/0, 0)
                            end
                        end
                    end
                end
            end)
        end
    end
})

end

do
local box = Tabs.Target:AddRightGroupbox("Settings", "wrench")

box:AddSlider("BlobDelay", {
    Text = "Blob Delay",
    Default = 0.05,
    Min = 0,
    Max = 1,
    Rounding = 2,
})

end

do
local box = Tabs.Main:AddRightGroupbox("Lags")
local lps = 100
local Packets = 3000
box:AddSlider("LPS", {
    Text = "Lines Per Second",
    Default = 100,
    Min = 1,
    Max = 10000,
    Rounding = 0,
    Callback = function(v)
        lps = v
    end
})

box:AddToggle("LineLag", {
    Text = "Line Lag",
    Default = false,
    Callback = function(v)
        linelag = v
        if v then
            task.spawn(function()
                while linelag do
                    for i=1, lps do
                        CreateLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                    end
                    task.wait(1)
                end
            end)
        end
    end
})

box:AddSlider("Packets", {
    Text = "Packet Strength",
    Default = 18000,
    Min = 3000,
    Max = 800000,
    Rounding = 0,
    Callback = function(v)
        Packets = v
    end
})

local AntiDetect = false
box:AddToggle("AntiDetect", {
    Text = "Anti Detect(Packets)",
    Default = false,
    Callback = function(v)
        AntiDetect = v
    end
})

do
local box = Tabs.Main:AddRightGroupbox("Lags")

local lps = 100
local Packets = 1800000
local PacketsEnabled = false
local AutoStopEnabled = true
local MaxPing = 1000
local AntiDetect = false

-- Função melhorada para pegar o ping
local function GetPing()
    local success, ping = pcall(function()
        local stats = game:GetService("Stats")
        local network = stats:FindFirstChild("Network")
        if network then
            local serverStats = network:FindFirstChild("ServerStatsItem")
            if serverStats then
                local dataPing = serverStats:FindFirstChild("Data Ping")
                if dataPing then
                    return dataPing:GetValue()
                end
            end
        end
        return stats.PerformanceStats.Ping and stats.PerformanceStats.Ping:GetValue() or 0
    end)
    return success and ping or 0
end

box:AddSlider("LPS", {
    Text = "Lines Per Second",
    Default = 100,
    Min = 1,
    Max = 10000,
    Rounding = 0,
    Callback = function(v)
        lps = v
    end
})

box:AddToggle("LineLag", {
    Text = "Line Lag",
    Default = false,
    Callback = function(v)
        linelag = v
        if v then
            task.spawn(function()
                while linelag do
                    for i=1, lps do
                        CreateLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                    end
                    task.wait(1)
                end
            end)
        end
    end
})

box:AddSlider("Packets", {
    Text = "Packet Strength",
    Default = 1800000,
    Min = 3000,
    Max = 800000,
    Rounding = 0,
    Callback = function(v)
        Packets = v
    end
})

box:AddToggle("AntiDetect", {
    Text = "Anti Detect (Packets)",
    Default = false,
    Callback = function(v)
        AntiDetect = v
    end
})

box:AddToggle("PacketLag", {
    Text = "Packet Lag",
    Default = false,
    Callback = function(v)
        PacketsEnabled = v
        
        if v then
            Library:Notify("Packet Lag Ativado", 3)
            task.spawn(function()
                while PacketsEnabled do
                    -- Auto Stop
                    if AutoStopEnabled then
                        local currentPing = GetPing()
                        if currentPing >= MaxPing then
                            PacketsEnabled = false
                            Library:Notify("🚫 Packet Lag DESATIVADO automaticamente\nPing: " .. math.floor(currentPing), 5)
                            break
                        end
                    end

                    pcall(function()
                        if AntiDetect then
                            game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                                string.rep("VTX_Hub is crazyyyyyyyy", Packets)
                            )
                        else
                            game:GetService("ReplicatedStorage").GrabEvents.ExtendGrabLine:FireServer(
                                string.rep("VTX_Hub is crazyyyyyyyy", Packets)
                            )
                        end
                    end)
                    
                    task.wait(0.05)
                end
            end)
        else
            Library:Notify("Packet Lag Desativado", 2)
        end
    end
})

box:AddToggle("AutoStop", {
    Text = "Stop on High Ping",
    Default = true,
    Callback = function(v)
        AutoStopEnabled = v
    end
})

box:AddSlider("MaxPingSlider", {
    Text = "Max Ping (Auto Stop)",
    Default = 1000,
    Min = 200,
    Max = 10000,
    Increment = 50,
    Callback = function(v)
        MaxPing = v
    end
})

end

end

do
local box = Tabs.Keybinds:AddLeftGroupbox("Keybinds")

box:AddLabel("Control(Mouse Target)", false):AddKeyPicker("Control", {
    Default = "F",
    Text = "MouseTarget",
    Mode = "Toggle",
    Callback = function(v)
        Control = v
        local tar = Mouse.Target
        if Control and tar and tar.Parent:FindFirstChild("Humanoid") then
            sno(tar.Parent.Head)
            task.spawn(function()
                local oldparent = tar.Parent.Parent
                tar.Parent.Parent = char
                while Control and RunService.RenderStepped:Wait() do
                    if not tar.Parent.Head:FindFirstChild("PartOwner") or tar.Parent.Head:FindFirstChild("PartOwner") and tar.Parent.Head.PartOwner.Value ~= plr.Name then
                        sno(tar.Parent.Head)
                    elseif PalletForRagdoll and PalletForRagdoll:FindFirstChild("SoundPart") then
                        task.spawn(function()
                            if not tar.Parent.Humanoid.Ragdolled.Value then
                                PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                                PalletForRagdoll.SoundPart.CFrame = tar.Parent.HumanoidRootPart.CFrame
                                task.wait(0.05)
                                PalletForRagdoll.SoundPart.CFrame = CFrame.new(0, 1e9, 0)
                            end
                        end)
                        task.spawn(function()
                            for i,v in tar.Parent:GetChildren() do
                                if char:FindFirstChild(v.Name) and v:IsA("BasePart") and HasProperty(v, "CFrame") then
                                    v.CFrame = char[v.Name].CFrame
                                    stvel(v)
                                    if v:FindFirstChild("RagdollLimbPart") then v.RagdollLimbPart.CanCollide = false end
                                    if v.CanCollide == true then v.CanCollide = false v.CanQuery = false v.CanTouch = false end
                                end
                            end
                        end)
                    end
                end
                tar.Parent.Parent = oldparent
            end)
        end
    end,
})

box:AddLabel("Destroy Limbs", false)

box:AddLabel("Remove Left Leg", false):AddKeyPicker("RemoveLeftLeg", {
    Default = "Z",
    Text = "DestroyLimbs1",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Left Leg") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Left Leg"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Right Leg", false):AddKeyPicker("RemoveRightLeg", {
    Default = "C",
    Text = "DestroyLimbs2",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Right Leg") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Right Leg"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Left Arm", false):AddKeyPicker("RemoveLeftArm", {
    Default = "V",
    Text = "DestroyLimbs3",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Left Arm") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Left Arm"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})

box:AddLabel("Remove Right Arm", false):AddKeyPicker("RemoveRightArm", {
    Default = "B",
    Text = "DestroyLimbs4",
    Mode = "Press",
    Callback = function()
        if workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart") then
            local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
            if target and target:FindFirstChild("Right Arm") and target:FindFirstChild("Humanoid") and target.Humanoid:FindFirstChild("Ragdolled") then
                if target.Humanoid.Ragdolled.Value then
                    local pos = target["Torso"].CFrame
                    workspace.FallenPartsDestroyHeight = -100
                    target["Right Arm"].CFrame = CFrame.new(0, -1000, 0)
                    task.wait(0.1)
                    target["Torso"].CFrame = CFrame.new(0, -950, 0)
                    task.wait(0)
                    target["Torso"].CFrame = pos
                else
                    Library:Notify("Ragdoll Target pls")
                end
            end
        else
            Library:Notify("Grab someone")
        end
    end,
})
end
-- ==================== VFLY ====================
do
    local box = Tabs.Keybinds:AddRightGroupbox("VFly")

    local vflyEnabled = false
    local flySpeed = 400
    local bv, bg

    local function startVFly()
        if vflyEnabled then return end
        local char = plr.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local hrp = char.HumanoidRootPart
        local humanoid = char:FindFirstChild("Humanoid")

        -- Remove fly antigo se existir
        if hrp:FindFirstChild("VFly_BV") then hrp.VFly_BV:Destroy() end
        if hrp:FindFirstChild("VFly_BG") then hrp.VFly_BG:Destroy() end

        bv = Instance.new("BodyVelocity")
        bv.Name = "VFly_BV"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.zero
        bv.Parent = hrp

        bg = Instance.new("BodyGyro")
        bg.Name = "VFly_BG"
        bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bg.D = 500
        bg.Parent = hrp

        vflyEnabled = true

        task.spawn(function()
            while vflyEnabled and char and char.Parent and hrp.Parent do
                local cam = workspace.CurrentCamera
                local moveDir = Vector3.zero

                if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir += cam.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir -= cam.CFrame.LookVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir -= cam.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir += cam.CFrame.RightVector end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir += Vector3.new(0,1,0) end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir -= Vector3.new(0,1,0) end

                if moveDir.Magnitude > 0 then
                    moveDir = moveDir.Unit
                end

                bv.Velocity = moveDir * flySpeed
                bg.CFrame = cam.CFrame

                RunService.RenderStepped:Wait()
            end
        end)
    end

    local function stopVFly()
        vflyEnabled = false

        local char = plr.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            local hrp = char.HumanoidRootPart
            
            -- Zera a velocidade antes de destruir
            if bv and bv.Parent then
                bv.Velocity = Vector3.zero
                task.wait() -- pequeno delay para aplicar a velocidade zero
            end
            
            if humanoid then
                humanoid.PlatformStand = false
            end

            -- Destroi os bodies
            if bv then bv:Destroy() end
            if bg then bg:Destroy() end
        end

        bv, bg = nil, nil
    end

    box:AddToggle("VFlyToggle", {
        Text = "Enable VFly",
        Default = false,
        Callback = function(v)
            if v then
                startVFly()
            else
                stopVFly()
            end
        end
    })

    box:AddSlider("VFlySpeed", {
        Text = "Fly Speed",
        Default = 400,
        Min = 10,
        Max = 2000,
        Rounding = 0,
        Callback = function(v)
            flySpeed = v
        end
    })

    box:AddButton("Reset VFly", function()
        stopVFly()
        task.wait(0.1)
        if Toggles.VFlyToggle.Value then
            startVFly()
        end
    end)

    -- Keybind
    box:AddLabel("VFly Keybind"):AddKeyPicker("VFlyKey", {
        Default = "V",
        Text = "Toggle VFly",
        Mode = "Toggle",
        Callback = function(v)
            if Toggles.VFlyToggle then
                Toggles.VFlyToggle:SetValue(v)
            end
        end
    })
end

do
local box = Tabs.Whitelist:AddLeftGroupbox("Whitelist")
local toggle
toggle = box:AddToggle("Whitelist", {
    Text = "Enable Whitelist",
    Default = false,
})

toggle:OnChanged(function(v)
    local val = v and "Disable" or not v and "Enable"
    toggle:SetText(val.." Whitelist")
    WhitelistEnabled = v
end)

end
-- ==================== BLACKLIST (Persistente com Arquivo) ====================
local BlacklistFile = "Blacklist_Config.json"

local function SaveBlacklist()
    local data = {
        Blacklist = Blacklist or {},
        Enabled = BlacklistEnabled or false
    }
    writefile(BlacklistFile, game:GetService("HttpService"):JSONEncode(data))
end

local function LoadBlacklist()
    if isfile(BlacklistFile) then
        local success, data = pcall(function()
            return game:GetService("HttpService"):JSONDecode(readfile(BlacklistFile))
        end)
        if success then
            Blacklist = data.Blacklist or {}
            BlacklistEnabled = data.Enabled or false
            return true
        end
    end
    Blacklist = {}
    BlacklistEnabled = false
    return false
end

-- Carrega ao iniciar o script
LoadBlacklist()

do
    local box = Tabs.Blacklist:AddLeftGroupbox("Blacklist")

    box:AddToggle("EnableBlacklist", {
        Text = "Enable Blacklist",
        Default = BlacklistEnabled,
        Callback = function(v)
            BlacklistEnabled = v
            SaveBlacklist()
        end
    })

    local ServerPlayersDropdown = box:AddDropdown("ServerPlayers", {
        Text = "Jogadores no Servidor",
        Values = {},
        Default = 1,
        Multi = false,
    })

    local BlacklistDropdown = box:AddDropdown("BlacklistList", {
        Text = "Jogadores na Blacklist",
        Values = {},
        Default = 1,
        Multi = true,
    })

    box:AddButton("→ Adicionar à Blacklist", function()
        local selected = ServerPlayersDropdown.Value
        if not selected or selected == "Nenhum jogador" then return end

        local name = selected:match("%((.+)%)") or selected
        if not table.find(Blacklist, name) then
            table.insert(Blacklist, name)
            Library:Notify("✅ " .. name .. " adicionado na Blacklist", 4)
            SaveBlacklist()
            UpdateBlacklistUI()
        else
            Library:Notify(name .. " já está na blacklist", 3)
        end
    end)

    box:AddButton("Remover Selecionados", function()
        local selected = BlacklistDropdown:GetValues()
        for _, display in ipairs(selected) do
            local name = display:match("%((.+)%)") or display
            local idx = table.find(Blacklist, name)
            if idx then
                table.remove(Blacklist, idx)
            end
        end
        SaveBlacklist()
        UpdateBlacklistUI()
    end)

    box:AddButton("Limpar Blacklist", function()
        Blacklist = {}
        SaveBlacklist()
        UpdateBlacklistUI()
        Library:Notify("Blacklist limpa!", 4)
    end)

    function UpdateBlacklistUI()
        -- Jogadores no servidor
        local serverList = {}
        for _, pl in ipairs(Players:GetPlayers()) do
            if pl ~= plr then
                table.insert(serverList, pl.DisplayName .. " (" .. pl.Name .. ")")
            end
        end
        if #serverList == 0 then 
            table.insert(serverList, "Nenhum jogador") 
        end
        ServerPlayersDropdown:SetValues(serverList)

        -- Blacklist
        local blList = {}
        for _, name in ipairs(Blacklist) do
            local pl = Players:FindFirstChild(name)
            if pl then
                table.insert(blList, pl.DisplayName .. " (" .. pl.Name .. ")")
            else
                table.insert(blList, name)
            end
        end
        if #blList == 0 then 
            table.insert(blList, "Blacklist vazia") 
        end
        BlacklistDropdown:SetValues(blList)
    end
end

-- Notificação ao entrar
Players.PlayerAdded:Connect(function(pl)
    task.wait(1.5)
    if not BlacklistEnabled then return end
    
    if table.find(Blacklist, pl.Name) or table.find(Blacklist, pl.DisplayName) then
        Library:Notify("⚠️ BLACKLIST: " .. pl.DisplayName .. " (" .. pl.Name .. ") entrou no servidor!", 6)
    end
end)

-- Atualizações automáticas
Players.PlayerAdded:Connect(UpdateBlacklistUI)
Players.PlayerRemoving:Connect(UpdateBlacklistUI)
UpdateBlacklistUI()

-- Salva quando o jogador sai (opcional, mas bom)
game.Players.PlayerRemoving:Connect(function(pl)
    if pl == plr then
        SaveBlacklist()
    end
end)


do
local box = Tabs["Server"]:AddLeftGroupbox("Main")

box:AddButton("Destroy Server(Need Blobman)", function()
    local blob = hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and hum.SeatPart.Parent
    if not blob then return end
    blob.Name = "blob"
    local CD,CR,CG = blob.BlobmanSeatAndOwnerScript.CreatureDrop, blob.BlobmanSeatAndOwnerScript.CreatureRelease, blob.BlobmanSeatAndOwnerScript.CreatureGrab
    local pos = blob.HumanoidRootPart.CFrame * CFrame.new(0, 30, 0)
    for i,v in game.Players:GetPlayers() do
        pcall(function()
            if v ~= plr and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and (not WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                blob.HumanoidRootPart.CFrame = v.Character.HumanoidRootPart.CFrame
                task.wait(0.2)
                CG:FireServer(nil, v.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                CR:FireServer(blob.RightDetector.RightWeld)
            end
        end)
        task.wait(0.1)
    end
    blob.HumanoidRootPart.CFrame = pos
    task.wait(0.1)
    blob.HumanoidRootPart.Anchored = true
    local rotation = 0
    for i,v in game.Players:GetPlayers() do
        pcall(function()
            if v ~= plr and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and isnetworkowner(v.Character.HumanoidRootPart) and (not WhitelistEnabled or not v:IsFriendsWith(plr.UserId)) then
                local bg = Instance.new("BodyGyro", v.Character.HumanoidRootPart)
                bg.CFrame = CFrame.new(0, 0, 0)
                stvel(blob.HumanoidRootPart)
                stvel(v.Character.HumanoidRootPart)
                rotation = rotation + 30
                v.Character.HumanoidRootPart.CFrame = CFrame.new(HRP.Position) * CFrame.Angles(0, math.rad(rotation), 0) * CFrame.new(i, 0, 0)
                stvel(blob.HumanoidRootPart)
                task.wait(0.2)
                sno(v.Character.HumanoidRootPart)
                DestroyLine:FireServer(v.Character.HumanoidRootPart)
                task.wait()
                CG:FireServer(nil, v.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
            end
        end)
        task.wait(0.1)
    end
    task.wait(0.1)
    blob.HumanoidRootPart.Anchored = false
    DestroyToy:FireServer(inv.blob)
end)

-- box:AddToggle("Lobotomy", {
--     Text = "Lobotomy",
--     Default = false,
--     Callback = function(v)
--         Labatamia = v
--         if v then
--             local tractor = spawntoy("TractorGreen", HRP.CFrame * CFrame.new(0, 5, 20))
--             repeat task.wait() until tractor and tractor:FindFirstChild("VehicleSeat")
--             tractor.VehicleSeat:Sit(hum)
--             task.wait(0.1)
--             task.spawn(function()
--                 while task.wait(0.1) and Labatamia do
--                     Ragdoll:FireServer(HRP, 0)
--                 end
--             end)
--             tractor.Name = "Lobotomy:skull:"
--         else
--             hum.Sit = true
--             task.wait(0.1)
--             hum.Sit = false
--         end
--     end
-- })


end

-- box:AddToggle("", {
--     Text = "",
--     Default = false,
--     Callback = function(v)
        
--     end
-- })

-- box:AddSlider("", {
--     Text = "",
--     Default = ,
--     Min = ,
--     Max = ,
--     Rounding = ,
-- })

















local activepackets = false
rs.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(player, args)
    if typeof(args) == "string" and string.len(args) > 300 and not activepackets then
        activepackets = true
        local function GetSizeMB(StringLength)
            return StringLength / (1024 * 1024)
        end
        local SizeRounded = math.round(GetSizeMB(string.len(args)) * 1000) / 1000 
Library:Notify({
    Title = "VTX_Hub",
    Description = player.Name .. " Enabled Packets Size: " .. SizeRounded,
    Time = 4,
    Icon = "rbxassetid://127990091575711"
})
        task.wait(4)
        activepackets = false
    end
end)

local MenuGroup = Tabs["UI Settings"]:AddLeftGroupbox("Menu Settings")

MenuGroup:AddButton("Unload", function()
    for i,v in Toggles do
        if not v.Value then continue end
        v:SetValue(false)
    end
    if game.CoreGui:FindFirstChild("SnowGui") then
        game.CoreGui.SnowGui:Destroy()
    end
    workspace.Camera.Blur.Size = 0
	Library:Unload()
end)

MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", {
    Default = "X",
    NoUI = false,
    Text = "Menu keybind"
})

MenuGroup:AddToggle("AlwaysViewCursor", {
    Text = "Always View Cursor",
    Default = false,
    Callback = function(v)
        alwaysshowcursor = v
        if v then
            task.spawn(function()
                while alwaysshowcursor and task.wait() do
                    UserInputService.MouseIconEnabled = true
                end
            end)
        else
            UserInputService.MouseIconEnabled = false
        end
    end
})

MenuGroup:AddToggle("Snowflakes", {
    Text = "Snowflakes",
    Default = false,
    Callback = function(v)
        Snowflakes = v
    end
})

MenuGroup:AddToggle("Blur", {
    Text = "Blur",
    Default = false,
    Callback = function(v)
        Blur = v
    end
})

Library.ToggleKeybind = Options.MenuKeybind 

ThemeManager:SetLibrary(Library)

ThemeManager:SetFolder("MyScriptHub")

ThemeManager:ApplyToTab(Tabs["UI Settings"])

ThemeManager:LoadDefault()

SaveManager:SetLibrary(Library)

SaveManager:IgnoreThemeSettings()

SaveManager:SetIgnoreIndexes({ "MenuKeybind" })

SaveManager:SetFolder("MyScriptHub/specific-game")

SaveManager:BuildConfigSection(Tabs["UI Settings"])

SaveManager:LoadAutoloadConfig()

local function UpdatePlayerLists()
    local list = {}
    for _, pl in ipairs(Players:GetPlayers()) do
        table.insert(list, pl.DisplayName.." ("..pl.Name..")")
    end
	GrabTarget:SetValues(list)
    BlobmanTarget:SetValues(list)
end

Players.PlayerAdded:Connect(UpdatePlayerLists)
Players.PlayerRemoving:Connect(UpdatePlayerLists)
UpdatePlayerLists()

for i,v in Players:GetPlayers() do
    if v ~= plr then
        if v.Character and v.Character.Humanoid.SeatPart then Seats[v.Name] = v.Character.Humanoid.SeatPart end
        v.CharacterAdded:Connect(function(c)
            c:WaitForChild("Humanoid"):GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = c.Humanoid.SeatPart
                if not seat then return end
                print(seat)
                Seats[v.Name] = seat
            end)
        end)
        if v.Character then
            v.Character.Humanoid:GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = v.Character.Humanoid.SeatPart
                if not seat then return end
                print(seat)
                Seats[v.Name] = seat
            end)
        end
    end
end

task.spawn(function()
    while task.wait(0.1) do
        if HRP and HRP.Parent then
            HRP.Massless = false
        end
    end
end)

Players.PlayerAdded:Connect(function(p)
    if p ~= plr then
        p.CharacterAdded:Connect(function(c)
            c:WaitForChild("Humanoid"):GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = c.Humanoid.SeatPart
                if not seat then return end
                Seats[p.Name] = seat
            end)
        end)
        if p.Character then
            p.Character.Humanoid:GetPropertyChangedSignal("SeatPart"):Connect(function()
                local seat = p.Character.Humanoid.SeatPart
                if not seat then return end
                Seats[p.Name] = seat
            end)
        end
    end
end)

for i,v in Players:GetPlayers() do
    if v ~= plr then
        if v.Character and v.Character.Humanoid.SeatPart then Seats[v.Name] = v.Character.Humanoid.SeatPart end
        v.CharacterAdded:Connect(function(c)
            task.wait(1)
            c.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if c.HumanoidRootPart.Massless == true then
                    c.HumanoidRootPart.Massless = false
                end
            end)
        end)
        if v.Character then
            v.Character.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if v.Character.HumanoidRootPart.Massless == true then
                    v.Character.HumanoidRootPart.Massless = false
                end
            end)
        end
    end
end

Players.PlayerAdded:Connect(function(p)
    if p ~= plr then
        p.CharacterAdded:Connect(function(c)
            task.wait(1)
            c.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if c.HumanoidRootPart.Massless == true then
                    c.HumanoidRootPart.Massless = false
                end
            end)
        end)
        if p.Character then
            p.Character.HumanoidRootPart:GetPropertyChangedSignal("Massless"):Connect(function()
                if p.Character.HumanoidRootPart.Massless == true then
                    p.Character.HumanoidRootPart.Massless = false
                end
            end)
        end
    end
end)

task.wait(3)
task.spawn(function()
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "SnowGui"

    local Frame = Instance.new("Frame", gui)
    Frame.AnchorPoint = Vector2.new(0.5, 0.5)
    Frame.Position = UDim2.new(0.5, 0, 0.5, 0)
    Frame.Size = UDim2.new(1, 0, 1, 0)
    Frame.BackgroundTransparency = 1

    local SNOW_COUNT = 100
    for i = 1, SNOW_COUNT do
        local snow = Instance.new("ImageLabel", Frame)
        local corner = Instance.new("UICorner", snow)
        snow.AnchorPoint = Vector2.new(0.5, 0)
        snow.Position = UDim2.new(math.random(), 0, -0.1, 0)
        snow.Size = UDim2.new(0, 10, 0, 10)
        snow.BackgroundTransparency = 0
        snow.BackgroundColor3 = Color3.fromRGB(255,255,255)

        local function fall()
            local tweenInfo = TweenInfo.new(3 + math.random(), Enum.EasingStyle.Linear)
            local tweenGoal = {Position = UDim2.new(snow.Position.X.Scale, 0, 1, 0)}
            local tween = TweenService:Create(snow, tweenInfo, tweenGoal)
            tween:Play()
            
            tween.Completed:Connect(function()
                snow.Position = UDim2.new(math.random(), 0, -0.1, 0)
                fall()
            end)
        end
        
        fall()
    end
    local fr
    for i,v in gethui().Obsidian.Main:GetChildren() do
        if v:IsA("Frame") and v.Position == UDim2.new(0,0,0,0) then
            fr = v
        end
    end
    local clone = fr.ImageLabel:Clone()
    clone.Parent = fr
    clone.Position = UDim2.new(0, 70, 0.5, -35)
    clone.Size = UDim2.new(0, 100, 0, 100)
    clone.Image = "rbxassetid://88035769656617"
    clone.ImageColor3 = Color3.fromRGB(255, 255, 255)
    clone.ImageRectOffset = Vector2.new(0,0)
    clone.ImageRectSize = Vector2.new(0,0)
    while task.wait(0.1) do
        gui.Enabled = Snowflakes and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Enabled = Blur and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Size = 30
    end
end)

game:GetService("Players").LocalPlayer.PlayerGui.GameCorrectionsGui.GameCorrectionsUiController.Enabled = false 
