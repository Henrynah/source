OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Utkaky/Main/refs/heads/main/Orion.lua"))()

Window = OrionLib:MakeWindow({
    Name = "67",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "idkhubi",
    IntroEnabled = false,
    IntroText = "idk Hub",
    KeyToOpenWindow = "M",
    FreeMouse = true
})

tabaura = Window:MakeTab({
    Name = "Loop Fling",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})
Players = game:GetService("Players")
me = Players.LocalPlayer
rs = game:GetService("ReplicatedStorage")
w = game:GetService("Workspace")
SelectedPlayers = {}
tabaura:AddPlayersDropdown({
    Name = "Select Players",
    MultipleSelection = true,
    RemoveDP = true,
    Default = "",
    Callback = function(selected)
        SelectedPlayers = selected
        print("[Selected Players]:", selected)
        
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= me then
                local isSelected = false
                
                if type(SelectedPlayers) == "table" then
                    for _, name in ipairs(SelectedPlayers) do
                        if player.Name == name then
                            isSelected = true
                            break
                        end
                    end
                elseif type(SelectedPlayers) == "string" and SelectedPlayers ~= "" then
                    isSelected = (player.Name == SelectedPlayers)
                end
                
                player:SetAttribute("IsAdded", isSelected)
            end
        end
    end,
    Flag = "TargetPlayers",
    Save = false
})
function isPlayerSelected(player)
    if type(SelectedPlayers) == "table" then
        for _, name in ipairs(SelectedPlayers) do
            if player.Name == name then
                return true
            end
        end
    elseif type(SelectedPlayers) == "string" and SelectedPlayers ~= "" then
        return player.Name == SelectedPlayers
    end
    return false
end
Players.PlayerAdded:Connect(function(player)
    task.wait(0.5)
    if player ~= me then
        player:SetAttribute("IsAdded", isPlayerSelected(player))
    end
end)
Players.PlayerRemoving:Connect(function(player)
    if type(SelectedPlayers) == "table" then
        for i, name in ipairs(SelectedPlayers) do
            if name == player.Name then
                table.remove(SelectedPlayers, i)
                break
            end
        end
    end
end)
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= me then
        player:SetAttribute("IsAdded", false)
    end
end

getgenv().Players = game:GetService("Players")
getgenv().ReplicatedStorage = game:GetService("ReplicatedStorage")
getgenv().RunService = game:GetService("RunService")
getgenv().Workspace = game:GetService("Workspace")

getgenv().lp = Players.LocalPlayer
getgenv().char = lp.Character or lp.CharacterAdded:Wait()
getgenv().root = char:WaitForChild("HumanoidRootPart")
getgenv().folder = Workspace:WaitForChild(lp.Name .. "SpawnedInToys")

getgenv().isProcessing      = false
getgenv().isEnabled         = false
getgenv().targetIndex       = 1
getgenv().flungMap          = {}
getgenv().currentDecoy      = nil
getgenv().currentTarget     = nil
getgenv().conn              = nil
getgenv().UNIQUE_ATTRIBUTE  = "OwnedByScript"
getgenv().FLING_FORCE       = 500
getgenv().ownershipMonitors = {}

getgenv().SelectedPlayers   = {}

getgenv().toyMap = {
    YouLittle = "Head",
    YouDecoy = "Head",
    DiceSmall = "SoundPart",
    DiceBig = "SoundPart"
}

getgenv().selectedToy = "DiceBig"

getgenv().getTargets = function()
    getgenv().targetsList = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp and p:GetAttribute("IsAdded") and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            table.insert(getgenv().targetsList, p)
        end
    end
    return getgenv().targetsList
end

getgenv().velocityHistory = {}

getgenv().isFlung = function(p)
    local h = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
    if not h then return true end

    local posY = h.Position.Y
    local v = h.Velocity
    local horizontalMag = Vector3.new(v.X, 0, v.Z).Magnitude

    if not velocityHistory[p] then
        velocityHistory[p] = {}
    end
    local hist = velocityHistory[p]
    table.insert(hist, {tick(), v, posY})
    if #hist > 15 then table.remove(hist, 1) end 

    local heightOK = posY > -100 and posY < 1500
    local verticalOK = math.abs(v.Y) < 180
    local horizOK = horizontalMag < 250

    local badFrames = 0
    for _, data in ipairs(hist) do
        local vel = data[2]
        local y   = data[3]
        if y > 3000 or y < -150 then
            badFrames += 1
        elseif math.abs(vel.Y) > 220 or Vector3.new(vel.X,0,vel.Z).Magnitude > 300 then
            badFrames += 1
        end
    end

    if badFrames / #hist >= 0.4 then
        return true
    end

    return false
end

getgenv().isGrounded = function(p)
    local h = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
    return h and h.Position.Y < 100 and math.abs(h.Velocity.Y) < 10
end

getgenv().pickNextTarget = function(targets)
    local c = #targets
    if c == 0 then return nil end
    for i = 1, c do
        targetIndex = ((targetIndex + i - 1) % c) + 1
        local t = targets[targetIndex]
        if not flungMap[t] or isGrounded(t) then
            return t
        end
    end
    return nil
end

getgenv().monitorOwnership = function(toy, toyPart)
    if ownershipMonitors[toy] then
        ownershipMonitors[toy]:Disconnect()
        ownershipMonitors[toy] = nil
    end
    ownershipMonitors[toy] = RunService.Heartbeat:Connect(function()
        if not toy or not toy.Parent then
            if ownershipMonitors[toy] then
                ownershipMonitors[toy]:Disconnect()
                ownershipMonitors[toy] = nil
            end
            return
        end
        local tag = toyPart:FindFirstChild("PartOwner")
        if tag and tag:IsA("StringValue") and tag.Value ~= lp.Name and tag.Value ~= "" then
            print("[Ownership Monitor] Ownership changed to: " .. tag.Value .. " - Destroying toy")
            rs.MenuToys.DestroyToy:FireServer(toy)
            if toy == currentDecoy then currentDecoy = nil end
            if ownershipMonitors[toy] then
                ownershipMonitors[toy]:Disconnect()
                ownershipMonitors[toy] = nil
            end
        end
    end)
end

getgenv().spawnDecoy = function()
    if currentDecoy and currentDecoy.Parent then return end
    local toy = selectedToy or "YouDecoy"
    rs.MenuToys.SpawnToyRemoteFunction:InvokeServer(
        toy,
        root.CFrame * CFrame.new(5, 0, 5),
        Vector3.new(0, 33, 0)
    )
end

getgenv().handleDecoy = function(d)
    if currentDecoy and currentDecoy.Parent then return end
    local partName = toyMap[d.Name]
    if not partName then return end
    local toyPart = d:WaitForChild(partName)
    local pivotCF = d:GetPivot()
    rs.GrabEvents.SetNetworkOwner:FireServer(toyPart, pivotCF)
    task.wait(0.09)

    local startTime = tick()
    local success = false
    local connection
    connection = RunService.Heartbeat:Connect(function()
        local tag = toyPart:FindFirstChild("PartOwner")
        if tag and tag:IsA("StringValue") and tag.Value == lp.Name then
            success = true
            connection:Disconnect()
            if not d:GetAttribute(UNIQUE_ATTRIBUTE) then
                d:SetAttribute(UNIQUE_ATTRIBUTE, true)
                currentDecoy = d
                monitorOwnership(d, toyPart)
                if isEnabled then setupFling(d) end
            end
        end
        if tick() - startTime >= 3 and not success then
            rs.MenuToys.DestroyToy:FireServer(d)
            connection:Disconnect()
        end
    end)
end

folder.ChildAdded:Connect(function(c)
    if toyMap[c.Name] then handleDecoy(c) end
end)

folder.ChildRemoved:Connect(function(c)
    if c == currentDecoy then currentDecoy = nil end
    if ownershipMonitors[c] then ownershipMonitors[c]:Disconnect(); ownershipMonitors[c] = nil end
end)

RunService.Heartbeat:Connect(function()
    if not currentDecoy or not currentDecoy.Parent then
        for _, t in ipairs(folder:GetChildren()) do
            if toyMap[t.Name] and t:GetAttribute(UNIQUE_ATTRIBUTE) then
                currentDecoy = t
                local toyPart = t:FindFirstChild(toyMap[t.Name])
                if toyPart and not ownershipMonitors[t] then monitorOwnership(t, toyPart) end
                return
            end
        end
        if isEnabled then spawnDecoy() end
    end
end)

getgenv().setupFling = function(d)
    local hrp = d:FindFirstChild("HumanoidRootPart") or d.PrimaryPart or d:FindFirstChild(toyMap[d.Name])
    if not hrp then return end
    d.PrimaryPart = hrp
    hrp.CanCollide = false

    local bt = Instance.new("BodyThrust")
    bt.Force = Vector3.zero
    bt.Parent = hrp

    local bav = Instance.new("BodyAngularVelocity")
    bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bav.AngularVelocity = Vector3.new(-1e6, -1e6, -1e6)
    bav.Parent = hrp

    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Blacklist
    params.FilterDescendantsInstances = {lp.Character, d}
    params.IgnoreWater = true

    conn = RunService.Heartbeat:Connect(function()
        if not d or not d.Parent or not isEnabled then
            if conn then conn:Disconnect() end
            if bt.Parent then bt:Destroy() end
            if bav.Parent then bav:Destroy() end
            return
        end

        w.FallenPartsDestroyHeight = 0/0
        local tList = getTargets()

        for p in pairs(flungMap) do
            if not table.find(tList, p) or not p.Character or isGrounded(p) then
                flungMap[p] = nil
            end
        end

        if currentTarget and (not currentTarget.Character or isFlung(currentTarget)) then
            flungMap[currentTarget] = true
            currentTarget = nil
        end
        if not currentTarget then currentTarget = pickNextTarget(tList) end

        local destCF
        if currentTarget and currentTarget.Character then
            local tHRP = currentTarget.Character:FindFirstChild("HumanoidRootPart")
            if tHRP then
                local vel = tHRP.Velocity
                local speed = vel.Magnitude
                local time = math.clamp(speed / 40, 0.25, 0.6)
                local predicted = tHRP.Position + vel*time + Vector3.new(0,2,0)
                local dir = (predicted - hrp.Position).Unit
                local dist = (predicted - hrp.Position).Magnitude
                local result = w:Raycast(hrp.Position, dir*dist, params)
                if result and result.Instance and result.Instance:IsDescendantOf(currentTarget.Character) then
                    destCF = CFrame.new(result.Position)
                else
                    destCF = CFrame.new(predicted)
                end
            end
        end

        if not destCF then destCF = CFrame.new(0, 5000, 0) end
        for _, p in ipairs(d:GetDescendants()) do
            if p:IsA("BasePart") then p.CFrame = destCF end
        end
        if bt.Parent then bt.Force = (destCF.Position - hrp.Position).Unit * FLING_FORCE end
    end)
end

lp.CharacterAdded:Connect(function(newChar)
    getgenv().char = newChar
    getgenv().root = newChar:WaitForChild("HumanoidRootPart")
    print("[Respawn] Character and HumanoidRootPart updated")
end)

lp.CharacterRemoving:Connect(function(oldChar)
    if char == oldChar then
        getgenv().char, getgenv().root = nil,nil
    end
end)

getgenv().process = function()
    if isProcessing then return end
    isProcessing=true
    if not currentDecoy or not currentDecoy.Parent or not currentDecoy:GetAttribute(UNIQUE_ATTRIBUTE) then
        if isEnabled then spawnDecoy() end
    else
        setupFling(currentDecoy)
    end
    isProcessing=false
end
tabaura:AddToggle({
    Name="Loop Fling",
    Default=false,
    Callback=function(state)
        isEnabled=state
        if state then process() else
            isProcessing=false
            if conn then conn:Disconnect() end
            for toy,mon in pairs(ownershipMonitors) do mon:Disconnect() end
            ownershipMonitors={}
            if currentDecoy and currentDecoy.Parent then
                rs.MenuToys.DestroyToy:FireServer(currentDecoy)
            end
            currentDecoy=nil
        end
    end
})
tabaura:AddDropdown({
    Name="Fling Toy",
    Options={ "YouLittle","YouDecoy","DiceSmall","DiceBig"},
    Default="DiceBig",
    Callback=function(option)
        selectedToy=option
        print("[Fling Toy Selected] -> "..selectedToy)
    end
})
