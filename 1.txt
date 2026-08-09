--Скрипт был создан для того чтобы покетить игроков
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

local Window = Library:CreateWindow({
    Title = "P",
    Footer = "Lag",
    ShowCustomCursor = false,
    EnableCompacting = true,
    SidebarCompacted = false,
    CornerRadius = 4
})

Library.ToggleKeybind = Enum.KeyCode.RightShift

local Tab = Window:AddTab("L", nil)

local packetLagActive = false
local packetLagSizeMB = 1.5
local loopDelay = 0.15
local selectedLagType = "Resonance"
local customLagText = "0"
local showNotifications = false

local cachedData = "0"
local cacheBuilt = false
local packetCount = 0

local allSymbolsPattern = ""
for i = 0, 255 do
    allSymbolsPattern = allSymbolsPattern .. string.char(i)
end

local Patterns = {
    Resonance = "0",
    ["The Worst Premium Free"] = "FLOPPA LOVES YOU ",
    ["The Worst Private"] = "Bliz_T HUB | FLOPPA LOVES YOU ",
    ["HammaM Root"] = "MISHA LUV PDD❤️❤️❤️",
    ["HammaM Rat"] = "🐀🐛",
    ["Zero Bits"] = "\0",
    ["Max Power"] = allSymbolsPattern
}

local function Notify(title, text)
    pcall(function()
        game.StarterGui:SetCore("SendNotification", {
            Title = title,
            Text = text,
            Duration = 2
        })
    end)
end

local function GetPattern()
    if selectedLagType == "Custom" then
        return (customLagText and customLagText ~= "") and customLagText or "0"
    end
    return Patterns[selectedLagType] or "0"
end

local function RebuildCache()
    local pattern = GetPattern()
    local bytes = math.floor(packetLagSizeMB * 1024 * 1024)
    if bytes <= 0 then
        cachedData = pattern:sub(1, 1)
    else
        local patternLen = #pattern
        if patternLen == 0 then
            pattern = "0"
            patternLen = 1
        end
        local repetitions = math.ceil(bytes / patternLen)
        cachedData = string.rep(pattern, repetitions):sub(1, bytes)
    end
    cacheBuilt = true
end

local function GetTargetRemote()
    local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
    if not GrabEvents then return nil end
    return GrabEvents:FindFirstChild("ExtendGrabLine") or GrabEvents:FindFirstChild("SetNetworkOwner")
end

local function SendPacket()
    if not cacheBuilt then RebuildCache() end
    local remote = GetTargetRemote()
    if not remote then return end

    local startTime = tick()

    local success, err = pcall(function()
        if remote.ClassName == "RemoteFunction" then
            remote:InvokeServer(cachedData)
        else
            remote:FireServer(cachedData)
        end
    end)

    if not success then
        local maxSize = 1
        local step = 0.5
        
        while maxSize > 0.01 do
            local testSize = math.floor(maxSize * 1024 * 1024)
            local testData = string.rep("0", testSize)
            
            local testSuccess = pcall(function()
                if remote.ClassName == "RemoteFunction" then
                    remote:InvokeServer(testData)
                else
                    remote:FireServer(testData)
                end
            end)
            
            if testSuccess then
                local actualMB = math.floor(testSize / (1024 * 1024) * 100) / 100
                Notify("Лимит найден", "Макс размер: " .. actualMB .. " МБ")
                break
            end
            
            maxSize = maxSize - step
            task.wait(0.1)
        end
        
        return
    end

    local elapsed = tick() - startTime
    packetCount = packetCount + 1

    if showNotifications then
        Notify("Packet Sent", string.format("%.1f MB | #%d | %.2fs", packetLagSizeMB, packetCount, elapsed))
    end
end

task.spawn(function()
    while true do
        if packetLagActive then
            local startLoop = tick()
            pcall(SendPacket)
            local elapsed = tick() - startLoop
            local waitTime = math.max(0.01, loopDelay - elapsed)
            task.wait(waitTime)
        else
            task.wait(0.1)
        end
    end
end)

local LeftGroupBox = Tab:AddLeftGroupbox("Controls")
local RightGroupBox = Tab:AddRightGroupbox("Settings")

LeftGroupBox:AddToggle("T", {
    Text = "Lag active",
    Default = false,
    Callback = function(Value)
        packetLagActive = Value
    end
})

LeftGroupBox:AddToggle("Notifs", {
    Text = "Notifications",
    Default = false,
    Callback = function(Value)
        showNotifications = Value
    end
})

LeftGroupBox:AddButton({
    Text = "Single packet",
    Func = function()
        pcall(SendPacket)
    end,
    DoubleClick = false
})

RightGroupBox:AddDropdown("LagTypeDropdown", {
    Values = {
        "Resonance",
        "The Worst Premium Free",
        "The Worst Private",
        "HammaM Root",
        "HammaM Rat",
        "Zero Bits",
        "Max Power",
        "Custom"
    },
    Default = 1,
    Multi = false,
    Text = "Lag type",
    Callback = function(Value)
        selectedLagType = Value
        cacheBuilt = false
    end
})

RightGroupBox:AddInput("CustomLagInput", {
    Default = "0",
    Numeric = false,
    Finished = false,
    Text = "Custom pattern",
    Placeholder = "Enter text...",
    Callback = function(Value)
        customLagText = tostring(Value)
        if selectedLagType == "Custom" then
            cacheBuilt = false
        end
    end
})

RightGroupBox:AddSlider("S", {
    Text = "MB size",
    Default = 1.5,
    Min = 0.01,
    Max = 19,
    Rounding = 2,
    Compact = false,
    Callback = function(Value)
        packetLagSizeMB = Value
        cacheBuilt = false
    end
})

RightGroupBox:AddSlider("D", {
    Text = "Delay sec",
    Default = 0.15,
    Min = 0.01,
    Max = 10,
    Rounding = 2,
    Compact = false,
    Callback = function(Value)
        loopDelay = Value
    end
})
