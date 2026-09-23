if game.PlaceId == 6961824067 then
    print("à¸¢à¸´à¸à¸à¸µà¸à¹à¸­à¸à¸£à¸±à¸à¹à¸à¹à¸²à¸ªà¸¹à¹ Jujutsu Shenanigans!")
end

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Moon Hub I FTAP",
    Icon = 0, 
    LoadingTitle = "load window wait...",
    LoadingSubtitle = "by oceancrimsno",
    
    Theme = {
        TextFont = "Default",
        TextColor = Color3.fromRGB(245, 240, 240),

        Background = Color3.fromRGB(15, 12, 12),      
        Topbar = Color3.fromRGB(25, 18, 18),        
        Shadow = Color3.fromRGB(5, 3, 3),           

        NotificationBackground = Color3.fromRGB(22, 15, 15),
        NotificationActionsBackground = Color3.fromRGB(240, 200, 200),

        TabBackground = Color3.fromRGB(35, 20, 20),
        TabStroke = Color3.fromRGB(60, 30, 30),
        TabBackgroundSelected = Color3.fromRGB(200, 60, 30), 
        TabTextColor = Color3.fromRGB(200, 170, 170),
        SelectedTabTextColor = Color3.fromRGB(255, 255, 255),

        ElementBackground = Color3.fromRGB(25, 18, 18),
        ElementBackgroundHover = Color3.fromRGB(35, 24, 24),
        SecondaryElementBackground = Color3.fromRGB(15, 12, 12),
        ElementStroke = Color3.fromRGB(50, 25, 25),
        SecondaryElementStroke = Color3.fromRGB(40, 20, 20),

        SliderBackground = Color3.fromRGB(220, 80, 30),    
        SliderProgress = Color3.fromRGB(240, 100, 30),
        SliderStroke = Color3.fromRGB(255, 140, 60),

        ToggleBackground = Color3.fromRGB(22, 15, 15),
        ToggleEnabled = Color3.fromRGB(220, 70, 30),       
        ToggleDisabled = Color3.fromRGB(80, 60, 60),
        ToggleEnabledStroke = Color3.fromRGB(255, 120, 50),
        ToggleDisabledStroke = Color3.fromRGB(50, 40, 40),
        ToggleEnabledOuterStroke = Color3.fromRGB(140, 50, 20),
        ToggleDisabledOuterStroke = Color3.fromRGB(40, 30, 30),

        DropdownSelected = Color3.fromRGB(35, 20, 20),
        DropdownUnselected = Color3.fromRGB(22, 15, 15),

        InputBackground = Color3.fromRGB(22, 15, 15),
        InputStroke = Color3.fromRGB(60, 30, 30),
        PlaceholderColor = Color3.fromRGB(160, 130, 130)
    },

    ToggleUIKeybind = "K", 
    DisableRayfieldPrompts = false,
    DisableBuildWarnings = false, 

    ConfigurationSaving = {
        Enabled = true,
        FolderName = nil, 
        FileName = "MoonHubConfig"
    },

    Discord = {
        Enabled = false, 
        Invite = "noinvitelink", 
        RememberJoins = true 
    },

    KeySystem = false, 
    KeySettings = {
        Title = "Untitled",
        Subtitle = "Key System",
        Note = "No method of obtaining the key is provided", 
        FileName = "Key", 
        SaveKey = true, 
        GrabKeyFromSite = false, 
        Key = {"Hello"} 
    }
})

-- à¸à¸£à¸´à¸à¸²à¸£à¸«à¸¥à¸±à¸à¹à¸¥à¸°à¸à¸±à¸§à¹à¸à¸£
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local Clipboard = setclipboard or toclipboard
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ==================== PLAYERS TAB ====================
local PlayersTab = Window:CreateTab("Players", "users")
local PlayersSection = PlayersTab:CreateSection("Server Players List")

local selectedPlayerName = ""
local selectedPlayerIdText = nil
local playerDropdown = nil
local playerNamesTable = {}

local function UpdatePlayerList()
    playerNamesTable = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(playerNamesTable, player.Name)
        end
    end
    if #playerNamesTable == 0 then
        table.insert(playerNamesTable, "No Other Players")
    end
end

UpdatePlayerList()

Players.PlayerAdded:Connect(function(player)
    UpdatePlayerList()
    if playerDropdown then
        playerDropdown:Refresh(playerNamesTable, true)
    end
end)

Players.PlayerRemoving:Connect(function(player)
    UpdatePlayerList()
    if playerDropdown then
        playerDropdown:Refresh(playerNamesTable, true)
    end
end)

playerDropdown = PlayersTab:CreateDropdown({
    Name = "Select Player",
    Options = playerNamesTable,
    CurrentOption = playerNamesTable[1] or "",
    Flag = "Player_Dropdown",
    Callback = function(Option)
        if type(Option) == "table" then
            selectedPlayerName = Option[1] or ""
        else
            selectedPlayerName = tostring(Option)
        end

        local targetPlayer = Players:FindFirstChild(selectedPlayerName)
        if targetPlayer and selectedPlayerIdText then
            selectedPlayerIdText:Set("Selected Player ID: " .. tostring(targetPlayer.UserId))
        elseif selectedPlayerIdText then
            selectedPlayerIdText:Set("Selected Player ID: N/A")
        end
    end,
})

local initialId = "N/A"
local firstTarget = Players:FindFirstChild(playerNamesTable[1])
if firstTarget then
    selectedPlayerName = firstTarget.Name
    initialId = tostring(firstTarget.UserId)
end

selectedPlayerIdText = PlayersTab:CreateParagraph({
    Title = "Player Information",
    Content = "Selected Player ID: " .. initialId
})

PlayersTab:CreateButton({
    Name = "Copy Player ID",
    Callback = function()
        local targetPlayer = Players:FindFirstChild(selectedPlayerName)
        if targetPlayer then
            if Clipboard then
                Clipboard(tostring(targetPlayer.UserId))
                Rayfield:Notify({
                    Title = "Success",
                    Content = "à¸à¸±à¸à¸¥à¸­à¸ User ID à¸à¸­à¸ " .. targetPlayer.Name .. " à¹à¸¥à¹à¸§!",
                    Duration = 2,
                })
            else
                Rayfield:Notify({
                    Title = "Error",
                    Content = "à¸£à¸°à¸à¸à¸à¸­à¸à¸à¸¸à¸à¹à¸¡à¹à¸£à¸­à¸à¸£à¸±à¸à¸à¸²à¸£à¸à¸±à¸à¸¥à¸­à¸à¸­à¸±à¸à¹à¸à¸¡à¸±à¸à¸´ (Clipboard)",
                    Duration = 2,
                })
            end
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "à¹à¸¡à¹à¸à¸à¸à¸¹à¹à¹à¸¥à¹à¸à¸à¸µà¹à¹à¸¥à¸·à¸­à¸",
                Duration = 2,
            })
        end
    end,
})

PlayersTab:CreateButton({
    Name = "Refresh Player List",
    Callback = function()
        UpdatePlayerList()
        if playerDropdown then
            playerDropdown:Refresh(playerNamesTable, true)
        end
        Rayfield:Notify({
            Title = "Player List",
            Content = "à¸£à¸µà¹à¸à¸£à¸à¸£à¸²à¸¢à¸à¸·à¹à¸­à¸à¸¹à¹à¹à¸¥à¹à¸à¹à¸£à¸µà¸¢à¸à¸£à¹à¸­à¸¢à¹à¸¥à¹à¸§",
            Duration = 2,
        })
    end,
})

PlayersTab:CreateButton({
    Name = "Teleport to Player",
    Callback = function()
        local targetPlayer = Players:FindFirstChild(selectedPlayerName)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if localRoot then
                localRoot.CFrame = targetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
                Rayfield:Notify({
                    Title = "Teleport",
                    Content = "à¸§à¸²à¸£à¹à¸à¹à¸à¸¢à¸±à¸ " .. targetPlayer.Name .. " à¸ªà¸³à¹à¸£à¹à¸!",
                    Duration = 2,
                })
            end
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "à¹à¸¡à¹à¸à¸à¸à¸±à¸§à¸¥à¸°à¸à¸£à¸à¸­à¸à¸à¸¹à¹à¹à¸¥à¹à¸à¸à¸à¸à¸µà¹",
                Duration = 2,
            })
        end
    end,
})

-- ==================== MOVEMENT TAB ====================
local MoveTab = Window:CreateTab("You Option", "user")
local MoveSection = MoveTab:CreateSection("Movement option")

local tpWalkEnabled = false
local tpWalkSpeed = 16

MoveTab:CreateToggle({
    Name = "Walkspeed",
    CurrentValue = false,
    Flag = "Toggle1", 
    Callback = function(Value)
        tpWalkEnabled = Value
    end,
})

MoveTab:CreateSlider({
    Name = "Walkspeed Option",
    Range = {0, 100},
    Increment = 5,
    Suffix = "Speed",
    CurrentValue = 16,
    Flag = "Slider1", 
    Callback = function(Value)
        tpWalkSpeed = Value
    end,
})

RunService.RenderStepped:Connect(function(dt)
    if tpWalkEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        local rootPart = LocalPlayer.Character.HumanoidRootPart
        
        if humanoid and humanoid.MoveDirection.Magnitude > 0 then
            rootPart.CFrame = rootPart.CFrame + (humanoid.MoveDirection * tpWalkSpeed * dt)
        end
    end
end)

local jumpPowerEnabled = false
local jumpPowerValue = 50

MoveTab:CreateToggle({
    Name = "JumpPower",
    CurrentValue = false,
    Flag = "Toggle2",
    Callback = function(Value)
        jumpPowerEnabled = Value
        local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.UseJumpPower = true
            humanoid.JumpPower = jumpPowerEnabled and jumpPowerValue or 50
        end
    end,
})

MoveTab:CreateSlider({
    Name = "JumpPower Option",
    Range = {50, 300},
    Increment = 10,
    Suffix = "Power",
    CurrentValue = 50,
    Flag = "Slider2",
    Callback = function(Value)
        jumpPowerValue = Value
        if jumpPowerEnabled then
            local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.UseJumpPower = true
                humanoid.JumpPower = jumpPowerValue
            end
        end
    end,
})

LocalPlayer.CharacterAdded:Connect(function(character)
    task.wait(0.5)
    local humanoid = character:WaitForChild("Humanoid")
    if jumpPowerEnabled then
        humanoid.UseJumpPower = true
        humanoid.JumpPower = jumpPowerValue
    end
end)

local infJumpEnabled = false

MoveTab:CreateToggle({
    Name = "Infinite Jump",
    CurrentValue = false,
    Flag = "Toggle_InfJump",
    Callback = function(Value)
        infJumpEnabled = Value
    end,
})

UserInputService.JumpRequest:Connect(function()
    if infJumpEnabled then
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end
end)

local BarrierbreakTab = Window:CreateTab("Barrier Break")

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- store originals
local originalMaxZoom
local originalMinZoom
local originalCameraMode
local originalMouseLock

BarrierbreakTab:CreateToggle({
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

BarrierbreakTab:CreateDivider()

Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
ReplicatedStorage = game:GetService("ReplicatedStorage")
Workspace = game:GetService("Workspace")

StatusLabel = BarrierbreakTab:CreateLabel("Broken: False")

destroyBarrierActive = false
destroyBarrierThread = nil
plotsBroken = false

BarrierbreakTab:CreateToggle({
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

local AntiTab = Window:CreateTab("Anti", "Shield")

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

local KeybindTab = Window:CreateTab("Keybinds", "keyboard")

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Keybind 1: Teleport (ID: Keybind1)
local TeleportKeybind = KeybindTab:CreateKeybind({
   Name = "Teleport",
   CurrentKeybind = "Z",
   HoldToInteract = false,
   Flag = "Keybind1",
   Callback = function(Keybind)
      local Character = LocalPlayer.Character
      if Character and Character:FindFirstChild("HumanoidRootPart") and Mouse.Hit then
         -- à¸§à¸²à¸£à¹à¸à¹à¸à¸à¸³à¹à¸«à¸à¹à¸à¸à¸µà¹à¹à¸¡à¸²à¸ªà¹à¸à¸µà¹ + à¸¢à¸à¸à¸±à¸§à¸à¸¶à¹à¸à¹à¸¥à¹à¸à¸à¹à¸­à¸¢ (3 studs) à¹à¸à¸·à¹à¸­à¹à¸¡à¹à¹à¸«à¹à¸à¸¡à¸à¸´à¸
         Character.HumanoidRootPart.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0))
      end
   end
})

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local controllingDecoy = false

local ControlYouDecoyKeybind = KeybindTab:CreateKeybind({
   Name = "Control Decoy",
   CurrentKeybind = "C",
   HoldToInteract = false,
   Flag = "ControlYouDecoyFlag", -- à¹à¸à¸¥à¸µà¹à¸¢à¸ Flag à¹à¸¡à¹à¹à¸«à¹à¸à¹à¸³à¸à¸±à¸ Keybind1 à¹à¸à¸·à¹à¸­à¸à¹à¸­à¸à¸à¸±à¸ Config à¸à¸à¸à¸±à¸
   Callback = function(Keybind)
      local Character = LocalPlayer.Character
      if not Character then return end
      
      local MainHumanoid = Character:FindFirstChildOfClass("Humanoid")
      -- à¸à¹à¸à¸«à¸²à¸à¸±à¸§à¸¥à¸°à¸à¸£ Decoy à¹à¸ Workspace à¹à¸à¸¢à¹à¸à¹ ID/à¸à¸·à¹à¸­ "YouDecoy"
      local Decoy = Workspace:FindFirstChild("YouDecoy")
      local DecoyHumanoid = Decoy and Decoy:FindFirstChildOfClass("Humanoid")

      if not controllingDecoy then
         -- à¸ªà¸¥à¸±à¸à¹à¸à¸ªà¸§à¸¡à¸£à¸­à¸¢à¸ªà¸¥à¸±à¸à¸à¸²à¸£à¸à¸§à¸à¸à¸¸à¸¡ Decoy
         if DecoyHumanoid then
            Camera.CameraSubject = DecoyHumanoid
            LocalPlayer.Character = Decoy
            controllingDecoy = true
         end
      else
         -- à¸ªà¸¥à¸±à¸à¸à¸¥à¸±à¸à¸¡à¸²à¸à¸§à¸à¸à¸¸à¸¡à¸à¸±à¸§à¸¥à¸°à¸à¸£à¸«à¸¥à¸±à¸
         if MainHumanoid then
            Camera.CameraSubject = MainHumanoid
            LocalPlayer.Character = Character
            controllingDecoy = false
         end
      end
   end,
})

local CombatTab = Window:CreateTab("Grab", "sword")

local Section = CombatTab:CreateSection("Grab")

_G.strength = 750
local strengthConnection

CombatTab:CreateSlider({
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

CombatTab:CreateToggle({
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

CombatTab:CreateToggle({
    Name = "Kill Grab",
    CurrentValue = false,
    Flag = "KillGrabToggle",
    Callback = function(Value)
        killGrabEnabled = Value
    end
})

CombatTab:CreateToggle({
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

        -- ð§ FIXED: ownership spam protection
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

local activeSparklers = {}
local sparklerConfig = {
    Height = 5,
    Speed = 2,
    Radius = 15,
    CurrentShape = 'Spinning Ring',
}

local shapeOptions = {
    'Spinning Ring', 'Supernova', 'Ophanim', 'Planet', 'Sphere', 'Cylinder', 'Double Ring', 'Star', 'Infinity', 'Heart', 
    'DNA Helix', 'Triple Helix', 'Tornado', 'Galaxy Spiral', 'Fibonacci Spiral', 
    'Spring Coil', 'Vortex Funnel', 'Box', 'Rounded Cube', 'Torus', 'Torus Knot', 
    'MÃ¶bius Strip', 'Saturn', 'Ice Cube', 'Rain', 'Sword', 'Sword v2', 'Sword v3', 'Energy', 'Tsunami', 'Grid', 'Robot',
    'Explosion', 'Flower', 'Penis', 'DivineLight', 'Crown', 'Hexagram', 'CubeOrbit', 'RingFloor', 'Event Horzion', "Pulsar Star",
    'Explosionv2', 'Dragon', 'Crazy Orbit', 'Crazy Orbit v2', 'Laser Beam', 'Hexagram Crazy', 'Gyro', 'Gyro2', 'Whipslash',
    'Sky', 'Manji', 'Small Circle', 'Atom', 'Meteor Shower', 'Meteor Shower (Star)', 'Diamond', 'SolarSystem', 'Windmill', 'Cat', 'Tentacle',
    'Portal', 'Water Wave', 'Moon Orbit'
}

local function SetupPhysics(obj, list)
    for _, v in ipairs(list) do
        if v == obj then return end
    end

    local mainPart = obj:IsA('BasePart') and obj or obj.PrimaryPart or obj:FindFirstChildWhichIsA('BasePart', true)
    if not mainPart then return end

    pcall(function()
        if mainPart:CanSetNetworkOwnership() then
            mainPart:SetNetworkOwner(LocalPlayer)
        end
    end)

    mainPart.Anchored = false

    local bp = mainPart:FindFirstChild('ToyBodyPos') or Instance.new('BodyPosition', mainPart)
    bp.Name = 'ToyBodyPos'
    bp.MaxForce = Vector3.new(1e8, 1e8, 1e8)
    bp.P = 100000
    bp.D = 800

    local bg = mainPart:FindFirstChild('ToyBodyGyro') or Instance.new('BodyGyro', mainPart)
    bg.Name = 'ToyBodyGyro'
    bg.MaxTorque = Vector3.new(1e8, 1e8, 1e8)
    bg.P = 50000

    for _, p in ipairs(obj:GetDescendants()) do
        if p:IsA('BasePart') then
            p.CanCollide = false
        end
    end

    table.insert(list, obj)
end

CombatTab:CreateButton({
    Name = "Synchronize All Sparklers",
    Callback = function()
        activeSparklers = {}
        for _, obj in ipairs(Workspace:GetDescendants()) do
            if obj.Name:find('FireworkSparkler') then
                SetupPhysics(obj, activeSparklers)
            end
        end
        print("à¸à¸´à¸à¸à¹à¹à¸­à¹à¸à¸¡à¸ªà¸³à¹à¸£à¹à¸ à¸à¸³à¸à¸§à¸à¸à¸±à¹à¸à¸«à¸¡à¸: " .. #activeSparklers)
    end,
})

CombatTab:CreateButton({
    Name = "Unsynchronize All Sparklers",
    Callback = function()
        activeSparklers = {}
        print("à¸¢à¸à¹à¸¥à¸´à¸à¸à¸²à¸£à¸à¸´à¸à¸à¹à¸à¸±à¹à¸à¸«à¸¡à¸à¹à¸¥à¹à¸§")
    end,
})

CombatTab:CreateSlider({
    Name = "Height Offset",
    Range = {-20, 150},
    Increment = 1,
    Suffix = "Studs",
    CurrentValue = 5,
    Flag = "Sparkler_Height",
    Callback = function(Value)
        sparklerConfig.Height = Value
    end,
})

CombatTab:CreateSlider({
    Name = "Shape Radius",
    Range = {0, 400},
    Increment = 1,
    Suffix = "Radius",
    CurrentValue = 15,
    Flag = "Sparkler_Radius",
    Callback = function(Value)
        sparklerConfig.Radius = Value
    end,
})

CombatTab:CreateSlider({
    Name = "Rotation Speed",
    Range = {0, 50},
    Increment = 0.5,
    Suffix = "Speed",
    CurrentValue = 15,
    Flag = "Sparkler_Speed",
    Callback = function(Value)
        sparklerConfig.Speed = Value
    end,
})

CombatTab:CreateDropdown({
    Name = "Select Shape",
    Options = shapeOptions,
    CurrentOption = "Spinning Ring",
    Flag = "Sparkler_Shape",
    Callback = function(Option)
        if type(Option) == "table" then
            sparklerConfig.CurrentShape = Option[1] or "Spinning Ring"
        else
            sparklerConfig.CurrentShape = tostring(Option)
        end
        print("à¹à¸à¸¥à¸µà¹à¸¢à¸à¸£à¸¹à¸à¸à¸£à¸à¹à¸à¹à¸: " .. tostring(sparklerConfig.CurrentShape))
    end,
})

-- ==================== SPARKLER TARGET PLAYER DROPDOWN ====================
local sparklerTargetPlayer = "None"
local sparklerPlayerNames = {"None"}

local function UpdateSparklerPlayerList()
    sparklerPlayerNames = {"None"}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(sparklerPlayerNames, player.Name)
        end
    end
end

local TAU = math.pi * 2
local sqrt = math.sqrt
local sin = math.sin
local cos = math.cos
local clamp = math.clamp

local Shapes = {}

-- Moon Orbit (Sphere à¸«à¸¥à¸±à¸ 1 à¸­à¸±à¸ + Sphere à¹à¸¥à¹à¸à¹ à¹à¸à¸à¸£)
Shapes['Moon Orbit'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- Sphere à¸«à¸¥à¸±à¸ 1 à¸­à¸±à¸ (à¸­à¸±à¸à¹à¸£à¸)
    if i == 1 then
        -- à¸à¸§à¸à¸à¸±à¸à¸à¸£à¹à¸«à¸¥à¸±à¸ (à¸à¸£à¸à¸à¸¥à¸²à¸)
        local phi = math.acos(1 - 2 * (i / safeN))
        local theta = i * math.pi * (3 - math.sqrt(5)) + t * sp * 0.5
        local radius = r * 0.3
        
        local x = math.cos(theta) * math.sin(phi) * radius
        local y = math.cos(phi) * radius + h
        local z = math.sin(theta) * math.sin(phi) * radius
        
        return Vector3.new(x, y, z)
    else
        -- Sphere à¹à¸¥à¹à¸à¹ à¹à¸à¸à¸£à¸£à¸­à¸
        local idx = i - 1
        local count = safeN - 1
        
        -- à¹à¸à¸à¸£à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡
        local angle = (idx / count) * TAU + t * sp * 2
        local orbitRadius = r * 0.7
        
        local x = math.cos(angle) * orbitRadius
        local z = math.sin(angle) * orbitRadius
        local y = h + math.sin(t * sp * 0.5 + idx) * orbitRadius * 0.1
        
        return Vector3.new(x, y, z)
    end
end

-- Water Wave (à¸à¸¥à¸·à¹à¸à¸à¹à¸³)
Shapes['Water Wave'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸¥à¸·à¹à¸à¸à¹à¸³ (à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡ + à¸à¸£à¸°à¹à¸à¸·à¹à¸­à¸¡)
    local angle = ((i - 1) / safeN) * TAU + t * sp * 1.5
    
    -- à¸à¸¥à¸·à¹à¸à¸à¸£à¸°à¹à¸à¸·à¹à¸­à¸¡ (à¹à¸«à¸¡à¸·à¸­à¸à¸à¹à¸³)
    local wave = math.sin(angle * 2 + t * sp * 2) * r * 0.3
    local wave2 = math.cos(angle * 3 + t * sp * 1.5) * r * 0.2
    
    -- à¸£à¸±à¸¨à¸¡à¸µà¸à¸­à¸à¸à¸¥à¸·à¹à¸
    local radius = r * (0.7 + 0.3 * math.sin(t * sp * 1.5 + i * 0.1))
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * radius + wave
    local z = math.sin(angle) * radius + wave2
    
    -- à¸à¸§à¸²à¸¡à¸ªà¸¹à¸ (à¸à¸¥à¸·à¹à¸à¸à¸°à¸ªà¸¹à¸à¸à¸¶à¹à¸à¹à¸¥à¸°à¸à¹à¸³à¸¥à¸)
    local y = h + math.sin(angle * 3 + t * sp * 2) * r * 0.2
    
    return Vector3.new(x, y, z)
end

Shapes.Diamond = function(i, n, t, r, h, sp)
    local frac = i / n
    local y = (frac - 0.5) * r * 2
    local radius = r * math.abs(math.sin(frac * TAU))
    
    return Vector3.new(math.cos(t * sp + i) * radius, h + y, math.sin(t * sp + i) * radius)
end

Shapes.SolarSystem = function(i, n, t, r, h, sp)
    local planet = i % 8
    local dist = r * (0.2 + planet * 0.1)
    local angle = (i / n) * TAU + t * sp * (1 + planet * 0.2)
    
    return Vector3.new(math.cos(angle) * dist, h + math.sin(i * 0.5) * 2, math.sin(angle) * dist)
end

Shapes.Windmill = function(i, n, t, r, h, sp)
    local blade = i % 4
    local bladeAngle = blade * (TAU / 4) + t * sp
    local radius = r * (0.5 + 0.5 * (i / n))
    
    return Vector3.new(math.cos(bladeAngle) * radius, h + math.sin(t * sp + i) * 2, math.sin(bladeAngle) * radius)
end

Shapes.Tentacle = function(i, n, t, r, h, sp)
    local frac = i / n
    local angle = frac * TAU * 2 + t * sp * 0.5
    local radius = r * frac
    
    return Vector3.new(math.cos(angle) * radius, h + math.sin(frac * TAU * 4 + t * sp) * r * 0.5, math.sin(angle) * radius)
end

Shapes.Portal = function(i, n, t, r, h, sp)
    local frac = i / n
    local angle = frac * TAU + t * sp
    local radius = r * (1 + 0.3 * math.sin(t * sp * 2 + i * 0.5))
    local distort = math.sin(t * sp * 3 + frac * TAU) * r * 0.1
    
    return Vector3.new(math.cos(angle) * radius + distort, h + math.sin(t * sp + i) * 3, math.sin(angle) * radius + distort)
end

-- Meteor Shower (à¸à¸à¸¥à¸à¸¡à¸²à¹à¸à¹à¸à¸£à¸¹à¸à¸à¸²à¸§)
Shapes['Galaxy Methor'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸£à¸¹à¸à¸à¸²à¸§ (Star Shape)
    local angle = ((i - 1) / safeN) * TAU + t * sp * 2
    local starRadius = (i % 2 == 0) and r or r * 0.38
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z à¸à¸­à¸à¸à¸²à¸§
    local x = math.cos(angle) * starRadius
    local z = math.sin(angle) * starRadius
    
    -- à¸à¸²à¸£à¸à¸à¸¥à¸à¸¡à¸² (Meteor Shower)
    local seed = i * 137.508
    local fallSpeed = 30 + math.sin(seed * 0.5) * 10
    local fallTime = (t * sp * 3 + i * 5) % 200  -- à¸à¸à¹à¸¥à¹à¸§à¸§à¸à¸à¸¥à¸±à¸
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¸à¸à¸¥à¸à¸¡à¸²à¸à¸²à¸à¸à¹à¸²
    local y = h + 100 - fallTime * 0.8
    
    -- à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¹à¸à¸à¹à¸²à¸à¸à¹à¸²à¸à¹à¸¥à¹à¸à¸à¹à¸­à¸¢à¸à¸à¸°à¸à¸à¸¥à¸
    x = x + math.sin(t * sp * 0.5 + i) * 10
    z = z + math.cos(t * sp * 0.5 + i) * 10
    
    -- à¸à¸³à¹à¸«à¹à¸à¸¹à¹à¸«à¸¡à¸·à¸­à¸à¸¡à¸µà¸«à¸²à¸ (à¹à¸ªà¹à¸à¸«à¸¥à¸±à¸)
    local trail = math.max(0, 1 - (fallTime / 200)) * 3
    
    return Vector3.new(x, y + trail, z)
end

-- Small Circle (à¸§à¸à¹à¸à¸à¸£à¹à¸¥à¹à¸à¹ + à¸«à¸±à¸à¸«à¸²à¸à¸±à¸§à¸à¸¥à¸­à¸)
Shapes['Small Circle'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¹à¸à¹à¸¥à¸° Sparkler à¸¡à¸µà¸§à¸à¸à¸¥à¸¡à¸à¸­à¸à¸à¸±à¸§à¹à¸­à¸
    local seed = i * 137.508
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¸à¸¥à¸²à¸à¸à¸­à¸à¸§à¸à¸à¸¥à¸¡ (à¸à¸£à¸°à¸à¸²à¸¢à¸à¸±à¸§à¸£à¸­à¸à¸à¸±à¸§)
    local centerX = math.sin(seed * 0.1 + t * sp * 0.1) * r * 0.8
    local centerZ = math.cos(seed * 0.05 + t * sp * 0.1) * r * 0.8
    local centerY = h + math.sin(seed * 0.03 + t * sp * 0.05) * r * 0.3
    
    -- à¸§à¸à¹à¸à¸à¸£à¹à¸¥à¹à¸à¹ (à¸£à¸­à¸ Sparkler à¹à¸à¹à¸¥à¸°à¸à¸´à¹à¸)
    local orbitRadius = r * 0.1 + math.sin(seed * 0.5) * r * 0.05
    local orbitAngle = t * sp * (3 + math.cos(seed * 0.1)) + seed
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¹à¸à¸§à¸à¹à¸à¸à¸£
    local x = centerX + math.cos(orbitAngle) * orbitRadius
    local z = centerZ + math.sin(orbitAngle) * orbitRadius
    local y = centerY + math.sin(t * sp * 0.5 + seed) * orbitRadius * 0.5
    
    return Vector3.new(x, y, z)
end

-- Manji (à¸ªà¸§à¸±à¸ªà¸à¸´à¸à¸°)
Shapes.Manji = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸ªà¸§à¸±à¸ªà¸à¸´à¸à¸°à¸à¸£à¸°à¸à¸­à¸à¸à¹à¸§à¸¢ 4 à¹à¸à¸ à¹à¸à¹à¸¥à¸°à¹à¸à¸à¸¡à¸µ 2 à¸à¹à¸­à¸
    local armCount = 4
    local segmentCount = math.floor(safeN / (armCount * 2))
    if segmentCount < 1 then segmentCount = 1 end
    
    -- à¸à¸³à¸à¸§à¸à¸§à¹à¸² Sparkler à¸à¸µà¹à¸­à¸¢à¸¹à¹à¹à¸à¸à¹à¸«à¸
    local armIndex = math.floor((i - 1) / (segmentCount * 2)) % armCount
    local segmentIndex = (i - 1) % (segmentCount * 2)
    local isInner = segmentIndex < segmentCount  -- à¸à¹à¸­à¸à¹à¸à¸«à¸£à¸·à¸­à¸à¹à¸­à¸à¸à¸­à¸
    
    -- à¸¡à¸¸à¸¡à¸à¸­à¸à¹à¸à¹à¸¥à¸°à¹à¸à¸ (0, 90, 180, 270 à¸­à¸à¸¨à¸²)
    local armAngle = (armIndex / armCount) * TAU + t * sp * 2
    
    -- à¸à¸§à¸²à¸¡à¸¢à¸²à¸§à¸à¸­à¸à¹à¸à¸
    local armLength = r * 0.8
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¸à¸²à¸¡à¹à¸à¸
    local progress = (segmentIndex % segmentCount) / segmentCount
    local distance = isInner and (progress * armLength * 0.5) or (armLength * 0.5 + progress * armLength * 0.5)
    
    -- à¸¡à¸¸à¸¡à¸à¸­à¸à¸à¹à¸­à¸ (à¸à¹à¸­à¸à¹à¸à¹à¸à¸à¸²à¸à¸à¹à¸²à¸¢, à¸à¹à¸­à¸à¸à¸­à¸à¹à¸à¸à¸²à¸à¸à¸§à¸²)
    local segmentAngle = isInner and (armAngle - TAU / 4) or (armAngle + TAU / 4)
    
    -- à¸à¸³à¸à¸§à¸à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(armAngle) * (isInner and distance * 0.5 or distance) + math.cos(segmentAngle) * (isInner and distance * 0.5 or 0)
    local z = math.sin(armAngle) * (isInner and distance * 0.5 or distance) + math.sin(segmentAngle) * (isInner and distance * 0.5 or 0)
    
    -- à¸à¸§à¸²à¸¡à¸ªà¸¹à¸ (à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡à¹à¸¥à¹à¸à¸à¹à¸­à¸¢)
    local y = h + math.sin(t * sp * 1.5 + i * 0.3) * r * 0.1
    
    return Vector3.new(x, y, z)
end

-- Sky (à¸§à¸²à¸£à¹à¸à¹à¸à¸§à¸²à¸£à¹à¸à¸¡à¸²à¹à¸à¹à¸à¹à¸ªà¹à¸à¸à¸£à¸)
Shapes.Sky = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local seed = i * 137.508 + i * i * 0.01
    
    -- à¸à¸³à¸«à¸à¸à¸à¸¸à¸à¹à¸£à¸´à¹à¸¡à¸à¹à¸à¹à¸¥à¸°à¸à¸¸à¸à¸à¸¥à¸²à¸¢ (à¹à¸ªà¹à¸à¸à¸£à¸)
    local startX = math.sin(seed * 0.1) * r
    local startZ = math.cos(seed * 0.05) * r
    local startY = h + math.abs(math.sin(seed * 0.03)) * 30
    
    local endX = math.cos(seed * 0.1) * r
    local endZ = math.sin(seed * 0.05) * r
    local endY = h + math.abs(math.cos(seed * 0.03)) * 30 + 20
    
    -- à¸à¸²à¸£à¸§à¸²à¸£à¹à¸ (à¸à¸£à¸°à¹à¸à¸à¹à¸à¸¡à¸²à¹à¸à¹à¸à¸à¸±à¸à¸«à¸§à¸°)
    local warpPhase = math.sin(t * sp * 0.5 + i * 0.5)
    local warp = math.floor(warpPhase * 2) % 2  -- 0 à¸«à¸£à¸·à¸­ 1 à¸ªà¸¥à¸±à¸à¸à¸±à¸
    
    -- à¸à¹à¸²à¸§à¸²à¸£à¹à¸ = 0 à¹à¸à¸à¸¸à¸à¹à¸£à¸´à¹à¸¡à¸à¹à¸, à¸à¹à¸² = 1 à¹à¸à¸à¸¸à¸à¸à¸¥à¸²à¸¢
    local targetX = (warp == 0) and startX or endX
    local targetZ = (warp == 0) and startZ or endZ
    local targetY = (warp == 0) and startY or endY
    
    -- à¹à¸à¸´à¹à¸¡à¸à¸²à¸£à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¹à¸à¸à¹à¸ªà¹à¸à¸à¸£à¸à¹à¸¥à¹à¸à¸à¹à¸­à¸¢à¸£à¸°à¸«à¸§à¹à¸²à¸à¸§à¸²à¸£à¹à¸
    local moveX = math.sin(t * sp * 0.1 + i) * 5
    local moveZ = math.cos(t * sp * 0.1 + i) * 5
    local moveY = math.sin(t * sp * 0.1 + i * 0.5) * 3
    
    local x = targetX + moveX
    local z = targetZ + moveZ
    local y = targetY + moveY
    
    return Vector3.new(x, y, z)
end

-- Gyro (10 à¸à¸±à¹à¸ à¸«à¸¡à¸¸à¸à¸ªà¸§à¸à¸à¸²à¸à¸à¸±à¸)
Shapes.Gyro = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local layers = 10
    local pointsPerLayer = math.max(math.floor(safeN / layers), 1)
    local layerIndex = math.floor((i - 1) / pointsPerLayer)
    if layerIndex >= layers then layerIndex = layers - 1 end
    local posInLayer = (i - 1) % pointsPerLayer
    
    -- à¹à¸à¹à¸¥à¸°à¸à¸±à¹à¸à¸¡à¸µà¸£à¸±à¸¨à¸¡à¸µà¸à¹à¸²à¸à¸à¸±à¸
    local radius = r * (0.2 + 0.8 * (layerIndex + 1) / layers)
    local heightOffset = (layerIndex - (layers - 1) / 2) * (r * 0.15)
    local yPos = h + heightOffset
    
    -- à¸ªà¸¸à¹à¸¡à¸à¸´à¸¨à¸à¸²à¸: à¸à¸±à¹à¸à¸à¸¹à¹à¸«à¸¡à¸¸à¸à¹à¸à¸à¸²à¸à¸à¸§à¸², à¸à¸±à¹à¸à¸à¸µà¹à¸«à¸¡à¸¸à¸à¹à¸à¸à¸²à¸à¸à¹à¸²à¸¢
    local direction = (layerIndex % 2 == 0) and 1 or -1
    local randomSpeed = 2 + math.sin(layerIndex * 1.5) * 1.5  -- à¸ªà¸¸à¹à¸¡à¸à¸§à¸²à¸¡à¹à¸£à¹à¸§ 0.5-3.5
    local angle = (posInLayer / pointsPerLayer) * TAU + t * sp * randomSpeed * direction
    
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, yPos, z)
end

-- Hexagram Crazy (6 à¸à¸¸à¸à¸£à¸§à¸¡à¸à¸±à¸ + à¹à¸à¸à¸£à¸à¹à¸²à¸à¸¥à¸±à¹à¸)
Shapes['Hexagram Crazy'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸²à¸§à¸«à¸à¹à¸à¸ 6 à¸à¸¸à¸ (à¸£à¸§à¸¡à¸à¸±à¸à¹à¸à¹à¸à¸à¹à¸­à¸à¹à¸à¸µà¸¢à¸§)
    local pointsPerTriangle = 3
    local totalPoints = 6
    
    -- à¸à¸¸à¸à¸¢à¸­à¸à¸à¸­à¸à¸ªà¸²à¸¡à¹à¸«à¸¥à¸µà¹à¸¢à¸¡
    local pointIndex = (i - 1) % totalPoints
    local isFirstTriangle = pointIndex < pointsPerTriangle
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¸à¸·à¹à¸à¸à¸²à¸à¸à¸­à¸à¸à¸²à¸§à¸«à¸à¹à¸à¸ (6 à¸à¸¸à¸à¸­à¸¢à¸¹à¹à¸£à¸§à¸¡à¸à¸±à¸)
    local spin = t * sp * 2
    local baseX, baseY, baseZ
    
    if isFirstTriangle then
        local idx = pointIndex
        local a = idx * (TAU / 3) + spin
        baseX = math.cos(a) * r
        baseZ = math.sin(a) * r
        baseY = h
    else
        local idx = pointIndex - pointsPerTriangle
        local a = idx * (TAU / 3) + math.pi / 3 + spin
        baseX = math.cos(a) * r
        baseZ = math.sin(a) * r
        baseY = h
    end
    
    -- à¸£à¸§à¸¡ 6 à¸à¸¸à¸à¹à¸«à¹à¹à¸à¹à¸à¸à¹à¸­à¸à¹à¸à¸µà¸¢à¸§ (à¸¢à¹à¸­à¸à¸à¸²à¸à¸¥à¸)
    local scale = 0.3  -- à¸¢à¹à¸­à¸à¸à¸²à¸à¹à¸«à¹à¹à¸¥à¹à¸à¸¥à¸
    baseX = baseX * scale
    baseY = baseY * scale
    baseZ = baseZ * scale
    
    -- à¹à¸à¸´à¹à¸¡à¸à¸²à¸£à¹à¸à¸à¸£à¸à¹à¸²à¸à¸¥à¸±à¹à¸ (à¸à¹à¸­à¸à¹à¸à¸µà¸¢à¸§à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¹à¸à¸¡à¸²)
    local seed = i * 137.508
    local noise1 = math.sin(seed * 0.1 + t * sp * 3) * math.cos(seed * 0.05 + t * sp * 2)
    local noise2 = math.cos(seed * 0.08 + t * sp * 4) * math.sin(seed * 0.12 + t * sp * 3)
    local noise3 = math.sin(seed * 0.15 + t * sp * 5) * math.cos(seed * 0.1 + t * sp * 4)
    
    -- à¸à¹à¸­à¸à¹à¸à¸µà¸¢à¸§à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¸à¹à¸²à¸à¸¥à¸±à¹à¸à¹à¸à¸¡à¸²
    local crazyX = noise1 * r * 1.2
    local crazyY = noise2 * r * 1.2
    local crazyZ = noise3 * r * 1.2
    
    -- à¸£à¸§à¸¡à¸à¸³à¹à¸«à¸à¹à¸: 6 à¸à¸¸à¸à¸£à¸§à¸¡à¸à¸±à¸ + à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¸à¹à¸²à¸à¸¥à¸±à¹à¸
    local finalX = baseX + crazyX
    local finalY = baseY + crazyY
    local finalZ = baseZ + crazyZ
    
    return Vector3.new(finalX, finalY, finalZ)
end

-- Whipslash (à¹à¸ªà¹à¸à¸à¸£à¸ à¸à¸²à¸à¹à¸£à¹à¸§)
Shapes.Whipslash = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local whipLength = r * 3
    local yPos = h + ((i - 1) / safeN) * whipLength
    
    -- à¸à¸²à¸à¹à¸£à¹à¸§à¸à¸¶à¹à¸
    local whipPhase = math.sin(t * sp * 4)
    local swingX = whipPhase * r * 0.9
    local x = swingX
    local z = 0
    
    return Vector3.new(x, yPos, z)
end

-- Laser Beam (à¸¥à¸³à¹à¸ªà¸à¸à¸£à¸°à¸à¸£à¸´à¸)
Shapes['Laser Beam'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local beamLength = r * 3
    local yPos = h + ((i - 1) / safeN) * beamLength - beamLength/2
    
    -- à¸¥à¸³à¹à¸ªà¸à¸à¸£à¸°à¸à¸£à¸´à¸à¹à¸à¹à¸à¸à¸±à¸à¸«à¸§à¸°
    local flicker = 1 + 0.3 * math.sin(t * sp * 6 + i * 0.5)
    local angle = ((i - 1) / safeN) * TAU + t * sp * 3
    
    local radius = r * 0.1 * flicker
    
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, yPos, z)
end

-- Crazy Orbit v2 (à¹à¸à¸à¸£à¸à¹à¸²à¸à¸¥à¸±à¹à¸à¹à¸à¹à¸­à¸¢à¸¹à¹à¸à¸±à¸à¸à¸µà¹ + à¸¥à¸à¸à¸§à¸²à¸¡à¹à¸£à¹à¸§)
Shapes['Crazy Orbit v2'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸¥à¸à¸à¸§à¸²à¸¡à¹à¸£à¹à¸§à¸¥à¸ (sp à¸à¹à¸²à¸¥à¸)
    local slowSpeed = sp * 0.3
    
    -- à¸ªà¸¸à¹à¸¡à¸à¸³à¹à¸«à¸à¹à¸à¹à¸à¸¢à¹à¸à¹ noise function à¹à¸à¹à¸à¹à¸²à¸¥à¸
    local seed = i * 137.508
    local noise1 = math.sin(seed * 0.1 + t * slowSpeed * 1.5) * math.cos(seed * 0.05 + t * slowSpeed * 1)
    local noise2 = math.cos(seed * 0.08 + t * slowSpeed * 2) * math.sin(seed * 0.12 + t * slowSpeed * 1.2)
    local noise3 = math.sin(seed * 0.15 + t * slowSpeed * 2.5) * math.cos(seed * 0.1 + t * slowSpeed * 1.8)
    
    -- à¸à¸³à¸à¸±à¸à¸£à¸±à¸¨à¸¡à¸µà¹à¸«à¹à¸­à¸¢à¸¹à¹à¹à¸à¸§à¸à¹à¸à¸ (à¸­à¸¢à¸¹à¹à¸à¸±à¸à¸à¸µà¹)
    local randomRadius = r * (0.3 + 0.4 * math.abs(noise1))  -- à¹à¸à¸à¸¥à¸
    
    -- à¸ªà¸¸à¹à¸¡à¸¡à¸¸à¸¡ (à¹à¸à¸¥à¸µà¹à¸¢à¸à¸à¹à¸²à¸¥à¸)
    local randomAngle = noise2 * TAU + t * slowSpeed * 2
    
    -- à¸ªà¸¸à¹à¸¡à¸à¸§à¸²à¸¡à¸ªà¸¹à¸ (à¸­à¸¢à¸¹à¹à¹à¸à¸à¸£à¸­à¸)
    local randomHeight = h + noise3 * r * 0.4  -- à¸ªà¸¹à¸à¹à¸¡à¹à¸¡à¸²à¸
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(randomAngle) * randomRadius
    local z = math.sin(randomAngle) * randomRadius
    
    return Vector3.new(x, randomHeight, z)
end

-- Crazy Orbit (à¹à¸à¹à¸¥à¸°à¸à¸´à¹à¸à¹à¸à¸¥à¸·à¹à¸­à¸à¸à¸µà¹à¸­à¸´à¸ªà¸£à¸°)
Shapes['Crazy Orbit'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¹à¸à¹à¸¥à¸°à¸à¸´à¹à¸à¸¡à¸µ seed à¸à¹à¸²à¸à¸à¸±à¸
    local seed = i * 137.508 + i * i * 0.01
    
    -- à¸ªà¸¸à¹à¸¡à¹à¸à¸à¹à¸¡à¹à¹à¸«à¸¡à¸·à¸­à¸à¸à¸±à¸
    local noise1 = math.sin(seed * 0.1 + t * sp * (2 + i * 0.05)) * math.cos(seed * 0.05 + t * sp * (1.5 + i * 0.03))
    local noise2 = math.cos(seed * 0.08 + t * sp * (2.5 + i * 0.04)) * math.sin(seed * 0.12 + t * sp * (1.8 + i * 0.02))
    local noise3 = math.sin(seed * 0.15 + t * sp * (3 + i * 0.06)) * math.cos(seed * 0.1 + t * sp * (2.2 + i * 0.05))
    
    -- à¸ªà¸¸à¹à¸¡à¸£à¸±à¸¨à¸¡à¸µ, à¸¡à¸¸à¸¡, à¸à¸§à¸²à¸¡à¸ªà¸¹à¸
    local randomRadius = r * (0.2 + 0.8 * math.abs(noise1))
    local randomAngle = noise2 * TAU + t * sp * (2 + i * 0.1)
    local randomHeight = h + noise3 * r * 1.2
    
    local x = math.cos(randomAngle) * randomRadius
    local z = math.sin(randomAngle) * randomRadius
    
    return Vector3.new(x, randomHeight, z)
end

-- Dragon Shape (à¸¡à¸±à¸à¸à¸£à¸¡à¸µà¸«à¸±à¸§à¹à¸¥à¸°à¸«à¸²à¸)
Shapes.Dragon = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local frac = i / safeN
    
    -- à¸ªà¹à¸§à¸à¸«à¸±à¸§ (20% à¹à¸£à¸)
    local isHead = frac < 0.2
    -- à¸ªà¹à¸§à¸à¸«à¸²à¸ (20% à¸ªà¸¸à¸à¸à¹à¸²à¸¢)
    local isTail = frac > 0.8
    
    local bodyLength = r * 3
    local yPos = h - bodyLength/2 + frac * bodyLength
    
    -- à¸à¸¥à¸·à¹à¸à¸à¸­à¸à¸¥à¸³à¸à¸±à¸§
    local wave = math.sin(frac * TAU * 4 + t * sp * 2) * r * 0.3
    local wave2 = math.cos(frac * TAU * 3 + t * sp * 1.5) * r * 0.2
    
    local angle = frac * TAU * 1.5 + t * sp * 1.5
    
    -- à¸à¸à¸²à¸à¸£à¸±à¸¨à¸¡à¸µ (à¸«à¸±à¸§à¹à¸«à¸à¹ à¸«à¸²à¸à¹à¸¥à¹à¸)
    local radius = r * 0.4
    if isHead then
        radius = radius * (1.5 - frac * 2)  -- à¸«à¸±à¸§à¹à¸«à¸à¹
    elseif isTail then
        radius = radius * (1 - (frac - 0.8) * 2)  -- à¸«à¸²à¸à¹à¸¥à¹à¸
    end
    
    local x = math.cos(angle) * radius + wave
    local z = math.sin(angle) * radius + wave2
    
    return Vector3.new(x, yPos, z)
end

-- 0. Spinning Ring
Shapes['Spinning Ring'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU + (t * sp * 5)
    return Vector3.new(cos(a) * r, h, sin(a) * r)
end

-- Supernova Shape
Shapes.Supernova = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local phi = math.acos(clamp(1 - 2 * (i / safeN), -1, 1))
    local theta = i * math.pi * (3 - sqrt(5)) + (t * sp * 2)
    local pulse = 1 + 0.3 * sin(t * sp * 4 + i)
    local currentR = r * pulse
    local x = cos(theta) * sin(phi) * currentR
    local y = cos(phi) * currentR + h
    local z = sin(theta) * sin(phi) * currentR
    return Vector3.new(x, y, z)
end

-- 1. Ophanim
Shapes.Ophanim = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local ring = (i - 1) % 3
    local ringIndex = math.floor((i - 1) / 3)
    local ringTotal = math.max(math.floor(safeN / 3), 1)
    local frac = ringIndex / ringTotal
    local a = frac * TAU + t * sp * (ring == 0 and 1 or (ring == 1 and -1.5 or 2))
    local rx = r * (ring == 0 and 1 or 0.75)
    local rz = r * (ring == 1 and 1 or 0.5)
    local x = cos(a) * rx
    local y = sin(a * 2 + t * sp) * (r * 0.25) + h
    local z = sin(a) * rz
    if ring == 1 then x, y = y - h, x + h
    elseif ring == 2 then z, y = y - h, z + h end
    return Vector3.new(x, y, z)
end

-- 2. Planet
Shapes.Planet = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local coreN = clamp(math.floor(safeN * 0.35), 4, 15)
    coreN = math.min(coreN, safeN)
    local ring1N = math.max(math.floor((safeN - coreN) / 2), 1)
    local spin = t * sp
    if i <= coreN then
        local phi = math.acos(clamp(1 - 2 * (i / coreN), -1, 1))
        local theta = i * math.pi * (3 - sqrt(5)) + spin
        local cr = r * 0.35
        return Vector3.new(cos(theta) * sin(phi) * cr, cos(phi) * cr + h, sin(theta) * sin(phi) * cr)
    elseif i <= coreN + ring1N then
        local idx = i - coreN
        local a = (idx / ring1N) * TAU + spin
        local rr = r * 0.9
        local tilt = math.rad(30)
        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
    else
        local idx = i - (coreN + ring1N)
        local c = math.max(safeN - (coreN + ring1N), 1)
        local a = (idx / c) * TAU + t * sp * 0.5
        local rr = r * 1.3
        local tilt = math.rad(-40)
        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
    end
end

-- 3. Sphere
Shapes.Sphere = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local phi = math.acos(clamp(1 - 2 * (i / safeN), -1, 1))
    local theta = i * math.pi * (3 - sqrt(5)) + t * sp * 2
    return Vector3.new(cos(theta) * sin(phi) * r, cos(phi) * r + h, sin(theta) * sin(phi) * r)
end

-- 4. Cylinder
Shapes.Cylinder = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU + t * sp
    local y = ((i - 1) / safeN) * r * 1.5 - r * 0.75
    return Vector3.new(cos(a) * r, h + y, sin(a) * r)
end

-- 5. Double Ring
Shapes['Double Ring'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local halfN = math.max(math.floor(safeN / 2), 1)
    local a = (((i - 1) % halfN) / halfN) * TAU + t * sp
    if i <= halfN then
        return Vector3.new(cos(a) * r, h, sin(a) * r)
    else
        return Vector3.new(0, h + cos(a) * r, sin(a) * r)
    end
end

-- 6. Star
Shapes.Star = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU + t * sp
    local rr = (i % 2 == 0) and r or r * 0.38
    return Vector3.new(cos(a) * rr, h + sin(t * sp * 1.5 + i * 0.3) * 1.5, sin(a) * rr)
end

-- 7. Infinity
Shapes.Infinity = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU + t * sp
    local d = 1 + sin(a) ^ 2
    return Vector3.new((r * cos(a)) / d, h + sin(t * sp + i * 0.2) * 1.2, (r * sin(a) * cos(a)) / d)
end

-- 8. Heart
Shapes.Heart = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU + t * sp
    local pulse = 1 + 0.12 * sin(t * sp * 2)
    local scale = (r / 15) * pulse
    local x = 16 * sin(a) ^ 3
    local z = -(13 * cos(a) - 5 * cos(2 * a) - 2 * cos(3 * a) - cos(4 * a))
    return Vector3.new(x * scale, h + sin(t * sp * 2) * 0.5, z * scale)
end

-- 9. DNA Helix
Shapes['DNA Helix'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local y = ((i - 1) / safeN) * r * 2 - r
    local a = ((i - 1) / safeN) * TAU + t * sp + y * 0.5
    local side = (i % 2 == 0) and 1 or -1
    return Vector3.new(cos(a) * r * side, h + y, sin(a) * r * side)
end

-- 10. Triple Helix
Shapes['Triple Helix'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local y = ((i - 1) / safeN) * r * 2 - r
    local a = ((i - 1) / safeN) * TAU + t * sp + y * 0.5
    local phase = ((i - 1) % 3) * (TAU / 3)
    return Vector3.new(cos(a + phase) * r, h + y, sin(a + phase) * r)
end

-- 11. Tornado
Shapes.Tornado = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local y = ((i - 1) / safeN) * (r * 2)
    local currentR = (y / (r * 2)) * r
    local a = ((i - 1) / safeN) * TAU * 3 + t * sp * 3
    return Vector3.new(cos(a) * currentR, h + y - r, sin(a) * currentR)
end

-- 12. Galaxy Spiral
Shapes['Galaxy Spiral'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local a = ((i - 1) / safeN) * TAU * 2 + t * sp
    local currentR = ((i - 1) / safeN) * r
    return Vector3.new(cos(a) * currentR, h + sin(a * 2) * 0.5, sin(a) * currentR)
end

-- 13. Fibonacci Spiral
Shapes['Fibonacci Spiral'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local phi = (1 + sqrt(5)) / 2
    local a = (i - 1) * phi * TAU + t * sp
    local currentR = sqrt((i - 1) / safeN) * r
    return Vector3.new(cos(a) * currentR, h, sin(a) * currentR)
end

-- 14. Spring Coil
Shapes['Spring Coil'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local y = ((i - 1) / safeN) * (r * 2) - r
    local a = ((i - 1) / safeN) * TAU * 5 + t * sp * 2
    return Vector3.new(cos(a) * (r * 0.5), h + y, sin(a) * (r * 0.5))
end

-- Event Horizon (à¸à¸­à¸à¸à¹à¸²à¹à¸«à¸à¸¸à¸à¸²à¸£à¸à¹ - à¸§à¸à¹à¸«à¸§à¸à¸«à¸¡à¸¸à¸)
Shapes['Event Horizon'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 6 + t * sp
    local ring = r * (0.3 + 0.7 * abs(sin(frac * math.pi)))
    local glow = 1 + 0.2 * sin(t * sp * 2 + frac * TAU)
    
    return Vector3.new(cos(a) * ring * glow, h + sin(t * sp + frac * TAU) * 2, sin(a) * ring * glow)
end

-- Pulsar Star (à¸à¸²à¸§à¸à¸±à¸¥à¸à¸²à¸£à¹ - à¸à¸£à¸°à¸à¸£à¸´à¸à¹à¸à¹à¸à¸à¸±à¸à¸«à¸§à¸°)
Shapes['Pulsar Star'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 4 + t * sp * 3
    local pulse = 1 + 0.5 * sin(t * sp * 6 + i)
    local dist = r * frac * pulse
    local y = h + sin(t * sp * 3 + frac * TAU) * r * 0.3
    
    return Vector3.new(cos(a) * dist, y, sin(a) * dist)
end

-- 15. Vortex Funnel
Shapes['Vortex Funnel'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local y = ((i - 1) / safeN) * (r * 2) - r
    local currentR = math.abs(y) * 0.8
    local a = ((i - 1) / safeN) * TAU * 4 + t * sp * 3
    return Vector3.new(cos(a) * currentR, h + y, sin(a) * currentR)
end

-- 16. Box
Shapes.Box = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local sideN = math.max(math.floor(safeN / 6), 1)
    local face = math.floor((i - 1) / sideN) % 6
    local idx = (i - 1) % sideN
    local f = (idx / sideN) * (r * 2) - r
    local x, y, z = 0, 0, 0
    if face == 0 then x, y, z = r, f, (idx % 2 == 0 and r or -r)
    elseif face == 1 then x, y, z = -r, f, (idx % 2 == 0 and r or -r)
    elseif face == 2 then x, y, z = f, r, (idx % 2 == 0 and r or -r)
    elseif face == 3 then x, y, z = f, -r, (idx % 2 == 0 and r or -r)
    elseif face == 4 then x, y, z = (idx % 2 == 0 and r or -r), f, r
    else x, y, z = (idx % 2 == 0 and r or -r), f, -r end
    return Vector3.new(x, h + y, z)
end

-- 17. Rounded Cube
Shapes['Rounded Cube'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local phi = math.acos(clamp(1 - 2 * (i / safeN), -1, 1))
    local theta = i * math.pi * (3 - sqrt(5)) + t * sp
    local x = cos(theta) * sin(phi)
    local y = cos(phi)
    local z = sin(theta) * sin(phi)
    local f = 1 + 0.2 * (x^4 + y^4 + z^4)
    return Vector3.new((x * r) / f, h + (y * r) / f, (z * r) / f)
end

-- 18. Torus
Shapes.Torus = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local majorN = clamp(math.floor(sqrt(safeN) * 1.5), 3, safeN)
    local minorN = math.max(math.floor(safeN / majorN), 1)
    local u = (((i - 1) % majorN) / majorN) * TAU + t * sp
    local v = (math.floor((i - 1) / majorN) / minorN) * TAU
    local R = r
    local tubeR = r * 0.35
    local x = (R + tubeR * cos(v)) * cos(u)
    local y = tubeR * sin(v)
    local z = (R + tubeR * cos(v)) * sin(u)
    return Vector3.new(x, h + y, z)
end

-- 19. Torus Knot
Shapes['Torus Knot'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local p, q = 2, 3
    local phi = ((i - 1) / safeN) * TAU * q + t * sp
    local r_knot = r * (0.8 + 0.2 * cos(p * phi / q))
    local x = r_knot * cos(phi)
    local y = r_knot * sin(phi) * 0.5
    local z = r_knot * sin(p * phi / q)
    return Vector3.new(x, h + y, z)
end

-- 20. MÃ¶bius Strip
Shapes['MÃ¶bius Strip'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local u = ((i - 1) / safeN) * TAU + t * sp
    local v = ((i % 2 == 0) and 1 or -1) * (r * 0.3)
    local x = (r + v * cos(u / 2)) * cos(u)
    local y = v * sin(u / 2)
    local z = (r + v * cos(u / 2)) * sin(u)
    return Vector3.new(x, h + y, z)
end

-- 21. Saturn
Shapes.Saturn = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local coreN = clamp(math.floor(safeN * 0.5), 4, 20)
    coreN = math.min(coreN, safeN)
    local ringN = math.max(safeN - coreN, 1)
    local spin = t * sp
    if i <= coreN then
        local phi = math.acos(clamp(1 - 2 * (i / coreN), -1, 1))
        local theta = i * math.pi * (3 - sqrt(5)) + spin
        local cr = r * 0.4
        return Vector3.new(cos(theta) * sin(phi) * cr, cos(phi) * cr + h, sin(theta) * sin(phi) * cr)
    else
        local idx = i - coreN
        local a = (idx / ringN) * TAU + spin * 1.5
        local rr = r * 1.4
        return Vector3.new(cos(a) * rr, h, sin(a) * rr)
    end
end

-- 22. Ice Cube
Shapes['Ice Cube'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local sideN = math.max(math.floor(safeN / 8), 1)
    local face = math.floor((i - 1) / sideN) % 8
    local idx = (i - 1) % sideN
    local f = (idx / sideN) * r - (r * 0.5)
    local hr = r * 0.6
    local x, y, z = 0, 0, 0
    if face == 0 then x, y, z = hr, f, hr
    elseif face == 1 then x, y, z = -hr, f, hr
    elseif face == 2 then x, y, z = hr, f, -hr
    elseif face == 3 then x, y, z = -hr, f, -hr
    elseif face == 4 then x, y, z = f, hr, hr
    elseif face == 5 then x, y, z = f, hr, -hr
    elseif face == 6 then x, y, z = f, -hr, hr
    else x, y, z = f, -hr, -hr end
    return Vector3.new(x, h + y, z)
end

-- 23. Rain Effect (à¸à¸à¸à¸à¹à¸£à¹à¸§)
Shapes.Rain = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    local angle = ((i - 1) / safeN) * math.pi * 2 + t * sp * 12
    local fallSpeed = 40 + (i % 5) * 3
    local yPos = (h + r * 1.5) - ((t * fallSpeed + i * 0.1) % (r * 3))
    local radiusOffset = r * (0.8 + 0.2 * math.sin(t * 15 + i * 3))
    local x = math.cos(angle) * radiusOffset
    local z = math.sin(angle) * radiusOffset
    return Vector3.new(x, yPos, z)
end

-- 24. Sword Shape (à¸à¸²à¸à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸ - à¹à¸à¸à¸à¸µà¹à¸à¸¸à¸à¸à¹à¸­à¸à¸à¸²à¸£)
Shapes.Sword = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸§à¸²à¸¡à¸¢à¸²à¸§à¸à¸²à¸ (à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸)
    local bladeLength = r * 3
    local bladeWidth = r * 0.2
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸ (à¹à¸£à¸´à¹à¸¡à¸à¸²à¸ 0 à¸à¸¶à¹à¸à¹à¸)
    local yPos = h + ((i - 1) / safeN) * bladeLength
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§ (à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡)
    local angle = ((i - 1) / safeN) * TAU + t * sp * 2
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * bladeWidth
    local z = math.sin(angle) * bladeWidth
    
    return Vector3.new(x, yPos, z)
end

-- 25. Sword v2 (à¸à¸²à¸à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸à¹à¸à¸à¹à¸à¸¥à¸µà¸¢à¸§)
Shapes['Sword v2'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸§à¸²à¸¡à¸¢à¸²à¸§à¸à¸²à¸
    local bladeLength = r * 3
    local bladeWidth = r * 0.2
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸
    local yPos = h + ((i - 1) / safeN) * bladeLength
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸: à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¹à¸à¸¥à¸µà¸¢à¸§ (à¹à¸à¸´à¹à¸¡à¸¡à¸¸à¸¡à¸à¸²à¸¡à¸à¸§à¸²à¸¡à¸ªà¸¹à¸)
    local angle = ((i - 1) / safeN) * TAU * 3 + t * sp * 2
    
    -- à¸£à¸±à¸¨à¸¡à¸µ: à¸à¸¢à¸²à¸¢à¸à¸§à¹à¸²à¸à¸à¸¶à¹à¸à¸à¸²à¸¡à¸à¸§à¸²à¸¡à¸ªà¸¹à¸ (à¸à¸²à¸à¸à¸°à¸à¸²à¸à¸­à¸­à¸à¸à¹à¸²à¸à¸à¸)
    local radius = bladeWidth * (1 + ((i - 1) / safeN) * 2)
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, yPos, z)
end

-- 26. Sword v3 (à¸à¸²à¸à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸à¹à¸¥à¸°à¹à¸­à¸µà¸¢à¸à¹à¸à¹à¸²à¸«à¸²à¸à¸±à¸§)
Shapes['Sword v3'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸§à¸²à¸¡à¸¢à¸²à¸§à¸à¸²à¸
    local bladeLength = r * 2.5
    local bladeWidth = r * 0.25
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¹à¸à¸¥à¹à¸à¸²à¸à¸à¸·à¹à¸
    local yPos = h + ((i - 1) / safeN) * bladeLength
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§
    local angle = ((i - 1) / safeN) * TAU + t * sp * 1.5
    
    -- à¸£à¸±à¸¨à¸¡à¸µ: à¹à¸à¸à¸¥à¸à¹à¸¡à¸·à¹à¸­à¸ªà¸¹à¸à¸à¸¶à¹à¸ (à¸à¸²à¸à¸à¸°à¹à¸­à¸µà¸¢à¸à¹à¸à¹à¸²à¸«à¸²à¸à¸±à¸§)
    local radius = bladeWidth * (1 - ((i - 1) / safeN) * 0.8)
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, yPos, z)
end

-- Energy Shape (à¸¥à¸¹à¸à¸à¸­à¸¥à¸à¸¥à¸±à¸à¸à¸²à¸à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§)
Shapes.Energy = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¹à¸à¸à¸¥à¸¹à¸à¸à¸­à¸¥à¸à¸¥à¸±à¸à¸à¸²à¸ (Sphere)
    local phi = math.acos(math.clamp(1 - 2 * (i / safeN), -1, 1))
    local theta = i * math.pi * (3 - math.sqrt(5)) + t * sp * 2
    local currentR = r * (0.5 + 0.3 * math.sin(t * 0.5 + i * 0.1))
    
    local x = math.cos(theta) * math.sin(phi) * currentR
    local y = math.cos(phi) * currentR + h
    local z = math.sin(theta) * math.sin(phi) * currentR
    
    return Vector3.new(x, y, z)
end

-- 28. Tsunami Shape (à¸à¸¥à¸·à¹à¸à¸ªà¸¶à¸à¸²à¸¡à¸´)
Shapes.Tsunami = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸§à¸²à¸¡à¸ªà¸¹à¸à¸à¸­à¸à¸à¸¥à¸·à¹à¸
    local waveHeight = r * 1.5
    local waveWidth = r * 0.8
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¸à¸¥à¸·à¹à¸à¸à¸°à¸à¸±à¸à¸à¸¶à¹à¸à¸à¸²à¸à¸à¸·à¹à¸à¹à¸¥à¹à¸§à¸¥à¸à¸¥à¸
    local phase = ((i - 1) / safeN) * TAU
    local waveY = h + (waveHeight * 0.5) + (math.sin(phase + t * sp * 2) * waveHeight * 0.5)
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z: à¸à¸£à¸°à¸à¸²à¸¢à¹à¸à¹à¸à¹à¸à¸§à¸à¸¥à¸·à¹à¸
    local angle = phase + t * sp * 1.5
    local radius = waveWidth * (1 + 0.3 * math.sin(t * sp + i * 0.2))
    
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, waveY, z)
end

-- 29. Grid Shape (à¹à¸§à¸­à¸£à¹à¸à¸±à¸à¸£à¸°à¸à¸²à¸ 2D)
Shapes.Grid = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸à¸²à¸à¸à¸²à¸£à¸²à¸ 2D (x à¹à¸¥à¸° z)
    local gridSize = math.floor(math.sqrt(safeN)) + 1
    if gridSize < 2 then gridSize = 2 end
    
    local cellSize = (r * 2) / gridSize
    local totalCells = gridSize * gridSize
    
    local idx = (i - 1) % totalCells
    
    local xIndex = idx % gridSize
    local zIndex = math.floor(idx / gridSize)
    
    local xPos = (xIndex - (gridSize - 1) / 2) * cellSize
    local yPos = h
    local zPos = (zIndex - (gridSize - 1) / 2) * cellSize
    
    -- à¸«à¸¡à¸¸à¸à¸£à¸°à¸à¸²à¸
    local rotAngle = t * sp * 1.5
    local cosA = math.cos(rotAngle)
    local sinA = math.sin(rotAngle)
    
    local rotatedX = xPos * cosA - zPos * sinA
    local rotatedZ = xPos * sinA + zPos * cosA
    
    return Vector3.new(rotatedX, yPos, rotatedZ)
end

-- 30. Robot Shape (à¸«à¸¸à¹à¸à¸¢à¸à¸à¹à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§)
Shapes.Robot = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸ªà¹à¸§à¸à¸à¸£à¸°à¸à¸­à¸à¸à¸­à¸à¸«à¸¸à¹à¸à¸¢à¸à¸à¹
    local headCount = math.floor(safeN * 0.15)  -- à¸«à¸±à¸§ 15%
    local bodyCount = math.floor(safeN * 0.25)  -- à¸¥à¸³à¸à¸±à¸§ 25%
    local armCount = math.floor(safeN * 0.30)   -- à¹à¸à¸ 30%
    local legCount = safeN - headCount - bodyCount - armCount  -- à¸à¸² 30%
    
    -- à¸à¸£à¸±à¸à¸à¸³à¸à¸§à¸à¹à¸«à¹à¸à¸¹à¸à¸à¹à¸­à¸
    if legCount < 0 then legCount = 0 end
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§
    local angle = ((i - 1) / safeN) * TAU + t * sp * 2
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¸à¸²à¸¡à¸ªà¹à¸§à¸à¸à¹à¸²à¸à¹
    if i <= headCount then
        -- à¸«à¸±à¸§ (à¸ªà¹à¸§à¸à¸à¸)
        local headY = h + r * 0.8
        local headRadius = r * 0.15
        local x = math.cos(angle) * headRadius
        local z = math.sin(angle) * headRadius
        return Vector3.new(x, headY, z)
        
    elseif i <= headCount + bodyCount then
        -- à¸¥à¸³à¸à¸±à¸§ (à¸ªà¹à¸§à¸à¸à¸¥à¸²à¸)
        local bodyY = h
        local bodyRadius = r * 0.3
        local idx = i - headCount
        local yOffset = (idx / bodyCount) * r * 0.4 - r * 0.2
        local x = math.cos(angle) * bodyRadius
        local z = math.sin(angle) * bodyRadius
        return Vector3.new(x, h + yOffset, z)
        
    elseif i <= headCount + bodyCount + armCount then
        -- à¹à¸à¸ (à¸à¹à¸²à¸à¸à¹à¸²à¸)
        local idx = i - headCount - bodyCount
        local armAngle = angle + math.pi / 2  -- à¹à¸à¸à¸à¸°à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¹à¸²à¸
        local armY = h + (idx / armCount) * r * 0.4 - r * 0.2
        local armRadius = r * 0.35
        local x = math.cos(armAngle) * armRadius
        local z = math.sin(armAngle) * armRadius
        return Vector3.new(x, armY, z)
        
    else
        -- à¸à¸² (à¸ªà¹à¸§à¸à¸¥à¹à¸²à¸)
        local idx = i - headCount - bodyCount - armCount
        local legAngle = angle + math.pi / 4  -- à¸à¸²à¸à¸°à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸¥à¹à¸²à¸
        local legY = h - r * 0.5 + (idx / legCount) * r * 0.3
        local legRadius = r * 0.25
        local x = math.cos(legAngle) * legRadius
        local z = math.sin(legAngle) * legRadius
        return Vector3.new(x, legY, z)
    end
end

-- 31. Explosion Shape (à¸£à¸°à¹à¸à¸´à¸à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡)
Shapes.Explosion = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸§à¸à¸à¸¥à¸¡à¸£à¸°à¹à¸à¸´à¸
    local angle = ((i - 1) / safeN) * TAU + t * sp * 2
    local pulse = 1 + 0.4 * math.sin(t * sp * 3 + i * 0.1)
    local currentR = r * pulse * (0.5 + 0.5 * ((i - 1) / safeN))
    
    local x = math.cos(angle) * currentR
    local y = h + math.sin(t * sp * 2 + i * 0.2) * 2
    local z = math.sin(angle) * currentR
    
    return Vector3.new(x, y, z)
end

-- Atom (à¸­à¸°à¸à¸­à¸¡ - à¸à¸´à¸§à¹à¸à¸¥à¸µà¸¢à¸ª + à¸­à¸´à¹à¸¥à¹à¸à¸à¸£à¸­à¸à¹à¸à¸à¸£)
Shapes.Atom = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¹à¸à¹à¸à¹à¸à¹à¸à¸à¸´à¸§à¹à¸à¸¥à¸µà¸¢à¸ª (30%) à¹à¸¥à¸°à¸­à¸´à¹à¸¥à¹à¸à¸à¸£à¸­à¸ (70%)
    local nucleusCount = math.floor(safeN * 0.3)
    local electronCount = safeN - nucleusCount
    
    if i <= nucleusCount then
        -- à¸à¸´à¸§à¹à¸à¸¥à¸µà¸¢à¸ª (à¸£à¸§à¸¡à¸à¸±à¸à¸à¸£à¸à¸à¸¥à¸²à¸)
        local phi = math.acos(1 - 2 * (i / nucleusCount))
        local theta = i * math.pi * (3 - math.sqrt(5)) + t * sp * 0.5
        local radius = r * 0.15
        
        local x = math.cos(theta) * math.sin(phi) * radius
        local y = math.cos(phi) * radius + h
        local z = math.sin(theta) * math.sin(phi) * radius
        
        return Vector3.new(x, y, z)
    else
        -- à¸­à¸´à¹à¸¥à¹à¸à¸à¸£à¸­à¸ (à¹à¸à¸à¸£à¹à¸à¹à¸à¸§à¸à¹à¸«à¸§à¸)
        local idx = i - nucleusCount
        local electronRing = idx % 3  -- 3 à¸§à¸à¹à¸à¸à¸£
        local ringAngle = (electronRing / 3) * TAU
        
        local posInRing = math.floor(idx / 3)
        local countInRing = math.max(math.floor(electronCount / 3), 1)
        
        local angle = (posInRing / countInRing) * TAU + t * sp * (2 + electronRing * 0.5) + ringAngle
        
        -- à¹à¸à¹à¸¥à¸°à¸§à¸à¸¡à¸µà¸£à¸±à¸¨à¸¡à¸µà¸à¹à¸²à¸à¸à¸±à¸
        local ringRadius = r * (0.4 + electronRing * 0.2)
        
        -- à¸§à¸à¹à¸à¸à¸£à¹à¸­à¸µà¸¢à¸ (à¹à¸à¸·à¹à¸­à¹à¸«à¹à¸à¸¹à¹à¸«à¸¡à¸·à¸­à¸à¸­à¸°à¸à¸­à¸¡à¸à¸£à¸´à¸)
        local tiltX = math.sin(ringAngle) * ringRadius * 0.1
        local tiltY = math.cos(ringAngle) * ringRadius * 0.1
        
        local x = math.cos(angle) * ringRadius
        local z = math.sin(angle) * ringRadius
        local y = h + tiltY + math.sin(t * sp * 0.5 + idx) * 0.5
        
        return Vector3.new(x, y, z)
    end
end

-- Meteor Shower (à¸à¸à¸à¸²à¸§à¸à¸ - à¸à¸à¸¥à¸à¸¡à¸²à¹à¸à¹à¸à¹à¸ªà¹à¸à¸à¸£à¸)
Shapes['Meteor Shower'] = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¹à¸à¹à¸¥à¸°à¸à¸²à¸§à¸à¸à¸¡à¸µà¸à¸³à¹à¸«à¸à¹à¸à¹à¸¥à¸°à¸à¸§à¸²à¸¡à¹à¸£à¹à¸§à¸à¹à¸²à¸à¸à¸±à¸
    local seed = i * 137.508
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¹à¸£à¸´à¹à¸¡à¸à¹à¸ (à¸à¸à¸à¹à¸²)
    local startX = math.sin(seed * 0.1) * r * 2
    local startZ = math.cos(seed * 0.05) * r * 2
    local startY = h + 100 + math.abs(math.sin(seed * 0.03)) * 50
    
    -- à¸à¸§à¸²à¸¡à¹à¸£à¹à¸§à¹à¸¥à¸°à¸à¸´à¸¨à¸à¸²à¸
    local speed = 20 + math.sin(seed * 0.5) * 10
    local dirX = math.cos(seed * 0.1) * speed
    local dirZ = math.sin(seed * 0.05) * speed
    
    -- à¸à¸³à¹à¸«à¸à¹à¸à¸à¸²à¸¡à¹à¸§à¸¥à¸² (à¸à¸à¸¥à¸à¸¡à¸²à¹à¸£à¸·à¹à¸­à¸¢à¹)
    local time = t * sp * 2
    local fall = (time * speed + i * 10) % 200  -- à¸à¸à¹à¸¥à¹à¸§à¸§à¸à¸à¸¥à¸±à¸
    
    local x = startX + dirX * (fall / 200)
    local z = startZ + dirZ * (fall / 200)
    local y = startY - fall
    
    -- à¸à¸³à¹à¸«à¹à¸à¸¹à¹à¸«à¸¡à¸·à¸­à¸à¸¡à¸µà¸«à¸²à¸ (à¹à¸ªà¹à¸à¸«à¸¥à¸±à¸)
    local trail = math.max(0, 1 - (fall / 200)) * 5
    
    return Vector3.new(x, y + trail, z)
end

-- 32. Flower Shape (à¸à¸­à¸à¹à¸¡à¹)
Shapes.Flower = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸ªà¹à¸§à¸à¸à¸£à¸°à¸à¸­à¸à¸à¸­à¸à¸à¸­à¸à¹à¸¡à¹
    local petalCount = math.floor(safeN * 0.7)  -- à¸à¸¥à¸µà¸à¸à¸­à¸ 70%
    local centerCount = safeN - petalCount      -- à¸à¸¥à¸²à¸à¸à¸­à¸ 30%
    
    if i <= petalCount then
        -- à¸à¸¥à¸µà¸à¸à¸­à¸ (à¸«à¸¡à¸¸à¸à¹à¸à¹à¸à¸§à¸à¸à¸¥à¸¡)
        local angle = ((i - 1) / petalCount) * TAU + t * sp * 1.5
        local petalRadius = r * 0.8
        local petalLength = r * 0.6
        
        -- à¸à¸¥à¸µà¸à¸à¸­à¸à¸à¸°à¸à¸²à¸à¸­à¸­à¸
        local bloom = 1 + 0.2 * math.sin(t * sp * 2 + i * 0.3)
        local x = math.cos(angle) * petalRadius * bloom
        local z = math.sin(angle) * petalRadius * bloom
        local y = h + math.sin(angle * 2 + t * sp) * (r * 0.2)
        
        return Vector3.new(x, y, z)
    else
        -- à¸à¸¥à¸²à¸à¸à¸­à¸ (à¸à¸£à¸à¸à¸¥à¸²à¸)
        local idx = i - petalCount
        local centerAngle = idx * math.pi * (3 - math.sqrt(5)) + t * sp
        local centerRadius = r * 0.2
        local x = math.cos(centerAngle) * centerRadius
        local y = math.cos(centerAngle * 2) * centerRadius * 0.5 + h
        local z = math.sin(centerAngle) * centerRadius
        
        return Vector3.new(x, y, z)
    end
end

-- 32. Flower Shape (à¸à¸­à¸à¹à¸¡à¹à¸à¸²à¸)
Shapes.Flower = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local petalCount = math.floor(safeN * 0.7)
    local centerCount = safeN - petalCount
    
    -- à¸à¸­à¸à¹à¸¡à¹à¸à¸²à¸à¸à¸²à¸¡à¹à¸§à¸¥à¸²
    local bloom = 1 + 0.3 * math.sin(t * sp * 2)
    
    if i <= petalCount then
        local angle = ((i - 1) / petalCount) * TAU + t * sp * 1.5
        local petalRadius = r * 0.8 * bloom
        local petalLength = r * 0.5
        
        -- à¸à¸¥à¸µà¸à¸à¸­à¸à¸à¸°à¸à¸¢à¸±à¸à¸à¸¶à¹à¸à¸¥à¸
        local wave = math.sin(t * sp * 3 + i * 0.5) * (r * 0.2)
        local x = math.cos(angle) * petalRadius
        local z = math.sin(angle) * petalRadius
        local y = h + wave
        
        return Vector3.new(x, y, z)
    else
        local idx = i - petalCount
        local centerAngle = idx * math.pi * (3 - math.sqrt(5)) + t * sp
        local centerRadius = r * 0.2 * bloom
        local x = math.cos(centerAngle) * centerRadius
        local y = math.cos(centerAngle * 2) * centerRadius * 0.5 + h
        local z = math.sin(centerAngle) * centerRadius
        
        return Vector3.new(x, y, z)
    end
end

-- 34. Divine Light Shape (à¹à¸ªà¸à¸à¸£à¸°à¹à¸à¹à¸²)
Shapes.DivineLight = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¹à¸ªà¸à¸à¸°à¸ªà¹à¸­à¸à¸à¸²à¸à¸à¹à¸²à¸à¸à¸à¸¥à¸à¸¡à¸²
    local heightRange = r * 3  -- à¸à¸§à¸²à¸¡à¸ªà¸¹à¸à¸à¸­à¸à¸¥à¸³à¹à¸ªà¸
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ Y: à¸à¸²à¸à¸à¸à¸¥à¸à¸¥à¹à¸²à¸
    local yPos = h + heightRange - ((i - 1) / safeN) * heightRange
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§ (à¹à¸ªà¸à¸à¸°à¸«à¸¡à¸¸à¸à¸£à¸­à¸)
    local angle = ((i - 1) / safeN) * TAU + t * sp * 1.5
    
    -- à¸£à¸±à¸¨à¸¡à¸µ: à¹à¸ªà¸à¸à¸°à¸à¸§à¹à¸²à¸à¸à¸µà¹à¸à¹à¸²à¸à¸à¸à¹à¸¥à¸°à¹à¸à¸à¸¥à¸à¸à¹à¸²à¸à¸¥à¹à¸²à¸
    local radius = r * (0.8 - ((i - 1) / safeN) * 0.6)
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, yPos, z)
end

-- 35. Crown Shape (à¸¡à¸à¸à¸¸à¸)
Shapes.Crown = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸ªà¹à¸§à¸à¸à¸£à¸°à¸à¸­à¸à¸à¸­à¸à¸¡à¸à¸à¸¸à¸
    local baseCount = math.floor(safeN * 0.4)   -- à¸à¸²à¸ 40%
    local spikeCount = math.floor(safeN * 0.4)  -- à¸¢à¸­à¸ 40%
    local jewelCount = safeN - baseCount - spikeCount  -- à¹à¸à¸à¸£ 20%
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§
    local angle = ((i - 1) / safeN) * TAU + t * sp * 2
    
    if i <= baseCount then
        -- à¸à¸²à¸à¸à¸­à¸à¸¡à¸à¸à¸¸à¸ (à¸§à¸à¹à¸«à¸§à¸)
        local idx = i - 1
        local baseRadius = r * 0.8
        local baseHeight = h + r * 0.2
        local x = math.cos(angle) * baseRadius
        local z = math.sin(angle) * baseRadius
        return Vector3.new(x, baseHeight, z)
        
    elseif i <= baseCount + spikeCount then
        -- à¸¢à¸­à¸à¸à¸­à¸à¸¡à¸à¸à¸¸à¸ (à¸ªà¸²à¸¡à¹à¸«à¸¥à¸µà¹à¸¢à¸¡)
        local idx = i - baseCount
        local spikeHeight = (idx / spikeCount) * r * 0.8
        local spikeRadius = r * 0.8 * (1 - (idx / spikeCount) * 0.7)
        local x = math.cos(angle) * spikeRadius
        local z = math.sin(angle) * spikeRadius
        local y = h + r * 0.2 + spikeHeight
        return Vector3.new(x, y, z)
        
    else
        -- à¹à¸à¸à¸£à¸à¸£à¸°à¸à¸±à¸ (à¸à¸¸à¸à¸à¸µà¹à¸¢à¸­à¸)
        local idx = i - baseCount - spikeCount
        local jewelAngle = angle + math.pi / 4
        local jewelRadius = r * 0.3
        local jewelY = h + r * 1.0 + math.sin(t * sp * 3 + idx) * r * 0.1
        local x = math.cos(jewelAngle) * jewelRadius
        local z = math.sin(jewelAngle) * jewelRadius
        return Vector3.new(x, jewelY, z)
    end
end

-- 36. Hexagram Shape (à¸à¸²à¸§à¸«à¸à¹à¸à¸ 3D)
Shapes.Hexagram = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local pointsPerTriangle = 3
    local totalPoints = 6
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸ 3 à¸¡à¸´à¸à¸´
    local rotX = math.sin(t * sp * 0.5) * (r * 0.3)
    local rotY = t * sp * 1.5
    
    local pointIndex = (i - 1) % totalPoints
    local isFirstTriangle = pointIndex < pointsPerTriangle
    
    if isFirstTriangle then
        local idx = pointIndex
        local a = idx * (TAU / 3) + rotY
        local x = math.cos(a) * r
        local z = math.sin(a) * r
        local y = h + rotX * math.sin(a)
        return Vector3.new(x, y, z)
    else
        local idx = pointIndex - pointsPerTriangle
        local a = idx * (TAU / 3) + math.pi / 3 + rotY
        local x = math.cos(a) * r
        local z = math.sin(a) * r
        local y = h - rotX * math.sin(a)
        return Vector3.new(x, y, z)
    end
end

-- 37. CubeOrbit Shape (à¸«à¸¡à¸¸à¸à¹à¸¥à¸°à¹à¸à¸à¸£à¹à¸£à¹à¸§)
Shapes.CubeOrbit = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    local cubeSize = math.floor(math.cbrt(safeN)) + 1
    if cubeSize < 2 then cubeSize = 2 end
    if cubeSize > 6 then cubeSize = 6 end
    
    local totalPoints = cubeSize * cubeSize * cubeSize
    local idx = (i - 1) % totalPoints
    
    local xIdx = idx % cubeSize
    local yIdx = math.floor(idx / cubeSize) % cubeSize
    local zIdx = math.floor(idx / (cubeSize * cubeSize))
    
    local half = (cubeSize - 1) / 2
    local localX = (xIdx - half) / cubeSize
    local localY = (yIdx - half) / cubeSize
    local localZ = (zIdx - half) / cubeSize
    
    local cubeRadius = r * 0.6
    
    -- à¸«à¸¡à¸¸à¸à¸£à¸­à¸à¸à¸±à¸§à¹à¸­à¸à¹à¸£à¹à¸§à¸à¸¶à¹à¸ (Spin)
    local spinAngle = t * sp * 6
    local cosSpin = math.cos(spinAngle)
    local sinSpin = math.sin(spinAngle)
    
    local rotatedX = localX * cosSpin - localZ * sinSpin
    local rotatedZ = localX * sinSpin + localZ * cosSpin
    local rotatedY = localY
    
    local cubeX = rotatedX * cubeRadius
    local cubeY = rotatedY * cubeRadius + h
    local cubeZ = rotatedZ * cubeRadius
    
    -- à¹à¸à¸à¸£à¸£à¸­à¸à¸à¸¹à¹à¹à¸¥à¹à¸à¹à¸£à¹à¸§à¸à¸¶à¹à¸ (Orbit)
    local orbitAngle = t * sp * 3
    local orbitRadius = r * 1.2
    local orbitX = math.cos(orbitAngle) * orbitRadius
    local orbitZ = math.sin(orbitAngle) * orbitRadius
    
    local finalX = cubeX + orbitX
    local finalY = cubeY
    local finalZ = cubeZ + orbitZ
    
    return Vector3.new(finalX, finalY, finalZ)
end

-- 38. Angel Shape (à¸à¸µà¸à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¸±à¸§ + à¸§à¸à¹à¸«à¸§à¸à¸«à¸¡à¸¸à¸)
Shapes.Angel = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸³à¸à¸§à¸à¸à¸´à¹à¸à¸ªà¹à¸§à¸: à¸§à¸à¹à¸«à¸§à¸ 10 à¸­à¸±à¸, à¸à¸µà¸à¸à¹à¸²à¸¢ 10 à¸­à¸±à¸, à¸à¸µà¸à¸à¸§à¸² 10 à¸­à¸±à¸
    local haloCount = 10
    local leftWingCount = 10
    local rightWingCount = 10
    local totalCount = haloCount + leftWingCount + rightWingCount
    
    -- à¸à¹à¸²à¸¡à¸µ Sparkler à¸à¹à¸­à¸¢à¸à¸§à¹à¸² 30 à¸­à¸±à¸ à¹à¸«à¹à¸à¸£à¸±à¸à¸à¸³à¸à¸§à¸à¹à¸«à¹à¸à¸­à¸à¸µ
    if safeN < totalCount then
        local ratio = safeN / totalCount
        haloCount = math.max(1, math.floor(haloCount * ratio))
        leftWingCount = math.max(1, math.floor(leftWingCount * ratio))
        rightWingCount = math.max(1, math.floor(rightWingCount * ratio))
        totalCount = haloCount + leftWingCount + rightWingCount
    end
    
    -- à¸à¸²à¸£à¸à¸£à¸°à¸à¸·à¸­à¸à¸µà¸ (à¸à¸¢à¸±à¸à¸à¸¶à¹à¸à¸¥à¸)
    local flap = math.sin(t * sp * 2)
    
    if i <= haloCount then
        -- à¸§à¸à¹à¸«à¸§à¸à¸à¸à¸«à¸±à¸§ (à¸«à¸¡à¸¸à¸)
        local idx = i - 1
        local haloAngle = (idx / haloCount) * TAU + t * sp * 3  -- à¸«à¸¡à¸¸à¸
        local haloRadius = r * 0.6
        local haloY = h + r * 1.3
        local glow = 1 + 0.2 * math.sin(t * sp * 3 + idx)
        local x = math.cos(haloAngle) * haloRadius * glow
        local z = math.sin(haloAngle) * haloRadius * glow
        return Vector3.new(x, haloY, z)
        
    elseif i <= haloCount + leftWingCount then
        -- à¸à¸µà¸à¸à¹à¸²à¸¢ (à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¸±à¸§ à¹à¸¡à¹à¸«à¸¡à¸¸à¸)
        local idx = i - haloCount
        local wingProgress = idx / leftWingCount
        
        -- à¸à¸µà¸à¸à¹à¸²à¸¢à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¸±à¸§ (X à¸à¸´à¸à¸¥à¸)
        local wingY = h + math.sin(wingProgress * math.pi * 0.8) * r * 0.8 + flap * r * 0.3 * wingProgress
        local wingX = -r * 0.7
        local wingZ = (wingProgress - 0.5) * r * 1.2
        
        return Vector3.new(wingX, wingY, wingZ)
        
    else
        -- à¸à¸µà¸à¸à¸§à¸² (à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¸±à¸§ à¹à¸¡à¹à¸«à¸¡à¸¸à¸)
        local idx = i - haloCount - leftWingCount
        local wingProgress = idx / rightWingCount
        
        -- à¸à¸µà¸à¸à¸§à¸²à¸­à¸¢à¸¹à¹à¸à¹à¸²à¸à¸à¸±à¸§ (X à¹à¸à¹à¸à¸à¸§à¸)
        local wingY = h + math.sin(wingProgress * math.pi * 0.8) * r * 0.8 + flap * r * 0.3 * wingProgress
        local wingX = r * 0.7
        local wingZ = (wingProgress - 0.5) * r * 1.2
        
        return Vector3.new(wingX, wingY, wingZ)
    end
end

-- 39. RingFloor Shape (à¸§à¸à¹à¸«à¸§à¸à¸à¸à¸à¸·à¹à¸)
Shapes.RingFloor = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸§à¸à¹à¸«à¸§à¸à¸à¸°à¸­à¸¢à¸¹à¹à¸à¸µà¹à¸£à¸°à¸à¸±à¸à¸à¸·à¹à¸ (h = 0)
    local floorY = 0
    
    -- à¸¡à¸¸à¸¡à¸«à¸¡à¸¸à¸
    local angle = ((i - 1) / safeN) * TAU + t * sp * 3
    
    -- à¸£à¸±à¸¨à¸¡à¸µà¸à¸­à¸à¸§à¸à¹à¸«à¸§à¸
    local radius = r * 0.9
    
    -- à¸à¸³à¹à¸«à¸à¹à¸ X, Z
    local x = math.cos(angle) * radius
    local z = math.sin(angle) * radius
    
    return Vector3.new(x, floorY, z)
end

-- 31. Explosion Shape (à¸£à¸°à¹à¸à¸´à¸à¸ªà¸µà¹à¸à¸)
Shapes.Explosionv2 = function(i, n, t, r, h, sp)
    local safeN = math.max(n, 1)
    
    -- à¸à¸²à¸£à¸£à¸°à¹à¸à¸´à¸à¸à¸µà¹à¸£à¸¸à¸à¹à¸£à¸
    local pulse = 1 + 0.6 * math.sin(t * sp * 4 + i * 0.3)
    local expand = 1 + 0.3 * math.sin(t * sp * 2)
    
    local phi = math.acos(math.clamp(1 - 2 * (i / safeN), -1, 1))
    local theta = i * math.pi * (3 - math.sqrt(5)) + t * sp * 3
    local currentR = r * pulse * expand * (0.2 + 0.8 * ((i - 1) / safeN))
    
    local x = math.cos(theta) * math.sin(phi) * currentR
    local y = math.cos(phi) * currentR + h
    local z = math.sin(theta) * math.sin(phi) * currentR
    
    -- à¹à¸à¸¥à¸µà¹à¸¢à¸à¸ªà¸µ Sparkler à¹à¸à¹à¸à¸ªà¸µà¹à¸à¸ (à¸à¹à¸²à¸à¹à¸­à¸à¸à¸²à¸£)
    -- à¸à¹à¸­à¸à¹à¸à¹à¸à¸´à¹à¸¡à¹à¸à¹à¸à¹à¸à¸ªà¹à¸§à¸à¸­à¸·à¹à¸
    
    return Vector3.new(x, y, z)
end

local function GetShapeOffset(index, total, t, cfg)
    local fn = Shapes[cfg.CurrentShape]
    if fn then
        local success, result = pcall(fn, index, total, t, cfg.Radius, cfg.Height, cfg.Speed)
        if success and result then
            return result
        end
    end
    local safeTotal = math.max(total, 1)
    local a = ((index - 1) / safeTotal) * TAU + t * cfg.Speed
    return Vector3.new(cos(a) * cfg.Radius, cfg.Height, sin(a) * cfg.Radius)
end

RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    local targetRoot = char and char:FindFirstChild('HumanoidRootPart')
    if not targetRoot then return end

    local t = tick()
    local prediction = targetRoot.AssemblyLinearVelocity * 0.12
    local rot = targetRoot.CFrame.Rotation

    for i = #activeSparklers, 1, -1 do
        local obj = activeSparklers[i]
        if obj and obj.Parent then
            local main = obj:IsA('BasePart') and obj or obj.PrimaryPart
            local bp = main and main:FindFirstChild('ToyBodyPos')
            local bg = main and main:FindFirstChild('ToyBodyGyro')

            if bp and bg then
                local offset = GetShapeOffset(i, #activeSparklers, t, sparklerConfig)
                bp.Position = targetRoot.Position + prediction + (rot * offset)
                bg.CFrame = CFrame.new(main.Position, targetRoot.Position + prediction)
            end
        else
            table.remove(activeSparklers, i)
        end
    end
end)

-- ==================== AIMBOT & TRIGGERBOT TAB ====================
local AimTab = Window:CreateTab("Combat (Aim)", "crosshair")
local AimSection = AimTab:CreateSection("Aim Options")

local aimbotEnabled = false
local triggerbotEnabled = false

AimTab:CreateToggle({
    Name = "Aimbot",
    CurrentValue = false,
    Flag = "Toggle_Aimbot",
    Callback = function(Value)
        aimbotEnabled = Value
    end,
})

AimTab:CreateToggle({
    Name = "Triggerbot",
    CurrentValue = false,
    Flag = "Toggle_Triggerbot",
    Callback = function(Value)
        triggerbotEnabled = Value
    end,
})

local function GetClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
            if humanoid and humanoid.Health > 0 then
                local pos, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
                if onScreen then
                    local magnitude = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)).Magnitude
                    if magnitude < shortestDistance then
                        shortestDistance = magnitude
                        closestPlayer = player.Character
                    end
                end
            end
        end
    end
    return closestPlayer
end

RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local target = GetClosestPlayer()
        if target and target:FindFirstChild("HumanoidRootPart") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.HumanoidRootPart.Position)
        end
    end

    if triggerbotEnabled then
        local mouseTarget = LocalPlayer:GetMouse().Target
        if mouseTarget and mouseTarget.Parent then
            local character = mouseTarget.Parent
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid and Players:GetPlayerFromCharacter(character) and Players:GetPlayerFromCharacter(character) ~= LocalPlayer then
                if humanoid.Health > 0 then                    mouse1click()
                end
            end
        end
    end
end)