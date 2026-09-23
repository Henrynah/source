--Posuu_verified
Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer

--whitelist = {
--    ["M4rt1_Fr1"] = true,
--    ["NICOWEZEL"] = true,
--    ["thecrazyteen"] = true,
--    ["emiyaExe"] = true,
--    ["EmiyaExe_mobile"] = true,
--    ["ratwowee"] = true,
--    ["Kaaanto3"] = true,
--    ["AshtvnGetBanPfff"] = true,
--    ["julyan200307_2"] = true,
--    ["IStepOnEGirls"] = true,
--    ["Julyan200307_2"] = true,
--    ["ftaptest"] = true,
--    ["goodcoconet"] = true,
--    ["RandomHubBestHubFrFr"] = true,
--    ["lovechickennuggetz14"] = true,
--    ["BombasticArab2"] = true,
--    ["Chl0eoff"] = true,
--    ["MSKCNCHEN_TH"] = true,
--    ["Test002533533"] = true,
--    ["bedwars5051"] = true,
--    ["MSKCNCHEN_TH"] = true,
--    ["78cvzl"] = true,
--    ["ZachPlaysRoblox12332"] = true,
--    ["Posuu_verified"] = true,
--}

--if not whitelist[LocalPlayer.Name] then
--    LocalPlayer:Kick("You're not whitelisted hoe dm gruisbackfromthedead to get whitelisted or dm nicoooo1547.")
--    return
-- end

local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
local Window = Rayfield:CreateWindow({
   Name = "WNCLY HUBBB",
   Icon = 0,
   LoadingTitle = "loading YES",
   LoadingSubtitle = "by lax and gru and NICO , yanis (wses)",
   ShowText = "Hi",
   Theme = {
    TextColor = Color3.fromRGB(230, 220, 255),
    Background = Color3.fromRGB(3, 0, 8),
    Topbar = Color3.fromRGB(8, 0, 15),
    Shadow = Color3.fromRGB(110, 40, 200),

    NotificationBackground = Color3.fromRGB(6, 0, 12),
    NotificationActionsBackground = Color3.fromRGB(30, 10, 50),

    TabBackground = Color3.fromRGB(6, 0, 12),
    TabStroke = Color3.fromRGB(120, 40, 220),
    TabBackgroundSelected = Color3.fromRGB(70, 15, 150),

    TabTextColor = Color3.fromRGB(210, 200, 250),
    SelectedTabTextColor = Color3.fromRGB(255, 240, 255),

    ElementBackground = Color3.fromRGB(8, 0, 15),
    ElementBackgroundHover = Color3.fromRGB(12, 0, 20),

    SecondaryElementBackground = Color3.fromRGB(8, 0, 15),
    ElementStroke = Color3.fromRGB(100, 30, 200),
    SecondaryElementStroke = Color3.fromRGB(12, 0, 20),

    SliderBackground = Color3.fromRGB(8, 0, 15),
    SliderProgress = Color3.fromRGB(160, 70, 255),
    SliderStroke = Color3.fromRGB(130, 40, 220),

    ToggleBackground = Color3.fromRGB(8, 0, 15),
    ToggleEnabled = Color3.fromRGB(160, 70, 255),
    ToggleDisabled = Color3.fromRGB(50, 20, 70),
    ToggleEnabledStroke = Color3.fromRGB(15, 0, 25),
    ToggleDisabledStroke = Color3.fromRGB(10, 0, 20),
    ToggleEnabledOuterStroke = Color3.fromRGB(200, 120, 255),
    ToggleDisabledOuterStroke = Color3.fromRGB(120, 50, 200),

    DropdownSelected = Color3.fromRGB(70, 15, 150),
    DropdownUnselected = Color3.fromRGB(8, 0, 15),

    InputBackground = Color3.fromRGB(8, 0, 15),
    InputStroke = Color3.fromRGB(130, 40, 220),

    PlaceholderColor = Color3.fromRGB(120, 90, 180)
},
   DisableRayfieldPrompts = true,
   DisableBuildWarnings = true,
   ConfigurationSaving = { Enabled = true, FolderName = nil, FileName = "lax hub" },
   Discord = { Enabled = false, Invite = "noinvitelink", RememberJoins = true },
   KeySystem = false,
   KeySettings = {
      Title = "lax hub v1",
      Subtitle = "Key System",
      Note = "",
      FileName = "Key",
      SaveKey = true,
      GrabKeyFromSite = true,
      Key = {"laxlebg"}
   }
})

 banner = playergui
if banner then
    local rayfield = banner:FindFirstChild("Rayfield")
    if rayfield and rayfield:FindFirstChild("Loading") then
        rayfield:FindFirstChild("Loading").Visible = true
        local imageId = rayfield.Loading:WaitForChild("Banner", 5)

        if imageId then

            imageId.Image = "rbxassetid://140311070669016"
            imageId.Size = UDim2.new(0, 262, 0, 262)
            imageId.ImageTransparency = 0.5
            imageId.Position = UDim2.new(0.5, 0, 0.5, 0)
            local cornerBro = Instance.new("UICorner")
            cornerBro.Parent = imageId
            cornerBro.CornerRadius = UDim.new(0, 200)



            local cloneimage = imageId:Clone()
            cloneimage.Size = UDim2.new(0.65, 262, 0.65, 262)
            cloneimage.Position = UDim2.new(0.475, 0, 0.6, 0)
            cloneimage.Image = "rbxassetid://140311070669016"
            cloneimage.ImageTransparency = 0.75
            cloneimage.Parent = imageId.Parent
            cloneimage.ScaleType = Enum.ScaleType.Crop
            cloneimage.Name = "Banner2"
        else
            warn("Banner not found inside Rayfield.Loading")
        end
    else
        warn("Rayfield or Loading not found in HUI")
    end
else
    warn("HUI not found in CoreGui")
end


Players = game:GetService("Players")
ReplicatedStorage = game:GetService("ReplicatedStorage")
 RunService = game:GetService("RunService")
 Workspace = game:GetService("Workspace")
UserInputService = game:GetService("UserInputService")
LocalPlayer = Players.LocalPlayer

connections = {}
 whitelistedPlayers = {
    "ilovebaguette",
    "78cvzl",
}
function isWhitelisted(player)
    if not player then return false end
    local username = type(player) == "string" and player or player.Name
    
    for _, name in ipairs(whitelistedPlayers) do
        if username == name then
            return true
        end
    end
    if type(player) ~= "string" and player.UserId then
        return LocalPlayer:IsFriendsWith(player.UserId)
    end
    return false
end

mainTab = Window:CreateTab("Main", "user")


 plr = game:GetService("Players")
 TeleportService = game:GetService("TeleportService")
 HttpService = game:GetService("HttpService")
 GuiService = game:GetService("GuiService")
PlaceId = game.PlaceId
JobId = game.JobId
 LocalPlayer = Players.LocalPlayer

autoRejoinEnabled = false

 mainTab:CreateButton({
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

mainTab:CreateButton({
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

mainTab:CreateToggle({
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

mainTab:CreateButton({
	Name = "Close Script",
	Callback = function()
		Rayfield:Destroy()
	end,
})

mainTab:CreateDivider()



mainTab:CreateDivider()


plr = game.Players.LocalPlayer
cam = workspace.CurrentCamera
mouse = plr:GetMouse()
uis = game:GetService("UserInputService")
inv = workspace:WaitForChild(plr.Name.."SpawnedInToys")
rs = game:GetService("ReplicatedStorage")
rs2 = game:GetService("RunService")
deb = game:GetService("Debris")
slideTPT = nil
slideTPD = false

function tpF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    if not slideTPT then
        if char and hrp and mouse.Target and not slideTPT then hrp.CFrame = CFrame.new(mouse.Hit.x, mouse.Hit.y + 5, mouse.Hit.z) end
    elseif slideTPT then
        if not slideTPD and slideTPT and mouse.Target then
            slideTPD = true
            local info = TweenInfo.new(
                0.5, 
                Enum.EasingStyle.Sine, 
                Enum.EasingDirection.In,
                0,
                false,
                0
            )
            local info2 = TweenInfo.new(
                0.5, 
                Enum.EasingStyle.Sine, 
                Enum.EasingDirection.In,
                0,
                true,
                0
            )
            local e = {["CFrame"] = CFrame.new(mouse.Hit.x, mouse.Hit.y + 3, mouse.Hit.z)}
            local e2 = {FieldOfView = 100}
            char.Head.CanCollide = false
            char.Torso.CanCollide = false
            game:GetService("TweenService"):Create(hrp, info, e):Play()
            game:GetService("TweenService"):Create(cam, info2, e2):Play()
            wait(0.55)
            char.Head.CanCollide = false
            char.Torso.CanCollide = false
            cam.FieldOfView = 70
            slideTPD = false
        end
    end
end

mainTab:CreateKeybind({
    Name = "TP",
    CurrentKeybind = "F",
    HoldToInteract = false,
    Flag = "tpKeybindFlag",
    Callback = function(Keybind)
        tpF()
    end
})

mainTab:CreateToggle({
    Name = "SlideTP",
    CurrentValue = false,
    Flag = "slideTPToggleFlag",
    Callback = function(Value)
        slideTPT = Value
    end
})

mainTab:CreateSection("Help")

escapeEnabled = false
 struggleConnection = nil
isHeld = false

function startStruggle()
    if struggleConnection then return end

    struggleConnection = RunService.Heartbeat:Connect(function()
        if not escapeEnabled then return end

        local character = LocalPlayer.Character
        local isHeldValue = character and character:FindFirstChild("IsHeld")
        if not isHeldValue then return end

        local currentlyHeld = isHeldValue.Value == true

        if currentlyHeld and not isHeld then
            Rayfield:Notify({ Title = "Grabbed!", Content = "Struggling to escape...", Duration = 2 })
        end

        if currentlyHeld then
            ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
        end

        isHeld = currentlyHeld
    end)
end

function stopStruggle()
    if struggleConnection then
        struggleConnection:Disconnect()
        struggleConnection = nil
    end
    isHeld = false
end

mainTab:CreateToggle({
    Name = "Escape FasterTEST(Auto Struggle)",
    CurrentValue = false,
    Flag = "EscapeToggle",
    Callback = function(Value)
        escapeEnabled = Value
        if Value then
            startStruggle()
        else
            stopStruggle()
        end
    end
})

mainTab:CreateKeybind({
    Name = "Toggle Escape",
    CurrentKeybind = "J",
    HoldToInteract = false,
    Flag = "EscapeKeybind",
    Callback = function()
        local toggle = Rayfield.Flags.EscapeToggle
        toggle:Set(not toggle.CurrentValue)
    end,
})

plr = game.Players.LocalPlayer
 cam = workspace.CurrentCamera
mouse = plr:GetMouse()
uis = game:GetService("UserInputService")
inv = workspace:WaitForChild(plr.Name.."SpawnedInToys")
rs = game:GetService("ReplicatedStorage")
rs2 = game:GetService("RunService")
deb = game:GetService("Debris")

function stopVelocityF()
    local char = plr.Character
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
    hrp.AssemblyLinearVelocity = Vector3.zero
end

mainTab:CreateKeybind({
   Name = "Stop velocity",
   CurrentKeybind = "G",
   HoldToInteract = false,
   Flag = "deleteFlag",
   Callback = function(Keybind)
        stopVelocityF()
    end
})

mainTab:CreateSection("Player")

 Players = game:GetService("Players")
 RunService = game:GetService("RunService")
Stats = game:GetService("Stats")
LocalPlayer = Players.LocalPlayer

MenuGui = LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("MenuGui")
 GameCorrectionsGui = LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("GameCorrectionsGui")
DataPing = Stats.Network.ServerStatsItem["Data Ping"]

TweenFrame = GameCorrectionsGui.TweenFrame
 TabContents = MenuGui.Menu.TabContents
 Settings = TabContents.Settings
LineColors = Settings.Contents.LineFrame.LineColorsNumbers
Shop = TabContents.Shop
 DestroyToy = TabContents.ToyDestroy
 ToyShop = TabContents.ToyShop
 Toys = TabContents.Toys
OriginalCoinsLabel = MenuGui.TopRight.CoinsFrame.CoinsDisplay.Coins
 CloneCoinsLabel = OriginalCoinsLabel:Clone()

 UIEnhanced = false
 PingConnection = nil
FPSConnection = nil
 RealPing = 0
function EnableUIEnhancer()
    if UIEnhanced then return end
    UIEnhanced = true

    MenuGui.Menu.TabBar.BackgroundTransparency = 0.5
    TabContents.BackgroundTransparency = 1

    TweenFrame.BackgroundTransparency = 1
    TweenFrame.NonsenseGrab.BackgroundTransparency = 0.8
    TweenFrame.Flying.BackgroundTransparency = 0.7
    TweenFrame.Flying.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

    Settings.BackgroundTransparency = 1
    Settings.Contents.BackgroundTransparency = 0.5
    Settings.Title.BackgroundTransparency = 0.3
    Settings.Title.Position = UDim2.new(0.505, 0, 0, 0)
    Settings.Title.Size = UDim2.new(1.01, 0, 0.1, 0)
    Settings.Contents.MaterialsFrame.Frame.BackgroundTransparency = 1

    for _, box in pairs({LineColors.HSV.H.TextBox, LineColors.HSV.S.TextBox, LineColors.HSV.V.TextBox,
                         LineColors.RGB.R.TextBox, LineColors.RGB.G.TextBox, LineColors.RGB.B.TextBox}) do
        box.BackgroundTransparency = 0.5
    end

    Shop.Contents.BackgroundTransparency = 0.5
    Shop.Title.BackgroundTransparency = 0.3
    Shop.Title.Position = UDim2.new(0.505, 0, 0, 0)
    Shop.Title.Size = UDim2.new(1.01, 0, 0.1, 0)

    DestroyToy.Contents.BackgroundTransparency = 0.5
    DestroyToy.Title.BackgroundTransparency = 0.3
    DestroyToy.Title.Position = UDim2.new(0.505, 0, 0, 0)
    DestroyToy.Title.Size = UDim2.new(1.01, 0, 0.1, 0)
    DestroyToy.MeterFrame.BackgroundTransparency = 0.5
    DestroyToy.MeterFrame.Meter.BackgroundTransparency = 0.7
    if DestroyToy:FindFirstChild("DropShadow") then DestroyToy.DropShadow:Destroy() end

    ToyShop.Contents.BackgroundTransparency = 0.5
    ToyShop.Title.BackgroundTransparency = 0.3
    ToyShop.Title.Position = UDim2.new(0.505, 0, 0, 0)
    ToyShop.Title.Size = UDim2.new(1.01, 0, 0.1, 0)
    ToyShop.SortingTabs.BackgroundTransparency = 0.5
    ToyShop.SortingTabs.BackgroundColor3 = Color3.fromRGB(100, 100, 100)

    Toys.Contents.BackgroundTransparency = 0.5
    Toys.Title.BackgroundTransparency = 0.3
    Toys.Title.Position = UDim2.new(0.505, 0, 0, 0)
    Toys.Title.Size = UDim2.new(1.01, 0, 0.1, 0)
    Toys.SortingTabs.BackgroundTransparency = 0.5
    Toys.SortingTabs.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    Toys.FavoritesFrame.BackgroundTransparency = 1
    Toys.MeterFrame.BackgroundTransparency = 0.5
    Toys.MeterFrame.Meter.BackgroundTransparency = 0.7
    for _, shadow in pairs(Toys:GetChildren()) do
        if shadow.Name == "DropShadow" then shadow:Destroy() end
    end

    MenuGui.TopRight.CoinsFrame.BackgroundTransparency = 0.5
    if MenuGui.TopRight.CoinsFrame.CoinsDisplay:FindFirstChild("CoinImage") then
        MenuGui.TopRight.CoinsFrame.CoinsDisplay.CoinImage:Destroy()
    end
    MenuGui.TopRight.CoinsFrame.CoinsDisplay.Size = UDim2.new(0.9, 0, 1, 0)

    OriginalCoinsLabel.TextTransparency = 1
    OriginalCoinsLabel.Size = UDim2.new(0, 0, 0, 0)

    CloneCoinsLabel.Parent = MenuGui.TopRight.CoinsFrame.CoinsDisplay
    CloneCoinsLabel.Name = "CoinsLabel_m1kp"
    CloneCoinsLabel.TextTransparency = 0
    CloneCoinsLabel.Position = UDim2.new(1, 10, 0, 4)
    CloneCoinsLabel.Size = UDim2.new(0, 0, 1, 0)

    for _, btn in pairs(Shop.ShopInfoFrameHolder:GetChildren()) do
        if btn:IsA("Frame") then
            btn.BackgroundTransparency = 0.5
            if btn:FindFirstChild("PassBackButton") then btn.PassBackButton.BackgroundTransparency = 0.5 end
            if btn:FindFirstChild("PassBuyButton") then btn.PassBuyButton.BackgroundTransparency = 0.5 end
        end
    end

    for _, btn in pairs(MenuGui.Menu.TabBar.Tabs:GetChildren()) do
        if btn:IsA("ImageButton") and btn:FindFirstChild("UICorner") then
            btn.UICorner.CornerRadius = UDim.new(0.2, 0)
        end
    end

    PingConnection = RunService.RenderStepped:Connect(function(delta)
        RealPing = tonumber(DataPing:GetValueString(math.round(2/delta)):match("%d+")) or 0
    end)

    FPSConnection = task.spawn(function()
        while UIEnhanced do
            local Display = MenuGui.TopRight.CoinsFrame.CoinsDisplay
            local Label = Display:FindFirstChild("CoinsLabel_m1kp")
            if Label then
                local fps = RunService.RenderStepped:Wait()
                Label.Text = "Coins: " .. OriginalCoinsLabel.Text ..
                             "\nFPS: " .. math.round(1/fps) ..
                             "\nPing: " .. math.round(RealPing)
            end
            task.wait(1)
        end
    end)

    Rayfield:Notify({ Title = "UI Enhanced", Content = "TYSM LAX", Duration = 3 })
end
 function DisableUIEnhancer()
    if not UIEnhanced then return end
    UIEnhanced = false

    if PingConnection then PingConnection:Disconnect() PingConnection = nil end
    if FPSConnection then task.cancel(FPSConnection) FPSConnection = nil end


    CloneCoinsLabel:Destroy()
    OriginalCoinsLabel.TextTransparency = 0
    OriginalCoinsLabel.Size = UDim2.new(1, 0, 1, 0)

    Rayfield:Notify({ Title = "UI Reset", Content = "UI restaurée à l'original.", Duration = 3 })
end

mainTab:CreateToggle({
    Name = "UI so tuff use it def",
    CurrentValue = false,
    Flag = "UIEnhancerToggle",
    Callback = function(Value)
        if Value then
            EnableUIEnhancer()
        else
            DisableUIEnhancer()
        end
    end
})


 WaterWalk = mainTab:CreateToggle({
    Name = "Water Walk(put shuriken under you)",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        local v1 = game:GetService("Workspace").Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
        for _,prt in pairs(v1:GetChildren()) do 
            if prt:IsA("Part") and prt.Name == "Ocean" then 
                prt.CanCollide = Value
            end
        end
        if Value then
            Rayfield:Notify({
                Title = "Water Walk has been enabled",
                Content = "Water Walk",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Water Walk has been disabled",
                Content = "Water Walk",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

local runService = game:GetService("RunService")
local players = game:GetService("Players")
local localPlayer = players.LocalPlayer
local character = localPlayer.Character
local humanoid = character and character:WaitForChild("Humanoid")
local humanoidRootPart = character and character:WaitForChild("HumanoidRootPart")
local playerUserId = localPlayer.UserId
local playerName = localPlayer.Name

localPlayer.CharacterAdded:Connect(function(char)
    character = char
    humanoid = character:WaitForChild("Humanoid")
    humanoidRootPart = character:WaitForChild("HumanoidRootPart")
end)

local infJumpConnection = nil
local originalMaxZoom = LocalPlayer.CameraMaxZoomDistance
local originalMinZoom = LocalPlayer.CameraMinZoomDistance
local originalCameraMode = LocalPlayer.CameraMode

mainTab:CreateToggle({
   Name = "Speed Boost",
   CurrentValue = false,
   Flag = "SpeedBoostToggle",
   Callback = function(Value)
       tpwalking = Value
   end,
})

mainTab:CreateSlider({
   Name = "Speed",
   Range = {0, 5},
   Increment = 0.1,
   Suffix = "*",
   CurrentValue = 0.1,
   Flag = "SpeedBoostSlider",
   Callback = function(Value)
       tspeed = Value
   end,
})

runService.Heartbeat:Connect(function()
    if tpwalking and character and humanoid then
        if humanoid.MoveDirection.Magnitude > 0 then
            if tspeed then
                 humanoidRootPart.CFrame = humanoidRootPart.CFrame + (humanoid.MoveDirection * tspeed)
            end
        end
    end
end)

mainTab:CreateToggle({
   Name = "Jump Boost",
   CurrentValue = false,
   Flag = "JumpBoostToggle",
   Callback = function(Value)
       humanoid.UseJumpPower = not Value
   end,
})

mainTab:CreateSlider({
   Name = "Jump",
   Range = {7.2, 500},
   Increment = 0.1,
   Suffix = "%",
   CurrentValue = 7.2,
   Flag = "JumpBoostSlider",
   Callback = function(Value)
       humanoid.JumpHeight = Value
   end,
})

mainTab:CreateToggle({
    Name = "Infinite Jump",
    Default = false,
    Callback = function(Value)
        if infJumpConnection then
            infJumpConnection:Disconnect()
            infJumpConnection = nil
        end

        if Value then
            infJumpDebounce = false
            infJumpConnection = UserInputService.JumpRequest:Connect(function()
                if not infJumpDebounce then
                    infJumpDebounce = true
                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
                        LocalPlayer.Character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
                    end
                    task.wait()
                    infJumpDebounce = false
                end
            end)
        end
    end
})

ps9999999FUCK  = nil

mainTab:CreateSection("Local Player")

mainTab:CreateSlider({
   Name = "Gravity",
   Range = {0, 192.6},
   Increment = 0.1,
   Suffix = "%",
   CurrentValue = 192.6,
   Flag = "GravitySlider",
   Callback = function(Value)
       workspace.Gravity = Value
   end,
})

mainTab:CreateSlider({
   Name = "FOV",
   Range = {0, 120},
   Increment = 0.1,
   Suffix = "%",
   CurrentValue = 70,
   Flag = "FOVSlider",
   Callback = function(Value)
       workspace.CurrentCamera.FieldOfView = Value
   end,
})

mainTab:CreateToggle({
    Name = "Third person",
    Default = false,
    Callback = function(Value)
        if Value then
            originalMaxZoom = LocalPlayer.CameraMaxZoomDistance
            originalMinZoom = LocalPlayer.CameraMinZoomDistance
            originalCameraMode = LocalPlayer.CameraMode

            LocalPlayer.CameraMaxZoomDistance = math.huge
            LocalPlayer.CameraMinZoomDistance = 0.5
            LocalPlayer.CameraMode = Enum.CameraMode.Classic
        else
            LocalPlayer.CameraMaxZoomDistance = originalMaxZoom
            LocalPlayer.CameraMinZoomDistance = originalMinZoom
            LocalPlayer.CameraMode = originalCameraMode
        end
    end
})

mainTab:CreateSection("Remove Limbs")

local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = game.Players.LocalPlayer
local isActive = false
local connection
local voidPos = CFrame.new(-9999, -999999, -9999)
local selectedLimbs = {}

local function startLoop()
    if connection then connection:Disconnect() end
    connection = RunService.Heartbeat:Connect(function()
        if not isActive then return end
        local character = player.Character
        if not character then return end
        local humanoid = character:FindFirstChild("Humanoid")
        if not humanoid then return end

        local rootPart = character:FindFirstChild("HumanoidRootPart")
        if not rootPart then return end
        local hasLimbs = false
        for _, limbName in ipairs(selectedLimbs) do
            if character:FindFirstChild(limbName) then
                hasLimbs = true
                break
            end
        end
        if hasLimbs then
            workspace.FallenPartsDestroyHeight = -10
            humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
            ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("RagdollRemote"):FireServer(rootPart, 1)
            
            task.wait(0.1)
            for _, limbName in ipairs(selectedLimbs) do
                local limb = character:FindFirstChild(limbName)
                if limb then
                    limb:PivotTo(voidPos)
                end
            end
        else
            humanoid.HipHeight = 2
            workspace.FallenPartsDestroyHeight = -500
        end
    end)
end

local function onCharacterAdded(char)
    char.ChildAdded:Connect(function(part)
        if table.find(selectedLimbs, part.Name) then
            startLoop()
        end
    end)

    char.ChildRemoved:Connect(function(part)
        if table.find(selectedLimbs, part.Name) then
            local humanoid = char:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.HipHeight = 2
            end
        end
    end)

    startLoop()
end

local function disableScript()
    if connection then
        connection:Disconnect()
        connection = nil
    end
    workspace.FallenPartsDestroyHeight = -500
    isActive = false
end

mainTab:CreateDropdown({
    Name = "Select Limbs to Remove of you",
    Options = {"Right Arm", "Left Arm", "Right Leg", "Left Leg"},
    CurrentOption = {},
    MultipleOptions = true,
    Flag = "LimbsDropdown",
    Callback = function(Options)
        selectedLimbs = Options
        if isActive then
            startLoop()
        end
    end
})

mainTab:CreateToggle({
    Name = "remove choosen limbs",
    Key = false,
    Value = false,
    Callback = function(state)
        isActive = state
        if not state then
            disableScript()
        elseif #selectedLimbs > 0 then
            startLoop()
        end
    end
})

player.CharacterAdded:Connect(onCharacterAdded)
if player.Character then
    onCharacterAdded(player.Character)
end

mainTab:CreateDivider()

mainTab:CreateKeybind({
   Name = "delete limbs of others",
   CurrentKeybind = "U",
   HoldToInteract = false,
   Flag = "deleteFlag",
   Callback = function()
        local GrabParts = game.workspace:FindFirstChild("GrabParts")
        if not GrabParts then return end 
        local grab = GrabParts:FindFirstChild("GrabPart")
        if not grab then return end 
        local torso = grab:FindFirstChild("WeldConstraint").Part1
        local plrtar = torso.Parent 
        local rl,ll = plrtar["Right Leg"], plrtar["Left Leg"]
        rl.CFrame = CFrame.new(0, -10000, 0)
        ll.CFrame = CFrame.new(0, -10000, 0)
    end    
})

mainTab:CreateKeybind({
   Name = "Delete Arms of others",
   CurrentKeybind = "Y",
   HoldToInteract = false,
   Flag = "deleteFlag",
   Callback = function()
        local GrabParts = game.workspace:FindFirstChild("GrabParts")
        if not GrabParts then return end 
        local grab = GrabParts:FindFirstChild("GrabPart")
        if not grab then return end 
        local torso = grab:FindFirstChild("WeldConstraint").Part1
        local plrtar = torso.Parent 
        local ra,la = plrtar["Right Arm"], plrtar["Left Arm"]
        ra.CFrame = CFrame.new(0, -10000, 0)
        la.CFrame = CFrame.new(0, -10000, 0)
    end    
})


mainTab:CreateSection("Fun animation's'")

spinning = false

function startSpinning()
    while spinning do
        local char = game.Players.LocalPlayer.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(9999999999999999), 0)
        end
        task.wait(0)
    end
end

mainTab:CreateToggle({
    Name = "Spin Character",
    Default = false,
    Callback = function(Value)
        spinning = Value
        if spinning then
            task.spawn(startSpinning)
        end
    end
})

game.Players.LocalPlayer.CharacterAdded:Connect(function()
    if spinning then
        task.spawn(startSpinning)
    end
end)

mainTab:CreateDivider()



local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local activeTracks = {}

local function createAnimationToggle(name, animId, speed, weight, loopSpeed)
    local track = nil
    local active = false

    mainTab:CreateToggle({
        Name = name,
        CurrentValue = false,
        Flag = name .. "Toggle",
        Callback = function(value)
            active = value
            if value then
                local anim = Instance.new("Animation")
                anim.AnimationId = "rbxassetid://" .. animId
                track = humanoid:LoadAnimation(anim)
                table.insert(activeTracks, track)

                if loopSpeed then
                    task.spawn(function()
                        while active and task.wait() do
                            if not track.IsPlaying then
                                track:Play(0.1, weight or 1, loopSpeed)
                            end
                        end
                    end)
                else
                    track:Play(0.1, weight or 1, speed or 1)
                end
            else
                if track then
                    track:Stop()
                    track = nil
                end
            end
        end
    })
end

-- R6 Animations
createAnimationToggle("Head Throw", 35154961, 1, 1, 1)
createAnimationToggle("Floating Head", 121572214, 1, 1)
createAnimationToggle("Crouch", 182724289, 1, 1)
createAnimationToggle("Floor Crawl", 282574440, 1, 1)
createAnimationToggle("Dino Walk", 204328711, 1, 1)
createAnimationToggle("Jumping Jacks", 429681631, 1, 1)
createAnimationToggle("Loop Head", 35154961, 1, 1, 1e6)
createAnimationToggle("Hero Jump", 184574340, 1, 1, 1)
createAnimationToggle("Faint", 181526230, 1, 1)
createAnimationToggle("Floor Faint", 181525546, 1, 1, 2)
createAnimationToggle("Super Faint", 181525546, 0.5, 1, 40)
createAnimationToggle("Levitate", 313762630, 1, 1)
createAnimationToggle("Dab", 183412246, 1, 1, 1)
createAnimationToggle("Spinner", 188632011, 1, 1, 2)
createAnimationToggle("Float Sit", 179224234, 1, 1)
createAnimationToggle("Moving Dance", 429703734, 1, 1, 1)
createAnimationToggle("Weird Move", 215384594, 1, 1)
createAnimationToggle("Clone Illusion", 215384594, 1, 1, 1e7)
createAnimationToggle("Glitch Levitate", 313762630, 1, 1, 1e7)
createAnimationToggle("Spin Dance", 429730430, 1, 1, 1)
createAnimationToggle("Moon Dance", 45834924, 1, 1, 1)
createAnimationToggle("Full Punch", 204062532, 1, 1, 1)
createAnimationToggle("Spin Dance 2", 186934910, 1, 1, 1)
createAnimationToggle("Bow Down", 204292303, 1, 1, 3)
createAnimationToggle("Sword Slam", 204295235, 1, 1, 1)
createAnimationToggle("Loop Slam", 204295235, 1, 1, 1e4)
createAnimationToggle("Mega Insane", 184574340, 0.5, 1, 40)
createAnimationToggle("Super Punch", 126753849, 1, 1, 3)
createAnimationToggle("Full Swing", 218504594, 1, 1, 1)
createAnimationToggle("Arm Turbine", 259438880, 1, 1, 1e3)
createAnimationToggle("Barrel Roll", 136801964, 1, 1, 1)
createAnimationToggle("Scared", 180612465, 1, 1, 1)
createAnimationToggle("Insane", 33796059, 1, 1, 1e8)
createAnimationToggle("Arm Detach", 33169583, 1, 1, 1e6)
createAnimationToggle("Sword Slice", 35978879, 1, 1)
createAnimationToggle("Insane Arms", 27432691, 1, 1, 1e4)

createAnimationToggle("Crazy Slash", 674871189, 1, 1, 1)
createAnimationToggle("Open", 582855105, 1, 1, 1)
createAnimationToggle("R15 Spinner", 754658275, 1, 1, 1)
createAnimationToggle("Arms Out", 582384156, 1, 1, 1)
createAnimationToggle("Float Slash", 717879555, 1, 1, 1)
createAnimationToggle("Weird Zombie", 708553116, 1, 1, 1)
createAnimationToggle("Down Slash", 746398327, 1, 1, 1)
createAnimationToggle("Pull", 675025795, 1, 1, 1)
createAnimationToggle("Circle Arm", 698251653, 1, 1, 1)
createAnimationToggle("Bend", 696096087, 1, 1, 1)
createAnimationToggle("Rotate Slash", 675025570, 1, 1, 1)
createAnimationToggle("Fling Arms", 754656200, 1, 1, 10)

LocalPlayer.CharacterAdded:Connect(function(newChar)
    humanoid = newChar:WaitForChild("Humanoid")
    for _, track in pairs(activeTracks) do
        if track then track:Stop() end
    end
    activeTracks = {}
end)

LocalPlayer.CharacterAdded:Connect(function(newChar)
    humanoid = newChar:WaitForChild("Humanoid")
    for _, track in pairs(activeTracks) do
        if track then track:Stop() end
    end
    activeTracks = {}
end)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local humanoid = LocalPlayer.Character:WaitForChild("Humanoid")
local animator = humanoid:WaitForChild("Animator")


local typingAnimation = Instance.new("Animation")
typingAnimation.AnimationId = "rbxassetid://18353618958"
local typingTrack = animator:LoadAnimation(typingAnimation)


local crouchAnimation = Instance.new("Animation")
crouchAnimation.AnimationId = "rbxassetid://6980229055"
local crouchTrack = animator:LoadAnimation(crouchAnimation)


local throwedAnimation = Instance.new("Animation")
throwedAnimation.AnimationId = "rbxassetid://7047322890"
local throwedTrack = animator:LoadAnimation(throwedAnimation)


local animation1 = Instance.new("Animation") 
animation1.AnimationId = "rbxassetid://33796059"
local animationWalk = Instance.new("Animation")
animationWalk.AnimationId = "rbxassetid://95415492"
local animationJump = Instance.new("Animation")
animationJump.AnimationId = "rbxassetid://165167557"
local animationFall = Instance.new("Animation")
animationFall.AnimationId = "rbxassetid://97170520"
local newIdleAnimation = Instance.new("Animation") 
newIdleAnimation.AnimationId = "rbxassetid://15517864808"

local idleTrack = humanoid:LoadAnimation(animation1)
local walkAnimTrack = humanoid:LoadAnimation(animationWalk)
local jumpTrack = humanoid:LoadAnimation(animationJump)
local fallTrack = humanoid:LoadAnimation(animationFall)
local newIdleTrack = humanoid:LoadAnimation(newIdleAnimation)

local psychoActive = false
local runningConnection
local jumpPlaying = false

local function playIdle()
	task.spawn(function()
		while psychoActive do
			if humanoid.MoveDirection.Magnitude == 0 and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping and humanoid:GetState() ~= Enum.HumanoidStateType.Freefall then
				idleTrack:Play(0.1, 1, 100)
				task.wait(0.2)
				idleTrack:Stop()
				task.wait(1.5)
				newIdleTrack:Play()
				newIdleTrack:AdjustSpeed(5)
				task.wait(0.3)
				newIdleTrack:Stop()
				task.wait(1.5)
			else
				task.wait(0.1)
			end
		end
	end)
end

local function stopIdle()
	idleTrack:Stop()
	newIdleTrack:Stop()
end

mainTab:CreateToggle({
	Name = "Typing Animation",
	Default = false,
	Callback = function(state)
		if state then typingTrack:Play() else typingTrack:Stop() end
	end
})

mainTab:CreateToggle({
	Name = "Crouch Animation",
	Default = false,
	Callback = function(state)
		if state then crouchTrack:Play() else crouchTrack:Stop() end
	end
})

mainTab:CreateToggle({
	Name = "Throwed Animation",
	Default = false,
	Callback = function(state)
		if state then throwedTrack:Play() else throwedTrack:Stop() end
	end
})

mainTab:CreateToggle({
	Name = "Psycho Animation",
	Default = false,
	Callback = function(state)
		psychoActive = state
		if state then
			humanoid.WalkSpeed = 9
			playIdle()
			if runningConnection then runningConnection:Disconnect() end
			runningConnection = humanoid:GetPropertyChangedSignal("MoveDirection"):Connect(function()
				if psychoActive and humanoid.MoveDirection.Magnitude > 0 and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping and humanoid:GetState() ~= Enum.HumanoidStateType.Freefall then
					stopIdle()
					if not walkAnimTrack.IsPlaying then walkAnimTrack:Play() end
				else
					walkAnimTrack:Stop()
				end
			end)
			humanoid.StateChanged:Connect(function(_, newState)
				if newState == Enum.HumanoidStateType.Jumping then
					if not jumpPlaying then
						stopIdle()
						walkAnimTrack:Stop()
						jumpPlaying = true
						jumpTrack:Play()
						jumpTrack.Stopped:Wait()
						jumpPlaying = false
					end
				elseif newState == Enum.HumanoidStateType.Freefall then
					stopIdle()
					jumpTrack:Stop()
					if not fallTrack.IsPlaying then fallTrack:Play() end
				elseif newState == Enum.HumanoidStateType.Landed then
					fallTrack:Stop()
					playIdle()
				end
			end)
		else
			humanoid.WalkSpeed = 16
			stopIdle()
			walkAnimTrack:Stop()
			jumpTrack:Stop()
			fallTrack:Stop()
			if runningConnection then runningConnection:Disconnect() end
		end
	end
})


LocalPlayer.CharacterAdded:Connect(function(character)
	humanoid = character:WaitForChild("Humanoid")
	animator = humanoid:WaitForChild("Animator")
	typingTrack = animator:LoadAnimation(typingAnimation)
	crouchTrack = animator:LoadAnimation(crouchAnimation)
	throwedTrack = animator:LoadAnimation(throwedAnimation)
	idleTrack = humanoid:LoadAnimation(animation1)
	walkAnimTrack = humanoid:LoadAnimation(animationWalk)
	jumpTrack = humanoid:LoadAnimation(animationJump)
	fallTrack = humanoid:LoadAnimation(animationFall)
	newIdleTrack = humanoid:LoadAnimation(newIdleAnimation)
 end)


isJerking = false
 animtrack = nil
 animation = nil
animationSpeed = 0.1

 function Notify(Title, Text, Duration)
    game:GetService('StarterGui'):SetCore('SendNotification', {
        Title = Title,
        Text = Text or '',
        Duration = Duration or 5
    })
end

 function getmodel()
    local char = plr.Character or plr.CharacterAdded:Wait()
    local hum = char:FindFirstChild("Humanoid") or char:WaitForChild("Humanoid")
    return hum.RigType == Enum.HumanoidRigType.R15 and "R15" or "R6"
end

 function startJerk()
    if isJerking then return end
    local char = plr.Character or plr.CharacterAdded:Wait()
    local hum = char:FindFirstChild("Humanoid") or char:WaitForChild("Humanoid")
    local anim = hum:FindFirstChildOfClass("Animator") or hum:WaitForChild("Animator")
    local pack = plr:FindFirstChild("Backpack") or plr:WaitForChild("Backpack")
    if workspace:FindFirstChild("aaa") then
        workspace:FindFirstChild("aaa"):Destroy()
    end
    animation = Instance.new("Animation")
    animation.Name = "aaa"
    animation.Parent = workspace
    animation.AnimationId = getmodel() == "R15" and "rbxassetid://698251653" or "rbxassetid://72042024"
    isJerking = true
    Notify("Jerk Animation", "Animation started! Toggle off to stop.", 3)
    spawn(function()
        while isJerking do
            if not animtrack then
                animtrack = anim:LoadAnimation(animation)
            end
            animtrack:Play()
            animtrack:AdjustSpeed(animationSpeed)
            animtrack.TimePosition = 0.7
            task.wait(0.3)
            while isJerking and animtrack and animtrack.TimePosition < 0.7 do
                task.wait(1)
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
    if animation and animation.Parent then
        animation:Destroy()
        animation = nil
    end
    Notify("Jerk Animation", "Animation stopped!", 3)
end

mainTab:CreateToggle({
    Name = "Jerk Animation",
    CurrentValue = false,
    Flag = "JerkToggle",
    Callback = function(Value)
        if Value then
            startJerk()
        else
            stopJerk()
        end
    end
})

mainTab:CreateSlider({
    Name = "Animation Speed",
    Range = {0.05, 10},
    Increment = 0.05,
    Suffix = "anim speed",
    CurrentValue = 0.1,
    Flag = "JerkSpeed",
    Callback = function(Value)
        animationSpeed = Value
        Notify("Speed Changed", "New speed: " .. Value .. "x", 2)
        if isJerking and animtrack then
            animtrack:AdjustSpeed(Value)
        end
    end,
})

plr.CharacterAdded:Connect(function()
    if isJerking then
        task.wait(0)
        startJerk()
    end
end)

Notify("Jerk Made By Sai", 2.5)

local AntiTab = Window:CreateTab("Anti's and protection'")

antiGrab1T = false
 antiGrab1AnchorT = false

 function antiGrab1F()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    if not hrp or not hum then return end
    
    while antiGrab1T and task.wait() do
        if LocalPlayer.IsHeld.Value == true and antiGrab1T == true then
            if hrp ~= nil then
                if antiGrab1AnchorT then
                    hrp.Anchored = true
                    while LocalPlayer.IsHeld.Value == true do 
                        ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                        task.wait(0.001) 
                    end
                    hrp.Anchored = false
                elseif not antiGrab1AnchorT then
                    while LocalPlayer.IsHeld.Value == true do 
                        ReplicatedStorage.CharacterEvents.Struggle:FireServer(LocalPlayer)
                        task.wait(0.001) 
                    end
                end
            end
        end
    end
end

playerName = LocalPlayer.Name

 humanoid, rootPart, seat = nil
 antiGucciConnection = nil
safePosition = nil
 restoreFrames = 0

function spawnBlobman()
    local args = {
        [1] = "CreatureBlobman",
        [2] = CFrame.new(0, 5000000, 0),
        [3] = Vector3.new(0, 60, 0)
    }
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))

    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("CreatureBlobman") then
        local blob = folder.CreatureBlobman
        if blob:FindFirstChild("Head") then
            blob.Head.CFrame = CFrame.new(0, 50000, 0)
            blob.Head.Anchored = true
        end
    end
end

function startAntiGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    local folder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    seat = blob and blob:FindFirstChild("VehicleSeat")

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

    if antiGucciConnection then antiGucciConnection:Disconnect() end
    antiGucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do
            task.wait(0)
        end
        task.wait(0)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

 function stopAntiGucci()
    if antiGucciConnection then
        antiGucciConnection:Disconnect()
        antiGucciConnection = nil
    end
    local blobFolder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
        blobFolder.CreatureBlobman:Destroy()
    end
end

--15244, 836, 14255

    AntiTab:CreateToggle({
    Name = "Auto Gucci",
    Default = false,
    Callback = function()
        spawnBlobman()
        startAntiGucci()
    end,
})

 humanoid, rootPart, seat = nil
 antiGucciConnection = nil
safePosition = nil
 restoreFrames = 0

function spawnBlobman()
    local args = {
        [1] = "CreatureBlobman",
        [2] = CFrame.new(0, 5000000, 0),
        [3] = Vector3.new(0, 60, 0)
    }
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))

    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("CreatureBlobman") then
        local blob = folder.CreatureBlobman
        if blob:FindFirstChild("Head") then
            blob.Head.CFrame = CFrame.new(0, 50000, 0)
            blob.Head.Anchored = true
        end
    end
end

function startAntiGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    local folder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    local blob = folder and folder:FindFirstChild("CreatureBlobman")
    seat = blob and blob:FindFirstChild("VehicleSeat")

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

    if antiGucciConnection then antiGucciConnection:Disconnect() end
    antiGucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do
            task.wait(0)
        end
        task.wait(0)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

 function stopAntiGucci()
    if antiGucciConnection then
        antiGucciConnection:Disconnect()
        antiGucciConnection = nil
    end
    local blobFolder = Workspace:FindFirstChild(playerName.."SpawnedInToys")
    if blobFolder and blobFolder:FindFirstChild("CreatureBlobman") then
        blobFolder.CreatureBlobman:Destroy()
    end
end

local gucciEnabled = false
local gucciConnection = nil
local tractor = nil
local vehicleSeat = nil
local safePosition = nil
local restoreFrames = 0

function spawnTractor()
    local args = {
        [1] = "TractorGreen",
        [2] = CFrame.new(0, 5000000, 0),
        [3] = Vector3.new(0, 60, 0)
    }
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))

    local folder = Workspace:WaitForChild(playerName.."SpawnedInToys", 5)
    if folder and folder:FindFirstChild("TractorGreen") then
        tractor = folder.TractorGreen
        vehicleSeat = tractor:FindFirstChild("VehicleSeat")
        if tractor:FindFirstChild("Main") then
            tractor.Main.CFrame = CFrame.new(0, 50000, 0)
            tractor.Main.Anchored = true
        end
    end
end

function startGucci()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local rootPart = character:WaitForChild("HumanoidRootPart")
    safePosition = rootPart.Position

    if vehicleSeat and vehicleSeat:IsA("VehicleSeat") then
        rootPart.CFrame = vehicleSeat.CFrame + Vector3.new(0, 2, 0)
        vehicleSeat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal("Jump"):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            restoreFrames = 15
            safePosition = rootPart.Position
        end
    end)


    if gucciConnection then gucciConnection:Disconnect() end
    gucciConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then return end
        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        if restoreFrames > 0 then
            rootPart.CFrame = CFrame.new(safePosition)
            restoreFrames = 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do
            task.wait(1)
        end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(safePosition)
    end)
end

function stopGucci()
    if gucciConnection then
        gucciConnection:Disconnect()
        gucciConnection = nil
    end
    if tractor then
        ReplicatedStorage.MenuToys.DestroyToy:FireServer(tractor)
        tractor:Destroy()
        tractor = nil
        vehicleSeat = nil
    end
end

 GucciToggle = AntiTab:CreateToggle({
    Name = "Invisible (can't use vehicle after that')",
    CurrentValue = false,
    Flag = "GucciTractorToggle",
    Callback = function(enabled)
        gucciEnabled = enabled
        if enabled then
            spawnTractor()
            task.wait(0.5)
            if tractor then
                startGucci()
                Rayfield:Notify({ Title = "Gucci ON", Content = "Tractor spawned & sat", Duration = 3 })
            else
                Rayfield:Notify({ Title = "Error", Content = "Failed to spawn Tractor", Duration = 3 })
            end
        else
            stopGucci()
            Rayfield:Notify({ Title = "Gucci OFF", Content = "Tractor destroyed", Duration = 3 })
        end
    end
})

ps3 = nil

AntiTab:CreateToggle({
    Name = "Anti Grab",
    Default = false,
    Callback = function(Value)
        antiGrab1T = Value
        if Value then
            spawn(antiGrab1F)
        end
    end
})


freezeDuration = -0
frozen = false
 antiGrabEnabled = false
 reinforceConnection = nil

function getCharacter(player)
    return player.Character or player.CharacterAdded:Wait()
end

function freezeCharacter()
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

function reinforceAntiGrab()
    if not antiGrabEnabled then return end

    local beingHeld = LocalPlayer:FindFirstChild("IsHeld")
    if beingHeld then
        reinforceConnection = RunService.Heartbeat:Connect(function()
            if not antiGrabEnabled then
                reinforceConnection:Disconnect()
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
 function onCharacterAdded(character)
    if not antiGrabEnabled then return end  

    local humanoid = character:WaitForChild("Humanoid")  
    reinforceAntiGrab()  

    humanoid.Died:Connect(function()
        if antiGrabEnabled then
            reinforceAntiGrab()
        end
    end)
end

Toggle = nil
Toggle = AntiTab:CreateToggle({
   Name = "AG freeze (anti grab test)",
   CurrentValue = false,
   Flag = "Toggle_AntiGrabFreeze",
   Callback = function(Value)
       antiGrabEnabled = Value  

       if Value then
           reinforceAntiGrab()
           print("⚡AG ON")
       else
           if reinforceConnection then
               reinforceConnection:Disconnect()  
           end
           print(" Anti-Grab")
       end
   end,
})

LocalPlayer.CharacterAdded:Connect(function(character)
    if antiGrabEnabled then
        onCharacterAdded(character)
    end
end)

LocalPlayer.CharacterAdded:Connect(function(character)
    resetAntiGrabOnDeath(character)
end)

AntiGrabTp = AntiTab:CreateToggle({
    Name = "Anti Grab Tp(good for vehicles)",
    CurrentValue = false,
    Flag = "AntiGrabTp",
    Callback = function(Value)
        antiGrabTpEnabled = Value
        if Value then
            task.spawn(function()
                while antiGrabTpEnabled do
                    local humanoidRootPart, humanoid = getAll()
                    if humanoid and checkHum(humanoid) then
                        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(100, 0, 0)
                        task.wait(0.08)
                        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(0, 0, 100)
                        task.wait(0.08)
                        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(-100, 0, 0)
                        task.wait(0.08)
                        humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(0, 0, -100)
                        task.wait(0.08)
                    end
                    task.wait(0.1)
                end
            end)
        end
    end
})

 antiFlingEnabled = false
 antiFlingConnection = nil
lastSafePosition = nil
 checkInterval = 0.1

 function updateSafePosition()
    local character = LocalPlayer.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    if hrp then
        lastSafePosition = hrp.Position
    end
end

function resetToSafePosition()
    local character = LocalPlayer.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    if hrp and lastSafePosition then
        hrp.CFrame = CFrame.new(lastSafePosition)
        hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        hrp.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
    end
end

function applyAntiFling()
    local character = LocalPlayer.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local currentPos = hrp.Position
    local distance = lastSafePosition and (currentPos - lastSafePosition).Magnitude or 0

    if distance > 20 then
        resetToSafePosition()
        updateSafePosition()
        return
    end
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            for _, part in pairs(plr.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CollisionGroup = "PlotPlayers"
                    local limb = part:FindFirstChild("RagdollLimbPart")
                    if limb then
                        limb.CollisionGroup = "PlotPlayers"
                    end
                end
            end
        end
    end
    for _, part in pairs(character:GetChildren()) do
        if part:IsA("BasePart") then
            part.CollisionGroup = "Players"
            local limb = part:FindFirstChild("RagdollLimbPart")
            if limb then
                limb.CollisionGroup = "Players"
            end
        end
    end

    updateSafePosition()
end

function startAntiFling()
    if antiFlingConnection then return end
    updateSafePosition()
    antiFlingConnection = RunService.Heartbeat:Connect(function()
        if antiFlingEnabled then
            applyAntiFling()
        end
    end)
end

function stopAntiFling()
    if antiFlingConnection then
        antiFlingConnection:Disconnect()
        antiFlingConnection = nil
    end
    for _, plr in pairs(Players:GetPlayers()) do
        if plr.Character then
            for _, part in pairs(plr.Character:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CollisionGroup = "Players"
                    local limb = part:FindFirstChild("RagdollLimbPart")
                    if limb then
                        limb.CollisionGroup = "Players"
                    end
                end
            end
        end
    end
end

AntiTab:CreateToggle({
    Name = "Anti-Fling (Reset Position)",
    CurrentValue = false,
    Flag = "AntiFlingToggle",
    Callback = function(Value)
        antiFlingEnabled = Value
        if Value then
            startAntiFling()
        else
            stopAntiFling()
        end
    end
})

 Services = {
    Players = game:GetService("Players"),
    ReplicatedStorage = game:GetService("ReplicatedStorage"),
    RunService = game:GetService("RunService")
}

LocalPlayer = Services.Players.LocalPlayer
 ReplicatedStorage = Services.ReplicatedStorage

 antiGrabLoopEnabled = false
antiGrabTpEnabled = false

Events = {
    Struggle = ReplicatedStorage:WaitForChild("CharacterEvents"):WaitForChild("Struggle")
}

function getAll()
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    local humanoid = character:FindFirstChild("Humanoid")
    return humanoidRootPart, humanoid
end

function checkHum(humanoid)
    return humanoid and humanoid.Health > 0 and humanoid:GetState() ~= Enum.HumanoidStateType.Dead
end

 AntiGrabLoop = AntiTab:CreateToggle({
    Name = "Anti Grab Loop( if sm1 loop grab u)",
    CurrentValue = false,
    Flag = "AntiGrabLoop",
    Callback = function(Value)
        antiGrabLoopEnabled = Value

        if Value then
            task.spawn(function()
                while antiGrabLoopEnabled do
                    Events.Struggle:FireServer(LocalPlayer)
                    task.wait(0.1)
                end
            end)
        end
    end
})

 antiok = true

AntiBlob = AntiTab:CreateToggle({
    Name = "Anti Ownership Kick",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
		antiok = Value
		while antiok do
			if game.Players.LocalPlayer.Character then
				if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					local distance = Vector3.new(game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.X^2,game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.Y^2,game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.Z^2)
					if math.sqrt(distance.X+distance.Y+distance.Z) > 10000000 then
						game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = SL
					end
				end
			end
			task.wait()
		end
    end,
})

task.spawn(function()
		while antiok do
		if game.Players.LocalPlayer.Character then
			if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				local distance = Vector3.new(game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.X^2,game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.Y^2,game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame.Position.Z^2)
				if math.sqrt(distance.X+distance.Y+distance.Z) > 10000000 then
					game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart").CFrame = SL
				end
			end
		end
		task.wait()
	end
end)

AntiTab:CreateSection("Anti Kick")

 currentKunai = nil
lokunaiSpawningEnabled = false

function spawnItemCf(itemName, cframe)
    local rotation = Vector3.new(90, 90, 0)
    local toy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(itemName, cframe, rotation)
    return toy
end

function attachKunaiToThigh()
    if currentKunai and currentKunai.Parent then 
        return 
    end
    local playerCharacter = localPlayer.Character or localPlayer.CharacterAdded:Wait()
    local torso = playerCharacter:WaitForChild("Torso", 5)
    if torso then
        local kunaiPosition = torso.CFrame
            * CFrame.new(-0.5, -torso.Size.Y / 2, 0)
            * CFrame.Angles(math.rad(-100), 0, 0)
        spawnItemCf("NinjaKunai", kunaiPosition)
        for i = 1, 20 do
            local toysFolder = workspace:FindFirstChild(localPlayer.Name .. "SpawnedInToys")
            if toysFolder then
                currentKunai = toysFolder:FindFirstChild("NinjaKunai")
                if currentKunai then
                    break
                end
            end
            task.wait()
        end
        if currentKunai then
            currentKunai.AncestryChanged:Connect(function(_, parent)
                if not parent and kunaiSpawningEnabled then
                    currentKunai = nil
                    attachKunaiToThigh()
                end
            end)
        else
            task.wait(1)
            attachKunaiToThigh()
        end
    else
        warn("Torso not found!")
    end
end

 function monitorKunai()
    while kunaiSpawningEnabled do
        if not (currentKunai and currentKunai.Parent) then
            currentKunai = nil
            attachKunaiToThigh()
        end
        task.wait()
    end
end

 function initializeKunai()
    attachKunaiToThigh()
    task.spawn(monitorKunai)
end

function disableKunai()
    kunaiSpawningEnabled = false
    if currentKunai then
        currentKunai:Destroy()
        currentKunai = nil
    end
end

AntiTab:CreateToggle({
    Name = "Anti-Kick",
    Default = false,
    Callback = function(enabled)
        if enabled then
            kunaiSpawningEnabled = true
            initializeKunai()
        else
            disableKunai()
        end
    end
})

Services = {
    Players = game:GetService("Players"),
    Workspace = game:GetService("Workspace")
}

 LocalPlayer = Services.Players.LocalPlayer
 Workspace = Services.Workspace

 antiBlobKickEnabled = false
playerBackpack = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")

function getPlayerBackpack()
    return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
end

function breakBlobDetectors(blob)
    if blob and blob.Name == "CreatureBlobman" then
        local backpack = getPlayerBackpack()
        if backpack and blob.Parent ~= backpack then
            local leftDetector = blob:FindFirstChild("LeftDetector")
            local rightDetector = blob:FindFirstChild("RightDetector")
            if leftDetector and rightDetector then
                local leftAttach = leftDetector:FindFirstChild("AttachPlayer")
                local rightAttach = rightDetector:FindFirstChild("AttachPlayer")
                if leftAttach then
                    leftAttach:Destroy()
                end
                if rightAttach then
                    rightAttach:Destroy()
                end
            end
        end
    end
end

AK = false

Penis = AntiTab:CreateToggle({
    Name = "Penis Anti-Kick",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
		AK = Value
		while AK do
		    while not game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild("ToolPencil") do
		        if game.Players.LocalPlayer.Character then
    		        if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
    		            game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("ToolPencil",CFrame.new(game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position)+Vector3.new(0,0,15),Vector3.new(0,0,0))
    		        end
    		    end
    		    task.wait()
    		end
    		if game.Players.LocalPlayer.Character then
    		    if game.Players.LocalPlayer.Character:FindFirstChild("Torso") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild("ToolPencil") then
    		        if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil:FindFirstChild("StickyPart") then
    		            if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart:FindFirstChild("StickyWeld") then
    		                if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
    		                    local a = game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.SoundPart.CFrame.Position
    		                    local b = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame.Position
    		                    local distance = Vector3.new((a.X-b.X)^2,(a.Y-b.Y)^2,(a.Z-b.Z)^2)
    		                    if math.sqrt(distance.X+distance.Y+distance.Z) > 20 then
    		                        game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil)
    		                    else
    		                        game.ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart,game.Players.LocalPlayer.Character.Torso,CFrame.new(0,-1,0) * CFrame.Angles(0,math.pi,0))
	                    		    for _,prt in pairs(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil:GetChildren()) do
	                    		        if prt.ClassName == "Part" then
	                    		            prt.CanQuery = false
	                    		        end
                        		    end
                        		    task.wait(0.2)
                        		    if game.Players.LocalPlayer.Character then
                            		    if game.Players.LocalPlayer.Character:FindFirstChild("Torso") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys"):FindFirstChild("ToolPencil") then
                            		        if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil:FindFirstChild("StickyPart") then
                            		            if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart:FindFirstChild("StickyWeld") then
                            		                if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
                            		                    if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil then
                            		                        if game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.StickyPart.StickyWeld.Part1 ~= game.Players.LocalPlayer.Character.Torso then
                            		                            game.ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.SoundPart,game.workspace:FindFirstChild(game.Players.LocalPlayer.Name.."SpawnedInToys").ToolPencil.SoundPart.CFrame)
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

AntiTab:CreateSection("Anti Blob")

 Players = game:GetService("Players")
 LocalPlayer = Players.LocalPlayer
 antiSeatResetEnabled = false
lastSeat = nil
function startAntiSeatReset()
    task.spawn(function()
        while antiSeatResetEnabled and task.wait(0.1) do
            local character = LocalPlayer.Character
            if character then
                local humanoid = character:FindFirstChild("Humanoid")
                if humanoid then
                    local isHeld = LocalPlayer:FindFirstChild("IsHeld")
                    if isHeld and isHeld.Value then
                        if humanoid.SeatPart then
                            lastSeat = humanoid.SeatPart
                        end
                        if lastSeat and humanoid.SeatPart ~= lastSeat then
                            while isHeld.Value and task.wait() do end
                            pcall(function()
                                lastSeat:Sit(humanoid)
                            end)
                        end
                    end
                end
            end
        end
    end)
end

AntiTab:CreateToggle({
    Name = "Anti Seat Reset",
    CurrentValue = false,
    Flag = "AntiSeatReset",
    Callback = function(enabled)
        antiSeatResetEnabled = enabled
        if enabled then
            lastSeat = nil
            startAntiSeatReset()
            Rayfield:Notify({ 
                Title = "Anti Seat Reset",
                Content = "Enabled",
                Duration = 2 
            })
        else
            Rayfield:Notify({ 
                Title = "Anti Seat Reset", 
                Content = "Disabled", 
                Duration = 2 
            })
        end
    end
})

antiblob = false

AntiBlob = AntiTab:CreateToggle({
    Name = "Antiblob seatandowner",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
		antiblob = Value
		while antiblob do
			if game.Players.LocalPlayer.Character then
				if not game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
					local tp = Instance.new("Part")
					tp.Parent = game.Players.LocalPlayer.Character
					tp.Name = "TruePositionPart"
					tp.Anchored = true
					tp.CFrame = CFrame.new(0,-100,0)
				end
				for _,prt in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
					if prt.ClassName == "Part" then
						if prt.Massless then
							prt.Massless = false
							for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
								if plot.Name ~= "PlayersInPlots" then
									for _,itm in pairs(plot:GetChildren()) do
										if itm.Name == "CreatureBlobman" then
											if itm:FindFirstChild("BlobmanSeatAndOwnerScript") and itm:FindFirstChild("RightDetector") and game.Players.LocalPlayer.Character then
												if itm.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop") and itm.RightDetector:FindFirstChild("RightWeld") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
													itm.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(itm.RightDetector["RightWeld"],game.Players.LocalPlayer.Character.HumanoidRootPart)
													game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
												end
											end
										end
									end
								end
							end
							for _,plr in pairs(game.Players:GetChildren()) do
								if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
									for _,itm in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
										if itm.Name == "CreatureBlobman" then
											if itm:FindFirstChild("BlobmanSeatAndOwnerScript") and itm:FindFirstChild("RightDetector") and game.Players.LocalPlayer.Character then
												if itm.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop") and itm.RightDetector:FindFirstChild("RightWeld") and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
													itm.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(itm.RightDetector["RightWeld"],game.Players.LocalPlayer.Character.HumanoidRootPart)
													game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(game.Players.LocalPlayer)
												end
											end
										end
									end
								end
							end
						end
					end
					if prt.Name == "HumanoidRootPart" then
						if game.Players.LocalPlayer.Character.HumanoidRootPart:FindFirstChild("RootAttachment") then
							task.wait(0.2)
							if game.Players.LocalPlayer.Character then
								if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
									game.Players.LocalPlayer.Character.HumanoidRootPart.RootAttachment.Parent = game.Players.LocalPlayer.Character.TruePositionPart
									Rayfield:Notify({
										Title = "Antiblob has been enabled",
										Content = "Antiblob",
										Duration = 6.5,
										Image = 4483362458,
									})
								end
							end
						end
					end
				end
			end
			task.wait()
		end
		if not Value and game.Players.LocalPlayer.Character then
			if game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and game.Players.LocalPlayer.Character:FindFirstChild("TruePositionPart") then
				if game.Players.LocalPlayer.Character.TruePositionPart:FindFirstChild("RootAttachment") then
					game.Players.LocalPlayer.Character.TruePositionPart.RootAttachment.Parent = game.Players.LocalPlayer.Character.HumanoidRootPart
					Rayfield:Notify({
						Title = "Antiblob has been disabled",
						Content = "Antiblob",
						Duration = 6.5,
						Image = 4483362458,
					})
				end
			end
		end
    end,
})

AntiBlobKick = AntiTab:CreateToggle({
    Name = "Anti Blob (visual like if they grab u ur not in their grab)",
    CurrentValue = false,
    Flag = "AntiBlobKick",
    Callback = function(Value)
        antiBlobKickEnabled = Value

        if Value then
            for _, obj in pairs(Workspace:GetDescendants()) do
                breakBlobDetectors(obj)
            end
            task.spawn(function()
                while antiBlobKickEnabled do
                    for _, obj in pairs(Workspace:GetDescendants()) do
                        breakBlobDetectors(obj)
                    end
                    task.wait(0.4)
                end
            end)
        end
    end
})

AntiTab:CreateSection("Anti Blob")


ps6 = nil

paintPartsBackup = {}
 connections = {}

function deleteAllPaintParts()
	for _, obj in ipairs(Workspace:GetDescendants()) do
		if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
			local clone = obj:Clone()
			clone.Archivable = true
			paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
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



AntiLag = AntiTab:CreateToggle({
    Name = "Anti-Lag",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        if Value then 
            if game:GetService("Players").LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove") then
				game:GetService("Players").LocalPlayer.PlayerScripts.CharacterAndBeamMove:Destroy()
			end
			for _,plr in pairs(game:GetService("Players"):GetChildren()) do
				if plr.Character:FindFirstChild("GrabParts") then
					plr.Character.GrabParts:Destroy()
				end
			end
            Rayfield:Notify({
                Title = "Anti-Lag has been enabled",
                Content = "Anti-Lag",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            if not game:GetService("Players").LocalPlayer.PlayerScripts:FindFirstChild("CharacterAndBeamMove") then
				local scriptt = game:GetService("StarterPlayer").StarterPlayerScripts.CharacterAndBeamMove:Clone()
				scriptt.Parent = game:GetService("Players").LocalPlayer.PlayerScripts
			end
            Rayfield:Notify({
                Title = "Anti-Lag has been disabled",
                Content = "Anti-Lag",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

AntiTab:CreateToggle({
    Name = "Anti Burn",
    Default = false,
    Callback = function(Value)
        if Value then
            local player = LocalPlayer
            local part = workspace.Map.FactoryIsland.PoisonContainer.ExtinguishPart
            local startPos = part.Position

            part.Transparency = 1

            local function setupCharacter(char)
                local humanoid = char:WaitForChild("Humanoid")
                local rootPart = char:WaitForChild("HumanoidRootPart")
                local fireDebounce = humanoid:WaitForChild("FireDebounce")

                local connection
                connection = RunService.Heartbeat:Connect(function()
                    if not char:IsDescendantOf(workspace) then
                        connection:Disconnect()
                        return
                    end

                    if fireDebounce and fireDebounce.Value then
                        local target = rootPart.Position
                        local cycle = (tick() * 3) % 3
                        local targetPos
                        if cycle < 1 then
                            targetPos = target
                        elseif cycle < 2 then
                            targetPos = startPos
                        else
                            targetPos = target
                        end

                        local offset = Vector3.new(
                            math.sin(tick() * 5) * 3,
                            math.cos(tick() * 3) * 2,
                            math.sin(tick() * 4) * 3
                        )

                        part.Position = targetPos + offset
                        part.CFrame = part.CFrame * CFrame.Angles(
                            math.rad(math.random(-10, 10)),
                            math.rad(math.random(-10, 10)),
                            math.rad(math.random(-10, 10))
                        )
                    else
                        part.Position = part.Position:Lerp(startPos, 0.1)
                    end
                end)
            end

            player.CharacterAdded:Connect(setupCharacter)
            if player.Character then
                setupCharacter(player.Character)
            end
        end
    end
})

 AntiVoid = AntiTab:CreateToggle({
    Name = "Disable Void",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
		antiboid = Value
        if Value then 
            workspace.FallenPartsDestroyHeight = 0/0
            Rayfield:Notify({
                Title = "Void has been disabled",
                Content = "Anti-Void",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            workspace.FallenPartsDestroyHeight = -100
            Rayfield:Notify({
                Title = "Void has been enabled",
                Content = "Anti-Void",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

AntiTab:CreateToggle({
    Name = "Anti Paint",
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


antiStickyEnabled = false
stickyConnection = nil

 function disableStickyScripts()
    local playerScripts = LocalPlayer:FindFirstChild("PlayerScripts")
    if not playerScripts then return end

    for _, obj in ipairs(playerScripts:GetDescendants()) do
        if (obj:IsA("Script") or obj:IsA("LocalScript")) then
            local name = obj.Name:lower()
            if name:find("sticky") or name:find("stick") then
                if not obj.Disabled then
                    obj.Disabled = true
                end
            end
        end
    end
end

 function startAntiSticky()
    if stickyConnection then return end
    disableStickyScripts()
    stickyConnection = RunService.Heartbeat:Connect(function()
        if antiStickyEnabled then
            disableStickyScripts()
        end
    end)
end

 function stopAntiSticky()
    if stickyConnection then
        stickyConnection:Disconnect()
        stickyConnection = nil
    end
end

AntiTab:CreateToggle({
    Name = "Anti Sticky",
    CurrentValue = false,
    Flag = "AntiStickyToggle",
    Callback = function(Value)
        antiStickyEnabled = Value
        if Value then
            startAntiSticky()
        else
            stopAntiSticky()
        end
    end
})

ps5 = nil


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local VirtualUser = game:GetService("VirtualUser")
local RunService = game:GetService("RunService")

local antiAFKEnabled = false
local antiAFKConnection = nil

function breakIdleConnections()
    if getconnections then
        local connections = getconnections(LocalPlayer.Idled)
        for _, connection in pairs(connections) do
            if connection.Function and connection.Thread then
                -- Utilise le thread pour casser
                task.cancel(connection.Thread)
            elseif connection.Disable then
                connection:Disable()
            elseif connection.Disconnect then
                connection:Disconnect()
            end
        end
    end
end

function simulateAntiIdle()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end

antiAFKToggle = AntiTab:CreateToggle({
    Name = "Anti AFK",
    CurrentValue = false,
    Flag = "AntiAFK",
    Callback = function(Value)
        antiAFKEnabled = Value

        if Value then
            breakIdleConnections()
            if not getconnections then
                antiAFKConnection = LocalPlayer.Idled:Connect(function()
                    simulateAntiIdle()
                end)
            end
            antiAFKLoop = task.spawn(function()
                while antiAFKEnabled do
                    simulateAntiIdle()
                    task.wait(25)
                end
            end)
            Rayfield:Notify({
                Title = "Anti AFK enabled",
                Content = "Anti idle is now active",
                Duration = 5,
                Image = 4483362458,
            })
        else
            if antiAFKConnection then
                antiAFKConnection:Disconnect()
                antiAFKConnection = nil
            end
            if antiAFKLoop then
                task.cancel(antiAFKLoop)
                antiAFKLoop = nil
            end

            Rayfield:Notify({
                Title = "Anti AFK disabled",
                Content = "Anti idle is now inactive",
                Duration = 5,
                Image = 4483362458,
            })
        end
    end
})
ps8 = nil

local GrabTab = Window:CreateTab("Grabs mode")

_G.RainbowLine = nil

 RunService = game:GetService("RunService")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 Event = ReplicatedStorage:WaitForChild("DataEvents"):WaitForChild("UpdateLineColorsEvent")

 Colors = {
	Color3.fromRGB(255, 0, 0),
	Color3.fromRGB(255, 85, 0),
	Color3.fromRGB(255, 255, 0),
	Color3.fromRGB(0, 255, 0),
	Color3.fromRGB(0, 170, 255),
	Color3.fromRGB(0, 0, 255),
	Color3.fromRGB(128, 0, 255)
}

 function StartRainbow()
	_G.RainbowLine = true
	task.spawn(function()
		local index = 1
		while _G.RainbowLine do
			local color = ColorSequence.new(Colors[index])
			Event:FireServer(color)
			index = index % #Colors + 1
			task.wait()
		end
	end)
end

 function StopRainbow()
	_G.RainbowLine = nil
end

GrabTab:CreateToggle({
	Name = "one color line changing",
	Default = false,
	Callback = function(Value)
		if Value then
			StartRainbow()
		else
			StopRainbow()
		end
	end    
})


 S = { RS = game:GetService("ReplicatedStorage"), HS = { JSONEncode = function(data) return game:GetServic("HttpService"):JSONEncode(data) end } }
Lt = { ccolors = {Color3.new(1, 0, 0), Color3.new(0, 1, 0), Color3.new(0, 0, 1), Color3.new(1, 1, 0), Color3.new(1, 0, 1),
 Color3.new(0, 1, 1), Color3.new(0.5, 0.5, 0.5), Color3.new(0.2, 0.8, 0.2), Color3.new(0.9, 0.1, 0.3),
Color3.new(0.4, 0.6, 0.8), Color3.new(0.7, 0.2, 0.9), Color3.new(0.3, 0.7, 0.4), Color3.new(0.8, 0.5, 0.1),
Color3.new(0.6, 0.3, 0.7), Color3.new(0.1, 0.9, 0.5), Color3.new(0.5, 0.2, 0.8), Color3.new(0.9, 0.4, 0.6),
Color3.new(0.3, 0.6, 0.1), Color3.new(0.7, 0.8, 0.2), Color3.new(0.4, 0.1, 0.9)} }
PackColor = function(c) return c end

O = { Events = { SetLineColorEvent = S.RS:WaitForChild("DataEvents"):WaitForChild("UpdateLineColorsEvent") }, 
           me = { BeamColor = { ColorSequenceHolder = { Color = ColorSequence.new(Color3.new(1, 1, 1)) } } } }
N = { linecolorscount = 0 }



SetLineColorEvent = O.Events.SetLineColorEvent
linecolorscount = 0

function set_line_color()
    local Data = {}
    for i, v in ipairs(Lt.ccolors) do
        Data[i] = PackColor(v)
    end
args = {
        ColorSequenceKeypoint.new(0, Lt.ccolors[1]),
        ColorSequenceKeypoint.new(0.10, Lt.ccolors[2]),
        ColorSequenceKeypoint.new(0.15, Lt.ccolors[3]),
        ColorSequenceKeypoint.new(0.20, Lt.ccolors[4]),
        ColorSequenceKeypoint.new(0.25, Lt.ccolors[5]),
        ColorSequenceKeypoint.new(0.30, Lt.ccolors[6]),
        ColorSequenceKeypoint.new(0.35, Lt.ccolors[7]),
        ColorSequenceKeypoint.new(0.40, Lt.ccolors[8]),
        ColorSequenceKeypoint.new(0.45, Lt.ccolors[9]),
        ColorSequenceKeypoint.new(0.50, Lt.ccolors[10]),
        ColorSequenceKeypoint.new(0.55, Lt.ccolors[11]),
        ColorSequenceKeypoint.new(0.60, Lt.ccolors[12]),
        ColorSequenceKeypoint.new(0.65, Lt.ccolors[13]),
        ColorSequenceKeypoint.new(0.70, Lt.ccolors[14]),
        ColorSequenceKeypoint.new(0.75, Lt.ccolors[15]),
        ColorSequenceKeypoint.new(0.80, Lt.ccolors[16]),
        ColorSequenceKeypoint.new(0.85, Lt.ccolors[17]),
        ColorSequenceKeypoint.new(0.90, Lt.ccolors[18]),
        ColorSequenceKeypoint.new(0.95, Lt.ccolors[19]),
        ColorSequenceKeypoint.new(1, Lt.ccolors[20])
    }
    O.Events.SetLineColorEvent:FireServer(ColorSequence.new(args))
end

GrabTab:CreateToggle({
    Name = "rainbow line changing",
    CurrentValue = false,
    Flag = "LoopRandomLineColor", 
    Callback = function(Value)
        local rgb = Value
        local save = O.me.BeamColor.ColorSequenceHolder.Color
        while rgb do
            if N.linecolorscount == 1 then
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new(Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))))
                task.wait()
            elseif N.linecolorscount == 2 then
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new({
                    ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255)))
                }))
                task.wait()
            elseif N.linecolorscount == 20 then
                local args = ColorSequence.new({
                    ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.05, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.10, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.15, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.20, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.25, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.30, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.35, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.40, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.45, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.50, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.55, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.60, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.65, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.70, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.75, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.80, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.85, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(0.90, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))),
                    ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255)))
                })
                O.Events.SetLineColorEvent:FireServer(args)
                task.wait()
            else
                local colors = {ColorSequenceKeypoint.new(0, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255)))}
                for colorscount = 0, N.linecolorscount do
                    if colorscount ~= 1 then
                        if colorscount ~= N.linecolorscount then
                            table.insert(colors, ColorSequenceKeypoint.new(1/N.linecolorscount * colorscount, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))))
                        else
                            table.insert(colors, ColorSequenceKeypoint.new(1, Color3.fromRGB(math.random(0, 255), math.random(0, 255), math.random(0, 255))))
                        end
                    end
                end
                O.Events.SetLineColorEvent:FireServer(ColorSequence.new(colors))
                task.wait()
            end
        end
        O.Events.SetLineColorEvent:FireServer(save)
    end,
})


GrabTab:CreateSection("Aim")

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

local lockConnections = {}
local holdingQ = false
local targetPart = nil

function findTargetPart()
    local playerFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if playerFolder then
        for _, ball in ipairs(playerFolder:GetChildren()) do
            if ball.Name == "BallBasketball" then
                local ball80 = ball:FindFirstChild("Ball80")
                if ball80 and ball80:FindFirstChild("WeldConstraint") then
                    return ball80
                end
            end
        end
    end
    return nil
end


GrabTab:CreateToggle({
    Name = "Basket LA test !",
    Default = false,
    Callback = function(Value)
        if Value then
            holdingQ = false
            table.insert(lockConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then
                    holdingQ = true
                    targetPart = findTargetPart()
                end
            end))
            table.insert(lockConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then
                    holdingQ = false
                    targetPart = nil
                end
            end))
            table.insert(lockConnections, RunService.RenderStepped:Connect(function()
                if holdingQ and targetPart and targetPart.Parent then
                    local targetPosition = targetPart.Position
                    if targetPosition then
                        Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPosition)
                    end
                end
            end))

            print("BallBasketball activé (touche Q)")
        else
            for _, connection in ipairs(lockConnections) do
                if connection then connection:Disconnect() end
            end
            lockConnections = {}
            holdingQ = false
            targetPart = nil
            print("BallBasketball désactivé")
        end
    end
})
ps8 = nil

aimbotConnections = {}

GrabTab:CreateToggle({
    Name = "La anti lag normal",
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

aimbotConnections = {}

GrabTab:CreateToggle({
	Name = "La decoy",
	Default = false,
	Callback = function(Value)
		if Value then
			local Players = game:GetService("Players")
			local UserInputService = game:GetService("UserInputService")
			local RunService = game:GetService("RunService")
			local LocalPlayer = Players.LocalPlayer
			local Camera = workspace.CurrentCamera
			local holdingQ = false


			local function getYouDecoy()
				local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
				if toysFolder then
					local decoy = toysFolder:FindFirstChild("YouDecoy")
					if decoy and decoy:FindFirstChild("HumanoidRootPart") and decoy:FindFirstChildOfClass("Humanoid") then
						return decoy
					end
				end
				return nil
			end

			table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
				if input.KeyCode == Enum.KeyCode.Q then
					holdingQ = true
				end
			end))

			table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
				if input.KeyCode == Enum.KeyCode.Q then
					holdingQ = false
				end
			end))

			table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
				if holdingQ then
					local decoy = getYouDecoy()
					if decoy then
						local hrp = decoy:FindFirstChild("HumanoidRootPart")
						if hrp then
							Camera.CFrame = CFrame.new(Camera.CFrame.Position, hrp.Position)
						end
					end
				end
			end))
		else
			for _, conn in pairs(aimbotConnections) do
				conn:Disconnect()
			end
			aimbotConnections = {}
		end
	end
})

aimbotConnections = {}
 lastDecoy = nil


 function getNearestDecoy()
    local localPlayer = game.Players.LocalPlayer
    local toysFolder = workspace:FindFirstChild(localPlayer.Name.."SpawnedInToys")
    if not toysFolder then return nil end

    local closest, closestDist = nil, math.huge
    local myPos = workspace.CurrentCamera.CFrame.Position

    for _, obj in ipairs(toysFolder:GetChildren()) do
        if obj.Name == "YouLittle" and obj:FindFirstChild("HumanoidRootPart") then
            local dist = (myPos - obj.HumanoidRootPart.Position).Magnitude
            if dist < closestDist then
                closest = obj
                closestDist = dist
            end
        end
    end

    return closest
end

GrabTab:CreateToggle({
    Name = "LA baby",
    Default = false,
    Callback = function(Value)
        if Value then
            local UserInputService = game:GetService("UserInputService")
            local RunService = game:GetService("RunService")
            local camera = workspace.CurrentCamera
            local holdingQ = false

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = true end
            end))

            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then holdingQ = false end
            end))

            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local decoy = getNearestDecoy()
                    if decoy and decoy:FindFirstChild("HumanoidRootPart") then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, decoy.HumanoidRootPart.Position)
                    end
                end
            end))

        else
            for _, conn in pairs(aimbotConnections) do
                conn:Disconnect()
            end
            aimbotConnections = {}
        end
    end
})

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera

local aimbotConnections = {}
local targetedPlayers = {}
local holdingQ = false

function getPlayerOptions()
    local options = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= localPlayer then
            table.insert(options, player.DisplayName .. " [" .. player.Name .. "]")
        end
    end
    return options
end

function extractRealName(displayString)
    return displayString:match("%[(.-)%]")
end

function getTargetedPlayer()
    local closest, closestDist = nil, math.huge
    local myPos = camera.CFrame.Position
    for _, targetName in ipairs(targetedPlayers) do
        local player = Players:FindFirstChild(targetName)
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (myPos - player.Character.HumanoidRootPart.Position).Magnitude
            if distance < closestDist then
                closest, closestDist = player, distance
            end
        end
    end
    return closest
end



 PlayerDropdown = GrabTab:CreateDropdown({
    Name = "Target Players",
    Options = getPlayerOptions(),
    CurrentOption = {},
    MultipleOptions = true,
    Flag = "TargetPlayersDropdown",
    Callback = function(Option)
        targetedPlayers = {}
        for _, displayName in ipairs(Option) do
            local realName = extractRealName(displayName)
            if realName then
                table.insert(targetedPlayers, realName)
            end
        end
        print("Targeted players:", table.concat(targetedPlayers, ", "))
    end
})

GrabTab:CreateButton({
    Name = "Refresh Player List",
    Callback = function()
        local newOptions = getPlayerOptions()
        PlayerDropdown:Refresh(newOptions)
        print("Player list refreshed!")
    end
})

GrabTab:CreateToggle({
    Name = "La antilag using list",
    Default = false,
    Callback = function(Value)
        if Value then
            if #targetedPlayers == 0 then
                print("No players targeted! Select players first.")
                return
            end

            table.insert(aimbotConnections, UserInputService.InputBegan:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then 
                    holdingQ = true 
                end
            end))

            table.insert(aimbotConnections, UserInputService.InputEnded:Connect(function(input)
                if input.KeyCode == Enum.KeyCode.Q then 
                    holdingQ = false 
                end
            end))

            table.insert(aimbotConnections, RunService.RenderStepped:Connect(function()
                if holdingQ then
                    local target = getTargetedPlayer()
                    if target and target.Character then
                        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                    end
                end
            end))

            print("Anti lag whitelist activated for", #targetedPlayers, "player(s)")
        else
            for _, conn in ipairs(aimbotConnections) do 
                if conn then conn:Disconnect() end
            end
            aimbotConnections = {}
            holdingQ = false
            print("Anti lag disabled")
        end
    end
})

GrabTab:CreateButton({
    Name = "Show Current Targets",
    Callback = function()
        if #targetedPlayers == 0 then
            print("No players currently targeted")
        else
            print("Current targets:", table.concat(targetedPlayers, ", "))
        end
    end
})

Players.PlayerAdded:Connect(function()
    task.wait(1)
    local newOptions = getPlayerOptions()
    PlayerDropdown:Refresh(newOptions)
end)

Players.PlayerRemoving:Connect(function()
    task.wait(1)
    local newOptions = getPlayerOptions()
    PlayerDropdown:Refresh(newOptions)
end)
ps7 = nil

Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
Camera = workspace.CurrentCamera
Hitbox = {"Head", "Torso", "Left Leg", "Right Leg"}

Toggle = false
Distance = 30

function SilentAimFunction(arg1, arg2, RFC)
    if Toggle then
        closest, distance = nil, math.huge
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character.HumanoidRootPart and player.Character.Humanoid and player.Character.Humanoid.Health > 0 then
                root = player.Character.HumanoidRootPart
                screenPos, onScreen = Camera:WorldToScreenPoint(root.Position)
                if onScreen then
                    dist = (LocalPlayer.Character.HumanoidRootPart.Position - root.Position).magnitude
                    if dist < distance then
                        closest = player
                        distance = dist
                    end
                end
            end
        end
        if closest and distance <= Distance and closest.Character then
            part = Hitbox[math.random(1, #Hitbox)]
            hitPart = closest.Character:FindFirstChild(part)
            if hitPart then
                origin = Camera.CFrame.Position
                direction = (hitPart.Position - origin).Unit * arg2
                result = {Instance = hitPart, Position = hitPart.Position, Normal = Vector3.new(0, 1, 0), Material = Enum.Material.Plastic}
                return result, hitPart.CFrame:ToObjectSpace(CFrame.new(result.Position))
            end
        end
    end
    return RFC(arg1, arg2)
end

PlayerScripts = LocalPlayer:WaitForChild("PlayerScripts", 5)
raycastModule = PlayerScripts:FindFirstChild("RaycastModule")
if raycastModule then
    module = require(raycastModule)
    if module.RaycastFromCam then
        RFC = module.RaycastFromCam
        module.RaycastFromCam = function(arg1, arg2)
            return SilentAimFunction(arg1, arg2, RFC)
        end
    end
end

GrabTab:CreateToggle({
	Name = "Silent Aim (hookless usin raycast)",
	Default = false,
	Callback = function(Value)
		Toggle = Value
	end
})

GrabTab:CreateSlider({
   Name = "Silent Aim distance",
   Range = {30, 50},
   Increment = 1,
   Suffix = "studs",
   CurrentValue = 10,
   Callback = function(Value)
		Distance = Value
	end
})

GrabTab:CreateDivider()

 ReplicatedStorage = game:GetService("ReplicatedStorage")
 CreateGrabLine = ReplicatedStorage.GrabEvents.CreateGrabLine

_G.InvisibleLine = nil

GrabTab:CreateToggle({
	Name = "Invisible Line",
	Default = false,
	Callback = function(Value)
		if Value then
			_G.InvisibleLine = true
			task.spawn(function()
				while _G.InvisibleLine do
					CreateGrabLine:FireServer()
					task.wait()
				end
			end)
		else
			_G.InvisibleLine = nil
		end
	end    
})

grabReachEnabled = false
 currentRange = 35
 originalRange = 35

function applyGrabReach(range)
    currentRange = range
    ReplicatedStorage.DataEvents.UpdateLineColorsEvent:FireServer(ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 0, 0)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 255, 195))
    }))
    pcall(function()
        for _, connection in pairs(getconnections(ReplicatedStorage.GamepassEvents.FurtherReachBoughtNotifier.OnClientEvent)) do
            for i in debug.getupvalues(connection.Function) do
                debug.setupvalue(connection.Function, i, range)
            end
        end
    end)
end
function resetGrabReach()
    applyGrabReach(originalRange)
end
LocalPlayer.CharacterAdded:Connect(function()
    if grabReachEnabled then
        task.wait(0.5)
        applyGrabReach(currentRange)
    end
end)
GrabTab:CreateToggle({
    Name = "Grab Further Reach(need executor with setupvalue and debugsetupvalue)",
    CurrentValue = false,
    Flag = "GrabReachToggle",
    Callback = function(enabled)
        grabReachEnabled = enabled
        if enabled then
            applyGrabReach(currentRange)
            Rayfield:Notify({ Title = "Grab Reach", Content = "ON - " .. currentRange .. " studs", Duration= 2 })
        else
            resetGrabReach()
            Rayfield:Notify({ Title = "Grab Reach", Content = "OFF", Duration= 2 })
        end
    end
})

GrabTab:CreateSlider({
    Name = "Grab Reach Distance",
    Range = {25, 45},
    Increment = 1,
    CurrentValue = 25,
    Flag = "GrabReachSlider",
    Callback = function(value)
        currentRange = value
        if grabReachEnabled then
            applyGrabReach(value)
            Rayfield:Notify({ Title = "Grab Reach", Content = value .. " studs", Duration= 1.5 })
        end
    end
})

GrabTab:CreateDivider()

GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
CreateGrabLine = GrabEvents:WaitForChild("CreateGrabLine")
DestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
toysFolder = workspace:FindFirstChild(localPlayer.Name.."SpawnedInToys")
Players = game:GetService("Players")

noclipCoroutine = nil
hellGrabCoroutine = nil
burnGrabCoroutine = nil
killGrabCoroutine = nil
heavenGrabCoroutine = nil
sitGrabCoroutine = nil
CrazyGrabCoroutine = nil
noclipGrabCoroutine = nil
fireToggle = false
crazyGrabCoroutine = nil
crazyGrabDelay = 0.1
latinaenabled = false
BMA = false
XTD = false
dick = Instance.new("Folder")
dick.Parent = game.workspace
dick.Name = "PlotItems"
canC = false
GPI = false
tpland = {

Prison = CFrame.new(195, -7, -561),
}
prisonactive = false
 locc = tpland["Prison"]
loccc = "Prison"

local plrinplot = Instance.new("Folder", game:getService("Workspace"))
plrinplot.Name = "Players In Plots"

for _,prt in pairs(game.workspace:GetChildren()) do
	if prt.Name == "GrabParts" then
		prt.Name = "OfflineGrabParts"
	end
end

for _,prt in pairs(game:GetService("Workspace").Plots:GetDescendants()) do
    if prt.Name == "PlotArea" then
        prt.CanQuery = false
        prt.CanCollide = false
    elseif prt.Name == "PlotBarrier" or prt.Name == "FalseBorder" then
        prt.CanQuery = true
        prt.CanCollide = true
    end 
end

for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
    for _,model in pairs(plot:GetChildren()) do
    end
    plot.ChildAdded:Connect(function(model)
    end)
end


function Latina(itm)
    if not itm:FindFirstChild("LatinaPart") then
        itm.Center.Transparency = 1
        itm.SoundPart.BillboardGui.ImageLabel.Visible = false
        itm.SoundPart.Attachment.PointLight.Enabled = false
        itm.SoundPart.Attachment.ParticleEmitter.Enabled = false
        itm.SoundPart.Size = Vector3.new(10, 10, 10)
        itm.SoundPart.Shape = "Block"
        local a = Instance.new("ManualWeld")
        local b = Instance.new("Part")
        b.Parent = itm
        b.CanQuery = false
        b.CanCollide = false
        b.CanTouch = false
        b.Size = Vector3.new(10, 10, 10)
        b.Name = "LatinaPart"
        b.Anchored = false
        b.Massless = true

        a.Parent = b
        a.Name = "LatinaGlue"
        a.Part0 = b
        a.Part1 = itm.Center

        local aa = Instance.new("Decal")
        aa.Texture = "rbxassetid://18700831357"
        aa.Name = "DaveLatina"
        aa.Parent = b
            
        local ab = Instance.new("Decal")
        ab.Texture = "rbxassetid://18700831357"
        ab.Name = "DaveLatina"
        ab.Face = "Back"
        ab.Parent = b

        local ac = Instance.new("Decal")
        ac.Texture = "rbxassetid://18700831357"
        ac.Name = "DaveLatina"
        ac.Face = "Top"
        ac.Parent = b
            
        local ad = Instance.new("Decal")
        ad.Texture = "rbxassetid://18700831357"
        ad.Name = "DaveLatina"
        ad.Face = "Bottom"
        ad.Parent = b

        local ae = Instance.new("Decal")
        ae.Texture = "rbxassetid://18700831357"
        ae.Name = "DaveLatina"
        ae.Face = "Left"
        ae.Parent = b
            
        local af = Instance.new("Decal")
        af.Texture = "rbxassetid://18700831357"
        af.Name = "DaveLatina"
        af.Face = "Right"
        af.Parent = b
    end
end

function UnLatina(itm)
    if itm:FindFirstChild("LatinaPart") then
        itm.Center.Transparency = 0
        itm.SoundPart.BillboardGui.ImageLabel.Visible = true
        itm.SoundPart.Attachment.PointLight.Enabled = true
        itm.SoundPart.Attachment.ParticleEmitter.Enabled = true
        itm.SoundPart.Size = Vector3.new(3, 3, 3)
        itm.SoundPart.Shape = "Block"
        itm.LatinaPart:Destroy()
    end
end


function spawnItem(itemName, position, orientation)
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

function firePlayer(p)
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


for _,plot in pairs(game.workspace.PlotItems:GetChildren()) do
	if plot.Name ~= "PlayersInPlots" then
		plot.ChildAdded:Connect(function(itm)
			task.wait()
			if canC then
				for _,prt in pairs(itm:GetChildren()) do
					if prt.ClassName == "Part" then
						prt.CollisionGroup = "Items"
					end
				end
			else
				for _,prt in pairs(itm:GetChildren()) do
					if prt.ClassName == "Part" then
						prt.CollisionGroup = "PlotItems"
					end
				end
			end
		end)
	end
end

    function grabHandler(grabType)
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

function burn(part)
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

function burnGrab()
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

paintPlayerParts = {}
for _, part in ipairs(workspace.Map:GetDescendants()) do
    if part:IsA("Part") and part.Name == "PaintPlayerPart" then
        table.insert(paintPlayerParts, part)
    end
end

function arson(part)
    if not toysFolder:FindFirstChild("Campfire") then
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("Campfire", CFrame.new(-72.9304581, -5.96906614, -265.543732), Vector3.new(0, 90, 0))
    end
    local campfire = toysFolder:FindFirstChild("Campfire")
    if campfire then
        local burnPart = campfire:FindFirstChild("FirePlayerPart")
        if burnPart then
            burnPart.Size = Vector3.new(7, 7, 7)
            burnPart.Position = part.Position
            task.wait(0.3)
            burnPart.Position = Vector3.new(0, -50, 0)
        end
    end
end

function killGrab()
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

    function noclipGrab()
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



function UnBoner(itm)
	if itm:FindFirstChild("BonerPart") then
    	itm.Center.Transparency = 0
     	itm.SoundPart.BillboardGui.ImageLabel.Visible = true
     	itm.SoundPart.Attachment.PointLight.Enabled = true
     	itm.SoundPart.Attachment.ParticleEmitter.Enabled = true
     	itm.SoundPart.Size = Vector3.new(3,3,3)
     	itm.SoundPart.Shape = "Block"
		itm.BonerPart:Destroy()
	end
end


function noclipGrab()
    local noclipCoroutine = coroutine.create(function()
        while true do
            local success, err = pcall(function()
                local child = workspace:FindFirstChild("GrabParts")
                if child and child.Name == "GrabParts" then
                    local grabPart = child:FindFirstChild("GrabPart")
                    local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                    local character = grabbedPart.Parent
                    if character and character:FindFirstChild("HumanoidRootPart") then
                        while workspace:FindFirstChild("GrabParts") do
                            for _, part in pairs(character:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                            task.wait()
                        end
                        for _, part in pairs(character:GetDescendants()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = true
                            end
                        end
                    end
                end
            end)
            task.wait(0.1)
        end
    end)
    coroutine.resume(noclipCoroutine)
end

--The function noclip grab is for the Aladin grab btw*

     GrabTab:CreateToggle({
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

    GrabTab:CreateToggle({
    Name = "Burn Grab",
    Default = false,
    Callback = function(state)
        fireToggle = state
        if state then
            task.spawn(function()
                while fireToggle do
                    pcall(function()
                        local grab = workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")
                        if grab and grab:FindFirstChild("WeldConstraint") then
                            local grabbed = grab.WeldConstraint.Part1
                            local head = grabbed and grabbed.Parent:FindFirstChild("Head")
                            if head then
                                arson(head)
                            end
                        end
                    end)
                    task.wait()
                end
            end)
        end
    end
})

GrabTab:CreateToggle({
    Name = "Weird Line Grab",
    Default = false,
    Callback = (function()
        local State = {
            Active = false,
            GrabPartsConnection = nil,
            DragPartConnection = nil,
        }

        local function configureDragPart(dragPart)
            if not dragPart then return end
            local alignPosition = dragPart:FindFirstChild("AlignPosition")
            local alignOrientation = dragPart:FindFirstChild("AlignOrientation")

            if alignPosition then
                alignPosition.Responsiveness = 200
                alignPosition.MaxForce = math.huge
            end

            if alignOrientation then
                alignOrientation.Responsiveness = 200
                alignOrientation.MaxTorque = math.huge
            end
        end

        local function disconnectConnections()
            if State.DragPartConnection then
                State.DragPartConnection:Disconnect()
                State.DragPartConnection = nil
            end
            if State.GrabPartsConnection then
                State.GrabPartsConnection:Disconnect()
                State.GrabPartsConnection = nil
            end
        end

        local function monitorDragPart(grabParts)
            if State.DragPartConnection then
                State.DragPartConnection:Disconnect()
                State.DragPartConnection = nil
            end

            local currentDragPart = grabParts:FindFirstChild("DragPart")
            if currentDragPart then
                configureDragPart(currentDragPart)
            end

            State.DragPartConnection = grabParts.ChildAdded:Connect(function(child)
                if child.Name == "DragPart" then
                    configureDragPart(child)
                end
            end)
        end

        local function monitorGrabParts()
            disconnectConnections()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if grabParts then
                monitorDragPart(grabParts)
            end

            State.GrabPartsConnection = workspace.ChildAdded:Connect(function(child)
                if child.Name == "GrabParts" then
                    monitorDragPart(child)
                end
            end)

            workspace.ChildRemoved:Connect(function(child)
                if child.Name == "GrabParts" then
                    if State.DragPartConnection then
                        State.DragPartConnection:Disconnect()
                        State.DragPartConnection = nil
                    end
                end
            end)
        end

        return function(Value)
            State.Active = Value
            if State.Active then
                monitorGrabParts()
            else
                disconnectConnections()
            end
        end
    end)()
})

    GrabTab:CreateToggle({
    Name = "Sus Grab",
    Default = false,
    Callback = function(state)
        noclipToggle = state
        if state then
            task.spawn(function()
                while noclipToggle do
                    pcall(function()
                        local grab = workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")
                        if grab and grab:FindFirstChild("WeldConstraint") then
                            local grabbed = grab.WeldConstraint.Part1
                            local character = grabbed and grabbed.Parent
                            if character and character:FindFirstChild("HumanoidRootPart") then
                                for _, part in pairs(character:GetChildren()) do
                                    if part:IsA("BasePart") then
                                        part.CanCollide = false
                                    end
                                end
                                task.wait()
                                for _, part in pairs(character:GetChildren()) do
                                    if part:IsA("BasePart") then
                                        part.CanCollide = true
                                    end
                                end
                            end
                        end
                    end)
                    task.wait()
                end
            end)
        end
    end
})

   GrabTab:CreateToggle({
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

    GrabTab:CreateToggle({
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

     GrabTab:CreateToggle({
    Name = "sit grab",
    Default = false,
    Color = Color3.fromRGB(102, 0, 102),
    Callback = function(Value)
        if Value then
            sitGrabCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        local grabParts = workspace:FindFirstChild("GrabParts")
                        if grabParts and grabParts.Name == "GrabParts" then
                            local grabPart = grabParts:FindFirstChild("GrabPart")
                            if grabPart then
                                local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
                                if weldConstraint then
                                    local grabbedPart = weldConstraint.Part1
                                    if grabbedPart then
                                        local trgtCHR = grabbedPart.Parent
                                        if trgtCHR and trgtCHR:IsA("Model") and trgtCHR:FindFirstChild("Humanoid") then
                                            for _, part in ipairs(trgtCHR:GetDescendants()) do
                                               if part:IsA("BasePart") then
                                                    part.CanCollide = true
                                                end
                                            end
                                            local target = trgtCHR:FindFirstChild("Torso")
                                            if target then
                                                local velocity = target:FindFirstChild("sitG") or Instance.new("BodyVelocity")
                                                velocity.Name = "sitG"
                                                velocity.Parent = target
                                                velocity.Velocity = Vector3.new(0, -9999999, 0)
                                                velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                game:GetService("Debris"):AddItem(velocity, 0.1)
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.1)
                end
            end)
            coroutine.resume(sitGrabCoroutine)
        else
            if sitGrabCoroutine then
                coroutine.close(sitGrabCoroutine)
                sitGrabCoroutine = nil
            end
            for _, player in pairs(game.Players:GetPlayers()) do
                local char = player.Character
                if char then
                    for _, part in ipairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then
part.CanCollide = false
end
end
                    local torso = char:FindFirstChild("Torso")
                    if torso then
                        local velocity = torso:FindFirstChild("hellG")
                        if velocity and velocity:IsA("BodyVelocity") then
                            velocity:Destroy()
                        end
                    end
                end
            end
        end
    end
})


GrabTab:CreateToggle({
    Name = "Aladin grab(grab sm1 ungrab and turn off toggle reset to turn off the fly)",
    Default = false,
    Color = Color3.fromRGB(102, 0, 102),
    Callback = function(Value)
        if Value then
            hellGrabCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        local child = workspace:FindFirstChild("GrabParts")
                        if child and child.Name == "GrabParts" then
                            local grabPart = child:FindFirstChild("GrabPart")
                            local grabbedPart = grabPart:FindFirstChild("WeldConstraint").Part1
                            local trgtCHR = grabbedPart.Parent
                            local target = trgtCHR.Torso
                            if trgtCHR then
                                local velocity = target:FindFirstChild("hellG") or Instance.new("BodyVelocity")
                                velocity.Name = "hellG"
                                velocity.Parent = target
                                velocity.Velocity = Vector3.new(0, -9999999, 0) 
                                velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                game:GetService("Debris"):AddItem(velocity, 100)
                            end
                        end
                    end)
                    task.wait()
                end
            end)
            coroutine.resume(hellGrabCoroutine)
            noclipGrab()
        else
            if hellGrabCoroutine then
                coroutine.close(hellGrabCoroutine)
                hellGrabCoroutine = nil
            end
            for _, v in pairs(Players:GetPlayers()) do
                local a = v.Character and v.Character:FindFirstChild("Torso")
                if a then
                    for _, i in pairs(a:GetChildren()) do
                        if i.Name == "hellG" and i:IsA("BodyVelocity") then
                            i:Destroy()
                        end
                    end
                end
            end
            for _, player in pairs(Players:GetPlayers()) do
                local char = player.Character
                if char then
                    for _, part in pairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = true
                        end
                    end
                end
            end
        end
    end
})


GrabTab:CreateToggle({
    Name = "Latina Light Bulb toys (client but cool)",
    CurrentValue = false,
    Flag = "Toggle1", 
    Callback = function(Value)
        latinaenabled = Value
        while latinaenabled do
            for _, plr in pairs(game.Players:GetChildren()) do
                if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
                    for _, itm in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
                        if itm.Name == "BallMagicLight" then
                            Latina(itm)
                        end
                    end
                end
            end
            for _, plot in pairs(game.workspace.PlotItems:GetChildren()) do
                for _, itm in pairs(plot:GetChildren()) do
                    if itm.Name == "BallMagicLight" then
                        Latina(itm)
                    end
                end
            end
            task.wait()
        end
        for _, plr in pairs(game.Players:GetChildren()) do
            if game.workspace:FindFirstChild(plr.Name.."SpawnedInToys") then
                for _, itm in pairs(game.workspace:FindFirstChild(plr.Name.."SpawnedInToys"):GetChildren()) do
                    if itm.Name == "BallMagicLight" then
                        UnLatina(itm)
                    end
                end
            end
        end
        for _, plot in pairs(game.workspace.PlotItems:GetChildren()) do
            for _, itm in pairs(plot:GetChildren()) do
                if itm.Name == "BallMagicLight" then
                    UnLatina(itm)
                end
            end
        end
    end,
})

 BMT = GrabTab:CreateToggle({
    Name = "Masless grab",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
		BMA = Value
		while BMA do
			if game.workspace:FindFirstChild("GrabParts") then
				if game.workspace.GrabParts:FindFirstChild("DragPart") then
					if game.workspace.GrabParts.DragPart:FindFirstChild("AlignOrientation") then
						game.workspace.GrabParts.DragPart.AlignOrientation.MaxTorque = math.huge
					end
					if game.workspace.GrabParts.DragPart:FindFirstChild("AlignPosition") then
						game.workspace.GrabParts.DragPart.AlignPosition.MaxForce = math.huge
					end
				end
			end
			task.wait()
		end
	end,
})

GrabTab:CreateToggle({
    Name = "Tp Grab",
    CurrentValue = false,
    Flag = "TeleportGrabToggle",
    Callback = function(enabled)
        if enabled then
            local targetPart = workspace.Map.AlwaysHereTweenedObjects.OuterUFO.Object.ObjectModel:GetChildren()[13]
            if not targetPart or not targetPart:IsA("BasePart") then return end

            GravityConnection = workspace.ChildAdded:Connect(function(model)
                if model.Name == "GrabParts" then
                    local grabPart = model:FindFirstChild("GrabPart")
                    local weld = grabPart and grabPart:FindFirstChildOfClass("WeldConstraint")
                    local partToTeleport = weld and weld.Part1
                    if partToTeleport and partToTeleport:IsA("BasePart") then
                        partToTeleport.CFrame = targetPart.CFrame + Vector3.new(0, 5, 0)
                    end
                end
            end)
        elseif GravityConnection then
            GravityConnection:Disconnect()
            GravityConnection = nil
        end
    end
})

GrabTab:CreateToggle({
    Name = "Crazy grab",
    Default = false,
    Color = Color3.fromRGB(255, 165, 0), 
    Callback = function(Value)
        if Value then
            crazyGrabCoroutine = coroutine.create(function()
                while true do
                    pcall(function()
                        local child = workspace:FindFirstChild("GrabParts")
                        if child and child.Name == "GrabParts" then
                            local grabPart = child:FindFirstChild("GrabPart")
                            if grabPart then
                                local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
                                if weldConstraint then
                                    local grabbedPart = weldConstraint.Part1
                                    if grabbedPart then
                                        local targetChar = grabbedPart.Parent
                                        if targetChar and targetChar:FindFirstChild("HumanoidRootPart") then
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(-17, 421, 50)
                                            task.wait(crazyGrabDelay)
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(145, 397, -126)
                                            task.wait(crazyGrabDelay)
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(157, 254, 89)
                                            task.wait(crazyGrabDelay)
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(-26, 207, -26)
                                            task.wait(crazyGrabDelay)
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(-23, 456, -19)
                                            task.wait(crazyGrabDelay)
                                            targetChar.HumanoidRootPart.CFrame = CFrame.new(15, 211, 45)
                                            task.wait(crazyGrabDelay)
                                        end
                                    end
                                end
                            end
                        end
                    end)
                    task.wait() 
                end
            end)
            coroutine.resume(crazyGrabCoroutine)
        else
            if crazyGrabCoroutine then
                coroutine.close(crazyGrabCoroutine)
                crazyGrabCoroutine = nil
            end
        end
    end
})

GrabTab:CreateSlider({
    Name = "Crazy Grab Delay",
    Range = {0.1, 2},
    Increment = 0.1,
    Suffix = "s",
    CurrentValue = 0.1,
    Flag = "CrazyGrabDelaySlider",
    Callback = function(Value)
        crazyGrabDelay = Value
    end
})

GrabTab:CreateDivider()

XTD = false

XTODELETE = GrabTab:CreateKeybind({
    Name = "X To Delete(only on humanoid blob decoy , plr...)",
    CurrentKeybind = "X",
    HoldToInteract = false,
    Flag = "Keybind1", 
    Callback = function(Keybind)
        if not XTD then return end 

        local success, err = pcall(function()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts or grabParts.Name ~= "GrabParts" then
                warn("GrabParts not found or invalid")
                return
            end

            local grabPart = grabParts:FindFirstChild("GrabPart")
            if not grabPart then
                warn("GrabPart not found")
                return
            end

            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if not weldConstraint then
                warn("WeldConstraint not found")
                return
            end

            local grabbedPart = weldConstraint.Part1
            if not grabbedPart then
                warn("Part1 not found in WeldConstraint")
                return
            end

            local parentModel = grabbedPart.Parent
            if not parentModel then
                warn("Parent model not found")
                return
            end
            if parentModel:FindFirstChild("HumanoidRootPart") then
                local humanoidRootPart = parentModel.HumanoidRootPart
                humanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                local replicatedStorage = game:GetService("ReplicatedStorage")
                if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                    replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(grabbedPart)
                else
                    warn("DestroyGrabLine event not found")
                end
                if grabParts then
                    grabParts:Destroy()
                end
                local partsToCheck = {"Head", "HumanoidRootPart", "Torso", "Right Arm", "Left Arm", "Right Leg", "Left Leg"}
                for _, partName in pairs(partsToCheck) do
                    local part = parentModel:FindFirstChild(partName)
                    if part and part:IsA("BasePart") then
                        part.AssemblyLinearVelocity = Vector3.new(0, 1000000, 0)
                    end
                end
                Rayfield:Notify({
                    Title = parentModel.Name .. " Deleted",
                    Content = "X To Delete",
                    Duration = 6.5,
                    Image = 4483362458,
                })
            elseif parentModel:FindFirstChild("SoundPart") then
                local soundPart = parentModel:FindFirstChild("SoundPart")
                if soundPart and soundPart:IsA("BasePart") then
                    soundPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                        replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(grabbedPart)
                    else
                        warn("DestroyGrabLine event not found")
                    end
                    task.wait()
                    soundPart.AssemblyLinearVelocity = Vector3.new(0, 1000000, 0)

                    Rayfield:Notify({
                        Title = parentModel.Name .. " Deleted",
                        Content = "X To Delete",
                        Duration = 6.5,
                        Image = 4483362458,
                    })
                end
            end
        end)

        if not success then
            warn("Error in X To Delete: " .. (err or "Unknown error"))
            Rayfield:Notify({
                Title = "Error",
                Content = "Failed to delete (check console)",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})
 XTDE = GrabTab:CreateToggle({
    Name = "X To Delete(if ur grab isn't appear click grab again)",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        XTD = Value
        if Value then
            Rayfield:Notify({
                Title = "X To Delete enabled",
                Content = "Press X to delete",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "X To Delete disabled",
                Content = "X To Delete",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})


 Prison = GrabTab:CreateKeybind({
    Name = "Send To Location",
    CurrentKeybind = "Q",
    HoldToInteract = false,
    Flag = "Keybind1",
    Callback = function(Keybind)
        if not prisonactive then return end

        local success, err = pcall(function()
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts or grabParts.Name ~= "GrabParts" then
                warn("GrabParts not found or invalid")
                return
            end
            local grabPart = grabParts:FindFirstChild("GrabPart")
            if not grabPart then
                warn("GrabPart not found")
                return
            end
            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if not weldConstraint then
                warn("WeldConstraint not found")
                return
            end
            local grabbedParent = weldConstraint.Part1.Parent
            if not grabbedParent then
                warn("Parent of Part1 not found")
                return
            end
            local playerFound = false
            for _, plr in pairs(game:GetService("Players"):GetChildren()) do
                if plr.Character == grabbedParent then
                    playerFound = true
                    local replicatedStorage = game:GetService("ReplicatedStorage")
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                        replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(weldConstraint.Part1)
                    else
                        warn("DestroyGrabLine event not found")
                    end
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner") then
                        replicatedStorage.GrabEvents.SetNetworkOwner:FireServer(grabbedParent, plr)
                        warn("SetNetworkOwner event not found")
                    end

                    task.wait()

                    local partsToCheck = {"Head", "HumanoidRootPart", "Torso", "Right Arm", "Left Arm", "Right Leg", "Left Leg"}
                    for _, partName in pairs(partsToCheck) do
                        local part = grabbedParent:FindFirstChild(partName)
                        if part and part:IsA("BasePart") then
                            part.CFrame = locc
                        end
                    end

                    Rayfield:Notify({
                        Title = "Prison Grab",
                        Content = plr.DisplayName .. " (@" .. plr.Name .. ") was sent to " .. loccc,
                        Duration = 6.5,
                        Image = 4483362458,
                    })
                    break
                end
            end

            if not playerFound then
                if grabbedParent.Name == "YouClone" then
                    local replicatedStorage = game:GetService("ReplicatedStorage")
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                        replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(weldConstraint.Part1)
                    end
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner") then
                        replicatedStorage.GrabEvents.SetNetworkOwner:FireServer(grabbedParent, nil)
                    end
                    task.wait()
                    local partsToCheck = {"Head", "HumanoidRootPart", "Torso", "Right Arm", "Left Arm", "Right Leg", "Left Leg"}
                    for _, partName in pairs(partsToCheck) do
                        local part = grabbedParent:FindFirstChild(partName)
                        if part and part:IsA("BasePart") then
                            part.CFrame = locc
                        end
                    end
                    Rayfield:Notify({
                        Title = "Prison Grab",
                        Content = "Clone was sent to " .. loccc,
                        Duration = 6.5,
                        Image = 4483362458,
                    })
                elseif grabbedParent.Name == "CreatureBlobman" then
                    local replicatedStorage = game:GetService("ReplicatedStorage")
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                        replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(weldConstraint.Part1)
                    end
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner") then
                        replicatedStorage.GrabEvents.SetNetworkOwner:FireServer(grabbedParent, nil)
                    end
                    task.wait()
                    local humanoidRootPart = grabbedParent:FindFirstChild("HumanoidRootPart")
                    if humanoidRootPart and humanoidRootPart:IsA("BasePart") then
                        humanoidRootPart.CFrame = locc
                    end
                    Rayfield:Notify({
                        Title = "Prison Grab",
                        Content = "Blobman was sent to " .. loccc,
                        Duration = 6.5,
                        Image = 4483362458,
                    })
                elseif grabbedParent:FindFirstChild("SoundPart") then
                    local replicatedStorage = game:GetService("ReplicatedStorage")
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine") then
                        replicatedStorage.GrabEvents.DestroyGrabLine:FireServer(weldConstraint.Part1)
                    end
                    if replicatedStorage:FindFirstChild("GrabEvents") and replicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner") then
                        replicatedStorage.GrabEvents.SetNetworkOwner:FireServer(grabbedParent, nil)
                    end
                    task.wait()
                    local soundPart = grabbedParent:FindFirstChild("SoundPart")
                    if soundPart and soundPart:IsA("BasePart") then
                        soundPart.CFrame = locc
                    end
                    Rayfield:Notify({
                        Title = "Prison Grab",
                        Content = grabbedParent.Name .. " was sent to " .. loccc,
                        Duration = 6.5,
                        Image = 4483362458,
                    })
                end
            end
        end)

        if not success then
            warn("Error in Send To Location: " .. (err or "Unknown error"))
            Rayfield:Notify({
                Title = "Error",
                Content = "Failed to send to prison (check console)",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

AtivatePrison = GrabTab:CreateToggle({
    Name = "Activate Send To Location grab",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        prisonactive = Value
        if Value then
            Rayfield:Notify({
                Title = "Send to prison enabled",
                Content = "Press Q to send to prison",
                Duration = 6.5,
                Image = 4483362458,
            })
        else
            Rayfield:Notify({
                Title = "Send to prison disabled",
                Content = "Prison grab",
                Duration = 6.5,
                Image = 4483362458,
            })
        end
    end,
})

local TpTab = Window:CreateTab("Teleport n plot", "user")

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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateButton({
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

TpTab:CreateDivider()

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local destroyBarrierActive = false
local destroyBarrierThread = nil
local plotsBroken = false

TpTab:CreateToggle({
    Name = "Break Plot Barrier (using FoodCoconugger test by gru)",
    CurrentValue = false,
    Flag = "Toggle1",
    Callback = function(Value)
        destroyBarrierActive = Value

        if destroyBarrierThread then
            task.cancel(destroyBarrierThread)
            destroyBarrierThread = nil
        end

        if Value then
            destroyBarrierThread = task.spawn(function()
                while destroyBarrierActive do
                    pcall(function()
                        local argsSpawn = {
                            "FoodCoconut",
                            CFrame.new(242.54727172851562, -5.691725254058838, 451.2679443359375, 0.7603510022163391, -0.20612803101539612, 0.6159363985061646, 0, 0.9483057856559753, 0.3173581063747406, -0.6495124101638794, -0.24130354821681976, 0.7210452556610107),
                            Vector3.new(0, 40.505001068115234, 0)
                        }
                        ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"):InvokeServer(unpack(argsSpawn))


                        task.wait(0)

                        local spawnedToysFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                        local coconut = spawnedToysFolder and spawnedToysFolder:WaitForChild("FoodCoconut", 1)
                        if coconut then
                            local argsHold = {
                                coconut,
                                LocalPlayer.Character
                            }
                            coconut:WaitForChild("HoldPart"):WaitForChild("HoldItemRemoteFunction"):InvokeServer(unpack(argsHold))

                            local argsUse = {
                                coconut
                            }
                            ReplicatedStorage:WaitForChild("HoldEvents"):WaitForChild("Use"):FireServer(unpack(argsUse))
                            local targetCFrame = CFrame.new(242.54727172851562, -7.691725254058838, 451.2679443359375)
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                LocalPlayer.Character.HumanoidRootPart.CFrame = targetCFrame
                            end
                            ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy"):FireServer(coconut)
                        end
                        task.wait(0)
                        local plotBarrierExists = false
                        for _, prt in pairs(Workspace.Plots:GetDescendants()) do
                            if prt.Name == "PlotBarrier" or prt.Name == "FalseBorder" then
                                plotBarrierExists = true
                                break
                            end
                        end
                        if not plotBarrierExists then
                            plotsBroken = true
                            destroyBarrierActive = false
                            Rayfield:Notify({
                                Title = "done hoes",
                                Content = "The plot barrier has been broken niggers",
                                Duration = 5,
                                Image = 4483345998,
                                Actions = {
                                    Ignore = {
                                        Name = "Awesome",
                                        Callback = function()
                                            print("Deliscimo")
                                        end
                                    },
                                },
                            })
                            
                        end
                    end)
                end
            end)
        else
            local spawnedToysFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
            if spawnedToysFolder then
                for _, item in ipairs(spawnedToysFolder:GetChildren()) do
                    if item.Name == "FoodCoconut" then
                        ReplicatedStorage.MenuToys.DestroyToy:FireServer(item)
                    end
                end
            end
        end
    end,
})
for _, prt in pairs(game:GetService("Workspace").Plots:GetDescendants()) do
    if prt.Name == "PlotArea" then
        prt.CanQuery = false
        prt.CanCollide = false
    elseif prt.Name == "PlotBarrier" or prt.Name == "FalseBorder" then
        prt.CanQuery = true
        prt.CanCollide = true
    end
end


Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 Workspace = game:GetService("Workspace")

local StatusLabel = TpTab:CreateLabel("Broken: False")

destroyBarrierActive = false
destroyBarrierThread = nil
plotsBroken = false

TpTab:CreateToggle({
    Name = "Destroy Barrier (snowball by yanis)",
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

TimeLabel = TpTab:CreateLabel("Current Time: ...",10111107788, Color3.fromRGB(5, 0, 10), false)
TpTab:CreateSlider({
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
TpTab:CreateToggle({
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

TpTab:CreateDivider()

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
            debugPrint("Tentative d'assise sur:", closest.Name)
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
        debugPrint("Tentative d'assise sur Blobman spécifique")
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
        
        debugPrint("Séquence ULTRA rapide démarrée")
        
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
            debugPrint("Assis sur le Blobman")
            task.wait(0)
        end
        
        teleportToPosition(targetCFrame)
        debugPrint("TP instantané à la cible")
        task.wait(0)
        
        getOffBlobman()
        debugPrint("Descendu instantanément")
        task.wait(0)
        
        if originalPosition then
            teleportToPosition(originalPosition)
            debugPrint("Retour instantané")
        end
        
        processingBlobmans[blobman] = nil
        debugPrint("Séquence ULTRA rapide terminée")
    end)
end

Workspace.DescendantAdded:Connect(function(descendant)
    if not autoBlobmanEnabled then return end
    
    if descendant.Name == "CreatureBlobman" and descendant:IsA("Model") then
        task.wait(0.1)
        executeBlobmanSequence(descendant)
    end
end)


TpTab:CreateToggle({
    Name = "Delete blob (when sm1 spawn a blob sit on it and put in da water to delete works for plots)",
    CurrentValue = false,
    Flag = "AutoBlobman",
    Callback = function(Value)
        autoBlobmanEnabled = Value
        if Value then
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "Système activé - En attente de Blobmans",
                Duration = 3
            })
        else
            Rayfield:Notify({
                Title = "Auto Blobman",
                Content = "Système désactivé",
                Duration = 2
            })
            processingBlobmans = {}
        end
    end
})

RunService.RenderStepped:Connect(chk)


local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

local Tab = Window:CreateTab("Blobman", 4483362458)

local loopActive = false
local teleportLoopActive = false
local seatLoopActive = false


local function startLoops()
    loopActive = true
    local player = game.Players.LocalPlayer
    local spawnedInToys = workspace:FindFirstChild(player.Name .. "SpawnedInToys")

    if not spawnedInToys then
        warn("SpawnedInToys não encontrado.")
        return
    end

    local creatureBlobman = spawnedInToys:WaitForChild("CreatureBlobman")
    local blobmanSeat = creatureBlobman:WaitForChild("VehicleSeat")
    local proximityPrompt = blobmanSeat:WaitForChild("ProximityPrompt")

    while loopActive do
        wait() 

        
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = blobmanSeat.CFrame
        end

        
        if proximityPrompt.Enabled then
            
            proximityPrompt:InputHoldBegin(nil)
            wait()
            proximityPrompt:InputHoldEnd(nil)
        end


        if not player or not player.Parent then
            break
        end
    end
end


local function stopLoops()
    loopActive = false
end


Tab:CreateToggle({
    Name = "Loop Seat On Blobman",
    Default = false,
    Callback = function(Value)
        if Value then
            startLoops()
        else
            stopLoops()
        end
    end    
})


local player = game.Players.LocalPlayer
local spawnedInToys = workspace:FindFirstChild(player.Name .. "SpawnedInToys")

if not spawnedInToys then
    Rayfield:MakeNotification({
        Name = "Erro!",
        Content = "SpawnedInToys não encontrado!",
        Image = "rbxassetid://4483345998",
        Time = 5
    })
    return
end

local trackedBlobmans = {}

local function trackBlobmans()
    for _, toy in ipairs(spawnedInToys:GetChildren()) do
        if toy.Name == "CreatureBlobman" and not trackedBlobmans[toy] then
            local grabbableHitbox = toy:FindFirstChild("GrabbableHitbox")
            if grabbableHitbox then
                trackedBlobmans[toy] = grabbableHitbox
                print("Novo Blobman detectado!")
            end
        end
    end
end

task.spawn(function()
    while true do
        trackBlobmans()
        task.wait(0.5)
    end
end)

Tab:CreateToggle({
    Name = "Freeze Blobman",
    Default = false,  
    Callback = function(value)
        for _, grabbableHitbox in pairs(trackedBlobmans) do
            grabbableHitbox.Anchored = value
        end

        Rayfield:MakeNotification({
            Name = "Updated Anchor",
            Content = "The anchor was " .. (value and "enabled" or "disabled") .. " for all Blobmans.",
            Image = "rbxassetid://4483345998",
            Time = 5
        })
        print("Anchor " .. (value and "enabled" or "disabled") .. " for all Blobmans")
    end    
})


local cfg = {
    enabled = true,
    highlight = true,
    notify = true,
    autoTP = false,
    autoTPToBlobman = false,
    autoSitOnBlobman = false,
    autoClaimPlot = false,
    tpOffset = Vector3.new(0, 5, 0),
    cooldown = 0.5
}

local selectionFor = {}
local lastSeenAt = setmetatable({}, {__mode="k"})

local function debugPrint(...)
    print("[BLOBMAN]", ...)
end

local function notify(title, content, dur)
    if not cfg.notify then return end
    safeNotify({Title = title, Content = content or "", Duration = dur or 3})
end

local VNC = { enabled = false, connection = nil }

function VNC:start()
    if self.enabled then return end
    self.enabled = true
    self.connection = RunService.Stepped:Connect(function()
        local char = LocalPlayer.Character
        if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local seat = hum and hum.SeatPart
        if seat and seat:IsA("VehicleSeat") then
            for _, v in ipairs(seat.Parent:GetDescendants()) do
                if v:IsA("BasePart") then v.CanCollide = false end
            end
        end
    end)
end

function VNC:stop()
    self.enabled = false
    if self.connection then self.connection:Disconnect(); self.connection = nil end
end


local function getBlobmanPosition(blobman)
    if blobman:FindFirstChild("HumanoidRootPart") then
        return blobman.HumanoidRootPart.CFrame
    elseif blobman:FindFirstChild("VehicleSeat") then
        return blobman.VehicleSeat.CFrame
    elseif blobman.PrimaryPart then
        return blobman:GetPrimaryPartCFrame()
    else
        for _, part in ipairs(blobman:GetChildren()) do
            if part:IsA("BasePart") then
                return part.CFrame
            end
        end
    end
    return nil
end

local function teleportToBlobman(blobman)
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        debugPrint("Impossible de TP: pas de personnage")
        return false
    end
    
    local blobmanCFrame = getBlobmanPosition(blobman)
    if not blobmanCFrame then
        debugPrint("Impossible de TP: position du Blobman introuvable")
        return false
    end
    
    local targetCFrame = blobmanCFrame + cfg.tpOffset
    LocalPlayer.Character.HumanoidRootPart.CFrame = targetCFrame
    
    debugPrint("TP effectué vers Blobman à la position:", targetCFrame.Position)
    return true
end

local function sitOnBlobman(blobman)
    local seat = blobman:FindFirstChild("VehicleSeat")
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
    if seat and hum and not seat.Occupant then
        seat:Sit(hum)
        return true
    end
    return false
end

local function sitOnClosestBlobman()
    local closest, dist = nil, math.huge
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj.Name == "CreatureBlobman" and obj:IsA("Model") then
            local cf = getBlobmanPosition(obj)
            if cf then
                local d = (root.Position - cf.Position).Magnitude
                if d < dist then dist = d; closest = obj end
            end
        end
    end
    return closest and sitOnBlobman(closest) or false
end

Tab:CreateButton({
    Name = "TP to nearest blob",
    Callback = function()
        local closestBlobman = nil
        local closestDistance = math.huge
        local playerPos = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.HumanoidRootPart.Position
        
        if not playerPos then
            Rayfield:Notify({Title = "Erreur", Content = "Impossible de trouver votre position", Duration = 3})
            return
        end
        
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj.Name == "CreatureBlobman" and obj:IsA("Model") then
                local blobmanCFrame = getBlobmanPosition(obj)
                if blobmanCFrame then
                    local distance = (playerPos - blobmanCFrame.Position).Magnitude
                    if distance < closestDistance then
                        closestDistance = distance
                        closestBlobman = obj
                    end
                end
            end
        end
        
        if closestBlobman then
            if teleportToBlobman(closestBlobman) then
                local ownerGuess = guessOwnerNameFromParent(closestBlobman)
                Rayfield:Notify({
                    Title = "TP Réussi", 
                    Content = "Téléporté vers Blobman" .. (ownerGuess and (" de " .. ownerGuess) or ""), 
                    Duration = 3
                })
            end
        else
            Rayfield:Notify({Title = "Aucun Blobman", Content = "Aucun CreatureBlobman trouvé", Duration = 3})
        end
    end
})

Tab:CreateButton({
    Name = "Sit on nearest blob (works for the one in da plot)",
    Callback = function()
        debugPrint("=== BOUTON ASSISE MANUELLE ===")
        local success = sitOnClosestBlobman()
        
        if success then
            Rayfield:Notify({
                Title = "Assis!", 
                Content = "Assis sur le Blobman le plus proche", 
                Duration = 3
            })
        else
            Rayfield:Notify({Title = "Échec", Content = "Impossible de s'asseoir sur un Blobman", Duration = 3})
        end
    end
})


local Section = Tab:CreateSection("Blobman control players")

local AutoWhitelist = false
local LoopEnabled = false
local FollowMode = "Poisson 2 (in wall)"
local FollowOffset = Vector3.new(0, 0, 0)
local AURA_RADIUS = 30
local Mode = "Object"
local IsProcessing = false

local CustomCoordinates = {
    ["Poisson 2 (in wall)"] = Vector3.new(52, -69, 219)
}

local function getMapObject(objectName)
    if CustomCoordinates[objectName] then
        return {Position = CustomCoordinates[objectName]}
    end
    
    local mapPath = workspace:FindFirstChild("Map")
    if not mapPath then return nil end
    
    local tweenedObjects = mapPath:FindFirstChild("AlwaysHereTweenedObjects")
    if not tweenedObjects then return nil end
    
    local object = tweenedObjects:FindFirstChild(objectName)
    if not object then return nil end
    
    local objectModel = object:FindFirstChild("Object")
    if not objectModel then return nil end
    
    return objectModel:FindFirstChild("FollowThisPart")
end


local function getBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        for _, v in pairs(workspace:GetDescendants()) do
            if v.Name == "BlobmanSeatAndOwnerScript" and v.Parent.Name == "CreatureBlobman" then
                if not humanoid.SeatPart then
                    local seat = v.Parent:FindFirstChildWhichIsA("VehicleSeat")
                    if seat then
                        humanoid.Sit = true
                        seat:Sit(humanoid)
                        task.wait(0.5)
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

local function isOnBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        if humanoid.SeatPart and humanoid.SeatPart.Parent and humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
            return true
        end
    end
    return false
end

local function getDetector(blob, side)
    local detector = blob:FindFirstChild(side.."Detector")
    local weld = detector and detector:FindFirstChild(side.."Weld")
    return detector, weld
end

local function isInAura(playerRoot)
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return false end
    
    local myPos = myChar.HumanoidRootPart.Position
    local distance = (playerRoot.Position - myPos).Magnitude
    
    return distance <= AURA_RADIUS
end

local function addFollowTrail(char)
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    if char:FindFirstChild("FollowTrail") then return end

    local trail = Instance.new("Trail")
    trail.Name = "FollowTrail"
    trail.Parent = char
    trail.Lifetime = 1.2
    trail.Color = ColorSequence.new(
        Color3.fromRGB(255, 100, 0),
        Color3.fromRGB(255, 255, 0)
    )
    trail.LightEmission = 1
    trail.Transparency = NumberSequence.new(0, 1)
    trail.WidthScale = NumberSequence.new(1.5, 0)
    
    local att0 = Instance.new("Attachment", hrp)
    local att1 = Instance.new("Attachment", hrp)
    att1.Position = Vector3.new(0, 2, 0)
    
    trail.Attachment0 = att0
    trail.Attachment1 = att1
end



local function removeAllTrails()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character and plr.Character:FindFirstChild("FollowTrail") then
            plr.Character.FollowTrail:Destroy()
        end
    end
end

local function removeTrail(char)
    if char and char:FindFirstChild("FollowTrail") then
        char.FollowTrail:Destroy()
    end
end

local ProcessedPlayers = {}

local function ReleasePlayer(blob, char)
    if not blob or not char then return end
    
    local detector, weld = getDetector(blob, "Right")
    if not (detector and weld) then return end
    
    for _, part in ipairs(char:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
        end
    end

    removeTrail(char)
end

local function ProcessPlayer(blob, char, targetPos)
    if IsProcessing or ProcessedPlayers[char.Name] then return end
    
    IsProcessing = true 
    ProcessedPlayers[char.Name] = true
    
    local detector, weld = getDetector(blob, "Right")
    if not (detector and weld and char) then 
        IsProcessing = false
        return 
    end

    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then 
        IsProcessing = false
        return 
    end

    local currentGrab = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
    if not currentGrab then
        IsProcessing = false
        return
    end

    currentGrab:FireServer(blob.RightDetector, hrp, weld)
    task.wait(0.2)

    for _, part in ipairs(char:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
            part.AssemblyLinearVelocity = Vector3.new(0, 30, 0)
            blob.BlobmanSeatAndOwnerScript.CreatureDrop:FireServer(weld, part)
        end
    end

    addFollowTrail(char)
    task.wait(0.3)

    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(targetPos)
        char.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
        char.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero

        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Velocity = Vector3.zero
                part.RotVelocity = Vector3.zero
            end
        end
        
        task.wait(0.2)

        ReleasePlayer(blob, char)
    end

    IsProcessing = false

    task.wait(1)
end

local function startFollow()
    local targetPos
    
    if Mode == "Object" then
        local targetObject = getMapObject(FollowMode)
        if not targetObject then
            Rayfield:Notify({
                Title = "Erreur",
                Content = "Objet " .. FollowMode .. " introuvable!",
                Duration = 4,
                Image = 4483362458
            })
            return
        end
        targetPos = targetObject.Position + FollowOffset
        
    elseif Mode == "Me" then
        local myChar = LocalPlayer.Character
        if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then
            Rayfield:Notify({
                Title = "Erreur",
                Content = "Personnage introuvable!",
                Duration = 4,
                Image = 4483362458
            })
            return
        end
        targetPos = myChar.HumanoidRootPart.Position + FollowOffset
    end

    local blob = getBlobman()
    if not blob then
        Rayfield:Notify({
            Title = "Erreur",
            Content = "Blobman introuvable! Monte sur un Blobman.",
            Duration = 4,
            Image = 4483362458
        })
        return
    end

    Rayfield:Notify({
        Title = "Follow Mode",
        Content = "Grab",
        Duration = 2,
        Image = 4483362458
    })

    task.spawn(function()
        while LoopEnabled do
            if isOnBlobman() and not IsProcessing then
                local currentBlob = getBlobman()
                if currentBlob then
                    local currentTargetPos = targetPos
                    if Mode == "Me" then
                        local myChar = LocalPlayer.Character
                        if myChar and myChar:FindFirstChild("HumanoidRootPart") then
                            currentTargetPos = myChar.HumanoidRootPart.Position + FollowOffset
                        end
                    end
                    for _, plr in ipairs(Players:GetPlayers()) do
                        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                            if not (AutoWhitelist and LocalPlayer:IsFriendsWith(plr.UserId)) then
                                local char = plr.Character
                                local root = char.HumanoidRootPart
                                if isInAura(root) and not ProcessedPlayers[char.Name] then
                                    ProcessPlayer(currentBlob, char, currentTargetPos)
                                    break
                                end
                            end
                        end
                    end
                end
            end
            task.wait(0.5)
        end
    end)
end

Tab:CreateDropdown({
   Name = "follow mode",
   Options = {"Object", "Me"},
   CurrentOption = {"Object"},
   MultipleOptions = false,
   Callback = function(Option)
        Mode = Option[1] or Option
    end
})

Tab:CreateDropdown({
   Name = "Object to follow",
   Options = {"Poisson 2 (in wall)"},
   CurrentOption = {"Poisson 2 (in wall)"},
   MultipleOptions = false,
   Callback = function(Option)
        FollowMode = Option[1] or Option
    end
})

Tab:CreateToggle({
    Name = "Follow",
    CurrentValue = false,
    Callback = function(Value)
        LoopEnabled = Value
        if Value then
            ProcessedPlayers = {}
            IsProcessing = false
            startFollow()
        else
            removeAllTrails()
            ProcessedPlayers = {}
            IsProcessing = false
            Rayfield:Notify({
                Title = "Follow",
                Content = "Suivi arrêté!",
                Duration = 2,
                Image = 4483362458
            })
        end
    end
})

Tab:CreateToggle({
    Name = "AutoWhitelist",
    CurrentValue = false,
    Callback = function(Value)
        AutoWhitelist = Value
    end
})

Tab:CreateSlider({
    Name = "Rayon Aura",
    Range = {10, 50},
    Increment = 5,
    Suffix = " studs",
    CurrentValue = 30,
    Callback = function(Value)
        AURA_RADIUS = Value
    end
})

Tab:CreateButton({
    Name = "Reset players if client in case",
    Callback = function()
        ProcessedPlayers = {}
        removeAllTrails()
        IsProcessing = false
        Rayfield:Notify({
            Title = "Reset",
            Content = "Liste réinitialisée!",
            Duration = 2,
            Image = 4483362458
        })
    end
})

local Section = Tab:CreateSection("All blob shits there")

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local TargetedPlayers = {}
local WhitelistFriends3 = false
local kickTargetPlayerName = ""
local kickTimerStartTime = nil
local kickTimerActive = false
local kickTimerConnection = nil
local kickGettingkicked = {}


local kickGrabActive = false
local kickQuickActive = false
local kickGrabDelay = 0.2
local kickFlySpeed = 75
local kickMaxDistance = 10
local kickDestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
local kickSetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
local kickDestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
local kickSpawnToyRemoteFunction = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
local KickAutoGrabToggle = nil
local KickQuickToggle = nil


local function getPlayerOptions()
    local options = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= Players.LocalPlayer then
            table.insert(options, player.DisplayName .. " [" .. player.Name .. "]")
        end
    end
    return options
end


local PlayerDropdown = Tab:CreateDropdown({
    Name = "Select bud's'",
    Options = getPlayerOptions(),
    MultipleOptions = true,
    CurrentOption = {},
    Callback = function(selected)
        TargetedPlayers = {}
        for _, name in ipairs(selected) do
            local realname = name:match("%[(.-)%]")
            if realname then
                table.insert(TargetedPlayers, realname)
            end
        end
        print("Joueurs ciblés :", table.concat(TargetedPlayers, ", "))
    end
})

local function updateDropdown()
    local newOptions = getPlayerOptions()
    PlayerDropdown:Refresh(newOptions)
end

Players.PlayerAdded:Connect(updateDropdown)
Players.PlayerRemoving:Connect(updateDropdown)

Tab:CreateInput({
    Name = "Target with users",
    PlaceholderText = "niha user...",
    RemoveTextAfterFocusLost = false,
    Callback = function(Text)
        kickTargetPlayerName = Text
    end
})

Tab:CreateButton({
    Name = "Reset Target",
    Callback = function()
        kickTargetPlayerName = ""
        Rayfield:Notify({
            Title = "Reset",
            Content = "Target réinitialisée",
            Duration = 2,
            Image = 4483345998
        })
    end
})

Tab:CreateToggle({
    Name = "Whitelist Friends",
    CurrentValue = false,
    Flag = "WhitelistFriends",
    Callback = function(Value)
        WhitelistFriends3 = Value
        if Value then
            print("Whitelist Friends activé - Les amis ne seront pas ciblés")
        else
            print("Whitelist Friends désactivé - Tous les joueurs seront ciblés")
        end
    end,
})

local Section = Tab:CreateSection("Blob kill")

local KickLoopTargeted = false
local KickLoopGlobal = false

local function getBlobman()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local humanoid = LocalPlayer.Character.Humanoid
        for _, v in pairs(workspace:GetDescendants()) do
            if v.Name == "BlobmanSeatAndOwnerScript" and v.Parent.Name == "CreatureBlobman" then
                if humanoid.SeatPart and humanoid.SeatPart.Parent == v.Parent then
                    return v.Parent
                end
            end
        end
    end
    return nil
end

local function GrabAndKill(blob, targetChar, side)
    local detector = blob:FindFirstChild(side .. "Detector")
    local weld = detector and detector:FindFirstChild(side .. "Weld")
    if not (detector and weld and targetChar) then return end
    if targetChar == blob.PrimaryPart then return end

    local humanoid = targetChar:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.Sit = false
        humanoid:ChangeState(Enum.HumanoidStateType.Dead)
        humanoid.BreakJointsOnDeath = true
        humanoid.Health = 0
    end

    for _, part in ipairs(targetChar:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
            part.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
        end
    end
end

local function killLoopTargeted()
    local blob = getBlobman()
    if not blob then
        warn("Blobman not found.")
        return
    end

    local grabScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    local grab = grabScript and grabScript:FindFirstChild("CreatureGrab")
    local weld = blob:FindFirstChild("RightDetector") and blob.RightDetector:FindFirstChild("RightWeld")

    if not (grab and weld) then
        warn(" Grab/weld missing.")
        return
    end

    task.spawn(function()
        while KickLoopTargeted do
            local targetPlayers = {}
            if kickTargetPlayerName ~= "" then
                for _, plr in ipairs(Players:GetPlayers()) do
                    if plr.Name:lower():find(kickTargetPlayerName:lower()) and plr ~= LocalPlayer then
                        table.insert(targetPlayers, plr)
                    end
                end
            else
                for _, targetName in ipairs(TargetedPlayers) do
                    local player = Players:FindFirstChild(targetName)
                    if player and player ~= LocalPlayer then
                        table.insert(targetPlayers, player)
                    end
                end
            end

            for _, plr in ipairs(targetPlayers) do
                if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local char = plr.Character
                    local root = char.HumanoidRootPart
                    grab:FireServer(blob.RightDetector, root, weld)

                    task.delay(0.1, function()
                        if char and char:FindFirstChildOfClass("Humanoid") and char:FindFirstChild("Head") then
                            GrabAndKill(blob, char, "Right")
                        end
                    end)
                end
            end
            task.wait(0.5)
        end
    end)
end

local function killLoopGlobal()
    local blob = getBlobman()
    if not blob then
        warn(" Blobman not found.")
        return
    end

    local grabScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    local grab = grabScript and grabScript:FindFirstChild("CreatureGrab")
    local weld = blob:FindFirstChild("RightDetector") and blob.RightDetector:FindFirstChild("RightWeld")

    if not (grab and weld) then
        warn(" Grab/weld missing.")
        return
    end

    task.spawn(function()
        while KickLoopGlobal do
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local skip = WhitelistFriends3 and LocalPlayer:IsFriendsWith(plr.UserId)
                    if not skip then
                        local char = plr.Character
                        local root = char.HumanoidRootPart
                        grab:FireServer(blob.RightDetector, root, weld)

                        task.delay(0.1, function()
                            if char and char:FindFirstChildOfClass("Humanoid") and char:FindFirstChild("Head") then
                                GrabAndKill(blob, char, "Right")
                            end
                        end)
                    end
                end
            end
            task.wait(0.5)
        end
    end)
end

Tab:CreateToggle({
    Name = "Loop GrabKill ciblé",
    CurrentValue = false,
    Callback = function(value)
        KickLoopTargeted = value
        if value then
            killLoopTargeted()
        end
    end,
})

Tab:CreateToggle({
    Name = "Loop GrabKill global",
    CurrentValue = false,
    Callback = function(value)
        KickLoopGlobal = value
        if value then
            killLoopGlobal()
        end
    end,
})


local Section = Tab:CreateSection("Kick's'")

local function getKickTargetPlayer()
    if kickTargetPlayerName ~= "" then
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr.Name:lower():find(kickTargetPlayerName:lower()) and plr ~= LocalPlayer then
                return plr
            end
        end
    else
        if #TargetedPlayers > 0 then
            for _, targetName in ipairs(TargetedPlayers) do
                local player = Players:FindFirstChild(targetName)
                if player and player ~= LocalPlayer then
                    return player
                end
            end
        end
    end
    return nil
end

local function spawnKickBlobman()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return nil end
    
    local spawnCFrame = char.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0)
    kickSpawnToyRemoteFunction:InvokeServer("CreatureBlobman", spawnCFrame, Vector3.new(0, 0, 0))
    
    task.wait(0)
    
    local toysFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if toysFolder then
        local blobman = toysFolder:FindFirstChild("CreatureBlobman")
        if blobman and blobman:IsA("Model") then
            return blobman
        end
    end
    
    return nil
end

local function sitOnKickBlobman(blobman)
    if not blobman or not blobman:IsA("Model") then return false end
    
    local seat = blobman:FindFirstChild("VehicleSeat")
    if not seat then return false end
    
    local char = LocalPlayer.Character
    if not char then return false end
    
    local humanoid = char:FindFirstChild("Humanoid")
    if humanoid then
        seat:Sit(humanoid)
        task.wait(0)
        return true
    end
    return false
end

local function findMountedKickBlobman()
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name == "CreatureBlobman" then
            if obj:FindFirstChild("VehicleSeat") and obj.VehicleSeat:FindFirstChild("SeatWeld") then
                local weldedPart = obj.VehicleSeat.SeatWeld.Part1
                if weldedPart and weldedPart.Parent == LocalPlayer.Character then
                    return obj
                end
            end
        end
    end
    return nil
end

local function teleportToKick(target)
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") and target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
    end
end

local function isNearKickTarget(target)
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return false end
    if not target or not target.Character or not target.Character:FindFirstChild("HumanoidRootPart") then return false end
    
    local distance = (myChar.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
    return distance <= kickMaxDistance
end

local function grabKickRoutine(blobman)
    local target = getKickTargetPlayer()
    if not target or not target.Character or not target.Character:FindFirstChild("HumanoidRootPart") then 
        return false
    end
    
    local targetHRP = target.Character.HumanoidRootPart
    
    Rayfield:Notify({
        Title = "TP",
        Content = "Téléportation vers la cible...",
        Duration = 2,
        Image = 4483345998
    })
    teleportToKick(target)
    task.wait(0.3)
    
    if not isNearKickTarget(target) then
        Rayfield:Notify({
            Title = "Erreur TP",
            Content = "TP raté, réessayez",
            Duration = 3,
            Image = 4483345998
        })
        return false
    end
    
    Rayfield:Notify({
        Title = "Kick",
        Content = "Lancement du kick...",
        Duration = 2,
        Image = 4483345998
    })
    
    kickSetNetworkOwner:FireServer(targetHRP, targetHRP.CFrame)
    kickDestroyGrabLine:FireServer(targetHRP)
    task.wait(0.3)
    
    local bp = Instance.new("BodyPosition")
    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bp.D = 200
    bp.P = kickFlySpeed * 1000
    bp.Position = targetHRP.Position + Vector3.new(0, 20, 0)
    bp.Parent = targetHRP
    task.wait(0.3)
    bp:Destroy()
    
    local leftDetector = blobman:FindFirstChild("LeftDetector")
    if leftDetector and leftDetector:FindFirstChild("LeftWeld") and blobman:FindFirstChild("BlobmanSeatAndOwnerScript") then
        local grabEvent = blobman.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")
        if grabEvent then
            grabEvent:FireServer(leftDetector, targetHRP, leftDetector.LeftWeld)
        end
    end
    
    kickTimerStartTime = tick()
    kickTimerActive = true
    kickDestroyToy:FireServer(blobman)
    
    return true
end

local function startAutoKickGrab()
    kickGrabActive = true
    local hasSetupBlobman = false
    
    while kickGrabActive do
        local blobman = findMountedKickBlobman()
        
        if not blobman and not hasSetupBlobman then
            Rayfield:Notify({
                Title = "Auto-Setup",
                Content = "Spawn du Blobman...",
                Duration = 2,
                Image = 4483345998
            })
            blobman = spawnKickBlobman()
            
            if blobman then
                if sitOnKickBlobman(blobman) then
                    Rayfield:Notify({
                        Title = "Auto-Setup",
                        Content = "Assis sur le Blobman!",
                        Duration = 2,
                        Image = 4483345998
                    })
                    hasSetupBlobman = true
                else
                    Rayfield:Notify({
                        Title = "Erreur",
                        Content = "Impossible de s'asseoir!",
                        Duration = 3,
                        Image = 4483345998
                    })
                    kickGrabActive = false
                    if KickAutoGrabToggle then
                        KickAutoGrabToggle:Set(false)
                    end
                    break
                end
            else
                Rayfield:Notify({
                    Title = "Erreur",
                    Content = "Impossible de spawn le Blobman!",
                    Duration = 3,
                    Image = 4483345998
                })
                kickGrabActive = false
                if KickAutoGrabToggle then
                    KickAutoGrabToggle:Set(false)
                end
                break
            end
        end
        
        if blobman and hasSetupBlobman then
            local success = grabKickRoutine(blobman)
            if success then
                kickGrabActive = false
                if KickAutoGrabToggle then
                    KickAutoGrabToggle:Set(false)
                end
                break
            end
        end
        
        task.wait(kickGrabDelay)
    end
end

KickAutoGrabToggle = Tab:CreateToggle({
    Name = "Kick players(use setnetownership and blob grab)",
    CurrentValue = false,
    Flag = "AutoKickGrab",
    Callback = function(Value)
        kickGrabActive = Value
        if Value then
            startAutoKickGrab()
        end
    end
})

 AuraSettings = {
    Enabled = false,
    Range = 50,
    ScanRate = 0.2,
    LiftHeight = 20,
    LiftPower = 50,
    AutoDestroy = true,
    ShowNotifications = true,
    BringAllEnabled = false,
    WhitelistFriends = false
}

 AuraState = {
    Active = false,
    TargetsInRange = {},
    CurrentBlobman = nil,
    ProcessingTarget = false,
    TimerStart = nil,
    TimerActive = false,
    BlackHoleTracking = {}
}

 BringState = {
    Active = false,
    Queue = {},
    SavedPos = nil,
    SavedCamCFrame = nil,
    Radius = 15,
    Connection = nil
}

GameEvents = {
    DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy"),
    SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"),
    DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
}

camBlocker = Instance.new("Part", Workspace)
camBlocker.Anchored = true
camBlocker.CanCollide = false
camBlocker.Transparency = 1
camBlocker.CanQuery = false
camBlocker.Size = Vector3.new(10,10,10)

 function freezeCamera()
    local cam = Workspace.CurrentCamera
    camBlocker.CFrame = BringState.SavedCamCFrame
    camBlocker.Parent = Workspace
    cam.CameraType = Enum.CameraType.Scriptable
    cam.CFrame = BringState.SavedCamCFrame
end

function unfreezeCamera()
    camBlocker.Parent = nil
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    if BringState.SavedCamCFrame then
        cam.CFrame = BringState.SavedCamCFrame
    end
    BringState.SavedCamCFrame = nil
end

function disableCollisions(character)
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end

function inPlot(player)
    local plots = Workspace:FindFirstChild("PlotItems")
    local playersInPlots = plots and plots:FindFirstChild("PlayersInPlots")
    return playersInPlots and playersInPlots:FindFirstChild(player.Name)
end

 function inRadius(part)
    return (part.Position - BringState.SavedPos).Magnitude <= BringState.Radius
end

 function shouldIgnore(player)
    if player == LocalPlayer then return true end
    if AuraSettings.WhitelistFriends and LocalPlayer:IsFriendsWith(player.UserId) then
        return true
    end
    return false
end

 function refreshQueue()
    BringState.Queue = {}
    for _, player in pairs(Players:GetPlayers()) do
        if not shouldIgnore(player) and player.Character and not inPlot(player) then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if root and not inRadius(root) then
                table.insert(BringState.Queue, player)
            end
        end
    end
end

 function processNext()
    if #BringState.Queue == 0 then
        refreshQueue()
        if #BringState.Queue == 0 then return end
    end
    local target = BringState.Queue[1]
    table.remove(BringState.Queue, 1)
    if not target or not target.Character then return end
    local root = target.Character:FindFirstChild("HumanoidRootPart")
    local head = target.Character:FindFirstChild("Head")
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if root and head and localRoot then
        LocalPlayer.Character:PivotTo(root.CFrame * CFrame.new(0, -6, 0))
        disableCollisions(LocalPlayer.Character)
        local tries = 0
        repeat
            GameEvents.SetNetworkOwner:FireServer(root, localRoot.CFrame)
            task.wait(0.15)
            tries = tries + 1
            if tries > 20 then break end
        until (head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name) or not BringState.Active

        if BringState.Active and head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name then
            root.CFrame = CFrame.new(BringState.SavedPos)
            root.Position = BringState.SavedPos
            root.AssemblyLinearVelocity = Vector3.zero
            task.wait(0.8)
        end
    end
end

 function startBringAll()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not localRoot then return end
    BringState.SavedPos = localRoot.Position
    BringState.SavedCamCFrame = Workspace.CurrentCamera.CFrame
    refreshQueue()
    freezeCamera()
    BringState.Connection = RunService.Heartbeat:Connect(function()
        if BringState.Active then
            processNext()
            if BringState.SavedCamCFrame then
                local cam = Workspace.CurrentCamera
                cam.CameraType = Enum.CameraType.Scriptable
                cam.CFrame = BringState.SavedCamCFrame
                camBlocker.CFrame = BringState.SavedCamCFrame
                camBlocker.Parent = Workspace
            end
        end
    end)
end

 function stopBringAll()
    if BringState.Connection then
        BringState.Connection:Disconnect()
        BringState.Connection = nil
    end
    unfreezeCamera()
    local localRoot = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if localRoot and BringState.SavedPos then
        localRoot.AssemblyLinearVelocity = Vector3.zero
        localRoot.CFrame = CFrame.new(BringState.SavedPos)
    end
end
function scanPlayersInAura()
    local targets = {}
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return targets end
    
    local myPos = myChar.HumanoidRootPart.Position
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if AuraSettings.WhitelistFriends and LocalPlayer:IsFriendsWith(player.UserId) then
                continue
            end
            local theirChar = player.Character
            if theirChar and theirChar:FindFirstChild("HumanoidRootPart") then
                local distance = (theirChar.HumanoidRootPart.Position - myPos).Magnitude
                if distance <= AuraSettings.Range then
                    table.insert(targets, {
                        Player = player,
                        Distance = distance,
                        Character = theirChar
                    })
                end
            end
        end
    end
    
    table.sort(targets, function(a, b)
        return a.Distance < b.Distance
    end)
    
    return targets
end
function findActiveBlobman()
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj.Name == "CreatureBlobman" then
            local seat = obj:FindFirstChild("VehicleSeat")
            if seat and seat:FindFirstChild("SeatWeld") then
                if seat.SeatWeld.Part1 and seat.SeatWeld.Part1.Parent == LocalPlayer.Character then
                    return obj
                end
            end
        end
    end
    return nil
end

 function auraWarp(targetChar)
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("HumanoidRootPart") then return false end
    if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then return false end
    
    myChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
    return true
end

function applyAuraEffect(targetChar, blobman)
    local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
    if not targetRoot then return false end
    
    GameEvents.SetNetworkOwner:FireServer(targetRoot, targetRoot.CFrame)
    GameEvents.DestroyGrabLine:FireServer(targetRoot)
    
    local levitation = Instance.new("BodyPosition")
    levitation.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    levitation.D = 200
    levitation.P = AuraSettings.LiftPower * 1000
    levitation.Position = targetRoot.Position + Vector3.new(0, AuraSettings.LiftHeight, 0)
    levitation.Parent = targetRoot
    
    task.wait(0.3)
    
    if levitation and levitation.Parent then
        levitation:Destroy()
    end
    
    local leftDetector = blobman:FindFirstChild("LeftDetector")
    local script = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
    
    if leftDetector and leftDetector:FindFirstChild("LeftWeld") and script then
        local grabEvent = script:FindFirstChild("CreatureGrab")
        if grabEvent then
            grabEvent:FireServer(leftDetector, targetRoot, leftDetector.LeftWeld)
        end
    end
    
    if AuraSettings.AutoDestroy then
        GameEvents.DestroyToy:FireServer(blobman)
    end
    
    return true
end

 function processAuraTarget(targetData, blobman)
    if AuraState.ProcessingTarget then return end
    AuraState.ProcessingTarget = true
    
    local success = auraWarp(targetData.Character)
    if success then
        task.wait(0.1)
        applyAuraEffect(targetData.Character, blobman)
        
        if AuraSettings.ShowNotifications then
            Rayfield:Notify({
                Title = "Aura Activée",
                Content = "Cible: "..targetData.Player.DisplayName,
                Duration = 2,
                Image = 4483345998
            })
        end
    end
    
    AuraState.ProcessingTarget = false
end

function auraMainLoop()
    while AuraState.Active do
        local blobman = findActiveBlobman()
        
        if not blobman then
            Rayfield:Notify({
                Title = "Aura Désactivée",
                Content = "Blobman introuvable!",
                Duration = 3,
                Image = 4483345998
            })
            AuraState.Active = false
            break
        end
        
        AuraState.CurrentBlobman = blobman
        local targets = scanPlayersInAura()
        
        if #targets > 0 and not AuraState.ProcessingTarget then
            processAuraTarget(targets[1], blobman)
        end
        
        task.wait(AuraSettings.ScanRate)
    end
end

 function enableAura()
    AuraState.Active = true
    AuraState.TimerStart = tick()
    AuraState.TimerActive = true
    
    Rayfield:Notify({
        Title = "Aura Activée",
        Content = "Portée: "..AuraSettings.Range.." studs",
        Duration = 3,
        Image = 4483345998
    })
    
    task.spawn(auraMainLoop)
end

function disableAura()
    AuraState.Active = false
    
    if AuraState.TimerActive then
        local elapsed = tick() - AuraState.TimerStart
        AuraState.TimerActive = false
        
        Rayfield:Notify({
            Title = "Aura Désactivée",
            Content = string.format("Durée: %.2fs", elapsed),
            Duration = 3,
            Image = 4483345998
        })
    end
end





RunService.Heartbeat:Connect(function()
    if AuraState.TimerActive and AuraState.TimerStart then
        local elapsed = tick() - AuraState.TimerStart
        TimerLabel:Set(string.format("Timer: %.2fs", elapsed))
    end
end)


Tab:CreateToggle({
    Name = "kick all aura(blob)",
    CurrentValue = false,
    Flag = "AuraToggle",
    Callback = function(value)
        if value then
            enableAura()
        else
            disableAura()
        end
    end
})

Tab:CreateSlider({
    Name = "aura",
    Range = {10, 100},
    Increment = 5,
    CurrentValue = 50,
    Flag = "AuraRange",
    Callback = function(value)
        AuraSettings.Range = value
        RangeLabel:Set("Portée Aura: "..value.." studs")
    end
})

Tab:CreateSlider({
    Name = "rate",
    Range = {0.1, 1},
    Increment = 0.05,
    CurrentValue = 0.2,
    Flag = "ScanRate",
    Callback = function(value)
        AuraSettings.ScanRate = value
    end
})

Tab:CreateToggle({
    Name = "Auto delete blob (if u kick all don't use')",
    CurrentValue = true,
    Flag = "AutoDestroy",
    Callback = function(value)
        AuraSettings.AutoDestroy = value
    end
})

Tab:CreateToggle({
    Name = "Notifications Aura",
    CurrentValue = true,
    Flag = "AuraNotifs",
    Callback = function(value)
        AuraSettings.ShowNotifications = value
    end
})


Tab:CreateSection("Bring All for kick all for fun !")

Tab:CreateToggle({
    Name = "Whitelist Friends(for bring and kick)",
    CurrentValue = false,
    Flag = "WhitelistFriends",
    Callback = function(value)
        AuraSettings.WhitelistFriends = value
    end
})

Tab:CreateToggle({
    Name = "Bring All Players",
    CurrentValue = false,
    Flag = "BringAllToggle",
    Callback = function(value)
        BringState.Active = value
        if value then
            startBringAll()
            Rayfield:Notify({
                Title = "Bring All",
                Content = "Système activé!",
                Duration = 3,
                Image = 4483345998
            })
        else
            stopBringAll()
            Rayfield:Notify({
                Title = "Bring All",
                Content = "Système désactivé",
                Duration = 2,
                Image = 4483345998
            })
        end
    end
})

 function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.Died:Connect(function()
        if BringState.Connection then
            BringState.Connection:Disconnect()
            BringState.Connection = nil
        end
        unfreezeCamera()
    end)
    if BringState.Active then
        local localRoot = character:WaitForChild("HumanoidRootPart")
        BringState.SavedPos = BringState.SavedPos or localRoot.Position
        BringState.SavedCamCFrame = BringState.SavedCamCFrame or Workspace.CurrentCamera.CFrame
        freezeCamera()
        BringState.Connection = RunService.Heartbeat:Connect(function()
            if BringState.Active then
                processNext()
                if BringState.SavedCamCFrame then
                    local cam = Workspace.CurrentCamera
                    cam.CameraType = Enum.CameraType.Scriptable
                    cam.CFrame = BringState.SavedCamCFrame
                    camBlocker.CFrame = BringState.SavedCamCFrame
                    camBlocker.Parent = Workspace
                end
            end
        end)
    end
end

LocalPlayer.CharacterAdded:Connect(onCharacterAdded)

Players.PlayerAdded:Connect(function()
    if BringState.Active then refreshQueue() end
end)

Players.PlayerRemoving:Connect(function(p)
    for i = #BringState.Queue, 1, -1 do
        if BringState.Queue[i] == p then
            table.remove(BringState.Queue, i)
        end
    end
    AuraState.GrabbedPlayers[p.UserId] = nil
end)

Workspace.ChildAdded:Connect(function(model)
    task.wait()
    
    if model.Name ~= "BlackHoleKick" then return end
    
    if AuraState.TimerActive then
        local finalTime = tick() - AuraState.TimerStart
        AuraState.TimerActive = false
        TimerLabel:Set(string.format("Timer arrêté: %.2fs", finalTime))
        
        Rayfield:Notify({
            Title = "BlackHole Détecté",
            Content = string.format("Temps total: %.2fs", finalTime),
            Duration = 5,
            Image = 4483345998
        })
    end
    
    local processed = false
    
    while not processed do
        if not model or not model.Parent then
            processed = true
            break
        end
        
        for _, player in pairs(Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local root = player.Character.HumanoidRootPart
                
                if root.Anchored then
                    processed = true
                    
                    local alreadyTracked = false
                    for _, id in ipairs(AuraState.BlackHoleTracking) do
                        if id == player.UserId then
                            alreadyTracked = true
                            break
                        end
                    end
                    
                    if not alreadyTracked then
                        table.insert(AuraState.BlackHoleTracking, player.UserId)
                        
                        local hole = model:FindFirstChild("Hole")
                        if hole then
                            local distance = (root.CFrame.Position - hole.CFrame.Position).Magnitude
                            local kickedByPlayer = distance > 2
                            
                            local kickType = kickedByPlayer and "par un joueur" or "automatiquement"
                            
                            Rayfield:Notify({
                                Title = "Aura BlackHole",
                                Content = player.DisplayName.." expulsé "..kickType,
                                Duration = 6.5,
                                Image = 4483345998
                            })
                        end
                        
                        task.delay(10, function()
                            for i, id in ipairs(AuraState.BlackHoleTracking) do
                                if id == player.UserId then
                                    table.remove(AuraState.BlackHoleTracking, i)
                                    break
                                end
                            end
                        end)
                    end
                end
            end
        end
        
        task.wait()
    end
end)

Tab:CreateSection("other kicks")


TargetPlayer = nil
SpamRepetitions = 30
 SpamSpeed = 0.05
 WhitelistFriends = true
 function sno(part)
    if not part or not part.Parent then return end
    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end
 function FindBlob()
    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if spawnedToys then
        for _, obj in ipairs(spawnedToys:GetChildren()) do
            if obj.Name == "CreatureBlobman" then
                return obj
            end
        end
    end
    return nil
end
 function IsFriend(player)
    if not WhitelistFriends then return false end
    return LocalPlayer:IsFriendsWith(player.UserId)
end
 function SpawnBlob()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    
 args = {
        "CreatureBlobman",
        char.HumanoidRootPart.CFrame,
        Vector3.new(0, 7.2, 0)
    }
    
    pcall(function()
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(unpack(args))
    end)
    
    task.wait(0.5)
    return FindBlob()
end
 function SitOnBlob(blob)
    if not blob then return false end
    
    local seat = blob:FindFirstChild("VehicleSeat")
    local char = LocalPlayer.Character
    local humanoid = char and char:FindFirstChild("Humanoid")
    
    if seat and humanoid then
        seat:Sit(humanoid)
        return true
    end
    return false
end

Tab:CreateButton({
    Name = "Spawn Blobman",
    Callback = function()
        local blob = SpawnBlob()
        if blob then
            Rayfield:Notify({
                Title = "Blobman Spawned",
                Content = "Blobman successfully spawned!",
                Duration = 3,
                Image = 4483345998
            })
        else
            Rayfield:Notify({
                Title = "Error",
                Content = "Failed to spawn Blobman!",
                Duration = 5,
                Image = 4483345998
            })
        end
    end
})

Tab:CreateButton({
    Name = "Sit on Blobman",
    Callback = function()
        local blob = FindBlob()
        if blob then
            local success = SitOnBlob(blob)
            if success then
                Rayfield:Notify({
                    Title = "Seated",
                    Content = "You are now sitting on the Blobman!",
                    Duration = 3,
                    Image = 4483345998
                })
            else
                Rayfield:Notify({
                    Title = " Error",
                    Content = "Failed to sit on Blobman!",
                    Duration = 5,
                    Image = 4483345998
                })
            end
        else
            Rayfield:Notify({
                Title = " No Blobman",
                Content = "Please spawn a Blobman first!",
                Duration = 5,
                Image = 4483345998
            })
        end
    end
})

Tab:CreateSlider({
    Name = "Spam Repetitions",
    Range = {10, 1000000},
    Increment = 10,
    Suffix = "times",
    CurrentValue = 30,
    Flag = "SpamRepetitions",
    Callback = function(Value)
        SpamRepetitions = Value
        Rayfield:Notify({
            Title = "Spam Updated",
            Content = "Repetitions set to: " .. Value,
            Duration = 3,
            Image = 4483345998
        })
    end
})

Tab:CreateSlider({
    Name = "Spam Speed",
    Range = {0, 1},
    Increment = 0.01,
    Suffix = "sec",
    CurrentValue = 0.05,
    Flag = "SpamSpeed",
    Callback = function(Value)
        SpamSpeed = Value
        Rayfield:Notify({
            Title = "⚡ Speed Updated",
            Content = "Delay set to: " .. Value .. "s",
            Duration = 3,
            Image = 4483345998
        })
    end
})

Tab:CreateInput({
    Name = "Target Player Name",
    CurrentValue = "",
    PlaceholderText = "Enter player name...",
    RemoveTextAfterFocusLost = false,
    Flag = "TargetPlayerName",
    Callback = function(Text)
        if Text == "" then
            TargetPlayer = nil
            Rayfield:Notify({
                Title = " Target Cleared",
                Content = "No player targeted",
                Duration = 3,
                Image = 4483345998
            })
            return
        end
        
        local found = false
        for _, v in pairs(Players:GetPlayers()) do
            if string.lower(v.Name):find(string.lower(Text)) or 
               string.lower(v.DisplayName):find(string.lower(Text)) then
                TargetPlayer = v
                found = true
                Rayfield:Notify({
                    Title = " Target Set",
                    Content = "Targeting: " .. v.Name,
                    Duration = 3,
                    Image = 4483345998
                })
                break
            end
        end
        
        if not found then
            Rayfield:Notify({
                Title = " Player Not Found",
                Content = "Could not find player: " .. Text,
                Duration = 5,
                Image = 4483345998
            })
            TargetPlayer = nil
        end
    end
})

Tab:CreateButton({
    Name = " Kick special TARGET PLAYER ",
    Callback = function()
        if not TargetPlayer then
            Rayfield:Notify({
                Title = " Error",
                Content = "No target player set! Enter a name first.",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        if not Players:FindFirstChild(TargetPlayer.Name) then
            Rayfield:Notify({
                Title = " Error",
                Content = "Target player left the game!",
                Duration = 5,
                Image = 4483345998
            })
            TargetPlayer = nil
            return
        end
        
        local blob = FindBlob()
        if not blob then
            Rayfield:Notify({
                Title = "Error",
                Content = "No Blobman found! Spawn one first.",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then
            Rayfield:Notify({
                Title = "Error",
                Content = "Character not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local targetChar = TargetPlayer.Character
        if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
            Rayfield:Notify({
                Title = " Error",
                Content = "Target character not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local RightDetector = blob:FindFirstChild("RightDetector")
        if not RightDetector then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman RightDetector not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local RightWeld = RightDetector:FindFirstChild("RightWeld")
        local BlobmanScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        
        if not (RightWeld and BlobmanScript) then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman components missing!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local CreatureGrab = BlobmanScript:FindFirstChild("CreatureGrab")
        local CreatureRelease = BlobmanScript:FindFirstChild("CreatureRelease")
        
        if not (CreatureGrab and CreatureRelease) then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman RemoteEvents not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        Rayfield:Notify({
            Title = " TARGETING: " .. TargetPlayer.Name,
            Content = "Starting destruction sequence...",
            Duration = 5,
            Image = 4483345998
        })
        
        local oldCF = char:GetPivot()
        local targetRoot = targetChar.HumanoidRootPart
        
        task.spawn(function()
            pcall(function()
                char:PivotTo(targetRoot.CFrame)
                task.wait(0.25)
                
                CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                task.wait(0.1)
                
                CreatureRelease:FireServer(RightWeld, targetRoot)
                
                task.defer(function()
                    local BodyPos = Instance.new("BodyPosition")
                    BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    BodyPos.Position = oldCF.Position + Vector3.new(
                        math.random(-15, 15),
                        math.random(-10, 10),
                        math.random(-15, 15)
                    )
                    BodyPos.Parent = targetRoot
                    BodyPos.P = 45000
                    BodyPos.D = 500
                end)
            end)
            
            task.wait(0.2)
            
            pcall(function()
                char:PivotTo(oldCF)
            end)
            task.wait(0.2)
            Rayfield:Notify({
                Title = "Phase 2: Chaos Loop",
                Content = "Spamming network events on " .. TargetPlayer.Name,
                Duration = 5,
                Image = 4483345998
            })
            
            for i = 1, SpamRepetitions do
                pcall(function()
                    sno(targetRoot)
                    ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(targetRoot)
                    CreatureGrab:FireServer(RightDetector, targetRoot, RightWeld)
                    CreatureRelease:FireServer(RightWeld, targetRoot)
                end)
                task.wait(SpamSpeed)
            end
            
            Rayfield:Notify({
                Title = "TARGET DESTROYED",
                Content = TargetPlayer.Name .. " should be experiencing severe lag!",
                Duration = 8,
                Image = 4483345998
            })
        end)
    end
})

Tab:CreateButton({
    Name = " DESTROY SERVER ",
    Callback = function()
        local blob = FindBlob()
        if not blob then
            Rayfield:Notify({
                Title = " Error",
                Content = "No Blobman found! Spawn one first.",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local char = LocalPlayer.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then
            Rayfield:Notify({
                Title = " Error",
                Content = "Character not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local RightDetector = blob:FindFirstChild("RightDetector")
        if not RightDetector then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman RightDetector not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local RightWeld = RightDetector:FindFirstChild("RightWeld")
        local BlobmanScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        
        if not (RightWeld and BlobmanScript) then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman components missing!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        local CreatureGrab = BlobmanScript:FindFirstChild("CreatureGrab")
        local CreatureRelease = BlobmanScript:FindFirstChild("CreatureRelease")
        
        if not (CreatureGrab and CreatureRelease) then
            Rayfield:Notify({
                Title = " Error",
                Content = "Blobman RemoteEvents not found!",
                Duration = 5,
                Image = 4483345998
            })
            return
        end
        
        Rayfield:Notify({
            Title = " DESTROY SERVER STARTED",
            Content = "Phase 1: Grabbing all players...",
            Duration = 5,
            Image = 4483345998
        })
        
        local oldCF = char:GetPivot()
        local grabbedPlayers = {}
        local targetCount = 0
        
        task.spawn(function()
            for _, v in pairs(Players:GetPlayers()) do
                if v ~= plr and not IsFriend(v) then
                    if v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                        targetCount = targetCount + 1
                        
                        pcall(function()
                            char:PivotTo(v.Character.HumanoidRootPart.CFrame)
                            task.wait(0.25)
                            
                            CreatureGrab:FireServer(RightDetector, v.Character.HumanoidRootPart, RightWeld)
                            CreatureRelease:FireServer(RightWeld, v.Character.HumanoidRootPart)
                            
                            task.defer(function()
                                local BodyPos = Instance.new("BodyPosition")
                                BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                BodyPos.Position = oldCF.Position + Vector3.new(
                                    math.random(-15, 15),
                                    math.random(-10, 10),
                                    math.random(-15, 15)
                                )
                                BodyPos.Parent = v.Character.HumanoidRootPart
                                BodyPos.P = 45000
                                BodyPos.D = 500
                            end)
                            
                            table.insert(grabbedPlayers, v.Character.HumanoidRootPart)
                        end)
                    end
                end
                task.wait(0.1)
            end
            
            Rayfield:Notify({
                Title = "Phase 1 Complete",
                Content = "Grabbed " .. #grabbedPlayers .. " players!",
                Duration = 3,
                Image = 4483345998
            })
            
            pcall(function()
                char:PivotTo(oldCF)
            end)
            task.wait(0.2)
            
            Rayfield:Notify({
                Title = "Phase 2: Final Chaos",
                Content = "Spamming network events...",
                Duration = 5,
                Image = 4483345998
            })
            
            for _, Root in pairs(grabbedPlayers) do
                pcall(function()
                    sno(Root)
                    ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Root)
                    CreatureGrab:FireServer(RightDetector, Root, RightWeld)
                end)
                task.wait(0.1)
            end
            
            Rayfield:Notify({
                Title = "DESTROY SERVER COMPLETE",
                Content = "Targeted " .. targetCount .. " players! Server should be unstable now.",
                Duration = 8,
                Image = 4483345998
            })
        end)
    end
})
Tab:CreateToggle({
    Name = "Whitelist Friends",
    CurrentValue = true,
    Flag = "WhitelistFriends",
    Callback = function(Value)
        WhitelistFriends = Value
        Rayfield:Notify({
            Title = "Friends Whitelist",
            Content = Value and "Friends will be protected" or "Friends will NOT be protected",
            Duration = 3,
            Image = 4483345998
        })
    end
})


local LoopTab = Window:CreateTab("Loops", 4483362458)
RemoteSetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
 RemoteDestroyGrabLine = GrabEvents:WaitForChild("DestroyGrabLine")
SpawnToyRF = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
SelectedPlayer = nil
 KillHB, KickHB = nil, nil
 LoopKickOn, LoopFlingOn, LoopBlobKickOn = false, false, false
 HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
 lastReturnCF = nil
function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants()) do
        if d:IsA("BasePart") then d.CanCollide = false end
    end
end
 function setNoCollideChar(char)
    for _, v in ipairs(char:GetDescendants()) do
        if v:IsA("BasePart") then v.CanCollide = false end
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
    SpawnToyRF:InvokeServer("CreatureBlobman", LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end
 function ensureBlobman()
    local b = findBlobman()
    if b then return b end
    spawnBlobman()
    for _ = 1, 30 do
        task.wait(0.1)
        b = findBlobman()
        if b then return b end
    end
    return nil
end
CameraAnchor = {}
CameraAnchor.__index = CameraAnchor
function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
function CameraAnchor:attach(cf)
    self:detach()
    local p = Instance.new("Part")
    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent =
        "CameraAnchor", Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
    self.part = p
    local cam = Workspace.CurrentCamera
    cam.CameraType = Enum.CameraType.Custom
    cam.CameraSubject = p
end
function CameraAnchor:detach()
    if self.part then self.part:Destroy() self.part = nil end
    local cam = Workspace.CurrentCamera
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end
 cameraAnchor = CameraAnchor.new()
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
    if not originalPos then return end
    
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
    if not (root and hum) or hum.Health <= 0 then return end
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
    if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
    local rag = hum:FindFirstChild("Ragdolled")
    if rag then rag.Value = false end

    local bv, bav = Instance.new("BodyVelocity"), Instance.new("BodyAngularVelocity")
    bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
    bv.P = 1e6
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
    bv.Parent, bav.Parent = root, root

    hum.BreakJointsOnDeath = false
    hum:ChangeState(Enum.HumanoidStateType.Dead)
    hum.RigType = Enum.HumanoidRigType.R15

    task.delay(2, function()
        if bv.Parent then bv:Destroy() end
        if bav.Parent then bav:Destroy() end
    end)
end
function performKill()
    if not SelectedPlayer then return end
    
    local target = Players:FindFirstChild(SelectedPlayer)
    local tChar = target and target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    local tHead = tChar and tChar:FindFirstChild("Head")
    
    if not (target and tRoot and tHum and tHead) then return end
    if isTooHigh(target) then return end
    if target:FindFirstChild("InPlot") and target.InPlot.Value then return end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not (char and hrp) then return end

    if not char:GetAttribute("SavingOriginalPos") then
        saveOriginalPosAttr()
    end
    char:SetAttribute("SavingOriginalPos", true)

    getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
    Workspace.FallenPartsDestroyHeight = 0/0

    local originalPos = getOriginalPosAttr()
    if originalPos then
        cameraAnchor:attach(originalPos)
    end

    local desiredCFrame = CFrame.new(tRoot.Position + TELEPORT_OFFSET)
    hrp:PivotTo(desiredCFrame)

    setNoCollideChar(tChar)
    RemoteSetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
    task.wait(0)
    RemoteDestroyGrabLine:FireServer(tRoot)
    task.wait(0)

    if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
        task.wait(0)
        modifyTarget(tRoot, tHum)
    end

    scheduleReturnHome()
end
 function StartLoopKill()
    if KillHB then KillHB:Disconnect() end
    KillHB = RunService.Heartbeat:Connect(performKill)
end
function StopLoopKill()
    if KillHB then KillHB:Disconnect() KillHB = nil end
    cameraAnchor:detach()
end
 function sendToSky(root, hum)
    DisableCollisions(hum.Parent)
    local BV = Instance.new("BodyVelocity")
    BV.Velocity = Vector3.new(0, 1e9, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 1e8
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true
    task.delay(3, function() if BV.Parent then BV:Destroy() end end)
end
function executeKick()
    if not SelectedPlayer then return end
    local p = Players:FindFirstChild(SelectedPlayer)
    local c = p and p.Character
    local root = c and c:FindFirstChild("HumanoidRootPart")
    local head = c and c:FindFirstChild("Head")
    local hum = c and c:FindFirstChild("Humanoid")
    if not (root and head and hum) or hum.Health <= 0 then return end
    if isTooHigh(p) then return end
    if p:FindFirstChild("InPlot") and p.InPlot.Value then return end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild("HumanoidRootPart")
    if not selfRoot then return end

    local saved = selfChar:GetPivot()
    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.05)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.05)
    local po = head:FindFirstChild("PartOwner")
    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end
function StartLoopKick()
    if KickHB then KickHB:Disconnect() end
    LoopKickOn = true
    KickHB = RunService.Heartbeat:Connect(function()
        if LoopKickOn then executeKick() end
    end)
end
function StopLoopKick()
    LoopKickOn = false
    if KickHB then KickHB:Disconnect() KickHB = nil end
end
 LoopFling = {
    on = false,
    targets = {},
    last = nil,
    tpSpeed = 0.2,
    magnetRange = 5,
    stay = 0.5,
    back = 0.5,
}
function tpTo(cf)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _ = 1, 5 do
        hrp.CFrame = hrp.CFrame:Lerp(cf, LoopFling.tpSpeed)
        task.wait(0.02)
    end
end
 function stick(root)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    if (hrp.Position - root.Position).Magnitude < LoopFling.magnetRange then
        hrp.CFrame = root.CFrame + Vector3.new(0, 0, -1)
        hrp.Velocity = Vector3.new(0, 0, 0)
    end
end
function execFling(tChar)
    local hum = tChar:FindFirstChildOfClass("Humanoid")
    local root = tChar:FindFirstChild("HumanoidRootPart")
    if not (hum and root) then return end
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
    bv.Velocity = Vector3.new(0, 1000, 0)
    bv.Parent = root
    local up = true
    while LoopFling.on do
        bv.Velocity = up and Vector3.new(0, 1000, 0) or Vector3.new(0, -1000, 0)
        up = not up
        task.wait(0.5)
    end
    bv:Destroy()
end
function startFlingFor(targetPlayer)
    if not targetPlayer then return end
    if not LoopFling.last then
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild("HumanoidRootPart")
        if hrp then LoopFling.last = hrp.CFrame end
    end
    while LoopFling.on do
        local tChar = targetPlayer.Character
        local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
        if tRoot then
            tpTo(tRoot.CFrame + Vector3.new(0, 0, -2))
            task.wait(0.05)
            stick(tRoot)
            execFling(tChar)
            task.wait(LoopFling.stay)
        end
        if LoopFling.last then
            tpTo(LoopFling.last)
            task.wait(LoopFling.back)
        end
        task.wait()
    end
end
function autoFlingLoop()
    while LoopFling.on do
        for _, name in ipairs(LoopFling.targets) do
            local tp = Players:FindFirstChild(name)
            if tp and tp.Character then
                task.spawn(function() startFlingFor(tp) end)
            end
        end
        task.wait(0.05)
    end
end
function StartLoopBounce(targetName)
    if not targetName or targetName == "" then return end
    LoopFling.on = true
    LoopFling.targets = { targetName }
    task.spawn(autoFlingLoop)
end
 function StopLoopBounce()
    LoopFling.on = false
end

 PlayerDropdown = LoopTab:CreateDropdown({
    Name = "Select Player",
    Options = {},
    CurrentOption = {},
    MultipleOptions = false,
    Flag = "PlayerDropdown",
    Callback = function(option)
        SelectedPlayer = option[1]:match("@([%w_]+)")
    end
})
function optionText(plr) return string.format(" @%s (%s)", plr.Name, plr.DisplayName or "") end
function refreshDropdown()
    local opts = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then table.insert(opts, optionText(p)) end
    end
    PlayerDropdown:Refresh(opts)
end
Players.PlayerAdded:Connect(function() task.wait(0.5) refreshDropdown() end)
Players.PlayerRemoving:Connect(function() task.wait(0.5) refreshDropdown() end)
refreshDropdown()

LoopTab:CreateButton({
    Name = 'Bring player',
    Callback = function()
        if not SelectedPlayer then
            return
        end

        local Players = game:GetService("Players")
        local ReplicatedStorage = game:GetService("ReplicatedStorage")

        local LocalPlayer = Players.LocalPlayer
        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local root = character:WaitForChild("HumanoidRootPart")
        local oldCFrame = root.CFrame

        local targetPlayer = Players:FindFirstChild(SelectedPlayer)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Head") then
            local targetHead = targetPlayer.Character.Head

            for i = 1, 2 do
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("Head") then break end
                root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)
                local args = { [1] = targetHead, [2] = root.CFrame }
                ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner"):FireServer(unpack(args))
                task.wait(0.15)
            end

            task.wait(0.1)
            root.CFrame = oldCFrame
            local front = oldCFrame.LookVector * 5
            targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

            local destroyArgs = { [1] = targetHead }
            ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine"):FireServer(unpack(destroyArgs))
        else
        end
    end
})

LoopTab:CreateToggle({
    Name = 'LoopKill',
    CurrentValue = false,
    Flag = "LoopKillToggle",
    Callback = function(v) if v then StartLoopKill() else StopLoopKill() end end
})
LoopTab:CreateToggle({
    Name = 'LoopKick setnetownership',
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
Players.LocalPlayer.CharacterAdded:Connect(function(char)
    initCharAttrs()
    local hum = char:WaitForChild("Humanoid", 5)
    if hum then hum.Died:Connect(function() cameraAnchor:detach() end) end
end)
if Players.LocalPlayer.Character then initCharAttrs() end

LoopTab:CreateToggle({
    Name = 'Loop Bounce',
    CurrentValue = false,
    Flag = "LoopFlingToggle",
    Callback = function(v)
        if v then StartLoopBounce(SelectedPlayer) else StopLoopBounce() end
    end
})

function findMountedBlob()
    local char = Players.LocalPlayer.Character
    local hum = char and char:FindFirstChild("Humanoid")
    return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == "CreatureBlobman") and hum.SeatPart.Parent or nil
end

function getPlayerList()
    local list = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= Players.LocalPlayer then
            table.insert(list, plr.Name)
        end
    end
    return list
end

 function GrabAndKill(blob, targetChar, side)
    local detector = blob:FindFirstChild(side .. "Detector")
    local weld = detector and detector:FindFirstChild(side .. "Weld")
    if not (detector and weld and targetChar) then return end
    if targetChar == blob.PrimaryPart then return end

    local humanoid = targetChar:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.Sit = false
        humanoid:ChangeState(Enum.HumanoidStateType.Dead)
        humanoid.BreakJointsOnDeath = true
        humanoid.Health = 0
    end

    for _, part in ipairs(targetChar:GetChildren()) do
        if part:IsA("BasePart") then
            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, part)
            part.AssemblyLinearVelocity = Vector3.new(0, -999, 0)
            task.spawn(function()
                local up = true
                while part and part.Parent do
                    part.AssemblyLinearVelocity = Vector3.new(0, up and 150 or -150, 0)
                    up = not up
                    task.wait(0.05)
                end
            end)
        end
    end
end

LoopTab:CreateButton({
    Name = "Perm Owner All and kill",
    Callback = function()
        local blob = findMountedBlob()
        if not blob then
            return
        end
        
        local players = getPlayerList()
        local seatScript = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        local grab = seatScript and seatScript:FindFirstChild("CreatureGrab")
        local release = seatScript and seatScript:FindFirstChild("CreatureRelease")
        local blobHrp = blob.PrimaryPart or blob:FindFirstChild("HumanoidRootPart")
        
        if not (grab and release and blobHrp) then
            return
        end
        
 function inRange(targetHrp, radius)
            return (blobHrp.Position - targetHrp.Position).Magnitude <= radius
        end
        
        local speed = 175
        local function moveBlobTowards(targetHrp, dt)
            local direction = targetHrp.Position - blobHrp.Position
            local distance = direction.Magnitude
            if distance <= speed * dt then
                blobHrp.CFrame = targetHrp.CFrame
                return true
            else
                local moveStep = direction.Unit * speed * dt
                blobHrp.CFrame = blobHrp.CFrame + moveStep
                return false
            end
        end
        
 function waitTime(seconds)
            local t0 = tick()
            repeat
                RunService.Heartbeat:Wait()
            until tick() - t0 >= seconds
        end
        
        for _, playerName in ipairs(players) do
            local player = Players:FindFirstChild(playerName)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local targetChar = player.Character
                local targetHrp = targetChar.HumanoidRootPart
                local reached = false
                local startTime = tick()
                
                repeat
                    local dt = 1 / 60
                    reached = moveBlobTowards(targetHrp, dt) or inRange(targetHrp, 30)
                until reached or tick() - startTime > 1.5
                
                if inRange(targetHrp, 30) then
                    waitTime(0.05)
                    
                    grab:FireServer(blob.RightDetector, targetHrp, blob.RightDetector.RightWeld)
                    waitTime(0.05)
                    GrabAndKill(blob, targetChar, "Right")
                    
                    waitTime(0.05)
                end
            end
        end
    end
})

local FunTab = Window:CreateTab("Fun ass", 4483362458)
ButtonSection = FunTab:CreateSection("Actions")

Paragraph = FunTab:CreateParagraph({Title = "Read me", Content = "This section ends to the unanchor train and starts here it's to control clones how does that works like exemple spawn a random toys of clones and clones nothings that are yours ! you're gonna click on grab all so it setown them and u can controls em the textbox is to troll players ! Ty to read."})

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
ReplicatedStorage = game:GetService("ReplicatedStorage")
PFS = game:GetService("PathfindingService")
LocalPlayer = Players.LocalPlayer

function findRandomSpawnedInToysFolder()
    for _, folder in ipairs(Workspace:GetChildren()) do
        if folder.Name:match("SpawnedInToys") and folder.Name ~= LocalPlayer.Name.."SpawnedInToys" then
            for _, child in ipairs(folder:GetChildren()) do
                if child:IsA("Model") and child.Name == "YouDecoy" then
                    return folder
                end
            end
        end
    end
    return nil
end

decoyFolder = findRandomSpawnedInToysFolder() or Workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys")
YourFolder = Workspace:WaitForChild(LocalPlayer.Name.."SpawnedInToys")

targetPlayer = nil
following = false
currentSpeed = 30
spinningDecoys = {}
loopJumpingDecoys = {}
wandering = false
orbiting = false
orbitTarget = nil
orbitAngleMap = {}
orbitRadius = 10
orbitSpeed = 90
flashStepping = false
flashStepTarget = nil
flashStepTime = 0
flashStepSpeed = 0.02
attacking = false
attackTarget = nil
wsesGrabEnabled = false

WsesCmds = {}
WsesCmds.fly = false
WsesCmds.flyspeed = 5
WsesCmds.spin = false
WsesCmds.spinspeed = 60
WsesCmds.speed = 16
WsesCmds.jumppower = 24
WsesCmds.target = nil
WsesCmds.oldfollow = false
WsesCmds.follow = false
WsesCmds.eyes = false
WsesCmds.eyes2 = false
WsesCmds.watersafe = false
WsesCmds.pathfollow = false
WsesCmds.pathfollowSpeed = 30

followCleanups = {}

Grab = ReplicatedStorage.GrabEvents.SetNetworkOwner
UnGrab = ReplicatedStorage.GrabEvents.DestroyGrabLine



function isValidYouDecoy(m)
   return m:IsA("Model") and m.Name == "YouDecoy" and m:FindFirstChildOfClass("Humanoid") and m:FindFirstChild("HumanoidRootPart")
end

function getTargetPlayer(name)
   return Players:FindFirstChild(name)
end

function followPlayer(botModel, targetPlayer, speed)
    local alive = true
    local RS = game:GetService("RunService")
    local PFS = game:GetService("PathfindingService")

    local bot = botModel.PrimaryPart or botModel:FindFirstChild("HumanoidRootPart")
    local humanoid = botModel:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.PlatformStand = false 
    end

currentPath = {}
currentWaypoint = 1
 useFallback = false

 function getTargetHRP()
        return targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart")
    end

function computePath(targetHRP)
        local path = PFS:CreatePath({
            AgentRadius = 2,
            AgentHeight = 5,
            AgentCanJump = true,
            AgentJumpHeight = 10,
            AgentCanClimb = true,
        })
        path:ComputeAsync(bot.Position, targetHRP.Position)

        if path.Status == Enum.PathStatus.Success then
            currentPath = path:GetWaypoints()
            currentWaypoint = 1
            useFallback = false
        else
            currentPath = {}
            useFallback = true
        end
    end

 computeLoop = task.spawn(function()
        while alive and bot and targetPlayer do
            local targetHRP = getTargetHRP()
            if targetHRP then
                computePath(targetHRP)
            else
                useFallback = true
                currentPath = {}
            end
            task.wait(1)
        end
    end)

    local heartbeatConn = RS.Heartbeat:Connect(function()
        if not alive or not bot or not targetPlayer then return end
        local targetHRP = getTargetHRP()
        if not targetHRP then
            bot.Velocity = Vector3.zero
            return
        end

        if useFallback then
            local dir = (targetHRP.Position - bot.Position)
            if dir.Magnitude > 1 then
                bot.Velocity = dir.Unit * (speed or 16)
            else
                bot.Velocity = Vector3.zero
            end
            return
        end

        local wp = currentPath[currentWaypoint]
        if not wp then return end

        local goalPos = wp.Position
        local dir = goalPos - bot.Position
        if dir.Magnitude < 2 then
            currentWaypoint = 1
            if currentWaypoint > #currentPath then
                bot.Velocity = Vector3.zero
                return
            end
        else
            bot.Velocity = dir.Unit * (speed or 16)
        end
    end)

    return function()
        alive = false
        heartbeatConn:Disconnect()
        task.cancel(computeLoop)
    end
end

TargetInput = nil

TargetInput = FunTab:CreateInput({
   Name = "Target Name",
   PlaceholderText = "Enter player name",
   RemoveTextAfterFocusLost = false,
   Flag = "TargetInput",
   Callback = function(text)
      local plr = getTargetPlayer(text)
      if plr then
         targetPlayer = plr
         orbitTarget = plr
         flashStepTarget = plr
         attackTarget = plr
         WsesCmds.target = plr
         Rayfield:Notify({Title="Target",Content="Target: "..text,Duration=2})
      else
         Rayfield:Notify({Title="Error",Content="Player not found",Duration=2})
      end
   end
})

followMode = true

FunTab:CreateButton({
    Name = "Decoy Follow(follows you)",
    Callback = function()
        local decoys = {}
        for _, descendant in pairs(workspace:GetDescendants()) do
            if descendant:IsA("Model") and descendant.Name == "YouDecoy" then
                table.insert(decoys, descendant)
            end
        end
        local numDecoys = #decoys
        local midPoint = math.ceil(numDecoys / 2)

        local function updateDecoyPositions()
            for index, decoy in pairs(decoys) do
                local torso = decoy:FindFirstChild("Torso")
                if torso then
                    local bodyPosition = torso:FindFirstChild("BodyPosition")
                    local bodyGyro = torso:FindFirstChild("BodyGyro")
                    if bodyPosition and bodyGyro then
                        local targetPosition
                        if followMode then
                            if playerCharacter and playerCharacter:FindFirstChild("HumanoidRootPart") then
                                targetPosition = playerCharacter.HumanoidRootPart.Position
                                local offset = (index - midPoint) * decoyOffset
                                local forward = playerCharacter.HumanoidRootPart.CFrame.LookVector
                                local right = playerCharacter.HumanoidRootPart.CFrame.RightVector
                                targetPosition = targetPosition - forward * decoyOffset + right * offset
                            end
                        else
                            local nearestPlayer = getNearestPlayer()
                            if nearestPlayer and nearestPlayer.Character and nearestPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                local angle = math.rad((index - 1) * (360 / numDecoys))
                                targetPosition = nearestPlayer.Character.HumanoidRootPart.Position + Vector3.new(math.cos(angle) * circleRadius, 0, math.sin(angle) * circleRadius)
                                bodyGyro.CFrame = CFrame.new(torso.Position, nearestPlayer.Character.HumanoidRootPart.Position)
                            end
                        end

                        if targetPosition then
                            local distance = (targetPosition - torso.Position).Magnitude
                            if distance > stopDistance then
                                bodyPosition.Position = targetPosition
                                if followMode then
                                    bodyGyro.CFrame = CFrame.new(torso.Position, targetPosition)
                                end
                            else
                                bodyPosition.Position = torso.Position
                                bodyGyro.CFrame = torso.CFrame
                            end
                        end
                    end
                end
            end
        end

        local function setupDecoy(decoy)
            local torso = decoy:FindFirstChild("Torso")
            if torso then
                local bodyPosition = Instance.new("BodyPosition")
                local bodyGyro = Instance.new("BodyGyro")
                bodyPosition.Parent = torso
                bodyGyro.Parent = torso
                bodyPosition.MaxForce = Vector3.new(40000, 40000, 40000)
                bodyPosition.D = 100
                bodyPosition.P = 100
                bodyGyro.MaxTorque = Vector3.new(40000, 40000, 40000)
                bodyGyro.D = 100
                bodyGyro.P = 20000
                local connection = RunService.Heartbeat:Connect(function()
                    updateDecoyPositions()
                end)
                table.insert(connections, connection)
                SetNetworkOwner:FireServer(torso, playerCharacter.Head.CFrame)
            end
        end

        for _, decoy in pairs(decoys) do
            setupDecoy(decoy)
        end
        Rayfield:Notify({Title = "Notification",Content = "Got "..numDecoys.." units. Manually click each unit if they don't move", Duration = 6.5,Image = 4483362458,})
    end
})

FunTab:CreateButton({
    Name = "Toggle Mode",

    Callback = function()
        followMode = not followMode
    end,
 })

ToggleSection = FunTab:CreateSection("Toggles")


FunTab:CreateToggle({
    Name = "Fly",
    CurrentValue = false,
    Flag = "WsesFly",
    Callback = function(v)
        WsesCmds.fly = v
    end,
})


FunTab:CreateToggle({
    Name = "Spin",
    CurrentValue = false,
    Flag = "WsesSpin",
    Callback = function(v)
        WsesCmds.spin = v
    end,
})

FunTab:CreateToggle({
    Name = "Pathfinding player",
    CurrentValue = false,
    Flag = "PathFollow",
    Callback = function(v)
        WsesCmds.pathfollow = v
        
        if v then
            if not WsesCmds.target then
                Rayfield:Notify({Title="Error",Content="Select target first!",Duration=2})
                WsesCmds.pathfollow = false
                return
            end
            
            for _, cleanup in pairs(followCleanups) do
                cleanup()
            end
            followCleanups = {}
            
            for _, d in ipairs(decoyFolder:GetChildren()) do
                if isValidYouDecoy(d) then
                    local cleanup = followPlayer(d, WsesCmds.target, WsesCmds.pathfollowSpeed)
                    table.insert(followCleanups, cleanup)
                end
            end
            
            Rayfield:Notify({Title="Pathfinding",Content="Following "..WsesCmds.target.Name,Duration=2})
        else
            for _, cleanup in pairs(followCleanups) do
                cleanup()
            end
            followCleanups = {}
        end
    end,
})


FunTab:CreateToggle({
    Name = "RandomShit",
    CurrentValue = false,
    Flag = "EyesTarget",
    Callback = function(v)
        WsesCmds.eyes2 = v
        if v and not WsesCmds.target then
            Rayfield:Notify({Title="Error",Content="Select target first!",Duration=2})
            WsesCmds.eyes2 = false
        else
            Rayfield:Notify({Title="👁️",Content=v and "Looking at "..WsesCmds.target.Name or "Stopped looking",Duration=2})
        end
    end,
})

FunTab:CreateToggle({
    Name = "Walk on water",
    CurrentValue = false,
    Flag = "WaterSafe",
    Callback = function(v)
        WsesCmds.watersafe = v
        if Workspace:FindFirstChild("Map") and Workspace.Map:FindFirstChild("AlwaysHereTweenedObjects") then
            local ocean = Workspace.Map.AlwaysHereTweenedObjects:FindFirstChild("Ocean")
            if ocean and ocean:FindFirstChild("Object") and ocean.Object:FindFirstChild("ObjectModel") then
                local oceanModel = ocean.Object.ObjectModel
                for _, part in ipairs(oceanModel:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = v
                        part.CanQuery = v
                    end
                end
                Rayfield:Notify({Title="WaterSafe",Content=v and "Walking on water!" or "Normal water",Duration=2})
            end
        end
    end,
})


FunTab:CreateToggle({
   Name = "Loop Jump",
   CurrentValue = false,
   Flag = "LoopJumpToggle",
   Callback = function(v)
      if v then
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then loopJumpingDecoys[d] = true end
         end
      else
         loopJumpingDecoys = {}
      end
   end
})

FunTab:CreateToggle({
   Name = "Wander",
   CurrentValue = false,
   Flag = "WanderToggle",
   Callback = function(v) wandering = v end
})

FunTab:CreateToggle({
   Name = "Orbit Target",
   CurrentValue = false,
   Flag = "OrbitToggle",
   Callback = function(v)
      orbiting = v
      if v and not orbitTarget then
         TargetInput:Set("Enter name first")
         orbiting = false
      else
         orbitAngleMap = {}
      end
   end
})

FunTab:CreateToggle({
   Name = "Flash Steps",
   CurrentValue = false,
   Flag = "FlashStepsToggle",
   Callback = function(v)
      flashStepping = v
      if v and not flashStepTarget then
         TargetInput:Set("Enter name first")
         flashStepping = false
      else
         flashStepTime = 0
      end
   end
})

FunTab:CreateToggle({
   Name = "Attack Target",
   CurrentValue = false,
   Flag = "AttackToggle",
   Callback = function(v)
      if v and attackTarget then
         startAttackLoop(attackTarget)
      else
         stopAttackLoop()
      end
   end
})

FunTab:CreateToggle({
   Name = "Grab all the np to netown(need to use that before)",
   CurrentValue = false,
   Flag = "WsesGrabToggle",
   Callback = function(v)
      wsesGrabEnabled = v
      if v then
         task.spawn(function()
            char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            h = char:WaitForChild("HumanoidRootPart")
            oldPos = h.Position

            for _, d in ipairs(YourFolder:GetChildren()) do
               if d.Name == "YouDecoy" and d:FindFirstChild("HumanoidRootPart") then
                  hrp = d.HumanoidRootPart
                  hum = d:FindFirstChildOfClass("Humanoid")
                  while hum and hum.Sit ~= true do
                     h.CFrame = CFrame.new(hrp.Position + Vector3.new(0,8,0))
                     Grab:FireServer(hrp, hrp.CFrame)
                     task.wait(0.1)
                     UnGrab:FireServer(hrp)
                  end
               end
            end

            h.CFrame = CFrame.new(oldPos)
         end)
      end
   end
})

SliderSection = FunTab:CreateSection("Sliders")

FunTab:CreateSlider({
    Name = "Spin Speed",
    Range = {-1000, 1000},
    Increment = 5,
    Suffix = " (30 Optimal)",
    CurrentValue = 30,
    Flag = "WsesSpinSpeed",
    Callback = function(v)
        WsesCmds.spinspeed = v
    end,
})

FunTab:CreateSlider({
    Name = "Walk Speed",
    Range = {0, 1000},
    Increment = 1,
    Suffix = " (16 Optimal)",
    CurrentValue = 16,
    Flag = "WalkSpeed",
    Callback = function(v)
        WsesCmds.speed = v
    end,
})

FunTab:CreateSlider({
    Name = "Jump Power",
    Range = {0, 1000},
    Increment = 1,
    Suffix = " (24 Optimal)",
    CurrentValue = 24,
    Flag = "JumpPower",
    Callback = function(v)
        WsesCmds.jumppower = v
    end,
})

FunTab:CreateSlider({
    Name = "Pathfinding shit",
    Range = {1, 1000},
    Increment = 1,
    Suffix = " studs/s",
    CurrentValue = 30,
    Flag = "PathSpeed",
    Callback = function(v)
        WsesCmds.pathfollowSpeed = v
    end,
})

FunTab:CreateSlider({
    Name = "Fly Speed",
    Range = {-1000, 1000},
    Increment = 5,
    Suffix = " (5 Optimal)",
    CurrentValue = 5,
    Flag = "FlySpeed",
    Callback = function(v)
        WsesCmds.flyspeed = v
    end,
})

FunTab:CreateSlider({
   Name = "Orbit Radius",
   Range = {5, 500},
   Increment = 1,
   Suffix = " studs",
   CurrentValue = orbitRadius,
   Flag = "OrbitRadius",
   Callback = function(v) orbitRadius = v end
})

FunTab:CreateSlider({
   Name = "Orbit Speed",
   Range = {10, 360},
   Increment = 10,
   Suffix = " deg/s",
   CurrentValue = orbitSpeed,
   Flag = "OrbitSpeed",
   Callback = function(v) orbitSpeed = v end
})

FunTab:CreateSlider({
   Name = "Flash Step Speed",
   Range = {0.001, 1},
   Increment = 0.01,
   Suffix = "s",
   CurrentValue = flashStepSpeed,
   Flag = "FlashStepSpeed",
   Callback = function(v) flashStepSpeed = v end
})

ButtonSection = FunTab:CreateSection("Actions")

FunTab:CreateButton({
   Name = "Sit All",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Sit = true end
      end
   end
})

FunTab:CreateButton({
   Name = "Stand All",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Sit = false end
      end
   end
})

FunTab:CreateButton({
   Name = "Line Up",
   Callback = function()
      char = LocalPlayer.Character
      if char and char:FindFirstChild("HumanoidRootPart") then
         basePos = char.HumanoidRootPart.Position + char.HumanoidRootPart.CFrame.LookVector * 5
         right = char.HumanoidRootPart.CFrame.RightVector
         index = 0
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
               offset = right * ((index - 0.5) * 3)
               pos = basePos + offset
               hrp = d.HumanoidRootPart
               if hrp then
                  hrp.CFrame = CFrame.new(pos, pos + char.HumanoidRootPart.CFrame.LookVector)
               end
               index = 1
            end
         end
      end
   end
})

FunTab:CreateButton({
   Name = "Jump",
   Callback = function()
      for _, d in ipairs(decoyFolder:GetChildren()) do
         if isValidYouDecoy(d) then
            d:FindFirstChildOfClass("Humanoid").Jump = true
         end
      end
   end,
})

FunTab:CreateButton({
    Name = "PlatformStand",
    CurrentValue = false,
    Flag = "WsesPlatform",
    Callback = function(v)
        for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
                d:FindFirstChildOfClass("Humanoid").PlatformStand = v
            end
        end
    end,
})


task.spawn(function()
   while true do
      if following and targetPlayer then
         pos = targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") and targetPlayer.Character.HumanoidRootPart.Position
         if pos then
            for _, d in ipairs(decoyFolder:GetChildren()) do
               if isValidYouDecoy(d) then
                  hum = d:FindFirstChildOfClass("Humanoid")
                  hrp = d.HumanoidRootPart
                  hum.WalkSpeed = currentSpeed
                  if (hrp.Position - pos).Magnitude > 5 then hum:MoveTo(pos) end
               end
            end
         end
      end
      task.wait(0.5)
   end
end)

RunService.Heartbeat:Connect(function(dt)
   for d, s in pairs(spinningDecoys) do
      if isValidYouDecoy(d) then
         d.HumanoidRootPart.CFrame = d.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(s)*dt, 0)
      else
         spinningDecoys[d] = nil
      end
   end
end)

task.spawn(function()
   while true do
      for d in pairs(loopJumpingDecoys) do
         if isValidYouDecoy(d) then d:FindFirstChildOfClass("Humanoid").Jump = true
         else loopJumpingDecoys[d] = nil end
      end
      task.wait(1.5)
   end
end)

task.spawn(function()
   while true do
      if wandering then
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) then
               hum = d:FindFirstChildOfClass("Humanoid")
               hrp = d.HumanoidRootPart
               offset = Vector3.new(math.random(-10,10),0,math.random(-10,10))
               hum:MoveTo(hrp.Position + offset)
            end
         end
      end
      task.wait(2)
   end
end)

RunService.Heartbeat:Connect(function(dt)
   if orbiting and orbitTarget and orbitTarget.Character and orbitTarget.Character:FindFirstChild("HumanoidRootPart") then
      hrp = orbitTarget.Character.HumanoidRootPart
      decoys = {}
      for _, d in ipairs(decoyFolder:GetChildren()) do if isValidYouDecoy(d) then table.insert(decoys, d) end end
      for i, d in ipairs(decoys) do
         angle = orbitAngleMap[d] or (360/#decoys)*i
         angle = (angle + orbitSpeed*dt)%360
         orbitAngleMap[d] = angle
         rad = math.rad(angle)
         offset = Vector3.new(math.cos(rad)*orbitRadius, 0, math.sin(rad)*orbitRadius)
         pos = hrp.Position + offset
         if d.HumanoidRootPart then d.HumanoidRootPart.CFrame = CFrame.new(pos, hrp.Position) end
      end
   end
end)

task.spawn(function()
   while true do
      if flashStepping and flashStepTarget and flashStepTarget.Character and flashStepTarget.Character:FindFirstChild("HumanoidRootPart") then
         hrp = flashStepTarget.Character.HumanoidRootPart
         flashStepTime = flashStepTime + 1
         base = hrp.Position
         back = -hrp.CFrame.LookVector
         right = hrp.CFrame.RightVector
         look = hrp.CFrame.LookVector
         decoys = {}
         for _, d in ipairs(decoyFolder:GetChildren()) do if isValidYouDecoy(d) then table.insert(decoys, d) end end
         for i, d in ipairs(decoys) do
            if d.HumanoidRootPart then
               phase = (#decoys-i)*math.pi/4
               swing = math.sin(flashStepTime*0.3 + phase)*5
               spread = (i - #decoys/2)*4
               side = swing + spread
               backOffset = 1.5 + i*0.15
               pos = base + back*backOffset + right*side
               d.HumanoidRootPart.CFrame = CFrame.new(pos, pos + look)
            end
         end
      end
      task.wait(flashStepSpeed)
   end
end)

RunService.Heartbeat:Connect(function(dt)
    for _, d in ipairs(decoyFolder:GetChildren()) do
        if isValidYouDecoy(d) then
            local hrp = d.HumanoidRootPart
            local humanoid = d:FindFirstChildOfClass("Humanoid")
            
            if humanoid then
                humanoid.JumpPower = WsesCmds.jumppower
                humanoid.WalkSpeed = WsesCmds.speed
                
                if WsesCmds.eyes2 and WsesCmds.target and WsesCmds.target.Character then
                    local targetHRP = WsesCmds.target.Character:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local selfCF = hrp.CFrame
                        local targetPos = Vector3.new(targetHRP.Position.X, hrp.Position.Y, targetHRP.Position.Z)
                        local direction = (targetPos - hrp.Position).Unit
                        local currentLook = Vector3.new(selfCF.LookVector.X, 0, selfCF.LookVector.Z).Unit
                        local angle = math.acos(math.clamp(currentLook:Dot(direction), -1, 1))
                        local cross = currentLook:Cross(direction)
                        local sign = cross.Y >= 0 and 1 or -1
                        local turnSpeed = angle * WsesCmds.spinspeed
                        hrp.AssemblyAngularVelocity = Vector3.new(0, sign * turnSpeed, 0)
                    end
                end
            end
            
            if WsesCmds.fly then
                hrp.AssemblyLinearVelocity = Vector3.new(0, WsesCmds.flyspeed, 0)
            end
            
            if WsesCmds.spin then
                hrp.AssemblyAngularVelocity = Vector3.new(0, WsesCmds.spinspeed, 0)
            end
        end
    end
end)

function startAttackLoop(t)
   attacking = true attackTarget = t
   task.spawn(function()
      while attacking and attackTarget and attackTarget.Character and attackTarget.Character:FindFirstChild("HumanoidRootPart") do
         thrp = attackTarget.Character.HumanoidRootPart
         for _, d in ipairs(decoyFolder:GetChildren()) do
            if isValidYouDecoy(d) and d.HumanoidRootPart then
               offset = Vector3.new(math.random(-3,3), math.random(2,5), math.random(-3,3))
               pos = thrp.Position + offset
               d.HumanoidRootPart.CFrame = CFrame.new(pos, thrp.Position)
            end
         end
         task.wait(0.05)
      end
   end)
end

function stopAttackLoop()
   attacking = false attackTarget = nil
end


ButtonSection = FunTab:CreateSection("unanchor train")

w = game:GetService("Workspace")
Players = game:GetService("Players")
rs = game:GetService("ReplicatedStorage")
RunService = game:GetService("RunService")
LocalPlayer = Players.LocalPlayer
Workspace = game:GetService("Workspace")

me = Players.LocalPlayer
BackPack = w[me.Name .. 'SpawnedInToys']

setowner = rs.GrabEvents.SetNetworkOwner
StickyPartEvent = rs.PlayerEvents.StickyPartEvent


FunTab:CreateButton({
    Name = "Train Unanchor trin(u gotta spawn 11 shurikens before and click on it for ts one)",
    Callback = function()
        train = w.Map.AlwaysHereTweenedObjects.Train.Object.ObjectModel.BallOctagon

        for i = 1,10 do
            shur = BackPack.NinjaShuriken
            shur.Name = i
            StickyPartEvent:FireServer(shur.StickyPart,train,CFrame.Angles(0,0,0))
        end

        for i = 1,100 do
            me.Character.HumanoidRootPart.CFrame = train.CFrame
            setowner:FireServer(train,train.CFrame)
            task.wait()
        end

        obj = w.Map.AlwaysHereTweenedObjects.Train.Object
        body = obj.ObjectModel.BallOctagon
        attach = body:FindFirstChild("ObjectModelAttachment")
        if attach then attach:Destroy() end

        obj.FollowThisPart.AlignPosition.Attachment0 = nil
        obj.FollowThisPart.AlignOrientation.Attachment0 = nil
    end
})

 Sense, Massless = 30, nil
FunTab:CreateToggle({
   Name = 'Massless Grab',
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
FunTab:CreateInput({
   Name = "Massless Sense to grab it",
   CurrentValue = tostring(Sense),
   PlaceholderText = "Enter sense value",
   RemoveTextAfterFocusLost = false,
   Flag = "MasslessSenseInput",
   Callback = function(Text)
       local v = tonumber(Text)
       if v and v > 0 then Sense = v end
   end,
})

CredsTab = Window:CreateTab("Info's'", "user")

Paragraph = CredsTab:CreateParagraph({Title = "Credits", Content = "Tester , Bug Finders , Creator, Friends, Helper , Idea's' thinkers,People's who gave me idea'"})

Paragraph = CredsTab:CreateParagraph({Title = "Testers", Content = "posuu_verified, 1k4q( on discord)"})

Paragraph = CredsTab:CreateParagraph({Title = "Bug Finders", Content = "lax380, 1k4q( on discord)"})

Paragraph = CredsTab:CreateParagraph({Title = "Friends", Content = "lax380,posuu_verified,yanispi360,nicoooo1547( on discord)"})

Paragraph = CredsTab:CreateParagraph({Title = "Helpers", Content = "lax380,yanispi360,nicoooo1547( on discord)"})

Paragraph = CredsTab:CreateParagraph({Title = "Idea's' thinkers", Content = "posuu_verified, 1k4q( on discord)"})

Paragraph = CredsTab:CreateParagraph({Title = "People's who gave me idea", Content = "__jeke,jibbefr,fusse2345610,helloa.,lax380,reqerewew,yanispi360,nicoooo154,blizzz_t,4art;Savior.liberty,oatssi,crysorune,vhck,xt._.tx and so much awesome people's !'( on discord)"})


CredsTab:CreateButton({
   Name = "Creators!",
   Callback = function()
      Rayfield:Notify({
         Title = "Creators",
         Content = "Nico and Gru (Wses tho he left after the beef🥀 )",
         Duration = 10.5,
         Image = 140311070669016
      })
   end,
})
