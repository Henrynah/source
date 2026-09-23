loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()

local success, Rayfield = pcall(function()
    return loadstring(game:HttpGet("https://raw.githubusercontent.com/Its3rr0rsWRLD/Rayfield/refs/heads/main/source.lua"))()
end)

if not success or not Rayfield then
    warn("Failed to load Rayfield")
    return
end

local Window = Rayfield:CreateWindow({
    Name = "ENTHEREAL // HUB",
    Icon = "heart",
    LoadingTitle = "Finalizing Systems",
    LoadingSubtitle = "Please Wait",
    ShowText = "Ethereal",
    Theme = {
TextColor = Color3.fromRGB(180, 255, 120),          -- light greenish text for good contrast
Background = Color3.fromRGB(15, 15, 15),            -- very dark grey/black background for shade
Topbar = Color3.fromRGB(20, 40, 20),                -- dark green top bar
Shadow = Color3.fromRGB(30, 30, 30),                -- subtle dark shadow (greyish black)

NotificationBackground = Color3.fromRGB(25, 40, 25),        -- darker greenish notifications
NotificationActionsBackground = Color3.fromRGB(50, 90, 50), -- slightly lighter for action buttons

TabBackground = Color3.fromRGB(15, 20, 15),          -- dark green tab backgrounds
TabStroke = Color3.fromRGB(80, 220, 100),            -- bright green tab outlines
TabBackgroundSelected = Color3.fromRGB(80, 220, 100),-- bright green selected tab bg
TabTextColor = Color3.fromRGB(200, 255, 150),        -- lighter green text on tabs
SelectedTabTextColor = Color3.fromRGB(15, 15, 15),   -- very dark text on selected tab (contrast)

ElementBackground = Color3.fromRGB(20, 18, 25),      -- dark muted background for elements
ElementBackgroundHover = Color3.fromRGB(40, 70, 40), -- brighter green hover effect
SecondaryElementBackground = Color3.fromRGB(15, 15, 15), -- darker secondary elements

ElementStroke = Color3.fromRGB(80, 220, 100),        -- bright green stroke around elements
SecondaryElementStroke = Color3.fromRGB(40, 120, 60),-- darker green secondary stroke

SliderBackground = Color3.fromRGB(20, 18, 25),       -- slider background dark
SliderProgress = Color3.fromRGB(120, 255, 120),      -- bright green progress bar
SliderStroke = Color3.fromRGB(80, 220, 100),         -- green slider stroke

ToggleBackground = Color3.fromRGB(30, 30, 28),       -- dark toggle background
ToggleEnabled = Color3.fromRGB(120, 255, 120),       -- bright green toggle ON
ToggleDisabled = Color3.fromRGB(50, 60, 40),         -- muted green toggle OFF

ToggleEnabledStroke = Color3.fromRGB(90, 255, 90),   -- bright green toggle border ON
ToggleDisabledStroke = Color3.fromRGB(40, 120, 60),  -- darker green toggle border OFF
ToggleEnabledOuterStroke = Color3.fromRGB(80, 220, 100),
ToggleDisabledOuterStroke = Color3.fromRGB(20, 60, 20),

DropdownSelected = Color3.fromRGB(40, 70, 40),       -- brighter green dropdown selected
DropdownUnselected = Color3.fromRGB(15, 15, 15),     -- dark unselected dropdown

InputBackground = Color3.fromRGB(25, 18, 25),        -- dark input box background
InputStroke = Color3.fromRGB(80, 220, 100),          -- bright green input border
PlaceholderColor = Color3.fromRGB(180, 255, 140),     -- soft light green placeholder text
    },
    DisableRayfieldPrompts = true,
    DisableBuildWarnings = true,
    FreeMouse = true,
    ConfigurationSaving = { Enabled = false, FolderName = nil, FileName = "void_hub" },
    Discord = { Enabled = false, Invite = "noinvitelink", RememberJoins = true },
    KeySystem = false,
    KeySettings = {
        Title = "Key is skibiditoilet",
        Subtitle = "Key System",
        Note = "people without whitelist access will get this prompt. there is no key for this script.",
        FileName = "Key",
        SaveKey = false,
        GrabKeyFromSite = false,
        Key = {"skibiditoilet"}
    }
})

ReallyDarkNiggas = {
 ["JobsonNumber"] = true,
 ["QR4TL"] = true,
 ["66hxstxfy"] = true,
 ["Test002533533"] = true,
}

Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
if not ReallyDarkNiggas[LocalPlayer.Name] then
    if Rayfield then Rayfield:Destroy() end
   return
end

local PlayerTab = Window:CreateTab("PLAYER", "user")

PlayerTab:CreateButton({
    Name = "DESTROY GUI",
    Callback = function()
        Rayfield:Destroy()
    end,
})

local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")

PlayerTab:CreateButton({
    Name = "REJOIN SERVER",
    Font = Enum.Font.GothamSemibold,
    Callback = function()
        local player = Players.LocalPlayer
        TeleportService:Teleport(game.PlaceId, player)
    end
})

local CurrentPlayerParagraph

local function updateCurrentPlayerUI()
    if CurrentPlayerParagraph then
        CurrentPlayerParagraph:Set({
            Title = "CURRENT PLAYER",
            Content = string.upper(LocalPlayer.DisplayName .. " (@" .. LocalPlayer.Name .. ")")
        })
    end
end

CurrentPlayerParagraph = PlayerTab:CreateParagraph({
    Title = "CURRENT PLAYER",
    Content = string.upper(LocalPlayer.DisplayName .. " (@" .. LocalPlayer.Name .. ")")
})

local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

LocalPlayer.CharacterAdded:Connect(function(char)
    character = char
    humanoid = char:WaitForChild("Humanoid")
    hrp = char:WaitForChild("HumanoidRootPart")
    updateCurrentPlayerUI()
end)

PlayerTab:CreateSection("SPEED")

speedEnabled, speedConnection = false, nil
customSpeedValue = 1

PlayerTab:CreateToggle({
    Name = "ENABLE CUSTOM SPEED",
    CurrentValue = false,
    Flag = "PLAYER_CUSTOMSPEED",
    Callback = function(v)
        speedEnabled = v
        if speedConnection then
            speedConnection:Disconnect()
            speedConnection = nil
        end
        if v then
            speedConnection = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if hrp and hum and hum.MoveDirection.Magnitude > 0 then
                    hrp.CFrame = hrp.CFrame + (hum.MoveDirection.Unit * customSpeedValue)
                end
            end)
        end
    end
})

PlayerTab:CreateInput({
    Name = "CUSTOM SPEED VALUE",
    CurrentValue = tostring(customSpeedValue),
    PlaceholderText = "ENTER SPEED",
    RemoveTextAfterFocusLost = false,
    Flag = "PLAYER_SPEEDINPUT",
    Callback = function(Text)
        local n = tonumber(Text)
        if n and n > 0 then customSpeedValue = n end
    end
})

PlayerTab:CreateSection("JUMP")

local jumpEnabled = false
local customJumpPower = 30
local jumpConnection = nil

PlayerTab:CreateToggle({
    Name = "JUMP POWER",
    CurrentValue = false,
    Callback = function(state)
        jumpEnabled = state

        if jumpConnection then
            jumpConnection:Disconnect()
            jumpConnection = nil
        end

        if state then
            jumpConnection = RunService.Heartbeat:Connect(function()
                if humanoid then
                    humanoid.UseJumpPower = true
                    humanoid.JumpPower = customJumpPower
                end
            end)
        else
            if humanoid then
                humanoid.UseJumpPower = false
                humanoid.JumpPower = 50
            end
        end
    end
})

PlayerTab:CreateInput({
    Name = "CHANGE JUMP POWER",
    CurrentValue = tostring(customJumpPower),
    PlaceholderText = "ENTER JUMP POWER",
    RemoveTextAfterFocusLost = false,
    Callback = function(text)
        local num = tonumber(text)
        if num and num > 0 then
            customJumpPower = num
        end
    end
})

PlayerTab:CreateToggle({
    Name = "INFINITE JUMP",
    CurrentValue = false,
    Callback = function(state)
        infJump = state

        if infJumpConnection then
            infJumpConnection:Disconnect()
            infJumpConnection = nil
        end

        if state then
            infJumpConnection = UserInputService.JumpRequest:Connect(function()
                if infJump and humanoid then
                    humanoid:ChangeState(Enum.HumanoidStateType.JUMPING)
                end
            end)
        end
    end
})

PlayerTab:CreateDivider()

local DEFAULT_GRAVITY = workspace.Gravity

PlayerTab:CreateSlider({
    Name = "GRAVITY",
    Range = {0, 300},
    Increment = 0.1,
    Suffix = "",
    CurrentValue = DEFAULT_GRAVITY,
    Flag = "GRAVITYSLIDER",
    Callback = function(Value)
        workspace.Gravity = Value
    end,
})

PlayerTab:CreateDivider()

PlayerTab:CreateKeybind({
    Name = "TELEPORT (TP)",
    CurrentKeybind = "F",
    HoldToInteract = false,
    Flag = "TP_KEYBIND",
    Callback = function()
        local char = plr.Character
        local hrp = char:WaitForChild("HumanoidRootPart")
        if mouse.Target and not slideTPT then
            hrp.CFrame = CFrame.new(mouse.Hit.x, mouse.Hit.y + 5, mouse.Hit.z)
        end
    end
})

PlayerTab:CreateToggle({
    Name = "SLIDE TP",
    CurrentValue = false,
    Flag = "SLIDE_TP_TOGGLE",
    Callback = function(Value)
        slideTPT = Value
    end
})

PlayerTab:CreateKeybind({
    Name = "STOP VELOCITY",
    CurrentKeybind = "G",
    HoldToInteract = false,
    Flag = "STOP_VELOCITY_KEYBIND",
    Callback = function()
        local char = plr.Character
        local hrp = char:WaitForChild("HumanoidRootPart")
        hrp.AssemblyLinearVelocity = Vector3.zero
    end
})

PlayerTab:CreateSection("CAMERA")

PlayerTab:CreateSlider({
    Name = "FOV",
    Range = {0, 120},
    Increment = 0.1,
    Suffix = "",
    CurrentValue = 70,
    Flag = "FOV_SLIDER",
    Callback = function(Value)
        workspace.CurrentCamera.FieldOfView = Value
    end,
})

PlayerTab:CreateToggle({
    Name = "THIRD PERSON",
    Default = false,
    Callback = function(Value)
        if Value then
            originalMaxZoom = LocalPlayer.CameraMaxZoomDistance
            originalMinZoom = LocalPlayer.CameraMinZoomDistance
            originalCameraMode = LocalPlayer.CameraMode

            LocalPlayer.CameraMaxZoomDistance = math.huge
            LocalPlayer.CameraMinZoomDistance = 0.5
            LocalPlayer.CameraMode = Enum.CameraMode.Classic
        else
            LocalPlayer.CameraMaxZoomDistance = originalMaxZoom
            LocalPlayer.CameraMinZoomDistance = originalMinZoom
            LocalPlayer.CameraMode = originalCameraMode
        end
    end
})

AntisTab = Window:CreateTab("ANTI", 6023426910)

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Debris = game:GetService("Debris")
LocalPlayer = Players.LocalPlayer

local defenseEnabled = false
local defenseConnection = nil
local defenseMode = "Fling"

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine")
CreateGrabEvent = GrabEvents:FindFirstChild("CreateGrabLine")

local function getAttacker()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Head") then return end
    local owner = char.Head:FindFirstChild("PartOwner")
    if not owner or not owner:IsA("StringValue") then return end
    return Players:FindFirstChild(owner.Value)
end

local function performFling(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 10000
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performKill(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 99999999999999
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performHeaven(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(0, 100, 0)
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 100, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 5)
    end)
end

local function performKick(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(0, 999999999999, 0)
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 99999999999999, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 5)
    end)
end

local function performRagdoll(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -90, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.1)
    end)
end

local function performHell(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        for _, part in ipairs(attacker.Character:GetDescendants()) do
            if part:IsA("BasePart") and not part.Anchored then
                part.CanCollide = false
            end
        end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -10000000, 0)
        bv.P = 12500
        bv.Parent = root
        local noclipConnection
        noclipConnection = RunService.Heartbeat:Connect(function()
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
        task.delay(1.5, function()
            if noclipConnection then noclipConnection:Disconnect() end
        end)
        Debris:AddItem(bv, 0.1)
    end)
end

local function performChina(attacker)
    if not attacker or not attacker.Character then return end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then DestroyGrabLine:FireServer(root) end
        root.CFrame = CFrame.new(591, 153, -101)
    end)
end

local crazyline = false
local function performSpamGrabLines(attacker)
    while crazyline do
        local char = LocalPlayer.Character
        if char then
            local head = char:FindFirstChild("Head")
            if head then
                local owner = head:FindFirstChild("PartOwner")
                if owner and owner:IsA("StringValue") then
                    local attacker = Players:FindFirstChild(owner.Value)
                    if attacker and attacker.Character then
                        local attackerHead = attacker.Character:FindFirstChild("Head")
                        local attackerHRP = attacker.Character:FindFirstChild("HumanoidRootPart")
                        if attackerHead and attackerHRP then
                            for i = 1, 3 do
                                pcall(function() CreateGrabEvent:FireServer(attackerHead, attackerHead.CFrame) end)
                            end
                            for i = 1, 3 do
                                pcall(function() CreateGrabEvent:FireServer(attackerHRP, attackerHRP.CFrame) end)
                            end
                        end
                    end
                end
            end
        end
        task.wait(1)
    end
end

local function startDefense()
    if defenseConnection then return end
    defenseConnection = RunService.Heartbeat:Connect(function()
        if not defenseEnabled then return end
        local attacker = getAttacker()
        if not attacker then return end
        if defenseMode == "Fling" then performFling(attacker)
        elseif defenseMode == "Kill" then performKill(attacker)
        elseif defenseMode == "Send to Heaven" then performHeaven(attacker)
        elseif defenseMode == "Kick" then performKick(attacker)
        elseif defenseMode == "Ragdoll" then performRagdoll(attacker)
        elseif defenseMode == "Hell" then performHell(attacker)
        elseif defenseMode == "China" then performChina(attacker)
        elseif defenseMode == "GrabLine" then performSpamGrabLines(attacker)
        end
    end)
end

local function stopDefense()
    if defenseConnection then
        defenseConnection:Disconnect()
        defenseConnection = nil
    end
    for _, plr in ipairs(Players:GetPlayers()) do
        local char = plr.Character
        if char then
            for _, obj in ipairs(char:GetDescendants()) do
                if obj:IsA("BodyVelocity") and (obj.Name == "RinneganFling" or obj.Name == "RinneganHeaven") then
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

AntisTab:CreateSection("AUTO ATTACKER")

AntisTab:CreateToggle({
    Name = 'AUTO ATTACK <font face="GothamBlack" color="rgb(160, 255, 0)">[ATTACKS THE USER IF YOUR GRABBED]</font>',
    CurrentValue = false,
    Flag = "RinneganDefenseToggle",
    Callback = function(enabled)
        defenseEnabled = enabled
        if enabled then
            startDefense()
        else
            stopDefense()
        end
    end
})

AntisTab:CreateDropdown({
    Name = "MODE",
    Options = {"Fling", "Kill", "Send to Heaven","Kick","Ragdoll","Hell","China","GrabLine"},
    CurrentOption = "Fling",
    Flag = "RinneganMode",
    Callback = function(mode)
        if typeof(mode) == "table" then mode = mode[1] end
        defenseMode = mode
    end
})

AntisTab:CreateDivider()

local gucciEnabled = false
local gucciConnection = nil
local tractor = nil
local vehicleSeat = nil
local safePosition = nil
local restoreFrames = 0
local humanoid, rootPart, seat = nil
local antiGucciConnection = nil
local antiGrabConn = nil
local playerName = LocalPlayer.Name

local function spawnTractor()
    local args = {"TractorGreen", CFrame.new(0, 5000000, 0), Vector3.new(0, 60, 0)}
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("TractorGreen") then
        tractor = folder.TractorGreen
        vehicleSeat = tractor:FindFirstChild("VehicleSeat")
        if tractor:FindFirstChild("Main") then
            tractor.Main.CFrame = CFrame.new(0, 50000, 0)
            tractor.Main.Anchored = true
        end
    end
end

local function startGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    if vehicleSeat and vehicleSeat:IsA("VehicleSeat") then
        rootPart.CFrame = vehicleSeat.CFrame + Vector3.new(0, 2, 0)
        vehicleSeat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            restoreFrames = 15
            safePosition = rootPart.Position
        end
    end)

    if gucciConnection then gucciConnection:Disconnect() end
    gucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do task.wait(1) end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

local function stopGucci()
    if gucciConnection then gucciConnection:Disconnect() gucciConnection = nil end
    if tractor then
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(tractor)
        tractor:Destroy()
        tractor = nil
        vehicleSeat = nil
    end
end

AntisTab:CreateToggle({
    Name = 'TRACTOR GUCCI <font face="GothamBlack" color="rgb(160, 255, 0)">[INVISIBLE]</font>',
    CurrentValue = false,
    Callback = function(enabled)
        gucciEnabled = enabled
        if enabled then
            spawnTractor()
            task.wait(0.5)
            if tractor then startGucci() end
        else
            stopGucci()
        end
    end
})

AntisTab:CreateToggle({
    Name = 'TRAIN GUCCI <font face="GothamBlack" color="rgb(160, 255, 0)">[INVISIBLE]</font>',
    Callback = function(enabled)
        local train = workspace.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel
        local Seat = train.Seat
        local hrpP = LocalPlayer.Character.HumanoidRootPart
        local ragdollingevent = ReplicatedStorage.CharacterEvents.RagdollRemote
        local conn
        conn = RunService.Heartbeat:Connect(function()
            task.wait(0.09)
            Seat:Sit(LocalPlayer.Character.Humanoid)
            ragdollingevent:FireServer(hrpP, 0)
            conn:Disconnect()
            Seat:Sit(LocalPlayer.Character.Humanoid)
        end)
    end
})


local AntiGucci = {
    Players = game:GetService("Players"),
    ReplicatedStorage = game:GetService("ReplicatedStorage"),
    RunService = game:GetService("RunService"),
    LP = game.Players.LocalPlayer,
    enabled = false,
    ragdollConn = nil,
    posCheckConn = nil
}

AntiGucci.spawnGucci = function()
    return AntiGucci.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
        "CreatureBlobman",
        CFrame.new(0, 50000, 0) * CFrame.Angles(-0.7351, 0.9028, 0.6173),
        Vector3.new(0, 59.667, 0)
    )
end

AntiGucci.setup = function()
    AntiGucci.spawnGucci()
    
    local playerName = AntiGucci.LP.Name
    local character = AntiGucci.LP.Character or AntiGucci.LP.CharacterAdded:Wait()
    local folder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    local blobHead = blob and blob:FindFirstChild("Head")
    
    if blobHead then
        blob:SetPrimaryPartCFrame(CFrame.new(0, 99999, 0)) -- send high up
        for _, part in pairs(blob:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Anchored = true
            end
        end
    end
    
    task.wait(0.1)
    
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    local safePosition = rootPart.Position
    local sitting = false
    local seat = blob and blob:FindFirstChild("VehicleSeat")
    
    if seat and seat:IsA("VehicleSeat") then
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        seat:Sit(humanoid)
        sitting = true
    end
    
    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if sitting and humanoid.Jump then
            task.wait(0.02)
            rootPart.CFrame = CFrame.new(safePosition)
            sitting = false
        end
    end)
    
    AntiGucci.ragdollConn = AntiGucci.RunService.Heartbeat:Connect(function()
        if AntiGucci.enabled and sitting then
            pcall(function()
                AntiGucci.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
            end)
        end
    end)
    
    AntiGucci.posCheckConn = AntiGucci.RunService.Heartbeat:Connect(function()
        if AntiGucci.enabled and (rootPart.Position - safePosition).Magnitude < 1 then
            sitting = false
        end
    end)
end

AntisTab:CreateToggle({
    Name = "GUCCI ANTI",
    Default = false,
    Callback = function(state)
        AntiGucci.enabled = state
        if state then
            AntiGucci.setup()
        else
            if AntiGucci.ragdollConn then
                AntiGucci.ragdollConn:Disconnect()
                AntiGucci.ragdollConn = nil
            end
            if AntiGucci.posCheckConn then
                AntiGucci.posCheckConn:Disconnect()
                AntiGucci.posCheckConn = nil
            end
        end
    end
})

AntisTab:CreateToggle({
    Name = 'ANTI GRAB <font face="GothamBlack" color="rgb(160, 255, 0)">[NOT THE BEST]</font>',
    CurrentValue = false,
    Flag = "AntiGrab",
    Callback = function(enabled)
        if antiGrabConn then antiGrabConn:Disconnect() antiGrabConn = nil end
        if not enabled then return end
        antiGrabConn = RunService.Heartbeat:Connect(function()
            local char = LocalPlayer.Character
            if not char then return end
            local head = char:FindFirstChild("Head")
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not head or not hrp or not hum then return end
            local grabbedTag = head:FindFirstChild("PartOwner")
            if not grabbedTag then return end
            local oldCF = hrp.CFrame
            for _=1,3 do
                pcall(function() ReplicatedStorage.CharacterEvents.Struggle:FireServer() end)
                pcall(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end)
                pcall(function() if DestroyGrabLine then DestroyGrabLine:FireServer() end end)
                hum.Sit = false
                hum.PlatformStand = false
                local rag = hum:FindFirstChild("Ragdolled") or (char:FindFirstChild("Humanoid") and char.Humanoid:FindFirstChild("Ragdolled"))
                if rag and rag:IsA("BoolValue") then rag.Value = false end
                hrp.AssemblyLinearVelocity = Vector3.zero
                hrp.AssemblyAngularVelocity = Vector3.zero
                pcall(function() if StopAllVelocity then StopAllVelocity:FireServer() end end)
                hrp.CFrame = oldCF
                pcall(function() if Look then Look:FireServer(hrp.CFrame) end end)
                pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
                task.wait()
            end
            pcall(function() hum:ChangeState(Enum.HumanoidStateType.Running) end)
        end)
    end
})

AntiStates = AntiStates or {
    AntiGucci = false,
    InstaGucci = false,
    AntiBurn = false,
    AntiVoid = false,
    AntiKick = false
}

Services = Services or {}
Services.ReplicatedStorage = game:GetService("ReplicatedStorage")
Services.RunService = game:GetService("RunService")

LocalPlayer = game.Players.LocalPlayer


Remotes = Remotes or {}
Remotes.Ragdoll = Services.ReplicatedStorage
    :WaitForChild("CharacterEvents")
    :WaitForChild("RagdollRemote")


MyToys = MyToys or {}
MyToys.Folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")


function GetCharacter(player)
    if player.Character then
        return player.Character
    end
    if player.CharacterAdded then
        return player.CharacterAdded:Wait()
    end
    return nil
end

function GetHRP(character)
    return character and character:FindFirstChild("HumanoidRootPart") or nil
end

function GetHumanoid(character)
    return character and character:FindFirstChild("Humanoid") or nil
end


function FindPlotItem(itemName)
    local ownedPlot = nil

    for i = 1, 5 do
        local plot = workspace.Plots["Plot" .. i]
        local owners = plot.PlotSign.ThisPlotsOwners

        if #owners:GetChildren() > 0 then
            if owners.Value and owners.Value.Value == LocalPlayer.Name then
                ownedPlot = plot
                break
            end
        end
    end

    if not ownedPlot then
        return nil
    end

    local plotItems = workspace.PlotItems[ownedPlot.Name]
    return plotItems and plotItems:FindFirstChild(itemName) or nil
end


function GetSeatedBlobman()
    local char = GetCharacter(LocalPlayer)
    local humanoid = GetHumanoid(char)

    if humanoid then
        local seat = humanoid.SeatPart
        if seat and seat.Parent.Name == "CreatureBlobman" then
            return seat.Parent
        end
    end

    return nil
end

function SpawnToy(toyName, cf)
    if not LocalPlayer.CanSpawnToy then
        return nil
    end

    task.spawn(function()
        local spawnRemote = Services.ReplicatedStorage
            :WaitForChild("MenuToys")
            :WaitForChild("SpawnToyRemoteFunction")

        spawnRemote:InvokeServer(
            toyName,
            cf * CFrame.new(0, 10, 20),
            Vector3.new(0, 0, 0)
        )

        repeat task.wait() until MyToys.Folder:FindFirstChild(toyName)
        task.wait(0.01)

        return MyToys.Folder:FindFirstChild(toyName)
    end)
end


function SetNetworkOwner(part, cf)
    task.spawn(function()
        Services.ReplicatedStorage
            :WaitForChild("GrabEvents")
            :WaitForChild("SetNetworkOwner")
            :FireServer(part, cf)
    end)
end


function AntiBurnLoop()
    while AntiStates.AntiBurn do
        local success, err = pcall(function()
            local char = GetCharacter(LocalPlayer)
            local hrp = GetHRP(char)

            if hrp and hrp:FindFirstChild("FireParticleEmitter") then
                local extinguisher =
                    MyToys.Folder:FindFirstChild("FireExtinguisher")
                    or SpawnToy("FireExtinguisher", hrp.CFrame * CFrame.new(0, 100, 0))

                if extinguisher then
                    local part = extinguisher.ExtinguishPart

                    while hrp:FindFirstChild("FireParticleEmitter") do
                        part.Position = hrp.Position
                        part.Size = Vector3.new(100, 100, 100)
                        Services.RunService.Heartbeat:Wait()

                        part.Position = Vector3.new(-1000, 0, 0)
                        Services.RunService.Heartbeat:Wait()
                    end

                    Services.ReplicatedStorage
                        :WaitForChild("MenuToys")
                        :WaitForChild("DestroyToy")
                        :FireServer(extinguisher)

                    task.wait(0.05)
                end
            end
        end)

        if not success then
            warn("AntiBurn error:", err)
        end

        task.wait(0.01)
    end
end


function BlobmanGrab(playerName, side)
    task.spawn(function()
        local targetPlayer = game.Players:FindFirstChild(playerName)
        if not targetPlayer then return end

        local myChar = GetCharacter(LocalPlayer)
        local myHRP = GetHRP(myChar)
        local myHum = GetHumanoid(myChar)

        local targetChar = GetCharacter(targetPlayer)
        local targetHRP = GetHRP(targetChar)

        if not (myHRP and myHum and targetHRP) then return end

        local blobman =
            GetSeatedBlobman()
            or MyToys.Folder:FindFirstChild("CreatureBlobman")
            or SpawnToy("CreatureBlobman", targetHRP.CFrame)

        if blobman then
            local detector = blobman:WaitForChild(side .. "Detector")
            local weld = detector:FindFirstChild(side .. "Weld")

            blobman.BlobmanSeatAndOwnerScript.CreatureGrab
                :FireServer(detector, targetHRP, weld)
        end
    end)
end


AntiGUI = AntiGUI or {}

AntiGUI.AntiGucciToggle = AntisTab:CreateToggle({
    Name = 'DESTORY EVERYONES GUCCI <font face="GothamBlack" color="rgb(160, 255, 0)">[SITS ON EVERYONES BLOBMAN. SO IT DESTORYS THEIR GUCCI (MAY NOT WORK.)]</font>',
    CurrentValue = false,
    Flag = "antigucci_toggle",
    Callback = function(Value)
        AntiStates.AntiGucci = Value

        if AntiStates.AntiGucci then
            task.spawn(function()
                while AntiStates.AntiGucci do
                    local char = GetCharacter(LocalPlayer)
                    local hrp = GetHRP(char)
                    if hrp then
                        pcall(function()
                            Remotes.Ragdoll:FireServer(hrp, 0)
                        end)
                    end

                    for _, blobman in ipairs(workspace:GetDescendants()) do
                        if blobman.Name == "CreatureBlobman" and blobman:FindFirstChild("GrabbableHitbox") then
                            BlobmanGrab(LocalPlayer.Name, "Left")
                            SetNetworkOwner(hrp, CFrame.new(blobman.GrabbableHitbox.Position))
                            task.wait(0.1)

                            AntiStates.InstaGucci = false
                        end

                        if not AntiStates.AntiGucci then
                            break
                        end
                    end

                    task.wait(0.05) -- slight wait before looping again
                end
            end)
        end
    end
})


AntiKickToggle = AntisTab:CreateToggle({
    Name = 'ANTI KICK <font face="GothamBlack" color="rgb(160, 255, 0)">[BEST ONE]</font>',
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
                            high.FillColor = Color3.fromRGB(0, 0, 0)
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
                            currentHRP.CFrame * CFrame.new(0, 12, 20),
                            Vector3.new(0,0,0)
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
                    task.wait(0.005)

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
                        wait(0.3)
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
                            wait(0.05)
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

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

antiblob = false
antiblobConnection = nil
truePosPart = nil

function createTruePospart()
    char = LocalPlayer.Character
    if not char then return end
    
    if char:FindFirstChild("TruePositionPart") then
        return char.TruePositionPart
    end
    
    tp = Instance.new("Part")
    tp.Name = "TruePositionPart"
    tp.Anchored = true
    tp.CanCollide = false
    tp.Transparency = 1
    tp.Size = Vector3.new(1, 1, 1)
    tp.CFrame = CFrame.new(0, -100, 0)
    tp.Parent = char
    
    return tp
end

function dropFromBlobs()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    for _, plot in pairs(Workspace.PlotItems:GetChildren()) do
        if plot.Name ~= "PlayersInPlots" then
            for _, itm in pairs(plot:GetChildren()) do
                if itm.Name == "CreatureBlobman" then
                    pcall(function()
                        script = itm:FindFirstChild("BlobmanSeatAndOwnerScript")
                        detector = itm:FindFirstChild("RightDetector")
                        
                        if script and detector then
                            drop = script:FindFirstChild("CreatureDrop")
                            weld = detector:FindFirstChild("RightWeld")
                            
                            if drop and weld then
                                drop:FireServer(weld, hrp)
                                ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                            end
                        end
                    end)
                end
            end
        end
    end
    
    for _, plr in pairs(Players:GetPlayers()) do
        toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
        if toysFolder then
            for _, itm in pairs(toysFolder:GetChildren()) do
                if itm.Name == "CreatureBlobman" then
                    pcall(function()
                        script = itm:FindFirstChild("BlobmanSeatAndOwnerScript")
                        detector = itm:FindFirstChild("RightDetector")
                        
                        if script and detector then
                            drop = script:FindFirstChild("CreatureDrop")
                            weld = detector:FindFirstChild("RightWeld")
                            
                            if drop and weld then
                                drop:FireServer(weld, hrp)
                                ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                            end
                        end
                    end)
                end
            end
        end
    end
end

function setMassless()
    char = LocalPlayer.Character
    if not char then return end
    
    for _, prt in pairs(char:GetChildren()) do
        if prt:IsA("BasePart") and prt.Massless then
            prt.Massless = false
            dropFromBlobs()
        end
    end
end

function moveRootAttachment()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    truePart = char:FindFirstChild("TruePositionPart")
    
    if hrp and truePart then
        rootAttachment = hrp:FindFirstChild("RootAttachment")
        
        if rootAttachment then
            task.wait(0.2)
            rootAttachment.Parent = truePart
            
            Rayfield:Notify({
                Title = "ANTI BLOB",
                Content = "Anti blob has been enabled",
                Duration = 3,
                Image = 4483362458,
            })
        end
    end
end

function restoreRootAttachment()
    char = LocalPlayer.Character
    if not char then return end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    truePart = char:FindFirstChild("TruePositionPart")
    
    if hrp and truePart then
        rootAttachment = truePart:FindFirstChild("RootAttachment")
        
        if rootAttachment then
            rootAttachment.Parent = hrp
            
            Rayfield:Notify({
                Title = "ANTI BLOB",
                Content = "ANTI BLOB HAS BEEN DISABLED",
                Duration = 3,
                Image = 4483362458,
            })
        end
        
        truePart:Destroy()
    end
end

function antiblobLoop()
    while antiblob do
        pcall(function()
            if LocalPlayer.Character then
                createTruePospart()
                setMassless()
                
                hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hrp and hrp:FindFirstChild("RootAttachment") then
                    truePart = LocalPlayer.Character:FindFirstChild("TruePositionPart")
                    if truePart and not truePart:FindFirstChild("RootAttachment") then
                        moveRootAttachment()
                    end
                end
            end
        end)
        
        task.wait(0.1)
    end
end

AntisTab:CreateToggle({
    Name = 'ANTI BLOB <font face="GothamBlack" color="rgb(160, 255, 0)">[ANTI SEAT AND OWNERSHIP]</font>',
    CurrentValue = false,
    Flag = "AntiblobToggle",
    Callback = function(Value)
        antiblob = Value
        
        if Value then
            if antiblobConnection then
                antiblobConnection:Disconnect()
            end
            
            task.spawn(antiblobLoop)
            
            antiblobConnection = LocalPlayer.CharacterAdded:Connect(function(char)
                if antiblob then
                    task.wait(1)
                    createTruePospart()
                    task.wait(0.5)
                    moveRootAttachment()
                end
            end)
        else
            if antiblobConnection then
                antiblobConnection:Disconnect()
                antiblobConnection = nil
            end
            
            restoreRootAttachment()
        end
    end,
})

GrabData = {
    toggle = false,
    lp = game.Players.LocalPlayer,
    ws = workspace,
    dropPos = CFrame.new(-238.98, -256.01, -123.97)
}
 function GrabItem(item)
    local hold = item:FindFirstChild("HoldPart")
    if not hold then return end
    
    local grab = hold:FindFirstChild("HoldItemRemoteFunction")
    local drop = hold:FindFirstChild("DropItemRemoteFunction")
    
    if grab and drop then
        pcall(function()
            grab:InvokeServer(item, GrabData.lp.Character)
        end)
        pcall(function()
            drop:InvokeServer(item, GrabData.dropPos, Vector3.new())
        end)
    end
end
 function GrabFolder(folder)
    for _, obj in ipairs(folder:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("HoldPart") then
            local holdPart = obj.HoldPart
            if holdPart:FindFirstChild("HoldItemRemoteFunction") then
                GrabItem(obj)
            end
        end
    end
end

function LoopGrab()
    while GrabData.toggle do
        for _, folder in ipairs(GrabData.ws:GetChildren()) do
            if folder:IsA("Folder") and folder.Name:find("SpawnedInToys") then
                GrabFolder(folder)
            end
        end
        
        local plotItems = GrabData.ws:FindFirstChild("PlotItems")
        if plotItems then
            for _, plot in pairs(plotItems:GetChildren()) do
                if plot.Name ~= "PlayersInPlots" then
                    for _, model in pairs(plot:GetChildren()) do
                        if model:IsA("Model") and model:FindFirstChild("HoldPart") then
                            local holdPart = model.HoldPart
                            if holdPart:FindFirstChild("HoldItemRemoteFunction") then
                                GrabItem(model)
                            end
                        end
                    end
                end
            end
        end
        
        task.wait()
    end
end

AntisTab:CreateToggle({
    Name = "DELETE ALL HOLDPART TOYS",
    Default = false,
    Callback = function(state)
        GrabData.toggle = state
        if state then
            task.spawn(LoopGrab)
        end
    end
})

vu43 = {}
vu43.anti_lagg_en = false
vu43.auto_anti_lagg_en = false

vu37 = {}
vu27 = game.Players.LocalPlayer
vu37.beam_move = vu27.PlayerScripts.CharacterAndBeamMove

vu40 = {}
vu40.lines_per_01sec = 0
vu40.line_owner = ""

vu28 = {}
vu28.Flags = {}
vu28.Flags.antilagg_toggle = {}
vu28.Flags.antilagg_toggle.Value = false
vu28.Flags.antilagg_toggle.Set = function(value)
    vu28.Flags.antilagg_toggle.Value = value
    vu43.anti_lagg_en = value
    vu37.beam_move.Disabled = value
end

vu26 = {}
vu26.others = game:GetService("Players")

vu79 = function(title, message, duration)
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = title,
        Text = message,
        Duration = duration
    })
end

vu43 = {}
vu43.anti_lagg_en = false
vu43.auto_anti_lagg_en = false

vu37 = {}
vu27 = game.Players.LocalPlayer
vu37.beam_move = vu27.PlayerScripts.CharacterAndBeamMove

vu40 = {}
vu40.lines_per_01sec = 0
vu40.line_owner = ""

vu28 = {}
vu28.Flags = {}
vu28.Flags.antilagg_toggle = {}
vu28.Flags.antilagg_toggle.Value = false
vu28.Flags.antilagg_toggle.Set = function(value)
    vu28.Flags.antilagg_toggle.Value = value
    vu43.anti_lagg_en = value
    vu37.beam_move.Disabled = value
end

vu26 = {}
vu26.others = game:GetService("Players")

vu79 = function(title, message, duration)
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = title,
        Text = message,
        Duration = duration
    })
end

v52 = {}
v52.defense_section = {}

v52.defense_section.CreateToggle = function(config)
    print("Toggle created:", config.Name)
    if config.Callback then
        config.Callback(config.Default)
    end
end

AntisTab:CreateToggle({
    Name = "ANTI LAG",
    Default = false,
    Callback = function(Value)
        local characterScript = LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove")
        if characterScript then
            characterScript.Disabled = Value
        end
    end
})

players = game:GetService("Players")
runService = game:GetService("RunService")
localPlayer = game.Players.LocalPlayer
replicatedStorage = game:GetService("ReplicatedStorage")
workspace = game:GetService("Workspace")

myToysFolder = workspace:WaitForChild(localPlayer.Name .. "SpawnedInToys")

antiBlobmanEnabled = false
antiBlobmanGrabEnabled = false
instaGucci = false
antiBlobmanConnection = nil

function getCharacter(player)
    char = player.Character
    if not char then
        if player.CharacterAdded then
            char = player.CharacterAdded:Wait() or nil
        else
            char = nil
        end
    end
    return char
end

function getHumanoidRootPart(character)
    return character:FindFirstChild("HumanoidRootPart") or nil
end

function getHumanoid(character)
    return character:FindFirstChild("Humanoid") or nil
end

function getDistance(part1, part2)
    return (part1.Position - part2.Position).Magnitude
end

function getAllToys(includePlots)
    allToys = {}
    playersList = players:GetPlayers()
    
    for _, player in ipairs(playersList) do
        toyFolder = workspace[player.Name .. "SpawnedInToys"]
        for _, toy in pairs(toyFolder:GetChildren()) do
            if toy:IsA("Model") then
                table.insert(allToys, toy)
            end
        end
    end
    
    if includePlots then
        for i = 1, 5 do
            for _, item in pairs(workspace.PlotItems["Plot" .. i]:GetChildren()) do
                if item:IsA("Model") then
                    table.insert(allToys, item)
                end
            end
        end
    end
    
    return allToys
end

function spawnToy(toyName, cframe)
    if localPlayer.CanSpawnToy then
        task.spawn(function()
            toySpawn = replicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
            toySpawn:InvokeServer(toyName, cframe * CFrame.new(0, 10, 20), Vector3.new(0, 0, 0))
            repeat
                task.wait()
            until myToysFolder:FindFirstChild(toyName)
            task.wait(0.01)
            return myToysFolder:FindFirstChild(toyName)
        end)
    end
end

function destroyToy(toy)
    task.spawn(function()
        success, err = pcall(function()
            toyDestroy = replicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
            toyDestroy:FireServer(toy)
        end)
        if not success then
            warn("Error: " .. err)
        end
    end)
end

function setNetworkOwner(part, cframe)
    task.spawn(function()
        setNetworkOwnerRemote = replicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        setNetworkOwnerRemote:FireServer(part, cframe)
    end)
end

function toggleConstraint(constraint, enabled)
    if constraint then
        if constraint.Name ~= "LeftWeld" then
            if constraint.Name ~= "LeftAlignOrientation" then
                if constraint.Name ~= "RightWeld" then
                    if constraint.Name == "RightAlignOrientation" and (constraint and constraint.Parent.Parent.Parent ~= myToysFolder) then
                        constraint.Enabled = enabled
                    end
                elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
                    constraint.Enabled = enabled
                end
            elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
                constraint.Enabled = enabled
            end
        elseif constraint and constraint.Parent.Parent.Parent ~= myToysFolder then
            constraint.Enabled = enabled
        end
    end
end

function updateAntiBlobman()
    pcall(function()
        if antiBlobmanConnection then
            antiBlobmanConnection:Disconnect()
        end
    end)
    
    allToys = getAllToys(true)
    
    if antiBlobmanEnabled then
        for _, toy in pairs(allToys) do
            if toy.Name == "CreatureBlobman" then
                leftDetector = toy:FindFirstChild("LeftDetector")
                rightDetector = toy:FindFirstChild("RightDetector")
                leftWeld = leftDetector and leftDetector:FindFirstChild("LeftWeld")
                leftAlign = leftDetector and leftDetector:FindFirstChild("LeftAlignOrientation")
                rightWeld = rightDetector and rightDetector:FindFirstChild("RightWeld")
                rightAlign = rightDetector and rightDetector:FindFirstChild("RightAlignOrientation")
                
                toggleConstraint(leftWeld, false)
                toggleConstraint(leftAlign, false)
                toggleConstraint(rightWeld, false)
                toggleConstraint(rightAlign, false)
            end
        end
        
        antiBlobmanConnection = workspace.DescendantAdded:Connect(function(descendant)
            toggleConstraint(descendant, false)
        end)
    else
        for _, toy in pairs(allToys) do
            if toy.Name == "CreatureBlobman" then
                leftDetector = toy:FindFirstChild("LeftDetector")
                rightDetector = toy:FindFirstChild("RightDetector")
                leftWeld = leftDetector and leftDetector:FindFirstChild("LeftWeld")
                leftAlign = leftDetector and leftDetector:FindFirstChild("LeftAlignOrientation")
                rightWeld = rightDetector and rightDetector:FindFirstChild("RightWeld")
                rightAlign = rightDetector and rightDetector:FindFirstChild("RightAlignOrientation")
                
                toggleConstraint(leftWeld, true)
                toggleConstraint(leftAlign, true)
                toggleConstraint(rightWeld, true)
                toggleConstraint(rightAlign, true)
            end
        end
    end
end

function ragdollFall()
    rootPart = getHumanoidRootPart(getCharacter(localPlayer))
    if rootPart then
        ragdollRemote = replicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")
        ragdollRemote:FireServer(rootPart, 1)
    end
end

function antiBananaDeleteLegs()
    if getCharacter(localPlayer) ~= nil then
        task.spawn(function()
            ragdollFall()
        end)
        task.wait(0.1)
        character = getCharacter(localPlayer)
        rightLeg = character:FindFirstChild("Right Leg")
        leftLeg = character:FindFirstChild("Left Leg")
        clonedLeg = rightLeg:Clone()
        clonedLeg.Parent = character
        clonedLeg.BallSocketConstraint:Destroy()
        task.wait(0.1)
        rightLeg.CFrame = CFrame.new(0, -59999, 0)
        leftLeg.CFrame = CFrame.new(0, -59999, 0)
        task.wait()
        cup = spawnToy("CupMugBrown", getHumanoidRootPart(getCharacter(localPlayer)).CFrame)
        cup = myToysFolder:WaitForChild("CupMugBrown")
        setNetworkOwner(getHumanoidRootPart(character), CFrame.new(cup.Hitbox.Position))
        task.wait()
        destroyToy(cup)
        clonedLeg.CFrame = CFrame.new(0, 9999999, 0)
        clonedLeg.Massless = true
    end
end

AntisTab:CreateToggle({
    Name = "ANTI BLOBMAN AURA GRAB",
    Default = false,
    Color = Color3.fromRGB(102, 0, 102),
    Flag = "antiblobmanaura_toggle",
    Callback = function(enabled)
        antiBlobmanGrabEnabled = enabled
        while antiBlobmanGrabEnabled do
            for _, player in pairs(players:GetPlayers()) do
                if player and getCharacter(player) then
                    playerCharacter = getCharacter(player)
                    playerRootPart = nil
                    if playerCharacter then
                        playerRootPart = getHumanoidRootPart(playerCharacter)
                    end
                    playerHumanoid = nil
                    if playerCharacter then
                        playerHumanoid = getHumanoid(playerCharacter)
                    end
                    myCharacter = getCharacter(localPlayer)
                    myRootPart = nil
                    if myCharacter then
                        myRootPart = getHumanoidRootPart(myCharacter)
                    end
                    if player.Name ~= localPlayer.Name and (playerRootPart and (myRootPart and (playerHumanoid and (playerHumanoid.SeatPart and (playerHumanoid.SeatPart.Parent and (playerHumanoid.SeatPart.Parent.Name == "CreatureBlobman" and getDistance(playerRootPart, myRootPart) <= 19)))))) then
                        setNetworkOwner(playerRootPart, playerRootPart.CFrame)
                        task.wait(0.01)
                    end
                end
            end
            task.wait(0.01)
        end
    end
})

AntisTab:CreateButton({
    Name = 'ANTI BANANA <font face="GothamBlack" color="rgb(160, 255, 0)">[DELETES LEGS]</font>',
    Callback = function()
        antiBananaDeleteLegs()
    end
})

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")

LocalPlayer = Players.LocalPlayer
folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")

currentFood = nil
lastSpawn = 0
spawnCooldown = 0.5
Root = nil
FoodToggle = false
HeartbeatConnection = nil

function gethrp()
    if LocalPlayer.Character then
        hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            Root = hrp
        end
    end
    return Root
end

function spawnfood()
    if tick() - lastSpawn < spawnCooldown then return end
    lastSpawn = tick()
    if not Root then return end

    args = {
        "FoodHamburger",
        Root.CFrame * CFrame.new(5, 0, 5),
        Vector3.new(0, 33.0880012512207, 0)
    }

    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
end

function hold(food)
    if not food or not food.Parent then return end
    holdPart = food:FindFirstChild("HoldPart")
    if not holdPart then return end

    holdArgs = { food, LocalPlayer.Character }
    dropArgs = {
        food,
        CFrame.new(-128.37586975097656, -10.35040283203125, 72.18002319335938),
        Vector3.new(0, 154.77699279785156, 0)
    }

    holdPart.HoldItemRemoteFunction:InvokeServer(unpack(holdArgs))
    holdPart.DropItemRemoteFunction:InvokeServer(unpack(dropArgs))
end

folder.ChildAdded:Connect(function(child)
    if child.Name == "FoodHamburger" then
        currentFood = child
    end
end)

AntisTab:CreateToggle({
    Name = "BURGER LOOP",
    Default = false,
    Callback = function(v)
        FoodToggle = v

        if v then
            HeartbeatConnection = RunService.Heartbeat:Connect(function()
                gethrp()

                if not currentFood or not currentFood.Parent then
                    currentFood = nil
                    spawnfood()
                else
                    hold(currentFood)
                end
            end)
        else
            if HeartbeatConnection then
                HeartbeatConnection:Disconnect()
                HeartbeatConnection = nil
            end
        end
    end
})


vu43 = vu43 or {}
vu43.antigucci_en = false
vu43.insta_gucci = false
vu43.anti_burn_en = false
vu43.anti_void_en = false
vu43.antikick_en = false

vu26 = vu26 or {}
vu26.replicated_storage = game:GetService("ReplicatedStorage")

vu27 = game.Players.LocalPlayer

vu32 = vu32 or {}
vu32.ragdoll = vu26.replicated_storage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

vu37 = vu37 or {}
vu37.my_toys = workspace:WaitForChild(vu27.Name .. "SpawnedInToys")

vu53 = vu53 or {}

vu82 = function(player)
    local char = player.Character
    if not char then
        if player.CharacterAdded then
            char = player.CharacterAdded:Wait() or nil
        else
            char = nil
        end
    end
    return char
end

vu84 = function(character)
    return character:FindFirstChild("HumanoidRootPart") or nil
end

vu86 = function(character)
    return character:FindFirstChild("Humanoid") or nil
end

vu107 = function(itemName)
    local plot = nil
    for i = 1, 5 do
        local plotOwners = workspace.Plots["Plot" .. i].PlotSign.ThisPlotsOwners
        if #plotOwners:GetChildren() ~= 0 then
            if plotOwners.Value then
                if plotOwners.Value.Value == vu27.Name then
                    plot = workspace.Plots["Plot" .. i]
                end
            end
        end
    end
    
    if plot == nil then
        return nil
    end
    
    local plotItems = workspace.PlotItems[plot.Name]
    if plotItems == nil then
        return nil
    else
        return plotItems:FindFirstChild(itemName)
    end
end

vu109 = function()
    if vu86(vu82(vu27)) then
        local seatPart = vu27.Character.Humanoid.SeatPart
        if seatPart and seatPart.Parent.Name == "CreatureBlobman" then
            return seatPart.Parent
        end
    end
end

vu177 = function(toyName, cframe)
    if vu27.CanSpawnToy then
        task.spawn(function()
            local toySpawn = vu26.replicated_storage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
            toySpawn:InvokeServer(toyName, cframe * CFrame.new(0, 10, 20), Vector3.new(0, 0, 0))
            repeat
                task.wait()
            until vu37.my_toys:FindFirstChild(toyName)
            task.wait(0.01)
            return vu37.my_toys:FindFirstChild(toyName)
        end)
    end
end

vu196 = function(part, cframe)
    task.spawn(function()
        local setNetworkOwner = vu26.replicated_storage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        setNetworkOwner:FireServer(part, cframe)
    end)
end

vu294 = function()
    while vu43.anti_burn_en do
        local success, err = pcall(function()
            local character = vu82(vu27)
            if character then
                local hrp = vu84(character)
                local fireExtinguisher = hrp and hrp:FindFirstChild("FireParticleEmitter") and (vu37.my_toys:FindFirstChild("FireExtinguisher") or vu177("FireExtinguisher", hrp.CFrame * CFrame.new(0, 100, 0)))
                if fireExtinguisher then
                    local extinguishPart = fireExtinguisher.ExtinguishPart
                    while hrp:FindFirstChild("FireParticleEmitter") do
                        extinguishPart.Position = hrp.Position
                        extinguishPart.Size = Vector3.new(100, 100, 100)
                        game:GetService("RunService").Heartbeat:Wait()
                        extinguishPart.Position = Vector3.new(-1000, 0, 0)
                        game:GetService("RunService").Heartbeat:Wait()
                    end
                    local toyDestroy = vu26.replicated_storage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                    toyDestroy:FireServer(fireExtinguisher)
                    task.wait(0.05)
                end
            end
        end)
        if not success then
            warn("Error: " .. err)
        end
        task.wait(0.01)
    end
end

vu404 = function(playerName, side)
    task.spawn(function()
        local targetPlayer = game.Players:FindFirstChild(playerName) or playerName
        local myChar = vu82(vu27)
        local myHRP = myChar and vu84(myChar)
        local myHum = myChar and vu86(myChar)
        
        local targetChar = targetPlayer and vu82(targetPlayer)
        local targetHRP = targetChar and vu84(targetChar)
        
        if myHRP and myHum and targetHRP then
            local blobman = vu109() or (vu37.my_toys:FindFirstChild("CreatureBlobman") or vu177("CreatureBlobman", targetHRP.CFrame))
            
            if blobman then
                local blobScript = blobman.BlobmanSeatAndOwnerScript
                local detector = blobman:WaitForChild(side .. "Detector")
                local weld = detector:FindFirstChild(side .. "Weld")
                blobScript.CreatureGrab:FireServer(detector, targetHRP, weld)
            end
        end
    end)
end



 AntiBurnToggle = AntisTab:CreateToggle({
    Name = "ANTI BURN",
    CurrentValue = false,
    Flag = "antiburn_toggle",
    Callback = function(Value)
        vu43.anti_burn_en = Value
        vu294()
    end,
})

 AntiVoidToggle = AntisTab:CreateToggle({
    Name = "ANTI VOID",
    CurrentValue = false,
    Flag = "antivoid_toggle",
    Callback = function(Value)
        vu43.anti_void_en = Value
        if vu43.anti_void_en then
            workspace.FallenPartsDestroyHeight = math.huge
        else
            workspace.FallenPartsDestroyHeight = -100
        end
    end,
})

BreakerTab = Window:CreateTab("MAP BREAKER", 4483362458)

BreakerTab:CreateSection("BREAKER OPTIONS")

w = game:GetService("Workspace")
Players = game:GetService("Players")
rs = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

me = Players.LocalPlayer
BackPack = w[me.Name .. 'SpawnedInToys']

setowner = rs.GrabEvents.SetNetworkOwner
StickyPartEvent = rs.PlayerEvents.StickyPartEvent

BreakerTab:CreateButton({
    Name = 'LARGE FACTORY ROCKS 1 <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
    Callback = function()
        local debris = w.Map.AlwaysHereTweenedObjects.LrgDebris.Object.ObjectModel.Rock
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, debris, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = debris.CFrame
            setowner:FireServer(debris, debris.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.LrgDebris.Object
        local body = obj.ObjectModel.Rock
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
    Name = 'LARGE FACTORY ROCKS 2 <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
    Callback = function()
        local debris = w.Map.AlwaysHereTweenedObjects.LrgDebris2.Object.ObjectModel.Rock
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, debris, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = debris.CFrame
            setowner:FireServer(debris, debris.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.LrgDebris2.Object
        local body = obj.ObjectModel.Rock
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
    Name = 'TRAIN <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
    Callback = function()
        local train = w.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.BallOctagon
        for i = 1,10 do
            local shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart, train, CFrame.Angles(0,0,0))
        end
        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = train.CFrame
            setowner:FireServer(train, train.CFrame)
            task.wait()
        end
        local obj = w.Map.AlwaysHereTweenedObjects.Train.Object
        local body = obj.ObjectModel.BallOctagon
        local attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end
        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

BreakerTab:CreateButton({
   Name = 'OUTER UFO <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel.Body
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,UFO,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = UFO.CFrame
          setowner:FireServer(UFO,UFO.CFrame)
          task.wait()
      end
      local obj = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object
      local body = obj.ObjectModel.Body
      local attach = body:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateButton({
   Name = 'INNER UFO <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object.ObjectModel.Body
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,UFO,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = UFO.CFrame
          setowner:FireServer(UFO,UFO.CFrame)
          task.wait()
      end
      local obj = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object
      local body = obj.ObjectModel.Body
      local attach = body:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateButton({
   Name = 'CAVE CART <font face="GothamBlack" color="rgb(160, 255, 0)">[SPAWN 11/MORE SHURIKENS THEN CLICK THIS.]</font>',
   Callback = function()
      local obj = w.Map.AlwaysHereTweenedObjects.CaveCart.Object
      local model = obj.ObjectModel
      local target = model:GetChildren()[13]
      for i = 1,10 do
          local shur = BackPack.NinjaShuriken
          shur.Name = i
          StickyPartEvent:FireServer(shur.StickyPart,target,CFrame.Angles(0,0,0))
      end
      for i = 1,100 do
          me.Character.HumanoidRootPart.CFrame = target.CFrame
          setowner:FireServer(target,target.CFrame)
          task.wait()
      end
      local attach = target:FindFirstChild("ObjectModelAttachment")
      if attach then attach:Destroy() end
      obj.FollowThisPart.AlignPosition.Attachment0 = nil
      obj.FollowThisPart.AlignOrientation.Attachment0 = nil
   end
})

BreakerTab:CreateDivider()

local Sense, Massless = 30, nil

BreakerTab:CreateToggle({
    Name = 'MASSLESS GRAB <font face="GothamBlack" color="rgb(160, 255, 0)">[USE THIS TO PICK UP THE BROKEN PARTS. USE 30000 MASS TO TAKE THEM INTO PLOTS.]</font>',
    CurrentValue = false,
    Flag = "MasslessToggle",
    Callback = function(v)
        if v then
            Massless = workspace.ChildAdded:Connect(function(r)
                if r.Name == "GrabParts" then
                    while workspace:FindFirstChild("GrabParts") do
                        task.wait()
                        local dp = r:FindFirstChild("DragPart")
                        if dp and dp:FindFirstChild("AlignPosition") and dp:FindFirstChild("AlignOrientation") then
                            dp.AlignPosition.Responsiveness = Sense
                            dp.AlignPosition.MaxForce = math.huge
                            dp.AlignPosition.MaxVelocity = math.huge
                            dp.AlignOrientation.Responsiveness = Sense
                            dp.AlignOrientation.MaxTorque = math.huge
                        end
                    end
                end
            end)
        else
            if Massless then Massless:Disconnect() Massless = nil end
        end
    end
})

BreakerTab:CreateInput({
    Name = "MASSLESS SENSE",
    CurrentValue = tostring(Sense),
    PlaceholderText = "ENTER SENSE VALUE",
    RemoveTextAfterFocusLost = false,
    Callback = function(Text)
        local v = tonumber(Text)
        if v and v > 0 then Sense = v end
    end
})

LoopTab = Window:CreateTab("LOOP", 7734058599)

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
LocalPlayer = Players.LocalPlayer

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

SelectedPlayer = nil
KillHB, KickHB, GrabKickHB = nil, nil, nil
LoopKickOn, LoopKillOn, LoopBlobKickOn, LoopBlobKillOn, LoopGrabKickOn = false, false, false, false, false
spamActive = false

HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

function sno(part)
    if not part or not part.Parent then return end
    pcall(function()
        RemoteSetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then d.CanCollide = false end
    end
end

function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then v.CanCollide = false end
    end
end

function isTooHigh(plr)
    local c = plr.Character
    local hrp = c and c:FindFirstChild("HumanoidRootPart")
    return not hrp or hrp.Position.Y > HEIGHT_LIMIT
end

function findBlobman()
    local toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    return toys and toys:FindFirstChild("CreatureBlobman") or nil
end

function spawnBlobman()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    SpawnToyRF:InvokeServer("CreatureBlobman", char.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end

function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 30 do
        task.wait(0.1)
        b = findBlobman()
        if b then return b end
    end
    return nil
end

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    local cam = Workspace.CurrentCamera
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
local cameraAnchor = CameraAnchor.new()

function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        char:SetAttribute("OriginalPosition", hrp:GetPivot())
    end
end

function getOriginalPosAttr()
    local char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end

function initCharAttrs()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetAttribute("OriginalPosition", char.HumanoidRootPart:GetPivot())
        char:SetAttribute("SavingOriginalPos", false)
    end
end

function scheduleReturnHome()
    local originalPos = getOriginalPosAttr()
    if not originalPos then return end
    
    local conn
    conn = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp:PivotTo(originalPos)
            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end
            char:SetAttribute("SavingOriginalPos", false)
        end
        cameraAnchor:detach()
        conn:Disconnect()
    end)
end

function modifyTarget(root, hum)
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

    local bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
    bv.P = 100
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(1.5, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end

function sendToSky(root, hum)
    DisableCollisions(hum.Parent)
    local BV = Instance.new("BodyVelocity")
    BV.Velocity = Vector3.new(0, 90000, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 100
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true
    task.delay(3, function() if BV.Parent then BV:Destroy() end end)
end

function executeKick()
    if not SelectedPlayer then return end
    local p = Players:FindFirstChild(SelectedPlayer)
    local c = p and p.Character
    local root = c and c:FindFirstChild("HumanoidRootPart")
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChild("Humanoid")
    if not (root and head and hum) or hum.Health <= 0 then return end
    if isTooHigh(p) then return end
    if p:FindFirstChild("InPlot") and p.InPlot.Value then return end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
    if not selfRoot then return end

    local saved = selfChar:GetPivot()
    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.05)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.05)
    local po = head:FindFirstChild("PartOwner")
    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end

function StartLoopKick()
    if KickHB then KickHB:Disconnect() end
    LoopKickOn = true
    KickHB = RunService.Heartbeat:Connect(function()
        if LoopKickOn then executeKick() end
    end)
end

function StopLoopKick()
    LoopKickOn = false
    if KickHB then KickHB:Disconnect() KickHB = nil end
end

function getPlayerList()
    local list = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local displayName = plr.DisplayName or plr.Name
            local entry = string.format("%s (@%s)", displayName, plr.Name)
            table.insert(list, entry)
        end
    end
    return list
end

function extractUsername(entry)
    local username = entry:match("@([%w_]+)")
    return username
end

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
local RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")

local SelectedPlayer = nil
local KillHB = nil
local TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
local HEIGHT_LIMIT = 100000

local CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2,0.2,0.2), 1, true, false, cf, Workspace
    self.part = p
    Workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
    Workspace.CurrentCamera.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    local cam = Workspace.CurrentCamera
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
local cameraAnchor = CameraAnchor.new()

local function getPlayerList()
    local list = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local displayName = plr.DisplayName or plr.Name
            table.insert(list, string.format("%s (@%s)", displayName, plr.Name))
        end
    end
    return list
end

local function extractUsername(entry)
    return entry:match("@([%w_]+)")
end

local function isTooHigh(plr)
    local c = plr.Character
    local hrp = c and c:FindFirstChild("HumanoidRootPart")
    return not hrp or hrp.Position.Y > HEIGHT_LIMIT
end

local function setNoCollideChar(char)
    for _, part in ipairs(char:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end

local function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then char:SetAttribute("OriginalPosition", hrp:GetPivot()) end
end

local function getOriginalPosAttr()
    local char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end

local function scheduleReturnHome()
    local originalPos = getOriginalPosAttr()
    if not originalPos then return end

    local conn
    conn = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then hrp:PivotTo(originalPos) end
        cameraAnchor:detach()
        conn:Disconnect()
    end)
end

local function modifyTarget(root, hum)
    if not (root and hum) or hum.Health <= 0 then return end
    hum:ChangeState(Enum.HumanoidStateType.Dead)
end

local function performKill()
    if not SelectedPlayer then return end

    local target = Players:FindFirstChild(SelectedPlayer)
    local tChar = target and target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    if not (target and tRoot and tHum) then return end
    if isTooHigh(target) then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then saveOriginalPosAttr() end
    char:SetAttribute("SavingOriginalPos", true)

    local originalPos = getOriginalPosAttr()
    if originalPos then cameraAnchor:attach(originalPos) end

    hrp:PivotTo(CFrame.new(tRoot.Position + TELEPORT_OFFSET))
    setNoCollideChar(tChar)
    RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
    task.wait()
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait()
    modifyTarget(tRoot, tHum)
    scheduleReturnHome()
end

local function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    KillHB = RunService.Heartbeat:Connect(performKill)
end

local function StopLoopKill()
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
end

local PlayerDropdown = LoopTab:CreateDropdown({
    Name = "TARGET PLAYER",
    Options = getPlayerList(),
    CurrentOption = {},
    Flag = "PlayerDropdown",
    Font = Enum.Font.GothamSemibold,
    PlaceholderText = "SEARCH BY NAME...",
    Searchable = true,
    Callback = function(option)
        local selected = type(option) == "table" and option[1] or option
        SelectedPlayer = extractUsername(selected)
    end
})

local function refreshPlayerDropdown()
    if PlayerDropdown then
        PlayerDropdown:Refresh(getPlayerList(), true)
    end
end

Players.PlayerAdded:Connect(refreshPlayerDropdown)
Players.PlayerRemoving:Connect(refreshPlayerDropdown)

LoopTab:CreateButton({
    Name = "REFRESH PLAYER LIST",
    Font = Enum.Font.GothamSemibold,
    Callback = refreshPlayerDropdown
})

LoopTab:CreateToggle({
    Name = "LOOP KILL",
    CurrentValue = false,
    Flag = "LoopKillToggle",
    Callback = function(v)
        if v then StartLoopKill() else StopLoopKill() end
    end
})

local TweenService = game:GetService("TweenService")

LoopTab:CreateToggle({
    Name = "BLOB KILL",
    CurrentValue = false,
    Flag = "BlobKillToggle",
    Callback = function(enabled)
        LoopBlobKillOn = enabled

        local function execBlobKill(targetName)
            if not targetName or targetName == "" then return end
            local plr = Players:FindFirstChild(targetName)
            if not plr or not plr.Character then return end
            local localPlayer = Players.LocalPlayer
            local localChar = localPlayer.Character
            if not localChar then return end
            local hum = localChar:FindFirstChild("Humanoid")
            local hrp = localChar:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end
            local blob =
            (hum.SeatPart
                and hum.SeatPart.Parent.Name == "CreatureBlobman"
                and hum.SeatPart.Parent)
            or ensureBlobman()
            
            if not blob or not blob.PrimaryPart then
    return
end

local targetHRP = plr.Character:FindFirstChild("HumanoidRootPart")
if not targetHRP then
    return
end
if targetHRP.Position.Y > HEIGHT_LIMIT then return end

        local startLocalCFrame = hrp.CFrame
        local startBlobCFrame = blob.PrimaryPart.CFrame

        blob:SetPrimaryPartCFrame(targetHRP.CFrame)

        if blob:FindFirstChild("VehicleSeat") then
            blob.VehicleSeat:Sit(hum)
            task.wait(0.2)
        end

        local detector = blob:FindFirstChild("LeftDetector")
        local weld = detector and detector:FindFirstChild("LeftWeld")
        if detector and weld then
            blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
        end

        task.wait(0.1)

        local targetHum = plr.Character:FindFirstChildOfClass("Humanoid")
        if targetHum then
            targetHum.RigType = Enum.HumanoidRigType.R15
        end

        task.wait(0.1)

        if weld then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, targetHRP)
        end

        task.delay(0.1, function()
            hrp.CFrame = startLocalCFrame
            if blob and blob.PrimaryPart then
                blob:SetPrimaryPartCFrame(startBlobCFrame)
            end
        end)
    end

    if enabled then
        task.spawn(function()
            while LoopBlobKillOn do
                if SelectedPlayer and Players:FindFirstChild(SelectedPlayer) then
                    local plr = Players[SelectedPlayer]
                    local hum = plr.Character and plr.Character:FindFirstChildOfClass("Humanoid")
                    if hum and hum.Health > 0 then
                        execBlobKill(SelectedPlayer)
                    end
                end
                task.wait(0.7)
            end
        end)
    else
        LoopBlobKillOn = false
    end
end
})

LoopTab:CreateToggle({
    Name = "BLOBMAN KICK",
    CurrentValue = false,
    Flag = "BlobKickToggle",
    Callback = function(enabled)
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman")
                and hum.SeatPart.Parent
                or nil
        end

        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not (hrp and blob) then return end

            -- Force ownership + clear any old grab
            RemoteSetNetworkOwner:FireServer(hrp, hrp.CFrame)
            RemoteDestroyGrabLine:FireServer(hrp)

            -- Grab with RIGHT arm
            if blob:FindFirstChild("RightDetector") then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(
                    Players.LocalPlayer,
                    hrp,
                    blob.RightDetector.RightWeld
                )
            end

            -- ALSO grab with LEFT arm (much more reliable)
            if blob:FindFirstChild("LeftDetector") then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(
                    Players.LocalPlayer,
                    hrp,
                    blob.LeftDetector.LeftWeld
                )
            end
        end

        local function execBlobKick(targetName)
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end

            local blob = findMountedBlob() or ensureBlobman()
            if not blob then return end
            task.wait(0)

            local seat = blob:FindFirstChild("VehicleSeat")
            if seat then seat:Sit(hum) task.wait(0.1) end

            local tp = Players:FindFirstChild(targetName)
            local tHRP = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then return end

            local startSelf, startBlob = hrp.CFrame, blob.PrimaryPart and blob.PrimaryPart.CFrame
            local oldCF = tHRP.CFrame
            hrp.CFrame = oldCF
            task.wait(0)

            for _ = 1, 15 do
                task.wait()
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)
            end
            task.wait(0)
            RemoteDestroyGrabLine:FireServer(tHRP)
            task.wait(0)

            -- Grab spam (unchanged count, stronger grab)
            for i = 1, 3 do
                bringRightArm(targetName, blob)
            end

            task.delay(0.1, function()
                if hrp then hrp.CFrame = startSelf end
                if blob and blob.PrimaryPart and startBlob then
                    blob:SetPrimaryPartCFrame(startBlob)
                end
            end)
        end

        local function loopBlobKick(targetName)
            spawn(function()
                while LoopBlobKickOn do
                    if targetName and targetName ~= "" then
                        execBlobKick(targetName)
                    end
                    task.wait(0.1)
                end
            end)
        end

        if enabled then
            if SelectedPlayer and SelectedPlayer ~= "" then
                loopBlobKick(SelectedPlayer)
            end
        else
            LoopBlobKickOn = false
        end
    end
})

LoopTab:CreateToggle({
    Name = "BLOBMAN KICK",
    CurrentValue = false,
    Flag = "BlobKickToggle",
    Callback = function(enabled)
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman")
                and hum.SeatPart.Parent
                or nil
        end

        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if hrp and blob and blob:FindFirstChild("RightDetector") then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(
                    Players.LocalPlayer,
                    hrp,
                    blob.RightDetector.RightWeld
                )
            end
        end

        local function execBlobKick(targetName)
            if not LoopBlobKickOn then return end

            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end

            local blob = findMountedBlob() or ensureBlobman()
            if not blob then return end

            local seat = blob:FindFirstChild("VehicleSeat")
            if seat and hum.SeatPart ~= seat then
                seat:Sit(hum)
            end

            local tp = Players:FindFirstChild(targetName)
            local tChar = tp and tp.Character
            local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
            if not tHRP then return end

            local oldCF = tHRP.CFrame

            for i = 1, 40 do
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 60, 0)
                RemoteDestroyGrabLine:FireServer(tHRP)
            end

            for i = 1, 6 do
                bringRightArm(targetName, blob)
            end
        end

        local respawnConn

        local function monitorRespawnBlobKick(targetName)
            local tp = Players:FindFirstChild(targetName)
            if not tp then return end

            if respawnConn then respawnConn:Disconnect() end
            respawnConn = tp.CharacterAdded:Connect(function()
                if LoopBlobKickOn then
                    task.wait(0.2)
                    execBlobKick(targetName)
                end
            end)
        end

        if enabled then
            if SelectedPlayer and SelectedPlayer ~= "" then
                execBlobKick(SelectedPlayer)
                monitorRespawnBlobKick(SelectedPlayer)
            end
        else
            LoopBlobKickOn = false
            if respawnConn then
                respawnConn:Disconnect()
                respawnConn = nil
            end
        end
    end
})

LoopTab:CreateToggle({
    Name = "BLOBMAN (BREAK HOUSE BARRIER FIRST.)",
    CurrentValue = false,
    Flag = "BlobKickToggle",
    Callback = function(enabled)
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman")
                and hum.SeatPart.Parent
                or nil
        end

        -- 🔧 FIXED + STRONGER GRAB (ONLY CHANGE)
        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not (hrp and blob) then return end

            -- Force ownership + clear any old grab
            RemoteSetNetworkOwner:FireServer(hrp, hrp.CFrame)
            RemoteDestroyGrabLine:FireServer(hrp)

            -- Grab with RIGHT arm
            if blob:FindFirstChild("RightDetector") then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(
                    Players.LocalPlayer,
                    hrp,
                    blob.RightDetector.RightWeld
                )
            end

            -- ALSO grab with LEFT arm (much more reliable)
            if blob:FindFirstChild("LeftDetector") then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(
                    Players.LocalPlayer,
                    hrp,
                    blob.LeftDetector.LeftWeld
                )
            end
        end

        local function execBlobKick(targetName)
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then return end

            local blob = findMountedBlob() or ensureBlobman()
            if not blob then return end
            task.wait(0)

            local seat = blob:FindFirstChild("VehicleSeat")
            if seat then seat:Sit(hum) task.wait(0.1) end

            local tp = Players:FindFirstChild(targetName)
            local tHRP = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then return end

            local startSelf, startBlob = hrp.CFrame, blob.PrimaryPart and blob.PrimaryPart.CFrame
            local oldCF = tHRP.CFrame
            hrp.CFrame = oldCF
            task.wait(0)

            for _ = 1, 15 do
                task.wait()
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)
            end
            task.wait(0)
            RemoteDestroyGrabLine:FireServer(tHRP)
            task.wait(0)

            -- Grab spam (unchanged count, stronger grab)
            for i = 1, 3 do
                bringRightArm(targetName, blob)
            end

            task.delay(0.1, function()
                if hrp then hrp.CFrame = startSelf end
                if blob and blob.PrimaryPart and startBlob then
                    blob:SetPrimaryPartCFrame(startBlob)
                end
            end)
        end

        local function loopBlobKick(targetName)
            spawn(function()
                while LoopBlobKickOn do
                    if targetName and targetName ~= "" then
                        execBlobKick(targetName)
                    end
                    task.wait(0.1)
                end
            end)
        end

        if enabled then
            if SelectedPlayer and SelectedPlayer ~= "" then
                loopBlobKick(SelectedPlayer)
            end
        else
            LoopBlobKickOn = false
        end
    end
})





LoopTab:CreateToggle({
    Name = 'LOOP KICK <font face="GothamBlack" color="rgb(160, 255, 0)">[GRABS THE USER AND LOOPKICKS THEM]</font>',
    CurrentValue = false,
    Flag = "LoopKickToggle",
    Callback = function(v)
        if v then
            StartLoopKick()
        else
            StopLoopKick()
        end
    end
})

LoopTab:CreateToggle({
Name = 'SOFTLOCK USER<font face="GothamBlack" color="rgb(160, 255, 0)">[SOFTLOCKS THE TARGET IN THE AIR.]</font>',
CurrentValue = false,
Flag = "SpamKickToggle",
Callback = function(Value)
spamActive = Value if Value then
        if not SelectedPlayer then
            spamActive = false
            return
        end
        
        local blob = findBlobman()
        if not blob then
            spamActive = false
            return
        end
        
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then
            spamActive = false
            return
        end
        
        local RightDetector = blob:FindFirstChild("RightDetector")
        if not RightDetector then
            spamActive = false
            return
        end
        
        local RightWeld = RightDetector:FindFirstChild("RightWeld")
        local BlobmanScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        
        if not (RightWeld and BlobmanScript) then
            spamActive = false
            return
        end
        
        local CreatureGrab = BlobmanScript:FindFirstChild("CreatureGrab")
        local CreatureRelease = BlobmanScript:FindFirstChild("CreatureRelease")
        
        if not (CreatureGrab and CreatureRelease) then
            spamActive = false
            return
        end
        
        task.spawn(function()
            local oldCF = char:GetPivot()
            local targetPlayer = Players:FindFirstChild(SelectedPlayer)
            local targetChar = targetPlayer and targetPlayer.Character
            
            if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
                spamActive = false
                return
            end
            
            local targetRoot = targetChar.HumanoidRootPart
            
            pcall(function()
                char:PivotTo(targetRoot.CFrame)
                task.wait(0.1)
                CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                task.wait(0.1)
                CreatureRelease:FireServer(RightWeld, targetRoot)
                
                task.defer(function()
                    local BodyPos = Instance.new("BodyPosition")
                    BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    BodyPos.Position = oldCF.Position + Vector3.new(math.random(-15, 15), math.random(-10, 10), math.random(-15, 15))
                    BodyPos.Parent = targetRoot
                    BodyPos.P = 45000
                    BodyPos.D = 500
                end)
            end)
            
            task.wait(0.1)
            pcall(function()
                char:PivotTo(oldCF)
            end)
            task.wait(0.1)
            
            while spamActive do
                targetPlayer = Players:FindFirstChild(SelectedPlayer)
                if not targetPlayer or not Players:FindFirstChild(SelectedPlayer) then
                    spamActive = false
                    break
                end
                
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    task.wait(0)
                    continue
                end
                
                targetRoot = targetPlayer.Character.HumanoidRootPart
                local targetHead = targetPlayer.Character:FindFirstChild("Head")
                
                pcall(function()
                    sno(targetRoot)
                    if targetHead then
                        sno(targetHead)
                    end
                    RemoteDestroyGrabLine:FireServer(targetRoot)
                    
                    CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                    
                    if targetHead then
                        GrabEvents.CreateGrabLine:FireServer(targetHead, targetHead.CFrame)
                    end
                    GrabEvents.CreateGrabLine:FireServer(targetRoot, targetRoot.CFrame)
                    
                    CreatureRelease:FireServer(RightWeld, targetRoot)
                end)
                
                task.wait(0.5)
            end
        end)
    end
end})

Players.PlayerRemoving:Connect(function()
task.wait(0.2)
PlayerDropdown:Refresh(getPlayerList(), true)
end)
LocalPlayer.CharacterAdded:Connect(function(char)
initCharAttrs()
local hum = char:WaitForChild("Humanoid", 5)
if hum then
hum.Died:Connect(function()
cameraAnchor:detach()
end)
end
end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer

local AuraSettings = AuraSettings or { WhitelistFriends = false }
local AuraState = AuraState or { GrabbedPlayers = {} }

local GameEvents = ReplicatedStorage:WaitForChild("GrabEvents", 5)

local BringState = {
    Active = false,
    Queue = {},
    SavedPos = nil,
    SavedCamCFrame = nil,
    Connection = nil,
    Radius = 1000
}

local camBlocker = Instance.new("Part")
camBlocker.Size = Vector3.new(1,1,1)
camBlocker.Anchored = true
camBlocker.Transparency = 1
camBlocker.CanCollide = false

local function freezeCamera()
    local cam = Workspace.CurrentCamera
    if not BringState.SavedCamCFrame then return end
    cam.CameraType = Enum.CameraType.Scriptable
    cam.CFrame = BringState.SavedCamCFrame
    camBlocker.CFrame = BringState.SavedCamCFrame
    camBlocker.Parent = Workspace
end

local function unfreezeCamera()
    camBlocker.Parent = nil
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
end

local function disableCollisions(char)
    for _, p in ipairs(char:GetDescendants()) do
        if p:IsA("BasePart") then
            p.CanCollide = false
        end
    end
end

local function shouldIgnore(player)
    if player == LocalPlayer then return true end
    if AuraSettings.WhitelistFriends and LocalPlayer:IsFriendsWith(player.UserId) then
        return true
    end
    return false
end

local function refreshQueueAll()
    BringState.Queue = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        if not shouldIgnore(plr) and plr.Character then
            table.insert(BringState.Queue, plr)
        end
    end
end

local function refreshQueueSingle(target)
    BringState.Queue = {}
    if target and target.Character and not shouldIgnore(target) then
        table.insert(BringState.Queue, target)
    end
end

local function processNext()
    if not BringState.Active then return end
    if #BringState.Queue == 0 then return end

    local target = table.remove(BringState.Queue, 1)
    if not target.Character then return end

    local root = target.Character:FindFirstChild("HumanoidRootPart")
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not (root and localRoot and GameEvents) then return end

    LocalPlayer.Character:PivotTo(root.CFrame * CFrame.new(0, -6, 0))
    disableCollisions(LocalPlayer.Character)

    for _ = 1, 20 do
        if not BringState.Active then break end
        GameEvents.SetNetworkOwner:FireServer(root, localRoot.CFrame)
        task.wait()
    end

    if BringState.SavedPos then
        root.CFrame = CFrame.new(BringState.SavedPos)
        root.AssemblyLinearVelocity = Vector3.zero
    end
end

local function startBring(queueFunc)
    if BringState.Connection then return end
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end

    BringState.Active = true
    BringState.SavedPos = localRoot.Position
    BringState.SavedCamCFrame = Workspace.CurrentCamera.CFrame

    queueFunc()
    freezeCamera()

    BringState.Connection = RunService.Heartbeat:Connect(processNext)
end

local function stopBring()
    BringState.Active = false
    if BringState.Connection then
        BringState.Connection:Disconnect()
        BringState.Connection = nil
    end
    BringState.Queue = {}
    unfreezeCamera()
end

SilentTab = Window:CreateTab("SILENT AIM", 6034287594)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")

local Hitboxes = {"Head", "Torso", "Left Leg", "Right Leg"}
local Toggle = false
local MaxDistance = 30
local TargetPlayers = {}

local function isVisible(part)
    local origin = Camera.CFrame.Position
    local direction = part.Position - origin
    local ray = Ray.new(origin, direction)
    local hit = workspace:FindPartOnRayWithIgnoreList(ray, {LocalPlayer.Character}, false, true)
    return not hit or hit:IsDescendantOf(part.Parent)
end

local function SilentAimFunction(arg1, arg2, OriginalFunction)
    if not Toggle then return OriginalFunction(arg1, arg2) end

    local closest, closestPart, closestDistance = nil, nil, math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChildOfClass("Humanoid") then
            if #TargetPlayers > 0 and not table.find(TargetPlayers, player.Name) then
                continue
            end

            local root = player.Character.HumanoidRootPart
            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid.Health <= 0 then continue end

            local directionToPlayer = (root.Position - Camera.CFrame.Position).Unit
            local cameraLook = Camera.CFrame.LookVector
            local dot = cameraLook:Dot(directionToPlayer)

            if dot < 0.7 then continue end
            if not isVisible(root) then continue end

            local distance = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
            if distance < MaxDistance and distance < closestDistance then
                closest = player
                closestDistance = distance
            end
        end
    end

    if closest and closest.Character then
        local partName = Hitboxes[math.random(1, #Hitboxes)]
        local hitPart = closest.Character:FindFirstChild(partName)
        if hitPart then
            local origin = Camera.CFrame.Position
            local direction = (hitPart.Position - origin).Unit * arg2
            local result = {
                Instance = hitPart,
                Position = hitPart.Position,
                Normal = Vector3.new(0,1,0),
                Material = Enum.Material.Plastic
            }
            return result, hitPart.CFrame:ToObjectSpace(CFrame.new(result.Position))
        end
    end

    return OriginalFunction(arg1, arg2)
end

local PlayerScripts = LocalPlayer:WaitForChild("PlayerScripts", 5)
local raycastModule = PlayerScripts:FindFirstChild("RaycastModule")
if raycastModule then
    local module = require(raycastModule)
    if module.RaycastFromCam then
        local OriginalFunction = module.RaycastFromCam
        module.RaycastFromCam = function(arg1, arg2)
            return SilentAimFunction(arg1, arg2, OriginalFunction)
        end
    end
end

SilentTab:CreateToggle({
    Name = "SILENT AIM",
    Default = false,
    Callback = function(Value)
        Toggle = Value
    end
})

SilentTab:CreateSlider({
   Name = "SILENT AIM DISTANCE",
   Range = {10, 50},
   Increment = 1,
   Suffix = "STUDS",
   CurrentValue = 30,
   Callback = function(Value)
        MaxDistance = Value
   end
})

local function getPlayerList()
    local list = {}
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    return list
end

SilentTab:CreateDropdown({
    Name = "TARGET PLAYER",
    Options = getPlayerList(), -- now it's a table, not a function
    MultiSelect = true,
    Default = {},
    Callback = function(selected)
        TargetPlayers = selected
    end
})

TpTab = Window:CreateTab("TELEPORTS", 6034287594)

local tpland = {
    Spawn = CFrame.new(0, -7.35, 0),
    SpawnCave = CFrame.new(-90, 14.6, -314.3),
    GreenHouse = CFrame.new(-538, -7, 74),
    PinkHouse = CFrame.new(-478, -7, -147),
    Barn = CFrame.new(-228, 82, -318),
    BlueHouse = CFrame.new(496, 83, -350),
    ChineseHouse = CFrame.new(542, 123, -93),
    PurpleHouse = CFrame.new(270, -7, 448),
    Factory = CFrame.new(134, 347, 352),
    OtherGreenHouse = CFrame.new(-359, 98, 357),
    BigCave = CFrame.new(-245, 80, 485),
    GoodPrison = CFrame.new(569.6, -7, 176.3),
    RuhubsDogAhhPrison = CFrame.new(564, 82.5, 210),
    ExtremelyGoodPrison = CFrame.new(525, 76, 56),
    TrainCave = CFrame.new(536.6, 87.5, -169.5),
    IslandCave = CFrame.new(75.8, 323, 368.5),
    ChineseRoof = CFrame.new(592, 153, -100),
    UfoCave = CFrame.new(29.6, 10.5, -225.8),
    Prison = CFrame.new(195, -7, -561),
    BlueHouseSlot = CFrame.new(562.2, 85.38, -212.56),
    SpawnSlot = CFrame.new(51.75, -5.3, -121.64),
    HauntedSlot = CFrame.new(164.57, -5.43, 530.97),
    RandomSlot = CFrame.new(-211.65, 85.7, 426.72),
    BeachSlot = CFrame.new(-546.97, -5.3, -41.09)
}

dick = Instance.new("Folder")
dick.Parent = game.Workspace
dick.Name = "PlotItems"

TpTab:CreateButton({
    Name = "GO TO SPAWN",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Spawn"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SPAWN",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO SPAWN CAVE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["SpawnCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SPAWN CAVE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO GREEN HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["GreenHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO GREEN HOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO PINK HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["PinkHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO PINKHOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO BARN",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Barn"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO BARN",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO BLUE HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BlueHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO BLUE HOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO CHINESE HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ChineseHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO CHINESE HOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO PURPLE HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["PurpleHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO PURPLE HOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO FACTORY",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Factory"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO FACTORY",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO MOUNTAIN GREEN HOUSE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["OtherGreenHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO MOUNTAIN GREEN HOUSE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO BIG CAVE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BigCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO BIG CAVE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO GOOD PRISON",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["GoodPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO GOOD PRISON",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO SHITTY PRISON",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["RuhubsDogAhhPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SHITTY PRISON",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO EXTEMELY GOOD PRISON",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ExtremelyGoodPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO EXTREMELY GOOD PRISON",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO TRAIN CAVE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["TrainCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO TRAIN CAVE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO ISLAND CAVE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["IslandCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO ISLAND CAVE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO CHINESE ROOF",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ChineseRoof"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO CHINESE ROOF",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO UFO CAVE",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["UfoCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO UFO CAVE",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO PRISON",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Prison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to Prison",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO BLUE HOUSE SLOT",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BlueHouseSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO BLUE HOUSE SLOT",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO SPAWN SLOT",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["SpawnSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SPAWN SLOT",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO HAUNTED SLOT",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["HauntedSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SPAWN SLOT",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO RANDOM SLOT",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["RandomSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO SPAWN SLOT",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateButton({
    Name = "GO TO BEACH SLOT",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BeachSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " WAS SENT TO BEACH SLOT",
                Duration = 0.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 0.5,
                Image = 4483362458,
            })
        end
    end,
})

TpTab:CreateDivider()

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local destroyBarrierActive = false
local destroyBarrierThread = nil
local plotsBroken = false



Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")

local StatusLabel = TpTab:CreateLabel("Broken: False")

destroyBarrierActive = false
destroyBarrierThread = nil
plotsBroken = false

TpTab:CreateToggle({
    Name = "DESTORY BARRIER (USING SNOWBALL)",
    CurrentValue = false,
    Flag = "DestroyBarrierHouse",
    Callback = function(Value)
        if Value and plotsBroken then
            Rayfield:Notify({
                Title = "ALREADY BROKEN",
                Content = "PLOTS ARE ALREADY BROKEN",
                Duration = 5,
                Image = 4483345998,
                Actions = {
                    Ignore = {
                        Name = "OKAY!",
                        Callback = function()
                            print("The user tapped Okay!")
                        end
                    },
                },
            })
            return
        end

        destroyBarrierActive = Value

        if destroyBarrierThread then
            task.cancel(destroyBarrierThread)
            destroyBarrierThread = nil
        end

        if Value then
            local spawnedToysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
            if spawnedToysFolder then
                for _, item in ipairs(spawnedToysFolder:GetChildren()) do
                    if item.Name == "BallSnowball" then
                        ReplicatedStorage.MenuToys.DestroyToy:FireServer(item)
                    end
                end
            end

            if not plotsBroken then
                StatusLabel:Set("Broken: False")
            end

            destroyBarrierThread = task.spawn(function()
                local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
                local SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")

                local targetCFrame = CFrame.new(264.5792541503906, -5.477070331573486, 433.4557800292969)

                local function getSnowballs()
                    local snowballs = {}
                    local spawnedToysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                    if spawnedToysFolder then
                        for _, toy in ipairs(spawnedToysFolder:GetChildren()) do
                            if toy.Name == "BallSnowball" then
                                table.insert(snowballs, toy)
                            end
                        end
                    end
                    return snowballs
                end

                local function spawnSnowball()
                    if not destroyBarrierActive then return end
                    local playerPosition = LocalPlayer.Character and LocalPlayer.Character.PrimaryPart.Position or Vector3.new(0, 0, 0)
                    local initialCFrame = CFrame.new(playerPosition) * CFrame.Angles(-0.807, -0.884, -0.679)
                    task.spawn(function()
                        MenuToys.SpawnToyRemoteFunction:InvokeServer("BallSnowball", initialCFrame, Vector3.new(0, -120.21099853515625, 0))
                    end)
                end

                local function processSnowball(snowball)
                    if not destroyBarrierActive then return end
                    local soundPart = snowball:FindFirstChild("SoundPart")
                    if soundPart then
                        task.spawn(function()
                            SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                        end)
                    end
                end

                local function teleportSnowballs(snowballs)
                    if not destroyBarrierActive then return end
                    for _, snowball in ipairs(snowballs) do
                        task.spawn(function()
                            for _, part in ipairs(snowball:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = targetCFrame
                                end
                            end
                        end)
                    end
                end

                local function testWithOven()
                    if not destroyBarrierActive then return false end

                    local targetPosition = CFrame.new(242.66055297851562, -9.196549415588379, 444.3758850097656)
                    MenuToys.SpawnToyRemoteFunction:InvokeServer("OvenDarkGray", targetPosition, Vector3.new(0, -74.0790023803711, 0))

                    task.wait(0.5)
                    local spawnedToysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                    local oven

                    if spawnedToysFolder then
                        for _, child in ipairs(spawnedToysFolder:GetChildren()) do
                            if child.Name == "OvenDarkGray" and child:GetAttribute("AtSpawned") == nil then
                                oven = child
                                break
                            end
                        end
                    end

                    local stillExists = oven ~= nil

                    if stillExists then
                        StatusLabel:Set("Broken: True")
                        plotsBroken = true
                        Rayfield:Notify({
                            Title = "Success!",
                            Content = "The plots have been broken successfully!",
                            Duration = 5,
                            Image = 4483345998,
                            Actions = {
                                Ignore = {
                                    Name = "Awesome!",
                                    Callback = function()
                                        print("The user tapped Awesome!")
                                    end
                                },
                            },
                        })
                    end

                    return stillExists
                end

                while destroyBarrierActive do
                    local snowballs = getSnowballs()

                    if #snowballs < 2 then
                        if #snowballs < 1 then
                            spawnSnowball()
                            task.wait(0.02)
                        end
                        spawnSnowball()
                        task.wait(0.02)
                        snowballs = getSnowballs()
                    end

                    if #snowballs == 2 then
                        for _, snowball in ipairs(snowballs) do
                            processSnowball(snowball)
                        end

                        teleportSnowballs(snowballs)

                        local ovenResult = testWithOven()
                        if ovenResult then
                            break
                        else
                            task.wait(0.05)
                        end
                    end

                    task.wait(0.05)
                end
            end)
        else
            local spawnedToysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
            if spawnedToysFolder then
                for _, item in ipairs(spawnedToysFolder:GetChildren()) do
                    if item.Name == "BallSnowball" or item.Name == "OvenDarkGray" then
                        ReplicatedStorage.MenuToys.DestroyToy:FireServer(item)
                    end
                end
            end
            if not plotsBroken then
                StatusLabel:Set("Broken: False")
            end
        end
    end,
})

TpTab:CreateDivider()

LocalPlayer = Players.LocalPlayer
 autoBlobmanEnabled = false
targetCFrame = CFrame.new(466.741, 28, -745.949, 0.906275, -0.000000, -0.422688, 0.000000, 1.000000, -0.000000, 0.422688, 0.000000, 0.906275)
 originalPosition = nil
 blobmanSequenceActive = false
 processingBlobmans = {}

 function saveCurrentPosition()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        originalPosition = LocalPlayer.Character.HumanoidRootPart.CFrame
        return true
    end
    return false
end

 function teleportToPosition(cframe)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = cframe
        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        LocalPlayer.Character.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
        return true
    end
    return false
end

 function debugPrint(...)
    print("[BLOBMAN DEBUG]", ...)
end

 function sitOnClosestBlobman()
    local closest, dist = nil, math.huge
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj.Name == "CreatureBlobman" and obj:IsA("Model") then
            local seat = obj:FindFirstChild("VehicleSeat")
            if seat then
                local d = (root.Position - seat.Position).Magnitude
                if d < dist then 
                    dist = d
                    closest = obj 
                end
            end
        end
    end
    
    if closest then
        local seat = closest:FindFirstChild("VehicleSeat")
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if seat and hum and not seat.Occupant then
            debugPrint("Tentative d'assise sur:", closest.Name)
            seat:Sit(hum)
            task.wait(0.3)
            return seat.Occupant == hum
        end
    end
    return false
end

 function sitOnBlobman(blobman)
    local seat = blobman:FindFirstChild("VehicleSeat")
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
    
    if seat and humanoid and not seat.Occupant then
        debugPrint("Tentative d'assise sur Blobman spécifique")
        seat:Sit(humanoid)
        task.wait(0.3)
        return seat.Occupant == humanoid
    end
    return false
end

 function getOffBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart then
            humanoid.Sit = false
            humanoid.Jump = true
            return true
        end
    end
    return false
end

 function executeBlobmanSequence(blobman)
    if processingBlobmans[blobman] then return end
    processingBlobmans[blobman] = true
    
    task.spawn(function()
        if not saveCurrentPosition() then
            processingBlobmans[blobman] = nil
            return
        end
        
        debugPrint("Séquence ULTRA rapide démarrée")
        
        local blobmanPosition = nil
        if blobman:FindFirstChild("VehicleSeat") then
            blobmanPosition = blobman.VehicleSeat.CFrame
        elseif blobman:FindFirstChild("HumanoidRootPart") then
            blobmanPosition = blobman.HumanoidRootPart.CFrame
        elseif blobman.PrimaryPart then
            blobmanPosition = blobman:GetPrimaryPartCFrame()
        end
        
        if blobmanPosition then
            teleportToPosition(blobmanPosition + Vector3.new(0, 2, 0))
            task.wait(0)
        end
        
        if sitOnBlobman(blobman) then
            debugPrint("Assis sur le Blobman")
            task.wait(0)
        end
        
        teleportToPosition(targetCFrame)
        debugPrint("TP instantané à la cible")
        task.wait(0)
        
        getOffBlobman()
        debugPrint("Descendu instantanément")
        task.wait(0)
        
        if originalPosition then
            teleportToPosition(originalPosition)
            debugPrint("Retour instantané")
        end
        
        processingBlobmans[blobman] = nil
        debugPrint("Séquence ULTRA rapide terminée")
    end)
end

Workspace.DescendantAdded:Connect(function(descendant)
    if not autoBlobmanEnabled then return end
    
    if descendant.Name == "CreatureBlobman" and descendant:IsA("Model") then
        task.wait(0.1)
        executeBlobmanSequence(descendant)
    end
end)


TpTab:CreateToggle({
    Name = "DELETE BLOBMANS (IF SOMEONE SPAWNS A BLOBMAN, SIT ON IT AND TAKE IT TO THE WATER)",
    CurrentValue = false,
    Flag = "AutoBlobman",
    Callback = function(Value)
        autoBlobmanEnabled = Value
        if Value then
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "Système activé - En attente de Blobmans",
                Duration = 3
            })
        else
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "Système désactivé",
                Duration = 2
            })
            processingBlobmans = {}
        end
    end
})

FunTab = Window:CreateTab("NPC CONTROL", 4483362458)
ButtonSection = FunTab:CreateSection("ACTIONS")

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
ReplicatedStorage = game:GetService("ReplicatedStorage")
PFS = game:GetService("PathfindingService")
LocalPlayer = Players.LocalPlayer

function findRandomSpawnedInToysFolder()
    for _, folder in ipairs(Workspace:GetChildren()) do
        if folder.Name:match("SpawnedInToys") and folder.Name ~= LocalPlayer.Name.."SpawnedInToys" then
            for _, child in ipairs(folder:GetChildren()) do
                if child:IsA("Model") and child.Name == "YouDecoy" then
                    return folder
                end
            end
        end
    end
    return nil
end

decoyFolder = findRandomSpawnedInToysFolder() or Workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys")
YourFolder = Workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys")

targetPlayer = nil
following = false
currentSpeed = 30
spinningDecoys = {}
loopJumpingDecoys = {}
wandering = false
orbiting = false
orbitTarget = nil
orbitAngleMap = {}
orbitRadius = 10
orbitSpeed = 90
flashStepping = false
flashStepTarget = nil
flashStepTime = 0
flashStepSpeed = 0.02
attacking = false
attackTarget = nil
wsesGrabEnabled = false

WsesCmds = {}
WsesCmds.fly = false
WsesCmds.flyspeed = 5
WsesCmds.spin = false
WsesCmds.spinspeed = 60
WsesCmds.speed = 16
WsesCmds.jumppower = 24
WsesCmds.target = nil
WsesCmds.oldfollow = false
WsesCmds.follow = false
WsesCmds.eyes = false
WsesCmds.eyes2 = false
WsesCmds.watersafe = false
WsesCmds.pathfollow = false
WsesCmds.pathfollowSpeed = 30

followCleanups = {}

Grab = ReplicatedStorage.GrabEvents.SetNetworkOwner
UnGrab = ReplicatedStorage.GrabEvents.DestroyGrabLine



function isValidYouDecoy(m)
   return m:IsA("Model") and m.Name == "YouDecoy" and m:FindFirstChildOfClass("Humanoid") and m:FindFirstChild("HumanoidRootPart")
end

function getTargetPlayer(name)
   return Players:FindFirstChild(name)
end

function followPlayer(botModel, targetPlayer, speed)
    local alive = true
    local RS = game:GetService("RunService")
    local PFS = game:GetService("PathfindingService")

    local bot = botModel.PrimaryPart or botModel:FindFirstChild("HumanoidRootPart")
    local humanoid = botModel:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.PlatformStand = false 
    end

currentPath = {}
currentWaypoint = 1
 useFallback = false

 function getTargetHRP()
        return targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    end

function computePath(targetHRP)
        local path = PFS:CreatePath({
            AgentRadius = 2,
            AgentHeight = 5,
            AgentCanJump = true,
            AgentJumpHeight = 10,
            AgentCanClimb = true,
        })
        path:ComputeAsync(bot.Position, targetHRP.Position)

        if path.Status == Enum.PathStatus.Success then
            currentPath = path:GetWaypoints()
            currentWaypoint = 1
            useFallback = false
        else
            currentPath = {}
            useFallback = true
        end
    end

 computeLoop = task.spawn(function()
        while alive and bot and targetPlayer do
            local targetHRP = getTargetHRP()
            if targetHRP then
                computePath(targetHRP)
            else
                useFallback = true
                currentPath = {}
            end
            task.wait(1)
        end
    end)

    local heartbeatConn = RS.Heartbeat:Connect(function()
        if not alive or not bot or not targetPlayer then return end
        local targetHRP = getTargetHRP()
        if not targetHRP then
            bot.Velocity = Vector3.zero
            return
        end

        if useFallback then
            local dir = (targetHRP.Position - bot.Position)
            if dir.Magnitude > 1 then
                bot.Velocity = dir.Unit * (speed or 16)
            else
                bot.Velocity = Vector3.zero
            end
            return
        end

        local wp = currentPath[currentWaypoint]
        if not wp then return end

        local goalPos = wp.Position
        local dir = goalPos - bot.Position
        if dir.Magnitude < 2 then
            currentWaypoint = 1
            if currentWaypoint > #currentPath then
                bot.Velocity = Vector3.zero
                return
            end
        else
            bot.Velocity = dir.Unit * (speed or 16)
        end
    end)

    return function()
        alive = false
        heartbeatConn:Disconnect()
        task.cancel(computeLoop)
    end
end

TargetInput = nil

TargetInput = FunTab:CreateInput({
   Name = "TARGET NAME",
   PlaceholderText = "ENTER PLAYER NAME",
   RemoveTextAfterFocusLost = false,
   Flag = "TargetInput",
   Callback = function(text)
      local plr = getTargetPlayer(text)
      if plr then
         targetPlayer = plr
         orbitTarget = plr
         flashStepTarget = plr
         attackTarget = plr
         WsesCmds.target = plr
         Rayfield:Notify({Title="Target",Content="Target: "..text,Duration=2})
      else
         Rayfield:Notify({Title="Error",Content="PLAYER NOT FOUND",Duration=2})
      end
   end
})


ToggleSection = FunTab:CreateSection("TOGGLES")

FunTab:CreateToggle({
   Name = "GRAB ALL NPCS (DO THIS FIRST)",
   CurrentValue = false,
   Flag = "WsesGrabToggle",
   Callback = function(v)
      wsesGrabEnabled = v
      if v then
         task.spawn(function()
            char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            h = char:WaitForChild("HumanoidRootPart")
            oldPos = h.Position

            for _, d in ipairs(YourFolder:GetChildren()) do
               if d.Name == "YouDecoy" and d:FindFirstChild("HumanoidRootPart") then
                  hrp = d.HumanoidRootPart
                  hum = d:FindFirstChildOfClass("Humanoid")
                  while hum and hum.Sit ~= true do
                     h.CFrame = CFrame.new(hrp.Position + Vector3.new(0,8,0))
                     Grab:FireServer(hrp, hrp.CFrame)
                     task.wait(0.1)
                     UnGrab:FireServer(hrp)
                  end
               end
            end

            h.CFrame = CFrame.new(oldPos)
         end)
      end
   end
})

FunTab:CreateToggle({
    Name = "FLY",
    CurrentValue = false,
    Flag = "WsesFly",
    Callback = function(v)
        WsesCmds.fly = v
    end,
})

FunTab:CreateToggle({
    Name = "SPIN",
    CurrentValue = false,
    Flag = "WsesSpin",
    Callback = function(v)
        WsesCmds.spin = v
    end,
})

FunTab:CreateToggle({
    Name = "FOLLOW PLAYER",
    CurrentValue = false,
    Flag = "PathFollow",
    Callback = function(v)
        WsesCmds.pathfollow = v
        
        if v then
            if not WsesCmds.target then
                Rayfield:Notify({Title="Error",Content="SELECT PLAYER FIRST",Duration=2})
                WsesCmds.pathfollow = false
                return
            end
            
            for _, cleanup in pairs(followCleanups) do
                cleanup()
            end
            followCleanups = {}
            
            for _, d in ipairs(decoyFolder:GetChildren()) do
                if isValidYouDecoy(d) then
                    local cleanup = followPlayer(d, WsesCmds.target, WsesCmds.pathfollowSpeed)
                    table.insert(followCleanups, cleanup)
                end
            end
            
            Rayfield:Notify({Title="Pathfinding",Content="Following "..WsesCmds.target.Name,Duration=2})
        else
            for _, cleanup in pairs(followCleanups) do
                cleanup()
            end
            followCleanups = {}
        end
    end,
})

FunTab:CreateToggle({
    Name = "STARE AT TARGET",
    CurrentValue = false,
    Flag = "EyesTarget",
    Callback = function(v)
        WsesCmds.eyes2 = v
        if v and not WsesCmds.target then
            Rayfield:Notify({Title="Error",Content="Select target first!",Duration=2})
            WsesCmds.eyes2 = false
        else
            Rayfield:Notify({Title="👁️",Content=v and "Looking at "..WsesCmds.target.Name or "Stopped looking",Duration=2})
        end
    end,
})

FunTab:CreateToggle({
    Name = "WALK ON WATER",
    CurrentValue = false,
    Flag = "WATER SAFE",
    Callback = function(v)
        WsesCmds.watersafe = v
        if Workspace:FindFirstChild("Map") and Workspace.Map:FindFirstChild("AlwaysHereTweenedObjects") then
            local ocean = Workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("Ocean")
            if ocean and ocean:FindFirstChild("Object") and ocean.Object:FindFirstChild("ObjectModel") then
                local oceanModel = ocean.Object.ObjectModel
                for _, part in ipairs(oceanModel:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = v
                        part.CanQuery = v
                    end
                end
                Rayfield:Notify({Title="WATER SAFE",Content=v and "WALKING ON WATER" or "NORMAL WATER",Duration=2})
            end
        end
    end,
})


FunTab:CreateToggle({
   Name = "LOOP JUMP",
   CurrentValue = false,
   Flag = "LoopJumpToggle",
   Callback = function(v)
      if v then
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then loopJumpingDecoys[d] = true end
         end
      else
         loopJumpingDecoys = {}
      end
   end
})

FunTab:CreateToggle({
   Name = "WANDER",
   CurrentValue = false,
   Flag = "WanderToggle",
   Callback = function(v) wandering = v end
})

FunTab:CreateToggle({
   Name = "ORBIT TARGET",
   CurrentValue = false,
   Flag = "OrbitToggle",
   Callback = function(v)
      orbiting = v
      if v and not orbitTarget then
         TargetInput:Set("ENTER NAME FIRST")
         orbiting = false
      else
         orbitAngleMap = {}
      end
   end
})

FunTab:CreateToggle({
   Name = "FLASH STEPS",
   CurrentValue = false,
   Flag = "FlashStepsToggle",
   Callback = function(v)
      flashStepping = v
      if v and not flashStepTarget then
         TargetInput:Set("ENTER NAME FIRST")
         flashStepping = false
      else
         flashStepTime = 0
      end
   end
})

FunTab:CreateToggle({
   Name = "ATTACK TARGET",
   CurrentValue = false,
   Flag = "AttackToggle",
   Callback = function(v)
      if v and attackTarget then
         startAttackLoop(attackTarget)
      else
         stopAttackLoop()
      end
   end
})

SliderSection = FunTab:CreateSection("SLIDERS")

FunTab:CreateSlider({
    Name = "SPIN SPEED",
    Range = {-1000, 1000},
    Increment = 5,
    Suffix = " (30 OPTIMAL)",
    CurrentValue = 30,
    Flag = "WsesSpinSpeed",
    Callback = function(v)
        WsesCmds.spinspeed = v
    end,
})

FunTab:CreateSlider({
    Name = "WALK SPEED",
    Range = {0, 1000},
    Increment = 1,
    Suffix = " (16 OPTIMAL)",
    CurrentValue = 16,
    Flag = "WalkSpeed",
    Callback = function(v)
        WsesCmds.speed = v
    end,
})

FunTab:CreateSlider({
    Name = "JUMP POWER",
    Range = {0, 1000},
    Increment = 1,
    Suffix = " (24 OPTIMAL)",
    CurrentValue = 24,
    Flag = "JumpPower",
    Callback = function(v)
        WsesCmds.jumppower = v
    end,
})

FunTab:CreateSlider({
    Name = "PATH FINDING",
    Range = {1, 1000},
    Increment = 1,
    Suffix = " STUDS/S",
    CurrentValue = 30,
    Flag = "PathSpeed",
    Callback = function(v)
        WsesCmds.pathfollowSpeed = v
    end,
})

FunTab:CreateSlider({
    Name = "FLY SPEED",
    Range = {-1000, 1000},
    Increment = 5,
    Suffix = " (5 OPTIMAL)",
    CurrentValue = 5,
    Flag = "FlySpeed",
    Callback = function(v)
        WsesCmds.flyspeed = v
    end,
})

FunTab:CreateSlider({
   Name = "ORBIT RADIUS",
   Range = {5, 500},
   Increment = 1,
   Suffix = " STUDS",
   CurrentValue = orbitRadius,
   Flag = "OrbitRadius",
   Callback = function(v) orbitRadius = v end
})

FunTab:CreateSlider({
   Name = "ORBIT SPEED",
   Range = {10, 360},
   Increment = 10,
   Suffix = " DEG/S",
   CurrentValue = orbitSpeed,
   Flag = "OrbitSpeed",
   Callback = function(v) orbitSpeed = v end
})

FunTab:CreateSlider({
   Name = "FLASH STEP SPEED",
   Range = {0.001, 1},
   Increment = 0.01,
   Suffix = "S",
   CurrentValue = flashStepSpeed,
   Flag = "FlashStepSpeed",
   Callback = function(v) flashStepSpeed = v end
})

ButtonSection = FunTab:CreateSection("ACTIONS")

FunTab:CreateButton({
   Name = "SIT ALL",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Sit = true end
      end
   end
})

FunTab:CreateButton({
   Name = "STAND ALL",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Sit = false end
      end
   end
})

FunTab:CreateButton({
   Name = "LINE UP",
   Callback = function()
      char = LocalPlayer.Character
      if char and char:FindFirstChild("HumanoidRootPart") then
         basePos = char.HumanoidRootPart.Position + char.HumanoidRootPart.CFrame.LookVector * 5
         right = char.HumanoidRootPart.CFrame.RightVector
         index = 0

         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
               local offset = right * ((index - 0.5) * 3)
               local pos = basePos + offset
               local hrp = d:FindFirstChild("HumanoidRootPart")
               if hrp then
                  hrp.CFrame = CFrame.new(pos, pos + char.HumanoidRootPart.CFrame.LookVector)
               end
               index = index + 1 -- Increment instead of resetting
            end
         end
      end
   end
})


FunTab:CreateButton({
   Name = "JUMP",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then
            d:FindFirstChildOfClass("Humanoid").Jump = true
         end
      end
   end,
})

FunTab:CreateButton({
    Name = "PLATFORM STAND",
    CurrentValue = false,
    Flag = "WsesPlatform",
    Callback = function(v)
        for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
                d:FindFirstChildOfClass("Humanoid").PlatformStand = v
            end
        end
    end,
})

task.spawn(function()
   while true do
      if following and targetPlayer then
         pos = targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") and targetPlayer.Character.HumanoidRootPart.Position
         if pos then
            for _, d in ipairs(decoyFolder:GetChildren()) do
               if isValidYouDecoy(d) then
                  hum = d:FindFirstChildOfClass("Humanoid")
                  hrp = d.HumanoidRootPart
                  hum.WalkSpeed = currentSpeed
                  if (hrp.Position - pos).Magnitude > 5 then hum:MoveTo(pos) end
               end
            end
         end
      end
      task.wait(0.5)
   end
end)

RunService.Heartbeat:Connect(function(dt)
   for d, s in pairs(spinningDecoys) do
      if isValidYouDecoy(d) then
         d.HumanoidRootPart.CFrame = d.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(s)*dt, 0)
      else
         spinningDecoys[d] = nil
      end
   end
end)

task.spawn(function()
   while true do
      for d in pairs(loopJumpingDecoys) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Jump = true
         else loopJumpingDecoys[d] = nil end
      end
      task.wait(1.5)
   end
end)

task.spawn(function()
   while true do
      if wandering then
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
               hum = d:FindFirstChildOfClass("Humanoid")
               hrp = d.HumanoidRootPart
               offset = Vector3.new(math.random(-10,10),0,math.random(-10,10))
               hum:MoveTo(hrp.Position + offset)
            end
         end
      end
      task.wait(2)
   end
end)

RunService.Heartbeat:Connect(function(dt)
   if orbiting and orbitTarget and orbitTarget.Character and orbitTarget.Character:FindFirstChild("HumanoidRootPart") then
      hrp = orbitTarget.Character.HumanoidRootPart
      decoys = {}
      for _, d in ipairs(decoyFolder:GetChildren()) do if isValidYouDecoy(d) then table.insert(decoys, d) end end
      for i, d in ipairs(decoys) do
         angle = orbitAngleMap[d] or (360/#decoys)*i
         angle = (angle + orbitSpeed*dt)%360
         orbitAngleMap[d] = angle
         rad = math.rad(angle)
         offset = Vector3.new(math.cos(rad)*orbitRadius, 0, math.sin(rad)*orbitRadius)
         pos = hrp.Position + offset
         if d.HumanoidRootPart then d.HumanoidRootPart.CFrame = CFrame.new(pos, hrp.Position) end
      end
   end
end)

task.spawn(function()
   while true do
      if flashStepping and flashStepTarget and flashStepTarget.Character and flashStepTarget.Character:FindFirstChild("HumanoidRootPart") then
         hrp = flashStepTarget.Character.HumanoidRootPart
         flashStepTime = flashStepTime + 1
         base = hrp.Position
         back = -hrp.CFrame.LookVector
         right = hrp.CFrame.RightVector
         look = hrp.CFrame.LookVector
         decoys = {}
         for _, d in ipairs(decoyFolder:GetChildren()) do if isValidYouDecoy(d) then table.insert(decoys, d) end end
         for i, d in ipairs(decoys) do
            if d.HumanoidRootPart then
               phase = (#decoys-i)*math.pi/4
               swing = math.sin(flashStepTime*0.3 + phase)*5
               spread = (i - #decoys/2)*4
               side = swing + spread
               backOffset = 1.5 + i*0.15
               pos = base + back*backOffset + right*side
               d.HumanoidRootPart.CFrame = CFrame.new(pos, pos + look)
            end
         end
      end
      task.wait(flashStepSpeed)
   end
end)

RunService.Heartbeat:Connect(function(dt)
    for _, d in ipairs(decoyFolder:GetChildren()) do
        if isValidYouDecoy(d) then
            local hrp = d.HumanoidRootPart
            local humanoid = d:FindFirstChildOfClass("Humanoid")
            
            if humanoid then
                humanoid.JumpPower = WsesCmds.jumppower
                humanoid.WalkSpeed = WsesCmds.speed
                
                if WsesCmds.eyes2 and WsesCmds.target and WsesCmds.target.Character then
                    local targetHRP = WsesCmds.target.Character:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local selfCF = hrp.CFrame
                        local targetPos = Vector3.new(targetHRP.Position.X, hrp.Position.Y, targetHRP.Position.Z)
                        local direction = (targetPos - hrp.Position).Unit
                        local currentLook = Vector3.new(selfCF.LookVector.X, 0, selfCF.LookVector.Z).Unit
                        local angle = math.acos(math.clamp(currentLook:Dot(direction), -1, 1))
                        local cross = currentLook:Cross(direction)
                        local sign = cross.Y >= 0 and 1 or -1
                        local turnSpeed = angle * WsesCmds.spinspeed
                        hrp.AssemblyAngularVelocity = Vector3.new(0, sign * turnSpeed, 0)
                    end
                end
            end
            
            if WsesCmds.fly then
                hrp.AssemblyLinearVelocity = Vector3.new(0, WsesCmds.flyspeed, 0)
            end
            
            if WsesCmds.spin then
                hrp.AssemblyAngularVelocity = Vector3.new(0, WsesCmds.spinspeed, 0)
            end
        end
    end
end)

function startAttackLoop(t)
   attacking = true attackTarget = t
   task.spawn(function()
      while attacking and attackTarget and attackTarget.Character and attackTarget.Character:FindFirstChild("HumanoidRootPart") do
         thrp = attackTarget.Character.HumanoidRootPart
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) and d.HumanoidRootPart then
               offset = Vector3.new(math.random(-3,3), math.random(2,5), math.random(-3,3))
               pos = thrp.Position + offset
               d.HumanoidRootPart.CFrame = CFrame.new(pos, thrp.Position)
            end
         end
         task.wait(0.05)
      end
   end)
end

function stopAttackLoop()
   attacking = false attackTarget = nil
end

CommandsTab = Window:CreateTab("COMMANDS (NORMAL TO BE EMPTY)","user")

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Workspace = game:GetService("Workspace")
UserInputService = game:GetService("UserInputService")
LocalPlayer = Players.LocalPlayer

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
Struggle = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
StickyPartEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
DestroyToyEvent = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
MenuToys = ReplicatedStorage:WaitForChild("MenuToys")

SelectedPlayer = nil
bringTimer = 0
tpTimer = 0
switchTimer = 0
Mouse = LocalPlayer:GetMouse()
KillHB = nil
LoopKillOn = false
GrabKickHB = nil
LoopGrabKickOn = false
LoopBringActive = false
LoopBangActive = false

LoopPencilActive = false
PencilList = {}
AKList = {}
PencilCooldown = {}

LoopBlobkickActive = false
BlobkickList = {}
Blob = nil
BlobkickCooldown = {}
BKA = false
BKAWL = false


ESPEnabled = false
ESPTargets = {}
ESPBoxes = {}


BangSpeed = 0.8
BangDistance = 4
HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)


AllowedUsers = {"JobsonNumber","QR4TL","66hxstxfy","gaigeskywalker1"}


function IsAllowed(playerName)
    for _, allowedName in pairs(AllowedUsers) do
        if string.lower(allowedName) == string.lower(playerName) then
            return true
        end
    end
    return false
end

function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end

function grab(prt)
    RemoteSetNetworkOwner:FireServer(prt, prt.CFrame)
end

function SpawnPencilForPlayer(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return nil end
    
    if not LocalPlayer.CanSpawnToy.Value then return nil end
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if spawnedToys then
        local oldPencil = spawnedToys:FindFirstChild(playerName)
        if oldPencil then
            DestroyToyEvent:FireServer(oldPencil)
            task.wait(0.18)
        end
    end
    
    local spawnCFrame = CFrame.new(targetPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 0, 5))
    
    local success, result = pcall(function()
        return SpawnToyRF:InvokeServer("ToolPencil", spawnCFrame, Vector3.new(0, 0, 0))
    end)
    
    if not success then return nil end
    
    for i = 1, 10 do
        local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if spawnedToys then
            local pencil = spawnedToys:FindFirstChild("ToolPencil")
            if pencil then
                pencil.Name = playerName
                return pencil
            end
        end
        task.wait(0.1)
    end
    
    return nil
end

function AttachPencilToPlayer(pencil, playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local torso = targetPlayer.Character:FindFirstChild("Torso")
    if not torso then return false end
    
    task.wait(0.4)
    
    local soundPart = pencil:FindFirstChild("SoundPart")
    if soundPart then
        RemoteSetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
    end
    
    if pencil:FindFirstChild("StickyPart") then
        StickyPartEvent:FireServer(pencil.StickyPart, torso, CFrame.new(0, -1, 0) * CFrame.Angles(0, math.pi, 0))
        return true
    end
    
    return false
end

function CheckAndManagePencil(playerName)
    if not LoopPencilActive then return end
    if not table.find(PencilList, playerName) then return end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then 
        local index = table.find(PencilList, playerName)
        if index then table.remove(PencilList, index) end
        return
    end
    
    if targetPlayer.InPlot.Value then return end
    
    if PencilCooldown[playerName] and tick() - PencilCooldown[playerName] < 2 then return end
    
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    local hasPencil = false
    
    if spawnedToys then
        local pencil = spawnedToys:FindFirstChild(playerName)
        if pencil and pencil:FindFirstChild("StickyPart") then
            local stickyWeld = pencil.StickyPart:FindFirstChild("StickyWeld")
            if stickyWeld and stickyWeld.Part1 then
                hasPencil = true
            end
        end
    end
    
    if not hasPencil then
        PencilCooldown[playerName] = tick()
        local newPencil = SpawnPencilForPlayer(playerName)
        if newPencil then
            AttachPencilToPlayer(newPencil, playerName)
        end
    end
end

function PencilPlayer(playerName, loopMode)
    if not Players:FindFirstChild(playerName) then
        return Rayfield:Notify({
            Title = "Pencil Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    
    if loopMode then
        if not table.find(PencilList, playerName) then
            table.insert(PencilList, playerName)
        end
        if not LoopPencilActive then
            LoopPencilActive = true
        end
        return true
    else
        local pencil = SpawnPencilForPlayer(playerName)
        if pencil then
            local attached = AttachPencilToPlayer(pencil, playerName)
            return attached
        end
        return false
    end
end

function DestroyBlob()
    if Blob and Blob.Parent then
        DestroyToyEvent:FireServer(Blob)
        Blob = nil
    end
end

function BlobKick()
    while true do
        local smegma = true
        local listtype = 1
        local gotsomeone = false
        local listtt = {}
        
        if BKA then
            listtype = 2
            for _,plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer then
                    if BKAWL and not LocalPlayer:IsFriendsWith(plr.UserId) then
                        table.insert(listtt,plr.Name)
                    elseif not BKAWL then
                        table.insert(listtt,plr.Name)
                    end
                end
            end
        else
            listtt = BlobkickList
        end
        
        if listtype == 2 then
            if not BKA then
                smegma = false
            end
        end
        
        for _,plrr in pairs(listtt) do
            local j, h
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                j = LocalPlayer.Character.HumanoidRootPart.CFrame
                h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
            end
            
            if smegma and Players:FindFirstChild(plrr) and Players:FindFirstChild(plrr) ~= LocalPlayer then
                local continue = false
                local plr = Players:FindFirstChild(plrr)
                
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    if plr.Character.HumanoidRootPart.Massless then
                        if plr.Character.Humanoid.SeatPart then
                            continue = true
                        end
                    else
                        continue = true
                    end
                end
                
                if BKA and BKAWL and LocalPlayer:IsFriendsWith(Players:FindFirstChild(plrr).UserId) then
                    continue = false
                end
                
                while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and plr and plr.Character and plr.Character.Parent == Workspace and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
                    if listtype == 2 then
                        if not BKA then
                            smegma = false
                        end
                    end
                    
                    gotsomeone = true
                    continue = false
                    
                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        if plr.Character.HumanoidRootPart.AssemblyLinearVelocity.Magnitude > 5000 then
                            continue = false
                        end
                        if plr.Character.HumanoidRootPart.Massless then
                            if plr.Character.Humanoid.SeatPart then
                                continue = true
                            end
                        else
                            continue = true
                        end
                    end
                    
                    if continue and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
                        Blob = LocalPlayer.Character.Humanoid.SeatPart.Parent
                        local localtime = tick()
                        
                        if (plr.Character.HumanoidRootPart.CFrame.Position - LocalPlayer.Character.HumanoidRootPart.CFrame.Position + LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity).magnitude > 30 and plr.Character.Parent == Workspace then
                            LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character:FindFirstChild("HumanoidRootPart").CFrame + (plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity / math.pi)
                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character:FindFirstChild("HumanoidRootPart").AssemblyLinearVelocity
                            task.wait(0.25)
                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                RemoteSetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                            end
                        end
                        
                        local timee = tick()
                        while tick() - timee < 0.25 and plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and continue do
                            RemoteSetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                            task.wait(0.1)
                        end
                        
                        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and Blob:FindFirstChild("RightDetector") and Blob.RightDetector:FindFirstChild("RightWeld") and Blob:FindFirstChild("BlobmanSeatAndOwnerScript") and Blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab") then
                            plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0,10^10,0)
                            RemoteDestroyGrabLine:FireServer(plr.Character.HumanoidRootPart)
                            Blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(Blob.RightDetector, plr.Character.HumanoidRootPart, Blob.RightDetector.RightWeld)
                        end
                    elseif continue then
                        if LocalPlayer.Character:FindFirstChild("Humanoid") then
                            if LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart then
                                LocalPlayer.Character.Humanoid.Sit = false
                            end
                        end
                        
                        if Blob and Blob:FindFirstChild("VehicleSeat") then
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                Blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end
                            task.wait(0.15)
                        else
                            local foundblob = false
                            Blob = nil
                            
                            local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                            if spawnedToys then
                                for _,itm in pairs(spawnedToys:GetChildren()) do
                                    if itm.Name == "CreatureBlobman" and itm:FindFirstChild("VehicleSeat") then
                                        foundblob = true
                                        Blob = itm
                                    elseif itm.Name == "CreatureBlobman" then
                                        while itm do
                                            DestroyToyEvent:FireServer(itm)
                                            task.wait(0.15)
                                        end
                                    end
                                end
                            end
                            
                            if Blob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                Blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end
                            
                            if not foundblob then
                                task.wait(0.15)
                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.Parent ~= Workspace then
                                        LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
                                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                                    end
                                    
                                    while smegma and not Blob do
                                        if listtype == 2 then
                                            if not BKA then
                                                smegma = false
                                            end
                                        end
                                        
                                        local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                                        if spawnedToys and spawnedToys:FindFirstChild("CreatureBlobman") then
                                            Blob = spawnedToys.CreatureBlobman
                                        elseif LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.Parent ~= Workspace then
                                            LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0,-10,0)
                                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                                        end
                                        
                                        if not Blob then
                                            task.spawn(function()
                                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                                    SpawnToyRF:InvokeServer("CreatureBlobman", CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0,0,15), Vector3.new(0,0,0))
                                                end
                                            end)
                                        end
                                        task.wait()
                                    end
                                end
                            end
                        end
                    end
                    task.wait(0.15)
                end
            end
            
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = j
                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
            end
        end
        
        if not gotsomeone and Blob then
            while Blob and Blob.Parent do
                DestroyToyEvent:FireServer(Blob)
                task.wait(0.15)
            end
            Blob = nil
        end
        task.wait()
    end
end

function BlobKickPlayer(playerName, loopMode)
    if not Players:FindFirstChild(playerName) then
        return Rayfield:Notify({
            Title = "Blobkick Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(playerName)
    
    if loopMode then
        if not table.find(BlobkickList, playerName) then
            table.insert(BlobkickList, playerName)
        end
        if not LoopBlobkickActive then
            LoopBlobkickActive = true
        end
        return true
    else
        local tempAdded = false
        if not table.find(BlobkickList, playerName) then
            table.insert(BlobkickList, playerName)
            tempAdded = true
        end
        
        task.wait(0.15)
        
        if tempAdded then
            local index = table.find(BlobkickList, playerName)
            if index then
                table.remove(BlobkickList, index)
            end
        end
        
        return true
    end
end

task.spawn(BlobKick)


function BringPlayer(name, loopMode)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character
    
    if not targetChar then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player has no character',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return end
    
    if targetHum.Health <= 0 then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player is dead',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Bring Error",
            Content = 'Player in safe zone',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -2, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.08)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return Rayfield:Notify({
            Title = "Bring Failed",
            Content = 'Could not grab player',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    myHRP.CFrame = BackUp
    
    if loopMode then
        task.spawn(function()
            while LoopBringActive and targetChar and targetChar.Parent do
                if not Players:FindFirstChild(name) then
                    LoopBringActive = false
                    break
                end
                
                if targetPlayer.InPlot.Value or targetHum.Health <= 0 then
                    LoopBringActive = false
                    break
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * 3)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                grab(targetHRP)
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    else
        task.defer(function()
            local startTime = tick()
            while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
                  targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * 3)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    end
    
    return true
end

function BangPlayer(name, loopMode)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player left',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character
    
    if not targetChar then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player has no character',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return end
    
    if targetHum.Health <= 0 then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player is dead',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Bang Error",
            Content = 'Player in safe zone',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -2, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.05)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return Rayfield:Notify({
            Title = "Bang Failed",
            Content = 'Could not grab player',
            Duration = 3,
            Image = 7743878857,
        })
    end
    
    myHRP.CFrame = BackUp
    
    if loopMode then
        task.spawn(function()
            local bangOffset = 0
            local bangDirection = 1
            
            while LoopBangActive and targetChar and targetChar.Parent do
                if not Players:FindFirstChild(name) then
                    LoopBangActive = false
                    break
                end
                
                if targetPlayer.InPlot.Value or targetHum.Health <= 0 then
                    LoopBangActive = false
                    break
                end
                
                bangOffset = bangOffset + (bangDirection * (BangSpeed * 0.08))
                
                if bangOffset >= BangDistance then
                    bangDirection = -1
                elseif bangOffset <= 0 then
                    bangDirection = 2
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * bangOffset)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                grab(targetHRP)
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    else
        task.defer(function()
            local startTime = tick()
            local bangOffset = 0
            local bangDirection = 2
            
            while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
                  targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 3 do
                
                bangOffset = bangOffset + (bangDirection * (BangSpeed * 0.3))
                
                if bangOffset >= BangDistance then
                    bangDirection = -1
                elseif bangOffset <= 0 then
                    bangDirection = 2
                end
                
                local targetCFrame = myHRP.CFrame + (myHRP.CFrame.LookVector * bangOffset)
                targetHRP.CFrame = targetCFrame
                targetHRP.Velocity = Vector3.new()
                
                task.wait()
            end
            Destroy_Line(targetHRP)
        end)
    end
    
    return true
end

function SwitchPlayer()
    if switchTimer ~= 0 then return end
    
    if not Players:FindFirstChild(SelectedPlayer) then
        return Rayfield:Notify({
            Title = "Switch error",
            Content = 'Player left',
            Duration = 4,
            Image = 7743878056,
        })
    end
    
    targetPlayer = Players:FindFirstChild(SelectedPlayer)
    targetChar = targetPlayer.Character
    
    if not targetChar then return end
    
    targetHum = FWC(targetChar, "Humanoid", 3)
    HRP = FWC(targetChar, "HumanoidRootPart", 3)
    
    if targetHum.Health == 0 or targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "Switch Error",
            Content = 'Player died or in safe-zone',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    mychar = LocalPlayer.Character
    myHRP = FWC(mychar, "HumanoidRootPart", 3)
    BackUp = myHRP.CFrame
    
    while task.wait() and HRP and FWC(mychar, "Humanoid").Health ~= 0 and not (HRP.Parent.Head:FindFirstChild("PartOwner")) or (HRP.Parent.Head:FindFirstChild("PartOwner") and HRP.Parent.Head:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) do
        if switchTimer > 75 then
            myHRP.CFrame = BackUp
            switchTimer = 0
            return
        else
            switchTimer = switchTimer + 1
        end
        
        if Workspace.PlotItems.PlayersInPlots:FindFirstChild(SelectedPlayer) or LocalPlayer.IsHeld.Value == true then
            myHRP.CFrame = BackUp
            return
        end
        
        myHRP.CFrame = HRP.CFrame
        task.defer(grab, HRP)
    end
    
    for _, prt in pairs(HRP.Parent:GetChildren()) do
        if prt:IsA("Part") then
            prt.Velocity = Vector3.new()
        end
    end
    
    HRP.CFrame = BackUp
    
    if GrabFlag == "Default" then
        Destroy_Line(HRP)
    end
    
    for _, prt in pairs(myHRP.Parent:GetChildren()) do
        if prt:IsA("Part") then
            prt.Velocity = Vector3.new()
        end
    end
    
    switchTimer = 0
end

function TPPlayerToWater(name)
    if not Players:FindFirstChild(name) then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player left',
            Duration = 4,
            Image = 7743878056,
        })
    end
    
    targetPlayer = Players:FindFirstChild(name)
    targetChar = targetPlayer.Character
    
    if not targetChar then return end
    
    targetHum = FWC(targetChar, "Humanoid", 3)
    targetHRP = FWC(targetChar, "HumanoidRootPart", 3)
    targetHead = FWC(targetChar, "Head", 3)
    
    if targetHum.Health == 0 or targetHum:GetState() == Enum.HumanoidStateType.Dead then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player died',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    if targetPlayer.InPlot.Value then
        return Rayfield:Notify({
            Title = "TP Error",
            Content = 'Player in House',
            Duration = 4,
            Image = 7743878857,
        })
    end
    
    mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    myhum = FWC(mychar, "Humanoid", 3)
    myHRP = FWC(mychar, "HumanoidRootPart", 3)
    myHead = FWC(mychar, "Head", 3)
    
    last_pos = targetHRP.Position
    BackUp = myHRP.CFrame
    
    function Stop(Back)
        myHRP.CFrame = Back
        for _, prt in pairs(mychar:GetChildren()) do
            if prt:IsA("BasePart") then
                prt.Velocity = Vector3.new()
            end
        end
        tpTimer = 0
    end
    
    task.spawn(function()
        while (not (targetHead:FindFirstChild("PartOwner")) or targetHead:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) and task.wait(0.1) do
            if last_pos ~= targetHRP.Position then
                last_pos = targetHRP.Position
            end
        end
    end)
    
    while not (targetHead:FindFirstChild("PartOwner")) or (targetHead:FindFirstChild("PartOwner") and targetHead:FindFirstChild("PartOwner").Value ~= LocalPlayer.Name) do
        if targetHRP and targetHRP.Parent and myhum.Health ~= 0 and targetHum.Health ~= 0 then
            if tpTimer < 74 and not (targetPlayer.InPlot.Value) then
                tpTimer = tpTimer + 1
                if not (LocalPlayer.IsHeld.Value) then
                    myHRP.CFrame = targetHRP.CFrame + ((targetHRP.Position - last_pos) * LocalPlayer:GetNetworkPing() * 25) + Vector3.new(0, -3.5, 0)
                    task.spawn(grab, targetHRP)
                    task.wait()
                else
                    while myHead:FindFirstChild("PartOwner") do
                        Struggle:FireServer(LocalPlayer)
                        task.wait()
                    end
                end
            else
                Stop(BackUp)
                return
            end
        else
            Stop(BackUp)
            return
        end
    end
    
    myHRP.CFrame = BackUp
    
    waterPos = Vector3.new(320, -61, 423)
    
    task.defer(function()
        startTime = tick()
        while targetHead and targetHead.Parent and (targetHead:FindFirstChild("PartOwner") and targetHead:FindFirstChild("PartOwner").Value == LocalPlayer.Name) and tick() - startTime < 5 do
            if not targetHead.BallSocketConstraint.Enabled then
                targetHRP.CFrame = CFrame.new(waterPos)
                for _, prt in pairs(targetChar:GetChildren()) do
                    if prt:IsA("Part") then
                        prt.Velocity = Vector3.new()
                        prt.CanTouch = false
                    end
                end
            else
                for _, prt in pairs(targetChar:GetChildren()) do
                    if prt:IsA("Part") then
                        prt.CFrame = CFrame.new(waterPos)
                        prt.Velocity = Vector3.new()
                        prt.CanTouch = false
                    end
                end
            end
            task.wait()
        end
    end)
    
    task.wait(0)
    if GrabFlag == "Default" then
        Destroy_Line(targetHRP)
    end
    tpTimer = 0
end

blobFreezeLoop = false
 blobFreezeTarget = nil
 TargetPosition = Vector3.new(-480, -36, -119)


function findBlobman()
    local toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    return toys and toys:FindFirstChild("CreatureBlobman") or nil
end

function spawnBlobman()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    SpawnToyRF:InvokeServer("CreatureBlobman", char.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end

function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 30 do
        task.wait(0.01)
        b = findBlobman()
        if b then return b end
    end
    return nil
end

function getBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        for _, v in pairs(Workspace:GetDescendants()) do
            if v.Name == "BlobmanSeatAndOwnerScript" and v.Parent.Name == "CreatureBlobman" then
                if not humanoid.SeatPart then
                    local seat = v.Parent:FindFirstChildWhichIsA("VehicleSeat")
                    if seat then
                        humanoid.Sit = true
                        seat:Sit(humanoid)
                        task.wait(0.1)
                    end
                end
                if humanoid.SeatPart and humanoid.SeatPart.Parent == v.Parent then
                    return v.Parent
                end
            end
        end
    end
    return nil
end

function isOnBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart and humanoid.SeatPart.Parent and humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
            return true
        end
    end
    return false
end

function getDetector(blob, side)
    local detector = blob:FindFirstChild(side.."Detector")
    local weld = detector and detector:FindFirstChild(side.."Weld")
    return detector, weld
end

function inPlot(player)
    local plots = Workspace:FindFirstChild("PlotItems")
    local playersInPlots = plots and plots:FindFirstChild("PlayersInPlots")
    return playersInPlots and playersInPlots:FindFirstChild(player.Name)
end

function ProcessBlobFreeze(targetName)
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then return false, "Player not found" end
    
    if inPlot(targetPlayer) then return false, "Player is in a plot" end
    
    local targetChar = targetPlayer.Character
    if not targetChar then return false, "Player has no character" end
    
    local blob = ensureBlobman()
    if not blob then return false, "Failed to spawn blobman" end
    
    if not isOnBlobman() then
        local currentBlob = getBlobman()
        if not currentBlob then return false, "Failed to mount blobman" end
        blob = currentBlob
    end
    
    local localChar = LocalPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    if not localChar or not localChar:FindFirstChild("HumanoidRootPart") or not targetHRP then
        return false, "Failed to get character parts"
    end
    
    localChar.HumanoidRootPart.CFrame = targetHRP.CFrame * CFrame.new(0, 0, 5)
    task.wait(0.3)
    
    local detector, weld = getDetector(blob, "Right")
    if not (detector and weld) then return false, "Failed to get blobman parts" end
    
    local currentGrab = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
    if not currentGrab then return false, "Failed to get CreatureGrab" end
    
    currentGrab:FireServer(blob.RightDetector, targetHRP, weld)
    task.wait(0.02)
    
    for _, part in ipairs(targetChar:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
            part.AssemblyLinearVelocity = Vector3.new(0, 15, 0)
            blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(weld, part)
        end
    end
    
    local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
    if drop then
        for _, part in ipairs(targetChar:GetDescendants()) do
            if part:IsA("Weld") or part:IsA("BallSocketConstraint") then
                drop:FireServer(part, part)
            end
        end
    end
    
    task.wait(0.1)
    
    if targetChar and targetChar:FindFirstChild("HumanoidRootPart") then
        targetChar.HumanoidRootPart.CFrame = CFrame.new(TargetPosition)
        targetChar.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        targetChar.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
        
        for _, part in pairs(targetChar:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Velocity = Vector3.zero
                part.RotVelocity = Vector3.zero
            end
        end
        
        task.wait(0.5)
        
        if targetChar:FindFirstChild("HumanoidRootPart") then
            targetChar.HumanoidRootPart.Anchored = true
        end
    end
    
    return true, "Success"
end

function BlobFreezeLoopFunc()
    while blobFreezeLoop do
        if blobFreezeTarget then
            ProcessBlobFreeze(blobFreezeTarget)
        end
        task.wait(0.5)
    end
end

 heartHighRun = false
heartTarget = LocalPlayer.Name 
heartConnection = nil
 heartToy = nil

function ToggleHeartSparkler(targetName, enable)
    heartHighRun = enable
    
    if targetName then
        heartTarget = targetName
    end
    
    if enable then
        task.spawn(function()
            local targetPlayer = Players:FindFirstChild(heartTarget)
            if not targetPlayer then
                heartTarget = LocalPlayer.Name
                targetPlayer = LocalPlayer
            end
            
            if not targetPlayer.Character then return end
            local hrp = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if not hrp then return end

            pcall(function()
                SpawnToyRF:InvokeServer("FireworkSparkler", hrp.CFrame * CFrame.new(0, 50, 0), Vector3.zero)
            end)

            local folderName = LocalPlayer.Name .. "SpawnedInToys"
            local folder = Workspace:WaitForChild(folderName, 5)
            if not folder then return end
            
            heartToy = folder:WaitForChild("FireworkSparkler", 5)
            if not heartToy then return end
            
            local part = heartToy:FindFirstChild("Handle") or heartToy:FindFirstChildWhichIsA("BasePart")
            if not part then return end

            task.wait(0.2)
            
            for _, v in pairs(heartToy:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Anchored = false
                    v.CanCollide = false
                    v.Massless = true
                end
            end
            part:BreakJoints()

            local bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bp.P = 20000
            bp.D = 500
            bp.Parent = part

            local bg = Instance.new("BodyGyro")
            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            bg.P = 3000
            bg.CFrame = CFrame.new()
            bg.Parent = part

            local t = 0
            
            if heartConnection then heartConnection:Disconnect() end
            
            heartConnection = RunService.Heartbeat:Connect(function(dt)
                if not heartHighRun or not part or not part.Parent then
                    if heartConnection then heartConnection:Disconnect() end
                    if heartToy then pcall(function() heartToy:Destroy() end) end
                    return
                end

                local targetPlayer = Players:FindFirstChild(heartTarget)
                if not targetPlayer or not targetPlayer.Character then 
                    heartTarget = LocalPlayer.Name
                    targetPlayer = LocalPlayer
                end
                
                local currentHrp = targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                if not currentHrp then return end

                pcall(function()
                    RemoteSetNetworkOwner:FireServer(part, part.CFrame)
                end)

                t = t + (8 * dt)
                
                local scale = 1.5
                local x = 16 * math.sin(t)^3
                local y = 13 * math.cos(t) - 5 * math.cos(2*t) - 2 * math.cos(3*t) - math.cos(4*t)

                local relPos = Vector3.new(x * scale, (y * scale) + 3, 0) 
                local finalPos = currentHrp.CFrame:PointToWorldSpace(relPos)

                bp.Position = finalPos
                bg.CFrame = currentHrp.CFrame
            end)
        end)
    else
        if heartConnection then 
            heartConnection:Disconnect() 
            heartConnection = nil 
        end
        if heartToy then 
            pcall(function() heartToy:Destroy() end) 
            heartToy = nil 
        end
    end
end

tptopoisonLoop = false
 tptopoisonTarget = nil
 PoisonPosition = Vector3.new(36, -68, 269)

function TPToPoison(targetName)
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then return false, "Player not found" end
    
    local targetChar = targetPlayer.Character
    if not targetChar then return false, "Player has no character" end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    if not targetHRP or not targetHum then return false, "Invalid character" end
    
    if targetHum.Health <= 0 then return false, "Player is dead" end
    if targetPlayer.InPlot.Value then return false, "Player in safe zone" end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    if not myHRP or not myHum then return false, "Your character invalid" end
    
    local BackUp = myHRP.CFrame
    local success = false

for i = 1, 30 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0.1)
    end
    
    if not success then
        myHRP.CFrame = BackUP
        return false, "Could not grab player"
    end

myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true, "Success"
end


function TPPoisonLoopFunc()
    while tptopoisonLoop do
        if tptopoisonTarget then
            TPToPoison(tptopoisonTarget)
        end
        task.wait(1)
    end
end

PoisonPosition = Vector3.new(36, -68, 269)
BringAllActive = false
BringAllLoopActive = false


function BringPlayerToPoison(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local targetChar = targetPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return false end
    
    if targetHum.Health <= 0 then return false end
    if targetPlayer.InPlot.Value then return false end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return false end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 45 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return false
    end
    
    myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true
end

function BringAllToPoison()
    local successCount = 0
    local failCount = 0
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local success = BringPlayerToPoison(player.Name)
            if success then
                successCount = successCount + 1
            else
                failCount = failCount + 1
            end
            task.wait(0)
        end
    end
    
    return successCount, failCount
end

function BringAllLoop()
    while BringAllLoopActive do
        BringAllToPoison()
        task.wait(0) 
    end
end

WaterPosition = Vector3.new(327, 26, 699)
BringAllActive2 = false
BringAllLoop2Active = false


function BringPlayerToWater(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)
    if not targetPlayer or not targetPlayer.Character then return false end
    
    local targetChar = targetPlayer.Character
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    local targetHum = targetChar:FindFirstChild("Humanoid")
    
    if not targetHRP or not targetHum then return false end
    
    if targetHum.Health <= 0 then return false end
    if targetPlayer.InPlot.Value then return false end
    
    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myHRP = mychar:FindFirstChild("HumanoidRootPart")
    local myHum = mychar:FindFirstChild("Humanoid")
    
    if not myHRP or not myHum then return false end
    
    local BackUp = myHRP.CFrame
    local success = false
    
    for i = 1, 45 do
        if not targetHRP or not targetHRP.Parent then break end
        if myHum.Health <= 0 then break end
        
        myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, -3, 0)
        grab(targetHRP)
        
        if targetChar.Head:FindFirstChild("PartOwner") and targetChar.Head.PartOwner.Value == LocalPlayer.Name then
            success = true
            break
        end
        
        task.wait(0)
    end
    
    if not success then
        myHRP.CFrame = BackUp
        return false
    end
    
    myHRP.CFrame = BackUp
    
    task.defer(function()
        local startTime = tick()
        while targetChar and targetChar.Parent and targetChar.Head:FindFirstChild("PartOwner") and 
              targetChar.Head.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            
            targetHRP.CFrame = CFrame.new(PoisonPosition)
            targetHRP.Velocity = Vector3.new()
            
            task.wait()
        end
        RemoteDestroyGrabLine:FireServer(targetHRP)
    end)
    
    return true
end

function BringAllToWater()
    local successCount = 0
    local failCount = 0
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local success = BringPlayerToPoison(player.Name)
            if success then
                successCount = successCount + 1
            else
                failCount = failCount + 1
            end
            task.wait(0)
        end
    end
    
    return successCount, failCount
end

function BringAllLoop2()
    while BringAllLoop2Active do
        BringAllToWater()
        task.wait(0) 
    end
end

function Destroy_Line(Part)
    if not Part then return end
    for _, v in pairs(Part.Parent:GetDescendants()) do
        if v.Name == "PartOwner" then
            RemoteDestroyGrabLine:FireServer(v.Parent)
        end
    end
end


function FindPlayerByPartialName(partial)
    partial = string.lower(partial)
    for _, player in pairs(Players:GetPlayers()) do
        playerNameLower = string.lower(player.Name)
        playerDisplayLower = string.lower(player.DisplayName)
        if string.find(playerNameLower, partial, 1, true) or string.find(playerDisplayLower, partial, 1, true) then
            return player.Name
        end
    end
    return nil
end


LocalPlayer.Chatted:Connect(function(message)
    if IsAllowed(LocalPlayer.Name) then
        msgLower = string.lower(message)
        args = string.split(message, " ")
        
        if string.sub(msgLower, 1, 9) == "!blobkick" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = BlobKickPlayer(targetName, false)
                Rayfield:Notify({
                    Title = success and "Blobkick Success" or "Blobkick Failed",
                    Content = success and "Blobkicked " .. targetName or "Failed to blobkick " .. targetName,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            
             elseif string.sub(msgLower, 1, 9) == "!bringall" then
            Rayfield:Notify({
                Title = "Bring All Started",
                Content = "Bringing all players to poison...",
                Duration = 3,
                Image = 7743876054,
            })
            
            local successCount, failCount = BringAllToPoison()
            
            Rayfield:Notify({
                Title = "Bring All Complete",
                Content = "Success: " .. successCount .. " | Failed: " .. failCount,
                Duration = 5,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 13) == "!loopbringall" then
            BringAllLoopActive = true
            task.spawn(BringAllLoop)
            Rayfield:Notify({
                Title = "Loop Bring All Started",
                Content = "Loop bringing all players to poison",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 15) == "!unloopbringall" then
            BringAllLoopActive = false
            Rayfield:Notify({
                Title = "Loop Bring All Stopped",
                Content = "Stopped loop bringing all",
                Duration = 3,
                Image = 7743876054,
            })
        
        
        
        elseif string.sub(msgLower, 1, 9) == "!bringalltowater" then
            Rayfield:Notify({
                Title = "Bring All Started",
                Content = "Bringing all players to water...",
                Duration = 3,
                Image = 7743876054,
            })
            
            local successCount, failCount = BringAllToWater()
            
            Rayfield:Notify({
                Title = "Bring All Complete",
                Content = "Success: " .. successCount .. " | Failed: " .. failCount,
                Duration = 5,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 13) == "!loopbringtowaterall" then
            BringAllLoop2Active = true
            task.spawn(BringAllLoop2)
            Rayfield:Notify({
                Title = "Loop Bring All Started",
                Content = "Loop bringing all players to water",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 15) == "!unloopbringtowaterall" then
            BringAllLoop2Active = false
            Rayfield:Notify({
                Title = "Loop Bring All Stopped",
                Content = "Stopped loop bringing all",
                Duration = 3,
                Image = 7743876054,
            })

elseif string.sub(msgLower, 1, 6) == "!heart" then
            local targetName = LocalPlayer.Name 
            
            if #args >= 2 then
                targetName = FindPlayerByPartialName(args[2])
                if not targetName then
                    Rayfield:Notify({
                        Title = "Error",
                        Content = "Player not found: " .. args[2],
                        Duration = 3,
                        Image = 7743878857,
                    })
                    return
                end
            end
            
            ToggleHeartSparkler(targetName, not heartHighRun)
            Rayfield:Notify({
                Title = "Heart " .. (not heartHighRun and "Enabled" or "Disabled"),
                Content = "Heart sparkler " .. (not heartHighRun and "activated on " .. targetName or "deactivated"),
                Duration = 3,
                Image = 7743876054,
            })
        elseif string.sub(msgLower, 1, 9) == "!loopheart" then
            local targetName = LocalPlayer.Name
            
            if #args >= 2 then
                targetName = FindPlayerByPartialName(args[2])
                if not targetName then
                    Rayfield:Notify({
                        Title = "Error",
                        Content = "Player not found: " .. args[2],
                        Duration = 3,
                        Image = 7743878857,
                    })
                    return
                end
            end
            
            if not heartHighRun then
                ToggleHeartSparkler(targetName, true)
                Rayfield:Notify({
                    Title = "Heart Enabled",
                    Content = "Heart sparkler activated on " .. targetName .. " (loop)",
                    Duration = 3,
                    Image = 7743876054,
                })
            end
        elseif string.sub(msgLower, 1, 11) == "!unloopheart" then
            if heartHighRun then
                ToggleHeartSparkler(nil, false)
                Rayfield:Notify({
                    Title = "Heart Disabled",
                    Content = "Heart sparkler deactivated",
                    Duration = 3,
                    Image = 7743876054,
                })
            end
        elseif string.sub(msgLower, 1, 11) == "!tptopoison" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TPToPoison(targetName)
                Rayfield:Notify({
                    Title = success and " TP Poison Success" or " TP Poison Failed",
                    Content = success and "TP " .. targetName .. " to poison" or "Failed: " .. message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            elseif string.sub(msgLower, 1, 15) == "!looptptopoison" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                tptopoisonTarget = targetName
                tptopoisonLoop = true
                task.spawn(TPPoisonLoopFunc)
                Rayfield:Notify({
                    Title = "Loop TP Poison Started",
                    Content = "Loop TP " .. targetName .. " to poison",
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 17) == "!unlooptptopoison" then
            tptopoisonLoop = false
            tptopoisonTarget = nil
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped loop TP poison",
                Duration = 3,
                Image = 7743876054,
            })
            
            elseif string.sub(msgLower, 1, 10) == "!tptowater" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                TPPlayerToWater(targetName)
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "TP " .. targetName .. " to water",
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
            
            elseif string.sub(msgLower, 1, 11) == "!blobfreeze" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = ProcessBlobFreeze(targetName)
                Rayfield:Notify({
                    Title = success and " BlobFreeze Success" or " BlobFreeze Failed",
                    Content = success and "Froze " .. targetName or "Failed: " .. message,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 15) == "!loopblobfreeze" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                blobFreezeTarget = targetName
                blobFreezeLoop = true
                task.spawn(BlobFreezeLoopFunc)
                Rayfield:Notify({
                    Title = " Loop BlobFreeze Started",
                    Content = "Loop freezing " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end

        elseif string.sub(msgLower, 1, 17) == "!unloopblobfreeze" then
            blobFreezeLoop = false
            blobFreezeTarget = nil
            Rayfield:Notify({
                Title = " Command Executed",
                Content = "Stopped loop blobfreeze",
                Duration = 3,
                Image = 7743876054,
            })
            
            elseif string.sub(msgLower, 1, 13) == "!switchplayer" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                SwitchPlayer()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Switching with " .. targetName,
                    Duration = 3,
                    Image = 7743876054,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        elseif string.sub(msgLower, 1, 13) == "!loopblobkick" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = BlobKickPlayer(targetName, true)
                Rayfield:Notify({
                    Title = success and "Loop Blobkick Started" or "Loop Blobkick Failed",
                    Content = success and "Loop blobkicking " .. targetName or "Failed to start loop blobkick",
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 15) == "!unloopblobkick" then
            LoopBlobkickActive = false
            BlobkickList = {}
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped all loop blobkicks",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 7) == "!pencil" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = PencilPlayer(targetName, false)
                Rayfield:Notify({
                    Title = success and "Pencil Success" or "Pencil Failed",
                    Content = success and "Penciled " .. targetName or "Failed to pencil " .. targetName,
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 11) == "!looppencil" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                local success = PencilPlayer(targetName, true)
                Rayfield:Notify({
                    Title = success and "Loop Pencil Started" or "Loop Pencil Failed",
                    Content = success and "Loop penciling " .. targetName or "Failed to start loop pencil",
                    Duration = 3,
                    Image = success and 7743876054 or 7743878857,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 13) == "!unlooppencil" then
            LoopPencilActive = false
            PencilList = {}
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped all loop pencils",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 6) == "!bring" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                BringPlayer(targetName, false)
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "Player not found: " .. args[2],
                    Duration = 3,
                    Image = 7743878857,
                })
            end
        
        elseif string.sub(msgLower, 1, 10) == "!loopbring" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
if targetName then
SelectedPlayer = targetName
LoopBringActive = true
BringPlayer(targetName, true)
else
Rayfield:Notify({
Title = "Error",
Content = "Player not found: " .. args[2],
Duration = 3,
Image = 7743878857,
})
end
    elseif string.sub(msgLower, 1, 12) == "!unloopbring" then
        LoopBringActive = false
        Rayfield:Notify({
            Title = "Command Executed",
            Content = "Stopped loop bring",
            Duration = 3,
            Image = 7743876054,
        })
    
    elseif string.sub(msgLower, 1, 5) == "!bang" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            SelectedPlayer = targetName
            BangPlayer(targetName, false)
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 9) == "!loopbang" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            SelectedPlayer = targetName
            LoopBangActive = true
            BangPlayer(targetName, true)
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 11) == "!unloopbang" then
        LoopBangActive = false
        Rayfield:Notify({
            Title = "Command Executed",
            Content = "Stopped loop bang",
            Duration = 3,
            Image = 7743876054,
        })
    
    elseif string.sub(msgLower, 1, 4) == "!esp" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            local success, msg = ToggleESP(targetName, true)
            Rayfield:Notify({
                Title = success and "ESP Enabled" or "ESP Error",
                Content = msg,
                Duration = 3,
                Image = success and 7743876054 or 7743878857,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    
    elseif string.sub(msgLower, 1, 6) == "!noesp" and #args >= 2 then
        targetName = FindPlayerByPartialName(args[2])
        if targetName then
            local success, msg = ToggleESP(targetName, false)
            Rayfield:Notify({
                Title = success and "ESP Disabled" or "ESP Error",
                Content = msg,
                Duration = 3,
                Image = success and 7743876054 or 7743878857,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Player not found: " .. args[2],
                Duration = 3,
                Image = 7743878857,
            })
        end
    end
end
end)


function optionText(plr)
return string.format("ðŸ‘¤ @%s (%s)", plr.Name, plr.DisplayName or plr.Name)
end

function refreshDropdown()
local opts = {}
for _, p in ipairs(Players:GetPlayers()) do
if p ~= LocalPlayer then
table.insert(opts, optionText(p))
end
end
PlayerDropdown:Refresh(opts)
end

Players.PlayerAdded:Connect(function()
task.wait(0.5)
refreshDropdown()
end)

Players.PlayerRemoving:Connect(function()
task.wait(0.5)
refreshDropdown()
end)

refreshDropdown()


task.spawn(function()
while true do


    if LoopPencilActive then
        for _, playerName in pairs(PencilList) do
            CheckAndManagePencil(playerName)
        end
    end
    
    task.wait(0.5)
end


end)

Players.PlayerRemoving:Connect(function(player)
playerName = player.Name

 index = table.find(PencilList, playerName)
if index then
    table.remove(PencilList, index)
end
PencilCooldown[playerName] = nil

index2 = table.find(BlobkickList, playerName)
if index2 then
    table.remove(BlobkickList, index2)
end
BlobkickCooldown[playerName] = nil

end)

task.spawn(function()
while true do
for _, playerName in pairs(PencilList) do
local player = Players:FindFirstChild(playerName)
if player and player.InPlot.Value then
local index = table.find(PencilList, playerName)
if index then
table.remove(PencilList, index)
end
end
end

    for _, playerName in pairs(BlobkickList) do
        local player = Players:FindFirstChild(playerName)
        if player and player.InPlot.Value then
            local index = table.find(BlobkickList, playerName)
            if index then
                table.remove(BlobkickList, index)
            end
        end
    end
    
    task.wait(2)
end
end)

TestTab = Window:CreateTab("ALL", 7734058599)

AuraSettings = {
    Enabled = false,
    Range = 50,
    ScanRate = 0.2,
    LiftHeight = 20,
    LiftPower = 50,
    AutoDestroy = true,
    ShowNotifications = true,
    BringAllEnabled = false,
    WhitelistFriends = false
}

 AuraState = {
    Active = false,
    TargetsInRange = {},
    CurrentBlobman = nil,
    ProcessingTarget = false,
    TimerStart = nil,
    TimerActive = false,
    BlackHoleTracking = {}
}

 BringState = {
    Active = false,
    Queue = {},
    SavedPos = nil,
    SavedCamCFrame = nil,
    Radius = 100,
    Connection = nil
}

GameEvents = {
    DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy"),
    SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
    DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
}

camBlocker = Instance.new("Part", Workspace)
camBlocker.Anchored = true
camBlocker.CanCollide = false
camBlocker.Transparency = 1
camBlocker.CanQuery = false
camBlocker.Size = Vector3.new(10,10,10)

 function freezeCamera()
    local cam = Workspace.CurrentCamera
    camBlocker.CFrame = BringState.SavedCamCFrame
    camBlocker.Parent = Workspace
    cam.CameraType = Enum.CameraType.Scriptable
    cam.CFrame = BringState.SavedCamCFrame
end

function unfreezeCamera()
    camBlocker.Parent = nil
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    if BringState.SavedCamCFrame then
        cam.CFrame = BringState.SavedCamCFrame
    end
    BringState.SavedCamCFrame = nil
end

function disableCollisions(character)
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end

function inPlot(player)
    local plots = Workspace:FindFirstChild("PlotItems")
    local playersInPlots = plots and plots:FindFirstChild("PlayersInPlots")
    return playersInPlots and playersInPlots:FindFirstChild(player.Name)
end

 function inRadius(part)
    return (part.Position - BringState.SavedPos).Magnitude <= BringState.Radius
end

 function shouldIgnore(player)
    if player == LocalPlayer then return true end
    if AuraSettings.WhitelistFriends and LocalPlayer:IsFriendsWith(player.UserId) then
        return true
    end
    return false
end

 function refreshQueue()
    BringState.Queue = {}
    for _, player in pairs(Players:GetPlayers()) do
        if not shouldIgnore(player) and player.Character and not inPlot(player) then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if root and not inRadius(root) then
                table.insert(BringState.Queue, player)
            end
        end
    end
end

 function processNext()
    if #BringState.Queue == 0 then
        refreshQueue()
        if #BringState.Queue == 0 then return end
    end
    local target = BringState.Queue[1]
    table.remove(BringState.Queue, 1)
    if not target or not target.Character then return end
    local root = target.Character:FindFirstChild("HumanoidRootPart")
    local head = target.Character:FindFirstChild("Head")
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if root and head and localRoot then
        LocalPlayer.Character:PivotTo(root.CFrame * CFrame.new(0, -6, 0))
        disableCollisions(LocalPlayer.Character)
        local tries = 0
        repeat
            GameEvents.SetNetworkOwner:FireServer(root, localRoot.CFrame)
            task.wait(0)
            tries = tries + 2
            if tries > 40 then break end
        until (head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name) or not BringState.Active

        if BringState.Active and head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name then
            root.CFrame = CFrame.new(BringState.SavedPos)
            root.Position = BringState.SavedPos
            root.AssemblyLinearVelocity = Vector3.zero
            task.wait(0)
        end
    end
end

 function startBringAll()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end
    BringState.SavedPos = localRoot.Position
    BringState.SavedCamCFrame = Workspace.CurrentCamera.CFrame
    refreshQueue()
    freezeCamera()
    BringState.Connection = RunService.Heartbeat:Connect(function()
        if BringState.Active then
            processNext()
            if BringState.SavedCamCFrame then
                local cam = Workspace.CurrentCamera
                cam.CameraType = Enum.CameraType.Scriptable
                cam.CFrame = BringState.SavedCamCFrame
                camBlocker.CFrame = BringState.SavedCamCFrame
                camBlocker.Parent = Workspace
            end
        end
    end)
end

 function stopBringAll()
    if BringState.Connection then
        BringState.Connection:Disconnect()
        BringState.Connection = nil
    end
    unfreezeCamera()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if localRoot and BringState.SavedPos then
        localRoot.AssemblyLinearVelocity = Vector3.zero
        localRoot.CFrame = CFrame.new(BringState.SavedPos)
    end
end

TestTab:CreateToggle({
    Name = 'WHITELIST FRIENDS <font face="GothamBlack" color="rgb(160, 255, 0)">[STOPS FRIENDS FROM BEING LOOPED.]</font>',
    CurrentValue = false,
    Flag = "WhitelistFriends",
    Callback = function(value)
        AuraSettings.WhitelistFriends = value
    end
})

TestTab:CreateToggle({
    Name = 'BRING ALL <font face="GothamBlack" color="rgb(160, 255, 0)">[BEST]</font>',
    CurrentValue = false,
    Flag = "BringAllToggle",
    Callback = function(value)
        BringState.Active = value
        if value then
            startBringAll()
            Rayfield:Notify({
                Title = "BRING ALL ON",
                Content = "System Activated!",
                Duration = 3,
                Image = 4483345998
            })
        else
            stopBringAll()
            Rayfield:Notify({
                Title = "BRING ALL OFF",
                Content = "System Deactivated",
                Duration = 2,
                Image = 4483345998
            })
        end
    end
})

 function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.Died:Connect(function()
        if BringState.Connection then
            BringState.Connection:Disconnect()
            BringState.Connection = nil
        end
        unfreezeCamera()
    end)
    if BringState.Active then
        local localRoot = character:WaitForChild("HumanoidRootPart")
        BringState.SavedPos = BringState.SavedPos or localRoot.Position
        BringState.SavedCamCFrame = BringState.SavedCamCFrame or Workspace.CurrentCamera.CFrame
        freezeCamera()
        BringState.Connection = RunService.Heartbeat:Connect(function()
            if BringState.Active then
                processNext()
                if BringState.SavedCamCFrame then
                    local cam = Workspace.CurrentCamera
                    cam.CameraType = Enum.CameraType.Scriptable
                    cam.CFrame = BringState.SavedCamCFrame
                    camBlocker.CFrame = BringState.SavedCamCFrame
                    camBlocker.Parent = Workspace
                end
            end
        end)
    end
end

LocalPlayer.CharacterAdded:Connect(onCharacterAdded)

Players.PlayerAdded:Connect(function()
    if BringState.Active then refreshQueue() end
end)

Players.PlayerRemoving:Connect(function(p)
    for i = #BringState.Queue, 1, -1 do
        if BringState.Queue[i] == p then
            table.remove(BringState.Queue, i)
        end
    end
    AuraState.GrabbedPlayers[p.UserId] = nil
end)

TestTab:CreateSection("KICK ALL / OTHER")

Players = game:GetService("Players")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
plr = Players.LocalPlayer

 delayBetweenPlayers = 0
 whitelistFriends = true

 GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
 SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
 DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")



 function findMountedBlobman()
    local char = plr.Character
    if not char then return nil end
    local hum = char:FindFirstChild("Humanoid")
    if not hum or not hum.SeatPart then return nil end
    
    local parent = hum.SeatPart.Parent
    if parent and parent.Name == "CreatureBlobman" then
        return parent
    end
    return nil
end

local function isValidTarget(player)
    if player == plr then return false end
    
    if whitelistFriends and plr:IsFriendsWith(player.UserId) then return false end
    
    local inPlot = player:FindFirstChild("InPlot")
    if inPlot and inPlot.Value == true then return false end
    
    local char = player.Character
    if not char then return false end
    
    local hum = char:FindFirstChild("Humanoid")
    if not hum or hum.Health <= 0 then return false end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    
    return true
end

function kickPlayer(target, blobman, detector, weld, creatureGrab, creatureDrop)
    local targetChar = target.Character
    if not targetChar then return false end
    
    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
    if not targetHRP then return false end
    
    local myChar = plr.Character
    if not myChar then return false end
    
    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
    if not myHRP then return false end
    
    local blobHum = blobman:FindFirstChild("Humanoid")
    if blobHum then
        blobHum.AutoRotate = false
    end
    
    local behindPos = targetHRP.CFrame * CFrame.new(0, 1, 5)
    myChar:PivotTo(CFrame.lookAt(behindPos.Position, targetHRP.Position))
    
    if not targetHRP.Parent then return false end
    

    for i = 1, 30 do
        pcall(function()
            SetNetworkOwner:FireServer(targetHRP, targetHRP.CFrame)
            targetHRP.CFrame = targetHRP.CFrame + Vector3.new(0, 20, 0)
            DestroyGrabLine:FireServer(targetHRP)
            creatureGrab:FireServer(detector, myHRP, weld)
            task.wait(0)
            creatureGrab:FireServer(detector, targetHRP, weld)
            creatureDrop:FireServer(weld)
        end)
        task.wait(0)
    end
    
    return true
end

TestTab:CreateButton({
    Name = "KICK ALL",
    Callback = function()
        local myChar = plr.Character
        if not myChar then return end
        
        local blobman = findMountedBlobman()
        if not blobman then
 
            return
        end
        
        local detector = blobman:FindFirstChild("LeftDetector")
        local weld = detector and detector:FindFirstChild("LeftWeld")
        local script = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
        local creatureGrab = script and script:FindFirstChild("CreatureGrab")
        local creatureDrop = script and script:FindFirstChild("CreatureDrop")
        
        if not (detector and weld and creatureGrab and creatureDrop) then
            return
        end
        
 
        local oldCF = myChar:GetPivot()
        local count = 0
        
        
        for _, target in ipairs(Players:GetPlayers()) do
            if not findMountedBlobman() then
                break
            end
            
            if isValidTarget(target) then
                local success = kickPlayer(target, blobman, detector, weld, creatureGrab, creatureDrop)
                if success then
                    count = count + 5
                end
                task.wait(delayBetweenPlayers)
            end
        end
        
        pcall(function()
            myChar:PivotTo(oldCF)
        end)

    end    
})

TestTab:CreateToggle({
    Name = "WHITELIST FRIENDS",
    Default = true,
    Callback = function(Value)
        whitelistFriends = Value
    end    
})

 espEnabled = false
	local espBoxes = {}
	local targetNames = {"partesp", "playercharacterlocationdetector"}
	local function IsTarget(obj)
		if not obj:IsA("BasePart") then return false end
		for _, name in ipairs(targetNames) do if string.lower(obj.Name) == string.lower(name) then return true end end
		return false
	end
	local function AddBoxESP(obj)
		if espBoxes[obj] then return end
		local box = Instance.new("BoxHandleAdornment")
		box.Adornee = obj
		box.AlwaysOnTop = true
		box.ZIndex = 5
		box.Color3 = Color3.fromRGB(255, 255, 255)
		box.Transparency = 0.5
		box.Size = obj.Size
		box.Parent = game.CoreGui
		espBoxes[obj] = box
		obj.AncestryChanged:Connect(function(_, parent)
			if not parent and espBoxes[obj] then espBoxes[obj]:Destroy() espBoxes[obj] = nil end
		end)
	end
 function RemoveAllBoxes()
		for obj, box in pairs(espBoxes) do if box then box:Destroy() end end
		espBoxes = {}
	end
	 function Scan()
		for _, obj in ipairs(workspace:GetDescendants()) do if espEnabled and IsTarget(obj) then AddBoxESP(obj) end end
	end
	workspace.DescendantAdded:Connect(function(obj) if espEnabled and IsTarget(obj) then AddBoxESP(obj) end end)

	TestTab:CreateToggle({
    Name = "ESP BOX VIEW",
		Default = false,
		Callback = function(Value)
			espEnabled = Value
			if espEnabled then Scan() else RemoveAllBoxes() end
		end
	})
	
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local Player = Players.LocalPlayer

local SpinEnabled = false
local SpinSpeed = 5
local SpinConnection = nil

TestTab:CreateToggle({
	Name = "SPIN CHARACTER",
	CurrentValue = false,
	Flag = "SpinCharacter",
	Callback = function(Value)
		SpinEnabled = Value

		if Value then
			if SpinConnection then SpinConnection:Disconnect() end

			SpinConnection = RunService.Heartbeat:Connect(function()
				local character = Player.Character
				local root = character and character:FindFirstChild("HumanoidRootPart")
				if root then
					root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(SpinSpeed), 0)
				end
			end)
		else
			if SpinConnection then
				SpinConnection:Disconnect()
				SpinConnection = nil
			end
		end
	end,
})

TestTab:CreateSlider({
	Name = "SPIN SPEED",
	Range = {1, 10000},
	Increment = 1,
	Suffix = "DEG",
	CurrentValue = 5,
	Flag = "SPIN SPEED",
	Callback = function(Value)
		SpinSpeed = Value
	end,
})

 Players = game:GetService("Players")
 Player = Players.LocalPlayer
 Workspace = workspace

 dick2 = Instance.new("Folder")
dick2.Parent = Workspace
dick2.Name = "BinisHoles"

 gettingkicked = {}
 VOBBC = false

function getClosestPlayer(pos)
    local closest = nil
    local dist = math.huge
    for _, plr in pairs(Players:GetPlayers()) do
        local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            local d = (hrp.Position - pos).Magnitude
            if d < dist then
                dist = d
                closest = plr
            end
        end
    end
    return closest
end

workspace.ChildAdded:Connect(function(model)
    if model.Name == "BlackHoleKick" then
        local hole = model:WaitForChild("Hole", 5)
        if not hole then return end
        
        local diddler
        local victimPlayer = nil
        
        task.spawn(function()
            task.wait(0.1)
            
            diddler = model:Clone()
            diddler.Parent = dick2
            diddler.Name = "Unknown's BlackHole"
            
            if not VOBBC then
                if diddler:FindFirstChild("Hole") then
                    diddler.Hole.Transparency = 1
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = false
                    end
                end
            end
            
            local starttime = tick()
            while diddler and diddler.Parent do
                if diddler:FindFirstChild("Hole") and diddler.Hole:FindFirstChild("BillboardGui") then
                    local billboardGui = diddler.Hole.BillboardGui
                    if billboardGui:FindFirstChild("Large") then
                        billboardGui.Large.Rotation = (tick() - starttime) * 150
                    end
                    if billboardGui:FindFirstChild("Small") then
                        billboardGui.Small.Rotation = (tick() - starttime) * 150
                    end
                end
                task.wait()
            end
        end)
        
        local solved = false
        while not solved and model and model.Parent do
            for _, plr in pairs(Players:GetPlayers()) do
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = plr.Character.HumanoidRootPart
                    
                    if hrp.Anchored == true then
                        solved = true
                        
                        local alreadyFlagged = false
                        for _, id in pairs(gettingkicked) do
                            if id == plr.UserId then
                                alreadyFlagged = true
                                break
                            end
                        end
                        
                        if not alreadyFlagged then
                            victimPlayer = plr
                            table.insert(gettingkicked, plr.UserId)
                            
                            if diddler then
                                diddler.Name = plr.Name .. "'s BlackHole"
                            end
                            
                            local hpp = hrp.CFrame.Position
                            local bhk = hole.CFrame.Position
                            local distance = (hpp - bhk).Magnitude
                            local byPlayer = distance > 2
                            
                            local notifContent = string.format(
                                "%s (@%s) has been kicked%s at position (%d, %d, %d)",
                                plr.DisplayName,
                                plr.Name,
                                byPlayer and " by a player" or "",
                                math.floor(hole.CFrame.Position.X),
                                math.floor(hole.CFrame.Position.Y),
                                math.floor(hole.CFrame.Position.Z)
                            )
                            
                            Rayfield:Notify({
                                Title = "BlackHole Kick",
                                Content = notifContent,
                                Duration = 0.5,
                                Image = 4483362458
                            })
                            
                            task.spawn(function()
                                task.wait(5)
                                for i, id in pairs(gettingkicked) do
                                    if id == plr.UserId then
                                        table.remove(gettingkicked, i)
                                        break
                                    end
                                end
                            end)
                        end
                        break
                    end
                end
            end
            task.wait()
        end
    end
end)


 ViewBlackHolesToggle = TestTab:CreateToggle({
    Name = "VIEW OFFLINE BLACKHOLES",
    CurrentValue = false,
    Flag = "ViewBlackHoles",
    Callback = function(Value)
        VOBBC = Value
        
        if VOBBC then
            for _, diddler in pairs(dick2:GetChildren()) do
                if diddler:FindFirstChild("Hole") then
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = true
                    end
                    
                    for _, prt in pairs(diddler:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.Transparency = 0
                            if prt.Name == "HumanoidRootPart" then
                                prt.Transparency = 1
                            elseif prt.Name == "Hole" then
                                prt.Transparency = 0.25
                            end
                        end
                    end
                end
            end
            
            Rayfield:Notify({
                Title = "BLACKHOLE VIEWER",
                Content = "OFFLINE BLACKHOLES ARE NOW VISIBLE",
                Duration = 0.5,
                Image = 4483362458
            })
        else
            for _, diddler in pairs(dick2:GetChildren()) do
                if diddler:FindFirstChild("Hole") then
                    if diddler.Hole:FindFirstChild("BillboardGui") then
                        diddler.Hole.BillboardGui.Enabled = false
                    end
                    
                    for _, prt in pairs(diddler:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.Transparency = 1
                        end
                    end
                end
            end
            
            Rayfield:Notify({
                Title = "BLACKHOLE VIEWER",
                Content = "OFFLINE BLACKHOLES ARE NOW HIDDEN",
                Duration = 0.5,
                Image = 4483362458
            })
        end
    end,
})

 ClearBlackHolesButton = TestTab:CreateButton({
    Name = "CLEAR ALL BLACKHOLES",
    Callback = function()
        local count = #dick2:GetChildren()
        dick2:ClearAllChildren()
        
        Rayfield:Notify({
            Title = "BLACKHOLE VIEWER",
            Content = string.format("Cleared %d BlackHole(s)", count),
            Duration = 0.5,
            Image = 4483362458
        })
    end,
})

local CommandsTab = Window:CreateTab("ADMIN COMMANDS", 1603736)

local CommandsParagraph = CommandsTab:CreateParagraph({
    Title = "ALL CHAT COMMANDS",
    Content = [[
📜 COMMAND LIST

This section explains all available commands, what they do, and how to use them correctly.

🧲 BRING COMMANDS

These commands teleport specific players to your location.
!bring <player>
Instantly teleports the selected player to you once.
!loopbring <player>
Continuously teleports the selected player to you.
The player will keep being brought until you stop it.
!unloopbring <player>
Stops the loop bring effect for that player.

🧲 BRING ALL COMMANDS

These commands affect every player in the server.
!bringall
Instantly teleports all players to you one time.
!loopbringall
Continuously teleports all players to you.
New players who join may also be affected (depending on script behavior).
!unloopbringall
Stops the loop bring effect for everyone.

✏️ PENCIL COMMANDS

These commands require a spawned Pencil to function.
!pencil <player>
Applies the pencil effect to the selected player.
Requires at least one Pencil to be spawned and grabbed.
!looppencil <player>
Continuously applies the pencil effect to the player.
!unlooppencil <player>
Stops the looping pencil effect.

🟣 BLOBMAN COMMANDS

These commands require a Blobman and specific conditions.
!blobkick <player>
Uses a Blobman to kick the target player.
The target must:
Be outside house barriers
Not be seated (no blob, tractor, or vehicle)
!loopblobkick <player>
Continuously kicks the selected player.
!unloopblobkick <player>
Stops the looping blob kick effect.

❄️ FREEZE COMMANDS (BLOB)

!blobfreeze <player>
Freezes the target player using a Blobman.
Requirements:
Target must be outside house barriers
Target must not be seated on anything
!loopblobfreeze <player>
Continuously freezes the selected player.
!unloopblobfreeze <player>
Stops the looping freeze effect.

💖 HEART EFFECT COMMANDS

These commands require a Sparkler.
!heart <player>
Applies a heart visual effect to the target player.
Requires a spawned and grabbed Sparkler.
!loopheart <player>
Continuously applies the heart effect.
!unloopheart <player>
Stops the looping heart effect.

🌊 WATER & POSITION COMMANDS

!tptowater <player>
Teleports the selected player directly into water.
!switchplayer <player>
Swaps your position with the target player instantly.

☠️ POISON TELEPORT COMMANDS

!tptopoison <player>
Teleports the selected player into poison once.
!looptptopoison <player>
Continuously teleports the player into poison.
!unlooptptopoison <player>
Stops the looping poison teleport.

⚙️ HOW THE COMMANDS ✏️ PENCIL & 💖 HEART COMMANDS WORK

Spawn one Sparkler or Pencil and grab it before using commands.
If it doesn’t work immediately, run the command again.
Spawning more than one tool is normal.

🟣 BLOB COMMANDS

Spawn one Blobman and sit on it.
Make sure:
The target is outside house barriers
The target is not seated
If the command stops working:
Delete the Blobman
Spawn a new one and try again

🔁 LOOP COMMANDS

Any command starting with !loop will repeat continuously
You must use the matching !unloop command to stop it

🚀 TELEPORT COMMANDS

Teleports are instant
Loop versions will keep teleporting the player until stopped

🔄 SWITCH PLAYER COMMANDS

Instantly swaps positions between you and the target player
No tools or setup required]]})

Rayfield:LoadConfiguration()