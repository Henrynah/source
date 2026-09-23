Players = game:GetService("Players")
LocalPlayer = Players.LocalPlayer
CoreGui = game:GetService("CoreGui")
loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
icons = "https://raw.githubusercontent.com/deividcomsono/lucide-roblox-direct/refs/heads/main/source.lua"
u = loadstring(game:HttpGet(('https://raw.githubusercontent.com/fiiremax/Scripts/refs/heads/master/Scriptnew/GitHub/Scripts/Module.lua')))()
Library = loadstring(game:HttpGet(repo .. "Library.lua"))()
ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
Options = Library.Options
Toggles = Library.Toggles

Players = game:GetService("Players")
player = Players.LocalPlayer
playerGui = player:WaitForChild("PlayerGui")
TweenService = game:GetService("TweenService")

function waitFor(parent, name, recursive, timeout)
    timeout = timeout or 10
    start = tick()
    result = nil
    while not result and tick() - start < timeout do
        if recursive then
            result = parent:FindFirstChild(name, true)
        else
            result = parent:FindFirstChild(name)
        end
        if not result then
            task.wait(0.1)
        end
    end
    return result
end

menuGui = waitFor(playerGui, "MenuGui", true, 10)
if not menuGui then
    warn("MenuGui not found in PlayerGui!")
    return
end

topRight = waitFor(menuGui, "TopRight", true, 10)
if not topRight then
    warn("TopRight not found!")
    return
end

coinsFrame = waitFor(topRight, "CoinsFrame", false, 10)
if not coinsFrame then
    warn("CoinsFrame not found!")
    return
end

coinsDisplay = waitFor(coinsFrame, "CoinsDisplay", false, 10)
if not coinsDisplay then
    warn("CoinsDisplay not found!")
    return
end

coinImage = coinsDisplay:FindFirstChild("CoinImage")
coinsText = coinsDisplay:FindFirstChild("Coins")

if coinImage then
    coinImage.Image = "rbxassetid://17629725719"
end

if coinsText then
    coinsText.TextColor3 = Color3.new(1, 1, 1)
end

coinsFrame.BackgroundTransparency = 0.35
coinsFrame.Position = coinsFrame.Position + UDim2.new(0, 0, 0, 15)

function StyleMenuUI()
    player = game:GetService("Players").LocalPlayer
    playerGui = player:WaitForChild("PlayerGui")
    menuGui = playerGui:WaitForChild("MenuGui")
    menu = menuGui:WaitForChild("Menu")

    black = Color3.fromRGB(0, 0, 0)
    blue = Color3.fromRGB(30, 100, 200)
    white = Color3.fromRGB(255, 255, 255)
    cornerRadius = UDim.new(0, 0)
    
    function roundCorners(obj)
        corner = obj:FindFirstChildOfClass("UICorner")
        if corner then
            corner.CornerRadius = cornerRadius
        else
            corner = Instance.new("UICorner")
            corner.CornerRadius = cornerRadius
            corner.Parent = obj
        end
    end
    
    function addPurpleBlueGradient(obj, rotation)
        gradient = obj:FindFirstChildOfClass("UIGradient")
        if not gradient then
            gradient = Instance.new("UIGradient")
        end

        gradient.Color = ColorSequence.new{
            ColorSequenceKeypoint.new(0, white),
            ColorSequenceKeypoint.new(1, blue)
        }
        gradient.Rotation = rotation or 45  
        gradient.Parent = obj
    end

    tabBarSizeY = 0.9999

    tabBar = menu:WaitForChild("TabBar")
    tabBar.BackgroundColor3 = black
    tabBar.BackgroundTransparency = 0.28
    tabBar.Size = UDim2.new(tabBar.Size.X.Scale, tabBar.Size.X.Offset, tabBarSizeY, tabBar.Size.Y.Offset)
    roundCorners(tabBar)

    tabContents = menu:WaitForChild("TabContents")
    for _, frame in ipairs(tabContents:GetChildren()) do
        if frame:IsA("Frame") then
            frame.BackgroundColor3 = black
            frame.BackgroundTransparency = 0.7
            roundCorners(frame)

            title = frame:FindFirstChild("Title")
            if title then
                title.BackgroundColor3 = black
                title.BackgroundTransparency = 0.4
                roundCorners(title)
                
                currentSize = title.Size
                title.Size = UDim2.new(currentSize.X.Scale * 0.975, currentSize.X.Offset, currentSize.Y.Scale, currentSize.Y.Offset)
            end

            sortingTabs = frame:FindFirstChild("SortingTabs")
            if sortingTabs then
                sortingTabs.BackgroundColor3 = black
                sortingTabs.BackgroundTransparency = 0.55
                roundCorners(sortingTabs)
            end

            contents = frame:FindFirstChild("Contents")
            if contents then
                contents.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
                contents.BackgroundTransparency = 0.55
                roundCorners(contents)
                addPurpleBlueGradient(contents, 45)

                contents.ChildAdded:Connect(function()
                    contents.BackgroundTransparency = 0
                    TweenService:Create(contents, TweenInfo.new(0.35, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                        BackgroundTransparency = 0.55
                    }):Play()
                end)
            end

            favoritesFrame = frame:FindFirstChild("FavoritesFrame")
            if favoritesFrame then
                favoritesFrame.BackgroundColor3 = black
                favoritesFrame.BackgroundTransparency = 1
                roundCorners(favoritesFrame)
            end

            if contents then
                for _, item in ipairs(contents:GetChildren()) do
                    fav = item:FindFirstChild("Favorite")
                    if fav and (fav:IsA("ImageLabel") or fav:IsA("ImageButton")) then
                        fav.ImageTransparency = 1
                        fav.BackgroundTransparency = 1
                    end
                end
            end
        end
    end

    for _, desc in ipairs(menuGui:GetDescendants()) do
        if desc.Name == "MeterFrame" and desc:IsA("GuiObject") then
            desc.BackgroundColor3 = black
            desc.BackgroundTransparency = 0.4
            roundCorners(desc)
        end
    end
end

StyleMenuUI()

Library.ForceCheckbox = false
Window = Library:CreateWindow({
	Title = "9rr",
	Footer = "Project 9rr | FTAP",
    Icon = 116176143011396,
	NotifySide = "Right",
	ShowCustomCursor = false,
    EnableCompacting = true, 
    SidebarCompacted = true,
    BackgroundImage = "rbxassetid://",
})

Tabs = {
    Main = Window:AddTab("Main", "house"),
    Player = Window:AddTab("Player", "person-standing"),
	Defense = Window:AddTab("Protections", "shield-plus"),
	Target = Window:AddTab("Loops", "sword"),
    Grab = Window:AddTab("Grabs", "hand-metal"),
    Visual = Window:AddTab("Visuals", "eye"),
    Server = Window:AddTab("Servers", "atom"),
    ToyTab = Window:AddTab("Toys", "shapes"),
    FigureTab = Window:AddTab("Figure", "mouse"),
    TpTab = Window:AddTab("Tp", "map-pin"),
    Keybinds = Window:AddTab("Keybind", "keyboard"),
    MapBreak = Window:AddTab("MapBreaks", "map"),
	UISettings = Window:AddTab("UI Settings", "cog")
}

do
    ProfileGroup = Tabs.Main:AddLeftGroupbox("Profile", "user")
    UtilityGroup = Tabs.Main:AddRightGroupbox("Utilities", "settings")
    AboutGroup = Tabs.Main:AddRightGroupbox("About", "info")

   do
    avatarview = ProfileGroup:AddViewport("Just your avatar", {
        Object = LocalPlayer.Character,
        Camera = Instance.new("Camera"),
        Interactive = true,
        AutoFocus = true,
        Height = 400,
    })

    LocalPlayer.CharacterAdded:Connect(function(chara)
        task.wait(1)
        avatarview:SetObject(chara:Clone())
    end)
end
    ProfileGroup:AddLabel("Good Evening, " .. LocalPlayer.DisplayName, true)

    UtilityGroup:AddButton({
        Text = "Rejoin",
        Callback = function()
            Library:Notify({
                Title = "LOLL",
                Description = "Rejoining...",
                Duration = 2
            })
            task.wait(0.5)
            game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
        end
    })

    UtilityGroup:AddButton({
        Text = "Server-Hop",
        Callback = function()
            Library:Notify({
                Title = "LOLL",
                Description = "Hopping to new server...",
                Duration = 2
            })
            task.wait(0.5)
            game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, tostring(math.random(1, 9999999)), LocalPlayer)
        end
    })


    AboutGroup:AddLabel("━━━━━━━━━━━━━━━━━━━━━━━━━━━━", true)
    AboutGroup:AddLabel("9rr · Project", true)
    AboutGroup:AddLabel("━━━━━━━━━━━━━━━━━━━━━━━━━━━━", true)
    AboutGroup:AddLabel("Developers", true)
    AboutGroup:AddLabel("  OCN_PXre - kshopnakub_2271", true)
    AboutGroup:AddLabel("  Ka1zen (Sir3n) - Rip_BotiTT", true)
    AboutGroup:AddLabel("━━━━━━━━━━━━━━━━━━━━━━━━━━━━", true)
    AboutGroup:AddLabel("Nothing", true)
    AboutGroup:AddLabel("This script is Goated", true)
    AboutGroup:AddLabel("━━━━━━━━━━━━━━━━━━━━━━━━━━━━", true)
end

do
    local GUtility = Tabs.Keybinds:AddLeftGroupbox('Mobile Controls', 'smartphone')
    local limbBox = Tabs.Keybinds:AddLeftGroupbox('Limb Control', 'scissors')
    local tpBox = Tabs.Keybinds:AddRightGroupbox('Teleport', 'map-pin')
    local MiscBox = Tabs.Keybinds:AddLeftGroupbox('Misc', 'settings')

    limbBox:AddLabel('Remove Left Leg', false):AddKeyPicker('RemoveLeftLeg', {
        Default = 'None',
        Text = 'Remove Left Leg',
        Mode = 'Press',
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent

                if target and target:FindFirstChild('Left Leg') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame

                        workspace.FallenPartsDestroyHeight = -100
                        target['Left Leg'].CFrame = CFrame.new(0, -1E3, 0)

                        task.wait(0.1)

                        target.Torso.CFrame = CFrame.new(0, -950, 0)

                        task.wait(0)

                        target.Torso.CFrame = pos
                    end
                end
            end
        end,
    })

    limbBox:AddLabel('Remove Right Leg', false):AddKeyPicker('RemoveRightLeg', {
        Default = 'None',
        Text = 'Remove Right Leg',
        Mode = 'Press',
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent

                if target and target:FindFirstChild('Right Leg') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame

                        workspace.FallenPartsDestroyHeight = -100
                        target['Right Leg'].CFrame = CFrame.new(0, -1E3, 0)

                        task.wait(0.1)

                        target.Torso.CFrame = CFrame.new(0, -950, 0)

                        task.wait(0)

                        target.Torso.CFrame = pos
                    end
                end
            end
        end,
    })

    limbBox:AddLabel('Remove Left Arm', false):AddKeyPicker('RemoveLeftArm', {
        Default = 'None',
        Text = 'Remove Left Arm',
        Mode = 'Press',
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent

                if target and target:FindFirstChild('Left Arm') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame

                        workspace.FallenPartsDestroyHeight = -100
                        target['Left Arm'].CFrame = CFrame.new(0, -1E3, 0)

                        task.wait(0.1)

                        target.Torso.CFrame = CFrame.new(0, -950, 0)

                        task.wait(0)

                        target.Torso.CFrame = pos
                    end
                end
            end
        end,
    })

    limbBox:AddLabel('Remove Right Arm', false):AddKeyPicker('RemoveRightArm', {
        Default = 'None',
        Text = 'Remove Right Arm',
        Mode = 'Press',
        Callback = function()
            if workspace:FindFirstChild('GrabParts') and workspace.GrabParts:FindFirstChild('GrabPart') then
                local target = workspace.GrabParts.GrabPart.WeldConstraint.Part1 and workspace.GrabParts.GrabPart.WeldConstraint.Part1.Parent

                if target and target:FindFirstChild('Right Arm') and target:FindFirstChild('Humanoid') and target.Humanoid:FindFirstChild('Ragdolled') then
                    if target.Humanoid.Ragdolled.Value then
                        local pos = target.Torso.CFrame

                        workspace.FallenPartsDestroyHeight = -100
                        target['Right Arm'].CFrame = CFrame.new(0, -1E3, 0)

                        task.wait(0.1)

                        target.Torso.CFrame = CFrame.new(0, -950, 0)

                        task.wait(0)

                        target.Torso.CFrame = pos
                    end
                end
            end
        end,
    })

local tpKey = Enum.KeyCode.X
tpBox:AddLabel("Teleport"):AddKeyPicker("TeleportKey", {
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

tpBox:AddCheckbox("TeleportToggle", {
    Text = "Teleport Binder",
    Default = false,
    Callback = function(Value)
        tpEnabled = Value
    end
})

    tpBox:AddLabel('TP to Spawn'):AddKeyPicker('TP_ToSpawn', {
        Default = 'None',
        Text = 'TP To Spawn',
        Mode = 'Press',
        Callback = function()
            local char = LocalPlayer.Character
            local humanoidRootPart = char and char:FindFirstChild('HumanoidRootPart')
            if humanoidRootPart then
                humanoidRootPart.CFrame = CFrame.new(0, -5, 0)
            end
        end,
    })

    tpBox:AddLabel('Loop TP Toggle'):AddKeyPicker('TP_LoopToggle', {
        Default = 'None',
        Text = 'Loop TP Toggle',
        Mode = 'Toggle',
        Callback = function(v)
            if Toggles.LoopTpToggle then
                Toggles.LoopTpToggle:SetValue(v)
            end
        end,
    })

    MiscBox:AddLabel('Break Barrier'):AddKeyPicker('BarrierKey', {
        Default = 'None',
        Text = 'Break Barrier',
        Mode = 'Press',
        Callback = function()
            checkPlotAndFarm()
        end,
    })

    MiscBox:AddLabel('Destroy Blob (Target)'):AddKeyPicker('DestroyBlobKey', {
        Default = 'None',
        Text = 'Destroy Target Blob',
        Mode = 'Toggle',
        Callback = function(v)
            if Toggles.AutoBlobmanToggle then
                Toggles.AutoBlobmanToggle:SetValue(v)
            end
        end,
    })

    GUtility:AddButton('MobileKeyboard', {
        Text = 'Mobile Keyboard',
        Func = function()
            if not _G.MobileKeyboardLoaded then
                _G.MobileKeyboardLoaded = true
                loadstring(game:HttpGet('https://raw.githubusercontent.com/Xxtan31/Ata/main/deltakeyboardcrack.txt', true))()
            end
        end,
    })
end

ServerPlayers = Tabs.Player:AddRightGroupbox("Server Players", "user-lock")
FunAnim = Tabs.Player:AddRightGroupbox("Animations", "venetian-mask")
MovementGroup = Tabs.Player:AddLeftGroupbox("Movement", "activity")

hkProfileId = nil
hkProfileInstance = nil

hkPlrNames = {}
for _,p in ipairs(Players:GetPlayers()) do
    table.insert(hkPlrNames, p.DisplayName .. " (@" .. p.Name .. ")")
end

function HK_ParseJsonString(HK_Str)
    if not HK_Str then return nil end
    HK_Success, HK_Data = pcall(function() return HttpService:JSONDecode(HK_Str) end)
    return HK_Success and HK_Data or nil
end

profileCardRef = ServerPlayers:AddLabel("Pick someone from the dropdown below")
local HK_CompilePlayerProfile

hkPlrDrop = ServerPlayers:AddDropdown("SelectPlayer", {
    Text = "Select Player",
    Values = hkPlrNames,
    Default = hkPlrNames[1] or "",
    Callback = function(name)
        username = name:match("@(.+)%)$") or name
        target = Players:FindFirstChild(username)
        if not target then return end
        hkProfileId = target.UserId
        if HK_CompilePlayerProfile then HK_CompilePlayerProfile(target) end
    end
})

function resetProfileCards()
    profileCardRef:Set("Pick someone from the dropdown below", "No player selected")
end

HK_CompilePlayerProfile = function(HK_SelectedPlr)
    hkProfileInstance = HK_SelectedPlr
    uid = HK_SelectedPlr.UserId
    thumb = Players:GetUserThumbnailAsync(uid, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)

    profileCardRef:Set(
        "@" .. HK_SelectedPlr.Name .. "\n\n" ..
        "Loading...   |   Loading...   |   Loading...\n" ..
        "Friends          Followers          Following\n\n" ..
        "About\nLoading...\n\n" ..
        "Joined  " .. tostring(HK_SelectedPlr.AccountAge) .. " days old",
        HK_SelectedPlr.DisplayName
    )
    pcall(function() profileCardRef:SetAvatar(thumb) end)

    task.spawn(function()
        displayName = HK_SelectedPlr.DisplayName
        username    = HK_SelectedPlr.Name
        accountAge  = tostring(HK_SelectedPlr.AccountAge)

        apiData = HK_ParseJsonString(tryGet("https://users.roblox.com/v1/users/" .. uid))
        bioRaw  = (apiData and apiData.description and apiData.description ~= "") and apiData.description or nil
        bio     = bioRaw and bioRaw:gsub("\n", " "):sub(1, 150) or "This goofy does NOT have a bio"

        frData = HK_ParseJsonString(tryGet("https://friends.roblox.com/v1/users/" .. uid .. "/friends/count"))
        flData = HK_ParseJsonString(tryGet("https://friends.roblox.com/v1/users/" .. uid .. "/followers/count"))
        fwData = HK_ParseJsonString(tryGet("https://friends.roblox.com/v1/users/" .. uid .. "/followings/count"))

        friends   = (frData and frData.count ~= nil) and tostring(frData.count) or "N/A"
        followers = (flData and flData.count ~= nil) and tostring(flData.count) or "N/A"
        following = (fwData and fwData.count ~= nil) and tostring(fwData.count) or "N/A"

        profileCardRef:Set(
            "@" .. username .. "\n\n" ..
            friends .. "   |   " .. followers .. "   |   " .. following .. "\n" ..
            "Friends          Followers          Following\n\n" ..
            "About\n" .. bio .. "\n\n" ..
            "Joined  " .. accountAge .. " days old",
            displayName
        )
        pcall(function() profileCardRef:SetAvatar(thumb) end)
    end)
end

function refreshDropdown()
    hkPlrNames = {}
    for _,p in ipairs(Players:GetPlayers()) do
        table.insert(hkPlrNames, p.DisplayName .. " (@" .. p.Name .. ")")
    end
    hkPlrDrop:SetValues(hkPlrNames)
end

Players.PlayerAdded:Connect(refreshDropdown)
Players.PlayerRemoving:Connect(function(leavingPlr)
    if hkProfileInstance and leavingPlr == hkProfileInstance then
        uid = tostring(leavingPlr.UserId)
        pcall(function() setclipboard(uid) end)
        Library:Notify({
            Title = "Coward left",
            Description = leavingPlr.DisplayName .. " (@" .. leavingPlr.Name .. ")  |  ID: " .. uid,
            Duration = 10
        })
        hkProfileInstance = nil
        hkProfileId = nil
        resetProfileCards()
    end
    refreshDropdown()
end)

ServerPlayers:AddDivider()

ServerPlayers:AddButton({
    Text = "Copy Profile Link",
    Callback = function()
        if hkProfileId then
            pcall(function() setclipboard("https://www.roblox.com/users/"..hkProfileId.."/profile") end)
            Library:Notify({Title = "Copied!", Description = "Profile link copied to clipboard.", Duration = 3})
        else
            Library:Notify({Title = "No player selected", Description = "Select a player first.", Duration = 3})
        end
    end
})


Movement = Movement or {
    Speed = {
        Enabled = false,
        Value = 16,
    },
    Fly = {
        Enabled = false,
        Speed = 120,
        Connection = nil,
    },
    InfiniteJump = false,
}
sprintEnabled = false
infiniteJump = false

local flyVelocity, flyGyro

function stopFly()
    if Movement.Fly.Connection then
        Movement.Fly.Connection:Disconnect()
        Movement.Fly.Connection = nil
    end
    if flyVelocity then
        flyVelocity:Destroy()
    end
    if flyGyro then
        flyGyro:Destroy()
    end
    flyVelocity, flyGyro = nil, nil
    char = LocalPlayer.Character
    if not char then
        return
    end
    hum = char:FindFirstChildOfClass('Humanoid')
    root = char:FindFirstChild('HumanoidRootPart')
    if hum then
        hum.PlatformStand = false
        hum:ChangeState(Enum.HumanoidStateType.GettingUp)
    end
    if root then
        root.AssemblyLinearVelocity = Vector3.zero
        root.AssemblyAngularVelocity = Vector3.zero
    end
end

function startFly()
    stopFly()
    if not Movement.Fly.Enabled then
        return
    end
    Movement.Fly.Connection = game:GetService("RunService").RenderStepped:Connect(function()
        char = LocalPlayer.Character
        if not char then
            return
        end
        hum = char:FindFirstChildOfClass('Humanoid')
        root = char:FindFirstChild('HumanoidRootPart')
        if not hum or not root then
            return
        end
        controlPart = (hum.Sit and hum.SeatPart and hum.SeatPart.AssemblyRootPart) or root
        if not flyVelocity or flyVelocity.Parent ~= controlPart then
            if flyVelocity then
                flyVelocity:Destroy()
            end
            flyVelocity = Instance.new('BodyVelocity')
            flyVelocity.MaxForce = Vector3.new(1e9, 1e9, 1e9)
            flyVelocity.Parent = controlPart
        end
        if not flyGyro or flyGyro.Parent ~= controlPart then
            if flyGyro then
                flyGyro:Destroy()
            end
            flyGyro = Instance.new('BodyGyro')
            flyGyro.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
            flyGyro.D = 100
            flyGyro.Parent = controlPart
        end
        move = Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            move = move + Vector3.new(0, 0, -1)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            move = move + Vector3.new(0, 0, 1)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            move = move + Vector3.new(-1, 0, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            move = move + Vector3.new(1, 0, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            move = move + Vector3.new(0, 1, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            move = move + Vector3.new(0, -1, 0)
        end
        camCF = workspace.CurrentCamera.CFrame
        speed = Movement.Fly.Speed or 120
        if move.Magnitude > 0 then
            flyVelocity.Velocity = (camCF.LookVector * -move.Z + camCF.RightVector * move.X + Vector3.new(0, move.Y, 0)) * speed
        else
            flyVelocity.Velocity = Vector3.zero
        end
        flyGyro.CFrame = CFrame.new(controlPart.Position, controlPart.Position + camCF.LookVector)
        hum.PlatformStand = not hum.Sit
    end)
end

MovementGroup:AddCheckbox("WalkspeedKeyToggle", {
    Text = "Walkspeed",
    Default = false,
    Callback = function(v)
        sprintEnabled = v
    end,
})

MovementGroup:AddSlider("WalkspeedValue", {
    Text = "Speed",
    Min = 0,
    Max = 500,
    Default = 16,
    Rounding = 0,
    Suffix = " studs/s",
    Callback = function(v)
        Movement.Speed.Value = v
    end,
})

do

    local noclipConnection = nil
    local UserInputService = game:GetService("UserInputService")

    UserInputService.JumpRequest:Connect(function()
        if _G.infJump then
            local char = LocalPlayer.Character
            local humanoid = char and char:FindFirstChild("Humanoid")
            if humanoid and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping then
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end)

    MovementGroup:AddCheckbox("InfiniteJump", {
        Text = "Infinite Jump",
        Default = false,
        Callback = function(Value)
            _G.infJump = Value
        end
    })

    local function ToggleNoClip(state)
        if noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection = nil
        end
        
        if state then
            noclipConnection = RunService.Stepped:Connect(function()
                local char = LocalPlayer.Character
                if char then
                    for _, part in pairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
            end)
        end
    end

    MovementGroup:AddCheckbox("NoClip", {
        Text = "No Clip",
        Default = false,
        Callback = function(Value)
            _G.noclip = Value
            ToggleNoClip(Value)
        end
    })
end

MovementGroup:AddCheckbox("FlyToggle", {
    Text = "Fly(fixing)",
    Default = false,
    Callback = function(v)
        Movement.Fly.Enabled = v
        startFly()
    end,
})

MovementGroup:AddSlider("FlySpeedValue", {
    Text = "Fly Speed",
    Min = 1,
    Max = 1000,
    Default = 120,
    Rounding = 0,
    Suffix = " studs/s",
    Callback = function(v)
        Movement.Fly.Speed = v
    end,
})

game:GetService("RunService").RenderStepped:Connect(function()
    if not sprintEnabled then
        return
    end
    char = LocalPlayer.Character
    if not char then
        return
    end
    hum = char:FindFirstChildOfClass('Humanoid')
    root = char:FindFirstChild('HumanoidRootPart')
    if not hum or not root then
        return
    end
    dir = hum.MoveDirection
    if dir.Magnitude > 0 then
        root.AssemblyLinearVelocity = Vector3.new(dir.Unit.X * Movement.Speed.Value, root.AssemblyLinearVelocity.Y, dir.Unit.Z * Movement.Speed.Value)
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    char = LocalPlayer.Character
    if not char then
        return
    end
    hum = char:FindFirstChildOfClass('Humanoid')
    if not hum then
        return
    end
    hum.UseJumpPower = true
    hum.JumpPower = 50
end)

LocalPlayer = game:GetService("Players").LocalPlayer
UserInputService = game:GetService("UserInputService")
RunService = game:GetService("RunService")

ocnFlyChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
ocnFlyHum = ocnFlyChar:WaitForChild("Humanoid")
ocnFlyRoot = ocnFlyChar:WaitForChild("HumanoidRootPart")

FLY_WALK_SPEED = 200
FLY_ASCEND_SPEED = 50
FLY_DESCEND_SPEED = 50
FLY_TURN_SPEED = 0.15
ocnFlying = false
ocnFlyAttach = nil
ocnFlyVel = nil
ocnLastTap = 0
DOUBLE_TAP_TIME = 0.3
ocnSpaceDown = false
flyEnabled = false

local function setFlying(enabled)
    if not flyEnabled then
        if ocnFlying then
            ocnFlying = false
            ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Freefall, true)
            ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
            ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            if ocnFlyVel then ocnFlyVel:Destroy(); ocnFlyVel = nil end
            if ocnFlyAttach then ocnFlyAttach:Destroy(); ocnFlyAttach = nil end
            ocnFlyHum:ChangeState(Enum.HumanoidStateType.GettingUp)
        end
        return
    end
    
    ocnFlying = enabled
    if enabled then
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Freefall, false)
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        ocnFlyHum:ChangeState(Enum.HumanoidStateType.Running)
        ocnFlyAttach = Instance.new("Attachment")
        ocnFlyAttach.Parent = ocnFlyRoot
        ocnFlyVel = Instance.new("LinearVelocity")
        ocnFlyVel.Attachment0 = ocnFlyAttach
        ocnFlyVel.VelocityConstraintMode = Enum.VelocityConstraintMode.Vector
        ocnFlyVel.MaxForce = math.huge
        ocnFlyVel.RelativeTo = Enum.ActuatorRelativeTo.World
        ocnFlyVel.VectorVelocity = Vector3.zero
        ocnFlyVel.Parent = ocnFlyRoot
    else
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Freefall, true)
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
        ocnFlyHum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
        if ocnFlyVel then ocnFlyVel:Destroy(); ocnFlyVel = nil end
        if ocnFlyAttach then ocnFlyAttach:Destroy(); ocnFlyAttach = nil end
        ocnFlyHum:ChangeState(Enum.HumanoidStateType.GettingUp)
    end
end

_G.Brkhs = false
fbexpConn = nil

function fbexp()
    if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
    fbexpConn = workspace.ChildAdded:Connect(function(child)
        if _G.Brkhs then
            if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
            return
        end
        if child.Name == "Part" and (child.Position - Vector3.new(263.4, -4.79, 466.8)).Magnitude <= 2 then
            _G.Brkhs = true
            Library:Notify({Title = "Note!", Description = "Destroyed Houses Barrier", Duration = 3})
            for _, plot in pairs(workspace.Plots:GetChildren()) do
                barrier = plot:FindFirstChild("Barrier")
                if barrier then
                    for _, part in pairs(barrier:GetChildren()) do
                        if part:IsA("BasePart") and part.CanCollide == true then
                            part.CanCollide = false
                        end
                    end
                end
            end
            if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
        end
    end)
end

function breakhouse(mode)
    if not mode then return end
    _G.Brkhs = false
    fbexp()
    startTime = tick()
    repeat
        pcall(function()
            game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(
                "BallSnowball",
                CFrame.new(263.5, -4.5, 486.9),
                Vector3.new(0, 0, 0)
            )
        end)
        wStart = tick()
        repeat task.wait() until not LocalPlayer:FindFirstChild("CanSpawnToy") or LocalPlayer.CanSpawnToy.Value or tick() - wStart > 2
    until _G.Brkhs or tick() - startTime >= 10
    if fbexpConn then fbexpConn:Disconnect() fbexpConn = nil end
end

FlyGroup = Tabs.Player:AddLeftGroupbox("Fly", "bird")
BarrierGroup = Tabs.Defense:AddLeftGroupbox("House Barrier", "house")

do
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")

    local StatusLabel = BarrierGroup:AddLabel("Broken: False")

    local destroyBarrierActive = false
    local destroyBarrierThread = nil
    local plotsBroken = false

    BarrierGroup:AddCheckbox("DestroyBarrierHouse", {
        Text = "Destroy Barrier (snowball)",
        Default = false,
        Callback = function(Value)
            if Value and plotsBroken then
                Library:Notify({
                    Title = "Already Broken",
                    Description = "Plots are already broken!",
                    Duration = 5
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
                    StatusLabel:SetText("Broken: False")
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
                            StatusLabel:SetText("Broken: True")
                            plotsBroken = true
                            Library:Notify({
                                Title = "Success!",
                                Description = "The plots have been broken successfully!",
                                Duration = 5
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
                    StatusLabel:SetText("Broken: False")
                end
            end
        end
    })
end

BarrierGroup:AddButton({
    Text = "Break House Barriers(Best)",
    Func = function()
        breakhouse("auto")
    end
})

BarrierGroup:AddToggle("AntiBarrier", {
    Text = "Anti Barrier",
    Default = false,
    Callback = function(val)
        plots = workspace:FindFirstChild("Plots")
        if not plots then return end
        for _, plot in ipairs(plots:GetChildren()) do
            barrierModel = plot:FindFirstChild("Barrier")
            if barrierModel then
                for _, part in ipairs(barrierModel:GetChildren()) do
                    if part:IsA("BasePart") and part.Name == "PlotBarrier" then
                        part.CanCollide = not val
                    end
                end
            end
        end
    end
})

FlyGroup:AddCheckbox("FlyToggle", {
    Text = "Enable Fly",
    Default = false,
    Callback = function(Value)
        flyEnabled = Value
        if not Value and ocnFlying then
            setFlying(false)
        end
    end
})

FlyGroup:AddLabel("Double-tap SPACE to toggle | WASD to walk | Hold SPACE to ascend | Hold CTRL to descend")

FlyGroup:AddSlider("FlyWalkSpeed", {
    Text = "Walk Speed",
    Min = 16,
    Max = 500,
    Default = 200,
    Rounding = 0,
    Suffix = " studs/s",
    Callback = function(Value)
        FLY_WALK_SPEED = Value
    end
})

FlyGroup:AddSlider("FlyAscendSpeed", {
    Text = "Ascend Speed",
    Min = 5,
    Max = 200,
    Default = 50,
    Rounding = 0,
    Suffix = " studs/s",
    Callback = function(Value)
        FLY_ASCEND_SPEED = Value
    end
})

FlyGroup:AddSlider("FlyDescendSpeed", {
    Text = "Descend Speed",
    Min = 5,
    Max = 200,
    Default = 50,
    Rounding = 0,
    Suffix = " studs/s",
    Callback = function(Value)
        FLY_DESCEND_SPEED = Value
    end
})

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Space then
        now = tick()
        if now - ocnLastTap < DOUBLE_TAP_TIME then
            setFlying(not ocnFlying)
        end
        ocnLastTap = now
        ocnSpaceDown = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Space then
        ocnSpaceDown = false
    end
end)

RunService.Heartbeat:Connect(function()
    if not ocnFlying or not flyEnabled then return end
    cam = workspace.CurrentCamera
    moveDir = Vector3.zero
    
    if UserInputService:IsKeyDown(Enum.KeyCode.W) then
        moveDir = moveDir + Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) then
        moveDir = moveDir - Vector3.new(cam.CFrame.LookVector.X, 0, cam.CFrame.LookVector.Z)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.A) then
        moveDir = moveDir - Vector3.new(cam.CFrame.RightVector.X, 0, cam.CFrame.RightVector.Z)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.D) then
        moveDir = moveDir + Vector3.new(cam.CFrame.RightVector.X, 0, cam.CFrame.RightVector.Z)
    end
    
    if moveDir.Magnitude > 0 then
        moveDir = moveDir.Unit
        targetCFrame = CFrame.new(ocnFlyRoot.Position, ocnFlyRoot.Position + moveDir)
        ocnFlyRoot.CFrame = ocnFlyRoot.CFrame:Lerp(targetCFrame, FLY_TURN_SPEED)
    end
    
    vertY = 0
    if ocnSpaceDown then
        vertY = FLY_ASCEND_SPEED
    elseif UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
        vertY = -FLY_DESCEND_SPEED
    end
    
    ocnFlyVel.VectorVelocity = Vector3.new(moveDir.X * FLY_WALK_SPEED, vertY, moveDir.Z * FLY_WALK_SPEED)
    ocnFlyHum:ChangeState(Enum.HumanoidStateType.Running)
end)

LocalPlayer.CharacterAdded:Connect(function(newChar)
    ocnFlyChar = newChar
    ocnFlyHum = newChar:WaitForChild("Humanoid")
    ocnFlyRoot = newChar:WaitForChild("HumanoidRootPart")
    ocnFlying = false
    ocnFlyAttach = nil
    ocnFlyVel = nil
end)

do
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    local animator = humanoid:WaitForChild("Animator")

    local activeTracks = {}
    local currentTrack = nil
    local psychoActive = false
    local AnimSpeedMultiplier = 1
    local stopLoopFn = nil
    local stopDanceLoopFn = nil

    local AnimPaths = {}
    local FTAPAnimList = {}
    local CurrentFTAPTrack = nil

    pcall(function()
        AnimPaths = {
            ["Crouch"] = ReplicatedFirst:WaitForChild("Animations"):WaitForChild("Crouch"),
            ["Fire Flail"] = ReplicatedFirst:WaitForChild("CatchFire"):WaitForChild("FireFlailAnimation"),
            ["Flail"] = ReplicatedFirst:WaitForChild("ThrowPlayers"):WaitForChild("Flail"),
            ["Type"] = ReplicatedFirst:WaitForChild("Typing"):WaitForChild("Type"),
        }
        for name in pairs(AnimPaths) do table.insert(FTAPAnimList, name) end
    end)

    function stopFTAPTracks()
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then
            local animRef = hum:FindFirstChildOfClass("Animator") or hum
            for _, track in pairs(animRef:GetPlayingAnimationTracks()) do track:Stop(0.1) end
        end
    end

    function playFTAPAnim()
        if not Toggles.FTAPActiveToggle.Value then return end
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not hum then return end
        local animObj = AnimPaths[Options.FTAPPoseDrop.Value]
        if animObj and animObj:IsA("Animation") then
            stopFTAPTracks()
            local animRef = hum:FindFirstChildOfClass("Animator") or Instance.new("Animator", hum)
            CurrentFTAPTrack = animRef:LoadAnimation(animObj)
            CurrentFTAPTrack.Looped = true
            CurrentFTAPTrack:Play(0.1, 1, AnimSpeedMultiplier)
        end
    end

    if #FTAPAnimList > 0 then
        FunAnim:AddDropdown("FTAPPoseDrop", {
            Values = FTAPAnimList,
            Default = FTAPAnimList[1],
            Text = "Pose",
            Callback = function()
                if Toggles.FTAPActiveToggle and Toggles.FTAPActiveToggle.Value then playFTAPAnim() end
            end
        })

        FunAnim:AddCheckbox("FTAPActiveToggle", {
            Text = "Play Pose",
            Default = false,
            Callback = function(state)
                if state then
                    playFTAPAnim()
                else
                    stopFTAPTracks()
                    if CurrentFTAPTrack then CurrentFTAPTrack:Stop() CurrentFTAPTrack = nil end
                end
            end
        })

        FunAnim:AddSlider("FTAPRateSlider", {
            Text = "Speed",
            Default = 1,
            Min = 0.1,
            Max = 5,
            Rounding = 1,
            Suffix = "x",
            Callback = function(v)
                AnimSpeedMultiplier = v
                if CurrentFTAPTrack and CurrentFTAPTrack.IsPlaying then CurrentFTAPTrack:AdjustSpeed(AnimSpeedMultiplier) end
                if currentTrack and currentTrack.IsPlaying then currentTrack:AdjustSpeed(AnimSpeedMultiplier) end
            end
        })
    end

    local emoteList = {
        {name="Head Throw", id=35154961, speed=1, weight=1, loopSpeed=1},
        {name="Floating Head", id=121572214, speed=1, weight=1},
        {name="Crouch", id=182724289, speed=1, weight=1},
        {name="Floor Crawl", id=282574440, speed=1, weight=1},
        {name="Dino Walk", id=204328711, speed=1, weight=1},
        {name="Jumping Jacks", id=429681631, speed=1, weight=1},
        {name="Loop Head", id=35154961, speed=1, weight=1, loopSpeed=1e6},
        {name="Hero Jump", id=184574340, speed=1, weight=1, loopSpeed=1},
        {name="Faint", id=181526230, speed=1, weight=1},
        {name="Floor Faint", id=181525546, speed=1, weight=1, loopSpeed=2},
        {name="Super Faint", id=181525546, speed=0.5, weight=1, loopSpeed=40},
        {name="Levitate", id=313762630, speed=1, weight=1},
        {name="Float Sit", id=179224234, speed=1, weight=1},
        {name="Weird Move", id=215384594, speed=1, weight=1},
        {name="Clone Illusion", id=215384594, speed=1, weight=1, loopSpeed=1e7},
        {name="Glitch Levitate", id=313762630, speed=1, weight=1, loopSpeed=1e7},
        {name="Full Punch", id=204062532, speed=1, weight=1, loopSpeed=1},
        {name="Bow Down", id=204292303, speed=1, weight=1, loopSpeed=3},
        {name="Sword Slam", id=204295235, speed=1, weight=1, loopSpeed=1},
        {name="Loop Slam", id=204295235, speed=1, weight=1, loopSpeed=1e4},
        {name="Mega Insane", id=184574340, speed=0.5, weight=1, loopSpeed=40},
        {name="Super Punch", id=126753849, speed=1, weight=1, loopSpeed=3},
        {name="Full Swing", id=218504594, speed=1, weight=1, loopSpeed=1},
        {name="Arm Turbine", id=259438880, speed=1, weight=1, loopSpeed=1e3},
        {name="Barrel Roll", id=136801964, speed=1, weight=1, loopSpeed=1},
        {name="Scared", id=180612465, speed=1, weight=1, loopSpeed=1},
        {name="Insane", id=33796059, speed=1, weight=1, loopSpeed=1e8},
        {name="Arm Detach", id=33169583, speed=1, weight=1, loopSpeed=1e6},
        {name="Sword Slice", id=35978879, speed=1, weight=1},
        {name="Insane Arms", id=27432691, speed=1, weight=1, loopSpeed=1e4},
    }

    local danceList = {
        {name="Dab", id=183412246, speed=1, weight=1, loopSpeed=1},
        {name="Spinner", id=188632011, speed=1, weight=1, loopSpeed=2},
        {name="Moving Dance", id=429703734, speed=1, weight=1, loopSpeed=1},
        {name="Spin Dance", id=429730430, speed=1, weight=1, loopSpeed=1},
        {name="Moon Dance", id=45834924, speed=1, weight=1, loopSpeed=1},
        {name="Spin Dance 2", id=186934910, speed=1, weight=1, loopSpeed=1},
        {name="Thriller", id=27789359, speed=1, weight=1, loopSpeed=1},
        {name="Robot", id=30196114, speed=1, weight=1, loopSpeed=1},
        {name="Shuffle", id=248263260, speed=1, weight=1, loopSpeed=1},
        {name="Groove", id=33796059, speed=1, weight=1, loopSpeed=1},
        {name="Club", id=28488254, speed=1, weight=1, loopSpeed=1},
        {name="Jump Dance", id=52155728, speed=1, weight=1, loopSpeed=1},
    }

    function stopAllTracks()
        for _, track in ipairs(activeTracks) do
            pcall(function() track:Stop() track:Destroy() end)
        end
        activeTracks = {}
        currentTrack = nil
    end

    function stopEverything()
        stopLoopFn = nil
        stopDanceLoopFn = nil
        stopAllTracks()
    end

    function playAnimation(data)
        stopEverything()
        local active = true
        local anim = Instance.new("Animation")
        anim.AnimationId = "rbxassetid://" .. data.id
        local track = animator:LoadAnimation(anim)
        currentTrack = track
        table.insert(activeTracks, track)

        function playOnce()
            if not active or currentTrack ~= track then return end
            if not track.IsPlaying then
                track:Play(0.1, data.weight or 1, (data.speed or 1) * AnimSpeedMultiplier)
            end
        end

        if data.loopSpeed then
            track:Play(0.1, data.weight or 1, data.loopSpeed * AnimSpeedMultiplier)
            task.spawn(function()
                while active and currentTrack == track do
                    task.wait()
                    playOnce()
                end
            end)
        else
            track:Play(0.1, data.weight or 1, (data.speed or 1) * AnimSpeedMultiplier)
        end

        return function()
            active = false
            if currentTrack == track then currentTrack = nil end
            pcall(function() track:Stop() track:Destroy() end)
        end
    end

    local emoteNames = {"None"}
    local danceNames = {"None"}
    for _, d in ipairs(emoteList) do table.insert(emoteNames, d.name) end
    for _, d in ipairs(danceList) do table.insert(danceNames, d.name) end

    FunAnim:AddDropdown("EmotePicker", {
        Values = emoteNames,
        Default = "None",
        Text = "Emote",
        Callback = function()
            local sel = Options.EmotePicker.Value
            stopEverything()
            if sel == "None" then return end
            for _, data in ipairs(emoteList) do
                if data.name == sel then stopLoopFn = playAnimation(data) return end
            end
        end
    })

    FunAnim:AddDropdown("DancePicker", {
        Values = danceNames,
        Default = "None",
        Text = "Dance",
        Callback = function()
            local sel = Options.DancePicker.Value
            stopEverything()
            if sel == "None" then return end
            for _, data in ipairs(danceList) do
                if data.name == sel then stopDanceLoopFn = playAnimation(data) return end
            end
        end
    })

    FunAnim:AddSlider("AnimSpeedMultiplier", {
        Text = "Animation Speed",
        Default = 1,
        Min = 0.1,
        Max = 10,
        Rounding = 1,
        Suffix = "x",
        Callback = function(v)
            AnimSpeedMultiplier = v
            if CurrentFTAPTrack and CurrentFTAPTrack.IsPlaying then
                CurrentFTAPTrack:AdjustSpeed(v)
            end
            if currentTrack and currentTrack.IsPlaying then
                currentTrack:AdjustSpeed(v)
            end
        end
    })

    local typingAnimation = Instance.new("Animation")
    typingAnimation.AnimationId = "rbxassetid://18353618958"
    local typingTrack = animator:LoadAnimation(typingAnimation)

    local crouchAnimation = Instance.new("Animation")
    crouchAnimation.AnimationId = "rbxassetid://6980229055"
    local crouchTrack = animator:LoadAnimation(crouchAnimation)

    local throwedAnimation = Instance.new("Animation")
    throwedAnimation.AnimationId = "rbxassetid://7047322890"
    local throwedTrack = animator:LoadAnimation(throwedAnimation)

    local anim1 = Instance.new("Animation")
    anim1.AnimationId = "rbxassetid://33796059"
    local animWalk = Instance.new("Animation")
    animWalk.AnimationId = "rbxassetid://95415492"
    local animJump = Instance.new("Animation")
    animJump.AnimationId = "rbxassetid://165167557"
    local animFall = Instance.new("Animation")
    animFall.AnimationId = "rbxassetid://97170520"
    local animNewIdle = Instance.new("Animation")
    animNewIdle.AnimationId = "rbxassetid://15517864808"

    local idleTrack = humanoid:LoadAnimation(anim1)
    local walkAnimTrack = humanoid:LoadAnimation(animWalk)
    local jumpTrack = humanoid:LoadAnimation(animJump)
    local fallTrack = humanoid:LoadAnimation(animFall)
    local newIdleTrack = humanoid:LoadAnimation(animNewIdle)

    function playIdle()
        task.spawn(function()
            while psychoActive do
                if humanoid.MoveDirection.Magnitude == 0
                and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping
                and humanoid:GetState() ~= Enum.HumanoidStateType.Freefall then
                    idleTrack:Play(0.1, 1, 100 * AnimSpeedMultiplier)
                    task.wait(0.2)
                    idleTrack:Stop()
                    task.wait(1.5)
                    newIdleTrack:Play()
                    newIdleTrack:AdjustSpeed(5 * AnimSpeedMultiplier)
                    task.wait(0.3)
                    newIdleTrack:Stop()
                    task.wait(1.5)
                else
                    task.wait(0.1)
                end
            end
        end)
    end

    function stopIdle()
        idleTrack:Stop()
        newIdleTrack:Stop()
    end

    LocalPlayer.CharacterAdded:Connect(function(newChar)
        humanoid = newChar:WaitForChild("Humanoid")
        animator = newChar:WaitForChild("Humanoid"):WaitForChild("Animator")
        stopAllTracks()
        psychoActive = false
        pcall(function()
            Options.EmotePicker:SetValue("None")
            Options.DancePicker:SetValue("None")
        end)
        task.delay(1.5, function()
            if Toggles.FTAPActiveToggle and Toggles.FTAPActiveToggle.Value then playFTAPAnim() end
        end)
    end)
end

    function stopIdle()
        idleTrack:Stop()
        newIdleTrack:Stop()
    end

    LocalPlayer.CharacterAdded:Connect(function(newChar)
        humanoid = newChar:WaitForChild("Humanoid")
        animator = newChar:WaitForChild("Humanoid"):WaitForChild("Animator")
        stopAllTracks()
        psychoActive = false
        pcall(function()
            Options.EmotePicker:SetValue("None")
            Options.DancePicker:SetValue("None")
        end)
        task.delay(1.5, function()
            if Toggles.FTAPActiveToggle and Toggles.FTAPActiveToggle.Value then playFTAPAnim() end
        end)
    end)

CosmeticGroup = Tabs.Player:AddRightGroupbox("Cosmetics", "sparkle")

FakeCosmeticsEnabled = false
RespawnPersist       = false
CosmeticChoice       = "Both"

KORBLOX_MESH_ID  = "101851696"
KORBLOX_TEX_ID   = "101851254"
HEADLESS_MESH_ID = "134082579"
HEADLESS_TEX_ID  = "134082627"

SavedHeadData = nil

function SnapshotHead()
    char = LocalPlayer.Character
    if not char then return end
    head = char:FindFirstChild("Head")
    if not head then return end
    SavedHeadData = {
        Transparency = head.Transparency,
        BrickColor   = head.BrickColor,
        Material     = head.Material,
        Meshes       = {},
        Decals       = {},
    }
    for _, obj in ipairs(head:GetChildren()) do
        if obj:IsA("SpecialMesh") then
            table.insert(SavedHeadData.Meshes, {
                MeshType    = obj.MeshType,
                MeshId      = obj.MeshId,
                TextureId   = obj.TextureId,
                Scale       = obj.Scale,
                Offset      = obj.Offset,
                VertexColor = obj.VertexColor,
                Name        = obj.Name,
            })
        elseif obj:IsA("Decal") then
            table.insert(SavedHeadData.Decals, {
                Texture      = obj.Texture,
                Face         = obj.Face,
                Transparency = obj.Transparency,
                Name         = obj.Name,
            })
        end
    end
end

function WearHeadless()
    char = LocalPlayer.Character
    if not char then return end
    head = char:FindFirstChild("Head")
    if not head then return end
    SnapshotHead()
    for _, obj in ipairs(head:GetChildren()) do
        if obj:IsA("Decal") or obj:IsA("SpecialMesh") then obj:Destroy() end
    end
    mesh = Instance.new("SpecialMesh")
    mesh.MeshType  = Enum.MeshType.FileMesh
    mesh.MeshId    = "rbxassetid://" .. HEADLESS_MESH_ID
    mesh.TextureId = "rbxassetid://" .. HEADLESS_TEX_ID
    mesh.Scale     = Vector3.new(1.25, 1.25, 1.25)
    mesh.Name      = "PhantomHeadlessMesh"
    mesh.Parent    = head
    head.Transparency = 0.1
    head.BrickColor   = BrickColor.new("Really black")
    head.Material     = Enum.Material.Plastic
end

function WearKorblox()
    char = LocalPlayer.Character
    if not char then return end
    rleg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightLowerLeg")
    if not rleg then return end
    old = char:FindFirstChild("PhantomKorbloxLeg")
    if old then old:Destroy() end
    fakeLeg = Instance.new("Part")
    fakeLeg.Name         = "PhantomKorbloxLeg"
    fakeLeg.Size         = rleg.Size
    fakeLeg.CFrame       = rleg.CFrame
    fakeLeg.Anchored     = false
    fakeLeg.CanCollide   = false
    fakeLeg.Transparency = 0
    fakeLeg.BrickColor   = BrickColor.new("Really black")
    fakeLeg.Material     = Enum.Material.Plastic
    fakeLeg.Parent       = char
    mesh = Instance.new("SpecialMesh")
    mesh.MeshType  = Enum.MeshType.FileMesh
    mesh.MeshId    = "rbxassetid://" .. KORBLOX_MESH_ID
    mesh.TextureId = "rbxassetid://" .. KORBLOX_TEX_ID
    mesh.Scale     = Vector3.new(1, 1, 1)
    mesh.Parent    = fakeLeg
    weld = Instance.new("WeldConstraint")
    weld.Part0  = rleg
    weld.Part1  = fakeLeg
    weld.Parent = rleg
    rleg.Transparency = 1
end

function ApplyCosmetics()
    char = LocalPlayer.Character
    if not char then return end
    if     CosmeticChoice == "Headless" then WearHeadless()
    elseif CosmeticChoice == "Korblox"  then WearKorblox()
    elseif CosmeticChoice == "Both"     then WearHeadless() WearKorblox() end
end

function StripCosmetics()
    char = LocalPlayer.Character
    if not char then return end
    head = char:FindFirstChild("Head")
    if head then
        m = head:FindFirstChild("PhantomHeadlessMesh")
        if m then m:Destroy() end
        if SavedHeadData then
            head.Transparency = SavedHeadData.Transparency
            head.BrickColor   = SavedHeadData.BrickColor
            head.Material     = SavedHeadData.Material
            for _, m2 in ipairs(SavedHeadData.Meshes) do
                mesh = Instance.new("SpecialMesh")
                mesh.MeshType    = m2.MeshType   mesh.MeshId      = m2.MeshId
                mesh.TextureId   = m2.TextureId  mesh.Scale       = m2.Scale
                mesh.Offset      = m2.Offset     mesh.VertexColor = m2.VertexColor
                mesh.Name        = m2.Name       mesh.Parent      = head
            end
            for _, d in ipairs(SavedHeadData.Decals) do
                decal = Instance.new("Decal")
                decal.Texture      = d.Texture  decal.Face        = d.Face
                decal.Transparency = d.Transparency decal.Name    = d.Name
                decal.Parent       = head
            end
            SavedHeadData = nil
        else
            head.Transparency = 0
        end
    end
    rleg = char:FindFirstChild("Right Leg") or char:FindFirstChild("RightLowerLeg")
    if rleg then
        rleg.Transparency = 0
        w = rleg:FindFirstChildOfClass("WeldConstraint")
        if w then w:Destroy() end
    end
    fakeleg = char:FindFirstChild("PhantomKorbloxLeg")
    if fakeleg then fakeleg:Destroy() end
end

LocalPlayer.CharacterAdded:Connect(function()
    task.wait(1)
    SavedHeadData = nil
    if FakeCosmeticsEnabled and RespawnPersist then ApplyCosmetics() end
end)

CosmeticGroup:AddCheckbox("CosmeticMasterToggle", {
    Text = "Enable Cosmetics",
    Default = false,
    Callback = function(v)
        FakeCosmeticsEnabled = v
        if v then
            ApplyCosmetics()
        else
            StripCosmetics()
        end
    end,
})

CosmeticGroup:AddDropdown("CosmeticTypeDrop", {
    Text = "Style",
    Values = {"Headless", "Korblox", "Both"},
    Default = "Both",
    Callback = function(v)
        CosmeticChoice = v
        if FakeCosmeticsEnabled then StripCosmetics() ApplyCosmetics() end
    end,
})

CosmeticGroup:AddCheckbox("CosmeticRespawnToggle", {
    Text = "Re-apply on Respawn",
    Default = false,
    Callback = function(v)
        RespawnPersist = v
    end,
})
EnvBox = Tabs.Player:AddRightGroupbox("Sounds", "audio-lines")

EnvBox:AddDivider()

Players = game:GetService('Players')
UserInputService = game:GetService('UserInputService')
SoundService = game:GetService('SoundService')
TextChatService = game:GetService('TextChatService')
player = Players.LocalPlayer

soundMap = {
    ['Normal Typing'] = 'rbxassetid://72486459002567',
    ['Thocky Typing'] = 'rbxassetid://76696739955497',
    ['Clean Typing'] = 'rbxassetid://131944804697356',
    ['Clicky Typing'] = 'rbxassetid://9116149587',
}

currentSoundId = soundMap['Normal Typing']
typingSound = Instance.new('Sound')
typingSound.SoundId = currentSoundId
typingSound.Looped = true
typingSound.Volume = 1
typingSound.Parent = SoundService

typing = false
enabled = true
volume = 100

function startSound()
    if not enabled then return end
    if typingSound.SoundId and typingSound.SoundId ~= '' then
        if not typingSound.IsPlaying then
            typingSound:Play()
        end
    end
end

function stopSound()
    typingSound:Stop()
end

UserInputService.TextBoxFocused:Connect(function()
    typing = true
    startSound()
end)

UserInputService.TextBoxFocusReleased:Connect(function()
    typing = false
    stopSound()
end)

EnvBox:AddCheckbox("TypingSoundToggle", {
    Text = "Typing Sound",
    Default = true,
    Callback = function(state)
        enabled = state
        if not state then
            stopSound()
        end
    end,
})

EnvBox:AddSlider("TypingVolumeSlider", {
    Text = "Volume",
    Default = 100,
    Min = 0,
    Max = 100,
    Rounding = 0,
    Suffix = "%",
    Callback = function(v)
        volume = v
        typingSound.Volume = v / 100
    end,
})

EnvBox:AddDropdown("TypingSoundDropdown", {
    Text = "Typing Sound",
    Default = "Normal Typing",
    Values = {
        "Normal Typing",
        "Thocky Typing",
        "Clean Typing",
        "Clicky Typing",
    },
    Callback = function(selected)
        id = soundMap[selected]
        if not id then return end
        currentSoundId = id
        typingSound:Stop()
        typingSound.SoundId = id
        typingSound.TimePosition = 0
    end,
})

DefL = Tabs.Defense:AddLeftGroupbox("Antis", "heart-minus")

antiGrabV1Active = false
antiGrabV1Task = nil

DefL:AddCheckbox("AntiGrabV1", {
    Text = "Anti Grab(Ags)",
    Default = false,
    Callback = function(Value)
        antiGrabV1Active = Value
        
        if Value then
            antiGrabV1Task = task.spawn(function()
                while antiGrabV1Active do
                    pcall(function()
                        plr = game:GetService("Players").LocalPlayer
                        isHeld = plr:FindFirstChild("IsHeld")
                        
                        if isHeld and isHeld.Value then
                            char = plr.Character
                            if char then
                                hum = char:FindFirstChild("Humanoid")
                                hrp = char:FindFirstChild("HumanoidRootPart")
                                
                                if hum and hrp then
                                    game:GetService("ReplicatedStorage").CharacterEvents.Struggle:FireServer(plr)
                                    game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0.00000000001)
                                    if hum.Sit then
                                        hum.Sit = false
                                    end
                                end
                            end
                        end
                    end)
                    task.wait(0.05)
                end
            end)
        else
            if antiGrabV1Task then
                task.cancel(antiGrabV1Task)
                antiGrabV1Task = nil
            end
        end
    end
})


antiGrabV2Active = false
antiGrabV2Task = nil

DefL:AddCheckbox("AntiGrabV2", {
    Text = "Anti Grab Best (seatless gucci)",
    Default = false,
    Callback = function(Value)
        antiGrabV2Active = Value
        
        if Value then
            antiGrabV2Task = task.spawn(function()
                hkAGSt = nil
                hkAGModel = nil
                hkPlot = nil
                
                while antiGrabV2Active do
                    pcall(function()
                        plr = game:GetService("Players").LocalPlayer
                        
                        for _, home in pairs(workspace.Plots:GetChildren()) do
                            for _, person in pairs(home.PlotSign.ThisPlotsOwners:GetChildren()) do
                                if person.Value == plr.Name then
                                    hkPlot = home.Name
                                end
                            end
                            for _, person in pairs(home.PlotSign:GetChildren()) do
                                if person.Name == "Sign" then
                                    if person.Screen.SurfaceGui.Frame.Visible and person.Screen.SurfaceGui.Frame.PlayerDisplayName.Text == plr.DisplayName then
                                        hkPlot = home.Name
                                    end
                                end
                            end
                        end
                        
                        hkAGModel = workspace[plr.Name .. "SpawnedInToys"]:FindFirstChild("InstrumentWoodwindOcarina")
                            or (hkPlot and workspace.PlotItems[hkPlot]:FindFirstChild("InstrumentWoodwindOcarina"))

                        if hkAGModel then
                            if plr.Character then
                                for _, prt in pairs(plr.Character:GetChildren()) do
                                    if prt:FindFirstChild("PartOwner") and prt.PartOwner.Value ~= "" then
                                        if hkAGModel:FindFirstChild("HoldPart") and hkAGModel.HoldPart:FindFirstChild("HoldItemRemoteFunction") then
                                            task.spawn(function()
                                                hkAGModel.HoldPart.HoldItemRemoteFunction:InvokeServer(hkAGModel, plr.Character)
                                            end)
                                            game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(hkAGModel)
                                            if plr.Character:FindFirstChild("Humanoid") then
                                                plr.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                                                plr.Character.Humanoid.AutoRotate = true
                                            end
                                            prt.PartOwner.Value = ""
                                            if plr.Character.Humanoid.Sit then
                                                plr.Character.Humanoid.Sit = false
                                            end
                                        end
                                    end
                                end
                            end
                        else
                            if plr.Character and plr:FindFirstChild("CanSpawnToy") and plr.CanSpawnToy.Value and not hkAGSt then
                                hkAGSt = tick()
                                task.spawn(function()
                                    game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                        "InstrumentWoodwindOcarina",
                                        CFrame.new(1e5, 1e5, 1e5),
                                        Vector3.new(0, 0, 0)
                                    )
                                end)
                            elseif hkAGSt and tick() - hkAGSt > 1 and not workspace[plr.Name .. "SpawnedInToys"]:FindFirstChild("InstrumentWoodwindOcarina") then
                                hkAGSt = nil
                            end

                            grabbed = false
                            if plr.Character then
                                for _, prt in pairs(plr.Character:GetChildren()) do
                                    if prt:FindFirstChild("PartOwner") and prt.PartOwner.Value ~= "" then
                                        grabbed = true
                                    end
                                end
                            end

                            if grabbed then
                                game.ReplicatedStorage.CharacterEvents.Struggle:FireServer(plr)
                                if plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character:FindFirstChild("HumanoidRootPart") then
                                    game.ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(plr.Character.HumanoidRootPart, 0.00000000001)
                                    for _, track in ipairs(plr.Character.Humanoid:GetPlayingAnimationTracks()) do
                                        if track.Animation.AnimationId == "rbxassetid://7047322890" then track:Stop() end
                                    end
                                end
                            end
                        end

                        if plr.Character and plr.Character:FindFirstChild("Humanoid") and antiGrabV2Active then
                            for _, track in ipairs(plr.Character.Humanoid:GetPlayingAnimationTracks()) do
                                if track.Animation.AnimationId == "rbxassetid://7047322890" then track:Stop() end
                            end
                        end
                    end)
                    task.wait()
                end
            end)
        else
            if antiGrabV2Task then
                task.cancel(antiGrabV2Task)
                antiGrabV2Task = nil
            end
        end
    end
})
gucciRunId = 0
gucciActive = false
gucciTask = nil
checkGucciSeatTask = nil

local function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time or 3)
end

local function toy_spawn_gucci(name, cframe, vector)
    ToySpawn = game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction
    InPlot, InOwnerPlot, CanSpawn = LocalPlayer.InPlot, LocalPlayer.InOwnedPlot, LocalPlayer.CanSpawnToy

    while InPlot.Value and not InOwnerPlot.Value and not CanSpawn.Value do
        task.wait(0.01)
    end

    task.spawn(function()
        ToySpawn:InvokeServer(name, cframe, vector or Vector3.new())
    end)
    
    BackPack = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
    local SpawnedToy
    BackPack.ChildAdded:Once(function(toy)
        if toy.Name == name and toy:IsA("Model") then
            SpawnedToy = toy
        end
    end)
    
    time = tick()
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
    if not gucciActive then return end
    
    gucciRunId = gucciRunId + 1
    MyId = gucciRunId
    
    char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    hum = FWC(char, "Humanoid")
    
    hum.Sit = true
    task.wait(0.02)
    hum.Sit = false
    task.wait(0.02)
    
    task.spawn(function()
        time = tick()
        while tick()-time < 0.8 do
            for _,v in pairs(char:GetChildren()) do
                if v:IsA('BasePart') then
                    v.Velocity = Vector3.new()
                end
            end
            task.wait(0.01)
        end
    end)
    
    autoGucciT, sitJumpT, Blob, BHead = true, false, nil, nil
    
    task.spawn(function()
        while not Blob and MyId == gucciRunId do
            task.wait(0.01)
        end
        if MyId ~= gucciRunId then return end
        
        BHead = FWC(Blob, "Head")
        HitBox = FWC(Blob, "GrabbableHitbox")
        
        while MyId == gucciRunId and BHead and
        (not BHead:FindFirstChild("PartOwner") or BHead.PartOwner.Value ~= LocalPlayer.Name) do
            if HitBox then
                game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(HitBox, HitBox.CFrame)
            end
            task.wait(0.01)
        end
    end)
    
    hrp = FWC(char, "HumanoidRootPart")
    Blob = toy_spawn_gucci(
        "CreatureBlobman",
        hrp.CFrame * CFrame.new(0, 0, -5),
        Vector3.new(0, -15.716, 0)
    )
    
    if not Blob then return end
    
    Seat = FWC(Blob, "VehicleSeat")
    
    task.defer(function()
        if not(char or hum) then return end
        
        startTime = tick()
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
                game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(hrp, 0.095)
            end
            task.wait(0.01)
        end
    end)
    
    local index
    
    task.wait(0.4)
    
    if MyId ~= gucciRunId then return end
    
    hum.Sit = false
    Blob.Name = "Gucci"
    
    BackPack = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
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
    
    success, contents = pcall(function()
        return LocalPlayer.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents
    end)
    
    if success and contents and index then
        for i,v in ipairs(contents:GetChildren()) do
            if v.Name == "CreatureBlobman" and i == index then
                view = v.ViewItemButton
                view.Text = "GUCCI"
                view.TextScaled = true
                view.LowResImage.Image = ""
            end
        end
    end
end

local GucciToggle = DefL:AddCheckbox("GucciAntiGrab", {
    Text = "Gucci Anti Grab",
    Default = false,
    Callback = function(Value)
        gucciActive = Value
        
        if gucciTask then
            task.cancel(gucciTask)
            gucciTask = nil
        end
        if checkGucciSeatTask then
            task.cancel(checkGucciSeatTask)
            checkGucciSeatTask = nil
        end
        
        if not Value then
            BackPack = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
            if BackPack then
                gucci = BackPack:FindFirstChild("Gucci")
                if gucci then
                    pcall(function()
                        game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(gucci)
                    end)
                end
                for _, v in pairs(BackPack:GetChildren()) do
                    if v.Name == "Gucci" or v.Name == "CreatureBlobman" then
                        pcall(function()
                            game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(v)
                        end)
                    end
                end
            end
            return
        end
        
        gucciTask = task.spawn(function()
            while gucciActive do
                BackPack = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
                gucci = BackPack and BackPack:FindFirstChild("Gucci")
                
                if not gucci then
                    GucciAntiGrab()
                end
                task.wait(0.5)
            end
        end)
        
        checkGucciSeatTask = task.spawn(function()
            while gucciActive do
                char = LocalPlayer.Character
                hum = char and char:FindFirstChild("Humanoid")
                
                if hum and hum.SeatPart then
                    seatParent = hum.SeatPart.Parent
                    if seatParent and (seatParent.Name == "Gucci" or seatParent.Name == "CreatureBlobman") then
                        isOurGucci = false
                        BackPack = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
                        if BackPack then
                            for _, v in pairs(BackPack:GetChildren()) do
                                if v == seatParent then
                                    isOurGucci = true
                                    break
                                end
                            end
                        end
                        
                        if isOurGucci then
                            pcall(function()
                                game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(seatParent)
                            end)
                            task.wait(0.2)
                            
                            if gucciActive then
                                GucciAntiGrab()
                            end
                        end
                    end
                end
                
                task.wait(0.3)
            end
        end)
    end
})

local GucciKeybind = GucciToggle:AddKeyPicker("GucciKeybind", {
    Text = "Gucci Keybind",
    Default = "G",
    Mode = "Toggle",
})

autoGucciActive = false
autoGucciTask = nil
trainLoopConnection = nil
trainJumpCounter = 0
trainRootPos = nil

local function stopTrainRide()
    if trainLoopConnection then
        trainLoopConnection:Disconnect()
        trainLoopConnection = nil
    end
end

local function initTrainRide()
    runService = game:GetService("RunService")
    character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    humanoid = character:WaitForChild('Humanoid')
    rootPart = character:WaitForChild('HumanoidRootPart')
    trainRootPos = rootPart.Position
    
    trainObject = workspace:FindFirstChild("Map")
    if trainObject then
        trainObject = trainObject:FindFirstChild("AlwaysHereTweenedObjects")
    end
    train = trainObject and trainObject:FindFirstChild('Train')
    trainSeat = nil
    
    if train then
        for _, descendant in ipairs(train:GetDescendants()) do
            if descendant:IsA('Seat') then
                trainSeat = descendant
                break
            end
        end
    end
    
    if not train or not trainSeat then
        return
    end
    
    rootPart.CFrame = trainSeat.CFrame + Vector3.new(0, 2, 0)
    trainSeat:Sit(humanoid)
    
    humanoid:GetPropertyChangedSignal('Jump'):Connect(function()
        if (humanoid.Jump and humanoid.Sit) then
            trainJumpCounter = 15
            trainRootPos = rootPart.Position
        end
    end)
    
    if trainLoopConnection then
        trainLoopConnection:Disconnect()
    end
    
    trainLoopConnection = runService.Heartbeat:Connect(function()
        if (not rootPart or not humanoid) then
            return
        end
        pcall(function()
            game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(rootPart, 0)
        end)
        if (trainJumpCounter > 0) then
            rootPart.CFrame = CFrame.new(trainRootPos)
            trainJumpCounter = trainJumpCounter - 1
        end
    end)
    
    task.spawn(function()
        while humanoid.Sit do
            task.wait(1)
        end
        task.wait(0.5)
        rootPart.CFrame = CFrame.new(trainRootPos)
    end)
end

DefL:AddCheckbox("AutoGucciTrain", {
    Text = "Auto Gucci (Train)",
    Default = false,
    Callback = function(Value)
        autoGucciActive = Value
        
        if Value then
            initTrainRide()
            
            autoGucciTask = task.spawn(function()
                while autoGucciActive do
                    pcall(function()
                        trainObject = workspace:FindFirstChild("Map")
                        if trainObject then
                            trainObject = trainObject:FindFirstChild("AlwaysHereTweenedObjects")
                        end
                        train = trainObject and trainObject:FindFirstChild('Train')
                        
                        if not train then
                            stopTrainRide()
                            attempts = 0
                            repeat
                                task.wait(0.2)
                                attempts = attempts + 1
                                trainObject = workspace:FindFirstChild("Map")
                                if trainObject then
                                    trainObject = trainObject:FindFirstChild("AlwaysHereTweenedObjects")
                                end
                            until (trainObject and trainObject:FindFirstChild('Train')) or (attempts > 25) or not autoGucciActive
                            
                            if (autoGucciActive and trainObject and trainObject:FindFirstChild('Train')) then
                                initTrainRide()
                            end
                        end
                    end)
                    task.wait(0.5)
                end
            end)
        else
            autoGucciActive = false
            stopTrainRide()
            
            if autoGucciTask then
                task.cancel(autoGucciTask)
                autoGucciTask = nil
            end
        end
    end
})

DefL:AddLabel("Antis Extras ----------------")

antiOwnershipActive = false
antiOwnershipTask = nil

DefL:AddCheckbox("AntiOwnership", {
    Text = "Anti Ownership",
    Default = false,
    Callback = function(Value)
        antiOwnershipActive = Value
        if Value then
            antiOwnershipTask = task.spawn(function()
                Struggle = game:GetService("ReplicatedStorage").CharacterEvents.Struggle
                while antiOwnershipActive do
                    pcall(function()
                        character = LocalPlayer.Character
                        if character and character:FindFirstChild("Head") then
                            head = character.Head
                            if head:FindFirstChild("PartOwner") then
                                Struggle:FireServer(LocalPlayer)
                                for _, part in pairs(character:GetChildren()) do
                                    if part:IsA("BasePart") then
                                        part.Anchored = true
                                    end
                                end
                                isHeld = LocalPlayer:FindFirstChild("IsHeld")
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
                    end)
                    task.wait(0.1)
                end
            end)
        else
            if antiOwnershipTask then
                task.cancel(antiOwnershipTask)
                antiOwnershipTask = nil
            end
            char = LocalPlayer.Character
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
paintPartsBackup = {}
paintConnections = {}

DefL:AddCheckbox("AntiPaint", {
    Text = "Anti Paint",
    Default = false,
    Callback = function(Value)
        if Value then
            pcall(function()
                for _, obj in ipairs(workspace:GetDescendants()) do
                    if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
                        clone = obj:Clone()
                        clone.Archivable = true
                        paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
                        obj:Destroy()
                    end
                end
            end)
            
            table.insert(paintConnections, workspace.DescendantAdded:Connect(function(obj)
                if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
                    task.defer(function()
                        if obj and obj.Parent then
                            clone = obj:Clone()
                            clone.Archivable = true
                            paintPartsBackup[obj:GetDebugId()] = { clone = clone, parent = obj.Parent }
                            obj:Destroy()
                        end
                    end)
                end
            end))
            
            char = workspace:FindFirstChild(LocalPlayer.Name)
            if char then
                for _, v in ipairs(char:GetChildren()) do
                    if v:IsA("Part") or v:IsA("BasePart") then
                        v.CanTouch = false
                        v.CanQuery = false
                    end
                end
            end
        else
            for _, data in pairs(paintPartsBackup) do
                if data.clone and data.parent then
                    data.clone.Parent = data.parent
                end
            end
            paintPartsBackup = {}
            for _, conn in ipairs(paintConnections) do
                if conn.Connected then conn:Disconnect() end
            end
            paintConnections = {}
            char = workspace:FindFirstChild(LocalPlayer.Name)
            if char then
                for _, v in ipairs(char:GetChildren()) do
                    if v:IsA("Part") or v:IsA("BasePart") then
                        v.CanTouch = true
                        v.CanQuery = true
                    end
                end
            end
        end
    end
})

antiFireActive = false
antiFireTask = nil
hkFirePart = nil

DefL:AddCheckbox("AntiFire", {
    Text = "Anti Fire",
    Default = false,
    Callback = function(Value)
        antiFireActive = Value
        
        if Value then
            pcall(function()
                if workspace.Plots and workspace.Plots.Plot5 and workspace.Plots.Plot5.Barrier then
                    if workspace.Plots.Plot5.Barrier:FindFirstChild("AntiFirePart") then
                        hkFirePart = workspace.Plots.Plot5.Barrier.AntiFirePart
                    else
                        hkFirePart = workspace.Plots.Plot5.Barrier:FindFirstChild("PlotBarrier")
                    end
                    if hkFirePart then
                        hkFirePart.CanCollide = true
                        hkFirePart.CanQuery = true
                        hkFirePart.Name = "AntiFirePart"
                        h2 = hkFirePart:Clone()
                        h2.Name = "FalseBorder"
                        h2.Parent = hkFirePart.Parent
                        hkFirePart.Size = Vector3.new(1,1,1)
                        for _, prt in pairs(hkFirePart:GetChildren()) do
                            prt:Destroy()
                        end
                        hkFirePart.CanQuery = false
                        hkFirePart.CanCollide = false
                    end
                end
            end)
            
            antiFireTask = task.spawn(function()
                while antiFireActive do
                    pcall(function()
                        if hkFirePart then
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                hkFirePart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame
                            end
                            hkFirePart.CanCollide = not hkFirePart.CanCollide
                            hkFirePart.CanCollide = not hkFirePart.CanCollide
                        end
                    end)
                    task.wait()
                end
                if hkFirePart then
                    hkFirePart.CFrame = CFrame.new(0,-15,0)
                end
            end)
        else
            if antiFireTask then
                task.cancel(antiFireTask)
                antiFireTask = nil
            end
            if hkFirePart then
                hkFirePart.CFrame = CFrame.new(0,-15,0)
            end
        end
    end
})

antiExplosionActive = false
antiExplosionConnection = nil

DefL:AddCheckbox("AntiExplosion", {
    Text = "Anti Explosion",
    Default = false,
    Callback = function(Value)
        antiExplosionActive = Value
        if Value then
            char = LocalPlayer.Character
            if not char then return end
            hrp = char:WaitForChild("HumanoidRootPart")
            antiExplosionConnection = workspace.ChildAdded:Connect(function(model)
                if model.Name == "Part" and antiExplosionActive then
                    pcall(function()
                        mag = (model.Position - hrp.Position).Magnitude
                        if mag <= 20 then
                            hrp.Anchored = true
                            task.wait(0.01)
                            rightArm = char:FindFirstChild("Right Arm")
                            if rightArm then
                                ragdollPart = rightArm:FindFirstChild("RagdollLimbPart")
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
                    end)
                end
            end)
        else
            if antiExplosionConnection then
                antiExplosionConnection:Disconnect()
                antiExplosionConnection = nil
            end
            char = LocalPlayer.Character
            if char then
                hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then hrp.Anchored = false end
            end
        end
    end
})

antiVoidActive = false
antiVoidConnection = nil

local function StartAntiVoid()
    VOID_THRESHOLD = -50
    SAFE_HEIGHT = 100
    antiVoidConnection = game:GetService("RunService").Heartbeat:Connect(function()
        if not antiVoidActive then return end
        char = LocalPlayer.Character
        if char and char.PrimaryPart then
            pos = char.PrimaryPart.Position
            if pos.Y < VOID_THRESHOLD then
                safePos = Vector3.new(pos.X, pos.Y + SAFE_HEIGHT, pos.Z)
                char:SetPrimaryPartCFrame(CFrame.new(safePos))
                char.PrimaryPart.AssemblyLinearVelocity = Vector3.zero
            end
        end
    end)
end

DefL:AddCheckbox("AntiVoidToggle", {
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

hkABlob = false

DefL:AddCheckbox("AntiBlob", {
    Text = "Anti-Blob",
    Default = false,
    Callback = function(Value)
        hkABlob = Value
        task.spawn(function()
            while hkABlob do
                if player.Character then
                    if not player.Character:FindFirstChild("TruePositionPart") then
                        local tp = Instance.new("Part")
                        tp.Parent = player.Character 
                        tp.Name = "TruePositionPart"
                        tp.Anchored = true 
                        tp.CFrame = CFrame.new(0,-100,0)
                    end
                    for _,prt in pairs(player.Character:GetChildren()) do
                        if prt:IsA("BasePart") and prt.Massless then 
                            prt.Massless = false 
                        end
                        if prt.Name == "HumanoidRootPart" and player.Character.HumanoidRootPart:FindFirstChild("RootAttachment") then
                            task.wait() 
                            task.wait() 
                            task.wait() 
                            task.wait() 
                            task.wait()
                            task.wait() 
                            task.wait() 
                            task.wait() 
                            task.wait() 
                            task.wait()
                            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and
                               player.Character.HumanoidRootPart:FindFirstChild("RootAttachment") and
                               player.Character:FindFirstChild("TruePositionPart") then
                                player.Character.HumanoidRootPart.RootAttachment.Parent = player.Character.TruePositionPart
                            end
                        end
                    end
                end
                task.wait()
            end
        end)
        if not Value and player.Character then
            if player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("TruePositionPart") then
                if player.Character.TruePositionPart:FindFirstChild("RootAttachment") then
                    player.Character.TruePositionPart.RootAttachment.Parent = player.Character.HumanoidRootPart
                end
            end
        end
    end
})
antiSnowballActive = false
antiSnowballTask = nil

DefL:AddCheckbox("AntiSnowball", {
    Text = "Anti Snowball",
    Default = false,
    Callback = function(Value)
        antiSnowballActive = Value
        if Value then
            antiSnowballTask = task.spawn(function()
                while antiSnowballActive do
                    pcall(function()
                        char = LocalPlayer.Character
                        hrp = char and char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(hrp, 0.5)
                        end
                    end)
                    task.wait(0.05)
                end
            end)
        else
            if antiSnowballTask then
                task.cancel(antiSnowballTask)
                antiSnowballTask = nil
            end
        end
    end
})

do
    AntiRagBlob = false
    RagdolledSit = false
    Cons = {}

    local function ApplyAntiRagdoll(char)
        if not char or not AntiRagBlob then return end
        
        hum = char:WaitForChild("Humanoid", 5)
        HRP = char:WaitForChild("HumanoidRootPart", 5)
        if not (hum and HRP) then return end
        
        if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
        Cons["ARSeat"] = hum:GetPropertyChangedSignal("SeatPart"):Connect(function()
            if hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" and not RagdolledSit then
                RagdolledSit = true
                Seat = hum.SeatPart
                while not hum.Sit do task.wait() end
                
                game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(HRP, 3)
                
                ragdolledVal = hum:FindFirstChild("Ragdolled")
                while ragdolledVal and not ragdolledVal.Value and not hum.Sit do task.wait() end
                
                task.wait(0.4)
                hum.Sit = false
                Seat:Sit(hum)
                
                task.delay(0.25, function()
                    while hum and hum.SeatPart do
                        game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(HRP, 1)
                        task.wait(0.05)
                    end
                    RagdolledSit = false
                end)
            end
        end)
    end

    DefL:AddCheckbox("AntiRagdoll", {
        Text = "Anti Ragdoll (On Blob)",
        Default = false,
        Callback = function(Value)
            AntiRagBlob = Value
            RagdolledSit = false
            
            if Cons["ARChar"] then Cons["ARChar"]:Disconnect() end
            if Cons["ARSeat"] then Cons["ARSeat"]:Disconnect() end
            
            if AntiRagBlob then
                ApplyAntiRagdoll(LocalPlayer.Character)
                Cons["ARChar"] = LocalPlayer.CharacterAdded:Connect(ApplyAntiRagdoll)
            end
        end
    })
end

ocnAntiLagOn = false
ocnFpsThreshold = 30
ocnFpsFrames = 0
ocnLastFpsCheck = tick()
ocnAutoLagStartDelay = tick()
ocnAutoLagEnabled = false

DefL:AddCheckbox("AntiLag", {
    Text = "Anti Lag",
    Default = false,
    Callback = function(Value)
        ocnAntiLagOn = Value
        if Value then
            pcall(function()
                LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = true
                for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
                    if plr.Character and plr.Character:FindFirstChild("GrabParts") then
                        plr.Character.GrabParts:Destroy()
                    end
                end
            end)
        else
            pcall(function()
                LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = false
            end)
        end
    end
})

DefL:AddCheckbox("AutoAntiLag", {
    Text = "Auto Anti Lag",
    Default = false,
    Callback = function(Value)
        ocnAutoLagEnabled = Value
        if Value then
            ocnLastFpsCheck = tick()
            ocnFpsFrames = 0
            ocnAutoLagStartDelay = tick()
        end
    end
})

DefL:AddSlider("AntiLagFPSThreshold", {
    Text = "Auto Anti Lag FPS Set",
    Min = 10,
    Max = 120,
    Default = 30,
    Rounding = 0,
    Suffix = " FPS",
    Callback = function(Value)
        ocnFpsThreshold = Value
    end
})

task.spawn(function()
    while task.wait() do
        if ocnAutoLagEnabled then
            if tick() - ocnAutoLagStartDelay < 5 then
                task.wait()
            else
                ocnFpsFrames = ocnFpsFrames + 1
                now = tick()
                if now - ocnLastFpsCheck >= 1 then
                    fps = ocnFpsFrames / (now - ocnLastFpsCheck)
                    ocnFpsFrames = 0
                    ocnLastFpsCheck = now
                    if fps <= ocnFpsThreshold and not ocnAntiLagOn then
                        ocnAntiLagOn = true
                        pcall(function()
                            LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = true
                        end)
                    elseif fps > ocnFpsThreshold and ocnAntiLagOn then
                        ocnAntiLagOn = false
                        pcall(function()
                            LocalPlayer.PlayerScripts.CharacterAndBeamMove.Disabled = false
                        end)
                    end
                end
            end
        end
        task.wait()
    end
end)


DefK = Tabs.Defense:AddRightGroupbox("Anti Kicks(I RECOMMEND USING THIRD PERSON)", "turkish-lira")

DefK:AddCheckbox("OatAntiKick", {
    Text = "Oat Anti-Kick(Break pcld)",
    Default = false,
    Callback = function(Value)
        hkExpectDeath = false
        hkSalmonList = {}
        hkSalmonList[LocalPlayer.UserId] = true
        
        local function hkApplySalmon(char)
            if not char then return end
            newHum = char:WaitForChild("Humanoid", 5)
            if not newHum then return end
            if hkSalmonList[LocalPlayer.UserId] and not hkExpectDeath then
                hkExpectDeath = true
                newHum:ChangeState(Enum.HumanoidStateType.Dead)
            else
                hkExpectDeath = false
            end
        end
        
        LocalPlayer.CharacterAdded:Connect(function(char)
            hkApplySalmon(char)
        end)
        
        hkSalmonList[LocalPlayer.UserId] = Value and true or nil
        if Value then
            hkExpectDeath = false
            char = LocalPlayer.Character
            hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then hum.Health = 0 end
        end
    end
})

DefK:AddButton({
    Text = "Delete Legs",
    Callback = function()
        pcall(function()
            char = LocalPlayer.Character
            if not char then return end
            
            if char:FindFirstChild("Left Leg") and char:FindFirstChild("Right Leg") then
                ll = char:FindFirstChild("Left Leg")
                rl = char:FindFirstChild("Right Leg")
                void = workspace.FallenPartsDestroyHeight
                torso = char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
                
                if not torso then return end
                
                pos = torso.CFrame
                hrp = char:FindFirstChild("HumanoidRootPart")
                hum = char:FindFirstChild("Humanoid")
                
                if not hrp or not hum then return end
                
                workspace.FallenPartsDestroyHeight = -100
                game:GetService("ReplicatedStorage").CharacterEvents.RagdollRemote:FireServer(hrp, 2)
                task.wait(0.5)
                
                rl.CFrame = CFrame.new(0, -10000, 0)
                ll.CFrame = CFrame.new(0, -10000, 0)
                task.wait(0.3)
                
                torso.CFrame = CFrame.new(0, -9970, 0)
                task.wait(0.5)
                
                torso.CFrame = pos
                task.wait(0.5)
                workspace.FallenPartsDestroyHeight = void
                
                task.spawn(function()
                    if not char:FindFirstChild("Left Leg") and not char:FindFirstChild("Right Leg") then
                        while char and char.Parent and hum and hum.Health > 0 do
                            pcall(function()
                                controls = LocalPlayer.PlayerGui:FindFirstChild("ControlsGui")
                                if controls and controls:FindFirstChild("PCFrame") and controls.PCFrame:FindFirstChild("Stand") then
                                    if controls.PCFrame.Stand.Visible == false then
                                        hum.HipHeight = 2
                                    else
                                        hum.HipHeight = 0
                                    end
                                end
                            end)
                            task.wait()
                        end
                    end
                end)
            end
        end)
    end
})

do
    shurikenAntiKickActive = false
    shurikenAntiKickTask = nil
    shurikenCharFixConnection = nil
    shurikenRespawnConnection = nil

    local ShurikenToyList = {
        ["Shuriken"] = "NinjaShuriken",
        ["Pickaxe"] = "ToolPickaxe",
        ["Kunai"] = "NinjaKunai",
        ["Cleaver"] = "ToolCleaver",
    }

    local ShurikenDropdownValues = {}
    for shortName, _ in pairs(ShurikenToyList) do
        table.insert(ShurikenDropdownValues, shortName)
    end
    table.sort(ShurikenDropdownValues)

    local SelectedShurikenToy = ShurikenToyList["Shuriken"] or ShurikenToyList[ShurikenDropdownValues[1]]

    DefK:AddDropdown("ShurikenItemSelect", {
        Text = "Select Anti Kick",
        Values = ShurikenDropdownValues,
        Default = "Shuriken",
        Callback = function(Value)
            SelectedShurikenToy = ShurikenToyList[Value]
        end
    })

    local function fixShurikenCharacter(char)
        if not char then return end
        local hum = char:FindFirstChild("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hum and hrp then
            hum.AutoRotate = true
            hum.Sit = false
            hrp.Velocity = Vector3.zero
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            hrp.CanCollide = true
            hrp.CanTouch = true
            hrp.CanQuery = true
            for _, part in pairs(char:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                    part.CanTouch = true
                    part.CanQuery = true
                    part.Velocity = Vector3.zero
                    part.AssemblyLinearVelocity = Vector3.zero
                    part.AssemblyAngularVelocity = Vector3.zero
                end
            end
            hum:ChangeState(Enum.HumanoidStateType.Running)
        end
    end

    local function ClearKunai()
        local inv = workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
        local destroyrem = game:GetService("ReplicatedStorage"):FindFirstChild("MenuToys") and game:GetService("ReplicatedStorage").MenuToys:FindFirstChild("DestroyToy")
        if inv and destroyrem then
            for _, v in pairs(inv:GetChildren()) do
                if v.Name == "AntiKick" or v.Name == SelectedShurikenToy then
                    pcall(function() destroyrem:FireServer(v) end)
                end
            end
        end
    end

    DefK:AddCheckbox("ShurikenAntiKick", {
        Text = "Anti Kick",
        Default = false,
        Callback = function(Value)
            shurikenAntiKickActive = Value
            
            if Value then
                fixShurikenCharacter(LocalPlayer.Character)
                
                if shurikenCharFixConnection then shurikenCharFixConnection:Disconnect() end
                shurikenCharFixConnection = LocalPlayer.CharacterAdded:Connect(function(char)
                    task.wait(0.5)
                    fixShurikenCharacter(char)
                end)

                -- Auto respawn shuriken on character respawn
                if shurikenRespawnConnection then shurikenRespawnConnection:Disconnect() end
                shurikenRespawnConnection = LocalPlayer.CharacterAdded:Connect(function()
                    task.wait(1)
                    if shurikenAntiKickActive then
                        -- Clear old and spawn new
                        ClearKunai()
                    end
                end)
                
                shurikenAntiKickTask = task.spawn(function()
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
                            if obj:IsA("BasePart") then
                                obj.CanTouch = false
                                obj.CanCollide = false
                                obj.CanQuery = false
                                obj.Transparency = 0.8
                            end
                        end
                        if kunai:FindFirstChild("Handle") then
                            local handle = kunai.Handle
                            if not handle:FindFirstChild("Highlight") then
                                local high = Instance.new("Highlight", handle)
                                high.FillColor = Color3.fromRGB(0, 255, 255)
                            end
                        end
                    end
                    
                    local function SpawnToy(name)
                        local t = tick()
                        while not canSpawn.Value do
                            if not shurikenAntiKickActive or tick() - t > 5 then return nil end
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
                    
                    while shurikenAntiKickActive do 
                        task.wait(0.005)
                        if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then 
                            task.wait(0.5)
                            ClearKunai()
                            return
                        end
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local kunai = inv and inv:FindFirstChild(SelectedShurikenToy)
                        
                        if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then 
                            local boolik, house = CheckForHome()
                            if boolik and house and workspace.Plots:FindFirstChild(house.Name) then
                                local sign = workspace.Plots[house.Name]:FindFirstChild("PlotSign")
                                if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then 
                                    kunai = SpawnToy(SelectedShurikenToy)
                                    if kunai == nil then return end
                                    kunai.Name = "AntiKick" 
                                    StickKunai(kunai)
                                end
                            end
                        end
                        
                        if not kunai then
                            if workspace.PlotItems.PlayersInPlots:FindFirstChild(plr.Name) then return end 
                            kunai = SpawnToy(SelectedShurikenToy)
                            if kunai == nil then return end 
                            kunai.Name = "AntiKick"
                            if not kunai then return end 
                        end
                        
                        repeat
                            if kunai and kunai:FindFirstChild("StickyPart") and kunai.StickyPart.CanTouch == true then
                                StickKunai(kunai)
                                kunai.Name = "AntiKick"
                            end
                            task.wait(0.3)
                        until not kunai or not shurikenAntiKickActive or not kunai:FindFirstChild("StickyPart") or kunai.StickyPart.CanTouch == false 
                            or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") 
                            or not kunai:FindFirstChild("StickyPart") 
                            or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20
                        
                        if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then 
                            ClearKunai()
                        end 
                        
                        pcall(function()
                            repeat
                                task.wait(0.05)
                            until not shurikenAntiKickActive or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai:FindFirstChild("StickyPart") or not kunai.StickyPart:FindFirstChild("StickyWeld") or not kunai.StickyPart.StickyWeld.Part1
                            if not kunai or not kunai:FindFirstChild("StickyPart") or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) or not kunai["StickyPart"]:FindFirstChild("StickyWeld").Part1 then 
                                ClearKunai()
                            end
                        end)
                    end
                    ClearKunai()
                end)
            else
                shurikenAntiKickActive = false
                if shurikenAntiKickTask then
                    task.cancel(shurikenAntiKickTask)
                    shurikenAntiKickTask = nil
                end
                if shurikenCharFixConnection then
                    shurikenCharFixConnection:Disconnect()
                    shurikenCharFixConnection = nil
                end
                if shurikenRespawnConnection then
                    shurikenRespawnConnection:Disconnect()
                    shurikenRespawnConnection = nil
                end
                
                fixShurikenCharacter(LocalPlayer.Character)
                ClearKunai()
            end
        end
    })
end

do
    local pencilAntiKickActive = false
    local pencilAntiKickTask = nil
    local pencilRespawnConnection = nil

    local function spawnPencil()
        local spawnFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if not spawnFolder then return end
        
        local pencil = spawnFolder:FindFirstChild("ToolPencil")
        if pencil then return pencil end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            pcall(function()
                game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction:InvokeServer(
                    "ToolPencil",
                    CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15),
                    Vector3.new(0, 0, 0)
                )
            end)
        end
        return nil
    end

    local function fixPencil()
        pcall(function()
            local playerName = LocalPlayer.Name
            local spawnFolder = workspace:FindFirstChild(playerName .. "SpawnedInToys")
            
            if not spawnFolder then return end
            
            local pencil = spawnFolder:FindFirstChild("ToolPencil")
            
            if not pencil then
                pencil = spawnPencil()
                if not pencil then return end
            end
            
            local char = LocalPlayer.Character
            if not char then return end
            
            local torso = char:FindFirstChild("Torso")
            local root = char:FindFirstChild("HumanoidRootPart")
            if not (torso and root) then return end
            
            local stickyPart = pencil:FindFirstChild("StickyPart")
            local soundPart = pencil:FindFirstChild("SoundPart")
            
            if stickyPart and stickyPart:FindFirstChild("StickyWeld") then
                local weld = stickyPart.StickyWeld
                
                if weld.Part1 ~= torso then
                    local a = soundPart and soundPart.CFrame.Position or Vector3.zero
                    local b = root.CFrame.Position
                    local dist = (a - b).Magnitude
                    
                    if dist > 20 then
                        pcall(function()
                            game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(pencil)
                        end)
                    else
                        pcall(function()
                            game:GetService("ReplicatedStorage").PlayerEvents.StickyPartEvent:FireServer(
                                stickyPart,
                                torso,
                                CFrame.new(0, -1, 0) * CFrame.Angles(0, math.pi, 0)
                            )
                        end)
                        
                        for _, prt in pairs(pencil:GetChildren()) do
                            if prt:IsA("BasePart") then
                                prt.CanQuery = false
                                prt.CanCollide = false
                                prt.CanTouch = false
                            end
                        end
                    end
                end
            end
        end)
    end

    DefK:AddCheckbox("AntiKickPencil", {
        Text = "Anti Kick (Pencil)",
        Default = false,
        Callback = function(Value)
            pencilAntiKickActive = Value
            
            if Value then
                -- Auto respawn pencil on character respawn
                if pencilRespawnConnection then pencilRespawnConnection:Disconnect() end
                pencilRespawnConnection = LocalPlayer.CharacterAdded:Connect(function()
                    task.wait(1)
                    if pencilAntiKickActive then
                        fixPencil()
                    end
                end)
                
                pencilAntiKickTask = task.spawn(function()
                    while pencilAntiKickActive do
                        fixPencil()
                        task.wait(0.5)
                    end
                end)
            else
                pencilAntiKickActive = false
                if pencilAntiKickTask then
                    task.cancel(pencilAntiKickTask)
                    pencilAntiKickTask = nil
                end
                if pencilRespawnConnection then
                    pencilRespawnConnection:Disconnect()
                    pencilRespawnConnection = nil
                end
                
                local spawnFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                if spawnFolder then
                    local pencil = spawnFolder:FindFirstChild("ToolPencil")
                    if pencil then
                        pcall(function()
                            game:GetService("ReplicatedStorage").MenuToys.DestroyToy:FireServer(pencil)
                        end)
                    end
                end
            end
        end
    })
end

do
    Players = game:GetService("Players")
    RS = game:GetService("ReplicatedStorage")
    RunService = game:GetService("RunService")
    plr = Players.LocalPlayer

    AntiKickItemActive = false
    MyPCLD = nil
    pcldConn = nil
    charFixConnection = nil

    local ToyList = {
        ["Japanese Lantern"] = "JapaneseLantern",
        ["Spray Can"]        = "SprayCanWD",
        ["Spooky Candle"]    = "SpookyCandle1",
    }

    local DropdownValues = {}
    for shortName, _ in pairs(ToyList) do
        table.insert(DropdownValues, shortName)
    end
    table.sort(DropdownValues)

    local SelectedToy = ToyList["SpookyCandle1"] or ToyList[DropdownValues[1]]

    DefK:AddDropdown("AntiKickItemSelect", {
        Text = "Anti Kick Item",
        Values = DropdownValues,
        Default = "Spooky Candle",
        Callback = function(Value)
            SelectedToy = ToyList[Value]
        end
    })

    local function GetMagnitude(Part1, Part2)
        return (Part1.Position - Part2.Position).Magnitude
    end

    local function FWD(parent, part, timeOffset)
        return parent:FindFirstChild(part) or parent:WaitForChild(part, timeOffset or 1)
    end

    local function CheckNetworkOwnerOnPart(Part) 
        local po = Part:FindFirstChild("PartOwner")
        return po and po.Value == plr.Name
    end

    local function sno(part)
        pcall(function()
            local grabEvents = RS:FindFirstChild("GrabEvents")
            local setNetOwner = grabEvents and grabEvents:FindFirstChild("SetNetworkOwner")
            if setNetOwner then
                setNetOwner:FireServer(part, part.CFrame)
            end
        end)
    end

    local function CheckForHome()
        local plotItems = workspace:FindFirstChild("PlotItems")
        local plots = workspace:FindFirstChild("Plots")
        
        if plots and plotItems then
            for i = 1, 5 do 
                local Plot = plots:FindFirstChild("Plot"..i)
                if Plot then
                    local sign = Plot:FindFirstChild("PlotSign")
                    local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
                    if owners then
                        for _,v in pairs(owners:GetChildren()) do 
                            if v.Value == plr.Name then 
                                return plotItems:FindFirstChild("Plot"..i)
                            end
                        end
                    end
                end
            end
        end
        return nil
    end

    local function SpawnToy(ToyName)
        local InPlot = plr:FindFirstChild("InPlot")
        local InOwnedPlot = plr:FindFirstChild("InOwnedPlot")
        local CanSpawnToy = plr:FindFirstChild("CanSpawnToy")
        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")

        if InPlot and InPlot.Value and InOwnedPlot and not InOwnedPlot.Value then 
            InPlot:GetPropertyChangedSignal("Value"):Wait()
        end 
        if CanSpawnToy and not CanSpawnToy.Value then 
            CanSpawnToy:GetPropertyChangedSignal("Value"):Wait()
        end

        local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return nil end

        local SpawnCF = (MyPCLD or hrp).CFrame * CFrame.new(0, 14, 20)
        local Container = (InOwnedPlot and InOwnedPlot.Value) and CheckForHome() or inv
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
                local menuToys = RS:FindFirstChild("MenuToys")
                local spawnRemote = menuToys and menuToys:FindFirstChild("SpawnToyRemoteFunction")
                if spawnRemote then
                    spawnRemote:InvokeServer(ToyName, SpawnCF, Vector3.zero)
                end
            end)
        end)

        local start = tick()
        repeat task.wait() until spawnedObject or (tick() - start) > 2.5

        if connection then connection:Disconnect() end
        return spawnedObject
    end

    local function FindPCLD(hrp)
        if pcldConn then pcldConn:Disconnect() end
        MyPCLD = nil
        pcldConn = RunService.Heartbeat:Connect(function()
            if MyPCLD or not hrp or not hrp.Parent then 
                if pcldConn then pcldConn:Disconnect() pcldConn = nil end
                return
            end
            for _, v in pairs(workspace:GetChildren()) do 
                if v.Name == "PlayerCharacterLocationDetector" and v:IsA("BasePart") then
                    if GetMagnitude(v, hrp) <= 2 then 
                        MyPCLD = v
                        break
                    end
                end
            end
        end)
    end

    local function fixCharacter(char)
        if not char then return end
        local hum = char:FindFirstChild("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hum and hrp then
            hum.AutoRotate = true
            hum.Sit = false
            hrp.Velocity = Vector3.zero
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            hrp.CanCollide = true
            hrp.CanTouch = true
            hrp.CanQuery = true
            for _, part in pairs(char:GetChildren()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                    part.CanTouch = true
                    part.CanQuery = true
                    part.Velocity = Vector3.zero
                    part.AssemblyLinearVelocity = Vector3.zero
                    part.AssemblyAngularVelocity = Vector3.zero
                end
            end
            hum:ChangeState(Enum.HumanoidStateType.Running)
        end
    end

    DefK:AddCheckbox("AntiKickItem", {
        Text = "Anti Kick [ITEM]",
        Default = false,
        Callback = function(Val)
            AntiKickItemActive = Val 
            
            if Val then
                fixCharacter(plr.Character)
                
                if charFixConnection then charFixConnection:Disconnect() end
                charFixConnection = plr.CharacterAdded:Connect(function(char)
                    task.wait(0.5)
                    fixCharacter(char)
                end)
                
                task.spawn(function()
                    local Item, SoundPart
                    while AntiKickItemActive and task.wait() do 
                        local char = plr.Character
                        local hrp = char and char:FindFirstChild("HumanoidRootPart")
                        local hum = char and char:FindFirstChild("Humanoid")
                        local inPlot = plr:FindFirstChild("InPlot")
                        local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                        local destroyToy = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
                        
                        if not hrp or not hum or hum.Health <= 0 or not inv then continue end  
                        if inPlot and inPlot.Value then continue end 
                        
                        if not MyPCLD and not pcldConn then
                            FindPCLD(hrp)
                        end

                        Item = inv:FindFirstChild("AntiKickItem") 
                        SoundPart = Item and Item:FindFirstChild("Hitbox")
                        
                        if not Item or not SoundPart then
                            for _,v in pairs(inv:GetChildren()) do 
                                if v.Name == "AntiKickItem" then 
                                    pcall(function() destroyToy:FireServer(v) end)
                                end
                            end
                            
                            Item = SpawnToy(SelectedToy)
                            if not Item then continue end 
                            
                            SoundPart = Item and FWD(Item, "Hitbox", 0.5)
                            if SoundPart then sno(SoundPart) end
                            
                            for _,v in pairs(Item:GetChildren()) do 
                                if v:IsA("BasePart") then 
                                    v.CanCollide = false 
                                    v.Transparency = 0.8
                                    v.Color = Color3.fromRGB(0, 255, 255)
                                end
                            end
                            
                            Item.Name = "AntiKickItem"
                        end
                        
                        if SoundPart and not CheckNetworkOwnerOnPart(SoundPart) then 
                            sno(SoundPart)
                        end
                        
                        local targetPart = MyPCLD or hrp:FindFirstChild("FirePlayerPart") or hrp
                        
                        if SoundPart and targetPart then
                            SoundPart.CFrame = targetPart.CFrame
                            SoundPart.AssemblyLinearVelocity = Vector3.zero
                            SoundPart.AssemblyAngularVelocity = Vector3.zero
                        end
                    end
                end)
            else
                if pcldConn then pcldConn:Disconnect() pcldConn = nil end
                if charFixConnection then
                    charFixConnection:Disconnect()
                    charFixConnection = nil
                end
                MyPCLD = nil
                
                fixCharacter(plr.Character)
                
                task.spawn(function()
                    local inv = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                    local destroyToy = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
                    if inv and destroyToy then
                        for _,v in pairs(inv:GetChildren()) do 
                            if v.Name == "AntiKickItem" then 
                                pcall(function() destroyToy:FireServer(v) end)
                            end
                        end
                    end
                end)
            end
        end
    })

    plr.CharacterAdded:Connect(function(char)
        if AntiKickItemActive then
            MyPCLD = nil
            local hrp = char:WaitForChild("HumanoidRootPart", 5)
            if hrp then FindPCLD(hrp) end
            task.wait(0.5)
            fixCharacter(char)
        end
    end)
end

DefR = Tabs.Defense:AddRightGroupbox("Extra Defense", "heart-plus")

DefK:AddDivider()

do
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
        ["Lyre"] = "InstrumentGuitarLyre",
    }

    local DropdownValues = {}
    for shortName, _ in pairs(ToyList) do
        table.insert(DropdownValues, shortName)
    end
    table.sort(DropdownValues)

    local SelectedToy = ToyList["Burger"]
    local instantLagActive = false
    local instantLagTask = nil
    local respawnConnection = nil

    DefR:AddDropdown("InputLagToySelect", {
        Text = "Select Input Lag Toy",
        Values = DropdownValues,
        Default = "Burger",
        Callback = function(Value)
            SelectedToy = ToyList[Value]
        end
    })

    DefR:AddCheckbox("AntiInputLag", {
        Text = "Anti Input Lag",
        Default = false,
        Callback = function(Value)
            instantLagActive = Value
            
            if Value then
                -- Auto respawn toy on character respawn
                if respawnConnection then respawnConnection:Disconnect() end
                respawnConnection = LocalPlayer.CharacterAdded:Connect(function()
                    task.wait(1)
                    if instantLagActive then
                        -- Clear old toy and it will respawn in loop
                    end
                end)
                
                instantLagTask = task.spawn(function()
                    local plr = LocalPlayer
                    local SpawnRemote = game:GetService("ReplicatedStorage").MenuToys.SpawnToyRemoteFunction

                    while instantLagActive do
                        pcall(function()
                            local char = plr.Character
                            local hrp = char and char:FindFirstChild("HumanoidRootPart")
                            
                            if hrp then
                                local toysFolder = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                                local name = SelectedToy 
                                local item = toysFolder and toysFolder:FindFirstChild(name)

                                -- Check if toy exists and is valid
                                local toyExists = item and item.Parent ~= nil
                                
                                -- If toy doesn't exist, spawn it
                                if not toyExists then
                                    task.spawn(function()
                                        pcall(function()
                                            SpawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, -12, 0), Vector3.zero)
                                        end)
                                    end)
                                    task.wait(0.1)
                                    -- Re-fetch item after spawn
                                    toysFolder = workspace:FindFirstChild(plr.Name.."SpawnedInToys")
                                    item = toysFolder and toysFolder:FindFirstChild(name)
                                end
                                
                                -- If toy exists, use it
                                if item and item.Parent then
                                    local holdPart = item:FindFirstChild("HoldPart")
                                    if holdPart then
                                        for _, v in pairs(item:GetDescendants()) do
                                            if v:IsA("BasePart") then 
                                                v.CanCollide = false 
                                                v.Massless = true 
                                            end
                                        end

                                        task.spawn(function()
                                            pcall(function()
                                                holdPart.HoldItemRemoteFunction:InvokeServer(item, char)
                                            end)
                                        end)

                                        task.wait(0.02)

                                        task.spawn(function()
                                            pcall(function()
                                                holdPart.DropItemRemoteFunction:InvokeServer(
                                                    item, 
                                                    CFrame.new(0, 5000, 0), 
                                                    Vector3.zero
                                                )
                                            end)
                                        end)
                                    end
                                end
                            end
                        end)
                        task.wait(0.02)
                    end
                end)
            else
                if instantLagTask then
                    task.cancel(instantLagTask)
                    instantLagTask = nil
                end
                if respawnConnection then
                    respawnConnection:Disconnect()
                    respawnConnection = nil
                end
            end
        end
    })
end

DefR:AddDivider()

ocnKakuConn = nil
ocnKakuAng = 0

DefR:AddCheckbox("AntiBlobmanKill", {
    Text = "Anti blobman kill",
    Default = false,
    Callback = function(Value)
        if Value then
            ocnKakuConn = game:GetService("RunService").RenderStepped:Connect(function(dt)
                pcall(function()
                    c = LocalPlayer.Character
                    root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        ocnKakuAng = ocnKakuAng + dt * 9999
                        rad = math.rad(ocnKakuAng)
                        root.CFrame = CFrame.new(math.cos(rad) * 50000, -100000, math.sin(rad) * 50000)
                    end
                end)
            end)
        else
            if ocnKakuConn then
                ocnKakuConn:Disconnect()
                ocnKakuConn = nil
            end
            ocnKakuAng = 0
        end
    end
})

ocnGroovConn = nil
ocnGroovPos = nil

DefR:AddCheckbox("PosLock", {
    Text = "Pos lock",
    Default = false,
    Callback = function(Value)
        if Value then
            pcall(function()
                c = LocalPlayer.Character
                root = c and c:FindFirstChild("HumanoidRootPart")
                if root then
                    ocnGroovPos = root.CFrame
                end
                ocnGroovConn = game:GetService("RunService").RenderStepped:Connect(function()
                    c2 = LocalPlayer.Character
                    root2 = c2 and c2:FindFirstChild("HumanoidRootPart")
                    if root2 and ocnGroovPos then
                        assembly = root2.AssemblyRootPart or root2
                        assembly.AssemblyLinearVelocity = Vector3.zero
                        assembly.AssemblyAngularVelocity = Vector3.zero
                        offset = assembly.CFrame:ToObjectSpace(root2.CFrame)
                        assembly.CFrame = ocnGroovPos * offset:Inverse()
                    end
                end)
            end)
        else
            if ocnGroovConn then
                ocnGroovConn:Disconnect()
                ocnGroovConn = nil
            end
            pcall(function()
                c2 = LocalPlayer.Character
                root2 = c2 and c2:FindFirstChild("HumanoidRootPart")
                if root2 then
                    assembly = root2.AssemblyRootPart or root2
                    assembly.AssemblyLinearVelocity = Vector3.zero
                    assembly.AssemblyAngularVelocity = Vector3.zero
                end
            end)
            ocnGroovPos = nil
        end
    end
})

ocnStasisConn = nil

DefR:AddCheckbox("AntiLoopKill", {
    Text = "Anti loop kill",
    Default = false,
    Callback = function(Value)
        if Value then
            ocnStasisConn = game:GetService("RunService").RenderStepped:Connect(function()
                pcall(function()
                    c = LocalPlayer.Character
                    root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        root.CFrame = CFrame.new(280, -4, 465)
                    end
                end)
            end)
        else
            if ocnStasisConn then
                ocnStasisConn:Disconnect()
                ocnStasisConn = nil
            end
        end
    end
})

ocnTornadoConn = nil
ocnTornadoAng = 0

DefR:AddCheckbox("LoopTpOp", {
    Text = "Loop Tp(Op)",
    Default = false,
    Callback = function(Value)
        if Value then
            ocnTornadoConn = game:GetService("RunService").RenderStepped:Connect(function(dt)
                pcall(function()
                    c = LocalPlayer.Character
                    root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        ocnTornadoAng = ocnTornadoAng + dt * 50000
                        rad = math.rad(ocnTornadoAng)
                        root.CFrame = CFrame.new(math.cos(rad) * 10000, 0, math.sin(rad) * 10000)
                    end
                end)
            end)
        else
            if ocnTornadoConn then
                ocnTornadoConn:Disconnect()
                ocnTornadoConn = nil
            end
            ocnTornadoAng = 0
        end
    end
})

ocnManiacConn = nil

DefR:AddCheckbox("LoopTp", {
    Text = "Loop Tp",
    Default = false,
    Callback = function(Value)
        if Value then
            ocnManiacConn = game:GetService("RunService").RenderStepped:Connect(function()
                pcall(function()
                    c = LocalPlayer.Character
                    root = c and c:FindFirstChild("HumanoidRootPart")
                    if root then
                        ms = 2000
                        root.CFrame = CFrame.new(
                            math.random(-ms, ms),
                            math.random(-50, 500),
                            math.random(-ms, ms)
                        )
                    end
                end)
            end)
        else
            if ocnManiacConn then
                ocnManiacConn:Disconnect()
                ocnManiacConn = nil
            end
        end
    end
})

defenseEnabled = false
defenseConnection = nil
defenseMode = "Fling"
crazyline = false
crazylineTask = nil

GrabEvents = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
SetNetworkOwner = GrabEvents:WaitForChild("SetNetworkOwner")
DestroyGrabLine = GrabEvents:FindFirstChild("DestroyGrabLine")
CreateGrabEvent = GrabEvents:FindFirstChild("CreateGrabLine")
Debris = game:GetService("Debris")

local function getAttacker()
    char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Head") then
        return nil
    end
    owner = char.Head:FindFirstChild("PartOwner")
    if not owner or not owner:IsA("StringValue") then
        return nil
    end
    return game:GetService("Players"):FindFirstChild(owner.Value)
end

local function performFling(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 90000
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end

local function performKill(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        away = (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Unit
        away = Vector3.new(away.X, 0, away.Z) * 99999999999999
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganFling"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = away
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end

local function performHeaven(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        root.CFrame = CFrame.new(0, 200, 0)
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 200, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end

local function performKick(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        root.CFrame = CFrame.new(0, 999999999999, 0)
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganHeaven"
        bv.MaxForce = Vector3.new(0, math.huge, 0)
        bv.Velocity = Vector3.new(0, 99999999999999, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end

local function performRagdoll(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -20, 0)
        bv.P = 12500
        bv.Parent = root
        Debris:AddItem(bv, 0.01)
    end)
end

local function performHell(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
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
        bv = Instance.new("BodyVelocity")
        bv.Name = "RinneganSpy"
        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bv.Velocity = Vector3.new(0, -100000000, 0)
        bv.P = 12500
        bv.Parent = root
        local noclipConnection
        noclipConnection = game:GetService("RunService").Heartbeat:Connect(function()
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

local function performChina(attacker)
    if not attacker or not attacker.Character then return end
    root = attacker.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    pcall(function()
        SetNetworkOwner:FireServer(root, root.CFrame)
        if DestroyGrabLine then
            DestroyGrabLine:FireServer(root)
        end
        root.CFrame = CFrame.new(591, 153, -101)
    end)
end

local function performSpamGrabLines(attacker)
    while crazyline do
        pcall(function()
            char = LocalPlayer.Character
            if char then
                head = char:FindFirstChild("Head")
                if head then
                    owner = head:FindFirstChild("PartOwner")
                    if owner and owner:IsA("StringValue") then
                        attacker = game:GetService("Players"):FindFirstChild(owner.Value)
                        if attacker and attacker.Character then
                            attackerHead = attacker.Character:FindFirstChild("Head")
                            attackerHRP = attacker.Character:FindFirstChild("HumanoidRootPart")
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
        end)
        task.wait(0.01)
    end
end

local function startDefense()
    if defenseConnection then
        return
    end
    defenseConnection = game:GetService("RunService").Heartbeat:Connect(function()
        if not defenseEnabled then
            return
        end
        attacker = getAttacker()
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
            if not crazylineTask then
                crazyline = true
                crazylineTask = task.spawn(performSpamGrabLines)
            end
        end
    end)
end

local function stopDefense()
    if defenseConnection then
        defenseConnection:Disconnect()
        defenseConnection = nil
    end
    crazyline = false
    if crazylineTask then
        task.cancel(crazylineTask)
        crazylineTask = nil
    end
    for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
        char = plr.Character
        if char then
            for _, obj in ipairs(char:GetDescendants()) do
                if obj:IsA("BodyVelocity") and (obj.Name == "RinneganFling" or obj.Name == "RinneganHeaven" or obj.Name == "RinneganSpy") then
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

DefR:AddCheckbox("RinneganDefense", {
    Text = "Counter Attacks",
    Default = false,
    Callback = function(Value)
        defenseEnabled = Value
        if Value then
            startDefense()
        else
            stopDefense()
        end
    end
})

DefR:AddDropdown("RinneganMode", {
    Text = "Attack Mode",
    Values = {"Fling", "Kill", "Send to Heaven", "Kick", "Ragdoll", "Hell", "China", "GrabLine"},
    Default = "Fling",
    Callback = function(Value)
        defenseMode = Value
    end
})

TgtL = Tabs.Target:AddLeftGroupbox("Target Selector", "crosshair")

SelectedPlayer = nil
lastGrabberDisplay = 'None'
lastGrabberName = nil
_selectedPlayers = {}
_killRoundIndex = 1
_dropdownUpdating = false
_previouslyTargeted = {}
_tracerEnabled = false
_tracerConnections = {}
_tracerParts = {}
_tracerObjects = {}
_isViewing = false

PLOT_NAMES = {
    [1] = 'Blue House',
    [2] = 'Pink House',
    [3] = 'Spooky House',
    [4] = 'Chinese House',
    [5] = 'Green House',
}
PLOT_COLORS = {
    [1] = '#5eb8ff',
    [2] = '#ff85c2',
    [3] = '#b06fff',
    [4] = '#ffcc55',
    [5] = '#55e87a',
}

RunService.Heartbeat:Connect(function()
    if #_selectedPlayers == 0 then
        SelectedPlayer = nil
        return
    end
    if _killRoundIndex > #_selectedPlayers then
        _killRoundIndex = 1
    end
    SelectedPlayer = _selectedPlayers[_killRoundIndex]
    _killRoundIndex = _killRoundIndex % #_selectedPlayers + 1
end)

function getPlayerList()
    list = {}
    for _, plr in ipairs(Players:GetPlayers())do
        if plr ~= LocalPlayer then
            table.insert(list, plr.DisplayName .. ' (@' .. plr.Name .. ')')
        end
    end
    return list
end

function extractUsername(entry)
    return entry and entry:match('@([%w_]+)')
end

function getSelectedPlayer()
    for _, un in ipairs(_selectedPlayers)do
        p = Players:FindFirstChild(un)
        if p then
            return p
        end
    end
    return nil
end

function _syncPreviousTargets()
    for _, un in ipairs(_selectedPlayers)do
        _previouslyTargeted[un] = true
    end
end

function _removeFromTargets(username)
    for i, un in ipairs(_selectedPlayers)do
        if un == username then
            table.remove(_selectedPlayers, i)
            break
        end
    end
end

function _refreshDropdown()
    _dropdownUpdating = true
    PlayerDropdown:SetValues(getPlayerList())
    _dropdownUpdating = false
end

function getPlayerPlot(player)
    plotsFolder = workspace:FindFirstChild('Plots') or workspace:FindFirstChild('plots')
    if not plotsFolder then
        return nil, nil
    end
    for i = 1, 5 do
        plot = plotsFolder:FindFirstChild('Plot' .. i)
        if plot then
            for _, desc in ipairs(plot:GetDescendants())do
                vt = desc.ClassName
                if vt == 'StringValue' or vt == 'ObjectValue' or vt == 'IntValue' then
                    ok, v = pcall(function()
                        return tostring(desc.Value)
                    end)
                    if ok and v and v:find(player.Name, 1, true) then
                        return plot, i
                    end
                end
            end
        end
    end
    return nil, nil
end

function targetInPlot(player)
    if not player or not player.Character then
        return false
    end
    hrp = player.Character:FindFirstChild('HumanoidRootPart')
    plot = getPlayerPlot(player)
    if not hrp or not plot then
        return false
    end
    base = plot:FindFirstChild('Base') or plot:FindFirstChildWhichIsA('BasePart')
    if not base then
        return false
    end
    pos = base.CFrame:PointToObjectSpace(hrp.Position)
    half = base.Size / 2
    return math.abs(pos.X) <= half.X and math.abs(pos.Z) <= half.Z and math.abs(pos.Y) <= (half.Y + 25)
end

function getPlotRichText(player)
    plot, idx = getPlayerPlot(player)
    if not plot or not idx then
        return "<font color='#555555'>None</font>"
    end
    name = PLOT_NAMES[idx] or ('Plot ' .. idx)
    col = PLOT_COLORS[idx] or '#ffffff'
    inside = targetInPlot(player)
    tag = inside and "  <font color='#55e87a'>[Inside]</font>" or "  <font color='#ffaa33'>[Nearby]</font>"
    return "<font color='" .. col .. "'><b>" .. name .. '</b></font>' .. tag
end

_currentAvatarUserId = nil

function _updateAvatarDisplay()
    primary = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])
    if not primary then
        _currentAvatarUserId = nil
        pcall(function()
            AvatarImage:SetImage('rbxassetid://0')
        end)
        return
    end
    uid = primary.UserId
    if uid == _currentAvatarUserId then
        return
    end
    _currentAvatarUserId = uid
    task.spawn(function()
        ok, content = pcall(Players.GetUserThumbnailAsync, Players, uid, Enum.ThumbnailType.AvatarBust, Enum.ThumbnailSize.Size420x420)
        if ok and content and content ~= '' then
            pcall(function()
                AvatarImage:SetImage(content)
            end)
        end
    end)
end

_tracerObjects = {}

function _cleanupTracers()
    for _, c in ipairs(_tracerConnections)do
        pcall(function()
            c:Disconnect()
        end)
    end
    for _, obj in ipairs(_tracerObjects)do
        pcall(function()
            obj.line:Remove()
        end)
        pcall(function()
            if obj.shadow then
                obj.shadow:Remove()
            end
        end)
        pcall(function()
            if obj.dot then
                obj.dot:Remove()
            end
        end)
        pcall(function()
            obj.highlight:Destroy()
        end)
    end
    _tracerConnections = {}
    _tracerObjects = {}
    for _, p in ipairs(_tracerParts)do
        pcall(function()
            p:Destroy()
        end)
    end
    _tracerParts = {}
end

function _buildTracerFor(plr)
    if not plr or not plr.Character then
        return
    end
    char = plr.Character
    hrp = char:FindFirstChild('HumanoidRootPart')
    if not hrp then
        return
    end
    hl = Instance.new('Highlight')
    hl.Name = '_PhantHL_' .. plr.Name
    hl.Adornee = char
    hl.OutlineColor = Color3.fromRGB(255, 255, 255)
    hl.OutlineTransparency = 0
    hl.FillColor = Color3.fromRGB(255, 255, 255)
    hl.FillTransparency = 1
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    hl.Parent = char
    shadow = Drawing.new('Line')
    shadow.Thickness = 3
    shadow.Color = Color3.fromRGB(20, 20, 20)
    shadow.Transparency = 0.55
    shadow.Visible = false
    core = Drawing.new('Line')
    core.Thickness = 1.5
    core.Color = Color3.fromRGB(255, 255, 255)
    core.Transparency = 1
    core.Visible = false
    dot = Drawing.new('Circle')
    dot.Radius = 3
    dot.Color = Color3.fromRGB(255, 80, 80)
    dot.Thickness = 1.5
    dot.Filled = true
    dot.Transparency = 1
    dot.Visible = false
    camera = workspace.CurrentCamera

    function getOrigin()
        vp = camera.ViewportSize
        return Vector2.new(vp.X * 0.5, vp.Y)
    end

    conn = RunService.RenderStepped:Connect(function()
        if not _tracerEnabled then
            return
        end
        if not hrp or not hrp.Parent then
            core.Visible = false
            shadow.Visible = false
            dot.Visible = false
            return
        end
        origin = getOrigin()
        cam = camera
        toTarget = hrp.Position - cam.CFrame.Position
        lookVec = cam.CFrame.LookVector
        inFront = toTarget:Dot(lookVec) > 0
        local tipPos
        if inFront then
            screenPos, onScreen = cam:WorldToViewportPoint(hrp.Position)
            if onScreen then
                tipPos = Vector2.new(screenPos.X, screenPos.Y)
            else
                vp = cam.ViewportSize
                raw = Vector2.new(screenPos.X, screenPos.Y)
                tipPos = Vector2.new(math.clamp(raw.X, 0, vp.X), math.clamp(raw.Y, 0, vp.Y))
            end
        else
            vp = cam.ViewportSize
            centre = Vector2.new(vp.X * 0.5, vp.Y * 0.5)
            rightVec = cam.CFrame.RightVector
            upVec = cam.CFrame.UpVector
            sx = toTarget:Dot(rightVec)
            sy = -toTarget:Dot(upVec)
            dir2 = Vector2.new(sx, sy)
            if dir2.Magnitude > 0 then
                dir2 = dir2.Unit
            end
            edgeX = sx > 0 and (vp.X - 10) or 10
            edgeY = math.clamp(centre.Y + dir2.Y * vp.Y * 0.4, 10, vp.Y - 10)
            tipPos = Vector2.new(edgeX, edgeY)
        end
        core.From = origin
        core.To = tipPos
        core.Visible = true
        shadow.From = origin
        shadow.To = tipPos
        shadow.Visible = true
        dot.Position = tipPos
        dot.Visible = true
        if hl.Adornee ~= plr.Character then
            hl.Adornee = plr.Character
        end
    end)

    table.insert(_tracerConnections, conn)
    table.insert(_tracerObjects, {
        line = core,
        shadow = shadow,
        dot = dot,
        highlight = hl,
        conn = conn,
        username = plr.Name,
    })
end

function _rebuildTracers()
    _cleanupTracers()
    if not _tracerEnabled then
        return
    end
    for _, un in ipairs(_selectedPlayers)do
        plr = Players:FindFirstChild(un)
        if plr then
            _buildTracerFor(plr)
        end
    end
end

function setTargetsFromTable(selectedMap, source)
    _selectedPlayers = {}
    for entry, state in pairs(selectedMap)do
        if state then
            un = extractUsername(entry)
            if un then
                plr = Players:FindFirstChild(un)
                if plr and plr ~= LocalPlayer then
                    table.insert(_selectedPlayers, un)
                end
            end
        end
    end
    _killRoundIndex = 1
    _syncPreviousTargets()
    first = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])
    if first then
        pcall(function()
            BlobmanTarget:SetValue(first.DisplayName .. ' (' .. first.Name .. ')')
        end)
        pcall(function()
            GrabTarget:SetValue(first.DisplayName .. ' (' .. first.Name .. ')')
        end)
    end
    count = #_selectedPlayers
    src = source and (' [' .. source .. ']') or ''
    if count == 0 then
        Library:Notify({Title = "Target", Description = "No targets selected", Duration = 2})
    elseif count == 1 then
        p = Players:FindFirstChild(_selectedPlayers[1])
        Library:Notify({Title = "Target set" .. src, Description = p and (p.DisplayName .. ' (@' .. p.Name .. ')') or _selectedPlayers[1], Duration = 4})
    else
        Library:Notify({Title = "Target set" .. src, Description = count .. ' players targeted', Duration = 4})
    end
    _updateAvatarDisplay()
    if _tracerEnabled then
        _rebuildTracers()
    end
end

function addTarget(plr, sourceTag)
    if plr == LocalPlayer then
        return
    end
    for _, un in ipairs(_selectedPlayers)do
        if un == plr.Name then
            Library:Notify({Title = "Target", Description = plr.DisplayName .. ' is already selected in your dropdown.', Duration = 2})
            return
        end
    end
    table.insert(_selectedPlayers, plr.Name)
    _previouslyTargeted[plr.Name] = true
    _dropdownUpdating = true
    pcall(function()
        PlayerDropdown:SetValue(plr.DisplayName .. ' (@' .. plr.Name .. ')')
    end)
    _dropdownUpdating = false
    Library:Notify({Title = "Target added", Description = plr.DisplayName .. ' (@' .. plr.Name .. ')', Duration = 4})
    _updateAvatarDisplay()
    if _tracerEnabled then
        _rebuildTracers()
    end
end

TgtL:AddDivider()

AvatarImage = TgtL:AddImage("AvatarDisplay", {
    Image = 'rbxassetid://0',
})

TgtL:AddDivider()

SelectedLabel = TgtL:AddLabel("<b><font color='#aaaaaa'>Selected</font></b> <font color='#888888'>None</font>")
TargetDistLabel = TgtL:AddLabel("<b><font color='#aaaaaa'>Distance</font></b>  <font color='#555555'>\u{2014}</font>")
LastGrabberLabel = TgtL:AddLabel("<b><font color='#aaaaaa'>Last Grabber</font></b> <font color='#888888'>None</font>")

TgtL:AddDivider()

PlayerDropdown = TgtL:AddDropdown("PlayerDropdown", {
    Text = "Select A Target",
    Values = getPlayerList(),
    Default = {},
    Multi = true,
    Callback = function(v)
        if _dropdownUpdating then
            return
        end
        setTargetsFromTable(v, 'Dropdown')
    end
})

TgtL:AddDivider()

local ViewButton

ViewButton = TgtL:AddButton({
    Text = "View Target",
    Func = function()
        if _isViewing then
            char = LocalPlayer.Character
            hum = char and char:FindFirstChildOfClass('Humanoid')
            if hum then
                workspace.CurrentCamera.CameraSubject = hum
            end
            workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
            _isViewing = false
            ViewButton:SetText("View Target")
            Library:Notify({Title = "View", Description = "Camera restored", Duration = 2})
        else
            plr = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])
            if not plr or not plr.Character then
                return Library:Notify({Title = "View", Description = "No valid target", Duration = 2})
            end
            hrp = plr.Character:FindFirstChild('HumanoidRootPart')
            if not hrp then
                return Library:Notify({Title = "View", Description = "No character root", Duration = 2})
            end
            workspace.CurrentCamera.CameraSubject = hrp
            workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
            _isViewing = true
            ViewButton:SetText("<b><font color='#55e87a'>Unview Target</font></b>")
            Library:Notify({Title = "View", Description = "Now viewing " .. plr.DisplayName, Duration = 3})
        end
    end,
})

TgtL:AddButton({
    Text = "<b><font color='#ff5050'>Clear All Targets</font></b>",
    Func = function()
        _selectedPlayers = {}
        _previouslyTargeted = {}
        _killRoundIndex = 1
        SelectedPlayer = nil
        _tracerEnabled = false
        _cleanupTracers()
        if _isViewing then
            char = LocalPlayer.Character
            hum = char and char:FindFirstChildOfClass('Humanoid')
            if hum then
                workspace.CurrentCamera.CameraSubject = hum
            end
            workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
            _isViewing = false
            ViewButton:SetText("View Target")
        end
        _refreshDropdown()
        _updateAvatarDisplay()
    end,
})

TgtL:AddDivider()

TgtL:AddLabel("Add Target"):AddKeyPicker("AimToTarget", {
    Default = "LeftAlt",
    NoUI = false,
    Text = "Aim To Target",
    Callback = function()
        best = nil
        tar = Mouse.Target
        if tar then
            c = tar
            while c and c ~= workspace do
                pl = Players:GetPlayerFromCharacter(c)
                if pl and pl ~= LocalPlayer then
                    best = pl
                    break
                end
                c = c.Parent
            end
        end
        if best then
            addTarget(best, 'AimKey')
        end
    end,
})

task.spawn(function()
    prev = {
        sel = '',
        dist = '',
        grab = '',
    }
    while true do
        task.wait(0.5)
        g = "<b><font color='#aaaaaa'>Last Grabber</font></b> <font color='#ff9955'>" .. lastGrabberDisplay .. '</font>'
        if g ~= prev.grab then
            prev.grab = g
            LastGrabberLabel:SetText(g)
        end
        count = #_selectedPlayers
        local sel
        if count == 0 then
            sel = "<b><font color='#aaaaaa'>Selected</font></b> <font color='#555555'>None</font>"
        elseif count == 1 then
            p = Players:FindFirstChild(_selectedPlayers[1])
            if p then
                sel = "<b><font color='#aaaaaa'>Selected</font></b> " .. "<font color='#fff'>" .. p.DisplayName .. '</font>' .. "<font color='#777'> (@" .. p.Name .. ')</font>'
            else
                sel = "<b><font color='#aaaaaa'>Selected</font></b> <font color='#555555'>left</font>"
            end
        else
            t = {}
            for _, n in ipairs(_selectedPlayers)do
                p = Players:FindFirstChild(n)
                table.insert(t, p and p.DisplayName or n)
            end
            sel = "<b><font color='#aaaaaa'>Selected</font></b> " .. "<font color='#fff'>" .. table.concat(t, ', ') .. '</font>'
        end
        if sel ~= prev.sel then
            prev.sel = sel
            SelectedLabel:SetText(sel)
        end
        primary = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])
        local distText
        if primary and primary.Character then
            hrp = primary.Character:FindFirstChild('HumanoidRootPart')
            lhrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
            if hrp and lhrp then
                d = math.floor((hrp.Position - lhrp.Position).Magnitude)
                col = '#55e87a'
                if d < 50 then
                    col = '#ff5050'
                elseif d < 150 then
                    col = '#ffcc55'
                end
                distText = "<b><font color='#aaaaaa'>Distance</font></b> " .. "<font color='" .. col .. "'><b>" .. d .. ' studs</b></font>'
            end
        end
        distText = distText or "<b><font color='#aaaaaa'>Distance</font></b> <font color='#555555'>\u{2014}</font>"
        if distText ~= prev.dist then
            prev.dist = distText
            TargetDistLabel:SetText(distText)
        end
    end
end)

Players.PlayerAdded:Connect(function(plr)
    task.wait(0.5)
    if _previouslyTargeted[plr.Name] and plr ~= LocalPlayer then
        table.insert(_selectedPlayers, plr.Name)
        _killRoundIndex = 1
        _dropdownUpdating = true
        pcall(function()
            PlayerDropdown:SetValue(plr.DisplayName .. ' (@' .. plr.Name .. ')')
        end)
        _dropdownUpdating = false
        Library:Notify({Title = "Target Rejoined", Description = plr.DisplayName, Duration = 6})
        if _tracerEnabled then
            task.wait(1)
            _rebuildTracers()
        end
    end
    _refreshDropdown()
end)

Players.PlayerRemoving:Connect(function(plr)
    task.wait(0.5)
    _removeFromTargets(plr.Name)
    _refreshDropdown()
end)

TgtR = Tabs.Target:AddRightGroupbox("No blobman methods", "axe")

do
    playerFlingActive = false
    flingBAV = nil
    originalPos = nil
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    R = ReplicatedStorage

    TgtR:AddCheckbox("FlingTarget", {
        Text = "Fling Target",
        Default = false,
        Callback = function(Value)
            playerFlingActive = Value
            
            if Value then
                targetPlayer = getSelectedPlayer()
                if not targetPlayer then
                    playerFlingActive = false
                    TgtR:SetValue(false)
                    return
                end
                
                MyChar = LocalPlayer.Character
                MyRoot = MyChar and MyChar:FindFirstChild("HumanoidRootPart")
                if MyRoot then
                    originalPos = MyRoot.CFrame
                end
                
                task.spawn(function()
                    while playerFlingActive do
                        target = getSelectedPlayer()
                        if not target then
                            playerFlingActive = false
                            break
                        end
                        char = LocalPlayer.Character
                        hrp = char and char:FindFirstChild("HumanoidRootPart")
                        hum = char and char:FindFirstChild("Humanoid")
                        
                        if not hrp or not hum then
                            task.wait(0.5)
                            continue
                        end
                        
                        if target and target.Parent then
                            tChar = target.Character
                            tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                            tHum = tChar and tChar:FindFirstChild("Humanoid")
                            
                            if tRoot and tHum and tHum.Health > 0 then
                                if not flingBAV or flingBAV.Parent ~= hrp then
                                    if flingBAV then
                                        flingBAV:Destroy()
                                    end
                                    flingBAV = Instance.new("BodyAngularVelocity")
                                    flingBAV.Name = "MaestroSpin"
                                    flingBAV.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                                    flingBAV.AngularVelocity = Vector3.new(0, 10000, 0)
                                    flingBAV.P = 10000
                                    flingBAV.Parent = hrp
                                end
                                
                                for _, part in pairs(char:GetDescendants()) do
                                    if part:IsA("BasePart") then
                                        part.CanCollide = false
                                    end
                                end
                                
                                loop = RunService.Heartbeat:Connect(function()
                                    if not playerFlingActive or not tRoot or not tRoot.Parent then
                                        return
                                    end
                                    hrp.CFrame = tRoot.CFrame
                                    hrp.Velocity = Vector3.zero
                                end)
                                
                                startTime = tick()
                                while tick() - startTime < 1.5 do
                                    if not playerFlingActive or not tRoot.Parent then
                                        break
                                    end
                                    task.wait(0.1)
                                end
                                
                                if loop then
                                    loop:Disconnect()
                                end
                            else
                                task.wait(0.2)
                            end
                        else
                            playerFlingActive = false
                        end
                        task.wait(0.1)
                    end
                    
                    if flingBAV then
                        flingBAV:Destroy()
                        flingBAV = nil
                    end
                    
                    char = LocalPlayer.Character
                    if char then
                        for _, part in pairs(char:GetDescendants()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = true
                            end
                        end
                        hrp = char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            hrp.RotVelocity = Vector3.zero
                            hrp.Velocity = Vector3.zero
                            if originalPos then
                                hrp.CFrame = originalPos
                            end
                        end
                    end
                end)
            else
                playerFlingActive = false
                if flingBAV then
                    flingBAV:Destroy()
                    flingBAV = nil
                end
                char = LocalPlayer.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.RotVelocity = Vector3.zero
                    hrp.Velocity = Vector3.zero
                end
            end
        end
    })
end

do
    loopKillActive = false
    loopKillHB = nil
    loopKillCameraAnchor = nil
    HEIGHT_LIMIT = 100000
    TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    R = ReplicatedStorage

    local function isTooHigh(plr)
        c = plr.Character
        hrp = c and c:FindFirstChild("HumanoidRootPart")
        return not hrp or hrp.Position.Y > HEIGHT_LIMIT
    end

    local function setNoCollideChar(char)
        for _, v in ipairs(char:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end

    local function saveOriginalPos()
        char = LocalPlayer.Character
        hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then char:SetAttribute("OriginalPosition", hrp:GetPivot()) end
    end

    local function getOriginalPos()
        char = LocalPlayer.Character
        return char and char:GetAttribute("OriginalPosition") or nil
    end

    local function findBlobman()
        toys = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        return toys and toys:FindFirstChild("CreatureBlobman") or nil
    end

    local function ensureBlobman()
        b = findBlobman()
        if b then return b end
        pcall(function()
            SpawnToy = R.MenuToys.SpawnToyRemoteFunction
            if SpawnToy then
                SpawnToy:InvokeServer("CreatureBlobman", LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
            end
        end)
        for _ = 1, 30 do
            task.wait(0.1)
            b = findBlobman()
            if b then return b end
        end
        return nil
    end

    local function modifyTarget(root, hum)
        if not (root and hum) or hum.Health <= 0 then return end
        blob = ensureBlobman()
        if blob and blob:FindFirstChild("BlobmanSeatAndOwnerScript") then
            drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureDrop")
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

        plr = Players:GetPlayerFromCharacter(hum.Parent)
        if plr and plr:FindFirstChild("IsHeld") then plr.IsHeld.Value = false end
        rag = hum:FindFirstChild("Ragdolled")
        if rag then rag.Value = false end

        bv = Instance.new("BodyVelocity")
        bav = Instance.new("BodyAngularVelocity")
        bv.MaxForce = Vector3.new(1e7, -1e7, 1e7)
        bv.P = 1e6
        bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
        bav.MaxTorque = Vector3.new(-1e7, -1e7, -1e7)
        bav.P = 1e6
        bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
        bv.Parent = root
        bav.Parent = root
        hum.BreakJointsOnDeath = false
        hum:ChangeState(Enum.HumanoidStateType.Dead)
        task.delay(2, function()
            if bv.Parent then bv:Destroy() end
            if bav.Parent then bav:Destroy() end
        end)
    end

    local function performKill()
        if not loopKillActive then return end
        target = getSelectedPlayer()
        if not target then return end
        tChar = target and target.Character
        tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
        tHum = tChar and tChar:FindFirstChild("Humanoid")
        tHead = tChar and tChar:FindFirstChild("Head")
        
        if not (target and tRoot and tHum and tHead) then return end
        if isTooHigh(target) then return end
        if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

        char = LocalPlayer.Character
        hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not (char and hrp) then return end

        if not char:GetAttribute("SavingOriginalPos") then
            saveOriginalPos()
        end
        char:SetAttribute("SavingOriginalPos", true)
        getgenv().originalFallenHeight = Workspace.FallenPartsDestroyHeight
        Workspace.FallenPartsDestroyHeight = 0/0

        originalPos = getOriginalPos()
        if originalPos and loopKillCameraAnchor then loopKillCameraAnchor:attach(originalPos) end

        hrp:PivotTo(CFrame.new(tRoot.Position + TELEPORT_OFFSET))
        setNoCollideChar(tChar)
        pcall(function() R.GrabEvents.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end)
        task.wait(0.05)
        pcall(function() R.GrabEvents.DestroyGrabLine:FireServer(tRoot) end)
        task.wait(0.05)

        if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
            task.wait(0.05)
            modifyTarget(tRoot, tHum)
        end
        scheduleReturnHome()
    end

    local function scheduleReturnHome()
        if not loopKillActive then return end
        originalPos = getOriginalPos()
        if not originalPos then return end
        local conn
        conn = RunService.Heartbeat:Connect(function()
            if not loopKillActive then
                conn:Disconnect()
                return
            end
            char = LocalPlayer.Character
            hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp:PivotTo(originalPos)
                if getgenv().originalFallenHeight then
                    Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
                end
                char:SetAttribute("SavingOriginalPos", false)
            end
            if loopKillCameraAnchor then loopKillCameraAnchor:detach() end
            conn:Disconnect()
        end)
    end

    TgtR:AddCheckbox("LoopKill", {
        Text = "Loop Kill",
        Default = false,
        Callback = function(Value)
            loopKillActive = Value
            
            if not loopKillCameraAnchor then
                loopKillCameraAnchor = {}
                loopKillCameraAnchor.__index = loopKillCameraAnchor
                function loopKillCameraAnchor:attach(cf)
                    self:detach()
                    p = Instance.new("Part")
                    p.Name = "CameraAnchor"
                    p.Size = Vector3.new(0.2, 0.2, 0.2)
                    p.Transparency = 1
                    p.Anchored = true
                    p.CanCollide = false
                    p.CFrame = cf
                    p.Parent = Workspace
                    self.part = p
                    cam = Workspace.CurrentCamera
                    cam.CameraType = Enum.CameraType.Custom
                    cam.CameraSubject = p
                end
                function loopKillCameraAnchor:detach()
                    if self.part then self.part:Destroy() self.part = nil end
                    cam = Workspace.CurrentCamera
                    char = LocalPlayer.Character
                    if char and char:FindFirstChild("Humanoid") then
                        cam.CameraSubject = char.Humanoid
                    else
                        cam.CameraType = Enum.CameraType.Custom
                    end
                end
            end
            
            if Value then
                target = getSelectedPlayer()
                if not target then
                    loopKillActive = false
                    TgtR:SetValue(false)
                    return
                end
                if loopKillHB then loopKillHB:Disconnect() end
                loopKillHB = RunService.Heartbeat:Connect(performKill)
            else
                loopKillActive = false
                if loopKillHB then 
                    loopKillHB:Disconnect() 
                    loopKillHB = nil 
                end
                if loopKillCameraAnchor then 
                    loopKillCameraAnchor:detach() 
                end
                getgenv().originalFallenHeight = nil
                char = LocalPlayer.Character
                if char then
                    char:SetAttribute("SavingOriginalPos", false)
                    char:SetAttribute("OriginalPosition", nil)
                end
                Workspace.FallenPartsDestroyHeight = -100
            end
        end
    })
end

do
    local oatsKickActive = false
    local oatsKickTask = nil
    local oatsKickGrabConnection = nil

    TgtR:AddCheckbox("OatsKick", {
        Text = "Oats Kick",
        Default = false,
        Callback = function(Value)
            oatsKickActive = Value
            
            local function sno(part)
                pcall(function()
                    local grabEvents = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
                    local setNetOwner = grabEvents and grabEvents:FindFirstChild("SetNetworkOwner")
                    if setNetOwner then
                        setNetOwner:FireServer(part, part.CFrame)
                    end
                end)
            end
            
            if Value then
                local targetPlayer = getSelectedPlayer()
                if not targetPlayer then
                    oatsKickActive = false
                    Library:Notify({Title = "Oats Kick", Description = "Please select a target first!", Duration = 3})
                    TgtR:SetValue(false)
                    return
                end
                
                if oatsKickGrabConnection then
                    oatsKickGrabConnection:Disconnect()
                    oatsKickGrabConnection = nil
                end
                
                oatsKickGrabConnection = workspace.ChildAdded:Connect(function(child)
                    if child.Name ~= "GrabParts" then return end
                    if not oatsKickActive then return end
                    
                    local grabPart = child:FindFirstChild("GrabPart")
                    if not grabPart then return end
                    
                    local weld = grabPart:FindFirstChild("WeldConstraint")
                    if not weld then return end
                    
                    local part1 = weld.Part1
                    if not part1 then return end
                    
                    local targetPlayer = Players:GetPlayerFromCharacter(part1.Parent)
                    if not targetPlayer then return end
                    
                    local destroyGrabLineEvent = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("DestroyGrabLine")
                    local setNetworkOwnerEvent = ReplicatedStorage:FindFirstChild("GrabEvents") and ReplicatedStorage.GrabEvents:FindFirstChild("SetNetworkOwner")
                    
                    if not destroyGrabLineEvent or not setNetworkOwnerEvent then return end

                    task.spawn(function()
                        while child.Parent and grabPart.Parent and oatsKickActive do
                            for i = 1, 8 do
                                if not (child.Parent and grabPart.Parent and oatsKickActive) then
                                    break
                                end

                                pcall(function()
                                    destroyGrabLineEvent:FireServer(grabPart)
                                end)
                                RunService.RenderStepped:Wait()

                                if not (child.Parent and grabPart.Parent and oatsKickActive) then
                                    break
                                end

                                pcall(function()
                                    setNetworkOwnerEvent:FireServer(grabPart, grabPart.CFrame)
                                end)
                                RunService.RenderStepped:Wait()
                            end
                            
                            local myChar = LocalPlayer.Character
                            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                            if myHRP and part1 and part1.Parent then
                                local kickPos = myHRP.CFrame * CFrame.new(0, 15, 0)
                                part1.CFrame = kickPos
                                part1.AssemblyLinearVelocity = Vector3.zero
                                part1.AssemblyAngularVelocity = Vector3.zero
                            end
                        end
                    end)
                end)
                
                oatsKickTask = task.spawn(function()
                    local RunService = game:GetService("RunService")
                    local ReplicatedStorage = game:GetService("ReplicatedStorage")
                    local target = targetPlayer
                    
                    local myChar = LocalPlayer.Character
                    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    if not (myChar and myHRP) then
                        oatsKickActive = false
                        return
                    end

                    local savedPos = myHRP.CFrame
                    local lastRemoteFire = tick()

                    while oatsKickActive and RunService.Heartbeat:Wait() do
                        local targetPlayer = getSelectedPlayer()
                        
                        if not targetPlayer then
                            break
                        end

                        myChar = LocalPlayer.Character
                        myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
                        local myHead = myChar and myChar:FindFirstChild("Head")

                        local tChar = targetPlayer.Character
                        local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        local tHum = tChar and tChar:FindFirstChild("Humanoid")

                        if not (myChar and myHRP and myHead) or not (tHRP and tHum) or tHum.Health <= 0 then
                            continue
                        end

                        local dist = (tHRP.Position - myHRP.Position).Magnitude

                        if dist > 30 then
                            pcall(function()
                                myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4))
                            end)
                            
                            sno(tHRP)

                            if not tHRP:FindFirstChild("KickAlign") then
                                local oldBp = tHRP:FindFirstChildOfClass("BodyPosition")
                                if oldBp then oldBp:Destroy() end

                                local att0 = Instance.new("Attachment", tHRP)
                                att0.Name = "KickAtt0"
                                
                                local att1 = Instance.new("Attachment", workspace.Terrain)
                                att1.Name = "KickAtt1"

                                local alignPos = Instance.new("AlignPosition")
                                alignPos.Name = "KickAlign"
                                alignPos.Attachment0 = att0
                                alignPos.Attachment1 = att1
                                alignPos.MaxForce = math.huge
                                alignPos.Responsiveness = 200
                                alignPos.Parent = tHRP

                                local alignRot = Instance.new("AlignOrientation")
                                alignRot.Name = "KickRot"
                                alignRot.Attachment0 = att0
                                alignRot.Mode = Enum.OrientationAlignmentMode.OneAttachment
                                alignRot.CFrame = CFrame.new() 
                                alignRot.MaxTorque = math.huge
                                alignRot.Responsiveness = 200
                                alignRot.Parent = tHRP
                            end

                            local grabStartTime = tick()
                            while (tick() - grabStartTime) < 0.3 and oatsKickActive do
                                task.wait(0.05)
                                sno(tHRP)
                                pcall(function()
                                    local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                    local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                    if destroyLine then
                                        destroyLine:FireServer(tHRP)
                                    end
                                end)
                                
                                local align = tHRP:FindFirstChild("KickAlign")
                                if myHead and align and align.Attachment1 then
                                    align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
                                end
                            end

                            if oatsKickActive then
                                pcall(function()
                                    myChar:PivotTo(savedPos)
                                    tHRP.CFrame = savedPos * CFrame.new(0, 15, 0)
                                end)
                            end
                            
                            continue
                        end

                        if not tHRP:FindFirstChild("KickAlign") then
                            local oldBp = tHRP:FindFirstChildOfClass("BodyPosition")
                            if oldBp then oldBp:Destroy() end

                            local att0 = Instance.new("Attachment", tHRP)
                            att0.Name = "KickAtt0"
                            
                            local att1 = Instance.new("Attachment", workspace.Terrain)
                            att1.Name = "KickAtt1"

                            local alignPos = Instance.new("AlignPosition")
                            alignPos.Name = "KickAlign"
                            alignPos.Attachment0 = att0
                            alignPos.Attachment1 = att1
                            alignPos.MaxForce = math.huge
                            alignPos.Responsiveness = 200
                            alignPos.Parent = tHRP

                            local alignRot = Instance.new("AlignOrientation")
                            alignRot.Name = "KickRot"
                            alignRot.Attachment0 = att0
                            alignRot.Mode = Enum.OrientationAlignmentMode.OneAttachment
                            alignRot.CFrame = CFrame.new() 
                            alignRot.MaxTorque = math.huge
                            alignRot.Responsiveness = 200
                            alignRot.Parent = tHRP
                        end

                        sno(tHRP)

                        local align = tHRP:FindFirstChild("KickAlign")
                        if align and align.Attachment1 and oatsKickActive then
                            align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 20, 0)
                        end

                        local rot = tHRP:FindFirstChild("KickRot")
                        if rot then 
                            rot.CFrame = CFrame.Angles(0, 0, 0) 
                        end

                        if tick() - lastRemoteFire > 0.05 and oatsKickActive then
                            pcall(function()
                                local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                if destroyLine then
                                    destroyLine:FireServer(tHRP)
                                end
                            end)
                            lastRemoteFire = tick()
                        end
                    end

                    if targetPlayer and targetPlayer.Character then
                        local tH = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                        if tH then
                            local align = tH:FindFirstChild("KickAlign")
                            local rot = tH:FindFirstChild("KickRot")
                            local att0 = tH:FindFirstChild("KickAtt0")
                            
                            if align then 
                                if align.Attachment1 then align.Attachment1:Destroy() end
                                align:Destroy() 
                            end
                            if rot then rot:Destroy() end
                            if att0 then att0:Destroy() end

                            pcall(function()
                                local grabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
                                local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                                if destroyLine then
                                    destroyLine:FireServer(tH)
                                end
                            end)
                        end
                    end

                    oatsKickActive = false
                end)
            else
                oatsKickActive = false
                if oatsKickTask then
                    task.cancel(oatsKickTask)
                    oatsKickTask = nil
                end
                if oatsKickGrabConnection then
                    oatsKickGrabConnection:Disconnect()
                    oatsKickGrabConnection = nil
                end
                
                local targetPlayer = getSelectedPlayer()
                if targetPlayer and targetPlayer.Character then
                    local tH = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if tH then
                        local align = tH:FindFirstChild("KickAlign")
                        local rot = tH:FindFirstChild("KickRot")
                        local att0 = tH:FindFirstChild("KickAtt0")
                        
                        if align then 
                            if align.Attachment1 then align.Attachment1:Destroy() end
                            align:Destroy() 
                        end
                        if rot then rot:Destroy() end
                        if att0 then att0:Destroy() end

                        pcall(function()
                            local grabEvents = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
                            local destroyLine = grabEvents and grabEvents:FindFirstChild("DestroyGrabLine")
                            if destroyLine then
                                destroyLine:FireServer(tH)
                            end
                        end)
                    end
                end
            end
        end
    })
end

do

    OwnershipKickEnabled = false
    OwnershipKickTask = nil

TgtR:AddCheckbox("OwnershipKick", {
    Text = "Ownership Kick",
    Default = false,
    Callback = function(Value)
        OwnershipKickEnabled = Value
        
        if Value then
            targetPlayer = getSelectedPlayer()
            if not targetPlayer then
                OwnershipKickEnabled = false
                Library:Notify({Title = "Ownership Kick", Description = "Please select a target first!", Duration = 3})
                TgtR:SetValue(false)
                return
            end
            
            OwnershipKickTask = task.spawn(function()
                RS = game:GetService("ReplicatedStorage")
                GE = RS:FindFirstChild("GrabEvents")
                RunService = game:GetService("RunService")
                Players = game:GetService("Players")
                LocalPlayer = game.Players.LocalPlayer
                
                if not GE then
                    OwnershipKickEnabled = false
                    return
                end
                
                myChar = LocalPlayer.Character
                myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                if not myRoot then
                    OwnershipKickEnabled = false
                    return
                end

                savedPos = myRoot.CFrame 
                dragging = false
                grabStartTime = 0
                checkStartTime = 0
                
                currentFPS = 60
                fpsConnection = RunService.RenderStepped:Connect(function(dt)
                    currentFPS = 1 / dt
                end)

                bodyPos = nil
                bodyGyro = nil

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
                    bodyGyro.D = 100
                    bodyGyro.CFrame = CFrame.new(pos)
                    bodyGyro.Parent = targetRoot
                end

                while OwnershipKickEnabled do
                    currentTarget = getSelectedPlayer()
                    if not currentTarget or not currentTarget.Parent then 
                        cleanupBodies()
                        break 
                    end
                    
                    myChar = LocalPlayer.Character
                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    tChar = currentTarget.Character
                    tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                    tHum = tChar and tChar:FindFirstChild("Humanoid")
                    
                    if tRoot and tHum and tHum.Health > 0 and myRoot then
                        if not dragging then
                            myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 3)
                            cleanupBodies()
                            checkStartTime = 0
                            
                            pcall(function()
                                tHum.PlatformStand = true
                                tHum.Sit = true
                                if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                                if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end
                                if GE.DestroyGrabLine then GE.DestroyGrabLine:FireServer(tRoot) end
                            end)
                            
                            myRoot.AssemblyLinearVelocity = Vector3.zero
                            myRoot.AssemblyAngularVelocity = Vector3.zero
                            
                            if grabStartTime == 0 then grabStartTime = tick() end
                            if tick() - grabStartTime > 0.35 then
                                dragging = true
                                grabStartTime = 0
                                checkStartTime = tick()
                                lockPos = savedPos * CFrame.new(5, 20, 4)
                                createBodies(tRoot, lockPos.Position)
                            end
                        else
                            myRoot.CFrame = savedPos
                            lockPos = savedPos * CFrame.new(5, 20, 4)
                            
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
                                if GE.SetNetworkOwner and GE.DestroyGrabLine then
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
                                end
                            end)
                            
                            if checkStartTime > 0 and tick() - checkStartTime > 0.30 then
                                currentDist = (tRoot.Position - lockPos.Position).Magnitude
                                
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
            end)
        else
            OwnershipKickEnabled = false
            if OwnershipKickTask then
                task.cancel(OwnershipKickTask)
                OwnershipKickTask = nil
            end
            
            targetPlayer = getSelectedPlayer()
            if targetPlayer and targetPlayer.Character then
                tRoot = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
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
end


TgtR:AddCheckbox("RemoveAntiKickToggle", {
    Text = "[AURA] Remove Target Anti Kick",
    Default = false,
    Callback = function(Value)
        antiAntiKickActive = Value
        if Value then
            task.spawn(function()
                SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
                while antiAntiKickActive do
                    target = getSelectedPlayer()
                    if target then
                        spawned = workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                        if spawned then
                            toys = {"NinjaKunai", "NinjaShuriken", "AntiKick", "ToolCleaver", "ToolPencil"}
                            for _, toyName in ipairs(toys) do
                                toy = spawned:FindFirstChild(toyName)
                                if toy then
                                    part = toy:FindFirstChild("SoundPart") or toy:FindFirstChild("StickyPart")
                                    if part then
                                        pcall(function()
                                            SetNetOwner:FireServer(part, part.CFrame)
                                            if part:FindFirstChild("PartOwner") and part.PartOwner.Value == LocalPlayer.Name then
                                                part.CFrame = CFrame.new(0, 1000, 0)
                                            end
                                        end)
                                    end
                                end
                            end
                        end
                    end
                    task.wait(0.1)
                end
            end)
        end
    end
})
do

DestroyTargetGucciActive = false

TgtR:AddCheckbox("DestroyTargetGucci", {
    Text = "[SIT] Remove Target Gucci",
    Default = false,
    Callback = function(Value)
        DestroyTargetGucciActive = Value
        if Value then
            target = getSelectedPlayer()
            if not target then
                Toggles.DestroyTargetGucci:SetValue(false)
                return
            end
            char = LocalPlayer.Character
            root = char and char:FindFirstChild("HumanoidRootPart")
            if not root then
                return
            end
            SafeSpot = root.CFrame
            folderName = target.Name .. "SpawnedInToys"
            task.spawn(function()
                while DestroyTargetGucciActive do
                    target = getSelectedPlayer()
                    if not target or not target.Parent then
                        DestroyTargetGucciActive = false
                        Toggles.DestroyTargetGucci:SetValue(false)
                        break
                    end
                    toysFolder = workspace:FindFirstChild(folderName)
                    if toysFolder then
                        for _, obj in ipairs(toysFolder:GetChildren()) do
                            if not DestroyTargetGucciActive then
                                break
                            end
                            if obj.Name == "CreatureBlobman" then
                                seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
                                if seat then
                                    myChar = LocalPlayer.Character
                                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                                    myHum = myChar and myChar:FindFirstChild("Humanoid")
                                    if myRoot and myHum and myHum.SeatPart ~= seat then
                                        local magnetConn
                                        magnetConn = RunService.Stepped:Connect(function()
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
                                            if not DestroyTargetGucciActive then
                                                break
                                            end
                                            if myHum.SeatPart == seat then
                                                break
                                            end
                                            seat:Sit(myHum)
                                            task.wait()
                                        end
                                        if magnetConn then
                                            magnetConn:Disconnect()
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
                    task.wait(1)
                end
            end)
        end
    end
})
end

TgtR:AddCheckbox("PalletRagdoll", {
    Text = "Pallet Ragdoll (Invis)",
    Default = false,
    Callback = function(Value)
        RS = game:GetService("ReplicatedStorage")
        RunService = game:GetService("RunService")
        DestroyToy = RS:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
        SetNetOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
        DestroyLine = RS:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
        toysFolder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
        lpName = LocalPlayer.Name

        function clearAttackLoop()
            if getgenv().ragdollSteppedConn then
                getgenv().ragdollSteppedConn:Disconnect()
                getgenv().ragdollSteppedConn = nil
            end
        end

        if Value then
            target = getSelectedPlayer()
            if not target then
                Toggles.PalletRagdoll:SetValue(false)
                return
            end

            getgenv().palletRagdollActive = true
            getgenv().PalletForRagdoll = nil
            
            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
            end
            clearAttackLoop()

            getgenv().palletCacheConn = toysFolder.ChildAdded:Connect(function(child)
                if not getgenv().palletRagdollActive then return end
                if child.Name ~= "PalletLightBrown" and child.Name ~= "PalletForRagdoll" then return end

                soundPart = child:WaitForChild("SoundPart", 3)
                if not soundPart then return end

                pcall(function()
                    SetNetOwner:FireServer(soundPart, soundPart.CFrame)
                    DestroyLine:FireServer(soundPart)
                end)

                partOwner = soundPart:WaitForChild("PartOwner", 1)
                if partOwner and partOwner.Value == lpName then
                    for _, v in pairs(child:GetChildren()) do
                        if v:IsA("BasePart") then
                            v.CanCollide = false
                            v.CanQuery = false
                            v.Transparency = 1 
                        end
                    end

                    child.Name = "PalletForRagdoll"
                    getgenv().PalletForRagdoll = child

                    strikePhase = false

                    getgenv().ragdollSteppedConn = RunService.Stepped:Connect(function()
                        if not getgenv().palletRagdollActive or not child.Parent then 
                            clearAttackLoop()
                            return 
                        end

                        tChar = target and target.Character
                        tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
                        tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")

                        if tRoot and tHum and soundPart.Parent and tHum.Health > 0 then
                            ragdolledVal = tHum:FindFirstChild("Ragdolled")
                            isRagdolled = ragdolledVal and ragdolledVal.Value or false

                            if not isRagdolled then
                                strikePhase = not strikePhase
                                if strikePhase then
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, 2, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, -9e5, 0)
                                else
                                    soundPart.CFrame = tRoot.CFrame * CFrame.new(0, -1, 0)
                                    soundPart.AssemblyLinearVelocity = Vector3.new(0, 9e5, 0)
                                end
                            else
                                soundPart.CFrame = CFrame.new(0, 9e9, 0)
                                soundPart.AssemblyLinearVelocity = Vector3.zero
                            end
                        else
                            soundPart.CFrame = CFrame.new(0, 9e9, 0)
                            soundPart.AssemblyLinearVelocity = Vector3.zero
                        end
                    end)

                    child.AncestryChanged:Connect(function()
                        if not child.Parent then
                            clearAttackLoop()
                            getgenv().PalletForRagdoll = nil
                            if getgenv().palletRagdollActive then
                                task.wait(0.03)
                                if getgenv().spawnNewPallet then getgenv().spawnNewPallet() end
                            end
                        end
                    end)
                else
                    pcall(function() DestroyToy:FireServer(child) end)
                end
            end)

            getgenv().spawnNewPallet = function()
                if not getgenv().palletRagdollActive then return end
                if getgenv().PalletForRagdoll and getgenv().PalletForRagdoll.Parent then return end
                
                c = LocalPlayer.Character
                h = c and c:FindFirstChild("HumanoidRootPart")
                if not h then return end

                task.spawn(function()
                    pcall(function()
                        RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                            "PalletLightBrown",
                            h.CFrame * CFrame.new(0, 10, 20),
                            Vector3.zero
                        )
                    end)
                end)
            end

            getgenv().spawnNewPallet()
        else
            getgenv().palletRagdollActive = false
            clearAttackLoop()

            if getgenv().palletCacheConn then
                getgenv().palletCacheConn:Disconnect()
                getgenv().palletCacheConn = nil
            end

            pallet = getgenv().PalletForRagdoll
            if pallet and pallet.Parent then
                pcall(function() DestroyToy:FireServer(pallet) end)
            end

            getgenv().PalletForRagdoll = nil

            if toysFolder:FindFirstChild("PalletForRagdoll") then
                pcall(function() DestroyToy:FireServer(toysFolder.PalletForRagdoll) end)
            end
        end
    end
})

function sno(part)
    if not part or not part.Parent then
        return
    end
    pcall(function()
        RemoteSetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

function executeGrabKick()
    if not SelectedPlayer then
        return
    end

    targetPlayer = Players:FindFirstChild(SelectedPlayer)

    if not targetPlayer or not targetPlayer.Character then
        return
    end

    char = LocalPlayer.Character
    hrp = char and char:FindFirstChild("HumanoidRootPart")
    head = char and char:FindFirstChild("Head")

    if not (char and hrp and head) then
        return
    end

    targetChar = targetPlayer.Character
    targetHrp = targetChar:FindFirstChild("HumanoidRootPart")
    targetHead = targetChar:FindFirstChild("Head")
    Hum = targetChar:FindFirstChild("Humanoid")

    if not (targetHrp and targetHead and Hum) or Hum.Health == 0 then
        return
    end

    if targetPlayer:FindFirstChild("InPlot") and targetPlayer.InPlot.Value then
        return
    end

    BodyPos = targetHrp:FindFirstChild("BodyPosition")
    BodyGyro = targetHrp:FindFirstChild("BodyGyro")

    if not BodyPos then
        BodyPos = Instance.new("BodyPosition")
        BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        BodyPos.Parent = targetHrp
        BodyPos.P = 50000
        BodyPos.D = 1000
    end
    if not BodyGyro then
        BodyGyro = Instance.new("BodyGyro")
        BodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        BodyGyro.Parent = targetHrp
        BodyGyro.P = 50000
        BodyGyro.D = 1000
    end

    offset = Vector3.new(kickOffsetX, kickOffsetY, kickOffsetZ)
    targetPos = head.Position + offset
    
    BodyPos.Position = targetPos
    BodyGyro.CFrame = hrp.CFrame

    sno(targetHrp)
    sno(targetHead)
    RemoteDestroyGrabLine:FireServer(targetHrp)
    RemoteDestroyGrabLine:FireServer(targetHead)

    if (targetHrp.Position - hrp.Position).Magnitude > 35 then
        oldCF = char:GetPivot()

        repeat
            if not LoopGrabKickOn then
                break
            end

            char:PivotTo(targetHrp.CFrame * CFrame.new(0, 0, -10))
            sno(targetHrp)
            sno(targetHead)
            task.wait()
        until (targetHrp.Position - hrp.Position).Magnitude <= 35 or targetHead:FindFirstChild("PartOwner") or not LoopGrabKickOn

        char:PivotTo(oldCF)
    end
end

function StartLoopGrabKick()
    if GrabKickHB then
        GrabKickHB:Disconnect()
    end

    LoopGrabKickOn = true
    GrabKickHB = RunService.Heartbeat:Connect(function()
        if LoopGrabKickOn then
            executeGrabKick()
        end
    end)
end

function StopLoopGrabKick()
    LoopGrabKickOn = false

    if GrabKickHB then
        GrabKickHB:Disconnect()
        GrabKickHB = nil
    end
    if SelectedPlayer then
        tp = Players:FindFirstChild(SelectedPlayer)
        if tp and tp.Character then
            tHrp = tp.Character:FindFirstChild("HumanoidRootPart")
            if tHrp then
                if tHrp:FindFirstChild("BodyPosition") then
                    tHrp.BodyPosition:Destroy()
                end
                if tHrp:FindFirstChild("BodyGyro") then
                    tHrp.BodyGyro:Destroy()
                end
            end
        end
    end
end

TgtB = Tabs.Target:AddRightGroupbox("Blobman methods", "loop")

do
    autoSitBlobActive = false
    autoSitBlobTask = nil
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction

    TgtB:AddCheckbox("AutoSitBlobman", {
        Text = "Auto Sit Blobman(fixing)",
        Default = false,
        Callback = function(Value)
            autoSitBlobActive = Value
            
            if Value then
                autoSitBlobTask = task.spawn(function()
                    while autoSitBlobActive do
                        pcall(function()
                            Char = LocalPlayer.Character
                            if not Char then return end
                            Hum = Char:FindFirstChildOfClass("Humanoid")
                            Root = Char:FindFirstChild("HumanoidRootPart")
                            if not Hum or not Root then return end
                            
                            if Hum.SeatPart then
                                task.wait(0.1)
                                return
                            end
                            
                            folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                            blob = folder and folder:FindFirstChild("CreatureBlobman")

                            if not blob then
                                pcall(function()
                                    SpawnToy:InvokeServer("CreatureBlobman", Root.CFrame * CFrame.new(0, 5, 5), Vector3.zero)
                                end)
                                t0 = tick()
                                repeat
                                    RunService.Heartbeat:Wait()
                                    folder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                                    blob = folder and folder:FindFirstChild("CreatureBlobman")
                                until blob or tick() - t0 > 5 or not autoSitBlobActive
                            end

                            if blob then
                                seat = blob:FindFirstChildWhichIsA("VehicleSeat")
                                if seat then
                                    Root.CFrame = seat.CFrame * CFrame.new(0, 1, 0)
                                    Root.Velocity = Vector3.zero
                                    pcall(function()
                                        seat:Sit(Hum)
                                    end)
                                end
                            end
                        end)
                        task.wait(0.1)
                    end
                end)
            else
                autoSitBlobActive = false
                if autoSitBlobTask then
                    task.cancel(autoSitBlobTask)
                    autoSitBlobTask = nil
                end
            end
        end
    })
end

do
    local BlobKillList = {}
    local blobKillActive = false
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")
    local LocalPlayer = Players.LocalPlayer
    local R = ReplicatedStorage

    local function checkgrab(itm)
        for _, prt in pairs(itm:GetChildren()) do
            if prt:IsA("BasePart") and prt:FindFirstChild("PartOwner") and prt.PartOwner.Value == LocalPlayer.Name then
                return true
            end
        end
        return false
    end

    local function BlobKick()
        while true do
            local gotsomeone = false
            
            for _, plr in pairs(Players:GetPlayers()) do
                if BlobKillList[plr.UserId] and blobKillActive then
                    local j, h
                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        j = LocalPlayer.Character.HumanoidRootPart.CFrame
                        h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
                    end

                    if plr ~= LocalPlayer then
                        local continue = false
                        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                            if plr.Character.HumanoidRootPart.Massless then
                                if plr.Character.Humanoid.SeatPart then
                                    continue = true
                                end
                            else
                                continue = true
                            end
                        end

                        local smegma = true
                        while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and plr and plr.Character and plr.Character.Parent == Workspace and plr.Character:FindFirstChild("HumanoidRootPart") and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and continue do
                            if not BlobKillList[plr.UserId] or not blobKillActive then
                                smegma = false
                                break
                            end

                            gotsomeone = true
                            continue = false
                            continue = true

                            local vel = plr.Character.HumanoidRootPart.AssemblyLinearVelocity
                            if vel.Magnitude > 10000 then
                                vel = Vector3.zero
                            end
                            if plr.Character.HumanoidRootPart.CFrame.Position.Magnitude > 1000000 then
                                continue = false
                            end

                            if smegma and continue and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
                                local blob = LocalPlayer.Character.Humanoid.SeatPart.Parent
                                if (plr.Character.HumanoidRootPart.CFrame.Position - LocalPlayer.Character.HumanoidRootPart.CFrame.Position + LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity).Magnitude > 30 and plr.Character.Parent == Workspace then
                                    LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character.HumanoidRootPart.CFrame + (plr.Character.HumanoidRootPart.AssemblyLinearVelocity / math.pi)
                                    LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character.HumanoidRootPart.AssemblyLinearVelocity

                                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                        if plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and blob and blob:FindFirstChild("RightDetector") and blob.RightDetector:FindFirstChild("RightWeld") and blob:FindFirstChild("BlobmanSeatAndOwnerScript") and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab") and blob.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureRelease") then
                                            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                                            task.wait(0.15)
                                            blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                            task.wait(0.1)
                                            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(blob.RightDetector.RightWeld, plr.Character.HumanoidRootPart)
                                        end
                                    end
                                end
                            elseif continue then
                                if LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and (LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Name ~= "CreatureBlobman" or not LocalPlayer.Character.Humanoid.SeatPart.Parent) then
                                    LocalPlayer.Character.Humanoid.Sit = false
                                end

                                local blob = nil
                                local foundblob = false
                                for _, itm in pairs(Workspace[LocalPlayer.Name .. "SpawnedInToys"]:GetChildren()) do
                                    if itm.Name == "CreatureBlobman" and itm:FindFirstChild("VehicleSeat") then
                                        foundblob = true
                                        blob = itm
                                    elseif itm.Name == "CreatureBlobman" then
                                        while itm do
                                            R.MenuToys.DestroyToy:FireServer(itm)
                                            task.wait(0.1)
                                        end
                                    end
                                end

                                if blob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                    blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                end
                                if not foundblob then
                                    task.wait(1)
                                    while smegma and not blob do
                                        if Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys"):FindFirstChild("CreatureBlobman") then
                                            blob = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys").CreatureBlobman
                                        else
                                            task.spawn(function()
                                                R.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15), Vector3.new(0, 0, 0))
                                            end)
                                        end
                                        task.wait()
                                    end
                                end
                            end
                            task.wait(0.15)
                        end

                        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and j then
                            LocalPlayer.Character.HumanoidRootPart.CFrame = j
                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
                        end
                    end
                end
            end

            if not gotsomeone and blob then
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent == blob then
                    LocalPlayer.Character.Humanoid.Sit = false
                end
                task.wait()
                if blob and blob:FindFirstChild("HumanoidRootPart") then
                    blob.HumanoidRootPart.CFrame = CFrame.new(0, 1e15, 0)
                end
            end
            task.wait(0.5)
        end
    end

    task.spawn(BlobKick)

    TgtB:AddCheckbox("BlobKillTarget", {
        Text = "Blob Kill Target",
        Default = false,
        Callback = function(Value)
            blobKillActive = Value
            local target = getSelectedPlayer()
            
            if Value then
                if not target then
                    blobKillActive = false
                    TgtR:SetValue(false)
                    return
                end
                BlobKillList[target.UserId] = true
            else
                if target then
                    BlobKillList[target.UserId] = nil
                end
            end
        end
    })
end

do
    kickLoopEnabled = false
    kickHeight = 25
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    R = ReplicatedStorage

    TgtB:AddCheckbox("LoopKickBlob", {
        Text = "Loop Kick (Grab + Blob)",
        Default = false,
        Callback = function(Value)
            kickLoopEnabled = Value
            
            if Value then
                target = getSelectedPlayer()
                if not target then
                    kickLoopEnabled = false
                    TgtR:SetValue(false)
                    return
                end
                
                task.spawn(function()
                    GE = R:FindFirstChild("GrabEvents")
                    myChar = LocalPlayer.Character
                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    
                    if not myRoot then 
                        kickLoopEnabled = false
                        return 
                    end

                    savedPos = myRoot.CFrame
                    dragging = false
                    grabStartTime = 0

                    while kickLoopEnabled do
                        target = getSelectedPlayer()
                        if not target or not target.Parent or not target.Character then 
                            kickLoopEnabled = false
                            break 
                        end
                        
                        tChar = target.Character
                        tRoot = tChar:FindFirstChild("HumanoidRootPart")
                        tHum = tChar:FindFirstChild("Humanoid")
                        
                        seat = myChar and myChar.Humanoid and myChar.Humanoid.SeatPart
                        
                        if tRoot and tHum and tHum.Health > 0 then
                            tRoot.AssemblyLinearVelocity = Vector3.zero
                            tRoot.Velocity = Vector3.zero

                            if seat then
                                blobman = seat.Parent
                                remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                                grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                                drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
                                
                                L_Det = blobman:FindFirstChild("LeftDetector")
                                R_Det = blobman:FindFirstChild("RightDetector")
                                L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                                R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

                                if grab and drop and L_Weld and R_Weld then
                                    pcall(function()
                                        grab:FireServer(L_Det, tRoot, L_Weld)
                                        grab:FireServer(R_Det, tRoot, R_Weld)
                                        drop:FireServer(L_Weld, tRoot)
                                        drop:FireServer(R_Weld, tRoot)
                                    end)
                                end
                            end

                            if not dragging then
                                myRoot.CFrame = tRoot.CFrame
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame) end
                                        if GE.CreateGrabLine then GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false) end
                                    end)
                                end
                                
                                if grabStartTime == 0 then grabStartTime = tick() end
                                if tick() - grabStartTime > 0.3 then
                                    dragging = true
                                    grabStartTime = 0
                                end
                            else
                                lockPos = savedPos * CFrame.new(0, kickHeight, 0)
                                myRoot.CFrame = savedPos
                                tRoot.CFrame = lockPos
                                
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, lockPos) end
                                        if GE.DestroyGrabLine then GE.DestroyGrabLine:FireServer(tRoot) end
                                        if GE.CreateGrabLine then GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false) end
                                    end)
                                end
                            end
                        else
                            dragging = false
                            grabStartTime = 0
                        end
                        
                        RunService.Heartbeat:Wait()
                    end

                    if myRoot and savedPos then
                        myRoot.CFrame = savedPos
                    end
                    kickLoopEnabled = false
                end)
            else
                kickLoopEnabled = false
            end
        end
    })
end

do
    spinLoopActive = false
    spinLoopTask = nil
    spinAngle = 0
    spinRadius = 25
    spinSpeed = 0.25
    customKickHeight = 20
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    R = ReplicatedStorage

    TgtB:AddSlider("SpinRadius", {
        Text = "Spin Radius",
        Default = 25,
        Min = 5,
        Max = 50,
        Rounding = 0,
        Callback = function(v)
            spinRadius = v
        end
    })

    TgtB:AddSlider("SpinSpeed", {
        Text = "Spin Speed",
        Default = 0.25,
        Min = 0.05,
        Max = 1,
        Rounding = 2,
        Callback = function(v)
            spinSpeed = v
        end
    })

    TgtB:AddCheckbox("SpinLoopKick", {
        Text = "Spin Loop Kick",
        Default = false,
        Callback = function(Value)
            spinLoopActive = Value
            
            if Value then
                target = getSelectedPlayer()
                if not target then
                    spinLoopActive = false
                    TgtR:SetValue(false)
                    return
                end
                
                spinAngle = 0
                
                spinLoopTask = task.spawn(function()
                    GE = R:FindFirstChild("GrabEvents")
                    
                    myChar = LocalPlayer.Character
                    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
                    
                    if not myRoot then 
                        spinLoopActive = false
                        return 
                    end

                    savedPos = myRoot.CFrame
                    dragging = false
                    grabStartTime = 0

                    while spinLoopActive do
                        target = getSelectedPlayer()
                        if not target or not target.Parent or not target.Character then 
                            spinLoopActive = false
                            break 
                        end
                        
                        tChar = target.Character
                        tRoot = tChar:FindFirstChild("HumanoidRootPart")
                        tHum = tChar:FindFirstChild("Humanoid")
                        
                        seat = myChar and myChar.Humanoid and myChar.Humanoid.SeatPart
                        
                        if tRoot and tHum and tHum.Health > 0 then
                            tRoot.AssemblyLinearVelocity = Vector3.zero
                            tRoot.Velocity = Vector3.zero

                            if seat then
                                blobman = seat.Parent
                                remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                                grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                                drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
                                
                                L_Det = blobman:FindFirstChild("LeftDetector")
                                R_Det = blobman:FindFirstChild("RightDetector")
                                L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                                R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

                                if grab and drop and L_Weld and R_Weld then
                                    pcall(function()
                                        grab:FireServer(L_Det, tRoot, L_Weld)
                                        grab:FireServer(R_Det, tRoot, R_Weld)
                                        drop:FireServer(L_Weld, tRoot)
                                        drop:FireServer(R_Weld, tRoot)
                                    end)
                                end
                            end

                            if not dragging then
                                spinAngle = spinAngle + spinSpeed
                                if spinAngle > 6.28 then spinAngle = 0 end
                                
                                x = math.cos(spinAngle) * spinRadius
                                z = math.sin(spinAngle) * spinRadius
                                
                                myRoot.CFrame = tRoot.CFrame * CFrame.new(x, 0, z)
                                
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame) end
                                        if GE.CreateGrabLine then GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false) end
                                    end)
                                end
                                
                                if grabStartTime == 0 then grabStartTime = tick() end
                                if tick() - grabStartTime > 0.3 then
                                    dragging = true
                                    grabStartTime = 0
                                end
                            else
                                spinAngle = spinAngle + spinSpeed
                                if spinAngle > 6.28 then spinAngle = 0 end
                                
                                x = math.cos(spinAngle) * spinRadius
                                z = math.sin(spinAngle) * spinRadius
                                
                                myRoot.CFrame = tRoot.CFrame * CFrame.new(x, 0, z)
                                
                                lockPos = savedPos * CFrame.new(0, customKickHeight, 0)
                                tRoot.CFrame = lockPos
                                
                                if GE then
                                    pcall(function()
                                        tHum.PlatformStand = true
                                        if GE.SetNetworkOwner then GE.SetNetworkOwner:FireServer(tRoot, lockPos) end
                                        if GE.DestroyGrabLine then GE.DestroyGrabLine:FireServer(tRoot) end
                                        if GE.CreateGrabLine then GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false) end
                                    end)
                                end
                            end
                        else
                            dragging = false
                            grabStartTime = 0
                        end
                        
                        RunService.Heartbeat:Wait()
                    end

                    if myRoot and savedPos then
                        myRoot.CFrame = savedPos
                    end
                    spinLoopActive = false
                end)
            else
                spinLoopActive = false
                if spinLoopTask then
                    task.cancel(spinLoopTask)
                    spinLoopTask = nil
                end
                spinAngle = 0
            end
        end
    })
end

Players = game:GetService('Players')
RunService = game:GetService('RunService')
Debris = game:GetService('Debris')
SoundService = game:GetService('SoundService')
LocalPlayer = Players.LocalPlayer
notificationSoundId = 'rbxassetid://97643101798871'
selectedTexture = 'Low Quality'
customTextureId = ''
useSelectedTexture = true
useCustomTexture = false
textureSpeed = 1
textureLength = 1
textureWidth = 0
customGrabSoundId = ''
speedEnabled = false
lengthEnabled = false
widthEnabled = false
beamTransparency = 0
beamCurveEnabled = false
beamCurveAmount = 0
beamFaceCameraEnabled = false
beamColorEnabled = false
beamColor0 = Color3.fromRGB(255, 255, 255)
beamColor1 = Color3.fromRGB(255, 255, 255)
beamLightEnabled = false
beamLightColor = Color3.fromRGB(255, 255, 255)
beamLightRange = 16
beamLightBrightness = 2
beamRainbowEnabled = false
beamRainbowHue = 0
beamRainbowSpeed = 0.5
beamPulseEnabled = false
beamPulseMin = 0
beamPulseMax = 1
beamPulseSpeed = 2
beamSegmentsEnabled = false
beamSegments = 10
beamZOffsetEnabled = false
beamZOffset = 0
beamWidth0 = 0
beamWidth1 = 0
beamWidthLinked = true
beamShadowEnabled = false
beamShadowOffset = Vector3.new(0.2, -0.2, 0)
beamShadowColor = Color3.fromRGB(0, 0, 0)
beamShadowTransp = 0.5
mirrorBeamEnabled = false
beamFlipEnabled = false
beamFlipX = false
beamFlipY = false
heartbeatConnection = nil
syncAccumulator = 0
toggleSyncing = false
rainbowConn = nil
pulseConn = nil
pulseDir = 1
pulseVal = 0
shadowBeam = nil
mirrorBeamObj = nil

local textureIds = {
    ['Low Quality'] = '',
    ['Non-Gamepass'] = 'rbxassetid://8933346550',
    Gamepass = 'rbxassetid://8933355899',
    Chain = 'rbxassetid://81358145120405',
    ['Chain 2'] = 'rbxassetid://132910145874066',
    ['Chain 3'] = 'rbxassetid://128466395060514',
    ['Chain 4'] = 'rbxassetid://73368670987191',
    Rope = 'rbxassetid://78999022056924',
    Spring = 'rbxassetid://18837732116',
    Circle = 'rbxassetid://5367817750',
    ['Circle-Outline'] = 'rbxassetid://12201347372',
    Triangle = 'rbxassetid://4704920160',
    ['Triangle-Outline'] = 'rbxassetid://94666748694025',
    Square = 'rbxassetid://15007588972',
    ['Square-Outline'] = 'rbxassetid://15420927706',
    Heart = 'rbxassetid://89015294175898',
    ['Heart-Outline'] = 'rbxassetid://125373934805238',
    Moon = 'rbxassetid://9013498676',
    Dots = 'rbxassetid://9169659357',
    Bubble = 'rbxassetid://1249690853',
    Star = 'rbxassetid://5639840603',
    Robux = 'rbxassetid://11560341132',
    ['Roblox-Logo'] = 'rbxassetid://12348119032',
    Brick = 'rbxassetid://4430903072',
    Studs = 'rbxassetid://15539356451',
    Fire = 'rbxassetid://18654087326',
    Lazar = 'rbxassetid://8922958725',
    ['Spider-Web'] = 'rbxassetid://123815660139244',
    Smoke = 'rbxassetid://12900071392',
    ['Audio-Visualiser'] = 'rbxassetid://81588563590679',
    Pulse = 'rbxassetid://82163767314193',
    Arrow = 'rbxassetid://9006027964',
    ['Arrow 2'] = 'rbxassetid://10249261576',
}
local textureValues = {
    'Low Quality',
    'Non-Gamepass',
    'Gamepass',
    'Chain',
    'Chain 2',
    'Chain 3',
    'Chain 4',
    'Rope',
    'Spring',
    'Circle',
    'Circle-Outline',
    'Triangle',
    'Triangle-Outline',
    'Square',
    'Square-Outline',
    'Heart',
    'Heart-Outline',
    'Moon',
    'Dots',
    'Bubble',
    'Star',
    'Robux',
    'Roblox-Logo',
    'Brick',
    'Studs',
    'Fire',
    'Lazar',
    'Spider-Web',
    'Smoke',
    'Audio-Visualiser',
    'Pulse',
    'Arrow',
    'Arrow 2',
}

function cleanId(text)
    return tostring(text or ''):gsub('%D', '')
end
function toAssetId(id)
    id = cleanId(id)

    return id ~= '' and 'rbxassetid://' .. id or ''
end
function playNotificationSound()
    local s = Instance.new('Sound')

    s.SoundId = notificationSoundId
    s.Volume = 1
    s.RollOffMaxDistance = 10000
    s.Parent = SoundService

    pcall(function()
        s:Play()
    end)
    Debris:AddItem(s, 3)
end
function notify(title, body, dur)
    pcall(function()
        Library:Notify({
            Title = title,
            Description = body or '',
            Time = dur or 4,
        })
    end)
    playNotificationSound()
end
function notifyLineChanged(name)
    notify('<b>UNSTABLE</b>', 'Now using: ' .. tostring(name), 3)
end
function getBeamPart()
    local gp = workspace:FindFirstChild('GrabParts')

    return gp and gp:FindFirstChild('BeamPart')
end
function getGrabPart()
    local gp = workspace:FindFirstChild('GrabParts')

    return gp and gp:FindFirstChild('GrabPart')
end
function captureOriginalSoundId(s)
    if s and s:IsA('Sound') and s:GetAttribute('OriginalSoundId') == nil then
        s:SetAttribute('OriginalSoundId', s.SoundId or '')
    end
end
function setSoundId(s, newId)
    if not s or not s:IsA('Sound') or not newId or newId == '' then
        return
    end
    if s.SoundId == newId then
        return
    end

    local wasPlaying = s.IsPlaying

    pcall(function()
        s:Stop()
    end)

    s.SoundId = newId

    if wasPlaying then
        pcall(function()
            s.TimePosition = 0

            s:Play()
        end)
    end
end
function restoreOriginalSound(s)
    if not s or not s:IsA('Sound') then
        return
    end

    captureOriginalSoundId(s)

    local orig = s:GetAttribute('OriginalSoundId')

    if orig and orig ~= '' and s.SoundId ~= orig then
        setSoundId(s, orig)
    end
end
function setLineMode(mode)
    if toggleSyncing then
        return
    end

    toggleSyncing = true

    if mode == 'selected' then
        useSelectedTexture = true
        useCustomTexture = false

        if Options.UseSelectedTexture then
            Options.UseSelectedTexture:SetValue(true)
        end
        if Options.UseCustomTexture then
            Options.UseCustomTexture:SetValue(false)
        end
    elseif mode == 'custom' then
        useSelectedTexture = false
        useCustomTexture = true

        if Options.UseSelectedTexture then
            Options.UseSelectedTexture:SetValue(false)
        end
        if Options.UseCustomTexture then
            Options.UseCustomTexture:SetValue(true)
        end
    end

    toggleSyncing = false
end
function ensureValidTextureMode()
    local hasCustom = cleanId(customTextureId) ~= ''

    if useCustomTexture and not hasCustom then
        setLineMode('selected')
    elseif not useSelectedTexture and not useCustomTexture then
        setLineMode(hasCustom and 'custom' or 'selected')
    end
end
function getActiveTexture()
    ensureValidTextureMode()

    if useCustomTexture then
        local ct = toAssetId(customTextureId)

        if ct ~= '' then
            return ct, 'Custom'
        end
    end

    return textureIds[selectedTexture] or '', selectedTexture
end
function updateShadowBeam()
    local beamPart = getBeamPart()

    if not beamPart then
        return
    end

    local beam = beamPart:FindFirstChild('GrabBeam')

    if not beam then
        return
    end
    if beamShadowEnabled then
        if not shadowBeam or not shadowBeam.Parent then
            shadowBeam = beam:Clone()
            shadowBeam.Name = '<b>UNSTABLE</b>_ShadowBeam'
            shadowBeam.Parent = beamPart
        end

        shadowBeam.Color = ColorSequence.new(beamShadowColor, beamShadowColor)
        shadowBeam.Transparency = NumberSequence.new(beamShadowTransp)
        shadowBeam.ZOffset = (beamZOffsetEnabled and beamZOffset or 0) - 0.05
    else
        if shadowBeam then
            shadowBeam:Destroy()

            shadowBeam = nil
        end
    end
end
function updateMirrorBeam()
    local beamPart = getBeamPart()

    if not beamPart then
        return
    end

    local beam = beamPart:FindFirstChild('GrabBeam')

    if not beam then
        return
    end
    if mirrorBeamEnabled then
        if not mirrorBeamObj or not mirrorBeamObj.Parent then
            mirrorBeamObj = beam:Clone()
            mirrorBeamObj.Name = '<b>UNSTABLE</b>_MirrorBeam'
            mirrorBeamObj.Parent = beamPart
        end

        mirrorBeamObj.CurveSize0 = -beam.CurveSize0
        mirrorBeamObj.CurveSize1 = -beam.CurveSize1
        mirrorBeamObj.ZOffset = (beamZOffsetEnabled and beamZOffset or 0) + 0.02
    else
        if mirrorBeamObj then
            mirrorBeamObj:Destroy()

            mirrorBeamObj = nil
        end
    end
end
function applyLineSettings()
    local beamPart = getBeamPart()

    if not beamPart then
        return
    end

    local beam = beamPart:FindFirstChild('GrabBeam')

    if not beam or not beam:IsA('Beam') then
        return
    end

    beam.Texture = getActiveTexture()

    if speedEnabled then
        beam.TextureSpeed = textureSpeed
    end
    if lengthEnabled then
        beam.TextureLength = textureLength
    end
    if widthEnabled then
        beam.Width0 = beamWidth0
        beam.Width1 = beamWidthLinked and beamWidth0 or beamWidth1
    end

    beam.Transparency = NumberSequence.new(beamPulseEnabled and pulseVal or beamTransparency)

    if beamCurveEnabled then
        local curve = beamCurveAmount
        local dir = beamCurveDirection or 1

        beam.CurveSize0 = curve * dir
        beam.CurveSize1 = curve * dir
    else
        beam.CurveSize0 = 0
        beam.CurveSize1 = 0
    end

    beam.FaceCamera = beamFaceCameraEnabled

    if beamZOffsetEnabled then
        beam.ZOffset = beamZOffset
    else
        beam.ZOffset = 0
    end
    if beamSegmentsEnabled then
        beam.Segments = beamSegments
    end
    if beamFlipEnabled then
        local sx = beamFlipX and -1 or 1
        local sy = beamFlipY and -1 or 1

        beam.TextureSpeed = textureSpeed * sx
        beam.Width0 = beam.Width0 * sy
    end
    if not beamRainbowEnabled then
        if beamColorEnabled then
            beam.Color = ColorSequence.new(beamColor0, beamColor1)
        end
    end
    if beamLightEnabled then
        local pl = beamPart:FindFirstChild('<b>UNSTABLE</b>_BeamLight') or Instance.new('PointLight')

        pl.Name = '<b>UNSTABLE</b>_BeamLight'
        pl.Color = beamLightColor
        pl.Range = beamLightRange
        pl.Brightness = beamLightBrightness
        pl.Parent = beamPart
    else
        local pl = beamPart:FindFirstChild('<b>UNSTABLE</b>_BeamLight')

        if pl then
            pl:Destroy()
        end
    end

    updateShadowBeam()
    updateMirrorBeam()
end
function applyGrabSound()
    local gp = getGrabPart()

    if not gp then
        return
    end

    local s = gp:FindFirstChild('AttachSound')

    if not s or not s:IsA('Sound') then
        return
    end

    captureOriginalSoundId(s)

    local custom = toAssetId(customGrabSoundId)

    if custom ~= '' then
        setSoundId(s, custom)
    else
        restoreOriginalSound(s)
    end
end
function setRainbowBeam(state)
    beamRainbowEnabled = state

    if rainbowConn then
        rainbowConn:Disconnect()

        rainbowConn = nil
    end
    if not state then
        return
    end

    rainbowConn = RunService.Heartbeat:Connect(function(dt)
        beamRainbowHue = (beamRainbowHue + dt * beamRainbowSpeed) % 1

        local c = Color3.fromHSV(beamRainbowHue, 1, 1)
        local beamPart = getBeamPart()

        if not beamPart then
            return
        end

        local beam = beamPart:FindFirstChild('GrabBeam')

        if beam and beam:IsA('Beam') then
            beam.Color = ColorSequence.new(c, c)
        end
        if mirrorBeamObj and mirrorBeamObj.Parent then
            mirrorBeamObj.Color = ColorSequence.new(c, c)
        end
    end)
end
function connectHeartbeat()
    if heartbeatConnection then
        return
    end

    heartbeatConnection = RunService.Heartbeat:Connect(function(dt)
        applyLineSettings()

        syncAccumulator += dt

        if syncAccumulator >= 0.15 then
            syncAccumulator = 0

            applyGrabSound()
        end
    end)
end

workspace.DescendantAdded:Connect(function(d)
    if d:IsA('Beam') and d.Name == 'GrabBeam' then
        task.defer(applyLineSettings)
    elseif d:IsA('Sound') and d.Name == 'AttachSound' then
        captureOriginalSoundId(d)
        task.defer(applyGrabSound)
    elseif d.Name == 'GrabPart' or d.Name == 'BeamPart' then
        task.defer(function()
            applyLineSettings()
            applyGrabSound()
        end)
    end
end)

local LineGroup = Tabs.Grab:AddLeftGroupbox("Line", "link")
local PanelAssist = Tabs.Grab:AddLeftGroupbox("Gamepass", "dollar-sign")

LineGroup:AddDropdown("TextureDropdown", {
    Text = "Decal",
    Values = textureValues,
    Default = 1,
    Callback = function(v)
        selectedTexture = v

        connectHeartbeat()
        applyLineSettings()
    end,
})
LineGroup:AddCheckbox("UseSelectedTexture", {
    Text = "Enable",
    Callback = function(v)
        useSelectedTexture = v

        if v then
            useCustomTexture = false
        end

        connectHeartbeat()
        applyLineSettings()
    end,
})
LineGroup:AddCheckbox("EnableBeamColor", {
    Text = "Colors",
    Callback = function(v)
        beamColorEnabled = v

        applyLineSettings()
    end,
})
LineGroup:AddLabel("Start"):AddColorPicker('BeamColor0', {
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(c)
        beamColor0 = c

        applyLineSettings()
    end,
})
LineGroup:AddLabel("End"):AddColorPicker('BeamColor1', {
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(c)
        beamColor1 = c

        applyLineSettings()
    end,
})
LineGroup:AddCheckbox("Rainbow", {
    Text = "Rainbow",
    Callback = function(v)
        setRainbowBeam(v)
    end,
})

PanelAssist:AddCheckbox("ReachToggle", {
    Text = "Free Gamepass",
    Tooltip = '<b>[XENO / SOLARA DOESNT WORK]</b> Gives you 30 studs of reach instantly',
    Default = false,
})
Toggles.ReachToggle:OnChanged(function(state)
    if state then
        local Reach = Instance.new("BoolValue")

        Reach.Name = "FartherReach"
        Reach.Parent = game.Players.LocalPlayer
        Reach.Value = true

        local Notifier = game.ReplicatedStorage.GamepassEvents:FindFirstChild("FurtherReachBoughtNotifier")

        if Notifier then
            for _, connection in ipairs(getconnections(Notifier.OnClientEvent))do
                pcall(connection.Function)
            end
        end
    else
        local Reach = game.Players.LocalPlayer:FindFirstChild("FartherReach")

        if Reach then
            Reach:Destroy()
        end
    end
end)

local GrabReachEnabled = false
local GrabReachDist = 25

function ApplyGrabReach(range)
    GrabReachDist = range

    pcall(function()
        RS.DataEvents.UpdateLineColorsEvent:FireServer(ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 0, 0)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 255, 195)),
        }))
    end)
    pcall(function()
        for _, conn in pairs(getconnections(RS.GamepassEvents.FurtherReachBoughtNotifier.OnClientEvent))do
            for i in debug.getupvalues(conn.Function)do
                debug.setupvalue(conn.Function, i, range)
            end
        end
    end)
end

PanelAssist:AddCheckbox("GrabReachToggle", {
    Text = "Further Reach",
    Tooltip = "Extends your grab distance beyond default",
    Default = false,
    Callback = function(v)
        GrabReachEnabled = v

        if v then
            ApplyGrabReach(GrabReachDist)
        else
            ApplyGrabReach(25)
        end
    end,
})
PanelAssist:AddSlider("GrabReachSlider", {
    Text = "Reach Distance",
    Default = 25,
    Min = 25,
    Max = 45,
    Rounding = 0,
    Callback = function(v)
        GrabReachDist = v

        if GrabReachEnabled then
            ApplyGrabReach(v)
        end
    end,
})

PanelTools = Tabs.Grab:AddRightGroupbox("Grabs", "hand")

SvcRS = game:GetService('ReplicatedStorage')
SvcWS = game:GetService('Workspace')
SvcRun = game:GetService('RunService')
SvcInput = game:GetService('UserInputService')
SvcDebris = game:GetService('Debris')
SvcPlayers = game:GetService('Players')
SvcTween = game:GetService('TweenService')
SvcCtx = game:GetService('ContextActionService')
SvcLight = game:GetService('Lighting')
Me = SvcPlayers.LocalPlayer
Cam = SvcWS.CurrentCamera
GrabFolder = SvcRS:WaitForChild('GrabEvents')
EvSetOwner = GrabFolder:WaitForChild('SetNetworkOwner')
EvDestroyLine = GrabFolder:WaitForChild('DestroyGrabLine')
EvCreateLine = GrabFolder:WaitForChild('CreateGrabLine')
EvExtendLine = GrabFolder:WaitForChild('ExtendGrabLine')
FnSpawnToy = SvcRS:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
EvDestroyToy = SvcRS:WaitForChild('MenuToys'):WaitForChild('DestroyToy')
ToysFolder = SvcWS:WaitForChild(Me.Name .. 'SpawnedInToys')

function LocalChar()
    return Me.Character
end
function LocalRoot()
    local c = LocalChar()

    return c and c:FindFirstChild('HumanoidRootPart')
end
function GrabbedPart()
    local gf = SvcWS:FindFirstChild('GrabParts')
    local gp = gf and gf:FindFirstChild('GrabPart')
    local w = gp and gp:FindFirstChild('WeldConstraint')

    return w and w.Part1 or nil
end
function GrabbedChar()
    local p = GrabbedPart()

    return p and p.Parent or nil
end
function InPlot(plr)
    local pi = SvcWS:FindFirstChild('PlotItems')
    local pp = pi and pi:FindFirstChild('PlayersInPlots')

    return pp and pp:FindFirstChild(plr.Name) ~= nil
end
function PlayerList()
    local t = {}

    for _, p in ipairs(SvcPlayers:GetPlayers())do
        if p ~= Me then
            table.insert(t, p.DisplayName .. ' (@' .. p.Name .. ')')
        end
    end

    if #t == 0 then
        table.insert(t, 'No players')
    end

    return t
end
function DoSpawnToy(name, cf)
    local r = LocalRoot()

    if not r then
        return nil
    end

    return FnSpawnToy:InvokeServer(name, cf or r.CFrame * CFrame.new(5, 5, 20), Vector3.zero)
end
function ClaimOwner(part)
    local r = LocalRoot()

    if r and part and part:IsA('BasePart') then
        EvSetOwner:FireServer(part, r.CFrame)
    end
end

GrabOptions = {
    'Throw on Release',
    'Noclip Grab',
    'Ragdoll Grab',
    'Unweld Grab',
    'Kill Grab',
    'Kick Grab',
    'Loop Grab',
    'Anchor Grab',
    'Massless Grab',
    'Invisible Grab',
    'Heavy Objects Grab',
    'Trigger Bot',
    'Extend Line',
}
GrabEnabled = {}
GrabMasterOn = false
ThrowConn = nil
ThrowForce = 750
_G.strength = ThrowForce
UnweldConn = nil
SpinActive = false
SpinConn = nil
SpinSpeed = 20
AnchorCoro = nil
AnchorParts = {}
AnchorConns = {}

local MasslessSense = 30

MasslessConn = nil
TBReady = true
TBLastTarget = nil
TBLastHit = 0
TBConn = nil
TBDist = 20
TBThrottle = 0.008
TBLastCheck = 0
TBMemory = 0.1
TBPreDelay = 0.00001
TBPostDelay = 0.05
TBRayPrms = RaycastParams.new()
TBRayPrms.FilterType = Enum.RaycastFilterType.Exclude

local InvisLineOn = false

lineDistanceV = 11
increaseLineExtendV = 7
infLineExtendT = false

local extendInputConn = nil
local extendChildConn = nil

kg_Players = game:GetService('Players')
kg_ReplicatedStorage = game:GetService('ReplicatedStorage')
kg_UserInputService = game:GetService('UserInputService')
kg_RunService = game:GetService('RunService')
kg_plr = kg_Players.LocalPlayer
kg_camera = workspace.CurrentCamera
kg_GrabEvents = kg_ReplicatedStorage:WaitForChild('GrabEvents')
kg_CreateGrabLine = kg_GrabEvents:WaitForChild('CreateGrabLine')
kg_SetNetworkOwner = kg_GrabEvents:WaitForChild('SetNetworkOwner')
kg_DestroyGrabLine = kg_GrabEvents:WaitForChild('DestroyGrabLine')
kg_Active = false
kg_FKeyActive = false
kg_fAttackTarget = nil
kg_fAttackConn = nil
kg_mainThread = nil
kg_inputConn = nil
lg_Players = game:GetService('Players')
lg_ReplicatedStorage = game:GetService('ReplicatedStorage')
lg_UserInputService = game:GetService('UserInputService')
lg_RunService = game:GetService('RunService')
lg_plr = lg_Players.LocalPlayer
lg_SetNetworkOwner = lg_ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')
lg_Active = false
lg_FTargetActive = false
lg_fGrabConn = nil
lg_mainThread = nil
lg_inputConn = nil

function IsDescOf(target, ancestor)
    local c = target.Parent

    while c do
        if c == ancestor then
            return true
        end

        c = c.Parent
    end

    return false
end
function CleanConns(t)
    for _, c in ipairs(t)do
        if c and c.Connected then
            c:Disconnect()
        end
    end

    table.clear(t)
end
function MakeAnchorHighlight(parent)
    local h = Instance.new('Highlight')

    h.DepthMode = Enum.HighlightDepthMode.Occluded
    h.FillTransparency = 1
    h.Name = 'GrabAnchorHL'
    h.OutlineColor = Color3.new(0, 0, 1)
    h.OutlineTransparency = 0.5
    h.Parent = parent

    return h
end
function MakeMovers(part, pos, rot)
    local bp = Instance.new('BodyPosition')

    bp.P = 15000
    bp.D = 200
    bp.MaxForce = Vector3.new(5e6, 5e6, 5e6)
    bp.Position = pos
    bp.Parent = part

    local bg = Instance.new('BodyGyro')

    bg.P = 15000
    bg.D = 200
    bg.MaxTorque = Vector3.new(5e6, 5e6, 5e6)
    bg.CFrame = rot
    bg.Parent = part
end
function ApplyThrow(v)
    if ThrowConn then
        ThrowConn:Disconnect()

        ThrowConn = nil
    end
    if not v then
        return
    end

    ThrowConn = SvcWS.ChildAdded:Connect(function(m)
        if m.Name ~= 'GrabParts' then
            return
        end

        local gp = m:FindFirstChild('GrabPart')
        local w = gp and gp:FindFirstChild('WeldConstraint')
        local tgt = w and w.Part1

        if not tgt then
            return
        end

        local bv = Instance.new('BodyVelocity', tgt)

        m:GetPropertyChangedSignal('Parent'):Connect(function()
            if m.Parent then
                return
            end
            if SvcInput:GetLastInputType() == Enum.UserInputType.MouseButton2 then
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Velocity = Cam.CFrame.LookVector * ThrowForce

                SvcDebris:AddItem(bv, 1)
            else
                bv:Destroy()
            end
        end)
    end)
end
function ApplyNoclip(v)
    if getgenv().ConnNoclipGrab then
        getgenv().ConnNoclipGrab:Disconnect()

        getgenv().ConnNoclipGrab = nil
    end
    if not v then
        local c = GrabbedChar()

        if c then
            for _, p in ipairs(c:GetDescendants())do
                if p:IsA('BasePart') and not p.Anchored then
                    p.CanCollide = true
                end
            end
        end

        return
    end

    getgenv().ConnNoclipGrab = SvcRun.Heartbeat:Connect(function()
        local c = GrabbedChar()

        if not c then
            return
        end

        for _, p in ipairs(c:GetDescendants())do
            if p:IsA('BasePart') and not p.Anchored then
                p.CanCollide = false
            end
        end
    end)
end
function ApplyRagdoll(v)
    if v then
        local pal, pal2

        pal2 = plr.PlayerGui.MenuGui.Menu.TabContents.ToyDestroy.Contents.ChildAdded:Connect(function(c)
            if c.Name == 'PalletLightBrown' then
                pal = c

                task.wait()
                pal2:Disconnect()

                pal2 = nil
            end
        end)

        local ragd = spawntoy('PalletLightBrown', HRP.CFrame * CFrame.new(5, 5, 20))
        local partt = ragd:WaitForChild('SoundPart', 0.1)

        ragd.Name = 'ragdoll'

        spawn(function()
            task.wait(1)

            local lbl = pal.ViewItemButton.NewMessage:Clone()

            lbl.Name = 'Ragdoll'
            lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
            lbl.Text = 'Ragdoll Grab'
            lbl.Visible = true
            lbl.Parent = pal.ViewItemButton
        end)

        repeat
            sno(partt)
            task.wait()
        until partt:FindFirstChild('PartOwner')

        partt.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)

        spawn(function()
            for _, v2 in pairs(ragd:GetDescendants())do
                if v2:IsA('Part') then
                    v2.Transparency = 1
                    v2.CanCollide = false
                end
            end
        end)

        cons.rgarab1 = workspace.ChildAdded:Connect(function(c)
            if c.Name ~= 'GrabParts' then
                return
            end

            local part = c:FindFirstChild('GrabPart') or c:WaitForChild('GrabPart', 3)

            if not part then
                return
            end

            local obj = part.WeldConstraint.Part1

            while workspace:FindFirstChild('GrabParts') and task.wait() do
                if obj and obj.Parent and obj.Parent:FindFirstChild('HumanoidRootPart') and obj.Parent:FindFirstChild('Humanoid') and obj.Parent.Humanoid:FindFirstChild('Ragdolled') and obj.Parent.Humanoid.Ragdolled.Value == false then
                    spawn(function()
                        partt.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                        partt.CFrame = obj.Parent.HumanoidRootPart.CFrame

                        task.wait(0.05)

                        partt.CFrame = CFrame.new(0, 1e9, 0)
                    end)
                end
            end
        end)
    else
        if cons.rgarab1 then
            cons.rgarab1:Disconnect()
        end

        pcall(function()
            DestroyToy:FireServer(inv.ragdoll)
        end)
    end
end
function ApplyUnweld(v)
    if UnweldConn then
        UnweldConn:Disconnect()

        UnweldConn = nil
    end
    if not v then
        return
    end

    UnweldConn = workspace.ChildAdded:Connect(function(model)
        if model.Name ~= 'GrabParts' then
            return
        end

        local grabPart = model:FindFirstChild('GrabPart')
        local weld2 = grabPart and grabPart:FindFirstChildOfClass('WeldConstraint')
        local partToTP = weld2 and weld2.Part1

        if not partToTP or not partToTP:IsA('BasePart') or partToTP.Anchored then
            return
        end

        task.wait(0.01)
        pcall(function()
            partToTP.CFrame = CFrame.new(partToTP.Position.X, -9999, partToTP.Position.Z)
        end)
    end)
end
function ApplyKill(v)
    KillOnGrab = v
end
function ApplySpin(v)
    SpinActive = v

    if SpinConn then
        SpinConn:Disconnect()

        SpinConn = nil
    end
    if not v then
        return
    end

    SpinConn = SvcWS.ChildAdded:Connect(function(e)
        if e.Name ~= 'GrabParts' or not e:FindFirstChild('GrabPart') then
            return
        end

        local dp = SvcWS.GrabParts and SvcWS.GrabParts:FindFirstChild('DragPart')

        if dp then
            local ao = dp:FindFirstChild('AlignOrientation')

            if ao then
                ao:Destroy()
            end
        end

        local w = e.GrabPart:FindFirstChild('WeldConstraint')
        local p1 = w and w.Part1

        if not p1 then
            return
        end

        while SvcWS:FindFirstChild('GrabParts') and SpinActive do
            p1.AssemblyAngularVelocity = Vector3.new(0, SpinSpeed, 0)

            task.wait()
        end
    end)
end
function ApplyHeavy(v)
    function SetForce(amount)
        local rf = game:FindFirstChild('ReplicatedFirst')
        local rfGrab = rf and rf:FindFirstChild('GrabParts')

        if rfGrab and rfGrab:FindFirstChild('DragPart') then
            local dp = rfGrab.DragPart

            if dp:FindFirstChild('AlignPosition') then
                dp.AlignPosition.MaxForce = amount
            end
            if dp:FindFirstChild('AlignOrientation') then
                dp.AlignOrientation.MaxTorque = amount
            end
        end

        local wp = workspace:FindFirstChild('GrabParts')

        if wp and wp:FindFirstChild('DragPart') then
            local dp = wp.DragPart

            if dp:FindFirstChild('AlignPosition') then
                dp.AlignPosition.MaxForce = amount
            end
            if dp:FindFirstChild('AlignOrientation') then
                dp.AlignOrientation.MaxTorque = amount
            end
        end
    end

    SetForce(v and math.huge or 60000)
end
function AnchorLoop()
    while true do
        pcall(function()
            local gf = SvcWS:FindFirstChild('GrabParts')

            if not gf then
                return
            end

            local gp = gf:FindFirstChild('GrabPart')

            if not gp then
                return
            end

            local w = gp:FindFirstChild('WeldConstraint')

            if not w or not w.Part1 then
                return
            end

            local prim = w.Part1.Parent and w.Part1.Parent.PrimaryPart

            if not prim then
                return
            end
            if IsDescOf(prim, SvcWS.Map) then
                return
            end

            for _, p in pairs(SvcPlayers:GetChildren())do
                if p.Character and IsDescOf(prim, p.Character) then
                    return
                end
            end

            if not table.find(AnchorParts, prim) then
                MakeAnchorHighlight(prim.Parent)
                table.insert(AnchorParts, prim)

                local conn = prim.Parent.DescendantAdded:Connect(function(d)
                    if d.Name ~= 'PartOwner' then
                        return
                    end

                    local h = prim.Parent:FindFirstChild('GrabAnchorHL')

                    if not h then
                        return
                    end

                    h.OutlineColor = d.Value ~= Me.Name and Color3.new(1, 0, 0) or Color3.new(0, 0, 1)
                end)

                table.insert(AnchorConns, conn)
            end

            for _, child in ipairs(prim:GetChildren())do
                if child:IsA('BodyPosition') or child:IsA('BodyGyro') then
                    child:Destroy()
                end
            end

            while SvcWS:FindFirstChild('GrabParts') do
                task.wait()
            end

            MakeMovers(prim, prim.Position, prim.CFrame)
        end)
        task.wait()
    end
end
function ReleaseAllAnchor()
    for _, part in ipairs(AnchorParts)do
        if part then
            local bp = part:FindFirstChild('BodyPosition')
            local bg = part:FindFirstChild('BodyGyro')
            local h = part.Parent and part.Parent:FindFirstChild('GrabAnchorHL')

            if bp then
                bp:Destroy()
            end
            if bg then
                bg:Destroy()
            end
            if h then
                h:Destroy()
            end
        end
    end

    CleanConns(AnchorConns)
    table.clear(AnchorParts)
end
function ApplyAnchor(v)
    if v then
        if not AnchorCoro or coroutine.status(AnchorCoro) == 'dead' then
            AnchorCoro = coroutine.create(AnchorLoop)

            coroutine.resume(AnchorCoro)
        end
    else
        if AnchorCoro and coroutine.status(AnchorCoro) ~= 'dead' then
            coroutine.close(AnchorCoro)

            AnchorCoro = nil
        end

        ReleaseAllAnchor()
    end
end
function ApplyMassless(v)
    if MasslessConn then
        MasslessConn:Disconnect()

        MasslessConn = nil
    end
    if not v then
        return
    end

    MasslessConn = SvcWS.ChildAdded:Connect(function(m)
        if m.Name ~= 'GrabParts' then
            return
        end

        local dp = m:FindFirstChild('DragPart')

        if not dp then
            return
        end

        local ap = dp:FindFirstChild('AlignPosition')
        local ao = dp:FindFirstChild('AlignOrientation')

        if not (ap and ao) then
            return
        end

        pcall(function()
            ap.Responsiveness = MasslessSense
            ap.MaxForce = math.huge
            ap.MaxVelocity = math.huge
            ao.Responsiveness = MasslessSense
            ao.MaxTorque = math.huge
        end)
    end)
end
function TBFindTarget()
    local c = LocalChar()

    if not c or not c:FindFirstChild('HumanoidRootPart') then
        return
    end
    if SvcWS:FindFirstChild('GrabParts') then
        return
    end

    local origin = Cam.CFrame.Position
    local dir = Cam.CFrame.LookVector

    TBRayPrms.FilterDescendantsInstances = {
        c,
        SvcWS.Terrain,
    }

    local res

    for _, d in ipairs({
        dir,
        (dir + Vector3.new(0, 0.075, 0)).Unit,
        (dir + Vector3.new(0, -7.5E-2, 0)).Unit,
    })do
        res = SvcWS:Raycast(origin, d * 1000, TBRayPrms)

        if res then
            break
        end
    end

    if not res then
        return
    end

    local m = res.Instance:FindFirstAncestorOfClass('Model')

    if not m or m == c then
        return
    end

    local hum = m:FindFirstChildOfClass('Humanoid')
    local root = m:FindFirstChild('HumanoidRootPart')

    if not hum or hum.Health <= 0 or not root then
        return
    end
    if (c.HumanoidRootPart.Position - root.Position).Magnitude > TBDist then
        return
    end

    return m
end
function ApplyTriggerBot(v)
    if TBConn then
        TBConn:Disconnect()

        TBConn = nil
    end
    if not v then
        return
    end

    TBConn = SvcRun.Heartbeat:Connect(function()
        if not TBReady then
            return
        end
        if SvcInput:GetFocusedTextBox() then
            return
        end
        if tick() - TBLastCheck < TBThrottle then
            return
        end

        TBLastCheck = tick()

        local t = TBFindTarget()

        if t then
            TBLastTarget = t
            TBLastHit = tick()
        elseif TBLastTarget and tick() - TBLastHit > TBMemory then
            TBLastTarget = nil
        end

        local c = LocalChar()
        local root = TBLastTarget and TBLastTarget:FindFirstChild('HumanoidRootPart')

        if not (TBLastTarget and c and c:FindFirstChild('HumanoidRootPart') and root) then
            return
        end
        if (c.HumanoidRootPart.Position - root.Position).Magnitude > TBDist then
            TBLastTarget = nil

            return
        end

        TBReady = false

        task.spawn(function()
            task.wait(TBPreDelay)
            pcall(mouse1press)

            local t0 = tick()

            repeat
                task.wait(0.02)
            until not SvcWS:FindFirstChild('GrabParts') or tick() - t0 > 1.6

            task.wait(TBPostDelay)

            TBReady = true
            TBLastTarget = nil
        end)
    end)
end
function ApplyInvisLine(v)
    InvisLineOn = v
    _G.InvisLine = v or nil

    if v then
        task.spawn(function()
            while InvisLineOn do
                EvCreateLine:FireServer()
                task.wait()
            end
        end)
    end
end
function startExtendLine()
    stopExtendLine()

    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:WaitForChild('HumanoidRootPart')
    local hum = char:WaitForChild('Humanoid')

    extendInputConn = UserInputService.InputChanged:Connect(function(input)
        if not infLineExtendT then
            return
        end
        if input.UserInputType == Enum.UserInputType.MouseWheel then
            if lineDistanceV < 11 then
                lineDistanceV = 11
            end
            if input.Position.Z > 0 then
                lineDistanceV += increaseLineExtendV
            elseif input.Position.Z < 0 then
                lineDistanceV -= increaseLineExtendV
            end
        end
    end)
    extendChildConn = workspace.ChildAdded:Connect(function(child)
        if not infLineExtendT then
            return
        end
        if child.Name ~= 'GrabParts' or not child:IsA('Model') then
            return
        end

        local grabPartsModel = child
        local dragPart = grabPartsModel:WaitForChild('DragPart')
        local clonedDragPart = dragPart:Clone()

        clonedDragPart.Name = 'DragPart1'

        local attach = clonedDragPart:FindFirstChild('DragAttach')

        if clonedDragPart:FindFirstChild('AlignPosition') and attach then
            clonedDragPart.AlignPosition.Attachment1 = attach
        end

        clonedDragPart.Parent = grabPartsModel

        local camera = workspace.CurrentCamera

        lineDistanceV = (clonedDragPart.Position - camera.CFrame.Position).Magnitude

        if clonedDragPart:FindFirstChild('AlignOrientation') then
            clonedDragPart.AlignOrientation.Enabled = false
        end
        if dragPart:FindFirstChild('AlignPosition') then
            dragPart.AlignPosition.Enabled = false
        end

        task.spawn(function()
            while grabPartsModel.Parent and infLineExtendT do
                clonedDragPart.Position = camera.CFrame.Position + camera.CFrame.LookVector * lineDistanceV

                RunService.RenderStepped:Wait()
            end

            lineDistanceV = 11
        end)
    end)
end
function stopExtendLine()
    if extendInputConn then
        extendInputConn:Disconnect()

        extendInputConn = nil
    end
    if extendChildConn then
        extendChildConn:Disconnect()

        extendChildConn = nil
    end

    lineDistanceV = 11
end
function ApplyExtendLine(v)
    infLineExtendT = v

    if v then
        startExtendLine()
    else
        stopExtendLine()
    end
end
function ApplyLoopGrab(on)
    Toggles.LoopGrabToggle:SetValue(on)
end
function ApplyKickGrab(on)
    Toggles.KickGrabToggle:SetValue(on)
end

local ApplyFns = {
    ['Throw on Release'] = ApplyThrow,
    ['Noclip Grab'] = ApplyNoclip,
    ['Ragdoll Grab'] = ApplyRagdoll,
    ['Unweld Grab'] = ApplyUnweld,
    ['Kill Grab'] = ApplyKill,
    ['Kick Grab'] = ApplyKickGrab,
    ['Loop Grab'] = ApplyLoopGrab,
    ['Heavy Objects Grab'] = ApplyHeavy,
    ['Anchor Grab'] = ApplyAnchor,
    ['Massless Grab'] = ApplyMassless,
    ['Invisible Grab'] = ApplyInvisLine,
    ['Trigger Bot'] = ApplyTriggerBot,
    ['Extend Line'] = ApplyExtendLine,
}

SvcWS.ChildAdded:Connect(function(c)
    if not KillOnGrab then
        return
    end
    if not (c:IsA('Model') and c.Name == 'GrabParts') then
        return
    end

    task.wait(0.05)

    local gp = c:FindFirstChild('GrabPart')
    local w = gp and gp:FindFirstChild('WeldConstraint')
    local tgt = w and w.Part1

    if not tgt or tgt.Parent == Me.Character then
        return
    end

    local hum = tgt.Parent:FindFirstChildOfClass('Humanoid')

    if not hum then
        return
    end

    pcall(function()
        hum.Health = 0

        tgt.Parent:BreakJoints()
    end)
end)
PanelTools:AddCheckbox('cbGrabMaster', {
    Text = 'Enable Grabs',
    Tooltip = 'Instantly enables or disables every selected grab',
    Default = false,
}):OnChanged(function(v)
    GrabMasterOn = v

    for name, active in pairs(GrabEnabled)do
        local fn = ApplyFns[name]

        if fn then
            fn(v and active)
        end
    end
end)
PanelTools:AddDivider()
PanelTools:AddDropdown('ddGrabModes', {
    Text = 'Active Grabs',
    Tooltip = 'Pick one or more modes. All selected grabs run together when the master toggle is ON',
    Values = GrabOptions,
    Default = {},
    Multi = true,
}):OnChanged(function(selectedMap)
    for _, name in ipairs(GrabOptions)do
        local wanted = selectedMap[name] == true
        local current = GrabEnabled[name] == true

        if wanted ~= current then
            GrabEnabled[name] = wanted

            if GrabMasterOn then
                local fn = ApplyFns[name]

                if fn then
                    fn(wanted)
                end
            end
        end
    end
end)
PanelTools:AddDivider()
PanelTools:AddCheckbox('LoopGrabToggle', {
    Text = 'Loop Grab',
    Default = false,
})
Toggles.LoopGrabToggle:OnChanged(function(state)
    if not state then
        getgenv().LoopGrabActive = false
        getgenv().FTargetGrabActive = false

        if getgenv().fGrabConnection then
            getgenv().fGrabConnection:Disconnect()

            getgenv().fGrabConnection = nil
        end
        if getgenv().LoopGrabInputConnection then
            getgenv().LoopGrabInputConnection:Disconnect()

            getgenv().LoopGrabInputConnection = nil
        end

        return
    end
    if getgenv().LoopGrabActive then
        return
    end

    getgenv().LoopGrabActive = true

    local Players = game:GetService('Players')
    local ReplicatedStorage = game:GetService('ReplicatedStorage')
    local UserInputService = game:GetService('UserInputService')
    local RunService = game:GetService('RunService')
    local plr = Players.LocalPlayer
    local SetNetworkOwner = ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')

    task.spawn(function()
        while getgenv().LoopGrabActive do
            local grabParts = workspace:FindFirstChild('GrabParts')

            if not grabParts then
                task.wait()

                continue
            end

            local gp = grabParts:FindFirstChild('GrabPart')
            local weld = gp and gp:FindFirstChildOfClass('WeldConstraint')
            local part1 = weld and weld.Part1

            if part1 then
                local ownerPlayer = nil

                for _, pl in ipairs(Players:GetPlayers())do
                    if pl.Character and part1:IsDescendantOf(pl.Character) then
                        ownerPlayer = pl

                        break
                    end
                end

                while getgenv().LoopGrabActive and workspace:FindFirstChild('GrabParts') do
                    if ownerPlayer then
                        local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('HumanoidRootPart')
                        local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('Head')
                        local myTorso = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

                        if tgtTorso and myTorso and tgtHead then
                            pcall(function()
                                SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position))
                            end)
                        end
                    else
                        if part1.Parent then
                            local myTorso = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

                            if myTorso then
                                pcall(function()
                                    SetNetworkOwner:FireServer(part1, CFrame.lookAt(myTorso.Position, part1.Position))
                                end)
                            end
                        end
                    end

                    task.wait()
                end
            end

            task.wait()
        end
    end)

    if game.PlaceId == 6961824067 then
        local G = game:GetService('ReplicatedStorage'):WaitForChild('GrabEvents')
        local existingEndGrab = G:FindFirstChild('EndGrabEarly')

        if existingEndGrab then
            existingEndGrab:Destroy()
        end

        Instance.new('RemoteEvent', G).Name = 'EndGrabEarly'
    end

    getgenv().FTargetGrabActive = false
    getgenv().fGrabConnection = nil

    function getCenterTarget()
        local cam = workspace.CurrentCamera
        local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
        local myTorso = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

        if not myTorso then
            return nil
        end

        local closestTarget = nil
        local minScreenDist = math.huge

        for _, p in ipairs(Players:GetPlayers())do
            if p ~= plr and p.Character and p.Character:FindFirstChild('HumanoidRootPart') then
                local tgtTorso = p.Character.HumanoidRootPart
                local dist = (tgtTorso.Position - myTorso.Position).Magnitude

                if dist <= 25 then
                    local screenPos, onScreen = cam:WorldToViewportPoint(tgtTorso.Position)

                    if onScreen then
                        local screenDist = (Vector2.new(screenPos.X, screenPos.Y) - center).Magnitude

                        if screenDist < minScreenDist then
                            minScreenDist = screenDist
                            closestTarget = p
                        end
                    end
                end
            end
        end

        return closestTarget
    end

    if getgenv().LoopGrabInputConnection then
        getgenv().LoopGrabInputConnection:Disconnect()

        getgenv().LoopGrabInputConnection = nil
    end

    getgenv().LoopGrabInputConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then
            return
        end
        if not getgenv().LoopGrabActive then
            return
        end
        if input.KeyCode == Enum.KeyCode.F then
            if getgenv().FTargetGrabActive then
                getgenv().FTargetGrabActive = false

                if getgenv().fGrabConnection then
                    getgenv().fGrabConnection:Disconnect()

                    getgenv().fGrabConnection = nil
                end
            else
                local target = getCenterTarget()

                if target then
                    getgenv().FTargetGrabActive = true

                    local cam = workspace.CurrentCamera

                    getgenv().fGrabConnection = RunService.RenderStepped:Connect(function()
                        if not getgenv().FTargetGrabActive or not target or not target.Character then
                            getgenv().FTargetGrabActive = false

                            if getgenv().fGrabConnection then
                                getgenv().fGrabConnection:Disconnect()

                                getgenv().fGrabConnection = nil
                            end

                            return
                        end

                        local tgtTorso = target.Character:FindFirstChild('HumanoidRootPart')
                        local tgtHead = target.Character:FindFirstChild('Head')
                        local myTorso = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

                        if tgtTorso and myTorso and tgtHead then
                            SetNetworkOwner:FireServer(tgtTorso, tgtTorso.CFrame)

                            if tgtHead:FindFirstChild('PartOwner') then
                                local targetPos = cam.CFrame.Position + (cam.CFrame.LookVector * 23)
                                local newCFrame = CFrame.new(targetPos)

                                tgtTorso.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                                tgtTorso.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
                                tgtTorso.CFrame = newCFrame

                                SetNetworkOwner:FireServer(tgtTorso, newCFrame)
                            end
                        end
                    end)
                end
            end
        end
    end)
end)
PanelTools:AddCheckbox('KickGrabToggle', {
    Text = 'Kick Grab',
    Default = false,
})
Toggles.KickGrabToggle:OnChanged(function(state)
    if not state then
        getgenv().KickGrabActive = false
        getgenv().FKeyAttackActive = false

        if getgenv().FKeyInputConnection then
            getgenv().FKeyInputConnection:Disconnect()

            getgenv().FKeyInputConnection = nil
        end

        return
    end
    if getgenv().KickGrabActive then
        return
    end

    getgenv().KickGrabActive = true
    getgenv().FKeyAttackActive = false

    local Players = game:GetService('Players')
    local ReplicatedStorage = game:GetService('ReplicatedStorage')
    local UserInputService = game:GetService('UserInputService')
    local RunService = game:GetService('RunService')
    local plr = Players.LocalPlayer
    local camera = workspace.CurrentCamera
    local GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents')
    local CreateGrabLine = GrabEvents:WaitForChild('CreateGrabLine')
    local SetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
    local DestroyGrabLine = GrabEvents:WaitForChild('DestroyGrabLine')

    task.spawn(function()
        while getgenv().KickGrabActive do
            local grabParts = workspace:FindFirstChild('GrabParts')

            if not grabParts then
                task.wait()

                continue
            end

            local gp = grabParts:FindFirstChild('GrabPart')
            local weld = gp and gp:FindFirstChildOfClass('WeldConstraint')
            local part1 = weld and weld.Part1

            if part1 then
                local ownerPlayer = nil

                for _, pl in ipairs(Players:GetPlayers())do
                    if pl.Character and part1:IsDescendantOf(pl.Character) then
                        ownerPlayer = pl

                        break
                    end
                end

                if not ownerPlayer then
                    task.wait()

                    continue
                end

                while getgenv().KickGrabActive and workspace:FindFirstChild('GrabParts') do
                    if ownerPlayer then
                        local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('HumanoidRootPart')
                        local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild('Head')
                        local myTorso = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

                        if tgtTorso and myTorso and tgtHead then
                            pcall(function()
                                SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position))
                            end)
                            task.wait()
                            pcall(function()
                                DestroyGrabLine:FireServer(tgtHead)
                            end)
                        end
                    end

                    task.wait()
                end
            end

            task.wait()
        end
    end)

    function getScreenCenterTarget()
        local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
        local ray = camera:ViewportPointToRay(screenCenter.X, screenCenter.Y)
        local raycastParams = RaycastParams.new()

        raycastParams.FilterType = Enum.RaycastFilterType.Exclude

        if plr.Character then
            raycastParams.FilterDescendantsInstances = {
                plr.Character,
            }
        end

        local result = workspace:Raycast(ray.Origin, ray.Direction * 1000, raycastParams)

        if result and result.Instance then
            for _, pl in ipairs(Players:GetPlayers())do
                if pl ~= plr and pl.Character and result.Instance:IsDescendantOf(pl.Character) then
                    return pl
                end
            end
        end

        return nil
    end

    local fAttackTarget = nil
    local fAttackConnection = nil

    function stopFKeyAttack()
        getgenv().FKeyAttackActive = false
        fAttackTarget = nil

        if fAttackConnection then
            fAttackConnection:Disconnect()

            fAttackConnection = nil
        end
    end
    function startFKeyAttack(targetPlayer)
        getgenv().FKeyAttackActive = true
        fAttackTarget = targetPlayer
        fAttackConnection = RunService.RenderStepped:Connect(function()
            if not getgenv().FKeyAttackActive or not fAttackTarget then
                stopFKeyAttack()

                return
            end

            local myChar = plr.Character
            local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')
            local tgtChar = fAttackTarget.Character
            local tgtRoot = tgtChar and tgtChar:FindFirstChild('HumanoidRootPart')

            if not myRoot or not tgtRoot then
                return
            end

            local camCF = camera.CFrame
            local teleportPos = camCF.Position + camCF.LookVector * 20

            pcall(function()
                tgtRoot.CFrame = CFrame.new(teleportPos)
            end)

            local grabCFrame = CFrame.new(-9.0301513671875E-2, 0.4190945625305176, 0.4999980926513672, 0.39632707834243774, 0, -0.9181094169616699, -1.0944717132588266E-7, 1, -4.7245869438938826E-8, 0.9181094169616699, 5.9604644775390625e-8, 0.39632707834243774)

            pcall(function()
                CreateGrabLine:FireServer(tgtRoot, grabCFrame)
                SetNetworkOwner:FireServer(tgtRoot, CFrame.lookAt(myRoot.Position, tgtRoot.Position))
                DestroyGrabLine:FireServer(tgtRoot)
            end)
        end)
    end

    getgenv().FKeyInputConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then
            return
        end
        if input.KeyCode ~= Enum.KeyCode.F then
            return
        end
        if getgenv().FKeyAttackActive then
            stopFKeyAttack()

            return
        end

        local target = getScreenCenterTarget()

        if not target then
            return
        end

        local myChar = plr.Character
        local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')
        local tgtChar = target.Character
        local tgtRoot = tgtChar and tgtChar:FindFirstChild('HumanoidRootPart')

        if not myRoot or not tgtRoot then
            return
        end

        local distance = (myRoot.Position - tgtRoot.Position).Magnitude

        if distance > 25 then
            return
        end

        startFKeyAttack(target)
    end)

    if game.PlaceId ~= 6961824067 then
        return
    end

    local G = game:GetService('ReplicatedStorage'):WaitForChild('GrabEvents')

    G:WaitForChild('EndGrabEarly'):Destroy()

    Instance.new('RemoteEvent', G).Name = 'EndGrabEarly'
end)


PanelTools:AddDivider()
PanelTools:AddSlider('slThrowForce', {
    Text = 'Throw Force',
    Tooltip = 'How far are the objects thrown?',
    Default = 750,
    Min = 1,
    Max = 20000,
    Rounding = 0,
}):OnChanged(function(v)
    ThrowForce = v
    _G.strength = v
end)
PanelTools:AddSlider('slMasslessSense', {
    Text = 'Line Responsiveness',
    Tooltip = 'Smoothness of grab line movement.',
    Default = 30,
    Min = 1,
    Max = 2000,
    Rounding = 0,
}):OnChanged(function(v)
    MasslessSense = math.max(1, v or MasslessSense)
end)
PanelTools:AddSlider('slExtendLineForce', {
    Text = 'Scroll Step Power',
    Tooltip = 'Distance moved per mouse wheel tick.',
    Default = 7,
    Min = 1,
    Max = 25,
    Rounding = 0,
}):OnChanged(function(v)
    increaseLineExtendV = v
end)
PanelTools:AddSlider('slTBDist', {
    Text = 'Trigger Range',
    Tooltip = 'Maximum distance for auto trigger detection.',
    Default = 20,
    Min = 5,
    Max = 45,
    Rounding = 0,
}):OnChanged(function(v)
    TBDist = v
end)
PanelTools:AddDivider()
PanelTools:AddButton({
    Text = 'Release Anchors',
    Tooltip = 'Clears all frozen / anchored objects.',
    Func = function()
        ReleaseAllAnchor()
        notify('Anchor System', 'All anchored parts released', 3)
    end,
})

local GrabR = Tabs.Grab:AddRightGroupbox("Auras", "cpu")

do
    local telekinesisRunning = false
    local telekinesisCoroutine = nil

    local function startTelekinesisAura()
        telekinesisRunning = true
        telekinesisCoroutine = task.spawn(function()
            while telekinesisRunning do
                local success, err = pcall(function()
                    local char = LocalPlayer.Character
                    if char and char:FindFirstChild("HumanoidRootPart") then
                        local root = char.HumanoidRootPart
                        local cam = workspace.CurrentCamera
                        for _, plr in pairs(Players:GetPlayers()) do
                            if plr ~= LocalPlayer and plr.Character then
                                local targetChar = plr.Character
                                local torso = targetChar:FindFirstChild("Torso") or targetChar:FindFirstChild("UpperTorso")
                                if torso and (torso.Position - root.Position).Magnitude <= 25 then
                                    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(torso, root.CFrame)
                                    for _, part in ipairs(targetChar:GetDescendants()) do
                                        if part:IsA("BasePart") then
                                            part.CanCollide = false
                                        end
                                    end
                                    local bp = torso:FindFirstChild("HellAuraPos") or Instance.new("BodyPosition")
                                    bp.Name = "HellAuraPos"
                                    bp.MaxForce = Vector3.new(100000, 100000, 100000)
                                    bp.D = 500
                                    bp.P = 50000
                                    bp.Parent = torso
                                    local bg = torso:FindFirstChild("HellAuraGyro") or Instance.new("BodyGyro")
                                    bg.Name = "HellAuraGyro"
                                    bg.MaxTorque = Vector3.new(100000, 100000, 100000)
                                    bg.D = 500
                                    bg.P = 50000
                                    bg.Parent = torso
                                    local look = cam.CFrame.LookVector
                                    bp.Position = root.Position + look * 15 + Vector3.new(0, 5, 0)
                                    bg.CFrame = CFrame.new(torso.Position, root.Position)
                                end
                            end
                        end
                    end
                end)
                if not success then
                    warn("Error in Telekinesis Aura: " .. tostring(err))
                end
                task.wait(0.05)
            end
        end)
    end

    local function stopTelekinesisAura()
        telekinesisRunning = false
        if telekinesisCoroutine then
            task.cancel(telekinesisCoroutine)
            telekinesisCoroutine = nil
        end
    end

    GrabR:AddCheckbox("TelekinesisAura", {
        Text = "Telekinesis Aura",
        Default = false,
        Callback = function(Value)
            if Value then
                startTelekinesisAura()
            else
                stopTelekinesisAura()
            end
        end
    })

    local deathAuraRunning = false
    local deathAuraConnection = nil

    local function startDeathAura()
        deathAuraRunning = true
        if deathAuraConnection then
            deathAuraConnection:Disconnect()
            deathAuraConnection = nil
        end
        deathAuraConnection = RunService.Heartbeat:Connect(function()
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Character then
                    local targetRoot = plr.Character:FindFirstChild("HumanoidRootPart")
                    local targetHead = plr.Character:FindFirstChild("Head")
                    local targetHum = plr.Character:FindFirstChildOfClass("Humanoid")
                    if targetRoot and targetHead and targetHum and targetHum.Health > 0 and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        if (targetRoot.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude <= 25 then
                            pcall(function()
                                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(targetRoot, targetRoot.CFrame)
                                task.wait(0.1)
                                ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(targetRoot)
                                if targetHead:FindFirstChild("PartOwner") and targetHead.PartOwner.Value == LocalPlayer.Name then
                                    for _, part in pairs(targetHum.Parent:GetChildren()) do
                                        if part:IsA("BasePart") then
                                            part.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    task.wait()
                                    for _, part in pairs(targetHum.Parent:GetChildren()) do
                                        if part:IsA("BasePart") then
                                            part.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    local bv = Instance.new("BodyVelocity")
                                    bv.Velocity = Vector3.new(0, -9999999, 0)
                                    bv.MaxForce = Vector3.new(9000000000, 9000000000, 9000000000)
                                    bv.P = 100000075
                                    bv.Parent = targetRoot
                                    targetHum.Sit = false
                                    targetHum.Jump = true
                                    targetHum.BreakJointsOnDeath = false
                                    targetHum:ChangeState(Enum.HumanoidStateType.Dead)
                                    task.delay(2, function()
                                        if bv and bv.Parent then
                                            bv:Destroy()
                                        end
                                    end)
                                end
                            end)
                        end
                    end
                end
            end
        end)
    end

    local function stopDeathAura()
        deathAuraRunning = false
        if deathAuraConnection then
            deathAuraConnection:Disconnect()
            deathAuraConnection = nil
        end
    end

    GrabR:AddCheckbox("DeathAura", {
        Text = "Death Aura",
        Default = false,
        Callback = function(Value)
            if Value then
                startDeathAura()
            else
                stopDeathAura()
            end
        end
    })

    local flingAuraRunning = false
    local flingAuraTask = nil

    _G.FlingStrength = 400
    _G.FlingTarget = 1

    local function startFlingAura()
        flingAuraRunning = true
        flingAuraTask = task.spawn(function()
            while flingAuraRunning do
                pcall(function()
                    local localChar = LocalPlayer.Character
                    local localRoot = localChar and localChar:FindFirstChild("HumanoidRootPart")
                    if not localRoot then task.wait(0.1) return end

                    if _G.FlingTarget == 1 or _G.FlingTarget == 3 then
                        for _, otherPlayer in ipairs(Players:GetPlayers()) do
                            if otherPlayer ~= LocalPlayer and otherPlayer.Character then
                                local otherRoot = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                                local otherHum = otherPlayer.Character:FindFirstChildOfClass("Humanoid")
                                if otherRoot and otherHum and otherHum.Health > 0 then
                                    local dist = (otherRoot.Position - localRoot.Position).Magnitude
                                    if dist <= 30 then
                                        pcall(function()
                                            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(otherRoot, otherRoot.CFrame)
                                            if not otherRoot:FindFirstChild("FlingAuraVelocity") then
                                                local bv = Instance.new("BodyVelocity")
                                                bv.Name = "FlingAuraVelocity"
                                                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                                local dir = (otherRoot.Position - localRoot.Position).Unit
                                                bv.Velocity = Vector3.new(dir.X, 0.5, dir.Z) * _G.FlingStrength
                                                bv.Parent = otherRoot
                                                Debris:AddItem(bv, 0.5)
                                            end
                                        end)
                                    end
                                end
                            end
                        end
                    end

                    if _G.FlingTarget == 2 or _G.FlingTarget == 3 then
                        for _, obj in ipairs(workspace:GetDescendants()) do
                            if obj:IsA("BasePart") and obj.CanQuery and not obj:IsDescendantOf(localChar) then
                                local dist = (obj.Position - localRoot.Position).Magnitude
                                if dist <= 28 then
                                    pcall(function()
                                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(obj, obj.CFrame)
                                        if not obj:FindFirstChild("FlingAuraVelocity") then
                                            local bv = Instance.new("BodyVelocity")
                                            bv.Name = "FlingAuraVelocity"
                                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                                            local dir = (obj.Position - localRoot.Position).Unit
                                            bv.Velocity = Vector3.new(dir.X, 0.5, dir.Z) * math.clamp(_G.FlingStrength, 400, 600)
                                            bv.Parent = obj
                                            Debris:AddItem(bv, 0.5)
                                        end
                                    end)
                                end
                            end
                        end
                    end
                end)
                task.wait(0.1)
            end
        end)
    end

    local function stopFlingAura()
        flingAuraRunning = false
        if flingAuraTask then
            task.cancel(flingAuraTask)
            flingAuraTask = nil
        end
    end

    GrabR:AddCheckbox("FlingAura", {
        Text = "Fling Aura",
        Default = false,
        Callback = function(Value)
            if Value then
                startFlingAura()
            else
                stopFlingAura()
            end
        end
    })

    GrabR:AddSlider("FlingStrength", {
        Text = "Fling Strength",
        Min = 400,
        Max = 10000,
        Default = 400,
        Rounding = 0,
        Suffix = "",
        Callback = function(Value)
            _G.FlingStrength = Value
        end
    })

    GrabR:AddDropdown("FlingTarget", {
        Text = "Fling Target",
        Values = {"Players", "Objects", "Players and Objects"},
        Default = "Players",
        Callback = function(Value)
            if Value == "Players" then
                _G.FlingTarget = 1
            elseif Value == "Objects" then
                _G.FlingTarget = 2
            else
                _G.FlingTarget = 3
            end
        end
    })
end

VisL = Tabs.Visual:AddLeftGroupbox("Visuals", "scan-eye")

do
    plr = game.Players.LocalPlayer

    VisL:AddCheckbox("Thirdperson", {
        Text = "Third Person",
        Default = false,
        Callback = function(Value)
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
end

VisL:AddSlider("VisualFOV", {
        Text = "Field of View",
        Min = 40,
        Max = 120,
        Default = 70,
        Rounding = 0,
        Callback = function(Value)
            local cam = workspace.CurrentCamera
            if cam then
                cam.FieldOfView = Value
            end
        end
    })

VisL:AddLabel("Esp ---------------")

do
    
    local espEnabled = false
    local espColor = Color3.fromRGB(255, 0, 0)
    local rainbowEnabled = false
    local espConnections = {}
    local espObjects = {}
    
    local function createESP(player)
        if not player or not player.Character then return end
        
        local char = player.Character
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Adornee = char
        highlight.FillColor = espColor
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.4
        highlight.OutlineTransparency = 0
        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        highlight.Parent = char
        
        local billboard = Instance.new("BillboardGui")
        billboard.Name = "ESP_Name"
        billboard.Adornee = hrp
        billboard.Size = UDim2.new(0, 200, 0, 30)
        billboard.StudsOffset = Vector3.new(0, 3, 0)
        billboard.AlwaysOnTop = true
        billboard.Parent = hrp
        
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Text = player.DisplayName .. " (" .. player.Name .. ")"
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextStrokeTransparency = 0
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        label.Font = Enum.Font.GothamBold
        label.TextScaled = true
        label.Parent = billboard
        
        local distBillboard = Instance.new("BillboardGui")
        distBillboard.Name = "ESP_Distance"
        distBillboard.Adornee = hrp
        distBillboard.Size = UDim2.new(0, 100, 0, 20)
        distBillboard.StudsOffset = Vector3.new(0, -2, 0)
        distBillboard.AlwaysOnTop = true
        distBillboard.Parent = hrp
        
        local distLabel = Instance.new("TextLabel")
        distLabel.Size = UDim2.new(1, 0, 1, 0)
        distLabel.BackgroundTransparency = 1
        distLabel.Text = "0 studs"
        distLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        distLabel.TextStrokeTransparency = 0
        distLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        distLabel.Font = Enum.Font.GothamBold
        distLabel.TextScaled = true
        distLabel.Parent = distBillboard
        
        table.insert(espObjects, {
            player = player,
            highlight = highlight,
            billboard = billboard,
            distBillboard = distBillboard,
            label = label,
            distLabel = distLabel
        })
        
        local conn = RunService.RenderStepped:Connect(function()
            if not espEnabled or not player.Character then
                conn:Disconnect()
                return
            end
            
            local localChar = LocalPlayer.Character
            local localHrp = localChar and localChar:FindFirstChild("HumanoidRootPart")
            if localHrp and hrp and hrp.Parent then
                local dist = math.floor((hrp.Position - localHrp.Position).Magnitude)
                distLabel.Text = dist .. " studs"
            end
        end)
        table.insert(espConnections, conn)
    end
    
    local function clearESP()
        for _, conn in ipairs(espConnections) do
            pcall(function() conn:Disconnect() end)
        end
        espConnections = {}
        
        for _, obj in ipairs(espObjects) do
            pcall(function()
                if obj.highlight then obj.highlight:Destroy() end
                if obj.billboard then obj.billboard:Destroy() end
                if obj.distBillboard then obj.distBillboard:Destroy() end
            end)
        end
        espObjects = {}
    end
    
    local function updateAllESP()
        clearESP()
        if not espEnabled then return end
        
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                createESP(plr)
            end
        end
    end
    
    local function updateColors()
        for _, obj in ipairs(espObjects) do
            pcall(function()
                if obj.highlight then
                    obj.highlight.FillColor = espColor
                end
            end)
        end
    end
    
    VisL:AddLabel("ESP Color"):AddColorPicker("ESPColorPicker", {
        Default = Color3.fromRGB(255, 0, 0),
        Title = "ESP Color",
        Callback = function(Value)
            espColor = Value
            if not rainbowEnabled then
                updateColors()
            end
        end
    })
    
    VisL:AddCheckbox("RainbowESP", {
        Text = "Rainbow ESP",
        Default = false,
        Callback = function(Value)
            rainbowEnabled = Value
            if Value then
                task.spawn(function()
                    while rainbowEnabled and espEnabled do
                        local hue = tick() % 1
                        espColor = Color3.fromHSV(hue, 1, 1)
                        updateColors()
                        task.wait(0.05)
                    end
                end)
            else
                espColor = Options.ESPColorPicker.Value or Color3.fromRGB(255, 0, 0)
                updateColors()
            end
        end
    })
    
    VisL:AddCheckbox("PlayerESP", {
        Text = "Enable Player ESP",
        Default = false,
        Callback = function(Value)
            espEnabled = Value
            if Value then
                updateAllESP()
                
                local playerAddedConn = Players.PlayerAdded:Connect(function(plr)
                    task.wait(0.5)
                    if espEnabled and plr ~= LocalPlayer and plr.Character then
                        createESP(plr)
                    end
                end)
                table.insert(espConnections, playerAddedConn)
                
                local charAddedConn = Players.PlayerAdded:Connect(function(plr)
                    if espEnabled and plr ~= LocalPlayer then
                        local charConn = plr.CharacterAdded:Connect(function()
                            task.wait(0.5)
                            if espEnabled and plr.Character then
                                local exists = false
                                for _, obj in ipairs(espObjects) do
                                    if obj.player == plr then
                                        exists = true
                                        break
                                    end
                                end
                                if not exists then
                                    createESP(plr)
                                end
                            end
                        end)
                        table.insert(espConnections, charConn)
                    end
                end)
                table.insert(espConnections, charAddedConn)
            else
                clearESP()
            end
        end
    })
    
    Players.PlayerRemoving:Connect(function(plr)
        for i, obj in ipairs(espObjects) do
            if obj.player == plr then
                pcall(function()
                    if obj.highlight then obj.highlight:Destroy() end
                    if obj.billboard then obj.billboard:Destroy() end
                    if obj.distBillboard then obj.distBillboard:Destroy() end
                end)
                table.remove(espObjects, i)
                break
            end
        end
    end)
end

do
    local Players = game:GetService("Players")
    local Workspace = game:GetService("Workspace")
    local RunService = game:GetService("RunService")
    local TweenService = game:GetService("TweenService")
    local LocalPlayer = Players.LocalPlayer
    local plr = LocalPlayer

    local smoothPCLDs = {}
    local pcldPlayerCache = {}
    local espColor = Color3.fromRGB(255, 60, 60)
    local smoothTime = 0.18
    local cons = {}
    local rainbowPCLD = false

    local function getPlayerFromPCLD(pcld)
        local closestPlayer
        local closestDist = 10

        for _, player in ipairs(Players:GetPlayers()) do
            local char = player.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then
                local dist = (hrp.Position - pcld.Position).Magnitude
                if dist < closestDist then
                    closestDist = dist
                    closestPlayer = player
                end
            end
        end

        if closestPlayer then
            pcldPlayerCache[pcld] = {
                player = closestPlayer,
                time = tick(),
            }
            return closestPlayer.DisplayName .. " (@ " .. closestPlayer.Name .. ")"
        end

        local cached = pcldPlayerCache[pcld]
        if cached and tick() - cached.time < 2 then
            local p = cached.player
            return p.DisplayName .. " (@ " .. p.Name .. ")"
        end

        return "Unknown"
    end

    VisL:AddLabel("ESP Color"):AddColorPicker("PCLDColor", {
        Default = espColor,
        Title = "PCLD ESP Color",
        Callback = function(v)
            espColor = v
            if not rainbowPCLD then
                for _, d in pairs(smoothPCLDs) do
                    if d.box then
                        d.box.Color = v
                    end
                    if d.outline then
                        d.outline.Color3 = v
                    end
                end
            end
        end,
    })

    VisL:AddCheckbox("RainbowPCLD", {
        Text = "PCLD Rainbow PCLD",
        Default = false,
        Callback = function(v)
            rainbowPCLD = v
            if v then
                task.spawn(function()
                    while rainbowPCLD do
                        local hue = tick() % 1
                        local color = Color3.fromHSV(hue, 1, 1)
                        for _, d in pairs(smoothPCLDs) do
                            if d.box then
                                d.box.Color = color
                            end
                            if d.outline then
                                d.outline.Color3 = color
                            end
                        end
                        task.wait(0.05)
                    end
                end)
            else
                espColor = Options.PCLDColor.Value or Color3.fromRGB(255, 60, 60)
                for _, d in pairs(smoothPCLDs) do
                    if d.box then
                        d.box.Color = espColor
                    end
                    if d.outline then
                        d.outline.Color3 = espColor
                    end
                end
            end
        end
    })


    local function createSmoothPCLD(original)
        if smoothPCLDs[original] then
            return
        end

        original.Transparency = 1

        local box = Instance.new("Part")
        box.Name = "PCLD_Box"
        box.Size = original.Size
        box.CFrame = original.CFrame
        box.Anchored = true
        box.CanCollide = false
        box.CanTouch = false
        box.CanQuery = false
        box.CastShadow = false
        box.Material = Enum.Material.Neon
        box.Color = espColor
        box.Transparency = 0.45
        box.Parent = Workspace

        local outline = Instance.new("SelectionBox")
        outline.Adornee = box
        outline.LineThickness = 0.02
        outline.Color3 = espColor
        outline.Transparency = 0.1
        outline.Parent = box

        local data = {
            box = box,
            outline = outline,
            original = original,
            lastUpdate = tick(),
            tween = nil,
        }

        smoothPCLDs[original] = data

        task.spawn(function()
            local lastPos = original.Position
            while box.Parent and original.Parent do
                local pos = original.Position
                local cf = original.CFrame
                if (pos - lastPos).Magnitude > 0.02 then
                    lastPos = pos
                    if data.tween then
                        data.tween:Cancel()
                    end
                    data.tween = TweenService:Create(box, TweenInfo.new(smoothTime, Enum.EasingStyle.Linear), {CFrame = cf})
                    data.tween:Play()
                end
                task.wait(0.03)
            end
        end)
    end

    local function removeSmoothPCLD(original)
        local d = smoothPCLDs[original]
        if not d then
            return
        end
        if d.tween then
            d.tween:Cancel()
        end
        if d.box then
            d.box:Destroy()
        end
        smoothPCLDs[original] = nil
        pcldPlayerCache[original] = nil
    end

    local function clearAllSmoothPCLDs()
        for _, d in pairs(smoothPCLDs) do
            if d.tween then
                d.tween:Cancel()
            end
            if d.box then
                d.box:Destroy()
            end
        end
        smoothPCLDs = {}
        pcldPlayerCache = {}
    end

    VisL:AddCheckbox("ViewPCLD", {
        Text = "PCLD ESP",
        Default = false,
        Callback = function(v)
            if v then
                for _, obj in ipairs(Workspace:GetChildren()) do
                    if obj.Name == "PlayerCharacterLocationDetector" then
                        createSmoothPCLD(obj)
                    end
                end

                cons.viewpcld = Workspace.ChildAdded:Connect(function(child)
                    if child.Name == "PlayerCharacterLocationDetector" then
                        task.wait(0.1)
                        createSmoothPCLD(child)
                    end
                end)

                cons.pcldRemoved = Workspace.ChildRemoved:Connect(function(child)
                    if child.Name == "PlayerCharacterLocationDetector" then
                        removeSmoothPCLD(child)
                    end
                end)
            else
                if cons.viewpcld then
                    cons.viewpcld:Disconnect()
                end
                if cons.pcldRemoved then
                    cons.pcldRemoved:Disconnect()
                end
                clearAllSmoothPCLDs()
            end
        end,
    })

    Options.PCLDColor:OnChanged(function()
        if not rainbowPCLD then
            espColor = Options.PCLDColor.Value
            for _, d in pairs(smoothPCLDs) do
                if d.box then
                    d.box.Color = espColor
                end
                if d.outline then
                    d.outline.Color3 = espColor
                end
            end
        end
    end)
end

do
    VisualsCus = Tabs.Visual:AddLeftGroupbox("Customization", "eye-closed")
    Lighting = game:GetService("Lighting")
    Library = _G.Library or Library
    CustomSkyEnabled = false
    CurrentSkybox = "Red Night"
    DefaultLighting = {
        Brightness = Lighting.Brightness,
        ClockTime = Lighting.ClockTime,
        GlobalShadows = Lighting.GlobalShadows,
        OutdoorAmbient = Lighting.OutdoorAmbient,
        Ambient = Lighting.Ambient,
        FogStart = Lighting.FogStart,
        FogEnd = Lighting.FogEnd,
        FogColor = Lighting.FogColor,
        ExposureCompensation = Lighting.ExposureCompensation,
    }
    DefaultSkySettings = {}
    defaultSky = Lighting:FindFirstChildOfClass("Sky")
    if defaultSky then
        DefaultSkySettings = {
            SkyboxBk = defaultSky.SkyboxBk,
            SkyboxDn = defaultSky.SkyboxDn,
            SkyboxFt = defaultSky.SkyboxFt,
            SkyboxLf = defaultSky.SkyboxLf,
            SkyboxRt = defaultSky.SkyboxRt,
            SkyboxUp = defaultSky.SkyboxUp,
        }
    end

    SkyboxAssets = {
        ["Black Storm"] = {
            Bk = "rbxassetid://15502511288",
            Dn = "rbxassetid://15502508460",
            Ft = "rbxassetid://15502510289",
            Lf = "rbxassetid://15502507918",
            Rt = "rbxassetid://15502509398",
            Up = "rbxassetid://15502511911"
        },
        ["HD"] = {
            Bk = "http://www.roblox.com/asset/?id=16553658937",
            Dn = "http://www.roblox.com/asset/?id=16553660713",
            Ft = "http://www.roblox.com/asset/?id=16553662144",
            Lf = "http://www.roblox.com/asset/?id=16553664042",
            Rt = "http://www.roblox.com/asset/?id=16553665766",
            Up = "http://www.roblox.com/asset/?id=16553667750"
        },
        ["Snow"] = {
            Bk = "http://www.roblox.com/asset/?id=155657655",
            Dn = "http://www.roblox.com/asset/?id=155674246",
            Ft = "http://www.roblox.com/asset/?id=155657609",
            Lf = "http://www.roblox.com/asset/?id=155657671",
            Rt = "http://www.roblox.com/asset/?id=155657619",
            Up = "http://www.roblox.com/asset/?id=155674931"
        },
        ["Blue Space"] = {
            Bk = "rbxassetid://15536110634",
            Dn = "rbxassetid://15536112543",
            Ft = "rbxassetid://15536116141",
            Lf = "rbxassetid://15536114370",
            Rt = "rbxassetid://15536118762",
            Up = "rbxassetid://15536117282"
        },
        ["Realistic"] = {
            Bk = "rbxassetid://653719502",
            Dn = "rbxassetid://653718790",
            Ft = "rbxassetid://653719067",
            Lf = "rbxassetid://653719190",
            Rt = "rbxassetid://653718931",
            Up = "rbxassetid://653719321"
        },
        ["Stormy"] = {
            Bk = "http://www.roblox.com/asset/?id=18703245834",
            Dn = "http://www.roblox.com/asset/?id=18703243349",
            Ft = "http://www.roblox.com/asset/?id=18703240532",
            Lf = "http://www.roblox.com/asset/?id=18703237556",
            Rt = "http://www.roblox.com/asset/?id=18703235430",
            Up = "http://www.roblox.com/asset/?id=18703232671"
        },
        ["Pink"] = {
            Bk = "rbxassetid://12216109205",
            Dn = "rbxassetid://12216109875",
            Ft = "rbxassetid://12216109489",
            Lf = "rbxassetid://12216110170",
            Rt = "rbxassetid://12216110471",
            Up = "rbxassetid://12216108877"
        },
        ["Sunset"] = {
            Bk = "rbxassetid://600830446",
            Dn = "rbxassetid://600831635",
            Ft = "rbxassetid://600832720",
            Lf = "rbxassetid://600886090",
            Rt = "rbxassetid://600833862",
            Up = "rbxassetid://600835177"
        },
        ["Arctic"] = {
            Bk = "http://www.roblox.com/asset/?id=225469390",
            Dn = "http://www.roblox.com/asset/?id=225469395",
            Ft = "http://www.roblox.com/asset/?id=225469403",
            Lf = "http://www.roblox.com/asset/?id=225469450",
            Rt = "http://www.roblox.com/asset/?id=225469471",
            Up = "http://www.roblox.com/asset/?id=225469481"
        },
        ["Space"] = {
            Bk = "http://www.roblox.com/asset/?id=166509999",
            Dn = "http://www.roblox.com/asset/?id=166510057",
            Ft = "http://www.roblox.com/asset/?id=166510116",
            Lf = "http://www.roblox.com/asset/?id=166510092",
            Rt = "http://www.roblox.com/asset/?id=166510131",
            Up = "http://www.roblox.com/asset/?id=166510114"
        },
        ["Roblox Default"] = {
            Bk = "rbxasset://textures/sky/sky512_bk.tex",
            Dn = "rbxasset://textures/sky/sky512_dn.tex",
            Ft = "rbxasset://textures/sky/sky512_ft.tex",
            Lf = "rbxasset://textures/sky/sky512_lf.tex",
            Rt = "rbxasset://textures/sky/sky512_rt.tex",
            Up = "rbxasset://textures/sky/sky512_up.tex"
        },
        ["Red Night"] = {
            Bk = "http://www.roblox.com/asset/?id=401664839",
            Dn = "http://www.roblox.com/asset/?id=401664862",
            Ft = "http://www.roblox.com/asset/?id=401664960",
            Lf = "http://www.roblox.com/asset/?id=401664881",
            Rt = "http://www.roblox.com/asset/?id=401664901",
            Up = "http://www.roblox.com/asset/?id=401664936"
        },
        ["Deep Space 1"] = {
            Bk = "http://www.roblox.com/asset/?id=149397692",
            Dn = "http://www.roblox.com/asset/?id=149397686",
            Ft = "http://www.roblox.com/asset/?id=149397697",
            Lf = "http://www.roblox.com/asset/?id=149397684",
            Rt = "http://www.roblox.com/asset/?id=149397688",
            Up = "http://www.roblox.com/asset/?id=149397702"
        },
        ["Pink Skies"] = {
            Bk = "http://www.roblox.com/asset/?id=151165214",
            Dn = "http://www.roblox.com/asset/?id=151165197",
            Ft = "http://www.roblox.com/asset/?id=151165224",
            Lf = "http://www.roblox.com/asset/?id=151165191",
            Rt = "http://www.roblox.com/asset/?id=151165206",
            Up = "http://www.roblox.com/asset/?id=151165227"
        },
        ["Purple Sunset"] = {
            Bk = "rbxassetid://264908339",
            Dn = "rbxassetid://264907909",
            Ft = "rbxassetid://264909420",
            Lf = "rbxassetid://264909758",
            Rt = "rbxassetid://264908886",
            Up = "rbxassetid://264907379"
        },
        ["Blue Night"] = {
            Bk = "http://www.roblox.com/asset/?id=12064107",
            Dn = "http://www.roblox.com/asset/?id=12064152",
            Ft = "http://www.roblox.com/asset/?id=12064121",
            Lf = "http://www.roblox.com/asset/?id=12063984",
            Rt = "http://www.roblox.com/asset/?id=12064115",
            Up = "http://www.roblox.com/asset/?id=12064131"
        },
        ["Blossom Daylight"] = {
            Bk = "http://www.roblox.com/asset/?id=271042516",
            Dn = "http://www.roblox.com/asset/?id=271077243",
            Ft = "http://www.roblox.com/asset/?id=271042556",
            Lf = "http://www.roblox.com/asset/?id=271042310",
            Rt = "http://www.roblox.com/asset/?id=271042467",
            Up = "http://www.roblox.com/asset/?id=271077958"
        },
        ["Blue Nebula"] = {
            Bk = "http://www.roblox.com/asset?id=135207744",
            Dn = "http://www.roblox.com/asset?id=135207662",
            Ft = "http://www.roblox.com/asset?id=135207770",
            Lf = "http://www.roblox.com/asset?id=135207615",
            Rt = "http://www.roblox.com/asset?id=135207695",
            Up = "http://www.roblox.com/asset?id=135207794"
        },
        ["Blue Planet"] = {
            Bk = "rbxassetid://218955819",
            Dn = "rbxassetid://218953419",
            Ft = "rbxassetid://218954524",
            Lf = "rbxassetid://218958493",
            Rt = "rbxassetid://218957134",
            Up = "rbxassetid://218950090"
        },
        ["Deep Space 2"] = {
            Bk = "http://www.roblox.com/asset/?id=159248188",
            Dn = "http://www.roblox.com/asset/?id=159248183",
            Ft = "http://www.roblox.com/asset/?id=159248187",
            Lf = "http://www.roblox.com/asset/?id=159248173",
            Rt = "http://www.roblox.com/asset/?id=159248192",
            Up = "http://www.roblox.com/asset/?id=159248176"
        },
        ["Summer"] = {
            Bk = "rbxassetid://16648590964",
            Dn = "rbxassetid://16648617436",
            Ft = "rbxassetid://16648595424",
            Lf = "rbxassetid://16648566370",
            Rt = "rbxassetid://16648577071",
            Up = "rbxassetid://16648598180"
        },
        ["Galaxy"] = {
            Bk = "rbxassetid://15983968922",
            Dn = "rbxassetid://15983966825",
            Ft = "rbxassetid://15983965025",
            Lf = "rbxassetid://15983967420",
            Rt = "rbxassetid://15983966246",
            Up = "rbxassetid://15983964246"
        },
        ["Stylized"] = {
            Bk = "rbxassetid://18351376859",
            Dn = "rbxassetid://18351374919",
            Ft = "rbxassetid://18351376800",
            Lf = "rbxassetid://18351376469",
            Rt = "rbxassetid://18351376457",
            Up = "rbxassetid://18351377189"
        },
        ["Minecraft"] = {
            Bk = "rbxassetid://8735166756",
            Dn = "http://www.roblox.com/asset/?id=8735166707",
            Ft = "http://www.roblox.com/asset/?id=8735231668",
            Lf = "http://www.roblox.com/asset/?id=8735166755",
            Rt = "http://www.roblox.com/asset/?id=8735166751",
            Up = "http://www.roblox.com/asset/?id=8735166729"
        },
        ["Cloudy Rain"] = {
            Bk = "http://www.roblox.com/asset/?id=4498828382",
            Dn = "http://www.roblox.com/asset/?id=4498828812",
            Ft = "http://www.roblox.com/asset/?id=4498829917",
            Lf = "http://www.roblox.com/asset/?id=4498830911",
            Rt = "http://www.roblox.com/asset/?id=4498830417",
            Up = "http://www.roblox.com/asset/?id=4498831746"
        },
        ["Black Cloudy Rain"] = {
            Bk = "http://www.roblox.com/asset/?id=149679669",
            Dn = "http://www.roblox.com/asset/?id=149681979",
            Ft = "http://www.roblox.com/asset/?id=149679690",
            Lf = "http://www.roblox.com/asset/?id=149679709",
            Rt = "http://www.roblox.com/asset/?id=149679722",
            Up = "http://www.roblox.com/asset/?id=149680199"
        },
    }

    local function applySkybox(name)
        s = SkyboxAssets[name]
        if not s then return end
        sky = Lighting:FindFirstChildOfClass("Sky") or Instance.new("Sky", Lighting)
        sky.Name = "Sky"
        sky.SkyboxBk = s.Bk
        sky.SkyboxDn = s.Dn
        sky.SkyboxFt = s.Ft
        sky.SkyboxLf = s.Lf
        sky.SkyboxRt = s.Rt
        sky.SkyboxUp = s.Up
    end

    local function restoreDefaultSky()
        sky = Lighting:FindFirstChildOfClass("Sky")
        if sky and DefaultSkySettings.SkyboxBk then
            sky.SkyboxBk = DefaultSkySettings.SkyboxBk
            sky.SkyboxDn = DefaultSkySettings.SkyboxDn
            sky.SkyboxFt = DefaultSkySettings.SkyboxFt
            sky.SkyboxLf = DefaultSkySettings.SkyboxLf
            sky.SkyboxRt = DefaultSkySettings.SkyboxRt
            sky.SkyboxUp = DefaultSkySettings.SkyboxUp
        elseif sky then
            sky:Destroy()
        end
    end


    skyboxNames = {}
    for name in pairs(SkyboxAssets) do
        table.insert(skyboxNames, name)
    end
    table.sort(skyboxNames)

    VisualsCus:AddDropdown("VisualSkybox", {
        Text = "Skybox",
        Values = skyboxNames,
        Default = "Red Night",
        Callback = function(Value)
            CurrentSkybox = Value
            if CustomSkyEnabled then
                applySkybox(Value)
            end
        end
    })

    VisualsCus:AddCheckbox("VisualSkyboxToggle", {
        Text = "Enable Custom Skybox",
        Default = false,
        Callback = function(Value)
            CustomSkyEnabled = Value
            if Value then
                applySkybox(CurrentSkybox)
                Library:Notify({
                    Title = "LOLL",
                    Description = "Skybox Enabled!",
                    Duration = 2
                })
            else
                restoreDefaultSky()
                Library:Notify({
                    Title = "LOLL",
                    Description = "Skybox Disabled!",
                    Duration = 2
                })
            end
        end
    })

local GWater = Tabs.Visual:AddRightGroupbox("Ocean", "waves-horizontal")
OceanFolder = workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
_cachedOceanParts = nil

function oceanParts()
    if _cachedOceanParts then
        return _cachedOceanParts
    end

    local parts = {}

    for _, obj in ipairs(OceanFolder:GetDescendants())do
        if obj:IsA('BasePart') then
            table.insert(parts, obj)
        end
    end

    _cachedOceanParts = parts

    return parts
end
function setOceanMaterial(materialName)
    local material = Enum.Material[materialName]

    if not material then
        return
    end

    for _, part in ipairs(oceanParts())do
        part.Material = material
    end
end
function setOceanColor(color)
    for _, part in ipairs(oceanParts())do
        part.Color = color
    end
end

WaterMaterial = GWater:AddDropdown('WaterMaterial', {
    Text = 'Ocean Material',
    Values = {
        'Water',
        'Glass',
        'Neon',
        'SmoothPlastic',
        'Ice',
        'Sand',
        'Rock',
        'Basalt',
        'Slate',
        'Mud',
        'Cobblestone',
    },
    Default = 'Water',
})

Options.WaterMaterial:OnChanged(function()
    setOceanMaterial(Options.WaterMaterial.Value)
end)

OceanToggle = GWater:AddCheckbox('EnableOceanColor', {
    Text = 'Custom Ocean Color',
    Default = true,
})

OceanToggle:AddColorPicker('OceanColorPicker', {
    Default = Color3.fromRGB(0, 170, 255),
    Title = 'Ocean Color',
    Callback = function(Value)
        setOceanColor(Value)
    end,
})
Options.OceanColorPicker:OnChanged(function()
    setOceanColor(Options.OceanColorPicker.Value)

    if Options.OceanColorPicker.Transparency then
        for _, part in ipairs(oceanParts())do
            part.Transparency = Options.OceanColorPicker.Transparency
        end
    end
end)
GWater:AddButton('Refresh Ocean Cache', function()
    _cachedOceanParts = nil
    _cachedOceanParts = oceanParts()

    setOceanMaterial(Options.WaterMaterial.Value)
    setOceanColor(Options.OceanColorPicker.Value)
end)
GWater:AddCheckbox('RealisticWater', {
    Text = 'Realistic Water',
    Default = false,
})
Toggles.RealisticWater:OnChanged(function(state)
    if state then
        local terrain = workspace.Terrain
        local model = workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel

        for _, part in ipairs(model:GetChildren())do
            if part:IsA('Part') then
                local size = part.Size
                local cf = part.CFrame
                local region = Region3.new(cf.Position - size / 2, cf.Position + size / 2):ExpandToGrid(4)

                terrain:FillRegion(region, 4, Enum.Material.Water)
                part:Destroy()
            end
        end
    end
end)

do
    VisualR = Tabs.Visual:AddRightGroupbox("Notifys", "bell")
    Players = game:GetService("Players")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    LocalPlayer = Players.LocalPlayer
    Library = _G.Library or Library

    kickNotifyConnection = nil

    local function getClosestPlayer(pos)
        if not pos then return nil end
        closestPlayer = nil
        minDistance = math.huge
        
        for _, player in pairs(Players:GetPlayers()) do
            if player.Character then
                hrp = player.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    distance = (hrp.Position - pos).Magnitude
                    if distance < minDistance then
                        minDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end
        return closestPlayer
    end

    VisualR:AddCheckbox("KickNotify", {
        Text = "Kick Notify",
        Default = false,
        Callback = function(Value)
            if Value then
                kickNotifyConnection = Workspace.ChildAdded:Connect(function(obj)
                    kickObjectNames = {
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
                        part = obj:FindFirstChildWhichIsA("BasePart", true)
                        if part then pos = part.Position end
                    end
                    if not pos then return end
                    closestPlayer = getClosestPlayer(pos)
                    if closestPlayer then
                        displayName = closestPlayer.DisplayName or "Unknown"
                        realName = closestPlayer.Name or "Unknown"
                        
                        Library:Notify({
                            Title = "KICK DETECTED",
                            Description = "Player: " .. realName .. " (" .. displayName .. ")",
                            Duration = 5,
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

    packetLagNotifyEnabled = false
    lastLagSource = false
    packetLagConnection = nil

    local function GetSizeMB(StringLength)
        return StringLength / (1024 * 1024)
    end

    local function StartPacketLagDetector()
        if packetLagConnection then
            packetLagConnection:Disconnect()
            packetLagConnection = nil
        end
        
        packetLagConnection = ReplicatedStorage.GrabEvents.ExtendGrabLine.OnClientEvent:Connect(function(arg1, data)
            if typeof(data) == "string" and not lastLagSource and packetLagNotifyEnabled then
                lastLagSource = true
                StringLen = string.len(data)
                
                if StringLen > 300 then
                    SizeRounded = math.round(GetSizeMB(StringLen) * 1000) / 1000
                    
                    Library:Notify({
                        Title = "PACKET LAG DETECTED",
                        Description = "Source: " .. tostring(arg1) .. "\nSize: " .. tostring(SizeRounded) .. " MB",
                        Duration = 5,
                    })
                end
                
                task.delay(5, function()
                    lastLagSource = false
                end)
            end
        end)
    end

    VisualR:AddCheckbox("PacketLagNotify", {
        Text = "Packet Lag Notify",
        Default = false,
        Callback = function(Value)
            packetLagNotifyEnabled = Value
            if Value then
                StartPacketLagDetector()
            else
                if packetLagConnection then
                    packetLagConnection:Disconnect()
                    packetLagConnection = nil
                end
            end
        end
    })
end

ServL = Tabs.Server:AddLeftGroupbox("lags", "zap")
ServR = Tabs.Server:AddRightGroupbox("Server Destroy", "skull")

selectedHeight = "Spawn"

function getAllPlayers()
    players = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            if not _G.isWhitelisted or not _G.isWhitelisted(plr) then
                table.insert(players, plr)
            end
        end
    end
    return players
end

GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")

function spamOwnership(hrp)
    if not GrabEvents then return end
    setOwner = GrabEvents:FindFirstChild("SetNetworkOwner")
    if setOwner and hrp then pcall(function() setOwner:FireServer(hrp, hrp.CFrame) end) end
end

function teleportToPlayer(myHrp, targetHrp)
    if not myHrp or not targetHrp then return end
    pcall(function()
        myHrp.CFrame = targetHrp.CFrame * CFrame.new(0, 5, 5)
        myHrp.AssemblyLinearVelocity = Vector3.zero
    end)
end

function destroyLineOnPlayer(hrp)
    if not GrabEvents then return end
    createLine = GrabEvents:FindFirstChild("CreateGrabLine")
    destroyLine = GrabEvents:FindFirstChild("DestroyGrabLine")
    if not createLine or not destroyLine then return end
    pcall(function()
        createLine:FireServer(hrp, CFrame.new(0, 1e9, 0))
        task.wait()
        destroyLine:FireServer(hrp)
    end)
end

lineLagThread = nil
lineLagEnabled = false

function startLineLag()
    if lineLagEnabled then return end
    lineLagEnabled = true
    lineLagThread = coroutine.create(function()
        if not GrabEvents then return end
        createLine = GrabEvents:FindFirstChild("CreateGrabLine")
        if not createLine then return end
        while lineLagEnabled do
            spawnLocation = Workspace:FindFirstChild("SpawnLocation") or Workspace:FindFirstChild("Spawn") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
            if spawnLocation then
                randomX = math.random(-1e9, 1e9)
                randomZ = math.random(-1e9, 1e9)
                directions = {CFrame.new(randomX, 0, randomZ), CFrame.new(-randomX, 0, -randomZ), CFrame.new(randomX, 0, -randomZ), CFrame.new(-randomX, 0, randomZ)}
                for _, pos in pairs(directions) do createLine:FireServer(spawnLocation, pos) end
            end
            task.wait()
        end
    end)
    coroutine.resume(lineLagThread)
end

function stopLineLag()
    lineLagEnabled = false
    if lineLagThread then coroutine.close(lineLagThread); lineLagThread = nil end
end

ServR:AddDropdown("DestroyHeight", {
    Text = "Destroy Height",
    Values = {"Spawn", "Heaven"},
    Default = "Spawn",
    Callback = function(Value)
        selectedHeight = Value
    end
})

ServR:AddButton({
    Text = "Destroy Server",
    Callback = function()
        task.spawn(function()
            height = (selectedHeight == "Heaven") and 1e9 or 35
            startLineLag()
            task.wait(1)

            players = getAllPlayers()
            if #players == 0 then
                stopLineLag()
                return
            end

            myChar = LocalPlayer.Character
            myHrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHrp then stopLineLag(); return end

            playerData = {}
            for _, plr in ipairs(players) do
                char = plr.Character
                hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then table.insert(playerData, {player = plr, hrp = hrp}) end
            end

            for _, data in ipairs(playerData) do
                teleportToPlayer(myHrp, data.hrp)
                task.wait(0.2)
                spamOwnership(data.hrp)
                task.wait()
            end

            radius = 40
            angleStep = (math.pi * 2) / #playerData
            for idx, data in ipairs(playerData) do
                angle = (idx - 1) * angleStep
                x = math.cos(angle) * radius
                z = math.sin(angle) * radius

                pcall(function()
                    data.hrp.CFrame = CFrame.new(x, height, z)
                    data.hrp.AssemblyLinearVelocity = Vector3.zero
                end)

                bp = Instance.new("BodyPosition")
                bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
                bp.P = 40000000
                bp.Position = Vector3.new(x, height, z)
                bp.Parent = data.hrp
                task.delay(2, function() pcall(function() bp:Destroy() end) end)
                task.wait()
            end

            for i = 1, 8 do
                for _, data in ipairs(playerData) do destroyLineOnPlayer(data.hrp) end
                task.wait(0.3)
            end
        end)
    end
})

ServR:AddButton({
    Text = "Stop Lag",
    Callback = function()
        stopLineLag()
    end
})

ServR:AddLabel("Blob -----------")

do
    local hkKickAll = false
    local hkKillAll = false
    local hkBlobRunning = false
    local hkBlob = nil
    local hkExpectingBlob = false
    local hkFoundBlob = false
    local hkAction = nil
    local hkBringTargets = {}
    local hkKickTargets = {}
    local hkKillTargets = {}

    local function hkIsGrabbed(char)
        if not char then return false end
        local hum = char:FindFirstChildOfClass("Humanoid")
        if not hum then return false end
        return hum.SeatPart ~= nil
    end

    local function hkDestroyBlob(blob)
        if blob and blob.Parent then
            pcall(function()
                ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
            end)
        end
    end

    function hkBlobService()
        while true do
            local hkListMode = 1

            if hkKickAll or hkKillAll then
                hkListMode = 2
            end

            hkBlobRunning = false

            for _, plr in pairs(Players:GetPlayers()) do
                if plr == LocalPlayer then continue end
                
                if hkBringTargets[plr.UserId] and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and hkIsGrabbed(plr.Character) then
                    if hkBringTargets[plr.UserId] == "none" or hkBringTargets[plr.UserId] == "crip" or not hkBringTargets[plr.UserId] or hkBringTargets[plr.UserId] == "bris" then
                        if hkBringTargets[plr.UserId] == "crip" then
                            local hum = plr.Character:FindFirstChild("Humanoid")
                            if hum then hum.WalkSpeed = 0 end
                        end
                        hkBringTargets[plr.UserId] = nil
                    else
                        local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            hrp.CFrame = hkBringTargets[plr.UserId]
                            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                        end
                        hkBringTargets[plr.UserId] = nil

                        if hkKickTargets[plr.UserId] or (hkKickAll and not _G.isWhitelisted(plr)) then
                            hkAction = "Kick"
                        end
                    end
                elseif hkKickTargets[plr.UserId] or hkKillTargets[plr.UserId] or hkBringTargets[plr.UserId] or hkKickAll or hkKillAll then
                    local j, h
                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        j = LocalPlayer.Character.HumanoidRootPart.CFrame
                        h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
                    end

                    local hkAction = nil

                    if hkBringTargets[plr.UserId] and hkBringTargets[plr.UserId] ~= "bris" then
                        hkAction = "Bring"
                    elseif hkKillTargets[plr.UserId] or hkBringTargets[plr.UserId] == "bris" then
                        hkAction = "Kill"
                    elseif hkKickTargets[plr.UserId] then
                        hkAction = "Kick"
                    else
                        if hkKickAll then
                            if not _G.isWhitelisted(plr) then
                                hkAction = "Kick"
                            end
                        end
                        if hkKillAll then
                            if not _G.isWhitelisted(plr) then
                                hkAction = "Kill"
                            end
                        end
                    end

                    if plr ~= LocalPlayer and hkAction then
                        local continue = false

                        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                            if plr.Character.HumanoidRootPart.Massless then
                                if plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.SeatPart then
                                    continue = true
                                end
                            else
                                continue = true
                            end
                        end

                        local hkActive = true

                        while hkActive and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and plr and plr.Character and (hkAction == "Kick" or hkAction == "Kill" or hkAction == "Bring") and plr.Character:FindFirstChild("HumanoidRootPart") and plr.Character.HumanoidRootPart.CFrame.Position.Magnitude < 1000000 and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and continue do
                            if hkListMode == 2 then
                                if not hkKickAll and not hkKillAll then
                                    hkActive = false
                                elseif _G.isWhitelisted(plr) then
                                    hkActive = false
                                end
                            else
                                if not hkKickTargets[plr.UserId] and not hkKillTargets[plr.UserId] and not hkBringTargets[plr.UserId] then
                                    hkActive = false
                                end
                            end

                            hkBlobRunning = true
                            continue = true

                            local vel = plr.Character.HumanoidRootPart.AssemblyLinearVelocity
                            if vel.Magnitude > 10000 then
                                vel = Vector3.zero
                            end

                            if hkActive and continue and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == "CreatureBlobman" then
                                hkExpectingBlob = true
                                hkBlob = LocalPlayer.Character.Humanoid.SeatPart.Parent

                                if hkBlob:FindFirstChild("HumanoidCreature") then
                                    hkBlob.HumanoidCreature.PlatformStand = true
                                end

                                local rightDetector = hkBlob:FindFirstChild("RightDetector")
                                local rightWeld = rightDetector and rightDetector:FindFirstChild("RightWeld")
                                local scriptObj = hkBlob:FindFirstChild("BlobmanSeatAndOwnerScript")
                                local creatureGrab = scriptObj and scriptObj:FindFirstChild("CreatureGrab")
                                local creatureRelease = scriptObj and scriptObj:FindFirstChild("CreatureRelease")
                                local creatureDrop = scriptObj and scriptObj:FindFirstChild("CreatureDrop")

                                if rightDetector and rightWeld and creatureGrab and creatureRelease and creatureDrop then
                                    local plrHRP = plr.Character:FindFirstChild("HumanoidRootPart")
                                    if plrHRP then
                                        LocalPlayer.Character.HumanoidRootPart.CFrame = (plrHRP.CFrame * CFrame.new(0, -20, 10)) + (plrHRP.AssemblyLinearVelocity / math.pi)
                                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plrHRP.AssemblyLinearVelocity

                                        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                                            if hkAction == "Bring" then
                                                local plrHum = plr.Character:FindFirstChild("Humanoid")
                                                if plrHum and plrHum.Health ~= 0 and hkBlob then
                                                    if hkIsGrabbed(plr.Character) then
                                                        local target = hkBringTargets[plr.UserId]
                                                        if target == "none" or target == "crip" or not target then
                                                            local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                                                            if hrp then
                                                                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                                                                if target == "crip" then
                                                                    plrHum.WalkSpeed = 0
                                                                end
                                                            end
                                                            hkBringTargets[plr.UserId] = nil
                                                        else
                                                            local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                                                            if hrp then
                                                                hrp.CFrame = target
                                                                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                                                            end
                                                            hkBringTargets[plr.UserId] = nil

                                                            if hkKickTargets[plr.UserId] or (hkKickAll and not _G.isWhitelisted(plr)) then
                                                                hkAction = "Kick"
                                                            end
                                                        end
                                                    else
                                                        creatureGrab:FireServer(rightDetector, plrHRP, rightWeld)
                                                        creatureRelease:FireServer(rightWeld, plrHRP)
                                                    end
                                                end
                                            elseif hkAction == "Kick" then
                                                if plr.Character.Parent == workspace then
                                                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                                                    if hrp then
                                                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(hrp, hrp.CFrame)
                                                        hrp.AssemblyLinearVelocity = Vector3.new(0, 1E10, 0)
                                                        ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(hrp)
                                                        
                                                        local myHRP = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                                                        creatureGrab:FireServer(rightDetector, hrp, rightWeld)
                                                        if myHRP then
                                                            creatureGrab:FireServer(rightDetector, myHRP, rightWeld)
                                                        end
                                                        creatureDrop:FireServer(rightWeld, hrp)
                                                    end
                                                else
                                                    hkBringTargets[plr.UserId] = CFrame.new(-73, -6, -265.5)
                                                    hkActive = false
                                                end
                                            else
                                                local plrHum = plr.Character:FindFirstChild("Humanoid")
                                                if plrHum and plrHum.Health ~= 0 and hkBlob then
                                                    if hkBringTargets[plr.UserId] == "bris" then
                                                        hkBringTargets[plr.UserId] = nil
                                                    end
                                                    plrHum:ChangeState(Enum.HumanoidStateType.Dead)
                                                    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                                                    if hrp then
                                                        creatureGrab:FireServer(rightDetector, hrp, rightWeld)
                                                        creatureRelease:FireServer(rightWeld, hrp)
                                                    end
                                                end
                                            end
                                        end
                                    end
                                else
                                    local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                                    if toysFolder then
                                        local blob = toysFolder:FindFirstChild("CreatureBlobman")
                                        if blob then
                                            hkDestroyBlob(blob)
                                        end
                                    end
                                end
                            elseif continue then
                                hkExpectingBlob = true

                                local playerHum = LocalPlayer.Character:FindFirstChild("Humanoid")
                                if playerHum and playerHum.SeatPart and (not playerHum.SeatPart.Parent or playerHum.SeatPart.Parent.Name ~= "CreatureBlobman") then
                                    playerHum.Sit = false
                                end

                                if hkBlob and hkBlob:FindFirstChild("VehicleSeat") then
                                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                        hkBlob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                    end
                                else
                                    hkFoundBlob = false
                                    hkBlob = nil

                                    local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                                    if toysFolder then
                                        for _, itm in pairs(toysFolder:GetChildren()) do
                                            if itm.Name == "CreatureBlobman" and itm:FindFirstChild("VehicleSeat") then
                                                local rd = itm:FindFirstChild("RightDetector")
                                                local rw = rd and rd:FindFirstChild("RightWeld")
                                                if rd and rw then
                                                    hkFoundBlob = true
                                                    hkBlob = itm
                                                else
                                                    hkDestroyBlob(itm)
                                                end
                                            elseif itm.Name == "CreatureBlobman" then
                                                hkDestroyBlob(itm)
                                                task.wait(0.1)
                                            end
                                        end
                                    end

                                    if hkBlob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                        hkBlob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                    end

                                    if not hkFoundBlob then
                                        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                                        if hrp then
                                            if LocalPlayer.Character.Parent ~= workspace then
                                                hrp.CFrame = CFrame.new(0, -10, 0)
                                                hrp.AssemblyLinearVelocity = Vector3.zero
                                            end

                                            while hkActive and not hkBlob do
                                                if hkListMode == 2 then
                                                    if not hkKickAll and not hkKillAll then
                                                        hkActive = false
                                                    elseif _G.isWhitelisted(plr) then
                                                        hkActive = false
                                                    end
                                                else
                                                    if not (hkKickTargets[plr.UserId] or hkKillTargets[plr.UserId] or hkBringTargets[plr.UserId]) then
                                                        hkActive = false
                                                    end
                                                end

                                                local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
                                                if toysFolder then
                                                    local blob = toysFolder:FindFirstChild("CreatureBlobman")
                                                    if blob then
                                                        local rd = blob:FindFirstChild("RightDetector")
                                                        local rw = rd and rd:FindFirstChild("RightWeld")
                                                        if rd and rw then
                                                            hkBlob = blob
                                                        else
                                                            hkDestroyBlob(blob)
                                                        end
                                                    end
                                                end

                                                if not hkBlob then
                                                    task.spawn(function()
                                                        pcall(function()
                                                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                                                "CreatureBlobman",
                                                                CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15),
                                                                Vector3.new(0, 0, 0)
                                                            )
                                                        end)
                                                    end)
                                                end

                                                task.wait()
                                            end
                                        end
                                    end
                                end
                            end

                            task.wait()
                        end
                    end

                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = j
                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
                    end
                end
            end

            if not hkBlobRunning and hkBlob then
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent == hkBlob then
                    LocalPlayer.Character.Humanoid.Sit = false
                end

                task.wait()

                if hkBlob and hkBlob:FindFirstChild("HumanoidRootPart") then
                    hkBlob.HumanoidRootPart.CFrame = CFrame.new(0, 1e15, 0)
                end
            end

            hkExpectingBlob = false
            task.wait()
        end
    end

    task.spawn(hkBlobService)

    ServR:AddCheckbox("BlobKickAll", {
        Text = "Blobman Kick All",
        Default = false,
        Callback = function(Value)
            hkKickAll = Value
        end
    })

    ServR:AddCheckbox("BlobKillAll", {
        Text = "Blobman Kill All",
        Default = false,
        Callback = function(Value)
            hkKillAll = Value
        end
    })
end

do
    
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    Workspace = game:GetService("Workspace")
    CreateLine = ReplicatedStorage.GrabEvents.CreateGrabLine

    lineLagActive = false
    lineLevel = 5
    lineAmount = 500

    local function updateLineAmount()
        -- Level 1 = 100, Level 10 = 1000
        lineAmount = 100 + (lineLevel - 1) * 100
    end

    ServL:AddSlider("LineLagLevel", {
        Text = "Line Lag Level (1-10)",
        Default = 5,
        Min = 1,
        Max = 10,
        Rounding = 0,
        Callback = function(v)
            lineLevel = v
            updateLineAmount()
        end
    })

  ServL:AddCheckbox("LineLag", {
        Text = "Line Lag",
        Default = false,
        Callback = function(v)
            lineLagActive = v
            if v then
                task.spawn(function()
                    while lineLagActive do
                        for i = 1, lineAmount do
                            pcall(function()
                                CreateLine:FireServer(Workspace.SpawnLocation, CFrame.new(0, 9e9, 0))
                            end)
                        end
                        task.wait(1)
                    end
                end)
            end
        end
    })
end

do
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    Workspace = game:GetService("Workspace")
    LocalPlayer = game:GetService("Players").LocalPlayer
    R = ReplicatedStorage

    monsterLagEnabled = false
    monsterLagTask = nil

    ServL:AddCheckbox("OatLagInstant", {
        Text = "Oat Lag (Instant)",
        Default = false,
        Callback = function(Value)
            monsterLagEnabled = Value
            
            if Value then
                monsterLagTask = task.spawn(function()
                    GrabEvents = R:FindFirstChild("GrabEvents")
                    CreateLine = GrabEvents and GrabEvents:FindFirstChild("CreateGrabLine")
                    
                    if not CreateLine then
                        monsterLagEnabled = false
                        return
                    end

                    while monsterLagEnabled and CreateLine do
                        spawnLocation = Workspace:FindFirstChild("SpawnLocation") 
                            or Workspace:FindFirstChild("Spawn") 
                            or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))

                        if spawnLocation then
                            randomX = math.random(-9e9, 9e9)
                            randomZ = math.random(-9e9, 9e9)
                            CreateLine:FireServer(spawnLocation, CFrame.new(randomX, 0, randomZ))
                        end
                        task.wait()
                    end
                end)
            else
                if monsterLagTask then
                    task.cancel(monsterLagTask)
                    monsterLagTask = nil
                end
            end
        end
    })
end

do
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    R = ReplicatedStorage

    packetLagActive = false
    packetLagTask = nil
    packetLagStrength = 6250

    ServL:AddSlider("PacketLagStrength", {
        Text = "Packet Lag Strength",
        Min = 100,
        Max = 6250,
        Default = 6250,
        Rounding = 0,
        Callback = function(v)
            packetLagStrength = v
        end
    })

    ServL:AddButton({
        Text = "Send Packet Lag (Once)",
        Callback = function()
            GrabEvents = R:FindFirstChild("GrabEvents")
            ExtendGrabLine = GrabEvents and GrabEvents:FindFirstChild("ExtendGrabLine")
            if not ExtendGrabLine then return end
            pcall(function()
                ExtendGrabLine:FireServer(string.rep("😂😂😂😂🤣🤣🤣🤣", 100 * packetLagStrength))
            end)
            Library:Notify({
                Title = "Packet Lag",
                Description = "Packet sent! Strength: " .. packetLagStrength,
                Duration = 3
            })
        end
    })

    ServL:AddCheckbox("PacketLag", {
        Text = "Packet Lag (Loop)",
        Default = false,
        Callback = function(Value)
            packetLagActive = Value
            if Value then
                packetLagTask = task.spawn(function()
                    GrabEvents = R:FindFirstChild("GrabEvents")
                    ExtendGrabLine = GrabEvents and GrabEvents:FindFirstChild("ExtendGrabLine")
                    if not ExtendGrabLine then
                        packetLagActive = false
                        return
                    end
                    while packetLagActive do
                        task.wait(1)
                        pcall(function()
                            ExtendGrabLine:FireServer(string.rep("😂😂😂😂🤣🤣🤣🤣", 100 * packetLagStrength))
                        end)
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
end

Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
ContextActionService = game:GetService('ContextActionService')
LocalPlayer = Players.LocalPlayer
SpawnToyRF = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
ToyFriendlyNames = {
    PalletLightBrown = 'Pallet',
    BallSnowball = 'Snowball',
    BombBalloon = 'Balloon Bomb',
    BombDarkMatter = 'Dark Matter Bomb',
    BombMissile = 'Missile',
    ArmChairBlue = 'Armchair (Blue)',
    ArmChairBrownGray = 'Armchair (Brown/Gray)',
    ArmChairDarkGray = 'Armchair (Dark Gray)',
    ArmChairLightBrownGray = 'Armchair (Light Brown)',
    ArmChairPink = 'Armchair (Pink)',
    ArmChairWhite = 'Armchair (White)',
    BathroomShower = 'Shower',
    BathroomSink = 'Sink',
    BedBlanketBlue = 'Bed (Blue)',
    BedFramedOrange = 'Bed (Orange)',
    BedFuton = 'Futon',
    ChildrensChair = 'Kids Chair',
    ChildrensCouch = 'Kids Couch',
    ChildrensDesk = 'Kids Desk',
    ChildrensShelf = 'Kids Shelf',
    ChildrensTable = 'Kids Table',
    ChildrensTableBench = 'Kids Table Bench',
    ChildrensTableBenchSmall = 'Kids Bench (Small)',
    ClockAlarm = 'Alarm Clock',
    ComputerLaptopOld = 'Old Laptop',
    CouchBlue = 'Couch (Blue)',
    CouchBrownGray = 'Couch (Brown/Gray)',
    CouchDarkGray = 'Couch (Dark Gray)',
    CouchLightBrownGray = 'Couch (Light Brown)',
    CouchPink = 'Couch (Pink)',
    CouchWhite = 'Couch (White)',
    CounterCorner = 'Counter (Corner)',
    CounterSink = 'Counter (Sink)',
    CounterStraight = 'Counter (Straight)',
    FactoryBench = 'Factory Bench',
    FactoryCabinet = 'Factory Cabinet',
    FactoryChair = 'Factory Chair',
    FactoryCouch = 'Factory Couch',
    FactoryDesk = 'Factory Desk',
    FactoryDeskMini = 'Factory Mini Desk',
    FactoryLight = 'Factory Light',
    FactoryShelf = 'Factory Shelf',
    FactoryTable = 'Factory Table',
    FanElectricLittle = 'Electric Fan',
    FanPaper = 'Paper Fan',
    FridgeBlack = 'Fridge (Black)',
    FutureAngularDesk = 'Futuristic Desk',
    JukeboxBlue = 'Jukebox (Blue)',
    JukeboxOrange = 'Jukebox (Orange)',
    JapaneseBanner = 'Japanese Banner',
    JapaneseBench = 'Japanese Bench',
    JapaneseChair = 'Japanese Chair',
    JapaneseCouch = 'Japanese Couch',
    JapaneseDeskMini = 'Japanese Mini Desk',
    JapaneseDresser = 'Japanese Dresser',
    JapaneseLantern = 'Japanese Lantern',
    JapaneseShelf = 'Japanese Shelf',
    JapaneseTable = 'Japanese Table',
    LadderLightBrown = 'Ladder',
    LightLampGray = 'Floor Lamp',
    MachineWasher = 'Washing Machine',
    NormalBench = 'Bench',
    NormalDesk = 'Desk',
    NormalCabinet = 'Cabinet',
    NormalShelves = 'Shelves',
    OvenBlack = 'Oven (Black)',
    OvenDarkGray = 'Oven (Dark Gray)',
    OvenLightGray = 'Oven (Light Gray)',
    OvenMicrowaveWhite = 'Microwave',
    OvenRusty = 'Rusty Oven',
    PlantPottedBonsai = 'Bonsai',
    PlantPottedCactus = 'Cactus',
    PlantPottedTree = 'Potted Tree',
    PlantPottedTreeChristmas = 'Christmas Tree',
    RedGateGong = 'Gong',
    SpookyBench = 'Spooky Bench',
    SpookyCabinet = 'Spooky Cabinet',
    SpookyChair = 'Spooky Chair',
    SpookyCouch = 'Spooky Couch',
    SpookyDesk = 'Spooky Desk',
    SpookyStool = 'Spooky Stool',
    SpookyShelf = 'Spooky Shelf',
    SpookyTable = 'Spooky Table',
    TableLunchTable = 'Lunch Table',
    TableSmallLabTable = 'Lab Table',
    TableWoodFourLegsBrown = 'Wood Table (4 Legs)',
    TableWoodTwoLegs = 'Wood Table (2 Legs)',
    TelevisionFlatscreen = 'Flatscreen TV',
    TelevisionGray = 'Old TV',
    ToiletGold = 'Toilet (Gold)',
    ToiletWhite = 'Toilet (White)',
    Airhorn = 'Airhorn',
    Boombox = 'Boombox',
    BubbleBlower = 'Bubble Blower',
    BucketPaint = 'Paint Bucket',
    Campfire = 'Campfire',
    CreatureBlobman = 'Blobman',
    CreatureRobot = 'Robot',
    DiscoColorBall = 'Disco Ball',
    FireExtinguisher = 'Fire Extinguisher',
    FireworkSmokeBomb = 'Smoke Bomb',
    FireworkMissile = 'Firework Missile',
    FireworkSparkler = 'Sparkler',
    FloatingIsland = 'Floating Island',
    FlyingToyHelicopter = 'Helicopter',
    FlyingToyPlane = 'Plane',
    FlyingToyUfo = 'UFO',
    FoodHotSauce = 'Hot Sauce',
    InstrumentBrassBugle = 'Bugle',
    InstrumentBrassTrumpet = 'Trumpet',
    InstrumentBrassVuvuzela = 'Vuvuzela',
    InstrumentDrumBongos = 'Bongos',
    InstrumentDrumSnare = 'Snare Drum',
    InstrumentGuitarAcoustic = 'Acoustic Guitar',
    InstrumentGuitarBanjo = 'Banjo',
    InstrumentGuitarLyre = 'Lyre',
    InstrumentGuitarUkulele = 'Ukulele',
    InstrumentGuitarViolin = 'Violin',
    InstrumentPianoKeyboard = 'Piano Keyboard',
    InstrumentPianoMelodica = 'Melodica',
    InstrumentVoiceMicrophone = 'Microphone',
    InstrumentWoodwindOcarina = 'Ocarina',
    InstrumentWoodwindSaxophone = 'Saxophone',
    MidiMaker = 'MIDI Maker',
    MusicKeyboard = 'Music Keyboard',
    NinjaKatana = 'Katana',
    NinjaKunai = 'Kunai',
    NinjaShuriken = 'Shuriken',
    NpcRobloxianMascot = 'Robloxian Mascot',
    PaperPlane = 'Paper Plane',
    PetSnowman = 'Snowman',
    PetTurkeyLeg = 'Turkey Leg',
    PlayhouseGingerbread = 'Gingerbread House',
    PresentBig = 'Big Present',
    PresentSmall = 'Small Present',
    SantaSleigh = "Santa's Sleigh",
    SoundWaveMaker = 'Sound Wave Maker',
    SprayCanWD = 'Spray Can',
    ToolCleaver = 'Cleaver',
    ToolDiggingForkRusty = 'Rusty Fork',
    ToolPencil = 'Pencil',
    ToolPickaxe = 'Pickaxe',
    TractorGreen = 'Tractor (Green)',
    TractorOrange = 'Tractor (Orange)',
    TractorRed = 'Tractor (Red)',
    YouDecoy = 'Decoy',
    YouLittle = 'Mini Me',
    AnvilGray = 'Anvil',
    BallBasketball = 'Basketball',
    BellBig = 'Big Bell',
    BellSmall = 'Small Bell',
    BookNormal = 'Book',
    BookManyPages = 'Big Book',
    BoxCrateWood = 'Wooden Crate',
    DiceBig = 'Big Dice',
    DiceSmall = 'Small Dice',
    DrawerLightBrown = 'Drawer',
    FlagUnitedStatesOfAmerica = 'USA Flag',
    FoodPlate = 'Plate',
    GlassBoxGray = 'Glass Box',
    HayBale = 'Hay Bale',
    MineralDiamond = 'Diamond',
    MineralCrystalPink = 'Pink Crystal',
    MineralIngotGold = 'Gold Ingot',
    PoopPile = 'Poop',
    PoopPileSparkle = 'Sparkle Poop',
    RollerGrayPurple = 'Roller',
    Snowflake = 'Snowflake',
    TeapotUtah = 'Teapot',
    TetracubeI = 'Tetracube I',
    TetracubeJ = 'Tetracube J',
    TetracubeL = 'Tetracube L',
    TetracubeO = 'Tetracube O',
    TetracubeS = 'Tetracube S',
    TetracubeT = 'Tetracube T',
    TetracubeZ = 'Tetracube Z',
    ToyAnimalBear = 'Bear',
    ToyAnimalDuck = 'Duck',
    ToyAnimalFrog = 'Frog',
    ToyAnimalTiger = 'Tiger',
    ToyAnimalUnicorn = 'Unicorn',
    YouFigurine = 'Figurine',
    BallMagicLight = 'Magic Light Ball',
    LightLampDeskLampBent = 'Desk Lamp',
    SpotlightBlue = 'Spotlight (Blue)',
    SpotlightCyan = 'Spotlight (Cyan)',
    SpotlightCyanBlue = 'Spotlight (Cyan/Blue)',
    SpotlightCyanGreen = 'Spotlight (Cyan/Green)',
    SpotlightGreen = 'Spotlight (Green)',
    SpotlightMagenta = 'Spotlight (Magenta)',
    SpotlightRed = 'Spotlight (Red)',
    SpotlightWhite = 'Spotlight (White)',
    SpotlightYellow = 'Spotlight (Yellow)',
    SpotlightYellowGreen = 'Spotlight (Yellow/Green)',
    SpotlightYellowRed = 'Spotlight (Yellow/Red)',
    SpookyCandle1 = 'Candle (x1)',
    SpookyCandle3 = 'Candle (x3)',
    SpookyCandle5 = 'Candle (x5)',
    CupMugBrown = 'Mug (Brown)',
    CupMugWhite = 'Mug (White)',
    FoodBanana = 'Banana',
    FoodBread = 'Bread',
    FoodBroccoli = 'Broccoli',
    FoodCakePink = 'Pink Cake',
    FoodCoconut = 'Coconut',
    FoodDippyEgg = 'Dippy Egg',
    FoodDonut = 'Donut',
    FoodFrenchFries = 'French Fries',
    FoodHamburger = 'Burger',
    FoodHotdog = 'Hotdog',
    FoodMayonnaise = 'Mayo',
    FoodMeatStick = 'Meat Stick',
    FoodMushroomPoison = 'Poison Mushroom',
    FoodPizzaCheese = 'Cheese Pizza',
    FoodPizzaPepperoni = 'Pepperoni Pizza',
    FoodSodaCan = 'Soda Can',
}
ToyDropdownValues = {}
DisplayToInternal = {}

for internalName, displayName in pairs(ToyFriendlyNames)do
    table.insert(ToyDropdownValues, displayName)

    DisplayToInternal[displayName] = internalName
end

table.sort(ToyDropdownValues)

SelectedToy = 'PalletLightBrown'

function SpawnSelectedToy()
    Character = LocalPlayer.Character

    if not Character then
        return
    end
    CamPart = Character:FindFirstChild('CamPart')
    if not CamPart then
        return
    end

    SpawnToyRF:InvokeServer(SelectedToy, CamPart.CFrame, Vector3.new(0, CamPart.Orientation.Y, 0))
end

Groupbox = Tabs.ToyTab:AddLeftGroupbox("Spawn Toy", "house")

SpawnToyToggle = Groupbox:AddCheckbox('SpawnToyToggle', {
    Text = 'Spawn Toy',
    Default = false,
})

Groupbox:AddDropdown('SpawnToyDropdown', {
    Values = ToyDropdownValues,
    Default = ToyFriendlyNames[SelectedToy],
    Searchable = true,
    Multi = false,
    Text = 'Selected Toy',
})
Options.SpawnToyDropdown:OnChanged(function(Value)
    SelectedToy = DisplayToInternal[Value]
end)

SpawnToyKeybind = SpawnToyToggle:AddKeyPicker('SpawnToyKeybind', {
    Default = 'Tab',
    Text = 'Spawn Keybind',
    Mode = 'Toggle',
    Callback = function()
        if not Toggles.SpawnToyToggle.Value then
            return
        end

        SpawnSelectedToy()
    end,
})

SpawnToyKeybind:OnChanged(function()
    print('Spawn keybind changed to:', SpawnToyKeybind.Value)
end)
Toggles.SpawnToyToggle:OnChanged(function(State)
    print('Spawn Toy Toggle:', State)
end)

Groupbox = Tabs.ToyTab:AddLeftGroupbox('Object Flinger', 'box')
Players = game:GetService('Players')
me = Players.LocalPlayer
rs = game:GetService('ReplicatedStorage')
w = game:GetService('Workspace')
SelectedPlayers = {}
playerNames = {}

for _, p in ipairs(Players:GetPlayers())do
    if p ~= me then
        table.insert(playerNames, p.Name)
    end
end

Players.PlayerAdded:Connect(function(p)
    if p ~= me then
        table.insert(playerNames, p.Name)
    end
end)
Groupbox:AddDropdown('TargetPlayers', {
    Values = playerNames,
    Default = playerNames[1] or '',
    Multi = true,
    Text = 'Select Players',
})
Options.TargetPlayers:OnChanged(function(selected)
    SelectedPlayers = {}

    for name, enabled in pairs(selected)do
        if enabled then
            table.insert(SelectedPlayers, name)
        end
    end

    print('[Selected Players]:', SelectedPlayers)

    for _, player in ipairs(Players:GetPlayers())do
        if player ~= me then
            local isSelected = false

            for _, name in ipairs(SelectedPlayers)do
                if player.Name == name then
                    isSelected = true

                    break
                end
            end

            player:SetAttribute('IsAdded', isSelected)
        end
    end
end)

function isPlayerSelected(player)
    for _, name in ipairs(SelectedPlayers)do
        if player.Name == name then
            return true
        end
    end

    return false
end

Players.PlayerAdded:Connect(function(player)
    task.wait(0.5)

    if player ~= me then
        player:SetAttribute('IsAdded', isPlayerSelected(player))
    end
end)
Players.PlayerRemoving:Connect(function(player)
    for i, name in ipairs(SelectedPlayers)do
        if name == player.Name then
            table.remove(SelectedPlayers, i)

            break
        end
    end
end)

for _, player in ipairs(Players:GetPlayers())do
    if player ~= me then
        player:SetAttribute('IsAdded', false)
    end
end

getgenv().Players = game:GetService('Players')
getgenv().ReplicatedStorage = game:GetService('ReplicatedStorage')
getgenv().RunService = game:GetService('RunService')
getgenv().Workspace = game:GetService('Workspace')
getgenv().lp = Players.LocalPlayer
getgenv().char = lp.Character or lp.CharacterAdded:Wait()
getgenv().root = char:WaitForChild('HumanoidRootPart')
getgenv().folder = Workspace:WaitForChild(lp.Name .. 'SpawnedInToys')
getgenv().isProcessing = false
getgenv().isEnabled = false
getgenv().targetIndex = 1
getgenv().flungMap = {}
getgenv().currentDecoy = nil
getgenv().currentTarget = nil
getgenv().conn = nil
getgenv().UNIQUE_ATTRIBUTE = 'OwnedByScript'
getgenv().FLING_FORCE = 500
getgenv().ownershipMonitors = {}
getgenv().SelectedPlayers = {}
getgenv().toyMap = {
    YouLittle = 'Head',
    YouDecoy = 'Head',
    DiceSmall = 'SoundPart',
    DiceBig = 'SoundPart',
}
getgenv().selectedToy = 'DiceBig'
getgenv().getTargets = function()
    getgenv().targetsList = {}

    for _, p in ipairs(Players:GetPlayers())do
        if p ~= lp and p:GetAttribute('IsAdded') and p.Character and p.Character:FindFirstChild('HumanoidRootPart') then
            table.insert(getgenv().targetsList, p)
        end
    end

    return getgenv().targetsList
end
getgenv().velocityHistory = {}
getgenv().isFlung = function(p)
    local h = p.Character and p.Character:FindFirstChild('HumanoidRootPart')

    if not h then
        return true
    end

    local posY = h.Position.Y
    local v = h.Velocity
    local horizontalMag = Vector3.new(v.X, 0, v.Z).Magnitude

    if not velocityHistory[p] then
        velocityHistory[p] = {}
    end

    local hist = velocityHistory[p]

    table.insert(hist, {
        tick(),
        v,
        posY,
    })

    if #hist > 15 then
        table.remove(hist, 1)
    end

    local heightOK = posY > -100 and posY < 1500
    local verticalOK = math.abs(v.Y) < 180
    local horizOK = horizontalMag < 250
    local badFrames = 0

    for _, data in ipairs(hist)do
        local vel = data[2]
        local y = data[3]

        if y > 3000 or y < -150 then
            badFrames += 1
        elseif math.abs(vel.Y) > 220 or Vector3.new(vel.X, 0, vel.Z).Magnitude > 300 then
            badFrames += 1
        end
    end

    if badFrames / #hist >= 0.4 then
        return true
    end

    return false
end
getgenv().isGrounded = function(p)
    local h = p.Character and p.Character:FindFirstChild('HumanoidRootPart')

    return h and h.Position.Y < 100 and math.abs(h.Velocity.Y) < 10
end
getgenv().pickNextTarget = function(targets)
    local c = #targets

    if c == 0 then
        return nil
    end

    for i = 1, c do
        targetIndex = ((targetIndex + i - 1) % c) + 1

        local t = targets[targetIndex]

        if not flungMap[t] or isGrounded(t) then
            return t
        end
    end

    return nil
end
getgenv().monitorOwnership = function(toy, toyPart)
    if ownershipMonitors[toy] then
        ownershipMonitors[toy]:Disconnect()

        ownershipMonitors[toy] = nil
    end

    ownershipMonitors[toy] = RunService.Heartbeat:Connect(function()
        if not toy or not toy.Parent then
            if ownershipMonitors[toy] then
                ownershipMonitors[toy]:Disconnect()

                ownershipMonitors[toy] = nil
            end

            return
        end

        local tag = toyPart:FindFirstChild('PartOwner')

        if tag and tag:IsA('StringValue') and tag.Value ~= lp.Name and tag.Value ~= '' then
            print('[Ownership Monitor] Ownership changed to: ' .. tag.Value .. ' - Destroying toy')
            rs.MenuToys.DestroyToy:FireServer(toy)

            if toy == currentDecoy then
                currentDecoy = nil
            end
            if ownershipMonitors[toy] then
                ownershipMonitors[toy]:Disconnect()

                ownershipMonitors[toy] = nil
            end
        end
    end)
end
getgenv().spawnDecoy = function()
    if currentDecoy and currentDecoy.Parent then
        return
    end

    local toy = selectedToy or 'YouDecoy'

    rs.MenuToys.SpawnToyRemoteFunction:InvokeServer(toy, root.CFrame * CFrame.new(5, 0, 5), Vector3.new(0, 33, 0))
end
getgenv().handleDecoy = function(d)
    if currentDecoy and currentDecoy.Parent then
        return
    end

    local partName = toyMap[d.Name]

    if not partName then
        return
    end

    local toyPart = d:WaitForChild(partName)
    local pivotCF = d:GetPivot()

    rs.GrabEvents.SetNetworkOwner:FireServer(toyPart, pivotCF)
    task.wait(0.09)

    local startTime = tick()
    local success = false
    local connection

    connection = RunService.Heartbeat:Connect(function()
        local tag = toyPart:FindFirstChild('PartOwner')

        if tag and tag:IsA('StringValue') and tag.Value == lp.Name then
            success = true

            connection:Disconnect()

            if not d:GetAttribute(UNIQUE_ATTRIBUTE) then
                d:SetAttribute(UNIQUE_ATTRIBUTE, true)

                currentDecoy = d

                monitorOwnership(d, toyPart)

                if isEnabled then
                    setupFling(d)
                end
            end
        end
        if tick() - startTime >= 3 and not success then
            rs.MenuToys.DestroyToy:FireServer(d)
            connection:Disconnect()
        end
    end)
end

folder.ChildAdded:Connect(function(c)
    if toyMap[c.Name] then
        handleDecoy(c)
    end
end)
folder.ChildRemoved:Connect(function(c)
    if c == currentDecoy then
        currentDecoy = nil
    end
    if ownershipMonitors[c] then
        ownershipMonitors[c]:Disconnect()

        ownershipMonitors[c] = nil
    end
end)
RunService.Heartbeat:Connect(function()
    if not currentDecoy or not currentDecoy.Parent then
        for _, t in ipairs(folder:GetChildren())do
            if toyMap[t.Name] and t:GetAttribute(UNIQUE_ATTRIBUTE) then
                currentDecoy = t

                local toyPart = t:FindFirstChild(toyMap[t.Name])

                if toyPart and not ownershipMonitors[t] then
                    monitorOwnership(t, toyPart)
                end

                return
            end
        end

        if isEnabled then
            spawnDecoy()
        end
    end
end)

getgenv().setupFling = function(d)
    local hrp = d:FindFirstChild('HumanoidRootPart') or d.PrimaryPart or d:FindFirstChild(toyMap[d.Name])

    if not hrp then
        return
    end

    d.PrimaryPart = hrp
    hrp.CanCollide = false

    local bt = Instance.new('BodyThrust')

    bt.Force = Vector3.zero
    bt.Parent = hrp

    local bav = Instance.new('BodyAngularVelocity')

    bav.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bav.AngularVelocity = Vector3.new(-1E6, -1E6, -1E6)
    bav.Parent = hrp

    local params = RaycastParams.new()

    params.FilterType = Enum.RaycastFilterType.Blacklist
    params.FilterDescendantsInstances = {
        lp.Character,
        d,
    }
    params.IgnoreWater = true
    conn = RunService.Heartbeat:Connect(function()
        if not d or not d.Parent or not isEnabled then
            if conn then
                conn:Disconnect()
            end
            if bt.Parent then
                bt:Destroy()
            end
            if bav.Parent then
                bav:Destroy()
            end

            return
        end

        w.FallenPartsDestroyHeight = 0 / 0

        local tList = getTargets()

        for p in pairs(flungMap)do
            if not table.find(tList, p) or not p.Character or isGrounded(p) then
                flungMap[p] = nil
            end
        end

        if currentTarget and (not currentTarget.Character or isFlung(currentTarget)) then
            flungMap[currentTarget] = true
            currentTarget = nil
        end
        if not currentTarget then
            currentTarget = pickNextTarget(tList)
        end

        local destCF

        if currentTarget and currentTarget.Character then
            local tHRP = currentTarget.Character:FindFirstChild('HumanoidRootPart')

            if tHRP then
                local vel = tHRP.Velocity
                local speed = vel.Magnitude
                local time = math.clamp(speed / 40, 0.25, 0.6)
                local predicted = tHRP.Position + vel * time + Vector3.new(0, 2, 0)
                local dir = (predicted - hrp.Position).Unit
                local dist = (predicted - hrp.Position).Magnitude
                local result = w:Raycast(hrp.Position, dir * dist, params)

                if result and result.Instance and result.Instance:IsDescendantOf(currentTarget.Character) then
                    destCF = CFrame.new(result.Position)
                else
                    destCF = CFrame.new(predicted)
                end
            end
        end
        if not destCF then
            destCF = CFrame.new(0, 5000, 0)
        end

        for _, p in ipairs(d:GetDescendants())do
            if p:IsA('BasePart') then
                p.CFrame = destCF
            end
        end

        if bt.Parent then
            bt.Force = (destCF.Position - hrp.Position).Unit * FLING_FORCE
        end
    end)
end

lp.CharacterAdded:Connect(function(newChar)
    getgenv().char = newChar
    getgenv().root = newChar:WaitForChild('HumanoidRootPart')

    print('[Respawn] Character and HumanoidRootPart updated')
end)
lp.CharacterRemoving:Connect(function(oldChar)
    if char == oldChar then
        getgenv().char, getgenv().root = nil, nil
    end
end)

getgenv().process = function()
    if isProcessing then
        return
    end

    isProcessing = true

    if not currentDecoy or not currentDecoy.Parent or not currentDecoy:GetAttribute(UNIQUE_ATTRIBUTE) then
        if isEnabled then
            spawnDecoy()
        end
    else
        setupFling(currentDecoy)
    end

    isProcessing = false
end

Groupbox:AddCheckbox('LoopFlingToggle', {
    Text = 'Loop Fling Target',
    Tooltip = 'Loop fling the selected target with the selected object',
    Default = false,
})
Toggles.LoopFlingToggle:OnChanged(function(state)
    isEnabled = state

    if state then
        process()
    else
        isProcessing = false

        if conn then
            conn:Disconnect()
        end

        for toy, mon in pairs(ownershipMonitors)do
            mon:Disconnect()
        end

        ownershipMonitors = {}

        if currentDecoy and currentDecoy.Parent then
            rs.MenuToys.DestroyToy:FireServer(currentDecoy)
        end

        currentDecoy = nil
    end
end)
Groupbox:AddDropdown('FlingToyDropdown', {
    Values = {
        'YouLittle',
        'YouDecoy',
        'DiceSmall',
        'DiceBig',
    },
    Default = 'DiceBig',
    Multi = false,
    Text = 'Fling Toy',
})
Options.FlingToyDropdown:OnChanged(function(option)
    selectedToy = option

    print('[Fling Toy Selected] -> ' .. selectedToy)
end)



getgenv().FigureGrabModule = {}
FGM = FigureGrabModule
FGM.Players = game:GetService('Players')
FGM.RunService = game:GetService('RunService')
FGM.ReplicatedStorage = game:GetService('ReplicatedStorage')
FGM.UserInputService = game:GetService('UserInputService')
FGM.Workspace = game:GetService('Workspace')
FGM.TweenService = game:GetService('TweenService')
FGM.LocalPlayer = FGM.Players.LocalPlayer
FGM.Mouse = FGM.LocalPlayer:GetMouse()

_grabFolder = FGM.ReplicatedStorage:WaitForChild('GrabEvents', 10)

FGM.GrabEvents = _grabFolder
FGM.SetNetworkOwner = _grabFolder and _grabFolder:WaitForChild('SetNetworkOwner', 5) or nil
FGM.DestroyLine = _grabFolder and _grabFolder:WaitForChild('DestroyGrabLine', 5) or nil
FGM.CreateLine = _grabFolder and _grabFolder:WaitForChild('CreateGrabLine', 5) or nil

_menuToys = FGM.ReplicatedStorage:WaitForChild('MenuToys', 10)
FGM.MenuToys = _menuToys
FGM.ToySpawn = _menuToys and _menuToys:WaitForChild('SpawnToyRemoteFunction', 5) or nil
FGM.DestroyToy = _menuToys and _menuToys:WaitForChild('DestroyToy', 5) or nil

function _FireSetNetworkOwner(part, cf)
    if FGM.SetNetworkOwner and part and part.Parent then
        pcall(function()
            FGM.SetNetworkOwner:FireServer(part, cf)
        end)
    end
end
function _FireDestroyLine(part)
    if FGM.DestroyLine and part and part.Parent then
        pcall(function()
            FGM.DestroyLine:FireServer(part)
        end)
    end
end

function FGM.ToggleAutoRagdoll(enabled)
    FGM.State.AutoRagdollEnabled = enabled
    if FGM.State.AutoRagdollConnection then
        FGM.State.AutoRagdollConnection:Disconnect()
        FGM.State.AutoRagdollConnection = nil
    end
    if not enabled then
        if FGM.State.RagdollPallet then
            pcall(function() FGM.DestroyToy:FireServer(FGM.State.RagdollPallet) end)
        end
        FGM.State.RagdollPallet = nil
        FGM.State.RagdollSoundPart = nil
        return
    end

    task.spawn(function()
        myChar = FGM.LocalPlayer.Character
        myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then return end

        FGM.MyToys = workspace:FindFirstChild(FGM.LocalPlayer.Name .. "SpawnedInToys")
        if not FGM.MyToys then return end

        pallet = FGM.MyToys:FindFirstChild("RagdollPallet") or FGM.MyToys:FindFirstChild("PalletLightBrown")
        if not pallet then
            FGM.ToySpawn:InvokeServer("PalletLightBrown", myHRP.CFrame * CFrame.new(5, 5, 20), Vector3.new(0, 0, 0))
            t = tick() + 5
            repeat task.wait(0.05) until FGM.MyToys:FindFirstChild("PalletLightBrown") or tick() > t
            pallet = FGM.MyToys:FindFirstChild("PalletLightBrown")
        end
        if not pallet then return end

        pallet.Name = "RagdollPallet"
        soundPart = pallet:WaitForChild("SoundPart", 5)
        if not soundPart then return end

        t2 = tick() + 3
        repeat
            FGM.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            task.wait()
        until soundPart:FindFirstChild("PartOwner") or tick() > t2

        soundPart.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)
        for _, v in pairs(pallet:GetDescendants()) do
            if v:IsA("BasePart") then v.Transparency = 1 v.CanCollide = false end
        end

        FGM.State.RagdollPallet = pallet
        FGM.State.RagdollSoundPart = soundPart

        FGM.State.AutoRagdollConnection = FGM.RunService.Heartbeat:Connect(function()
            if not FGM.State.AutoRagdollEnabled then return end
            sp = FGM.State.RagdollSoundPart
            if not sp or not sp.Parent then
                if FGM.State.AutoRagdollConnection then
                    FGM.State.AutoRagdollConnection:Disconnect()
                    FGM.State.AutoRagdollConnection = nil
                end
                FGM.State.RagdollPallet = nil
                FGM.State.RagdollSoundPart = nil
                return
            end

            targets = {}
            if FGM.State.FigureGrabEnabled and FGM.State.TargetCharacter then
                table.insert(targets, FGM.State.TargetCharacter)
            end
            if FGM.State.SeveralEnabled then
                for _, e in ipairs(FGM.State.SeveralTargets) do table.insert(targets, e.char) end
            end

            for _, targetChar in ipairs(targets) do
                hrp = targetChar:FindFirstChild("HumanoidRootPart")
                hum = targetChar:FindFirstChild("Humanoid")
                if hrp and hum then
                    ragdolled = hum:FindFirstChild("Ragdolled")
                    if ragdolled and ragdolled.Value == false then
                        task.spawn(function()
                            sp.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
                            sp.CFrame = hrp.CFrame
                            task.wait(0.05)
                            if sp and sp.Parent then sp.CFrame = CFrame.new(0, 1e9, 0) end
                        end)
                    end
                end
            end
        end)
    end)
end

function FGM.BringTargetToMe(target)
    if not target then return false end
    myChar = FGM.LocalPlayer.Character
    myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then return false end
    savedPos = myRoot.CFrame
    if not target.Character then return false end
    tRoot = target.Character:FindFirstChild("HumanoidRootPart")
    tHum = target.Character:FindFirstChild("Humanoid")
    if not tRoot or not tHum then return false end
    myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 2.5)
    myRoot.AssemblyLinearVelocity = Vector3.zero
    task.wait(0.05)
    for i = 1, 8 do
        pcall(function() FGM.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end)
        task.wait(0.01)
    end
    for i = 1, 4 do
        pcall(function() FGM.DestroyLine:FireServer(tRoot) end)
        task.wait(0.01)
    end
    tRoot.CFrame = savedPos * CFrame.new(0, 0, 2)
    tRoot.AssemblyLinearVelocity = Vector3.zero
    pcall(function() tHum.PlatformStand = true end)
    task.wait(0.05)
    myRoot.CFrame = savedPos
    myRoot.AssemblyLinearVelocity = Vector3.zero
    return true
end

function FGM.JumpAndReturn()
    myChar = FGM.LocalPlayer.Character
    myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myHRP then return end
    FGM.State.IsReturning = true
    if FGM.State.SavedPosition then
        myHRP.CFrame = FGM.State.SavedPosition
        myHRP.Velocity = Vector3.zero
        myHRP.AssemblyAngularVelocity = Vector3.zero
    end
    FGM.State.IsReturning = false
end

FGM.State = {
    FigureGrabEnabled = false,
    FigureGrabConnection = nil,
    TargetCharacter = nil,
    TargetPlayer = nil,
    AnimationCopyEnabled = false,
    VectorZero = Vector3.new(0, 0, 0),
    PalletForRagdoll = nil,
    RagdollConnections = {},
    LoopRagdollEnabled = false,
    RespawnConnection = nil,
    RejoinConnection = nil,
    SmoothedCFrames = {},
    SelectedLimb = 'Torso',
    AutoGrabActive = false,
    AutoGrabConnection = nil,
    LastGrabTargetRef = nil,
    DistanceTPInProgress = false,
    FreezeLimbsEnabled = false,
    FrozenCFrames = {},
    VelSuppressEnabled = false,
    GravityFlipEnabled = false,
    LockRotationEnabled = false,
    LockedRotation = CFrame.identity,
    ForceLookAtEnabled = false,
    ForceUprightEnabled = false,
    FlingOnReleaseEnabled = false,
    FlingForce = 300,
    HoldAtCameraEnabled = false,
    OscillateEnabled = false,
    OscillateSpeed = 2,
    OscillateAmount = 3,
    OscillateTimer = 0,
    SpinEnabled = false,
    SpinSpeed = 180,
    SpinAngle = 0,
    ActiveNetworkTarget = nil,
    HighlightedLimb = nil,
    LimbHighlight = nil,
    PersistentGrabActive = false,
    PersistentGrabThread = nil,
    SavedPosition = nil,
    AutoRagdollToggle = false,
    AutoRagdollEnabled = false,
    AutoRagdollConnection = nil,
    RagdollPallet = nil,
    RagdollSoundPart = nil,
    SeveralEnabled = false,
    SeveralTargets = {},
    LastTargetUserId = nil,
    LastTargetHRP = nil,
    IsReturning = false,
}
FGM.Configuration = {
    DampingEnabled = true,
    DampingSpeed = 12,
    SnapEnabled = false,
    SnapPosStep = 0.5,
    SnapRotStep = 15,
    LineDistance = 0,
    AutoTPDistance = 40,
    HoldPosition = { X = 0, Y = 0, Z = -5 },
    HoldRotation = { X = 0, Y = 0, Z = 0 },
    LeftArmPosition = { X = 0, Y = 0, Z = 0 },
    LeftArmRotation = { X = 0, Y = 0, Z = 0 },
    RightArmPosition = { X = 0, Y = 0, Z = 0 },
    RightArmRotation = { X = 0, Y = 0, Z = 0 },
    LeftLegPosition = { X = 0, Y = 0, Z = 0 },
    LeftLegRotation = { X = 0, Y = 0, Z = 0 },
    RightLegPosition = { X = 0, Y = 0, Z = 0 },
    RightLegRotation = { X = 0, Y = 0, Z = 0 },
    HeadPosition = { X = 0, Y = 0, Z = 0 },
    HeadRotation = { X = 0, Y = 0, Z = 0 },
}
FGM.Presets = {
    Pose1 = {
        HoldPosition = { X = 0, Y = 0, Z = -7.5 },
        HoldRotation = { X = 90, Y = 0, Z = 108 },
        LeftArmPosition = { X = -1.5, Y = 1, Z = -1 },
        LeftArmRotation = { X = 283, Y = 0, Z = 0 },
        RightArmPosition = { X = 1.5, Y = 0.5, Z = 1 },
        RightArmRotation = { X = 270, Y = 0, Z = 0 },
        LeftLegPosition = { X = 0.5, Y = -1.5, Z = 0.5 },
        LeftLegRotation = { X = 312, Y = 0, Z = 0 },
        RightLegPosition = { X = -0.5, Y = -1.5, Z = 0.5 },
        RightLegRotation = { X = 283, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 1.5, Z = 0 },
        HeadRotation = { X = 0, Y = 0, Z = 0 },
    },
    Pose2 = {
        HoldPosition = { X = 0, Y = -1.5, Z = -12.5 },
        HoldRotation = { X = 272, Y = 0, Z = 0 },
        LeftArmPosition = { X = -1, Y = 1, Z = -0.5 },
        LeftArmRotation = { X = 90, Y = 0, Z = 0 },
        RightArmPosition = { X = 1, Y = 1, Z = -0.5 },
        RightArmRotation = { X = 90, Y = 0, Z = 0 },
        LeftLegPosition = { X = 1, Y = -1, Z = -0.5 },
        LeftLegRotation = { X = 90, Y = 0, Z = 0 },
        RightLegPosition = { X = -1, Y = -1, Z = -0.5 },
        RightLegRotation = { X = 90, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 1, Z = 1 },
        HeadRotation = { X = 90, Y = 0, Z = 0 },
    },
    Pose3 = {
        HoldPosition = { X = 0, Y = -5.5, Z = -4 },
        HoldRotation = { X = 0, Y = 0, Z = 0 },
        LeftArmPosition = { X = 1, Y = 7.5, Z = 1.5 },
        LeftArmRotation = { X = 0, Y = 0, Z = 0 },
        RightArmPosition = { X = 1, Y = 6, Z = 1.5 },
        RightArmRotation = { X = 0, Y = 0, Z = 0 },
        LeftLegPosition = { X = 0.5, Y = 5, Z = 1.5 },
        LeftLegRotation = { X = 0, Y = 0, Z = 92 },
        RightLegPosition = { X = -0.5, Y = 5, Z = 1.5 },
        RightLegRotation = { X = 0, Y = 0, Z = 90 },
        HeadPosition = { X = 0, Y = 0, Z = 0 },
        HeadRotation = { X = 0, Y = 0, Z = 0 },
    },
    Pose4 = {
        HoldPosition = { X = 1.5, Y = -8.5, Z = -1.5 },
        HoldRotation = { X = 0, Y = 0, Z = 0 },
        LeftArmPosition = { X = 0, Y = 0, Z = 0 },
        LeftArmRotation = { X = 0, Y = 0, Z = 0 },
        RightArmPosition = { X = 0, Y = 0, Z = 0 },
        RightArmRotation = { X = 0, Y = 0, Z = 0 },
        LeftLegPosition = { X = 0, Y = 0, Z = 0 },
        LeftLegRotation = { X = 0, Y = 0, Z = 0 },
        RightLegPosition = { X = 1.5, Y = 0, Z = 0 },
        RightLegRotation = { X = 0, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 9, Z = 0 },
        HeadRotation = { X = 0, Y = 0, Z = 0 },
    },
    Pose5 = {
        HoldPosition = { X = 0, Y = -3, Z = -6 },
        HoldRotation = { X = 270, Y = 0, Z = 0 },
        LeftArmPosition = { X = -1, Y = 0.5, Z = 0 },
        LeftArmRotation = { X = 180, Y = 0, Z = 0 },
        RightArmPosition = { X = 1, Y = 0.5, Z = 0 },
        RightArmRotation = { X = 180, Y = 0, Z = 0 },
        LeftLegPosition = { X = 0, Y = -3, Z = 0 },
        LeftLegRotation = { X = 0, Y = 0, Z = 0 },
        RightLegPosition = { X = 0, Y = -2, Z = 0.5 },
        RightLegRotation = { X = 45, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 1.5, Z = -0.5 },
        HeadRotation = { X = 270, Y = 0, Z = 0 },
    },
    Pose6 = {
        HoldPosition = { X = 5.5, Y = 0.5, Z = -1.5 },
        HoldRotation = { X = 345, Y = 39, Z = 0 },
        LeftArmPosition = { X = 2, Y = 0.5, Z = 0 },
        LeftArmRotation = { X = 0, Y = 43, Z = 121 },
        RightArmPosition = { X = -2, Y = 0, Z = -0 },
        RightArmRotation = { X = 64, Y = 112, Z = 0 },
        LeftLegPosition = { X = -0.5, Y = -2, Z = 0 },
        LeftLegRotation = { X = 349, Y = 0, Z = 360 },
        RightLegPosition = { X = 0.5, Y = -2, Z = 0 },
        RightLegRotation = { X = 345, Y = 360, Z = 10 },
        HeadPosition = { X = 0, Y = 1.5, Z = 0 },
        HeadRotation = { X = 0, Y = 344, Z = 0 },
    },
    Pose7 = {
        HoldPosition = { X = 0, Y = -2, Z = -10 },
        HoldRotation = { X = 90, Y = 0, Z = 0 },
        LeftArmPosition = { X = -1.5, Y = 0, Z = 0 },
        LeftArmRotation = { X = 270, Y = 0, Z = 315 },
        RightArmPosition = { X = 1.5, Y = 0, Z = 0 },
        RightArmRotation = { X = 270, Y = 0, Z = 45 },
        LeftLegPosition = { X = -1, Y = -1.5, Z = 0 },
        LeftLegRotation = { X = 90, Y = 0, Z = 0 },
        RightLegPosition = { X = 1, Y = -1.5, Z = 0 },
        RightLegRotation = { X = 90, Y = 0, Z = 0 },
        HeadPosition = { X = 0, Y = 1.5, Z = 0 },
        HeadRotation = { X = 0, Y = 0, Z = 0 },
    },
    JojoStand = {
        HoldPosition = { X = -4.5, Y = 0.5, Z = -1.5 },
        HoldRotation = { X = 8, Y = 349, Z = 0 },
        LeftArmPosition = { X = 1.5, Y = 0, Z = -0 },
        LeftArmRotation = { X = 15, Y = 62, Z = 41 },
        RightArmPosition = { X = -1.5, Y = 0.5, Z = -0.5 },
        RightArmRotation = { X = 65, Y = 149, Z = 6 },
        LeftLegPosition = { X = -0.5, Y = -2, Z = 0 },
        LeftLegRotation = { X = 349, Y = 0, Z = 360 },
        RightLegPosition = { X = 0.5, Y = -2, Z = 0 },
        RightLegRotation = { X = 345, Y = 360, Z = 10 },
        HeadPosition = { X = 0, Y = 1.5, Z = 0 },
        HeadRotation = { X = 0, Y = 344, Z = 0 },
    },
}
FGM.CustomPresets = {}

LIMB_OPTIONS = {
    'Torso',
    'Head',
    'Left Arm',
    'Right Arm',
    'Left Leg',
    'Right Leg',
}
LIMB_SECTION_MAP = {
    Torso = { pos = 'HoldPosition', rot = 'HoldRotation' },
    Head = { pos = 'HeadPosition', rot = 'HeadRotation' },
    ['Left Arm'] = { pos = 'LeftArmPosition', rot = 'LeftArmRotation' },
    ['Right Arm'] = { pos = 'RightArmPosition', rot = 'RightArmRotation' },
    ['Left Leg'] = { pos = 'LeftLegPosition', rot = 'LeftLegRotation' },
    ['Right Leg'] = { pos = 'RightLegPosition', rot = 'RightLegRotation' },
}
PART_NAME_MAP = {
    Torso = 'Torso',
    Head = 'Head',
    ['Left Arm'] = 'Left Arm',
    ['Right Arm'] = 'Right Arm',
    ['Left Leg'] = 'Left Leg',
    ['Right Leg'] = 'Right Leg',
}

function SnapValue(value, step)
    if step == 0 then return value end
    return math.round(value / step) * step
end
function FGM.ApplySnap(section, axis, value)
    if not FGM.Configuration.SnapEnabled then return value end
    isRot = section:find('Rotation')
    step = isRot and FGM.Configuration.SnapRotStep or FGM.Configuration.SnapPosStep
    return SnapValue(value, step)
end
function BuildTargetCFrame(partName, torsoWorldCFrame, config)
    local posKey, rotKey
    if partName == 'Left Arm' then posKey, rotKey = 'LeftArmPosition', 'LeftArmRotation'
    elseif partName == 'Right Arm' then posKey, rotKey = 'RightArmPosition', 'RightArmRotation'
    elseif partName == 'Left Leg' then posKey, rotKey = 'LeftLegPosition', 'LeftLegRotation'
    elseif partName == 'Right Leg' then posKey, rotKey = 'RightLegPosition', 'RightLegRotation'
    elseif partName == 'Head' then posKey, rotKey = 'HeadPosition', 'HeadRotation'
    else return nil end
    p = config[posKey]
    r = config[rotKey]
    if not p or not r then return nil end
    return torsoWorldCFrame * CFrame.new(p.X, p.Y, p.Z) * CFrame.Angles(math.rad(r.X), math.rad(r.Y), math.rad(r.Z))
end
function LerpCFrame(a, b, alpha)
    return a:Lerp(b, alpha)
end
function FGM.GetCharacter(player)
    character = player.Character
    if not character then character = player.CharacterAdded:Wait() end
    return character
end
function InitSmoothedCFrames(targetCharacter)
    FGM.State.SmoothedCFrames = {}
    parts = { 'Torso', 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
    for _, name in ipairs(parts) do
        part = targetCharacter:FindFirstChild(name)
        if part then FGM.State.SmoothedCFrames[name] = part.CFrame end
    end
end
function SetupBodyParts(targetCharacter)
    bodyParts = { 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
    for _, partName in pairs(bodyParts) do
        part = targetCharacter:FindFirstChild(partName)
        if part then
            part.Anchored = false
            part.CanCollide = true
            part.Massless = true
        end
    end
    InitSmoothedCFrames(targetCharacter)
end
function FGM.ClearLimbHighlight()
    state = FGM.State
    if state.LimbHighlight and state.LimbHighlight.Parent then
        state.LimbHighlight:Destroy()
    end
    state.LimbHighlight = nil
    state.HighlightedLimb = nil
end
function FGM.ApplyLimbHighlight(limbName)
    FGM.ClearLimbHighlight()
    state = FGM.State
    target = state.TargetCharacter
    if not target then return end
    partName = PART_NAME_MAP[limbName]
    if not partName then return end
    part = target:FindFirstChild(partName)
    if not part then return end
    highlight = Instance.new('SelectionBox')
    highlight.Adornee = part
    highlight.Color3 = Color3.fromRGB(0, 120, 255)
    highlight.LineThickness = 0.05
    highlight.SurfaceTransparency = 0.6
    highlight.SurfaceColor3 = Color3.fromRGB(0, 100, 255)
    highlight.Parent = FGM.Workspace.CurrentCamera
    state.LimbHighlight = highlight
    state.HighlightedLimb = limbName
end
function FGM.ExecuteGrabTP(targetChar)
    state = FGM.State
    myChar = FGM.GetCharacter(FGM.LocalPlayer)
    if not myChar then return false end
    myHRP = myChar:FindFirstChild('HumanoidRootPart')
    targetHRP = targetChar and targetChar:FindFirstChild('HumanoidRootPart')
    if not myHRP or not targetHRP then return false end
    if targetChar.Parent ~= FGM.Workspace then return false end
    savedPos = myHRP.CFrame
    for _ = 1, 5 do
        _FireDestroyLine(targetHRP)
        FGM.RunService.RenderStepped:Wait()
        _FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
    end
    dist = (targetHRP.Position - myHRP.Position).Magnitude
    if dist >= 5 then
        tp(myHRP, targetHRP)
        task.wait(0.2)
        pcall(function() sno(targetHRP) end)
        task.wait(0.05)
        myHRP.AssemblyLinearVelocity = Vector3.zero
        myHRP.AssemblyAngularVelocity = Vector3.zero
        targetHRP.AssemblyLinearVelocity = Vector3.zero
        targetHRP.AssemblyAngularVelocity = Vector3.zero
        myHRP.CFrame = savedPos
        task.wait(0.2)
    end
    for _, v in pairs(targetChar:GetChildren()) do
        if v:IsA('BasePart') then
            pcall(function()
                v.AssemblyLinearVelocity = Vector3.zero
                v.AssemblyAngularVelocity = Vector3.zero
            end)
        end
    end
    cfg = FGM.Configuration
    holdCF = myHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
    torso = targetChar:FindFirstChild('Torso')
    if torso then
        pcall(function()
            torso.CFrame = holdCF
            torso.AssemblyLinearVelocity = Vector3.zero
            torso.AssemblyAngularVelocity = Vector3.zero
        end)
    end
    state.ActiveNetworkTarget = targetHRP
    return true
end
function FGM.StartPersistentGrab()
    FGM.StopPersistentGrab()
    FGM.State.PersistentGrabActive = true
    FGM.State.PersistentGrabThread = task.spawn(function()
        while FGM.State.PersistentGrabActive do
            task.wait(0.1)
            state = FGM.State
            target = state.TargetCharacter
            if not target or not state.FigureGrabEnabled then continue end
            myChar = FGM.LocalPlayer.Character
            if not myChar then continue end
            myHRP = myChar:FindFirstChild('HumanoidRootPart')
            targetHRP = target:FindFirstChild('HumanoidRootPart')
            if not myHRP or not targetHRP then continue end
            _FireDestroyLine(targetHRP)
            _FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
            dist = (targetHRP.Position - myHRP.Position).Magnitude
            if dist >= FGM.Configuration.AutoTPDistance then
                if not state.DistanceTPInProgress then
                    state.DistanceTPInProgress = true
                    task.spawn(function()
                        if state.TargetPlayer then
                            FGM.BringTargetToMe(state.TargetPlayer)
                        end
                        task.wait(0.3)
                        state.DistanceTPInProgress = false
                    end)
                end
            end
        end
    end)
end
function FGM.StopPersistentGrab()
    FGM.State.PersistentGrabActive = false
    if FGM.State.PersistentGrabThread then
        pcall(function() task.cancel(FGM.State.PersistentGrabThread) end)
        FGM.State.PersistentGrabThread = nil
    end
end
function FGM.WaitForCharacterReady(targetPlayer, timeout)
    timeout = timeout or 15
    deadline = tick() + timeout
    while tick() < deadline do
        char = targetPlayer.Character
        if char and char.Parent and char:FindFirstChild('HumanoidRootPart') and char:FindFirstChild('Torso') and char:FindFirstChild('Humanoid') and char.Humanoid.Health > 0 then
            return char
        end
        task.wait(0.15)
    end
    return nil
end
function FGM.ReattachToCharacter(newCharacter)
    state = FGM.State
    if not state.FigureGrabEnabled then return end
    myChar = FGM.GetCharacter(FGM.LocalPlayer)
    if not myChar then return end
    FGM.ToggleAutoRagdoll(false)
    task.wait(0.1)
    state.TargetCharacter = newCharacter
    state.ActiveNetworkTarget = newCharacter:FindFirstChild('HumanoidRootPart')
    SetupBodyParts(newCharacter)
    if state.TargetPlayer then
        FGM.BringTargetToMe(state.TargetPlayer)
    end
    task.wait(0.1)
    RunHeartbeat(myChar)
    if state.AutoRagdollToggle then
        FGM.ToggleAutoRagdoll(true)
    end
    if state.HighlightedLimb then
        FGM.ApplyLimbHighlight(state.HighlightedLimb)
    end
end
function FGM.WatchForRespawn(targetPlayer)
    if FGM.State.RespawnConnection then
        pcall(function() FGM.State.RespawnConnection:Disconnect() end)
        FGM.State.RespawnConnection = nil
    end
    FGM.State.RespawnConnection = targetPlayer.CharacterAdded:Connect(function()
        if not FGM.State.FigureGrabEnabled then return end
        readyChar = FGM.WaitForCharacterReady(targetPlayer, 15)
        if not readyChar then return end
        FGM.ReattachToCharacter(readyChar)
    end)
end
function FGM.WatchForRejoin(targetPlayer)
    if FGM.State.RejoinConnection then
        pcall(function() FGM.State.RejoinConnection:Disconnect() end)
        FGM.State.RejoinConnection = nil
    end
    targetUserId = targetPlayer.UserId
    local removingConn
    removingConn = FGM.Players.PlayerRemoving:Connect(function(leavingPlayer)
        if leavingPlayer.UserId ~= targetUserId then return end
        if not FGM.State.FigureGrabEnabled then
            pcall(function() removingConn:Disconnect() end)
            FGM.State.RejoinConnection = nil
            return
        end
        task.spawn(function()
            local rejoinConn
            rejoinConn = FGM.Players.PlayerAdded:Connect(function(newPlayer)
                if newPlayer.UserId ~= targetUserId then return end
                pcall(function() rejoinConn:Disconnect() end)
                pcall(function() removingConn:Disconnect() end)
                FGM.State.RejoinConnection = nil
                if not FGM.State.FigureGrabEnabled then return end
                readyChar = FGM.WaitForCharacterReady(newPlayer, 30)
                if not readyChar then return end
                FGM.State.TargetPlayer = newPlayer
                FGM.WatchForRespawn(newPlayer)
                FGM.WatchForRejoin(newPlayer)
                FGM.ReattachToCharacter(readyChar)
            end)
        end)
    end)
    FGM.State.RejoinConnection = removingConn
end
function FGM.CopyAnimationsFromLimbs()
    if not FGM.State.AnimationCopyEnabled then return end
    if not FGM.State.TargetCharacter then return end
    MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
    if not MyCharacter then return end
    MyHRP = MyCharacter:FindFirstChild('HumanoidRootPart')
    MyTorso = MyCharacter:FindFirstChild('Torso')
    TargetTorso = FGM.State.TargetCharacter:FindFirstChild('Torso')
    if not MyHRP or not MyTorso or not TargetTorso then return end
    cfg = FGM.Configuration
    holdCFrame = MyHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
    pcall(function()
        TargetTorso.CFrame = holdCFrame
        torsoRelative = MyHRP.CFrame:ToObjectSpace(MyTorso.CFrame)
        TargetTorso.CFrame = TargetTorso.CFrame * torsoRelative.Rotation
        TargetTorso.Velocity = FGM.State.VectorZero
        TargetTorso.RotVelocity = FGM.State.VectorZero
    end)
    limbs = { 'Head', 'Right Arm', 'Left Arm', 'Right Leg', 'Left Leg' }
    for _, limbName in ipairs(limbs) do
        myPart = MyCharacter:FindFirstChild(limbName)
        targetPart = FGM.State.TargetCharacter:FindFirstChild(limbName)
        if myPart and targetPart then
            pcall(function()
                relative = MyTorso.CFrame:ToObjectSpace(myPart.CFrame)
                targetPart.CFrame = TargetTorso.CFrame:ToWorldSpace(relative)
                targetPart.Velocity = FGM.State.VectorZero
                targetPart.RotVelocity = FGM.State.VectorZero
            end)
        end
    end
end
function FGM.CheckDistanceAndTP(myHRP, targetChar) end
function RunHeartbeat(MyCharacter)
    cfg = FGM.Configuration
    state = FGM.State
    zero = state.VectorZero
    bodyParts = { 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }
    lastTime = tick()
    if state.FigureGrabConnection then
        pcall(function() state.FigureGrabConnection:Disconnect() end)
    end
    state.FigureGrabConnection = FGM.RunService.Heartbeat:Connect(function()
        now = tick()
        dt = math.min(now - lastTime, 0.1)
        lastTime = now
        target = state.TargetCharacter
        if not target or not MyCharacter then return end
        MyRoot = MyCharacter:FindFirstChild('HumanoidRootPart')
        TargetTorso = target:FindFirstChild('Torso')
        if not MyRoot or not TargetTorso then return end
        if state.SpinEnabled then
            state.SpinAngle = (state.SpinAngle + state.SpinSpeed * dt) % 360
        end
        if state.OscillateEnabled then
            state.OscillateTimer = state.OscillateTimer + dt
        end
        holdOffsetZ = cfg.HoldPosition.Z
        if state.OscillateEnabled then
            holdOffsetZ = holdOffsetZ + math.sin(state.OscillateTimer * state.OscillateSpeed * math.pi * 2) * state.OscillateAmount
        end
        baseHoldCFrame = MyRoot.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, holdOffsetZ) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
        if state.HoldAtCameraEnabled then
            cam = FGM.Workspace.CurrentCamera
            baseHoldCFrame = cam.CFrame * CFrame.new(0, 0, -math.abs(cfg.HoldPosition.Z))
        end
        if state.GravityFlipEnabled then
            pos = baseHoldCFrame.Position
            myY = MyRoot.Position.Y
            flippedY = myY - (pos.Y - myY)
            rot = baseHoldCFrame.Rotation
            baseHoldCFrame = CFrame.new(pos.X, flippedY, pos.Z) * rot
        end
        holdCFrame = baseHoldCFrame
        if state.ForceLookAtEnabled then
            lookDir = (MyRoot.Position - baseHoldCFrame.Position)
            if lookDir.Magnitude > 0.01 then
                holdCFrame = CFrame.new(baseHoldCFrame.Position, baseHoldCFrame.Position + lookDir)
            end
        end
        if state.ForceUprightEnabled then
            p = holdCFrame.Position
            holdCFrame = CFrame.new(p) * CFrame.Angles(0, math.rad(cfg.HoldRotation.Y), 0)
        end
        if state.LockRotationEnabled then
            holdCFrame = CFrame.new(holdCFrame.Position) * state.LockedRotation
        end
        if state.SpinEnabled then
            holdCFrame = holdCFrame * CFrame.Angles(0, math.rad(state.SpinAngle), 0)
        end
        if state.FreezeLimbsEnabled and next(state.FrozenCFrames) then
            for partName, frozenCF in pairs(state.FrozenCFrames) do
                part = target:FindFirstChild(partName)
                if part and part.Parent then
                    pcall(function()
                        part.CFrame = frozenCF
                        part.Velocity = zero
                        part.RotVelocity = zero
                    end)
                end
            end
            if cfg.DampingEnabled then
                prev = state.SmoothedCFrames.Torso or holdCFrame
                alpha = math.min(1, cfg.DampingSpeed * dt)
                state.SmoothedCFrames.Torso = LerpCFrame(prev, holdCFrame, alpha)
                pcall(function()
                    TargetTorso.CFrame = state.SmoothedCFrames.Torso
                    TargetTorso.Velocity = zero
                    TargetTorso.RotVelocity = zero
                end)
            else
                pcall(function()
                    TargetTorso.CFrame = holdCFrame
                    TargetTorso.Velocity = zero
                    TargetTorso.RotVelocity = zero
                end)
            end
            _FireSetNetworkOwner(state.ActiveNetworkTarget, holdCFrame)
            return
        end
        if cfg.DampingEnabled then
            prev = state.SmoothedCFrames.Torso or holdCFrame
            alpha = math.min(1, cfg.DampingSpeed * dt)
            state.SmoothedCFrames.Torso = LerpCFrame(prev, holdCFrame, alpha)
            pcall(function()
                TargetTorso.CFrame = state.SmoothedCFrames.Torso
                TargetTorso.Velocity = zero
                TargetTorso.RotVelocity = zero
            end)
        else
            pcall(function()
                TargetTorso.CFrame = holdCFrame
                TargetTorso.Velocity = zero
                TargetTorso.RotVelocity = zero
            end)
        end
        if state.VelSuppressEnabled then
            for _, part in pairs(target:GetChildren()) do
                if part:IsA('BasePart') then
                    pcall(function()
                        part.AssemblyLinearVelocity = zero
                        part.AssemblyAngularVelocity = zero
                    end)
                end
            end
        end
        if state.AnimationCopyEnabled then
            FGM.CopyAnimationsFromLimbs()
        else
            torsoCF = TargetTorso.CFrame
            for _, partName in pairs(bodyParts) do
                part = target:FindFirstChild(partName)
                if part and part.Parent then
                    targetCF = BuildTargetCFrame(partName, torsoCF, cfg)
                    if targetCF then
                        if cfg.DampingEnabled then
                            prev = state.SmoothedCFrames[partName] or targetCF
                            alpha = math.min(1, cfg.DampingSpeed * dt)
                            state.SmoothedCFrames[partName] = LerpCFrame(prev, targetCF, alpha)
                            pcall(function()
                                part.CFrame = state.SmoothedCFrames[partName]
                                part.Velocity = zero
                                part.RotVelocity = zero
                            end)
                        else
                            pcall(function()
                                part.CFrame = targetCF
                                part.Velocity = zero
                                part.RotVelocity = zero
                            end)
                        end
                    end
                end
            end
        end
        _FireSetNetworkOwner(state.ActiveNetworkTarget, holdCFrame)
    end)
end
function FGM.GetPlayerList()
    list = {}
    for _, plr in pairs(FGM.Players:GetPlayers()) do
        if plr ~= FGM.LocalPlayer then
            table.insert(list, plr.Name)
        end
    end
    return list
end
function FGM.GrabPlayerByName(playerName)
    targetPlayer = FGM.Players:FindFirstChild(playerName)
    if not targetPlayer then return end
    targetChar = targetPlayer.Character
    if not targetChar then return end
    if not targetChar:FindFirstChild('Torso') then return end
    MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
    if not MyCharacter then return end
    if FGM.State.FigureGrabEnabled then
        FGM.ToggleFigureGrab()
        task.wait(0.1)
    end
    state = FGM.State
    state.TargetCharacter = targetChar
    state.TargetPlayer = targetPlayer
    state.FigureGrabEnabled = true
    state.LastGrabTargetRef = targetChar:FindFirstChild('HumanoidRootPart')
    state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart')
    FGM.Configuration.LineDistance = 5
    FGM.BringTargetToMe(targetPlayer)
    task.wait(0.15)
    SetupBodyParts(targetChar)
    FGM.StartPersistentGrab()
    FGM.WatchForRespawn(targetPlayer)
    FGM.WatchForRejoin(targetPlayer)
    RunHeartbeat(MyCharacter)
    if state.AutoRagdollToggle then
        FGM.ToggleAutoRagdoll(true)
    end
    if state.HighlightedLimb then
        FGM.ApplyLimbHighlight(state.HighlightedLimb)
    end
end
function FGM.ToggleFigureGrab()
    if not FGM.State.FigureGrabEnabled then
        tn = FGM.State.SelectedTarget
        if not tn or tn == "" then return end
        targetPlayer = FGM.Players:FindFirstChild(tn)
        if not targetPlayer then return end
        targetChar = targetPlayer.Character
        if not targetChar then return end
        if not targetChar:FindFirstChild('Torso') then return end
        MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
        if not MyCharacter then return end
        myHRP = MyCharacter:FindFirstChild('HumanoidRootPart')
        if myHRP then
            FGM.State.SavedPosition = myHRP.CFrame
        end
        bringSuccess = FGM.BringTargetToMe(targetPlayer)
        if not bringSuccess then return end
        task.wait(0.2)
        targetChar = targetPlayer.Character
        if not targetChar then return end
        state = FGM.State
        state.TargetCharacter = targetChar
        state.TargetPlayer = targetPlayer
        state.FigureGrabEnabled = true
        state.LastGrabTargetRef = targetChar:FindFirstChild('HumanoidRootPart')
        state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart')
        FGM.Configuration.LineDistance = 5
        SetupBodyParts(targetChar)
        FGM.StartPersistentGrab()
        if targetPlayer then
            FGM.WatchForRespawn(targetPlayer)
            FGM.WatchForRejoin(targetPlayer)
        end
        RunHeartbeat(MyCharacter)
        if state.HighlightedLimb then
            FGM.ApplyLimbHighlight(state.HighlightedLimb)
        end
        if state.AutoRagdollToggle then
            FGM.ToggleAutoRagdoll(true)
        end
    else
        state = FGM.State
        if state.FlingOnReleaseEnabled and state.TargetCharacter then
            targetHRP = state.TargetCharacter:FindFirstChild('HumanoidRootPart')
            myChar = FGM.GetCharacter(FGM.LocalPlayer)
            myHRP = myChar and myChar:FindFirstChild('HumanoidRootPart')
            if targetHRP and myHRP then
                flingDir = (targetHRP.Position - myHRP.Position)
                if flingDir.Magnitude > 0 then flingDir = flingDir.Unit end
                pcall(function() targetHRP.AssemblyLinearVelocity = flingDir * state.FlingForce end)
            end
        end
        FGM.JumpAndReturn()
        FGM.ClearLimbHighlight()
        FGM.StopPersistentGrab()
        state.FigureGrabEnabled = false
        state.AnimationCopyEnabled = false
        state.SmoothedCFrames = {}
        state.FrozenCFrames = {}
        state.SpinAngle = 0
        state.OscillateTimer = 0
        state.DistanceTPInProgress = false
        state.ActiveNetworkTarget = nil
        state.LastTargetUserId = nil
        state.LastTargetHRP = nil
        FGM.ToggleAutoRagdoll(false)
        if state.FigureGrabConnection then
            pcall(function() state.FigureGrabConnection:Disconnect() end)
            state.FigureGrabConnection = nil
        end
        if state.RespawnConnection then
            pcall(function() state.RespawnConnection:Disconnect() end)
            state.RespawnConnection = nil
        end
        if state.RejoinConnection then
            pcall(function() state.RejoinConnection:Disconnect() end)
            state.RejoinConnection = nil
        end
        state.TargetCharacter = nil
        state.TargetPlayer = nil
        state.LastGrabTargetRef = nil
    end
end
function FGM.SetAnimationCopy(enabled)
    FGM.State.AnimationCopyEnabled = enabled
end
function FGM.ResetPose()
    limbSections = {
        'LeftArmPosition', 'LeftArmRotation', 'RightArmPosition', 'RightArmRotation',
        'LeftLegPosition', 'LeftLegRotation', 'RightLegPosition', 'RightLegRotation',
        'HeadPosition', 'HeadRotation', 'HoldRotation',
    }
    for _, section in ipairs(limbSections) do
        t = FGM.Configuration[section]
        if t then
            for axis in pairs(t) do t[axis] = 0 end
        end
    end
    FGM.Configuration.HoldPosition = { X = 0, Y = 0, Z = -5 }
end
function FGM.ApplyPreset(presetName)
    preset = FGM.Presets[presetName]
    if not preset then return end
    for section, values in pairs(preset) do
        if FGM.Configuration[section] then
            for axis, value in pairs(values) do
                FGM.Configuration[section][axis] = value
            end
        end
    end
end
function FGM.UpdateConfig(section, axis, rawValue)
    cfg = FGM.Configuration
    if cfg[section] and cfg[section][axis] ~= nil then
        cfg[section][axis] = FGM.ApplySnap(section, axis, rawValue)
    end
end
function FGM.SnapshotLimbsForFreeze()
    state = FGM.State
    target = state.TargetCharacter
    state.FrozenCFrames = {}
    if not target then return end
    for _, name in ipairs({ 'Head', 'Left Arm', 'Right Arm', 'Left Leg', 'Right Leg' }) do
        part = target:FindFirstChild(name)
        if part then state.FrozenCFrames[name] = part.CFrame end
    end
end
function FGM.GetCurrentConfigSnapshot()
    cfg = FGM.Configuration
    snapshot = {}
    keys = {
        'HoldPosition', 'HoldRotation', 'LeftArmPosition', 'LeftArmRotation',
        'RightArmPosition', 'RightArmRotation', 'LeftLegPosition', 'LeftLegRotation',
        'RightLegPosition', 'RightLegRotation', 'HeadPosition', 'HeadRotation',
    }
    for _, key in ipairs(keys) do
        if cfg[key] then
            snapshot[key] = { X = cfg[key].X, Y = cfg[key].Y, Z = cfg[key].Z }
        end
    end
    return snapshot
end
function FGM.SaveCustomPreset(name)
    if not name or name == '' then return false end
    FGM.CustomPresets[name] = FGM.GetCurrentConfigSnapshot()
    return true
end
function FGM.LoadCustomPreset(name)
    preset = FGM.CustomPresets[name]
    if not preset then return false end
    for section, values in pairs(preset) do
        if FGM.Configuration[section] then
            for axis, value in pairs(values) do
                FGM.Configuration[section][axis] = value
            end
        end
    end
    return true
end
function FGM.DeleteCustomPreset(name)
    if not FGM.CustomPresets[name] then return false end
    FGM.CustomPresets[name] = nil
    return true
end
function FGM.GetCustomPresetNames()
    names = {}
    for name in pairs(FGM.CustomPresets) do table.insert(names, name) end
    table.sort(names)
    return names
end
function GetActiveSections()
    limb = FGM.State.SelectedLimb
    return LIMB_SECTION_MAP[limb] or LIMB_SECTION_MAP.Torso
end

if not Tabs or not Tabs.FigureTab then
    return
end

FigureTab = Tabs.FigureTab

GrpTarget = FigureTab:AddLeftGroupbox('Target', 'user')
ControlPanel = FigureTab:AddLeftGroupbox('Control', 'user-round-cog')
GrpSettings = FigureTab:AddLeftGroupbox('Settings', 'settings')
GrpPhysics = FigureTab:AddLeftGroupbox('Physics', 'activity')
GrpMotion = FigureTab:AddLeftGroupbox('Movement', 'wind')
GrpLimb = FigureTab:AddRightGroupbox('Limb Control', 'sliders-horizontal')
GrpSnap = FigureTab:AddRightGroupbox('Snap Grid', 'grid')
GrpPoses = FigureTab:AddRightGroupbox('Poses', 'bookmark')
GrpActions = FigureTab:AddRightGroupbox('Actions', 'zap')

game_Players = game:GetService('Players')
game_Debris = game:GetService('Debris')
game_Workspace = game:GetService('Workspace')
game_Lighting = game:GetService('Lighting')
game_TweenService = game:GetService('TweenService')
game_UserInputService = game:GetService('UserInputService')
game_ReplicatedStorage = game:GetService('ReplicatedStorage')
game_ContextActionService = game:GetService('ContextActionService')
game_RunService = game:GetService('RunService')
CurrentPlayer = game_Players.LocalPlayer
GrabEventsFolder = game_ReplicatedStorage:WaitForChild('GrabEvents')
SetOwnershipEvent = GrabEventsFolder:WaitForChild('SetNetworkOwner')
DestroyLineEvent = GrabEventsFolder:WaitForChild('DestroyGrabLine')
CreateLineEvent = GrabEventsFolder:WaitForChild('CreateGrabLine', 3)
_G.ControllingCreature = nil
ConnectionNoclip = nil
NoclipEnabled = false
RaycastParameters = RaycastParams.new()
RaycastParameters.FilterDescendantsInstances = {
    CurrentPlayer.Character,
}
RaycastParameters.FilterType = Enum.RaycastFilterType.Exclude

function CreateLookAtCFrame(startPosition, endPosition)
    DirectionVector = (endPosition - startPosition).Unit
    RightAxis = DirectionVector:Cross(Vector3.new(0, 1, 0))
    UpAxis = RightAxis:Cross(DirectionVector)

    return CFrame.fromMatrix(startPosition, RightAxis, UpAxis)
end
function GetCurrentCharacter()
    if CurrentPlayer.Character and (CurrentPlayer.Character:FindFirstChild('HumanoidRootPart') and CurrentPlayer.Character:FindFirstChildOfClass('Humanoid')) then
        return CurrentPlayer.Character
    end
end
function ValidatePartOwnership(partInstance, shouldReturnOwner)
    if typeof(partInstance) == 'Instance' and (partInstance:FindFirstChild('PartOwner') and partInstance.PartOwner.Value == CurrentPlayer.Name) then
        return not shouldReturnOwner and true or partInstance.PartOwner
    end
end
function RequestOwnershipSingle(partInstance)
    DistanceFromPlayer = CurrentPlayer:DistanceFromCharacter(partInstance.Position)

    if CurrentPlayer.Character and CurrentPlayer.Character:FindFirstChild('HumanoidRootPart') then
        if ValidatePartOwnership(partInstance) then
            return true
        end
        if DistanceFromPlayer <= 30 then
            SetOwnershipEvent:FireServer(partInstance, CreateLookAtCFrame(CurrentPlayer.Character.HumanoidRootPart.Position, partInstance.Position))
        end
    end
end
function RequestOwnershipAndRemoveLine(partInstance)
    DistanceCalculated = CurrentPlayer:DistanceFromCharacter(partInstance.Position)
    IsConnected = partInstance:GetAttribute('Connected')
    HasCreatedConnection = partInstance:GetAttribute('CreatedConnected')

    if CurrentPlayer.Character and CurrentPlayer.Character:FindFirstChild('HumanoidRootPart') then
        if ValidatePartOwnership(partInstance) then
            partInstance:SetAttribute('Connected', true)
            DestroyLineEvent:FireServer(partInstance)

            if not HasCreatedConnection then
                partInstance:SetAttribute('CreatedConnected', true)
                partInstance.ChildAdded:Connect(function(childAdded)
                    if childAdded.Name == 'PartOwner' and childAdded.Value ~= CurrentPlayer.Name then
                        partInstance:SetAttribute('Connected', false)
                    end
                end)
            end
        elseif DistanceCalculated <= 30 and not IsConnected then
            SetOwnershipEvent:FireServer(partInstance, CreateLookAtCFrame(CurrentPlayer.Character.HumanoidRootPart.Position, partInstance.Position))
        end
    end
end
function ActivateNoclipMode()
    if not ConnectionNoclip then
        NoclipEnabled = false
        NoclipLoopFunction = function()
            if NoclipEnabled == false and game_Players.LocalPlayer.Character ~= nil then
                ChildrenIterator, ChildrenTable, CurrentIndex = pairs(game_Players.LocalPlayer.Character:GetChildren())

                while true do
                    CurrentChild = nil
                    CurrentIndex, CurrentChild = ChildrenIterator(ChildrenTable, CurrentIndex)

                    if CurrentIndex == nil then
                        break
                    end
                    if CurrentChild:IsA('BasePart') and CurrentChild.CanCollide then
                        CurrentChild.CanCollide = false
                    end
                end
            end

            wait(0.21)
        end
        ConnectionNoclip = game_RunService.Stepped:Connect(NoclipLoopFunction)
    end
end
function DeactivateNoclipMode()
    if not _G.NoclipToggle then
        if ConnectionNoclip then
            ConnectionNoclip:Disconnect()

            ConnectionNoclip = nil
        end

        NoclipEnabled = true
    end
end
function DisableCharacterQuery(characterModel)
    PartsIterator, PartsTable, PartIndex = pairs(characterModel:GetChildren())

    while true do
        CurrentPart = nil
        PartIndex, CurrentPart = PartsIterator(PartsTable, PartIndex)

        if PartIndex == nil then
            break
        end
        if CurrentPart:IsA('Part') then
            CurrentPart.CanQuery = false
        end
    end
end
function EnableCharacterQuery(characterModel)
    IteratorFunc, TableData, IndexPos = pairs(characterModel:GetChildren())

    while true do
        PartData = nil
        IndexPos, PartData = IteratorFunc(TableData, IndexPos)

        if IndexPos == nil then
            break
        end
        if PartData:IsA('Part') then
            PartData.CanQuery = true
        end
    end
end

ControlAudioEffect = Instance.new('Sound', game_Workspace)
ControlAudioEffect.SoundId = 'rbxassetid://9114374439'
ControlAudioEffect.PlaybackSpeed = 1.25
ColorCorrectionInstance = Instance.new('ColorCorrectionEffect', game_Lighting)
ColorCorrectionInstance.Enabled = false
FieldOfViewTween = game_TweenService:Create(game_Workspace.CurrentCamera, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, 0, true), {FieldOfView = 100})
TintColorTween = game_TweenService:Create(ColorCorrectionInstance, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
    TintColor = Color3.fromRGB(200, 210, 240),
})
BrightnessTween = game_TweenService:Create(ColorCorrectionInstance, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
    Brightness = -0.1,
})
ResetColorTween = game_TweenService:Create(ColorCorrectionInstance, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
    TintColor = Color3.new(3, 3, 3),
    Brightness = 0,
})

function StartControlVisuals()
    ColorCorrectionInstance.Enabled = true
    ColorCorrectionInstance.TintColor = Color3.new()

    FieldOfViewTween:Play()
    TintColorTween:Play()
    ControlAudioEffect:Play()
    TintColorTween.Completed:Once(function()
        BrightnessTween:Play()
    end)
end
function EndControlVisuals()
    ResetColorTween:Play()
    ResetColorTween.Completed:Once(function()
        ColorCorrectionInstance.Enabled = false
    end)
end
function MovePlayerToPosition(targetCFrame, offsetVector)
    PlayerCharacter = GetCurrentCharacter()

    if PlayerCharacter and typeof(targetCFrame) == 'CFrame' then
        RootPartInstance = PlayerCharacter.HumanoidRootPart
        HumanoidInstance = PlayerCharacter:FindFirstChildOfClass('Humanoid')
        RootPartInstance.CFrame = RootPartInstance.CFrame.Rotation + targetCFrame.Position

        if HumanoidInstance.SeatPart == nil or tostring(HumanoidInstance.SeatPart.Parent) ~= 'CreatureBlobman' then
            HumanoidInstance.Sit = false
        end
    end
end
function BeginControllingTarget(targetModelInstance)
    if typeof(targetModelInstance) == 'Instance' and targetModelInstance:IsA('Model') then
        TargetModel = targetModelInstance
        TargetHumanoid = TargetModel:FindFirstChildOfClass('Humanoid')
        TargetRootPart = TargetModel:FindFirstChild('HumanoidRootPart')
        TargetHead = TargetModel:FindFirstChild('Head')
        IsValidTargetType = (function()
            if not game_Players:GetPlayerFromCharacter(targetModelInstance) and (targetModelInstance.Name == 'YouDecoy' or (targetModelInstance.Name == 'CreatureBlobman' or tostring(targetModelInstance.Parent.Name) == 'Robloxians')) then
                return true
            end
        end)()

        if TargetModel and (TargetHumanoid and TargetRootPart) then
            ConnectionsTable = {}
            CleanupAllConnections = function()
                ConnectionIterator, ConnectionData, ConnectionIndex = pairs(ConnectionsTable)

                while true do
                    ConnectionObject = nil
                    ConnectionIndex, ConnectionObject = ConnectionIterator(ConnectionData, ConnectionIndex)

                    if ConnectionIndex == nil then
                        break
                    end
                    if typeof(ConnectionObject) == 'RBXScriptConnection' then
                        ConnectionObject:Disconnect()
                    end
                end

                table.clear(ConnectionsTable)
            end
            _G.ControllingCreature = TargetModel
            TargetHumanoid.WalkSpeed = 0
            TargetHumanoid.JumpPower = 24
            TargetHumanoid.CameraOffset = Vector3.new(0, 0, -0.7)
            ConnectionsTable[1] = TargetHumanoid.Died:Connect(function()
                _G.ControllingCreature = nil
            end)
            VelocityController = Instance.new('BodyVelocity', TargetRootPart)
            PlayerVelocityController = Instance.new('BodyVelocity')
            PlayerVelocityController.MaxForce = Vector3.new(0, math.huge, 0)
            PlayerVelocityController.Velocity = Vector3.new()
            VelocityController.MaxForce = Vector3.new(math.huge, 0, math.huge)

            DisableCharacterQuery(TargetModel)
            task.spawn(function()
                ActivateNoclipMode()

                while TargetModel.Parent and _G.ControllingCreature ~= nil do
                    if IsValidTargetType then
                        RequestOwnershipAndRemoveLine(TargetHead)
                    else
                        RequestOwnershipSingle(TargetHead)
                    end

                    TargetHumanoid.AutoRotate = true

                    task.wait()
                end
            end)

            game_Workspace.CurrentCamera.CameraSubject = TargetHumanoid

            StartControlVisuals()

            PlayerCharacterCurrent = GetCurrentCharacter()
            PlayerRootPartReference = nil
            PlayerHumanoidReference = nil

            if PlayerCharacterCurrent then
                PlayerHumanoidReference = PlayerCharacterCurrent:FindFirstChildOfClass('Humanoid')
                PlayerRootPartReference = PlayerCharacterCurrent:FindFirstChild('HumanoidRootPart')
                PlayerVelocityController.Parent = PlayerRootPartReference
                ConnectionsTable[2] = PlayerHumanoidReference.Died:Connect(function()
                    _G.ControllingCreature = nil
                end)
                ConnectionsTable[3] = game_UserInputService.JumpRequest:Connect(function()
                    TargetHumanoid:ChangeState('Jumping')
                end)
                ConnectionsTable[5] = PlayerHumanoidReference.Changed:Connect(function(propertyName)
                    if propertyName == 'MoveDirection' then
                        VelocityController.Velocity = PlayerHumanoidReference.MoveDirection * 20
                    end
                end)
                ConnectionsTable[6] = workspace.CurrentCamera.Changed:Connect(function(cameraProperty)
                    if cameraProperty == 'CameraSubject' then
                        game_Workspace.CurrentCamera.CameraSubject = TargetHumanoid
                    end
                end)
                CameraDirectionVector = nil
                ConnectionsTable[7] = TargetHead.Changed:Connect(function(headProperty)
                    if headProperty == 'CFrame' then
                        CameraDirectionVector = game_Workspace.CurrentCamera.CFrame.lookVector
                        TargetHumanoid.CameraOffset = -Vector3.new(CameraDirectionVector.X, 5, CameraDirectionVector.Z) * 1.7
                    end
                end)

                TargetHumanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
            else
                PlayerRootPartReference = nil
                PlayerHumanoidReference = nil
            end

            while TargetModel.Parent and (_G.ControllingCreature ~= nil and (PlayerCharacterCurrent and PlayerCharacterCurrent.Parent)) do
                MovePlayerToPosition(CFrame.new(TargetRootPart.Position + Vector3.new(0, -10, 0)))
                task.wait()
            end

            CleanupAllConnections()
            DeactivateNoclipMode()
            MovePlayerToPosition(CFrame.new(TargetRootPart.Position + Vector3.new(5, 15, 5)))
            EnableCharacterQuery(TargetModel)
            VelocityController:Destroy()
            PlayerVelocityController:Destroy()

            game_Workspace.CurrentCamera.CameraSubject = PlayerHumanoidReference
            _G.ControllingCreature = nil
            PlayerRootPartReference.Velocity = Vector3.new()

            EndControlVisuals()
        end
    end
end
function ExecuteControlAction()
    ActiveCharacter = GetCurrentCharacter()

    if ActiveCharacter then
        CharacterHead = ActiveCharacter.Head
        ActiveCamera = game_Workspace.CurrentCamera
        CharacterHumanoid = ActiveCharacter:FindFirstChildOfClass('Humanoid')
        RaycastResult = game_Workspace:Raycast(CharacterHead.Position, ActiveCamera.CFrame.lookVector * 50, RaycastParameters)

        if RaycastResult and (CharacterHumanoid and CharacterHumanoid.Health > 0) then
            HitInstanceParent = RaycastResult.Instance.Parent

            if HitInstanceParent:FindFirstChildOfClass('Humanoid') then
                BeginControllingTarget(HitInstanceParent)
            end
        end
    end
end
function ExecuteControlToggle()
    if _G.ControllingCreature then
        _G.ControllingCreature = nil
    else
        ExecuteControlAction()
    end
end

CurrentPlayer.CharacterAdded:Connect(function(newCharacter)
    RaycastParameters.FilterDescendantsInstances = {newCharacter}
end)

local ControlToggle = ControlPanel:AddCheckbox('ControlCharactersToggle', {
    Text = 'Player + NPC Control',
    Default = false,
    Tooltip = 'Control the NPC/Player you are looking at.',
})
local ControlKeybind = ControlToggle:AddKeyPicker('ControlKeybind', {
    Default = 'V',
    Text = 'Control Key',
    Mode = 'Toggle',
    Callback = function(isActive)
        if not ControlToggle.Value then
            return
        end

        ExecuteControlToggle()
    end,
})

ControlToggle:OnChanged(function(enabled) end)
ControlKeybind:OnChanged(function()
    print('Control keybind updated \u{2192}', ControlKeybind.Value)
end)

FGM = FGM or {}
FGM.State = FGM.State or {}
FGM.Configuration = FGM.Configuration or {}
Toggles = Toggles or {}
Options = Options or {}
Notifications = Notifications or {}

getgenv().FigureGrabModule = getgenv().FigureGrabModule or {}
FGM = getgenv().FigureGrabModule

FGM.Players = game:GetService("Players")
FGM.RunService = game:GetService("RunService")
FGM.ReplicatedStorage = game:GetService("ReplicatedStorage")
FGM.UserInputService = game:GetService("UserInputService")

FGM.LocalPlayer = FGM.Players.LocalPlayer
FGM.Mouse = FGM.LocalPlayer:GetMouse()

FGM.GrabEvents = FGM.ReplicatedStorage:WaitForChild("GrabEvents")
FGM.SetNetworkOwner = FGM.GrabEvents:WaitForChild("SetNetworkOwner")
FGM.DestroyLine = FGM.GrabEvents:WaitForChild("DestroyGrabLine")
FGM.CreateLine = FGM.GrabEvents:WaitForChild("CreateGrabLine")

FGM.MenuToys = FGM.ReplicatedStorage:WaitForChild("MenuToys")
FGM.ToySpawn = FGM.MenuToys:WaitForChild("SpawnToyRemoteFunction")
FGM.DestroyToy = FGM.MenuToys:WaitForChild("DestroyToy")

local function FG_GetPlayerList()
    list = {}
    for _, p in ipairs(FGM.Players:GetPlayers()) do
        if p ~= FGM.LocalPlayer then
            table.insert(list, p.DisplayName .. " (@" .. p.Name .. ")")
        end
    end
    table.sort(list)
    return list
end

local function FG_GetUsernameFromFormatted(fmt)
    return fmt:match("%(@(.+)%)") or fmt
end

FGM.State.TargetDropdown = GrpTarget:AddDropdown("FG_TargetPlayer", {
    Text = "Select Target",
    Values = FG_GetPlayerList(),
    Default = 1,
    Callback = function(Value)
        FGM.State.SelectedTarget = FG_GetUsernameFromFormatted(Value)
    end
})

FGM.Players.PlayerAdded:Connect(function()
    task.wait(0.5)
    if FGM.State.TargetDropdown then
        pcall(function() FGM.State.TargetDropdown:SetValues(FG_GetPlayerList()) end)
    end
end)

FGM.Players.PlayerRemoving:Connect(function()
    task.wait(0.3)
    if FGM.State.TargetDropdown then
        pcall(function() FGM.State.TargetDropdown:SetValues(FG_GetPlayerList()) end)
    end
end)

GrpTarget:AddButton({
    Text = "Refresh Target List",
    Func = function()
        if FGM.State.TargetDropdown then
            pcall(function() FGM.State.TargetDropdown:SetValues(FG_GetPlayerList()) end)
        end
    end
})

GrpTarget:AddDivider()

GrpTarget:AddCheckbox('FG_EnableToggle', {
    Text = 'Enable Figure Grab',
    Default = false,
    Tooltip = 'kinda buggy (soon fixed)',
    Callback = function(Value)
        if Value then
            if not FGM.State.SelectedTarget or FGM.State.SelectedTarget == "" then
                pcall(function() Toggles.FG_EnableToggle:SetValue(false) end)
                return
            end
            FGM.ToggleFigureGrab()
        else
            if FGM.State.FigureGrabEnabled then
                FGM.ToggleFigureGrab()
            end
        end
    end,
})

GrpTarget:AddCheckbox('FG_AutoRagdollToggle', {
    Text = 'Auto Ragdoll Target',
    Default = false,
    Tooltip = 'Automatically ragdolls the grabbed target using pallet hammer',
    Callback = function(Value)
        FGM.State.AutoRagdollToggle = Value
        if FGM.State.FigureGrabEnabled then
            FGM.ToggleAutoRagdoll(Value)
        end
    end,
})

GrpSettings:AddLabel('<b>Behaviour</b>')
GrpSettings:AddCheckbox('FG_AnimCopyToggle', {
    Text = 'Mirror My Animations',
    Default = false,
})
Toggles.FG_AnimCopyToggle:OnChanged(function(v)
    if FGM.SetAnimationCopy then
        FGM.SetAnimationCopy(v)
    end
    notify(v and 'Animation mirroring on' or 'Animation mirroring off', 2)
end)
GrpSettings:AddDivider()
GrpSettings:AddLabel('<b>Damping</b>')
GrpSettings:AddCheckbox('FG_DampingEnabled', {
    Text = 'Smooth Movement',
    Default = true,
})
Toggles.FG_DampingEnabled:OnChanged(function(v)
    FGM.Configuration.DampingEnabled = v
    if not v then
        FGM.State.SmoothedCFrames = {}
    end
end)
GrpSettings:AddSlider('FG_DampingSpeed', {
    Text = 'Damping Speed',
    Default = 12,
    Min = 1,
    Max = 60,
    Rounding = 0,
    Callback = function(v)
        FGM.Configuration.DampingSpeed = v
    end,
})
GrpPhysics:AddLabel('<b>Constraints</b>')
GrpPhysics:AddCheckbox('FG_VelSuppress', {
    Text = 'Zero All Velocities',
    Default = false,
})
Toggles.FG_VelSuppress:OnChanged(function(v)
    FGM.State.VelSuppressEnabled = v
end)
GrpPhysics:AddCheckbox('FG_LockRotation', {
    Text = 'Lock Torso Rotation',
    Default = false,
})
Toggles.FG_LockRotation:OnChanged(function(v)
    FGM.State.LockRotationEnabled = v
end)
GrpPhysics:AddCheckbox('FG_ForceUpright', {
    Text = 'Keep Target Upright',
    Default = false,
})
Toggles.FG_ForceUpright:OnChanged(function(v)
    FGM.State.ForceUprightEnabled = v
end)
GrpPhysics:AddCheckbox('FG_ForceLookAt', {
    Text = 'Face Toward Me',
    Default = false,
})
Toggles.FG_ForceLookAt:OnChanged(function(v)
    FGM.State.ForceLookAtEnabled = v
end)
GrpPhysics:AddDivider()
GrpPhysics:AddLabel('<b>Hold</b>')
GrpPhysics:AddCheckbox('FG_HoldAtCamera', {
    Text = 'Anchor to Camera',
    Default = false,
})
Toggles.FG_HoldAtCamera:OnChanged(function(v)
    FGM.State.HoldAtCameraEnabled = v
end)
GrpMotion:AddLabel('<b>Effects</b>')
GrpMotion:AddCheckbox('FG_Spin', {
    Text = 'Spin Target',
    Default = false,
})
Toggles.FG_Spin:OnChanged(function(v)
    FGM.State.SpinEnabled = v
    FGM.State.SpinAngle = 0
end)
GrpMotion:AddSlider('FG_SpinSpeed', {
    Text = 'Spin Speed',
    Default = 180,
    Min = 10,
    Max = 720,
    Rounding = 0,
    Callback = function(v)
        FGM.State.SpinSpeed = v
    end,
})
GrpMotion:AddDivider()
GrpMotion:AddCheckbox('FG_Oscillate', {
    Text = '<b>[FLOAT]</b> Oscillate',
    Default = false,
})

if Toggles.FG_Oscillate then
    Toggles.FG_Oscillate:OnChanged(function(v)
        FGM.State.OscillateEnabled = v
        FGM.State.OscillateTimer = 0
    end)
end

GrpMotion:AddSlider('FG_OscillateSpeed', {
    Text = 'Float Speed',
    Default = 2,
    Min = 1,
    Max = 10,
    Rounding = 1,
    Callback = function(v)
        FGM.State.OscillateSpeed = v
    end,
})
GrpMotion:AddSlider('FG_OscillateAmount', {
    Text = 'Float Distance',
    Default = 3,
    Min = 1,
    Max = 20,
    Rounding = 1,
    Callback = function(v)
        FGM.State.OscillateAmount = v
    end,
})
GrpLimb:AddLabel('<b>Active Limb</b>')
GrpLimb:AddDropdown('FG_LimbSelector', {
    Values = LIMB_OPTIONS or { 'Head' },
    Default = 'Head',
    Multi = false,
    Text = 'Select A Limb',
    Callback = function(selected)
        FGM.State.SelectedLimb = selected
        if FGM.ApplyLimbHighlight then
            FGM.ApplyLimbHighlight(selected)
        end
    end,
})
GrpLimb:AddDivider()
GrpLimb:AddLabel('<b>Position</b>')

function updateLimb(section, axis, value)
    if FGM.UpdateConfig then
        FGM.UpdateConfig(section, axis, value)
    end
end

GrpLimb:AddSlider('FG_LimbPosX', {
    Text = 'Left  /  Right',
    Default = 0,
    Min = -50,
    Max = 50,
    Rounding = 1,
    Callback = function(v)
        updateLimb(GetActiveSections().pos, 'X', v)
    end,
})
GrpLimb:AddSlider('FG_LimbPosY', {
    Text = 'Up  /  Down',
    Default = 0,
    Min = -50,
    Max = 50,
    Rounding = 1,
    Callback = function(v)
        updateLimb(GetActiveSections().pos, 'Y', v)
    end,
})
GrpLimb:AddSlider('FG_LimbPosZ', {
    Text = 'Forward  /  Back',
    Default = -5,
    Min = -50,
    Max = 50,
    Rounding = 1,
    Callback = function(v)
        updateLimb(GetActiveSections().pos, 'Z', v)
    end,
})
GrpLimb:AddDivider()
GrpLimb:AddLabel('<b>Rotation</b>')
GrpLimb:AddSlider('FG_LimbRotX', {
    Text = 'Pitch  (Up / Down)',
    Default = 0,
    Min = 0,
    Max = 360,
    Rounding = 0,
    Callback = function(v)
        updateLimb(GetActiveSections().rot, 'X', v)
    end,
})
GrpLimb:AddSlider('FG_LimbRotY', {
    Text = 'Yaw  (Left / Right)',
    Default = 0,
    Min = 0,
    Max = 360,
    Rounding = 0,
    Callback = function(v)
        updateLimb(GetActiveSections().rot, 'Y', v)
    end,
})
GrpLimb:AddSlider('FG_LimbRotZ', {
    Text = 'Roll  (Tilt)',
    Default = 0,
    Min = 0,
    Max = 360,
    Rounding = 0,
    Callback = function(v)
        updateLimb(GetActiveSections().rot, 'Z', v)
    end,
})
GrpSnap:AddLabel('<b>Snap Grid</b>')
GrpSnap:AddCheckbox('FG_SnapEnabled', {
    Text = 'Enable Snap',
    Default = false,
})
Toggles.FG_SnapEnabled:OnChanged(function(v)
    FGM.Configuration.SnapEnabled = v
end)
GrpSnap:AddSlider('FG_SnapPosStep', {
    Text = 'Position Step',
    Default = 0.5,
    Min = 0.1,
    Max = 5,
    Rounding = 1,
    Callback = function(v)
        FGM.Configuration.SnapPosStep = v
    end,
})
GrpSnap:AddSlider('FG_SnapRotStep', {
    Text = 'Rotation Step',
    Default = 15,
    Min = 1,
    Max = 90,
    Rounding = 0,
    Callback = function(v)
        FGM.Configuration.SnapRotStep = v
    end,
})
GrpPoses:AddLabel('<b>Built-in Poses</b>')
GrpPoses:AddDropdown('FGM_PresetPose', {
    Values = {
        'Pose1', 'Pose2', 'Pose3', 'Pose4', 'Pose5', 'Pose6', 'Pose7', 'JojoStand',
    },
    Default = 'Pose1',
    Multi = false,
    Text = 'Select Pose',
})
GrpPoses:AddButton({
    Text = 'Apply Pose',
    Func = function()
        if FGM.ApplyPreset then
            selected = Options.FGM_PresetPose and Options.FGM_PresetPose.Value
            FGM.ApplyPreset(selected)
            notify('Pose applied — ' .. tostring(selected), 2)
        end
    end,
})
GrpPoses:AddButton({
    Text = 'Reset to Default Pose',
    Func = function()
        if FGM.ResetPose then
            FGM.ResetPose()
            notify('Pose reset to default', 2)
        end
    end,
})
GrpActions:AddLabel('<b>Actions</b>')
GrpActions:AddButton({
    Text = 'Force Re-Grab',
    Func = function()
        target = FGM.State.TargetCharacter
        if not target then return end
        if FGM.ExecuteGrabTP then
            FGM.ExecuteGrabTP(target)
        end
    end,
})
GrpActions:AddButton({
    Text = 'Freeze Limbs',
    Func = function()
        if FGM.SnapshotLimbsForFreeze then
            FGM.SnapshotLimbsForFreeze()
        end
        FGM.State.FreezeLimbsEnabled = true
    end,
})
GrpActions:AddButton({
    Text = 'Unfreeze Limbs',
    Func = function()
        FGM.State.FreezeLimbsEnabled = false
        FGM.State.FrozenCFrames = {}
        notify('Limbs unfrozen', 2)
    end,
})
GrpActions:AddDivider()
GrpActions:AddButton({
    Text = 'Release Target',
    Func = function()
        if FGM.State.FigureGrabEnabled then
            FGM.ToggleFigureGrab()
        else
            notify('Nothing is currently grabbed', 2)
        end
    end,
})

_expTarget = nil
_expDropUpdate = false
AutoExplosionEnabled = false
ExplosionType = 'BombMissile'
ExplosionInterval = 0
PredictMovement = false
ExplosionAmount = 3
SpawnSpeed = 0
SetupSpeed = 0
ExplosionColorEnabled = false
ExplosionColor = Color3.fromRGB(255, 0, 0)
RainbowExplosionEnabled = false
ExplosionBrightness = 10
origexplosionpresets = {}
origbrightnessvals = {}
origsizevals = {}
origspeedvals = {}
origlifetimevals = {}
origdensityvals = {}
ParticleSize = 1
ParticleSpeed = 1
ParticleLifetime = 1
ParticleDensity = 1
ParticleTransparency = 0
TransparentExplosionEnabled = false
PulseColorEnabled = false
PulseColorA = Color3.fromRGB(255, 80, 0)
PulseColorB = Color3.fromRGB(0, 120, 255)
PulseSpeed = 5
StrobeEnabled = false
StrobeIntensity = 5
InvertColorEnabled = false
BlendMode = 'Default'
ExplosionConfigBox = Tabs.ToyTab:AddRightGroupbox('Settings', 'settings')
AutoExplosionBox = Tabs.ToyTab:AddRightGroupbox('Auto Explode', 'bomb')
ExplosionVisualBox = Tabs.ToyTab:AddRightGroupbox('Visuals', 'eye')
ExplosionFXBox = Tabs.ToyTab:AddRightGroupbox('Effects', 'sparkles')
SpawnToyRF = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
DeleteToyRE = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('DestroyToy')
BuyToy = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('BuyToyRemoteFunction')
BombEvents = ReplicatedStorage:WaitForChild('BombEvents')
SetNetworkOwner = ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')
HitboxNames = {
    BombMissile = 'PartHitDetector',
    BombDarkMatter = 'PartHitDetector',
    FireworkMissile = 'PartHitDetector',
    BombBalloon = 'Balloon',
    PresentBig = 'Box',
    PresentSmall = 'Box',
}
SetupParts = {
    BombMissile = 'Body',
    BombDarkMatter = 'Pyramid',
    FireworkMissile = 'Hitbox',
    BombBalloon = 'Balloon',
    PresentBig = 'Box',
    PresentSmall = 'Box',
}

function InitializeExplosionPresets()
    if ReplicatedStorage:FindFirstChild('ExplosionMaker') and ReplicatedStorage.ExplosionMaker:FindFirstChild('ParticlePresets') then
        for _, particle in ipairs(ReplicatedStorage.ExplosionMaker.ParticlePresets:GetChildren())do
            if particle:IsA('ParticleEmitter') then
                origexplosionpresets[particle] = particle.Color
                origbrightnessvals[particle] = particle.Brightness
                origsizevals[particle] = particle.Size
                origspeedvals[particle] = particle.Speed
                origlifetimevals[particle] = particle.Lifetime
                origdensityvals[particle] = particle.Rate
            end
        end
    end
end
function GetParticles()
    if not ReplicatedStorage:FindFirstChild('ExplosionMaker') or not ReplicatedStorage.ExplosionMaker:FindFirstChild('ParticlePresets') then
        return {}
    end

    out = {}

    for _, p in ipairs(ReplicatedStorage.ExplosionMaker.ParticlePresets:GetChildren())do
        if p:IsA('ParticleEmitter') then
            table.insert(out, p)
        end
    end

    return out
end
function InvertColor(c)
    return Color3.new(1 - c.R, 1 - c.G, 1 - c.B)
end
function ApplyExplosionColor()
    for _, particle in ipairs(GetParticles())do
        local col

        if RainbowExplosionEnabled then
            col = ColorSequence.new({
                ColorSequenceKeypoint.new(0, Color3.new(1, 0, 0)),
                ColorSequenceKeypoint.new(0.25, Color3.new(0, 1, 0)),
                ColorSequenceKeypoint.new(0.5, Color3.new(0, 0, 1)),
                ColorSequenceKeypoint.new(0.75, Color3.new(1, 1, 0)),
                ColorSequenceKeypoint.new(1, Color3.new(1, 0, 0)),
            })
        elseif ExplosionColorEnabled then
            c = InvertColorEnabled and InvertColor(ExplosionColor) or ExplosionColor

            col = ColorSequence.new(c)
        else
            col = origexplosionpresets[particle]
        end

        particle.Color = col
    end
end
function ApplyExplosionBrightness()
    for _, particle in ipairs(GetParticles())do
        if ExplosionBrightness == 10 then
            particle.Brightness = origbrightnessvals[particle]
        else
            particle.Brightness = origbrightnessvals[particle] * 2 * (ExplosionBrightness - 9)
        end
    end
end
function ApplyParticleSize()
    for _, particle in ipairs(GetParticles())do
        orig = origsizevals[particle]

        if orig then
            kps = orig.Keypoints
            newkps = {}

            for _, kp in ipairs(kps)do
                table.insert(newkps, NumberSequenceKeypoint.new(kp.Time, kp.Value * ParticleSize, kp.Envelope))
            end

            particle.Size = NumberSequence.new(newkps)
        end
    end
end
function ApplyParticleSpeed()
    for _, particle in ipairs(GetParticles())do
        orig = origspeedvals[particle]

        if orig then
            kps = orig.Keypoints
            newkps = {}

            for _, kp in ipairs(kps)do
                table.insert(newkps, NumberSequenceKeypoint.new(kp.Time, kp.Value * ParticleSpeed, kp.Envelope))
            end

            particle.Speed = NumberRange.new(orig.Min * ParticleSpeed, orig.Max * ParticleSpeed)
        end
    end
end
function ApplyParticleLifetime()
    for _, particle in ipairs(GetParticles())do
        orig = origlifetimevals[particle]

        if orig then
            particle.Lifetime = NumberRange.new(orig.Min * ParticleLifetime, orig.Max * ParticleLifetime)
        end
    end
end
function ApplyParticleDensity()
    for _, particle in ipairs(GetParticles())do
        orig = origdensityvals[particle]

        if orig then
            particle.Rate = orig * ParticleDensity
        end
    end
end
function ApplyParticleTransparency()
    for _, particle in ipairs(GetParticles())do
        if TransparentExplosionEnabled then
            particle.Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, ParticleTransparency),
                NumberSequenceKeypoint.new(1, 1),
            })
        else
            particle.Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 0),
                NumberSequenceKeypoint.new(1, 1),
            })
        end
    end
end
function ApplyBlendMode()
    for _, particle in ipairs(GetParticles())do
        pcall(function()
            particle.LightEmission = (BlendMode == 'Additive') and 1 or 0
        end)
    end
end
function PulseColorLoop()
    t = 0

    while PulseColorEnabled do
        t = t + task.wait(0.05) * PulseSpeed

        alpha = (math.sin(t) + 1) / 2
        blended = Color3.new(PulseColorA.R + (PulseColorB.R - PulseColorA.R) * alpha, PulseColorA.G + (PulseColorB.G - PulseColorA.G) * alpha, PulseColorA.B + (PulseColorB.B - PulseColorA.B) * alpha)

        for _, particle in ipairs(GetParticles())do
            particle.Color = ColorSequence.new(blended)
        end
    end
end
function StrobeBrightnessLoop()
    while StrobeEnabled do
        for _, particle in ipairs(GetParticles())do
            base = origbrightnessvals[particle] or 1

            particle.Brightness = base * StrobeIntensity * (math.random() > 0.5 and 1 or 0.1)
        end

        task.wait(0.05)
    end

    ApplyExplosionBrightness()
end
function GetSpawnedToys()
    return Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
end
function ExpGetPlayerList()
    list = {}

    for _, p in pairs(Players:GetPlayers())do
        if p ~= LocalPlayer then
            table.insert(list, p.DisplayName .. ' (@' .. p.Name .. ')')
        end
    end

    table.sort(list, function(a, b)
        return a:lower() < b:lower()
    end)

    return list
end
function ExpExtractUsername(entry)
    return entry and entry:match('@([%w_]+)')
end
function ExpGetPlayerByName(username)
    if not username or username == '' then
        return nil
    end

    return Players:FindFirstChild(username)
end
function ExpGetTargetHRP()
    if not _expTarget then
        return nil, nil
    end

    p = ExpGetPlayerByName(_expTarget)

    if p and p.Character and p.Character:FindFirstChild('HumanoidRootPart') then
        return p.Character.HumanoidRootPart, p
    end

    return nil, nil
end
function ExpRefreshDropdown()
    _expDropUpdate = true

    pcall(function()
        ExplosionPlayerDropdown:SetValues(ExpGetPlayerList())
    end)

    _expDropUpdate = false
end
function GetPlayerCharacterLocal()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
        return LocalPlayer.Character
    end

    return nil
end
function LookAt(from, to)
    dir = (to - from).Unit
    right = dir:Cross(Vector3.new(0, 1, 0))
    up = right:Cross(dir)

    return CFrame.fromMatrix(from, right, up)
end
function SetNetworkOwnership(part)
    if not part then
        return
    end
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
        pcall(function()
            SetNetworkOwner:FireServer(part, LookAt(LocalPlayer.Character.HumanoidRootPart.Position, part.Position))
        end)
    end
end
function SpawNoneBomb()
    char = GetPlayerCharacterLocal()

    if char then
        pos = char.HumanoidRootPart.Position

        pcall(function()
            SpawnToyRF:InvokeServer(ExplosionType, CFrame.new(pos + Vector3.new(0, 5, 0)), Vector3.new(0, 0, 0))
            BuyToy:InvokeServer(ExplosionType)
        end)
    end
end
function GetAllBombs()
    toys = GetSpawnedToys()

    if not toys then
        return {}
    end

    bombs = {}

    for _, toy in pairs(toys:GetChildren())do
        if toy.Name == ExplosionType then
            table.insert(bombs, toy)
        end
    end

    return bombs
end
function SetupBomb(bomb)
    if not bomb or not bomb.PrimaryPart then
        return
    end

    hitPart = bomb:FindFirstChild(SetupParts[bomb.Name])

    if not hitPart then
        return
    end

    SetNetworkOwnership(hitPart)
    task.wait(0.05)
    pcall(function()
        for _, v in pairs(bomb.PrimaryPart:GetChildren())do
            if v:IsA('BodyVelocity') or v.Name == 'Stable' then
                v:Destroy()
            end
        end

        bodyVel = Instance.new('BodyVelocity')

        bodyVel.Velocity = Vector3.new(0, 0, 0)
        bodyVel.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVel.Name = 'Stable'
        bodyVel.Parent = bomb.PrimaryPart

        bomb:PivotTo(CFrame.new(math.random(-500, 500), 10000, math.random(-500, 500)))
    end)
end
function ExplodeBomb(bomb, targetHRP)
    if not bomb or not targetHRP then
        return
    end

    hitbox = bomb:FindFirstChild(HitboxNames[bomb.Name])

    if hitbox then
        targetPos = targetHRP.Position

        if PredictMovement then
            targetPos = targetPos + targetHRP.Velocity / 1.93
        end

        pcall(function()
            BombEvents.BombExplode:FireServer({
                Hitbox = hitbox,
                PositionPart = targetHRP,
            }, targetPos)
        end)
    end
end
function DeleteAllBombs()
    for _, bomb in pairs(GetAllBombs())do
        pcall(function()
            DeleteToyRE:FireServer(bomb)
        end)
    end
end
function AutoExplosionLoop()
    while AutoExplosionEnabled do
        targetHRP, targetPlayer = ExpGetTargetHRP()

        if not targetPlayer then
            AutoExplosionEnabled = false

            Toggles.AutoExplosionToggle:SetValue(false)

            break
        end
        if not targetHRP then
            task.wait(0.2)

            continue
        end

        DeleteAllBombs()
        task.wait(0.1)

        while#GetAllBombs() < ExplosionAmount and AutoExplosionEnabled do
            task.spawn(SpawNoneBomb)
            task.wait(SpawnSpeed)
            task.wait(0.02)
        end

        task.wait(0.15)

        for _, bomb in pairs(GetAllBombs())do
            if not AutoExplosionEnabled then
                break
            end

            task.spawn(function()
                SetupBomb(bomb)
            end)
            task.wait(SetupSpeed)
        end

        task.wait(0.2)

        targetHRP, targetPlayer = ExpGetTargetHRP()

        if targetHRP and targetPlayer then
            for _, bomb in pairs(GetAllBombs())do
                if not AutoExplosionEnabled then
                    break
                end

                task.spawn(function()
                    ExplodeBomb(bomb, targetHRP)
                end)
                task.wait(0.01)
            end
        end

        task.wait(0.15)
        DeleteAllBombs()
        task.wait(ExplosionInterval)
    end
end

InitializeExplosionPresets()

ExplosionPlayerDropdown = ExplosionConfigBox:AddDropdown('ExplosionPlayerDropdown', {
    Values = ExpGetPlayerList(),
    Default = nil,
    Text = 'Select Target',
    Multi = false,
    Searchable = true,
    Tooltip = 'Select the player you want to bomb',
})

ExplosionPlayerDropdown:OnChanged(function(v)
    if _expDropUpdate then
        return
    end

    un = ExpExtractUsername(v)

    _expTarget = un or nil
end)
ExplosionConfigBox:AddButton({
    Text = 'Refresh Player List',
    Tooltip = 'Refreshes the target list',
    Func = function()
        ExpRefreshDropdown()
    end,
})
ExplosionConfigBox:AddDropdown('ExplosionTypeSelect', {
    Values = {
        'Missile',
        'Firework',
        'Void',
        'Balloon',
        'Small Present',
        'Big Present',
    },
    Default = 'Missile',
    Tooltip = 'Choose what Explosion Type you want',
    Text = 'Explosion Type',
    Callback = function(Value)
        typeMap = {
            Missile = 'BombMissile',
            Firework = 'FireworkMissile',
            Void = 'BombDarkMatter',
            Balloon = 'BombBalloon',
            ['Small Present'] = 'PresentSmall',
            ['Big Present'] = 'PresentBig',
        }

        ExplosionType = typeMap[Value] or 'BombMissile'
    end,
})
ExplosionConfigBox:AddSlider('BombAmount', {
    Text = 'Bomb Amount',
    Tooltip = 'How many bombs explode per cycle',
    Default = 3,
    Min = 1,
    Max = 10,
    Rounding = 0,
    Suffix = ' bombs',
    Callback = function(Value)
        ExplosionAmount = Value
    end,
})
ExplosionConfigBox:AddCheckbox('PredictMovement', {
    Text = 'Predict Movement',
    Default = false,
    Tooltip = 'Leads the target based on their current velocity',
    Callback = function(Value)
        PredictMovement = Value
    end,
})

ExplosionColorToggle = ExplosionVisualBox:AddCheckbox('ExplosionColorToggle', {
    Text = 'Custom Explosion Color',
    Default = false,
    Tooltip = 'Change the explosion particle color',
    Callback = function(Value)
        ExplosionColorEnabled = Value
        RainbowExplosionEnabled = false

        ApplyExplosionColor()
    end,
})

ExplosionColorToggle:AddColorPicker('ExplosionColorPicker', {
    Title = 'Explosion Color',
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(Value)
        ExplosionColor = Value

        if ExplosionColorEnabled then
            ApplyExplosionColor()
        end
    end,
})
ExplosionVisualBox:AddCheckbox('RainbowExplosion', {
    Text = 'Rainbow Explosions',
    Default = false,
    Tooltip = 'Cycle through all hues on every explosion',
    Callback = function(Value)
        RainbowExplosionEnabled = Value

        if Value then
            ExplosionColorEnabled = false
        end

        ApplyExplosionColor()
    end,
})
ExplosionVisualBox:AddCheckbox('InvertColorToggle', {
    Text = 'Invert Explosion Color',
    Default = false,
    Tooltip = 'Inverts the currently applied explosion color',
    Callback = function(Value)
        InvertColorEnabled = Value

        ApplyExplosionColor()
    end,
})
ExplosionVisualBox:AddSlider('BrightnessSlider', {
    Text = 'Explosion Brightness',
    Default = 10,
    Tooltip = 'Multiplies particle brightness',
    Min = 10,
    Max = 50,
    Rounding = 0,
    Callback = function(Value)
        ExplosionBrightness = Value

        ApplyExplosionBrightness()
    end,
})
ExplosionVisualBox:AddSlider('ParticleSizeSlider', {
    Text = 'Particle Size',
    Default = 1,
    Tooltip = 'Scales the size of explosion particles',
    Min = 1,
    Max = 10,
    Rounding = 1,
    Suffix = 'x',
    Callback = function(Value)
        ParticleSize = Value

        ApplyParticleSize()
    end,
})
ExplosionVisualBox:AddSlider('ParticleSpeedSlider', {
    Text = 'Particle Speed',
    Default = 1,
    Tooltip = 'Scales how fast explosion particles travel outward',
    Min = 1,
    Max = 10,
    Rounding = 1,
    Suffix = 'x',
    Callback = function(Value)
        ParticleSpeed = Value

        ApplyParticleSpeed()
    end,
})
ExplosionVisualBox:AddSlider('ParticleLifetimeSlider', {
    Text = 'Particle Lifetime',
    Default = 1,
    Tooltip = 'Multiplies how long each explosion particle lives',
    Min = 1,
    Max = 10,
    Rounding = 1,
    Suffix = 'x',
    Callback = function(Value)
        ParticleLifetime = Value

        ApplyParticleLifetime()
    end,
})
ExplosionVisualBox:AddSlider('ParticleDensitySlider', {
    Text = 'Particle Density',
    Default = 1,
    Tooltip = 'Multiplies how many particles emit per burst',
    Min = 1,
    Max = 10,
    Rounding = 1,
    Suffix = 'x',
    Callback = function(Value)
        ParticleDensity = Value

        ApplyParticleDensity()
    end,
})
ExplosionVisualBox:AddCheckbox('TransparentExplosionToggle', {
    Text = 'Custom Transparency',
    Default = false,
    Tooltip = 'Enables custom particle transparency',
    Callback = function(Value)
        TransparentExplosionEnabled = Value

        ApplyParticleTransparency()
    end,
})
ExplosionVisualBox:AddSlider('ParticleTransparencySlider', {
    Text = 'Particle Transparency',
    Default = 0,
    Tooltip = 'Transparency at particle birth (0 = solid, 1 = invisible)',
    Min = 0,
    Max = 10,
    Rounding = 1,
    Suffix = '',
    Callback = function(Value)
        ParticleTransparency = Value / 10

        if TransparentExplosionEnabled then
            ApplyParticleTransparency()
        end
    end,
})
ExplosionVisualBox:AddDropdown('BlendModeSelect', {
    Values = {
        'Default',
        'Additive',
    },
    Default = 'Default',
    Text = 'Blend Mode',
    Tooltip = 'Default = normal rendering; Additive = glowing/light-emission look',
    Callback = function(Value)
        BlendMode = Value

        ApplyBlendMode()
    end,
})
ExplosionFXBox:AddCheckbox('PulseColorToggle', {
    Text = 'Pulse Color',
    Default = false,
    Tooltip = 'Smoothly pulses particle color between two chosen colors',
    Callback = function(Value)
        PulseColorEnabled = Value

        if Value then
            RainbowExplosionEnabled = false
            ExplosionColorEnabled = false

            task.spawn(PulseColorLoop)
        else
            ApplyExplosionColor()
        end
    end,
})

PulseColorACheckbox = ExplosionFXBox:AddCheckbox('PulseColorACheckbox', {
    Text = 'Pulse Color A',
    Default = false,
    Tooltip = 'Pick the first pulse color',
})

PulseColorACheckbox:AddColorPicker('PulseColorA', {
    Title = 'Pulse Color A',
    Default = Color3.fromRGB(255, 80, 0),
    Callback = function(Value)
        PulseColorA = Value
    end,
})

PulseColorBCheckbox = ExplosionFXBox:AddCheckbox('PulseColorBCheckbox', {
    Text = 'Pulse Color B',
    Default = false,
    Tooltip = 'Pick the second pulse color',
})

PulseColorBCheckbox:AddColorPicker('PulseColorB', {
    Title = 'Pulse Color B',
    Default = Color3.fromRGB(0, 120, 255),
    Callback = function(Value)
        PulseColorB = Value
    end,
})
ExplosionFXBox:AddSlider('PulseSpeedSlider', {
    Text = 'Pulse Speed',
    Default = 5,
    Tooltip = 'How fast the color oscillates between the two pulse colors',
    Min = 1,
    Max = 20,
    Rounding = 0,
    Suffix = ' spd',
    Callback = function(Value)
        PulseSpeed = Value
    end,
})
ExplosionFXBox:AddCheckbox('StrobeExplosionToggle', {
    Text = 'Strobe Brightness',
    Default = false,
    Tooltip = 'Rapidly flickers particle brightness for a strobe effect',
    Callback = function(Value)
        StrobeEnabled = Value

        if Value then
            task.spawn(StrobeBrightnessLoop)
        else
            ApplyExplosionBrightness()
        end
    end,
})
ExplosionFXBox:AddSlider('StrobeIntensitySlider', {
    Text = 'Strobe Intensity',
    Default = 5,
    Tooltip = 'How extreme the brightness flicker is',
    Min = 1,
    Max = 20,
    Rounding = 0,
    Suffix = 'x',
    Callback = function(Value)
        StrobeIntensity = Value
    end,
})
AutoExplosionBox:AddCheckbox('AutoExplosionToggle', {
    Text = 'Loop Explode',
    Default = false,
    Tooltip = 'Continuously explodes the selected target',
    Callback = function(Value)
        AutoExplosionEnabled = Value

        if Value then
            if not _expTarget then
                AutoExplosionEnabled = false

                Toggles.AutoExplosionToggle:SetValue(false)
                notify('Explosion', 'Select a target first', 3)

                return
            end
            if not ExpGetPlayerByName(_expTarget) then
                AutoExplosionEnabled = false

                Toggles.AutoExplosionToggle:SetValue(false)
                notify('Explosion', 'Target not found in server', 3)

                return
            end

            task.spawn(AutoExplosionLoop)
        else
            task.wait(0.3)
            DeleteAllBombs()
        end
    end,
})
AutoExplosionBox:AddSlider('ExplosionInterval', {
    Text = 'Explosion Interval',
    Default = 0,
    Tooltip = 'Delay between each explosion cycle',
    Min = 0,
    Max = 5,
    Rounding = 1,
    Suffix = ' sec',
    Callback = function(Value)
        ExplosionInterval = Value
    end,
})
Players.PlayerAdded:Connect(function()
    task.wait(0.5)
    ExpRefreshDropdown()
end)
Players.PlayerRemoving:Connect(function(plr)
    task.wait(0.5)

    if plr.Name == _expTarget then
        AutoExplosionEnabled = false
        _expTarget = nil

        pcall(function()
            Toggles.AutoExplosionToggle:SetValue(false)
        end)
    end

    ExpRefreshDropdown()
end)

activeSparklers = {}
sparklerConfig = {
    Height = 5,
    Speed = 2,
    Radius = 15,
    CurrentShape = 'Planet',
}
shapeOptions = {
    'Planet',
    'Sphere',
    'Cylinder',
    'Double Ring',
    'Star',
    'Infinity',
    'Heart',
    'DNA Helix',
    'Triple Helix',
    'Tornado',
    'Galaxy Spiral',
    'Fibonacci Spiral',
    'Spring Coil',
    'Vortex Funnel',
    'Box',
    'Rounded Cube',
    'Torus',
    'Torus Knot',
    'M\u{f6}bius Strip',
    'Saturn',
    'Ice Cube',
}

function SetupPhysics(obj, list)
    for _, v in ipairs(list)do
        if v == obj then
            return
        end
    end

    mainPart = obj:IsA('BasePart') and obj or obj.PrimaryPart or obj:FindFirstChildWhichIsA('BasePart', true)

    if not mainPart then
        return
    end

    pcall(function()
        if mainPart:CanSetNetworkOwnership() then
            mainPart:SetNetworkOwner(LocalPlayer)
        end
    end)

    mainPart.Anchored = false

    bp = mainPart:FindFirstChild('ToyBodyPos') or Instance.new('BodyPosition', mainPart)

    bp.Name = 'ToyBodyPos'
    bp.MaxForce = Vector3.new(1e8, 1e8, 1e8)
    bp.P = 100000
    bp.D = 800

    bg = mainPart:FindFirstChild('ToyBodyGyro') or Instance.new('BodyGyro', mainPart)

    bg.Name = 'ToyBodyGyro'
    bg.MaxTorque = Vector3.new(1e8, 1e8, 1e8)
    bg.P = 50000

    for _, p in ipairs(obj:GetDescendants())do
        if p:IsA('BasePart') then
            p.CanCollide = false
        end
    end

    table.insert(list, obj)
end

TAU = math.pi * 2
sqrt = math.sqrt
sin = math.sin
cos = math.cos
abs = math.abs
sign = math.sign
pow = function(b, e)
    return b >= 0 and b ^ e or -((-b) ^ e)
end
clamp = math.clamp

function baseAngle(i, n, t, speed)
    return (i / n) * TAU + t * speed
end

Shapes = {}
Shapes.Planet = function(i, n, t, r, h, sp)
    coreN = clamp(math.floor(n * 0.35), 8, 15)

    coreN = math.min(coreN, n)

    ring1N = math.max(math.floor((n - coreN) / 2), 1)
    spin = t * sp

    if i <= coreN then
        phi = math.acos(1 - 2 * (i / coreN))
        theta = i * math.pi * (3 - sqrt(5)) + spin
        cr = r * 0.35

        return Vector3.new(cos(theta) * sin(phi) * cr, cos(phi) * cr + h, sin(theta) * sin(phi) * cr)
    elseif i <= coreN + ring1N then
        idx = i - coreN
        a = (idx / ring1N) * TAU + spin
        rr = r * 0.9
        tilt = math.rad(30)

        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
    else
        idx = i - (coreN + ring1N)
        c = math.max(n - (coreN + ring1N), 1)
        a = (idx / c) * TAU + t * sp * 0.5
        rr = r * 1.3
        tilt = math.rad(-40)

        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
    end
end
Shapes.Sphere = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5)) + t * sp * 2

    return Vector3.new(cos(theta) * sin(phi) * r, cos(phi) * r + h, sin(theta) * sin(phi) * r)
end
Shapes.Cylinder = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    y = (i / n) * r * 1.5 - r * 0.75

    return Vector3.new(cos(a) * r, h + y, sin(a) * r)
end
Shapes['Double Ring'] = function(i, n, t, r, h, sp)
    a = (i / (n / 2)) * TAU + t * sp

    if i % 2 == 0 then
        return Vector3.new(cos(a) * r, h, sin(a) * r)
    else
        return Vector3.new(0, h + cos(a) * r, sin(a) * r)
    end
end
Shapes.Star = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    rr = (i % 2 == 0) and r or r * 0.38

    return Vector3.new(cos(a) * rr, h + sin(t * sp * 1.5 + i * 0.3) * 1.5, sin(a) * rr)
end
Shapes.Infinity = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    d = 1 + sin(a) ^ 2

    return Vector3.new((r * cos(a)) / d, h + sin(t * sp + i * 0.2) * 1.2, (r * sin(a) * cos(a)) / d)
end
Shapes.Heart = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    pulse = 1 + 0.12 * sin(t * sp * 2)
    scale = (r / 15) * pulse
    x = 16 * sin(a) ^ 3
    z = -(13 * cos(a) - 5 * cos(2 * a) - 2 * cos(3 * a) - cos(4 * a))

    return Vector3.new(x * scale, h + sin(t * sp * 2) * 0.5, z * scale)
end
Shapes['DNA Helix'] = function(i, n, t, r, h, sp)
    y = (i / n) * r * 2 - r
    a = baseAngle(i, n, t, sp) + y * 0.5
    side = (i % 2 == 0) and 1 or -1

    return Vector3.new(cos(a) * r * side, h + y, sin(a) * r * side)
end
Shapes['Triple Helix'] = function(i, n, t, r, h, sp)
    y = (i / n) * r * 2 - r
    a = baseAngle(i, n, t, sp) + y * 0.5
    phase = (i % 3) * (TAU / 3)

    return Vector3.new(cos(a + phase) * r, h + y, sin(a + phase) * r)
end
Shapes.Tornado = function(i, n, t, r, h, sp)
    y = (i / n) * r * 2 - r
    rr = ((y + r) / (r * 2)) * r + 2
    a = baseAngle(i, n, t, sp) + y * 0.5

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes['Galaxy Spiral'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * 12 + t * sp

    return Vector3.new(cos(a) * r * (frac ^ 1.5), h + sin(t * sp * 2 + frac * 10), sin(a) * r * (frac ^ 1.5))
end
Shapes['Fibonacci Spiral'] = function(i, n, t, r, h, sp)
    frac = i / n
    angle = frac * TAU * 6.18 + t * sp
    dist = frac * r
    wave = sin(t * sp + frac * TAU) * 2

    return Vector3.new(cos(angle) * dist, h + wave, sin(angle) * dist)
end
Shapes['Spring Coil'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU * 5 + t * sp
    y = frac * r * 2 - r

    return Vector3.new(cos(a) * r * 0.5, h + y, sin(a) * r * 0.5)
end
Shapes['Vortex Funnel'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU * 4 + t * sp
    rr = frac * r
    y = (1 - frac) * r * 1.5

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes.Seashell = function(i, n, t, r, h, sp)
    frac = i / n
    u = frac * TAU * 3 + t * sp
    v = frac * TAU
    growth = math.exp(0.15 * u)
    x = growth * cos(u) * (1 + cos(v)) * r * 0.15
    y = growth * sin(u) * (1 + cos(v)) * r * 0.15
    z = growth * sin(v) * r * 0.15

    return Vector3.new(x, h + y, z)
end
Shapes.Box = function(i, n, t, r, h, sp)
    face = i % 6
    a = baseAngle(i, n, t, sp)
    wb = sin(t * sp + i) * 0.3
    s = r
    edges = {
        Vector3.new(s, sin(a) * s, cos(a) * s),
        Vector3.new(-s, sin(a) * s, cos(a) * s),
        Vector3.new(sin(a) * s, s, cos(a) * s),
        Vector3.new(sin(a) * s, -s, cos(a) * s),
        Vector3.new(sin(a) * s, cos(a) * s, s),
        Vector3.new(sin(a) * s, cos(a) * s, -s),
    }
    v = edges[face + 1]

    return Vector3.new(v.X, v.Y + h + wb, v.Z)
end
Shapes['Rounded Cube'] = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    x = pow(cos(a) * r, 0.85)
    y = pow(sin(a * 1.3) * r * 0.6, 0.85)
    z = pow(cos(a * 0.7) * r, 0.85)

    return Vector3.new(x, y + h, z)
end
Shapes.Torus = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    b = baseAngle(i, n, t * 3, sp)
    R = r
    rt = r * 0.35

    return Vector3.new((R + rt * cos(b)) * cos(a), rt * sin(b) + h, (R + rt * cos(b)) * sin(a))
end
Shapes['Torus Knot'] = function(i, n, t, r, h, sp)
    p, q = 2, 3
    a = baseAngle(i, n, t, sp)
    phi = a * q
    R = r * (1 + 0.35 * cos(p * a))

    return Vector3.new(R * cos(phi), r * 0.35 * sin(p * a) + h, R * sin(phi))
end
Shapes['M\u{f6}bius Strip'] = function(i, n, t, r, h, sp)
    frac = i / n
    u = frac * TAU + t * sp
    v = (i % 2 == 0) and 0.5 or -0.5
    w = r * 0.35

    return Vector3.new((r + w * v * cos(u / 2)) * cos(u), w * v * sin(u / 2) + h, (r + w * v * cos(u / 2)) * sin(u))
end
Shapes.Saturn = function(i, n, t, r, h, sp)
    spin = t * sp

    if i <= n * 0.6 then
        phi = math.acos(1 - 2 * (i / (n * 0.6)))
        theta = i * math.pi * (3 - sqrt(5)) + spin
        pr = r * 0.45

        return Vector3.new(cos(theta) * sin(phi) * pr, cos(phi) * pr + h, sin(theta) * sin(phi) * pr)
    else
        idx = i - n * 0.6
        c = n - n * 0.6
        a = (idx / c) * TAU + spin
        rr = r * 1.2

        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(math.rad(25)) + h, sin(a) * rr * cos(math.rad(25)))
    end
end
Shapes['Ice Cube'] = function(i, n, t, r, h, sp)
    face = i % 6
    frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
    a = frac * TAU
    s = r * 0.8
    crack = sin(t * sp * 2 + i * 0.5) * 0.4
    pts = {
        Vector3.new(s + crack, cos(a) * s, sin(a) * s),
        Vector3.new(-s - crack, cos(a) * s, sin(a) * s),
        Vector3.new(cos(a) * s, s + crack, sin(a) * s),
        Vector3.new(cos(a) * s, -s - crack, sin(a) * s),
        Vector3.new(cos(a) * s, sin(a) * s, s + crack),
        Vector3.new(cos(a) * s, sin(a) * s, -s - crack),
    }
    v = pts[face + 1]

    return Vector3.new(v.X, v.Y + h, v.Z)
end
Shapes['Black Hole'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU * 3 + t * sp
    dist = r * (1 - frac * 0.7)
    suck = sin(t * sp * 2) * 0.5

    return Vector3.new(cos(a) * dist, h + suck * frac * 3, sin(a) * dist)
end
Shapes['Hyper Sphere'] = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5)) + t * sp
    pulse = r + sin(t * sp + i * 0.3) * r * 0.25

    return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse + h, sin(theta) * sin(phi) * pulse)
end
Shapes['Orbital Rings'] = function(i, n, t, r, h, sp)
    ring = i % 3
    a = baseAngle(i, n, t, sp)
    tilts = {
        math.rad(0),
        math.rad(60),
        math.rad(-60),
    }
    tilt = tilts[ring + 1]

    return Vector3.new(cos(a) * r, sin(a) * r * sin(tilt) + h, sin(a) * r * cos(tilt))
end
Shapes['Lightning Tornado'] = function(i, n, t, r, h, sp)
    y = (i / n) * r * 2 - r
    rr = ((y + r) / (r * 2)) * r + 2
    bolt = sin(i * 7.3 + t * sp * 5) * 3
    a = baseAngle(i, n, t, sp) + y * 0.5

    return Vector3.new(cos(a) * rr + bolt, h + y, sin(a) * rr + bolt)
end
Shapes['Plasma Cage'] = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5))
    arc = sin(t * sp * 2 + phi * 6) * r * 0.2
    rr = r + arc

    return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h, sin(theta) * sin(phi) * rr)
end
Shapes.Wormhole = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU + t * sp
    y = (frac - 0.5) * r * 3
    neck = r * (1 - math.exp(-((y / (r * 0.8)) ^ 2))) + 0.5

    return Vector3.new(cos(a) * neck, h + y, sin(a) * neck)
end
Shapes['Quantum Lattice'] = function(i, n, t, r, h, sp)
    grid = math.ceil(n ^ (0.3333333333333333))
    gx = i % grid
    gy = math.floor(i / grid) % grid
    gz = math.floor(i / (grid * grid)) % grid
    scale = r * 2 / grid
    jitter = sin(t * sp + i * 1.7) * 0.3

    return Vector3.new((gx - grid / 2) * scale + jitter, (gy - grid / 2) * scale + h, (gz - grid / 2) * scale + jitter)
end
Shapes['Neutron Burst'] = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5))
    burst = r * abs(sin(t * sp + i * 0.4))

    return Vector3.new(cos(theta) * sin(phi) * burst, cos(phi) * burst + h, sin(theta) * sin(phi) * burst)
end
Shapes['Arc Discharge'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU
    arc = sin(frac * math.pi) * r
    zap = sin(t * sp * 8 + i * 2.1) * r * 0.15

    return Vector3.new(cos(a) * r + zap, h + arc + zap, sin(a) * r + zap)
end
Shapes['Event Horizon'] = function(i, n, t, r, h, sp)
    frac = i / n
    a = frac * TAU * 5 + t * sp
    dist = r * (0.2 + 0.8 * abs(sin(frac * math.pi)))
    warp = sin(t * sp * 3 + frac * TAU) * r * 0.1

    return Vector3.new(cos(a) * dist, h + warp, sin(a) * dist)
end
Shapes.Butterfly = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp * 0.5)
    ex = math.exp(cos(a)) - 2 * cos(4 * a) - sin(a / 12) ^ 5
    rr = ex * r * 0.4

    return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.1) * 1.5, sin(a) * rr)
end
Shapes['Rose Petal'] = function(i, n, t, r, h, sp)
    k = 5
    a = baseAngle(i, n, t, sp * 0.3)
    rr = r * cos(k * a)

    return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.2) * 2, sin(a) * rr)
end
Shapes.Snowflake = function(i, n, t, r, h, sp)
    arm = i % 6
    frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
    baseA = arm * (TAU / 6) + t * sp * 0.2
    dist = frac * r
    branch = sin(frac * math.pi * 4) * r * 0.2

    return Vector3.new(cos(baseA) * dist + cos(baseA + math.pi / 2) * branch, h + cos(t * sp) * 0.5, sin(baseA) * dist + sin(baseA + math.pi / 2) * branch)
end
Shapes['Crystal Bloom'] = function(i, n, t, r, h, sp)
    petals = 8
    arm = i % petals
    frac = (i % math.max(math.floor(n / petals), 1)) / math.max(math.floor(n / petals), 1)
    baseA = arm * (TAU / petals) + t * sp * 0.3
    dist = frac * r
    lift = sin(frac * math.pi) * r * 0.5

    return Vector3.new(cos(baseA) * dist, h + lift, sin(baseA) * dist)
end
Shapes['Vine Wrap'] = function(i, n, t, r, h, sp)
    frac = i / n
    turns = 4
    a = frac * TAU * turns + t * sp
    y = frac * r * 2 - r
    bulge = 1 + 0.3 * sin(frac * TAU * turns * 2)
    rr = r * 0.5 * bulge

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes['Flower Bloom'] = function(i, n, t, r, h, sp)
    petals = 6
    a = baseAngle(i, n, t, sp * 0.4)
    rr = r * abs(cos(petals * a * 0.5))
    bloom = 1 + 0.2 * sin(t * sp * 2)

    return Vector3.new(cos(a) * rr * bloom, h + sin(t * sp + a) * 1.5, sin(a) * rr * bloom)
end
Shapes.Jellyfish = function(i, n, t, r, h, sp)
    bell = math.floor(n * 0.4)

    if i <= bell then
        phi = (i / bell) * math.pi * 0.5
        theta = baseAngle(i, bell, t, sp)
        pulse = r * (1 + 0.2 * sin(t * sp * 3))

        return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse * 0.5 + h, sin(theta) * sin(phi) * pulse)
    else
        idx = i - bell
        c = n - bell
        a = (idx / c) * TAU + t * sp
        drop = (idx / c) * r * 1.5
        wave = sin(t * sp * 4 + a * 3) * r * 0.15

        return Vector3.new(cos(a) * r * 0.2 + wave, h - drop, sin(a) * r * 0.2 + wave)
    end
end
Shapes['Coral Reef'] = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp * 0.2)
    frac = i / n
    y = sin(frac * TAU * 3 + t * sp) * r * 0.8
    rr = r * (0.5 + 0.5 * sin(frac * TAU * 5))
    sway = sin(t * sp + frac * 12) * r * 0.1

    return Vector3.new(cos(a) * rr + sway, h + y, sin(a) * rr + sway)
end
Shapes['Volcano Burst'] = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    burst = abs(sin(t * sp + i)) * r * 2

    return Vector3.new(cos(a) * burst, h + burst, sin(a) * burst)
end
Shapes['Cosmic Explosion'] = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    wave = abs(sin(t * sp * 2 - i * 0.1))

    return Vector3.new(cos(a) * r * wave * 3, h + wave * 5, sin(a) * r * wave * 3)
end
Shapes.Supernova = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5)) + t * sp
    blast = r * (1 + sin(t * sp * 0.5) * 0.5)
    eject = sin(phi * 3 + t * sp * 4) * r * 0.3

    return Vector3.new(cos(theta) * sin(phi) * (blast + eject), cos(phi) * (blast + eject) + h, sin(theta) * sin(phi) * (blast + eject))
end
Shapes['Firework Pop'] = function(i, n, t, r, h, sp)
    phi = math.acos(1 - 2 * (i / n))
    theta = i * math.pi * (3 - sqrt(5))
    trail = abs(sin(t * sp * 3 + i * 0.7))
    rr = r * trail
    sparkle = sin(t * sp * 10 + i) * 0.8

    return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h + sparkle, sin(theta) * sin(phi) * rr)
end
Shapes.Shockwave = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    ring = sin(t * sp * 3) * r
    y = cos(t * sp * 2 + i * 0.2) * r * 0.3

    return Vector3.new(cos(a) * ring, h + y, sin(a) * ring)
end
Shapes.Lissajous = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    p, q = 3, 2
    d = math.pi / 2

    return Vector3.new(r * sin(p * a + d), h + r * 0.4 * sin(t * sp + i * 0.1), r * sin(q * a))
end
Shapes.Hypotrochoid = function(i, n, t, r, h, sp)
    R, rd, d = r, r * 0.4, r * 0.7
    a = baseAngle(i, n, t, sp)
    x = (R - rd) * cos(a) + d * cos((R - rd) / rd * a)
    z = (R - rd) * sin(a) - d * sin((R - rd) / rd * a)

    return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.2) * 2, z * 0.7)
end
Shapes.Epitrochoid = function(i, n, t, r, h, sp)
    R, rd, d = r * 0.6, r * 0.35, r * 0.5
    a = baseAngle(i, n, t, sp)
    x = (R + rd) * cos(a) - d * cos((R + rd) / rd * a)
    z = (R + rd) * sin(a) - d * sin((R + rd) / rd * a)

    return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.15) * 2, z * 0.7)
end
Shapes.Trefoil = function(i, n, t, r, h, sp)
    a = baseAngle(i, n, t, sp)
    x = sin(a) + 2 * sin(2 * a)
    y = cos(a) - 2 * cos(2 * a)
    z = -sin(3 * a)
    sc = r / 3

    return Vector3.new(x * sc, y * sc + h, z * sc)
end
Shapes['Klein Bottle Slice'] = function(i, n, t, r, h, sp)
    u = baseAngle(i, n, t, sp)
    v = baseAngle(i, math.max(n, 1), t * 2, sp)
    a = r * 0.3
    x = (a + a * cos(v)) * cos(u)
    y = (a + a * cos(v)) * sin(u)
    z = a * sin(v) + sin(t * sp + i * 0.2) * 2

    return Vector3.new(x, y + h, z)
end
Shapes.Harmonograph = function(i, n, t, r, h, sp)
    frac = i / n
    decay = math.exp(-frac * 0.5)
    f1, f2, f3, f4 = 3, 2, 3, 2
    p1, p2 = math.pi / 4, math.pi / 6
    a = frac * TAU * 8 + t * sp
    x = r * decay * (sin(f1 * a + p1) + sin(f2 * a))
    z = r * decay * (sin(f3 * a + p2) + sin(f4 * a))

    return Vector3.new(x * 0.5, h + sin(t * sp + frac * 12) * 1.5, z * 0.5)
end

function GetShapeOffset(index, total, t, cfg)
    fn = Shapes[cfg.CurrentShape]

    if fn then
        return fn(index, total, t, cfg.Radius, cfg.Height, cfg.Speed)
    end

    a = (index / total) * TAU + t * cfg.Speed

    return Vector3.new(cos(a) * cfg.Radius, cfg.Height, sin(a) * cfg.Radius)
end

SparklerGroup = Tabs.ToyTab:AddLeftGroupbox('Sparkler Auras', 'flame')

SparklerGroup:AddButton('Synchronize All Sparklers', function()
    for _, obj in ipairs(workspace:GetDescendants())do
        if obj.Name:find('FireworkSparkler') then
            SetupPhysics(obj, activeSparklers)
        end
    end
end)
SparklerGroup:AddButton('Unsynchronize All Sparklers', function()
    activeSparklers = {}
end)
SparklerGroup:AddSlider('HeightSlider', {
    Text = 'Height Offset',
    Default = 5,
    Min = -20,
    Max = 150,
    Rounding = 0,
})
Options.HeightSlider:OnChanged(function()
    sparklerConfig.Height = Options.HeightSlider.Value
end)
SparklerGroup:AddSlider('RadiusSlider', {
    Text = 'Shape Radius',
    Default = 15,
    Min = 2,
    Max = 100,
    Rounding = 0,
})
Options.RadiusSlider:OnChanged(function()
    sparklerConfig.Radius = Options.RadiusSlider.Value
end)
SparklerGroup:AddSlider('SpeedSlider', {
    Text = 'Rotation Speed',
    Default = 2,
    Min = 0,
    Max = 20,
    Rounding = 1,
})
Options.SpeedSlider:OnChanged(function()
    sparklerConfig.Speed = Options.SpeedSlider.Value
end)
SparklerGroup:AddDropdown('ShapeDropdown', {
    Values = shapeOptions,
    Default = 'Planet',
    Multi = false,
    Text = 'Select Shape',
})
Options.ShapeDropdown:OnChanged(function()
    sparklerConfig.CurrentShape = Options.ShapeDropdown.Value
end)
RunService.RenderStepped:Connect(function()
    char = LocalPlayer.Character
    targetRoot = char and char:FindFirstChild('HumanoidRootPart')

    if not targetRoot then
        return
    end

    t = tick()
    prediction = targetRoot.AssemblyLinearVelocity * 0.12
    rot = targetRoot.CFrame.Rotation

    for i = #activeSparklers, 1, -1 do
        obj = activeSparklers[i]

        if obj and obj.Parent then
            main = obj:IsA('BasePart') and obj or obj.PrimaryPart
            bp = main and main:FindFirstChild('ToyBodyPos')
            bg = main and main:FindFirstChild('ToyBodyGyro')

            if bp and bg then
                offset = GetShapeOffset(i, #activeSparklers, t, sparklerConfig)

                bp.Position = targetRoot.Position + prediction + (rot * offset)
                bg.CFrame = CFrame.new(main.Position, targetRoot.Position + prediction)
            end
        else
            table.remove(activeSparklers, i)
        end
    end
end)

local Coco = Tabs.ToyTab:AddLeftGroupbox("Coconuts", "burger")

Coco:AddDivider()
Coco:AddCheckbox('CoconutOrbit', {
    Text = 'Coconut Penis',
    Tooltip = 'Makes a dick out of coconuts',
    Default = false,
})
Toggles.CoconutOrbit:OnChanged(function(Value)
    CoconutEnabled = Value

    if not Value then
        return
    end

    task.spawn(function()
        local Me = game:GetService('Players').LocalPlayer
        local ReplicatedStorage = game:GetService('ReplicatedStorage')
        local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
        local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
        local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
        local Offsets = {
            [1] = CFrame.new(-0.45, -1.2, -0.7),
            [2] = CFrame.new(0.45, -1.2, -0.7),
            [3] = CFrame.new(0, -1, 0.8),
        }
        local Coconuts = {}

        while CoconutEnabled do
            local Character = Me.Character
            local Root = Character and Character:FindFirstChild('HumanoidRootPart')

            if not Root then
                task.wait(0.1)

                continue
            end

            local Folder = workspace:FindFirstChild(Me.Name .. 'SpawnedInToys')

            if not Folder then
                task.wait(0.1)

                continue
            end

            local Length = Options.CoconutAmount.Value
            local Damping = Options.CoconutDamping.Value

            table.clear(Coconuts)

            for _, Toy in ipairs(Folder:GetChildren())do
                if Toy.Name == 'FoodCoconut' then
                    table.insert(Coconuts, Toy)
                end
            end

            if #Coconuts < (Length + 2) then
                task.spawn(function()
                    SpawnToy:InvokeServer('FoodCoconut', Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                end)
            end

            for i, Coconut in ipairs(Coconuts)do
                local Part = Coconut:FindFirstChild('SoundPart')
                local HoldPart = Coconut:FindFirstChild('HoldPart')
                local Rigid = HoldPart and HoldPart:FindFirstChild('RigidConstraint')
                local PartOwner = Part and Part:FindFirstChild('PartOwner')

                if Part and HoldPart and Rigid then
                    if PartOwner and PartOwner.Value == Me.Name then
                        if i <= 2 then
                            Part.CFrame = Root.CFrame * Offsets[i] * CFrame.new(Root.Velocity / Damping)
                        else
                            Part.CFrame = Root.CFrame * Offsets[3] * CFrame.new(Root.Velocity / Damping) * CFrame.new(0, 0, Offsets[3].Z - (i + 0.2))
                        end

                        Part.Velocity = Vector3.zero
                    else
                        SetNetworkOwner:FireServer(Part, Part.CFrame)
                    end
                    if Rigid.Attachment1 then
                        DestroyToy:FireServer(Coconut)
                    end

                    for _, PartObj in ipairs(Coconut:GetChildren())do
                        if PartObj:IsA('BasePart') then
                            PartObj.CanCollide = false
                            PartObj.CanQuery = false

                            if PartObj.Transparency ~= 1 then
                                PartObj.Transparency = 0
                            end
                        end
                    end
                end
            end

            task.wait(0.01)
        end
    end)
end)
Coco:AddDivider()
Coco:AddCheckbox('CoconutBodyParts', {
    Text = "Coconut boob's and ass",
    Tooltip = 'Places coconuts on your chest and ass (making boobs and ass sob)',
    Default = false,
})
Toggles.CoconutBodyParts:OnChanged(function(Value)
    CoconutBodyEnabled = Value

    if not Value then
        return
    end

    task.spawn(function()
        local Me = game:GetService('Players').LocalPlayer
        local ReplicatedStorage = game:GetService('ReplicatedStorage')
        local SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
        local SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
        local DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
        local Coconuts = {}

        while CoconutBodyEnabled do
            local Character = Me.Character
            local Root = Character and Character:FindFirstChild('HumanoidRootPart')

            if not Root then
                task.wait(0.1)

                continue
            end

            local Folder = workspace:FindFirstChild(Me.Name .. 'SpawnedInToys')

            if not Folder then
                task.wait(0.1)

                continue
            end

            table.clear(Coconuts)

            for _, Toy in ipairs(Folder:GetChildren())do
                if Toy.Name == 'FoodCoconut' then
                    table.insert(Coconuts, Toy)
                end
            end

            if #Coconuts < 4 then
                for _ = 1, 4 - #Coconuts do
                    task.spawn(function()
                        SpawnToy:InvokeServer('FoodCoconut', Root.CFrame * CFrame.new(-5, 0, 10), Vector3.zero)
                    end)
                end
            end

            for i = 1, math.min(4, #Coconuts)do
                local Coconut = Coconuts[i]
                local Part = Coconut:FindFirstChild('SoundPart')
                local HoldPart = Coconut:FindFirstChild('HoldPart')
                local Rigid = HoldPart and HoldPart:FindFirstChild('RigidConstraint')
                local PartOwner = Part and Part:FindFirstChild('PartOwner')

                if Part and HoldPart and Rigid then
                    if PartOwner and PartOwner.Value == Me.Name then
                        local TargetCF

                        if i == 1 then
                            TargetCF = Root.CFrame * CFrame.new(-0.4, 0.3, -0.55)
                        elseif i == 2 then
                            TargetCF = Root.CFrame * CFrame.new(0.4, 0.3, -0.55)
                        elseif i == 3 then
                            TargetCF = Root.CFrame * CFrame.new(-0.35, -1.1, 0.45)
                        else
                            TargetCF = Root.CFrame * CFrame.new(0.35, -1.1, 0.45)
                        end

                        Part.CFrame = TargetCF
                        Part.Velocity = Vector3.zero
                    else
                        SetNetworkOwner:FireServer(Part, Part.CFrame)
                    end
                    if Rigid.Attachment1 then
                        DestroyToy:FireServer(Coconut)
                    end

                    for _, PartObj in ipairs(Coconut:GetChildren())do
                        if PartObj:IsA('BasePart') then
                            PartObj.CanCollide = false
                            PartObj.CanQuery = false

                            if PartObj.Transparency ~= 1 then
                                PartObj.Transparency = 0
                            end
                        end
                    end
                end
            end

            task.wait(0.01)
        end
    end)
end)
Coco:AddDivider()

local CoconutEnabled = false
local ShapeEnabled = false

Coco:AddSlider('CoconutAmount', {
    Text = 'Coconut Amount',
    Default = 10,
    Min = 3,
    Max = 25,
    Rounding = 0,
})
Coco:AddSlider('CoconutDamping', {
    Text = 'Damping',
    Default = 100,
    Min = 1,
    Max = 500,
    Rounding = 0,
})

do
    local PlaceBox = Tabs.TpTab:AddLeftGroupbox('Teleport To Location', 'plane')
    local Scripts = Tabs.TpTab:AddRightGroupbox('Auto Claim Plot', 'wind')

    PLOT_NAMES = {
        [1] = 'Pink House',
        [2] = 'Spooky House',
        [3] = 'Blue House',
        [4] = 'Green House',
        [5] = 'Chinese House',
    }

    getgenv().SelectedPlot = nil
    getgenv().ClaimSelectedPlot = false
    getgenv().MyPlotClaimStart = {}
    PlotClaimTimes = {}
    PlotInfoLabel = Scripts:AddLabel('No plot selected')
    PlotInsideLabel = Scripts:AddLabel('Inside Plot: N/A')
    PlotInsideCountLabel = Scripts:AddLabel('Players Inside: 0')
    PlotMyTimeLabel = Scripts:AddLabel('Your Ownership: N/A')

    function FormatTime(seconds)
        seconds = math.floor(seconds)
        local hrs = math.floor(seconds / 3600)
        local mins = math.floor((seconds % 3600) / 60)
        local secs = seconds % 60

        if hrs > 0 then
            return string.format('%02ih %02im %02is', hrs, mins, secs)
        elseif mins > 0 then
            return string.format('%02im %02is', mins, secs)
        else
            return string.format('%02is', secs)
        end
    end

    function IsPointInsidePart(part, point)
        local relative = part.CFrame:PointToObjectSpace(point)
        local half = part.Size * 0.5
        return math.abs(relative.X) <= half.X and math.abs(relative.Y) <= half.Y and math.abs(relative.Z) <= half.Z
    end

    function GetPlotIndex(plot)
        for i, p in ipairs(workspace.Plots:GetChildren()) do
            if p == plot then
                return i
            end
        end
        return nil
    end

    function GetPlayersInPlot(plot)
        local zone = plot:FindFirstChild('Region') or plot:FindFirstChild('Zone') or plot:FindFirstChild('Base') or plot:FindFirstChildWhichIsA('BasePart')
        if not zone then
            return {}
        end

        local inside = {}
        for _, plr in ipairs(Players:GetPlayers()) do
            local char = plr.Character
            local hrp = char and char:FindFirstChild('HumanoidRootPart')
            if hrp and IsPointInsidePart(zone, hrp.Position) then
                table.insert(inside, plr)
            end
        end
        return inside
    end

    function UpdatePlotInfo()
        local selected = getgenv().SelectedPlot

        if not selected then
            PlotInfoLabel:SetText('No plot selected')
            PlotInsideLabel:SetText('Inside Plot: N/A')
            PlotInsideCountLabel:SetText('Players Inside: 0')
            PlotMyTimeLabel:SetText('Your Ownership: N/A')
            return
        end

        for _, plot in ipairs(workspace.Plots:GetChildren()) do
            local i = GetPlotIndex(plot)
            if not i then
                continue
            end

            local plotName = PLOT_NAMES[i] or ('Plot ' .. i)

            if selected ~= plotName then
                continue
            end

            local ownersFolder = plot.PlotSign.ThisPlotsOwners
            local owners = ownersFolder:GetChildren()
            local ownerNames = {}
            local playerCount = 0
            local longestTime = 0
            local isLocalOwner = false

            for _, owner in ipairs(owners) do
                if owner.Value and owner.Value ~= '' then
                    playerCount = playerCount + 1

                    if owner.Value == LocalPlayer.Name then
                        isLocalOwner = true
                    end

                    local plr = Players:FindFirstChild(owner.Value)
                    local displayText = owner.Value

                    if plr and plr.DisplayName ~= plr.Name then
                        displayText = plr.DisplayName .. ' (@' .. plr.Name .. ')'
                    end

                    table.insert(ownerNames, displayText)

                    if not PlotClaimTimes[owner.Value] then
                        PlotClaimTimes[owner.Value] = tick()
                    end

                    local ownedFor = tick() - PlotClaimTimes[owner.Value]

                    if ownedFor > longestTime then
                        longestTime = ownedFor
                    end
                end
            end

            if isLocalOwner then
                if not getgenv().MyPlotClaimStart[selected] then
                    getgenv().MyPlotClaimStart[selected] = tick()
                end
            else
                getgenv().MyPlotClaimStart[selected] = nil
            end

            local myOwnedFor = getgenv().MyPlotClaimStart[selected] and (tick() - getgenv().MyPlotClaimStart[selected]) or nil
            local playersInside = GetPlayersInPlot(plot)
            local insideCount = #playersInside
            local insideNames = {}

            for _, p in ipairs(playersInside) do
                table.insert(insideNames, p.DisplayName or p.Name)
            end

            PlotInfoLabel:SetText('Selected Plot: ' .. plotName)
            PlotInsideLabel:SetText('Inside Plot: ' .. (insideCount > 0 and table.concat(insideNames, ', ') or 'None'))
            PlotInsideCountLabel:SetText('Players Inside: ' .. insideCount)
            PlotMyTimeLabel:SetText('Your Ownership: ' .. (myOwnedFor and FormatTime(myOwnedFor) or 'N/A'))

            return
        end
    end

    function GetPlotData()
        local tbl = {}
        local plotsFolder = workspace:FindFirstChild('Plots')

        if not plotsFolder then
            return tbl
        end

        for i, plot in ipairs(plotsFolder:GetChildren()) do
            local plotName = PLOT_NAMES[i] or ('Plot ' .. i)
            table.insert(tbl, plotName)
        end

        return tbl
    end

    local PlotSelector

    task.defer(function()
        PlotSelector = Scripts:AddDropdown('PlotSelector', {
            Text = 'Select Plot',
            Values = GetPlotData(),
            Multi = false,
            Default = nil,
            Tooltip = 'Choose plot',
            Callback = function(Value)
                getgenv().SelectedPlot = Value
                UpdatePlotInfo()
            end,
        })
    end)

    Scripts:AddButton({
        Text = 'Refresh Plots',
        Tooltip = 'Update plots',
        Func = function()
            if PlotSelector then
                PlotSelector:SetValues(GetPlotData())
            end
            UpdatePlotInfo()
        end,
    })

    Scripts:AddCheckbox('ClaimSelectedPlot', {
        Text = 'Claim Selected Plot',
        Tooltip = 'Claims chosen plot',
        Default = false,
        Callback = function(v)
            getgenv().ClaimSelectedPlot = v
        end
    })

    task.spawn(function()
        while true do
            task.wait(1)
            UpdatePlotInfo()
        end
    end)

    task.spawn(function()
        while true do
            task.wait(0.1)

            if not getgenv().ClaimSelectedPlot then
                continue
            end
            if not getgenv().SelectedPlot then
                continue
            end

            local character = LocalPlayer.Character
            local hrp = character and character:FindFirstChild('HumanoidRootPart')

            if not hrp then
                continue
            end

            local oldCFrame = hrp.CFrame
            local oldVel = hrp.AssemblyLinearVelocity

            for i, plot in ipairs(workspace.Plots:GetChildren()) do
                local plotName = PLOT_NAMES[i] or ('Plot ' .. i)

                if getgenv().SelectedPlot == plotName then
                    local ownersFolder = plot.PlotSign.ThisPlotsOwners

                    if #ownersFolder:GetChildren() <= 0 then
                        local jellyfish = plot.PlotSign.Sign.Plus.PlusGrabPart

                        while #ownersFolder:GetChildren() <= 0 and getgenv().ClaimSelectedPlot do
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                                hrp.CFrame = jellyfish.CFrame + Vector3.new(10, 10, 10)
                                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(jellyfish, jellyfish.CFrame)
                            end
                            task.wait(0.1)
                        end

                        hrp.CFrame = oldCFrame
                        hrp.AssemblyLinearVelocity = oldVel
                    end

                    break
                end
            end
        end
    end)

    local loopTeleportActive = false

    PlaceBox:AddDropdown('PlaceSelect', {
        Values = {
            'Spawn',
            'SpawnCave',
            'GreenHouse',
            'PinkHouse',
            'Barn',
            'BlueHouse',
            'ChineseHouse',
            'PurpleHouse',
            'Factory',
            'OtherGreenHouse',
            'BigCave',
            'TrainCave',
            'IslandCave',
            'ChineseRoof',
            'UfoCave',
            'Prison',
            'GoodPrison',
            'RuhubsDogAhhPrison',
            'ExtremelyGoodPrison',
            'BlueHouseSlot',
            'SpawnSlot',
            'HauntedSlot',
            'RandomSlot',
            'BeachSlot',
        },
        Default = 'Spawn',
        Text = 'Select Place',
        Tooltip = 'Choose what place you want to teleport to',
        Callback = function(Value)
            _G.PlaceToTeleport = Value
        end,
    })

    PlaceBox:AddCheckbox('LoopTeleportToggle', {
        Text = 'Loop Teleport',
        Default = false,
        Tooltip = 'Continuously teleport to the selected place with no delay',
        Callback = function(Value)
            loopTeleportActive = Value

            if Value then
                task.spawn(function()
                    while loopTeleportActive do
                        local targetLocation = placeLocations[_G.PlaceToTeleport]

                        if targetLocation then
                            TeleportPlayer(targetLocation)
                        end

                        task.wait()
                    end
                end)
            end
        end,
    })

    local SlotsFolder = workspace:WaitForChild('Slots')
    local SlotsScreen = SlotsFolder:WaitForChild('Slots')
    local SlotGui = SlotsScreen:WaitForChild('Screen'):WaitForChild('SlotGui')
    local TimeTextObj = SlotGui:WaitForChild('TimeLeftFrame'):WaitForChild('TimeText')
    local slotHandle = workspace.Slots.Slots.SlotHandle.Handle
    local targetTime = '0:00'
    local farmingActive = false
    local slotTeleportPosition = Vector3.new(-224.941177, 91.364975, 425.75116)
    local savedCFrame = nil
    local FarmGroup = Tabs.TpTab:AddLeftGroupbox('Farm Coins', 'boxes')
    local timeLabel = FarmGroup:AddLabel('<b>Time:</b> ' .. TimeTextObj.Text)

    task.spawn(function()
        while task.wait(1) do
            if TimeTextObj and timeLabel then
                pcall(function()
                    timeLabel:SetText('Time: ' .. TimeTextObj.Text)
                end)
            end
        end
    end)

    function farmLoop()
        while farmingActive do
            task.wait(1)

            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild('HumanoidRootPart')

            if not hrp then
                continue
            end

            savedCFrame = hrp.CFrame

            local currentTime = TimeTextObj and TimeTextObj.Text

            if currentTime == targetTime then
                hrp.CFrame = CFrame.new(slotTeleportPosition)

                task.wait(1)
                pcall(function()
                    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(slotHandle, CFrame.new(slotTeleportPosition))
                end)
                task.wait(1)

                if hrp and hrp.Parent then
                    hrp.CFrame = savedCFrame
                end
            end
        end
    end

    FarmGroup:AddCheckbox('AutoFarmToggle', {
        Text = 'Auto Spin Slots',
        Tooltip = 'Automatically spins a slot for you',
        Default = false,
        Callback = function(state)
            farmingActive = state

            if state then
                task.spawn(farmLoop)
            end
        end,
    })

    LocalPlayer.CharacterAdded:Connect(function(char)
        char:WaitForChild('HumanoidRootPart')

        if farmingActive then
            task.spawn(farmLoop)
        end
    end)

do
    tppEnabled = false
    tppTargets = {}
    tppMethod = "Grab"
    tppDropdown = nil
    tppCountLabel = nil
    tppStatusLabel = nil
    Players = game:GetService("Players")
    RunService = game:GetService("RunService")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    Workspace = game:GetService("Workspace")
    LocalPlayer = Players.LocalPlayer
    plr = LocalPlayer
    HRP = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
    R = ReplicatedStorage
    SetNetOwner = R.GrabEvents.SetNetworkOwner
    CreateLine = R.GrabEvents.CreateGrabLine
    DestroyLine = R.GrabEvents.DestroyGrabLine
    DestroyToy = R.MenuToys.DestroyToy
    SpawnToy = R.MenuToys.SpawnToyRemoteFunction
    StickyEvent = R.PlayerEvents.StickyPartEvent

    local function refreshPlayers()
        list = {}
        for _, pl in ipairs(Players:GetPlayers()) do
            if pl ~= plr then
                table.insert(list, pl.DisplayName .. " (" .. pl.Name .. ")")
            end
        end
        if tppDropdown then
            tppDropdown:SetValues(list)
        end
    end

    local function formatList(t)
        if #t <= 3 then
            return table.concat(t, ", ")
        end
        return table.concat(t, ", ", 1, 3) .. " +" .. (#t - 3)
    end

    local function runTPPLoop()
        task.spawn(function()
            while tppEnabled do
                RunService.RenderStepped:Wait()
                if #tppTargets == 0 then
                    continue
                end

                for _, targetName in ipairs(tppTargets) do
                    target = Players:FindFirstChild(targetName)
                    if not target or not target.Character then
                        continue
                    end

                    char = target.Character
                    hrp = char:FindFirstChild("HumanoidRootPart")
                    head = char:FindFirstChild("Head")

                    if not hrp or not head then
                        continue
                    end

                    partOwner = head:FindFirstChild("PartOwner")
                    if not partOwner or partOwner.Value == "" or partOwner.Value == plr.Name then
                        continue
                    end

                    distance = (hrp.Position - HRP.Position).Magnitude

                    if distance <= 30 then
                        pcall(function()
                            SetNetOwner:FireServer(hrp, hrp.CFrame)
                            CreateLine:FireServer(hrp, Vector3.zero, hrp.Position, false)
                        end)

                        weOwnIt = head:FindFirstChild("PartOwner") and head.PartOwner.Value == plr.Name

                        if weOwnIt then
                            if tppMethod == "Bring" then
                                hrp.CFrame = HRP.CFrame * CFrame.new(0, 5, 0)
                                hrp.AssemblyLinearVelocity = Vector3.zero
                                hrp.AssemblyAngularVelocity = Vector3.zero
                            end
                            pcall(function()
                                DestroyLine:FireServer(hrp)
                            end)
                        end
                    else
                        saved = HRP.CFrame
                        HRP.CFrame = hrp.CFrame * CFrame.new(0, 0, 2)
                        task.wait(0.05)

                        for i = 1, 15 do
                            pcall(function()
                                SetNetOwner:FireServer(hrp, hrp.CFrame)
                                CreateLine:FireServer(hrp, Vector3.zero, hrp.Position, false)
                            end)
                            task.wait(0.01)
                        end

                        weOwnIt = head:FindFirstChild("PartOwner") and head.PartOwner.Value == plr.Name

                        if weOwnIt then
                            if tppMethod == "Bring" then
                                hrp.CFrame = saved * CFrame.new(0, 5, 0)
                                hrp.AssemblyLinearVelocity = Vector3.zero
                                hrp.AssemblyAngularVelocity = Vector3.zero
                                task.wait(0.05)
                            end
                            pcall(function()
                                DestroyLine:FireServer(hrp)
                            end)
                        end

                        HRP.CFrame = saved
                        HRP.AssemblyLinearVelocity = Vector3.zero
                        HRP.AssemblyAngularVelocity = Vector3.zero
                    end
                end
            end
        end)
    end

DefT = Tabs.Defense:AddRightGroupbox("Third Party Protection", "shield")

    tppDropdown = DefT:AddDropdown("TPPTarget", {
        Text = "Protect Players",
        Values = {},
        Default = {},
        Multi = true,
        Callback = function(v)
            tppTargets = {}
            if typeof(v) == "table" then
                for key, state in pairs(v) do
                    if state == true then
                        name = key:match("%((.-)%)")
                        if name then
                            table.insert(tppTargets, name)
                        end
                    end
                end
            end
        end
    })

    DefT:AddButton({
        Text = "Refresh Players",
        Func = refreshPlayers
    })

    DefT:AddButton({
        Text = "Clear All Protected",
        Func = function()
            tppTargets = {}
            tppDropdown:SetValue({})
        end
    })

    tppCountLabel = DefT:AddLabel("Protecting: 0 players")
    tppStatusLabel = DefT:AddLabel("Status: Disabled")

    task.spawn(function()
        while task.wait(0.5) do
            if tppCountLabel then
                tppCountLabel:SetText("Protecting: " .. #tppTargets .. " players")
            end
            if tppStatusLabel then
                if not tppEnabled then
                    tppStatusLabel:SetText("Status: Disabled")
                elseif #tppTargets == 0 then
                    tppStatusLabel:SetText("Status: No Targets")
                else
                    tppStatusLabel:SetText("Status: Active - " .. #tppTargets .. " targets")
                end
            end
        end
    end)

    DefT:AddDropdown("TPPMethod", {
        Text = "Method",
        Values = {"Grab", "Bring"},
        Default = "Grab",
        Callback = function(v)
            tppMethod = v
        end
    })

    DefT:AddCheckbox("TPPEnabled", {
        Text = "Third Party Protection",
        Default = false,
        Callback = function(v)
            tppEnabled = v
            if v then
                runTPPLoop()
            end
        end
    })

    DefT:AddCheckbox("TPPAntiKick", {
        Text = "Anti Kick Target(fixing)",
        Default = false,
        Callback = function(v)
            if not v then
                for _, targetName in ipairs(tppTargets) do
                    target = Players:FindFirstChild(targetName)
                    if target then
                        targetInv = Workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                        if targetInv and targetInv:FindFirstChild("TPPShuriken_" .. targetName) then
                            pcall(function() DestroyToy:FireServer(targetInv["TPPShuriken_" .. targetName]) end)
                        end
                    end
                end
                return
            end

            task.spawn(function()
                shurikens = {}
                setupDone = {}

                while Toggles.TPPAntiKick.Value do
                    RunService.RenderStepped:Wait()

                    if #tppTargets == 0 then
                        continue
                    end

                    for _, targetName in ipairs(tppTargets) do
                        pcall(function()
                            target = Players:FindFirstChild(targetName)
                            if not target or not target.Character then
                                setupDone[targetName] = false
                                return
                            end

                            targetChar = target.Character
                            targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                            targetFirePart = targetHRP and targetHRP:FindFirstChild("FirePlayerPart")

                            if not targetHRP or not targetFirePart then
                                return
                            end

                            myChar = plr.Character
                            myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")

                            if myHRP then
                                dist = (targetHRP.Position - myHRP.Position).Magnitude
                                if dist > 30 then
                                    setupDone[targetName] = false
                                    return
                                end
                            end

                            targetInv = Workspace:FindFirstChild(target.Name .. "SpawnedInToys")
                            if not targetInv then
                                return
                            end

                            shuName = "TPPShuriken_" .. targetName
                            shuData = shurikens[targetName]
                            shu = shuData and shuData.toy
                            part = shuData and shuData.part
                            shuExists = shu and shu.Parent ~= nil
                            partExists = part and part.Parent ~= nil

                            if setupDone[targetName] and (not shuExists or not partExists) then
                                setupDone[targetName] = false
                                shurikens[targetName] = nil
                            end

                            if not setupDone[targetName] then
                                if not target.CanSpawnToy or not target.CanSpawnToy.Value then
                                    return
                                end

                                local function spawntoy(toy, cf)
                                    if not plr.CanSpawnToy.Value then
                                        plr.CanSpawnToy.Changed:Wait()
                                    end
                                    local t
                                    local toyadded
                                    inv = Workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
                                    toyadded = inv.ChildAdded:Connect(function(c)
                                        if c.Name == toy then
                                            t = c
                                            toyadded:Disconnect()
                                        end
                                    end)
                                    task.spawn(function()
                                        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
                                    end)
                                    time = tick() + 1
                                    repeat task.wait() until t or tick() > time
                                    if t then
                                        return t
                                    else
                                        return nil
                                    end
                                end

                                shu = spawntoy("NinjaShuriken", targetHRP.CFrame * CFrame.new(5, 10, 20))
                                if not shu then
                                    return
                                end

                                shu.Name = shuName
                                part = shu:WaitForChild("StickyPart", 0.5)
                                if not part then
                                    return
                                end

                                SetNetOwner:FireServer(part, part.CFrame)
                                task.wait(0.1)
                                StickyEvent:FireServer(part, targetFirePart, CFrame.new(0, 0, 0, 1, 0, 0, 0, 0, -1, 0, 1, 0))

                                shurikens[targetName] = {
                                    toy = shu,
                                    part = part,
                                }
                                setupDone[targetName] = true
                            end

                            if part and part:FindFirstChild("PartOwner") and part.PartOwner.Value ~= plr.Name then
                                SetNetOwner:FireServer(part, part.CFrame)
                            end

                            for _, toy in ipairs(targetInv:GetChildren()) do
                                if toy:FindFirstChild("StickyPart") then
                                    sp = toy.StickyPart
                                    po = sp:FindFirstChild("PartOwner")
                                    sw = sp:FindFirstChild("StickyWeld")

                                    if po and po.Value ~= "" and po.Value ~= target.Name then
                                        SetNetOwner:FireServer(sp, sp.CFrame)
                                        task.wait()
                                        if po.Value == plr.Name then
                                            sp.CFrame = CFrame.new(0, 0/0, 0)
                                        end
                                    end
                                    if sw and sw.Part1 then
                                        weldParent = sw.Part1.Parent
                                        if weldParent and weldParent ~= targetChar then
                                            SetNetOwner:FireServer(sp, sp.CFrame)
                                            task.wait()
                                            if po and po.Value == plr.Name then
                                                sp.CFrame = CFrame.new(0, 0/0, 0)
                                            end
                                        end
                                    end
                                end
                            end
                        end)
                    end
                end

                for targetName, data in pairs(shurikens) do
                    pcall(function()
                        if data.toy then
                            DestroyToy:FireServer(data.toy)
                        end
                    end)
                end
                shurikens = {}
                setupDone = {}
            end)
        end
    })

    refreshPlayers()
    Players.PlayerAdded:Connect(refreshPlayers)
    Players.PlayerRemoving:Connect(function()
        task.wait(0.1)
        refreshPlayers()
    end)
end

MapLeft = Tabs.MapBreak:AddLeftGroupbox("Map1", "map-pin")
MapRight = Tabs.MapBreak:AddRightGroupbox("Map2", "pin")


do
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")
    local Players = game:GetService("Players")

    local LocalPlayer = Players.LocalPlayer

    local SpawnToyRemote = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
    local DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    local SetNetworkOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
    local DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
    local StickyPartEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")

    local currentGrabbedHighlight = nil

    getgenv().getCurrentToyFolder2 = getgenv().getCurrentToyFolder2 or function()
        local inPlot = LocalPlayer:FindFirstChild("InPlot")
        if inPlot and inPlot.Value then
            local plots = Workspace:FindFirstChild("Plots")
            if plots then
                for i = 1, 5 do
                    local p = plots:FindFirstChild("Plot"..i)
                    if p and p:FindFirstChild("PlotSign") then
                        local sign = p.PlotSign
                        for _, name in ipairs({"ThisPlotsOwners","ThisPlotsOwner","ThisPlotOwners"}) do
                            local c = sign:FindFirstChild(name)
                            if c then
                                local owner = c:IsA("StringValue") and c or c:FindFirstChildOfClass("StringValue")
                                if owner and owner.Value == LocalPlayer.Name then
                                    return Workspace.PlotItems:FindFirstChild("Plot"..i)
                                end
                            end
                        end
                    end
                end
            end
        end
        return Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    end

    local function GetFolder()
        return getgenv().getCurrentToyFolder2()
    end

    local function HideShuriken(shuriken)
        pcall(function()
            for _, v in ipairs(shuriken:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.CanTouch = false
                    v.CanCollide = false
                    v.CanQuery = false
                    v.Transparency = 1
                end
            end
        end)
    end

    local function WaitForNewShuriken(timeout, activeCheck, existing)
        timeout = timeout or 5
        local folder = GetFolder()
        if not folder then return nil end

        local result = nil
        local conn = folder.ChildAdded:Connect(function(child)
            if child.Name == "ToolPencil" and not result and not (existing and existing[child]) then
                result = child
            end
        end)

        local start = tick()
        while not result and (tick() - start < timeout) do
            if activeCheck and not activeCheck() then
                conn:Disconnect()
                return nil
            end
            task.wait()
        end
        conn:Disconnect()
        return result
    end

    local function SpawnShuriken()
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        task.spawn(function()
            local cf = hrp.CFrame * CFrame.Angles(-0.605224, -0.321753, 0)
            SpawnToyRemote:InvokeServer("ToolPencil", cf, Vector3.new(0, 25.02, 0))
        end)
    end

    local function CreateAndOwnShuriken(activeCheck)
        local folder = GetFolder()
        local existing = {}
        if folder then
            for _, v in ipairs(folder:GetChildren()) do
                if v.Name == "ToolPencil" then existing[v] = true end
            end
        end

        while activeCheck == nil or activeCheck() do
            SpawnShuriken()
            local shuriken = WaitForNewShuriken(4, activeCheck, existing)
            if not shuriken then
                if activeCheck and not activeCheck() then return nil end
                task.wait(0.25)
                continue
            end

            task.wait(0.08)
            HideShuriken(shuriken)

            local soundPart = shuriken:FindFirstChild("SoundPart") or shuriken:FindFirstChildOfClass("BasePart")
            if not soundPart then
                pcall(function() DestroyToy:FireServer(shuriken) end)
                task.wait(0.2)
                continue
            end

            SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            task.wait(0.12)

            if activeCheck and not activeCheck() then
                pcall(function() DestroyToy:FireServer(shuriken) end)
                return nil
            end

            local owner = soundPart:FindFirstChild("PartOwner")
            if owner and owner.Value == LocalPlayer.Name then
                return shuriken
            else
                pcall(function() DestroyToy:FireServer(shuriken) end)
                task.wait(0.25)
            end
        end
        return nil
    end

    local function GetStickyPart(shuriken)
        return shuriken:FindFirstChild("StickyPart") or shuriken:FindFirstChildOfClass("BasePart")
    end

    local function StickShurikenToTarget(shuriken, target, cf)
        local stickyPart = GetStickyPart(shuriken)
        if not stickyPart or not target then return end
        pcall(function()
            DestroyGrabLine:FireServer(shuriken:FindFirstChildOfClass("BasePart"))
        end)
        pcall(function()
            StickyPartEvent:FireServer(table.unpack({
                [1] = stickyPart,
                [2] = target,
                [3] = cf,
            }))
        end)
    end

    local function GetGrabbedPart()
        local g = Workspace:FindFirstChild("GrabParts")
        if not g then return nil end
        local gp = g:FindFirstChild("GrabPart")
        if not gp then return nil end
        local weld = gp:FindFirstChild("WeldConstraint") or gp:FindFirstChild("Weld")
        if not weld then return nil end
        return weld.Part1 or nil
    end

    local function StickToGrabbedPartOnce()
        local grabbed = GetGrabbedPart()
        if not grabbed then
            return
        end

        if currentGrabbedHighlight then
            pcall(function() currentGrabbedHighlight:Destroy() end)
            currentGrabbedHighlight = nil
        end

        local highlight = Instance.new("SelectionBox")
        highlight.Adornee = grabbed
        highlight.Color3 = Color3.fromRGB(0, 250, 0)
        highlight.LineThickness = 0.03
        highlight.SurfaceTransparency = 0.8
        highlight.SurfaceColor3 = Color3.fromRGB(0, 250, 0)
        highlight.Parent = grabbed
        currentGrabbedHighlight = highlight

        local shuriken = CreateAndOwnShuriken(function() return true end)
        if not shuriken then
            pcall(function() highlight:Destroy() end)
            currentGrabbedHighlight = nil
            return
        end

        StickShurikenToTarget(shuriken, grabbed, CFrame.new(0/0, 0/0, 0/0))
        HideShuriken(shuriken)

        task.delay(3, function()
            if currentGrabbedHighlight and currentGrabbedHighlight == highlight then
                pcall(function() highlight:Destroy() end)
                currentGrabbedHighlight = nil
            end
        end)
    end


    MapRight:AddButton({
        Text = "Stick to Grabbed Part",
        Callback = function()
            task.spawn(StickToGrabbedPartOnce)
        end
    })

    MapRight:AddButton({
        Text = "Find Closest BaseGround",
        Callback = function()
            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then
                return
            end

            local baseGround = Workspace:FindFirstChild("Map") and Workspace.Map:FindFirstChild("BaseGround")
            if not baseGround then
                return
            end

            local children = baseGround:GetChildren()
            local closestPart = nil
            local closestIndex = nil
            local closestDist = math.huge
            local myPos = hrp.Position

            for i, child in ipairs(children) do
                if child:IsA("BasePart") then
                    local dist = (child.Position - myPos).Magnitude
                    if dist < closestDist then
                        closestDist = dist
                        closestPart = child
                        closestIndex = i
                    end
                end
            end

            if not closestPart then
                return
            end

            local pos = closestPart.Position
            local info = string.format(
                "Name: %s\nIndex: [%d]\nPosition: Vector3.new(%.2f, %.2f, %.2f)\nDistance: %.2f studs\nAccess: workspace.Map.BaseGround:GetChildren()[%d]",
                closestPart.Name,
                closestIndex,
                pos.X, pos.Y, pos.Z,
                closestDist,
                closestIndex
            )

            pcall(function()
                if setclipboard then
                    setclipboard(info)
                elseif toclipboard then
                    toclipboard(info)
                end
            end)

            pcall(function()
                local highlight = Instance.new("Highlight")
                highlight.Name = "ClosestBaseGroundESP"
                highlight.Adornee = closestPart
                highlight.FillColor = Color3.fromRGB(0, 255, 0)
                highlight.OutlineColor = Color3.fromRGB(255, 255, 0)
                highlight.FillTransparency = 0.4
                highlight.OutlineTransparency = 0
                highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                highlight.Parent = closestPart

                local billboard = Instance.new("BillboardGui")
                billboard.Name = "ClosestBaseGroundLabel"
                billboard.Adornee = closestPart
                billboard.Size = UDim2.new(0, 300, 0, 80)
                billboard.StudsOffset = Vector3.new(0, 5, 0)
                billboard.AlwaysOnTop = true
                billboard.Parent = closestPart

                local label = Instance.new("TextLabel")
                label.Size = UDim2.new(1, 0, 1, 0)
                label.BackgroundTransparency = 1
                label.Text = string.format("[%d] %s\n%.0f studs away", closestIndex, closestPart.Name, closestDist)
                label.TextColor3 = Color3.fromRGB(0, 255, 0)
                label.TextStrokeTransparency = 0
                label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
                label.Font = Enum.Font.GothamBold
                label.TextScaled = true
                label.Parent = billboard

                local att0 = Instance.new("Attachment", hrp)
                local att1 = Instance.new("Attachment", closestPart)

                local beam = Instance.new("Beam")
                beam.Attachment0 = att0
                beam.Attachment1 = att1
                beam.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0), Color3.fromRGB(255, 255, 0))
                beam.Width0 = 0.5
                beam.Width1 = 0.5
                beam.FaceCamera = true
                beam.LightEmission = 1
                beam.Transparency = NumberSequence.new(0)
                beam.Parent = hrp

                task.delay(8, function()
                    if highlight then highlight:Destroy() end
                    if billboard then billboard:Destroy() end
                    if beam then beam:Destroy() end
                    if att0 then att0:Destroy() end
                    if att1 then att1:Destroy() end
                end)
            end)
        end
    })
end

do
   
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")

    local LocalPlayer = Players.LocalPlayer

    local SpawnToyRemoteFunction = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
    local GrabEvent = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
    local DestroyToy = ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy")
    local DestroyGrabLine = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("DestroyGrabLine")
    local StickyPartEvent = ReplicatedStorage:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")

    local Character, HumanoidRootPart
    local folder
    local activePencils = {}

    -- =========================================================================
    -- HELPER FUNCTIONS
    -- =========================================================================
    local function GetHighestGrabbableHitbox(blob)
        local highest = nil
        local highestY = -math.huge

        for _, v in ipairs(blob:GetDescendants()) do
            if v.Name == "GrabbableHitbox" and v:IsA("BasePart") then
                local y = v.Position.Y
                if y > highestY then
                    highestY = y
                    highest = v
                end
            end
        end
        return highest
    end

    local function SetupCharacter(char)
        Character = char
        HumanoidRootPart = char:WaitForChild("HumanoidRootPart")
        folder = Workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys")
    end

    SetupCharacter(LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait())
    LocalPlayer.CharacterAdded:Connect(SetupCharacter)

    local function SpawnPencil()
        if not HumanoidRootPart then return end
        local pos = HumanoidRootPart.CFrame * CFrame.Angles(-0.605224, -0.321753, 0)

        task.spawn(function()
            pcall(function()
                SpawnToyRemoteFunction:InvokeServer("ToolPencil", pos, Vector3.zero)
            end)
        end)
    end

    local function WaitForNewPencil(timeout)
        timeout = timeout or 6
        local result = nil
        local done = false
        local connection

        if not folder then return nil end

        connection = folder.ChildAdded:Connect(function(child)
            if child.Name == "ToolPencil" then
                result = child
                done = true
            end
        end)

        local start = tick()
        while not done and tick() - start < timeout do
            task.wait()
        end

        connection:Disconnect()
        return result
    end

    local function ApplyPermanentLift(pencil)
        local soundPart = pencil:FindFirstChild("SoundPart")
        if not soundPart then return end

        for _, v in ipairs(soundPart:GetChildren()) do
            if v:IsA("LinearVelocity") then
                v:Destroy()
            end
        end

        local att = Instance.new("Attachment")
        att.Parent = soundPart
    end

    local function CreateOnePencil()
        if not HumanoidRootPart then return false end

        SpawnPencil()
        local pencil = WaitForNewPencil()
        if not pencil then
            task.wait(0.2)
            return false
        end

        task.wait(0.19)
        local stickyPart = pencil:FindFirstChild("StickyPart")
        if stickyPart then
            stickyPart.CanTouch = false
        end

        local soundPart = pencil:FindFirstChild("SoundPart")
        if not soundPart then return false end

        GrabEvent:FireServer(soundPart, soundPart.CFrame)
        task.wait(0.15)

        local owner = soundPart:FindFirstChild("PartOwner")
        if owner and owner.Value == LocalPlayer.Name then
            table.insert(activePencils, pencil)
            ApplyPermanentLift(pencil)
            return true
        else
            pcall(function()
                DestroyToy:FireServer(pencil)
            end)
        end

        task.wait(0.2)
        return false
    end

    -- =========================================================================
    -- MAIN FUNCTIONS
    -- =========================================================================
    function KickPlayerOnBlob(blob)
        if not blob then
            Library:Notify({ Title = "LOLL", Description = "Blob not found!", Duration = 3 })
            return
        end

        local target = 1
        local created = 0

        while created < target do
            if CreateOnePencil() then
                created = created + 1
            end
            task.wait(0.4)
        end

        local hitbox = GetHighestGrabbableHitbox(blob)
        if not hitbox then
            Library:Notify({ Title = "LOLL", Description = "Hitbox not found!", Duration = 3 })
            return
        end

        for _, pencil in ipairs(activePencils) do
            if pencil and pencil.Parent then
                pcall(function()
                    DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))
                    StickyPartEvent:FireServer(pencil.StickyPart, hitbox, CFrame.new(1e45, math.huge, 1e98))
                end)
            end
        end

        activePencils = {}
        Library:Notify({ Title = "LOLL", Description = "Kick Player on Blob Executed!", Duration = 3 })
    end

    function KickPlayerOnBlob12Kunai(blob)
        if not blob then
            Library:Notify({ Title = "LOLL", Description = "Blob not found!", Duration = 3 })
            return
        end

        local target = 12
        local created = 0

        while created < target do
            if CreateOnePencil() then
                created = created + 1
            end
            task.wait(0.4)
        end

        local hitbox = GetHighestGrabbableHitbox(blob)
        if not hitbox then
            Library:Notify({ Title = "LOLL", Description = "Hitbox not found!", Duration = 3 })
            return
        end

        for _, pencil in ipairs(activePencils) do
            if pencil and pencil.Parent then
                pcall(function()
                    DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))
                    StickyPartEvent:FireServer(pencil.StickyPart, hitbox, CFrame.new(1e45, math.huge, 1e98))
                end)
            end
        end

        activePencils = {}
        Library:Notify({ Title = "LOLL", Description = "Kick Player on Blob (12 Kunai) Executed!", Duration = 3 })
    end

    function BreakMap()
        local target = 1
        local created = 0

        while created < target do
            if CreateOnePencil() then
                created = created + 1
            end
            task.wait(0.4)
        end

        local baseGround = Workspace:FindFirstChild("Map")
        if baseGround then
            baseGround = baseGround:FindFirstChild("BaseGround")
        end

        if not baseGround then
            Library:Notify({ Title = "LOLL", Description = "BaseGround not found!", Duration = 3 })
            return
        end

        local children = baseGround:GetChildren()
        local targetPart = children[154]

        if not targetPart then
            Library:Notify({ Title = "LOLL", Description = "Target part not found!", Duration = 3 })
            return
        end

        for _, pencil in ipairs(activePencils) do
            if pencil and pencil.Parent then
                pcall(function()
                    DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))
                    StickyPartEvent:FireServer(pencil.StickyPart, targetPart, CFrame.new(1e45, math.huge, 1e98))
                end)
            end
        end

        activePencils = {}
        Library:Notify({ Title = "LOLL", Description = "Map Broken!", Duration = 3 })
    end

    function BreakPlot(...)
        local plots = { ... }
        local target = #plots

        if target == 0 then
            Library:Notify({ Title = "LOLL", Description = "No plots selected!", Duration = 3 })
            return
        end

        local created = 0
        local pencils = {}

        while created < target do
            if CreateOnePencil() then
                created = created + 1
                local pencil = activePencils[#activePencils]
                table.insert(pencils, pencil)
            end
            task.wait(0.4)
        end

        for i, plot in ipairs(plots) do
            local pencil = pencils[i]
            local plotArea = plot:FindFirstChild("PlotArea")

            if not plotArea then
                Library:Notify({ Title = "LOLL", Description = "PlotArea not found for plot " .. i, Duration = 3 })
                continue
            end

            if pencil and pencil.Parent then
                pcall(function()
                    DestroyGrabLine:FireServer(pencil:FindFirstChildOfClass("BasePart"))
                    StickyPartEvent:FireServer(pencil.StickyPart, plotArea, CFrame.new(0/0, math.huge, 0/0))
                end)
            end
        end

        activePencils = {}
        Library:Notify({ Title = "LOLL", Description = "Plot(s) Broken!", Duration = 3 })
    end
 
    -- Break All Plots
    MapRight:AddButton({
        Text = "Break All Plots",
        Callback = function()
            local plots = Workspace:FindFirstChild("Plots")
            if not plots then
                Library:Notify({ Title = "LOLL", Description = "Plots not found!", Duration = 3 })
                return
            end

            local plotList = {}
            for _, plot in ipairs(plots:GetChildren()) do
                if plot:IsA("Model") then
                    table.insert(plotList, plot)
                end
            end

            if #plotList == 0 then
                Library:Notify({ Title = "LOLL", Description = "No plots found!", Duration = 3 })
                return
            end

            BreakPlot(unpack(plotList))
        end
    })
end

do

    local SelectedPlot = "1"
    local PlotOptions = {"Plot1 (Green)", "Plot2 (Pink)", "Plot3 (Purple)", "Plot4 (Blue)", "Plot5 (Yellow)"}

    MapRight:AddDropdown("PlotBreaker_SelectPlot", {
        Text = "Select Plot",
        Values = PlotOptions,
        Default = "Plot1 (Green)",
        Callback = function(Value)
            SelectedPlot = Value:match("Plot(%d)") or "1"
        end
    })

    local function getHRP()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            return LocalPlayer.Character.HumanoidRootPart
        else
            local character = LocalPlayer.CharacterAdded:Wait()
            return character:WaitForChild("HumanoidRootPart")
        end
    end

    local function getOrCreateShuriken()
        local playersInPlots = Workspace:FindFirstChild("PlotItems") and Workspace.PlotItems:FindFirstChild("PlayersInPlots")
        if playersInPlots and playersInPlots:FindFirstChild(LocalPlayer.Name) then
            Library:Notify({Title = "Break Plot", Description = "You are in safe zone!", Duration = 3})
            return nil
        end
        
        local inv = Workspace:FindFirstChild(LocalPlayer.Name.."SpawnedInToys")
        if not inv then return nil end
        
        local myHRP = getHRP()
        for _, obj in pairs(inv:GetChildren()) do
            if (obj.Name == "NinjaShuriken" or obj.Name == "Noclipped") and obj:FindFirstChild("StickyPart") and obj:FindFirstChild("SoundPart") then
                if myHRP and (obj.StickyPart.Position - myHRP.Position).Magnitude <= 12 then
                    return obj
                end
            end
        end
        
        local currentHRP = getHRP()
        if not currentHRP then return nil end
        
        local toyAdded
        local shur = nil
        toyAdded = inv.ChildAdded:Connect(function(child)
            if child.Name == "NinjaShuriken" then
                shur = child
                toyAdded:Disconnect()
            end
        end)
        
        ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("NinjaShuriken", currentHRP.CFrame * CFrame.new(5, 8, 20), Vector3.new(0, 0, 0))
        
        local startTime = tick()
        repeat
            if shur and shur:FindFirstChild("StickyPart") and shur:FindFirstChild("SoundPart") then
                return shur
            end
            task.wait(0.01)
        until tick() - startTime > 0.1
        
        return inv:FindFirstChild("NinjaShuriken")
    end

    MapRight:AddButton({
        Text = "Break Plot [Shuriken]",
        Callback = function()
            local shur = getOrCreateShuriken()
            if not shur then return end
            
            local soundPart = shur:FindFirstChild("SoundPart")
            local stickyPart = shur:FindFirstChild("StickyPart")
            if not stickyPart then return end
            
            if soundPart then
                local setOwner = ReplicatedStorage:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
                for i = 1, 20 do
                    setOwner:FireServer(soundPart, soundPart.CFrame)
                    if soundPart:FindFirstChild("PartOwner") and soundPart.PartOwner.Value == LocalPlayer.Name then break end
                end
            end
            
            for _, obj in pairs(shur:GetChildren()) do
                if obj:IsA("BasePart") then
                    obj.CanTouch = false
                    obj.CanCollide = false
                    obj.CanQuery = false
                    if obj.Transparency == 0 then obj.Transparency = 1 end
                end
            end
            shur.Name = "Noclipped"
            
            local plot = Workspace.Plots:FindFirstChild("Plot"..SelectedPlot)
            if not plot then 
                Library:Notify({Title = "Error", Description = "Plot not found!", Duration = 3})
                return 
            end
            local plotArea = plot:FindFirstChild("PlotArea")
            if not plotArea then 
                Library:Notify({Title = "Error", Description = "PlotArea not found!", Duration = 3})
                return 
            end
            
            ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(stickyPart, plotArea, CFrame.new(1099511627776, 1099511627776, 1099511627776, 1, 0, 0, 0, 1, 0, 0, 0, 1))
            Library:Notify({Title = "Success", Description = "Plot " .. SelectedPlot .. " broken!", Duration = 3})
        end
    })
end

do

    MapLeft:AddButton({
        Text = "Break Outer UFO",
        Callback = function()
            pcall(function()
                local w = Workspace
                local me = LocalPlayer
                local BackPack = w[me.Name .. 'SpawnedInToys']
                
                -- หา Outer UFO
                local ufoModel = w:FindFirstChild("Map")
                if ufoModel then
                    ufoModel = ufoModel:FindFirstChild("AlwaysHereTweenedObjects")
                    if ufoModel then
                        ufoModel = ufoModel:FindFirstChild("OuterUFO")
                    end
                end
                
                if not ufoModel then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Outer UFO not found!",
                        Duration = 3
                    })
                    return
                end
                
                local UFO = ufoModel:FindFirstChild("Object")
                if not UFO then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "UFO Object not found!",
                        Duration = 3
                    })
                    return
                end
                
                local body = UFO:FindFirstChild("ObjectModel")
                if body then
                    body = body:FindFirstChild("Body")
                end
                
                if not body then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "UFO Body not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- Spawn Shurikens 10 อัน
                local function spawnShurikens(count)
                    local spawnCFrame = me.Character and me.Character:FindFirstChild("HumanoidRootPart")
                    if not spawnCFrame then return end
                    spawnCFrame = spawnCFrame.CFrame * CFrame.new(0, 3, 0)
                    
                    for i = 1, count do
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                "NinjaShuriken",
                                spawnCFrame,
                                Vector3.zero
                            )
                        end)
                        task.wait(0.05)
                    end
                end
                
                spawnShurikens(10)
                task.wait(1)
                
                -- ติด Shuriken เข้ากับ UFO
                for i = 1, 10 do
                    local shur = BackPack and BackPack:FindFirstChild("NinjaShuriken")
                    if shur then
                        local stickyPart = shur:FindFirstChild("StickyPart")
                        if stickyPart then
                            shur.Name = tostring(i)
                            pcall(function()
                                ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(
                                    stickyPart,
                                    body,
                                    CFrame.Angles(0, 0, 0)
                                )
                            end)
                        end
                    end
                    task.wait(0.1)
                end
                
                -- วนลูปย้ายตำแหน่ง
                for i = 1, 100 do
                    pcall(function()
                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(body, body.CFrame)
                    end)
                    task.wait()
                end
                
                -- ลบ Attachment
                local attach = body:FindFirstChild("ObjectModelAttachment")
                if attach then
                    attach:Destroy()
                end
                
                -- ลบ AlignPosition และ AlignOrientation
                local followPart = ufoModel:FindFirstChild("FollowThisPart")
                if followPart then
                    local alignPos = followPart:FindFirstChild("AlignPosition")
                    local alignRot = followPart:FindFirstChild("AlignOrientation")
                    
                    if alignPos then
                        alignPos.Attachment0 = nil
                    end
                    if alignRot then
                        alignRot.Attachment0 = nil
                    end
                end
                
                Library:Notify({
                    Title = "LOLL",
                    Description = "Outer UFO Broken!",
                    Duration = 3
                })
            end)
        end
    })

    MapLeft:AddButton({
        Text = "Break Inner UFO",
        Callback = function()
            pcall(function()
                local w = Workspace
                local me = LocalPlayer
                local BackPack = w[me.Name .. 'SpawnedInToys']
                
                -- หา Inner UFO
                local ufoModel = w:FindFirstChild("Map")
                if ufoModel then
                    ufoModel = ufoModel:FindFirstChild("AlwaysHereTweenedObjects")
                    if ufoModel then
                        ufoModel = ufoModel:FindFirstChild("InnerUFO")
                    end
                end
                
                if not ufoModel then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Inner UFO not found!",
                        Duration = 3
                    })
                    return
                end
                
                local UFO = ufoModel:FindFirstChild("Object")
                if not UFO then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "UFO Object not found!",
                        Duration = 3
                    })
                    return
                end
                
                local body = UFO:FindFirstChild("ObjectModel")
                if body then
                    body = body:FindFirstChild("Body")
                end
                
                if not body then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "UFO Body not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- Spawn Shurikens 10 อัน
                local function spawnShurikens(count)
                    local spawnCFrame = me.Character and me.Character:FindFirstChild("HumanoidRootPart")
                    if not spawnCFrame then return end
                    spawnCFrame = spawnCFrame.CFrame * CFrame.new(0, 3, 0)
                    
                    for i = 1, count do
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                "NinjaShuriken",
                                spawnCFrame,
                                Vector3.zero
                            )
                        end)
                        task.wait(0.05)
                    end
                end
                
                spawnShurikens(10)
                task.wait(1)
                
                -- ติด Shuriken เข้ากับ UFO
                for i = 1, 10 do
                    local shur = BackPack and BackPack:FindFirstChild("NinjaShuriken")
                    if shur then
                        local stickyPart = shur:FindFirstChild("StickyPart")
                        if stickyPart then
                            shur.Name = tostring(i)
                            pcall(function()
                                ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(
                                    stickyPart,
                                    body,
                                    CFrame.Angles(0, 0, 0)
                                )
                            end)
                        end
                    end
                    task.wait(0.1)
                end
                
                -- วนลูปย้ายตำแหน่ง
                for i = 1, 100 do
                    pcall(function()
                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(body, body.CFrame)
                    end)
                    task.wait()
                end
                
                -- ลบ Attachment
                local attach = body:FindFirstChild("ObjectModelAttachment")
                if attach then
                    attach:Destroy()
                end
                
                -- ลบ AlignPosition และ AlignOrientation
                local followPart = ufoModel:FindFirstChild("FollowThisPart")
                if followPart then
                    local alignPos = followPart:FindFirstChild("AlignPosition")
                    local alignRot = followPart:FindFirstChild("AlignOrientation")
                    
                    if alignPos then
                        alignPos.Attachment0 = nil
                    end
                    if alignRot then
                        alignRot.Attachment0 = nil
                    end
                end
                
                Library:Notify({
                    Title = "LOLL",
                    Description = "Inner UFO Broken!",
                    Duration = 3
                })
            end)
        end
    })

    MapLeft:AddButton({
        Text = "Break CaveCart",
        Callback = function()
            pcall(function()
                local w = Workspace
                local me = LocalPlayer
                local BackPack = w[me.Name .. 'SpawnedInToys']
                
                -- หา CaveCart
                local caveModel = w:FindFirstChild("Map")
                if caveModel then
                    caveModel = caveModel:FindFirstChild("AlwaysHereTweenedObjects")
                    if caveModel then
                        caveModel = caveModel:FindFirstChild("CaveCart")
                    end
                end
                
                if not caveModel then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "CaveCart not found!",
                        Duration = 3
                    })
                    return
                end
                
                local obj = caveModel:FindFirstChild("Object")
                if not obj then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "CaveCart Object not found!",
                        Duration = 3
                    })
                    return
                end
                
                local model = obj:FindFirstChild("ObjectModel")
                if not model then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "CaveCart ObjectModel not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- หา Target (ส่วนที่ 13 ของ ObjectModel)
                local children = model:GetChildren()
                local target = children[13]
                
                if not target then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "CaveCart target part not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- Spawn Shurikens 10 อัน
                local function spawnShurikens(count)
                    local spawnCFrame = me.Character and me.Character:FindFirstChild("HumanoidRootPart")
                    if not spawnCFrame then return end
                    spawnCFrame = spawnCFrame.CFrame * CFrame.new(0, 3, 0)
                    
                    for i = 1, count do
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                "NinjaShuriken",
                                spawnCFrame,
                                Vector3.zero
                            )
                        end)
                        task.wait(0.05)
                    end
                end
                
                spawnShurikens(10)
                task.wait(1)
                
                -- ติด Shuriken เข้ากับ target
                for i = 1, 10 do
                    local shur = BackPack and BackPack:FindFirstChild("NinjaShuriken")
                    if shur then
                        local stickyPart = shur:FindFirstChild("StickyPart")
                        if stickyPart then
                            shur.Name = tostring(i)
                            pcall(function()
                                ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(
                                    stickyPart,
                                    target,
                                    CFrame.Angles(0, 0, 0)
                                )
                            end)
                        end
                    end
                    task.wait(0.1)
                end
                
                -- วนลูปย้ายตำแหน่ง
                for i = 1, 100 do
                    pcall(function()
                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(target, target.CFrame)
                    end)
                    task.wait()
                end
                
                -- ลบ Attachment
                local attach = target:FindFirstChild("ObjectModelAttachment")
                if attach then
                    attach:Destroy()
                end
                
                -- ลบ AlignPosition และ AlignOrientation
                local followPart = obj:FindFirstChild("FollowThisPart")
                if followPart then
                    local alignPos = followPart:FindFirstChild("AlignPosition")
                    local alignRot = followPart:FindFirstChild("AlignOrientation")
                    
                    if alignPos then
                        alignPos.Attachment0 = nil
                    end
                    if alignRot then
                        alignRot.Attachment0 = nil
                    end
                end
                
                Library:Notify({
                    Title = "LOLL",
                    Description = "CaveCart Broken!",
                    Duration = 3
                })
            end)
        end
    })
end

do
    MapLeft:AddButton({
        Text = "Break Train",
        Callback = function()
            pcall(function()
                local w = Workspace
                local me = LocalPlayer
                local BackPack = w[me.Name .. 'SpawnedInToys']
                
                -- หา Train
                local trainModel = w:FindFirstChild("Map")
                if trainModel then
                    trainModel = trainModel:FindFirstChild("AlwaysHereTweenedObjects")
                    if trainModel then
                        trainModel = trainModel:FindFirstChild("Train")
                    end
                end
                
                if not trainModel then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Train not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- หา Object ใน Train
                local obj = trainModel:FindFirstChild("Object")
                if not obj then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Train Object not found!",
                        Duration = 3
                    })
                    return
                end
                
                local model = obj:FindFirstChild("ObjectModel")
                if not model then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Train ObjectModel not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- หา Target ส่วนที่น่าจะเป็นตัวรถไฟ (ส่วนที่ 2 หรือ 3)
                local children = model:GetChildren()
                local target = children[2] or children[3] or children[1]
                
                if not target or not target:IsA("BasePart") then
                    Library:Notify({
                        Title = "LOLL",
                        Description = "Train target part not found!",
                        Duration = 3
                    })
                    return
                end
                
                -- Spawn Shurikens 10 อัน
                local function spawnShurikens(count)
                    local spawnCFrame = me.Character and me.Character:FindFirstChild("HumanoidRootPart")
                    if not spawnCFrame then return end
                    spawnCFrame = spawnCFrame.CFrame * CFrame.new(0, 3, 0)
                    
                    for i = 1, count do
                        pcall(function()
                            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer(
                                "NinjaShuriken",
                                spawnCFrame,
                                Vector3.zero
                            )
                        end)
                        task.wait(0.05)
                    end
                end
                
                spawnShurikens(10)
                task.wait(1)
                
                -- ติด Shuriken เข้ากับ Train
                for i = 1, 10 do
                    local shur = BackPack and BackPack:FindFirstChild("NinjaShuriken")
                    if shur then
                        local stickyPart = shur:FindFirstChild("StickyPart")
                        if stickyPart then
                            shur.Name = tostring(i)
                            pcall(function()
                                ReplicatedStorage.PlayerEvents.StickyPartEvent:FireServer(
                                    stickyPart,
                                    target,
                                    CFrame.Angles(0, 0, 0)
                                )
                            end)
                        end
                    end
                    task.wait(0.1)
                end
                
                -- วนลูปย้ายตำแหน่ง Train
                for i = 1, 100 do
                    pcall(function()
                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(target, target.CFrame)
                    end)
                    task.wait()
                end
                
                -- ลบ Attachment
                local attach = target:FindFirstChild("ObjectModelAttachment")
                if attach then
                    attach:Destroy()
                end
                
                -- ลบ AlignPosition และ AlignOrientation
                local followPart = obj:FindFirstChild("FollowThisPart")
                if followPart then
                    local alignPos = followPart:FindFirstChild("AlignPosition")
                    local alignRot = followPart:FindFirstChild("AlignOrientation")
                    
                    if alignPos then
                        alignPos.Attachment0 = nil
                    end
                    if alignRot then
                        alignRot.Attachment0 = nil
                    end
                end
                
                Library:Notify({
                    Title = "LOLL",
                    Description = "Train Broken!",
                    Duration = 3
                })
            end)
        end
    })
end

MenuGroup = Tabs.UISettings:AddLeftGroupbox("Menu", "user-lock")
BackGroup = Tabs.UISettings:AddRightGroupbox("Background", "mail")

MenuGroup:AddCheckbox("KeybindMenuOpen", {
    Text = "Open Keybind Menu",
    Default = Library.KeybindFrame.Visible,
    Callback = function(value)
        Library.KeybindFrame.Visible = value
    end,
})

MenuGroup:AddCheckbox("ShowCustomCursor", {
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
        DPI = tonumber(Value)
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

bgData = {
    Enabled = true,
    Image = "0",
    Last = nil,
    Transparency = 0.15
}

bgToken = 0

local function formatImage(id)
    id = tostring(id or ""):gsub("%s+", "")
    if id == "" then
        return "rbxassetid://0"
    end
    if id:find("rbxassetid://") then
        return id
    end
    return "rbxassetid://" .. id
end

local function applyBackground()
    bgToken = bgToken + 1
    current = bgToken
    
    task.delay(0.05, function()
        if current ~= bgToken then
            return
        end
        
        image = bgData.Enabled and formatImage(bgData.Image) or "rbxassetid://0"
        
        if bgData.Last == image then
            return
        end
        
        bgData.Last = image
        
        pcall(function()
            Window:SetBackgroundImage(image)
            Window:SetBackgroundTransparency(bgData.Transparency)
        end)
    end)
end

BackGroup:AddCheckbox("EnableBackground", {
    Text = "Enable Background",
    Default = true,
    Callback = function(Value)
        bgData.Enabled = Value
        applyBackground()
    end
})

BackGroup:AddInput("BackgroundAsset", {
    Text = "Asset ID",
    Default = "0",
    Callback = function(Value)
        bgData.Image = tostring(Value)
        applyBackground()
    end
})

BackGroup:AddButton({
    Text = "Clear Background",
    Callback = function()
        bgData.Image = "0"
        applyBackground()
    end
})

applyBackground()

Library.ToggleKeybind = Options.MenuKeybind

ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({ "MenuKeybind" })
ThemeManager:SetFolder("9rr")
SaveManager:SetFolder("9rr/Configs")
SaveManager:SetSubFolder("game-config")

ThemeManager:ApplyToTab(Tabs.UISettings)
SaveManager:BuildConfigSection(Tabs.UISettings)

Window:SetCornerRadius(20)
Library.ShowCustomCursor = true
watermark = Library:AddDraggableLabel("•9rr | 0 FPS | 0 ms")

task.spawn(function()
    while task.wait(0.5) do
        fps = math.floor(1 / game:GetService("RunService").RenderStepped:Wait())
        ping = math.floor(game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue())
        watermark:SetText(string.format("•9rr | %d FPS | %d ms", fps, ping))
    end
end)

end

print("9rr Loaded, Sir3n and Oat is the goat frfr")

end