local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local TextChatService = game:GetService("TextChatService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local localPlayer = Players.LocalPlayer

local config = {
    enabled = false,
    tpEnabled = false, 
    shieldBreaker = false,
    infJump = false,
    autoChat = false, -- 自動チャット設定
    chatMessage = "こんにちは！",
    chatInterval = 5, -- チャットの間隔（秒）
    interval = 0.1, 
    range = 100,      
    keyToTap = Enum.KeyCode.F,
    lookAtTarget = true,
    holdTime = 0.05
}

local function SendChatMessage(message)
    if TextChatService.ChatVersion == Enum.ChatVersion.TextChatService then
        local channel = TextChatService.TextChannels.RBXGeneral
        if channel then
            channel:SendAsync(message)
        end
    else
        local remote = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents") and ReplicatedStorage.DefaultChatSystemChatEvents:FindFirstChild("SayMessageRequest")
        if remote then
            remote:FireServer(message, "All")
        end
    end
end

task.spawn(function()
    task.wait(1)
    SendChatMessage("こんにちは")
end)

task.spawn(function()
    while true do
        if config.autoChat then
            SendChatMessage(config.chatMessage)
        end
        task.wait(config.chatInterval)
    end
end)

local Window = Rayfield:CreateWindow({
    Name = "yazu hub ❤(/ω＼)ｲﾔﾝ機能少ないです❤",
    LoadingTitle = "yazu hub ❤(/ω＼)ｲﾔﾝ Loading...",
    LoadingSubtitle = "yazu hub ❤",
    ConfigurationSaving = {
        Enabled = false
    },
    KeySystem = false
})

local MainTab = Window:CreateTab("メイン設定", 4483362458)
local PlayerTab = Window:CreateTab("プレイヤー設定", 4483362458)
local MiscTab = Window:CreateTab("その他", 4483362458)

local Section = MainTab:CreateSection("自動掴み設定")

local Toggle = MainTab:CreateToggle({
    Name = "自動掴みを起動",
    CurrentValue = false,
    Flag = "AutoGrabToggle",
    Callback = function(Value)
        config.enabled = Value
        if Value then
            Rayfield:Notify({
                Title = "起動",
                Content = "自動掴みモードがオンになりました。",
                Duration = 3,
                Image = 4483362458,
            })
        end
    end,
})

local ShieldToggle = MainTab:CreateToggle({
    Name = "Shield Breaker (高速連打+物理振動)",
    CurrentValue = false,
    Flag = "ShieldBreakerToggle",
    Callback = function(Value)
        config.shieldBreaker = Value
        if Value then
            config.interval = 0.03
            config.holdTime = 0.01
        else
            config.interval = 0.1
            config.holdTime = 0.05
        end
    end,
})

local TPToggle = MainTab:CreateToggle({
    Name = "テレポート掴み (遠くの人を捕まえに行く)",
    CurrentValue = false,
    Flag = "TPGrabToggle",
    Callback = function(Value)
        config.tpEnabled = Value
    end,
})

local RangeSlider = MainTab:CreateSlider({
    Name = "ターゲット検索範囲 (Studs)",
    Range = {5, 500},
    Increment = 5,
    Suffix = "Studs",
    CurrentValue = 100,
    Flag = "RangeSlider",
    Callback = function(Value)
        config.range = Value
    end,
})

local PSection = PlayerTab:CreateSection("移動・アクション")

local JumpToggle = PlayerTab:CreateToggle({
    Name = "無限ジャンプ",
    CurrentValue = false,
    Flag = "InfJumpToggle",
    Callback = function(Value)
        config.infJump = Value
    end,
})

local MSection = MiscTab:CreateSection("自動チャット設定")

local ChatToggle = MiscTab:CreateToggle({
    Name = "自動で「こんにちは！」と言う",
    CurrentValue = false,
    Flag = "AutoChatToggle",
    Callback = function(Value)
        config.autoChat = Value
    end,
})

local ChatIntervalSlider = MiscTab:CreateSlider({
    Name = "チャットの間隔 (秒)",
    Range = {1, 60},
    Increment = 1,
    Suffix = "秒",
    CurrentValue = 5,
    Flag = "ChatIntervalSlider",
    Callback = function(Value)
        config.chatInterval = Value
    end,
})

UserInputService.JumpRequest:Connect(function()
    if config.infJump then
        local char = localPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

local function performGrab(target)
    if not target then return end
    
    local char = localPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    if config.tpEnabled then
        root.CFrame = target.CFrame * CFrame.new(0, 0, 2)
        task.wait(0.01)
    end

    if config.lookAtTarget then
        local targetPos = Vector3.new(target.Position.X, root.Position.Y, target.Position.Z)
        root.CFrame = CFrame.lookAt(root.Position, targetPos)
    end

    if config.shieldBreaker then
        root.CFrame = root.CFrame * CFrame.new(math.random(-1, 1) / 10, 0, math.random(-1, 1) / 10)
    end

    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
    VirtualInputManager:SendKeyEvent(true, config.keyToTap, false, game)
    task.wait(config.holdTime)
    VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 0)
    VirtualInputManager:SendKeyEvent(false, config.keyToTap, false, game)
end

task.spawn(function()
    while true do
        if config.enabled then
            local char = localPlayer.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if root then
                local targetPart = nil
                local closestDist = config.range
                for _, other in pairs(Players:GetPlayers()) do
                    if other ~= localPlayer and other.Character then
                        local oRoot = other.Character:FindFirstChild("HumanoidRootPart")
                        local oHum = other.Character:FindFirstChild("Humanoid")
                        if oRoot and oHum and oHum.Health > 0 then
                            local d = (root.Position - oRoot.Position).Magnitude
                            if d <= closestDist then
                                targetPart = oRoot
                                closestDist = d
                            end
                        end
                    end
                end
                if targetPart then
                    performGrab(targetPart)
                end
            end
        end
        task.wait(config.interval)
    end
end)

Rayfield:Notify({
    Title = "yazu hub",
    Content = "自動チャット機能を追加しました❤",
    Duration = 5,
    Image = 4483362458,
})
