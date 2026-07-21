--  Gucci Tractor or Blobman It's nice 

OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/BlizTBr/scripts/main/Orion%20X"))()

OrionLib.Themes.PatoSigma = {
    Main     = Color3.fromRGB(15, 10, 25),
    Second   = Color3.fromRGB(30, 20, 45), 
    Stroke   = Color3.fromRGB(200, 120, 255),
    Divider  = Color3.fromRGB(140, 80, 210),
    Text     = Color3.fromRGB(245, 220, 255),
    TextDark = Color3.fromRGB(190, 150, 240)
}

OrionLib.SelectedTheme = "PatoSigma"

Window = OrionLib:MakeWindow({
    Name = "idk Hub",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "idkhubi",
    IntroEnabled = false,
    IntroText = "idk Hub",
    FreeMouse = true
})

getgenv().Players = game:GetService('Players')
getgenv().RunService = game:GetService('RunService')
getgenv().ReplicatedStorage = game:GetService('ReplicatedStorage')
getgenv().Workspace = game:GetService('Workspace')
getgenv().LocalPlayer = getgenv().Players.LocalPlayer

game:GetService("ReplicatedStorage").GrabEvents.EndGrabEarly:Destroy()
s1 = Instance.new("RemoteEvent")
s1.Name = "EndGrabEarly"
s1.Parent = game:GetService("ReplicatedStorage").GrabEvents

InvisTab = Window:MakeTab({
    Name = "Grab",
    Icon = "rbxassetid://4483362458",
    PremiumOnly = false
})

getgenv().UseBlob = false
function rgucci()
game.Players.LocalPlayer.Character:WaitForChild("Humanoid").Sit = true
wait()
args = {
    [1] = game:GetService("Players").LocalPlayer.Character.HumanoidRootPart,
    [2] = math.random(0.00000001, 0.00001)
}

game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(unpack(args))
end

function destroySeatWelds(seat)
    if not seat or not seat.Parent then
        return
    end

    local unwelded = false

    -- Unweld direct children
    for _, child in pairs(seat:GetChildren()) do
        if child:IsA("Weld") or child:IsA("WeldConstraint") then
            if child:IsA("Weld") then
                child.Part0 = nil
                child.Part1 = nil
            elseif child:IsA("WeldConstraint") then
                child.Part0 = nil
                child.Part1 = nil
            end
            unwelded = true
        end
    end

    -- Unweld welds in parent model connected to seat
    for _, desc in pairs(seat.Parent:GetDescendants()) do
        if (desc:IsA("Weld") or desc:IsA("WeldConstraint")) and (desc.Part0 == seat or desc.Part1 == seat) then
            desc.Part0 = nil
            desc.Part1 = nil
            unwelded = true
        end
    end

    if not unwelded then
    end
end

getgenv().getCurrentToyFolder2 = function()
inPlot = game.Players.LocalPlayer.InPlot
    if inPlot and inPlot.Value then
plots = getgenv().Workspace:FindFirstChild("Plots")
        if plots then
            for i = 1, 5 do
p = plots["Plot"..i]
                if p and p:FindFirstChild("PlotSign") then
sign = p.PlotSign
                    for _, name in ipairs({"ThisPlotsOwners","ThisPlotsOwner","ThisPlotOwners"}) do
c = sign:FindFirstChild(name)
                        if c then
owner = (c:IsA("StringValue") and c)
                                          or c:FindFirstChildOfClass("StringValue")
                            if owner and owner.Value == getgenv().LOCAL_PLAYER.Name then
                                return getgenv().Workspace.PlotItems["Plot"..i], p
                            end
                        end
                    end
                end
            end
        end
    end
    return workspace:WaitForChild(game.Players.LocalPlayer.Name .. "SpawnedInToys"), nil
end

getgenv().getCurrentToyFolder = getCurrentToyFolder2

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")

LocalPlayer = Players.LocalPlayer
Character = LocalPlayer and (LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()) or nil
Humanoid = Character and Character:FindFirstChild("Humanoid") or nil
HRP = Character and Character:FindFirstChild("HumanoidRootPart") or nil

tractorSystemEnabled = false
tractorRespawnConnection = nil
tractorMainLoopConnection = nil
tractorDeathConnection = nil
charAddedConnection = nil
tractorWatcherConnection = nil

respawning = false
tractor = nil
seat = nil

ragdollConnection = nil
ragdollWaiting = false

tractorMode = "Desync"

function refreshCharacterRefs()
    if not LocalPlayer then return end

    Character = LocalPlayer.Character
    if not Character then
        tries = 0
        repeat
            Character = LocalPlayer.Character
            tries = tries + 1
            task.wait(0.05)
        until Character or tries >= 40
    end

    if Character then
        pcall(function()
            Humanoid = Character:FindFirstChild("Humanoid") or Character:WaitForChild("Humanoid")
            HRP = Character:FindFirstChild("HumanoidRootPart") or Character:WaitForChild("HumanoidRootPart")
        end)
    else
        Humanoid = nil
        HRP = nil
    end
end

function isValidTractor(t)
    return t and t.Parent and t:IsDescendantOf(workspace) and t:FindFirstChildWhichIsA("VehicleSeat", true)
end

function safeDestroyToyArg(arg)
    mt = ReplicatedStorage:FindFirstChild("MenuToys")
    if not mt or not mt:FindFirstChild("DestroyToy") then
        if typeof(arg) == "Instance" then
            pcall(function() arg:Destroy() end)
        end
        return
    end

    pcall(function()
        if typeof(arg) == "Instance" and arg.Name then
            mt.DestroyToy:FireServer(arg)
        else
            mt.DestroyToy:FireServer(arg)
        end
    end)
end

function getTractorAndSeat()
    folder = nil
    if type(getCurrentToyFolder2) == "function" then
        pcall(function()
            folder = getCurrentToyFolder2()
        end)
    end

    function scanParent(parent)
        if not parent then return nil, nil end
        if getgenv().UseBlob then
            for _, child in ipairs(parent:GetChildren()) do
                if child:IsA("Model") and child.Name == "CreatureBlobman" then
                    s = child:FindFirstChildWhichIsA("VehicleSeat", true) or child:FindFirstChild("VehicleSeat", true)
                    if s then
                        return child, s
                    end
                end
            end
        else
            for _, child in ipairs(parent:GetChildren()) do
                if child:IsA("Model") and child.Name == "TractorGreen" then
                    s = child:FindFirstChildWhichIsA("VehicleSeat", true) or child:FindFirstChild("VehicleSeat", true)
                    if s then
                        return child, s
                    end
                end
            end
        end

        return nil, nil
    end

    if folder then
        t, s = scanParent(folder)
        if t and s then
            return t, s
        end
    end

    t2, s2 = scanParent(workspace)
    if t2 and s2 then
        return t2, s2
    end

    return nil, nil
end

function spawnTractor()
    localHRP = (LocalPlayer and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")) or HRP

    if localHRP and localHRP.CFrame then
        baseCFrame = CFrame.new(0,10000000000,0) * CFrame.Angles(-2.5694754123687744, 0.10936412960290909, 3.0714223384857178)
    else
        baseCFrame = CFrame.new(40.31, -6.02, 73.32) * CFrame.Angles(0.5082, -0.7958, 0.3788)
    end

    mt = ReplicatedStorage:FindFirstChild("MenuToys")
    if not mt then return end

    foundTr, foundSeat = nil, nil

    repeat
        wait(0.09)
        if not getgenv().UseBlob then
            spawn(function()
                pcall(function()
                    if mt:FindFirstChild("SpawnToyRemoteFunction") then
                        mt.SpawnToyRemoteFunction:InvokeServer(
                            "TractorGreen",
                            baseCFrame,
                            Vector3.new(0, -49.45, 0)
                        )
                    elseif mt:FindFirstChild("SpawnToy") then
                        mt.SpawnToy(
                            "TractorGreen",
                            baseCFrame,
                            Vector3.new(0, -49.45, 0)
                        )
                    end
                end)
            end)
        else
            spawn(function()
                mt.SpawnToyRemoteFunction:InvokeServer(
                    "CreatureBlobman",
                    baseCFrame,
                    Vector3.new(0, -49.45, 0)
                )
            end)
        end

        task.wait(0.2)

        foundTr, foundSeat = getTractorAndSeat()

    until foundTr and foundSeat

    return foundTr, foundSeat
end

function anchorPulseForModel(model)
    if not model then return end
    task.spawn(function()
        descendants = model:GetDescendants()
        for i = 1, #descendants do
            part = descendants[i]
            if part and part:IsA("BasePart") then
                success = false
                repeat
                    success = pcall(function()
                        part.Anchored = true
                        part.CanCollide = false
                        part.CanTouch = false
                        RunService.Heartbeat:Wait()
                        part.Anchored = false
                    end)
                    if not success then
                        wait(0.5)
                    end
                until success
            end
        end
    end)
end

function startLoopRG(targetHRP)
    RunService = game:GetService("RunService")
    ReplicatedStorage = game:GetService("ReplicatedStorage")

    if not targetHRP or not targetHRP.Parent then
        return
    end

    startTime = tick()
    conn = nil
    conn = RunService.Heartbeat:Connect(function()
        if not targetHRP.Parent then
            conn:Disconnect()
            return
        end

        if tick() - startTime >= 3 then
            conn:Disconnect()
            return
        end

        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(targetHRP, -math.huge)
    end)

    return conn
end

function stopLoopRG(loopConnection)
    if loopConnection and loopConnection.Connected then
        loopConnection:Disconnect()
    else
    end
end

function gucci(seatObj)
    if not seatObj or not seatObj.Parent then return end
    spawn(function() _G.loop = startLoopRG(HRP) end)
    seatObj.ProximityPrompt.Enabled = false
    rgucci()
    wait(0.09)
    startTime = tick(); repeat task.wait() if tick() - startTime > 0.5 then break end until not Humanoid.Ragdolled.Value
    pcall(function()
        possibleh = seatObj.Parent.HumanoidRootPart
    end)
    pcall(function()
        seatObj.WeldConstraint.Part1 = nil
    end)
    destroySeatWelds(seatObj)
    wait()

    spawn(function() anchorPulseForModel(seatObj.Parent) end)

    oldCF3 = HRP and HRP.CFrame or nil
    promptFired = false

    task.wait(0.09)

    for i = 1, 40 do
        ok = false
        repeat
            ok = pcall(function()
                if not seatObj or not seatObj.Parent then return end
                if not seatObj:IsDescendantOf(workspace) then return end
                if not Humanoid or not Humanoid.Parent then return end

                spawn(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge) end)

                RunService.Heartbeat:Wait()

                spawn(function() ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge) end)

                if not promptFired then
                    pcall(function()
                        RunService.Heartbeat:Wait()

                        attempts = 0
                        maxAttempts = 100

                        repeat
                            if seatObj and seatObj.Parent then
                                game:GetService("RunService").Heartbeat:Wait()
                                spawn(function() anchorPulseForModel(seatObj.Parent) end)
                                seatObj:Sit(Humanoid)
                                spawn(function() anchorPulseForModel(seatObj.Parent) end)
                                attempts = attempts + 1
                                RunService.Heartbeat:Wait()
                            else
                                break
                            end
                        until (seatObj and seatObj.Occupant == Humanoid) or attempts >= maxAttempts
                    end)

                    promptFired = true
                end

                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)
            end)

            if not ok then
                RunService.Heartbeat:Wait()
            end
        until ok
    end

    stopLoopRG(_G.loop)

    if oldCF3 and HRP and HRP.Parent then
        pcall(function()
        end)
    end

    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)

    pcall(function()
        if not seatObj or not seatObj.Parent then return end
        if not Humanoid or not Humanoid.Parent then return end

        if not seatObj:FindFirstChildOfClass("BodyVelocity") then
            pcall(function()
                seatObj.WeldConstraint.Part1 = possibleh
            end)
            bv = Instance.new("BodyVelocity")
            bv.Velocity = Vector3.new(0, 99999, 0)
            bv.MaxForce = Vector3.new(0, 9999999, 0)
            bv.P = 1500
            bv.Parent = seatObj.Parent.Hitbox
        end

        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, 0.9)
    end)
end

function desyncG(seatObj)
    if not seatObj or not seatObj.Parent then return end

    anchorPulseForModel(seatObj.Parent)

    oldCF3 = HRP and HRP.CFrame or nil

    spawn(function()
        for i = 1, 50 do wait(0.01) pcall(function()
            if ReplicatedStorage.CharacterEvents and ReplicatedStorage.CharacterEvents.RagdollRemote then
                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)
            end
        end) end
    end)

    wait(0.09)

    if HRP and HRP.Parent then
        HRP.CFrame = seatObj.CFrame
    end

    for i = 1, 40 do
        ok = false
        repeat
            ok = pcall(function()
                if not seatObj or not seatObj.Parent then return end
                if not seatObj:IsDescendantOf(workspace) then return end
                if not Humanoid or not Humanoid.Parent then return end

                if ReplicatedStorage.CharacterEvents and ReplicatedStorage.CharacterEvents.RagdollRemote then
                    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)
                end

                game:GetService("RunService").Heartbeat:Wait()

                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)

                prompt = seatObj:FindFirstChild("ProximityPrompt")
                pcall(function() fireproximityprompt(prompt) end)

                ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)
            end)

            if not ok then
                game:GetService("RunService").Heartbeat:Wait()
            end

        until ok
    end

    if oldCF3 and HRP and HRP.Parent then
        pcall(function()
            HRP.CFrame = oldCF3
        end)
    end

    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)

    pcall(function()
        task.wait(1.5)

        if not seatObj or not seatObj.Parent then return end
        if not Humanoid or not Humanoid.Parent then return end

        if Humanoid.SeatPart == seatObj then
            Humanoid.Jump = true
            game:GetService("RunService").Heartbeat:Wait()
            gucci(seatObj)
            return
        end

        bv = Instance.new("BodyVelocity")
        bv.Velocity = Vector3.new(0, 99999, 0)
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.P = 15000
        bv.Parent = seatObj
    end)
end

function performSeatMethod(seatObj)
    if not seatObj then return end
    pcall(function()
        if tractorMode == "Desync" or tractorMode == "desync" then
            gucci(seatObj)
        else
            gucci(seatObj)
        end
    end)
end

function setupRespawnListener(currentTractor)
    if tractorRespawnConnection then
        tractorRespawnConnection:Disconnect()
        tractorRespawnConnection = nil
    end

    if not currentTractor then
        pcall(function() stopSeatWatcher() end)
        return
    end

    pcall(function()
        seat = currentTractor:FindFirstChildWhichIsA("VehicleSeat", true) or currentTractor:FindFirstChild("VehicleSeat", true)
        pcall(function() startSeatWatcher() end)
    end)

    tractorRespawnConnection = currentTractor.AncestryChanged:Connect(function(_, parent)
        if not tractorSystemEnabled then return end
        if parent then return end
        if respawning then return end

        respawning = true
        task.wait(0.15)

        newTr, newSeat = spawnTractor()

        tries = 0
        repeat
            if not newTr or not newSeat or not newTr.Parent then
                newTr, newSeat = getTractorAndSeat()
            end
            tries = tries + 1
            task.wait(0.08)
        until (newTr and newSeat) or tries >= 30 or not tractorSystemEnabled

        if tractorSystemEnabled and newTr and newSeat and isValidTractor(newTr) then
            tractor = newTr
            seat = newSeat
            setupRespawnListener(tractor)
            pcall(function() performSeatMethod(seat) end)
        end

        respawning = false
    end)
end

function connectDeathHandler()
    if tractorDeathConnection then
        tractorDeathConnection:Disconnect()
        tractorDeathConnection = nil
    end

    if not Humanoid then return end

    tractorDeathConnection = Humanoid.Died:Connect(function()
        if not tractorSystemEnabled then return end

        oldTractor = tractor
        oldSeat = seat
        tractor = nil
        seat = nil

        respawning = true

        LocalPlayer.CharacterAdded:Wait()
        task.wait(0.5)

        Character = LocalPlayer.Character
        if Character then
            Humanoid = Character:WaitForChild("Humanoid")
            HRP = Character:WaitForChild("HumanoidRootPart")
        end

        if oldTractor and oldTractor.Parent then
            safeDestroyToyArg(oldTractor)
            pcall(function() if oldTractor.Parent then oldTractor:Destroy() end end)
        end

        newTr, newS = spawnTractor()
        tries = 0
        repeat
            if not newTr or not newS then
                newTr, newS = getTractorAndSeat()
            end
            tries = tries + 1
            task.wait(0.15)
        until ((newTr and newS and newTr ~= oldTractor) or tries >= 30 or not tractorSystemEnabled)

        if tractorSystemEnabled and isValidTractor(newTr) and newTr ~= oldTractor and newS and newS.Parent then
            tractor = newTr
            seat = newS
            respawning = false
            pcall(function() performSeatMethod(seat) end)
            setupRespawnListener(tractor)
        else
            tractor = nil
            seat = nil
            respawning = false
        end

        connectDeathHandler()
    end)
end

enabled = true
ragdollConnection = nil
ragdollWaiting = false

function readRagdollValue()
    if not enabled then return nil, nil end
    if not Humanoid then return nil, nil end

    bv = Humanoid:FindFirstChild("Ragdolled")
    if bv and bv:IsA("BoolValue") then
        return "bool", bv
    end

    if Humanoid.GetAttribute and Humanoid:GetAttribute("Ragdolled") ~= nil then
        return "attr", nil
    end

    return nil, nil
end

function handleBoolRagdollCycle(boolInstance)
    if not enabled then return end
    if ragdollWaiting then return end

    ragdollWaiting = true

    repeat
        task.wait(0.05)
        if not enabled then
            ragdollWaiting = false
            return
        end
    until not boolInstance.Value

    ragdollWaiting = false
    if not enabled then return end

    ok, tractorObj, seatObj = pcall(function()
        return getTractorAndSeat()
    end)

    if ok and seatObj then
        pcall(function()
            safeDestroyToyArg(tractorObj)
        end)
    end
end

function handleAttrRagdollCycle()
    if not enabled then return end
    if ragdollWaiting then return end

    ragdollWaiting = true

    repeat
        task.wait(0.05)
        if not enabled then
            ragdollWaiting = false
            return
        end
    until not Humanoid:GetAttribute("Ragdolled")

    ragdollWaiting = false
    if not enabled then return end

    ok, tractorObj, seatObj = pcall(function()
        return getTractorAndSeat()
    end)

    if ok and seatObj then
        pcall(function()
            safeDestroyToyArg(tractorObj)
        end)
    end
end

function startRagdollWatcher()
    if not enabled then return end

    if ragdollConnection then
        pcall(function()
            ragdollConnection:Disconnect()
        end)
        ragdollConnection = nil
    end

    ragdollWaiting = false

    if not Humanoid then return end

    kind, ref = readRagdollValue()

    if kind == "bool" and ref then
        ragdollConnection = ref.Changed:Connect(function(v)
            if not enabled then return end
            if v == true then
                task.spawn(function()
                    handleBoolRagdollCycle(ref)
                end)
            end
        end)

        if ref.Value == true then
            task.spawn(function()
                handleBoolRagdollCycle(ref)
            end)
        end

    elseif kind == "attr" and Humanoid.GetAttributeChangedSignal then
        ragdollConnection = Humanoid:GetAttributeChangedSignal("Ragdolled"):Connect(function()
            if not enabled then return end

            if Humanoid:GetAttribute("Ragdolled") == true then
                task.spawn(function()
                    handleAttrRagdollCycle()
                end)
            end
        end)

        if Humanoid:GetAttribute("Ragdolled") == true then
            task.spawn(function()
                handleAttrRagdollCycle()
            end)
        end

    else
        ragdollConnection = Humanoid.DescendantAdded:Connect(function(child)
            if not enabled then return end

            if child.Name == "Ragdolled" and child:IsA("BoolValue") then
                pcall(function()
                    ragdollConnection:Disconnect()
                end)

                startRagdollWatcher()
            end
        end)

        if Humanoid.GetAttributeChangedSignal then
            ok, attrConn = pcall(function()
                return Humanoid:GetAttributeChangedSignal("Ragdolled")
            end)

            if ok and attrConn then
                tmpConn = attrConn:Connect(function()
                    if not enabled then return end

                    if Humanoid:GetAttribute("Ragdolled") ~= nil then
                        pcall(function()
                            if ragdollConnection then
                                ragdollConnection:Disconnect()
                            end
                        end)

                        startRagdollWatcher()
                    end
                end)

                ragdollConnection = tmpConn
            end
        end
    end
end

function stopRagdollWatcher()
    if ragdollConnection then
        pcall(function()
            ragdollConnection:Disconnect()
        end)
        ragdollConnection = nil
    end

    ragdollWaiting = false
end

seatWatcherConnection = nil
seatWatcherOccupiedBy = nil

function tryOrionNotify(text)
    pcall(function()
        if OrionLib ~= nil and type(OrionLib.MakeNotification) == "function" then
            OrionLib:MakeNotification({Name = "Anti Gucci", Content = text, Time = 5})
            return
        end
        if Orion ~= nil and type(Orion.MakeNotification) == "function" then
            Orion.MakeNotification({Name = "Anti Gucci", Content = text, Time = 5})
            return
        end
        if orion ~= nil and type(orion.MakeNotification) == "function" then
            orion.MakeNotification({Name = "Anti Gucci", Content = text, Time = 5})
            return
        end
        if game:GetService("StarterGui") and type(game:GetService("StarterGui").SetCore) == "function" then
            pcall(function()
                game:GetService("StarterGui"):SetCore("SendNotification", {
                    Title = "Anti Gucci",
                    Text = text,
                    Duration = 5
                })
            end)
            return
        end
    end)
end

function stopSeatWatcher()
    if seatWatcherConnection then
        pcall(function() seatWatcherConnection:Disconnect() end)
        seatWatcherConnection = nil
    end
    seatWatcherOccupiedBy = nil
end

function startSeatWatcher()
    if seatWatcherConnection then
        pcall(function() seatWatcherConnection:Disconnect() end)
        seatWatcherConnection = nil
    end
    seatWatcherOccupiedBy = nil

    if not seat or not seat.Parent then return end

    function handleOccupantChange()
        if not tractorSystemEnabled then return end
        if not seat or not seat.Parent then
            stopSeatWatcher()
            return
        end

        occupant = nil
        pcall(function() occupant = seat.Occupant end)

        if occupant and occupant.Parent then
            if Character and occupant.Parent == Character then
                return
            end

            seatWatcherOccupiedBy = occupant
            pcall(function()
                OrionLib:MakeNotification({
                    Title = "Notification",
                    Content = "possible remove gucci detected",
                    Time = 3
                })
            end)

            task.spawn(function()
                repeat
                    task.wait(0.1)
                    if not tractorSystemEnabled then
                        seatWatcherOccupiedBy = nil
                        return
                    end
                    if not seat or not seat.Parent then
                        seatWatcherOccupiedBy = nil
                        return
                    end
                    currentOcc = nil
                    pcall(function() currentOcc = seat.Occupant end)
                    if not currentOcc then break end
                    if seatWatcherOccupiedBy ~= currentOcc then break end
                until false

                if not tractorSystemEnabled then
                    seatWatcherOccupiedBy = nil
                    return
                end
                if seat and seat.Parent then
                    occNow = nil
                    pcall(function() occNow = seat.Occupant end)
                    if not occNow then
                        pcall(function() gucci(seat) end)
                    else
                        pcall(function() gucci(seat) end)
                    end
                end

                seatWatcherOccupiedBy = nil
            end)
        end
    end

    pcall(function()
        if seat and seat.Parent and seat.GetPropertyChangedSignal then
            seatWatcherConnection = seat:GetPropertyChangedSignal("Occupant"):Connect(function()
                if not tractorSystemEnabled then return end
                pcall(function() handleOccupantChange() end)
            end)
            task.spawn(function() task.wait(0.05) pcall(handleOccupantChange) end)
        else
            seatWatcherConnection = seat.Changed:Connect(function(prop)
                if not tractorSystemEnabled then return end
                if prop == "Occupant" then
                    pcall(function() handleOccupantChange() end)
                end
            end)
            task.spawn(function() task.wait(0.05) pcall(handleOccupantChange) end)
        end
    end)
end

function startTractorWatcher()
    if tractorWatcherConnection then
        tractorWatcherConnection:Disconnect()
        tractorWatcherConnection = nil
    end

    tractorWatcherConnection = RunService.Heartbeat:Connect(function()
        if not tractorSystemEnabled then return end

        if respawning then return end

        okCheck, tExists = pcall(function()
            return isValidTractor(tractor)
        end)
        if not okCheck then tExists = false end

        if not tExists then
            newTr2, newSeat2 = getTractorAndSeat()
            if newTr2 and newSeat2 and isValidTractor(newTr2) then
                tractor = newTr2
                seat = newSeat2
                pcall(function() setupRespawnListener(tractor) end)
                pcall(function() performSeatMethod(seat) end)
                return
            end

            respawning = true
            task.spawn(function()
                task.wait(0.06)
                newT, newS = spawnTractor()
                tries = 0
                repeat
                    if not newT or not newS or not newT.Parent then
                        newT, newS = getTractorAndSeat()
                    end
                    tries = tries + 1
                    task.wait(0.08)
                until (newT and newS) or tries >= 30 or not tractorSystemEnabled

                if tractorSystemEnabled and newT and newS and isValidTractor(newT) then
                    tractor = newT
                    seat = newS
                    pcall(function() setupRespawnListener(tractor) end)
                    pcall(function() performSeatMethod(seat) end)
                else
                    tractor = nil
                    seat = nil
                end
                respawning = false
            end)
        else
            seatOk, seatValid = pcall(function()
                return seat and seat.Parent and seat:IsDescendantOf(workspace)
            end)
            if not seatOk or not seatValid then
                pcall(function()
                    if tractor and tractor.Parent then
                        seat = tractor:FindFirstChildWhichIsA("VehicleSeat", true) or tractor:FindFirstChild("VehicleSeat", true)
                    end
                end)
                if not seat or not seat.Parent then
                    pcall(function() tractor = nil end)
                else
                    pcall(function() startSeatWatcher() end)
                end
            end
        end
    end)
end

function stopTractorWatcher()
    if tractorWatcherConnection then
        tractorWatcherConnection:Disconnect()
        tractorWatcherConnection = nil
    end
end

function enableTractorSystem()
    if tractorSystemEnabled then return end
    tractorSystemEnabled = true

    refreshCharacterRefs()

    task.spawn(function()
        ok = false
        repeat
            newTr, newSeat = spawnTractor()
            if newTr and newSeat then
                tractor = newTr
                seat = newSeat
                ok = true
                break
            end
            game:GetService("RunService").Heartbeat:Wait()
        until not tractorSystemEnabled

        if not ok then
            return
        end

        setupRespawnListener(tractor)

        if seat then
            pcall(function()
                performSeatMethod(seat)
                if ReplicatedStorage.CharacterEvents and ReplicatedStorage.CharacterEvents.RagdollRemote then
                    ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(HRP, -math.huge)
                end
            end)
        end

        pcall(function() startSeatWatcher() end)

        if tractorMainLoopConnection then
            tractorMainLoopConnection:Disconnect()
            tractorMainLoopConnection = nil
        end

        startRagdollWatcher()
        startTractorWatcher()
        connectDeathHandler()
    end)

    if charAddedConnection then
        charAddedConnection:Disconnect()
        charAddedConnection = nil
    end
    charAddedConnection = LocalPlayer.CharacterAdded:Connect(function(char)
        Character = char
        Humanoid = char:WaitForChild("Humanoid")
        HRP = char:WaitForChild("HumanoidRootPart")
        task.wait(0.1)
        if not tractorSystemEnabled then return end

        ok = false
        repeat
            tractor, seat = getTractorAndSeat()
            ok = tractor and seat
            task.wait(0.2)
        until ok or not tractorSystemEnabled

        if tractor and seat then
            setupRespawnListener(tractor)
            pcall(function()
                mt = ReplicatedStorage:FindFirstChild("MenuToys")
                if mt and mt:FindFirstChild("DestroyToy") and seat.Parent and seat.Parent.Name then
                    mt.DestroyToy:FireServer(seat.Parent.Name)
                else
                    safeDestroyToyArg(seat and seat.Parent)
                end
            end)

            newTr, newSeat = spawnTractor()
            if newTr and newSeat then
                tractor, seat = newTr, newSeat
                task.wait(0.09)
            end
        end

        startRagdollWatcher()
        pcall(function() startSeatWatcher() end)
        connectDeathHandler()
    end)
end

function disableTractorSystem()
    tractorSystemEnabled = false

    if tractorRespawnConnection then
        tractorRespawnConnection:Disconnect()
        tractorRespawnConnection = nil
    end

    if tractorMainLoopConnection then
        tractorMainLoopConnection:Disconnect()
        tractorMainLoopConnection = nil
    end

    if tractorDeathConnection then
        tractorDeathConnection:Disconnect()
        tractorDeathConnection = nil
    end

    if charAddedConnection then
        charAddedConnection:Disconnect()
        charAddedConnection = nil
    end

    stopRagdollWatcher()
    stopTractorWatcher()
    pcall(function() stopSeatWatcher() end)

    respawning = false
    tractor = nil
    seat = nil
end

if InvisTab and type(InvisTab.AddToggle) == "function" then
    invisibleTractorToggle = InvisTab:AddToggle({
        Name = "Anti Gucci",
        Default = false,
        Callback = function(Value)
            if Value then
                refreshCharacterRefs()
                enableTractorSystem()
            else
                toy = tractor
                disableTractorSystem()

                pcall(function()
                    if ReplicatedStorage and ReplicatedStorage:FindFirstChild("MenuToys") and ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy") and toy then
                        ReplicatedStorage.MenuToys.DestroyToy:FireServer(toy)
                    end
                end)

                pcall(function()
                    localHum = game.Players.LocalPlayer and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
                    if localHum then
                        localHum.Sit = true
                    end
                end)
                task.wait(0.05)
                game:GetService("RunService").Heartbeat:Wait()

                pcall(function()
                    if game:GetService("ReplicatedStorage").CharacterEvents and game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote then
                        hroot = game:GetService("Players").LocalPlayer.Character and game:GetService("Players").LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                        if hroot then
                            rgucci()
                        end
                    end
                end)
            end
        end
    })
else
    return {
        Enable = enableTractorSystem,
        Disable = disableTractorSystem,
    }
end
