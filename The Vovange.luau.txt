loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()

local BetterOrion = loadstring(game:HttpGet("https://raw.githubusercontent.com/m1kp0/BetterOrion/refs/heads/main/Library.lua"))()

local function safeGetCustomAsset(path)
    if getcustomasset then return getcustomasset(path)
    elseif getsynasset then return getsynasset(path)
    elseif syn and syn.get_custom_asset then return syn.get_custom_asset(path)
    else return nil, "getcustomasset не поддерживается" end
end

local function safeMakeFolder(path)
    if isfolder and not isfolder(path) then makefolder(path)
    elseif makefolder then pcall(makefolder, path) end
end

local function notify(title, message, duration)
    BetterOrion:MakeNotification({
        Name = title or "The Vovange",
        Content = message or "",
        Image = "rbxassetid://1920055157",
        Time = duration or 5
    })
end

local Toggles = {}
local Options = {}

local FrameTimer = tick()
local FrameCounter = 0;
local FPS = 60;
local Stats = game:GetService("Stats")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
Workspace.FallenPartsDestroyHeight = "nan"
local targetNotifyConnections = {}

local Window = BetterOrion:MakeWindow({
    Name = "The Vovange",
    SubName = "v2.0 | FTAP", 
    
    IntroEnabled = true, 
    IntroIcon = "hand", 
    IntroText = "The Vovange by MELLSTROYIPDD and yshlepok233 , Welcome!", 
    
    Size = UDim2.new(0, 720, 0, 500),
    MinSize = UDim2.new(0, 500, 0, 200),
    MaxSize = UDim2.new(0, 9999, 0, 9999),

    ShowIcon = true,
    Icon = "hourglass",

    Transparency = 0.45,
    ElementsTransparency = 0.8,
})

local DefTab = Window:MakeTab({ Name = "Defense", Icon = "shield", PremiumOnly = false })
local TargetTab = Window:MakeTab({ Name = "Target", Icon = "crosshair", PremiumOnly = false })
local VisTab = Window:MakeTab({ Name = "Visuals", Icon = "eye", PremiumOnly = false })
local ServerTab = Window:MakeTab({ Name = "Server", Icon = "server", PremiumOnly = false })
local MiscTab = Window:MakeTab({ Name = "Miscs", Icon = "boxes", PremiumOnly = false })
local FunTab = Window:MakeTab({ Name = "Fun", Icon = "star", PremiumOnly = false })
local MenuTab = Window:MakeTab({ Name = "Menu", Icon = "menu", PremiumOnly = false })

_G.ShurikenAntiKick = false
_G.NewPlotAntiKick = false
_G.AutoSitBlobZ = false
_G.AntiInputLag = false
_G.AutoAttacker = false
_G.CounterMode = "Repulsion"

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
local antiOwnershipTask = nil
local antiExplosionConnection = nil
local antiBurnConnection = nil
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
local killDodgeTask = nil
local flyingResetConnection = nil
local antiRagBlobConnections = {}
local jerkOffKey = Enum.KeyCode.Q
local bangKey = Enum.KeyCode.B

local packetLagNotifyEnabled = false
local lastLagSource = false
local packetLagActive = false
local loopKillActive = false
local ownershipKickActive = false
local ownershipKickGrabEnabled = false
local ownershipKickOffsetX = 0
local ownershipKickOffsetY = 0
local ownershipKickOffsetZ = 0
local ownershipRagdollActive = false
local snowballRagdollActive = false
local loopKickBlobActive = false
local loopKickSpinGrabBlobActive = false
local loopKickSpinGrabTask = nil
local lagLineActive = false
local LagServer = false
local LinesCount = 250
local antiOwnershipActive = false
local loopTPActive = false
local antiExplosionActive = false
local antiBurnActive = false
local antiVoidActive = false
local tpEnabled = false
local slideTeleportBusy = false
local antiAntiKickActive = false
local antiAntiLagEnabled = false
local antiLagEnabled = false
local antiLagType = "Default"
local lastAntiLagLaggerNotify = nil
local CreateGrabEvent = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("CreateGrabLine")
local removeAntiKickAuraActive = false
local removeAntiKickRadius = 15
local useWhitelistRemoveAntiKick = true
local destroyGucciActive = false
local playJerkOffActive = false
local traceEnabled = false
local traceColor = Color3.fromRGB(255, 0, 0)
local antiBananaSitActive = false
local killDodgeActive = false
local flyingResetActive = false
local antiRagBlobActive = false
local paintPartsBackup = {}

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

local function IsTarget(obj)
    if not obj:IsA("BasePart") then return false end
    for _, name in ipairs(targetNames) do
        if string.lower(obj.Name) == string.lower(name) then
            return true
        end
    end
    return false
end

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

local function FWC(Parent, Name, Time) 
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3) 
end

local function grab(prt) 
    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(prt, prt.CFrame) 
end

loadstring([[
local Players = game:GetService("Players")
local RS = game:GetService("ReplicatedStorage")
local LP = Players.LocalPlayer
local DestroyGrabLine = RS:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
local SetNetworkOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")

local function lookAt(a, b)
    local d = (b - a).Unit
    local r = d:Cross(Vector3.new(0, 1, 0))
    return CFrame.fromMatrix(a, r, r:Cross(d))
end

local function snowShip(plr, cb)
    local my = LP.Character
    local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
    if not (my and my:FindFirstChild("HumanoidRootPart") and hrp) then return end
    local head = plr.Character:FindFirstChild("Head")
    local po = head and head:FindFirstChild("PartOwner")
    if po and po.Value == LP.Name then
        if cb then cb() end
        return true
    end
    if LP:DistanceFromCharacter(hrp.Position) <= 30 then
        SetNetworkOwner:FireServer(hrp, lookAt(my.HumanoidRootPart.Position, hrp.Position))
    end
end

local function onPartOwner(hitPart)
    if hitPart.Name ~= "PartOwner" or not _G.AutoAttacker then return end
    local other = Players:FindFirstChild(tostring(hitPart.Value))
    if not other or other == LP or not other.Character then return end
    local hum = other.Character:FindFirstChildOfClass("Humanoid")
    local root = other.Character:FindFirstChild("HumanoidRootPart")
    if not hum or not root then return end
    local mode = _G.CounterMode or "Fling"
    local cb
    if mode == "Fling" then
        cb = function()
            local cf = lookAt(LP.Character.HumanoidRootPart.Position, root.Position)
            local bv = Instance.new("BodyVelocity", root)
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.new(cf.LookVector.X, 0.5, cf.LookVector.Z) * 100
            task.wait()
            bv:Destroy()
            DestroyGrabLine:FireServer(root)
        end
    elseif mode == "Ownership" then
        cb = function() hum.WalkSpeed, hum.Sit, hum.JumpPower = 0, false, 0 end
    elseif mode == "Kill" then
        cb = function()
            if not root:FindFirstChild("SkyVelocity") then
                local bv = Instance.new("BodyVelocity", root)
                bv.Name, bv.Velocity, bv.MaxForce = "SkyVelocity", Vector3.new(0, 1e14, 0), Vector3.new(math.huge, math.huge, math.huge)
            end
            for _ = 0, 20 do
                hum.BreakJointsOnDeath = false
                hum:ChangeState(Enum.HumanoidStateType.Dead)
                hum.Jump, hum.Sit = true, true
            end
            task.wait()
            DestroyGrabLine:FireServer(root)
        end
    elseif mode == "Void" then
        cb = function()
            if not root:FindFirstChild("SkyVelocity") then
                local bv = Instance.new("BodyVelocity", root)
                bv.Name, bv.Velocity, bv.MaxForce = "SkyVelocity", Vector3.new(0, 1e14, 0), Vector3.new(math.huge, math.huge, math.huge)
            end
            task.wait(1)
            DestroyGrabLine:FireServer(root)
        end
    end
    if not cb then return end
    for _ = 1, 50 do
        if snowShip(other, cb) then break end
        task.wait()
    end
end

local function hookChar(char) char.DescendantAdded:Connect(onPartOwner) end
LP.CharacterAdded:Connect(hookChar)
if LP.Character then hookChar(LP.Character) end
]])()

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

local function RemoveAntiKickFunction()
    local RS = ReplicatedStorage
    local toyNames = {"NinjaKunai", "NinjaShuriken", "AntiKick"}
    local setOwner = nil

    while antiAntiKickActive do
        if not setOwner or not setOwner.Parent then
            local grabEvents = RS:WaitForChild("GrabEvents", 10)
            setOwner = grabEvents and grabEvents:WaitForChild("SetNetworkOwner", 10)
        end

        if not setOwner then
            task.wait(0.25)
            continue
        end

        local targetName = Options.TargetPlayer and Options.TargetPlayer.Value or ""
        local target = (targetName ~= "") and Players:FindFirstChild(targetName) or nil
        local spawned = target and Workspace:FindFirstChild(target.Name .. "SpawnedInToys")

        if spawned then
            for _, toyName in ipairs(toyNames) do
                local toy = spawned:FindFirstChild(toyName)
                if toy then
                    local part = toy:FindFirstChild("SoundPart")
                    if part and part:IsA("BasePart") then
                        pcall(function()
                            setOwner:FireServer(part, part.CFrame)
                        end)
                        pcall(function()
                            local po = part:FindFirstChild("PartOwner")
                            if not po then
                                return
                            end
                            local isOurs = false
                            if po:IsA("ObjectValue") and po.Value == LocalPlayer then
                                isOurs = true
                            elseif po:IsA("StringValue") and po.Value == LocalPlayer.Name then
                                isOurs = true
                            end
                            if isOurs then
                                part.CFrame = CFrame.new(0, 1000, 0)
                            end
                        end)
                    end
                end
            end
        end

        task.wait(0.1)
    end
end

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

    while antiAntiLagEnabled do
        local targetUser = Options.TargetPlayer and Options.TargetPlayer.Value or ""
        local targetPlr = targetUser ~= "" and Players:FindFirstChild(targetUser) or nil
        local toysFolder = targetPlr and Workspace:FindFirstChild(targetUser .. "SpawnedInToys")

        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")

        if toysFolder and hrp and char then
            local burgers = {}
            for _, v in ipairs(toysFolder:GetDescendants()) do
                if
                    AllowedItems[v.Name]
                    and v:IsA("Model")
                    and v:FindFirstChild("HoldPart")
                then
                    table.insert(burgers, v)
                end
            end

            for _, b in ipairs(burgers) do
                char = LocalPlayer.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                if not hrp or not char then
                    break
                end
                if
                    b
                    and b.Parent
                    and b:IsDescendantOf(toysFolder)
                    and b:FindFirstChild("HoldPart")
                then
                    local hp = b.HoldPart
                    pcall(function()
                        hp.HoldItemRemoteFunction:InvokeServer(b, char)
                    end)
                    task.wait()
                    hrp = LocalPlayer.Character
                        and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if not hrp then
                        break
                    end
                    pcall(function()
                        hp.DropItemRemoteFunction:InvokeServer(
                            b,
                            CFrame.new(hrp.Position + Vector3.new(0, -2000, 0)),
                            Vector3.new(0, 0, 0)
                        )
                    end)
                end
            end
        end

        task.wait(0.05)
    end
end

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

local function LoopKickBlobFunction(targetName)
    local blobLoop = true
    local GE = ReplicatedStorage:WaitForChild("GrabEvents")

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

local function LoopKickSpinGrabFunction(targetName)
    if loopKickSpinGrabTask then
        pcall(task.cancel, loopKickSpinGrabTask)
        loopKickSpinGrabTask = nil
    end

    local blobLoop = true
    local GE = ReplicatedStorage:WaitForChild("GrabEvents")

    local REMOTE_DELAY = 0.002
    local GRAB_PHASE_TIME = 0.35
    local ORBIT_RADIUS = 30
    local ORBIT_ANGULAR_SPEED = 30
    local ORBIT_HEIGHT_OFFSET = 6

    local function BlobSpinGrab()
        local target = Players:FindFirstChild(targetName)
        if not target then return warn("Target not found") end

        local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local hum = char:WaitForChild("Humanoid")
        local seat = hum.SeatPart
        if not seat or seat.Parent.Name ~= "CreatureBlobman" then
            return warn("Sit on Blobman first")
        end

        local dragging = false
        local grabStartTime = 0
        local orbitAngle = 0
        local lastRemote = 0
        
        local savedBlobCF = nil
        local lockedPcldWorldPos = nil

        while blobLoop and loopKickSpinGrabBlobActive do
            local currentTarget = Players:FindFirstChild(targetName)
            if not currentTarget then break end

            char = LocalPlayer.Character
            if not char then
                RunService.Heartbeat:Wait()
                continue
            end
            
            hum = char:FindFirstChild("Humanoid")
            seat = hum and hum.SeatPart
            if not seat or seat.Parent.Name ~= "CreatureBlobman" then
                warn("Stopped: left Blobman")
                break
            end

            local blob = seat.Parent
            local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
            local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
            local CG = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
            local CD = scriptObj and scriptObj:FindFirstChild("CreatureDrop")
            local R_Det = blob:FindFirstChild("RightDetector")

            local tChar = currentTarget.Character
            local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar and tChar:FindFirstChild("Humanoid")

            local remotesOk = scriptObj and CG and CD and R_Det and R_Det:IsDescendantOf(blob)

            if tRoot and tHum and tHum.Health > 0 and blobRoot then
                tRoot.Velocity = Vector3.zero

                if not dragging then
                    if grabStartTime == 0 then
                        grabStartTime = tick()
                        savedBlobCF = blobRoot.CFrame
                    end

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

                    if tick() - grabStartTime > GRAB_PHASE_TIME then
                        dragging = true
                        grabStartTime = 0
                        orbitAngle = 0
                        
                        local pcld = nil
                        for _, v in ipairs(tChar:GetChildren()) do
                            if v:IsA("BasePart") and string.lower(v.Name) == "playercharacterlocationdetector" then
                                pcld = v
                                break
                            end
                        end
                        lockedPcldWorldPos = pcld and pcld.Position or tRoot.Position

                        blobRoot.CFrame = savedBlobCF
                        blobRoot.Velocity = Vector3.zero
                        
                        if remotesOk then
                            pcall(function()
                                local weld = R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld")
                                if weld then
                                    CD:FireServer(weld)
                                    CG:FireServer(R_Det, tRoot, weld)
                                end
                            end)
                        end
                    end
                    RunService.Heartbeat:Wait()
                else
                    local dt = RunService.Heartbeat:Wait()
                    orbitAngle = orbitAngle + ORBIT_ANGULAR_SPEED * dt

                    local basePos = lockedPcldWorldPos or tRoot.Position

                    local rayOrigin = basePos + Vector3.new(0, 5, 0)
                    local rayDir = Vector3.new(0, -50, 0)
                    local rayParams = RaycastParams.new()
                    rayParams.FilterType = Enum.RaycastFilterType.Exclude
                    rayParams.FilterDescendantsInstances = {tChar, char, blob}

                    local groundY = basePos.Y
                    local raycastResult = workspace:Raycast(rayOrigin, rayDir, rayParams)
                    if raycastResult then
                        groundY = raycastResult.Position.Y
                    end

                    local orbitCenter = Vector3.new(basePos.X, groundY + ORBIT_HEIGHT_OFFSET, basePos.Z)
                    
                    local offset = Vector3.new(
                        math.cos(orbitAngle) * ORBIT_RADIUS,
                        0,
                        math.sin(orbitAngle) * ORBIT_RADIUS
                    )
                    local orbitPos = orbitCenter + offset

                    blobRoot.CFrame = CFrame.lookAt(orbitPos, orbitCenter)
                    blobRoot.Velocity = Vector3.zero
                    pcall(function()
                        blobRoot.AssemblyLinearVelocity = Vector3.zero
                        blobRoot.AssemblyAngularVelocity = Vector3.zero
                    end)

                    tHum.PlatformStand = true
                    tHum.Sit = true

                    if tick() - lastRemote >= REMOTE_DELAY then
                        lastRemote = tick()
                        pcall(function()
                            local targetCF = CFrame.new(basePos) * tRoot.CFrame.Rotation
                            GE.SetNetworkOwner:FireServer(tRoot, targetCF)
                            GE.DestroyGrabLine:FireServer(tRoot)
                            if remotesOk then
                                local weld = R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChildWhichIsA("Weld")
                                if weld then
                                    CD:FireServer(weld)
                                    CG:FireServer(R_Det, tRoot, weld)
                                end
                            end
                        end)
                    end
                end
            else
                if dragging or grabStartTime > 0 then
                    dragging = false
                    grabStartTime = 0
                    lockedPcldWorldPos = nil
                    if savedBlobCF and blobRoot then
                        blobRoot.CFrame = savedBlobCF
                        blobRoot.Velocity = Vector3.zero
                    end
                end
                RunService.Heartbeat:Wait()
            end
        end

        if savedBlobCF then
            local pChar = LocalPlayer.Character
            local pHum = pChar and pChar:FindFirstChild("Humanoid")
            local pSeat = pHum and pHum.SeatPart
            if pSeat and pSeat.Parent.Name == "CreatureBlobman" then
                local bRoot = pSeat.Parent:FindFirstChild("HumanoidRootPart") or pSeat.Parent.PrimaryPart
                if bRoot then
                    bRoot.CFrame = savedBlobCF
                    bRoot.Velocity = Vector3.zero
                end
            end
        end
    end

    local th
    th = task.spawn(function()
        local ok, err = pcall(BlobSpinGrab)
        if loopKickSpinGrabTask == th then
            loopKickSpinGrabTask = nil
        end
        if not ok and err then
            warn("[LoopKickSpinGrab] ", err)
        end
    end)
    loopKickSpinGrabTask = th
end

local displayName = LocalPlayer.DisplayName
local username = LocalPlayer.Name
local userId = LocalPlayer.UserId
local thumbType = Enum.ThumbnailType.HeadShot
local thumbSize = Enum.ThumbnailSize.Size420x420
local avatarImage, isReady = Players:GetUserThumbnailAsync(userId, thumbType, thumbSize)

local AntiFuncSection = DefTab:AddSection({ Name = "Anti Functions", Side = "Left"})
local CounterSection = DefTab:AddSection({ Name = "Counter Attack", Side = "Left"})
local GucciDefSection = DefTab:AddSection({ Name = "Gucci Anti-Grab", Side = "Right"})
local ExtraDefSection = DefTab:AddSection({ Name = "Extra Defense", Side = "Right"})
local PlayerDefSection = DefTab:AddSection({ Name = "Player Limits", Side = "Right"})

CounterSection:AddToggle({
    Name = "Auto-Attacker",
    Default = false,
    Callback = function(autoAttackerEnabled)
        _G.AutoAttacker = autoAttackerEnabled
    end,
})

Options.CounterMode = CounterSection:AddDropdown({
    Name = "Counter Mode",
    Default = "Repulsion",
    Options = {
        "Fling",
        "Ownership",
        "Kill",
        "Void"
    },
    Callback = function(counterMode)
        _G.CounterMode = counterMode
    end,
})

AntiFuncSection:AddToggle({
    Name = "Anti-Grab",
    Default = false,
    Callback = function(Value)
        Toggles.AntiGrabToggle = {Value = Value}
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

AntiFuncSection:AddToggle({
    Name = "Anti-Ownership",
    Default = false,
    Callback = function(Value)
        Toggles.AntiOwnership2Toggle = {Value = Value}
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

AntiFuncSection:AddToggle({
    Name = "Anti-Barrier",
    Default = false,
    Callback = function(Value)
        for _, v in pairs(Workspace.Plots:GetChildren()) do
            v.PlotArea.CanQuery = not(Value)
            for _, b in pairs(v.Barrier:GetChildren()) do
                b.CanCollide = not(Value)
                b.CanQuery = not(Value)
                b.CanTouch = not(Value)
            end
        end
    end
})

AntiFuncSection:AddToggle({
    Name = "Anti-Paint",
    Default = false,
    Callback = function(Value)
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

local tpActive = false
AntiFuncSection:AddToggle({
    Name = "Anti-Kick (Shuriken)",
    Default = false,
    Callback = function(Value)
        _G.ShurikenAntiKick = Value
        if Value then
            local function StartAntiKickLoop()
                local plr = LocalPlayer
                local ReplicatedStorage = game:GetService("ReplicatedStorage")
                local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                local stickyEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
                local spawnRemote = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
                local destroyrem = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
                local canSpawn = plr:WaitForChild("CanSpawnToy")

                local function getHRP()
                    local char = plr.Character or plr.CharacterAdded:Wait()
                    return char:WaitForChild("HumanoidRootPart")
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
                        if obj.Name == "Pyramid" or obj.Name == "Main" or obj:IsA("BasePart") then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = obj.Name == "Main" and 0 or 1
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = obj.Name == "Main" and Color3.fromRGB(255,255,255) or Color3.fromRGB(0,0,0)
                            end
                        end
                    end
                end

                local function SpawnToy(name)
                    local t = tick()
                    while not canSpawn.Value and _G.ShurikenAntiKick do
                        if tick() - t > 5 then return nil end
                        task.wait(0.1)
                    end
                    local currentHRP = getHRP()
                    task.spawn(function()
                        pcall(function()
                            spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0,0,0))
                        end)
                    end)
                    local boolik, house = CheckForHome()
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    if boolik and house then 
                        return house:WaitForChild(name, 2)
                    elseif inv then 
                        return inv:WaitForChild(name, 2)
                    end
                    return nil
                end

                while _G.ShurikenAntiKick do 
                    task.wait(0.005)
                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                        task.wait(0.5)
                        continue 
                    end

                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    local kunai = inv and (inv:FindFirstChild("AntiKick") or inv:FindFirstChild("NinjaShuriken"))

                    if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                        local boolik, house = CheckForHome()
                        if boolik and house then
                            local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
                            if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then 
                                kunai = SpawnToy("NinjaShuriken")
                                if kunai then
                                    kunai.Name = "AntiKick"
                                    StickKunai(kunai)
                                end
                            end
                        end
                    end

                    if not kunai then
                        if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then
                            kunai = SpawnToy("NinjaShuriken")
                            if kunai then kunai.Name = "AntiKick" end
                        end
                    end

                    if kunai and kunai:FindFirstChild("StickyPart") then
                        repeat
                            if kunai.StickyPart.CanTouch == true then
                                StickKunai(kunai)
                                kunai.Name = "AntiKick"
                            end
                            task.wait(0.3)
                        until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild("StickyPart") 
                            or kunai.StickyPart.CanTouch == false 
                            or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                    end

                    task.wait(0.1)
                end

                if destroyrem then
                    for _, v in pairs((workspace:FindFirstChild(plr.Name.."SpawnedInToys") or {}):GetChildren()) do
                        if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                            pcall(function() destroyrem:FireServer(v) end)
                        end
                    end
                end
            end

            task.spawn(StartAntiKickLoop)

        else
            local destroyrem = game.ReplicatedStorage:FindFirstChild("MenuToys", true) and game.ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy")
            if destroyrem then
                local inv = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
                if inv then
                    for _, v in pairs(inv:GetChildren()) do
                        if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                            pcall(function() destroyrem:FireServer(v) end)
                        end
                    end
                end
            end
        end
    end
})

AntiFuncSection:AddToggle({
    Name = "Anti-Kick (Shuriken + Plot) BETA",
    Default = false,
    Callback = function(Value)
        _G.NewPlotAntiKick = Value
        local plr = game:GetService("Players").LocalPlayer
        local RS = game:GetService("ReplicatedStorage")
        local Workspace = game:GetService("Workspace")

        if Value then
            task.spawn(function()
                local GE = RS:WaitForChild("GrabEvents", 5) 
                local setOwner = GE and GE:WaitForChild("SetNetworkOwner", 5)
                local PE = RS:WaitForChild("PlayerEvents", 5) 
                local stickyEvent = PE and PE:WaitForChild("StickyPartEvent", 5)
                local MT = RS:WaitForChild("MenuToys", 5) 
                local spawnRemote = MT and MT:WaitForChild("SpawnToyRemoteFunction", 5)
                local canSpawn = plr:WaitForChild("CanSpawnToy", 5)

                if not (setOwner and stickyEvent and spawnRemote and canSpawn) then
                    _G.NewPlotAntiKick = false
                    return
                end

                local function getHRP()
                    local char = plr.Character or plr.CharacterAdded:Wait()
                    return char:WaitForChild("HumanoidRootPart")
                end

                local function isPlotFree(plot)
                    local sign = plot:FindFirstChild("PlotSign")
                    if not sign then return false end
                    local limit = sign:FindFirstChild("ToysLimitNum")
                    if limit and limit:IsA("IntValue") and limit.Value == 0 then
                        return true
                    end
                    local owners = sign:FindFirstChild("ThisPlotsOwners")
                    if owners then
                        for _, desc in ipairs(owners:GetDescendants()) do
                            if desc.Name == "TimeRemainingNum" then
                                local valObj = desc.Parent
                                if valObj and valObj:IsA("StringValue") and valObj.Value ~= "" and valObj.Value ~= plr.Name then
                                    return false 
                                end
                            end
                        end
                    end
                    return true 
                end

                local function checkMyPlot()
                    for i = 1, 5 do
                        local plot = Workspace.Plots:FindFirstChild("Plot"..i)
                        if plot then
                            local sign = plot:FindFirstChild("PlotSign")
                            if sign then
                                for _, desc in ipairs(sign:GetDescendants()) do
                                    if desc.Name == "TimeRemainingNum" then
                                        local valObj = desc.Parent
                                        if valObj and valObj:IsA("StringValue") and valObj.Value == plr.Name then
                                            return plot
                                        end
                                    end
                                end
                            end
                        end
                    end
                    return nil
                end

                local function claimPlot(plot)
                    local sign = plot:FindFirstChild("PlotSign")
                    if not sign then return end
                    local grabPart = nil
                    for _, desc in ipairs(sign:GetDescendants()) do
                        if desc:IsA("BasePart") and desc.Name == "PlusGrabPart" then
                            grabPart = desc
                            break
                        end
                    end
                    if grabPart then
                        local hrp = getHRP()
                        local hum = plr.Character:FindFirstChild("Humanoid")
                        if hum then hum.Sit = false end 
                        
                        hrp.CFrame = grabPart.CFrame * CFrame.new(0, 3, 0)
                        hrp.Velocity = Vector3.zero
                        task.wait(0.2)

                        local claimTimeout = tick()
                        while _G.NewPlotAntiKick and not checkMyPlot() do
                            pcall(function() setOwner:FireServer(grabPart, grabPart.CFrame) end)
                            task.wait(0.1)
                            if tick() - claimTimeout > 5 then break end
                        end
                    end
                end

                local function StickKunai(kunai)
                    if not kunai or not kunai:FindFirstChild("StickyPart") then return end
                    local currentHRP = getHRP()
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai.SoundPart:FindFirstChild("PartOwner") or kunai.SoundPart.PartOwner.Value ~= plr.Name then 
                            pcall(function() setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame) end)
                        end
                    end
                    local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart", 5)
                    if firePart then
                        pcall(function()
                            stickyEvent:FireServer(kunai.StickyPart, firePart, CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90)))
                        end)
                    end
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" or obj.Name == "Main" or obj:IsA("BasePart") then
                            obj.CanTouch = false
                            obj.CanCollide = false
                            obj.CanQuery = false
                            obj.Transparency = obj.Name == "Main" and 0 or 1
                            if not obj:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", obj)
                                high.FillColor = obj.Name == "Main" and Color3.fromRGB(255,255,255) or Color3.fromRGB(0,0,0)
                            end
                        end
                    end
                end

                local function SpawnToy(name, currentPlotName)
                    local t = tick()
                    while not canSpawn.Value and _G.NewPlotAntiKick do
                        if tick() - t > 5 then return nil end
                        task.wait(0.1)
                    end
                    local currentHRP = getHRP()
                    task.spawn(function()
                        pcall(function()
                            spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0,0,0))
                        end)
                    end)
                    
                    local folder = Workspace.PlotItems:FindFirstChild(currentPlotName)
                    if folder then 
                        return folder:WaitForChild(name, 2)
                    end
                    return nil
                end

                local savedPosition = nil

                while _G.NewPlotAntiKick do
                    task.wait(0.05)

                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then continue end
                    local hrp = getHRP()

                    local currentPlot = checkMyPlot()

                    if not currentPlot then
                        if not savedPosition then savedPosition = hrp.CFrame end

                        for i = 1, 5 do
                            local plot = Workspace.Plots:FindFirstChild("Plot"..i)
                            if plot and isPlotFree(plot) then
                                claimPlot(plot)
                                currentPlot = checkMyPlot()
                                if currentPlot then break end
                            end
                        end
                    else
                        local sign = currentPlot:FindFirstChild("PlotSign")
                        local plotItemsFolder = Workspace:FindFirstChild("PlotItems")
                        local playersInPlots = plotItemsFolder and plotItemsFolder:FindFirstChild("PlayersInPlots")
                        
                        local myPlotFolder = plotItemsFolder and plotItemsFolder:FindFirstChild(currentPlot.Name)
                        local kunai = myPlotFolder and (myPlotFolder:FindFirstChild("AntiKick") or myPlotFolder:FindFirstChild("NinjaShuriken"))

                        local imInPlot = playersInPlots and playersInPlots:FindFirstChild(plr.Name)
                        
                        if not imInPlot and sign then
                            if not savedPosition then savedPosition = hrp.CFrame end
                            local hum = plr.Character:FindFirstChild("Humanoid")
                            if hum then hum.Sit = false end
                            
                            hrp.CFrame = sign:GetPivot() * CFrame.new(0, 0, 10)
                            hrp.Velocity = Vector3.zero
                            task.wait(0.5) 
                        elseif imInPlot then
                            if not kunai then
                                kunai = SpawnToy("NinjaShuriken", currentPlot.Name)
                                if kunai then kunai.Name = "AntiKick" end
                            end

                            if kunai and kunai:FindFirstChild("StickyPart") then
                                local attemptStart = tick()
                                repeat
                                    if kunai.StickyPart.CanTouch == true then
                                        StickKunai(kunai)
                                        kunai.Name = "AntiKick"
                                    end
                                    task.wait(0.3)
                                until not kunai or not _G.NewPlotAntiKick or not kunai:FindFirstChild("StickyPart") 
                                    or kunai.StickyPart.CanTouch == false 
                                    or (hrp.Position - kunai.StickyPart.Position).Magnitude >= 20
                                    or tick() - attemptStart > 3

                                if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == false then
                                    if savedPosition then
                                        task.wait(0.2)
                                        hrp.CFrame = savedPosition
                                        hrp.Velocity = Vector3.zero
                                        savedPosition = nil
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        else
            local RS = game:GetService("ReplicatedStorage")
            local destroyRem = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
            if destroyRem then
                local plotItems = Workspace:FindFirstChild("PlotItems")
                if plotItems then
                    for i = 1, 5 do
                        local pFolder = plotItems:FindFirstChild("Plot"..i)
                        if pFolder then
                            for _, item in ipairs(pFolder:GetChildren()) do
                                if item.Name == "AntiKick" or item.Name == "NinjaShuriken" then
                                    pcall(function() destroyRem:FireServer(item) end)
                                end
                            end
                        end
                    end
                end
            end
        end
    end
})

AntiFuncSection:AddToggle({
    Name = "Anti-Burn",
    Default = false,
    Callback = function(Value)
        _G.AntiBurn = Value
        
        if _G.AntiBurn then
            task.spawn(function()
                local RS = game:GetService("ReplicatedStorage")
                local RunService = game:GetService("RunService")
                local spawnRemote = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
                
                local isSpawning = false

                local connection
                connection = RunService.RenderStepped:Connect(function()
                    if not _G.AntiBurn then 
                        connection:Disconnect() 
                        return 
                    end
                    
                    local plr = game.Players.LocalPlayer
                    local char = plr.Character
                    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
                    local root = char.HumanoidRootPart

                    local fire = char:FindFirstChild("FireLight", true) or char:FindFirstChild("Fire", true)
                    
                    local toysFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    local extinguisher = plr.Backpack:FindFirstChild("FireExtinguisher") 
                        or (toysFolder and toysFolder:FindFirstChild("FireExtinguisher"))
                        or char:FindFirstChild("FireExtinguisher")

                    if extinguisher then
                        isSpawning = false
                        local exPart = extinguisher:FindFirstChild("ExtinguishPart")
                        local handle = extinguisher:FindFirstChild("Handle") or extinguisher:FindFirstChildOfClass("Part")
                        
                        if fire then
                            if exPart then
                                exPart.CFrame = root.CFrame
                                exPart.Velocity = Vector3.zero
                            end
                        else
                            if handle then
                                handle.Anchored = true
                                handle.CFrame = root.CFrame * CFrame.new(0, 1000, 0)
                                handle.Velocity = Vector3.zero
                            end
                            if exPart then
                                exPart.CFrame = handle.CFrame
                            end
                        end
                    else
                        if not isSpawning then
                            isSpawning = true
                            pcall(function()
                                local skyPos = root.CFrame * CFrame.new(0, 1000, 0)
                                spawnRemote:InvokeServer("FireExtinguisher", skyPos, Vector3.zero)
                            end)
                            task.delay(1, function() isSpawning = false end)
                        end
                    end
                end)
            end)
        end
    end
	})

local function destroyAllGrabParts()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr.Character and plr.Character:FindFirstChild("GrabParts") then
            plr.Character:FindFirstChild("GrabParts"):Destroy()
        end
    end
end

local function applyAntiLagDefault()
    LocalPlayer:WaitForChild("PlayerScripts"):WaitForChild("CharacterAndBeamMove").Disabled = true
    destroyAllGrabParts()
end

local function startAntiLagAutomatic(lagViewLabel)
    local LinesCount = 0
    local LinesPlayers = {}
    AGConnections["ALEvent"] = CreateGrabEvent.OnClientEvent:Connect(function(plr)
        LinesCount = LinesCount + 1
        if not LinesPlayers[plr] then
            LinesPlayers[plr] = 1
        else
            LinesPlayers[plr] = LinesPlayers[plr] + 1
        end
    end)
    task.defer(function()
        while antiLagType == "Automatic" and task.wait(0.5) do
            if LinesCount > 55 then
                LocalPlayer:WaitForChild("PlayerScripts"):WaitForChild("CharacterAndBeamMove").Disabled = true
                for i, v in LinesPlayers do
                    if v > 55 then
                        for _, plr in pairs(Players:GetPlayers()) do
                            if plr.Name == tostring(i) then
                                lagViewLabel:Set("Lagger : " .. plr.DisplayName .. " (" .. plr.Name .. ")")
                                if lastAntiLagLaggerNotify ~= plr.Name then
                                    lastAntiLagLaggerNotify = plr.Name
                                    notify("Line Lags", plr.DisplayName .. " (" .. plr.Name .. ") spam line lags", 5)
                                end
                                break
                            end
                        end
                        break
                    end
                end
                destroyAllGrabParts()

                local time2 = tick()
                while tick() - time2 < 15 and task.wait(0.2) do
                    if LinesCount > 55 then
                        time2 = tick()
                    end
                end
                lastAntiLagLaggerNotify = nil
            else
                LocalPlayer:WaitForChild("PlayerScripts"):WaitForChild("CharacterAndBeamMove").Disabled = false
            end
        end
    end)

    while antiLagType == "Automatic" do
        local time1 = tick()
        while tick() - time1 < 0.5 do task.wait() end
        LinesCount = 0
        for i, _ in LinesPlayers do
            LinesPlayers[i] = 0
        end
        task.wait()
    end
end

local function enableAntiLag(lagViewLabel)
    if antiLagType == "Default" or antiLagType == nil then
        applyAntiLagDefault()
    elseif antiLagType == "Automatic" then
        startAntiLagAutomatic(lagViewLabel)
    end
end

AntiFuncSection:AddToggle({
    Name = "Anti-Lag",
    Default = false,
    Callback = function(Value)
        antiLagEnabled = Value
        if antiLagEnabled then
            enableAntiLag(antiLagLaggerLabel)
        else
            lastAntiLagLaggerNotify = nil
            LocalPlayer:WaitForChild("PlayerScripts"):WaitForChild("CharacterAndBeamMove").Disabled = false
            Disc("ALEvent")
        end
    end,
})

local autoGucciActiveTractor = false
local antiGucciConnectionTractor
local safePositionTractor
local restoreFramesTractor = 0

local function spawnTractorForGucci()
    local RS = game:GetService("ReplicatedStorage")
    local spawnRemote = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
    if spawnRemote then
        local Player = game.Players.LocalPlayer
        local character = Player.Character or Player.CharacterAdded:Wait()
        local hrp = character:FindFirstChild("HumanoidRootPart")

        local skyPos = hrp.CFrame * CFrame.new(0, 10000, 2) 

        pcall(function()
            spawnRemote:InvokeServer("TractorGreen", skyPos, Vector3.zero)
        end)
    end
end

local function startAntiGucciTractor()
    local Player = game.Players.LocalPlayer
    local character = Player.Character or Player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    
    safePositionTractor = rootPart.Position 

    local folder = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
    local tractor = folder and folder:FindFirstChild("TractorGreen")
    
    if not tractor then spawnTractorForGucci(); task.wait(1); end 
    folder = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
    tractor = folder and folder:FindFirstChild("TractorGreen")

    local seat = tractor and (tractor:FindFirstChildOfClass("VehicleSeat") or tractor:FindFirstChildOfClass("Seat"))
    
    if seat then 
        seat.Anchored = true
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        task.wait(0.1)
        seat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(function() 
        if humanoid.Jump and humanoid.Sit then 
            restoreFramesTractor = 15
            safePositionTractor = rootPart.Position 
        end 
    end)
    
    if antiGucciConnectionTractor then antiGucciConnectionTractor:Disconnect() end
    antiGucciConnectionTractor = game:GetService("RunService").Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFramesTractor > 0 then 
            rootPart.CFrame = CFrame.new(safePositionTractor) 
            restoreFramesTractor = restoreFramesTractor - 1 
        end
    end)
    
    task.spawn(function() 
        while humanoid.Sit do task.wait(0) end 
        task.wait(0.4) 
        rootPart.CFrame = CFrame.new(safePositionTractor) 
    end)
end

local function stopAntiGucciTractor()
    autoGucciActiveTractor = false
    if antiGucciConnectionTractor then antiGucciConnectionTractor:Disconnect() antiGucciConnectionTractor = nil end
    local Player = game.Players.LocalPlayer
    local folder = workspace:FindFirstChild(Player.Name.."SpawnedInToys")
    local tractor = folder and folder:FindFirstChild("TractorGreen")
    if tractor then tractor:Destroy() end
    safePositionTractor = nil
end

GucciDefSection:AddToggle({
    Name = "Gucci (Blobman)",
    Default = false,
    Callback = function(Value)
        _G.GucciActive = Value
        
        if Value then
            local function activateGucci()
                local plr = game:GetService("Players").LocalPlayer
                local HRP = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
                if not HRP then return end
                
                local inv = game:GetService("Workspace"):FindFirstChild(plr.Name.."SpawnedInToys")
                if not inv then return end
                
                local RagdollRemote = game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote
                local SpawnToyRemote = game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction
                
                if not plr.CanSpawnToy.Value then plr.CanSpawnToy.Changed:Wait() end
                
                task.spawn(function() 
                    SpawnToyRemote:InvokeServer("CreatureBlobman", HRP.CFrame * CFrame.new(5, 5, 20), Vector3.new(0,0,0)) 
                end)
                
                task.wait(0.3)
                
                local t
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name == "CreatureBlobman" and v.PrimaryPart and (v.PrimaryPart.Position - HRP.Position).Magnitude < 30 then 
                        t = v 
                    end
                end
                
                if not t then
                    local plot
                    for i = 1, 5 do
                        local p = game:GetService("Workspace").Plots:FindFirstChild("Plot"..i)
                        if p then
                            local val = p.PlotSign.ThisPlotsOwners:FindFirstChild("Value")
                            if val and val.Value:find(plr.Name) then plot = p break end
                        end
                    end
                    if plot then 
                        t = game:GetService("Workspace").PlotItems[plot.Name]:FindFirstChild("CreatureBlobman") or 
                            game:GetService("Workspace").PlotItems[plot.Name]:WaitForChild("CreatureBlobman", 0.5) 
                    end
                end
                
                if not t then return end
                local seat = t:WaitForChild("VehicleSeat", 3)
                if not seat then return end
                
                seat:Sit(plr.Character.Humanoid)
                
                task.spawn(function()
                    local endTime = tick() + 3
                    while tick() < endTime and _G.GucciActive do 
                        RagdollRemote:FireServer(HRP, 0) 
                        task.wait() 
                    end
                end)
                
                local waited = 0
                while seat.Occupant ~= plr.Character.Humanoid and waited < 10 and _G.GucciActive do 
                    task.wait(0.1) 
                    waited = waited + 0.1 
                end
                
                if seat.Occupant == plr.Character.Humanoid then
                    plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                    task.wait(0.2)
                    seat.CFrame = CFrame.new(0, 1e9, 0)
                end
            end
            
            pcall(activateGucci)
        end
    end
})


GucciDefSection:AddToggle({
    Name = "Gucci (Tractor)",
    Default = false,
    Callback = function(Value)
        autoGucciActiveTractor = Value

        if Value then
            startAntiGucciTractor()
            notify("Tractor Active")

            task.spawn(function()
                while autoGucciActiveTractor do
                    task.wait(1.5)
                    local Player = game.Players.LocalPlayer
                    local toysFolder = workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                    local tractor = toysFolder and toysFolder:FindFirstChild("TractorGreen")
                    local hum = Player.Character and Player.Character:FindFirstChild("Humanoid")

                    if autoGucciActiveTractor and not tractor then
                        startAntiGucciTractor()
                    end
                end
            end)
        else
            stopAntiGucciTractor()
        end
    end
})

AntiFuncSection:AddToggle({
    Name = "Anti-Loop",
    Default = false,
    Callback = function(Value)
        killDodgeActive = Value
        if Value then
            KillDodgeFunction()
        end
    end
})

AntiFuncSection:AddToggle({
    Name = "Anti-Ragdoll (Blob)",
    Default = false,
    Callback = function(Value)
        antiRagBlobActive = Value
        AntiRagBlobFunction()
    end
})

AntiFuncSection:AddToggle({
    Name = "Flying-Reset",
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

AntiFuncSection:AddButton({
    Name = "Anti-Banana",
    Default = false,
    Callback = function()
        local Players = game:GetService("Players")
        local Workspace = game:GetService("Workspace")
        local ReplicatedStorage = game:GetService("ReplicatedStorage")
        
        local plr = Players.LocalPlayer
        local char = plr.Character or plr.CharacterAdded:Wait()
        local hum = char:FindFirstChildOfClass("Humanoid")
        local HRP = char:FindFirstChild("HumanoidRootPart")
        local Ragdoll = ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("RagdollRemote")

        local function notify_user(title, message, duration, type)
            if _G.notify and type(_G.notify) == "function" then
                _G.notify(title, message, duration, type)
            end
        end
        
        if not (char and hum and HRP and Ragdoll) then
            notify_user("Error", "no have RagdollRemote", 5, "error")
            return 
        end
        
        local ll = char:FindFirstChild("Left Leg")
        local rl = char:FindFirstChild("Right Leg")
        local torso = char:FindFirstChild("Torso")

        if not (ll and rl and torso) then
             notify_user("Error", "legs already deleted", 5, "warning")
             return
        end
        
        local originalHealth = hum.Health
        hum.Health = hum.MaxHealth 
            
        local originalDestroyHeight = Workspace.FallenPartsDestroyHeight
        local originalPosition = torso.CFrame
        
        Workspace.FallenPartsDestroyHeight = -200000
        
        notify_user("Delete Legs", "Deleting legs...", 3)
        
        Ragdoll:FireServer(HRP, 2)
        task.wait(0.5)
        
        rl.CFrame = CFrame.new(0, -60000, 0)
        ll.CFrame = CFrame.new(0, -60000, 0)
        task.wait(0.3)
        
        torso.CFrame = CFrame.new(0, -59970, 0)
        task.wait(0.5)
        
        torso.CFrame = originalPosition
        task.wait(0.5)
        
        Workspace.FallenPartsDestroyHeight = originalDestroyHeight

        hum.Health = originalHealth
        
        if not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") then
            notify_user("Delete Legs", "Legs successfully deleted!", 5)
            
            task.spawn(function()
                while char.Parent do
                    local current_hum = char:FindFirstChildOfClass("Humanoid")
                    local controls = plr.PlayerGui:FindFirstChild("ControlsGui")
                    
                    if current_hum and controls then
                        local standButton = controls:FindFirstChild("Stand", true)
                        current_hum.HipHeight = (standButton and not standButton.Visible) and 2 or 0
                    end
                    task.wait(0.1) 
                end
            end)
        else
            notify_user("Delete Legs", "Warning: Legs not fully deleted", 3)
        end
    end
})

local resetting = false

ExtraDefSection:AddToggle({
    Name = "Break PCLD (Auto Reset)",
    Default = false,
    Callback = function(Value)
        autoResetEnabled = Value 
        if not Value then return end 

        task.spawn(function()
            local plr = game.Players.LocalPlayer
            
            local function doDoubleReset()
                if resetting then return end
                resetting = true
                
                if plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
                    plr.Character.Humanoid.Health = 0
                end
                
                local char2 = plr.CharacterAdded:Wait()
                local hum2 = char2:WaitForChild("Humanoid", 5)
                task.wait(0.15)
                if hum2 then hum2.Health = 0 end
                
                plr.CharacterAdded:Wait()
                task.wait(0.5)
                resetting = false
            end

            doDoubleReset()

            while autoResetEnabled do
                local char = plr.Character or plr.CharacterAdded:Wait()
                local hum = char:WaitForChild("Humanoid", 10)
                
                if hum then
                    hum.Died:Wait()
                    if autoResetEnabled and not resetting then
                        task.wait(0.5)
                        doDoubleReset()
                    end
                end
                task.wait(0.1)
            end
        end)
    end 
})

PlayerDefSection:AddToggle({
    Name = "Water Walk",
    Default = false,
    Callback = function(Value)
        setWaterWalk(Value)
    end
})

local currentSpeedValue = 50
local isForceSpeedEnabled = false
local forceSpeedConnection

PlayerDefSection:AddSlider({
    Name = "Walkspeed Amount",
    Min = 1,
    Max = 1000,
    Default = 50,
    Increment = 1,
    Callback = function(Value)
        currentSpeedValue = Value
    end
})

local forceSpeedBindLetter = "V"
local forceSpeedToggle = PlayerDefSection:AddToggle({
    Name = "Enable Walkspeed",
    Default = false,
    Binded = true,
    DefaultBind = forceSpeedBindLetter,
    Callback = function(Value)
        isForceSpeedEnabled = Value

        if forceSpeedConnection then 
            forceSpeedConnection:Disconnect() 
            forceSpeedConnection = nil
        end

        if isForceSpeedEnabled then
            forceSpeedConnection = game:GetService("RunService").RenderStepped:Connect(function(dt)
                local char = game.Players.LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                local hum = char and char:FindFirstChildOfClass("Humanoid")

                if hrp and hum and hum.Health > 0 then
                    hum.WalkSpeed = 0 
                    
                    if hum.MoveDirection.Magnitude > 0 then
                        local speedFormula = hum.MoveDirection * (currentSpeedValue * 0.8) * dt
                        hrp.CFrame = hrp.CFrame + speedFormula
                    end
                end
            end)
        else
            local char = game.Players.LocalPlayer.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then 
                hum.WalkSpeed = 16 
            end
        end
    end
})
do
    local ok, keyCode = pcall(function()
        return Enum.KeyCode[forceSpeedBindLetter]
    end)
    if ok and keyCode and forceSpeedToggle.SetBind then
        forceSpeedToggle:SetBind(keyCode)
    end
end

local selectedInputLagToy = "FoodMushroomPoison"

ExtraDefSection:AddDropdown({
    Name = "Select Input Lag Toy",
    Options = {
        "InstrumentDrumBongos", "InstrumentBrassTrumpet", "InstrumentBrassBugle", 
        "InstrumentVoiceMicrophone", "InstrumentWoodwindOcarina", "InstrumentGuitarUkulele", 
        "InstrumentGuitarLyre", "InstrumentGuitarBanjo", "InstrumentGuitarAcoustic", 
        "CupMugBrown", "CupMugWhite", "FoodBread", "FoodBroccoli", "FoodDippyEgg", 
        "FoodDonut", "FoodHotdog", "FoodMeatStick", "FoodPizzaCheese", "FoodPizzaPepperoni",
        "FoodMushroomPoison", "FoodHamburger", "FoodCoconut", "FoodFrenchFries", 
        "FoodMayonnaise", "PoopPileSparkle", "FoodCakePink", "FoodSodaCan", "FoodBanana", "PoopPile"
    },
    Default = "FoodMushroomPoison",
    Callback = function(Value)
        selectedInputLagToy = Value
    end,
})

_G.CycleSpeed = 0.05

ExtraDefSection:AddSlider({
    Name = 'Net-Owner Speed',
    Default = 0.05,
    Min = 0.03,
    Max = 0.3,
    Rounding = 2, 
    Callback = function(Value)
        _G.CycleSpeed = Value 
    end
})

ExtraDefSection:AddToggle({
    Name = "Anti-Net Food",
    Default = false,
    Callback = function(Value)
        _G.InstantLagActive = Value

        if Value then
            task.spawn(function()
                while _G.InstantLagActive do
                    local plr = game.Players.LocalPlayer
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    
                    if hrp then
                        local RS = game:GetService("ReplicatedStorage")
                        local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
                        local toysFolder = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local name = selectedInputLagToy 
                        local item = toysFolder and toysFolder:FindFirstChild(name)

                        if not item or not item.Parent then
                            task.spawn(function()
                                pcall(function()
                                    SpawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, -12, 0), Vector3.new())
                                end)
                            end)
                            task.wait(0.1)
                        else
                            local holdPart = item:FindFirstChild("HoldPart")
                            if holdPart then
                                task.spawn(function()
                                    pcall(function()
                                        holdPart.HoldItemRemoteFunction:InvokeServer(item, char)
                                    end)
                                end)

                                task.wait(0.05)

                                task.spawn(function()
                                    pcall(function()
                                        holdPart.DropItemRemoteFunction:InvokeServer(
                                            item, 
                                            CFrame.new(0, 5000, 0), 
                                            Vector3.new()
                                        )
                                    end)
                                end)
                            end
                        end
                    end
                    task.wait(_G.CycleSpeed or 0.1) 
                end
            end)
        end
    end
})


local function getTargetPlayerOptionsList()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    if #list == 0 then
        table.insert(list, "None")
    end
    return list
end

local TargetSection = TargetTab:AddSection({ Name = "Target Selection", Side = "Left"})
local TargetMethods = TargetTab:AddSection({ Name = "Blobman Methods", Side = "Right"})
local TargetExtra = TargetTab:AddSection({ Name = "Destroying", Side = "Left" })
local XZSection = TargetTab:AddSection({ Name = "Loop Functions", Side = "Left"})
local KickSection = TargetTab:AddSection({ Name = "Ownership Methods", Side = "Right"})

local playerList = getTargetPlayerOptionsList()

local selectedTargetInfoLabel
local selectedMethodInfoLabel
local function updateSelectedTargetInfoLabel()
    local text
    local v = Options.TargetPlayer and Options.TargetPlayer.Value
    if type(v) ~= "string" or v == "" or v == "None" or v == "..." then
        text = "Selected target: None"
    else
        local p = Players:FindFirstChild(v)
        if p and p:IsA("Player") then
            text = string.format("Selected target: %s (%s)", p.DisplayName, p.Name)
        else
            text = "Selected target: None"
        end
    end
    if selectedTargetInfoLabel then
        selectedTargetInfoLabel:Set(text)
    end
end

Options.TargetPlayer = TargetSection:AddDropdown({
    Name = "Target Player Name",
    Default = playerList[1],
    Options = playerList,
    Callback = function()
        updateSelectedTargetInfoLabel()
    end,
})

selectedTargetInfoLabel = TargetSection:AddLabel("Selected target: None")
updateSelectedTargetInfoLabel()

local function refreshTargetPlayersDropdown()
    local newList = getTargetPlayerOptionsList()
    local prev = Options.TargetPlayer.Value
    Options.TargetPlayer:Refresh(newList, true)
    local function setIfChanged(v)
        if Options.TargetPlayer.Value ~= v then
            pcall(function()
                Options.TargetPlayer:Set(v)
            end)
        end
    end
    if type(prev) == "string" and prev ~= "" and table.find(newList, prev) then
        setIfChanged(prev)
    else
        setIfChanged(newList[1])
    end
    updateSelectedTargetInfoLabel()
end

TargetSection:AddBind({
    Name = "Select Target Key",
    Default = Enum.KeyCode.T,
    Callback = function()
        local mouse = LocalPlayer:GetMouse()
        local targetPart = mouse and mouse.Target
        if not targetPart then return end

        local targetModel = targetPart:FindFirstAncestorOfClass("Model")
        if not targetModel then return end

        local hoveredPlayer = Players:GetPlayerFromCharacter(targetModel)
        if hoveredPlayer and hoveredPlayer ~= LocalPlayer then
            Options.TargetPlayer:Set(hoveredPlayer.Name)
        end
    end,
})

TargetExtra:AddToggle({
    Name = "Destroy Gucci (sit)",
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

TargetExtra:AddToggle({
    Name = "Destroy Anti-Net Owner",
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

TargetExtra:AddToggle({
    Name = "Remove Anti-Kick",
    Default = false,
    Callback = function(Value)
        antiAntiKickActive = Value
        if Value then
            if antiAntiKickTask then
                pcall(task.cancel, antiAntiKickTask)
                antiAntiKickTask = nil
            end
            local targetName = Options.TargetPlayer.Value
            if targetName and targetName ~= "" then
                antiAntiKickTask = task.spawn(RemoveAntiKickFunction)
            else
                antiAntiKickActive = false
            end
        else
            if antiAntiKickTask then
                pcall(task.cancel, antiAntiKickTask)
                antiAntiKickTask = nil
            end
        end
    end
})

Options.MethodSelect = TargetMethods:AddDropdown({
    Name = "Method List",
    Options = {"Bring", "Bypass", "Loop Kick (Grab+Blob)", "Loop Kick (Spin grab)", "Blob Kill", "Lock"},
    Default = "Bring",
    Callback = function()
        local m = Options.MethodSelect and Options.MethodSelect.Value
        if selectedMethodInfoLabel and type(m) == "string" then
            selectedMethodInfoLabel:Set("Selected Method: " .. m)
        end
    end,
})

selectedMethodInfoLabel = TargetMethods:AddLabel("Selected Method: " .. (Options.MethodSelect.Value or "Bring"))

TargetMethods:AddToggle({
    Name = "Auto Sit Blobman",
    Default = false,
    Callback = function(Value)
        _G.AutoSitBlobZ = Value
        if Value then
            task.spawn(AutoSitLoop)
        end
    end
})

Toggles.LoopAppleMethod = TargetMethods:AddToggle({
    Name = "Loop Apply Method",
    Default = false,
    Callback = function(Value)
        if Value then
            local targetName = Options.TargetPlayer.Value
            local method = Options.MethodSelect.Value
            if targetName and targetName ~= "" and targetName ~= "None" then
                task.spawn(function()
                    if method == "Loop Kick (Grab+Blob)" then
                        loopKickBlobActive = true
                        LoopKickBlobFunction(targetName)
                        while Toggles.LoopAppleMethod.Value do
                            task.wait(0.1)
                        end
                        loopKickBlobActive = false
                    elseif method == "Loop Kick (Spin grab)" then
                        loopKickSpinGrabBlobActive = true
                        LoopKickSpinGrabFunction(targetName)
                        while Toggles.LoopAppleMethod.Value do
                            task.wait(0.1)
                        end
                        loopKickSpinGrabBlobActive = false
                    elseif method == "Lock" then
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
            loopKickSpinGrabBlobActive = false
            if loopKickSpinGrabTask then
                pcall(task.cancel, loopKickSpinGrabTask)
                loopKickSpinGrabTask = nil
            end
            if BlobLock then
                BlobLock:Stop()
            end
        end
    end,
})

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

local BlobLock = {
    MyBlob = nil,
    Running = false,
    Time = 0,
    StartPos = nil,
    LastTP = 0
}

local function isnetworkowner(part)
    if not part or not part:IsDescendantOf(workspace) or part.Anchored then
        return false
    end
    local ok, receiveAge = pcall(function()
        return part.ReceiveAge
    end)
    if ok and receiveAge == 0 then
        return true
    end
    local okOwner, owner = pcall(function()
        return part:GetNetworkOwner()
    end)
    if okOwner then
        return owner == LocalPlayer
    end
    return false
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

TargetExtra:AddButton({
    Name = "Destroy Visual",
    Func = function()
        local targetName = Options.TargetPlayer.Value
        if targetName and targetName ~= "" then
            BlobHeal(targetName)
        end
    end,
    DoubleClick = false
})

KickSection:AddSlider({
    Name = "Ragdoll Kick X",
    Min = -5,
    Max = 8,
    Default = 0,
    Rounding = 1,
    Callback = function(Value)
        ownershipKickOffsetX = Value
    end
})

KickSection:AddSlider({
    Name = "Ragdoll Kick Y",
    Min = -5,
    Max = 5,
    Default = 0,
    Rounding = 1,
    Callback = function(Value)
        ownershipKickOffsetY = Value
    end
})

KickSection:AddSlider({
    Name = "Ragdoll Kick Z",
    Min = -5,
    Max = 8,
    Default = 0,
    Rounding = 1,
    Callback = function(Value)
        ownershipKickOffsetZ = Value
    end
})

KickSection:AddToggle({
    Name = "Ragdoll Kick",
    Default = false,
    Callback = function(Value)
        local targetName = Options.TargetPlayer.Value

        if not Value then
            ownershipKickActive = false
            if ownershipKickTask then
                task.cancel(ownershipKickTask)
                ownershipKickTask = nil
            end
            return
        end

        if not targetName or targetName == "" then
            ownershipKickActive = false
            print("Error: Select Target First")
            Toggles.OwnershipKickToggle:SetValue(false)
            return
        end

        ownershipKickActive = true

        ownershipKickTask = task.spawn(function()
            local Players = game:GetService("Players")
            local RunService = game:GetService("RunService")
            local RS = game:GetService("ReplicatedStorage")
            local LP = Players.LocalPlayer

            local GE = RS:WaitForChild("GrabEvents")
            local SetNetOwner = GE:WaitForChild("SetNetworkOwner")
            local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine")

            local ZERO_VECTOR = Vector3.new(0, 0, 0)
            local GRAB_TIMEOUT = 0.10
            local MAX_DISTANCE = 25
            local PALLET_COOLDOWN = 0.01
            local HIDDEN_CF = CFrame.new(0, 1e9, 0)

            local target = nil
            local isGrabbing = false
            local startTime = nil
            local lastPalletTime = 0
            local checkStartTime = nil
            local currentTargetRoot = nil
            local attachments = {}

            local function getCurrentOffset()
                return Vector3.new(ownershipKickOffsetX or 0, ownershipKickOffsetY or 0, ownershipKickOffsetZ or 0)
            end

            local function cleanupPhysicsObjects()
                isGrabbing = false
                currentTargetRoot = nil
                for _, v in pairs(attachments) do
                    if v then pcall(function() v:Destroy() end) end
                end
                attachments = {}
            end

            local function isTargetAlive()
                target = Players:FindFirstChild(targetName)
                if not target or not target.Parent then return false end
                
                local char = target.Character
                if not char then return false end
                
                local hum = char:FindFirstChild("Humanoid")
                local root = char:FindFirstChild("HumanoidRootPart")
                
                if not hum or hum.Health <= 0 or not root then return false end
                
                currentTargetRoot = root
                return true
            end

            task.spawn(function()
                while ownershipKickActive do
                    if target and target.Parent and target.Character then
                        local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
                        if tRoot then
                            SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                            DestroyGrabLine:FireServer(tRoot)
                            tRoot.AssemblyLinearVelocity = ZERO_VECTOR
                            tRoot.AssemblyAngularVelocity = ZERO_VECTOR
                        end
                    end
                    task.wait()
                end
            end)

            task.spawn(function()
                while ownershipKickActive do
                    local ClickEvent = RS:FindFirstChild("ClickEvent") or RS:FindFirstChild("ClickRemote")
                    if ClickEvent then
                        if ClickEvent:IsA("RemoteEvent") then 
                            ClickEvent:FireServer() 
                        elseif ClickEvent:IsA("RemoteFunction") then 
                            ClickEvent:InvokeServer() 
                        end
                    end
                    task.wait()
                end
            end)

            local MyToys = workspace:FindFirstChild(LP.Name.."SpawnedInToys")
            local pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
            if not pallet and LP.CanSpawnToy.Value then
                RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", HIDDEN_CF, Vector3.new(0, -90, 0))
                task.wait(0.2)
                MyToys = workspace:FindFirstChild(LP.Name.."SpawnedInToys")
                pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown")
            end
            local soundPart = pallet and pallet:FindFirstChild("SoundPart")

            local myChar = LP.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myRoot then return end
            local savedPos = myRoot.CFrame

            while ownershipKickActive do
                RunService.Stepped:Wait()

                if not isTargetAlive() then
                    cleanupPhysicsObjects()
                    task.wait(0.5)
                    continue
                end

                myChar = LP.Character
                myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                if not myRoot then continue end

                local tChar = target.Character
                local tHum = tChar:FindFirstChild("Humanoid")
                local tRoot = currentTargetRoot

                if not isGrabbing then
                    myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -6, -10)
                    myRoot.Velocity = ZERO_VECTOR
                    tHum.PlatformStand = true
                    tHum.Sit = false

                    if not startTime then startTime = tick() end

                    if tick() - startTime > GRAB_TIMEOUT then
                        isGrabbing = true
                        startTime = nil
                        myRoot.CFrame = savedPos
                        
                        local bp = Instance.new("BodyPosition", tRoot)
                        bp.MaxForce = Vector3.new(2e9, 2e9, 2e9)
                        bp.P = 5000000; bp.D = 2500
                        bp.Position = myRoot.Position + Vector3.new(0, 12.5, 0) + getCurrentOffset()
                        
                        local bg = Instance.new("BodyGyro", tRoot)
                        bg.MaxTorque = Vector3.new(2e9, 2e9, 2e9)
                        bg.P = 5000000; bg.D = 2500
                        bg.CFrame = myRoot.CFrame
                        
                        local force = Instance.new("BodyForce", tRoot)
                        local m = 0
                        for _, v in pairs(tChar:GetChildren()) do
                            if v:IsA("BasePart") then m = m + v:GetMass() end
                        end
                        force.Force = Vector3.new(0, m * workspace.Gravity, 0)

                        attachments.bp = bp
                        attachments.bg = bg
                        attachments.force = force
                        tHum:ChangeState(Enum.HumanoidStateType.Physics)
                    end
                else
                    for _, part in ipairs(tChar:GetDescendants()) do
                        if part:IsA("BasePart") then part.CanCollide = false end
                    end

                    if attachments.bp and myRoot then
                        attachments.bp.Position = myRoot.Position + Vector3.new(0, 12.5, 0) + getCurrentOffset()
                        tRoot.AssemblyLinearVelocity = ZERO_VECTOR
                    end

                    if attachments.bg and myRoot then
                        attachments.bg.CFrame = myRoot.CFrame * CFrame.Angles(math.rad(-120), 0, 0)
                    end

                    if not checkStartTime then checkStartTime = tick() + 0.25 end
                    if checkStartTime and tick() >= checkStartTime then
                        local dist = (myRoot.Position - tRoot.Position).Magnitude
                        if dist > MAX_DISTANCE then
                            cleanupPhysicsObjects()
                            isGrabbing = false
                            startTime = tick()
                            checkStartTime = nil
                            savedPos = myRoot.CFrame
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -8, 0)
                            SetNetOwner:FireServer(tRoot, tRoot.CFrame)
                            task.wait()
                            DestroyGrabLine:FireServer(tRoot)
                            continue
                        end
                    end

                    local currentTime = tick()
                    if currentTime - lastPalletTime >= PALLET_COOLDOWN then
                        lastPalletTime = currentTime
                        if soundPart then
                            soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2.5, 0)
                            SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                            task.wait()
                            soundPart.CFrame = HIDDEN_CF
                        end
                    end
                end
            end

            cleanupPhysicsObjects()
            if myRoot and savedPos then myRoot.CFrame = savedPos end
        end)
    end
})

Toggles.OwnershipKickGrabToggle = KickSection:AddToggle({
    Name = "Spam-Grab Kick",
    Default = false,
    Callback = function(Value)
        ownershipKickGrabEnabled = Value
        if not Value then
            return
        end

        local targetName = Options.TargetPlayer.Value
        if not targetName or targetName == "" then
            ownershipKickGrabEnabled = false
            print("Error: Select Target First")
            Toggles.OwnershipKickGrabToggle:SetValue(false)
            return
        end

        local target = Players:FindFirstChild(targetName)
        if not target then
            ownershipKickGrabEnabled = false
            Toggles.OwnershipKickGrabToggle:SetValue(false)
            return
        end

        task.spawn(function()
            local GE = ReplicatedStorage:WaitForChild("GrabEvents")
            local myChar = LocalPlayer.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myRoot then
                ownershipKickGrabEnabled = false
                Toggles.OwnershipKickGrabToggle:SetValue(false)
                return
            end

            local savedPos = myRoot.CFrame
            local dragging = false
            local grabStartTime = 0

            while ownershipKickGrabEnabled do
                target = Players:FindFirstChild(targetName)
                if not target or not target.Parent then
                    break
                end

                myChar = LocalPlayer.Character
                myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                if not myRoot then
                    break
                end

                local tChar = target.Character
                local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                local tHum = tChar and tChar:FindFirstChild("Humanoid")

                if tRoot and tHum and tHum.Health > 0 then
                    tRoot.AssemblyLinearVelocity = Vector3.zero
                    tRoot.AssemblyAngularVelocity = Vector3.zero
                    tRoot.Velocity = Vector3.zero

                    if not dragging then
                        myRoot.CFrame = tRoot.CFrame
                        myRoot.Velocity = Vector3.zero
                        pcall(function()
                            tHum.PlatformStand = true
                            tHum.Sit = true
                            GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                            GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        end)
                        if grabStartTime == 0 then
                            grabStartTime = tick()
                        end
                        if tick() - grabStartTime > 0.35 then
                            dragging = true
                            grabStartTime = 0
                        end
                    else
                        myRoot.CFrame = savedPos
                        myRoot.Velocity = Vector3.zero
                        local lockPos = savedPos * CFrame.new(0, 17, 0)
                        tRoot.CFrame = lockPos
                        tRoot.Velocity = Vector3.zero
                        tRoot.RotVelocity = Vector3.zero
                        pcall(function()
                            tHum.PlatformStand = true
                            tHum.Sit = false
                            GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                            GE.DestroyGrabLine:FireServer(tRoot)
                            GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        end)
                    end
                else
                    dragging = false
                    grabStartTime = 0
                end

                RunService.Heartbeat:Wait()
            end

            if myRoot then
                myRoot.CFrame = savedPos
                myRoot.Velocity = Vector3.zero
            end
            ownershipKickGrabEnabled = false
            Toggles.OwnershipKickGrabToggle:SetValue(false)
        end)
    end
})

KickSection:AddToggle({ 
    Name = "Loop Bring", 
    Default = false, 
    Callback = function(on) 
        kickLoopEnabled = on 
        local targetName = Options.TargetPlayer.Value
        local target = game:GetService("Players"):FindFirstChild(targetName)

        if on and not target then 
            if Toggles.LoopBringToggle then Toggles.LoopBringToggle:SetValue(false) end 
            return 
        end 

        if not on then 
            kickLoopEnabled = false 
            return 
        end 

        task.spawn(function() 
            local RS = game:GetService("ReplicatedStorage") 
            local RunService = game:GetService("RunService") 
            local Players = game:GetService("Players") 
            local LP = Players.LocalPlayer 
            local GE = RS:WaitForChild("GrabEvents") 
            local SetNetOwner = GE:WaitForChild("SetNetworkOwner") 
            local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine") 
            
            local ZERO_VECTOR = Vector3.new(0, 0, 0) 
            local GRAB_TIMEOUT = 0.10 
            local MAX_DISTANCE = 35 
            local PALLET_COOLDOWN = 0.05 
            local CLEANUP_DELAY = 0.5 
            local HIDDEN_CF = CFrame.new(0, 1e9, 0) 
            
            local myChar = LP.Character 
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart") 
            if not myRoot then return end 
            
            local savedPos = myRoot.CFrame 
            local isGrabbing = false 
            local startTime = nil 
            local lastPalletTime = 0 
            local checkStartTime = nil 
            local grabAttemptStartTime = nil 
            local currentTargetRoot = nil 
            local rotationAngle = 0 
            local rotationSpeed = 3 
            local attachments = {} 

            task.spawn(function() 
                while kickLoopEnabled and target and target.Parent do 
                    local tChar = target.Character 
                    local tRootActual = tChar and tChar:FindFirstChild("HumanoidRootPart") 
                    if tRootActual then 
                        SetNetOwner:FireServer(tRootActual, tRootActual.CFrame) 
                        tRootActual.AssemblyLinearVelocity = ZERO_VECTOR 
                        tRootActual.AssemblyAngularVelocity = ZERO_VECTOR 
                    end 
                    task.wait(0.000001) 
                end 
            end) 

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

            local MyToys = workspace:FindFirstChild(LP.Name.."SpawnedInToys") 
            local pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown") 
            if not pallet and LP.CanSpawnToy.Value then 
                RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("PalletLightBrown", HIDDEN_CF, Vector3.new(0, -90, 0)) 
                task.wait(0.5) 
                MyToys = workspace:FindFirstChild(LP.Name.."SpawnedInToys") 
                pallet = MyToys and MyToys:FindFirstChild("PalletLightBrown") 
            end 
            
            local soundPart = pallet and pallet:FindFirstChild("SoundPart") 
            if not soundPart then kickLoopEnabled = false return end 

            while kickLoopEnabled and target do 
                RunService.Stepped:Wait() 
                if not isTargetAlive() then 
                    cleanupPhysicsObjects() 
                    local waitStart = tick() 
                    while kickLoopEnabled and target and tick() - waitStart < CLEANUP_DELAY do 
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

                myChar = LP.Character 
                myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart") 
                if not myRoot then task.wait() continue end 
                
                local tChar = target.Character 
                local tHum = tChar and tChar:FindFirstChild("Humanoid") 
                local tRoot = currentTargetRoot 
                
                if tRoot then 
                    SetNetOwner:FireServer(tRoot, tRoot.CFrame) 
                    DestroyGrabLine:FireServer(tRoot) 
                    local Limbs = {"Left Leg", "Right Leg", "Left Arm", "Right Arm", "Head", "Torso", "UpperTorso", "LowerTorso"} 
                    for _, limbName in ipairs(Limbs) do 
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
                    tHum.PlatformStand = false 
                    tHum.Sit = true 
                    if not startTime then startTime = tick() end 
                    if tick() - startTime > GRAB_TIMEOUT then 
                        isGrabbing = true 
                        startTime = nil 
                        grabAttemptStartTime = nil 
                        myRoot.CFrame = savedPos 
                        myRoot.Velocity = ZERO_VECTOR 
                        local att0 = Instance.new("Attachment", tRoot) 
                        local att1 = Instance.new("Attachment", myRoot) 
                        att1.CFrame = CFrame.new(0, 12, 0) 
                        local ap = Instance.new("AlignPosition", tRoot) 
                        ap.Attachment0 = att0 ap.Attachment1 = att1 
                        ap.MaxForce = math.huge ap.Responsiveness = 200 
                        local ao = Instance.new("AlignOrientation", tRoot) 
                        ao.Attachment0 = att0 ao.Attachment1 = att1 
                        ao.MaxTorque = math.huge ao.Responsiveness = 200 
                        attachments.att0 = att0 attachments.att1 = att1 
                        attachments.ap = ap attachments.ao = ao 
                        tHum:ChangeState(Enum.HumanoidStateType.Physics) 
                    end 
                else 
                    rotationAngle = (rotationAngle + rotationSpeed * 0.016) % 360 
                    if attachments.att1 then 
                        attachments.att1.CFrame = CFrame.new(0, 10, 0) * CFrame.Angles(math.rad(rotationAngle), 0, 0) 
                    end 
                    if not isTargetAlive() then 
                        cleanupPhysicsObjects() 
                        savedPos = myRoot.CFrame 
                        myRoot.CFrame = savedPos 
                        myRoot.Velocity = ZERO_VECTOR 
                        task.wait(0.05) 
                        continue 
                    end 
                    tRoot = currentTargetRoot 
                    if not checkStartTime then checkStartTime = tick() + 0.25 end 
                    if checkStartTime and tick() >= checkStartTime then 
                        local currentDistance = (myRoot.Position - tRoot.Position).Magnitude 
                        if currentDistance > MAX_DISTANCE then 
                            cleanupPhysicsObjects() 
                            isGrabbing = false 
                            startTime = tick() 
                            grabAttemptStartTime = nil 
                            checkStartTime = nil 
                            savedPos = myRoot.CFrame 
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0, -8, 0) 
                            myRoot.Velocity = ZERO_VECTOR 
                            SetNetOwner:FireServer(tRoot, tRoot.CFrame) 
                            DestroyGrabLine:FireServer(tRoot) 
                            task.wait() 
                            continue 
                        end 
                    end 
                    SetNetOwner:FireServer(tRoot, tRoot.CFrame) 
                    DestroyGrabLine:FireServer(tRoot) 
                    local currentTime = tick() 
                    if currentTime - lastPalletTime >= PALLET_COOLDOWN then 
                        lastPalletTime = currentTime 
                        soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2.5, 0) 
                        SetNetOwner:FireServer(soundPart, soundPart.CFrame) 
                        task.wait() 
                        soundPart.CFrame = HIDDEN_CF 
                    end 
                end 
                task.wait() 
            end 
            cleanupPhysicsObjects() 
            if myRoot and savedPos then myRoot.CFrame = savedPos end 
        end) 
    end
})

XZSection:AddToggle({ 
    Name = "Loop Snowball", 
    Default = false, 
    Callback = function(on) 
        _G.RagdollEnabled = on 
        local Players = game:GetService("Players") 
        local RS = game:GetService("ReplicatedStorage") 
        local Workspace = game:GetService("Workspace") 
        local RunService = game:GetService("RunService") 
        local Player = Players.LocalPlayer 
        local SpawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction") 

        task.spawn(function() 
            while _G.RagdollEnabled do 
                local targetName = Options.TargetPlayer.Value
                local target = Players:FindFirstChild(targetName)
                
                local tChar = target and target.Character 
                local tRoot = tChar and (tChar:FindFirstChild("HumanoidRootPart") or tChar:FindFirstChild("Torso")) 
                
                if tRoot and Player.Character and Player.Character:FindFirstChild("Head") then 
                    task.spawn(function() 
                        pcall(function() 
                            local spawnPos = Player.Character.Head.CFrame * CFrame.new(0, 5, 0) 
                            local SnowModel = SpawnRemote:InvokeServer("BallSnowball", spawnPos, Vector3.new(0, -10, 0)) 
                            local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys") 
                            
                            if folder then 
                                for _, snowball in ipairs(folder:GetChildren()) do 
                                    if snowball.Name == "BallSnowball" then 
                                        local part = snowball:FindFirstChild("SoundPart") or snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart") 
                                        if part then 
                                            firetouchinterest(part, tRoot, 0) 
                                            task.wait() 
                                            firetouchinterest(part, tRoot, 1) 
                                        end 
                                    end 
                                end 
                            end 
                        end) 
                    end) 
                end 
                RunService.Heartbeat:Wait() 
            end 
        end) 
    end 
})

XZSection:AddToggle({ 
    Name = "Loop WD", 
    Default = false, 
    Callback = function(on) 
        sprayActive = on 
        local targetName = Options.TargetPlayer.Value
        local target = game:GetService("Players"):FindFirstChild(targetName)

        if on and not target then 
            if Toggles.WDTrapToggle then Toggles.WDTrapToggle:SetValue(false) end 
            return 
        end 

        if not on then 
            sprayActive = false 
            return 
        end 

        task.spawn(function() 
            local RS = game:GetService("ReplicatedStorage") 
            local LP = game:GetService("Players").LocalPlayer 
            local HIDDEN_CF = CFrame.new(0, 1e9, 0) 
            local SetNetOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner") 
            local sprayName = "SprayCanWD" 

            RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(sprayName, HIDDEN_CF, Vector3.new(0, -90, 0)) 
            task.wait(0.5) 

            local MyToys = workspace:FindFirstChild(LP.Name.."SpawnedInToys") 
            local spray = MyToys and MyToys:FindFirstChild(sprayName) 
            
            if not spray then 
                sprayActive = false 
                return 
            end 

            local remover = spray:WaitForChild("StickyRemoverPart", 5) 
            local primary = spray:FindFirstChild("Handle") or spray.PrimaryPart or spray:FindFirstChildWhichIsA("BasePart") 
            
            primary.Anchored = false 
            primary.CanCollide = false 
            remover.Anchored = false 
            remover.CanCollide = false 

            local bPos = Instance.new("BodyPosition") 
            bPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge) 
            bPos.P = 20000 
            bPos.Parent = primary 

            while sprayActive and spray.Parent and target and target.Parent do 
                local tChar = target.Character 
                local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart") 
                
                if tRoot then 
                    bPos.Position = tRoot.Position + Vector3.new(0, 15, 0) 
                    remover.CFrame = tRoot.CFrame 
                    SetNetOwner:FireServer(primary, primary.CFrame) 
                    primary.AssemblyLinearVelocity = Vector3.new(0,0,0) 
                end 
                task.wait() 
            end 

            if spray.Parent then 
                RS.MenuToys.DestroyToyRemoteEvent:FireServer(spray) 
            end 
        end) 
    end 
})

local VisSection = VisTab:AddSection({ Name = "Visuals", Side = "Left"})
local VisRightSection = VisTab:AddSection({ Name = "Visuals", Side = "Right"})

local currentFOV = workspace.CurrentCamera.FieldOfView
VisSection:AddSlider({
    Name = "Field of View",
    Default = currentFOV,
    Min = 1,
    Max = 120,
    Rounding = 1,
    Callback = function(Value)
        workspace.CurrentCamera.FieldOfView = Value
    end
})

local thirdpersonToggle = VisSection:AddToggle({
    Name = "Third person",
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

do
local espEnabled = false 
local espBoxes = {} 
local targetNames = {"partesp", "playercharacterlocationdetector"} 
local espColor = Color3.fromRGB(30, 30, 30)

local function IsTarget(obj) 
    if not obj:IsA("BasePart") then return false end 
    for _, name in ipairs(targetNames) do 
        if string.lower(obj.Name) == string.lower(name) then return true end 
    end 
    return false 
end 

local function AddBoxESP(obj) 
    if espBoxes[obj] then return end 
    local group = {} 
    
    local box = Instance.new("BoxHandleAdornment") 
    box.Adornee = obj 
    box.AlwaysOnTop = true 
    box.ZIndex = 8 
    box.Color3 = espColor 
    box.Transparency = 0.6 
    box.Size = obj.Size 
    box.Parent = game.CoreGui 
    table.insert(group, box) 
    
    local outline = Instance.new("SelectionBox") 
    outline.Adornee = obj 
    outline.Color3 = Color3.fromRGB(0, 0, 0) 
    outline.LineThickness = 0.09
    outline.SurfaceTransparency = 1 
    outline.Parent = game.CoreGui 
    table.insert(group, outline) 
    
    espBoxes[obj] = group 
    
    obj.AncestryChanged:Connect(function(_, parent) 
        if not parent and espBoxes[obj] then 
            for _, item in ipairs(espBoxes[obj]) do 
                if item then item:Destroy() end 
            end 
            espBoxes[obj] = nil 
        end 
    end) 
end 

local function RemoveAllBoxes() 
    for obj, items in pairs(espBoxes) do 
        for _, item in ipairs(items) do 
            if item then item:Destroy() end 
        end 
    end 
    espBoxes = {} 
end 

local function Scan() 
    for _, obj in ipairs(workspace:GetDescendants()) do 
        if espEnabled and IsTarget(obj) then 
            AddBoxESP(obj) 
        end 
    end 
end 

workspace.DescendantAdded:Connect(function(obj) 
    if espEnabled and IsTarget(obj) then 
        AddBoxESP(obj) 
    end 
end) 

VisRightSection:AddToggle({ 
    Name = "Player-Location (PCLD ESP)", 
    Default = false, 
    Callback = function(Value) 
        espEnabled = Value 
        if espEnabled then 
            Scan() 
        else 
            RemoveAllBoxes() 
        end 
    end
})

VisRightSection:AddColorpicker({
    Name = "PCLD ESP Color",
    Default = Color3.fromRGB(0, 0, 0),
    Callback = function(Value)
        espColor = Value
        for obj, group in pairs(espBoxes) do
            if group and group[1] then
                group[1].Color3 = espColor
            end
        end
    end
})

local espNameEnabled = false
local espNameColor = Color3.fromRGB(255, 255, 255)
local espNames = {}

local function AddNameESP(player)
    if player == LocalPlayer then return end

    local function setupEsp(character)
        if espNames[player.Name] then
            espNames[player.Name]:Destroy()
            espNames[player.Name] = nil
        end

        local head = character:WaitForChild("Head", 5)
        if not head then return end

        local billboard = Instance.new("BillboardGui")
        billboard.Name = player.Name .. "_NameESP"
        billboard.Adornee = head
        billboard.Size = UDim2.new(0, 200, 0, 50)
        billboard.StudsOffset = Vector3.new(0, 2.5, 0)
        billboard.AlwaysOnTop = true

        local textLabel = Instance.new("TextLabel")
        textLabel.Parent = billboard
        textLabel.BackgroundTransparency = 1
        textLabel.Size = UDim2.new(1, 0, 1, 0)
        textLabel.Text = player.Name
        textLabel.TextColor3 = espNameColor
        textLabel.TextStrokeTransparency = 0.2
        textLabel.TextSize = 14
        textLabel.Font = Enum.Font.GothamBold

        local success, _ = pcall(function()
            billboard.Parent = game:GetService("CoreGui")
        end)
        if not success then
            billboard.Parent = LocalPlayer:WaitForChild("PlayerGui")
        end

        espNames[player.Name] = billboard
    end

    if player.Character then
        setupEsp(player.Character)
    end

    player.CharacterAdded:Connect(function(char)
        if espNameEnabled then
            setupEsp(char)
        end
    end)
end

local function ScanPlayers()
    for _, player in pairs(Players:GetPlayers()) do
        AddNameESP(player)
    end
end

local function RemoveAllNames()
    for _, gui in pairs(espNames) do
        if gui then gui:Destroy() end
    end
    table.clear(espNames)
end

Players.PlayerAdded:Connect(function(player)
    if espNameEnabled then
        AddNameESP(player)
    end
end)

Players.PlayerRemoving:Connect(function(player)
    if espNames[player.Name] then
        espNames[player.Name]:Destroy()
        espNames[player.Name] = nil
    end
end)

VisRightSection:AddToggle({
    Name = "Name ESP",
    Default = false,
    Callback = function(Value)
        espNameEnabled = Value
        if Value then
            ScanPlayers()
        else
            RemoveAllNames()
        end
    end
})

VisRightSection:AddColorpicker({
    Name = "Name ESP Color",
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(Value)
        espNameColor = Value
        for _, gui in pairs(espNames) do
            if gui and gui:FindFirstChild("TextLabel") then
                gui.TextLabel.TextColor3 = espNameColor
            end
        end
    end
})
end

local ServerSection = ServerTab:AddSection({ Name = "Server Lags", Side = "Left"})
local ServerRightSection = ServerTab:AddSection({ Name = "Server Packets", Side = "Right"})

ServerSection:AddSlider({
    Name = "Lines Count",
    Default = 250,
    Min = 50,
    Max = 3000,
    Rounding = 0,
    Compact = false,
    Callback = function(Value)
        LinesCount = Value
    end,
})

ServerSection:AddToggle({
    Name = "Line Lags",
    Default = false,
    Callback = function(Value)
        LagServer = Value
        while LagServer do
            for i = 1, LinesCount do
                CreateGrabEvent:FireServer(workspace.SpawnLocation, workspace.SpawnLocation.CFrame)
            end
            task.wait(1)
        end
    end,
})

local PacketSpamAmount = 30000 

ServerRightSection:AddSlider({ 
    Name = "Packet Lag", 
    Default = 30000, 
    Min = 30000, 
    Max = 350000, 
    Rounding = 0, 
    Callback = function(Value) 
        PacketSpamAmount = Value
    end 
})

ServerRightSection:AddToggle({
    Name = "Packet Lag",
    Default = false,
    Callback = function(Value)
        _G.PacketLagActive = Value

        if Value then
            task.spawn(function()
                local event = game:GetService("ReplicatedStorage")
                    :WaitForChild("GrabEvents")
                    :WaitForChild("ExtendGrabLine")

                while _G.PacketLagActive do
                    local payload = string.rep("😂", PacketSpamAmount)
                    
                    event:FireServer(payload)
                    
                    task.wait(0.1)
                end
            end)
        end
    end
})

local FunSection = FunTab:AddSection({ Name = "Fun Stuff" })

FunSection:AddToggle({
    Name = "Jerk Off",
    Default = false,
    Callback = function(Value)
        if Value then
            StartJerkOff()
        else
            StopJerkOff()
        end
    end
})

do
local playBangActive = false
local bangAnimTrack = nil
local bangAnimId = "rbxassetid://148840371" 
local bangSpeed = 10

local function startBang()
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
	bangAnimTrack:AdjustSpeed(bangSpeed)

	task.spawn(function()
		while playBangActive do
			task.wait(0.1)
			if bangAnimTrack and bangAnimTrack.IsPlaying then
				bangAnimTrack.TimePosition = 0.1
			end
		end
	end)
end

local function stopBang()
	if bangAnimTrack then
		bangAnimTrack:Stop()
		bangAnimTrack = nil
	end
end

FunSection:AddToggle({
	Name = "Bang Off",
	Default = false,
	Callback = function(on)
		playBangActive = on
		if on then
			startBang()
		else
			stopBang()
		end
	end
})
end

local MiscSection = MiscTab:AddSection({ Name = "Miscellaneous", Side = "Left"})
local AuraSection = MiscTab:AddSection({ Name = "Aura Settings", Side = "Right"})
local WhiteSection = MiscTab:AddSection({ Name = "Whitelist", Side = "Left"})

MiscSection:AddBind({
    Name = "Slide Teleport",
    Binded = true,
    DefaultBind = "X",
    Callback = function()
        if not tpEnabled or slideTeleportBusy then return end
        local character = LocalPlayer.Character
        local hrp = character and character:FindFirstChild("HumanoidRootPart")
        if not hrp then return end

        local Mouse = LocalPlayer:GetMouse()
        if not (Mouse.Target and Mouse.Target.CanCollide) then return end

        local targetPos = Mouse.Hit.Position
        local endPos = targetPos + Vector3.new(0, 3, 0)
        local startCF = hrp.CFrame
        local startRot = startCF - startCF.Position
        local startPos = startCF.Position
        local dist = (endPos - startPos).Magnitude
        if dist < 0.25 then return end

        slideTeleportBusy = true
        task.spawn(function()
            local duration = math.clamp(0.12 + dist * 0.0022, 0.18, 0.45)
            local arcHeight = math.clamp(dist * 0.22, 3.5, 18)
            local t0 = tick()
            local conn
            conn = RunService.RenderStepped:Connect(function()
                if not hrp.Parent then
                    conn:Disconnect()
                    slideTeleportBusy = false
                    return
                end
                local alpha = math.clamp((tick() - t0) / duration, 0, 1)
                local s = alpha * alpha * (3 - 2 * alpha)
                local basePos = startPos:Lerp(endPos, s)
                local lift = Vector3.new(0, arcHeight * math.sin(s * math.pi), 0)
                hrp.CFrame = CFrame.new(basePos + lift) * startRot
                if alpha >= 1 then
                    conn:Disconnect()
                    hrp.CFrame = CFrame.new(endPos)
                    pcall(function()
                        hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    end)
                    slideTeleportBusy = false
                end
            end)
        end)
    end
})

MiscSection:AddToggle({
    Name = "Enable Slide Teleport",
    Default = false,
    Callback = function(Value)
        tpEnabled = Value
    end
})

AuraSection:AddSlider({
    Name = "Aura Radius",
    Default = 15,
    Min = 1,
    Max = 20,
    Rounding = 0,
    Callback = function(Value)
        removeAntiKickRadius = Value
    end
})

AuraSection:AddToggle({
    Name = "Remove Anti Kick Aura",
    Default = false,
    Callback = function(Value)
        removeAntiKickAuraActive = Value
        if Value then
            task.spawn(RemoveAntiKickAuraFunction)
        else
        end
    end
})

WhiteSection:AddToggle({
    Name = "Whitelist Friends",
    Default = true,
    Callback = function(Value)
        useWhitelistRemoveAntiKick = Value
    end
})

UserInputService.InputBegan:Connect(function(input, processed)
    if not processed then
        if input.KeyCode == bangKey then
            if Toggles.BangToggle then
                Toggles.BangToggle:SetValue(not Toggles.BangToggle.Value)
            end
        end

        if input.KeyCode == jerkOffKey then
            if Toggles.JerkOffToggle then
                Toggles.JerkOffToggle:SetValue(not Toggles.JerkOffToggle.Value)
            end
        end
    end
end)

UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == gucciKey then
        GucciAntiGrab()
    end
end)

Players.PlayerAdded:Connect(function()
    task.wait(1)
    refreshTargetPlayersDropdown()
end)

Players.PlayerRemoving:Connect(function()
    task.wait(0.5)
    refreshTargetPlayersDropdown()
end)

BetterOrion:MakeNotification({
    Name = "The Vovange",
    Content = "The Vovange Loaded Successfully",
    Image = "aperture",
    Time = 5
})

do
local function playKickSound()
    local sound = Instance.new("Sound")
    -- Я добавил ID звука (например, стандартный звук удара/punch)
    sound.SoundId = "rbxassetid://133774990250427" 
    sound.Volume = 1
    sound.Parent = game:GetService("SoundService")
    sound:Play()
    game:GetService("Debris"):AddItem(sound, 2)
end

workspace.ChildAdded:Connect(function(obj)
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
    local currentTarget = Options.TargetPlayer and Options.TargetPlayer.Value or ""

    if closestPlayer and currentTarget ~= "" and closestPlayer.Name == currentTarget then
        playKickSound()
        
        local displayName = closestPlayer.DisplayName or "Unknown"
        local realName = closestPlayer.Name or "Unknown"

        BetterOrion:MakeNotification({
            Name = "The Vovange",
            Content = "Player: " .. displayName .. " (" .. realName .. ") — " .. " has been kicked",
            Image = "alert-circle",
            Time = 5
        })
    end
end)
end

do
    local packetDebounce = {}
    local extendGrabEvents = ReplicatedStorage:WaitForChild("GrabEvents", 10)
    if extendGrabEvents and extendGrabEvents:FindFirstChild("ExtendGrabLine") then
        extendGrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(sender, data)
            if typeof(data) ~= "string" then return end

            local stringLen = #data
            if stringLen <= 300 then return end

            local currentTime = tick()
            local senderName = tostring(sender)

            if (not packetDebounce[senderName] or (currentTime - packetDebounce[senderName]) >= 5) and not lastLagSource then
                lastLagSource = true

                local sizeMB = math.round((stringLen / (1024 * 1024)) * 1000) / 1000
                local displayName = (sender and sender:IsA("Player") and sender.DisplayName) or senderName

                BetterOrion:MakeNotification({
                    Name = "Packet Lag Detected",
                    Content = "Player: " .. displayName .. "\nSize: " .. sizeMB .. " MB (" .. stringLen .. ")",
                    Image = "activity",
                    Time = 5
                })

                packetDebounce[senderName] = currentTime

                task.delay(5, function()
                    lastLagSource = false
                end)
            end
        end)
    end
end

Players.PlayerAdded:Connect(function(player)
    local currentTarget = Options.TargetPlayer and Options.TargetPlayer.Value or ""
    if currentTarget ~= "" and player.Name == currentTarget then
        BetterOrion:MakeNotification({
            Name = "The Vovange",
            Content = player.DisplayName .. " (" .. player.Name .. ") Target Player Joined",
            Image = "thumbs-up",
            Time = 5
        })
    end
end)

Players.PlayerRemoving:Connect(function(player)
    local currentTarget = Options.TargetPlayer and Options.TargetPlayer.Value or ""
    if currentTarget ~= "" and player.Name == currentTarget then
        BetterOrion:MakeNotification({
            Name = "The Vovange",
            Content = player.DisplayName .. " (" .. player.Name .. ") Target Player Leaved",
            Image = "thumbs-down",
            Time = 5
        })
    end
end)

local pendingBHUrl = "74782701834762"

local bhAssetId = pendingBHUrl:match("^rbxassetid://") and pendingBHUrl or "rbxassetid://" .. pendingBHUrl
local originalTextures = {}

local function applyBHSkin(child)
    if not child or not child.Parent then return end
    
    if not originalTextures[child] then
        originalTextures[child] = (child:IsA("Decal") or child:IsA("Texture")) and child.Texture or child.Image
    end

    local root = child.Parent
    if not root:IsA("BasePart") then 
        root = root.Parent 
    end

    if child:IsA("Decal") or child:IsA("Texture") then
        child.Texture = bhAssetId
        child.Color3 = Color3.new(1, 1, 1)
        child.Transparency = 0
    elseif child:IsA("ImageLabel") then
        child.Image = bhAssetId
        child.ImageColor3 = Color3.new(1, 1, 1)
        child.BackgroundTransparency = 1
        child.Size = UDim2.fromScale(0.5, 0.5)
    end

    if root and root:IsA("BasePart") then
        root.Transparency = 1
    end
end

for _, v in ipairs(workspace:GetDescendants()) do
    if v.Name == "Small" or v.Name == "Large" then
        applyBHSkin(v)
    end
end

workspace.DescendantAdded:Connect(function(desc)
    if desc.Name == "Small" or desc.Name == "Large" then
        applyBHSkin(desc)
    end
end)

print("The Vovange loaded")
BetterOrion:Init()
