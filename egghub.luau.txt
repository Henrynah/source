-- ███╗   ██╗ ██████╗  ██████╗ ██████╗ ██╗      ██████╗ ██╗     
-- ████╗  ██║██╔═══██╗██╔═══██╗██╔══██╗██║     ██╔═══██╗██║     
-- ██╔██╗ ██║██║   ██║██║   ██║██████╔╝██║     ██║   ██║██║     
-- ██║╚██╗██║██║   ██║██║   ██║██╔══██╗██║     ██║   ██║██║     
-- ██║ ╚████║╚██████╔╝╚██████╔╝██████╔╝███████╗╚██████╔╝███████╗
-- ╚═╝  ╚═══╝ ╚═════╝  ╚═════╝ ╚═════╝ ╚══════╝ ╚═════╝ ╚══════╝

-- egg hub 1.0  by nooblol
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local W = game:GetService("Workspace")
local RS = game:GetService("ReplicatedStorage")
local Debris = game:GetService("Debris")
local LP = Players.LocalPlayer

local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/orion"))()
local Window = OrionLib:MakeWindow({ Name = "Egg Hub 1.0", HidePremium = true, SaveConfig = false })

local Config = {
    Grab = { KickGrab = false, KillGrab = false, VoidGrab = false, AnchorGrab = false, SuperStrength = false, StrengthPower = 250 },
    Defense = { AntiGrab = false, AntiVoid = false, AntiExplode = false, AntiRagdoll = false, AntiGucci = false },
    Aura = { KillAura = false, VoidAura = false, RagdollAura = false, FireAura = false, AnchorAura = false, NoclipAura = false, Radius = 32 },
    Blobman = { GrabAura = false, KickAura = false, LoopKick = false, ArmSide = "Left", Target = "" },
    Snipes = { TargetPlayer = nil, LoopKill = false, LoopVoid = false, LoopPoison = false, LoopRagdoll = false, LoopDeath = false, LoopBring = false, LoopPull = false },
    Player = { SpeedValue = 16, JumpValue = 50, SpeedEnabled = false, JumpEnabled = false, Noclip = false },
    GrabLine = { ChaosLine = false }
}

local LoopTimer, AuraTimer, DefenseTimer, ChaosLineTimer = 0, 0, 0, 0

local function HRP() local c = LP.Character or LP.CharacterAdded:Wait() return c:FindFirstChild("HumanoidRootPart") end
local function getHum() local c = LP.Character if not c then return nil end return c:FindFirstChildOfClass("Humanoid") end
local function SetNetworkOwner(part, cframe) if not part then return end pcall(function() RS.GrabEvents.SetNetworkOwner:FireServer(part, cframe or HRP().CFrame) end) end

local function Velocity(part, vel)
    if not part or not part.Parent then return end
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e8, 1e8, 1e8)
    bv.Velocity = vel
    bv.Parent = part
    task.delay(1, function() pcall(function() bv:Destroy() end) end)
end

local function MoveTo(part, targetCFrame)
    if not part or not part.Parent then return end
    for _, v in ipairs(part.Parent:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end
    local b = Instance.new("BodyPosition")
    b.MaxForce = Vector3.new(1e8, 1e8, 1e8)
    b.Position = targetCFrame.Position
    b.P, b.D = 2e4, 5e3
    b.Parent = part
    task.spawn(function()
        task.wait(1)
        pcall(function() b:Destroy() for _, v in ipairs(part.Parent:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = true end end end)
    end)
end

local function ungrab(part) pcall(function() RS.GrabEvents.DestroyGrabLine:FireServer(part) end) end
local function createLine(part) pcall(function() RS.GrabEvents.CreateGrabLine:FireServer(part, CFrame.identity) end) end
local function GetNearParts(origin, radius) return W:GetPartBoundsInRadius(origin, radius) end

local function spawntoy(name, cframe)
    local success, result = pcall(function() return RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(name, cframe, Vector3.zero) end)
    return success and result or nil
end

local function destroyToy(model) pcall(function() RS.MenuToys.DestroyToy:FireServer(model) end) end

local function getBlobman()
    local inv = W:FindFirstChild(LP.Name .. "SpawnedInToys")
    if inv then local blob = inv:FindFirstChild("CreatureBlobman") if blob then return blob end end
    return nil
end

local function spawnBlobman() return spawntoy("CreatureBlobman", HRP().CFrame) end

local function blobGrab(blob, target, side)
    if not blob or not target then return end
    pcall(function()
        local args = { [1] = blob:FindFirstChild(side .. "Detector"), [2] = target, [3] = blob:FindFirstChild(side .. "Detector"):FindFirstChild(side .. "Weld") }
        blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(unpack(args))
    end)
end

local function blobKick(blob, target, side)
    if not blob or not target then return end
    blobGrab(blob, HRP(), side)
    task.wait(0.1)
    SetNetworkOwner(target)
    task.wait()
    target.CFrame = target.CFrame + Vector3.new(0, 16, 0)
    task.wait(0.1)
    ungrab(target)
    blobGrab(blob, target, side)
end

local function blobBring(blob, target, side)
    local pos = HRP().CFrame
    HRP().CFrame = target.CFrame
    task.wait(0.25)
    blobGrab(blob, target, side)
    task.wait(0.25)
    HRP().CFrame = pos
end

local function blobVoid(blob, target, side)
    local pos = HRP().CFrame
    blobGrab(blob, HRP(), side)
    task.wait()
    blobBring(blob, target, side)
    task.wait()
    HRP().CFrame = CFrame.new(1e32, -16, 1e32)
    task.wait(1)
    getHum().Sit = false
    task.wait(0.1)
    HRP().CFrame = pos
    task.wait()
    destroyToy(blob)
end

local function blobSlide(blob, target, side)
    local pos = HRP().CFrame
    blobGrab(blob, HRP(), side)
    task.wait()
    blobBring(blob, target, side)
    task.wait()
    HRP().CFrame = pos
    task.wait(0.5)
    destroyToy(blob)
end

local function blobLock(blob, target, side)
    local pos = HRP().CFrame
    blobBring(blob, target, side)
    task.wait()
    HRP().CFrame = pos
end

local function getPlayerFromName(name)
    if not name or name == "" then return nil end
    local sname = name:lower()
    local tplayer = nil
    for _, player in pairs(Players:GetPlayers()) do
        if player.DisplayName:lower():sub(1, #sname) == sname then tplayer = player break
        elseif player.Name:lower():sub(1, #sname) == sname then if not tplayer then tplayer = player end end
    end
    return tplayer
end

local function getPlayerFromDisplayName(displayName)
    if not displayName or displayName == "" then return nil end
    for _, player in ipairs(Players:GetPlayers()) do if player.DisplayName == displayName then return player end end
    return nil
end

local function updatePlayerList()
    local displayNames = {}
    for _, player in ipairs(Players:GetPlayers()) do if player ~= LP then table.insert(displayNames, player.DisplayName) end end
    return displayNames
end

local function Snipefunc(root, func, ...)
    if not root or not root.Parent then return end
    local pos = HRP().CFrame
    local args = {...}
    task.spawn(function()
        local parts = {"Head", "Torso", "HumanoidRootPart"}
        for _, p in pairs(parts) do local part = LP.Character:FindFirstChild(p) if part then part.CanCollide = false end end
        local targetPos = root.Position
        HRP().CFrame = CFrame.new(targetPos.X, targetPos.Y - 6, targetPos.Z)
        task.wait(0.1)
        W.CurrentCamera.CFrame = CFrame.lookAt(W.CurrentCamera.CFrame.Position, root.Position)
        for _ = 1, 4 do SetNetworkOwner(root, HRP().CFrame) task.wait(0.05) end
        local look = W.CurrentCamera.CFrame
        task.wait(0.1)
        func(unpack(args))
        W.CurrentCamera.CFrame = look
        task.wait(0.1)
        for _, p in pairs(parts) do local part = LP.Character:FindFirstChild(p) if part then part.CanCollide = true end end
        HRP().CFrame = pos
        Velocity(HRP(), Vector3.zero)
    end)
end

local function SnipeKill(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function() MoveTo(root, CFrame.new(4096, -75, 4096)) Velocity(root, Vector3.new(0, -1000, 0)) end)
end

local function SnipeVoid(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function() Velocity(root, Vector3.new(0, 10000, 0)) end)
end

local function SnipePoison(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function() MoveTo(root, CFrame.new(58, -70, 271)) end)
end

local function SnipeRagdoll(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function()
        local rpos = root.CFrame
        Velocity(root, Vector3.new(0, -64, 0))
        task.wait(0.1)
        HRP().CFrame = rpos
        Velocity(root, Vector3.zero)
    end)
end

local function SnipeDeath(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function()
        local hum = character:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) end
        task.wait(0.5)
        ungrab(root)
    end)
end

local function SnipeBring(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local pos = HRP().CFrame
    Snipefunc(root, function() task.wait(0.01) root.CFrame = pos task.wait(0.5) ungrab(root) end)
end

local function SnipePull(target)
    local character = target.Character
    if not character then return end
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    Snipefunc(root, function()
        local bp = Instance.new("BodyPosition")
        bp.Name = "PullBodyPosition"
        bp.MaxForce = Vector3.new(1e8, 1e8, 1e8)
        bp.P, bp.D = 1e6, 1e5
        bp.Parent = root
        task.spawn(function()
            while bp and bp.Parent do
                if not root or not root.Parent then break end
                local currentMyPos = HRP().Position
                bp.Position = currentMyPos
                root.CFrame = CFrame.new(currentMyPos)
                SetNetworkOwner(root)
                task.wait(0.05)
            end
        end)
        task.wait(0.1)
    end)
    task.delay(2, function() if root and root.Parent then local bp = root:FindFirstChild("PullBodyPosition") if bp then bp:Destroy() end ungrab(root) end end)
end

local Tab1 = Window:MakeTab({ Name = "Grab", Icon = "rbxassetid://4483362458" })
local Tab2 = Window:MakeTab({ Name = "Defense", Icon = "rbxassetid://4483362458" })
local Tab3 = Window:MakeTab({ Name = "Aura", Icon = "rbxassetid://4483362458" })
local Tab4 = Window:MakeTab({ Name = "Blobman", Icon = "rbxassetid://4483362458" })
local Tab5 = Window:MakeTab({ Name = "Target Kill", Icon = "rbxassetid://4483362458" })
local Tab6 = Window:MakeTab({ Name = "Camera", Icon = "rbxassetid://4483362458" })
local Tab7 = Window:MakeTab({ Name = "Player", Icon = "rbxassetid://4483362458" })
local Tab8 = Window:MakeTab({ Name = "Grab Line", Icon = "rbxassetid://4483362458" })

Tab1:AddSection({ Name = "Grab Controls" })
Tab1:AddToggle({ Name = "Kick Grab", Default = false, Callback = function(v) Config.Grab.KickGrab = v end })
Tab1:AddToggle({ Name = "Kill Grab", Default = false, Callback = function(v) Config.Grab.KillGrab = v end })
Tab1:AddToggle({ Name = "Void Grab", Default = false, Callback = function(v) Config.Grab.VoidGrab = v end })
Tab1:AddToggle({ Name = "Anchor Grab", Default = false, Callback = function(v) Config.Grab.AnchorGrab = v end })

Tab1:AddSection({ Name = "Super Strength" })
Tab1:AddSlider({ Name = "Throw Power", Min = 0, Max = 10000, Default = 250, Increment = 10, Callback = function(v) Config.Grab.StrengthPower = v end })
Tab1:AddToggle({ Name = "Super Strength", Default = false, Callback = function(v) Config.Grab.SuperStrength = v end })

Tab2:AddSection({ Name = "Defense Systems" })
Tab2:AddToggle({ Name = "Anti-Grab", Default = false, Callback = function(v) Config.Defense.AntiGrab = v end })
Tab2:AddToggle({ Name = "Anti-Void", Default = false, Callback = function(v) Config.Defense.AntiVoid = v end })
Tab2:AddToggle({ Name = "Anti-Explode", Default = false, Callback = function(v) Config.Defense.AntiExplode = v end })
Tab2:AddToggle({ Name = "Anti-Ragdoll", Default = false, Callback = function(v) Config.Defense.AntiRagdoll = v end })
Tab2:AddToggle({ Name = "Anti-Gucci", Default = false, Callback = function(v) Config.Defense.AntiGucci = v end })

Tab3:AddSection({ Name = "Aura Attacks" })
Tab3:AddToggle({ Name = "Kill Aura", Default = false, Callback = function(v) Config.Aura.KillAura = v end })
Tab3:AddToggle({ Name = "Void Aura", Default = false, Callback = function(v) Config.Aura.VoidAura = v end })
Tab3:AddToggle({ Name = "Ragdoll Aura", Default = false, Callback = function(v) Config.Aura.RagdollAura = v end })
Tab3:AddToggle({ Name = "Fire Aura", Default = false, Callback = function(v) Config.Aura.FireAura = v end })
Tab3:AddToggle({ Name = "Anchor Aura", Default = false, Callback = function(v) Config.Aura.AnchorAura = v end })
Tab3:AddToggle({ Name = "Noclip Aura", Default = false, Callback = function(v) Config.Aura.NoclipAura = v end })
Tab3:AddSlider({ Name = "Aura Radius", Min = 10, Max = 100, Default = 32, Increment = 1, Callback = function(v) Config.Aura.Radius = v end })

Tab4:AddSection({ Name = "Blobman Controls" })
Tab4:AddButton({ Name = "Spawn Blobman", Callback = function() spawnBlobman() end })
Tab4:AddTextbox({ Name = "Target Player", Default = "", TextDisappear = false, Callback = function(v) Config.Blobman.Target = v end })
Tab4:AddDropdown({ Name = "Arm Side", Default = "Left", Options = {"Left", "Right"}, Callback = function(v) Config.Blobman.ArmSide = v end })

Tab4:AddSection({ Name = "Single Actions" })
Tab4:AddButton({ Name = "Grab", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobGrab(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })
Tab4:AddButton({ Name = "Bring", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobBring(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })
Tab4:AddButton({ Name = "Kick", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobKick(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })
Tab4:AddButton({ Name = "Void", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobVoid(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })
Tab4:AddButton({ Name = "Slide", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobSlide(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })
Tab4:AddButton({ Name = "Lock", Callback = function() local t = getPlayerFromName(Config.Blobman.Target) if t and t.Character then local blob = getBlobman() if blob then blobLock(blob, t.Character:FindFirstChild("HumanoidRootPart"), Config.Blobman.ArmSide) end end end })

Tab4:AddSection({ Name = "All Players" })
Tab4:AddButton({ Name = "Grab All", Callback = function() local blob = getBlobman() if not blob then return end for _, player in ipairs(Players:GetPlayers()) do if player ~= LP and player.Character then local root = player.Character:FindFirstChild("HumanoidRootPart") if root then task.wait(0.2) blobGrab(blob, root, Config.Blobman.ArmSide) end end end end })
Tab4:AddButton({ Name = "Kick All", Callback = function() local blob = getBlobman() if not blob then return end for _, player in ipairs(Players:GetPlayers()) do if player ~= LP and player.Character then local root = player.Character:FindFirstChild("HumanoidRootPart") if root then task.wait(0.25) blobKick(blob, root, Config.Blobman.ArmSide) end end end end })

Tab4:AddSection({ Name = "Auras" })
Tab4:AddToggle({ Name = "Grab Aura", Default = false, Callback = function(v) Config.Blobman.GrabAura = v end })
Tab4:AddToggle({ Name = "Kick Aura", Default = false, Callback = function(v) Config.Blobman.KickAura = v end })
Tab4:AddToggle({ Name = "Loop Kick", Default = false, Callback = function(v) Config.Blobman.LoopKick = v end })

Tab5:AddSection({ Name = "Target Selection" })
local PlayerDropdown = Tab5:AddDropdown({ Name = "Target List", Default = "", Options = updatePlayerList(), Callback = function(selectedDisplayName) Config.Snipes.TargetPlayer = getPlayerFromDisplayName(selectedDisplayName) end })
Tab5:AddButton({ Name = "🔄 Update Player List", Callback = function() PlayerDropdown:Refresh(updatePlayerList(), true) end })

Tab5:AddSection({ Name = "Loop Attacks" })
Tab5:AddToggle({ Name = "Loop Kill", Default = false, Callback = function(v) Config.Snipes.LoopKill = v end })
Tab5:AddToggle({ Name = "Loop Void", Default = false, Callback = function(v) Config.Snipes.LoopVoid = v end })
Tab5:AddToggle({ Name = "Loop Poison", Default = false, Callback = function(v) Config.Snipes.LoopPoison = v end })
Tab5:AddToggle({ Name = "Loop Ragdoll", Default = false, Callback = function(v) Config.Snipes.LoopRagdoll = v end })
Tab5:AddToggle({ Name = "Loop Death", Default = false, Callback = function(v) Config.Snipes.LoopDeath = v end })
Tab5:AddToggle({ Name = "Loop Bring", Default = false, Callback = function(v) Config.Snipes.LoopBring = v end })
Tab5:AddToggle({ Name = "Loop Pull", Default = false, Callback = function(v) Config.Snipes.LoopPull = v end })

Tab6:AddSection({ Name = "Camera Settings" })
Tab6:AddToggle({ Name = "50000 Stud Zoom", Default = false, Callback = function(v) LP.CameraMaxZoomDistance = v and 50000 or 128 end })

Tab7:AddSection({ Name = "Movement" })
Tab7:AddSlider({ Name = "Speed Value", Min = 16, Max = 1000, Default = 16, Increment = 1, Callback = function(v) Config.Player.SpeedValue = v end })
Tab7:AddToggle({ Name = "Speed Boost", Default = false, Callback = function(v) Config.Player.SpeedEnabled = v end })
Tab7:AddSlider({ Name = "Jump Value", Min = 50, Max = 500, Default = 50, Increment = 1, Callback = function(v) Config.Player.JumpValue = v end })
Tab7:AddToggle({ Name = "Jump Boost", Default = false, Callback = function(v) Config.Player.JumpEnabled = v end })
Tab7:AddToggle({ Name = "Noclip", Default = false, Callback = function(v) Config.Player.Noclip = v end })

Tab8:AddSection({ Name = "Grab Line Controls" })
Tab8:AddToggle({ Name = "Chaos Line", Default = false, Callback = function(v) Config.GrabLine.ChaosLine = v end })
Tab8:AddButton({ Name = "🔄 Reconnect All", Callback = function() for _, player in ipairs(Players:GetPlayers()) do if player ~= LP and player.Character then local root = player.Character:FindFirstChild("HumanoidRootPart") if root then createLine(root) end end end end })

RunService.Heartbeat:Connect(function(dt)
    AuraTimer, DefenseTimer, LoopTimer, ChaosLineTimer = AuraTimer + dt, DefenseTimer + dt, LoopTimer + dt, ChaosLineTimer + dt
    local root, hum = HRP(), getHum()
    if not root or not hum then return end
    
    if DefenseTimer >= 0.1 then
        if Config.Defense.AntiGrab then pcall(function() RS.CharacterEvents.Struggle:FireServer(LP) RS.GameCorrectionEvents.StopAllVelocity:FireServer() end) end
        if Config.Defense.AntiVoid and root.Position.Y < -87.5 then root.CFrame = CFrame.new(0, 10, 0) end
        if Config.Defense.AntiRagdoll and hum:GetState() == Enum.HumanoidStateType.Ragdoll then hum:ChangeState(Enum.HumanoidStateType.Running) end
        DefenseTimer = 0
    end
    
    if AuraTimer >= 0.5 then
        if Config.Aura.KillAura or Config.Aura.VoidAura or Config.Aura.RagdollAura or Config.Aura.NoclipAura then
            for _, part in ipairs(GetNearParts(root.Position, Config.Aura.Radius)) do
                if part.Name == "HumanoidRootPart" and not part:IsDescendantOf(LP.Character) then
                    SetNetworkOwner(part)
                    if Config.Aura.KillAura then MoveTo(part, CFrame.new(4096, -75, 4096)) Velocity(part, Vector3.new(0, -1000, 0)) end
                    if Config.Aura.VoidAura then Velocity(part, Vector3.new(0, 10000, 0)) end
                    if Config.Aura.RagdollAura then Velocity(part, Vector3.new(0, -256, 0)) end
                    if Config.Aura.NoclipAura then part.CanCollide = false end
                end
            end
        end
        if Config.Blobman.GrabAura or Config.Blobman.KickAura then
            local blob = getBlobman()
            if blob then
                for _, part in ipairs(GetNearParts(root.Position, Config.Aura.Radius)) do
                    if part.Name == "HumanoidRootPart" and not part:IsDescendantOf(LP.Character) then
                        if Config.Blobman.GrabAura then blobGrab(blob, part, Config.Blobman.ArmSide) end
                        if Config.Blobman.KickAura then blobKick(blob, part, Config.Blobman.ArmSide) end
                    end
                end
            end
        end
        AuraTimer = 0
    end
    
    if Config.Player.SpeedEnabled then hum.WalkSpeed = Config.Player.SpeedValue end
    if Config.Player.JumpEnabled then hum.JumpPower = Config.Player.JumpValue end
    if Config.Player.Noclip then
        for _, v in ipairs(LP.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end
        if hum.Sit then local seat = hum.SeatPart if seat and seat.Parent then for _, v in ipairs(seat.Parent:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end end
    end
    
    if LoopTimer >= 1.5 then
        if Config.Snipes.TargetPlayer and Config.Snipes.TargetPlayer.Character then
            if Config.Snipes.LoopKill then task.spawn(SnipeKill, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopVoid then task.spawn(SnipeVoid, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopPoison then task.spawn(SnipePoison, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopRagdoll then task.spawn(SnipeRagdoll, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopDeath then task.spawn(SnipeDeath, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopBring then task.spawn(SnipeBring, Config.Snipes.TargetPlayer) end
            if Config.Snipes.LoopPull then task.spawn(SnipePull, Config.Snipes.TargetPlayer) end
        end
        LoopTimer = 0
    end
    
    if Config.GrabLine.ChaosLine and ChaosLineTimer >= 0.0001 then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LP and player.Character then
                local targetRoot = player.Character:FindFirstChild("HumanoidRootPart")
                if targetRoot then createLine(targetRoot) end
            end
        end
        ChaosLineTimer = 0
    end
end)

W.ChildAdded:Connect(function(v)
    if v.Name == "GrabParts" and v:IsA("Model") then
        local GrabPart = v:FindFirstChild("GrabPart")
        if not GrabPart then return end
        local WeldConstraint = GrabPart:FindFirstChild("WeldConstraint")
        if not WeldConstraint or not WeldConstraint.Part1 then return end
        local target = WeldConstraint.Part1
        
        if Config.Grab.SuperStrength then
            v:GetPropertyChangedSignal("Parent"):Connect(function()
                if not v.Parent and target and target.Parent then
                    local throwVel = Instance.new("BodyVelocity")
                    throwVel.MaxForce = Vector3.new(1e8, 1e8, 1e8)
                    throwVel.Velocity = W.CurrentCamera.CFrame.LookVector * Config.Grab.StrengthPower
                    throwVel.Parent = target
                    Debris:AddItem(throwVel, 0.1)
                end
            end)
        end
        
        task.spawn(function()
            task.wait(0.1)
            if Config.Grab.VoidGrab then SetNetworkOwner(target) Velocity(target, Vector3.new(0, 10000, 0)) end
            if Config.Grab.KillGrab then SetNetworkOwner(target) MoveTo(target, CFrame.new(4096, -75, 4096)) Velocity(target, Vector3.new(0, -1000, 0)) end
            if Config.Grab.KickGrab then
                local player = Players:GetPlayerFromCharacter(target.Parent)
                if player then SetNetworkOwner(target) MoveTo(target, CFrame.new(25e25, 25e25, 25e25)) task.wait(0.5) ungrab(target) end
            end
            if Config.Grab.AnchorGrab then
                SetNetworkOwner(target)
                local pos = target.CFrame
                for _ = 1, 2 do
                    SetNetworkOwner(target)
                    local bp = Instance.new("BodyPosition")
                    bp.Position = pos.Position
                    bp.MaxForce = Vector3.new(1e8, 1e8, 1e8)
                    bp.Parent = target
                    local bg = Instance.new("BodyGyro")
                    bg.CFrame = pos
                    bg.MaxTorque = Vector3.new(1e8, 1e8, 1e8)
                    bg.Parent = target
                    task.wait(0.5)
                end
            end
        end)
    end
end)

Players.PlayerAdded:Connect(function() task.wait(0.5) PlayerDropdown:Refresh(updatePlayerList(), true) end)
Players.PlayerRemoving:Connect(function(player)
    if Config.Snipes.TargetPlayer == player then
        Config.Snipes.TargetPlayer = nil
        OrionLib:MakeNotification({ Name = "Target Left", Content = "ターゲットがサーバーを離れました", Time = 3 })
    end
    task.wait(0.5)
    PlayerDropdown:Refresh(updatePlayerList(), true)
end)

OrionLib:Init()
LP.CameraMode = Enum.CameraMode.Classic
LP.CameraMinZoomDistance = 0.5
LP.CameraMaxZoomDistance = 128

OrionLib:MakeNotification({ Name = "Egg Hub 1.0", Content = "🥚 All systems loaded successfully!", Time = 5 })
