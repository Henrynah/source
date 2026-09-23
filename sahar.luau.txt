local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"

loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()

local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()

local Options = Library.Options
local Toggles = Library.Toggles

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")
local SoundService = game:GetService("SoundService")
local CoreGui = game:GetService("CoreGui")
local TeleportService = game:GetService("TeleportService")

local plr = Players.LocalPlayer

pcall(function() plr.PlayerScripts.CharacterAndBeamMove.Enabled = false end, function() end)
pcall(function() plr.PlayerScripts.StickyPartsTouchDetection.Enabled = false end, function() end)

local spawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
local destroyToyRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")

local Window = Library:CreateWindow({
    Title = "SAHAR ",
    Center = true,
    AutoShow = true,
    TabPadding = 8,
    MenuFadeTime = 0.2
})

local devsLuaSign = Instance.new("Part")
devsLuaSign.Name = "SAHAR_DevsLuaSign"
devsLuaSign.Size = Vector3.new(10, 10, 1)
devsLuaSign.Position = Vector3.new(-271, 59, -455)
devsLuaSign.Anchored = true
devsLuaSign.CanCollide = false
devsLuaSign.Transparency = 1
devsLuaSign.Parent = workspace

local devsLuaBillboard = Instance.new("BillboardGui")
devsLuaBillboard.Name = "SAHAR_DevsLuaBillboard"
devsLuaBillboard.Size = UDim2.new(10, 0, 10, 0)
devsLuaBillboard.StudsOffset = Vector3.new(0, 0, 0)
devsLuaBillboard.Adornee = devsLuaSign
devsLuaBillboard.AlwaysOnTop = true
devsLuaBillboard.Parent = devsLuaSign

local devsLuaLabel = Instance.new("TextLabel")
devsLuaLabel.Size = UDim2.new(1, 0, 1, 0)
devsLuaLabel.BackgroundTransparency = 1
devsLuaLabel.Text = "Devs_lua"
devsLuaLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
devsLuaLabel.TextScaled = true
devsLuaLabel.Font = Enum.Font.GothamBold
devsLuaLabel.TextStrokeTransparency = 0.5
devsLuaLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
devsLuaLabel.Parent = devsLuaBillboard

Library:OnUnload(function()
    if devsLuaSign then devsLuaSign:Destroy() end
end)

local mouse = plr:GetMouse()

local catCursorGui = Instance.new("ScreenGui")
catCursorGui.Name = "SAHAR_CatCursor"
catCursorGui.IgnoreGuiInset = true
catCursorGui.ResetOnSpawn = false
catCursorGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
catCursorGui.DisplayOrder = 2147483647
catCursorGui.Parent = CoreGui

local catCursor = Instance.new("Frame")
catCursor.Name = "Paw"
catCursor.BackgroundTransparency = 1
catCursor.Size = UDim2.fromOffset(30, 30)
catCursor.AnchorPoint = Vector2.new(0.15, 0.1)
catCursor.ZIndex = 2147483647
catCursor.Parent = catCursorGui

local function makePawPart(size, pos, color)
    local part = Instance.new("Frame")
    part.BorderSizePixel = 0
    part.BackgroundColor3 = color
    part.Size = size
    part.Position = pos
    part.ZIndex = 2147483647
    part.Parent = catCursor

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0)
    corner.Parent = part

    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(50, 140, 130)
    stroke.Thickness = 1.1
    stroke.Parent = part

    return part
end

makePawPart(UDim2.fromOffset(8, 8), UDim2.fromOffset(2, 3), Color3.fromRGB(169, 255, 226))
makePawPart(UDim2.fromOffset(8, 8), UDim2.fromOffset(10, 0), Color3.fromRGB(169, 255, 226))
makePawPart(UDim2.fromOffset(8, 8), UDim2.fromOffset(18, 3), Color3.fromRGB(169, 255, 226))
makePawPart(UDim2.fromOffset(7, 7), UDim2.fromOffset(9, 8), Color3.fromRGB(169, 255, 226))

local pad = makePawPart(UDim2.fromOffset(16, 14), UDim2.fromOffset(7, 13), Color3.fromRGB(135, 252, 245))
local padCorner = pad:FindFirstChildOfClass("UICorner")
if padCorner then
    padCorner.CornerRadius = UDim.new(0.6, 0)
end

Library.ShowCustomCursor = false
UserInputService.MouseIconEnabled = false
mouse.Icon = ""

local catCursorDown = false

local forceMouseOffConn = RunService.RenderStepped:Connect(function()
    UserInputService.MouseIconEnabled = false
    mouse.Icon = ""

    local pos = UserInputService:GetMouseLocation()
    catCursor.Position = UDim2.fromOffset(pos.X, pos.Y)
    catCursor.Rotation = catCursorDown and -12 or 0
    catCursor.Size = catCursorDown and UDim2.fromOffset(27, 27) or UDim2.fromOffset(30, 30)
    catCursor.Visible = true
end)

local mouseIconChangedConn = UserInputService:GetPropertyChangedSignal("MouseIconEnabled"):Connect(function()
    if UserInputService.MouseIconEnabled then
        UserInputService.MouseIconEnabled = false
    end
end)

local catCursorInputBeganConn = UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        catCursorDown = true
    end
end)

local catCursorInputEndedConn = UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        catCursorDown = false
    end
end)

Library:OnUnload(function()
    UserInputService.MouseIconEnabled = true
    mouse.Icon = ""

    if forceMouseOffConn then forceMouseOffConn:Disconnect() end
    if mouseIconChangedConn then mouseIconChangedConn:Disconnect() end
    if catCursorInputBeganConn then catCursorInputBeganConn:Disconnect() end
    if catCursorInputEndedConn then catCursorInputEndedConn:Disconnect() end
    if catCursorGui then catCursorGui:Destroy() end
end)

local statsGui = Instance.new("ScreenGui")
statsGui.Name = "SAHAR_Stats"
statsGui.IgnoreGuiInset = true
statsGui.ResetOnSpawn = false
statsGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
statsGui.DisplayOrder = 2147483646
statsGui.Parent = CoreGui

local statsFrame = Instance.new("Frame")
statsFrame.Size = UDim2.new(0, 140, 0, 70)
statsFrame.Position = UDim2.new(0.5, -70, 0, 5)
statsFrame.BackgroundColor3 = Color3.fromRGB(10, 40, 38)
statsFrame.BackgroundTransparency = 0.3
statsFrame.BorderSizePixel = 0
statsFrame.Active = true
statsFrame.Draggable = true
statsFrame.Parent = statsGui

local statsCorner = Instance.new("UICorner")
statsCorner.CornerRadius = UDim.new(0, 6)
statsCorner.Parent = statsFrame

local statsStroke = Instance.new("UIStroke")
statsStroke.Color = Color3.fromRGB(135, 252, 245)
statsStroke.Thickness = 1
statsStroke.Transparency = 0.5
statsStroke.Parent = statsFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, 0, 0, 15)
titleLabel.Position = UDim2.new(0, 0, 0, 2)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "SAHAR"
titleLabel.TextColor3 = Color3.fromRGB(135, 252, 245)
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 11
titleLabel.Parent = statsFrame

local fpsLabel = Instance.new("TextLabel")
fpsLabel.Size = UDim2.new(1, -10, 0, 15)
fpsLabel.Position = UDim2.new(0, 5, 0, 18)
fpsLabel.BackgroundTransparency = 1
fpsLabel.Text = "FPS: ..."
fpsLabel.TextColor3 = Color3.fromRGB(169, 255, 226)
fpsLabel.Font = Enum.Font.Gotham
fpsLabel.TextSize = 12
fpsLabel.TextXAlignment = Enum.TextXAlignment.Left
fpsLabel.Parent = statsFrame

local pingLabel = Instance.new("TextLabel")
pingLabel.Size = UDim2.new(1, -10, 0, 15)
pingLabel.Position = UDim2.new(0, 5, 0, 34)
pingLabel.BackgroundTransparency = 1
pingLabel.Text = "PING: ..."
pingLabel.TextColor3 = Color3.fromRGB(169, 255, 226)
pingLabel.Font = Enum.Font.Gotham
pingLabel.TextSize = 12
pingLabel.TextXAlignment = Enum.TextXAlignment.Left
pingLabel.Parent = statsFrame

local grabLabel = Instance.new("TextLabel")
grabLabel.Size = UDim2.new(1, -10, 0, 15)
grabLabel.Position = UDim2.new(0, 5, 0, 50)
grabLabel.BackgroundTransparency = 1
grabLabel.Text = "GRAB: nobody"
grabLabel.TextColor3 = Color3.fromRGB(169, 255, 226)
grabLabel.Font = Enum.Font.Gotham
grabLabel.TextSize = 12
grabLabel.TextXAlignment = Enum.TextXAlignment.Left
grabLabel.TextTruncate = Enum.TextTruncate.AtEnd
grabLabel.Parent = statsFrame

local Stats = game:GetService("Stats")
local frameCount = 0
local lastTime = tick()
local lastGrabberText = "nobody"

RunService.RenderStepped:Connect(function()
    frameCount = frameCount + 1
end)

task.spawn(function()
    while true do
        task.wait(0.5)
        local now = tick()
        local delta = now - lastTime
        local currentFPS = math.floor(frameCount / delta)
        frameCount = 0
        lastTime = now

        local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())

        fpsLabel.Text = "FPS: " .. tostring(currentFPS)
        pingLabel.Text = "PING: " .. tostring(ping) .. "ms"
    end
end)

task.spawn(function()
    while true do
        task.wait(0.15)

        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChild("Humanoid")

        if hrp then
            local foundGrabber = nil

            local function CheckOwnerValue(ownerValue)
                if not ownerValue then return nil end
                if ownerValue:IsA("StringValue") then
                    local ownerName = ownerValue.Value
                    if ownerName and ownerName ~= "" and ownerName ~= plr.Name then
                        local grabber = Players:FindFirstChild(ownerName)
                        if grabber then
                            return grabber.DisplayName .. "/" .. grabber.Name
                        else
                            return ownerName
                        end
                    end
                elseif ownerValue:IsA("ObjectValue") then
                    local ownerPlayer = ownerValue.Value
                    if ownerPlayer and ownerPlayer:IsA("Player") and ownerPlayer ~= plr then
                        return ownerPlayer.DisplayName .. "/" .. ownerPlayer.Name
                    end
                end
                return nil
            end

            local function CheckObject(obj)
                local ownerValue = obj:FindFirstChild("PartOwner") or obj:FindFirstChild("SetOwner") or obj:FindFirstChild("Owner") or obj:FindFirstChild("NetworkOwner")
                local result = CheckOwnerValue(ownerValue)
                if result then return result end
                return nil
            end

            for _, child in pairs(hrp:GetChildren()) do
                local result = CheckObject(child)
                if result then foundGrabber = result break end

                if child:IsA("Weld") or child:IsA("RopeConstraint") or child:IsA("RodConstraint") or child:IsA("Attachment") then
                    local result2 = CheckObject(child)
                    if result2 then foundGrabber = result2 break end

                    for _, subChild in pairs(child:GetChildren()) do
                        local result3 = CheckObject(subChild)
                        if result3 then foundGrabber = result3 break end
                    end
                end
            end

            if not foundGrabber then
                for _, part in pairs(char:GetChildren()) do
                    if part:IsA("BasePart") then
                        for _, child in pairs(part:GetChildren()) do
                            local result = CheckObject(child)
                            if result then foundGrabber = result break end

                            if child:IsA("Weld") or child:IsA("RopeConstraint") or child:IsA("RodConstraint") then
                                local result2 = CheckObject(child)
                                if result2 then foundGrabber = result2 break end
                            end
                        end
                        if foundGrabber then break end
                    end
                end
            end

            if not foundGrabber then
                for _, descendant in pairs(hrp:GetDescendants()) do
                    local result = CheckObject(descendant)
                    if result then foundGrabber = result break end
                end
            end

            if not foundGrabber then
                local isHeldVal = plr:FindFirstChild("IsHeld")
                local isGrabbed = false

                if isHeldVal and isHeldVal.Value then
                    isGrabbed = true
                end

                if not isGrabbed and hum and hum.PlatformStand then
                    isGrabbed = true
                end

                if not isGrabbed then
                    for _, child in pairs(hrp:GetChildren()) do
                        if child:IsA("RopeConstraint") or child:IsA("RodConstraint") then
                            isGrabbed = true
                            break
                        end
                    end
                end

                if not isGrabbed then
                    for _, child in pairs(hrp:GetChildren()) do
                        if child:IsA("Weld") and child.Name ~= "RootJoint" and child.Name ~= "RootWeld" then
                            if child.Part1 and not child.Part1:IsDescendantOf(char) then
                                isGrabbed = true
                                break
                            end
                        end
                    end
                end

                if isGrabbed then
                    local closest, closestDist = nil, math.huge
                    for _, p in pairs(Players:GetPlayers()) do
                        if p ~= plr and p.Character then
                            local phrp = p.Character:FindFirstChild("HumanoidRootPart")
                            if phrp then
                                local dist = (hrp.Position - phrp.Position).Magnitude
                                if dist < closestDist then
                                    closestDist = dist
                                    closest = p
                                end
                            end
                        end
                    end
                    if closest and closestDist < 50 then
                        foundGrabber = closest.DisplayName .. "/" .. closest.Name
                    end
                end
            end

            if foundGrabber then
                lastGrabberText = foundGrabber
            end
        end

        pcall(function()
            grabLabel.Text = "GRAB: " .. lastGrabberText
        end)
    end
end)

Library:OnUnload(function()
    if statsGui then statsGui:Destroy() end
end)

local Tabs = {
    Info = Window:AddTab("Info"),
    Defense = Window:AddTab("Defense"),
    Kick = Window:AddTab("Kick"),
    Blobman = Window:AddTab("Blobman"),
    Visuals = Window:AddTab("Visuals"),
    Fun = Window:AddTab("Fun"),
    Misc = Window:AddTab("Misc"),
    Player = Window:AddTab("Player"),
    ServerLags = Window:AddTab("Server Lags"),
    Auras = Window:AddTab("+1000 aura"),
    Detect = Window:AddTab("Detect"),
    Settings = Window:AddTab("Settings")
}

local function GetPlayerList()
    local list = {}
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= plr then
            table.insert(list, p.DisplayName .. "/" .. p.Name)
        end
    end
    return list
end

local function GetTargetFromString(str)
    if not str then return nil end
    local targetName = str:match("/(.+)$") or str
    return Players:FindFirstChild(targetName)
end

local InfoLeft = Tabs.Info:AddLeftGroupbox("Welcome")
local InfoRight = Tabs.Info:AddRightGroupbox("Info")

InfoLeft:AddLabel("Player: " .. plr.DisplayName .. " / " .. plr.Name)
InfoLeft:AddDivider()
InfoLeft:AddLabel("This script was made for people")
InfoLeft:AddLabel("who can't afford normal scripts")
InfoLeft:AddLabel("but really want to learn HvH.")
InfoLeft:AddDivider()
InfoLeft:AddLabel("Credits:")
InfoLeft:AddLabel("rip / oreo_vkisnoebludo")
InfoLeft:AddLabel("telegram: SLRcheck")
InfoLeft:AddLabel("discord: 10women")

InfoRight:AddDivider()
InfoRight:AddButton("Rejoin Server", function()
    TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, plr)
end)

local DefenseLeft = Tabs.Defense:AddLeftGroupbox("Defense")
local DefenseRight = Tabs.Defense:AddRightGroupbox("Bor Defense")

local KickLeft = Tabs.Kick:AddLeftGroupbox("Target")
local KickRight = Tabs.Kick:AddRightGroupbox("Kick Actions")

local BlobmanLeft = Tabs.Blobman:AddLeftGroupbox("Blobman")
local BlobmanRight = Tabs.Blobman:AddRightGroupbox("Blob Kill")

local FunLeft = Tabs.Fun:AddLeftGroupbox("Fun")
local FunRight = Tabs.Fun:AddRightGroupbox("Animations")

local MiscLeft = Tabs.Misc:AddLeftGroupbox("World")
local MiscRight = Tabs.Misc:AddRightGroupbox("ESP")

local PlayerLeft = Tabs.Player:AddLeftGroupbox("Movement")
local PlayerRight = Tabs.Player:AddRightGroupbox("Camera")

local ServerLagsLeft = Tabs.ServerLags:AddLeftGroupbox("Line Lag")
local ServerLagsRight = Tabs.ServerLags:AddRightGroupbox("Packet Lag")

local antiExplodeT = false
DefenseLeft:AddToggle("AntiExplodeToggle", {
    Text = "Anti Explode", Default = false,
    Callback = function(Value)
        antiExplodeT = Value
        if not Value then return end
        local conn
        conn = workspace.ChildAdded:Connect(function(model)
            if not antiExplodeT then conn:Disconnect() return end
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp and model.Name == "Part" then
                local mag = (model.Position - hrp.Position).Magnitude
                if mag <= 20 then
                    hrp.Anchored = true
                    task.wait(0.05)
                    if char:FindFirstChild("Right Arm") and char["Right Arm"]:FindFirstChild("RagdollLimbPart") then
                        while char["Right Arm"].RagdollLimbPart.CanCollide do task.wait(0.01) end
                    end
                    hrp.Anchored = false
                end
            end
        end)
    end
})

local antiStruggleActive = false
DefenseLeft:AddToggle("AntiStruggleToggle", {
    Text = "Anti-Struggle",
    Default = false,
    Callback = function(v)
        antiStruggleActive = v
        if v then
            task.spawn(function()
                while antiStruggleActive do
                    local isHeld = plr:FindFirstChild("IsHeld")
                    if isHeld and isHeld.Value then
                        for i = 1, 5 do
                            ReplicatedStorage.CharacterEvents.Struggle:FireServer(plr)
                        end
                        local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(hrp, hrp.CFrame)
                        end
                    end
                    task.wait()
                end
            end)
        end
    end
})

local POS_cons = {}
local POS_inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
plr.CharacterAdded:Connect(function() POS_inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys") end)

DefenseLeft:AddToggle("AntiBlobToggle", {
    Text = "Anti-Blobman", Default = false,
    Callback = function(v)
        if v then
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name == "CreatureBlobman" and not (POS_inv and obj:IsDescendantOf(POS_inv)) then
                    local rd = obj:FindFirstChild("RightDetector") or obj:WaitForChild("RightDetector", 3)
                    local ld = obj:FindFirstChild("LeftDetector") or obj:WaitForChild("LeftDetector", 3)
                    if rd and ld then
                        rd.RightAlignOrientation.Enabled = false
                        rd.RightWeld.Enabled = false
                        ld.LeftAlignOrientation.Enabled = false
                        ld.LeftWeld.Enabled = false
                    end
                end
            end
            POS_cons["antiblob"] = workspace.DescendantAdded:Connect(function(d)
                if d.Name == "CreatureBlobman" and not (POS_inv and d:IsDescendantOf(POS_inv)) then
                    local rd = d:FindFirstChild("RightDetector") or d:WaitForChild("RightDetector", 3)
                    local ld = d:FindFirstChild("LeftDetector") or d:WaitForChild("LeftDetector", 3)
                    if rd and ld then
                        local rao = rd:WaitForChild("RightAlignOrientation", 1)
                        local rw = rd:WaitForChild("RightWeld", 1)
                        local lao = ld:WaitForChild("LeftAlignOrientation", 1)
                        local lw = ld:WaitForChild("LeftWeld", 1)
                        if rao then rao.Enabled = false end
                        if rw then rw.Enabled = false end
                        if lao then lao.Enabled = false end
                        if lw then lw.Enabled = false end
                    end
                end
            end)
        else
            if POS_cons["antiblob"] then POS_cons["antiblob"]:Disconnect() POS_cons["antiblob"] = nil end
        end
    end
})

local OA_antiVoidConn = nil
DefenseLeft:AddToggle("AntiVoidToggle", {
    Text = "Anti-Void", Default = false,
    Callback = function(on)
        if on then
            if OA_antiVoidConn then OA_antiVoidConn:Disconnect() end
            OA_antiVoidConn = RunService.Heartbeat:Connect(function()
                local char = plr.Character
                if char and char.PrimaryPart then
                    local pos = char.PrimaryPart.Position
                    if pos.Y < -50 then
                        char:SetPrimaryPartCFrame(CFrame.new(pos.X, pos.Y + 100, pos.Z))
                        char.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
                    end
                end
            end)
        else
            if OA_antiVoidConn then OA_antiVoidConn:Disconnect() OA_antiVoidConn = nil end
        end
    end
})

local antiFireEnabled = false
local antiFireCharConn, antiFireDebConn

local function getExt()
    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    local tool = inv and inv:FindFirstChild("FireExtinguisher")
    local part = tool and (tool:FindFirstChild("Handle") or tool.PrimaryPart or tool:FindFirstChildWhichIsA("BasePart", true))
    return tool, part
end

local function setupAntiFire(char)
    if antiFireDebConn then antiFireDebConn:Disconnect() antiFireDebConn = nil end

    local hrp = char:WaitForChild("HumanoidRootPart", 5)
    local hum = char:WaitForChild("Humanoid", 5)
    local fireDeb = hum and hum:WaitForChild("FireDebounce", 5)
    if not hrp or not fireDeb then return end

    antiFireDebConn = fireDeb:GetPropertyChangedSignal("Value"):Connect(function()
        if not antiFireEnabled or not fireDeb.Value then return end

        local tool, part = getExt()
        if tool then
            xpcall(function() destroyToyRemote:FireServer(tool) end, function() end)
            task.wait(0.1)
        end

        xpcall(function()
            spawnRemote:InvokeServer("FireExtinguisher", hrp.CFrame * CFrame.new(0, 2, 0), Vector3.zero)
        end, function() end)

        local t = tick()
        repeat
            task.wait()
            tool, part = getExt()
        until part or tick() - t > 1

        if part then
            part.CFrame = hrp.CFrame + Vector3.new(0.5, 0.5, 0.5)
        end

        task.wait(0.2)

        tool = getExt()
        if tool then
            xpcall(function() destroyToyRemote:FireServer(tool) end, function() end)
        end
    end)
end

DefenseLeft:AddToggle("AntiFireToggle", {
    Text = "Anti Fire",
    Default = false,
    Callback = function(Value)
        antiFireEnabled = Value

        if antiFireDebConn then antiFireDebConn:Disconnect() antiFireDebConn = nil end
        if antiFireCharConn then antiFireCharConn:Disconnect() antiFireCharConn = nil end

        if not Value then
            local tool = getExt()
            if tool then xpcall(function() destroyToyRemote:FireServer(tool) end, function() end) end
            return
        end

        local char = plr.Character or plr.CharacterAdded:Wait()
        setupAntiFire(char)

        antiFireCharConn = plr.CharacterAdded:Connect(function(newChar)
            if antiFireEnabled then
                task.wait(0.5)
                setupAntiFire(newChar)
            end
        end)
    end
})

local antiGrabConn = nil
DefenseLeft:AddToggle("AntiGrabToggle", {
    Text = "Anti Grab", Default = false,
    Callback = function(Value)
        local isHeld = plr:WaitForChild("IsHeld", 5)
        local struggleEvent = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
        local savedCFrame = nil
        if antiGrabConn then antiGrabConn:Disconnect() antiGrabConn = nil end
        if not Value then
            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then plr.Character.HumanoidRootPart.Anchored = false end
            return
        end
        local function onHeldChanged(heldState)
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if heldState then
                if hrp then savedCFrame = hrp.CFrame hrp.Anchored = true end
                task.spawn(function()
                    while isHeld.Value and Value do struggleEvent:FireServer(plr) task.wait() end
                    if hrp then hrp.Anchored = false if savedCFrame then hrp.CFrame = savedCFrame end end
                end)
            else
                if hrp then hrp.Anchored = false if savedCFrame then hrp.CFrame = savedCFrame end end
            end
        end
        if isHeld then
            antiGrabConn = isHeld.Changed:Connect(onHeldChanged)
            if isHeld.Value then onHeldChanged(true) end
        end
    end
})

DefenseLeft:AddToggle("AntiLagToggle", {
    Text = "Anti Lag", Default = true,
    Callback = function(Value)
        if Value then
            xpcall(function() plr.PlayerScripts.CharacterAndBeamMove.Enabled = false end, function() end)
            xpcall(function() plr.PlayerScripts.StickyPartsTouchDetection.Enabled = false end, function() end)
        else
            xpcall(function() plr.PlayerScripts.CharacterAndBeamMove.Enabled = true end, function() end)
            xpcall(function() plr.PlayerScripts.StickyPartsTouchDetection.Enabled = true end, function() end)
        end
    end
})

DefenseLeft:AddToggle("ShurikenAntiKickToggle", {
    Text = "Antikick", Default = false,
    Callback = function(Value)
        _G.SAHAR_ShurikenAntiKick = Value
        local function ClearShurikens()
            local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            if inv and destroyToyRemote then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name == "SAHAR_AntiKick" or v.Name == "NinjaShuriken" then
                        xpcall(function() destroyToyRemote:FireServer(v) end, function() end)
                    end
                end
            end
        end
        if Value then
            task.spawn(function()
                local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local canSpawn = plr:WaitForChild("CanSpawnToy")
                local function getHRP()
                    local char = plr.Character
                    if char and char:FindFirstChild("HumanoidRootPart") then return char.HumanoidRootPart end
                    local c = plr.CharacterAdded:Wait()
                    return c:WaitForChild("HumanoidRootPart")
                end
                local function IsInPlot()
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then return false end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        local sign = v:FindFirstChild("PlotSign")
                        local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                        if owners then for _, b in pairs(owners:GetChildren()) do
                            if b.Value == plr.Name then local folder = workspace.PlotItems:FindFirstChild(v.Name) if folder then return true, folder end end
                        end end
                    end
                    return false
                end
                local function AttachShuriken(shuriken)
                    if not shuriken or not shuriken:FindFirstChild("StickyPart") then return end
                    local hrp = getHRP()
                    if not hrp then return end
                    if shuriken:FindFirstChild("SoundPart") then
                        if not shuriken.SoundPart:FindFirstChild("PartOwner") or shuriken.SoundPart.PartOwner.Value ~= plr.Name then
                            setOwner:FireServer(shuriken.SoundPart, shuriken.SoundPart.CFrame)
                        end
                    end
                    local firePart = hrp:FindFirstChild("FirePlayerPart") or hrp:WaitForChild("FirePlayerPart", 5)
                    if firePart then stickyEvent:FireServer(shuriken.StickyPart, firePart, CFrame.new(0,0,0) * CFrame.Angles(0, math.rad(90), math.rad(90))) end
                    for _, obj in pairs(shuriken:GetChildren()) do
                        if obj:IsA("BasePart") then
                            obj.CanTouch = false obj.CanCollide = false obj.CanQuery = false
                            obj.Color = Color3.fromRGB(200, 30, 30) obj.Transparency = 0.6
                            if obj:FindFirstChild("Highlight") then obj.Highlight:Destroy() end
                        end
                    end
                end
                local function SpawnShuriken()
                    local t = tick()
                    while not canSpawn.Value do if not _G.SAHAR_ShurikenAntiKick or tick() - t > 5 then return nil end task.wait(0.1) end
                    local hrp = getHRP()
                    if hrp then task.spawn(function() xpcall(function() spawnRemote:InvokeServer("NinjaShuriken", hrp.CFrame * CFrame.new(0,12,20), Vector3.zero) end, function() end) end) end
                    local inPlot, house = IsInPlot()
                    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    if inPlot and house then return house:WaitForChild("NinjaShuriken", 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and inv then return inv:WaitForChild("NinjaShuriken", 2) end
                    return nil
                end
                while _G.SAHAR_ShurikenAntiKick do
                    task.wait(0.005)
                    local char = plr.Character
                    if not char or not char:FindFirstChild("Humanoid") or char.Humanoid.Health <= 0 then continue end
                    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    local shuriken = inv and inv:FindFirstChild("NinjaShuriken")
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then
                        local inPlot, house = IsInPlot()
                        if inPlot and house and workspace.Plots:FindFirstChild(house.Name) then
                            local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
                            if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then
                                shuriken = SpawnShuriken()
                                if not shuriken then continue end
                                shuriken.Name = "SAHAR_AntiKick" AttachShuriken(shuriken)
                            end
                        end
                    end
                    if not shuriken then
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then continue end
                        shuriken = SpawnShuriken()
                        if not shuriken then continue end
                        shuriken.Name = "SAHAR_AntiKick"
                    end
                    repeat
                        if shuriken and shuriken:FindFirstChild("StickyPart") and shuriken.StickyPart.CanTouch then AttachShuriken(shuriken) shuriken.Name = "SAHAR_AntiKick" end
                        task.wait(0.3)
                    until not shuriken or not _G.SAHAR_ShurikenAntiKick or not shuriken:FindFirstChild("StickyPart") or not shuriken.StickyPart.CanTouch or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - shuriken.StickyPart.Position).Magnitude >= 20
                    if not shuriken or not shuriken:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - shuriken.StickyPart.Position).Magnitude >= 20 then ClearShurikens() end
                    xpcall(function()
                        repeat task.wait(0.05) until not _G.SAHAR_ShurikenAntiKick or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not shuriken or not shuriken:FindFirstChild("StickyPart") or not shuriken.StickyPart:FindFirstChild("StickyWeld") or not shuriken.StickyPart.StickyWeld.Part1
                        if not shuriken or not shuriken:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not shuriken.StickyPart:FindFirstChild("StickyWeld") or not shuriken.StickyPart.StickyWeld.Part1 then ClearShurikens() end
                    end, function() end)
                end
            end)
        else
            _G.SAHAR_ShurikenAntiKick = false
            ClearShurikens()
        end
    end
})

DefenseLeft:AddDivider()

local LN_ANO_SelectedToy = "FoodHamburger"
DefenseLeft:AddDropdown("NetworkToyDropdown", {
    Values = {"FoodHamburger","FoodDonut","InstrumentGuitarBanjo","InstrumentDrumBongos","FoodMayonnaise","FoodSodaCan"},
    Default = 1, Text = "Network Ownership Toy",
    Callback = function(Value) LN_ANO_SelectedToy = Value end
})

DefenseLeft:AddToggle("AntiNetworkOwnershipToggle", {
    Text = "Anti-Network Ownership", Default = false,
    Callback = function(Value)
        _G.LN_AntiInputLag = Value
        if not Value then return end
        task.spawn(function()
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart")
            while _G.LN_AntiInputLag do
                local toysFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if not toysFolder then RunService.Heartbeat:Wait() continue end
                local toy = toysFolder:FindFirstChild(LN_ANO_SelectedToy)
                if not toy then
                    xpcall(function() spawnRemote:InvokeServer(LN_ANO_SelectedToy, hrp.CFrame * CFrame.new(0,5,0), Vector3.zero) end, function() end)
                    RunService.Heartbeat:Wait()
                else
                    local holdPart = toy:FindFirstChild("HoldPart")
                    if holdPart then
                        local holdingPlayer = holdPart:FindFirstChild("HoldingPlayer") and holdPart.HoldingPlayer.Value
                        if holdingPlayer and holdingPlayer ~= plr then
                            xpcall(function() holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0,2000,0), Vector3.zero) end, function() end)
                            toy:Destroy()
                        else
                            while _G.LN_AntiInputLag do
                                xpcall(function() holdPart.HoldItemRemoteFunction:InvokeServer(toy, char) holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0,5000,0), Vector3.zero) end, function() end)
                                if not toy or not toy.Parent or not toysFolder:FindFirstChild(LN_ANO_SelectedToy) then break end
                                RunService.Heartbeat:Wait()
                            end
                        end
                    else
                        xpcall(function() if toy and toy.Parent then toy:Destroy() end spawnRemote:InvokeServer(LN_ANO_SelectedToy, hrp.CFrame * CFrame.new(0,5,0), Vector3.zero) end, function() end)
                        RunService.Heartbeat:Wait()
                    end
                end
            end
        end)
    end
})

local antiKickResetConnection = nil
DefenseRight:AddToggle("AutoResetToggle", {
    Text = "Auto Reset", Default = false,
    Callback = function(v)
        if v then
            local notifyEvent = ReplicatedStorage:WaitForChild("GameCorrectionEvents"):WaitForChild("GameCorrectionsNotify")
            antiKickResetConnection = notifyEvent.OnClientEvent:Connect(function(reason)
                if reason == "Flying" then
                    local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
                    if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) hum.Health = 0 end
                end
            end)
        else
            if antiKickResetConnection then antiKickResetConnection:Disconnect() antiKickResetConnection = nil end
        end
    end
})

local loopTpActive = false
local loopTpSavedPos = nil

DefenseRight:AddToggle("LoopTpToggle", {
    Text = "Loop TP",
    Default = false,
    Callback = function(Value)
        loopTpActive = Value
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        
        if Value then
            if hrp then loopTpSavedPos = hrp.CFrame end
            task.spawn(function()
                while loopTpActive do
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        local x = math.random(100000, 1000000) * (math.random(1, 2) == 1 and 1 or -1)
                        local y = math.random(100000, 1000000)
                        local z = math.random(100000, 1000000) * (math.random(1, 2) == 1 and 1 or -1)
                        hrp.CFrame = CFrame.new(x, y, z)
                    end
                    RunService.Heartbeat:Wait()
                end
            end)
        else
            if hrp and loopTpSavedPos then
                hrp.CFrame = loopTpSavedPos
                hrp.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end
})

local platformActive = false
local platformPart = nil
local platformSavedCFrame = nil

DefenseRight:AddToggle("PlatformToggle", {
    Text = "Platform",
    Default = false,
    Callback = function(Value)
        platformActive = Value
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChild("Humanoid")

        if Value then
            if not hrp then
                platformActive = false
                if Toggles and Toggles.PlatformToggle then Toggles.PlatformToggle:SetValue(false) end
                return
            end

            platformSavedCFrame = hrp.CFrame

            local platformPos = Vector3.new(1e6, 1e4, 1e6)

            platformPart = Instance.new("Part")
            platformPart.Name = "SAHAR_Platform"
            platformPart.Size = Vector3.new(230, 10, 300)
            platformPart.Position = platformPos
            platformPart.Anchored = true
            platformPart.CanCollide = true
            platformPart.Material = Enum.Material.Neon
            platformPart.Color = Color3.fromRGB(130, 50, 200)
            platformPart.Transparency = 0.5
            platformPart.Parent = workspace

            local highlight = Instance.new("Highlight")
            highlight.Name = "SAHAR_PlatformHighlight"
            highlight.FillColor = Color3.fromRGB(130, 50, 200)
            highlight.FillTransparency = 0.8
            highlight.OutlineColor = Color3.fromRGB(170, 80, 255)
            highlight.OutlineTransparency = 0.3
            highlight.Parent = platformPart

            task.wait(0.1)
            hrp.CFrame = CFrame.new(platformPos + Vector3.new(0, 3, 0))
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
        else
            if hrp and platformSavedCFrame then
                hrp.CFrame = platformSavedCFrame
                hrp.AssemblyLinearVelocity = Vector3.zero
                hrp.AssemblyAngularVelocity = Vector3.zero
            end

            if platformPart then
                platformPart:Destroy()
                platformPart = nil
            end

            platformSavedCFrame = nil
        end
    end
}):AddKeyPicker("PlatformBind", {
    Default = "P",
    SyncToggleState = true,
    Mode = "Toggle",
    Text = "Platform",
    NoUI = false
})

DefenseRight:AddToggle("AutoBreakPcldToggle", {
    Text = "Auto Break PCLD",
    Default = false,
    Callback = function(Value)
        _G.SAHAR_PcldLoop = Value
        if not Value then return end

        task.spawn(function()
            _G.SAHAR_PcldKills = 0
            
            while _G.SAHAR_PcldLoop and _G.SAHAR_PcldKills < 2 do
                if plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
                    task.wait()
                    plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                    plr.Character.Humanoid.Health = 0
                    _G.SAHAR_PcldKills = _G.SAHAR_PcldKills + 1
                    task.wait(1.5)
                end
                task.wait(0.1)
            end

            if not _G.SAHAR_PcldLoop then return end

            task.wait(3)

            while _G.SAHAR_PcldLoop do
                task.wait(0.1)
                
                if plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0 then
                    while _G.SAHAR_PcldLoop do
                        task.wait(0.1)
                        if plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
                            break
                        end
                    end
                    
                    if _G.SAHAR_PcldLoop and plr.Character and plr.Character:FindFirstChild("Humanoid") then
                        task.wait(0.2)
                        plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                        plr.Character.Humanoid.Health = 0
                        task.wait(3)
                    end
                end
            end
        end)
    end
})

local antibananaSit = false

DefenseRight:AddToggle("AntiBananaSit", {
    Text = "anti banana",
    Default = false,
    Callback = function(Value)
        antibananaSit = Value
        if Value then
            task.spawn(function()
                while antibananaSit do
                    local char = plr.Character
                    local hum = char and char:FindFirstChild("Humanoid")
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local cam = workspace.CurrentCamera

                    if hum and hrp and hum.Health > 0 then
                        hum.Sit = true
                        hum:ChangeState(Enum.HumanoidStateType.Running)
                        local Vec = cam.CFrame.LookVector
                        hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(Vec.X, 0, Vec.Z))
                    end
                    task.wait()
                    if hum then hum:ChangeState(Enum.HumanoidStateType.Running) end
                end
            end)
        else
            antibananaSit = false
        end
    end
})

local tractorGucciActive = false
local tractorRagdollConnection = nil

DefenseRight:AddToggle("TractorGucciLoopToggle", {
    Text = "Tractor Gucci Loop",
    Default = false,
    Callback = function(Value)
        tractorGucciActive = Value

        if tractorRagdollConnection then tractorRagdollConnection:Disconnect() tractorRagdollConnection = nil end

        if not Value then
            local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
            if hum then
                for i = 1, 30 do hum.Sit = true task.wait() hum.Sit = false end
            end
            local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            if inv then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name:find("Tractor") then destroyToyRemote:FireServer(v) end
                end
            end
            return
        end

        local function ActivateGucci()
            local char = plr.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hum or not hrp then return false end

            local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            if inv then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name:find("Tractor") then destroyToyRemote:FireServer(v) end
                end
            end

            spawnRemote:InvokeServer("TractorGreen", hrp.CFrame * CFrame.new(5, 5, 20), Vector3.zero)

            local tractor = nil
            for i = 1, 30 do
                inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if inv then
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name:find("Tractor") then tractor = v break end
                    end
                end
                if tractor then break end
                task.wait()
            end

            if not tractor then return false end

            local seat = tractor:WaitForChild("VehicleSeat", 2)
            if not seat then return false end

            seat:Sit(hum)

            tractorRagdollConnection = RunService.Heartbeat:Connect(function()
                if tractorGucciActive and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(plr.Character.HumanoidRootPart, 0)
                end
            end)

            local endTime = tick() + 3
            while tick() < endTime do
                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                task.wait()
            end

            local waitT = tick()
            while seat.Occupant ~= hum and tick() - waitT < 3 do task.wait() end

            hum:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait()

            local mainPart = tractor:FindFirstChild("Part") or tractor.PrimaryPart
            if mainPart then
                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(mainPart, mainPart.CFrame)
            end

            task.wait(0.2)
            seat.CFrame = CFrame.new(0, 9e9, 0)

            return true
        end

        task.spawn(function()
            local success = ActivateGucci()
            if success then
                Library:Notify("Gucci activated", 2)
            else
                Library:Notify("Gucci error", 2)
            end

            while tractorGucciActive do
                local char = plr.Character
                local hum = char and char:FindFirstChild("Humanoid")

                if not hum or not hum.SeatPart or not hum.SeatPart.Parent or not hum.SeatPart.Parent.Name:find("Tractor") then
                    if tractorRagdollConnection then tractorRagdollConnection:Disconnect() tractorRagdollConnection = nil end
                    local s = ActivateGucci()
                    if s then
                        Library:Notify("Gucci activated", 2)
                    else
                        Library:Notify("Gucci error", 2)
                    end
                end

                task.wait(0.1)
            end
        end)
    end
}):AddKeyPicker("TractorGucciLoopBind", {
    Default = "H",
    SyncToggleState = true,
    Mode = "Toggle",
    Text = "Tractor Gucci Loop",
    NoUI = false
})

local selectedGucciType = "Blobman"
DefenseRight:AddDropdown("GucciTypeDropdown", { Values = {"Blobman","Tractor"}, Default = 1, Text = "Gucci Type", Callback = function(Value) selectedGucciType = Value end })

local function doGucciBlobman()
    task.spawn(function()
        local char = plr.Character or plr.CharacterAdded:Wait()
        local hum = char:WaitForChild("Humanoid", 5)
        local hrp = char:WaitForChild("HumanoidRootPart", 5)
        if not hum or not hrp then return end
        local invFolder = workspace:WaitForChild(plr.Name .. "SpawnedInToys", 5)
        if not invFolder then return end
        xpcall(function() spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame * CFrame.new(5,5,20), Vector3.zero) end, function() end)
        local blob, t0 = nil, tick()
        repeat task.wait() blob = invFolder:FindFirstChild("CreatureBlobman") until blob or tick() - t0 > 5
        if not blob then return end
        local seat = blob:WaitForChild("VehicleSeat", 3)
        if not seat then return end
        seat:Sit(hum)
        task.spawn(function() local endTime = tick() + 3 while tick() < endTime do xpcall(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end, function() end) task.wait() end end)
        task.wait()
        local waitT = tick()
        while blob.VehicleSeat.Occupant ~= hum and tick() - waitT < 3 do task.wait() end
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
        task.wait()
        xpcall(function() blob.VehicleSeat.CFrame = CFrame.new(0, 0/0, 0) end, function() end)
    end)
end

local function undoGucciBlobman()
    local invFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    local blob = invFolder and invFolder:FindFirstChild("CreatureBlobman")
    if blob then xpcall(function() destroyToyRemote:FireServer(blob) end, function() end) end
    local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
    if hum then for i = 1, 30 do hum.Sit = true task.wait() hum.Sit = false end end
end

local function doGucciTractor()
    task.spawn(function()
        local Ragdoll = ReplicatedStorage.CharacterEvents.RagdollRemote
        local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
        local char = plr.Character
        local hum = char and char:FindFirstChild("Humanoid")
        local HRP = char and char:FindFirstChild("HumanoidRootPart")
        if not (char and hum and HRP) then return end
        local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
        if inv and inv:FindFirstChild("SAHAR_tractorgucci") then destroyToyRemote:FireServer(inv.SAHAR_tractorgucci) end
        spawnRemote:InvokeServer("TractorGreen", HRP.CFrame * CFrame.new(5,5,20), Vector3.zero)
        local blobb, t = nil, tick()
        repeat task.wait() inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys") if inv then blobb = inv:FindFirstChild("TractorGreen") end until blobb or tick() - t > 5
        if blobb then
            blobb.Name = "SAHAR_tractorgucci"
            local seat = blobb:WaitForChild("VehicleSeat", 3)
            if seat then
                seat:Sit(hum)
                task.spawn(function() local endTime = tick() + 3 while tick() < endTime do Ragdoll:FireServer(HRP, 0) task.wait() end end)
                task.wait()
                while seat.Occupant ~= hum do task.wait() end
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
                local mainPart = blobb:FindFirstChild("Part") or blobb.PrimaryPart
                if mainPart then SetNetworkOwner:FireServer(mainPart, mainPart.CFrame) end
                task.wait(0.2)
                seat.CFrame = CFrame.new(0, 0/0, 0)
            end
        end
    end)
end

local function undoGucciTractor()
    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    if inv and inv:FindFirstChild("SAHAR_tractorgucci") then destroyToyRemote:FireServer(inv.SAHAR_tractorgucci) end
    local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
    if hum then for i = 1, 30 do hum.Sit = true task.wait() hum.Sit = false end end
end

DefenseRight:AddToggle("GucciToggle", {
    Text = "Gucci", Default = false,
    Callback = function(Value)
        if Value then if selectedGucciType == "Blobman" then doGucciBlobman() else doGucciTractor() end
        else if selectedGucciType == "Blobman" then undoGucciBlobman() else undoGucciTractor() end end
    end
}):AddKeyPicker("GucciBind", { Default = "G", SyncToggleState = true, Mode = "Toggle", Text = "Gucci", NoUI = false })

local selectedKickTarget = nil
KickLeft:AddDropdown("KickTargetDropdown", { Values = GetPlayerList(), Default = 1, Text = "Select Target", Callback = function(Value) selectedKickTarget = Value end })

KickLeft:AddToggle("MouseTargetToggle", {
    Text = "Select Target (Mouse)",
    Default = false,
    Callback = function(Value)
        if Value then
            local target = mouse.Target
            if target then
                local model = target:FindFirstAncestorOfClass("Model")
                local p = Players:GetPlayerFromCharacter(model)
                if p and p ~= plr then
                    local targetStr = p.DisplayName .. "/" .. p.Name
                    if Options.KickTargetDropdown then
                        Options.KickTargetDropdown:SetValue(targetStr)
                    end
                end
            end
            
            task.delay(0.05, function()
                if Toggles.MouseTargetToggle then
                    Toggles.MouseTargetToggle:SetValue(false)
                end
            end)
        end
    end
}):AddKeyPicker("MouseTargetBind", {
    Default = "T",
    SyncToggleState = true,
    Mode = "Toggle",
    Text = "Select Target"
})

task.spawn(function() while task.wait(1.5) do xpcall(function() Options.KickTargetDropdown:SetValues(GetPlayerList()) end, function() end) end end)

local kickGrabActive = false
local hugeForce = Vector3.new(math.huge, math.huge, math.huge)

local function invis_touch(part, cf)
    xpcall(function() ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, cf) end, function() end)
end

KickRight:AddToggle("KickGrabToggle", {
    Text = "Kick Grab", Default = false,
    Callback = function(Value)
        kickGrabActive = Value

        if not Value then
            if selectedKickTarget then
                local target = GetTargetFromString(selectedKickTarget)
                local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if tHRP:FindFirstChild("KickBP") then tHRP.KickBP:Destroy() end
                    if tHRP:FindFirstChild("KickBG") then tHRP.KickBG:Destroy() end
                end
            end
            return
        end

        task.spawn(function()
            local DestroyLine = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine")

            while kickGrabActive do
                if selectedKickTarget then
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local target = GetTargetFromString(selectedKickTarget)
                    local tChar = target and target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")

                    if hrp and tHRP then
                        local bp = tHRP:FindFirstChild("KickBP")
                        if not bp then
                            bp = Instance.new("BodyPosition")
                            bp.Name = "KickBP"
                            bp.MaxForce = hugeForce
                            bp.D = 200
                            bp.P = 20000
                            bp.Parent = tHRP
                        end
                        bp.Position = hrp.Position + Vector3.new(0, 15, 0)

                        local bg = tHRP:FindFirstChild("KickBG")
                        if not bg then
                            bg = Instance.new("BodyGyro")
                            bg.Name = "KickBG"
                            bg.MaxTorque = hugeForce
                            bg.Parent = tHRP
                        end
                        bg.CFrame = hrp.CFrame

                        if tHRP.Position.Y < 2000 then
                            invis_touch(tHRP, tHRP.CFrame)
                            if DestroyLine then DestroyLine:FireServer(tHRP) end
                        end

                        local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                        if spawned then
                            local function yeetPart(part)
                                if part then
                                    invis_touch(part, part.CFrame)
                                    local owner = part:FindFirstChild("PartOwner")
                                    if owner and owner.Value == plr.Name then
                                        part.CFrame = CFrame.new(0, 10000, 0)
                                    end
                                end
                            end

                            local kunai = spawned:FindFirstChild("NinjaKunai")
                            if kunai then yeetPart(kunai:FindFirstChild("SoundPart")) end
                            local shuriken = spawned:FindFirstChild("NinjaShuriken")
                            if shuriken then yeetPart(shuriken:FindFirstChild("SoundPart")) end
                        end

                        local dist = (hrp.Position - tHRP.Position).Magnitude
                        if tHRP.Position.Y < 2000 and dist > 25 then
                            local oldCF = hrp.CFrame
                            hrp.CFrame = tHRP.CFrame * CFrame.new(0, 0, 5)
                            task.wait(0.15)
                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                hrp.CFrame = oldCF
                            end
                        end
                    end
                end
                task.wait()
            end

            if selectedKickTarget then
                local target = GetTargetFromString(selectedKickTarget)
                local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if tHRP:FindFirstChild("KickBP") then tHRP.KickBP:Destroy() end
                    if tHRP:FindFirstChild("KickBG") then tHRP.KickBG:Destroy() end
                end
            end
        end)
    end
})

local ownershipLoopActive = false

KickRight:AddToggle("OwnershipKickLoopToggle", {
    Text = "Ownership Kick Loop",
    Default = false,
    Callback = function(Value)
        ownershipLoopActive = Value
        
        if not Value then
            if selectedKickTarget then
                local target = GetTargetFromString(selectedKickTarget)
                local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if tHRP:FindFirstChild("OwnerKickBP") then tHRP.OwnerKickBP:Destroy() end
                    if tHRP:FindFirstChild("OwnerKickBG") then tHRP.OwnerKickBG:Destroy() end
                end
            end
            return 
        end
        
        task.spawn(function()
            local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
            local destroyLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
            
            local target = GetTargetFromString(selectedKickTarget)
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            
            if not target or not target.Character or not hrp then
                ownershipLoopActive = false
                if Toggles and Toggles.OwnershipKickLoopToggle then Toggles.OwnershipKickLoopToggle:SetValue(false) end
                return
            end
            
            local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then
                ownershipLoopActive = false
                if Toggles and Toggles.OwnershipKickLoopToggle then Toggles.OwnershipKickLoopToggle:SetValue(false) end
                return
            end

            local oldCF = hrp.CFrame
            
            hrp.CFrame = tHRP.CFrame
            hrp.AssemblyLinearVelocity = Vector3.zero
            task.wait(0.15)
            
            setOwner:FireServer(tHRP, tHRP.CFrame)
            destroyLine:FireServer(tHRP)
            
            hrp.CFrame = oldCF
            hrp.AssemblyLinearVelocity = Vector3.zero
            
            while ownershipLoopActive do
                target = GetTargetFromString(selectedKickTarget)
                char = plr.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if target and target.Character and hrp then
                    tHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    
                    if tHRP then
                        setOwner:FireServer(tHRP, tHRP.CFrame)
                        destroyLine:FireServer(tHRP)
                        
                        local bp = tHRP:FindFirstChild("OwnerKickBP")
                        if not bp then
                            bp = Instance.new("BodyPosition")
                            bp.Name = "OwnerKickBP"
                            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bp.D = 100
                            bp.P = 100000
                            bp.Parent = tHRP
                        end
                        bp.Position = hrp.Position + Vector3.new(0, 14, 0)
                        
                        local bg = tHRP:FindFirstChild("OwnerKickBG")
                        if not bg then
                            bg = Instance.new("BodyGyro")
                            bg.Name = "OwnerKickBG"
                            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            bg.P = 100000
                            bg.Parent = tHRP
                        end
                        bg.CFrame = hrp.CFrame
                        
                        tHRP.AssemblyLinearVelocity = Vector3.zero
                        tHRP.AssemblyAngularVelocity = Vector3.zero
                    end
                end
                RunService.Heartbeat:Wait()
            end
            
            if selectedKickTarget then
                local targetReset = GetTargetFromString(selectedKickTarget)
                local tHRPReset = targetReset and targetReset.Character and targetReset.Character:FindFirstChild("HumanoidRootPart")
                if tHRPReset then
                    if tHRPReset:FindFirstChild("OwnerKickBP") then tHRPReset.OwnerKickBP:Destroy() end
                    if tHRPReset:FindFirstChild("OwnerKickBG") then tHRPReset.OwnerKickBG:Destroy() end
                end
            end
        end)
    end
})

local ragdollPalletKickActive = false

local function ClearRagdollPalletKick()
    for _, p in pairs(Players:GetPlayers()) do
        local tHRP = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if tHRP then
            local bp = tHRP:FindFirstChild("SAHAR_RPK_BP")
            local bg = tHRP:FindFirstChild("SAHAR_RPK_BG")
            if bp then bp:Destroy() end
            if bg then bg:Destroy() end
        end
    end

    local myToys = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    local pallet = myToys and myToys:FindFirstChild("SAHAR_RagdollPalletKick")
    if pallet then
        destroyToyRemote:FireServer(pallet)
    end
end

KickRight:AddToggle("RagdollPalletKickToggle", {
    Text = "Ragdoll Pallet Kick",
    Default = false,
    Callback = function(Value)
        ragdollPalletKickActive = Value

        if not Value then
            ClearRagdollPalletKick()
            return
        end

        if not selectedKickTarget then
            ragdollPalletKickActive = false
            if Toggles and Toggles.RagdollPalletKickToggle then
                Toggles.RagdollPalletKickToggle:SetValue(false)
            end
            return
        end

        task.spawn(function()
            local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
            local destroyLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
            local ragdollRemote = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

            local function GetPallet()
                local myToys = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                local pallet = myToys and (myToys:FindFirstChild("SAHAR_RagdollPalletKick") or myToys:FindFirstChild("PalletLightBrown"))
                if pallet and pallet.Name ~= "SAHAR_RagdollPalletKick" then
                    pallet.Name = "SAHAR_RagdollPalletKick"
                end
                return pallet
            end

            local function SpawnPallet(cf)
                spawnRemote:InvokeServer("PalletLightBrown", cf, Vector3.new(0, -90, 0))
                local pallet
                local t = tick()
                repeat
                    task.wait()
                    pallet = GetPallet()
                until pallet or tick() - t > 3
                return pallet
            end

            local function EnsurePallet()
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp then return nil, nil, nil, nil end

                local pallet = GetPallet()
                if not pallet then
                    pallet = SpawnPallet(hrp.CFrame * CFrame.new(0, 8, 0))
                end
                if not pallet then return nil, nil, nil, nil end

                local palletPart = pallet:FindFirstChild("SoundPart")
                local palletMain = pallet:FindFirstChild("Main")
                if not palletPart or not palletMain then return nil, nil, nil, nil end

                local owner = palletPart:FindFirstChild("PartOwner")
                if not owner or owner.Value ~= plr.Name then
                    setOwner:FireServer(palletPart, palletPart.CFrame)
                end

                local spin = palletPart:FindFirstChild("RagdollGrabSpin")
                if not spin then
                    spin = Instance.new("AngularVelocity")
                    spin.Name = "RagdollGrabSpin"
                    spin.MaxTorque = math.huge
                    spin.AngularVelocity = Vector3.new(100000, 0, 100000)
                    spin.RelativeTo = Enum.ActuatorRelativeTo.World
                    spin.Attachment0 = palletPart:FindFirstChild("Attachment") or Instance.new("Attachment", palletPart)
                    spin.Parent = palletPart
                end

                local pos = palletMain:FindFirstChild("RagdollGrabPosition")
                if not pos then
                    pos = Instance.new("BodyPosition")
                    pos.Name = "RagdollGrabPosition"
                    pos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    pos.P = 300000
                    pos.D = 100
                    pos.Parent = palletMain
                end

                for _, part in pairs(pallet:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                        part.CanTouch = true
                        part.CanQuery = true
                    end
                end

                return pallet, palletPart, palletMain, pos
            end

            local target = GetTargetFromString(selectedKickTarget)
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")

            if not target or not target.Character or not hrp then
                ragdollPalletKickActive = false
                if Toggles and Toggles.RagdollPalletKickToggle then
                    Toggles.RagdollPalletKickToggle:SetValue(false)
                end
                return
            end

            local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then
                ragdollPalletKickActive = false
                if Toggles and Toggles.RagdollPalletKickToggle then
                    Toggles.RagdollPalletKickToggle:SetValue(false)
                end
                return
            end

            local pallet, palletPart, palletMain, palletPos = EnsurePallet()
            if not pallet or not palletPart or not palletMain or not palletPos then
                ragdollPalletKickActive = false
                if Toggles and Toggles.RagdollPalletKickToggle then
                    Toggles.RagdollPalletKickToggle:SetValue(false)
                end
                return
            end

            local oldCF = hrp.CFrame
            hrp.CFrame = tHRP.CFrame
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            task.wait(0.15)
            setOwner:FireServer(tHRP, tHRP.CFrame)
            destroyLine:FireServer(tHRP)
            hrp.CFrame = oldCF
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero

            local smashUp = true

            while ragdollPalletKickActive do
                target = GetTargetFromString(selectedKickTarget)
                char = plr.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                local tChar = target and target.Character
                tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum = tChar and tChar:FindFirstChild("Humanoid")

                if not hrp or not tHRP or not tHum then
                    task.wait()
                    continue
                end

                if not pallet or not pallet.Parent or not palletPart or not palletPart.Parent or not palletMain or not palletMain.Parent or not palletPos or not palletPos.Parent then
                    pallet, palletPart, palletMain, palletPos = EnsurePallet()
                    if not pallet or not palletPart or not palletMain or not palletPos then
                        task.wait()
                        continue
                    end
                end

                setOwner:FireServer(tHRP, tHRP.CFrame)
                destroyLine:FireServer(tHRP)

                local bp = tHRP:FindFirstChild("SAHAR_RPK_BP")
                if not bp then
                    bp = Instance.new("BodyPosition")
                    bp.Name = "SAHAR_RPK_BP"
                    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bp.P = 1000000
                    bp.D = 1000
                    bp.Parent = tHRP
                end
                bp.Position = hrp.Position + Vector3.new(0, 14, 0)

                local bg = tHRP:FindFirstChild("SAHAR_RPK_BG")
                if not bg then
                    bg = Instance.new("BodyGyro")
                    bg.Name = "SAHAR_RPK_BG"
                    bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                    bg.P = 1000000
                    bg.D = 500
                    bg.Parent = tHRP
                end
                bg.CFrame = hrp.CFrame

                tHRP.CFrame = hrp.CFrame * CFrame.new(0, 14, 0)
                tHRP.AssemblyLinearVelocity = Vector3.zero
                tHRP.AssemblyAngularVelocity = Vector3.zero
                tHum.PlatformStand = true

                ragdollRemote:FireServer(tHRP, 0)

                local owner = palletPart:FindFirstChild("PartOwner")
                if not owner or owner.Value ~= plr.Name then
                    setOwner:FireServer(palletPart, palletPart.CFrame)
                end

                if smashUp then
                    palletPos.Position = tHRP.Position + Vector3.new(0, 8, 0)
                else
                    palletPos.Position = tHRP.Position + Vector3.new(0, 1, 0)
                    palletMain.AssemblyLinearVelocity = Vector3.new(0, -120, 0)
                    palletPart.AssemblyAngularVelocity = Vector3.new(600, 0, 600)
                end
                smashUp = not smashUp

                local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                if spawned then
                    local kunai = spawned:FindFirstChild("NinjaKunai")
                    local shuriken = spawned:FindFirstChild("NinjaShuriken")

                    if kunai and kunai:FindFirstChild("SoundPart") then
                        setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        local owner1 = kunai.SoundPart:FindFirstChild("PartOwner")
                        if owner1 and owner1.Value == plr.Name then
                            kunai.SoundPart.CFrame = CFrame.new(0, 10000, 0)
                        end
                    end

                    if shuriken and shuriken:FindFirstChild("SoundPart") then
                        setOwner:FireServer(shuriken.SoundPart, shuriken.SoundPart.CFrame)
                        local owner2 = shuriken.SoundPart:FindFirstChild("PartOwner")
                        if owner2 and owner2.Value == plr.Name then
                            shuriken.SoundPart.CFrame = CFrame.new(0, 10000, 0)
                        end
                    end
                end

                RunService.Heartbeat:Wait()
            end

            ClearRagdollPalletKick()
        end)
    end
})

local destroyGucciActive = false

KickRight:AddToggle("DestroyTargetGucciToggle", {
    Text = "Destroy Target Gucci",
    Default = false,
    Callback = function(Value)
        destroyGucciActive = Value
        if Value then
            task.spawn(function()
                local target = GetTargetFromString(selectedKickTarget)
                if not target then 
                    destroyGucciActive = false
                    Toggles.DestroyTargetGucciToggle:SetValue(false)
                    return 
                end

                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                local safeSpot = hrp.CFrame

                local targetVehicles = {
                    "CreatureBlobman", "TractorOrange", "TractorGreen", 
                    "TractorRed", "SantaSleigh", "Train", "Handle", "Seat"
                }

                while destroyGucciActive do
                    target = GetTargetFromString(selectedKickTarget)
                    if not target or not target.Parent then break end
                    
                    local folders = {
                        workspace:FindFirstChild(target.Name .. "SpawnedInToys"),
                        workspace.PlotItems:FindFirstChild(target.Name), -- Для Train и построек в плотах
                        workspace:FindFirstChild("Train") -- Глобальный поиск поезда
                    }

                    for _, folder in pairs(folders) do
                        if not folder then continue end
                        local searchArea = (folder.Name == "Train") and {folder} or folder:GetChildren()
                        
                        for _, obj in pairs(searchArea) do
                            if not destroyGucciActive then break end
                            
                            local isTarget = false
                            for _, vehName in pairs(targetVehicles) do
                                if obj.Name:find(vehName) then isTarget = true break end
                            end

                            if isTarget then
                                local seat = obj:IsA("VehicleSeat") and obj or obj:FindFirstChildWhichIsA("VehicleSeat", true) or obj:FindFirstChildWhichIsA("Seat", true)
                                
                                if seat then
                                    local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
                                    if hum and hum.SeatPart ~= seat then
                                        local sitStart = tick()
                                        while tick() - sitStart < 0.5 do
                                            if not destroyGucciActive or hum.SeatPart == seat then break end
                                            seat:Sit(hum)
                                            RunService.Heartbeat:Wait()
                                        end
                                        
                                        if hum.SeatPart == seat then
                                            task.wait(0.1)
                                            hum.Sit = false
                                            hum:ChangeState(Enum.HumanoidStateType.Jumping)
                                            task.wait(0.05)
                                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                                plr.Character.HumanoidRootPart.CFrame = safeSpot
                                                plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
                                            end
                                            pcall(function() destroyToyRemote:FireServer(obj) end)
                                            task.wait(0.1)
                                        end
                                    end
                                end
                            end
                        end
                    end
                    task.wait(0.1)
                end
                destroyGucciActive = false
                Toggles.DestroyTargetGucciToggle:SetValue(false)
            end)
        end
    end
})

local loopKillActive = false

KickRight:AddToggle("LoopKillToggle", {
    Text = "loop kill",
    Default = false,
    Callback = function(Value)
        loopKillActive = Value
        if not Value then return end

        task.spawn(function()
            local SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
            local DestroyLine = ReplicatedStorage.GrabEvents.DestroyGrabLine
            
            while loopKillActive do
                local target = GetTargetFromString(selectedKickTarget)
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if target and char and hrp then
                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local tHum = tChar and tChar:FindFirstChild("Humanoid")
                    
                    if tHRP and tHum and tHum.Health > 0 and tChar.Parent == workspace then
                        local oldCF = hrp.CFrame
                        
                        hrp.CFrame = tHRP.CFrame
                        local count = 0
                        repeat
                            SetNetOwner:FireServer(tHRP, tHRP.CFrame)
                            RunService.Heartbeat:Wait()
                            count = count + 1
                        until (tChar:FindFirstChild("Head") and tChar.Head:FindFirstChild("PartOwner")) or count > 10 or not loopKillActive
                        
                        if loopKillActive then
                            tHum:ChangeState(Enum.HumanoidStateType.Dead)
                            tHum.Health = 0
                            DestroyLine:FireServer(tHRP)
                        end
                        
                        hrp.CFrame = oldCF
                        hrp.AssemblyLinearVelocity = Vector3.zero
                    end
                end
                task.wait(0.05)
            end
        end)
    end
})

local rotateKickActive = false

KickRight:AddToggle("RotateKickToggle", {
    Text = "Rotate Kick", Default = false,
    Callback = function(Value)
        rotateKickActive = Value

        if not Value then
            if selectedKickTarget then
                local target = GetTargetFromString(selectedKickTarget)
                local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if tHRP:FindFirstChild("RotateBP") then tHRP.RotateBP:Destroy() end
                    if tHRP:FindFirstChild("RotateAV") then tHRP.RotateAV:Destroy() end
                end
            end
            return
        end

        task.spawn(function()
            local DestroyLine = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine")

            while rotateKickActive do
                if selectedKickTarget then
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local target = GetTargetFromString(selectedKickTarget)
                    local tChar = target and target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")

                    if hrp and tHRP then
                        invis_touch(tHRP, tHRP.CFrame)
                        if DestroyLine then DestroyLine:FireServer(tHRP) end

                        local lockPos = hrp.Position + Vector3.new(0, 15, 0)

                        local bp = tHRP:FindFirstChild("RotateBP")
                        if not bp then
                            bp = Instance.new("BodyPosition")
                            bp.Name = "RotateBP"
                            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bp.D = 200
                            bp.P = 20000
                            bp.Parent = tHRP
                        end
                        bp.Position = lockPos

                        local av = tHRP:FindFirstChild("RotateAV")
                        if not av then
                            av = Instance.new("BodyAngularVelocity")
                            av.Name = "RotateAV"
                            av.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            av.Parent = tHRP
                        end
                        av.AngularVelocity = Vector3.new(0, 30, 30)

                        tHRP.AssemblyLinearVelocity = Vector3.zero

                        local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                        if spawned then
                            local function yeetPart(part)
                                if part then
                                    invis_touch(part, part.CFrame)
                                    local owner = part:FindFirstChild("PartOwner")
                                    if owner and owner.Value == plr.Name then
                                        part.CFrame = CFrame.new(0, 10000, 0)
                                    end
                                end
                            end
                            local kunai = spawned:FindFirstChild("NinjaKunai")
                            if kunai then yeetPart(kunai:FindFirstChild("SoundPart")) end
                            local shuriken = spawned:FindFirstChild("NinjaShuriken")
                            if shuriken then yeetPart(shuriken:FindFirstChild("SoundPart")) end
                        end

                        local dist = (hrp.Position - tHRP.Position).Magnitude
                        if dist > 25 then
                            local oldCF = hrp.CFrame
                            hrp.CFrame = tHRP.CFrame * CFrame.new(0, 0, 5)
                            task.wait(0.1)
                            invis_touch(tHRP, tHRP.CFrame)
                            if DestroyLine then DestroyLine:FireServer(tHRP) end
                            task.wait(0.05)
                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                hrp.CFrame = oldCF
                            end
                        end
                    end
                end
                task.wait()
            end

            if selectedKickTarget then
                local target = GetTargetFromString(selectedKickTarget)
                local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                if tHRP then
                    if tHRP:FindFirstChild("RotateBP") then tHRP.RotateBP:Destroy() end
                    if tHRP:FindFirstChild("RotateAV") then tHRP.RotateAV:Destroy() end
                end
            end
        end)
    end
})

local kickAllActive = false

KickRight:AddToggle("KickAllToggle", {
    Text = "Kick All", Default = false,
    Callback = function(Value)
        kickAllActive = Value

        if not Value then
            for _, target in pairs(Players:GetPlayers()) do
                if target ~= plr and target.Character then
                    local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        if tHRP:FindFirstChild("KickAllBP") then tHRP.KickAllBP:Destroy() end
                        if tHRP:FindFirstChild("KickAllBG") then tHRP.KickAllBG:Destroy() end
                    end
                end
            end
            return
        end

        task.spawn(function()
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then kickAllActive = false return end

            local DestroyLine = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine")
            local oldCFrame = hrp.CFrame
            local holdPos = Vector3.new(0, 70, 0)
            local playersnow = Players:GetPlayers()
            local captured = {}

            for _, target in pairs(playersnow) do
                if target == plr then continue end
                if not kickAllActive then break end

                local tChar = target.Character
                local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                if not tHRP then continue end

                local success = xpcall(function()
                    for i = 1, 10 do
                        if not kickAllActive then return end
                        hrp.CFrame = tHRP.CFrame * CFrame.new(0, 0, 5)
                        hrp.AssemblyLinearVelocity = Vector3.zero
                        hrp.AssemblyAngularVelocity = Vector3.zero
                        invis_touch(tHRP, tHRP.CFrame)
                        if DestroyLine then DestroyLine:FireServer(tHRP) end
                        task.wait(0.05)
                    end
                end, function() end)

                if success then
                    table.insert(captured, target)
                end
            end

            if not kickAllActive then return end

            for i, target in pairs(captured) do
                local tChar = target.Character
                local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                if not tHRP then continue end

                local angle = i * (math.pi * 2) / #captured
                local x = holdPos.X + math.cos(angle) * 15
                local z = holdPos.Z + math.sin(angle) * 15

                local bp = Instance.new("BodyPosition")
                bp.Name = "KickAllBP"
                bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bp.D = 200
                bp.P = 20000
                bp.Position = Vector3.new(x, holdPos.Y, z)
                bp.Parent = tHRP

                local bg = Instance.new("BodyGyro")
                bg.Name = "KickAllBG"
                bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                bg.CFrame = CFrame.new(Vector3.new(x, holdPos.Y, z), holdPos)
                bg.Parent = tHRP
            end

            hrp.CFrame = CFrame.new(holdPos)
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            task.wait(0.1)

            while kickAllActive do
                local anyLeft = false

                for _, target in pairs(captured) do
                    if not target.Parent then continue end
                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    if not tHRP then continue end

                    anyLeft = true

                    invis_touch(tHRP, tHRP.CFrame)
                    if DestroyLine then DestroyLine:FireServer(tHRP) end

                    tHRP.AssemblyLinearVelocity = Vector3.zero
                    tHRP.AssemblyAngularVelocity = Vector3.zero

                    local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                    if spawned then
                        local function yeetPart(part)
                            if part then
                                invis_touch(part, part.CFrame)
                                local owner = part:FindFirstChild("PartOwner")
                                if owner and owner.Value == plr.Name then
                                    part.CFrame = CFrame.new(0, 10000, 0)
                                end
                            end
                        end
                        local kunai = spawned:FindFirstChild("NinjaKunai")
                        if kunai then yeetPart(kunai:FindFirstChild("SoundPart")) end
                        local shuriken = spawned:FindFirstChild("NinjaShuriken")
                        if shuriken then yeetPart(shuriken:FindFirstChild("SoundPart")) end
                    end
                end

                hrp.CFrame = CFrame.new(holdPos)
                hrp.AssemblyLinearVelocity = Vector3.zero

                if not anyLeft then break end
                task.wait()
            end

            hrp.CFrame = oldCFrame

            for _, target in pairs(captured) do
                if target.Character then
                    local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        if tHRP:FindFirstChild("KickAllBP") then tHRP.KickAllBP:Destroy() end
                        if tHRP:FindFirstChild("KickAllBG") then tHRP.KickAllBG:Destroy() end
                    end
                end
            end

            kickAllActive = false
            if Toggles and Toggles.KickAllToggle then Toggles.KickAllToggle:SetValue(false) end
        end)
    end
})

local snowballAttackActive = false

KickRight:AddToggle("SnowballAttackToggle", {
    Text = "SnowBall Attack",
    Default = false,
    Callback = function(Value)
        snowballAttackActive = Value

        if not Value then
            local folder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            if folder then
                for _, snowball in pairs(folder:GetChildren()) do
                    if snowball.Name == "BallSnowball" then
                        pcall(function() destroyToyRemote:FireServer(snowball) end)
                    end
                end
            end
            return
        end

        if not selectedKickTarget then
            snowballAttackActive = false
            if Toggles and Toggles.SnowballAttackToggle then
                Toggles.SnowballAttackToggle:SetValue(false)
            end
            return
        end

        task.spawn(function()
            while snowballAttackActive do
                local target = GetTargetFromString(selectedKickTarget)
                if not target or not target.Parent then
                    task.wait(0.5)
                    continue
                end

                local tChar = target.Character
                local torso = tChar and (tChar:FindFirstChild("UpperTorso") or tChar:FindFirstChild("Torso") or tChar:FindFirstChild("HumanoidRootPart"))
                
                if not torso then
                    task.wait(0.5)
                    continue
                end

                pcall(function()
                    local offset = Vector3.new(
                        math.random(-50, 50) / 100,
                        math.random(-50, 50) / 100,
                        math.random(-50, 50) / 100
                    )
                    spawnRemote:InvokeServer("BallSnowball", torso.CFrame * CFrame.new(offset), Vector3.zero)
                end)

                local folder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if folder then
                    for _, snowball in pairs(folder:GetChildren()) do
                        if snowball.Name == "BallSnowball" and snowball.Parent then
                            local part = snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart")
                            if part and torso then
                                local offset = Vector3.new(
                                    math.random(-50, 50) / 100,
                                    math.random(-50, 50) / 100,
                                    math.random(-50, 50) / 100
                                )
                                part.CFrame = torso.CFrame * CFrame.new(offset)
                                part.AssemblyLinearVelocity = Vector3.zero
                                part.AssemblyAngularVelocity = Vector3.zero
                            end
                        end
                    end
                end

                task.wait(0.05)
            end
        end)
    end
})

local autoSitBlobActive = false
local autoSitBlobConn = nil
local currentBlobSeat = nil

BlobmanLeft:AddToggle("AutoSpawnSitBlobToggle", {
    Text = "Auto Spawn & Sit Blobman", Default = false,
    Callback = function(Value)
        autoSitBlobActive = Value

        if autoSitBlobConn then autoSitBlobConn:Disconnect() autoSitBlobConn = nil end

        if not Value then
            currentBlobSeat = nil
            return
        end

        task.spawn(function()
            local char = plr.Character or plr.CharacterAdded:Wait()
            local hum = char:WaitForChild("Humanoid", 5)
            local hrp = char:WaitForChild("HumanoidRootPart", 5)
            if not hum or not hrp then return end
            local invFolder = workspace:WaitForChild(plr.Name .. "SpawnedInToys", 5)
            if not invFolder then return end

            local blob = invFolder:FindFirstChild("CreatureBlobman")
            if not blob then
                xpcall(function() spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame * CFrame.new(0,3,5), Vector3.zero) end, function() end)
                local t0 = tick()
                repeat task.wait() blob = invFolder:FindFirstChild("CreatureBlobman") until blob or tick() - t0 > 5
            end
            if not blob then
                autoSitBlobActive = false
                if Toggles and Toggles.AutoSpawnSitBlobToggle then Toggles.AutoSpawnSitBlobToggle:SetValue(false) end
                return
            end

            local seat = blob:WaitForChild("VehicleSeat", 3)
            if not seat then
                autoSitBlobActive = false
                if Toggles and Toggles.AutoSpawnSitBlobToggle then Toggles.AutoSpawnSitBlobToggle:SetValue(false) end
                return
            end

            currentBlobSeat = seat
            seat:Sit(hum)
            local sitT = tick()
            while seat.Occupant ~= hum and tick() - sitT < 2 do seat:Sit(hum) task.wait() end

            autoSitBlobConn = RunService.Heartbeat:Connect(function()
                if not autoSitBlobActive then
                    if autoSitBlobConn then autoSitBlobConn:Disconnect() autoSitBlobConn = nil end
                    return
                end

                local c = plr.Character
                local h = c and c:FindFirstChild("Humanoid")
                if not h then return end

                if not currentBlobSeat or not currentBlobSeat.Parent then
                    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    local b = inv and inv:FindFirstChild("CreatureBlobman")
                    if b then
                        local s = b:FindFirstChild("VehicleSeat")
                        if s then currentBlobSeat = s end
                    end
                end

                if currentBlobSeat and currentBlobSeat.Parent then
                    if h.SeatPart ~= currentBlobSeat then
                        currentBlobSeat:Sit(h)
                    end
                end
            end)
        end)
    end
})

local wushaKickActive = false

KickRight:AddToggle("WushaKickToggle", {
    Text = "blob Kick",
    Default = false,
    Callback = function(Value)
        wushaKickActive = Value

        if not Value then
            wushaKickActive = false
            return
        end

        if not selectedKickTarget then
            wushaKickActive = false
            if Toggles and Toggles.WushaKickToggle then Toggles.WushaKickToggle:SetValue(false) end
            return
        end

        task.spawn(function()
            local GE = ReplicatedStorage:WaitForChild("GrabEvents")
            local SetNetworkOwner = GE:WaitForChild("SetNetworkOwner")
            local CreateGrabLine = GE:WaitForChild("CreateGrabLine")
            local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine")

            local function GetBlobData()
                local char = plr.Character
                local hum = char and char:FindFirstChild("Humanoid")
                local seat = hum and hum.SeatPart
                if not seat or seat.Parent.Name ~= "CreatureBlobman" then return nil end

                local blob = seat.Parent
                local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
                local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                local CG = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
                local CD = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
                local R_Det = blob:FindFirstChild("RightDetector")
                local L_Det = blob:FindFirstChild("LeftDetector")
                local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld"))
                local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChildWhichIsA("Weld"))

                if not blobRoot or not CG or not CD or not R_Det then return nil end

                return {
                    blob = blob,
                    root = blobRoot,
                    CG = CG,
                    CD = CD,
                    R_Det = R_Det,
                    L_Det = L_Det,
                    R_Weld = R_Weld,
                    L_Weld = L_Weld
                }
            end

            while wushaKickActive do
                local blobData = GetBlobData()

                if not blobData then
                    task.wait(0.3)
                    continue
                end

                local target = GetTargetFromString(selectedKickTarget)
                if not target or not target.Parent then
                    task.wait(0.3)
                    continue
                end

                local tChar = target.Character
                local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum = tChar and tChar:FindFirstChild("Humanoid")

                if not tRoot or not tHum or tHum.Health <= 0 then
                    task.wait(0.3)
                    continue
                end

                local savedPos = blobData.root.CFrame

                local bringStart = tick()
                while tick() - bringStart < 0.25 do
                    if not wushaKickActive then break end

                    blobData = GetBlobData()
                    if not blobData then break end

                    tChar = target.Character
                    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    if not tRoot then break end

                    blobData.root.CFrame = tRoot.CFrame
                    blobData.root.Velocity = Vector3.zero

                    pcall(function()
                        blobData.CG:FireServer(blobData.R_Det, tRoot, blobData.R_Weld)
                        CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        SetNetworkOwner:FireServer(tRoot, blobData.root.CFrame)
                    end)
                    RunService.Heartbeat:Wait()
                end

                if not wushaKickActive then break end

                blobData = GetBlobData()
                if blobData then
                    blobData.root.CFrame = savedPos
                    blobData.root.Velocity = Vector3.zero
                end

                while wushaKickActive do
                    blobData = GetBlobData()
                    if not blobData then break end

                    target = GetTargetFromString(selectedKickTarget)
                    if not target or not target.Parent then break end

                    tChar = target.Character
                    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    tHum = tChar and tChar:FindFirstChild("Humanoid")

                    if not tRoot or not tHum then break end
                    if tHum.Health <= 0 then break end

                    local currentPos = blobData.root.CFrame
                    local lockPos = currentPos * CFrame.new(0, 23, 0)

                    tRoot.CFrame = lockPos
                    tRoot.Velocity = Vector3.zero
                    tRoot.RotVelocity = Vector3.zero

                    tHum.PlatformStand = true
                    tHum.Sit = true

                    pcall(function()
                        SetNetworkOwner:FireServer(tRoot, lockPos)
                        blobData.CD:FireServer(blobData.R_Weld)
                        DestroyGrabLine:FireServer(tRoot)
                    end)

                    RunService.Heartbeat:Wait()

                    pcall(function()
                        blobData.CG:FireServer(blobData.R_Det, tRoot, blobData.R_Weld)
                        CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    end)

                    local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                    if spawned then
                        local function yeetPart(part)
                            if part then
                                pcall(function() SetNetworkOwner:FireServer(part, part.CFrame) end)
                                local owner = part:FindFirstChild("PartOwner")
                                if owner and owner.Value == plr.Name then
                                    part.CFrame = CFrame.new(0, 10000, 0)
                                end
                            end
                        end
                        local kunai = spawned:FindFirstChild("NinjaKunai")
                        if kunai then yeetPart(kunai:FindFirstChild("SoundPart")) end
                        local shuriken = spawned:FindFirstChild("NinjaShuriken")
                        if shuriken then yeetPart(shuriken:FindFirstChild("SoundPart")) end
                    end

                    RunService.Heartbeat:Wait()
                end

                if not wushaKickActive then break end

                if target and target.Parent then
                    local waitStart = tick()
                    while tick() - waitStart < 2 do
                        if not wushaKickActive then break end
                        local tc = target.Character
                        local tr = tc and tc:FindFirstChild("HumanoidRootPart")
                        local th = tc and tc:FindFirstChild("Humanoid")
                        if tr and th and th.Health > 0 then break end
                        task.wait(0.05)
                    end
                end
            end

            wushaKickActive = false
            if Toggles and Toggles.WushaKickToggle then Toggles.WushaKickToggle:SetValue(false) end
        end)
    end
})

local selectedBlobTarget = nil
local blobKillRadius = 25
local persistentBlobKill = false
local currentBlob = nil
local blobSeat = nil

BlobmanRight:AddDropdown("BlobTargetDropdown", {
    Values = GetPlayerList(), 
    Default = 1, 
    Text = "Select Target", 
    Callback = function(Value) 
        selectedBlobTarget = Value 
    end
})

task.spawn(function()
    while task.wait(1.5) do
        xpcall(function()
            Options.BlobTargetDropdown:SetValues(GetPlayerList())
        end, function() end)
    end
end)

BlobmanRight:AddSlider("BlobKillRadiusSlider", {
    Text = "Attack Radius",
    Default = 25,
    Min = 10,
    Max = 60,
    Rounding = 0,
    Suffix = " studs",
    Callback = function(Value)
        blobKillRadius = Value
    end
})

local function GetBlobman()
    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    if inv then
        local blob = inv:FindFirstChild("CreatureBlobman")
        if blob then
            currentBlob = blob
            blobSeat = blob:FindFirstChild("VehicleSeat")
            return blob
        end
    end
    return nil
end

local function SpawnAndSitOnBlob()
    local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end

    xpcall(function()
        spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame * CFrame.new(0, 5, 15), Vector3.zero)
    end, function() end)

    task.wait(0.8)

    local blob = GetBlobman()
    if not blob or not blobSeat then return false end

    local hum = plr.Character and plr.Character:FindFirstChild("Humanoid")
    if hum then
        blobSeat:Sit(hum)
        task.wait(0.4)
        if blobSeat.Occupant ~= hum then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
            task.wait(0.2)
            blobSeat:Sit(hum)
        end
    end
    return true
end

local function KillTarget(target)
    if not target or not target.Character then return end
    local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
    local tHum = target.Character:FindFirstChild("Humanoid")
    if not tHRP or not tHum then return end

    local blob = GetBlobman()
    if not blob or not blobSeat then return end

    local leftDetector = blob:FindFirstChild("LeftDetector")
    local rightDetector = blob:FindFirstChild("RightDetector")
    local scriptFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    local grabEvent = scriptFolder and scriptFolder:FindFirstChild("CreatureGrab")
    local releaseEvent = scriptFolder and scriptFolder:FindFirstChild("CreatureRelease")

    if grabEvent and releaseEvent and (leftDetector or rightDetector) then
        for i = 1, 2 do
            local detector = (i == 1) and leftDetector or rightDetector
            if detector then
                xpcall(function()
                    grabEvent:FireServer(detector, tHRP, detector:FindFirstChild("LeftWeld") or detector:FindFirstChild("RightWeld"))
                end, function() end)
                task.wait(0.06)
                xpcall(function() releaseEvent:FireServer(detector) end, function() end)
                task.wait(0.06)
            end
        end
        xpcall(function() tHum:ChangeState(Enum.HumanoidStateType.Dead) end, function() end)
    end
end

local BlobmanRight = Tabs.Blobman:AddRightGroupbox("Blob Kill")

local selectedBlobTarget = nil
local persistentBlobKill = false

BlobmanRight:AddDropdown("BlobTargetDropdown", {
    Values = GetPlayerList(),
    Default = 1,
    Text = "Select Target",
    Callback = function(Value)
        selectedBlobTarget = Value
    end
})

task.spawn(function()
    while task.wait(1.5) do
        xpcall(function()
            Options.BlobTargetDropdown:SetValues(GetPlayerList())
        end, function() end)
    end
end)

BlobmanRight:AddToggle("PersistentBlobKillToggle", {
    Text = "Loop kill",
    Default = false,
    Callback = function(Value)
        persistentBlobKill = Value
        if not Value then return end

        task.spawn(function()
            while persistentBlobKill do
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChild("Humanoid")

                if not hrp or not hum then
                    task.wait(1)
                    continue
                end

                local mytoys = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                if not mytoys then
                    task.wait(1)
                    continue
                end

                local savedPosition = hrp.CFrame
                local blobSpawned = false
                local blobDone = false
                local toyConnection

                toyConnection = mytoys.ChildAdded:Connect(function(toy)
                    if toy.Name ~= "CreatureBlobman" then return end
                    if blobSpawned then return end
                    blobSpawned = true

                    if toyConnection then
                        toyConnection:Disconnect()
                    end

                    local vehsit = toy:WaitForChild("VehicleSeat", 2)
                    local leftDetector = toy:WaitForChild("LeftDetector", 2)
                    local rightDetector = toy:WaitForChild("RightDetector", 2)
                    local leftWeld = leftDetector and leftDetector:WaitForChild("LeftWeld", 2)
                    local rightWeld = rightDetector and rightDetector:WaitForChild("RightWeld", 2)
                    local seatownerscript = toy:WaitForChild("BlobmanSeatAndOwnerScript", 2)
                    local grab = seatownerscript and seatownerscript:WaitForChild("CreatureGrab", 2)
                    local release = seatownerscript and seatownerscript:WaitForChild("CreatureRelease", 2)

                    if not (vehsit and leftDetector and rightDetector and leftWeld and rightWeld and grab and release) then
                        blobDone = true
                        return
                    end

                    local sitStart = tick()
                    while vehsit.Occupant ~= hum and tick() - sitStart < 2 do
                        if not persistentBlobKill then blobDone = true return end
                        vehsit:Sit(hum)
                        task.wait()
                    end
                    task.wait(0.1)

                    while persistentBlobKill do
                        char = plr.Character
                        hrp = char and char:FindFirstChild("HumanoidRootPart")
                        hum = char and char:FindFirstChild("Humanoid")

                        if not hrp or not hum then break end
                        if not hum.SeatPart or not hum.SeatPart.Parent or hum.SeatPart.Parent.Name ~= "CreatureBlobman" then break end

                        local target = GetTargetFromString(selectedBlobTarget)
                        if not target or not target.Parent then
                            task.wait(0.5)
                            continue
                        end

                        local targetChar = target.Character
                        local targetHRP = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
                        local targetHum = targetChar and targetChar:FindFirstChild("Humanoid")

                        if not targetHRP or not targetHum or targetHum.Health <= 0 then
                            task.wait(0.5)
                            continue
                        end

                        local distance = (hrp.Position - targetHRP.Position).Magnitude

                        if distance > 30 then
                            hrp.CFrame = targetHRP.CFrame * CFrame.new(0, 20, 0)
                            hrp.Velocity = Vector3.zero
                            hrp.RotVelocity = Vector3.zero
                            task.wait(0.2)
                        end

                        for i = 1, 2 do
                            if not persistentBlobKill then break end

                            targetChar = target.Character
                            targetHRP = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
                            targetHum = targetChar and targetChar:FindFirstChild("Humanoid")

                            if not targetHRP or not targetHum or targetHum.Health <= 0 then break end

                            local detector = (i == 1) and leftDetector or rightDetector
                            local weld = (i == 1) and leftWeld or rightWeld

                            if detector and weld then
                                pcall(function() grab:FireServer(detector, targetHRP, weld) end)
                                task.wait(0.05)
                                pcall(function() release:FireServer(weld) end)
                                task.wait(0.05)
                                if targetHum and targetHum.Health > 0 then
                                    pcall(function() targetHum:ChangeState(Enum.HumanoidStateType.Dead) end)
                                end
                            end
                        end

                        task.wait(0.3)
                    end

                    if hrp and hrp.Parent then
                        hrp.CFrame = savedPosition
                        hrp.Velocity = Vector3.zero
                        hrp.RotVelocity = Vector3.zero
                    end

                    pcall(function() destroyToyRemote:FireServer(toy) end)
                    blobDone = true
                end)

                local existingBlob = mytoys:FindFirstChild("CreatureBlobman")
                if existingBlob then
                    pcall(function() destroyToyRemote:FireServer(existingBlob) end)
                    task.wait(0.5)
                end

                pcall(function()
                    spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.zero)
                end)

                local spawnTimeout = tick()
                while not blobSpawned and not blobDone and persistentBlobKill and tick() - spawnTimeout < 5 do
                    task.wait(0.1)
                end

                if not blobSpawned and toyConnection then
                    toyConnection:Disconnect()
                end

                while not blobDone and persistentBlobKill do
                    task.wait(0.1)
                end

                if not persistentBlobKill then
                    if toyConnection then pcall(function() toyConnection:Disconnect() end) end
                    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    local blob = inv and inv:FindFirstChild("CreatureBlobman")
                    if blob then pcall(function() destroyToyRemote:FireServer(blob) end) end
                    break
                end

                task.wait(1)
            end
        end)
    end
})

local VisualsLeft = Tabs.Visuals:AddLeftGroupbox("Shaders")
local VisualsRight = Tabs.Visuals:AddRightGroupbox("Other")

local Lighting = game:GetService("Lighting")

local oldFogEnd = Lighting.FogEnd
local oldFogStart = Lighting.FogStart

local oldAtmo = Lighting:FindFirstChildOfClass("Atmosphere")
local oldAtmoSettings = oldAtmo and {
    Density = oldAtmo.Density,
    Color = oldAtmo.Color,
    Decay = oldAtmo.Decay,
    Glare = oldAtmo.Glare,
    Haze = oldAtmo.Haze
} or nil

local function SetShader(State)
    Lighting.FogStart = State and 0 or oldFogStart
    Lighting.FogEnd = State and 100000 or oldFogEnd

    local Bloom = Lighting:FindFirstChild("SAHAR_Bloom")
    if State then
        if not Bloom then
            Bloom = Instance.new("BloomEffect")
            Bloom.Name = "SAHAR_Bloom"
            Bloom.Parent = Lighting
        end
        Bloom.Intensity = 1.5
        Bloom.Size = 30
        Bloom.Threshold = 0.8
        Bloom.Enabled = true
    else
        if Bloom then Bloom:Destroy() end
    end

    local Sun = Lighting:FindFirstChild("SAHAR_SunRays")
    if State then
        if not Sun then
            Sun = Instance.new("SunRaysEffect")
            Sun.Name = "SAHAR_SunRays"
            Sun.Parent = Lighting
        end
        Sun.Intensity = 0.2
        Sun.Spread = 0.3
        Sun.Enabled = true
    else
        if Sun then Sun:Destroy() end
    end

    local CC = Lighting:FindFirstChild("SAHAR_ColorCorrection")
    if State then
        if not CC then
            CC = Instance.new("ColorCorrectionEffect")
            CC.Name = "SAHAR_ColorCorrection"
            CC.Parent = Lighting
        end
        CC.Saturation = 0.4
        CC.Contrast = 0.3
        CC.Brightness = 0.05
        CC.TintColor = Color3.fromRGB(200, 210, 255)
        CC.Enabled = true
    else
        if CC then CC:Destroy() end
    end

    local Atmo = Lighting:FindFirstChildOfClass("Atmosphere")
    if State then
        if Atmo then
            Atmo.Density = 0.3
            Atmo.Color = Color3.fromRGB(150, 170, 220)
            Atmo.Decay = Color3.fromRGB(90, 110, 180)
            Atmo.Glare = 0
            Atmo.Haze = 1
        end
    else
        if Atmo and oldAtmoSettings then
            Atmo.Density = oldAtmoSettings.Density
            Atmo.Color = oldAtmoSettings.Color
            Atmo.Decay = oldAtmoSettings.Decay
            Atmo.Glare = oldAtmoSettings.Glare
            Atmo.Haze = oldAtmoSettings.Haze
        end
    end
end

SetShader(true)

VisualsLeft:AddToggle("ShaderToggle", {
    Text = "Shader",
    Default = true,
    Callback = function(Value)
        SetShader(Value)
    end
})

VisualsLeft:AddLabel("Stats Text"):AddColorPicker("StatsTextColor", {
    Default = Color3.fromRGB(169, 255, 226),
    Title = "Stats Text Color",
    Transparency = 0,
    Callback = function(Value)
        fpsLabel.TextColor3 = Value
        pingLabel.TextColor3 = Value
    end
})

VisualsLeft:AddLabel("Stats Background"):AddColorPicker("StatsBGColor", {
    Default = Color3.fromRGB(10, 40, 38),
    Title = "Stats Background Color",
    Transparency = 0,
    Callback = function(Value)
        statsFrame.BackgroundColor3 = Value
    end
})

VisualsLeft:AddSlider("StatsScaleSlider", {
    Text = "Stats Size",
    Default = 100,
    Min = 50,
    Max = 200,
    Rounding = 0,
    Compact = false,
    Suffix = "%",
    Callback = function(Value)
        local scale = Value / 100
        statsFrame.Size = UDim2.new(0, math.floor(140 * scale), 0, math.floor(70 * scale))
        titleLabel.TextSize = math.floor(11 * scale)
        fpsLabel.TextSize = math.floor(12 * scale)
        pingLabel.TextSize = math.floor(12 * scale)
        grabLabel.TextSize = math.floor(12 * scale)
        fpsLabel.Position = UDim2.new(0, math.floor(5 * scale), 0, math.floor(18 * scale))
        pingLabel.Position = UDim2.new(0, math.floor(5 * scale), 0, math.floor(34 * scale))
        grabLabel.Position = UDim2.new(0, math.floor(5 * scale), 0, math.floor(50 * scale))
    end
})

local palletNeonEnabled = false
local palletNeonConn = nil
local palletNeonData = {}

VisualsRight:AddToggle("PalletNeonToggle", {
    Text = "Neon Pallet",
    Default = false,
    Callback = function(Value)
        palletNeonEnabled = Value

        local function ApplyNeonPart(part)
            if not part:IsA("BasePart") then return end
            if not palletNeonData[part] then
                palletNeonData[part] = {
                    Material = part.Material
                }
            end
            part.Material = Enum.Material.Neon
            local highlight = part:FindFirstChild("SAHAR_PalletHighlight")
            if not highlight then
                highlight = Instance.new("Highlight")
                highlight.Name = "SAHAR_PalletHighlight"
                highlight.FillTransparency = 1
                highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
                highlight.OutlineTransparency = 0
                highlight.Parent = part
            end
        end

        local function ApplyNeonPallet(pallet)
            for _, part in pairs(pallet:GetDescendants()) do
                ApplyNeonPart(part)
            end
            if pallet:IsA("BasePart") then
                ApplyNeonPart(pallet)
            end
        end

        local function IsPallet(obj)
            if obj.Name == "PalletLightBrown" then return true end
            local current = obj
            while current do
                if current.Name == "PalletLightBrown" then return true end
                current = current.Parent
            end
            return false
        end

        if Value then
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name == "PalletLightBrown" then
                    ApplyNeonPallet(obj)
                end
            end

            palletNeonConn = workspace.DescendantAdded:Connect(function(obj)
                if not palletNeonEnabled then return end
                task.wait(0.1)
                if obj.Name == "PalletLightBrown" then
                    ApplyNeonPallet(obj)
                elseif obj:IsA("BasePart") and IsPallet(obj) then
                    ApplyNeonPart(obj)
                end
            end)
        else
            if palletNeonConn then
                palletNeonConn:Disconnect()
                palletNeonConn = nil
            end

            for part, data in pairs(palletNeonData) do
                if part and part.Parent then
                    part.Material = data.Material
                    local highlight = part:FindFirstChild("SAHAR_PalletHighlight")
                    if highlight then highlight:Destroy() end
                end
            end

            for _, obj in pairs(workspace:GetDescendants()) do
                if obj:IsA("BasePart") then
                    local highlight = obj:FindFirstChild("SAHAR_PalletHighlight")
                    if highlight then
                        highlight:Destroy()
                        obj.Material = Enum.Material.Wood
                    end
                end
            end

            palletNeonData = {}
        end
    end
})

local bhkColor = Color3.fromRGB(255, 0, 0)
local bhkData = {}
local bhkConn = nil

VisualsRight:AddToggle("BHKHighlightToggle", {
    Text = "Custom Kick",
    Default = false,
    Callback = function(Value)
        if Value then
            local function ApplyBHK(obj)
                if obj.Name == "BlackHoleKick" and obj:IsA("BasePart") then
                    local highlight = obj:FindFirstChild("SAHAR_BHKHighlight")
                    if not highlight then
                        highlight = Instance.new("Highlight")
                        highlight.Name = "SAHAR_BHKHighlight"
                        highlight.FillColor = bhkColor
                        highlight.FillTransparency = 0.5
                        highlight.OutlineColor = bhkColor
                        highlight.OutlineTransparency = 0
                        highlight.Parent = obj
                    end
                    bhkData[obj] = highlight
                end
            end

            for _, v in pairs(workspace:GetDescendants()) do
                ApplyBHK(v)
            end

            bhkConn = workspace.DescendantAdded:Connect(function(obj)
                task.wait()
                ApplyBHK(obj)
            end)
        else
            if bhkConn then
                bhkConn:Disconnect()
                bhkConn = nil
            end

            for part, highlight in pairs(bhkData) do
                if highlight and highlight.Parent then
                    highlight:Destroy()
                end
            end
            bhkData = {}
        end
    end
})

VisualsRight:AddLabel("Kick colour"):AddColorPicker("BHKColorPicker", {
    Default = Color3.fromRGB(255, 0, 0),
    Title = "Kick colour",
    Transparency = 0,
    Callback = function(Value)
        bhkColor = Value
        for _, highlight in pairs(bhkData) do
            if highlight and highlight.Parent then
                highlight.FillColor = Value
                highlight.OutlineColor = Value
            end
        end
    end
})

MiscLeft:AddToggle("AntiBarrierToggle", {
    Text = "Anti Barrier",
    Default = false,
    Callback = function(Value)
        for _, v in pairs(workspace.Plots:GetChildren()) do
            local plotArea = v:FindFirstChild("PlotArea")
            if plotArea then
                plotArea.CanQuery = not Value
            end
            local barrier = v:FindFirstChild("Barrier")
            if barrier then
                for _, b in pairs(barrier:GetChildren()) do
                    if b:IsA("BasePart") then
                        b.CanCollide = not Value
                        b.CanQuery = not Value
                        b.CanTouch = not Value
                    end
                end
            end
        end
    end
})

local stealAllActive = false

MiscLeft:AddToggle("StealAllToggle", {
    Text = "Steal All Toys",
    Default = false,
    Callback = function(Value)
        stealAllActive = Value
        if not Value then return end

        task.spawn(function()
            local SetNetworkOwner = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
            
            while stealAllActive do
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if hrp then
                    local frontCF = hrp.CFrame * CFrame.new(0, 0, -7)
                    
                    for _, targetPlayer in pairs(Players:GetPlayers()) do
                        if targetPlayer == plr then continue end
                        
                        local folder = workspace:FindFirstChild(targetPlayer.Name .. "SpawnedInToys")
                        if folder then
                            for _, toy in pairs(folder:GetChildren()) do
                                local part = toy:IsA("BasePart") and toy or toy.PrimaryPart or toy:FindFirstChildWhichIsA("BasePart", true)
                                
                                if part and not part.Anchored then
                                    pcall(function()
                                        if SetNetworkOwner then
                                            SetNetworkOwner:FireServer(part, part.CFrame)
                                        end
                                        
                                        local offset = Vector3.new(
                                            math.random(-40, 40) / 10,
                                            math.random(0, 50) / 10,
                                            math.random(-40, 40) / 10
                                        )
                                        
                                        part.CFrame = CFrame.new(frontCF.Position + offset)
                                        part.AssemblyLinearVelocity = Vector3.zero
                                        part.AssemblyAngularVelocity = Vector3.zero
                                    end)
                                end
                            end
                        end
                    end
                end
                task.wait(0.05)
            end
        end)
    end
})

local infLineActive = false

MiscLeft:AddToggle("InfLineToggle", {
    Text = "Infinite Grab Line",
    Default = false,
    Callback = function(Value)
        infLineActive = Value
        if not Value then return end

        task.spawn(function()
            local extendLine = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("ExtendGrabLine")
            if not extendLine then return end

            while infLineActive do
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    for _, v in pairs(hrp:GetChildren()) do
                        if v:IsA("RopeConstraint") or v:IsA("RodConstraint") then
                            v.Length = 9999
                        end
                    end

                    for _, v in pairs(workspace:GetDescendants()) do
                        if (v:IsA("RopeConstraint") or v:IsA("RodConstraint")) and v.Length < 9999 then
                            local att0 = v.Attachment0
                            local att1 = v.Attachment1
                            if att0 and att1 then
                                local p0 = att0.Parent
                                local p1 = att1.Parent
                                if p0 and p1 then
                                    if p0:IsDescendantOf(char) or p1:IsDescendantOf(char) then
                                        v.Length = 9999
                                    end
                                end
                            end
                        end
                    end
                end
                task.wait(0.1)
            end
        end)
    end
})

local coordsGui = Instance.new("ScreenGui")
coordsGui.Name = "SAHAR_Coords"
coordsGui.IgnoreGuiInset = true
coordsGui.ResetOnSpawn = false
coordsGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
coordsGui.DisplayOrder = 2147483646
coordsGui.Parent = CoreGui

local coordsFrame = Instance.new("Frame")
coordsFrame.Size = UDim2.new(0, 250, 0, 35)
coordsFrame.Position = UDim2.new(0.5, -125, 1, -45)
coordsFrame.BackgroundColor3 = Color3.fromRGB(10, 40, 38)
coordsFrame.BackgroundTransparency = 0.3
coordsFrame.BorderSizePixel = 0
coordsFrame.Active = true
coordsFrame.Draggable = true
coordsFrame.Visible = false
coordsFrame.Parent = coordsGui

local coordsCorner = Instance.new("UICorner")
coordsCorner.CornerRadius = UDim.new(0, 6)
coordsCorner.Parent = coordsFrame

local coordsStroke = Instance.new("UIStroke")
coordsStroke.Color = Color3.fromRGB(135, 252, 245)
coordsStroke.Thickness = 1
coordsStroke.Transparency = 0.5
coordsStroke.Parent = coordsFrame

local coordsLabel = Instance.new("TextLabel")
coordsLabel.Size = UDim2.new(1, -10, 1, 0)
coordsLabel.Position = UDim2.new(0, 5, 0, 0)
coordsLabel.BackgroundTransparency = 1
coordsLabel.Text = "X: 0 | Y: 0 | Z: 0"
coordsLabel.TextColor3 = Color3.fromRGB(169, 255, 226)
coordsLabel.Font = Enum.Font.GothamBold
coordsLabel.TextSize = 14
coordsLabel.TextXAlignment = Enum.TextXAlignment.Center
coordsLabel.Parent = coordsFrame

local coordsEnabled = false
local coordsUpdateConn = nil

MiscLeft:AddToggle("CoordsToggle", {
    Text = "Show Coordinates",
    Default = false,
    Callback = function(Value)
        coordsEnabled = Value
        coordsFrame.Visible = Value

        if coordsUpdateConn then
            coordsUpdateConn:Disconnect()
            coordsUpdateConn = nil
        end

        if Value then
            coordsUpdateConn = RunService.Heartbeat:Connect(function()
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local pos = hrp.Position
                    coordsLabel.Text = string.format("X: %d | Y: %d | Z: %d", math.floor(pos.X), math.floor(pos.Y), math.floor(pos.Z))
                else
                    coordsLabel.Text = "X: --- | Y: --- | Z: ---"
                end
            end)
        end
    end
})

Library:OnUnload(function()
    if coordsGui then coordsGui:Destroy() end
    if coordsUpdateConn then coordsUpdateConn:Disconnect() end
end)

local peopleConnectActive = false
local peopleConnectRadius = 40

MiscLeft:AddSlider("PeopleConnectRadiusSlider", {
    Text = "Connect Radius",
    Default = 40,
    Min = 5,
    Max = 100,
    Rounding = 0,
    Compact = false,
    Suffix = " studs",
    Callback = function(Value)
        peopleConnectRadius = Value
    end
})

MiscLeft:AddToggle("PeopleConnectToggle", {
    Text = "People Connect",
    Default = false,
    Callback = function(Value)
        peopleConnectActive = Value

        if not Value then
            for _, target in pairs(Players:GetPlayers()) do
                if target ~= plr and target.Character then
                    local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        if tHRP:FindFirstChild("SAHAR_ConnectBP") then tHRP.SAHAR_ConnectBP:Destroy() end
                        if tHRP:FindFirstChild("SAHAR_ConnectBG") then tHRP.SAHAR_ConnectBG:Destroy() end
                    end
                end
            end
            return
        end

        task.spawn(function()
            local DestroyLine = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine")
            local networkTick = {}

            while peopleConnectActive do
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp then task.wait(0.1) continue end

                local myPos = hrp.Position

                for _, target in pairs(Players:GetPlayers()) do
                    if target == plr then continue end
                    if not peopleConnectActive then break end

                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    if not tHRP then continue end

                    local dist = (myPos - tHRP.Position).Magnitude

                    if dist <= peopleConnectRadius then
                        local now = tick()
                        if not networkTick[target] or now - networkTick[target] > 1 then
                            networkTick[target] = now
                            invis_touch(tHRP, tHRP.CFrame)
                            if DestroyLine then DestroyLine:FireServer(tHRP) end
                        end

                        local bp = tHRP:FindFirstChild("SAHAR_ConnectBP")
                        if not bp then
                            bp = Instance.new("BodyPosition")
                            bp.Name = "SAHAR_ConnectBP"
                            bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bp.D = 200
                            bp.P = 20000
                            bp.Parent = tHRP
                        end
                        bp.Position = myPos + Vector3.new(0, 13, 0)

                        local bg = tHRP:FindFirstChild("SAHAR_ConnectBG")
                        if not bg then
                            bg = Instance.new("BodyGyro")
                            bg.Name = "SAHAR_ConnectBG"
                            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            bg.Parent = tHRP
                        end
                        bg.CFrame = hrp.CFrame
                    else
                        if tHRP:FindFirstChild("SAHAR_ConnectBP") then tHRP.SAHAR_ConnectBP:Destroy() end
                        if tHRP:FindFirstChild("SAHAR_ConnectBG") then tHRP.SAHAR_ConnectBG:Destroy() end
                        networkTick[target] = nil
                    end
                end
                task.wait()
            end

            for _, target in pairs(Players:GetPlayers()) do
                if target ~= plr and target.Character then
                    local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        if tHRP:FindFirstChild("SAHAR_ConnectBP") then tHRP.SAHAR_ConnectBP:Destroy() end
                        if tHRP:FindFirstChild("SAHAR_ConnectBG") then tHRP.SAHAR_ConnectBG:Destroy() end
                    end
                end
            end
        end)
    end
})

MiscLeft:AddToggle("AntiVoidDestroyToggle", {
    Text = "Anti Void Destroy", Default = false,
    Callback = function(Value) if Value then workspace.FallenPartsDestroyHeight = 0/0 else workspace.FallenPartsDestroyHeight = -100 end end
})

local espData = { pcldBoxes = {}, pcldConns = {}, nameBillboards = {}, nameConns = {}, pcldEnabled = false, nameEnabled = false, pcldColor = Color3.fromRGB(255, 255, 255) }

local function IsPCLD(obj)
    if not obj:IsA("BasePart") then return false end
    local name = obj.Name:lower()
    return name == "playercharacterlocationdetector" or name == "partesp" or name == "playercharacterlocationdetectorfake"
end

local function CreatePCLDBox(obj)
    if not obj or not obj.Parent or espData.pcldBoxes[obj] then return end
    local box = Instance.new("BoxHandleAdornment")
    box.Name = "SAHAR_PCLD_Box" box.Adornee = obj box.AlwaysOnTop = true box.ZIndex = 10 box.Size = obj.Size * 1.05 box.Transparency = 0.75 box.Color3 = espData.pcldColor box.Parent = CoreGui
    espData.pcldBoxes[obj] = box
    local conn = obj.AncestryChanged:Connect(function(_, parent) if not parent and espData.pcldBoxes[obj] then espData.pcldBoxes[obj]:Destroy() espData.pcldBoxes[obj] = nil end end)
    table.insert(espData.pcldConns, conn)
end

local function ClearPCLDBoxes()
    for _, box in pairs(espData.pcldBoxes) do xpcall(function() box:Destroy() end, function() end) end espData.pcldBoxes = {}
    for _, conn in pairs(espData.pcldConns) do xpcall(function() conn:Disconnect() end, function() end) end espData.pcldConns = {}
end

local function UpdatePCLDColor() for _, box in pairs(espData.pcldBoxes) do xpcall(function() box.Color3 = espData.pcldColor end, function() end) end end

MiscRight:AddToggle("PCLDViewToggle", {
    Text = "PCLD View", Default = false,
    Callback = function(Value)
        espData.pcldEnabled = Value
        if Value then
            for _, obj in pairs(workspace:GetDescendants()) do if IsPCLD(obj) then CreatePCLDBox(obj) end end
            local addConn = workspace.DescendantAdded:Connect(function(obj) if espData.pcldEnabled and IsPCLD(obj) then CreatePCLDBox(obj) end end)
            table.insert(espData.pcldConns, addConn)
        else ClearPCLDBoxes() end
    end
})

MiscRight:AddLabel("PCLD Color"):AddColorPicker("PCLDColorPicker", {
    Default = Color3.fromRGB(255, 255, 255), Title = "PCLD Color", Transparency = 0,
    Callback = function(Value) espData.pcldColor = Value UpdatePCLDColor() end
})

local function CreateNameESP(player)
    if player == plr or espData.nameBillboards[player] then return end
    local function setupBillboard(char)
        if espData.nameBillboards[player] then xpcall(function() espData.nameBillboards[player]:Destroy() end, function() end) espData.nameBillboards[player] = nil end
        local head = char:WaitForChild("Head", 5) if not head then return end
        local bb = Instance.new("BillboardGui") bb.Name = "SAHAR_NameESP" bb.Adornee = head bb.Size = UDim2.new(0,200,0,50) bb.StudsOffset = Vector3.new(0,3,0) bb.AlwaysOnTop = true bb.Parent = CoreGui
        local label = Instance.new("TextLabel") label.Size = UDim2.new(1,0,1,0) label.BackgroundTransparency = 1 label.Text = player.DisplayName .. "/" .. player.Name label.TextColor3 = Color3.fromRGB(255,255,255) label.TextStrokeTransparency = 0.5 label.TextStrokeColor3 = Color3.fromRGB(0,0,0) label.Font = Enum.Font.GothamBold label.TextSize = 14 label.TextScaled = false label.Parent = bb
        espData.nameBillboards[player] = bb
    end
    if player.Character then setupBillboard(player.Character) end
    local conn = player.CharacterAdded:Connect(function(char) if espData.nameEnabled then task.wait(0.5) setupBillboard(char) end end)
    table.insert(espData.nameConns, conn)
end

local function ClearNameESP()
    for _, bb in pairs(espData.nameBillboards) do xpcall(function() bb:Destroy() end, function() end) end espData.nameBillboards = {}
    for _, conn in pairs(espData.nameConns) do xpcall(function() conn:Disconnect() end, function() end) end espData.nameConns = {}
end

local namePlayerAddedConn = nil
MiscRight:AddToggle("NameESPToggle", {
    Text = "Name ESP", Default = false,
    Callback = function(Value)
        espData.nameEnabled = Value
        if Value then
            for _, p in pairs(Players:GetPlayers()) do CreateNameESP(p) end
            namePlayerAddedConn = Players.PlayerAdded:Connect(function(p) if espData.nameEnabled then task.wait(1) CreateNameESP(p) end end)
            local removeConn = Players.PlayerRemoving:Connect(function(p) if espData.nameBillboards[p] then xpcall(function() espData.nameBillboards[p]:Destroy() end, function() end) espData.nameBillboards[p] = nil end end)
            table.insert(espData.nameConns, removeConn)
        else
            if namePlayerAddedConn then namePlayerAddedConn:Disconnect() namePlayerAddedConn = nil end
            ClearNameESP()
        end
    end
})

PlayerRight:AddSlider("CameraSensSlider", {
    Text = "Camera Sensitivity",
    Default = 1,
    Min = 0.1,
    Max = 5,
    Rounding = 1,
    Compact = false,
    Callback = function(Value)
        UserInputService.MouseDeltaSensitivity = Value
    end
})

local speedActive = false
local speedValue = 50
PlayerLeft:AddSlider("SpeedSlider", { Text = "Speed Value", Default = 50, Min = 0, Max = 500, Rounding = 0, Compact = false, Callback = function(Value) speedValue = Value end })
PlayerLeft:AddToggle("SpeedToggle", { Text = "Speed", Default = false, Callback = function(on) speedActive = on end }):AddKeyPicker("SpeedBind", { Default = "X", SyncToggleState = true, Mode = "Toggle", Text = "Speed", NoUI = false })

RunService.Heartbeat:Connect(function(deltaTime)
    if speedActive then
        local char = plr.Character local hum = char and char:FindFirstChild("Humanoid") local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hum and hrp and hum.MoveDirection.Magnitude > 0 then
            local currentCFrame = hrp.CFrame
            hrp.CFrame = CFrame.new(currentCFrame.Position + (hum.MoveDirection * speedValue * deltaTime)) * CFrame.Angles(currentCFrame:ToEulerAnglesYXZ())
        end
    end
end)

MiscLeft:AddToggle("WaterWalkToggle", {
    Text = "Water Walk",
    Default = false,
    Callback = function(State)
        for _, v in workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren() do
            if v:IsA("BasePart") then
                v.CanCollide = State
                v.Transparency = State and 0.5 or 0
                v.Material = State and Enum.Material.Neon or Enum.Material.Water
            end
        end
    end
})

local freeCamActive = false
local freeCamPart = nil
local freeCamSpeed = 1
local controls = require(plr.PlayerScripts:WaitForChild("PlayerModule")):GetControls()

PlayerRight:AddToggle("FreeCameraToggle", {
    Text = "Free camera mode",
    Default = false,
    Callback = function(Value)
        freeCamActive = Value
        local camera = workspace.CurrentCamera
        local char = plr.Character

        if Value then
            if not char or not char:FindFirstChild("HumanoidRootPart") then 
                Toggles.FreeCameraToggle:SetValue(false)
                return 
            end
            
            controls:Disable()
            
            freeCamPart = Instance.new("Part")
            freeCamPart.Name = "SAHAR_FreeCamPart"
            freeCamPart.Transparency = 1
            freeCamPart.CanCollide = false
            freeCamPart.Anchored = true
            freeCamPart.CFrame = camera.CFrame
            freeCamPart.Parent = workspace
            
            camera.CameraSubject = freeCamPart
            
            task.spawn(function()
                while freeCamActive do
                    local dt = RunService.RenderStepped:Wait()
                    local moveDir = Vector3.zero
                    
                    if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + camera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - camera.CFrame.LookVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - camera.CFrame.RightVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + camera.CFrame.RightVector end
                    if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
                    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0, 1, 0) end
                    
                    if moveDir.Magnitude > 0 then
                        freeCamPart.Position = freeCamPart.Position + (moveDir.Unit * (freeCamSpeed * 100) * dt)
                    end
                end
            end)
        else
            controls:Enable()
            if freeCamPart then freeCamPart:Destroy() freeCamPart = nil end
            if char and char:FindFirstChild("Humanoid") then
                camera.CameraSubject = char.Humanoid
            end
        end
    end
})

PlayerRight:AddSlider("FreeCamSpeedSlider", {
    Text = "Free Cam Speed",
    Default = 1,
    Min = 0.1,
    Max = 5,
    Rounding = 1,
    Callback = function(Value)
        freeCamSpeed = Value
    end
})

PlayerRight:AddToggle("ThirdPersonToggle", {
    Text = "3rd view",
    Default = false,
    Callback = function(val)
        if val then
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = math.huge
            plr.CameraMinZoomDistance = 0
        else
            plr.CameraMode = Enum.CameraMode.LockFirstPerson
            task.wait()
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = 0.5
            plr.CameraMinZoomDistance = 0.5
        end
    end
})

local rightLegViewActive = false
local rightLegViewConn = nil

PlayerRight:AddToggle("RightLegViewToggle", {
    Text = "right leg view (why i do this...)",
    Default = false,
    Callback = function(val)
        rightLegViewActive = val

        if val then
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = math.huge
            plr.CameraMinZoomDistance = 0

            rightLegViewConn = RunService.RenderStepped:Connect(function()
                if not rightLegViewActive then return end
                local char = plr.Character
                if not char then return end
                local rightLeg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightFoot")
                if not rightLeg then return end
                local cam = workspace.CurrentCamera
                local humRootPart = char:FindFirstChild("HumanoidRootPart")
                if cam and humRootPart then
                    cam.CFrame = rightLeg.CFrame * CFrame.new(0, 0, 0.5)
                    cam.CameraSubject = rightLeg
                end
            end)
        else
            if rightLegViewConn then
                rightLegViewConn:Disconnect()
                rightLegViewConn = nil
            end

            local char = plr.Character
            local hum = char and char:FindFirstChild("Humanoid")
            if hum then
                workspace.CurrentCamera.CameraSubject = hum
            end

            plr.CameraMode = Enum.CameraMode.LockFirstPerson
            task.wait()
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = 0.5
            plr.CameraMinZoomDistance = 0.5
        end
    end
})

PlayerRight:AddSlider("FOVSlider", { Text = "Field of View", Default = 70, Min = 10, Max = 120, Rounding = 0, Compact = false, Callback = function(Value) workspace.CurrentCamera.FieldOfView = Value end })

local relaxMusic = SoundService:FindFirstChild("SAHAR_RelaxMusic")
if not relaxMusic then
    relaxMusic = Instance.new("Sound") relaxMusic.Name = "SAHAR_RelaxMusic" relaxMusic.SoundId = "rbxassetid://140722099430139" relaxMusic.Volume = 2.5 relaxMusic.Looped = true relaxMusic.Parent = SoundService
end

FunLeft:AddToggle("RelaxMusicToggle", { Text = "Relax Music", Default = false, Callback = function(val) if val then relaxMusic:Play() else relaxMusic:Stop() end end })

local jerkActive = false
local jerkTrack = nil
local jerkCharConn = nil
local jerkAnimate = nil
local jerkSpeed = 2

local function stopJerk()
    if jerkTrack then
        pcall(function()
            jerkTrack:Stop()
            jerkTrack:Destroy()
        end)
        jerkTrack = nil
    end
    if jerkAnimate then
        pcall(function()
            jerkAnimate.Disabled = false
        end)
        jerkAnimate = nil
    end
end

local function playJerk(char)
    if not char then return false end

    local hum = char:FindFirstChildWhichIsA("Humanoid") or char:WaitForChild("Humanoid", 5)
    if not hum then return false end

    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = hum
    end

    stopJerk()

    local animate = char:FindFirstChild("Animate")
    if animate then
        jerkAnimate = animate
        pcall(function() animate.Disabled = true end)
    end

    for _, track in pairs(animator:GetPlayingAnimationTracks()) do
        pcall(function() track:Stop(0.1) end)
    end

    local anim = Instance.new("Animation")
    anim.AnimationId = "rbxassetid://168268306"

    local ok, track = pcall(function()
        return animator:LoadAnimation(anim)
    end)

    if not ok or not track then
        if jerkAnimate then
            pcall(function() jerkAnimate.Disabled = false end)
            jerkAnimate = nil
        end
        return false
    end

    jerkTrack = track
    jerkTrack.Priority = Enum.AnimationPriority.Action4
    jerkTrack:Play(0.1, 1, jerkSpeed)

    task.spawn(function()
        while jerkActive do
            task.wait(0.08)
            if jerkTrack and jerkTrack.IsPlaying then
                jerkTrack.TimePosition = 0.3
            end
        end
    end)

    return true
end

FunLeft:AddSlider("JerkSpeedSlider", {
    Text = "Jerk Speed",
    Default = 25,
    Min = 5,
    Max = 100,
    Rounding = 0,
    Compact = false,
    Suffix = "",
    Callback = function(Value)
        jerkSpeed = Value / 10
        if jerkActive and jerkTrack then
            pcall(function()
                jerkTrack:AdjustSpeed(jerkSpeed)
            end)
        end
    end
})

FunLeft:AddToggle("JerkToggle", {
    Text = "Jerk",
    Default = false,
    Callback = function(Value)
        jerkActive = Value

        if jerkCharConn then
            jerkCharConn:Disconnect()
            jerkCharConn = nil
        end

        if not Value then
            stopJerk()
            return
        end

        local char = plr.Character or plr.CharacterAdded:Wait()
        local success = playJerk(char)

        if not success then
            jerkActive = false
            if Toggles and Toggles.JerkToggle then
                Toggles.JerkToggle:SetValue(false)
            end
            return
        end

        jerkCharConn = plr.CharacterAdded:Connect(function(newChar)
            if not jerkActive then return end
            task.wait(1)
            playJerk(newChar)
        end)
    end
}):AddKeyPicker("JerkBind", {
    Default = "F",
    SyncToggleState = true,
    Mode = "Toggle",
    Text = "Jerk",
    NoUI = false
})

local legsBehindActive = false

FunLeft:AddToggle("LegsBehindToggle", {
    Text = "Legs Behind Back",
    Default = false,
    Callback = function(Value)
        legsBehindActive = Value
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local rs = game:GetService("ReplicatedStorage")
        local ragdoll = rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("RagdollRemote")

        if not Value then
            local hum = char and char:FindFirstChild("Humanoid")
            if hum then hum.Health = 0 end
            return
        end

        if char and hrp and ragdoll then
            local ll = char:FindFirstChild("Left Leg") or char:FindFirstChild("LeftLowerLeg")
            local rl = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightLowerLeg")
            
            if ll and rl then
                pcall(function() ragdoll:FireServer(hrp, 2) end)
                task.wait(0.5)

                local function attachLeg(leg, offset)
                    local weld = Instance.new("Weld")
                    weld.Name = "SAHAR_LegWeld"
                    weld.Part0 = hrp
                    weld.Part1 = leg
                    weld.C0 = offset
                    weld.Parent = leg
                    
                    for _, p in pairs(leg:GetDescendants()) do
                        if p:IsA("BasePart") then p.CanCollide = false end
                    end
                    leg.CanCollide = false
                end

                attachLeg(ll, CFrame.new(-0.5, 1, 2) * CFrame.Angles(math.rad(90), 0, 0))
                attachLeg(rl, CFrame.new(0.5, 1, 2) * CFrame.Angles(math.rad(90), 0, 0))
                
                local hum = char:FindFirstChild("Humanoid")
                if hum then
                    task.spawn(function()
                        while legsBehindActive and char and char.Parent do
                            hum.HipHeight = 1.5
                            task.wait(0.1)
                        end
                        if hum then hum.HipHeight = 0 end
                    end)
                end
            end
        end
    end
})

local spinActive = false local spinAngle = 0 local spinConn = nil
FunLeft:AddToggle("SpinToggle", {
    Text = "Spin", Default = false,
    Callback = function(val)
        spinActive = val
        if val then spinAngle = 0 spinConn = RunService.Heartbeat:Connect(function(dt)
            if not spinActive then return end
            local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            spinAngle = spinAngle + (math.pi * 2 * 5 * dt)
            hrp.CFrame = CFrame.new(hrp.Position) * CFrame.Angles(0, spinAngle, 0)
        end)
        else if spinConn then spinConn:Disconnect() spinConn = nil end end
    end
}):AddKeyPicker("SpinBind", { Default = "V", SyncToggleState = true, Mode = "Toggle", Text = "Spin", NoUI = false })

local burmaDanceActive = false
local burmaDanceTrack = nil
local burmaDanceCharConn = nil
local burmaDanceAnimate = nil

local function stopBurmaDance()
    if burmaDanceTrack then
        pcall(function()
            burmaDanceTrack:Stop()
            burmaDanceTrack:Destroy()
        end)
        burmaDanceTrack = nil
    end
    if burmaDanceAnimate then
        pcall(function()
            burmaDanceAnimate.Disabled = false
        end)
        burmaDanceAnimate = nil
    end
end

local function playBurmaDance(char)
    if not char then return false end

    local hum = char:FindFirstChildWhichIsA("Humanoid") or char:WaitForChild("Humanoid", 5)
    if not hum then return false end

    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = hum
    end

    stopBurmaDance()

    local animate = char:FindFirstChild("Animate")
    if animate then
        burmaDanceAnimate = animate
        pcall(function()
            animate.Disabled = true
        end)
    end

    for _, track in pairs(animator:GetPlayingAnimationTracks()) do
        pcall(function()
            track:Stop(0.1)
        end)
    end

    local animId
    if hum.RigType == Enum.HumanoidRigType.R15 then
        animId = "rbxassetid://10214311282"
    else
        animId = "rbxassetid://27789359"
    end

    local animation = Instance.new("Animation")
    animation.AnimationId = animId

    local ok, track = pcall(function()
        return animator:LoadAnimation(animation)
    end)

    if not ok or not track then
        if burmaDanceAnimate then
            pcall(function()
                burmaDanceAnimate.Disabled = false
            end)
            burmaDanceAnimate = nil
        end
        return false
    end

    burmaDanceTrack = track
    burmaDanceTrack.Priority = Enum.AnimationPriority.Action4
    burmaDanceTrack.Looped = true
    burmaDanceTrack:Play(0.1, 1, 1)

    return true
end

FunRight:AddToggle("BurmaDanceToggle", {
    Text = "burmaldance",
    Default = false,
    Callback = function(Value)
        burmaDanceActive = Value

        if burmaDanceCharConn then
            burmaDanceCharConn:Disconnect()
            burmaDanceCharConn = nil
        end

        if not Value then
            stopBurmaDance()
            return
        end

        local char = plr.Character or plr.CharacterAdded:Wait()
        local success = playBurmaDance(char)

        if not success then
            burmaDanceActive = false
            if Toggles and Toggles.BurmaDanceToggle then
                Toggles.BurmaDanceToggle:SetValue(false)
            end
            return
        end

        burmaDanceCharConn = plr.CharacterAdded:Connect(function(newChar)
            if not burmaDanceActive then return end
            task.wait(1)
            playBurmaDance(newChar)
        end)
    end
}):AddKeyPicker("BurmaDanceBind", {
    Default = "B",
    SyncToggleState = true,
    Mode = "Toggle",
    Text = "burmaldance",
    NoUI = false
})

local lps = 100 local linesActive = false
ServerLagsLeft:AddSlider("LineLagSlider", { Text = "Lines Per Second", Default = 100, Min = 1, Max = 5000, Rounding = 0, Compact = false, Suffix = " lines", Callback = function(v) lps = v end })
ServerLagsLeft:AddToggle("LineLagToggle", {
    Text = "Line Lag", Default = false,
    Callback = function(Value)
        linesActive = Value
        if Value then task.spawn(function() while linesActive do for i = 1, lps do xpcall(function() ReplicatedStorage.GrabEvents.CreateGrabLine:FireServer(workspace.SpawnLocation, CFrame.new(0,9e9,0)) end, function() end) end task.wait(1) end end) end
    end
})

local LagSettings = { Strength = 2200, Active = false, Data = string.rep("🟥🟥🟥🟥", 400), Remote = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("ExtendGrabLine") }
ServerLagsRight:AddSlider("PacketStrengthSlider", { Text = "Packet Strength", Default = 2200, Min = 100, Max = 5000, Rounding = 0, Compact = false, Suffix = " str", Callback = function(Value) LagSettings.Strength = Value end })
ServerLagsRight:AddToggle("PacketLagToggle", {
    Text = "Packet Lag", Default = false,
    Callback = function(Value)
        LagSettings.Active = Value
        if not Value or not LagSettings.Remote then return end
        task.spawn(function() while LagSettings.Active do task.spawn(function() xpcall(function() LagSettings.Remote:FireServer(string.rep(LagSettings.Data, LagSettings.Strength)) end, function() end) end) task.wait(0.24) end end)
    end
})

local DetectLeft = Tabs.Detect:AddLeftGroupbox("Detectors")

local packetLagNotifyEnabled = true
local lastLagSource = false
local packetLagDetectorStarted = false
local packetCount = 0
local packetPerSecond = 0

local packetDetectSound = Instance.new("Sound")
packetDetectSound.Name = "SAHAR_PacketDetectSound"
packetDetectSound.SoundId = "rbxassetid://134699420140804"
packetDetectSound.Volume = 2
packetDetectSound.Parent = SoundService

local packetLabel = DetectLeft:AddLabel("Packets/s: 0")

task.spawn(function()
    while true do
        task.wait(1)
        packetPerSecond = packetCount
        packetCount = 0
        xpcall(function()
            packetLabel.Text = "Packets/s: " .. tostring(packetPerSecond)
        end, function() end)
    end
end)

local function GetSizeMB(StringLength)
    return StringLength / (1024 * 1024)
end

local function StartPacketLagDetector()
    if packetLagDetectorStarted then return end
    packetLagDetectorStarted = true

    local RS = game:GetService("ReplicatedStorage")
    RS.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(arg1, data)
        if typeof(data) == "string" then
            packetCount = packetCount + 1

            if not lastLagSource and packetLagNotifyEnabled then
                local StringLen = string.len(data)

                if StringLen > 300 then
                    lastLagSource = true

                    local SizeRounded = math.round(GetSizeMB(StringLen) * 1000) / 1000

                    packetDetectSound:Play()

                    local notif = Instance.new("ScreenGui")
                    notif.Name = "SAHAR_PacketNotif"
                    notif.IgnoreGuiInset = true
                    notif.ResetOnSpawn = false
                    notif.DisplayOrder = 2147483646
                    notif.Parent = CoreGui

                    local frame = Instance.new("Frame")
                    frame.Size = UDim2.new(0, 350, 0, 95)
                    frame.Position = UDim2.new(1, -360, 0, 10)
                    frame.BackgroundColor3 = Color3.fromRGB(10, 60, 60)
                    frame.BorderSizePixel = 0
                    frame.Parent = notif

                    local corner = Instance.new("UICorner")
                    corner.CornerRadius = UDim.new(0, 8)
                    corner.Parent = frame

                    local stroke = Instance.new("UIStroke")
                    stroke.Color = Color3.fromRGB(135, 252, 245)
                    stroke.Thickness = 1.5
                    stroke.Parent = frame

                    local title = Instance.new("TextLabel")
                    title.Size = UDim2.new(1, -20, 0, 25)
                    title.Position = UDim2.new(0, 10, 0, 5)
                    title.BackgroundTransparency = 1
                    title.Text = "SAHAR DETECT PACKET LAG!"
                    title.TextColor3 = Color3.fromRGB(135, 252, 245)
                    title.Font = Enum.Font.GothamBold
                    title.TextSize = 14
                    title.TextXAlignment = Enum.TextXAlignment.Left
                    title.Parent = frame

                    local info = Instance.new("TextLabel")
                    info.Size = UDim2.new(1, -20, 0, 55)
                    info.Position = UDim2.new(0, 10, 0, 30)
                    info.BackgroundTransparency = 1
                    info.Text = "Sender: " .. tostring(arg1):sub(1, 30) .. "\nSize: " .. tostring(SizeRounded) .. " MB\nPackets/s: " .. tostring(packetPerSecond)
                    info.TextColor3 = Color3.fromRGB(169, 255, 226)
                    info.Font = Enum.Font.Gotham
                    info.TextSize = 13
                    info.TextXAlignment = Enum.TextXAlignment.Left
                    info.TextYAlignment = Enum.TextYAlignment.Top
                    info.Parent = frame

                    task.delay(5, function()
                        if notif and notif.Parent then
                            notif:Destroy()
                        end
                    end)

                    task.delay(5, function()
                        lastLagSource = false
                    end)
                end
            end
        end
    end)
end

DetectLeft:AddToggle("PacketLagNotifyToggle", {
    Text = "Packet Lag Detect",
    Default = true,
    Callback = function(Value)
        packetLagNotifyEnabled = Value
        if Value then
            StartPacketLagDetector()
        end
    end
})

StartPacketLagDetector()

local kickNotifyEnabled = true
local kickNotifyConn = nil

local kickDetectSound = Instance.new("Sound")
kickDetectSound.Name = "SAHAR_KickDetectSound"
kickDetectSound.SoundId = "rbxassetid://119842898163832"
kickDetectSound.Volume = 2
kickDetectSound.Parent = SoundService

local function StartKickNotify()
    if kickNotifyConn then
        kickNotifyConn:Disconnect()
        kickNotifyConn = nil
    end

    kickNotifyConn = workspace.ChildAdded:Connect(function(child)
        if not kickNotifyEnabled then return end
        if child.Name ~= "BlackHoleKick" and child.Name ~= "BlackHole" then return end

        local preList = {}
        for _, p in pairs(Players:GetPlayers()) do
            preList[p.Name] = p.DisplayName
        end

        task.wait(3.5)

        for name, display in pairs(preList) do
            if not Players:FindFirstChild(name) then
                task.spawn(function()
                    pcall(function()
                        kickDetectSound:Play()

                        local sg = Instance.new("ScreenGui")
                        sg.Name = "SAHAR_KickNotif"
                        sg.IgnoreGuiInset = true
                        sg.ResetOnSpawn = false
                        sg.DisplayOrder = 2147483646
                        sg.Parent = CoreGui

                        local f = Instance.new("Frame")
                        f.Size = UDim2.new(0, 350, 0, 60)
                        f.Position = UDim2.new(1, -360, 0, 10)
                        f.BackgroundColor3 = Color3.fromRGB(10, 60, 60)
                        f.BorderSizePixel = 0
                        f.Parent = sg

                        local corner = Instance.new("UICorner")
                        corner.CornerRadius = UDim.new(0, 8)
                        corner.Parent = f

                        local stroke = Instance.new("UIStroke")
                        stroke.Color = Color3.fromRGB(135, 252, 245)
                        stroke.Thickness = 1.5
                        stroke.Parent = f

                        local t = Instance.new("TextLabel")
                        t.Size = UDim2.new(1, -20, 0, 25)
                        t.Position = UDim2.new(0, 10, 0, 5)
                        t.BackgroundTransparency = 1
                        t.Text = "⁺˚⋆ SAHAR ⋆˚⁺ KICK DETECTED"
                        t.TextColor3 = Color3.fromRGB(135, 252, 245)
                        t.Font = Enum.Font.GothamBold
                        t.TextSize = 14
                        t.TextXAlignment = Enum.TextXAlignment.Left
                        t.Parent = f

                        local m = Instance.new("TextLabel")
                        m.Size = UDim2.new(1, -20, 0, 20)
                        m.Position = UDim2.new(0, 10, 0, 30)
                        m.BackgroundTransparency = 1
                        m.Text = display .. " (@" .. name .. ") got kicked!"
                        m.TextColor3 = Color3.fromRGB(169, 255, 226)
                        m.Font = Enum.Font.GothamBold
                        m.TextSize = 13
                        m.TextXAlignment = Enum.TextXAlignment.Left
                        m.TextWrapped = true
                        m.Parent = f

                        task.wait(5)
                        sg:Destroy()
                    end)
                end)
            end
        end
    end)
end

DetectLeft:AddToggle("KickNotifyToggle", {
    Text = "Kick Detect",
    Default = true,
    Callback = function(Value)
        kickNotifyEnabled = Value
        if Value then
            StartKickNotify()
        else
            if kickNotifyConn then
                kickNotifyConn:Disconnect()
                kickNotifyConn = nil
            end
        end
    end
})

StartKickNotify()

local AurasGroup = Tabs.Auras:AddLeftGroupbox("Auras")

local removeAntiKickAuraActive = false
local removeAntiKickAuraConnection = nil
local removeAntiKickRadius = 15

AurasGroup:AddSlider("RemoveAntiKickAuraRadiusSlider", {
    Text = "Anti Kick Aura Radius",
    Default = 15,
    Min = 5,
    Max = 27,
    Rounding = 0,
    Compact = false,
    Suffix = " studs",
    Callback = function(value)
        removeAntiKickRadius = value
    end
})

AurasGroup:AddToggle("RemoveAntiKickAuraToggle", {
    Text = "Remove Anti Kick Aura",
    Default = false,
    Callback = function(Value)
        removeAntiKickAuraActive = Value

        if not Value then
            if removeAntiKickAuraConnection then
                removeAntiKickAuraConnection:Disconnect()
                removeAntiKickAuraConnection = nil
            end
            return
        end

        task.spawn(function()
            local SetNetOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")

            removeAntiKickAuraConnection = RunService.Heartbeat:Connect(function()
                local myChar = plr.Character
                local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                if not myRoot then return end

                for _, target in pairs(Players:GetPlayers()) do
                    if target ~= plr then
                        local tChar = target.Character
                        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        if not tRoot then continue end

                        if (tRoot.Position - myRoot.Position).Magnitude <= removeAntiKickRadius then
                            local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                            if spawned then
                                local targetToys = {
                                    "NinjaKunai",
                                    "NinjaShuriken",
                                    "AntiKick",
                                    "NinjaKatana",
                                    "ToolCleaver",
                                    "ToolDiggingForkRusty",
                                    "ToolPencil",
                                    "ToolPickaxe"
                                }

                                for _, toyName in pairs(targetToys) do
                                    local toy = spawned:FindFirstChild(toyName)
                                    if toy then
                                        local part = toy:FindFirstChild("SoundPart") or toy.PrimaryPart or toy:FindFirstChildWhichIsA("BasePart", true)
                                        if part then
                                            pcall(function()
                                                SetNetOwner:FireServer(part, part.CFrame)
                                                if part:FindFirstChild("PartOwner") and part.PartOwner.Value == plr.Name then
                                                    part.CFrame = CFrame.new(0, 10000, 0)
                                                end
                                            end)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        end)
    end
})

local MenuGroup = Tabs.Settings:AddLeftGroupbox("Menu")
MenuGroup:AddButton("Unload", function()
    ClearPCLDBoxes()
    ClearNameESP()
    Library:Unload()
end)
MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", {
    Default = "M",
    NoUI = true,
    Text = "Menu keybind"
})
Library.ToggleKeybind = Options.MenuKeybind

ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({"MenuKeybind"})
ThemeManager:SetFolder("SAHAR")
SaveManager:SetFolder("SAHAR/Configs")

SaveManager:BuildConfigSection(Tabs.Settings)
ThemeManager:ApplyToTab(Tabs.Settings)
SaveManager:LoadAutoloadConfig()

Library:Notify("SAHAR loaded successfully!", 3)