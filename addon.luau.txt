local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Siro-script/FtaP.90hub/refs/heads/main/Orion"))()

local Window = OrionLib:MakeWindow({Name = "Silent Kick All Script", HidePremiumNotification = false})

-- Services
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

-- Helper Functions
function getCurrentCharacter()
    return LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
end

function getBlobman()
    local blobFolder = Workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if blobFolder then
        return blobFolder:FindFirstChild("CreatureBlobman")
    end
    return nil
end

function getPlayerList()
    local players = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(players, player.Name)
        end
    end
    return players
end

function snapBlobTo(blob, targetCFrame)
    local hrp = blob:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    hrp.CFrame = targetCFrame

    local att0 = hrp:FindFirstChild("BlobAttachment") or Instance.new("Attachment", hrp)
    att0.Name = "BlobAttachment"

    local targetPart = workspace:FindFirstChild("BlobTarget") or Instance.new("Part")
    targetPart.Anchored = true
    targetPart.CanCollide = false
    targetPart.Size = Vector3.new(1,1,1)
    targetPart.Transparency = 1
    targetPart.CFrame = targetCFrame
    targetPart.Name = "BlobTarget"
    targetPart.Parent = workspace

    local att1 = targetPart:FindFirstChild("BlobTargetAttachment") or Instance.new("Attachment", targetPart)
    att1.Name = "BlobTargetAttachment"

    local ap = hrp:FindFirstChild("BlobAlignPos") or Instance.new("AlignPosition", hrp)
    ap.Name = "BlobAlignPos"
    ap.Attachment0 = att0
    ap.Attachment1 = att1
    ap.RigidityEnabled = true
    ap.Responsiveness = 300
    ap.MaxForce = math.huge

    local ao = hrp:FindFirstChild("BlobAlignOri") or Instance.new("AlignOrientation", hrp)
    ao.Name = "BlobAlignOri"
    ao.Attachment0 = att0
    ao.Attachment1 = att1
    ao.Responsiveness = 300
    ao.MaxTorque = math.huge
    ao.RigidityEnabled = true
end

function releaseBlobAlign(blob)
    local hrp = blob:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    if hrp:FindFirstChild("BlobAlignPos") then hrp.BlobAlignPos:Destroy() end
    if hrp:FindFirstChild("BlobAlignOri") then hrp.BlobAlignOri:Destroy() end
    if hrp:FindFirstChild("BlobAttachment") then hrp.BlobAttachment:Destroy() end
    local targetPart = workspace:FindFirstChild("BlobTarget")
    if targetPart then targetPart:Destroy() end
end

-- UI Tabs
local BlobTab = Window:MakeTab({
    Name = "Blob Features",
    Icon = "rbxassetid://4335112704",
    PremiumOnly = false
})

local SilentKickSection = BlobTab:AddSection({
    Name = "Silent Kick All"
})

-- Silent Kick All Button
SilentKickSection:AddButton({
    Name = "Execute Silent Kick All",
    Callback = function()
        local blob = getBlobman()
        if not blob then
            OrionLib:MakeNotification({
                Name = "Error",
                Content = "Blobman not found!",
                Image = "rbxassetid://4335112704",
                Time = 3
            })
            return
        end

        local players = getPlayerList()
        if #players == 0 then
            OrionLib:MakeNotification({
                Name = "Error",
                Content = "No players to kick!",
                Image = "rbxassetid://4335112704",
                Time = 3
            })
            return
        end

        OrionLib:MakeNotification({
            Name = "Starting",
            Content = "Silent kick all in progress...",
            Image = "rbxassetid://4335112704",
            Time = 3
        })

        local grab = blob.BlobmanSeatAndOwnerScript.CreatureGrab
        local release = blob.BlobmanSeatAndOwnerScript.CreatureRelease
        local drop = blob.BlobmanSeatAndOwnerScript.CreatureDrop
        local set = ReplicatedStorage.GrabEvents.SetNetworkOwner
        local del = ReplicatedStorage.GrabEvents.DestroyGrabLine

        local detector = blob.LeftDetector
        local weld = blob.LeftDetector.LeftWeld
        local localHrp = getCurrentCharacter().HumanoidRootPart

        -- Step 1: Grab & Release for all players
        for _, playerName in ipairs(players) do
            local player = Players:FindFirstChild(playerName)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = player.Character.HumanoidRootPart
                snapBlobTo(blob, targetHrp.CFrame)
                task.wait(0.15)
                grab:FireServer(blob.RightDetector, targetHrp, blob.RightDetector.RightWeld)
                release:FireServer(blob.RightDetector.RightWeld)
                task.wait(0)
            end
        end

        -- Step 2: Move Blob to map center
        snapBlobTo(blob, CFrame.new(Vector3.new(0,100,0)))
        task.wait(0.05)

        -- Step 3: Move all targets above Blob (Y+20)
        for _, playerName in ipairs(players) do
            local player = Players:FindFirstChild(playerName)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = player.Character.HumanoidRootPart
                local blobHrp = blob.HumanoidRootPart
                targetHrp.CFrame = CFrame.new(blobHrp.Position.X, blobHrp.Position.Y + 20, blobHrp.Position.Z)
            end
        end
        task.wait(0.1)

        -- Step 4: SetNetworkOwner + DestroyGrabLine + GrabDrop
        for _, playerName in ipairs(players) do
            local player = Players:FindFirstChild(playerName)
            if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local targetHrp = player.Character.HumanoidRootPart
                set:FireServer(targetHrp, targetHrp.CFrame)
                del:FireServer(targetHrp)

                -- Execute Silent Kick
                grab:FireServer(detector, localHrp, weld)
                grab:FireServer(detector, targetHrp, weld)
                drop:FireServer(weld, targetHrp.RootAttachment)
            end
        end
        
        task.wait(3)
        releaseBlobAlign(blob)
        
        OrionLib:MakeNotification({
            Name = "Complete",
            Content = "Silent kick all executed!",
            Image = "rbxassetid://4335112704",
            Time = 3
        })
    end
})

-- Settings Tab
local SettingsTab = Window:MakeTab({
    Name = "Settings",
    Icon = "rbxassetid://4335109659",
    PremiumOnly = false
})

local SettingsSection = SettingsTab:AddSection({
    Name = "UI Settings"
})

SettingsSection:AddButton({
    Name = "Destroy UI",
    Callback = function()
        OrionLib:Destroy()
    end
})

OrionLib:Init()
