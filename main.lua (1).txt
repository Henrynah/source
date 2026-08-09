local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")
local Debris = game:GetService("Debris")
local ContextActionService = game:GetService("ContextActionService")
local VirtualUser = game:GetService("VirtualUser")
local Lighting = game:GetService("Lighting")
local TextChatService = game:GetService("TextChatService")
local CoreGui = game:GetService("CoreGui")
local StarterGui = game:GetService("StarterGui")
local TeleportService = game:GetService("TeleportService")
local PhysicsService = game:GetService("PhysicsService")
local MaterialService = game:GetService("MaterialService")
local SoundService = game:GetService("SoundService")

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/fiiremax/Scripts/refs/heads/main/Loader.lua')))()

local SpawnedInToys = Workspace[LocalPlayer.Name .. "SpawnedInToys"]
local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
local BombEvents = ReplicatedStorage:WaitForChild("BombEvents")
local DataEvents = ReplicatedStorage:WaitForChild("DataEvents")
local PlayerEvents = ReplicatedStorage:WaitForChild("PlayerEvents")
local HoldEvents = ReplicatedStorage:WaitForChild("HoldEvents")

local SpawnToyRF = MenuToys:WaitForChild("SpawnToyRemoteFunction")
local DestroyToy = MenuToys:WaitForChild("DestroyToy")
local BuyToy = MenuToys:WaitForChild("BuyToyRemoteFunction")
local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
local DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
local ExtendGrabLine = GrabEvents:WaitForChild("ExtendGrabLine")
local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")
local Struggle = CharacterEvents:WaitForChild("Struggle")
local StickyPartEvent = PlayerEvents:WaitForChild("StickyPartEvent")
local BombExplode = BombEvents:WaitForChild("BombExplode")
local UpdateLineColors = DataEvents:WaitForChild("UpdateLineColorsEvent")
local UseEvent = HoldEvents:WaitForChild("Use")

local PoisonContainer = Workspace.Map.FactoryIsland.PoisonContainer.PoisonHurtPart
local PoisonBigHole = Workspace.Map.Hole.PoisonBigHole.PoisonHurtPart
local PoisonSmallHole = Workspace.Map.Hole.PoisonSmallHole.PoisonHurtPart
local ExtinguishPart = Workspace.Map.Hole.PoisonBigHole.ExtinguishPart
local UFO = Workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("OuterUFO")
if UFO and UFO:FindFirstChild("Object") and UFO.Object:FindFirstChild("ObjectModel") then
    UFO = UFO.Object.ObjectModel.PaintPlayerPart
else
    UFO = Instance.new("Part")
    UFO.Size = Vector3.new(0.5, 0.5, 0.5)
    UFO.Transparency = 1
    UFO.Anchored = true
    UFO.Parent = Workspace
end

PoisonContainer.Size = Vector3.new(2, 2, 2)
PoisonBigHole.Size = Vector3.new(2, 2, 2)
PoisonSmallHole.Size = Vector3.new(2, 2, 2)
PoisonContainer.Position = Vector3.new(0, -500, 0)
PoisonBigHole.Position = Vector3.new(0, -500, 0)
PoisonSmallHole.Position = Vector3.new(0, -500, 0)

local Window = OrionLib:MakeWindow({
    Name = "NyPlus Hub",
    ConfigFolder = "NyPlusHub",
    SaveConfig = true,
    HidePremium = false,
    IntroEnabled = false,
    SearchBar = true,
    Openkey = "RightShift"
})

-- Criar todas as abas
local PlayerTab = Window:MakeTab({Name = "Player", Icon = "rbxassetid://7743871002"})
local CombatTab = Window:MakeTab({Name = "Combat", Icon = "rbxassetid://7485051715"})
local GrabTab = Window:MakeTab({Name = "Grab", Icon = "rbxassetid://7733955740"})
local AuraTab = Window:MakeTab({Name = "Auras", Icon = "rbxassetid://7733666258"})
local BlobTab = Window:MakeTab({Name = "Blobman", Icon = "rbxassetid://10152135063"})
local VisualTab = Window:MakeTab({Name = "Visual", Icon = "rbxassetid://7733774602"})
local AntiTab = Window:MakeTab({Name = "Anti", Icon = "rbxassetid://8932822968"})
local KeybindTab = Window:MakeTab({Name = "Keybinds", Icon = "rbxassetid://11710306232"})
local LoopTab = Window:MakeTab({Name = "Loop", Icon = "rbxassetid://7734058599"})
local ExplosionTab = Window:MakeTab({Name = "Explosions", Icon = "rbxassetid://17837704089"})
local TeleportTab = Window:MakeTab({Name = "Teleport", Icon = "rbxassetid://7733992829"})
local MiscTab = Window:MakeTab({Name = "Misc", Icon = "rbxassetid://7733916988"})
local ConfigTab = Window:MakeTab({Name = "Config", Icon = "rbxassetid://7734053495"})
local WhitelistTab = Window:MakeTab({Name = "Whitelist", Icon = "rbxassetid://10723433811"})

-- ============================================
-- FUNÇÕES AUXILIARES
-- ============================================

local function getRoot()
    if LocalPlayer.Character then
        return LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    end
    return nil
end

local function getHumanoid()
    if LocalPlayer.Character then
        return LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    end
    return nil
end

local function getHead()
    if LocalPlayer.Character then
        return LocalPlayer.Character:FindFirstChild("Head")
    end
    return nil
end

local function getTorso()
    if LocalPlayer.Character then
        return LocalPlayer.Character:FindFirstChild("Torso") or LocalPlayer.Character:FindFirstChild("UpperTorso")
    end
    return nil
end

local function isOwner(part)
    if part and part:FindFirstChild("PartOwner") then
        return part.PartOwner.Value == LocalPlayer.Name
    end
    return false
end

local function grabPart(part, safe)
    if not part then return end
    SetNetworkOwner:FireServer(part, part.CFrame)
end

local function ungrabPart(part)
    if part then
        DestroyGrabLine:FireServer(part)
    end
end

local function getGrabPart(toy)
    if toy:FindFirstChild("SoundPart") then
        return toy.SoundPart
    elseif toy:FindFirstChild("HoldPart") then
        return toy.HoldPart
    elseif toy:FindFirstChild("Part") then
        return toy.Part
    elseif toy:FindFirstChild("HitboxPart") then
        return toy.HitboxPart
    end
    for _, v in pairs(toy:GetChildren()) do
        if v:IsA("BasePart") then
            return v
        end
    end
    return nil
end

local function getHitboxPart(toy, bombType)
    if bombType == "BombMissile" then
        return toy:FindFirstChild("PartHitDetector") or toy:FindFirstChild("HitboxBodyTop")
    elseif bombType == "FireworkMissile" then
        return toy:FindFirstChild("PartHitDetector") or toy:FindFirstChild("PyramidOctagon")
    elseif bombType == "BombBalloon" then
        return toy:FindFirstChild("Balloon")
    elseif bombType == "BombDarkMatter" then
        return toy:FindFirstChild("PartHitDetector") or toy:FindFirstChild("Spinner")
    elseif bombType == "BallSnowball" then
        return toy:FindFirstChild("SoundPart")
    end
    return getGrabPart(toy)
end

local function spawnToy(name, cframe)
    if not LocalPlayer.Character then return nil end
    local root = getRoot()
    if not root then return nil end
    local cf = cframe or (root.CFrame * CFrame.new(0, 5, 0))
    SpawnToyRF:InvokeServer(name, cf, Vector3.zero)
    task.wait(0.3)
    for _, v in pairs(SpawnedInToys:GetChildren()) do
        if v.Name == name then
            return v
        end
    end
    return nil
end

local function spawnToyAtHead(name)
    local head = getHead()
    if head then
        return spawnToy(name, CFrame.new(head.Position + Vector3.new(0, 1, 0), head.Position) * CFrame.Angles(math.pi, 0, 0))
    end
    return spawnToy(name)
end

local function deleteToy(toy)
    if toy then
        DestroyToy:FireServer(toy)
    end
end

local function explodeBomb(hitbox, positionPart, explosionPos)
    BombExplode:FireServer({{Hitbox = hitbox, PositionPart = positionPart}, explosionPos})
end

local function getAllPlayers()
    local list = {}
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer then
            table.insert(list, v.Name .. " (" .. v.DisplayName .. ")")
        end
    end
    table.sort(list)
    return list
end

local function getPlayerFromName(name)
    if not name then return nil end
    local clean = name:match("^(.-)%s*%(")
    if clean then
        return Players:FindFirstChild(clean)
    end
    return Players:FindFirstChild(name)
end

local function getNearestPlayer()
    local root = getRoot()
    if not root then return nil end
    local nearest = nil
    local minDist = 50
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local target = plr.Character:FindFirstChild("Torso") or plr.Character:FindFirstChild("UpperTorso") or plr.Character:FindFirstChild("HumanoidRootPart")
            if target then
                local dist = (root.Position - target.Position).Magnitude
                if dist < minDist then
                    minDist = dist
                    nearest = target
                end
            end
        end
    end
    return nearest
end

local function getClosestPlayerToCursor()
    local mouse = LocalPlayer:GetMouse()
    if not mouse then return nil end
    local nearest = nil
    local minDist = math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local pos, onScreen = Camera:WorldToScreenPoint(hrp.Position)
                if onScreen then
                    local dist = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(pos.X, pos.Y)).Magnitude
                    if dist < minDist then
                        minDist = dist
                        nearest = plr
                    end
                end
            end
        end
    end
    return nearest
end

local function getCharacterFromPart(part)
    if not part then return nil end
    local current = part
    while current do
        if current:IsA("Model") and current:FindFirstChild("Humanoid") then
            return current
        end
        current = current.Parent
    end
    return nil
end

-- ============================================
-- HOption TABELA DE CONFIGURAÇÃO
-- ============================================

HOption = {
    walkSpeed = false,
    walkSpeedVal = 50,
    jumpPower = false,
    jumpPowerVal = 100,
    infJump = false,
    gravity = false,
    gravityVal = 50,
    noclip = false,
    silentAim = false,
    silentRange = 50,
    silentPart = "Torso",
    killAura = false,
    killRange = 30,
    poisonAura = false,
    flingAura = false,
    flingStrength = 1000,
    attractAura = false,
    grabAura = false,
    killGrab = false,
    poisonGrab = false,
    noclipGrab = false,
    invisGrab = false,
    superStrength = false,
    strength = 1000,
    invisLine = false,
    furtherExtend = false,
    extendDist = 100,
    antiGrab = false,
    antiRagdoll = false,
    antiExplosion = false,
    antiVoid = false,
    antiLag = false,
    antiFire = false,
    antiBlob = false,
    antiKick = false,
    antiFling = false,
    antiBanana = false,
    antiDsync = true,
    esp = false,
    chams = false,
    loopEnabled = false,
    loopType = "Kill",
    lagServer = false,
    lagIntensity = 100,
    explosionType = "BombMissile",
    explosionTarget = "Mouse",
    explosionDelay = 0,
    explosionAmmount = 5,
    radioactiveGrab = false,
    infiniteZoom = false,
    godMode = false,
    safeReset = false,
    breakBarriers = false,
    autoSpin = false,
    autoSaveTime = false,
    autoClaimPlot = false,
    autoGucci = false,
    controlCreature = false,
    anchorGrab = false,
    compileParts = false,
    perspectiveGrab = false,
    tearAura = false,
    tornadoAura = false,
    blackholeAura = false,
    silentAimV2 = false,
    customFOV = false,
    fovValue = 70,
    fullBright = false,
    muteBoombox = false,
    muteJukebox = false,
    syncTime = false,
    rgbLine = false
}

HFPS = {
    ESP = {},
    Chams = {},
    Anchored = {},
    Compiled = {},
    Tornado = {},
    LineColors = {},
    GrabbedParts = {},
    PoisonParts = {PoisonContainer, PoisonBigHole, PoisonSmallHole}
}

HList = {
    Whitelist = {},
    KillList = {},
    BlobList = {},
    LoopList = {},
    AnchoredParts = {},
    CompiledGroups = {}
}

-- ============================================
-- PLAYER TAB
-- ============================================

PlayerTab:AddSection({Name = "WalkSpeed"})

local walkSpeedToggle = PlayerTab:AddToggle({
    Name = "WalkSpeed",
    Default = false,
    Flag = "WalkSpeed",
    Save = true,
    Callback = function(val)
        HOption.walkSpeed = val
        if not val and LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then hum.WalkSpeed = 16 end
        end
    end
})

local walkSpeedSlider = PlayerTab:AddSlider({
    Name = "Speed Value",
    Min = 16,
    Max = 350,
    Default = 50,
    Increment = 5,
    ValueName = "speed",
    Flag = "WalkSpeedVal",
    Save = true,
    Callback = function(val)
        HOption.walkSpeedVal = val
        if HOption.walkSpeed and LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then hum.WalkSpeed = val end
        end
    end
})

PlayerTab:AddSection({Name = "JumpPower"})

local jumpPowerToggle = PlayerTab:AddToggle({
    Name = "JumpPower",
    Default = false,
    Flag = "JumpPower",
    Save = true,
    Callback = function(val)
        HOption.jumpPower = val
        if not val and LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then hum.JumpPower = 50 end
        end
    end
})

local jumpPowerSlider = PlayerTab:AddSlider({
    Name = "Jump Value",
    Min = 50,
    Max = 500,
    Default = 100,
    Increment = 10,
    ValueName = "power",
    Flag = "JumpPowerVal",
    Save = true,
    Callback = function(val)
        HOption.jumpPowerVal = val
        if HOption.jumpPower and LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then hum.JumpPower = val end
        end
    end
})

PlayerTab:AddSection({Name = "Infinite Jump"})

PlayerTab:AddToggle({
    Name = "Infinite Jump",
    Default = false,
    Flag = "InfJump",
    Save = true,
    Callback = function(val)
        HOption.infJump = val
    end
})

PlayerTab:AddSection({Name = "Gravity"})

local gravityToggle = PlayerTab:AddToggle({
    Name = "Custom Gravity",
    Default = false,
    Flag = "CustomGravity",
    Save = true,
    Callback = function(val)
        HOption.gravity = val
        if not val then
            Workspace.Gravity = 100
        end
    end
})

local gravitySlider = PlayerTab:AddSlider({
    Name = "Gravity Value",
    Min = -200,
    Max = 300,
    Default = 50,
    Increment = 10,
    ValueName = "gravity",
    Flag = "GravityVal",
    Save = true,
    Callback = function(val)
        HOption.gravityVal = val
        if HOption.gravity then
            Workspace.Gravity = val
        end
    end
})

PlayerTab:AddSection({Name = "Noclip"})

PlayerTab:AddToggle({
    Name = "Noclip",
    Default = false,
    Flag = "Noclip",
    Save = true,
    Callback = function(val)
        HOption.noclip = val
    end
})

PlayerTab:AddSection({Name = "God Mode"})

PlayerTab:AddToggle({
    Name = "God Mode",
    Default = false,
    Flag = "GodMode",
    Save = true,
    Callback = function(val)
        HOption.godMode = val
    end
})

PlayerTab:AddSection({Name = "Super Strength"})

PlayerTab:AddToggle({
    Name = "Super Strength",
    Default = false,
    Flag = "SuperStrength",
    Save = true,
    Callback = function(val)
        HOption.superStrength = val
    end
})

local playerStrengthSlider = PlayerTab:AddSlider({
    Name = "Strength Value",
    Min = 100,
    Max = 10000,
    Default = 1000,
    Increment = 100,
    ValueName = "strength",
    Flag = "StrengthVal",
    Save = true,
    Callback = function(val)
        HOption.strength = val
    end
})

PlayerTab:AddSection({Name = "Safe Reset"})

PlayerTab:AddToggle({
    Name = "Safe Reset",
    Default = false,
    Flag = "SafeReset",
    Save = true,
    Callback = function(val)
        HOption.safeReset = val
        local rb = Instance.new("BindableEvent")
        rb.Event:Connect(function()
            if HOption.safeReset then
                local char = LocalPlayer.Character
                if char then
                    local hrp = getRoot()
                    local pos = hrp and hrp.CFrame
                    char:BreakJoints()
                    LocalPlayer.CharacterAdded:Wait()
                    task.wait(0.5)
                    local newHrp = getRoot()
                    if newHrp and pos then
                        newHrp.CFrame = pos
                    end
                end
            else
                if LocalPlayer.Character then
                    local hum = getHumanoid()
                    if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) end
                end
            end
        end)
        StarterGui:SetCore("ResetButtonCallback", rb)
    end
})

PlayerTab:AddSection({Name = "Break Barriers"})

PlayerTab:AddButton({
    Name = "Break House Barriers",
    Callback = function()
        HOption.breakBarriers = true
        local pos = Vector3.new(263.4, -4.79, 466.8)
        local spawnPos = CFrame.new(263.5, -4.5, 486.9)
        local conn = Workspace.ChildAdded:Connect(function(child)
            if HOption.breakBarriers and child.Name == "Part" and (child.Position - pos).Magnitude <= 2 then
                HOption.breakBarriers = false
                conn:Disconnect()
                for _, plot in pairs(Workspace.Plots:GetChildren()) do
                    local barrier = plot:FindFirstChild("Barrier")
                    if barrier then
                        for _, part in pairs(barrier:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = false
                            end
                        end
                    end
                end
                OrionLib:MakeNotification({Name = "Success", Content = "Barriers destroyed!", Time = 3})
            end
        end)
        for i = 1, 10 do
            if not HOption.breakBarriers then break end
            spawnToy("BallSnowball", spawnPos)
            task.wait(1)
        end
        if HOption.breakBarriers then
            HOption.breakBarriers = false
            conn:Disconnect()
            OrionLib:MakeNotification({Name = "Failed", Content = "Could not destroy barriers", Time = 3})
        end
    end
})

PlayerTab:AddSection({Name = "Auto Spin (Coins)"})

PlayerTab:AddToggle({
    Name = "Auto Spin",
    Default = false,
    Flag = "AutoSpin",
    Save = true,
    Callback = function(val)
        HOption.autoSpin = val
    end
})

PlayerTab:AddSection({Name = "Auto Save House Time"})

PlayerTab:AddToggle({
    Name = "Auto Save House Time",
    Default = false,
    Flag = "AutoSaveTime",
    Save = true,
    Callback = function(val)
        HOption.autoSaveTime = val
    end
})

PlayerTab:AddSection({Name = "Auto Claim Plot"})

local plotDropdown = PlayerTab:AddDropdown({
    Name = "Select Plot",
    Default = "Plot3 (Witch)",
    Options = {"Plot1 (Common)", "Plot2 (Lumber)", "Plot3 (Witch)", "Plot4 (American)", "Plot5 (Chinese)"},
    Callback = function(val)
        HOption.selectedPlot = val:match("(%w+)")
    end,
    Flag = "SelectedPlot",
    Save = true
})

PlayerTab:AddButton({
    Name = "Claim Plot",
    Callback = function()
        if not HOption.selectedPlot then return end
        local plot = Workspace.Plots:FindFirstChild(HOption.selectedPlot)
        if plot then
            local sign = plot:FindFirstChild("PlotSign")
            if sign then
                local plus = sign:FindFirstChild("Plus")
                if plus then
                    local grabPartPart = plus:FindFirstChild("PlusGrabPart")
                    if grabPartPart then
                        grabPart(grabPartPart, true)
                        task.wait(0.5)
                        ungrabPart(grabPartPart)
                    end
                end
            end
        end
    end
})

-- ============================================
-- COMBAT TAB
-- ============================================

CombatTab:AddSection({Name = "Silent Aim"})

local silentAimToggle = CombatTab:AddToggle({
    Name = "Silent Aim",
    Default = false,
    Flag = "SilentAim",
    Save = true,
    Callback = function(val)
        HOption.silentAim = val
    end
})

local silentAimRange = CombatTab:AddSlider({
    Name = "Aim Range",
    Min = 10,
    Max = 100,
    Default = 50,
    Increment = 5,
    ValueName = "studs",
    Flag = "SilentRange",
    Save = true,
    Callback = function(val)
        HOption.silentRange = val
    end
})

local targetPartDrop = CombatTab:AddDropdown({
    Name = "Target Part",
    Default = "Torso",
    Options = {"Head", "Torso", "HumanoidRootPart"},
    Flag = "SilentPart",
    Save = true,
    Callback = function(val)
        HOption.silentPart = val
    end
})

CombatTab:AddSection({Name = "Silent Aim V2"})

CombatTab:AddToggle({
    Name = "Silent Aim V2 (Mouse)",
    Default = false,
    Flag = "SilentAimV2",
    Save = true,
    Callback = function(val)
        HOption.silentAimV2 = val
    end
})

CombatTab:AddSection({Name = "Kill Aura"})

local killAuraToggle = CombatTab:AddToggle({
    Name = "Kill Aura",
    Default = false,
    Flag = "KillAura",
    Save = true,
    Callback = function(val)
        HOption.killAura = val
    end
})

local killAuraRange = CombatTab:AddSlider({
    Name = "Kill Range",
    Min = 10,
    Max = 50,
    Default = 30,
    Increment = 5,
    ValueName = "studs",
    Flag = "KillRange",
    Save = true,
    Callback = function(val)
        HOption.killRange = val
    end
})

CombatTab:AddSection({Name = "Tear Aura"})

CombatTab:AddToggle({
    Name = "Tear Aura",
    Default = false,
    Flag = "TearAura",
    Save = true,
    Callback = function(val)
        HOption.tearAura = val
    end
})

CombatTab:AddSection({Name = "Bring Player"})

local bringDropdown = CombatTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.bringTarget = getPlayerFromName(val)
    end,
    Flag = "BringTarget",
    Save = true
})

CombatTab:AddButton({
    Name = "Bring Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local hrp = HOption.bringTarget.Character:FindFirstChild("HumanoidRootPart")
            local root = getRoot()
            if hrp and root then
                grabPart(hrp, true)
                task.wait(0.3)
                hrp.CFrame = root.CFrame + Vector3.new(0, 5, 0)
                ungrabPart(hrp)
            end
        end
    end
})

CombatTab:AddButton({
    Name = "Kill Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local hum = HOption.bringTarget.Character:FindFirstChild("Humanoid")
            if hum then
                hum:ChangeState(Enum.HumanoidStateType.Dead)
            end
        end
    end
})

CombatTab:AddButton({
    Name = "Fling Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local hrp = HOption.bringTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                grabPart(hrp, true)
                local bv = Instance.new("BodyVelocity", hrp)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Vector3.new(0, 10000, 0)
                task.delay(1, function()
                    if bv then bv:Destroy() end
                end)
            end
        end
    end
})

CombatTab:AddSection({Name = "Ragdoll Player"})

CombatTab:AddButton({
    Name = "Ragdoll Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local hrp = HOption.bringTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                RagdollRemote:FireServer(hrp, 0)
            end
        end
    end
})

CombatTab:AddSection({Name = "Snowball Ragdoll"})

CombatTab:AddButton({
    Name = "Snowball Ragdoll Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local head = HOption.bringTarget.Character:FindFirstChild("Head")
            if head then
                local toy = spawnToyAtHead("BallSnowball")
                if toy then
                    local sp = toy:FindFirstChild("SoundPart")
                    if sp then
                        grabPart(sp, true)
                        firetouchinterest(sp, head, 0)
                        task.delay(2, function()
                            deleteToy(toy)
                        end)
                    end
                end
            end
        end
    end
})

CombatTab:AddSection({Name = "Banana Ragdoll"})

CombatTab:AddButton({
    Name = "Banana Ragdoll Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local leg = HOption.bringTarget.Character:FindFirstChild("Right Leg") or HOption.bringTarget.Character:FindFirstChild("RightLowerLeg")
            if leg then
                local toy = spawnToyAtHead("FoodBanana")
                if toy then
                    local peel = toy:FindFirstChild("BananaPeel")
                    if peel then
                        grabPart(peel, true)
                        firetouchinterest(peel, leg, 0)
                        task.delay(2, function()
                            deleteToy(toy)
                        end)
                    end
                end
            end
        end
    end
})

CombatTab:AddSection({Name = "Fire Player"})

CombatTab:AddButton({
    Name = "Fire Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local hrp = HOption.bringTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local toy = spawnToyAtHead("Campfire")
                if toy then
                    local firePart = toy:FindFirstChild("FirePlayerPart")
                    if firePart then
                        grabPart(firePart, true)
                        firePart.Position = hrp.Position
                        task.delay(3, function()
                            deleteToy(toy)
                        end)
                    end
                end
            end
        end
    end
})

CombatTab:AddSection({Name = "Anti Kunai"})

CombatTab:AddButton({
    Name = "Anti Kunai Player",
    Callback = function()
        if HOption.bringTarget and HOption.bringTarget.Character then
            local leg = HOption.bringTarget.Character:FindFirstChild("Left Leg") or HOption.bringTarget.Character:FindFirstChild("LeftLowerLeg")
            if leg then
                local toy = spawnToyAtHead("SprayCanWD")
                if toy then
                    local remover = toy:FindFirstChild("StickyRemoverPart")
                    if remover then
                        grabPart(remover, true)
                        firetouchinterest(remover, leg, 0)
                        task.delay(2, function()
                            deleteToy(toy)
                        end)
                    end
                end
            end
        end
    end
})

-- ============================================
-- GRAB TAB
-- ============================================

GrabTab:AddSection({Name = "Grab Mods"})

GrabTab:AddToggle({
    Name = "Kill Grab",
    Default = false,
    Flag = "KillGrab",
    Save = true,
    Callback = function(val)
        HOption.killGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Poison Grab",
    Default = false,
    Flag = "PoisonGrab",
    Save = true,
    Callback = function(val)
        HOption.poisonGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Radioactive Grab",
    Default = false,
    Flag = "RadioactiveGrab",
    Save = true,
    Callback = function(val)
        HOption.radioactiveGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Noclip Grab",
    Default = false,
    Flag = "NoclipGrab",
    Save = true,
    Callback = function(val)
        HOption.noclipGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Invisible Grab",
    Default = false,
    Flag = "InvisGrab",
    Save = true,
    Callback = function(val)
        HOption.invisGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Super Strength Grab",
    Default = false,
    Flag = "SuperStrengthGrab",
    Save = true,
    Callback = function(val)
        HOption.superStrength = val
    end
})

local grabStrengthSlider = GrabTab:AddSlider({
    Name = "Strength Value",
    Min = 100,
    Max = 10000,
    Default = 1000,
    Increment = 100,
    ValueName = "strength",
    Flag = "GrabStrengthVal",
    Save = true,
    Callback = function(val)
        HOption.strength = val
    end
})

GrabTab:AddToggle({
    Name = "Massless Grab",
    Default = false,
    Flag = "MasslessGrab",
    Save = true,
    Callback = function(val)
        HOption.masslessGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Ragdoll Grab",
    Default = false,
    Flag = "RagdollGrab",
    Save = true,
    Callback = function(val)
        HOption.ragdollGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Crazy Grab",
    Default = false,
    Flag = "CrazyGrab",
    Save = true,
    Callback = function(val)
        HOption.crazyGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Spin Grab",
    Default = false,
    Flag = "SpinGrab",
    Save = true,
    Callback = function(val)
        HOption.spinGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Ultra Grab",
    Default = false,
    Flag = "UltraGrab",
    Save = true,
    Callback = function(val)
        HOption.ultraGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Infinite Zoom (Q/E)",
    Default = false,
    Flag = "InfiniteZoom",
    Save = true,
    Callback = function(val)
        HOption.infiniteZoom = val
    end
})

GrabTab:AddSection({Name = "Line Mods"})

GrabTab:AddToggle({
    Name = "Invisible Line",
    Default = false,
    Flag = "InvisLine",
    Save = true,
    Callback = function(val)
        HOption.invisLine = val
        if val then
            CreateGrabLine:FireServer()
        end
    end
})

GrabTab:AddToggle({
    Name = "Further Extend",
    Default = false,
    Flag = "FurtherExtend",
    Save = true,
    Callback = function(val)
        HOption.furtherExtend = val
        if val then
            ExtendGrabLine:FireServer(HOption.extendDist)
        end
    end
})

local extendSlider = GrabTab:AddSlider({
    Name = "Extend Distance",
    Min = 10,
    Max = 500,
    Default = 100,
    Increment = 10,
    ValueName = "studs",
    Flag = "ExtendDist",
    Save = true,
    Callback = function(val)
        HOption.extendDist = val
        if HOption.furtherExtend then
            ExtendGrabLine:FireServer(val)
        end
    end
})

GrabTab:AddToggle({
    Name = "Crazy Line",
    Default = false,
    Flag = "CrazyLine",
    Save = true,
    Callback = function(val)
        HOption.crazyLine = val
    end
})

GrabTab:AddSection({Name = "Anchor Grab"})

GrabTab:AddToggle({
    Name = "Anchor Grab",
    Default = false,
    Flag = "AnchorGrab",
    Save = true,
    Callback = function(val)
        HOption.anchorGrab = val
    end
})

GrabTab:AddToggle({
    Name = "Compile Parts",
    Default = false,
    Flag = "CompileParts",
    Save = true,
    Callback = function(val)
        HOption.compileParts = val
    end
})

GrabTab:AddButton({
    Name = "Unanchor All",
    Callback = function()
        for _, data in pairs(HFPS.Anchored) do
            if data and data.BodyPosition then
                data.BodyPosition:Destroy()
            end
            if data and data.BodyGyro then
                data.BodyGyro:Destroy()
            end
            if data and data.Model then
                data.Model:SetAttribute("IsAnchored", false)
            end
        end
        HFPS.Anchored = {}
        HList.AnchoredParts = {}
    end
})

GrabTab:AddButton({
    Name = "Disassemble All",
    Callback = function()
        for _, group in pairs(HList.CompiledGroups) do
            for _, data in pairs(group) do
                if data.BodyPosition then
                    data.BodyPosition:Destroy()
                end
                if data.BodyGyro then
                    data.BodyGyro:Destroy()
                end
                if data.Model then
                    data.Model:SetAttribute("Glue", false)
                    data.Model:SetAttribute("GluePrimary", false)
                end
            end
        end
        HList.CompiledGroups = {}
        HFPS.Compiled = {}
    end
})

GrabTab:AddSection({Name = "Perspective Grab"})

GrabTab:AddToggle({
    Name = "Perspective Grab",
    Default = false,
    Flag = "PerspectiveGrab",
    Save = true,
    Callback = function(val)
        HOption.perspectiveGrab = val
    end
})

local perspectiveSpeed = GrabTab:AddSlider({
    Name = "Perspective Speed",
    Min = 50,
    Max = 150,
    Default = 50,
    Increment = 5,
    ValueName = "speed",
    Flag = "PerspectiveSpeed",
    Save = true,
    Callback = function(val)
        HOption.perspectiveSpeed = val
    end
})

-- ============================================
-- AURAS TAB
-- ============================================

AuraTab:AddSection({Name = "Combat Auras"})

AuraTab:AddToggle({
    Name = "Poison Aura",
    Default = false,
    Flag = "PoisonAura",
    Save = true,
    Callback = function(val)
        HOption.poisonAura = val
    end
})

AuraTab:AddToggle({
    Name = "Fling Aura",
    Default = false,
    Flag = "FlingAura",
    Save = true,
    Callback = function(val)
        HOption.flingAura = val
    end
})

local flingStrengthSlider = AuraTab:AddSlider({
    Name = "Fling Strength",
    Min = 400,
    Max = 5000,
    Default = 1000,
    Increment = 100,
    ValueName = "strength",
    Flag = "FlingStrength",
    Save = true,
    Callback = function(val)
        HOption.flingStrength = val
    end
})

AuraTab:AddToggle({
    Name = "Attraction Aura",
    Default = false,
    Flag = "AttractAura",
    Save = true,
    Callback = function(val)
        HOption.attractAura = val
    end
})

AuraTab:AddToggle({
    Name = "Grab Aura",
    Default = false,
    Flag = "GrabAura",
    Save = true,
    Callback = function(val)
        HOption.grabAura = val
    end
})

AuraTab:AddToggle({
    Name = "Tornado Aura",
    Default = false,
    Flag = "TornadoAura",
    Save = true,
    Callback = function(val)
        HOption.tornadoAura = val
    end
})

local tornadoSpeedSlider = AuraTab:AddSlider({
    Name = "Tornado Speed",
    Min = 1,
    Max = 20,
    Default = 5,
    Increment = 1,
    ValueName = "speed",
    Flag = "TornadoSpeed",
    Save = true,
    Callback = function(val)
        HOption.tornadoSpeed = val
    end
})

local tornadoRangeSlider = AuraTab:AddSlider({
    Name = "Tornado Range",
    Min = 10,
    Max = 50,
    Default = 25,
    Increment = 5,
    ValueName = "studs",
    Flag = "TornadoRange",
    Save = true,
    Callback = function(val)
        HOption.tornadoRange = val
    end
})

AuraTab:AddToggle({
    Name = "Blackhole Aura",
    Default = false,
    Flag = "BlackholeAura",
    Save = true,
    Callback = function(val)
        HOption.blackholeAura = val
    end
})

local blackholeStrengthSlider = AuraTab:AddSlider({
    Name = "Blackhole Strength",
    Min = 500,
    Max = 5000,
    Default = 1500,
    Increment = 100,
    ValueName = "strength",
    Flag = "BlackholeStrength",
    Save = true,
    Callback = function(val)
        HOption.blackholeStrength = val
    end
})

-- ============================================
-- BLOBMAN TAB
-- ============================================

BlobTab:AddSection({Name = "Blobman Functions"})

local blobPlayerDropdown = BlobTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.blobTarget = getPlayerFromName(val)
    end,
    Flag = "BlobTarget",
    Save = true
})

BlobTab:AddButton({
    Name = "Get Blobman",
    Callback = function()
        if not HOption.blobTarget then return end
        local root = getRoot()
        if not root then return end
        local blob = spawnToy("CreatureBlobman")
        if blob then
            local seat = blob:FindFirstChild("VehicleSeat")
            if seat then
                seat:Sit(getHumanoid())
                task.wait(0.5)
                local hrp = HOption.blobTarget.Character and HOption.blobTarget.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local leftDetector = blob:FindFirstChild("LeftDetector")
                    local rightDetector = blob:FindFirstChild("RightDetector")
                    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                    if leftDetector and rightDetector and script then
                        local leftWeld = leftDetector:FindFirstChild("LeftWeld")
                        local rightWeld = rightDetector:FindFirstChild("RightWeld")
                        local creatureGrab = script:FindFirstChild("CreatureGrab")
                        if creatureGrab and leftWeld then
                            creatureGrab:FireServer(leftDetector, hrp, leftWeld)
                        end
                        if creatureGrab and rightWeld then
                            creatureGrab:FireServer(rightDetector, hrp, rightWeld)
                        end
                    end
                end
            end
        end
    end
})

BlobTab:AddButton({
    Name = "Kick Player",
    Callback = function()
        if HOption.blobTarget and HOption.blobTarget.Character then
            local hrp = HOption.blobTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local bv = Instance.new("BodyPosition", hrp)
                bv.Position = hrp.Position + Vector3.new(0, 5000, 0)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.D = 1000
                bv.P = 50000
                task.delay(1, function()
                    if bv then bv:Destroy() end
                end)
            end
        end
    end
})

BlobTab:AddButton({
    Name = "Kill Player",
    Callback = function()
        if HOption.blobTarget and HOption.blobTarget.Character then
            local hum = HOption.blobTarget.Character:FindFirstChild("Humanoid")
            if hum then
                hum:ChangeState(Enum.HumanoidStateType.Dead)
            end
        end
    end
})

BlobTab:AddButton({
    Name = "Lock Player",
    Callback = function()
        if HOption.blobTarget and HOption.blobTarget.Character then
            local hrp = HOption.blobTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.Anchored = true
                task.delay(5, function()
                    if hrp then hrp.Anchored = false end
                end)
            end
        end
    end
})

BlobTab:AddButton({
    Name = "Bring Player",
    Callback = function()
        if HOption.blobTarget and HOption.blobTarget.Character then
            local hrp = HOption.blobTarget.Character:FindFirstChild("HumanoidRootPart")
            local root = getRoot()
            if hrp and root then
                grabPart(hrp, true)
                hrp.CFrame = root.CFrame + Vector3.new(0, 5, 0)
                ungrabPart(hrp)
            end
        end
    end
})

BlobTab:AddSection({Name = "Auto Gucci"})

BlobTab:AddToggle({
    Name = "Auto Gucci (Blobman)",
    Default = false,
    Flag = "AutoGucci",
    Save = true,
    Callback = function(val)
        HOption.autoGucci = val
    end
})

BlobTab:AddToggle({
    Name = "Auto Gucci (Tractor)",
    Default = false,
    Flag = "AutoGucciTractor",
    Save = true,
    Callback = function(val)
        HOption.autoGucciTractor = val
    end
})

BlobTab:AddToggle({
    Name = "Auto Gucci (Train)",
    Default = false,
    Flag = "AutoGucciTrain",
    Save = true,
    Callback = function(val)
        HOption.autoGucciTrain = val
    end
})

-- ============================================
-- VISUAL TAB
-- ============================================

VisualTab:AddSection({Name = "ESP"})

local espFillColor = Color3.fromRGB(255, 0, 0)
local espOutlineColor = Color3.fromRGB(0, 0, 0)
local espFillTrans = 0.5
local espOutlineTrans = 0

local espFillPicker = VisualTab:AddColorpicker({
    Name = "Fill Color",
    Default = espFillColor,
    Flag = "EspFillColor",
    Save = true,
    Callback = function(val)
        espFillColor = val
        for _, h in pairs(HFPS.ESP) do
            if h then h.FillColor = val end
        end
    end
})

local espFillSlider = VisualTab:AddSlider({
    Name = "Fill Transparency",
    Min = 0,
    Max = 1,
    Default = 0.5,
    Increment = 0.1,
    ValueName = "trans",
    Flag = "EspFillTrans",
    Save = true,
    Callback = function(val)
        espFillTrans = val
        for _, h in pairs(HFPS.ESP) do
            if h then h.FillTransparency = val end
        end
    end
})

local espOutlinePicker = VisualTab:AddColorpicker({
    Name = "Outline Color",
    Default = espOutlineColor,
    Flag = "EspOutlineColor",
    Save = true,
    Callback = function(val)
        espOutlineColor = val
        for _, h in pairs(HFPS.ESP) do
            if h then h.OutlineColor = val end
        end
    end
})

local espOutlineSlider = VisualTab:AddSlider({
    Name = "Outline Transparency",
    Min = 0,
    Max = 1,
    Default = 0,
    Increment = 0.1,
    ValueName = "trans",
    Flag = "EspOutlineTrans",
    Save = true,
    Callback = function(val)
        espOutlineTrans = val
        for _, h in pairs(HFPS.ESP) do
            if h then h.OutlineTransparency = val end
        end
    end
})

local espToggle = VisualTab:AddToggle({
    Name = "ESP Players",
    Default = false,
    Flag = "ESP",
    Save = true,
    Callback = function(val)
        HOption.esp = val
        if val then
            HFPS.ESP = {}
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hl = Instance.new("Highlight", plr.Character)
                    hl.FillColor = espFillColor
                    hl.OutlineColor = espOutlineColor
                    hl.FillTransparency = espFillTrans
                    hl.OutlineTransparency = espOutlineTrans
                    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                    table.insert(HFPS.ESP, hl)
                end
            end
        else
            for _, h in pairs(HFPS.ESP) do
                if h then h:Destroy() end
            end
            HFPS.ESP = {}
        end
    end
})

VisualTab:AddSection({Name = "Chams"})

local chamsFillColor = Color3.fromRGB(0, 255, 0)
local chamsOutlineColor = Color3.fromRGB(0, 0, 0)
local chamsFillTrans = 0.3
local chamsOutlineTrans = 0

local chamsFillPicker = VisualTab:AddColorpicker({
    Name = "Chams Fill",
    Default = chamsFillColor,
    Flag = "ChamsFillColor",
    Save = true,
    Callback = function(val)
        chamsFillColor = val
        for _, h in pairs(HFPS.Chams) do
            if h then h.FillColor = val end
        end
    end
})

local chamsOutlinePicker = VisualTab:AddColorpicker({
    Name = "Chams Outline",
    Default = chamsOutlineColor,
    Flag = "ChamsOutlineColor",
    Save = true,
    Callback = function(val)
        chamsOutlineColor = val
        for _, h in pairs(HFPS.Chams) do
            if h then h.OutlineColor = val end
        end
    end
})

local chamsToggle = VisualTab:AddToggle({
    Name = "Chams",
    Default = false,
    Flag = "Chams",
    Save = true,
    Callback = function(val)
        HOption.chams = val
        if val then
            HFPS.Chams = {}
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hl = Instance.new("Highlight", plr.Character)
                    hl.FillColor = chamsFillColor
                    hl.OutlineColor = chamsOutlineColor
                    hl.FillTransparency = chamsFillTrans
                    hl.OutlineTransparency = chamsOutlineTrans
                    hl.DepthMode = Enum.HighlightDepthMode.Occluded
                    table.insert(HFPS.Chams, hl)
                end
            end
        else
            for _, h in pairs(HFPS.Chams) do
                if h then h:Destroy() end
            end
            HFPS.Chams = {}
        end
    end
})

VisualTab:AddSection({Name = "Line Colors"})

local lineColors = {}
for i = 1, 20 do
    lineColors[i] = Color3.fromRGB(255, 0, 0)
end

for i = 1, 10 do
    local colorPicker = VisualTab:AddColorpicker({
        Name = "Color " .. i,
        Default = lineColors[i],
        Flag = "LineColor" .. i,
        Save = true,
        Callback = function(val)
            lineColors[i] = val
            local cs = {}
            for j = 1, 20 do
                if j == 1 then
                    cs[j] = ColorSequenceKeypoint.new(0, lineColors[j])
                else
                    cs[j] = ColorSequenceKeypoint.new((j - 1) / 19, lineColors[j])
                end
            end
            UpdateLineColors:FireServer(ColorSequence.new(cs))
        end
    })
end

VisualTab:AddToggle({
    Name = "RGB Line",
    Default = false,
    Flag = "RGBLine",
    Save = true,
    Callback = function(val)
        HOption.rgbLine = val
    end
})

-- ============================================
-- ANTI TAB
-- ============================================

AntiTab:AddSection({Name = "Protections"})

AntiTab:AddToggle({
    Name = "Anti Grab",
    Default = false,
    Flag = "AntiGrab",
    Save = true,
    Callback = function(val)
        HOption.antiGrab = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Ragdoll",
    Default = false,
    Flag = "AntiRagdoll",
    Save = true,
    Callback = function(val)
        HOption.antiRagdoll = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Explosion",
    Default = false,
    Flag = "AntiExplosion",
    Save = true,
    Callback = function(val)
        HOption.antiExplosion = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Void",
    Default = false,
    Flag = "AntiVoid",
    Save = true,
    Callback = function(val)
        HOption.antiVoid = val
        if val then
            Workspace.FallenPartsDestroyHeight = -50000
        else
            Workspace.FallenPartsDestroyHeight = -100
        end
    end
})

AntiTab:AddToggle({
    Name = "Anti Lag",
    Default = false,
    Flag = "AntiLag",
    Save = true,
    Callback = function(val)
        HOption.antiLag = val
        local script = LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove")
        if script then
            script.Disabled = val
        end
    end
})

AntiTab:AddToggle({
    Name = "Anti Fire",
    Default = false,
    Flag = "AntiFire",
    Save = true,
    Callback = function(val)
        HOption.antiFire = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Blobman",
    Default = false,
    Flag = "AntiBlob",
    Save = true,
    Callback = function(val)
        HOption.antiBlob = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Kick",
    Default = false,
    Flag = "AntiKick",
    Save = true,
    Callback = function(val)
        HOption.antiKick = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Fling",
    Default = false,
    Flag = "AntiFling",
    Save = true,
    Callback = function(val)
        HOption.antiFling = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Banana",
    Default = false,
    Flag = "AntiBanana",
    Save = true,
    Callback = function(val)
        HOption.antiBanana = val
    end
})

AntiTab:AddToggle({
    Name = "Anti Dsync",
    Default = true,
    Flag = "AntiDsync",
    Save = true,
    Callback = function(val)
        HOption.antiDsync = val
    end
})

AntiTab:AddSection({Name = "Anti Input Lag"})

AntiTab:AddToggle({
    Name = "Anti Input Lag",
    Default = false,
    Flag = "AntiInputLag",
    Save = true,
    Callback = function(val)
        HOption.antiInputLag = val
    end
})

local antiInputToy = AntiTab:AddDropdown({
    Name = "Anti Input Toy",
    Default = "FoodDonut",
    Options = {"FoodDonut", "FoodApple", "FoodBanana", "FoodOrange"},
    Flag = "AntiInputToy",
    Save = true,
    Callback = function(val)
        HOption.antiInputToy = val
    end
})

-- ============================================
-- KEYBINDS TAB
-- ============================================

KeybindTab:AddSection({Name = "Teleport"})

KeybindTab:AddBind({
    Name = "Teleport to Mouse",
    Default = Enum.KeyCode.Z,
    Hold = false,
    Flag = "TeleportToMouseBind",
    Save = true,
    Callback = function()
        local root = getRoot()
        if not root then return end
        local mouse = LocalPlayer:GetMouse()
        local hit = mouse.Hit
        if hit then
            root.CFrame = CFrame.new(hit.Position) + Vector3.new(0, 5, 0)
            root.AssemblyLinearVelocity = Vector3.zero
        end
    end
})

KeybindTab:AddBind({
    Name = "Click TP (Hold)",
    Default = Enum.KeyCode.LeftAlt,
    Hold = true,
    Flag = "ClickTPBind",
    Save = true,
    Callback = function(hold)
        if hold then
            local root = getRoot()
            if not root then return end
            local mouse = LocalPlayer:GetMouse()
            local hit = mouse.Hit
            if hit then
                root.CFrame = CFrame.new(hit.Position) + Vector3.new(0, 5, 0)
                root.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end
})

KeybindTab:AddSection({Name = "Grab"})

KeybindTab:AddBind({
    Name = "Invisible Grab",
    Default = Enum.KeyCode.X,
    Hold = false,
    Flag = "InvisGrabBind",
    Save = true,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target then
            grabPart(target, true)
        end
    end
})

KeybindTab:AddBind({
    Name = "Safe Grab",
    Default = Enum.KeyCode.C,
    Hold = false,
    Flag = "SafeGrabBind",
    Save = true,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target then
            local root = getRoot()
            if root then
                for i = 1, 10 do
                    root.CFrame = CFrame.new(target.Position) + Vector3.new(0, 5, 0)
                    SetNetworkOwner:FireServer(target, target.CFrame)
                    task.wait(0.05)
                end
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Ungrab All",
    Default = Enum.KeyCode.V,
    Hold = false,
    Flag = "UngrabAllBind",
    Save = true,
    Callback = function()
        local grabParts = Workspace:FindFirstChild("GrabParts")
        if grabParts then
            local gp = grabParts:FindFirstChild("GrabPart")
            if gp and gp:FindFirstChild("WeldConstraint") then
                DestroyGrabLine:FireServer(gp.WeldConstraint.Part1)
            end
        end
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local head = plr.Character:FindFirstChild("Head")
                if head then
                    DestroyGrabLine:FireServer(head)
                end
            end
        end
    end
})

KeybindTab:AddSection({Name = "Combat"})

KeybindTab:AddBind({
    Name = "Kill Nearest",
    Default = Enum.KeyCode.T,
    Hold = false,
    Flag = "KillNearestBind",
    Save = true,
    Callback = function()
        local nearest = getNearestPlayer()
        if nearest and nearest.Parent then
            local hum = nearest.Parent:FindFirstChild("Humanoid")
            if hum then
                hum:ChangeState(Enum.HumanoidStateType.Dead)
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Bring Nearest",
    Default = Enum.KeyCode.B,
    Hold = false,
    Flag = "BringNearestBind",
    Save = true,
    Callback = function()
        local nearest = getNearestPlayer()
        if nearest and nearest.Parent then
            local hrp = nearest.Parent:FindFirstChild("HumanoidRootPart")
            local root = getRoot()
            if hrp and root then
                grabPart(hrp, true)
                hrp.CFrame = root.CFrame + Vector3.new(0, 5, 0)
                ungrabPart(hrp)
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Fling Nearest",
    Default = Enum.KeyCode.F,
    Hold = false,
    Flag = "FlingNearestBind",
    Save = true,
    Callback = function()
        local nearest = getNearestPlayer()
        if nearest and nearest.Parent then
            local hrp = nearest.Parent:FindFirstChild("HumanoidRootPart")
            if hrp then
                local bv = Instance.new("BodyVelocity", hrp)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Vector3.new(0, 10000, 0)
                task.delay(1, function()
                    if bv then bv:Destroy() end
                end)
            end
        end
    end
})

KeybindTab:AddSection({Name = "Animations"})

KeybindTab:AddBind({
    Name = "Jerk Off",
    Default = Enum.KeyCode.J,
    Hold = true,
    Flag = "JerkOffBind",
    Save = true,
    Callback = function(hold)
        if LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then
                local animator = hum:FindFirstChild("Animator")
                if animator then
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://168268306"
                    local track = animator:LoadAnimation(anim)
                    if hold then
                        track:Play()
                        while hold do
                            track.TimePosition = 0.3
                            task.wait(0.1)
                        end
                        track:Stop()
                    end
                end
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Typing Animation",
    Default = Enum.KeyCode.Y,
    Hold = true,
    Flag = "TypingAnimBind",
    Save = true,
    Callback = function(hold)
        if LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then
                local animator = hum:FindFirstChild("Animator")
                if animator then
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://18353618958"
                    local track = animator:LoadAnimation(anim)
                    if hold then
                        track:Play()
                    else
                        track:Stop()
                    end
                end
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Crouch Animation",
    Default = Enum.KeyCode.LeftControl,
    Hold = true,
    Flag = "CrouchAnimBind",
    Save = true,
    Callback = function(hold)
        if LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then
                local animator = hum:FindFirstChild("Animator")
                if animator then
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://6980229055"
                    local track = animator:LoadAnimation(anim)
                    if hold then
                        track:Play()
                    else
                        track:Stop()
                    end
                end
            end
        end
    end
})

KeybindTab:AddBind({
    Name = "Throwed Animation",
    Default = Enum.KeyCode.R,
    Hold = false,
    Flag = "ThrowedAnimBind",
    Save = true,
    Callback = function()
        if LocalPlayer.Character then
            local hum = getHumanoid()
            if hum then
                local animator = hum:FindFirstChild("Animator")
                if animator then
                    local anim = Instance.new("Animation")
                    anim.AnimationId = "rbxassetid://7047322890"
                    local track = animator:LoadAnimation(anim)
                    track:Play()
                end
            end
        end
    end
})

KeybindTab:AddSection({Name = "Anchor"})

KeybindTab:AddBind({
    Name = "Anchor Object",
    Default = Enum.KeyCode.K,
    Hold = false,
    Flag = "AnchorObjectBind",
    Save = true,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target then
            anchorPart(target)
        end
    end
})

KeybindTab:AddBind({
    Name = "Unanchor Object",
    Default = Enum.KeyCode.U,
    Hold = false,
    Flag = "UnanchorObjectBind",
    Save = true,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target and target.Parent then
            unanchorPart(target.Parent)
        end
    end
})

KeybindTab:AddBind({
    Name = "Compile Group",
    Default = Enum.KeyCode.L,
    Hold = false,
    Flag = "CompileGroupBind",
    Save = true,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target then
            compileGroup(target)
        end
    end
})

KeybindTab:AddSection({Name = "Control"})

KeybindTab:AddBind({
    Name = "Control Creature",
    Default = Enum.KeyCode.C,
    Hold = false,
    Flag = "ControlCreatureBind",
    Save = true,
    Callback = function()
        if HOption.controlCreature then
            HOption.controlCreature = false
            if HOption.controlCreatureConn then
                HOption.controlCreatureConn:Disconnect()
            end
            return
        end
        HOption.controlCreature = true
        local mouse = LocalPlayer:GetMouse()
        local target = mouse.Target
        if target then
            local char = getCharacterFromPart(target)
            if char and char:FindFirstChild("Humanoid") then
                local origChar = LocalPlayer.Character
                local origHum = getHumanoid()
                local origRoot = getRoot()
                HOption.controlCreatureConn = RunService.Heartbeat:Connect(function()
                    if not HOption.controlCreature or not char.Parent then
                        HOption.controlCreature = false
                        HOption.controlCreatureConn:Disconnect()
                        if origChar then
                            LocalPlayer.Character = origChar
                            Camera.CameraSubject = origHum
                        end
                        return
                    end
                    LocalPlayer.Character = char
                    Camera.CameraSubject = char:FindFirstChild("Humanoid")
                    if origRoot then
                        origRoot.CFrame = char:FindFirstChild("HumanoidRootPart").CFrame
                    end
                end)
            end
        end
    end
})

-- ============================================
-- LOOP TAB
-- ============================================

LoopTab:AddSection({Name = "Player Loops"})

local loopPlayersList = {}

local loopPlayerDropdown = LoopTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        local plr = getPlayerFromName(val)
        if plr and not table.find(loopPlayersList, plr) then
            table.insert(loopPlayersList, plr)
            addToLoopList(plr.Name)
        end
    end
})

LoopTab:AddButton({
    Name = "Clear Loop List",
    Callback = function()
        loopPlayersList = {}
        HList.LoopList = {}
    end
})

local loopTypeDropdown = LoopTab:AddDropdown({
    Name = "Loop Type",
    Default = "Kill",
    Options = {"Kill", "Bring", "Fling", "Ragdoll", "Poison", "Fire", "Banana"},
    Flag = "LoopType",
    Save = true,
    Callback = function(val)
        HOption.loopType = val
    end
})

local loopToggle = LoopTab:AddToggle({
    Name = "Enable Loop",
    Default = false,
    Flag = "LoopEnabled",
    Save = true,
    Callback = function(val)
        HOption.loopEnabled = val
    end
})

LoopTab:AddSection({Name = "Kick Loop"})

local kickLoopTarget = LoopTab:AddDropdown({
    Name = "Kick Target",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.kickTarget = getPlayerFromName(val)
    end,
    Flag = "KickTarget",
    Save = true
})

local kickLoopToggle = LoopTab:AddToggle({
    Name = "Loop Kick",
    Default = false,
    Flag = "KickLoop",
    Save = true,
    Callback = function(val)
        HOption.kickLoop = val
    end
})

LoopTab:AddSection({Name = "Bring Loop"})

local bringLoopTarget = LoopTab:AddDropdown({
    Name = "Bring Target",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.bringLoopTarget = getPlayerFromName(val)
    end,
    Flag = "BringLoopTarget",
    Save = true
})

local bringLoopToggle = LoopTab:AddToggle({
    Name = "Loop Bring",
    Default = false,
    Flag = "BringLoop",
    Save = true,
    Callback = function(val)
        HOption.bringLoop = val
    end
})

LoopTab:AddSection({Name = "Server Lag"})

local lagToggle = LoopTab:AddToggle({
    Name = "Lag Server",
    Default = false,
    Flag = "LagServer",
    Save = true,
    Callback = function(val)
        HOption.lagServer = val
    end
})

local lagIntensitySlider = LoopTab:AddSlider({
    Name = "Lag Intensity",
    Min = 10,
    Max = 500,
    Default = 100,
    Increment = 10,
    ValueName = "intensity",
    Flag = "LagIntensity",
    Save = true,
    Callback = function(val)
        HOption.lagIntensity = val
    end
})

LoopTab:AddSection({Name = "Packet Send"})

local packetToggle = LoopTab:AddToggle({
    Name = "Packet Send",
    Default = false,
    Flag = "PacketSend",
    Save = true,
    Callback = function(val)
        HOption.packetSend = val
    end
})

local packetValue = LoopTab:AddTextbox({
    Name = "Packet Strings",
    Default = "1000",
    TextDisappear = false,
    Callback = function(val)
        HOption.packetValue = tonumber(val) or 1000
    end
})

-- ============================================
-- EXPLOSION TAB
-- ============================================

ExplosionTab:AddSection({Name = "Explosions"})

local explosionTypeDropdown = ExplosionTab:AddDropdown({
    Name = "Explosion Type",
    Default = "BombMissile",
    Options = {"BombMissile", "FireworkMissile", "BombBalloon", "BombDarkMatter", "BallSnowball"},
    Flag = "ExplosionType",
    Save = true,
    Callback = function(val)
        HOption.explosionType = val
    end
})

local explosionTargetDropdown = ExplosionTab:AddDropdown({
    Name = "Target",
    Default = "Mouse",
    Options = {"Mouse", "Nearest Player", "All Players"},
    Flag = "ExplosionTarget",
    Save = true,
    Callback = function(val)
        HOption.explosionTarget = val
    end
})

local explosionDelaySlider = ExplosionTab:AddSlider({
    Name = "Explosion Delay",
    Min = 0,
    Max = 1,
    Default = 0,
    Increment = 0.05,
    ValueName = "sec",
    Flag = "ExplosionDelay",
    Save = true,
    Callback = function(val)
        HOption.explosionDelay = val
    end
})

local explosionAmmountSlider = ExplosionTab:AddSlider({
    Name = "Explosion Ammount",
    Min = 1,
    Max = 20,
    Default = 5,
    Increment = 1,
    ValueName = "amount",
    Flag = "ExplosionAmmount",
    Save = true,
    Callback = function(val)
        HOption.explosionAmmount = val
    end
})

ExplosionTab:AddButton({
    Name = "Explode Once",
    Callback = function()
        local root = getRoot()
        if not root then return end
        local pos = nil
        if HOption.explosionTarget == "Mouse" then
            local mousePos = UserInputService:GetMouseLocation()
            local ray = Camera:ScreenPointToRay(mousePos.X, mousePos.Y)
            local result = Workspace:Raycast(ray.Origin, ray.Direction * 1000)
            if result then
                pos = result.Position
            else
                pos = ray.Origin + ray.Direction * 1000
            end
        elseif HOption.explosionTarget == "Nearest Player" then
            local nearest = getNearestPlayer()
            if nearest then
                pos = nearest.Position
            end
        end
        if pos then
            local toy = spawnToy(HOption.explosionType)
            if toy then
                local hitbox = getHitboxPart(toy, HOption.explosionType)
                if hitbox then
                    explodeBomb(hitbox, hitbox, pos)
                end
                deleteToy(toy)
            end
        end
    end
})

ExplosionTab:AddToggle({
    Name = "Auto Explode",
    Default = false,
    Flag = "AutoExplode",
    Save = true,
    Callback = function(val)
        HOption.autoExplode = val
    end
})

-- ============================================
-- TELEPORT TAB
-- ============================================

TeleportTab:AddSection({Name = "Location Teleports"})

local locations = {
    ["Spawn"] = CFrame.new(0, 5, 0),
    ["Green House"] = CFrame.new(-520.812, -7.374, 69.908),
    ["Red House"] = CFrame.new(-453.869, -7.374, -140.031),
    ["Blue House"] = CFrame.new(479.196, 83.313, -274.814),
    ["Chinese House"] = CFrame.new(499.552, 123.313, -92.731),
    ["Purple House"] = CFrame.new(256.983, -7.374, 420.039),
    ["Farm"] = CFrame.new(-148.779, 59.755, -240.749),
    ["Ice Mountain"] = CFrame.new(-415.797, 230.622, 485.994),
    ["Secret Cave"] = CFrame.new(123.519, -7.374, 630.845),
    ["Sky Island"] = CFrame.new(57.608, 346.170, 348.660),
    ["Broken Bridge"] = CFrame.new(451.626, 163.315, 207.368),
    ["Void"] = CFrame.new(0, -49980, 0),
    ["Sky"] = CFrame.new(10000, 10000, 10000)
}

local locationDropdown = TeleportTab:AddDropdown({
    Name = "Select Location",
    Default = "Spawn",
    Options = {"Spawn", "Green House", "Red House", "Blue House", "Chinese House", "Purple House", "Farm", "Ice Mountain", "Secret Cave", "Sky Island", "Broken Bridge", "Void", "Sky"},
    Flag = "TeleportLocation",
    Save = true,
    Callback = function(val)
        HOption.teleportLocation = val
    end
})

TeleportTab:AddButton({
    Name = "Teleport",
    Callback = function()
        local root = getRoot()
        if root and HOption.teleportLocation then
            local cf = locations[HOption.teleportLocation]
            if cf then
                root.CFrame = cf
                root.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end
})

TeleportTab:AddSection({Name = "Player Teleports"})

local teleportPlayerDropdown = TeleportTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.teleportPlayer = getPlayerFromName(val)
    end,
    Flag = "TeleportPlayer",
    Save = true
})

TeleportTab:AddButton({
    Name = "Teleport to Player",
    Callback = function()
        if HOption.teleportPlayer and HOption.teleportPlayer.Character then
            local root = getRoot()
            local targetRoot = HOption.teleportPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root and targetRoot then
                root.CFrame = targetRoot.CFrame + Vector3.new(0, 5, 0)
                root.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end
})

TeleportTab:AddButton({
    Name = "Bring Player to You",
    Callback = function()
        if HOption.teleportPlayer and HOption.teleportPlayer.Character then
            local root = getRoot()
            local targetRoot = HOption.teleportPlayer.Character:FindFirstChild("HumanoidRootPart")
            if root and targetRoot then
                grabPart(targetRoot, true)
                targetRoot.CFrame = root.CFrame + Vector3.new(0, 5, 0)
                ungrabPart(targetRoot)
            end
        end
    end
})

TeleportTab:AddSection({Name = "Part Teleports"})

TeleportTab:AddButton({
    Name = "Select Part",
    Callback = function()
        HOption.selectingPart = true
        OrionLib:MakeNotification({Name = "Selecting", Content = "Click on a part to teleport to it", Time = 3})
        local conn
        conn = UserInputService.InputBegan:Connect(function(input)
            if HOption.selectingPart and input.UserInputType == Enum.UserInputType.MouseButton1 then
                local mouse = LocalPlayer:GetMouse()
                local target = mouse.Target
                if target then
                    HOption.selectedPart = target
                    HOption.selectingPart = false
                    conn:Disconnect()
                    OrionLib:MakeNotification({Name = "Selected", Content = "Part selected: " .. target.Name, Time = 2})
                end
            end
        end)
    end
})

TeleportTab:AddButton({
    Name = "Teleport to Selected Part",
    Callback = function()
        if HOption.selectedPart then
            local root = getRoot()
            if root then
                root.CFrame = HOption.selectedPart.CFrame + Vector3.new(0, 3, 0)
                root.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end
})

TeleportTab:AddButton({
    Name = "Copy Part Path",
    Callback = function()
        if HOption.selectedPart then
            local path = ""
            local current = HOption.selectedPart
            while current do
                if path == "" then
                    path = current.Name
                else
                    path = current.Name .. "." .. path
                end
                current = current.Parent
            end
            setclipboard(path)
            OrionLib:MakeNotification({Name = "Copied", Content = "Part path copied to clipboard", Time = 2})
        end
    end
})

-- ============================================
-- MISC TAB
-- ============================================

MiscTab:AddSection({Name = "FOV"})

local fovToggle = MiscTab:AddToggle({
    Name = "Custom FOV",
    Default = false,
    Flag = "CustomFOV",
    Save = true,
    Callback = function(val)
        HOption.customFOV = val
        if not val then
            Camera.FieldOfView = 70
        end
    end
})

local fovSlider = MiscTab:AddSlider({
    Name = "FOV Value",
    Min = 1,
    Max = 120,
    Default = 70,
    Increment = 1,
    ValueName = "fov",
    Flag = "FOVValue",
    Save = true,
    Callback = function(val)
        HOption.fovValue = val
        if HOption.customFOV then
            Camera.FieldOfView = val
        end
    end
})

MiscTab:AddSection({Name = "Time"})

local timeSlider = MiscTab:AddSlider({
    Name = "Time of Day",
    Min = 0,
    Max = 24,
    Default = 14,
    Increment = 0.1,
    ValueName = "hours",
    Flag = "TimeOfDay",
    Save = true,
    Callback = function(val)
        Lighting.ClockTime = val
    end
})

MiscTab:AddToggle({
    Name = "Sync Time",
    Default = false,
    Flag = "SyncTime",
    Save = true,
    Callback = function(val)
        HOption.syncTime = val
    end
})

MiscTab:AddSection({Name = "Graphics"})

local graphicsDropdown = MiscTab:AddDropdown({
    Name = "Graphics Quality",
    Default = "Medium",
    Options = {"Low", "Medium", "High", "Ultra"},
    Flag = "GraphicsQuality",
    Save = true,
    Callback = function(val)
        HOption.graphics = val
        if val == "Low" then
            settings().Rendering.QualityLevel = 1
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 100000
        elseif val == "Medium" then
            settings().Rendering.QualityLevel = 3
            Lighting.GlobalShadows = true
        elseif val == "High" then
            settings().Rendering.QualityLevel = 6
            Lighting.GlobalShadows = true
        elseif val == "Ultra" then
            settings().Rendering.QualityLevel = 10
            Lighting.GlobalShadows = true
        end
    end
})

MiscTab:AddToggle({
    Name = "Full Bright",
    Default = false,
    Flag = "FullBright",
    Save = true,
    Callback = function(val)
        HOption.fullBright = val
        if val then
            Lighting.Brightness = 2
            Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
            Lighting.Ambient = Color3.fromRGB(128, 128, 128)
        else
            Lighting.Brightness = 1
            Lighting.OutdoorAmbient = Color3.fromRGB(127, 127, 127)
            Lighting.Ambient = Color3.fromRGB(127, 127, 127)
        end
    end
})

MiscTab:AddSection({Name = "Sounds"})

MiscTab:AddToggle({
    Name = "Mute Boombox",
    Default = false,
    Flag = "MuteBoombox",
    Save = true,
    Callback = function(val)
        HOption.muteBoombox = val
    end
})

MiscTab:AddToggle({
    Name = "Mute Jukebox",
    Default = false,
    Flag = "MuteJukebox",
    Save = true,
    Callback = function(val)
        HOption.muteJukebox = val
    end
})

MiscTab:AddSection({Name = "Camera"})

MiscTab:AddToggle({
    Name = "First Person",
    Default = false,
    Flag = "FirstPerson",
    Save = true,
    Callback = function(val)
        if val then
            LocalPlayer.CameraMode = Enum.CameraMode.LockFirstPerson
        else
            LocalPlayer.CameraMode = Enum.CameraMode.Classic
        end
    end
})

MiscTab:AddToggle({
    Name = "Shift Lock",
    Default = false,
    Flag = "ShiftLock",
    Save = true,
    Callback = function(val)
        LocalPlayer.DevEnableMouseLock = val
    end
})

MiscTab:AddSection({Name = "Break Loops"})

MiscTab:AddButton({
    Name = "Break All Loops",
    Callback = function()
        HOption.loopEnabled = false
        HOption.kickLoop = false
        HOption.bringLoop = false
        HOption.autoExplode = false
        HOption.lagServer = false
        HOption.packetSend = false
        if loopToggle then loopToggle:Set(false) end
        if kickLoopToggle then kickLoopToggle:Set(false) end
        if bringLoopToggle then bringLoopToggle:Set(false) end
        OrionLib:MakeNotification({Name = "Loops Broken", Content = "All loops have been stopped", Time = 2})
    end
})

-- ============================================
-- CONFIG TAB
-- ============================================

ConfigTab:AddSection({Name = "UI Settings"})

ConfigTab:AddTextbox({
    Name = "Window Name",
    Default = "NyPlus Hub",
    TextDisappear = false,
    Callback = function(val)
        if val and val ~= "" then
            Window:SetName({{val, "#FFFFFF"}})
        end
    end
})

ConfigTab:AddTextbox({
    Name = "Window Icon",
    Default = "rbxassetid://114143041236784",
    TextDisappear = false,
    Callback = function(val)
        if val and val ~= "" then
            Window:ChangeIcon(val)
        end
    end
})

ConfigTab:AddSection({Name = "Discord"})

ConfigTab:AddButton({
    Name = "Copy Discord Link",
    Callback = function()
        setclipboard("https://discord.gg/nyplushub")
        OrionLib:MakeNotification({Name = "Copied", Content = "Discord link copied to clipboard", Time = 2})
    end
})

ConfigTab:AddSection({Name = "Credits"})

ConfigTab:AddParagraph("NyPlus Hub", "Made by NyPlus Team\nVersion: 2.0.0\n\nThanks to all contributors!", "Center")

-- ============================================
-- WHITELIST TAB
-- ============================================

WhitelistTab:AddSection({Name = "Whitelist Management"})

local whitelistDropdown = WhitelistTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.whitelistPlayer = getPlayerFromName(val)
    end
})

WhitelistTab:AddButton({
    Name = "Add to Whitelist",
    Callback = function()
        if HOption.whitelistPlayer then
            addToWhitelist(HOption.whitelistPlayer.Name)
            OrionLib:MakeNotification({Name = "Added", Content = HOption.whitelistPlayer.Name .. " added to whitelist", Time = 2})
        end
    end
})

WhitelistTab:AddButton({
    Name = "Remove from Whitelist",
    Callback = function()
        if HOption.whitelistPlayer then
            removeFromWhitelist(HOption.whitelistPlayer.Name)
            OrionLib:MakeNotification({Name = "Removed", Content = HOption.whitelistPlayer.Name .. " removed from whitelist", Time = 2})
        end
    end
})

WhitelistTab:AddButton({
    Name = "Clear Whitelist",
    Callback = function()
        HList.Whitelist = {}
        OrionLib:MakeNotification({Name = "Cleared", Content = "Whitelist cleared", Time = 2})
    end
})

WhitelistTab:AddSection({Name = "Kill List"})

local killListDropdown = WhitelistTab:AddDropdown({
    Name = "Select Player",
    Default = "",
    Options = getAllPlayers(),
    Callback = function(val)
        HOption.killListPlayer = getPlayerFromName(val)
    end
})

WhitelistTab:AddButton({
    Name = "Add to Kill List",
    Callback = function()
        if HOption.killListPlayer then
            addToKillList(HOption.killListPlayer.Name)
            OrionLib:MakeNotification({Name = "Added", Content = HOption.killListPlayer.Name .. " added to kill list", Time = 2})
        end
    end
})

WhitelistTab:AddButton({
    Name = "Remove from Kill List",
    Callback = function()
        if HOption.killListPlayer then
            removeFromKillList(HOption.killListPlayer.Name)
            OrionLib:MakeNotification({Name = "Removed", Content = HOption.killListPlayer.Name .. " removed from kill list", Time = 2})
        end
    end
})

WhitelistTab:AddButton({
    Name = "Clear Kill List",
    Callback = function()
        HList.KillList = {}
        OrionLib:MakeNotification({Name = "Cleared", Content = "Kill list cleared", Time = 2})
    end
})

-- ============================================
-- LOOP FUNÇÕES AUXILIARES
-- ============================================

local function anchorPart(part)
    if not part or not part.Parent then return end
    local model = part.Parent
    if model:IsA("Model") and not model:GetAttribute("IsAnchored") then
        local bp = Instance.new("BodyPosition", part)
        local bg = Instance.new("BodyGyro", part)
        bp.Name = "AnchorBP"
        bg.Name = "AnchorBG"
        bp.P = 40000
        bp.D = 950
        bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bp.Position = part.Position
        bg.P = 40000
        bg.D = 950
        bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bg.CFrame = part.CFrame
        model:SetAttribute("IsAnchored", true)
        table.insert(HFPS.Anchored, {Model = model, BodyPosition = bp, BodyGyro = bg, Part = part})
        table.insert(HList.AnchoredParts, model)
    end
end

local function unanchorPart(model)
    for i, data in pairs(HFPS.Anchored) do
        if data.Model == model then
            if data.BodyPosition then data.BodyPosition:Destroy() end
            if data.BodyGyro then data.BodyGyro:Destroy() end
            table.remove(HFPS.Anchored, i)
            break
        end
    end
    for i, m in pairs(HList.AnchoredParts) do
        if m == model then
            table.remove(HList.AnchoredParts, i)
            break
        end
    end
    model:SetAttribute("IsAnchored", false)
end

local function compileGroup(primaryPart)
    local group = {}
    local primaryModel = primaryPart.Parent
    local primaryPartInst = primaryPart
    for _, data in pairs(HFPS.Anchored) do
        if data.Model ~= primaryModel and data.Model:GetAttribute("IsAnchored") then
            local offset = primaryPartInst.CFrame:ToObjectSpace(data.Part.CFrame)
            table.insert(group, {
                Model = data.Model,
                Part = data.Part,
                Offset = offset,
                BodyPosition = data.BodyPosition,
                BodyGyro = data.BodyGyro
            })
            data.Model:SetAttribute("Glue", true)
        end
    end
    primaryModel:SetAttribute("GluePrimary", true)
    table.insert(HList.CompiledGroups, {Primary = primaryModel, Group = group})
    table.insert(HFPS.Compiled, {Primary = primaryModel, Group = group})
    for _, data in pairs(group) do
        if data.BodyPosition and data.BodyGyro then
            data.BodyPosition.P = 40000
            data.BodyPosition.D = 200
            data.BodyGyro.P = 40000
            data.BodyGyro.D = 200
        end
    end
end

local function updateCompiledGroups()
    for _, group in pairs(HList.CompiledGroups) do
        if group.Primary and group.Primary.Parent then
            local primaryPart = group.Primary:FindFirstChild("HumanoidRootPart") or group.Primary:FindFirstChild("Head") or group.Primary:FindFirstChildOfClass("BasePart")
            if primaryPart then
                for _, data in pairs(group.Group) do
                    if data.BodyPosition and data.BodyGyro and data.Offset then
                        data.BodyPosition.Position = (primaryPart.CFrame * data.Offset).Position
                        data.BodyGyro.CFrame = primaryPart.CFrame * data.Offset
                    end
                end
            end
        end
    end
end

local function autoGucciBlobman()
    if not HOption.autoGucci then return end
    local root = getRoot()
    if not root then return end
    local blob = SpawnedInToys:FindFirstChild("CreatureBlobman")
    if not blob then
        blob = spawnToy("CreatureBlobman")
        if not blob then return end
    end
    local seat = blob:FindFirstChild("VehicleSeat")
    if seat then
        seat:Sit(getHumanoid())
        task.wait(0.5)
        local rootPos = root.Position
        local bv = Instance.new("BodyVelocity", root)
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, 1000, 0)
        task.delay(0.5, function()
            if bv then bv:Destroy() end
            root.CFrame = CFrame.new(rootPos)
        end)
    end
end

local function autoGucciTractor()
    if not HOption.autoGucciTractor then return end
    local tractor = SpawnedInToys:FindFirstChild("TractorOrange")
    if not tractor then
        tractor = spawnToy("TractorOrange")
        if not tractor then return end
    end
    local seat = tractor:FindFirstChildOfClass("VehicleSeat")
    if seat then
        seat:Sit(getHumanoid())
        task.wait(0.5)
        local root = getRoot()
        if root then
            local bv = Instance.new("BodyVelocity", root)
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.new(0, 10000, 0)
        end
    end
end

local function autoGucciTrain()
    if not HOption.autoGucciTrain then return end
    local train = Workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("Train")
    if train then
        local seat = nil
        for _, v in pairs(train:GetDescendants()) do
            if v:IsA("Seat") or v:IsA("VehicleSeat") then
                seat = v
                break
            end
        end
        if seat then
            seat:Sit(getHumanoid())
        end
    end
end

-- ============================================
-- RUNSERVICE LOOPS
-- ============================================

RunService.RenderStepped:Connect(function()
    if HOption.noclip and LocalPlayer.Character then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
            if v:IsA("BasePart") and v.CanCollide then
                v.CanCollide = false
            end
        end
    end
    if HOption.infiniteZoom then
        if Workspace:FindFirstChild("GrabParts") then
            local grabParts = Workspace.GrabParts
            local dragPart = grabParts:FindFirstChild("DragPart")
            if dragPart then
                local dist = (dragPart.Position - Camera.CFrame.Position).Magnitude
                if UserInputService:IsKeyDown(Enum.KeyCode.Q) then
                    dist = dist - 3
                elseif UserInputService:IsKeyDown(Enum.KeyCode.E) then
                    dist = dist + 3
                end
                ExtendGrabLine:FireServer(math.clamp(dist, 5, 500))
            end
        end
    end
end)

RunService.Heartbeat:Connect(function()
    if HOption.walkSpeed and LocalPlayer.Character then
        local hum = getHumanoid()
        if hum then
            hum.WalkSpeed = HOption.walkSpeedVal
        end
    end
    if HOption.gravity then
        Workspace.Gravity = HOption.gravityVal
    end
    if HOption.infJump then
        local hum = getHumanoid()
        if hum and hum:GetState() == Enum.HumanoidStateType.Freefall then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
    if HOption.godMode and LocalPlayer.Character then
        local hum = getHumanoid()
        if hum then
            hum.MaxHealth = math.huge
            hum.Health = math.huge
            hum.BreakJointsOnDeath = false
        end
    end
    if HOption.autoSpin then
        local slots = Workspace.Slots
        if slots then
            local allNeon = true
            for _, slot in pairs(slots:GetChildren()) do
                local light = slot:FindFirstChild("SlotHandle")
                if light then
                    local ball = light:FindFirstChild("LightBall")
                    if ball and ball.Material ~= Enum.Material.Neon then
                        allNeon = false
                        break
                    end
                end
            end
            if allNeon then
                local handle = nil
                for _, slot in pairs(slots:GetChildren()) do
                    local slotHandle = slot:FindFirstChild("SlotHandle")
                    if slotHandle then
                        handle = slotHandle:FindFirstChild("Handle")
                        if handle then
                            break
                        end
                    end
                end
                if handle then
                    grabPart(handle, false)
                end
            end
        end
    end
    if HOption.autoSaveTime then
        local inPlot = false
        for _, plot in pairs(Workspace.Plots:GetChildren()) do
            local sign = plot:FindFirstChild("PlotSign")
            if sign then
                local owners = sign:FindFirstChild("ThisPlotsOwners")
                if owners then
                    for _, owner in pairs(owners:GetChildren()) do
                        if owner.Value == LocalPlayer.Name then
                            local timeLeft = owner:FindFirstChild("TimeRemainingNum")
                            if timeLeft and timeLeft.Value < 20 then
                                local plotItem = Workspace.PlotItems:FindFirstChild(plot.Name)
                                if plotItem then
                                    local area = plotItem:FindFirstChild("PlotArea")
                                    if area then
                                        local root = getRoot()
                                        if root then
                                            root.CFrame = area.CFrame
                                            task.wait(1)
                                        end
                                    end
                                end
                            end
                            inPlot = true
                            break
                        end
                    end
                end
            end
            if inPlot then break end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    if HOption.infJump and LocalPlayer.Character then
        local hum = getHumanoid()
        if hum then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

-- ============================================
-- EVENTOS DE GRAB
-- ============================================

Workspace.ChildAdded:Connect(function(child)
    if HOption.killGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part and part.Parent and part.Parent:FindFirstChild("Humanoid") then
                task.spawn(function()
                    part.Parent.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                end)
            end
        end
    end
    if HOption.noclipGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part and part.Parent then
                for _, v in pairs(part.Parent:GetDescendants()) do
                    if v:IsA("BasePart") then
                        v.CanCollide = false
                    end
                end
                part.AncestryChanged:Connect(function()
                    for _, v in pairs(part.Parent:GetDescendants()) do
                        if v:IsA("BasePart") then
                            v.CanCollide = true
                        end
                    end
                end)
            end
        end
    end
    if HOption.invisGrab and child.Name == "GrabParts" then
        local beam = child:FindFirstChild("BeamPart")
        local drag = child:FindFirstChild("DragPart")
        local grab = child:FindFirstChild("GrabPart")
        if beam then beam:Destroy() end
        if drag then drag.Transparency = 1 end
        if grab then grab.Transparency = 1 end
        CreateGrabLine:FireServer()
        LocalPlayer.PlayerScripts.CharacterAndBeamMove.GrabNotifyEvent:Fire(false)
    end
    if HOption.superStrength and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                local bv = Instance.new("BodyVelocity", part)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Vector3.zero
                child:GetPropertyChangedSignal("Parent"):Connect(function()
                    if not child.Parent then
                        if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                            bv.Velocity = Camera.CFrame.LookVector * HOption.strength
                        end
                        task.delay(2, function()
                            if bv then bv:Destroy() end
                        end)
                    end
                end)
            end
        end
    end
    if HOption.masslessGrab and child.Name == "GrabParts" then
        local dragPart = child:FindFirstChild("DragPart")
        if dragPart then
            local ao = dragPart:FindFirstChild("AlignOrientation")
            local ap = dragPart:FindFirstChild("AlignPosition")
            if ao then
                ao.MaxTorque = math.huge
                ao.Responsiveness = 200
            end
            if ap then
                ap.MaxForce = math.huge
                ap.Responsiveness = 200
            end
        end
    end
    if HOption.ragdollGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part and part.Parent and part.Parent:FindFirstChild("Humanoid") then
                RagdollRemote:FireServer(part, 0)
            end
        end
    end
    if HOption.poisonGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                task.spawn(function()
                    while child.Parent and HOption.poisonGrab do
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.CFrame = part.CFrame
                        end
                        task.wait()
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.Position = Vector3.new(0, -500, 0)
                        end
                    end
                end)
            end
        end
    end
    if HOption.radioactiveGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                task.spawn(function()
                    while child.Parent and HOption.radioactiveGrab do
                        UFO.Position = part.Position
                        task.wait()
                        UFO.Position = Vector3.new(0, -500, 0)
                    end
                end)
            end
        end
    end
    if HOption.anchorGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                anchorPart(part)
            end
        end
    end
    if HOption.crazyGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                local bav = Instance.new("BodyAngularVelocity", part)
                bav.AngularVelocity = Vector3.new(0, 1000, 0)
                bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                task.delay(5, function()
                    if bav then bav:Destroy() end
                end)
            end
        end
    end
    if HOption.spinGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                local bav = Instance.new("BodyAngularVelocity", part)
                bav.AngularVelocity = Vector3.new(0, 100, 0)
                bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            end
        end
    end
    if HOption.ultraGrab and child.Name == "GrabParts" then
        local dragPart = child:FindFirstChild("DragPart")
        if dragPart then
            local ao = dragPart:FindFirstChild("AlignOrientation")
            local ap = dragPart:FindFirstChild("AlignPosition")
            if ao then
                ao.Responsiveness = 200
                ao.MaxTorque = math.huge
            end
            if ap then
                ap.Responsiveness = 200
                ap.MaxForce = math.huge
            end
        end
    end
    if HOption.perspectiveGrab and child.Name == "GrabParts" then
        local gp = child:FindFirstChild("GrabPart")
        if gp and gp:FindFirstChild("WeldConstraint") then
            local part = gp.WeldConstraint.Part1
            if part then
                task.spawn(function()
                    local root = getRoot()
                    if not root then return end
                    local origPos = root.CFrame
                    local speed = HOption.perspectiveSpeed or 50
                    local perspConn = RunService.Heartbeat:Connect(function(dt)
                        if not child.Parent or not HOption.perspectiveGrab then
                            perspConn:Disconnect()
                            return
                        end
                        local moveDir = getHumanoid() and getHumanoid().MoveDirection or Vector3.zero
                        root.CFrame = root.CFrame + moveDir * speed * dt
                        root.CFrame = CFrame.new(root.Position, part.Position)
                    end)
                    child.AncestryChanged:Connect(function()
                        if not child.Parent then
                            perspConn:Disconnect()
                            root.CFrame = origPos
                        end
                    end)
                end)
            end
        end
    end
end)

RunService.Heartbeat:Connect(function()
    if HOption.compileParts then
        updateCompiledGroups()
    end
    if HOption.crazyLine then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local torso = plr.Character:FindFirstChild("Torso") or plr.Character:FindFirstChild("UpperTorso")
                if torso then
                    CreateGrabLine:FireServer(torso, torso.CFrame)
                end
            end
        end
    end
end)

-- ============================================
-- AURAS LOOP
-- ============================================

local tornadoObjects = {}

RunService.Heartbeat:Connect(function()
    if HOption.poisonAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= 30 then
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.CFrame = hrp.CFrame
                        end
                        task.wait()
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.Position = Vector3.new(0, -500, 0)
                        end
                    end
                end
            end
        end
    end
    if HOption.flingAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= 25 then
                        if not hrp:FindFirstChild("FlingAuraBV") then
                            local bv = Instance.new("BodyVelocity", hrp)
                            bv.Name = "FlingAuraBV"
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bv.Velocity = (hrp.Position - root.Position).Unit * HOption.flingStrength + Vector3.new(0, 500, 0)
                            task.delay(0.5, function()
                                if bv then bv:Destroy() end
                            end)
                        end
                    end
                end
            end
        end
    end
    if HOption.attractAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= 35 then
                        local hum = plr.Character:FindFirstChild("Humanoid")
                        if hum then
                            hum.Sit = false
                            hum:MoveTo(root.Position)
                        end
                    end
                end
            end
        end
    end
    if HOption.grabAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local head = plr.Character:FindFirstChild("Head")
                    if head and (root.Position - head.Position).Magnitude <= 25 then
                        if not isOwner(head) then
                            grabPart(head, false)
                        end
                    end
                end
            end
        end
    end
    if HOption.tornadoAura then
        local root = getRoot()
        if root then
            local angle = 0
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= HOption.tornadoRange then
                        if not hrp:FindFirstChild("TornadoBV") then
                            local bv = Instance.new("BodyVelocity", hrp)
                            bv.Name = "TornadoBV"
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            table.insert(tornadoObjects, {Part = hrp, BV = bv, Angle = angle})
                            angle = angle + math.pi * 2 / 8
                        end
                    end
                end
            end
            for _, obj in pairs(tornadoObjects) do
                if obj.Part and obj.Part.Parent then
                    obj.Angle = obj.Angle + (HOption.tornadoSpeed or 5) * 0.01
                    local x = math.cos(obj.Angle) * 15
                    local z = math.sin(obj.Angle) * 15
                    obj.BV.Velocity = Vector3.new(x, 50, z)
                else
                    if obj.BV then obj.BV:Destroy() end
                end
            end
            tornadoObjects = {}
        end
    end
    if HOption.blackholeAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= 40 then
                        if not hrp:FindFirstChild("BlackholeBV") then
                            local bv = Instance.new("BodyVelocity", hrp)
                            bv.Name = "BlackholeBV"
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bv.Velocity = (root.Position - hrp.Position).Unit * HOption.blackholeStrength
                            task.delay(1, function()
                                if bv then bv:Destroy() end
                            end)
                        end
                    end
                end
            end
        end
    end
end)

-- ============================================
-- AUTOGUCCI LOOP
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.autoGucci then
        autoGucciBlobman()
    end
    if HOption.autoGucciTractor then
        autoGucciTractor()
    end
    if HOption.autoGucciTrain then
        autoGucciTrain()
    end
end)

-- ============================================
-- SILENT AIM LOOP
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.killAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= (HOption.killRange or 30) then
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.CFrame = hrp.CFrame
                        end
                        task.wait()
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.Position = Vector3.new(0, -500, 0)
                        end
                    end
                end
            end
        end
    end
    if HOption.tearAura then
        local root = getRoot()
        if root then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and (root.Position - hrp.Position).Magnitude <= 30 then
                        RagdollRemote:FireServer(hrp, 0)
                    end
                end
            end
        end
    end
    if HOption.silentAimV2 then
        local target = getClosestPlayerToCursor()
        if target and target.Character then
            local part = target.Character:FindFirstChild(HOption.silentPart) or target.Character:FindFirstChild("HumanoidRootPart")
            if part then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, part.Position)
            end
        end
    end
end)

-- ============================================
-- ANTI LOOPS
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.antiGrab and LocalPlayer.Character then
        local root = getRoot()
        if root then
            if LocalPlayer.IsHeld.Value then
                root.Anchored = true
                Struggle:FireServer()
                RagdollRemote:FireServer(root, 0)
            else
                root.Anchored = false
            end
        end
    end
    if HOption.antiRagdoll and LocalPlayer.Character then
        local hum = getHumanoid()
        if hum and hum.Ragdolled and hum.Ragdolled.Value then
            hum.Ragdolled.Value = false
            RagdollRemote:FireServer(getRoot(), 0)
        end
    end
    if HOption.antiVoid and LocalPlayer.Character then
        local root = getRoot()
        if root and root.Position.Y < -300 then
            root.CFrame = CFrame.new(0, 10, 0)
            root.AssemblyLinearVelocity = Vector3.zero
        end
    end
    if HOption.antiFire and LocalPlayer.Character then
        local root = getRoot()
        if root then
            local firePart = root:FindFirstChild("FirePlayerPart")
            if firePart and firePart:FindFirstChild("CanBurn") and firePart.CanBurn.Value then
                firetouchinterest(firePart, ExtinguishPart, 0)
            end
        end
    end
    if HOption.antiBlob and LocalPlayer.Character then
        local hum = getHumanoid()
        if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
            local blob = hum.SeatPart.Parent
            local leftDetector = blob:FindFirstChild("LeftDetector")
            local rightDetector = blob:FindFirstChild("RightDetector")
            if leftDetector and rightDetector then
                local leftWeld = leftDetector:FindFirstChild("LeftWeld")
                local rightWeld = rightDetector:FindFirstChild("RightWeld")
                if leftWeld then leftWeld.Enabled = false end
                if rightWeld then rightWeld.Enabled = false end
                hum.Sit = false
            end
        end
    end
    if HOption.antiKick and LocalPlayer.Character then
        local kunai = SpawnedInToys:FindFirstChild("NinjaKunai")
        if not kunai then
            kunai = spawnToyAtHead("NinjaKunai")
        end
        if kunai then
            local stickyPart = kunai:FindFirstChild("StickyPart")
            if stickyPart then
                if not isOwner(stickyPart) then
                    grabPart(stickyPart, true)
                end
                local leg = LocalPlayer.Character:FindFirstChild("Left Leg") or LocalPlayer.Character:FindFirstChild("LeftLowerLeg")
                if leg and not stickyPart:FindFirstChild("StickyWeld") then
                    StickyPartEvent:FireServer(stickyPart, leg, CFrame.new(0, -0.5, 0) * CFrame.Angles(0, 0, math.rad(90)))
                end
            end
        end
    end
    if HOption.antiFling and LocalPlayer.Character then
        local root = getRoot()
        if root then
            PhysicsService:SetPartCollisionGroup(root, "PlotPlayers")
        end
    else
        local root = getRoot()
        if root then
            PhysicsService:SetPartCollisionGroup(root, "Players")
        end
    end
    if HOption.antiBanana then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                for _, v in pairs(plr.Character:GetDescendants()) do
                    if v.Name == "BananaPeel" and v:IsA("BasePart") then
                        v.CanTouch = false
                    end
                end
            end
        end
        for _, toy in pairs(SpawnedInToys:GetChildren()) do
            if toy.Name == "FoodBanana" then
                local holdPart = toy:FindFirstChild("HoldPart")
                if holdPart then
                    local rigidConstraint = holdPart:FindFirstChild("RigidConstraint")
                    if rigidConstraint then
                        rigidConstraint.Enabled = false
                    end
                end
            end
        end
    end
    if HOption.antiDsync then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                if hrp and hrp.Massless then
                    hrp.Massless = false
                end
            end
        end
    end
    if HOption.antiInputLag then
        if not HOption.antiInputToy then
            HOption.antiInputToy = "FoodDonut"
        end
        local toy = SpawnedInToys:FindFirstChild(HOption.antiInputToy)
        if not toy then
            toy = spawnToyAtHead(HOption.antiInputToy)
        end
        if toy then
            local holdPart = toy:FindFirstChild("HoldPart")
            if holdPart then
                local holdFunc = holdPart:FindFirstChild("HoldItemRemoteFunction")
                if holdFunc then
                    holdFunc:InvokeServer(toy, LocalPlayer.Character)
                end
            end
        end
    end
end)

-- ============================================
-- EXPLOSION LOOP
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.autoExplode then
        local root = getRoot()
        if not root then return end
        local targets = {}
        if HOption.explosionTarget == "Mouse" then
            local mousePos = UserInputService:GetMouseLocation()
            local ray = Camera:ScreenPointToRay(mousePos.X, mousePos.Y)
            local result = Workspace:Raycast(ray.Origin, ray.Direction * 1000)
            if result then
                targets = {result.Position}
            end
        elseif HOption.explosionTarget == "Nearest Player" then
            local nearest = getNearestPlayer()
            if nearest then
                targets = {nearest.Position}
            end
        elseif HOption.explosionTarget == "All Players" then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        table.insert(targets, hrp.Position)
                    end
                end
            end
        end
        for _, pos in pairs(targets) do
            for i = 1, HOption.explosionAmmount or 1 do
                local toy = spawnToy(HOption.explosionType)
                if toy then
                    local hitbox = getHitboxPart(toy, HOption.explosionType)
                    if hitbox then
                        explodeBomb(hitbox, hitbox, pos)
                    end
                    deleteToy(toy)
                end
                if HOption.explosionDelay > 0 then
                    task.wait(HOption.explosionDelay)
                end
            end
        end
    end
end)

-- ============================================
-- LOOP LOOP (Player Loops)
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.loopEnabled then
        for _, plr in pairs(loopPlayersList) do
            if plr and plr.Character then
                local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                local hum = plr.Character:FindFirstChild("Humanoid")
                if hrp and hum and hum.Health > 0 then
                    if HOption.loopType == "Kill" then
                        hum:ChangeState(Enum.HumanoidStateType.Dead)
                    elseif HOption.loopType == "Bring" then
                        local root = getRoot()
                        if root then
                            grabPart(hrp, true)
                            hrp.CFrame = root.CFrame + Vector3.new(0, 5, 0)
                            ungrabPart(hrp)
                        end
                    elseif HOption.loopType == "Fling" then
                        if not hrp:FindFirstChild("LoopFlingBV") then
                            local bv = Instance.new("BodyVelocity", hrp)
                            bv.Name = "LoopFlingBV"
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bv.Velocity = Vector3.new(0, 10000, 0)
                            task.delay(0.5, function()
                                if bv then bv:Destroy() end
                            end)
                        end
                    elseif HOption.loopType == "Ragdoll" then
                        RagdollRemote:FireServer(hrp, 0)
                    elseif HOption.loopType == "Poison" then
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.CFrame = hrp.CFrame
                        end
                        task.wait()
                        for _, poison in pairs(HFPS.PoisonParts) do
                            poison.Position = Vector3.new(0, -500, 0)
                        end
                    elseif HOption.loopType == "Fire" then
                        local toy = spawnToyAtHead("Campfire")
                        if toy then
                            local firePart = toy:FindFirstChild("FirePlayerPart")
                            if firePart then
                                firePart.Position = hrp.Position
                                task.delay(2, function()
                                    deleteToy(toy)
                                end)
                            end
                        end
                    elseif HOption.loopType == "Banana" then
                        local leg = plr.Character:FindFirstChild("Right Leg") or plr.Character:FindFirstChild("RightLowerLeg")
                        if leg then
                            local toy = spawnToyAtHead("FoodBanana")
                            if toy then
                                local peel = toy:FindFirstChild("BananaPeel")
                                if peel then
                                    firetouchinterest(peel, leg, 0)
                                    task.delay(2, function()
                                        deleteToy(toy)
                                    end)
                                end
                            end
                        end
                    end
                end
            end
        end
    end
    if HOption.kickLoop and HOption.kickTarget and HOption.kickTarget.Character then
        local hrp = HOption.kickTarget.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            local bv = Instance.new("BodyPosition", hrp)
            bv.Position = hrp.Position + Vector3.new(0, 5000, 0)
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.D = 1000
            bv.P = 50000
        end
    end
    if HOption.bringLoop and HOption.bringLoopTarget and HOption.bringLoopTarget.Character then
        local hrp = HOption.bringLoopTarget.Character:FindFirstChild("HumanoidRootPart")
        local root = getRoot()
        if hrp and root then
            grabPart(hrp, true)
            hrp.CFrame = root.CFrame + Vector3.new(0, 5, 0)
            ungrabPart(hrp)
        end
    end
    if HOption.lagServer then
        for i = 1, (HOption.lagIntensity or 100) do
            CreateGrabLine:FireServer(Workspace.SpawnLocation, Workspace.SpawnLocation.CFrame)
        end
    end
    if HOption.packetSend then
        local amount = HOption.packetValue or 1000
        for i = 1, amount do
            CreateGrabLine:FireServer(Workspace.SpawnLocation, Workspace.SpawnLocation.CFrame)
        end
    end
end)

-- ============================================
-- RGB LINE LOOP
-- ============================================

RunService.Heartbeat:Connect(function()
    if HOption.rgbLine then
        local cs = {}
        for i = 1, 20 do
            local color = Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))
            if i == 1 then
                cs[i] = ColorSequenceKeypoint.new(0, color)
            else
                cs[i] = ColorSequenceKeypoint.new((i - 1) / 19, color)
            end
        end
        UpdateLineColors:FireServer(ColorSequence.new(cs))
    end
end)

-- ============================================
-- EVENTOS DE PLAYER
-- ============================================

Players.PlayerAdded:Connect(function(plr)
    if HOption.esp and plr ~= LocalPlayer then
        plr.CharacterAdded:Connect(function(char)
            task.wait(0.5)
            local hl = Instance.new("Highlight", char)
            hl.FillColor = espFillColor
            hl.OutlineColor = espOutlineColor
            hl.FillTransparency = espFillTrans
            hl.OutlineTransparency = espOutlineTrans
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            table.insert(HFPS.ESP, hl)
        end)
    end
    if HOption.chams and plr ~= LocalPlayer then
        plr.CharacterAdded:Connect(function(char)
            task.wait(0.5)
            local hl = Instance.new("Highlight", char)
            hl.FillColor = chamsFillColor
            hl.OutlineColor = chamsOutlineColor
            hl.FillTransparency = chamsFillTrans
            hl.OutlineTransparency = chamsOutlineTrans
            hl.DepthMode = Enum.HighlightDepthMode.Occluded
            table.insert(HFPS.Chams, hl)
        end)
    end
end)

Players.PlayerRemoving:Connect(function(plr)
    if HFPS.ESP then
        for i, h in pairs(HFPS.ESP) do
            if h and h.Parent and h.Parent:FindFirstChild(plr.Name) then
                h:Destroy()
                HFPS.ESP[i] = nil
            end
        end
    end
    if HFPS.Chams then
        for i, h in pairs(HFPS.Chams) do
            if h and h.Parent and h.Parent:FindFirstChild(plr.Name) then
                h:Destroy()
                HFPS.Chams[i] = nil
            end
        end
    end
end)

Workspace.DescendantAdded:Connect(function(child)
    if HOption.muteBoombox and child.Name == "Boombox" then
        local speaker = child:FindFirstChild("Speaker")
        if speaker then speaker:Destroy() end
    end
    if HOption.muteJukebox and (child.Name == "JukeboxOrange" or child.Name == "JukeboxBlue" or child.Name == "Jukebox") then
        local speakerPart = child:FindFirstChild("SpeakerPart")
        if speakerPart then speakerPart:Destroy() end
    end
end)

-- ============================================
-- SYNC TIME
-- ============================================

if HOption.syncTime then
    task.spawn(function()
        while HOption.syncTime do
            local success, data = pcall(function()
                return game:HttpGet("https://timeapi.io/api/time/current/zone?timeZone=UTC")
            end)
            if success and data then
                local decoded = HttpService:JSONDecode(data)
                if decoded and decoded.hour then
                    Lighting.TimeOfDay = string.format("%02d:%02d:%02d", decoded.hour, decoded.minute, decoded.seconds)
                end
            end
            task.wait(60)
        end
    end)
end

-- ============================================
-- ANTI EXPLOSION
-- ============================================

Workspace.ChildAdded:Connect(function(child)
    if HOption.antiExplosion and child.Name == "Part" then
        local root = getRoot()
        if root and (root.Position - child.Position).Magnitude <= 18 then
            root.Anchored = true
            task.wait(0.1)
            root.Anchored = false
        end
    end
end)

-- ============================================
-- ANTI GRAB
-- ============================================

if HOption.antiGrab then
    local head = getHead()
    if head then
        head.ChildAdded:Connect(function(child)
            if child.Name == "PartOwner" and HOption.antiGrab then
                Struggle:FireServer()
                RagdollRemote:FireServer(getRoot(), 0)
            end
        end)
    end
end

-- ============================================
-- SILENT AIM HOOK
-- ============================================

local oldNamecall = nil
if hookmetamethod then
    oldNamecall = hookmetamethod(game, "__namecall", function(self, ...)
        if not checkcaller() and HOption.silentAim and self == Workspace then
            local method = getnamecallmethod()
            if method == "Raycast" then
                local nearest = nil
                local root = getRoot()
                if root then
                    local minDist = HOption.silentRange or 50
                    for _, plr in pairs(Players:GetPlayers()) do
                        if plr ~= LocalPlayer and plr.Character then
                            local target = plr.Character:FindFirstChild(HOption.silentPart)
                            if target then
                                local dist = (root.Position - target.Position).Magnitude
                                if dist < minDist then
                                    minDist = dist
                                    nearest = target
                                end
                            end
                        end
                    end
                end
                if nearest then
                    local args = {...}
                    if #args >= 2 then
                        local dir = (nearest.Position - args[1]).Unit * 1000
                        args[2] = dir
                        return oldNamecall(self, unpack(args))
                    end
                end
            end
        end
        return oldNamecall(self, ...)
    end)
end

-- ============================================
-- INICIALIZAÇÃO
-- ============================================

OrionLib:Init()

LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(0.5)
    HOption.walkSpeed = false
    HOption.jumpPower = false
    HOption.gravity = false
    HOption.noclip = false
    HOption.godMode = false
    if walkSpeedToggle then walkSpeedToggle:Set(false) end
    if jumpPowerToggle then jumpPowerToggle:Set(false) end
    if gravityToggle then gravityToggle:Set(false) end
end)

for _, v in pairs(Workspace.Plots:GetDescendants()) do
    if v.Name == "PlotBarrier" or v.Name == "PlotArea" then
        v.CanQuery = false
        v.CanCollide = false
    end
end

local lineColorsSequence = {}
for i = 1, 20 do
    lineColorsSequence[i] = ColorSequenceKeypoint.new((i - 1) / 19, lineColors[i])
end
UpdateLineColors:FireServer(ColorSequence.new(lineColorsSequence))
