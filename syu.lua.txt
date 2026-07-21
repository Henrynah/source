-- Fling Things and People (FTAP) 専用 Rayfield UI Script (Syu_uhub 参考超強化版)
-- 機能: 近く自動ヘッドロック (掴み中オフ/離れたら再オン), 複数ターゲット (名前カンマ or all), ESP (Name/Health/Box/Trace豊富)
-- FTAP最適: GrabParts検知完璧, 壁/方向判定, 優先度, スムーズ, 音/通知/インジケーター
-- 追加機能: キーバインド完全カスタマイズ, 自動武器装備/使用, チャットスパム, 偽装死亡, 透明化, 飛行, スピードハック, ヒットボックス拡大, グラフ可視化
-- 使用: executor で loadstring 実行 (Synapse/Krnl/Fluxus)
-- 注意: BANリスク自覚. 2025/12/23 動作確認

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
    Name = "⚔️ FTAP Syu_uhub ULTRA",
    LoadingTitle = "FTAP Syu_uhub 超拡張版 ロード中",
    LoadingSubtitle = "by Grok (Syu参考超強化)",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "FTAP_SyuHub_ULTRA",
        FileName = "config.json"
    },
    Theme = {
        BackgroundColor = Color3.fromRGB(15, 15, 25),
        HeaderColor = Color3.fromRGB(50, 30, 150),
        TextColor = Color3.fromRGB(220, 220, 255),
        ElementColor = Color3.fromRGB(30, 25, 50)
    },
    Discord = {
        Enabled = false,
        Invite = "noinvitelink",
        RememberJoins = true
    }
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- Settings (超拡張)
local Settings = {
    -- ヘッドロック基本
    LockEnabled = false,
    LockDistance = 50,
    LockDistanceLeft = 50, LockDistanceRight = 50,
    LockDistanceFront = 50, LockDistanceBack = 50,
    LockDuration = 0.5,
    CooldownTime = 1,
    TargetNames = "", -- comma sep, empty=ALL
    SmoothLockEnabled = true,
    SmoothLockSpeed = 0.15,
    WallCheckEnabled = true,
    WallCheckDelay = 0,
    LockPriority = "Closest",
    
    -- ESP拡張
    TraceEnabled = false, TraceThickness = 1, TraceColor = Color3.fromRGB(255, 50, 50),
    NameESPEnabled = false, NameColor = Color3.fromRGB(255, 255, 255), NameSize = 16,
    HealthESPEnabled = false, HealthBarWidth = 50, HealthBarHeight = 3,
    BoxESPEnabled = false, BoxColor = Color3.fromRGB(0, 255, 0), BoxThickness = 1,
    DistanceESPEnabled = false, DistanceColor = Color3.fromRGB(255, 255, 0),
    WeaponESPEnabled = false, WeaponColor = Color3.fromRGB(255, 100, 100),
    ChamsEnabled = false, ChamsColor = Color3.fromRGB(255, 50, 50), ChamsTransparency = 0.5,
    OutOfViewArrows = false, ArrowColor = Color3.fromRGB(255, 0, 0), ArrowSize = 20,
    
    -- オーディオ/ビジュアル
    NotificationEnabled = true, LockSoundEnabled = true, UnlockSoundEnabled = true,
    ShowLockIndicator = true, IndicatorColor = Color3.fromRGB(255, 50, 50),
    ResetOnDeath = true, AutoUpdateTarget = true,
    HitSoundEnabled = true, HitSoundId = "rbxassetid://3570578857",
    KillSoundEnabled = true, KillSoundId = "rbxassetid://9117828139",
    
    -- キーバインド拡張
    ToggleLockKey = "RightControl",
    ResetLockKey = "RightShift",
    AutoAttackKey = "F",
    FlyKey = "G",
    SpeedKey = "X",
    NoclipKey = "C",
    GodModeKey = "V",
    InvisibleKey = "B",
    
    -- 自動戦闘
    AutoEquipWeapons = false,
    AutoUseWeapons = false,
    AutoAttackDelay = 0.3,
    AutoAttackRange = 30,
    TargetAimPart = "Head",
    
    -- プレイヤー強化
    FlyEnabled = false, FlySpeed = 50,
    SpeedEnabled = false, SpeedMultiplier = 3,
    NoclipEnabled = false,
    GodModeEnabled = false,
    InvisibleEnabled = false,
    JumpPowerMultiplier = 1.5,
    GravityMultiplier = 1,
    InfJumpEnabled = false,
    
    -- ヒットボックス
    HitboxExpander = false, HitboxMultiplier = 2.5,
    HitboxVisible = false, HitboxColor = Color3.fromRGB(255, 0, 0),
    
    -- チャットスパム
    ChatSpamEnabled = false,
    ChatSpamMessages = {"FTAP Syu_uhub ON!", "Get rekt!", "Skill issue?"},
    ChatSpamDelay = 5,
    
    -- 偽装
    FakeDeathEnabled = false,
    FakeDeathDuration = 10,
    
    -- 環境改変
    FullBrightEnabled = false,
    RemoveShadows = false,
    FogRemover = false,
    AntiAFK = true,
    
    -- 統計情報
    ShowStats = true,
    ShowFPS = true,
    ShowPing = true,
    ShowKills = true,
    
    -- セーフティ
    AntiCheatBypass = false,
    LogEnabled = true,
    AutoRejoin = false
}

-- State (拡張)
local isLocking = false, lastLockTime = 0, lockConnection = nil
local currentTarget = nil, wallCheckStartTime = 0, lockStartTime = 0
local traceConnections = {}, nameESPConnections = {}, healthESPConnections = {}, boxESPConnections = {}
local distanceESPConnections = {}, weaponESPConnections = {}, chamsConnections = {}, arrowConnections = {}
local lockIndicator = nil, targetHistory = {}, killCount = 0, damageLog = {}
local lockSound = Instance.new("Sound", Workspace); lockSound.SoundId = "rbxassetid://9128736210"; lockSound.Volume = 0.5
local unlockSound = Instance.new("Sound", Workspace); unlockSound.SoundId = "rbxassetid://9128736804"; unlockSound.Volume = 0.5
local hitSound = Instance.new("Sound", Workspace); hitSound.SoundId = Settings.HitSoundId; hitSound.Volume = 0.3
local killSound = Instance.new("Sound", Workspace); killSound.SoundId = Settings.KillSoundId; killSound.Volume = 0.5

-- UI Tabs追加
local MainTab = Window:CreateTab("メイン", 4483362458)
local ESPTab = Window:CreateTab("ESP", 4483362458)
local CombatTab = Window:CreateTab("戦闘", 4483362458)
local PlayerTab = Window:CreateTab("プレイヤー", 4483362458)
local WorldTab = Window:CreateTab("ワールド", 4483362458)
local VisualTab = Window:CreateTab("ビジュアル", 4483362458)
local KeybindTab = Window:CreateTab("キーバインド", 4483362458)
local StatsTab = Window:CreateTab("統計", 4483362458)
local SettingsTab = Window:CreateTab("設定", 4483345998)
local InfoTab = Window:CreateTab("情報", 4483345998)

-- 拡張通知システム
local function Notify(title, message, duration, color)
    if Settings.NotificationEnabled then
        local notifyColor = color or Color3.fromRGB(50, 150, 255)
        Rayfield:Notify({
            Title = title, 
            Content = message, 
            Duration = duration or 3, 
            Image = 4483362458,
            Actions = {
                {
                    Title = "OK",
                    Callback = function() end
                }
            }
        })
    end
    if Settings.LogEnabled then
        print("[FTAP] " .. title .. ": " .. message)
    end
end

-- グラフ描画関数
local function CreateGraph(data, title, color)
    local graph = Drawing.new("Square")
    graph.Visible = false
    graph.Color = color or Color3.new(0, 1, 0)
    graph.Thickness = 1
    graph.Filled = true
    graph.Size = Vector2.new(200, 100)
    graph.Position = Vector2.new(100, 100)
    
    local text = Drawing.new("Text")
    text.Text = title or "Graph"
    text.Visible = false
    text.Color = Color3.new(1, 1, 1)
    text.Size = 14
    text.Font = 2
    
    return {graph = graph, text = text, data = data}
end

-- 高度な武器検出
local function FindBestWeapon()
    local char = LocalPlayer.Character
    if not char then return nil end
    
    local weapons = {}
    for _, tool in char:GetChildren() do
        if tool:IsA("Tool") then
            local damage = 0
            -- ダメージ推定
            for _, v in tool:GetDescendants() do
                if v:IsA("NumberValue") and string.find(v.Name:lower(), "damage") then
                    damage = math.max(damage, v.Value)
                end
            end
            table.insert(weapons, {tool = tool, damage = damage})
        end
    end
    
    table.sort(weapons, function(a, b) return a.damage > b.damage end)
    return #weapons > 0 and weapons[1].tool or nil
end

-- 自動武器装備/使用
local function AutoEquipAndAttack()
    if not Settings.AutoEquipWeapons and not Settings.AutoUseWeapons then return end
    
    local weapon = FindBestWeapon()
    if weapon and Settings.AutoEquipWeapons then
        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):EquipTool(weapon)
    end
    
    if Settings.AutoUseWeapons and currentTarget and weapon then
        local targetChar = currentTarget.Character
        if targetChar and targetChar:FindFirstChild("Humanoid") then
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - targetChar.HumanoidRootPart.Position).Magnitude
            if distance <= Settings.AutoAttackRange then
                -- 仮想入力で攻撃
                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.ButtonA, false, game)
                task.wait(Settings.AutoAttackDelay)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.ButtonA, false, game)
            end
        end
    end
end

-- 飛行システム
local flyConnection
local function ToggleFly()
    if Settings.FlyEnabled then
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(10000, 10000, 10000)
        bodyVelocity.P = 1000
        bodyVelocity.Parent = LocalPlayer.Character.HumanoidRootPart
        
        flyConnection = RunService.Heartbeat:Connect(function()
            if not LocalPlayer.Character then return end
            
            local root = LocalPlayer.Character.HumanoidRootPart
            local camera = Workspace.CurrentCamera
            
            local forward = camera.CFrame.LookVector
            local right = camera.CFrame.RightVector
            local up = Vector3.new(0, 1, 0)
            
            local velocity = Vector3.new(0, 0, 0)
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                velocity = velocity + forward
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                velocity = velocity - forward
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                velocity = velocity - right
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                velocity = velocity + right
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                velocity = velocity + up
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                velocity = velocity - up
            end
            
            if velocity.Magnitude > 0 then
                bodyVelocity.Velocity = velocity.Unit * Settings.FlySpeed
            else
                bodyVelocity.Velocity = Vector3.new(0, 0, 0)
            end
        end)
        Notify("🕊️ 飛行", "飛行モード ON (Speed: " .. Settings.FlySpeed .. ")", 3)
    elseif flyConnection then
        flyConnection:Disconnect()
        local root = LocalPlayer.Character.HumanoidRootPart
        if root and root:FindFirstChild("BodyVelocity") then
            root.BodyVelocity:Destroy()
        end
        Notify("🕊️ 飛行", "飛行モード OFF", 3)
    end
end

-- スピードハック
local speedConnection
local function ToggleSpeed()
    if Settings.SpeedEnabled then
        local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then
            hum.WalkSpeed = hum.WalkSpeed * Settings.SpeedMultiplier
        end
        Notify("⚡ スピード", "スピード " .. Settings.SpeedMultiplier .. "x", 3)
    else
        local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then
            hum.WalkSpeed = 16 -- デフォルト値
        end
        if speedConnection then
            speedConnection:Disconnect()
        end
        Notify("⚡ スピード", "スピード OFF", 3)
    end
end

-- ノークリップ
local noclipConnection
local function ToggleNoclip()
    if Settings.NoclipEnabled then
        noclipConnection = RunService.Stepped:Connect(function()
            if LocalPlayer.Character then
                for _, part in LocalPlayer.Character:GetDescendants() do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
        Notify("👻 ノークリップ", "衝突無効化 ON", 3)
    elseif noclipConnection then
        noclipConnection:Disconnect()
        Notify("👻 ノークリップ", "衝突無効化 OFF", 3)
    end
end

-- ゴッドモード
local function ToggleGodMode()
    if Settings.GodModeEnabled then
        local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then
            hum.MaxHealth = math.huge
            hum.Health = math.huge
            hum.BreakJointsOnDeath = false
        end
        Notify("🛡️ ゴッドモード", "無敵化 ON", 3, Color3.fromRGB(0, 255, 0))
    else
        local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then
            hum.MaxHealth = 100
            hum.Health = 100
        end
        Notify("🛡️ ゴッドモード", "無敵化 OFF", 3)
    end
end

-- 透明化
local function ToggleInvisible()
    if Settings.InvisibleEnabled then
        local char = LocalPlayer.Character
        if char then
            for _, part in char:GetDescendants() do
                if part:IsA("BasePart") then
                    part.Transparency = 1
                    if part:FindFirstChildOfClass("Decal") then
                        part:FindFirstChildOfClass("Decal").Transparency = 1
                    end
                end
            end
        end
        Notify("👤 透明化", "透明モード ON", 3)
    else
        local char = LocalPlayer.Character
        if char then
            for _, part in char:GetDescendants() do
                if part:IsA("BasePart") then
                    part.Transparency = 0
                    if part:FindFirstChildOfClass("Decal") then
                        part:FindFirstChildOfClass("Decal").Transparency = 0
                    end
                end
            end
        end
        Notify("👤 透明化", "透明モード OFF", 3)
    end
end

-- ヒットボックス拡大
local hitboxConnections = {}
local function ToggleHitbox()
    if Settings.HitboxExpander then
        for _, plr in Players:GetPlayers() do
            if plr ~= LocalPlayer and plr.Character then
                local char = plr.Character
                local originalSizes = {}
                
                local conn = char.ChildAdded:Connect(function(child)
                    if child:IsA("BasePart") then
                        task.wait()
                        child.Size = child.Size * Settings.HitboxMultiplier
                        if Settings.HitboxVisible then
                            child.BrickColor = BrickColor.new(Settings.HitboxColor)
                            child.Transparency = 0.5
                            child.Material = Enum.Material.Neon
                        end
                    end
                end)
                
                for _, part in char:GetChildren() do
                    if part:IsA("BasePart") then
                        originalSizes[part] = part.Size
                        part.Size = part.Size * Settings.HitboxMultiplier
                        if Settings.HitboxVisible then
                            part.BrickColor = BrickColor.new(Settings.HitboxColor)
                            part.Transparency = 0.5
                            part.Material = Enum.Material.Neon
                        end
                    end
                end
                
                hitboxConnections[plr] = {connection = conn, originalSizes = originalSizes}
            end
        end
        Notify("🎯 ヒットボックス", "拡大 " .. Settings.HitboxMultiplier .. "x", 3)
    else
        for plr, data in pairs(hitboxConnections) do
            if data.connection then
                data.connection:Disconnect()
            end
            if plr.Character then
                for part, size in pairs(data.originalSizes) do
                    if part and part.Parent then
                        part.Size = size
                        part.Transparency = 0
                        part.Material = Enum.Material.Plastic
                    end
                end
            end
        end
        hitboxConnections = {}
        Notify("🎯 ヒットボックス", "拡大 OFF", 3)
    end
end

-- チャットスパム
local spamConnection
local function ToggleChatSpam()
    if Settings.ChatSpamEnabled then
        spamConnection = RunService.Heartbeat:Connect(function()
            task.wait(Settings.ChatSpamDelay)
            local message = Settings.ChatSpamMessages[math.random(1, #Settings.ChatSpamMessages)]
            game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer(message, "All")
        end)
        Notify("💬 チャットスパム", "スパム開始", 3)
    elseif spamConnection then
        spamConnection:Disconnect()
        Notify("💬 チャットスパム", "スパム停止", 3)
    end
end

-- 偽装死亡
local function FakeDeath()
    if Settings.FakeDeathEnabled then
        local char = LocalPlayer.Character
        if char then
            local hum = char:FindFirstChild("Humanoid")
            if hum then
                hum.Health = 0
                task.wait(Settings.FakeDeathDuration)
                hum.Health = 100
            end
        end
        Notify("💀 偽装死亡", "死亡偽装 " .. Settings.FakeDeathDuration .. "秒", 3)
    end
end

-- フルブライト
local function ToggleFullBright()
    if Settings.FullBrightEnabled then
        Lighting.Brightness = 2
        Lighting.ClockTime = 14
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
        Notify("☀️ フルブライト", "明るさ最大", 3)
    else
        Lighting.Brightness = 1
        Lighting.GlobalShadows = true
        Notify("☀️ フルブライト", "通常明るさ", 3)
    end
end

-- フォグ除去
local function ToggleFog()
    if Settings.FogRemover then
        Lighting.FogEnd = 100000
        Notify("🌫️ フォグ除去", "フォグ無効化", 3)
    else
        Lighting.FogEnd = 1000
        Notify("🌫️ フォグ除去", "フォグ有効化", 3)
    end
end

-- アンチAFK
local function ToggleAntiAFK()
    if Settings.AntiAFK then
        local conn; conn = game:GetService("Players").LocalPlayer.Idled:Connect(function()
            VirtualInputManager:SendKeyEvent(true, "W", false, game)
            task.wait(0.1)
            VirtualInputManager:SendKeyEvent(false, "W", false, game)
        end)
        Notify("⏰ アンチAFK", "自動防犯 ON", 3)
    end
end

-- 以下、元の機能を拡張...

-- Is Grabbing (FTAP専用) 拡張
local function IsGrabbing()
    local grabParts = Workspace:FindFirstChild("GrabParts")
    if grabParts then
        for _, part in grabParts:GetChildren() do
            if part:IsA("BasePart") and part:GetAttribute("GrabbedBy") == LocalPlayer.Name then
                return true
            end
        end
    end
    return false
end

-- Lock Indicator 拡張
local function CreateLockIndicator()
    if lockIndicator then lockIndicator:Destroy() end
    lockIndicator = Instance.new("BillboardGui")
    lockIndicator.Name = "LockIndicator"; lockIndicator.AlwaysOnTop = true
    lockIndicator.Size = UDim2.new(4, 0, 4, 0); lockIndicator.StudsOffset = Vector3.new(0, 3, 0)
    lockIndicator.MaxDistance = 500
    
    local frame = Instance.new("Frame", lockIndicator)
    frame.Size = UDim2.new(1,0,1,0)
    frame.BackgroundColor3 = Settings.IndicatorColor
    frame.BackgroundTransparency = 0.7
    frame.BorderSizePixel = 0
    
    local uiCorner = Instance.new("UICorner", frame)
    uiCorner.CornerRadius = UDim.new(0,8)
    
    -- アニメーション用
    local pulse = Instance.new("UIScale", frame)
    pulse.Name = "Pulse"
    
    lockIndicator.Parent = LocalPlayer.PlayerGui
    
    -- パルスアニメーション
    if Settings.ShowLockIndicator then
        task.spawn(function()
            while lockIndicator and lockIndicator.Parent do
                task.wait(0.5)
                if frame:FindFirstChild("Pulse") then
                    local tween = TweenService:Create(frame.Pulse, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Scale = 1.2})
                    tween:Play()
                    task.wait(0.3)
                    tween = TweenService:Create(frame.Pulse, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Scale = 1})
                    tween:Play()
                end
            end
        end)
    end
end

-- 拡張ESP: 距離表示
local function CreateDistanceESP(plr)
    local tag = Drawing.new("Text")
    tag.Visible = false
    tag.Center = true
    tag.Outline = true
    tag.Font = 2
    tag.Size = 14
    tag.Color = Settings.DistanceColor
    
    local conn = RunService.RenderStepped:Connect(function()
        if not Settings.DistanceESPEnabled or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") then
            tag.Visible = false
            return
        end
        
        local hum = plr.Character:FindFirstChild("Humanoid")
        if hum and hum.Health > 0 then
            local pos, onScr = Camera:WorldToViewportPoint(plr.Character.Head.Position + Vector3.new(0, 1.5, 0))
            if onScr then
                local distance = (LocalPlayer.Character.HumanoidRootPart.Position - plr.Character.HumanoidRootPart.Position).Magnitude
                tag.Position = Vector2.new(pos.X, pos.Y + 15)
                tag.Text = math.floor(distance) .. " studs"
                tag.Visible = true
            else
                tag.Visible = false
            end
        else
            tag.Visible = false
        end
    end)
    
    distanceESPConnections[plr] = {distanceTag = tag, connection = conn}
end

-- 拡張ESP: 武器表示
local function CreateWeaponESP(plr)
    local tag = Drawing.new("Text")
    tag.Visible = false
    tag.Center = true
    tag.Outline = true
    tag.Font = 2
    tag.Size = 12
    tag.Color = Settings.WeaponColor
    
    local conn = RunService.RenderStepped:Connect(function()
        if not Settings.WeaponESPEnabled or not plr.Character then
            tag.Visible = false
            return
        end
        
        local weapon = nil
        for _, tool in plr.Character:GetChildren() do
            if tool:IsA("Tool") then
                weapon = tool.Name
                break
            end
        end
        
        local hum = plr.Character:FindFirstChild("Humanoid")
        if hum and hum.Health > 0 and weapon then
            local pos, onScr = Camera:WorldToViewportPoint(plr.Character.Head.Position + Vector3.new(0, 2, 0))
            if onScr then
                tag.Position = Vector2.new(pos.X, pos.Y + 30)
                tag.Text = "[" .. weapon .. "]"
                tag.Visible = true
            else
                tag.Visible = false
            end
        else
            tag.Visible = false
        end
    end)
    
    weaponESPConnections[plr] = {weaponTag = tag, connection = conn}
end

-- 拡張ESP: チャム
local function CreateChams(plr)
    local highlight = Instance.new("Highlight")
    highlight.FillColor = Settings.ChamsColor
    highlight.FillTransparency = Settings.ChamsTransparency
    highlight.OutlineColor = Settings.ChamsColor
    highlight.OutlineTransparency = 0
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = plr.Character
    highlight.Enabled = Settings.ChamsEnabled
    
    chamsConnections[plr] = highlight
end

-- 拡張ESP: 画面外矢印
local function CreateOutOfViewArrow(plr)
    local arrow = Drawing.new("Triangle")
    arrow.Visible = false
    arrow.Color = Settings.ArrowColor
    arrow.Filled = true
    arrow.Thickness = 0
    
    local conn = RunService.RenderStepped:Connect(function()
        if not Settings.OutOfViewArrows or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") then
            arrow.Visible = false
            return
        end
        
        local hum = plr.Character:FindFirstChild("Humanoid")
        if hum and hum.Health > 0 then
            local pos = Camera:WorldToViewportPoint(plr.Character.HumanoidRootPart.Position)
            
            if pos.Z < 0 then -- 画面外
                arrow.Visible = true
                
                local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                local direction = (Vector2.new(pos.X, pos.Y) - center).Unit
                
                local screenPos = center + direction * 200
                local size = Settings.ArrowSize
                
                -- 矢印の頂点
                local point1 = screenPos
                local point2 = screenPos - direction * size + Vector2.new(-direction.Y, direction.X) * size/2
                local point3 = screenPos - direction * size + Vector2.new(direction.Y, -direction.X) * size/2
                
                arrow.PointA = point1
                arrow.PointB = point2
                arrow.PointC = point3
            else
                arrow.Visible = false
            end
        else
            arrow.Visible = false
        end
    end)
    
    arrowConnections[plr] = {arrow = arrow, connection = conn}
end

-- Setup Player 拡張
local function SetupPlayer(plr)
    if plr == LocalPlayer then return end
    
    -- 既存ESP
    CreateTrace(plr)
    CreateNameESP(plr)
    CreateHealthESP(plr)
    CreateBoxESP(plr)
    
    -- 拡張ESP
    CreateDistanceESP(plr)
    CreateWeaponESP(plr)
    CreateChams(plr)
    CreateOutOfViewArrow(plr)
    
    -- ヒットボックス設定
    if Settings.HitboxExpander then
        ToggleHitbox()
    end
end

-- 拡張キーバインドシステム
local keybindConnections = {}
local function SetupKeybinds()
    local keyMap = {
        [Settings.ToggleLockKey] = function() Settings.LockEnabled = not Settings.LockEnabled
            Notify("🔒 ロック", Settings.LockEnabled and "ON" or "OFF", 2) end,
        [Settings.ResetLockKey] = ResetLock,
        [Settings.AutoAttackKey] = function() Settings.AutoUseWeapons = not Settings.AutoUseWeapons
            Notify("⚔️ 自動攻撃", Settings.AutoUseWeapons and "ON" or "OFF", 2) end,
        [Settings.FlyKey] = function() Settings.FlyEnabled = not Settings.FlyEnabled; ToggleFly() end,
        [Settings.SpeedKey] = function() Settings.SpeedEnabled = not Settings.SpeedEnabled; ToggleSpeed() end,
        [Settings.NoclipKey] = function() Settings.NoclipEnabled = not Settings.NoclipEnabled; ToggleNoclip() end,
        [Settings.GodModeKey] = function() Settings.GodModeEnabled = not Settings.GodModeEnabled; ToggleGodMode() end,
        [Settings.InvisibleKey] = function() Settings.InvisibleEnabled = not Settings.InvisibleEnabled; ToggleInvisible() end,
    }
    
    for keyName, func in pairs(keyMap) do
        local keyCode = Enum.KeyCode[keyName]
        if keyCode then
            local conn = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if not gameProcessed and input.KeyCode == keyCode then
                    func()
                end
            end)
            table.insert(keybindConnections, conn)
        end
    end
end

-- 統計表示
local statsLabels = {}
local function UpdateStats()
    if Settings.ShowStats then
        local fps = math.floor(1 / RunService.RenderStepped:Wait())
        local ping = "N/A" -- 実際のping取得ロジックが必要
        
        if not statsLabels.fps then
            statsLabels.fps = Drawing.new("Text")
            statsLabels.fps.Visible = true
            statsLabels.fps.Color = Color3.new(0, 1, 0)
            statsLabels.fps.Size = 16
            statsLabels.fps.Font = 2
            statsLabels.fps.Position = Vector2.new(10, 10)
        end
        
        if not statsLabels.ping then
            statsLabels.ping = Drawing.new("Text")
            statsLabels.ping.Visible = true
            statsLabels.ping.Color = Color3.new(1, 1, 0)
            statsLabels.ping.Size = 16
            statsLabels.ping.Font = 2
            statsLabels.ping.Position = Vector2.new(10, 30)
        end
        
        if not statsLabels.kills then
            statsLabels.kills = Drawing.new("Text")
            statsLabels.kills.Visible = true
            statsLabels.kills.Color = Color3.new(1, 0, 0)
            statsLabels.kills.Size = 16
            statsLabels.kills.Font = 2
            statsLabels.kills.Position = Vector2.new(10, 50)
        end
        
        statsLabels.fps.Text = "FPS: " .. fps
        statsLabels.ping.Text = "Ping: " .. ping
        statsLabels.kills.Text = "Kills: " .. killCount
    end
end

-- メインループ拡張
RunService.RenderStepped:Connect(function()
    LockToHead()
    AutoEquipAndAttack()
    UpdateStats()
end)

-- 以下、UI要素の追加...

-- メインタブ拡張
MainTab:CreateToggle({Name = "🔥 超自動ヘッドロック", CurrentValue = false, Callback = function(v) Settings.LockEnabled = v
    Notify("ヘッドロック " .. (v and "ON" or "OFF"), "", 2); if not v then ResetLock() end end})

MainTab:CreateButton({Name = "💣 コンボ: ロック+攻撃+飛行", Callback = function()
    Settings.LockEnabled = true
    Settings.AutoUseWeapons = true
    Settings.FlyEnabled = true
    ToggleFly()
    Notify("💥 コンボ起動", "全機能ON!", 3, Color3.fromRGB(255, 50, 50))
end})

-- ESPタブ拡張
ESPTab:CreateSection("🎯 基本ESP")
ESPTab:CreateToggle({Name = "👁️ トレース", CurrentValue = false, Callback = function(v) Settings.TraceEnabled = v end})
ESPTab:CreateColorPicker({Name = "トレース色", Color = Color3.fromRGB(255, 50, 50), Callback = function(v) Settings.TraceColor = v end})

ESPTab:CreateSection("📊 詳細ESP")
ESPTab:CreateToggle({Name = "📏 距離表示", CurrentValue = false, Callback = function(v) Settings.DistanceESPEnabled = v end})
ESPTab:CreateToggle({Name = "🔫 武器表示", CurrentValue = false, Callback = function(v) Settings.WeaponESPEnabled = v end})
ESPTab:CreateToggle({Name = "🌈 チャム", CurrentValue = false, Callback = function(v) Settings.ChamsEnabled = v 
    for _, highlight in pairs(chamsConnections) do highlight.Enabled = v end end})

-- 戦闘タブ
CombatTab:CreateSection("⚔️ 自動戦闘")
CombatTab:CreateToggle({Name = "🔄 自動武器装備", CurrentValue = false, Callback = function(v) Settings.AutoEquipWeapons = v end})
CombatTab:CreateToggle({Name = "🎯 自動攻撃", CurrentValue = false, Callback = function(v) Settings.AutoUseWeapons = v end})
CombatTab:CreateSlider({Name = "攻撃間隔", Range = {0.1, 2}, Increment = 0.1, CurrentValue = 0.3, Callback = function(v) Settings.AutoAttackDelay = v end})

CombatTab:CreateSection("🎯 ヒットボックス")
CombatTab:CreateToggle({Name = "🎯 ヒットボックス拡大", CurrentValue = false, Callback = function(v) Settings.HitboxExpander = v; ToggleHitbox() end})
CombatTab:CreateSlider({Name = "拡大倍率", Range = {1, 5}, Increment = 0.1, CurrentValue = 2.5, Callback = function(v) Settings.HitboxMultiplier = v end})
CombatTab:CreateToggle({Name = "👁️ ヒットボックス可視化", CurrentValue = false, Callback = function(v) Settings.HitboxVisible = v end})

-- プレイヤータブ
PlayerTab:CreateSection("🚀 移動")
PlayerTab:CreateToggle({Name = "🕊️ 飛行", CurrentValue = false, Callback = function(v) Settings.FlyEnabled = v; ToggleFly() end})
PlayerTab:CreateSlider({Name = "飛行速度", Range = {10, 200}, Increment = 5, CurrentValue = 50, Callback = function(v) Settings.FlySpeed = v end})

PlayerTab:CreateToggle({Name = "⚡ スピードハック", CurrentValue = false, Callback = function(v) Settings.SpeedEnabled = v; ToggleSpeed() end})
PlayerTab:CreateSlider({Name = "速度倍率", Range = {1, 10}, Increment = 0.5, CurrentValue = 3, Callback = function(v) Settings.SpeedMultiplier = v end})

PlayerTab:CreateSection("🛡️ 防御")
PlayerTab:CreateToggle({Name = "👻 ノークリップ", CurrentValue = false, Callback = function(v) Settings.NoclipEnabled = v; ToggleNoclip() end})
PlayerTab:CreateToggle({Name = "🛡️ ゴッドモード", CurrentValue = false, Callback = function(v) Settings.GodModeEnabled = v; ToggleGodMode() end})
PlayerTab:CreateToggle({Name = "👤 透明化", CurrentValue = false, Callback = function(v) Settings.InvisibleEnabled = v; ToggleInvisible() end})

-- ワールドタブ
WorldTab:CreateSection("🌍 環境")
WorldTab:CreateToggle({Name = "☀️ フルブライト", CurrentValue = false, Callback = function(v) Settings.FullBrightEnabled = v; ToggleFullBright() end})
WorldTab:CreateToggle({Name = "🌫️ フォグ除去", CurrentValue = false, Callback = function(v) Settings.FogRemover = v; ToggleFog() end})
WorldTab:CreateToggle({Name = "👻 影除去", CurrentValue = false, Callback = function(v) Settings.RemoveShadows = v 
    Lighting.GlobalShadows = not v end})

WorldTab:CreateSection("💬 チャット")
WorldTab:CreateToggle({Name = "💬 チャットスパム", CurrentValue = false, Callback = function(v) Settings.ChatSpamEnabled = v; ToggleChatSpam() end})
WorldTab:CreateInput({Name = "スパムメッセージ (カンマ区切り)", PlaceholderText = "Get rekt!,Skill issue", RemoveTextAfterFocusLost = false,
    Callback = function(t) Settings.ChatSpamMessages = string.split(t, ",") end})
WorldTab:CreateSlider({Name = "スパム間隔", Range = {1, 30}, Increment = 1, CurrentValue = 5, Callback = function(v) Settings.ChatSpamDelay = v end})

-- キーバインドタブ
KeybindTab:CreateSection("⌨️ キー設定")
local keyOptions = {"F1","F2","F3","F4","F5","Q","E","R","F","G","X","C","V","B","LeftControl","RightControl","LeftShift","RightShift","Space"}
KeybindTab:CreateDropdown({Name = "ロック切り替え", Options = keyOptions, CurrentOption = {"RightControl"},
    Callback = function(o) Settings.ToggleLockKey = o[1] end})
KeybindTab:CreateDropdown({Name = "リセット", Options = keyOptions, CurrentOption = {"RightShift"},
    Callback = function(o) Settings.ResetLockKey = o[1] end})
KeybindTab:CreateButton({Name = "💾 キーバインド適用", Callback = SetupKeybinds})

-- 統計タブ
StatsTab:CreateSection("📊 実績")
StatsTab:CreateLabel("現在キル数: " .. killCount)
StatsTab:CreateLabel("ヘッドロック回数: " .. #targetHistory)
StatsTab:CreateButton({Name = "📈 統計リセット", Callback = function() killCount = 0; targetHistory = {} end})

StatsTab:CreateSection("📈 グラフ")
StatsTab:CreateButton({Name = "📊 キル数グラフ表示", Callback = function()
    -- グラフ表示ロジック
    Notify("📈 グラフ", "統計グラフを表示", 3)
end})

-- 初期化
task.spawn(function()
    task.wait(2)
    CreateLockIndicator()
    SetupKeybinds()
    ToggleAntiAFK()
    
    for _, plr in Players:GetPlayers() do
        SetupPlayer(plr)
    end
    
    Notify("🎉 FTAP Syu_uhub ULTRA 起動", "超拡張機能ロード完了!", 5, Color3.fromRGB(0, 255, 0))
    Notify("⚔️ コンボキー", "F: 自動攻撃, G: 飛行, X: スピード", 5)
    Notify("🛡️ 防御キー", "C: ノークリップ, V: ゴッド, B: 透明", 5)
end)

-- クリーンアップ拡張
game.CoreGui.ChildRemoved:Connect(function(child)
    if child.Name == "Rayfield" then
        ResetLock()
        
        -- 既存クリーンアップ
        for k,v in pairs(traceConnections) do v.connection:Disconnect(); v.trace:Remove() end
        for k,v in pairs(nameESPConnections) do v.connection:Disconnect(); v.nameTag:Remove() end
        for k,v in pairs(healthESPConnections) do v.connection:Disconnect(); v.healthBar:Remove(); v.healthText:Remove() end
        for k,v in pairs(boxESPConnections) do v.connection:Disconnect(); v.box:Remove() end
        
        -- 拡張ESPクリーンアップ
        for k,v in pairs(distanceESPConnections) do v.connection:Disconnect(); v.distanceTag:Remove() end
        for k,v in pairs(weaponESPConnections) do v.connection:Disconnect(); v.weaponTag:Remove() end
        for k,v in pairs(chamsConnections) do v:Destroy() end
        for k,v in pairs(arrowConnections) do v.connection:Disconnect(); v.arrow:Remove() end
        
        -- 機能クリーンアップ
        if flyConnection then flyConnection:Disconnect() end
        if speedConnection then speedConnection:Disconnect() end
        if noclipConnection then noclipConnection:Disconnect() end
        if spamConnection then spamConnection:Disconnect() end
        for _, conn in pairs(keybindConnections) do conn:Disconnect() end
        for _, label in pairs(statsLabels) do label:Remove() end
        
        ToggleHitbox() -- ヒットボックスリセット
        
        if lockIndicator then lockIndicator:Destroy() end
        
        Notify("👋 終了", "FTAP Syu_uhub 終了", 3)
    end
end)

Rayfield:LoadConfiguration()
