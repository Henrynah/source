if game.Players.LocalPlayer.Name == "bebra7658" or "asqw_zv" or "Yaros1979" or "abororoumn" or "LYBLY_COCATb6969" then
    local HubName = "m1kp"
    local hui = false
    local UserInputService = game:GetService("UserInputService")
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local LocalCharacter = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local toysFolder = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Debris = game:GetService("Debris")
    --server
    local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
    local MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
    local CharacterEvents = ReplicatedStorage:WaitForChild("CharacterEvents")
    local SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
    local Struggle = CharacterEvents:WaitForChild("Struggle")
    local CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
    local DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
    local DestroyToy = MenuToys:WaitForChild("DestroyToy")
    local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")

    --Variables
    local humanoid = game.Players.LocalPlayer.Character.Humanoid
    local burnPart
    local strength = 400
    local strengthConnection
    local clickKill
    local clickBurn
    local ragdoll
    local auraRadius = 20
    local lag
    local lagValue
    local LeftBlobSelected
    local RightBlobSelected
    local DuoBlobSelected
    local whiteListEnabled = false
    local ServerBreak = false
    local TargetSelected
    local time
    local customTime
    local burnReach
    local defenseType
    local TpToPlayer
    local TpTrgtPos
    local houseSelected
    local buildSelected
    local secretSelected
    local defenseToggle
    local defenseStrength = 25
    local slotSelected
    local AmountOfPlayers = 0
    local pos = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
    local SpawnedInToys = game.Players.LocalPlayer.Name.."SpawnedInToys"
    local songSelected
    local SpamToggle = false
    local explodeAll
    local WhitelistPlayer
    local WhiteListedPlrs = {}
    local allPlrs
    local walkSpd = game.Players.LocalPlayer.Character.Humanoid.WalkSpeed
    local jumpPwr = game.Players.LocalPlayer.Character.Humanoid.JumpPower
    local gravity = game.Workspace.Gravity
    local infJump = false
    local FlnPrtsDstrHght = game:GetService('Workspace').FallenPartsDestroyHeight
    local antiVoidEnabled = false
    local StarterGui = game:GetService("StarterGui")
    local Players = game:GetService("Players")
    local player = Players.LocalPlayer
    local Sped = false
    local blobDelay
    --Coroutines
    local PoisonGrabCoroutine
    local poisonAuraCoroutine
    local grabAuraCoroutine
    local RadiactiveGrabCoroutine
    local antiGrabCoroutine
    local burnGrabCoroutine
    local burnBindCoroutine
    local firePlayerCoroutine
    local loopFirePlayerCoroutine
    local tpToSkyAuraCoroutine
    local killGrabCoroutine
    local defenseCoroutine
    local heavenGrabCoroutine
    local CrazyGrabCoroutine
    local RadioactiveAuraCoroutine
    local noclipGrabCoroutine
    local heavenBindCoroutine

    local function getDescendantParts(descendantName)
        local parts = {}
        for _, descendant in ipairs(workspace:GetDescendants()) do
            if descendant:IsA("Part") and descendant.Name == descendantName then
                table.insert(parts, descendant)
            end
        end
        return parts
    end

    local PoisonHurtParts = getDescendantParts("PoisonHurtPart")
    local PaintPlayerParts = getDescendantParts("PaintPlayerPart")

    local function spawnItem(itemName, position, orientation)
        task.spawn(function()
            local cframe = CFrame.new(position)
            local rotation = Vector3.new(0, 90, 0)
            MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
        end)
    end
    local function spawnItemCf(itemName, cframe)
        task.spawn(function()
            local rotation = Vector3.new(0, 0, 0)
            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
        end)
    end

    local function firePlayer(p)
        pcall(function()
            local player = Players:FindFirstChild(p)
            if player and player.Character then
                local playerCharacter = LocalCharacter
                if toysFolder:FindFirstChild("Campfire") then
                    DestroyToy:FireServer(toysFolder:FindFirstChild("Campfire"))
                    wait(0.5)
                end
                spawnItemCf("Campfire", playerCharacter.Head.CFrame + Vector3.new(0,-10,0))
                local campfire = toysFolder:WaitForChild("Campfire")
                local firePlayerPart
                for _, part in pairs(campfire:GetChildren()) do
                    if part.Name == "FirePlayerPart" then
                        part.Size = Vector3.new(10, 10, 10)
                        firePlayerPart = part
                        break
                    end
                end
                local originalPosition = playerCharacter.Torso.Position
                SetNetworkOwner:FireServer(firePlayerPart, firePlayerPart.CFrame)
                firePlayerPart.Size = Vector3.new(1,1,1)
                wait(0.3)
                playerCharacter:MoveTo(originalPosition)
                firePlayerPart.Size = Vector3.new(10, 10, 10)
                local bodyPosition = Instance.new("BodyPosition")
                bodyPosition.Parent = campfire.Main
                bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
                if player.Character and player.Character.HumanoidRootPart then
                    firePlayerPart.Position = player.Character.HumanoidRootPart.Position or player.Character.Head.Position
                    wait()
                end
            end
        end)
    end

    local function loopFirePlayer(p)
        pcall(function()
            local player = Players:FindFirstChild(p)
            if player and player.Character then
                local playerCharacter = LocalCharacter
                if toysFolder:FindFirstChild("Campfire") then
                    DestroyToy:FireServer(toysFolder:FindFirstChild("Campfire"))
                    wait(0.5)
                end
                spawnItemCf("Campfire", playerCharacter.Head.CFrame + Vector3.new(0,-10,0))
                local campfire = toysFolder:WaitForChild("Campfire")
                local firePlayerPart
                for _, part in pairs(campfire:GetChildren()) do
                    if part.Name == "FirePlayerPart" then
                    part.Size = Vector3.new(10, 10, 10)
                    firePlayerPart = part
                        break
                    end
                end
                SetNetworkOwner:FireServer(firePlayerPart, firePlayerPart.CFrame)
                firePlayerPart.Size = Vector3.new(1,1,1)
                wait(0.3)
                firePlayerPart.Size = Vector3.new(10, 10, 10)
                local bodyPosition = Instance.new("BodyPosition")
                bodyPosition.Parent = campfire.Main
                bodyPosition.Position = playerCharacter.Head.Position + Vector3.new(0, 600, 0)
                while true do
                    if player.Character and player.Character.HumanoidRootPart then
                        firePlayerPart.Position = player.Character.HumanoidRootPart.Position or player.Character.Head.Position
                        wait()
                        firePlayerPart.Position = Vector3.new(0,600,0)
                    end
                end
            end
            wait()
        end)
    end

    local function grabHandler(grabType)
        while true do
            local success, err = pcall(function()
                local child = workspace:FindFirstChild("GrabParts")
                if child and child.Name == "GrabParts" then
                    local grabPart = child:FindFirstChild("GrabPart")
                    local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                    local head = grabbedPart.Parent:FindFirstChild("Head")
                    if head then
                        while workspace:FindFirstChild("GrabParts") do
                            local partsTable = grabType == "poison" and PoisonHurtParts or PaintPlayerParts
                            for _, part in pairs(partsTable) do
                                part.Size = Vector3.new(2, 2, 2)
                                part.Transparency = 1
                                part.Position = head.Position
                            end
                            wait()
                            for _, part in pairs(partsTable) do
                                part.Position = Vector3.new(0, -200, 0)
                            end
                        end
                        for _, part in pairs(partsTable) do
                            part.Position = Vector3.new(0, -200, 0)
                        end
                    end
                end
            end)
            wait()
        end
    end

    local function burn(part)
        if not toysFolder:FindFirstChild("Campfire") then
            spawnItem("Campfire", Vector3.new(-72.9304581, -5.96906614, -265.543732))
        end
        local campfire = toysFolder:FindFirstChild("Campfire")
        burnPart = campfire:FindFirstChild("FirePlayerPart") or campfire.FirePlayerPart
        burnPart.Size = Vector3.new(7, 7, 7)
        burnPart.Position = part.Position
        task.wait(0.3)
        burnPart.Position = Vector3.new(0, -50, 0)
    end

    local function burnGrab()
        while true do
            local success, err = pcall(function()
                local child = workspace:FindFirstChild("GrabParts")
                if child and child.Name == "GrabParts" then
                    local grabPart = child:FindFirstChild("GrabPart")
                    local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                    local head = grabbedPart.Parent:FindFirstChild("Head")
                    if head then
                        burn(head)
                    end
                end
            end)
            wait()
        end
    end

    local function killGrab()
        while true do
            pcall(function()
                local child = workspace:FindFirstChild("GrabParts")
                if child and child.Name == "GrabParts" then
                    local grabPart = child:FindFirstChild("GrabPart")
                    local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                    local trgtCHR = grabbedPart.Parent
                    local trgt = trgtCHR.Parent
                    if grabbedPart.Parent then wait(0.4) trgtCHR.Humanoid.Health = 0 end
                end
            end)
            wait()
        end
    end

    local function setupAntiExplosion(character)
        local partOwner = character:WaitForChild("Humanoid"):FindFirstChild("Ragdolled")
        if partOwner then
            local partOwnerChangedConn
            partOwnerChangedConn = partOwner:GetPropertyChangedSignal("Value"):Connect(function()
                if partOwner.Value then
                    for _, part in ipairs(character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.Anchored = true
                        end
                    end
                else
                    for _, part in ipairs(character:GetChildren()) do
                        if part:IsA("BasePart") then
                            part.Anchored = false
                        end
                    end
                end
            end)
            antiExplosionConnection = partOwnerChangedConn
        end
    end

    local function kill(p)
        local pCHR = Players:FindFirstChild(p).Character
        local pHRP = pCHR.HumanoidRootPart
        local a = LocalPlayer.Character
        local inPos = a:GetPivot()
        while pCHR.Humanoid.Health ~= 0 do
            a.HumanoidRootPart.CFrame = pHRP.CFrame - Vector3.new(0, 10, 0)
            local args = {
                [1] = game:GetService("Players"):FindFirstChild(p).Character.HumanoidRootPart,
                [2] = CFrame.new(pHRP.Position) * CFrame.Angles(-0, 0, -0)
            }
            SetNetworkOwner:FireServer(unpack(args))
            for _, part in pairs(PoisonHurtParts) do
                part.Size = Vector3.new(1.5,1.5,1.5)
                part.Transparency = 1
                part.Position = pCHR:FindFirstChild("Head").Position
            end
            wait()
            for _, part in pairs(PoisonHurtParts) do
                part.Position = Vector3.new(0, -200, 0)
            end
        end
        for _, part in pairs(PoisonHurtParts) do
            part.Position = Vector3.new(0, -200, 0)
        end
        a:PivotTo(inPos)
    end

    local function TargetPlayersDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}

        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames
    end

    local function bringLeft(k)
        for _, v in pairs(workspace:GetDescendants()) do
            if v.Name == "CreatureBlobman" then
                local args = {
                    [1] = v.LeftDetector,
                    [2] = game:GetService("Players"):FindFirstChild(k).Character.HumanoidRootPart,
                    [3] = v.LeftDetector.LeftWeld
                }
        
                v.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
            end
        end
    end
    local function bringRight(k)
        for _, v in pairs(workspace:GetDescendants()) do
            if v.Name == "CreatureBlobman" then
                local args = {
                    [1] = v.RightDetector,
                    [2] = game:GetService("Players"):FindFirstChild(k).Character.HumanoidRootPart,
                    [3] = v.RightDetector.RightWeld
                }
        
                v.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
            end
        end
    end

    local function noclipGrab()
        while true do
            local success, err = pcall(function()
                local child = workspace:FindFirstChild("GrabParts")
                if child and child.Name == "GrabParts" then
                    local grabPart = child:FindFirstChild("GrabPart")
                    local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                    local character = grabbedPart.Parent
                    if character.HumanoidRootPart then
                        while workspace:FindFirstChild("GrabParts") do
                            for _, part in pairs(character:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                            wait()
                        end
                        for _, part in pairs(character:GetChildren()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = true
                            end
                        end
                    end
                end
            end)
            wait()
        end
    end

    local function LeftBlobPlayersDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function RightBlobPlayersDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function DuoBlobPlayersDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function TeleportToPlayersDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function ExplodePlayerDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function WhitelistPlayerDropdown()
        local players = game:GetService("Players"):GetPlayers()
        local playerNames = {}
        
        for _, player in ipairs(players) do
            table.insert(playerNames, player.Name)
        end
        
        return playerNames   
    end

    local function applyActionToPlayer(player)
        local torso = player.Character and player.Character:FindFirstChild("Torso")
        if torso then
            local args = {
                torso,
                CFrame.new(
                    -0.170715332,
                    0.714402676,
                    -0.5,
                    -0.898518324,
                    0,
                    0.438936025,
                    5.23252517e-08,
                    1,
                    1.07111731e-07,
                    -0.438936025,
                    5.96046448e-08,
                    -0.898518324
                )
            }
            CreateGrabLine:FireServer(unpack(args))
        end
    end

    local function laggScript(repetitions)
        local isScriptEnabled = true
        local scriptRunning = true
        
        while true do
            if scriptRunning and isScriptEnabled then
                for _, player in ipairs(Players:GetPlayers()) do
                    for _ = 1, repetitions do
                        applyActionToPlayer(player)
                    end
                end
            end
            wait(0.001)
            isScriptEnabled = false
            wait(10)
        end
    end

    --GUI
    local OrionLib = loadstring(game:HttpGet(("https://raw.githubusercontent.com/m1kp0/libraries/refs/heads/main/m1kpe0_orion_lib.lua")))()
    local Window = OrionLib:MakeWindow({Name = HubName, HidePremium = false, SaveConfig = False, ConfigFolder = "OrionTest", IntroEnabled = false})

    --Tabs
    local MainTab = Window:MakeTab({Name = "Line", Icon = "", PremiumOnly = false})
    local CharTab = Window:MakeTab({Name = "Character", Icon = "", PremiumOnly = false})
    local AuraTab = Window:MakeTab({Name = "Aura", Icon = "", PremiumOnly = false})
    local Tab = Window:MakeTab({Name = "Anti-all", Icon = "", PremiumOnly = false})
    local FunTab = Window:MakeTab({Name = "Fun", Icon = "", PremiumOnly = false})
    local BlobTab = Window:MakeTab({Name = "Blob", Icon = "", PremiumOnly = false})
    local TargetTab = Window:MakeTab({Name = "Target", Icon = "", PremiumOnly = false})
    local TPTab = Window:MakeTab({Name = "Teleport", Icon = "", PremiumOnly = false})
    local AutoPianoTab = Window:MakeTab({Name = "Auto piano", Icon = "", PremiumOnly = false})
    local VisualTab = Window:MakeTab({Name = "Visual", Icon = "", PremiumOnly = false})
    local BindTab = Window:MakeTab({Name = "Keybinds", Icon = "", PremiumOnly = false})
    local WhitelistTab = Window:MakeTab({Name = "Whitelist (скоро)", Icon = "", PremiumOnly = false})
    local ServerInfoTab = Window:MakeTab({Name = "ServerInfo", Icon = "", PremiumOnly = false})
    local ScriptTab = Window:MakeTab({Name = "Scripts", Icon = "", PremiumOnly = false})


    MainTab:AddToggle({
        Name = "More strength",
        Default = false,
        Save = true,
        Color = Color3.fromRGB(102, 0, 102),
        Flag = "StrengthToggle",
        Callback = function(Value)
            if Value then
                strengthConnection = workspace.ChildAdded:Connect(function(model)
                    if model.Name == "GrabParts" then
                        local partToFling = model.GrabPart.WeldConstraint.Part1
                        if partToFling then
                            local velocityObj = Instance.new("BodyVelocity", partToFling)
                            model:GetPropertyChangedSignal("Parent"):Connect(function()
                                if not model.Parent then
                                    if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                                        velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        velocityObj.Velocity = workspace.CurrentCamera.CFrame.LookVector * strength
                                        Debris:AddItem(velocityObj, 1)
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

    MainTab:AddSlider({
        Name = "Strength",
        Min = 100,
        Max = 10000,
        Color = Color3.fromRGB(102, 0, 102),
        ValueName = "",
        Increment = 50,
        Default = strength,
        Callback = function(Value)
            strength = Value
        end
    })

    local Section = MainTab:AddSection({Name = "Grabs"})

    MainTab:AddToggle({
        Name = "Poison grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                PoisonGrabCoroutine = coroutine.create(function() grabHandler("poison") end)
                coroutine.resume(PoisonGrabCoroutine)
            else
                if PoisonGrabCoroutine then
                    coroutine.close(PoisonGrabCoroutine)
                    PoisonGrabCoroutine = nil
                    for _, prt in pairs(PoisonHurtParts) do
                        prt.Position = Vector3.new(0,-200,0)
                    end
                end
            end
        end
    })

    MainTab:AddToggle({
        Name = "Radiactive grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                RadiactiveGrabCoroutine = coroutine.create(function() grabHandler("radioctive") end)
                coroutine.resume(RadiactiveGrabCoroutine)
            else
                if RadiactiveGrabCoroutine then
                    coroutine.close(RadiactiveGrabCoroutine)
                    RadiactiveGrabCoroutine = nil
                    for _, prt in pairs(PaintPlayerParts) do
                        prt.Position = Vector3.new(0,-200,0)
                    end
                end
            end
        end
    })

    MainTab:AddToggle({
        Name = "Burn grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                burnGrabCoroutine = coroutine.create(burnGrab)
                coroutine.resume(burnGrabCoroutine)
            else
                if burnGrabCoroutine then
                    coroutine.close(burnGrabCoroutine)
                    burnGrabCoroutine = nil
                end
            end
        end
    })

    MainTab:AddToggle({
        Name = "Kill grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                killGrabCoroutine = coroutine.create(killGrab)
                coroutine.resume(killGrabCoroutine)
            else
                if killGrabCoroutine then
                    coroutine.close(killGrabCoroutine)
                    killGrabCoroutine = nil
                end
            end
        end
    })

    MainTab:AddToggle({
        Name = "Heaven grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                heavenGrabCoroutine = coroutine.create(function()
                    while true do
                        pcall(function()
                            local child = workspace:FindFirstChild("GrabParts")
                            if child and child.Name == "GrabParts" then
                                local grabPart = child:FindFirstChild("GrabPart")
                                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                                local trgtCHR = grabbedPart.Parent
                                local target = trgtCHR.Torso
                                if trgtCHR then
                                    local velocity = target:FindFirstChild("heavenG") or Instance.new("BodyVelocity")
                                    velocity.Name = "heavenG"
                                    velocity.Parent = target
                                    velocity.Velocity = Vector3.new(0,9999999,0)
                                    velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    Debris:AddItem(velocity, 100)
                                end
                            end
                        end)
                        wait()
                    end
                end)
                coroutine.resume(heavenGrabCoroutine)
            else
                if heavenGrabCoroutine then
                    coroutine.close(heavenGrabCoroutine)
                    heavenGrabCoroutine = nil
                    for _, v in pairs(Players:GetPlayers()) do
                        local a = v.Torso
                        if a then
                            for _, i in pairs(a:GetChildren()) do
                                if i.Name == "heavenG" and i:IsA("BodyVelocuty") then i:Destroy() end
                            end
                        end
                    end
                end
            end
        end
    })

    MainTab:AddToggle({
        Name = "Teleport grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                CrazyGrabCoroutine = coroutine.create(function()
                    while true do
                        pcall(function()
                            local child = workspace:FindFirstChild("GrabParts")
                            if child and child.Name == "GrabParts" then
                                local grabPart = child:FindFirstChild("GrabPart")
                                local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                                local trgtCHR = grabbedPart.Parent
                                local target = trgtCHR.Torso
                                if trgtCHR then
                                    if TPgrabOption == "TP to spawn" then
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(-1, -7, -9)
                                        wait()
                                    elseif TPgrabOption == "Crazy teleport" then
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(-17, 421, 50)
                                        wait(0.1)
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(145, 397, -126)
                                        wait(0.1)
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(157, 254, 89)
                                        wait(0.1)
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(-26, 207, -26)
                                        wait(0.1)
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(-23, 456, -19)
                                        wait(0.1)
                                        trgtCHR.HumanoidRootPart.CFrame = CFrame.new(15, 211, 45)
                                        wait(0.1)
                                    end
                                end
                            end
                        end)
                        wait()
                    end
                end)
                coroutine.resume(CrazyGrabCoroutine)
            else
                if CrazyGrabCoroutine then
                    coroutine.close(CrazyGrabCoroutine)
                    CrazyGrabCoroutine = nil
                end
            end
        end
    })

    MainTab:AddDropdown({
        Name = "Teleport grab",
        Default = "TP to spawn",
        Options = {"TP to spawn", "Crazy teleport"},
        Callback = function(Value)
            TPgrabOption = Value
        end    
    })

    MainTab:AddToggle({
        Name = "Noclip grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Save = true,
        Callback = function(enabled)
            if enabled then
                noclipGrabCoroutine = coroutine.create(noclipGrab)
                coroutine.resume(noclipGrabCoroutine)
            else
                if noclipGrabCoroutine then
                    coroutine.close(noclipGrabCoroutine)
                    noclipGrabCoroutine = nil
                end
            end
        end
    })

    local Section = CharTab:AddSection({
	Name = "Default"
    })

    CharTab:AddSlider({
    	Name = "Walk speed",
    	Min = 0,
    	Max = 200,
    	Default = 16,
    	Color = Color3.fromRGB(102, 0, 102),
    	Increment = 1,
    	ValueName = "Speed",
    	Callback = function(Speeds)
            print(Speeds)
    	end     
    })

    CharTab:AddSlider({
    	Name = "Jump Power",
    	Min = 0,
    	Max = 500,
    	Default = 28,
    	Color = Color3.fromRGB(102, 0, 102),
    	Increment = 1,
    	ValueName = "Jump",
    	Callback = function(Jumpss)
            print(Jumpss)
    	end    
    })

    CharTab:AddSlider({
    	Name = "Gravity",
    	Min = 0,
    	Max = 500,
    	Default = 200,
    	Color = Color3.fromRGB(102, 0, 102),
    	Increment = 1,
    	ValueName = "Gravity",
    	Callback = function(Gravs)
            print(Gravs)
    	end    
    })

    local Section = CharTab:AddSection({Name = "Advanced"})

    CharTab:AddToggle({
        Name = "Infinite jumps",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                infJump = true
                game:GetService("UserInputService").JumpRequest:Connect(function()
                    if infJump then
                        game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
                    end
                end)
            else
                infJump = false
            end
        end
    })

    local Section = CharTab:AddSection({Name = "Character"})

    CharTab:AddButton({
        Name = "Sit",
        Callback = function()
            game.Players.LocalPlayer.Character.Humanoid.Sit = true
        end    
    })

    AuraTab:AddToggle({
        Name = "Grab aura",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                grabAuraCoroutine = coroutine.create(function()
                    while Value do
                        pcall(function()
                            local character = LocalCharacter
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character.HumanoidRootPart

                                for _, player in pairs(Players:GetPlayers()) do
                                    if player ~= LocalPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerHRP = playerCharacter:FindFirstChild("HumanoidRootPart")
                                        if playerHRP then
                                            if (LocalPlayer.Character.HumanoidRootPart.Position - playerCharacter.HumanoidRootPart.Position).Magnitude <= (LocalPlayer:FindFirstChild("DefaultReach").Value - 5) then
                                                if playerHRP then
                                                    if not whiteListEnabled or not game.Players.LocalPlayer:IsFriendsWith(player.UserId) then
                                                        SetNetworkOwner:FireServer(playerCharacter.HumanoidRootPart, playerCharacter.HumanoidRootPart.CFrame)
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end)
                        wait(0.02)
                    end
                end)
                coroutine.resume(grabAuraCoroutine)
            elseif grabAuraCoroutine then
                coroutine.close(grabAuraCoroutine)
                grabAuraCoroutine = nil
            end
        end 
    })

    AuraTab:AddToggle({
        Name = "Poison Aura",
        Default = false,
        Save = true,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                poisonAuraCoroutine = coroutine.create(function()
                    while Value do
                        pcall(function()
                            local character = LocalCharacter
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character.HumanoidRootPart

                                for _, player in pairs(Players:GetPlayers()) do
                                    if player ~= LocalPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerHRP = playerCharacter:FindFirstChild("HumanoidRootPart")
                                        if playerHRP then
                                            if (LocalPlayer.Character.HumanoidRootPart.Position - playerCharacter.HumanoidRootPart.Position).Magnitude <= (LocalPlayer:FindFirstChild("DefaultReach").Value - 5) then
                                                local head = playerCharacter:FindFirstChild("Head")
                                                while (LocalPlayer.Character.HumanoidRootPart.Position - playerCharacter.HumanoidRootPart.Position).Magnitude <= (LocalPlayer:FindFirstChild("DefaultReach").Value - 5) and playerCharacter.Humanoid.Health ~= 0 do
                                                    if playerHRP then
                                                        if not whiteListEnabled or not game.Players.LocalPlayer:IsFriendsWith(player.UserId) then
                                                            SetNetworkOwner:FireServer(playerCharacter.HumanoidRootPart, playerCharacter.HumanoidRootPart.CFrame)
                                                            for _, part in pairs(PoisonHurtParts) do
                                                                part.Size = Vector3.new(1, 3, 1)
                                                                part.Transparency = 1
                                                                part.Position = head.Position
                                                            end
                                                            wait()
                                                            for _, part in pairs(PoisonHurtParts) do
                                                                part.Position = Vector3.new(0, -200, 0)
                                                            end
                                                        end
                                                    end
                                                end
                                                for _, part in pairs(PoisonHurtParts) do
                                                    part.Position = Vector3.new(0, -200, 0)
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end)
                        wait(0.02)
                    end
                end)
                coroutine.resume(poisonAuraCoroutine)
            elseif poisonAuraCoroutine then
                coroutine.close(poisonAuraCoroutine)
                for _, part in pairs(PoisonHurtParts) do
                    part.Position = Vector3.new(0, -200, 0)
                end
                poisonAuraCoroutine = nil
            end
        end
    })

    AuraTab:AddToggle({
        Name = "Delete aura",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                tpToSkyAuraCoroutine = coroutine.create(function()
                    while Value do
                        pcall(function()
                            local character = LocalCharacter
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character.HumanoidRootPart
                                for _, player in pairs(Players:GetPlayers()) do
                                    if player ~= LocalPlayer and player.Character then
                                        local playerCharacter = player.Character
                                        local playerHRP = playerCharacter:FindFirstChild("HumanoidRootPart")
                                        if playerHRP then
                                            if (LocalPlayer.Character.HumanoidRootPart.Position - playerCharacter.HumanoidRootPart.Position).Magnitude <= (LocalPlayer:FindFirstChild("DefaultReach").Value - 5) then
                                                while (LocalPlayer.Character.HumanoidRootPart.Position - playerCharacter.HumanoidRootPart.Position).Magnitude <= (LocalPlayer:FindFirstChild("DefaultReach").Value - 5) and playerCharacter.Humanoid.Health ~= 0 do
                                                    if playerHRP then
                                                        if not whiteListEnabled or not game.Players.LocalPlayer:IsFriendsWith(player.UserId) then
                                                            SetNetworkOwner:FireServer(playerCharacter.Torso, playerCharacter.HumanoidRootPart.CFrame)
                                                            local trgtCHR = playerCharacter
                                                            local target = trgtCHR.Torso
                                                            if trgtCHR then
                                                                local velocity = target:FindFirstChild("heavenG") or Instance.new("BodyVelocity")
                                                                velocity.Name = "heavenG"
                                                                velocity.Parent = target
                                                                velocity.Velocity = Vector3.new(0,9999999,0)
                                                                velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                                Debris:AddItem(velocity, 100)
                                                            end
                                                        end
                                                    end
                                                    wait()
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end)
                        wait(0.02)
                    end
                end)
                coroutine.resume(tpToSkyAuraCoroutine)
            elseif tpToSkyAuraCoroutine then
                coroutine.close(tpToSkyAuraCoroutine)
                tpToSkyAuraCoroutine = nil
                for _, v in pairs(Players:GetPlayers()) do
                    local a = v.Torso
                    if a then
                        for _, i in pairs(a:GetChildren()) do
                            if i.Name == "heavenG" and i:IsA("BodyVelocuty") then i:Destroy() end
                        end
                    end
                end
            end
        end 
    })

    

    local Section = AuraTab:AddSection({Name = "Whitelist"})

    AuraTab:AddToggle({
        Name = "Friends Whitelist",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            whiteListEnabled = Value
            if whiteListEnabled == true then 
                OrionLib:MakeNotification(
                    {
                        Name = "whitelist enabled",
                        Content = "ur aura cant grab ur frends",
                        Image = "rbxassetid://7734053281",
                        Time = 5
                    }
                )
            end
        end
    })

    Tab:AddButton({
        Name = "Super anti grab (needs ragdoll and blob)",
        Callback = function()
            for _, v in pairs(workspace:GetDescendants()) do
                if v.Name == "CreatureBlobman" then
                    local args = {
                        [1] = v.RightDetector,
                        [2] = game:GetService("Players").LocalPlayer.Character.HumanoidRootPart,
                        [3] = v.RightDetector.RightWeld
                    }
            
                    v.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
                    local args = {
                        [1] = game:GetService("Players").LocalPlayer.Character.HumanoidRootPart,
                        [2] = CFrame.new(v.RightDetector.Position) * CFrame.Angles(-0, 0, -0)
                    }
            
                    SetNetworkOwner:FireServer(unpack(args))     
                end
            end
        end    
    })

    Tab:AddToggle({
        Name = "Anti grab",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)     
            if Value then
                antiGrabCoroutine = RunService.Heartbeat:Connect(function()
                    local character = LocalPlayer.Character
                    if character and character:FindFirstChild("Head") then
                        local head = character.Head
                        local partOwner = head:FindFirstChild("PartOwner")
                        if partOwner then
                            Struggle:FireServer()
                            RagdollRemote:FireServer(character.HumanoidRootPart, 0)
                            ReplicatedStorage.GameCorrectionEvents.StopAllVelocity:FireServer()
                            for _, part in pairs(character:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.Anchored = true
                                end
                            end
                            while LocalPlayer.IsHeld.Value do
                                wait()
                            end
                            for _, part in pairs(character:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.Anchored = false
                                end
                            end
                        end
                    end
                end)
            else
                if antiGrabCoroutine then
                    antiGrabCoroutine:Disconnect()
                    antiGrabCoroutine = nil
                end
            end
        end 
    })

    Tab:AddToggle({
        Name = "Anti explosion",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Save = true,
        Flag = "AntiExplosion",
        Callback = function(enabled)
            local localPlayer = game.Players.LocalPlayer
    
            if enabled then
                if localPlayer.Character then
                    setupAntiExplosion(localPlayer.Character)
                end
                characterAddedConn = localPlayer.CharacterAdded:Connect(function(character)
                    if antiExplosionConnection then
                        antiExplosionConnection:Disconnect()
                    end
                    setupAntiExplosion(character)
                end)
            else
                if antiExplosionConnection then
                    antiExplosionConnection:Disconnect()
                    antiExplosionConnection = nil
                end
                if characterAddedConn then
                    characterAddedConn:Disconnect()
                    characterAddedConn = nil
                end
            end
        end
    })

    Tab:AddToggle({
        Name = "Anti lag",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            local a = game.Players.LocalPlayer.Name
            if Value == true then
                game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = false
            elseif Value == false then
                game.Players.LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = true
            end
        end    
    })

    Tab:AddToggle({
        Name = "Anti bring",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            local a = game.Players.LocalPlayer.Name.."SpawnedInToys"
            hui = Value
            if hui == true then
                for _, v in pairs(workspace:GetDescendants()) do
                    if v.Name == "LeftWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "LeftAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "RightWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "RightAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    end
                end
                workspace.DescendantAdded:Connect(function(v)
                    if v.Name == "LeftWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "LeftAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "RightWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    elseif v.Name == "RightAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = false
                        end
                    end
                end)
            end
            if hui == false then
                for _, v in pairs(workspace:GetDescendants()) do
                    if v.Name == "LeftWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = true
                        end
                    elseif v.Name == "LeftAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = true
                        end
                    elseif v.Name == "RightWeld" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = true
                        end
                    elseif v.Name == "RightAlignOrientation" then
                        if v.Parent.Parent.Parent ~= workspace:FindFirstChild(a) then
                            v.Enabled = true
                        end
                    end
                end
            end
        end
    })

    Tab:AddToggle({
        Name = "Anti void",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                game:GetService('Workspace').FallenPartsDestroyHeight = -100000
                while Value do
                    while game.Players.LocalPlayer.Character.HumanoidRootPart and game.Players.LocalPlayer.Character.HumanoidRootPart.Position.Y < -500 and antiVoidEnabled do
                        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(2, -7, -4)
                        OrionLib:MakeNotification({
                            Name = "Theres a staarmaan waiting in the sky",
                            Content = "i will save you next time:3",
                            Image = "rbxassetid://18624604880",
                            Time = 5
                        })
                        wait()
                    end
                    wait()
                end
            else
                game:GetService('Workspace').FallenPartsDestroyHeight = -100
                antiVoidEnabled = false
            end
        end    
    })

    Tab:AddButton({
        Name = "Anti bang (нажми если включили банг)",
        Callback = function(Value)
            local positionOld = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame
		    game:GetService('Workspace').FallenPartsDestroyHeight = -1000
		    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(Vector3.new(0, game:GetService('Workspace').FallenPartsDestroyHeight + 500, 0))
		    wait(0.8)
		    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(positionOld)
            game:GetService('Workspace').FallenPartsDestroyHeight = -100
        end    
    })

    Tab:AddParagraph("Note","Super anti-grab do BIG ping (for all), reset and turn off 'Ragdoll' and server will got small ping")

    local Section = Tab:AddSection({Name = "Rinnegan"})

    Tab:AddDropdown({
        Name = "Rinnegan type",
        Default = "Throw",
        Options = {"Throw", "Kill", "Delete"},
        Callback = function(Value)
            defenseType = Value
        end    
    })

    Tab:AddToggle({
        Name = "Rinnegan",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            defenseToggle = Value
            if defenseToggle then
                defenseCoroutine = coroutine.create(function()
                    while true do
                        if defenseType == "Throw" then
                            local attacker
                            local character = LocalPlayer.Character
                            if character and character:FindFirstChild("Head") then
                                local head = character.Head
                                local partOwner = head:FindFirstChild("PartOwner")
                                if partOwner then
                                    attacker = Players:FindFirstChild(partOwner.Value)
                                    if attacker and attacker.Character then
                                        SetNetworkOwner:FireServer(attacker.Character.Torso, attacker.Character.HumanoidRootPart.CFrame)
                                        task.wait(0.1)
                                        local target = attacker.Character:FindFirstChild("Torso")
                                        if target then
                                            local velocity = target:FindFirstChild("Fling") or Instance.new("BodyVelocity")
                                            local lookVector = attacker.Character.HumanoidRootPart.CFrame.LookVector
                                            local offset = Vector3.new(-lookVector.X, 0, -lookVector.Z) * defenseStrength
                                            velocity.Name = "Fling"
                                            velocity.Parent = target
                                            velocity.Velocity = offset + Vector3.new(0,7.5,0)
                                            velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                            Debris:AddItem(velocity, 100)
                                            wait(0.2)                     
                                            local velocity = attacker.Character.Torso:FindFirstChild("Fling")
                                            velocity:Destroy()
                                            DestroyGrabLine:FireServer(attacker.Character.Torso)
                                        end
                                    end
                                end
                            end
                        elseif defenseType == "Kill" then
                            local character = LocalPlayer.Character
                            if character and character:FindFirstChild("Head") then
                                local head = character.Head
                                local partOwner = head:FindFirstChild("PartOwner")
                                if partOwner then
                                    local attacker = Players:FindFirstChild(partOwner.Value)
                                    if attacker and attacker.Character then
                                        SetNetworkOwner:FireServer(attacker.Character.Head or attacker.Character.Torso, attacker.Character.HumanoidRootPart.CFrame)
                                        task.wait(0.05)
                                        local target = attacker.Character
                                        local targetHead = target.Head
                                        if target and targetHead then
                                            while target.Humanoid.Health ~= 0 do
                                                for _, part in pairs(PoisonHurtParts) do
                                                    part.Size = Vector3.new(1, 3, 1)
                                                    part.Transparency = 1
                                                    part.Position = targetHead.Position
                                                end
                                                wait()
                                                for _, part in pairs(PoisonHurtParts) do
                                                    part.Position = Vector3.new(0, -200, 0)
                                                end
                                            end
                                            for _, part in pairs(PoisonHurtParts) do
                                                part.Position = Vector3.new(0, -200, 0)
                                            end
                                        end
                                    end
                                end
                            end
                        elseif defenseType == "Delete" then
                            local character = LocalPlayer.Character
                            if character and character:FindFirstChild("Head") then
                                local head = character.Head
                                local partOwner = head:FindFirstChild("PartOwner")
                                if partOwner then
                                    local attacker = Players:FindFirstChild(partOwner.Value)
                                    if attacker and attacker.Character then
                                        SetNetworkOwner:FireServer(attacker.Character.Head or attacker.Character.Torso, attacker.Character.HumanoidRootPart.CFrame)
                                        task.wait(0.1)
                                        local target = attacker.Character:FindFirstChild("Torso")
                                        if target then
                                            local velocity = target:FindFirstChild("heavenD") or Instance.new("BodyVelocity")
                                            velocity.Name = "heavenD"
                                            velocity.Parent = target
                                            velocity.Velocity = Vector3.new(0,9999999,0)
                                            velocity.MaxForce = Vector3.new(0, math.huge, 0)
                                            Debris:AddItem(velocity, 100)
                                        end
                                    end
                                end
                            end
                        end
                        wait(0.02)
                    end
                end)
                coroutine.resume(defenseCoroutine)
            else
                if defenseCoroutine then
                    coroutine.close(defenseCoroutine)
                    for _, v in pairs(Players:GetPlayers()) do
                        local a = v.Torso
                        if a then
                            for _, i in pairs(a:GetChildren()) do
                                if i.Name == "heavenG" or "heavenD" or "Fling" and i:IsA("BodyVelocuty") then i:Destroy() end
                            end
                        end
                    end
                    defenseCoroutine = nil
                end
            end
        end
    })

    Tab:AddSlider({
        Name = "Throw strength",
        Min = 25,
        Max = 200,
        Color = Color3.fromRGB(102, 0, 102),
        ValueName = "",
        Increment = 1,
        Default = defenseStrength,
        Callback = function(Value)
            defenseStrength = Value
        end
    })

    FunTab:AddToggle({
        Name = "Ragdoll (enable for super anti-grab)",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            ragdoll = Value
            if ragdoll then
                spawn(function()
                    while ragdoll do
                        local character = game:GetService("Players").LocalPlayer.Character
                        if character and character:FindFirstChild("HumanoidRootPart") then
                            local args = {
                                [1] = character.HumanoidRootPart,
                                [2] = 0
                            }
                            RagdollRemote:FireServer(unpack(args))
                        end
                        wait()
                    end
                end)
            end
        end
    })

    local Section = BlobTab:AddSection({Name = "Left bring"})

    local LeftBlobDrop = BlobTab:AddDropdown({
        Name = "Left player",
        Default = "",
        Options = LeftBlobPlayersDropdown(),
        Callback = function(Value)
            LeftBlobSelected = Value
        end    
    })

    BlobTab:AddButton({
        Name = "Left bring",
        Callback = function()
            if LeftBlobSelected then
                bringLeft(LeftBlobSelected)
            end
        end    
    })

    BlobTab:AddToggle({
        Name = "Loop left bring (bugged)",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if LeftBlobSelected then
                hui = Value
                if hui then
                    while hui do
                        bringLeft(LeftBlobSelected)
                        wait(0.1)
                    end
                end
            end
        end    
    })

    local Section = BlobTab:AddSection({Name = "Right bring"})

    local RightBlobDrop = BlobTab:AddDropdown({
        Name = "Right player",
        Default = "",
        Options = RightBlobPlayersDropdown(),
        Callback = function(Value)
            RightBlobSelected = Value
        end    
    })

    BlobTab:AddButton({
        Name = "Right bring",
        Callback = function()
            bringRight(RightBlobSelected)
        end    
    })

    BlobTab:AddToggle({
        Name = "Loop right bring (bugged)",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if RightBlobSelected then
                hui = Value
                if hui then
                    while hui do
                        bringRight(RightBlobSelected)
                        wait(0.1)
                    end
                end
            end
        end    
    })

    local Section = BlobTab:AddSection({Name = "Duo bring"})

    local DuoBlobDrop = BlobTab:AddDropdown({
        Name = "Two hands player",
        Default = "",
        Options = DuoBlobPlayersDropdown(),
        Callback = function(Value)
            DuoBlobSelected = Value
        end    
    })

    BlobTab:AddButton({
        Name = "Two hands bring",
        Callback = function()
            if DuoBlobSelected then
                bringRight(DuoBlobSelected)
                bringLeft(DuoBlobSelected)
            end
        end    
    })

    BlobTab:AddToggle({
        Name = "Loop two hands bring",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if DuoBlobSelected then
                hui = Value
                if hui then
                    while hui do
                        bringLeft(DuoBlobSelected)
                        bringRight(DuoBlobSelected)
                        wait(0.1)
                    end
                end
            end
        end    
    })

    local Section = BlobTab:AddSection({Name = "Destroy server"})

    BlobTab:AddButton({
        Name = "Bring all",
        Callback = function()
            for _, player in pairs(Players:GetPlayers()) do
                if player.Name ~= LocalPlayer.Name then
                    bringLeft(player.Name)
                    bringRight(player.Name)
                end
            end
        end    
    })

    BlobTab:AddToggle({
        Name = "Destroy server",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            ServerBreak = Value
            local playerName = game.Players.LocalPlayer.Name
            local Vex2ySpawnedInToys = playerName.."SpawnedInToys"

            local function handlePlayer(player, detector, weld)
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    for _, v in pairs(workspace:GetDescendants()) do
                        if v.Name == "CreatureBlobman" then
                            local creatureBlobman = v
                            local args = {
                                [1] = detector,
                                [2] = player.Character.HumanoidRootPart,
                                [3] = weld
                            }
                            wait(0.01)
                            creatureBlobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
                        end
                    end
                end
            end

            while ServerBreak do
                for _, player in pairs(game:GetService("Players"):GetPlayers()) do
                    if player ~= LocalPlayer then
                        for _, v in pairs(workspace:GetDescendants()) do
                            if v.Name == "CreatureBlobman" then
                                if not whiteListEnabled or not game.Players.LocalPlayer:IsFriendsWith(player.UserId) then
                                    local creatureBlobman = v.Parent
                                    if creatureBlobman and creatureBlobman:FindFirstChild("CreatureBlobman") then
                                        for _, player in pairs(Players:GetPlayers()) do
                                            if player.Name ~= LocalPlayer.Name then
                                                bringLeft(player.Name)
                                                bringRight(player.Name)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
                wait(blobDelay)
            end
        end
    })

    BlobTab:AddTextbox({
        Name = "Delay (не слайдер для мобил)",
        Default = "0.001",
        TextDisappear = false,
        Callback = function(BlobDelay)
            blobDelay = BlobDelay
        end
    })

    BlobTab:AddToggle({
        Name = "Friends Whitelist",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            whiteListEnabled = Value
            if whiteListEnabled == true then 
                OrionLib:MakeNotification(
                    {
                        Name = "whitelist enabled",
                        Content = "ur blob cant grab ur frends",
                        Image = "rbxassetid://7734053281",
                        Time = 5
                    }
                )
            end
        end
    })

    local TargetDrop = TargetTab:AddDropdown({
        Name = "Select player",
        Default = "",
        Options = TargetPlayersDropdown(),
        Callback = function(Value)
            TargetSelected = Value
        end    
    })

    TargetTab:AddButton({
        Name = "Kill",
        Callback = function()
            kill(TargetSelected)
        end   
    })

    TargetTab:AddButton({
        Name = "Burn",
        Callback = function()
            firePlayer(TargetSelected)
        end   
    })

    TargetTab:AddToggle({
        Name = "Loop burn",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value then
                loopFirePlayerCoroutine = coroutine.create(function() loopFirePlayer(TargetSelected) end)
                coroutine.resume(loopFirePlayerCoroutine)
            else
                if loopFirePlayerCoroutine then
                    coroutine.close(loopFirePlayerCoroutine)
                    loopFirePlayerCoroutine = nil
                end
            end
        end
    })

    VisualTab:AddToggle({
        Name = "Unblur (disable blur)",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            if Value == true then
                workspace.Camera.Blur.Enabled = false
            else
                workspace.Camera.Blur.Enabled = true
            end
        end 
    })

    VisualTab:AddButton({
        Name = "Delete clouds",
        Callback = function()
            workspace.Terrain.Clouds:Destroy()
        end 
    })

    local Section = VisualTab:AddSection({Name = "Custom time"})

    VisualTab:AddButton({
        Name = "Day (default)",
        Callback = function()
            game.Lighting.ClockTime = 10
        end 
    })

    VisualTab:AddButton({
        Name = "Night",
        Callback = function()
            game.Lighting.ClockTime = 0
        end 
    })

    VisualTab:AddButton({
        Name = "Morning",
        Callback = function()
            game.Lighting.ClockTime = 6
        end 
    })

    VisualTab:AddButton({
        Name = "Evening",
        Callback = function()
            game.Lighting.ClockTime = 18
        end 
    })

    BindTab:AddToggle({
        Name = "Burn",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            clickBurn = Value
            spawnItem("Campfire")
        end
    })

    BindTab:AddBind({
        Name = "Bind burn",
        Default = nil,
        Hold = false,
        Callback = function()
            if clickBurn == true then
                local Player = Players.LocalPlayer
                local Mouse = Player:GetMouse()
                local target = Mouse.Target
                if target and target.Parent then
                    local targetHumanoid = target.Parent:FindFirstChildOfClass("Humanoid")
                    local targetPlayer = Players:GetPlayerFromCharacter(target.Parent)
                    if targetHumanoid and targetPlayer and targetPlayer ~= Player then
                        burnBindCoroutine = coroutine.create(function() burn(targetPlayer.Character:FindFirstChild("Head")) end)
                        if (Player.Character.HumanoidRootPart.Position - target.Position).Magnitude <= 20 then
                            if workspace:FindFirstChild("GrabParts") then
                                coroutine.resume(burnBindCoroutine)
                            else
                                SetNetworkOwner:FireServer(targetPlayer.Character.HumanoidRootPart, targetPlayer.Character.HumanoidRootPart.CFrame)
                                coroutine.resume(burnBindCoroutine)
                                DestroyGrabLine:FireServer(targetPlayer.Character.HumanoidRootPart)
                            end
                        end
                    end
                end
            end
        end
    })

    BindTab:AddToggle({
        Name = "Kill",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            clickKill = Value
        end
    })

    BindTab:AddBind({
        Name = "Bind kill",
        Default = nil,
        Hold = false,
        Callback = function()
            if clickKill == true then
                local Player = Players.LocalPlayer
                local Mouse = Player:GetMouse()
                local target = Mouse.Target
                if target and target.Parent then
                    local targetHumanoid = target.Parent:FindFirstChildOfClass("Humanoid")
                    local targetPlayer = Players:GetPlayerFromCharacter(target.Parent)
                    if targetHumanoid and targetPlayer and targetPlayer ~= Player then
                        if (Player.Character.HumanoidRootPart.Position - target.Position).Magnitude <= 20 then
                            while targetPlayer.Character.Humanoid.Health ~= 0 do
                                local args = {
                                    [1] = targetPlayer.Character.HumanoidRootPart,
                                    [2] = CFrame.new(targetPlayer.Character.HumanoidRootPart.Position) * CFrame.Angles(-0, 0, -0)
                                }
                                SetNetworkOwner:FireServer(unpack(args))
                                for _, part in pairs(PoisonHurtParts) do
                                    part.Size = Vector3.new(1.5,1.5,1.5)
                                    part.Transparency = 1
                                    part.Position = targetPlayer.Character:FindFirstChild("Head").Position
                                end
                                wait()
                                for _, part in pairs(PoisonHurtParts) do
                                    part.Position = Vector3.new(0, -200, 0)
                                end
                            end
                            for _, part in pairs(PoisonHurtParts) do
                                part.Position = Vector3.new(0, -200, 0)
                            end
                        end
                    end
                end
            end
        end
    })

    local Section = TPTab:AddSection({Name = "Players"})

    local TPDrop = TPTab:AddDropdown({
        Name = "Select player",
        Default = "",
        Options = TeleportToPlayersDropdown(),
        Callback = function(plrname)
            TpToPlayer = game.Players:FindFirstChild(plrname)
            TpTrgtPos = TpToPlayer.Character.HumanoidRootPart.Position
        end    
    })

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            if TpTrgtPos then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(TpTrgtPos)
            end
    end
    })

    local Section = TPTab:AddSection({Name = "Spawn"})

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(2, -7, -4)
        end
    })

    local Section = TPTab:AddSection({Name = "Houses"})

    TPTab:AddDropdown({
        Name = "Select house",
        Default = "Not selected",
        Options = {"Yellow house", "Green house", "Purple house", "China house", "Blue house"},
        Callback = function(Value)
            houseSelected = Value
        end
    })

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            if houseSelected == "Yellow house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-492, -7, -164)
            elseif houseSelected == "Green house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-532, -7, 95)
            elseif houseSelected == "Purple house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(255, -7, 465)
            elseif houseSelected == "China house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(558, 123, -76)
            elseif houseSelected == "Blue house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(511, 83, -344)
            end
        end    
    })

    local Section = TPTab:AddSection({Name = "Other builds"})

    TPTab:AddDropdown({
        Name = "Select build",
        Default = "Not selected",
        Options = {"Big house", "Farm", "Snow mountain", "Default mountain", "Flying island"},
        Callback = function(Value)
            buildSelected = Value
        end
    })

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            if buildSelected == "Big house" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-244, 80, 293)
            elseif buildSelected == "Farm" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-197, 59, -285)
            elseif buildSelected == "Snow mountain" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-433, 230, 516)
            elseif buildSelected == "Default mountain" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(394, 163, 278)
            elseif buildSelected == "Flying island" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(71, 346, 330)
            end
        end    
    })

    local Section = TPTab:AddSection({Name = "Secrets"})

    TPTab:AddDropdown({
        Name = "Select secret",
        Default = "Not selected",
        Options = {"Big secret exit 1", "Big secret exit 2", "Small secret", "Big secret in the cave", "Flying island secret", "Mountain secret"},
        Callback = function(Value)
            secretSelected = Value
        end
    })

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            if secretSelected == "Big secret exit 1" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(59, 37, 485)
            elseif secretSelected == "Big secret exit 2" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-321, 20, 466)
            elseif secretSelected == "Small secret" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-9, 32, -259)
            elseif secretSelected == "Big secret in the cave" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(588, 83, -181)
            elseif secretSelected == "Flying island secret" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(116, 296, 335)
            elseif secretSelected == "Mountain secret" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-362, -7, -344)
            end
        end    
    })

    local Section = TPTab:AddSection({Name = "Slots"})

    TPTab:AddDropdown({
        Name = "Slot selected",
        Default = "Not selected",
        Options = {"Slot 1 (spawn)", "Slot 2 (yellow and green houses)", "Slot 3 (big house)", "Slot 4 (purple house)", "Slot 5 (blue house)"},
        Callback = function(Value)
            slotSelected = Value
        end
    })

    TPTab:AddButton({
        Name = "Teleport",
        Callback = function()
            if slotSelected == "Slot 1 (spawn)" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(66, -7, -110)
            elseif slotSelected == "Slot 2 (yellow and green houses)" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-531, -7, -48)
            elseif slotSelected == "Slot 3 (big house)" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-227, 83, 418)
            elseif slotSelected == "Slot 4 (purple house)" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(169, -7, 513)
            elseif slotSelected == "Slot 5 (blue house)" then
                game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(554, 83, -229)
            end
        end    
    })

    AmountOfPlayers = #Players:GetPlayers()
    allPlrs = #Players:GetPlayers()

    local CounOfPlayersLbl = ServerInfoTab:AddLabel("Count of players: "..AmountOfPlayers.."")
    local AllPlayersLbl = ServerInfoTab:AddLabel("All players: "..allPlrs.."")

    local Section = AutoPianoTab:AddSection({Name = "Play"})

    AutoPianoTab:AddDropdown({
        Name = "Choose a song",
        Default = "Happy birthday",
        Options = {"Happy birthday", "Jingle bells (soon)", "Was wollen wir trinken (soon)"},
        Callback = function(Value)
            songSelected = Value
        end    
    })

    AutoPianoTab:AddButton({
        Name = "Play",
        Callback = function()
            if songSelected == "Happy birthday" then
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0,-0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2A,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(1)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2A,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.9)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2C,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2A,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2C,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2C,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.5)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2C,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.8)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2A,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.7)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                wait(0.9)
                local args = {
                    [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                    [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                }
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
            elseif songSelected == "Jingle bells (soon)" then
                OrionLib:MakeNotification({
                    Name = "This song has not been released yet",
                    Content = "Wait until the next update",
                    Image = "rbxassetid://4483345998",
                    Time = 5
                })
            end
        end    
    })

    AutoPianoTab:AddToggle({
        Name = "Spam",
        Default = false,
        Color = Color3.fromRGB(102, 0, 102),
        Callback = function(Value)
            SpamToggle = Value
            if Value then
                while SpamToggle == true do
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1C,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1D,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1E,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1F,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key1G,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2A,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2B,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2C,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2D,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2E,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2F,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key2G,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key3A,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key3B,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                    local args = {
                        [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Key3C,
                        [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
                    }
                    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
                    wait()
                end
            end
        end    
    })

    local Section = AutoPianoTab:AddSection({Name = "Spawn"})

    AutoPianoTab:AddButton({
        Name = "Spawn piano",
        Callback = function()
            local args = {
                [1] = "MusicKeyboard",
                [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0),
                [3] = Vector3.new(0, -0, 0)
            }
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
            wait()
            local args = {
                [1] = workspace:FindFirstChild(SpawnedInToys).MusicKeyboard.Part,
                [2] = CFrame.new(pos) * CFrame.Angles(-0, 0, -0)
            }
            game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(unpack(args))
        end    
    })

    local Section = ScriptTab:AddSection({Name = "Default"})

    ScriptTab:AddButton({
        Name = "Infinite yield",
        Callback = function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
        end    
    })

    ScriptTab:AddButton({
        Name = "SystemBroken",
        Callback = function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/H20CalibreYT/SystemBroken/main/script"))()
        end    
    })

    ScriptTab:AddButton({
        Name = "Float",
        Callback = function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/GhostPlayer352/Test4/main/Float"))("https://t.me/arceusxscripts")
        end    
    })

    ScriptTab:AddButton({
        Name = "Shaders",
        Callback = function()
            loadstring(game:HttpGet(("https://pastefy.app/xXkUxA0P/raw"),true))("t.me/arceusxscripts")
        end    
    })

    ScriptTab:AddButton({
        Name = "Dex Explorer v2",
        Callback = function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/MariyaFurmanova/Library/main/dex2.0", true))()
        end    
    })

    Players.PlayerAdded:Connect(function()
        TPDrop:Refresh(TeleportToPlayersDropdown(), true)
        DuoBlobDrop:Refresh(DuoBlobPlayersDropdown(), true)
        LeftBlobDrop:Refresh(LeftBlobPlayersDropdown(), true)
        RightBlobDrop:Refresh(RightBlobPlayersDropdown(), true)
        TargetDrop:Refresh(TargetPlayersDropdown(), true)
        ExplodePlayerDrop:Refresh(ExplodePlayerDropdown(), true)
        WhitelistPlayerDrop:Refresh(WhitelistPlayerDropdown(), true)
        AmountOfPlayers = AmountOfPlayers + 1
        CounOfPlayersLbl:Set("Count of players: "..AmountOfPlayers.."")
        allPlrs = allPlrs + 1
        AllPlayersLbl:Set("Count of players: "..allPlrs.."")
    end)

    Players.PlayerRemoving:Connect(function()
        TPDrop:Refresh(TeleportToPlayersDropdown(), true)
        DuoBlobDrop:Refresh(DuoBlobPlayersDropdown(), true)
        LeftBlobDrop:Refresh(LeftBlobPlayersDropdown(), true)
        RightBlobDrop:Refresh(RightBlobPlayersDropdown(), true)
        TargetDrop:Refresh(TargetPlayersDropdown(), true)
        ExplodePlayerDrop:Refresh(ExplodePlayerDropdown(), true)
        WhitelistPlayerDrop:Refresh(WhitelistPlayerDropdown(), true)
        AmountOfPlayers = AmountOfPlayers - 1
        CounOfPlayersLbl:Set("Count of players: "..AmountOfPlayers.."")
    end)

    wait()
    OrionLib:MakeNotification({
        Name = HubName.." loaded",
        Content = "completely",
        Image = "",
        Time = 5
    })
else
    game.Players.LocalPlayer:Kick("do not use this script")
end
