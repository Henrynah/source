
gethui = function()
	return game.CoreGui.RobloxGui
end


repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
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
                               
local admins =loadstring(game:HttpGet("https://raw.githubusercontent.com/Brovaky/Friendly/refs/heads/main/admins"))()
 

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
local Window = Library:CreateWindow({
    Title = "VTX Hub",
    Footer = 'version: 2.5',
    Center = true,
    AutoShow = true
})

local HomeTab = Window:AddTab({
    Name = "Home",
    Icon = "user"
})
local LeftGroup = HomeTab:AddLeftGroupbox("⚙️ Greetings")
local RightGroup = HomeTab:AddRightGroupbox("📊 Statistics")


local Players = game:GetService("Players")
local player = Players.LocalPlayer


LeftGroup:AddLabel("Evening, " .. player.Name)
LeftGroup:AddDivider()
LeftGroup:AddLabel("👋 Welcome to VTX Hub!")
LeftGroup:AddLabel("Maintained and developed by Snow.")
LeftGroup:AddLabel("🎉 Thank you for your purchase! Your license is lifetime")


LeftGroup:AddDivider()
LeftGroup:AddLabel("👑 Owner: InthewordSnow1")
LeftGroup:AddLabel("🛠️ Helper: Richardcasta1")
LeftGroup:AddLabel("📋 Beta Tester: VTX_Hd")
LeftGroup:AddLabel("📋 Beta Tester: VTX_Nether")


local ProfileBox = HomeTab:AddLeftGroupbox("👤 Profile")

ProfileBox:AddLabel("👤 Name: " .. player.Name)
ProfileBox:AddLabel("🔍 Display: " .. player.DisplayName)
ProfileBox:AddLabel("🏷️ User ID: " .. player.UserId)
ProfileBox:AddDivider()
ProfileBox:AddLabel("⭐ Rank: Premium User")
ProfileBox:AddLabel("📜 License: Lifetime")
ProfileBox:AddLabel("🟢 Status: Online")

local elapsedTime = 0
local ElapsedLabel = RightGroup:AddLabel("Elapsed time: 0 seconds")

task.spawn(function()
    while task.wait(1) do
        elapsedTime += 1

        if ElapsedLabel and ElapsedLabel.SetText then
            ElapsedLabel:SetText("Elapsed time: " .. elapsedTime .. " seconds")
        end
    end
end)


local Players = game:GetService("Players")

local Players = game:GetService("Players")

local kickedPlayers = 0
local label = RightGroup:AddLabel("Kicks: 0")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

Players.PlayerRemoving:Connect(function(player)
    kickedPlayers += 1
    label:SetText("Kicks: " .. kickedPlayers)
end)



Players.PlayerRemoving:Connect(function(player)
    kickedPlayers += 1
    print(player.Name .. " saiu. Total de kicks: " .. kickedPlayers)
end)



RightGroup:AddDivider()
RightGroup:AddDivider()
RightGroup:AddLabel("Current time: " .. os.date("%H:%M"))
RightGroup:AddLabel("Script version: v2.5")



task.spawn(function()
    while task.wait(1) do
        elapsedTime += 1
        ElapsedLabel:Set("Elapsed time: " .. elapsedTime .. " seconds")
    end
end)




RightGroup:AddButton({
    Text = "Unload Script",
    Func = function()
        Library:Unload()
    end
})

RightGroup:AddButton({
    Text = "Rejoin",
    Func = function()
        game:GetService("TeleportService"):Teleport(game.PlaceId)
    end
})

local Tabs = {
	Main = Window:AddTab({
		Name = "Main",
		Icon = "house"  -- Ícone principal
	}),
	
	Defence = Window:AddTab({
		Name = "Defence",
		Icon = "shield"  -- Escudo para defesa
	}),
	
	Visual = Window:AddTab({
		Name = "Visual",
		Icon = "eye"  -- Olho para visuals/ESP
	}),
	
	Target = Window:AddTab({
		Name = "Target",
		Icon = "crosshair"  -- Mira para targeting
	}),
	
	Server = Window:AddTab({
		Name = "Server",
		Icon = "server"  -- Servidor
	}),
	
	Keybinds = Window:AddTab({
		Name = "Keybinds",
		Icon = "keyboard"  -- Teclado
	}),
	
	Whitelist = Window:AddTab({
		Name = "Whitelist",
		Icon = "user-check"  -- Usuário aprovado
	}),
	
	Blacklist = Window:AddTab({
		Name = "Blacklist",
		Icon = "user-x"  -- Usuário bloqueado
	}),
	
	Notify = Window:AddTab({
		Name = "Notify",
		Icon = "bell"  -- Sino de notificação
	}),

    Discord = Window:AddTab({
    Name = "Discord",
    Icon = "message-circle"
}),
    Teleports = Window:AddTab({
    Name = "Teleports",
    Icon = "map"
}),
	
	["UI Settings"] = Window:AddTab({
		Name = "UI Settings",
		Icon = "settings"  -- Configurações
	}),
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


Window:SetCornerRadius(20)
Library.ShowCustomCursor = true

local watermark = Library:AddDraggableLabel("VTX HUB | 00:00:00 | 0 FPS | 0 ms")

local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")

task.spawn(function()
    while task.wait(0.5) do
        local fps = math.floor(1 / RunService.RenderStepped:Wait())

        local ping = 0
        pcall(function()
            ping = math.floor(
                Stats.Network.ServerStatsItem["Data Ping"]:GetValue()
            )
        end)

        local horario = os.date("%H:%M:%S")

        watermark:SetText(
            string.format(
                "VTX HUB | %s | %d FPS | %d ms",
                horario,
                fps,
                ping
            )
        )
    end
end)



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
    Text = "View PCLD (Bordas Rainbow)",
    Default = false,
    Callback = function(v)
        cons = cons or {}

        if v then
            local trans = Options.PCLDTransparency and Options.PCLDTransparency.Value or 0.2
            local RunService = game:GetService("RunService")
            local adornments = {}
            local hue = 0

            local function AddBorders(obj)
                if obj.Name == "PlayerCharacterLocationDetector"
                and obj:IsA("BasePart")
                and not obj:FindFirstChild("PCLD_Borders") then

                    obj.Transparency = 1
                    obj.Material = Enum.Material.SmoothPlastic

                    local selection = Instance.new("SelectionBox")
                    selection.Name = "PCLD_Borders"
                    selection.Adornee = obj
                    selection.LineThickness = 0.06
                    selection.Transparency = trans
                    selection.Color3 = Color3.fromHSV(0, 1, 1)
                    selection.Parent = obj

                    table.insert(adornments, selection)
                end
            end

            for _, child in ipairs(workspace:GetChildren()) do
                AddBorders(child)
            end

            cons["viewpcld"] = workspace.ChildAdded:Connect(AddBorders)

            -- Rainbow mais lento e suave
            cons["rainbow"] = RunService.RenderStepped:Connect(function(dt)
                hue = (hue + dt * 0.09) % 1
                local rainbowColor = Color3.fromHSV(hue, 1, 1)

                for i = #adornments, 1, -1 do
                    local sb = adornments[i]

                    if sb and sb.Parent and sb.Adornee and sb.Adornee.Parent then
                        sb.Color3 = rainbowColor
                    else
                        if sb then
                            sb:Destroy()
                        end
                        table.remove(adornments, i)
                    end
                end
            end)

        else
            if cons["viewpcld"] then
                cons["viewpcld"]:Disconnect()
                cons["viewpcld"] = nil
            end

            if cons["rainbow"] then
                cons["rainbow"]:Disconnect()
                cons["rainbow"] = nil
            end

            for _, part in ipairs(workspace:GetChildren()) do
                if part.Name == "PlayerCharacterLocationDetector" and part:IsA("BasePart") then
                    local border = part:FindFirstChild("PCLD_Borders")
                    if border then
                        border:Destroy()
                    end
                    part.Transparency = 1
                end
            end
        end
    end
})
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local rainbowConnections = {} -- Controla a animação do arco-íris

-- Função para gerar cor do arco-íris
local function GetRainbowColor()
    local tempo = os.clock() * 1 -- Velocidade: 1 = devagar, 2 = normal, 3 = rápido
    return Color3.fromHSV(tempo % 1, 1, 1)
end

local function AddESP(plr)
    if plr == Players.LocalPlayer then return end

    local function Apply(char)
        -- Evita duplicar elementos
        if char:FindFirstChild("VTX_ESP") or char:FindFirstChild("VTX_NameESP") then return end

        -- Contorno com efeito arco-íris
        local hl = Instance.new("Highlight")
        hl.Name = "VTX_ESP"
        hl.FillTransparency = 1
        hl.OutlineTransparency = 0
        hl.OutlineColor = GetRainbowColor()
        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        hl.Parent = char

        -- Nome com parênteses, branco normal, acima da cabeça
        local head = char:FindFirstChild("Head")
        if head then
            local bill = Instance.new("BillboardGui")
            bill.Name = "VTX_NameESP"
            bill.Size = UDim2.new(0, 120, 0, 50) -- Ajustado para caber os parênteses
            bill.StudsOffset = Vector3.new(0, 3.5, 0) -- Bem acima da cabeça
            bill.AlwaysOnTop = true
            bill.MaxDistance = 3000 -- Alcance grande
            bill.Parent = head

            local txt = Instance.new("TextLabel")
            txt.Size = UDim2.new(1, 0, 1, 0)
            txt.BackgroundTransparency = 1
            txt.Text = "(" .. plr.Name .. ")" -- Formato com parênteses
            txt.TextColor3 = Color3.fromRGB(255, 255, 255) -- Branco fixo
            txt.TextStrokeTransparency = 0
            txt.TextStrokeColor3 = Color3.new(0, 0, 0) -- Borda preta para legibilidade
            txt.TextScaled = true
            txt.Font = Enum.Font.SourceSansBold
            txt.AnchorPoint = Vector2.new(0.5, 0.5)
            txt.Position = UDim2.new(0.5, 0, 0.5, 0)
            txt.Parent = bill
        end

        -- Animação apenas do contorno
        local conn = RunService.RenderStepped:Connect(function()
            if not hl:IsDescendantOf(game) then
                conn:Disconnect()
                rainbowConnections[char] = nil
                return
            end
            hl.OutlineColor = GetRainbowColor()
        end)

        rainbowConnections[char] = conn
    end

    -- Aplica se o personagem já existir
    if plr.Character then
        Apply(plr.Character)
    end

    -- Aplica quando o personagem carregar ou renascer
    plr.CharacterAdded:Connect(Apply)
end

local function RemoveESP()
    -- Para todas as animações
    for char, conn in pairs(rainbowConnections) do
        conn:Disconnect()
    end
    rainbowConnections = {}

    -- Remove todos os elementos visuais
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

-- 🔹 AQUI: Detecta jogadores que entrarem a qualquer momento
Players.PlayerAdded:Connect(function(novoJogador)
    task.spawn(function() -- Evita travar se houver atraso no carregamento
        novoJogador.CharacterAdded:Wait() -- Espera o personagem dele aparecer
        if _G.ESPAtivado then -- Se o ESP estiver ligado, aplica automaticamente
            AddESP(novoJogador)
        end
    end)
end)

-- Botão de ativar/desativar
box:AddToggle("PlayerESP", {
    Text = "Player ESP",
    Default = false,
    Callback = function(ativado)
        _G.ESPAtivado = ativado -- Guarda o estado atual

        if ativado then
            -- Aplica em todos que já estão na sala
            for _, plr in ipairs(Players:GetPlayers()) do
                AddESP(plr)
            end
        else
            RemoveESP()
        end
    end
})

box:AddDropdown("Skybox", {
    Text = "Skybox",
    Values = {
        "Black Storm",
        "HD",
        "Snow",
        "Blue Space",
        "Realistic",
        "Stormy",
        "Pink",
        "Sunset",
        "Arctic",
        "Space",
        "Roblox Default",
        "Red Night",
        "Deep Space 1",
        "Pink Skies",
        "Purple Sunset",
        "Blue Night",
        "Blossom Daylight",
        "Blue Nebula",
        "Blue Planet",
        "Deep Space 2",
        "Summer",
        "Galaxy",
        "Stylized",
        "Minecraft",
        "Cloudy Rain",
        "Black Cloudy Rain"
    },
    Default = "HD",
    Callback = function(Value)
        local Skyboxes = {
            ["Black Storm"]={Bk="rbxassetid://15502511288",Dn="rbxassetid://15502508460",Ft="rbxassetid://15502510289",Lf="rbxassetid://15502507918",Rt="rbxassetid://15502509398",Up="rbxassetid://15502511911"},
            ["HD"]={Bk="rbxassetid://16553658937",Dn="rbxassetid://16553660713",Ft="rbxassetid://16553662144",Lf="rbxassetid://16553664042",Rt="rbxassetid://16553665766",Up="rbxassetid://16553667750"},
            ["Snow"]={Bk="rbxassetid://155657655",Dn="rbxassetid://155674246",Ft="rbxassetid://155657609",Lf="rbxassetid://155657671",Rt="rbxassetid://155657619",Up="rbxassetid://155674931"},
            ["Blue Space"]={Bk="rbxassetid://15536110634",Dn="rbxassetid://15536112543",Ft="rbxassetid://15536116141",Lf="rbxassetid://15536114370",Rt="rbxassetid://15536118762",Up="rbxassetid://15536117282"},
            ["Realistic"]={Bk="rbxassetid://653719502",Dn="rbxassetid://653718790",Ft="rbxassetid://653719067",Lf="rbxassetid://653719190",Rt="rbxassetid://653718931",Up="rbxassetid://653719321"},
            ["Stormy"]={Bk="rbxassetid://18703245834",Dn="rbxassetid://18703243349",Ft="rbxassetid://18703240532",Lf="rbxassetid://18703237556",Rt="rbxassetid://18703235430",Up="rbxassetid://18703232671"},
            ["Pink"]={Bk="rbxassetid://12216109205",Dn="rbxassetid://12216109875",Ft="rbxassetid://12216109489",Lf="rbxassetid://12216110170",Rt="rbxassetid://12216110471",Up="rbxassetid://12216108877"},
            ["Sunset"]={Bk="rbxassetid://600830446",Dn="rbxassetid://600831635",Ft="rbxassetid://600832720",Lf="rbxassetid://600886090",Rt="rbxassetid://600833862",Up="rbxassetid://600835177"},
            ["Arctic"]={Bk="rbxassetid://225469390",Dn="rbxassetid://225469395",Ft="rbxassetid://225469403",Lf="rbxassetid://225469450",Rt="rbxassetid://225469471",Up="rbxassetid://225469481"},
            ["Space"]={Bk="rbxassetid://166509999",Dn="rbxassetid://166510057",Ft="rbxassetid://166510116",Lf="rbxassetid://166510092",Rt="rbxassetid://166510131",Up="rbxassetid://166510114"},
            ["Roblox Default"]={Bk="rbxasset://textures/sky/sky512_bk.tex",Dn="rbxasset://textures/sky/sky512_dn.tex",Ft="rbxasset://textures/sky/sky512_ft.tex",Lf="rbxasset://textures/sky/sky512_lf.tex",Rt="rbxasset://textures/sky/sky512_rt.tex",Up="rbxasset://textures/sky/sky512_up.tex"},
            ["Red Night"]={Bk="rbxassetid://401664839",Dn="rbxassetid://401664862",Ft="rbxassetid://401664960",Lf="rbxassetid://401664881",Rt="rbxassetid://401664901",Up="rbxassetid://401664936"},
            ["Deep Space 1"]={Bk="rbxassetid://149397692",Dn="rbxassetid://149397686",Ft="rbxassetid://149397697",Lf="rbxassetid://149397684",Rt="rbxassetid://149397688",Up="rbxassetid://149397702"},
            ["Pink Skies"]={Bk="rbxassetid://151165214",Dn="rbxassetid://151165197",Ft="rbxassetid://151165224",Lf="rbxassetid://151165191",Rt="rbxassetid://151165206",Up="rbxassetid://151165227"},
            ["Purple Sunset"]={Bk="rbxassetid://264908339",Dn="rbxassetid://264907909",Ft="rbxassetid://264909420",Lf="rbxassetid://264909758",Rt="rbxassetid://264908886",Up="rbxassetid://264907379"},
            ["Blue Night"]={Bk="rbxassetid://12064107",Dn="rbxassetid://12064152",Ft="rbxassetid://12064121",Lf="rbxassetid://12063984",Rt="rbxassetid://12064115",Up="rbxassetid://12064131"},
            ["Blossom Daylight"]={Bk="rbxassetid://271042516",Dn="rbxassetid://271077243",Ft="rbxassetid://271042556",Lf="rbxassetid://271042310",Rt="rbxassetid://271042467",Up="rbxassetid://271077958"},
            ["Blue Nebula"]={Bk="rbxassetid://135207744",Dn="rbxassetid://135207662",Ft="rbxassetid://135207770",Lf="rbxassetid://135207615",Rt="rbxassetid://135207695",Up="rbxassetid://135207794"},
            ["Blue Planet"]={Bk="rbxassetid://218955819",Dn="rbxassetid://218953419",Ft="rbxassetid://218954524",Lf="rbxassetid://218958493",Rt="rbxassetid://218957134",Up="rbxassetid://218950090"},
            ["Deep Space 2"]={Bk="rbxassetid://159248188",Dn="rbxassetid://159248183",Ft="rbxassetid://159248187",Lf="rbxassetid://159248173",Rt="rbxassetid://159248192",Up="rbxassetid://159248176"},
            ["Summer"]={Bk="rbxassetid://16648590964",Dn="rbxassetid://16648617436",Ft="rbxassetid://16648595424",Lf="rbxassetid://16648566370",Rt="rbxassetid://16648577071",Up="rbxassetid://16648598180"},
            ["Galaxy"]={Bk="rbxassetid://15983968922",Dn="rbxassetid://15983966825",Ft="rbxassetid://15983965025",Lf="rbxassetid://15983967420",Rt="rbxassetid://15983966246",Up="rbxassetid://15983964246"},
            ["Stylized"]={Bk="rbxassetid://18351376859",Dn="rbxassetid://18351374919",Ft="rbxassetid://18351376800",Lf="rbxassetid://18351376469",Rt="rbxassetid://18351376457",Up="rbxassetid://18351377189"},
            ["Minecraft"]={Bk="rbxassetid://8735166756",Dn="rbxassetid://8735166707",Ft="rbxassetid://8735231668",Lf="rbxassetid://8735166755",Rt="rbxassetid://8735166751",Up="rbxassetid://8735166729"},
            ["Cloudy Rain"]={Bk="rbxassetid://4498828382",Dn="rbxassetid://4498828812",Ft="rbxassetid://4498829917",Lf="rbxassetid://4498830911",Rt="rbxassetid://4498830417",Up="rbxassetid://4498831746"},
            ["Black Cloudy Rain"]={Bk="rbxassetid://149679669",Dn="rbxassetid://149681979",Ft="rbxassetid://149679690",Lf="rbxassetid://149679709",Rt="rbxassetid://149679722",Up="rbxassetid://149680199"},
        }

        local data = Skyboxes[Value]
        if not data then return end

        local Lighting = game:GetService("Lighting")
        local sky = Lighting:FindFirstChildOfClass("Sky") or Instance.new("Sky", Lighting)

        sky.SkyboxBk = data.Bk
        sky.SkyboxDn = data.Dn
        sky.SkyboxFt = data.Ft
        sky.SkyboxLf = data.Lf
        sky.SkyboxRt = data.Rt
        sky.SkyboxUp = data.Up
    end
})

local Lighting = game:GetService("Lighting")

local Original = {
    Sky = Lighting:FindFirstChildOfClass("Sky"),
    Atmosphere = Lighting:FindFirstChildOfClass("Atmosphere"),
    CC = Lighting:FindFirstChildOfClass("ColorCorrectionEffect"),
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    EnvDiff = Lighting.EnvironmentDiffuseScale,
    EnvSpec = Lighting.EnvironmentSpecularScale
}

local function applySky()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") or v:IsA("Atmosphere") or v:IsA("ColorCorrectionEffect") then
            v:Destroy()
        end
    end

    -- MAIS CLARO QUE ANTES
    Lighting.ClockTime = 3.1
    Lighting.Brightness = 1.6
    Lighting.GlobalShadows = true
    Lighting.EnvironmentDiffuseScale = 0.75
    Lighting.EnvironmentSpecularScale = 0.35
    Lighting.Ambient = Color3.fromRGB(90, 90, 100)
    Lighting.OutdoorAmbient = Color3.fromRGB(70, 70, 80)

    local sky = Instance.new("Sky")
    sky.SkyboxBk = "rbxassetid://159454299"
    sky.SkyboxDn = "rbxassetid://159454296"
    sky.SkyboxFt = "rbxassetid://159454293"
    sky.SkyboxLf = "rbxassetid://159454286"
    sky.SkyboxRt = "rbxassetid://159454300"
    sky.SkyboxUp = "rbxassetid://159454288"
    sky.StarCount = 1800
    sky.CelestialBodiesShown = false
    sky.Parent = Lighting

    local atm = Instance.new("Atmosphere")
    atm.Density = 0.18
    atm.Offset = 0.1
    atm.Color = Color3.fromRGB(150, 150, 170)
    atm.Decay = Color3.fromRGB(50, 50, 60)
    atm.Glare = 0.15
    atm.Haze = 1
    atm.Parent = Lighting

    local cc = Instance.new("ColorCorrectionEffect")
    cc.Brightness = 0.08
    cc.Contrast = 0.12
    cc.Saturation = -0.05
    cc.TintColor = Color3.fromRGB(210, 220, 255)
    cc.Parent = Lighting
end

local function resetSky()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") or v:IsA("Atmosphere") or v:IsA("ColorCorrectionEffect") then
            v:Destroy()
        end
    end

    if Original.Sky then Original.Sky.Parent = Lighting end
    if Original.Atmosphere then Original.Atmosphere.Parent = Lighting end
    if Original.CC then Original.CC.Parent = Lighting end

    Lighting.Brightness = Original.Brightness
    Lighting.ClockTime = Original.ClockTime
    Lighting.Ambient = Original.Ambient
    Lighting.OutdoorAmbient = Original.OutdoorAmbient
    Lighting.EnvironmentDiffuseScale = Original.EnvDiff
    Lighting.EnvironmentSpecularScale = Original.EnvSpec
end

box:AddToggle("SkyToggle", {
    Text = "Sky (Bright)",
    Default = false,
    Callback = function(Value)
        if Value then
            applySky()
        else
            resetSky()
        end
    end
})

local Lighting = game:GetService("Lighting")

local Original = {
    Sky = Lighting:FindFirstChildOfClass("Sky"),
    Atmosphere = Lighting:FindFirstChildOfClass("Atmosphere"),
    CC = Lighting:FindFirstChildOfClass("ColorCorrectionEffect"),
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    EnvDiff = Lighting.EnvironmentDiffuseScale,
    EnvSpec = Lighting.EnvironmentSpecularScale
}

local function applyDay()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") or v:IsA("Atmosphere") or v:IsA("ColorCorrectionEffect") then
            v:Destroy()
        end
    end

    -- DIA LIMPO
    Lighting.ClockTime = 14
    Lighting.Brightness = 2
    Lighting.GlobalShadows = true
    Lighting.EnvironmentDiffuseScale = 1
    Lighting.EnvironmentSpecularScale = 0.8
    Lighting.Ambient = Color3.fromRGB(135, 135, 135)
    Lighting.OutdoorAmbient = Color3.fromRGB(180, 180, 180)

    local sky = Instance.new("Sky")
    sky.SkyboxBk = "rbxassetid://271042516"
    sky.SkyboxDn = "rbxassetid://271077243"
    sky.SkyboxFt = "rbxassetid://271042556"
    sky.SkyboxLf = "rbxassetid://271042310"
    sky.SkyboxRt = "rbxassetid://271042467"
    sky.SkyboxUp = "rbxassetid://271077958"
    sky.CelestialBodiesShown = true
    sky.StarCount = 0
    sky.Parent = Lighting

    local atm = Instance.new("Atmosphere")
    atm.Density = 0.3
    atm.Offset = 0.2
    atm.Color = Color3.fromRGB(199, 199, 199)
    atm.Decay = Color3.fromRGB(255, 255, 255)
    atm.Glare = 0.2
    atm.Haze = 1.5
    atm.Parent = Lighting

    local cc = Instance.new("ColorCorrectionEffect")
    cc.Brightness = 0.05
    cc.Contrast = 0.1
    cc.Saturation = 0.1
    cc.TintColor = Color3.fromRGB(255, 245, 235)
    cc.Parent = Lighting
end

local function resetSky()
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Sky") or v:IsA("Atmosphere") or v:IsA("ColorCorrectionEffect") then
            v:Destroy()
        end
    end

    if Original.Sky then Original.Sky.Parent = Lighting end
    if Original.Atmosphere then Original.Atmosphere.Parent = Lighting end
    if Original.CC then Original.CC.Parent = Lighting end

    Lighting.Brightness = Original.Brightness
    Lighting.ClockTime = Original.ClockTime
    Lighting.Ambient = Original.Ambient
    Lighting.OutdoorAmbient = Original.OutdoorAmbient
    Lighting.EnvironmentDiffuseScale = Original.EnvDiff
    Lighting.EnvironmentSpecularScale = Original.EnvSpec
end

box:AddToggle("DaySky", {
    Text = "Day Sky",
    Default = false,
    Callback = function(Value)
        if Value then
            applyDay()
        else
            resetSky()
        end
    end
})
box:AddToggle("ShaderPvp", {
    Text = "Shader PVP",
    Default = false,

    Callback = function(Value)
        local Lighting = game:GetService("Lighting")

        if Value then
            -- Ambiente
            Lighting.Brightness = 2
            Lighting.ClockTime = 17
            Lighting.GlobalShadows = true
            Lighting.OutdoorAmbient = Color3.fromRGB(70,70,70)
            Lighting.Ambient = Color3.fromRGB(80,80,80)

            Lighting.FogStart = 200
Lighting.FogEnd = 3000

atmosphere.Density = 0.08
atmosphere.Haze = 0.2

blur.Size = 1
            -- Atmosfera
            local atmosphere = Lighting:FindFirstChild("PVPAtmosphere") or Instance.new("Atmosphere")
            atmosphere.Name = "PVPAtmosphere"
            atmosphere.Parent = Lighting
            atmosphere.Density = 0.15
atmosphere.Haze = 0.5
            atmosphere.Color = Color3.fromRGB(190,190,190)
            atmosphere.Decay = Color3.fromRGB(60,60,60)
            atmosphere.Glare = 0
            atmosphere.Haze = 2.5

            -- Blur
            local blur = Lighting:FindFirstChild("PVPBlur") or Instance.new("BlurEffect")
            blur.Name = "PVPBlur"
            blur.Parent = Lighting
            blur.Size = 3

            -- Correção de cor
            local color = Lighting:FindFirstChild("PVPColor") or Instance.new("ColorCorrectionEffect")
            color.Name = "PVPColor"
            color.Parent = Lighting
            color.Brightness = -0.05
            color.Contrast = 0.35
            color.Saturation = -0.2
            color.TintColor = Color3.fromRGB(220,220,220)

            -- Bloom
            local bloom = Lighting:FindFirstChild("PVPBloom") or Instance.new("BloomEffect")
            bloom.Name = "PVPBloom"
            bloom.Parent = Lighting
            bloom.Intensity = 0.3
            bloom.Size = 20
            bloom.Threshold = 1

            -- Profundidade
            local dof = Lighting:FindFirstChild("PVPDOF") or Instance.new("DepthOfFieldEffect")
            dof.Name = "PVPDOF"
            dof.Parent = Lighting
            dof.FarIntensity = 0.15
            dof.FocusDistance = 50
            dof.InFocusRadius = 25
            dof.NearIntensity = 0

            -- Raios de sol
            local sun = Lighting:FindFirstChild("PVPSun") or Instance.new("SunRaysEffect")
            sun.Name = "PVPSun"
            sun.Parent = Lighting
            sun.Intensity = 0.02
            sun.Spread = 0.4

        else
            for _, name in ipairs({
                "PVPAtmosphere",
                "PVPBlur",
                "PVPColor",
                "PVPBloom",
                "PVPDOF",
                "PVPSun"
            }) do
                local obj = Lighting:FindFirstChild(name)
                if obj then
                    obj:Destroy()
                end
            end

            Lighting.Brightness = 2
            Lighting.ClockTime = 14
            Lighting.FogStart = 0
            Lighting.FogEnd = 100000
            Lighting.GlobalShadows = true
            Lighting.OutdoorAmbient = Color3.fromRGB(128,128,128)
            Lighting.Ambient = Color3.fromRGB(128,128,128)
        end
    end
})
box:AddToggle("FPSOptimizer", {
    Text = "FPS Optimizer",
    Default = false,

    Callback = function(Value)
        if Value then
            -- Iluminação
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 100000
            Lighting.Brightness = 1

            -- Remove efeitos
            for _, v in ipairs(Lighting:GetChildren()) do
                if v:IsA("BlurEffect")
                or v:IsA("SunRaysEffect")
                or v:IsA("BloomEffect")
                or v:IsA("ColorCorrectionEffect")
                or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = false
                end
            end

            -- Terreno
            if Terrain then
                Terrain.WaterWaveSize = 0
                Terrain.WaterWaveSpeed = 0
                Terrain.WaterReflectance = 0
                Terrain.WaterTransparency = 1
            end

            -- Objetos do mapa
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("BasePart") then
                    obj.Material = Enum.Material.Plastic
                    obj.Reflectance = 0
                elseif obj:IsA("Decal") or obj:IsA("Texture") then
                    obj.Transparency = 1
                elseif obj:IsA("ParticleEmitter")
                    or obj:IsA("Trail")
                    or obj:IsA("Fire")
                    or obj:IsA("Smoke")
                    or obj:IsA("Sparkles") then
                    obj.Enabled = false
                end
            end
        else
            -- Restaura algumas configurações
            Lighting.GlobalShadows = true
            Lighting.Brightness = 2

            for _, v in ipairs(Lighting:GetChildren()) do
                if v:IsA("BlurEffect")
                or v:IsA("SunRaysEffect")
                or v:IsA("BloomEffect")
                or v:IsA("ColorCorrectionEffect")
                or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = true
                end
            end
        end
    end
})

end



 
local box = Tabs.Visual:AddRightGroupbox("Map Loader")

local AimTrainerMap = nil
local AimPos = Vector3.new(-195.08, 20.08, 1139.97)

box:AddButton({
    Text = "Teleport to Aim Trainer",
    Callback = function()
        local Player = game.Players.LocalPlayer
        local Character = Player.Character or Player.CharacterAdded:Wait()
        local HRP = Character:WaitForChild("HumanoidRootPart")
        HRP.CFrame = CFrame.new(AimPos + Vector3.new(0, 5, 0))
    end
})


box:AddToggle("StaticAim", {
    Text = "Static Targets",
    Default = false,
    Callback = function(Value)
        if Value then
            if not AimTrainerMap then
                AimTrainerMap = Instance.new("Folder")
                AimTrainerMap.Name = "AimTrainer"
                AimTrainerMap.Parent = workspace
            end

            local Floor = Instance.new("Part")
            Floor.Name = "Floor"
            Floor.Size = Vector3.new(400, 8, 750)
            Floor.Position = AimPos - Vector3.new(0, 8, -175)
            Floor.Anchored = true
            Floor.Material = Enum.Material.Slate
            Floor.Color = Color3.fromRGB(55, 55, 65)
            Floor.Parent = AimTrainerMap
        local WallThickness = 6
local WallHeight = 120

local function CreateWall(pos, size)
    local wall = Instance.new("Part")
    wall.Size = size
    wall.Position = pos
    wall.Anchored = true
    wall.Material = Enum.Material.SmoothPlastic
    wall.Color = Color3.fromRGB(20, 20, 25)
    wall.Transparency = 0.45
    wall.CanCollide = true
    wall.Parent = AimTrainerMap
    return wall
end


CreateWall(
    AimPos + Vector3.new(-200, WallHeight / 2, 175),
    Vector3.new(WallThickness, WallHeight, 750)
)


CreateWall(
    AimPos + Vector3.new(200, WallHeight / 2, 175),
    Vector3.new(WallThickness, WallHeight, 750)
)


CreateWall(
    AimPos + Vector3.new(0, WallHeight / 2, 550),
    Vector3.new(400, WallHeight, WallThickness)
)


CreateWall(
    AimPos + Vector3.new(0, WallHeight / 2, -200),
    Vector3.new(400, WallHeight, WallThickness)
)

            local Positions = {}
            for x = -180, 180, 16 do
                for y = 15, 75, 8 do
                    for z = -170, 350, 18 do
                        table.insert(Positions, AimPos + Vector3.new(x, y, z))
                    end
                end
            end

            local function CreateTarget()
                local Ball = Instance.new("Part")
                Ball.Name = "Target"
                Ball.Shape = Enum.PartType.Ball
                Ball.Size = Vector3.new(4, 4, 4)
                Ball.Material = Enum.Material.Neon
                Ball.Color = Color3.fromRGB(255, 40, 40)
                Ball.Anchored = true
                Ball.CanCollide = false
                Ball.Transparency = 0.1
                Ball.Position = Positions[math.random(#Positions)]
                Ball.Parent = AimTrainerMap

                local PointLight = Instance.new("PointLight")
                PointLight.Color = Color3.fromRGB(255, 60, 60)
                PointLight.Brightness = 2.5
                PointLight.Range = 18
                PointLight.Parent = Ball

                Ball.Touched:Connect(function(hit)
                    if hit and hit.Parent:FindFirstChild("Humanoid") then
                        Ball.Position = Positions[math.random(#Positions)]
                    end
                end)
            end

            for i = 1, 50 do
                CreateTarget()
            end

        elseif AimTrainerMap then
            AimTrainerMap:Destroy()
            AimTrainerMap = nil
        end
    end
})
box:AddToggle("MovingR6Bot", {
    Text = "Moving bot",
    Default = false,
    Callback = function(Value)
        if Value then
            if not AimTrainerMap then
                AimTrainerMap = Instance.new("Folder")
                AimTrainerMap.Name = "AimTrainer"
                AimTrainerMap.Parent = workspace
            end

            
            local old = AimTrainerMap:FindFirstChild("R6Bot")
            if old then old:Destroy() end

            local basePos = Vector3.new(-231.87, 64.46, 1672.23)

            local Bot = Instance.new("Model")
            Bot.Name = "R6Bot"
            Bot.Parent = AimTrainerMap

            
            local Root = Instance.new("Part")
            Root.Name = "HumanoidRootPart"
            Root.Size = Vector3.new(2, 2, 1)
            Root.Transparency = 1
            Root.CanCollide = false
            Root.Anchored = true
            Root.Position = basePos
            Root.Parent = Bot

            
            local Torso = Instance.new("Part")
            Torso.Name = "Torso"
            Torso.Size = Vector3.new(2, 2, 1)
            Torso.Color = Color3.fromRGB(200, 50, 50)
            Torso.Material = Enum.Material.Neon
            Torso.Position = basePos + Vector3.new(0, 3, 0)
            Torso.Parent = Bot

            
            local Head = Instance.new("Part")
            Head.Name = "Head"
            Head.Size = Vector3.new(2, 1, 1)
            Head.Color = Color3.fromRGB(255, 220, 180)
            Head.Material = Enum.Material.SmoothPlastic
            Head.Position = basePos + Vector3.new(0, 5, 0)
            Head.Parent = Bot

            
            local LeftArm = Instance.new("Part")
            LeftArm.Name = "Left Arm"
            LeftArm.Size = Vector3.new(1, 2, 1)
            LeftArm.Color = Color3.fromRGB(200, 50, 50)
            LeftArm.Material = Enum.Material.Neon
            LeftArm.Position = basePos + Vector3.new(-1.5, 3, 0)
            LeftArm.Parent = Bot

            local RightArm = LeftArm:Clone()
            RightArm.Name = "Right Arm"
            RightArm.Position = basePos + Vector3.new(1.5, 3, 0)
            RightArm.Parent = Bot

            
            local LeftLeg = Instance.new("Part")
            LeftLeg.Name = "Left Leg"
            LeftLeg.Size = Vector3.new(1, 2, 1)
            LeftLeg.Color = Color3.fromRGB(30, 30, 40)
            LeftLeg.Material = Enum.Material.Fabric
            LeftLeg.Position = basePos + Vector3.new(-0.5, 1, 0)
            LeftLeg.Parent = Bot

            local RightLeg = LeftLeg:Clone()
            RightLeg.Name = "Right Leg"
            RightLeg.Position = basePos + Vector3.new(0.5, 1, 0)
            RightLeg.Parent = Bot

            
            local function CreateMotor(part0, part1, c0, c1)
                local motor = Instance.new("Motor6D")
                motor.Name = part1.Name .. " Motor"
                motor.Part0 = part0
                motor.Part1 = part1
                motor.C0 = c0 or CFrame.new()
                motor.C1 = c1 or CFrame.new()
                motor.Parent = part0
            end

            CreateMotor(Root, Torso, CFrame.new(0, 0, 0))
            CreateMotor(Torso, Head, CFrame.new(0, 1.5, 0), CFrame.new(0, -0.5, 0))
            CreateMotor(Torso, LeftArm, CFrame.new(-1.5, 0.5, 0), CFrame.new(0, 0.5, 0))
            CreateMotor(Torso, RightArm, CFrame.new(1.5, 0.5, 0), CFrame.new(0, 0.5, 0))
            CreateMotor(Torso, LeftLeg, CFrame.new(-0.5, -1.5, 0), CFrame.new(0, 1, 0))
            CreateMotor(Torso, RightLeg, CFrame.new(0.5, -1.5, 0), CFrame.new(0, 1, 0))

            Bot.PrimaryPart = Root

            
            local t = 0
            local connection
            connection = game:GetService("RunService").Heartbeat:Connect(function(dt)
                if not (Bot and Bot.Parent) then
                    if connection then connection:Disconnect() end
                    return
                end

                t = t + dt * 0.65

                local x = basePos.X + math.sin(t * 1.05) * 68
                local y = basePos.Y + math.sin(t * 0.72) * 29
                local pos = Vector3.new(x, y, basePos.Z)

                Root.Position = pos
                Torso.Position = pos + Vector3.new(0, 3, 0)
                Head.Position = pos + Vector3.new(0, 5, 0)
                LeftArm.Position = pos + Vector3.new(-1.5, 3, 0)
                RightArm.Position = pos + Vector3.new(1.5, 3, 0)
                LeftLeg.Position = pos + Vector3.new(-0.5, 1, 0)
                RightLeg.Position = pos + Vector3.new(0.5, 1, 0)
            end)

        else
            if AimTrainerMap then
                AimTrainerMap:Destroy()
                AimTrainerMap = nil
            end
        end
    end
})
local Terrain = workspace.Terrain

box:AddToggle("WaterToggle", {
    Text = "Water System",
    Default = false,
    Callback = function(Value)

        local center = Vector3.new(100.54, -100.85, 100.34)
        local sizeX = 4000
        local sizeZ = 4000
        local height = 180

        if Value then

            -- Cria o oceano
            Terrain:FillBlock(
                CFrame.new(center),
                Vector3.new(sizeX, height, sizeZ),
                Enum.Material.Water
            )

            local function RemoveWater(startPos, endPos)
                local centerPos = (startPos + endPos) / 2

                local size = Vector3.new(
                    math.abs(endPos.X - startPos.X) + 10,
                    math.abs(endPos.Y - startPos.Y) + 10,
                    math.abs(endPos.Z - startPos.Z) + 10
                )

                Terrain:FillBlock(
                    CFrame.new(centerPos),
                    size,
                    Enum.Material.Air
                )
            end

            -- Áreas sem água
            RemoveWater(Vector3.new(67.64, -8.62, 204.86), Vector3.new(64.61, -68.77, 319.71))
            RemoveWater(Vector3.new(-142.60, -11.10, -43.67), Vector3.new(-67.13, -10.54, 33.32))
            RemoveWater(Vector3.new(143.00, -9.61, 264.90), Vector3.new(164.51, -56.49, 313.10))
            RemoveWater(Vector3.new(52.53, -11.15, 210.08), Vector3.new(52.69, -13.09, 329.59))
            RemoveWater(Vector3.new(124.46, -10.48, 314.90), Vector3.new(16.14, -9.97, 222.29))
            RemoveWater(Vector3.new(12.10, -14.47, 219.33), Vector3.new(125.13, -15.61, 306.55))
            RemoveWater(Vector3.new(-20, -100, 150), Vector3.new(180, 50, 360))

        else

            -- Remove todo o oceano
            Terrain:FillBlock(
                CFrame.new(center),
                Vector3.new(sizeX, height, sizeZ),
                Enum.Material.Air
            )

        end
    end
})

local ponte -- referência global do modelo

box:AddToggle("Ponte Toggle", {
    Text = "Ponte Toggle",
    Default = false,

    Callback = function(Value)

        -- LIGA
        if Value then
            -- evita duplicar ponte
            if ponte then
                ponte:Destroy()
            end

            ponte = Instance.new("Model")
            ponte.Name = "Ponte"
            ponte.Parent = workspace

            local inicio = Vector3.new(-913.53, -9.22, 17.08)
            local comprimento = 120
            local espacamento = 10

            for i = 0, math.floor(comprimento / espacamento) do
                local x = i * espacamento

                -- Piso
                local piso = Instance.new("Part")
                piso.Size = Vector3.new(espacamento, 1, 8)
                piso.Position = inicio + Vector3.new(x, 0, 0)
                piso.Anchored = true
                piso.Material = Enum.Material.WoodPlanks
                piso.Color = Color3.fromRGB(55, 35, 20)
                piso.Parent = ponte

                -- Pilar esquerdo
                local p1 = Instance.new("Part")
                p1.Size = Vector3.new(1, 6, 1)
                p1.Position = piso.Position + Vector3.new(0, -3.5, -3)
                p1.Anchored = true
                p1.Material = Enum.Material.Wood
                p1.Color = piso.Color
                p1.Parent = ponte

                -- Pilar direito
                local p2 = p1:Clone()
                p2.Position = piso.Position + Vector3.new(0, -3.5, 3)
                p2.Parent = ponte

                -- Corrimão esquerdo
                local c1 = Instance.new("Part")
                c1.Size = Vector3.new(espacamento, 0.3, 0.3)
                c1.Position = piso.Position + Vector3.new(0, 2, -3)
                c1.Anchored = true
                c1.Material = Enum.Material.Wood
                c1.Color = piso.Color
                c1.Parent = ponte

                -- Corrimão direito
                local c2 = c1:Clone()
                c2.Position = piso.Position + Vector3.new(0, 2, 3)
                c2.Parent = ponte
            end

        -- DESLIGA
        else
            if ponte then
                ponte:Destroy()
                ponte = nil
            end
        end
    end
})
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

local maxDistance = 20

-- pega partes perto do mouse
local function getNearbyParts(position)
    local parts = {}

    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and not obj.Anchored then
            if (obj.Position - position).Magnitude <= maxDistance then
                table.insert(parts, obj)
            end
        end
    end

    return parts
end

-- junta as partes
local function weldParts(parts)
    if #parts < 2 then return end

    local main = parts[1]

    for i = 2, #parts do
        local weld = Instance.new("WeldConstraint")
        weld.Part0 = main
        weld.Part1 = parts[i]
        weld.Parent = main
    end
end

-- 🔥 BOTÃO JUNÇÃO
box:AddButton({
    Text = "Junção",
    Callback = function()
        local targetPos = mouse.Hit.Position
        local parts = getNearbyParts(targetPos)

        weldParts(parts)
    end
})

-- ⚡ BOTÃO VELOCIDADE
box:AddButton({
    Text = "Velocidade 100",
    Callback = function()
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:FindFirstChildOfClass("Humanoid")

        if humanoid then
            humanoid.WalkSpeed = 100
        end
    end
})
box:AddButton({
    Text = "Spawn Boat",
    Callback = function()
        local Players = game:GetService("Players")
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local root = character:WaitForChild("HumanoidRootPart")

        -- COORDENADAS EXATAS QUE VOCÊ PEDIU
        local posicaoDesejada = CFrame.new(-907.91, -1.13, 5.70)
        local spawnCFrame = posicaoDesejada -- Agora usa a posição fixa, não mais ligada ao jogador

        local boat = Instance.new("Model")
        boat.Name = "BigBoat"
        boat.Parent = workspace

        local function createPart(size, cf, color)
            local p = Instance.new("Part")
            p.Size = size
            p.CFrame = cf
            p.Material = Enum.Material.WoodPlanks
            p.Color = color
            p.Anchored = false
            p.TopSurface = Enum.SurfaceType.Smooth
            p.BottomSurface = Enum.SurfaceType.Smooth
            p.Parent = boat
            return p
        end

        local wood = Color3.fromRGB(105, 70, 40)

        -- Base
        local base = createPart(Vector3.new(30, 2, 12), spawnCFrame, wood)

        -- Laterais
        createPart(Vector3.new(30, 3, 1), spawnCFrame * CFrame.new(0, 3, -5.5), wood)
        createPart(Vector3.new(30, 3, 1), spawnCFrame * CFrame.new(0, 3, 5.5), wood)

        -- Frente
        createPart(Vector3.new(2, 3, 12), spawnCFrame * CFrame.new(15, 3, 0), wood)

        -- Traseira
        createPart(Vector3.new(2, 3, 12), spawnCFrame * CFrame.new(-15, 3, 0), wood)

        -- Piso
        createPart(Vector3.new(26, 1, 10), spawnCFrame * CFrame.new(0, 1.5, 0), Color3.fromRGB(120, 80, 50))


        -- Vela (mastro)
        local mast = createPart(Vector3.new(1, 28, 1), spawnCFrame * CFrame.new(0, 15, 0), Color3.fromRGB(80, 50, 25))

        -- Vela (tecido)
        local sail = Instance.new("Part")
        sail.Size = Vector3.new(0.2, 15, 8)
        sail.CFrame = spawnCFrame * CFrame.new(2.5, 20, 0)
        sail.Material = Enum.Material.Fabric
        sail.Color = Color3.fromRGB(240, 240, 230)
        sail.Anchored = false
        sail.CanCollide = false
        sail.Parent = boat

        -- Soldar tudo na base
        for _, obj in ipairs(boat:GetChildren()) do
            if obj:IsA("BasePart") and obj ~= base then
                local weld = Instance.new("WeldConstraint")
                weld.Part0 = base
                weld.Part1 = obj
                weld.Parent = base
            end
        end

        boat.PrimaryPart = base

        for _, obj in ipairs(boat:GetChildren()) do
            if obj:IsA("BasePart") then
                obj.Massless = false
            end
        end
    end
})


local Players = game:GetService("Players")

local joinConnection



local SoundService = game:GetService("SoundService")



local function playKickSound()
	local s = Instance.new("Sound")
	s.SoundId = "rbxassetid://97643101798871" 
	s.Volume = 3
	s.PlayOnRemove = true
	s.Parent = SoundService
	s:Destroy()
end


do
local box = Tabs.Defence:AddLeftGroupbox("Defence")
local Protected = false

local antiGrabConnection

box:AddToggle("AntiGrab", {
    Text = "Anti Grab",
    Default = false,

    Callback = function(v)
        if v then
            if antiGrabConnection then
                antiGrabConnection:Disconnect()
            end

            antiGrabConnection = plr.IsHeld.Changed:Connect(function()
                if not plr.IsHeld.Value then
                    return
                end

                local character = plr.Character
                if not character then
                    return
                end

                local root = character:FindFirstChild("HumanoidRootPart")
                if not root then
                    return
                end

                Struggle:FireServer()

                root.Anchored = true
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero

                repeat
                    Struggle:FireServer()
                    task.wait()
                until not plr.IsHeld.Value

                root.Anchored = false
            end)
        else
            if antiGrabConnection then
                antiGrabConnection:Disconnect()
                antiGrabConnection = nil
            end

            local character = plr.Character
            local root = character and character:FindFirstChild("HumanoidRootPart")

            if root then
                root.Anchored = false
            end
        end
    end
})


local SoundService = game:GetService("SoundService")



local function playKickSound()
	local s = Instance.new("Sound")
	s.SoundId = "rbxassetid://97643101798871" 
	s.Volume = 3
	s.PlayOnRemove = true
	s.Parent = SoundService
	s:Destroy()
end


local function notifyKick(displayName, username)
	Library:Notify({
		Title = "VTX Hub",
		Description = displayName .. " (" .. username .. ") has been kicked",
		Time = 4,
})
end





local function getClosestPlayer(pos)
	local closestPlr = nil
	local closestDist = math.huge
	for _, plr in ipairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character then
			local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
			if hrp then
				local dist = (hrp.Position - pos).Magnitude
				if dist < closestDist then
					closestDist = dist
					closestPlr = plr
				end
			end
		end
	end
	return closestPlr
end


Workspace.ChildAdded:Connect(function(obj)
	if obj.Name == "BlackHoleKick" or obj.Name == "BlackHoleDetected" then
		task.wait(0.05)
		local pos
		if obj:IsA("BasePart") then
			pos = obj.Position
		elseif obj:IsA("Model") and obj.PrimaryPart then
			pos = obj.PrimaryPart.Position
		end
		if not pos then
			return
		end
		local plr = getClosestPlayer(pos)
		if not plr then
			return
		end
		playKickSound()
		notifyKick(plr.DisplayName, plr.Name)
	end
end)




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
        Lighting.Brightness = 2.9        
        Lighting.Ambient = Color3.fromRGB(130, 145, 170)

        effects.Bloom.Intensity = 1.75   
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
        effects.Atmosphere.Glare = 0.42    

        effects.SunRays.Intensity = 0.26   
        effects.SunRays.Spread = 0.8       
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
        Lighting.Brightness = 3.1          

        effects.Bloom.Intensity = 1.95     
        effects.Bloom.Size = 29
        effects.Bloom.Threshold = 0.68

        effects.Color.Brightness = 0.13
        effects.Color.Contrast = 0.16
        effects.Color.Saturation = 1.32
        effects.Color.TintColor = Color3.fromRGB(205, 230, 255)

        effects.Atmosphere.Density = 0.24
        effects.Atmosphere.Color = Color3.fromRGB(175, 205, 255)
        effects.Atmosphere.Decay = Color3.fromRGB(135, 180, 255)
        effects.Atmosphere.Glare = 0.48    
        effects.Atmosphere.Haze = 1.35

        effects.SunRays.Intensity = 0.24   
        effects.SunRays.Spread = 0.72      
    end

    

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
        Text = "divine Golden Hour", 
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
            cons["Auto Reset Running"] = rs.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    Library:Notify("Auto Reset Running", 4)
                    hum:ChangeState("Dead")
                end
            end)
        else
            if cons["AutoReset"] then cons["AutoReset"]:Disconnect() end
        end
    end
})
Toggles.AutoReset:SetValue(true)




local ToyList = {
    ["Coconut"] = "FoodCoconut", ["Banana"] = "FoodBanana", ["Fries"] = "FoodFrenchFries",
    ["MeatStick"] = "FoodMeatStick", ["Poop"] = "PoopPile", ["Donut"] = "FoodDonut",
    ["Cake"] = "FoodCakePink", ["Burger"] = "FoodHamburger", ["Pizza"] = "FoodPizzaCheese",
    ["Hotdog"] = "FoodHotdog", ["Mushroom"] = "FoodMushroomPoison",
    ["Banjo"] = "InstrumentGuitarBanjo", ["Violin"] = "InstrumentGuitarViolin",
    ["Ukulele"] = "InstrumentGuitarUkulele", ["Sax"] = "InstrumentWoodwindSaxophone",
    ["Vuvuzela"] = "InstrumentBrassVuvuzela", ["Bongos"] = "InstrumentDrumBongos",
    ["Mic"] = "InstrumentVoiceMicrophone", 
    ["Pepperoni"] = "FoodPizzaPepperoni", ["Piano"] = "InstrumentPianoMelodica",
    ["Bread"] = "FoodBread", ["Egg"] = "FoodDippyEgg", ["Mayo"] = "FoodMayonnaise",
    ["WhiteMug"] = "CupMugWhite", ["Ocarina"] = "InstrumentWoodwindOcarina",
    ["SparklePoop"] = "PoopPileSparkle", ["BrownMug"] = "CupMugBrown",
    ["Trumpet"] = "InstrumentBrassTrumpet", ["Snare"] = "InstrumentDrumSnare",
}

local dropdownValues = {}
for shortName in pairs(ToyList) do
    table.insert(dropdownValues, shortName)
end
table.sort(dropdownValues)

local SelectedToy = ToyList["Mic"]  


box:AddDropdown("AntiInputLagToy", {
    Text = "Input Lag Item",
    Values = dropdownValues,
    Default = table.find(dropdownValues, "Mic") or 2,
    Callback = function(Value)
        SelectedToy = ToyList[Value]
    end
})

box:AddToggle("Anti Input Lag", {
    Text = "Input Lag",
    Default = false,
    Callback = function(Value)
        _G.AutoSpawnUseToy = Value
        if not Value then return end

        task.spawn(function()
            local Players = game:GetService("Players")
            local ReplicatedStorage = game:GetService("ReplicatedStorage")
            local Workspace = game:GetService("Workspace")
            local RunService = game:GetService("RunService")

            local plr = Players.LocalPlayer
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart", 5)

            local MenuToys = ReplicatedStorage:WaitForChild("MenuToys", 10)
            if not MenuToys then return end
            local SpawnRemote = MenuToys:WaitForChild("SpawnToyRemoteFunction", 10)
            if not SpawnRemote then return end

            local SelectedToyName = SelectedToy

            while _G.AutoSpawnUseToy do
                char = plr.Character or plr.CharacterAdded:Wait()
                hrp = char:FindFirstChild("HumanoidRootPart") or char:WaitForChild("HumanoidRootPart", 2)

                local toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if not toysFolder then
                    RunService.RenderStepped:Wait()
                    continue
                end

                local toy = toysFolder:FindFirstChild(SelectedToyName)
                if not toy then
                    pcall(function()
                        SpawnRemote:InvokeServer(
                            SelectedToyName,
                            hrp.CFrame * CFrame.new(0, 1000, 0),
                            Vector3.zero
                        )
                    end)
                    RunService.RenderStepped:Wait()
                    continue
                end

                local holdPart = toy:FindFirstChild("HoldPart")
                if holdPart then
                    local holdingPlayer = holdPart:FindFirstChild("HoldingPlayer") and holdPart.HoldingPlayer.Value

                    if holdingPlayer and holdingPlayer ~= plr then
                        pcall(function()
                            holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 1000, 0), Vector3.zero)
                        end)
                    else
                        pcall(function()
                            holdPart.HoldItemRemoteFunction:InvokeServer(toy, char)
                            holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 1000, 0), Vector3.zero)
                        end)
                    end
                end

                RunService.RenderStepped:Wait()
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
local BreakPCLDConnection1
local BreakPCLDConnection2

box:AddToggle("BreakPCLD", {
    Text = "Break PCLD",
    Default = false,

    Callback = function(Value)
        if Value then
            print("BREAK PCLD ativado")

            local resets = 0
            local maxResets = 1
            local player = game.Players.LocalPlayer

            local function resetCharacter()
                if resets < maxResets then
                    resets += 1

                    task.wait(0.2)

                    if player.Character then
                        player.Character:BreakJoints()
                    end
                end
            end

            BreakPCLDConnection1 = player.CharacterAdded:Connect(function()
                resetCharacter()
            end)

            resetCharacter()

            local autoReset = false

            BreakPCLDConnection2 = player.CharacterAdded:Connect(function(character)
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
            print("Break PCLD desativado")

            if BreakPCLDConnection1 then
                BreakPCLDConnection1:Disconnect()
                BreakPCLDConnection1 = nil
            end

            if BreakPCLDConnection2 then
                BreakPCLDConnection2:Disconnect()
                BreakPCLDConnection2 = nil
            end
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
    CurrentValue = false,
    Flag = "AntiKickToggle",
    Callback = function(Value)
        getgenv().AntiKickEnabled = Value
        
        if Value then
            task.spawn(function()
                local plr = game.Players.LocalPlayer
                local inv = workspace[plr.Name.."SpawnedInToys"]
                local hrp

                local setOwner = game.ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = game.ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local destroyrem = game.ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
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
                    local ToyFolder
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return false
                    end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        for _, b in pairs(v.PlotSign.ThisPlotsOwners:GetChildren()) do
                            if b.Value == plr.Name then
                                ToyFolder = workspace.PlotItems[v.Name]
                            end
                        end
                    end
                    if ToyFolder then 
                        return true, ToyFolder
                    else 
                        return false
                    end
                end

                local function StickKunai(kunai)
                    if not kunai or not kunai:FindFirstChild("StickyPart") then return end

                    local currentHRP = getHRP()
                    
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai["SoundPart"]:FindFirstChild("PartOwner") or kunai["SoundPart"].PartOwner.Value ~= plr.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    stickyEvent:FireServer(
                        kunai.StickyPart,
                        currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart"),
                        CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90))
                    )
                    
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(255, 0, 0)
                            high.Parent = obj

                        elseif obj.Name == "Main" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(255, 255, 255)
                            high.Parent = obj

                        elseif obj:IsA("BasePart") then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 1
                        end
                    end
                end

                local function ClearKunai()
                    for _,v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" then
                            destroyrem:FireServer(v)
                        end
                    end
                end

                local function SpawnToy(name)
                    while not canSpawn.Value do
                        canSpawn.Changed:Wait()
                    end

                    local currentHRP = getHRP()
                    
                    task.spawn(function()
                        game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                            name,
                            currentHRP.CFrame * CFrame.new(0, 15, 20),
                            Vector3.new(1,5,0)
                        )
                    end)
                    
                    local boolik, house = CheckForHome()
                    if boolik then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return inv:WaitForChild(name, 2)
                    elseif workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and not boolik then 
                        return nil
                    end
                end

                while getgenv().AntiKickEnabled do 
                    task.wait(0.1)

                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    
                    local kunai = inv:FindFirstChild("NinjaShuriken")
                    
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house and workspace.Plots:FindFirstChild(house.Name) and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]:FindFirstChild("Value") and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]["Value"]["TimeRemainingNum"].Value > 89 then 
                            kunai = SpawnToy("NinjaShuriken")
                            if kunai == nil then continue end
                            kunai.Name = "AntiKick" 
                            StickKunai(kunai)
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
                        wait(0.1)
                    until not kunai or not getgenv().AntiKickEnabled 
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
                            wait(0.1)
                        until not getgenv().AntiKickEnabled or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        
                        if not kunai or not kunai:FindFirstChild("StickyPrt") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
            end)
        end
    end,
})
box:AddToggle("AntiKick", {
    Text = "Anti Kick (Anti Remove)",
    CurrentValue = false,
    Flag = "AntiKickToggle",
    Callback = function(Value)
        getgenv().AntiKickEnabled = Value
        
        if Value then
            task.spawn(function()
                local plr = game.Players.LocalPlayer
                local inv = workspace[plr.Name.."SpawnedInToys"]
                local hrp

                local setOwner = game.ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = game.ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local destroyrem = game.ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                local canSpawn = plr:WaitForChild("CanSpawnToy")

                
                local FIXED_SPAWN = CFrame.new(-539.21, 2.28, 99.30)

                local function getHRP()
                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        return plr.Character.HumanoidRootPart
                    else
                        local character = plr.CharacterAdded:Wait()
                        return character:WaitForChild("HumanoidRootPart")
                    end
                end

                local function CheckForHome()
                    local ToyFolder
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return false
                    end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        for _, b in pairs(v.PlotSign.ThisPlotsOwners:GetChildren()) do
                            if b.Value == plr.Name then
                                ToyFolder = workspace.PlotItems[v.Name]
                            end
                        end
                    end
                    if ToyFolder then 
                        return true, ToyFolder
                    else 
                        return false
                    end
                end

                local function StickKunai(kunai)
                    if not kunai or not kunai:FindFirstChild("StickyPart") then return end

                    local currentHRP = getHRP()
                    
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai["SoundPart"]:FindFirstChild("PartOwner") or kunai["SoundPart"].PartOwner.Value ~= plr.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    stickyEvent:FireServer(
                        kunai.StickyPart,
                        currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart"),
                        CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90))
                    )
                    
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(255, 0, 0)
                            high.Parent = obj

                        elseif obj.Name == "Main" then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 0
                            local high = Instance.new("Highlight")
                            high.FillColor = Color3.fromRGB(255, 255, 255)
                            high.Parent = obj

                        elseif obj:IsA("BasePart") then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = 1
                        end
                    end
                end

                local function ClearKunai()
                    for _,v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" then
                            destroyrem:FireServer(v)
                        end
                    end
                end

                local function SpawnToy(name)
                    while not canSpawn.Value do
                        canSpawn.Changed:Wait()
                    end

                    local currentHRP = getHRP()
                    
                    task.spawn(function()
                        game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                            name,
                            FIXED_SPAWN,           
                            Vector3.new(1,5,0)
                        )
                    end)
                    
                    local boolik, house = CheckForHome()
                    if boolik then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return inv:WaitForChild(name, 2)
                    elseif workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and not boolik then 
                        return nil
                    end
                end

                while getgenv().AntiKickEnabled do 
                    task.wait(0.1)

                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    
                    local kunai = inv:FindFirstChild("NinjaShuriken")
                    
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house and workspace.Plots:FindFirstChild(house.Name) and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]:FindFirstChild("Value") and workspace.Plots:FindFirstChild(house.Name)["PlotSign"]["ThisPlotsOwners"]["Value"]["TimeRemainingNum"].Value > 89 then 
                            kunai = SpawnToy("NinjaShuriken")
                            if kunai == nil then continue end
                            kunai.Name = "AntiKick" 
                            StickKunai(kunai)
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
                        wait(0.1)
                    until not kunai or not getgenv().AntiKickEnabled 
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
                            wait(0.1)
                        until not getgenv().AntiKickEnabled or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        
                        if not kunai or not kunai:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
            end)
        end
    end,
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
    Callback = function(state)
        
        if not state then
            if antiburn then antiburn:Disconnect() end
            if antiburn1 then antiburn1:Disconnect() end
            if antiburnLoop then antiburnLoop:Disconnect() end
            return
        end

        local plr = game.Players.LocalPlayer
        local character = plr.Character or plr.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local hrp = character:WaitForChild("HumanoidRootPart")

        local barrier = workspace:FindFirstChild("Plots", true) 
                     and workspace.Plots:FindFirstChild("Plot1") 
                     and workspace.Plots.Plot1.Barrier:FindFirstChild("PlotBarrier")

        local originalCFrame = barrier and barrier.CFrame

        local function handleFireDebounce(hum)
            if hum.FireDebounce.Value == true then
                task.spawn(function()
                    if barrier then
                        local startTime = tick()
                        repeat
                            barrier.CFrame = hrp.CFrame * CFrame.new(0, 0, 0) 
                            task.wait()
                        until not hum.FireDebounce.Value or (tick() - startTime > 5)
                        
                        task.wait(0.5)
                        hum.FireDebounce.Value = false
                    else
                        
                        hum.FireDebounce.Value = false
                    end
                    
                    
                    if barrier and originalCFrame then
                        barrier.CFrame = originalCFrame
                    end
                end)
            end
        end

        
        antiburn = humanoid.FireDebounce.Changed:Connect(function()
            handleFireDebounce(humanoid)
        end)

        
        antiburn1 = plr.CharacterAdded:Connect(function(newChar)
            character = newChar
            humanoid = newChar:WaitForChild("Humanoid")
            hrp = newChar:WaitForChild("HumanoidRootPart")
            
            if antiburn then antiburn:Disconnect() end
            
            antiburn = humanoid.FireDebounce.Changed:Connect(function()
                handleFireDebounce(humanoid)
            end)
        end)

        
        antiburnLoop = game:GetService("RunService").Heartbeat:Connect(function()
            if humanoid and humanoid.FireDebounce.Value == true then
                handleFireDebounce(humanoid)
            end
        end)
    end
})

local box = Tabs.Defence:AddRightGroupbox("More")

box:AddToggle("AntiBlobman", {
    Text = "Anti Blobman",
    Default = false,

    Callback = function(state)
        if not state then
            for _, c in pairs(cons) do
                if typeof(c) == "RBXScriptConnection" then
                    c:Disconnect()
                elseif typeof(c) == "thread" then
                    task.cancel(c)
                end
            end
            return
        end

        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")

        local lp = Players.LocalPlayer
        local savedCF

        local function getChar()
            return lp.Character
        end

        local function forceExit(char)
            if not char then return end

            local hum = char:FindFirstChildOfClass("Humanoid")
            local hrp = char:FindFirstChild("HumanoidRootPart")

            if hum and hrp then
                for _, v in pairs(char:GetDescendants()) do
                    if v:IsA("Weld") or v:IsA("WeldConstraint") or v:IsA("BallSocketConstraint") then
                        v:Destroy()
                    end
                end

                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                hum.Sit = false
            end
        end

        local function disableBlob(blob)
            for _, v in pairs(blob:GetDescendants()) do
                if v:IsA("Attachment") and (v.Name:find("Grab") or v.Name:find("Hold") or v.Name:find("Attach")) then
                    v:Destroy()
                elseif v:IsA("WeldConstraint") or v:IsA("BallSocketConstraint") or v:IsA("HingeConstraint") then
                    v.Enabled = false
                elseif v:IsA("AlignOrientation") or v:IsA("Weld") or v:IsA("Motor6D") then
                    v.Enabled = false
                end
            end
        end

        local function lock(hrp)
            if not savedCF then
                savedCF = hrp.CFrame
            end

            hrp.CFrame = savedCF
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
        end

        -- aplica em existentes
        for _, v in ipairs(workspace:GetChildren()) do
            if v.Name == "CreatureBlobman" then
                disableBlob(v)
            end
        end

        cons["antiblob"] = workspace.ChildAdded:Connect(function(v)
            if v.Name == "CreatureBlobman" then
                disableBlob(v)
            end
        end)

        cons["mainLoop"] = task.spawn(function()
            while state do
                task.wait()

                local char = getChar()
                if not char then continue end

                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then continue end

                savedCF = hrp.CFrame -- atualiza posição segura

                lock(hrp)

                for _, v in ipairs(workspace:GetChildren()) do
                    if v.Name == "CreatureBlobman" then
                        disableBlob(v)

                        if v:IsDescendantOf(char) or char:IsDescendantOf(v) then
                            forceExit(char)
                            lock(hrp)
                        end
                    end
                end
            end
        end)
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

local antiLagActive = false
local autoAntiLagConn = nil

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

        if not v then
            plr.PlayerScripts.CharacterAndBeamMove.Enabled = true
        end
    end
})

workspace.DescendantAdded:Connect(function(d)
    if not autoantilag then return end
    if d.Name ~= "GrabBeam" then return end

    Lines += 1
    lagger = d.Parent.Parent.Parent

    if Lines > 200 then
        plr.PlayerScripts.CharacterAndBeamMove.Enabled = false

        Library:Notify({
            Title = "Auto Anti Lag",
            Description = lagger.Name .. " Low FPS detected, anti lag enabled",
            Time = 4,
        })

        Lines = 0
    end
end)

local AntiStickyConn

box:AddToggle("AntiSticky", {
    Text = "Anti Sticky",
    Default = false,
    Callback = function(v)

        if AntiStickyConn then
            AntiStickyConn:Disconnect()
            AntiStickyConn = nil
        end

        local function clean(obj)
            if not plr.Character then return end

            -- remove qualquer constraint/weld ligado no personagem
            if obj:IsA("Weld")
            or obj:IsA("WeldConstraint")
            or obj:IsA("Motor6D")
            or obj:IsA("AlignPosition")
            or obj:IsA("AlignOrientation")
            or obj:IsA("BodyPosition")
            or obj:IsA("BodyVelocity") then

                local p0 = obj.Part0
                local p1 = obj.Part1

                if (p0 and p0:IsDescendantOf(plr.Character))
                or (p1 and p1:IsDescendantOf(plr.Character)) then
                    obj:Destroy()
                end
            end
        end

        if v then
            -- limpa o que já existe
            if plr.Character then
                for _,obj in ipairs(plr.Character:GetDescendants()) do
                    clean(obj)
                end
            end

            -- remove quando aparecer novo “sticky”
            AntiStickyConn = plr.Character.DescendantAdded:Connect(function(obj)
                task.wait()
                clean(obj)
            end)
        end
    end
})
box:AddToggle("AntiExplode", {
    Text = "Anti Explode",
    Default = false,
    Callback = function(v)
        if v then
            cons["antiexp"] = workspace.ChildAdded:Connect(function(c)
                if c:IsA("BasePart") and c.Name == "Part" then
                    if (c.Position - HRP.Position).Magnitude <= 40 then
                        task.spawn(function()
                            local rag = hum:FindFirstChild("Ragdolled")

                            HRP.Anchored = true

                            for i = 1, 20 do
                                if rag then
                                    rag.Value = false
                                end
                                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                                hum:ChangeState(Enum.HumanoidStateType.Running)
                                RunService.Heartbeat:Wait()
                            end

                            stvel(HRP)
                            HRP.Anchored = false
                        end)
                    end
                end
            end)
        else
            if cons["antiexp"] then
                cons["antiexp"]:Disconnect()
                cons["antiexp"] = nil
            end
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


end
local box = Tabs.Defence:AddRightGroupbox("Fun")
box:AddToggle("COCONUT PENIS",{
    Text = "Coconut Penis",
    Default = false,
    Callback = function(Value)
        _G.En = Value
        
        if Value then
            task.spawn(function()
                local Me = game.Players.LocalPlayer
                local RunService = game:GetService("RunService")
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                
                local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
                
                local Offsets = {
                    [1] = CFrame.new(-0.45, -1.2, -0.7),
                    [2] = CFrame.new(0.45, -1.2, -0.7),
                    [3] = CFrame.new(0, -1, 0.8)
                }
                
                local Length = 15
                local Coconuts = {}
                
                while _G.En do
                    local Char = Me.Character
                    local Root = Char and Char:FindFirstChild("HumanoidRootPart")
                    
                    if not Root then 
                        task.wait(0.1)
                        continue 
                    end
                    
                    Coconuts = {}
                    for _, toy in pairs(workspace[Me.Name.."SpawnedInToys"]:GetChildren()) do
                        if toy.Name == "FoodCoconut" then
                            table.insert(Coconuts, toy)
                        end
                    end
                    
                    if #Coconuts < (Length + 2) then
                        task.spawn(function()
                            SpawnToy:InvokeServer("FoodCoconut", Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                        end)
                    end
                    
                    for i, Coco in ipairs(Coconuts) do
                        local Part = Coco:FindFirstChild("SoundPart")
                        local HoldPart = Coco:FindFirstChild("HoldPart")
                        local Rigid = HoldPart and HoldPart:FindFirstChild("RigidConstraint")
                        local PartOwner = Part and Part:FindFirstChild("PartOwner")
                        
                        if Part and HoldPart and Rigid then
                            if PartOwner and PartOwner.Value == Me.Name then
                                if i <= 2 then
                                    Part.CFrame = Root.CFrame * Offsets[i] * CFrame.new(Root.Velocity / 100)
                                else
                                    Part.CFrame = Root.CFrame * Offsets[3] * CFrame.new(Root.Velocity / 100) * CFrame.new(0, 0, Offsets[3].Z - (i + 0.2))
                                end
                                Part.Velocity = Vector3.zero
                            end
                            
                            if not PartOwner or PartOwner.Value ~= Me.Name then
                                SetNetworkOwner:FireServer(Part, Part.CFrame)
                            end
                            
                            if Rigid.Attachment1 then
                                DestroyToy:FireServer(Coco)
                            end
                            
                            for _, part in pairs(Coco:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                    part.CanQuery = false
                                    if part.Transparency ~= 1 then
                                        part.Transparency = 0
                                    end
                                end
                            end
                        end
                    end
                    
                    task.wait(0.01)
                end
            end)
        end
    end
})


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
_G.AntiSync = true

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

for _, Player in pairs(Players:GetPlayers()) do
    if Player.Character then
        local HRP = Player.Character:FindFirstChild("HumanoidRootPart")

        if HRP then
            HRP.Massless = true
            HRP.Massless = false
        end
    end
end

RunService.Heartbeat:Connect(function()
    if not _G.AntiSync then
        return
    end

    for _, Player in pairs(Players:GetPlayers()) do
        if Player ~= LocalPlayer and Player.Character then
            local HRP = Player.Character:FindFirstChild("HumanoidRootPart")

            if HRP and HRP.Massless then
                HRP.Massless = false
            end
        end
    end
end)

local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local plr = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ShiftLockEnabled = false
local ShiftLockKey = Enum.KeyCode.LeftShift 

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
    local burg = inv:FindFirstChild("FoodHamburger") or spawntoy("FoodHamburger", HRP.CFrame * CFrame.new(5, 5, 20))
    
    task.wait(0.1)
    grab(burg)
    
    -- Teleporte para a posição que você pediu
    HRP.CFrame = CFrame.new(258.65, -7.35, 439.35)
    
    task.wait(0.25)  -- Aumentado um pouco para dar tempo do glitch funcionar
    
    DestroyToy:FireServer(burg)
    
    -- Volta para a posição original
    task.wait(0.1)
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
    box:AddLabel("TP To Mouse"):AddKeyPicker("TPTOMOUSE", {
        Default = "Z",
        Text = "Teleport To Mouse",
        Callback = function()
            if Mouse.Target then
                local targetCFrame = Mouse.Hit * CFrame.new(1, 2.1, 1)
                
                
                if EnableEffect then
                    spawn(function()
                        createEndermanTeleportEffect(HRP.Position, targetCFrame.Position)
                    end)
                end
                
                
                HRP.CFrame = targetCFrame
                stvel(HRP)
            end
        end
    })
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
    end })

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
                                local blob = gblob() or inv:FindFirstChild("CreatureBlobman") or spawntoy("CreatureBlobman", HRP.CFrame * CFrame.new(0, 0, 20))
                                repeat task.wait() until blob and blob:FindFirstChild("RightDetector") and blob:FindFirstChild("VehicleSeat")
                                blob.Name = "cringe"
                                if blob then
                                    repeat task.wait() blob.VehicleSeat:Sit(hum) until hum.SeatPart == blob.VehicleSeat 
                                    local CG,CD = blob.BlobmanSeatAndOwnerScript.CreatureGrab, blob.BlobmanSeatAndOwnerScript.CreatureRelease
                                    blob.HumanoidRootPart.CFrame = Sets.HRP.CFrame
                                    task.wait(0.5)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.3)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.2)
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
                                if (Sets.HRP.Position - HRP.Position).Magnitude >= 50 and Sets.Char.Parent == workspace then
                                    task.wait(0.1,4)
                                    tp(HRP, Sets.HRP)
                                    task.wait(0.2)
                                    sno(Sets.HRP)
                                    task.wait(0.1,4)
                                    HRP.CFrame = pos
                                    task.wait(0.1,6)
                                    for i,v in pairs(Sets.Char:GetChildren()) do
                                        if v:IsA("Part") and v.Name ~= "Humanoid" then
                                            v.CFrame = pos * offset
                                        end
                                    end
                                end
                                if Sets.HRP.Position.Y < HRP.Position.Y + 5 and Sets.Char.Parent == workspace then
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1999, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.0)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(0, 2000, 0)
                                    end
                                end)
                                if not kickbp or kickbp.Parent ~= Sets.HRP then
                                    kickbp = Instance.new("BodyPosition")
                                    kickbp.Parent = Sets.HRP
                                    kickbp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 200
                                    kickbp.Position = HRP.Position + Vector3.new(4.5, 13.5, 0)
                                end
                                if not kickbg or kickbg.Parent ~= Sets.HRP then
                                    kickbg = Instance.new("BodyGyro")
                                    kickbg.Parent = Sets.HRP
                                    kickbg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    kickbp.D = 250
                                    kickbg.CFrame = CFrame.new(0, 13.5, 0)
                                end
                                task.spawn(function()
                                    for i,v in Sets.Char:GetChildren() do
                                        if HasProperty(v, "AssemblyLinearVelocity") then
                                            stvel(v)
                                            v.Velocity = Vector3.zero
                                        end
                                    end
                                end)
                                kickbp.Position = HRP.Position + Vector3.new(4.5, 13, 0)
                            end
                        elseif Method == "Loop Grab" then
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace then
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 45 then
                                    stvel(HRP)
                                    HRP.CFrame = Sets.HRP.CFrame * CFrame.new(0,0,5)
                                    task.wait(0.0)
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * CFrame.new(1,11,0)
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(1, 13, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.0)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(1, 13, 0)
                                    end
                                end)
                                if Sets.Char.Head:FindFirstChild("PartOwner") then
                                    Sets.HRP.CFrame = HRP.CFrame * offset
                                end
                                sno(Sets.HRP)
                            end
                        elseif Method == "Loop Grab" then
                            if Sets.HRP and hum and HRP and Sets.Char.Parent == workspace then
                                if (Sets.HRP.Position - HRP.Position).Magnitude > 35 then
                                    stvel(HRP)
                                    HRP.CFrame = Sets.HRP.CFrame * CFrame.new(0,2,5)
                                    task.wait(0.0)
                                    repeat task.wait() sno(Sets.HRP) until Sets.Char.Head:FindFirstChild("PartOwner")
                                    HRP.CFrame = pos
                                    Sets.HRP.CFrame = HRP.CFrame * CFrame.new(1,11,0)
                                end
                                task.spawn(function()
                                    if Toggles.EnableRagdoll.Value and PalletForRagdoll and inv:FindFirstChild("PalletForRagdoll") then
                                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(1, 11, 0)
                                        PalletForRagdoll.SoundPart.CFrame = Sets.HRP.CFrame
                                        task.wait(0.0)
                                        PalletForRagdoll.SoundPart.CFrame = HRP.CFrame * CFrame.new(1, 11, 0)
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
                                    task.wait(0.4)
                                    repeat
                                        task.wait()
                                        CG:FireServer(blob.RightDetector, Sets.HRP, blob.RightDetector.RightWeld)
                                        CD:FireServer(blob.RightDetector.RightWeld)
                                    until isnetworkowner(Sets.HRP)
                                    task.wait(0.3)
                                    Sets.HRP.CFrame = pos * offset
                                    HRP.CFrame = pos
                                    DestroyToy:FireServer(inv:FindFirstChild("cringe"))
                                    task.wait(0.2)
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


box:AddToggle("LoopKickBlob", {
    Text = "Loop Kick (Grab + Blob)",
    Default = false,
    Callback = function(ativado)
        if not ativado then return end

        -- Verificações iniciais rápidas
        local nomeAlvo = GrabTarget.Value or BlobmanTarget.Value
        if not nomeAlvo or nomeAlvo == "None" then
            Library:Notify("⚠️ Selecione um alvo primeiro!", 2)
            return Toggles.LoopKickBlob:SetValue(false)
        end

        local alvo = Players:FindFirstChild(getname(nomeAlvo))
        if not alvo or not alvo:IsA("Player") then
            Library:Notify("❌ Alvo não encontrado!", 2)
            return Toggles.LoopKickBlob:SetValue(false)
        end

        local charLocal = plr.Character
        local humLocal = charLocal and charLocal:FindFirstChildOfClass("Humanoid")
        if not humLocal or not humLocal.SeatPart then
            Library:Notify("❌ Sente no Blobman primeiro!", 2)
            return Toggles.LoopKickBlob:SetValue(false)
        end

        local assento = humLocal.SeatPart
        local blob = assento.Parent
        if blob.Name ~= "CreatureBlobman" then
            Library:Notify("❌ Não está no Blobman!", 2)
            return Toggles.LoopKickBlob:SetValue(false)
        end

        -- Carrega objetos uma única vez
        local blobRoot = blob:FindFirstChild("HumanoidRootPart")
        local scriptBlob = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        local CriarGrab = scriptBlob and scriptBlob:FindFirstChild("CreatureGrab")
        local CriarDrop = scriptBlob and scriptBlob:FindFirstChild("CreatureDrop")
        local DetectorDireito = blob:FindFirstChild("RightDetector")
        local soldaFixa = DetectorDireito and (DetectorDireito:FindFirstChildWhichIsA("Weld") or DetectorDireito:FindFirstChild("RightWeld"))

        if not blobRoot or not CriarGrab or not CriarDrop or not DetectorDireito or not soldaFixa then
            Library:Notify("❌ Estrutura incompleta!", 2)
            return Toggles.LoopKickBlob:SetValue(false)
        end

        local posOriginal = blobRoot.CFrame
        local RunService = game:GetService("RunService")
        local conexoes = {}

        -- Proteção mantida
        local function Proteger()
            conexoes.charAdd = alvo.CharacterAdded:Connect(function(novaChar)
                novaChar.DescendantAdded:Connect(function(obj)
                    if not Toggles.LoopKickBlob.Value then return end
                    if (obj:IsA("Weld") or obj:IsA("ManualWeld") or obj:IsA("Motor6D") or obj:IsA("Constraint"))
                    and obj.Part1 and (obj.Part1:IsDescendantOf(charLocal) or obj.Part1:IsDescendantOf(blob)) then
                        obj:Destroy()
                    end
                end)
            end)

            conexoes.heartbeat = RunService.Heartbeat:Connect(function()
                if not Toggles.LoopKickBlob.Value then
                    for _, c in pairs(conexoes) do c:Disconnect() end
                    return
                end
                blobRoot.Velocity = Vector3.zero
                blobRoot.RotVelocity = Vector3.zero
            end)
        end
        Proteger()

        -- Thread principal com 1 Grab apenas
        task.spawn(function()
            -- Pegada inicial
            for _ = 1, 2 do
                if not Toggles.LoopKickBlob.Value then break end
                local charAlvo = alvo.Character
                local rootAlvo = charAlvo and charAlvo:FindFirstChild("HumanoidRootPart")
                local humAlvo = charAlvo and charAlvo:FindFirstChildOfClass("Humanoid")
                if rootAlvo and humAlvo and humAlvo.Health > 0 then
                    blobRoot.CFrame = rootAlvo.CFrame * CFrame.new(0, 0.2, -0.35)
                    pcall(function()
                        -- Apenas 1 Grab
                        CriarGrab:FireServer(DetectorDireito, rootAlvo, soldaFixa)
                        DestroyLine:FireServer(rootAlvo)
                    end)
                end
                task.wait(0.002)
            end

            blobRoot.CFrame = posOriginal
            blobRoot.Velocity = Vector3.zero

            -- ⚡ Loop rápido
            local intervalo = 0.003
            local ultimoTempo = 0

            while Toggles.LoopKickBlob.Value do
                local tempoAtual = tick()
                if tempoAtual - ultimoTempo < intervalo then
                    RunService.Heartbeat:Wait()
                    continue
                end
                ultimoTempo = tempoAtual

                local charAlvo = alvo.Character
                local rootAlvo = charAlvo and charAlvo:FindFirstChild("HumanoidRootPart")
                local humAlvo = charAlvo and charAlvo:FindFirstChildOfClass("Humanoid")

                if not rootAlvo or not humAlvo or humAlvo.Health <= 0 then break end

                pcall(function()
                    humAlvo.PlatformStand = true

                    -- Solta
                    CriarDrop:FireServer(soldaFixa)
                    task.wait(0.0005)

                    -- Chute
                    rootAlvo.CFrame = posOriginal * CFrame.new(math.random(-1,1), 85, 6)
                    rootAlvo.Velocity = Vector3.new(0, 130, math.random(28, 38))
                    DestroyLine:FireServer(rootAlvo)

                    -- Pega de volta com 1 Grab apenas
                    task.wait(0.001)
                    CriarGrab:FireServer(DetectorDireito, rootAlvo, soldaFixa)
                end)

                RunService.Heartbeat:Wait()
            end

            -- Limpeza final
            blobRoot.CFrame = posOriginal
            blobRoot.Velocity = Vector3.zero
            for _, c in pairs(conexoes) do c:Disconnect() end
            Toggles.LoopKickBlob:SetValue(false)
        end)
    end
})

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

                
                local function StartMonitoring()
                    local folderName = target.Name .. "SpawnedInToys"
                    local folder = workspace:FindFirstChild(folderName)

                    if folder then
                        
                        for _, item in ipairs(folder:GetChildren()) do
                            ProcessItem(item)
                        end

                        
                        connections[#connections+1] = folder.ChildAdded:Connect(function(item)
                            task.wait(0.05) 
                            ProcessItem(item)
                        end)
                    end
                end

                StartMonitoring()

                
                connections[#connections+1] = target.CharacterAdded:Connect(function()
                    task.wait(1)
                    StartMonitoring()
                end)

                
                while Toggles.RemoveAntiInputLagTarget.Value do
                    StartMonitoring()
                    task.wait(2)
                end

                
                for _, conn in ipairs(connections) do
                    pcall(function() conn:Disconnect() end)
                end
            end)
        end
    })

    
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
                
                Seat:Sit(hum)
                
                
                task.wait(0.03)
                stvel(HRP)
                HRP.CFrame = HRP.CFrame  
            end
            
            task.wait(0.15) 
        end
    end)
end)


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


local box = Tabs.Target:AddRightGroupbox("Black Hole")

local Enabled = false
local SelectedStyle = "Realistic"
local Connection

local function applyRealisticBH(hole)
    if hole:FindFirstChild("RealisticBH") then return end

    local ok, assets = pcall(function()
        return game:GetObjects("rbxassetid://16797584940")
    end)

    if not ok or not assets[1] then return end

    local folder = Instance.new("Folder")
    folder.Name = "RealisticBH"
    folder.Parent = hole

    for _, obj in ipairs(assets[1]:GetDescendants()) do
        if obj:IsA("ParticleEmitter")
        or obj:IsA("Beam")
        or obj:IsA("Trail")
        or obj:IsA("PointLight")
        or obj:IsA("Sound") then
            obj:Clone().Parent = hole
        end
    end
end

local RunService = game:GetService("RunService")

local function applyGalaxyBH(hole)
	local pos = hole.Position

	local folder = Instance.new("Folder")
	folder.Name = "GalaxyBH"
	folder.Parent = workspace

	-- 🌑 NÚCLEO
	local core = Instance.new("Part")
	core.Shape = Enum.PartType.Ball
	core.Size = Vector3.new(10,10,10)
	core.Anchored = true
	core.CanCollide = false
	core.Material = Enum.Material.Neon
	core.Color = Color3.fromRGB(0,0,0)
	core.Position = pos
	core.Parent = folder

	local coreLight = Instance.new("PointLight")
	coreLight.Color = Color3.fromRGB(190, 90, 255)
	coreLight.Brightness = 12
	coreLight.Range = 45
	coreLight.Parent = core

	-- 🔊 SOM CINEMÁTICO
	local sound = Instance.new("Sound")
	sound.Name = "BlackHoleSound"
	sound.SoundId = "rbxassetid://1843529633" -- ambience sci-fi
	sound.Volume = 0.2
	sound.Looped = true
	sound.RollOffMaxDistance = 250
	sound.EmitterSize = 40
	sound.Parent = core
	sound:Play()

	-- 🌈 PALETA GALÁXIA
	local palette = {
		Color3.fromRGB(170, 90, 255),
		Color3.fromRGB(90, 180, 255),
		Color3.fromRGB(255, 90, 200),
		Color3.fromRGB(120, 255, 230),
		Color3.fromRGB(255, 255, 255)
	}

	local stars = {}

	local arms = 5
	local perArm = 180
	local radius = 110

	-- 🌌 BRAÇOS ESPIRAIS
	for arm = 1, arms do
		for i = 1, perArm do
			local t = i / perArm

			local spiral = t * math.pi * 7
			local angle = spiral + (arm * (math.pi * 2 / arms))

			local jitter = math.random(-10,10) * 0.15
			local r = t * radius + jitter

			local height = math.noise(i * 0.1, arm) * 6

			local star = Instance.new("Part")
			star.Shape = Enum.PartType.Ball
			star.Size = Vector3.new(0.2,0.2,0.2) * (math.random(5,12)/10)
			star.Anchored = true
			star.CanCollide = false
			star.Material = Enum.Material.Neon
			star.Color = palette[math.random(1,#palette)]
			star.Position = pos
			star.Parent = folder

			table.insert(stars, {
				part = star,
				angle = angle,
				radius = r,
				height = height,
				speed = math.random(30,80)/1000
			})
		end
	end

	-- 🌫️ ANEL DE ACRESÇÃO
	local rings = {}
	for i = 1, 80 do
		local ring = Instance.new("Part")
		ring.Shape = Enum.PartType.Ball
		ring.Size = Vector3.new(0.3,0.3,0.3)
		ring.Anchored = true
		ring.CanCollide = false
		ring.Material = Enum.Material.Neon
		ring.Color = Color3.fromRGB(255, 170, 255)
		ring.Transparency = 0.3
		ring.Parent = folder

		table.insert(rings, {
			part = ring,
			angle = math.random() * math.pi * 2,
			radius = math.random(18, 35),
			speed = math.random(40,120)/1000
		})
	end

	-- ✨ PARTICULAS CENTRAIS
	local emitter = Instance.new("ParticleEmitter")
	emitter.Texture = "rbxasset://textures/particles/sparkles_main.dds"
	emitter.Rate = 600
	emitter.Speed = NumberRange.new(1, 6)
	emitter.Lifetime = NumberRange.new(1, 2.5)
	emitter.LightEmission = 1
	emitter.SpreadAngle = Vector2.new(360,360)
	emitter.Size = NumberSequence.new({
		NumberSequenceKeypoint.new(0, 0.4),
		NumberSequenceKeypoint.new(1, 0)
	})
	emitter.Parent = core

	-- ⏱️ LOOP
	local start = tick()

	local conn
	conn = RunService.Heartbeat:Connect(function()
		local time = tick() - start

		if time > 5 then
			conn:Disconnect()
			folder:Destroy()
			return
		end

		-- 💥 núcleo pulsando
		local pulse = 1 + math.sin(time * 10) * 0.25
		core.Size = Vector3.new(10,10,10) * pulse

		coreLight.Brightness = 10 + math.sin(time * 8) * 3

		-- 🔊 SOM DINÂMICO (ISSO AQUI É O QUE DEIXA CINEMÁTICO)
		local intensity = 0.5 + math.sin(time * 2) * 0.5
		sound.Volume = 0.2 + intensity * 0.7
		sound.PlaybackSpeed = 0.85 + intensity * 0.35

		-- 🌌 estrelas orbitando
		for _, s in ipairs(stars) do
			s.angle += s.speed

			local spin = time * 0.8
			local x = math.cos(s.angle + spin) * s.radius
			local z = math.sin(s.angle + spin) * s.radius
			local y = s.height + math.sin(time * 2 + s.radius) * 2

			local pull = math.clamp(1 - (s.radius / radius), 0, 1)

			s.part.Position = pos + Vector3.new(x,y,z) * Vector3.new(1 - pull*0.2,1,1 - pull*0.2)
		end

		-- 🌫️ anel girando
		for _, r in ipairs(rings) do
			r.angle += r.speed

			local x = math.cos(r.angle + time * 2) * r.radius
			local z = math.sin(r.angle + time * 2) * r.radius

			r.part.Position = pos + Vector3.new(x, math.sin(time*3)*0.5, z)
		end
	end)
end


local function applyVoidBH(hole)
    if hole:FindFirstChild("VoidBH") then return end

    local folder = Instance.new("Folder")
    folder.Name = "VoidBH"
    folder.Parent = hole


    local smoke = Instance.new("ParticleEmitter")
    smoke.Texture = "rbxasset://textures/particles/smoke_main.dds"
    smoke.Color = ColorSequence.new(Color3.fromRGB(80,0,120))
    smoke.Rate = 200
    smoke.Speed = NumberRange.new(1,4)
    smoke.Size = NumberSequence.new(10)
    smoke.Lifetime = NumberRange.new(3,6)
    smoke.Parent = hole
end

local RunService = game:GetService("RunService")

local function applyTimeBH(hole)
	if not hole or not hole:IsA("BasePart") then return end
	if hole:FindFirstChild("TimeBH") then return end

	local folder = Instance.new("Folder")
	folder.Name = "TimeBH"
	folder.Parent = hole

	-- 💡 LUZ SUAVE (BRILHO CONTROLADO)
	local light = Instance.new("PointLight")
	light.Color = Color3.fromRGB(210, 180, 140)
	light.Brightness = 2.5
	light.Range = 18
	light.Parent = hole

	-- 🌫️ POEIRA TEMPORAL BRILHANTE (MAS NÃO ESTOURADA)
	local particles = Instance.new("ParticleEmitter")
	particles.Texture = "rbxasset://textures/particles/sparkles_main.dds"
	particles.Color = ColorSequence.new(
		Color3.fromRGB(230, 210, 170),
		Color3.fromRGB(120, 100, 80)
	)
	particles.LightEmission = 0.4 -- brilho leve (IMPORTANTE)
	particles.Rate = 160
	particles.Speed = NumberRange.new(1, 3)
	particles.Lifetime = NumberRange.new(2.5, 4)

	particles.Size = NumberSequence.new({
		NumberSequenceKeypoint.new(0, 0.9),
		NumberSequenceKeypoint.new(1, 0)
	})

	particles.SpreadAngle = Vector2.new(180, 180)
	particles.Rotation = NumberRange.new(0, 360)
	particles.RotSpeed = NumberRange.new(-40, 40)
	particles.Parent = hole

	-- 🌟 ORBES BRILHANTES (EFEITO “ENERGIA PRESA NO TEMPO”)
	local orbs = {}

	for i = 1, 16 do
		local orb = Instance.new("Part")
		orb.Shape = Enum.PartType.Ball
		orb.Size = Vector3.new(0.25,0.25,0.25)
		orb.Anchored = true
		orb.CanCollide = false
		orb.Material = Enum.Material.Neon
		orb.Color = Color3.fromRGB(240, 210, 160)
		orb.Transparency = 0.15
		orb.Parent = folder

		local glow = Instance.new("PointLight")
		glow.Color = orb.Color
		glow.Brightness = 1.5
		glow.Range = 6
		glow.Parent = orb

		table.insert(orbs, {
			part = orb,
			angle = math.random() * math.pi * 2,
			radius = math.random(5, 12),
			speed = math.random(20, 60)/1000
		})
	end

	-- ⏱️ LOOP
	local start = tick()

	local conn
	conn = RunService.Heartbeat:Connect(function()
		if not hole or not hole.Parent then
			conn:Disconnect()
			folder:Destroy()
			return
		end

		local t = tick() - start
		local pos = hole.Position

		-- 🌟 brilho pulsante leve (não estoura)
		local pulse = 1 + math.sin(t * 2) * 0.1
		light.Brightness = 2 + math.sin(t * 3) * 0.8
		particles.Speed = NumberRange.new(1, 3 * pulse)

		-- 🌪️ órbita dos orbes brilhantes
		for _, o in ipairs(orbs) do
			o.angle += o.speed

			local x = math.cos(o.angle + t) * o.radius
			local z = math.sin(o.angle + t) * o.radius
			local y = math.sin(t * 2 + o.radius) * 0.3

			o.part.Position = pos + Vector3.new(x, y, z)
		end
	end)
end

box:AddDropdown("BHStyle", {
    Text = "Black Hole Style",
    Values = {
        "Realistic",
        "Galaxy",
        "Void",
        "Time"
    },
    Default = 1,
    Multi = false,
    Callback = function(v)
        SelectedStyle = v
    end
})

box:AddToggle("CustomBH", {
    Text = "Enable",
    Default = false,
    Callback = function(v)
        Enabled = v

        if Connection then
            Connection:Disconnect()
            Connection = nil
        end

        if not v then return end

        Connection = workspace.DescendantAdded:Connect(function(obj)
            if not Enabled then return end

            if obj.Name == "Hole" and obj:IsA("BasePart") then
                task.wait(0.1)

                if SelectedStyle == "Realistic" then
                    applyRealisticBH(obj)

                elseif SelectedStyle == "Galaxy" then
                    applyGalaxyBH(obj)

                elseif SelectedStyle == "Void" then
                    applyVoidBH(obj)

                elseif SelectedStyle == "Time" then
                    applyTimeBH(obj)
                end
            end
        end)
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
    Default = 5000,
    Min = 1000,
    Max = 50000,
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
            Library:Notify("Packet Lag on", 3)
            task.spawn(function()
                while PacketsEnabled do
                    
                    if AutoStopEnabled then
                        local currentPing = GetPing()
                        if currentPing >= MaxPing then
                            PacketsEnabled = false
                            Library:Notify("Packet off high ping " .. math.floor(currentPing), 5)
                            break
                        end
                    end

                    pcall(function()
                        if AntiDetect then
                            game:GetService("ReplicatedStorage").GrabEvents.CreateGrabLine:FireServer(
                                string.rep("‌‌‌‌‌‌‌‌‌‍‌‌‌‌VTX_Hub is craz‌‌‌‌‌‌‌‌‌‍‌‌‌‌‍‌‌‌‌‌‌‌‌‌‌‍‌‌‌‌‍‌yy⚡", Packets)
                            )
                        else
                            game:GetService("ReplicatedStorage").GrabEvents.ExtendGrabLine:FireServer(
                                string.rep("VTX_Hub is crazyy⚡", Packets)
                            )
                        end
                    end)
                    
                    task.wait(0.05)
                end
            end)
        else
            Library:Notify("Packet Lag off", 2)
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
-- Configurações de cor e transparência da caixa de seleção
SB_LineTransparencyValue = Instance.new("NumberValue")
SB_SurfaceTransparencyValue = Instance.new("NumberValue")
SB_AnchoredColor3 = Instance.new("Color3Value")
SB_AnchoredColor3Surface = Instance.new("Color3Value")
SB_GlueColor3 = Instance.new("Color3Value")
SB_GlueColor3Surface = Instance.new("Color3Value")
SB_MainGlueColor3 = Instance.new("Color3Value")
SB_MainGlueColor3Surface = Instance.new("Color3Value")

-- Valores padrão de transparência
SB_LineTransparencyValue.Value = 0.3
SB_SurfaceTransparencyValue.Value = 0.5

SB_AnchoredColor3.Value = Color3.fromRGB(22, 2, 138)
SB_AnchoredColor3Surface.Value = Color3.fromRGB(38, 85, 172)
SB_GlueColor3.Value = Color3.fromRGB(242, 124, 17)
SB_GlueColor3Surface.Value = Color3.fromRGB(253, 243, 130)
SB_MainGlueColor3.Value = Color3.fromRGB(0, 85, 0)
SB_MainGlueColor3Surface.Value = Color3.fromRGB(89, 225, 65)

-- Função para mudar estado visual da caixa
function ChangeSBstate(selectionBox, selectionBoxState)
    if typeof(selectionBox) == "Instance" and selectionBox:IsA("SelectionBox") then
        selectionBox:SetAttribute("SB_State", selectionBoxState)
        if selectionBoxState == "Anchored" then
            selectionBox.Color3 = SB_AnchoredColor3.Value
            selectionBox.SurfaceColor3 = SB_AnchoredColor3Surface.Value
        elseif selectionBoxState == "Glue" then
            selectionBox.Color3 = SB_GlueColor3.Value
            selectionBox.SurfaceColor3 = SB_GlueColor3Surface.Value
        elseif selectionBoxState == "GluePrimary" then
            selectionBox.Color3 = SB_MainGlueColor3.Value
            selectionBox.SurfaceColor3 = SB_MainGlueColor3Surface.Value
        else
            selectionBox.Color3 = Color3.fromRGB(139, 0, 0)
            selectionBox.SurfaceColor3 = Color3.fromRGB(193, 0, 0)
        end
    end
end

-- Tabela para armazenar objetos ancorados
AnchoredObjects = {}

-- Função principal de ancoragem
function setanchorObject(part)
    if typeof(part) == "Instance" and part.Parent and (part.Parent:IsA("Model") or part.Parent:IsA("Folder")) then
        local parentModel = part.Parent
        if parentModel:IsA("Folder") or parentModel == workspace then
            parentModel = part
        end
        if parentModel:GetAttribute("IsAnchored") or parentModel:GetAttribute("Glue") then
            unAnchorObject(part)
        else
            local anchorPositionBody = parentModel:FindFirstChild("AnchorPositionBody") or (part:FindFirstChild("AnchorPositionBody") or Instance.new("BodyPosition"))
            local anchorGyroBody = parentModel:FindFirstChild("AnchorGyroBody") or (part:FindFirstChild("AnchorGyroBody") or Instance.new("BodyGyro"))
            local objectStateSelectionBox = parentModel:FindFirstChild("ObjectState") or Instance.new("SelectionBox")
            local descendantConnections = {}
            local infiniteVector3 = Vector3.new(math.huge, math.huge, math.huge)
            local zeroVector = Vector3.new(0, 0, 0)
            local partPosition = part.Position
            local currentPart = nil
            local isConnected = false
            local isPartOwnerRemoved = false

            if part.Parent:FindFirstChild("Head") and part.Parent:FindFirstChildOfClass("Humanoid") then
                if game.Players:GetPlayerFromCharacter(part.Parent) then
                    isPartOwnerRemoved = true
                else
                    isConnected = true
                end
            end

            anchorPositionBody.Name = "AnchorPositionBody"
            anchorPositionBody.Position = part.Position
            anchorPositionBody.Parent = part
            anchorGyroBody.Name = "AnchorGyroBody"
            anchorGyroBody.Parent = part
            anchorGyroBody.CFrame = part.CFrame
            anchorGyroBody.D = 950
            anchorGyroBody.P = 40000
            anchorPositionBody.P = 40000
            anchorPositionBody.D = 950

            objectStateSelectionBox.Name = "ObjectState"
            objectStateSelectionBox.LineThickness = 0.025
            objectStateSelectionBox.SurfaceTransparency = SB_SurfaceTransparencyValue.Value
            objectStateSelectionBox.Transparency = SB_LineTransparencyValue.Value
            objectStateSelectionBox.Visible = true
            objectStateSelectionBox.Parent = parentModel
            objectStateSelectionBox.Adornee = parentModel

            local function updateJointMaxForce()
                if parentModel:GetAttribute("IsAnchored") or parentModel:GetAttribute("Glue") then
                    anchorGyroBody.MaxTorque = infiniteVector3
                    anchorPositionBody.MaxForce = infiniteVector3
                end
                if parentModel:GetAttribute("GluePrimary") and not parentModel:GetAttribute("IsAnchored") then
                    ChangeSBstate(objectStateSelectionBox, "GluePrimary")
                elseif parentModel:GetAttribute("Glue") and not parentModel:GetAttribute("IsAnchored") then
                    ChangeSBstate(objectStateSelectionBox, "Glue")
                else
                    ChangeSBstate(objectStateSelectionBox, "Anchored")
                end
            end

            local function resetAnchorForces()
                anchorGyroBody.MaxTorque = Vector3.new()
                anchorPositionBody.MaxForce = Vector3.new()
                ChangeSBstate(objectStateSelectionBox)
                parentModel:SetAttribute("AnchorOwnership", false)
            end

            local function updateSelectionBoxState()
                local selectedObject = objectStateSelectionBox
                ChangeSBstate(objectStateSelectionBox, selectedObject:GetAttribute("SB_State"))
            end

            descendantConnections[1] = parentModel.DescendantAdded:Connect(function(descendant)
                if descendant.Name == "PartOwner" then
                    if descendant.Value ~= game.Players.LocalPlayer.Name then
                        resetAnchorForces()
                    else
                        currentPart = descendant
                        updateJointMaxForce()
                    end
                end
            end)

            descendantConnections[2] = parentModel.DescendantRemoving:Connect(function(descendant)
                if descendant.Name == "PartOwner" and descendant.Value == game.Players.LocalPlayer.Name then
                    if descendant.Value ~= game.Players.LocalPlayer.Name or not isConnected then
                        if descendant.Value == game.Players.LocalPlayer.Name and isPartOwnerRemoved then
                            currentPart = nil
                            resetAnchorForces()
                        end
                    else
                        currentPart = nil
                        updateJointMaxForce()
                    end
                end
            end)

            descendantConnections[#descendantConnections + 1] = SB_LineTransparencyValue.Changed:Connect(function(transparencyValue)
                objectStateSelectionBox.Transparency = transparencyValue
            end)

            descendantConnections[#descendantConnections + 1] = SB_SurfaceTransparencyValue.Changed:Connect(function(surfaceTransparencyValue)
                objectStateSelectionBox.SurfaceTransparency = surfaceTransparencyValue
            end)

            descendantConnections[#descendantConnections + 1] = SB_AnchoredColor3.Changed:Connect(function(_) updateSelectionBoxState() end)
            descendantConnections[#descendantConnections + 1] = SB_AnchoredColor3Surface.Changed:Connect(function(_) updateSelectionBoxState() end)
            descendantConnections[#descendantConnections + 1] = SB_GlueColor3.Changed:Connect(function(_) updateSelectionBoxState() end)
            descendantConnections[#descendantConnections + 1] = SB_GlueColor3Surface.Changed:Connect(function(_) updateSelectionBoxState() end)
            descendantConnections[#descendantConnections + 1] = SB_MainGlueColor3.Changed:Connect(function(_) updateSelectionBoxState() end)
            descendantConnections[#descendantConnections + 1] = SB_MainGlueColor3Surface.Changed:Connect(function(_) updateSelectionBoxState() end)

            task.spawn(function()
                while anchorPositionBody.Parent and not parentModel:GetAttribute("Glue") do
                    if parentModel:GetAttribute("IsAnchored") then
                        anchorGyroBody.MaxTorque = infiniteVector3
                        anchorPositionBody.MaxForce = infiniteVector3
                    else
                        anchorGyroBody.MaxTorque = zeroVector
                        anchorPositionBody.MaxForce = zeroVector
                    end
                    anchorPositionBody.Position = partPosition + Vector3.new(0, 0.001, 0)
                    task.wait()
                    anchorPositionBody.Position = partPosition
                end
            end)

            AnchoredObjects[parentModel] = {
                BodyPosition = anchorPositionBody,
                BodyGyro = anchorGyroBody,
                PartAnchored = part,
                SB = objectStateSelectionBox,
                Connections = descendantConnections,
                Model = parentModel
            }

            parentModel:SetAttribute("IsAnchored", true)
            updateJointMaxForce()
        end
    end
end

-- Função para desancorar
function unAnchorObject(anchoredObject)
    if typeof(anchoredObject) == "Instance" and anchoredObject.Parent and (anchoredObject.Parent:IsA("Model") or anchoredObject.Parent:IsA("Folder")) then
        local anchoredObjectParent = anchoredObject.Parent
        local isAnchoredAttribute = anchoredObjectParent:GetAttribute("IsAnchored")
        local gluePrimaryAttribute = anchoredObjectParent:GetAttribute("GluePrimary")
        
        if not anchoredObjectParent:IsA("Folder") and anchoredObjectParent ~= workspace then
            anchoredObject = anchoredObjectParent
        end

        if AnchoredObjects[anchoredObject] and isAnchoredAttribute then
            local anchoredObjectData = AnchoredObjects[anchoredObject]
            anchoredObjectData.BodyPosition.Parent = anchoredObject
            anchoredObjectData.BodyGyro.Parent = anchoredObject
            anchoredObjectData.PartAnchored = nil

            if gluePrimaryAttribute then
                ChangeSBstate(anchoredObjectData.SB, "GluePrimary")
            else
                anchoredObjectData.SB.Visible = false
            end

            for _, connection in pairs(anchoredObjectData.Connections) do
                connection:Disconnect()
            end

            anchoredObject:SetAttribute("IsAnchored", false)
            anchoredObject:SetAttribute("AnchorOwnership", false)
            
            if not gluePrimaryAttribute then
                AnchoredObjects[anchoredObject] = nil
            end
        end
    end
end

-- Função que verifica o alvo e executa a âncora
function anchorfunc()
    local grabPartsFolder = workspace:FindFirstChild("GrabParts")
    local function isGrabbablePart(part)
        if part and not (part:IsDescendantOf(workspace.Map) or part.Anchored) then
            return true
        end
    end

    if grabPartsFolder then
        local grabbedPart = grabPartsFolder.GrabPart.WeldConstraint.Part1
        if isGrabbablePart(grabbedPart) then
            setanchorObject(grabbedPart)
        end
    elseif game.Players.LocalPlayer.Character then
        local character = game.Players.LocalPlayer.Character
        local cameraPart = character:FindFirstChild("CamPart") or character:FindFirstChild("Head")
        if cameraPart then
            local raycast = workspace:Raycast(cameraPart.Position, cameraPart.CFrame.LookVector * 5000, RaycastParams.new())
            if raycast and raycast.Instance and raycast.Instance.Parent then
                local rayParent = raycast.Instance.Parent
                if rayParent:IsA("Model") and not rayParent:IsDescendantOf(workspace.Map) then
                    setanchorObject(raycast.Instance)
                end
            end
        end
    end
end
local UserInputService = game:GetService("UserInputService")

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end

    if input.KeyCode == Enum.KeyCode.K then
        anchorfunc()
    end
end)



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
            
            
            if bv and bv.Parent then
                bv.Velocity = Vector3.zero
                task.wait() 
            end
            
            if humanoid then
                humanoid.PlatformStand = false
            end

            
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


Players.PlayerAdded:Connect(function(pl)
    task.wait(1.5)
    if not BlacklistEnabled then return end
    
    if table.find(Blacklist, pl.Name) or table.find(Blacklist, pl.DisplayName) then
        Library:Notify("⚠️ BLACKLIST: " .. pl.DisplayName .. " (" .. pl.Name .. ") entrou no servidor!", 6)
    end
end)


Players.PlayerAdded:Connect(UpdateBlacklistUI)
Players.PlayerRemoving:Connect(UpdateBlacklistUI)
UpdateBlacklistUI()


game.Players.PlayerRemoving:Connect(function(pl)
    if pl == plr then
        SaveBlacklist()
    end
end)

local box = Tabs.Notify:AddLeftGroupbox("Notify")
box:AddToggle("NotifyJoin", {
    Text = "Notify Join",
    Default = false,

    Callback = function(v)
        if v then
            joinConnection = Players.PlayerAdded:Connect(function(player)
                Library:Notify({
                    Title = "Player Joined",
                    Description = player.DisplayName .. " (@" .. player.Name .. ") entrou no servidor",
                    Time = 5,
                })
            end)
        else
            if joinConnection then
                joinConnection:Disconnect()
                joinConnection = nil
            end
        end
    end
})
local leaveConnection



box:AddToggle("NotifyLeave", {
    Text = "Notify Leave",
    Default = false,

    Callback = function(v)
        if v then
            leaveConnection = Players.PlayerRemoving:Connect(function(player)
                Library:Notify({
                    Title = "Player Left",
                    Description = player.DisplayName .. " (@" .. player.Name .. ") saiu do servidor",
                    Time = 5,
                })
            end)
        else
            if leaveConnection then
                leaveConnection:Disconnect()
                leaveConnection = nil
            end
        end
    end
})
local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")

local Connection

box:AddToggle("BlobmanNotify", {
    Text = "Blobman Notify",
    Default = false,

    Callback = function(v)
        if v then
            local Blobman = workspace:WaitForChild("Blobman")

            local Seat = Blobman:FindFirstChildWhichIsA("VehicleSeat", true)
                or Blobman:FindFirstChildWhichIsA("Seat", true)

            if not Seat then
                warn("Nenhum Seat encontrado dentro do Blobman.")
                return
            end

            Connection = Seat:GetPropertyChangedSignal("Occupant"):Connect(function()
                local Humanoid = Seat.Occupant
                if Humanoid then
                    local Plr = Players:GetPlayerFromCharacter(Humanoid.Parent)
                    if Plr then
                        pcall(function()
                            StarterGui:SetCore("SendNotification", {
                                Title = "Blobman",
                                Text = Plr.Name .. " entrou no Blobman!",
                                Duration = 5
                            })
                        end)
                    end
                end
            end)

        else
            if Connection then
                Connection:Disconnect()
                Connection = nil
            end
        end
    end
})



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

local box = Tabs["Discord"]:AddLeftGroupbox("Join My Discord Premium And Clan")
box:AddButton({
    Text = "Join Discord Premium",
    Default = false,
    Callback = function(Value)
        if Value then
            local invite = "https://discord.gg/WKPEsuUh4"

            if setclipboard then
                setclipboard(invite)
            end

            if openurl then
                openurl(invite)
            elseif open_url then
                open_url(invite)
            elseif DiscordLib and DiscordLib.Join then
                DiscordLib:Join(invite)
            end

            Library:Notify({
                Title = "Discord",
                Description = "Link do Discord copiado!",
                Time = 5
            })
        end
    end
})

box:AddButton({
    Text = "Join Discord Clan",
    Default = false,
    Callback = function(Value)
        if Value then
            local invite = "https://discord.gg/2aAX36Tkxj"

            if setclipboard then
                setclipboard(invite)
            end

            if openurl then
                openurl(invite)
            elseif open_url then
                open_url(invite)
            elseif DiscordLib and DiscordLib.Join then
                DiscordLib:Join(invite)
            end

            Library:Notify({
                Title = "Discord",
                Description = "Link do Discord copiado!",
                Time = 5
            })
        end
    end
})

local box = Tabs["Teleports"]:AddLeftGroupbox("Teleports")

local Players = game:GetService("Players")
local player = Players.LocalPlayer

local locations = {
    ["Purple House"] = Vector3.new(250, -6, 463),
}

local enabled = false

local function teleportAndAnchor()
    local character = player.Character or player.CharacterAdded:Wait()
    local hrp = character:WaitForChild("HumanoidRootPart")

    local pos = locations["Purple House"]
    if not pos then return end

    -- Teleport
    hrp.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))

    -- espera anti-bug
    task.wait(0.2)

    -- Anchor apenas se ainda estiver ativo
    if enabled and hrp and hrp.Parent then
        hrp.Anchored = true
    end
end

local function unanchor()
    local character = player.Character
    if not character then return end

    local hrp = character:FindFirstChild("HumanoidRootPart")
    if hrp then
        hrp.Anchored = false
    end
end

-- 🔘 TOGGLE
box:AddToggle("PurpleHouseTP", {
    Text = "Teleport",
    Default = false,
    Callback = function(Value)
        enabled = Value

        if Value then
            task.spawn(function()
                teleportAndAnchor()
            end)
        else
            unanchor()
        end
    end
})

-- 🔁 respawn fix mais confiável
player.CharacterAdded:Connect(function()
    if enabled then
        task.wait(1)
        teleportAndAnchor()
    end
end)

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
    frame.Size = UDim2.new(0, 240, 0, 380) -- Aumentado para caber o novo botão
    frame.Position = UDim2.new(0.75, 0, 0.25, 0)
    frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    frame.BackgroundTransparency = 0.65
    frame.BorderSizePixel = 0
    frame.Parent = gui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 12)
    corner.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 40)
    title.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    title.BackgroundTransparency = 0.7
    title.Text = "Teleportes House"
    title.TextColor3 = Color3.new(0, 0, 0)
    title.Font = Enum.Font.GothamBold
    title.TextSize = 16
    title.Parent = frame

    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 12)
    titleCorner.Parent = title

    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 32, 0, 32)
    close.Position = UDim2.new(1, -38, 0, 4)
    close.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
    close.BackgroundTransparency = 0.3
    close.Text = "X"
    close.TextColor3 = Color3.new(1, 1, 1)
    close.Font = Enum.Font.GothamBold
    close.TextSize = 16
    close.Parent = frame

    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = close

    close.MouseButton1Click:Connect(function()
        gui.Enabled = false
    end)

    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, -20, 1, -55)
    container.Position = UDim2.new(0, 10, 0, 45)
    container.BackgroundTransparency = 1
    container.Parent = frame

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 6)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = container

    
    local looptp = false
    local originalCFrame = nil
    local loopConnection = nil

    local function stvel(hrp)
        if hrp and hrp.AssemblyLinearVelocity then
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        end
    end

    local function TP(pos)
        local char = plr.Character or plr.CharacterAdded:Wait()
        local hrp = char:WaitForChild("HumanoidRootPart")
        hrp.CFrame = CFrame.new(pos)
    end

    
    for name, pos in pairs(locations) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, 0, 0, 38)
        btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        btn.BackgroundTransparency = 0.75
        btn.TextColor3 = Color3.new(0, 0, 0)
        btn.Font = Enum.Font.GothamSemibold
        btn.TextSize = 15
        btn.Text = name
        btn.Parent = container

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 10)
        btnCorner.Parent = btn

        btn.MouseButton1Click:Connect(function()
            TP(pos)
        end)
    end

    
    local loopBtn = Instance.new("TextButton")
    loopBtn.Size = UDim2.new(1, 0, 0, 38)
    loopBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    loopBtn.BackgroundTransparency = 0.75
    loopBtn.TextColor3 = Color3.new(0, 0, 0)
    loopBtn.Font = Enum.Font.GothamSemibold
    loopBtn.TextSize = 15
    loopBtn.Text = "Loop Tp - OFF"
    loopBtn.Parent = container

    local loopCorner = Instance.new("UICorner")
    loopCorner.CornerRadius = UDim.new(0, 10)
    loopCorner.Parent = loopBtn

    loopBtn.MouseButton1Click:Connect(function()
        looptp = not looptp
        
        local char = plr.Character
        if not char then return end
        local HRP = char:FindFirstChild("HumanoidRootPart")
        if not HRP then return end

        if looptp then
            originalCFrame = HRP.CFrame
            loopBtn.BackgroundColor3 = Color3.fromRGB(80, 200, 80)  -- Verde quando ativo
            loopBtn.Text = "Loop Tp - ON"
            
            loopConnection = task.spawn(function()
                while looptp and task.wait(0.05) do
                    if HRP and HRP.Parent then
                        local randomOffset = CFrame.new(
                            math.random(-1000, 1000),
                            0,
                            math.random(-1000, 1000)
                        )
                        HRP.CFrame = originalCFrame * randomOffset
                        stvel(HRP)
                    end
                end
            end)
        else
            loopBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            loopBtn.Text = "Loop Tp - OFF"
            if loopConnection then
                task.cancel(loopConnection)
                loopConnection = nil
            end
            stvel(HRP)
            if originalCFrame then
                HRP.CFrame = originalCFrame
            end
        end
    end)

    -- Drag
    local dragging = false
    local dragStart
    local startPos

    title.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
        end
    end)

    title.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    UIS.InputBegan:Connect(function(input, gp)
        if gp then return end
        if input.KeyCode == Enum.KeyCode.Tab then
            gui.Enabled = not gui.Enabled
        end
    end)

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
























local lastLagSource = false
local packetLagNotifyEnabled = true

local function GetSizeMB(StringLength)
    return StringLength / (1024 * 1024)
end

local function StartPacketLagDetector()
    local RS = game:GetService("ReplicatedStorage")

    RS.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(arg1, data)
        if typeof(data) == "string" and not lastLagSource and packetLagNotifyEnabled then
            lastLagSource = true

            local StringLen = #data

            if StringLen > 300 then
                local SizeRounded = math.round(GetSizeMB(StringLen) * 1000) / 1000

                Library:Notify({
                    Title = "VTX_Hub",
                    Description = "PACKET LAG DETECTED\nSource: " ..
                        tostring(arg1):sub(1, 20) ..
                        "\nSize: " .. tostring(SizeRounded) .. " MB",
                    Time = 3,
                    Icon = "rbxassetid://127990091575711"
                })
            end

            task.delay(5, function()
                lastLagSource = false
            end)
        end
    end)
end

StartPacketLagDetector()

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
MenuGroup:AddInput("BackgroundInput", {
    Text        = "Image ID ou URL",
    Default     = "",
    Placeholder = "rbxassetid://... id or https://...",
    Callback    = function(value)
        if not value or value == "" then
            pcall(function() Window:SetBackgroundImage("") end)
            return
        end

        if value:find("^https?://") then
            task.spawn(function()
                local ok, data = pcall(function()
                    return game:HttpGet(value)
                end)
                if not ok or not data then return end

                local fileName = "bg_" .. tostring(math.floor(tick() * 1000)) .. ".png"
                pcall(function()
                    writefile(fileName, data)
                    local assetId = getcustomasset(fileName)
                    Window:SetBackgroundImage(assetId)
                end)
            end)
        else
            local id = tonumber(value) and ("rbxassetid://" .. value) or value
            pcall(function() Window:SetBackgroundImage(id) end)
        end
    end,
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
    clone.Image = "85152712632085"
    clone.ImageColor3 = Color3.fromRGB(255, 0, 0)
    clone.ImageRectOffset = Vector2.new(0,0)
    clone.ImageRectSize = Vector2.new(0,0)
    while task.wait(0.1) do
        gui.Enabled = Snowflakes and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Enabled = Blur and gethui().Obsidian.Main.Visible
        workspace.Camera.Blur.Size = 30
    end
end) 
game:GetService("Players").LocalPlayer.PlayerGui.GameCorrectionsGui.GameCorrectionsUiController.Enabled = false 
