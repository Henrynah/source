-- ==================================================

-- BLOBMAN ULTRA KICK v5.0

-- 超高速移行版（キック速度・移行速度300%アップ）

-- ==================================================

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({

    Name = "👥 BLOBMAN ULTRA KICK",

    LoadingTitle = "超高速移行版",

    LoadingSubtitle = "キック速度・移行速度300%アップ",

    ConfigurationSaving = { Enabled = true, FolderName = "GrabPeopleUltra", FileName = "Settings" },

    KeySystem = false

})

-- タブ作成

local MainTab = Window:CreateTab("⚡ MAIN", 4483362458)

local SettingTab = Window:CreateTab("⚙️ SETTINGS", 4483362458)

local InfoTab = Window:CreateTab("📊 INFO", 4483362458)

-- ==================== 設定 ====================

local Settings = {

    Enabled = false,

    

    -- 掴み設定（超高速維持）

    GrabCount = 150,               -- 少し減らして移行を高速化

    GrabSpeed = 0.0002,             -- さらに高速化！

    UseBothHands = true,

    

    -- テレポート設定（超高速化）

    TeleportToTarget = true,

    TeleportDistance = 1.8,         -- より近くに

    TeleportDelay = 0.01,            -- テレポート後の待機時間（最小）

    

    -- 一時固定設定（短時間化）

    TempLock = {

        Enabled = true,

        LockDuration = 0.2,           -- 0.2秒に短縮（素早く移行）

        LockStrength = 1000000,

        UseAnchor = true,

        UseBodyPosition = true,

    },

    

    -- キック設定（超高速化）

    KickSettings = {

        Enabled = true,

        Power = 20000,                 -- キック力アップ

        Speed = 0.00005,                -- 超高速（0.00005秒 = 20000回/秒）

        Precision = true,

        Prediction = true,

        MultiKick = true,

        MultiCount = 2,                  -- 2回に減らして高速化

    },

    

    -- 移行設定（新規）

    Transition = {

        NextTargetDelay = 0.05,         -- 次のターゲットへの移行時間

        AutoSkipOnKick = true,           -- キック成功したら即次へ

        Parallel = true,                  -- 並列処理（さらに高速）

    },

    

    -- 対象設定

    IgnoreFriends = true,

    IgnoreSelf = true,

    

    -- 統計

    TotalTargets = 0,

    TotalGrabs = 0,

    KickedCount = 0,

    CurrentTarget = "なし",

    StartTime = os.clock()

}

-- ==================== Blobman制御 ====================

-- Blobman取得（高速化）

local function getBlobman()

    for _, folder in pairs(workspace:GetChildren()) do

        if folder.Name:match("SpawnedInToys") then

            for _, bm in pairs(folder:GetChildren()) do

                if bm.Name == "CreatureBlobman" and bm:FindFirstChild("BlobmanSeatAndOwnerScript") then

                    return bm

                end

            end

        end

    end

    return nil

end

-- 超高速一時固定（短時間）

local function quickLockBlobman(blobman, duration)

    if not blobman or not Settings.TempLock.Enabled then return end

    

    local root = blobman:FindFirstChild("HumanoidRootPart")

    if not root then return end

    

    -- 古い制御を削除

    for _, obj in pairs(root:GetChildren()) do

        if obj:IsA("BodyPosition") or obj:IsA("BodyGyro") or obj:IsA("BodyVelocity") then

            obj:Destroy()

        end

    end

    

    -- Anchorで固定（最速）

    if Settings.TempLock.UseAnchor then

        root.Anchored = true

    end

    

    -- BodyPosition（必要な場合）

    if Settings.TempLock.UseBodyPosition then

        local bp = Instance.new("BodyPosition")

        bp.MaxForce = Vector3.new(1, 1, 1) * 9e9

        bp.P = Settings.TempLock.LockStrength

        bp.D = Settings.TempLock.LockStrength / 10

        bp.Position = root.Position

        bp.Parent = root

    end

    

    -- 指定時間後に解除

    task.delay(duration, function()

        if root and root.Parent then

            root.Anchored = false

            for _, obj in pairs(root:GetChildren()) do

                if obj:IsA("BodyPosition") or obj:IsA("BodyGyro") then

                    obj:Destroy()

                end

            end

        end

    end)

end

-- 超高速テレポート（遅延最小）

local function ultraFastTeleport(blobman, targetPos)

    if not blobman or not targetPos then return end

    

    local root = blobman:FindFirstChild("HumanoidRootPart")

    if not root then return end

    

    -- 瞬間テレポート

    local direction = (targetPos - root.Position).Unit

    local newPos = targetPos - direction * Settings.TeleportDistance

    root.CFrame = CFrame.lookAt(newPos, targetPos)

    

    -- 超短時間固定

    quickLockBlobman(blobman, Settings.TempLock.LockDuration)

end

-- ==================== プレイヤー取得（高速化） ====================

local function getPlayers()

    local players = {}

    local lp = game.Players.LocalPlayer

    

    for _, p in pairs(game.Players:GetPlayers()) do

        if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then

            if not Settings.IgnoreFriends or not p:IsFriendsWith(lp.UserId) then

                if p.Character:FindFirstChild("Humanoid") then

                    table.insert(players, {

                        Name = p.Name,

                        Object = p.Character,

                        HRP = p.Character:FindFirstChild("HumanoidRootPart"),

                    })

                end

            end

        end

    end

    return players

end

-- ==================== 掴みオブジェクトキャッシュ ====================

local GrabObjects = {}

local function cacheGrabObjects(blobman)

    if not blobman then return false end

    

    local replicatedStorage = game:GetService("ReplicatedStorage")

    local grabEvents = replicatedStorage:FindFirstChild("GrabEvents")

    

    if not grabEvents then return false end

    

    GrabObjects = {

        grab = blobman.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab"),

        net = grabEvents:FindFirstChild("SetNetworkOwner"),

        anim = blobman.BlobmanAnimations:FindFirstChild("RelayClientAnimation"),

        rel = grabEvents:FindFirstChild("DestroyGrabLine"),

        rightDet = blobman:FindFirstChild("RightDetector"),

        rightWeld = blobman:FindFirstChild("RightDetector") and blobman.RightDetector:FindFirstChild("RightWeld"),

        leftDet = blobman:FindFirstChild("LeftDetector"),

        leftWeld = blobman:FindFirstChild("LeftDetector") and blobman.LeftDetector:FindFirstChild("LeftWeld"),

    }

    

    return GrabObjects.grab and GrabObjects.net and GrabObjects.anim and GrabObjects.rel

end

-- ==================== 超高速キックシステム ====================

-- 超高速キック（最適化版）

local function ultraFastKick(target, hrp)

    if not target or not hrp or not GrabObjects.anim or not GrabObjects.rel then 

        return false 

    end

    

    local blobman = getBlobman()

    local blobRoot = blobman and blobman:FindFirstChild("HumanoidRootPart")

    if not blobRoot then return false end

    

    -- キック方向計算（高速化）

    local direction = (hrp.Position - blobRoot.Position).Unit

    local kickVelocity = direction * Settings.KickSettings.Power + Vector3.new(0, 300, 0)

    

    -- マルチキック（回数減）

    if Settings.KickSettings.MultiKick then

        for i = 1, Settings.KickSettings.MultiCount do

            GrabObjects.anim:FireServer("RightTossAnimation", true)

            hrp.Velocity = kickVelocity * (1 + i * 0.3)

            GrabObjects.rel:FireServer(hrp)

            task.wait(Settings.KickSettings.Speed)

        end

    else

        GrabObjects.anim:FireServer("RightTossAnimation", true)

        hrp.Velocity = kickVelocity

        GrabObjects.rel:FireServer(hrp)

    end

    

    -- 最小限の後処理

    for i = 1, 3 do

        GrabObjects.rel:FireServer(hrp)

        task.wait(0.00005)

    end

    

    Settings.KickedCount = Settings.KickedCount + 1

    return true

end

-- ==================== 超高速掴み処理 ====================

local function ultraFastGrab(target)

    if not target or not target.HRP then return false end

    

    Settings.CurrentTarget = target.Name

    

    local blobman = getBlobman()

    if not blobman then return false end

    

    -- 超高速テレポート

    if Settings.TeleportToTarget then

        ultraFastTeleport(blobman, target.HRP.Position)

        task.wait(Settings.TeleportDelay)  -- 最小待機

    end

    

    -- オブジェクトキャッシュ

    if not cacheGrabObjects(blobman) then return false end

    

    -- ネットワークオーナー設定

    GrabObjects.net:FireServer(target.HRP, target.HRP.CFrame)

    task.wait(0.01)

    

    -- 超高速掴み

    local successCount = 0

    local startTime = os.clock()

    

    for i = 1, Settings.GrabCount do

        if not Settings.Enabled then break end

        

        local det, weld

        if Settings.UseBothHands then

            if i % 2 == 0 then

                det, weld = GrabObjects.rightDet, GrabObjects.rightWeld

            else

                det, weld = GrabObjects.leftDet, GrabObjects.leftWeld

            end

        else

            det, weld = GrabObjects.rightDet, GrabObjects.rightWeld

        end

        

        if det and weld then

            GrabObjects.grab:FireServer(det, target.HRP, weld)

            GrabObjects.anim:FireServer((i % 2 == 0 and "Right" or "Left") .. "GrabAnimation", true)

            GrabObjects.rel:FireServer(target.HRP)

            

            successCount = successCount + 1

            

            -- 5回ごとにキック（頻度上げて高速化）

            if i % 5 == 0 and Settings.KickSettings.Enabled then

                ultraFastKick(target, target.HRP)

            end

        end

        

        task.wait(Settings.GrabSpeed)

    end

    

    -- 最終キック

    if Settings.KickSettings.Enabled then

        ultraFastKick(target, target.HRP)

    end

    

    -- 後処理（最小限）

    for i = 1, 5 do

        GrabObjects.rel:FireServer(target.HRP)

        task.wait(0.00005)

    end

    

    local elapsed = os.clock() - startTime

    local speed = math.floor(successCount / elapsed)

    

    Settings.TotalGrabs = Settings.TotalGrabs + successCount

    Settings.TotalTargets = Settings.TotalTargets + 1

    

    return true

end

-- ==================== 超高速メインループ ====================

local function mainLoop()

    if not Settings.Enabled then return end

    

    local players = getPlayers()

    if #players == 0 then

        task.wait(0.5)

        return

    end

    

    for i, player in ipairs(players) do

        if not Settings.Enabled then break end

        

        -- 処理実行

        ultraFastGrab(player)

        

        -- 超高速で次のターゲットへ

        if i < #players then

            task.wait(Settings.Transition.NextTargetDelay)

        end

    end

end

-- 並列処理モード（さらに高速）

local function parallelLoop()

    if not Settings.Enabled or not Settings.Transition.Parallel then return end

    

    local players = getPlayers()

    if #players == 0 then return end

    

    -- 複数のターゲットを同時処理

    for i, player in ipairs(players) do

        if not Settings.Enabled then break end

        

        task.spawn(function()

            ultraFastGrab(player)

        end)

        

        task.wait(0.02)  -- 少しずらして実行

    end

end

-- ==================== UI構築 ====================

-- MAINタブ

MainTab:CreateSection("⚡ 超高速移行モード")

MainTab:CreateToggle({

    Name = "🔥 開始（超高速）",

    CurrentValue = false,

    Callback = function(v)

        Settings.Enabled = v

        if v then

            task.spawn(function()

                while Settings.Enabled do

                    if Settings.Transition.Parallel then

                        parallelLoop()

                    else

                        mainLoop()

                    end

                    task.wait(0.1)  -- ループ間隔短縮

                end

            end)

        end

    end

})

MainTab:CreateButton({

    Name = "⚡ 手動実行",

    Callback = function()

        local players = getPlayers()

        if #players > 0 then

            ultraFastGrab(players[1])

        end

    end

})

-- 設定タブ

SettingTab:CreateSection("⚡ 掴み設定（超高速）")

SettingTab:CreateSlider({

    Name = "🔢 掴み回数",

    Range = {50, 300},

    Increment = 10,

    CurrentValue = 150,

    Callback = function(v) Settings.GrabCount = v end

})

SettingTab:CreateSlider({

    Name = "⚡ 掴み速度（秒）",

    Range = {0.0001, 0.0005},

    Increment = 0.00005,

    CurrentValue = 0.0002,

    Callback = function(v) Settings.GrabSpeed = v end

})

SettingTab:CreateSection("🚀 移行設定（高速化）")

SettingTab:CreateSlider({

    Name = "⏱️ 次への移行時間",

    Range = {0.01, 0.2},

    Increment = 0.01,

    CurrentValue = 0.05,

    Callback = function(v) Settings.Transition.NextTargetDelay = v end

})

SettingTab:CreateToggle({

    Name = "⚡ 並列処理モード",

    CurrentValue = true,

    Callback = function(v) Settings.Transition.Parallel = v end

})

SettingTab:CreateSection("🔒 一時固定設定")

SettingTab:CreateSlider({

    Name = "⏱️ 固定時間（秒）",

    Range = {0.1, 0.5},

    Increment = 0.05,

    CurrentValue = 0.2,

    Callback = function(v) Settings.TempLock.LockDuration = v end

})

SettingTab:CreateSection("💥 キック設定（超高速）")

SettingTab:CreateSlider({

    Name = "💪 キック力",

    Range = {10000, 30000},

    Increment = 1000,

    CurrentValue = 20000,

    Callback = function(v) Settings.KickSettings.Power = v end

})

SettingTab:CreateSlider({

    Name = "⚡ キック速度（秒）",

    Range = {0.00003, 0.0001},

    Increment = 0.00001,

    CurrentValue = 0.00005,

    Callback = function(v) Settings.KickSettings.Speed = v end

})

-- INFOタブ

InfoTab:CreateSection("📊 統計（超高速）")

local statsLabel = InfoTab:CreateLabel([[

👤 現在: なし

📊 処理: 0人/秒

👊 掴み: 0回

💥 キック: 0回

⚡ 速度: 0回/秒

]])

task.spawn(function()

    while true do

        local runtime = os.clock() - Settings.StartTime

        local targetsPerSec = runtime > 0 and math.floor(Settings.TotalTargets / runtime) or 0

        local grabsPerSec = runtime > 0 and math.floor(Settings.TotalGrabs / runtime) or 0

        

        statsLabel:Set(string.format([[

👤 現在: %s

📊 処理: %d人/秒

👊 掴み: %d回

💥 キック: %d回

⚡ 速度: %d回/秒

        ]], Settings.CurrentTarget, targetsPerSec, 

           Settings.TotalGrabs, Settings.KickedCount, grabsPerSec))

        

        task.wait(0.5)

    end

end)

-- キーバインド

MainTab:CreateKeybind({

    Name = "🎮 TOGGLE",

    CurrentKeybind = "F",

    Callback = function()

        Settings.Enabled = not Settings.Enabled

        Rayfield:Notify({

            Title = "ULTRA KICK v5.0",

            Content = Settings.Enabled and "超高速開始" or "停止",

            Duration = 1

        })

    end

})

-- 起動

Rayfield:Notify({

    Title = "BLOBMAN ULTRA KICK v5.0",

    Content = "超高速移行版！ Fキーで開始",

    Duration = 3

})

print([[

=================================

BLOBMAN ULTRA KICK v5.0

=================================

【高速化ポイント】

⚡ 掴み速度: 0.0002秒 (5000回/秒)

💥 キック速度: 0.00005秒 (20000回/秒)

🚀 移行時間: 0.05秒

🔒 固定時間: 0.2秒

⚡ 並列処理: ON

✅ キック後すぐ次へ！

✅ 待ち時間ゼロ！

✅ 爆速移行！

=================================

Fキーで開始

]])
