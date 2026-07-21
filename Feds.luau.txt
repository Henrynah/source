local Rayfield = loadstring(game:HttpGet('https://raw.githubusercontent.com/fedoradevs/Fedfield/refs/heads/main/Fedfield.lua'))()

local Window = Rayfield:CreateWindow({
   Name = "Feds FTAP (BROKEN)",
   Icon = 0, 
   LoadingTitle = "Valid Executor! Welcome.",
   LoadingSubtitle = "by federal",

   Theme = { 
       TextColor = Color3.fromRGB(150, 225, 150), -- Darker green for text

       Background = Color3.fromRGB(15, 15, 15), 
       Topbar = Color3.fromRGB(25, 25, 25), 
       Shadow = Color3.fromRGB(10, 10, 10), 

       NotificationBackground = Color3.fromRGB(20, 20, 20), 
       NotificationActionsBackground = Color3.fromRGB(34, 34, 34), 

       TabBackground = Color3.fromRGB(45, 45, 45), 
       TabStroke = Color3.fromRGB(50, 50, 50), 
       TabBackgroundSelected = Color3.fromRGB(30, 30, 30), 
       TabTextColor = Color3.fromRGB(150, 225, 150), -- Dark green text for tabs
       SelectedTabTextColor = Color3.fromRGB(40, 200, 40), -- Slightly darker selected tab green

       ElementBackground = Color3.fromRGB(25, 25, 25), 
       ElementBackgroundHover = Color3.fromRGB(35, 35, 35), 
       SecondaryElementBackground = Color3.fromRGB(20, 20, 20), 
       ElementStroke = Color3.fromRGB(30, 100, 30), -- Darker green stroke
       SecondaryElementStroke = Color3.fromRGB(25, 80, 25), -- Darker green

       SliderBackground = Color3.fromRGB(30, 70, 30), -- Darker green slider
       SliderProgress = Color3.fromRGB(40, 200, 40), -- Darker green progress bar
       SliderStroke = Color3.fromRGB(30, 150, 30), -- Adjusted green stroke

       ToggleBackground = Color3.fromRGB(25, 25, 25), 
       ToggleEnabled = Color3.fromRGB(40, 200, 40), -- Darker green enabled state
       ToggleDisabled = Color3.fromRGB(70, 70, 70), -- Slightly darker gray
       ToggleEnabledStroke = Color3.fromRGB(30, 150, 30), -- Dark green stroke
       ToggleDisabledStroke = Color3.fromRGB(50, 50, 50), 
       ToggleEnabledOuterStroke = Color3.fromRGB(25, 90, 25), 
       ToggleDisabledOuterStroke = Color3.fromRGB(35, 35, 35), 

       DropdownSelected = Color3.fromRGB(35, 35, 35), 
       DropdownUnselected = Color3.fromRGB(25, 25, 25), 

       InputBackground = Color3.fromRGB(20, 20, 20), 
       InputStroke = Color3.fromRGB(30, 90, 30), -- Darker green stroke for input fields
       PlaceholderColor = Color3.fromRGB(110, 200, 110) -- Darker green for placeholder text
   }, 

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, 

   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil, 
      FileName = "Feds Hub"
   },

   Discord = {
      Enabled = false, 
      Invite = "", 
      RememberJoins = true 
   },

   KeySystem = false, 
   KeySettings = {
      Title = "Fed'sHub | FlingThingsAndPeople:",
      Subtitle = "Key System",
      Note = "Creator/Admin only, please get a key from federal.", 
      FileName = "Key", 
      SaveKey = true, 
      GrabKeyFromSite = true, 
      Key = {"feds-77%$!@&$TYAF7474asjdbUSdj982340SF00"} 
   }
 
})
Rayfield:Notify({
    Title = "Enjoying FedsHub?",
    Content = "",
    Duration = 6.5,
    Image = 4483362458,
 })

local Tab = Window:CreateTab("Preventions")

local Section = Tab:CreateSection("Main")

local Toggle = Tab:CreateToggle({
    Name = "Anti-grab",
    CurrentValue = false,
    Flag = "AntiGrabToggle",
    Callback = function(Value)
        if Value then
            _G.AntiGrab = true
            task.spawn(function()
                local PS = game:GetService("Players")
                local Player = PS.LocalPlayer
                local Character = Player.Character or Player.CharacterAdded:Wait()
                local RS = game:GetService("ReplicatedStorage")
                local CE = RS:WaitForChild("CharacterEvents")
                local R = game:GetService("RunService")
                local BeingHeld = Player:WaitForChild("IsHeld")
                local StruggleEvent = CE:WaitForChild("Struggle")

                -- Prevent explosions from affecting the player
                local workspaceAdded = workspace.DescendantAdded:Connect(function(v)
                    if not _G.AntiGrab then return end
                    if v:IsA("Explosion") then
                        v.BlastPressure = 0
                        v.BlastRadius = 0
                    end
                end)

                local initialPosition

                -- Function to lock torso immediately
                local function lockTorso()
                    if not _G.AntiGrab then return end
                    local char = Player.Character
                    if char then
                        local HumanoidRootPart = char:FindFirstChild("HumanoidRootPart")
                        local Humanoid = char:FindFirstChild("Humanoid")
                        if HumanoidRootPart and Humanoid then
                            HumanoidRootPart.Anchored = true
                            HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                            Humanoid.PlatformStand = true
                            initialPosition = HumanoidRootPart.Position
                        end
                    end
                end

                -- Function to unlock torso immediately after release
                local function unlockTorso()
                    if not _G.AntiGrab then return end
                    local char = Player.Character
                    if char then
                        local HumanoidRootPart = char:FindFirstChild("HumanoidRootPart")
                        local Humanoid = char:FindFirstChild("Humanoid")
                        if HumanoidRootPart and Humanoid then
                            HumanoidRootPart.Anchored = false
                            Humanoid.PlatformStand = false
                        end
                    end
                end

                -- Detect when the player is grabbed or released
                local beingHeldChanged = BeingHeld.Changed:Connect(function(C)
                    if not _G.AntiGrab then return end
                    if C == true then
                        lockTorso()
                        -- Struggling part remains, but simplified to minimize lag
                        local struggleConnection
                        struggleConnection = R.Heartbeat:Connect(function()
                            if BeingHeld.Value == true then
                                StruggleEvent:FireServer(Player)
                            else
                                unlockTorso()
                                struggleConnection:Disconnect()
                            end
                        end)
                    else
                        unlockTorso()
                    end
                end)

                -- Prevent forced sitting while locked
                local function preventSit()
                    local Character = Player.Character or Player.CharacterAdded:Wait()
                    local Humanoid = Character:FindFirstChildWhichIsA("Humanoid") or Character:WaitForChild("Humanoid")
                    Humanoid:GetPropertyChangedSignal("Sit"):Connect(function()
                        if not _G.AntiGrab then return end
                        if Humanoid.Sit == true then
                            if Humanoid.SeatPart ~= nil and tostring(Humanoid.SeatPart.Parent) == "CreatureBlobman" then
                                return
                            else
                                Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                                Humanoid.Sit = false
                            end
                        end
                    end)
                end

                -- Initial setup for sitting prevention
                preventSit()

                -- Reconnect function to handle rejoining or character respawn
                local function reconnect()
                    if not _G.AntiGrab then return end
                    local Character = Player.Character or Player.CharacterAdded:Wait()
                    local Humanoid = Character:FindFirstChildWhichIsA("Humanoid") or Character:WaitForChild("Humanoid")
                    HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")

                    preventSit()
                end

                reconnect()
                local characterAdded = Player.CharacterAdded:Connect(reconnect)
            end)
        else
            _G.AntiGrab = false
        end
    end,
})



local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

local freezeDuration = -0.000000001  -- Duration of freeze in seconds (adjustable)
local frozen = false
local antiGrabEnabled = false  -- Track if the anti-grab is enabled or not
local reinforceConnection  -- Hold the connection for the heartbeat function

local function getCharacter(player)
    return player.Character or player.CharacterAdded:Wait()
end

-- Function to freeze the character temporarily
local function freezeCharacter()
    local character = getCharacter(LocalPlayer)
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    local humanoid = character:FindFirstChildOfClass("Humanoid")

    if humanoidRootPart and humanoid then
        humanoidRootPart.Anchored = true
        humanoid.PlatformStand = true  

        wait(freezeDuration)

        humanoidRootPart.Anchored = false
        humanoid.PlatformStand = false
    end
end

-- Function to detect grab and apply freeze
local function reinforceAntiGrab()
    if not antiGrabEnabled then return end  -- Exit if toggle is off

    local beingHeld = LocalPlayer:FindFirstChild("IsHeld")
    if beingHeld then
        reinforceConnection = RunService.Heartbeat:Connect(function()
            if not antiGrabEnabled then
                reinforceConnection:Disconnect()  -- Ensure it stops when disabled
                return
            end

            if beingHeld.Value and not frozen then
                frozen = true  
                freezeCharacter()  
                frozen = false  
            end
        end)
    end
end

-- Function to reset on death and re-enable anti-grab freeze
local function onCharacterAdded(character)
    if not antiGrabEnabled then return end  

    local humanoid = character:WaitForChild("Humanoid")  
    reinforceAntiGrab()  

    humanoid.Died:Connect(function()
        if antiGrabEnabled then
            reinforceAntiGrab()
        end
    end)
end

-- FedsHub Toggle Integration
local Toggle
Toggle = Tab:CreateToggle({
   Name = "Feds-anti",  
   CurrentValue = false,  
   Flag = "Toggle_AntiGrabFreeze",
   Callback = function(Value)
       antiGrabEnabled = Value  

       if Value then
           reinforceAntiGrab()
           print("⚡ Anti-Grab Freeze Activated ⚡")
       else
           if reinforceConnection then
               reinforceConnection:Disconnect()  
           end
           print("⚡ Anti-Grab Freeze Deactivated ⚡")
       end
   end,
})

LocalPlayer.CharacterAdded:Connect(function(character)
    if antiGrabEnabled then
        onCharacterAdded(character)
    end
end)


-- Call reset function on character added
LocalPlayer.CharacterAdded:Connect(function(character)
    resetAntiGrabOnDeath(character)
end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local playerName = LocalPlayer.Name

local antiGucciConnection
local positionCheckConnection
local looping = false
local blobman
local humanoid
local rootPart
local seat

local function spawnBlobman()
    local args = {
        [1] = "CreatureBlobman",
        [2] = CFrame.new(0, 50000, 0) * CFrame.Angles(-0.7351, 0.9028, 0.6173),
        [3] = Vector3.new(0, 59.667, 0)
    }

    blobman = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))

    local blobHead = Workspace:FindFirstChild(playerName .. "SpawnedInToys") and
                     Workspace[playerName .. "SpawnedInToys"]:FindFirstChild("CreatureBlobman") and
                     Workspace[playerName .. "SpawnedInToys"].CreatureBlobman:FindFirstChild("Head")

    if blobHead then
        blobHead.CFrame = CFrame.new(0, 50000, 0)
        blobHead.Anchored = true
    end
end

local function startAntiGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")

    local startPosition = rootPart.Position
    local isSitting = false

    seat = Workspace:FindFirstChild(playerName .. "SpawnedInToys") and
           Workspace[playerName .. "SpawnedInToys"]:FindFirstChild("CreatureBlobman") and
           Workspace[playerName .. "SpawnedInToys"].CreatureBlobman:FindFirstChild("VehicleSeat")

    if seat and seat:IsA("VehicleSeat") then
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
        seat:Sit(humanoid)
        isSitting = true
    end

    local function onJump()
        if isSitting and humanoid.Jump then
            task.wait(0.02)
            rootPart.CFrame = CFrame.new(startPosition)
            isSitting = false
        end
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(onJump)

    local function startLoop()
        looping = true
    end

    local function stopLoop()
        looping = false
    end

    antiGucciConnection = RunService.Heartbeat:Connect(function()
        if looping then
            ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(Workspace[playerName].HumanoidRootPart, 0)
        end
    end)

    positionCheckConnection = RunService.Heartbeat:Connect(function()
        if (rootPart.Position - startPosition).Magnitude < 1 then
            stopLoop()
        end
    end)

    if isSitting then
        startLoop()
    end
end

local function stopAntiGucci()
    looping = false

    if antiGucciConnection then
        antiGucciConnection:Disconnect()
        antiGucciConnection = nil
    end

    if positionCheckConnection then
        positionCheckConnection:Disconnect()
        positionCheckConnection = nil
    end

    if humanoid and humanoid.Sit then
        humanoid.Sit = false
    end

    local spawnedToys = Workspace:FindFirstChild(playerName .. "SpawnedInToys")
    if spawnedToys and spawnedToys:FindFirstChild("CreatureBlobman") then
        spawnedToys:FindFirstChild("CreatureBlobman"):Destroy()
    end
end

local Button = Tab:CreateButton({
    Name = "Anti-gucci (Reset to disable)",
    Callback = function()
        spawnBlobman()
        startAntiGucci()
    end,
})

local Section = Tab:CreateSection("Essentials")

local Button = Tab:CreateButton({
    Name = "Anti-bomb",
    Callback = function()
        local replicatedStorage = game:GetService("ReplicatedStorage")
        local bombEvents = replicatedStorage:WaitForChild("BombEvents")
        
        -- deletetion
        local bombReplicator = bombEvents:FindFirstChild("BombReplicator")
        if bombReplicator then
            bombReplicator:Destroy()
        end
        
        local bombExplode = bombEvents:FindFirstChild("BombExplode")
        if bombExplode then
            bombExplode:Destroy()
        end
    end,
 })

 local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local poisonMonitorConnection  -- Store connection for monitoring poison
local poisonParts = {}  -- Table to store poison parts

-- Function to block poison parts once
local function disablePoisonParts()
    for _, poisonPart in pairs(Workspace:GetDescendants()) do
        if poisonPart:IsA("Part") and poisonPart.Name == "PoisonHurtPart" then
            poisonPart.CanTouch = false  
            poisonPart.CanCollide = false  
            poisonPart.Size = Vector3.new(0, 0, 0)  
            table.insert(poisonParts, poisonPart)  -- Store the modified poison part
        end
    end
end

-- Function to monitor health and remove poison effects
local function monitorHealth()
    if poisonMonitorConnection then
        poisonMonitorConnection:Disconnect()
    end

    poisonMonitorConnection = RunService.Heartbeat:Connect(function()
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChild("Humanoid")
            if humanoid and humanoid.Health < humanoid.MaxHealth then
                humanoid.Health = humanoid.MaxHealth
            end
        end
    end)
end

-- Button to activate Anti-Poison
local Button = Tab:CreateButton({
    Name = "Anti-poison",
    Callback = function()
        disablePoisonParts()  -- Disable poison parts
        monitorHealth()  -- Start monitoring health
    end,
})

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local StarterGui = game:GetService("StarterGui")

-- Global Variables
local LocalPlayer = Players.LocalPlayer
local fpsdiv = 5
local destroyheight = Workspace.FallenPartsDestroyHeight
local dhoffset = 5
local dhto = 25
local sentnotif = false
local destroyheightnew = destroyheight + dhoffset
local connectsextra = {}
local charcframe = nil
local tpcframe = nil
local stopped = false
local character = LocalPlayer.Character
local characteradded
local stepped

-- Function to handle character logic
local function dochar(character)
    coroutine.wrap(function()
        repeat task.wait() until character:FindFirstChildWhichIsA("Humanoid")
        local hum = character:FindFirstChildWhichIsA("Humanoid")
        local state = hum:GetState()
        table.insert(connectsextra, hum.StateChanged:Connect(function(_, new)
            state = new
        end))
        local oldstate = nil
        while hum and hum.Parent and not stopped do
            task.wait()
            if state ~= oldstate and (state ~= Enum.HumanoidStateType.Jumping and state ~= Enum.HumanoidStateType.Freefall) or 
               (state == Enum.HumanoidStateType.Running or state == Enum.HumanoidStateType.Landed) then
                tpcframe = charcframe
            end
            oldstate = state
        end
    end)()
end

-- Notification Function
local function prompt(message, yesorno, yesfunc)
    pcall(function()
        if yesorno and yesfunc then
            local bindfunc = Instance.new("BindableFunction")
            bindfunc.OnInvoke = function(buttonname)
                if buttonname == "Yes" then
                    yesfunc()
                    sentnotif = false
                end
            end
            StarterGui:SetCore("SendNotification", {
                Title = "Notification",
                Text = message,
                Duration = 5,
                Callback = bindfunc,
                Button1 = "Yes",
                Button2 = "No"
            })
            task.wait(5)
            sentnotif = false
        else
            StarterGui:SetCore("SendNotification", {
                Title = "Notification",
                Text = message,
                Duration = 5
            })
        end
    end)
end

-- Function to prevent void kick
local function fixchar(part)
    if character then
        local piv = character:GetPivot()
        character:PivotTo(CFrame.new(piv.Position.X, destroyheight + dhto + character:GetExtentsSize().Y, piv.Position.Z))
    end
    if part then
        part.Velocity = Vector3.new(0, 0, 0)
        if character then
            for _, v in pairs(character:GetChildren()) do
                if v:IsA("BasePart") then
                    v.Velocity = Vector3.new(0, 0, 0)
                end
            end
        end
    end
    if tpcframe and not sentnotif then
        sentnotif = true
        prompt("Teleport back to last touched (buggy)", true, function()
            if character then
                character:PivotTo(tpcframe)
            end
            if part then
                part.Velocity = Vector3.new(0, 0, 0)
            end
        end)
    end
end

-- Toggle for Anti-Kick
local Toggle = Tab:CreateToggle({
    Name = "Anti-kick",
    CurrentValue = false,
    Flag = "AntiVoidToggle",
    Callback = function(Value)
        if Value then
            -- Enable Anti-Kick
            Workspace.FallenPartsDestroyHeight = -50000
            stopped = false

            if getgenv().deletescript123456lol69 then
                getgenv().deletescript123456lol69()
            end

            character = LocalPlayer.Character
            characteradded = LocalPlayer.CharacterAdded:Connect(function(character2)
                character = character2
                dochar(character2)
            end)
            dochar(character)

            stepped = RunService.Stepped:Connect(function()
                if character and character:FindFirstChildWhichIsA("BasePart") then
                    local part = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChildWhichIsA("BasePart")
                    local cfr = (character:FindFirstChild("HumanoidRootPart") and character.HumanoidRootPart.CFrame) or character:GetPivot()
                    charcframe = cfr
                    if cfr.Position.Y < destroyheightnew then
                        fixchar(part)
                    end
                    local partvel = part.Velocity
                    if (partvel.Y / fpsdiv) + part.Position.Y < destroyheightnew then
                        fixchar(part)
                    end
                end
            end)

            prompt("Anti-kick Loaded!")

            -- Cleanup function
            getgenv().deletescript123456lol69 = function()
                Workspace.FallenPartsDestroyHeight = destroyheight
                if characteradded then characteradded:Disconnect() end
                if stepped then stepped:Disconnect() end
                for _, v in pairs(connectsextra) do
                    v:Disconnect()
                end
                stopped = true
            end
        else
            -- Disable Anti-Kick
            if getgenv().deletescript123456lol69 then
                getgenv().deletescript123456lol69()
            end
            StarterGui:SetCore("SendNotification", {
                Title = "Anti-kick Disabled",
                Text = "Anti-kick is now inactive.",
                Duration = 5
            })
        end
    end,
})

local originalDestroyHeight = workspace.FallenPartsDestroyHeight  -- Store the original value

local Toggle = Tab:CreateToggle({
   Name = "Anti-void",
   CurrentValue = false,
   Flag = "AntiVoidHeightToggle",
   Callback = function(Value)
       if Value then
           workspace.FallenPartsDestroyHeight = -123478901740928312347089123478091234789012347890049381274873912009482317
           print("⚡ Anti-Void Activated: Height Modified ⚡")
       else
           workspace.FallenPartsDestroyHeight = originalDestroyHeight
           print("⚡ Anti-Void Deactivated: Height Restored ⚡")
       end
   end,
})

local Tab = Window:CreateTab("Auras") -- Title, Image

local Section = Tab:CreateSection("Main")

-- Global variables to avoid crossing the 200 limit
_G.GrabAuraActive = false -- Flag to track if the aura is active or not
_G.RunService = game:GetService("RunService")
_G.player = game.Players.LocalPlayer
_G.GrabAuraLoop = nil

-- Function to execute kill (unchanged)
local function ExecuteKill(otherHumanoid)
    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(otherHumanoid.RootPart, otherHumanoid.RootPart.CFrame)
    otherHumanoid.Health = 100
end

-- Create the toggle for Grab Aura
local Toggle = Tab:CreateToggle({
    Name = "Grab Aura",  -- Title
    CurrentValue = false, -- Initial state of the toggle
    Flag = "GrabAuraToggle", -- Unique flag for configuration saving
    Callback = function(Value)
        -- If turned on, start the aura
        if Value then
            _G.GrabAuraActive = true
            -- Start the loop that checks for players and applies the grab
            _G.GrabAuraLoop = _G.RunService.Heartbeat:Connect(function()
                if _G.GrabAuraActive then
                    for _, otherPlayer in ipairs(game.Players:GetPlayers()) do
                        if otherPlayer ~= _G.player and otherPlayer.Character then
                            local otherHumanoid = otherPlayer.Character:FindFirstChildOfClass("Humanoid")
                            if otherHumanoid then
                                ExecuteKill(otherHumanoid)
                            end
                        end
                    end
                end
            end)
        else
            -- If turned off, stop the aura
            _G.GrabAuraActive = false
            if _G.GrabAuraLoop then
                _G.GrabAuraLoop:Disconnect()  -- Disconnect the loop
            end
        end
    end,
})

-- Global variables to avoid crossing the 200 limit
_G.StrengthAuraActive = false -- Flag to track if the aura is active or not
_G.RunService = game:GetService("RunService")
_G.player = game.Players.LocalPlayer
_G.strength = 800
_G.localMaxDistance = 40

-- Get the player's character and humanoid
_G.character = _G.player.Character or _G.player.CharacterAdded:Wait()
_G.humanoid = _G.character:WaitForChild("Humanoid")

-- Function to execute the provided code
local function ExecuteCode(otherTorso)
    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(otherTorso, otherTorso.CFrame)
    local velocityObj = Instance.new("BodyVelocity", otherTorso)

    print("Launched!")
    velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    velocityObj.Velocity = Vector3.new(90, _G.strength, 90)

    wait(0.5) -- Wait for half a second before destroying the BodyVelocity object
    velocityObj:Destroy()
end

-- Loop variable
_G.StrengthAuraLoop = nil

-- Create the toggle for Strength Aura
local Toggle = Tab:CreateToggle({
    Name = "Strength Aura",  -- Title
    CurrentValue = false, -- Initial state of the toggle
    Flag = "StrengthAuraToggle", -- Unique flag for configuration saving
    Callback = function(Value)
        -- If turned on, start the aura
        if Value then
            _G.StrengthAuraActive = true
            -- Start the loop that checks for players within the max distance
            _G.StrengthAuraLoop = _G.RunService.Heartbeat:Connect(function()
                if _G.StrengthAuraActive then
                    -- Find all players within the specified distance
                    local players = game.Players:GetPlayers()
                    for _, otherPlayer in ipairs(players) do
                        -- Skip the local player
                        if otherPlayer ~= _G.player then
                            -- Get the other player's character and torso
                            local otherCharacter = otherPlayer.Character
                            if otherCharacter then
                                local otherTorso = otherCharacter:FindFirstChild("Torso")
                                if otherTorso then
                                    -- Calculate the distance between the players
                                    local distance = (otherTorso.Position - _G.humanoid.RootPart.Position).Magnitude

                                    -- Check if the distance is within the specified range
                                    if distance <= _G.localMaxDistance then
                                        -- Execute the provided code for each player
                                        ExecuteCode(otherTorso)
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        else
            -- If turned off, stop the aura
            _G.StrengthAuraActive = false
            if _G.StrengthAuraLoop then
                _G.StrengthAuraLoop:Disconnect()  -- Disconnect the loop
            end
        end
    end,
})

-- Global variables to avoid crossing the 200 limit
_G.KillAuraActive = false -- Flag to track if the aura is active or not
_G.RunService = game:GetService("RunService")
_G.player = game.Players.LocalPlayer

-- Define the default maximum distance to consider
_G.maxDistance = 35

-- Get the player's character and humanoid
_G.character = _G.player.Character or _G.player.CharacterAdded:Wait()
_G.humanoid = _G.character:WaitForChild("Humanoid")

-- Function to execute the provided code (Kill Aura logic)
local function ExecuteCode(otherHumanoid)
    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(otherHumanoid.RootPart, otherHumanoid.RootPart.CFrame)
    otherHumanoid.Health = 0
    otherHumanoid.Health = 100
    otherHumanoid.Health = 0
    otherHumanoid:ChangeState(Enum.HumanoidStateType.Dead)
end

-- Global variable for the KillAura loop
_G.KillAuraLoop = nil

-- Create the toggle for Kill Aura
local Toggle = Tab:CreateToggle({
    Name = "Kill Aura",  -- Title
    CurrentValue = false, -- Initial state of the toggle
    Flag = "KillAuraToggle", -- Unique flag for configuration saving
    Callback = function(Value)
        -- If turned on, start the aura
        if Value then
            _G.KillAuraActive = true
            -- Start the loop that checks for players within the max distance
            _G.KillAuraLoop = _G.RunService.Heartbeat:Connect(function()
                if _G.KillAuraActive then
                    -- Find all players within the specified distance
                    local players = game.Players:GetPlayers()
                    for _, otherPlayer in ipairs(players) do
                        -- Skip the local player
                        if otherPlayer ~= _G.player then
                            -- Get the other player's character and humanoid
                            local otherCharacter = otherPlayer.Character
                            if otherCharacter then
                                local otherHumanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
                                local otherHumanoid = otherCharacter:FindFirstChildOfClass("Humanoid")
                                if otherHumanoidRootPart and otherHumanoid then
                                    -- Calculate the distance between the players
                                    local distance = (otherHumanoidRootPart.Position - _G.humanoid.RootPart.Position).Magnitude

                                    -- Check if the distance is within the specified range
                                    if distance <= _G.maxDistance then
                                        -- Execute the provided code for each player
                                        ExecuteCode(otherHumanoid)
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        else
            -- If turned off, stop the aura
            _G.KillAuraActive = false
            if _G.KillAuraLoop then
                _G.KillAuraLoop:Disconnect()  -- Disconnect the loop
            end
        end
    end,
})

-- Global variables to avoid crossing the 200 limit
_G.SpeedAuraActive = false -- Flag to track if the aura is active or not
_G.RunService = game:GetService("RunService")
_G.player = game.Players.LocalPlayer

-- Define the default maximum distance to consider
_G.maxDistance = 40

-- Get the player's character and humanoid
_G.character = _G.player.Character or _G.player.CharacterAdded:Wait()
_G.humanoid = _G.character:WaitForChild("Humanoid")

-- Function to execute the provided code (Speed Aura logic)
local function ExecuteCode(otherHumanoid)
    game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(otherHumanoid.RootPart, otherHumanoid.RootPart.CFrame)
    otherHumanoid.Sit = false
    otherHumanoid.WalkSpeed = 350  -- Set the speed to 350 (can be modified with slider)
end

-- Global variable for the SpeedAura loop
_G.SpeedAuraLoop = nil

-- Create the toggle for Speed Aura
local Toggle = Tab:CreateToggle({
    Name = "Speed Aura",  -- Title
    CurrentValue = false, -- Initial state of the toggle
    Flag = "SpeedAuraToggle", -- Unique flag for configuration saving
    Callback = function(Value)
        -- If turned on, start the aura
        if Value then
            _G.SpeedAuraActive = true
            -- Start the loop that checks for players within the max distance
            _G.SpeedAuraLoop = _G.RunService.Heartbeat:Connect(function()
                if _G.SpeedAuraActive then
                    -- Find all players within the specified distance
                    local players = game.Players:GetPlayers()
                    for _, otherPlayer in ipairs(players) do
                        -- Skip the local player
                        if otherPlayer ~= _G.player then
                            -- Get the other player's character and humanoid
                            local otherCharacter = otherPlayer.Character
                            if otherCharacter then
                                local otherHumanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
                                local otherHumanoid = otherCharacter:FindFirstChildOfClass("Humanoid")
                                if otherHumanoidRootPart and otherHumanoid then
                                    -- Calculate the distance between the players
                                    local distance = (otherHumanoidRootPart.Position - _G.humanoid.RootPart.Position).Magnitude

                                    -- Check if the distance is within the specified range
                                    if distance <= _G.maxDistance then
                                        -- Execute the provided code for each player
                                        ExecuteCode(otherHumanoid)
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        else
            -- If turned off, stop the aura
            _G.SpeedAuraActive = false
            if _G.SpeedAuraLoop then
                _G.SpeedAuraLoop:Disconnect()  -- Disconnect the loop
            end
        end
    end,
})

local Section = Tab:CreateSection("Settings")
-- Create slider for controlling the max distance
local Slider = Tab:CreateSlider({
    Name = "Kill Aura Max Distance",  -- Slider Title
    Range = {0, 100},  -- Range for the distance (can be adjusted)
    Increment = 1,  -- Increment step for each move
    Suffix = "Studs",  -- Suffix to indicate the distance is in studs
    CurrentValue = 35,  -- Default value for the slider
    Flag = "KillAuraDistanceSlider", -- Unique flag for configuration saving
    Callback = function(Value)
        -- The variable (Value) will hold the slider value, which we can use to adjust the max distance
        maxDistance = Value
    end,
})

-- Create slider for controlling the max distance
local Slider = Tab:CreateSlider({
    Name = "Speed Aura Max Distance",  -- Slider Title
    Range = {0, 100},  -- Range for the distance (can be adjusted)
    Increment = 1,  -- Increment step for each move
    Suffix = "Studs",  -- Suffix to indicate the distance is in studs
    CurrentValue = 40,  -- Default value for the slider
    Flag = "SpeedAuraDistanceSlider", -- Unique flag for configuration saving
    Callback = function(Value)
        -- The variable (Value) will hold the slider value, which we can use to adjust the max distance
        maxDistance = Value
    end,
})

-- Create slider to control the strength value
local StrengthSlider = Tab:CreateSlider({
    Name = "Strength Aura Power",  -- Slider Title
    Range = {0, 500},  -- Range for the strength (can be adjusted)
    Increment = 10,  -- Increment step for each move
    Suffix = "Power",  -- Suffix to indicate power
    CurrentValue = _G.strength,  -- Default value for the slider
    Flag = "StrengthAuraPowerSlider", -- Unique flag for configuration saving
    Callback = function(Value)
        -- The variable (Value) will hold the slider value, which we can use to adjust the strength
        _G.strength = Value
        print("Strength set to: " .. _G.strength) -- Print the current strength value
    end,
})

local Tab = Window:CreateTab("Aim Bot") -- Title, Image

local Section = Tab:CreateSection("Main")

-- Global variables for Aim Bot functionality only
_G.Players = game:GetService("Players")
_G.LocalPlayer = _G.Players.LocalPlayer
_G.Camera = workspace.CurrentCamera
_G.RunService = game:GetService("RunService")
_G.ReplicatedStorage = game:GetService("ReplicatedStorage")
_G.AttackerAimActive = false
_G.connection = nil

-- Function to detect if the player is being grabbed
_G.getGrabber = function()
    for _, player in ipairs(_G.Players:GetPlayers()) do
        if player ~= _G.LocalPlayer and player.Character then
            local grabParts = player.Character:FindFirstChild("GrabParts")
            if grabParts then
                local grabPart = grabParts:FindFirstChild("GrabPart")
                if grabPart and grabPart:FindFirstChild("WeldConstraint") then
                    local weldConstraint = grabPart.WeldConstraint
                    if weldConstraint.Part1 and weldConstraint.Part1:IsDescendantOf(_G.LocalPlayer.Character) then
                        return player
                    end
                end
            end
        end
    end
    return nil
end

-- Function to enable Silent Aim when grabbed
_G.silentAim = function()
    local isAiming = false
    _G.connection = _G.RunService.RenderStepped:Connect(function()
        local grabber = _G.getGrabber()
        if grabber and grabber.Character then
            local head = grabber.Character:FindFirstChild("Head")
            if head then
                -- Aim at the grabber's head
                _G.Camera.CFrame = CFrame.new(_G.Camera.CFrame.Position, head.Position)
                -- Simulate clicking
                mouse1click()
                isAiming = true
            end
        elseif isAiming then
            -- Reset aim if no longer grabbed
            isAiming = false
        end
    end)
end

-- Toggle for Attacker Aim (kept as local)
local Toggle = Tab:CreateToggle({
    Name = "Attacker Aim",
    CurrentValue = false,
    Flag = "AttackerAimToggle",
    Callback = function(Value)
        if Value then
            -- When toggled on, start Silent Aim
            _G.AttackerAimActive = true
            _G.silentAim()
        else
            -- When toggled off, stop Silent Aim
            _G.AttackerAimActive = false
            if _G.connection then
                _G.connection:Disconnect()
            end
        end
    end,
})

-- Global variables for Aimbot functionality
_G.UserInputService = game:GetService("UserInputService")
_G.holdingQ = false
_G.AimbotActive = false

-- Function to get the nearest player
_G.getNearestPlayer = function()
    local closest, closestDist = nil, math.huge
    local myPos = _G.Camera.CFrame.Position
    for _, player in pairs(_G.Players:GetPlayers()) do
        if player ~= _G.LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (myPos - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < closestDist then
                closest, closestDist = player, distance
            end
        end
    end
    return closest
end

_G.UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Q then
        _G.holdingQ = true
    end
end)

_G.UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Q then
        _G.holdingQ = false
    end
end)

-- Function to enable the aimbot behavior
_G.aimbot = function()
    _G.RunService.RenderStepped:Connect(function()
        if _G.holdingQ then
            local target = _G.getNearestPlayer()
            if target and target.Character then
                _G.Camera.CFrame = CFrame.new(_G.Camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
            end
        end
    end)
end

-- Toggle for Aimbot (kept as local)
local Toggle = Tab:CreateToggle({
    Name = "Aimbot (Q)",
    CurrentValue = false,
    Flag = "AimbotToggle",
    Callback = function(Value)
        if Value then
            -- When toggled on, start the aimbot
            _G.AimbotActive = true
            _G.aimbot()
        else
            -- When toggled off, stop the aimbot
            _G.AimbotActive = false
        end
    end,
})

-- Toggle for Attacker Aim


local Tab = Window:CreateTab("Abilities") -- Title, Image

local Section = Tab:CreateSection("Main")

-- Global variables to avoid crossing the 200 limit
_G.Players = game:GetService("Players")
_G.UserInputService = game:GetService("UserInputService")
_G.Workspace = game:GetService("Workspace")
_G.Debris = game:GetService("Debris")
_G.RunService = game:GetService("RunService")
_G.localPlayer = _G.Players.LocalPlayer

-- Strength Grab Globals
_G.strength = 356
_G.grabConnection = nil

-- Freecam Globals
_G.player = _G.Players.LocalPlayer
_G.cam = _G.Workspace.CurrentCamera
_G.char = _G.player.Character or _G.player.CharacterAdded:Wait()
_G.head = _G.char:WaitForChild("Head")
_G.UIS = _G.UserInputService
_G.RS = _G.RunService
_G.freecamEnabled = false
_G.moveSpeed = 0.5
_G.mouseSensitivity = 0.2
_G.keysDown = {}

-- Poison Grab Globals
_G.poisonEnabled = false
_G.poison_part1 = _G.Workspace.Map.Hole.PoisonBigHole.PoisonHurtPart
_G.poison_part2 = _G.Workspace.Map.Hole.PoisonSmallHole.PoisonHurtPart
_G.poison_parts = {_G.poison_part1, _G.poison_part2}

-- Death Grab Globals
_G.deathGrabEnabled = false
_G.poison_part3 = nil
_G.poison_part4 = nil

-- Void Grab Globals
_G.voidGrabEnabled = false

-- Teleport Grab Globals
_G.teleportGrabEnabled = false
_G.teleportLocation = Vector3.new(122.50618, 346.693817, 312.610504)

-- Initialize additional poison parts
for _, part in pairs(_G.Workspace.Map.FactoryIsland:GetDescendants()) do
    if part.Name == "PoisonHurtPart" then
        if not _G.poison_part3 then
            _G.poison_part3 = part
            part.Transparency = 1
            part.Size = Vector3.new(0.5, 0.5, 0.5)
            part.Position = Vector3.new()
        elseif not _G.poison_part4 then
            _G.poison_part4 = part
            part.Transparency = 1
            part.Size = Vector3.new(0.5, 0.5, 0.5)
            part.Position = Vector3.new()
        end
        table.insert(_G.poison_parts, part)
    end
end

-- Strength Grab Functions
_G.strengthGrab = function()
    _G.grabConnection = _G.Workspace.ChildAdded:Connect(function(model)
        if model.Name == "GrabParts" then
            local part_to_impulse = model.GrabPart.WeldConstraint.Part1
            if part_to_impulse then
                print("Part found!")
                local velocityObj = Instance.new("BodyVelocity", part_to_impulse)
                
                model:GetPropertyChangedSignal("Parent"):Connect(function()
                    if not model.Parent then
                        if _G.UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                            print("Launched!")
                            velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            velocityObj.Velocity = _G.Workspace.CurrentCamera.CFrame.lookVector * _G.strength
                            _G.Debris:AddItem(velocityObj, 1)
                        elseif _G.UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton1 then
                            velocityObj:Destroy()
                            print("Cancel Launch!")
                        else
                            velocityObj:Destroy()
                            print("No two keys pressed!")
                        end
                    end
                end)
            end
        end
    end)
end

_G.stopStrengthGrab = function()
    if _G.grabConnection then
        _G.grabConnection:Disconnect()
        _G.grabConnection = nil
        print("Strength Grab Disabled")
    end
end

-- Freecam Functions
_G.enableFreecam = function()
    _G.freecamEnabled = true
    _G.cam.CameraType = Enum.CameraType.Scriptable
    _G.cam.CFrame = _G.head.CFrame
    _G.UIS.MouseBehavior = Enum.MouseBehavior.LockCenter
end

_G.disableFreecam = function()
    _G.freecamEnabled = false
    _G.cam.CameraType = Enum.CameraType.Custom
    _G.cam.CameraSubject = _G.char:FindFirstChild("Humanoid")
    _G.UIS.MouseBehavior = Enum.MouseBehavior.Default
end

_G.toggleFreecam = function()
    if _G.freecamEnabled then
        _G.disableFreecam()
    else
        _G.enableFreecam()
    end
end

-- Poison Functions
_G.applyPoison = function(humanoid)
    if humanoid and _G.poisonEnabled then
        local player = _G.Players:GetPlayerFromCharacter(humanoid.Parent)
        if not player then return end

        local poisonActive = true
        print(player.Name .. " is now poisoned!")

        task.spawn(function()
            while poisonActive and humanoid.Health > 0 and _G.poisonEnabled do
                humanoid:TakeDamage(10)
                task.wait(2)
            end
        end)

        task.delay(10, function()
            poisonActive = false
            print(player.Name .. " is no longer poisoned.")
        end)
    end
end

-- Death Grab Functions
_G.setupDeathGrabParts = function()
    _G.poison_part1.Size = Vector3.new(0.5, 0.5, 0.5)
    _G.poison_part2.Size = Vector3.new(0.5, 0.5, 0.5)
    _G.poison_part1.Position = Vector3.new(0, 0, 0)
    _G.poison_part2.Position = Vector3.new(0, 0, 0)
end

-- Void Grab Functions
_G.voidGrabHandler = function(model)
    if model.Name == "GrabParts" and _G.voidGrabEnabled then
        local part_to_impulse = model.GrabPart.WeldConstraint.Part1
        if part_to_impulse then
            part_to_impulse.Parent:BreakJoints()
            local player = part_to_impulse.Parent
            if player and player:FindFirstChild("HumanoidRootPart") then
                local humanoid = player:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid.Health = 0
                end

                local farDistance = -20202020
                local direction = Vector3.new(math.random(-1, 1), 0, math.random(-1, 1)) * farDistance
                player.HumanoidRootPart.CFrame = CFrame.new(player.HumanoidRootPart.Position + direction)
            end
        end
    end
end

-- Teleport Grab Functions
_G.smoothMove = function(targetCharacter)
    if not _G.teleportGrabEnabled then return end

    local humanoidRootPart = targetCharacter:FindFirstChild("HumanoidRootPart")
    if humanoidRootPart then
        local startPosition = humanoidRootPart.Position
        local duration = (startPosition - _G.teleportLocation).Magnitude / 100
        duration = math.clamp(duration, 0.5, 1.5)

        humanoidRootPart.Anchored = true

        local elapsedTime = 0
        local connection
        connection = _G.RunService.Heartbeat:Connect(function(deltaTime)
            if targetCharacter.Parent and _G.teleportGrabEnabled then
                elapsedTime = elapsedTime + deltaTime
                local alpha = math.clamp(elapsedTime / duration, 0, 1)
                humanoidRootPart.CFrame = CFrame.new(startPosition:Lerp(_G.teleportLocation, alpha))

                if alpha >= 1 then
                    humanoidRootPart.CFrame = CFrame.new(_G.teleportLocation)
                    humanoidRootPart.Anchored = false
                    connection:Disconnect()
                end
            else
                humanoidRootPart.Anchored = false
                connection:Disconnect()
            end
        end)
    end
end

-- Event Connections
_G.UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.G then
        _G.toggleFreecam()
    end
    if not _G.freecamEnabled then return end
    _G.keysDown[input.KeyCode] = true
end)

_G.UIS.InputEnded:Connect(function(input)
    _G.keysDown[input.KeyCode] = false
end)

_G.RS.RenderStepped:Connect(function()
    if not _G.freecamEnabled then return end

    local moveDir = Vector3.new()
    if _G.keysDown[Enum.KeyCode.W] then moveDir = moveDir + _G.cam.CFrame.LookVector end
    if _G.keysDown[Enum.KeyCode.S] then moveDir = moveDir - _G.cam.CFrame.LookVector end
    if _G.keysDown[Enum.KeyCode.A] then moveDir = moveDir - _G.cam.CFrame.RightVector end
    if _G.keysDown[Enum.KeyCode.D] then moveDir = moveDir + _G.cam.CFrame.RightVector end
    _G.cam.CFrame = _G.cam.CFrame + (moveDir * _G.moveSpeed)

    local delta = _G.UIS:GetMouseDelta()
    local xRot = CFrame.Angles(-math.rad(delta.Y * _G.mouseSensitivity), 0, 0)
    local yRot = CFrame.Angles(0, -math.rad(delta.X * _G.mouseSensitivity), 0)
    _G.cam.CFrame = _G.cam.CFrame * yRot * xRot
    
    _G.UIS.MouseBehavior = Enum.MouseBehavior.LockCenter
end)

_G.Workspace.ChildAdded:Connect(function(model)
    -- Strength Grab
    if model.Name == "GrabParts" and _G.grabConnection then
        local part_to_impulse = model.GrabPart.WeldConstraint.Part1
        if part_to_impulse then
            print("Part found!")
            local velocityObj = Instance.new("BodyVelocity", part_to_impulse)
            
            model:GetPropertyChangedSignal("Parent"):Connect(function()
                if not model.Parent then
                    if _G.UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                        print("Launched!")
                        velocityObj.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        velocityObj.Velocity = _G.Workspace.CurrentCamera.CFrame.lookVector * _G.strength
                        _G.Debris:AddItem(velocityObj, 1)
                    elseif _G.UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton1 then
                        velocityObj:Destroy()
                        print("Cancel Launch!")
                    else
                        velocityObj:Destroy()
                        print("No two keys pressed!")
                    end
                end
            end)
        end
    end
    
    -- Poison Grab
    if model.Name == "GrabParts" and _G.poisonEnabled then
        local part_to_impulse = model.GrabPart.WeldConstraint.Part1
        if part_to_impulse then
            local humanoid = part_to_impulse.Parent:FindFirstChild("Humanoid")
            local localPlayer = _G.Players.LocalPlayer
            if humanoid and not humanoid:IsDescendantOf(localPlayer.Character) then
                _G.applyPoison(humanoid)
            end
        end
    end
    
    -- Death Grab
    if model.Name == "GrabParts" and _G.deathGrabEnabled then
        local part_to_impulse = model.GrabPart.WeldConstraint.Part1
        if part_to_impulse then
            print("Part found!")
            local humanoid = part_to_impulse.Parent:FindFirstChild("Humanoid")
            local localPlayer = _G.Players.LocalPlayer
            if humanoid and not humanoid:IsDescendantOf(localPlayer.Character) then
                print("Poison Started!")
                local torso = part_to_impulse.Parent:FindFirstChild("Torso") or part_to_impulse.Parent:FindFirstChild("UpperTorso")
                if torso then
                    print("Player found")
                    torso:Destroy()
                    print("Poison ended!")
                end
            end
        end
    end
    
    -- Void Grab
    _G.voidGrabHandler(model)
    
    -- Teleport Grab
    if model.Name == "GrabParts" and _G.teleportGrabEnabled then
        local grabbedPart = model:FindFirstChild("GrabPart")
        if grabbedPart and grabbedPart:FindFirstChild("WeldConstraint") then
            local grabbedPlayer = grabbedPart.WeldConstraint.Part1.Parent
            if grabbedPlayer and grabbedPlayer:FindFirstChild("Humanoid") then
                _G.smoothMove(grabbedPlayer)
                print(grabbedPlayer.Name .. " is instantly gliding toward the target location!")
            end
        end
    end
end)

-- Toggles (keep exactly the same)
local ToggleStrengthGrab = Tab:CreateToggle({
    Name = "Strength Grab",
    CurrentValue = false,
    Flag = "StrengthGrabToggle",
    Callback = function(Value)
        if Value then
            _G.strengthGrab()
        else
            _G.stopStrengthGrab()
        end
    end,
})

local PerspectiveGrabToggle = Tab:CreateToggle({
    Name = "Free Grab (G)",
    CurrentValue = false,
    Flag = "PerspectiveGrabToggle",
    Callback = function(Value)
        if Value then
            _G.enableFreecam()
        else
            _G.disableFreecam()
        end
    end
})

local PoisonGrabToggle = Tab:CreateToggle({
    Name = "Poison Grab",
    CurrentValue = false,
    Flag = "PoisonGrabToggle",
    Callback = function(Value)
        _G.poisonEnabled = Value
    end,
})

local DeathGrabToggle = Tab:CreateToggle({
    Name = "Death Grab",
    CurrentValue = false,
    Flag = "DeathGrabToggle",
    Callback = function(Value)
        _G.deathGrabEnabled = Value
    end,
})

local VoidGrabToggle = Tab:CreateToggle({
    Name = "Void Grab",
    CurrentValue = false,
    Flag = "VoidGrabToggle",
    Callback = function(Value)
        _G.voidGrabEnabled = Value
    end,
})

local TeleportGrabToggle = Tab:CreateToggle({
    Name = "Teleport Grab",
    CurrentValue = false,
    Flag = "TeleportGrabToggle",
    Callback = function(Value)
        _G.teleportGrabEnabled = Value
    end,
})

print("All grab scripts loaded successfully!")
-- Anchor Grab Script with Global Variables to Avoid Roblox 200 Variable Limit

-- Global variables to avoid exceeding the 200 variable limit
_G.AnchorToggleEnabled = false  -- Toggle state
_G.HighlightColor = Color3.fromRGB(0, 255, 0) -- Green highlight for anchored objects

-- Global references for services
_G.UserInputService = game:GetService("UserInputService")
_G.ReplicatedStorage = game:GetService("ReplicatedStorage")
_G.Players = game:GetService("Players")
_G.LocalPlayer = _G.Players.LocalPlayer
_G.Character = _G.LocalPlayer.Character or _G.LocalPlayer.CharacterAdded:Wait()

-- Function to toggle anchor state
local function toggleAnchor(part)
    if part and part:IsA("BasePart") then
        part.Anchored = not part.Anchored
        
        -- Manage highlight effect
        local existingHighlight = part:FindFirstChild("AnchorHighlight")
        if part.Anchored then
            if not existingHighlight then
                local highlight = Instance.new("SelectionBox")
                highlight.Name = "AnchorHighlight"
                highlight.Adornee = part
                highlight.LineThickness = 0.05
                highlight.Color3 = _G.HighlightColor
                highlight.Parent = part
            end
        else
            if existingHighlight then
                existingHighlight:Destroy()
            end
        end
    end
end

-- Detect when the player presses "T"
_G.UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if _G.AnchorToggleEnabled and input.KeyCode == Enum.KeyCode.T then
        local grabParts = workspace:FindFirstChild("GrabParts")
        if grabParts and grabParts:FindFirstChild("GrabPart") then
            local grabbedObject = grabParts.GrabPart:FindFirstChild("WeldConstraint")
            if grabbedObject and grabbedObject.Part1 then
                toggleAnchor(grabbedObject.Part1)
            end
        end
    end
end)

-- GUI Toggle for enabling/disabling anchor functionality
local Toggle = Tab:CreateToggle({
   Name = "Anchor Grab (T)",
   CurrentValue = false,
   Flag = "AnchorToggle",
   Callback = function(Value)
       _G.AnchorToggleEnabled = Value
   end,
})

print("✅ Anchor Grab Loaded! Press 'T' to anchor/unanchor objects.")

print("🔥 Fire Grab Loaded!")

-- Global variables to avoid exceeding the 200 variable limit
_G.userinputs = game:GetService("UserInputService")
_G.w = game:GetService("Workspace")
_G.ReplicatedStorage = game:GetService("ReplicatedStorage")
_G.Players = game:GetService("Players")
_G.LocalPlayer = _G.Players.LocalPlayer
_G.toysFolder = _G.w:FindFirstChild(_G.LocalPlayer.Name .. "SpawnedInToys")

_G.Fire_Touch = false  -- Toggle state

_G.burnDuration = 30  -- Total duration of fire damage
_G.damagePerSecond = 50  -- Fire damage dealt per second

-- Function to spawn items
local function spawnItem(itemName, position)
    local args = {
        [1] = itemName,
        [2] = CFrame.new(position),
        [3] = position,
    }
    _G.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
end

-- Function to ignite the target
local function igniteTarget(humanoidRootPart)
    if not _G.toysFolder:FindFirstChild("Campfire") then
        spawnItem("Campfire", humanoidRootPart.Position)
    end

    local campfire = _G.toysFolder:FindFirstChild("Campfire")
    if campfire then
        local burnPart = campfire:FindFirstChild("FirePlayerPart") or campfire:WaitForChild("FirePlayerPart")
        if burnPart then
            burnPart.Size = Vector3.new(7, 7, 7)
            burnPart.Position = humanoidRootPart.Position

            -- Deal burn damage over time
            local fireTime = 0
            while fireTime < _G.burnDuration and _G.Fire_Touch do
                if humanoidRootPart and humanoidRootPart.Parent and humanoidRootPart.Parent:FindFirstChild("Humanoid") then
                    local humanoid = humanoidRootPart.Parent:FindFirstChild("Humanoid")
                    humanoid:TakeDamage(_G.damagePerSecond)
                end
                fireTime = fireTime + 1
                task.wait(1) -- Wait 1 second per tick
            end

            burnPart.Position = Vector3.new(0, -50, 0) -- Move fire away when done
        end
    end
end

-- Detecting when the player is grabbed and applying fire damage
_G.w.ChildAdded:Connect(function(model)
    if model.Name == "GrabParts" and _G.Fire_Touch then
        local part_to_impulse = model:FindFirstChild("GrabPart") and model["GrabPart"]:FindFirstChild("WeldConstraint") and model["GrabPart"]["WeldConstraint"].Part1
        
        if part_to_impulse then
            local humanoidRootPart = part_to_impulse.Parent:FindFirstChild("HumanoidRootPart")
            local humanoid = part_to_impulse.Parent:FindFirstChild("Humanoid")
            local localCharacter = _G.LocalPlayer.Character

            if humanoid and humanoidRootPart and not humanoid:IsDescendantOf(localCharacter) then
                print("🔥 Fire Grab Activated!")
                igniteTarget(humanoidRootPart)
            end
        end
    end
end)

-- Fire Grab Toggle
local FireGrabToggle = Tab:CreateToggle({
    Name = "Fire Grab",
    CurrentValue = false,
    Flag = "FireGrabToggle",
    Callback = function(Value)
        _G.Fire_Touch = Value
    end,
})

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

-- Global variables to avoid exceeding the 200 variable limit
_G.PoisonDragEnabled = false -- Toggle state

-- Poison Hole Parts (for visuals & damage source)
_G.poison_part1 = Workspace["Map"]["Hole"]["PoisonBigHole"]["PoisonHurtPart"]
_G.poison_part2 = Workspace["Map"]["Hole"]["PoisonSmallHole"]["PoisonHurtPart"]

-- Configure poison size & transparency (make sure it's visible)
_G.poison_part1.Size, _G.poison_part2.Size = Vector3.new(3, 3, 3), Vector3.new(3, 3, 3)
_G.poison_part1.Transparency, _G.poison_part2.Transparency = 0.5, 0.5 -- Make poison slightly visible

local function spawnPoisonEffect(target)
    if target then
        local poisonEffect = Instance.new("ParticleEmitter")
        poisonEffect.Texture = "rbxassetid://271832000" -- Poison effect texture
        poisonEffect.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0)) -- Green poison color
        poisonEffect.Size = NumberSequence.new({NumberSequenceKeypoint.new(0, 2), NumberSequenceKeypoint.new(1, 4)})
        poisonEffect.Rate = 50
        poisonEffect.Lifetime = NumberRange.new(1, 2)
        poisonEffect.Parent = target
        return poisonEffect
    end
end

Workspace.ChildAdded:Connect(function(model)
    if model.Name == "GrabParts" and _G.PoisonDragEnabled then
        local part_to_impulse = model:FindFirstChild("GrabPart") and model["GrabPart"]:FindFirstChild("WeldConstraint") and model["GrabPart"]["WeldConstraint"].Part1

        if part_to_impulse then
            print("☠️ Target Found!")

            local humanoidRootPart = part_to_impulse.Parent:FindFirstChild("HumanoidRootPart")
            local humanoid = part_to_impulse.Parent:FindFirstChild("Humanoid")
            local localCharacter = _G.LocalPlayer.Character

            if humanoid and humanoidRootPart and not humanoid:IsDescendantOf(localCharacter) then
                print("☠️ Poison Applied!")

                -- Spawn poison effect on the player
                local poisonEffect = spawnPoisonEffect(humanoidRootPart)

                -- Send them to the poison hole & kill them
                task.wait(0.3)
                humanoidRootPart.CFrame = _G.poison_part1.CFrame + Vector3.new(0, 3, 0) -- Teleport to poison
                humanoid.Health = 0 -- Instantly kill

                -- Remove poison effect after death
                task.wait(1)
                if poisonEffect then
                    poisonEffect:Destroy()
                end
            end
        end
    end
end)

-- Poison Drag Toggle
local PoisonDragToggle = Tab:CreateToggle({
    Name = "Poison Drag",
    CurrentValue = false,
    Flag = "PoisonDragToggle",
    Callback = function(Value)
        _G.PoisonDragEnabled = Value
    end,
})

local Section = Tab:CreateSection("Settings")

-- Strength Grab Strength Slider
local StrengthGrabSlider = Tab:CreateSlider({
    Name = "Strength Grab Power",  -- Title of the slider
    Range = {0, 1000},  -- Min and Max strength range
    Increment = 10,  -- Increment value
    Suffix = " Power",  -- Suffix to show in UI
    CurrentValue = _G.strength,  -- Initial value for the strength variable
    Flag = "StrengthGrabPower",  -- Unique flag for configuration saving
    Callback = function(Value)
        _G.strength = Value  -- Set the strength to the new value from the slider
    end,
})

-- Free Grab Speed Slider
local FreeGrabSpeedSlider = Tab:CreateSlider({
    Name = "Free Grab Speed",  -- Title of the slider
    Range = {0.1, 5},  -- Min and Max speed range for Free Grab
    Increment = 0.1,  -- Increment value
    Suffix = " Speed",  -- Suffix to show in UI
    CurrentValue = _G.moveSpeed,  -- Initial value for the moveSpeed variable
    Flag = "FreeGrabSpeed",  -- Unique flag for configuration saving
    Callback = function(Value)
        _G.moveSpeed = Value  -- Set the speed to the new value from the slider
    end,
})

local Tab = Window:CreateTab("People") -- Title, Image

local Section = Tab:CreateSection("Loop kill (Stop to regain movement)")

-- Create the global variables
getgenv().mainScriptVars = {
    isnetworkowner = function(Part)
        if Part.ReceiveAge == 0 and not Part.Anchored then
            return true
        end
        return false
    end,
    RunService = game:GetService("RunService"),
    Players = game:GetService("Players"),
    ReplicatedStorage = game:GetService("ReplicatedStorage"),
    LocalPlayer = Players.LocalPlayer,
    targetNames = {},
    loopActive = false,
    lastPositions = {},
    stayDuration = 0.000000000001,
    returnDuration = 1,
    magnetRange = 1,
    teleportSpeed = 0.8,
    playerIsInVoid = false
}

local vars = getgenv().mainScriptVars

-- Rest of the functions will go here (I'll provide them in next parts)
vars.ExecuteKill = function(targetCharacter)
    local humanoid = targetCharacter:FindFirstChildOfClass("Humanoid")
    local rootPart = targetCharacter:FindFirstChild("HumanoidRootPart")
    if humanoid and rootPart then
        vars.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(rootPart, rootPart.CFrame)
        
        for _ = 1, 10 do
            humanoid.Health = 0
            humanoid:ChangeState(Enum.HumanoidStateType.Dead)
            task.wait(0.02)
        end

        local farDistance = -20202020
        local direction = Vector3.new(math.random(-1, 1), 0, math.random(-1, 1)) * farDistance
        rootPart.CFrame = CFrame.new(rootPart.Position + direction)
    end
end

vars.teleportTo = function(position)
    if vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = vars.LocalPlayer.Character.HumanoidRootPart
        root.CFrame = root.CFrame:Lerp(position, vars.teleportSpeed)
    end
end

vars.teleportBehind = function(targetRoot)
    if vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = vars.LocalPlayer.Character.HumanoidRootPart
        local direction = targetRoot.CFrame.LookVector * -2
        root.CFrame = targetRoot.CFrame + direction
    end
end

vars.stickToTarget = function(targetRoot)
    if vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        if not vars.isnetworkowner(targetRoot) then
            local root = vars.LocalPlayer.Character.HumanoidRootPart
            local distance = (root.Position - targetRoot.Position).Magnitude

            if distance < vars.magnetRange then
                root.CFrame = targetRoot.CFrame + Vector3.new(0, 0, -1)
                root.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end
end

vars.setMovementFreeze = function(isFrozen)
    if vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = vars.LocalPlayer.Character.Humanoid
        if isFrozen then
            humanoid.PlatformStand = false
            humanoid:Move(Vector3.new(0, 0, 0))
        else
            humanoid.PlatformStand = false
        end
    end
end
vars.startLoopKill = function(targetPlayer)
    if not targetPlayer then return end

    if not vars.lastPositions[targetPlayer.Name] and vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        vars.lastPositions[targetPlayer.Name] = vars.LocalPlayer.Character.HumanoidRootPart.CFrame
    end

    while vars.loopActive do
        local targetCharacter = targetPlayer.Character
        warn(targetPlayer.InPlot)
        warn(targetPlayer.InPlot.Value)

        if (targetPlayer.InPlot and not targetPlayer.InPlot.Value) and targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart") then
            local targetRoot = targetCharacter.HumanoidRootPart
            local TargetHum = targetCharacter:FindFirstChildWhichIsA("Humanoid")
            warn("loop")

            if targetRoot.Position.Y < -100 then
                task.wait(0.5)
                continue
            end

            local distanceToTarget = (vars.LocalPlayer.Character.HumanoidRootPart.Position - targetRoot.Position).Magnitude
            if distanceToTarget > 900 then
                task.wait(0.2)
                continue
            end

            if TargetHum:GetState() ~= Enum.HumanoidStateType.Dead then
                vars.setMovementFreeze(true)
            end

            vars.teleportBehind(targetRoot)
            task.wait(0.2)
            vars.stickToTarget(targetRoot)

            vars.ExecuteKill(targetCharacter)
            task.wait(vars.stayDuration)

            if TargetHum:GetState() == Enum.HumanoidStateType.Dead then
                vars.setMovementFreeze(true)
            end
        end

        if vars.lastPositions[targetPlayer.Name] then
            vars.teleportTo(vars.lastPositions[targetPlayer.Name])
            task.wait(vars.returnDuration)
            vars.setMovementFreeze(true)
        end
        task.wait()
    end
end

vars.autoAdjustLoop = function()
    while vars.loopActive do
        for _, targetName in ipairs(vars.targetNames) do
            local targetPlayer = vars.Players:FindFirstChild(targetName)
            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                if targetPlayer.Character.HumanoidRootPart.Position.Y > -100 then
                    task.spawn(function() vars.startLoopKill(targetPlayer) end)
                end
            end
        end
        task.wait(0.1)
    end
end
-- Heartbeat connections
vars.RunService.Heartbeat:Connect(function()
    if not vars.loopActive then return end

    for _, targetName in ipairs(vars.targetNames) do
        local targetPlayer = vars.Players:FindFirstChild(targetName)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if not vars.lastPositions[targetPlayer.Name] then
                vars.lastPositions[targetPlayer.Name] = vars.LocalPlayer.Character.HumanoidRootPart.CFrame
            end
        end
    end
end)

vars.RunService.Heartbeat:Connect(function()
    if vars.LocalPlayer.Character and vars.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local rootPart = vars.LocalPlayer.Character.HumanoidRootPart
        if rootPart.Position.Y < -100 then
            vars.playerIsInVoid = true
            if vars.lastPositions[vars.LocalPlayer.Name] then
                vars.teleportTo(vars.lastPositions[vars.LocalPlayer.Name])
            end
        else
            vars.playerIsInVoid = false
        end
    end
end)

vars.forceRestartLoop = function()
    vars.loopActive = false
    task.wait(1)
    vars.loopActive = true
end

vars.RunService.Heartbeat:Connect(function()
    if not vars.loopActive then
        vars.forceRestartLoop()
    end
end)

-- Player tracking functions
local function updatePlayerList()
    local playerNames = {}
    for _, player in ipairs(vars.Players:GetPlayers()) do
        if player ~= vars.LocalPlayer then
            table.insert(playerNames, player.Name)
        end
    end
    return playerNames
end

-- Create the UI
local Toggle = Tab:CreateToggle({
    Name = "Loop kill",
    CurrentValue = false,
    Flag = "KillLoopToggle",
    Callback = function(Value)
        vars.loopActive = Value
        if Value then
            task.spawn(vars.autoAdjustLoop)
        end
    end,
})

local playerDropdown = Tab:CreateDropdown({
    Name = "Select players",
    Options = updatePlayerList(),
    CurrentOption = {},
    MultipleOptions = true,
    Flag = "TargetPlayersDropdown",
    Callback = function(Options)
        vars.targetNames = Options
    end,
})

-- Update player list when players join/leave
vars.Players.PlayerAdded:Connect(function(player)
    playerDropdown:Refresh(updatePlayerList(), true)
end)

vars.Players.PlayerRemoving:Connect(function(player)
    playerDropdown:Refresh(updatePlayerList(40), true)
end)
vars.optimizePerformance = function()
    local lastUpdate = tick()
    vars.RunService.Heartbeat:Connect(function()
        if tick() - lastUpdate >= 0.1 then
            task.wait(50)
            lastUpdate = tick()
        end
    end)
end

vars.controlledWait = function(time)
    local startTime = tick()
    repeat
        task.wait()
    until tick() - startTime >= time
end

vars.adjustStayDuration = function()
    while vars.loopActive do
        vars.stayDuration = math.max(vars.stayDuration * 0.005, 0.001)
        task.wait(0.01)
    end
end

vars.preventLag = function()
    local lastUpdate = tick()
    vars.RunService.Heartbeat:Connect(function()
        if tick() - lastUpdate >= 0.1 then
            task.wait(0.05)
            lastUpdate = tick()
        end
    end)
end

vars.monitorLoop = function()
    while vars.loopActive do
        if not vars.loopActive then
            warn("Loop stopped unexpectedly! Restarting...")
            vars.loopActive = true
            task.spawn(vars.autoAdjustLoop)
        end
        task.wait(1)
    end
end

vars.preventErrors = function()
    local success, err = pcall(function()
        while vars.loopActive do
            task.wait(0.5)
        end
    end)
    if not success then
        warn("Error detected: " .. err)
        task.wait(1)
        vars.loopActive = true
        task.spawn(vars.autoAdjustLoop)
    end
end

-- Initialize
vars.optimizePerformance()
vars.controlledWait(0.1)
vars.preventLag()
task.spawn(vars.monitorLoop)
task.spawn(vars.preventErrors)

local Section = Tab:CreateSection("Loop bounce")

local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Global variables
_G.LoopFling = {
    targetNames = {},
    loopActive = false,
    lastPosition = nil,
    stayDuration = 1,
    returnDuration = 0.4,
    magnetRange = 1,
    teleportSpeed = 0.8,
    playerIsInVoid = false
}

-- Function to update player list in dropdown
local function updatePlayerList(dropdown)
    local currentPlayers = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(currentPlayers, player.Name)
        end
    end
    dropdown:Refresh(currentPlayers, false)
end

-- Create UI elements
local Toggle = Tab:CreateToggle({
    Name = "Loop bounce",
    CurrentValue = _G.LoopFling.loopActive,
    Flag = "LoopFlingToggle",
    Callback = function(Value)
        _G.LoopFling.loopActive = Value
    end,
})

local Dropdown = Tab:CreateDropdown({
    Name = "Select players",
    Options = {},
    CurrentOption = {},
    MultipleOptions = true,
    Flag = "LoopFlingDropdown",
    Callback = function(Options)
        _G.LoopFling.targetNames = Options
    end,
})

-- Initial player list update
updatePlayerList(Dropdown)

-- Update player list when players join/leave
Players.PlayerAdded:Connect(function()
    updatePlayerList(Dropdown)
end)

Players.PlayerRemoving:Connect(function()
    updatePlayerList(Dropdown)
end)
-- Function to apply insane fling to the target (instead of killing)
local function ExecuteFling(targetCharacter)
    local humanoid = targetCharacter:FindFirstChildOfClass("Humanoid")
    local rootPart = targetCharacter:FindFirstChild("HumanoidRootPart")
    if humanoid and rootPart then
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(rootPart, rootPart.CFrame)
        
        -- Apply extreme BodyVelocity for flinging
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(100000, 100000, 100000) -- Extremely high force
        bodyVelocity.Velocity = Vector3.new(0, 1000, 0) -- Upwards velocity
        bodyVelocity.Parent = rootPart

        -- Fling the player in a loop (up and down)
        local isGoingUp = true
        while true do
            if isGoingUp then
                bodyVelocity.Velocity = Vector3.new(0, 1000, 0) -- Push player upwards
            else
                bodyVelocity.Velocity = Vector3.new(0, -1000, 0) -- Pull player downwards with massive force
            end

            isGoingUp = not isGoingUp -- Toggle the direction of movement

            -- Wait for a short time before changing direction
            wait(0.5)
        end
    end
end

-- Function to teleport smoothly
local function teleportTo(position)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = LocalPlayer.Character.HumanoidRootPart
        for _ = 1, 5 do
            root.CFrame = root.CFrame:Lerp(position, _G.LoopFling.teleportSpeed)
            task.wait(0.02)
        end
    end
end

-- Magnet effect to stick to target
local function stickToTarget(targetRoot)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = LocalPlayer.Character.HumanoidRootPart
        local distance = (root.Position - targetRoot.Position).Magnitude

        if distance < _G.LoopFling.magnetRange then
            root.CFrame = targetRoot.CFrame + Vector3.new(0, 0, -1)
            root.Velocity = Vector3.new(0, 0, 0)
        end
    end
end
-- Function to start LoopFling
local function startLoopFling(targetPlayer)
    if not targetPlayer then return end

    -- Save last position only ONCE to ensure perfect return
    if not _G.LoopFling.lastPosition and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        _G.LoopFling.lastPosition = LocalPlayer.Character.HumanoidRootPart.CFrame
    end

    while _G.LoopFling.loopActive do
        local targetCharacter = targetPlayer.Character
        if targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart") then
            local targetRoot = targetCharacter.HumanoidRootPart

            -- Teleport behind target & magnetize
            teleportTo(targetRoot.CFrame + Vector3.new(0, 0, -2))
            task.wait(0.1)
            stickToTarget(targetRoot)

            -- Fling the target player (instead of killing)
            ExecuteFling(targetCharacter)

            task.wait(_G.LoopFling.stayDuration)
        end

        -- Return to last saved position (faster return)
        if _G.LoopFling.lastPosition then
            teleportTo(_G.LoopFling.lastPosition)
            task.wait(_G.LoopFling.returnDuration)
        end
    end
end

-- Auto-restarts the loop if player resets or is not found
local function autoAdjustLoop()
    while _G.LoopFling.loopActive do
        for _, targetName in ipairs(_G.LoopFling.targetNames) do
            local targetPlayer = Players:FindFirstChild(targetName)
            if targetPlayer and targetPlayer.Character then
                task.spawn(function() startLoopFling(targetPlayer) end)
            end
        end
        task.wait(0.1)
    end
end

-- Check for player reset and maintain loop
RunService.Heartbeat:Connect(function()
    if not _G.LoopFling.loopActive then return end

    for _, targetName in ipairs(_G.LoopFling.targetNames) do
        local targetPlayer = Players:FindFirstChild(targetName)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if not _G.LoopFling.lastPosition then
                _G.LoopFling.lastPosition = LocalPlayer.Character.HumanoidRootPart.CFrame
            end
        end
    end
end)

-- Ensure player is teleported back if they enter the void
RunService.Heartbeat:Connect(function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local rootPart = LocalPlayer.Character.HumanoidRootPart
        -- Check if the player is in the void (y position < -100)
        if rootPart.Position.Y < -100 then
            _G.LoopFling.playerIsInVoid = true
            -- Teleport the player back to the last position to prevent getting stuck
            if _G.LoopFling.lastPosition then
                teleportTo(_G.LoopFling.lastPosition)
            end
        else
            _G.LoopFling.playerIsInVoid = false
        end
    end
end)

-- Force a restart of the loop if necessary
local function forceRestartLoop()
    _G.LoopFling.loopActive = false
    task.wait(1)
    _G.LoopFling.loopActive = true
end

-- Trigger the force restart if conditions are met
RunService.Heartbeat:Connect(function()
    -- If loop stops working after a while, force a restart
    if not _G.LoopFling.loopActive then
        forceRestartLoop()
    end
end)

-- Start the loop when toggle is enabled
Toggle.Callback = function(Value)
    _G.LoopFling.loopActive = Value
    if Value then
        task.spawn(autoAdjustLoop)
    end
end

local Section = Tab:CreateSection("Spectate")

-- Global variables to avoid exceeding Roblox's 200 local limit
local _SpectateGlobals = {
    players = game:GetService("Players"),
    camera = workspace.CurrentCamera,
    localPlayer = nil,
    targetPlayer = nil,
    spectating = false,
    playerList = {}
}

-- Initialize local player
_SpectateGlobals.localPlayer = _SpectateGlobals.players.LocalPlayer

-- Function to update player list
local function updatePlayerList()
    _SpectateGlobals.playerList = {}
    for _, player in ipairs(_SpectateGlobals.players:GetPlayers()) do
        if player ~= _SpectateGlobals.localPlayer then
            table.insert(_SpectateGlobals.playerList, player.Name)
        end
    end
    return _SpectateGlobals.playerList
end

-- Function to spectate a player
local function spectatePlayer(targetName)
    _SpectateGlobals.targetPlayer = _SpectateGlobals.players:FindFirstChild(targetName)
    if _SpectateGlobals.targetPlayer and _SpectateGlobals.targetPlayer.Character 
       and _SpectateGlobals.targetPlayer.Character:FindFirstChild("Humanoid") then
        _SpectateGlobals.camera.CameraSubject = _SpectateGlobals.targetPlayer.Character.Humanoid
        print("Now spectating: " .. targetName)
        _SpectateGlobals.spectating = true
    else
        warn("Player not found or not loaded correctly.")
        _SpectateGlobals.spectating = false
    end
end
-- Player tracking connections  
local function setupPlayerTracking()  
    -- Update player list when someone joins  
    _SpectateGlobals.players.PlayerAdded:Connect(function(player)  
        updatePlayerList(10)  
        if Dropdown then  
            Dropdown.Refresh(_SpectateGlobals.playerList, false)  
        end  
    end)  

    -- Update player list when someone leaves  
    _SpectateGlobals.players.PlayerRemoving:Connect(function(player)  
        updatePlayerList(10)  
        if Dropdown then  
            Dropdown.Refresh(_SpectateGlobals.playerList, false)  
        end  
        -- Stop spectating if the target leaves  
        if _SpectateGlobals.spectating and player.Name == (_SpectateGlobals.targetPlayer and _SpectateGlobals.targetPlayer.Name) then  
            _SpectateGlobals.camera.CameraSubject = _SpectateGlobals.localPlayer.Character and _SpectateGlobals.localPlayer.Character:FindFirstChild("Humanoid")  
            _SpectateGlobals.spectating = false  
        end  
    end)  
end  

-- Create the Toggle  
local Toggle = Tab:CreateToggle({  
    Name = "Spectate",  
    CurrentValue = false,  
    Flag = "SpectateToggle",  
    Callback = function(Value)  
        if Value then  
            if _SpectateGlobals.targetPlayer then  
                spectatePlayer(_SpectateGlobals.targetPlayer.Name)  
            else  
                warn("No player selected!")  
                Toggle.Set(false)  
            end  
        else  
            if _SpectateGlobals.localPlayer.Character then  
                _SpectateGlobals.camera.CameraSubject = _SpectateGlobals.localPlayer.Character:FindFirstChild("Humanoid")  
                _SpectateGlobals.spectating = false  
            end  
        end  
    end,  
})  

-- Create the Dropdown  
local Dropdown = Tab:CreateDropdown({  
    Name = "Select Player",  
    Options = updatePlayerList(),  
    CurrentOption = {},  
    MultipleOptions = false,  
    Flag = "SpectateDropdown",  
    Callback = function(Option)  
        if #Option > 0 then  
            _SpectateGlobals.targetPlayer = _SpectateGlobals.players:FindFirstChild(Option[1])  
            if Toggle.CurrentValue then  
                spectatePlayer(Option[1])  
            end  
        else  
            _SpectateGlobals.targetPlayer = nil  
            if Toggle.CurrentValue then  
                Toggle.Set(false)  
            end  
        end  
    end,  
})  

-- Initialize player tracking  
setupPlayerTracking()  

local Tab = Window:CreateTab("Attacks") -- Title, Image

local Section = Tab:CreateSection("Main")

-- Global variables for Void Attack
local VoidAttackEnabled = false
local VoidAttackConnections = {}

-- Void Attack function
local function setupVoidAttack()
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local Camera = workspace.CurrentCamera
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    -- Function to detect if the player is being grabbed
    local function getGrabber()
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                local grabParts = player.Character:FindFirstChild("GrabParts")
                if grabParts then
                    local grabPart = grabParts:FindFirstChild("GrabPart")
                    if grabPart and grabPart:FindFirstChild("WeldConstraint") then
                        local weldConstraint = grabPart.WeldConstraint
                        if weldConstraint.Part1 and weldConstraint.Part1:IsDescendantOf(LocalPlayer.Character) then
                            return player
                        end
                    end
                end
            end
        end
        return nil
    end

    -- Function to enable Silent Aim when grabbed
    local function silentAim()
        local isAiming = false
        local connection = RunService.RenderStepped:Connect(function()
            local grabber = getGrabber()
            if grabber and grabber.Character then
                local head = grabber.Character:FindFirstChild("Head")
                if head then
                    -- Aim at the grabber's head
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, head.Position)
                    -- Simulate clicking
                    mouse1click()
                    isAiming = true
                end
            elseif isAiming then
                -- Reset aim if no longer grabbed
                isAiming = false
            end
        end)
        table.insert(VoidAttackConnections, connection)
    end

    silentAim()

    local childAddedConnection = game:GetService("Workspace").ChildAdded:Connect(function(model)
        if model.Name == "GrabParts" then
            local part_to_impulse = model["GrabPart"]["WeldConstraint"].Part1
            if part_to_impulse then
                part_to_impulse.Parent:BreakJoints()
                local player = part_to_impulse.Parent
                if player and player:FindFirstChild("HumanoidRootPart") then
                    local humanoid = player:FindFirstChildOfClass("Humanoid")
                    if humanoid then
                        humanoid.Health = 0 
                    end

                    local farDistance = -20202020
                    local direction = Vector3.new(math.random(-1, 1), 0, math.random(-1, 1)) * farDistance
                    player.HumanoidRootPart.CFrame = CFrame.new(player.HumanoidRootPart.Position + direction)
                end
            end
        end
    end)
    table.insert(VoidAttackConnections, childAddedConnection)
end

-- Global variables for Poison Attack
local PoisonAttackEnabled = false
local PoisonAttackConnections = {}

-- Poison Attack function
local function setupPoisonAttack()
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local Camera = workspace.CurrentCamera
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    -- Function to detect if the player is being grabbed
    local function getGrabber()
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                local grabParts = player.Character:FindFirstChild("GrabParts")
                if grabParts then
                    local grabPart = grabParts:FindFirstChild("GrabPart")
                    if grabPart and grabPart:FindFirstChild("WeldConstraint") then
                        local weldConstraint = grabPart.WeldConstraint
                        if weldConstraint.Part1 and weldConstraint.Part1:IsDescendantOf(LocalPlayer.Character) then
                            return player
                        end
                    end
                end
            end
        end
        return nil
    end

    -- Function to enable Silent Aim when grabbed
    local function silentAim()
        local isAiming = false
        local connection = RunService.RenderStepped:Connect(function()
            local grabber = getGrabber()
            if grabber and grabber.Character then
                local head = grabber.Character:FindFirstChild("Head")
                if head then
                    -- Aim at the grabber's head
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, head.Position)
                    -- Simulate clicking
                    mouse1click()
                    isAiming = true
                end
            elseif isAiming then
                -- Reset aim if no longer grabbed
                isAiming = false
            end
        end)
        table.insert(PoisonAttackConnections, connection)
    end

    silentAim()

    print("Poison Grab Loaded!")

    local userinputs = game:GetService("UserInputService")
    local w = game:GetService("Workspace")
    local r = game:GetService("RunService")
    local players = game:GetService("Players")

    local poison_part1 = w["Map"]["Hole"]["PoisonBigHole"]["PoisonHurtPart"]
    local poison_part2 = w["Map"]["Hole"]["PoisonSmallHole"]["PoisonHurtPart"]

    local poison_parts = {poison_part1, poison_part2}

    -- Add more poison parts in the Factory Island area
    for _, part in pairs(w["Map"]["FactoryIsland"]:GetDescendants()) do
        if part.Name == "PoisonHurtPart" then
            table.insert(poison_parts, part)
        end
    end

    -- Function to apply poison damage over time
    local function applyPoison(humanoid)
        if humanoid then
            local player = players:GetPlayerFromCharacter(humanoid.Parent)
            if not player then return end

            local poisonActive = true  -- Keep track if poison should continue
            print(player.Name .. " is now poisoned!")

            -- Damage loop (5 HP per second)
            task.spawn(function()
                while poisonActive and humanoid.Health > 0 do
                    humanoid:TakeDamage(5)
                    task.wait(1)  -- Apply damage every second
                end
            end)

            -- Stop poison effect after a duration (10 seconds for example)
            task.delay(10, function()
                poisonActive = false
                print(player.Name .. " is no longer poisoned.")
            end)
        end
    end

    -- Detect when a player is grabbed
    local childAddedConnection = w.ChildAdded:Connect(function(model)
        if model.Name == "GrabParts" then
            local part_to_impulse = model["GrabPart"]["WeldConstraint"].Part1

            if part_to_impulse then
                local humanoid = part_to_impulse.Parent:FindFirstChild("Humanoid")
                local localPlayer = players.LocalPlayer

                if humanoid and not humanoid:IsDescendantOf(localPlayer.Character) then
                    applyPoison(humanoid)
                end
            end
        end
    end)
    table.insert(PoisonAttackConnections, childAddedConnection)
end

-- Create toggles for each feature
local VoidAttackToggle = Tab:CreateToggle({
    Name = "Void Attack",
    CurrentValue = VoidAttackEnabled,
    Flag = "VoidAttackToggle",
    Callback = function(Value)
        VoidAttackEnabled = Value
        if Value then
            setupVoidAttack()
        else
            -- Disconnect all connections
            for _, connection in pairs(VoidAttackConnections) do
                connection:Disconnect()
            end
            VoidAttackConnections = {}
        end
    end,
})

local PoisonAttackToggle = Tab:CreateToggle({
    Name = "Poison Attack",
    CurrentValue = PoisonAttackEnabled,
    Flag = "PoisonAttackToggle",
    Callback = function(Value)
        PoisonAttackEnabled = Value
        if Value then
            setupPoisonAttack()
        else
            -- Disconnect all connections
            for _, connection in pairs(PoisonAttackConnections) do
                connection:Disconnect()
            end
            PoisonAttackConnections = {}
        end
    end,
})

local Tab = Window:CreateTab("Client") -- Title, Image

local Section = Tab:CreateSection("Main (Server-sided)")

-- Global Variables
getgenv().WalkSpeedEnabled = false
getgenv().WalkSpeedValue = 9999
getgenv().InfJumpEnabled = false
getgenv().JumpHeight = 50
getgenv().NoClipEnabled = false
getgenv().SpyChatEnabled = false
getgenv().FOVEnabled = false
getgenv().FOVValue = 100
getgenv().ThirdPersonEnabled = false
getgenv().InvisibleLineEnabled = false
getgenv().ClearAnimationsEnabled = false

-- Toggles
local WalkSpeedToggle = Tab:CreateToggle({
   Name = "Walk Speed",
   CurrentValue = false,
   Flag = "WalkSpeedToggle",
   Callback = function(Value)
       getgenv().WalkSpeedEnabled = Value
       local Player = game:service'Players'.LocalPlayer
       if Player.Character and Player.Character:FindFirstChild("Humanoid") then
           if Value then
               Player.Character.Humanoid.WalkSpeed = getgenv().WalkSpeedValue
               Player.Character.Humanoid:GetPropertyChangedSignal'WalkSpeed':Connect(function()
                   if getgenv().WalkSpeedEnabled then
                       Player.Character.Humanoid.WalkSpeed = getgenv().WalkSpeedValue
                   end
               end)
           else
               Player.Character.Humanoid.WalkSpeed = 16 -- Default speed
           end
       end
   end,
})

local InfJumpToggle = Tab:CreateToggle({
   Name = "Infinite Jump",
   CurrentValue = false,
   Flag = "InfJumpToggle",
   Callback = function(Value)
       getgenv().InfJumpEnabled = Value
       if Value then
           local Player = game:GetService'Players'.LocalPlayer
           local UIS = game:GetService'UserInputService'
           
           getgenv().InfJumpConnection = UIS.InputBegan:Connect(function(UserInput)
               if UserInput.UserInputType == Enum.UserInputType.Keyboard and UserInput.KeyCode == Enum.KeyCode.Space then
                   if Player.Character and Player.Character:FindFirstChild("Humanoid") then
                       local humanoid = Player.Character.Humanoid
                       if humanoid:GetState() == Enum.HumanoidStateType.Jumping or humanoid:GetState() == Enum.HumanoidStateType.Freefall then
                           if humanoid.Parent and humanoid.Parent:FindFirstChild("HumanoidRootPart") then
                               humanoid.Parent.HumanoidRootPart.Velocity = Vector3.new(0, getgenv().JumpHeight, 0)
                           end
                       end
                   end
               end
           end)
       else
           if getgenv().InfJumpConnection then
               getgenv().InfJumpConnection:Disconnect()
           end
       end
   end,
})

local NoClipToggle = Tab:CreateToggle({
    Name = "No Clip",
    CurrentValue = false,
    Flag = "NoClipToggle",
    Callback = function(Value)
        getgenv().NoClipEnabled = Value
        if Value then
            if not getgenv().NoclipLoop then
                getgenv().NoclipLoop = game:GetService('RunService').Stepped:Connect(function()
                    if getgenv().NoClipEnabled and game.Players.LocalPlayer.Character then
                        for _,v in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                            if v:IsA('BasePart') and v.CanCollide then
                                v.CanCollide = false
                            end
                        end
                    end
                end)
            end
        else
            if getgenv().NoclipLoop then
                getgenv().NoclipLoop:Disconnect()
            end
        end
    end,
})
 
local SpyChatToggle = Tab:CreateToggle({
    Name = "Spy Chat",
    CurrentValue = false,
    Flag = "SpyChatToggle",
    Callback = function(Value)
        getgenv().SpyChatEnabled = Value
        enabled = Value
        if game:GetService("StarterGui") then
            local privateProperties = {
                Color = Color3.fromRGB(0,255,255), 
                Font = Enum.Font.SourceSansBold,
                TextSize = 18,
                Text = "{SPY "..(Value and "EN" or "DIS").."ABLED}"
            }
            game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", privateProperties)
        end
    end,
})
 
local FOVToggle = Tab:CreateToggle({
    Name = "FOV Adjuster",
    CurrentValue = false,
    Flag = "FOVToggle",
    Callback = function(Value)
        getgenv().FOVEnabled = Value
        if workspace:FindFirstChild("Camera") then
            workspace.Camera.FieldOfView = Value and getgenv().FOVValue or 70
        end
    end,
})
 
local ThirdPersonToggle = Tab:CreateToggle({
    Name = "Third Person",
    CurrentValue = false,
    Flag = "ThirdPersonToggle",
    Callback = function(Value)
        getgenv().ThirdPersonEnabled = Value
        local LocalPlayer = game:GetService'Players'.LocalPlayer
        if Value then
            LocalPlayer.CameraMode = Enum.CameraMode.Classic
            LocalPlayer.CameraMaxZoomDistance = 10
            LocalPlayer.CameraMinZoomDistance = 10
        else
            LocalPlayer.CameraMode = Enum.CameraMode.LockFirstPerson
            LocalPlayer.CameraMaxZoomDistance = 0
            LocalPlayer.CameraMinZoomDistance = 0
        end
    end,
})
 
local InvisibleLineToggle = Tab:CreateToggle({
    Name = "Invisible Line",
    CurrentValue = false,
    Flag = "InvisibleLineToggle",
    Callback = function(Value)
        getgenv().InvisibleLineEnabled = Value
        local ReplicatedStorage = game:GetService("ReplicatedStorage")
        local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
        
        if Value then
            if GrabEvents then
                local function removeCreateGrabLine()
                    local target = GrabEvents:FindFirstChild("CreateGrabLine")
                    if target then target:Destroy() end
                end
                
                removeCreateGrabLine()
                getgenv().GrabLineConnection = GrabEvents.ChildAdded:Connect(function(child)
                    if child.Name == "CreateGrabLine" then
                        child:Destroy()
                    end
                end)
            end
        else
            if getgenv().GrabLineConnection then
                getgenv().GrabLineConnection:Disconnect()
            end
        end
    end,
})
 
local ClearAnimationsToggle = Tab:CreateToggle({
    Name = "Clear Animations",
    CurrentValue = false,
    Flag = "ClearAnimationsToggle",
    Callback = function(Value)
        getgenv().ClearAnimationsEnabled = Value
        if Value then
            if not getgenv().ClearAnimationsLoop then
                getgenv().ClearAnimationsLoop = task.spawn(function()
                    while getgenv().ClearAnimationsEnabled do
                        local ReplicatedStorage = game:GetService("ReplicatedStorage")
                        local Workspace = game:GetService("Workspace")
                        
                        local function deleteIfExists(instance)
                            if instance then instance:Destroy() end
                        end
                        
                        deleteIfExists(ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("Look"))
                        deleteIfExists(ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("CreateGrabLine"))
                        deleteIfExists(Workspace:FindFirstChild("oajkfodsjjgfsagag") and Workspace.oajkfodsjjgfsagag:FindFirstChild("Animate"))
                        task.wait(0.5)
                    end
                end)
            end
        else
            if getgenv().ClearAnimationsLoop then
                task.cancel(getgenv().ClearAnimationsLoop)
            end
        end
    end,
})

local Section = Tab:CreateSection("Coin slot")

getgenv().AutoTeleportEnabled = false

local AutoTeleportToggle = Tab:CreateToggle({
    Name = "Auto Cash",
    CurrentValue = false,
    Flag = "AutoTeleportToggle",
    Callback = function(Value)
        getgenv().AutoTeleportEnabled = Value
        if Value then
            if not getgenv().AutoTeleportLoop then
                getgenv().AutoTeleportLoop = task.spawn(function()
                    local player = game.Players.LocalPlayer
                    local character = player.Character or player.CharacterAdded:Wait()
                    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
                    local targetPosition = Vector3.new(53.934, 0.351, -115.221)
                    
                    while getgenv().AutoTeleportEnabled do
                        local originalPosition = humanoidRootPart.CFrame
                        
                        for i = 1, 900 do
                            if not getgenv().AutoTeleportEnabled then break end
                            task.wait(1)
                        end
                        
                        if getgenv().AutoTeleportEnabled then
                            humanoidRootPart.CFrame = CFrame.new(targetPosition)
                            for i = 1, 5 do
                                if not getgenv().AutoTeleportEnabled then break end
                                task.wait(1)
                            end
                            if getgenv().AutoTeleportEnabled then
                                humanoidRootPart.CFrame = originalPosition
                            end
                        end
                    end
                end)
            end
        else
            if getgenv().AutoTeleportLoop then
                task.cancel(getgenv().AutoTeleportLoop)
            end
        end
    end,
})

local Section = Tab:CreateSection("Settings")

-- Sliders
local FOVSlider = Tab:CreateSlider({
    Name = "FOV",
    Range = {70, 120},
    Increment = 1,
    Suffix = "",
    CurrentValue = 100,
    Flag = "FOVSlider",
    Callback = function(Value)
        getgenv().FOVValue = Value
        if getgenv().FOVEnabled and workspace:FindFirstChild("Camera") then
            workspace.Camera.FieldOfView = Value
        end
    end,
})
 
local SpeedSlider = Tab:CreateSlider({
    Name = "Walk Speed",
    Range = {16, 9999},
    Increment = 10,
    Suffix = "",
    CurrentValue = 9999,
    Flag = "SpeedSlider",
    Callback = function(Value)
        getgenv().WalkSpeedValue = Value
        if getgenv().WalkSpeedEnabled then
            local Player = game:service'Players'.LocalPlayer
            if Player.Character and Player.Character:FindFirstChild("Humanoid") then
                Player.Character.Humanoid.WalkSpeed = Value
            end
        end
    end,
})
 
local JumpSlider = Tab:CreateSlider({
    Name = "Jump Height",
    Range = {0, 500},
    Increment = 5,
    Suffix = "",
    CurrentValue = 50,
    Flag = "JumpSlider",
    Callback = function(Value)
        getgenv().JumpHeight = Value
    end,
})

local Tab = Window:CreateTab("Teleports") 

local Section = Tab:CreateSection("Main")

-- Initialize player references
local player = game:GetService("Players").LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- Teleport Player Dropdown and Toggle
local playerDropdown = Tab:CreateDropdown({
    Name = "TP Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(Option)
        -- Store selected player for the teleport button
        getgenv().SelectedPlayer = Option[1]
    end
})

-- Refresh player list function
local function refreshPlayerList()
    local players = {}
    for _, p in ipairs(game:GetService("Players"):GetPlayers()) do
        if p ~= player then
            table.insert(players, p.Name)
        end
    end
    playerDropdown:Refresh(players, false)
end

-- Initial refresh and set up player added/removed connections
refreshPlayerList()
game:GetService("Players").PlayerAdded:Connect(refreshPlayerList)
game:GetService("Players").PlayerRemoving:Connect(refreshPlayerList)

-- Teleport Player Button
Tab:CreateButton({
    Name = "Teleport to Selected Player",
    Callback = function()
        if getgenv().SelectedPlayer then
            local targetPlayer = game:GetService("Players"):FindFirstChild(getgenv().SelectedPlayer)
            if targetPlayer and targetPlayer.Character then
                local targetHRP = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                if targetHRP and humanoidRootPart then
                    humanoidRootPart.CFrame = targetHRP.CFrame
                end
            end
        end
    end
})

local Section = Tab:CreateSection("Only use when being looped")
-- Anti Loop Toggle
Tab:CreateToggle({
    Name = "Anti Loop",
    CurrentValue = false,
    Flag = "AntiLoopToggle",
    Callback = function(Value)
        if Value then
            -- Fixed Coordinates (Optimized for Aggressive Looping)
            getgenv().teleportLocations = {
                Vector3.new(518.52, 834.62, -362.38),
                Vector3.new(566.16, 124.33, -93.13),
                Vector3.new(80.63, 346.19, 338.40),
                Vector3.new(288.70, 447.54, 481.17),
                Vector3.new(-331.23, 81.64, 340.15),
                Vector3.new(-566.02, -6.35, 84.20),
                Vector3.new(-514.05, -6.35, -160.63),
                Vector3.new(-215.14, 60.76, -304.82),
                Vector3.new(-338.28, 22.11, 484.03)
            }
            
            -- Function that keeps the loop running forever
            getgenv().ultraFastLoopTP = function()
                while getgenv().AntiLoopEnabled do
                    local char = player.Character or player.CharacterAdded:Wait()
                    local hrp = char:WaitForChild("HumanoidRootPart")
                    
                    for _, location in ipairs(getgenv().teleportLocations) do
                        if hrp and getgenv().AntiLoopEnabled then
                            hrp.CFrame = CFrame.new(location)
                        end
                        task.wait(0.1)
                    end
                end
            end
            
            getgenv().AntiLoopEnabled = true
            task.spawn(getgenv().ultraFastLoopTP)
            
            -- Always restart loop after dying/resetting
            player.CharacterAdded:Connect(function()
                task.wait(0.1)
                if getgenv().AntiLoopEnabled then
                    task.spawn(getgenv().ultraFastLoopTP)
                end
            end)
        else
            getgenv().AntiLoopEnabled = false
        end
    end
})

local Section = Tab:CreateSection("Waypoints")
-- Teleport Buttons
local teleportButtons = {
    {Name = "Blue House", Position = Vector3.new(499.349396, 83.3367767, -348.046204)},
    {Name = "Japanese House", Position = Vector3.new(552.457214, 124.338623, -91.4771805)},
    {Name = "Spooky House", Position = Vector3.new(282.208405, -4.47540379, 469.873444)},
    {Name = "Poison Hole", Position = Vector3.new(117.453735, -5.39313602, 288.412567)},
    {Name = "Tudor House", Position = Vector3.new(-556.870178, -6.35040379, 83.4246979)},
    {Name = "Train Cave", Position = Vector3.new(519.345032, 13.647583, -373.584656)},
    {Name = "Barn", Position = Vector3.new(-221.904755, 60.7637939, -310.083466)},
    {Name = "Small Cave", Position = Vector3.new(-38.3516655, -7.35040283, -292.126373)},
    {Name = "Pink Roof House", Position = Vector3.new(-514.025146, -8.25040436, -159.503799)},
    {Name = "Green House", Position = Vector3.new(-338.432281, 81.6490479, 347.547516)},
    {Name = "Mountain Cave", Position = Vector3.new(-69.426, -7.350, 558.632)},
    {Name = "Spawn Point", Position = Vector3.new(5.708, -7.350, 7.902)},
    {Name = "Factory", Position = Vector3.new(149.050, 389.982, 332.567)}
}

for _, buttonInfo in ipairs(teleportButtons) do
    Tab:CreateButton({
        Name = buttonInfo.Name,
        Callback = function()
            if humanoidRootPart then
                humanoidRootPart.CFrame = CFrame.new(buttonInfo.Position)
            end
        end
    })
end

local Tab = Window:CreateTab("Map")

local Section = Tab:CreateSection("Main visuals")
-- Global variables
_G.ESPEnabled = false
_G.VisibleNamesEnabled = false
_G.HighlightESPEnabled = false
_G.RemoveFogEnabled = false
_G.RemoveShadowsEnabled = false
_G.TimeOfDayEnabled = false
_G.CurrentTime = "18:00"

-- Store references for cleanup
_G.ESPConnections = {}
_G.VisibleNamesDrawings = {}
_G.HighlightESPInstances = {}


-- Visible Names Toggle
local VisibleNamesToggle = Tab:CreateToggle({
   Name = "Visible Names",
   CurrentValue = _G.VisibleNamesEnabled,
   Flag = "VisibleNames",
   Callback = function(Value)
      _G.VisibleNamesEnabled = Value
      if Value then
         local c = workspace.CurrentCamera
         local ps = game:GetService("Players")
         local lp = ps.LocalPlayer
         local rs = game:GetService("RunService")

         local function esp(p,cr)
            local h = cr:WaitForChild("Humanoid")
            local hrp = cr:WaitForChild("Head")

            local text = Drawing.new("Text")
            text.Visible = false
            text.Center = true
            text.Outline = false 
            text.Font = 3
            text.Size = 16.16
            text.Color = Color3.new(170,170,170)

            -- Store drawing for this player
            _G.VisibleNamesDrawings[p.Name] = text

            local conection
            local conection2
            local conection3

            local function dc()
               text.Visible = false
               text:Remove()
               if conection then conection:Disconnect() end
               if conection2 then conection2:Disconnect() end
               if conection3 then conection3:Disconnect() end
            end

            conection2 = cr.AncestryChanged:Connect(function(_,parent)
               if not parent then dc() end
            end)

            conection3 = h.HealthChanged:Connect(function(v)
               if (v<=0) or (h:GetState() == Enum.HumanoidStateType.Dead) then dc() end
            end)

            conection = rs.RenderStepped:Connect(function()
               if not _G.VisibleNamesEnabled then dc() return end
               local hrp_pos,hrp_onscreen = c:WorldToViewportPoint(hrp.Position)
               if hrp_onscreen then
                  text.Position = Vector2.new(hrp_pos.X, hrp_pos.Y - 27)
                  text.Text = "[ "..p.Name.." ]"
                  text.Visible = true
               else
                  text.Visible = false
               end
               wait(0.073)
            end)
         end

         local function p_added(p)
            if p.Character then esp(p,p.Character) end
            p.CharacterAdded:Connect(function(cr) esp(p,cr) end)
         end

         for i,p in next, ps:GetPlayers() do 
            if p ~= lp then p_added(p) end
         end

         local playerAddedConn = ps.PlayerAdded:Connect(p_added)
         _G.VisibleNamesDrawings["PlayerAdded"] = playerAddedConn
      else
         -- Clean up Visible Names
         for _, drawing in pairs(_G.VisibleNamesDrawings) do
            if typeof(drawing) == "Instance" and drawing:IsA("Text") then
               pcall(function() drawing:Remove() end)
            elseif typeof(drawing) == "RBXScriptConnection" then
               drawing:Disconnect()
            end
         end
         _G.VisibleNamesDrawings = {}
      end
   end,
})

-- Highlight ESP Toggle
local HighlightESPToggle = Tab:CreateToggle({
   Name = "Highlight ESP",
   CurrentValue = _G.HighlightESPEnabled,
   Flag = "HighlightESP",
   Callback = function(Value)
      _G.HighlightESPEnabled = Value
      if Value then
         spawn(function()
            while _G.HighlightESPEnabled and wait(1) do
               local players = game.Players:GetPlayers()
               for i,v in pairs(players) do
                  if v.Character then
                     local highlight = Instance.new("Highlight")
                     highlight.Name = v.Name
                     highlight.FillTransparency = 0.5
                     highlight.FillColor = Color3.new(0, 0, 0)
                     highlight.OutlineColor = Color3.new(255, 255, 255)
                     highlight.OutlineTransparency = 0
                     highlight.Parent = v.Character
                     _G.HighlightESPInstances[v.Name] = highlight
                  end
               end
            end
         end)
      else
         -- Clean up Highlight ESP
         for _, highlight in pairs(_G.HighlightESPInstances) do
            pcall(function() highlight:Destroy() end)
         end
         _G.HighlightESPInstances = {}
      end
   end,
})

-- Remove Fog Toggle
local RemoveFogToggle = Tab:CreateToggle({
   Name = "Remove Fog",
   CurrentValue = _G.RemoveFogEnabled,
   Flag = "RemoveFog",
   Callback = function(Value)
      _G.RemoveFogEnabled = Value
      local Lighting = game:GetService("Lighting")
      if Value then
         _G.OriginalFogEnd = Lighting.FogEnd
         _G.OriginalFogStart = Lighting.FogStart
         Lighting.FogEnd = 1000000
         Lighting.FogStart = 1000000
         if Lighting:FindFirstChildOfClass("Atmosphere") then
            _G.OriginalAtmosphere = Lighting:FindFirstChildOfClass("Atmosphere"):Clone()
            Lighting:FindFirstChildOfClass("Atmosphere"):Destroy()
         end
      else
         Lighting.FogEnd = _G.OriginalFogEnd or 10000
         Lighting.FogStart = _G.OriginalFogStart or 0
         if _G.OriginalAtmosphere then
            _G.OriginalAtmosphere.Parent = Lighting
         end
      end
   end,
})

-- Remove Shadows Toggle
local RemoveShadowsToggle = Tab:CreateToggle({
   Name = "Remove Shadows",
   CurrentValue = _G.RemoveShadowsEnabled,
   Flag = "RemoveShadows",
   Callback = function(Value)
      _G.RemoveShadowsEnabled = Value
      local Lighting = game:GetService("Lighting")
      if Value then
         _G.OriginalGlobalShadows = Lighting.GlobalShadows
         _G.OriginalAmbient = Lighting.Ambient
         _G.OriginalOutdoorAmbient = Lighting.OutdoorAmbient
         
         Lighting.GlobalShadows = false
         Lighting.Ambient = Color3.new(1, 1, 1)
         Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
      else
         Lighting.GlobalShadows = _G.OriginalGlobalShadows or true
         Lighting.Ambient = _G.OriginalAmbient or Color3.new(0.5, 0.5, 0.5)
         Lighting.OutdoorAmbient = _G.OriginalOutdoorAmbient or Color3.new(0.5, 0.5, 0.5)
      end
   end,
})


local Section = Tab:CreateSection("Time of Day")

-- Time Of Day Toggle and Slider
local TimeOfDayToggle = Tab:CreateToggle({
    Name = "Time Of Day",
    CurrentValue = _G.TimeOfDayEnabled,
    Flag = "TimeOfDayToggle",
    Callback = function(Value)
       _G.TimeOfDayEnabled = Value
       local Lighting = game:GetService("Lighting")
       if Value then
          _G.OriginalTimeOfDay = Lighting.TimeOfDay
          Lighting.TimeOfDay = _G.CurrentTime
       else
          Lighting.TimeOfDay = _G.OriginalTimeOfDay or "14:00:00"
       end
    end,
 })
 
 local TimeOfDaySlider = Tab:CreateSlider({
    Name = "Time Of Day (HH:MM)",
    Range = {0, 23},
    Increment = 1,
    Suffix = ":00",
    CurrentValue = tonumber(string.sub(_G.CurrentTime, 1, 2)),
    Flag = "TimeOfDaySlider",
    Callback = function(Value)
       _G.CurrentTime = string.format("%02d:00", Value)
       if _G.TimeOfDayEnabled then
          local Lighting = game:GetService("Lighting")
          Lighting.TimeOfDay = _G.CurrentTime
       end
    end,
 })


local Tab = Window:CreateTab("Creatue")

local Section = Tab:CreateSection("Anti blob loop (Only use when being looped)")

local Button = Tab:CreateButton({
    Name = "Anti blob loop",
    Callback = function()
        _G.masslesscoroutine1 = nil
        _G.masslesscoroutine2 = nil
        
        local players = game:GetService("Players")
        local workspace = game:GetService("Workspace")
        local runservice = game:GetService("RunService")
        
        function setmasslesstofalse(instance)
            if instance:IsA("BasePart") and instance.Massless then
                pcall(function() instance.Massless = false end)
            end
        end
        
        function AntiBlobLoop1()
            if not _G.masslesscoroutine1 then
                _G.masslesscoroutine1 = coroutine.create(function()
                    while true do
                        local playername = players.LocalPlayer.Name
                        local character = workspace:FindFirstChild(playername)
                        if character then
                            for _, descendant in ipairs(character:GetDescendants()) do
                                setmasslesstofalse(descendant)
                            end
                            local descendantaddedconnection
                            descendantaddedconnection = character.DescendantAdded:Connect(function(newdescendant)
                                setmasslesstofalse(newdescendant)
                                descendantaddedconnection:Disconnect()
                                descendantaddedconnection = nil
                            end)
                        end
                        task.wait(0.25)
                    end
                end)
                coroutine.resume(_G.masslesscoroutine1)
            end
        end
        
        function AntiBlobLoop2()
            if not _G.masslesscoroutine2 then
                _G.masslesscoroutine2 = runservice.Heartbeat:Connect(function()
                    local playername = players.LocalPlayer.Name
                    local character = workspace.PlotItems.PlayersInPlots:FindFirstChild(playername)
                    if character then
                        for _, descendant in ipairs(character:GetDescendants()) do
                            setmasslesstofalse(descendant)
                        end
                        local descendantaddedconnection
                        descendantaddedconnection = character.DescendantAdded:Connect(function(newdescendant)
                            setmasslesstofalse(newdescendant)
                            descendantaddedconnection:Disconnect()
                            descendantaddedconnection = nil
                        end)
                    end
                    task.wait(1)
                end)
            end
        end
        
        AntiBlobLoop1()
        AntiBlobLoop2()
        
    end,
 })

local Section = Tab:CreateSection("Bring player")

-- Global variables
_G.target = "PlayerName" -- Replace with the player's name you want to target
_G.Tab = Tab -- Assuming Tab is already defined in your environment

-- Create dropdown for player selection
local playerDropdown = _G.Tab:CreateDropdown({
    Name = "Select Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(Options)
        _G.target = Options[1] -- Update the target when selection changes
    end,
})

-- Update dropdown with current players
local function updatePlayerDropdown()
    local players = game.Players:GetPlayers()
    local playerNames = {}
    for _, player in ipairs(players) do
        table.insert(playerNames, player.Name)
    end
    playerDropdown:Refresh(playerNames, false)
end

-- Initial update and set up listener for new players
updatePlayerDropdown()
game.Players.PlayerAdded:Connect(updatePlayerDropdown)
game.Players.PlayerRemoving:Connect(updatePlayerDropdown)

-- Create button to execute the script
local Button = _G.Tab:CreateButton({
    Name = "Bring",
    Callback = function()
        -- The original script exactly as is, but using _G.target
        local function findPlayerByPartialName(target)
            local players = game.Players:GetPlayers()
            local foundPlayers = {}
            local highestPriority = -math.huge
            local selectedPlayers = {}

            for i, player in ipairs(players) do
                local lowerTarget = string.lower(target)
                local lowerUsername = string.lower(player.Name)
                local lowerDisplayName = string.lower(player.DisplayName)

                local displayNameMatch = string.find(lowerDisplayName, lowerTarget, 1, true)
                local usernameMatch = string.find(lowerUsername, lowerTarget, 1, true)

                if displayNameMatch and usernameMatch then
                    if displayNameMatch < usernameMatch then
                        if usernameMatch > highestPriority then
                            selectedPlayers = {player}
                            highestPriority = usernameMatch
                        elseif usernameMatch == highestPriority then
                            table.insert(selectedPlayers, player)
                        end
                    else
                        if displayNameMatch > highestPriority then
                            selectedPlayers = {player}
                            highestPriority = displayNameMatch
                        elseif displayNameMatch == highestPriority then
                            table.insert(selectedPlayers, player)
                        end
                    end
                elseif displayNameMatch then
                    if displayNameMatch > highestPriority then
                        selectedPlayers = {player}
                        highestPriority = displayNameMatch
                    elseif displayNameMatch == highestPriority then
                        table.insert(selectedPlayers, player)
                    end
                elseif usernameMatch then
                    if usernameMatch > highestPriority then
                        selectedPlayers = {player}
                        highestPriority = usernameMatch
                    elseif usernameMatch == highestPriority then
                        table.insert(selectedPlayers, player)
                    end
                end
            end

            if #selectedPlayers > 0 then
                local randomIndex = math.random(1, #selectedPlayers)
                table.insert(foundPlayers, selectedPlayers[randomIndex])
            end

            return foundPlayers
        end

        local matchingPlayers = findPlayerByPartialName(_G.target)
        for i, player in ipairs(matchingPlayers) do
            print(player.Name)
        end

        local player = game.Players.LocalPlayer
        local character = player.Character
        local username = player.Name
        local spawnToys = game.Workspace:WaitForChild(username .. "SpawnedInToys")

        -- Check if the character exists
        if character then
            local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

            -- Retrieve the CFrame of the HumanoidRootPart
            local cframe = humanoidRootPart.CFrame
            print("CFrame:", cframe)

            -- Retrieve the Vector3 position of the HumanoidRootPart
            local position = humanoidRootPart.Position
            print("Position:", position)

            -- This was generated from engospy RemoteSpy tool.
            local creatureExists

            -- Check if there is at least one CreatureBlobman object in spawnToys
            local creatureBlobman = spawnToys:FindFirstChild("CreatureBlobman")

            -- Update creatureExists based on the condition
            creatureExists = creatureBlobman ~= nil

            -- Loop until there are no more CreatureBlobman objects
            while creatureBlobman do
                -- Destroy the CreatureBlobman toy
                game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(creatureBlobman)

                -- Wait for a moment before checking again
                wait(0.1)

                -- Check if there is another CreatureBlobman object in spawnToys
                creatureBlobman = spawnToys:FindFirstChild("CreatureBlobman")
            end

            -- Print a message based on the value of creatureExists
            if creatureExists then
                print("At least one CreatureBlobman found in spawnToys!")
            else
                print("No CreatureBlobman found in spawnToys!")
            end

            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(table.unpack({
                [1] = "CreatureBlobman",
                [2] = cframe,
                [3] = position,
            }))

            local function fireProximityPrompts(promptName)
                if fireproximityprompt then
                    if promptName then
                        for _, descendant in ipairs(workspace:GetDescendants()) do
                            if descendant:IsA("ProximityPrompt") and descendant.Name == promptName then
                                fireproximityprompt(descendant)
                            end
                        end
                    else
                        for _, descendant in ipairs(workspace:GetDescendants()) do
                            if descendant:IsA("ProximityPrompt") then
                                fireproximityprompt(descendant)
                            end
                        end
                    end
                else
                    print("Incompatible Exploit: Your exploit does not support the fireproximityprompt function.")
                end
            end

            if spawnToys then
                local creatureBlobman = spawnToys:WaitForChild("CreatureBlobman")
                local vehicleSeat = creatureBlobman:FindFirstChildOfClass("VehicleSeat")
                if vehicleSeat then
                    local proximityPrompt = vehicleSeat:FindFirstChildOfClass("ProximityPrompt")
                    if proximityPrompt then
                        fireProximityPrompts(proximityPrompt.Name)
                    end
                end
            end

            local Players = game:GetService("Players")
            local player = Players.LocalPlayer
            local character = player.Character

            -- Check if the character exists
            if character then
                local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

                -- Retrieve the current position of the HumanoidRootPart
                local currentPosition = humanoidRootPart.Position

                -- Calculate the new position 1 stud above the current position
                local newPosition = currentPosition + Vector3.new(0, 5, 0)

                -- Teleport the character to the new position
                character:SetPrimaryPartCFrame(CFrame.new(newPosition))

                print("Player teleported 1 stud above their previous position!")
            else
                print("Character not found!")
            end

            character.Torso.Anchored = true
            character.Head.Anchored = true
            character["Left Arm"].Anchored = true
            character["Right Arm"].Anchored = true
            character["Left Leg"].Anchored = true
            character["Right Leg"].Anchored = true

            wait(0.1)
            
            -- Check if 'cute' object exists
            local function findObjectInWorkspace(workspace, objectName)
                for _, object in ipairs(workspace:GetDescendants()) do
                    if object.Name == objectName then
                        return object
                    end
                end
            end

            local tar = findObjectInWorkspace(workspace, _G.target)
            if tar then
                -- Object found, perform actions with it
                print("tar object found:", tar.Name)

                spawnToys.CreatureBlobman.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(table.unpack({ 
                    [1] = spawnToys.CreatureBlobman.LeftDetector,
                    [2] = tar.HumanoidRootPart,
                    [3] = spawnToys.CreatureBlobman.LeftDetector.LeftWeld,
                }))
            else
                print("tar object not found in Workspace!")
                character.Torso.Anchored = false
                character.Head.Anchored = false
                character["Left Arm"].Anchored = false
                character["Right Arm"].Anchored = false
                character["Left Leg"].Anchored = false
                character["Right Leg"].Anchored = false
            end
            wait(0.6)
            -- This was generated from engospy RemoteSpy tool.
            spawnToys.CreatureBlobman.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(table.unpack({
                [1] = spawnToys.CreatureBlobman.LeftDetector.LeftWeld,
                [2] = tar.HumanoidRootPart,
            }))

            character.Torso.Anchored = false
            character.Head.Anchored = false
            character["Left Arm"].Anchored = false
            character["Right Arm"].Anchored = false
            character["Left Leg"].Anchored = false
            character["Right Leg"].Anchored = false

            if player.Character then
                -- Get the humanoid of the character
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")

                -- Check if the humanoid exists
                if humanoid then
                    -- Make the player jump
                    humanoid.Jump = true
                end
            end

            wait(0.5)

            local creatureBlobman = spawnToys:FindFirstChild("CreatureBlobman")

            -- Update creatureExists based on the condition
            creatureExists = creatureBlobman ~= nil

            -- Loop until there are no more CreatureBlobman objects
            while creatureBlobman do
                -- Destroy the CreatureBlobman toy
                game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(creatureBlobman)

                -- Wait for a moment before checking again
                wait(0.1)

                -- Check if there is another CreatureBlobman object in spawnToys
                creatureBlobman = spawnToys:FindFirstChild("CreatureBlobman")
            end

            -- Print a message based on the value of creatureExists
            if creatureExists then
                print("At least one CreatureBlobman found in spawnToys!")
            else
                print("No CreatureBlobman found in spawnToys!")
            end

            print("Player tped")
        else
            print("Character not found!")
        end

        character.Torso.Anchored = false
        character.Head.Anchored = false
        character["Left Arm"].Anchored = false
        character["Right Arm"].Anchored = false
        character["Left Leg"].Anchored = false
        character["Right Leg"].Anchored = false
    end,
})

local Section = Tab:CreateSection("Kick all (Sit on blob-man first)")

local BlobmanGrabber = {
    running = true,
    whitelistedPlayers = {},  -- Add player names here to exclude them

    getSpawnerPlayer = function(blobman)
        local players = game:GetService("Players")
        for _, player in pairs(players:GetPlayers()) do
            local spawns = workspace:FindFirstChild(player.Name.."SpawnedInToys")
            if spawns and spawns:FindFirstChild("CreatureBlobman") then
                return player
            end
        end
        return nil
    end,

    startLoop = function(self)
        local players = game:GetService("Players")
        while self.running do
            for _, player in pairs(players:GetPlayers()) do
                if not table.find(self.whitelistedPlayers, player.Name) then 
                    local character = player.Character
                    if character and character:FindFirstChild("HumanoidRootPart") then
                        local spawnerPlayer = self:getSpawnerPlayer(workspace:FindFirstChild(player.Name.."SpawnedInToys") and workspace:FindFirstChild(player.Name.."SpawnedInToys"):FindFirstChild("CreatureBlobman"))

                        if spawnerPlayer then
                            local spawnerPath = workspace[spawnerPlayer.Name.."SpawnedInToys"].CreatureBlobman
                            local args = {
                                [1] = spawnerPath.LeftDetector,
                                [2] = character.HumanoidRootPart,
                                [3] = spawnerPath.LeftDetector.LeftWeld
                            }

                            -- Fire multiple times in rapid succession
                            for i = 1, 8 do  -- 8 rapid-fire attempts per player
                                spawnerPath.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
                                if i % 3 == 0 then  -- Small delay every 3 fires
                                    wait(0.001)
                                end
                            end
                        end
                    end
                end
            end
            wait(0.005)  -- Reduced delay from 0.010 to 0.005 for faster scanning
        end
    end,

    start = function(self)
        -- Start multiple parallel loops for maximum effectiveness
        for i = 1, 3 do  -- Triple the attack power with 3 parallel loops
            coroutine.wrap(function()
                self:startLoop()
            end)()
        end
    end,

    stop = function(self)
        self.running = false
    end
}

local Toggle = Tab:CreateToggle({
    Name = "Kick all",
    CurrentValue = false,
    Flag = "BlobmanGrabberToggle",
    Callback = function(Value)
        if Value then
            BlobmanGrabber.running = true
            BlobmanGrabber:start()
        else
            BlobmanGrabber:stop()
        end
    end,
})

local Section = Tab:CreateSection("Kick player")

local Players = game:GetService("Players")
local running = false
local targetPlayerName = ""

-- Get all players for dropdown
local playerOptions = {}
for _, player in ipairs(Players:GetPlayers()) do
    table.insert(playerOptions, player.Name)
end

-- Create the UI elements
local Dropdown = Tab:CreateDropdown({
    Name = "Select Target",
    Options = playerOptions,
    CurrentOption = "",
    MultipleOptions = false,
    Flag = "TargetDropdown",
    Callback = function(selected)
        targetPlayerName = selected[1]
    end,
})

local Toggle = Tab:CreateToggle({
    Name = "Kick player (Sit on blob first)",
    CurrentValue = false,
    Flag = "GrabToggle",
    Callback = function(state)
        running = state
    end,
})

-- Your EXACT grab script (unchanged)
local function getSpawnerPlayer(blobman)
    for _, player in pairs(Players:GetPlayers()) do
        local spawns = workspace:FindFirstChild(player.Name.."SpawnedInToys")
        if spawns and spawns:FindFirstChild("CreatureBlobman") then
            return player
        end
    end
    return nil
end

-- Modified to respect the toggle state
local function startLoop()
    while running do
        if targetPlayerName ~= "" then
            local targetPlayer = Players:FindFirstChild(targetPlayerName)
            
            if targetPlayer then
                local character = targetPlayer.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local spawnerPlayer = getSpawnerPlayer(workspace:FindFirstChild(targetPlayer.Name.."SpawnedInToys") and workspace:FindFirstChild(targetPlayer.Name.."SpawnedInToys"):FindFirstChild("CreatureBlobman"))

                    if spawnerPlayer then
                        local spawnerPath = workspace[spawnerPlayer.Name.."SpawnedInToys"].CreatureBlobman
                        local args = {
                            [1] = spawnerPath.LeftDetector,
                            [2] = character.HumanoidRootPart,
                            [3] = spawnerPath.LeftDetector.LeftWeld
                        }

                        for i = 1, 8 do
                            spawnerPath.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
                            if i % 3 == 0 then
                                task.wait(0.001)
                            end
                        end
                    end
                end
            end
        end
        task.wait(0.005)
    end
end

-- Start loops when toggle is enabled
Toggle.Callback = function(state)
    running = state
    if state then
        for i = 1, 2 do
            coroutine.wrap(startLoop)()
        end
    end
end

-- Update player list dynamically
Players.PlayerAdded:Connect(function(player)
    table.insert(playerOptions, player.Name)
    Dropdown:Refresh(playerOptions, true)
end)

Players.PlayerRemoved:Connect(function(player)
    for i, name in ipairs(playerOptions) do
        if name == player.Name then
            table.remove(playerOptions, i)
            break
        end
    end
    Dropdown:Refresh(playerOptions, true)
end)