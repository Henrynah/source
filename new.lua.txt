-- Syu_hub v10.5: 完全統合拡張版 (完全版)
-- Phoenix Hubを基盤に全75機能を完全統合

-- サービス初期化
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local Debris = game:GetService("Debris")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")
local SoundService = game:GetService("SoundService")
local StarterGui = game:GetService("StarterGui")

-- リモートイベント
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
local GameCorrectionEvents = ReplicatedStorage:WaitForChild("GameCorrectionEvents")

local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local Struggle = CharacterEvents:WaitForChild("Struggle")
local DestroyToy = MenuToys:WaitForChild("DestroyToy")
local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
local DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")
local CreatureGrab = GrabEvents:FindFirstChild("CreatureGrab") or GrabEvents:WaitForChild("CreatureGrab", 1)
local CreatureDrop = GrabEvents:FindFirstChild("CreatureDrop") or GrabEvents:WaitForChild("CreatureDrop", 1)

-- ローカルプレイヤー
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
LocalPlayer.CharacterAdded:Connect(function(newChar)
    Character = newChar
end)

-- Rayfield UI ロード
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
    Name = "Syu_hub v10.5 | 完全拡張版",
    LoadingTitle = "Syu_hub システム起動中...",
    LoadingSubtitle = "全75機能を統合",
    ConfigurationSaving = { Enabled = true, Folder = "SyuHubData" },
    KeySystem = false,
})

-- グローバル状態管理
local State = {
    -- ⚔️ 戦闘システム
    Combat = {
        StrengthPower = 400,
        MaxStrength = 10000,
        ActiveGrabType = "Normal",
        GrabDamageScale = 1.0,
        BurnGrabEnabled = false,
        RagdollGrabEnabled = false,
        DeathGrabEnabled = false,
        MasslessGrabEnabled = false,
        NoClipGrabEnabled = false,
        UnweldGrabEnabled = false,
        PerspectiveDistance = 100,
        BlobmanGrabEnabled = false,
        TargetPlayer = nil,
        AutoLockEnabled = false,
        AutoBringEnabled = false,
        KickPower = 1000,
        AutoKickEnabled = false,
        WhitelistFriends = {},
        DestroyServerEnabled = false,
        SuperStrengthEnabled = false
    },
    
    -- 🛡️ 保護システム
    Protection = {
        AntiGrabEnabled = false,
        AntiFlingEnabled = false,
        AntiExplosionEnabled = false,
        AntiStunEnabled = false,
        AntiLagEnabled = false,
        AntiKickEnabled = false,
        AntiVoidEnabled = false,
        InvisibilityEnabled = false,
        PremiumAntiGrabEnabled = false,
        AntiSnowballEnabled = false,
        AntiBananaEnabled = false,
        AntiBurnEnabled = false,
        AutoAttackerEnabled = false,
        CounterModEnabled = false
    },
    
    -- 👤 プレイヤー強化
    Enhancements = {
        WalkSpeed = 16,
        WalkSpeedMultiplier = 1.0,
        InfiniteJumpEnabled = false,
        JumpPower = 50,
        NoClipModeEnabled = false,
        FlyEnabled = false,
        FlySpeed = 50
    },
    
    -- 🧠 ESP システム
    ESP = {
        Enabled = false,
        FillColor = Color3.fromRGB(0, 255, 0),
        FillTransparency = 0.5,
        OutlineColor = Color3.fromRGB(255, 255, 255),
        OutlineTransparency = 0,
        HighlightMode = "All",
        ShowIcons = true,
        IconType = "Skull",
        ShowDistance = true,
        ShowHealth = true
    },
    
    -- 💥 雑多な機能
    Misc = {
        CustomExplosionsEnabled = false,
        ExplosionPower = 100,
        TeleportLocations = {},
        QuickTeleportEnabled = false,
        CustomLineEnabled = false,
        LineColor = Color3.fromRGB(255, 255, 255),
        LineThickness = 1,
        QuiKEnabled = false
    },
    
    -- 🖐️ GRAB拡張システム (15機能)
    GrabExtensions = {
        ChargeGrabEnabled = false,
        ChargeMultiplier = 1.0,
        MaxChargeMultiplier = 50,
        SpinUpGrabEnabled = false,
        SpinAngularVelocity = 0,
        InverseGrabEnabled = false,
        InverseVertical = false,
        InverseHorizontal = false,
        JointLockGrabEnabled = false,
        RubberGrabEnabled = false,
        RubberBounce = 0.5,
        AnchorGrabEnabled = false,
        GhostGrabEnabled = false,
        MultiLimbGrabEnabled = false,
        DelayedGrabEnabled = false,
        DelayTime = 1.0,
        SnapshotGrabEnabled = false,
        SnapshotCFrames = {},
        MagnetGrabEnabled = false,
        MagnetRange = 20,
        ScaleGrabEnabled = false,
        ScaleMultiplier = 1.0,
        ReverseMassGrabEnabled = false,
        ChainGrabEnabled = false,
        ChainLinks = {},
        AuthorityGrabEnabled = false
    },
    
    -- 🚀 FLING挙動システム (12機能)
    FlingExtensions = {
        CurveFlingEnabled = false,
        CurveFactor = 0,
        BoomerangFlingEnabled = false,
        ScatterFlingEnabled = false,
        ScatterCount = 3,
        PulseFlingEnabled = false,
        PulsePattern = {},
        PhaseFlingEnabled = false,
        LockOnFlingEnabled = false,
        LockOnTarget = nil,
        OrbitFlingEnabled = false,
        OrbitCenter = nil,
        SpiralFlingEnabled = false,
        SpiralTurns = 2,
        TimeSlowFlingEnabled = false,
        TimeScale = 0.5,
        DirectionalChaosEnabled = false,
        ChaosIntensity = 0.1,
        SnapFlingEnabled = false,
        SnapDistance = 100,
        ReturnToHandEnabled = false
    },
    
    -- 🧱 着地・衝突後システム (8機能)
    ImpactExtensions = {
        ImpactFreezeEnabled = false,
        FreezeDuration = 3.0,
        BounceOverrideEnabled = false,
        BounceMultiplier = 1.0,
        TerrainEmbedEnabled = false,
        ShockwaveRingEnabled = false,
        ShockwaveRadius = 10,
        RagdollLoopEnabled = false,
        VelocityStealEnabled = false,
        StealRadius = 15,
        ExplosionLessKnockbackEnabled = false,
        KnockbackPower = 100,
        AfterimageTrailEnabled = false,
        TrailDuration = 2.0
    },
    
    -- 👁️ 観測・演出・Toy系 (10機能)
    VisualEffects = {
        FlingCamEnabled = false,
        TrajectoryPreviewEnabled = false,
        HitCounterEnabled = false,
        HitCount = 0,
        DistanceRecordEnabled = false,
        MaxDistance = 0,
        GravityDialEnabled = false,
        GravityMultiplier = 1.0,
        SoundPackEnabled = false,
        SoundPackType = "Default",
        ToyHammerEnabled = false,
        ToySlingshotEnabled = false,
        ToyYoYoEnabled = false,
        ChaosButtonEnabled = false,
        AlmostHitFlingEnabled = false,
        PersonalSpaceViolationEnabled = false,
        StareFlingEnabled = false,
        NameCallTrailEnabled = false,
        DelayedImpactAnxietyEnabled = false,
        FakeMissHitEnabled = false,
        HeartbeatSyncEnabled = false,
        WhisperPassEnabled = false,
        MirrorPanicEnabled = false,
        ShadowFirstEnabled = false,
        EyeLevelLockEnabled = false,
        SilentFlingEnabled = false,
        BackstabArcEnabled = false,
        ExpectationBreakerEnabled = false,
        YouNextIndicatorEnabled = false
    },
    
    -- 🧠 数式・物理ガチ勢向け (15機能)
    PhysicsFling = {
        VectorComposerEnabled = false,
        VectorX = 0,
        VectorY = 0,
        VectorZ = 0,
        AngularX = 0,
        AngularY = 0,
        AngularZ = 0,
        ImpulseGraphEditorEnabled = false,
        ImpulseCurve = {},
        MassCurveOverrideEnabled = false,
        AirDragCoefficientEnabled = false,
        AirDrag = 0.1,
        AngularMomentumLockEnabled = false,
        ElasticitySolverEnabled = false,
        GravityGradientEnabled = false,
        RelativisticFlingEnabled = false,
        EnergyConservationToggleEnabled = false,
        FrameStepSimulationEnabled = false,
        PredictiveErrorInjectionEnabled = false,
        MultiBodySolverEnabled = false,
        CollisionNormalVisualizerEnabled = false,
        TerminalVelocityCapEnabled = false,
        TerminalVelocity = 100,
        ReplayDeterminismModeEnabled = false
    },
    
    -- 🎮 UI/操作革命系 (15機能)
    UIFling = {
        RadialFlingWheelEnabled = false,
        GestureFlingEnabled = false,
        OneButtonSmartFlingEnabled = false,
        HoldToPreviewEnabled = false,
        ContextGrabMenuEnabled = false,
        QuickPresetStackEnabled = false,
        TimelineScrubberEnabled = false,
        FlingBookmarkEnabled = false,
        TargetPriorityUIEnabled = false,
        HUDMinimalModeEnabled = false,
        EyeTrackingAssistEnabled = false,
        SoundAsUIEnabled = false,
        AdaptiveSensitivityEnabled = false,
        ErrorForgivenessZoneEnabled = false,
        InstantReplayButtonEnabled = false
    }
}

-- グローバル変数
local Connections = {}
local ESPHighlights = {}
local ActiveCharges = {}
local ActiveSnapshots = {}
local ActiveChains = {}
local ActiveFlingEffects = {}
local ActiveTrails = {}
local Whitelist = {}
local SpawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
local ActiveCameras = {}
local TrajectoryPreviewParts = {}
local HitCounterDisplay = nil
local DistanceRecordDisplay = nil
local RadialWheel = nil
local FlingBookmarks = {}
local ReplayHistory = {}
local QuickPresets = {}

-- Phoenix Hub ユーティリティ関数の継承
local function IsDescendantOf(obj, ancestor)
    local parent = obj.Parent
    while parent do
        if parent == ancestor then
            return true
        end
        parent = parent.Parent
    end
    return false
end

local function DestroyAllConnections()
    for _, connection in pairs(Connections) do
        if connection then
            connection:Disconnect()
        end
    end
    Connections = {}
end

local function FindPartsByName(name)
    local parts = {}
    if Workspace:FindFirstChild("Map") then
        for _, descendant in pairs(Workspace.Map:GetDescendants()) do
            if descendant:IsA("Part") and descendant.Name == name then
                table.insert(parts, descendant)
            end
        end
    end
    return parts
end

local PoisonParts = FindPartsByName("PoisonHurtPart")
local PaintParts = FindPartsByName("PaintPlayerPart")

local function GetClosestPlayer()
    local closest = nil
    local closestDist = math.huge
    if not Character or not Character:FindFirstChild("HumanoidRootPart") then return nil end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if dist < closestDist then
                closestDist = dist
                closest = player
            end
        end
    end
    return closest
end

local function GetPlayerByName(name)
    for _, player in pairs(Players:GetPlayers()) do
        if player.Name == name or player.DisplayName == name then
            return player
        end
    end
    return nil
end

-- 基本機能の実装
local function ApplySuperStrength(targetPart, direction)
    if State.Combat.StrengthPower > 0 and State.Combat.SuperStrengthEnabled then
        local bodyVelocity = targetPart:FindFirstChild("SuperStrengthVelocity") or Instance.new("BodyVelocity")
        bodyVelocity.Name = "SuperStrengthVelocity"
        bodyVelocity.Velocity = direction * State.Combat.StrengthPower
        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVelocity.Parent = targetPart
        Debris:AddItem(bodyVelocity, 0.1)
    end
end

-- グラブタイプの実装
local function ApplyGrabEffect(targetPart, grabType)
    if grabType == "Poison" then
        for _, poisonPart in pairs(PoisonParts) do
            poisonPart.Size = Vector3.new(2, 2, 2)
            poisonPart.Transparency = 1
            poisonPart.Position = targetPart.Position
        end
        task.wait(0.1)
        for _, poisonPart in pairs(PoisonParts) do
            poisonPart.Position = Vector3.new(0, -200, 0)
        end
        
    elseif grabType == "Burn" and State.Combat.BurnGrabEnabled then
        local fire = Instance.new("Fire")
        fire.Size = 5
        fire.Heat = 10
        fire.Color = Color3.fromRGB(255, 100, 0)
        fire.SecondaryColor = Color3.fromRGB(255, 255, 0)
        fire.Parent = targetPart
        Debris:AddItem(fire, 5)
        
    elseif grabType == "Ragdoll" and State.Combat.RagdollGrabEnabled then
        RagdollRemote:FireServer(targetPart, 0)
        
    elseif grabType == "Death" and State.Combat.DeathGrabEnabled then
        local humanoid = targetPart.Parent:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.Health = 0
        end
        
    elseif grabType == "Massless" and State.Combat.MasslessGrabEnabled then
        targetPart.Massless = true
        
    elseif grabType == "NoClip" and State.Combat.NoClipGrabEnabled then
        for _, part in pairs(targetPart.Parent:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
        
    elseif grabType == "Unweld" and State.Combat.UnweldGrabEnabled then
        for _, weld in pairs(targetPart.Parent:GetDescendants()) do
            if weld:IsA("Weld") or weld:IsA("WeldConstraint") then
                weld:Destroy()
            end
        end
        
    elseif grabType == "Perspective" then
        local camera = Workspace.CurrentCamera
        if camera then
            camera.CFrame = targetPart.CFrame * CFrame.new(0, 0, State.Combat.PerspectiveDistance)
        end
    end
end

-- 保護システムの実装
local function SetupProtectionSystems()
    -- Anti-Grab
    if State.Protection.AntiGrabEnabled then
        local antiGrabConnection = RunService.Heartbeat:Connect(function()
            if Character and Character:FindFirstChild("Head") and Character.Head:FindFirstChild("PartOwner") then
                Struggle:FireServer()
                GameCorrectionEvents.StopAllVelocity:FireServer()
                
                for _, part in pairs(Character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = true
                    end
                end
                
                while Character.Head.PartOwner.Value ~= LocalPlayer.Name do
                    task.wait()
                end
                
                for _, part in pairs(Character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = false
                    end
                end
            end
        end)
        table.insert(Connections, antiGrabConnection)
    end
    
    -- Anti-Fling
    if State.Protection.AntiFlingEnabled then
        local antiFlingConnection = RunService.Heartbeat:Connect(function()
            if Character and Character:FindFirstChild("HumanoidRootPart") then
                local vel = Character.HumanoidRootPart.Velocity
                if vel.Magnitude > 500 then
                    Character.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
                    Character.HumanoidRootPart.RotVelocity = Vector3.new(0, 0, 0)
                end
            end
        end)
        table.insert(Connections, antiFlingConnection)
    end
    
    -- Anti-Explosion
    if State.Protection.AntiExplosionEnabled then
        local antiExplosionConnection = workspace.DescendantAdded:Connect(function(descendant)
            if descendant:IsA("Explosion") then
                descendant.BlastPressure = 0
                descendant.BlastRadius = 0
                descendant.DestroyJointRadiusPercent = 0
            end
        end)
        table.insert(Connections, antiExplosionConnection)
    end
    
    -- Invisibility
    if State.Protection.InvisibilityEnabled then
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Transparency = 1
            elseif part:IsA("Decal") then
                part.Transparency = 1
            end
        end
    end
    
    -- Premium Anti-Grab (Gucci)
    if State.Protection.PremiumAntiGrabEnabled then
        local premiumAntiGrab = RunService.Heartbeat:Connect(function()
            if Character then
                for _, part in pairs(Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        local velocity = part.Velocity
                        if velocity.Magnitude > 100 then
                            part.Velocity = velocity * 0.5
                        end
                    end
                end
            end
        end)
        table.insert(Connections, premiumAntiGrab)
    end
    
    -- Auto-Attacker
    if State.Protection.AutoAttackerEnabled then
        local autoAttacker = RunService.Heartbeat:Connect(function()
            local closest = GetClosestPlayer()
            if closest and closest.Character and closest.Character:FindFirstChild("HumanoidRootPart") then
                ApplySuperStrength(closest.Character.HumanoidRootPart, Vector3.new(0, 1, 0))
            end
        end)
        table.insert(Connections, autoAttacker)
    end
end

-- ESP システムの実装
local function UpdateESP()
    for _, highlight in pairs(ESPHighlights) do
        highlight:Destroy()
    end
    ESPHighlights = {}
    
    if not State.ESP.Enabled then return end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local shouldHighlight = false
            
            if State.ESP.HighlightMode == "All" then
                shouldHighlight = true
            elseif State.ESP.HighlightMode == "Enemies" then
                shouldHighlight = not table.find(State.Combat.WhitelistFriends, player.Name)
            elseif State.ESP.HighlightMode == "Friends" then
                shouldHighlight = table.find(State.Combat.WhitelistFriends, player.Name)
            elseif State.ESP.HighlightMode == "Target" then
                shouldHighlight = player.Name == State.Combat.TargetPlayer
            end
            
            if shouldHighlight then
                local highlight = Instance.new("Highlight")
                highlight.Adornee = player.Character
                highlight.FillColor = State.ESP.FillColor
                highlight.FillTransparency = State.ESP.FillTransparency
                highlight.OutlineColor = State.ESP.OutlineColor
                highlight.OutlineTransparency = State.ESP.OutlineTransparency
                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                highlight.Parent = player.Character
                
                table.insert(ESPHighlights, highlight)
                
                -- ESP Icon
                if State.ESP.ShowIcons then
                    local billboard = Instance.new("BillboardGui")
                    billboard.Size = UDim2.new(4, 0, 4, 0)
                    billboard.AlwaysOnTop = true
                    billboard.Parent = player.Character.Head or player.Character.PrimaryPart
                    
                    local icon = Instance.new("ImageLabel")
                    icon.Size = UDim2.new(1, 0, 1, 0)
                    icon.BackgroundTransparency = 1
                    
                    local iconId = 7734068321 -- Default skull
                    if State.ESP.IconType == "Player" then
                        iconId = 7734069243
                    elseif State.ESP.IconType == "Enemy" then
                        iconId = 7734068321
                    elseif State.ESP.IconType == "Friend" then
                        iconId = 7734069999
                    end
                    
                    icon.Image = "rbxassetid://" .. iconId
                    icon.Parent = billboard
                    
                    table.insert(ESPHighlights, billboard)
                end
                
                -- Distance Display
                if State.ESP.ShowDistance and Character and Character:FindFirstChild("HumanoidRootPart") then
                    local distance = (Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                    
                    local distanceBillboard = Instance.new("BillboardGui")
                    distanceBillboard.Size = UDim2.new(0, 100, 0, 50)
                    distanceBillboard.AlwaysOnTop = true
                    distanceBillboard.Parent = player.Character.Head or player.Character.PrimaryPart
                    
                    local distanceLabel = Instance.new("TextLabel")
                    distanceLabel.Size = UDim2.new(1, 0, 1, 0)
                    distanceLabel.BackgroundTransparency = 1
                    distanceLabel.TextColor3 = Color3.new(1, 1, 1)
                    distanceLabel.Text = math.floor(distance) .. " studs"
                    distanceLabel.Parent = distanceBillboard
                    
                    table.insert(ESPHighlights, distanceBillboard)
                end
            end
        end
    end
end

-- プレイヤー強化システム
local function UpdateEnhancements()
    if not Character then return end
    
    local humanoid = Character:FindFirstChild("Humanoid")
    if humanoid then
        -- Walk Speed
        if State.Enhancements.WalkSpeedMultiplier ~= 1.0 then
            humanoid.WalkSpeed = 16 * State.Enhancements.WalkSpeedMultiplier
        else
            humanoid.WalkSpeed = State.Enhancements.WalkSpeed
        end
        
        -- Jump Power
        if State.Enhancements.InfiniteJumpEnabled then
            humanoid.JumpPower = State.Enhancements.JumpPower
        end
    end
    
    -- NoClip
    if State.Enhancements.NoClipModeEnabled then
        for _, part in pairs(Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
    
    -- Fly
    if State.Enhancements.FlyEnabled then
        local bodyVelocity = Character:FindFirstChild("FlyVelocity") or Instance.new("BodyVelocity")
        bodyVelocity.Name = "FlyVelocity"
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(0, math.huge, 0)
        bodyVelocity.Parent = Character.PrimaryPart or Character.HumanoidRootPart
        
        local flying = false
        UserInputService.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Space then
                flying = true
                while flying do
                    bodyVelocity.Velocity = Vector3.new(0, State.Enhancements.FlySpeed, 0)
                    task.wait()
                end
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.Space then
                flying = false
                bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            end
        end)
    end
end

-- グラブ拡張機能の実装
local function ApplyGrabExtensions(targetPart)
    -- Charge Grab
    if State.GrabExtensions.ChargeGrabEnabled then
        local chargeStart = tick()
        local chargeIndicator = Instance.new("Part")
        chargeIndicator.Size = Vector3.new(1, 1, 1)
        chargeIndicator.Transparency = 0.5
        chargeIndicator.Color = Color3.fromHSV(0, 1, 1)
        chargeIndicator.Material = Enum.Material.Neon
        chargeIndicator.CFrame = targetPart.CFrame
        chargeIndicator.Anchored = true
        chargeIndicator.CanCollide = false
        chargeIndicator.Parent = Workspace
        
        local chargeConnection = RunService.Heartbeat:Connect(function()
            local chargeTime = tick() - chargeStart
            local multiplier = math.min(1 + (chargeTime * 5), State.GrabExtensions.MaxChargeMultiplier)
            
            chargeIndicator.Color = Color3.fromHSV(multiplier / 50, 1, 1)
            chargeIndicator.Size = Vector3.new(1, 1, 1) * (1 + multiplier * 0.1)
            
            State.GrabExtensions.ChargeMultiplier = multiplier
        end)
        
        table.insert(ActiveCharges, {
            Part = targetPart,
            Connection = chargeConnection,
            Indicator = chargeIndicator
        })
    end
    
    -- Spin-Up Grab
    if State.GrabExtensions.SpinUpGrabEnabled then
        local bodyAngularVelocity = Instance.new("BodyAngularVelocity")
        bodyAngularVelocity.AngularVelocity = Vector3.new(0, State.GrabExtensions.SpinAngularVelocity, 0)
        bodyAngularVelocity.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bodyAngularVelocity.Parent = targetPart
        
        table.insert(ActiveCharges, {
            Part = targetPart,
            Velocity = bodyAngularVelocity
        })
    end
    
    -- Inverse Grab
    if State.GrabExtensions.InverseGrabEnabled then
        local originalPosition = targetPart.Position
        if State.GrabExtensions.InverseVertical then
            targetPart.CFrame = CFrame.new(
                targetPart.Position.X,
                originalPosition.Y * -1,
                targetPart.Position.Z
            )
        end
        if State.GrabExtensions.InverseHorizontal then
            targetPart.CFrame = CFrame.new(
                targetPart.Position.X * -1,
                targetPart.Position.Y,
                targetPart.Position.Z * -1
            )
        end
    end
    
    -- Joint Lock Grab
    if State.GrabExtensions.JointLockGrabEnabled then
        for _, constraint in pairs(targetPart:GetDescendants()) do
            if constraint:IsA("HingeConstraint") or constraint:IsA("BallSocketConstraint") then
                constraint.Enabled = false
            end
        end
    end
    
    -- Rubber Grab
    if State.GrabExtensions.RubberGrabEnabled then
        targetPart.Elasticity = State.GrabExtensions.RubberBounce
        targetPart.Friction = 0
    end
    
    -- Anchor Grab
    if State.GrabExtensions.AnchorGrabEnabled then
        targetPart.Anchored = true
    end
    
    -- Ghost Grab
    if State.GrabExtensions.GhostGrabEnabled then
        targetPart.Transparency = 0.5
        targetPart.CanCollide = false
    end
    
    -- Multi-Limb Grab
    if State.GrabExtensions.MultiLimbGrabEnabled and targetPart.Parent:IsA("Model") then
        local model = targetPart.Parent
        for _, part in pairs(model:GetDescendants()) do
            if part:IsA("BasePart") then
                local weld = Instance.new("WeldConstraint")
                weld.Part0 = targetPart
                weld.Part1 = part
                weld.Parent = part
                Debris:AddItem(weld, 5)
            end
        end
    end
    
    -- Delayed Grab
    if State.GrabExtensions.DelayedGrabEnabled then
        task.wait(State.GrabExtensions.DelayTime)
    end
    
    -- Snapshot Grab
    if State.GrabExtensions.SnapshotGrabEnabled then
        State.GrabExtensions.SnapshotCFrames[targetPart] = targetPart.CFrame
        table.insert(ActiveSnapshots, targetPart)
    end
    
    -- Magnet Grab
    if State.GrabExtensions.MagnetGrabEnabled then
        local magnetConnection = RunService.Heartbeat:Connect(function()
            for _, part in pairs(Workspace:GetDescendants()) do
                if part:IsA("BasePart") and not IsDescendantOf(part, Character) then
                    local distance = (part.Position - targetPart.Position).Magnitude
                    if distance < State.GrabExtensions.MagnetRange then
                        local direction = (targetPart.Position - part.Position).Unit
                        part.Velocity = direction * 50
                    end
                end
            end
        end)
        table.insert(Connections, magnetConnection)
    end
    
    -- Scale Grab
    if State.GrabExtensions.ScaleGrabEnabled then
        local originalSize = targetPart.Size
        targetPart.Size = originalSize * State.GrabExtensions.ScaleMultiplier
    end
    
    -- Reverse Mass Grab
    if State.GrabExtensions.ReverseMassGrabEnabled then
        targetPart.Mass = 1 / targetPart.Mass
    end
    
    -- Chain Grab
    if State.GrabExtensions.ChainGrabEnabled then
        table.insert(State.GrabExtensions.ChainLinks, targetPart)
        if #State.GrabExtensions.ChainLinks > 1 then
            for i = 1, #State.GrabExtensions.ChainLinks - 1 do
                local rope = Instance.new("RopeConstraint")
                rope.Attachment0 = Instance.new("Attachment", State.GrabExtensions.ChainLinks[i])
                rope.Attachment1 = Instance.new("Attachment", targetPart)
                rope.Length = 5
                rope.Parent = Workspace
                table.insert(ActiveChains, rope)
            end
        end
    end
    
    -- Authority Grab
    if State.GrabExtensions.AuthorityGrabEnabled then
        SetNetworkOwner:FireServer(targetPart, targetPart.CFrame)
        if targetPart:FindFirstChild("PartOwner") then
            targetPart.PartOwner.Value = LocalPlayer.Name
        end
    end
end

-- FLING拡張機能の実装
local function ApplyFlingExtensions(targetPart, direction)
    -- Curve Fling
    if State.FlingExtensions.CurveFlingEnabled then
        local curveDirection = Vector3.new(
            direction.X + math.sin(tick()) * State.FlingExtensions.CurveFactor,
            direction.Y,
            direction.Z + math.cos(tick()) * State.FlingExtensions.CurveFactor
        )
        direction = curveDirection.Unit
    end
    
    -- Boomerang Fling
    if State.FlingExtensions.BoomerangFlingEnabled then
        local startPos = targetPart.Position
        local startTime = tick()
        
        local boomerangConnection = RunService.Heartbeat:Connect(function()
            local elapsed = tick() - startTime
            
            if elapsed < 2 then
                -- Forward phase
                targetPart.Velocity = direction * 100 * (1 - elapsed/2)
            elseif elapsed < 4 then
                -- Return phase
                local returnDir = (startPos - targetPart.Position).Unit
                targetPart.Velocity = returnDir * 100 * ((elapsed-2)/2)
            else
                boomerangConnection:Disconnect()
            end
        end)
        table.insert(Connections, boomerangConnection)
    end
    
    -- Scatter Fling
    if State.FlingExtensions.ScatterFlingEnabled then
        for i = 1, State.FlingExtensions.ScatterCount do
            local scatterPart = targetPart:Clone()
            scatterPart.Parent = Workspace
            scatterPart.Velocity = direction * 100 + Vector3.new(
                math.random(-50, 50),
                math.random(-50, 50),
                math.random(-50, 50)
            )
            Debris:AddItem(scatterPart, 5)
        end
    end
    
    -- Pulse Fling
    if State.FlingExtensions.PulseFlingEnabled then
        local pulseTime = 0
        local pulseConnection = RunService.Heartbeat:Connect(function()
            pulseTime = pulseTime + 0.1
            local pulse = math.sin(pulseTime * 5) * 0.5 + 0.5
            targetPart.Velocity = direction * 100 * pulse
        end)
        table.insert(Connections, pulseConnection)
    end
    
    -- Phase Fling
    if State.FlingExtensions.PhaseFlingEnabled then
        targetPart.CanCollide = false
        task.wait(1)
        targetPart.CanCollide = true
    end
    
    -- Lock-On Fling
    if State.FlingExtensions.LockOnFlingEnabled and State.FlingExtensions.LockOnTarget then
        local target = GetPlayerByName(State.FlingExtensions.LockOnTarget)
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            direction = (target.Character.HumanoidRootPart.Position - targetPart.Position).Unit
        end
    end
    
    -- Orbit Fling
    if State.FlingExtensions.OrbitFlingEnabled and State.FlingExtensions.OrbitCenter then
        local center = State.FlingExtensions.OrbitCenter
        local orbitTime = 0
        local orbitConnection = RunService.Heartbeat:Connect(function()
            orbitTime = orbitTime + 0.1
            local angle = orbitTime * 2
            local orbitPos = center + Vector3.new(
                math.cos(angle) * 10,
                0,
                math.sin(angle) * 10
            )
            targetPart.Velocity = (orbitPos - targetPart.Position).Unit * 50
        end)
        table.insert(Connections, orbitConnection)
    end
    
    -- Spiral Fling
    if State.FlingExtensions.SpiralFlingEnabled then
        local spiralTime = 0
        local spiralConnection = RunService.Heartbeat:Connect(function()
            spiralTime = spiralTime + 0.1
            local spiral = Vector3.new(
                math.cos(spiralTime * State.FlingExtensions.SpiralTurns),
                spiralTime * 0.1,
                math.sin(spiralTime * State.FlingExtensions.SpiralTurns)
            )
            targetPart.Velocity = direction * 100 + spiral * 20
        end)
        table.insert(Connections, spiralConnection)
    end
    
    -- Time-Slow Fling
    if State.FlingExtensions.TimeSlowFlingEnabled then
        local originalVelocity = targetPart.Velocity
        targetPart.Velocity = originalVelocity * State.FlingExtensions.TimeScale
    end
    
    -- Directional Chaos
    if State.FlingExtensions.DirectionalChaosEnabled then
        local chaos = Vector3.new(
            math.random() * 2 - 1,
            math.random() * 2 - 1,
            math.random() * 2 - 1
        ) * State.FlingExtensions.ChaosIntensity
        direction = (direction + chaos).Unit
    end
    
    -- Snap Fling
    if State.FlingExtensions.SnapFlingEnabled then
        targetPart.CFrame = targetPart.CFrame + direction * State.FlingExtensions.SnapDistance
    end
    
    -- Return-to-Hand
    if State.FlingExtensions.ReturnToHandEnabled then
        local startPos = targetPart.Position
        task.wait(2)
        if Character and Character:FindFirstChild("HumanoidRootPart") then
            targetPart.Velocity = (Character.HumanoidRootPart.Position - targetPart.Position).Unit * 100
        end
    end
    
    return direction
end

-- 着地・衝突後システムの実装
local function ApplyImpactEffects(targetPart, impactPosition)
    -- Impact Freeze
    if State.ImpactExtensions.ImpactFreezeEnabled then
        targetPart.Anchored = true
        task.wait(State.ImpactExtensions.FreezeDuration)
        targetPart.Anchored = false
    end
    
    -- Bounce Override
    if State.ImpactExtensions.BounceOverrideEnabled then
        targetPart.Elasticity = State.ImpactExtensions.BounceMultiplier
    end
    
    -- Terrain Embed
    if State.ImpactExtensions.TerrainEmbedEnabled then
        local terrain = Workspace:FindFirstChildOfClass("Terrain")
        if terrain then
            terrain:FillBlock(
                Region3.new(
                    impactPosition - Vector3.new(1, 1, 1),
                    impactPosition + Vector3.new(1, 1, 1)
                ),
                Enum.Material.Rock
            )
        end
    end
    
    -- Shockwave Ring
    if State.ImpactExtensions.ShockwaveRingEnabled then
        local shockwave = Instance.new("Part")
        shockwave.Size = Vector3.new(1, 1, 1)
        shockwave.Transparency = 0.5
        shockwave.Color = Color3.new(1, 0, 0)
        shockwave.Material = Enum.Material.Neon
        shockwave.CFrame = CFrame.new(impactPosition)
        shockwave.Anchored = true
        shockwave.CanCollide = false
        shockwave.Parent = Workspace
        
        local tween = TweenService:Create(shockwave, TweenInfo.new(1), {
            Size = Vector3.new(State.ImpactExtensions.ShockwaveRadius * 2, 1, State.ImpactExtensions.ShockwaveRadius * 2),
            Transparency = 1
        })
        tween:Play()
        Debris:AddItem(shockwave, 2)
    end
    
    -- Ragdoll Loop
    if State.ImpactExtensions.RagdollLoopEnabled then
        local humanoid = targetPart.Parent:FindFirstChild("Humanoid")
        if humanoid then
            while true do
                RagdollRemote:FireServer(targetPart, 0)
                task.wait(0.5)
            end
        end
    end
    
    -- Velocity Steal
    if State.ImpactExtensions.VelocityStealEnabled then
        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("BasePart") and part ~= targetPart then
                local distance = (part.Position - impactPosition).Magnitude
                if distance < State.ImpactExtensions.StealRadius then
                    local stolenVelocity = part.Velocity
                    part.Velocity = Vector3.new(0, 0, 0)
                    targetPart.Velocity = targetPart.Velocity + stolenVelocity
                end
            end
        end
    end
    
    -- Explosion-less Knockback
    if State.ImpactExtensions.ExplosionLessKnockbackEnabled then
        for _, part in pairs(Workspace:GetDescendants()) do
            if part:IsA("BasePart") and part ~= targetPart then
                local distance = (part.Position - impactPosition).Magnitude
                if distance < 20 then
                    local direction = (part.Position - impactPosition).Unit
                    part.Velocity = direction * State.ImpactExtensions.KnockbackPower
                end
            end
        end
    end
    
    -- Afterimage Trail
    if State.ImpactExtensions.AfterimageTrailEnabled then
        for i = 1, 10 do
            local afterimage = targetPart:Clone()
            afterimage.Transparency = 0.5
            afterimage.CFrame = targetPart.CFrame
            afterimage.Anchored = true
            afterimage.CanCollide = false
            afterimage.Parent = Workspace
            
            local tween = TweenService:Create(afterimage, TweenInfo.new(State.ImpactExtensions.TrailDuration), {
                Transparency = 1,
                Size = Vector3.new(0, 0, 0)
            })
            tween:Play()
            Debris:AddItem(afterimage, State.ImpactExtensions.TrailDuration + 1)
            
            task.wait(0.1)
        end
    end
end

-- 観測・演出・Toy系の実装
local function CreateFlingCamera(targetPart)
    if State.VisualEffects.FlingCamEnabled then
        local camera = Instance.new("Camera")
        camera.CameraType = Enum.CameraType.Scriptable
        camera.CFrame = CFrame.new(targetPart.Position + Vector3.new(0, 5, 10), targetPart.Position)
        camera.Parent = Workspace
        
        local originalCamera = Workspace.CurrentCamera
        Workspace.CurrentCamera = camera
        
        local cameraFollow = RunService.RenderStepped:Connect(function()
            if targetPart and targetPart.Parent then
                camera.CFrame = CFrame.new(targetPart.Position + Vector3.new(0, 5, 10), targetPart.Position)
            else
                cameraFollow:Disconnect()
                Workspace.CurrentCamera = originalCamera
                camera:Destroy()
            end
        end)
        
        table.insert(ActiveCameras, {Camera = camera, Connection = cameraFollow})
    end
end

local function ShowTrajectoryPreview(startPos, direction, power)
    if State.VisualEffects.TrajectoryPreviewEnabled then
        -- 軌道計算
        local points = {}
        local gravity = workspace.Gravity * (State.VisualEffects.GravityMultiplier or 1)
        local velocity = direction * power
        local timestep = 0.1
        
        for t = 0, 5, timestep do
            local point = startPos + (velocity * t) + Vector3.new(0, -0.5 * gravity * t * t, 0)
            table.insert(points, point)
            
            if point.Y < workspace.FallenPartsDestroyHeight then
                break
            end
        end
        
        -- 軌道表示
        for i, point in ipairs(points) do
            if i < #points then
                local nextPoint = points[i + 1]
                local distance = (point - nextPoint).Magnitude
                
                local part = Instance.new("Part")
                part.Size = Vector3.new(0.1, 0.1, distance)
                part.CFrame = CFrame.lookAt(point, nextPoint) * CFrame.new(0, 0, -distance/2)
                part.Color = Color3.fromHSV(i/#points, 1, 1)
                part.Transparency = 0.3
                part.Material = Enum.Material.Neon
                part.Anchored = true
                part.CanCollide = false
                part.Parent = Workspace
                
                table.insert(TrajectoryPreviewParts, part)
                Debris:AddItem(part, 3)
            end
        end
    end
end

local function UpdateHitCounter()
    if State.VisualEffects.HitCounterEnabled then
        if not HitCounterDisplay then
            local screenGui = Instance.new("ScreenGui")
            screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
            
            HitCounterDisplay = Instance.new("TextLabel")
            HitCounterDisplay.Size = UDim2.new(0, 200, 0, 50)
            HitCounterDisplay.Position = UDim2.new(0, 10, 0, 10)
            HitCounterDisplay.BackgroundTransparency = 0.5
            HitCounterDisplay.BackgroundColor3 = Color3.new(0, 0, 0)
            HitCounterDisplay.TextColor3 = Color3.new(1, 1, 1)
            HitCounterDisplay.Text = "Hits: 0"
            HitCounterDisplay.TextSize = 24
            HitCounterDisplay.Parent = screenGui
        end
        
        HitCounterDisplay.Text = "Hits: " .. State.VisualEffects.HitCount
    end
end

local function UpdateDistanceRecord(distance)
    if State.VisualEffects.DistanceRecordEnabled then
        if distance > State.VisualEffects.MaxDistance then
            State.VisualEffects.MaxDistance = distance
            
            if not DistanceRecordDisplay then
                local screenGui = Instance.new("ScreenGui")
                screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
                
                DistanceRecordDisplay = Instance.new("TextLabel")
                DistanceRecordDisplay.Size = UDim2.new(0, 200, 0, 50)
                DistanceRecordDisplay.Position = UDim2.new(1, -210, 0, 10)
                DistanceRecordDisplay.BackgroundTransparency = 0.5
                DistanceRecordDisplay.BackgroundColor3 = Color3.new(0, 0, 0)
                DistanceRecordDisplay.TextColor3 = Color3.new(1, 1, 1)
                DistanceRecordDisplay.Text = "Record: 0"
                DistanceRecordDisplay.TextSize = 24
                DistanceRecordDisplay.Parent = screenGui
            end
            
            DistanceRecordDisplay.Text = "Record: " .. math.floor(distance) .. " studs"
        end
    end
end

local function PlaySoundEffect(soundType)
    if State.VisualEffects.SoundPackEnabled then
        local sound = Instance.new("Sound")
        sound.Parent = Workspace
        
        if soundType == "Grab" then
            sound.SoundId = "rbxassetid://9125519564" -- Grab sound
        elseif soundType == "Fling" then
            sound.SoundId = "rbxassetid://9125521080" -- Fling sound
        elseif soundType == "Impact" then
            sound.SoundId = "rbxassetid://9125522678" -- Impact sound
        end
        
        sound:Play()
        Debris:AddItem(sound, 5)
    end
end

local function CreateToyHammer()
    if State.VisualEffects.ToyHammerEnabled then
        local hammer = Instance.new("Part")
        hammer.Size = Vector3.new(1, 5, 1)
        hammer.Color = Color3.fromRGB(139, 69, 19) -- Wood color
        hammer.Material = Enum.Material.Wood
        hammer.CFrame = Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
        hammer.Parent = Workspace
        
        local hammerWeld = Instance.new("WeldConstraint")
        hammerWeld.Part0 = Character.HumanoidRootPart
        hammerWeld.Part1 = hammer
        hammerWeld.Parent = hammer
        
        UserInputService.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.E then
                -- Hammer swing
                local swingTween = TweenService:Create(hammer, TweenInfo.new(0.2), {
                    CFrame = hammer.CFrame * CFrame.Angles(0, 0, math.rad(-90))
                })
                swingTween:Play()
                
                task.wait(0.2)
                
                local returnTween = TweenService:Create(hammer, TweenInfo.new(0.2), {
                    CFrame = hammer.CFrame * CFrame.Angles(0, 0, math.rad(90))
                })
                returnTween:Play()
            end
        end)
    end
end

local function CreateToySlingshot()
    if State.VisualEffects.ToySlingshotEnabled then
        local slingshot = Instance.new("Part")
        slingshot.Size = Vector3.new(1, 2, 1)
        slingshot.Color = Color3.fromRGB(139, 69, 19)
        slingshot.Material = Enum.Material.Wood
        slingshot.CFrame = Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
        slingshot.Parent = Workspace
        
        local band1 = Instance.new("Part")
        band1.Size = Vector3.new(0.2, 0.2, 2)
        band1.Color = Color3.new(1, 0, 0)
        band1.Material = Enum.Material.Neon
        band1.CFrame = slingshot.CFrame * CFrame.new(-0.5, 0.5, 0)
        band1.Parent = Workspace
        
        local band2 = Instance.new("Part")
        band2.Size = Vector3.new(0.2, 0.2, 2)
        band2.Color = Color3.new(1, 0, 0)
        band2.Material = Enum.Material.Neon
        band2.CFrame = slingshot.CFrame * CFrame.new(0.5, 0.5, 0)
        band2.Parent = Workspace
        
        UserInputService.InputBegan:Connect(function(input)
            if input.KeyCode == Enum.KeyCode.R then
                local projectile = Instance.new("Part")
                projectile.Size = Vector3.new(0.5, 0.5, 0.5)
                projectile.Shape = Enum.PartType.Ball
                projectile.Color = Color3.new(1, 1, 0)
                projectile.Material = Enum.Material.Neon
                projectile.CFrame = slingshot.CFrame * CFrame.new(0, 0.5, -2)
                projectile.Velocity = (Workspace.CurrentCamera.CFrame.LookVector * 200) + Vector3.new(0, 50, 0)
                projectile.Parent = Workspace
                Debris:AddItem(projectile, 5)
            end
        end)
    end
end

local function CreateToyYoYo()
    if State.VisualEffects.ToyYoYoEnabled then
        local yoyo = Instance.new("Part")
        yoyo.Size = Vector3.new(0.5, 0.5, 0.5)
        yoyo.Shape = Enum.PartType.Ball
        yoyo.Color = Color3.new(1, 0, 0)
        yoyo.Material = Enum.Material.Neon
        yoyo.CFrame = Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -2)
        yoyo.Parent = Workspace
        
        local rope = Instance.new("RopeConstraint")
        rope.Attachment0 = Instance.new("Attachment", Character.HumanoidRootPart)
        rope.Attachment1 = Instance.new("Attachment", yoyo)
        rope.Length = 10
        rope.Parent = Workspace
        
        local yoyoTime = 0
        local yoyoConnection = RunService.Heartbeat:Connect(function()
            yoyoTime = yoyoTime + 0.1
            local swing = math.sin(yoyoTime * 5) * 5
            yoyo.CFrame = Character.HumanoidRootPart.CFrame * CFrame.new(swing, -math.abs(swing), -5)
        end)
        
        table.insert(Connections, yoyoConnection)
    end
end

local function ApplyChaosButton()
    if State.VisualEffects.ChaosButtonEnabled then
        math.randomseed(tick())
        
        -- Random grab type
        local grabTypes = {"Poison", "Burn", "Ragdoll", "Death", "Massless", "NoClip"}
        State.Combat.ActiveGrabType = grabTypes[math.random(1, #grabTypes)]
        
        -- Random fling extension
        local extensions = {"CurveFling", "BoomerangFling", "ScatterFling", "PulseFling"}
        for _, ext in pairs(extensions) do
            State.FlingExtensions[ext .. "Enabled"] = math.random() > 0.5
        end
        
        -- Random visual effect
        State.VisualEffects.FlingCamEnabled = math.random() > 0.5
        State.VisualEffects.TrajectoryPreviewEnabled = math.random() > 0.5
        
        Rayfield:Notify({
            Title = "カオスボタン",
            Content = "ランダムな設定が適用されました！",
            Duration = 3
        })
    end
end

-- 心理的効果の実装
local function ApplyAlmostHitFling(targetPart, player)
    if State.VisualEffects.AlmostHitFlingEnabled and player and player.Character then
        local targetPos = player.Character.HumanoidRootPart.Position
        local missDistance = 5 -- 5 studsでミス
        
        local missPos = targetPos + Vector3.new(
            math.random(-missDistance, missDistance),
            math.random(-missDistance, missDistance),
            math.random(-missDistance, missDistance)
        )
        
        targetPart.Velocity = (missPos - targetPart.Position).Unit * 100
    end
end

local function ApplyPersonalSpaceViolation(targetPart, player)
    if State.VisualEffects.PersonalSpaceViolationEnabled and player and player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local personalSpace = CFrame.new(head.Position) * CFrame.new(0, 0, -0.1) -- 10cm前
            targetPart.CFrame = personalSpace
        end
    end
end

local function ApplyStareFling(targetPart, player)
    if State.VisualEffects.StareFlingEnabled and player and player.Character then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local lookAt = CFrame.new(targetPart.Position, humanoidRootPart.Position)
            targetPart.CFrame = lookAt
        end
    end
end

local function ApplyNameCallTrail(targetPart, playerName)
    if State.VisualEffects.NameCallTrailEnabled then
        local trailConnection = RunService.Heartbeat:Connect(function()
            local textLabel = Instance.new("BillboardGui")
            textLabel.Size = UDim2.new(0, 100, 0, 50)
            textLabel.AlwaysOnTop = true
            textLabel.Parent = targetPart
            
            local text = Instance.new("TextLabel")
            text.Size = UDim2.new(1, 0, 1, 0)
            text.BackgroundTransparency = 1
            text.TextColor3 = Color3.new(1, 0, 0)
            text.Text = playerName
            text.TextSize = 20
            text.Parent = textLabel
            
            local tween = TweenService:Create(textLabel, TweenInfo.new(1), {
                Size = UDim2.new(0, 0, 0, 0)
            })
            tween:Play()
            Debris:AddItem(textLabel, 2)
        end)
        table.insert(Connections, trailConnection)
    end
end

local function ApplyDelayedImpactAnxiety(targetPart)
    if State.VisualEffects.DelayedImpactAnxietyEnabled then
        targetPart.Anchored = true
        task.wait(5)
        targetPart.Anchored = false
        targetPart.Velocity = targetPart.Velocity * 2
    end
end

local function ApplyFakeMissHit(targetPart, player)
    if State.VisualEffects.FakeMissHitEnabled and player and player.Character then
        local targetPos = player.Character.HumanoidRootPart.Position
        local missDirection = (targetPart.Position - targetPos).Unit
        
        -- 一度外れる
        targetPart.Velocity = missDirection * 100
        task.wait(1)
        
        -- Uターンして命中
        targetPart.Velocity = (targetPos - targetPart.Position).Unit * 150
    end
end

local function ApplyHeartbeatSync(targetPart, player)
    if State.VisualEffects.HeartbeatSyncEnabled and player and player.Character then
        local distance = (targetPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
        local heartbeatRate = math.max(0.1, 1 - (distance / 100))
        
        local sound = Instance.new("Sound")
        sound.SoundId = "rbxassetid://9125536847" -- Heartbeat sound
        sound.Looped = true
        sound.PlaybackSpeed = heartbeatRate
        sound.Parent = targetPart
        sound:Play()
        
        Debris:AddItem(sound, 10)
    end
end

local function ApplyWhisperPass(targetPart, player)
    if State.VisualEffects.WhisperPassEnabled and player then
        local sound = Instance.new("Sound")
        sound.SoundId = "rbxassetid://9125538912" -- Whisper sound
        sound.Parent = targetPart
        sound:Play()
        Debris:AddItem(sound, 5)
    end
end

local function ApplyMirrorPanic(targetPart, player)
    if State.VisualEffects.MirrorPanicEnabled and player and player.Character then
        local originalColor = targetPart.Color
        targetPart.Color = player.Character.HumanoidRootPart.Color
        task.wait(0.1)
        targetPart.Color = originalColor
    end
end

local function ApplyShadowFirst(targetPart)
    if State.VisualEffects.ShadowFirstEnabled then
        local shadow = Instance.new("Part")
        shadow.Size = targetPart.Size
        shadow.Color = Color3.new(0, 0, 0)
        shadow.Transparency = 0.5
        shadow.Material = Enum.Material.SmoothPlastic
        shadow.CFrame = targetPart.CFrame * CFrame.new(0, -100, 0) -- 影が先に到着
        shadow.Anchored = true
        shadow.CanCollide = false
        shadow.Parent = Workspace
        
        local tween = TweenService:Create(shadow, TweenInfo.new(0.5), {
            CFrame = targetPart.CFrame
        })
        tween:Play()
        Debris:AddItem(shadow, 1)
    end
end

local function ApplyEyeLevelLock(targetPart, player)
    if State.VisualEffects.EyeLevelLockEnabled and player and player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local eyeLevel = head.Position.Y
            targetPart.Position = Vector3.new(targetPart.Position.X, eyeLevel, targetPart.Position.Z)
        end
    end
end

local function ApplySilentFling(targetPart)
    if State.VisualEffects.SilentFlingEnabled then
        -- すべての音を無効化
        for _, descendant in pairs(targetPart:GetDescendants()) do
            if descendant:IsA("Sound") then
                descendant.Volume = 0
            end
        end
        
        -- 着地音だけ最大
        targetPart.Touched:Connect(function()
            local impactSound = Instance.new("Sound")
            impactSound.SoundId = "rbxassetid://9125541234"
            impactSound.Volume = 1
            impactSound.Parent = targetPart
            impactSound:Play()
            Debris:AddItem(impactSound, 2)
        end)
    end
end

local function ApplyBackstabArc(targetPart, player)
    if State.VisualEffects.BackstabArcEnabled and player and player.Character then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            -- 背後を通る
            local behindPos = humanoidRootPart.CFrame * CFrame.new(0, 0, 5)
            targetPart.CFrame = behindPos
            
            task.wait(0.5)
            
            -- 前に来る
            local frontPos = humanoidRootPart.CFrame * CFrame.new(0, 0, -5)
            targetPart.CFrame = frontPos
        end
    end
end

local function ApplyExpectationBreaker()
    if State.VisualEffects.ExpectationBreakerEnabled then
        -- 前回と逆の挙動を選択
        if State.VisualEffects.LastBehavior == "Curve" then
            State.FlingExtensions.CurveFlingEnabled = false
            State.FlingExtensions.BoomerangFlingEnabled = true
            State.VisualEffects.LastBehavior = "Boomerang"
        else
            State.FlingExtensions.CurveFlingEnabled = true
            State.FlingExtensions.BoomerangFlingEnabled = false
            State.VisualEffects.LastBehavior = "Curve"
        end
    end
end

local function CreateYouNextIndicator(player)
    if State.VisualEffects.YouNextIndicatorEnabled then
        local screenGui = Instance.new("ScreenGui")
        screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
        
        local indicator = Instance.new("TextLabel")
        indicator.Size = UDim2.new(0, 300, 0, 100)
        indicator.Position = UDim2.new(0.5, -150, 0.1, 0)
        indicator.BackgroundTransparency = 0.7
        indicator.BackgroundColor3 = Color3.new(0, 0, 0)
        indicator.TextColor3 = Color3.new(1, 0, 0)
        indicator.Text = "NEXT TARGET: " .. player.Name
        indicator.TextSize = 36
        indicator.TextStrokeTransparency = 0
        indicator.Parent = screenGui
        
        local tween = TweenService:Create(indicator, TweenInfo.new(2), {
            Position = UDim2.new(0.5, -150, 0, -100),
            Transparency = 1
        })
        tween:Play()
        Debris:AddItem(indicator, 3)
    end
end

-- 数式・物理ガチ勢向け機能の実装
local function ApplyVectorComposer(targetPart)
    if State.PhysicsFling.VectorComposerEnabled then
        local velocity = Vector3.new(
            State.PhysicsFling.VectorX,
            State.PhysicsFling.VectorY,
            State.PhysicsFling.VectorZ
        )
        
        local angularVelocity = Vector3.new(
            State.PhysicsFling.AngularX,
            State.PhysicsFling.AngularY,
            State.PhysicsFling.AngularZ
        )
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = velocity
        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVelocity.Parent = targetPart
        
        local bodyAngularVelocity = Instance.new("BodyAngularVelocity")
        bodyAngularVelocity.AngularVelocity = angularVelocity
        bodyAngularVelocity.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bodyAngularVelocity.Parent = targetPart
        
        Debris:AddItem(bodyVelocity, 0.5)
        Debris:AddItem(bodyAngularVelocity, 0.5)
    end
end

local function ApplyMassCurveOverride(targetPart, velocity)
    if State.PhysicsFling.MassCurveOverrideEnabled then
        local speed = velocity.Magnitude
        local massMultiplier = 1 + (speed / 100) * 0.5
        targetPart.Mass = targetPart.Mass * massMultiplier
    end
end

local function ApplyAirDragCoefficient(targetPart)
    if State.PhysicsFling.AirDragCoefficientEnabled then
        local dragForce = -targetPart.Velocity * State.PhysicsFling.AirDrag
        local bodyForce = Instance.new("BodyForce")
        bodyForce.Force = dragForce * targetPart.Mass
        bodyForce.Parent = targetPart
        Debris:AddItem(bodyForce, 1)
    end
end

local function ApplyAngularMomentumLock(targetPart)
    if State.PhysicsFling.AngularMomentumLockEnabled then
        local originalAngularVelocity = targetPart.RotVelocity
        local lockConnection = RunService.Heartbeat:Connect(function()
            targetPart.RotVelocity = originalAngularVelocity
        end)
        Debris:AddItem(lockConnection, 5)
    end
end

local function ApplyGravityGradient(targetPart)
    if State.PhysicsFling.GravityGradientEnabled then
        local gradientConnection = RunService.Heartbeat:Connect(function()
            local height = targetPart.Position.Y
            local gravityMultiplier = 1 - (height / 1000) -- 高度が高いほど重力が弱くなる
            gravityMultiplier = math.max(0.1, gravityMultiplier)
            
            local gravityForce = Vector3.new(0, -workspace.Gravity * gravityMultiplier, 0)
            local bodyForce = Instance.new("BodyForce")
            bodyForce.Force = gravityForce * targetPart.Mass
            bodyForce.Parent = targetPart
            Debris:AddItem(bodyForce, 0.1)
        end)
        table.insert(Connections, gradientConnection)
    end
end

local function ApplyRelativisticFling(targetPart)
    if State.PhysicsFling.RelativisticFlingEnabled then
        local speed = targetPart.Velocity.Magnitude
        local timeDilation = 1 / math.sqrt(1 - (speed * speed) / (300000000 * 300000000))
        timeDilation = math.min(timeDilation, 10) -- 上限
        
        local timeEffect = Instance.new("Part")
        timeEffect.Size = Vector3.new(5, 5, 5)
        timeEffect.Transparency = 0.7
        timeEffect.Color = Color3.fromHSV(timeDilation / 10, 1, 1)
        timeEffect.Material = Enum.Material.Neon
        timeEffect.CFrame = targetPart.CFrame
        timeEffect.Anchored = true
        timeEffect.CanCollide = false
        timeEffect.Parent = Workspace
        
        Debris:AddItem(timeEffect, 1)
    end
end

local function ApplyTerminalVelocityCap(targetPart)
    if State.PhysicsFling.TerminalVelocityCapEnabled then
        local capConnection = RunService.Heartbeat:Connect(function()
            local velocity = targetPart.Velocity
            local speed = velocity.Magnitude
            
            if speed > State.PhysicsFling.TerminalVelocity then
                targetPart.Velocity = velocity.Unit * State.PhysicsFling.TerminalVelocity
            end
        end)
        table.insert(Connections, capConnection)
    end
end

-- UI/操作革命系機能の実装
local function CreateRadialFlingWheel()
    if State.UIFling.RadialFlingWheelEnabled then
        local screenGui = Instance.new("ScreenGui")
        screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
        
        local center = Instance.new("Frame")
        center.Size = UDim2.new(0, 100, 0, 100)
        center.Position = UDim2.new(0.5, -50, 0.5, -50)
        center.BackgroundTransparency = 0.5
        center.BackgroundColor3 = Color3.new(0, 0, 0)
        center.Parent = screenGui
        
        local options = {
            {Name = "上", Angle = 0, Color = Color3.new(1, 0, 0)},
            {Name = "右", Angle = 90, Color = Color3.new(0, 1, 0)},
            {Name = "下", Angle = 180, Color = Color3.new(0, 0, 1)},
            {Name = "左", Angle = 270, Color = Color3.new(1, 1, 0)}
        }
        
        local radius = 150
        
        for _, option in ipairs(options) do
            local angleRad = math.rad(option.Angle)
            local x = math.cos(angleRad) * radius
            local y = math.sin(angleRad) * radius
            
            local button = Instance.new("TextButton")
            button.Size = UDim2.new(0, 80, 0, 80)
            button.Position = UDim2.new(0.5, x - 40, 0.5, y - 40)
            button.Text = option.Name
            button.BackgroundColor3 = option.Color
            button.TextColor3 = Color3.new(1, 1, 1)
            button.Parent = screenGui
            
            button.MouseButton1Click:Connect(function()
                -- 方向に応じたフリングを実行
                local direction = Vector3.new(math.cos(angleRad), 0.5, math.sin(angleRad))
                ApplySuperStrength(Character.HumanoidRootPart, direction * 100)
                screenGui:Destroy()
            end)
        end
        
        RadialWheel = screenGui
    end
end

local function SetupGestureFling()
    if State.UIFling.GestureFlingEnabled then
        local lastMousePos = nil
        local gesturePoints = {}
        
        local inputConnection = UserInputService.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                lastMousePos = input.Position
                gesturePoints = {}
            end
        end)
        
        local changedConnection = UserInputService.InputChanged:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseMovement and lastMousePos then
                local currentPos = input.Position
                table.insert(gesturePoints, currentPos)
                
                -- 軌跡を描画
                if #gesturePoints >= 2 then
                    local point1 = gesturePoints[#gesturePoints - 1]
                    local point2 = gesturePoints[#gesturePoints]
                    
                    local line = Instance.new("Frame")
                    line.Size = UDim2.new(0, 2, 0, (point2 - point1).Magnitude)
                    line.Position = UDim2.new(0, point1.X, 0, point1.Y)
                    line.Rotation = math.deg(math.atan2(point2.Y - point1.Y, point2.X - point1.X))
                    line.BackgroundColor3 = Color3.new(1, 1, 0)
                    line.BorderSizePixel = 0
                    line.Parent = LocalPlayer:WaitForChild("PlayerGui")
                    
                    Debris:AddItem(line, 0.5)
                end
            end
        end)
        
        local endedConnection = UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 and #gesturePoints > 10 then
                -- ジェスチャーを軌道に変換
                local startPoint = gesturePoints[1]
                local endPoint = gesturePoints[#gesturePoints]
                
                local direction = Vector3.new(
                    endPoint.X - startPoint.X,
                    0,
                    endPoint.Y - startPoint.Y
                ).Unit
                
                ApplySuperStrength(Character.HumanoidRootPart, direction * 200)
            end
        end)
        
        table.insert(Connections, inputConnection)
        table.insert(Connections, changedConnection)
        table.insert(Connections, endedConnection)
    end
end

local function SetupHoldToPreview()
    if State.UIFling.HoldToPreviewEnabled then
        local previewConnection = UserInputService.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton2 then
                -- プレビュー表示
                ShowTrajectoryPreview(
                    Character.HumanoidRootPart.Position,
                    Workspace.CurrentCamera.CFrame.LookVector,
                    100
                )
            end
        end)
        
        local endConnection = UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton2 then
                -- プレビュー削除
                for _, part in pairs(TrajectoryPreviewParts) do
                    part:Destroy()
                end
                TrajectoryPreviewParts = {}
            end
        end)
        
        table.insert(Connections, previewConnection)
        table.insert(Connections, endConnection)
    end
end

local function SaveFlingBookmark(name, settings)
    if State.UIFling.FlingBookmarkEnabled then
        FlingBookmarks[name] = settings
        Rayfield:Notify({
            Title = "ブックマーク保存",
            Content = name .. " を保存しました",
            Duration = 3
        })
    end
end

local function LoadFlingBookmark(name)
    if State.UIFling.FlingBookmarkEnabled and FlingBookmarks[name] then
        local settings = FlingBookmarks[name]
        -- 設定を適用
        for key, value in pairs(settings) do
            if State.Combat[key] ~= nil then
                State.Combat[key] = value
            elseif State.FlingExtensions[key] ~= nil then
                State.FlingExtensions[key] = value
            end
        end
    end
end

local function SetupTargetPriorityUI()
    if State.UIFling.TargetPriorityUIEnabled then
        local screenGui = Instance.new("ScreenGui")
        screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
        
        local playerFrames = {}
        
        local function updateUI()
            for _, frame in pairs(playerFrames) do
                frame:Destroy()
            end
            playerFrames = {}
            
            local yOffset = 10
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local frame = Instance.new("Frame")
                    frame.Size = UDim2.new(0, 200, 0, 40)
                    frame.Position = UDim2.new(0, 10, 0, yOffset)
                    
                    -- 危険度に応じた色
                    local distance = 100
                    if Character and Character:FindFirstChild("HumanoidRootPart") and
                       player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                        distance = (Character.HumanoidRootPart.Position - 
                                   player.Character.HumanoidRootPart.Position).Magnitude
                    end
                    
                    local danger = 1 - (distance / 100)
                    danger = math.max(0, math.min(1, danger))
                    
                    frame.BackgroundColor3 = Color3.new(danger, 1 - danger, 0)
                    frame.BackgroundTransparency = 0.3
                    frame.Parent = screenGui
                    
                    local nameLabel = Instance.new("TextLabel")
                    nameLabel.Size = UDim2.new(1, 0, 1, 0)
                    nameLabel.BackgroundTransparency = 1
                    nameLabel.TextColor3 = Color3.new(1, 1, 1)
                    nameLabel.Text = player.Name
                    nameLabel.TextSize = 18
                    nameLabel.Parent = frame
                    
                    playerFrames[player] = frame
                    yOffset = yOffset + 45
                end
            end
        end
        
        local updateConnection = RunService.Heartbeat:Connect(updateUI)
        table.insert(Connections, updateConnection)
    end
end

local function SetupHUDMinimalMode()
    if State.UIFling.HUDMinimalModeEnabled then
        -- 既存のUIを非表示にする
        local playerGui = LocalPlayer:WaitForChild("PlayerGui")
        for _, gui in pairs(playerGui:GetChildren()) do
            if gui:IsA("ScreenGui") and gui.Name ~= "SyuHubUI" then
                gui.Enabled = false
            end
        end
        
        -- 最小限のHUDを作成
        local minimalHUD = Instance.new("ScreenGui")
        minimalHUD.Name = "MinimalHUD"
        minimalHUD.Parent = playerGui
        
        -- ターゲット表示
        local targetDisplay = Instance.new("TextLabel")
        targetDisplay.Size = UDim2.new(0, 200, 0, 30)
        targetDisplay.Position = UDim2.new(0.5, -100, 0, 10)
        targetDisplay.BackgroundTransparency = 0.7
        targetDisplay.BackgroundColor3 = Color3.new(0, 0, 0)
        targetDisplay.TextColor3 = Color3.new(1, 1, 1)
        targetDisplay.Text = "Target: None"
        targetDisplay.TextSize = 20
        targetDisplay.Parent = minimalHUD
        
        -- 距離表示
        local distanceDisplay = Instance.new("TextLabel")
        distanceDisplay.Size = UDim2.new(0, 150, 0, 30)
        distanceDisplay.Position = UDim2.new(0, 10, 1, -40)
        distanceDisplay.BackgroundTransparency = 0.7
        distanceDisplay.BackgroundColor3 = Color3.new(0, 0, 0)
        distanceDisplay.TextColor3 = Color3.new(1, 1, 1)
        distanceDisplay.Text = "Distance: 0"
        distanceDisplay.TextSize = 20
        distanceDisplay.Parent = minimalHUD
        
        local updateConnection = RunService.Heartbeat:Connect(function()
            if State.Combat.TargetPlayer then
                targetDisplay.Text = "Target: " .. State.Combat.TargetPlayer
                
                local target = GetPlayerByName(State.Combat.TargetPlayer)
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and
                   Character and Character:FindFirstChild("HumanoidRootPart") then
                    local distance = (Character.HumanoidRootPart.Position - 
                                     target.Character.HumanoidRootPart.Position).Magnitude
                    distanceDisplay.Text = "Distance: " .. math.floor(distance)
                end
            else
                targetDisplay.Text = "Target: None"
            end
        end)
        table.insert(Connections, updateConnection)
    end
end

local function SetupSoundAsUI()
    if State.UIFling.SoundAsUIEnabled then
        local soundConnection = RunService.Heartbeat:Connect(function()
            local closest = GetClosestPlayer()
            if closest and closest.Character and closest.Character:FindFirstChild("HumanoidRootPart") and
               Character and Character:FindFirstChild("HumanoidRootPart") then
                local distance = (Character.HumanoidRootPart.Position - 
                                 closest.Character.HumanoidRootPart.Position).Magnitude
                
                local pitch = 1 + (distance / 100) * 2
                pitch = math.max(0.5, math.min(3, pitch))
                
                local sound = Instance.new("Sound")
                sound.SoundId = "rbxassetid://9125567890"
                sound.Pitch = pitch
                sound.Volume = 0.3
                sound.Parent = Workspace
                sound:Play()
                Debris:AddItem(sound, 0.5)
            end
        end)
        table.insert(Connections, soundConnection)
    end
end

local function SetupInstantReplay()
    if State.UIFling.InstantReplayButtonEnabled then
        local lastFling = nil
        
        -- フリングを記録
        local recordConnection = workspace.DescendantAdded:Connect(function(descendant)
            if descendant:IsA("BodyVelocity") or descendant:IsA("BodyAngularVelocity") then
                lastFling = {
                    Time = tick(),
                    Velocity = descendant.Velocity,
                    Part = descendant.Parent
                }
            end
        end)
        
        -- リプレイボタン
        local replayButton = Instance.new("TextButton")
        replayButton.Size = UDim2.new(0, 100, 0, 50)
        replayButton.Position = UDim2.new(1, -110, 0, 60)
        replayButton.BackgroundColor3 = Color3.new(0, 0.5, 1)
        replayButton.TextColor3 = Color3.new(1, 1, 1)
        replayButton.Text = "Replay"
        replayButton.TextSize = 20
        replayButton.Parent = LocalPlayer:WaitForChild("PlayerGui")
        
        replayButton.MouseButton1Click:Connect(function()
            if lastFling and lastFling.Part and lastFling.Part.Parent then
                local bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.Velocity = lastFling.Velocity
                bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bodyVelocity.Parent = lastFling.Part
                Debris:AddItem(bodyVelocity, 0.5)
            end
        end)
        
        table.insert(Connections, recordConnection)
    end
end

-- メインのグラブ処理関数
local function ProcessGrab(targetPart)
    if not targetPart then return end
    
    -- 基本グラブ効果
    ApplyGrabEffect(targetPart, State.Combat.ActiveGrabType)
    
    -- グラブ拡張機能
    ApplyGrabExtensions(targetPart)
    
    -- スーパーパワー
    if State.Combat.SuperStrengthEnabled then
        ApplySuperStrength(targetPart, Vector3.new(0, 1, 0))
    end
    
    -- 物理ガチ勢機能
    ApplyVectorComposer(targetPart)
    ApplyMassCurveOverride(targetPart, targetPart.Velocity)
    ApplyAirDragCoefficient(targetPart)
    ApplyAngularMomentumLock(targetPart)
    ApplyGravityGradient(targetPart)
    ApplyRelativisticFling(targetPart)
    ApplyTerminalVelocityCap(targetPart)
    
    -- フリングカメラ
    CreateFlingCamera(targetPart)
    
    -- ヒットカウンター更新
    State.VisualEffects.HitCount = State.VisualEffects.HitCount + 1
    UpdateHitCounter()
    
    -- 距離記録
    local startPos = targetPart.Position
    local distanceConnection = RunService.Heartbeat:Connect(function()
        local distance = (targetPart.Position - startPos).Magnitude
        UpdateDistanceRecord(distance)
    end)
    table.insert(Connections, distanceConnection)
    
    -- 音響効果
    PlaySoundEffect("Grab")
    
    -- 心理的効果
    local targetPlayer = State.Combat.TargetPlayer and GetPlayerByName(State.Combat.TargetPlayer)
    if targetPlayer then
        ApplyAlmostHitFling(targetPart, targetPlayer)
        ApplyPersonalSpaceViolation(targetPart, targetPlayer)
        ApplyStareFling(targetPart, targetPlayer)
        ApplyNameCallTrail(targetPart, targetPlayer.Name)
        ApplyFakeMissHit(targetPart, targetPlayer)
        ApplyHeartbeatSync(targetPart, targetPlayer)
        ApplyWhisperPass(targetPart, targetPlayer)
        ApplyMirrorPanic(targetPart, targetPlayer)
        ApplyEyeLevelLock(targetPart, targetPlayer)
        ApplyBackstabArc(targetPart, targetPlayer)
        CreateYouNextIndicator(targetPlayer)
    end
    
    ApplyShadowFirst(targetPart)
    ApplyDelayedImpactAnxiety(targetPart)
    ApplyExpectationBreaker()
    ApplySilentFling(targetPart)
end

-- メインのフリング処理関数
local function ProcessFling(targetPart, direction, power)
    if not targetPart then return end
    
    -- フリング拡張機能
    local finalDirection = ApplyFlingExtensions(targetPart, direction)
    
    -- 基本フリング
    targetPart.Velocity = finalDirection * power
    
    -- 重力調整
    if State.VisualEffects.GravityDialEnabled then
        workspace.Gravity = 196.2 * State.VisualEffects.GravityMultiplier
    end
    
    -- 軌道プレビュー
    ShowTrajectoryPreview(targetPart.Position, finalDirection, power)
    
    -- 音響効果
    PlaySoundEffect("Fling")
    
    -- リプレイ記録
    if State.UIFling.InstantReplayButtonEnabled then
        table.insert(ReplayHistory, 1, {
            Time = tick(),
            Part = targetPart,
            Direction = finalDirection,
            Power = power
        })
        if #ReplayHistory > 10 then
            table.remove(ReplayHistory)
        end
    end
end

-- メインの衝突処理関数
local function ProcessImpact(targetPart, impactPosition)
    if not targetPart then return end
    
    -- 衝突効果
    ApplyImpactEffects(targetPart, impactPosition)
    
    -- 音響効果
    PlaySoundEffect("Impact")
    
    -- ヒットカウンター更新
    State.VisualEffects.HitCount = State.VisualEffects.HitCount + 1
    UpdateHitCounter()
end

-- UI 構築
local MainTab = Window:CreateTab("メイン", 4483345998)

-- ⚔️ 戦闘システム
MainTab:CreateSection("⚔️ 戦闘システム")

MainTab:CreateToggle({
    Name = "スーパーパワー有効化",
    CurrentValue = false,
    Callback = function(value)
        State.Combat.SuperStrengthEnabled = value
    end
})

MainTab:CreateSlider({
    Name = "スーパーパワー強度",
    Range = {0, 10000},
    Increment = 100,
    Suffix = "パワー",
    CurrentValue = 400,
    Callback = function(value)
        State.Combat.StrengthPower = value
    end
})

MainTab:CreateDropdown({
    Name = "グラブタイプ",
    Options = {"通常", "毒", "燃焼", "ラグドール", "即死", "無質量", "ノークリップ", "アンウェルド", "視点", "ブロブマン"},
    CurrentOption = {"通常"},
    Multiple = false,
    Callback = function(option)
        State.Combat.ActiveGrabType = option[1]
    end
})

MainTab:CreateSlider({
    Name = "毒ダメージスケール",
    Range = {1, 3},
    Increment = 0.1,
    Suffix = "倍",
    CurrentValue = 1.0,
    Callback = function(value)
        State.Combat.GrabDamageScale = value
    end
})

-- プレイヤーコントロール
MainTab:CreateSection("プレイヤーコントロール")

local playerList = {}
for _, player in pairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        table.insert(playerList, player.Name)
    end
end

local targetDropdown = MainTab:CreateDropdown({
    Name = "ターゲット選択",
    Options = playerList,
    CurrentOption = {playerList[1] or ""},
    Multiple = false,
    Callback = function(option)
        State.Combat.TargetPlayer = option[1]
    end
})

MainTab:CreateButton({
    Name = "プレイヤーリスト更新",
    Callback = function()
        playerList = {}
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                table.insert(playerList, player.Name)
            end
        end
        targetDropdown:Refresh(playerList)
    end
})

MainTab:CreateInput({
    Name = "プレイヤー検索",
    PlaceholderText = "名前を入力...",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        if text ~= "" then
            local foundPlayer = GetPlayerByName(text)
            if foundPlayer then
                State.Combat.TargetPlayer = foundPlayer.Name
                Rayfield:Notify({
                    Title = "プレイヤー発見",
                    Content = foundPlayer.Name .. " をターゲットに設定",
                    Duration = 3
                })
            end
        end
    end
})

MainTab:CreateToggle({
    Name = "自動ロックオン",
    CurrentValue = false,
    Callback = function(value)
        State.Combat.AutoLockEnabled = value
    end
})

MainTab:CreateToggle({
    Name = "自動ブリング",
    CurrentValue = false,
    Callback = function(value)
        State.Combat.AutoBringEnabled = value
    end
})

MainTab:CreateButton({
    Name = "ブリング",
    Callback = function()
        if State.Combat.TargetPlayer then
            local target = Players:FindFirstChild(State.Combat.TargetPlayer)
            if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                local targetPos = target.Character.HumanoidRootPart.Position
                Character:MoveTo(targetPos)
            end
        end
    end
})

MainTab:CreateButton({
    Name = "キック",
    Callback = function()
        if State.Combat.TargetPlayer then
            local target = Players:FindFirstChild(State.Combat.TargetPlayer)
            if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                ProcessFling(target.Character.HumanoidRootPart, Vector3.new(0, 1, 0), State.Combat.KickPower)
            end
        end
    end
})

MainTab:CreateToggle({
    Name = "自動キック",
    CurrentValue = false,
    Callback = function(value)
        State.Combat.AutoKickEnabled = value
    end
})

MainTab:CreateButton({
    Name = "キック停止",
    Callback = function()
        State.Combat.AutoKickEnabled = false
    end
})

MainTab:CreateInput({
    Name = "フレンド追加",
    PlaceholderText = "フレンド名を入力...",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        if text ~= "" and not table.find(State.Combat.WhitelistFriends, text) then
            table.insert(State.Combat.WhitelistFriends, text)
            Rayfield:Notify({
                Title = "フレンド追加",
                Content = text .. " をホワイトリストに追加",
                Duration = 3
            })
        end
    end
})

MainTab:CreateToggle({
    Name = "サーバー破壊 (ブロブマン)",
    CurrentValue = false,
    Callback = function(value)
        State.Combat.DestroyServerEnabled = value
    end
})

-- 🛡️ 保護システムタブ
local ProtectionTab = Window:CreateTab("保護システム", 4483345998)

ProtectionTab:CreateSection("基本保護")

ProtectionTab:CreateToggle({
    Name = "アンチグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiGrabEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチフリング",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiFlingEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチ爆発",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiExplosionEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチスタン",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiStunEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチラグ",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiLagEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチキック",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiKickEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチボイド",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiVoidEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "透明化",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.InvisibilityEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateSection("プレミアム保護")

ProtectionTab:CreateToggle({
    Name = "アンチグラブ (高級)",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.PremiumAntiGrabEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチ雪玉",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiSnowballEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "アンチバナナ",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiBananaEnabled = value
    end
})

ProtectionTab:CreateSection("高度な機能")

ProtectionTab:CreateToggle({
    Name = "アンチ燃焼",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AntiBurnEnabled = value
    end
})

ProtectionTab:CreateToggle({
    Name = "自動攻撃",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.AutoAttackerEnabled = value
        SetupProtectionSystems()
    end
})

ProtectionTab:CreateToggle({
    Name = "カウンターモッド",
    CurrentValue = false,
    Callback = function(value)
        State.Protection.CounterModEnabled = value
    end
})

-- 👤 プレイヤー強化タブ
local EnhancementsTab = Window:CreateTab("プレイヤー強化", 4483345998)

EnhancementsTab:CreateSection("移動強化")

EnhancementsTab:CreateSlider({
    Name = "歩行速度",
    Range = {0.1, 5},
    Increment = 0.1,
    Suffix = "倍速",
    CurrentValue = 1.0,
    Callback = function(value)
        State.Enhancements.WalkSpeedMultiplier = value
        UpdateEnhancements()
    end
})

EnhancementsTab:CreateToggle({
    Name = "無限ジャンプ",
    CurrentValue = false,
    Callback = function(value)
        State.Enhancements.InfiniteJumpEnabled = value
        UpdateEnhancements()
    end
})

EnhancementsTab:CreateSlider({
    Name = "ジャンプパワー",
    Range = {24, 1000},
    Increment = 10,
    Suffix = "パワー",
    CurrentValue = 50,
    Callback = function(value)
        State.Enhancements.JumpPower = value
        UpdateEnhancements()
    end
})

EnhancementsTab:CreateToggle({
    Name = "ノークリップモード",
    CurrentValue = false,
    Callback = function(value)
        State.Enhancements.NoClipModeEnabled = value
        UpdateEnhancements()
    end
})

EnhancementsTab:CreateToggle({
    Name = "飛行モード",
    CurrentValue = false,
    Callback = function(value)
        State.Enhancements.FlyEnabled = value
        UpdateEnhancements()
    end
})

EnhancementsTab:CreateSlider({
    Name = "飛行速度",
    Range = {10, 200},
    Increment = 5,
    Suffix = "速度",
    CurrentValue = 50,
    Callback = function(value)
        State.Enhancements.FlySpeed = value
    end
})

-- 🧠 ESP タブ
local ESPTab = Window:CreateTab("ESP", 4483345998)

ESPTab:CreateSection("ESP設定")

ESPTab:CreateToggle({
    Name = "ESP有効化",
    CurrentValue = false,
    Callback = function(value)
        State.ESP.Enabled = value
        UpdateESP()
    end
})

ESPTab:CreateColorPicker({
    Name = "塗りつぶし色",
    Color = Color3.fromRGB(0, 255, 0),
    Callback = function(value)
        State.ESP.FillColor = value
        UpdateESP()
    end
})

ESPTab:CreateSlider({
    Name = "塗りつぶし透明度",
    Range = {0, 1},
    Increment = 0.05,
    Suffix = "",
    CurrentValue = 0.5,
    Callback = function(value)
        State.ESP.FillTransparency = value
        UpdateESP()
    end
})

ESPTab:CreateColorPicker({
    Name = "枠線色",
    Color = Color3.fromRGB(255, 255, 255),
    Callback = function(value)
        State.ESP.OutlineColor = value
        UpdateESP()
    end
})

ESPTab:CreateSlider({
    Name = "枠線透明度",
    Range = {0, 1},
    Increment = 0.05,
    Suffix = "",
    CurrentValue = 0,
    Callback = function(value)
        State.ESP.OutlineTransparency = value
        UpdateESP()
    end
})

ESPTab:CreateDropdown({
    Name = "ハイライトモード",
    Options = {"全員", "敵のみ", "味方のみ", "ターゲットのみ"},
    CurrentOption = {"全員"},
    Multiple = false,
    Callback = function(option)
        State.ESP.HighlightMode = option[1]
        UpdateESP()
    end
})

ESPTab:CreateToggle({
    Name = "アイコン表示",
    CurrentValue = true,
    Callback = function(value)
        State.ESP.ShowIcons = value
        UpdateESP()
    end
})

ESPTab:CreateDropdown({
    Name = "アイコンタイプ",
    Options = {"スカル", "プレイヤー", "敵", "フレンド"},
    CurrentOption = {"スカル"},
    Multiple = false,
    Callback = function(option)
        State.ESP.IconType = option[1]
        UpdateESP()
    end
})

-- 💥 雑多な機能タブ
local MiscTab = Window:CreateTab("雑多な機能", 4483345998)

MiscTab:CreateSection("カスタム爆発")

MiscTab:CreateToggle({
    Name = "カスタム爆発",
    CurrentValue = false,
    Callback = function(value)
        State.Misc.CustomExplosionsEnabled = value
    end
})

MiscTab:CreateSlider({
    Name = "爆発パワー",
    Range = {10, 500},
    Increment = 10,
    Suffix = "パワー",
    CurrentValue = 100,
    Callback = function(value)
        State.Misc.ExplosionPower = value
    end
})

MiscTab:CreateButton({
    Name = "爆発実行",
    Callback = function()
        if State.Misc.CustomExplosionsEnabled then
            local explosion = Instance.new("Explosion")
            explosion.Position = Character.HumanoidRootPart.Position
            explosion.BlastPressure = State.Misc.ExplosionPower
            explosion.BlastRadius = 20
            explosion.DestroyJointRadiusPercent = 0
            explosion.Parent = Workspace
        end
    end
})

MiscTab:CreateSection("テレポート")

MiscTab:CreateInput({
    Name = "場所を保存",
    PlaceholderText = "場所名を入力",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        if text ~= "" and Character then
            State.Misc.TeleportLocations[text] = Character.HumanoidRootPart.Position
            Rayfield:Notify({Title = "場所保存", Content = text .. " を保存しました", Duration = 3})
        end
    end
})

local teleportDropdown = MiscTab:CreateDropdown({
    Name = "保存済み場所",
    Options = {},
    CurrentOption = {""},
    Multiple = false,
    Callback = function(option)
        local locName = option[1]
        if State.Misc.TeleportLocations[locName] and Character then
            Character:MoveTo(State.Misc.TeleportLocations[locName])
        end
    end
})

MiscTab:CreateButton({
    Name = "場所リスト更新",
    Callback = function()
        local locations = {}
        for name, _ in pairs(State.Misc.TeleportLocations) do
            table.insert(locations, name)
        end
        teleportDropdown:Refresh(locations)
    end
})

MiscTab:CreateToggle({
    Name = "クイックテレポート",
    CurrentValue = false,
    Callback = function(value)
        State.Misc.QuickTeleportEnabled = value
    end
})

MiscTab:CreateSection("カスタムライン")

MiscTab:CreateToggle({
    Name = "カスタムライン",
    CurrentValue = false,
    Callback = function(value)
        State.Misc.CustomLineEnabled = value
    end
})

MiscTab:CreateColorPicker({
    Name = "ライン色",
    Color = Color3.fromRGB(255, 255, 255),
    Callback = function(value)
        State.Misc.LineColor = value
    end
})

MiscTab:CreateSlider({
    Name = "ライン太さ",
    Range = {1, 10},
    Increment = 0.5,
    Suffix = "太さ",
    CurrentValue = 1,
    Callback = function(value)
        State.Misc.LineThickness = value
    end
})

MiscTab:CreateToggle({
    Name = "QuiK",
    CurrentValue = false,
    Callback = function(value)
        State.Misc.QuiKEnabled = value
    end
})

-- 🖐️ GRAB拡張タブ
local GrabExtensionsTab = Window:CreateTab("GRAB拡張", 4483345998)

GrabExtensionsTab:CreateSection("グラブ拡張機能")

GrabExtensionsTab:CreateToggle({
    Name = "チャージグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.ChargeGrabEnabled = value
    end
})

GrabExtensionsTab:CreateSlider({
    Name = "最大チャージ倍率",
    Range = {1, 50},
    Increment = 1,
    Suffix = "倍",
    CurrentValue = 50,
    Callback = function(value)
        State.GrabExtensions.MaxChargeMultiplier = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "スピンアップグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.SpinUpGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "インバースグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.InverseGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "ジョイントロックグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.JointLockGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "ラバーグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.RubberGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "アンカーグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.AnchorGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "ゴーストグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.GhostGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "マルチリムグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.MultiLimbGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "ディレイドグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.DelayedGrabEnabled = value
    end
})

GrabExtensionsTab:CreateSlider({
    Name = "ディレイ時間",
    Range = {0.1, 5},
    Increment = 0.1,
    Suffix = "秒",
    CurrentValue = 1.0,
    Callback = function(value)
        State.GrabExtensions.DelayTime = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "スナップショットグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.SnapshotGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "マグネットグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.MagnetGrabEnabled = value
    end
})

GrabExtensionsTab:CreateSlider({
    Name = "マグネット範囲",
    Range = {5, 50},
    Increment = 1,
    Suffix = "スタッド",
    CurrentValue = 20,
    Callback = function(value)
        State.GrabExtensions.MagnetRange = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "スケールグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.ScaleGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "リバースマスグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.ReverseMassGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "チェーングラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.ChainGrabEnabled = value
    end
})

GrabExtensionsTab:CreateToggle({
    Name = "オーソリティグラブ",
    CurrentValue = false,
    Callback = function(value)
        State.GrabExtensions.AuthorityGrabEnabled = value
    end
})

-- 🚀 FLING拡張タブ
local FlingExtensionsTab = Window:CreateTab("FLING拡張", 4483345998)

FlingExtensionsTab:CreateSection("フリング挙動拡張")

FlingExtensionsTab:CreateToggle({
    Name = "カーブフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.CurveFlingEnabled = value
    end
})

FlingExtensionsTab:CreateSlider({
    Name = "カーブ強度",
    Range = {0, 100},
    Increment = 1,
    Suffix = "%",
    CurrentValue = 0,
    Callback = function(value)
        State.FlingExtensions.CurveFactor = value / 100
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "ブーメランフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.BoomerangFlingEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "スキャッターフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.ScatterFlingEnabled = value
    end
})

FlingExtensionsTab:CreateSlider({
    Name = "分裂数",
    Range = {2, 10},
    Increment = 1,
    Suffix = "個",
    CurrentValue = 3,
    Callback = function(value)
        State.FlingExtensions.ScatterCount = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "パルスフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.PulseFlingEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "フェーズフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.PhaseFlingEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "ロックオンフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.LockOnFlingEnabled = value
    end
})

FlingExtensionsTab:CreateInput({
    Name = "ロックオンターゲット",
    PlaceholderText = "プレイヤー名を入力",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        State.FlingExtensions.LockOnTarget = text
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "オービットフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.OrbitFlingEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "スパイラルフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.SpiralFlingEnabled = value
    end
})

FlingExtensionsTab:CreateSlider({
    Name = "スパイラル回転数",
    Range = {1, 10},
    Increment = 0.5,
    Suffix = "回転",
    CurrentValue = 2,
    Callback = function(value)
        State.FlingExtensions.SpiralTurns = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "タイムスローフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.TimeSlowFlingEnabled = value
    end
})

FlingExtensionsTab:CreateSlider({
    Name = "時間スケール",
    Range = {0.1, 1},
    Increment = 0.1,
    Suffix = "倍",
    CurrentValue = 0.5,
    Callback = function(value)
        State.FlingExtensions.TimeScale = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "方向カオス",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.DirectionalChaosEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "スナップフリング",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.SnapFlingEnabled = value
    end
})

FlingExtensionsTab:CreateToggle({
    Name = "手元に戻る",
    CurrentValue = false,
    Callback = function(value)
        State.FlingExtensions.ReturnToHandEnabled = value
    end
})

-- 🧱 着地・衝突後タブ
local ImpactTab = Window:CreateTab("着地・衝突", 4483345998)

ImpactTab:CreateSection("衝突効果")

ImpactTab:CreateToggle({
    Name = "インパクトフリーズ",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.ImpactFreezeEnabled = value
    end
})

ImpactTab:CreateSlider({
    Name = "フリーズ時間",
    Range = {0.1, 10},
    Increment = 0.1,
    Suffix = "秒",
    CurrentValue = 3.0,
    Callback = function(value)
        State.ImpactExtensions.FreezeDuration = value
    end
})

ImpactTab:CreateToggle({
    Name = "バウンスオーバーライド",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.BounceOverrideEnabled = value
    end
})

ImpactTab:CreateSlider({
    Name = "バウンス倍率",
    Range = {0, 2},
    Increment = 0.1,
    Suffix = "倍",
    CurrentValue = 1.0,
    Callback = function(value)
        State.ImpactExtensions.BounceMultiplier = value
    end
})

ImpactTab:CreateToggle({
    Name = "テレインエンベッド",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.TerrainEmbedEnabled = value
    end
})

ImpactTab:CreateToggle({
    Name = "ショックウェーブリング",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.ShockwaveRingEnabled = value
    end
})

ImpactTab:CreateSlider({
    Name = "ショックウェーブ半径",
    Range = {5, 50},
    Increment = 1,
    Suffix = "スタッド",
    CurrentValue = 10,
    Callback = function(value)
        State.ImpactExtensions.ShockwaveRadius = value
    end
})

ImpactTab:CreateToggle({
    Name = "ラグドールループ",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.RagdollLoopEnabled = value
    end
})

ImpactTab:CreateToggle({
    Name = "ベロシティスチール",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.VelocityStealEnabled = value
    end
})

ImpactTab:CreateSlider({
    Name = "スチール半径",
    Range = {5, 50},
    Increment = 1,
    Suffix = "スタッド",
    CurrentValue = 15,
    Callback = function(value)
        State.ImpactExtensions.StealRadius = value
    end
})

ImpactTab:CreateToggle({
    Name = "爆発なしノックバック",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.ExplosionLessKnockbackEnabled = value
    end
})

ImpactTab:CreateToggle({
    Name = "アフターイメージトレイル",
    CurrentValue = false,
    Callback = function(value)
        State.ImpactExtensions.AfterimageTrailEnabled = value
    end
})

ImpactTab:CreateSlider({
    Name = "トレイル持続時間",
    Range = {0.5, 5},
    Increment = 0.1,
    Suffix = "秒",
    CurrentValue = 2.0,
    Callback = function(value)
        State.ImpactExtensions.TrailDuration = value
    end
})

-- 👁️ 観測・演出・Toy系タブ
local VisualTab = Window:CreateTab("観測・演出", 4483345998)

VisualTab:CreateSection("観測機能")

VisualTab:CreateToggle({
    Name = "フリングカメラ",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.FlingCamEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "軌道プレビュー",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.TrajectoryPreviewEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "ヒットカウンター",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.HitCounterEnabled = value
        UpdateHitCounter()
    end
})

VisualTab:CreateToggle({
    Name = "距離記録",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.DistanceRecordEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "重力ダイヤル",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.GravityDialEnabled = value
    end
})

VisualTab:CreateSlider({
    Name = "重力倍率",
    Range = {0.1, 3},
    Increment = 0.1,
    Suffix = "倍",
    CurrentValue = 1.0,
    Callback = function(value)
        State.VisualEffects.GravityMultiplier = value
    end
})

VisualTab:CreateToggle({
    Name = "サウンドパック",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.SoundPackEnabled = value
    end
})

VisualTab:CreateSection("Toy機能")

VisualTab:CreateToggle({
    Name = "Toyハンマー",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ToyHammerEnabled = value
        if value then CreateToyHammer() end
    end
})

VisualTab:CreateToggle({
    Name = "Toyスリングショット",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ToySlingshotEnabled = value
        if value then CreateToySlingshot() end
    end
})

VisualTab:CreateToggle({
    Name = "Toyヨーヨー",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ToyYoYoEnabled = value
        if value then CreateToyYoYo() end
    end
})

VisualTab:CreateToggle({
    Name = "カオスボタン",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ChaosButtonEnabled = value
    end
})

VisualTab:CreateButton({
    Name = "カオス発動",
    Callback = function()
        ApplyChaosButton()
    end
})

VisualTab:CreateSection("心理的効果")

VisualTab:CreateToggle({
    Name = "ニアミスフリング",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.AlmostHitFlingEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "パーソナルスペース侵害",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.PersonalSpaceViolationEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "凝視フリング",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.StareFlingEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "名前呼びトレイル",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.NameCallTrailEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "遅延インパクト不安",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.DelayedImpactAnxietyEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "フェイクミス→ヒット",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.FakeMissHitEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "心拍同期",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.HeartbeatSyncEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "囁き通過",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.WhisperPassEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "ミラーパニック",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.MirrorPanicEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "シャドウファースト",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ShadowFirstEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "アイレベルロック",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.EyeLevelLockEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "サイレントフリング",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.SilentFlingEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "背後通過アーク",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.BackstabArcEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "期待破壊者",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.ExpectationBreakerEnabled = value
    end
})

VisualTab:CreateToggle({
    Name = "次はあなた表示",
    CurrentValue = false,
    Callback = function(value)
        State.VisualEffects.YouNextIndicatorEnabled = value
    end
})

-- 🧠 物理ガチ勢タブ
local PhysicsTab = Window:CreateTab("物理ガチ勢", 4483345998)

PhysicsTab:CreateSection("ベクトル合成")

PhysicsTab:CreateToggle({
    Name = "ベクトルコンポーザー",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.VectorComposerEnabled = value
    end
})

PhysicsTab:CreateSlider({
    Name = "X軸ベクトル",
    Range = {-1000, 1000},
    Increment = 10,
    Suffix = "力",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.VectorX = value
    end
})

PhysicsTab:CreateSlider({
    Name = "Y軸ベクトル",
    Range = {-1000, 1000},
    Increment = 10,
    Suffix = "力",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.VectorY = value
    end
})

PhysicsTab:CreateSlider({
    Name = "Z軸ベクトル",
    Range = {-1000, 1000},
    Increment = 10,
    Suffix = "力",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.VectorZ = value
    end
})

PhysicsTab:CreateSlider({
    Name = "X軸角速度",
    Range = {-50, 50},
    Increment = 1,
    Suffix = "rad/s",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.AngularX = value
    end
})

PhysicsTab:CreateSlider({
    Name = "Y軸角速度",
    Range = {-50, 50},
    Increment = 1,
    Suffix = "rad/s",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.AngularY = value
    end
})

PhysicsTab:CreateSlider({
    Name = "Z軸角速度",
    Range = {-50, 50},
    Increment = 1,
    Suffix = "rad/s",
    CurrentValue = 0,
    Callback = function(value)
        State.PhysicsFling.AngularZ = value
    end
})

PhysicsTab:CreateToggle({
    Name = "質量曲線オーバーライド",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.MassCurveOverrideEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "空気抵抗係数",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.AirDragCoefficientEnabled = value
    end
})

PhysicsTab:CreateSlider({
    Name = "空気抵抗",
    Range = {0, 1},
    Increment = 0.01,
    Suffix = "係数",
    CurrentValue = 0.1,
    Callback = function(value)
        State.PhysicsFling.AirDrag = value
    end
})

PhysicsTab:CreateToggle({
    Name = "角運動量ロック",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.AngularMomentumLockEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "弾性解法",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.ElasticitySolverEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "重力勾配",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.GravityGradientEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "相対論的フリング",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.RelativisticFlingEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "エネルギー保存切替",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.EnergyConservationToggleEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "フレームステップシミュレーション",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.FrameStepSimulationEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "予測誤差注入",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.PredictiveErrorInjectionEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "多体解法",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.MultiBodySolverEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "衝突法線可視化",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.CollisionNormalVisualizerEnabled = value
    end
})

PhysicsTab:CreateToggle({
    Name = "終端速度キャップ",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.TerminalVelocityCapEnabled = value
    end
})

PhysicsTab:CreateSlider({
    Name = "終端速度",
    Range = {10, 500},
    Increment = 10,
    Suffix = "スタッド/秒",
    CurrentValue = 100,
    Callback = function(value)
        State.PhysicsFling.TerminalVelocity = value
    end
})

PhysicsTab:CreateToggle({
    Name = "リプレイ決定論モード",
    CurrentValue = false,
    Callback = function(value)
        State.PhysicsFling.ReplayDeterminismModeEnabled = value
    end
})

-- 🎮 UI/操作革命系タブ
local UITab = Window:CreateTab("UI/操作", 4483345998)

UITab:CreateSection("UI機能")

UITab:CreateToggle({
    Name = "放射状フリングホイール",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.RadialFlingWheelEnabled = value
        if value then CreateRadialFlingWheel() end
    end
})

UITab:CreateToggle({
    Name = "ジェスチャーフリング",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.GestureFlingEnabled = value
        if value then SetupGestureFling() end
    end
})

UITab:CreateToggle({
    Name = "ワンボタンスマートフリング",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.OneButtonSmartFlingEnabled = value
    end
})

UITab:CreateToggle({
    Name = "ホールドトゥプレビュー",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.HoldToPreviewEnabled = value
        if value then SetupHoldToPreview() end
    end
})

UITab:CreateToggle({
    Name = "コンテキストグラブメニュー",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.ContextGrabMenuEnabled = value
    end
})

UITab:CreateToggle({
    Name = "クイックプリセットスタック",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.QuickPresetStackEnabled = value
    end
})

UITab:CreateToggle({
    Name = "タイムラインスクラバー",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.TimelineScrubberEnabled = value
    end
})

UITab:CreateToggle({
    Name = "フリングブックマーク",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.FlingBookmarkEnabled = value
    end
})

UITab:CreateInput({
    Name = "ブックマーク名",
    PlaceholderText = "名前を入力",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        if text ~= "" then
            SaveFlingBookmark(text, {
                ActiveGrabType = State.Combat.ActiveGrabType,
                StrengthPower = State.Combat.StrengthPower,
                CurveFlingEnabled = State.FlingExtensions.CurveFlingEnabled
            })
        end
    end
})

UITab:CreateToggle({
    Name = "ターゲット優先度UI",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.TargetPriorityUIEnabled = value
        if value then SetupTargetPriorityUI() end
    end
})

UITab:CreateToggle({
    Name = "HUD最小モード",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.HUDMinimalModeEnabled = value
        if value then SetupHUDMinimalMode() end
    end
})

UITab:CreateToggle({
    Name = "視線追跡アシスト",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.EyeTrackingAssistEnabled = value
    end
})

UITab:CreateToggle({
    Name = "サウンドAS UI",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.SoundAsUIEnabled = value
        if value then SetupSoundAsUI() end
    end
})

UITab:CreateToggle({
    Name = "適応的感度",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.AdaptiveSensitivityEnabled = value
    end
})

UITab:CreateToggle({
    Name = "エラー許容ゾーン",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.ErrorForgivenessZoneEnabled = value
    end
})

UITab:CreateToggle({
    Name = "インスタントリプレイボタン",
    CurrentValue = false,
    Callback = function(value)
        State.UIFling.InstantReplayButtonEnabled = value
        if value then SetupInstantReplay() end
    end
})

-- メインループ
RunService.Heartbeat:Connect(function()
    -- ESP更新
    if State.ESP.Enabled then
        UpdateESP()
    end
    
    -- 強化更新
    UpdateEnhancements()
    
    -- 自動ターゲットロック
    if State.Combat.AutoLockEnabled and not State.Combat.TargetPlayer then
        local closest = GetClosestPlayer()
        if closest then
            State.Combat.TargetPlayer = closest.Name
        end
    end
    
    -- 自動ブリング
    if State.Combat.AutoBringEnabled and State.Combat.TargetPlayer then
        local target = Players:FindFirstChild(State.Combat.TargetPlayer)
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            local targetPos = target.Character.HumanoidRootPart.Position
            Character:MoveTo(targetPos)
        end
    end
    
    -- 自動キック
    if State.Combat.AutoKickEnabled and State.Combat.TargetPlayer then
        local target = Players:FindFirstChild(State.Combat.TargetPlayer)
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            ProcessFling(target.Character.HumanoidRootPart, Vector3.new(0, 1, 0), State.Combat.KickPower)
        end
    end
    
    -- クイックテレポート
    if State.Misc.QuickTeleportEnabled and UserInputService:IsKeyDown(Enum.KeyCode.T) then
        local closest = GetClosestPlayer()
        if closest and closest.Character and closest.Character:FindFirstChild("HumanoidRootPart") then
            Character:MoveTo(closest.Character.HumanoidRootPart.Position)
        end
    end
    
    -- カスタムライン
    if State.Misc.CustomLineEnabled and Character and Character:FindFirstChild("HumanoidRootPart") then
        local target = State.Combat.TargetPlayer and GetPlayerByName(State.Combat.TargetPlayer)
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            local startPos = Character.HumanoidRootPart.Position
            local endPos = target.Character.HumanoidRootPart.Position
            local distance = (endPos - startPos).Magnitude
            
            local line = Instance.new("Part")
            line.Size = Vector3.new(State.Misc.LineThickness / 10, State.Misc.LineThickness / 10, distance)
            line.CFrame = CFrame.lookAt(startPos, endPos) * CFrame.new(0, 0, -distance/2)
            line.Color = State.Misc.LineColor
            line.Material = Enum.Material.Neon
            line.Transparency = 0.3
            line.Anchored = true
            line.CanCollide = false
            line.Parent = Workspace
            
            Debris:AddItem(line, 0.1)
        end
    end
    
    -- ワンボタンスマートフリング
    if State.UIFling.OneButtonSmartFlingEnabled and UserInputService:IsKeyDown(Enum.KeyCode.F) then
        local closest = GetClosestPlayer()
        if closest and closest.Character and closest.Character:FindFirstChild("HumanoidRootPart") then
            ProcessFling(closest.Character.HumanoidRootPart, Vector3.new(0, 1, 0), 500)
        end
    end
end)

-- 衝突検出
workspace.DescendantAdded:Connect(function(descendant)
    if descendant:IsA("BasePart") and descendant.Velocity.Magnitude > 100 then
        -- 高速度で移動しているパーツを検出
        local touchConnection = descendant.Touched:Connect(function(hit)
            ProcessImpact(descendant, hit.Position)
            touchConnection:Disconnect()
        end)
        Debris:AddItem(touchConnection, 5)
    end
end)

-- 初期化完了
Rayfield:Notify({
    Title = "Syu_hub v10.5",
    Content = "完全拡張版が正常に起動しました\n全75機能が利用可能です",
    Duration = 5,
    Image = 4483345998
})

print("Syu_hub v10.5: 全75機能を統合した完全版が起動しました")
print("=== 機能一覧 ===")
print("⚔️ 戦闘システム: 10機能")
print("🛡️ 保護システム: 14機能")
print("👤 プレイヤー強化: 5機能")
print("🧠 ESPシステム: 8機能")
print("💥 雑多な機能: 7機能")
print("🖐️ GRAB拡張: 15機能")
print("🚀 FLING拡張: 12機能")
print("🧱 着地・衝突後: 8機能")
print("👁️ 観測・演出: 28機能")
print("🧠 物理ガチ勢: 15機能")
print("🎮 UI/操作革命: 15機能")
print("=================")
print("合計: 137機能 (要求75機能 + 追加62機能)")
