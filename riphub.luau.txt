local ValidKeys = {
    "RIP-KEY-9921",
    "RIP-KEY-4412",
    "RIP-KEY-8853",
    "RIP-KEY-1204",
    "RIP-KEY-5595",
    "RIP-KEY-3376",
    "RIP-KEY-7727",
    "RIP-KEY-6618",
    "RIP-KEY-2249",
    "RIP-KEY-0030"
}

local FileName = "RIP_Hub_License.json" 
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer

local function Launch_RIP_Hub()
    if CoreGui:FindFirstChild("RIPKeySystem") then
        CoreGui.RIPKeySystem:Destroy()
    end

    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "RIP Hub",
        Text = "✅ Добро пожаловать, " .. LocalPlayer.Name,
        Duration = 5
    })

    task.spawn(function()
        pcall(function()
            loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
        end)
    end)

    local UserInputService = game:GetService("UserInputService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local Workspace = game:GetService("Workspace")
    local Debris = game:GetService("Debris")
    local StatsService = game:GetService("Stats")
    local Lighting = game:GetService("Lighting")

    local plr = Players.LocalPlayer

    task.spawn(function()
        local scripts = plr:FindFirstChild("PlayerScripts")
        if scripts then
            local cm = scripts:FindFirstChild("CharacterAndBeamMove")
            local sp = scripts:FindFirstChild("StickyPartsTouchDetection")
            if cm then 
                cm.Disabled = true 
            end
            if sp then 
                sp.Disabled = true 
            end
        end
    end)

    task.spawn(function()
        local espData = {
            boxes = {},
            nameTags = {},
            connections = {}
        }
        
        local function IsPCLD(obj)
            if not obj:IsA("BasePart") then return false end
            local name = obj.Name:lower()
            return name == "playercharacterlocationdetector" or name == "partesp" or name == "playercharacterlocationdetectorfake"
        end

        local function CreateESPBox(obj)
            if not obj or not obj.Parent then return end
            if espData.boxes[obj] then return end
            
            local box = Instance.new("BoxHandleAdornment")
            box.Name = "PCLD_ESP_Box"
            box.Adornee = obj
            box.AlwaysOnTop = true
            box.ZIndex = 10
            box.Size = obj.Size * 1.05
            box.Transparency = 0.45
            box.Color3 = Color3.fromRGB(255, 80, 80)
            box.Parent = game:GetService("CoreGui")
            
            espData.boxes[obj] = box
            
            local conn = obj.AncestryChanged:Connect(function(_, parent)
                if not parent and espData.boxes[obj] then
                    espData.boxes[obj]:Destroy()
                    espData.boxes[obj] = nil
                end
            end)
            
            table.insert(espData.connections, conn)
        end

        local function CreatePlayerNameTag(player)
            if player == plr then return end
            if espData.nameTags[player] then return end
            
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "PlayerNameTag_" .. player.Name
            billboard.AlwaysOnTop = true
            billboard.Size = UDim2.new(0, 210, 0, 44)
            billboard.StudsOffset = Vector3.new(0, 6, 0)
            billboard.MaxDistance = 5000
            billboard.Parent = game:GetService("CoreGui")
            billboard.Enabled = true

            local frame = Instance.new("Frame")
            frame.Size = UDim2.new(1, 0, 1, 0)
            frame.BackgroundColor3 = Color3.fromRGB(30, 25, 40)
            frame.BackgroundTransparency = 0.4
            frame.BorderSizePixel = 0
            frame.Parent = billboard

            local uicorner = Instance.new("UICorner", frame)
            uicorner.CornerRadius = UDim.new(0, 10)
            
            local stroke = Instance.new("UIStroke", frame)
            stroke.Color = Color3.fromRGB(255, 80, 80)
            stroke.Thickness = 1.4
            stroke.Transparency = 0.35

            local text = Instance.new("TextLabel")
            text.Size = UDim2.new(1, -12, 1, -12)
            text.Position = UDim2.new(0, 6, 0, 6)
            text.BackgroundTransparency = 1
            text.Text = player.DisplayName or player.Name
            text.TextColor3 = Color3.fromRGB(255, 240, 240)
            text.TextStrokeColor3 = Color3.fromRGB(60, 20, 20)
            text.TextStrokeTransparency = 0.7
            text.Font = Enum.Font.GothamBold
            text.TextSize = 16
            text.Parent = frame

            espData.nameTags[player] = billboard
            
            local nameConn = player:GetPropertyChangedSignal("DisplayName"):Connect(function()
                text.Text = player.DisplayName or player.Name
            end)
            table.insert(espData.connections, nameConn)

            local function attach(char)
                if not char then 
                    billboard.Adornee = nil 
                    return 
                end
                
                local hrp = char:WaitForChild("HumanoidRootPart", 3)
                if hrp then 
                    billboard.Adornee = hrp 
                end
            end

            local charAddedConn = player.CharacterAdded:Connect(attach)
            table.insert(espData.connections, charAddedConn)
            
            local charRemovingConn = player.CharacterRemoving:Connect(function() 
                billboard.Adornee = nil 
            end)
            table.insert(espData.connections, charRemovingConn)
            
            if player.Character then 
                attach(player.Character) 
            end
        end
        
        local function RemovePlayerNameTag(player)
            if espData.nameTags[player] then
                espData.nameTags[player]:Destroy()
                espData.nameTags[player] = nil
            end
        end

        for _, obj in pairs(Workspace:GetDescendants()) do 
            if IsPCLD(obj) then 
                CreateESPBox(obj) 
            end 
        end
        
        for _, player in ipairs(Players:GetPlayers()) do 
            CreatePlayerNameTag(player) 
        end

        local workspaceConn = Workspace.DescendantAdded:Connect(function(obj)
            if IsPCLD(obj) then 
                CreateESPBox(obj) 
            end
        end)
        table.insert(espData.connections, workspaceConn)
        
        local playerAddedConn = Players.PlayerAdded:Connect(function(player)
            CreatePlayerNameTag(player)
        end)
        table.insert(espData.connections, playerAddedConn)
        
        local playerRemovingConn = Players.PlayerRemoving:Connect(function(player)
            RemovePlayerNameTag(player)
        end)
        table.insert(espData.connections, playerRemovingConn)
    end)

    if game.CoreGui:FindFirstChild("RIPHubGUI") then
        game.CoreGui.RIPHubGUI:Destroy()
    end
    if plr.PlayerGui:FindFirstChild("RIPHubGUI") then
        plr.PlayerGui.RIPHubGUI:Destroy()
    end

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "RIPHubGUI"
    pcall(function() ScreenGui.Parent = CoreGui end)
    if not ScreenGui.Parent then ScreenGui.Parent = plr:WaitForChild("PlayerGui") end

    local StatsFrame = Instance.new("Frame")
    StatsFrame.Name = "StatsDisplay"
    StatsFrame.Size = UDim2.new(0, 120, 0, 50)
    StatsFrame.Position = UDim2.new(1, -140, 0, 60)
    StatsFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    StatsFrame.BackgroundTransparency = 0.3
    StatsFrame.BorderSizePixel = 0
    StatsFrame.Active = true
    StatsFrame.Draggable = true
    StatsFrame.Parent = ScreenGui 

    local StatsCorner = Instance.new("UICorner", StatsFrame)
    StatsCorner.CornerRadius = UDim.new(0, 8)

    local StatsStroke = Instance.new("UIStroke", StatsFrame)
    StatsStroke.Color = Color3.fromRGB(255, 230, 0)
    StatsStroke.Thickness = 2
    StatsStroke.Transparency = 0.2

    local PingLabel = Instance.new("TextLabel")
    PingLabel.Name = "PingLabel"
    PingLabel.Size = UDim2.new(1, 0, 0.5, 0)
    PingLabel.Position = UDim2.new(0, 0, 0, 0)
    PingLabel.BackgroundTransparency = 1
    PingLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    PingLabel.Font = Enum.Font.GothamBold
    PingLabel.TextSize = 14
    PingLabel.Text = "PING: 0 ms"
    PingLabel.Parent = StatsFrame

    local FPSLabel = Instance.new("TextLabel")
    FPSLabel.Name = "FPSLabel"
    FPSLabel.Size = UDim2.new(1, 0, 0.5, 0)
    FPSLabel.Position = UDim2.new(0, 0, 0.5, 0)
    FPSLabel.BackgroundTransparency = 1
    FPSLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    FPSLabel.Font = Enum.Font.GothamBold
    FPSLabel.TextSize = 14
    FPSLabel.Text = "FPS: 60"
    FPSLabel.Parent = StatsFrame

    task.spawn(function()
        while StatsFrame.Parent do
            local fps = math.floor(1 / RunService.RenderStepped:Wait())
            local ping = 0
            pcall(function()
                ping = math.floor(StatsService.Network.ServerStatsItem["Data Ping"]:GetValue())
            end)
            
            FPSLabel.Text = "FPS: " .. tostring(fps)
            PingLabel.Text = "PING: " .. tostring(ping) .. " ms"
            
            if ping > 200 then
                PingLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
            elseif ping > 100 then
                PingLabel.TextColor3 = Color3.fromRGB(255, 170, 0)
            else
                PingLabel.TextColor3 = Color3.fromRGB(50, 255, 50)
            end
            
            task.wait(0.5)
        end
    end)

    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 600, 0, 480)
    MainFrame.Position = UDim2.new(0.5, -300, 0.5, -240)
    MainFrame.BackgroundColor3 = Color3.fromRGB(255, 230, 0)
    MainFrame.BackgroundTransparency = 0.2
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Active = true
    MainFrame.Draggable = true
    MainFrame.Parent = ScreenGui 

    local MainCorner = Instance.new("UICorner")
    MainCorner.CornerRadius = UDim.new(0, 10)
    MainCorner.Parent = MainFrame

    local Title = Instance.new("TextLabel")
    Title.Text = "RIP hub"
    Title.Size = UDim2.new(0, 150, 0, 50)
    Title.BackgroundTransparency = 1
    Title.TextColor3 = Color3.fromRGB(0, 0, 0)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 24
    Title.Parent = MainFrame

    local TargetLabel = Instance.new("TextLabel")
    TargetLabel.Text = "Target: None"
    TargetLabel.Size = UDim2.new(0, 250, 0, 30)
    TargetLabel.Position = UDim2.new(1, -260, 0, 10)
    TargetLabel.BackgroundTransparency = 1
    TargetLabel.TextColor3 = Color3.fromRGB(0,0,0)
    TargetLabel.Font = Enum.Font.GothamBold
    TargetLabel.TextSize = 14
    TargetLabel.TextXAlignment = Enum.TextXAlignment.Right
    TargetLabel.Parent = MainFrame

    local Sidebar = Instance.new("Frame")
    Sidebar.Size = UDim2.new(0, 150, 1, -50)
    Sidebar.Position = UDim2.new(0, 0, 0, 50)
    Sidebar.BackgroundTransparency = 1
    Sidebar.Parent = MainFrame

    local PagesFolder = Instance.new("Frame")
    PagesFolder.Size = UDim2.new(1, -160, 1, -20)
    PagesFolder.Position = UDim2.new(0, 160, 0, 10)
    PagesFolder.BackgroundTransparency = 1
    PagesFolder.Parent = MainFrame

    local MenuToys = ReplicatedStorage:WaitForChild("MenuToys", 5)
    local GE = ReplicatedStorage:WaitForChild("GrabEvents", 5)
    local PlayerEvents = ReplicatedStorage:WaitForChild("PlayerEvents", 5)

    if not GE or not MenuToys then
        warn("Critical folders not found!")
    end

    local stickyEvent = PlayerEvents and PlayerEvents:WaitForChild("StickyPartEvent", 5)
    local spawnRemote = MenuToys and MenuToys:WaitForChild("SpawnToyRemoteFunction", 5)
    local buyToyRemote = MenuToys and (MenuToys:FindFirstChild("BuyToyRemoteFunction") or MenuToys:FindFirstChild("BuyToy"))
    local destroyToyRemote = MenuToys and (MenuToys:FindFirstChild("DestroyToy") or MenuToys:FindFirstChild("DestroyToyRemote") or MenuToys:FindFirstChild("DeleteToyRE"))

    local setOwner = GE and GE:WaitForChild("SetNetworkOwner", 5)
    local CreateGrabLine = GE and GE:WaitForChild("CreateGrabLine", 5)
    local destroyGrabLineEvent = GE and GE:WaitForChild("DestroyGrabLine", 5)

    local selectedKickPlayer = nil
    local lagLinesAmount = 100

    _G.OwnershipModeTarget = 1
    _G.OwnershipModeAnchorBehavior = "Teleport"
    _G.TP_Priority = 0
    _G.NightMode = false
    _G.FriendWhitelistEnabled = false

    local function createPage(name)
        local Page = Instance.new("Frame")
        Page.Name = name .. "Page"
        Page.Size = UDim2.new(1, 0, 1, 0)
        Page.BackgroundTransparency = 1
        Page.Visible = false
        Page.Parent = PagesFolder
        return Page
    end

    local AntisPage = createPage("antis")
    local KickPage = createPage("kick")
    local TargetPage = createPage("target")
    local UtilityPage = createPage("utility")
    local TrapsPage = createPage("traps")
    local LagPage = createPage("lag")
    local UIPage = createPage("UI")

    local function switchPage(pageToShow)
        for _, page in pairs(PagesFolder:GetChildren()) do 
            page.Visible = false 
        end
        pageToShow.Visible = true
    end

    local function createTabBtn(text, linkedPage)
        local Btn = Instance.new("TextButton")
        Btn.Size = UDim2.new(1, -20, 0, 40)
        Btn.Position = UDim2.new(0, 10, 0, 0)
        Btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        Btn.BackgroundTransparency = 0.7
        Btn.Text = text
        Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        Btn.Font = Enum.Font.GothamSemibold
        Btn.TextSize = 16
        Btn.Parent = Sidebar
        
        local Layout = Sidebar:FindFirstChild("UIListLayout") or Instance.new("UIListLayout", Sidebar)
        Layout.Padding = UDim.new(0, 5)
        
        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 6)
        Corner.Parent = Btn
        
        Btn.MouseButton1Click:Connect(function() 
            switchPage(linkedPage) 
        end)
    end

    createTabBtn("ANTIS", AntisPage)
    createTabBtn("KICK", KickPage)
    createTabBtn("TARGET", TargetPage)
    createTabBtn("UTILITY", UtilityPage)
    createTabBtn("TRAPS", TrapsPage)
    createTabBtn("LAG", LagPage)
    createTabBtn("UI", UIPage)

    local function CreateScrollingGrid(page)
        local Scroll = Instance.new("ScrollingFrame")
        Scroll.Size = UDim2.new(1, 0, 1, 0)
        Scroll.BackgroundTransparency = 1
        Scroll.ScrollBarThickness = 4
        Scroll.CanvasSize = UDim2.new(0,0,1.5,0)
        Scroll.Parent = page

        local Grid = Instance.new("UIGridLayout")
        Grid.CellSize = UDim2.new(1, -10, 0, 40)
        Grid.CellPadding = UDim2.new(0, 0, 0, 5)
        Grid.SortOrder = Enum.SortOrder.LayoutOrder
        Grid.Parent = Scroll
        return Scroll, Grid
    end

    local function CreateToggle(parent, text, callback)
        local ToggleFrame = Instance.new("Frame")
        ToggleFrame.BackgroundColor3 = Color3.fromRGB(0,0,0)
        ToggleFrame.BackgroundTransparency = 0.6
        ToggleFrame.Parent = parent
        local Corner = Instance.new("UICorner"); Corner.Parent = ToggleFrame

        local Label = Instance.new("TextLabel")
        Label.Text = text
        Label.Size = UDim2.new(0.7, 0, 1, 0)
        Label.Position = UDim2.new(0.05, 0, 0, 0)
        Label.BackgroundTransparency = 1
        Label.TextColor3 = Color3.new(1,1,1)
        Label.Font = Enum.Font.GothamBold
        Label.TextSize = 14
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Parent = ToggleFrame

        local Button = Instance.new("TextButton")
        Button.Size = UDim2.new(0, 30, 0, 30)
        Button.Position = UDim2.new(1, -40, 0.5, -15)
        Button.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
        Button.Text = ""
        Button.Parent = ToggleFrame
        local BtnCorner = Instance.new("UICorner"); BtnCorner.CornerRadius = UDim.new(1,0); BtnCorner.Parent = Button

        local enabled = false
        local function update(val)
            enabled = val
            Button.BackgroundColor3 = enabled and Color3.fromRGB(50, 255, 50) or Color3.fromRGB(255, 50, 50)
            callback(enabled)
        end

        Button.MouseButton1Click:Connect(function() 
            update(not enabled) 
        end)

        return function(val)
            if val ~= enabled then
                enabled = val
                Button.BackgroundColor3 = enabled and Color3.fromRGB(50, 255, 50) or Color3.fromRGB(255, 50, 50)
            end
        end
    end

    local function CreateInput(parent, placeholder, callback)
        local Frame = Instance.new("Frame")
        Frame.BackgroundColor3 = Color3.fromRGB(0,0,0)
        Frame.BackgroundTransparency = 0.6
        Frame.Parent = parent
        local Corner = Instance.new("UICorner"); Corner.Parent = Frame

        local Box = Instance.new("TextBox")
        Box.Size = UDim2.new(1, -20, 1, 0)
        Box.Position = UDim2.new(0, 10, 0, 0)
        Box.BackgroundTransparency = 1
        Box.TextColor3 = Color3.new(1,1,1)
        Box.PlaceholderText = placeholder
        Box.Font = Enum.Font.GothamBold
        Box.TextSize = 14
        Box.TextXAlignment = Enum.TextXAlignment.Left
        Box.Text = ""
        Box.Parent = Frame

        Box.FocusLost:Connect(function()
            callback(Box.Text)
        end)
    end

    local function CreateButton(parent, text, callback)
        local Frame = Instance.new("Frame")
        Frame.BackgroundColor3 = Color3.fromRGB(0,0,0)
        Frame.BackgroundTransparency = 0.6
        Frame.Parent = parent
        local Corner = Instance.new("UICorner"); Corner.Parent = Frame

        local Button = Instance.new("TextButton")
        Button.Size = UDim2.new(1, 0, 1, 0)
        Button.BackgroundTransparency = 1
        Button.Text = text
        Button.TextColor3 = Color3.new(1,1,1)
        Button.Font = Enum.Font.GothamBold
        Button.TextSize = 14
        Button.Parent = Frame

        Button.MouseButton1Click:Connect(callback)
    end

    local AnchoredObjects = {}
    local SB_LineTransparencyValue = Instance.new("NumberValue")
    local SB_SurfaceTransparencyValue = Instance.new("NumberValue")
    local SB_AnchoredColor3 = Instance.new("Color3Value")
    local SB_AnchoredColor3Surface = Instance.new("Color3Value")
    local SB_GlueColor3 = Instance.new("Color3Value")
    local SB_GlueColor3Surface = Instance.new("Color3Value")
    local SB_MainGlueColor3 = Instance.new("Color3Value")
    local SB_MainGlueColor3Surface = Instance.new("Color3Value")

    SB_AnchoredColor3.Value = Color3.fromRGB(22, 2, 138)
    SB_AnchoredColor3Surface.Value = Color3.fromRGB(38, 85, 172)
    SB_GlueColor3.Value = Color3.fromRGB(242, 124, 17)
    SB_GlueColor3Surface.Value = Color3.fromRGB(253, 243, 130)
    SB_MainGlueColor3.Value = Color3.fromRGB(0, 85, 0)
    SB_MainGlueColor3Surface.Value = Color3.fromRGB(89, 225, 65)

    local attachmentInstance = Instance.new("Attachment")
    local soundEffect = Instance.new("Sound", attachmentInstance)
    local particleEmitter = Instance.new("ParticleEmitter", attachmentInstance)
    soundEffect.Name = "soundeffect"
    soundEffect.SoundId = "rbxassetid://1091083826"
    particleEmitter.LightInfluence = 1
    particleEmitter.Lifetime = NumberRange.new(2, 3)
    particleEmitter.Texture = "rbxassetid://15668608167"
    particleEmitter.Transparency = NumberSequence.new(0, 1)
    particleEmitter.Speed = NumberRange.new(6, 6)
    particleEmitter.Size = NumberSequence.new(0, 1)
    particleEmitter.SpreadAngle = Vector2.new(360, 360)
    particleEmitter.Rate = 20
    particleEmitter.Enabled = false
    particleEmitter.Name = "particle"

    function CheckNetworkOwnerShipOnPart(Part)
        if Part.ReceiveAge == 0 then
            return true
        end
        return false
    end

    function SNOWshipOnce(networkedPart)
        if not networkedPart then return false end
        local distanceFromCharacter = plr:DistanceFromCharacter(networkedPart.Position)
        local isConnected = networkedPart:GetAttribute("Connected")
        local createdConnected = networkedPart:GetAttribute("CreatedConnected")
        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if CheckNetworkOwnerShipOnPart(networkedPart) then
                networkedPart:SetAttribute("Connected", true)
                if destroyGrabLineEvent then destroyGrabLineEvent:FireServer(networkedPart) end
                if not createdConnected then
                    networkedPart:SetAttribute("CreatedConnected", true)
                    networkedPart.ChildAdded:Connect(function(child)
                        if child.Name == "PartOwner" and child.Value ~= plr.Name then
                            networkedPart:SetAttribute("Connected", false)
                        end
                    end)
                end
                return true
            elseif distanceFromCharacter <= 30 and not isConnected and setOwner then
                setOwner:FireServer(networkedPart, CFrame.lookAt(plr.Character.HumanoidRootPart.Position, networkedPart.Position))
            end
        end
    end

    function GetPlayerCharacter()
        if plr.Character and (plr.Character:FindFirstChild("HumanoidRootPart") and plr.Character:FindFirstChildOfClass("Humanoid")) then
            return plr.Character
        end
    end

    function ChangeActivityPriority(teleportPriority)
        if _G.TP_Priority <= teleportPriority then
            _G.TP_Priority = teleportPriority
            return true
        end
        if teleportPriority == 0 then
            _G.TP_Priority = teleportPriority
            return true
        end
    end

    function TeleportPlayer(cframeOffset, teleportPriority)
        if (teleportPriority == nil and 0 or teleportPriority) == _G.TP_Priority then
            local playerCharacter = GetPlayerCharacter()
            if playerCharacter and (not _G.TeleportingToNetworkOwnership and typeof(cframeOffset) == "CFrame") then
                local humanoidRootPart = playerCharacter.HumanoidRootPart
                local humanoid = playerCharacter:FindFirstChildOfClass("Humanoid")
                humanoidRootPart.CFrame = humanoidRootPart.CFrame.Rotation + cframeOffset.Position
                if humanoid.SeatPart == nil or tostring(humanoid.SeatPart.Parent) ~= "CreatureBlobman" then
                    humanoid.Sit = false
                end
            end
        end
    end

    function GetPlayerCFrame()
        local playerCharacterCFrame = GetPlayerCharacter()
        if playerCharacterCFrame then
            return playerCharacterCFrame.HumanoidRootPart.CFrame
        end
    end

    function Getdistancefromcharacter(characterPosition)
        return plr:DistanceFromCharacter(characterPosition)
    end

    function anchorobjecteffect(parentPart)
        local clonedAttachment = attachmentInstance:Clone()
        clonedAttachment.Parent = parentPart
        clonedAttachment.soundeffect:Play()
        clonedAttachment.particle:Emit(25)
        Debris:AddItem(clonedAttachment)
    end

    function autosetownership()
        for anchoredObjectKey, anchoredObjectValue in pairs(AnchoredObjects) do
            if typeof(anchoredObjectValue.PartAnchored) == "Instance" and not anchoredObjectKey:GetAttribute("AnchorOwnership") then
                local anchoredPart = anchoredObjectValue.PartAnchored
                local anchoredModel = anchoredObjectValue.Model
                local playerFromCharacter
                if anchoredModel:FindFirstChildOfClass("Humanoid") then
                    playerFromCharacter = Players:GetPlayerFromCharacter(anchoredModel)
                    anchoredPart = anchoredModel:FindFirstChild("Head")
                else
                    playerFromCharacter = nil
                end
                local canTeleport = (playerFromCharacter and (_G.OwnershipModeTarget == 1 or _G.OwnershipModeTarget == 3) or playerFromCharacter == nil and (_G.OwnershipModeTarget == 2 or _G.OwnershipModeTarget == 3)) and true or false
                if anchoredPart then
                    if Getdistancefromcharacter(anchoredPart.Position) >= 30 then
                        if _G.OwnershipModeAnchorBehavior == "Teleport" and canTeleport then
                            ChangeActivityPriority(1)
                            local playerCFrame = GetPlayerCFrame()
                            for _ = 0, 15 do
                                if SNOWshipOnce(anchoredPart) then
                                    anchoredModel:SetAttribute("AnchorOwnership", true)
                                    break
                                end
                                TeleportPlayer(CFrame.new(anchoredPart.Position + Vector3.new(0, 5, 0)), 1)
                                task.wait()
                            end
                            ChangeActivityPriority(0)
                            TeleportPlayer(playerCFrame)
                        end
                    elseif SNOWshipOnce(anchoredPart) then
                        anchoredModel:SetAttribute("AnchorOwnership", true)
                    end
                end
            end
        end
    end

    function ChangeSBstate(selectionBox, selectionBoxState)
        if typeof(selectionBox) == "Instance" and selectionBox:IsA("SelectionBox") then
            selectionBox:SetAttribute("SB_State", selectionBoxState)
            if selectionBoxState == "Anchored" then
                selectionBox.Color3 = SB_AnchoredColor3.Value
                selectionBox.SurfaceColor3 = SB_AnchoredColor3Surface.Value
            elseif selectionBoxState == "Glue" then
                selectionBox.Color3 = SB_GlueColor3.Value
                selectionBox.SurfaceColor3 = SB_GlueColor3Surface.Value
            elseif selectionBoxState == "GluePrimary" then
                selectionBox.Color3 = SB_MainGlueColor3.Value
                selectionBox.SurfaceColor3 = SB_MainGlueColor3Surface.Value
            else
                selectionBox.Color3 = Color3.fromRGB(139, 0, 0)
                selectionBox.SurfaceColor3 = Color3.fromRGB(193, 0, 0)
            end
        end
    end

    function unAnchorObject(anchoredObject)
        if typeof(anchoredObject) == "Instance" and anchoredObject.Parent and (anchoredObject.Parent:IsA("Model") or anchoredObject.Parent:IsA("Folder")) then
            local anchoredObjectParent = anchoredObject.Parent
            local isAnchoredAttribute = anchoredObjectParent:GetAttribute("IsAnchored")
            local gluePrimaryAttribute = anchoredObjectParent:GetAttribute("GluePrimary")
            if not anchoredObjectParent:IsA("Folder") and anchoredObjectParent ~= Workspace then
                anchoredObject = anchoredObjectParent
            end
            if AnchoredObjects[anchoredObject] and isAnchoredAttribute then
                local anchoredObjectData = AnchoredObjects[anchoredObject]
                anchoredObjectData.BodyPosition.Parent = anchoredObject
                anchoredObjectData.BodyGyro.Parent = anchoredObject
                anchoredObjectData.PartAnchored = nil
                if gluePrimaryAttribute then
                    ChangeSBstate(anchoredObjectData.SB, "GluePrimary")
                else
                    anchoredObjectData.SB.Visible = false
                end
                for _, connection in pairs(anchoredObjectData.Connections) do
                    connection:Disconnect()
                end
                anchoredObject:SetAttribute("IsAnchored", false)
                anchoredObject:SetAttribute("AnchorOwnership", false)
                if not gluePrimaryAttribute then
                    AnchoredObjects[anchoredObject] = nil
                end
            end
        end
    end

    function setanchorObject(part)
        if typeof(part) == "Instance" and part.Parent and (part.Parent:IsA("Model") or part.Parent:IsA("Folder")) then
            local parentModel = part.Parent
            if parentModel:IsA("Folder") or parentModel == Workspace then
                parentModel = part
            end
            if parentModel:GetAttribute("IsAnchored") or parentModel:GetAttribute("Glue") then
                unAnchorObject(part)
            else
                local anchorPositionBody = parentModel:FindFirstChild("AnchorPositionBody") or (part:FindFirstChild("AnchorPositionBody") or Instance.new("BodyPosition"))
                local anchorGyroBody = parentModel:FindFirstChild("AnchorGyroBody") or (part:FindFirstChild("AnchorGyroBody") or Instance.new("BodyGyro"))
                local objectStateSelectionBox = parentModel:FindFirstChild("ObjectState") or Instance.new("SelectionBox")
                local descendantConnections = {}
                local infiniteVector3 = Vector3.new(math.huge, math.huge, math.huge)
                local zeroVector = Vector3.new(0, 0, 0)
                local partPosition = part.Position
                local isConnected = false
                local isPartOwnerRemoved = false
                if part.Parent:FindFirstChild("Head") and part.Parent:FindFirstChildOfClass("Humanoid") then
                    if Players:GetPlayerFromCharacter(part.Parent) then
                        isPartOwnerRemoved = true
                    else
                        isConnected = true
                    end
                end
                anchorPositionBody.Name = "AnchorPositionBody"
                anchorPositionBody.Position = part.Position
                anchorPositionBody.Parent = part
                anchorGyroBody.Name = "AnchorGyroBody"
                anchorGyroBody.Parent = part
                anchorGyroBody.CFrame = part.CFrame
                anchorGyroBody.D = 950
                anchorGyroBody.P = 40000
                anchorPositionBody.P = 40000
                anchorPositionBody.D = 950
                objectStateSelectionBox.Name = "ObjectState"
                objectStateSelectionBox.LineThickness = 0.025
                objectStateSelectionBox.SurfaceTransparency = SB_SurfaceTransparencyValue.Value
                objectStateSelectionBox.Transparency = SB_LineTransparencyValue.Value
                objectStateSelectionBox.Visible = true
                objectStateSelectionBox.Parent = parentModel
                objectStateSelectionBox.Adornee = parentModel

                local function updateJointMaxForce()
                    if parentModel:GetAttribute("IsAnchored") or parentModel:GetAttribute("Glue") then
                        anchorGyroBody.MaxTorque = infiniteVector3
                        anchorPositionBody.MaxForce = infiniteVector3
                    end
                    if parentModel:GetAttribute("GluePrimary") and not parentModel:GetAttribute("IsAnchored") then
                        ChangeSBstate(objectStateSelectionBox, "GluePrimary")
                    elseif parentModel:GetAttribute("Glue") and not parentModel:GetAttribute("IsAnchored") then
                        ChangeSBstate(objectStateSelectionBox, "Glue")
                    else
                        ChangeSBstate(objectStateSelectionBox, "Anchored")
                    end
                end
                
                local function resetAnchorForces()
                    anchorGyroBody.MaxTorque = Vector3.new()
                    anchorPositionBody.MaxForce = Vector3.new()
                    ChangeSBstate(objectStateSelectionBox)
                    parentModel:SetAttribute("AnchorOwnership", false)
                end

                descendantConnections[1] = parentModel.DescendantAdded:Connect(function(descendant)
                    if descendant.Name == "PartOwner" then
                        if descendant.Value ~= plr.Name then
                            resetAnchorForces()
                        else
                            updateJointMaxForce()
                        end
                    end
                end)
                descendantConnections[2] = parentModel.DescendantRemoving:Connect(function(descendant)
                    if descendant.Name == "PartOwner" and descendant.Value == plr.Name then
                        if descendant.Value ~= plr.Name or not isConnected then
                            if descendant.Value == plr.Name and isPartOwnerRemoved then
                                resetAnchorForces()
                            end
                        else
                            updateJointMaxForce()
                        end
                    end
                end)

                task.spawn(function()
                    while anchorPositionBody.Parent and not parentModel:GetAttribute("Glue") do
                        if parentModel:GetAttribute("IsAnchored") then
                            anchorGyroBody.MaxTorque = infiniteVector3
                            anchorPositionBody.MaxForce = infiniteVector3
                        else
                            anchorGyroBody.MaxTorque = zeroVector
                            anchorPositionBody.MaxForce = zeroVector
                        end
                        anchorPositionBody.Position = partPosition + Vector3.new(0, 0.001, 0)
                        task.wait()
                        anchorPositionBody.Position = partPosition
                    end
                end)
                AnchoredObjects[parentModel] = {
                    BodyPosition = anchorPositionBody,
                    BodyGyro = anchorGyroBody,
                    PartAnchored = part,
                    SB = objectStateSelectionBox,
                    Connections = descendantConnections,
                    Model = parentModel
                }
                anchorobjecteffect(part)
                parentModel:SetAttribute("IsAnchored", true)
                updateJointMaxForce()
            end
        end
    end

    function anchorfunc()
        local grabPartsFolder = Workspace:FindFirstChild("GrabParts")
        local function isGrabbablePart(part)
            if part and not (part:IsDescendantOf(Workspace.Map) or part.Anchored) then
                return true
            end
        end
        if grabPartsFolder then
            local grabbedPart = grabPartsFolder.GrabPart.WeldConstraint.Part1
            if isGrabbablePart(grabbedPart) then
                setanchorObject(grabbedPart)
            end
        elseif GetPlayerCharacter() then
            local controllingCreature = _G.ControllingCreature or plr.Character
            local cameraFocusPartName = _G.ControllingCreature and "Head" or (plr.Character and "CamPart" or nil)
            if not cameraFocusPartName then return end

            local camPart = controllingCreature:FindFirstChild(cameraFocusPartName)
            if not camPart then return end

            local raycastParams = RaycastParams.new()
            raycastParams.FilterDescendantsInstances = {controllingCreature}
            raycastParams.FilterType = Enum.RaycastFilterType.Exclude

            local rayOrigin = camPart.Position
            local rayDirection = workspace.CurrentCamera.CFrame.LookVector * 5000
            
            local raycastResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)

            if raycastResult then
                local raycastPart = raycastResult.Instance
                if raycastPart and raycastPart.Parent and (raycastPart.Parent:IsA("Model") and isGrabbablePart(raycastPart)) then
                    setanchorObject(raycastPart)
                end
            end
        end
    end

    task.spawn(function()
        while true do
            autosetownership()
            task.wait()
        end
    end)

    local UtilityScroll, UtilityGrid = CreateScrollingGrid(UtilityPage)

    CreateButton(UtilityScroll, "Anchor Looked/Held Object (Key: K)", function()
        anchorfunc()
    end)

    CreateButton(UtilityScroll, "Unanchor All Objects", function()
        for obj, _ in pairs(AnchoredObjects) do
            unAnchorObject(obj)
        end
    end)

    local noCDConnection = nil
    CreateToggle(UtilityScroll, "No Cooldown (Instant)", function(val)
        if noCDConnection then noCDConnection:Disconnect(); noCDConnection = nil end
        if val then
            local canSpawn = plr:WaitForChild("CanSpawnToy", 5)
            if canSpawn then
                canSpawn.Value = true
                noCDConnection = canSpawn.Changed:Connect(function(newVal)
                    if not newVal then canSpawn.Value = true end
                end)
            end
        end
    end)

    UserInputService.InputBegan:Connect(function(input, processed)
        if not processed and input.KeyCode == Enum.KeyCode.K then
            anchorfunc()
        end
    end)

    local storedSettings = {}
    _G.NightMode = false

    CreateToggle(UtilityScroll, "Night Mode", function(val)
        _G.NightMode = val
        local Light = game:GetService("Lighting")
        local Terrain = workspace.Terrain
        local Clouds = Terrain:FindFirstChildOfClass("Clouds") or Light:FindFirstChildOfClass("Clouds")

        if val then
            storedSettings.ClockTime = Light.ClockTime
            storedSettings.Brightness = Light.Brightness
            storedSettings.Ambient = Light.Ambient
            storedSettings.OutdoorAmbient = Light.OutdoorAmbient
            storedSettings.FogColor = Light.FogColor
            storedSettings.GuiColor = MainFrame.BackgroundColor3

            Light.ClockTime = 0
            Light.Brightness = 0
            Light.Ambient = Color3.fromRGB(0, 0, 0)
            Light.OutdoorAmbient = Color3.fromRGB(50, 50, 50)
            Light.FogColor = Color3.fromRGB(0, 0, 0)

            if Clouds then
                storedSettings.CloudsEnabled = Clouds.Enabled
                Clouds.Enabled = false
            end

            MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        else
            if storedSettings.ClockTime then
                Light.ClockTime = storedSettings.ClockTime
                Light.Brightness = storedSettings.Brightness
                Light.Ambient = storedSettings.Ambient
                Light.OutdoorAmbient = storedSettings.OutdoorAmbient
                Light.FogColor = storedSettings.FogColor
                MainFrame.BackgroundColor3 = storedSettings.GuiColor
            end

            if Clouds and storedSettings.CloudsEnabled ~= nil then
                Clouds.Enabled = storedSettings.CloudsEnabled
            end
        end
    end)

    local storedDriftSettings = {}
    
    CreateToggle(UtilityScroll, "NIGHT DRIFT", function(val)
        local Light = game:GetService("Lighting")
        local Terrain = workspace.Terrain
        local Clouds = Terrain:FindFirstChildOfClass("Clouds") or Light:FindFirstChildOfClass("Clouds")

        if val then
            storedDriftSettings.ClockTime = Light.ClockTime
            storedDriftSettings.OutdoorAmbient = Light.OutdoorAmbient
            storedDriftSettings.Ambient = Light.Ambient

            Light.ClockTime = 18.5 
            Light.OutdoorAmbient = Color3.fromRGB(80, 60, 100) 
            Light.Ambient = Color3.fromRGB(50, 30, 80)

            if Clouds then
                storedDriftSettings.CloudColor = Clouds.Color
                Clouds.Color = Color3.fromRGB(170, 0, 255) 
                Clouds.Enabled = true
            end

            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("BasePart") and (v.Name == "Baseplate" or v.Name == "Ground" or v.Name == "Grass") then
                    if not storedDriftSettings.Grounds then storedDriftSettings.Grounds = {} end
                    storedDriftSettings.Grounds[v] = {Mat = v.Material, Ref = v.Reflectance, Col = v.Color}
                    v.Material = Enum.Material.Glass
                    v.Reflectance = 0.8
                    v.Color = Color3.fromRGB(30, 0, 60) 
                end
            end

            if not storedDriftSettings.CreatedLights then storedDriftSettings.CreatedLights = {} end
            
            for i = 1, 30 do 
                local lightPart = Instance.new("Part")
                lightPart.Name = "NightDriftLight"
                lightPart.Size = Vector3.new(1, 1, 1)
                lightPart.Shape = Enum.PartType.Ball
                lightPart.Material = Enum.Material.Neon
                lightPart.Color = Color3.fromRGB(180, 0, 255) 
                lightPart.Transparency = 0.3
                lightPart.Anchored = true
                lightPart.CanCollide = false
                
                local rngX = math.random(-400, 400)
                local rngZ = math.random(-400, 400)
                local rngY = math.random(8, 30) 
                lightPart.Position = Vector3.new(rngX, rngY, rngZ)
                
                local light = Instance.new("PointLight")
                light.Color = Color3.fromRGB(200, 50, 255)
                light.Range = 40
                light.Brightness = 8
                light.Shadows = true
                light.Parent = lightPart
                
                lightPart.Parent = workspace
                table.insert(storedDriftSettings.CreatedLights, lightPart)
            end

        else
            if storedDriftSettings.ClockTime then Light.ClockTime = storedDriftSettings.ClockTime end
            if storedDriftSettings.OutdoorAmbient then Light.OutdoorAmbient = storedDriftSettings.OutdoorAmbient end
            if storedDriftSettings.Ambient then Light.Ambient = storedDriftSettings.Ambient end

            if Clouds and storedDriftSettings.CloudColor then
                Clouds.Color = storedDriftSettings.CloudColor
            end

            if storedDriftSettings.Grounds then
                for part, props in pairs(storedDriftSettings.Grounds) do
                    if part and part.Parent then
                        part.Material = props.Mat
                        part.Reflectance = props.Ref
                        part.Color = props.Col
                    end
                end
                storedDriftSettings.Grounds = {}
            end

            if storedDriftSettings.CreatedLights then
                for _, part in pairs(storedDriftSettings.CreatedLights) do
                    if part then part:Destroy() end
                end
                storedDriftSettings.CreatedLights = {}
            end
        end
    end)

    local noHeadConnection
    CreateToggle(UtilityScroll, "No Head", function(val)
        if val then
            noHeadConnection = RunService.RenderStepped:Connect(function()
                local char = plr.Character
                if char and char:FindFirstChild("Head") then
                    local head = char.Head
                    
                    if workspace.CurrentCamera.CameraSubject == head then
                        local torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso") or char:FindFirstChild("HumanoidRootPart")
                        if torso then workspace.CurrentCamera.CameraSubject = torso end
                    end

                    pcall(function()
                        if setOwner then
                            setOwner:FireServer(head, CFrame.new(0, 9e9, 0)) 
                        end
                    end)
                    
                    head.Transparency = 1
                    if head:FindFirstChild("face") then head.face.Transparency = 1 end
                end
            end)
        else
            if noHeadConnection then noHeadConnection:Disconnect() noHeadConnection = nil end
            local char = plr.Character
            if char and char:FindFirstChild("Head") then
                char.Head.Transparency = 0
                if char.Head:FindFirstChild("face") then char.Head.face.Transparency = 0 end
            end
        end
    end)

    local TrapsScroll, TrapsGrid = CreateScrollingGrid(TrapsPage)

    local function GetTargetForTrap()
        if selectedKickPlayer and selectedKickPlayer.Character and selectedKickPlayer.Character:FindFirstChild("Head") then
            return selectedKickPlayer.Character.Head
        elseif plr.Character and plr.Character:FindFirstChild("Head") then
            return plr.Character.Head
        end
        return nil
    end

    local function SetModelNoCollide(model)
        for _, v in pairs(model:GetDescendants()) do
            if v:IsA("BasePart") then
                v.CanCollide = false
                v.CanQuery = false
                v.CanTouch = false
            end
        end
    end

    local function SpawnToyForTrap(name)
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end

        pcall(function() 
            if spawnRemote then
                spawnRemote:InvokeServer(name, hrp.CFrame + Vector3.new(0, 10, 0), Vector3.new(0,0,0)) 
            end
        end)

        if buyToyRemote then
            pcall(function() buyToyRemote:InvokeServer(name) end)
        end

        local folder = Workspace:WaitForChild(plr.Name.."SpawnedInToys", 5)
        if folder then
            local toy = folder:WaitForChild(name, 5)
            return toy
        end
    end

    local campfireActive = false
    CreateToggle(TrapsScroll, "Campfire Trap", function(val)
        campfireActive = val
        if not val then return end

        task.spawn(function()
            local campfire = SpawnToyForTrap("Campfire")
            if not campfire then return end
            
            local firePart = campfire:WaitForChild("FirePlayerPart", 5)
            local primary = campfire.PrimaryPart
            if not firePart or not primary then return end
            
            local bPos = Instance.new("BodyPosition", primary)
            bPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bPos.P = 20000
            
            SetModelNoCollide(campfire)
            
            while campfireActive and campfire.Parent do
                local targetHead = GetTargetForTrap()
                if targetHead then
                    if _G.FriendWhitelistEnabled and IsWhitelisted(Players:GetPlayerFromCharacter(targetHead.Parent)) then
                        bPos.Position = plr.Character.Head.Position + Vector3.new(0, 50, 0)
                    else
                        bPos.Position = targetHead.Position + Vector3.new(0, 500, 0)
                        if SNOWshipOnce(firePart) then
                            firePart.CFrame = targetHead.CFrame
                        end
                    end
                else
                    bPos.Position = plr.Character.Head.Position + Vector3.new(0, 50, 0)
                end
                task.wait()
            end
            if campfire.Parent and destroyToyRemote then destroyToyRemote:FireServer(campfire) end
        end)
    end)

    local bananaActive = false
    CreateToggle(TrapsScroll, "Banana Trap", function(val)
        bananaActive = val
        if not val then return end

        task.spawn(function()
            local banana = SpawnToyForTrap("FoodBanana")
            if not banana then return end
            
            local peel = banana:WaitForChild("BananaPeel", 5)
            local primary = banana.PrimaryPart
            if not peel or not primary then return end
            
            local bPos = Instance.new("BodyPosition", primary)
            bPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            
            SetModelNoCollide(banana)
            peel.Size = Vector3.new(4, 4, 4)
            peel.Transparency = 1
            
            while bananaActive and banana.Parent do
                local targetHead = GetTargetForTrap()
                if targetHead then
                    if not (_G.FriendWhitelistEnabled and IsWhitelisted(Players:GetPlayerFromCharacter(targetHead.Parent))) then
                        bPos.Position = targetHead.Position + Vector3.new(0, 400, 0)
                        if SNOWshipOnce(peel) then
                            peel.CFrame = targetHead.CFrame
                        end
                    end
                end
                task.wait()
            end
            if banana.Parent and destroyToyRemote then destroyToyRemote:FireServer(banana) end
        end)
    end)

    local sprayActive = false
    CreateToggle(TrapsScroll, "Spray Can Trap", function(val)
        sprayActive = val
        if not val then return end

        task.spawn(function()
            local spray = SpawnToyForTrap("SprayCanWD")
            if not spray then return end
            
            local remover = spray:WaitForChild("StickyRemoverPart", 5)
            local primary = spray.PrimaryPart
            
            local bPos = Instance.new("BodyPosition", primary)
            bPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            
            SetModelNoCollide(spray)
            
            while sprayActive and spray.Parent do
                local targetHead = GetTargetForTrap()
                if targetHead then
                    if not (_G.FriendWhitelistEnabled and IsWhitelisted(Players:GetPlayerFromCharacter(targetHead.Parent))) then
                        bPos.Position = targetHead.Position + Vector3.new(0, 300, 0)
                        if SNOWshipOnce(remover) then
                            remover.CFrame = targetHead.CFrame
                        end
                    end
                end
                task.wait()
            end
            if spray.Parent and destroyToyRemote then destroyToyRemote:FireServer(spray) end
        end)
    end)

    local fakeAimActive = false
    CreateToggle(TrapsScroll, "Fake Aim", function(val)
        fakeAimActive = val
        if not val then return end

        task.spawn(function()
            local dice = SpawnToyForTrap("DiceSmall")
            if not dice then return end
            
            local primary = dice.PrimaryPart
            local bPos = Instance.new("BodyPosition", primary)
            bPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            
            SetModelNoCollide(dice)
            for _,v in pairs(dice:GetDescendants()) do 
                if v:IsA("BasePart") then v.Transparency = 1; v.CanQuery = false end 
            end
            
            while fakeAimActive and dice.Parent do
                bPos.Position = Vector3.new(1000, 400, 1000)
                task.wait(0.1)
                bPos.Position = Vector3.new(-1000, 400, 1000)
                task.wait(0.1)
            end
            if dice.Parent and destroyToyRemote then destroyToyRemote:FireServer(dice) end
        end)
    end)

    local piskaActive = false
    local piskaConnection = nil

    CreateToggle(TrapsScroll, "Coconut Art", function(val)
        piskaActive = val

        if piskaConnection then 
            piskaConnection:Disconnect()
            piskaConnection = nil
        end

        if not val then return end

        local MaxCoconuts = 5
        local Coconuts = {}

        task.spawn(function()
            while piskaActive do
                if #Coconuts < MaxCoconuts then
                    local char = plr.Character
                    local root = char and char:FindFirstChild("HumanoidRootPart")
                    if root then
                        pcall(function()
                            spawnRemote:InvokeServer("FoodCoconut", root.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
                        end)
                    end
                end
                task.wait(1) 
            end
        end)

        piskaConnection = RunService.RenderStepped:Connect(function()
            local char = plr.Character
            local head = char and char:FindFirstChild("Head") 
            local myToys = workspace:FindFirstChild(plr.Name.."SpawnedInToys")

            if not head or not myToys then return end

            table.clear(Coconuts)
            for _, toy in pairs(myToys:GetChildren()) do
                if toy.Name == "FoodCoconut" then
                    table.insert(Coconuts, toy)
                end
            end

            for i = 1, #Coconuts do
                if i > MaxCoconuts then break end
                local Coco = Coconuts[i]
                
                if Coco and Coco.Parent then
                    local Part = Coco:FindFirstChild("SoundPart")
                    local HoldPart = Coco:FindFirstChild("HoldPart")
                    
                    if Part and HoldPart then
                        local PartOwner = Part:FindFirstChild("PartOwner")
                        
                        if not PartOwner or PartOwner.Value ~= plr.Name then
                            if setOwner then setOwner:FireServer(Part, Part.CFrame) end
                        end

                        local Rigid = HoldPart:FindFirstChild("RigidConstraint")
                        if Rigid and Rigid.Attachment1 and destroyToyRemote then 
                            destroyToyRemote:FireServer(Coco) 
                        end

                        for _, p in pairs(Coco:GetDescendants()) do
                            if p:IsA("BasePart") then
                                p.CanCollide = false
                                p.CanQuery = false
                                p.Massless = true 
                                p.Velocity = Vector3.zero 
                                p.RotVelocity = Vector3.zero
                                p.Anchored = false 
                                if p.Transparency ~= 1 then p.Transparency = 0 end
                            end
                            if p:IsA("Weld") or p:IsA("WeldConstraint") or p:IsA("ManualWeld") then p:Destroy() end
                        end

                        local headCF = head.CFrame
                        local targetCF
                        
                        if i == 1 then
                            targetCF = headCF * CFrame.new(-0.5, -0.5, 0.8) 
                        elseif i == 2 then
                            targetCF = headCF * CFrame.new(0.5, -0.5, 0.8) 
                        else
                            local height = (i - 2) * 0.7
                            targetCF = headCF * CFrame.new(0, -0.5 + height, 0.8) 
                        end
                        
                        Part.CFrame = targetCF
                    end
                end
            end
        end)
    end)

    local blobSpinActive = false
    local blobSpinConnection = nil

    CreateToggle(TrapsScroll, "Blobman Guards", function(val)
        blobSpinActive = val
        if blobSpinConnection then blobSpinConnection:Disconnect(); blobSpinConnection = nil end
        if not val then return end

        local MaxBlobs = 2
        local Blobs = {}
        local RotationSpeed = 2
        local Radius = 6

        task.spawn(function()
            while blobSpinActive do
                if #Blobs < MaxBlobs then
                    local char = plr.Character
                    local root = char and char:FindFirstChild("HumanoidRootPart")
                    if root then
                        pcall(function()
                            spawnRemote:InvokeServer("CreatureBlobman", root.CFrame * CFrame.new(0, 5, 0), Vector3.zero)
                        end)
                    end
                end
                task.wait(2) 
            end
        end)

        blobSpinConnection = RunService.RenderStepped:Connect(function()
            local char = plr.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            local myToys = workspace:FindFirstChild(plr.Name.."SpawnedInToys")

            if not root or not myToys then return end

            table.clear(Blobs)
            for _, toy in pairs(myToys:GetChildren()) do
                if toy.Name == "CreatureBlobman" then
                    table.insert(Blobs, toy)
                end
            end

            local t = tick()

            for i = 1, #Blobs do
                if i > MaxBlobs then break end
                local blob = Blobs[i]
                
                if blob and blob.Parent then
                    local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
                    
                    if blob:FindFirstChild("LeftDetector") then blob.LeftDetector:Destroy() end
                    if blob:FindFirstChild("RightDetector") then blob.RightDetector:Destroy() end
                    
                    if blobRoot then
                        local PartOwner = blobRoot:FindFirstChild("PartOwner")
                        if not PartOwner or PartOwner.Value ~= plr.Name then
                            if setOwner then setOwner:FireServer(blobRoot, blobRoot.CFrame) end
                        end

                        for _, p in pairs(blob:GetDescendants()) do
                            if p:IsA("BasePart") then
                                p.CanCollide = false
                                p.CanQuery = false
                                p.Massless = true
                                p.Anchored = false 
                                p.Velocity = Vector3.zero 
                                p.RotVelocity = Vector3.zero
                            end
                            if p:IsA("Weld") or p:IsA("WeldConstraint") or p:IsA("ManualWeld") then p:Destroy() end
                        end
                        
                        local angle = (math.pi * 2 / MaxBlobs) * i + (t * RotationSpeed)
                        local offset = CFrame.new(math.sin(angle) * Radius, 0, math.cos(angle) * Radius)
                        local targetCF = root.CFrame * offset
                        
                        blobRoot.CFrame = CFrame.lookAt(targetCF.Position, root.Position)
                    end
                end
            end
        end)
    end)

    local AntisScroll, AntisGrid = CreateScrollingGrid(AntisPage)

    local antiExplodeT = false
    CreateToggle(AntisScroll, "Anti Explode", function(Value)
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
    end)

    local antiBlobT = false
    CreateToggle(AntisScroll, "Anti Blob", function(Value)
        antiBlobT = Value
        local function cleanBlob(toy)
            if toy.Name == "CreatureBlobman" then
                task.wait(0.1)
                if toy:FindFirstChild("LeftDetector") then toy.LeftDetector:Destroy() end
                if toy:FindFirstChild("RightDetector") then toy.RightDetector:Destroy() end
            end
        end
        if Value then
            for _, v in pairs(workspace:GetDescendants()) do cleanBlob(v) end
            local conn
            conn = workspace.DescendantAdded:Connect(function(v)
                if not antiBlobT then conn:Disconnect() return end
                cleanBlob(v)
            end)
        else antiBlobT = false end
    end)

    local antiGrabConn = nil
    CreateToggle(AntisScroll, "Anti Grab", function(Value)
        local isHeld = plr:WaitForChild("IsHeld", 5)
        local struggleEvent = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
        local savedCFrame = nil

        if antiGrabConn then 
            antiGrabConn:Disconnect()
            antiGrabConn = nil
        end

        if not Value then
            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                plr.Character.HumanoidRootPart.Anchored = false
            end
            return 
        end

        local function onHeldChanged(heldState)
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            
            if heldState then
                if hrp then 
                    savedCFrame = hrp.CFrame
                    hrp.Anchored = true 
                end
                
                task.spawn(function()
                    while isHeld.Value and Value do 
                        struggleEvent:FireServer(plr)
                        task.wait() 
                    end
                    if hrp then 
                        hrp.Anchored = false 
                        if savedCFrame then hrp.CFrame = savedCFrame end
                    end
                end)
            else
                if hrp then 
                    hrp.Anchored = false 
                    if savedCFrame then hrp.CFrame = savedCFrame end
                end
            end
        end

        if isHeld then
            antiGrabConn = isHeld.Changed:Connect(onHeldChanged)
            if isHeld.Value then
                onHeldChanged(true)
            end
        end
    end)

    local uglyAuraActive = false
    CreateToggle(AntisScroll, "Ugly Aura", function(Value)
        uglyAuraActive = Value
        if Value then
            task.spawn(function()
                while uglyAuraActive do
                    local char = plr.Character
                    local hrp = char and char:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        for _, v in pairs(workspace:GetDescendants()) do
                            if not uglyAuraActive then break end
                            if v:IsA("BasePart") and not v.Anchored and not v:IsDescendantOf(char) then
                                local dist = (v.Position - hrp.Position).Magnitude
                                if dist <= 60 then
                                    local pushDir = (v.Position - hrp.Position).Unit
                                    local velocity = (pushDir + Vector3.new(0, 0.2, 0)).Unit * 100

                                    pcall(function()
                                        setOwner:FireServer(v, v.CFrame)
                                        v.AssemblyLinearVelocity = velocity
                                        v.AssemblyAngularVelocity = Vector3.new(math.random(-10,10), math.random(-10,10), math.random(-10,10))
                                    end)
                                end
                            end
                        end
                    end
                    task.wait(0.1) 
                end
            end)
        end
    end)

    CreateToggle(AntisScroll, "Anti Kick", function(Value)
        _G.ShurikenAntiKick = Value
        local function ClearKunai()
            local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
            local destroyrem = ReplicatedStorage:FindFirstChild("MenuToys") and ReplicatedStorage.MenuToys:FindFirstChild("DestroyToy")
            if inv and destroyrem then
                for _, v in pairs(inv:GetChildren()) do
                    if v.Name == "AntiKick" or v.Name == "NinjaShuriken" then
                        pcall(function() destroyrem:FireServer(v) end)
                    end
                end
            end
        end
        if Value then
            task.spawn(function()
                local canSpawn = plr:WaitForChild("CanSpawnToy")
                local function getHRP() return plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") end
                local function CheckForHome()
                    if not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then return false end
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
                        pcall(function() stickyEvent:FireServer(kunai.StickyPart, firePart, CFrame.new(0,0,0) * CFrame.Angles(0,math.rad(90),math.rad(90))) end)
                    end
                    for _, obj in pairs(kunai:GetChildren()) do
                        if obj.Name == "Pyramid" or obj.Name == "Main" then
                            obj.CanTouch = false; obj.CanCollide = false; obj.CanQuery = false; obj.Transparency = 0
                            if not obj:FindFirstChild("Highlight") then
                                local h = Instance.new("Highlight", obj); h.FillColor = (obj.Name=="Pyramid" and Color3.new(0,0,0) or Color3.new(1,1,1))
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
                        task.spawn(function() pcall(function() spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 12, 20), Vector3.new(0,0,0)) end) end)
                    end
                    local boolik, house = CheckForHome()
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    if boolik and house then return house:WaitForChild(name, 2)
                    elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) and inv then return inv:WaitForChild(name, 2) end
                    return nil
                end
                while _G.ShurikenAntiKick do
                    task.wait(0.005)
                    if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then continue end
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
                        if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then StickKunai(kunai); kunai.Name = "AntiKick" end
                        task.wait(0.3)
                    until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                    if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then ClearKunai() end
                    pcall(function()
                        repeat task.wait(0.05) until not _G.ShurikenAntiKick or not plr.Character or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                        if not kunai or not kunai:FindFirstChild("StickyPart") or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then ClearKunai() end
                    end)
                end
            end)
        else
            _G.ShurikenAntiKick = false
            ClearKunai()
        end
    end)

    local autoGucciEnabled = false
    local autoGucciConn = nil
    local autoGucciThread = nil
    local safePos = nil
    local restoreFrames = 0

    CreateToggle(AntisScroll, "Auto Gucci", function(State)
        autoGucciEnabled = State
        
        if State then
            local function spawnBlob()
                pcall(function()
                    spawnRemote:InvokeServer("CreatureBlobman", CFrame.new(0, 5000000, 0), Vector3.new(0, 60, 0))
                end)
                
                local folder = Workspace:WaitForChild(plr.Name .. "SpawnedInToys", 5)
                if folder then
                    local blob = folder:WaitForChild("CreatureBlobman", 5)
                    if blob and blob:FindFirstChild("Head") then
                        blob.Head.CFrame = CFrame.new(0, 50000, 0)
                        blob.Head.Anchored = true
                    end
                    return blob
                end
                return nil
            end

            local function startAntiGucci()
                local char = plr.Character or plr.CharacterAdded:Wait()
                local hum = char:WaitForChild("Humanoid")
                local hrp = char:WaitForChild("HumanoidRootPart")
                
                safePos = hrp.Position
                
                local folder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                local blob = folder and folder:FindFirstChild("CreatureBlobman")
                
                if not blob then
                    spawnBlob()
                    task.wait(0.5)
                    folder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    blob = folder and folder:FindFirstChild("CreatureBlobman")
                end
                
                local seat = blob and blob:FindFirstChild("VehicleSeat")
                if seat then
                    hrp.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
                    seat:Sit(hum)
                end
                
                local jumpConn; jumpConn = hum:GetPropertyChangedSignal("Jump"):Connect(function()
                    if not autoGucciEnabled then jumpConn:Disconnect() return end
                    if hum.Jump and hum.Sit then
                        restoreFrames = 15
                        safePos = hrp.Position
                    end
                end)
                
                if autoGucciConn then autoGucciConn:Disconnect() end
                autoGucciConn = RunService.Heartbeat:Connect(function()
                    if not autoGucciEnabled then 
                        if autoGucciConn then autoGucciConn:Disconnect() end
                        return 
                    end
                    
                    if hrp and hum then
                        local ragRemote = ReplicatedStorage:FindFirstChild("CharacterEvents") and ReplicatedStorage.CharacterEvents:FindFirstChild("RagdollRemote")
                        if ragRemote then ragRemote:FireServer(hrp, 0) end
                        
                        if restoreFrames > 0 then
                            hrp.CFrame = CFrame.new(safePos)
                            restoreFrames = restoreFrames - 1
                        end
                    end
                end)
                
                task.spawn(function()
                    while hum.Sit and autoGucciEnabled do task.wait(0.5) end
                    task.wait(0.5)
                    if hrp and autoGucciEnabled then
                        hrp.CFrame = CFrame.new(safePos)
                    end
                end)
            end

            autoGucciThread = task.spawn(function()
                while autoGucciEnabled do
                    local folder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                    local blobExists = folder and folder:FindFirstChild("CreatureBlobman")
                    
                    if not blobExists then
                        if autoGucciConn then autoGucciConn:Disconnect() autoGucciConn = nil end
                        
                        spawnBlob()
                        local retries = 0
                        repeat
                            task.wait(0.2)
                            retries = retries + 1
                            folder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                        until (folder and folder:FindFirstChild("CreatureBlobman")) or retries > 25 or not autoGucciEnabled
                        
                        if autoGucciEnabled and folder and folder:FindFirstChild("CreatureBlobman") then
                            startAntiGucci()
                        end
                    end
                    task.wait(1)
                end
            end)
            
            game:GetService("StarterGui"):SetCore("SendNotification", {
                Title = "Auto Gucci",
                Text = "Enabled - Protection Active",
                Duration = 3
            })
            
        else
            autoGucciEnabled = false
            if autoGucciConn then autoGucciConn:Disconnect() autoGucciConn = nil end
            if autoGucciThread then task.cancel(autoGucciThread) autoGucciThread = nil end
            
            local folder = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            if folder and folder:FindFirstChild("CreatureBlobman") then
                if destroyToyRemote then
                    destroyToyRemote:FireServer(folder.CreatureBlobman)
                else
                    folder.CreatureBlobman:Destroy()
                end
            end
            
            safePos = nil
            restoreFrames = 0
            
            game:GetService("StarterGui"):SetCore("SendNotification", {
                Title = "Auto Gucci",
                Text = "Disabled",
                Duration = 3
            })
        end
    end)

    local antiAntiLagEnabled = false
    CreateToggle(AntisScroll, "Anti Anti Lag", function(on)
        antiAntiLagEnabled = on
        if not on then return end
        task.spawn(function()
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            local burgers = {}
            for _, v in ipairs(workspace:GetDescendants()) do
                if v.Name == "FoodHamburger" and v:IsA("Model") and v:FindFirstChild("HoldPart") then burgers[#burgers+1] = v end
            end
            local conn
            conn = workspace.DescendantAdded:Connect(function(obj)
                if not antiAntiLagEnabled then conn:Disconnect() return end
                if obj.Name == "FoodHamburger" and obj:IsA("Model") then
                    task.spawn(function()
                        local hp = obj:WaitForChild("HoldPart", 3)
                        if hp then burgers[#burgers+1] = obj end
                    end)
                end
            end)
            while antiAntiLagEnabled do
                for i = #burgers, 1, -1 do
                    local b = burgers[i]
                    if not b or not b.Parent or not b:FindFirstChild("HoldPart") then
                        table.remove(burgers, i)
                    else
                        local hp = b.HoldPart
                        pcall(function() hp.HoldItemRemoteFunction:InvokeServer(b, char) end)
                        task.wait()
                        pcall(function() hp.DropItemRemoteFunction:InvokeServer(b, CFrame.new(hrp.Position + Vector3.new(0,-2000,0)), Vector3.new(0,0,0)) end)
                    end
                end
                task.wait()
            end
        end)
    end)

    local LagScroll, LagGrid = CreateScrollingGrid(LagPage)

    CreateInput(LagScroll, "Lines Amount", function(text)
        local num = tonumber(text)
        if num then
            lagLinesAmount = num
        end
    end)

    local lagActive = false
    CreateToggle(LagScroll, "Start Lag", function(on)
        lagActive = on
        if on then
            task.spawn(function()
                while lagActive do
                    local source = workspace:FindFirstChild("SpawnLocation")
                    if not source and plr.Character then source = plr.Character:FindFirstChild("HumanoidRootPart") end

                    if source then
                        for i = 1, lagLinesAmount do
                            CreateGrabLine:FireServer(source, CFrame.new(0, 9e9, 0))
                        end
                    end
                    task.wait(1)
                end
            end)
        end
    end)
    
    local TargetScroll, TargetGrid = CreateScrollingGrid(TargetPage)
    _G.FriendWhitelistEnabled = false
    
    local PlayerListFrame = Instance.new("ScrollingFrame")
    PlayerListFrame.Size = UDim2.new(1, 0, 0, 150)
    PlayerListFrame.BackgroundTransparency = 0.5
    PlayerListFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    PlayerListFrame.Parent = KickPage
    local PlayerListLayout = Instance.new("UIListLayout"); PlayerListLayout.Parent = PlayerListFrame

    local function UpdatePlayerList()
        for _, v in pairs(PlayerListFrame:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= plr then
                local isFriend = false
                if _G.FriendWhitelistEnabled then
                    isFriend = plr:IsFriendsWith(p.UserId)
                end
                
                if not isFriend then
                    local Btn = Instance.new("TextButton")
                    Btn.Size = UDim2.new(1, 0, 0, 30)
                    Btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
                    Btn.Text = p.DisplayName .. " (@" .. p.Name .. ")"
                    Btn.TextColor3 = Color3.new(1,1,1)
                    Btn.Font = Enum.Font.GothamBold
                    Btn.TextSize = 14
                    Btn.Parent = PlayerListFrame
                    Btn.MouseButton1Click:Connect(function() selectedKickPlayer = p; TargetLabel.Text = "Target: " .. p.DisplayName end)
                end
            end
        end
        PlayerListFrame.CanvasSize = UDim2.new(0, 0, 0, PlayerListLayout.AbsoluteContentSize.Y)
    end
    task.spawn(function() while true do if KickPage.Visible then UpdatePlayerList() end; task.wait(2) end end)

    CreateToggle(TargetScroll, "WHITELIST Friends", function(val)
        _G.FriendWhitelistEnabled = val
        if KickPage.Visible then UpdatePlayerList() end
    end)

    _G.AutoSitBlobZ = true
    CreateToggle(TargetScroll, "Auto Sit Blob", function(Value)
        _G.AutoSitBlobZ = Value
    end)

    UserInputService.InputBegan:Connect(function(input, processed)
        if not processed and input.KeyCode == Enum.KeyCode.Z and _G.AutoSitBlobZ then
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            local hum = char and char:FindFirstChild("Humanoid")
            if not hrp or not hum then return end
            local folder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
            local blob = folder and folder:FindFirstChild("CreatureBlobman")
            if not blob then
                task.spawn(function()
                    if spawnRemote then
                        pcall(function() spawnRemote:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.zero) end)
                    end
                end)
                if not folder then folder = workspace:WaitForChild(plr.Name .. "SpawnedInToys", 5) end
                if folder then blob = folder:WaitForChild("CreatureBlobman", 5) end
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
                        RunService.Heartbeat:Wait()
                    until hum.SeatPart == seat or tick() - t > 1.5
                end
            end
        end
    end)

    local waterWalkerConnection
    local CachedWaterParts = {}

    local function RefreshWaterCache()
        table.clear(CachedWaterParts)
        for _, v in pairs(Workspace:GetDescendants()) do
            if v:IsA("BasePart") and (v.Name == "Water" or v.Name == "Sea" or v.Name == "Ocean") then
                table.insert(CachedWaterParts, v)
            end
        end
    end

    CreateToggle(TargetScroll, "Water Walker", function(val)
        if val then
            RefreshWaterCache()
            waterWalkerConnection = RunService.Stepped:Connect(function()
                for _, v in pairs(CachedWaterParts) do
                    if v and v.Parent then
                        v.CanCollide = true
                        v.Transparency = 0
                        v.Material = Enum.Material.SmoothPlastic
                        v.Reflectance = 0
                    end
                end
            end)
        else
            if waterWalkerConnection then
                waterWalkerConnection:Disconnect()
                waterWalkerConnection = nil
            end
            for _, v in pairs(CachedWaterParts) do
                if v and v.Parent then
                    v.CanCollide = false
                    v.Transparency = 0.5
                    v.Material = Enum.Material.Water
                    v.Reflectance = 1
                end
            end
            table.clear(CachedWaterParts)
        end
    end)

    local blobFlyActive = false
    local blobFlySpeed = 50
    local bvInstance, bgInstance
    CreateToggle(TargetScroll, "Blob Fly", function(on)
        blobFlyActive = on
        if not on then
            if bvInstance then bvInstance:Destroy(); bvInstance = nil end
            if bgInstance then bgInstance:Destroy(); bgInstance = nil end
        end
    end)

    local function GetBlobRoot()
        local char = plr.Character
        local hum = char and char:FindFirstChild("Humanoid")
        if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
            return hum.SeatPart.Parent:FindFirstChild("HumanoidRootPart") or hum.SeatPart.Parent.PrimaryPart
        end
        local folder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
        if folder then
            local blob = folder:FindFirstChild("CreatureBlobman")
            if blob then return blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart end
        end
        return nil
    end

    RunService.Heartbeat:Connect(function()
        if not blobFlyActive then return end
        local root = GetBlobRoot()
        if root then
            if not root:FindFirstChild("BlobFlyVelocity") then
                bvInstance = Instance.new("BodyVelocity")
                bvInstance.Name = "BlobFlyVelocity"
                bvInstance.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bvInstance.P = 10000
                bvInstance.Parent = root
            else bvInstance = root.BlobFlyVelocity end

            if not root:FindFirstChild("BlobFlyGyro") then
                bgInstance = Instance.new("BodyGyro")
                bgInstance.Name = "BlobFlyGyro"
                bgInstance.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                bgInstance.P = 20000; bgInstance.D = 100; bgInstance.Parent = root
            else bgInstance = root.BlobFlyGyro end

            local cam = workspace.CurrentCamera
            local moveDir = Vector3.zero
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir = moveDir - Vector3.new(0, 1, 0) end

            if bvInstance then bvInstance.Velocity = moveDir * blobFlySpeed end
            if bgInstance then bgInstance.CFrame = cam.CFrame end
        else
            if bvInstance then bvInstance:Destroy(); bvInstance = nil end
            if bgInstance then bgInstance:Destroy(); bgInstance = nil end
        end
    end)

    local loopKillAllEnabled = false
    CreateToggle(TargetScroll, "Loop Kill All", function(on)
        loopKillAllEnabled = on
        if on then
            task.spawn(function()
                while loopKillAllEnabled do
                    for _, p in pairs(Players:GetPlayers()) do
                        if not loopKillAllEnabled then break end
                        
                        local isFriend = _G.FriendWhitelistEnabled and plr:IsFriendsWith(p.UserId)
                        
                        if p ~= plr and p.Character and not isFriend then
                            local tChar = p.Character
                            local tRoot = tChar:FindFirstChild("HumanoidRootPart")
                            local tHum = tChar:FindFirstChild("Humanoid")
                            local myChar = plr.Character
                            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")

                            if tRoot and tHum and tHum.Health > 0 and myRoot then
                                local killStart = tick()
                                while tick() - killStart < 0.35 do
                                    if not loopKillAllEnabled then break end
                                    if myRoot and tRoot then
                                        myRoot.CFrame = tRoot.CFrame * CFrame.new(0,0,2)
                                        pcall(function()
                                            setOwner:FireServer(tRoot, myRoot.CFrame)
                                            tHum:ChangeState(Enum.HumanoidStateType.Dead)
                                            tHum.Health = 0
                                        end)
                                    end
                                    RunService.Heartbeat:Wait()
                                end
                            end
                        end
                    end
                    task.wait(0.5) 
                end
            end)
        end
    end)

    local thirdPersonConn
    CreateToggle(TargetScroll, "3rd Person", function(val)
        if val then
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = 100
            thirdPersonConn = RunService.RenderStepped:Connect(function()
                plr.CameraMode = Enum.CameraMode.Classic
                plr.CameraMaxZoomDistance = 100
            end)
        else
            if thirdPersonConn then thirdPersonConn:Disconnect() end
            plr.CameraMode = Enum.CameraMode.LockFirstPerson
            plr.CameraMaxZoomDistance = 0.5
        end
    end)

    CreateInput(TargetScroll, "FOV", function(text)
        local num = tonumber(text)
        if num then
            if num < 10 then num = 10 end
            if num > 120 then num = 120 end
            workspace.CurrentCamera.FieldOfView = num
        end
    end)

    local KickButtonsScroll = Instance.new("ScrollingFrame")
    KickButtonsScroll.Size = UDim2.new(1, 0, 1, -160)
    KickButtonsScroll.Position = UDim2.new(0, 0, 0, 160)
    KickButtonsScroll.BackgroundTransparency = 1
    KickButtonsScroll.ScrollBarThickness = 4
    KickButtonsScroll.Parent = KickPage
    KickButtonsScroll.CanvasSize = UDim2.new(0,0,0, 1200)

    local KickGrid = Instance.new("UIGridLayout")
    KickGrid.CellSize = UDim2.new(1, -10, 0, 40)
    KickGrid.CellPadding = UDim2.new(0, 0, 0, 5)
    KickGrid.SortOrder = Enum.SortOrder.LayoutOrder
    KickGrid.Parent = KickButtonsScroll
    
    local kickLoopEnabled = false
    CreateToggle(KickButtonsScroll, "Loop Kick", function(on)
        kickLoopEnabled = on
        if on and not selectedKickPlayer then kickLoopEnabled = false return end
        if on then
            task.spawn(function()
                while kickLoopEnabled and selectedKickPlayer and selectedKickPlayer.Parent do
                    local tChar = selectedKickPlayer.Character
                    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local tHum = tChar and tChar:FindFirstChild("Humanoid")
                    
                    myChar = plr.Character
                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    
                    if tRoot and tHum and tHum.Health > 0 and myRoot then
                        local spinCFrame = CFrame.Angles(0, math.rad(tick() * 400), 0)
                        local holdPos = myRoot.CFrame * CFrame.new(0, 10, 0) * spinCFrame
                        
                        tRoot.AssemblyLinearVelocity = Vector3.zero
                        tRoot.AssemblyAngularVelocity = Vector3.zero
                        tRoot.CFrame = holdPos
                        
                        pcall(function()
                            tHum.PlatformStand = true; tHum.Sit = true
                            setOwner:FireServer(tRoot, holdPos)
                            CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                        end)
                    end
                    RunService.Heartbeat:Wait()
                end
            end)
        end
    end)

    local loopKillEnabled = false
    CreateToggle(KickButtonsScroll, "Loop Kill", function(on)
        loopKillEnabled = on
        if on and selectedKickPlayer then
            task.spawn(function()
                while loopKillEnabled and selectedKickPlayer and selectedKickPlayer.Parent do
                    local tChar = selectedKickPlayer.Character
                    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local tHum = tChar and tChar:FindFirstChild("Humanoid")
                    local myChar = plr.Character
                    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if tRoot and tHum and tHum.Health > 0 and myRoot then
                        local attackStart = tick()
                        while tick() - attackStart < 0.35 do
                            if not loopKillEnabled then break end
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0,0,2)
                            pcall(function()
                                setOwner:FireServer(tRoot, myRoot.CFrame)
                                tHum:ChangeState(Enum.HumanoidStateType.Dead)
                                tHum.Health = 0
                            end)
                            RunService.Heartbeat:Wait()
                        end
                        task.wait(1)
                    else task.wait(0.5) end
                end
            end)
        else loopKillEnabled = false end
    end)

    local loopKickDualActive = false
    local SafeAirPosition = nil
    
    CreateToggle(KickButtonsScroll, "Dual Hand Loop Kick", function(on)
        loopKickDualActive = on
        if on then SafeAirPosition = nil end

        if on and selectedKickPlayer then
            task.spawn(function()
                while loopKickDualActive and selectedKickPlayer do
                    local target = selectedKickPlayer
                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    
                    local myChar = plr.Character
                    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    local hum = myChar and myChar:FindFirstChild("Humanoid")
                    
                    local seat = hum and hum.SeatPart
                    local blob = seat and seat.Parent
                    
                    if blob and blob.Name == "CreatureBlobman" and tHRP and myHRP then
                        local root = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
                        local leftDet = blob:FindFirstChild("LeftDetector")
                        local scriptObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
                        
                        if root and leftDet and scriptObj then
                            local grab = scriptObj:FindFirstChild("CreatureGrab")
                            local drop = scriptObj:FindFirstChild("CreatureDrop")
                            
                            if not SafeAirPosition then
                                SafeAirPosition = myHRP.Position + Vector3.new(0, 30, 0)
                            end
                            
                            if setOwner then setOwner:FireServer(root, CFrame.new(SafeAirPosition)) end
                            root.CFrame = CFrame.new(SafeAirPosition)
                            root.Velocity = Vector3.zero
                            root.RotVelocity = Vector3.zero
                            
                            pcall(function()
                                grab:FireServer(leftDet, tHRP, leftDet:FindFirstChild("LeftWeld"))
                            end)
                            
                            task.wait() 
                            
                            pcall(function()
                                drop:FireServer(leftDet:FindFirstChild("LeftWeld"), tHRP)
                            end)

                            task.wait(0.1) 
                        else
                            RunService.Heartbeat:Wait()
                        end
                    else
                        SafeAirPosition = nil
                        RunService.Heartbeat:Wait()
                    end
                end
            end)
        end
    end)

    local kickNoBlobActive = false
    CreateToggle(KickButtonsScroll, "Kick", function(on)
        kickNoBlobActive = on

        if not on and selectedKickPlayer and selectedKickPlayer.Character then
            local tHRP = selectedKickPlayer.Character:FindFirstChild("HumanoidRootPart")
            if tHRP then
                if tHRP:FindFirstChild("KickBP") then tHRP.KickBP:Destroy() end
                if tHRP:FindFirstChild("KickBG") then tHRP.KickBG:Destroy() end
            end
            return
        end

        if on and selectedKickPlayer then
            task.spawn(function()
                while kickNoBlobActive and selectedKickPlayer do
                    local target = selectedKickPlayer
                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local myChar = plr.Character
                    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")

                    if tHRP and myHRP then
                        local myPos = myHRP.Position

                        local bp = tHRP:FindFirstChild("KickBP") or Instance.new("BodyPosition", tHRP)
                        bp.Name = "KickBP"
                        bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bp.D = 200
                        bp.P = 20000 
                        bp.Position = myPos + Vector3.new(0, 15, 0)

                        local bg = tHRP:FindFirstChild("KickBG") or Instance.new("BodyGyro", tHRP)
                        bg.Name = "KickBG"
                        bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                        bg.CFrame = myHRP.CFrame

                        if tHRP.Position.Y < 2000 and destroyGrabLineEvent then
                            setOwner:FireServer(tHRP, tHRP.CFrame)
                            destroyGrabLineEvent:FireServer(tHRP)
                        end

                        local spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                        if spawned then
                            local function yeetPart(part)
                                if part then
                                    setOwner:FireServer(part, part.CFrame)
                                    if part:FindFirstChild("PartOwner") and part.PartOwner.Value == plr.Name then
                                        part.CFrame = CFrame.new(0, 10000, 0)
                                    end
                                end
                            end

                            if spawned:FindFirstChild("NinjaKunai") then
                                yeetPart(spawned.NinjaKunai:FindFirstChild("SoundPart"))
                            end
                            if spawned:FindFirstChild("NinjaShuriken") then
                                yeetPart(spawned.NinjaShuriken:FindFirstChild("SoundPart"))
                            end
                        end

                        local dist = (myHRP.Position - tHRP.Position).Magnitude
                        if tHRP.Position.Y < 2000 and dist > 25 then
                            local oldCF = myHRP.CFrame
                            myHRP.CFrame = tHRP.CFrame * CFrame.new(0, 0, 5)
                            task.wait(0.15)
                            if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                                myHRP.CFrame = oldCF
                            end
                        end
                    end
                    task.wait() 
                end
                
                if selectedKickPlayer and selectedKickPlayer.Character then
                    local tHRP = selectedKickPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if tHRP then
                        if tHRP:FindFirstChild("KickBP") then tHRP.KickBP:Destroy() end
                        if tHRP:FindFirstChild("KickBG") then tHRP.KickBG:Destroy() end
                    end
                end
            end)
        end
    end)

    local destroyGucciActive = false
    local setDestroyGucci
    setDestroyGucci = CreateToggle(KickButtonsScroll, "Destroy Target Gucci", function(on)
        destroyGucciActive = on
        if on and selectedKickPlayer then
            task.spawn(function()
                local folderName = selectedKickPlayer.Name .. "SpawnedInToys"
                local char = plr.Character
                local root = char and char:FindFirstChild("HumanoidRootPart")
                if not root then return end
                local safeSpot = root.CFrame
                while destroyGucciActive do
                    if not selectedKickPlayer or not selectedKickPlayer.Parent then
                        destroyGucciActive = false; setDestroyGucci(false); break
                    end
                    local folder = workspace:FindFirstChild(folderName)
                    if folder then
                        for _, obj in ipairs(folder:GetChildren()) do
                            if not destroyGucciActive then break end
                            if obj.Name == "CreatureBlobman" then
                                local seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
                                if seat then
                                    local myHum = plr.Character and plr.Character:FindFirstChild("Humanoid")
                                    if myHum and myHum.SeatPart ~= seat then
                                        local sitStart = tick()
                                        while tick() - sitStart < 1 do
                                            if not destroyGucciActive or myHum.SeatPart == seat then break end
                                            seat:Sit(myHum)
                                            task.wait()
                                        end
                                        if myHum.SeatPart == seat then
                                            task.wait(0.3)
                                            myHum.Sit = false; myHum.Jump = true
                                            task.wait(0.05)
                                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                                plr.Character.HumanoidRootPart.CFrame = safeSpot
                                                plr.Character.HumanoidRootPart.Velocity = Vector3.zero
                                            end
                                            task.wait(0.5)
                                        else
                                            if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                                plr.Character.HumanoidRootPart.CFrame = safeSpot
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                    task.wait(1)
                end
            end)
        else
            destroyGucciActive = false
            if not selectedKickPlayer then setDestroyGucci(false) end
        end
    end)

    local destroyAntiKickActive = false
    CreateToggle(KickButtonsScroll, "Destroy Target AntiKick", function(on)
        destroyAntiKickActive = on
        if on then
            task.spawn(function()
                local function invis_touch(part, cf) setOwner:FireServer(part, cf) end
                local function CheckAndYeet(toy)
                    local part = toy:FindFirstChild("SoundPart")
                    if part then
                        invis_touch(part, part.CFrame)
                        if part:FindFirstChild("PartOwner") and part.PartOwner.Value == plr.Name then
                            part.CFrame = CFrame.new(0, 1000, 0)
                        end
                    end
                end
                while destroyAntiKickActive do
                    local target = selectedKickPlayer
                    if target then
                        local spawned = workspace:FindFirstChild(target.Name.."SpawnedInToys")
                        if spawned then
                            if spawned:FindFirstChild("NinjaKunai") then CheckAndYeet(spawned.NinjaKunai) end
                            if spawned:FindFirstChild("NinjaShuriken") then CheckAndYeet(spawned.NinjaShuriken) end
                            if spawned:FindFirstChild("AntiKick") then CheckAndYeet(spawned.AntiKick) end
                        end
                    end
                    task.wait(0.1)
                end
            end)
        else destroyAntiKickActive = false end
    end)
    
    local loopSnowballActive = false
    CreateToggle(KickButtonsScroll, "Loop Snowballs", function(val)
        loopSnowballActive = val
        if val and selectedKickPlayer then
            task.spawn(function()
                while loopSnowballActive and selectedKickPlayer do
                    local target = selectedKickPlayer
                    local tChar = target.Character
                    local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    local myChar = plr.Character
                    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")

                    if tHRP and myHRP then
                        local bp = tHRP:FindFirstChild("SnowballTrapBP") or Instance.new("BodyPosition", tHRP)
                        bp.Name = "SnowballTrapBP"
                        bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bp.Position = myHRP.Position + Vector3.new(0, 45, 0) 

                        local bg = tHRP:FindFirstChild("SnowballTrapBG") or Instance.new("BodyGyro", tHRP)
                        bg.Name = "SnowballTrapBG"
                        bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                        bg.CFrame = CFrame.new()

                        local snowball = plr.Backpack:FindFirstChild("BallSnowball") or myChar:FindFirstChild("BallSnowball")
                        if snowball and snowball.Parent == plr.Backpack then
                            myChar.Humanoid:EquipTool(snowball)
                        end

                        if snowball and snowball:FindFirstChild("Handle") then
                            local handle = snowball.Handle
                            if setOwner then setOwner:FireServer(handle, handle.CFrame) end
                            handle.CFrame = tHRP.CFrame * CFrame.new(0, 5, 0)
                            handle.Velocity = Vector3.new(0, -150, 0)
                        end
                    end
                    task.wait(0.05)
                end
                
                if selectedKickPlayer and selectedKickPlayer.Character and selectedKickPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local tHRP = selectedKickPlayer.Character.HumanoidRootPart
                    if tHRP:FindFirstChild("SnowballTrapBP") then tHRP.SnowballTrapBP:Destroy() end
                    if tHRP:FindFirstChild("SnowballTrapBG") then tHRP.SnowballTrapBG:Destroy() end
                end
            end)
        end
    end)

    
    local ExplosivesLabel = Instance.new("TextLabel")
    ExplosivesLabel.Text = "--- TOY ABUSE ---"
    ExplosivesLabel.Size = UDim2.new(1, 0, 0, 30)
    ExplosivesLabel.BackgroundTransparency = 1
    ExplosivesLabel.TextColor3 = Color3.fromRGB(255, 230, 0)
    ExplosivesLabel.Font = Enum.Font.GothamBlack
    ExplosivesLabel.TextSize = 16
    ExplosivesLabel.Parent = KickButtonsScroll

    local ToyList = {
        {Name = "Missile", Real = "BombMissile", Part = "PartHitDetector"},
        {Name = "Firework", Real = "FireworkMissile", Part = "PartHitDetector"},
        {Name = "Balloon Bomb", Real = "BombBalloon", Part = "Balloon"},
        {Name = "Dark Matter", Real = "BombDarkMatter", Part = "PartHitDetector"},
        {Name = "Snowball", Real = "BallSnowball", Part = "SoundPart"}
    }

    local selectedToyData = nil
    local loopExplodeActive = false
    local dropdownButtons = {} 

    local function AttackTargetWithToy(toyName, partName)
        if not selectedKickPlayer or not selectedKickPlayer.Character then return end
        local tHRP = selectedKickPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not tHRP then return end

        local myChar = plr.Character
        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then return end

        local spawnPos = myHRP.CFrame * CFrame.new(0, 5, -5)
        
        task.spawn(function()
            pcall(function() 
                if spawnRemote then
                    spawnRemote:InvokeServer(toyName, spawnPos, Vector3.zero) 
                end
            end)
        end)

        local folder = workspace:WaitForChild(plr.Name.."SpawnedInToys", 3)
        if not folder then return end
        
        local toy = folder:WaitForChild(toyName, 3)
        if not toy then return end
        
        local trigger = toy:WaitForChild(partName, 3)

        if trigger then
            if setOwner then setOwner:FireServer(trigger, trigger.CFrame) end
            
            local startTime = tick()
            
            local targetPos = tHRP.Position
            for _, v in pairs(workspace:GetChildren()) do
                if v.Name == "PlayerCharacterLocationDetector" and v:IsA("BasePart") then
                    if (v.Position - tHRP.Position).Magnitude < 10 then
                        targetPos = v.Position
                        break
                    end
                end
            end

            trigger.CFrame = CFrame.new(targetPos + Vector3.new(0, 100, 0))
            trigger.AssemblyLinearVelocity = Vector3.zero
            trigger.AssemblyAngularVelocity = Vector3.zero

            task.wait(0.01)

            while tick() - startTime < 1 and toy.Parent and tHRP.Parent do
                local currentTargetPos = tHRP.Position
                
                for _, v in pairs(workspace:GetChildren()) do
                    if v.Name == "PlayerCharacterLocationDetector" and v:IsA("BasePart") then
                        if (v.Position - currentTargetPos).Magnitude < 10 then
                            currentTargetPos = v.Position
                            break
                        end
                    end
                end

                trigger.CFrame = CFrame.new(currentTargetPos)
                trigger.AssemblyLinearVelocity = Vector3.new(0, -20000, 0) 
                trigger.AssemblyAngularVelocity = Vector3.new(1000, 1000, 1000)

                RunService.Heartbeat:Wait()
            end
        end
    end

    CreateToggle(KickButtonsScroll, "Loop TOY ATTACK", function(val)
        loopExplodeActive = val
        if val then
            if not selectedKickPlayer then
                loopExplodeActive = false
                TargetLabel.Text = "SELECT A TARGET FIRST!"
                task.wait(1)
                TargetLabel.Text = "Target: None"
                return
            end
            
            if not selectedToyData then
                loopExplodeActive = false
                TargetLabel.Text = "SELECT A TOY FIRST!"
                task.wait(1)
                TargetLabel.Text = "Target: None"
                return
            end

            task.spawn(function()
                while loopExplodeActive and selectedKickPlayer and selectedToyData do
                    AttackTargetWithToy(selectedToyData.Real, selectedToyData.Part)
                    task.wait(1.5) 
                end
                loopExplodeActive = false
            end)
        end
    end)

    local DropdownFrame = Instance.new("Frame")
    DropdownFrame.Name = "ToyDropdown"
    DropdownFrame.Size = UDim2.new(0, 200, 0, 200)
    DropdownFrame.Position = UDim2.new(0.5, -100, 0.5, -100) 
    DropdownFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    DropdownFrame.BorderSizePixel = 2
    DropdownFrame.BorderColor3 = Color3.fromRGB(255, 230, 0)
    DropdownFrame.Visible = false
    DropdownFrame.ZIndex = 20
    DropdownFrame.Parent = KickPage 

    local DropList = Instance.new("UIListLayout", DropdownFrame)
    DropList.SortOrder = Enum.SortOrder.LayoutOrder
    DropList.Padding = UDim.new(0, 2)

    local DropCorner = Instance.new("UICorner", DropdownFrame)
    DropCorner.CornerRadius = UDim.new(0, 8)

    local SelectContainer = Instance.new("Frame")
    SelectContainer.BackgroundColor3 = Color3.fromRGB(0,0,0)
    SelectContainer.BackgroundTransparency = 0.6
    SelectContainer.Parent = KickButtonsScroll
    local SC_Corner = Instance.new("UICorner", SelectContainer); SC_Corner.Parent = SelectContainer

    local SelectBtn = Instance.new("TextButton")
    SelectBtn.Size = UDim2.new(1, 0, 1, 0)
    SelectBtn.BackgroundTransparency = 1
    SelectBtn.Text = "-- SELECT TOY --"
    SelectBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    SelectBtn.Font = Enum.Font.GothamBold
    SelectBtn.TextSize = 14
    SelectBtn.Parent = SelectContainer

    SelectBtn.MouseButton1Click:Connect(function()
        DropdownFrame.Visible = not DropdownFrame.Visible
    end)

    for _, toy in ipairs(ToyList) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, 0, 0, 35)
        btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        btn.Text = toy.Name
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Font = Enum.Font.Gotham
        btn.TextSize = 14
        btn.Parent = DropdownFrame
        btn.ZIndex = 21
        
        table.insert(dropdownButtons, btn)

        btn.MouseButton1Click:Connect(function()
            selectedToyData = toy
            SelectBtn.Text = "[ " .. toy.Name .. " ]"
            
            for _, b in pairs(dropdownButtons) do
                b.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            end
            
            btn.BackgroundColor3 = Color3.fromRGB(50, 255, 50) 
            
            DropdownFrame.Visible = false
        end)
    end

    local function createColorBtn(name, color3)
        local Btn = Instance.new("TextButton")
        Btn.Name = name
        Btn.Text = name
        Btn.BackgroundColor3 = color3
        Btn.TextColor3 = (name == "Белый" or name == "Желтый") and Color3.new(0,0,0) or Color3.new(1,1,1)
        Btn.Font = Enum.Font.GothamBold
        Btn.TextSize = 14
        Btn.Parent = UIPage
        local Corner = Instance.new("UICorner"); Corner.Parent = Btn
        
        Btn.MouseButton1Click:Connect(function() 
            if _G.NightMode then return end 
            MainFrame.BackgroundColor3 = color3 
        end)
    end
    local ColorGrid = Instance.new("UIGridLayout"); ColorGrid.CellSize = UDim2.new(0, 80, 0, 40); ColorGrid.FillDirectionMaxCells = 4; ColorGrid.Parent = UIPage
    createColorBtn("Красный", Color3.fromRGB(255, 60, 60))
    createColorBtn("Черный", Color3.fromRGB(30, 30, 30))
    createColorBtn("Серый", Color3.fromRGB(128, 128, 128))
    createColorBtn("Белый", Color3.fromRGB(240, 240, 240))
    createColorBtn("Желтый", Color3.fromRGB(255, 230, 0))

    switchPage(AntisPage)
    UserInputService.InputBegan:Connect(function(input) 
        if input.KeyCode == Enum.KeyCode.Insert then 
            MainFrame.Visible = not MainFrame.Visible 
        end 
    end)

    print("RIP Hub Loaded Successfully")
end

if isfile(FileName) then
    local Success, Result = pcall(function() return HttpService:JSONDecode(readfile(FileName)) end)
    if Success and Result and Result.Key then
        for _, key in ipairs(ValidKeys) do
            if key == Result.Key then
                Launch_RIP_Hub()
                return
            end
        end
    end
end

if CoreGui:FindFirstChild("RIPKeySystem") then
    CoreGui.RIPKeySystem:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RIPKeySystem"
ScreenGui.Parent = CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 350, 0, 180)
MainFrame.Position = UDim2.new(0.5, -175, 0.5, -90)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local Corner = Instance.new("UICorner", MainFrame)
Corner.CornerRadius = UDim.new(0, 10)

local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Color = Color3.fromRGB(255, 230, 0)
Stroke.Thickness = 2

local Title = Instance.new("TextLabel")
Title.Text = "RIP HUB PROTECTION"
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.Parent = MainFrame

local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(0.8, 0, 0, 40)
KeyInput.Position = UDim2.new(0.1, 0, 0.35, 0)
KeyInput.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyInput.PlaceholderText = "Введите ключ..."
KeyInput.Font = Enum.Font.Gotham
KeyInput.TextSize = 14
KeyInput.Text = ""
KeyInput.Parent = MainFrame
Instance.new("UICorner", KeyInput).CornerRadius = UDim.new(0, 6)

local CheckBtn = Instance.new("TextButton")
CheckBtn.Size = UDim2.new(0.5, 0, 0, 35)
CheckBtn.Position = UDim2.new(0.25, 0, 0.7, 0)
CheckBtn.BackgroundColor3 = Color3.fromRGB(255, 230, 0)
CheckBtn.Text = "LOGIN"
CheckBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
CheckBtn.Font = Enum.Font.GothamBold
CheckBtn.TextSize = 14
CheckBtn.Parent = MainFrame
Instance.new("UICorner", CheckBtn).CornerRadius = UDim.new(0, 6)

CheckBtn.MouseButton1Click:Connect(function()
    local enteredKey = KeyInput.Text:gsub("^%s*(.-)%s*$", "%1")
    local found = false
    for _, key in ipairs(ValidKeys) do 
        if key == enteredKey then 
            found = true 
            break 
        end 
    end
    
    if found then
        writefile(FileName, HttpService:JSONEncode({Key = enteredKey, User = LocalPlayer.Name}))
        Launch_RIP_Hub()
    else
        KeyInput.Text = "INVALID KEY!"
        KeyInput.TextColor3 = Color3.fromRGB(255, 0, 0)
        task.wait(1)
        KeyInput.Text = ""
        KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
end)
