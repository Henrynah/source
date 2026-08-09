-- POLAR HUB by Absolute_SEB / prevosxodn1yy (feat. DeepSeek and ChatGPT)
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
local repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
local Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
local ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
local SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()

local Options = Library.Options
local Toggles = Library.Toggles

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local targetNotifyConnections = {}

local Window = Library:CreateWindow({
    Title = "POLAR HUB",
    Footer = "by Absolute_SEB",
    Icon = 120050296012939,
    NotifySide = "Right",
    ShowCustomCursor = true,
	EnableCompacting = true,
	SidebarCompacted = true,
	CornerRadius = 15
})

local Tabs = {
    Defence = Window:AddTab("Defence", "shield"),
    Target = Window:AddTab("Target", "target"),
    Visuals = Window:AddTab("Visuals", "eye"),
    Server = Window:AddTab("Server", "server"),
    Miscs = Window:AddTab("Miscs", "palette"),
    Fun = Window:AddTab("Fun", "smile"),
    Credits = Window:AddTab("Credits", "users"),
    UISettings = Window:AddTab("UI Settings", "settings"),
}

_G.ShurikenAntiKick = false
_G.AutoSitBlobZ = false
_G.AntiInputLag = false

local paintPartsBackup = {}
local connections = {}
local antiGrabProc = false
local AGWalk = false
local AGConnections = {}
local ragdollLoopD = false
local kickNotifyConnection = nil
local packetLagTask = nil
local loopKillTask = nil
local ownershipKickTask = nil
local ownershipRagdollTask = nil
local snowballRagdollTask = nil
local loopKickBlobTask = nil
local lagLineTask = nil
local antiInputLagTask = nil
local loopTPTask = nil
local antiOwnershipTask = nil
local antiExplosionConnection = nil
local antiBurnConnection = nil
local antiVoidConnection = nil
local tpConnection = nil
local antiAntiKickTask = nil
local antiAntiLagTask = nil
local destroyGucciTask = nil
local jerkOffAnimTrack = nil
local platformTPActive = false
local platformTPKey = Enum.KeyCode.X
local platformTPConnection = nil
local platformPart = nil
local oldPlatformPos = nil
local traceBeam = nil
local traceConnection = nil
local antiBananaSitTask = nil
local killDodgeTask = nil
local flyingResetConnection = nil
local antiRagBlobConnections = {}
local jerkOffKey = Enum.KeyCode.Q

local espEnabled = false
local espBoxes = {}
local espColor = Color3.fromRGB(255, 255, 255)
local targetNames = {"partesp", "playercharacterlocationdetector"}

local packetLagNotifyEnabled = false
local lastLagSource = false
local packetLagActive = false
local loopKillActive = false
local ownershipKickActive = false
local ownershipRagdollActive = false
local snowballRagdollActive = false
local loopKickBlobActive = false
local lagLineActive = false
local antiOwnershipActive = false
local loopTPActive = false
local antiExplosionActive = false
local antiBurnActive = false
local antiVoidActive = false
local tpEnabled = false
local antiAntiKickActive = false
local antiAntiLagEnabled = false
local removeAntiKickAuraActive = false
local removeAntiKickRadius = 15
local useWhitelistRemoveAntiKick = true
local gucciKey = Enum.KeyCode.J
local gucciRunId = 0
local destroyGucciActive = false
local playJerkOffActive = false
local platformTPToggle = false
local traceEnabled = false
local traceColor = Color3.fromRGB(255, 0, 0)
local antiBananaSitActive = false
local killDodgeActive = false
local flyingResetActive = false
local antiRagBlobActive = false
local telekinesisShieldActive = false
local telekinesisShieldTask = nil

local function updatePlayerList()
    local currentPlayers = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(currentPlayers, player.Name)
        end
    end
    return currentPlayers
end

local function deleteAllPaintParts()
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
            local clone = obj:Clone()
            clone.Archivable = true
            paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
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
    table.insert(connections, Workspace.DescendantAdded:Connect(function(obj)
        if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
            task.defer(function()
                if obj and obj.Parent then
                    local clone = obj:Clone()
                    clone.Archivable = true
                    paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
                    obj:Destroy()
                end
            end)
        end
    end))
end

local function disconnectWatchers()
    for _, conn in ipairs(connections) do
        if conn.Connected then conn:Disconnect() end
    end
    connections = {}
end

local function setTouchQuery(state)
    local char = Workspace:FindFirstChild(LocalPlayer.Name)
    if not char then return end
    for _, v in ipairs(char:GetChildren()) do
        if v:IsA("Part") or v:IsA("BasePart") then
            v.CanTouch = state
            v.CanQuery = state
        end
    end
end

local function FWC(obj, name)
    return obj:WaitForChild(name)
end

local function Disc(name)
    if AGConnections[name] then
        AGConnections[name]:Disconnect()
        AGConnections[name] = nil
    end
end

local function toy_spawn(name, cframe, vector)
    local args = { name, cframe, vector }
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    local folder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", 5)
    if folder then
        return folder:WaitForChild(name, 5)
    end
    return nil
end

local function grab(hitbox)
    if hitbox and hitbox.Parent then
        ReplicatedStorage.CharacterEvents.Grab:FireServer(hitbox)
    end
end

local function getClosestPlayer(pos)
    if not pos then return nil end
    local closestPlayer = nil
    local minDistance = math.huge
    
    for _, player in pairs(game.Players:GetPlayers()) do
        if player.Character then
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local distance = (hrp.Position - pos).Magnitude
                if distance < minDistance then
                    minDistance = distance
                    closestPlayer = player
                end
            end
        end
    end
    return closestPlayer
end

-- PACKET LAG DETECTOR 
local function GetSizeMB(StringLength)
    return StringLength / (1024 * 1024)
end

local function StartPacketLagDetector()
    local RS = game:GetService("ReplicatedStorage")
    RS.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(arg1, data)
        if typeof(data) == "string" and not lastLagSource and packetLagNotifyEnabled then
            lastLagSource = true
            local StringLen = string.len(data)
            
            if StringLen > 300 then
                local SizeRounded = math.round(GetSizeMB(StringLen) * 1000) / 1000
                
                Library:Notify({
                    Title = "POLAR HUB",
                    Description = "PACKET LAG DETECTED\nSource: " .. tostring(arg1):sub(1, 20) .. "\nSize: " .. tostring(SizeRounded) .. " MB",
                    Duration = 5
                })
            end
            
            task.delay(5, function()
                lastLagSource = false
            end)
        end
    end)
end

local function setWaterWalk(state)
    local workspaceMap = workspace:FindFirstChild("Map")
    if workspaceMap then
        local alwaysHere = workspaceMap:FindFirstChild("AlwaysHereTweenedObjects")
        if alwaysHere then
            local ocean = alwaysHere:FindFirstChild("Ocean")
            if ocean then
                local object = ocean:FindFirstChild("Object")
                if object then
                    local objectModel = object:FindFirstChild("ObjectModel")
                    if objectModel then
                        for _, child in pairs(objectModel:GetChildren()) do
                            if child:IsA("BasePart") then
                                child.CanCollide = state
                            end
                        end
                    end
                end
            end
        end
    end
end

-- PCLD
local function IsTarget(obj)
    if not obj:IsA("BasePart") then return false end
    for _, name in ipairs(targetNames) do
        if string.lower(obj.Name) == string.lower(name) then
            return true
        end
    end
    return false
end

local function AddBoxESP(obj)
    if espBoxes[obj] then 
        espBoxes[obj].Color3 = espColor
        return 
    end
    local box = Instance.new("BoxHandleAdornment")
    box.Adornee = obj
    box.AlwaysOnTop = true
    box.ZIndex = 5
    box.Color3 = espColor
    box.Transparency = 0.5
    box.Size = obj.Size
    box.Parent = game.CoreGui
    espBoxes[obj] = box
    
    obj.AncestryChanged:Connect(function(_, parent)
        if not parent and espBoxes[obj] then
            espBoxes[obj]:Destroy()
            espBoxes[obj] = nil
        end
    end)
end

local function RemoveAllBoxes()
    for obj, box in pairs(espBoxes) do
        if box then box:Destroy() end
    end
    espBoxes = {}
end

local function ScanPCLD()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if espEnabled and IsTarget(obj) then
            AddBoxESP(obj)
        end
    end
end

-- Auto Sit
local function AutoSitLoop()
    while _G.AutoSitBlobZ do
        local plr = game.Players.LocalPlayer
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChild("Humanoid")
        if not hrp or not hum then 
            task.wait(1)
            continue 
        end
        if hum.SeatPart then
            task.wait(0.5)
            continue
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
                until hum.SeatPart == seat or tick() - t > 1.5 or not _G.AutoSitBlobZ
            end
        end
        task.wait(0.5)
    end
end

-- Anti Ownership 1
local function AntiOwnershipFunction()
    local Struggle = ReplicatedStorage.CharacterEvents.Struggle
    while antiOwnershipActive do
        local character = LocalPlayer.Character
        if character and character:FindFirstChild("Head") then
            local head = character.Head
            if head:FindFirstChild("PartOwner") then
                Struggle:FireServer(LocalPlayer)
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = true
                    end
                end
                local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                while isHeld and isHeld.Value and antiOwnershipActive do
                    task.wait()
                end
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = false
                    end
                end
            end
        end
        task.wait(0.1)
    end
end

-- Loop TP 
local function LoopTPFunction()
    local Player = game.Players.LocalPlayer
    while loopTPActive do
        local char = Player.Character
        if char then
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hrp and hum then
                hum.PlatformStand = true
                local x = math.random(-500, 500)
                local y = math.random(30, 480)
                local z = math.random(-500, 500)
                hrp.CFrame = CFrame.new(x, y, z)
            end
        end
        task.wait(0.03)
    end
    local char = Player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.PlatformStand = false
        end
    end
end

-- Anti Explosion 
local function StartAntiExplosion()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:WaitForChild("HumanoidRootPart")
    antiExplosionConnection = workspace.ChildAdded:Connect(function(model)
        if model.Name == "Part" and antiExplosionActive then
            local mag = (model.Position - hrp.Position).Magnitude
            if mag <= 20 then
                hrp.Anchored = true
                task.wait(0.01)
                local rightArm = char:FindFirstChild("Right Arm")
                if rightArm then
                    local ragdollPart = rightArm:FindFirstChild("RagdollLimbPart")
                    if ragdollPart then
                        while ragdollPart.CanCollide and antiExplosionActive do
                            task.wait(0.001)
                        end
                    end
                end
                if antiExplosionActive then
                    hrp.Anchored = false
                end
            end
        end
    end)
end

-- Anti Burn 
local function StartAntiBurn()
    local char = LocalPlayer.Character
    if not char then return end
    local hum = char:WaitForChild("Humanoid")
    local hrp = char:WaitForChild("HumanoidRootPart")
    char.PrimaryPart = hrp
    
    antiBurnConnection = hum.FireDebounce.Changed:Connect(function(isBurning)
        if isBurning and antiBurnActive then
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
                            if obj:IsA("Sound") then obj:Stop() end
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
                    if me and me.PrimaryPart and antiBurnActive then
                        me:SetPrimaryPartCFrame(oldCF)
                    end
                end
            end
        end
    end)
end

-- Anti Void 
local function StartAntiVoid()
    local VOID_THRESHOLD = -50
    local SAFE_HEIGHT = 100
    antiVoidConnection = RunService.Heartbeat:Connect(function()
        if not antiVoidActive then return end
        local char = LocalPlayer.Character
        if char and char.PrimaryPart then
            local pos = char.PrimaryPart.Position
            if pos.Y < VOID_THRESHOLD then
                local safePos = Vector3.new(pos.X, pos.Y + SAFE_HEIGHT, pos.Z)
                char:SetPrimaryPartCFrame(CFrame.new(safePos))
                char.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end)
end

-- GUCCI 
local function FWC(Parent, Name, Time) 
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3) 
end

local function grab(prt) 
    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(prt, prt.CFrame) 
end

local function toy_spawn_gucci(name, cframe, vector)
    local ToySpawn = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
    local InPlot, InOwnerPlot, CanSpawn = LocalPlayer.InPlot, LocalPlayer.InOwnedPlot, LocalPlayer.CanSpawnToy

    while InPlot.Value and not InOwnerPlot.Value and not CanSpawn.Value do
        task.wait(0.01)
    end

    task.spawn(function()
        ToySpawn:InvokeServer(name, cframe, vector or Vector3.new())
    end)
    
    local BackPack = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
    local SpawnedToy
    BackPack.ChildAdded:Once(function(toy)
        if toy.Name == name and toy:IsA("Model") then
            SpawnedToy = toy
        end
    end)
    
    local time = tick()
    while not SpawnedToy do
        if tick()-time < 2 then
            task.wait(0.01)
        else
            return false
        end
    end
    return SpawnedToy
end

local function GucciAntiGrab()
    gucciRunId = gucciRunId + 1
    local MyId = gucciRunId
    
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hum = FWC(char, "Humanoid")
    
    hum.Sit = true
    task.wait(0.02)
    hum.Sit = false
    task.wait(0.02)
    
    task.spawn(function()
        local time = tick()
        while tick()-time < 0.8 do
            for _,v in pairs(char:GetChildren()) do
                if v:IsA('BasePart') then
                    v.Velocity = Vector3.new()
                end
            end
            task.wait(0.01)
        end
    end)
    
    local autoGucciT, sitJumpT, Blob, BHead = true, false, nil, nil
    
    task.spawn(function()
        while not Blob and MyId == gucciRunId do
            task.wait(0.01)
        end
        if MyId ~= gucciRunId then return end
        
        BHead = FWC(Blob, "Head")
        local HitBox = FWC(Blob, "GrabbableHitbox")
        
        while MyId == gucciRunId and BHead and
        (not BHead:FindFirstChild("PartOwner") or BHead.PartOwner.Value ~= LocalPlayer.Name) do
            grab(HitBox)
            task.wait(0.01)
        end
    end)
    
    local hrp = FWC(char, "HumanoidRootPart")
    Blob = toy_spawn_gucci(
        "CreatureBlobman",
        hrp.CFrame * CFrame.new(0, 0, -5),
        Vector3.new(0, -15.716, 0)
    )
    
    if not Blob then return end
    
    local Seat = FWC(Blob, "VehicleSeat")
    
    task.defer(function()
        if not(char or hum) then return end
        
        local startTime = tick()
        while autoGucciT and MyId == gucciRunId and tick()-startTime < 0.3 do
            if Blob and Blob.Parent then
                if Seat and Seat.Parent and Seat.Occupant ~= hum then
                    Seat:Sit(hum)
                end
            end
            task.wait(0.03)
            if char and hum and hum.Parent then
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
            end
            task.wait(0.03)
        end
        autoGucciT = false
        sitJumpT = false
    end)
    
    sitJumpT = true
    
    task.defer(function()
        while sitJumpT and MyId == gucciRunId do
            if char and hrp and hrp.Parent then
                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0.095)
            end
            task.wait(0.01)
        end
    end)
    
    local index
    
    task.wait(0.4)
    
    if MyId ~= gucciRunId then return end
    
    hum.Sit = false
    Blob.Name = "Gucci"
    
    local BackPack = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
    for i,v in pairs(BackPack:GetChildren()) do
        if v.Name == "Gucci" then
            index = i
            break
        end
    end
    
    for _,v in pairs(Blob:GetChildren()) do
        if v:IsA("BasePart") then
            v.CanCollide = false
            v.CanTouch = false
            v.CanQuery = false
        end
    end
    
    task.defer(function()
        while MyId == gucciRunId and Blob and BHead do
            BHead.CFrame = CFrame.new(BHead.Position.X, 1e5, BHead.Position.Z)
            task.wait(0.01)
        end
    end)
    
    local success, contents = pcall(function()
        return LocalPlayer.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
    end)
    
    if success and contents and index then
        for i,v in ipairs(contents:GetChildren()) do
            if v.Name == "CreatureBlobman" and i == index then
                local view = v.ViewItemButton
                view.Text = "GUCCI"
                view.TextScaled = true
                view.LowResImage.Image = ""
            end
        end
    end
end

-- DESTROY GUCCI 
local function destroyPlayerGucci(targetPlayer)
    if not targetPlayer or targetPlayer == LocalPlayer then return false end
    
    local folderName = targetPlayer.Name .. "SpawnedInToys"
    local toysFolder = Workspace:FindFirstChild(folderName)
    if not toysFolder then return false end
    
    for _, obj in ipairs(toysFolder:GetChildren()) do
        if obj.Name == "CreatureBlobman" then
            local seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
            if seat then
                local myChar = LocalPlayer.Character
                if not myChar then return false end
                
                local myHum = myChar:FindFirstChild("Humanoid")
                local myRoot = myChar:FindFirstChild("HumanoidRootPart")
                if not myHum or not myRoot then return false end
                
                local safeSpot = myRoot.CFrame
                myRoot.CFrame = seat.CFrame
                myRoot.Velocity = Vector3.zero
                seat:Sit(myHum)
                task.wait(0.3)
                
                if myHum.SeatPart == seat then
                    myHum.Sit = false
                    task.wait(0.1)
                    myRoot.CFrame = safeSpot
                    task.wait(0.5)
                    obj:Destroy()
                    return true
                else
                    myRoot.CFrame = safeSpot
                end
            end
        end
    end
    return false
end

local function StartDestroyGucciLoop()
    while destroyGucciActive do
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                destroyPlayerGucci(player)
            end
        end
        task.wait(2)
    end
end

-- JERK OFF 
local function StartJerkOff()
    local plr = LocalPlayer
    local char = plr.Character or plr.CharacterAdded:Wait()
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    
    local animator = hum:FindFirstChildOfClass("Animator")
    if not animator then
        animator = Instance.new("Animator")
        animator.Parent = hum
    end
    
    local anim = Instance.new("Animation")
    anim.AnimationId = "rbxassetid://168268306"
    jerkOffAnimTrack = animator:LoadAnimation(anim)
    jerkOffAnimTrack.Priority = Enum.AnimationPriority.Action
    jerkOffAnimTrack:Play()
    
    playJerkOffActive = true
    task.spawn(function()
        while playJerkOffActive do
            task.wait(0.1)
            if jerkOffAnimTrack and jerkOffAnimTrack.IsPlaying then
                jerkOffAnimTrack.TimePosition = 0.3
            end
        end
    end)
end

local function StopJerkOff()
    playJerkOffActive = false
    if jerkOffAnimTrack then
        jerkOffAnimTrack:Stop()
        jerkOffAnimTrack = nil
    end
end

-- Remove Anti Kick 
local function RemoveAntiKickFunction(targetName)
    local SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
    local LocalPlayer = game.Players.LocalPlayer
    
    local function invis_touch(part, cf)
        SetNetOwner:FireServer(part, cf)
    end
    
    local function CheckAndYeet(toy)
        local part = toy:FindFirstChild("SoundPart")
        if part then
            invis_touch(part, part.CFrame)
            if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
                part.CFrame = CFrame.new(0, 1000, 0)
            end
        end
    end
    
    while antiAntiKickActive do
        local target = game.Players:FindFirstChild(targetName)
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
            end
        end
        task.wait(0.1)
    end
end

-- Remove All Anti Input 
local function RemoveAllAntiInputFunction()
    local AllowedItems = {
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
        CupMugWhite = true,
        CupMugBrown = true,
        PoopPile = true,
        PoopPileSparkle = true,
    }
    
    local plr = game.Players.LocalPlayer
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    local burgers = {}
    local descConnection = workspace.DescendantAdded:Connect(function(obj)
        if AllowedItems[obj.Name] and obj:IsA("Model") then
            task.spawn(function()
                local hp = obj:WaitForChild("HoldPart", 3)
                if hp then
                    table.insert(burgers, obj)
                end
            end)
        end
    end)
    
    for _, v in ipairs(workspace:GetDescendants()) do
        if AllowedItems[v.Name] and v:IsA("Model") and v:FindFirstChild("HoldPart") then
            table.insert(burgers, v)
        end
    end
    
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
    
    descConnection:Disconnect()
end

-- Remove Anti Kick Aura 
local function RemoveAntiKickAuraFunction()
    local RS = game:GetService("ReplicatedStorage")
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local GrabEvents = RS:WaitForChild("GrabEvents")
    local SetNetOwner = GrabEvents:WaitForChild("SetNetworkOwner")
    
    while removeAntiKickAuraActive do
        local myChar = LocalPlayer.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myRoot then 
            task.wait(0.1)
            continue 
        end
        
        for _, target in ipairs(Players:GetPlayers()) do
            if target ~= LocalPlayer then
                if useWhitelistRemoveAntiKick and LocalPlayer:IsFriendsWith(target.UserId) then
                    continue
                end
                
                local tChar = target.Character
                local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                if not tRoot then continue end
                
                if (tRoot.Position - myRoot.Position).Magnitude <= removeAntiKickRadius then
                    local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                    if spawned then
                        for _, toyName in ipairs({"NinjaKunai", "NinjaShuriken", "AntiKick"}) do
                            local toy = spawned:FindFirstChild(toyName)
                            if toy then
                                local part = toy:FindFirstChild("SoundPart")
                                if part then
                                    pcall(function()
                                        SetNetOwner:FireServer(part, part.CFrame)
                                    end)
                                    if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
                                        part.CFrame = CFrame.new(0, 1000, 0)
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
        task.wait(0.1)
    end
end

local function OwnershipKickFunction(targetName)
    local target = Players:FindFirstChild(targetName)
    if not target then return end
    
    local RS = game:GetService("ReplicatedStorage")
    local GE = RS:WaitForChild("GrabEvents")
    local RunService = game:GetService("RunService")
    
    local myChar = LocalPlayer.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then return end

    local savedPos = myRoot.CFrame 
    local dragging = false
    local grabStartTime = 0
    local checkStartTime = 0
    
    local currentFPS = 60
    local fpsConnection = RunService.RenderStepped:Connect(function(dt)
        currentFPS = 1 / dt
    end)

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
        bodyPos.D = 100
        bodyGyro.CFrame = CFrame.new(pos)
        bodyGyro.Parent = targetRoot
    end

    while ownershipKickActive do
        local currentTarget = Players:FindFirstChild(target.Name)
        if not currentTarget or not currentTarget.Parent then 
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
                    GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                    GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                    GE.DestroyGrabLine:FireServer(tRoot)
                end)
                
                myRoot.AssemblyLinearVelocity = Vector3.zero
                myRoot.AssemblyAngularVelocity = Vector3.zero
                
                if grabStartTime == 0 then grabStartTime = tick() end
                if tick() - grabStartTime > 0.35 then
                    dragging = true
                    grabStartTime = 0
                    checkStartTime = tick()
                    local lockPos = savedPos * CFrame.new(0, 17, 0)
                    createBodies(tRoot, lockPos.Position)
                end
            else
                myRoot.CFrame = savedPos
                local lockPos = savedPos * CFrame.new(0, 17, 0)
                
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
                end)
                
                if checkStartTime > 0 and tick() - checkStartTime > 0.30 then
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
    
    fpsConnection:Disconnect()
    cleanupBodies()
    if myRoot then myRoot.CFrame = savedPos end
end

local function PalletRagdollFunction(targetName)
    local target = Players:FindFirstChild(targetName)
    if not target or not target.Character then return end
    
    local RS = ReplicatedStorage
    local GE = RS:WaitForChild("GrabEvents")
    local skyPos = CFrame.new(0, 800000, 0)
    
    RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", skyPos, Vector3.zero)
    
    local pallet
    repeat
        pallet = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys") 
            and workspace[LocalPlayer.Name.."SpawnedInToys"]:FindFirstChild("PalletLightBrown")
        RunService.Heartbeat:Wait()
    until pallet or not ownershipRagdollActive
    
    if not pallet then return end
    
    local mainPart = pallet:FindFirstChild("SoundPart")
    if not mainPart then return end
    
    mainPart.CanCollide = false
    mainPart.Anchored = false
    
    local function claim(part)
        GE.SetNetworkOwner:FireServer(part, part.CFrame)
        GE.CreateGrabLine:FireServer(part, Vector3.zero, part.Position, false)
        GE.DestroyGrabLine:FireServer(part)
    end
    
    claim(mainPart)
    
    while ownershipRagdollActive do
        for i = 1, 20 do
            RunService.Heartbeat:Wait()
        end
        
        if not target or not target.Parent or not target.Character then break end
        
        local head = target.Character:FindFirstChild("Head")
        if not head then continue end
        
        local targetPos = head.Position
        mainPart.CFrame = CFrame.new(targetPos.X, targetPos.Y + 0.2, targetPos.Z)
        mainPart.AssemblyLinearVelocity = Vector3.zero
        mainPart.AssemblyAngularVelocity = Vector3.new(1000, 1000, 1000)
        
        claim(mainPart)
        
        mainPart.CanCollide = true
        for i = 1, 3 do
            RunService.Heartbeat:Wait()
        end
        
        mainPart.CanCollide = false
        mainPart.CFrame = skyPos
        mainPart.AssemblyAngularVelocity = Vector3.zero
    end
    
    if pallet then
        pcall(function()
            RS.MenuToys.DestroyToy:FireServer(pallet)
        end)
        if pallet.Parent then 
            pallet:Destroy() 
        end
    end
end

--  Platform TP 
local function PlatformTPFunction()
    if not platformPart then
        platformPart = Instance.new("Part", workspace)
        platformPart.Name = "SkyBase"
        platformPart.Anchored = true
        platformPart.Size = Vector3.new(1500, 2, 1500)
        platformPart.CFrame = CFrame.new(0, 1000000, 0)
        workspace.FallenPartsDestroyHeight = -9999999
    end
    
    platformTPConnection = UserInputService.InputBegan:Connect(function(input, processed)
        if not processed and input.KeyCode == platformTPKey and platformTPToggle then
            local char = LocalPlayer.Character
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
    end)
end

--  Trace to Target
local function StartTrace(targetName)
    local targetPlayer = Players:FindFirstChild(targetName)
    if not targetPlayer then return end

    local part0 = Instance.new("Part")
    part0.Anchored = true
    part0.CanCollide = false
    part0.Transparency = 1
    part0.Size = Vector3.new(0.1,0.1,0.1)
    part0.Parent = Workspace

    local part1 = Instance.new("Part")
    part1.Anchored = true
    part1.CanCollide = false
    part1.Transparency = 1
    part1.Size = Vector3.new(0.1,0.1,0.1)
    part1.Parent = Workspace

    local att0 = Instance.new("Attachment", part0)
    local att1 = Instance.new("Attachment", part1)

    traceBeam = Instance.new("Beam")
    traceBeam.Attachment0 = att0
    traceBeam.Attachment1 = att1
    traceBeam.Color = ColorSequence.new(traceColor)
    traceBeam.Width0 = 0.6
    traceBeam.Width1 = 0.6
    traceBeam.LightEmission = 0
    traceBeam.LightInfluence = 1
    traceBeam.FaceCamera = true
    traceBeam.Parent = Workspace

    local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local targetHRP = targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart")

    traceConnection = RunService.RenderStepped:Connect(function()
        if not traceEnabled then
            if traceBeam then traceBeam:Destroy() end
            if part0 then part0:Destroy() end
            if part1 then part1:Destroy() end
            return
        end

        if not Players:FindFirstChild(targetName) then
            if traceBeam then traceBeam:Destroy() end
            if part0 then part0:Destroy() end
            if part1 then part1:Destroy() end
            return
        end

        if LocalPlayer.Character and targetPlayer.Character then
            myHRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            targetHRP = targetPlayer.Character:FindFirstChild("HumanoidRootPart")

            if myHRP and targetHRP and part0 and part1 then
                part0.Position = myHRP.Position
                part1.Position = targetHRP.Position
                traceBeam.Color = ColorSequence.new(traceColor)
            end
        end
    end)

    LocalPlayer.CharacterAdded:Connect(function(char)
        myHRP = char:WaitForChild("HumanoidRootPart")
    end)

    targetPlayer.CharacterAdded:Connect(function(char)
        targetHRP = char:WaitForChild("HumanoidRootPart")
    end)
end

--  Anti Banana Sit 
local function AntiBananaSitFunction()
    while antiBananaSitActive do
        local char = LocalPlayer.Character
        if char then
            local hum = char:FindFirstChild("Humanoid")
            local hrp = char:FindFirstChild("HumanoidRootPart")
            
            if hum and hrp and hum.Health > 0 then
                hum.Sit = true
                hum:ChangeState(Enum.HumanoidStateType.Running)
                
                local camera = workspace.CurrentCamera
                if camera then
                    local lookVec = camera.CFrame.LookVector
                    hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(lookVec.X, 0, lookVec.Z))
                end
            end
        end
        task.wait()
    end
end

--  Kill Dodge 
local function KillDodgeFunction()
    local Struggle = ReplicatedStorage:FindFirstChild("Struggle")
    local Tppos = Vector3.new(252, -7, 464)
    
    local function FindPlot()
        for _, plot in pairs(workspace.Plots:GetChildren()) do
            if plot:FindFirstChild(LocalPlayer.Name) then
                return plot
            end
        end
        return nil
    end

    local Plot = FindPlot()
    if Plot then
        if Plot.Name == "Plot1" then Tppos = Vector3.new(-533, -7, 90)
        elseif Plot.Name == "Plot2" then Tppos = Vector3.new(-483, -7, -164)
        elseif Plot.Name == "Plot3" then Tppos = Vector3.new(252, -7, 464)
        elseif Plot.Name == "Plot4" then Tppos = Vector3.new(509, 83, -339)
        else Tppos = Vector3.new(553, 123, -74) end
    end

    LocalPlayer.CharacterAdded:Connect(function(char)
        local hrp = char:WaitForChild("HumanoidRootPart")
        local hum = char:WaitForChild("Humanoid")
        
        task.spawn(function()
            while killDodgeActive do
                task.wait()
                if not (LocalPlayer:FindFirstChild("InPlot") and LocalPlayer.InPlot.Value) and hum.Health > 0 then
                    hrp.CFrame = CFrame.new(Tppos)
                    hrp.Anchored = false
                end
            end
        end)
        
        hum.Died:Connect(function()
            task.wait(2.8)
            while killDodgeActive and not (LocalPlayer:FindFirstChild("InPlot") and LocalPlayer.InPlot.Value) and hum.Health > 0 do
                for i = 1,3 do 
                    task.spawn(function() 
                        if Struggle then Struggle:FireServer(LocalPlayer) end 
                    end) 
                end
                task.wait()
            end
        end)
    end)
end



-- : Anti Ragdoll on Blob 
local function AntiRagBlobFunction()
    local RagdollRemote = ReplicatedStorage:FindFirstChild("RagdollRemote")
    local RagdolledSit = false
    
    local function DiscAR(con)
        if antiRagBlobConnections[con] then
            antiRagBlobConnections[con]:Disconnect()
            antiRagBlobConnections[con] = nil
        end
    end

    local function setupCharacter(char)
        local hum = char and char:FindFirstChild("Humanoid")
        local HRP = char and char:FindFirstChild("HumanoidRootPart")
        
        if hum and HRP and RagdollRemote then
            DiscAR("ARSeat")
            antiRagBlobConnections["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
                if hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                    RagdolledSit = true
                    local Seat = hum.SeatPart
                    
                    while not hum.Sit do task.wait() end
                    
                    RagdollRemote:FireServer(HRP, 3)
                    
                    while not (hum:FindFirstChild("Ragdolled") and hum.Ragdolled.Value) and not hum.Sit do task.wait() end
                    
                    task.wait(0.4)
                    hum.Sit = false
                    
                    if Seat and Seat:IsA("Part") then
                        Seat:Sit(hum)
                    end
                    
                    task.delay(0.25, function()
                        while hum and hum.SeatPart do
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                RagdollRemote:FireServer(LocalPlayer.Character.HumanoidRootPart, 1)
                            end
                            task.wait(0.05)
                        end
                        RagdolledSit = false
                    end)
                end
            end)
        end
    end

    if antiRagBlobActive then
        setupCharacter(LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait())
        
        DiscAR("ARChar")
        antiRagBlobConnections["ARChar"] = LocalPlayer.CharacterAdded:Connect(function(newChar)
            task.wait(0.5)
            setupCharacter(newChar)
        end)
    else
        for _, conn in pairs(antiRagBlobConnections) do
            if conn then conn:Disconnect() end
        end
        antiRagBlobConnections = {}
    end
end

--  Shield 
local function TelekinesisShieldFunction()
    local Players = game:GetService("Players")
    local plr = Players.LocalPlayer
    local setOwner = game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner
    
    while telekinesisShieldActive do
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            for _, v in pairs(workspace:GetDescendants()) do
                if not telekinesisShieldActive then break end
                if v:IsA("BasePart") and not v.Anchored and not v:IsDescendantOf(char) then
                    local dist = (v.Position - hrp.Position).Magnitude
                    if dist <= 60 then
                        local pushDir = (v.Position - hrp.Position).Unit
                        local velocity = (pushDir + Vector3.new(0, 0.2, 0)).Unit * 100

                        pcall(function()
                            setOwner:FireServer(v, v.CFrame)
                            v.AssemblyLinearVelocity = velocity
                            v.AssemblyAngularVelocity = Vector3.new(
                                math.random(-10,10),
                                math.random(-10,10),
                                math.random(-10,10)
                            )
                        end)
                    end
                end
            end
        end
        task.wait(0.1)
    end
end

-- Loop Kill
local function LoopKillFunction(targetName)
    local target = game.Players:FindFirstChild(targetName)
    if not target then return end
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local GE = RS:WaitForChild("GrabEvents")

    while loopKillActive and target and target.Parent do
        if not target.Character then
            task.wait(0.5)
            continue
        end
        local myChar = game.Players.LocalPlayer.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        local tChar = target.Character
        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
        local tHum = tChar and tChar:FindFirstChild("Humanoid")
        if tRoot and tHum and tHum.Health > 0 and myRoot then
            local currentPos = myRoot.CFrame
            local attackStart = tick()
            while tick() - attackStart < 0.35 and loopKillActive do
                if not tRoot.Parent then break end
                myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 2)
                myRoot.Velocity = Vector3.zero
                pcall(function()
                    GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                    tHum:ChangeState(Enum.HumanoidStateType.Dead)
                    tHum.Health = 0
                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                    GE.DestroyGrabLine:FireServer(tRoot)
                end)
                RunService.Heartbeat:Wait()
            end
            if myRoot then
                myRoot.CFrame = currentPos
                myRoot.Velocity = Vector3.zero
            end
            task.wait(1.2)
        else
            task.wait(0.5)
        end
    end
    local char = game.Players.LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then root.Velocity = Vector3.zero end
end

-- Snowball Ragdoll
local function SnowballRagdollFunction(targetName)
    local target = Players:FindFirstChild(targetName)
    if not target then return end
    local Player = Players.LocalPlayer
    local SpawnRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")

    while snowballRagdollActive do
        if not target or not target.Parent then break end
        local tChar = target.Character
        local torso = tChar and (tChar:FindFirstChild("UpperTorso") or tChar:FindFirstChild("Torso"))
        if not torso then continue end

        pcall(function()
            local offset = Vector3.new(
                math.random(-30, 30) / 100,
                math.random(-30, 30) / 100,
                math.random(-30, 30) / 100
            )
            local spawnCFrame = torso.CFrame * CFrame.new(offset)
            SpawnRemote:InvokeServer("BallSnowball", spawnCFrame, Vector3.zero)
        end)

        local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
        if folder then
            for _, snowball in pairs(folder:GetChildren()) do
                if snowball.Name == "BallSnowball" and snowball.Parent then
                    local part = snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart")
                    if part then
                        local offset = Vector3.new(
                            math.random(-30, 30) / 100,
                            math.random(-30, 30) / 100,
                            math.random(-30, 30) / 100
                        )
                        part.CFrame = torso.CFrame * CFrame.new(offset)
                        part.AssemblyLinearVelocity = Vector3.zero
                        part.AssemblyAngularVelocity = Vector3.zero
                    end
                end
            end
        end
        task.wait()
    end
end

-- Loop Kick Grab + Blob
local function LoopKickBlobFunction(targetName)
    local blobLoop = true
    local Players = game:GetService("Players")
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local LocalPlayer = Players.LocalPlayer
    local GE = RS:WaitForChild("GrabEvents")

    local REMOTE_DELAY = 0.002
    local lastRemote = 0

    local function BlobGrabKickHard()
        local target = Players:FindFirstChild(targetName)
        if not target then return warn("Target not found") end

        local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local hum = char:WaitForChild("Humanoid")
        local seat = hum.SeatPart
        if not seat or seat.Parent.Name ~= "CreatureBlobman" then
            return warn("Sit on Blobman first")
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

        while blobLoop and loopKickBlobActive do
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

                            local weld =
                                R_Det:FindFirstChild("RightWeld")
                                or R_Det:FindFirstChildWhichIsA("Weld")

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
end

-- Server Lag Line
local function ServerLagLineFunction(intensity)
    local players = game:GetService("Players")
    local rs = game:GetService("ReplicatedStorage")
    local createGrabLineEvent = rs:WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine")
    
    while lagLineActive do
        for _ = 1, intensity do
            for _, player in pairs(players:GetPlayers()) do
                if player.Character then
                    local torso = player.Character:FindFirstChild("Torso") or player.Character:FindFirstChild("UpperTorso")
                    if torso then
                        createGrabLineEvent:FireServer(torso, torso.CFrame)
                    end
                end
            end
        end
        task.wait(1)
    end
end

-- GUI

-- DEF
local LeftGroupBox = Tabs.Defence:AddLeftGroupbox("Anti Functions")
local RightGroupBox = Tabs.Defence:AddRightGroupbox("Extra Defense")

-- Anti Ownership
LeftGroupBox:AddToggle("AntiOwnershipToggle", {
    Text = "Anti Ownership",
    Default = false,
    Callback = function(Value)
        antiOwnershipActive = Value
        if Value then
            antiOwnershipTask = task.spawn(AntiOwnershipFunction)
        else
            if antiOwnershipTask then
                task.cancel(antiOwnershipTask)
                antiOwnershipTask = nil
            end
            local char = LocalPlayer.Character
            if char then
                for _, part in pairs(char:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Anchored = false
                    end
                end
            end
        end
    end
})

LeftGroupBox:AddToggle("AntiGrabToggle", {
    Text = "Anti Grab",
    Default = false,
    Callback = function(Value)
        if Value then
            local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            local hrp, hum, head = FWC(char, "HumanoidRootPart"), FWC(char, "Humanoid"), FWC(char, "Head")
            AGConnections["AGHead"] = head.ChildAdded:Connect(function(PartOwner)
                if PartOwner.Name == "PartOwner" then
                    if not antiGrabProc then
                        antiGrabProc = true
                        hum.Sit = false
                        ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                        task.spawn(function() 
                            while (head and head:FindFirstChild("PartOwner")) or LocalPlayer.IsHeld.Value do
                                ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                                task.wait()
                            end
                        end)
                        hrp.Anchored = true
                        if not AGWalk then
                            AGWalk = true
                            while LocalPlayer.IsHeld.Value and task.wait() do 
                                hrp.CFrame = hrp.CFrame + hum.MoveDirection * 0.43 
                            end
                        end
                        hrp.Anchored = false
                        antiGrabProc = false
                        AGWalk = false
                    end
                end
            end)
            AGConnections["AGRagdoll"] = FWC(hum, "Ragdolled").Changed:Connect(function()
                if hum.Ragdolled.Value then
                    for _, v in pairs(char:GetChildren()) do
                        if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                            v.BallSocketConstraint.Enabled = false
                            if v:FindFirstChild("RagdollLimbPart") then
                                v.RagdollLimbPart.WeldConstraint.Enabled = false
                            end
                        end
                    end
                end
            end)
            AGConnections["AGWeld"] = FWC(hrp, "WeldHRP").Changed:Connect(function()
                if hrp.WeldHRP.Enabled then
                    while not hum.Sit do task.wait() end
                    hum.Sit = false
                    hum.AutoRotate = true
                    hum.HipHeight = 1
                    while hrp.WeldHRP.Enabled and task.wait() do 
                        head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) 
                    end
                    hum.HipHeight = 0
                end
            end)
            for _, v in pairs(char:GetChildren()) do
                if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                    v.BallSocketConstraint.Enabled = false
                    if v:FindFirstChild("RagdollLimbPart") then
                        v.RagdollLimbPart.WeldConstraint.Enabled = false
                    end
                end
            end
            AGConnections["AGChar"] = LocalPlayer.CharacterAdded:Connect(function(newChar)
                local newHrp, newHum, newHead = FWC(newChar, "HumanoidRootPart"), FWC(newChar, "Humanoid"), FWC(newChar, "Head")
                AGConnections["AGHeadNew"] = newHead.ChildAdded:Connect(function(PartOwner)
                    if PartOwner.Name == "PartOwner" then
                        if not antiGrabProc then
                            antiGrabProc = true
                            newHum.Sit = false
                            ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                            task.spawn(function() 
                                while (newHead and newHead:FindFirstChild("PartOwner")) or LocalPlayer.IsHeld.Value do
                                    ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                                    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(newHrp, 0)
                                    task.wait()
                                end
                            end)
                            newHrp.Anchored = true
                            if not AGWalk then
                                AGWalk = true
                                while LocalPlayer.IsHeld.Value and task.wait() do 
                                    newHrp.CFrame = newHrp.CFrame + newHum.MoveDirection * 0.43 
                                end
                            end
                            newHrp.Anchored = false
                            antiGrabProc = false
                            AGWalk = false
                        end
                    end
                end)
                AGConnections["AGRagdollNew"] = FWC(newHum, "Ragdolled").Changed:Connect(function()
                    if newHum.Ragdolled.Value then
                        for _, v in pairs(newChar:GetChildren()) do
                            if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                                v.BallSocketConstraint.Enabled = false
                                if v:FindFirstChild("RagdollLimbPart") then
                                    v.RagdollLimbPart.WeldConstraint.Enabled = false
                                end
                            end
                        end
                    end
                end)
                AGConnections["AGWeldNew"] = FWC(newHrp, "WeldHRP").Changed:Connect(function()
                    if newHrp.WeldHRP.Enabled then
                        while not newHum.Sit do task.wait() end
                        newHum.Sit = false
                        newHum.AutoRotate = true
                        newHum.HipHeight = 1
                        while newHrp.WeldHRP.Enabled and task.wait() do 
                            newHead.CFrame = newHrp.CFrame + Vector3.new(0, 1.35, 0) 
                        end
                        newHum.HipHeight = 0
                    end
                end)
                for _, v in pairs(newChar:GetChildren()) do
                    if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                        v.BallSocketConstraint.Enabled = false
                        if v:FindFirstChild("RagdollLimbPart") then
                            v.RagdollLimbPart.WeldConstraint.Enabled = false
                        end
                    end
                end
            end)
        else
            local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            for _, v in pairs(char:GetChildren()) do
                if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
                    v.BallSocketConstraint.Enabled = false
                    if v:FindFirstChild("RagdollLimbPart") then
                        v.RagdollLimbPart.WeldConstraint.Enabled = true
                    end
                end
            end
            Disc("AGHead"); Disc("AGRagdoll"); Disc("AGWeld"); Disc("AGChar"); Disc("AGHeadNew"); Disc("AGRagdollNew"); Disc("AGWeldNew")
        end
    end
})

LeftGroupBox:AddToggle("AntiOwnership2Toggle", {
    Text = "Anti Ownership 2",
    Default = false,
    Callback = function(Value)
        if Value then
            local plr = LocalPlayer
            local isHeld = plr:WaitForChild("IsHeld", 5)
            local struggleEvent = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
            local savedCFrame = nil
            
            AGConnections["AntiOwn2"] = isHeld.Changed:Connect(function(heldState)
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if heldState then
                    if hrp then 
                        savedCFrame = hrp.CFrame
                        hrp.Anchored = true 
                    end
                    
                    AGConnections["AntiOwn2Loop"] = task.spawn(function()
                        while isHeld and isHeld.Value and Toggles.AntiOwnership2Toggle.Value do 
                            struggleEvent:FireServer(plr)
                            task.wait() 
                        end
                        if hrp and hrp.Parent then 
                            hrp.Anchored = false 
                            if savedCFrame then hrp.CFrame = savedCFrame end
                        end
                    end)
                else
                    if hrp and hrp.Parent then 
                        hrp.Anchored = false 
                        if savedCFrame then hrp.CFrame = savedCFrame end
                    end
                end
            end)
            
            AGConnections["AntiOwn2Char"] = plr.CharacterAdded:Connect(function(char)
                task.wait(0.5)
                local hrp = char:WaitForChild("HumanoidRootPart")
                savedCFrame = hrp.CFrame
            end)
            
            if isHeld.Value then
                local char = plr.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then 
                    savedCFrame = hrp.CFrame
                    hrp.Anchored = true 
                end
                AGConnections["AntiOwn2Loop"] = task.spawn(function()
                    while isHeld and isHeld.Value and Toggles.AntiOwnership2Toggle.Value do 
                        struggleEvent:FireServer(plr)
                        task.wait() 
                    end
                    if hrp and hrp.Parent then 
                        hrp.Anchored = false 
                        if savedCFrame then hrp.CFrame = savedCFrame end
                    end
                end)
            end
        else
            Disc("AntiOwn2")
            Disc("AntiOwn2Loop")
            Disc("AntiOwn2Char")
            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.Anchored = false
            end
        end
    end
})

-- Gucci Bind
RightGroupBox:AddLabel("Gucci Binder"):AddKeyPicker("GucciKeyPicker", {
    Default = "J",
    Mode = "Press",
    Text = "Gucci Key",
    NoUI = false,
    Callback = function()
        GucciAntiGrab()
    end,
    ChangedCallback = function(NewKey)
        gucciKey = NewKey
    end
})

LeftGroupBox:AddToggle("GoToHomeToggle", {
    Text = "GO to home",
    Default = false,
    Callback = function(Value)
        if Value then
            local Plot = nil
            for _, plot in pairs(Workspace.Plots:GetChildren()) do
                if plot:FindFirstChild(LocalPlayer.Name) then
                    Plot = plot
                    break
                end
            end
            local Tppos
            if Plot then
                if Plot.Name == "Plot1" then Tppos = Vector3.new(-533, -7, 90)
                elseif Plot.Name == "Plot2" then Tppos = Vector3.new(-483, -7, -164)
                elseif Plot.Name == "Plot3" then Tppos = Vector3.new(252, -7, 464)
                elseif Plot.Name == "Plot4" then Tppos = Vector3.new(509, 83, -339)
                else Tppos = Vector3.new(553, 123, -74) end
            else
                Tppos = Vector3.new(252, -7, 464)
            end
            local function setupGoToHome(char)
                local hrp = FWC(char, "HumanoidRootPart")
                local hum = FWC(char, "Humanoid")
                task.spawn(function()
                    while Toggles.GoToHomeToggle.Value and LocalPlayer.InPlot and not LocalPlayer.InPlot.Value and hum.Health ~= 0 and task.wait() do
                        hrp.CFrame = CFrame.new(Tppos)
                        hrp.Anchored = false
                    end
                end)
                hum.Died:Connect(function()
                    if Toggles.GoToHomeToggle.Value then
                        task.wait(2.8)
                        while Toggles.GoToHomeToggle.Value and LocalPlayer.InPlot and not LocalPlayer.InPlot.Value and hum.Health ~= 0 do
                            for i = 1, 3 do 
                                task.spawn(function() 
                                    ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer) 
                                end) 
                            end
                            task.wait()
                        end
                    end
                end)
            end
            setupGoToHome(LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait())
            LocalPlayer.CharacterAdded:Connect(setupGoToHome)
        end
    end
})

LeftGroupBox:AddToggle("PlotBarriersToggle", {
    Text = "Anti Barrier",
    Default = false,
    Callback = function(Value)
        local plots = workspace:FindFirstChild("Plots")
        if not plots then return end
        for _, plot in ipairs(plots:GetChildren()) do
            local barrierModel = plot:FindFirstChild("Barrier")
            if barrierModel then
                for _, part in ipairs(barrierModel:GetChildren()) do
                    if part:IsA("BasePart") and part.Name == "PlotBarrier" then
                        part.CanCollide = not Value
                    end
                end
            end
        end
    end
})

LeftGroupBox:AddToggle("PaintDeleteToggle", {
    Text = "Anti Paint",
    Default = false,
    Callback = function(state)
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

--  Delete Legs 
LeftGroupBox:AddButton({
    Text = "Delete Legs",
    Func = function()
        local players = game:GetService("Players")
        local localPlayer = players.LocalPlayer
        local workspace = game:GetService("Workspace")
        local rs = game:GetService("ReplicatedStorage")
        local RagdollRemote = rs:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")
        
        local character = localPlayer.Character
        if not character then 
            character = localPlayer.CharacterAdded:Wait()
        end
        
        local leftLeg = character:FindFirstChild("Left Leg")
        local rightLeg = character:FindFirstChild("Right Leg")
        local torso = character:WaitForChild("Torso") or character:WaitForChild("UpperTorso")
        local hrp = character:WaitForChild("HumanoidRootPart")
        
        if leftLeg and rightLeg and torso and hrp then
            local originalFallHeight = workspace.FallenPartsDestroyHeight
            local originalCFrame = torso.CFrame
            
            workspace.FallenPartsDestroyHeight = -100
            RagdollRemote:FireServer(hrp, 2)
            
            wait(0.5)
            
            leftLeg.CFrame = CFrame.new(0, -10000, 0)
            rightLeg.CFrame = CFrame.new(0, -10000, 0)
            
            wait(0.3)
            
            torso.CFrame = CFrame.new(0, -9970, 0)
            
            wait(0.5)
            
            torso.CFrame = originalCFrame
            
            wait(0.5)
            workspace.FallenPartsDestroyHeight = originalFallHeight
        end
    end,
    DoubleClick = false
})

LeftGroupBox:AddToggle("ShurikenAntiKickToggle", {
    Text = "Shuriken Anti Kick",
    Default = false,
    Callback = function(Value)
        _G.ShurikenAntiKick = Value
        if Value then
            local function ClearKunai()
                local plr = LocalPlayer
                local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                local destroyrem = game.ReplicatedStorage:FindFirstChild("MenuToys") and game.ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy")
                if inv and destroyrem then
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                            pcall(function() destroyrem:FireServer(v) end)
                        end
                    end
                end
            end
            task.spawn(function()
                local plr = LocalPlayer
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local spawnRemote = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                local destroyrem = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
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
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        return false
                    end
                    for _, v in pairs(workspace.Plots:GetChildren()) do
                        local sign = v:FindFirstChild("PlotSign")
                        local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                        if owners then
                            for _, b in pairs(owners:GetChildren()) do
                                if b.Value == plr.Name then
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
                        if not kunai.SoundPart:FindFirstChild("PartOwner") or kunai.SoundPart.PartOwner.Value ~= plr.Name then 
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
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    if boolik and house then 
                        return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and inv then 
                        return inv:WaitForChild(name, 2)
                    end
                    return nil
                end
                while _G.ShurikenAntiKick do 
                    task.wait(0.005)
                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                        continue 
                    end
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    local kunai = inv and inv:FindFirstChild("NinjaShuriken")
                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
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
                        task.wait(0.3)
                    until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false 
                        or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") 
                        or not kunai:FindFirstChild("StickyPart") 
                        or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                    if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then 
                        ClearKunai()
                    end 
                    pcall(function()
                        repeat
                            task.wait(0.05)
                        until not _G.ShurikenAntiKick or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        if not kunai or not kunai:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                            ClearKunai()
                        end
                    end)
                end
                ClearKunai()
            end)
        else
            local function ClearKunai()
                local plr = LocalPlayer
                local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                local destroyrem = game.ReplicatedStorage:FindFirstChild("MenuToys") and game.ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy")
                if inv and destroyrem then
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                            pcall(function() destroyrem:FireServer(v) end)
                        end
                    end
                end
            end
            ClearKunai()
        end
    end
})

LeftGroupBox:AddToggle("AntiLagToggle", {
    Text = "Anti Lag",
    Default = false,
    Callback = function(Value)
        local localPlayer = Players.LocalPlayer
        local playerScriptsFolder = localPlayer:WaitForChild("PlayerScripts")
        local antiCreateLineScript = playerScriptsFolder:WaitForChild("CharacterAndBeamMove")
        
        if Value then
            antiCreateLineScript.Disabled = true
            

            for _, v in ipairs(workspace:GetDescendants()) do
                if v:IsA("Beam") or v.Name:lower():find("line") then 
                    v:Destroy() 
                end
            end
        else
            -- Выключаем анти-лаг: включаем скрипт обратно
            antiCreateLineScript.Disabled = false
        end
    end
})

-- EXTRA DEFENSE
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
}

local dropdownValues = {}
for shortName, _ in pairs(ToyList) do
    table.insert(dropdownValues, shortName)
end
table.sort(dropdownValues)

local SelectedToy = ToyList[dropdownValues[1]]

RightGroupBox:AddDropdown("AntiInputLagToy", {
    Text = "Input Lag Item",
    Values = dropdownValues,
    Default = 1,
    Callback = function(Value)
        SelectedToy = ToyList[Value]
    end
})

RightGroupBox:AddToggle("AntiInputLag", {
    Text = "Anti Input Lag",
    Default = false,
    Callback = function(Value)
        if Value then
            _G.AntiInputLag = true
            antiInputLagTask = task.spawn(function()
                local Players = game:GetService("Players")
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                local Workspace = game:GetService("Workspace")
                local RunService = game:GetService("RunService")
                
                local plr = Players.LocalPlayer
                local char = plr.Character or plr.CharacterAdded:Wait()
                local hrp = char:WaitForChild("HumanoidRootPart")
                local SpawnRemote = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                
                while _G.AntiInputLag do
                    local toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    if not toysFolder then
                        task.wait(0.1)
                        continue
                    end
                    
                    local toy = toysFolder:FindFirstChild(SelectedToy)
                    
                    if not toy then
                        pcall(function()
                            SpawnRemote:InvokeServer(SelectedToy, hrp.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
                        end)
                        
                        local t0 = tick()
                        repeat
                            RunService.Heartbeat:Wait()
                            toysFolder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                            toy = toysFolder and toysFolder:FindFirstChild(SelectedToy)
                        until toy or tick() - t0 > 1 or not _G.AntiInputLag
                    end
                    
                    if toy and toy.Parent then
                        local holdPart = toy:FindFirstChild("HoldPart")
                        if holdPart then
                            local holdingPlayer = holdPart:FindFirstChild("HoldingPlayer")
                            holdingPlayer = holdingPlayer and holdingPlayer.Value
                            
                            if holdingPlayer and holdingPlayer ~= plr then
                                pcall(function()
                                    holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 2000, 0), Vector3.zero)
                                end)
                                toy:Destroy()
                            else
                                pcall(function()
                                    holdPart.HoldItemRemoteFunction:InvokeServer(toy, char)
                                end)
                                task.wait(0.05)
                                
                                pcall(function()
                                    holdPart.DropItemRemoteFunction:InvokeServer(toy, hrp.CFrame * CFrame.new(0, 2000, 0), Vector3.zero)
                                end)
                                task.wait(0.01)
                            end
                        end
                    end
                    
                    RunService.Heartbeat:Wait()
                end
            end)
        else
            _G.AntiInputLag = false
            if antiInputLagTask then
                task.cancel(antiInputLagTask)
                antiInputLagTask = nil
            end
        end
    end
})

RightGroupBox:AddToggle("LoopTPToggle", {
    Text = "Loop TP",
    Default = false,
    Callback = function(Value)
        loopTPActive = Value
        if Value then
            loopTPTask = task.spawn(LoopTPFunction)
        else
            if loopTPTask then
                task.cancel(loopTPTask)
                loopTPTask = nil
            end
            local char = LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum then
                    hum.PlatformStand = false
                end
            end
        end
    end
})

RightGroupBox:AddToggle("AntiExplosionToggle", {
    Text = "Anti Explosion",
    Default = false,
    Callback = function(Value)
        antiExplosionActive = Value
        if Value then
            StartAntiExplosion()
        else
            if antiExplosionConnection then
                antiExplosionConnection:Disconnect()
                antiExplosionConnection = nil
            end
            local char = LocalPlayer.Character
            if char then
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.Anchored = false
                end
            end
        end
    end
})

RightGroupBox:AddToggle("AntiBurnToggle", {
    Text = "Anti Burn",
    Default = false,
    Callback = function(Value)
        antiBurnActive = Value
        if Value then
            StartAntiBurn()
        else
            if antiBurnConnection then
                antiBurnConnection:Disconnect()
                antiBurnConnection = nil
            end
        end
    end
})

RightGroupBox:AddToggle("AntiVoidToggle", {
    Text = "Anti Void",
    Default = false,
    Callback = function(Value)
        antiVoidActive = Value
        if Value then
            StartAntiVoid()
        else
            if antiVoidConnection then
                antiVoidConnection:Disconnect()
                antiVoidConnection = nil
            end
        end
    end
})
RightGroupBox:AddToggle("AntiBananaSitToggle", {
    Text = "Anti Banana Sit",
    Default = false,
    Callback = function(Value)
        antiBananaSitActive = Value
        if Value then
            antiBananaSitTask = task.spawn(AntiBananaSitFunction)
        else
            if antiBananaSitTask then
                task.cancel(antiBananaSitTask)
                antiBananaSitTask = nil
            end
        end
    end
})

RightGroupBox:AddToggle("PlatformTPToggle", {
    Text = "Platform TP",
    Default = false,
    Callback = function(Value)
        platformTPToggle = Value
        if Value then
            PlatformTPFunction()
        else
            if platformTPConnection then
                platformTPConnection:Disconnect()
                platformTPConnection = nil
            end
            platformTPActive = false
        end
    end
})

RightGroupBox:AddLabel("Platform TP Key"):AddKeyPicker("PlatformTPKey", {
    Default = "X",
    Mode = "Press",
    Text = "Platform TP Key",
    NoUI = false,
    Callback = function(Value)
    end,
    ChangedCallback = function(NewKey)
        platformTPKey = NewKey
    end
})

RightGroupBox:AddToggle("KillDodgeToggle", {
    Text = "Kill Dodge",
    Default = false,
    Callback = function(Value)
        killDodgeActive = Value
        if Value then
            KillDodgeFunction()
        end
    end
})

RightGroupBox:AddToggle("AntiRagBlobToggle", {
    Text = "Anti Ragdoll on Blob",
    Default = false,
    Callback = function(Value)
        antiRagBlobActive = Value
        AntiRagBlobFunction()
    end
})

RightGroupBox:AddToggle("TelekinesisShieldToggle", {
    Text = "Telekinesis Shield",
    Default = false,
    Callback = function(Value)
        telekinesisShieldActive = Value
        if Value then
            telekinesisShieldTask = task.spawn(TelekinesisShieldFunction)
        else
            if telekinesisShieldTask then
                task.cancel(telekinesisShieldTask)
                telekinesisShieldTask = nil
            end
        end
    end
})

RightGroupBox:AddToggle("FlyingResetToggle", {
    Text = "Flying Reset",
    Default = false,
    Callback = function(Value)
        flyingResetActive = Value
        if Value then
            local Players = game:GetService("Players")
            local rs = game:GetService("ReplicatedStorage")
            local me = Players.LocalPlayer

            local CharacterEvents = rs:WaitForChild("CharacterEvents")
            local Struggle = CharacterEvents:WaitForChild("Struggle")

            local CorrectionEvents = rs:WaitForChild("GameCorrectionEvents")
            local GameNotify = CorrectionEvents:WaitForChild("GameCorrectionsNotify")

            flyingResetConnection = GameNotify.OnClientEvent:Connect(function(Type)
                if flyingResetActive and Type == "Flying" then
                    Struggle:FireServer(me)
                    
                    local character = me.Character
                    if character then
                        local humanoid = character:FindFirstChildOfClass("Humanoid")
                        if humanoid then
                            humanoid.Health = 0
                        end
                    end
                end
            end)
        else
            if flyingResetConnection then
                flyingResetConnection:Disconnect()
                flyingResetConnection = nil
            end
        end
    end
})

-- Break PCLD 
RightGroupBox:AddButton({
    Text = "Break PCLD",
    Func = function()
        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")

        local plr = Players.LocalPlayer
        local serverPos = CFrame.new(-272.2197265625, -7.350403785705566, 475.0108947753906)
        workspace.FallenPartsDestroyHeight = 0/0

        local storedJoints = {}
        local root
        local conn
        local active = false

        local function breakPCLD()
            local char = plr.Character
            if not char then return end
            root = char:WaitForChild("HumanoidRootPart")

            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("Motor6D") then
                    storedJoints[v] = v.Part0
                    v.Part0 = nil
                end
            end

            root.CFrame = serverPos

            conn = RunService.RenderStepped:Connect(function()
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
            end)
        end

        local function restore()
            if conn then conn:Disconnect() conn = nil end

            for m, p0 in pairs(storedJoints) do
                if m and m.Parent then
                    m.Part0 = p0
                end
            end
            storedJoints = {}
        end

        local function press6()
            active = not active
            if active then
                breakPCLD()
            else
                restore()
            end
        end

        press6()
        task.wait(0.12)
        press6()

        plr.CharacterAdded:Once(function()
            task.wait(0.25)
            press6()
            task.wait(0.12)
            press6()
        end)
    end,
    DoubleClick = false
})

-- TARGET 
local TargetLeft = Tabs.Target:AddLeftGroupbox("Target Selection")
local TargetRight = Tabs.Target:AddRightGroupbox("Blobman Features")
local KickSection = Tabs.Target:AddRightGroupbox("Kick Methods")
local XZSection = Tabs.Target:AddRightGroupbox("XZ")
local TargetExtra = Tabs.Target:AddLeftGroupbox("Extra")
local TraceSection = Tabs.Target:AddLeftGroupbox("Trace")

local playerList = updatePlayerList()

TargetLeft:AddDropdown("TargetPlayer", {
    Text = "Target Player Name",
    Values = playerList,
    Default = playerList[1] or nil,
    Callback = function(Value) end
})

TargetLeft:AddButton({
    Text = "Refresh List",
    Func = function()
        local newList = updatePlayerList()
        Options.TargetPlayer:SetValues(newList)
        if #newList > 0 then
            Options.TargetPlayer:SetValue(newList[1])
        end
    end,
    DoubleClick = false
})

-- Destroy Gucci
TargetExtra:AddToggle("DestroyGucciToggle", {
    Text = "Destroy Gucci",
    Default = false,
    Callback = function(Value)
        destroyGucciActive = Value
        if Value then
            destroyGucciTask = task.spawn(StartDestroyGucciLoop)
        else
            if destroyGucciTask then
                task.cancel(destroyGucciTask)
                destroyGucciTask = nil
            end
        end
    end
})

-- Remove All Anti Input 
TargetExtra:AddToggle("RemoveAllAntiInputToggle", {
    Text = "Remove All Anti Input",
    Default = false,
    Callback = function(Value)
        antiAntiLagEnabled = Value
        if Value then
            antiAntiLagTask = task.spawn(RemoveAllAntiInputFunction)
        else
            if antiAntiLagTask then
                task.cancel(antiAntiLagTask)
                antiAntiLagTask = nil
            end
        end
    end
})

-- Leave Join Target Notify 
TargetExtra:AddToggle("TargetNotifyToggle", {
    Text = "Leave/Join Target Notify",
    Default = false,
    Callback = function(Value)
        if Value then
            local targetName = Options.TargetPlayer.Value
            if not targetName or targetName == "" then
                Toggles.TargetNotifyToggle:SetValue(false)
                return
            end
            
            local target = Players:FindFirstChild(targetName)
            if target then
                Library:Notify({
                    Title = "POLAR HUB",
                    Description = target.DisplayName .. " (" .. target.Name .. ") is currently in game",
                    Duration = 3
                })
            end
            
            targetNotifyConnections["Added"] = Players.PlayerAdded:Connect(function(player)
                if player.Name == Options.TargetPlayer.Value then
                    Library:Notify({
                        Title = "POLAR HUB",
                        Description = player.DisplayName .. " (" .. player.Name .. ") Joined",
                        Duration = 3
                    })
                end
            end)
            
            targetNotifyConnections["Removing"] = Players.PlayerRemoving:Connect(function(player)
                if player.Name == Options.TargetPlayer.Value then
                    Library:Notify({
                        Title = "POLAR HUB",
                        Description = player.DisplayName .. " (" .. player.Name .. ") Left",
                        Duration = 3
                    })
                end
            end)
        else
            for _, conn in pairs(targetNotifyConnections) do
                if conn then conn:Disconnect() end
            end
            targetNotifyConnections = {}
        end
    end
})

-- Remove Anti Kick 
TargetRight:AddToggle("RemoveAntiKickToggle", {
    Text = "Remove Anti Kick",
    Default = false,
    Callback = function(Value)
        antiAntiKickActive = Value
        if Value then
            local targetName = Options.TargetPlayer.Value
            if targetName and targetName ~= "" then
                antiAntiKickTask = task.spawn(function()
                    RemoveAntiKickFunction(targetName)
                end)
            else
                antiAntiKickActive = false
            end
        else
            if antiAntiKickTask then
                task.cancel(antiAntiKickTask)
                antiAntiKickTask = nil
            end
        end
    end
})

-- Auto Sit Blobman
TargetRight:AddToggle("AutoSitBlobmanToggle", {
    Text = "Auto Sit Blobman",
    Default = false,
    Callback = function(Value)
        _G.AutoSitBlobZ = Value
        if Value then
            task.spawn(AutoSitLoop)
        end
    end
})

TargetRight:AddDropdown("MethodSelect", {
    Text = "Selected Method",
    Values = {"Bring", "Loop Kick", "Bypass", "Kick", "Loop Kick (Grab+Blob)", "Blob Kill", "Lock"},
    Default = "Bring",
    Callback = function(Value) end
})

-- BLOB KILL FUNCTION
local function BlobKill(targetPlayerName)
    local Players = game:GetService("Players")
    local me = Players.LocalPlayer
    local rs = game:GetService("ReplicatedStorage")
    local FWC = function(Parent, Name, Time) 
        return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3) 
    end
    local grab = function(prt) 
        rs.GrabEvents.SetNetworkOwner:FireServer(prt, prt.CFrame) 
    end
    local blob_kick = function(blob, hrp, rl, v)
        local detec = blob:FindFirstChild(rl .. "Detector")
        if not detec then return end
        local script = blob.BlobmanSeatAndOwnerScript
        if v == "Default" then
            script.CreatureGrab:FireServer(detec, hrp, detec[rl .. "Weld"])
        elseif v == "DDrop" then
            script.CreatureDrop:FireServer(detec[rl .. "Weld"])
        elseif v == "Release" then
            script.CreatureRelease:FireServer(detec[rl .. "Weld"], hrp)
        end
    end
    
    local kfmb = true
    local MyBlob
    
    while true do
        local mychar = me.Character or me.CharacterAdded:Wait()
        local myhum = FWC(mychar, "Humanoid")
        if myhum.SeatPart then
            MyBlob = myhum.SeatPart.Parent
            break
        end
        task.wait()
    end
    
    while kfmb and task.wait() do
        local mychar = me.Character or me.CharacterAdded:Wait()
        local myHRP = FWC(mychar, "HumanoidRootPart")
        local myhum = FWC(mychar, "Humanoid")
        
        if not myhum.SeatPart then 
            kfmb = false
            print("Stopped: left Blob")
            break
        end
        
        if myhum.SeatPart.Parent ~= MyBlob then
            kfmb = false
            print("Stopped: changed Blob")
            break
        end
        
        local plr = Players:FindFirstChild(targetPlayerName)
        if not plr then continue end
        
        local char = plr.Character
        if not char then continue end
        
        local hum = FWC(char, "Humanoid", 2)
        local HRP = FWC(char, "HumanoidRootPart", 2)
        
        if not (hum and HRP) then continue end
        
        if hum.Health == 0 then
            char = plr.CharacterAdded:Wait()
            hum = FWC(char, "Humanoid", 2)
            HRP = FWC(char, "HumanoidRootPart", 2)
            task.wait(0.15)
            if not (hum and HRP) then continue end
        end
        
        if not (kfmb and MyBlob and MyBlob.Parent) then continue end
        
        local LD = MyBlob:FindFirstChild("LeftDetector")
        local LW = LD and LD:FindFirstChild("LeftWeld")
        
        if LD and LW then
            while LW.Attachment0 ~= HRP.RootAttachment and kfmb do
                local SavedPosition = myHRP.CFrame
                
                while hum.SeatPart do
                    task.spawn(grab, HRP)
                    task.wait()
                end
                
                for i = 1, 4 do
                    if not myhum.SeatPart then 
                        kfmb = false
                        break
                    end
                    
                    myHRP.CFrame = HRP.CFrame - Vector3.new(0, 10, 0)
                    blob_kick(MyBlob, HRP, "Left", "Default")
                    task.wait(0.05)
                    blob_kick(MyBlob, HRP, "Left", "Release")
                    hum.Health = 0
                    task.wait()
                end
                
                if not kfmb then break end
                myHRP.CFrame = SavedPosition
            end
        end
    end
end

-- BLOB HEAL FUNCTION 
local function BlobHeal(targetPlayerName)
    local Players = game:GetService("Players")
    local me = Players.LocalPlayer
    local rs = game:GetService("ReplicatedStorage")
    local FWC = function(Parent, Name, Time) 
        return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3) 
    end
    local grab = function(prt) 
        rs.GrabEvents.SetNetworkOwner:FireServer(prt, prt.CFrame) 
    end
    local blob_kick = function(blob, hrp, rl, v)
        local detec = blob:FindFirstChild(rl .. "Detector")
        if not detec then return end
        local script = blob.BlobmanSeatAndOwnerScript
        if v == "Default" then
            script.CreatureGrab:FireServer(detec, hrp, detec[rl .. "Weld"])
        elseif v == "DDrop" then
            script.CreatureDrop:FireServer(detec[rl .. "Weld"])
        elseif v == "Release" then
            script.CreatureRelease:FireServer(detec[rl .. "Weld"], hrp)
        end
    end
    
    local MyBlob
    while true do
        local mychar = me.Character or me.CharacterAdded:Wait()
        local myhum = FWC(mychar, "Humanoid")
        if myhum.SeatPart then
            MyBlob = myhum.SeatPart.Parent
            break
        end
        task.wait()
    end
    
    local function DoHeal()
        local mychar = me.Character or me.CharacterAdded:Wait()
        local myHRP = FWC(mychar, "HumanoidRootPart")
        local myhum = FWC(mychar, "Humanoid")
        
        if not myhum.SeatPart then 
            print("Not sitting on Blob!")
            return false 
        end
        
        if myhum.SeatPart.Parent ~= MyBlob then
            print("Changed Blob!")
            return false 
        end
        
        local plr = Players:FindFirstChild(targetPlayerName)
        if not plr then return false end
        
        local char = plr.Character
        if not char then return false end
        
        local hum = char:FindFirstChild("Humanoid")
        local HRP = char:FindFirstChild("HumanoidRootPart")
        
        if not (hum and HRP) then return false end
        if not (MyBlob and MyBlob.Parent) then return false end
        
        local LD = MyBlob:FindFirstChild("LeftDetector")
        local LW = LD and LD:FindFirstChild("LeftWeld")
        
        if not (LD and LW) then return false end
        
        local SavedPosition = myHRP.CFrame
        
        task.spawn(grab, HRP)
        task.wait(0.1)
        
        for i = 1, 3 do
            if not myhum.SeatPart then return false end
            
            myHRP.CFrame = HRP.CFrame * CFrame.new(0, 0, -2.5)
            blob_kick(MyBlob, HRP, "Left", "Default")
            task.wait(0.08)
            blob_kick(MyBlob, HRP, "Left", "Release")
            hum.Health = hum.MaxHealth
            task.wait(0.08)
        end
        
        myHRP.CFrame = SavedPosition
        print("Heal completed for " .. targetPlayerName)
        return true
    end
    
    local success1 = DoHeal()
    if not success1 then
        task.wait(0.5)
        DoHeal()
    end
end

-- Target 

-- Target functions
local function Bring(targetPlayerName)
    local NetworkRemote = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
    local TargetPlayer = Players:FindFirstChild(targetPlayerName)
    if not TargetPlayer then return end
    local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local Humanoid = Character:WaitForChild("Humanoid")
    local RootPart = Character:WaitForChild("HumanoidRootPart")
    local Seat = Humanoid.SeatPart
    if not Seat or not TargetPlayer or TargetPlayer == LocalPlayer then return end
    local SeatObject = Seat.Parent
    local TargetCharacter = TargetPlayer.Character or TargetPlayer.CharacterAdded:Wait()
    local TargetRoot = TargetCharacter:WaitForChild("HumanoidRootPart")
    local Detector = SeatObject:WaitForChild("LeftDetector")
    local Weld = Detector:WaitForChild("LeftWeld")
    local GrabRemote = SeatObject.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
    local OriginalPosition = RootPart.CFrame
    local OriginalTransparency = {}
    for _, part in ipairs(Character:GetDescendants()) do
        if part:IsA("BasePart") then
            OriginalTransparency[part] = part.Transparency
            part.Transparency = 1
        end
    end
    local Camera = workspace.CurrentCamera
    local OriginalCameraCFrame = Camera.CFrame
    Camera.CameraType = Enum.CameraType.Scriptable
    Camera.CFrame = OriginalCameraCFrame
    RootPart.CFrame = TargetRoot.CFrame * CFrame.new(0, 0, 2.5)
    task.wait()
    GrabRemote:FireServer(Detector, TargetRoot, Weld)
    task.delay(0.1, function()
        GrabRemote:FireServer(Detector, TargetRoot, Weld)
    end)
    task.delay(0.2, function()
        RootPart.CFrame = OriginalPosition
        for part, transparency in pairs(OriginalTransparency) do
            if part and part.Parent then
                part.Transparency = transparency
            end
        end
        Camera.CameraType = Enum.CameraType.Custom
        Camera.CameraSubject = Humanoid
    end)
end

local function Kick(targetPlayerName)
    local target = Players:FindFirstChild(targetPlayerName)
    if not target then return end
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = char:WaitForChild("Humanoid")
    local hrp = char:WaitForChild("HumanoidRootPart")
    local seat = humanoid.SeatPart
    if seat and target and target ~= LocalPlayer then
        local seatParent, targetChar = seat.Parent, target.Character or target.CharacterAdded:Wait()
        local targetHRP = targetChar:WaitForChild("HumanoidRootPart")
        local det = seatParent:WaitForChild("LeftDetector")
        local weld = det:WaitForChild("LeftWeld")
        local grab = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
        local drop = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureDrop")
        local originalCFrame = hrp.CFrame
        hrp.CFrame = targetHRP.CFrame * CFrame.new(0, 0, 3)
        task.wait(0.5)
        grab:FireServer(det, targetHRP, weld)
        task.wait(0.7)
        drop:FireServer(weld, targetHRP)
        task.wait(0.3)
        grab:FireServer(det, targetHRP, weld)
        local bp = Instance.new("BodyPosition")
        bp.Position = Vector3.new(0, 999e5000, 0)
        bp.MaxForce = Vector3.new(0, 99999e990, 0)
        bp.Parent = targetHRP
        task.wait(0.6)
        grab:FireServer(det, targetHRP, weld)
        bp:Destroy()
        hrp.CFrame = originalCFrame
    end
end

local function LoopKick(targetPlayerName)
    local target = Players:FindFirstChild(targetPlayerName)
    if not target then return end
    if target == LocalPlayer then return end
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = char:WaitForChild("Humanoid")
    local hrp = char:WaitForChild("HumanoidRootPart")
    local seat = humanoid.SeatPart
    if not seat then return end
    local seatParent = seat.Parent
    local targetChar = target.Character or target.CharacterAdded:Wait()
    local targetHumanoid = targetChar:WaitForChild("Humanoid")
    local targetHRP = targetChar:WaitForChild("HumanoidRootPart")
    local leftDet = seatParent:WaitForChild("LeftDetector")
    local leftWeld = leftDet:WaitForChild("LeftWeld")
    local rightDet = seatParent:WaitForChild("RightDetector")
    local rightWeld = rightDet:WaitForChild("RightWeld")
    local grab = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
    local drop = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureDrop")
    local function grabWithHand(det, weld, targetPart)
        grab:FireServer(det, targetPart, weld)
    end
    local function dropWithHand(weld, targetPart)
        drop:FireServer(weld, targetPart)
    end
    local originalCFrame = hrp.CFrame
    hrp.CFrame = targetHRP.CFrame * CFrame.new(0, -5, 0)
    task.wait(0.1)
    grabWithHand(leftDet, leftWeld, targetHRP)
    task.wait(0.5)
    dropWithHand(leftWeld, targetHRP)
    task.wait(0.2)
    local bp = Instance.new("BodyPosition")
    bp.Position = Vector3.new(0, 999e6, 0)
    bp.MaxForce = Vector3.new(999e6, 999e6, 999e6)
    bp.Parent = targetHRP
    grabWithHand(leftDet, leftWeld, targetHRP)
    task.wait(0.5)
    dropWithHand(leftWeld, targetHRP)
    task.wait(0.5)
    while target and target.Parent and targetHumanoid.Health > 0 do
        if targetHumanoid.Health > 0 then
            grabWithHand(leftDet, leftWeld, targetHRP)
            task.wait()
            dropWithHand(leftWeld, targetHRP)
            task.wait()
            grabWithHand(rightDet, rightWeld, targetHRP)
            task.wait()
            dropWithHand(rightWeld, targetHRP)
            task.wait()
            grabWithHand(leftDet, leftWeld, targetHRP)
            grabWithHand(rightDet, rightWeld, targetHRP)
            task.wait()
            dropWithHand(leftWeld, targetHRP)
            dropWithHand(rightWeld, targetHRP)
            task.wait()
        else
            break
        end
    end
    if bp then bp:Destroy() end
    hrp.CFrame = originalCFrame
end

local function Bypass(targetPlayerName)
    local target = Players:FindFirstChild(targetPlayerName)
    if not target or target == LocalPlayer then return end
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = char:WaitForChild("Humanoid")
    local hrp = char:WaitForChild("HumanoidRootPart")
    local seat = humanoid.SeatPart
    if not seat then return end
    local seatParent = seat.Parent
    local targetChar = target.Character or target.CharacterAdded:Wait()
    local targetHumanoid = targetChar:WaitForChild("Humanoid")
    local targetHRP = targetChar:WaitForChild("HumanoidRootPart")
    local leftDet = seatParent:WaitForChild("LeftDetector")
    local leftWeld = leftDet:WaitForChild("LeftWeld")
    local rightDet = seatParent:WaitForChild("RightDetector")
    local rightWeld = rightDet:WaitForChild("RightWeld")
    local grab = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureGrab")
    local drop = seatParent.BlobmanSeatAndOwnerScript:WaitForChild("CreatureDrop")
    local function grabWithHand(det, weld, targetPart)
        grab:FireServer(det, targetPart, weld)
    end
    local function dropWithHand(weld, targetPart)
        drop:FireServer(weld, targetPart)
    end
    task.wait(0.05)
    while target and target.Parent and targetHumanoid.Health > 0 and Toggles.LoopAppleMethod.Value do
        for i = 1, 20 do
            grabWithHand(leftDet, leftWeld, targetHRP)
        end
        dropWithHand(leftWeld, targetHRP)
        dropWithHand(leftWeld, targetHRP)
        task.wait(0.01)
        if not Toggles.LoopAppleMethod.Value then
            break
        end
    end
end

-- BLOB LOCK FUNCTION
local BlobLock = {
    MyBlob = nil,
    Running = false,
    Time = 0,
    StartPos = nil,
    LastTP = 0
}

local function isnetworkowner(part)
    return part and part:IsDescendantOf(workspace) and part:GetNetworkOwner() == LocalPlayer
end

local function FindTargetByName(partialName)
    partialName = string.lower(partialName)
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            if string.find(string.lower(plr.Name), partialName) or 
               string.find(string.lower(plr.DisplayName), partialName) then
                return plr.Name
            end
        end
    end
    return nil
end

function BlobLock:TPToTargetAndBack(targetHRP)
    local mychar = LocalPlayer.Character
    if not mychar then return end
    local myHRP = FWC(mychar, "HumanoidRootPart", 2)
    if not myHRP then return end
    
    self.StartPos = myHRP.CFrame
    
    myHRP.CFrame = targetHRP.CFrame + Vector3.new(0, 5, 0)
    task.wait(0.05)
    
    for i = 1, 3 do
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(targetHRP, targetHRP.CFrame)
        task.wait()
    end
    
    task.wait(0.1)
    myHRP.CFrame = self.StartPos
    self.LastTP = tick()
end

function BlobLock:Start(targetPlayerName)
    if self.Running then return end
    self.Running = true
    
    if targetPlayerName == "" or targetPlayerName == nil then
        self.Running = false
        return
    end
    
    if not Players:FindFirstChild(targetPlayerName) then
        local found = FindTargetByName(targetPlayerName)
        if found then
            targetPlayerName = found
        else
            self.Running = false
            return
        end
    end
    
    task.spawn(function()
        local targetPlr = Players:FindFirstChild(targetPlayerName)
        if not targetPlr then 
            self:Stop()
            return
        end
        
        local char = targetPlr.Character
        if not char then
            targetPlr.CharacterAdded:Wait()
            task.wait(0.5)
            char = targetPlr.Character
        end
        
        local HRP = char and FWC(char, "HumanoidRootPart", 2)
        if HRP then
            self:TPToTargetAndBack(HRP)
        end
        
        while self.Running do
            task.wait()
            
            local mychar = LocalPlayer.Character
            if not mychar then continue end
            
            local myHRP = FWC(mychar, "HumanoidRootPart", 2)
            local myhum = FWC(mychar, "Humanoid", 2)
            if not myHRP or not myhum then continue end
            
            if not myhum.SeatPart then
                self:Stop()
                break
            end
            
            if myhum.SeatPart then 
                self.MyBlob = myhum.SeatPart.Parent 
            end
            
            targetPlr = Players:FindFirstChild(targetPlayerName)
            if not targetPlr then 
                self:Stop()
                break
            end
            
            char = targetPlr.Character
            if not char then continue end
            
            local hum = FWC(char, "Humanoid", 2)
            local HRP = FWC(char, "HumanoidRootPart", 2)
            if not hum or not HRP then continue end
            
            if hum.Health == 0 then continue end
            
            local dist = (myHRP.Position - HRP.Position).Magnitude
            if dist > 15 and tick() - self.LastTP > 0.5 then
                self:TPToTargetAndBack(HRP)
            end
            
            if self.MyBlob and self.MyBlob.Parent then
                task.defer(function()
                    if isnetworkowner(HRP) then
                        if tick() - self.Time > 0.5 then
                            hum.Sit = true
                            task.wait(0.16)
                            hum.Sit = false
                            self.Time = tick()
                        end  
                        
                        local LD = self.MyBlob:FindFirstChild("LeftDetector")
                        if LD then 
                            HRP.CFrame = LD.CFrame 
                        end
                        
                        for _, v in pairs(char:GetChildren()) do
                            if v:IsA("BasePart") then 
                                v.Velocity = Vector3.new() 
                            end
                        end
                        
                        if dist < 40 and hum.SeatPart then
                            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(HRP, HRP.CFrame)
                        end
                    end
                end)
                
                -- Используем существующую blob_kick или делаем inline
                local blob = self.MyBlob
                local LD = blob:FindFirstChild("LeftDetector")
                if LD then
                    local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
                    local rel = blob.BlobmanSeatAndOwnerScript.CreatureRelease
                    grab:FireServer(LD, HRP, LD.LeftWeld)
                    task.wait(0.005)
                    rel:FireServer(LD.LeftWeld, HRP)
                end
            end
        end
    end)
end

function BlobLock:Stop()
    self.Running = false
    self.MyBlob = nil
end



-- Apply Method Once 
TargetRight:AddButton({
    Text = "Apply Method Once",
    Func = function()
        local targetName = Options.TargetPlayer.Value
        local method = Options.MethodSelect.Value
        if targetName and targetName ~= "" then
            if method == "Bring" then
                Bring(targetName)
            elseif method == "Kick" then
                Kick(targetName)
            elseif method == "Loop Kick" then
                LoopKick(targetName)
            elseif method == "Bypass" then
                Bypass(targetName)
            elseif method == "Loop Kick (Grab+Blob)" then
                loopKickBlobActive = true
                LoopKickBlobFunction(targetName)
            elseif method == "Blob Kill" then
                BlobKill(targetName)
            elseif method == "Lock" then
                BlobLock:Start(targetName)
            end
        end
    end,
    DoubleClick = false
})

-- Destroy Visual
TargetRight:AddButton({
    Text = "Destroy Visual (Try 2 Times)",
    Func = function()
        local targetName = Options.TargetPlayer.Value
        if targetName and targetName ~= "" then
            BlobHeal(targetName)
        end
    end,
    DoubleClick = false
})

-- Loop Apple Method 
TargetRight:AddToggle("LoopAppleMethod", {
    Text = "Loop Apple Method",
    Default = false,
    Callback = function(Value)
        if Value then
            local targetName = Options.TargetPlayer.Value
            local method = Options.MethodSelect.Value
            if targetName and targetName ~= "" then
                task.spawn(function()
                    if method == "Loop Kick (Grab+Blob)" then
                        loopKickBlobActive = true
                        LoopKickBlobFunction(targetName)
                        while Toggles.LoopAppleMethod.Value do
                            task.wait(0.1)
                        end
                        loopKickBlobActive = false
                    elseif method == "Lock" then
                        -- Для Lock запускаем один раз, он сам лупится внутри
                        BlobLock:Start(targetName)
                        while Toggles.LoopAppleMethod.Value and BlobLock.Running do
                            task.wait(0.1)
                        end
                        BlobLock:Stop()
                    else
                        while Toggles.LoopAppleMethod.Value do
                            if method == "Bring" then Bring(targetName)
                            elseif method == "Kick" then Kick(targetName)
                            elseif method == "Loop Kick" then LoopKick(targetName)
                            elseif method == "Bypass" then Bypass(targetName)
                            elseif method == "Blob Kill" then BlobKill(targetName) end
                            task.wait(1)
                        end
                    end
                end)
            else
                Toggles.LoopAppleMethod:SetValue(false)
            end
        else
            loopKickBlobActive = false
            BlobLock:Stop()
        end
    end
})

-- Kick Methods
KickSection:AddToggle("OwnershipKickToggle", {
    Text = "Ownership Kick",
    Default = false,
    Callback = function(Value)
        ownershipKickActive = Value
        local targetName = Options.TargetPlayer.Value
        if Value then
            if targetName and targetName ~= "" then
                ownershipKickTask = task.spawn(function()
                    OwnershipKickFunction(targetName)
                end)
            else
                ownershipKickActive = false
            end
        else
            if ownershipKickTask then
                task.cancel(ownershipKickTask)
                ownershipKickTask = nil
            end
            
            local target = Players:FindFirstChild(Options.TargetPlayer.Value or "")
            if target and target.Character then
                local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
                if tRoot then
                    for _, v in pairs(tRoot:GetChildren()) do
                        if v:IsA("BodyPosition") or v:IsA("BodyGyro") then
                            pcall(function() v:Destroy() end)
                        end
                    end
                    pcall(function()
                        tRoot.AssemblyLinearVelocity = Vector3.zero
                        tRoot.AssemblyAngularVelocity = Vector3.zero
                    end)
                end
            end
        end
    end
})

-- pallet ragdoll
KickSection:AddToggle("OwnershipRagdollToggle", {
    Text = "Pallet Ragdoll",
    Default = false,
    Callback = function(Value)
        ownershipRagdollActive = Value
        local targetName = Options.TargetPlayer.Value
        if Value then
            if targetName and targetName ~= "" then
                ownershipRagdollTask = task.spawn(function()
                    PalletRagdollFunction(targetName)
                end)
            else
                ownershipRagdollActive = false
                Toggles.OwnershipRagdollToggle:SetValue(false)
            end
        else
            if ownershipRagdollTask then
                task.cancel(ownershipRagdollTask)
                ownershipRagdollTask = nil
            end
            -- Принудительная очистка паллеты при выключении
            task.spawn(function()
                local toysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                if toysFolder then
                    local pallet = toysFolder:FindFirstChild("PalletLightBrown")
                    if pallet then
                        pcall(function()
                            ReplicatedStorage.MenuToys.DestroyToy:FireServer(pallet)
                        end)
                        if pallet.Parent then
                            pallet:Destroy()
                        end
                    end
                end
            end)
        end
    end
})

-- XZ 
XZSection:AddToggle("LoopKillToggle", {
    Text = "Loop Kill",
    Default = false,
    Callback = function(Value)
        loopKillActive = Value
        local targetName = Options.TargetPlayer.Value
        if Value then
            if targetName and targetName ~= "" then
                loopKillTask = task.spawn(function()
                    LoopKillFunction(targetName)
                end)
            else
                loopKillActive = false
            end
        else
            if loopKillTask then
                task.cancel(loopKillTask)
                loopKillTask = nil
            end
        end
    end
})

XZSection:AddToggle("SnowballRagdollToggle", {
    Text = "Snowball Ragdoll",
    Default = false,
    Callback = function(Value)
        snowballRagdollActive = Value
        local targetName = Options.TargetPlayer.Value
        if Value then
            if targetName and targetName ~= "" then
                snowballRagdollTask = task.spawn(function()
                    SnowballRagdollFunction(targetName)
                end)
            else
                snowballRagdollActive = false
                Toggles.SnowballRagdollToggle:SetValue(false)
            end
        else
            if snowballRagdollTask then
                task.cancel(snowballRagdollTask)
                snowballRagdollTask = nil
            end
        end
    end
})

-- : Trace Section
TraceSection:AddToggle("TraceToggle", {
    Text = "Trace to Target",
    Default = false,
    Callback = function(Value)
        traceEnabled = Value
        if Value then
            local targetName = Options.TargetPlayer.Value
            if targetName and targetName ~= "" then
                StartTrace(targetName)
            else
                traceEnabled = false
                Toggles.TraceToggle:SetValue(false)
            end
        else
            if traceConnection then
                traceConnection:Disconnect()
                traceConnection = nil
            end
            if traceBeam then
                traceBeam:Destroy()
                traceBeam = nil
            end
        end
    end
})

TraceSection:AddLabel("Trace Color"):AddColorPicker("TraceColorPicker", {
    Default = Color3.fromRGB(255, 0, 0),
    Title = "Trace Color",
    Callback = function(Value)
        traceColor = Value
    end
})

-- VISUAL
local VisualsLeft = Tabs.Visuals:AddLeftGroupbox("Visuals")

local currentFOV = workspace.CurrentCamera.FieldOfView
VisualsLeft:AddSlider("FOVSlider", {
    Text = "Field of View",
    Default = currentFOV,
    Min = 1,
    Max = 120,
    Rounding = 1,
    Callback = function(Value)
        workspace.CurrentCamera.FieldOfView = Value
    end
})

VisualsLeft:AddToggle("Thirdperson", {
    Text = "Third person",
    Default = false,
    Callback = function(Value)
        local plr = game.Players.LocalPlayer
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

-- PCLD
VisualsLeft:AddToggle("PCLDToggle", {
    Text = "PCLD ESP",
    Default = false,
    Callback = function(Value)
        espEnabled = Value
        if Value then
            workspace.DescendantAdded:Connect(function(obj)
                if espEnabled and IsTarget(obj) then
                    AddBoxESP(obj)
                end
            end)
            ScanPCLD()
        else
            RemoveAllBoxes()
        end
    end
}):AddColorPicker("PCLDColor", {
    Default = Color3.fromRGB(255, 255, 255),
    Title = "ESP Color",
    Callback = function(Value)
        espColor = Value
        for obj, box in pairs(espBoxes) do
            if box then
                box.Color3 = espColor
            end
        end
    end
})

VisualsLeft:AddToggle("KickNotifyToggle", {
    Text = "Kick Notify",
    Default = false,
    Callback = function(Value)
        if Value then
            kickNotifyConnection = workspace.ChildAdded:Connect(function(obj)
                local kickObjectNames = {
                    ["blackholekick"] = true,
                    ["blackholekicktweens(old)"] = true,
                    ["blackholekicktweens"] = true,
                    ["jhole"] = true,
                    ["blackhole"] = true,
                    ["black_hole"] = true,
                    ["voidhole"] = true,
                    ["singularity"] = true,
                }
                if not obj.Name or not kickObjectNames[obj.Name:lower()] then return end
                task.wait(0.1)
                local pos
                if obj:IsA("BasePart") then 
                    pos = obj.Position 
                else
                    local part = obj:FindFirstChildWhichIsA("BasePart", true)
                    if part then pos = part.Position end
                end
                if not pos then return end
                local closestPlayer = getClosestPlayer(pos)
                if closestPlayer then
                    local displayName = closestPlayer.DisplayName or "Unknown"
                    local realName = closestPlayer.Name or "Unknown"
                    Library:Notify({
                        Title = "POLAR HUB",
                        Description = displayName .. " (" .. realName .. ") Got Kicked!",
                        Duration = 3
                    })
                else
                    Library:Notify({
                        Title = "POLAR HUB",
                        Description = "Someone Got Kicked!",
                        Duration = 3
                    })
                end
            end)
        else
            if kickNotifyConnection then
                kickNotifyConnection:Disconnect()
                kickNotifyConnection = nil
            end
        end
    end
})

VisualsLeft:AddToggle("PacketLagNotifyToggle", {
    Text = "Packet Lag Notify",
    Default = false,
    Callback = function(Value)
        packetLagNotifyEnabled = Value
        if Value and not lastLagSource then
            StartPacketLagDetector()
        end
    end
})

-- SERVER TAB 
local ServerLeft = Tabs.Server:AddLeftGroupbox("Server Lag")

ServerLeft:AddToggle("ServerLagLineToggle", {
    Text = "Server Lag Line",
    Default = false,
    Callback = function(Value)
        lagLineActive = Value
        local intensity = Options.LagLineIntensity.Value or 150
        if Value then
            lagLineTask = task.spawn(function()
                ServerLagLineFunction(intensity)
            end)
        else
            if lagLineTask then
                task.cancel(lagLineTask)
                lagLineTask = nil
            end
        end
    end
})

ServerLeft:AddSlider("LagLineIntensity", {
    Text = "Lag Line Intensity",
    Default = 150,
    Min = 1,
    Max = 1000,
    Rounding = 0,
    Callback = function(Value)
        if lagLineActive then
            lagLineActive = false
            task.wait(0.1)
            lagLineActive = true
            lagLineTask = task.spawn(function()
                ServerLagLineFunction(Value)
            end)
        end
    end
})

ServerLeft:AddToggle("PacketLagToggle", {
    Text = "Packet Lag",
    Default = false,
    Callback = function(Value)
        packetLagActive = Value
        if Value then
            local strength = Options.PacketLagStrength.Value
            packetLagTask = task.spawn(function()
                local GrabEvents = game.ReplicatedStorage.GrabEvents
                local ExtendGrabLine = GrabEvents.ExtendGrabLine
                while packetLagActive do
                    task.wait(1)
                    ExtendGrabLine:FireServer(string.rep("😂😂😂😂🤣🤣🤣🤣", 100 * strength))
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

ServerLeft:AddSlider("PacketLagStrength", {
    Text = "Packet Lag Strength",
    Default = 6250,
    Min = 0,
    Max = 6250,
    Rounding = 1,
    Callback = function(Value)
    end
})

-- FUN 
local FunLeft = Tabs.Fun:AddLeftGroupbox("Fun Stuff")

--  Jerk Off 
FunLeft:AddToggle("JerkOffToggle", {
    Text = "Jerk Off",
    Default = false,
    Callback = function(Value)
        if Value then
            StartJerkOff()
        else
            StopJerkOff()
        end
    end
})

FunLeft:AddLabel("Jerk Off Key"):AddKeyPicker("JerkOffKeyPicker", {
    Default = "Q",
    Mode = "Press",
    Text = "Jerk Off Key",
    NoUI = false,
    Callback = function(Value)
    end,
    ChangedCallback = function(NewKey)
        jerkOffKey = NewKey
    end
})

-- MISC 
local MiscLeft = Tabs.Miscs:AddLeftGroupbox("Miscellaneous")
local MiscAura = Tabs.Miscs:AddRightGroupbox("Aura")

-- Teleport Bind
local tpKey = Enum.KeyCode.X
MiscLeft:AddLabel("Teleport"):AddKeyPicker("TeleportKey", {
    Default = "X",
    Mode = "Press",
    Text = "Teleport Key",
    NoUI = false,
    Callback = function()
        if tpEnabled then
            local character = LocalPlayer.Character
            local hrp = character and character:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            
            local Mouse = LocalPlayer:GetMouse()
            local targetPos = Mouse.Hit.Position
            hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
        end
    end
})

MiscLeft:AddToggle("TeleportToggle", {
    Text = "Teleport Binder",
    Default = false,
    Callback = function(Value)
        tpEnabled = Value
    end
})

MiscLeft:AddToggle("WaterWalkToggle", {
    Text = "Water Walk",
    Default = false,
    Callback = function(Value)
        setWaterWalk(Value)
    end
})


-- Remove Anti Kick Aura
MiscAura:AddSlider("AuraRadius", {
    Text = "Aura Radius",
    Default = 15,
    Min = 1,
    Max = 20,
    Rounding = 0,
    Callback = function(Value)
        removeAntiKickRadius = Value
    end
})

MiscAura:AddToggle("RemoveAntiKickAuraToggle", {
    Text = "Remove Anti Kick Aura",
    Default = false,
    Callback = function(Value)
        removeAntiKickAuraActive = Value
        if Value then
            task.spawn(RemoveAntiKickAuraFunction)
        else
        end
    end
})

MiscAura:AddToggle("AuraWhitelistToggle", {
    Text = "Whitelist Friends",
    Default = true,
    Callback = function(Value)
        useWhitelistRemoveAntiKick = Value
    end
})

-- CREDITS 
local CreditsLeft = Tabs.Credits:AddLeftGroupbox("Credits")

CreditsLeft:AddLabel("POLAR HUB", true)
CreditsLeft:AddLabel("Creator: Absolute_SEB", true)
CreditsLeft:AddLabel("Discord: absolute_seb", true)
CreditsLeft:AddLabel("")
CreditsLeft:AddLabel("Helper & Tester: dreamqqbb", true)
CreditsLeft:AddLabel("Discord: imdreamqqb", true)

-- UI SETTINGS 
local MenuGroup = Tabs.UISettings:AddLeftGroupbox("Menu")

MenuGroup:AddToggle("KeybindMenuOpen", {
    Default = Library.KeybindFrame.Visible,
    Text = "Open Keybind Menu",
    Callback = function(value)
        Library.KeybindFrame.Visible = value
    end,
})

MenuGroup:AddToggle("ShowCustomCursor", {
    Text = "Custom Cursor",
    Default = true,
    Callback = function(Value)
        Library.ShowCustomCursor = Value
    end,
})

MenuGroup:AddDropdown("NotificationSide", {
    Values = { "Left", "Right" },
    Default = "Right",
    Text = "Notification Side",
    Callback = function(Value)
        Library:SetNotifySide(Value)
    end,
})

MenuGroup:AddDropdown("DPIDropdown", {
    Values = { "50%", "75%", "100%", "125%", "150%", "175%", "200%" },
    Default = "100%",
    Text = "DPI Scale",
    Callback = function(Value)
        Value = Value:gsub("%%", "")
        local DPI = tonumber(Value)
        Library:SetDPIScale(DPI)
    end,
})

MenuGroup:AddDivider()

MenuGroup:AddLabel("Menu bind"):AddKeyPicker("MenuKeybind", { 
    Default = "RightShift", 
    NoUI = true, 
    Text = "Menu keybind" 
})

MenuGroup:AddButton({
    Text = "Unload",
    Func = function()
        Library:Unload()
    end,
    DoubleClick = false
})

Library.ToggleKeybind = Options.MenuKeybind

-- сейвы
ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({ "MenuKeybind" })
ThemeManager:SetFolder("POLAR_HUB")
SaveManager:SetFolder("POLAR_HUB/FlingThings")
SaveManager:SetSubFolder("game-config")

ThemeManager:ApplyToTab(Tabs.UISettings)
SaveManager:BuildConfigSection(Tabs.UISettings)


--  Jerk Off Key handler
UserInputService.InputBegan:Connect(function(input, processed)
    if not processed then
        if input.KeyCode == jerkOffKey then
            if Toggles.JerkOffToggle then
                Toggles.JerkOffToggle:SetValue(not Toggles.JerkOffToggle.Value)
            end
        end
    end
end)

-- Gucci Key handler
UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == gucciKey then
        GucciAntiGrab()
    end
end)

-- Player list updates
Players.PlayerAdded:Connect(function()
    task.wait(1)
    local newList = updatePlayerList()
    Options.TargetPlayer:SetValues(newList)
end)

Players.PlayerRemoving:Connect(function()
    task.wait(0.5)
    local newList = updatePlayerList()
    Options.TargetPlayer:SetValues(newList)
end)

Library:Notify({
    Title = "POLAR HUB",
    Description = "Script loaded successfully",
    Duration = 5
})

SaveManager:LoadAutoloadConfig()
print("POLAR HUB ready")
