--https://github.com/Oxwoey/FTAP-Module srry vex
--rename by ai ((( 
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local GrabEvents = ReplicatedStorage:WaitForChild("GrabEvents")
local Debris = game:GetService("Debris")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local SpawnedInToys = Workspace:WaitForChild(Players.LocalPlayer.Name .. "SpawnedInToys")
local GameCorrectionEvents = SpawnedInToys:WaitForChild("GameCorrectionEvents")
local CharacterEvents = GameCorrectionEvents:WaitForChild("CharacterEvents")
local RagdollRemote = CharacterEvents:WaitForChild("RagdollRemote")
local MenuToys = CharacterEvents:WaitForChild("MenuToys")
local SetNetworkOwner = MenuToys:WaitForChild("SetNetworkOwner")
local Struggle = MenuToys:WaitForChild("Struggle")
local CreateGrabLine = MenuToys:WaitForChild("CreateGrabLine")
local StopAllVelocity = MenuToys:WaitForChild("StopAllVelocity")
local DestroyGrabLine = MenuToys:WaitForChild("DestroyGrabLine")
local DestroyToy = MenuToys:WaitForChild("DestroyToy")
local Look = MenuToys:WaitForChild("Look")
local LocalPlayer = Players.LocalPlayer
local HumanoidRootPart = LocalPlayer.Character.HumanoidRootPart

if LocalPlayer.Character then
    LocalPlayer.CharacterAdded:Connect(function(character)
        local humanoid = character:WaitForChild("Humanoid")
    end)
end

local CurrentCamera = workspace.CurrentCamera
local FallenPartsDestroyHeight = workspace.FallenPartsDestroyHeight

local Module = {}
Module.antiGrabCoroutine = nil
Module.antiExplosionConnection = nil
Module.characterAddedConn = nil
Module.burnPart = nil
Module.burnGrabCoroutine = nil
Module.RadiationGrabCoroutine = nil
Module.PoisonGrabCoroutine = nil
Module.NoClipGrabCoroutine = nil
Module.auraRadius = 18
Module.radiactiveAuraCoroutine = nil
Module.PoisonAuraCoroutine = nil

local ConfigWebhook = {}
ConfigWebhook.NameHub = ""
ConfigWebhook.WebhookEnabled = nil
ConfigWebhook.ExecuteLogSecret = nil
ConfigWebhook.WebhookLink = ""
Module.ConfigWebhook = ConfigWebhook
Module.Blacklist = ConfigWebhook

Module.SetSettings = nil
Module.SendWebhook = nil

local getgenv = getgenv or function() return _G end
getgenv().AntiGrab = nil
getgenv().AntiExploin = nil
getgenv().AntiBlobman = nil
getgenv().AntiVoid = nil
getgenv().AntiLag = nil
getgenv().AntiBurn = nil
getgenv().SuperStrength = nil
getgenv().ValueStrength = 50
getgenv().BurnGrab = nil
getgenv().PoisonGrab = nil
getgenv().RadiationGrab = nil
getgenv().NoClipGrab = nil
getgenv().KickAura = nil
getgenv().MagneticAura = nil
getgenv().RadiationAura = nil
getgenv().PoisonAura = nil
getgenv().whiteListEnabled = nil

local spawn_toy = nil
local getDescendantParts = nil

Module.AntiGrab = nil
Module.AntiExploin = nil
Module.AntiBlobman = nil
Module.AntiVoid = nil
Module.AntiLag = nil
local burn = nil
local burnGrab = nil
Module.BurnGrab = nil
local Rot = nil
local RotGrab = nil
Module.RadiationGrab = nil
local Posion = nil
local PosionGrab = nil
Module.PoisonGrab = nil
local noclipGrab = nil
Module.NoClipGrab = nil
Module.KillGrab = nil
Module.AirGrab = nil
local sendToHeaven = nil
local attractPlayer = false
local magneticAura = false
Module.KickAura = attractPlayer
Module.MagneticAura = magneticAura

local PaintPlayerPart = getDescendantParts("PaintPlayerPart")
local PoisonHurtPart = getDescendantParts("PoisonHurtPart")

Module.RadiationAura = PoisonHurtPart
Module.PoisonAura = PoisonHurtPart
Module.AuraWhiteList = PoisonHurtPart

local LineSettings = {}
LineSettings.LineRBG = nil
LineSettings.invisline = nil
local RbgLine = nil
Module.RbgLine = RbgLine
Module.InvisibleGrab = RbgLine

local BlobmanSettings = {}
BlobmanSettings.Blobman = nil
BlobmanSettings.blobWhiteListEnabled = nil
local DestroyServer = {}

local Mr_Voidz13 = "Mr_Voidz13"
local ex0lir = "ex0lir"
Mr_Voidz13 = nil

Module.DestroyServer = nil
Module.DestroyServerWhite = nil

return Module

local function AntiBlobmanLoop()
    if getgenv().Blobman then
        for i, part in ipairs(workspace:GetDescendants()) do
            if part:IsA("VehicleSeat") then
                local seatWeld = part:FindFirstChild("SeatWeld")
                if seatWeld and seatWeld.Part1 and seatWeld.Part1.Parent and seatWeld.Part1.Parent:IsA("Model") then
                    local model = seatWeld.Part1.Parent
                    if model:FindFirstChild("Humanoid") then
                        local humanoid = model:FindFirstChild("Humanoid")
                        if humanoid and humanoid.Parent and humanoid.Parent:IsA("Model") then
                            local character = humanoid.Parent
                            if character:FindFirstChild("HumanoidRootPart") then
                                local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                                if humanoidRootPart then
                                    if getgenv().blobWhiteListEnabled then
                                        if not table.find(DestroyServer, character.UserId) then
                                            character:Destroy()
                                        end
                                    else
                                        character:Destroy()
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
end

local function KickAuraToggle()
    if getgenv().KickAura then
        return
    end
    getgenv().KickAura = true
    task.spawn(function()
        while getgenv().KickAura do
            task.wait(0.02)
            local character = LocalPlayer.Character
            if character then
                local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local myPosition = humanoidRootPart.Position
                    for i, player in pairs(Players:GetPlayers()) do
                        if player ~= LocalPlayer then
                            local otherCharacter = player.Character
                            if otherCharacter then
                                local otherHumanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
                                if otherHumanoidRootPart then
                                    if getgenv().whiteListEnabled then
                                        if not table.find(Module.AuraWhiteList, player.UserId) then
                                            local distance = (myPosition - otherHumanoidRootPart.Position).Magnitude
                                            if distance <= Module.auraRadius then
                                                sendToHeaven(player)
                                            end
                                        end
                                    else
                                        local distance = (myPosition - otherHumanoidRootPart.Position).Magnitude
                                        if distance <= Module.auraRadius then
                                            sendToHeaven(player)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
        getgenv().KickAura = false
    end)
end

local function safe_call(func)
    local success, result = pcall(func)
    task.wait()
end

local function RotGrabLoop()
    local grabParts = workspace:FindFirstChild("GrabParts")
    if grabParts then
        if grabParts.Name == "GrabParts" then
            local grabPart = grabParts:FindFirstChild("GrabPart")
            local weldConstraint = grabParts:FindFirstChild("WeldConstraint")
            if weldConstraint and weldConstraint.Part1 then
                local parent = weldConstraint.Part1.Parent
                local head = parent:FindFirstChild("Head")
                if head then
                    Rot(head)
                end
            end
        end
    end
end

local function getParts(name)
    local parts = {}
    for i, part in ipairs(workspace:GetDescendants()) do
        if part:IsA("BasePart") and part.Name == name then
            table.insert(parts, part)
        end
    end
    return parts
end

local function NoClipGrabToggle()
    getgenv().NoClipGrab = not getgenv().NoClipGrab
    if getgenv().NoClipGrab then
        if not Module.NoClipGrabCoroutine then
            local co = coroutine.create(noclipGrab)
            Module.NoClipGrabCoroutine = co
            coroutine.resume(co)
        end
    else
        if Module.NoClipGrabCoroutine then
            coroutine.close(Module.NoClipGrabCoroutine)
            Module.NoClipGrabCoroutine = nil
        end
    end
end

local function AntiGrabToggle()
    getgenv().AntiGrab = not getgenv().AntiGrab
    if getgenv().AntiGrab then
        Module.antiGrabCoroutine = RunService.Heartbeat:Connect(function()
            RotGrabLoop()
        end)
    else
        if Module.antiGrabCoroutine then
            Module.antiGrabCoroutine:Disconnect()
            Module.antiGrabCoroutine = nil
        end
    end
end

local function CheckHeldPart()
    local character = LocalPlayer.Character
    if character then
        local head = character:FindFirstChild("Head")
        if head then
            local partOwner = head:FindFirstChild("PartOwner")
            if partOwner then
                local character = LocalPlayer.Character
                local humanoidRootPart = character.HumanoidRootPart
                local cframe = humanoidRootPart.CFrame
                local isHeld = partOwner.IsHeld.Value
                if isHeld then
                    local character = LocalPlayer.Character
                    local humanoid = character.Humanoid
                    if humanoid.Sit then
                        task.wait()
                        if character and character.Humanoid then
                            character.Humanoid.Sit = false
                        end
                    end
                end
                local character = LocalPlayer.Character
                local humanoidRootPart = character.HumanoidRootPart
                humanoidRootPart.CFrame = cframe
            end
        end
    end
end

local function BurnGrabLoop()
    local campfire = workspace:FindFirstChild("Campfire")
    if campfire then
        spawn_toy("Campfire")
    end
    local campfire = workspace:FindFirstChild("Campfire")
    local firePlayerPart = campfire:FindFirstChild("FirePlayerPart")
    if firePlayerPart then
        Module.burnPart = campfire.FirePlayerPart
    end
    Module.burnPart.Size = Vector3.new(7, 7, 7)
    Module.burnPart.Position = LocalPlayer.Character.HumanoidRootPart.Position
    task.wait(0.3)
    Module.burnPart.Position = Vector3.new(0, -50, 0)
end

local function BlacklistToggle()
    if Module.BlacklistToggle == nil then
        return
    end
    local blacklist = nil
    local success, result = pcall(blacklist)
    if success then
        return
    end
    local playerAddedConnection = Players.PlayerAdded:Connect(function(player)
        if table.find(blacklist, player.UserId) then
            player:Kick("Account blacklisted")
        end
    end)
    for i, player in pairs(Players:GetPlayers()) do
        if table.find(blacklist, player.UserId) then
            player:Kick("Account blacklisted")
        end
    end
end

local function CheckBlacklist(player)
    for i, blacklistEntry in pairs(Module.Blacklist) do
        local lowerName = string.lower(LocalPlayer.Name)
        local lowerBlacklistName = string.lower(blacklistEntry)
        if lowerName == lowerBlacklistName then
            LocalPlayer:Kick("Account blacklisted")
        end
    end
end

local function SendWebhook()
    return
end

local function safe_call(func)
    local success, result = pcall(func)
    task.wait()
end

local function PosionGrabLoop()
    local grabParts = workspace:FindFirstChild("GrabParts")
    if grabParts then
        if grabParts.Name == "GrabParts" then
            local grabPart = grabParts:FindFirstChild("GrabPart")
            local weldConstraint = grabParts:FindFirstChild("WeldConstraint")
            if weldConstraint and weldConstraint.Part1 then
                local parent = weldConstraint.Part1.Parent
                local humanoidRootPart = parent:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local model = humanoidRootPart:FindFirstChild("Model")
                    if model then
                        local player = model:FindFirstChild("Player")
                        if player then
                            Posion(head)
                        end
                    end
                end
            end
        end
    end
end

local function SetSettings(settings)
    if type(settings) == "table" then
        for key, value in pairs(settings) do
            if Module.ConfigWebhook[key] == nil then
                Module.ConfigWebhook[key] = value
            end
        end
    end
    if Module.ConfigWebhook.WebhookEnabled then
        if Module.ConfigWebhook.WebhookEnabled ~= ConfigWebhook.WebhookEnabled then
            SendWebhook()
        end
    end
end

local function AntiExplosion(character)
    local humanoid = character:FindFirstChild("Humanoid")
    local ragdolled = character:FindFirstChild("Ragdolled")
    if ragdolled then
        local antiExplosionConnection = ragdolled:GetPropertyChangedSignal("Value"):Connect(function()
            safe_call(function()
                local humanoid = character:FindFirstChild("Humanoid")
                local ragdolled = character:FindFirstChild("Ragdolled")
                if ragdolled then
                    local antiExplosionConnection = ragdolled:GetPropertyChangedSignal("Value"):Connect(function()
                        safe_call(function()
                            local humanoid = character:FindFirstChild("Humanoid")
                            local ragdolled = character:FindFirstChild("Ragdolled")
                            if ragdolled then
                                Module.antiExplosionConnection = antiExplosionConnection
                            end
                        end)
                    end)
                end
            end)
        end)
        Module.antiExplosionConnection = antiExplosionConnection
    end
end

local function SitFix()
    local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
    local ragdolled = LocalPlayer.Character:FindFirstChild("Ragdolled")
    if ragdolled and ragdolled.Value then
        task.wait()
        if LocalPlayer and LocalPlayer.Character and LocalPlayer.Character.Humanoid then
            LocalPlayer.Character.Humanoid.Sit = false
            LocalPlayer.Character.Humanoid.Sit = false
        end
    end
end

local function PoisonAuraToggle()
    getgenv().PoisonAura = not getgenv().PoisonAura
    if getgenv().PoisonAura then
        local co = coroutine.create(function()
            while getgenv().PoisonAura do
                task.wait(0.02)
                local character = LocalPlayer.Character
                if character then
                    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                    if humanoidRootPart then
                        local myPosition = humanoidRootPart.Position
                        for i, player in pairs(Players:GetPlayers()) do
                            if player ~= LocalPlayer then
                                local otherCharacter = player.Character
                                if otherCharacter then
                                    if getgenv().whiteListEnabled then
                                        if not table.find(Module.AuraWhiteList, player.UserId) then
                                            local otherHumanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
                                            if otherHumanoidRootPart then
                                                local distance = (myPosition - otherHumanoidRootPart.Position).Magnitude
                                                if distance <= Module.auraRadius then
                                                    local head = otherCharacter:FindFirstChild("Head")
                                                    if head then
                                                        local poisonPart = Instance.new("Part")
                                                        poisonPart.Size = Vector3.new(1, 3, 1)
                                                        poisonPart.Transparency = 1
                                                        poisonPart.Position = otherCharacter.HumanoidRootPart.Position
                                                        task.wait()
                                                        poisonPart.Position = Vector3.new(0, -200, 0)
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
        end)
        Module.PoisonAuraCoroutine = co
        coroutine.resume(co)
    else
        if Module.PoisonAuraCoroutine then
            coroutine.close(Module.PoisonAuraCoroutine)
            for i, part in pairs(getParts("PoisonHurtPart")) do
                part.Position = Vector3.new(0, -200, 0)
            end
            Module.PoisonAuraCoroutine = nil
        end
    end
end

local function safe_call(func)
    local success, result = pcall(func)
    local wait = wait
    wait()
end

local function RadiationAuraLoop()
    local character = LocalPlayer.Character
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            for i, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    if getgenv().whiteListEnabled then
                        if not table.find(Module.AuraWhiteList, player.UserId) then
                            local otherCharacter = player.Character
                            if otherCharacter then
                                local otherHumanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
                                if otherHumanoidRootPart then
                                    local distance = (humanoidRootPart.Position - otherHumanoidRootPart.Position).Magnitude
                                    if distance <= Module.auraRadius then
                                        local humanoid = otherCharacter:FindFirstChild("Humanoid")
                                        if humanoid and humanoid.Health > 0 then
                                            local head = otherCharacter:FindFirstChild("Head")
                                            if not head:FindFirstChild(player.Name) then
                                                local radiationPart = Instance.new("Part")
                                                radiationPart.Size = Vector3.new(1, 3, 1)
                                                radiationPart.Transparency = 1
                                                radiationPart.Position = otherHumanoidRootPart.CFrame
                                                task.wait()
                                                radiationPart.Position = Vector3.new(0, -200, 0)
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

local function BlobmanToggle()
    getgenv().Blobman = not getgenv().Blobman
    if getgenv().Blobman then
        local blobmanCoroutine = coroutine.create(function()
            while getgenv().Blobman do
                local blobman = nil
                for i, part in ipairs(workspace:GetDescendants()) do
                    if part:IsA("VehicleSeat") then
                        local seatWeld = part:FindFirstChild("SeatWeld")
                        if seatWeld and seatWeld.Part1 and seatWeld.Part1.Parent and seatWeld.Part1.Parent:IsA("Model") then
                            local model = seatWeld.Part1.Parent
                            if model:FindFirstChild("Humanoid") then
                                local humanoid = model:FindFirstChild("Humanoid")
                                if humanoid and humanoid.Parent and humanoid.Parent:IsA("Model") then
                                    local character = humanoid.Parent
                                    if character:FindFirstChild("HumanoidRootPart") then
                                        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
                                        if humanoidRootPart then
                                            blobman = character
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
                if blobman then
                    coroutine.resume(blobmanCoroutine)
                end
            end
        end)
        coroutine.resume(blobmanCoroutine)
    else
        if blobmanCoroutine then
            coroutine.close(blobmanCoroutine)
            blobmanCoroutine = nil
        end
    end
end

local function safe_call(func)
    func()
end

local function SuperStrengthValue(value)
    if not getgenv().SuperStrength then
        return
    end
    getgenv().ValueStrength = value
end

local function SuperStrengthToggle(enabled)
    getgenv().SuperStrength = enabled
    if enabled then
        if Module.SuperStrengthConnection then
            Module.SuperStrengthConnection:Disconnect()
            Module.SuperStrengthConnection = nil
        end
        return
    end
    if Module.SuperStrengthConnection then
        Module.SuperStrengthConnection:Disconnect()
    end
    Module.SuperStrengthConnection = workspace.ChildAdded:Connect(function(child)
        if child.Name == "GrabParts" then
            local grabPart = child:FindFirstChild("GrabPart")
            if grabPart then
                local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
                if weldConstraint then
                    local part1 = weldConstraint.Part1
                    if part1 then
                        if getgenv().SuperStrength then
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.Parent = part1
                        end
                    end
                end
            end
        end
    end)
end

local function ApplySuperStrength()
    local userInputType = UserInputService:GetLastInputType()
    if userInputType == Enum.UserInputType.MouseButton2 then
        local maxForce = Vector3.new(math.huge, math.huge, math.huge)
        local cameraCFrame = workspace.CurrentCamera.CFrame
        local lookVector = cameraCFrame.LookVector
        local valueStrength = getgenv().ValueStrength or 100
        local velocity = lookVector * valueStrength
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = maxForce
        bodyVelocity.Velocity = velocity
        local debris = Debris:GetService()
        debris:AddItem(bodyVelocity, 1)
    end
end

local function MagnetAura(player)
    if player then
        local character = player.Character
        if character then
            local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local magnet = Instance.new("BodyPosition")
                magnet.Name = "Magnet"
                magnet.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                magnet.Parent = humanoidRootPart
                magnet.Position = LocalPlayer.Character.HumanoidRootPart.Position
                local debris = Debris:GetService()
                debris:AddItem(magnet, 1)
            end
        end
    end
end

local function PosionLoop()
    local map = workspace:FindFirstChild("Map")
    local factoryIsland = map:FindFirstChild("FactoryIsland")
    local poisonContainer = factoryIsland:FindFirstChild("PoisonContainer")
    local poisonHurtPart = poisonContainer:FindFirstChild("PoisonHurtPart")
    poisonHurtPart.Size = Vector3.new(7, 7, 7)
    poisonHurtPart.Position = LocalPlayer.Character.HumanoidRootPart.Position
    task.wait(0.3)
    poisonHurtPart.Position = Vector3.new(0, -50, 0)
end

local function AntiBlobmanToggle()
    getgenv().AntiBlobman = not getgenv().AntiBlobman
    if not getgenv().AntiBlobman then
        for i, part in pairs(workspace:GetDescendants()) do
            if part.Name == "LeftWeld" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = true
            end
            if part.Name == "LeftAlignOrientation" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = true
            end
            if part.Name == "RightWeld" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = true
            end
            if part.Name == "RightAlignOrientation" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = true
            end
        end
        Module.DescendantAddedConnection = workspace.DescendantAdded:Connect(function(descendant)
            if descendant.Name == "LeftWeld" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
                descendant.Enabled = true
            end
            if descendant.Name == "LeftAlignOrientation" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
                descendant.Enabled = true
            end
            if descendant.Name == "RightWeld" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
                descendant.Enabled = true
            end
            if descendant.Name == "RightAlignOrientation" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
                descendant.Enabled = true
            end
        end)
    else
        for i, part in pairs(workspace:GetDescendants()) do
            if part.Name == "LeftWeld" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = false
            end
            if part.Name == "LeftAlignOrientation" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = false
            end
            if part.Name == "RightWeld" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = false
            end
            if part.Name == "RightAlignOrientation" and part.Parent and part.Parent.Parent and part.Parent.Parent.Parent == workspace then
                part.Enabled = false
            end
        end
        if Module.DescendantAddedConnection then
            Module.DescendantAddedConnection:Disconnect()
        end
    end
end

local function OnDescendantAdded(descendant)
    if descendant.Name == "LeftWeld" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
        descendant.Enabled = false
    end
    if descendant.Name == "LeftAlignOrientation" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
        descendant.Enabled = false
    end
    if descendant.Name == "RightWeld" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
        descendant.Enabled = false
    end
    if descendant.Name == "RightAlignOrientation" and descendant.Parent and descendant.Parent.Parent and descendant.Parent.Parent.Parent == workspace then
        descendant.Enabled = false
    end
end

local function InvisibleGrabToggle()
    getgenv().invisline = not getgenv().invisline
    if Module.InvisibleGrabConnection then
        task.cancel(Module.InvisibleGrabConnection)
    end
    if getgenv().invisline then
        Module.InvisibleGrabConnection = task.spawn(function()
            while getgenv().invisline do
                task.wait(0.2)
                CreateGrabLine:FireServer()
            end
        end)
    end
end

local function InvisibleGrabLoop()
    if getgenv().invisline then
        task.wait(0.2)
        CreateGrabLine:FireServer()
    end
end

local function AirGrabToggle()
    getgenv().AirGrab = not getgenv().AirGrab
    Module.ChildAddedConnection = workspace.ChildAdded:Connect(function(child)
        if child.Name == "GrabParts" then
            local grabPart = child:FindFirstChild("GrabPart")
            if grabPart then
                local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
                if weldConstraint then
                    local part1 = weldConstraint.Part1
                    local bodyVelocity = nil
                    if getgenv().AirGrab then
                        local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
                        if humanoid then
                            local bodyVelocity = Instance.new("BodyVelocity")
                            bodyVelocity.MaxForce = Vector3.new(0, 9999999, 0)
                            bodyVelocity.Velocity = Vector3.new(0, math.huge, 0)
                            local debris = Debris:GetService()
                            debris:AddItem(bodyVelocity, 100)
                        end
                    end
                end
            end
        end
    end)
end

local function OnChildAdded(child)
    if child.Name == "GrabParts" then
        local grabPart = child:FindFirstChild("GrabPart")
        if grabPart then
            local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
            if weldConstraint then
                local part1 = weldConstraint.Part1
                local bodyVelocity = nil
                if getgenv().AirGrab then
                    local humanoid = LocalPlayer.Character:FindFirstChild("Humanoid")
                    if humanoid then
                        local bodyVelocity = Instance.new("BodyVelocity")
                        bodyVelocity.MaxForce = Vector3.new(0, 9999999, 0)
                        bodyVelocity.Velocity = Vector3.new(0, math.huge, 0)
                        local debris = Debris:GetService()
                        debris:AddItem(bodyVelocity, 100)
                    end
                end
            end
        end
    end
end

local function safe_call(func)
    if func then
        safe_call(func)
    end
end

local function WhiteListToggle(enabled)
    getgenv().whiteListEnabled = enabled
end

local function CheckName(name)
    if LocalPlayer.Name == name then
        return true
    end
    return false
end

local function safe_call(func)
    local success, result = pcall(func)
    return
end

local function NoCollideLoop()
    local grabParts = workspace:FindFirstChild("GrabParts")
    if grabParts then
        if grabParts.Name == "GrabParts" then
            local grabPart = grabParts:FindFirstChild("GrabPart")
            local weldConstraint = grabParts:FindFirstChild("WeldConstraint")
            if weldConstraint and weldConstraint.Part1 then
                local parent = weldConstraint.Part1.Parent
                local humanoidRootPart = parent:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local model = humanoidRootPart:FindFirstChild("Model")
                    if model then
                        local player = model:FindFirstChild("Player")
                        if player then
                            for i, part in pairs(workspace:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                            task.wait()
                            for i, part in pairs(workspace:GetDescendants()) do
                                if part:IsA("BasePart") then
                                    part.CanCollide = false
                                end
                            end
                        end
                    end
                end
            end
        end
    end
end

local function SendWebhookLog()
    if not LocalPlayer then
        return
    end
    local userId = LocalPlayer.UserId
    local placeId = game.PlaceId
    local jobId = tostring(game.JobId)
    local gameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
    local currentDate = os.date("%Y-%m-%d %H:%M:%S")
    local maxPlayers = Players.MaxPlayers
    local content = ""
    local embeds = {}
    local embed = {}
    embed.title = "Script Execution Detected | Exec Log"
    embed.description = "A script was executed in your script. Here are the details:"
    embed.color = tonumber(3447003)
    local fields = {}
    local scriptInfo = {}
    scriptInfo.name = "Script Info"
    scriptInfo.value = string.format("Script Name: %s\nExecuted At: %s", Module.ConfigWebhook.NameHub, currentDate)
    scriptInfo.inline = false
    local playerDetails = {}
    playerDetails.name = "Player Details"
    playerDetails.value = string.format("Username: %s\nUserID: %s", LocalPlayer.Name, userId)
    playerDetails.inline = true
    local gameDetails = {}
    gameDetails.name = "Game Details"
    gameDetails.value = string.format("Game Name: %s\nPlace ID: %s\nJob ID: %s\nMax Players: %s", gameName, placeId, jobId, maxPlayers)
    gameDetails.inline = true
    table.insert(fields, scriptInfo)
    table.insert(fields, playerDetails)
    table.insert(fields, gameDetails)
    embed.fields = fields
    table.insert(embeds, embed)
    local data = {
        content = content,
        embeds = embeds
    }
    local jsonData = game:GetService("HttpService"):JsonEncode(data)
    local webhookLink = Module.ConfigWebhook.WebhookLink
    local headers = {
        ["Content-Type"] = "application/json"
    }
    local request = {
        Url = webhookLink,
        Method = "POST",
        Headers = headers,
        Body = jsonData
    }
    local success, result = pcall(function()
        game:GetService("HttpService"):RequestAsync(request)
    end)
    if not success then
        warn("Failed to send webhook:", result)
    end
end
