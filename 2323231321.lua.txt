

    warn([[
        ----------------------------------------------
        ##   ##  ##   ##  ###               ### 
        ###  ##  ###  ##   ##                ## 
        #### ##  #### ##   ##      ##  ##    ## 
        ## ####  ## ####   #####   ##  ##    ##### 
        ##  ###  ##  ###   ##  ##  ##  ##    ##  ## 
        ##   ##  ##   ##   ##  ##  ##  ##    ##  ## 
        ##   ##  ##   ##  ###  ##   ######  ###### ...
        ----------------------------------------------
    ]])
    local StartTick1 = tick()

    -- Services -- 
    local Players = game:GetService("Players")
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local UIS = game:GetService("UserInputService")
    local Debris = game:GetService("Debris")
    local TextChatService = game:GetService("TextChatService")
    local Lighting = game:GetService("Lighting")
    -- LocalPlayer Locals -- 
    local plr = Players.LocalPlayer
    local mouse = plr:GetMouse()
    local char = plr.Character
    local hum = char:FindFirstChild("Humanoid") or char:WaitForChild("Humanoid")
    local hrp = char:FindFirstChild("HumanoidRootPart") or char:WaitForChild("HumanoidRootPart")
    local head = char:FindFirstChild("Head") or char:WaitForChild("Head")
    local IsHeld = plr.IsHeld
    local CanSpawnToy = plr.CanSpawnToy
    local InPlot = plr.InPlot
    local InOwnedPlot = plr.InOwnedPlot
    local AntiLineLag = plr.PlayerScripts.CharacterAndBeamMove
    local AntiShuriLag = plr.PlayerScripts.StickyPartsTouchDetection
    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys") or workspace:WaitForChild(plr.Name.."SpawnedInToys")
    -- Remotes --
    local SetNetworkOwner = RS.GrabEvents:FindFirstChild("SetNetworkOwner") or RS.GrabEvents:WaitForChild("SetNetworkOwner")
    local StickyEvent = RS.PlayerEvents:FindFirstChild("StickyPartEvent") or RS.PlayerEvents:WaitForChild("StickyPartEvent")
    local DestroyToy = RS.MenuToys:FindFirstChild("DestroyToy") or RS.MenuToys:WaitForChild("DestroyToy")
    local Struggle = RS.CharacterEvents:FindFirstChild("Struggle") or RS.CharacterEvents:WaitForChild("Struggle")
    local CreateGrabLine = RS.GrabEvents:FindFirstChild("CreateGrabLine") or RS.GrabEvents:WaitForChild("CreateGrabLine")
    local SetLineColor = RS.DataEvents:FindFirstChild("UpdateLineColorsEvent") or RS.DataEvents:WaitForChild("UpdateLineColorsEvent")
    local SpawnToyRemote = RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction") or RS.MenuToys:WaitForChild("SpawnToyRemoteFunction")
    local DestroyGrabLine = RS.GrabEvents:FindFirstChild("DestroyGrabLine") or RS.GrabEvents:WaitForChild("DestroyGrabLine")
    local RagdollRemote = RS.CharacterEvents:FindFirstChild("RagdollRemote") or RS.CharacterEvents:WaitForChild("RagdollRemote")

    -- Toggles and etc. --
    local MyPCLD = nil  
    local cons = {}
    local bool = {}
    local int = {}
    local etc = {
        MyPCLD = nil,
        TargetPLR = nil,
        Root = nil,
        Head = nil,
        Torso = nil,
        Hum = nil,
        TargetChar = nil,
        Limbs = {
            "Left Arm",
            "Left Leg",
            "Right Arm",
            "Right Leg"
        },
        MapPoints = {
            ["🟩Green House🟩"] = CFrame.new(-548.305054, -2.45424771, 79.3213348),
            ["🩷Pink House🩷"] = CFrame.new(-475.493835, -2.70774508, -159.395279),
            ["🦇Witch House🦇"] = CFrame.new(270.225922, -2.48055029, 458.186493),
            ["🟦Blue House🟦"] = CFrame.new(501.939911, 88.2323608, -349.129211),
            ["🀄China House💮"] = CFrame.new(545.441833, 128.004593, -99.4881439)
        },
        LastBlob = nil,
        LastTrainSeat = nil
    }
    -- Functions --
    local function GetMagnitude(Part1: Instance, Part2: Instance)
        return (Part1.Position - Part2.Position).Magnitude
    end

    local function FWD(parent,part,time)
        return parent:FindFirstChild(part) or parent:WaitForChild(part,time)
    end

    local function HasProperty(obj, property)
        local ok = pcall(function() if obj[property] then end end)
        return ok
    end

    local function CFP(parent,part)
        return parent:FindFirstChild(part) ~= nil  
    end

    function cons:disc(con)
        if cons[con] then 
            cons[con]:Disconnect()
            cons[con] = nil
        end 
    end
    function cons:discAll()
        for _,v in pairs(cons) do 
            v:Disconnect()
        end 
        cons = {}
    end
    function cons:cancel(coroutine)
        if cons[coroutine] then 
            task.cancel(cons[coroutine])
            cons[coroutine] = nil
        end 
    end 
    local function FindPCLD()
        cons:disc("FindPCLDCon")
        cons["FindPCLDCon"] = RunService.Heartbeat:Connect(function()
            if etc.MyPCLD then 
                cons:disc("FindPCLDCon")
            end
            for _, v in pairs(workspace:GetChildren()) do 
                if v.Name == "PlayerCharacterLocationDetector" and GetMagnitude(v,hrp) <= 2 then 
                    etc.MyPCLD = v
                    break
                end
            end
        end)
    end

    local function OnCharAdded(character)
        char = character
        etc["MyPCLD"] = nil
        cons:disc("AntiMassless")
        cons:disc("ThirdpFix")
        head = FWD(char,"Head")
        hrp = FWD(char,"HumanoidRootPart")
        hum = FWD(char,"Humanoid")
        cons["AntiMassless"] = hrp:GetPropertyChangedSignal("Massless"):Connect(function()
            if (hrp.Massless) and (bool.AntiVoidEnabled) then 
                hrp.Massless = false 
            end
        end)
        cons["ThirdpFix"] = head:GetPropertyChangedSignal("Transparency"):Connect(function()
            if head.Transparency == 1 then 
                task.wait(0.1)
                for _,v in pairs(char:GetChildren()) do 
                    if (v:IsA("Accessory") and v.Name ~= "TypingKeyboardMyWorld") or v:IsA("Hat") then 
                        v.Handle.Transparency = 0
                    elseif v:IsA("BasePart") and v ~= hrp then  
                        v.Transparency = 0
                    end
                end
            end
        end)
        task.spawn(FindPCLD)
        hrp.CFrame = hrp.CFrame * CFrame.new(0,1,0)
        FWD(hrp,"Scream"):Destroy()
    end
    local function toy_delete(toy) DestroyToy:FireServer(toy) end
    local function sno(part) SetNetworkOwner:FireServer(part,part.CFrame) end

    local function unsno(part) DestroyGrabLine:FireServer(part) end

    local function line(part) CreateGrabLine:FireServer(part,part.CFrame) end

    local function CheckNetworkOwnerOnPart(Part) 
        return CFP(Part,"PartOwner") and Part["PartOwner"].Value == plr.Name
    end

    local function CheckNetworkOwnerOnPlayer(TargetPlr,Root) 
        if Root then
            local Head = Root.Parent and Root.Parent:FindFirstChild("Head")
            return Head and CheckNetworkOwnerOnPart(Head)
        else 
            TargetChar = TargetPlr.Character
            TargetRoot = TargetChar and TargetChar.Parent and TargetChar:FindFirstChild("Head")
            return TargetRoot and CheckNetworkOwnerOnPart(TargetRoot)
        end
    end

    local function FindBlob()
        if etc["LastBlob"] == nil or not CFP(etc["LastBlob"],"VehicleSeat") or etc["LastBlob"].VehicleSeat.Occupant ~= hum then 
            etc["LastBlob"] = hum.SeatPart and hum.SeatPart:FindFirstAncestor("CreatureBlobman")
        end 
        return etc["LastBlob"]
    end

    local function StopAllVelocity(parent)
        local ToStopPart = parent.PrimaryPart or parent
        ToStopPart.AssemblyLinearVelocity = Vector3.zero
        ToStopPart.AssemblyAngularVelocity = Vector3.zero
    end

    local function StopVelocityF()
        if hrp then 
            hrp.AssemblyLinearVelocity = Vector3.zero
        end 
    end 

    local function Notify(Title,SubTitle)
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = Title,
            Text = SubTitle,
            Duration = 5,
        })
    end

    local function ToCFrame(Vectorbf:Vector3)
        return CFrame.new(Vectorbf)
    end


    local function CheckForHome()
        if etc["LastHouse"] == nil or etc["LastPlotOwner"] == nil or etc["LastPlotOwner"].Parent == nil then 
            for i = 1,5 do 
                local Plot = workspace.Plots["Plot"..i]
                for _,v in pairs(Plot.PlotSign["ThisPlotsOwners"]:GetChildren()) do 
                    if v.Value == plr.Name then 
                        etc["LastHouse"] = workspace.PlotItems["Plot"..i]
                        etc["LastPlotOwner"] = v 
                        return etc["LastHouse"]
                    end
                end
            end
        end
        return etc["LastHouse"]
    end

    local function Huge() 
        return math.huge,math.huge,math.huge
    end

    -- local function SpawnToy(ToyName: string)
    --     if InPlot.Value and not InOwnedPlot.Value then 
    --         InPlot:GetPropertyChangedSignal("Value"):Wait()
    --     end 
    --     if not CanSpawnToy.Value then 
    --         CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
    --     end
    --     local SpawnCF = (etc.MyPCLD or hrp).CFrame * CFrame.new(0,14,20)
    --     task.spawn(SpawnToyRemote.InvokeServer, SpawnToyRemote, ToyName, SpawnCF, Vector3.zero)
    --     if InOwnedPlot.Value then 
    --         local Plot = CheckForHome()
    --         return Plot and FWD(Plot,ToyName,2.5)
    --     else 
    --         return FWD(inv,ToyName,2.5)
    --     end
    -- end
    local function SpawnToy(ToyName: string)
    if InPlot.Value and not InOwnedPlot.Value then 
        InPlot:GetPropertyChangedSignal("Value"):Wait()
    end 
    if not CanSpawnToy.Value then 
        CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
    end

    local SpawnCF = (etc.MyPCLD or hrp).CFrame * CFrame.new(0, 14, 20)
    
    local Container = InOwnedPlot.Value and CheckForHome() or inv
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
            SpawnToyRemote:InvokeServer(ToyName, SpawnCF, Vector3.zero)
        end)
    end)

    local start = tick()
    repeat task.wait() until spawnedObject or (tick() - start) > 2.5

    connection:Disconnect()
    return spawnedObject
end

    local function BringRight(k)
        local v = FindBlob()
        local Root = Players[k].Character:FindFirstChild("HumanoidRootPart")
        if not Root or not v then return end
        v.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(v.RightDetector, Root, v.RightDetector.RightWeld)
    end

    local function DropRight(k)
        local v = FindBlob()
        local Root = Players[k].Character:FindFirstChild("HumanoidRootPart")
        if not Root or not v then return end
        v.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(v.RightDetector.RightWeld, Root)
    end

    local function BugRight(k)
        local v = FindBlob()
        local Root = Players[k].Character:FindFirstChild("HumanoidRootPart")
        if not Root or not v then return end
        v.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(v.RightDetector.RightWeld, Root)
    end

    local function BuildPlayerOptions()
        local options = {}
        local map = {}
        
        for _, player in ipairs(Players:GetPlayers()) do
            local display = string.format(
                '<font color="rgb(255,0,0)"><b>%s</b></font> <b><i>(%s)</i></b>',
                player.Name,
                player.DisplayName
            )   
            table.insert(options, display)
            map[display] = player
        end
        
        return options, map
    end

    local function ChangeCollision(obj,val)
        for _,v in pairs(obj:GetChildren()) do 
            if v:IsA("BasePart") and v.Name ~= "GrabParts" then  
                v.CanCollide = val
            end
        end
    end

    local function createLagWithGrabLine()
        for _, object in ipairs(workspace:GetDescendants()) do
            if object:IsA("BasePart") and object.Parent ~= plr.Character then
                local cf = object.CFrame * CFrame.new(
                    math.random(-int.lagIntensity, int.lagIntensity),
                    1e9,
                    math.random(-int.lagIntensity, int.lagIntensity)
                ) * CFrame.Angles(
                    math.rad(math.random(0, 360)),
                    math.rad(math.random(0, 360)),
                    math.rad(math.random(0, 360))
                )
                CreateGrabLine:FireServer(object, cf)
            end
        end
    end
    int.PCLDColor = Color3.fromRGB(0, 255, 255)
    local function AddESP(obj)
        if obj.Name == "PlayerCharacterLocationDetector" and not obj:FindFirstChild("PCLD_ESP") then
            local adorn = Instance.new("BoxHandleAdornment")
            adorn.Name = "PCLD_ESP"
            adorn.Size = obj.Size
            adorn.AlwaysOnTop = true
            adorn.ZIndex = 10
            adorn.Color3 = int.PCLDColor
            adorn.Transparency = 0.75
            adorn.Adornee = obj
            adorn.Parent = obj
        end
    end

    local function RemoveESP(obj)
        if obj.Name == "PlayerCharacterLocationDetector" then
            local esp = obj:FindFirstChild("PCLD_ESP")
            if esp then
                esp:Destroy()
            end
        end
    end

    local function lag()
        local oldCF = char:GetPivot()

        if bool.ShurikenLagServerT then
            local ToyFolder = CheckForHome()
            if not ToyFolder then return end
            
            local decoys = {}
            local shurikens = {}

            for _, obj in pairs(ToyFolder:GetChildren()) do
                if obj:IsA("Model") then
                    if obj.Name == "YouDecoy" then

                        repeat 
                            char:PivotTo(obj.HumanoidRootPart.CFrame * CFrame.new(0,10,0))
                            sno(obj.HumanoidRootPart)
                            task.wait(0.02)
                        until obj.Head:FindFirstChild("PartOwner") or not bool.ShurikenLagServerT
                        
                        table.insert(decoys, obj)
                    elseif obj.Name == "NinjaShuriken" then
                        
                        for _, child in pairs(obj.StickyPart:GetChildren()) do
                            if child.Name == "TouchInterest" then
                                child:Destroy()
                            end
                        end
                        
                        task.wait()

                        local tick1 = tick()
                        repeat
                            char:PivotTo(obj.SoundPart.CFrame * CFrame.new(0,10,0))
                            sno(obj.SoundPart)
                            task.wait(0.02)
                        until obj.SoundPart:FindFirstChild("PartOwner") or not bool.ShurikenLagServerT or (tick() - tick1 >= 5)
                        
                        table.insert(shurikens, obj)
                    end
                end
            end
            
            char:PivotTo(oldCF)
            local maxshurikensperdecoy = 5

            for decoyindex, decoy in ipairs(decoys) do
                local decoyHRP = decoy:FindFirstChild("HumanoidRootPart")
                if decoyHRP and bool.ShurikenLagServerT then
                    decoyHRP.Anchored = true
                    local startindex = (decoyindex - 1) * maxshurikensperdecoy + 1
                    local endindex = startindex + maxshurikensperdecoy - 1
                    
                    for shurikenindex = startindex, endindex do
                        local shuriken = shurikens[shurikenindex]
                        if not shuriken then
                            break
                        end
                        
                        local StickyPart = shuriken:FindFirstChild("StickyPart")
                        if StickyPart then
                            StickyPart.CanTouch = true
                            
                            for _, part in pairs(decoy:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                            
                            local BodyPosition = Instance.new("BodyPosition")
                            BodyPosition.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            BodyPosition.P = 10000
                            BodyPosition.D = 500
                            BodyPosition.Parent = StickyPart
                            
                            for _, part in pairs(shuriken:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                            
                            for _, child in pairs(StickyPart:GetChildren()) do
                                if child.Name == "TouchInterest" then
                                    child:Destroy()
                                end
                            end
                            
                            task.defer(function()
                                repeat
                                    StickyPart.AssemblyAngularVelocity = Vector3.new(
                                        math.random(-100, 100) * 50,
                                        math.random(-100, 100) * 50,
                                        math.random(-100, 100) * 50
                                    )
                                    BodyPosition.Position = Vector3.new(
                                        decoyHRP.Position.X,
                                        decoyHRP.Position.Y - 4,
                                        decoyHRP.Position.Z
                                    )
                                    wait(0.0001)
                                    BodyPosition.Position = Vector3.new(
                                        decoyHRP.Position.X,
                                        decoyHRP.Position.Y + 3,
                                        decoyHRP.Position.Z
                                    )
                                    wait(0.0001)
                                until not bool.ShurikenLagServerT or not shuriken.Parent or not decoy.Parent
                            end)
                        end
                        wait()
                    end
                end
                wait()
            end
        end
    end

    local function RenameInShop(Instance,OriginalName,ToRename)
        local index = nil
        local ToCheckName = Instance.Name or Instance
        for i,v in pairs(inv:GetChildren()) do if v.Name == ToCheckName then index = i end end
        if index == nil then return end 
        local contents = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
        for i,v in ipairs(contents:GetChildren()) do
            if v.Name == OriginalName and i == index then
                local view = v.ViewItemButton
                view.Text = ToRename
                view.TextScaled = true
                view.LowResImage.Image = ""
            end
        end
    end

    loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()



    local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/CoolBoogie/MyScript/refs/heads/main/OrionLibTest')))()
    -- local OrionLib = loadstring(readfile("OrionLiv.txt"))()
    -- local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/Jeffepicy/OrionGod/refs/heads/main/CosmicOrion')))()
    local Window = OrionLib:MakeWindow({
        Name = "NoName", 
        HidePremium = false, 
        SaveConfig = true, 
        ConfigFolder = "NNhub", 
        IntroEnabled = true, 
        IntroText = "NNhub",
        Keybind = "K",
        FreeMouse = true,
    })


    -- local AlternativeIcons = {
    --     ["shield"] = "rbxassetid://6035067833",
    --     ["hand-grab"] = "rbxassetid://6035067856",
    --     ["user"] = "rbxassetid://6035067867", 
    --     ["crosshair"] = "rbxassetid://6035067878",
    --     ["keyboard"] = "rbxassetid://6035067889",
    --     ["wallpaper"] = "rbxassetid://6035067900",
    --     ["ellipsis"] = "rbxassetid://6035067911",
    --     ["crown"] = "rbxassetid://6035067922"
    -- }
    local DefTab = Window:MakeTab({
        Name = "<font size = \"18\">Defense</font>",
        Icon = "shield",
        PremiumOnly = false
    })

    local GrabTab = Window:MakeTab({
        Name = "<font size = \"18\">Grabs</font>",
        Icon = "hand",
        PremiumOnly = false
    })

    local PlrTab = Window:MakeTab({
        Name = "<font size = \"18\">Player</font>",
        Icon = "user",
        PremiumOnly = false
    })

    local TarTab = Window:MakeTab({
        Name = "<font size = \"18\">Target</font>",
        Icon = "crosshair",
        PremiumOnly = false
    })
    local KeybindTab = Window:MakeTab({
        Name = "<font size = \"18\">Keybinds</font>",
        Icon = "keyboard",
        PremiumOnly = false
    })  
    local VisualTab = Window:MakeTab({
        Name = "<font size = \"18\">Visual</font>",
        Icon = "monitor",
        PremiumOnly = false
    })

    local MiscTab = Window:MakeTab({
        Name = "<font size = \"18\">Misc</font>",
        Icon = "circle-ellipsis",
        PremiumOnly = false
    })

    local OwnTab = Window:MakeTab({
        Name = "<font size = \"18\">Owner</font>",
        Icon = "crown",
        PremiumOnly = false
    })
    local ConfigTab = Window:MakeTab({
        Name = "<font size = \"18\">Settings</font>",
        Icon = "settings",
        PremiumOnly = false
    })
    local Toggles = {}


    -- DEFENSE --
    Toggles["AntiGrab"] = DefTab:AddToggle({
        Name = "AntiGrab<font color=\"rgb(255, 165, 0)\"><b>[BASE]</b></font>",
        Default = false,
        Save = true,
        Flag = "AntiGrab",
        Callback = function(Value)
            if Value then 
                cons["AntiGrab1Con"] = IsHeld:GetPropertyChangedSignal("Value"):Connect(function()
                    if IsHeld.Value then 
                        hrp.Anchored = true 
                        Struggle:FireServer("Unbind")
                        cons:disc("AntiGrabSpawned")
                        cons["AntiGrabSpawned"] = RunService.Heartbeat:Connect(function()
                            Struggle:FireServer("Unbind")
                            StopVelocityF()
                            hrp.Anchored = IsHeld.Value
                            if not bool.WalkSpeed then 
                                hrp.CFrame = hrp.CFrame + (hum.MoveDirection * 0.5)
                            end
                            hum.Sit = false 
                            hum.AutoRotate = true
                        end)
                        IsHeld:GetPropertyChangedSignal("Value"):Wait()
                        if not(IsHeld.Value) then
                            RagdollRemote:FireServer(hrp,0)
                            hrp.Anchored = CFP(head,"PartOwner")
                            cons:disc("AntiGrabSpawned")
                        end
                    end 
                end)
            else 
                cons:disc("AntiGrab1Con")
                cons:disc("AntiGrabSpawned")
                hrp.Anchored = false
            end
        end    
    })
    DefTab:AddDropdown({
        Name = "GUCCI Method",
        Default = "CreatureBlobman",
        Options = {"CreatureBlobman", "TractorGreen"},
        Callback = function(Value)
            int.GucciMethod = Value
        end    
    })

    DefTab:AddButton({
        Name = "AntiGrab<font color=\"rgb(0, 180, 0)\"><b>[GUCCI]</b></font>",
        Callback = function()
            local blob,VehicleSeat
            blob = SpawnToy(int.GucciMethod)
            if not blob then return end 
            blob.Name = "AntiGucciBlob"
            task.delay(1,RenameInShop,blob,int.GucciMethod,"GucciThing")
            VehicleSeat = FWD(blob,"VehicleSeat") 
            if not VehicleSeat then DestroyToy:FireServer(blob) return end 
            VehicleSeat:Sit(hum)
            task.spawn(function()
                local EndTime = tick() + 0.5
                while tick() < EndTime and task.wait() do 
                    RagdollRemote:FireServer(hrp,0)
                end
            end)
            while not hum.SeatPart or IsHeld.Value do task.wait() end 
            task.wait()
            hum.Sit = false
            task.wait()
            blob:PivotTo(CFrame.new(0,1e3,0))
            VehicleSeat.CFrame = CFrame.new(0,1e3,0)
            do 
                local BodyPos = Instance.new("BodyPosition")
                BodyPos.P = 10000
                BodyPos.D = 4000
                BodyPos.MaxForce = Vector3.new(Huge())
                BodyPos.Parent = VehicleSeat
                BodyPos.Position = Vector3.new(0,1e5,0)
            end
        end
    })

    DefTab:AddButton({
        Name = "Gucci<font color=\"rgb(255, 215, 0)\"><b>[TRAIN]</b></font>",
        Callback = function()
            local oldCF = char:GetPivot()
            local Train,Seat
            Train = workspace.Map.AlwaysHereTweenedObjects.Train.Object:FindFirstChild("ObjectModel")
            if not Train then return end 
            for _,SeatRandom in pairs(Train:GetChildren()) do 
                if SeatRandom.Name == "Seat" and SeatRandom.Occupant == nil and SeatRandom ~= etc.LastSeat then 
                    etc.LastSeat = SeatRandom 
                    Seat = SeatRandom
                    break 
                end                 
            end
            if not Seat then return end 
            Seat:Sit(hum)
            task.spawn(function()
                local endTime = tick() + 0.5
                while tick() < endTime and task.wait() do
                    game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                end
            end)
            task.wait()
            while not hum.SeatPart or IsHeld.Value do task.wait() end
            hum.Sit = false
            task.wait()
            for _ = 1,3 do 
                char:PivotTo(oldCF)
            end
        end    
    })
    DefTab:AddButton({
        Name = "DestroyGucci",
        Callback = function()
            plr.IsHeld.Value = false  
            for i =  1,100 do 
                hum.Sit = true
            end
            task.wait(0.1)
            hum.Sit = false 
        end    
    })
    DefTab:AddToggle({
        Name = "Auto-Gucci<font color=\"rgb(255, 0, 0)\"><b>[Buggyyy!11]</b></font>",
        Default = false,
        Callback = function(Val)
            bool.AutoGucci = Val
            if Val then
                -- local GucciThing
                -- local function gucci()
                --     if not bool.AutoGucci then return end 
                --     bool.IsActive = true
                --     local oldCF = char:GetPivot()
                --     if GucciThing then
                --         cons:disc("OnceDestroying")
                --         for _,v in pairs(inv:GetChildren()) do
                --             if v.Name == "AutoGucci" then 
                --                 DestroyToy:FireServer(v)
                --             end
                --         end
                --     end 
                --     IsHeld.Value = head:FindFirstChild("PartOwner") ~= nil
                --     local ragdolled = FWD(hum, "Ragdolled")
                --     while ragdolled.Value or IsHeld.Value do task.wait() end 
                --     for _ = 1,100 do 
                --         hum.Sit = true
                --     end 
                --     task.wait(0.1)
                --     hum.Sit = false
                --     GucciThing = SpawnToy("TractorGreen")
                --     while not GucciThing and task.wait(0.25) do 
                --         GucciThing = SpawnToy("TractorGreen")
                --     end
                --     GucciThing.Name = "AutoGucci"
                --     FWD(GucciThing,"VehicleSeat"):Sit(hum)
                --     task.spawn(function()
                --         local endTime = tick() + 0.5
                --         while tick() < endTime and bool.IsActive and task.wait() do
                --             game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                --         end
                --     end)
                --     while not(hum.SeatPart or IsHeld.Value) and task.wait() do GucciThing.VehicleSeat:Sit(hum) end
                --     hum.Sit = false
                --     task.delay(0.05,function()
                --         GucciThing:PivotTo(CFrame.new(0,1e6,0))
                --         local BodyPos = Instance.new("BodyPosition")
                --         BodyPos.Position = Vector3.new(0,1e6,0)
                --         BodyPos.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
                --         BodyPos.Parent = GucciThing.PrimaryPart
                --     end)
                --     cons["OnceDestroying"] = GucciThing.Destroying:Once(gucci)
                --     bool.IsActive = false
                -- end
                local GucciThing
                local function gucci()
                    if not bool.AutoGucci then return end 
                    bool.IsActive = true
                    if GucciThing then
                        cons:disc("OnceDestroying")
                        for _, v in pairs(inv:GetChildren()) do
                            if v.Name == "AutoGucci" or v.Name == "TractorGreen" then 
                                DestroyToy:FireServer(v)
                            end
                        end
                    end 

                    IsHeld.Value = head:FindFirstChild("PartOwner") ~= nil
                    local ragdolled = FWD(hum, "Ragdolled")
                    while ragdolled.Value or IsHeld.Value do task.wait() end 
                    for _ = 1,100 do 
                        hum.Sit = true
                    end 
                    task.wait(0.1)
                    hum.Sit = false 
                    GucciThing = SpawnToy("TractorGreen")
                    while not GucciThing do 
                        task.wait(0.25)
                        GucciThing = SpawnToy("TractorGreen")
                    end
                    
                    GucciThing.Name = "AutoGucci"
                    local seat = FWD(GucciThing, "VehicleSeat")
                    cons:cancel("SPAAAM")
                    cons["SPAAAM"] = task.spawn(function()
                        local endTime = tick() + 0.5
                        while tick() < endTime and task.wait() and bool.IsActive do
                            game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                        end
                    end)
                    local lastSitAttempt = 0
                    while not (hum.SeatPart or IsHeld.Value) do
                        if tick() - lastSitAttempt > 0.1 then
                            seat:Sit(hum)
                            lastSitAttempt = tick()
                        end
                        task.wait() 
                    end
                    hum.Sit = false
                    hum:ChangeState(Enum.HumanoidStateType.Jumping)
                    hrp.Anchored = true 
                    task.spawn(function()
                        repeat task.wait() until not(CFP(GucciThing.VehicleSeat,"SeatWeld"))
                        GucciThing:PivotTo(CFrame.new(0, 1e6, 0))
                        local BodyPos = Instance.new("BodyPosition")
                        BodyPos.Position = Vector3.new(0, 1e6, 0)
                        BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        BodyPos.Parent = GucciThing.PrimaryPart
                    end)
                    hrp.Anchored = false
                    cons["OnceDestroying"] = GucciThing.Destroying:Once(gucci)
                    bool.IsActive = false
                end
                gucci()
                cons["GucciChecker"] = RunService.Heartbeat:Connect(function()
                    if not bool.AutoGucci then 
                        cons:disc("GucciChecker")
                        return 
                    end
                    if bool.IsActive then return end 
                    if not (hrp and hum) or hum.Health == 0 then 
                        bool.IsActive = true
                        local newChar = plr.CharacterAdded:Wait()
                        hrp = newChar:WaitForChild("HumanoidRootPart")
                        hum = newChar:WaitForChild("Humanoid")
                        
                        task.wait(0.5)
                        gucci()
                        return
                    end
                    if not bool.IsActive then
                        if not isnetworkowner(hrp) or IsHeld.Value or hum.Sit then 
                            gucci()
                        end
                    end
                end)
            end
        end
    })

    DefTab:AddButton({
        Name = "Delete Legs 🦵",
        Callback = function()
            local ll,rl = char:FindFirstChild("Left Leg"), char:FindFirstChild("Right Leg")
            if not ll or not rl then return end 
            local oldCF = char:GetPivot()
            local oldFal = workspace.FallenPartsDestroyHeight
            workspace.FallenPartsDestroyHeight = -50000
            RagdollRemote:FireServer(hrp, 1)
            task.wait(0.5)
            rl.CFrame = CFrame.new(0, -60000, 0)
            ll.CFrame = CFrame.new(0, -60000, 0)
            task.wait(0.1)
            char:PivotTo(CFrame.new(0, -55970, 0))
            task.wait(0.1)
            char:PivotTo(oldCF)
            workspace.FallenPartsDestroyHeight = oldFal
            task.delay(0.3,function()
                while task.wait() do 
                    if not hum or hum.Health == 0 or char:FindFirstChild("Right Leg") then break end
                    if plr.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                        hum.HipHeight = 2
                    else
                        hum.HipHeight = 0
                    end
                end
            end)
        end    
    })

    DefTab:AddToggle({
        Name = "Struggle SPAM(AntiGrab/AntiBlob)",
        Default = false,
        Callback = function(Val)
            bool.LoopStruggle = Val
            while bool.LoopStruggle and task.wait() do  
                Struggle:FireServer("Unbind")
            end
        end
    })



    Toggles["AntiExplodeT"] = DefTab:AddToggle({
        Name = "AntiExplode",
        Default = false,
        Save = true,
        Flag = "AntiExplodeT",
        Callback = function(Val)
            bool.AntiExplodeT = Val 
            if Val then 
                cons["AntiExplodeCon"] = RS.BombEvents.BombExplode.OnClientEvent:Connect(function()
                    hrp.Anchored = true
                    task.wait()
                    hrp.Anchored = false                   
                    if not hum.SeatPart then  
                        hum.Sit = false 
                    end
                    for i = 1, #etc.Limbs do
                        local limb = char:FindFirstChild(etc.Limbs[i])
                        if limb then
                            limb.RagdollLimbPart.CanCollide = false
                        end
                    end
                end)
            else    
                cons:disc("AntiExplodeCon") 
            end 
        end
    })
    Toggles["AdditionalAntiKickMethod"] = DefTab:AddDropdown({
        Name = "Additional-Antikick method",
        Default = "<font color=\"rgb(255, 0, 0)\"><b>[DEATH]</b></font>",
        Options = {"<font color=\"rgb(255, 0, 0)\"><b>[DEATH]</b></font>","<font color=\"rgb(0, 0, 255)\"><b>[SNO]</b></font>","<font color=\"rgb(0, 255, 0)\"><b>[FORCE TELEPORT]</b></font>"},
        Save = true,
        Flag = "AdditionalAntiKickMethod",
        Callback = function(Value)
            int.AddAntiKickMethod = Value
        end    
    })
    local function FindFloor()
        local rayOrigin = hrp.Position
        local rayDirection = Vector3.new(0, -500, 0)
        local raycastParams = RaycastParams.new()
        raycastParams.FilterDescendantsInstances = {character}
        raycastParams.FilterType = Enum.RaycastFilterType.Exclude
        local raycastResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
        if raycastResult then 
            return raycastResult.Position + Vector3.new(0,2,0)
        else 
            return Vector3.new(0,2,0)
        end
    end

    Toggles["AdditionalAntiKick"] = DefTab:AddToggle({
        Name = "Additional-Antikick",
        Default = true,
        Save = true,
        Flag = "AdditionalAntiKick",
        Callback = function(Val)
            if Val then  
                cons["ADDANTIKICK"] = RS.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(reason)
                    if reason == "Flying" then 
                        if int.AddAntiKickMethod == "<font color=\"rgb(255, 0, 0)\"><b>[DEATH]</b></font>" then
                            Struggle:FireServer("Unbind")
                            hum.Health = 0
                        elseif int.AddAntiKickMethod == "<font color=\"rgb(0, 0, 255)\"><b>[SNO]</b></font>" then 
                            for i = 1 ,100 do 
                                sno(hrp)
                                task.wait(0.01)
                            end
                        else 
                            local BodyVelocity = Instance.new("BodyVelocity")
                            BodyVelocity.P = 10000
                            BodyVelocity.MaxForce = Vector3.new(Huge())
                            BodyVelocity.Velocity = Vector3.new(0,-1000,0)
                            while (hum) and hum.FloorMaterial == Enum.Material.Air do task.wait() end
                            if BodyVelocity and BodyVelocity.Parent ~= nil then 
                                for _,v in pairs(hrp:GetChildren()) do if v:IsA("BodyVelocity") then v:Destroy() end end
                            end
                        end
                    end
                end)
            else 
                cons:disc("ADDANTIKICK")
            end
        end
    })
 
    
    local function ClearKunai()
        for _, v in pairs(inv:GetChildren()) do
            if v.Name == "AntiKick" or v.Name == "NinjaShuriken" or v.Name == "WaitingForChild" then
                DestroyToy:FireServer(v)
                if v:IsDescendantOf(game) then
                    Debris:AddItem(v, 0)
                end
            end
        end
    end
    int.MainAntiKickColor = Color3.fromRGB(255, 0, 255)
    int.PyramidAntiKickColor = Color3.fromRGB(255, 212, 127)
    local function HighlightKunai(kunai,Part1Weld) 
        if kunai and Part1Weld and Part1Weld == hrp.FirePlayerPart and kunai.Name ~= "AntiKick" then  
            kunai.Name = "AntiKick"
            for _, obj in ipairs(kunai:GetChildren()) do
                if obj:IsA("BasePart") then
                    obj.CanTouch = false
                    obj.CanCollide = false
                    obj.CanQuery = false
                    if obj.Name == "Main" or obj.Name == "Pyramid" then
                        obj.Transparency = 0
                        local high = Instance.new("Highlight")
                        high.FillColor = (obj.Name == "Main") and int.MainAntiKickColor or int.PyramidAntiKickColor
                        high.Parent = obj
                    else
                        obj.Transparency = 1
                    end
                end
            end
            task.spawn(RenameInShop,kunai,"NinjaShuriken","AntiKick")
        end
    end

    local function StickKunai(kunai)
        local StickyPart = kunai:FindFirstChild("StickyPart")
        local StickyWeld = StickyPart and FWD(StickyPart,"StickyWeld",0.2)
        local FirePlayerPart = hrp and hrp:FindFirstChild("FirePlayerPart")
        local Part1Weld,PartOwner
        if not(StickyWeld) or not(FirePlayerPart) then 
            kunai.Name = "AntiKick"
            return false
        end   
        sno(StickyPart)
        StickyEvent:FireServer(StickyPart, FirePlayerPart, CFrame.Angles(0, math.rad(90), math.rad(90)))
        local EndTick = tick() + 0.7
        while RunService.Heartbeat:Wait() do 
            StickyWeld = StickyPart and StickyPart:FindFirstChild("StickyWeld")
            PartOwner = StickyPart and StickyPart:FindFirstChild("PartOwner")
            Part1Weld = StickyWeld and StickyWeld.Part1
            if PartOwner and PartOwner.Value ~= plr.Name then 
                sno(StickyPart)
                StickyEvent:FireServer(StickyPart, FirePlayerPart, CFrame.Angles(0, math.rad(90), math.rad(90)))
                EndTick = tick() + 0.7
            end
            if not(StickyWeld) or Part1Weld or (tick() >= EndTick) or (GetMagnitude(StickyPart,(etc.MyPCLD or hrp)) > 20) then  
                break
            end
        end
        task.spawn(HighlightKunai,kunai,Part1Weld)
        -- return Part1Weld == FirePlayerPart
    end

    -- Toggles["AntiKick"] = DefTab:AddToggle({
    --     Name = "AntiKick<font color=\"rgb(0, 255, 0)\"><b>[SHURIKEN]</b></font>",
    --     Default = false,
    --     Save = true,
    --     Flag = "AntiKick",
    --     Callback = function(Val)
    --         bool.AntiKick = Val
    --         local kunai, StickyPart, StickyWeld, Part1Weld
    --         task.spawn(function() 
    --             while bool.AntiKick and RunService.Heartbeat:Wait() do
    --                 if not hrp or not hum or hum.Health == 0 then continue end  
    --                 if InPlot.Value and not InOwnedPlot.Value then 
    --                     continue
    --                 elseif InOwnedPlot.Value then 
    --                     local House = CheckForHome()
    --                     kunai = House and House:FindFirstChild("AntiKick")
    --                 else  
    --                     kunai = inv:FindFirstChild("AntiKick")
    --                 end

    --                 if kunai then 
    --                     StickyPart = kunai:FindFirstChild("StickyPart")
    --                     StickyWeld = StickyPart and StickyPart:FindFirstChild("StickyWeld")
    --                     Part1Weld = StickyWeld and StickyWeld.Part1

    --                     if Part1Weld ~= hrp.FirePlayerPart then
    --                         if (StickyPart) and GetMagnitude(StickyPart,(etc.MyPCLD or hrp)) <= 20 then
    --                             StickKunai(kunai)
    --                         else
    --                             task.spawn(ClearKunai)
    --                             kunai = SpawnToy("NinjaShuriken")
    --                             if kunai then StickKunai(kunai) end
    --                         end
    --                     end
    --                 else 
    --                     task.spawn(ClearKunai)
    --                     kunai = SpawnToy("NinjaShuriken")
    --                     if kunai then StickKunai(kunai) end
    --                 end 
    --             end
                
    --             if not bool.AntiKick then
    --                 task.spawn(ClearKunai)
    --             end
    --         end)
    --     end
    -- })
    local function EnsureStuck(kunai)
        local StickyPart = kunai:FindFirstChild("StickyPart")
        if not StickyPart then return false end
        local FirePlayerPart = hrp and hrp:FindFirstChild("FirePlayerPart")
        if not FirePlayerPart then return false end
        local StickyWeld = StickyPart:FindFirstChild("StickyWeld")

        if StickyWeld and StickyWeld.Part1 == FirePlayerPart then
            return true
        end
        StickKunai(kunai)
        return false
    end

    Toggles["AntiKick"] = DefTab:AddToggle({
        Name = "AntiKick<font color=\"rgb(0, 255, 0)\"><b>[SHURIKEN]</b></font>",
        Default = false,
        Save = true,
        Flag = "AntiKick",
        Callback = function(Val)
            bool.AntiKick = Val 
            task.spawn(function() 
                while bool.AntiKick do
                    RunService.Heartbeat:Wait()
                    if not hrp or not hum or hum.Health <= 0 then continue end  
                    local kunai
                    if InOwnedPlot.Value then 
                        local House = CheckForHome()
                        kunai = House and House:FindFirstChild("AntiKick")
                    elseif not InPlot.Value then
                        kunai = inv:FindFirstChild("AntiKick")
                    else 
                        continue
                    end
                    if kunai then 
                        local StickyPart = kunai:FindFirstChild("StickyPart")
                        if StickyPart and GetMagnitude(StickyPart,hrp) > 30 then
                            task.spawn(ClearKunai)
                            SpawnToy("NinjaShuriken")
                        else
                            EnsureStuck(kunai)
                        end
                    else 
                        task.spawn(ClearKunai)
                        local newKunai = SpawnToy("NinjaShuriken")
                        if newKunai then EnsureStuck(newKunai) end
                    end 
                end
                task.spawn(ClearKunai)
            end)
        end
    })
    Toggles["AntiKick2"] = DefTab:AddToggle({
        Name = "AntiKICK[2]",
        Default = false,
        Save = true,
        Flag = "Antikick2",
        Callback = function(Val)
        task.wait(0.1)
        bool.antikick = Val
        if bool.antikick then
            task.spawn(function()
                task.wait(0.1)
                if not inv:FindFirstChild("NinjaShuriken1") then
                    repeat task.wait() until plr.CanSpawnToy.Value
                    local shu
                    local part
                    local plot = CheckForHome()
                    while bool.antikick and task.wait() do
                        pcall(function()
                            local char = plr.Character
                            if not shu or not inv:FindFirstChild("NinjaShuriken1") and not workspace.PlotItems:FindFirstChild("NinjaShuriken1", true) then
                                print(1)
                                shu = SpawnToy("NinjaShuriken")
                                shu.Name = "NinjaShuriken1"
                                part = shu:WaitForChild("StickyPart", 0.3)
                                sno(part)
                            end
                            if shu and shu:FindFirstChild("StickyPart") and shu.StickyPart:FindFirstChild("PartOwner") and shu.StickyPart:FindFirstChild("PartOwner").Value ~= plr.Name then
                                print(2)
                                sno(part)
                            end
                            if part and part:FindFirstChild("StickyWeld") and part.StickyWeld.Part1 ~= char.HumanoidRootPart.FirePlayerPart then
                                print(3)
                                sno(part)
                                StickyEvent:FireServer(part, char.HumanoidRootPart.FirePlayerPart, CFrame.new(0,0,0,1,0,0,0,0,-1,0,1,0))
                            end
                            task.wait(0.2)
                            if shu and shu:FindFirstChild("StickyPart") and (shi.StickyPart.Position - HRP.Position).Magnitude > 30 then
                                print(4)
                                DestroyToy:FireServer(inv.NinjaShuriken1)
                                shu = SpawnToy("NinjaShuriken ")
                                part = shu:WaitForChild("StickyPart", 0.3)
                                shu.Name = "NinjaShuriken1"
                                sno(part)
                            end
                        end)
                    end
                end
            end)
        else
            if inv:FindFirstChild("NinjaShuriken1") then DestroyToy:FireServer(inv.NinjaShuriken1) end
        end
        end
    })
    Toggles["AntiKick"] = DefTab:AddToggle({
        Name = "AntiKick<font color=\"rgb(0, 255, 0)\"><b>[ITEM]</b></font>",
        Default = false,
        Save = true,
        Flag = "AntiKickItem",
        Callback = function(Val)
            bool.AntiKickItem = Val 
            task.spawn(function()
                local Item,SoundPart,Weld
                while bool.AntiKickItem and task.wait() do 
                    if not hrp or not hum or hum.Health == 0 then continue end  
                    if InPlot.Value then continue end 
                    Item = inv:FindFirstChild("AntiKickItem") 
                    SoundPart = Item and Item:FindFirstChild("Hitbox")
                    if not(Item) or not(SoundPart) then
                        for _,v in pairs(inv:GetChildren()) do 
                            if v.Name == "AntiKickItem" then 
                                DestroyToy:FireServer(v)
                            end
                        end
                        Item = SpawnToy("SpookyCandle1")
                        if not Item then continue end 
                        SoundPart = Item and FWD(Item,"Hitbox",0.5)
                        sno(SoundPart)
                        Weld = Instance.new("WeldConstraint")
                        for _,v in pairs(Item:GetChildren()) do 
                            if v:IsA("BasePart") then 
                                v.CanCollide = false 
                                v.CanQuery = false
                                v.Transparency = 0.8
                            end
                        end
                        SoundPart.CFrame = (etc["MyPCLD"] or hrp).CFrame 
                        Weld.Part0 = SoundPart
                        Weld.Part1 = (etc["MyPCLD"] or hrp.FirePlayerPart)
                        Weld.Parent = SoundPart
                        Weld.Name = "WeldBlabla"
                        Item.Name = "AntiKickItem"
                    end
                    Weld = SoundPart and SoundPart:FindFirstChild("WeldBlabla")
                    if not CheckNetworkOwnerOnPart(SoundPart) then 
                        sno(SoundPart)
                    end
                    if Weld and Weld.Part1 ~= (etc["MyPCLD"] or hrp.FirePlayerPart) then
                        Weld.Enabled = false 
                        SoundPart.CFrame = (etc["MyPCLD"] or hrp).CFrame 
                        Weld.Part1 = (etc["MyPCLD"] or hrp.FirePlayerPart)
                        Weld.Enabled = true
                    end
                end
            end)
        end
    })
    Toggles["AntiBlob"] = DefTab:AddToggle({
        Name = "AntiBlob",
        Default = false,
        Save = true,
        Flag = "AntiBlob",
        Callback = function(Val)
            bool.AntiBlob = Val
            for _,v in (Players:GetChildren()) do 
                if v == plr then continue end 
                for _,x in pairs(workspace[v.Name.."SpawnedInToys"]:GetChildren()) do 
                    if x.Name == "CreatureBlobman" then  
                        local RightDetector,LeftDetector = x:FindFirstChild("RightDetector"),x:FindFirstChild("LeftDetector")
                        if RightDetector and LeftDetector then  
                            RightDetector.RightWeld.Enabled = not(bool.AntiBlob)
                            RightDetector.RightAlignOrientation.Enabled = not(bool.AntiBlob)  
                            RightDetector.RightAlignOrientation.RigidityEnabled = not(bool.AntiBlob)
                            LeftDetector.LeftWeld.Enabled = not(bool.AntiBlob)
                            LeftDetector.LeftAlignOrientation.Enabled = not(bool.AntiBlob)
                            LeftDetector.LeftAlignOrientation.RigidityEnabled = not(bool.AntiBlob)
                        end
                    end
                end
            end
            for i = 1,5 do 
                for _,y in pairs(workspace.Plots["Plot"..i]:GetChildren()) do 
                    if y.Name == "CreatureBlobman" then  
                        local LeftDetector,RightDetector = y:FindFirstChild("LeftDetector"),y:FindFirstChild("RightDetector")
                        if LeftDetector and RightDetector then  
                            RightDetector.RightWeld.Enabled = not(bool.AntiBlob)
                            RightDetector.RightAlignOrientation.Enabled = not(bool.AntiBlob)
                            RightDetector.RightAlignOrientation.RigidityEnabled = not(bool.AntiBlob)
                            LeftDetector.LeftWeld.Enabled = not(bool.AntiBlob)
                            LeftDetector.LeftAlignOrientation.Enabled = not(bool.AntiBlob)
                            LeftDetector.LeftAlignOrientation.RigidityEnabled = not(bool.AntiBlob)
                        end
                    end
                end
            end
        end
    })










    Toggles["LoopTpHouse"] = DefTab:AddDropdown({
        Name = "House to loopTP",
        Default = "🟩Green House🟩",
        Options = {"🟩Green House🟩", "🩷Pink House🩷","🦇Witch House🦇","🟦Blue House🟦","🀄China House💮"},
        Save = true,
        Flag = "LoopTpHouse",
        Callback = function(Value)
            if etc.MapPoints[Value] then 
                int.LoopTpCFrame = etc.MapPoints[Value]
            end
        end    
    })

    DefTab:AddToggle({
        Name = "AntiLoopKill<font color=\"rgb(255, 0, 0)\"><b>[LoopTP]</b></font>",
        Default = false,
        Callback = function(Val)
            bool.LoopTpToHouse = Val 
            local char 
            task.spawn(function()
            if bool.LoopTpToHouse then
                cons["AntiLoppKillLOOPTP"] = RunService.Heartbeat:Connect(function()
                    char = plr.Character 
                    if not char then return end 
                    char:PivotTo(int.LoopTpCFrame)
                end)
            else 
                cons:disc("AntiLoppKillLOOPTP")
                pcall(StopVelocityF)
            end
            end)
        end
    })   

    DefTab:AddToggle({
        Name = "AntiLoopKill<font color=\"rgb(0, 0, 255)\"><b>[RANDOM]</b></font>",
        Default = false,
        Callback = function(Val)
            bool.LoopTpRandom = Val  
            task.spawn(function() 
                if bool.LoopTpRandom then
                    local char = plr.Character 
                    local oldCF = char:GetPivot()
                    while bool.LoopTpRandom do
                        for _,CF in pairs(etc.MapPoints) do  
                            char = plr.Character 
                            if not char then 
                                while not plr.Character do task.wait() end 
                                char = plr.Character
                            end
                            char:PivotTo(CF)
                            task.wait(0.1)
                        end
                    end
                    StopAllVelocity(char)
                end
            end)
        end
    })
    DefTab:AddToggle({
        Name = "AntiLoopKill<font color=\"rgb(255, 0, 255)\"><b>[VOID]</b></font>",
        Default = false,
        Callback = function(Val)
            bool.LoopTpRandom1 = Val  
            local oldHeight = workspace.FallenPartsDestroyHeight
            task.spawn(function()
            if bool.LoopTpRandom1 then
                oldHeight = 0/0
                while bool.LoopTpRandom1 do
                    local char = plr.Character 
                    if not char then continue end 
                    char:PivotTo(CFrame.new(0,-75000000,-500000))
                    task.wait(0.05)
                    char:PivotTo(CFrame.new(0,-75000000,500000))
                    task.wait(0.05)
                    char:PivotTo(CFrame.new(500000,-75000000,0))
                    task.wait(0.05)
                    char:PivotTo(CFrame.new(-500000,-75000000,0))
                    task.wait(0.05)
                    char:PivotTo(CFrame.new(-500000,-75000000,-500000))
                    task.wait(0.05)
                    char:PivotTo(CFrame.new(500000,-75000000,500000))
                    task.wait(0.05)
                end
                StopAllVelocity(char)
                workspace.FallenPartsDestroyHeight = oldHeight
            end
            end)
        end
    })
    
    Toggles["AntiPaint"] = DefTab:AddToggle({
        Name = "AntiPaint",
        Default = false,
        Save = true,
        Flag = "AntiPaint",
        Callback = function(Val)
            bool.AntiPaint = Val
            if Val then  
                for _,v in pairs(game.Players:GetPlayers()) do 
                    local invPlr = workspace[v.Name.."SpawnedInToys"]
                    if invPlr then  
                        for _,x in pairs(invPlr:GetDescendants()) do  
                            if x.Name == "PaintPlayerPart" then  
                                x:Destroy()
                            end
                        end
                    end
                end
                for i = 1,5 do  
                    for _,v in pairs(workspace.Plots["Plot"..i]:GetDescendants()) do  
                        if v.Name == "PaintPlayerPart" then  
                            v:Destroy()
                        end
                    end
                end
            end
        end
    })

    local function SetupAntiBurn(char)
        cons:disc("AntiBurn")
        if not hum then 
            while not hum do task.wait() end 
        end
        local FireDebounce = hum and FWD(hum,"FireDebounce")
        if not FireDebounce then return end
        local Barrier = workspace.Map.Hole.PoisonBigHole.ExtinguishPart
        if not Barrier then warn("[DEBUG] Какого хуя??") return end 
        if CFP(Barrier,"Tex") then 
            Barrier.Tex:Destroy()
        end
        local OldPos = Barrier.Position
        cons["AntiBurn"] = FireDebounce:GetPropertyChangedSignal("Value"):Connect(function()
            FireDebounce = hum and hum:FindFirstChild("FireDebounce")
            if FireDebounce.Value and not bool.WorkingOnFire then  
                bool.WorkingOnFire = true
                Barrier.Transparency = 1
                while (FireDebounce and FireDebounce.Value) and not CFP(hrp,"FireLight") do task.wait(); print("Waiting") end 
                while (FireDebounce and FireDebounce.Value) and (hrp and CFP(hrp,"FireLight")) and task.wait() do 
                    Barrier.Position = hrp.FirePlayerPart.Position
                end
                Barrier.Position = OldPos   
                bool.WorkingOnFire = false
            end
        end)
    end
    DefTab:AddToggle({
        Name = "AntiBurn",
        Default = false,
        Save = true,
        Flag = "AntiBurn",
        Callback = function(Val)

            if Val then  
                if char then  
                    SetupAntiBurn(char)
                end
                cons["AntiBurnHelper"] = plr.CharacterAdded:Connect(SetupAntiBurn)
            else  
                cons:disc("AntiBurnHelper")
                cons:disc("AntiBurn")
            end
        end
    })
    Toggles["AntiVoid"] = DefTab:AddToggle({
        Name = "AntiVoid",
        Default = false,
        Save = true,
        Flag = "AntiVoid",
        Callback = function(Val)
            bool.AntiVoidEnabled = Val
            workspace.FallenPartsDestroyHeight = Val and 0/0 or -100
        end
    })
    Toggles["AntiBarrier"] = DefTab:AddToggle({
        Name = "AntiBarrier",
        Default = false,
        Save = true,
        Flag = "AntiBarrier",
        Callback = function(Value)
            for i = 1 ,5 do 
                local barrier = FWD(FWD(workspace.Plots,"Plot"..i),"Barrier")
                for _,v in pairs(barrier:GetChildren()) do  
                    v.CanCollide = not Value
                    v.CanQuery = not Value
                end    
            end
        end
    })
    DefTab:AddButton({
        Name = "Break SafeZone",
        Callback = function()
        local Hamburger,Plot3
        local oldCF = char:GetPivot()
        Hamburger = SpawnToy("FoodHamburger")
        if not Hamburger then return end
        local GrabItemRemoteFunction = FWD(FWD(Hamburger,"HoldPart",10),"HoldItemRemoteFunction",10)
        if GrabItemRemoteFunction then 
            GrabItemRemoteFunction:InvokeServer(Hamburger,char)
            char:PivotTo(workspace.Plots.Plot3.PlotArea.CFrame)
            task.wait(0.1)
            char:PivotTo(oldCF)
            DestroyToy:FireServer(Hamburger)
        end 
        end    
    })
    

    Toggles["AntiLag"] = DefTab:AddToggle({
        Name = "AntiLag",
        Default = false,
        Save = true,
        Flag = "AntiLag",
        Callback = function(Value)
            CountOfLines = 0
            AntiLineLag.Enabled = not Value
            AntiShuriLag.Enabled = not Value
        end
    })
    local LastLagSource = nil  
    local CountOfLines = 0 
    DefTab:AddToggle({
        Name = "Auto AntiLag",
        Default = true,
        Callback = function(Value)
        bool.AutoAntiLag = Value
    end
    })
    -- DefTab:AddToggle({
    --     Name = "AntiFling",
    --     Default = true,
    --     Callback = function(Value)
    --     bool.AntiFling = Value 
    --     for _,v in pairs(game.Players:GetPlayers()) do 
    --         if v == plr then continue end 
    --         local char = v.Character 
    --         if v.Character then  
    --             for _,x in pairs(char:GetDescendants()) do 
    --                 x.CanCollide = Value  
    --             end
    --         end
    --         local targetInv = workspace[v.Name.."SpawnedInToys"]
    --         for _,c in pairs(targetInv:GetChildren()) do 
    --             if c.Name == "YouDecoy" or c.Name == "PalletLightBrown" then  
    --                 for _,z in pairs(c:GetDescendants()) do 
    --                     if z:IsA("BasePart") then 
    --                         z.CanCollide = Value
    --                     end
    --                 end
    --             end
    --         end
    --     end
    -- end
    -- })

    DefTab:AddToggle({
        Name = "Anti-INPUTLAG[BURGER]",
        Default = false,
        Callback = function(Value)
            bool.AntiLagInput = Value
            if bool.AntiLagInput then 
                local burger = inv:FindFirstChild("FoodMayonnaise")
                local HoldPart = burger and burger:FindFirstChild("HoldPart")
                local DropItem,HoldItem
                while bool.AntiLagInput and task.wait() do 

                    if not char or not char:FindFirstChild("Left Arm") then continue end

                    HoldPart = burger and burger.Parent and burger:FindFirstChild("HoldPart")
                    DropItem,HoldItem = HoldPart and HoldPart:FindFirstChild("DropItemRemoteFunction"),HoldPart and HoldPart:FindFirstChild("HoldItemRemoteFunction")

                    if not HoldPart or not (DropItem and HoldItem) then   
                        for _,v in pairs(inv:GetChildren()) do  
                            if v.Name == "FoodMayonnaise" then  
                                DestroyToy:FireServer(v)
                            end
                        end
                        burger = SpawnToy("FoodMayonnaise")
                        if not burger then continue end 
                        HoldPart = FWD(burger,"HoldPart",1)
                        DropItem,HoldItem = HoldPart and FWD(HoldPart,"DropItemRemoteFunction",1), HoldPart and FWD(HoldPart,"HoldItemRemoteFunction",1)
                    end

                    task.spawn(function()
                        HoldItem:InvokeServer(burger,char)
                        while HoldPart and not HoldPart.RigidConstraint.Attachment1 do task.wait() end 
                        if HoldPart["RigidConstraint"].Attachment1 ~= char["Left Arm"].LeftGripAttachment then 
                            for _,v in pairs(inv:GetChildren()) do  
                                if v.Name == "FoodMayonnaise" then  
                                    DestroyToy:FireServer(v)
                                end
                            end
                            burger = nil
                        end
                    end)
                    task.wait(0.1)
                    task.spawn(DropItem.InvokeServer,DropItem,burger,char:GetPivot() * CFrame.new(0,500,0),Vector3.zero)
                end
            end
        end
    })
    DefTab:AddToggle({
        Name = "Anti-INPUTLAG[TEST,MB PINGY]",
        Default = false,
        Callback = function(Val)
            bool.AntiInputLag1 = Val 
            while bool.AntiInputLag1 and task.wait() do
                if hrp and not isnetworkowner(hrp) and not IsHeld.Value then  
                    RagdollRemote:FireServer(hrp,0)
                end
            end
        end
    })
    


    DefTab:AddButton({
        Name = "Bread ANTIKICK",
        Callback = function()
        local target = etc.MyPCLD or hrp.FirePlayerPart or hrp  
        local bread = SpawnToy("FoodBread")

        repeat
            game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(bread.SoundPart,bread.SoundPart.CFrame)
            task.wait()
        until (not bread or not bread:FindFirstChild("SoundPart")) or bread.SoundPart:FindFirstChild("PartOwner")

        ChangeCollision(bread,false)
        task.spawn(function()
            while bread.Parent ~= nil do
                task.wait()
                bread.SoundPart.CFrame = target.CFrame * CFrame.Angles(math.rad(90), 0, 0)
                task.defer(StopAllVelocity,bread)
            end
        end)
        
        end    
    })

    DefTab:AddToggle({
        Name = "Perm ragdoll",
        Default = false,
        Callback = function(Val)
            bool.PermRag = Val 
            local char,hrp
            while bool.PermRag and task.wait(0.7) do
                hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
                if not hrp then continue end 
                RagdollRemote:FireServer(hrp,1)
            end
        end
    })



    int.Strength = 600 

    Toggles["Strength"] = GrabTab:AddSlider({
        Name = "Slider",
        Min = 600,
        Max = 10000,
        Default = 600,
        Color = Color3.fromRGB(255, 80, 80),
        Increment = 100,
        ValueName = "S",
        Save = true,
        Flag = "Strength",
        Callback = function(Value)
            int.Strength = Value
        end    
    })
    Toggles["SuperStrengthToggle"] = GrabTab:AddToggle({
        Name = "Super Strength",
        Default = false,
        Save = true,
        Flag = "SuperStrengthToggle",
        Callback = function(Value)
            if Value then
                local obj
                cons["SuperGrab"] = workspace.ChildAdded:Connect(function(c)
                    if c.Name == "GrabParts" then
                        local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
                        if part then
                            local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
                            if weld then
                                obj = weld.Part1
                            end
                        end
                    end
                end)
                cons["WaitUIS"] = game:GetService("UserInputService").InputBegan:Connect(function(inp)
                    if inp.UserInputType == Enum.UserInputType.MouseButton2 then
                        if obj then
                            local bv = Instance.new("BodyVelocity", obj)
                            local Camera = workspace.CurrentCamera
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bv.Velocity = Camera.CFrame.LookVector * int.Strength
                            Debris:AddItem(bv,4)
                            obj = nil
                        end
                    end
                end)
                cons["DeleteObj"] = workspace.ChildRemoved:Connect(function(desc)
                    if desc.Name == "GrabParts" then  
                        task.delay(1,function()
                            obj = nil
                        end)
                    end
                end)
            else
                cons:disc("SuperGrab")
                cons:disc("WaitUIS")
                cons:disc("DeleteObj")
            end
        end
    })

    int.increaseLineExtendV = 7
    Toggles["InfLineExtendT"] = GrabTab:AddToggle({
        Name = "Infinity Line Extend",
        Default = false,
        Save = true,
        Flag = "InfLineExtendT",
        Callback = function(Value)
            local char = plr.Character
            local hrp = char:WaitForChild("HumanoidRootPart")
            local hum = char:WaitForChild("Humanoid")
            local cam = workspace.CurrentCamera
            int.lineDistanceV = 0
            if Value then 
                cons["INFLINEHELPER"] = UIS.InputChanged:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseWheel then
                        if int.lineDistanceV <= 3 then
                            int.lineDistanceV = 3
                        end
                
                        if input.Position.Z > 0 then
                            int.lineDistanceV = int.lineDistanceV + int.increaseLineExtendV
                        elseif input.Position.Z < 0 then
                            int.lineDistanceV = int.lineDistanceV - int.increaseLineExtendV
                        end
                    end
                end)
                
                cons["INFLINE"] = workspace.ChildAdded:Connect(function(child)
                    if child.Name == "GrabParts" and child:IsA("Model") then
                        if UIS.MouseEnabled then
                            local grabPartsModel = child

                            grabPartsModel:WaitForChild("GrabPart")
                            grabPartsModel:WaitForChild("DragPart")
                                
                            local clonedDragPart = grabPartsModel.DragPart:Clone()
                            clonedDragPart.Name = "DragPart1"
                            clonedDragPart.AlignPosition.Attachment1 = clonedDragPart.DragAttach
                            clonedDragPart.Parent = grabPartsModel           
                            int.lineDistanceV = GetMagnitude(clonedDragPart,cam.CFrame)
                            clonedDragPart.AlignOrientation.Enabled = false
                            grabPartsModel.DragPart.AlignPosition.Enabled = false
                
                            task.spawn(function()
                                while grabPartsModel.Parent do
                                    clonedDragPart.Position = cam.CFrame.Position + cam.CFrame.LookVector * int.lineDistanceV
                                    task.wait()
                                end
                        
                                int.lineDistanceV = 0
                            end)
                        end
                    end
                end)
            else 
                cons:disc("INFLINEHELPER")
                cons:disc("INFLINE")
            end
        end
    })

    Toggles["LineIncreaseValue"] = GrabTab:AddSlider({
        Name = "Line Extend Increase",
        Min = 1,
        Max = 10,
        Default = 7,
        Color = Color3.fromRGB(255, 0,0),
        Increment = 1,
        ValueName = "S",
        Save = true,
        Flag = "LineIncreaseValue",
        Callback = function(Value)
            int.increaseLineExtendV = Value
        end    
    })

    GrabTab:AddSection({Name = "Grabs"})
    Toggles["KiKiGrab"] = GrabTab:AddToggle({
        Name = "kikimora grab",
        Default = false,
        Save = true,
        Flag = "KiKiGrab",
        Callback = function(Value)
            if Value then 
                cons["KickGrab"] = workspace.ChildAdded:Connect(function(c)
                    if c.Name ~= "GrabParts" then return end
                    local GrabPart = c:WaitForChild("GrabPart", 0.1)
                    task.wait(0.1)
                    cons["kickgrabdestroy"] = c.Destroying:Once(function()
                        task.wait(0.1)
                        DestroyGrabLine:FireServer(part)
                    end)
                    local part = GrabPart.WeldConstraint.Part1
                    if game.Players:FindFirstChild(part.Parent.Name) then
                        while GrabPart and GrabPart.Parent do
                            DestroyGrabLine:FireServer(part)
                            RunService.RenderStepped:Wait()
                            SetNetworkOwner:FireServer(part, part.CFrame)
                            DestroyGrabLine:FireServer(part)
                            RunService.RenderStepped:Wait()
                            SetNetworkOwner:FireServer(part, part.CFrame)
                        end
                    end
                end)
            else 
                if cons["kickgrabdestroy"] then cons["kickgrabdestroy"]:Disconnect() cons["kickgrabdestroy"] = nil end
                if cons["KickGrab"] then cons["KickGrab"]:Disconnect() cons["KickGrab"] = nil end
            end
        end
    })

    Toggles["MassLessGrab"] = GrabTab:AddToggle({
        Name = "MassLessGrab",
        Default = false,
        Save = true,
        Flag = "MassLessGrab",
        Callback = function(Value)
            bool.MassLessGrab = Value
        end
    })


    Toggles["Sense"] = GrabTab:AddSlider({
        Name = "Slider",
        Min = 1,
        Max = 200,
        Default = 30,
        Color = Color3.fromRGB(255, 0, 255),
        Increment = 1,
        ValueName = "Sense",
        Save = true,
        Flag = "Sense",
        Callback = function(Value)
            int.Sense = Value
        end    
    })

    Toggles["SpinGrab"] = GrabTab:AddToggle({
        Name = "SpinGrab",
        Default = false,
        Save = true,
        Flag = "SpinGrab",
        Callback = function(Value)
            bool.SpinGrab = Value
        end
    })


    Toggles["RagdollGrab"] = GrabTab:AddToggle({
        Name = "Ragdoll Grab",
        Default = false,
        Save = true,
        Flag = "RagdollGrab",
        Callback = function(Value)
            bool.RagdollGrab = Value
            if Value then 
                local palete = inv:FindFirstChild("RagdollPalete")
                if not palete then 
                    palete = SpawnToy("PalletLightBrown")
                    if not palete then return end 
                    FWD(palete,"SoundPart")
                end
                local oldCF = char:GetPivot() 
                while not CheckNetworkOwnerOnPart(palete.SoundPart) and task.wait(0.05) do 
                    char:PivotTo(palete.SoundPart.CFrame)
                    sno(palete.SoundPart)
                end
                for _,v in pairs(palete:GetChildren()) do 
                    if v:IsA("BasePart") then 
                        v.Transparency = 0.8
                        v.CanCollide = false 
                        v.CanQuery = false
                    end
                end
                char:PivotTo(oldCF)
                palete.Name = "RagdollPalete"
                task.delay(1,RenameInShop,palete,"PalletLightBrown","Ragdoll")
                local bv = Instance.new("BodyVelocity")
                bv.MaxForce = Vector3.new(0,math.huge,0)
                bv.Velocity = Vector3.new(0,900,0)
                bv.Parent = palete.SoundPart
            else 
                cons:disc("Ragdoll")
            end
        end
    })

    Toggles["DeathGrab"] = GrabTab:AddToggle({
        Name = "DeathGrab",
        Default = false,
        Save = true,
        Flag = "DeathGrab",
        Callback = function(Value)
            bool.DeathGrab = Value
        end
    })

    Toggles["CrazyGrab"] = GrabTab:AddToggle({
        Name = "CrazyGrab",
        Default = false,
        Save = true,
        Flag = "CrazyGrab",
        Callback = function(Value)
            bool.CrazyGrab = Value
        end
    })

    GrabTab:AddSection({
        Name = "Advanced Line Mods"
    })

    GrabTab:AddToggle({
        Name = "RGB line[FE]",
        Default = false,
        Callback = function(Value)
            bool.RGBLine = Value
            while bool.RGBLine do
                local args = {
                    [1] = ColorSequence.new{
                        ColorSequenceKeypoint.new(0.0, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                        ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255)))
                    }
                }
                SetLineColor:FireServer(unpack(args))
                task.wait(0.1)
            end
        end
    })
    GrabTab:AddToggle({
        Name = "Invisible Line",
        Default = false,
        Save = true,
        Flag = "InvisibleLineFeature",
        Callback = function(Value)
            bool.InvisLine = Value
        end
    })
    workspace.ChildAdded:Connect(function(child)
        if child.Name == "GrabParts" then 
            local Weld = FWD(FWD(child,"GrabPart",5),"WeldConstraint",5)
            local GrabPart = Weld and Weld.Part1
            local Parent = GrabPart and GrabPart.Parent 
            local Root = Parent and Parent:FindFirstChild("HumanoidRootPart")
            if bool.InvisLine then 
                CreateGrabLine:FireServer()
            end
            if bool.CrazyGrab and Root then 
                task.spawn(function()
                    while CFP(workspace,"GrabParts") do 
                        Root.CFrame = CFrame.new(0,-20,0)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(1e9,-20,0)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(-1e9,-20,0)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(1e9,-20,1e9)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(1e9,-20,-1e9)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(0,-20,1e9)
                        task.wait(0.05)
                        Root.CFrame = CFrame.new(1e9,1e9,1e9)
                        task.wait(0.05)
                    end
                end)
            end
            if bool.DeathGrab and Root then 
                task.spawn(function()
                    while not CheckNetworkOwnerOnPlayer(nil,Root) and CFP(workspace,"GrabParts") do task.wait() end
                    _G.ForceDeath(Root,Parent:FindFirstChildOfClass("Humanoid"))
                    unsno(Root)
                end)
            end
            if Root and bool.RagdollGrab then 
                task.spawn(function()
                    local Ragdolled = CFP(Parent,"Humanoid") and Parent.Humanoid:FindFirstChild("Ragdolled")
                    local palete = inv:FindFirstChild("RagdollPalete")
                    if not palete then 
                        palete = SpawnToy("PalletLightBrown")
                        if not palete then return end 
                        while not CheckNetworkOwnerOnPart(FWD(palete,"SoundPart")) and task.wait(0.05) do 
                            sno(palete.SoundPart)
                        end
                        for _,v in pairs(palete:GetChildren()) do 
                            if v:IsA("BasePart") then 
                                v.Transparency = 0.8
                                v.CanCollide = false 
                                v.CanQuery = false
                            end
                        end
                        palete.Name = "RagdollPalete"
                        task.delay(1,RenameInShop,palete,"PalletLightBrown","Ragdoll")
                        local bv = Instance.new("BodyVelocity")
                        bv.MaxForce = Vector3.new(0,math.huge,0)
                        bv.Velocity = Vector3.new(0,900,0)
                        bv.Parent = palete.SoundPart
                    end
                    while true do 
                        Ragdolled = CFP(Parent,"Humanoid") and Parent.Humanoid:FindFirstChild("Ragdolled")
                        if not Root or not Ragdolled or not CFP(workspace,"GrabParts") or Ragdolled.Value then 
                            break
                        end
                        palete.SoundPart.Position = Root.Position
                        task.wait(0.1)
                    end 
                end)
            end
            if bool.SpinGrab then 
                local b = Instance.new("BodyAngularVelocity",Parent.PrimaryPart)
                b.AngularVelocity = Vector3.new(0,10,0)
                b.MaxTorque = Vector3.new(0,math.huge,0)
            end
            if bool.MassLessGrab then 
                task.spawn(function()
                    if cons["INFLINE"] then 
                        while CFP(workspace,"GrabParts") and task.wait() do
                            child.DragPart1.AlignPosition.Responsiveness = int.Sense
                            child.DragPart.AlignOrientation.Responsiveness = int.Sense
                            child.DragPart1.AlignPosition.MaxForce = math.huge
                            child.DragPart1.AlignPosition.MaxVelocity = math.huge
                            child.DragPart.AlignOrientation.MaxTorque = math.huge
                        end
                    else 
                        while CFP(workspace,"GrabParts") and task.wait() do
                            child.DragPart.AlignPosition.Responsiveness = int.Sense
                            child.DragPart.AlignOrientation.Responsiveness = int.Sense
                            child.DragPart.AlignPosition.MaxForce = math.huge
                            child.DragPart.AlignPosition.MaxVelocity = math.huge
                            child.DragPart.AlignOrientation.MaxTorque = math.huge
                        end
                    end
                end)
            end
        end
    end)

    GrabTab:AddButton({
        Name = "Return base color",
        Callback = function()
           local args = {
                [1] = ColorSequence.new({
                    ColorSequenceKeypoint.new(0.00, Color3.fromRGB(230, 255, 240)), 
                    ColorSequenceKeypoint.new(0.09, Color3.fromRGB(180, 255, 200)), 
                    ColorSequenceKeypoint.new(0.18, Color3.fromRGB(130, 255, 160)), 
                    ColorSequenceKeypoint.new(0.27, Color3.fromRGB(100, 240, 180)), 
                    ColorSequenceKeypoint.new(0.36, Color3.fromRGB(90, 200, 255)),  
                    ColorSequenceKeypoint.new(0.45, Color3.fromRGB(80, 150, 255)),  
                    ColorSequenceKeypoint.new(0.54, Color3.fromRGB(110, 110, 255)), 
                    ColorSequenceKeypoint.new(0.63, Color3.fromRGB(160, 80, 255)),  
                    ColorSequenceKeypoint.new(0.72, Color3.fromRGB(200, 60, 255)),  
                    ColorSequenceKeypoint.new(0.81, Color3.fromRGB(255, 40, 200)),  
                    ColorSequenceKeypoint.new(0.90, Color3.fromRGB(255, 30, 80)),   
                    ColorSequenceKeypoint.new(1.00, Color3.fromRGB(0, 0, 0))        
                })
            }
            SetLineColor:FireServer(unpack(args))
            task.wait() 
        end    
    })
    GrabTab:AddToggle({
        Name = "Crazy Line(Players)",
        Default = false,
        Callback = function(Value)
            bool.CrazyLinePlr = Value 
            while bool.CrazyLinePlr do
                for _,plr in pairs(Players:GetPlayers()) do 
                    local character = plr.Character
                    if character then  
                        local hrp1 = character:FindFirstChild("HumanoidRootPart")
                        if hrp1 then  
                            CreateGrabLine:FireServer(hrp1,hrp1.CFrame * CFrame.new(0,math.random(0,100),0))
                            task.wait(0.15)
                        end
                    end 
                end
            end
        end
    })


    GrabTab:AddSection({
        Name = "Auras"
    })

    local function IsFriend(player)
        if not player or not player.UserId then return false end
        local success, result = pcall(function()
            return plr:IsFriendsWith(player.UserId)
        end)
        if success then
            return result
        else
            return false
        end
    end


    GrabTab:AddSlider({ Name = "Fling Aura Power", Min = 600, Max = 10000, Default = 600, Color = Color3.fromRGB(255, 80, 80), Increment = 100, ValueName = "S", Save = true, Flag = "FlingAuraStrength", Callback = function(Value) int.flingPwr = Value end })
    GrabTab:AddToggle({
        Name = "Fling Aura",
        Default = false,
        Callback = function(Value)
            bool.FlingAura = Value  
            if bool.FlingAura then  
                while bool.FlingAura do 
                    task.wait() 
                    for _,v in pairs(game.Players:GetPlayers()) do 
                        task.wait() 
                        if v ~= plr and not IsFriend(v) and bool.FlingAura and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then  
                            local v1 = v.Character 
                            local v2 = v1 and v1:FindFirstChild("HumanoidRootPart")  
                            if v2 and GetMagnitude(hrp,v2) <= 40 then  
                                sno(v2)
                                task.wait()
                                if not v2:FindFirstChild("BodyVelocity") then  
                                    local bv = Instance.new("BodyVelocity") 
                                    bv.MaxForce = Vector3.new(Huge())
                                    local direction = hrp.CFrame.LookVector
                                    bv.Velocity = direction * int.flingPwr
                                    bv.Parent = v2
                                end
                            end
                        end
                    end
                end
            end
        end
    })

    GrabTab:AddToggle({
        Name = "Click Aura",
        Default = false,
        Callback = function(Value)
            bool.ClickAura = Value  
            if bool.ClickAura then  
                while bool.ClickAura do 
                    task.wait() 
                    for _,v in pairs(game.Players:GetPlayers()) do 
                        task.wait() 
                        if v ~= plr and not IsFriend(v) and  bool.ClickAura and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then  
                            local v1 = v.Character 
                            local v2 = v1 and v1:FindFirstChild("HumanoidRootPart")  
                            if v2 and GetMagnitude(hrp,v2) <= 40 then  
                                sno(v2)
                                task.wait()
                            end
                        end
                    end
                end
            end
        end
    })

    GrabTab:AddToggle({
        Name = "anti-antikick Aura",
        Default = false,
        Callback = function(Value)
            bool.AntiKickAura = Value 
            local parts,ParentName,SoundPart
            while bool.AntiKickAura and task.wait(0.05) do 
                parts = Workspace:GetPartBoundsInRadius(hrp.Position,35)
                for _,v in pairs(parts) do 
                    local ParentName = v.Parent and v.Parent.Name
                    if ParentName == "NinjaShuriken" or ParentName == "NinjaKunai" then 
                        SoundPart = v.Parent:FindFirstChild("SoundPart") 
                        if SoundPart then 
                            sno(SoundPart)
                        end
                    end
                end 
            end 
        end
    })
    GrabTab:AddSection({
        Name = "c00lLine"
    })
    Toggles["ColorPicker123322231"] = GrabTab:AddSlider({
        Name = "Colors Count",
        Min = 1,
        Max = 20,
        Default = 5,
        Color = Color3.fromRGB(255,255,255),
        Increment = 1,
        ValueName = "colors",
        Save = true,
        Flag = "ColorPicker123322231",
        Callback = function(Value)
            int.sliderValue = Value
        end    
    })

    for i = 1, 20 do
        Toggles["ColorPicker"..i] = GrabTab:AddColorpicker({
            Name = "Colorpicker " .. i,
            Default = Color3.fromRGB(255, 0, 0),
            Save = true,
            Flag = "ColorPicker"..i,
            Callback = function(Value)
                int["Color"..i] = Value
            end   
        })
        int["Color"..i] = Color3.fromRGB(255,0,0)
    end


    Toggles["ApplyColorGrabline"] = GrabTab:AddToggle({
        Name = "ApplyColor",
        Default = false,
        Save = true,
        Flag = "ApplyColorGrabline",
        Callback = function(Value)
            bool.toggleEnabled = Value
            if bool.toggleEnabled then
                task.spawn(function()
                    local keypoints = {}
                    while bool.toggleEnabled and task.wait(1) do
                        keypoints = {}
                        for i = 1, int.sliderValue do
                            local pos = (i-1) / math.max(int.sliderValue-1, 1) 
                            table.insert(keypoints, ColorSequenceKeypoint.new(pos, int["Color"..i]))
                        end
                        SetLineColor:FireServer(ColorSequence.new(keypoints))
                    end
                end)
            end
        end    
    })



    _G.WalkspeedValue = 5 
    function _G.WalkspeedFunc()
        cons:disc("WS")
        if bool.WalkSpeed then
            cons["WS"] = RunService.Stepped:Connect(function()
                if plr and plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') and plr.Character:FindFirstChildOfClass('Humanoid') and typeof(_G.WalkspeedValue) == 'number' then
                    h = plr.Character.HumanoidRootPart
                    u = plr.Character:FindFirstChildOfClass('Humanoid')
                    h.CFrame = h.CFrame + u.MoveDirection * ((16 * _G.WalkspeedValue) / 10)
                end
            end)
        end
    end

    Toggles["WalkSpeedValue"] = PlrTab:AddSlider({
        Name = "WalkSpeed",
        Min = 1,
        Max = 10,
        Default = 5,
        Color = Color3.fromRGB(51, 204, 51),
        Increment = 1,
        ValueName = "multiplier",
        Save = true,
        Flag = "WalkSpeedValue",
        Callback = function(Value)
            _G.WalkspeedValue = Value
        end    
    })

    Toggles["ApplyWalkSpeed"] = PlrTab:AddToggle({
        Name = "Apply WalkSpeed",
        Default = false,
        Save = true,
        Flag = "ApplyWalkSpeed",
        Callback = function(Value)
            bool.WalkSpeed = Value
            if Value then 
                _G.WalkspeedFunc()
            else 
                cons:disc("WS")
            end
        end
    })
    Toggles["IF"] = PlrTab:AddToggle({
        Name = "Infinity Jump",
        Default = false,
        Save = true,
        Flag = "IF",
        Callback = function(Value)
            if Value then 
                cons["IF"] = game:GetService("UserInputService").JumpRequest:Connect(function()
                    hum:ChangeState(Enum.HumanoidStateType.Jumping)
                end)
            else 
                cons:disc("IF")
            end
        end
    })
    Toggles["OceanWalk"] = PlrTab:AddToggle({
        Name = "Ocean Walk",
        Default = false,
        Save = true,
        Flag = "OceanWalk",
        Callback = function(Value)
            for _,v in pairs(workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel:GetChildren()) do  
                if v:IsA("BasePart") then  
                    v.CanCollide = Value
                    v.CanTouch = Value  
                    v.CanQuery = Value
                end
            end
        end
    })


    local ESPConnections = {}

    local function createESP(plr)
        local function onESPCharAdded(char)
            local Head = FWD(char,"Head",3)
            if not head or not bool.ESPEnabled then return end
            local BillboardGUI = Instance.new('BillboardGui')
            BillboardGUI.Adornee = Head
            BillboardGUI.Size = UDim2.new(0, 50, 0, 50)
            BillboardGUI.AlwaysOnTop = true
            BillboardGUI.StudsOffset = Vector3.new(0, 3, 0)
            local ImageLabel = Instance.new('ImageLabel', BillboardGUI)
            ImageLabel.BackgroundTransparency = 1
            ImageLabel.Size = UDim2.new(0, 50, 0, 50)
            ImageLabel.Position = UDim2.new(0.5, -25, 0, 0)
            ImageLabel.Image = 'https://www.roblox.com/headshot-thumbnail/image?userId=' .. plr.UserId .. '&width=420&height=420&format=png'
            local TextLabel = Instance.new('TextLabel', BillboardGUI)
            TextLabel.BackgroundTransparency = 1
            TextLabel.Size = UDim2.new(1, 0, 0, 32)
            TextLabel.Position = UDim2.new(0, 0, 1, -32)
            TextLabel.Text = plr.Name .. ' (@' .. plr.DisplayName .. ')'
            TextLabel.TextColor3 = Color3.new(1,1,1)
            TextLabel.TextStrokeTransparency = 0
            BillboardGUI.Parent = Head
        end

        if plr.Character then
            onESPCharAdded(plr.Character)
        end

        local NewPlrCon = plr.CharacterAdded:Connect(onESPCharAdded)
        table.insert(ESPConnections, NewPlrCon)
    end


    local function clearESP()
        for _, conn in ipairs(ESPConnections) do
            conn:Disconnect()
        end
        ESPConnections = {}
        task.wait()
        for _, plr in ipairs(game.Players:GetPlayers()) do
            if plr.Character then
                if plr.Character:FindFirstChild("Head") and plr.Character.Head:FindFirstChild("BillboardGui") then
                    plr.Character.Head["BillboardGui"]:Destroy()
                end
            end
        end
    end
    Toggles["Preserver"] = PlrTab:AddToggle({
        Name = "Preserve House Time",
        Default = false,
        Save = true,
        Flag = "Preserver",
        Callback = function(Value)
            bool.Preserve = Value
            local Plot,PlrInHouse,TimeRemainingNum
            while bool.Preserve and task.wait(0.5) do
                for i = 1,5 do 
                    for _, owner in pairs(workspace.Plots:FindFirstChild("Plot"..i).PlotSign.ThisPlotsOwners:GetChildren()) do
                        if owner.Value == plr.Name then
                            Plot = workspace.Plots:FindFirstChild("Plot"..i)
                            PlrInHouse = owner
                            break 
                        end
                    end
                if PlrInHouse and PlrInHouse.Parent and Plot then Notify("Preserver debug","Found you in "..Plot.Name) break end 
                end
                while bool.Preserve and PlrInHouse and PlrInHouse.Parent and task.wait(0.25) do
                    TimeRemainingNum = PlrInHouse and PlrInHouse.Parent and PlrInHouse:FindFirstChild("TimeRemainingNum")
                    if TimeRemainingNum then
                        if TimeRemainingNum.Value <= 10 then
                            if not bool.inwork then
                                bool.inwork = true  
                                local oldCF = char:GetPivot()
                                char:PivotTo(Plot.PlotArea.CFrame)
                                task.wait(0.35)
                                char:PivotTo(oldCF)
                                bool.inwork = false
                            end
                        end
                    else    
                        break
                    end
                end
            end
        end
    })

    Toggles["ThirdP"] = VisualTab:AddToggle({
        Name = "Thirdp",
        Default = false,
        Save = true,
        Flag = "ThirdP",
        Callback = function(Value)
            plr.CameraMaxZoomDistance = 1e9
            if Value then
                plr.CameraMode = "Classic"
            else    
                plr.CameraMode = "LockFirstPerson"
            end 
        end
    })

    Toggles["FOVvalue"] = VisualTab:AddSlider({
        Name = "FOV",
        Min = 10,
        Max = 120,
        Default = 80,
        Color = Color3.fromRGB(255,0,255),
        Increment = 1,
        ValueName = "",
        Save = true,
        Flag = "FOVvalue",
        Callback = function(Value)
            workspace.CurrentCamera.FieldOfView = Value
        end    
    })


    Toggles["ESP"] = VisualTab:AddToggle({
        Name = "Icon ESP",
        Default = false,
        Save = true,
        Flag = "ESP",
        Callback = function(Value)
            bool.ESPEnabled = Value
            if bool.ESPEnabled then
                for _, plr in ipairs(game.Players:GetPlayers()) do
                    if plr ~= game.Players.LocalPlayer then
                        createESP(plr)
                    end
                end
                cons["NewPlayerCon"] = game.Players.PlayerAdded:Connect(function(plr)
                    if plr ~= game.Players.LocalPlayer then
                        createESP(plr)
                    end
                end)
            else
                clearESP()
                cons:disc("NewPlayerCon")
            end
        end
    })


    Toggles["PCLD_ESP"] = VisualTab:AddToggle({
        Name = "PCLD ESP",
        Default = false,
        Save = true,
        Flag = "PCLD_ESP",
        Callback = function(Value)
            if Value then
                
                for _, v in pairs(workspace:GetChildren()) do
                    AddESP(v)
                end
                
                PCLD_ESP_CON = workspace.ChildAdded:Connect(function(obj)
                    AddESP(obj)
                end)
            else
                
                for _, v in pairs(workspace:GetChildren()) do
                    RemoveESP(v)
                end
                
                if PCLD_ESP_CON then
                    PCLD_ESP_CON:Disconnect()
                    PCLD_ESP_CON = nil
                end
            end
        end
    })

    Toggles["Time"] = VisualTab:AddSlider({
        Name = "Time",
        Min = 0,
        Max = 24,
        Default = 14,
        Color = Color3.fromRGB(255, 0, 0),
        Increment = 1,
        ValueName = "Hours",
        Save = true,
        Flag = "Time",
        Callback = function(Value)
            Lighting.ClockTime = Value
        end    
    })
    local Depth = Instance.new("DepthOfFieldEffect")
    Depth.Parent = Lighting
    Depth.FocusDistance = 0.1
    Toggles["DepthOfField"] = VisualTab:AddSlider({
        Name = "Depth of Field",
        Min = 0,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(0, 255, 0),
        Increment = 1,
        ValueName = "Depths",
        Save = true,
        Flag = "DepthOfField",
        Callback = function(Value)
            Depth.FarIntensity = Value * 0.01
        end    
    })
    Toggles["FOGS"] = VisualTab:AddSlider({
        Name = "FOG Intensity",
        Min = 0,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(0, 0, 255),
        Increment = 1,
        ValueName = "FOGS",
        Save = true,
        Flag = "FOGS",
        Callback = function(Value)
            Lighting.FogEnd = (10000 - Value * 100)
        end    
    })
    Toggles["FOGcolor"] = VisualTab:AddColorpicker({
        Name = "FOG color",
        Default = Color3.fromRGB(192, 192, 192),
        Save = true,
        Flag = "FOGcolor",
        Callback = function(Value)
            Lighting.FogColor = Value
        end	  
    })

    Toggles["Ambientcolor"] = VisualTab:AddColorpicker({
        Name = "Ambient color",
        Default = Color3.fromRGB(120, 120, 120),
        Save = true,
        Flag = "Ambientcolor",
        Callback = function(Value)
            Lighting.FogColor = Value
        end	  
    })

    local ColorCol = Instance.new("ColorCorrectionEffect",Lighting)
    Toggles["Brigtness"] = VisualTab:AddSlider({
        Name = "Brigtness",
        Min = -100,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(255, 0, 0),
        Increment = 1,
        ValueName = "Brights",
        Save = true,
        Flag = "",
        Callback = function(Value)
            ColorCol.Brightness = Value * 0.01
        end    
    })
    Toggles["Contrast"] = VisualTab:AddSlider({
        Name = "Contrast",
        Min = -100,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(0, 255, 0),
        Increment = 1,
        ValueName = "Contrast",
        Save = true,
        Flag = "",
        Callback = function(Value)
            ColorCol.Contrast = Value * 0.01
        end    
    })
    Toggles["Saturation"] = VisualTab:AddSlider({
        Name = "Saturation",
        Min = -100,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(0, 0, 255),
        Increment = 1,
        ValueName = "",
        Save = true,
        Flag = "Saturation",
        Callback = function(Value)
            ColorCol.Saturation = Value * 0.01
        end    
    })
    local Bloom = Instance.new("BloomEffect")
    Bloom.Parent = Lighting
    Bloom.Threshold = 0.9

    Toggles["BloomIntensity"] = VisualTab:AddSlider({
        Name = "Bloom Intensity",
        Min = 0,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(255, 255, 0),
        Increment = 1,
        ValueName = "Intens",
        Save = true,
        Flag = "BloomIntensity",
        Callback = function(Value)
            Bloom.Intensity = Value * 0.05 
        end
    })

    local options, playerMap = BuildPlayerOptions()
    local selectedPlrName = nil 
    local Dropdown1 = TarTab:AddDropdown({
        Name = "Target",
        Default = "",
        Options = options,
        Callback = function(Value)
            local chosenPlayer = playerMap[Value]
            selectedPlrName = chosenPlayer and chosenPlayer.Name or nil
        end
    })
    TarTab:AddTextbox({
        Name = "Find By Nick[PARTIAL]",
        Default = "",
        TextDisappear = true,
        Callback = function(Value)
            if Value == "" then return end
            Value = Value:lower()

            for _, plr1 in pairs(game.Players:GetPlayers()) do
                local nameMatch = string.sub(plr1.Name:lower(), 1, #Value) == Value
                local displayMatch = string.sub(plr1.DisplayName:lower(), 1, #Value) == Value

                if nameMatch or displayMatch then
                    local displayString = string.format(
                        '<font color="rgb(255,0,0)"><b>%s</b></font> <b><i>(%s)</i></b>',
                        plr1.Name,
                        plr1.DisplayName
                    )
                    selectedPlrName = plr1.Name
                    Dropdown1:Set(displayString)
                    break 
                end
            end
        end   
    })

    TarTab:AddButton({
        Name = "Decoy Fling",
        Callback = function()
            local decoy = SpawnToy("YouDecoy")
            decoy.Name = "FLINGDECOY"
            while not CFP(decoy,"HumanoidRootPart") or not CFP(decoy,"Torso") do task.wait() end 
            local hrp = FWD(decoy,"HumanoidRootPart")
            local head = FWD(decoy,"Head")
            repeat 
                sno(hrp)
                task.wait(0.05)
            until not hrp.Parent or decoy:WaitForChild("Head"):FindFirstChild("PartOwner")
            local target = game.Players[selectedPlrName]
            if not target or not target.Character or not target.Character:FindFirstChild("HumanoidRootPart") then return end
            local targetHRP = target.Character.HumanoidRootPart

            task.spawn(function()
                while decoy and decoy.Parent and target and target.Character and task.wait() do
                    hrp.CFrame = targetHRP.CFrame
                    head.CFrame = targetHRP.CFrame * CFrame.new(0,1.5,0)
                    decoy["Torso"].CFrame = targetHRP.CFrame
                    hrp.AssemblyLinearVelocity = Vector3.new(
                        math.random(-50, 50),
                        math.random(100, 200),
                        math.random(-50, 50)
                    )
                    hrp.AssemblyAngularVelocity = Vector3.new(
                        math.random(-200, 200) * 100,
                        math.random(-200, 200) * 100,
                        math.random(-200, 200) * 100
                    )
                end
            end)
        end    
    })


    TarTab:AddToggle({
        Name = "Don`t Give chance to seat on blob",
        Default = false,
        Callback = function(Value)
            bool.AntiBlobGrab = Value
            while bool.AntiBlobGrab do
                task.wait(0.1)
                if not Players[selectedPlrName] or not Players[selectedPlrName].Character or not Players[selectedPlrName].Character.Humanoid then continue end 
                if Players[selectedPlrName].Character.Humanoid.SeatPart ~= nil then 
                    local oldCF = hrp.CFrame
                    task.wait(0.1) 
                    hrp.CFrame = Players[selectedPlrName].Character.HumanoidRootPart.CFrame * CFrame.new(0,5,5)
                    task.wait(0.2) 
                    sno(Players[selectedPlrName].Character.Head)
                    hrp.CFrame = oldCF
                    task.wait(0.5)
                end
            end
        end    
    })
TarTab:AddButton({
    Name = "Check For Friend Here",
    Callback = function()
        local TargetPLR = Players:FindFirstChild(selectedPlrName)
        if not TargetPLR then 
            Notify("Error!", "Player not found!")
            return 
        end 
        
        local suc, FriendsList = pcall(function()
            return Players:GetFriendsAsync(TargetPLR.UserId)
        end)
        
        local Friends = {}
        if suc and FriendsList then
            for _, fr in pairs(FriendsList:GetCurrentPage()) do 
                table.insert(Friends, fr.Username)
            end
        else
            Notify("Error!", "Failed to get friends list")
            return
        end
        
        
        local foundAny = false
        for _, v in pairs(Players:GetPlayers()) do 
            for _, friendName in pairs(Friends) do 
                if v.Name == friendName then 
                    foundAny = true
                    OrionLib:MakeNotification({
                        Name = "Friend found",
                        Content = v.Name .. " (" .. v.DisplayName .. ")",
                        Image = "book-user",
                        Time = 5
                    })
                end
            end
        end
        
        if not foundAny then
            OrionLib:MakeNotification({
                Name = "No friends here",
                Content = "None of " .. TargetPLR.Name .. "'s friends are on this server",
                Image = "circle-x",
                Time = 5
            })
        end
    end    
})
    TarTab:AddSection({ Name = "<font color=\"rgb(255, 0, 255)\" size = \"24\"><b>Anti-AntiSmth</b></font>"})
    TarTab:AddParagraph("NOTE", [[<font color="#FF00FF"><b>[WD]</b></font> Поможет от близзов у которых антикик сносится хоть чем 
    <font color="#00FF00"><b>[CLICK]</b></font> самый обычный кликер который просто ставит нетворк овнера 
    <font color="#64FF64"><b>[BYPASS]</b></font> Будет пытаться сломать антикик что бы он забагался в воздухе, может поднять пинг 🛜
    <font color="#FF64FF"><b>[BLACKHOLE]</b></font> Будет пытаться снести антикик черной дырой от спама предметов, полезно если антикик из дома]])
    TarTab:AddToggle({
        Name = "Anti-AntiKick<font color=\"rgb(255, 0, 255)\"><b>[WD]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopWD = Value
            if Value then 
                local WD = inv:FindFirstChild("SprayCanWD")
                local SoundPart = WD and WD:FindFirstChild("SoundPart")
                etc.Root = selectedPlrName and game.Players:FindFirstChild(selectedPlrName) and game.Players:FindFirstChild(selectedPlrName).Character:FindFirstChild("HumanoidRootPart")

                if not WD then 
                    WD = SpawnToy("SprayCanWD")
                    repeat task.wait() until WD and WD:FindFirstChild("SoundPart") or not bool.LoopWD
                    SoundPart = WD:FindFirstChild("SoundPart") or WD:WaitForChild("SoundPart",3)
                    ChangeCollision(WD,false)
                    task.delay(1,RenameInShop,WD,WD.Name,"Anti-AntIkick")
                end

                local HitBox = FWD(WD,"Hitbox")
                repeat  
                    sno(HitBox)
                    task.wait(0.05)
                until not HitBox.Parent or not bool.LoopWD or HitBox:FindFirstChild("PartOwner")

                while bool.LoopWD and task.wait(0.2) do
                    SoundPart = WD and WD.Parent and WD:FindFirstChild("SoundPart")
                    etc.Root = game.Players:FindFirstChild(selectedPlrName) and game.Players:FindFirstChild(selectedPlrName).Character and game.Players:FindFirstChild(selectedPlrName).Character:FindFirstChild("HumanoidRootPart")
                    if not etc.Root then  
                        repeat 
                            etc.Root = game.Players:FindFirstChild(selectedPlrName) and game.Players:FindFirstChild(selectedPlrName).Character and game.Players:FindFirstChild(selectedPlrName).Character:FindFirstChild("HumanoidRootPart")
                            task.wait(0.1)
                        until etc.Root or not bool.LoopWD
                    end
                    if not SoundPart then  
                        WD = SpawnToy("SprayCanWD")
                        repeat task.wait() until WD and WD:FindFirstChild("SoundPart") or not bool.LoopWD
                        SoundPart = WD:FindFirstChild("SoundPart") or WD:WaitForChild("SoundPart",3)
                        ChangeCollision(WD,false)
                        HitBox = FWD(WD,"Hitbox")
                        repeat  
                            sno(HitBox)
                            task.wait(0.05)
                        until not HitBox.Parent or HitBox:FindFirstChild("PartOwner") or not bool.LoopWD
                        task.delay(1,RenameInShop,WD,WD.Name,"Anti-Antikick")
                    end
                    SoundPart.CFrame = etc.Root.CFrame * CFrame.new(0,0,4)
                    task.wait()
                    SoundPart.CFrame = CFrame.new(-382.838318, 41.6491699, 665.570251)
                    SoundPart.AssemblyAngularVelocity = Vector3.zero
                    SoundPart.AssemblyLinearVelocity = Vector3.zero
                end
            end
        end    
    })
    TarTab:AddToggle({
        Name = "Anti-AntiKick<font color=\"rgb(0, 255, 0)\"><b>[CLICK]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopGrabAntiKick1 = Value
            if bool.LoopGrabAntiKick1 then 
                local TargetInv = selectedPlrName and workspace:FindFirstChild(selectedPlrName.."SpawnedInToys")
                local TargetPLR = selectedPlrName and Players:FindFirstChild(selectedPlrName)
                local Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                local FirePlayerPart = Root and Root:FindFirstChild("FirePlayerPart")
                local StickyPart,PartOwner,StickyWeld
                while bool.LoopGrabAntiKick1 and task.wait() do 
                    Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                    if not Root then continue end 
                    FirePlayerPart = Root:FindFirstChild("FirePlayerPart")
                    if not FirePlayerPart then continue end
                    for _,v in pairs(TargetInv:GetChildren()) do 
                        StickyPart = v:FindFirstChild("StickyPart")
                        if StickyPart then 
                            if StickyPart.CanQuery then 
                                task.spawn(function()
                                    for _,part in pairs(v:GetChildren()) do  
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
            end
        end    
    })
    TarTab:AddToggle({
        Name = "Anti-AntiKick<font color=\"rgb(100, 255, 100)\"><b>[BYPASS]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopGrabAntiKick1 = Value
            if bool.LoopGrabAntiKick1 then 
                local TargetInv = selectedPlrName and workspace:FindFirstChild(selectedPlrName.."SpawnedInToys")
                local TargetPLR = selectedPlrName and Players:FindFirstChild(selectedPlrName)
                local Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                local FirePlayerPart = Root and Root:FindFirstChild("FirePlayerPart")
                local StickyPart,PartOwner,StickyWeld
                while bool.LoopGrabAntiKick1 and task.wait() do 
                    Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                    if not Root then continue end 
                    FirePlayerPart = Root:FindFirstChild("FirePlayerPart")
                    if not FirePlayerPart then continue end
                    for _,v in pairs(TargetInv:GetChildren()) do 
                        StickyPart = v:FindFirstChild("StickyPart")
                        if StickyPart then 
                            StickyWeld = StickyPart and StickyPart:FindFirstChild("StickyWeld")
                            if not StickyWeld then continue end 
                            if StickyPart.CanQuery then 
                                task.spawn(function()
                                    for _,part in pairs(v:GetChildren()) do  
                                        if part:IsA("BasePart") then  
                                            part.CanCollide = false 
                                            part.CanTouch = false 
                                            part.CanQuery = false
                                        end
                                    end
                                end)
                            end
                            if StickyWeld.Part1 == hrp.FirePlayerPart and GetMagnitude(FirePlayerPart,StickyPart) > 7 then 
                                continue 
                            elseif not CheckNetworkOwnerOnPart(StickyPart) then 
                                sno(StickyPart)
                            else
                                StickyEvent:FireServer(
                                    StickyPart,
                                    FirePlayerPart,
                                    CFrame.new(0,-10,5)
                                )
                            end
                        end
                    end
                end
            end
        end    
    })
    TarTab:AddToggle({
        Name = "Anti-AntiKick<font color=\"rgb(255,100,255)\"><b>[BLACKHOLE]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.loopGrabAntiKick1 = Value
            if not bool.loopGrabAntiKick1 then return end 
            local targetInv = workspace:FindFirstChild(selectedPlrName.."SpawnedInToys") 
            local ShurikensToBlackhole = {}
            while bool.loopGrabAntiKick1 and task.wait() do
                for i = 1,10 do 
                    local Shur = SpawnToy("NinjaKunai")
                    if not Shur then continue end
                    local StickyPart = FWD(Shur,"StickyPart")
                    sno(StickyPart)
                    Shur.Name = i
                    task.delay(0.5,RenameInShop,Shur,"NinjaKunai","Anti-AntiKick(Kunai)")
                    do
                        local BodyPos = Instance.new("BodyPosition")
                        BodyPos.Position = Vector3.new(math.random(-100,100),1e3,math.random(-100,100))
                        BodyPos.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
                        BodyPos.Parent = StickyPart
                    end
                    table.insert(ShurikensToBlackhole,StickyPart)
                end
                local HRP = game.Players:FindFirstChild(selectedPlrName) and game.Players:FindFirstChild(selectedPlrName).Character and game.Players:FindFirstChild(selectedPlrName).Character:FindFirstChild("HumanoidRootPart") and game.Players:FindFirstChild(selectedPlrName).Character:FindFirstChild("HumanoidRootPart"):FindFirstChild("FirePlayerPart")
                for _,v in pairs(ShurikensToBlackhole) do 
                    if not v or not v.Parent then continue end
                    StickyEvent:FireServer(
                        v,
                        HRP,
                        CFrame.new(0,0,0)
                    )
                end
                task.wait(10)
            end
        end    
    })
    TarTab:AddToggle({
        Name = "Anti-Burger",
        Default = false,
        Callback = function(Value)
            bool.StealBurger = Value
            if Value then 
                local TargetInv = workspace[selectedPlrName.."SpawnedInToys"]
                for _,v in pairs(TargetInv:GetDescendants()) do 
                    if v.Name == "HoldPart" then 
                        local HoldFunction = v:FindFirstChild("HoldItemRemoteFunction")
                        local DropFunction = v:FindFirstChild("DropItemRemoteFunction")
                        repeat 
                            HoldFunction = v.Parent and v:FindFirstChild("HoldItemRemoteFunction")
                            DropFunction = v.Parent and v:FindFirstChild("DropItemRemoteFunction")
                            if not HoldFunction then break end
                            task.wait(0.05)
                            task.spawn(function()
                                HoldFunction:InvokeServer(v.Parent,char)
                            end)
                        until not v or not v.Parent or not HoldFunction or (v["RigidConstraint"].Attachment1 and v["RigidConstraint"].Attachment1 == char["Left Arm"].LeftGripAttachment) or not bool.StealBurger 
                        if DropFunction then 
                            task.spawn(function()
                                DropFunction:InvokeServer(v.Parent)
                            end)
                        end
                        v.Name = "NotHoldPart"
                    end
                end
                cons["StealBurger"] = TargetInv.DescendantAdded:Connect(function(desc)
                    if desc.Name == "HoldPart" then 
                        task.spawn(function()
                            local suc
                            FWD(desc,"HoldItemRemoteFunction"):InvokeServer(desc.Parent,char)
                            suc,_ = pcall(function() 
                                FWD(desc,"HoldItemRemoteFunction"):InvokeServer(desc.Parent,char)
                            end)
                            if suc then 
                                repeat  
                                    if not desc or not desc.Parent then break end
                                    if desc.AssemblyRootPart ~= hrp then 
                                        FWD(desc,"HoldItemRemoteFunction"):InvokeServer(desc.Parent,char)
                                    end
                                    if not desc or not desc.Parent then break end 
                                    if desc.AssemblyRootPart ~= hrp then continue end 
                                    suc,_ = pcall(function() FWD(desc,"HoldItemRemoteFunction"):InvokeServer(desc.Parent,char); end)
                                    task.wait()
                                until not suc or not desc or not bool.StealBurger
                            end
                        end)
                    end
                end)
            else 
                cons:disc("StealBurger")
            end
        end    
    })
    TarTab:AddSection({ Name = "<font color=\"rgb(100, 255, 100)\" size = \"24\"><b>BUTTON KICKS</b></font> "})
    TarTab:AddButton({
        Name = "Just Grab",
        Callback = function()
            BringRight(selectedPlrName)
        end    
    })

    local function CheckForPartOwner(Head)
        local PartOwner = Head:FindFirstChild("PartOwner")
        local HasYou = PartOwner and PartOwner.Value == plr.Name
        return HasYou
    end  

    TarTab:AddButton({
        Name = "Bug in Hand",
        Callback = function()
            local v = FindBlob()
            local RightDetector,RightWeld = v and v:FindFirstChild("RightDetector"),v:FindFirstChild("RightDetector") and v.RightDetector:FindFirstChild("RightWeld")
            local CreatureGrab,CreatureDrop = v and v.BlobmanSeatAndOwnerScript.CreatureGrab,v and v.BlobmanSeatAndOwnerScript.CreatureDrop
            local clone = SpawnToy("YouDecoy")
            repeat
                task.wait(0.05)
                CreatureGrab:FireServer(RightDetector,clone:WaitForChild("HumanoidRootPart",5),RightWeld)
            until v.RightDetector.RightWeld.Attachment0 or not hum.SeatPart
            task.wait()
            DestroyToy:FireServer(clone)

            

            local clone = SpawnToy("YouDecoy")
            task.wait()
            repeat 
                CreatureGrab:FireServer(v.LeftDetector,clone:WaitForChild("HumanoidRootPart"),v.LeftDetector.LeftWeld)
                task.wait(0.05)
            until v.LeftDetector.LeftWeld.Attachment0 or not hum.SeatPart
            DestroyToy:FireServer(clone)
            task.wait(0.05)

            sno(game.Players:FindFirstChild(selectedPlrName).Character.HumanoidRootPart)
            CreatureGrab:FireServer(RightDetector,game.Players:FindFirstChild(selectedPlrName).Character.HumanoidRootPart,RightWeld)
            CreatureGrab:FireServer(v.LeftDetector,game.Players:FindFirstChild(selectedPlrName).Character.HumanoidRootPart,v.LeftDetector.LeftWeld)
        end    
    })

    TarTab:AddDropdown({
        Name = "BUTTON KICKS MODE",
        Default = "[BASE]",
        Options = {"[BASE]","[BLITZ]"},
        Callback = function(Value)
            int.selectedButtonMode = Value
        end    
    })

    TarTab:AddButton({
        Name = "Kick[BLOB ONLY]",
        Callback = function()
            local blob = FindBlob()
            etc.Root = game.Players[selectedPlrName].Character.HumanoidRootPart
            local oldCF = char:GetPivot()
            if not etc.Root then return end  
            if not blob then 
                blob = SpawnToy("CreatureBlobman")
                if not blob then return end 
                local Seat = FWD(blob,"VehicleSeat")
                Seat:Sit(hum)
                while Seat.Occupant ~= hum do task.wait() end 
            end
            local RightDetector,RightWeld = blob:FindFirstChild("RightDetector"), blob.RightDetector and blob.RightDetector:FindFirstChild("RightWeld")
            local CreatureGrab,CreatureDrop,CreatureRelease = blob.BlobmanSeatAndOwnerScript.CreatureGrab,blob.BlobmanSeatAndOwnerScript.CreatureDrop,blob.BlobmanSeatAndOwnerScript.CreatureRelease
            repeat
                char:PivotTo(etc.Root.CFrame * CFrame.new(0,10,5) * CFrame.new(etc.Root.AssemblyLinearVelocity * 0.4))
                CreatureGrab:FireServer(RightDetector,etc.Root,RightWeld)
                task.wait(0.05)
            until RightWeld.Attachment0 or not hum.SeatPart
            if RightWeld.Attachment0 then 
                CreatureRelease:FireServer(RightWeld,etc["Root"])
                -- for _ = 1,5 do 
                --     CreatureGrab:FireServer(RightDetector,etc["Root"],RightWeld)
                --     CreatureRelease:FireServer(RightWeld,etc["Root"])
                --     if isnetworkowner(etc["Root"]) then 
                --         etc["Root"].Parent:PivotTo(oldCF * CFrame.new(0,20,0))
                --     end
                --     RunService.Stepped:Wait()
                -- end
                if isnetworkowner(etc["Root"]) then 
                    for _,v in pairs(etc["Root"].Parent) do 
                        if v:IsA("BasePart") then 
                            v.CFrame = oldCF * CFrame.new(0,20,0)
                        end
                    end
                end
                char:PivotTo(oldCF)
                while not CheckNetworkOwnerOnPlayer(_,etc["Root"]) and RunService.Stepped:Wait() and hum.SeatPart do 
                    sno(etc["Root"])
                end
                etc["Root"].Parent:PivotTo(oldCF * CFrame.new(0,20,0))
                StopAllVelocity(etc["Root"].Parent)
                repeat
                    CreatureGrab:FireServer(RightDetector,etc["Root"],RightWeld)
                    task.wait(0.05)
                until RightWeld.Attachment0 or not hum.SeatPart
                for _ = 1,5 do 
                    unsno(etc["Root"])
                end
                if int.selectedButtonMode == "[BLITZ]" then 
                    task.wait(1) 
                    DestroyToy:FireServer(blob)
                    etc.LastBlob = nil
                end
            end
        end    
    })

    TarTab:AddButton({
        Name = "STOOP",
        Callback = function()
            bool.Running = false
        end    
    })

    TarTab:AddButton({
        Name = "Kick[SNO + BLOB]",
        Callback = function()
            local blob = FindBlob()
            local oldCF = char:GetPivot()
            etc.Root = game.Players[selectedPlrName].Character.HumanoidRootPart

            if not etc.Root then return end 
            if not blob then 
                blob = SpawnToy("CreatureBlobman")
                if not blob then return end 
                local Seat = FWD(blob,"VehicleSeat")
                Seat:Sit(hum)
                while Seat.Occupant ~= hum do task.wait() end 
            end

            local RightDetector,RightWeld,CreatureGrab = blob and blob.RightDetector, blob and blob.RightDetector and blob.RightDetector.RightWeld,blob and blob.BlobmanSeatAndOwnerScript.CreatureGrab
            local BodyPos = Instance.new("BodyPosition")
            BodyPos.Name = ("BlobSnoKickBodyPos")
            BodyPos.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
            BodyPos.Position = etc.Root.Position + Vector3.new(0,20,0)
            BodyPos.Parent = etc.Root
            BodyPos.P = 45000
            BodyPos.D = 500
            
            repeat
                task.wait(0.05)
                blob:PivotTo(etc.Root.CFrame * CFrame.new(0,5,5))
                sno(etc.Root)
            until (not hum or not hum.SeatPart or not etc.Root) or CheckForPartOwner(game.Players[selectedPlrName].Character.Head)

            for i = 1 ,10 do
                task.wait(0.01) 
                sno(etc.Root)
            end

            task.defer(StopAllVelocity,blob)
            unsno(etc.Root)
            blob:PivotTo(etc.Root.CFrame)

            repeat 
                CreatureGrab:FireServer(RightDetector,etc.Root,RightWeld)
                task.wait(0.05)
            until blob.RightDetector.RightWeld.Attachment0 or not hum.SeatPart

            blob:PivotTo(oldCF)
            Debris:AddItem(BodyPos,1)
            if int.selectedButtonMode == "[BLITZ]" then  
                task.wait(0.1)
                DestroyToy:FireServer(blob)
                etc.LastBlob = nil
            end
        end    
    })
    TarTab:AddSection({ Name = "<font color=\"rgb(255, 0, 0)\" size = \"24\"><b>SNOS GUCCI</b></font>"})
    TarTab:AddButton({
        Name = "Destroy Gucci[BLOB]📛",
        Callback = function()
            local blob = FindBlob() or SpawnToy("CreatureBlobman")
            if not blob then return end 
            local oldCF = char:GetPivot()
            etc.TargetPLR = game.Players:FindFirstChild(selectedPlrName)
            if not etc.TargetPLR then return end 
            etc.Root,etc.Hum = etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart"),etc.TargetPLR.Character:FindFirstChild("Humanoid")
            etc["Root"].Massless = false 
            FWD(blob,"VehicleSeat"):Sit(hum)
            while not hum.SeatPart do task.wait() end 
            local CreatureGrab = FWD(FWD(blob,"BlobmanSeatAndOwnerScript"),"CreatureGrab")
            local CreatureRelease = FWD(FWD(blob,"BlobmanSeatAndOwnerScript"),"CreatureRelease")
            local RightDetector = FWD(blob,"RightDetector")
            local RightWeld = RightDetector and FWD(RightDetector,"RightWeld")
            blob:PivotTo(etc["Root"].CFrame)
            task.wait(0.15)
            for _ = 1,15 do 
                CreatureGrab:FireServer(RightDetector,etc.Root,RightWeld)
                CreatureRelease:FireServer(RightWeld,etc["Root"])
                etc["Root"].Massless = false  
                for _ = 1,10 do 
                    etc["Hum"].Sit = true
                end
                task.wait()
            end
            char:PivotTo(oldCF)
        end    
    })

    TarTab:AddButton({
        Name = "Destroy Gucci[JUMP/SIT]📛",
        Callback = function()
            local blobs = {}
            local hum = FWD(char,"Humanoid")
            local oldCF = char:GetPivot()
            local TarInv = workspace:FindFirstChild(selectedPlrName.."SpawnedInToys")
            if TarInv then
                for _, v in pairs(TarInv:GetChildren()) do
                    if v.Name == "CreatureBlobman" or v.Name:sub(1,7) == "Tractor" then
                        table.insert(blobs, v)
                    end
                end
            end
            for _, plot in pairs(workspace.Plots:GetChildren()) do
                for _, owner in pairs(plot.PlotSign.ThisPlotsOwners:GetChildren()) do
                    if owner.Value == selectedPlrName then
                        local toyFolder = workspace.PlotItems:FindFirstChild(plot.Name)
                        if toyFolder then
                            for _, v in pairs(toyFolder:GetChildren()) do
                                if v.Name == "CreatureBlobman" or v.Name:sub(1,7) == "Tractor" then
                                    table.insert(blobs, v)
                                end
                            end
                        end
                    end
                end
            end
            if #blobs == 0 then
                return
            end
            for _, v in pairs(blobs) do
                for i = 1, 2 do
                    local seat = v:FindFirstChild("VehicleSeat")
                    if seat then
                        seat:Sit(hum)
                        while not seat.Occupant do task.wait() end 
                        char:PivotTo(oldCF)
                        hum:ChangeState(Enum.HumanoidStateType.Jumping)
                        task.defer(StopAllVelocity, char)
                        task.wait(0.1)
                    else 
                        break
                    end
                end
                StopAllVelocity(char)
                char:PivotTo(oldCF)
            end
        end    
    })

    TarTab:AddButton({
        Name = "BlackHoleBlobman",
        Callback = function()
            local targetinv = game.Workspace:WaitForChild(selectedPlrName.."SpawnedInToys")
            if not targetinv:FindFirstChild("CreatureBlobman") then return end
            local shurikens1 = {}
            for i = 1, 10 do
                local shuriken = SpawnToy("NinjaShuriken")
                if not shuriken then continue end 
                local StickyPart = FWD(shuriken,"StickyPart")
                sno(StickyPart)
                shuriken.Name = i
                task.delay(0.5,RenameInShop,shuriken,"NinjaShuriken","BlobmanBlackhole"..tostring(i))
                do
                    local BodyPos = Instance.new("BodyPosition")
                    BodyPos.Position = Vector3.new(math.random(-100,100),1e3,math.random(-100,100))
                    BodyPos.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
                    BodyPos.Parent = StickyPart
                end
                table.insert(shurikens1, StickyPart)
            end
            for _, shuriken in ipairs(shurikens1) do
                StickyPartEvent:FireServer(
                    shuriken,
                    targetinv.CreatureBlobman.Head,
                    CFrame.new(0,3,0,0,0,0,0,0,0,0,0,0)
                )
            end
        end    
    })
    TarTab:AddSection({ Name = "<font color=\"rgb(0, 0, 255)\" size = \"24\"><b>Loops</b></font>"})
    TarTab:AddDropdown({
        Name = "LoopGrab Type",
        Default = "[Ragdoll]",
        Options = {"[Ragdoll]","[UnRagdoll]"},
        Save = true,
        Flag = "LoopGrabType",
        Callback = function(Value)
            bool.DoRagdoll = (Value == "[Ragdoll]")
        end    
    })
    local counter = 0 
    TarTab:AddToggle({
        Name = "LoopGrab<font color=\"rgb(255, 0, 255)\"><b>[NEAR]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopGrabNear = Value 
            if bool.LoopGrabNear then
                etc.TargetPLR = selectedPlrName and game.Players:FindFirstChild(selectedPlrName)
                etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                local Pallete
                local function SpawnPallete()
                    Pallete = SpawnToy("PalletLightBrown")
                    if not Pallete then return end 
                    local SoundPart = FWD(Pallete,"SoundPart")
                    repeat sno(SoundPart) task.wait(0.05) until CFP(SoundPart,"PartOwner") or not bool.LoopGrabNear
                    for _,v in pairs(Pallete:GetDescendants()) do 
                        if v:IsA("BasePart") then 
                            v.CanCollide = false 
                            v.Transparency = 0.8
                        end
                    end
                    Pallete.Name = "RagdollPalete"
                    task.delay(1,RenameInShop,Pallete,"PalletLightBrown","Ragdoll")
                    local bv = Instance.new("BodyVelocity")
                    bv.MaxForce = Vector3.new(0,math.huge,0)
                    bv.Velocity = Vector3.new(0,900,0)
                    bv.Parent = Pallete.SoundPart
                    return Pallete
                end
                if etc.TargetPLR then 
                    if bool.DoRagdoll then 
                        Pallete = inv:FindFirstChild("RagdollPalete")
                        if not Pallete then 
                            Pallete = SpawnPallete()
                        end
                    end
                    local oldCF = hrp.CFrame
                    local BodyPos
                    task.spawn(function()
                        while bool.LoopGrabNear and RunService.RenderStepped:Wait() do 
                            etc.TargetPLR = selectedPlrName and game.Players:FindFirstChild(selectedPlrName)
                            if not etc.TargetPLR then continue end
                            etc.Head = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Head")
                            etc.Root = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("HumanoidRootPart")
                            etc.Hum = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Humanoid")
                            BodyPos = etc.Root and etc.Root:FindFirstChild("BodyPosition")
                            if not(BodyPos) and (etc.Root) then 
                                BodyPos = Instance.new("BodyPosition")
                                BodyPos.MaxForce = Vector3.new(Huge())
                                BodyPos.Parent = etc.Root
                                BodyPos.P = 45000
                                BodyPos.D = 500
                                BodyPos.Position = hrp.Position + Vector3.new(0,1,10)
                            end
                            if (etc.Head and etc.Hum and etc.Root and etc.Hum.Health ~= 0) and GetMagnitude(hrp,etc["Root"]) <= 30 then  
                                sno(etc["Root"])
                                RunService.RenderStepped:Wait()
                                if bool.DoRagdoll then 
                                    Pallete = inv:FindFirstChild("RagdollPalete")
                                    if not Pallete then 
                                        Pallete = SpawnPallete()
                                    end
                                    if CFP(etc["Hum"],"Ragdolled") and not(etc["Hum"].Ragdolled.Value) then 
                                        Pallete.SoundPart.Position = etc["Root"].Position
                                    end
                                end
                                sno(etc["Root"])
                            else 
                                if not etc["Head"] or not etc["Hum"] or etc["Hum"].Health == 0 then  
                                    etc.TargetPLR.CharacterAdded:Wait()
                                    etc.Head = FWD(etc.TargetPLR.Character,"Head",5)
                                    etc.Hum = FWD(etc.TargetPLR.Character,"Humanoid",5)
                                    BodyPos = Instance.new("BodyPosition")
                                    BodyPos.MaxForce = Vector3.new(Huge())
                                    BodyPos.Parent = etc.Root
                                    BodyPos.P = 10000
                                    BodyPos.D = 100
                                    BodyPos.Position = hrp.Position + Vector3.new(0,1,10)
                                end
                                oldCF = hrp.CFrame
                                while (etc["Head"] and etc["Hum"] and etc['Hum'].Health ~= 0) and task.wait(0.01) and bool.LoopGrabNear do 
                                    etc["TargetPLR"] = selectedPlrName and Players:FindFirstChild(selectedPlrName)
                                    if not etc["TargetPLR"] then continue end
                                    if etc["TargetPLR"].InPlot.Value then continue end
                                    etc["Head"] = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Head")
                                    etc["Hum"] = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Humanoid")
                                    if not(etc["Head"]) or not(etc["Hum"]) then break end 
                                    char:PivotTo(etc["Head"].CFrame * CFrame.new(0,10,0))
                                    sno(etc["Head"])
                                    if CheckForPartOwner(etc["Head"]) then 
                                        break
                                    end
                                end
                                hrp.CFrame = oldCF
                                if etc.Head and CheckForPartOwner(etc["Head"]) then 
                                    for _,v in pairs(etc.TargetPLR.Character:GetChildren()) do
                                        if v:IsA("BasePart") then
                                            v.CFrame = CFrame.new(BodyPos.Position)
                                        end
                                    end
                                end
                            end
                        end
                        if etc["Root"] then 
                            for _,v in pairs(etc["Root"]:GetChildren()) do 
                                if v.Name == "BodyPosition" then 
                                    v:Destroy()
                                end
                            end
                        end
                    end)
                end
            end
        end    
    })
    TarTab:AddToggle({
        Name = "LoopGrab<font color=\"rgb(100, 255, 200)\"><b>[KICK]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopGrabKick = Value  
            if bool.LoopGrabKick then 
                task.spawn(function()
                    etc.TargetPLR = selectedPlrName and game.Players:FindFirstChild(selectedPlrName)
                    etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                    etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                    local Pallete = inv:FindFirstChild("RagdollPalete")
                    local function SpawnPallete()
                        Pallete = SpawnToy("PalletLightBrown")
                        if not Pallete then return end 
                        local SoundPart = FWD(Pallete,"SoundPart")
                        repeat sno(SoundPart) task.wait(0.05) until CFP(SoundPart,"PartOwner") or not bool.LoopGrabNear
                        for _,v in pairs(Pallete:GetDescendants()) do 
                            if v:IsA("BasePart") then 
                                v.CanCollide = false 
                                v.Transparency = 0.8
                            end
                        end
                        Pallete.Name = "RagdollPalete"
                        task.delay(1,RenameInShop,Pallete,"PalletLightBrown","Ragdoll")
                        local bv = Instance.new("BodyVelocity")
                        bv.MaxForce = Vector3.new(0,math.huge,0)
                        bv.Velocity = Vector3.new(0,900,0)
                        bv.Parent = Pallete.SoundPart
                        return Pallete
                    end
                    if not(Pallete) and bool.DoRagdoll then 
                        Pallete = SpawnPallete()
                    end
                    while bool.LoopGrabKick and RunService.RenderStepped:Wait() do 
                        etc.TargetPLR = selectedPlrName and game.Players:FindFirstChild(selectedPlrName)
                        if not etc.TargetPLR then continue end
                        etc.Head = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Head")
                        etc.Root = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("HumanoidRootPart")
                        etc.Hum = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Humanoid")
                        BodyPos = etc.Root and etc.Root:FindFirstChild("BodyPosition")
                        if (etc.Head and etc.Hum and etc.Root and etc.Hum.Health ~= 0) and GetMagnitude(hrp,etc["Root"]) <= 30 then  
                            if not(BodyPos) then 
                                BodyPos = Instance.new("BodyPosition")
                                BodyPos.MaxForce = Vector3.new(Huge())
                                BodyPos.Parent = etc.Root
                                BodyPos.P = 75000
                                BodyPos.D = 12000
                                BodyPos.Position = hrp.Position + Vector3.new(0,15,0)
                            end
                            BodyPos.Position = hrp.Position + Vector3.new(0,15,0)
                            if (etc.Root.Position.Y <= hrp.Position.Y + 15) and etc["TargetPLR"].IsHeld.Value then
                                for _,v in pairs(etc.TargetPLR.Character:GetChildren()) do
                                    if v:IsA("Part") then
                                        v.CFrame = hrp.CFrame * CFrame.new(0, 15, 0)
                                    end
                                end
                            end
                            etc["Root"].AssemblyAngularVelocity,etc["Root"].AssemblyLinearVelocity = Vector3.zero,Vector3.zero
                            etc.Hum.PlatformStand = true
                            sno(etc["Root"])
                            RunService.RenderStepped:Wait()
                            sno(etc["Root"])
                            unsno(etc["Root"])
                            RunService.RenderStepped:Wait()
                            sno(etc["Root"])
                            if bool.DoRagdoll then 
                                Pallete = inv:FindFirstChild("RagdollPalete")
                                if not Pallete then 
                                    Pallete = SpawnPallete()
                                end
                                if CFP(etc["Hum"],"Ragdolled") and not(etc["Hum"].Ragdolled.Value) then 
                                    Pallete.SoundPart.Position = etc["Root"].Position
                                end
                            end
                            RunService.RenderStepped:Wait()
                            unsno(etc["Root"])
                        else 
                            if not etc["Head"] or not etc["Hum"] or etc["Hum"].Health == 0 then  
                                etc.TargetPLR.CharacterAdded:Wait()
                                etc.Head = FWD(etc.TargetPLR.Character,"Head",5)
                                etc.Hum = FWD(etc.TargetPLR.Character,"Humanoid",5)
                                BodyPos = Instance.new("BodyPosition")
                                BodyPos.MaxForce = Vector3.new(Huge())
                                BodyPos.Parent = etc.Root
                                BodyPos.P = 75000
                                BodyPos.D = 12000
                                BodyPos.Position = hrp.Position + Vector3.new(0,15,0)
                            end
                            oldCF = hrp.CFrame
                            while (etc["Head"] and etc["Hum"] and etc['Hum'].Health ~= 0) and task.wait(0.01) and bool.LoopGrabKick do 
                                etc["TargetPLR"] = selectedPlrName and Players:FindFirstChild(selectedPlrName)
                                if not etc["TargetPLR"] then break end
                                if etc["TargetPLR"].InPlot.Value then continue end
                                etc["Head"] = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Head")
                                etc["Hum"] = etc["TargetPLR"].Character and etc["TargetPLR"].Character:FindFirstChild("Humanoid")
                                if not(etc["Head"]) or not(etc["Hum"]) then break end 
                                char:PivotTo(etc["Head"].CFrame * CFrame.new(0,10,0))
                                sno(etc["Head"])
                                if CheckForPartOwner(etc["Head"]) then 
                                    break
                                end
                            end
                            hrp.CFrame = oldCF
                            if etc.Head and CheckForPartOwner(etc["Head"]) then 
                                for _,v in pairs(etc.TargetPLR.Character:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.CFrame = hrp.CFrame * CFrame.new(0, 15, 0)
                                    end
                                end
                            end
                        end
                    end
                    if etc["Root"] then 
                        for _,v in pairs(etc["Root"]:GetChildren()) do 
                            if v.Name == "BodyPosition" then 
                                v:Destroy()
                            end
                        end
                    end
                end)
            end
        end    
    })
    TarTab:AddToggle({
        Name = "Loop Banana Ragdoll",
        Default = false,
        Callback = function(Value)
            bool.LoopRagdoll = Value 
            if Value then 
                task.spawn(function()
                    local banana,SoundPart
                    etc.TargetPLR = Players:FindFirstChild(selectedPlrName)
                    if not etc.TargetPLR then Notify("Error","Target is not exists!") return end 
                    etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                    local AlignPos
                    local AtachNew
                    while bool.LoopRagdoll and task.wait() do 
                        etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Left Leg")
                        if not etc.Root then continue end 
                        banana = inv:FindFirstChild("FoodBanana")
                        SoundPart = banana and banana:FindFirstChild("SoundPart")
                        if not SoundPart then 
                            for _,v in pairs(inv:GetChildren()) do 
                                if v.Name == "FoodBanana" then 
                                    DestroyToy:FireServer(v)
                                end 
                            end 
                            banana = SpawnToy("FoodBanana")
                            if not banana then continue end
                            SoundPart = FWD(banana,"SoundPart",5)
                            if not SoundPart then continue end
                            FWD(FWD(banana,"HoldPart"),"HoldItemRemoteFunction"):InvokeServer(banana,char)
                            RS.HoldEvents.Use:FireServer(banana)
                            while CFP(banana,"EdiblePart") do task.wait() end
                            RS.HoldEvents.Use:FireServer(banana)
                            FWD(FWD(banana,"HoldPart"),"DropItemRemoteFunction"):InvokeServer(banana,char:GetPivot() * CFrame.new(0,15,-10),Vector3.zero)
                            repeat 
                                task.wait(0.01)
                                SoundPart = banana and banana:FindFirstChild("SoundPart")
                                if not SoundPart then break end 
                                sno(SoundPart)
                            until not SoundPart or CFP(SoundPart,"PartOwner")
                            unsno(SoundPart)
                            local Atach = Instance.new("Attachment")
                            Atach.Parent = SoundPart
                            AlignPos = Instance.new("AlignPosition")
                            AlignPos.Responsiveness = 100
                            AlignPos.Parent = SoundPart
                            AlignPos.Attachment0 = Atach
                        end
                        for _,v in pairs(banana:GetChildren()) do 
                            if CFP(v,"PartOwner") and not CheckNetworkOwnerOnPart(v) then 
                                DestroyToy:FireServer(banana) 
                                banana = nil 
                                break 
                            end
                        end
                        if not banana then continue end 
                        AlignPos = SoundPart:FindFirstChild("AlignPosition")
                        if not AlignPos then DestroyToy:FireServer(banana) banana = nil continue end
                        AtachNew = etc.Root and etc.Root:FindFirstChild("LeftFootAttachment")
                        if not AtachNew then continue end 
                        AlignPos.Attachment1 = AtachNew
                    end 
                end)
            else 
                if banana then 
                    if AlignPos then 
                        AlignPos:Destroy()
                    end 
                    DestroyToy:FireServer(banana)
                end
            end 
        end    
    })

    -- TarTab:AddDropdown({
    --     Name = "Mode",
    --     Default = "[2Hand]",
    --     Options = {"[1Hand]","[2Hand]","[INPUTLAG]"},
    --     Callback = function(Value)
    --         int.selectedMode = Value
    --     end    
    -- })
    -- int.delay1 = 0.05
    -- int.delay2 = 0
    -- TarTab:AddSlider({
    --     Name = "Delay1",
    --     Min = 0,
    --     Max = 0.15,
    --     Default = 0.05,
    --     Color = Color3.fromRGB(0, 0, 255),
    --     Increment = 0.001,
    --     ValueName = "bananas",
    --     Callback = function(Value)
    --         int.delay1 = Value
    --     end    
    -- })


    -- TarTab:AddSlider({
    --     Name = "Delay2",
    --     Min = 0,
    --     Max = 0.15,
    --     Default = 0,
    --     Color = Color3.fromRGB(0, 0, 255),
    --     Increment = 0.001,
    --     ValueName = "bananas",
    --     Callback = function(Value)
    --         int.delay2 = Value
    --     end    
    -- })


    -- TarTab:AddToggle({
    --     Name = "LoopKick<font color=\"rgb(1, 250, 0)\"><b>[DELAY APPLY]</b></font>",
    --     Default = false,
    --     Callback = function(Value)
    --     bool.LoopKickWithDelay = Value 
    --     local blob = FindBlob()
    --     local CreatureGrab,CreatureDrop,CreatureRelease = blob and blob.BlobmanSeatAndOwnerScript.CreatureGrab,blob and blob.BlobmanSeatAndOwnerScript.CreatureDrop,blob and blob.BlobmanSeatAndOwnerScript.CreatureRelease
    --     local LeftDetector,RightDetector = blob and blob.LeftDetector,blob and blob.RightDetector
    --     local TargetPLR = selectedPlrName and  game.Players:FindFirstChild(selectedPlrName)
    --     local Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --     local Head = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("Head")
    --     local oldCF = char:GetPivot()
    --     if not blob or not TargetPLR then return end 
    --         if int.selectedMode == "[1Hand]" then  
    --             while bool.LoopKickWithDelay do 
    --                 Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                 if Root and TargetPLR.Character and TargetPLR.Character:FindFirstChild("Humanoid") and TargetPLR.Character.Humanoid.Health ~= 0 then 
    --                     CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                     task.wait(int.delay1)
    --                     CreatureDrop:FireServer(RightDetector.RightWeld,Root)
    --                     task.wait(int.delay2)
    --                 else 
    --                     if not game.Players:FindFirstChild(selectedPlrName) then 
    --                         break 
    --                     end
    --                     oldCF = char:GetPivot()
    --                     TargetPLR.CharacterAdded:Wait()
    --                     Root = FWD(TargetPLR.Character,"HumanoidRootPart",5)
    --                     Head = FWD(TargetPLR.Character,"Head",5)
    --                     if Root then  
    --                         RightDetector.RightWeld.Attachment0 = nil
    --                         repeat  
    --                             char:PivotTo(Root.CFrame * CFrame.new(0,10,0) * CFrame.new(Root.AssemblyLinearVelocity * 0.4))
    --                             CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                             task.wait(0.07)
    --                         until RightDetector.RightWeld.Attachment0 or not hum.SeatPart or not bool.LoopKickWithDelay
    --                         char:PivotTo(oldCF)
    --                         task.wait(0.45)
    --                         CreatureDrop:FireServer(RightDetector.RightWeld,Root)
    --                         task.wait(0.05)
    --                     end
    --                 end
    --             end
    --         elseif int.selectedMode == "[2Hand]" then  
    --             while bool.LoopKickWithDelay do 
    --                 Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                 if Root and TargetPLR.Character and TargetPLR.Character:FindFirstChild("Humanoid") and TargetPLR.Character.Humanoid.Health ~= 0 then 
    --                     CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                     task.wait(int.delay1)
    --                     CreatureDrop:FireServer(RightDetector.RightWeld,Root)
    --                     task.wait(int.delay2)
    --                     CreatureGrab:FireServer(LeftDetector,Root,LeftDetector.LeftWeld)
    --                     task.wait(int.delay1)
    --                     CreatureDrop:FireServer(LeftDetector.LeftWeld,Root)
    --                     task.wait(int.delay2)
    --                 else 
    --                     if not game.Players:FindFirstChild(selectedPlrName) then 
    --                         break 
    --                     end
    --                     oldCF = char:GetPivot()
    --                     TargetPLR.CharacterAdded:Wait()
    --                     Root = FWD(TargetPLR.Character,"HumanoidRootPart",5)
    --                     Head = FWD(TargetPLR.Character,"Head",5)
    --                     if Root then  
    --                         RightDetector.RightWeld.Attachment0 = nil
    --                         repeat  
    --                             char:PivotTo(Root.CFrame * CFrame.new(0,10,0) * CFrame.new(Root.AssemblyLinearVelocity * 0.4))
    --                             CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                             task.wait(0.07)
    --                         until RightDetector.RightWeld.Attachment0 or not hum.SeatPart or not bool.LoopKickWithDelay
    --                         char:PivotTo(oldCF)
    --                         task.wait(0.45)
    --                         CreatureDrop:FireServer(RightDetector.RightWeld,Root)
    --                         task.wait(0.05)
    --                     end
    --                 end
    --             end
    --         elseif int.selectedMode == "[INPUTLAG]" then 
    --             while bool.LoopKickWithDelay do
    --                 Root = TargetPLR and TargetPLR.Character and TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                 if Root and TargetPLR.Character and TargetPLR.Character:FindFirstChild("Humanoid") and TargetPLR.Character.Humanoid.Health ~= 0 then  
    --                     CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                     task.wait(int.delay1)
    --                     CreatureRelease:FireServer(RightDetector.RightWeld,Root)
    --                     RightDetector.RightWeld.Attachment0 = Root.RootAttachment
    --                     task.wait(int.delay2)
    --                 else 
    --                     if not game.Players:FindFirstChild(selectedPlrName) then 
    --                         break 
    --                     end
    --                     oldCF = char:GetPivot()
    --                     TargetPLR.CharacterAdded:Wait()
    --                     Root = FWD(TargetPLR.Character,"HumanoidRootPart",5)
    --                     Head = FWD(TargetPLR.Character,"Head",5)
    --                     if Root then  
    --                         RightDetector.RightWeld.Attachment0 = nil
    --                         repeat  
    --                             char:PivotTo(Root.CFrame * CFrame.new(0,10,0) * CFrame.new(Root.AssemblyLinearVelocity * 0.4))
    --                             CreatureGrab:FireServer(RightDetector,Root,RightDetector.RightWeld)
    --                             task.wait(0.07)
    --                         until RightDetector.RightWeld.Attachment0 or not hum.SeatPart or not bool.LoopKickWithDelay
    --                         char:PivotTo(oldCF)
    --                         task.wait(0.45)
    --                         CreatureDrop:FireServer(RightDetector.RightWeld,Root)
    --                         task.wait(0.05)
    --                     end
    --                 end
    --             end 
    --         end
    --     end    
    -- })

    Toggles["LockSettings"] = TarTab:AddDropdown({
        Name = "LOCK TP SETTING",
        Default = "[GRAB]",
        Options = {"[GRAB]","[WITHOUTGRAB]"},
        Save = true,
        Flag = "LockSettings",
        Callback = function(Value)
            int.selectedMode23 = Value
        end    
    })
    TarTab:AddToggle({
        Name = "LoopBLOB<font color=\"rgb(255, 0, 255)\"><b>[LOCK]</b></font>",
        Default = false,
        Callback = function(Value)
            bool.LoopKickMax2 = Value 
            if bool.LoopKickMax2 then
                local blob = FindBlob()
                local CreatureGrab,CreatureDrop,CreatureRelease = blob and blob.BlobmanSeatAndOwnerScript.CreatureGrab,blob and blob.BlobmanSeatAndOwnerScript.CreatureDrop,blob and blob.BlobmanSeatAndOwnerScript.CreatureRelease
                local LeftDetector,RightDetector = blob and blob.LeftDetector,blob and blob.RightDetector
                etc.TargetPLR = selectedPlrName and  game.Players:FindFirstChild(selectedPlrName)
                etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                local oldCF = char:GetPivot()
                if not etc.Root or not etc.TargetPLR or not blob then return end
                local BodyPos = etc.Root:FindFirstChild("KickBodyPos")
                if BodyPos then  
                    for _,v in pairs(etc.Root:GetChildren()) do 
                        if v.Name == "KickBodyPos" then  
                            v:Destroy()
                        end
                    end
                end 
                BodyPos = Instance.new("BodyPosition")
                BodyPos.Name = "KickBodyPos"
                BodyPos.MaxForce = Vector3.new(Huge())
                BodyPos.Position = blob.LeftDetector.Position + Vector3.new(0,0,-5)
                BodyPos.Parent = etc.Root
                BodyPos.P = 45000
                BodyPos.D = 500
                if int.selectedMode23 == "[GRAB]" then 
                    repeat
                        task.wait(0.01)
                        if etc.TargetPLR.InPlot.Value then continue end 
                        etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                        if not etc.Root then  
                            break  
                        end
                        char:PivotTo(etc.Root.CFrame * CFrame.new(etc.Root.AssemblyLinearVelocity * 0.1))
                        sno(etc.Root)
                    until not (etc.Head or etc.Root) or (etc.Head:FindFirstChild("PartOwner") and etc.Head:FindFirstChild("PartOwner").Value == plr.Name) or not bool.LoopKickMax2 or not hum.SeatPart
                    char:PivotTo(oldCF)
                    if etc.Head and etc.Head:FindFirstChild("PartOwner") then 
                        task.defer(function()
                            for i = 1,10 do 
                                etc["Root"].CFrame = oldCF * CFrame.new(0,0,-5)
                                blob.HumanoidCreature.PlatformStand = false
                                blob.HumanoidCreature:ChangeState(Enum.HumanoidStateType.GettingUp)
                            end 
                        end)
                    end
                end
                if (etc.Head and etc.Root) and (CheckForPartOwner(etc.Head) or int.selectedMode23 ~= "[GRAB]") then
                    etc["Root"].CFrame = blob.LeftDetector.CFrame * CFrame.new(0,0,-5)
                    task.defer(function()
                        ChangeCollision(etc.TargetPLR.Character,false)
                        StopAllVelocity(blob)
                        StopAllVelocity(etc.TargetPLR.Character)
                        BodyPos:Destroy()
                    end)
                    cons["LOCKTHREAD"] = RunService.Stepped:Connect(function()
                        etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                        if not etc.Root then  
                            return 
                        end
                        CreatureGrab:FireServer(LeftDetector,etc.Root,LeftDetector.LeftWeld)
                        RunService.Heartbeat:Wait()
                        CreatureRelease:FireServer(LeftDetector.LeftWeld,etc.Root)
                        LeftDetector.LeftWeld.Attachment0 = etc["Root"].RootAttachment
                    end)
                end
            else  
                cons:disc("LOCKTHREAD")
                if etc.Root then    
                    if etc.Root:FindFirstChild("KickBodyPos") then   
                        etc.Root:FindFirstChild("KickBodyPos"):Destroy()
                    end
                end
            end
        end    
    })

    TarTab:AddToggle({
        Name = "Use SNO to kick ?",
        Default = false,
        Callback = function(Value)
            bool.UseSNO = Value 
        end    
    })

    -- int.CounterOfSno = 0
    -- TarTab:AddToggle({
    --     Name = "LoopKick<font color=\"rgb(255, 0, 0)\"><b>[INIT + PINGY]</b></font>",
    --     Default = false,
    --     Callback = function(Value)
    --         bool.LoopKickMax1 = Value 
    --         if bool.LoopKickMax1 then
    --             cons["LoopKick"] = task.spawn(function()
    --                 local blob = FindBlob()
    --                 local CreatureGrab,CreatureDrop,CreatureRelease = blob and blob.BlobmanSeatAndOwnerScript.CreatureGrab,blob and blob.BlobmanSeatAndOwnerScript.CreatureDrop,blob and blob.BlobmanSeatAndOwnerScript.CreatureRelease
    --                 local LeftDetector,RightDetector = blob and blob.LeftDetector,blob and blob.RightDetector
    --                 etc.TargetPLR = selectedPlrName and  game.Players:FindFirstChild(selectedPlrName)
    --                 etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                 etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
    --                 etc.Hum = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Humanoid")
    --                 local oldCF = hrp.CFrame
    --                 if not etc.Root or not etc.TargetPLR or not blob then return end
    --                 local BodyPos = etc.Root:FindFirstChild("KickBodyPos1")
    --                 if BodyPos then  
    --                     for _,v in pairs(etc.Root:GetChildren()) do 
    --                         if v.Name == "KickBodyPos1" then  
    --                             v:Destroy()
    --                         end
    --                     end
    --                 end 
    --                 BodyPos = Instance.new("BodyPosition")
    --                 BodyPos.Name = "KickBodyPos1"
    --                 BodyPos.MaxForce = Vector3.new(Huge())
    --                 BodyPos.Position = oldCF.Position + Vector3.new(0,25,0)
    --                 BodyPos.Parent = etc.Root
    --                 BodyPos.P = 45000
    --                 BodyPos.D = 500
    --                 repeat
    --                     task.wait(0.01)
    --                     etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                     etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
    --                     if etc["TargetPLR"].InPlot.Value then continue end 
    --                     if not etc.Root then  
    --                         break  
    --                     end
    --                     blob:PivotTo(etc.Root.CFrame * CFrame.new(etc.Root.AssemblyLinearVelocity * 0.1))
    --                     sno(etc.Root)
    --                 until (not etc.Root or not etc.Head) or CheckForPartOwner(etc.Head) or not bool.LoopKickMax1 or not hum.SeatPart
    --                 char:PivotTo(oldCF)
    --                 if (etc.Root and etc.Head) and CheckForPartOwner(etc.Head) then
    --                     CreatureGrab:FireServer(RightDetector,etc.Root,RightDetector.RightWeld)
    --                     task.defer(function()
    --                         for _,v in pairs(etc["TargetPLR"].Character:GetChildren()) do 
    --                             if v:IsA("BasePart") then 
    --                                 v.CFrame = oldCF * CFrame.new(0,20,0)
    --                             end
    --                             blob.HumanoidCreature.PlatformStand = false
    --                             blob.HumanoidCreature:ChangeState(Enum.HumanoidStateType.GettingUp)
    --                         end
    --                         StopAllVelocity(char)
    --                         StopAllVelocity(blob)
    --                         StopAllVelocity(etc.TargetPLR.Character)
    --                     end)
    --                     StopVelocityF()
    --                     unsno(etc["Root"])
    --                     while RunService.Heartbeat:Wait() do 
    --                         etc.TargetPLR = Players:FindFirstChild(selectedPlrName)
    --                         etc.Root = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                         etc.Hum = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("Humanoid")
    --                         if not etc.Root or not etc.Hum or etc.Hum.Health == 0 then 
    --                             etc["TargetPLR"].CharacterAdded:Wait()
    --                             etc.Root = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                             etc.Hum = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("Humanoid")
    --                             oldCF = char:GetPivot()
    --                                 BodyPos = Instance.new("BodyPosition")
    --                                 BodyPos.Name = "KickBodyPos1"
    --                                 BodyPos.MaxForce = Vector3.new(Huge())
    --                                 BodyPos.Position = oldCF.Position + Vector3.new(0,25,0)
    --                                 BodyPos.Parent = etc.Root
    --                                 BodyPos.P = 45000
    --                                 BodyPos.D = 500
    --                             repeat
    --                                 task.wait(0.01)
    --                                 etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
    --                                 etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
    --                                 if etc["TargetPLR"].InPlot.Value then continue end 
    --                                 if not etc.Root then break end
    --                                 blob:PivotTo(etc.Root.CFrame * CFrame.new(etc.Root.AssemblyLinearVelocity * 0.1))
    --                                 sno(etc.Root)
    --                             until (not etc.Root or not etc.Head) or CheckForPartOwner(etc.Head) or not bool.LoopKickMax1 or not hum.SeatPart
    --                             char:PivotTo(oldCF)
    --                             task.defer(function()
    --                                 StopAllVelocity(blob)
    --                                 StopAllVelocity(etc.TargetPLR.Character)
    --                                 for _,v in pairs(etc["TargetPLR"].Character:GetChildren()) do 
    --                                     if v:IsA("BasePart") then 
    --                                         v.CFrame = oldCF * CFrame.new(0,20,0)
    --                                     end
    --                                     blob.HumanoidCreature.PlatformStand = false
    --                                     blob.HumanoidCreature:ChangeState(Enum.HumanoidStateType.GettingUp)
    --                                 end 
    --                             end)
    --                         end
    --                         CreatureGrab:FireServer(RightDetector,etc.Root,RightDetector.RightWeld)
    --                         RunService.Heartbeat:Wait()
    --                         CreatureDrop:FireServer(RightDetector.RightWeld,etc.Root) 
    --                         if bool.UseSNO then
    --                             int.CounterOfSno += 1  
    --                             if int.CounterOfSno >= 100 then  
    --                                 int.CounterOfSno = 0 
    --                                 sno(etc.Root)
    --                                 unsno(etc.Root)
    --                             end
    --                         end
    --                     end
    --                 end
    --             end)
    --         else 
    --             cons:cancel("LoopKick")
    --             if etc.Root and etc.Root:FindFirstChild("KickBodyPos1") then   
    --                 etc.Root:FindFirstChild("KickBodyPos1"):Destroy()
    --             end
    --         end
    --     end    
    -- })
    TarTab:AddToggle({
    Name = "LoopKick<font color=\"rgb(255, 0, 0)\"><b>[INIT + PINGY]</b></font>",
    Default = false,
    Callback = function(Value)
        bool.LoopKickMax1 = Value 
        if Value then
            local blob = FindBlob()
            local CreatureGrab,CreatureDrop,CreatureRelease = blob and blob.BlobmanSeatAndOwnerScript.CreatureGrab,blob and blob.BlobmanSeatAndOwnerScript.CreatureDrop,blob and blob.BlobmanSeatAndOwnerScript.CreatureRelease
            local LeftDetector,RightDetector = blob and blob.LeftDetector,blob and blob.RightDetector
            etc.TargetPLR = selectedPlrName and  game.Players:FindFirstChild(selectedPlrName)
            etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
            etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
            etc.Hum = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Humanoid")
            local oldCF = hrp.CFrame
            local function CREATEBODYPOS(Parent)
                for _,v in pairs(Parent:GetChildren()) do 
                    if v.Name == "KickBodyPos1" then  
                        v:Destroy()
                    end
                end
                BodyPos = Instance.new("BodyPosition")
                BodyPos.Name = "KickBodyPos1"
                BodyPos.MaxForce = Vector3.new(Huge())
                BodyPos.Position = oldCF.Position + Vector3.new(0,25,0)
                BodyPos.Parent = Parent
                BodyPos.P = 45000
                BodyPos.D = 500
            end
            local IsPreparing = true
            local IsFirst = true
            if not etc.Root or not etc.TargetPLR or not blob then return end
            cons["LoopKickINIT"] = task.spawn(function()
                while RunService.Heartbeat:Wait() do 
                    etc.TargetPLR = selectedPlrName and game.Players:FindFirstChild(selectedPlrName)
                    if not etc.TargetPLR then 
                        IsPreparing = true 
                        continue 
                    end
                    etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                    etc.Head = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                    etc.Hum = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                    if not etc.Root or not etc.Hum or etc.Hum.Health == 0 or IsFirst then 
                        IsPreparing = true 
                        if not IsFirst then
                            etc["TargetPLR"].CharacterAdded:Wait()
                        end
                        IsFirst = false
                        etc.Root = etc.TargetPLR and FWD(etc.TargetPLR.Character,"HumanoidRootPart",5)
                        etc.Hum = etc.TargetPLR and FWD(etc.TargetPLR.Character,"Humanoid",5)
                        oldCF = char:GetPivot()
                        CREATEBODYPOS(etc.Root)
                        repeat
                            task.wait(0.01)
                            etc.Root = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                            etc.Head = etc.TargetPLR and etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("Head")
                            if etc["TargetPLR"].InPlot.Value then continue end 
                            if not etc.Root then break end
                            blob:PivotTo(etc.Root.CFrame * CFrame.new(etc.Root.AssemblyLinearVelocity * 0.1))
                            sno(etc.Root)
                        until (not etc.Root or not etc.Head) or CheckForPartOwner(etc.Head) or not bool.LoopKickMax1 or not hum.SeatPart
                        char:PivotTo(oldCF)
                        IsPreparing = false
                        StopAllVelocity(blob)
                        StopAllVelocity(etc.TargetPLR.Character)
                        for _,v in pairs(etc["TargetPLR"].Character:GetChildren()) do 
                            if v:IsA("BasePart") then 
                                v.CFrame = oldCF * CFrame.new(0,20,0)
                            end
                            blob.HumanoidCreature.PlatformStand = false
                            blob.HumanoidCreature:ChangeState(Enum.HumanoidStateType.GettingUp)
                        end 
                    end
                end
            end)
            cons["LoopKickMain"] = RunService.Heartbeat:Connect(function()
                if not bool.LoopKickMax1 or IsPreparing then return end 
                etc.Root = etc.TargetPLR.Character and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                if not etc.Root then return end
                CreatureGrab:FireServer(RightDetector,etc.Root,RightDetector.RightWeld)
                RunService.Heartbeat:Wait()
                CreatureDrop:FireServer(RightDetector.RightWeld,etc.Root) 
                if bool.UseSNO then
                    int.CounterOfSno += 1  
                    if int.CounterOfSno >= 100 then  
                        int.CounterOfSno = 0 
                        sno(etc.Root)
                        unsno(etc.Root)
                    end
                end
            end)
        else
            cons:cancel("LoopKickINIT")
            cons:disc("LoopKickMain")
            if etc.Root and etc.Root:FindFirstChild("KickBodyPos1") then   
                etc.Root:FindFirstChild("KickBodyPos1"):Destroy()
            end
        end
    end
    })
    local function getHRP(char)
        return char and char:FindFirstChild("HumanoidRootPart")
    end
    local function ForceDeathNotG(hum)  
        hum.BreakJointsOnDeath = false
        hum:ChangeState(Enum.HumanoidStateType.Dead)
        hum.Sit = false 
        hum.Jump = true  
    end 
    TarTab:AddToggle({
            Name = "LoopKill<font color=\"rgb(255, 255, 0)\"><b>[BLOB]</b></font>",
            Default = false,
            Callback = function(Value)
                bool.BypassAntiBlob1 = Value
                if Value then
                    etc.TargetPLR = game.Players:FindFirstChild(selectedPlrName)
                    etc.Root = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("HumanoidRootPart")
                    etc.Hum = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                    local oldCF = char:GetPivot()
                    local blob = FindBlob() or SpawnToy("CreatureBlobman")
                    FWD(blob,"VehicleSeat"):Sit(hum)
                    local CreatureGrab = FWD(FWD(blob,"BlobmanSeatAndOwnerScript"),"CreatureGrab")
                    local CreatureRelease = FWD(FWD(blob,"BlobmanSeatAndOwnerScript"),"CreatureRelease")
                    local RightDetector = FWD(blob,"RightDetector")
                    local RightWeld = RightDetector and FWD(RightDetector,"RightWeld")
                    while not hum.SeatPart do task.wait() end 
                    task.spawn(function()
                        while bool.BypassAntiBlob1 and task.wait() do  
                            oldCF = char:GetPivot() 
                            etc.Root = getHRP(etc.TargetPLR.Character)
                            etc.Hum = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                            if (not etc.Root or not etc.Hum) or _G.IsAboveLimit(etc.TargetPLR) or etc.Hum.Health == 0  then continue end 
                            while not RightWeld.Attachment0 and bool.BypassAntiBlob1 do
                                task.wait(0.05)
                                etc.Root = getHRP(etc.TargetPLR.Character)
                                etc.Hum = etc.TargetPLR and etc.TargetPLR.Character:FindFirstChild("Humanoid")
                                if (not etc.Root or not etc.Hum) or _G.IsAboveLimit(etc.TargetPLR) or etc.Hum.Health == 0  then char:PivotTo(oldCF) break end 
                                if not hum.SeatPart then  
                                    inv:FindFirstChild("CreatureBlobman").VehicleSeat:Sit(hum)
                                end 
                                char:PivotTo(etc.Root.CFrame)
                                hrp.AssemblyLinearVelocity = Vector3.zero
                                hrp.AssemblyAngularVelocity = Vector3.zero
                                CreatureGrab:FireServer(RightDetector,etc.Root,RightWeld)
                            end 
                            CreatureRelease:FireServer(RightWeld,etc.Root)
                            char:PivotTo(etc.Root.CFrame)
                            for _ = 1,10 do 
                                hrp.AssemblyLinearVelocity = Vector3.zero
                                hrp.AssemblyAngularVelocity = Vector3.zero
                                CreatureGrab:FireServer(RightDetector,etc.Root,RightWeld)
                                CreatureRelease:FireServer(RightWeld,etc.Root)
                                if isnetworkowner(etc.Root) then  
                                    ForceDeathNotG(etc.Hum)
                                end 
                                task.wait()
                            end 
                            char:PivotTo(oldCF)
                            hrp.AssemblyLinearVelocity = Vector3.zero
                            hrp.AssemblyAngularVelocity = Vector3.zero
                        end 
                    end)
                end
            end    
        })
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    Workspace = game:GetService("Workspace")

    _G.LoopKill = false
    _G.LoopKillConnection = nil
    _G.SavedCFrame = nil
    _G.KillOffset = Vector3.new(5, -18.5, 0)
    _G.HeightLimit = 10000
    _G.KillDelay = 0.5

    function _G.IsAboveLimit(Player)
        if Player and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
            return Player.Character.HumanoidRootPart.Position.Y > _G.HeightLimit
        end
        return true
    end

    function _G.DisableCollisions(Character)
        for _, Desc in pairs(Character:GetDescendants()) do
            if Desc:IsA("BasePart") then
                Desc.CanCollide = false
            end
        end
    end

    function _G.ForceDeath(Root, Humanoid)
        for _, Part in pairs(Humanoid.Parent:GetChildren()) do
            if Part:IsA("BasePart") then
                Part.CFrame = CFrame.new(-999999999999, 9999999999999, -999999999999)
            end
        end
        task.wait()
        for _, Part2 in pairs(Humanoid.Parent:GetChildren()) do
            if Part2:IsA("BasePart") then
                Part2.CFrame = CFrame.new(-999999999999, 9999999999999, -999999999999)
            end
        end
        BV = Instance.new("BodyVelocity")
        BV.Velocity = Vector3.new(0, 99999999999, 0)
        BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        BV.P = 100000075
        BV.Parent = Root
        Humanoid.Sit = false
        Humanoid.Jump = true
        Humanoid.BreakJointsOnDeath = false
        Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
        task.delay(2, function()
            if BV and BV.Parent then BV:Destroy() end
            BV = nil
        end)
    end

    function _G.ReturnToSavedPosition()
        Char = Players.LocalPlayer.Character
        if Char and Char:FindFirstChild("HumanoidRootPart") and _G.SavedCFrame then
            Char:PivotTo(_G.SavedCFrame)
        end
    end

    function _G.ExecuteKill(Player)
        if not _G.LoopKill or not Player or not Player.Character then return end
        if Workspace.PlotItems.PlayersInPlots:FindFirstChild(Player.Name) then return end

        Char = Player.Character
        Root = Char:FindFirstChild("HumanoidRootPart")
        Head = Char:FindFirstChild("Head")
        Humanoid = Char:FindFirstChild("Humanoid")
        if not (Root and Head and Humanoid) or Humanoid.Health <= 0 or _G.IsAboveLimit(Player) then return end

        SelfChar = Players.LocalPlayer.Character
        if not SelfChar or not SelfChar:FindFirstChild("HumanoidRootPart") then return end

        pcall(function()
            if not _G.LoopKill then return end
            SelfChar:PivotTo(CFrame.new(Root.Position + _G.KillOffset))
            _G.DisableCollisions(Char)
            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(Root, Root.CFrame)
            task.wait()
            _G.ReturnToSavedPosition()
            task.wait(0.1)
            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Root)
            task.wait(0.1)
            if Head:FindFirstChild("PartOwner") and Head.PartOwner.Value == Players.LocalPlayer.Name then
                _G.ForceDeath(Root, Humanoid)
            end
        end)
        task.wait(0.1)
    end

    function _G.LoopKillFunction()
        Char = Players.LocalPlayer.Character
        if not Char or not Char:FindFirstChild("HumanoidRootPart") then return end
        _G.SavedCFrame = Char:GetPivot()

        if not _G.LoopKill then return end
        Player = Players:FindFirstChild(selectedPlrName)
        if Player then  
            _G.ExecuteKill(Player)
        end

    end

    function _G.StartLoopKill()
        if _G.LoopKillConnection then _G.LoopKillConnection:Disconnect() end
        _G.LoopKillConnection = RunService.Heartbeat:Connect(function()
            if _G.LoopKill then
                _G.LoopKillFunction()
            else
                if _G.LoopKillConnection then
                    _G.LoopKillConnection:Disconnect()
                    _G.LoopKillConnection = nil
                end
            end
        end)
    end

    function _G.StopLoopKill()
        _G.LoopKill = false
        if _G.LoopKillConnection then
            _G.LoopKillConnection:Disconnect()
            _G.LoopKillConnection = nil
        end
        _G.SavedCFrame = nil
    end

    TarTab:AddToggle({
        Name = "LoopKill<font color=\"rgb(0, 255, 0)\"><b>[SNO]</b></font>",
        Default = false,
        Callback = function(Value)
            _G.LoopKill = Value
            if Value then 
                _G.StartLoopKill()
            else 
                _G.StopLoopKill()
            end
        end    
    })


    
    MiscTab:AddSection({ Name = "<b>Line-Lags</b>"})
    Toggles["LineAmount"] = MiscTab:AddSlider({
        Name = "Lag Amout",
        Min = 1,
        Max = 30000,
        Default = 50,
        Color = Color3.fromRGB(255,0,170),
        Increment = 100,
        ValueName = "bananas",
        Save = true,
        Flag = "LineAmount",
        Callback = function(Value)
            int.lagIntensity = Value
        end    
    })
    MiscTab:AddButton({
        Name = "Line Lag<font color=\"rgb(0, 255, 3)\"><b>[USING AMOUT]</b></font>",
        Callback = function()
            for i = 1, int.lagIntensity do  
                CreateGrabLine:FireServer(workspace.SpawnLocation,CFrame.new(workspace.SpawnLocation.Position.X, 1e9, workspace.SpawnLocation.Position.Z) * CFrame.Angles(math.rad(1e9),math.rad(1e9),math.rad(1e9)))
            end
        end    
    })

    MiscTab:AddButton({
        Name = "Line Lag<font color=\"rgb(255, 0, 0)\"><b>[DESCENDANTS]</b></font>",
        Callback = function()
            createLagWithGrabLine()
        end    
    })
    MiscTab:AddToggle({
        Name = "Just Loop-Lag",
        Default = false,
        Callback = function(Value)
            if Value then 
                cons["LAGS"] = task.spawn(function()
                    while RunService.RenderStepped:Wait() do
                        task.defer(function()
                            CreateGrabLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                            CreateGrabLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 8e9, 0))
                            CreateGrabLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 7e9, 0))
                            CreateGrabLine:FireServer(workspace.SpawnLocation, CFrame.new(0, 6e9, 0))
                        end)
                    end
                end)
            else 
                cons:cancel("LAGS")
            end
        end    
    })
    MiscTab:AddSection({ Name = "<b>Packets-Lags</b>"})
    local StringSize = string.len("metaballs metaballs metaballs metaballs metaballs metaballs metaballs metaballs")
    local function CalculateRepeats(Value)
        local TargetBytes = Value * 1024 * 1024
        local Repeats = math.floor(TargetBytes / 143)
        int.LagRepeats = math.max(1,Repeats)
    end
    int.RedeemPackets,int.SentPackets = 0,0
    Toggles["Redeemed"] = MiscTab:AddLabel("🛜🔽 Redeemed packets:"..tostring(int.RedeemPackets))
    Toggles["Sent"] = MiscTab:AddLabel("🛜🔼 Sent packets:"..tostring(int.SentPackets))
    Toggles["PacketsSize"] = MiscTab:AddSlider({
        Name = "Size of Packets to PACKET LAG",
        Min = 0.01,
        Max = 1.6,
        Default = 0.1,
        Color = Color3.fromRGB(255, 150,0),
        Increment = 0.01,
        ValueName = "MB",
        Save = true,
        Flag = "PacketsSize",
        Callback = CalculateRepeats  
    })
    MiscTab:AddToggle({
        Name = "Packet Lag Server",
        Default = false,
        Callback = function(Value)
            bool.LagPacket = Value
            while bool.LagPacket and task.wait(0.1) do
                RS.GrabEvents.ExtendGrabLine:FireServer(string.rep("metaballs metaballs metaballs metaballs metaballs metaballs metaballs metaballs",int.LagRepeats))
                int.SentPackets += 1
                Toggles["Sent"]:Set("🛜🔼 Sent packets:"..tostring(int.SentPackets))
            end
        end    
    })
    MiscTab:AddToggle({
        Name = "ShurikenLagServerT",
        Default = false,
        Callback = function(Value)
            bool.ShurikenLagServerT = Value 
            if Value then 
                lag()
            end
        end    
    })
    PlrTab:AddButton({
        Name = "FindBlistUsers",
        Callback = function()
        for i,v in ipairs(workspace:GetDescendants()) do 
            if v.Name == "NinjaKunai" and v.StickyPart.StickyWeld.Part1 ~= nil then 
                local name = v.Parent.Name:gsub("SpawnedInToys$", "")
                if name == game.Players.LocalPlayer.Name then continue end 
                OrionLib:MakeNotification({
                Name = "MB found",
                Content = "[Script]: "..name,
                Image = "user-star",
                Time = 5
            })
            end
        end
        end    
    })


    MiscTab:AddSection({ Name = "<b>РАЗНОС ДОМААА!!</b>"})
    MiscTab:AddDropdown({
        Name = "House to destroy",
        Default = "Plot1",
        Options = {"Plot1","Plot2","Plot3","Plot4","Plot5"},
        Callback = function(Value)
            int.Plot = Value
        end    
    })
    
    MiscTab:AddButton({
        Name = "Разнести все дома",
        Callback = function()
            local clone = SpawnToy("YouDecoy")
            if not clone then return end
            clone.Name = "blablabalbb1"
            local decoyHRP = FWD(clone, "HumanoidRootPart")
            
            repeat 
                sno(decoyHRP)
                task.wait(0.05)
            until clone:FindFirstChild("Head") and CheckForPartOwner(clone.Head)
            
            local BodyPos = Instance.new("BodyPosition")
            BodyPos.P = 45000
            BodyPos.D = 250
            BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            BodyPos.Parent = decoyHRP
            
            local BodyGyro = Instance.new("BodyGyro")
            BodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            BodyGyro.P = 10000
            BodyGyro.D = 500
            BodyGyro.Parent = decoyHRP
            
            Instance.new("Highlight").Parent = clone.Torso
            unsno(decoyHRP)
            
            local function flingObject(object)
                if not object or not object.PrimaryPart then return end
                
                local targetPos = object.PrimaryPart.Position
                
                BodyPos.Position = targetPos + Vector3.new(0, 5, 0) 
                
                while decoyHRP and decoyHRP.Parent and object and object.PrimaryPart do
                    local distance = (decoyHRP.Position - object.PrimaryPart.Position).Magnitude
                    if distance < 15 then
                        break
                    end
                    task.wait()
                end

                if decoyHRP and decoyHRP.Parent then
                    local direction = (decoyHRP.Position - object.PrimaryPart.Position).Unit
                    decoyHRP.AssemblyLinearVelocity = direction * 500

                    task.spawn(function()
                        for _ = 1,10 do 
                            task.wait()
                            decoyHRP.AssemblyAngularVelocity = Vector3.new(math.random(-100, 100) * 100, math.random(-100, 100) * 100, math.random(-100, 100) * 100)
                        end
                    end)

                    task.wait(0.2)
                end
            end
            
            local plot = Workspace.PlotItems[int.Plot]
            if plot then
                for _, v in pairs(plot:GetChildren()) do
                    if v.Name ~= "RemoveLagToys" and v.Name ~= "ToysLimitNum" and v.PrimaryPart then
                        flingObject(v)
                    end
                end
            end

            task.wait(2)
            if BodyPos then BodyPos:Destroy() end
            if BodyGyro then BodyGyro:Destroy() end
            DestroyToy:FireServer(clone)
        end    
    })
    local targetnyhrp = nil
    bool.JerkFlag = false
    local Jerk = nil
    Toggles["JerkOffKeybind"] = KeybindTab:AddBind({
        Name = "Jerk Off",
        Default = Enum.KeyCode.One,
        Hold = false,
        Save = true,
        Flag = "JerkOffKeybind",
        Callback = function()
            bool.JerkFlag = not bool.JerkFlag 
            if bool.JerkFlag then 
                local NewAnim = Instance.new("Animation")
                NewAnim.AnimationId = "rbxassetid://72042024"
                local Animator = hum:FindFirstChild("Animator")
                if not Animator then return end 
                Jerk = Animator:LoadAnimation(NewAnim)
                Jerk:Play()
                Jerk:AdjustSpeed(0.65)
                task.spawn(function()
                    while bool.JerkFlag and task.wait(0.05) do 
                        Jerk:Play()
                        Jerk.TimePosition = 0.6
                    end
                end)
            else     
                if Jerk then
                    Jerk:Stop()
                    Jerk = nil
                end
            end
        end    
    })
    
    local Gui = Instance.new("ScreenGui")
    Gui.Name = "TPGui"
    Gui.Parent = game.CoreGui
    Gui.Enabled = false

    Button = Instance.new("ImageButton")
    Button.Size = UDim2.new(0, 80, 0, 80)
    Button.Position = UDim2.new(1, -267, 1, -90)
    Button.Image = "rbxassetid://97166444"
    Button.BackgroundTransparency = 1
    Button.ImageTransparency = 0.5
    Button.Parent = Gui

    Label = Instance.new("ImageLabel")
    Label.Size = UDim2.new(0.8, 0, 0.8, 0)
    Label.Position = UDim2.new(0.1, 0, 0.1, 0)
    Label.Image = "rbxassetid://6723742952"
    Label.BackgroundTransparency = 1
    Label.Parent = Button

    Button.MouseButton1Click:Connect(function()
        if bool.TeleportKeybind then
            local MouseTarget = mouse and mouse.Target
            if MouseTarget then 
                StopVelocityF()
                hrp.CFrame = mouse.Hit * CFrame.new(0,5,0)
            end
        end
    end)

    Toggles["EnableTeleport"] =  KeybindTab:AddToggle({
        Name = "Enable Teleport Keybind",
        Default = false,
        Save = true,
        Flag = "EnableTeleport",
        Callback = function(Value)
            bool.TeleportKeybind = Value
            Gui.Enabled = game:GetService("UserInputService").TouchEnabled and Value or false
        end    
    })
    Toggles["TeleportKeybind"] = KeybindTab:AddBind({
        Name = "Teleport KeyBind",
        Default = Enum.KeyCode.X,
        Hold = false,
        Save = true,
        Flag = "TeleportKeybind",
        Callback = function()
            if bool.TeleportKeybind then
                local MouseTarget = mouse and mouse.Target
                if MouseTarget then 
                    StopVelocityF()
                    hrp.CFrame = mouse.Hit * CFrame.new(0,5,0)
                end
            end
        end    
    })
    Toggles["ShootItemVelocity"] = KeybindTab:AddSlider({
        Name = "Velocity",
        Min = 100,
        Max = 3000,
        Default = 100,
        Color = Color3.fromRGB(255,0,255),
        Increment = 100,
        ValueName = "VELOCITIES",
        Callback = function(Value)
            int.StrengthFlingBLALBLBA = Value
        end    
    })

    Toggles["ShootItemITEM"] = KeybindTab:AddDropdown({
        Name = "Item to Shoot",
        Default = "---",
        Options = {},
        Callback = function(Value)
            int.SelectedItem = Value
        end    
    })
    task.spawn(function()
        local list = {}
        for _,v in plr.PlayerGui.MenuGui.Menu.TabContents.Toys.Contents:GetChildren() do
            if v:IsA("Frame") then
                table.insert(list,v.Name)
            end
        end
        Toggles["ShootItemITEM"]:Refresh(list,true)
    end)


    Toggles["ShootItem"] = KeybindTab:AddBind({
        Name = "Shoot Item keybind",
        Default = Enum.KeyCode.Two,
        Hold = false,
        Save = true,
        Flag = "ShootItemKeybind",
        Callback = function()
            local item = SpawnToy(int.SelectedItem)
            if not item then return end 
            repeat task.wait() until item and item.PrimaryPart
            local PartToGrab
            local part = item.PrimaryPart
            for _,v in item:GetChildren() do
                if HasProperty(v, "CanQuery") and v.CanQuery and v.CanTouch then PartToGrab = v break end
            end
            if item:FindFirstChild("HumanoidCreature", true) then item:FindFirstChild("HumanoidCreature", true).Sit = true item:FindFirstChild("HumanoidCreature", true).PlatformStand = true Grab(item.LeftDetector) end
            sno(PartToGrab)
            task.wait(0.2)
            local Camera = workspace.CurrentCamera
            part.CFrame = Camera.CFrame * CFrame.new(0, 0, -8)
            part.AssemblyLinearVelocity = Camera.CFrame.LookVector * int.StrengthFlingBLALBLBA or 100
        end    
    })
    Toggles["Pose"] = KeybindTab:AddDropdown({
        Name = "LoopGrab Pose",
        Default = "[HRP]",
        Options = {"[HRP]","[DOG]"},
        Save = true,
        Flag = "Pose",
        Callback = function(Value)
            int.LoopGrabType = Value
        end    
    })
    Toggles["SpamKeybind"] =  KeybindTab:AddBind({
        Name = "Spam",
        Default = Enum.KeyCode.T,
        Hold = false,
        Save = true,
        Flag = "SpamKeybind",
        Callback = function()
        bool.LoopGrabKey = not bool.LoopGrabKey
            if bool.LoopGrabKey then 
                local PartToMaybeSpam = mouse.Target     
                int.SpamChar = PartToMaybeSpam.Parent
                local Head,Torso = int.SpamChar and int.SpamChar:FindFirstChild("Head"), int.SpamChar and int.SpamChar:FindFirstChild("Torso") 
                if Torso and Head and int.SpamChar:FindFirstChild("Humanoid") then  
                    if int.LoopGrabType == "[DOG]" then
                        cons["LoopGrabCon13"] = RunService.Heartbeat:Connect(function()
                            Torso = int.SpamChar and int.SpamChar:FindFirstChild("Torso")
                            Head = int.SpamChar and int.SpamChar:FindFirstChild("Head")
                            if not Torso or not Head then cons:disc("LoopGrabCon13") end
                            sno(Head)
                            for _,x in pairs(int.SpamChar:GetDescendants()) do 
                                if x:IsA("BasePart") then 
                                    x.CanCollide = false
                                end
                            end
                            int.SpamChar["Humanoid"].Health = 100
                            Torso.CFrame = hrp.CFrame * CFrame.new(-0,-1,-2)* CFrame.Angles(math.rad(-90), 0, math.rad(180))
                            Head.CFrame = Torso.CFrame * CFrame.new(0, 1, 0) * CFrame.Angles(math.rad(90), 0, 0)
                            if int.SpamChar:FindFirstChild("Left Arm") then 
                                int.SpamChar["Left Arm"].CFrame = Torso.CFrame * CFrame.new(-1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(-30))
                            end
                            if int.SpamChar:FindFirstChild("Right Arm") then    
                                int.SpamChar["Right Arm"].CFrame = Torso.CFrame * CFrame.new(1, 0.5, 0) * CFrame.Angles(math.rad(60), 0, math.rad(30))
                            end
                            if int.SpamChar:FindFirstChild("Left Leg") then 
                                int.SpamChar["Left Leg"].CFrame = Torso.CFrame * CFrame.new(-0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0)
                            end
                            if int.SpamChar:FindFirstChild("Right Leg") then 
                                int.SpamChar["Right Leg"].CFrame = Torso.CFrame * CFrame.new(0.5, -1, 0) * CFrame.Angles(math.rad(40), 0, 0)
                            end
                        end)
                    elseif int.LoopGrabType == "[HRP]" then 
                        cons["LoopGrabCon13"] = RunService.Heartbeat:Connect(function()
                            Torso = int.SpamChar and int.SpamChar:FindFirstChild("Torso")
                            Head = int.SpamChar and int.SpamChar:FindFirstChild("Head")
                            if not Torso or not Head then cons:disc("LoopGrabCon13") end
                            sno(Head)
                            if CheckForPartOwner(Head) then 
                                for _,v in pairs(int.SpamChar:GetDescendants()) do 
                                    if v:IsA("BasePart") then 
                                        v.CanCollide = false 
                                        v.CFrame = hrp.CFrame * CFrame.new(0,0,-5)
                                    end
                                end
                            end
                        end)
                    end
                end
            else 
                cons:disc("LoopGrabCon13")
                if int.SpamChar and int.SpamChar.Parent and int.SpamChar:FindFirstChild("Head") then 
                    for _,v in pairs(int.SpamChar:GetChildren()) do 
                        if v:IsA("BasePart") then 
                            v.AssemblyLinearVelocity = Vector3.zero
                            v.AssemblyAngularVelocity = Vector3.zero
                            v.CanCollide = true
                        end
                    end
                end
            end
        end    
    })
    int.OutlineAnchorColor = Color3.fromRGB(0,0,255)
    int.AnchorTransp = 0
    local function CreateBodyPos1(PrimaryPart)  
        local bodyPosition = Instance.new("BodyPosition")
        local bodyGyro = Instance.new("BodyGyro")
        bodyGyro.Name = "AnchorGyro"
        bodyPosition.Name = "AnchorPos"
        bodyPosition.P = 200000
        bodyPosition.D = 1000
        bodyPosition.MaxForce = Vector3.new(Huge())
        bodyPosition.Position = PrimaryPart.Position
        bodyGyro.P = 200000
        bodyGyro.D = 1000
        bodyGyro.MaxTorque = Vector3.new(Huge())
        bodyGyro.CFrame = PrimaryPart.CFrame

        bodyGyro.Parent = PrimaryPart
        bodyPosition.Parent = PrimaryPart
        
        local Parent = PrimaryPart.Parent
        if Parent then 
            local SelectionBox = Instance.new("SelectionBox")
            SelectionBox.Parent = PrimaryPart
            SelectionBox.Adornee = Parent
            SelectionBox.LineThickness = 0.05
            SelectionBox.Color3 = int.OutlineAnchorColor
            SelectionBox.SurfaceTransparency = 1
            SelectionBox.Transparency = int.AnchorTransp
            SelectionBox.Name = "AnchorSelection"
        end
    end

    Toggles["AnchorGrabKeybind"] = KeybindTab:AddBind({
        Name = "Anchor Grab",
        Default = Enum.KeyCode.V,
        Hold = false,
        Save = true,
        Flag = "AnchorGrabKeybind",
        Callback = function()
            local GrabParts = game.workspace:FindFirstChild("GrabParts")
            if not GrabParts then return end 
            
            local GrabPart = GrabParts:WaitForChild("GrabPart", 1):WaitForChild("WeldConstraint", 1).Part1
            if not GrabPart then return end 
            
            local plrtar = GrabPart.Parent 
            local TargetPart = plrtar.PrimaryPart
            if not TargetPart then return end

            if TargetPart:FindFirstChild("AnchorPos") then 
                TargetPart:FindFirstChild("AnchorPos"):Destroy()
                TargetPart:FindFirstChild("AnchorGyro"):Destroy()
                TargetPart:FindFirstChild("AnchorSelection"):Destroy()
            else 
                CreateBodyPos1(TargetPart)
            end
        end    
    })

    Toggles["SitOnBlobKeybind"] = KeybindTab:AddBind({
        Name = "Sit on Your blob",
        Default = Enum.KeyCode.Z,
        Hold = false,
        Save = true,
        Flag = "SitOnBlobKeybind",
        Callback = function()
            local blob = nil  
            for _,v in pairs(inv:GetChildren()) do 
                if v.Name == "CreatureBlobman" then 
                    if v:FindFirstChild("HumanoidRootPart") and v["HumanoidRootPart"].Position.Y <= 1000 then  
                        blob = v  
                        break  
                    else 
                        v.Name = "IDIOTBLOB"
                    end 
                end
            end
            if blob ~= nil then  
                blob.VehicleSeat:Sit(hum)
            else 
                blob = SpawnToy("CreatureBlobman")
                repeat task.wait() until (not blob) or blob:FindFirstChild("Weight")
                blob:WaitForChild("VehicleSeat",1):Sit(hum)
                local end1 = tick() + 1
                while tick() < end1 and task.wait() do 
                    FWD(blob,"HumanoidCreature"):ChangeState(Enum.HumanoidStateType.Running)
                end
            end 
        end    
    })


    Toggles["DeleteLegsKeybind"] = KeybindTab:AddBind({
        Name = "Delete Legs",
        Default = Enum.KeyCode.Y,
        Hold = false,
        Save = true,
        Flag = "DeleteLegsKeybind",
        Callback = function()
            local GrabParts = game.workspace:FindFirstChild("GrabParts")
            if not GrabParts then return end 
            local GrabPart = GrabParts:WaitForChild("GrabPart",1):WaitForChild("WeldConstraint",1).Part1
            if not GrabPart then return end 
            local plrtar = GrabPart.Parent 
            local rl,ll,torso = plrtar:FindFirstChild("Right Leg"),plrtar:FindFirstChild("Left Leg"),plrtar:FindFirstChild("Torso") 
            if not rl or not ll or not torso then  
                return 
            end
            local oldFAL = workspace.FallenPartsDestroyHeight
            Workspace.FallenPartsDestroyHeight = -50000
            rl.CFrame = CFrame.new(0,-60000,0)
            ll.CFrame = CFrame.new(0,-60000,0)
            task.wait(0.1)
            torso.CFrame = CFrame.new(0,-55970,0)
            task.wait(0.1)
            workspace.FallenPartsDestroyHeight = oldFAL
        end    
    })


    Toggles["DeleteArmsKeybind"] = KeybindTab:AddBind({
        Name = "Delete Arms",
        Default = Enum.KeyCode.U,
        Hold = false,
        Save = true,
        Flag = "DeleteArmsKeybind",
        Callback = function()
            local GrabParts = game.workspace:FindFirstChild("GrabParts")
            if not GrabParts then return end 
            local GrabPart = GrabParts:WaitForChild("GrabPart",1):WaitForChild("WeldConstraint",1).Part1
            if not GrabPart then return end 
            local plrtar = GrabPart.Parent 
            local ra,la,torso = plrtar:FindFirstChild("Right Arm"),plrtar:FindFirstChild("Left Arm"),plrtar:FindFirstChild("Torso")
            if not ra or not la or not torso then 
                return
            end
            local oldFAL = workspace.FallenPartsDestroyHeight
            workspace.FallenPartsDestroyHeight = -50000
            ra.CFrame = CFrame.new(0,-60000,0)
            la.CFrame = CFrame.new(0,-60000,0)
            task.wait(0.1)
            torso.CFrame = CFrame.new(0,-55970,0)
            task.wait(0.1)
            workspace.FallenPartsDestroyHeight = oldFAL
        end    
    })


    OwnTab:AddButton({
        Name = "InputLagPLR",
        Callback = function()
            local v = FindBlob()
            hrp.CFrame = game.Players[selectedPlrName].Character.HumanoidRootPart.CFrame
            BringRight(selectedPlrName)
            task.wait()
            BugRight(selectedPlrName)
        end    
    })

    OwnTab:AddButton({
        Name = "Bring",
        Callback = function()
            local v = FindBlob()
            local oldCF = hrp.CFrame 
            repeat 
                hrp.CFrame = game.Players[selectedPlrName].Character.HumanoidRootPart.CFrame * CFrame.new(0,10,0)
                BringRight(selectedPlrName)
                task.wait(0.05)
            until not hrp or not v or not hum.SeatPart or v.RightDetector.RightWeld.Attachment0
            BugRight(selectedPlrName)
            task.wait(0.05)
            hrp.CFrame = oldCF
            task.wait(0.1)  
            task.spawn(function()
                for i = 1,6 do 
                    game.Players[selectedPlrName].Character.HumanoidRootPart.CFrame = oldCF * CFrame.new(0,20,0)
                    task.defer(StopAllVelocity,game.Players[selectedPlrName].Character)
                    task.wait(0.1)
                end
            end)
            task.wait(0.4)
            repeat 
                sno(game.Players[selectedPlrName].Character.HumanoidRootPart)
                task.wait(0.08)
            until game.Players[selectedPlrName].Character.Head:FindFirstChild("PartOwner") or not hum.SeatPart
            game.ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(game.Players[selectedPlrName].Character.HumanoidRootPart)
        end    
    })
    OwnTab:AddButton({  
        Name = "Jump",
        Callback = function()
            if selectedPlrName == nil then return end
            game.Players[selectedPlrName].Character.Humanoid.Jump = true
        end    
    })
    OwnTab:AddButton({  
        Name = "Sit",
        Callback = function()
        if selectedPlrName == nil then return end
            game.Players[selectedPlrName].Character.Humanoid.Sit = not game.Players[selectedPlrName].Character.Humanoid.Sit
        end      
    })

    OwnTab:AddToggle({
        Name = "Loop Walk to me",
        Default = false,
        Callback = function(Value)
            bool.LoopWalk = Value
            if Value then 
            local v = FindBlob()
            
            hrp.CFrame = game.Players[selectedPlrName].Character.HumanoidRootPart.CFrame
            BringRight(selectedPlrName)
            task.wait()
            BugRight(selectedPlrName)
            local Mouse = plr:GetMouse()
            repeat
                task.wait()
                game.Players[selectedPlrName].Character.Humanoid:MoveTo(hrp.Position)
            until not game.Players[selectedPlrName].Character.Humanoid or not bool.LoopWalk
            end
        end    
    })
    OwnTab:AddToggle({
        Name = "Loop Walk to mouse",
        Default = false,
        Callback = function(Value)
            bool.LoopWalk1 = Value
            if Value then 
            local v = FindBlob()
            
            hrp.CFrame = game.Players[selectedPlrName].Character.HumanoidRootPart.CFrame
            BringRight(selectedPlrName)
            task.wait()
            BugRight(selectedPlrName)
            local Mouse = plr:GetMouse()
            repeat
                task.wait()
                game.Players[selectedPlrName].Character.Humanoid:MoveTo(Mouse.Hit.p)
            until not game.Players[selectedPlrName].Character.Humanoid or not bool.LoopWalk1
            end
        end    
    })
    
    OwnTab:AddSlider({
        Name = "WalkSpeed",
        Min = 1,
        Max = 200,
        Default = 16,
        Color = Color3.fromRGB(0, 0, 255),
        Increment = 10,
        ValueName = "bananas",
        Callback = function(Value)
            if selectedPlrName == nil then return end
            game.Players[selectedPlrName].Character.Humanoid.WalkSpeed = Value 
        end    
    })

    OwnTab:AddSlider({
        Name = "JumpPower",
        Min = 1,
        Max = 200,
        Default = 10,
        Color = Color3.fromRGB(255, 102, 204),
        Increment = 10,
        ValueName = "bananas",
        Callback = function(Value)
            if selectedPlrName == nil then return end
            game.Players[selectedPlrName].Character.Humanoid.JumpPower = Value 
        end    
    })
    local positionsTable = {
        [1] = CFrame.new(4.12, -12.45, 15.30),
        [2] = CFrame.new(-10.54, 8.21, -3.44),
        [3] = CFrame.new(18.22, 0.15, -10.87),
        [4] = CFrame.new(-5.30, -18.90, 2.11),
        [5] = CFrame.new(0.45, 14.56, 12.00),
        [6] = CFrame.new(12.78, -9.33, -14.20),
        [7] = CFrame.new(-15.60, -2.10, 10.45),
        [8] = CFrame.new(2.11, 20.34, -5.67),
        [9] = CFrame.new(-12.00, 10.00, 15.12),
        [10] = CFrame.new(15.40, 12.30, 0.45),
        [11] = CFrame.new(-8.20, -10.45, -18.30),
        [12] = CFrame.new(5.67, -2.50, 22.10),
        [13] = CFrame.new(-20.10, 5.40, -8.90),
        [14] = CFrame.new(10.12, 18.22, 5.30),
        [15] = CFrame.new(-3.45, -15.12, -12.00),
        [16] = CFrame.new(22.30, -5.67, 4.12),
        [17] = CFrame.new(-14.20, 16.10, -2.11),
        [18] = CFrame.new(7.89, -20.45, 0.15),
        [19] = CFrame.new(0.15, 6.78, -20.10),
        [20] = CFrame.new(-18.30, -8.20, 5.67)
    }
    OwnTab:AddButton({  
        Name = "Destroy Server",
        Callback = function()
            local blob = FindBlob()
            if not blob then return end 
            
            local RightDetector = blob.RightDetector
            local LeftDetector = blob.LeftDetector
            local RightWeld = RightDetector and RightDetector.RightWeld
            local LeftWeld = LeftDetector and LeftDetector.LeftWeld
            
            local scripts = blob.BlobmanSeatAndOwnerScript
            local CreatureGrab = scripts.CreatureGrab
            local CreatureRelease = scripts.CreatureRelease 
            
            local oldCF = char:GetPivot()
            local grabbedPlayers = {}
            local validTargetIndex = 0

            for _, v in pairs(Players:GetPlayers()) do
                if v ~= plr and not IsFriend(v) then  
                    local hrp1 = v.Character and v.Character:FindFirstChild("HumanoidRootPart")
                    if hrp1 then  
                        validTargetIndex = validTargetIndex + 1
                        local posIndex = ((validTargetIndex - 1) % #positionsTable) + 1
                        for _, x in pairs(hrp1:GetChildren()) do 
                            if x:IsA("BodyPosition") then x:Destroy() end
                        end
                        local EndTick = tick() + 1
                        while (hrp1 and hrp1.Parent) and not (RightWeld.Attachment0) and (tick() < EndTick) do 
                            if not hum.SeatPart then break end 
                            char:PivotTo(hrp1.CFrame)
                            CreatureGrab:FireServer(RightDetector, hrp1, RightWeld)
                            task.wait(0.01)
                        end 
                        if RightWeld.Attachment0 then 
                            CreatureRelease:FireServer(RightWeld, hrp1)
                            local BodyPos = Instance.new("BodyPosition")
                            BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            BodyPos.Position = oldCF.Position + positionsTable[posIndex].Position
                            BodyPos.P = 45000
                            BodyPos.D = 500
                            BodyPos.Parent = hrp1
                            table.insert(grabbedPlayers, hrp1)
                            hrp.CFrame = CFrame.new(0,1000,0)
                            while RightWeld.Attachment0 do task.wait() end 
                        end
                    end
                end
            end 

            char:PivotTo(oldCF)
            task.wait(0.2)

            for i, Root in ipairs(grabbedPlayers) do 
                sno(Root)
                unsno(Root)
                
                if i % 2 == 0 then 
                    CreatureGrab:FireServer(RightDetector, Root, RightWeld)
                else 
                    CreatureGrab:FireServer(LeftDetector, Root, LeftWeld)
                end
                task.wait(0.03) 
            end
        end    
    })
    ConfigTab:AddSection({
        Name = "ESP COLORS(Restart the PCLD_ESP function)"
    })

    Toggles["PCLD_ESPCOLOR"] = ConfigTab:AddColorpicker({
        Name = "PCLD_ESP COLOR",
        Default = Color3.fromRGB(0, 255, 255),
        Save = true,
        Flag = "PCLD_ESPCOLOR",
        Callback = function(Value)
            int.PCLDColor = Value
        end	  
    })
    ConfigTab:AddSection({
        Name = "AntiKick(respawn the antikick)"
    })
    Toggles["MainAntiKickColor"] = ConfigTab:AddColorpicker({
        Name = "AntiKick MainPart Color",
        Default = Color3.fromRGB(255, 0, 255),
        Save = true,
        Flag = "MainAntiKickColor",
        Callback = function(Value)
            int.MainAntiKickColor = Value
        end	  
    })
    Toggles["PyramidAntiKickColor"] = ConfigTab:AddColorpicker({
        Name = "AntiKick Pyramids Color",
        Default = Color3.fromRGB(255, 210, 130),
        Save = true,
        Flag = "PyramidAntiKickColor",
        Callback = function(Value)
            int.PyramidAntiKickColor = Value
        end	  
    })
    ConfigTab:AddSection({
        Name = "Anchor(Re-Anchor to see)"
    })
    Toggles["AnchorColor"] = ConfigTab:AddColorpicker({
        Name = "Anchor outline color",
        Default = Color3.fromRGB(0, 0, 255),
        Save = true,
        Flag = "AnchorColor",
        Callback = function(Value)
            int.OutlineAnchorColor = Value
        end	  
    })
    Toggles["AnchorTransparency"] = ConfigTab:AddSlider({
        Name = "AnchorTransparency",
        Min = 0,
        Max = 100,
        Default = 0,
        Color = Color3.fromRGB(255, 0, 255),
        Increment = 1,
        ValueName = "",
        Save = true,
        Flag = "AnchorTransparency",
        Callback = function(Value)
            int.AnchorTransp = Value * 0.01
        end    
    })

    
    ConfigTab:AddSection({
        Name = "GUI settings"
    })
        local GUI = FWD(game.CoreGui,"NNhubOrion") 
        local MainFrame 
        for _,v in pairs(GUI:GetChildren()) do 
            if v.Name == "Frame" and v.BackgroundTransparency ~= 1 then 
                MainFrame = v
            end
        end
    Toggles["GUIWIDE"] = ConfigTab:AddSlider({
        Name = "GUI SIZE X",
        Min = 400,
        Max = 5000,
        Default = 650,
        Color = Color3.fromRGB(0,0,255),
        Increment = 1,
        ValueName = "pix",
        Save = true,
        Flag = "GUISIZEX",
        Callback = function(Value)
            int.GUIWIDE = Value
        end    
    })
    Toggles["GUIHEIGHT"] = ConfigTab:AddSlider({
        Name = "GUI SIZE Y",
        Min = 200,
        Max = 5000,
        Default = 400,
        Color = Color3.fromRGB(255, 0, 0),
        Increment = 1,
        ValueName = "pix",
        Save = true,
        Flag = "GUISIZEY",
        Callback = function(Value)
            int.GUIHEIGHT = Value
        end    
    })
    ConfigTab:AddButton({
        Name = "Apply Size",
        Callback = function()
            MainFrame.Size = UDim2.new(0,int.GUIWIDE,0,int.GUIHEIGHT)
        end    
    })


    Toggles["GUITransparency"] = ConfigTab:AddSlider({
        Name = "GUI Transparency",
        Min = 0,
        Max = 100,
        Default = 30,
        Color = Color3.fromRGB(255, 0, 255),
        Increment = 1,
        ValueName = "",
        Save = true,
        Flag = "GUITransparency",
        Callback = function(Value)
            MainFrame.BackgroundTransparency = Value * 0.01
        end    
    })
        Toggles["GUICOLOR"] = ConfigTab:AddColorpicker({
            Name = "GUI Background Color",
            Default = Color3.fromRGB(25, 25, 25),
            Save = true,
            Flag = "GUICOLOR",
            Callback = function(Value)
                MainFrame.BackgroundColor3 = Value
            end	  
        })
    

    ConfigTab:AddSection({
        Name = "Config saver/loader"
    })

    ConfigTab:AddTextbox({
        Name = "FileName(Without .txt)",
        Default = "",
        TextDisappear = false,
        Callback = function(Value)
            etc.FileName = Value
        end	  
    })
    ConfigTab:AddButton({
        Name = "Save Config",
        Callback = function()
            if etc.FileName ~= nil then 
                OrionLib:SaveCfg1(etc.FileName)
            else 
                Notify("Error!","Expected smth in filename!")
            end
        end    
    })

    local function GetFiles() 
        local files = listfiles('NNhub')
        local metaball = {}
        for _,v in pairs(files) do 
            if v:find('.txt') then 
                table.insert(metaball,v)
            end
        end
        return metaball
    end

    local ConfigDropDown = ConfigTab:AddDropdown({
        Name = "List files",
        Default = "",
        Options = GetFiles(),
        Callback = function(Value)
            etc.FileToLoad = Value
    end})

    ConfigTab:AddButton({
        Name = "Load Config",
        Callback = function()
            if etc.FileToLoad ~= nil then 
                OrionLib:LoadCfg1(etc.FileToLoad)
            else 
                Notify("Error!","Expected file got nil????")
            end
        end    
    })

    local oldFiles = {}

    coroutine.wrap(function()
        while task.wait(1) do
            local newFiles = GetFiles()
            
            if #newFiles ~= #oldFiles then
                ConfigDropDown:Refresh(newFiles, true)
                oldFiles = newFiles
            end
        end
    end)()

    task.delay(2,function()
        local StartTick = tick()
        local Character,Root
        while task.wait(0.025) do
            if tick() - StartTick >= 0.1 then
                -- бла бла бла антилаг
                if CountOfLines >= 40 then
                    local LagPlr = game.Players:GetPlayerFromCharacter(LastLagSource)
                    if LagPlr and OrionLib and bool.AutoAntiLag then 
                        OrionLib:MakeNotification({
                            Name = "<font color=\"rgb(255, 0, 0)\"><b>AUTO-ANTILAG</b></font> NOTIFY",
                            Content = "<font color=\"rgb(100, 0, 100)\"><b>[💢LAG] </b></font>: "..LagPlr.Name.."(@"..LagPlr.DisplayName..")",
                            Image = "angry",
                            Time = 5
                        })
                        Toggles["AntiLag"]:Set(true)
                    end
                end
                StartTick = tick()
                CountOfLines = 0
                -- AntiMassless Everyone!!1!1
                for _,v in pairs(Players:GetPlayers()) do
                    if v ~= plr then
                        local char = v.Character
                        if char then
                            local hrp = char:FindFirstChild("HumanoidRootPart")
                            if hrp and hrp.Massless then
                                local hum = hrp and char:FindFirstChild("Humanoid")
                                if (hum) and not(hum.Sit) then 
                                    hrp.Massless = false
                                end
                            end
                        end
                    end
                end
                -- бла бла канец 
            end
        end
    end)

    int.LastKunaiSource = nil
    _G.TryAntiFling = false
    workspace.DescendantAdded:Connect(function(v)
        if v.Name == "NinjaKunai" or v.Name == "NinjaShuriken" then 
        local name = v.Parent.Name:gsub("SpawnedInToys$", "")
        local plr = game.Players:FindFirstChild(name)
            if plr and plr ~= game.Players.LocalPlayer and plr ~= int.LastKunaiSource then  
                int.LastKunaiSource = plr
                OrionLib:MakeNotification({
                    Name = "ANTIKICK NOTIFY",
                    Content = "[Script]: "..plr.Name.." ("..plr.DisplayName..")",
                    Image = "user-star",
                    Time = 5
                })
                task.delay(5, function()
                    int.LastKunaiSource = nil
                end)
            end
        elseif v.Name == "GrabBeam" then  
            CountOfLines += 1  
            LastLagSource = v.Parent.Parent.Parent
        elseif v.Name == "PaintPlayerPart" and bool.AntiPaint then  
            Debris:AddItem(v,0)
        elseif ((v.Name == "PalletLightBrown" or v.Name == "YouDecoy") and v.Parent ~= inv) and _G.TryAntiFling then  
            for _,v in pairs(v:GetDescendants()) do 
                v.CanCollide = false 
            end
        elseif (v.Name == "CreatureBlobman" and v.Parent ~= inv) and bool.AntiBlob then 
            task.spawn(function()
                if InOwnedPlot.Value then  
                    local Plot = CheckForHome()
                    if Plot and v.Parent == Plot then return end
                end
                local LeftDetector,RightDetector = FWD(v,"LeftDetector",5),FWD(v,"RightDetector",5)
                if LeftDetector then 
                    FWD(LeftDetector,"LeftWeld").Enabled = false 
                    FWD(LeftDetector,"LeftAlignOrientation").Enabled = false  
                    FWD(LeftDetector,"LeftAlignOrientation").RigidityEnabled = false
                end 
                if RightDetector then  
                    FWD(RightDetector,"RightWeld").Enabled = false 
                    FWD(RightDetector,"RightAlignOrientation").Enabled = false 
                    FWD(RightDetector,"RightAlignOrientation").RigidityEnabled = false
                end
            end)
        end
    end)

    for i, v in pairs(workspace.Map.TrainTunnel:GetDescendants()) do
        if v.ClassName == "Part" and v.CFrame == CFrame.new(556.441345, 134.338745, 37.904129, 0.980784655, 0, 0.195093334, 0, 1, 0, -0.195093334, 0, 0.980784655) then
            v:Destroy()
        end
    end

    OnCharAdded(plr.Character)
    local WasLagged = false
    local function GetSizeMB(StringLength)
        return StringLength / (1024 * 1024)
    end
    RS.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(arg1,data)
        if typeof(data) == "string" and not WasLagged then 
            WasLagged = true
            local StringLen = string.len(data)
            if StringLen > 300 then 
                int.RedeemPackets += 1
                Toggles["Redeemed"]:Set("🛜🔽 Redeemed packets:"..tostring(int.RedeemPackets))
                local SizeRounded = math.round(GetSizeMB(StringLen) * 1000) / 1000 
                OrionLib:MakeNotification({
                    Name = "<font color=\"rgb(255, 0, 0)\"><b>PACKET LAGS DETECTED</b></font>",
                    Content = "Source: "..tostring(arg1).."\nSize: "..tostring(SizeRounded).." MB",
                    Image = "rat",
                    Time = 5
                })
                task.delay(5, function()
                    WasLagged = false
                end)    
            end
        end
    end)
    game.Players.PlayerAdded:Connect(function(plr1)
        options, playerMap = BuildPlayerOptions()
        Dropdown1:Refresh(options,true)
        if plr1.Name == selectedPlrName then 
            OrionLib:MakeNotification({
                Name = "<font color=\"rgb(255, 6, 0)\"><b>TARGET</b></font>".." Joined",
                Content = "<font color=\"rgb(0, 255, 0)\"><b>[🚪JOIN] </b></font>"..plr1.Name.." ("..plr1.DisplayName..")",
                Image = "user-plus",
                Time = 5
            })
        elseif IsFriend(plr1) then   
            OrionLib:MakeNotification({
                Name = "<font color=\"rgb(0, 255, 0)\"><b>FRIEND</b></font>".." Joined",
                Content = "<font color=\"rgb(0, 255, 0)\"><b>[🚪JOIN] </b></font>"..plr1.Name.." ("..plr1.DisplayName..")",
                Image = "shield-user",
                Time = 5
            })
        end
    end)

    game.Players.PlayerRemoving:Connect(function(plr1)
        options, playerMap = BuildPlayerOptions()
        Dropdown1:Refresh(options,true)
        if CFP(workspace,"BlackHoleKick") then 
            workspace["BlackHoleKick"].Name = "UsedblackHole"
            OrionLib:MakeNotification({
                Name = "PLR ".."<font color=\"rgb(255, 93, 0)\"><b>KICK</b></font>",
                Content = "<font color=\"rgb(255, 93, 0)\"><b>[🦵KICK] </b></font>"..plr1.Name.." ("..plr1.DisplayName..")",
                Image = "user-lock",
                Time = 5
            })
        else 
            OrionLib:MakeNotification({
            Name = "PLR ".."<font color=\"rgb(255, 217, 0)\"><b>LEFT</b></font>",
            Content = "<font color=\"rgb(255, 217, 0)\"><b>[🤝LEFT] </b></font>"..plr1.Name.." ("..plr1.DisplayName..")",
            Image = "user-minus",
            Time = 5
        })
        end
    end)
    plr.CharacterAdded:Connect(OnCharAdded)
    OrionLib:Init()
    OrionLib:MakeNotification({
        Name = "<b>NNhub</b><font color=\"rgb(0,255,0)\"> loaded succesfully</font>",
        Content = "loaded in "..tostring(math.round(tick() - StartTick1)).." Seconds\nPress RightShift to Hide/Unhide menu",
        Image = "file-terminal",
        Time = 10
    })
