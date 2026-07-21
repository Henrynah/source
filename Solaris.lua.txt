-- SolarisHub - Roblox Script
-- 構造化・最適化バージョン（キーバインドタブ削除、タブ順序修正）
-- ToyMod Wings 統合版 + Blitz Line + KillAll/BringAll/TargetKill + Train Control
-- ============================================
 
-- ============================================
-- UIライブラリの読み込み
-- ============================================
local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/nooblol2/SolarisHub.gg/refs/heads/main/good.orion')))()
 
-- ============================================
-- サービスの取得
-- ============================================
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Debris = game:GetService("Debris")
local Lighting = game:GetService("Lighting")
local VirtualUser = game:GetService("VirtualUser")
 
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = Workspace.CurrentCamera
 
-- ============================================
-- リモートイベント・フォルダの参照（早期定義）
-- ============================================
local characterEventsFolder = ReplicatedStorage:WaitForChild("CharacterEvents")
local ragdollRemoteEvent = characterEventsFolder:WaitForChild("RagdollRemote")
local struggleEvent = characterEventsFolder:WaitForChild("Struggle")
 
local grabEventsFolder = ReplicatedStorage:WaitForChild("GrabEvents")
local setNetworkOwnerEvent = grabEventsFolder:WaitForChild("SetNetworkOwner")
local destroyGrabLineEvent = grabEventsFolder:WaitForChild("DestroyGrabLine")
 
local menuToysFolder = ReplicatedStorage:WaitForChild("MenuToys")
local SpawnToyRF = menuToysFolder:WaitForChild("SpawnToyRemoteFunction")
local DeleteToyRE = menuToysFolder:WaitForChild("DestroyToy")
 
local playerEventsFolder = ReplicatedStorage:WaitForChild("PlayerEvents")
local StickyPartEvent = playerEventsFolder:WaitForChild("StickyPartEvent")
 
local isHeldValue = LocalPlayer:WaitForChild("IsHeld")
 
local playerScriptsFolder = LocalPlayer:WaitForChild("PlayerScripts")
local anticreatelinelocalscript = playerScriptsFolder:WaitForChild("CharacterAndBeamMove")
 
local spawnedInToysFolder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
 
-- アンチバーン用消火パーツ
local apagarfogo = Workspace.Map.Hole.PoisonBigHole:WaitForChild("ExtinguishPart")
apagarfogo.Size = Vector3.new(0.5, 0.5, 0.5)
apagarfogo.Transparency = 1
if apagarfogo:FindFirstChild("Tex") then
    apagarfogo.Tex.Transparency = 1
end
 
-- Blitz用追加イベント
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
local ExtendGrabLine = GrabEvents:WaitForChild("ExtendGrabLine")
local updateLineColorsEvent = ReplicatedStorage:WaitForChild("DataEvents"):WaitForChild("UpdateLineColorsEvent")
 
-- トレインコントロール用
local PM = playerScriptsFolder:WaitForChild("PlayerModule")
local CM = require(PM:WaitForChild("ControlModule"))
 
-- ============================================
-- グローバル設定テーブル（タブ順）
-- ============================================
local Settings = {
    -- グラブタブ
    Grab = {
        strength = 5000,
        EnableThrowStrength = false,
        VoidGrab = false,
        DeathGrab = false,
        KickGrab = false,
        NoclipGrab = false,
    },
    -- アンチタブ
    Anti = {
        -- 保護機能
        AntiGrab = false,
        -- 邪魔対策
        AntiBurn = false,
        AntiExplosion = false,
        AntiVoid = false,
        AntiLag = false,
        AntiKick = false,
        AntiKickKunai = false,
        -- 追加のAnti機能
        AntiSticky = false,
        AntiPaint = false,
        AntiGucciBlobman = false,
        AntiGucciTrain = false,
        AntiRagdoll = false,
        AntiBanana = false,
    },
    -- ブロブマンベータタブ
    BlobmanBeta = {
        ignoreFriends = false,
        kickAuraActive = false,
        KickDelay = 0.18,
        FloatAmount = 16,
        ignoreHeights = {526.377685546875, 5.2832231521606445},
        currentSide = "Left",
        isExecuting = false,
        auraKickedPlayers = {},
        selectedTarget = nil,
    },
    -- PvPタブ
    PvP = {
        SilentAimEnabled = false,
        AimStrength = 50,
        AutoGrabEnabled = false,
    },
    -- Loop機能タブ（KillAll統合）
    Loop = {
        KillAll = false,
        BringAll = false,
        TP_Priority = 0,
        TeleportHeight = -3,
        TargetPlayers = {},
        OriginalPosition = nil,
        LastTeleportTime = 0,
        TeleportCooldown = 0.3,
        ProcessedPlayers = {},
        WhitelistFriends = false,
        LastTargetKillTime = 0,
        killAllLoop = nil,
        targetKillLoop = nil,
        killAllEnabled = false,
        targetKillEnabled = false,
        floatConnection = nil,
    },
    -- Visualsタブ
    Visuals = {
        ESPEnabled = false,
        ESPFillColor = Color3.fromRGB(255, 0, 0),
        ESPOutlineColor = Color3.fromRGB(255, 255, 255),
        ESPFillTransparency = 0.5,
        ESPOutlineTransparency = 0,
        ShowHitbox = false,
        HitboxColor = Color3.fromRGB(0, 100, 255),
        ShowPlayerInfo = false,
        CameraFOV = 70,
        SpectatePlayer = nil,
    },
    -- Auraタブ
    Aura = {
        VoidAura = false,
        RagdollAura = false,
        DeathAura = false,
        FireAura = false,
        AuraRadius = 32,
        IgnoreFriendAura = false,
    },
    -- Lineタブ（Blitz統合）
    Line = {
        ultragrabbb = false,
        LineColorChangeValue = Color3.fromRGB(255, 0, 0),
        CrazyLine = false,
        InvisibleLine = false,
        RainbowLine = false,
        FlashingLine = false,
        GradientLine = false,
        LineSpeed = 1,
        StrobeSpeed = 0.5,
        PulseEffect = false,
        IncreaseLineExtend = 3,
        Lag_Intensity = 150,
        LagServerActive = false,
    },
    -- ToyModタブ（統合）
    ToyMod = {
        UsePlotItems = false,
        XSpacing = 0.1,
        YOffset = 0,
        ZOffset = 0,
        AngleH = 90,
        AngleV = 0,
        FlapEnabled = false,
        FlapSpeed = 2,
        FlapRange = 30,
        FlapType = 1,
        SpeedLinkFlap = false
    },
    -- Train Controlタブ
    TrainControl = {
        Enabled = false,
        FlySpeed = 15,
        NoClip = false,
        ToyName = "FoodHamburger",
        Interval = 0.01,
        HeadOffset = Vector3.new(0, 3, 0),
        SpawnTimeout = 0.1,
        SPEED_MULT = 25,
        BV_P = 25000,
        BG_P = 6000,
        SeatGraceSeconds = 2.5,
        SitRetryInterval = 0.01,
        NetworkOwnershipInterval = 0.005,
        ParallelOwnershipThreads = 5,
        Running = false,
        TrainEnableAt = 0,
        LastSitAttempt = 0,
        HadSeatOnce = false,
        LoopThread = nil,
        VFlyEnabled = false,
        TargetPart = nil,
        BV = nil,
        BG = nil,
        LastNetworkOwnership = 0,
        OwnershipThreads = {},
        NoClipVehicleModel = nil,
        NoClipCharModel = nil,
        NoClipVehicleState = {},
        NoClipCharState = {},
        prevOcclusionMode = nil,
    },
    -- Miscタブ
    Misc = {
        ThirdPerson = false,
        FPSBooster = false,
    },
    -- Keybindタブ
    Keybind = {
        SilentAimToggleKey = Enum.KeyCode.Q,
    }
}
 
-- ============================================
-- 内部状態管理テーブル
-- ============================================
local State = {
    -- 内部フラグ
    IsCharacterInRagdoll = false,
    noclipRunning = false,
    noclipLoop = nil,
    noclipTrackedChar = nil,
    isBarrierRunning = false,
 
    -- PvP関連
    CameraClone = nil,
    CameraInitialized = false,
    HRPs = {},
    Character = nil,
    Humanoid = nil,
    Root = nil,
    GrabbedPart = nil,
    LastGrabbedTarget = nil,
    LastGrabbedTime = 0,
    ReachDistance = 30,
    LastGrabTime = 0,
    GrabCooldown = 0.05,
 
    -- コネクション管理
    connections = {
        strength = nil,
        antiGrab = nil,
        antiExplosionChar = nil,
        walkspeed = nil,
        infiniteJump = nil,
        thirdPerson = nil,
        fpsBooster = nil,
    },
 
    -- ループ管理
    loops = {
        noclip = nil,
        kunaiCheck = nil,
        antiBanana = nil,
    },
 
    -- タスク管理
    struggleTasks = {},
    GrabMaintainConnections = {},
 
    -- データ保存
    ESPObjects = {},
    HitboxObjects = {},
    NameLabels = {},
    paintPartsBackup = {},
    paintConnections = {},
    originalSettings = nil,
    currentKunai = nil,
 
    -- タイマー管理
    timers = {
        AuraTimer = 0,
        espTimer = 0,
        AntiBananaTimer = 0,
        LastUpdate = 0,
        UPDATE_INTERVAL = 0.1,
    },
 
    -- プレイヤー設定保存
    originalJump = {
        UseJumpPower = nil,
        JumpPower = nil,
        JumpHeight = nil,
    },
 
    -- アンチGucci用
    antiGucci = {
        safePosition = nil,
        restoreFrames = 0,
        connection = nil,
        safePositionTrain = nil,
        restoreFramesTrain = 0,
        connectionTrain = nil,
    },
 
    -- ToyMod Wings 状態
    ToyModWings = {
        Parts = {},
        BodyPositions = {},
        AlignOrientations = {},
        OriginalAnchored = {},
        Connection = nil,
        FlapTime = 0,
        LastPos = nil,
        TargetCFrames = {}
    },
 
    -- BringAll/KillAll用
    cameraAnchor = nil,
    originalCameraSubject = nil,
    freezePart = nil,
}
 
-- ============================================
-- 基本ユーティリティ関数
-- ============================================
local Utility = {}
 
function Utility.GetPlayerCharacter()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChildOfClass("Humanoid") then
        return char
    end
end
 
function Utility.GetPlayerRootPart()
    local char = Utility.GetPlayerCharacter()
    return char and (char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso"))
end
 
function Utility.GetPlayerCFrame()
    local root = Utility.GetPlayerRootPart()
    return root and root.CFrame
end
 
function Utility.getHum(char)
    return char and char:FindFirstChildOfClass("Humanoid")
end
 
function Utility.getRoot(char)
    return char and (char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso"))
end
 
function Utility.waitForChild(parent, childName, timeout)
    local startTime = tick()
    while tick() - startTime < timeout do
        local child = parent:FindFirstChild(childName)
        if child then
            return child
        end
        task.wait(0.1)
    end
    return nil
end
 
-- ============================================
-- FTAP Grab 補助関数
-- ============================================
local FTAP = {}
 
function FTAP.SetNetworkOwner(part, cf)
    if not part or not part.Parent then return end
    pcall(function()
        setNetworkOwnerEvent:FireServer(part, cf or Utility.GetPlayerCFrame())
    end)
end
 
function FTAP.Velocity(part, vel)
    if not part then return end
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
    bv.Velocity = vel
    bv.Parent = part
    Debris:AddItem(bv, 1.2)
end
 
function FTAP.DestroyGrabLine(part)
    if not part then return end
    pcall(function()
        destroyGrabLineEvent:FireServer(part)
    end)
end
 
-- ============================================
-- Auraタブ - 補助関数
-- ============================================
local AuraHelper = {}
 
function AuraHelper.GetNearParts(origin, radius)
    return Workspace:GetPartBoundsInRadius(origin, radius)
end
 
function AuraHelper.Velocity(part, value)
    local b = Instance.new("BodyVelocity")
    b.MaxForce = Vector3.one * math.huge
    b.Velocity = value
    b.Parent = part
    Debris:AddItem(b, 1)
end
 
function AuraHelper.MoveTo(part, x)
    for _, v in ipairs(part.Parent:GetDescendants()) do
        if v:IsA("BasePart") then
            v.CanCollide = false
        end
    end
    local pos = typeof(x) == "CFrame" and x.Position or x
    local b = Instance.new("BodyPosition")
    b.MaxForce = Vector3.one * math.huge
    b.Position = pos
    b.P = 2e4
    b.D = 5e3
    b.Parent = part
    task.spawn(function()
        b.ReachedTarget:Wait()
        pcall(game.Destroy, b)
        for _, v in ipairs(part.Parent:GetDescendants()) do
            if v:IsA("BasePart") then
                v.CanCollide = true
            end
        end
    end)
end
 
function AuraHelper.SpawnToy(name, cframe)
    local success, toy = pcall(function()
        return SpawnToyRF:InvokeServer(name, cframe, Vector3.zero)
    end)
    if success then
        return toy
    end
    return nil
end
 
function AuraHelper.IsFriend(player)
    if not player or not player.UserId or not LocalPlayer then return false end
    return LocalPlayer:IsFriendsWith(player.UserId)
end
 
-- ============================================
-- グラブタブ機能
-- ============================================
local GrabFeature = {}
 
-- 力の強化
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
 
    local velocityObj = Instance.new("BodyVelocity")
    velocityObj.MaxForce = Vector3.zero
    velocityObj.Velocity = Vector3.zero
    velocityObj.P = 2000
    velocityObj.Name = "ThrowForce"
    velocityObj.Parent = partToImpulse
 
    local conn
    conn = model:GetPropertyChangedSignal("Parent"):Connect(function()
        if not model.Parent then
            local lastInput = UserInputService:GetLastInputType()
            if lastInput == Enum.UserInputType.MouseButton2 or lastInput == Enum.UserInputType.Touch then
                if Settings.Grab.EnableThrowStrength then
                    velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    velocityObj.Velocity = Workspace.CurrentCamera.CFrame.LookVector * Settings.Grab.strength
                    Debris:AddItem(velocityObj, 1)
                else
                    velocityObj:Destroy()
                end
            else
                velocityObj:Destroy()
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
 
-- Void/Death/Kick Grab用ネットワーク維持
function GrabFeature.maintainNetworkOwnership(grabModel, grabbedPart)
    if State.GrabMaintainConnections[grabModel] then
        State.GrabMaintainConnections[grabModel]:Disconnect()
        State.GrabMaintainConnections[grabModel] = nil
    end
 
    local conn = RunService.Heartbeat:Connect(function()
        if not grabModel.Parent or not grabbedPart.Parent then
            if State.GrabMaintainConnections[grabModel] then
                State.GrabMaintainConnections[grabModel]:Disconnect()
                State.GrabMaintainConnections[grabModel] = nil
            end
            return
        end
 
        FTAP.SetNetworkOwner(grabbedPart)
        FTAP.SetNetworkOwner(grabbedPart, Utility.GetPlayerCFrame())
 
        if Settings.Grab.VoidGrab then
            FTAP.Velocity(grabbedPart, Vector3.new(0, 12000, 0))
        end
    end)
 
    State.GrabMaintainConnections[grabModel] = conn
end
 
function GrabFeature.onGrabPartsAdded_FTAP(model)
    if model.Name ~= "GrabParts" or not model:IsA("Model") then return end
 
    local grabPart = model:WaitForChild("GrabPart", 8)
    if not grabPart then return end
 
    local weld = grabPart:FindFirstChild("WeldConstraint")
    if not weld or not weld.Part1 then return end
 
    local grabbedPart = weld.Part1
    if grabbedPart.Anchored then return end
 
    local victimChar = grabbedPart.Parent
    if not victimChar then return end
 
    local victimPlayer = Players:GetPlayerFromCharacter(victimChar)
    if not victimPlayer then return end
 
    local anySpecialEnabled = Settings.Grab.VoidGrab or Settings.Grab.DeathGrab or Settings.Grab.KickGrab or Settings.Grab.NoclipGrab
 
    if not anySpecialEnabled then
        return
    end
 
    task.wait(0.07)
 
    for _ = 1, 6 do
        FTAP.SetNetworkOwner(grabbedPart)
        task.wait(0.04)
    end
 
    GrabFeature.maintainNetworkOwnership(model, grabbedPart)
 
    if Settings.Grab.VoidGrab then
        FTAP.Velocity(grabbedPart, Vector3.new(0, 14000, 0))
        task.delay(2.0, function()
            if grabbedPart and grabbedPart.Parent then
                FTAP.DestroyGrabLine(grabbedPart)
            end
        end)
    end
 
    if Settings.Grab.DeathGrab then
        local hum = Utility.getHum(victimChar)
        if hum then
            hum.Health = 0
            hum:ChangeState(Enum.HumanoidStateType.Dead)
            task.delay(0.3, function()
                if grabbedPart and grabbedPart.Parent then
                    FTAP.DestroyGrabLine(grabbedPart)
                end
            end)
        end
    end
 
    if Settings.Grab.KickGrab then
        task.spawn(function()
            task.wait(0.55)
            if not grabbedPart or not grabbedPart.Parent then return end
 
            local victimRoot = Utility.getRoot(victimChar)
            if not victimRoot then return end
 
            local myRoot = Utility.GetPlayerRootPart()
            if not myRoot then return end
 
            local oldPos = myRoot.CFrame
 
            while victimRoot and victimRoot.Parent do
                local hum = Utility.getHum(victimChar)
                if hum and hum.FloorMaterial ~= Enum.Material.Air then
                    myRoot.CFrame = victimRoot.CFrame * CFrame.new(0, 3, 0)
                    task.wait(0.14)
 
                    FTAP.SetNetworkOwner(victimRoot)
                    task.wait(0.1)
 
                    local extremePos = CFrame.new(2.5e25, 2.5e25, 2.5e25)
                    local bp = Instance.new("BodyPosition")
                    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bp.Position = extremePos.Position
                    bp.P = 25000
                    bp.D = 6000
                    bp.Parent = victimRoot
 
                    FTAP.Velocity(victimRoot, Vector3.new(0, 1e6, 0) + victimRoot.AssemblyLinearVelocity * 1e6)
 
                    task.wait(0.28)
 
                    if victimRoot and victimRoot.Parent then
                        FTAP.DestroyGrabLine(victimRoot)
                    end
 
                    myRoot.CFrame = oldPos
                    bp:Destroy()
                    break
                end
                task.wait(0.04)
            end
        end)
    end
end
 
-- Noclip Grab
function GrabFeature.noclipGrab()
    State.noclipRunning = true
    while State.noclipRunning do
        local success, err = pcall(function()
            local grabModel = Workspace:FindFirstChild("GrabParts")
            if grabModel and grabModel.Name == "GrabParts" then
                local grabPart = grabModel:FindFirstChild("GrabPart")
                if not grabPart then return end
 
                local weld = grabPart:FindFirstChildOfClass("WeldConstraint")
                if not weld or not weld.Part1 then return end
 
                local grabbedPart = weld.Part1
                local character = grabbedPart.Parent
                if character and character:FindFirstChild("HumanoidRootPart") then
                    while Workspace:FindFirstChild("GrabParts") do
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = false
                            end
                        end
                        task.wait()
                    end
                    for _, part in pairs(character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = true
                        end
                    end
                end
            else
                task.wait()
            end
        end)
        if not success then
            warn("noclipGrab error: ".. tostring(err))
            task.wait(1)
        end
        task.wait()
    end
end
 
function GrabFeature.stopNoclip()
    State.noclipRunning = false
    if State.loops.noclip then
        task.cancel(State.loops.noclip)
        State.loops.noclip = nil
    end
end
 
function GrabFeature.toggleNoclip(enabled)
    if enabled then
        if State.loops.noclip then
            task.cancel(State.loops.noclip)
        end
        State.loops.noclip = task.spawn(GrabFeature.noclipGrab)
    else
        GrabFeature.stopNoclip()
    end
end
 
-- ============================================
-- アンチタブ機能
-- ============================================
local AntiFeature = {}
 
-- アンチグラブ v1
function AntiFeature.setupAntiGrabV1()
    isHeldValue.Changed:Connect(function(isBeingHeld)
        if isBeingHeld and Settings.Anti.AntiGrab then
            local hrp = (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()):WaitForChild("HumanoidRootPart")
            local conn
            conn = RunService.Heartbeat:Connect(function()
                if isHeldValue.Value then
                    hrp.Velocity = Vector3.new()
                    hrp.Anchored = true
                    struggleEvent:FireServer(LocalPlayer)
                    ragdollRemoteEvent:FireServer(hrp, 0)
                else
                    hrp.Velocity = Vector3.new()
                    hrp.Anchored = false
                    conn:Disconnect()
                end
            end)
        end
    end)
end
 
-- アンチグラブ v2
function AntiFeature.startStruggleLoop()
    local char = LocalPlayer.Character
    if not char or State.struggleTasks[char] then return end
    State.struggleTasks[char] = true
    task.spawn(function()
        while State.struggleTasks[char] do
            local head = char:FindFirstChild("Head")
            if head and head:FindFirstChild("PartOwner") and isHeldValue.Value then
                pcall(function()
                    struggleEvent:FireServer()
                    local gce = ReplicatedStorage:FindFirstChild("GameCorrectionEvents")
                    if gce then
                        local sav = gce:FindFirstChild("StopAllVelocity")
                        if sav then sav:FireServer() end
                    end
                end)
            else
                State.struggleTasks[char] = nil
                break
            end
            task.wait(0.1)
        end
    end)
end
 
function AntiFeature.enableAntiGrabSolaris()
    if State.connections.antiGrab then State.connections.antiGrab:Disconnect() end
    State.connections.antiGrab = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        if char and char:FindFirstChild("Head") then
            local head = char.Head
            if head:FindFirstChild("PartOwner") and isHeldValue.Value then
                AntiFeature.startStruggleLoop()
            end
        end
    end)
end
 
function AntiFeature.disableAntiGrabSolaris()
    if State.connections.antiGrab then
        State.connections.antiGrab:Disconnect()
        State.connections.antiGrab = nil
    end
    for k in pairs(State.struggleTasks) do
        State.struggleTasks[k] = nil
    end
end
 
-- アンチエクスプロージョン
local antiExplosionConnection = nil
 
function AntiFeature.setupAntiExplosion(character)
    local humanoid = character:WaitForChild("Humanoid", 5)
    if not humanoid then return end
    local ragdolled = humanoid:FindFirstChild("Ragdolled")
    if not ragdolled then return end
 
    if antiExplosionConnection then
        antiExplosionConnection:Disconnect()
    end
 
    antiExplosionConnection = ragdolled:GetPropertyChangedSignal("Value"):Connect(function()
        for _, part in ipairs(character:GetChildren()) do
            if part:IsA("BasePart") then
                part.Anchored = ragdolled.Value
            end
        end
    end)
end
 
-- AntiKick Kunai
local kunaiMessageGui = nil
local kunaiTextLabel = nil
 
function AntiFeature.createKunaiMessageGui()
    if kunaiMessageGui then return end
    kunaiMessageGui = Instance.new("ScreenGui")
    kunaiMessageGui.Name = "KunaiMessageGui"
    kunaiMessageGui.ResetOnSpawn = false
    kunaiMessageGui.Parent = PlayerGui
 
    kunaiTextLabel = Instance.new("TextLabel")
    kunaiTextLabel.Size = UDim2.new(0, 480, 0, 38)
    kunaiTextLabel.Position = UDim2.new(0, 16, 1, -60)
    kunaiTextLabel.AnchorPoint = Vector2.new(0, 1)
    kunaiTextLabel.BackgroundTransparency = 1
    kunaiTextLabel.TextColor3 = Color3.fromRGB(255, 240, 120)
    kunaiTextLabel.Font = Enum.Font.Code
    kunaiTextLabel.TextSize = 18
    kunaiTextLabel.TextXAlignment = Enum.TextXAlignment.Left
    kunaiTextLabel.TextTransparency = 1
    kunaiTextLabel.RichText = true
    kunaiTextLabel.Parent = kunaiMessageGui
end
 
local fadeInInfo = TweenInfo.new(0.3, Enum.EasingStyle.Linear)
local fadeOutInfo = TweenInfo.new(0.6, Enum.EasingStyle.Linear)
 
function AntiFeature.typeKunaiMessage(msg, charDelay)
    charDelay = charDelay or 0.038
    if not kunaiTextLabel then AntiFeature.createKunaiMessageGui() end
    kunaiTextLabel.Text = ""
    TweenService:Create(kunaiTextLabel, fadeInInfo, {TextTransparency = 0}):Play()
 
    task.spawn(function()
        for i = 1, #msg do
            kunaiTextLabel.Text = msg:sub(1, i)
            task.wait(charDelay)
        end
        task.wait(2.5)
        TweenService:Create(kunaiTextLabel, fadeOutInfo, {TextTransparency = 1}):Play()
    end)
end
 
function AntiFeature.getRightLeg(char)
    return char:FindFirstChild("Right Leg")
        or char:FindFirstChild("RightFoot")
        or char:FindFirstChild("RightLowerLeg")
        or char:FindFirstChild("RightUpperLeg")
end
 
function AntiFeature.cleanupMyKunaiToys()
    if not spawnedInToysFolder then return end
    for _, toy in ipairs(spawnedInToysFolder:GetChildren()) do
        if toy.Name == "NinjaKunai" then
            pcall(function()
                DeleteToyRE:FireServer(toy)
            end)
        end
    end
end
 
function AntiFeature.attachKunai(isReattach)
    AntiFeature.cleanupMyKunaiToys()
    local char = Utility.GetPlayerCharacter()
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local rightLeg = AntiFeature.getRightLeg(char)
    if not rightLeg then return end
 
    local spawnCFrame = hrp.CFrame * CFrame.new(0, 0, -3.5)
 
    task.spawn(function()
        pcall(function()
            SpawnToyRF:InvokeServer("NinjaKunai", spawnCFrame, Vector3.new(0, 0, 0))
        end)
    end)
 
    task.wait(0.1)
 
    local kunai = nil
    for i = 1, 15 do
        kunai = spawnedInToysFolder:FindFirstChild("NinjaKunai")
        if kunai and kunai:FindFirstChild("StickyPart") then break end
        task.wait(0.05)
    end
 
    if not kunai or not kunai:FindFirstChild("StickyPart") then
        if not isReattach then
            task.delay(0.3, function()
                if Settings.Anti.AntiKickKunai then AntiFeature.attachKunai(true) end
            end)
        end
        return
    end
 
    local sticky = kunai.StickyPart
 
    for i = 1, 25 do
        task.spawn(function()
            pcall(function()
                local randOffset = CFrame.new(
                    math.random(-10,10)/100,
                    math.random(-10,10)/100,
                    math.random(-8,8)/100
                )
                setNetworkOwnerEvent:FireServer(sticky, rightLeg.CFrame * randOffset)
            end)
        end)
    end
 
    local attachCFrame = CFrame.new(0.04, 0.48, 0.02) * CFrame.Angles(0, math.rad(1), math.rad(-90))
 
    for i = 1, 20 do
        task.spawn(function()
            pcall(function()
                StickyPartEvent:FireServer(sticky, rightLeg, attachCFrame)
            end)
        end)
    end
 
    task.spawn(function()
        for _, v in kunai:GetDescendants() do
            if v:IsA("BasePart") then
                v.Transparency = 0.7
            elseif v:IsA("Decal") or v:IsA("Texture") then
                v.Transparency = 0.7
            end
        end
    end)
 
    State.currentKunai = kunai
end
 
function AntiFeature.isKunaiAttached()
    if not Settings.Anti.AntiKickKunai then return false end
    if not spawnedInToysFolder then return false end
 
    local kunai = nil
    for _, obj in spawnedInToysFolder:GetChildren() do
        if obj.Name == "NinjaKunai" then kunai = obj break end
    end
 
    if not kunai or not kunai:FindFirstChild("StickyPart") then return false end
 
    local leg = AntiFeature.getRightLeg(Utility.GetPlayerCharacter())
    if not leg then return false end
 
    local dist = (kunai.StickyPart.Position - leg.Position).Magnitude
    return dist < 7
end
 
-- Anti Sticky
function AntiFeature.setTouchQuery(state)
    local char = Workspace:FindFirstChild(LocalPlayer.Name)
    if not char then return end
    for _, v in ipairs(char:GetChildren()) do
        if v:IsA("Part") or v:IsA("BasePart") then
            v.CanTouch = state
            v.CanQuery = state
        end
    end
end
 
-- Anti Paint
function AntiFeature.deleteAllPaintParts()
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
            local clone = obj:Clone()
            clone.Archivable = true
            State.paintPartsBackup[obj:GetDebugId()] = {
                clone = clone,
                parent = obj.Parent
            }
            obj:Destroy()
        end
    end
end
 
function AntiFeature.restorePaintParts()
    for _, data in pairs(State.paintPartsBackup) do
        if data.clone and data.parent then
            data.clone.Parent = data.parent
        end
    end
    State.paintPartsBackup = {}
end
 
function AntiFeature.watchNewPaintParts()
    table.insert(State.paintConnections, Workspace.DescendantAdded:Connect(function(obj)
        if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
            task.defer(function()
                if obj and obj.Parent then
                    local clone = obj:Clone()
                    clone.Archivable = true
                    State.paintPartsBackup[obj:GetDebugId()] = {
                        clone = clone,
                        parent = obj.Parent
                    }
                    obj:Destroy()
                end
            end)
        end
    end))
end
 
function AntiFeature.disconnectWatchers()
    for _, conn in ipairs(State.paintConnections) do
        if conn.Connected then
            conn:Disconnect()
        end
    end
    State.paintConnections = {}
end
 
-- Anti Gucci (Blobman)
function AntiFeature.spawnBlobman()
    local args = {
        [1] = "CreatureBlobman",
        [2] = CFrame.new(0, 5000000, 0),
        [3] = Vector3.new(0, 60, 0)
    }
    pcall(function()
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    end)
    local folder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", 5)
    if folder and folder:FindFirstChild("CreatureBlobman") then
        local blob = folder.CreatureBlobman
        if blob:FindFirstChild("Head") then
            blob.Head.CFrame = CFrame.new(0, 50000, 0)
            blob.Head.Anchored = true
        end
    end
end
 
function AntiFeature.startAntiGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    State.antiGucci.safePosition = rootPart.Position
 
    local folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    local seat = blob and blob:FindFirstChild("VehicleSeat")
 
    if not blob then
        AntiFeature.spawnBlobman()
        task.wait(1)
        folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        blob = folder and folder:FindFirstChild("CreatureBlobman")
        seat = blob and blob:FindFirstChild("VehicleSeat")
    end
 
    if seat and seat:IsA("VehicleSeat") then
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        seat:Sit(humanoid)
    end
 
    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            State.antiGucci.restoreFrames = 15
            State.antiGucci.safePosition = rootPart.Position
        end
    end)
 
    if State.antiGucci.connection then
        State.antiGucci.connection:Disconnect()
    end
 
    State.antiGucci.connection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then
            return
        end
 
        local beingHeld = LocalPlayer:FindFirstChild("IsHeld")
        if not beingHeld or not beingHeld.Value then
            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        end
 
        local blobFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        local currentBlob = blobFolder and blobFolder:FindFirstChild("CreatureBlobman")
        if currentBlob and currentBlob:FindFirstChild("Head") then
            currentBlob.Head.CFrame = CFrame.new(0, 50000, 0)
            currentBlob.Head.Anchored = true
        end
 
        if State.antiGucci.restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(State.antiGucci.safePosition)
            State.antiGucci.restoreFrames = State.antiGucci.restoreFrames - 1
        end
    end)
 
    task.spawn(function()
        while humanoid.Sit do
            task.wait(1)
        end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(State.antiGucci.safePosition)
    end)
end
 
function AntiFeature.stopAntiGucci()
    if State.antiGucci.connection then
        State.antiGucci.connection:Disconnect()
        State.antiGucci.connection = nil
    end
 
    local character = LocalPlayer.Character
    if character then
        local rootPart = character:FindFirstChild("HumanoidRootPart")
        local humanoid = character:FindFirstChild("Humanoid")
        if rootPart and State.antiGucci.safePosition then
            rootPart.CFrame = CFrame.new(State.antiGucci.safePosition)
        end
        if humanoid then
            humanoid.Sit = false
        end
    end
 
    task.wait(0.1)
    local blobFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
        pcall(function()
            ReplicatedStorage.MenuToys.DestroyToy:FireServer(blobFolder.CreatureBlobman)
        end)
    end
end
 
-- Anti Gucci (Train)
function AntiFeature.startAntiGucciTrain()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    State.antiGucci.safePositionTrain = rootPart.Position
 
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
            State.antiGucci.restoreFramesTrain = 15
            State.antiGucci.safePositionTrain = rootPart.Position
        end
    end)
 
    if State.antiGucci.connectionTrain then
        State.antiGucci.connectionTrain:Disconnect()
    end
 
    State.antiGucci.connectionTrain = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then
            return
        end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if State.antiGucci.restoreFramesTrain > 0 then
            rootPart.CFrame = CFrame.new(State.antiGucci.safePositionTrain)
            State.antiGucci.restoreFramesTrain = State.antiGucci.restoreFramesTrain - 1
        end
    end)
 
    task.spawn(function()
        while humanoid.Sit do
            task.wait(1)
        end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(State.antiGucci.safePositionTrain)
    end)
end
 
function AntiFeature.stopAntiGucciTrain()
    if State.antiGucci.connectionTrain then
        State.antiGucci.connectionTrain:Disconnect()
        State.antiGucci.connectionTrain = nil
    end
end
 
-- Anti-Ragdoll
function AntiFeature.ragdoll()
    local args = {
        [1] = LocalPlayer.Character:FindFirstChild("HumanoidRootPart"),
        [2] = 0
    }
    if args[1] then
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(unpack(args))
    end
end
 
-- ============================================
-- ブロブマンベータ機能
-- ============================================
local BlobmanBetaFeature = {}
 
function BlobmanBetaFeature.getLocalChar() return LocalPlayer.Character end
function BlobmanBetaFeature.getLocalRoot()
    if not BlobmanBetaFeature.getLocalChar() then return end
    return BlobmanBetaFeature.getLocalChar():FindFirstChild("HumanoidRootPart") or BlobmanBetaFeature.getLocalChar():FindFirstChild("Torso")
end
function BlobmanBetaFeature.getLocalHum()
    if not BlobmanBetaFeature.getLocalChar() then return end
    return BlobmanBetaFeature.getLocalChar():FindFirstChildOfClass("Humanoid")
end
function BlobmanBetaFeature.getInv()
    return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
end
 
function BlobmanBetaFeature.SetNetworkOwner(part)
    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, BlobmanBetaFeature.getLocalRoot().CFrame)
end
 
function BlobmanBetaFeature.ungrab(part)
    ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(part)
end
 
function BlobmanBetaFeature.getBlobman()
    local inv = BlobmanBetaFeature.getInv()
    if inv then
        local v = inv:FindFirstChild("CreatureBlobman")
        if v and v.ClassName == "Model" and v:FindFirstChild("VehicleSeat") then
            return v
        end
    end
    for _, p in Workspace.PlotItems:GetChildren() do
        local m = p:FindFirstChild("CreatureBlobman")
        if m and m:FindFirstChild("PlayerValue") and m.PlayerValue.Value == LocalPlayer.Name then
            return m
        end
    end
    return nil
end
 
function BlobmanBetaFeature.spawnBlobman()
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", BlobmanBetaFeature.getLocalRoot().CFrame, Vector3.zero)
    task.wait(1.0)
    return BlobmanBetaFeature.getBlobman()
end
 
function BlobmanBetaFeature.destroyBlobman()
    local blob = BlobmanBetaFeature.getBlobman()
    if blob then
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
    end
end
 
function BlobmanBetaFeature.resetBlobmanPhysics()
    local blob = BlobmanBetaFeature.getBlobman()
    if not blob then return end
    for _, part in blob:GetDescendants() do
        if part:IsA("BasePart") then
            part.AssemblyLinearVelocity = Vector3.zero
            part.AssemblyAngularVelocity = Vector3.zero
        end
    end
end
 
function BlobmanBetaFeature.stabilizeBlobman()
    local blob = BlobmanBetaFeature.getBlobman()
    if not blob or not blob:FindFirstChild("VehicleSeat") then return end
    local seat = blob.VehicleSeat
    if seat then
        seat.CFrame = seat.CFrame + (BlobmanBetaFeature.getLocalRoot().CFrame.Position - seat.Position) * 0.1
    end
end
 
function BlobmanBetaFeature.isSittingOnBlobman()
    local hum = BlobmanBetaFeature.getLocalHum()
    if not hum then return false end
    local blob = BlobmanBetaFeature.getBlobman()
    if not blob or not blob:FindFirstChild("VehicleSeat") then return false end
    local seat = blob.VehicleSeat
    return hum.Sit and hum.SeatPart == seat
end
 
function BlobmanBetaFeature.ensureSitBlobman()
    local blob = BlobmanBetaFeature.getBlobman()
    if not blob or not blob:FindFirstChild("VehicleSeat") then return false end
    local seat = blob.VehicleSeat
    local hum = BlobmanBetaFeature.getLocalHum()
    if hum and not hum.Sit then
        seat:Sit(hum)
        task.wait(0.6)
    end
    BlobmanBetaFeature.resetBlobmanPhysics()
    BlobmanBetaFeature.stabilizeBlobman()
    return BlobmanBetaFeature.isSittingOnBlobman()
end
 
function BlobmanBetaFeature.blobGrab(blob, target, side)
    if not blob then return end
    local detector = blob:FindFirstChild(side .. "Detector")
    if not detector then return end
    local weld = detector:FindFirstChild(side .. "Weld")
    if not weld then return end
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript", true)
    if not script then return end
    local remote = script:FindFirstChild("CreatureGrab")
    if remote then
        pcall(function()
            remote:FireServer(detector, target, weld)
        end)
    end
end
 
function BlobmanBetaFeature.blobDrop(blob, target, side)
    if not blob then return end
    local detector = blob:FindFirstChild(side .. "Detector")
    if not detector then return end
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript", true)
    if not script then return end
    local remote = script:FindFirstChild("CreatureDrop")
    if remote then
        pcall(function()
            remote:FireServer(detector, target)
        end)
    end
end
 
function BlobmanBetaFeature.blobKick(blob, target, side)
    BlobmanBetaFeature.blobGrab(blob, BlobmanBetaFeature.getLocalRoot(), side)
    task.wait(0.08)
    BlobmanBetaFeature.SetNetworkOwner(target)
    task.wait(0.08)
    target.CFrame = target.CFrame + Vector3.new(0, Settings.BlobmanBeta.FloatAmount, 0)
    task.wait(0.08)
    BlobmanBetaFeature.ungrab(target)
    task.wait(0.08)
    BlobmanBetaFeature.blobGrab(blob, target, side)
    task.wait(0.08)
    BlobmanBetaFeature.blobDrop(blob, target, side)
    task.wait(0.08)
    BlobmanBetaFeature.ungrab(target)
end
 
function BlobmanBetaFeature.ensureBlob()
    local blob = BlobmanBetaFeature.getBlobman()
    if not blob then blob = BlobmanBetaFeature.spawnBlobman() end
    local hum = BlobmanBetaFeature.getLocalHum()
    if hum and not hum.Sit then
        blob.VehicleSeat:Sit(hum)
        task.wait(0.22)
    end
    return BlobmanBetaFeature.getBlobman()
end
 
function BlobmanBetaFeature.GetPlayerList()
    local list = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then table.insert(list, plr.Name) end
    end
    return list
end
 
function BlobmanBetaFeature.checkTarget()
    if not Settings.BlobmanBeta.selectedTarget then
        OrionLib:MakeNotification({Name = "Error", Content = "Select target", Time = 3})
        return false
    end
    local target = Players:FindFirstChild(Settings.BlobmanBeta.selectedTarget)
    if not target then
        OrionLib:MakeNotification({Name = "Error", Content = "Target not found", Time = 3})
        return false
    end
    return target
end
 
-- ============================================
-- PvPタブ機能
-- ============================================
local PvPFeature = {}
 
function PvPFeature.UpdateCharacter(NewCharacter)
    if not (NewCharacter and NewCharacter:IsDescendantOf(workspace)) then return end
 
    State.Character = NewCharacter
    State.Humanoid = State.Character:WaitForChild("Humanoid", 3)
    State.Root = State.Character:WaitForChild("HumanoidRootPart", 3)
 
    local CurrentReach = LocalPlayer:FindFirstChild("CurrentReach")
    if CurrentReach then
        State.ReachDistance = CurrentReach.Value
    end
 
    State.HRPs[State.Root] = nil
end
 
function PvPFeature.IsPlayerTarget(HRP)
    if not HRP or not HRP.Parent then return false end
 
    local Model = HRP.Parent
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character == Model and player ~= LocalPlayer then
            return true
        end
    end
    return false
end
 
function PvPFeature.GetTarget()
    if not Settings.PvP.SilentAimEnabled then return nil end
    if not State.CameraClone or not State.Root then return nil end
 
    local Center = Camera.ViewportSize / 2
    local Distance = math.huge
    local Target = nil
 
    local MaxScreenDistance = (Camera.ViewportSize.X + Camera.ViewportSize.Y) / 2
    local AdjustedStrength = (Settings.PvP.AimStrength / 100) * MaxScreenDistance
 
    for HRP in pairs(State.HRPs) do
        if HRP and HRP.Parent and HRP ~= State.Root and HRP:IsDescendantOf(workspace) then
            local Model = HRP.Parent
            local TargetHumanoid = Model:FindFirstChildWhichIsA("Humanoid")
            if TargetHumanoid and TargetHumanoid.Health > 0 then
                local Screen3D, OnScreen = Camera:WorldToScreenPoint(HRP.Position)
 
                if OnScreen then
                    local Magnitude = (HRP.Position - State.CameraClone.CFrame.Position).Magnitude
 
                    if Magnitude <= State.ReachDistance then
                        local Screen2D = Vector2.new(Screen3D.X, Screen3D.Y)
                        local ScreenDistance = (Screen2D - Center).Magnitude
 
                        if ScreenDistance <= AdjustedStrength then
                            if ScreenDistance < Distance then
                                Distance = ScreenDistance
                                Target = HRP
                            end
                        end
                    end
                end
            end
        end
    end
 
    return Target
end
 
function PvPFeature.GetAutoGrabTarget()
    if not Settings.PvP.AutoGrabEnabled then return nil end
    if not Camera or not State.Root then return nil end
 
    local Center = Camera.ViewportSize / 2
    local Distance = math.huge
    local Target = nil
 
    local MaxScreenDistance = (Camera.ViewportSize.X + Camera.ViewportSize.Y) / 2
    local AdjustedStrength = (Settings.PvP.AimStrength / 100) * MaxScreenDistance
 
    for HRP in pairs(State.HRPs) do
        if HRP and HRP.Parent and HRP ~= State.Root and HRP:IsDescendantOf(workspace) then
            if PvPFeature.IsPlayerTarget(HRP) then
                local Model = HRP.Parent
                local TargetHumanoid = Model:FindFirstChildWhichIsA("Humanoid")
 
                if TargetHumanoid and TargetHumanoid.Health > 0 then
                    if HRP == State.LastGrabbedTarget and tick() - State.LastGrabbedTime < 0.5 then
                        continue
                    end
 
                    local Screen3D, OnScreen = Camera:WorldToScreenPoint(HRP.Position)
 
                    if OnScreen then
                        local Magnitude = (HRP.Position - Camera.CFrame.Position).Magnitude
 
                        if Magnitude <= State.ReachDistance then
                            local Screen2D = Vector2.new(Screen3D.X, Screen3D.Y)
                            local ScreenDistance = (Screen2D - Center).Magnitude
 
                            if ScreenDistance <= AdjustedStrength * 0.9 then
                                if ScreenDistance < Distance then
                                    Distance = ScreenDistance
                                    Target = HRP
                                end
                            end
                        end
                    end
                end
            end
        end
    end
 
    return Target
end
 
-- ============================================
-- プレイヤータブ機能
-- ============================================
local PlayerFeature = {}
 
function PlayerFeature.enableWalkspeed()
    if State.connections.walkspeed then State.connections.walkspeed:Disconnect() end
    State.connections.walkspeed = RunService.Stepped:Connect(function()
        local char = Utility.GetPlayerCharacter()
        if char then
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            if hrp and humanoid then
                hrp.CFrame = hrp.CFrame + humanoid.MoveDirection * (16 * Settings.Player.WalkspeedValue / 10)
            end
        end
    end)
end
 
function PlayerFeature.disableWalkspeed()
    if State.connections.walkspeed then
        State.connections.walkspeed:Disconnect()
        State.connections.walkspeed = nil
    end
end
 
function PlayerFeature.saveOriginalJumpSettings()
    local char = Utility.GetPlayerCharacter()
    if char then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            State.originalJump.UseJumpPower = humanoid.UseJumpPower
            if humanoid.UseJumpPower then
                State.originalJump.JumpPower = humanoid.JumpPower
            else
                State.originalJump.JumpHeight = humanoid.JumpHeight
            end
        end
    end
end
 
function PlayerFeature.restoreOriginalJumpSettings()
    local char = Utility.GetPlayerCharacter()
    if char then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            if State.originalJump.UseJumpPower ~= nil then
                humanoid.UseJumpPower = State.originalJump.UseJumpPower
            end
            if State.originalJump.JumpPower then
                humanoid.JumpPower = State.originalJump.JumpPower
            end
            if State.originalJump.JumpHeight then
                humanoid.JumpHeight = State.originalJump.JumpHeight
            end
        end
    end
end
 
function PlayerFeature.enableInfiniteJump()
    PlayerFeature.saveOriginalJumpSettings()
    if State.connections.infiniteJump then State.connections.infiniteJump:Disconnect() end
    State.connections.infiniteJump = UserInputService.JumpRequest:Connect(function()
        local char = Utility.GetPlayerCharacter()
        if char then
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid:ChangeState(Enum.HumanoidStateType.Freefall)
                task.wait()
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                if not humanoid.UseJumpPower then
                    humanoid.JumpHeight = math.clamp(Settings.Player.InfiniteJumpPower / 10, 7.2, 50)
                else
                    humanoid.JumpPower = Settings.Player.InfiniteJumpPower
                end
            end
        end
    end)
end
 
function PlayerFeature.disableInfiniteJump()
    if State.connections.infiniteJump then
        State.connections.infiniteJump:Disconnect()
        State.connections.infiniteJump = nil
    end
    PlayerFeature.restoreOriginalJumpSettings()
end
 
-- ============================================
-- Loopタブ機能（KillAll/BringAll/TargetKill統合）
-- ============================================
local LoopFeature = {}
 
-- plot内かどうか（character.Parent が workspace 直下でなければplot内）
function LoopFeature.isPlayerInPlot(player)
    local char = player.Character
    if not char then return false end
    return char.Parent ~= Workspace
end
 
-- InPlot ValueObject による旧来チェック（BringAll用）
function LoopFeature.IsPlayerInsideSafeZone(player)
    return player:FindFirstChild("InPlot") and player.InPlot.Value
end
 
function LoopFeature.CheckPlayer(player)
    if player == LocalPlayer then return false end
    if not player.Character then return false end
    local hum = player.Character:FindFirstChildOfClass("Humanoid")
    if not hum or hum.Health <= 0 then return false end
    return true
end
 
function LoopFeature.CheckPlayerVelocity(player)
    local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    return root and root.Velocity.Magnitude or 0
end
 
-- BringAll/旧KillAll用チェック
function LoopFeature.CheckPlayerBring(player)
    return LoopFeature.CheckPlayer(player)
        and not LoopFeature.IsPlayerInsideSafeZone(player)
        and LoopFeature.CheckPlayerVelocity(player) < 20
end
 
function LoopFeature.isWhitelisted(player)
    if not Settings.Loop.WhitelistFriends then return false end
    return LocalPlayer:IsFriendsWith(player.UserId)
end
 
-- BringAll/旧KillAll用：Follow カメラ
function LoopFeature.setupFreezePart()
    if not State.freezePart then
        State.freezePart = Instance.new("Part")
        State.freezePart.Anchored = true
        State.freezePart.CanCollide = false
        State.freezePart.Transparency = 1
        State.freezePart.Size = Vector3.new(1, 1, 1)
        State.freezePart.Parent = Workspace
    end
end
 
function LoopFeature.FreezeCam(cframe)
    LoopFeature.setupFreezePart()
    State.freezePart.CFrame = cframe
    Workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
    Workspace.CurrentCamera.CameraSubject = State.freezePart
end
 
function LoopFeature.unFreezeCam()
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then Workspace.CurrentCamera.CameraSubject = hum end
    Workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
end
 
-- KillAll（新版）用：自由回転カメラアンカー
function LoopFeature.fixCameraAtCurrentPosition()
    if State.cameraAnchor then return end
    local root = Utility.GetPlayerRootPart()
    if not root then return end
    local camera = Workspace.CurrentCamera
 
    State.cameraAnchor = Instance.new("Part")
    State.cameraAnchor.Name = "CameraAnchor_KillAll"
    State.cameraAnchor.Anchored = true
    State.cameraAnchor.CanCollide = false
    State.cameraAnchor.Transparency = 1
    State.cameraAnchor.Size = Vector3.new(1, 1, 1)
    State.cameraAnchor.CFrame = CFrame.new(root.Position + Vector3.new(0, 20, 0))
    State.cameraAnchor.Parent = Workspace
 
    State.originalCameraSubject = camera.CameraSubject
    camera.CameraSubject = State.cameraAnchor
end
 
function LoopFeature.restoreCamera()
    if State.cameraAnchor then
        local camera = Workspace.CurrentCamera
        if camera and State.originalCameraSubject then
            camera.CameraSubject = State.originalCameraSubject
        end
        State.cameraAnchor:Destroy()
        State.cameraAnchor = nil
        State.originalCameraSubject = nil
    end
end
 
-- Noclip（浮遊）
function LoopFeature.startFloating()
    if Settings.Loop.floatConnection then return end
    Settings.Loop.floatConnection = RunService.Stepped:Connect(function()
        if (getgenv().BringAll or getgenv().KillAll) and LocalPlayer.Character then
            for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = false end
            end
        end
    end)
end
 
function LoopFeature.stopFloating()
    if Settings.Loop.floatConnection then
        Settings.Loop.floatConnection:Disconnect()
        Settings.Loop.floatConnection = nil
    end
end
 
-- ネットワークオーナー系（BringAll/旧KillAll用）
function LoopFeature.SNOWshipOnce(part)
    if not part then return false end
    if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
        return true
    end
    local setOwner = grabEventsFolder:FindFirstChild("SetNetworkOwner")
    local root = Utility.GetPlayerRootPart()
    if setOwner and root then
        setOwner:FireServer(part, CFrame.lookAt(root.Position, part.Position))
    end
    for _ = 1, 5 do
        task.wait()
        if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
            return true
        end
    end
    return false
end
 
function LoopFeature.CheckNetworkOwnerShipOnPlayer(player)
    local head = player.Character and player.Character:FindFirstChild("Head")
    return head and head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name
end
 
function LoopFeature.CreateBringBody(targetPart, dest)
    local bp = targetPart:FindFirstChild("BringBody")
    if not bp then
        bp = Instance.new("BodyPosition")
        bp.Name = "BringBody"
        bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bp.D = 5000
        bp.P = 1500000
        bp.Parent = targetPart
    end
    bp.Position = typeof(dest) == "CFrame" and dest.Position or dest
end
 
-- Kill（新版 Advanced Kill All で使用）
function LoopFeature.killPlayer(player)
    local success, err = pcall(function()
        local char = player.Character
        if not char then return end
        local root = char:FindFirstChild("HumanoidRootPart")
        local hum = char:FindFirstChildOfClass("Humanoid")
        if not root or not hum or hum.Health <= 0 then return end
 
        grabEventsFolder.SetNetworkOwner:FireServer(root, root.CFrame)
        task.wait(0.1)
        grabEventsFolder.DestroyGrabLine:FireServer(root)
        task.wait(0.1)
        char:BreakJoints()
    end)
    if not success then warn("killPlayer error:", err) return false end
    return true
end
 
-- テレポート（新版 KillAll / TargetKill 用）
function LoopFeature.teleportToPlayer(targetPlayer)
    local now = tick()
    if now - Settings.Loop.LastTeleportTime < Settings.Loop.TeleportCooldown then return false end
 
    local localChar = LocalPlayer.Character
    if not localChar then return false end
    local localRoot = localChar:FindFirstChild("HumanoidRootPart")
    local targetChar = targetPlayer.Character
    if not localRoot or not targetChar then return false end
    local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
    if not targetRoot then return false end
 
    if not Settings.Loop.OriginalPosition then
        Settings.Loop.OriginalPosition = localRoot.Position
    end
 
    localRoot.CFrame = CFrame.new(targetRoot.Position + Vector3.new(0, Settings.Loop.TeleportHeight, 0))
    Settings.Loop.LastTeleportTime = now
    return true
end
 
function LoopFeature.returnToOriginalPosition()
    if not Settings.Loop.OriginalPosition then return end
    local localChar = LocalPlayer.Character
    if not localChar then return end
    local root = localChar:FindFirstChild("HumanoidRootPart")
    if root then root.CFrame = CFrame.new(Settings.Loop.OriginalPosition) end
end
 
-- リスポーン位置ソート（TargetKill用）
function LoopFeature.getRespawnPosition()
    local spawn = Workspace:FindFirstChild("SpawnLocation")
    if spawn then return spawn.Position end
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("SpawnLocation") then return obj.Position end
    end
    return Vector3.new(0, 50, 0)
end
 
function LoopFeature.sortPlayersByRespawnDistance(players)
    local respawnPos = LoopFeature.getRespawnPosition()
    local list = {}
    for _, player in ipairs(players) do
        local char = player.Character
        if char then
            local root = char:FindFirstChild("HumanoidRootPart")
            if root then
                table.insert(list, { player = player, distance = (root.Position - respawnPos).Magnitude })
            end
        end
    end
    table.sort(list, function(a, b) return a.distance < b.distance end)
    local sorted = {}
    for _, data in ipairs(list) do table.insert(sorted, data.player) end
    return sorted
end
 
-- Bring All ループ（元のスクリプト）
function LoopFeature.startBringAll()
    local playerCFrame = Utility.GetPlayerCFrame()
    if not playerCFrame then return end
 
    local camCFrame = CFrame.lookAt(
        Workspace.CurrentCamera.CFrame.Position + Vector3.new(-15, 15, 0),
        playerCFrame.Position
    )
    Workspace.CurrentCamera.CFrame = camCFrame
 
    while getgenv().BringAll do
        LoopFeature.FreezeCam(camCFrame)
 
        for _, player in ipairs(Players:GetPlayers()) do
            if LoopFeature.CheckPlayerBring(player) then
                local root = player.Character:FindFirstChild("HumanoidRootPart")
                local hum = player.Character:FindFirstChildOfClass("Humanoid")
                local ragdolled = hum and hum:FindFirstChild("Ragdolled")
                if root and hum and ragdolled then
                    for _ = 1, 50 do
                        if not getgenv().BringAll then break end
                        LoopFeature.startFloating()
                        LoopFeature.SNOWshipOnce(root)
 
                        if LoopFeature.CheckNetworkOwnerShipOnPlayer(player) then
                            if not ragdolled.Value
                                and player:DistanceFromCharacter(playerCFrame.Position) > 10 then
                                root.CFrame = playerCFrame
                            end
                            LoopFeature.CreateBringBody(root, playerCFrame)
                            break
                        end
 
                        task.wait()
                        if root.Position.Y <= -12 then
                            Utility.GetPlayerRootPart().CFrame = CFrame.new(root.Position + Vector3.new(0, 5, -15))
                        else
                            Utility.GetPlayerRootPart().CFrame = CFrame.new(root.Position + Vector3.new(0, -10, -10))
                        end
                    end
                end
            end
        end
 
        Utility.GetPlayerRootPart().CFrame = CFrame.new(527, 123, -376)
        task.wait()
    end
 
    LoopFeature.unFreezeCam()
    LoopFeature.stopFloating()
    Utility.GetPlayerRootPart().CFrame = playerCFrame
end
 
-- Kill All ループ（Advanced版：近い順テレポートキル）
function LoopFeature.startKillAll()
    if Settings.Loop.killAllLoop then task.cancel(Settings.Loop.killAllLoop) Settings.Loop.killAllLoop = nil end
 
    Settings.Loop.killAllLoop = task.spawn(function()
        local root = Utility.GetPlayerRootPart()
        if root then
            Settings.Loop.OriginalPosition = root.Position
            LoopFeature.fixCameraAtCurrentPosition()
        end
 
        local charAddedConn
        charAddedConn = LocalPlayer.CharacterAdded:Connect(function()
            if Settings.Loop.killAllEnabled then
                task.wait(0.5)
                if Settings.Loop.killAllEnabled then LoopFeature.fixCameraAtCurrentPosition() end
            end
        end)
 
        while Settings.Loop.killAllEnabled do
            local lp = LocalPlayer
            local targetPlayers = {}
 
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= lp
                    and not Settings.Loop.ProcessedPlayers[player.UserId]
                    and not LoopFeature.isWhitelisted(player)
                    and not LoopFeature.isPlayerInPlot(player) then
                    table.insert(targetPlayers, player)
                end
            end
 
            if #targetPlayers == 0 then
                Settings.Loop.ProcessedPlayers = {}
                Settings.Loop.LastTeleportTime = 0
                LoopFeature.returnToOriginalPosition()
                continue
            end
 
            local localChar = lp.Character
            if not localChar then task.wait(0.5) continue end
            local localRoot = localChar:FindFirstChild("HumanoidRootPart")
            if not localRoot then task.wait(0.5) continue end
 
            -- 自分からの距離が近い順にソート
            local withDist = {}
            for _, player in ipairs(targetPlayers) do
                local char = player.Character
                if char then
                    local r = char:FindFirstChild("HumanoidRootPart")
                    if r then
                        table.insert(withDist, {
                            player = player,
                            distance = (r.Position - localRoot.Position).Magnitude
                        })
                    end
                end
            end
            table.sort(withDist, function(a, b) return a.distance < b.distance end)
 
            for _, data in ipairs(withDist) do
                if not Settings.Loop.killAllEnabled then break end
                if LoopFeature.teleportToPlayer(data.player) then
                    task.wait(0.3)
                    local ok = LoopFeature.killPlayer(data.player)
                    if ok then
                        Settings.Loop.ProcessedPlayers[data.player.UserId] = true
                    end
                    task.wait(0.3)
                else
                    task.wait(0.15)
                end
            end
        end
 
        if charAddedConn then charAddedConn:Disconnect() end
        LoopFeature.returnToOriginalPosition()
        Settings.Loop.OriginalPosition = nil
        Settings.Loop.ProcessedPlayers = {}
        LoopFeature.restoreCamera()
    end)
end
 
-- Target Kill ループ（指定プレイヤーのみ、距離制限なし）
function LoopFeature.startTargetKill()
    if Settings.Loop.targetKillLoop then task.cancel(Settings.Loop.targetKillLoop) Settings.Loop.targetKillLoop = nil end
 
    Settings.Loop.targetKillLoop = task.spawn(function()
        while Settings.Loop.targetKillEnabled do
            local now = tick()
            local validTargets = {}
 
            for userId, _ in pairs(Settings.Loop.TargetPlayers) do
                local player = Players:GetPlayerByUserId(userId)
                if player and player.Parent then
                    if LoopFeature.isPlayerInPlot(player) then continue end
                    local char = player.Character
                    if char then
                        local hum = char:FindFirstChildOfClass("Humanoid")
                        if hum and hum.Health > 0 then
                            table.insert(validTargets, player)
                        end
                    end
                else
                    Settings.Loop.TargetPlayers[userId] = nil
                end
            end
 
            if #validTargets == 0 then task.wait(1) continue end
 
            validTargets = LoopFeature.sortPlayersByRespawnDistance(validTargets)
            local target = validTargets[1]
 
            if now - Settings.Loop.LastTargetKillTime < 1.5 then
                task.wait(0.1)
                continue
            end
 
            if not Settings.Loop.OriginalPosition then
                local char = LocalPlayer.Character
                if char then
                    local r = char:FindFirstChild("HumanoidRootPart")
                    if r then Settings.Loop.OriginalPosition = r.Position end
                end
            end
 
            if LoopFeature.teleportToPlayer(target) then
                task.wait(0.3)
                local ok = LoopFeature.killPlayer(target)
                if ok then
                    task.wait(0.3)
                    LoopFeature.returnToOriginalPosition()
                    Settings.Loop.OriginalPosition = nil
                    Settings.Loop.LastTeleportTime = 0
                end
            end
 
            Settings.Loop.LastTargetKillTime = tick()
            task.wait(0.1)
        end
 
        LoopFeature.returnToOriginalPosition()
        Settings.Loop.OriginalPosition = nil
    end)
end
 
-- プレイヤーリスト更新
function LoopFeature.updatePlayerDropdown()
    local list = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(list, player.Name)
        end
    end
    return list
end
 
function LoopFeature.getSelectedTargetsDisplay()
    local names = {}
    for userId, _ in pairs(Settings.Loop.TargetPlayers) do
        local p = Players:GetPlayerByUserId(userId)
        if p then table.insert(names, p.Name) end
    end
    return #names > 0 and table.concat(names, ", ") or "None"
end
 
-- ============================================
-- Visuals機能
-- ============================================
local VisualsFeature = {}
 
function VisualsFeature.updateESP()
    for i = #State.ESPObjects, 1, -1 do
        local highlight = State.ESPObjects[i]
        if not Settings.Visuals.ESPEnabled or not highlight or not highlight.Parent then
            pcall(function()
                highlight:Destroy()
            end)
            table.remove(State.ESPObjects, i)
        else
            highlight.FillColor = Settings.Visuals.ESPFillColor
            highlight.OutlineColor = Settings.Visuals.ESPOutlineColor
            highlight.FillTransparency = Settings.Visuals.ESPFillTransparency
            highlight.OutlineTransparency = Settings.Visuals.ESPOutlineTransparency
        end
    end
 
    for i = #State.HitboxObjects, 1, -1 do
        local hitbox = State.HitboxObjects[i]
        if not Settings.Visuals.ShowHitbox or not hitbox or not hitbox.Parent then
            pcall(function()
                hitbox:Destroy()
            end)
            table.remove(State.HitboxObjects, i)
        else
            hitbox.Color3 = Settings.Visuals.HitboxColor
        end
    end
 
    for i = #State.NameLabels, 1, -1 do
        local label = State.NameLabels[i]
        if not Settings.Visuals.ShowPlayerInfo or not label or not label.Parent then
            pcall(function()
                label:Destroy()
            end)
            table.remove(State.NameLabels, i)
        end
    end
end
 
function VisualsFeature.createHitbox(character)
    if character:FindFirstChild("__esp_hitbox") then return end
 
    local hrp = character:FindFirstChild("HumanoidRootPart")
    local head = character:FindFirstChild("Head")
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    local leftLeg = character:FindFirstChild("Left Leg") or character:FindFirstChild("LeftFoot")
    local rightLeg = character:FindFirstChild("Right Leg") or character:FindFirstChild("RightFoot")
 
    if not hrp or not head then return end
 
    local topY = head.Position.Y + (head.Size.Y / 2)
    local bottomY = hrp.Position.Y - (hrp.Size.Y / 2)
 
    if leftLeg then
        local legBottom = leftLeg.Position.Y - (leftLeg.Size.Y / 2)
        bottomY = math.min(bottomY, legBottom)
    end
 
    if rightLeg then
        local legBottom = rightLeg.Position.Y - (rightLeg.Size.Y / 2)
        bottomY = math.min(bottomY, legBottom)
    end
 
    if humanoid and humanoid.HipHeight then
        bottomY = bottomY - humanoid.HipHeight
    end
 
    local totalHeight = topY - bottomY
    local centerY = (topY + bottomY) / 2
    local offset = Vector3.new(0, centerY - hrp.Position.Y, 0)
 
    local box = Instance.new("BoxHandleAdornment")
    box.Name = "__esp_hitbox"
    box.Size = Vector3.new(hrp.Size.X * 1.5, totalHeight, hrp.Size.Z * 1.5)
    box.CFrame = CFrame.new(offset)
    box.Color3 = Settings.Visuals.HitboxColor
    box.Transparency = 0.7
    box.AlwaysOnTop = true
    box.ZIndex = 1
    box.Adornee = hrp
    box.Parent = character
 
    table.insert(State.HitboxObjects, box)
end
 
function VisualsFeature.createNameLabel(character, player)
    if character:FindFirstChild("__esp_namelabel") then return end
 
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
 
    local billboardGui = Instance.new("BillboardGui")
    billboardGui.Name = "__esp_namelabel"
    billboardGui.Adornee = hrp
    billboardGui.Size = UDim2.new(0, 200, 0, 50)
    billboardGui.StudsOffset = Vector3.new(0, 3, 0)
    billboardGui.AlwaysOnTop = true
    billboardGui.Parent = character
 
    local iconFrame = Instance.new("Frame")
    iconFrame.Size = UDim2.new(0, 40, 0, 40)
    iconFrame.Position = UDim2.new(0.5, -70, 0, 0)
    iconFrame.BackgroundTransparency = 1
    iconFrame.Parent = billboardGui
 
    local iconImage = Instance.new("ImageLabel")
    iconImage.Size = UDim2.new(1, 0, 1, 0)
    iconImage.BackgroundTransparency = 1
    iconImage.Image = Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
    iconImage.Parent = iconFrame
 
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0)
    corner.Parent = iconImage
 
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Size = UDim2.new(0, 150, 0, 40)
    nameLabel.Position = UDim2.new(0.5, -30, 0, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = player.DisplayName .. " (@" .. player.Name .. ")"
    nameLabel.TextColor3 = Color3.new(1, 1, 1)
    nameLabel.TextStrokeTransparency = 0
    nameLabel.TextScaled = true
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.Parent = billboardGui
 
    table.insert(State.NameLabels, billboardGui)
end
 
function VisualsFeature.addESP(character)
    if character:FindFirstChild("__esp_highlight") then return end
 
    local highlight = Instance.new("Highlight")
    highlight.Name = "__esp_highlight"
    highlight.FillColor = Settings.Visuals.ESPFillColor
    highlight.OutlineColor = Settings.Visuals.ESPOutlineColor
    highlight.FillTransparency = Settings.Visuals.ESPFillTransparency
    highlight.OutlineTransparency = Settings.Visuals.ESPOutlineTransparency
    highlight.Parent = character
 
    table.insert(State.ESPObjects, highlight)
end
 
function VisualsFeature.enableESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            VisualsFeature.addESP(player.Character)
            if Settings.Visuals.ShowHitbox then
                VisualsFeature.createHitbox(player.Character)
            end
            if Settings.Visuals.ShowPlayerInfo then
                VisualsFeature.createNameLabel(player.Character, player)
            end
        end
    end
end
 
function VisualsFeature.disableESP()
    for _, highlight in ipairs(State.ESPObjects) do
        pcall(function()
            highlight:Destroy()
        end)
    end
    State.ESPObjects = {}
 
    for _, hitbox in ipairs(State.HitboxObjects) do
        pcall(function()
            hitbox:Destroy()
        end)
    end
    State.HitboxObjects = {}
 
    for _, label in ipairs(State.NameLabels) do
        pcall(function()
            label:Destroy()
        end)
    end
    State.NameLabels = {}
end
 
function VisualsFeature.enableHitbox()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            VisualsFeature.createHitbox(player.Character)
        end
    end
end
 
function VisualsFeature.disableHitbox()
    for _, hitbox in ipairs(State.HitboxObjects) do
        pcall(function()
            hitbox:Destroy()
        end)
    end
    State.HitboxObjects = {}
end
 
function VisualsFeature.enableNameLabels()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            VisualsFeature.createNameLabel(player.Character, player)
        end
    end
end
 
function VisualsFeature.disableNameLabels()
    for _, label in ipairs(State.NameLabels) do
        pcall(function()
            label:Destroy()
        end)
    end
    State.NameLabels = {}
end
 
-- Spectate機能
function VisualsFeature.getLocalHum()
    if not LocalPlayer.Character then return end
    return LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
end
 
function VisualsFeature.spectatePlayer(playerName)
    if playerName == "" or playerName == LocalPlayer.Name then
        Workspace.CurrentCamera.CameraSubject = VisualsFeature.getLocalHum()
        Settings.Visuals.SpectatePlayer = nil
        return
    end
 
    local targetPlayer = nil
    local lowerName = playerName:lower()
 
    for _, player in ipairs(Players:GetPlayers()) do
        if player.DisplayName:lower():sub(1, #lowerName) == lowerName or 
           player.Name:lower():sub(1, #lowerName) == lowerName then
            targetPlayer = player
            break
        end
    end
 
    if targetPlayer and targetPlayer.Character then
        local humanoid = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            Workspace.CurrentCamera.CameraSubject = humanoid
            Settings.Visuals.SpectatePlayer = targetPlayer
        end
    end
end
 
function VisualsFeature.getPlayerNames()
    local names = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(names, player.Name)
    end
    return names
end
 
-- ============================================
-- Auraタブ機能
-- ============================================
local AuraFeature = {}
 
function AuraFeature.UpdateAuras(dt)
    State.timers.AuraTimer = State.timers.AuraTimer + dt
 
    if State.timers.AuraTimer >= 0.5 then
        local root = Utility.GetPlayerRootPart()
        if not root then 
            State.timers.AuraTimer = 0
            return 
        end
 
        for _, v in ipairs(AuraHelper.GetNearParts(root.Position, Settings.Aura.AuraRadius)) do
            if not v.Anchored and 
               not v:IsDescendantOf(Utility.GetPlayerCharacter()) and 
               (v.Name == "HumanoidRootPart" or v.Name == "Torso" or v.Name == "Head") then
 
                local p = Players:GetPlayerFromCharacter(v.Parent)
 
                if AuraHelper.IsFriend(p) and Settings.Aura.IgnoreFriendAura then 
                    continue 
                end
 
                if Settings.Aura.VoidAura then
                    task.spawn(function()
                        FTAP.SetNetworkOwner(v)
                        v.CanCollide = false
                        AuraHelper.Velocity(v, Vector3.new(0, 1e4, 0))
                    end)
                end
 
                if Settings.Aura.RagdollAura then
                    task.spawn(function()
                        local pos = v.CFrame
                        FTAP.SetNetworkOwner(v)
                        AuraHelper.Velocity(v, Vector3.new(0, -256, 0))
                        task.wait(0.1)
                        AuraHelper.MoveTo(v, pos)
                        AuraHelper.Velocity(v, Vector3.zero)
                    end)
                end
 
                if Settings.Aura.DeathAura then
                    task.spawn(function()
                        FTAP.SetNetworkOwner(v)
                        task.wait(0.1)
                        local humanoid = v.Parent:FindFirstChildOfClass("Humanoid")
                        if humanoid then
                            humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                        end
                        task.wait(0.5)
                        FTAP.DestroyGrabLine(v)
                    end)
                end
 
                if Settings.Aura.FireAura then
                    task.spawn(function()
                        local campfire = AuraHelper.SpawnToy("Campfire", root.CFrame)
                        if campfire then
                            local soundPart = campfire:FindFirstChild("SoundPart")
                            if soundPart then
                                FTAP.SetNetworkOwner(soundPart)
                                task.wait(0.1)
                                soundPart.CFrame = v.CFrame
                                task.delay(0.5, function()
                                    pcall(function()
                                        DeleteToyRE:FireServer(campfire)
                                    end)
                                end)
                            end
                        end
                    end)
                end
            end
        end
 
        State.timers.AuraTimer = 0
    end
end
 
-- ============================================
-- Lineタブ機能（Blitz統合）
-- ============================================
local LineFeature = {}
 
-- Get CharacterAndBeamMove script environment for Further Extend
local playerScriptsLine = LocalPlayer:WaitForChild("PlayerScripts")
local characterBeamMoveScript = playerScriptsLine:WaitForChild("CharacterAndBeamMove")
local senv = getsenv and getsenv(characterBeamMoveScript) or getfenv(characterBeamMoveScript)
local minDistance = 3
 
-- Enhanced GrabParts monitoring
Workspace.ChildAdded:Connect(function(model)
    if model.Name == "GrabParts" and Settings.Line.ultragrabbb then
        local dragPart = model:FindFirstChild("DragPart")
        if dragPart then
            local alignOrientation = dragPart:FindFirstChild("AlignOrientation")
            if alignOrientation then
                alignOrientation.Responsiveness = 200
                alignOrientation.MaxTorque = math.huge
            end
 
            local alignPosition = dragPart:FindFirstChild("AlignPosition")
            if alignPosition then
                alignPosition.MaxAxesForce = Vector3.new(math.huge, math.huge, math.huge)
                alignPosition.MaxForce = math.huge
                alignPosition.Responsiveness = 200
            end
        end
    end
 
    if model.Name == "GrabParts" and Settings.Line.InvisibleLine then
        CreateGrabLine:FireServer()
    end
end)
 
-- Line colors array
local lineColors = {
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0),
    Color3.new(1, 0, 0)
}
 
-- Further Extend GUI Setup (Always active, no toggle needed)
function LineFeature.IsMobile()
    if LocalPlayer.PlayerGui:FindFirstChild("ContextActionGui") then
        return true
    end
    return false
end
 
local guiLine = Instance.new("ScreenGui")
guiLine.Name = "FurtherExtendGUI"
guiLine.ResetOnSpawn = false
guiLine.Parent = LocalPlayer.PlayerGui
 
-- Increase button
local imageButton = Instance.new("ImageButton")
imageButton.Name = "IncreaseButton"
imageButton.Size = UDim2.new(0, 50, 0, 50)
imageButton.Position = UDim2.new(0.5, 60, 0.5, -25)
imageButton.BackgroundTransparency = 1
imageButton.ImageTransparency = 0.2
imageButton.Visible = false
imageButton.ImageColor3 = Color3.fromRGB(142, 142, 142)
imageButton.Parent = guiLine
 
local imageLabel = Instance.new("ImageLabel")
imageLabel.Size = UDim2.new(1, 0, 1, 0)
imageLabel.Image = "rbxassetid://9603827111"
imageLabel.BackgroundTransparency = 1
imageLabel.Parent = imageButton
 
-- Decrease button
local imageButtonDe = Instance.new("ImageButton")
imageButtonDe.Name = "DecreaseButton"
imageButtonDe.Size = UDim2.new(0, 50, 0, 50)
imageButtonDe.Position = UDim2.new(0.5, -110, 0.5, -25)
imageButtonDe.BackgroundTransparency = 1
imageButtonDe.ImageTransparency = 0.2
imageButtonDe.Visible = false
imageButtonDe.ImageColor3 = Color3.fromRGB(142, 142, 142)
imageButtonDe.Parent = guiLine
 
local imageLabelDe = Instance.new("ImageLabel")
imageLabelDe.Size = UDim2.new(1, 0, 1, 0)
imageLabelDe.Image = "rbxassetid://9603826756"
imageLabelDe.BackgroundTransparency = 1
imageLabelDe.Parent = imageButtonDe
 
-- Further Extend functions
function LineFeature.buttonClicked()
    if senv and senv.distance then
        senv.distance = (senv.distance or 0) + Settings.Line.IncreaseLineExtend
        if senv.distance < minDistance then
            senv.distance = minDistance
        end
    end
end
 
function LineFeature.buttonClickedDE()
    if senv and senv.distance then
        senv.distance = (senv.distance or 0) - Settings.Line.IncreaseLineExtend
        if senv.distance < minDistance then
            senv.distance = minDistance
        end
    end
end
 
function LineFeature.toggleButtonState(isExtended)
    if isExtended then
        imageButton.Visible = true
        imageButton.Active = true
        imageButtonDe.Visible = true
        imageButtonDe.Active = true
    else
        imageButton.Visible = false
        imageButton.Active = false
        imageButtonDe.Visible = false
        imageButtonDe.Active = false
    end
end
 
function LineFeature.toggleDefaultExtendButtons(isVisible)
    local controlsGui = LocalPlayer.PlayerGui:FindFirstChild("ControlsGui")
    if controlsGui then
        local decreaseButton = controlsGui:FindFirstChild("DecreaseLineExtendButton")
        local increaseButton = controlsGui:FindFirstChild("IncreaseLineExtendButton")
        if decreaseButton then
            decreaseButton.Visible = isVisible
        end
        if increaseButton then
            increaseButton.Visible = isVisible
        end
    end
end
 
-- Connect buttons (only for mobile)
imageButton.MouseButton1Click:Connect(LineFeature.buttonClicked)
imageButtonDe.MouseButton1Click:Connect(LineFeature.buttonClickedDE)
 
-- Mouse wheel control for PC (Always active)
UserInputService.InputChanged:Connect(function(input, gameProcessed)
    if not gameProcessed then
        if input.UserInputType == Enum.UserInputType.MouseWheel then
            if input.Position.Z > 0 then
                -- Scroll up - increase distance
                LineFeature.buttonClicked()
            elseif input.Position.Z < 0 then
                -- Scroll down - decrease distance
                LineFeature.buttonClickedDE()
            end
        end
    end
end)
 
-- Monitor Holding state (only show buttons for mobile users)
LocalPlayer:WaitForChild("IsHeld").Changed:Connect(function(isHolding)
    if isHolding then
        if LineFeature.IsMobile() then
            LineFeature.toggleDefaultExtendButtons(false)
            LineFeature.toggleButtonState(true)
        end
    else
        if LineFeature.IsMobile() then
            LineFeature.toggleDefaultExtendButtons(true)
        end
        LineFeature.toggleButtonState(false)
    end
end)
 
-- Rainbow effect function
local function updateRainbowLine()
    spawn(function()
        local hue = 0
        while Settings.Line.RainbowLine do
            hue = (hue + (Settings.Line.LineSpeed * 0.01)) % 1
            local color = Color3.fromHSV(hue, 1, 1)
 
            for i = 1, #lineColors do
                if i == 1 then
                    lineColors[i] = ColorSequence.new(color)
                else
                    lineColors[i] = color
                end
            end
            updateLineColorsEvent:FireServer(unpack(lineColors))
            task.wait(0.05)
        end
    end)
end
 
-- Flashing effect function
local function updateFlashingLine()
    spawn(function()
        local visible = true
        while Settings.Line.FlashingLine do
            if visible then
                for i = 1, #lineColors do
                    if i == 1 then
                        lineColors[i] = ColorSequence.new(Settings.Line.LineColorChangeValue)
                    else
                        lineColors[i] = Settings.Line.LineColorChangeValue
                    end
                end
            else
                for i = 1, #lineColors do
                    if i == 1 then
                        lineColors[i] = ColorSequence.new(Color3.new(0, 0, 0))
                    else
                        lineColors[i] = Color3.new(0, 0, 0)
                    end
                end
            end
            updateLineColorsEvent:FireServer(unpack(lineColors))
            visible = not visible
            task.wait(Settings.Line.StrobeSpeed)
        end
    end)
end
 
-- Gradient effect function
local function updateGradientLine()
    spawn(function()
        while Settings.Line.GradientLine do
            local primaryColor = Settings.Line.LineColorChangeValue
            local secondaryColor = Color3.new(
                1 - primaryColor.R,
                1 - primaryColor.G,
                1 - primaryColor.B
            )
 
            for i = 1, #lineColors do
                local t = (i - 1) / (#lineColors - 1)
                local blendedColor = primaryColor:Lerp(secondaryColor, t)
 
                if i == 1 then
                    lineColors[i] = ColorSequence.new(blendedColor)
                else
                    lineColors[i] = blendedColor
                end
            end
            updateLineColorsEvent:FireServer(unpack(lineColors))
            task.wait(0.1)
        end
    end)
end
 
-- Pulse effect function
local function updatePulseEffect()
    spawn(function()
        local intensity = 0
        local increasing = true
        while Settings.Line.PulseEffect do
            if increasing then
                intensity = intensity + 0.05
                if intensity >= 1 then
                    increasing = false
                end
            else
                intensity = intensity - 0.05
                if intensity <= 0 then
                    increasing = true
                end
            end
 
            local pulsedColor = Color3.new(
                Settings.Line.LineColorChangeValue.R * intensity,
                Settings.Line.LineColorChangeValue.G * intensity,
                Settings.Line.LineColorChangeValue.B * intensity
            )
 
            for i = 1, #lineColors do
                if i == 1 then
                    lineColors[i] = ColorSequence.new(pulsedColor)
                else
                    lineColors[i] = pulsedColor
                end
            end
            updateLineColorsEvent:FireServer(unpack(lineColors))
            task.wait(0.05)
        end
    end)
end
 
-- ============================================
-- ToyMod Wings 機能（統合）
-- ============================================
local ToyModFeature = {}
 
local Plots = {
    {Name = "Plot1", Center = Vector3.new(-581.7, -6.4, 89.1), Radius = 53.51},
    {Name = "Plot2", Center = Vector3.new(-461.3, -7.4, -141.6), Radius = 71.29},
    {Name = "Plot3", Center = Vector3.new(305.5, -4.5, 489.0), Radius = 74.07},
    {Name = "Plot4", Center = Vector3.new(534.7, 83.5, -370.3), Radius = 51.06},
    {Name = "Plot5", Center = Vector3.new(588.4, 124.3, -93.9), Radius = 54.64}
}
 
function ToyModFeature.GetToys()
    local toys = {}
    local playerName = LocalPlayer.Name
 
    local spawned = workspace:FindFirstChild(playerName .. "SpawnedInToys")
    if spawned then
        for _, toy in pairs(spawned:GetChildren()) do
            if toy.Name ~= "ToyNumber" then
                table.insert(toys, toy)
            end
        end
    end
 
    if #toys == 0 then
        local toysFolder = workspace:FindFirstChild("Toys")
        if toysFolder then
            for _, toy in pairs(toysFolder:GetChildren()) do
                if toy:IsA("Model") or toy:IsA("BasePart") then
                    table.insert(toys, toy)
                end
            end
        end
    end
 
    if #toys == 0 then
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:IsA("Model") and obj ~= LocalPlayer.Character then
                local isPart = obj:FindFirstChildWhichIsA("BasePart")
                if isPart and obj.Name ~= "Baseplate" and obj.Name ~= "Terrain" then
                    local size = obj:GetExtentsSize()
                    if size.Magnitude > 0.5 and size.Magnitude < 50 then
                        table.insert(toys, obj)
                    end
                end
            end
        end
    end
 
    if Settings.ToyMod.UsePlotItems then
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return toys end
 
        local playerPos = hrp.Position
        local myPlot = nil
 
        for _, plotInfo in ipairs(Plots) do
            local distXZ = (Vector2.new(playerPos.X, playerPos.Z) - Vector2.new(plotInfo.Center.X, plotInfo.Center.Z)).Magnitude
            if distXZ <= plotInfo.Radius then
                myPlot = plotInfo
                break
            end
        end
 
        if myPlot then
            local plotItems = workspace:FindFirstChild("PlotItems")
            if plotItems then
                local plotFolder = plotItems:FindFirstChild(myPlot.Name)
                if plotFolder then
                    for _, obj in pairs(plotFolder:GetDescendants()) do
                        if (obj:IsA("BasePart") or obj:IsA("Model")) and obj.Name ~= "ToysLimitNum" then
                            local size = obj:IsA("BasePart") and obj.Size or (obj:IsA("Model") and obj:GetExtentsSize())
                            if size and size.Magnitude > 1 and size.Magnitude < 20 then
                                table.insert(toys, obj)
                            end
                        end
                    end
                end
            end
        end
    end
 
    return toys
end
 
function ToyModFeature.CreateWings()
    if State.ToyModWings.Connection then 
        State.ToyModWings.Connection:Disconnect() 
        State.ToyModWings.Connection = nil 
    end
 
    for _, v in pairs(State.ToyModWings.BodyPositions) do 
        if v and v.Parent then v:Destroy() end 
    end
 
    for _, v in pairs(State.ToyModWings.AlignOrientations) do 
        if v and v.Parent then v:Destroy() end 
    end
 
    State.ToyModWings.Parts = {}
    State.ToyModWings.BodyPositions = {}
    State.ToyModWings.AlignOrientations = {}
    State.ToyModWings.OriginalAnchored = {}
    State.ToyModWings.FlapTime = 0
    State.ToyModWings.TargetCFrames = {}
 
    local toys = ToyModFeature.GetToys()
    print("Found " .. #toys .. " toys")
 
    if #toys == 0 then 
        print("No toys found! Please select toys first or enable 家のアイテム使用")
        return 
    end
 
    for i = 1, #toys do
        local toy = toys[i]
        local part = toy:IsA("BasePart") and toy or toy:FindFirstChildWhichIsA("BasePart")
        if not part then continue end
 
        local partsToProcess = toy:IsA("Model") and toy:GetDescendants() or {part}
        for _, p in pairs(partsToProcess) do
            if p:IsA("BasePart") then
                table.insert(State.ToyModWings.OriginalAnchored, {Part = p, Anchored = p.Anchored})
                if p.Anchored then
                    p.Anchored = false
                end
                p.CanCollide = false
            end
        end
 
        local bp = Instance.new("BodyPosition")
        bp.MaxForce = Vector3.new(5e6, 5e6, 5e6)
        bp.P = 15000
        bp.D = 200
        bp.Position = part.Position
        bp.Parent = part
        table.insert(State.ToyModWings.BodyPositions, bp)
 
        local ao = Instance.new("AlignOrientation")
        ao.MaxTorque = 400000
        ao.Responsiveness = 2000
        ao.Mode = Enum.OrientationAlignmentMode.OneAttachment
        ao.Parent = part
        local att = Instance.new("Attachment", part)
        ao.Attachment0 = att
        table.insert(State.ToyModWings.AlignOrientations, ao)
        table.insert(State.ToyModWings.Parts, part)
        table.insert(State.ToyModWings.TargetCFrames, part.CFrame)
    end
 
    State.ToyModWings.Connection = RunService.Heartbeat:Connect(function()
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local root = hrp.CFrame
 
        local currentPos = hrp.Position
        local playerVelocity = hrp.AssemblyLinearVelocity.Magnitude
 
        if not State.ToyModWings.LastPos then
            State.ToyModWings.LastPos = currentPos
        end
 
        local cframeVelocity = (currentPos - State.ToyModWings.LastPos).Magnitude / (1 / 60)
        State.ToyModWings.LastPos = currentPos
 
        playerVelocity = math.max(playerVelocity, cframeVelocity)
 
        local speedMultiplier = 1
        if Settings.ToyMod.SpeedLinkFlap then
            speedMultiplier = 0.7 + (playerVelocity / 20)
        end
 
        if Settings.ToyMod.FlapEnabled then
            State.ToyModWings.FlapTime = State.ToyModWings.FlapTime + Settings.ToyMod.FlapSpeed * speedMultiplier * 0.016
        end
 
        local total = #State.ToyModWings.Parts
        local center = (total + 1) / 2
 
        for i, part in ipairs(State.ToyModWings.Parts) do
            local offsetFromCenter = i - center
            local side = math.sign(offsetFromCenter)
            local dist = math.abs(offsetFromCenter)
 
            local xOff = side * (dist + 0.5) * Settings.ToyMod.XSpacing
 
            local flap = 0
            local yMove = 0
            local zRot = 0
 
            if Settings.ToyMod.FlapEnabled then
                local time = State.ToyModWings.FlapTime
                local mul = (dist + 1) / (math.max(center, 1) + 0.5)
 
                -- 左右で位相を同じ方向に（同期しやすく）
                local phaseOffset = dist * 0.08  -- sideの符号を使わず、常に正の遅れ
 
                if Settings.ToyMod.FlapType == 1 then
                    flap = math.sin(time + phaseOffset)
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.2) * mul
                    zRot = flap * Settings.ToyMod.FlapRange * mul
 
                elseif Settings.ToyMod.FlapType == 2 then
                    flap = math.sin(time + phaseOffset * 1.5) * 0.8 + math.sin(time * 0.5 + phaseOffset * 0.8) * 0.2
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.15) * mul
                    zRot = math.sin(time * 1.2 + phaseOffset * 1.2) * Settings.ToyMod.FlapRange * 0.7 * mul
 
                elseif Settings.ToyMod.FlapType == 3 then
                    flap = math.sin(time * 0.5 + phaseOffset * 0.4) * 0.6 + math.sin(time * 0.3 + phaseOffset * 0.6) * 0.4
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.3) * mul
                    zRot = math.sin(time * 0.8 + phaseOffset * 0.5) * (Settings.ToyMod.FlapRange * 0.3) * mul
 
                elseif Settings.ToyMod.FlapType == 4 then
                    flap = math.sin(time * 2 + phaseOffset * 2) * 0.7
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.25) * mul
                    zRot = math.sin(time * 2.5 + phaseOffset * 2.2) * Settings.ToyMod.FlapRange * 0.8 * mul
 
                elseif Settings.ToyMod.FlapType == 5 then
                    flap = math.sin(time * 0.8 + phaseOffset) * 0.6
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.3) * mul
                    zRot = (time * 80 + dist * 30) % 360
 
                elseif Settings.ToyMod.FlapType == 6 then
                    flap = math.sin(time * 0.3 + phaseOffset * 3) * 0.9
                    yMove = (flap + 0.5) * (Settings.ToyMod.FlapRange * 0.2) * mul
                    zRot = math.sin(time * 0.4 + phaseOffset * 1.5) * Settings.ToyMod.FlapRange * 0.6 * mul
 
                elseif Settings.ToyMod.FlapType == 7 then
                    flap = math.cos(time * 1.5 + phaseOffset * 1.5) * 0.8
                    yMove = math.sin(time * 0.6 + phaseOffset) * (Settings.ToyMod.FlapRange * 0.15) * mul
                    zRot = flap * Settings.ToyMod.FlapRange * 1.2 * mul
 
                elseif Settings.ToyMod.FlapType == 8 then
                    local pulse = math.floor(time * 2 + phaseOffset) % 2
                    flap = pulse == 0 and 1 or -1
                    yMove = flap * (Settings.ToyMod.FlapRange * 0.2) * mul
                    zRot = flap * Settings.ToyMod.FlapRange * 0.7 * mul
 
                elseif Settings.ToyMod.FlapType == 9 then
                    local cycleFrac = ((time + phaseOffset) % 4) / 4
                    if cycleFrac < 0.3 then
                        yMove = (0.3 - cycleFrac) / 0.3 * Settings.ToyMod.FlapRange * 0.4 * mul
                    else
                        yMove = -((cycleFrac - 0.3) / 0.7) * Settings.ToyMod.FlapRange * 0.4 * mul
                    end
                    zRot = math.sin(time * 1.5 + phaseOffset) * Settings.ToyMod.FlapRange * 0.5 * mul
 
                elseif Settings.ToyMod.FlapType == 10 then
                    flap = math.sin(time * 0.7 + phaseOffset * 4) * 0.8
                    yMove = math.sin(time * 0.5 + phaseOffset * 2) * (Settings.ToyMod.FlapRange * 0.25) * mul
                    zRot = (math.sin(time * 1.1 + phaseOffset * 3) + math.sin(time * 0.5 + phaseOffset * 1)) * Settings.ToyMod.FlapRange * 0.5 * mul
 
                elseif Settings.ToyMod.FlapType == 11 then
                    local waveA = math.sin(time * 0.6 + phaseOffset * 0.6) * 0.7
                    local waveB = math.sin(time * 0.3 + dist * 0.5) * 0.3
                    yMove = (waveA + waveB) * (Settings.ToyMod.FlapRange * 0.35) * mul
                    zRot = (waveA * 1.2 + waveB * 0.5) * Settings.ToyMod.FlapRange * mul
 
                elseif Settings.ToyMod.FlapType == 12 then
                    local rot = time * 120 + dist * 40
                    zRot = rot % 360
                    yMove = math.sin(time * 1.5 + phaseOffset * 1.5) * (Settings.ToyMod.FlapRange * 0.25) * mul
                end
            end
 
            -- 左右両方同じ角度（オリジナル準拠）
            local appliedAngleH = Settings.ToyMod.AngleH
 
            local targetCFrame = root
                * CFrame.new(xOff, Settings.ToyMod.YOffset + yMove, Settings.ToyMod.ZOffset)
                * CFrame.Angles(
                    math.rad(Settings.ToyMod.AngleV),
                    math.rad(appliedAngleH),
                    math.rad(zRot)
                )
 
            State.ToyModWings.TargetCFrames[i] = targetCFrame
            State.ToyModWings.BodyPositions[i].Position = targetCFrame.Position
            State.ToyModWings.AlignOrientations[i].CFrame = targetCFrame
        end
    end)
end
 
function ToyModFeature.DestroyWings()
    if State.ToyModWings.Connection then 
        State.ToyModWings.Connection:Disconnect() 
        State.ToyModWings.Connection = nil 
    end
 
    for _, v in pairs(State.ToyModWings.BodyPositions) do 
        if v and v.Parent then v:Destroy() end 
    end
 
    for _, v in pairs(State.ToyModWings.AlignOrientations) do 
        if v and v.Parent then v:Destroy() end 
    end
 
    for _, info in pairs(State.ToyModWings.OriginalAnchored) do
        if info.Part and info.Part.Parent then
            info.Part.Anchored = info.Anchored
        end
    end
 
    State.ToyModWings.Parts = {}
    State.ToyModWings.BodyPositions = {}
    State.ToyModWings.AlignOrientations = {}
    State.ToyModWings.OriginalAnchored = {}
    State.ToyModWings.FlapTime = 0
    State.ToyModWings.TargetCFrames = {}
end
 
-- ============================================
-- Train Control機能
-- ============================================
local TrainFeature = {}
 
function TrainFeature.getMoveVector()
    return CM:GetMoveVector()
end
 
function TrainFeature.getToyFolder()
    return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
end
 
function TrainFeature.getToyInFolder()
    local folder = TrainFeature.getToyFolder()
    if folder then
        return folder:FindFirstChild(Settings.TrainControl.ToyName)
    end
    return nil
end
 
function TrainFeature.spawnToy(targetCFrame)
    local menu = ReplicatedStorage:FindFirstChild("MenuToys")
    local spawnRF = menu and menu:FindFirstChild("SpawnToyRemoteFunction")
    if not spawnRF then return nil end
    local ok = pcall(function()
        spawnRF:InvokeServer(Settings.TrainControl.ToyName, targetCFrame, Vector3.new(0, 0, 0))
    end)
    if not ok then return nil end
    local start = tick()
    while tick() - start < Settings.TrainControl.SpawnTimeout do
        local toy = TrainFeature.getToyInFolder()
        if toy then return toy end
        task.wait(0.01)
    end
    return nil
end
 
function TrainFeature.getHRP()
    local char = LocalPlayer.Character
    return char and (char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso"))
end
 
function TrainFeature.forceSpawnToyAtHRP()
    local hrp = TrainFeature.getHRP()
    if not hrp then return nil end
    local folder = TrainFeature.getToyFolder()
    if folder then
        local old = folder:FindFirstChild(Settings.TrainControl.ToyName)
        if old then
            pcall(function() old:Destroy() end)
        end
    end
    return TrainFeature.spawnToy(hrp.CFrame * CFrame.new(Settings.TrainControl.HeadOffset))
end
 
function TrainFeature.ensureToy()
    local toy = TrainFeature.getToyInFolder()
    if toy and toy.Parent then return toy end
    return TrainFeature.forceSpawnToyAtHRP()
end
 
-- 列車の全座席を取得
function TrainFeature.getAllTrainSeats()
    local seats = {}
    local map = Workspace:FindFirstChild("Map")
    local ahto = map and map:FindFirstChild("AlwaysHereTweenedObjects")
    local train = ahto and ahto:FindFirstChild("Train")
    local obj = train and train:FindFirstChild("Object")
    local objModel = obj and obj:FindFirstChild("ObjectModel")
 
    if objModel then
        for _, descendant in ipairs(objModel:GetDescendants()) do
            if descendant:IsA("Seat") or descendant:IsA("VehicleSeat") then
                table.insert(seats, descendant)
            end
        end
    end
 
    return seats
end
 
function TrainFeature.getFirstTrainSeat()
    local seats = TrainFeature.getAllTrainSeats()
    return seats[1]
end
 
function TrainFeature.getTrainModel()
    local map = Workspace:FindFirstChild("Map")
    local ahto = map and map:FindFirstChild("AlwaysHereTweenedObjects")
    local train = ahto and ahto:FindFirstChild("Train")
    local obj = train and train:FindFirstChild("Object")
    return obj and obj:FindFirstChild("ObjectModel")
end
 
function TrainFeature.isTrainSeat(seatPart)
    if not seatPart then return false end
    local trainModel = TrainFeature.getTrainModel()
    if not trainModel then return false end
 
    return seatPart:IsDescendantOf(trainModel)
end
 
function TrainFeature.sitTrain()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    local seat = TrainFeature.getFirstTrainSeat()
    if hum and seat then
        pcall(function()
            seat:Sit(hum)
        end)
    end
end
 
function TrainFeature.getHum()
    local c = LocalPlayer.Character
    return c and c:FindFirstChildOfClass("Humanoid")
end
 
function TrainFeature.enableCameraNoClip()
    if Settings.TrainControl.prevOcclusionMode == nil then
        Settings.TrainControl.prevOcclusionMode = LocalPlayer.DevCameraOcclusionMode
    end
    LocalPlayer.DevCameraOcclusionMode = Enum.DevCameraOcclusionMode.Invisicam
end
 
function TrainFeature.restoreCameraNoClip()
    if Settings.TrainControl.prevOcclusionMode ~= nil then
        LocalPlayer.DevCameraOcclusionMode = Settings.TrainControl.prevOcclusionMode
        Settings.TrainControl.prevOcclusionMode = nil
    end
end
 
function TrainFeature.getAllPlayerEggs()
    local eggs = {}
    for _, player in ipairs(Players:GetPlayers()) do
        local egg = Workspace:FindFirstChild(player.Name)
        if egg then
            table.insert(eggs, egg)
        end
    end
    return eggs
end
 
function TrainFeature.startLoop()
    if Settings.TrainControl.LoopThread then return end
    Settings.TrainControl.LoopThread = task.spawn(function()
        while Settings.TrainControl.Running and not TrainFeature.getHRP() do
            task.wait(0.01)
        end
        if not Settings.TrainControl.Running then
            Settings.TrainControl.LoopThread = nil
            return
        end
 
        while Settings.TrainControl.Running do
            local cycleStart = tick()
 
            local hrp = TrainFeature.getHRP()
            local eggs = TrainFeature.getAllPlayerEggs()
 
            if hrp and #eggs > 0 then
                for _, egg in ipairs(eggs) do
                    local toy = TrainFeature.ensureToy()
                    if toy and toy.Parent then
                        local menu = ReplicatedStorage:FindFirstChild("MenuToys")
                        local holdFn = (toy:FindFirstChild("HoldPart") and toy.HoldPart:FindFirstChild("HoldItemRemoteFunction")) or (menu and menu:FindFirstChild("HoldItemRemoteFunction"))
                        if holdFn then
                            pcall(function()
                                holdFn:InvokeServer(toy, egg)
                            end)
                        end
 
                        task.wait(0.03)
 
                        local dropFn = (toy:FindFirstChild("HoldPart") and toy.HoldPart:FindFirstChild("DropItemRemoteFunction")) or (menu and menu:FindFirstChild("DropItemRemoteFunction"))
                        if dropFn then
                            pcall(function()
                                dropFn:InvokeServer(toy, hrp.CFrame * CFrame.new(Settings.TrainControl.HeadOffset), Vector3.new(0, -6.55, 0))
                            end)
                        end
                    end
                end
            end
 
            local elapsed = tick() - cycleStart
            local remain = Settings.TrainControl.Interval - elapsed
            task.wait(remain > 0 and remain or 0.01)
        end
 
        Settings.TrainControl.LoopThread = nil
    end)
end
 
function TrainFeature.stopLoop()
    Settings.TrainControl.Running = false
end
 
function TrainFeature.restoreNoClipState(modelRef, state)
    if modelRef then
        for part, st in pairs(state) do
            if part and part.Parent and typeof(st) == "table" then
                pcall(function()
                    part.CanCollide = st.CanCollide
                    part.CanQuery = st.CanQuery
                    part.CanTouch = st.CanTouch
                end)
            end
        end
    end
end
 
function TrainFeature.restoreVehicleNoClip()
    TrainFeature.restoreNoClipState(Settings.TrainControl.NoClipVehicleModel, Settings.TrainControl.NoClipVehicleState)
    Settings.TrainControl.NoClipVehicleModel = nil
    Settings.TrainControl.NoClipVehicleState = {}
end
 
function TrainFeature.restoreCharNoClip()
    TrainFeature.restoreNoClipState(Settings.TrainControl.NoClipCharModel, Settings.TrainControl.NoClipCharState)
    Settings.TrainControl.NoClipCharModel = nil
    Settings.TrainControl.NoClipCharState = {}
end
 
function TrainFeature.applyNoClipToModel(model, stateTable)
    if not model then return end
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then
            if not stateTable[d] then
                stateTable[d] = {CanCollide = d.CanCollide, CanQuery = d.CanQuery, CanTouch = d.CanTouch}
            end
            d.CanCollide = false
            d.CanQuery = false
            d.CanTouch = false
        end
    end
end
 
-- ネットワークオーナーシップ
function TrainFeature.claimNetworkOwnershipSingle(part)
    if not part then return end
    pcall(function()
        part:SetNetworkOwner(LocalPlayer)
    end)
end
 
function TrainFeature.claimNetworkOwnershipModel(model)
    if not model then return end
 
    pcall(function()
        for _, descendant in ipairs(model:GetDescendants()) do
            if descendant:IsA("BasePart") then
                pcall(function()
                    descendant:SetNetworkOwner(LocalPlayer)
                end)
            end
        end
    end)
end
 
function TrainFeature.claimNetworkOwnership(part)
    if not part then return end
 
    TrainFeature.claimNetworkOwnershipSingle(part)
 
    local model = part:FindFirstAncestorOfClass("Model")
    if model then
        TrainFeature.claimNetworkOwnershipModel(model)
    end
end
 
function TrainFeature.startOwnershipThread(threadId, targetPart)
    if Settings.TrainControl.OwnershipThreads[threadId] then return end
 
    Settings.TrainControl.OwnershipThreads[threadId] = task.spawn(function()
        while Settings.TrainControl.Enabled and targetPart and targetPart.Parent do
            TrainFeature.claimNetworkOwnership(targetPart)
            task.wait(Settings.TrainControl.NetworkOwnershipInterval)
        end
        Settings.TrainControl.OwnershipThreads[threadId] = nil
    end)
end
 
function TrainFeature.stopAllOwnershipThreads()
    for id, thread in pairs(Settings.TrainControl.OwnershipThreads) do
        if thread then
            pcall(function()
                task.cancel(thread)
            end)
        end
    end
    Settings.TrainControl.OwnershipThreads = {}
end
 
function TrainFeature.destroyVFly()
    if Settings.TrainControl.BV then 
        pcall(function() Settings.TrainControl.BV:Destroy() end)
        Settings.TrainControl.BV = nil 
    end
    if Settings.TrainControl.BG then 
        pcall(function() Settings.TrainControl.BG:Destroy() end)
        Settings.TrainControl.BG = nil 
    end
    Settings.TrainControl.TargetPart = nil
    Settings.TrainControl.VFlyEnabled = false
    Settings.TrainControl.LastNetworkOwnership = 0
    TrainFeature.stopAllOwnershipThreads()
end
 
function TrainFeature.getTrainRootPart()
    local trainModel = TrainFeature.getTrainModel()
    if not trainModel then return nil end
 
    if trainModel.PrimaryPart then
        return trainModel.PrimaryPart
    end
 
    for _, child in ipairs(trainModel:GetDescendants()) do
        if child:IsA("BasePart") and child.Size.Magnitude > 5 then
            return child
        end
    end
 
    return nil
end
 
function TrainFeature.startVFlyForTrain()
    TrainFeature.destroyVFly()
    local part = TrainFeature.getTrainRootPart()
    if not part then 
        return 
    end
 
    TrainFeature.claimNetworkOwnership(part)
 
    Settings.TrainControl.BV = Instance.new("BodyVelocity")
    Settings.TrainControl.BV.MaxForce = Vector3.new(1e9, 1e9, 1e9)
    Settings.TrainControl.BV.P = Settings.TrainControl.BV_P
    Settings.TrainControl.BV.Velocity = Vector3.zero
    Settings.TrainControl.BV.Parent = part
 
    Settings.TrainControl.BG = Instance.new("BodyGyro")
    Settings.TrainControl.BG.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
    Settings.TrainControl.BG.P = Settings.TrainControl.BG_P
    Settings.TrainControl.BG.CFrame = part.CFrame
    Settings.TrainControl.BG.Parent = part
 
    Settings.TrainControl.TargetPart = part
    Settings.TrainControl.VFlyEnabled = true
    Settings.TrainControl.LastNetworkOwnership = tick()
 
    for i = 1, Settings.TrainControl.ParallelOwnershipThreads do
        TrainFeature.startOwnershipThread(i, part)
    end
end
 
function TrainFeature.hardStopAll()
    Settings.TrainControl.Enabled = false
    Settings.TrainControl.TrainEnableAt = 0
    Settings.TrainControl.LastSitAttempt = 0
    Settings.TrainControl.HadSeatOnce = false
 
    TrainFeature.destroyVFly()
    TrainFeature.stopLoop()
    TrainFeature.restoreCameraNoClip()
    Settings.TrainControl.NoClip = false
    TrainFeature.stopAllOwnershipThreads()
end
 
-- ============================================
-- Miscタブ機能
-- ============================================
local MiscFeature = {}
 
function MiscFeature.enableThirdPerson()
    Settings.Misc.ThirdPerson = true
 
    LocalPlayer.CameraMode = Enum.CameraMode.Classic
    LocalPlayer.CameraMinZoomDistance = 0.5
    LocalPlayer.CameraMaxZoomDistance = 400
 
    if State.connections.thirdPerson then State.connections.thirdPerson:Disconnect() end
 
    State.connections.thirdPerson = RunService.Stepped:Connect(function()
        if not Settings.Misc.ThirdPerson then return end
 
        local currentTime = tick()
        if currentTime - State.timers.LastUpdate < State.timers.UPDATE_INTERVAL then
            return
        end
        State.timers.LastUpdate = currentTime
 
        local cam = Workspace.CurrentCamera
        local root = Utility.GetPlayerRootPart()
 
        if cam and root then
            local distance = (cam.CFrame.Position - root.Position).Magnitude
            if distance < 2 then
                local newPos = root.Position - (cam.CFrame.LookVector * 5)
                cam.CFrame = CFrame.new(newPos, root.Position)
            end
        end
    end)
end
 
function MiscFeature.disableThirdPerson()
    Settings.Misc.ThirdPerson = false
 
    if State.connections.thirdPerson then
        State.connections.thirdPerson:Disconnect()
        State.connections.thirdPerson = nil
    end
 
    LocalPlayer.CameraMode = Enum.CameraMode.Classic
    LocalPlayer.CameraMinZoomDistance = 0.5
    LocalPlayer.CameraMaxZoomDistance = 0.5
end
 
function MiscFeature.enableFPSBooster()
    Settings.Misc.FPSBooster = true
 
    local lighting = Lighting
    local terrain = Workspace.Terrain
 
    if not State.originalSettings then
        State.originalSettings = {
            GlobalShadows = lighting.GlobalShadows,
            Brightness = lighting.Brightness,
            WaterTransparency = terrain.WaterTransparency,
            WaterWaveSize = terrain.WaterWaveSize,
            WaterWaveSpeed = terrain.WaterWaveSpeed
        }
    end
 
    lighting.GlobalShadows = false
    lighting.Brightness = 0
 
    terrain.WaterTransparency = 1
    terrain.WaterWaveSize = 0
    terrain.WaterWaveSpeed = 0
 
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
            obj.Enabled = false
        end
 
        if obj:IsA("Decal") then
            local isSpawnMarker = false
            local parent = obj.Parent
            if parent and parent.Name == "RespawnLocation" then
                isSpawnMarker = true
            end
 
            if not isSpawnMarker then
                if not obj:GetAttribute("OriginalTexture") then
                    obj:SetAttribute("OriginalTexture", obj.Texture)
                end
                obj.Texture = "rbxasset://textures/face.png"
            end
        end
 
        if obj:IsA("Texture") then
            if not obj:GetAttribute("OriginalTexture") then
                obj:SetAttribute("OriginalTexture", obj.Texture)
            end
            obj.Texture = "rbxasset://textures/face.png"
        end
 
        if obj:IsA("MeshPart") or obj:IsA("UnionOperation") or obj:IsA("Part") then
            if not obj:GetAttribute("OriginalMaterial") then
                obj:SetAttribute("OriginalMaterial", obj.Material.Name)
                obj:SetAttribute("OriginalReflectance", obj.Reflectance)
            end
            obj.Material = Enum.Material.SmoothPlastic
            obj.Reflectance = 0
        end
    end
 
    if State.connections.fpsBooster then State.connections.fpsBooster:Disconnect() end
 
    State.connections.fpsBooster = Workspace.DescendantAdded:Connect(function(obj)
        if not Settings.Misc.FPSBooster then return end
 
        if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
            obj.Enabled = false
        end
 
        if obj:IsA("Decal") then
            local isSpawnMarker = false
            local parent = obj.Parent
            if parent and parent.Name == "RespawnLocation" then
                isSpawnMarker = true
            end
 
            if not isSpawnMarker then
                if not obj:GetAttribute("OriginalTexture") then
                    obj:SetAttribute("OriginalTexture", obj.Texture)
                end
                obj.Texture = "rbxasset://textures/face.png"
            end
        end
 
        if obj:IsA("Texture") then
            if not obj:GetAttribute("OriginalTexture") then
                obj:SetAttribute("OriginalTexture", obj.Texture)
            end
            obj.Texture = "rbxasset://textures/face.png"
        end
 
        if obj:IsA("MeshPart") or obj:IsA("UnionOperation") or obj:IsA("Part") then
            if not obj:GetAttribute("OriginalMaterial") then
                obj:SetAttribute("OriginalMaterial", obj.Material.Name)
                obj:SetAttribute("OriginalReflectance", obj.Reflectance)
            end
            obj.Material = Enum.Material.SmoothPlastic
            obj.Reflectance = 0
        end
    end)
end
 
function MiscFeature.disableFPSBooster()
    Settings.Misc.FPSBooster = false
 
    if State.connections.fpsBooster then
        State.connections.fpsBooster:Disconnect()
        State.connections.fpsBooster = nil
    end
 
    if State.originalSettings then
        local lighting = Lighting
        local terrain = Workspace.Terrain
 
        lighting.GlobalShadows = State.originalSettings.GlobalShadows
        lighting.Brightness = State.originalSettings.Brightness
 
        terrain.WaterTransparency = State.originalSettings.WaterTransparency
        terrain.WaterWaveSize = State.originalSettings.WaterWaveSize
        terrain.WaterWaveSpeed = State.originalSettings.WaterWaveSpeed
 
        for _, obj in pairs(Workspace:GetDescendants()) do
            if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
                obj.Enabled = true
            end
 
            if obj:IsA("Decal") and obj:GetAttribute("OriginalTexture") then
                obj.Texture = obj:GetAttribute("OriginalTexture")
            end
 
            if obj:IsA("Texture") and obj:GetAttribute("OriginalTexture") then
                obj.Texture = obj:GetAttribute("OriginalTexture")
            end
 
            if (obj:IsA("MeshPart") or obj:IsA("UnionOperation") or obj:IsA("Part")) and obj:GetAttribute("OriginalMaterial") then
                obj.Material = Enum.Material[obj:GetAttribute("OriginalMaterial")]
                obj.Reflectance = obj:GetAttribute("OriginalReflectance") or 0
            end
        end
 
        State.originalSettings = nil
    end
end
 
-- Barrier Destroyer
function MiscFeature.ExecuteBarrierDestroyer()
    local player = LocalPlayer
    local playerName = player.Name
 
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "Character not found",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    local originalPosition = player.Character.HumanoidRootPart.CFrame
 
    local spawnResult = SpawnToyRF:InvokeServer(
        "InstrumentWoodwindOcarina",
        CFrame.new(184.148834, -5.54824972, 498.136749, 0.829037189, -0.214714944, 0.516328275, 0, 0.923344612, 0.383972496, -0.559193552, -0.318327487, 0.765486956),
        Vector3.new(0, 34, 0)
    )
 
    task.wait(0.3)
 
    local toyFolder = Utility.waitForChild(Workspace, playerName .. "SpawnedInToys", 3)
    if not toyFolder then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "Toy folder not found",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    local ocarina = Utility.waitForChild(toyFolder, "InstrumentWoodwindOcarina", 3)
    if not ocarina then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "Ocarina not found",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    local holdPart = Utility.waitForChild(ocarina, "HoldPart", 2)
    if not holdPart then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "HoldPart not found",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    task.wait(0.1)
 
    holdPart.HoldItemRemoteFunction:InvokeServer(
        ocarina,
        Workspace[playerName]
    )
 
    task.wait(0.3)
 
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "Character disappeared before teleporting",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    player.Character.HumanoidRootPart.CFrame = CFrame.new(304.06, 25.77, 488.54)
    task.wait(0.15)
 
    if ocarina and ocarina.Parent then
        pcall(function()
            DeleteToyRE:FireServer(ocarina)
        end)
    end
 
    task.wait(0.15)
 
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = originalPosition
    end
 
    for _, v in ipairs(Workspace.Plots:GetChildren()) do
        local barrier = v:FindFirstChild("Barrier")
        if barrier then
            for _, p in ipairs(barrier:GetChildren()) do
                if p:IsA("BasePart") then
                    p.CanCollide = false
                end
            end
        end
    end
 
    task.wait(0.5)
 
    OrionLib:MakeNotification({
        Name = "Solaris Hub",
        Content = "Checking barrier...",
        Image = "rbxassetid://4483345998",
        Time = 2
    })
 
    local campfireSpawn = SpawnToyRF:InvokeServer(
        "Campfire",
        player.Character.HumanoidRootPart.CFrame,
        Vector3.zero
    )
 
    task.wait(0.2)
 
    local campfire = Utility.waitForChild(toyFolder, "Campfire", 2)
    if not campfire then
        OrionLib:MakeNotification({
            Name = "error",
            Content = "Failed to spawn campfire",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    local soundPart = campfire:FindFirstChild("SoundPart")
    if not soundPart then
        pcall(function()
            DeleteToyRE:FireServer(campfire)
        end)
        OrionLib:MakeNotification({
            Name = "error",
            Content = "SoundPart not found",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
        return false
    end
 
    FTAP.SetNetworkOwner(soundPart, player.Character.HumanoidRootPart.CFrame)
    soundPart.CFrame = CFrame.new(304, 25, 488)
    task.wait(2)
 
    local success = campfire.Parent ~= nil
 
    if success then
        OrionLib:MakeNotification({
            Name = "success",
            Content = "Barrier destroyed!",
            Image = "rbxassetid://4483345998",
            Time = 5
        })
    else
        OrionLib:MakeNotification({
            Name = "failure",
            Content = "Failed to destroy barrier",
            Image = "rbxassetid://4483345998",
            Time = 3
        })
    end
 
    if campfire and campfire.Parent then
        pcall(function()
            DeleteToyRE:FireServer(campfire)
        end)
    end
 
    return success
end
 
-- ============================================
-- メインループ
-- ============================================
local function MainLoop()
    -- Heartbeatループ
    RunService.Heartbeat:Connect(function(dt)
        State.timers.espTimer = State.timers.espTimer + dt
 
        AuraFeature.UpdateAuras(dt)
 
        if State.timers.espTimer >= 1 then
            VisualsFeature.updateESP()
            if Settings.Visuals.ESPEnabled then
                for _, player in ipairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        VisualsFeature.addESP(player.Character)
                    end
                end
            end
            if Settings.Visuals.ShowHitbox then
                for _, player in ipairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        VisualsFeature.createHitbox(player.Character)
                    end
                end
            end
            if Settings.Visuals.ShowPlayerInfo then
                for _, player in ipairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        VisualsFeature.createNameLabel(player.Character, player)
                    end
                end
            end
            State.timers.espTimer = 0
        end
 
        if Workspace.CurrentCamera then
            Workspace.CurrentCamera.FieldOfView = Settings.Visuals.CameraFOV
        end
    end)
 
    -- AntiBanana ループ
    if Settings.Anti.AntiBanana then
        if State.loops.antiBanana then
            State.loops.antiBanana:Disconnect()
        end
 
        State.loops.antiBanana = RunService.Heartbeat:Connect(function(dt)
            if not Settings.Anti.AntiBanana then return end
 
            State.timers.AntiBananaTimer = State.timers.AntiBananaTimer + dt
            if State.timers.AntiBananaTimer >= 1 then
                local character = LocalPlayer.Character
                if not character then return end
                local rootPart = character:FindFirstChild("HumanoidRootPart")
                if not rootPart then return end
 
                for _, v in ipairs(Workspace:GetChildren()) do
                    if v:IsA("Folder") and v.Name:find("SpawnedInToys") then
                        local playerInv = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        if v ~= playerInv then
                            local banana = v:FindFirstChild("FoodBanana")
                            if banana then
                                for _, part in ipairs(banana:GetDescendants()) do
                                    if part:IsA("BasePart") then
                                        part.CanQuery = false
                                        part.CanTouch = false
 
                                        local distance = (part.Position - rootPart.Position).Magnitude
                                        if distance <= 8 then
                                            pcall(function()
                                                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, rootPart.CFrame)
                                            end)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
                State.timers.AntiBananaTimer = 0
            end
        end)
    else
        if State.loops.antiBanana then
            State.loops.antiBanana:Disconnect()
            State.loops.antiBanana = nil
        end
        State.timers.AntiBananaTimer = 0
    end
end
 
-- ============================================
-- プレイヤーイベント接続
-- ============================================
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(0.5)
        if Settings.Visuals.ESPEnabled then
            VisualsFeature.addESP(character)
        end
        if Settings.Visuals.ShowHitbox then
            VisualsFeature.createHitbox(character)
        end
        if Settings.Visuals.ShowPlayerInfo then
            VisualsFeature.createNameLabel(character, player)
        end
    end)
end)
 
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        player.CharacterAdded:Connect(function(character)
            wait(0.5)
            if Settings.Visuals.ESPEnabled then
                VisualsFeature.addESP(character)
            end
            if Settings.Visuals.ShowHitbox then
                VisualsFeature.createHitbox(character)
            end
            if Settings.Visuals.ShowPlayerInfo then
                VisualsFeature.createNameLabel(character, player)
            end
        end)
    end
end
 
-- ブロブマンベータ用プレイヤーリスト更新
Players.PlayerAdded:Connect(function()
    task.wait(1)
    if TargetDropdown_blob then
        TargetDropdown_blob:Refresh(BlobmanBetaFeature.GetPlayerList(), true)
    end
end)
 
Players.PlayerRemoving:Connect(function()
    task.wait(0.5)
    if TargetDropdown_blob then
        TargetDropdown_blob:Refresh(BlobmanBetaFeature.GetPlayerList(), true)
    end
    if Settings.BlobmanBeta.selectedTarget and not Players:FindFirstChild(Settings.BlobmanBeta.selectedTarget) then
        Settings.BlobmanBeta.selectedTarget = nil
    end
end)
 
-- ============================================
-- キャラクター復帰時処理
-- ============================================
local function reconnect()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:FindFirstChildWhichIsA("Humanoid") or character:WaitForChild("Humanoid")
    local hrp = character:WaitForChild("HumanoidRootPart")
    character:WaitForChild("Head")
    character:WaitForChild("Torso")
    State.IsCharacterInRagdoll = false
 
    local canBurnValue = hrp:WaitForChild("FirePlayerPart"):WaitForChild("CanBurn")
    canBurnValue.Changed:Connect(function(v)
        if v and Settings.Anti.AntiBurn then
            while canBurnValue.Value do
                if firetouchinterest then
                    firetouchinterest(hrp.FirePlayerPart, apagarfogo, 0)
                    task.wait()
                    firetouchinterest(hrp.FirePlayerPart, apagarfogo, 1)
                else
                    apagarfogo.CFrame = hrp.FirePlayerPart.CFrame * CFrame.new(math.random(-1,1), math.random(-1,1), math.random(-1,1))
                    task.wait()
                    apagarfogo.Position = Vector3.new(0, -100, 0)
                end
            end
        end
    end)
 
    humanoid.Changed:Connect(function(prop)
        if prop == "Sit" and humanoid.Sit == true then
            if humanoid.SeatPart == nil and Settings.Anti.AntiGrab then
                humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                humanoid.Sit = false
            end
        end
    end)
 
    if Settings.Player.Walkspeed then PlayerFeature.enableWalkspeed() end
    if Settings.Player.InfiniteJump then PlayerFeature.enableInfiniteJump() end
    if Settings.Anti.AntiKickKunai then
        task.delay(0.8, function()
            if Settings.Anti.AntiKickKunai then AntiFeature.attachKunai(true) end
        end)
    end
    if Settings.Anti.AntiExplosion and character then
        AntiFeature.setupAntiExplosion(character)
    end
 
    GrabFeature.stopNoclip()
end
 
-- ============================================
-- グラブタブイベント接続
-- ============================================
Workspace.ChildAdded:Connect(function(child)
    if child.Name == "GrabParts" then
        task.spawn(function()
            GrabFeature.onGrabPartAdded_ThrowStrength(child)
            GrabFeature.onGrabPartsAdded_FTAP(child)
        end)
    end
end)
 
LocalPlayer.CharacterAdded:Connect(reconnect)
task.spawn(reconnect)
 
-- アンチグラブ v1 初期化
AntiFeature.setupAntiGrabV1()
 
-- PvP初期化
if LocalPlayer.Character then
    PvPFeature.UpdateCharacter(LocalPlayer.Character)
end
LocalPlayer.CharacterAdded:Connect(PvPFeature.UpdateCharacter)
 
-- HRP追跡
workspace.DescendantAdded:Connect(function(Descendant)
    if Descendant:IsA("BasePart") and Descendant.Name == "HumanoidRootPart" then
        if Descendant ~= State.Root then
            State.HRPs[Descendant] = true
        end
    end
end)
 
workspace.DescendantRemoving:Connect(function(Descendant)
    if State.HRPs[Descendant] then
        State.HRPs[Descendant] = nil
        if State.LastGrabbedTarget == Descendant then
            State.LastGrabbedTarget = nil
        end
    end
end)
 
-- 初期HRP収集
for _, Descendant in workspace:GetDescendants() do
    if Descendant:IsA("BasePart") and Descendant.Name == "HumanoidRootPart" then
        if Descendant ~= State.Root then
            State.HRPs[Descendant] = true
        end
    end
end
 
-- GrabParts検知
workspace.ChildAdded:Connect(function(GrabParts)
    if not (GrabParts.Name == "GrabParts" and GrabParts:IsA("Model")) then return end
 
    local GrabPart = GrabParts:WaitForChild("GrabPart", 3)
    local WeldConstraint = (GrabPart) and GrabPart:WaitForChild("WeldConstraint", 3)
    local Target = (WeldConstraint) and WeldConstraint.Part1
    if not (Target and Target:IsDescendantOf(workspace)) then return end
 
    State.GrabbedPart = Target
 
    local Removed
    Removed = GrabParts.AncestryChanged:Connect(function()
        if GrabParts:IsDescendantOf(workspace) then return end
        State.GrabbedPart = nil
 
        if Removed then
            Removed:Disconnect()
            Removed = nil
        end
    end)
end)
 
-- サイレントエイム：メインループ
RunService.RenderStepped:Connect(function()
    if not State.CameraInitialized then
        return
    end
 
    local TargetCFrame = State.CameraClone.CFrame
 
    if Settings.PvP.SilentAimEnabled and not workspace:FindFirstChild("GrabParts") then
        local Target = PvPFeature.GetTarget()
 
        if Target and Target ~= State.Root then
            local Screen3D, OnScreen = Camera:WorldToScreenPoint(Target.Position)
            if OnScreen then
                if TargetCFrame.LookVector:Dot((Target.Position - TargetCFrame.Position).Unit) > 0 then
                    TargetCFrame = CFrame.new(TargetCFrame.Position, Target.Position)
                end
            end
        end
    end
 
    Camera.CFrame = TargetCFrame
end)
 
-- 自動掴み：メインループ
RunService.Heartbeat:Connect(function()
    if not Settings.PvP.AutoGrabEnabled then return end
    if State.GrabbedPart then return end
    if tick() - State.LastGrabTime < State.GrabCooldown then return end
 
    local Target = PvPFeature.GetAutoGrabTarget()
 
    if Target and Target.Parent then
        local Center = Camera.ViewportSize / 2
 
        VirtualUser:ClickButton1(Center, Camera.CFrame)
        State.LastGrabTime = tick()
        State.LastGrabbedTarget = Target
        State.LastGrabbedTime = tick()
    end
end)
 
-- トレインコントロール用メインループ
RunService.RenderStepped:Connect(function()
    local hum = TrainFeature.getHum()
    if not hum then
        if Settings.TrainControl.Enabled or Settings.TrainControl.VFlyEnabled or Settings.TrainControl.Running or Settings.TrainControl.NoClip then
            TrainFeature.hardStopAll()
        end
        return
    end
 
    local seatPart = hum.SeatPart
 
    if Settings.TrainControl.Enabled then
        if Settings.TrainControl.HadSeatOnce and not seatPart then
            if Settings.TrainControl.VFlyEnabled then
                TrainFeature.destroyVFly()
            end
            if tick() - Settings.TrainControl.LastSitAttempt >= Settings.TrainControl.SitRetryInterval then
                Settings.TrainControl.LastSitAttempt = tick()
                for i = 1, 3 do
                    TrainFeature.sitTrain()
                    task.wait(0.005)
                end
            end
            return
        end
 
        if not seatPart then
            if tick() - Settings.TrainControl.TrainEnableAt <= Settings.TrainControl.SeatGraceSeconds then
                if tick() - Settings.TrainControl.LastSitAttempt >= Settings.TrainControl.SitRetryInterval then
                    Settings.TrainControl.LastSitAttempt = tick()
                    for i = 1, 3 do
                        TrainFeature.sitTrain()
                        task.wait(0.005)
                    end
                end
                return
            else
                TrainFeature.hardStopAll()
                return
            end
        end
 
        if not TrainFeature.isTrainSeat(seatPart) then
            if tick() - Settings.TrainControl.TrainEnableAt <= Settings.TrainControl.SeatGraceSeconds then
                if tick() - Settings.TrainControl.LastSitAttempt >= Settings.TrainControl.SitRetryInterval then
                    Settings.TrainControl.LastSitAttempt = tick()
                    for i = 1, 3 do
                        TrainFeature.sitTrain()
                        task.wait(0.005)
                    end
                end
                return
            else
                TrainFeature.hardStopAll()
                return
            end
        end
 
        local justMounted = false
        if not Settings.TrainControl.HadSeatOnce then
            justMounted = true
        end
 
        Settings.TrainControl.HadSeatOnce = true
        TrainFeature.enableCameraNoClip()
 
        if justMounted then
            TrainFeature.forceSpawnToyAtHRP()
        end
 
        if Settings.TrainControl.NoClip then
            local trainModel = TrainFeature.getTrainModel()
            if trainModel then
                if Settings.TrainControl.NoClipVehicleModel ~= trainModel then
                    TrainFeature.restoreVehicleNoClip()
                    Settings.TrainControl.NoClipVehicleModel = trainModel
                end
                TrainFeature.applyNoClipToModel(trainModel, Settings.TrainControl.NoClipVehicleState)
            end
 
            local cModel = LocalPlayer.Character
            if cModel then
                if Settings.TrainControl.NoClipCharModel ~= cModel then
                    TrainFeature.restoreCharNoClip()
                    Settings.TrainControl.NoClipCharModel = cModel
                end
                TrainFeature.applyNoClipToModel(cModel, Settings.TrainControl.NoClipCharState)
            end
        else
            if Settings.TrainControl.NoClipVehicleModel then TrainFeature.restoreVehicleNoClip() end
            if Settings.TrainControl.NoClipCharModel then TrainFeature.restoreCharNoClip() end
        end
 
        if not Settings.TrainControl.VFlyEnabled then
            TrainFeature.startVFlyForTrain()
        end
 
        if Settings.TrainControl.VFlyEnabled and Settings.TrainControl.TargetPart and tick() - Settings.TrainControl.LastNetworkOwnership > Settings.TrainControl.NetworkOwnershipInterval then
            TrainFeature.claimNetworkOwnership(Settings.TrainControl.TargetPart)
            Settings.TrainControl.LastNetworkOwnership = tick()
        end
    else
        if Settings.TrainControl.VFlyEnabled or Settings.TrainControl.Running or Settings.TrainControl.NoClip then
            TrainFeature.hardStopAll()
        end
    end
 
    if not Settings.TrainControl.VFlyEnabled then return end
    if not (Settings.TrainControl.TargetPart and Settings.TrainControl.TargetPart.Parent and Settings.TrainControl.BV and Settings.TrainControl.BG) then
        TrainFeature.hardStopAll()
        return
    end
 
    local cam = Workspace.CurrentCamera
    if not cam then return end
 
    local move = TrainFeature.getMoveVector()
    local dir = Vector3.zero
 
    if move.Magnitude > 0 then
        dir = cam.CFrame.RightVector * move.X + cam.CFrame.LookVector * (-move.Z)
        if dir.Magnitude > 0 then dir = dir.Unit end
    end
 
    local speed = Settings.TrainControl.FlySpeed * Settings.TrainControl.SPEED_MULT
    Settings.TrainControl.BV.Velocity = dir * speed
    Settings.TrainControl.BG.CFrame = CFrame.lookAt(Settings.TrainControl.TargetPart.Position, Settings.TrainControl.TargetPart.Position + cam.CFrame.LookVector, cam.CFrame.UpVector)
end)
 
LocalPlayer.CharacterAdded:Connect(function()
    task.wait(0.5)
    TrainFeature.hardStopAll()
end)
 
-- メインループ開始
MainLoop()
 
-- ============================================
-- UI構築
-- ============================================
local Window = OrionLib:MakeWindow({
    Name = "SolarisHub BETA",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "SolarisHub",
    IntroEnabledLong = true,
    IntroText = false,
    FreeMouse = true,
})
 
-- ============================================
-- タブ一括作成（指定された順序）
-- ============================================
local GrabTab = Window:MakeTab({Name = "Grab",Icon = "rbxassetid://7733955740",PremiumOnly = false})
local AntiTab = Window:MakeTab({Name = "Anti",Icon = "rbxassetid://7734056411",PremiumOnly = false})
local BlobmanBetaTab = Window:MakeTab({Name = "Blobman Beta",Icon = "rbxassetid://7733916988",PremiumOnly = false})
local PvPTab = Window:MakeTab({Name = "PvP",Icon = "rbxassetid://7733771628",PremiumOnly = false})
local LoopTab = Window:MakeTab({Name = "Loop",Icon = "",PremiumOnly = false})
local VisualsTab = Window:MakeTab({Name = "Visuals", Icon = "rbxassetid://7733774602",PremiumOnly = false})
local AuraTab = Window:MakeTab({Name = "Aura",Icon = "rbxassetid://7743868000",PremiumOnly = false})
local LineTab = Window:MakeTab({Name = "Line",Icon = "rbxassetid://7734022107",PremiumOnly = false})
local ToyModTab = Window:MakeTab({Name = "ToyMod",Icon = "rbxassetid://7733956134",PremiumOnly = false})
local TrainTab = Window:MakeTab({Name = "Train Control",Icon = "",PremiumOnly = false})
local MiscTab = Window:MakeTab({Name = "Misc",Icon = "rbxassetid://7733970442",PremiumOnly = false})
local KeybindTab = Window:MakeTab({Name = "Keybind",Icon = "rbxassetid://7743869317",PremiumOnly = false})
local CRTab = Window:MakeTab({Name = "Credit",Icon = "rbxassetid://7733673987",PremiumOnly = false})
 
-- ============================================
-- グラブタブ
-- ============================================
GrabTab:AddSection({ Name = "Grab Strength Adjustment" })
 
GrabTab:AddSlider({
    Name = "Strength",
    Min = 300,
    Max = 40000,
    Increment = 1,
    Default = Settings.Grab.strength,
    ValueName = ".",
    Callback = function(value)
        Settings.Grab.strength = value
    end
})
 
GrabTab:AddToggle({
    Name = "Strength Adjustment",
    Default = false,
    Callback = function(enabled)
        Settings.Grab.EnableThrowStrength = enabled
        if enabled then
            GrabFeature.enableStrength()
        else
            GrabFeature.disableStrength()
        end
    end
})
 
GrabTab:AddSection({ Name = "Grab function" })
 
GrabTab:AddToggle({
    Name = "Void Grab",
    Default = false,
    Callback = function(v) 
        Settings.Grab.VoidGrab = v 
    end
})
 
GrabTab:AddToggle({
    Name = "Death Grab",
    Default = false,
    Callback = function(v) 
        Settings.Grab.DeathGrab = v 
    end
})
 
GrabTab:AddToggle({
    Name = "Kick Grab",
    Default = false,
    Callback = function(v) 
        Settings.Grab.KickGrab = v 
    end
})
 
GrabTab:AddToggle({
    Name = "Noclip Grab",
    Default = false,
    Callback = function(enabled)
        Settings.Grab.NoclipGrab = enabled
        GrabFeature.toggleNoclip(enabled)
    end
})
 
-- ============================================
-- アンチタブ
-- ============================================
 
AntiTab:AddSection({ Name = "Protection Features" })
 
AntiTab:AddToggle({
    Name = "Anti Grab v1",
    Default = false,
    Callback = function(enabled) 
        Settings.Anti.AntiGrab = enabled 
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Grab v2",
    Default = false,
    Callback = function(enabled)
        if enabled then 
            AntiFeature.enableAntiGrabSolaris() 
        else 
            AntiFeature.disableAntiGrabSolaris() 
        end
    end
})
 
AntiTab:AddSection({ Name = "Disruption Countermeasures" })
 
AntiTab:AddToggle({
    Name = "Anti Burn",
    Default = false,
    Callback = function(enabled) 
        Settings.Anti.AntiBurn = enabled 
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Explosion",
    Default = false,
    Callback = function(enabled)
        Settings.Anti.AntiExplosion = enabled
        if enabled then
            if LocalPlayer.Character then 
                AntiFeature.setupAntiExplosion(LocalPlayer.Character) 
            end
            if State.connections.antiExplosionChar then 
                State.connections.antiExplosionChar:Disconnect() 
            end
            State.connections.antiExplosionChar = LocalPlayer.CharacterAdded:Connect(function(char)
                if antiExplosionConnection then 
                    antiExplosionConnection:Disconnect() 
                end
                AntiFeature.setupAntiExplosion(char)
            end)
        else
            if antiExplosionConnection then
                antiExplosionConnection:Disconnect()
                antiExplosionConnection = nil
            end
            if State.connections.antiExplosionChar then
                State.connections.antiExplosionChar:Disconnect()
                State.connections.antiExplosionChar = nil
            end
        end
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Void",
    Default = false,
    Callback = function(enabled)
        Settings.Anti.AntiVoid = enabled
        if enabled then
            Workspace.FallenPartsDestroyHeight = -1000
            task.spawn(function()
                while Settings.Anti.AntiVoid do
                    local char = Utility.GetPlayerCharacter()
                    if char and char:FindFirstChild("HumanoidRootPart") then
                        if char.HumanoidRootPart.Position.Y < -800 then
                            char:SetPrimaryPartCFrame(CFrame.new(0, 100, 0))
                        end
                    end
                    task.wait(0.1)
                end
            end)
        else
            Workspace.FallenPartsDestroyHeight = -100
        end
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Lag",
    Default = false,
    Callback = function(enabled)
        anticreatelinelocalscript.Disabled = enabled
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Kick",
    Default = false,
    Callback = function(enabled) 
        Settings.Anti.AntiKick = enabled 
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Kick Kunai (BETA)",
    Default = false,
    Callback = function(enabled)
        Settings.Anti.AntiKickKunai = enabled
        if enabled then
            AntiFeature.createKunaiMessageGui()
            AntiFeature.attachKunai(false)
            if State.loops.kunaiCheck then 
                task.cancel(State.loops.kunaiCheck) 
            end
            State.loops.kunaiCheck = task.spawn(function()
                while Settings.Anti.AntiKickKunai do
                    task.wait(1.5)
                    if not AntiFeature.isKunaiAttached() then 
                        AntiFeature.attachKunai(true) 
                    end
                end
            end)
        else
            if State.loops.kunaiCheck then
                task.cancel(State.loops.kunaiCheck)
                State.loops.kunaiCheck = nil
            end
            AntiFeature.cleanupMyKunaiToys()
            State.currentKunai = nil
            if kunaiTextLabel then
                kunaiTextLabel.Text = ""
                kunaiTextLabel.TextTransparency = 1
            end
        end
    end
})
 
AntiTab:AddToggle({
    Name = "Anti Sticky",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiSticky = Value
        if Value then
            AntiFeature.setTouchQuery(false)
        else
            AntiFeature.setTouchQuery(true)
        end
    end    
})
 
AntiTab:AddToggle({
    Name = "Anti Paint",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiPaint = Value
        if Value then
            AntiFeature.deleteAllPaintParts()
            AntiFeature.watchNewPaintParts()
        else
            AntiFeature.disconnectWatchers()
            AntiFeature.restorePaintParts()
        end
    end    
})
 
AntiTab:AddToggle({
    Name = "Anti Gucci (Blobman)",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiGucciBlobman = Value
        if Value then
            AntiFeature.startAntiGucci()
        else
            AntiFeature.stopAntiGucci()
        end
    end    
})
 
AntiTab:AddToggle({
    Name = "Anti Gucci (Train)",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiGucciTrain = Value
        if Value then
            AntiFeature.startAntiGucciTrain()
        else
            AntiFeature.stopAntiGucciTrain()
        end
    end    
})
 
AntiTab:AddToggle({
    Name = "Anti-Ragdoll",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiRagdoll = Value
        if Value then
            RunService.Heartbeat:Connect(function()
                if not Settings.Anti.AntiRagdoll then return end
                local character = LocalPlayer.Character
                if character then
                    local humanoid = character:FindFirstChildOfClass("Humanoid")
                    if humanoid then
                        local state = humanoid:GetState()
                        if state == Enum.HumanoidStateType.Ragdoll or state == Enum.HumanoidStateType.FallingDown then
                            AntiFeature.ragdoll()
                            humanoid:ChangeState(Enum.HumanoidStateType.Running)
                        end
                    end
                end
            end)
        end
    end    
})
 
AntiTab:AddToggle({
    Name = "Anti-Banana",
    Default = false,
    Callback = function(Value)
        Settings.Anti.AntiBanana = Value
    end    
})
 
-- ============================================
-- ブロブマンベータタブ
-- ============================================
 
BlobmanBetaTab:AddToggle({
    Name = "Ignore Friends",
    Default = false,
    Callback = function(v)
        Settings.BlobmanBeta.ignoreFriends = v
    end
})
 
BlobmanBetaTab:AddSection({ Name = "Blobman" })
 
local TargetDropdown_blob = BlobmanBetaTab:AddDropdown({
    Name = "Select Target",
    Default = "",
    Options = BlobmanBetaFeature.GetPlayerList(),
    Callback = function(Value)
        Settings.BlobmanBeta.selectedTarget = Value
    end
})
 
BlobmanBetaTab:AddButton({
    Name = "Kick",
    Callback = function()
        task.spawn(function()
            local target = BlobmanBetaFeature.checkTarget()
            if not target then return end
 
            local blob = BlobmanBetaFeature.ensureBlob()
            if not blob then return end
 
            local character = target.Character
            if not character then return end
            local root = character:FindFirstChild("HumanoidRootPart")
            if not root then return end
 
            local side = (math.random() >= 0.5) and "Left" or "Right"
            local pos = BlobmanBetaFeature.getLocalRoot().CFrame
 
            task.wait(0.1)
            BlobmanBetaFeature.getLocalRoot().CFrame = root.CFrame
            task.wait(0.08)
            BlobmanBetaFeature.blobKick(blob, root, side)
            task.wait(0.1)
            BlobmanBetaFeature.getLocalRoot().CFrame = pos
        end)
    end
})
 
BlobmanBetaTab:AddButton({
    Name = "Bring",
    Callback = function()
        task.spawn(function()
            local target = BlobmanBetaFeature.checkTarget()
            if not target then return end
 
            local blob = BlobmanBetaFeature.ensureBlob()
            if not blob then return end
 
            local character = target.Character
            if not character then return end
            local root = character:FindFirstChild("HumanoidRootPart")
            if not root then return end
 
            local side = (math.random() >= 0.5) and "Left" or "Right"
            local myPos = BlobmanBetaFeature.getLocalRoot().CFrame
 
            BlobmanBetaFeature.SetNetworkOwner(root)
            task.wait(0.15)
            BlobmanBetaFeature.getLocalRoot().CFrame = root.CFrame
            task.wait(0.15)
            BlobmanBetaFeature.blobGrab(blob, root, side)
            task.wait(0.2)
            BlobmanBetaFeature.getLocalRoot().CFrame = myPos
        end)
    end
})
 
BlobmanBetaTab:AddButton({
    Name = "Slide",
    Callback = function()
        task.spawn(function()
            local target = BlobmanBetaFeature.checkTarget()
            if not target then return end
 
            local blob = BlobmanBetaFeature.ensureBlob()
            if not blob then return end
 
            local character = target.Character
            if not character then return end
            local root = character:FindFirstChild("HumanoidRootPart")
            if not root then return end
 
            local side = (math.random() >= 0.5) and "Left" or "Right"
            local pos = BlobmanBetaFeature.getLocalRoot().CFrame
 
            BlobmanBetaFeature.SetNetworkOwner(root)
            task.wait(0.08)
            BlobmanBetaFeature.getLocalRoot().CFrame = root.CFrame
            task.wait(0.08)
            BlobmanBetaFeature.blobGrab(blob, BlobmanBetaFeature.getLocalRoot(), side)
            task.wait(0.08)
            BlobmanBetaFeature.SetNetworkOwner(root)
            task.wait(0.08)
            root.CFrame = root.CFrame + Vector3.new(0, 2, 0)
            task.wait(0.08)
            BlobmanBetaFeature.ungrab(root)
            task.wait(0.08)
            BlobmanBetaFeature.blobGrab(blob, root, side)
            task.wait(0.08)
            BlobmanBetaFeature.blobDrop(blob, root, side)
            task.wait(0.08)
            BlobmanBetaFeature.ungrab(root)
            task.wait(0.08)
            BlobmanBetaFeature.getLocalRoot().CFrame = pos
        end)
    end
})
 
BlobmanBetaTab:AddSection({ Name = "Kick all" })
 
BlobmanBetaTab:AddButton({
    Name = "Kick All Execute",
    Callback = function()
        if Settings.BlobmanBeta.isExecuting then
            OrionLib:MakeNotification({Name = "Executing", Content = "Already executing", Time = 3})
            return
        end
        Settings.BlobmanBeta.isExecuting = true
 
        local function executeKick()
            local blob = BlobmanBetaFeature.getBlobman()
            if not blob then
                blob = BlobmanBetaFeature.spawnBlobman()
                if not blob then
                    OrionLib:MakeNotification({Name = "Error", Content = "Failed to spawn Blobman", Time = 5})
                    return false
                end
            end
 
            if not BlobmanBetaFeature.isSittingOnBlobman() then
                BlobmanBetaFeature.ensureSitBlobman()
                if not BlobmanBetaFeature.isSittingOnBlobman() then
                    OrionLib:MakeNotification({Name = "Error", Content = "Not sitting on Blobman", Time = 4})
                    return false
                end
            end
 
            BlobmanBetaFeature.ensureSitBlobman()
 
            local targets = {}
            for _, plr in Players:GetPlayers() do
                if plr == LocalPlayer then continue end
                if Settings.BlobmanBeta.ignoreFriends then
                    local isFriend = false
                    pcall(function() isFriend = plr:IsFriendsWith(LocalPlayer.UserId) end)
                    if isFriend then continue end
                end
                local char = plr.Character
                if not char then continue end
                local root = char:FindFirstChild("HumanoidRootPart")
                if not root then continue end
                local playerY = root.Position.Y
                if playerY < -25.072832107543945 then continue end
                local shouldIgnore = false
                for _, ignoreHeight in ipairs(Settings.BlobmanBeta.ignoreHeights) do
                    if math.abs(playerY - ignoreHeight) < 0.1 then
                        shouldIgnore = true
                        break
                    end
                end
                if shouldIgnore then continue end
                table.insert(targets, {plr = plr, root = root})
            end
 
            for i = #targets, 2, -1 do
                local j = math.random(i)
                targets[i], targets[j] = targets[j], targets[i]
            end
 
            if blob then
                local blobPos = blob:FindFirstChild("VehicleSeat")
                if blobPos then
                    blobPos = blobPos.Position
                    table.sort(targets, function(a, b)
                        local distA = (a.root.Position - blobPos).Magnitude
                        local distB = (b.root.Position - blobPos).Magnitude
                        return distA < distB
                    end)
                end
            end
 
            for _, t in ipairs(targets) do
                local kickSuccess = false
                local retries = 2
                while not kickSuccess and retries > 0 do
                    if not BlobmanBetaFeature.isSittingOnBlobman() then
                        BlobmanBetaFeature.ensureSitBlobman()
                        if not BlobmanBetaFeature.isSittingOnBlobman() then break end
                    end
                    local myRoot = BlobmanBetaFeature.getLocalRoot()
                    if myRoot and t.root then
                        local pos = myRoot.CFrame
                        myRoot.CFrame = t.root.CFrame
                        task.wait(0.07)
                        BlobmanBetaFeature.blobKick(blob, t.root, Settings.BlobmanBeta.currentSide)
                        kickSuccess = true
                        task.wait(Settings.BlobmanBeta.KickDelay)
                        myRoot.CFrame = pos
                        task.wait(0.02)
                    else
                        retries = retries - 1
                    end
                end
            end
 
            BlobmanBetaFeature.destroyBlobman()
            OrionLib:MakeNotification({Name = "Complete", Content = "Kick All executed", Time = 5})
            return true
        end
 
        executeKick()
        Settings.BlobmanBeta.isExecuting = false
    end
})
 
BlobmanBetaTab:AddSection({ Name = "Kick aura" })
 
BlobmanBetaTab:AddToggle({
    Name = "Kick Aura",
    Default = false,
    Callback = function(v)
        Settings.BlobmanBeta.kickAuraActive = v
        if v then
            Settings.BlobmanBeta.auraKickedPlayers = {}
            local auraSide = "Left"
            spawn(function()
                while Settings.BlobmanBeta.kickAuraActive do
                    if not BlobmanBetaFeature.isSittingOnBlobman() then
                        BlobmanBetaFeature.ensureSitBlobman()
                        task.wait(0.5)
                        continue
                    end
 
                    local blob = BlobmanBetaFeature.getBlobman()
                    if not blob then
                        task.wait(1)
                        continue
                    end
 
                    local myRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if not myRoot then
                        task.wait(0.5)
                        continue
                    end
 
                    local currentTime = tick()
                    for playerId, kickTime in pairs(Settings.BlobmanBeta.auraKickedPlayers) do
                        if currentTime - kickTime >= 1.5 then
                            Settings.BlobmanBeta.auraKickedPlayers[playerId] = nil
                        end
                    end
 
                    local nearbyPlayers = {}
                    for _, plr in Players:GetPlayers() do
                        if plr == LocalPlayer then continue end
 
                        if Settings.BlobmanBeta.auraKickedPlayers[plr.UserId] then continue end
 
                        if Settings.BlobmanBeta.ignoreFriends then
                            local isFriend = false
                            pcall(function()
                                isFriend = plr:IsFriendsWith(LocalPlayer.UserId)
                            end)
                            if isFriend then continue end
                        end
 
                        local char = plr.Character
                        if not char then continue end
 
                        local root = char:FindFirstChild("HumanoidRootPart")
                        if not root then continue end
 
                        local hum = char:FindFirstChildOfClass("Humanoid")
                        if not hum or hum.Health <= 0 then continue end
 
                        local playerY = root.Position.Y
                        if playerY < -25.072832107543945 then continue end
 
                        local shouldIgnore = false
                        for _, ignoreHeight in ipairs(Settings.BlobmanBeta.ignoreHeights) do
                            if math.abs(playerY - ignoreHeight) < 0.1 then
                                shouldIgnore = true
                                break
                            end
                        end
                        if shouldIgnore then continue end
 
                        local dist = (root.Position - myRoot.Position).Magnitude
                        if dist < 50 then
                            table.insert(nearbyPlayers, {plr = plr, root = root, dist = dist})
                        end
                    end
 
                    table.sort(nearbyPlayers, function(a, b)
                        return a.dist < b.dist
                    end)
 
                    if #nearbyPlayers > 0 then
                        local target = nearbyPlayers[1]
                        local targetHum = target.root.Parent:FindFirstChildOfClass("Humanoid")
                        local healthBefore = targetHum and targetHum.Health or 0
 
                        if BlobmanBetaFeature.isSittingOnBlobman() then
                            myRoot.AssemblyLinearVelocity = Vector3.zero
                            BlobmanBetaFeature.resetBlobmanPhysics()
 
                            BlobmanBetaFeature.blobGrab(blob, myRoot, auraSide)
                            task.wait(0.11)
 
                            BlobmanBetaFeature.SetNetworkOwner(target.root)
                            task.wait(0.05)
 
                            local oldPos = target.root.CFrame
                            target.root.CFrame = oldPos + Vector3.new(0, Settings.BlobmanBeta.FloatAmount, 0)
                            task.wait(0.08)
 
                            BlobmanBetaFeature.ungrab(target.root)
                            BlobmanBetaFeature.blobGrab(blob, target.root, auraSide)
 
                            BlobmanBetaFeature.resetBlobmanPhysics()
                            BlobmanBetaFeature.stabilizeBlobman()
 
                            task.wait(0.1)
                            local healthAfter = targetHum and targetHum.Health or 0
 
                            if healthAfter <= 0 then
                                Settings.BlobmanBeta.auraKickedPlayers[target.plr.UserId] = nil
                            else
                                Settings.BlobmanBeta.auraKickedPlayers[target.plr.UserId] = tick()
                            end
 
                            auraSide = (auraSide == "Left") and "Right" or "Left"
 
                            task.wait(Settings.BlobmanBeta.KickDelay)
                        end
                    end
 
                    task.wait(0.5)
                end
                Settings.BlobmanBeta.auraKickedPlayers = {}
            end)
        end
    end
})
 
-- ============================================
-- PvPタブ
-- ============================================
 
local SilentAimToggle = PvPTab:AddToggle({
    Name = "Silent Aim",
    Default = false,
    Callback = function(Value)
        Settings.PvP.SilentAimEnabled = Value
 
        if Value and not State.CameraInitialized then
            State.CameraClone = Camera:Clone()
            State.CameraClone.Parent = workspace
            State.CameraClone.Name = "SilentCamera"
            State.CameraClone.CFrame = Camera.CFrame
            workspace.CurrentCamera = State.CameraClone
            State.CameraInitialized = true
        end
    end    
})
 
PvPTab:AddSlider({
    Name = "Silent Aim Strength",
    Min = 1,
    Max = 100,
    Default = 50,
    Increment = 1,
    ValueName = "Strength",
    Callback = function(Value)
        Settings.PvP.AimStrength = Value
    end    
})
 
PvPTab:AddToggle({
    Name = "Enable Auto Grab",
    Default = false,
    Callback = function(Value)
        Settings.PvP.AutoGrabEnabled = Value
    end    
})
 
-- ============================================
-- Loopタブ（KillAll/BringAll/TargetKill）
-- ============================================
 
LoopTab:AddSection({ Name = "Settings" })
 
LoopTab:AddToggle({
    Name    = "Whitelist Friends",
    Default = false,
    Callback = function(value)
        Settings.Loop.WhitelistFriends = value
        OrionLib:MakeNotification({
            Name    = "Whitelist",
            Content = "Friends whitelist: " .. (value and "Enabled" or "Disabled"),
            Image   = "rbxassetid://4483345998",
            Time    = 2,
        })
    end,
})
 
LoopTab:AddSection({ Name = "Bring All" })
 
LoopTab:AddToggle({
    Name    = "Bring All",
    Default = false,
    Save    = false,
    Flag    = "BringAllToggle",
    Callback = function(state)
        getgenv().BringAll = state
        Settings.Loop.BringAll = state
        if state then
            getgenv().KillAll = false
            Settings.Loop.killAllEnabled = false
            if Settings.Loop.killAllLoop then task.cancel(Settings.Loop.killAllLoop) Settings.Loop.killAllLoop = nil end
            LoopFeature.restoreCamera()
            coroutine.wrap(LoopFeature.startBringAll)()
            OrionLib:MakeNotification({ Name = "Bring All", Content = "Started", Image = "rbxassetid://4483345998", Time = 2 })
        else
            LoopFeature.unFreezeCam()
            LoopFeature.stopFloating()
            OrionLib:MakeNotification({ Name = "Bring All", Content = "Stopped", Image = "rbxassetid://4483345998", Time = 2 })
        end
    end,
})
 
LoopTab:AddSection({ Name = "Kill All" })
 
LoopTab:AddToggle({
    Name    = "Kill All Players",
    Default = false,
    Flag    = "KillAllToggle",
    Callback = function(value)
        Settings.Loop.killAllEnabled = value
        getgenv().KillAll = value
        if value then
            getgenv().BringAll = false
            LoopFeature.unFreezeCam()
            LoopFeature.stopFloating()
            Settings.Loop.OriginalPosition = nil
            Settings.Loop.LastTeleportTime = 0
            Settings.Loop.ProcessedPlayers = {}
            LoopFeature.startKillAll()
            OrionLib:MakeNotification({ Name = "Kill All", Content = "Started (closest first)", Image = "rbxassetid://4483345998", Time = 3 })
        else
            if Settings.Loop.killAllLoop then task.cancel(Settings.Loop.killAllLoop) Settings.Loop.killAllLoop = nil end
            LoopFeature.returnToOriginalPosition()
            Settings.Loop.OriginalPosition = nil
            Settings.Loop.ProcessedPlayers = {}
            LoopFeature.restoreCamera()
            OrionLib:MakeNotification({ Name = "Kill All", Content = "Stopped", Image = "rbxassetid://4483345998", Time = 2 })
        end
    end,
})
 
LoopTab:AddSection({ Name = "Target Kill" })
 
local TargetLabel = LoopTab:AddLabel("Selected: None")
 
local PlayerDropdown = LoopTab:AddDropdown({
    Name = "Select / Deselect Target",
    Default = "",
    Options = LoopFeature.updatePlayerDropdown(),
    Callback = function(value)
        local player = Players:FindFirstChild(value)
        if player then
            if Settings.Loop.TargetPlayers[player.UserId] then
                Settings.Loop.TargetPlayers[player.UserId] = nil
                OrionLib:MakeNotification({ Name = "Target Removed", Content = player.Name .. " was removed", Image = "rbxassetid://4483345998", Time = 2 })
            else
                Settings.Loop.TargetPlayers[player.UserId] = true
                OrionLib:MakeNotification({ Name = "Target Added", Content = player.Name .. " was added", Image = "rbxassetid://4483345998", Time = 2 })
            end
            TargetLabel:Set("Selected: " .. LoopFeature.getSelectedTargetsDisplay())
        end
    end,
})
 
local function refreshPlayerListLoop()
    PlayerDropdown:Refresh(LoopFeature.updatePlayerDropdown(), true)
end
 
LoopTab:AddButton({
    Name = "Refresh Player List",
    Callback = function()
        refreshPlayerListLoop()
        OrionLib:MakeNotification({ Name = "Refreshed", Content = "Player list updated", Image = "rbxassetid://4483345998", Time = 2 })
    end,
})
 
LoopTab:AddButton({
    Name = "Clear All Targets",
    Callback = function()
        Settings.Loop.TargetPlayers = {}
        TargetLabel:Set("Selected: None")
        OrionLib:MakeNotification({ Name = "Cleared", Content = "All targets cleared", Image = "rbxassetid://4483345998", Time = 2 })
    end,
})
 
LoopTab:AddToggle({
    Name    = "Kill Selected Targets",
    Default = false,
    Callback = function(value)
        Settings.Loop.targetKillEnabled = value
        if value then
            if next(Settings.Loop.TargetPlayers) == nil then
                OrionLib:MakeNotification({ Name = "Error", Content = "ターゲットを選択してください", Image = "rbxassetid://4483345998", Time = 3 })
                Settings.Loop.targetKillEnabled = false
                return
            end
            Settings.Loop.OriginalPosition = nil
            Settings.Loop.LastTeleportTime = 0
            Settings.Loop.LastTargetKillTime = 0
            LoopFeature.startTargetKill()
            OrionLib:MakeNotification({ Name = "Target Kill", Content = "Started", Image = "rbxassetid://4483345998", Time = 3 })
        else
            if Settings.Loop.targetKillLoop then task.cancel(Settings.Loop.targetKillLoop) Settings.Loop.targetKillLoop = nil end
            LoopFeature.returnToOriginalPosition()
            Settings.Loop.OriginalPosition = nil
            OrionLib:MakeNotification({ Name = "Target Kill", Content = "Stopped", Image = "rbxassetid://4483345998", Time = 2 })
        end
    end,
})
 
-- プレイヤー参加/退出時のリスト自動更新
Players.PlayerAdded:Connect(refreshPlayerListLoop)
Players.PlayerRemoving:Connect(function(player)
    Settings.Loop.TargetPlayers[player.UserId] = nil
    TargetLabel:Set("Selected: " .. LoopFeature.getSelectedTargetsDisplay())
    refreshPlayerListLoop()
end)
 
-- ============================================
-- Visualsタブ
-- ============================================
VisualsTab:AddSection({ Name = "ESP Settings" })
 
VisualsTab:AddToggle({
    Name = "ESP",
    Default = false,
    Callback = function(value)
        Settings.Visuals.ESPEnabled = value
        if value then
            VisualsFeature.enableESP()
        else
            VisualsFeature.disableESP()
        end
    end    
})
 
VisualsTab:AddColorpicker({
    Name = "ESP Fill Color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(value)
        Settings.Visuals.ESPFillColor = value
    end      
})
 
VisualsTab:AddColorpicker({
    Name = "ESP Outline Color",
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(value)
        Settings.Visuals.ESPOutlineColor = value
    end      
})
 
VisualsTab:AddToggle({
    Name = "Show Hitbox",
    Default = false,
    Callback = function(value)
        Settings.Visuals.ShowHitbox = value
        if value then
            VisualsFeature.enableHitbox()
        else
            VisualsFeature.disableHitbox()
        end
    end    
})
 
VisualsTab:AddColorpicker({
    Name = "Hitbox Color",
    Default = Color3.fromRGB(0, 100, 255),
    Callback = function(value)
        Settings.Visuals.HitboxColor = value
    end      
})
 
VisualsTab:AddToggle({
    Name = "Show Player Info",
    Default = false,
    Callback = function(value)
        Settings.Visuals.ShowPlayerInfo = value
        if value then
            VisualsFeature.enableNameLabels()
        else
            VisualsFeature.disableNameLabels()
        end
    end    
})
 
VisualsTab:AddSection({ Name = "Camera Settings" })
 
VisualsTab:AddSlider({
    Name = "FOV",
    Min = 1,
    Max = 120,
    Default = 70,
    Increment = 1,
    ValueName = "FOV",
    Callback = function(value)
        Settings.Visuals.CameraFOV = value
        Workspace.CurrentCamera.FieldOfView = value
    end    
})
 
VisualsTab:AddSection({ Name = "Spectate Settings" })
 
VisualsTab:AddDropdown({
    Name = "Spectate Player",
    Default = LocalPlayer.Name,
    Options = VisualsFeature.getPlayerNames(),
    Callback = function(value)
        VisualsFeature.spectatePlayer(value)
    end    
})
 
VisualsTab:AddButton({
    Name = "Refresh Player List",
    Callback = function()
    end    
})
 
VisualsTab:AddButton({
    Name = "Reset Camera",
    Callback = function()
        VisualsFeature.spectatePlayer(LocalPlayer.Name)
    end    
})
 
-- ============================================
-- Auraタブ
-- ============================================
 
AuraTab:AddSection({ Name = "Settings" })
 
AuraTab:AddSlider({
    Name = "Aura Range",
    Min = 0,
    Max = 32,
    Default = 32,
    Increment = 1,
    ValueName = "studs",
    Callback = function(value)
        Settings.Aura.AuraRadius = value
    end
})
 
AuraTab:AddToggle({
    Name = "Ignore Friends",
    Default = false,
    Callback = function(value)
        Settings.Aura.IgnoreFriendAura = value
    end
})
 
AuraTab:AddSection({ Name = "Aura Features" })
 
AuraTab:AddToggle({
    Name = "Void Aura",
    Default = false,
    Callback = function(value)
        Settings.Aura.VoidAura = value
    end
})
 
AuraTab:AddToggle({
    Name = "Ragdoll Aura",
    Default = false,
    Callback = function(value)
        Settings.Aura.RagdollAura = value
    end
})
 
AuraTab:AddToggle({
    Name = "Death Aura",
    Default = false,
    Callback = function(value)
        Settings.Aura.DeathAura = value
    end
})
 
AuraTab:AddToggle({
    Name = "Fire Aura",
    Default = false,
    Callback = function(value)
        Settings.Aura.FireAura = value
    end
})
 
-- ============================================
-- Lineタブ（Blitz統合）
-- ============================================
 
LineTab:AddToggle({
    Name = "Ultra Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.ultragrabbb = Value
    end
})
 
LineTab:AddToggle({
    Name = "Invisible Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.InvisibleLine = Value
    end
})
 
LineTab:AddToggle({
    Name = "Crazy Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.CrazyLine = Value
        if Value then
            spawn(function()
                while Settings.Line.CrazyLine do
                    for _, player in pairs(Players:GetPlayers()) do
                        if player and player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Torso") then
                            CreateGrabLine:FireServer(player.Character.Torso, CFrame.new(0.12640380859375, 0.9606337547302246, -0.5000009536743164, 0.9985212683677673, 0, -0.05436277016997337, -6.4805472099749295e-9, 1, -1.1903301100346653e-7, 0.05436277016997337, 5.960464477539063e-8, 0.9985212683677673))
                        end
                        task.wait()
                    end
                end
            end)
        end
    end
})
 
local LagServerToggle = nil
 
LagServerToggle = LineTab:AddToggle({
    Name = "Lag Server",
    Default = false,
    Callback = function(laggg)
        spawn(function()
            while laggg and Settings.Line.LagServerActive do
                for _ = 0, Settings.Line.Lag_Intensity do
                    for _, player in ipairs(Players:GetPlayers()) do
                        if player.Character and player.Character:FindFirstChild("Torso") then
                            CreateGrabLine:FireServer(player.Character.Torso, player.Character.Torso.CFrame)
                        end
                    end
                end
                wait(1)
            end
        end)
        Settings.Line.LagServerActive = laggg
    end
})
 
LineTab:AddSlider({
    Name = "Lag Intensity",
    Min = 1,
    Max = 400,
    Default = 150,
    Increment = 1,
    ValueName = "Can kick players!",
    Callback = function(Value)
        Settings.Line.Lag_Intensity = Value
    end
})
 
LineTab:AddColorpicker({
    Name = "Line color selection",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(Value)
        Settings.Line.LineColorChangeValue = Value
    end
})
 
LineTab:AddButton({
    Name = "Apply Color",
    Callback = function()
        for i = 1, #lineColors do
            if i == 1 then
                lineColors[i] = ColorSequence.new(Settings.Line.LineColorChangeValue)
            else
                lineColors[i] = Color3.new(Settings.Line.LineColorChangeValue.R, Settings.Line.LineColorChangeValue.G, Settings.Line.LineColorChangeValue.B)
            end
        end
        updateLineColorsEvent:FireServer(unpack(lineColors))
    end
})
 
LineTab:AddToggle({
    Name = "Rainbow Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.RainbowLine = Value
        if Value then
            Settings.Line.FlashingLine = false
            Settings.Line.GradientLine = false
            Settings.Line.PulseEffect = false
            updateRainbowLine()
        end
    end
})
 
LineTab:AddToggle({
    Name = "Flashing Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.FlashingLine = Value
        if Value then
            Settings.Line.RainbowLine = false
            Settings.Line.GradientLine = false
            Settings.Line.PulseEffect = false
            updateFlashingLine()
        end
    end
})
 
LineTab:AddToggle({
    Name = "Gradient Line",
    Default = false,
    Callback = function(Value)
        Settings.Line.GradientLine = Value
        if Value then
            Settings.Line.RainbowLine = false
            Settings.Line.FlashingLine = false
            Settings.Line.PulseEffect = false
            updateGradientLine()
        end
    end
})
 
LineTab:AddToggle({
    Name = "Pulse Effect",
    Default = false,
    Callback = function(Value)
        Settings.Line.PulseEffect = Value
        if Value then
            Settings.Line.RainbowLine = false
            Settings.Line.FlashingLine = false
            Settings.Line.GradientLine = false
            updatePulseEffect()
        end
    end
})
 
LineTab:AddSlider({
    Name = "Effect Speed",
    Min = 0.1,
    Max = 5,
    Default = 1,
    Increment = 0.1,
    ValueName = "Speed",
    Callback = function(Value)
        Settings.Line.LineSpeed = Value
    end
})
 
LineTab:AddSlider({
    Name = "Strobe Speed",
    Min = 0.1,
    Max = 2,
    Default = 0.5,
    Increment = 0.1,
    ValueName = "Seconds",
    Callback = function(Value)
        Settings.Line.StrobeSpeed = Value
    end
})
 
-- ============================================
-- ToyModタブ（統合）
-- ============================================
 
ToyModTab:AddToggle({Name = "Use house items", Default = false, Callback = function(v) Settings.ToyMod.UsePlotItems = v end})
 
ToyModTab:AddButton({Name = "Create Wings", Callback = function() ToyModFeature.CreateWings() end})
ToyModTab:AddButton({Name = "Destroy All Wings", Callback = function() ToyModFeature.DestroyWings() end})
 
ToyModTab:AddSection({Name = "Placement"})
ToyModTab:AddSlider({Name = "Feather spacing", Min = 0.05, Max = 5, Default = 0.1, Increment = 0.05, Callback = function(v) Settings.ToyMod.XSpacing = v end})
ToyModTab:AddSlider({Name = "Y Offset", Min = -10, Max = 10, Default = 0, Increment = 0.5, Callback = function(v) Settings.ToyMod.YOffset = v end})
ToyModTab:AddSlider({Name = "Z Offset", Min = -15, Max = 10, Default = 0, Increment = 0.5, Callback = function(v) Settings.ToyMod.ZOffset = v end})
 
ToyModTab:AddSection({Name = "Rotation"})
ToyModTab:AddSlider({Name = "Horizontal Angle", Min = -180, Max = 180, Default = -90, Increment = 5, ValueName = "°", Callback = function(v) Settings.ToyMod.AngleH = v end})
ToyModTab:AddSlider({Name = "Vertical Angle", Min = -90, Max = 90, Default = 0, Increment = 5, ValueName = "°", Callback = function(v) Settings.ToyMod.AngleV = v end})
 
ToyModTab:AddSection({Name = "Flapping"})
ToyModTab:AddToggle({Name = "Enabled", Default = false, Callback = function(v) Settings.ToyMod.FlapEnabled = v if not v then State.ToyModWings.FlapTime = 0 end end})
ToyModTab:AddDropdown({
    Name = "Animation", 
    Default = "1: Standard Flap", 
    Options = {
        "1: Standard Flap",
        "2: Wavy Wings",
        "3: Floating Up and Down",
        "4: Hummingbird",
        "5: Spiral",
        "6: Elegant",
        "7: Twist",
        "8: Pulse",
        "9: Jump",
        "10: Ribbon",
        "11: Phoenix",
        "12: Cyclone"
    }, 
    Callback = function(v) 
        for i = 1, 12 do
            local names = {
                "Standard Flap", "Wavy Wings", "Floating Up and Down", "Hummingbird", "Spiral",
                "Elegant", "Twist", "Pulse", "Jump", "Ribbon",
                "Phoenix", "Cyclone"
            }
            if v == i .. ": " .. names[i] then
                Settings.ToyMod.FlapType = i
                break
            end
        end
    end
})
ToyModTab:AddSlider({Name = "Speed", Min = 0.5, Max = 8, Default = 2, Increment = 0.1, Callback = function(v) Settings.ToyMod.FlapSpeed = v end})
ToyModTab:AddSlider({Name = "Size", Min = 10, Max = 300, Default = 30, Increment = 5, ValueName = "°", Callback = function(v) Settings.ToyMod.FlapRange = v end})
 
ToyModTab:AddSection({Name = "Advanced Settings"})
ToyModTab:AddToggle({Name = "Link Speed", Default = false, Callback = function(v) Settings.ToyMod.SpeedLinkFlap = v end})
 
-- ============================================
-- Train Controlタブ
-- ============================================
 
TrainTab:AddToggle({
    Name = "Train Control",
    Default = false,
    Callback = function(Value)
        if Value then
            Settings.TrainControl.Enabled = true
            Settings.TrainControl.TrainEnableAt = tick()
            Settings.TrainControl.LastSitAttempt = 0
            Settings.TrainControl.HadSeatOnce = false
            Settings.TrainControl.Running = true
            TrainFeature.startLoop()
            for i = 1, 5 do
                TrainFeature.sitTrain()
                task.wait(0.01)
            end
        else
            TrainFeature.hardStopAll()
        end
    end    
})
 
TrainTab:AddSlider({
    Name = "Flying Speed",
    Min = 1,
    Max = 50,
    Default = 15,
    Increment = 1,
    ValueName = "Speed",
    Callback = function(Value)
        Settings.TrainControl.FlySpeed = Value
    end    
})
 
TrainTab:AddToggle({
    Name = "NoClip",
    Default = false,
    Callback = function(Value)
        Settings.TrainControl.NoClip = Value
    end    
})
 
-- ============================================
-- Miscタブ
-- ============================================
 
MiscTab:AddSection({ Name = "Camera" })
 
MiscTab:AddToggle({
    Name = "3rd Person",
    Default = false,
    Callback = function(value)
        Settings.Misc.ThirdPerson = value
        if value then
            MiscFeature.enableThirdPerson()
        else
            MiscFeature.disableThirdPerson()
        end
    end    
})
 
MiscTab:AddSection({ Name = "Performance" })
 
MiscTab:AddToggle({
    Name = "FPS Booster",
    Default = false,
    Callback = function(value)
        Settings.Misc.FPSBooster = value
        if value then
            MiscFeature.enableFPSBooster()
        else
            MiscFeature.disableFPSBooster()
        end
    end    
})
 
MiscTab:AddSection({ Name = "Utilities" })
 
MiscTab:AddButton({
    Name = "Barrier Destroyer (BETA)",
    Callback = function()
        if State.isBarrierRunning then
            OrionLib:MakeNotification({
                Name = "Running",
                Content = "Already running...",
                Image = "rbxassetid://4483345998",
                Time = 2
            })
            return
        end
 
        State.isBarrierRunning = true
 
        OrionLib:MakeNotification({
            Name = "Solaris Hub",
            Content = "Starting barrier destruction...",
            Image = "rbxassetid://4483345998",
            Time = 2
        })
 
        local success = MiscFeature.ExecuteBarrierDestroyer()
 
        if not success then
            OrionLib:MakeNotification({
                Name = "Retry",
                Content = "Try again...",
                Image = "rbxassetid://4483345998",
                Time = 2
            })
            task.wait(0.5)
            MiscFeature.ExecuteBarrierDestroyer()
        end
 
        State.isBarrierRunning = false
    end    
})
 
-- ============================================
-- Keybindタブ
-- ============================================
 
KeybindTab:AddBind({
    Name = "Silent Aim Toggle",
    Default = Enum.KeyCode.Q,
    Hold = false,
    Callback = function()
        Settings.PvP.SilentAimEnabled = not Settings.PvP.SilentAimEnabled
        SilentAimToggle:Set(Settings.PvP.SilentAimEnabled)
 
        if Settings.PvP.SilentAimEnabled and not State.CameraInitialized then
            State.CameraClone = Camera:Clone()
            State.CameraClone.Parent = workspace
            State.CameraClone.Name = "SilentCamera"
            State.CameraClone.CFrame = Camera.CFrame
            workspace.CurrentCamera = State.CameraClone
            State.CameraInitialized = true
        end
 
        local statusText = Settings.PvP.SilentAimEnabled and "On" or "Off"
 
        OrionLib:MakeNotification({
            Name = "Silent Aim",
            Content = "Silent Aim: " .. statusText,
            Image = "rbxassetid://4483345998",
            Time = 2
        })
    end    
})
 
CRTab:AddParagraph(
    "Solaris Hub owner",
    "LOL"
)
 
CRTab:AddParagraph(
    "Train Control Source",
    "雪見だいふくの空箱"
)
 
CRTab:AddParagraph(
    "Silent Aim & Trigger Bot Source",
    "VF_owner"
)
 
-- ============================================
-- UI初期化
-- ============================================
OrionLib:Init()
