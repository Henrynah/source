-- FTAP Reverse
-- https://t.me/ReverseFTAP

print("[Oblivion] Начинаем загрузку...")
repo = "https://raw.githubusercontent.com/deividcomsono/Obsidian/main/"
-- === ОПТИМИЗАЦИЯ ЗАГРУЗКИ: все 3 HTTP-запроса параллельно ===
-- Раньше было 3 последовательных HttpGet (каждый ждал завершения предыдущего).
-- Теперь грузим одновременно — время загрузки падает с 3× до 1×.
Library, ThemeManager, SaveManager = nil, nil, nil
libDone, themeDone, saveDone = false, false, false
task.spawn(function()
 print("[Oblivion] Скачиваем Library с GitHub...")
 local success, res = pcall(function() return loadstring(game:HttpGet(repo .. "Library.lua"))() end)
 if success then 
 Library = res
 libDone = true
 print("[Oblivion] Library успешно скачана!")
 else
 warn("[Oblivion] ОШИБКА загрузки Library: " .. tostring(res))
 end
end)
task.spawn(function()
 ThemeManager = loadstring(game:HttpGet(repo .. "addons/ThemeManager.lua"))()
 themeDone = true
end)
task.spawn(function()
 SaveManager = loadstring(game:HttpGet(repo .. "addons/SaveManager.lua"))()
 saveDone = true
end)
-- Ждём только Library (он нужен для создания окна), остальные догрузятся в фоне
t0 = tick()
while not libDone do
 if tick() - t0 > 30 then 
 warn("[Oblivion] ТАЙМАУТ: Библиотека не скачалась за 30 секунд!")
 break 
 end
 task.wait(0.05)
end

if not Library then
 warn("[Oblivion] КРИТИЧЕСКАЯ ОШИБКА: Library = nil. Скрипт остановлен.")
 return
end
print("[Oblivion] Создаем главное окно...")
Players = game:GetService("Players")
RunService = game:GetService("RunService")
LocalPlayer = Players.LocalPlayer

UserInputService = game:GetService("UserInputService")
Window = Library:CreateWindow({
 Title = "Oblivion | FTAP",
 Center = true,
 AutoShow = true,
 Resizable = true,
 NotifySide = "Right",
 ShowCustomCursor = true,
})

Tabs = {
 Combat = Window:AddTab("Combat", "swords"),
 Def = Window:AddTab("Def", "shield"),
 Movement = Window:AddTab("Movement", "person-standing"),
 Visual = Window:AddTab("Visual", "eye"),
 Target = Window:AddTab("Target", "target"),
 Bloodman = Window:AddTab("Bloodman", "skull"),
 Fun = Window:AddTab("Fun", "smile"),
 Misc = Window:AddTab("Misc", "component"),
 Settings = Window:AddTab("Settings", "settings")
}

Toggles = Library.Toggles
Options = Library.Options

-- ==============================================
do
-- Вкладка: COMBAT — GRAB (Модификатор)
-- ==============================================
-- Логика перенесена из NoName / INIT (RagdollGrab при легитимном захвате).
-- Это не кликер! Когда вы захватываете игрока (появляется GrabParts),
-- скрипт автоматически применяет к нему выбранный эффект.

CombatGrab = Tabs.Combat:AddLeftGroupbox("Grab Modifier")

CombatGrab:AddToggle("EnableCombatGrab", {
 Text = "Ragdoll Grab",
 Default = false,
 Tooltip = "Автоматически рагдаллит игрока, которого вы захватили",
})

Toggles.EnableCombatGrab:OnChanged(function()
 if Toggles.EnableCombatGrab.Value then
 -- Подготавливаем палетку заранее при ВКЛ (как в NoName)
 task.spawn(function()
 ragdollGrabPreparePalete()
 end)
 end
end)

CombatGrab:AddToggle("EnableFlingGrab", {
 Text = "Fling Grab",
 Default = false,
 Tooltip = "Запускает игрока высоко в небо (Fling)",
})

CombatGrab:AddToggle("EnableKillGrab", {
 Text = "Kill Grab",
 Default = false,
 Tooltip = "Мгновенно убивает игрока (бросок в бездну под карту)",
})

CombatGrab:AddToggle("EnableFreezeGrab", {
 Text = "Freeze Grab",
 Default = false,
 Tooltip = "Множественный freeze только на игроков: каждый новый захват замораживает нового игрока, повторный захват того же — разморозка. Тоггл OFF — размораживает всех корректно",
})

CombatGrab:AddToggle("EnableSpinGrab", {
 Text = "Spin Grab",
 Default = false,
 Tooltip = "Вращает захваченного игрока/предмет вокруг своей оси",
})

CombatGrab:AddSlider("SpinGrabSpeed", {
 Text = "Spin Speed",
 Default = 30,
 Min = 1,
 Max = 200,
 Increment = 1,
 Tooltip = "Скорость вращения при Spin Grab",
})

CombatGrab:AddToggle("EnableMasslessGrab", {
 Text = "Massless Grab",
 Default = false,
 Tooltip = "Линия захвата без физики — цель становится очень лёгкой, без массы",
})

CombatGrab:AddToggle("EnableInvisibleGrab", {
 Text = "Invisible Grab",
 Default = false,
 Tooltip = "Спам CreateGrabLine → граблайн невидим для всех",
})

CombatGrab:AddToggle("EnableNoclipGrab", {
 Text = "Noclip Grab",
 Default = false,
 Tooltip = "Target passes through walls",
})

CombatGrab:AddToggle("EnableAnchorGrab", {
 Text = "Anchor Grab",
 Default = false,
 Tooltip = "Якорит захваченный предмет/игрока на месте. Повторный захват того же — разякоривает. Тоггл OFF — разякоривает всех",
})


-- Freeze Grab logic: multi-target BodyPosition + BodyGyro server freeze
local freezeGrabCurrentTarget = nil

-- ==============================================
-- Combat: Right groupbox (Spin Speed + Infinite Line)
-- ==============================================
local CombatGrabRight = Tabs.Combat:AddRightGroupbox("Grab Options")

CombatGrabRight:AddToggle("EnableInfiniteLine", {
 Text = "Infinite Line Extend",
 Default = false,
 Tooltip = "Infinite grab line - scroll mouse wheel to extend/retract",
})

CombatGrabRight:AddSlider("LineExtendIncrease", {
 Text = "Line Extend Step",
 Default = 7,
 Min = 1,
 Max = 10,
 Increment = 1,
 Tooltip = "How much to extend per scroll step",
})

-- Infinite Line Extend logic
local infiniteLineConn = nil
local infiniteLineWheelConn = nil
local infiniteLineDistance = 0
local infiniteLineIncrease = 7

Toggles.EnableInfiniteLine:OnChanged(function()
 if Toggles.EnableInfiniteLine.Value then
 local cam = workspace.CurrentCamera
 local UIS = game:GetService("UserInputService")
 infiniteLineDistance = 0
 infiniteLineIncrease = Options.LineExtendIncrease and Options.LineExtendIncrease.Value or 7

 infiniteLineWheelConn = UIS.InputChanged:Connect(function(input)
 if input.UserInputType == Enum.UserInputType.MouseWheel then
 if infiniteLineDistance <= 3 then
 infiniteLineDistance = 3
 end
 if input.Position.Z > 0 then
 infiniteLineDistance = infiniteLineDistance + infiniteLineIncrease
 elseif input.Position.Z < 0 then
 infiniteLineDistance = infiniteLineDistance - infiniteLineIncrease
 end
 end
 end)

 infiniteLineConn = workspace.ChildAdded:Connect(function(child)
 if child.Name == "GrabParts" and child:IsA("Model") then
 if UIS.MouseEnabled then
 local grabPartsModel = child
 grabPartsModel:WaitForChild("GrabPart")
 grabPartsModel:WaitForChild("DragPart")

 local clonedDragPart = grabPartsModel.DragPart:Clone()
 clonedDragPart.Name = "DragPart1"
 if clonedDragPart:FindFirstChild("AlignPosition") and clonedDragPart:FindFirstChild("DragAttach") then
 clonedDragPart.AlignPosition.Attachment1 = clonedDragPart.DragAttach
 end
 clonedDragPart.Parent = grabPartsModel

 infiniteLineDistance = (clonedDragPart.Position - cam.CFrame.Position).Magnitude

 if grabPartsModel.DragPart:FindFirstChild("AlignPosition") then
 grabPartsModel.DragPart.AlignPosition.Enabled = false
 end
 if clonedDragPart:FindFirstChild("AlignOrientation") then
 clonedDragPart.AlignOrientation.Enabled = false
 end

 task.spawn(function()
 while grabPartsModel.Parent do
 clonedDragPart.Position = cam.CFrame.Position + cam.CFrame.LookVector * infiniteLineDistance
 task.wait()
 end
 infiniteLineDistance = 0
 end)
 end
 end
 end)
 else
 if infiniteLineWheelConn then
 infiniteLineWheelConn:Disconnect()
 infiniteLineWheelConn = nil
 end
 if infiniteLineConn then
 infiniteLineConn:Disconnect()
 infiniteLineConn = nil
 end
 infiniteLineDistance = 0
 end
end)

Options.LineExtendIncrease:OnChanged(function()
 infiniteLineIncrease = Options.LineExtendIncrease.Value
end)

-- Spin Grab tracking
local spinGrabTargets = {}

-- Invisible/Noclip Grab tracking
local invisibleGrabTargets = {}
local noclipGrabTargets = {}

-- === INVISIBLE GRAB (из unstable.txt 18414-18426) ===
-- Спамим CreateGrabLine:FireServer() каждый кадр → сервер не успевает
-- отрисовать граблайн → он невидим для всех игроков.
InvisLineOn = false
_invisLineTask = nil

local function ApplyInvisLine(v)
    InvisLineOn = v
    _G.InvisLine = v or nil

    -- Останавливаем старый цикл
    if _invisLineTask then
        pcall(function() task.cancel(_invisLineTask) end)
        _invisLineTask = nil
    end

    if v then
        -- Получаем CreateGrabLine remote (лениво, как в unstable)
        local grabFolder = ReplicatedStorage:FindFirstChild("GrabEvents")
        if not grabFolder then grabFolder = ReplicatedStorage:WaitForChild("GrabEvents", 5) end
        local createLine = grabFolder and grabFolder:FindFirstChild("CreateGrabLine")
        if not createLine then
            Library:Notify("Invisible Grab: CreateGrabLine не найден", 3)
            Toggles.EnableInvisibleGrab:SetValue(false)
            return
        end

        -- Запускаем цикл спама CreateGrabLine:FireServer() каждый кадр
        _invisLineTask = task.spawn(function()
            while InvisLineOn do
                pcall(function() createLine:FireServer() end)
                RunService.Heartbeat:Wait()
            end
        end)
    end
end

Toggles.EnableInvisibleGrab:OnChanged(function()
    -- Запускаем новую механику (спам CreateGrabLine каждый кадр)
    ApplyInvisLine(Toggles.EnableInvisibleGrab.Value)

    -- Также восстанавливаем оригинальные свойства (если были сохранены)
    if not Toggles.EnableInvisibleGrab.Value then
        for obj, data in pairs(invisibleGrabTargets) do
            if obj and obj.Parent then
                if data.isBeam then
                    pcall(function() obj.Enabled = data.enabled end)
                else
                    pcall(function() obj.Transparency = data.trans end)
                end
            end
        end
        invisibleGrabTargets = {}
    end
end)

-- === KICK GRAB — точная копия из unstable.txt 18821-18906 ===
-- + авто-выключение конфликтных тумблеров (Ragdoll/Kill/Fling/Spin/Massless/Anchor Grab)
-- чтобы они не перебивали Kick Grab вызовом DestroyGrabLine при появлении GrabParts.
CombatGrab:AddToggle("EnableKickGrab", {
    Text = "Kick Grab",
    Default = false,
})
Toggles.EnableKickGrab:OnChanged(function(state)
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

    -- Авто-выключаем конфликтные тумблеры (они вызывают DestroyGrabLine при GrabParts)
    local conflictToggles = {
        "EnableCombatGrab", -- Ragdoll Grab
        "EnableKillGrab",
        "EnableFlingGrab",
        "EnableSpinGrab",
        "EnableMasslessGrab",
        "EnableAnchorGrab",
    }
    for _, flag in ipairs(conflictToggles) do
        if Toggles[flag] and Toggles[flag].Value then
            pcall(function() Toggles[flag]:SetValue(false) end)
            Library:Notify("Kick Grab: " .. flag .. " выключен (конфликт)", 2)
        end
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

                for _, pl in ipairs(Players:GetPlayers()) do
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
                        local tgtHead = ownerPlayer.Character and (ownerPlayer.Character:FindFirstChild('Head') or ownerPlayer.Character:FindFirstChild('Torso') or ownerPlayer.Character:FindFirstChild('UpperTorso'))
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
end)

Toggles.EnableNoclipGrab:OnChanged(function()
 if not Toggles.EnableNoclipGrab.Value then
 for part, origCollide in pairs(noclipGrabTargets) do
 if part and part.Parent then
 pcall(function() part.CanCollide = origCollide end)
 end
 end
 noclipGrabTargets = {}
 end
end)

Toggles.EnableSpinGrab:OnChanged(function()
 if not Toggles.EnableSpinGrab.Value then
 for primary, bav in pairs(spinGrabTargets) do
 if bav and bav.Parent then bav:Destroy() end
 end
 spinGrabTargets = {}
 end
end)
local freezeGrabData = {} -- {[model] = {savedState, heartbeat, bodyPosition, bodyGyro}}

local function freezeGrabGetNetworkOwner()
 local rs = game:GetService("ReplicatedStorage")
 local folder = rs:FindFirstChild("GrabEvents") or rs:WaitForChild("GrabEvents", 10)
 if folder then
 return folder:FindFirstChild("SetNetworkOwner") or folder:WaitForChild("SetNetworkOwner", 5)
 end
 return nil
end

local function freezeGrabGetPrimaryPart(model)
 if not model then return nil end
 if model:IsA("Model") and model.PrimaryPart then return model.PrimaryPart end
 local hum = model:FindFirstChildOfClass("Humanoid")
 if hum and model:FindFirstChild("HumanoidRootPart") then return model.HumanoidRootPart end
 for _, part in ipairs(model:GetDescendants()) do
 if part:IsA("BasePart") then return part end
 end
 if model:IsA("BasePart") then return model end
 return nil
end

local function freezeGrabModel(model)
 if not model or not model.Parent then return end
 if freezeGrabData[model] then return end

 local primary = freezeGrabGetPrimaryPart(model)
 if not primary then return end

 local hum = model:FindFirstChildOfClass("Humanoid")
 local setOwner = freezeGrabGetNetworkOwner()

 local savedState = {
 primary = primary,
 anchored = primary.Anchored,
 platformStand = hum and hum.PlatformStand or nil,
 sit = hum and hum.Sit or nil,
 pos = primary.Position,
 }

 -- Take ownership of the whole model so physics changes replicate
 if setOwner then
 for _, part in ipairs(model:GetDescendants()) do
 if part:IsA("BasePart") then
 pcall(function() setOwner:FireServer(part, part.CFrame) end)
 end
 end
 if model:IsA("BasePart") then
 pcall(function() setOwner:FireServer(model, model.CFrame) end)
 end
 end

 local bp = Instance.new("BodyPosition")
 bp.Name = "FreezeGrabBodyPosition"
 bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
 bp.P = 1e7
 bp.D = 1e5
 bp.Position = primary.Position
 bp.Parent = primary

 local bg = Instance.new("BodyGyro")
 bg.Name = "FreezeGrabBodyGyro"
 bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
 bg.P = 1e7
 bg.D = 1e5
 bg.CFrame = primary.CFrame
 bg.Parent = primary

 pcall(function() primary.Anchored = true end)

 if hum then
 pcall(function()
 hum.PlatformStand = true
 hum.Sit = true
 end)
 end

 local heartbeat = game:GetService("RunService").Heartbeat:Connect(function()
 local owner = freezeGrabGetNetworkOwner()
 if primary and primary.Parent then
 pcall(function()
 primary.Anchored = true
 primary.AssemblyLinearVelocity = Vector3.zero
 primary.AssemblyAngularVelocity = Vector3.zero
 end)
 if bp and bp.Parent then
 pcall(function() bp.Position = savedState.pos end)
 end
 if owner then
 pcall(function() owner:FireServer(primary, primary.CFrame) end)
 end
 else
 -- Target was destroyed, clean up automatically
 if freezeGrabData[model] then
 freezeGrabData[model].heartbeat:Disconnect()
 freezeGrabData[model] = nil
 end
 end
 if hum and hum.Parent then
 pcall(function()
 hum.PlatformStand = true
 hum.Sit = true
 end)
 end
 end)

 freezeGrabData[model] = {
 savedState = savedState,
 heartbeat = heartbeat,
 bodyPosition = bp,
 bodyGyro = bg,
 }

 Library:Notify("Freeze Grab: заморожено", 1)
end

local function unfreezeGrabModel(model)
 if not model then return end
 local data = freezeGrabData[model]
 if not data then return end

 if data.heartbeat then
 data.heartbeat:Disconnect()
 end
 if data.bodyPosition then
 pcall(function() data.bodyPosition:Destroy() end)
 end
 if data.bodyGyro then
 pcall(function() data.bodyGyro:Destroy() end)
 end

 local saved = data.savedState
 if saved then
 local primary = saved.primary
 if primary and primary.Parent then
 pcall(function()
 primary.Anchored = false
 primary.AssemblyLinearVelocity = Vector3.zero
 primary.AssemblyAngularVelocity = Vector3.zero
 primary.Velocity = Vector3.new(0, -5, 0)
 primary.AssemblyLinearVelocity = Vector3.new(0, -5, 0)
 end)
 -- Force network ownership back to server so physics replicates correctly
 local setOwner = freezeGrabGetNetworkOwner()
 if setOwner then
 pcall(function() setOwner:FireServer(primary, primary.CFrame) end)
 end
 end
 local hum = saved.primary and saved.primary.Parent and saved.primary.Parent:FindFirstChildOfClass("Humanoid")
 if hum then
 pcall(function()
 hum.PlatformStand = saved.platformStand
 hum.Sit = saved.sit
 hum:ChangeState(Enum.HumanoidStateType.GettingUp)
 hum:ChangeState(Enum.HumanoidStateType.Running)
 end)
 end
 end

 freezeGrabData[model] = nil
 Library:Notify("Freeze Grab: разморожено", 1)
end

Toggles.EnableFreezeGrab:OnChanged(function()
 if Toggles.EnableFreezeGrab.Value then
 if freezeGrabCurrentTarget and not freezeGrabData[freezeGrabCurrentTarget] then
 freezeGrabModel(freezeGrabCurrentTarget)
 end
 else
 -- Collect all frozen models first, then unfreeze them (avoid modifying table during pairs)
 local frozenModels = {}
 for model, _ in pairs(freezeGrabData) do
 table.insert(frozenModels, model)
 end
 for _, model in ipairs(frozenModels) do
 unfreezeGrabModel(model)
 task.wait(0.05)
 end
 end
end)

workspace.ChildAdded:Connect(function(child)
 if child.Name == "GrabParts" then
 local grabPart = child:WaitForChild("GrabPart", 1)
 if not grabPart then return end
 local weld = grabPart:WaitForChild("WeldConstraint", 1)
 if not weld then return end
 local targetPart = weld.Part1
 local targetModel = targetPart and targetPart.Parent
 local targetPlayer = targetModel and Players:GetPlayerFromCharacter(targetModel)
 if targetModel and targetModel ~= LocalPlayer.Character and targetPlayer and targetPlayer ~= LocalPlayer then
 freezeGrabCurrentTarget = targetModel
 if Toggles.EnableFreezeGrab and Toggles.EnableFreezeGrab.Value then
 if freezeGrabData[targetModel] then
 -- Same target grabbed again: unfreeze
 unfreezeGrabModel(targetModel)
 else
 -- New target: freeze it, keep old frozen targets frozen
 freezeGrabModel(targetModel)
 end
 end
 end
 end
end)

workspace.ChildRemoved:Connect(function(child)
 if child.Name == "GrabParts" then
 freezeGrabCurrentTarget = nil
 for primary, bav in pairs(spinGrabTargets) do
 if bav and bav.Parent then bav:Destroy() end
 end
 spinGrabTargets = {}
 -- Frozen targets stay frozen; they are only unfrozen by re-grabbing or toggle off
 end
end)


-- ==============================================
-- Anchor Grab logic (из venom X, адаптировано: люди + предметы, toggle, заливка)
-- ==============================================
anchorGrabParts = {} -- [primaryPart] = true
anchorGrabData = {} -- [primaryPart] = {model, highlight, descConn}
anchorGrabCoroutine = nil

local function anchorGrabGetPrimaryPart(weldPart1)
 if not weldPart1 then return nil end
 if weldPart1.Name == "SoundPart" then
 local model = weldPart1.Parent
 if model and model:IsA("Model") then
 return model.PrimaryPart or weldPart1
 end
 return weldPart1
 end
 local model = weldPart1.Parent
 if model and model:IsA("Model") and model ~= workspace then
 return model.PrimaryPart or weldPart1
 end
 return weldPart1
end

local function anchorGrabGetTargetModel(primaryPart)
 if not primaryPart then return nil end
 local model = primaryPart.Parent
 if model and model:IsA("Model") and model ~= workspace then
 return model
 end
 return primaryPart
end

local function anchorGrabCreateHighlight(parent)
 -- ЗАЛИВКА вместо обводки (FillTransparency = 0.5, FillColor = green)
 local highlight = Instance.new("Highlight")
 highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
 highlight.FillTransparency = 0.5
 highlight.FillColor = Color3.fromRGB(0, 170, 0)
 highlight.OutlineColor = Color3.fromRGB(0, 170, 0)
 highlight.OutlineTransparency = 0
 highlight.Name = "AnchorGrabHighlight"
 highlight.Parent = parent
 return highlight
end

local function anchorGrabCreateBodyMovers(part, position, rotation)
 local bodyPosition = Instance.new("BodyPosition")
 bodyPosition.Name = "AnchorGrabBodyPosition"
 bodyPosition.P = 15000
 bodyPosition.D = 200
 bodyPosition.MaxForce = Vector3.new(5000000, 5000000, 5000000)
 bodyPosition.Position = position
 bodyPosition.Parent = part

 local bodyGyro = Instance.new("BodyGyro")
 bodyGyro.Name = "AnchorGrabBodyGyro"
 bodyGyro.P = 15000
 bodyGyro.D = 200
 bodyGyro.MaxTorque = Vector3.new(5000000, 5000000, 5000000)
 bodyGyro.CFrame = rotation
 bodyGyro.Parent = part
end

local function anchorGrabAnchor(primaryPart)
 if not primaryPart or not primaryPart.Parent then return end
 if anchorGrabData[primaryPart] then return end

 local targetModel = anchorGrabGetTargetModel(primaryPart)
 if not targetModel then return end

 -- Удалить старые BodyPosition/BodyGyro (не трогаем Freeze Grab)
 -- Как в venom X: ищем по всей модели если это Model
 if targetModel:IsA("Model") then
 for _, child in ipairs(targetModel:GetDescendants()) do
 if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
 if child.Name ~= "FreezeGrabBodyPosition" and child.Name ~= "FreezeGrabBodyGyro" then
 child:Destroy()
 end
 end
 end
 else
 for _, child in ipairs(primaryPart:GetChildren()) do
 if child:IsA("BodyPosition") or child:IsA("BodyGyro") then
 if child.Name ~= "FreezeGrabBodyPosition" and child.Name ~= "FreezeGrabBodyGyro" then
 child:Destroy()
 end
 end
 end
 end

 -- Создать заливку (highlight)
 local highlight = anchorGrabCreateHighlight(targetModel)

 -- Визуальный маркер: красная сфера на позиции якоря + Beam до игрока
 local markerSphere = Instance.new("Part")
 markerSphere.Name = "AnchorMarker"
 markerSphere.Shape = Enum.PartType.Ball
 markerSphere.Size = Vector3.new(2, 2, 2)
 markerSphere.Color = Color3.fromRGB(255, 50, 50)
 markerSphere.Material = Enum.Material.Neon
 markerSphere.Anchored = true
 markerSphere.CanCollide = false
 markerSphere.CanQuery = false
 markerSphere.Transparency = 0.3
 markerSphere.CFrame = CFrame.new(primaryPart.Position)
 markerSphere.Parent = workspace

 -- Beam от сферы до игрока (показывает где игрок реально)
 local beam = Instance.new("Beam")
 beam.Name = "AnchorBeam"
 beam.Attachment0 = Instance.new("Attachment", markerSphere)
 beam.Attachment1 = Instance.new("Attachment", primaryPart)
 beam.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0))
 beam.Width0 = 0.3
 beam.Width1 = 0.3
 beam.FaceCamera = true
 beam.Parent = markerSphere

 -- BodyPosition + BodyGyro для фиксации (как в venom X: P=15000, D=200, MaxForce=5M)
 -- НЕТ SNO! НЕТ Heartbeat! Только BodyMovers на primaryPart.
 anchorGrabCreateBodyMovers(primaryPart, primaryPart.Position, primaryPart.CFrame)

 -- Для игроков: SNO один раз + Anchored + PlatformStand + Sit
 -- SNO нужен чтобы сервер узнал о якоре (иначе только у нас висит, у игрока на земле)
 -- ВАЖНО: только ОДИН вызов, не спамить!
 local humanoid = nil
 if targetModel and targetModel:IsA("Model") then
 humanoid = targetModel:FindFirstChildOfClass("Humanoid")
 end
 local setOwner = nil
 if humanoid then
 setOwner = freezeGrabGetNetworkOwner()
 if setOwner then
 pcall(function() setOwner:FireServer(primaryPart, primaryPart.CFrame) end)
 end
 pcall(function()
 primaryPart.Anchored = true
 humanoid.PlatformStand = true
 humanoid.Sit = true
 end)
 end

 -- Отслеживание PartOwner (меняем цвет заливки)
 local descConn
 if targetModel and targetModel:IsA("Model") then
 descConn = targetModel.DescendantAdded:Connect(function(descendant)
 if descendant.Name == "PartOwner" then
 local hl = targetModel:FindFirstChild("AnchorGrabHighlight")
 if hl then
 if descendant.Value ~= LocalPlayer.Name then
 hl.FillColor = Color3.fromRGB(255, 0, 0)
 hl.OutlineColor = Color3.fromRGB(255, 0, 0)
 else
 hl.FillColor = Color3.fromRGB(0, 170, 0)
 hl.OutlineColor = Color3.fromRGB(0, 170, 0)
 end
 end
 end
 end)
 end

 anchorGrabData[primaryPart] = {
 model = targetModel,
 highlight = highlight,
 descConn = descConn,
 humanoid = humanoid,
 setOwner = setOwner,
 lastSno = 0,
 anchorPos = primaryPart.Position,
 anchorCFrame = primaryPart.CFrame,
 markerSphere = markerSphere,
 beam = beam,
 }
 anchorGrabParts[primaryPart] = true

 Library:Notify("Anchor Grab: заякорено", 1)
end

local function anchorGrabUnanchor(primaryPart)
 if not primaryPart then return end
 local data = anchorGrabData[primaryPart]
 if not data then return end

 local bp = primaryPart:FindFirstChild("AnchorGrabBodyPosition")
 if bp then bp:Destroy() end
 local bg = primaryPart:FindFirstChild("AnchorGrabBodyGyro")
 if bg then bg:Destroy() end

 if data.highlight and data.highlight.Parent then
 data.highlight:Destroy()
 end
 if data.markerSphere and data.markerSphere.Parent then
 data.markerSphere:Destroy()
 end

 if data.descConn then
 data.descConn:Disconnect()
 end

 -- Восстановить игрока: Anchored=false + PlatformStand=false + Sit=false
 if data.humanoid and data.humanoid.Parent then
 pcall(function()
 primaryPart.Anchored = false
 data.humanoid.PlatformStand = false
 data.humanoid.Sit = false
 end)
 end

 anchorGrabData[primaryPart] = nil
 anchorGrabParts[primaryPart] = nil

 Library:Notify("Anchor Grab: разякорено", 1)
end

local function anchorGrabCleanupAll()
 local parts = {}
 for primaryPart, _ in pairs(anchorGrabData) do
 table.insert(parts, primaryPart)
 end
 for _, primaryPart in ipairs(parts) do
 anchorGrabUnanchor(primaryPart)
 end
 anchorGrabParts = {}
end

-- Heartbeat: Ð¿Ð¾Ð´Ð´ÐµÑÐ¶Ð°Ð½Ð¸Ðµ ÑÐºÐ¾ÑÑ Ð´Ð»Ñ Ð¸Ð³ÑÐ¾ÐºÐ¾Ð²
-- Ð Ðµ-Ð°Ð¿Ð¿Ð»Ð°Ð¸Ð¼ SNO + Anchored + PlatformStand ÐºÐ°Ð¶Ð´ÑÑ ÑÐµÐºÑÐ½Ð´Ñ
-- ÐÐµÐ· ÑÑÐ¾Ð³Ð¾ ÑÐµÑÐ²ÐµÑ Ð²Ð¾Ð·Ð²ÑÐ°ÑÐ°ÐµÑ network ownership Ð¸ Ð¸Ð³ÑÐ¾Ðº Ð¼Ð¾Ð¶ÐµÑ ÑÐ±ÐµÐ¶Ð°ÑÑ
anchorGrabMaintainConn = nil
local function anchorGrabStartMaintain()
 if anchorGrabMaintainConn then return end
 anchorGrabMaintainConn = game:GetService("RunService").Heartbeat:Connect(function()
 -- Check if user is trying to grab an anchored player (for toggle-unanchor)
 local grabParts = workspace:FindFirstChild("GrabParts")
 local grabbedAnchoredPart = nil
 if grabParts then
 local grabPart = grabParts:FindFirstChild("GrabPart")
 if grabPart then
 local wc = grabPart:FindFirstChild("WeldConstraint")
 if wc and wc.Part1 then
 for pp, dd in pairs(anchorGrabData) do
 if dd.humanoid and pp.Parent then
 if wc.Part1 == pp or wc.Part1:IsDescendantOf(dd.model) then
 grabbedAnchoredPart = pp
 break
 end
 end
 end
 end
 end
 end

 for primaryPart, data in pairs(anchorGrabData) do
 if data.humanoid and data.humanoid.Parent and primaryPart.Parent then
 -- Update beam color: green=in place, red=escaped
 if data.beam and data.beam.Parent then
 local dist = (primaryPart.Position - data.anchorPos).Magnitude
 if dist > 1 then
 data.beam.Color = ColorSequence.new(Color3.fromRGB(255, 0, 0))
 data.beam.Width0 = 0.5
 data.beam.Width1 = 0.5
 else
 data.beam.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0))
 data.beam.Width0 = 0.3
 data.beam.Width1 = 0.3
 end
 end

 -- If user is grabbing THIS anchored player, temporarily unanchor
 if grabbedAnchoredPart == primaryPart then
 pcall(function() primaryPart.Anchored = false end)
 else
 -- Check: did the player escape? (threshold 0.5 studs = very fast detection)
 local currentPos = primaryPart.Position
 local anchorPos = data.anchorPos
 local dist = (Vector3.new(currentPos.X, 0, currentPos.Z) - Vector3.new(anchorPos.X, 0, anchorPos.Z)).Magnitude
 local heightDiff = math.abs(currentPos.Y - anchorPos.Y)

 if dist > 0.5 or heightDiff > 1 then
 -- Player escaped! IMMEDIATE teleport back + re-anchor (no delay!)
 pcall(function()
 -- Fire SNO immediately (no throttle on escape!)
 if data.setOwner then
 data.setOwner:FireServer(primaryPart, data.anchorCFrame)
 end
 -- Teleport back NOW
 local model = data.model
 if model and model:IsA("Model") and model.PrimaryPart then
 model:PivotTo(data.anchorCFrame)
 else
 primaryPart.CFrame = data.anchorCFrame
 end
 primaryPart.Anchored = true
 data.humanoid.PlatformStand = true
 data.humanoid.Sit = true
 -- Recreate BodyMovers
 local bp = primaryPart:FindFirstChild("AnchorGrabBodyPosition")
 local bg = primaryPart:FindFirstChild("AnchorGrabBodyGyro")
 if bp then bp:Destroy() end
 if bg then bg:Destroy() end
 anchorGrabCreateBodyMovers(primaryPart, anchorPos, data.anchorCFrame)
 end)
 data.lastSno = os.clock()
 else
 -- Player is in place - maintain anchor
 pcall(function()
 primaryPart.Anchored = true
 data.humanoid.PlatformStand = true
 data.humanoid.Sit = true
 end)
 -- Re-apply SNO every second
 local now = os.clock()
 if data.setOwner and (now - (data.lastSno or 0)) > 1 then
 data.lastSno = now
 pcall(function() data.setOwner:FireServer(primaryPart, data.anchorCFrame) end)
 end
 -- Check BodyPosition/BodyGyro
 if not primaryPart:FindFirstChild("AnchorGrabBodyPosition") then
 anchorGrabCreateBodyMovers(primaryPart, anchorPos, data.anchorCFrame)
 end
 end
 end
 end
 end
 end)
end

local function anchorGrabStopMaintain()
 if anchorGrabMaintainConn then
 anchorGrabMaintainConn:Disconnect()
 anchorGrabMaintainConn = nil
 end
end

-- Главный цикл Anchor Grab (мониторит GrabParts как в venom X)
local function anchorGrabLoop()
 while true do
 pcall(function()
 local grabParts = workspace:FindFirstChild("GrabParts")
 if not grabParts then return end

 local grabPart = grabParts:FindFirstChild("GrabPart")
 if not grabPart then return end

 local weldConstraint = grabPart:FindFirstChild("WeldConstraint")
 if not weldConstraint or not weldConstraint.Part1 then return end

 local primaryPart = anchorGrabGetPrimaryPart(weldConstraint.Part1)
 if not primaryPart then return end

 -- Проверка: не карта
 local function isDescendantOfMap(target)
 local current = target.Parent
 while current do
 if current.Name == "Map" then return true end
 current = current.Parent
 end
 return false
 end
 if isDescendantOfMap(primaryPart) then return end

 -- TOGGLE: если уже якорен -> разякорить
 if anchorGrabData[primaryPart] then
 anchorGrabUnanchor(primaryPart)
 while workspace:FindFirstChild("GrabParts") do
 task.wait()
 end
 return
 end

 -- Ждём отпускания захвата (сначала двигаем предмет куда надо)
 while workspace:FindFirstChild("GrabParts") do
 task.wait()
 end

 -- Якорим ТОЛЬКО после отпускания
 anchorGrabAnchor(primaryPart)
 end)
 task.wait()
 end
end

Toggles.EnableAnchorGrab:OnChanged(function()
 if Toggles.EnableAnchorGrab.Value then
 anchorGrabCoroutine = task.spawn(anchorGrabLoop)
 anchorGrabStartMaintain()
 else
 if anchorGrabCoroutine then
 task.cancel(anchorGrabCoroutine)
 anchorGrabCoroutine = nil
 end
 anchorGrabStopMaintain()
 anchorGrabCleanupAll()
 end
end)

-- ==============================================
-- Ragdoll Grab / Fling Grab / Kill Grab (логика применения при захвате)
-- ==============================================

local function combatGrabGetTargetModel(child)
 local grabPart = child:WaitForChild("GrabPart", 1)
 if not grabPart then return nil end
 local weld = grabPart:WaitForChild("WeldConstraint", 1)
 if not weld then return nil end
 local targetPart = weld.Part1
 local targetModel = targetPart and targetPart.Parent
 if not targetModel then return nil end
 local targetPlayer = Players:GetPlayerFromCharacter(targetModel)
 if targetModel == LocalPlayer.Character then return nil end
 if targetPlayer == LocalPlayer then return nil end
 return targetModel
end

local function combatGrabGetDestroyLine()
 local rs = game:GetService("ReplicatedStorage")
 local folder = rs:FindFirstChild("GrabEvents") or rs:WaitForChild("GrabEvents", 10)
 if folder then
 return folder:FindFirstChild("DestroyGrabLine") or folder:WaitForChild("DestroyGrabLine", 5)
 end
 return nil
end

-- Отпускает цель из захвата (рвёт WeldConstraint через DestroyGrabLine),
-- иначе цель остаётся приварена к твоему GrabPart и висит в воздухе.
local function combatGrabReleaseHold(targetModel, targetRoot)
 local destroyLine = combatGrabGetDestroyLine()
 if not destroyLine then return end

 if targetModel then
 for _, v in ipairs(targetModel:GetDescendants()) do
 if v.Name == "PartOwner" then
 pcall(function() destroyLine:FireServer(v.Parent) end)
 end
 end
 end

 if targetRoot then
 for _ = 1, 3 do
 pcall(function() destroyLine:FireServer(targetRoot) end)
 end
 end
end

-- Ragdoll Grab (точная логика из NoName):
-- 1) При ВКЛ тумблера — спавним PalletLightBrown, берём ownership через
-- DestroyGrabLine (sno) + PivotTo к палетке, делаем прозрачной, BV вверх 900
-- 2) При захвате (GrabParts) — позиционируем SoundPart на RootPart цели
-- паллетка касается цели → игра рагдоллит через встроенную механику
-- НИКАКОГО RagdollRemote — только физика палетки!
ragdollPalete = nil

local function ragdollGrabGetDestroyLine()
 local RS = game:GetService("ReplicatedStorage")
 local folder = RS:FindFirstChild("GrabEvents") or RS:WaitForChild("GrabEvents", 10)
 if folder then
 return folder:FindFirstChild("DestroyGrabLine") or folder:WaitForChild("DestroyGrabLine", 5)
 end
 return nil
end

local function ragdollGrabGetSpawnRemote()
 local RS = game:GetService("ReplicatedStorage")
 local menuToys = RS:FindFirstChild("MenuToys") or RS:WaitForChild("MenuToys", 10)
 if menuToys then
 return menuToys:FindFirstChild("SpawnToyRemoteFunction") or menuToys:WaitForChild("SpawnToyRemoteFunction", 5)
 end
 return nil
end

local function ragdollGrabCheckOwner(part)
 return part:FindFirstChild("PartOwner") and part["PartOwner"].Value == LocalPlayer.Name
end

local function ragdollGrabSpawnPalete()
 local spawnRemote = ragdollGrabGetSpawnRemote()
 if not spawnRemote then return nil end
 local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy") or LocalPlayer:WaitForChild("CanSpawnToy", 10)
 if not canSpawn then return nil end
 local t0 = tick()
 while not canSpawn.Value do
 if tick() - t0 > 5 then return nil end
 task.wait(0.1)
 end
 local myHRP = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
 if not myHRP then return nil end
 local spawnCF = myHRP.CFrame * CFrame.new(0, 14, 20)
 local inv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 if not inv then return nil end
 local spawned = nil
 local conn
 conn = inv.ChildAdded:Connect(function(child)
 if child.Name == "PalletLightBrown" then spawned = child end
 end)
 task.spawn(function()
 pcall(function() spawnRemote:InvokeServer("PalletLightBrown", spawnCF, Vector3.zero) end)
 end)
 local startT = tick()
 repeat task.wait() until spawned or (tick() - startT) > 2.5
 conn:Disconnect()
 if spawned then
 spawned:WaitForChild("SoundPart", 3)
 end
 return spawned
end

-- Подготовка палетки при ВКЛ тумблера (как в NoName)
local function ragdollGrabPreparePalete()
 local inv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 if inv and inv:FindFirstChild("RagdollPalete") then
 ragdollPalete = inv:FindFirstChild("RagdollPalete")
 return
 end
 ragdollPalete = ragdollGrabSpawnPalete()
 if not ragdollPalete then return end
 local soundPart = ragdollPalete:FindFirstChild("SoundPart")
 if not soundPart then return end
 local setOwner = freezeGrabGetNetworkOwner()
 local oldCF = LocalPlayer.Character and LocalPlayer.Character:GetPivot()
 -- Ждём network ownership: PivotTo к палетке + DestroyGrabLine (sno)
 while not ragdollGrabCheckOwner(soundPart) do
 if not soundPart or not soundPart.Parent then return end
 if LocalPlayer.Character then
 pcall(function() LocalPlayer.Character:PivotTo(soundPart.CFrame) end)
 end
 if setOwner then pcall(function() setOwner:FireServer(soundPart, soundPart.CFrame) end) end
 task.wait(0.05)
 end
 -- Делаем прозрачной и неколлизионной
 for _, v in pairs(ragdollPalete:GetChildren()) do
 if v:IsA("BasePart") then
 v.Transparency = 0.8
 v.CanCollide = false
 v.CanQuery = false
 end
 end
 -- Возвращаем игрока на исходную позицию
 if oldCF and LocalPlayer.Character then
 pcall(function() LocalPlayer.Character:PivotTo(oldCF) end)
 end
 ragdollPalete.Name = "RagdollPalete"
 -- BodyVelocity вверх 900 на SoundPart
 local bv = Instance.new("BodyVelocity")
 bv.MaxForce = Vector3.new(0, math.huge, 0)
 bv.Velocity = Vector3.new(0, 900, 0)
 bv.Parent = soundPart
end

local function ragdollGrabApply(targetModel)
 if not targetModel then return end
 local hum = targetModel:FindFirstChildOfClass("Humanoid")
 if not hum then return end
 local root = targetModel:FindFirstChild("HumanoidRootPart")
 if not root then return end

 local setOwner = freezeGrabGetNetworkOwner()

 task.spawn(function()
 -- Проверяем/создаём палетку
 if not ragdollPalete or not ragdollPalete.Parent then
 ragdollGrabPreparePalete()
 end
 if not ragdollPalete or not ragdollPalete.Parent then return end
 local soundPart = ragdollPalete:FindFirstChild("SoundPart")
 if not soundPart then return end

 -- Если ownership потеряли — пытаемся вернуть через sno
 if not ragdollGrabCheckOwner(soundPart) then
 local t0 = tick()
 while not ragdollGrabCheckOwner(soundPart) and tick() - t0 < 5 do
 if setOwner then pcall(function() setOwner:FireServer(soundPart, soundPart.CFrame) end) end
 task.wait(0.05)
 end
 end

 -- Цикл: позиционируем SoundPart на RootPart цели пока GrabParts существует
 while true do
 local ragdolled = hum:FindFirstChild("Ragdolled")
 if not root or not root.Parent then break end
 if not workspace:FindFirstChild("GrabParts") then break end
 if ragdolled and ragdolled.Value then break end
 soundPart.Position = root.Position
 task.wait(0.1)
 end
 end)

 Library:Notify("Ragdoll Grab: цель рагдоллнута", 1)
end

-- Fling Grab: как Super Grab, но подбрасывает цель вертикально высоко в небо
flingGrabTargetPart = nil

local function flingGrabApply(targetPart)
 if not targetPart or not targetPart:IsA("BasePart") or not targetPart.Parent then return end

 local setOwner = freezeGrabGetNetworkOwner()
 if setOwner then
 pcall(function() setOwner:FireServer(targetPart, targetPart.CFrame) end)
 end

 pcall(function()
 targetPart.AssemblyLinearVelocity = Vector3.new(0, 400, 0)
 end)

 local bv = Instance.new("BodyVelocity")
 bv.Name = "FlingGrabVelocity"
 bv.MaxForce = Vector3.new(0, math.huge, 0)
 bv.P = 100000
 bv.Velocity = Vector3.new(0, 400, 0)
 bv.Parent = targetPart
 game:GetService("Debris"):AddItem(bv, 0.5)

 Library:Notify("Fling Grab: цель подброшена в небо", 1)
end

-- Kill Grab (логика из NoName / ForceDeath):
-- 1) Ждём network ownership на цели
-- 2) Сдвигаем все BasePart в крайние координаты (CFrame -999e9, 999e9, -999e9)
-- 3) BodyVelocity с огромной скоростью вверх на Root
-- 4) Humanoid.Sit=false, Jump=true, BreakJointsOnDeath=false
-- 5) Humanoid:ChangeState(Dead)
-- 6) DestroyGrabLine → рвём захват
local function killGrabApply(targetModel)
 if not targetModel then return end
 local hum = targetModel:FindFirstChildOfClass("Humanoid")
 if not hum then return end
 local root = targetModel:FindFirstChild("HumanoidRootPart")
 if not root then return end

 local setOwner = freezeGrabGetNetworkOwner()
 local destroyLine = combatGrabGetDestroyLine()

 task.spawn(function()
 -- Ждём network ownership (проверяем через PartOwner на Head)
 local head = targetModel:FindFirstChild("Head")
 local t0 = tick()
 while head and head:FindFirstChild("PartOwner") and head["PartOwner"].Value ~= LocalPlayer.Name do
 if not workspace:FindFirstChild("GrabParts") then return end
 if tick() - t0 > 10 then return end
 if setOwner then
 pcall(function() setOwner:FireServer(root, root.CFrame) end)
 end
 task.wait(0.05)
 end

 -- ForceDeath: сдвигаем все части в крайние координаты
 local deathCF = CFrame.new(-999999999999, 9999999999999, -999999999999)
 for _, part in pairs(targetModel:GetChildren()) do
 if part:IsA("BasePart") then
 pcall(function() part.CFrame = deathCF end)
 end
 end
 task.wait()
 -- Повторяем для надёжности
 for _, part in pairs(targetModel:GetChildren()) do
 if part:IsA("BasePart") then
 pcall(function() part.CFrame = deathCF end)
 end
 end

 -- BodyVelocity с огромной скоростью вверх на Root
 local bv = Instance.new("BodyVelocity")
 bv.Velocity = Vector3.new(0, 99999999999, 0)
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.P = 100000075
 bv.Parent = root

 -- Убиваем
 pcall(function()
 hum.Sit = false
 hum.Jump = true
 hum.BreakJointsOnDeath = false
 hum:ChangeState(Enum.HumanoidStateType.Dead)
 end)

 -- Чистим BodyVelocity через 2 сек
 task.delay(2, function()
 if bv and bv.Parent then bv:Destroy() end
 end)

 -- Рвём захват
 if setOwner then
 pcall(function() destroyLine:FireServer(root) end)
 end
 end)

 Library:Notify("Kill Grab: цель убита", 1)
end

workspace.ChildAdded:Connect(function(child)
 if child.Name ~= "GrabParts" then return end

 task.spawn(function()
 task.wait(0.05)
 local targetModel = combatGrabGetTargetModel(child)
 if not targetModel then return end

 if Toggles.EnableCombatGrab and Toggles.EnableCombatGrab.Value then
 ragdollGrabApply(targetModel)
 end

 if Toggles.EnableKillGrab and Toggles.EnableKillGrab.Value then
 killGrabApply(targetModel)
 end

 if Toggles.EnableFlingGrab and Toggles.EnableFlingGrab.Value then
 local hum = targetModel:FindFirstChildOfClass("Humanoid")
 local root = targetModel:FindFirstChild("HumanoidRootPart")
 if root and hum and hum.Health > 0 then
 flingGrabTargetPart = root
 end
 end

 -- Spin Grab: ������ращаем захваченную цель
 if Toggles.EnableSpinGrab and Toggles.EnableSpinGrab.Value then
 local primary = targetModel.PrimaryPart or targetModel:FindFirstChild("HumanoidRootPart") or targetModel:FindFirstChildWhichIsA("BasePart")
 if primary then
 local bav = Instance.new("BodyAngularVelocity")
 bav.Name = "SpinGrabBAV"
 bav.AngularVelocity = Vector3.new(0, Options.SpinGrabSpeed and Options.SpinGrabSpeed.Value or 30, 0)
 bav.MaxTorque = Vector3.new(0, math.huge, 0)
 bav.Parent = primary
 spinGrabTargets[primary] = bav
 end
 end

 -- Massless Grab: линия без физики, цель очень лёгкая
 if Toggles.EnableMasslessGrab and Toggles.EnableMasslessGrab.Value then
 task.spawn(function()
 local dragPart = child:WaitForChild("DragPart", 5)
 if not dragPart then return end
 local alignPos = dragPart:WaitForChild("AlignPosition", 5)
 local alignOri = dragPart:WaitForChild("AlignOrientation", 5)
 if not alignPos or not alignOri then return end
 local setOwner = freezeGrabGetNetworkOwner()
 if setOwner then
 pcall(function() setOwner:FireServer(dragPart, dragPart.CFrame) end)
 task.wait(0.1)
 end
 local dragPart1 = child:FindFirstChild("DragPart1")
 if dragPart1 then
 local dp1AlignPos = dragPart1:WaitForChild("AlignPosition", 5)
 if setOwner then
 pcall(function() setOwner:FireServer(dragPart1, dragPart1.CFrame) end)
 task.wait(0.1)
 end
 while workspace:FindFirstChild("GrabParts") and task.wait() do
 if dp1AlignPos then
 dp1AlignPos.Responsiveness = 200
 dp1AlignPos.MaxForce = math.huge
 dp1AlignPos.MaxVelocity = math.huge
 end
 alignOri.Responsiveness = 200
 alignOri.MaxTorque = math.huge
 end
 else
 while workspace:FindFirstChild("GrabParts") and task.wait() do
 alignPos.Responsiveness = 200
 alignOri.Responsiveness = 200
 alignPos.MaxForce = math.huge
 alignPos.MaxVelocity = math.huge
 alignOri.MaxTorque = math.huge
 end
 end
 end)
 end

 -- Invisible Grab: убрано — теперь использует спам CreateGrabLine (см. ApplyInvisLine выше)

 -- Noclip Grab: loop-based CanCollide=false, NO root ownership
 if Toggles.EnableNoclipGrab and Toggles.EnableNoclipGrab.Value then
 task.spawn(function()
 local dragPart = child:WaitForChild("DragPart", 5)
 local setOwner = freezeGrabGetNetworkOwner()
 if setOwner and dragPart then
 pcall(function() setOwner:FireServer(dragPart, dragPart.CFrame) end)
 task.wait(0.1)
 end
 local dragPart1 = child:FindFirstChild("DragPart1")
 if setOwner and dragPart1 then
 pcall(function() setOwner:FireServer(dragPart1, dragPart1.CFrame) end)
 task.wait(0.1)
 end
 if dragPart then noclipGrabTargets[dragPart] = dragPart.CanCollide end
 if dragPart1 then noclipGrabTargets[dragPart1] = dragPart1.CanCollide end
 for _, part in ipairs(targetModel:GetDescendants()) do
 if part:IsA("BasePart") then
 noclipGrabTargets[part] = part.CanCollide
 end
 end
 while workspace:FindFirstChild("GrabParts") and task.wait() do
 if dragPart then pcall(function() dragPart.CanCollide = false end) end
 if dragPart1 then pcall(function() dragPart1.CanCollide = false end) end
 for _, part in ipairs(targetModel:GetDescendants()) do
 if part:IsA("BasePart") then
 pcall(function() part.CanCollide = false end)
 end
 end
 end
 end)
 end
 end)
end)

workspace.ChildRemoved:Connect(function(child)
 if child.Name ~= "GrabParts" then return end
 if Toggles.EnableFlingGrab and Toggles.EnableFlingGrab.Value and flingGrabTargetPart and flingGrabTargetPart.Parent then
 flingGrabApply(flingGrabTargetPart)
 end
 for primary, bav in pairs(spinGrabTargets) do
 if bav and bav.Parent then bav:Destroy() end
 end
 spinGrabTargets = {}
 for obj, data in pairs(invisibleGrabTargets) do
 if obj and obj.Parent then
 if data.isBeam then
 pcall(function() obj.Enabled = data.enabled end)
 else
 pcall(function() obj.Transparency = data.trans end)
 end
 end
 end
 invisibleGrabTargets = {}
 for part, origCollide in pairs(noclipGrabTargets) do
 if part and part.Parent then
 pcall(function() part.CanCollide = origCollide end)
 end
 end
 noclipGrabTargets = {}
 flingGrabTargetPart = nil
end)


-- ==============================================
-- Strong Throw (Автоматическое усиление броска)
-- ==============================================
local StrongThrow = Tabs.Combat:AddLeftGroupbox("Strong Throw")

StrongThrow:AddToggle("EnableStrongThrow", {
 Text = "Strong Throw",
 Default = false,
 Tooltip = "ПКМ бросает захваценную цель с усиленной силой",
})

StrongThrow:AddSlider("StrongThrowPower", {
 Text = "Сила броска",
 Default = 100,
 Min = 1,
 Max = 1000,
 Rounding = 0,
 Compact = false,
 Tooltip = "Сила броска ПКМ — это прямое значение скорости, без процентов",
})

strongThrowObj = nil
strongThrowConnections = {}

local function cleanupStrongThrow()
 for _, conn in ipairs(strongThrowConnections) do
 pcall(function() conn:Disconnect() end)
 end
 strongThrowConnections = {}
 strongThrowObj = nil
end

Toggles.EnableStrongThrow:OnChanged(function()
 if not Toggles.EnableStrongThrow.Value then
 cleanupStrongThrow()
 return
 end

 table.insert(strongThrowConnections, workspace.ChildAdded:Connect(function(c)
 if c.Name == "GrabParts" then
 local part = c:FindFirstChild("GrabPart") or c:WaitForChild("GrabPart", 1)
 if part then
 local weld = part:FindFirstChild("WeldConstraint") or part:WaitForChild("WeldConstraint", 1)
 if weld then
 strongThrowObj = weld.Part1
 end
 end
 end
 end))

 table.insert(strongThrowConnections, UserInputService.InputBegan:Connect(function(inp)
 if inp.UserInputType == Enum.UserInputType.MouseButton2 then
 local obj = strongThrowObj
 if obj and obj.Parent then
 local bv = Instance.new("BodyVelocity", obj)
 local Camera = workspace.CurrentCamera
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.Velocity = Camera.CFrame.LookVector * Options.StrongThrowPower.Value
 game:GetService("Debris"):AddItem(bv, 4)
 strongThrowObj = nil
 Library:Notify("Strong Throw: бросок ПКМ", 1)
 end
 end
 end))

 table.insert(strongThrowConnections, workspace.ChildRemoved:Connect(function(desc)
 if desc.Name == "GrabParts" then
 task.delay(1, function()
 strongThrowObj = nil
 end)
 end
 end))

 Library:Notify("Strong Throw ВКЛ: ПКМ бросает цель", 2)
end)

-- ==============================================
-- Super Grab (бросок при естественном отпускании захвата)
-- ==============================================
local SuperGrab = Tabs.Combat:AddLeftGroupbox("Super Grab")

SuperGrab:AddToggle("EnableSuperGrab", {
 Text = "Super Grab",
 Default = false,
 Tooltip = "Бросает захваченную цель вперёд при естественном отпускании захвата",
})

SuperGrab:AddSlider("SuperGrabPower", {
 Text = "Сила Super Grab",
 Default = 100,
 Min = 1,
 Max = 1000,
 Rounding = 0,
 Compact = false,
 Tooltip = "Сила броска при отпускании (1-1000)",
})

SuperGrab:AddDropdown("SuperGrabTargets", {
 Text = "На что действует",
 Default = {"Люди", "Предметы"},
 Values = {"Люди", "Предметы"},
 Multi = true,
 Tooltip = "Выбери на что будет действовать сила",
})

superGrabTargetPart = nil

local function applySuperGrab(targetPart)
 if not targetPart or not targetPart:IsA("BasePart") or not targetPart.Parent then return end

 local targetModel = targetPart.Parent
 local throwPart = targetPart
 if targetModel and targetModel:FindFirstChildOfClass("Humanoid") and targetModel:FindFirstChild("HumanoidRootPart") then
 throwPart = targetModel.HumanoidRootPart
 end

 if throwPart.Parent == LocalPlayer.Character then return end
 local throwPlayer = throwPart.Parent and Players:GetPlayerFromCharacter(throwPart.Parent)
 if throwPlayer == LocalPlayer then return end

 local power = Options.SuperGrabPower and Options.SuperGrabPower.Value or 100
 local cam = workspace.CurrentCamera
 if not cam then return end

 local dir = cam.CFrame.LookVector
 dir = dir
 local force = dir * (power * 20)

 local setOwner = freezeGrabGetNetworkOwner()
 if setOwner then
 pcall(function() setOwner:FireServer(throwPart, throwPart.CFrame) end)
 end

 pcall(function()
 throwPart.AssemblyLinearVelocity = force
 end)

 local bv = Instance.new("BodyVelocity")
 bv.Name = "SuperGrabVelocity"
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.P = 100000
 bv.Velocity = force
 bv.Parent = throwPart
 game:GetService("Debris"):AddItem(bv, 0.15)

 Library:Notify("Super Grab: бросок (сила " .. tostring(power) .. ")", 1)
end

-- Отслеживание захваченных целей (для Super Grab)
workspace.ChildAdded:Connect(function(child)
 if child.Name == "GrabParts" then
 if not (Toggles.EnableSuperGrab and Toggles.EnableSuperGrab.Value) then return end
 superGrabTargetPart = nil
 local grabPart = child:WaitForChild("GrabPart", 1)
 if not grabPart then return end
 local weld = grabPart:WaitForChild("WeldConstraint", 1)
 if not weld then return end
 local targetPart = weld.Part1
 local targetModel = targetPart and targetPart.Parent
 local targetPlayer = targetModel and Players:GetPlayerFromCharacter(targetModel)
 local sgTargets = Options.SuperGrabTargets and Options.SuperGrabTargets.Value or {}
 local sgHitPlayers = sgTargets["Люди"] or false
 local sgHitObjects = sgTargets["Предметы"] or false
 if targetPart and targetPart.Parent and targetModel ~= LocalPlayer.Character and ((sgHitPlayers and targetPlayer and targetPlayer ~= LocalPlayer) or (sgHitObjects and not targetPlayer and not targetPart.Anchored)) then
 superGrabTargetPart = targetPart
 end
 end
end)

workspace.ChildRemoved:Connect(function(child)
 if child.Name == "GrabParts" then
 if Toggles.EnableSuperGrab and Toggles.EnableSuperGrab.Value and superGrabTargetPart and superGrabTargetPart.Parent then
 applySuperGrab(superGrabTargetPart)
 end
 superGrabTargetPart = nil
 end
end)




-- ==============================================


-- ==============================================
end
-- Combat: Auras
-- ==============================================
local CombatAuras = Tabs.Combat:AddRightGroupbox("Auras (Targets & Range)")

CombatAuras:AddSlider("AuraDistance", {
    Text = "Aura Distance",
    Default = 20,
    Min = 0,
    Max = 30,
    Rounding = 0,
    Compact = false,
    Tooltip = "Радиус действия всех аур",
})

CombatAuras:AddToggle("TargetPlayers", {
    Text = "Target Players",
    Default = true,
    Tooltip = "Действует ли на игроков",
})

CombatAuras:AddToggle("TargetItems", {
    Text = "Target Items",
    Default = false,
    Tooltip = "Действует ли на предметы",
})

local function getAuraTargets(rootPos)
    local targets = {}
    local dist = Options.AuraDistance.Value
    
    if Toggles.TargetPlayers.Value then
        for _, player in pairs(game:GetService("Players"):GetPlayers()) do
            if player ~= game:GetService("Players").LocalPlayer and player.Character then
                local hrp = player.Character:FindFirstChild("HumanoidRootPart")
                if hrp and (hrp.Position - rootPos).Magnitude <= dist then
                    table.insert(targets, hrp)
                end
            end
        end
    end
    
    if Toggles.TargetItems.Value then
        for _, obj in ipairs(workspace:GetPartBoundsInRadius(rootPos, dist)) do
            if obj:IsA("BasePart") and not obj.Anchored and obj.Parent ~= game:GetService("Players").LocalPlayer.Character then
                local isPlayerPart = false
                if not Toggles.TargetPlayers.Value then
                    for _, p in pairs(game:GetService("Players"):GetPlayers()) do
                        if p.Character and p.Character:IsAncestorOf(obj) then
                            isPlayerPart = true
                            break
                        end
                    end
                end
                if not isPlayerPart then
                    table.insert(targets, obj)
                end
            end
        end
    end
    return targets
end

local function getMyRoot()
    local c = game:GetService("Players").LocalPlayer.Character
    return c and c:FindFirstChild("HumanoidRootPart")
end

local function setNetOwner(part)
    pcall(function() game:GetService("ReplicatedStorage").GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame) end)
end

-- 1. Fling Aura (ОСЛАБЛЕНА: множители уменьшены в 25 раз от оригинала)
CombatAuras:AddToggle("FlingAura", {Text = "Fling Aura", Default = false})
CombatAuras:AddSlider("FlingAuraPower", {Text = "Fling Aura Power", Default = 5, Min = 1, Max = 100, Rounding = 0, Compact = false})
runningFlingAura = false
Toggles.FlingAura:OnChanged(function()
    runningFlingAura = Toggles.FlingAura.Value
    if runningFlingAura then
        task.spawn(function()
            while runningFlingAura do
                local root = getMyRoot()
                if root then
                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        pcall(function()
                            setNetOwner(target)
                            local pwr = Options.FlingAuraPower.Value
                            local bv = Instance.new("BodyVelocity", target)
                            -- Ослаблено ещё в 5 раз: было 30*pwr по X/Z и 100*pwr по Y
                            -- Теперь 6*pwr по X/Z и 20*pwr по Y
                            -- При pwr=5: Y=100 (было 500), XZ=30 (было 150)
                            bv.Velocity = Vector3.new(
                                math.random(-1, 1) * (6 * pwr),
                                20 * pwr,
                                math.random(-1, 1) * (6 * pwr)
                            )
                            bv.MaxForce = Vector3.one * math.huge
                            game:GetService("Debris"):AddItem(bv, 0.2)
                        end)
                    end
                end
                task.wait()
            end
        end)
    end
end)

-- 2. Void Aura
CombatAuras:AddToggle("VoidAura", {Text = "Void Aura", Default = false})
runningVoidAura = false
Toggles.VoidAura:OnChanged(function()
    runningVoidAura = Toggles.VoidAura.Value
    if runningVoidAura then
        task.spawn(function()
            while runningVoidAura do
                local root = getMyRoot()
                if root then
                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        pcall(function()
                            setNetOwner(target)
                            -- Отправляем жестко вниз и фиксируем велосити
                            target.CFrame = target.CFrame - Vector3.new(0, 1500, 0)
                            local bv = Instance.new("BodyVelocity", target)
                            bv.Velocity = Vector3.new(0, -9e9, 0)
                            bv.MaxForce = Vector3.one * math.huge
                            game:GetService("Debris"):AddItem(bv, 0.2)
                        end)
                    end
                end
                task.wait(0.05)
            end
        end)
    end
end)

-- 3. Kill Aura (Death Aura — перенос из слива)
CombatAuras:AddToggle("KillAura", {Text = "Kill Aura", Default = false})
runningKillAura = false
killAuraTask = nil
Toggles.KillAura:OnChanged(function()
    runningKillAura = Toggles.KillAura.Value
    if runningKillAura then
        killAuraTask = task.spawn(function()
            local GE = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
            local SetNetworkOwner = GE:WaitForChild("SetNetworkOwner")
            local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine")
            while runningKillAura do
                local root = getMyRoot()
                if root then
                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        pcall(function()
                            local targetModel = target.Parent
                            local head = targetModel:FindFirstChild("Head")
                            local hum = targetModel:FindFirstChildOfClass("Humanoid")
                            if hum and hum.Health > 0 and head then
                                -- 1. Забираем сетевую ownership
                                SetNetworkOwner:FireServer(target, target.CFrame)
                                task.wait(0.1)
                                -- 2. Рвём линию захвата
                                DestroyGrabLine:FireServer(target)
                                -- 3. Если игрок теперь "владеет" целью — убиваем
                                if head:FindFirstChild("PartOwner") and head.PartOwner.Value == LocalPlayer.Name then
                                    -- Телепорт всех частей далеко (дважды, как в сливе)
                                    for _, part in pairs(targetModel:GetChildren()) do
                                        if part:IsA("BasePart") then
                                            part.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    task.wait()
                                    for _, part in pairs(targetModel:GetChildren()) do
                                        if part:IsA("BasePart") then
                                            part.CFrame = CFrame.new(-1000000000, 1000000000, -1000000000)
                                        end
                                    end
                                    -- BodyVelocity вниз
                                    local bv = Instance.new("BodyVelocity")
                                    bv.Velocity = Vector3.new(0, -9999999, 0)
                                    bv.MaxForce = Vector3.new(9000000000, 9000000000, 9000000000)
                                    bv.P = 100000075
                                    bv.Parent = target
                                    hum.Sit = false
                                    hum.Jump = true
                                    hum.BreakJointsOnDeath = false
                                    hum:ChangeState(Enum.HumanoidStateType.Dead)
                                    task.delay(2, function()
                                        if bv and bv.Parent then bv:Destroy() end
                                    end)
                                end
                            end
                        end)
                    end
                end
                task.wait(0.1)
            end
        end)
    else
        if killAuraTask then task.cancel(killAuraTask) killAuraTask = nil end
    end
end)

-- 4. Spin Aura (при Height>=20 — орбита над головой, цели не улетают)
CombatAuras:AddToggle("SpinAura", {Text = "Spin Aura", Default = false})
CombatAuras:AddSlider("SpinAuraSpeed", {Text = "Spin Speed", Default = 5, Min = 1, Max = 100, Rounding = 0, Compact = false})
CombatAuras:AddSlider("SpinAuraHeight", {Text = "Spin Height", Default = 5, Min = -50, Max = 50, Rounding = 0, Compact = false})
runningSpinAura = false
spinAuraTask = nil
Toggles.SpinAura:OnChanged(function()
    runningSpinAura = Toggles.SpinAura.Value
    if runningSpinAura then
        spinAuraTask = task.spawn(function()
            local spinAngle = 0
            while runningSpinAura do
                pcall(function()
                    local root = getMyRoot()
                    if not root then return end
                    local spd = Options.SpinAuraSpeed.Value
                    local heightVal = Options.SpinAuraHeight.Value
                    spinAngle = spinAngle + (spd / 100)
                    if spinAngle >= 6.28 then spinAngle = 0 end

                    -- Если Height >= 20 — орбита над головой (цели не улетают, кружатся над игроком)
                    local isOrbitMode = heightVal >= 20
                    -- Радиус орбиты/спирали
                    local radius = isOrbitMode and 6 or 10
                    -- Высота: при орбите используем Height напрямую (как высоту над головой)
                    -- при обычном режиме — как смещение по Y
                    local yOffset = isOrbitMode and math.max(heightVal, 5) or heightVal

                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        local targetPosition = root.Position + Vector3.new(
                            math.cos(spinAngle) * radius,
                            yOffset,
                            math.sin(spinAngle) * radius
                        )
                        pcall(function()
                            setNetOwner(target)
                            local p = target.Parent
                            local hum = p and p:FindFirstChildOfClass("Humanoid")
                            if hum then
                                hum.PlatformStand = true
                            end
                            -- BodyVelocity тянем к targetPosition
                            local bv = target:FindFirstChild("SpinAuraBV") or Instance.new("BodyVelocity")
                            bv.Name = "SpinAuraBV"
                            -- В орбите — сильнее держим (не даём улететь), в обычном — мягче
                            local pullStrength = isOrbitMode and 35 or 25
                            bv.Velocity = (targetPosition - target.Position) * pullStrength
                            bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            bv.P = 10000
                            bv.Parent = target
                            -- BodyGyro: цель смотрит на игрока
                            local bg = target:FindFirstChild("SpinAuraBG") or Instance.new("BodyGyro")
                            bg.Name = "SpinAuraBG"
                            bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            bg.P = 10000
                            bg.D = 500
                            bg.CFrame = CFrame.new(target.Position, root.Position)
                            bg.Parent = target
                        end)
                    end
                end)
                task.wait(0.02)
            end
        end)
    else
        if spinAuraTask then task.cancel(spinAuraTask) spinAuraTask = nil end
        for _, p in pairs(game:GetService("Players"):GetPlayers()) do
            if p.Character then
                local hum = p.Character:FindFirstChildOfClass("Humanoid")
                if hum then hum.PlatformStand = false end
                local t = p.Character:FindFirstChild("HumanoidRootPart") or p.Character:FindFirstChild("Torso")
                if t then
                    if t:FindFirstChild("SpinAuraBV") then t.SpinAuraBV:Destroy() end
                    if t:FindFirstChild("SpinAuraBG") then t.SpinAuraBG:Destroy() end
                end
            end
        end
    end
end)

-- 5. Telekinesis Aura (XOCO HellSendAura — перенос из слива)
CombatAuras:AddToggle("TelekinesisAura", {Text = "Telekinesis Aura", Default = false})
runningTKAura = false
tkAuraTask = nil
Toggles.TelekinesisAura:OnChanged(function()
    runningTKAura = Toggles.TelekinesisAura.Value
    if runningTKAura then
        tkAuraTask = task.spawn(function()
            local GE = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
            local SetNetworkOwner = GE:WaitForChild("SetNetworkOwner")
            local Camera = workspace.CurrentCamera
            while runningTKAura do
                pcall(function()
                    local root = getMyRoot()
                    if not root then return end
                    local lookDir = Camera.CFrame.LookVector
                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        pcall(function()
                            local targetModel = target.Parent
                            -- Отключаем коллизию у всех частей цели
                            for _, desc in ipairs(targetModel:GetDescendants()) do
                                if desc:IsA("BasePart") then desc.CanCollide = false end
                            end
                            -- Забираем ownership
                            SetNetworkOwner:FireServer(target, root.CFrame)
                            -- BodyPosition: держим цель в 15 studs перед игроком + 5 вверх
                            local pos = target:FindFirstChild("HellAuraPos") or Instance.new("BodyPosition")
                            pos.Name = "HellAuraPos"
                            pos.MaxForce = Vector3.new(100000, 100000, 100000)
                            pos.D = 500
                            pos.P = 50000
                            pos.Position = root.Position + lookDir * 15 + Vector3.new(0, 5, 0)
                            pos.Parent = target
                            -- BodyGyro: цель смотрит на игрока
                            local gyro = target:FindFirstChild("HellAuraGyro") or Instance.new("BodyGyro")
                            gyro.Name = "HellAuraGyro"
                            gyro.MaxTorque = Vector3.new(100000, 100000, 100000)
                            gyro.D = 500
                            gyro.P = 50000
                            gyro.CFrame = CFrame.new(target.Position, root.Position)
                            gyro.Parent = target
                        end)
                    end
                end)
                task.wait(0.05)
            end
        end)
    else
        if tkAuraTask then task.cancel(tkAuraTask) tkAuraTask = nil end
        -- Очистка HellAura-объектов
        for _, p in pairs(game:GetService("Players"):GetPlayers()) do
            if p.Character then
                local t = p.Character:FindFirstChild("HumanoidRootPart") or p.Character:FindFirstChild("Torso")
                if t then
                    if t:FindFirstChild("HellAuraPos") then t.HellAuraPos:Destroy() end
                    if t:FindFirstChild("HellAuraGyro") then t.HellAuraGyro:Destroy() end
                end
            end
        end
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                if obj:FindFirstChild("HellAuraPos") then obj.HellAuraPos:Destroy() end
                if obj:FindFirstChild("HellAuraGyro") then obj.HellAuraGyro:Destroy() end
            end
        end
    end
end)

-- 6. Click Aura
CombatAuras:AddToggle("ClickAura", {Text = "Click Aura", Default = false})
clickAuraEnabled = false
clickAuraTask = nil
Toggles.ClickAura:OnChanged(function()
    clickAuraEnabled = Toggles.ClickAura.Value
    if clickAuraEnabled then
        clickAuraTask = task.spawn(function()
            while clickAuraEnabled do
                pcall(function()
                    local root = getMyRoot()
                    if not root then return end
                    for _, target in ipairs(getAuraTargets(root.Position)) do
                        pcall(function()
                            setNetOwner(target)
                            if not target:FindFirstChild("ClickAuraBV") then
                                local bv = Instance.new("BodyVelocity")
                                bv.Name = "ClickAuraBV"
                                bv.Velocity = Vector3.zero
                                bv.MaxForce = Vector3.one * math.huge
                                bv.Parent = target
                                local bg = Instance.new("BodyGyro")
                                bg.Name = "ClickAuraBG"
                                bg.MaxTorque = Vector3.one * math.huge
                                bg.CFrame = CFrame.new(target.Position, target.Position + Vector3.new(0, 0, 1))
                                bg.Parent = target
                            end
                        end)
                    end
                end)
                task.wait(0.05)
            end
        end)
    else
        if clickAuraTask then task.cancel(clickAuraTask) clickAuraTask = nil end
    end
end)

-- 7. Anti Anti Kick Aura
CombatAuras:AddToggle("DestroyAntiKickAura", {Text = "Anti Anti Kick Aura", Default = false})
runningDestroyAntiKickAura = false
Toggles.DestroyAntiKickAura:OnChanged(function()
    runningDestroyAntiKickAura = Toggles.DestroyAntiKickAura.Value
    if runningDestroyAntiKickAura then
        task.spawn(function()
            local GE = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
            local setNE = GE:WaitForChild("SetNetworkOwner")
            local createGL = GE:WaitForChild("CreateGrabLine")
            local destroyGL = GE:WaitForChild("DestroyGrabLine")
            local auraFrame = 0
            while runningDestroyAntiKickAura do
                local root = getMyRoot()
                if root then
                    for _, obj in ipairs(workspace:GetPartBoundsInRadius(root.Position, Options.AuraDistance.Value)) do
                        if obj:IsA("BasePart") and obj.Name == "StickyPart" and not game:GetService("Players").LocalPlayer.Character:IsAncestorOf(obj) then
                            if auraFrame % 3 == 0 then pcall(function() setNE:FireServer(obj, obj.CFrame) end)
                            elseif auraFrame % 3 == 1 then pcall(function() createGL:FireServer(obj, Vector3.zero, obj.Position, false) end)
                            else pcall(function() destroyGL:FireServer(obj) end) end
                        end
                    end
                    auraFrame = auraFrame + 1
                end
                task.wait()
            end
        end)
    end
end)

-- 8. Anti Banana Aura
CombatAuras:AddToggle("AntiBananaAura", {Text = "Anti Banana Aura", Default = false})
runningAntiBananaAura = false
Toggles.AntiBananaAura:OnChanged(function()
    runningAntiBananaAura = Toggles.AntiBananaAura.Value
    if runningAntiBananaAura then
        task.spawn(function()
            local GE = game:GetService("ReplicatedStorage"):WaitForChild("GrabEvents")
            local setNE = GE:WaitForChild("SetNetworkOwner")
            local createGL = GE:WaitForChild("CreateGrabLine")
            local destroyGL = GE:WaitForChild("DestroyGrabLine")
            local auraFrame = 0
            while runningAntiBananaAura do
                local root = getMyRoot()
                if root then
                    for _, obj in ipairs(workspace:GetPartBoundsInRadius(root.Position, Options.AuraDistance.Value)) do
                        if obj:IsA("BasePart") and obj.Name == "HitboxPart" and not game:GetService("Players").LocalPlayer.Character:IsAncestorOf(obj) then
                            if auraFrame % 3 == 0 then pcall(function() setNE:FireServer(obj, obj.CFrame) end)
                            elseif auraFrame % 3 == 1 then pcall(function() createGL:FireServer(obj, Vector3.zero, obj.Position, false) end)
                            else pcall(function() destroyGL:FireServer(obj) end) end

                            pcall(function()
                                local direction = (obj.Position - root.Position).Unit
                                local bv = Instance.new("BodyVelocity")
                                bv.Velocity = direction * 5000 + Vector3.new(0, 2000, 0)
                                bv.MaxForce = Vector3.one * math.huge
                                bv.Parent = obj
                                game:GetService("Debris"):AddItem(bv, 0.3)
                            end)
                        end
                    end
                    auraFrame = auraFrame + 1
                end
                task.wait()
            end
        end)
    end
end)

-- ==============================================
-- COMBAT: LINE LAG + PACKET LAG
-- ==============================================
do
    local lagSection = Tabs.Combat:AddRightGroupbox("Server Lag")

    local function getGrabEventsFolder()
        return game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
    end

    -- === LINE LAG ===
    lagSection:AddSlider("LineLagPower", {
        Text = "Line Lag Power",
        Default = 500,
        Min = 0,
        Max = 1000,
        Rounding = 0,
        Compact = false,
        Tooltip = "Сколько линий создавать за кадр",
    })

    local lineLagEnabled = false
    local lineLagThread = nil

    local function startLineLag()
        if lineLagEnabled then return end
        lineLagEnabled = true
        lineLagThread = coroutine.create(function()
            local grabEvents = getGrabEventsFolder()
            if not grabEvents then return end
            local createLine = grabEvents:FindFirstChild("CreateGrabLine")
            if not createLine then return end
            while lineLagEnabled do
                local power = Options.LineLagPower and Options.LineLagPower.Value or 500
                if power > 0 then
                    local spawnLocation = workspace:FindFirstChild("SpawnLocation") or workspace:FindFirstChild("Spawn") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
                    if spawnLocation then
                        for _ = 1, power do
                            local randomX = math.random(-1e9, 1e9)
                            local randomZ = math.random(-1e9, 1e9)
                            local directions = {
                                CFrame.new(randomX, 0, randomZ),
                                CFrame.new(-randomX, 0, -randomZ),
                                CFrame.new(randomX, 0, -randomZ),
                                CFrame.new(-randomX, 0, randomZ)
                            }
                            for _, pos in pairs(directions) do
                                createLine:FireServer(spawnLocation, pos)
                            end
                        end
                    end
                end
                task.wait()
            end
        end)
        coroutine.resume(lineLagThread)
    end

    local function stopLineLag()
        lineLagEnabled = false
        if lineLagThread then
            coroutine.close(lineLagThread)
            lineLagThread = nil
        end
        -- Очистка: уничтожаем созданные граб-линии чтобы лаг прекратился сразу
        task.spawn(function()
            local grabEvents = getGrabEventsFolder()
            if not grabEvents then return end
            local destroyLine = grabEvents:FindFirstChild("DestroyGrabLine")
            if not destroyLine then return end
            local spawnLocation = workspace:FindFirstChild("SpawnLocation") or workspace:FindFirstChild("Spawn") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
            if not spawnLocation then return end
            -- Спамим DestroyGrabLine 200 раз чтобы очистить все созданные линии
            for _ = 1, 200 do
                pcall(function() destroyLine:FireServer(spawnLocation) end)
            end
        end)
    end

    lagSection:AddToggle("EnableLineLag", {
        Text = "Line Lag",
        Default = false,
        Tooltip = "Спамит CreateGrabLine с огромными координатами",
        Callback = function(v)
            if v then startLineLag() else stopLineLag() end
        end,
    })

    -- === PACKET LAG ===
    lagSection:AddSlider("PacketLagStrength", {
        Text = "Packet Lag Strength",
        Default = 6250,
        Min = 100,
        Max = 6250,
        Rounding = 0,
        Compact = false,
        Tooltip = "Сила Packet Lag",
    })

    local packetLagActive = false
    local packetLagTask = nil

    lagSection:AddToggle("EnablePacketLag", {
        Text = "Packet Lag",
        Default = false,
        Tooltip = "Спамит ExtendGrabLine с большой строкой",
        Callback = function(v)
            packetLagActive = v
            if v then
                packetLagTask = task.spawn(function()
                    local grabEvents = getGrabEventsFolder()
                    local extendGL = grabEvents and grabEvents:FindFirstChild("ExtendGrabLine")
                    if not extendGL then
                        packetLagActive = false
                        return
                    end
                    while packetLagActive do
                        task.wait(1)
                        local strength = Options.PacketLagStrength and Options.PacketLagStrength.Value or 6250
                        extendGL:FireServer(string.rep("������������������������������������������������", 100 * strength))
                    end
                end)
            else
                if packetLagTask then
                    task.cancel(packetLagTask)
                    packetLagTask = nil
                end
            end
        end,
    })
end


do
-- ==============================================
-- Вкладка: DEF — ANTI GRAB
-- ==============================================
-- Защита от захвата. В FTAP захват серверный: игрок берёт тебя
-- и может даже отбросить. Рабочий способ (из готовых скриптов):
-- • IsHeld (значение у игрока) — надёжный признак "меня держат";
-- • CharacterEvents.Struggle — легитимное "вырваться" (спамим);
-- • GameCorrectionEvents.StopAllVelocity — гасит бросок/отлёт;
-- • тело НЕ якорим — линия рвётся, а ты продолжаешь свободно идти;
-- • снимаем Sit/PlatformStand, чтобы управление не залипало от спам-кликов.
DefSection = Tabs.Def:AddLeftGroupbox("", nil, nil, nil, true)
DefAntiGrab = DefSection

DefAntiGrab:AddToggle("EnableAntiGrab", {
    Text = "Anti Grab [BEST]",
    Default = false,
    Tooltip = "Ходишь не замечая что тебя берут",
})

antiGrabRS = game:GetService("ReplicatedStorage")
antiGrabProc = false
antiGrabConns = {}

function antiGrabStruggleEvent()
    local ce = antiGrabRS:FindFirstChild("CharacterEvents")
    return ce and ce:FindFirstChild("Struggle")
end

function antiGrabRagdollRemote()
    local ce = antiGrabRS:FindFirstChild("CharacterEvents")
    return ce and (ce:FindFirstChild("RagdollRemote") or ce:WaitForChild("RagdollRemote", 5))
end

function antiGrabStopVelocityEvent()
    local ge = antiGrabRS:FindFirstChild("GameCorrectionEvents")
    return ge and ge:FindFirstChild("StopAllVelocity")
end

function antiGrabIsHeld()
    local char = LocalPlayer.Character
    if not char then return false end
    local head = char:FindFirstChild("Head")
    local po = head and head:FindFirstChild("PartOwner")
    if po then
        local ownerName = tostring(po.Value)
        if ownerName ~= "" and ownerName ~= LocalPlayer.Name then
            local ownerPlayer = game:GetService("Players"):FindFirstChild(ownerName)
            if ownerPlayer and ownerPlayer.Character then
                for _, obj in ipairs(workspace:GetChildren()) do
                    if obj.Name == "CreatureBlobman" and obj:IsA("Model") then
                        if ownerPlayer.Character:IsDescendantOf(obj) then return false end
                    end
                end
            end
            return true
        end
    end
    local isHeld = LocalPlayer:FindFirstChild("IsHeld")
    if isHeld and isHeld.Value then return true end
    return false
end

-- Disable ragdoll constraints to prevent limbs from flopping
function antiGrabDisableRagdoll(char)
    for _, v in pairs(char:GetChildren()) do
        if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
            v.BallSocketConstraint.Enabled = false
            if v:FindFirstChild("RagdollLimbPart") then
                v.RagdollLimbPart.WeldConstraint.Enabled = false
            end
        end
    end
end

function antiGrabEnableRagdoll(char)
    for _, v in pairs(char:GetChildren()) do
        if v:IsA("BasePart") and v:FindFirstChild("BallSocketConstraint") and v.Name ~= "Head" then
            v.BallSocketConstraint.Enabled = true
            if v:FindFirstChild("RagdollLimbPart") then
                v.RagdollLimbPart.WeldConstraint.Enabled = true
            end
        end
    end
end

function antiGrabApply(char)
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    local head = char:FindFirstChild("Head")
    if not (hrp and hum and head) then return end
    
    -- Disable ragdoll constraints immediately
    antiGrabDisableRagdoll(char)
    
    -- Monitor Head.ChildAdded for PartOwner (someone grabs you)
    if antiGrabConns["Head"] then antiGrabConns["Head"]:Disconnect() end
    antiGrabConns["Head"] = head.ChildAdded:Connect(function(partOwner)
        if partOwner.Name ~= "PartOwner" or antiGrabProc then return end
        antiGrabProc = true
        hum.Sit = false
        
        local struggle = antiGrabStruggleEvent()
        local ragdollRemote = antiGrabRagdollRemote()
        
        -- Anchor HRP so grabber cant pull/slow us, then CFrame teleport at normal speed
        -- NO velocity zeroing, NO StopAllVelocity (those caused throw + acceleration)
        hrp.Anchored = true
        task.spawn(function()
            while (head and head:FindFirstChild("PartOwner")) or (LocalPlayer:FindFirstChild("IsHeld") and LocalPlayer.IsHeld.Value) do
                if struggle then pcall(function() struggle:FireServer(LocalPlayer) end) end
                if ragdollRemote then pcall(function() ragdollRemote:FireServer(hrp, 0) end) end
                -- Walk at exact normal speed (WalkSpeed / 60fps = studs per frame)
                pcall(function()
                    hrp.CFrame = hrp.CFrame + hum.MoveDirection * (hum.WalkSpeed / 60)
                    hum.PlatformStand = false
                    hum.Sit = false
                    hum.AutoRotate = true
                end)
                -- Force head position if WeldHRP is enabled
                if hrp:FindFirstChild("WeldHRP") and hrp.WeldHRP.Enabled then
                    pcall(function() head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) end)
                end
                task.wait()
            end
            -- Release: unanchor, no velocity changes (prevents throw)
            pcall(function() hrp.Anchored = false end)
            -- Destroy ALL frozen grab lines on ALL players (aggressive cleanup)
            task.spawn(function()
                task.wait(0.3)
                local ge = antiGrabRS:FindFirstChild("GrabEvents")
                local dgl = ge and ge:FindFirstChild("DestroyGrabLine")
                if dgl then
                    for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
                        if plr ~= LocalPlayer and plr.Character then
                            local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
                            local tHead = plr.Character:FindFirstChild("Head")
                            -- Destroy on HRP (3x for reliability)
                            if tRoot then
                                for _ = 1, 3 do
                                    pcall(function() dgl:FireServer(tRoot) end)
                                end
                            end
                            -- Destroy on Head too
                            if tHead then
                                pcall(function() dgl:FireServer(tHead) end)
                            end
                            -- Also destroy on any part with PartOwner
                            for _, v in ipairs(plr.Character:GetDescendants()) do
                                if v.Name == "PartOwner" then
                                    pcall(function() dgl:FireServer(v.Parent) end)
                                end
                            end
                        end
                    end
                end
            end)
            antiGrabProc = false
        end)
    end)
    
    -- Monitor Sit (un-sit if not on blobman)
    if antiGrabConns["Hum"] then antiGrabConns["Hum"]:Disconnect() end
    antiGrabConns["Hum"] = hum.Changed:Connect(function(prop)
        if prop == "Sit" and hum.Sit then
            if not (hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman") then
                hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
                hum.Sit = false
            end
        end
    end)
    
    -- Monitor WeldHRP (if welded, force head position + un-sit)
    local weldHRP = hrp:FindFirstChild("WeldHRP")
    if weldHRP then
        if antiGrabConns["Weld"] then antiGrabConns["Weld"]:Disconnect() end
        antiGrabConns["Weld"] = weldHRP.Changed:Connect(function()
            if not hrp.WeldHRP.Enabled then return end
            task.spawn(function()
                while not hum.Sit do task.wait() end
                hum.Sit = false
                hum.AutoRotate = true
                while hrp.WeldHRP.Enabled do
                    pcall(function() head.CFrame = hrp.CFrame + Vector3.new(0, 1.35, 0) end)
                    task.wait()
                end
            end)
        end)
    end
    
    -- Monitor Ragdolled value
    local ragdolled = hum:FindFirstChild("Ragdolled")
    if ragdolled then
        if antiGrabConns["Ragdoll"] then antiGrabConns["Ragdoll"]:Disconnect() end
        antiGrabConns["Ragdoll"] = ragdolled.Changed:Connect(function()
            if hum.Ragdolled.Value then
                antiGrabDisableRagdoll(char)
                pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
            end
        end)
    end
end

function antiGrabCleanup()
    for k, conn in pairs(antiGrabConns) do
        if conn then conn:Disconnect() end
    end
    antiGrabConns = {}
    antiGrabProc = false
end

Toggles.EnableAntiGrab:OnChanged(function()
    if Toggles.EnableAntiGrab.Value then
        antiGrabCleanup()
        antiGrabApply(LocalPlayer.Character)
        antiGrabConns["CharAdded"] = LocalPlayer.CharacterAdded:Connect(function(newChar)
            task.wait(0.5)
            antiGrabApply(newChar)
        end)
    else
        antiGrabCleanup()
        local char = LocalPlayer.Character
        if char then
            antiGrabEnableRagdoll(char)
            local hrp = char:FindFirstChild("HumanoidRootPart")
            if hrp then hrp.Anchored = false end
        end
    end
end)

-- ==============================================
-- Вкладка: DEF — ANTI LOOP TP
-- ==============================================
DefAntiLoop = DefSection

DefAntiLoop:AddToggle("EnableAntiLoopTP", {
 Text = "Anti Loop TP",
 Default = false,
 Tooltip = "Возвращает на место, если читер пытается кидать тебя по карте",
})

DefAntiLoop:AddSlider("AntiLoopThreshold", {
 Text = "Порог телепорт����",
 Default = 50,
 Min = 20,
 Max = 200,
 Rounding = 0,
 Compact = false,
 Tooltip = "Если перемещение за кадр больше этого числа, тебя вернёт обратно",
})

lastSafeCF = nil
antiLoopConn = nil

Toggles.EnableAntiLoopTP:OnChanged(function()
 if Toggles.EnableAntiLoopTP.Value then
 lastSafeCF = nil
 if antiLoopConn then antiLoopConn:Disconnect() end
 antiLoopConn = RunService.Heartbeat:Connect(function()
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if not hrp or not hum then return end
 
 local threshold = Options.AntiLoopThreshold and Options.AntiLoopThreshold.Value or 50
 
 -- Проверяем, летим ли мы сами с помощью наших же читов
 local isFlying = (Toggles.EnableFly and Toggles.EnableFly.Value) or (Toggles.EnableBlobFly and Toggles.EnableBlobFly.Value)
 
 -- Безопасная точка обновляется ТОЛЬКО если мы стоим на земле (или сами летим)
 local isGrounded = (hum.FloorMaterial ~= Enum.Material.Air) or isFlying
 local vel = hrp.AssemblyLinearVelocity.Magnitude
 
 -- Если мы на земле и нас не швыряет, то запоминаем точку как безопасную
 if isGrounded and vel < (isFlying and 500 or 150) then
 lastSafeCF = hrp.CFrame
 end
 
 if not lastSafeCF then return end
 
 local dist = (hrp.Position - lastSafeCF.Position).Magnitude
 
 -- Если нас утащили за порог от последней точки на земле, ИЛИ скорость абсурдно большая
 if dist > threshold or (not isFlying and vel > 350) then
 -- Пытаемся вырваться (сбиваем стан/захват)
 pcall(function()
 hum.Sit = false
 hum.PlatformStand = false
 end)
 local ce = game:GetService("ReplicatedStorage"):FindFirstChild("CharacterEvents")
 local struggle = ce and ce:FindFirstChild("Struggle")
 if struggle then pcall(function() struggle:FireServer(LocalPlayer) end) end
 
 -- Возвращаем на землю!
 hrp.AssemblyLinearVelocity = Vector3.zero
 hrp.AssemblyAngularVelocity = Vector3.zero
 hrp.CFrame = lastSafeCF
 end
 end)
 else
 if antiLoopConn then antiLoopConn:Disconnect() end
 antiLoopConn = nil
 lastSafeCF = nil
 end
end)

-- ==============================================
-- Вкладка: DEF — ANTI EXPLOSION
-- ==============================================
-- Не даёт отлетать от взрывов (момба и т.п.). Логика собрана из готовых
-- скриптов: взрыв в этой игре кидает персонажа в рэгдолл
-- (Humanoid.Ragdolled) и придаёт импульс отлёта. П����ка и��ёт рэгдолл —
-- гасим скорость (StopAllVelocity + обнуляем AssemblyLinearVelocity у всех
-- деталей тела), поэтому тебя не швыряет в сторону.
DefAntiExplode = DefSection

DefAntiExplode:AddToggle("EnableAntiExplode", {
 Text = "Anti Explosion",
 Default = false,
 Tooltip = "Гасит отлёт от взрывов момбы: при взрыве тебя не швыряет",
})

antiExplodeRS = game:GetService("ReplicatedStorage")
antiExplodeConn = nil

-- Легитимное событие игры для гашения скорости (то же, что в Anti Grab)
function antiExplodeStopVelocityEvent()
 local ge = antiExplodeRS:FindFirstChild("GameCorrectionEvents")
 return ge and ge:FindFirstChild("StopAllVelocity")
end

-- В рэгдолле? Взрыв ставит Humanoid.Ragdolled = true
function antiExplodeIsRagdolled(hum)
 local r = hum and hum:FindFirstChild("Ragdolled")
 return r ~= nil and r.Value == true
end

-- Обнуляем скорость всех деталей тела (чтобы импульс отлёта не сдвинул)
function antiExplodeKillVelocity(char)
 for _, part in ipairs(char:GetDescendants()) do
 if part:IsA("BasePart") then
 pcall(function()
 part.AssemblyLinearVelocity = Vector3.zero
 part.AssemblyAngularVelocity = Vector3.zero
 end)
 end
 end
end

function antiExplodeTick()
 local char = LocalPlayer.Character
 if not char then return end
 local hum = char:FindFirstChildOfClass("Humanoid")
 -- Рэгдолл от взрыва ��� гасим отлёт каждый кадр
 if antiExplodeIsRagdolled(hum) then
 local stopVel = antiExplodeStopVelocityEvent()
 if stopVel then pcall(function() stopVel:FireServer() end) end
 antiExplodeKillVelocity(char)
 end
end

Toggles.EnableAntiExplode:OnChanged(function()
 if Toggles.EnableAntiExplode.Value then
 if antiExplodeConn then antiExplodeConn:Disconnect() end
 antiExplodeConn = RunService.Heartbeat:Connect(antiExplodeTick)
 else
 if antiExplodeConn then antiExplodeConn:Disconnect() end
 antiExplodeConn = nil
 end
end)

-- ==============================================
-- Вкладка: DEF — ANTI LAG
-- ==============================================
-- Защита от "лаг-сервера". В FTAP частый способ лагать всех — спам
-- грабящих лучей (GrabBeam/Beam): игра плодит тысячи линий, и клиент
-- захлёбывается их отрисовкой → просадка FPS и "лаги". Логика собрана
-- из готовых скриптов: гасим саму отрисовку линий —
-- • отключаем локальный скрипт отрисовки лучей (CharacterAndBeamMove);
-- • вычищаем уже созданные Beam/лучи и убиваем новые по мере появления.
-- ВАЖНО: чинит КЛИЕНТСКИ�� лаг от спама линий (FPS). Если сервер физически
-- перегружен, реальный сетевой пинг кли��нтом не понизить — но именно
-- "линий-лаг" в FTAP этот способ убирает.
DefAntiLag = DefSection

DefAntiLag:AddToggle("EnableAntiLag", {
 Text = "Anti Lag",
 Default = false,
 Tooltip = "Убирает лаги от спама грабящих линий (lag server): чистит и глушит отрисовку лучей",
})

antiLagConn = nil -- слежение за новыми лучами
antiLagBeamScript = nil -- отключённый скрипт отрисовки лучей

-- Это грабящий луч/линия? (по классу Beam или по имени)
function antiLagIsLine(obj)
 if obj:IsA("Beam") then return true end
 local n = string.lower(obj.Name)
 return n:find("grabbeam") ~= nil or n:find("grabline") ~= nil
end

-- Разовая зачистка всех уже созданных лучей/линий
function antiLagSweep()
 for _, obj in ipairs(workspace:GetDescendants()) do
 if antiLagIsLine(obj) then
 pcall(function() obj:Destroy() end)
 end
 end
end

Toggles.EnableAntiLag:OnChanged(function()
 if Toggles.EnableAntiLag.Value then
 -- Отключаем локальный ����к��и��т, который рисует грабящие лучи
 local ps = LocalPlayer:FindFirstChild("PlayerScripts")
 local beamScript = ps and ps:FindFirstChild("CharacterAndBeamMove")
 if beamScript then
 antiLagBeamScript = beamScript
 pcall(function() beamScript.Disabled = true end)
 end
 -- Чистим текущий спам и глушим новые лучи по мере появления
 antiLagSweep()
 if antiLagConn then antiLagConn:Disconnect() end
 antiLagConn = workspace.DescendantAdded:Connect(function(obj)
 if not (Toggles.EnableAntiLag and Toggles.EnableAntiLag.Value) then return end
 if antiLagIsLine(obj) then
 pcall(function() obj:Destroy() end)
 end
 end)
 Library:Notify("Anti Lag ВКЛ — чищу спам линий", 2)
 else
 if antiLagConn then antiLagConn:Disconnect(); antiLagConn = nil end
 -- Возвращаем скрипт отрисовки лучей
 if antiLagBeamScript then
 pcall(function() antiLagBeamScript.Disabled = false end)
 antiLagBeamScript = nil
 end
 Library:Notify("Anti Lag ВЫКЛ", 2)
 end
end)

do
-- ==============================================
-- Вкладка: DEF — ANTI KICK
-- ==============================================
-- Защита от кика через сюрикен (NinjaShuriken). Идея из готовых скриптов:
-- спавним сюрикен себе в грудь, забираем на него сетевое владение
-- (SetNetworkOwner) и приклеиваем к телу через StickyPartEvent
-- (к FirePlayerPart на HumanoidRootPart). Пока на тебе висит "твой"
-- залоченный предмет — чужие попытки схв��тить/кикнуть сбиваются.
-- Логика:
-- • спавним NinjaShuriken рядом с HRP и переименовываем в "AntiKick";
-- • делаем его невидимым/непробиваемым (CanTouch/CanCollide/CanQuery=false);
-- • забираем владение и прикле��ваем к груди, держим приклеенным;
-- • если предмет потерян или улетел дальше 20 студов — чистим и спавним заново.
DefAntiKick = Tabs.Def:AddLeftGroupbox("Anti Kick")


local autoLeaveConn = nil
DefAntiKick:AddToggle("EnableAutoLeave", {
 Text = "Auto Leave (Anti-Ban)",
 Default = false,
 Tooltip = "Автоматически выходит из игры, если сервер отправляет слишком много предупреждений 'Flying'",
 Callback = function(Value)
  if autoLeaveConn then autoLeaveConn:Disconnect() autoLeaveConn = nil end
  if Value then
   local warnTimestamps = {}
   local notify = game:GetService("ReplicatedStorage"):FindFirstChild("GameCorrectionEvents")
   if notify then
    notify = notify:FindFirstChild("GameCorrectionsNotify")
   end
   if not notify then return end
   
   autoLeaveConn = notify.OnClientEvent:Connect(function(reason)
    if reason == "Flying" then
     local currentTime = os.clock()
     table.insert(warnTimestamps, currentTime)
     
     for i = #warnTimestamps, 1, -1 do
      if currentTime - warnTimestamps[i] > 1 then
       table.remove(warnTimestamps, i)
      end
     end
     
     if #warnTimestamps >= 3 then
      LocalPlayer:Kick("Oblivion Safety: Отключено для предотвращения бана.")
     end
    end
   end)
  end
 end
})

DefAntiKick:AddToggle("EnableAntiKickShuriken", {
 Text = "Anti Kick Shuriken",
 Default = false,
 Tooltip = "Спавн��т сюрикен в грудь и держит его залоченным на тебе — сбивает попытки кикнуть",
})

DefAntiKick:AddToggle("EnableAntiKickItem", {
 Text = "Anti Kick Item",
 Default = false,
 Tooltip = "Спавнит выбранн��й предмет (свеча/часы/тарелка) в грудь и приклеивает Weld'ом — сбивает попытки кикнуть",
})

DefAntiKick:AddDropdown("AntiKickItemName", {
 Text = "Предмет",
 Values = {"SpookyCandle1", "ClockAlarm", "FoodPlate", "SprayCanWD", "JapaneseLantern"},
 Default = 1,
 Multi = false,
 Tooltip = "Какой предмет спавнить для Anti Kick Item (SprayCanWD / JapaneseLantern добавлены)",
})


-- === ANTI KICK PALLETKA (soundPart ownership, persistent, respawn) ===
-- Логика взята из XOCO "Pallet Ragdoll (Invis)": спавним PalletLightBrown,
-- забираем SetNetworkOwner у SoundPart, делаем пейллет невидимым/неколлизионным,
-- держим владельцем пока тогл включён. Если пейллет уничтожают — респавним.
DefAntiKick:AddToggle("EnableAntiKickPalletka", {
 Text = "Anti Kick Palletka",
 Default = false,
 Tooltip = "Держит SoundPart пейлета под network ownership — защищает от кика",
})

-- Состояние анти-кик пейлета (глобальное, чтобы можно было чистить снаружи)
palletAntiKickState = {
 active = false,
 palletModel = nil,
 soundPart = nil,
 steppedConn = nil,
 cacheConn = nil,
 ancestryConn = nil,
}

local function clearPalletAntiKick()
 palletAntiKickState.active = false
 if palletAntiKickState.steppedConn then palletAntiKickState.steppedConn:Disconnect(); palletAntiKickState.steppedConn = nil end
 if palletAntiKickState.cacheConn   then palletAntiKickState.cacheConn:Disconnect();   palletAntiKickState.cacheConn   = nil end
 if palletAntiKickState.ancestryConn then palletAntiKickState.ancestryConn:Disconnect(); palletAntiKickState.ancestryConn = nil end
 local RS = game:GetService("ReplicatedStorage")
 local destroyRE = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
 if palletAntiKickState.palletModel and palletAntiKickState.palletModel.Parent and destroyRE then
  pcall(function() destroyRE:FireServer(palletAntiKickState.palletModel) end)
 end
 palletAntiKickState.palletModel = nil
 palletAntiKickState.soundPart = nil
end

local function spawnPalletAntiKick()
 local RS = game:GetService("ReplicatedStorage")
 local spawnRF = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
 if not spawnRF then return end
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 if not hrp then return end
 local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy")
 if canSpawn and not canSpawn.Value then return end
 task.spawn(function()
  pcall(function()
   spawnRF:InvokeServer("PalletLightBrown", hrp.CFrame * CFrame.new(0, 10, 20), Vector3.zero)
  end)
 end)
end

Toggles.EnableAntiKickPalletka:OnChanged(function()
 if Toggles.EnableAntiKickPalletka.Value then
  -- Включаем
  clearPalletAntiKick()
  palletAntiKickState.active = true

  local RS = game:GetService("ReplicatedStorage")
  local setNetOwner = RS:FindFirstChild("GrabEvents") and RS.GrabEvents:FindFirstChild("SetNetworkOwner")
  local destroyRE  = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("DestroyToy")
  local lpName = LocalPlayer.Name
  local toyFolderName = lpName .. "SpawnedInToys"

  -- Ждём появления папки SpawnedInToys
  local toysFolder = workspace:FindFirstChild(toyFolderName)
  if not toysFolder then
   toysFolder = workspace:WaitForChild(toyFolderName, 5)
  end
  if not toysFolder then
   Library:Notify("Anti Kick Palletka: папка SpawnedInToys не найдена", 3)
   clearPalletAntiKick()
   Toggles.EnableAntiKickPalletka:SetValue(false)
   return
  end

  -- Слушаем ChildAdded на самой папке (в этом была проблема со старым pallet ragdoll!)
  palletAntiKickState.cacheConn = toysFolder.ChildAdded:Connect(function(child)
   if not palletAntiKickState.active then return end
   if child.Name ~= "PalletLightBrown" and child.Name ~= "PalletForAntiKick" then return end
   -- Если у нас уже есть живой пейллет — уничтожаем дубль
   if palletAntiKickState.palletModel and palletAntiKickState.palletModel.Parent then
    if destroyRE then pcall(function() destroyRE:FireServer(child) end) end
    return
   end

   local sp = child:WaitForChild("SoundPart", 3)
   if not sp then
    if destroyRE then pcall(function() destroyRE:FireServer(child) end) end
    return
   end

   -- Забираем ownership SoundPart'а
   if setNetOwner then
    pcall(function() setNetOwner:FireServer(sp, sp.CFrame) end)
   end

   -- Ждём подтверждения PartOwner
   local partOwner = sp:WaitForChild("PartOwner", 1)
   if not (partOwner and partOwner.Value == lpName) then
    -- Ownership не прошёл — попробуем ещё раз, иначе уничтожаем
    if setNetOwner then
     pcall(function() setNetOwner:FireServer(sp, sp.CFrame) end)
    end
    partOwner = sp:WaitForChild("PartOwner", 1)
    if not (partOwner and partOwner.Value == lpName) then
     if destroyRE then pcall(function() destroyRE:FireServer(child) end) end
     return
    end
   end

   -- Делаем невидимым и неколлизионным для локала
   for _, v in pairs(child:GetChildren()) do
    if v:IsA("BasePart") then
     v.CanCollide   = false
     v.CanQuery     = false
     v.Transparency = 1
    end
   end
   -- SoundPart прячем подальше от основного экшена
   sp.CFrame = CFrame.new(0, 9e9, 0)
   sp.AssemblyLinearVelocity  = Vector3.zero
   sp.AssemblyAngularVelocity = Vector3.zero

   child.Name = "PalletForAntiKick"
   palletAntiKickState.palletModel = child
   palletAntiKickState.soundPart   = sp

   -- Heartbeat: пере-клэйм ownership + парковка (защита от десинка)
   if palletAntiKickState.steppedConn then palletAntiKickState.steppedConn:Disconnect() end
   local lastOwnerTime = tick()
   palletAntiKickState.steppedConn = RunService.Heartbeat:Connect(function()
    if not palletAntiKickState.active then return end
    if not child.Parent or not sp.Parent then
     if palletAntiKickState.steppedConn then palletAntiKickState.steppedConn:Disconnect(); palletAntiKickState.steppedConn = nil end
     return
    end
    -- Периодически пере-забираем ownership (каждые 1.5 сек)
    if tick() - lastOwnerTime > 1.5 and setNetOwner then
     pcall(function() setNetOwner:FireServer(sp, sp.CFrame) end)
     lastOwnerTime = tick()
    end
    -- Держим SoundPart на 9e9 (далеко от физики, чтобы не лагало)
    sp.CFrame = CFrame.new(0, 9e9, 0)
    sp.AssemblyLinearVelocity  = Vector3.zero
    sp.AssemblyAngularVelocity = Vector3.zero
   end)

   -- Если пейллет уничтожили — респавним
   if palletAntiKickState.ancestryConn then palletAntiKickState.ancestryConn:Disconnect() end
   palletAntiKickState.ancestryConn = child.AncestryChanged:Connect(function(_, newParent)
    if not newParent then
     palletAntiKickState.palletModel = nil
     palletAntiKickState.soundPart   = nil
     if palletAntiKickState.steppedConn then palletAntiKickState.steppedConn:Disconnect(); palletAntiKickState.steppedConn = nil end
     if palletAntiKickState.active then
      task.wait(0.1)
      if palletAntiKickState.active then spawnPalletAntiKick() end
     end
    end
   end)
  end)

  -- Первый спавн
  spawnPalletAntiKick()
  Library:Notify("Anti Kick Palletka ВКЛ", 2)
 else
  -- Выключаем
  clearPalletAntiKick()
  Library:Notify("Anti Kick Palletka ВЫКЛ", 2)
 end
end)

DefAntiKick:AddToggle("EnableAntiVoid", {
 Text = "Anti Void",
 Default = false,
 Tooltip = "Не даёт упасть в void: убирает FallenPartsDestroyHeight и телепортирует на спавн при падении"
})

local antiVoidConnections = {}
local originalFallenHeight = nil
local lastSafePosition = nil

function cleanupAntiVoid()
 for _, conn in ipairs(antiVoidConnections) do
 pcall(function() conn:Disconnect() end)
 end
 antiVoidConnections = {}
 if originalFallenHeight ~= nil then
 workspace.FallenPartsDestroyHeight = originalFallenHeight
 originalFallenHeight = nil
 end
end

Toggles.EnableAntiVoid:OnChanged(function()
 if Toggles.EnableAntiVoid.Value then
 originalFallenHeight = workspace.FallenPartsDestroyHeight
 workspace.FallenPartsDestroyHeight = -50000
 lastSafePosition = CFrame.new(0, 10, 0)

 table.insert(antiVoidConnections, RunService.Heartbeat:Connect(function()
 local char = LocalPlayer.Character
 if not char then return end
 local root = char:FindFirstChild("HumanoidRootPart")
 local hum = char:FindFirstChildOfClass("Humanoid")
 if not root then return end
 if root.Position.Y > -100 then
 lastSafePosition = root.CFrame
 end
 if root.Position.Y < -800 then
 root.CFrame = CFrame.new(0, 10, 0)
 root.AssemblyLinearVelocity = Vector3.zero
 root.AssemblyAngularVelocity = Vector3.zero
 if hum then hum.PlatformStand = false end
 Library:Notify("Anti Void: телепорт на спавн", 3)
 end
 end))

 table.insert(antiVoidConnections, game:GetService("Workspace").ChildRemoved:Connect(function(child)
 if child.Name == LocalPlayer.Name then
 cleanupAntiVoid()
 Toggles.EnableAntiVoid:SetValue(false)
 end
 end))

 Library:Notify("Anti Void ВКЛ", 2)
 else
 cleanupAntiVoid()
 Library:Notify("Anti Void ВЫКЛ", 2)
 end
end)

-- === ANTI BURN / ANTI FREEZE (под реальный объектный слой игры) ===
DefAntiKick:AddToggle("EnableAntiBurn", {
 Text = "Anti Burn",
 Default = false,
 Tooltip = "Не даёт сгореть: тушит костёр/плиту через огнетушитель (firetouchinterest)"
})

DefAntiKick:AddToggle("EnableAntiFreeze", {
 Text = "Anti Freeze",
 Default = false,
 Tooltip = "Автоматически снимает чужой Freeze Grab с тебя: удаляет чужие BodyPosition/BodyGyro и сбрасывает PlatformStand"
})

antiFreezeConnections = {}
antiFreezeHeartbeat = nil

function cleanupAntiFreeze()
 for _, conn in ipairs(antiFreezeConnections) do
 pcall(function() conn:Disconnect() end)
 end
 antiFreezeConnections = {}
 if antiFreezeHeartbeat then
 pcall(function() antiFreezeHeartbeat:Disconnect() end)
 antiFreezeHeartbeat = nil
 end
end

function unfreezeSelf(char)
 pcall(function()
 local hum = char:FindFirstChildOfClass("Humanoid")
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if not hum or not hrp then return end

 for _, part in ipairs(char:GetDescendants()) do
 if part:IsA("BasePart") then
 for _, inst in ipairs(part:GetChildren()) do
 if inst:IsA("BodyPosition") or inst:IsA("BodyGyro") or inst.Name == "FreezeGrabBodyPosition" or inst.Name == "FreezeGrabBodyGyro" then
 inst:Destroy()
 end
 end
 part.Anchored = false
 part.AssemblyLinearVelocity = Vector3.zero
 part.AssemblyAngularVelocity = Vector3.zero
 end
 end

 hum.PlatformStand = false
 hum.Sit = false
 hum:ChangeState(Enum.HumanoidStateType.GettingUp)
 hum:ChangeState(Enum.HumanoidStateType.Running)
 end)
end

function setupAntiFreeze(char)
 local hum = char:FindFirstChildOfClass("Humanoid")
 if not hum then return end

 table.insert(antiFreezeConnections, char.DescendantAdded:Connect(function(desc)
 if desc:IsA("BodyPosition") or desc:IsA("BodyGyro") or desc.Name == "FreezeGrabBodyPosition" or desc.Name == "FreezeGrabBodyGyro" then
 unfreezeSelf(char)
 end
 end))

 table.insert(antiFreezeConnections, hum:GetPropertyChangedSignal("PlatformStand"):Connect(function()
 if hum.PlatformStand then unfreezeSelf(char) end
 end))
end

Toggles.EnableAntiFreeze:OnChanged(function()
 if Toggles.EnableAntiFreeze.Value then
 cleanupAntiFreeze()
 if LocalPlayer.Character then
 setupAntiFreeze(LocalPlayer.Character)
 unfreezeSelf(LocalPlayer.Character)
 end
 table.insert(antiFreezeConnections, LocalPlayer.CharacterAdded:Connect(function(char)
 setupAntiFreeze(char)
 unfreezeSelf(char)
 end))
 antiFreezeHeartbeat = RunService.Heartbeat:Connect(function()
 local char = LocalPlayer.Character
 if not char then return end
 local hum = char:FindFirstChildOfClass("Humanoid")
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if not hum or not hrp then return end
 if hum.PlatformStand or hrp.Anchored then
 unfreezeSelf(char)
 end
 end)
 Library:Notify("Anti Freeze ВКЛ", 2)
 else
 cleanupAntiFreeze()
 Library:Notify("Anti Freeze ВЫКЛ", 2)
 end
end)

antiBurnConnections = {}

function cleanupAntiBurn()
 for _, conn in ipairs(antiBurnConnections) do
 pcall(function() conn:Disconnect() end)
 end
 antiBurnConnections = {}
end

function getExtinguishPart()
 local p1 = workspace:FindFirstChild("Map")
 and workspace.Map:FindFirstChild("Hole")
 and workspace.Map.Hole:FindFirstChild("PoisonBigHole")
 and workspace.Map.Hole.PoisonBigHole:FindFirstChild("ExtinguishPart")
 if p1 and p1:IsA("BasePart") then return p1 end
 for _, obj in ipairs(workspace:GetDescendants()) do
 if obj:IsA("BasePart") and obj.Name == "ExtinguishPart" then
 return obj
 end
 end
 return nil
end

function setupAntiBurn(char)
 local hum = char:FindFirstChildOfClass("Humanoid")
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if not hum or not hrp then return end

 local apagarfogo = getExtinguishPart()
 if apagarfogo and apagarfogo:IsA("BasePart") then
 apagarfogo.Size = Vector3.new(0.5, 0.5, 0.5)
 apagarfogo.Transparency = 1
 local tex = apagarfogo:FindFirstChild("Tex")
 if tex and tex:IsA("Decal") then tex.Transparency = 1 end
 end

 local function doExtinguishLoop()
 local firePart = char:FindFirstChild("FirePlayerPart", true)
 if not firePart or not apagarfogo then return end
 local oldCF = apagarfogo.CFrame
 local oldSize = apagarfogo.Size
 task.spawn(function()
 while firePart and firePart.Parent do
 local fd = hum:FindFirstChild("FireDebounce")
 local cb = firePart:FindFirstChild("CanBurn")
 if (not fd or not fd.Value) and (not cb or not cb.Value) then break end
 if cb then cb.Value = false end
 if fd then fd.Value = false end
 apagarfogo.CFrame = firePart.CFrame * CFrame.new(math.random(-1, 1), math.random(-1, 1), math.random(-1, 1))
 if firetouchinterest then
 firetouchinterest(firePart, apagarfogo, 0)
 task.wait()
 firetouchinterest(firePart, apagarfogo, 1)
 else
 task.wait(0.05)
 end
 end
 pcall(function()
 apagarfogo.CFrame = oldCF
 apagarfogo.Size = oldSize
 end)
 end)
 end

 local fireDebounce = hum:FindFirstChild("FireDebounce")
 if fireDebounce then
 table.insert(antiBurnConnections, fireDebounce:GetPropertyChangedSignal("Value"):Connect(function()
 if fireDebounce.Value then doExtinguishLoop() end
 end))
 if fireDebounce.Value then doExtinguishLoop() end
 end

 local firePart = char:FindFirstChild("FirePlayerPart", true)
 if firePart and firePart:FindFirstChild("CanBurn") then
 table.insert(antiBurnConnections, firePart.CanBurn:GetPropertyChangedSignal("Value"):Connect(function()
 if firePart.CanBurn.Value then doExtinguishLoop() end
 end))
 if firePart.CanBurn.Value then doExtinguishLoop() end
 end

 table.insert(antiBurnConnections, char.DescendantAdded:Connect(function(desc)
 if desc.Name == "FirePlayerPart" then
 local cb = desc:FindFirstChild("CanBurn")
 if cb then
 table.insert(antiBurnConnections, cb:GetPropertyChangedSignal("Value"):Connect(function()
 if cb.Value then doExtinguishLoop() end
 end))
 if cb.Value then doExtinguishLoop() end
 end
 local fd = hum:FindFirstChild("FireDebounce")
 if fd and fd.Value then doExtinguishLoop() end
 end
 end))
end

Toggles.EnableAntiBurn:OnChanged(function()
 if Toggles.EnableAntiBurn.Value then
 cleanupAntiBurn()
 if LocalPlayer.Character then
 setupAntiBurn(LocalPlayer.Character)
 end
 table.insert(antiBurnConnections, LocalPlayer.CharacterAdded:Connect(setupAntiBurn))
 Library:Notify("Anti Burn ВКЛ", 2)
 else
 cleanupAntiBurn()
 Library:Notify("Anti Burn ВЫКЛ", 2)
 end
end)

function antiKickClear()
 local inv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 local menuToys = game:GetService("ReplicatedStorage"):FindFirstChild("MenuToys")
 local destroyrem = menuToys and menuToys:FindFirstChild("DestroyToy")
 if inv and destroyrem then
 for _, v in pairs(inv:GetChildren()) do
 if v.Name == "AntiKick" or v.Name == "AntiKickItem" or v.Name == "NinjaShuriken" or v.Name == "SpookyCandle1" or v.Name == "JapaneseLantern" or v.Name == "ClockAlarm" or v.Name == "FoodPlate" or v.Name == "SprayCanWD" then
 pcall(function() destroyrem:FireServer(v) end)
 end
 end
 end
end

Toggles.EnableAntiKickShuriken:OnChanged(function()
 local plr = LocalPlayer
 local RS = game:GetService("ReplicatedStorage")

 local function getAntiKickItemName()
 return "NinjaShuriken"
 end

 if Toggles.EnableAntiKickShuriken.Value then
 Library:Notify("Anti Kick Shuriken ВКЛ", 2)
 task.spawn(function()
 local setOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
 local stickyEvent = RS:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent")
 local spawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
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
 local plotItems = workspace:FindFirstChild("PlotItems")
 local playersInPlots = plotItems and plotItems:FindFirstChild("PlayersInPlots")
 if not (playersInPlots and playersInPlots:FindFirstChild(plr.Name)) then
 return false
 end
 local plots = workspace:FindFirstChild("Plots")
 if not plots then return false end
 for _, v in pairs(plots:GetChildren()) do
 local sign = v:FindFirstChild("PlotSign")
 local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
 if owners then
 for _, b in pairs(owners:GetChildren()) do
 if b.Value == plr.Name then
 local folder = plotItems:FindFirstChild(v.Name)
 if folder then
 return true, folder
 end
 end
 end
 end
 end
 return false
 end

 local function getAttachPart(obj)
 return obj:FindFirstChild("StickyPart") or obj:FindFirstChild("HoldPart") or obj:FindFirstChild("Main") or obj:FindFirstChildWhichIsA("BasePart")
 end
 local function StickKunai(kunai)
 local attachPart = getAttachPart(kunai)
 if not kunai or not attachPart then
 return
 end
 local currentHRP = getHRP()
 if not currentHRP then
 return
 end
 local ownerPart = kunai:FindFirstChild("SoundPart") or attachPart
 if ownerPart then
 if not ownerPart:FindFirstChild("PartOwner") or ownerPart.PartOwner.Value ~= plr.Name then
 pcall(function() setOwner:FireServer(ownerPart, ownerPart.CFrame) end)
 end
 end
 local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP:WaitForChild("FirePlayerPart", 5)
 if firePart then
 pcall(function()
 stickyEvent:FireServer(
 attachPart,
 firePart,
 CFrame.new(0, 0, 0) * CFrame.Angles(0, math.rad(90), math.rad(90))
 )
 end)
 if not attachPart:FindFirstChild("LocalFakeWeld") then
 attachPart.CFrame = firePart.CFrame * CFrame.Angles(0, math.rad(90), math.rad(90))
 local w = Instance.new("WeldConstraint")
 w.Name = "LocalFakeWeld"
 w.Part0 = attachPart
 w.Part1 = firePart
 w.Parent = attachPart
 end
 end
 for _, obj in pairs(kunai:GetChildren()) do
 if obj:IsA("BasePart") then
 obj.CanTouch = false
 obj.CanCollide = false
 obj.CanQuery = false
 if obj.Name == "StickyPart" or obj.Name == "SoundPart" or obj.Name == "Hitbox" or obj.Transparency == 1 then
 obj.Transparency = 1
 else
 obj.Transparency = 0.8
 if not obj:FindFirstChild("Highlight") then
 local high = Instance.new("Highlight", obj)
 high.FillColor = Color3.fromRGB(255, 255, 255)
 high.OutlineColor = Color3.fromRGB(0, 0, 0)
 end
 end
 end
 end
 end

 local function SpawnToy(name)
 local t = tick()
 while not canSpawn.Value do
 if not Toggles.EnableAntiKickShuriken.Value or tick() - t > 5 then
 return nil
 end
 task.wait(0.1)
 end
 local currentHRP = getHRP()
 if currentHRP then
 task.spawn(function()
 pcall(function()
 spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 5, -5), Vector3.new(0, 0, 0))
 end)
 end)
 end
 local boolik, house = CheckForHome()
 local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 local plotItems = workspace:FindFirstChild("PlotItems")
 local playersInPlots = plotItems and plotItems:FindFirstChild("PlayersInPlots")
 if boolik and house then
 return house:WaitForChild(name, 2)
 elseif not (playersInPlots and playersInPlots:FindFirstChild(plr.Name)) and inv then
 return inv:WaitForChild(name, 2)
 end
 return nil
 end

 while Toggles.EnableAntiKickShuriken.Value do
 task.wait(0.005)
 if not plr.Character or not plr.Character:FindFirstChild("Humanoid") or plr.Character.Humanoid.Health <= 0 then
 continue
 end
 local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 local kunai = (inv and inv:FindFirstChild("AntiKick")) or (inv and inv:FindFirstChild(getAntiKickItemName()))
 local plotItems = workspace:FindFirstChild("PlotItems")
 local playersInPlots = plotItems and plotItems:FindFirstChild("PlayersInPlots")

 pcall(function()
 if playersInPlots and playersInPlots:FindFirstChild(plr.Name) then
 local boolik, house = CheckForHome()
 local plots = workspace:FindFirstChild("Plots")
 if boolik and house and plots and plots:FindFirstChild(house.Name) then
 local sign = plots[house.Name]:FindFirstChild("PlotSign")
 if sign and sign.ThisPlotsOwners.Value.TimeRemainingNum.Value > 89 then
 local k = SpawnToy(getAntiKickItemName())
 if k then
 k.Name = "AntiKick"
 StickKunai(k)
 kunai = k
 end
 end
 end
 end
 end)

 if not kunai then
 if playersInPlots and playersInPlots:FindFirstChild(plr.Name) then
 continue
 end
 kunai = SpawnToy(getAntiKickItemName())
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
 task.wait(0.3)
 until not kunai or not Toggles.EnableAntiKickShuriken.Value
 or not kunai:FindFirstChild("StickyPart")
 or kunai.StickyPart.CanTouch == false
 or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart")
 or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 40

 if not kunai or not kunai:FindFirstChild("StickyPart") or not plr.Character or not plr.Character:FindFirstChild("HumanoidRootPart") or (plr.Character.HumanoidRootPart.Position - kunai.StickyPart.Position).Magnitude >= 40 then
 antiKickClear()
 end

 pcall(function()
 repeat
 task.wait(0.05)
 local a = getAttachPart(kunai)
 until not Toggles.EnableAntiKickShuriken.Value or not plr.Character or not plr.Character:FindFirstChild("Humanoid") or not kunai or not kunai.Parent or not a

 local a2 = kunai and getAttachPart(kunai)
 if not a2 or not kunai.Parent or (plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health <= 0) then
 antiKickClear()
 end
 end)
 end
 end)
 else
 antiKickClear()
 Library:Notify("Anti Kick Shuriken ВЫКЛ", 2)
 end
end)

Toggles.EnableAntiKickItem:OnChanged(function()
 local plr = LocalPlayer
 local RS = game:GetService("ReplicatedStorage")

 if Toggles.EnableAntiKickItem.Value then
 Library:Notify("Anti Kick Item ВКЛ", 2)
 task.spawn(function()
 local setOwner = RS:WaitForChild("GrabEvents"):WaitForChild("SetNetworkOwner")
 local spawnRemote = RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction")
 local canSpawn = plr:WaitForChild("CanSpawnToy")

 local function getHRP()
 if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
 return plr.Character.HumanoidRootPart
 else
 local character = plr.CharacterAdded:Wait()
 return character:WaitForChild("HumanoidRootPart")
 end
 end

 local function getSelectedItem()
 local sel = Options.AntiKickItemName and Options.AntiKickItemName.Value
 if sel and sel ~= "" then
 return sel
 end
 return "SpookyCandle1"
 end

 local function FWD(parent, part, time)
 return parent:FindFirstChild(part) or parent:WaitForChild(part, time)
 end

 local function itemSpawn(name)
 local t = tick()
 while not canSpawn.Value do
 if not Toggles.EnableAntiKickItem.Value or tick() - t > 5 then
 return nil
 end
 task.wait(0.1)
 end
 local currentHRP = getHRP()
 if not currentHRP then return nil end
 pcall(function()
 spawnRemote:InvokeServer(name, currentHRP.CFrame * CFrame.new(0, 5, -5), Vector3.new(0, 0, 0))
 end)
 local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 if inv then
 return inv:WaitForChild(name, 3)
 end
 return nil
 end

 local function sno(part)
 pcall(function() setOwner:FireServer(part, part.CFrame) end)
 end

 local function isNetworkOwner(part)
 local po = part and part:FindFirstChild("PartOwner")
 return po and po.Value == plr.Name
 end

 local function getSoundPart(item, name)
 if name == "SpookyCandle1" then
 return FWD(item, "Hitbox", 0.5)
 end
 return item:FindFirstChild("Hitbox") or item:FindFirstChild("SoundPart") or item:FindFirstChild("Main") or item:FindFirstChildWhichIsA("BasePart")
 end


 while Toggles.EnableAntiKickItem.Value do
 task.wait(0.05)
 if not plr.Character or not plr.Character:FindFirstChildOfClass("Humanoid") or plr.Character:FindFirstChildOfClass("Humanoid").Health <= 0 then
 continue
 end

 local inv = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 local itemName = getSelectedItem()
 local Item = inv and inv:FindFirstChild("AntiKickItem")
 local SoundPart = Item and getSoundPart(Item, itemName)

 if not Item or not SoundPart then
 if inv then
 for _, v in pairs(inv:GetChildren()) do
 if v.Name == "AntiKickItem" then
 pcall(function()
 local d = RS:FindFirstChild("MenuToys")
 d = d and d:FindFirstChild("DestroyToy")
 if d then d:FireServer(v) end
 end)
 end
 end
 end
 Item = itemSpawn(itemName)
 if not Item then continue end
 SoundPart = getSoundPart(Item, itemName)
 if not SoundPart then continue end
 sno(SoundPart)
 for _, v in pairs(Item:GetChildren()) do
 if v:IsA("BasePart") then
 v.CanCollide = false
 v.CanQuery = false
 v.Transparency = 0.8
 end
 end
 local currentHRP = getHRP()
 if currentHRP then
 local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP
 local Weld
 if itemName == "SpookyCandle1" then
 SoundPart.CFrame = currentHRP.CFrame * CFrame.new(0, 1, 0)
 Weld = Instance.new("Weld")
 Weld.C0 = CFrame.new(0, 1, 0)
 Weld.C1 = CFrame.new(0, 0, 0)
 else
 SoundPart.CFrame = currentHRP.CFrame
 Weld = Instance.new("WeldConstraint")
 end
 Weld.Name = "WeldBlabla"
 Weld.Part0 = SoundPart
 Weld.Part1 = firePart
 Weld.Parent = SoundPart
 end
 Item.Name = "AntiKickItem"
 end

 if not Item or not SoundPart then continue end
 if not isNetworkOwner(SoundPart) then
 sno(SoundPart)
 end
 local currentHRP = getHRP()
 if currentHRP then
 local firePart = currentHRP:FindFirstChild("FirePlayerPart") or currentHRP
 local Weld = SoundPart:FindFirstChild("WeldBlabla")
 if itemName == "SpookyCandle1" then
 if not Weld then
 SoundPart.CFrame = currentHRP.CFrame * CFrame.new(0, 1, 0)
 Weld = Instance.new("Weld")
 Weld.Name = "WeldBlabla"
 Weld.C0 = CFrame.new(0, 1, 0)
 Weld.C1 = CFrame.new(0, 0, 0)
 Weld.Part0 = SoundPart
 Weld.Part1 = firePart
 Weld.Parent = SoundPart
 elseif Weld.Part1 ~= firePart then
 Weld.Part1 = firePart
 end
 else
 if not Weld then
 SoundPart.CFrame = currentHRP.CFrame
 Weld = Instance.new("WeldConstraint")
 Weld.Name = "WeldBlabla"
 Weld.Part0 = SoundPart
 Weld.Part1 = firePart
 Weld.Parent = SoundPart
 elseif Weld.Part1 ~= firePart then
 Weld.Enabled = false
 SoundPart.CFrame = currentHRP.CFrame
 Weld.Part1 = firePart
 Weld.Enabled = true
 end
 end
 end
 end
 end)
 else
 antiKickClear()
 Library:Notify("Anti Kick Item ВЫКЛ", 2)
 end
end)
end
-- ==============================================
-- Вкладка: DEF — ANTI OWNERSHIP
-- ==============================================
-- Доп. защита с ДРУГИМ принцип��м, чем Anti Kick: постоянно держим в руках еду
-- и едим её. Пока ты «занят» своим съедобным предметом, чужие попытки
-- взять тебя на линию/кикнуть сбиваются.
-- Механика взята и�� готового рабочего скрипта под эту игру:
-- • спавним еду в инвентарь;
-- • берём её в руки через HoldItemRemoteFunction (это как «ПКМ»);
-- • ����ткусываем через HoldEvents.Use, пока есть EdiblePart (это как «ЛКМ»);
-- • когда съели — спавним свежую и повторяем.

-- Дружелюбные названия -> реальные имена ��г��ушек-еды в FTAP
antiOwnFoods = {
 ["Pizza (Cheese)"] = "FoodPizzaCheese",
 ["Pizza (Pepperoni)"] = "FoodPizzaPepperoni",
 ["Burger"] = "FoodHamburger",
 ["Hotdog"] = "FoodHotdog",
 ["Banana"] = "FoodBanana",
 ["Donut"] = "FoodDonut",
 ["Cake"] = "FoodCakePink",
 ["Bread"] = "FoodBread",
 ["Mayonnaise"] = "FoodMayonnaise",
 ["French Fries"] = "FoodFrenchFries",
 ["Dippy Egg"] = "FoodDippyEgg",
 ["Coconut"] = "FoodCoconut",
 ["Meat Stick"] = "FoodMeatStick",
 ["Poison Mushroom"] = "FoodMushroomPoison",
 -- Музыкальные инструменты (из unstable.txt 3076-3090)
 ["Snare Drum"] = "InstrumentDrumSnare",
 ["Bongos"] = "InstrumentDrumBongos",
 ["Bugle"] = "InstrumentBrassBugle",
 ["Trumpet"] = "InstrumentBrassTrumpet",
 ["Vuvuzela"] = "InstrumentBrassVuvuzela",
 ["Acoustic Guitar"] = "InstrumentGuitarAcoustic",
 ["Banjo"] = "InstrumentGuitarBanjo",
 ["Lyre"] = "InstrumentGuitarLyre",
 ["Ukulele"] = "InstrumentGuitarUkulele",
 ["Melodica"] = "InstrumentPianoMelodica",
 ["Microphone"] = "InstrumentVoiceMicrophone",
 ["Ocarina"] = "InstrumentWoodwindOcarina",
 ["Saxophone"] = "InstrumentWoodwindSaxophone",
}
antiOwnFoodOrder = {
 "Pizza (Cheese)",
 "Pizza (Pepperoni)",
 "Burger",
 "Hotdog",
 "Banana",
 "Donut",
 "Cake",
 "Bread",
 "Mayonnaise",
 "French Fries",
 "Dippy Egg",
 "Coconut",
 "Meat Stick",
 "Poison Mushroom",
 -- Музыкальные предметы
 "Snare Drum",
 "Bongos",
 "Bugle",
 "Trumpet",
 "Vuvuzela",
 "Acoustic Guitar",
 "Banjo",
 "Lyre",
 "Ukulele",
 "Melodica",
 "Microphone",
 "Ocarina",
 "Saxophone",
}

DefAntiOwnership = DefSection

DefAntiOwnership:AddDropdown("AntiOwnFood", {
 Text = "Еда",
 Values = antiOwnFoodOrder,
 Default = 1,
 Multi = false,
 Tooltip = "Что спавнить и есть для защиты",
})

DefAntiOwnership:AddToggle("EnableAntiOwnership", {
 Text = "Anti Ownership",
 Default = false,
 Tooltip = "Спавнит еду, берёт в руки (ПКМ) и быстро ест (ЛКМ) — держит тебя 'занятым', сбивая захват/кик",
})

Toggles.EnableAntiOwnership:OnChanged(function()
 local plr = LocalPlayer
 local RS = game:GetService("ReplicatedStorage")

 -- Стоишь ли в своём плоте (доме)? Тогда предметы уходят в папку дома,
 -- а НЕ в обычный инвентарь (точно как в рабочем Anti Kick).
 local function antiOwnCheckForHome()
 local plotItems = workspace:FindFirstChild("PlotItems")
 local playersInPlots = plotItems and plotItems:FindFirstChild("PlayersInPlots")
 if not (playersInPlots and playersInPlots:FindFirstChild(plr.Name)) then
 return false
 end
 local plots = workspace:FindFirstChild("Plots")
 if not plots then return false end
 for _, v in pairs(plots:GetChildren()) do
 local sign = v:FindFirstChild("PlotSign")
 local owners = sign and sign:FindFirstChild("ThisPlotsOwners")
 if owners then
 for _, b in pairs(owners:GetChildren()) do
 if b.Value == plr.Name then
 local folder = plotItems:FindFirstChild(v.Name)
 if folder then
 return true, folder
 end
 end
 end
 end
 end
 return false
 end

 -- Активная папка со спавнутыми предметами: дом (если стоишь в плоте) либо инвентарь.
 -- Раньше искали ТОЛЬКО инвентарь — поэтому в своём плоте еда "пропадала".
 local function antiOwnInv()
 local inHome, house = antiOwnCheckForHome()
 if inHome and house then return house end
 return workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 end

 -- Текущее выбранное блюдо -> реальное имя игрушки
 local function antiOwnFoodName()
 local sel = Options.AntiOwnFood and Options.AntiOwnFood.Value
 return antiOwnFoods[sel] or "FoodHamburger"
 end

 -- Убираем всю нашу спавнутую еду (имена начинаются с "Food")
 local function antiOwnClear()
 local inv = antiOwnInv()
 local menuToys = RS:FindFirstChild("MenuToys")
 local destroyrem = menuToys and menuToys:FindFirstChild("DestroyToy")
 if inv and destroyrem then
 for _, v in pairs(inv:GetChildren()) do
 if string.sub(v.Name, 1, 4) == "Food" then
 pcall(function() destroyrem:FireServer(v) end)
 end
 end
 end
 end

 if Toggles.EnableAntiOwnership.Value then
 Library:Notify("Anti Ownership ВКЛ", 2)
 task.spawn(function()
 -- ВСЕ ожидания с таймаутом, чтобы поток НЕ висел навсегда
 local menuToys = RS:WaitForChild("MenuToys", 10)
 local spawnRemote = menuToys and menuToys:WaitForChild("SpawnToyRemoteFunction", 10)
 local destroyRemote = menuToys and menuToys:WaitForChild("DestroyToy", 10)
 local holdEvents = RS:WaitForChild("HoldEvents", 10)
 local holdUse = holdEvents and holdEvents:WaitForChild("Use", 10)
 -- CanSpawnToy может отсутствовать/��оявляться позже — НЕ виснем (таймаут 10с)
 local canSpawn = plr:WaitForChild("CanSpawnToy", 10)

 -- Диагностика: если чего-то нет — сразу пишем в чём дело, а не молчим
 if not spawnRemote then Library:Notify("Anti Ownership: не найден SpawnToyRemoteFunction", 5) return end
 if not holdUse then Library:Notify("Anti Ownership: не найден HoldEvents.Use", 5) return end
 if not canSpawn then Library:Notify("Anti Ownership: нет CanSpawnToy — спавню без гейта", 4) end

 local function antiOwnGetHRP()
 if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
 return plr.Character.HumanoidRootPart
 end
 local char = plr.CharacterAdded:Wait()
 return char:WaitForChild("HumanoidRootPart", 5)
 end

 -- Спавним еду и БЫСТРО ждём её появления (поллинг через Heartbeat)
 local antiOwnWarned = false
 local antiOwnHeldNotified = false
 local function antiOwnSpawn(name)
 -- ждём кулдаун спавна (обычн�� мгновенно); canSpawn может быть nil
 local t = tick()
 while canSpawn and not canSpawn.Value do
 if not Toggles.EnableAntiOwnership.Value or tick() - t > 3 then break end
 RunService.Heartbeat:Wait()
 end
 local hrp = antiOwnGetHRP()
 if not hrp then return nil end
 -- Спавн запускаем в ОТДЕЛЬНОМ потоке (как �� рабочем Anti Kick):
 -- InvokeServer у RemoteFunction ждёт ответ сервера и может подвесить
 -- ожидание — из-за этого еда порой не появлялась. Спавним не блокируя.
 task.spawn(function()
 local okSpawn, errSpawn = pcall(function()
 spawnRemote:InvokeServer(name, hrp.CFrame * CFrame.new(0, 5, -5), Vector3.new(0, 0, 0))
 end)
 if not okSpawn and not antiOwnWarned then
 antiOwnWarned = true
 Library:Notify("Anti Ownership: спавн '" .. tostring(name) .. "' упал (" .. tostring(errSpawn) .. ")", 6)
 end
 end)
 -- ждём появления игрушки, но возвращаем сразу как только есть
 local t1 = tick()
 repeat
 RunService.Heartbeat:Wait()
 local inv = antiOwnInv()
 local toy = inv and inv:FindFirstChild(name)
 if toy then return toy end
 until not Toggles.EnableAntiOwnership.Value or tick() - t1 > 2
 -- сюда попали = игрушка так и не появилась в инвентаре
 if not antiOwnWarned then
 antiOwnWarned = true
 Library:Notify("Anti Ownership: '" .. tostring(name) .. "' не появилась в инве��т��ре — проверь имя еды/ремоут", 6)
 end
 return nil
 end

 -- ============ ПОЕДАНИЕ (полность�� переписано по рабочему noname) ============
 -- Точная рабочая последовательность из готового скрипта:
 -- 1) взять в руки (ПКМ) и ДОЖДАТЬСЯ реального захвата;
 -- 2) нажать «есть» (HoldEvents.Use) — начинаем жевать;
 -- 3) ждать, пока EdiblePart ��е исчезнет (доедено);
 -- 4) снова нажать HoldEvents.Use (отпустить);
 -- 5) выбросить и удалить огрызок -> на след. тике заспавнить свежую.

 -- Прячем еду ЛОКАЛЬНО у себя (LocalTransparencyModifier = 1) — только на
 -- нашем экране, чтобы не видеть еду/жевание перед лицом. Для других игроков
 -- предмет остаётся видимым (нужно, чтобы «занятость» р��ально читалась сервером).
 local function antiOwnHideLocal(food)
 if not food then return end
 for _, d in ipairs(food:GetDescendants()) do
 if d:IsA("BasePart") then
 d.LocalTransparencyModifier = 1
 end
 end
 end

 -- Жмём «взять» повторно, пока еда реально не окажется у нас в руках.
 -- Признак захвата: RigidConstraint.Attachment1 наш ЛИБО HoldingPlayer == мы.
 local function antiOwnGrab(food, holdPart, holdRF, char)
 local t0 = tick()
 while food.Parent and Toggles.EnableAntiOwnership.Value and tick() - t0 < 3 do
 -- уже в руках?
 local rigid = holdPart:FindFirstChild("RigidConstraint")
 if rigid and rigid.Attachment1 and rigid.Attachment1:IsDescendantOf(char) then
 return true
 end
 local holding = holdPart:FindFirstChild("HoldingPlayer")
 if holding and holding.Value == plr then
 return true
 end
 -- Берём в руки РОВНО ОДИН раз, затем ждём реакции сервера.
 -- ВАЖНО: HoldItemRemoteFunction — переключатель (взять/бросить).
 -- Если дёргать его каждый кадр, захват тут же отменяется
 -- следующим вызовом — поэтому раньше еда бесконечно
 -- спавнилась и мгно��енно выкидывалась (без единого уведомления).
 -- Вызов в отдельном потоке: InvokeServer ждёт ответ сервера
 -- и иначе подвешивал бы нам весь цикл.
 task.spawn(function() pcall(function() holdRF:InvokeServer(food, char) end) end)
 antiOwnHideLocal(food)
 -- ждём до 0.5с появления захвата, лишь затем повторяем попытку
 local w = tick()
 repeat
 RunService.Heartbeat:Wait()
 local r = holdPart:FindFirstChild("RigidConstraint")
 if r and r.Attachment1 and r.Attachment1:IsDescendantOf(char) then
 return true
 end
 local h = holdPart:FindFirstChild("HoldingPlayer")
 if h and h.Value == plr then
 return true
 end
 until tick() - w > 0.5 or not food.Parent or not Toggles.EnableAntiOwnership.Value
 end
 return false
 end

 while Toggles.EnableAntiOwnership.Value do
 local char = plr.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if not hum or hum.Health <= 0 then task.wait(0.1) continue end

 local name = antiOwnFoodName()
 local inv = antiOwnInv()
 local food = inv and inv:FindFirstChild(name)

 -- нет еды -> спавним и получаем ссылку на неё
 if not food then
 food = antiOwnSpawn(name)
 if not food then task.wait(0.05) continue end
 end

 local holdPart = food:WaitForChild("HoldPart", 3)
 if not holdPart then task.wait(0.05) continue end
 local holdRF = holdPart:FindFirstChild("HoldItemRemoteFunction")
 local dropRF = holdPart:FindFirstChild("DropItemRemoteFunction")
 if not holdRF then task.wait(0.05) continue end

 -- 1) взять в руки (ПКМ) и дождаться захвата
 if not antiOwnGrab(food, holdPart, holdRF, char) then
 -- не удалось взять -> убрать и попробовать заново
 pcall(function() destroyRemote:FireServer(food) end)
 task.wait(0.1)
 continue
 end

 -- Одноразовое подтвержде��ие, что всё реально работает (еда просто спрятана у тебя на экране)
 if not antiOwnHeldNotified then
 antiOwnHeldNotified = true
 Library:Notify("Anti Ownership: еда в руках, защита активна", 3)
 end

 -- 2) П��ОСТО К��ИКАЕМ ��КМ, пока еда у нас в руках.
 -- НЕ едим специально, ничего сами НЕ роняем и НЕ удаляем —
 -- просто держим предмет и кликаем ЛКМ (Use), пока он в руках.
 -- Если еду выбили из рук �� выходим, внешний цикл заберёт её заново.
 while food.Parent and Toggles.EnableAntiOwnership.Value do
 local hum2 = plr.Character and plr.Character:FindFirstChildOfClass("Humanoid")
 if not hum2 or hum2.Health <= 0 then break end
 local holding = holdPart:FindFirstChild("HoldingPlayer")
 if holding and holding.Value and holding.Value ~= plr then break end
 local rigid = holdPart:FindFirstChild("RigidConstraint")
 local held = (rigid and rigid.Attachment1 and rigid.Attachment1:IsDescendantOf(char))
 or (holding and holding.Value == plr)
 -- кликаем ЛКМ ТОЛЬКО ког��а еда реаль��о в руках
 if held then
 antiOwnHideLocal(food)
 pcall(function() holdUse:FireServer(food) end)
 else
 break
 end
 task.wait(0.1)
 end

 -- Сюда попадаем только если ед�� пропала/выбили ИЛИ выключили тумблер.
 -- Чистим ос��а��ок (��сли ещё ��исит) -> внешний цикл заспавнит свежую.
 if food and food.Parent and not Toggles.EnableAntiOwnership.Value then
 pcall(function() destroyRemote:FireServer(food) end)
 end
 task.wait(0.05)
 end
 end)
 else
 antiOwnClear()
 Library:Notify("Anti Ownership ВЫКЛ", 2)
 end
end)
end

-- ==============================================
-- Вкладка: DEF — Anti Loop Kick (Loop TP to House)
-- ==============================================
-- Вкладка: DEF — Anti Loop Kick (Zone-based)
-- ==============================================
local DefAntiLoopKick = Tabs.Def:AddRightGroupbox("Anti Loop Kick")

antiLoopKickConn = nil
antiLoopKickHouse = nil
antiLoopKickRadius = 10

DefAntiLoopKick:AddDropdown("AntiLoopKickHouse", {
 Text = "Дом для Loop TP",
 Default = "Green House",
 Values = {"Green House", "Pink House", "Witch House", "Blue House", "China House"},
 Multi = false,
 Tooltip = "Выбери дом",
 Callback = function(Value)
 antiLoopKickHouse = Value
 end
})

DefAntiLoopKick:AddToggle("EnableAntiLoopKick", {
 Text = "Anti Loop Kick",
 Default = false,
 Tooltip = "Zone-based, radius 10",
 Callback = function(Value)
 if Value then
 local houseName = antiLoopKickHouse or (Options.AntiLoopKickHouse and Options.AntiLoopKickHouse.Value) or "Green House"
 local cf = houseMapPoints and houseMapPoints[houseName]
 if not cf then
 local pts = {
 ["Green House"] = CFrame.new(-548.305054, -2.45424771, 79.3213348),
 ["Pink House"] = CFrame.new(-475.493835, -2.70774508, -159.395279),
 ["Witch House"] = CFrame.new(270.225922, -2.48055029, 458.186493),
 ["Blue House"] = CFrame.new(501.939911, 88.2323608, -349.129211),
 ["China House"] = CFrame.new(545.441833, 128.004593, -99.4881439)
 }
 cf = pts[houseName]
 end
 if not cf then return end
 local housePos = cf.Position
 antiLoopKickConn = RunService.Heartbeat:Connect(function()
 local char = LocalPlayer.Character
 if not char then return end
 local root = char:FindFirstChild("HumanoidRootPart")
 if not root then return end
 if (root.Position - housePos).Magnitude > antiLoopKickRadius then
 char:PivotTo(cf)
 end
 end)
 Library:Notify("Anti Loop Kick ВКЛ: " .. houseName, 2)
 else
 if antiLoopKickConn then antiLoopKickConn:Disconnect() antiLoopKickConn = nil end
 local char = LocalPlayer.Character
 if char and char:FindFirstChild("HumanoidRootPart") then
 char.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
 char.HumanoidRootPart.AssemblyAngularVelocity = Vector3.zero
 end
 Library:Notify("Anti Loop Kick ВЫКЛ", 2)
 end
 end
})

-- ==============================================
-- Вкладка: VISUAL
-- Вкладка: VISUAL
-- ==============================================
-- ==============================================
-- DEF - COUNTER MODE
-- ==============================================
DefCounterMode = Tabs.Def:AddRightGroupbox("Counter Mode")

DefCounterMode:AddToggle("EnableCounterMode", {
 Text = "Counter Mode",
 Default = false,
 Tooltip = "Когда тебя трогают — применяет эффект к игроку",
})

DefCounterMode:AddDropdown("CounterModeType", {
 Text = "Режим",
 Default = "Отскок",
 Values = {"Отскок", "Умереть", "Флинг"},
 Multi = false,
 Tooltip = "Что произойдет с игроком который тебя тронет",
})

DefCounterMode:AddSlider("CounterRepulsionForce", {
 Text = "Сила отскока",
 Default = 100,
 Min = 10,
 Max = 500,
 Rounding = 0,
 Increment = 10,
 Tooltip = "Сила с которой игрок отлетит",
})

DefCounterMode:AddSlider("CounterFlingForce", {
 Text = "Сила флинга",
 Default = 400,
 Min = 100,
 Max = 5000,
 Rounding = 0,
 Increment = 50,
 Tooltip = "Сила с которой игрок улетит вверх",
})

counterModeConn = nil

function counterModeLookAt(startPos, targetPos)
 local dir = (targetPos - startPos).Unit
 local right = dir:Cross(Vector3.new(0, 1, 0))
 local up = right:Cross(dir)
 return CFrame.fromMatrix(startPos, right, up)
end

function counterModeApply(otherPlayer)
 if not otherPlayer or not otherPlayer.Character then return end
 local otherChar = otherPlayer.Character
 local otherRoot = otherChar:FindFirstChild("HumanoidRootPart")
 local otherHum = otherChar:FindFirstChildOfClass("Humanoid")
 if not otherRoot or not otherHum then return end
 local myChar = LocalPlayer.Character
 if not myChar then return end
 local myRoot = myChar:FindFirstChild("HumanoidRootPart")
 if not myRoot then return end
 local mode = Options.CounterModeType and Options.CounterModeType.Value or "Отскок"
 local setOwner = freezeGrabGetNetworkOwner()
 local destroyLine = combatGrabGetDestroyLine()
 if setOwner then
 pcall(function() setOwner:FireServer(otherRoot, otherRoot.CFrame) end)
 end
 task.wait()
 if mode == "Отскок" then
 local force = (Options.CounterRepulsionForce and Options.CounterRepulsionForce.Value) or 100
 local cf = counterModeLookAt(myRoot.Position, otherRoot.Position)
 local bv = Instance.new("BodyVelocity")
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.Velocity = Vector3.new(cf.lookVector.X, 0.5, cf.lookVector.Z) * force
 bv.Parent = otherRoot
 task.wait(0.1)
 bv:Destroy()
 if destroyLine then pcall(function() destroyLine:FireServer(otherRoot) end) end
 elseif mode == "Умереть" then
 local bv = Instance.new("BodyVelocity")
 bv.Name = "SkyVelocity"
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.Velocity = Vector3.new(0, 100000, 0)
 bv.Parent = otherRoot
 for _ = 1, 20 do
 pcall(function()
 otherHum.BreakJointsOnDeath = false
 otherHum:ChangeState(Enum.HumanoidStateType.Dead)
 otherHum.Jump = true
 otherHum.Sit = true
 end)
 task.wait()
 end
 bv:Destroy()
 if destroyLine then pcall(function() destroyLine:FireServer(otherRoot) end) end
 elseif mode == "Флинг" then
 local force = (Options.CounterFlingForce and Options.CounterFlingForce.Value) or 400
 local bv = Instance.new("BodyVelocity")
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.Velocity = Vector3.new(0, force, 0)
 bv.Parent = otherRoot
 task.wait(0.5)
 bv:Destroy()
 if destroyLine then pcall(function() destroyLine:FireServer(otherRoot) end) end
 end
end

counterCooldown = {}

function counterModeHook(char)
 if not char then return end
 char.DescendantAdded:Connect(function(desc)
 if desc.Name == "PartOwner" and Toggles.EnableCounterMode and Toggles.EnableCounterMode.Value then
 local ownerName = tostring(desc.Value)
 if ownerName == LocalPlayer.Name then return end
 local otherPlayer = game:GetService("Players"):FindFirstChild(ownerName)
 if otherPlayer and otherPlayer ~= LocalPlayer then
 task.spawn(function() counterModeApply(otherPlayer) end)
 end
 end
 end)
 for _, part in ipairs(char:GetDescendants()) do
 if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
 part.Touched:Connect(function(hit)
 if not Toggles.EnableCounterMode or not Toggles.EnableCounterMode.Value then return end
 if not hit or not hit.Parent then return end
 local hitModel = hit.Parent
 local otherChar = nil
 local otherPlayer = nil
 for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character and hit:IsDescendantOf(plr.Character) then
 otherChar = plr.Character
 otherPlayer = plr
 break
 end
 end
 if not otherPlayer then return end
 local now = tick()
 if counterCooldown[otherPlayer.Name] and now - counterCooldown[otherPlayer.Name] < 0.5 then return end
 counterCooldown[otherPlayer.Name] = now
 task.spawn(function() counterModeApply(otherPlayer) end)
 end)
 end
 end
 char.DescendantAdded:Connect(function(desc)
 if desc:IsA("BasePart") and Toggles.EnableCounterMode and Toggles.EnableCounterMode.Value then
 desc.Touched:Connect(function(hit)
 if not Toggles.EnableCounterMode or not Toggles.EnableCounterMode.Value then return end
 if not hit or not hit.Parent then return end
 for _, plr in ipairs(game:GetService("Players"):GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character and hit:IsDescendantOf(plr.Character) then
 local now = tick()
 if counterCooldown[plr.Name] and now - counterCooldown[plr.Name] < 0.5 then return end
 counterCooldown[plr.Name] = now
 task.spawn(function() counterModeApply(plr) end)
 break
 end
 end
 end)
 end
 end)
end

Toggles.EnableCounterMode:OnChanged(function()
 if Toggles.EnableCounterMode.Value then
 if LocalPlayer.Character then counterModeHook(LocalPlayer.Character) end
 counterModeConn = LocalPlayer.CharacterAdded:Connect(function(char)
 task.wait(0.5)
 counterModeHook(char)
 end)
 else
 if counterModeConn then counterModeConn:Disconnect() counterModeConn = nil end
 end
end)

-- ==============================================
-- DEF — ANTI PAINT ( mechanics)
-- ==============================================
DefAntiPaint = Tabs.Def:AddRightGroupbox("Anti Paint")

DefAntiPaint:AddToggle("EnableAntiPaint", {
 Text = "Anti Paint",
 Default = false,
 Tooltip = "\u{0423}\u{0434}\u{0430}\u{043b}\u{044f}\u{0435}\u{0442} \u{043a}\u{0440}\u{0430}\u{0441}\u{043a}\u{0443} \u{0438} \u{043d}\u{0435} \u{0434}\u{0430}\u{0451}\u{0442} \u{043d}\u{043e}\u{0432}\u{043e}\u{0439} \u{043b}\u{0435}\u{0447}\u{044c}",
})

antiPaintConns = {}

function antiPaintDeleteAll()
 for _, obj in ipairs(workspace:GetDescendants()) do
 if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
 pcall(function() obj:Destroy() end)
 end
 end
end

function antiPaintWatch()
 table.insert(antiPaintConns, workspace.DescendantAdded:Connect(function(obj)
 if obj:IsA("BasePart") and obj.Name == "PaintPlayerPart" then
 task.defer(function()
 if obj and obj.Parent then pcall(function() obj:Destroy() end) end
 end)
 end
 end))
end

function antiPaintSetTouch(state)
 local char = LocalPlayer.Character
 if not char then return end
 for _, v in ipairs(char:GetChildren()) do
 if v:IsA("Part") or v:IsA("BasePart") then
 v.CanTouch = state
 v.CanQuery = state
 end
 end
end

function antiPaintCleanup()
 for _, conn in ipairs(antiPaintConns) do
 pcall(function() conn:Disconnect() end)
 end
 antiPaintConns = {}
 antiPaintSetTouch(true)
end

Toggles.EnableAntiPaint:OnChanged(function()
 if Toggles.EnableAntiPaint.Value then
 antiPaintDeleteAll()
 antiPaintWatch()
 antiPaintSetTouch(false)
 Library:Notify("Anti Paint \u{0412}\u{041a}\u{041b}", 2)
 else
 antiPaintCleanup()
 Library:Notify("Anti Paint \u{0412}\u{042b}\u{041a}\u{041b}", 2)
 end
end)

-- ==============================================
-- DEF — ANTI STICK / SNOWBALL / BANANA / POISON
-- ==============================================
DefAntiItems = Tabs.Def:AddRightGroupbox("Anti Items")

DefAntiItems:AddToggle("EnableAntiStick", {
 Text = "Anti Sticky",
 Default = false,
 Tooltip = "Remove sticky items from you + send underground",
})

antiStickyRunning = false

Toggles.EnableAntiStick:OnChanged(function()
 if Toggles.EnableAntiStick.Value then
 antiStickyRunning = true
 local sptd = LocalPlayer.PlayerScripts:FindFirstChild("StickyPartsTouchDetection")
 if sptd then sptd.Disabled = true end
 local setOwner = freezeGrabGetNetworkOwner()
 local destroyLine = combatGrabGetDestroyLine()
 task.spawn(function()
 while antiStickyRunning do
 local char = LocalPlayer.Character
 if char then
 for _, obj in ipairs(char:GetDescendants()) do
 if not antiStickyRunning then break end
 if obj.Name == "StickyWeld" and obj:IsA("Weld") and obj.Part1 then
 local sp = obj.Parent
 if sp and sp:IsA("BasePart") then
 if setOwner then
 pcall(function() setOwner:FireServer(sp, sp.CFrame) end)
 end
 if destroyLine then
 pcall(function() destroyLine:FireServer(sp) end)
 end
 pcall(function() obj.Part1 = nil end)
 pcall(function() obj:Destroy() end)
 pcall(function() sp.Anchored = true end)
 pcall(function() sp.CFrame = CFrame.new(0, -500, 0) end)
 task.spawn(function()
 task.wait(0.5)
 pcall(function() sp:Destroy() end)
 end)
 end
 end
 end
 for _, obj in ipairs(workspace:GetChildren()) do
 if not antiStickyRunning then break end
 if obj:IsA("Model") and obj.Name == "NinjaShuriken" then
 local sp = obj:FindFirstChild("StickyPart")
 if sp and sp:IsA("BasePart") then
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if hrp and (sp.Position - hrp.Position).Magnitude < 30 then
 if setOwner then
 pcall(function() setOwner:FireServer(sp, sp.CFrame) end)
 end
 if destroyLine then
 pcall(function() destroyLine:FireServer(sp) end)
 end
 for _, w in ipairs(obj:GetDescendants()) do
 if w:IsA("Weld") and w.Name == "StickyWeld" then
 pcall(function() w.Part1 = nil end)
 pcall(function() w:Destroy() end)
 end
 end
 pcall(function() sp.Anchored = true end)
 pcall(function() sp.CFrame = CFrame.new(0, -500, 0) end)
 task.spawn(function()
 task.wait(0.5)
 pcall(function() obj:Destroy() end)
 end)
 end
 end
 end
 end
 end
 task.wait(0.2)
 end
 end)
 Library:Notify("Anti Sticky ON", 2)
 else
 antiStickyRunning = false
 Library:Notify("Anti Sticky OFF", 2)
 end
end)

DefAntiItems:AddToggle("EnableAntiSnowball", {
 Text = "Anti Snowball",
 Default = false,
 Tooltip = "Cancel ragdoll when snowball hits",
})

antiSnowballRunning = false

Toggles.EnableAntiSnowball:OnChanged(function()
 if Toggles.EnableAntiSnowball.Value then
 antiSnowballRunning = true
 local rs = game:GetService("ReplicatedStorage")
 local ce = rs:FindFirstChild("CharacterEvents")
 local ragdollRemote = ce and (ce:FindFirstChild("RagdollRemote") or ce:WaitForChild("RagdollRemote", 5))
 task.spawn(function()
 while antiSnowballRunning do
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 if hum and hrp and hum.Health > 0 then
 local state = hum:GetState()
 if hum.PlatformStand or state == Enum.HumanoidStateType.Ragdoll or state == Enum.HumanoidStateType.Physics or state == Enum.HumanoidStateType.FallingDown then
 if ragdollRemote then
 pcall(function() ragdollRemote:FireServer(hrp, 0) end)
 end
 pcall(function() hum:ChangeState(Enum.HumanoidStateType.Running) end)
 end
 end
 task.wait(0.1)
 end
 end)
 Library:Notify("Anti Snowball ON", 2)
 else
 antiSnowballRunning = false
 Library:Notify("Anti Snowball OFF", 2)
 end
end)

DefAntiItems:AddToggle("EnableAntiBanana", {
 Text = "Anti Banana",
 Default = false,
 Tooltip = "Prevents banana slip",
})

antiBananaRunning = false

Toggles.EnableAntiBanana:OnChanged(function()
 if Toggles.EnableAntiBanana.Value then
 antiBananaRunning = true
 task.spawn(function()
 while antiBananaRunning do
 local char = LocalPlayer.Character
 if char then
 local hum = char:FindFirstChildOfClass("Humanoid")
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if hum and hrp and hum.Health > 0 then
 hum.Sit = true
 hum:ChangeState(Enum.HumanoidStateType.Running)
 local cam = workspace.CurrentCamera
 local vec = cam and cam.CFrame.LookVector or Vector3.new(0, 0, -1)
 hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(vec.X, 0, vec.Z))
 end
 end
 task.wait()
 end
 end)
 Library:Notify("Anti Banana ON", 2)
 else
 antiBananaRunning = false
 Library:Notify("Anti Banana OFF", 2)
 end
end)

DefAntiItems:AddToggle("EnableAntiPoison", {
 Text = "Anti Poison",
 Default = false,
 Tooltip = "Destroy poison mushrooms",
})

antiPoisonConns = {}

antiPoisonConns = {}

function antiPoisonCleanup()
 for _, conn in ipairs(antiPoisonConns) do
 pcall(function() conn:Disconnect() end)
 end
 antiPoisonConns = {}
end

Toggles.EnableAntiPoison:OnChanged(function()
 if Toggles.EnableAntiPoison.Value then
 local setOwner = freezeGrabGetNetworkOwner()
 local function destroyPoison()
 for _, obj in ipairs(workspace:GetDescendants()) do
 if obj:IsA("BasePart") then
 local n = obj.Name:lower()
 if n:find("poison") or n:find("mushroom") or n:find("foodmush") then
 if setOwner then
 pcall(function() setOwner:FireServer(obj, obj.CFrame) end)
 end
 pcall(function() obj:Destroy() end)
 end
 end
 end
 end
 destroyPoison()
 table.insert(antiPoisonConns, workspace.DescendantAdded:Connect(function(obj)
 if obj:IsA("BasePart") then
 local n = obj.Name:lower()
 if n:find("poison") or n:find("mushroom") or n:find("foodmush") then
 task.defer(function()
 if setOwner and obj and obj.Parent then
 pcall(function() setOwner:FireServer(obj, obj.CFrame) end)
 end
 if obj and obj.Parent then pcall(function() obj:Destroy() end) end
 end)
 end
 end
 end))
 local healConn
 healConn = game:GetService("RunService").Heartbeat:Connect(function()
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if hum and hum.Health > 0 and hum.Health < hum.MaxHealth then
 pcall(function() hum.Health = hum.MaxHealth end)
 end
 if char then
 for _, v in ipairs(char:GetDescendants()) do
 local vn = v.Name:lower()
 if vn:find("poison") or vn:find("sick") or vn:find("toxic") then
 if v:IsA("BoolValue") then pcall(function() v.Value = false end) end
 if v:IsA("NumberValue") then pcall(function() v.Value = 0 end) end
 if v:IsA("IntValue") then pcall(function() v.Value = 0 end) end
 if v:IsA("StringValue") then pcall(function() v.Value = "" end) end
 end
 end
 end
 end)
 table.insert(antiPoisonConns, healConn)
 Library:Notify("Anti Poison ON", 2)
 else
 antiPoisonCleanup()
 Library:Notify("Anti Poison OFF", 2)
 end
end)


-- ==============================================
-- DEF — AUTO RESET ( mechanics)
-- ==============================================
local DefAutoReset = Tabs.Def:AddRightGroupbox("Auto Reset")


do
do
local pcldActive = false
local pcldConn = nil
local pcldDeathConn = nil

local function stopPCLD()
 pcldActive = false
 if pcldConn then pcldConn:Disconnect(); pcldConn = nil end
 if pcldDeathConn then pcldDeathConn:Disconnect(); pcldDeathConn = nil end
end

local function startPCLD()
 stopPCLD()
 pcldActive = true
 task.spawn(function()
  local isFirstCycle = true
  while pcldActive do
   local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
   local hrp = char:WaitForChild("HumanoidRootPart", 5)
   local hum = char:WaitForChild("Humanoid", 5)
   if not hrp or not hum then task.wait(0.5) continue end
   if hum.Health <= 0 then
    char = LocalPlayer.CharacterAdded:Wait()
    hrp = char:WaitForChild("HumanoidRootPart", 5)
    hum = char:WaitForChild("Humanoid", 5)
   end
   if not hrp or not hum then continue end

   if isFirstCycle then
    -- 1. Save Position
    local savedCF = hrp.CFrame
    -- 2. Teleport High & Kill
    hrp.CFrame = CFrame.new(hrp.Position.X, 50000, hrp.Position.Z)
    task.wait(0.05)
    hum.Health = 0
    -- 3. Wait for Respawn
    char = LocalPlayer.CharacterAdded:Wait()
    if not pcldActive then break end
    hrp = char:WaitForChild("HumanoidRootPart", 5)
    hum = char:WaitForChild("Humanoid", 5)
    if hrp and hum then
     task.wait(0.1)
     -- 4. Teleport back & Kill again
     hrp.CFrame = savedCF
     task.wait(0.05)
     hum.Health = 0
    end
    isFirstCycle = false
   else
    -- Subsequent respawns: Kill once immediately
    task.wait(0.1)
    if hum then
     hum.Health = 0
     pcall(function() char:BreakJoints() end)
    end
   end

   char = LocalPlayer.CharacterAdded:Wait()
   if not pcldActive then break end
   hum = char:WaitForChild("Humanoid", 5)
   if hum then
    -- Wait until player naturally dies to repeat the cycle
    hum.Died:Wait()
   end
  end
 end)
end

DefAutoReset:AddToggle("EnablePCLDBreak", {
 Text = "Auto PCLD Break",
 Default = false,
 Tooltip = "Хитрая система смертей, которая ломает хитбоксы и делает тебя бессмертным",
 Callback = function(Value)
  if Value then
   startPCLD()
  else
   stopPCLD()
  end
 end
})
end



-- ==============================================
-- Вкладка: DEFENSE — GUCCI (Godmode & Invisible)
-- ==============================================
do
local DefGucci = Tabs.Def:AddRightGroupbox("Gucci (Godmode & Invis)")

local gucciConn = nil
local gucciActive = false
local gucciVehicle = nil

local function stopGucci()
 gucciActive = false
 if gucciConn then gucciConn:Disconnect(); gucciConn = nil end
 
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if hum then
  hum.Sit = false
  hum:ChangeState(Enum.HumanoidStateType.GettingUp)
 end
 
 if gucciVehicle and gucciVehicle.Parent then
  pcall(function()
   local RS = game:GetService("ReplicatedStorage")
   RS.MenuToys.DestroyToy:FireServer(gucciVehicle)
  end)
 end
 gucciVehicle = nil
end

local function startGucci(mode)
 stopGucci()
 gucciActive = true
 
 task.spawn(function()
  local char = LocalPlayer.Character
  local hrp = char and char:WaitForChild("HumanoidRootPart", 3)
  local hum = char and char:WaitForChild("Humanoid", 3)
  if not hrp or not hum then return end
  
  local RS = game:GetService("ReplicatedStorage")
  local savedCF = hrp.CFrame
  
  local seat = nil
  local isTrain = false
  
  if mode == "Anti Kill (Train)" then
   isTrain = true
   local trainFolder = workspace:FindFirstChild("Map") and workspace.Map:FindFirstChild("AlwaysHereTweenedObjects")
   local train = trainFolder and trainFolder:FindFirstChild("Train")
   if train then
    for _, d in ipairs(train:GetDescendants()) do
     if d:IsA("Seat") then
      seat = d
      break
     end
    end
   end
   if not seat then
    Library:Notify("Поезд не найден на карте!", 3)
    stopGucci()
    return
   end
  else
   local toyName = "TractorGreen"
   if mode == "Godmode (Blobman)" then
    toyName = "CreatureBlobman"
   end
   
   -- Spawn toy
   pcall(function()
    RS.MenuToys.SpawnToyRemoteFunction:InvokeServer(toyName, CFrame.new(0, 50000, 0), Vector3.zero)
   end)
   
   local folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", 5)
   if folder then
    local toy = folder:WaitForChild(toyName, 5)
    if toy then
     gucciVehicle = toy
     seat = toy:WaitForChild("VehicleSeat", 3) or toy:FindFirstChildWhichIsA("VehicleSeat", true) or toy:FindFirstChildWhichIsA("Seat", true)
    end
   end
  end
  
  if not seat then return end
  
  -- Force Sit
  local t0 = tick()
  while seat.Occupant ~= hum and (tick() - t0) < 3 and gucciActive do
   if not isTrain then
    seat.CFrame = CFrame.new(0, 50000, 0)
   end
   hrp.CFrame = seat.CFrame + Vector3.new(0, 2, 0)
   seat:Sit(hum)
   task.wait(0.05)
  end
  
  if not gucciActive then return end
  
  hrp.CFrame = savedCF
  
  -- Maintain loop
  gucciConn = game:GetService("RunService").Heartbeat:Connect(function()
   if not hrp or not hrp.Parent or not hum then return end
   
   pcall(function() RS.CharacterEvents.RagdollRemote:FireServer(hrp, 0) end)
   
   if not isTrain and seat and seat.Parent then
    seat.CFrame = CFrame.new(0, 50000, 0)
    seat.AssemblyLinearVelocity = Vector3.zero
    seat.AssemblyAngularVelocity = Vector3.zero
   end
   
   -- Lock player back to real position so they don't drift to sky
   if hum.Sit then
    hrp.CFrame = savedCF
   else
    savedCF = hrp.CFrame
   end
  end)
  
  Library:Notify("Gucci Mode (" .. mode .. ") Активирован!", 2)
 end)
end

DefGucci:AddToggle("EnableGucci", {
 Text = "Включить Gucci Mode",
 Default = false,
 Tooltip = "Делает тебя бессмертным или невидимым за счет бага",
 Callback = function(Value)
  if Value then
   local mode = Options.GucciType and Options.GucciType.Value or "Invisible (Tractor)"
   startGucci(mode)
  else
   stopGucci()
   Library:Notify("Gucci Mode Отключен", 2)
  end
 end
})

DefGucci:AddDropdown("GucciType", {
 Text = "Тип Gucci",
 Default = "Invisible (Tractor)",
 Values = {"Invisible (Tractor)", "Godmode (Blobman)", "Anti Kill (Train)"},
 Tooltip = "Tractor - невидимость, Blobman - Годмод, Train - защита через поезд",
})

Options.GucciType:OnChanged(function()
 if Toggles.EnableGucci and Toggles.EnableGucci.Value then
  local mode = Options.GucciType and Options.GucciType.Value
  startGucci(mode)
 end
end)
end



end
DefAutoReset:AddToggle("EnableAutoReset", {
 Text = "Auto Reset",
 Default = false,
 Tooltip = "\u{0410}\u{0432}\u{0442}\u{043e}\u{043c}\u{0430}\u{0442}\u{0438}\u{0447}\u{0435}\u{0441}\u{043a}\u{0438} \u{0440}\u{0435}\u{0441}\u{0435}\u{0442}\u{0438}\u{0442} \u{0435}\u{0441}\u{043b}\u{0438} \u{0441}\u{0435}\u{0440}\u{0432}\u{0435}\u{0440} \u{043f}\u{0438}\u{0448}\u{0435}\u{0442} Flying (\u{0430}\u{043d}\u{0442}\u{0438}-\u{0431}\u{0430}\u{043d})",
})

autoResetConn = nil

Toggles.EnableAutoReset:OnChanged(function()
 if Toggles.EnableAutoReset.Value then
 local rs = game:GetService("ReplicatedStorage")
 local gce = rs:FindFirstChild("GameCorrectionEvents")
 local notify = gce and gce:FindFirstChild("GameCorrectionsNotify")
 if notify then
 autoResetConn = notify.OnClientEvent:Connect(function(r)
 if r == "Flying" then
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if hum then
 Library:Notify("Auto Reset: \u{0440}\u{0435}\u{0441}\u{0435}\u{0442} \u{043e}\u{0442} \u{0431}\u{0430}\u{043d}\u{0430}", 3)
 pcall(function() char:BreakJoints() end)
 pcall(function() hum.Health = 0 end)
 end
 end
 end)
 end
 Library:Notify("Auto Reset ВКЛ", 2)
 else
 if autoResetConn then autoResetConn:Disconnect() autoResetConn = nil end
 Library:Notify("Auto Reset ВЫКЛ", 2)
 end
end)

-- Loop Reset — бесконечно ресетает character
DefAutoReset:AddToggle("EnableLoopReset", {
 Text = "Loop Reset",
 Default = false,
 Tooltip = "Бесконечно ресетает персонажа (BreakJoints + Health=0)",
})

loopResetConn = nil

Toggles.EnableLoopReset:OnChanged(function()
 if Toggles.EnableLoopReset.Value then
 loopResetConn = task.spawn(function()
 while Toggles.EnableLoopReset and Toggles.EnableLoopReset.Value do
 local char = LocalPlayer.Character
 if char then
 local hum = char:FindFirstChildOfClass("Humanoid")
 if hum and hum.Health > 0 then
 pcall(function() char:BreakJoints() end)
 pcall(function() hum.Health = 0 end)
 end
 end
 task.wait(1)
 end
 end)
 Library:Notify("Loop Reset ВКЛ", 2)
 else
 if loopResetConn then task.cancel(loopResetConn) loopResetConn = nil end
 Library:Notify("Loop Reset ВЫКЛ", 2)
 end
end)

-- ==============================================
-- DEF — ANTI BLOBMAN ( mechanics)
-- ==============================================
DefAntiBlobman = Tabs.Def:AddRightGroupbox("Anti Blobman")

DefAntiBlobman:AddToggle("EnableAntiBlobman", {
 Text = "Anti Blobman",
 Default = false,
 Tooltip = "Нельзя тебя схватить + удаляет детекторы",
})

antiBlobmanRunning = false
antiBlobmanDescConn = nil
antiBlobmanHeartbeat = nil
antiBlobmanLoopConn = nil

function antiBlobmanFindAllBlobs()
 local blobs = {}
 local plotItems = workspace:FindFirstChild("PlotItems")
 if plotItems then
 for _, plot in pairs(plotItems:GetChildren()) do
 if plot.Name ~= "PlayersInPlots" then
 for _, item in pairs(plot:GetChildren()) do
 if item.Name == "CreatureBlobman" then
 table.insert(blobs, item)
 end
 end
 end
 end
 end
 for _, plr in pairs(game:GetService("Players"):GetPlayers()) do
 local toyFolder = workspace:FindFirstChild(plr.Name .. "SpawnedInToys")
 if toyFolder then
 for _, item in pairs(toyFolder:GetChildren()) do
 if item.Name == "CreatureBlobman" then
 table.insert(blobs, item)
 end
 end
 end
 end
 for _, child in pairs(workspace:GetChildren()) do
 if child.Name == "CreatureBlobman" then
 table.insert(blobs, child)
 end
 end
 return blobs
end

function antiBlobmanFireDrop(blob, hrp)
 local sObj = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local rightDetector = blob:FindFirstChild("RightDetector")
 local leftDetector = blob:FindFirstChild("LeftDetector")
 if not sObj then return end
 local dropEvent = sObj:FindFirstChild("CreatureDrop")
 if not dropEvent then return end
 if rightDetector then
 local rightWeld = rightDetector:FindFirstChild("RightWeld")
 if rightWeld then pcall(function() dropEvent:FireServer(rightWeld, hrp) end) end
 end
 if leftDetector then
 local leftWeld = leftDetector:FindFirstChild("LeftWeld")
 if leftWeld then pcall(function() dropEvent:FireServer(leftWeld, hrp) end) end
 end
 local ce = game:GetService("ReplicatedStorage"):FindFirstChild("CharacterEvents")
 if ce then
 local struggle = ce:FindFirstChild("Struggle")
 if struggle then pcall(function() struggle:FireServer(LocalPlayer) end) end
 end
end

Toggles.EnableAntiBlobman:OnChanged(function()
 if Toggles.EnableAntiBlobman.Value then
 antiBlobmanRunning = true
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 if hrp and char and not char:FindFirstChild("TruePositionPart") then
 local tpp = Instance.new("Part")
 tpp.Parent = char
 tpp.Name = "TruePositionPart"
 tpp.Anchored = true
 tpp.Transparency = 1
 tpp.CanCollide = false
 tpp.Size = Vector3.new(0.1, 0.1, 0.1)
 tpp.CFrame = CFrame.new(0, -10000000, 0)
 end
 antiBlobmanDescConn = workspace.DescendantAdded:Connect(function(toy)
 if toy.Name == "CreatureBlobman" and antiBlobmanRunning then
 task.defer(function()
 local leftDetector = toy:WaitForChild("LeftDetector", 3)
 local rightDetector = toy:WaitForChild("RightDetector", 3)
 local c = LocalPlayer.Character
 local h = c and c:FindFirstChild("HumanoidRootPart")
 if h and toy:FindFirstChild("Head") then
 local d = (toy.Head.Position - h.Position).Magnitude
 local curDist = 15
 if d <= curDist then
 if leftDetector then pcall(function() leftDetector:Destroy() end) end
 if rightDetector then pcall(function() rightDetector:Destroy() end) end
 end
 end
 end)
 end
 end)
 antiBlobmanHeartbeat = RunService.Heartbeat:Connect(function()
 local c = LocalPlayer.Character
 if not c then return end
 local h = c:FindFirstChild("HumanoidRootPart")
 if not h then return end
 local tpp = c:FindFirstChild("TruePositionPart")
 if tpp and h then
 local rootAtt = h:FindFirstChild("RootAttachment")
 if rootAtt and rootAtt.Parent == h then
 pcall(function() rootAtt.Parent = tpp end)
 end
 end
 local isGrabbed = false
 for _, part in pairs(c:GetChildren()) do
 if part:IsA("Part") and part.Massless then
 part.Massless = false
 isGrabbed = true
 end
 end
 if isGrabbed then
 h.AssemblyLinearVelocity = Vector3.new(0, 15000000, 0)
 local blobs = antiBlobmanFindAllBlobs()
 for _, blob in ipairs(blobs) do
 pcall(function() antiBlobmanFireDrop(blob, h) end)
 end
 end
 end)
 antiBlobmanLoopConn = RunService.Heartbeat:Connect(function()
 local c = LocalPlayer.Character
 local h = c and c:FindFirstChild("HumanoidRootPart")
 if not h then return end
 local curDist = 15
 local blobs = antiBlobmanFindAllBlobs()
 for _, blob in ipairs(blobs) do
 local blobHead = blob:FindFirstChild("Head")
 if blobHead then
 local d = (blobHead.Position - h.Position).Magnitude
 if d <= curDist then
 local ld = blob:FindFirstChild("LeftDetector")
 local rd = blob:FindFirstChild("RightDetector")
 if ld and ld.Parent then pcall(function() ld:Destroy() end) end
 if rd and rd.Parent then pcall(function() rd:Destroy() end) end
 end
 end
 end
 end)
 Library:Notify("Anti Blobman ВКЛ", 2)
 else
 antiBlobmanRunning = false
 if antiBlobmanDescConn then antiBlobmanDescConn:Disconnect() antiBlobmanDescConn = nil end
 if antiBlobmanHeartbeat then antiBlobmanHeartbeat:Disconnect() antiBlobmanHeartbeat = nil end
 if antiBlobmanLoopConn then antiBlobmanLoopConn:Disconnect() antiBlobmanLoopConn = nil end
 local c = LocalPlayer.Character
 local h = c and c:FindFirstChild("HumanoidRootPart")
 local tpp = c and c:FindFirstChild("TruePositionPart")
 if h and tpp then
 local rootAtt = tpp:FindFirstChild("RootAttachment")
 if rootAtt then pcall(function() rootAtt.Parent = h end) end
 pcall(function() tpp:Destroy() end)
 end
 Library:Notify("Anti Blobman ВЫКЛ", 2)
 end
end)





VisualCamera = Tabs.Visual:AddLeftGroupbox("Camera")
defaultFOV = 70 

function UpdateFOV()
 local camera = workspace.CurrentCamera
 if camera then
 if Toggles.EnableFOV.Value then
 camera.FieldOfView = Options.FOVValue.Value
 else
 camera.FieldOfView = defaultFOV
 end
 end
end

VisualCamera:AddToggle("EnableFOV", {
 Text = "Custom FOV",
 Default = false,
 Tooltip = "Включить кастомный Field of View",
 Callback = function(Value)
 UpdateFOV()
 end
})

VisualCamera:AddSlider("FOVValue", {
 Text = "FOV Amount",
 Default = 70,
 Min = 0,
 Max = 120,
 Rounding = 0,
 Compact = false,
 Tooltip = "Настройка угла обзора",
 Callback = function(Value)
 UpdateFOV()
 end
})

task.spawn(function()
 while task.wait(0.1) do
 if Toggles.EnableFOV and Toggles.EnableFOV.Value then
 local camera = workspace.CurrentCamera
 if camera and camera.FieldOfView ~= Options.FOVValue.Value then
 camera.FieldOfView = Options.FOVValue.Value
 end
 end
 end
end)

-- ==============================================
do
-- Вкладка: VISUAL — 3RD PERSON (третье лицо)
-- ==============================================
-- Разрешаем вид от третьего лица: снимаем блокировку камеры и открываем
-- зум колесиком ��ыши. ��ока выключено — возвращаем исходные настройки камеры.
tpDefCamMode = LocalPlayer.CameraMode
tpDefMaxZoom = LocalPlayer.CameraMaxZoomDistance
tpDefMinZoom = LocalPlayer.CameraMinZoomDistance

-- NoName-style 3rd person: just CameraMode + CameraMaxZoomDistance
-- No CameraType=Custom, no CameraSubject, no RenderStepped loop, no body hooks
-- Default Roblox camera handles body visibility automatically

VisualCamera:AddToggle("EnableThirdPerson", {
 Text = "3rd Person",
 Default = false,
 Tooltip = "Вид от третьего лица: отдаляй камеру колесиком мыши",
 Callback = function(Value)
 LocalPlayer.CameraMaxZoomDistance = 1e9
 if Value then
 LocalPlayer.CameraMode = Enum.CameraMode.Classic
 else
 LocalPlayer.CameraMode = Enum.CameraMode.LockFirstPerson
 end
 end
})

VisualCamera:AddSlider("ThirdPersonDistance", {
 Text = "Дальность камеры",
 Default = 128,
 Min = 5,
 Max = 400,
 Rounding = 0,
 Compact = false,
 Tooltip = "Максимальное отдаление камеры колесиком мыши",
 Callback = function(Value)
 if Toggles.EnableThirdPerson and Toggles.EnableThirdPerson.Value then
 LocalPlayer.CameraMaxZoomDistance = Value
 end
 end
})

VisualCamera:AddToggle("Enable4x3", {
 Text = "Camera 4:3",
 Default = false,
 Tooltip = "Сплющить экран в 4:3 (через FOV)",
 Callback = function(Value)
 local cam = workspace.CurrentCamera
 if not cam then return end
 if Value then
 _G.Cam4x3DefaultFOV = cam.FieldOfView
 local vFOV = math.rad(cam.FieldOfView)
 local newFOV = math.deg(2 * math.atan(math.tan(vFOV / 2) * 0.75))
 cam.FieldOfView = newFOV
 _G.Cam4x3Conn = RunService.RenderStepped:Connect(function()
 if workspace.CurrentCamera and _G.Cam4x3DefaultFOV then
 local cur = math.rad(_G.Cam4x3DefaultFOV)
 local nf = math.deg(2 * math.atan(math.tan(cur / 2) * 0.75))
 workspace.CurrentCamera.FieldOfView = nf
 end
 end)
 else
 if _G.Cam4x3Conn then _G.Cam4x3Conn:Disconnect() _G.Cam4x3Conn = nil end
 if _G.Cam4x3DefaultFOV then
 if workspace.CurrentCamera then workspace.CurrentCamera.FieldOfView = _G.Cam4x3DefaultFOV end
 _G.Cam4x3DefaultFOV = nil
 end
 end
 end
})


-- Игра может насильно возвращать камеру �� п��рвое лицо — держим Classic
-- КАЖДЫЙ КАДР (иначе игра ус��евает вернуть первое лицо между кадрами).
-- NoName-style 3rd person: no RenderStepped loop needed, no body visibility hooks
-- Default Roblox camera system handles body visibility when CameraMode = Classic


-- ==============================================
-- Вкладка: VISUAL — SHADERS
-- ==============================================
local VisualShaders = Tabs.Visual:AddLeftGroupbox("Shaders")
local Lighting = game:GetService("Lighting")

-- Сохраняем оригинальные настройки освещения
originalLight = {
 Brightness = Lighting.Brightness,
 Ambient = Lighting.Ambient,
 OutdoorAmbient = Lighting.OutdoorAmbient,
 TimeOfDay = Lighting.TimeOfDay,
 ClockTime = Lighting.ClockTime,
 FogEnd = Lighting.FogEnd,
 FogStart = Lighting.FogStart,
 ExposureCompensation = Lighting.ExposureCompensation,
}

-- Темы шейдеров
local ShaderThemes = {
 ["Ночь"] = {
 Brightness = 2.0,
 Ambient = Color3.fromRGB(60, 70, 130),
 OutdoorAmbient = Color3.fromRGB(70, 80, 150),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.3,
 CC = { Brightness = 0, Contrast = 0.1, Saturation = -0.1, Tint = Color3.fromRGB(120, 140, 220) },
 Bloom = { Intensity = 0.6, Size = 32, Threshold = 0.7 },
 },
 ["Вечер"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(170, 110, 75),
 OutdoorAmbient = Color3.fromRGB(190, 120, 85),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.4,
 CC = { Brightness = 0, Contrast = 0.1, Saturation = 0.2, Tint = Color3.fromRGB(255, 200, 160) },
 Bloom = { Intensity = 0.6, Size = 32, Threshold = 0.7 },
 },
 ["Закат"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(210, 120, 75),
 OutdoorAmbient = Color3.fromRGB(230, 130, 85),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.4,
 CC = { Brightness = 0, Contrast = 0.15, Saturation = 0.35, Tint = Color3.fromRGB(255, 170, 120) },
 Bloom = { Intensity = 0.8, Size = 40, Threshold = 0.5 },
 },
 ["Туман"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(200, 200, 210),
 OutdoorAmbient = Color3.fromRGB(200, 200, 210),
 ClockTime = 12,
 FogEnd = 300,
 FogStart = 50,
 ExposureCompensation = 0,
 CC = { Brightness = 0.05, Contrast = -0.1, Saturation = -0.2, Tint = Color3.fromRGB(220, 220, 230) },
 Bloom = { Intensity = 0.3, Size = 48, Threshold = 0.9 },
 },
 ["Яркий"] = {
 Brightness = 3.0,
 Ambient = Color3.fromRGB(220, 220, 220),
 OutdoorAmbient = Color3.fromRGB(240, 240, 240),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.5,
 CC = { Brightness = 0.1, Contrast = 0.2, Saturation = 0.5, Tint = Color3.fromRGB(255, 255, 240) },
 Bloom = { Intensity = 1.0, Size = 32, Threshold = 0.4 },
 },
 ["Неон"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(80, 40, 120),
 OutdoorAmbient = Color3.fromRGB(90, 50, 140),
 ClockTime = 22,
 FogEnd = 1500,
 FogStart = 200,
 ExposureCompensation = 0.3,
 CC = { Brightness = 0, Contrast = 0.4, Saturation = 0.8, Tint = Color3.fromRGB(180, 100, 255) },
 Bloom = { Intensity = 1.3, Size = 44, Threshold = 0.35 },
 },
 ["Холодный"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(120, 140, 180),
 OutdoorAmbient = Color3.fromRGB(140, 160, 200),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.1,
 CC = { Brightness = 0, Contrast = 0.1, Saturation = 0.1, Tint = Color3.fromRGB(150, 180, 255) },
 Bloom = { Intensity = 0.4, Size = 24, Threshold = 0.8 },
 },
 ["Тёплый"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(180, 140, 100),
 OutdoorAmbient = Color3.fromRGB(200, 160, 120),
 ClockTime = 12,
 FogEnd = 100000,
 FogStart = 90000,
 ExposureCompensation = 0.2,
 CC = { Brightness = 0.02, Contrast = 0.1, Saturation = 0.3, Tint = Color3.fromRGB(255, 200, 150) },
 Bloom = { Intensity = 0.5, Size = 28, Threshold = 0.6 },
 },
 ["Реал��стичный"] = {
 Brightness = 3.5,
 Ambient = Color3.fromRGB(80, 80, 80),
 OutdoorAmbient = Color3.fromRGB(120, 120, 120),
 ClockTime = 14,
 FogEnd = 50000,
 FogStart = 5000,
 ExposureCompensation = 0.3,
 GlobalShadows = true,
 Use2022Materials = true,
 CC = { Brightness = 0.05, Contrast = 0.2, Saturation = 0.3, Tint = Color3.fromRGB(255, 250, 245) },
 Bloom = { Intensity = 0.3, Size = 24, Threshold = 0.8 },
 SunRays = { Intensity = 0.05, Spread = 0.1 },
 },
 ["Ультра-Реализм"] = {
 Brightness = 3.0,
 Ambient = Color3.fromRGB(50, 50, 50),
 OutdoorAmbient = Color3.fromRGB(130, 130, 130),
 ClockTime = 15.5,
 FogEnd = 8000,
 FogStart = 500,
 ExposureCompensation = 0.2,
 GlobalShadows = true,
 Use2022Materials = true,
 ForceMaterials = true,
 EnvDiffuse = 1.0,
 EnvSpecular = 1.0,
 CC = { Brightness = 0.05, Contrast = 0.25, Saturation = 0.3, Tint = Color3.fromRGB(255, 250, 240) },
 Bloom = { Intensity = 0.05, Size = 10, Threshold = 2.0 },
 SunRays = { Intensity = 0.3, Spread = 0.2 },
 DOF = { FocusDistance = 25, InFocusRadius = 50, NearIntensity = 0.1, FarIntensity = 0.3 },
 },
 -- 4 шейдера из unstable.txt (точные параметры)
 ["Twilight"] = {
 Brightness = 3.5,
 Ambient = Color3.fromRGB(59, 33, 27),
 OutdoorAmbient = Color3.fromRGB(34, 0, 49),
 ClockTime = 6.7,
 FogEnd = 1000,
 FogStart = 0,
 FogColor = Color3.fromRGB(94, 76, 106),
 ExposureCompensation = 0.24,
 ColorShift_Top = Color3.fromRGB(240, 127, 14),
 ColorShift_Bottom = Color3.fromRGB(11, 0, 20),
 CC = { Brightness = 0, Contrast = 0, Saturation = 0.05, Tint = Color3.fromRGB(255, 224, 219) },
 Bloom = { Intensity = 0.1, Size = 100, Threshold = 0 },
 SunRays = { Intensity = 0.05, Spread = 0.8 },
 CustomSkybox = { Bk = "rbxassetid://323494035", Dn = "rbxassetid://323494368", Ft = "rbxassetid://323494130", Lf = "rbxassetid://323494252", Rt = "rbxassetid://323494067", Up = "rbxassetid://323493360" },
 },
 ["Luminous"] = {
 Brightness = 2,
 Ambient = Color3.fromRGB(50, 50, 50),
 OutdoorAmbient = Color3.fromRGB(150, 150, 150),
 ClockTime = 10,
 FogEnd = 10000,
 FogStart = 0,
 FogColor = Color3.fromRGB(20, 20, 20),
 ExposureCompensation = 0.5,
 ColorShift_Top = Color3.fromRGB(250, 250, 250),
 ColorShift_Bottom = Color3.fromRGB(250, 250, 250),
 CC = { Brightness = 0, Contrast = 0, Saturation = 0, Tint = Color3.fromRGB(255, 255, 255) },
 Bloom = { Intensity = 0.1, Size = 100, Threshold = 0 },
 CustomSkybox = { Bk = "rbxassetid://323494035", Dn = "rbxassetid://323494368", Ft = "rbxassetid://323494130", Lf = "rbxassetid://323494252", Rt = "rbxassetid://323494067", Up = "rbxassetid://323493360" },
 },
 ["Sandstorm"] = {
 Brightness = 2.5,
 Ambient = Color3.fromRGB(80, 40, 10),
 OutdoorAmbient = Color3.fromRGB(100, 50, 10),
 ClockTime = 7,
 FogEnd = 1000,
 FogStart = 0,
 FogColor = Color3.fromRGB(100, 55, 20),
 ExposureCompensation = 0.5,
 ColorShift_Top = Color3.fromRGB(240, 127, 14),
 ColorShift_Bottom = Color3.fromRGB(240, 120, 20),
 CC = { Brightness = 0, Contrast = 0, Saturation = 0, Tint = Color3.fromRGB(255, 220, 180) },
 Bloom = { Intensity = 0.1, Size = 100, Threshold = 0 },
 CustomSkybox = { Bk = "rbxassetid://323494035", Dn = "rbxassetid://323494368", Ft = "rbxassetid://323494130", Lf = "rbxassetid://323494252", Rt = "rbxassetid://323494067", Up = "rbxassetid://323493360" },
 },
 ["Arctic"] = {
 Brightness = 2,
 Ambient = Color3.fromRGB(0, 50, 100),
 OutdoorAmbient = Color3.fromRGB(150, 170, 200),
 GlobalShadows = true,
 ExposureCompensation = 0,
 CC = { Brightness = 0, Contrast = 0.2, Saturation = -0.3, Tint = Color3.fromRGB(220, 240, 255) },
 Bloom = { Intensity = 0.8, Size = 24, Threshold = 0.6 },
 Atmosphere = { Density = 0.45, Color = Color3.fromRGB(180, 200, 255), Decay = Color3.fromRGB(255, 255, 255), Glare = 0.6 },
 },
}

-- Ссылк�� на пост-эффекты

local origMaterials = {}
local function applyRealMaterials()
 task.spawn(function()
 for _, v in ipairs(workspace:GetDescendants()) do
 if v:IsA("BasePart") then
 if not origMaterials[v] then
 origMaterials[v] = v.Material
 end
 if v.Material == Enum.Material.Plastic or v.Material == Enum.Material.SmoothPlastic then
 v.Material = Enum.Material.Concrete
 end
 end
 end
 end)
end
local function restoreMaterials()
 task.spawn(function()
 for k, v in pairs(origMaterials) do
 if k and k.Parent then
 k.Material = v
 end
 end
 origMaterials = {}
 end)
end

local ccEffect, bloomEffect, sunRaysEffect, dofEffect
currentShaderTheme = nil

existingEffects = {}
local function clearExistingEffects()
 for _, v in ipairs(Lighting:GetChildren()) do
 if v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("DepthOfFieldEffect") then
 table.insert(existingEffects, v)
 v.Enabled = false
 end
 end
end
local function restoreExistingEffects()
 for _, v in ipairs(existingEffects) do
 v.Enabled = true
 end
 existingEffects = {}
end

local function applyShaderTheme(themeName)
 local theme = ShaderThemes[themeName]
 if not theme then return end
 currentShaderTheme = themeName

 -- Очищаем существующие эф��екты игры (чтобы не накладывались поверх)
 clearExistingEffects()

 -- Освещение
 Lighting.Brightness = theme.Brightness
 Lighting.Ambient = theme.Ambient
 Lighting.OutdoorAmbient = theme.OutdoorAmbient
 if theme.ClockTime then
 Lighting.ClockTime = theme.ClockTime
 elseif theme.TimeOfDay then
 Lighting.TimeOfDay = theme.TimeOfDay
 end
 Lighting.FogEnd = theme.FogEnd
 if theme.FogStart then
 Lighting.FogStart = theme.FogStart
 end
 if theme.ExposureCompensation then
 Lighting.ExposureCompensation = theme.ExposureCompensation
 end
 
 if theme.GlobalShadows ~= nil then
 Lighting.GlobalShadows = theme.GlobalShadows
 else
 Lighting.GlobalShadows = false
 end

 if theme.Use2022Materials then
 pcall(function() game:GetService("MaterialService").Use2022Materials = true end)
 else
 pcall(function() game:GetService("MaterialService").Use2022Materials = false end)
 end

 if theme.EnvDiffuse then pcall(function() Lighting.EnvironmentDiffuseScale = theme.EnvDiffuse end) end
 if theme.EnvSpecular then pcall(function() Lighting.EnvironmentSpecularScale = theme.EnvSpecular end) end

 -- Отключаем Atmosphere игры (делает небо тёмным), если только тема не хочет её оставить
 local atm = Lighting:FindFirstChildOfClass("Atmosphere")
 if atm then atm.Enabled = false end

 -- ColorCorrection
 if theme.CC then
 if not ccEffect then
 ccEffect = Instance.new("ColorCorrectionEffect")
 ccEffect.Parent = Lighting
 end
 ccEffect.Brightness = theme.CC.Brightness
 ccEffect.Contrast = theme.CC.Contrast
 ccEffect.Saturation = theme.CC.Saturation
 ccEffect.TintColor = theme.CC.Tint
 ccEffect.Enabled = true
 end

 -- Bloom
 if theme.Bloom then
 if not bloomEffect then
 bloomEffect = Instance.new("BloomEffect")
 bloomEffect.Parent = Lighting
 end
 bloomEffect.Intensity = theme.Bloom.Intensity
 bloomEffect.Size = theme.Bloom.Size
 bloomEffect.Threshold = theme.Bloom.Threshold
 bloomEffect.Enabled = true
 else
 if bloomEffect then bloomEffect.Enabled = false end
 end

 -- SunRays
 if theme.SunRays then
 if not sunRaysEffect then
 sunRaysEffect = Instance.new("SunRaysEffect")
 sunRaysEffect.Parent = Lighting
 end
 sunRaysEffect.Intensity = theme.SunRays.Intensity
 sunRaysEffect.Spread = theme.SunRays.Spread
 sunRaysEffect.Enabled = true
 else
 if sunRaysEffect then sunRaysEffect.Enabled = false end
 end

 -- DOF
 if theme.DOF then
 if not dofEffect then
 dofEffect = Instance.new("DepthOfFieldEffect")
 dofEffect.Parent = Lighting
 end
 dofEffect.FocusDistance = theme.DOF.FocusDistance
 dofEffect.InFocusRadius = theme.DOF.InFocusRadius
 dofEffect.NearIntensity = theme.DOF.NearIntensity
 dofEffect.FarIntensity = theme.DOF.FarIntensity
 dofEffect.Enabled = true
 else
 if dofEffect then dofEffect.Enabled = false end
 end
 -- CustomSkybox (для Twilight/Luminous/Sandstorm)
 if theme.CustomSkybox then
  local sky = Lighting:FindFirstChildOfClass("Sky")
  if not sky then
   sky = Instance.new("Sky")
   sky.Parent = Lighting
  end
  sky.SkyboxBk = theme.CustomSkybox.Bk
  sky.SkyboxDn = theme.CustomSkybox.Dn
  sky.SkyboxFt = theme.CustomSkybox.Ft
  sky.SkyboxLf = theme.CustomSkybox.Lf
  sky.SkyboxRt = theme.CustomSkybox.Rt
  sky.SkyboxUp = theme.CustomSkybox.Up
  sky.SunAngularSize = 14
  sky.Parent = nil
  sky.Parent = Lighting
 else
  -- Если выключаем тему без CustomSkybox — не трогаем существующий Sky (пусть его шейдер управляет)
 end
 -- Atmosphere (для Arctic)
 if theme.Atmosphere then
  local atmos = Lighting:FindFirstChildOfClass("Atmosphere")
  if not atmos then
   atmos = Instance.new("Atmosphere")
   atmos.Parent = Lighting
  end
  atmos.Density = theme.Atmosphere.Density
  atmos.Color = theme.Atmosphere.Color
  atmos.Decay = theme.Atmosphere.Decay
  atmos.Glare = theme.Atmosphere.Glare
 end
 -- ColorShift_Top / ColorShift_Bottom (для Twilight/Luminous/Sandstorm)
 if theme.ColorShift_Top then Lighting.ColorShift_Top = theme.ColorShift_Top end
 if theme.ColorShift_Bottom then Lighting.ColorShift_Bottom = theme.ColorShift_Bottom end
 -- FogColor
 if theme.FogColor then Lighting.FogColor = theme.FogColor end
end
local function restoreLighting()
 Lighting.Brightness = originalLight.Brightness
 Lighting.Ambient = originalLight.Ambient
 Lighting.OutdoorAmbient = originalLight.OutdoorAmbient
 if originalLight.ClockTime then
 Lighting.ClockTime = originalLight.ClockTime
 else
 Lighting.TimeOfDay = originalLight.TimeOfDay
 end
 Lighting.FogEnd = originalLight.FogEnd
 Lighting.FogStart = originalLight.FogStart
 Lighting.ExposureCompensation = originalLight.ExposureCompensation
 Lighting.GlobalShadows = true
 local atm = Lighting:FindFirstChildOfClass("Atmosphere")
 if atm then atm.Enabled = true end
 if ccEffect then ccEffect:Destroy(); ccEffect = nil end
 if bloomEffect then bloomEffect:Destroy(); bloomEffect = nil end
 if sunRaysEffect then sunRaysEffect:Destroy(); sunRaysEffect = nil end
 if dofEffect then dofEffect:Destroy(); dofEffect = nil end
 pcall(function() game:GetService("MaterialService").Use2022Materials = false end)
 if _G.MaterialsForced then
 _G.MaterialsForced = false
 restoreMaterials()
 end
 restoreExistingEffects()
 currentShaderTheme = nil
end

VisualShaders:AddToggle("EnableShaders", {
 Text = "Shaders",
 Default = false,
 Tooltip = "Включить шейдеры (изменяет освещение игры)",
 Callback = function(Value)
 if Value then
 applyShaderTheme(Options.ShaderTheme.Value)
 else
 restoreLighting()
 end
 end
})

-- ПОСТОЯННОЕ ��РИМЕН��НИЕ ТЕМЫ (за��ит�� от ��ерезаписи игрой)
task.spawn(function()
 while task.wait(0.05) do
 if Toggles.EnableShaders and Toggles.EnableShaders.Value and currentShaderTheme then
 local theme = ShaderThemes[currentShaderTheme]
 if theme then
 -- Освещение
 Lighting.Brightness = theme.Brightness
 Lighting.Ambient = theme.Ambient
 Lighting.OutdoorAmbient = theme.OutdoorAmbient
 if theme.ClockTime then
 Lighting.ClockTime = theme.ClockTime
 end
 if theme.ExposureCompensation then
 Lighting.ExposureCompensation = theme.ExposureCompensation
 end
 if theme.GlobalShadows ~= nil then
 Lighting.GlobalShadows = theme.GlobalShadows
 else
 Lighting.GlobalShadows = false
 end
 Lighting.FogEnd = theme.FogEnd
 if theme.FogStart then Lighting.FogStart = theme.FogStart end
 
 if theme.Use2022Materials then
 pcall(function() game:GetService("MaterialService").Use2022Materials = true end)
 else
 pcall(function() game:GetService("MaterialService").Use2022Materials = false end)
 end

 if theme.ForceMaterials then
 if not _G.MaterialsForced then
 _G.MaterialsForced = true
 applyRealMaterials()
 end
 else
 if _G.MaterialsForced then
 _G.MaterialsForced = false
 restoreMaterials()
 end
 end

 -- Отключаем Atmosphere игры каждый кадр, только если не включен свой цвет неба
 local atm = Lighting:FindFirstChildOfClass("Atmosphere")
 if atm then 
 if Toggles.EnableCustomSky and Toggles.EnableCustomSky.Value then
 atm.Enabled = true
 else
 atm.Enabled = false 
 end
 end
 -- Отключаем чужие эффекты игры (каждый кадр!)
 for _, v in ipairs(Lighting:GetChildren()) do
 if v ~= ccEffect and v ~= bloomEffect and v ~= sunRaysEffect then
 if v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("DepthOfFieldEffect") then
 v.Enabled = false
 end
 end
 end
 -- Наши эффекты всегда включены
 if ccEffect and theme.CC then
 ccEffect.Enabled = true
 if not ccEffect.Parent then ccEffect.Parent = Lighting end
 end
 if bloomEffect and theme.Bloom then
 bloomEffect.Enabled = true
 if not bloomEffect.Parent then bloomEffect.Parent = Lighting end
 end
 if sunRaysEffect and theme.SunRays then
 sunRaysEffect.Enabled = true
 if not sunRaysEffect.Parent then sunRaysEffect.Parent = Lighting end
 end
 if dofEffect and theme.DOF then
 dofEffect.Enabled = true
 if not dofEffect.Parent then dofEffect.Parent = Lighting end
 end
 
 if theme.EnvDiffuse then pcall(function() Lighting.EnvironmentDiffuseScale = theme.EnvDiffuse end) end
 if theme.EnvSpecular then pcall(function() Lighting.EnvironmentSpecularScale = theme.EnvSpecular end) end
 end
 end
 end
end)

VisualShaders:AddDropdown("ShaderTheme", {
 Text = "Тема",
 Default = "Ночь",
 Values = {"Ночь", "Вечер", "Закат", "Туман", "Яркий", "Неон", "Холодный", "Тёплый", "Ультра-Реализм", "Twilight", "Luminous", "Sandstorm", "Arctic"},
 Multi = false,
 Tooltip = "Выбери тему освещения",
 Callback = function(Value)
 if Toggles.EnableShaders and Toggles.EnableShaders.Value then
 applyShaderTheme(Value)
 end
 end
})


-- ==============================================
-- Вкладка: VISUAL — ATMOSPHERE
-- ==============================================

do
VisualAtmo = Tabs.Visual:AddLeftGroupbox("Атмосфера")

-- Состояние оригинальных настроек, чтобы можно было вернуть всё назад
atmoOriginal = {
 saved = false,
 ClockTime = Lighting.ClockTime,
 TimeOfDay = Lighting.TimeOfDay,
 FogEnd = Lighting.FogEnd,
 FogStart = Lighting.FogStart,
 FogColor = Lighting.FogColor,
 OutdoorAmbient = Lighting.OutdoorAmbient,
 ColorShift_Top = Lighting.ColorShift_Top,
 ColorShift_Bottom = Lighting.ColorShift_Bottom,
}

-- Сохраняем оригинальные Sky/Atmosphere (только первый раз)
atmoOriginalSky = {}
atmoCustomObjects = {}

-- Модуль цвета неба:
-- NoName: Ambient/FOG color => Lighting.FogColor = Value
-- INIT: colorshifttop/colorshiftbottom => Lighting.ColorShift_Top / ColorShift_Bottom
-- Скайбоксы из (отдельные текстуры на каждую грань)
SkyboxList = {
 ["HD"]={Bk="http://www.roblox.com/asset/?id=16553658937",Dn="http://www.roblox.com/asset/?id=16553660713",Ft="http://www.roblox.com/asset/?id=16553662144",Lf="http://www.roblox.com/asset/?id=16553664042",Rt="http://www.roblox.com/asset/?id=16553665766",Up="http://www.roblox.com/asset/?id=16553667750"},
 ["Black Storm"]={Bk="rbxassetid://15502511288",Dn="rbxassetid://15502508460",Ft="rbxassetid://15502510289",Lf="rbxassetid://15502507918",Rt="rbxassetid://15502509398",Up="rbxassetid://15502511911"},
 ["Snow"]={Bk="http://www.roblox.com/asset/?id=155657655",Dn="http://www.roblox.com/asset/?id=155674246",Ft="http://www.roblox.com/asset/?id=155657609",Lf="http://www.roblox.com/asset/?id=155657671",Rt="http://www.roblox.com/asset/?id=155657619",Up="http://www.roblox.com/asset/?id=155674931"},
 ["Blue Space"]={Bk="rbxassetid://15536110634",Dn="rbxassetid://15536112543",Ft="rbxassetid://15536116141",Lf="rbxassetid://15536114370",Rt="rbxassetid://15536118762",Up="rbxassetid://15536117282"},
 ["Realistic"]={Bk="rbxassetid://653719502",Dn="rbxassetid://653718790",Ft="rbxassetid://653719067",Lf="rbxassetid://653719190",Rt="rbxassetid://653718931",Up="rbxassetid://653719321"},
 ["Sunset"]={Bk="rbxassetid://600830446",Dn="rbxassetid://600831635",Ft="rbxassetid://600832720",Lf="rbxassetid://600886090",Rt="rbxassetid://600833862",Up="rbxassetid://600835177"},
 ["Space"]={Bk="http://www.roblox.com/asset/?id=166509999",Dn="http://www.roblox.com/asset/?id=166510057",Ft="http://www.roblox.com/asset/?id=166510116",Lf="http://www.roblox.com/asset/?id=166510092",Rt="http://www.roblox.com/asset/?id=166510131",Up="http://www.roblox.com/asset/?id=166510114"},
 ["Pink"]={Bk="rbxassetid://12216109205",Dn="rbxassetid://12216109875",Ft="rbxassetid://12216109489",Lf="rbxassetid://12216110170",Rt="rbxassetid://12216110471",Up="rbxassetid://12216108877"},
 ["Galaxy"]={Bk="rbxassetid://15983968922",Dn="rbxassetid://15983966825",Ft="rbxassetid://15983965025",Lf="rbxassetid://15983967420",Rt="rbxassetid://15983966246",Up="rbxassetid://15983964246"},
 ["Roblox Default"]={Bk="rbxasset://textures/sky/sky512_bk.tex",Dn="rbxasset://textures/sky/sky512_dn.tex",Ft="rbxasset://textures/sky/sky512_ft.tex",Lf="rbxasset://textures/sky/sky512_lf.tex",Rt="rbxasset://textures/sky/sky512_rt.tex",Up="rbxasset://textures/sky/sky512_up.tex"},
}
currentSkybox = nil
skyboxOriginal = { saved = false, originalSky = nil, originalBk = nil, originalDn = nil, originalFt = nil, originalLf = nil, originalRt = nil, originalUp = nil }

function saveSkyboxOriginal()
 if skyboxOriginal.saved then return end
 local existing = Lighting:FindFirstChildOfClass("Sky")
 if existing then
 skyboxOriginal.originalSky = existing
 skyboxOriginal.originalBk = existing.SkyboxBk
 skyboxOriginal.originalDn = existing.SkyboxDn
 skyboxOriginal.originalFt = existing.SkyboxFt
 skyboxOriginal.originalLf = existing.SkyboxLf
 skyboxOriginal.originalRt = existing.SkyboxRt
 skyboxOriginal.originalUp = existing.SkyboxUp
 skyboxOriginal.saved = true
 else
 skyboxOriginal.saved = true
 end
end

function applySkybox(skyboxName)
 -- Не пересоздаём если тот же скайбокс уже активен
 if currentSkybox == skyboxName then return end
 saveSkyboxOriginal()
 local s = SkyboxList[skyboxName] or SkyboxList["HD"]
 -- Меняем текстуры у существующего Sky (надёжнее чем создание нового)
 local sky = Lighting:FindFirstChildOfClass("Sky")
 if not sky then
 sky = Instance.new("Sky")
 sky.Parent = Lighting
 end
 sky.SkyboxBk = s.Bk
 sky.SkyboxDn = s.Dn
 sky.SkyboxFt = s.Ft
 sky.SkyboxLf = s.Lf
 sky.SkyboxRt = s.Rt
 sky.SkyboxUp = s.Up
 sky.StarCount = 3000
 sky.SunAngularSize = 11
 sky.MoonAngularSize = 11
 -- Трюк: переподключаем для принудительного обновления рендера
 sky.Parent = nil
 sky.Parent = Lighting
 currentSkybox = skyboxName
end

function disableSkybox()
 if currentSkybox == nil then return end
 -- Восстанавливаем оригинальные текстуры
 if skyboxOriginal.originalSky then
 local sky = skyboxOriginal.originalSky
 sky.SkyboxBk = skyboxOriginal.originalBk or ""
 sky.SkyboxDn = skyboxOriginal.originalDn or ""
 sky.SkyboxFt = skyboxOriginal.originalFt or ""
 sky.SkyboxLf = skyboxOriginal.originalLf or ""
 sky.SkyboxRt = skyboxOriginal.originalRt or ""
 sky.SkyboxUp = skyboxOriginal.originalUp or ""
 if not sky.Parent then sky.Parent = Lighting end
 else
 -- Не было оригинального Sky — удаляем наш
 for _, s in ipairs(Lighting:GetChildren()) do
 if s:IsA("Sky") then s:Destroy() end
 end
 end
 currentSkybox = nil
end

function saveAtmoOriginal()
 if atmoOriginal.saved then return end
 atmoOriginal.ClockTime = Lighting.ClockTime
 atmoOriginal.TimeOfDay = Lighting.TimeOfDay
 atmoOriginal.FogEnd = Lighting.FogEnd
 atmoOriginal.FogStart = Lighting.FogStart
 atmoOriginal.FogColor = Lighting.FogColor
 atmoOriginal.OutdoorAmbient = Lighting.OutdoorAmbient
 atmoOriginal.ColorShift_Top = Lighting.ColorShift_Top
 atmoOriginal.saved = true

 -- Сохран��ем существующие Sky и Atmosphere
 atmoOriginalSky = {}
 for _, obj in ipairs(Lighting:GetChildren()) do
 if obj:IsA("Sky") or obj:IsA("Atmosphere") then
 atmoOriginalSky[obj] = {
 parent = obj.Parent,
 props = obj:IsA("Atmosphere") and {
 Density = obj.Density,
 Offset = obj.Offset,
 Glare = obj.Glare,
 Haze = obj.Haze,
 Color = obj.Color,
 Decay = obj.Decay,
 Enabled = obj.Enabled,
 } or nil
 }
 end
 end
end

function restoreAtmoOriginal()
 if not atmoOriginal.saved then return end

 -- Восстанавливаем базовые настройки Lighting
 Lighting.ClockTime = atmoOriginal.ClockTime
 Lighting.TimeOfDay = atmoOriginal.TimeOfDay
 Lighting.FogEnd = atmoOriginal.FogEnd
 Lighting.FogStart = atmoOriginal.FogStart
 Lighting.FogColor = atmoOriginal.FogColor
 Lighting.OutdoorAmbient = atmoOriginal.OutdoorAmbient
 Lighting.ColorShift_Top = atmoOriginal.ColorShift_Top
 if atmoOriginal.ColorShift_Bottom then Lighting.ColorShift_Bottom = atmoOriginal.ColorShift_Bottom end

 -- Удаляем наши кастомные объекты
 disableSkybox()
 for _, obj in ipairs(atmoCustomObjects) do
 pcall(function() obj:Destroy() end)
 end
 atmoCustomObjects = {}

 -- Восстанавливаем оригинальные Sky/Atmosphere
 for obj, data in pairs(atmoOriginalSky) do
 if obj and obj.Parent then
 if obj:IsA("Atmosphere") and data.props then
 obj.Density = data.props.Density
 obj.Offset = data.props.Offset
 obj.Glare = data.props.Glare
 obj.Haze = data.props.Haze
 obj.Color = data.props.Color
 obj.Decay = data.props.Decay
 obj.Enabled = data.props.Enabled
 end
 elseif obj and not obj.Parent then
 -- Sky убрали в небо — вернуть обратно в Lighting
 pcall(function() obj.Parent = data.parent end)
 end
 end
end

-- Лёгкая функция: только устанавливает ВКЛЮЧЁННЫЕ опции (без сброса/восстановления).
-- Вызывается каждый кадр из Heartbeat — не мерцает, т.к. не сбрасывает ничего.
local function maintainAtmosphere()
 -- Время суток
 if Toggles.EnableCustomTime and Toggles.EnableCustomTime.Value then
 local timeVal = Options.CustomTimeValue and Options.CustomTimeValue.Value or 12
 Lighting.ClockTime = timeVal
 end

 -- Туман
 if Toggles.EnableCustomFog and Toggles.EnableCustomFog.Value then
 local fogColor = Options.CustomFogColor and Options.CustomFogColor.Value or Color3.fromRGB(200, 200, 200)
 local fogDist = Options.CustomFogDistance and Options.CustomFogDistance.Value or 1000
 Lighting.FogColor = fogColor
 Lighting.FogEnd = fogDist
 Lighting.FogStart = 0
 end

 -- Skybox: НЕ пересоздаём каждый кадр — только в callbacks
 -- (applySkybox вызывается из toggle/dropdown callbacks)
end

-- Полная функция: вызывается из Callback'ов (вкл/выкл тумблеров, изменение цвета/ползунка).
-- Делает полную настройку: создаёт/удаляет Atmosphere, прячет/возвращает Sky и т.д.
function applyAtmosphere()
 saveAtmoOriginal()

 local shadersActive = Toggles.EnableShaders and Toggles.EnableShaders.Value

 -- 1) Время суток
 if Toggles.EnableCustomTime and Toggles.EnableCustomTime.Value then
 local timeVal = Options.CustomTimeValue and Options.CustomTimeValue.Value or 12
 Lighting.ClockTime = timeVal
 Lighting.TimeOfDay = tostring(timeVal) .. ":00:00"
 else
 if not shadersActive then
 Lighting.ClockTime = atmoOriginal.ClockTime
 Lighting.TimeOfDay = atmoOriginal.TimeOfDay
 end
 end

 -- 2) Туман
 if Toggles.EnableCustomFog and Toggles.EnableCustomFog.Value then
 fogColor = Options.CustomFogColor and Options.CustomFogColor.Value or Color3.fromRGB(200, 200, 200)
 fogDist = Options.CustomFogDistance and Options.CustomFogDistance.Value or 1000
 Lighting.FogColor = fogColor
 Lighting.FogEnd = fogDist
 Lighting.FogStart = 0
 else
 if not shadersActive then
 Lighting.FogEnd = atmoOriginal.FogEnd
 Lighting.FogStart = atmoOriginal.FogStart
 Lighting.FogColor = atmoOriginal.FogColor
 end
 end

 -- 3) Skybox (заменяем оригинальный Sky на кастомный)
 if Toggles.EnableCustomSky and Toggles.EnableCustomSky.Value then
 applySkybox(Options.SkyboxType and Options.SkyboxType.Value or "HD")
 else
 -- Возвращаем оригинальные Sky и Atmosphere
 disableSkybox()
 for obj, data in pairs(atmoOriginalSky) do
 if obj:IsA("Sky") and not obj.Parent then
 pcall(function() obj.Parent = data.parent end)
 end
 end
 end

 -- 4) Цвет скайбокса (ColorShift_Top / ColorShift_Bottom)
 if Toggles.EnableSkyboxColor and Toggles.EnableSkyboxColor.Value then
 local sc = Options.SkyboxColor and Options.SkyboxColor.Value or Color3.fromRGB(128, 128, 128)
 Lighting.ColorShift_Top = sc
 Lighting.ColorShift_Bottom = sc
 else
 Lighting.ColorShift_Top = atmoOriginal.ColorShift_Top or Color3.fromRGB(0, 0, 0)
 Lighting.ColorShift_Bottom = atmoOriginal.ColorShift_Bottom or Color3.fromRGB(0, 0, 0)
 end
end

VisualAtmo:AddToggle("EnableCustomTime", {
 Text = "Время суток",
 Default = false,
 Tooltip = "Заморозить и изменить время суток",
 Callback = function(Value)
 if Value then
 applyAtmosphere()
 else
 if not (Toggles.EnableShaders and Toggles.EnableShaders.Value) then
 Lighting.ClockTime = atmoOriginal.ClockTime
 Lighting.TimeOfDay = atmoOriginal.TimeOfDay
 end
 end
 end
})
VisualAtmo:AddSlider("CustomTimeValue", {
 Text = "Час",
 Default = 12,
 Min = 0,
 Max = 24,
 Rounding = 1,
 Compact = false,
 Callback = function(Value)
 if Toggles.EnableCustomTime and Toggles.EnableCustomTime.Value then
 applyAtmosphere()
 end
 end
})

VisualAtmo:AddToggle("EnableCustomSky", {
 Text = "Skybox",
 Default = false,
 Tooltip = "Заменяет небо на кастомный скайбокс",
 Callback = function(Value)
 if Value then
 applySkybox(Options.SkyboxType and Options.SkyboxType.Value or "HD")
 else
 disableSkybox()
 if not (Toggles.EnableShaders and Toggles.EnableShaders.Value) then
 restoreAtmoOriginal()
 end
 end
 end
})

VisualAtmo:AddDropdown("SkyboxType", {
 Text = "Список скайбоксов",
 Default = "HD",
 Values = {"HD", "Black Storm", "Snow", "Blue Space", "Realistic", "Sunset", "Space", "Pink", "Galaxy", "Roblox Default"},
 Multi = false,
 Tooltip = "Выбери скайбокс",
 Callback = function(Value)
 if Toggles.EnableCustomSky and Toggles.EnableCustomSky.Value then
 applySkybox(Value)
 end
 end
})

VisualAtmo:AddToggle("EnableSkyboxColor", {
 Text = "Цвет скайбокса",
 Default = false,
 Tooltip = "Изменить цвет неба на кастомный через цветовую палитру",
 Callback = function(Value)
 if Value then
 local sc = Options.SkyboxColor and Options.SkyboxColor.Value or Color3.fromRGB(128, 128, 128)
 Lighting.ColorShift_Top = sc
 Lighting.ColorShift_Bottom = sc
 else
 Lighting.ColorShift_Top = atmoOriginal.ColorShift_Top or Color3.fromRGB(0, 0, 0)
 Lighting.ColorShift_Bottom = atmoOriginal.ColorShift_Bottom or Color3.fromRGB(0, 0, 0)
 end
 end
})

VisualAtmo:AddLabel("Цвет неба"):AddColorPicker("SkyboxColor", {
 Default = Color3.fromRGB(128, 128, 128),
 Title = "Цвет скайбокса",
 Callback = function(Value)
 if Toggles.EnableSkyboxColor and Toggles.EnableSkyboxColor.Value then
 Lighting.ColorShift_Top = Value
 Lighting.ColorShift_Bottom = Value
 end
 end
})

VisualAtmo:AddToggle("EnableCustomFog", {
 Text = "Туман",
 Default = false,
 Tooltip = "Густой туман с настраиваемым цветом",
 Callback = function(Value)
 applyAtmosphere()
 if not Value and not (Toggles.EnableShaders and Toggles.EnableShaders.Value) then
 Lighting.FogEnd = atmoOriginal.FogEnd
 Lighting.FogStart = atmoOriginal.FogStart
 Lighting.FogColor = atmoOriginal.FogColor
 end
 end
}):AddColorPicker("CustomFogColor", {
 Default = Color3.fromRGB(200, 200, 200),
 Title = "Цвет тумана",
 Callback = function(Value)
 if Toggles.EnableCustomFog and Toggles.EnableCustomFog.Value then
 applyAtmosphere()
 end
 end
})
VisualAtmo:AddSlider("CustomFogDistance", {
 Text = "Дальность тумана",
 Default = 1000,
 Min = 50,
 Max = 10000,
 Rounding = 0,
 Compact = false,
 Callback = function(Value)
 if Toggles.EnableCustomFog and Toggles.EnableCustomFog.Value then
 applyAtmosphere()
 end
 end
})

-- Постоянное применение: ОДИН вызов maintainAtmosphere() за кадр.
-- maintainAtmosphere только устанавливает включённые опции — не сбрасывает ничего,
-- поэтому мерцания нет. Полную настройку (создание/удаление Atmosphere, в��зврат Sky)
-- делает applyAtmosphere() из Callback'ов при вкл/выкл тумблеров.
RunService.Heartbeat:Connect(function()
 local anyAtmo = (Toggles.EnableCustomTime and Toggles.EnableCustomTime.Value)
 or (Toggles.EnableCustomFog and Toggles.EnableCustomFog.Value)
 or (Toggles.EnableCustomSky and Toggles.EnableCustomSky.Value)
 if anyAtmo then
 maintainAtmosphere()
 end
end)

end
-- ==============================================
-- Вкладка: VISUAL — FULLBRIGHT
-- ==============================================
do
    local VisualBright = Tabs.Visual:AddLeftGroupbox("Освещение")
    
    VisualBright:AddToggle("EnableFullbright", {
        Text = "Fullbright",
        Default = false,
        Tooltip = "Всё видно в темноте — максимальная яркость",
        Callback = function(Value)
            if Value then
                -- Save originals if not saved
                if not originalLight._fbSaved then
                    originalLight._fbSaved = true
                    originalLight._fbBrightness = Lighting.Brightness
                    originalLight._fbAmbient = Lighting.Ambient
                    originalLight._fbOutdoorAmbient = Lighting.OutdoorAmbient
                    originalLight._fbClockTime = Lighting.ClockTime
                    originalLight._fbGlobalShadows = Lighting.GlobalShadows
                    originalLight._fbExposure = Lighting.ExposureCompensation
                end
                Lighting.Brightness = 3
                Lighting.Ambient = Color3.fromRGB(255, 255, 255)
                Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
                Lighting.ClockTime = 12
                Lighting.GlobalShadows = false
                Lighting.ExposureCompensation = 0.5
            else
                -- Restore
                if originalLight._fbSaved then
                    Lighting.Brightness = originalLight._fbBrightness
                    Lighting.Ambient = originalLight._fbAmbient
                    Lighting.OutdoorAmbient = originalLight._fbOutdoorAmbient
                    Lighting.ClockTime = originalLight._fbClockTime
                    Lighting.GlobalShadows = originalLight._fbGlobalShadows
                    Lighting.ExposureCompensation = originalLight._fbExposure
                end
            end
        end
    })
end

-- ==============================================
-- Вкладка: VISUAL — TEXTURES
-- ==============================================

do
VisualTex = Tabs.Visual:AddRightGroupbox("Текстуры карты")

VisualTex:AddToggle("EnableGeneralTint", {
 Text = "Общее",
 Default = false,
 Tooltip = "Красит всё глобальное освещение и атмосферу кар��ы",
})
VisualTex:AddLabel("Цвет общего освещения"):AddColorPicker("GeneralTintColor", {
 Default = Color3.fromRGB(150, 200, 255),
 Title = "Общий цвет",
})

origPartColors = {}
recoloredMats = {
 [Enum.Material.Grass] = Color3.fromRGB(75, 150, 75),
 [Enum.Material.Wood] = Color3.fromRGB(130, 100, 75),
 [Enum.Material.Concrete] = Color3.fromRGB(150, 150, 150),
 [Enum.Material.Plastic] = Color3.fromRGB(200, 200, 200),
 [Enum.Material.SmoothPlastic] = Color3.fromRGB(200, 200, 200),
}

function applyTextureColors()
 -- Safe check for Initialization
 if not Toggles or not Toggles.EnableTexColors then return end
 if not Toggles.EnableTexColors.Value then return end
 
 if Options.TexColorGrass then recoloredMats[Enum.Material.Grass] = Options.TexColorGrass.Value end
 if Options.TexColorWood then recoloredMats[Enum.Material.Wood] = Options.TexColorWood.Value end
 if Options.TexColorConcrete then recoloredMats[Enum.Material.Concrete] = Options.TexColorConcrete.Value end
 if Options.TexColorPlastic then
 recoloredMats[Enum.Material.Plastic] = Options.TexColorPlastic.Value
 recoloredMats[Enum.Material.SmoothPlastic] = Options.TexColorPlastic.Value
 end

 task.spawn(function()
 for _, part in ipairs(workspace:GetDescendants()) do
 if part:IsA("BasePart") then
 local tColor = recoloredMats[part.Material]
 if tColor then
 if not origPartColors[part] then
 origPartColors[part] = part.Color
 end
 part.Color = tColor
 end
 end
 end
 -- Красим террейн
 pcall(function()
 if Options.TexColorGrass then workspace.Terrain:SetMaterialColor(Enum.Material.Grass, Options.TexColorGrass.Value) end
 if Options.TexColorWood then workspace.Terrain:SetMaterialColor(Enum.Material.Wood, Options.TexColorWood.Value) end
 if Options.TexColorConcrete then workspace.Terrain:SetMaterialColor(Enum.Material.Concrete, Options.TexColorConcrete.Value) end
 if Options.TexColorPlastic then workspace.Terrain:SetMaterialColor(Enum.Material.Plastic, Options.TexColorPlastic.Value) end
 end)
 end)
end

VisualTex:AddToggle("EnableTexColors", {
 Text = "Изменить цвета текстур",
 Default = false,
 Tooltip = "Перекрашивает объекты на карте по их материалу",
 Callback = function(Value)
 if Value then
 applyTextureColors()
 else
 -- Возвращаем оригинальные цвета
 for part, color in pairs(origPartColors) do
 if part and part.Parent then
 part.Color = color
 end
 end
 origPartColors = {}
 -- Возвращаем цвета террейна
 pcall(function()
 workspace.Terrain:SetMaterialColor(Enum.Material.Grass, Color3.fromRGB(106, 127, 63))
 workspace.Terrain:SetMaterialColor(Enum.Material.Wood, Color3.fromRGB(212, 175, 55))
 workspace.Terrain:SetMaterialColor(Enum.Material.Concrete, Color3.fromRGB(127, 127, 127))
 workspace.Terrain:SetMaterialColor(Enum.Material.Plastic, Color3.fromRGB(200, 200, 200))
 end)
 end
 end
})

VisualTex:AddLabel("Цвет травы"):AddColorPicker("TexColorGrass", {
 Default = Color3.fromRGB(75, 150, 75),
 Title = "Цвет травы",
 Callback = applyTextureColors
})
VisualTex:AddLabel("Цвет дерева"):AddColorPicker("TexColorWood", {
 Default = Color3.fromRGB(130, 100, 75),
 Title = "Цвет дерева",
 Callback = applyTextureColors
})
VisualTex:AddLabel("Цвет камня/бетона"):AddColorPicker("TexColorConcrete", {
 Default = Color3.fromRGB(150, 150, 150),
 Title = "Цвет камня/бетона",
 Callback = applyTextureColors
})
VisualTex:AddLabel("Цвет пластика"):AddColorPicker("TexColorPlastic", {
 Default = Color3.fromRGB(200, 200, 200),
 Title = "Цвет пластика",
 Callback = applyTextureColors
})

-- Применяем эффект общего освещения (из вкладки Текстуры) каждый кадр
-- Внимание: время суток, туман и цвет неба теперь управляются в блоке Атмосфера выше.
origGeneralTint = {}

RunService.Heartbeat:Connect(function()
 if not Toggles then return end
 local lighting = game:GetService("Lighting")

 -- Общее (Глобальное осв��щение / Атмосфера из вкладки Текстуры)
 if Toggles.EnableGeneralTint and Toggles.EnableGeneralTint.Value then
 local cColor = Options.GeneralTintColor and Options.GeneralTintColor.Value or Color3.fromRGB(150, 200, 255)
 
 if not origGeneralTint["OutdoorAmbient"] then
 origGeneralTint["OutdoorAmbient"] = lighting.OutdoorAmbient
 origGeneralTint["ColorShift_Top"] = lighting.ColorShift_Top
 end
 lighting.OutdoorAmbient = cColor
 lighting.ColorShift_Top = cColor

 local atm = lighting:FindFirstChildOfClass("Atmosphere")
 if not atm then
 atm = Instance.new("Atmosphere")
 atm.Name = "CustomAtmosphere"
 atm.Density = 0.3
 atm.Offset = 0.25
 atm.Parent = lighting
 origGeneralTint[atm] = "Created"
 elseif not origGeneralTint[atm] then
 origGeneralTint[atm] = {Color = atm.Color, Decay = atm.Decay, Enabled = atm.Enabled}
 end
 
 atm.Enabled = true
 atm.Color = cColor
 atm.Decay = Color3.new(cColor.R * 0.5, cColor.G * 0.5, cColor.B * 0.5)
 else
 if origGeneralTint["OutdoorAmbient"] then
 lighting.OutdoorAmbient = origGeneralTint["OutdoorAmbient"]
 lighting.ColorShift_Top = origGeneralTint["ColorShift_Top"]
 origGeneralTint["OutdoorAmbient"] = nil
 end
 for obj, data in pairs(origGeneralTint) do
 if typeof(obj) == "Instance" and obj:IsA("Atmosphere") then
 if data == "Created" then
 obj:Destroy()
 else
 obj.Color = data.Color
 obj.Decay = data.Decay
 obj.Enabled = data.Enabled
 end
 end
 end
 origGeneralTint = {}
 end
end)


do
-- ==============================================
-- Вкладка: VISUAL — ESP
-- ==============================================
-- VISUAL — EFFECTS (BLUR)
local VisualEffects = Tabs.Visual:AddLeftGroupbox("Effects")

VisualEffects:AddToggle("EnableBlur", {
 Text = "Blur",
 Default = false,
 Tooltip = "Размытие экрана",
 Callback = function(Value)
 if Value then
 local blur = Instance.new("BlurEffect")
 blur.Name = "OblivionBlur"
 blur.Size = Options.BlurSize.Value
 blur.Parent = game:GetService("Lighting")
 else
 local ex = game:GetService("Lighting"):FindFirstChild("OblivionBlur")
 if ex then ex:Destroy() end
 end
 end
})

VisualEffects:AddSlider("BlurSize", {
 Text = "Blur Size",
 Default = 10,
 Min = 0,
 Max = 24,
 Rounding = 1,
 Increment = 0.5,
 Tooltip = "Сила размытия",
 Callback = function(Value)
 local blur = game:GetService("Lighting"):FindFirstChild("OblivionBlur")
 if blur then blur.Size = Value end
 end
})

-- ==============================================
-- Вкладка: VISUAL — 3RD PERSON EFFECTS (эффекты от 3 лица)
-- ==============================================
local Visual3rdEffects = Tabs.Visual:AddLeftGroupbox("3rd Person Effects")

Visual3rdEffects:AddToggle("Enable3rdPersonEffects", {
 Text = "Эффекты (3rd Person)",
 Default = false,
 Tooltip = "Визуальные эффекты на персонаже, видны только от 3 лица",
 Callback = function(Value)
 if Value then
 apply3rdPersonEffect(Options.EffectType and Options.EffectType.Value or "Fire")
 else
 remove3rdPersonEffect()
 end
 end
})

Visual3rdEffects:AddDropdown("EffectType", {
 Text = "Список эффектов",
 Default = "Fire",
 Values = {"Fire", "Sparkles", "Toxic", "Godly", "Super Sayien", "North Star", "Blue Lord", "Pink Aura", "Angel Wing", "Sweet Heart", "Ethereal Aura"},
 Multi = false,
 Tooltip = "Выбери эффект для персонажа",
 Callback = function(Value)
 if Toggles.Enable3rdPersonEffects and Toggles.Enable3rdPersonEffects.Value then
 apply3rdPersonEffect(Value)
 end
 end
})

Visual3rdEffects:AddLabel("Цвет эффектов"):AddColorPicker("EffectColor", {
 Default = Color3.fromRGB(255, 100, 0),
 Title = "Цвет эффекта",
 Callback = function(Value)
 if Toggles.Enable3rdPersonEffects and Toggles.Enable3rdPersonEffects.Value then
 apply3rdPersonEffect(Options.EffectType and Options.EffectType.Value or "Fire")
 end
 end
})

local current3rdEffectName = nil
local current3rdEffectParts = {}

local function clear3rdEffectParts()
 for _, part in ipairs(current3rdEffectParts) do
 pcall(function() part:Destroy() end)
 end
 current3rdEffectParts = {}
end

-- Aura models from (loaded via game:GetObjects)
AuraModels = {
 ["Godly"] = "rbxassetid://16699750981",
 ["Super Sayien"] = "rbxassetid://116109508364297",
 ["North Star"] = "rbxassetid://83945069652732",
 ["Blue Lord"] = "rbxassetid://10974316799",
 ["Pink Aura"] = "rbxassetid://115980859615239",
 ["Angel Wing"] = "rbxassetid://90022969696073",
 ["Sweet Heart"] = "rbxassetid://91724768175470",
 ["Ethereal Aura"] = "rbxassetid://97041568674250",
}
currentAuraModel = nil
lastLoadedAura = nil

function loadAuraModel(effectName)
 local id = AuraModels[effectName]
 if not id then return false end
 local ok, m = pcall(function() return game:GetObjects(id)[1] end)
 if ok and m then
 currentAuraModel = m
 return true
 end
 return false
end

function enableAuraModel(char)
 if not currentAuraModel then return end
 local tmp = currentAuraModel:Clone()
 local allowedTypes = {
 ParticleEmitter = true, Fire = true, Smoke = true, Sparkles = true,
 PointLight = true, SpotLight = true, SurfaceLight = true,
 Beam = true, Trail = true, BillboardGui = true,
 }
 local ec = (Options.EffectColor and Options.EffectColor.Value) or nil
 for _, o in ipairs(tmp:GetDescendants()) do
 if allowedTypes[o.ClassName] then
 local cl = o:Clone()
 local pn = o.Parent and o.Parent.Name
 local tgt = pn and char:FindFirstChild(pn) or char:FindFirstChildWhichIsA("BasePart")
 if tgt and not tgt:FindFirstChild(cl.Name) then
 cl.Parent = tgt
 -- Apply custom color to aura effects
 if ec then
 pcall(function()
 if cl:IsA("ParticleEmitter") then
 cl.Color = ColorSequence.new(ec)
 elseif cl:IsA("Fire") then
 cl.Color = ec
 cl.SecondaryColor = ec
 elseif cl:IsA("Smoke") then
 cl.Color = ec
 elseif cl:IsA("Sparkles") then
 cl.SparkleColor = ec
 elseif cl:IsA("PointLight") or cl:IsA("SpotLight") or cl:IsA("SurfaceLight") then
 cl.Color = ec
 elseif cl:IsA("Beam") then
 cl.Color = ColorSequence.new(ec)
 elseif cl:IsA("Trail") then
 cl.Color = ColorSequence.new(ec)
 end
 end)
 end
 table.insert(current3rdEffectParts, cl)
 end
 end
 end
 tmp:Destroy()
end

function apply3rdPersonEffect(effectName)
 remove3rdPersonEffect()
 local char = LocalPlayer.Character
 if not char then return end
 local hrp = char:FindFirstChild("HumanoidRootPart")
 local torso = char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
 if not hrp or not torso then return end

 current3rdEffectName = effectName

 if effectName == "Fire" then
 local ec = (Options.EffectColor and Options.EffectColor.Value) or Color3.fromRGB(255, 100, 0)
 local fire = Instance.new("Fire")
 fire.Name = "Oblivion3rdEffect"
 fire.Size = 5
 fire.Color = ec
 fire.SecondaryColor = ec
 fire.Heat = 5
 fire.Parent = torso
 table.insert(current3rdEffectParts, fire)
 local fire2 = Instance.new("Fire")
 fire2.Name = "Oblivion3rdEffect2"
 fire2.Size = 3
 fire2.Color = ec
 fire2.SecondaryColor = ec
 fire2.Heat = 3
 fire2.Parent = hrp
 table.insert(current3rdEffectParts, fire2)
 local pl = Instance.new("PointLight")
 pl.Name = "Oblivion3rdEffectLight"
 pl.Color = ec
 pl.Range = 12
 pl.Brightness = 3
 pl.Parent = hrp
 table.insert(current3rdEffectParts, pl)

 elseif effectName == "Sparkles" then
 local ec = (Options.EffectColor and Options.EffectColor.Value) or Color3.fromRGB(255, 255, 255)
 local sparkles = Instance.new("Sparkles")
 sparkles.Name = "Oblivion3rdEffect"
 sparkles.SparkleColor = ec
 sparkles.Parent = torso
 table.insert(current3rdEffectParts, sparkles)
 local att = Instance.new("Attachment")
 att.Name = "Oblivion3rdEffectAtt"
 att.Parent = hrp
 local pe = Instance.new("ParticleEmitter")
 pe.Texture = "rbxassetid://243660364"
 pe.Color = ColorSequence.new(ec)
 pe.Size = NumberSequence.new({NumberSequenceKeypoint.new(0, 0.5, 0), NumberSequenceKeypoint.new(1, 1, 0)})
 pe.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0, 0), NumberSequenceKeypoint.new(1, 1, 0)})
 pe.Lifetime = NumberRange.new(1)
 pe.Rate = 30
 pe.Speed = NumberRange.new(1, 3)
 pe.Parent = att
 table.insert(current3rdEffectParts, att)
 local pl = Instance.new("PointLight")
 pl.Name = "Oblivion3rdEffectLight"
 pl.Color = ec
 pl.Range = 10
 pl.Brightness = 2
 pl.Parent = hrp
 table.insert(current3rdEffectParts, pl)

 elseif effectName == "Toxic" then
 local ec = (Options.EffectColor and Options.EffectColor.Value) or Color3.fromRGB(50, 255, 0)
 local smoke = Instance.new("Smoke")
 smoke.Name = "Oblivion3rdEffect"
 smoke.Color = ec
 smoke.Size = 5
 smoke.RiseVelocity = 2
 smoke.Opacity = 0.6
 smoke.Parent = torso
 table.insert(current3rdEffectParts, smoke)
 local smoke2 = Instance.new("Smoke")
 smoke2.Name = "Oblivion3rdEffect2"
 smoke2.Color = ec
 smoke2.Size = 3
 smoke2.RiseVelocity = 1
 smoke2.Opacity = 0.5
 smoke2.Parent = hrp
 table.insert(current3rdEffectParts, smoke2)
 local pl = Instance.new("PointLight")
 pl.Name = "Oblivion3rdEffectLight"
 pl.Color = ec
 pl.Range = 12
 pl.Brightness = 3
 pl.Parent = hrp
 table.insert(current3rdEffectParts, pl)

 elseif AuraModels[effectName] then
 -- Aura model effect (from )
 if lastLoadedAura ~= effectName then
 if currentAuraModel then pcall(function() currentAuraModel:Destroy() end) end
 currentAuraModel = nil
 if loadAuraModel(effectName) then
 lastLoadedAura = effectName
 end
 end
 if currentAuraModel then
 enableAuraModel(char)
 end
 end
end

function remove3rdPersonEffect()
 clear3rdEffectParts()
 current3rdEffectName = nil
 local char = LocalPlayer.Character
 if char then
 for _, child in ipairs(char:GetDescendants()) do
 if child.Name == "Oblivion3rdEffect" or child.Name == "Oblivion3rdEffect2" or child.Name == "Oblivion3rdEffectAtt" or child.Name == "Oblivion3rdEffectLight" then
 pcall(function() child:Destroy() end)
 end
 end
 end
end

-- Обновляем эффект при смене персонажа
LocalPlayer.CharacterAdded:Connect(function()
 task.wait(1)
 if Toggles.Enable3rdPersonEffects and Toggles.Enable3rdPersonEffects.Value then
 apply3rdPersonEffect(Options.EffectType and Options.EffectType.Value or "Fire")
 end
end)

-- VISUAL — COIN
local VisualCoin = Tabs.Visual:AddLeftGroupbox("Coin")

VisualCoin:AddButton({
 Text = "Set Coins",
 Tooltip = "Введите число и нажмите Enter (как в venom X)",
 Func = function()
 local sg = Instance.new("ScreenGui")
 sg.Name = "CoinInputGui"
 sg.ResetOnSpawn = false
 sg.IgnoreGuiInset = true
 sg.DisplayOrder = 9999
 sg.Parent = (gethui and gethui()) or game:GetService("CoreGui")

 local overlay = Instance.new("Frame")
 overlay.Size = UDim2.new(1, 0, 1, 0)
 overlay.BackgroundColor3 = Color3.new(0, 0, 0)
 overlay.BackgroundTransparency = 0.5
 overlay.BorderSizePixel = 0
 overlay.Parent = sg

 local frame = Instance.new("Frame")
 frame.Size = UDim2.new(0, 340, 0, 180)
 frame.Position = UDim2.new(0.5, -170, 0.5, -90)
 frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
 frame.BorderSizePixel = 0
 frame.Parent = sg

 local fc = Instance.new("UICorner")
 fc.CornerRadius = UDim.new(0, 12)
 fc.Parent = frame

 local fs = Instance.new("UIStroke")
 fs.Color = Color3.fromRGB(100, 80, 200)
 fs.Thickness = 2
 fs.Transparency = 0.2
 fs.Parent = frame

 local fg = Instance.new("UIGradient")
 fg.Rotation = 90
 fg.Color = ColorSequence.new(Color3.fromRGB(35, 35, 50), Color3.fromRGB(20, 20, 30))
 fg.Parent = frame

 local titleBar = Instance.new("Frame")
 titleBar.Size = UDim2.new(1, 0, 0, 40)
 titleBar.BackgroundColor3 = Color3.fromRGB(100, 80, 200)
 titleBar.BorderSizePixel = 0
 titleBar.Parent = frame

 local tbc = Instance.new("UICorner")
 tbc.CornerRadius = UDim.new(0, 12)
 tbc.Parent = titleBar

 local tbg = Instance.new("UIGradient")
 tbg.Rotation = 90
 tbg.Color = ColorSequence.new(Color3.fromRGB(120, 100, 220), Color3.fromRGB(80, 60, 180))
 tbg.Parent = titleBar

 local title = Instance.new("TextLabel")
 title.Size = UDim2.new(1, -40, 1, 0)
 title.Position = UDim2.new(0, 15, 0, 0)
 title.BackgroundTransparency = 1
 title.Text = "������ Монеты"
 title.TextColor3 = Color3.fromRGB(255, 255, 255)
 title.Font = Enum.Font.SourceSansBold
 title.TextSize = 18
 title.TextXAlignment = Enum.TextXAlignment.Left
 title.Parent = titleBar

 local closeBtn = Instance.new("TextButton")
 closeBtn.Size = UDim2.new(0, 30, 0, 30)
 closeBtn.Position = UDim2.new(1, -35, 0, 5)
 closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
 closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
 closeBtn.Font = Enum.Font.SourceSansBold
 closeBtn.TextSize = 16
 closeBtn.Text = "X"
 closeBtn.BorderSizePixel = 0
 closeBtn.Parent = titleBar

 local cbc = Instance.new("UICorner")
 cbc.CornerRadius = UDim.new(0, 6)
 cbc.Parent = closeBtn

 closeBtn.MouseButton1Click:Connect(function() sg:Destroy() end)

 local hint = Instance.new("TextLabel")
 hint.Size = UDim2.new(1, -30, 0, 20)
 hint.Position = UDim2.new(0, 15, 0, 48)
 hint.BackgroundTransparency = 1
 hint.Text = "Введите количество монет:"
 hint.TextColor3 = Color3.fromRGB(180, 180, 200)
 hint.Font = Enum.Font.SourceSans
 hint.TextSize = 14
 hint.TextXAlignment = Enum.TextXAlignment.Left
 hint.Parent = frame

 local box = Instance.new("TextBox")
 box.Size = UDim2.new(1, -30, 0, 40)
 box.Position = UDim2.new(0, 15, 0, 75)
 box.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
 box.TextColor3 = Color3.fromRGB(255, 255, 255)
 box.Font = Enum.Font.SourceSansBold
 box.TextSize = 20
 box.PlaceholderText = "Например: 999999"
 box.PlaceholderColor3 = Color3.fromRGB(120, 120, 140)
 box.Text = ""
 box.ClearTextOnFocus = false
 box.BorderSizePixel = 0
 box.Parent = frame

 local bxc = Instance.new("UICorner")
 bxc.CornerRadius = UDim.new(0, 8)
 bxc.Parent = box

 local bxs = Instance.new("UIStroke")
 bxs.Color = Color3.fromRGB(100, 80, 200)
 bxs.Thickness = 1.5
 bxs.Transparency = 0.3
 bxs.Parent = box

 box:CaptureFocus()

 local btn = Instance.new("TextButton")
 btn.Size = UDim2.new(1, -30, 0, 38)
 btn.Position = UDim2.new(0, 15, 0, 128)
 btn.BackgroundColor3 = Color3.fromRGB(0, 170, 80)
 btn.TextColor3 = Color3.fromRGB(255, 255, 255)
 btn.Font = Enum.Font.SourceSansBold
 btn.TextSize = 16
 btn.Text = "✓ Применить"
 btn.BorderSizePixel = 0
 btn.Parent = frame

 local bnc = Instance.new("UICorner")
 bnc.CornerRadius = UDim.new(0, 8)
 bnc.Parent = btn

 local bng = Instance.new("UIGradient")
 bng.Rotation = 90
 bng.Color = ColorSequence.new(Color3.fromRGB(0, 200, 100), Color3.fromRGB(0, 140, 60))
 bng.Parent = btn

 local function applyCoins()
 local amt = tonumber(box.Text) or 0
 pcall(function() LocalPlayer.PlayerGui.MenuGui.TopRight.CoinsFrame.CoinsDisplay.Coins.Text = tostring(amt) end)
 Library:Notify("Монеты: " .. tostring(amt), 2)
 sg:Destroy()
 end

 btn.MouseButton1Click:Connect(applyCoins)
 box.FocusLost:Connect(function(ep) if ep then applyCoins() end end)
 overlay.MouseButton1Click:Connect(function() sg:Destroy() end)
 end
})

VisualESP = Tabs.Visual:AddRightGroupbox("ESP")

espColor = Color3.fromRGB(0, 255, 0)
guiParent = (gethui and gethui()) or game:GetService("CoreGui")
playerGui = LocalPlayer:FindFirstChild("PlayerGui") or LocalPlayer:WaitForChild("PlayerGui", 10)

-- === ESP UI ===
espObjects = {}

VisualESP:AddToggle("EnableESP", {
 Text = "ESP",
 Default = false,
 Tooltip = "Включить/��ыключить ESP",
}):AddColorPicker("ESPColor", {
 Default = Color3.fromRGB(0, 255, 0),
 Title = "Цвет ESP",
 Callback = function(Value)
 espColor = Value
 for _, obj in pairs(espObjects) do
 if obj.hl then obj.hl.FillColor = Value; obj.hl.OutlineColor = Value end
 if obj.box then obj.box.Color3 = Value end
 if obj.name then obj.name.TextColor3 = Value end
 if obj.dist then obj.dist.TextColor3 = Value end
 if obj.drawing then obj.drawing.Color = Value end
 end
 end,
})

VisualESP:AddDropdown("ESPMode", {
 Text = "Режим",
 Default = "Контур",
 Values = {"Контур", "Заливка", "Box"},
 Multi = false,
 Tooltip = "Режим отображения ESP (PCLD ESP теперь отдельным тумблером ниже)",
})

-- === PCLD ESP — как в NoName: BoxHandleAdornment на каждый PlayerCharacterLocationDetector ===
-- Реальные PCLD-объекты в workspace. Добавляет синий контур (как NoName) с возможностью смены цвета.
pcldColor = Color3.fromRGB(0, 255, 255)
pcldEspConns = {}
pcldEspAdorns = {}

local function pcldEspAdd(obj)
    if obj.Name ~= "PlayerCharacterLocationDetector" then return end
    if obj:FindFirstChild("PCLD_ESP") then return end
    local adorn = Instance.new("BoxHandleAdornment")
    adorn.Name = "PCLD_ESP"
    adorn.Size = obj.Size
    adorn.AlwaysOnTop = true
    adorn.ZIndex = 10
    adorn.Color3 = pcldColor
    adorn.Transparency = 0.25
    adorn.Adornee = obj
    adorn.Parent = obj
    pcldEspAdorns[obj] = adorn
end

local function pcldEspRemove(obj)
    if obj.Name ~= "PlayerCharacterLocationDetector" then return end
    local esp = obj:FindFirstChild("PCLD_ESP")
    if esp then esp:Destroy() end
    pcldEspAdorns[obj] = nil
end

local function pcldEspRemoveAll()
    for _, v in ipairs(workspace:GetChildren()) do
        pcldEspRemove(v)
    end
    -- fallback: пройтись по всем descendants на случай если PCLD внутри папок
    for _, v in ipairs(workspace:GetDescendants()) do
        if v.Name == "PlayerCharacterLocationDetector" and v:FindFirstChild("PCLD_ESP") then
            v.PCLD_ESP:Destroy()
        end
    end
    pcldEspAdorns = {}
end

local function pcldEspAddAll()
    for _, v in ipairs(workspace:GetDescendants()) do
        if v.Name == "PlayerCharacterLocationDetector" then
            pcldEspAdd(v)
        end
    end
end

VisualESP:AddToggle("EnablePCLDESP", {
    Text = "PCLD ESP",
    Default = false,
    Tooltip = "BoxHandleAdornment на каждом PlayerCharacterLocationDetector (как в NoName)",
    Callback = function(Value)
        if Value then
            -- Добавляем ко всем существующим
            pcldEspAddAll()
            -- Подписываемся на новые
            local conn = workspace.ChildAdded:Connect(function(obj)
                pcldEspAdd(obj)
                -- PCLD может быть вложен глубже — подписываемся и на descendants
                local sub = obj.DescendantAdded:Connect(function(d)
                    pcldEspAdd(d)
                end)
                table.insert(pcldEspConns, sub)
            end)
            table.insert(pcldEspConns, conn)
        else
            -- Отписываемся
            for _, c in ipairs(pcldEspConns) do
                pcall(function() c:Disconnect() end)
            end
            pcldEspConns = {}
            -- Удаляем все PCLD_ESP
            pcldEspRemoveAll()
        end
    end,
}):AddColorPicker("PCLDESPColor", {
    Default = Color3.fromRGB(0, 255, 255),
    Title = "Цвет PCLD ESP",
    Callback = function(Value)
        pcldColor = Value
        for obj, adorn in pairs(pcldEspAdorns) do
            if adorn and adorn.Parent then
                adorn.Color3 = Value
            end
        end
        -- fallback: пройтись по workspace и обновить все
        for _, v in ipairs(workspace:GetDescendants()) do
            if v.Name == "PlayerCharacterLocationDetector" and v:FindFirstChild("PCLD_ESP") then
                v.PCLD_ESP.Color3 = Value
            end
        end
    end,
})

-- ФОРАРД ДЕКЛАРАЦИЯ updateESP (ЛОКАЛЬНАЯ переменная!)
-- Без local updateESP Callback ESPElements берёт ГЛОБАЛЬНЫЙ updateESP = nil → crash!
-- updateESP опреде��яется ниже как updateESP = function(plr) и присв��ивается в эту local
updateESP = nil

VisualESP:AddDropdown("ESPElements", {
 Text = "Элементы",
 Default = {},
 Values = {"Ник", "Дистанция", "Иконка"},
 Multi = true,
 Tooltip = "Сними все галочки — останутся только боксы/контур. Или на��ми кнопку ниже.",
 Callback = function(Value)
 -- Ничего не делаем — главный цикл updateESP каждые 0.1с
 -- читает Options.ESPElements.Value напрямую и скрывает/показывает
 end,
})

-- Кнопка принудительного сброса всех элементов
VisualESP:AddButton({
 Text = "Сбросить элементы",
 Tooltip = "Снимает ВСЕ элементы ESP (Ник, Дистанция, Иконка)",
 Func = function()
 if Options.ESPElements and Options.ESPElements.SetValue then
 pcall(function() Options.ESPElements:SetValue({}) end)
 end
 -- Главный цикл updateESP сам увидит пустой Value и скроет все элементы
 end,
})

VisualESP:AddSlider("ESPSize", {
 Text = "Размер",
 Default = 12,
 Min = 6,
 Max = 30,
 Rounding = 0,
 Tooltip = "Размер текста и иконок",
})

-- === ESP RENDERING ===

function createESP(plr)
 if espObjects[plr] then return end
 local hl = Instance.new("Highlight")
 hl.FillColor = espColor
 hl.OutlineColor = espColor
 hl.FillTransparency = 1
 hl.OutlineTransparency = 1
 hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
 hl.Enabled = false
 hl.Parent = workspace

 -- БОЛЬШОЙ 3D ПРЯМОУГОЛЬ��ИК (SelectionBox) для режима PCLD
 -- ВАЖНО: SelectionBox не имеет .Visible! Скрываем через Adornee = nil
 local box = Instance.new("SelectionBox")
 box.Color3 = espColor
 box.Transparency = 0
 box.Adornee = nil -- nil = скрыт
 box.LineThickness = 0.05
 box.Parent = workspace

 -- Невидимый Part-якорь для SelectionBox (размер 4x6x2 studs)
 -- ВАЖН��: Part не имеет .Visible! Скрываем через Transparency = 1 (уже стоит)
 local boxPart = Instance.new("Part")
 boxPart.Name = "ESPBox"
 boxPart.Size = Vector3.new(4, 6, 2)
 boxPart.Transparency = 1
 boxPart.CanCollide = false
 boxPart.Anchored = true
 boxPart.Parent = workspace

 local bb = Instance.new("BillboardGui")
 bb.Size = UDim2.new(0, 200, 0, 80)
 bb.StudsOffset = Vector3.new(0, 3, 0)
 bb.AlwaysOnTop = true
 bb.LightInfluence = 0
 bb.MaxDistance = 600
 bb.Enabled = false
 bb.ResetOnSpawn = false -- НЕ удалять при респавне!
 bb.Parent = guiParent -- CoreGui — не чистится при респавне (вместо PlayerGui)

 local nLabel = Instance.new("TextLabel")
 nLabel.Size = UDim2.new(1, 0, 0, 20)
 nLabel.BackgroundTransparency = 1
 nLabel.Text = ""
 nLabel.TextColor3 = espColor
 nLabel.Font = Enum.Font.SourceSansBold
 nLabel.TextSize = 14
 nLabel.TextStrokeTransparency = 0.5
 nLabel.Parent = bb

 local dLabel = Instance.new("TextLabel")
 dLabel.Size = UDim2.new(1, 0, 0, 16)
 dLabel.Position = UDim2.new(0, 0, 0, 20)
 dLabel.BackgroundTransparency = 1
 dLabel.Text = ""
 dLabel.TextColor3 = espColor
 dLabel.Font = Enum.Font.SourceSans
 dLabel.TextSize = 12
 dLabel.TextStrokeTransparency = 0.5
 dLabel.Parent = bb

 local iLabel = Instance.new("ImageLabel")
 iLabel.Size = UDim2.new(0, 40, 0, 40)
 iLabel.Position = UDim2.new(0.5, -20, 0, 40)
 iLabel.BackgroundTransparency = 1
 iLabel.Image = ""
 iLabel.Parent = bb

 espObjects[plr] = {hl = hl, box = box, boxPart = boxPart, bb = bb, name = nLabel, dist = dLabel, icon = iLabel}
 
 -- Создаём Drawing для режима Box сразу
 local draw = Drawing.new("Square")
 draw.Thickness = 1.5
 draw.Filled = false
 draw.Color = espColor
 draw.Visible = false
 espObjects[plr].drawing = draw
end

function removeESP(plr)
 local obj = espObjects[plr]
 if not obj then return end
 if obj.hl then obj.hl:Destroy() end
 if obj.box then obj.box:Destroy() end
 if obj.boxPart then obj.boxPart:Destroy() end
 if obj.bb then obj.bb:Destroy() end
 if obj.drawing then obj.drawing:Remove() end
 espObjects[plr] = nil
end

-- hasElement и updateESP ниже

function hasElement(el, name)
 if type(el) ~= "table" then return false end
 if el[name] == true then return true end
 for _, v in pairs(el) do
 if v == name then return true end
 end
 return false
end

updateESP = function(plr)
 local obj = espObjects[plr]
 if not obj then return end

 -- Функция скрытия всех элементов
 local function hideElements()
 if obj.bb then
 obj.bb.Adornee = nil
 obj.bb.Enabled = false
 end
 if obj.name then obj.name.Visible = false end
 if obj.dist then obj.dist.Visible = false end
 if obj.icon then obj.icon.Visible = false end
 end

 -- Функция скрытия всех визуальных режимов
 local function hideVisuals()
 if obj.hl then obj.hl.Enabled = false; obj.hl.Adornee = nil end
 if obj.box then obj.box.Adornee = nil end
 if obj.drawing then obj.drawing.Visible = false end
 end

 if not (Toggles.EnableESP and Toggles.EnableESP.Value) then
 hideVisuals()
 hideElements()
 return
 end
 local char = plr.Character
 if not char then
 hideVisuals()
 hideElements()
 return
 end
 local root = char:FindFirstChild("HumanoidRootPart")
 local hum = char:FindFirstChild("Humanoid")
 local head = char:FindFirstChild("Head")
 if not root or not hum or hum.Health <= 0 then
 hideVisuals()
 hideElements()
 return
 end

 obj.hl.FillColor = espColor
 obj.hl.OutlineColor = espColor
 obj.name.TextColor3 = espColor
 obj.dist.TextColor3 = espColor
 obj.box.Color3 = espColor

 local mode = Options.ESPMode and Options.ESPMode.Value or "Конт��р"
 if mode == "Контур" then
 obj.hl.FillTransparency = 1
 obj.hl.OutlineTransparency = 0
 obj.hl.Adornee = char
 obj.hl.Enabled = true
 obj.box.Adornee = nil
 elseif mode == "Заливка" then
 obj.hl.FillTransparency = 0.5
 obj.hl.OutlineTransparency = 0.2
 obj.hl.Adornee = char
 obj.hl.Enabled = true
 obj.box.Adornee = nil
 elseif mode == "Box" then
 obj.hl.Enabled = false; obj.hl.Adornee = nil
 obj.box.Adornee = nil
 end
 if mode ~= "Box" and obj.drawing then
 obj.drawing.Visible = false
 end

 -- === ЭЛЕМЕНТЫ ESP ===
 -- Читаем Value напрямую из Options каждый раз (каждые 0.1с)
 -- НИКАКИХ флагов espElementsHidden — только реальное Value из дропдауна
 local elements = Options.ESPElements and Options.ESPElements.Value or {}
 local showN = hasElement(elements, "Ник")
 local showD = hasElement(elements, "Дистанция")
 local showI = hasElement(elements, "Иконка")
 local anyElement = showN or showD or showI

 -- Если нет выбранных элементов — скрываем billboard, остаются ��олько боксы/контур/PCLD
 if not anyElement then
 hideElements()
 return -- ВАЖНО: return здесь — элементы скрыты, но Контур/PCLD/Box остаются!
 end

 -- Есть выбранные элементы — показываем BillboardGui
 obj.bb.Adornee = head or root
 obj.bb.Enabled = true

 local ts = Options.ESPSize and Options.ESPSize.Value or 12
 obj.name.TextSize = ts
 obj.dist.TextSize = math.max(ts - 2, 6)
 obj.icon.Size = UDim2.new(0, ts * 3, 0, ts * 3)
 obj.icon.Position = UDim2.new(0.5, -ts * 1.5, 0, ts + 20)

 obj.name.Visible = showN
 obj.name.Text = showN and plr.DisplayName or ""

 if showD then
 local cam = workspace.CurrentCamera
 if cam and root then
 obj.dist.Text = string.format("%.0f studs", (root.Position - cam.CFrame.Position).Magnitude)
 obj.dist.Visible = true
 end
 else
 obj.dist.Visible = false
 end

 obj.icon.Visible = showI
 if showI and obj.icon.Image == "" then
 obj.icon.Image = string.format("rbxthumb://type=AvatarHeadShot&id=%d&w=150&h=150", plr.UserId)
 end
end

-- Важно: updateESP должна вызываться только через предобъявленную локальную переменную,
-- ��оторая видна из Callback ESPElements выше.

task.spawn(function()
 while task.wait(0.1) do
 if Toggles.EnableESP and Toggles.EnableESP.Value then
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer then
 local obj = espObjects[plr]
 if obj and (not obj.bb.Parent or not obj.hl.Parent) then
 removeESP(plr)
 obj = nil
 end
 if not obj then createESP(plr) end
 updateESP(plr)
 end
 end
 end
 end
end)

Players.PlayerRemoving:Connect(removeESP)

Toggles.EnableESP:OnChanged(function()
 if not Toggles.EnableESP.Value then
 for _, obj in pairs(espObjects) do
 obj.hl.Enabled = false
 obj.box.Adornee = nil
 obj.bb.Enabled = false
 if obj.drawing then obj.drawing.Visible = false end
 end
 end
end)

-- === BOX ESP: RenderStepped (каждый кадр, плавно) ===
RunService.RenderStepped:Connect(function()
 if not (Toggles.EnableESP and Toggles.EnableESP.Value) then return end
 if not (Options.ESPMode and Options.ESPMode.Value == "Box") then return end
 local cam = workspace.CurrentCamera
 if not cam then return end
 for plr, obj in pairs(espObjects) do
 if plr ~= LocalPlayer and obj.drawing then
 local char = plr.Character
 if not char then obj.drawing.Visible = false; continue end
 local root = char:FindFirstChild("HumanoidRootPart")
 local hum = char:FindFirstChild("Humanoid")
 local head = char:FindFirstChild("Head")
 if not root or not hum or hum.Health <= 0 then
 obj.drawing.Visible = false
 continue
 end
 -- Исправленный Box ESP: верх по Head, низ по RootPart-3, центр по среднему X
 local topWorld = (head and head.Position or root.Position) + Vector3.new(0, 0.5, 0)
 local botWorld = root.Position - Vector3.new(0, 3, 0)
 local screenTop, onTop = cam:WorldToViewportPoint(topWorld)
 local screenBot, onBot = cam:WorldToViewportPoint(botWorld)
 if onTop and onBot then
 local height = math.abs(screenTop.Y - screenBot.Y)
 local width = height * 0.5
 width = math.max(width, 15)
 height = math.max(height, 25)
 -- Центрируем бокс по среднему X из верхней и нижней точек
 local centerX = (screenTop.X + screenBot.X) / 2
 obj.drawing.Size = Vector2.new(width, height)
 obj.drawing.Position = Vector2.new(centerX - width / 2, screenTop.Y)
 obj.drawing.Color = espColor
 obj.drawing.Thickness = 1.5
 obj.drawing.Visible = true
 else
 obj.drawing.Visible = false
 end
 end
 end
end)

end



-- ==============================================
-- FUN: CUSTOM BLACK HOLE — кастомизация BlackHoleKick модели
-- Работает с workspace.BlackHoleKick (появляется когда кикаешь кого-то BlackHole'ом)
-- ==============================================
do
local CBHGroup = Tabs.Visual:AddRightGroupbox("Custom Black Hole")

local CBH = {}
CBH.RS = game:GetService("ReplicatedStorage")
CBH.WS = game:GetService("Workspace")
CBH.RunService = game:GetService("RunService")
CBH.Players = game:GetService("Players")
CBH.LocalPlayer = CBH.Players.LocalPlayer

-- ============ Состояние ============
CBH.settings = {
    colorMode = "Default",       -- Default / White / Red / Blue / Green / Gold / Cyan / Pink
    neonGlow = false,
    silent = false,             -- убирает звуки Drone + Scream
    rainbow = false,            -- режим переливания
    hideBillboard = false,      -- прячет BillboardGui с иконкой
    beamWidth0 = 1,
    beamWidth1 = 1,
    beamTransparency = 0,        -- 0..100 (%)
    billboardSize = 10,          -- studs
    realistic = false,          -- загружает Realistic модель (rbxassetid://16797584940)
}

CBH.rainbowConn = nil
CBH.watcherConn = nil

-- ============ Палитра цветов ============
CBH.palette = {
    ["Default"]   = { hole = Color3.fromRGB(0,   0,   0),   beam = Color3.fromRGB(170, 0, 255),  gui = Color3.fromRGB(150, 0, 255) },
    ["White"]     = { hole = Color3.fromRGB(255, 255, 255), beam = Color3.fromRGB(255, 255, 255),gui = Color3.fromRGB(255, 255, 255) },
    ["Red"]       = { hole = Color3.fromRGB(180,  0,   0),   beam = Color3.fromRGB(255, 50, 50),  gui = Color3.fromRGB(200, 30, 30) },
    ["Blue"]      = { hole = Color3.fromRGB(0,   50, 180),   beam = Color3.fromRGB(50, 120, 255), gui = Color3.fromRGB(30,  80, 220) },
    ["Green"]     = { hole = Color3.fromRGB(0,  120,  30),   beam = Color3.fromRGB(50, 255, 100), gui = Color3.fromRGB(20, 180, 60) },
    ["Gold"]      = { hole = Color3.fromRGB(180,140,   0),   beam = Color3.fromRGB(255, 220, 50), gui = Color3.fromRGB(220, 180, 20) },
    ["Cyan"]      = { hole = Color3.fromRGB(0,  180, 200),   beam = Color3.fromRGB(50, 230, 255), gui = Color3.fromRGB(0,  200, 230) },
    ["Pink"]      = { hole = Color3.fromRGB(220, 50, 180),   beam = Color3.fromRGB(255, 100, 220),gui = Color3.fromRGB(230, 60, 200) },
}

CBH.colorList = {}
for k, _ in pairs(CBH.palette) do table.insert(CBH.colorList, k) end
table.sort(CBH.colorList, function(a, b)
    if a == "Default" then return true end
    if b == "Default" then return false end
    return a < b
end)

-- ============ Применение визуала ============
CBH.applyToModel = function(model)
    if not model then return end
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    -- Материал
    hole.Material = CBH.settings.neonGlow and Enum.Material.Neon or Enum.Material.Plastic

    -- Цвет (только если не rainbow)
    if not CBH.settings.rainbow then
        local ct = CBH.palette[CBH.settings.colorMode]
        if ct then
            hole.Color = ct.hole
            local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Color = ColorSequence.new(ct.beam) end
            local gui = hole:FindFirstChild("BillboardGui")
            if gui then
                if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = ct.gui end
                if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = ct.gui end
            end
        end
    end

    -- Beam ширина и прозрачность
    local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
    if beam then
        beam.Width0 = CBH.settings.beamWidth0
        beam.Width1 = CBH.settings.beamWidth1
        beam.Transparency = NumberSequence.new(CBH.settings.beamTransparency / 100)
    end

    -- Billboard размер и видимость
    local gui = hole:FindFirstChild("BillboardGui")
    if gui then
        gui.Size = UDim2.new(CBH.settings.billboardSize, 0, CBH.settings.billboardSize, 0)
        gui.Enabled = not CBH.settings.hideBillboard
    end

    -- Звуки
    local drone  = hole:FindFirstChild("Drone")
    local scream = hole:FindFirstChild("Scream")
    if drone  then drone.Volume  = CBH.settings.silent and 0 or 1 end
    if scream then scream.Volume = CBH.settings.silent and 0 or 1 end
end

CBH.applyCurrent = function()
    CBH.applyToModel(CBH.WS:FindFirstChild("BlackHoleKick"))
end

-- ============ Realistic Black Hole ============
CBH.applyRealistic = function(model)
    if not model then return end
    local hole = model:FindFirstChild("Hole")
    if not hole then return end

    local success, realisticModel = pcall(function()
        return game:GetObjects("rbxassetid://16797584940")[1]
    end)
    if not success or not realisticModel then return end

    -- Копируем частицы/бимы/звуки/трейлы из realistic модели в Hole
    for _, obj in ipairs(realisticModel:GetDescendants()) do
        if obj:IsA("ParticleEmitter") or obj:IsA("Beam") or obj:IsA("Sound") or obj:IsA("Trail") then
            local clone = obj:Clone()
            clone.Parent = hole
        end
        if obj:IsA("BillboardGui") then
            local curGui = hole:FindFirstChild("BillboardGui")
            if curGui then
                local newGui = obj:Clone()
                newGui.Parent = hole
                if curGui:FindFirstChild("Large") and newGui:FindFirstChild("Large") then
                    curGui.Large.Image = newGui.Large.Image
                end
                if curGui:FindFirstChild("Small") and newGui:FindFirstChild("Small") then
                    curGui.Small.Image = newGui.Small.Image
                end
                newGui:Destroy()
            end
        end
    end

    realisticModel:Destroy()
end

-- ============ Watcher (применяет настройки к новым BlackHoleKick) ============
CBH.setupWatcher = function()
    if CBH.watcherConn then CBH.watcherConn:Disconnect() CBH.watcherConn = nil end
    CBH.watcherConn = CBH.WS.ChildAdded:Connect(function(child)
        if child.Name == "BlackHoleKick" then
            task.wait(0.1)
            CBH.applyToModel(child)
            if CBH.settings.realistic then
                CBH.applyRealistic(child)
            end
        end
    end)
end

CBH.setupWatcher()

-- ============ UI ============

-- Dropdown: Цвет
CBHGroup:AddDropdown("CBHColorMode", {
    Text = "Цвет дыры",
    Values = CBH.colorList,
    Default = "Default",
    Multi = false,
    Tooltip = "Цвет Hole + Beam + Billboard иконки",
    Callback = function(Value)
        CBH.settings.colorMode = Value
        -- При смене цвета выключаем rainbow
        if CBH.settings.rainbow then
            CBH.settings.rainbow = false
            if Toggles.CBHRainbow then Toggles.CBHRainbow:SetValue(false) end
            if CBH.rainbowConn then CBH.rainbowConn:Disconnect() CBH.rainbowConn = nil end
        end
        CBH.applyCurrent()
    end,
})

-- Toggle: Neon Glow
CBHGroup:AddToggle("CBHNeonGlow", {
    Text = "Neon Glow",
    Default = false,
    Tooltip = "Дыра светится (Material = Neon)",
    Callback = function(Value)
        CBH.settings.neonGlow = Value
        CBH.applyCurrent()
    end,
})

-- Toggle: Rainbow Mode
CBHGroup:AddToggle("CBHRainbow", {
    Text = "Rainbow Mode",
    Default = false,
    Tooltip = "Дыра переливается цветами радуги (по Hue)",
    Callback = function(Value)
        CBH.settings.rainbow = Value
        if CBH.rainbowConn then CBH.rainbowConn:Disconnect() CBH.rainbowConn = nil end
        if Value then
            local hue = 0
            CBH.rainbowConn = CBH.RunService.Heartbeat:Connect(function(dt)
                hue = (hue + dt * 0.3) % 1
                local c = Color3.fromHSV(hue, 1, 1)
                local model = CBH.WS:FindFirstChild("BlackHoleKick")
                if not model then return end
                local hole = model:FindFirstChild("Hole")
                if not hole then return end
                hole.Color = c
                local beam = hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
                if beam then beam.Color = ColorSequence.new(c) end
                local gui = hole:FindFirstChild("BillboardGui")
                if gui then
                    if gui:FindFirstChild("Large") then gui.Large.ImageColor3 = c end
                    if gui:FindFirstChild("Small") then gui.Small.ImageColor3 = c end
                end
            end)
        else
            CBH.applyCurrent()
        end
    end,
})

-- Toggle: Realistic Black Hole (загружает модель)
CBHGroup:AddToggle("CBHRealistic", {
    Text = "Realistic Black Hole",
    Default = false,
    Tooltip = "Загружает реалистичную модель с частицами/звуками/трейлами",
    Callback = function(Value)
        CBH.settings.realistic = Value
        if Value then
            local current = CBH.WS:FindFirstChild("BlackHoleKick")
            if current then
                CBH.applyRealistic(current)
            end
        end
    end,
})

CBHGroup:AddDivider()

-- Toggle: Hide Billboard
CBHGroup:AddToggle("CBHHideBillboard", {
    Text = "Hide Billboard",
    Default = false,
    Tooltip = "Прячет иконку-картинку (остаётся только Hole + Beam)",
    Callback = function(Value)
        CBH.settings.hideBillboard = Value
        CBH.applyCurrent()
    end,
})

-- Toggle: Silent Black Hole
CBHGroup:AddToggle("CBHSilent", {
    Text = "Silent (без звука)",
    Default = false,
    Tooltip = "Убирает звуки Drone + Scream",
    Callback = function(Value)
        CBH.settings.silent = Value
        CBH.applyCurrent()
    end,
})

CBHGroup:AddDivider()

-- Slider: Beam Width (Inner)
CBHGroup:AddSlider("CBHBeamW0", {
    Text = "Beam ширина (внутренняя)",
    Default = 1,
    Min = 0,
    Max = 20,
    Rounding = 1,
    Compact = false,
    Tooltip = "Толщина луча у центра Hole",
    Callback = function(Value)
        CBH.settings.beamWidth0 = Value
        local model = CBH.WS:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width0 = Value end
        end
    end,
})

-- Slider: Beam Width (Outer)
CBHGroup:AddSlider("CBHBeamW1", {
    Text = "Beam ширина (внешняя)",
    Default = 1,
    Min = 0,
    Max = 20,
    Rounding = 1,
    Compact = false,
    Tooltip = "Толщина луча у края Hole",
    Callback = function(Value)
        CBH.settings.beamWidth1 = Value
        local model = CBH.WS:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then beam.Width1 = Value end
        end
    end,
})

-- Slider: Beam Transparency
CBHGroup:AddSlider("CBHBeamTransparency", {
    Text = "Beam прозрачность (%)",
    Default = 0,
    Min = 0,
    Max = 100,
    Rounding = 0,
    Compact = false,
    Tooltip = "0 = полностью непрозрачный, 100 = невидимый",
    Callback = function(Value)
        CBH.settings.beamTransparency = Value
        local model = CBH.WS:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local beam = hole and hole:FindFirstChild("Attachment") and hole.Attachment:FindFirstChild("Beam")
            if beam then
                beam.Transparency = NumberSequence.new(Value / 100)
            end
        end
    end,
})

-- Slider: Billboard Size
CBHGroup:AddSlider("CBHBillboardSize", {
    Text = "Billboard размер",
    Default = 10,
    Min = 2,
    Max = 40,
    Rounding = 0,
    Compact = false,
    Tooltip = "Размер иконки-картинки над Hole (studs)",
    Callback = function(Value)
        CBH.settings.billboardSize = Value
        local model = CBH.WS:FindFirstChild("BlackHoleKick")
        if model then
            local hole = model:FindFirstChild("Hole")
            local gui  = hole and hole:FindFirstChild("BillboardGui")
            if gui then gui.Size = UDim2.new(Value, 0, Value, 0) end
        end
    end,
})

-- Кнопка: Применить сейчас
CBHGroup:AddButton({
    Text = "Применить к текущей дыре",
    Tooltip = "Применяет все настройки к workspace.BlackHoleKick если она существует",
    Func = function()
        CBH.applyCurrent()
        Library:Notify("Custom Black Hole: настройки применены", 3)
    end,
})

-- Кнопка: Сброс
CBHGroup:AddButton({
    Text = "Сброс к Default",
    Tooltip = "Сбрасывает все настройки к стандартным",
    Func = function()
        CBH.settings.colorMode = "Default"
        CBH.settings.neonGlow = false
        CBH.settings.silent = false
        CBH.settings.hideBillboard = false
        CBH.settings.beamWidth0 = 1
        CBH.settings.beamWidth1 = 1
        CBH.settings.beamTransparency = 0
        CBH.settings.billboardSize = 10

        -- Выключаем rainbow
        if CBH.settings.rainbow then
            CBH.settings.rainbow = false
            if Toggles.CBHRainbow then Toggles.CBHRainbow:SetValue(false) end
            if CBH.rainbowConn then CBH.rainbowConn:Disconnect() CBH.rainbowConn = nil end
        end

        -- Сбрасываем UI
        if Options.CBHColorMode then Options.CBHColorMode:SetValue("Default") end
        if Toggles.CBHNeonGlow then Toggles.CBHNeonGlow:SetValue(false) end
        if Toggles.CBHHideBillboard then Toggles.CBHHideBillboard:SetValue(false) end
        if Toggles.CBHSilent then Toggles.CBHSilent:SetValue(false) end
        if Options.CBHBeamW0 then Options.CBHBeamW0:SetValue(1) end
        if Options.CBHBeamW1 then Options.CBHBeamW1:SetValue(1) end
        if Options.CBHBeamTransparency then Options.CBHBeamTransparency:SetValue(0) end
        if Options.CBHBillboardSize then Options.CBHBillboardSize:SetValue(10) end

        CBH.applyCurrent()
        Library:Notify("Custom Black Hole: сброс к Default", 3)
    end,
})
end


end
-- ==============================================
-- Вкладка: VISUAL — WEATHER
-- ==============================================
local VisualWeather = Tabs.Visual:AddLeftGroupbox("Погода")

VisualWeather:AddToggle("EnableWeather", {
 Text = "Погода",
 Default = false,
 Tooltip = "Включить красивые погодные эффекты",
})

VisualWeather:AddDropdown("WeatherType", {
 Text = "Тип погоды",
 Default = "Снег",
 Values = {"Снег", "Дождь", "Радуга", "Гроза с дождем"},
 Multi = false,
 Tooltip = "Выбери ��ффект",
})

VisualWeather:AddSlider("WeatherAmount", {
 Text = "Количество",
 Default = 1,
 Min = 0.2,
 Max = 3,
 Rounding = 1,
 Compact = false,
 Tooltip = "Сила снега, дождя �� грозы",
})

VisualWeather:AddSlider("WeatherSpeed", {
 Text = "Скорость",
 Default = 1,
 Min = 0.2,
 Max = 3,
 Rounding = 1,
 Compact = false,
 Tooltip = "Скорость падения снега, дождя и грозы",
})

VisualWeather:AddLabel("Цвет всего"):AddColorPicker("WeatherColor", {
 Default = Color3.fromRGB(255, 255, 255),
 Title = "Цвет всей погод��",
})

VisualWeather:AddButton({
 Text = "Сброс цвета",
 Tooltip = "Вернуть обычные цв��та снега, дождя, радуги и грозы",
 Func = function()
 if Options.WeatherColor and Options.WeatherColor.SetValue then
 pcall(function() Options.WeatherColor:SetValue(Color3.fromRGB(255, 255, 255)) end)
 end
 local rb = workspace:FindFirstChild("RainbowFixed")
 if rb then rb:Destroy() end
 end,
})

task.spawn(function()
 local weatherPart = nil
 local weatherEmitter = nil
 local weatherLight = nil
 local weatherObjects = {}
 local weatherDrops = {}
 local weatherAnchorPos = nil
 local lastDropSpawn = 0
 local lastLightning = 0
 local lastRainbowColorKey = nil

 local function addWeatherObject(obj)
 weatherObjects[#weatherObjects + 1] = obj
 return obj
 end

 local function clearDrops()
 for i = #weatherDrops, 1, -1 do
 pcall(function() weatherDrops[i].part:Destroy() end)
 weatherDrops[i] = nil
 end
 end

 local function clearWeatherVisuals(keepPart)
 for _, obj in ipairs(weatherObjects) do
 pcall(function() obj:Destroy() end)
 end
 weatherObjects = {}
 clearDrops()
 if weatherEmitter then weatherEmitter.Enabled = false end
 if weatherLight then weatherLight.Enabled = false end
 if not keepPart and weatherPart then
 weatherPart:Destroy()
 weatherPart = nil
 weatherEmitter = nil
 weatherLight = nil
 end
 end

 local function getHRP()
 local char = LocalPlayer.Character
 return char and char:FindFirstChild("HumanoidRootPart")
 end

 local function getWeatherAmount()
 return math.clamp((Options.WeatherAmount and Options.WeatherAmount.Value) or 1, 0.2, 3)
 end

 local function getWeatherSpeed()
 return math.clamp((Options.WeatherSpeed and Options.WeatherSpeed.Value) or 1, 0.2, 3)
 end

 local function getWeatherColor()
 return (Options.WeatherColor and Options.WeatherColor.Value) or Color3.fromRGB(255, 255, 255)
 end

 local function isCustomWeatherColor()
 local c = getWeatherColor()
 return not (c.R > 0.98 and c.G > 0.98 and c.B > 0.98)
 end

 local function weatherColor(defaultColor)
 if isCustomWeatherColor() then
 return getWeatherColor()
 end
 return defaultColor
 end

 local function getGroundY(pos, fallbackY)
 local params = RaycastParams.new()
 params.FilterType = Enum.RaycastFilterType.Blacklist
 params.FilterDescendantsInstances = {LocalPlayer.Character, weatherPart}
 local result = workspace:Raycast(pos + Vector3.new(0, 35, 0), Vector3.new(0, -500, 0), params)
 if result then
 return result.Position.Y + 0.35
 end
 return fallbackY or (pos.Y - 90)
 end

 local function makePart(name, parent, size, cframe, color, transparency)
 local p = Instance.new("Part")
 p.Name = name
 p.Anchored = true
 p.CanCollide = false
 p.CanQuery = false
 p.CanTouch = false
 p.Material = Enum.Material.Neon
 p.Color = color
 p.Transparency = transparency or 0
 p.Size = size
 p.CFrame = cframe
 p.Parent = parent
 return addWeatherObject(p)
 end

 local function spawnDrop(kind, basePos, speedMul)
 speedMul = speedMul or getWeatherSpeed()
 local maxDrops = kind == "snow" and math.floor(420 * getWeatherAmount()) or math.floor(260 * getWeatherAmount())
 if #weatherDrops >= maxDrops then return end
 local p = Instance.new("Part")
 p.Anchored = true
 p.CanCollide = false
 p.CanQuery = false
 p.CanTouch = false
 p.Material = Enum.Material.Neon
 if kind == "snow" then
 p.Name = "WeatherSnowflake"
 p.Shape = Enum.PartType.Ball
 p.Color = weatherColor(Color3.fromRGB(255, 255, 255))
 p.Transparency = 0.03
 local sz = math.random(7, 14) / 10
 local spawnPos = basePos + Vector3.new(math.random(-190,190), math.random(55,95), math.random(-190,190))
 p.Size = Vector3.new(sz, sz, sz)
 p.CFrame = CFrame.new(spawnPos)
 weatherDrops[#weatherDrops + 1] = {
 part = p,
 vel = Vector3.new(math.random(-4,4)/10, -math.random(80,140)/10 * speedMul, math.random(-4,4)/10),
 life = 16,
 groundLife = 1.8,
 groundY = getGroundY(spawnPos, basePos.Y - 4),
 kind = kind,
 phase = math.random()*10,
 landed = false,
 }
 else
 p.Name = "WeatherRainDrop"
 p.Color = weatherColor(Color3.fromRGB(120, 175, 255))
 p.Transparency = 0.02
 p.Size = Vector3.new(0.08, math.random(38, 62) / 10, 0.08)
 p.CFrame = CFrame.new(basePos + Vector3.new(math.random(-210,210), math.random(70,115), math.random(-210,210)))
 weatherDrops[#weatherDrops + 1] = {part = p, vel = Vector3.new(math.random(-8,8)/10, -math.random(120,170) * speedMul, math.random(-8,8)/10), life = 1.8 / speedMul, kind = kind, phase = 0}
 end
 p.Parent = workspace
 end

 local function updateDrops(dt, basePos, kind)
 for i = #weatherDrops, 1, -1 do
 local d = weatherDrops[i]
 if not d.part or not d.part.Parent or d.kind ~= kind then
 if d.part then pcall(function() d.part:Destroy() end) end
 table.remove(weatherDrops, i)
 else
 d.life = d.life - dt
 if kind == "snow" then
 if d.landed then
 d.groundLife = d.groundLife - dt
 d.part.Transparency = math.clamp(1 - (d.groundLife / 1.8), 0.03, 1)
 if d.groundLife <= 0 then
 pcall(function() d.part:Destroy() end)
 table.remove(weatherDrops, i)
 end
 else
 local sway = Vector3.new(math.sin(tick()*1.1 + d.phase) * 0.45, 0, math.cos(tick()*1.0 + d.phase) * 0.45)
 d.part.CFrame = d.part.CFrame * CFrame.Angles(0, math.rad(18*dt), math.rad(12*dt))
 d.part.CFrame = d.part.CFrame + ((d.vel + sway) * dt)
 if d.part.Position.Y <= d.groundY then
 d.landed = true
 d.part.CFrame = CFrame.new(d.part.Position.X, d.groundY, d.part.Position.Z)
 d.part.Transparency = 0.1
 elseif d.life <= 0 then
 pcall(function() d.part:Destroy() end)
 table.remove(weatherDrops, i)
 end
 end
 else
 d.part.CFrame = d.part.CFrame + (d.vel * dt)
 local pos = d.part.Position
 if d.life <= 0 or pos.Y < basePos.Y - 15 or (Vector3.new(pos.X, basePos.Y, pos.Z) - Vector3.new(basePos.X, basePos.Y, basePos.Z)).Magnitude > 300 then
 pcall(function() d.part:Destroy() end)
 table.remove(weatherDrops, i)
 end
 end
 end
 end
 end

 function makeLightning(origin)
 local boltColor = weatherColor(Color3.fromRGB(215, 235, 255))
 local lastPos = origin + Vector3.new(math.random(-30,30), 380, math.random(-30,30))
 local segments = math.random(8, 12)
 for i = 1, segments do
 local nextPos = origin + Vector3.new(math.random(-70, 70), 380 - (i * (380 / segments)), math.random(-70, 70))
 local mid = (lastPos + nextPos) / 2
 local len = (lastPos - nextPos).Magnitude
 local part = makePart("WeatherLightning", workspace, Vector3.new(7, 7, len + 2), CFrame.lookAt(mid, nextPos), boltColor, 0)
 task.delay(0.22, function() if part then part:Destroy() end end)
 lastPos = nextPos
 end
 -- Дополнительная короткая ветка молнии без рекурсии, чтобы не грузить скрипт бесконечными вызовами.
 if math.random(1, 2) == 1 then
 local branchStart = origin + Vector3.new(math.random(-80,80), 260, math.random(-80,80))
 local branchEnd = branchStart + Vector3.new(math.random(-160,160), -math.random(80,150), math.random(-160,160))
 local mid = (branchStart + branchEnd) / 2
 local len = (branchStart - branchEnd).Magnitude
 local branch = makePart("WeatherLightningBranch", workspace, Vector3.new(5, 5, len + 2), CFrame.lookAt(mid, branchEnd), boltColor, 0)
 task.delay(0.22, function() if branch then branch:Destroy() end end)
 end
 end

 function makeRainbowSegment(parent, p1, p2, color, thick)
 local mid = (p1 + p2) / 2
 local len = (p1 - p2).Magnitude
 local seg = Instance.new("Part")
 seg.Name = "RainbowSegment"
 seg.Anchored = true
 seg.CanCollide = false
 seg.CanQuery = false
 seg.CanTouch = false
 seg.Material = Enum.Material.Neon
 seg.Color = color
 seg.Transparency = 0.08
 seg.Size = Vector3.new(thick, thick, len + 3)
 seg.CFrame = CFrame.lookAt(mid, p2)
 seg.Parent = parent
 end

 function ensureRainbow(hrp)
 local c = getWeatherColor()
 local colorKey = tostring(math.floor(c.R*255))..":"..tostring(math.floor(c.G*255))..":"..tostring(math.floor(c.B*255))
 local oldRainbow = workspace:FindFirstChild("RainbowFixed")
 if oldRainbow and lastRainbowColorKey == colorKey then return end
 if oldRainbow then oldRainbow:Destroy() end
 lastRainbowColorKey = colorKey
 clearWeatherVisuals(true)
 local model = Instance.new("Model")
 model.Name = "RainbowFixed"
 model.Parent = workspace
 addWeatherObject(model)

 -- Огромная радуга на всю длину за картой: стоит в мире и не смотрит/не поворачи��ается за игроком.
 weatherAnchorPos = weatherAnchorPos or (hrp.Position + Vector3.new(0, 0, -1150))
 local center = weatherAnchorPos + Vector3.new(0, 80, 0)
 local colors = {
 weatherColor(Color3.fromRGB(255, 0, 0)), weatherColor(Color3.fromRGB(255, 120, 0)), weatherColor(Color3.fromRGB(255, 255, 0)),
 weatherColor(Color3.fromRGB(0, 255, 0)), weatherColor(Color3.fromRGB(0, 120, 255)), weatherColor(Color3.fromRGB(100, 0, 200)), weatherColor(Color3.fromRGB(180, 0, 255)),
 }
 local radiusBase = 620
 local steps = 120
 for band, color in ipairs(colors) do
 local radius = radiusBase - (band * 16)
 local last = nil
 for i = 0, steps do
 local a = math.rad(180 - (i * 180 / steps))
 local pos = center + Vector3.new(math.cos(a) * radius, math.sin(a) * radius, 0)
 if last then makeRainbowSegment(model, last, pos, color, 14) end
 last = pos
 end
 end
 end

 function ensureWeatherPart()
 if weatherPart then return end
 weatherPart = Instance.new("Part")
 weatherPart.Name = "OblivionWeatherPart"
 weatherPart.Transparency = 1
 weatherPart.CanCollide = false
 weatherPart.CanQuery = false
 weatherPart.CanTouch = false
 weatherPart.Anchored = true
 weatherPart.Size = Vector3.new(460, 1, 460)
 weatherPart.Parent = workspace
 weatherEmitter = Instance.new("ParticleEmitter")
 weatherEmitter.Parent = weatherPart
 weatherEmitter.EmissionDirection = Enum.NormalId.Bottom
 weatherLight = Instance.new("PointLight")
 weatherLight.Parent = weatherPart
 weatherLight.Enabled = false
 end

 function updateWeather()
 if not (Toggles.EnableWeather and Toggles.EnableWeather.Value) then
 clearWeatherVisuals(false)
 weatherAnchorPos = nil
 return
 end

 local hrp = getHRP()
 if not hrp then return end
 ensureWeatherPart()
 local wType = Options.WeatherType and Options.WeatherType.Value or "Снег"
 local now = tick()
 local amountMul = getWeatherAmount()
 local speedMul = getWeatherSpeed()
 weatherPart.CFrame = CFrame.new(hrp.Position + Vector3.new(0, 85, 0))

 if wType ~= "Радуга" and workspace:FindFirstChild("RainbowFixed") then clearWeatherVisuals(true) end

 if wType == "Снег" then
 -- Для снега ��ткл��чаем текстурный эмиттер: именно он давал лишние летающие полоски.
 weatherEmitter.Enabled = false
 weatherEmitter.Texture = ""
 weatherEmitter.Acceleration = Vector3.new(0, -6, 0)
 weatherEmitter.Drag = 12
 weatherEmitter.Rate = 0
 weatherEmitter.Lifetime = NumberRange.new(9, 14)
 weatherEmitter.Speed = NumberRange.new(0, 0)
 weatherEmitter.Rotation = NumberRange.new(0, 360)
 weatherEmitter.RotSpeed = NumberRange.new(-55, 55)
 weatherEmitter.SpreadAngle = Vector2.new(70, 70)
 weatherEmitter.Size = NumberSequence.new({NumberSequenceKeypoint.new(0, 1.7), NumberSequenceKeypoint.new(1, 2.6)})
 weatherEmitter.Transparency = NumberSequence.new(0.02)
 weatherEmitter.Color = ColorSequence.new(weatherColor(Color3.fromRGB(255,255,255)))
 weatherLight.Enabled = false
 updateDrops(1/60, hrp.Position, "snow")
 if now - lastDropSpawn > math.max(0.035, 0.075 / amountMul) then
 lastDropSpawn = now
 for i = 1, math.max(1, math.floor(4 * amountMul)) do spawnDrop("snow", hrp.Position, speedMul) end
 end
 elseif wType == "Дождь" then
 weatherEmitter.Enabled = true
 weatherEmitter.Texture = "rbxassetid://182285145"
 weatherEmitter.Acceleration = Vector3.new(0, -320 * speedMul, 0)
 weatherEmitter.Drag = 0
 weatherEmitter.Rate = 12000 * amountMul
 weatherEmitter.Lifetime = NumberRange.new(0.9 / speedMul, 1.4 / speedMul)
 weatherEmitter.Speed = NumberRange.new(120 * speedMul, 180 * speedMul)
 weatherEmitter.SpreadAngle = Vector2.new(18, 18)
 weatherEmitter.Rotation = NumberRange.new(0, 0)
 weatherEmitter.RotSpeed = NumberRange.new(0, 0)
 weatherEmitter.Size = NumberSequence.new(3.2)
 weatherEmitter.Transparency = NumberSequence.new(0.02)
 weatherEmitter.Color = ColorSequence.new(weatherColor(Color3.fromRGB(95, 160, 255)))
 weatherLight.Enabled = false
 updateDrops(1/60, hrp.Position, "rain")
 if now - lastDropSpawn > math.max(0.008, 0.02 / amountMul) then
 lastDropSpawn = now
 for i = 1, math.max(1, math.floor(12 * amountMul)) do spawnDrop("rain", hrp.Position, speedMul) end
 end
 elseif wType == "Радуга" then
 clearDrops()
 weatherEmitter.Enabled = false
 weatherLight.Enabled = false
 ensureRainbow(hrp)
 elseif wType == "Гроза с дождем" then
 weatherEmitter.Enabled = true
 weatherEmitter.Texture = "rbxassetid://182285145"
 weatherEmitter.Acceleration = Vector3.new(0, -360 * speedMul, 0)
 weatherEmitter.Drag = 0
 weatherEmitter.Rate = 15000 * amountMul
 weatherEmitter.Lifetime = NumberRange.new(0.8 / speedMul, 1.2 / speedMul)
 weatherEmitter.Speed = NumberRange.new(150 * speedMul, 210 * speedMul)
 weatherEmitter.SpreadAngle = Vector2.new(20, 20)
 weatherEmitter.Rotation = NumberRange.new(0, 0)
 weatherEmitter.RotSpeed = NumberRange.new(0, 0)
 weatherEmitter.Size = NumberSequence.new(3.5)
 weatherEmitter.Transparency = NumberSequence.new(0.01)
 weatherEmitter.Color = ColorSequence.new(weatherColor(Color3.fromRGB(80, 130, 230)))
 updateDrops(1/60, hrp.Position, "rain")
 if now - lastDropSpawn > math.max(0.006, 0.016 / amountMul) then
 lastDropSpawn = now
 for i = 1, math.max(1, math.floor(16 * amountMul)) do spawnDrop("rain", hrp.Position, speedMul) end
 end
 if now - lastLightning > (math.random(15, 35) / 10) / amountMul then
 lastLightning = now
 for i = 1, math.random(math.max(1, math.floor(2 * amountMul)), math.max(2, math.floor(4 * amountMul))) do
 local side = math.random(1, 2) == 1 and -1 or 1
 local origin = hrp.Position + Vector3.new(math.random(-1000,1000), 0, side * math.random(900,1500))
 makeLightning(origin)
 end
 end
 end
 end

 RunService.Heartbeat:Connect(updateWeather)
end)

-- ==============================================
-- Вкладка: VISUAL — REALISTIC WATER
-- ==============================================

do
local VisualWater = Tabs.Visual:AddRightGroupbox("World")

local waterSavedData = {}

VisualWater:AddToggle("EnableRealisticWater", {
 Text = "Realistic Water",
 Default = false,
 Tooltip = "Заменяет плоскую текстуру океана за картой на НАСТОЯЩУЮ воду, в которой можно плавать.",
 Callback = function(Value)
 pcall(function()
 local terrain = workspace.Terrain
 local model = workspace:FindFirstChild("Map")
 model = model and model:FindFirstChild("AlwaysHereTweenedObjects")
 model = model and model:FindFirstChild("Ocean")
 model = model and model:FindFirstChild("Object")
 model = model and model:FindFirstChild("ObjectModel")
 
 if model then
 if Value then
 -- Включаем воду
 for _, part in ipairs(model:GetChildren()) do
 if part.Name == "Ocean" and part:IsA("BasePart") then
 if not waterSavedData[part] then
 local cf = part.CFrame
 local size = part.Size
 -- Чуть занижаем верхнюю границу, чтобы вода не вылезала на саму карту
 local region = Region3.new(
 cf.Position - (size / 2),
 cf.Position + (size / 2) - Vector3.new(0, 1, 0)
 ):ExpandToGrid(4)
 
 waterSavedData[part] = {
 region = region,
 transparency = part.Transparency,
 cancollide = part.CanCollide
 }
 end
 
 local data = waterSavedData[part]
 -- Прячем оригинальную деталь
 part.Transparency = 1
 part.CanCollide = false
 -- Заливаем регион водой
 terrain:FillRegion(data.region, 4, Enum.Material.Water)
 end
 end
 else
 -- Выключаем воду
 for part, data in pairs(waterSavedData) do
 if part and part.Parent then
 -- Возвращаем деталь
 part.Transparency = data.transparency
 -- Если включен WaterWalk, коллизия долж��а остаться true. Иначе - как было.
 if Toggles.EnableWaterWalk and Toggles.EnableWaterWalk.Value then
 part.CanCollide = true
 else
 part.CanCollide = data.cancollide
 end
 end
 -- Убираем воду (заливаем воздухом)
 terrain:FillRegion(data.region, 4, Enum.Material.Air)
 end
 end
 end
 end)
 end
})

VisualWater:AddSlider("WaterWaveSize", {
 Text = "Размер волн",
 Default = 0.15,
 Min = 0,
 Max = 3,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 - штиль (гладкая вода), 3 - гигантские волны",
 Callback = function(Value)
 pcall(function()
 workspace.Terrain.WaterWaveSize = Value
 workspace.Terrain.WaterWaveSpeed = Value * 20
 end)
 end
})




end
-- ==============================================
-- Вкладка: VISUAL — TRACERS
-- ==============================================
VisualTracers = Tabs.Visual:AddRightGroupbox("Tracer Lines")

tracerColor = Color3.fromRGB(255, 0, 0)
tracerObjects = {}

VisualTracers:AddToggle("EnableTracers", {
 Text = "Tracer Lines",
 Default = false,
 Tooltip = "Линии от экрана к игрокам",
}):AddColorPicker("TracerColor", {
 Default = Color3.fromRGB(255, 0, 0),
 Title = "Цвет линий",
 Callback = function(Value)
 tracerColor = Value
 for _, obj in pairs(tracerObjects) do
 if obj.line then obj.line.Color = tracerColor end
 end
 end,
})

VisualTracers:AddSlider("TracerWidth", {
 Text = "Толщина линии",
 Default = 1,
 Min = 1,
 Max = 5,
 Rounding = 0,
 Tooltip = "Ширина tracer линии",
})

VisualTracers:AddDropdown("TracerOrigin", {
 Text = "Откуда вести",
 Default = "Центр экрана",
 Values = {"Сверху по центру", "Центр экрана", "Снизу по центру"},
 Multi = false,
 Tooltip = "Точка откуда ведутся линии",
})

VisualTracers:AddToggle("TracerThroughWalls", {
 Text = "Показывать за спиной",
 Default = false,
 Tooltip = "Показывать линии к игрокам даже если они за камерой",
})

function getTracerOrigin()
 local cam = workspace.CurrentCamera
 if not cam then return Vector2.new(0, 0) end
 local vp = cam.ViewportSize
 local origin = Options.TracerOrigin and Options.TracerOrigin.Value or "Центр экрана"
 if origin == "Сверху по центру" then
 return Vector2.new(vp.X / 2, 0)
 elseif origin == "Снизу по центру" then
 return Vector2.new(vp.X / 2, vp.Y)
 else
 return Vector2.new(vp.X / 2, vp.Y / 2)
 end
end

function createTracer(plr)
 if tracerObjects[plr] then return end
 local line = Drawing.new("Line")
 line.Thickness = 1
 line.Color = tracerColor
 line.Visible = false
 tracerObjects[plr] = {line = line}
end

function removeTracer(plr)
 local obj = tracerObjects[plr]
 if not obj then return end
 if obj.line then obj.line:Remove() end
 tracerObjects[plr] = nil
end

function updateTracer(plr)
 local obj = tracerObjects[plr]
 if not obj then return end
 if not (Toggles.EnableTracers and Toggles.EnableTracers.Value) then
 obj.line.Visible = false
 return
 end
 local char = plr.Character
 if not char then obj.line.Visible = false; return end
 local root = char:FindFirstChild("HumanoidRootPart")
 local hum = char:FindFirstChild("Humanoid")
 if not root or not hum or hum.Health <= 0 then
 obj.line.Visible = false
 return
 end
 local cam = workspace.CurrentCamera
 if not cam then obj.line.Visible = false; return end
 local origin = getTracerOrigin()
 local throughWalls = Toggles.TracerThroughWalls and Toggles.TracerThroughWalls.Value or false
 -- Линия рисуется НАПРЯМУЮ к позиции игрока н�� экране — от origin к screenPos
 local screenPos, onScreen = cam:WorldToViewportPoint(root.Position)
 if onScreen then
 -- Игрок на экране: линия от origin прямо к игроку
 obj.line.From = origin
 obj.line.To = Vector2.new(screenPos.X, screenPos.Y)
 elseif throughWalls then
 -- Игрок за камерой: проекция на плоскость экрана
 local camCF = cam.CFrame
 local toPlayer = root.Position - camCF.Position
 local dx = toPlayer:Dot(camCF.RightVector)
 local dy = toPlayer:Dot(camCF.UpVector)
 -- Угол к игроку (экранный Y инвертирован → -dy)
 local angle = math.atan2(-dy, dx)
 -- Линия от origin в направлении игрока за спиной (фиксированная длина)
 local lineLen = 200
 obj.line.From = origin
 obj.line.To = origin + Vector2.new(math.cos(angle), math.sin(angle)) * lineLen
 else
 obj.line.Visible = false
 return
 end
 obj.line.Color = tracerColor
 obj.line.Thickness = Options.TracerWidth and Options.TracerWidth.Value or 1
 obj.line.Visible = true
end

-- TRACERS: RenderStepped (каждый кадр, плавно)
RunService.RenderStepped:Connect(function()
 if not (Toggles.EnableTracers and Toggles.EnableTracers.Value) then return end
 -- Не рисуем линии ��огда меню видимо
 if Window and Window.Visible == true then return end
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer then
 local obj = tracerObjects[plr]
 if not obj then createTracer(plr); obj = tracerObjects[plr] end
 if obj then updateTracer(plr) end
 end
 end
end)

Players.PlayerRemoving:Connect(removeTracer)

Toggles.EnableTracers:OnChanged(function()
 if not Toggles.EnableTracers.Value then
 for _, obj in pairs(tracerObjects) do
 if obj.line then obj.line.Visible = false end
 end
 end
end)

do
-- ==============================================
-- Вкладка: VISUAL — GRAB LINE (кастомная форма линии захвата)
-- ==============================================
-- Меняет вид линии (Grab), кото��ой ты хватаешь игроков. Линия захвата в игре —
-- это Beam с именем "GrabBeam" внутри модели GrabParts (е�� создаёт и игра, и
-- силент-граб). Мы КАЖДЫЙ кадр находим все такие Beam и применяем к ним выбранную
-- ф��рму (толщина, свечение, текстура, цвет). Физику линии (изгиб/кривизну)
-- НЕ трогаем �� её ос��авляе�� игре, чтобы линия вела себя как обычно.
VisualGrabLine = Tabs.Visual:AddRightGroupbox("Grab Line")

grabLineOriginals = setmetatable({}, { __mode = "k" }) -- [beam] = исходные свойства
grabBeams = {} -- живой набор [beam] = true

function isGrabBeam(obj)
 return obj:IsA("Beam") and obj.Name == "GrabBeam"
end

-- Запоминаем оригинальные свойства Beam один раз (чтобы вернуть при выключении)
function saveGrabBeamOriginal(beam)
 if grabLineOriginals[beam] then return end
 grabLineOriginals[beam] = {
 Width0 = beam.Width0, Width1 = beam.Width1,
 Texture = beam.Texture, TextureLength = beam.TextureLength,
 TextureSpeed = beam.TextureSpeed, TextureMode = beam.TextureMode,
 LightEmission = beam.LightEmission,
 Color = beam.Color,
 Transparency = beam.Transparency,
 }
end

function restoreGrabBeam(beam)
 local o = grabLineOriginals[beam]
 if not o then return end
 pcall(function()
 beam.Width0 = o.Width0; beam.Width1 = o.Width1
 beam.Texture = o.Texture; beam.TextureLength = o.TextureLength
 beam.TextureSpeed = o.TextureSpeed; beam.TextureMode = o.TextureMode
 beam.LightEmission = o.LightEmission
 beam.Color = o.Color
 beam.Transparency = o.Transparency
 end)
end






-- Красивые текстуры для линии — ��аждый стиль свой рисунок.
-- Берём встроенные текстуры Roblox (они всегда доступны и точно
-- отрисуются) + игровую текстуру энергии. Только текстуры,
-- никакого перелива цветов.
-- Большой набор красивых текстур линии захвата (из unstable.txt + встроенные Roblox)
grabTextures = {
 -- Встроенные Roblox-текстуры
 ["Молния"] = { id = "rbxasset://textures/particles/sparkles_main.dds", length = 0.7, speed = 5 },
 ["Взрыв"] = { id = "rbxasset://textures/particles/explosion01_implosion_main.dds", length = 2.5, speed = 1.5 },
 ["Вспышка"] = { id = "rbxasset://textures/particles/explosion01_seeds.dds", length = 1.4, speed = 2.5 },
 ["Плазма"] = { id = "rbxasset://textures/particles/explosion01_layer2.dds", length = 3, speed = 1.5, width = 1.0 },
 ["Дым"] = { id = "rbxasset://textures/particles/smoke_main.dds", length = 2, speed = 1, width = 1.4 },
 ["Пламя"] = { id = "rbxasset://textures/particles/fire_main.dds", length = 1.6, speed = 3, width = 1.2 },
 ["Искры"] = { id = "rbxasset://textures/particles/sparkles_main.dds", length = 1.2, speed = 2, width = 0.9 },
 -- Текстуры из unstable.txt (asset IDs)
 ["Non-Gamepass"] = { id = "rbxassetid://8933346550", length = 1, speed = 1, width = 1.0 },
 ["Gamepass"] = { id = "rbxassetid://8933355899", length = 1, speed = 1, width = 1.0 },
 ["Chain"] = { id = "rbxassetid://81358145120405", length = 1, speed = 1, width = 1.0 },
 ["Chain 2"] = { id = "rbxassetid://132910145874066", length = 1, speed = 1, width = 1.0 },
 ["Chain 3"] = { id = "rbxassetid://128466395060514", length = 1, speed = 1, width = 1.0 },
 ["Chain 4"] = { id = "rbxassetid://73368670987191", length = 1, speed = 1, width = 1.0 },
 ["Rope"] = { id = "rbxassetid://78999022056924", length = 1, speed = 1, width = 1.0 },
 ["Spring"] = { id = "rbxassetid://18837732116", length = 1, speed = 1, width = 1.0 },
 ["Circle"] = { id = "rbxassetid://5367817750", length = 1, speed = 1, width = 1.0 },
 ["Circle-Outline"] = { id = "rbxassetid://12201347372", length = 1, speed = 1, width = 1.0 },
 ["Triangle"] = { id = "rbxassetid://4704920160", length = 1, speed = 1, width = 1.0 },
 ["Triangle-Outline"] = { id = "rbxassetid://94666748694025", length = 1, speed = 1, width = 1.0 },
 ["Square"] = { id = "rbxassetid://15007588972", length = 1, speed = 1, width = 1.0 },
 ["Square-Outline"] = { id = "rbxassetid://15420927706", length = 1, speed = 1, width = 1.0 },
 ["Heart"] = { id = "rbxassetid://89015294175898", length = 1, speed = 1, width = 1.0 },
 ["Heart-Outline"] = { id = "rbxassetid://125373934805238", length = 1, speed = 1, width = 1.0 },
 ["Moon"] = { id = "rbxassetid://9013498676", length = 1, speed = 1, width = 1.0 },
 ["Dots"] = { id = "rbxassetid://9169659357", length = 1, speed = 1, width = 1.0 },
 ["Bubble"] = { id = "rbxassetid://1249690853", length = 1, speed = 1, width = 1.0 },
 ["Star"] = { id = "rbxassetid://5639840603", length = 1, speed = 1, width = 1.0 },
 ["Robux"] = { id = "rbxassetid://11560341132", length = 1, speed = 1, width = 1.0 },
 ["Roblox-Logo"] = { id = "rbxassetid://12348119032", length = 1, speed = 1, width = 1.0 },
 ["Brick"] = { id = "rbxassetid://4430903072", length = 1, speed = 1, width = 1.0 },
 ["Studs"] = { id = "rbxassetid://15539356451", length = 1, speed = 1, width = 1.0 },
 ["Fire"] = { id = "rbxassetid://18654087326", length = 1, speed = 1, width = 1.0 },
 ["Lazar"] = { id = "rbxassetid://8922958725", length = 1, speed = 1, width = 1.0 },
 ["Spider-Web"] = { id = "rbxassetid://123815660139244", length = 1, speed = 1, width = 1.0 },
 ["Smoke"] = { id = "rbxassetid://12900071392", length = 1, speed = 1, width = 1.0 },
 ["Audio-Visualiser"] = { id = "rbxassetid://81588563590679", length = 1, speed = 1, width = 1.0 },
 ["Pulse"] = { id = "rbxassetid://82163767314193", length = 1, speed = 1, width = 1.0 },
 ["Arrow"] = { id = "rbxassetid://9006027964", length = 1, speed = 1, width = 1.0 },
 ["Arrow 2"] = { id = "rbxassetid://10249261576", length = 1, speed = 1, width = 1.0 },
}

-- Применяем выбранную форму к одному Beam.
-- ВАЖНО: физику линии (CurveSize/Segments/изгиб) НЕ трогаем — её полностью
-- оста��ляем игре, поэтому линия ведёт себя (изгибает��я/натягивается) как обычно.
-- Меняем только текстуру, толщину и свечение. Цвет линии НЕ трогаем —
-- оставляем цвет игры (никаких радуг и переливов).
-- Готовые узоры прозрачности вдоль линии: то��ько рисунок из прозрачных и
-- непр��зрачных участков (пунктир, точки, комета). Цвет и физику НЕ трогаем.
-- Рабо��ают всегда — без внешних ас��етов/текстур.
grabDashSeq = NumberSequence.new({
 NumberSequenceKeypoint.new(0, 0),
 NumberSequenceKeypoint.new(0.12, 0),
 NumberSequenceKeypoint.new(0.13, 1),
 NumberSequenceKeypoint.new(0.24, 1),
 NumberSequenceKeypoint.new(0.25, 0),
 NumberSequenceKeypoint.new(0.37, 0),
 NumberSequenceKeypoint.new(0.38, 1),
 NumberSequenceKeypoint.new(0.49, 1),
 NumberSequenceKeypoint.new(0.5, 0),
 NumberSequenceKeypoint.new(0.62, 0),
 NumberSequenceKeypoint.new(0.63, 1),
 NumberSequenceKeypoint.new(0.74, 1),
 NumberSequenceKeypoint.new(0.75, 0),
 NumberSequenceKeypoint.new(0.87, 0),
 NumberSequenceKeypoint.new(0.88, 1),
 NumberSequenceKeypoint.new(1, 1),
})
grabDotSeq = NumberSequence.new({
 NumberSequenceKeypoint.new(0, 0),
 NumberSequenceKeypoint.new(0.05, 0),
 NumberSequenceKeypoint.new(0.06, 1),
 NumberSequenceKeypoint.new(0.24, 1),
 NumberSequenceKeypoint.new(0.25, 0),
 NumberSequenceKeypoint.new(0.3, 0),
 NumberSequenceKeypoint.new(0.31, 1),
 NumberSequenceKeypoint.new(0.49, 1),
 NumberSequenceKeypoint.new(0.5, 0),
 NumberSequenceKeypoint.new(0.55, 0),
 NumberSequenceKeypoint.new(0.56, 1),
 NumberSequenceKeypoint.new(0.74, 1),
 NumberSequenceKeypoint.new(0.75, 0),
 NumberSequenceKeypoint.new(0.8, 0),
 NumberSequenceKeypoint.new(0.81, 1),
 NumberSequenceKeypoint.new(0.99, 1),
 NumberSequenceKeypoint.new(1, 0),
})
grabCometSeq = NumberSequence.new({
 NumberSequenceKeypoint.new(0, 1),
 NumberSequenceKeypoint.new(0.6, 0.55),
 NumberSequenceKeypoint.new(1, 0),
})
grabSolidSeq = NumberSequence.new(0)

-- Применяем выбранный стиль к одному Beam. Физику (изгиб/сегменты) и ЦВЕТ
-- линии НЕ трогаем. Ширина берётся из ползунка "Ширина линии" (для всех
-- стилей, кроме "Обычная", где возвращаем полностью вид игры).
-- Rainbow connection state
grabRainbowConn = nil
grabRainbowHue = 0

-- Pulse state
grabPulseVal = 0
grabPulseDir = 1

function applyGrabBeamStyle(beam)
 local style = (Options.GrabLineStyle and Options.GrabLineStyle.Value) or "Обычная"
 local o = grabLineOriginals[beam]
 local t = tick()
 local tex = grabTextures[style]
 local baseW = (Options.GrabLineWidth and Options.GrabLineWidth.Value) or 1

 -- "Обычная" — полностью возвращаем вид игры и выходим.
 if style == "Обычная" then
  if o then
   beam.Texture = o.Texture
   beam.TextureLength = o.TextureLength
   beam.TextureSpeed = o.TextureSpeed
   beam.TextureMode = o.TextureMode
   beam.Width0 = o.Width0; beam.Width1 = o.Width1
   beam.LightEmission = o.LightEmission
   beam.Transparency = o.Transparency
   beam.Color = o.Color
  end
  return
 end

 -- Low Quality — без текстуры, цвет игры
 if style == "Low Quality" then
  beam.Texture = ""
  if o then
   beam.TextureLength = o.TextureLength
   beam.TextureSpeed = o.TextureSpeed
   beam.TextureMode = o.TextureMode
  end
  beam.Width0 = baseW; beam.Width1 = baseW
  beam.LightEmission = (Options.GrabLineLightEm and Options.GrabLineLightEm.Value) or 1
  beam.Transparency = grabSolidSeq
  -- Цвет и Rainbow обрабатываются ниже (общий блок)
 elseif style == "Custom" then
  -- Custom Texture ID из инпута
  local customId = (Options.GrabCustomTexId and Options.GrabCustomTexId.Value) or ""
  -- Извлекаем только цифры (как в 9rr.txt cleanId)
  local cleanId = tostring(customId):gsub("%D", "")
  if cleanId ~= "" then
   beam.Texture = "rbxassetid://" .. cleanId
   beam.TextureMode = Enum.TextureMode.Wrap
   beam.TextureLength = (Options.GrabLineLengthEnabled and Options.GrabLineLengthEnabled.Value and Options.GrabLineLength and Options.GrabLineLength.Value) or 1
   beam.TextureSpeed = (Options.GrabLineSpeedEnabled and Options.GrabLineSpeedEnabled.Value and Options.GrabLineSpeed and Options.GrabLineSpeed.Value) or 1
  elseif o then
   beam.Texture = o.Texture
   beam.TextureLength = o.TextureLength
   beam.TextureSpeed = o.TextureSpeed
   beam.TextureMode = o.TextureMode
  end
  beam.Width0 = baseW; beam.Width1 = baseW
  beam.LightEmission = (Options.GrabLineLightEm and Options.GrabLineLightEm.Value) or 1
  beam.Transparency = grabSolidSeq
 elseif tex then
  -- Обычные текстуры из grabTextures
  local texId = tex.id
  -- Special: Custom entry returns "__CUSTOM__" — skip if user somehow selected
  if texId == "__CUSTOM__" then
   local customId = (Options.GrabCustomTexId and Options.GrabCustomTexId.Value) or ""
   local cleanId = tostring(customId):gsub("%D", "")
   texId = cleanId ~= "" and ("rbxassetid://" .. cleanId) or ""
  end
  beam.Texture = texId
  beam.TextureMode = Enum.TextureMode.Wrap
  if Options.GrabLineLengthEnabled and Options.GrabLineLengthEnabled.Value and Options.GrabLineLength then
   beam.TextureLength = Options.GrabLineLength.Value
  else
   beam.TextureLength = tex.length
  end
  if Options.GrabLineSpeedEnabled and Options.GrabLineSpeedEnabled.Value and Options.GrabLineSpeed then
   beam.TextureSpeed = Options.GrabLineSpeed.Value
  else
   beam.TextureSpeed = tex.speed
  end
  beam.Width0 = baseW; beam.Width1 = baseW
  beam.LightEmission = (Options.GrabLineLightEm and Options.GrabLineLightEm.Value) or 1
  beam.Transparency = grabSolidSeq
 else
  -- Fallback
  beam.Width0 = baseW; beam.Width1 = baseW
  beam.LightEmission = (Options.GrabLineLightEm and Options.GrabLineLightEm.Value) or 1
  beam.Transparency = grabSolidSeq
 end

 -- ===== ЦВЕТ ЛИНИИ =====
 -- Rainbow имеет приоритет, потом обычный color picker, потом цвет игры
 if Toggles.GrabLineRainbow and Toggles.GrabLineRainbow.Value then
  local h = (tick() * 0.5) % 1
  local c = Color3.fromHSV(h, 1, 1)
  beam.Color = ColorSequence.new(c, c)
 elseif Toggles.GrabLineColorEnabled and Toggles.GrabLineColorEnabled.Value then
  local c0 = (Options.GrabLineColor0 and Options.GrabLineColor0.Value) or Color3.fromRGB(255, 255, 255)
  local c1 = (Options.GrabLineColor1 and Options.GrabLineColor1.Value) or c0
  beam.Color = ColorSequence.new(c0, c1)
 elseif o then
  beam.Color = o.Color
 end

 -- ===== ПУЛЬСАЦИЯ ШИРИНЫ =====
 if Toggles.GrabLinePulse and Toggles.GrabLinePulse.Value then
  local pulseSpeed = (Options.GrabLinePulseSpeed and Options.GrabLinePulseSpeed.Value) or 2
  local w = baseW * (0.55 + 0.45 * (0.5 + 0.5 * math.sin(t * pulseSpeed * 2)))
  beam.Width0 = w; beam.Width1 = w
 end
end

VisualGrabLine:AddToggle("EnableGrabLine", {
 Text = "Custom Grab Line",
 Default = false,
 Tooltip = "Меняет форму и вид линии захвата (Grab), кото��ой ты хватаешь",
})

VisualGrabLine:AddDropdown("GrabLineStyle", {
 Text = "Форма линии",
 Default = "Обычная",
 Values = {
  "Обычная",
  -- Встроенные
  "Дым", "Пламя", "Искры", "Молния", "Плазма", "Взрыв", "Вспышка",
  -- Из unstable.txt
  "Non-Gamepass", "Gamepass",
  "Chain", "Chain 2", "Chain 3", "Chain 4",
  "Rope", "Spring",
  "Circle", "Circle-Outline",
  "Triangle", "Triangle-Outline",
  "Square", "Square-Outline",
  "Heart", "Heart-Outline",
  "Moon", "Dots", "Bubble", "Star",
  "Robux", "Roblox-Logo",
  "Brick", "Studs",
  "Fire", "Lazar",
  "Spider-Web", "Smoke",
  "Audio-Visualiser", "Pulse",
  "Arrow", "Arrow 2",
 },
 Multi = false,
 Tooltip = "Вид линии захвата: 38 разных текстур (из unstable.txt + встроенные)",
})

VisualGrabLine:AddSlider("GrabLineWidth", {
 Text = "Ширина линии",
 Default = 1,
 Min = 0.05,
 Max = 3,
 Rounding = 2,
 Compact = false,
 Tooltip = "Толщина линии захвата (для всех стилей, кроме «Обычная»)",
})

-- Custom Texture ID (как в 9rr.txt customTextureId)
VisualGrabLine:AddInput("GrabCustomTexId", {
 Text = "Custom Texture ID",
 Default = "",
 Placeholder = "rbxassetid://1234567890",
 Tooltip = "Введи asset ID текстуры (например 5639840603). Активируй «Custom» в дропдауне выше.",
})

-- Color picker для Start (как в 9rr.txt BeamColor0)
VisualGrabLine:AddLabel("Цвет линии — Start"):AddColorPicker("GrabLineColor0", {
 Default = Color3.fromRGB(255, 255, 255),
 Title = "Цвет начала линии",
})

-- Color picker для End (как в 9rr.txt BeamColor1)
VisualGrabLine:AddLabel("Цвет линии — End"):AddColorPicker("GrabLineColor1", {
 Default = Color3.fromRGB(255, 255, 255),
 Title = "Цвет конца линии",
})

-- Включение цвета (как в 9rr.txt EnableBeamColor)
VisualGrabLine:AddToggle("GrabLineColorEnabled", {
 Text = "Включить цвет линии",
 Default = false,
 Tooltip = "Применять выбранные цвета к линии (иначе — цвет игры)",
})

-- Rainbow mode (как в 9rr.txt Rainbow)
VisualGrabLine:AddToggle("GrabLineRainbow", {
 Text = "Rainbow (перелив)",
 Default = false,
 Tooltip = "Линия автоматически переливается всеми цветами радуги",
})

-- Texture Speed (как в 9rr.txt textureSpeed)
VisualGrabLine:AddToggle("GrabLineSpeedEnabled", {
 Text = "Своя скорость текстуры",
 Default = false,
 Tooltip = "Включить ползунок скорости прокрутки текстуры",
})

VisualGrabLine:AddSlider("GrabLineSpeed", {
 Text = "Скорость текстуры",
 Default = 1,
 Min = -10,
 Max = 10,
 Rounding = 1,
 Compact = false,
 Tooltip = "Скорость прокрутки текстуры вдоль линии (отрицательная = реверс)",
})

-- Texture Length (как в 9rr.txt textureLength)
VisualGrabLine:AddToggle("GrabLineLengthEnabled", {
 Text = "Своя длина текстуры",
 Default = false,
 Tooltip = "Включить ползунок длины текстуры",
})

VisualGrabLine:AddSlider("GrabLineLength", {
 Text = "Длина текстуры",
 Default = 1,
 Min = 0.1,
 Max = 10,
 Rounding = 2,
 Compact = false,
 Tooltip = "Длина (размер) текстуры вдоль линии",
})

-- Light Emission (свечение)
VisualGrabLine:AddSlider("GrabLineLightEm", {
 Text = "Свечение (LightEmission)",
 Default = 1,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "Сила свечения текстуры (0 = нет, 1 = максимум)",
})

-- Pulse (пульсация ширины, как в 9rr.txt beamPulseEnabled)
VisualGrabLine:AddToggle("GrabLinePulse", {
 Text = "Пульсация ширины",
 Default = false,
 Tooltip = "Ширина линии пульсирует (как дыхание)",
})

VisualGrabLine:AddSlider("GrabLinePulseSpeed", {
 Text = "Скорость пульсации",
 Default = 2,
 Min = 0.1,
 Max = 10,
 Rounding = 1,
 Compact = false,
 Tooltip = "Как быстро пульсирует ширина",
})

-- Следим за появлением/удалением лини�� захвата (без перебора всего workspace каждый кадр)
-- ОПТИМИЗАЦИЯ: отложили GetDescendants в фон — не блокируем загрузку UI
task.spawn(function()
 for _, obj in ipairs(workspace:GetDescendants()) do
 if isGrabBeam(obj) then grabBeams[obj] = true end
 end
end)
workspace.DescendantAdded:Connect(function(obj)
 if isGrabBeam(obj) then grabBeams[obj] = true end
end)
workspace.DescendantRemoving:Connect(function(obj)
 if grabBeams[obj] then
 grabBeams[obj] = nil
 grabLineOriginals[obj] = nil
 end
end)

Toggles.EnableGrabLine:OnChanged(function()
 if not Toggles.EnableGrabLine.Value then
 for beam in pairs(grabBeams) do
 if beam and beam.Parent then restoreGrabBeam(beam) end
 end
 end
end)

-- Каждый кадр применяем выбранную форму ко всем активным линиям захвата
RunService.RenderStepped:Connect(function()
 if not (Toggles.EnableGrabLine and Toggles.EnableGrabLine.Value) then return end
 for beam in pairs(grabBeams) do
 if beam.Parent then
 saveGrabBeamOriginal(beam)
 pcall(applyGrabBeamStyle, beam)
 else
 grabBeams[beam] = nil
 end
 end
end)


-- ==============================================
do
-- Вкладка: VISUAL — TARGET ESP (ON HOVER / MAIN TARGET)
-- ==============================================
local VisualHoverHL = Tabs.Visual:AddRightGroupbox("Target ESP")

VisualHoverHL:AddDropdown("TargetESPMode", {
 Text = "Target ESP Mode",
 Default = "None",
 Values = {"None", "Style 1 (Highlight)", "Style 2 (Box)", "Style 3 (Brackets)", "Style 4 (Particles)"},
 Tooltip = "Выделять игрока при наведении",
})

VisualHoverHL:AddLabel("Цвет Target ESP"):AddColorPicker("TargetESPColor", {
 Default = Color3.fromRGB(200, 0, 255),
 Title = "Цвет Target ESP",
})

VisualHoverHL:AddDropdown("HoverHighlightDist", {
 Text = "Дистанция",
 Default = "30 studs",
 Values = {"20 studs", "30 studs"},
 Multi = false,
 Tooltip = "Максимальная дистанция для выделения",
})

do
-- Logic for Target ESP
local targetEspHl = nil
local targetEspBox = nil
local targetEspBrackets = nil
local targetEspParticles = nil

local function removeTargetESP()
 if targetEspHl then pcall(function() targetEspHl:Destroy() end) targetEspHl = nil end
 if targetEspBox then pcall(function() targetEspBox:Destroy() end) targetEspBox = nil end
 if targetEspBrackets then pcall(function() targetEspBrackets:Destroy() end) targetEspBrackets = nil end
 if targetEspParticles then pcall(function() targetEspParticles:Destroy() end) targetEspParticles = nil end
end

local function createBrackets(parent, color)
 local bgui = Instance.new("BillboardGui")
 bgui.Name = "OblivionTargetBrackets"
 bgui.Adornee = parent
 bgui.Size = UDim2.new(4, 0, 5.5, 0)
 bgui.AlwaysOnTop = true
 bgui.LightInfluence = 0

 local thickness = 10
 local length = 0.25

 local function makeLine(size, pos, anchor)
  local f = Instance.new("Frame")
  f.BorderSizePixel = 0
  f.BackgroundColor3 = color
  f.Size = size
  f.Position = pos
  f.AnchorPoint = anchor
  f.Parent = bgui
  local uiCorner = Instance.new("UICorner")
  uiCorner.CornerRadius = UDim.new(1, 0)
  uiCorner.Parent = f
  return f
 end

 makeLine(UDim2.new(length, 0, 0, thickness), UDim2.new(0, 0, 0, 0), Vector2.new(0, 0))
 makeLine(UDim2.new(0, thickness, length, 0), UDim2.new(0, 0, 0, 0), Vector2.new(0, 0))
 makeLine(UDim2.new(length, 0, 0, thickness), UDim2.new(1, 0, 0, 0), Vector2.new(1, 0))
 makeLine(UDim2.new(0, thickness, length, 0), UDim2.new(1, 0, 0, 0), Vector2.new(1, 0))
 makeLine(UDim2.new(length, 0, 0, thickness), UDim2.new(0, 0, 1, 0), Vector2.new(0, 1))
 makeLine(UDim2.new(0, thickness, length, 0), UDim2.new(0, 0, 1, 0), Vector2.new(0, 1))
 makeLine(UDim2.new(length, 0, 0, thickness), UDim2.new(1, 0, 1, 0), Vector2.new(1, 1))
 makeLine(UDim2.new(0, thickness, length, 0), UDim2.new(1, 0, 1, 0), Vector2.new(1, 1))

 bgui.Parent = parent
 return bgui
end

local function createParticles(parent, color)
 local att = Instance.new("Attachment")
 att.Name = "OblivionTargetParticlesAtt"
 att.Position = Vector3.new(0, -1.5, 0)
 
 local pe = Instance.new("ParticleEmitter")
 pe.Name = "OblivionTargetParticles"
 pe.Texture = "rbxassetid://284205403"
 pe.Color = ColorSequence.new(color)
 pe.Size = NumberSequence.new({NumberSequenceKeypoint.new(0, 0), NumberSequenceKeypoint.new(0.5, 2), NumberSequenceKeypoint.new(1, 0)})
 pe.Rate = 40
 pe.Lifetime = NumberRange.new(1, 2)
 pe.Speed = NumberRange.new(3, 7)
 pe.VelocitySpread = 60
 pe.EmissionDirection = Enum.NormalId.Top
 pe.Parent = att
 
 att.Parent = parent
 return att
end

RunService.RenderStepped:Connect(function()
 local mode = Options.TargetESPMode and Options.TargetESPMode.Value or "None"
 if mode == "None" then
  removeTargetESP()
  return
 end

 local mouse = LocalPlayer:GetMouse()
 local hitPart = mouse.Target
 if not hitPart then
  removeTargetESP()
  return
 end
 
 local distStr = Options.HoverHighlightDist and Options.HoverHighlightDist.Value or "30 studs"
 local maxDist = tonumber(distStr:match("(%d+)")) or 30
 local targetChar = hitPart:FindFirstAncestorOfClass("Model")
 if not targetChar then
  removeTargetESP()
  return
 end
 local plr = Players:GetPlayerFromCharacter(targetChar)
 if not plr or plr == LocalPlayer then
  removeTargetESP()
  return
 end
 local myChar = LocalPlayer.Character
 local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
 local theirRoot = targetChar:FindFirstChild("HumanoidRootPart")
 if not myRoot or not theirRoot then
  removeTargetESP()
  return
 end
 local dist = (myRoot.Position - theirRoot.Position).Magnitude
 if dist > maxDist then
  removeTargetESP()
  return
 end

 local root = targetChar:FindFirstChild("HumanoidRootPart")
 if not root then removeTargetESP() return end
 
 local espColor = Options.TargetESPColor and Options.TargetESPColor.Value or Color3.fromRGB(200, 0, 255)

 if mode == "Style 1 (Highlight)" then
  if targetEspBox then pcall(function() targetEspBox:Destroy() end) targetEspBox = nil end
  if targetEspBrackets then pcall(function() targetEspBrackets:Destroy() end) targetEspBrackets = nil end
  if targetEspParticles then pcall(function() targetEspParticles:Destroy() end) targetEspParticles = nil end
  
  if not targetEspHl or targetEspHl.Parent ~= targetChar then
   if targetEspHl then pcall(function() targetEspHl:Destroy() end) end
   targetEspHl = Instance.new("Highlight")
   targetEspHl.Name = "OblivionTargetESP"
   targetEspHl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
   targetEspHl.FillTransparency = 0.5
   targetEspHl.OutlineTransparency = 0
   targetEspHl.Parent = targetChar
  end
  targetEspHl.FillColor = espColor
  targetEspHl.OutlineColor = espColor

 elseif mode == "Style 2 (Box)" then
  if targetEspHl then pcall(function() targetEspHl:Destroy() end) targetEspHl = nil end
  if targetEspBrackets then pcall(function() targetEspBrackets:Destroy() end) targetEspBrackets = nil end
  if targetEspParticles then pcall(function() targetEspParticles:Destroy() end) targetEspParticles = nil end
  
  if not targetEspBox or targetEspBox.Parent ~= root then
   if targetEspBox then pcall(function() targetEspBox:Destroy() end) end
   targetEspBox = Instance.new("SelectionBox")
   targetEspBox.Name = "OblivionTargetESPBox"
   targetEspBox.LineThickness = 0.05
   targetEspBox.SurfaceTransparency = 0.8
   targetEspBox.Adornee = root
   targetEspBox.Parent = root
  end
  targetEspBox.Color3 = espColor
  targetEspBox.SurfaceColor3 = espColor

 elseif mode == "Style 3 (Brackets)" then
  if targetEspHl then pcall(function() targetEspHl:Destroy() end) targetEspHl = nil end
  if targetEspBox then pcall(function() targetEspBox:Destroy() end) targetEspBox = nil end
  if targetEspParticles then pcall(function() targetEspParticles:Destroy() end) targetEspParticles = nil end

  if not targetEspBrackets or targetEspBrackets.Parent ~= root then
   if targetEspBrackets then pcall(function() targetEspBrackets:Destroy() end) end
   targetEspBrackets = createBrackets(root, espColor)
  else
   for _, child in ipairs(targetEspBrackets:GetChildren()) do
    if child:IsA("Frame") then child.BackgroundColor3 = espColor end
   end
  end

 elseif mode == "Style 4 (Particles)" then
  if targetEspHl then pcall(function() targetEspHl:Destroy() end) targetEspHl = nil end
  if targetEspBox then pcall(function() targetEspBox:Destroy() end) targetEspBox = nil end
  if targetEspBrackets then pcall(function() targetEspBrackets:Destroy() end) targetEspBrackets = nil end

  if not targetEspParticles or targetEspParticles.Parent ~= root then
   if targetEspParticles then pcall(function() targetEspParticles:Destroy() end) end
   targetEspParticles = createParticles(root, espColor)
  else
   local pe = targetEspParticles:FindFirstChild("OblivionTargetParticles")
   if pe then pe.Color = ColorSequence.new(espColor) end
  end
 end
end)end



-- ==============================================
end
-- Вкладка: VISUAL — PALLET COLOR
-- ==============================================
-- Красит палетку, на которой стоишь, в выбранный цвет.
-- Цвет сбрасывается обратно ��олько через N секунд после того,
-- как ты с неё сошёл (по умол��анию 3 сек).
local VisualPallet = Tabs.Visual:AddLeftGroupbox("Pallet Color")

-- === SAFE ADDON: HATS VISUAL ===
do
 local HatSection = Tabs.Visual:AddRightGroupbox("Hats")
 local hatTypes = { "Китайская шляпа", "Корона", "Пропеллер", "Уши кота", "Шляпа Волшебника", "Шляпа Ковбоя", "Рога Демона", "Шлем Рыцаря" }

 HatSection:AddToggle("EnableHat", {
  Text = "Надеть шляпу",
  Default = false,
  Tooltip = "Локальная визуальная шляпа",
 }):AddColorPicker("HatColor", {
  Default = Color3.fromRGB(255, 50, 50),
  Title = "Цвет шляпы",
 })

 HatSection:AddDropdown("HatType", {
  Text = "Тип шляпы",
  Values = hatTypes,
  Default = "Китайская шляпа",
  Multi = false,
 })

 HatSection:AddSlider("HatSize", {
  Text = "Размер шляпы",
  Default = 1,
  Min = 0.5,
  Max = 3,
  Rounding = 1,
  Compact = false,
 })

 HatSection:AddToggle("RainbowHat", {
  Text = "Радужная шляпа",
  Default = false,
 })

 local hatModel = nil
 local hatParts = {}
 local hatConn = nil
 local hatMeshPart = nil
 local hatMesh = nil

 local hatMeshIds = {
  ["Шляпа Волшебника"] = "rbxassetid://716245457",
  ["Шляпа Ковбоя"] = "rbxassetid://1029586",
  ["Шлем Рыцаря"] = "rbxassetid://8570385",
 }
 local hatYOffsets = {
  ["Шляпа Волшебника"] = 1.1,
  ["Шляпа Ковбоя"] = 0.7,
  ["Шлем Рыцаря"] = 0.2,
 }
 local hatZOffsets = {
  ["Шляпа Волшебника"] = 0.2,
  ["Шляпа Ковбоя"] = 0,
  ["Шлем Рыцаря"] = 0,
 }
 local hatMeshAsset = nil

 local function clearHat()
  if hatConn then hatConn:Disconnect(); hatConn = nil end
  if hatModel then pcall(function() hatModel:Destroy() end); hatModel = nil end
  if hatMeshPart then pcall(function() hatMeshPart:Destroy() end); hatMeshPart = nil end
  hatMesh = nil
  hatParts = {}
 end

 local function addHatPart(className, size, color)
  local p = Instance.new(className or "Part")
  p.Name = "OblivionHatPart"
  p.Anchored = true
  p.CanCollide = false
  p.CanTouch = false
  p.CanQuery = false
  p.CastShadow = false
  p.Material = Enum.Material.SmoothPlastic
  p.Color = color
  p.Size = size
  p.Parent = hatModel
  table.insert(hatParts, p)
  return p
 end

 local function createHat()
  clearHat()
  local char = LocalPlayer.Character
  local head = char and char:FindFirstChild("Head")
  if not head then return end
  local typ = (Options.HatType and Options.HatType.Value) or "Китайская шляпа"
  local s = (Options.HatSize and Options.HatSize.Value) or 1
  local col = (Options.HatColor and Options.HatColor.Value) or Color3.fromRGB(255, 50, 50)

  local meshId = hatMeshIds[typ]
  if meshId then
   hatMeshPart = Instance.new("Part")
   hatMeshPart.Name = "OblivionHatMesh"
   hatMeshPart.Anchored = true
   hatMeshPart.CanCollide = false
   hatMeshPart.CanTouch = false
   hatMeshPart.CanQuery = false
   hatMeshPart.CastShadow = false
   hatMeshPart.Massless = true
   hatMeshPart.Transparency = 0
   hatMeshPart.Material = Enum.Material.SmoothPlastic
   hatMeshPart.Color = col
   hatMeshPart.Size = Vector3.new(0.1, 0.1, 0.1)
   hatMeshPart.Parent = workspace
   hatMesh = Instance.new("SpecialMesh")
   hatMesh.MeshType = Enum.MeshType.FileMesh
   hatMesh.MeshId = meshId
   hatMesh.Scale = Vector3.new(s, s, s)
   hatMesh.Parent = hatMeshPart
  else
   hatModel = Instance.new("Model")
   hatModel.Name = "OblivionHat"
   hatModel.Parent = workspace
   if typ == "Китайская шляпа" then
    for i = 1, 16 do
     addHatPart("WedgePart", Vector3.new(0.34*s, 0.22*s, 1.55*s), col)
    end
    addHatPart("Part", Vector3.new(0.08*s, 0.9*s, 0.08*s), Color3.fromRGB(40, 24, 10))
   elseif typ == "Корона" then
    addHatPart("Part", Vector3.new(1.55*s, 0.18*s, 1.55*s), col)
    for i = 1, 12 do addHatPart("WedgePart", Vector3.new(0.20*s, 0.58*s, 0.28*s), col) end
    for i = 1, 12 do addHatPart("Part", Vector3.new(0.16*s, 0.16*s, 0.16*s), Color3.fromHSV((i-1)/12, 0.9, 1)) end
    addHatPart("Part", Vector3.new(0.38*s, 0.38*s, 0.38*s), Color3.fromRGB(255, 240, 90))
   elseif typ == "Пропеллер" then
    addHatPart("Part", Vector3.new(0.3*s,0.3*s,0.3*s), col).Shape = Enum.PartType.Ball
    addHatPart("Part", Vector3.new(1.8*s,0.08*s,0.35*s), col)
    addHatPart("Part", Vector3.new(0.35*s,0.08*s,1.8*s), col)
   elseif typ == "Уши кота" then
    addHatPart("WedgePart", Vector3.new(0.5*s,0.65*s,0.22*s), col)
    addHatPart("WedgePart", Vector3.new(0.5*s,0.65*s,0.22*s), col)
   elseif typ == "Рога Демона" then
    for i = 1, 6 do addHatPart("WedgePart", Vector3.new(0.18*s, 0.35*s, 0.6*s), col) end
    for i = 1, 6 do addHatPart("WedgePart", Vector3.new(0.18*s, 0.35*s, 0.6*s), col) end
   end
  end

  local spin = 0
  hatConn = RunService.RenderStepped:Connect(function(dt)
   local c = LocalPlayer.Character
   local h = c and c:FindFirstChild("Head")
   if not h then return end
   typ = (Options.HatType and Options.HatType.Value) or typ
   s = (Options.HatSize and Options.HatSize.Value) or s
   col = (Options.HatColor and Options.HatColor.Value) or col
   if Toggles.RainbowHat and Toggles.RainbowHat.Value then
    col = Color3.fromHSV((tick() % 5) / 5, 1, 1)
   end
   local cam = workspace.CurrentCamera
   local fp = cam and (cam.CFrame.Position - h.Position).Magnitude < 1.2
   local tr = fp and 1 or 0
   spin = spin + dt * 8

   if hatMeshPart and hatMesh then
    local yOff = hatYOffsets[typ] or 0
    local zOff = hatZOffsets[typ] or 0
    hatMeshPart.CFrame = h.CFrame * CFrame.new(0, yOff, zOff)
    hatMeshPart.Color = col
    hatMesh.Scale = Vector3.new(s, s, s)
    hatMeshPart.Transparency = fp and 1 or 0
    return
   end

   if not hatModel then return end

   if typ == "Китайская шляпа" and #hatParts >= 17 then
    for i = 1, 16 do
     local a = math.rad((i-1) * 22.5)
     local p = hatParts[i]
     p.Size = Vector3.new(0.34*s, 0.22*s, 1.55*s)
     p.CFrame = h.CFrame * CFrame.new(math.cos(a)*0.70*s, 0.72*s, math.sin(a)*0.70*s)
      * CFrame.Angles(0, -a, math.rad(-18))
     p.Color = col
    end
    hatParts[17].Size = Vector3.new(0.08*s, 0.9*s, 0.08*s)
    hatParts[17].CFrame = h.CFrame * CFrame.new(0, 0.18*s, 0)
    hatParts[17].Color = Color3.fromRGB(40, 24, 10)
   elseif typ == "Корона" and #hatParts >= 26 then
    hatParts[1].Size = Vector3.new(1.55*s, 0.18*s, 1.55*s)
    hatParts[1].CFrame = h.CFrame * CFrame.new(0, 0.76*s, 0)
    hatParts[1].Color = col
    for i = 1, 12 do
     local a = math.rad((i-1) * 30)
     local spike = hatParts[i+1]
     spike.Size = Vector3.new(0.20*s, 0.58*s, 0.28*s)
     spike.CFrame = h.CFrame * CFrame.new(math.cos(a)*0.62*s, 1.05*s, math.sin(a)*0.62*s)
      * CFrame.Angles(0, -a, math.rad(10))
     spike.Color = col
     local gem = hatParts[i+13]
     gem.Size = Vector3.new(0.16*s, 0.16*s, 0.16*s)
     gem.CFrame = h.CFrame * CFrame.new(math.cos(a)*0.65*s, 1.38*s, math.sin(a)*0.65*s)
     gem.Color = Color3.fromHSV((i-1)/12, 0.9, 1)
     gem.Material = Enum.Material.Neon
    end
    hatParts[26].Size = Vector3.new(0.38*s, 0.38*s, 0.38*s)
    hatParts[26].CFrame = h.CFrame * CFrame.new(0, 1.50*s, 0)
    hatParts[26].Color = Color3.fromRGB(255, 240, 90)
    hatParts[26].Material = Enum.Material.Neon
   elseif typ == "Пропеллер" and #hatParts >= 3 then
    hatParts[1].CFrame = h.CFrame * CFrame.new(0, 0.88*s, 0)
    hatParts[2].CFrame = h.CFrame * CFrame.new(0, 1.08*s, 0) * CFrame.Angles(0, spin, 0)
    hatParts[3].CFrame = h.CFrame * CFrame.new(0, 1.08*s, 0) * CFrame.Angles(0, spin, 0)
    for _, p in ipairs(hatParts) do p.Color = col end
   elseif typ == "Уши кота" and #hatParts >= 2 then
    hatParts[1].CFrame = h.CFrame * CFrame.new(-0.43*s, 0.86*s, 0) * CFrame.Angles(0, 0, math.rad(-20))
    hatParts[2].CFrame = h.CFrame * CFrame.new(0.43*s, 0.86*s, 0) * CFrame.Angles(0, 0, math.rad(20))
    for _, p in ipairs(hatParts) do p.Color = col end
   elseif typ == "Рога Демона" and #hatParts >= 12 then
    for i = 1, 6 do
     local p = hatParts[i]
     local h_frac = (i-1) / 5
     local y = 0.8 + h_frac * 1.2
     p.Size = Vector3.new(0.18*s, 0.35*s, 0.6*s)
     p.CFrame = h.CFrame * CFrame.new(-0.4*s - h_frac*0.15*s, y*s, 0)
      * CFrame.Angles(math.rad(20 + h_frac*30), 0, math.rad(-15 - h_frac*10))
     p.Color = col
    end
    for i = 7, 12 do
     local j = i - 6
     local h_frac = (j-1) / 5
     local y = 0.8 + h_frac * 1.2
     p = hatParts[i]
     p.Size = Vector3.new(0.18*s, 0.35*s, 0.6*s)
     p.CFrame = h.CFrame * CFrame.new(0.4*s + h_frac*0.15*s, y*s, 0)
      * CFrame.Angles(math.rad(20 + h_frac*30), 0, math.rad(15 + h_frac*10))
     p.Color = col
    end
   end
   for _, p in ipairs(hatParts) do
    p.Transparency = fp and 1 or 0
   end
  end)
 end

 Toggles.EnableHat:OnChanged(function()
  if Toggles.EnableHat.Value then createHat() else clearHat() end
 end)
 if Options.HatType then Options.HatType:OnChanged(function() if Toggles.EnableHat and Toggles.EnableHat.Value then createHat() end end) end
 if Options.HatSize then Options.HatSize:OnChanged(function() if Toggles.EnableHat and Toggles.EnableHat.Value then createHat() end end) end
 LocalPlayer.CharacterAdded:Connect(function()
  task.wait(0.8)
  if Toggles.EnableHat and Toggles.EnableHat.Value then createHat() end
 end)
end


-- === SAFE ADDON: PLAYER EFFECTS VISUALS (Jump + Walk Trail) ===
do
 local PlayerEffects = Tabs.Visual:AddLeftGroupbox("Player Effects")

 PlayerEffects:AddToggle("EnableJumpEffect", {
  Text = "Jump Effect",
  Default = false,
  Tooltip = "Рисует эффект при прыжке",
 })

 PlayerEffects:AddDropdown("JumpEffectType", {
  Text = "Тип прыжка",
  Values = {"Star Burst", "Smoke Ring", "Sparkle"},
  Default = "Smoke Ring",
  Multi = false,
 })

 PlayerEffects:AddSlider("JumpEffectRadius", {
  Text = "Радиус",
  Default = 25,
  Min = 5,
  Max = 100,
  Rounding = 1,
  Compact = false,
 })

 PlayerEffects:AddSlider("JumpEffectDuration", {
  Text = "Длительность",
  Default = 0.8,
  Min = 0.1,
  Max = 3,
  Rounding = 2,
  Compact = false,
 })

 PlayerEffects:AddLabel("Цвет прыжка"):AddColorPicker("JumpEffectColor", {
  Default = Color3.fromRGB(0, 255, 255),
  Title = "Цвет прыжка",
 })

 PlayerEffects:AddToggle("EnableWalkTrail", {
  Text = "Walk Trail",
  Default = false,
  Tooltip = "Рисует след за спиной при ходьбе",
 })

 PlayerEffects:AddDropdown("WalkTrailType", {
  Text = "Тип следа",
  Values = {"Светящаяся Нить", "Кристаллы", "Shadow Smoke"},
  Default = "Светящаяся Нить",
  Multi = false,
 })

 PlayerEffects:AddSlider("WalkTrailLength", {
  Text = "Длина следа",
  Default = 200,
  Min = 10,
  Max = 1000,
  Rounding = 0,
  Compact = false,
 })

 PlayerEffects:AddLabel("Цвет следа"):AddColorPicker("WalkTrailColor", {
  Default = Color3.fromRGB(255, 0, 255),
  Title = "Цвет следа",
 })

 local jumpEffectObjects = {}
 local walkTrailCustomParts = {}
 local walkTrailAttach0 = nil
 local walkTrailAttach1 = nil
 local walkTrail = nil
 local walkTrailCore = nil
 local walkTrailEmitter = nil

 local function cleanupJumpEffects()
  for _, obj in ipairs(jumpEffectObjects) do
   pcall(function() obj:Destroy() end)
  end
  jumpEffectObjects = {}
 end

 local function cleanupWalkTrail()
  for _, p in ipairs(walkTrailCustomParts) do
   pcall(function() p:Destroy() end)
  end
  walkTrailCustomParts = {}
  if walkTrail then pcall(function() walkTrail:Destroy() end); walkTrail = nil end
  if walkTrailCore then pcall(function() walkTrailCore:Destroy() end); walkTrailCore = nil end
  if walkTrailEmitter then pcall(function() walkTrailEmitter:Destroy() end); walkTrailEmitter = nil end
  if walkTrailAttach0 then pcall(function() walkTrailAttach0:Destroy() end); walkTrailAttach0 = nil end
  if walkTrailAttach1 then pcall(function() walkTrailAttach1:Destroy() end); walkTrailAttach1 = nil end
 end

 local function createJumpEffect()
  if not (Toggles.EnableJumpEffect and Toggles.EnableJumpEffect.Value) then return end
  local char = LocalPlayer.Character
  local root = char and char:FindFirstChild("HumanoidRootPart")
  if not root then return end
  local typ = Options.JumpEffectType and Options.JumpEffectType.Value or "Smoke Ring"
  local radius = Options.JumpEffectRadius and Options.JumpEffectRadius.Value or 25
  local duration = Options.JumpEffectDuration and Options.JumpEffectDuration.Value or 0.8
  local color = Options.JumpEffectColor and Options.JumpEffectColor.Value or Color3.fromRGB(0, 255, 255)
  local pos = root.Position - Vector3.new(0, 2.9, 0)

  if typ == "Smoke Ring" then
   local part = Instance.new("Part")
   part.Anchored = true
   part.CanCollide = false
   part.CanQuery = false
   part.CanTouch = false
   part.Transparency = 1
   part.Size = Vector3.new(0.1, 0.1, 0.1)
   part.CFrame = CFrame.new(pos)
   part.Parent = workspace
   table.insert(jumpEffectObjects, part)
   local emitter = Instance.new("ParticleEmitter")
   emitter.Texture = "rbxassetid://7083340510"
   emitter.Color = ColorSequence.new(color)
   emitter.Size = NumberSequence.new({
    NumberSequenceKeypoint.new(0, radius * 0.1),
    NumberSequenceKeypoint.new(0.5, radius * 0.2),
    NumberSequenceKeypoint.new(1, 0)
   })
   emitter.Transparency = NumberSequence.new({
    NumberSequenceKeypoint.new(0, 0),
    NumberSequenceKeypoint.new(0.7, 0.3),
    NumberSequenceKeypoint.new(1, 1)
   })
   emitter.Lifetime = NumberRange.new(duration, duration)
   emitter.Rate = 0
   emitter.Rotation = NumberRange.new(0, 360)
   emitter.RotSpeed = NumberRange.new(-90, 90)
   emitter.Speed = NumberRange.new(0, 0)
   emitter.LightEmission = 1
   emitter.Parent = part
   emitter:Emit(30)
   task.delay(duration + 0.5, function()
    pcall(function() part:Destroy() end)
   end)
  elseif typ == "Star Burst" then
   local part = Instance.new("Part")
   part.Anchored = true
   part.CanCollide = false
   part.CanQuery = false
   part.CanTouch = false
   part.Transparency = 1
   part.Size = Vector3.new(0.1, 0.1, 0.1)
   part.CFrame = CFrame.new(pos)
   part.Parent = workspace
   table.insert(jumpEffectObjects, part)
   local emitter = Instance.new("ParticleEmitter")
   emitter.Texture = "rbxassetid://16857504782"
   emitter.Color = ColorSequence.new(color)
   emitter.Size = NumberSequence.new({
    NumberSequenceKeypoint.new(0, radius * 0.15),
    NumberSequenceKeypoint.new(1, 0)
   })
   emitter.Transparency = NumberSequence.new({
    NumberSequenceKeypoint.new(0, 0),
    NumberSequenceKeypoint.new(1, 1)
   })
   emitter.Lifetime = NumberRange.new(duration * 0.5, duration)
   emitter.Rate = 0
   emitter.Rotation = NumberRange.new(0, 360)
   emitter.RotSpeed = NumberRange.new(-180, 180)
   emitter.Speed = NumberRange.new(radius * 2, radius * 4)
   emitter.SpreadAngle = Vector2.new(180, 180)
   emitter.LightEmission = 1
   emitter.Acceleration = Vector3.new(0, -10, 0)
   emitter.Parent = part
   emitter:Emit(50)
   task.delay(duration + 1, function()
    pcall(function() part:Destroy() end)
   end)
  else
   local part = Instance.new("Part")
   part.Anchored = true
   part.CanCollide = false
   part.CanQuery = false
   part.CanTouch = false
   part.Transparency = 1
   part.Size = Vector3.new(0.1, 0.1, 0.1)
   part.CFrame = CFrame.new(pos + Vector3.new(0, 1, 0))
   part.Parent = workspace
   table.insert(jumpEffectObjects, part)
   local light = Instance.new("PointLight")
   light.Color = color
   light.Range = radius * 3
   light.Brightness = 20
   light.Shadows = false
   light.Parent = part
   local emitter = Instance.new("ParticleEmitter")
   emitter.Texture = "rbxassetid://16857504782"
   emitter.Color = ColorSequence.new(color)
   emitter.Size = NumberSequence.new({
    NumberSequenceKeypoint.new(0, radius * 0.2),
    NumberSequenceKeypoint.new(1, 0)
   })
   emitter.Transparency = NumberSequence.new({
    NumberSequenceKeypoint.new(0, 0),
    NumberSequenceKeypoint.new(1, 1)
   })
   emitter.Lifetime = NumberRange.new(duration * 0.3, duration * 0.5)
   emitter.Rate = 0
   emitter.Rotation = NumberRange.new(0, 360)
   emitter.Speed = NumberRange.new(0, 0)
   emitter.LightEmission = 1
   emitter.Parent = part
   emitter:Emit(15)
   task.spawn(function()
    local t = 0
    while t < duration and part.Parent do
     t = t + task.wait()
     local alpha = t / duration
     light.Range = radius * 3 * (1 - alpha)
     light.Brightness = 20 * (1 - alpha)
    end
    pcall(function() part:Destroy() end)
   end)
  end
 end

 local function setupTrailLine()
  local char = LocalPlayer.Character
  local root = char and char:FindFirstChild("HumanoidRootPart")
  if not root then return end
  walkTrailAttach0 = Instance.new("Attachment")
  walkTrailAttach0.Position = Vector3.new(0, -2.5, 2.0)
  walkTrailAttach0.Parent = root
  walkTrailAttach1 = Instance.new("Attachment")
  walkTrailAttach1.Position = Vector3.new(0, -2.5, -2.0)
  walkTrailAttach1.Parent = root
  walkTrail = Instance.new("Trail")
  walkTrail.Name = "OblivionWalkTrail"
  walkTrail.Attachment0 = walkTrailAttach0
  walkTrail.Attachment1 = walkTrailAttach1
  walkTrail.Texture = ""
  walkTrail.LightEmission = 1
  walkTrail.LightInfluence = 0
  walkTrail.WidthScale = NumberSequence.new({
   NumberSequenceKeypoint.new(0, 8),
   NumberSequenceKeypoint.new(1, 0)
  })
  walkTrail.Parent = root
  walkTrailCore = Instance.new("Trail")
  walkTrailCore.Name = "OblivionWalkTrailCore"
  walkTrailCore.Attachment0 = walkTrailAttach0
  walkTrailCore.Attachment1 = walkTrailAttach1
  walkTrailCore.Texture = ""
  walkTrailCore.Color = ColorSequence.new(Color3.fromRGB(255, 255, 255))
  walkTrailCore.LightEmission = 1
  walkTrailCore.LightInfluence = 0
  walkTrailCore.WidthScale = NumberSequence.new({
   NumberSequenceKeypoint.new(0, 3),
   NumberSequenceKeypoint.new(1, 0)
  })
  walkTrailCore.Parent = root
 end

 local lastWalkPos = nil

 local function updateWalkTrail()
  if not (Toggles.EnableWalkTrail and Toggles.EnableWalkTrail.Value) then return end
  local char = LocalPlayer.Character
  local root = char and char:FindFirstChild("HumanoidRootPart")
  if not root then return end
  local typ = Options.WalkTrailType and Options.WalkTrailType.Value or "Светящаяся Нить"
  local color = Options.WalkTrailColor and Options.WalkTrailColor.Value or Color3.fromRGB(255, 0, 255)
  local length = Options.WalkTrailLength and Options.WalkTrailLength.Value or 200

  if typ == "Светящаяся Нить" then
   if not walkTrail then setupTrailLine() end
   if walkTrail then
    walkTrail.Color = ColorSequence.new(color)
    walkTrail.Lifetime = math.clamp(length / 2, 0.5, 40)
    walkTrail.Enabled = true
   end
   if walkTrailCore then
    walkTrailCore.Lifetime = walkTrail.Lifetime
    walkTrailCore.Enabled = true
   end
   return
  end

  if walkTrail then walkTrail.Enabled = false end
  if walkTrailCore then walkTrailCore.Enabled = false end

  if typ == "Shadow Smoke" then
   if not walkTrailEmitter then
    walkTrailEmitter = Instance.new("ParticleEmitter")
    walkTrailEmitter.Name = "OblivionSmokeTrail"
    walkTrailEmitter.Texture = "rbxassetid://7083340510"
    walkTrailEmitter.Color = ColorSequence.new(color, Color3.fromRGB(50, 50, 50))
    walkTrailEmitter.Size = NumberSequence.new({
     NumberSequenceKeypoint.new(0, 3),
     NumberSequenceKeypoint.new(1, 0)
    })
    walkTrailEmitter.Transparency = NumberSequence.new({
     NumberSequenceKeypoint.new(0, 0.3),
     NumberSequenceKeypoint.new(1, 1)
    })
    walkTrailEmitter.Lifetime = NumberRange.new(1, 2)
    walkTrailEmitter.Speed = NumberRange.new(1, 3)
    walkTrailEmitter.SpreadAngle = Vector2.new(30, 30)
    walkTrailEmitter.Rate = 40
    walkTrailEmitter.Rotation = NumberRange.new(0, 360)
    walkTrailEmitter.RotSpeed = NumberRange.new(-45, 45)
    walkTrailEmitter.LightEmission = 0.3
    walkTrailEmitter.Acceleration = Vector3.new(0, 3, 0)
    walkTrailEmitter.Parent = root
   end
   walkTrailEmitter.Color = ColorSequence.new(color, Color3.fromRGB(50, 50, 50))
   walkTrailEmitter.Enabled = true
   return
  end

  if walkTrailEmitter then walkTrailEmitter.Enabled = false end

  if typ == "Кристаллы" then
   local backPos = root.Position - Vector3.new(0, 3, 0)
   if lastWalkPos and (backPos - lastWalkPos).Magnitude < 1 then return end
   lastWalkPos = backPos
   local p = Instance.new("Part")
   p.Anchored = true
   p.CanCollide = false
   p.CanQuery = false
   p.CanTouch = false
   p.Material = Enum.Material.Neon
   p.Color = color
   p.Transparency = 0.2
   local height = math.random(20, 60) * 0.1
   p.Size = Vector3.new(0.3, height, 0.3)
   local offset = Vector3.new(math.random(-30, 30)*0.1, height/2, math.random(-30, 30)*0.1)
   p.CFrame = CFrame.new(backPos + offset) * CFrame.Angles(math.rad(math.random(-40, 40)), math.rad(math.random(0, 360)), math.rad(math.random(-20, 20)))
   p.Parent = workspace
   table.insert(walkTrailCustomParts, p)
   while #walkTrailCustomParts > length do
    local old = table.remove(walkTrailCustomParts, 1)
    pcall(function() old:Destroy() end)
   end
  end
 end

 Toggles.EnableJumpEffect:OnChanged(function()
  if not Toggles.EnableJumpEffect.Value then
   cleanupJumpEffects()
  end
 end)

 Toggles.EnableWalkTrail:OnChanged(function()
  if Toggles.EnableWalkTrail.Value then
   -- Initial setup will happen in update loop
  else
   cleanupWalkTrail()
  end
 end)

 if Options.WalkTrailType then
  Options.WalkTrailType:OnChanged(function()
   if Toggles.EnableWalkTrail and Toggles.EnableWalkTrail.Value and LocalPlayer.Character then
    cleanupWalkTrail()
   end
  end)
 end

 if Options.WalkTrailColor then
  Options.WalkTrailColor:OnChanged(function()
   if walkTrail and Toggles.EnableWalkTrail and Toggles.EnableWalkTrail.Value then
    walkTrail.Color = ColorSequence.new(Options.WalkTrailColor.Value)
   end
   if walkTrailEmitter and Toggles.EnableWalkTrail and Toggles.EnableWalkTrail.Value then
    walkTrailEmitter.Color = ColorSequence.new(Options.WalkTrailColor.Value, Color3.fromRGB(50, 50, 50))
   end
  end)
 end

 local function hookJumpEffect(char)
  local hum = char:FindFirstChildOfClass("Humanoid")
  if not hum then return end
  hum.StateChanged:Connect(function(_, newState)
   if newState == Enum.HumanoidStateType.Jumping then
    createJumpEffect()
   end
  end)
 end

 if LocalPlayer.Character then
  task.delay(0.5, function()
   hookJumpEffect(LocalPlayer.Character)
  end)
 end

 LocalPlayer.CharacterAdded:Connect(function(char)
  task.wait(0.5)
  cleanupJumpEffects()
  cleanupWalkTrail()
  hookJumpEffect(char)
 end)

 RunService.Heartbeat:Connect(function()
  updateWalkTrail()
 end)
end
palletColor = Color3.fromRGB(0, 255, 128)
palletColorData = {} -- [pallet] = { originals = {[part]=Color3}, lastStand = tick() }

VisualPallet:AddToggle("EnablePalletColor", {
 Text = "Цвет палетки под ногами",
 Default = false,
 Tooltip = "Красит палетку, на которой стоишь; сброс через N сек после схода",
}):AddColorPicker("PalletColor", {
 Default = Color3.fromRGB(0, 255, 128),
 Title = "Цвет палетки",
 Callback = function(Value)
 palletColor = Value
 end,
})

VisualPallet:AddSlider("PalletResetDelay", {
 Text = "Сброс через (сек)",
 Default = 3,
 Min = 0,
 Max = 10,
 Rounding = 1,
 Compact = false,
 Tooltip = "Через сколько секунд после схода палетка вернёт исходный цвет",
})

VisualPallet:AddSlider("PalletTransparency", {
 Text = "Прозрачность",
 Default = 0.5,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 — непрозрачная, 1 — полностью невидимая",
})

VisualPallet:AddSlider("PalletGlow", {
 Text = "Подсветка",
 Default = 0,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 ��� без подсветки, 1 — максимальное свечение со всех сторон",
})

VisualPallet:AddSlider("PalletSmoothness", {
 Text = "Плавность появления",
 Default = 0,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 — цвет появляется сразу; 1 — очень плавное появление",
})

-- Ищем палетку среди предков детали (имя содержит "pallet")
local function getPalletFromPart(part)
 local cur = part
 while cur and cur ~= workspace do
 if string.find(string.lower(cur.Name), "pallet") then
 return cur
 end
 cur = cur.Parent
 end
 return nil
end

-- Собираем все красимые детали палетки
local function getPalletParts(pallet)
 local parts = {}
 if pallet:IsA("BasePart") then
 table.insert(parts, pallet)
 end
 for _, d in ipairs(pallet:GetDescendants()) do
 if d:IsA("BasePart") then
 table.insert(parts, d)
 end
 end
 return parts
end

local function restorePallet(data)
 for part, orig in pairs(data.originals) do
 if part and part.Parent then
 pcall(function()
 part.Color = orig.color
 part.Transparency = orig.transparency
 local light = part:FindFirstChild("OblivionPalletLight")
 if light then light:Destroy() end
 if part.Material == Enum.Material.Neon then
 part.Material = Enum.Material.SmoothPlastic
 end
 end)
 end
 end
 if data.boxes then
 for p, box in pairs(data.boxes) do
 pcall(function() box:Destroy() end)
 data.boxes[p] = nil
 end
 end
 if data.highlight then
 pcall(function() data.highlight:Destroy() end)
 data.highlight = nil
 end
end

local function restoreAllPallets()
 for _, data in pairs(palletColorData) do
 restorePallet(data)
 end
 palletColorData = {}
end

Toggles.EnablePalletColor:OnChanged(function()
 if not Toggles.EnablePalletColor.Value then
 restoreAllPallets()
 end
end)

-- ==============================================
-- VISUAL: Input Overlay (WASD + Mouse icon)
-- ==============================================
inputOverlayGui = nil
inputOverlayKeys = {}
inputOverlayMouse = {}
inputOverlayConn = nil

local function createInputOverlay()
 if inputOverlayGui then return end
 local sg = Instance.new("ScreenGui")
 sg.Name = "InputOverlay"
 sg.ResetOnSpawn = false
 sg.IgnoreGuiInset = true
 sg.DisplayOrder = 9999
 sg.Parent = game:GetService("CoreGui")
 local container = Instance.new("Frame")
 container.Name = "Container"
 container.Size = UDim2.new(0, 230, 0, 110)
 container.Position = UDim2.new(0, 20, 1, -130)
 container.BackgroundTransparency = 1
 container.Parent = sg
 inputOverlayGui = sg
 local keySize = 35
 local keyGap = 4
 local keyStartX = 0
 local keyStartY = 35
 local keyLayout = {
 {key = "W", x = keyStartX + keySize + keyGap, y = keyStartY},
 {key = "A", x = keyStartX, y = keyStartY + keySize + keyGap},
 {key = "S", x = keyStartX + keySize + keyGap, y = keyStartY + keySize + keyGap},
 {key = "D", x = keyStartX + (keySize + keyGap) * 2, y = keyStartY + keySize + keyGap},
 }
 for _, layout in ipairs(keyLayout) do
 local frame = Instance.new("Frame")
 frame.Name = "Key_" .. layout.key
 frame.Size = UDim2.new(0, keySize, 0, keySize)
 frame.Position = UDim2.new(0, layout.x, 0, layout.y)
 frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
 frame.BackgroundTransparency = 0.3
 frame.BorderSizePixel = 1
 frame.BorderColor3 = Color3.fromRGB(60, 60, 60)
 frame.Parent = container
 local corner = Instance.new("UICorner")
 corner.CornerRadius = UDim.new(0, 6)
 corner.Parent = frame
 local label = Instance.new("TextLabel")
 label.Size = UDim2.new(1, 0, 1, 0)
 label.BackgroundTransparency = 1
 label.Text = layout.key
 label.TextColor3 = Color3.fromRGB(200, 200, 200)
 label.Font = Enum.Font.GothamBold
 label.TextSize = 16
 label.Parent = frame
 inputOverlayKeys[layout.key] = frame
 end
 local mouseX = keyStartX + (keySize + keyGap) * 3 + 18
 local mouseY = keyStartY - 2
 local mouseBody = Instance.new("Frame")
 mouseBody.Name = "MouseBody"
 mouseBody.Size = UDim2.new(0, 40, 0, 72)
 mouseBody.Position = UDim2.new(0, mouseX, 0, mouseY)
 mouseBody.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
 mouseBody.BackgroundTransparency = 0.1
 mouseBody.BorderSizePixel = 0
 mouseBody.Parent = container
 local mbCorner = Instance.new("UICorner")
 mbCorner.CornerRadius = UDim.new(0, 12)
 mbCorner.Parent = mouseBody
 local mbStroke = Instance.new("UIStroke")
 mbStroke.Color = Color3.fromRGB(80, 80, 90)
 mbStroke.Thickness = 1.5
 mbStroke.Transparency = 0.15
 mbStroke.Parent = mouseBody
 local mbGrad = Instance.new("UIGradient")
 mbGrad.Rotation = 90
 mbGrad.Color = ColorSequence.new({
 ColorSequenceKeypoint.new(0, Color3.fromRGB(50, 50, 58)),
 ColorSequenceKeypoint.new(0.5, Color3.fromRGB(38, 38, 44)),
 ColorSequenceKeypoint.new(1, Color3.fromRGB(28, 28, 33)),
 })
 mbGrad.Parent = mouseBody
 local splitLine = Instance.new("Frame")
 splitLine.Name = "SplitLine"
 splitLine.Size = UDim2.new(0, 1, 0, 32)
 splitLine.Position = UDim2.new(0, 19.5, 0, 3)
 splitLine.BackgroundColor3 = Color3.fromRGB(70, 70, 80)
 splitLine.BackgroundTransparency = 0.3
 splitLine.BorderSizePixel = 0
 splitLine.Parent = mouseBody
 local mouseLeft = Instance.new("Frame")
 mouseLeft.Name = "MouseLeft"
 mouseLeft.Size = UDim2.new(0, 17, 0, 32)
 mouseLeft.Position = UDim2.new(0, 2, 0, 2)
 mouseLeft.BackgroundColor3 = Color3.fromRGB(45, 45, 52)
 mouseLeft.BackgroundTransparency = 0.05
 mouseLeft.BorderSizePixel = 0
 mouseLeft.Parent = mouseBody
 local mlCorner = Instance.new("UICorner")
 mlCorner.CornerRadius = UDim.new(0, 8)
 mlCorner.Parent = mouseLeft
 local mlStroke = Instance.new("UIStroke")
 mlStroke.Color = Color3.fromRGB(65, 65, 75)
 mlStroke.Thickness = 1
 mlStroke.Transparency = 0.25
 mlStroke.Parent = mouseLeft
 local mouseRight = Instance.new("Frame")
 mouseRight.Name = "MouseRight"
 mouseRight.Size = UDim2.new(0, 17, 0, 32)
 mouseRight.Position = UDim2.new(0, 21, 0, 2)
 mouseRight.BackgroundColor3 = Color3.fromRGB(45, 45, 52)
 mouseRight.BackgroundTransparency = 0.05
 mouseRight.BorderSizePixel = 0
 mouseRight.Parent = mouseBody
 local mrCorner = Instance.new("UICorner")
 mrCorner.CornerRadius = UDim.new(0, 8)
 mrCorner.Parent = mouseRight
 local mrStroke = Instance.new("UIStroke")
 mrStroke.Color = Color3.fromRGB(65, 65, 75)
 mrStroke.Thickness = 1
 mrStroke.Transparency = 0.25
 mrStroke.Parent = mouseRight
 inputOverlayMouse.Left = mouseLeft
 inputOverlayMouse.Right = mouseRight
 inputOverlayMouse.LeftStroke = mlStroke
 inputOverlayMouse.RightStroke = mrStroke
end

local function destroyInputOverlay()
 if inputOverlayGui and inputOverlayGui.Parent then inputOverlayGui:Destroy() end
 inputOverlayGui = nil
 inputOverlayKeys = {}
 inputOverlayMouse = {}
end

local function startInputOverlayTracking()
 if inputOverlayConn then return end
 local UIS = game:GetService("UserInputService")
 inputOverlayConn = RunService.RenderStepped:Connect(function()
 if not inputOverlayGui then return end
 for key, frame in pairs(inputOverlayKeys) do
 local pressed = false
 if key == "W" then pressed = UIS:IsKeyDown(Enum.KeyCode.W) end
 if key == "A" then pressed = UIS:IsKeyDown(Enum.KeyCode.A) end
 if key == "S" then pressed = UIS:IsKeyDown(Enum.KeyCode.S) end
 if key == "D" then pressed = UIS:IsKeyDown(Enum.KeyCode.D) end
 if pressed then
 frame.BackgroundColor3 = Color3.fromRGB(100, 200, 100)
 frame.BackgroundTransparency = 0.1
 else
 frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
 frame.BackgroundTransparency = 0.3
 end
 end
 if inputOverlayMouse.Left then
 if UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
 inputOverlayMouse.Left.BackgroundColor3 = Color3.fromRGB(80, 140, 255)
 inputOverlayMouse.Left.BackgroundTransparency = 0
 if inputOverlayMouse.LeftStroke then
 inputOverlayMouse.LeftStroke.Color = Color3.fromRGB(120, 180, 255)
 inputOverlayMouse.LeftStroke.Transparency = 0
 end
 else
 inputOverlayMouse.Left.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
 inputOverlayMouse.Left.BackgroundTransparency = 0.1
 if inputOverlayMouse.LeftStroke then
 inputOverlayMouse.LeftStroke.Color = Color3.fromRGB(60, 60, 70)
 inputOverlayMouse.LeftStroke.Transparency = 0.3
 end
 end
 end
 if inputOverlayMouse.Right then
 if UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
 inputOverlayMouse.Right.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
 inputOverlayMouse.Right.BackgroundTransparency = 0
 if inputOverlayMouse.RightStroke then
 inputOverlayMouse.RightStroke.Color = Color3.fromRGB(255, 140, 140)
 inputOverlayMouse.RightStroke.Transparency = 0
 end
 else
 inputOverlayMouse.Right.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
 inputOverlayMouse.Right.BackgroundTransparency = 0.1
 if inputOverlayMouse.RightStroke then
 inputOverlayMouse.RightStroke.Color = Color3.fromRGB(60, 60, 70)
 inputOverlayMouse.RightStroke.Transparency = 0.3
 end
 end
 end
 end)
end

local function stopInputOverlayTracking()
 if inputOverlayConn then inputOverlayConn:Disconnect() inputOverlayConn = nil end
end

VisualPallet:AddToggle("EnableInputOverlay", {
 Text = "Input Overlay",
 Default = false,
 Tooltip = "Shows WASD + mouse clicks at bottom left",
}):OnChanged(function()
 if Toggles.EnableInputOverlay.Value then
 createInputOverlay()
 startInputOverlayTracking()
 else
 stopInputOverlayTracking()
 destroyInputOverlay()
 end
end)

RunService.Heartbeat:Connect(function()
 if not (Toggles.EnablePalletColor and Toggles.EnablePalletColor.Value) then
 return
 end
 local char = LocalPlayer.Character
 local root = char and char:FindFirstChild("HumanoidRootPart")
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 local now = tick()
 local delay = Options.PalletResetDelay and Options.PalletResetDelay.Value or 3

 -- На какой палетке стоим прямо сейчас
 local standingPallet = nil
 if root and hum and hum.Health > 0 and hum.FloorMaterial ~= Enum.Material.Air then
 local params = RaycastParams.new()
 params.FilterType = Enum.RaycastFilterType.Exclude
 params.FilterDescendantsInstances = { char }
 local result = workspace:Raycast(root.Position, Vector3.new(0, -10, 0), params)
 if result then
 standingPallet = getPalletFromPart(result.Instance)
 end
 end

 -- Стоим на палетке → красим и обновляем время
 if standingPallet then
 local data = palletColorData[standingPallet]
 if not data then
 data = { originals = {}, lastStand = now }
 for _, part in ipairs(getPalletParts(standingPallet)) do
 data.originals[part] = { color = part.Color, transparency = part.Transparency }
 end
 palletColorData[standingPallet] = data
 end
 data.lastStand = now
 local wantT = (Options.PalletTransparency and Options.PalletTransparency.Value) or 0
 local smooth = (Options.PalletSmoothness and Options.PalletSmoothness.Value) or 0

 for part in pairs(data.originals) do
 if part and part.Parent then
 if smooth > 0 then
 -- Используем Lerp в Heartbeat: чем меньше smooth, тем быстрее покраска.
 -- Если smooth = 1, альфа около 0.05. Если smooth близко к 0, альфа стремится к 1.
 local dt = 1/60 -- приблизительный шаг кадра
 local alpha = math.clamp(dt / math.max(0.05, smooth), 0.01, 1)
 if part.Color ~= palletColor then
 part.Color = part.Color:Lerp(palletColor, alpha)
 end
 if math.abs(part.Transparency - wantT) > 0.01 then
 part.Transparency = part.Transparency + (wantT - part.Transparency) * alpha
 end
 else
 if part.Color ~= palletColor then part.Color = palletColor end
 if part.Transparency ~= wantT then part.Transparency = wantT end
 end
 local glow = (Options.PalletGlow and Options.PalletGlow.Value) or 0
 local light = part:FindFirstChild("OblivionPalletLight")
 if glow > 0 then
 if glow >= 0.55 then part.Material = Enum.Material.Neon else part.Material = Enum.Material.SmoothPlastic end
 part.Color = palletColor:Lerp(Color3.new(1, 1, 1), glow * 0.35)
 if not light then
 light = Instance.new("PointLight")
 light.Name = "OblivionPalletLight"
 light.Shadows = false
 light.Parent = part
 end
 light.Enabled = true
 light.Color = palletColor
 light.Brightness = 0.2 + glow * 7
 light.Range = 3 + glow * 20
 else
 if light then light.Enabled = false end
 if part.Material == Enum.Material.Neon then part.Material = Enum.Material.SmoothPlastic end
 end
 end
 end
 -- Старую SelectionBox-подсветку убрал: теперь ползунок PalletGlow управляет только PointLight/Neon.
 end

 -- Сбрасываем цвет у палеток, с которых сошли дольше delay секунд назад
 for pallet, data in pairs(palletColorData) do
 if not pallet.Parent then
 palletColorData[pallet] = nil
 elseif pallet ~= standingPallet and now - data.lastStand >= delay then
 restorePallet(data)
 palletColorData[pallet] = nil
 end
 end
end)

-- ==============================================
-- Вкладка: VISUAL — XOCO SKIN
-- ==============================================
do
local XocoSkinSection = Tabs.Visual:AddLeftGroupbox("Custom Skin")

local skinConn = nil
local skinOriginals = {}

local function getXocoColor()
 if Options.CustomSkinColor and Options.CustomSkinColor.Value then
  return Options.CustomSkinColor.Value
 end
 return Color3.fromRGB(255, 255, 255)
end

local function getXocoMaterial()
 local name = Options.CustomSkinMaterial and Options.CustomSkinMaterial.Value or "Default"
 if name == "Default" then return nil end
 local ok, mat = pcall(function() return Enum.Material[name] end)
 if ok then return mat end
 return nil
end

local function applyXocoSkin()
 local char = LocalPlayer.Character
 if not char then return end
 local color = getXocoColor()
 local mat = getXocoMaterial()
 for _, part in ipairs(char:GetDescendants()) do
  if part:IsA("Clothing") or part:IsA("ShirtGraphic") or part:IsA("Decal") then
   if not skinOriginals[part] then
    skinOriginals[part] = { Type = "Clothing", Parent = part.Parent }
   end
   pcall(function() part.Parent = game:GetService("Lighting") end)
  elseif part:IsA("BasePart") then
   if not skinOriginals[part] then
    local tex = part:IsA("MeshPart") and part.TextureID or nil
    skinOriginals[part] = { Type = "BasePart", Material = part.Material, Color = part.Color, Transparency = part.Transparency, TextureID = tex }
   end
   pcall(function()
    part.Color = color
    if mat then part.Material = mat end
    if part:IsA("MeshPart") then part.TextureID = "" end
   end)
  elseif part:IsA("SpecialMesh") then
   if not skinOriginals[part] then
    skinOriginals[part] = { Type = "SpecialMesh", TextureId = part.TextureId }
   end
   pcall(function() part.TextureId = "" end)
  end
 end
end

XocoSkinSection:AddToggle("EnableCustomSkin", {
 Text = "Custom Skin",
 Default = false,
 Tooltip = "Красит твою модель игрока в выбранный цвет",
 Callback = function(Value)
  if Value then
   if not skinConn then
    skinConn = RunService.Heartbeat:Connect(function()
     applyXocoSkin()
    end)
   end
  else
   if skinConn then
    skinConn:Disconnect()
    skinConn = nil
   end
   for part, orig in pairs(skinOriginals) do
    if part then
     if orig.Type == "Clothing" and orig.Parent then
      pcall(function() part.Parent = orig.Parent end)
     elseif orig.Type == "BasePart" and part.Parent then
      pcall(function()
       part.Material = orig.Material
       part.Color = orig.Color
       part.Transparency = orig.Transparency
       if part:IsA("MeshPart") and orig.TextureID then part.TextureID = orig.TextureID end
      end)
     elseif orig.Type == "SpecialMesh" and part.Parent then
      pcall(function() part.TextureId = orig.TextureId end)
     end
    end
   end
   skinOriginals = {}
  end
 end
})

XocoSkinSection:AddLabel("Paint Color"):AddColorPicker("CustomSkinColor", {
 Default = Color3.fromRGB(255, 255, 255),
 Title = "Цвет краски",
})

XocoSkinSection:AddDropdown("CustomSkinMaterial", {
 Text = "Material",
 Values = {"Default", "ForceField", "Neon", "Glass", "Foil", "Ice", "CorrodedMetal", "DiamondPlate", "Wood", "WoodPlanks", "Marble", "Granite", "Slate", "Brick", "Fabric", "Sand", "Plastic", "SmoothPlastic", "Cobblestone", "Grass"},
 Default = "Default",
 Tooltip = "Материал частей тела",
})

LocalPlayer.CharacterAdded:Connect(function(char)
 if Toggles.EnableCustomSkin and Toggles.EnableCustomSkin.Value then
  task.wait(0.2)
  applyXocoSkin()
 end
end)
end

-- ==============================================
-- Вкладка: VISUAL — XOCO IMAGE
-- ==============================================
do
local XocoImageSection = Tabs.Visual:AddRightGroupbox("Custom Image")

local imageGui = nil
local imageLabel = nil

-- Пресеты с примерами ID. Если ID не грузится — вставь свой в поле ниже.
local customImages = {
 ["Skull"] = "rbxassetid://17754592235",
 ["Fire"] = "rbxassetid://1215152788",
 ["Galaxy"] = "rbxassetid://940794397",
 ["Smile"] = "rbxassetid://10932783329",
 ["Target"] = "rbxassetid://16885137630",
 ["Custom"] = "",
}

XocoImageSection:AddToggle("EnableCustomImage", {
 Text = "Custom Image",
 Default = false,
 Tooltip = "Показывает картинку на экране",
 Callback = function(Value)
  if not Value and imageGui then
   pcall(function() imageGui:Destroy() end)
   imageGui = nil
   imageLabel = nil
  end
 end,
})

XocoImageSection:AddDropdown("CustomImageSelect", {
 Text = "Image",
 Values = {"Skull", "Fire", "Galaxy", "Smile", "Target", "Custom"},
 Default = "Skull",
 Tooltip = "Выбери картинку. Если не грузится — вставь свой ID в скрипт (строка customImages)",
})

XocoImageSection:AddSlider("CustomImageX", {
 Text = "Position X",
 Default = 50,
 Min = 0,
 Max = 100,
 Rounding = 0,
 Suffix = "%",
})

XocoImageSection:AddSlider("CustomImageY", {
 Text = "Position Y",
 Default = 50,
 Min = 0,
 Max = 100,
 Rounding = 0,
 Suffix = "%",
})

XocoImageSection:AddSlider("CustomImageZ", {
 Text = "Scale / ZIndex",
 Default = 50,
 Min = 10,
 Max = 200,
 Rounding = 0,
})

local function updateXocoImage()
 if not (Toggles.EnableCustomImage and Toggles.EnableCustomImage.Value) then
  if imageGui then
   pcall(function() imageGui:Destroy() end)
   imageGui = nil
   imageLabel = nil
  end
  return
 end

 if not imageGui then
  imageGui = Instance.new("ScreenGui")
  imageGui.Name = "XocoImageGui"
  imageGui.ResetOnSpawn = false
  imageGui.IgnoreGuiInset = true
  local targetParent = LocalPlayer:WaitForChild("PlayerGui")
  pcall(function() targetParent = game:GetService("CoreGui") end)
  pcall(function() if gethui then targetParent = gethui() end end)
  imageGui.Parent = targetParent

  imageLabel = Instance.new("ImageLabel")
  imageLabel.Name = "XocoImage"
  imageLabel.BackgroundTransparency = 1
  imageLabel.Size = UDim2.new(0, 200, 0, 200)
  imageLabel.AnchorPoint = Vector2.new(0.5, 0.5)
  imageLabel.Parent = imageGui
 end

 local imgName = Options.CustomImageSelect and Options.CustomImageSelect.Value or "Skull"
 local customID = Options.CustomImageCustomID and Options.CustomImageCustomID.Value or ""
 local imageID = customImages[imgName] or ""
 
 if imgName == "Custom" and customID ~= "" then
  imageID = customID
 end

 if imageID ~= "" then
  imageLabel.Image = imageID
  imageLabel.ImageColor3 = Color3.fromRGB(255, 255, 255)
 else
  imageLabel.Image = ""
 end

 local posX = Options.CustomImageX and Options.CustomImageX.Value or 50
 local posY = Options.CustomImageY and Options.CustomImageY.Value or 50
 local scaleZ = Options.CustomImageZ and Options.CustomImageZ.Value or 50

 imageLabel.Position = UDim2.new(posX / 100, 0, posY / 100, 0)
 imageLabel.Size = UDim2.new(0, 100 + scaleZ, 0, 100 + scaleZ)
 imageLabel.ZIndex = math.clamp(scaleZ, 1, 100)
end

RunService.RenderStepped:Connect(updateXocoImage)
end

-- ==============================================
end
do
-- Вкладка: MOVEMENT
-- ==============================================
local MovementSection = Tabs.Movement:AddLeftGroupbox("Player Movement")

-- Обход античита PCLD
local function BypassPCLD()
 for _, obj in ipairs(workspace:GetDescendants()) do
 if obj.Name == "PCLD" or obj.Name == "PCLDPart" then
 obj.Transparency = 1
 obj.CanCollide = false
 end
 end
end
-- ОПТИМИЗАЦИЯ: отложили BypassPCLD в ��он — не блокируем загрузку UI
task.spawn(BypassPCLD)

workspace.DescendantAdded:Connect(function(obj)
 if obj.Name == "PCLD" or obj.Name == "PCLDPart" then
 task.wait()
 obj.Transparency = 1
 obj.CanCollide = false
 end
end)

local function getRoot(char)
 return char:FindFirstChild("HumanoidRootPart")
end

MovementSection:AddToggle("EnableFly", {
        Text = "Fly (Bypass)",
        Default = false,
        Tooltip = "Полет с обходом гравитации и скорости (CFrame)",
}):AddKeyPicker("FlyKeybind", {
        Default = "None",
        SyncToggleState = true,
        Mode = "Toggle",
        Text = "Fly Key",
        NoUI = false,
})
flyHeight = nil -- высота полёта (для фиксации когда не двигаемся)

MovementSection:AddSlider("FlySpeed", {
 Text = "Fly Speed",
 Default = 50,
 Min = 10,
 Max = 300,
 Rounding = 0,
 Compact = false,
 Tooltip = "Настройка скорости полета",
})

MovementSection:AddToggle("EnableSpeed", {
 Text = "CFrame Speed",
 Default = false,
 Tooltip = "Универсальный спидхак (работает везде)",
})

MovementSection:AddSlider("SpeedValue", {
 Text = "Speed Multiplier",
 Default = 1,
 Min = 1,
 Max = 100,
 Rounding = 1,
 Compact = false,
 Tooltip = "Множитель скорости (CFrame)",
})

-- === FLY ANTI-KICK (встроен в Fly — отдельного тумблера больше нет) ===
-- Игра детектит полёт и шлёт "Flying" через GameCorrectionEvents,
-- после чего кикает. Anti-Kick стартует автоматически вместе с Fly
-- и тихо гасит кик через CharacterEvents.Struggle.

flyAntiKickConn = nil
local function startFlyAntiKick()
 if flyAntiKickConn then return end
 local rs = game:GetService("ReplicatedStorage")
 local ce = rs:FindFirstChild("GameCorrectionEvents")
 local notify = ce and ce:FindFirstChild("GameCorrectionsNotify")
 local che = rs:FindFirstChild("CharacterEvents")
 local struggle = che and che:FindFirstChild("Struggle")
 if not notify then
 Library:Notify("Fly Anti-Kick: событие детекта не найдено", 4)
 return
 end
 flyAntiKickConn = notify.OnClientEvent:Connect(function(reason)
 if reason ~= "Flying" then return end
 if not (Toggles.EnableFly and Toggles.EnableFly.Value) then return end
 -- Struggle тихо гасит текущую "коррекцию" сервера за полёт
 if struggle then pcall(function() struggle:FireServer(LocalPlayer) end) end
 end)
end
local function stopFlyAntiKick()
 if flyAntiKickConn then
 flyAntiKickConn:Disconnect()
 flyAntiKickConn = nil
 end
end

-- Флаг: был ли сюрикен запущен автоматически вместе с Fly
flyStartedShuriken = false

Toggles.EnableFly:OnChanged(function()
 if Toggles.EnableFly.Value then
 startFlyAntiKick()
 -- 2-в-1: автоматически включаем Anti Kick Shuriken
 if Toggles.EnableAntiKickShuriken and not Toggles.EnableAntiKickShuriken.Value then
 flyStartedShuriken = true
 Toggles.EnableAntiKickShuriken:SetValue(true)
 end
 Library:Notify("Fly ВКЛ (anti-kick + shuriken)", 2)
 else
 stopFlyAntiKick()
 if flyStartedShuriken and Toggles.EnableAntiKickShuriken and Toggles.EnableAntiKickShuriken.Value then
 Toggles.EnableAntiKickShuriken:SetValue(false)
 end
 flyStartedShuriken = false
 flyHeight = nil
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 local hum = char and char:FindFirstChild("Humanoid")
 if hrp then
 if hrp.Anchored then hrp.Anchored = false end
 hrp.AssemblyLinearVelocity = Vector3.zero
 if hrp:FindFirstChild("FlyVelocity") then hrp.FlyVelocity:Destroy() end
 end
 if hum then
 hum.AutoRotate = true
 end
 Library:Notify("Fly ВЫКЛ", 2)
 end

end)

RunService.Heartbeat:Connect(function(delta)
 local char = LocalPlayer.Character
 if not char then return end
 local root = getRoot(char)
 local hum = char:FindFirstChild("Humanoid")
 if not root or not hum then return end

 -- ЛОГИКА ФЛАЯ (FLY) — прямой CFrame, без PlatformStand, без lerp
 if Toggles.EnableFly and Toggles.EnableFly.Value then
 -- Гасим скорость — гравитация не тянет вниз
 root.AssemblyLinearVelocity = Vector3.zero
 -- Сюрикен может повалить — держим стоя
 if hum.PlatformStand then hum.PlatformStand = false end

 local cam = workspace.CurrentCamera
 if not cam then return end
 local moveDir = Vector3.new(0, 0, 0)
 
 if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
 if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) or UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0, 1, 0) end

 local speed = Options.FlySpeed and Options.FlySpeed.Value or 50

 if moveDir.Magnitude > 0 then
 moveDir = moveDir.Unit
 -- Прямое движение — мгновенный отклик
 local newPos = root.CFrame.Position + (moveDir * speed * delta)
 root.CFrame = CFrame.new(newPos) * (root.CFrame - root.CFrame.Position)
 flyHeight = newPos
 else
 -- Не жмём кнопки — фиксируем позицию
 if not flyHeight then flyHeight = root.CFrame.Position end
 root.CFrame = CFrame.new(flyHeight) * (root.CFrame - root.CFrame.Position)
 end
 else
 flyHeight = nil
 if root.Anchored then root.Anchored = false end
 end
 -- ЛОГИКА СПИДХАКА
 if Toggles.EnableSpeed and Toggles.EnableSpeed.Value and not (Toggles.EnableFly and Toggles.EnableFly.Value) then
 if hum.MoveDirection.Magnitude > 0 then
 root.CFrame = root.CFrame + (hum.MoveDirection * Options.SpeedValue.Value * delta * 20)
 end
 end
end)

MovementSection:AddToggle("EnableNoClip", {
 Text = "NoClip",
 Default = false,
 Tooltip = "П��охождение сквозь стены",
})

MovementSection:AddToggle("EnableWaterWalk", {
 Text = "Water Walk",
 Default = false,
 Tooltip = "Делает воду за картой твердой, позволяя ходить по ней",
 Callback = function(Value)
 pcall(function()
 local model = workspace:FindFirstChild("Map") 
 model = model and model:FindFirstChild("AlwaysHereTweenedObjects")
 model = model and model:FindFirstChild("Ocean")
 model = model and model:FindFirstChild("Object")
 model = model and model:FindFirstChild("ObjectModel")
 
 if model then
 for _, obj in pairs(model:GetChildren()) do
 if obj.Name == "Ocean" and obj:IsA("BasePart") then
 obj.CanCollide = Value
 end
 end
 end
 end)
 end
})

-- На всякий случай обновляем каждый кадр (если игра пытается вернуть воде прозрач��ость)
RunService.Heartbeat:Connect(function()
 if Toggles.EnableWaterWalk and Toggles.EnableWaterWalk.Value then
 pcall(function()
 local model = workspace:FindFirstChild("Map") 
 model = model and model:FindFirstChild("AlwaysHereTweenedObjects")
 model = model and model:FindFirstChild("Ocean")
 model = model and model:FindFirstChild("Object")
 model = model and model:FindFirstChild("ObjectModel")
 
 if model then
 for _, obj in pairs(model:GetChildren()) do
 if obj.Name == "Ocean" and obj:IsA("BasePart") and not obj.CanCollide then
 obj.CanCollide = true
 end
 end
 end
 end)
 end
end)

-- Логик�� NoClip выполняется каж��ый Stepped (перед просчетом физики)
RunService.Stepped:Connect(function()
 if Toggles.EnableNoClip and Toggles.EnableNoClip.Value then
 local char = LocalPlayer.Character
 if char then
 for _, part in ipairs(char:GetDescendants()) do
 if part:IsA("BasePart") and part.CanCollide then
 part.CanCollide = false
 end
 end
 end
 end
end)

originalJumpPower = 50
originalJumpHeight = 7.2
originalUseJumpPower = false
wasJumpEnabled = false

MovementSection:AddToggle("EnableJumpPower", {
 Text = "Custom Jump Power",
 Default = false,
 Tooltip = "Включить кастомную высоту прыжка",
})

MovementSection:AddSlider("JumpPowerValue", {
 Text = "Jump Power Amount",
 Default = 50,
 Min = 50,
 Max = 300,
 Rounding = 0,
 Compact = false,
 Tooltip = "Настройка высоты прыжка",
})

Toggles.EnableJumpPower:OnChanged(function()
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChild("Humanoid")
 if not hum then return end
 
 if Toggles.EnableJumpPower.Value then
  if not wasJumpEnabled then
   originalJumpPower = hum.JumpPower
   originalJumpHeight = hum.JumpHeight
   originalUseJumpPower = hum.UseJumpPower
   wasJumpEnabled = true
  end
  hum.UseJumpPower = true 
  hum.JumpPower = Options.JumpPowerValue.Value
 else
  if wasJumpEnabled then
   hum.UseJumpPower = originalUseJumpPower
   hum.JumpPower = originalJumpPower
   hum.JumpHeight = originalJumpHeight
   wasJumpEnabled = false
  end
 end
end)

Options.JumpPowerValue:OnChanged(function()
 if Toggles.EnableJumpPower.Value then
  local char = LocalPlayer.Character
  local hum = char and char:FindFirstChild("Humanoid")
  if hum then
   hum.JumpPower = Options.JumpPowerValue.Value
  end
 end
end)

MovementSection:AddToggle("EnableInfiniteJump", {
 Text = "Infinite Jump",
 Default = false,
 Tooltip = "Бесконечный прыжок: можно прыгать сколько угодно раз в воздухе",
})

UserInputService.JumpRequest:Connect(function()
 if not (Toggles.EnableInfiniteJump and Toggles.EnableInfiniteJump.Value) then return end
 pcall(function()
  local char = LocalPlayer.Character
  local hum = char and char:FindFirstChildOfClass("Humanoid")
  if not hum or hum.Health <= 0 then return end
  if Toggles.EnableJumpPower and Toggles.EnableJumpPower.Value and Options.JumpPowerValue then
   hum.UseJumpPower = true
   hum.JumpPower = Options.JumpPowerValue.Value
  end
  hum:ChangeState(Enum.HumanoidStateType.Jumping)
  hum.Jump = true
 end)
end)




jerkOffAnimTrack = nil
jerkOffActive = false

function stopJerkOff()
 jerkOffActive = false
 if jerkOffAnimTrack then
  jerkOffAnimTrack:Stop()
  jerkOffAnimTrack = nil
 end
end

function startJerkOff()
 if jerkOffActive then return end
 local char = LocalPlayer.Character
 if not char then return end
 local hum = char:FindFirstChildOfClass("Humanoid")
 if not hum then return end
 local animator = hum:FindFirstChild("Animator")
 if not animator then
  animator = Instance.new("Animator")
  animator.Parent = hum
 end
 
 local anim = Instance.new("Animation")
 anim.AnimationId = "rbxassetid://168268306"
 
 jerkOffAnimTrack = animator:LoadAnimation(anim)
 jerkOffAnimTrack.Priority = Enum.AnimationPriority.Action
 jerkOffAnimTrack:Play()
 jerkOffActive = true
 
 task.spawn(function()
  while jerkOffActive and jerkOffAnimTrack and jerkOffAnimTrack.IsPlaying do
   task.wait(0.1)
   pcall(function() jerkOffAnimTrack.TimePosition = 0.3 end)
  end
 end)
end

MovementSection:AddToggle("EnableJerkOff", {
 Text = "Jerk Off",
 Default = false,
 Tooltip = "Анимация дрочки (видна всем игрокам на сервере)",
 Callback = function(Value)
  if Value then
   startJerkOff()
  else
   stopJerkOff()
  end
 end
}):AddKeyPicker("JerkOffKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Jerk Off Key",
 NoUI = false,
})

-- ==============================================
-- STAND BEHIND (встать за игроком)
-- ==============================================
standBehindConn = nil
standBehindTarget = nil

local function standBehindList()
 local list = {}
 for _, p in ipairs(Players:GetPlayers()) do
  if p ~= LocalPlayer then
   table.insert(list, p.Name)
  end
 end
 return list
end

MovementSection:AddDropdown("StandBehindTarget", {
 Text = "Stand Behind — Цель",
 Values = standBehindList(),
 Default = (standBehindList())[1] or nil,
 Tooltip = "Выбери игрока из списка",
 Callback = function(Value)
  standBehindTarget = Value
 end,
})

local function standBehindRefresh()
 if not Options.StandBehindTarget then return end
 local list = standBehindList()
 pcall(function() Options.StandBehindTarget:SetValues(list) end)
 local cur = Options.StandBehindTarget.Value
 local stillHere = false
 for _, n in ipairs(list) do if n == cur then stillHere = true break end end
 if (not stillHere) and #list > 0 then
  pcall(function() Options.StandBehindTarget:SetValue(list[1]) end)
 end
 standBehindTarget = Options.StandBehindTarget.Value
end

Players.PlayerAdded:Connect(function() task.wait(0.3) standBehindRefresh() end)
Players.PlayerRemoving:Connect(function() task.defer(standBehindRefresh) end)

local function standBehindStop()
 if standBehindConn then standBehindConn:Disconnect(); standBehindConn = nil end
 local char = LocalPlayer.Character
 if not char then return end
 for _, p in pairs(char:GetChildren()) do
  if p:IsA("BasePart") then
   p.Velocity = Vector3.zero
   p.RotVelocity = Vector3.zero
  end
 end
end

local function standBehindStart()
 standBehindStop()
 standBehindConn = game:GetService("RunService").Heartbeat:Connect(function()
  local char = LocalPlayer.Character
  if not char then return end
  local hrp = char:FindFirstChild("HumanoidRootPart")
  if not hrp then return end
  local targetName = standBehindTarget or (Options.StandBehindTarget and Options.StandBehindTarget.Value)
  if not targetName then return end
  local targetPlayer = Players:FindFirstChild(targetName)
  if not targetPlayer or not targetPlayer.Character then return end
  local tHrp = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
  if not tHrp then return end
  hrp.Velocity = Vector3.zero
  hrp.RotVelocity = Vector3.zero
  hrp.CFrame = tHrp.CFrame * CFrame.new(0, 0, 2)
 end)
end

MovementSection:AddToggle("EnableStandBehind", {
 Text = "Stand Behind",
 Default = false,
 Tooltip = "Встать за выбранным игроком (следует за ним)",
 Callback = function(Value)
  if Value then
   standBehindStart()
  else
   standBehindStop()
  end
 end
}):AddKeyPicker("StandBehindKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Stand Behind Key",
 NoUI = false,
})

-- ==============================================
-- SPIDER (лазание по стенам)
-- ==============================================
spiderConn = nil
spiderSpeed = 30

local function spiderStop()
 if spiderConn then spiderConn:Disconnect(); spiderConn = nil end
end

local function spiderStart()
 local char = LocalPlayer.Character
 if not char then return end
 local hrp = char:FindFirstChild("HumanoidRootPart")
 if not hrp then return end
 spiderConn = game:GetService("RunService").Heartbeat:Connect(function(dt)
  local c = LocalPlayer.Character
  if not c then return end
  local h = c:FindFirstChild("HumanoidRootPart")
  local hum = c:FindFirstChildOfClass("Humanoid")
  if not h or not hum then return end
  if not Toggles.EnableSpider or not Toggles.EnableSpider.Value then return end
  local cam = workspace.CurrentCamera
  local moveDir = Vector3.zero
  local moving = false
  local keys = {W=false,A=false,S=false,D=false}
  pcall(function()
   keys.W = UserInputService:IsKeyDown(Enum.KeyCode.W)
   keys.A = UserInputService:IsKeyDown(Enum.KeyCode.A)
   keys.S = UserInputService:IsKeyDown(Enum.KeyCode.S)
   keys.D = UserInputService:IsKeyDown(Enum.KeyCode.D)
  end)
  if keys.W or keys.S or keys.A or keys.D then
   moving = true
   local camCF = cam.CFrame
   local forward = camCF.LookVector * ((keys.W and 1 or 0) - (keys.S and 1 or 0))
   local right = camCF.RightVector * ((keys.D and 1 or 0) - (keys.A and 1 or 0))
   moveDir = (forward + right)
   if moveDir.Magnitude > 0 then moveDir = moveDir.Unit end
  end
  local speed = spiderSpeed
  if Options.SpiderSpeed then speed = Options.SpiderSpeed.Value end
  local rayParams = RaycastParams.new()
  rayParams.FilterType = Enum.RaycastFilterType.Blacklist
  rayParams.FilterDescendantsInstances = {c}
  local ray = workspace:Raycast(h.Position, h.CFrame.LookVector * 2, rayParams)
  local wall = ray and ray.Instance
  if wall then
   hum.PlatformStand = false
   hum.Sit = false
   local climbDir = Vector3.new(0, 1, 0)
   if moving then
    climbDir = (moveDir + Vector3.new(0, 1, 0)).Unit
   end
   h.Velocity = climbDir * speed
   h.CFrame = CFrame.new(h.Position, h.Position + wall.Normal * -1)
  elseif moving then
   local floorRay = workspace:Raycast(h.Position, Vector3.new(0, -4, 0), rayParams)
   if not floorRay then
    h.Velocity = moveDir * speed + Vector3.new(0, hum.WalkSpeed * 0.5, 0)
   end
  end
 end)
end

MovementSection:AddToggle("EnableSpider", {
 Text = "Spider",
 Default = false,
 Tooltip = "Лазание по стенам (WASD + автоприлипание к стенам)",
 Callback = function(Value)
  if Value then
   spiderStart()
  else
   spiderStop()
  end
 end
}):AddKeyPicker("SpiderKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Spider Key",
 NoUI = false,
})

MovementSection:AddSlider("SpiderSpeed", {
 Text = "Spider Speed",
 Default = 30,
 Min = 10,
 Max = 200,
 Rounding = 0,
 Compact = false,
 Tooltip = "Скорость лазания по стенам",
})
-- BANG (анимация из Infinite Yield / XOCO)
-- ==============================================
playBangActive = false
bangAnimTrack = nil
bangAnimId = "rbxassetid://148840371"
bangSpeed = 10

local function bangStop()
 playBangActive = false
 if bangAnimTrack then
  bangAnimTrack:Stop()
  bangAnimTrack = nil
 end
end

local function bangStart()
 local char = LocalPlayer.Character
 if not char then return end
 local hum = char:FindFirstChildOfClass("Humanoid")
 if not hum then return end
 local animator = hum:FindFirstChildOfClass("Animator")
 if not animator then
  animator = Instance.new("Animator")
  animator.Parent = hum
 end
 local anim = Instance.new("Animation")
 anim.AnimationId = bangAnimId
 bangAnimTrack = animator:LoadAnimation(anim)
 bangAnimTrack.Priority = Enum.AnimationPriority.Action
 bangAnimTrack:Play()
 local speed = (Options.BangSpeed and Options.BangSpeed.Value) or 10
 bangAnimTrack:AdjustSpeed(speed)
 playBangActive = true
 task.spawn(function()
  while playBangActive do
   task.wait(0.1)
   if bangAnimTrack and bangAnimTrack.IsPlaying then
    bangAnimTrack.TimePosition = 0.1
   end
  end
 end)
end

MovementSection:AddToggle("EnableBang", {
 Text = "Bang",
 Default = false,
 Tooltip = "Анимация bang (как в XOCO / Infinite Yield)",
 Callback = function(Value)
  if Value then
   bangStart()
  else
   bangStop()
  end
 end
}):AddKeyPicker("BangKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Bang Key",
 NoUI = false,
})

MovementSection:AddSlider("BangSpeed", {
 Text = "Bang Speed",
 Default = 10,
 Min = 0.1,
 Max = 20,
 Rounding = 1,
 Compact = false,
 Tooltip = "Скорость анимации Bang (как в XOCO: 1 = нормально, 10 = быстро)",
})


-- ==============================================

-- ==============================================
-- Вкладка: MOVEMENT — BLOB FLY
-- ==============================================
-- Настоящего полёта игрока в этой игре нет — сервер контролит движение.
-- Рабочий способ: заспавнить ��ущество CreatureBlobman, сесть на его
-- сидень�� и рулить блобом через BodyVelocity + BodyGyro. Ты сидишь
-- на блобе → летит блоб, а вместе с ни�� и ты. Поскольку ты формально
-- сидишь — сервер не видит "полёт игрока", поэтому метод тихий.
ReplicatedStorage = game:GetService("ReplicatedStorage")
BlobFlySection = Tabs.Movement:AddRightGroupbox("Blob Fly")

-- === SAFE ADDON: TELEPORT TO CROSSHAIR ===
do
 TPCrosshairSection = Tabs.Movement:AddRightGroupbox("Teleport to Crosshair")

 TPCrosshairSection:AddToggle("EnableTPCrosshair", {
 Text = "Телепорт к прицелу",
 Default = false,
 Tooltip = "Вкл/Выкл телепорт туда, куда смотришь",
 }):AddKeyPicker("TPCrosshairKey", {
 Default = "None",
 SyncToggleState = false,
 Mode = "Toggle",
 Text = "Кнопка ТП",
 NoUI = false,
 })

 TPCrosshairSection:AddSlider("TPCrosshairSmoothness", {
 Text = "Плавность ТП",
 Default = 0,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 — моментально; 1 — очень плавно",
 })

 TweenService = game:GetService("TweenService")

 function tpKeyMatches(input, boundKey)
 if not boundKey then return false end
 if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then return true end
 if (boundKey == "MB1" or boundKey == "MouseButton1") and input.UserInputType == Enum.UserInputType.MouseButton1 then return true end
 if (boundKey == "MB2" or boundKey == "MouseButton2") and input.UserInputType == Enum.UserInputType.MouseButton2 then return true end
 return false
 end

 function doTPCrosshair()
 if not (Toggles.EnableTPCrosshair and Toggles.EnableTPCrosshair.Value) then return end
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 local cam = workspace.CurrentCamera
 if not (hrp and cam) then return end
 local params = RaycastParams.new()
 params.FilterType = Enum.RaycastFilterType.Exclude
 params.FilterDescendantsInstances = { char }
 local origin = cam.CFrame.Position
 local dir = cam.CFrame.LookVector * 5000
 local hit = workspace:Raycast(origin, dir, params)
 local targetPos = hit and (hit.Position + Vector3.new(0, 3, 0)) or (origin + cam.CFrame.LookVector * 200)
 local smooth = Options.TPCrosshairSmoothness and Options.TPCrosshairSmoothness.Value or 0
 if smooth <= 0.05 then
 hrp.CFrame = CFrame.new(targetPos)
 else
 TweenService:Create(hrp, TweenInfo.new(smooth, Enum.EasingStyle.Linear), { CFrame = CFrame.new(targetPos) }):Play()
 end
 end

 UserInputService.InputBegan:Connect(function(input, gpe)
 if gpe then return end
 if Options.TPCrosshairKey and tpKeyMatches(input, Options.TPCrosshairKey.Value) then
 doTPCrosshair()
 end
 end)
end


blobFlyBV = nil
blobFlyBG = nil

-- Спавним блоба в точке игрока (чтобы посадка не телепортировала черт-знает-куда)
function spawnBlobman()
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 local spawnCF = hrp and hrp.CFrame or CFrame.new(0, 100, 0)
 pcall(function()
 ReplicatedStorage.MenuToys.SpawnToyRemoteFunction:InvokeServer("CreatureBlobman", spawnCF, Vector3.zero)
 end)
 local folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 if not folder then
 folder = workspace:WaitForChild(LocalPlayer.Name .. "SpawnedInToys", 5)
 end
 if folder then
 local blob = folder:WaitForChild("CreatureBlobman", 5)
 if blob then
 Library:Notify("Blobman заспавнен!", 3)
 return blob
 end
 end
 return nil
end

-- Возвращает root блоба (через сиденье, либо из папки SpawnedInToys)
function GetBlobRoot()
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChild("Humanoid")
 if hum and hum.SeatPart and hum.SeatPart.Parent and hum.SeatPart.Parent.Name == "CreatureBlobman" then
 return hum.SeatPart.Parent:FindFirstChild("HumanoidRootPart") or hum.SeatPart.Parent.PrimaryPart
 end
 local folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 if folder then
 local blob = folder:FindFirstChild("CreatureBlobman")
 if blob then
 return blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
 end
 end
 return nil
end

-- Спавним (если надо) и сажаем игрока на сиденье блоба
bmKickingInProgress = false -- флаг: идёт кик — не пересоздавать блобман!

function sitOnBlobman()
 local char = LocalPlayer.Character
 local hrp = char and char:FindFirstChild("HumanoidRootPart")
 local hum = char and char:FindFirstChild("Humanoid")
 if not hrp or not hum then return false end
 local folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
 local blob = folder and folder:FindFirstChild("CreatureBlobman")
 -- ЧИСТКА: если старый блобман есть, но сиденье сл��мано/занято — уничтожаем!
 -- НО НЕ во время кика!
 if blob and not bmKickingInProgress then
 local seat = blob:FindFirstChild("VehicleSeat")
 if not seat or (seat and seat.Occupant and seat.Occupant ~= hum) then
 pcall(function()
 local destroyRem = ReplicatedStorage:FindFirstChild("MenuToys")
 destroyRem = destroyRem and destroyRem:FindFirstChild("DestroyToy")
 if destroyRem then destroyRem:FireServer(blob) end
 end)
 -- ждём удаления старого блобмана
 local dw = tick()
 while folder and folder:FindFirstChild("CreatureBlobman") and tick() - dw < 2 do
 task.wait(0.05)
 end
 blob = nil
 end
 end
 if not blob then
 blob = spawnBlobman()
 task.wait(0.5)
 end
 if not blob then return false end
 local seat = blob:WaitForChild("VehicleSeat", 5)
 if not seat then return false end
 -- если сиденье занято кем-то другим — уничтожаем блобмана и спавним новый
 if seat.Occupant and seat.Occupant ~= hum then
 pcall(function()
 local destroyRem = ReplicatedStorage:FindFirstChild("MenuToys")
 destroyRem = destroyRem and destroyRem:FindFirstChild("DestroyToy")
 if destroyRem then destroyRem:FireServer(blob) end
 end)
 task.wait(0.3)
 blob = spawnBlobman()
 if not blob then return false end
 seat = blob:WaitForChild("VehicleSeat", 5)
 if not seat then return false end
 end
 local t = tick()
 repeat
 if not hum.SeatPart then
 hrp.CFrame = seat.CFrame + Vector3.new(0, 1, 0)
 hrp.AssemblyLinearVelocity = Vector3.zero
 seat:Sit(hum)
 end
 RunService.Heartbeat:Wait()
 until hum.SeatPart == seat or tick() - t > 3
 return hum.SeatPart == seat
end

function cleanupBlobFly()
 if blobFlyBV then blobFlyBV:Destroy(); blobFlyBV = nil end
 if blobFlyBG then blobFlyBG:Destroy(); blobFlyBG = nil end
end

BlobFlySection:AddToggle("EnableBlobFly", {
 Text = "Blob Fly",
 Default = false,
 Tooltip = "Спавнит CreatureBlobman, сажает тебя на него и даёт летать",
})

BlobFlySection:AddSlider("BlobFlySpeed", {
 Text = "Blob Fly Speed",
 Default = 60,
 Min = 10,
 Max = 300,
 Rounding = 0,
 Compact = false,
 Tooltip = "Скорость полёта блоба",
})

BlobFlySection:AddButton({
 Text = "Заспавнить + сесть на блоба",
 Tooltip = "Ручной спавн и посадка (если авто не сработал)",
 Func = function()
 task.spawn(sitOnBlobman)
 end,
})

Toggles.EnableBlobFly:OnChanged(function()
 if Toggles.EnableBlobFly.Value then
 task.spawn(function()
 local ok = sitOnBlobman()
 if not ok then
 Library:Notify("Не удалось сесть на блоба — нажми кнопку ниже", 4)
 end
 end)
 else
 cleanupBlobFly()
 end
end)

-- Основной цикл блоб-флая (BodyVelocity + BodyGyro на root блоба)
RunService.Heartbeat:Connect(function()
 if not (Toggles.EnableBlobFly and Toggles.EnableBlobFly.Value) then
 if blobFlyBV or blobFlyBG then cleanupBlobFly() end
 return
 end
 local root = GetBlobRoot()
 if not root then return end

 -- ��оздаём/переиспользуем движки на root блоба
 if not root:FindFirstChild("BlobFlyVelocity") then
 blobFlyBV = Instance.new("BodyVelocity")
 blobFlyBV.Name = "BlobFlyVelocity"
 blobFlyBV.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 blobFlyBV.P = 10000
 blobFlyBV.Parent = root
 else
 blobFlyBV = root.BlobFlyVelocity
 end
 if not root:FindFirstChild("BlobFlyGyro") then
 blobFlyBG = Instance.new("BodyGyro")
 blobFlyBG.Name = "BlobFlyGyro"
 blobFlyBG.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
 blobFlyBG.P = 20000
 blobFlyBG.D = 100
 blobFlyBG.Parent = root
 else
 blobFlyBG = root.BlobFlyGyro
 end

 cam = workspace.CurrentCamera
 moveDir = Vector3.zero
 if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
 if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
 if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir = moveDir - Vector3.new(0, 1, 0) end

 speed = Options.BlobFlySpeed and Options.BlobFlySpeed.Value or 60
 if blobFlyBV then blobFlyBV.Velocity = moveDir * speed end
 if blobFlyBG then blobFlyBG.CFrame = cam.CFrame end
end)

-- ==============================================
-- Вкладка: MOVEMENT — House Teleport
-- ==============================================
local HouseTPSection = Tabs.Movement:AddRightGroupbox("House Teleport")

houseMapPoints = {
 ["Green House"] = CFrame.new(-548.305054, -2.45424771, 79.3213348),
 ["Pink House"] = CFrame.new(-475.493835, -2.70774508, -159.395279),
 ["Witch House"] = CFrame.new(270.225922, -2.48055029, 458.186493),
 ["Blue House"] = CFrame.new(501.939911, 88.2323608, -349.129211),
 ["China House"] = CFrame.new(545.441833, 128.004593, -99.4881439)
}

selectedHouseTP = nil

HouseTPSection:AddDropdown("HouseTPSelect", {
 Text = "Выбор дома",
 Default = "Green House",
 Values = {"Green House", "Pink House", "Witch House", "Blue House", "China House"},
 Multi = false,
 Tooltip = "Выбери дом для телепортации",
 Callback = function(Value)
 selectedHouseTP = Value
 end
})

HouseTPSection:AddButton({
 Text = "Телепортироваться",
 Tooltip = "Мгновенно телепортирует в выбранный дом",
 Func = function()
 local houseName = selectedHouseTP or (Options.HouseTPSelect and Options.HouseTPSelect.Value) or "Green House"
 local cf = houseMapPoints[houseName]
 if not cf then return end
 local char = LocalPlayer.Character
 if not char then return end
 char:PivotTo(cf)
 Library:Notify("Телепорт: " .. houseName, 2)
 end
})

-- ==============================================
-- Вкладка: MOVEMENT — POSES (Figure Grab Module, FGM)
-- ==============================================
do
    local FigureMain   = Tabs.Movement:AddRightGroupbox("Figure Grab")
    local FigurePoses  = Tabs.Movement:AddRightGroupbox("Figure Poses")
    local FigureOffsets = Tabs.Movement:AddRightGroupbox("Limb Offsets")

    getgenv().FigureGrabModule = getgenv().FigureGrabModule or {}
    local FGM = getgenv().FigureGrabModule

    FGM.Players          = Players
    FGM.RunService       = game:GetService("RunService")
    FGM.ReplicatedStorage = game:GetService("ReplicatedStorage")
    FGM.UserInputService = game:GetService("UserInputService")
    FGM.LocalPlayer      = LocalPlayer
    FGM.Mouse            = LocalPlayer:GetMouse()

    FGM.GrabEvents       = FGM.ReplicatedStorage:FindFirstChild("GrabEvents")
    FGM.SetNetworkOwner  = FGM.GrabEvents and FGM.GrabEvents:FindFirstChild("SetNetworkOwner")
    FGM.MenuToys         = FGM.ReplicatedStorage:FindFirstChild("MenuToys")
    FGM.ToySpawn         = FGM.MenuToys and FGM.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
    FGM.DestroyToy       = FGM.MenuToys and FGM.MenuToys:FindFirstChild("DestroyToy")

    FGM.State = {
        FigureGrabEnabled   = false,
        FigureGrabConnection = nil,
        TargetCharacter     = nil,
        MouseTarget         = nil,
        AnimationCopyEnabled = false,
        VectorZero          = Vector3.new(0, 0, 0),

        AutoRagdollToggle   = false,
        AutoRagdollEnabled = false,
        AutoRagdollConnection = nil,
        RagdollPallet       = nil,
        RagdollSoundPart    = nil,
        SeveralEnabled      = false,
        SeveralTargets      = {}
    }

    FGM.Configuration = {
        LineDistance = 0,
        HoldPosition = {X = 0, Y = 0, Z = -5},
        HoldRotation = {X = 0, Y = 0, Z = 0},
        LeftArmPosition = {X = 0, Y = 0, Z = 0},
        LeftArmRotation = {X = 0, Y = 0, Z = 0},
        RightArmPosition = {X = 0, Y = 0, Z = 0},
        RightArmRotation = {X = 0, Y = 0, Z = 0},
        LeftLegPosition = {X = 0, Y = 0, Z = 0},
        LeftLegRotation = {X = 0, Y = 0, Z = 0},
        RightLegPosition = {X = 0, Y = 0, Z = 0},
        RightLegRotation = {X = 0, Y = 0, Z = 0},
        HeadPosition = {X = 0, Y = 0, Z = 0},
        HeadRotation = {X = 0, Y = 0, Z = 0}
    }

    FGM.Presets = {
        Pose1 = {HoldPosition={X=0,Y=0,Z=-7.5},HoldRotation={X=90,Y=0,Z=108},LeftArmPosition={X=-1.5,Y=1,Z=-1},LeftArmRotation={X=283,Y=0,Z=0},RightArmPosition={X=1.5,Y=0.5,Z=1},RightArmRotation={X=270,Y=0,Z=0},LeftLegPosition={X=0.5,Y=-1.5,Z=0.5},LeftLegRotation={X=312,Y=0,Z=0},RightLegPosition={X=-0.5,Y=-1.5,Z=0.5},RightLegRotation={X=283,Y=0,Z=0},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=0,Y=0,Z=0}},
        Pose2 = {HoldPosition={X=0,Y=-1.5,Z=-12.5},HoldRotation={X=272,Y=0,Z=0},LeftArmPosition={X=-1,Y=1,Z=-0.5},LeftArmRotation={X=90,Y=0,Z=0},RightArmPosition={X=1,Y=1,Z=-0.5},RightArmRotation={X=90,Y=0,Z=0},LeftLegPosition={X=1,Y=-1,Z=-0.5},LeftLegRotation={X=90,Y=0,Z=0},RightLegPosition={X=-1,Y=-1,Z=-0.5},RightLegRotation={X=90,Y=0,Z=0},HeadPosition={X=0,Y=1,Z=1},HeadRotation={X=90,Y=0,Z=0}},
        Pose3 = {HoldPosition={X=0,Y=-5.5,Z=-4},HoldRotation={X=0,Y=0,Z=0},LeftArmPosition={X=1,Y=7.5,Z=1.5},LeftArmRotation={X=0,Y=0,Z=0},RightArmPosition={X=1,Y=6,Z=1.5},RightArmRotation={X=0,Y=0,Z=0},LeftLegPosition={X=0.5,Y=5,Z=1.5},LeftLegRotation={X=0,Y=0,Z=92},RightLegPosition={X=-0.5,Y=5,Z=1.5},RightLegRotation={X=0,Y=0,Z=90},HeadPosition={X=0,Y=0,Z=0},HeadRotation={X=0,Y=0,Z=0}},
        Pose4 = {HoldPosition={X=1.5,Y=-8.5,Z=-1.5},HoldRotation={X=0,Y=0,Z=0},LeftArmPosition={X=0,Y=0,Z=0},LeftArmRotation={X=0,Y=0,Z=0},RightArmPosition={X=0,Y=0,Z=0},RightArmRotation={X=0,Y=0,Z=0},LeftLegPosition={X=0,Y=0,Z=0},LeftLegRotation={X=0,Y=0,Z=0},RightLegPosition={X=1.5,Y=0,Z=0},RightLegRotation={X=0,Y=0,Z=0},HeadPosition={X=0,Y=9,Z=0},HeadRotation={X=0,Y=0,Z=0}},
        Pose5 = {HoldPosition={X=0,Y=-3,Z=-6},HoldRotation={X=270,Y=0,Z=0},LeftArmPosition={X=-1,Y=0.5,Z=0},LeftArmRotation={X=180,Y=0,Z=0},RightArmPosition={X=1,Y=0.5,Z=0},RightArmRotation={X=180,Y=0,Z=0},LeftLegPosition={X=0,Y=-3,Z=0},LeftLegRotation={X=0,Y=0,Z=0},RightLegPosition={X=0,Y=-2,Z=0.5},RightLegRotation={X=45,Y=0,Z=0},HeadPosition={X=0,Y=1.5,Z=-0.5},HeadRotation={X=270,Y=0,Z=0}},
        Pose6 = {HoldPosition={X=5.5,Y=0.5,Z=-1.5},HoldRotation={X=345,Y=39,Z=0},LeftArmPosition={X=2,Y=0.5,Z=0},LeftArmRotation={X=0,Y=43,Z=121},RightArmPosition={X=-2,Y=0,Z=0},RightArmRotation={X=64,Y=112,Z=0},LeftLegPosition={X=-0.5,Y=-2,Z=0},LeftLegRotation={X=349,Y=0,Z=360},RightLegPosition={X=0.5,Y=-2,Z=0},RightLegRotation={X=345,Y=360,Z=10},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=0,Y=344,Z=0}},
        Pose7 = {HoldPosition={X=0,Y=-2,Z=-10},HoldRotation={X=90,Y=0,Z=0},LeftArmPosition={X=-1.5,Y=0,Z=0},LeftArmRotation={X=270,Y=0,Z=315},RightArmPosition={X=1.5,Y=0,Z=0},RightArmRotation={X=270,Y=0,Z=45},LeftLegPosition={X=-1,Y=-1.5,Z=0},LeftLegRotation={X=90,Y=0,Z=0},RightLegPosition={X=1,Y=-1.5,Z=0},RightLegRotation={X=90,Y=0,Z=0},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=0,Y=0,Z=0}},
        JojoStand = {HoldPosition={X=-4.5,Y=0.5,Z=-1.5},HoldRotation={X=8,Y=349,Z=0},LeftArmPosition={X=1.5,Y=0,Z=0},LeftArmRotation={X=15,Y=62,Z=41},RightArmPosition={X=-1.5,Y=0.5,Z=-0.5},RightArmRotation={X=65,Y=149,Z=6},LeftLegPosition={X=-0.5,Y=-2,Z=0},LeftLegRotation={X=349,Y=0,Z=360},RightLegPosition={X=0.5,Y=-2,Z=0},RightLegRotation={X=345,Y=360,Z=10},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=0,Y=344,Z=0}},
        ShoulderRide = {HoldPosition={X=0,Y=3.2,Z=-0.3},HoldRotation={X=0,Y=0,Z=0},LeftArmPosition={X=-1.5,Y=-0.5,Z=-1.4},LeftArmRotation={X=80,Y=0,Z=-15},RightArmPosition={X=1.5,Y=-0.5,Z=-1.4},RightArmRotation={X=80,Y=0,Z=15},LeftLegPosition={X=0.8,Y=-1.1,Z=-1.3},LeftLegRotation={X=70,Y=0,Z=-30},RightLegPosition={X=-0.8,Y=-1.1,Z=-1.3},RightLegRotation={X=70,Y=0,Z=30},HeadPosition={X=0,Y=1.5,Z=0},HeadRotation={X=-8,Y=0,Z=0}}
    }

    function FGM.GetCharacter(player)
        local character = player.Character
        if not character and player.CharacterAdded then
            character = player.CharacterAdded:Wait()
        end
        return character
    end

    function FGM.CopyAnimationsFromLimbs()
        if not FGM.State.AnimationCopyEnabled or not FGM.State.TargetCharacter then return end
        local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
        if not MyCharacter then return end
        local MyHRP = MyCharacter:FindFirstChild("HumanoidRootPart")
        local MyTorso = MyCharacter:FindFirstChild("Torso")
        local TargetTorso = FGM.State.TargetCharacter:FindFirstChild("Torso")
        if not MyHRP or not MyTorso or not TargetTorso then return end
        local holdCFrame = MyHRP.CFrame * CFrame.new(
            FGM.Configuration.HoldPosition.X, FGM.Configuration.HoldPosition.Y, FGM.Configuration.HoldPosition.Z
        ) * CFrame.Angles(
            math.rad(FGM.Configuration.HoldRotation.X), math.rad(FGM.Configuration.HoldRotation.Y), math.rad(FGM.Configuration.HoldRotation.Z)
        )
        TargetTorso.CFrame = holdCFrame
        local torsoRelative = MyHRP.CFrame:ToObjectSpace(MyTorso.CFrame)
        TargetTorso.CFrame = TargetTorso.CFrame * torsoRelative.Rotation
        TargetTorso.Velocity = FGM.State.VectorZero
        TargetTorso.RotVelocity = FGM.State.VectorZero
        local limbs = {"Head", "Right Arm", "Left Arm", "Right Leg", "Left Leg"}
        for _, limbName in ipairs(limbs) do
            local myPart = MyCharacter:FindFirstChild(limbName)
            local targetPart = FGM.State.TargetCharacter:FindFirstChild(limbName)
            if myPart and targetPart then
                local relative = MyTorso.CFrame:ToObjectSpace(myPart.CFrame)
                targetPart.CFrame = TargetTorso.CFrame:ToWorldSpace(relative)
                targetPart.Velocity = FGM.State.VectorZero
                targetPart.RotVelocity = FGM.State.VectorZero
            end
        end
    end

    function FGM.ToggleAutoRagdoll(enabled)
        FGM.State.AutoRagdollEnabled = enabled
        if FGM.State.AutoRagdollConnection then
            FGM.State.AutoRagdollConnection:Disconnect()
            FGM.State.AutoRagdollConnection = nil
        end
        if not enabled then
            if FGM.State.RagdollPallet and FGM.DestroyToy then
                pcall(function() FGM.DestroyToy:FireServer(FGM.State.RagdollPallet) end)
            end
            FGM.State.RagdollPallet = nil
            FGM.State.RagdollSoundPart = nil
            return
        end
        task.spawn(function()
            if not FGM.ToySpawn or not FGM.DestroyToy then return end
            local myChar = FGM.LocalPlayer.Character
            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end
            FGM.MyToys = workspace:FindFirstChild(FGM.LocalPlayer.Name .. "SpawnedInToys")
            if not FGM.MyToys then return end
            local pallet = FGM.MyToys:FindFirstChild("RagdollPallet") or FGM.MyToys:FindFirstChild("PalletLightBrown")
            if not pallet then
                FGM.ToySpawn:InvokeServer("PalletLightBrown", myHRP.CFrame * CFrame.new(5, 5, 20), Vector3.new(0, 0, 0))
                local t = tick() + 5
                repeat task.wait(0.05) until FGM.MyToys:FindFirstChild("PalletLightBrown") or tick() > t
                pallet = FGM.MyToys:FindFirstChild("PalletLightBrown")
            end
            if not pallet then return end
            pallet.Name = "RagdollPallet"
            local soundPart = pallet:FindFirstChild("SoundPart")
            if not soundPart then return end
            local t2 = tick() + 3
            repeat
                FGM.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
                task.wait()
            until (soundPart:FindFirstChild("PartOwner") or tick() > t2)
            soundPart.AssemblyLinearVelocity = Vector3.new(0, 10000, 0)
            for _, v in pairs(pallet:GetDescendants()) do
                if v:IsA("BasePart") then v.Transparency = 1 v.CanCollide = false end
            end
            FGM.State.RagdollPallet = pallet
            FGM.State.RagdollSoundPart = soundPart
            FGM.State.AutoRagdollConnection = FGM.RunService.Heartbeat:Connect(function()
                if not FGM.State.AutoRagdollEnabled then return end
                local sp = FGM.State.RagdollSoundPart
                if not sp or not sp.Parent then
                    if FGM.State.AutoRagdollConnection then
                        FGM.State.AutoRagdollConnection:Disconnect()
                        FGM.State.AutoRagdollConnection = nil
                    end
                    FGM.State.RagdollPallet = nil
                    FGM.State.RagdollSoundPart = nil
                    return
                end
                local targets = {}
                if FGM.State.FigureGrabEnabled and FGM.State.TargetCharacter then
                    table.insert(targets, FGM.State.TargetCharacter)
                end
                if FGM.State.SeveralEnabled then
                    for _, e in ipairs(FGM.State.SeveralTargets) do table.insert(targets, e.char) end
                end
                for _, targetChar in ipairs(targets) do
                    local hrp = targetChar:FindFirstChild("HumanoidRootPart")
                    local hum = targetChar:FindFirstChild("Humanoid")
                    if hrp and hum then
                        local ragdolled = hum:FindFirstChild("Ragdolled")
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

    function FGM.ToggleFigureGrab()
        if not FGM.State.FigureGrabEnabled then
            local MouseTarget = FGM.Mouse.Target
            if not MouseTarget then
                Library:Notify("Наведи на игрока мышкой!", 3)
                return
            end
            local targetParent = MouseTarget.Parent
            local hum = targetParent and targetParent:FindFirstChildOfClass("Humanoid")
            if not hum then
                Library:Notify("Это не игрок!", 3)
                return
            end
            FGM.State.TargetCharacter = targetParent
            FGM.State.MouseTarget = MouseTarget
            local MyCharacter = FGM.GetCharacter(FGM.LocalPlayer)
            if not FGM.State.TargetCharacter or not MyCharacter then
                Library:Notify("Invalid target", 3)
                return
            end
            local BodyParts = {"Head", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}
            local TargetTorso = FGM.State.TargetCharacter:FindFirstChild("Torso") or FGM.State.TargetCharacter:FindFirstChild("UpperTorso")
            if not TargetTorso then
                Library:Notify("Torso not found", 3)
                return
            end
            for _, partName in pairs(BodyParts) do
                local part = FGM.State.TargetCharacter:FindFirstChild(partName)
                if part then
                    part.Anchored = false
                    part.CanCollide = true
                    part.Massless = true
                end
            end
            FGM.State.FigureGrabEnabled = true
            FGM.Configuration.LineDistance = 5
            if FGM.State.FigureGrabConnection then
                FGM.State.FigureGrabConnection:Disconnect()
            end
            FGM.State.FigureGrabConnection = FGM.RunService.Heartbeat:Connect(function()
                if not FGM.State.TargetCharacter or not MyCharacter or not MyCharacter.Parent then
                    FGM.State.FigureGrabEnabled = false
                    if FGM.State.FigureGrabConnection then
                        FGM.State.FigureGrabConnection:Disconnect()
                        FGM.State.FigureGrabConnection = nil
                    end
                    return
                end
                local MyRoot = MyCharacter:FindFirstChild("HumanoidRootPart")
                if not MyRoot then return end
                local holdCFrame = MyRoot.CFrame * CFrame.new(
                    FGM.Configuration.HoldPosition.X,
                    FGM.Configuration.HoldPosition.Y,
                    FGM.Configuration.HoldPosition.Z
                )
                TargetTorso = FGM.State.TargetCharacter:FindFirstChild("Torso") or FGM.State.TargetCharacter:FindFirstChild("UpperTorso")
                if not TargetTorso then return end
                TargetTorso.CFrame = holdCFrame * CFrame.Angles(
                    math.rad(FGM.Configuration.HoldRotation.X),
                    math.rad(FGM.Configuration.HoldRotation.Y),
                    math.rad(FGM.Configuration.HoldRotation.Z)
                )
                TargetTorso.Velocity = FGM.State.VectorZero
                TargetTorso.RotVelocity = FGM.State.VectorZero
                if FGM.State.AnimationCopyEnabled then
                    FGM.CopyAnimationsFromLimbs()
                else
                    for _, partName in pairs(BodyParts) do
                        local part = FGM.State.TargetCharacter:FindFirstChild(partName)
                        if part and part ~= TargetTorso then
                            local posKey = string.gsub(partName, " ", "") .. "Position"
                            local rotKey = string.gsub(partName, " ", "") .. "Rotation"
                            if FGM.Configuration[posKey] and FGM.Configuration[rotKey] then
                                part.CFrame = TargetTorso.CFrame * CFrame.new(
                                    FGM.Configuration[posKey].X, FGM.Configuration[posKey].Y, FGM.Configuration[posKey].Z
                                ) * CFrame.Angles(
                                    math.rad(FGM.Configuration[rotKey].X),
                                    math.rad(FGM.Configuration[rotKey].Y),
                                    math.rad(FGM.Configuration[rotKey].Z)
                                )
                                part.Velocity = FGM.State.VectorZero
                                part.RotVelocity = FGM.State.VectorZero
                            end
                        end
                    end
                end
                if FGM.SetNetworkOwner and MouseTarget and MouseTarget.Parent then
                    pcall(function() FGM.SetNetworkOwner:FireServer(MouseTarget, holdCFrame) end)
                end
            end)
            if FGM.State.AutoRagdollToggle then
                FGM.ToggleAutoRagdoll(true)
            end
            Library:Notify("Figure Grab активирован", 3)
        else
            FGM.State.FigureGrabEnabled = false
            FGM.State.AnimationCopyEnabled = false
            if FGM.State.FigureGrabConnection then
                FGM.State.FigureGrabConnection:Disconnect()
                FGM.State.FigureGrabConnection = nil
            end
            FGM.ToggleAutoRagdoll(false)
            Library:Notify("Figure Grab выключен", 3)
        end
    end

    function FGM.ResetPose()
        for section, values in pairs(FGM.Configuration) do
            if typeof(values) == "table" then
                for axis, _ in pairs(values) do
                    values[axis] = 0
                end
            end
        end
    end

    function FGM.ApplyPreset(presetName)
        local preset = FGM.Presets[presetName]
        if preset then
            for section, values in pairs(preset) do
                if FGM.Configuration[section] then
                    for axis, value in pairs(values) do
                        FGM.Configuration[section][axis] = value
                    end
                end
            end
        end
    end

    function FGM.UpdateConfig(section, axis, value)
        if FGM.Configuration[section] and FGM.Configuration[section][axis] ~= nil then
            FGM.Configuration[section][axis] = value
        end
    end

    -- ============ UI (Linoria) ============

    FigureMain:AddToggle("FG_EnableGrab", {
        Text = "Enable Figure Grab",
        Default = false,
        Tooltip = "Наведи на игрока мышкой и включи — цель застывает в выбранной позе",
    }):AddKeyPicker("FG_ToggleKeybind", {
        Default = "V",
        SyncToggleState = true,
        Mode = "Toggle",
        Text = "Grab Key",
        NoUI = false,
    })

    FigureMain:AddToggle("FG_AutoRagdollToggle", {
        Text = "Auto Ragdoll Target",
        Default = false,
        Tooltip = "Автоматически рагдоллить цель во время граба",
    })

    FigureMain:AddToggle("FG_AnimCopyToggle", {
        Text = "Copy My Animations to Target",
        Default = false,
        Tooltip = "Копировать твои движения на цель",
    })

    FigurePoses:AddButton({Text = "Reset Pose", Func = function() FGM.ResetPose() end})
    FigurePoses:AddButton({Text = "Pose 1 Jesus",      Func = function() FGM.ApplyPreset("Pose1") end})
    FigurePoses:AddButton({Text = "Pose 2 Dog",        Func = function() FGM.ApplyPreset("Pose2") end})
    FigurePoses:AddButton({Text = "Pose 3 L",          Func = function() FGM.ApplyPreset("Pose3") end})
    FigurePoses:AddButton({Text = "Pose 4 Head Hold",  Func = function() FGM.ApplyPreset("Pose4") end})
    FigurePoses:AddButton({Text = "Pose 5 Handstand", Func = function() FGM.ApplyPreset("Pose5") end})
    FigurePoses:AddButton({Text = "Pose 6 Stand 1",    Func = function() FGM.ApplyPreset("Pose6") end})
    FigurePoses:AddButton({Text = "Pose 7 T-Pose",    Func = function() FGM.ApplyPreset("Pose7") end})
    FigurePoses:AddButton({Text = "Pose 8 Stand 2",    Func = function() FGM.ApplyPreset("JojoStand") end})
    FigurePoses:AddButton({Text = "Shoulder Ride",    Func = function() FGM.ApplyPreset("ShoulderRide") end})

    local function CreateLimbSliders(limbName, configKey)
        for _, axis in ipairs({"X", "Y", "Z"}) do
            FigureOffsets:AddSlider("FG_" .. configKey .. "Pos" .. axis, {
                Text = limbName .. " Pos " .. axis,
                Min = -50, Max = 50, Default = 0, Rounding = 1,
                Compact = false,
                Callback = function(value) FGM.UpdateConfig(configKey .. "Position", axis, value) end
            })
            FigureOffsets:AddSlider("FG_" .. configKey .. "Rot" .. axis, {
                Text = limbName .. " Rot " .. axis,
                Min = 0, Max = 360, Default = 0, Rounding = 0,
                Compact = false,
                Callback = function(value) FGM.UpdateConfig(configKey .. "Rotation", axis, value) end
            })
        end
    end

    CreateLimbSliders("Hold (Torso)", "Hold")
    CreateLimbSliders("Left Arm", "LeftArm")
    CreateLimbSliders("Right Arm", "RightArm")
    CreateLimbSliders("Left Leg", "LeftLeg")
    CreateLimbSliders("Right Leg", "RightLeg")
    CreateLimbSliders("Head", "Head")

    Toggles.FG_EnableGrab:OnChanged(function()
        FGM.ToggleFigureGrab()
    end)

    Toggles.FG_AutoRagdollToggle:OnChanged(function(Value)
        FGM.State.AutoRagdollToggle = Value
        if FGM.State.FigureGrabEnabled then
            FGM.ToggleAutoRagdoll(Value)
        end
    end)

    Toggles.FG_AnimCopyToggle:OnChanged(function(Value)
        FGM.State.AnimationCopyEnabled = Value
        if Value then
            Library:Notify("Animation Copy: копирую твои движения", 3)
        else
            Library:Notify("Animation Copy: ручное управление", 3)
        end
    end)
end

-- ==============================================
-- Вкладка: MOVEMENT — BHOP (Bunny Hop + Strafes)
-- ==============================================
do
local BhopSection = Tabs.Movement:AddRightGroupbox("Bhop")

BhopSection:AddToggle("EnableBhop", {
 Text = "Bhop (Bunny Hop)",
 Default = false,
 Tooltip = "Начни двигаться — персонаж сам начнёт распрыгиваться",
})

BhopSection:AddToggle("EnableBhopStrafe", {
 Text = "Strafes (разгон мышкой)",
 Default = false,
 Tooltip = "В полёте крути мышку в сторону движения — набираешь скорость, как в CS",
})

BhopSection:AddSlider("BhopJumpPower", {
 Text = "Высота прыжка",
 Default = 50,
 Min = 10,
 Max = 200,
 Rounding = 0,
 Compact = false,
})

BhopSection:AddSlider("BhopSpeedGain", {
 Text = "Разгон за прыжок",
 Default = 4,
 Min = 0,
 Max = 20,
 Rounding = 1,
 Compact = false,
 Tooltip = "Сколько скорости добавляется при каждом отталкивании от земли",
})

BhopSection:AddSlider("BhopStrafeSpeed", {
 Text = "Сила стрейфа",
 Default = 0.05,
 Min = 0.01,
 Max = 0.3,
 Rounding = 2,
 Compact = false,
 Tooltip = "Насколько сильно мышка подгоняет скорость в полёте",
})

local bhopConn = nil
local bhopLastYaw = nil
local bhopCurSpeed = 0

local function stopBhop()
 if bhopConn then
  bhopConn:Disconnect()
  bhopConn = nil
 end
 bhopLastYaw = nil
 bhopCurSpeed = 0
end

Toggles.EnableBhop:OnChanged(function()
 stopBhop()
 if not Toggles.EnableBhop.Value then return end
 bhopConn = RunService.Heartbeat:Connect(function()
  local char = LocalPlayer.Character
  local hum = char and char:FindFirstChildOfClass("Humanoid")
  local root = char and char:FindFirstChild("HumanoidRootPart")
  if not (hum and root) then return end

  local jumpPower = Options.BhopJumpPower and Options.BhopJumpPower.Value or 50
  local speedGain = Options.BhopSpeedGain and Options.BhopSpeedGain.Value or 4
  local strafeGain = Options.BhopStrafeSpeed and Options.BhopStrafeSpeed.Value or 0.05
  local maxSpeed = 200

  local vel = root.AssemblyLinearVelocity
  local flatVel = Vector3.new(vel.X, 0, vel.Z)

  -- СБРОС разгона если игрок остановился (не двигается и на земле)
  local moving = hum.MoveDirection.Magnitude > 0.05
  if not moving and hum.FloorMaterial ~= Enum.Material.Air then
   bhopCurSpeed = 0
  end

  -- АВТО-ПРЫЖОК: как только коснулись земли и игрок двигается — прыгаем сами + добавляем разгон
  if hum.FloorMaterial ~= Enum.Material.Air and moving then
   local dir = hum.MoveDirection.Unit
   bhopCurSpeed = math.max(flatVel.Magnitude, 16) + speedGain
   if bhopCurSpeed > maxSpeed then bhopCurSpeed = maxSpeed end
   root.AssemblyLinearVelocity = Vector3.new(dir.X * bhopCurSpeed, jumpPower, dir.Z * bhopCurSpeed)
  end

  -- СТРЕЙФЫ: в полёте поворачиваем вектор скорости за камерой и чуть-чуть ускоряем
  if Toggles.EnableBhopStrafe and Toggles.EnableBhopStrafe.Value then
   if hum.FloorMaterial == Enum.Material.Air then
    local cam = workspace.CurrentCamera
    if cam and flatVel.Magnitude > 5 then
     local _, yaw = cam.CFrame:ToEulerAnglesYXZ()
     if bhopLastYaw then
      local delta = yaw - bhopLastYaw
      if delta > math.pi then delta = delta - math.pi * 2 end
      if delta < -math.pi then delta = delta + math.pi * 2 end
      if math.abs(delta) > 0.0005 then
       -- Поворачиваем текущую горизонтальную скорость вслед за камерой и подгоняем
       local rotated = CFrame.Angles(0, delta, 0) * flatVel
       local newSpeed = rotated.Magnitude + math.abs(delta) * strafeGain * 100
       if newSpeed > maxSpeed then newSpeed = maxSpeed end
       local newFlat = rotated.Unit * newSpeed
       root.AssemblyLinearVelocity = Vector3.new(newFlat.X, vel.Y, newFlat.Z)
      end
     end
     bhopLastYaw = yaw
    end
   else
    bhopLastYaw = nil
   end
  end
 end)
end)

end


-- ==============================================
do
-- Вкладка: MOVEMENT — SPIN CHARACTER (from XOCO)
-- ==============================================
do
local SpinSection = Tabs.Movement:AddRightGroupbox("Spin Character")

SpinSection:AddToggle("EnableSpinChar", {
 Text = "Spin Character",
 Default = false,
 Tooltip = "Быстрое вращение персонажа. Видно только от 3 лица — камера не крутится",
})

SpinSection:AddSlider("SpinCharSpeed", {
 Text = "Скорость вращения",
 Default = 5,
 Min = 1,
 Max = 50,
 Rounding = 0,
 Compact = false,
})

local spinCharConn = nil

local function stopSpinChar()
 if spinCharConn then
  spinCharConn:Disconnect()
  spinCharConn = nil
 end
end

Toggles.EnableSpinChar:OnChanged(function()
 stopSpinChar()
 if not Toggles.EnableSpinChar.Value then return end
 spinCharConn = RunService.Heartbeat:Connect(function()
  local char = LocalPlayer.Character
  local root = char and char:FindFirstChild("HumanoidRootPart")
  local head = char and char:FindFirstChild("Head")
  if not root then return end
  -- Только от 3 лица: если камера вплотную к голове (1 лицо), не крутим
  local cam = workspace.CurrentCamera
  if cam and head then
   local dist = (cam.CFrame.Position - head.Position).Magnitude
   if dist < 1.5 then return end
  end
  local speed = Options.SpinCharSpeed and Options.SpinCharSpeed.Value or 5
  root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(speed), 0)
 end)
end)

end
end


-- ==============================================
do
-- Вкладка: MOVEMENT — ANIMATIONS (from XOCO)
-- ==============================================
do
local AnimSection = Tabs.Movement:AddLeftGroupbox("Animations")

AnimSection:AddDropdown("SelectedAnimation", {
 Text = "Animation",
 Values = {"Crazy", "Insane", "Collapse", "Zombie", "Moon Dance", "Full Punch", "Full Swing", "Arm Turbine", "Barrel Roll", "Arm Detach", "Insane Arms", "Spinner", "Crazy Slash", "Zombie Attack"},
 Default = "Crazy",
 Tooltip = "Выбери анимацию",
})

local animTrack_Custom = nil

local animIds_Custom = {
 ["Crazy"]    = "rbxassetid://248263260",
 ["Insane"]   = "rbxassetid://35654637",
 ["Collapse"] = "rbxassetid://35154961",
 ["Zombie"]   = "rbxassetid://33796059",
 ["Moon Dance"] = "rbxassetid://45834924",
 ["Full Punch"] = "rbxassetid://204062532",
 ["Full Swing"] = "rbxassetid://218504594",
 ["Arm Turbine"]= "rbxassetid://259438880",
 ["Barrel Roll"]= "rbxassetid://136801964",
 ["Arm Detach"] = "rbxassetid://33169583",
 ["Insane Arms"]= "rbxassetid://27432691",
 ["Spinner"]    = "rbxassetid://754658275",
 ["Crazy Slash"]= "rbxassetid://674871189",
 ["Zombie Attack"] = "rbxassetid://708553116",
}

local function stopCustomAnim()
 if animTrack_Custom then
  animTrack_Custom:Stop()
  animTrack_Custom = nil
 end
end

local function playCustomAnim()
 stopCustomAnim()
 local char = LocalPlayer.Character
 if not char then return end
 local hum = char:FindFirstChildOfClass("Humanoid")
 if not hum then return end
 local animator = hum:FindFirstChildOfClass("Animator")
 if not animator then
  animator = Instance.new("Animator")
  animator.Parent = hum
 end
 local animName = Options.SelectedAnimation and Options.SelectedAnimation.Value or "Crazy"
 local animId = animIds_Custom[animName]
 if not animId then return end
 local anim = Instance.new("Animation")
 anim.AnimationId = animId
 animTrack_Custom = animator:LoadAnimation(anim)
 animTrack_Custom.Priority = Enum.AnimationPriority.Action
 animTrack_Custom.Looped = true
 animTrack_Custom:Play()
 
 task.spawn(function()
  while Toggles.EnableAnimation and Toggles.EnableAnimation.Value and animTrack_Custom do
   pcall(function()
    if animTrack_Custom.TimePosition > 0.9 then
     animTrack_Custom.TimePosition = 0.3
    end
   end)
   task.wait(0.05)
  end
 end)
end

AnimSection:AddToggle("EnableAnimation", {
 Text = "Play Animation",
 Default = false,
 Tooltip = "Запустить выбранную анимацию",
}):AddKeyPicker("AnimationKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Animation Key",
 NoUI = false,
})

Toggles.EnableAnimation:OnChanged(function()
 if Toggles.EnableAnimation.Value then
  playCustomAnim()
 else
  stopCustomAnim()
 end
end)

Options.SelectedAnimation:OnChanged(function()
 if Toggles.EnableAnimation and Toggles.EnableAnimation.Value then
  playCustomAnim()
 end
end)
end



end
-- ==============================================
-- Вкладка: MOVEMENT — TRACTOR SPEED (перенос из кряк.txt)
-- ==============================================
do
local TractorSpeedSection = Tabs.Movement:AddLeftGroupbox("Tractor Speed")

-- === Состояние ===
local tractorState = {
    enabled = false,
    nitroActive = false,
    conn = nil,
    gyro = nil,
    speed = 0,
}

-- === Тумблер: Speed Tractor ===
TractorSpeedSection:AddToggle("EnableTractorSpeed", {
    Text = "Speed Tractor",
    Default = false,
    Tooltip = "Увеличивает скорость трактора (TractorGreen/Red/Orange).\nНитро — удерживайте бинд ниже.",
    Callback = function(v)
        tractorState.enabled = v
        if v then
            tractorState.gyro = nil
            tractorState.speed = 0
            tractorState.conn = RunService.Heartbeat:Connect(function(dt)
                if not tractorState.enabled then
                    if tractorState.gyro then tractorState.gyro:Destroy() tractorState.gyro = nil end
                    tractorState.speed = 0
                    return
                end
                local char = LocalPlayer.Character
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if not hum then return end
                local seatPart = hum.SeatPart
                if not seatPart or not seatPart:IsA("VehicleSeat") then
                    if tractorState.gyro then tractorState.gyro:Destroy() tractorState.gyro = nil end
                    tractorState.speed = 0
                    return
                end
                local model = seatPart.Parent
                if not model then return end
                local name = model.Name
                if name ~= "TractorGreen" and name ~= "TractorRed" and name ~= "TractorOrange" then
                    if tractorState.gyro then tractorState.gyro:Destroy() tractorState.gyro = nil end
                    tractorState.speed = 0
                    return
                end
                local root = model.PrimaryPart or seatPart
                if not root then return end

                -- BodyGyro для стабилизации (только Y-вращение свободно)
                if not tractorState.gyro or tractorState.gyro.Parent ~= root then
                    if tractorState.gyro then tractorState.gyro:Destroy() end
                    local gyro = Instance.new("BodyGyro")
                    gyro.MaxTorque = Vector3.new(1e6, 0, 1e6)
                    gyro.P = 5e4
                    gyro.D = 5e3
                    gyro.CFrame = root.CFrame
                    gyro.Parent = root
                    tractorState.gyro = gyro
                end
                local gyroObj = tractorState.gyro
                gyroObj.CFrame = CFrame.new(root.Position, root.Position + Vector3.new(root.CFrame.LookVector.X, 0, root.CFrame.LookVector.Z).Unit)

                -- Сбрасываем боковое вращение
                root.AssemblyAngularVelocity = Vector3.new(0, root.AssemblyAngularVelocity.Y, 0)

                local vel = root.AssemblyLinearVelocity
                local flatVel = Vector3.new(vel.X, 0, vel.Z)
                local throttle = seatPart.Throttle
                local pressing = math.abs(throttle) > 0

                -- Базовая и нитро-скорость из слайдеров
                local baseSpeed = Options.TractorBaseSpeed and Options.TractorBaseSpeed.Value or 100
                local nitroSpeed = Options.TractorNitroSpeed and Options.TractorNitroSpeed.Value or 300
                local targetSpeed = pressing and (tractorState.nitroActive and nitroSpeed or baseSpeed) or 0

                -- Плавный набор/сброс скорости (lerp)
                tractorState.speed = tractorState.speed + (targetSpeed - tractorState.speed) * math.clamp(dt * 4, 0, 1)

                if not pressing then
                    -- Естественное торможение
                    local friction = flatVel.Magnitude > 0.5 and flatVel.Unit * -1 * 80 * dt or Vector3.zero
                    root.AssemblyLinearVelocity = vel + friction
                elseif tractorState.speed > 0.5 then
                    -- Движение по направлению взгляда сиденья
                    local seatLook = Vector3.new(seatPart.CFrame.LookVector.X, 0, seatPart.CFrame.LookVector.Z).Unit
                    local dir = throttle > 0 and seatLook or -seatLook
                    root.AssemblyLinearVelocity = dir * tractorState.speed + Vector3.new(0, vel.Y, 0)
                end
            end)
        else
            if tractorState.conn then
                tractorState.conn:Disconnect()
                tractorState.conn = nil
            end
            if tractorState.gyro then tractorState.gyro:Destroy() tractorState.gyro = nil end
            tractorState.speed = 0
        end
    end,
})

-- === Слайдер: Base Speed ===
TractorSpeedSection:AddSlider("TractorBaseSpeed", {
    Text = "Base Speed",
    Default = 100,
    Min = 50,
    Max = 200,
    Rounding = 0,
    Compact = false,
    Tooltip = "Базовая скорость трактора",
})

-- === Слайдер: Nitro Speed ===
TractorSpeedSection:AddSlider("TractorNitroSpeed", {
    Text = "Nitro Speed",
    Default = 300,
    Min = 150,
    Max = 500,
    Rounding = 0,
    Compact = false,
    Tooltip = "Скорость при удержании Nitro",
})

-- === Keybind: Tractor Nitro (Hold) ===
TractorSpeedSection:AddToggle("EnableTractorNitro", {
    Text = "Tractor Nitro",
    Default = false,
    NoUI = true,
}):AddKeyPicker("NitroKey", {
    Default = "LeftShift",
    SyncToggleState = false,
    Mode = "Hold",
    Text = "Nitro Key (Hold)",
    NoUI = false,
})

-- === Keybind: Tractor Jump ===
TractorSpeedSection:AddToggle("EnableTractorJump", {
    Text = "Tractor Jump",
    Default = false,
    NoUI = true,
}):AddKeyPicker("TractorJumpKey", {
    Default = "Space",
    SyncToggleState = false,
    Mode = "Toggle",
    Text = "Jump Key",
    NoUI = false,
})

-- === Слушатель InputBegan для Tractor Jump ===
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    local boundKey = Options.TractorJumpKey and Options.TractorJumpKey.Value
    if not boundKey then return end
    if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not hum or not hum.SeatPart or not hum.SeatPart:IsA("VehicleSeat") then return end
        local seatPart = hum.SeatPart
        local model = seatPart.Parent
        if not model then return end
        local name = model.Name
        if name ~= "TractorGreen" and name ~= "TractorRed" and name ~= "TractorOrange" then return end
        local root = model.PrimaryPart or seatPart
        if not root then return end
        -- Подбрасываем трактор вверх (Y velocity = 100)
        root.AssemblyLinearVelocity = Vector3.new(
            root.AssemblyLinearVelocity.X,
            100,
            root.AssemblyLinearVelocity.Z
        )
    end
end)

-- === Слушатели InputBegan/InputEnded для Nitro ===
local function checkNitro(input, isHeld)
    local boundKey = Options.NitroKey and Options.NitroKey.Value
    if not boundKey then return end
    if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then
        tractorState.nitroActive = isHeld
    end
end

UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    checkNitro(input, true)
end)

UserInputService.InputEnded:Connect(function(input)
    checkNitro(input, false)
end)
end

end

-- Вкладка: TARGET — Список игроков + действия

-- ==============================================
-- FRIEND WHITELIST — не кикать друзей
-- Загружает список друзей через GetFriendsAsync и хранит в _friendWhitelist
-- Используется в Target Loop Server, Bloodman Loop Server, Test Bomba
-- ==============================================
_friendWhitelist = {}
_friendWhitelistEnabled = false

function LoadFriendsIntoWhitelist()
    _friendWhitelist = {}
    if not LocalPlayer then return end
    local success, pages = pcall(function()
        return LocalPlayer:GetFriendsAsync()
    end)
    if not success or not pages then return end
    repeat
        local currentPage = pages:GetCurrentPage()
        for _, friend in ipairs(currentPage) do
            if friend and friend.Id then
                _friendWhitelist[friend.Id] = true
            end
        end
        if not pages.IsFinished then
            pages:AdvanceToNextPageAsync()
        else
            break
        end
    until false
    Library:Notify("Друзья загружены: " .. #_friendWhitelist .. " в вайтлисте", 2)
end

function isFriendWhitelisted(plr)
    if not _friendWhitelistEnabled then return false end
    if not plr then return false end
    return _friendWhitelist[plr.UserId] == true
end

-- ==============================================-- Вкладка: TARGET — Список игроков + действия
-- ==============================================
local TargetPlayers = Tabs.Target:AddLeftGroupbox("Игроки на сервере")
local TargetAction = Tabs.Target:AddRightGroupbox("Действие")

-- === АВАТАР + СТАТИСТИКА ИГРОКА (как в 9rr.txt 4423-4445 + 4674-4677) ===
-- Аватар сверху + 12 строк статистики под ним: Name/Username/Health/Studs/Cord/
-- In plot/Ragdoll/Kick/Left/Rejoin/Last grab/PCLD Break
TargetAvatarImage = TargetPlayers:AddImage("TargetAvatarDisplay", {
    Image = "rbxassetid://0",
    Size = Vector2.new(150, 150),
})

-- Под аватаром: DisplayName + @username
TargetAvatarLabel = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Цель не выбрана</font></b>")

-- 12 строк статистики (как в 9rr 4674-4677)
TargetStatName        = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Name:</font></b> <font color='#888888'>—</font>")
TargetStatUsername    = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Username:</font></b> <font color='#888888'>—</font>")
TargetStatHealth      = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Health:</font></b> <font color='#888888'>—</font>")
TargetStatStuds        = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#888888'>—</font>")
TargetStatCord        = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#888888'>—</font>")
TargetStatInPlot      = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>In plot:</font></b> <font color='#888888'>—</font>")
TargetStatRagdoll     = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='#888888'>—</font>")
TargetStatKick        = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Kick:</font></b> <font color='#888888'>—</font>")
TargetStatLeft        = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Left:</font></b> <font color='#888888'>—</font>")
TargetStatRejoin      = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#888888'>—</font>")
TargetStatLastGrab    = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='#888888'>None</font>")
TargetStatPCLDBreak   = TargetPlayers:AddLabel("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='#888888'>—</font>")

_targetAvatarCurrentUid = nil
_tgtStatConn = nil

-- Обновление статистики каждые 0.5с (по Heartbeat таймеру)
function tgtUpdateStats()
    if not Options.TargetPlayer then return end
    local value = Options.TargetPlayer.Value
    if not value or value == "" then return end
    local plr = tgtGetPlayerByName(value)
    if not plr or not plr.Character then return end

    local char = plr.Character
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    local lpHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

    -- Name / Username
    pcall(function() TargetStatName:SetText("<b><font color='#aaaaaa'>Name:</font></b> <font color='#ffffff'>" .. tostring(plr.DisplayName) .. "</font>") end)
    pcall(function() TargetStatUsername:SetText("<b><font color='#aaaaaa'>Username:</font></b> <font color='#ffffff'>" .. tostring(plr.Name) .. "</font>") end)

    -- Health (зелёный если > 50, жёлтый 1-50, красный если 0)
    if hum then
        local hp = math.floor(hum.Health)
        local hpMax = math.floor(hum.MaxHealth)
        local pct = hpMax > 0 and math.floor((hp / hpMax) * 100) or 0
        local color = "#55ff55"
        if pct <= 0 then color = "#ff5555"
        elseif pct <= 50 then color = "#ffff55" end
        pcall(function() TargetStatHealth:SetText("<b><font color='#aaaaaa'>Health:</font></b> <font color='" .. color .. "'>" .. pct .. "%</font>") end)
    end

    -- Studs (дистанция от тебя до цели)
    if hrp and lpHrp then
        local studs = math.floor((lpHrp.Position - hrp.Position).Magnitude)
        pcall(function() TargetStatStuds:SetText("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#ffffff'>" .. studs .. "</font>") end)
    end

    -- Cord (координаты цели X, Y, Z)
    if hrp then
        local pos = hrp.Position
        pcall(function() TargetStatCord:SetText("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#ffffff'>" .. math.floor(pos.X) .. ", " .. math.floor(pos.Y) .. ", " .. math.floor(pos.Z) .. "</font>") end)
    end

    -- In plot (зелёный Yes / красный No)
    local inPlot = false
    local okPlot = pcall(function()
        inPlot = plr:FindFirstChild("InPlot") and plr.InPlot.Value or false
    end)
    local plotColor = inPlot and "#55ff55" or "#ff5555"
    pcall(function() TargetStatInPlot:SetText("<b><font color='#aaaaaa'>In plot:</font></b> <font color='" .. plotColor .. "'>" .. (inPlot and "Yes" or "No") .. "</font>") end)

    -- Ragdoll (зелёный No / красный Yes)
    local isRagdolled = false
    if hum then
        local ragVal = hum:FindFirstChild("Ragdolled")
        isRagdolled = ragVal and ragVal.Value or false
    end
    local ragColor = isRagdolled and "#ff5555" or "#55ff55"
    pcall(function() TargetStatRagdoll:SetText("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='" .. ragColor .. "'>" .. (isRagdolled and "Yes" or "No") .. "</font>") end)

    -- Kick (пусто если нет, иначе имя кикера)
    local kickText = "—"
    local kickColor = "#888888"
    pcall(function()
        local kickAttr = char:GetAttribute("KickedBy")
        if kickAttr and kickAttr ~= "" then
            kickText = tostring(kickAttr)
            kickColor = "#ff5555"
        end
    end)
    pcall(function() TargetStatKick:SetText("<b><font color='#aaaaaa'>Kick:</font></b> <font color='" .. kickColor .. "'>" .. kickText .. "</font>") end)

    -- Left (время до выхода) — узнаём через GetGameSessionInfo если доступно
    local leftText = "—"
    pcall(function()
        -- Пробуем разные атрибуты которые могут содержать время до кика
        local leaveTime = plr:GetAttribute("TimeUntilKick")
        if leaveTime and leaveTime > 0 then
            local h = math.floor(leaveTime / 3600)
            local m = math.floor((leaveTime % 3600) / 60)
            local s = math.floor(leaveTime % 60)
            leftText = string.format("%d (%02d:%02d:%02d)", leaveTime, h, m, s)
        end
    end)
    pcall(function() TargetStatLeft:SetText("<b><font color='#aaaaaa'>Left:</font></b> <font color='#ffffff'>" .. leftText .. "</font>") end)

    -- Rejoin (похожее, но отдельный атрибут)
    local rejoinText = "—"
    pcall(function()
        local rejoinTime = plr:GetAttribute("TimeUntilRejoin")
        if rejoinTime and rejoinTime > 0 then
            local h = math.floor(rejoinTime / 3600)
            local m = math.floor((rejoinTime % 3600) / 60)
            local s = math.floor(rejoinTime % 60)
            rejoinText = string.format("%d (%02d:%02d:%02d)", rejoinTime, h, m, s)
        end
    end)
    pcall(function() TargetStatRejoin:SetText("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#ffffff'>" .. rejoinText .. "</font>") end)

    -- Last grab — имя последнего кто хватал цель (через атрибут)
    local lastGrabText = "None"
    local lastGrabColor = "#ff9955"
    pcall(function()
        local grabber = char:GetAttribute("LastGrabber")
        if grabber and grabber ~= "" then
            lastGrabText = tostring(grabber)
            lastGrabColor = "#ffaa55"
        end
    end)
    pcall(function() TargetStatLastGrab:SetText("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='" .. lastGrabColor .. "'>" .. lastGrabText .. "</font>") end)

    -- PCLD Break (зелёный No / красный Yes)
    local pcldBreak = false
    pcall(function()
        local pcld = char:GetAttribute("PCLDBreak")
        pcldBreak = pcld == true
    end)
    local pcldColor = pcldBreak and "#ff5555" or "#55ff55"
    pcall(function() TargetStatPCLDBreak:SetText("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='" .. pcldColor .. "'>" .. (pcldBreak and "Yes" or "No") .. "</font>") end)
end

-- Запускаем Heartbeat-лупу для обновления статистики (каждые 0.5 сек)
task.spawn(function()
    while true do
        if Options.TargetPlayer and Options.TargetPlayer.Value and Options.TargetPlayer.Value ~= "" then
            pcall(tgtUpdateStats)
        end
        task.wait(0.5)
    end
end)

function tgtUpdateAvatarDisplay()
    if not Options.TargetPlayer then return end
    local value = Options.TargetPlayer.Value
    if not value or value == "" then
        _targetAvatarCurrentUid = nil
        pcall(function() TargetAvatarImage:SetImage("rbxassetid://0") end)
        pcall(function() TargetAvatarLabel:SetText("<b><font color='#aaaaaa'>Цель не выбрана</font></b>") end)
        -- Сброс статистики
        pcall(function() TargetStatName:SetText("<b><font color='#aaaaaa'>Name:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatUsername:SetText("<b><font color='#aaaaaa'>Username:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatHealth:SetText("<b><font color='#aaaaaa'>Health:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatStuds:SetText("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatCord:SetText("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatInPlot:SetText("<b><font color='#aaaaaa'>In plot:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatRagdoll:SetText("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatKick:SetText("<b><font color='#aaaaaa'>Kick:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatLeft:SetText("<b><font color='#aaaaaa'>Left:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatRejoin:SetText("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#888888'>—</font>") end)
        pcall(function() TargetStatLastGrab:SetText("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='#888888'>None</font>") end)
        pcall(function() TargetStatPCLDBreak:SetText("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='#888888'>—</font>") end)
        return
    end
    local plr = tgtGetPlayerByName(value)
    if not plr then
        _targetAvatarCurrentUid = nil
        pcall(function() TargetAvatarImage:SetImage("rbxassetid://0") end)
        pcall(function() TargetAvatarLabel:SetText("<b><font color='#ff5555'>Игрок не найден</font></b>") end)
        return
    end
    local uid = plr.UserId
    if uid == _targetAvatarCurrentUid then return end
    _targetAvatarCurrentUid = uid
    -- Обновляем лейбл сразу (DisplayName + @username)
    pcall(function()
        TargetAvatarLabel:SetText("<b><font color='#ffffff'>" .. plr.DisplayName .. "</font></b> <font color='#888888'>(@" .. plr.Name .. ")</font>")
    end)
    -- Загружаем аватар асинхронно
    task.spawn(function()
        local ok, content = pcall(Players.GetUserThumbnailAsync, Players, uid, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
        if ok and content and content ~= "" and plr.Parent then
            -- Проверяем что цель не сменилась
            if _targetAvatarCurrentUid == uid then
                pcall(function() TargetAvatarImage:SetImage(content) end)
            end
        end
    end)
end

-- Список игроков: "DisplayName (@username)"
local function tgtPlayerList()
 local list = {}
 for _, p in ipairs(Players:GetPlayers()) do
 if p ~= LocalPlayer then
 table.insert(list, p.DisplayName .. " (@" .. p.Name .. ")")
 end
 end
 return list
end

-- Извлекаем username из "DisplayName (@username)"
function tgtGetPlayerByName(value)
 if not value or value == "" then return nil end
 local username = value:match("@(%S-)%)")
 if username then
 return Players:FindFirstChild(username)
 end
 return Players:FindFirstChild(value)
end

TargetPlayers:AddDropdown("TargetPlayer", {
 Text = "Цель",
 Values = tgtPlayerList(),
 Default = (tgtPlayerList())[1] or nil,
 Tooltip = "Список игроков обновляется сам",
 Callback = function() tgtUpdateAvatarDisplay() end,
})

local function tgtRefresh()
 if not Options.TargetPlayer then return end
 local list = tgtPlayerList()
 pcall(function() Options.TargetPlayer:SetValues(list) end)
 local cur = Options.TargetPlayer.Value
 local stillHere = false
 for _, n in ipairs(list) do if n == cur then stillHere = true break end end
 if (not stillHere) and #list > 0 then
 pcall(function() Options.TargetPlayer:SetValue(list[1]) end)
 end
 tgtUpdateAvatarDisplay()
end

Players.PlayerAdded:Connect(function() task.wait(0.3) tgtRefresh() end)
Players.PlayerRemoving:Connect(function() task.defer(tgtRefresh) end)

-- Стартовое обновление аватара (через кадр после создания dropdown'а)
task.spawn(function() task.wait(0.1) tgtUpdateAvatarDisplay() end)


TargetPlayers:AddButton({
 Text = "Обновить список",
 Func = function() tgtRefresh() end,
})

-- Loop toggles
TargetPlayers:AddToggle("TargetLoopPlayer", {
 Text = "Loop Player",
 Default = false,
 Tooltip = "Бесконечно применять действие к выбранной цели",
})

TargetPlayers:AddToggle("TargetLoopServer", {
 Text = "Loop Server",
 Default = false,
 Tooltip = "Бесконечно применять действие ко всему серверу",
})

TargetPlayers:AddToggle("TargetSkipFriends", {
 Text = "Не трогать друзей",
 Default = false,
 Tooltip = "Исключает друзей из кика по всему серверу (Loop Server / Server кнопка)",
})

-- === DESTROY HEIGHT dropdown ===
TargetAction:AddDropdown("DestroyHeightMode", {
 Text = "Destroy Height",
 Values = {"Spawn", "Heaven"},
 Default = "Spawn",
 Multi = false,
 Tooltip = "Spawn = высота 35 (как в 9rr). Heaven = 1e9 (в космос)",
})

-- === LOOP SERVER handler ===
Toggles.TargetLoopServer:OnChanged(function()
 if Toggles.TargetLoopServer.Value then
  Library:Notify("Destroy Server: запускаю (с лагами!)...", 2)
  task.spawn(function()
   while Toggles.TargetLoopServer.Value do
    local heightMode = (Options.DestroyHeightMode and Options.DestroyHeightMode.Value) or "Spawn"
    local height = (heightMode == "Heaven") and 1e9 or 35

    tgtStartLineLag()
    task.wait(1)

    local players = {}
    for _, plr in ipairs(Players:GetPlayers()) do
     if plr ~= LocalPlayer and not isFriendWhitelisted(plr) then
      table.insert(players, plr)
     end
    end
    if #players == 0 then tgtStopLineLag() task.wait(1) continue end

    local myChar = LocalPlayer.Character
    local myHrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myHrp then tgtStopLineLag() task.wait(1) continue end

    local playerData = {}
    for _, plr in ipairs(players) do
     local char = plr.Character
     local hrp = char and char:FindFirstChild("HumanoidRootPart")
     if hrp then table.insert(playerData, {player = plr, hrp = hrp}) end
    end

    for _, data in ipairs(playerData) do
     pcall(function() myHrp.CFrame = data.hrp.CFrame * CFrame.new(0, 5, 5) end)
     task.wait(0.2)
     local GE = ReplicatedStorage:FindFirstChild("GrabEvents")
     local setOwner = GE and GE:FindFirstChild("SetNetworkOwner")
     if setOwner and data.hrp then
      pcall(function() setOwner:FireServer(data.hrp, data.hrp.CFrame) end)
     end
     task.wait()
    end

    pcall(function()
     myHrp.CFrame = myHrp.CFrame
     myHrp.AssemblyLinearVelocity = Vector3.zero
    end)

    local radius = 40
    local angleStep = (math.pi * 2) / #playerData
    for idx, data in ipairs(playerData) do
     local angle = (idx - 1) * angleStep
     local x = math.cos(angle) * radius
     local z = math.sin(angle) * radius

     pcall(function()
      data.hrp.CFrame = CFrame.new(x, height, z)
      data.hrp.AssemblyLinearVelocity = Vector3.zero
     end)

     local bp = Instance.new("BodyPosition")
     bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
     bp.P = 40000000
     bp.Position = Vector3.new(x, height, z)
     bp.Parent = data.hrp
     task.delay(2, function() pcall(function() bp:Destroy() end) end)
     task.wait()
    end

    local GE = ReplicatedStorage:FindFirstChild("GrabEvents")
    local destroyLine = GE and GE:FindFirstChild("DestroyGrabLine")
    if destroyLine then
     for i = 1, 8 do
      for _, data in ipairs(playerData) do
       pcall(function() destroyLine:FireServer(data.hrp) end)
      end
      task.wait(0.3)
     end
    end

    tgtStopLineLag()
    task.wait(1)
   end
  end)
 else
  tgtStopLineLag()
  Library:Notify("Destroy Server: остановлено", 2)
 end
end)

-- Bring helper: teleport target player(s) to me
tgtBringLoop = false
tgtBringInit = false  -- have we taken ownership yet?

function getGE()
    return ReplicatedStorage:FindFirstChild("GrabEvents")
end

function tgtBringTeleport(targetName, isFirstFrame)
    local target = Players:FindFirstChild(targetName)
    if not target then return end
    local tChar = target.Character
    local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
    local tHum = tChar and tChar:FindFirstChild("Humanoid")
    local myChar = LocalPlayer.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    local myHum = myChar and myChar:FindFirstChildOfClass("Humanoid")
    if not (tRoot and tHum and tHum.Health > 0 and myRoot and myHum) then return end
    
    local GE = GE_Bloodman or getGE()
    if not GE then return end
    local sno = GE:FindFirstChild("SetNetworkOwner")
    if not sno then return end
    local cgl = createGrabLineEvent or GE:FindFirstChild("CreateGrabLine")
    local dgl = GE:FindFirstChild("DestroyGrabLine")
    
    if isFirstFrame then
        -- Save my position
        local savedPos = myRoot.CFrame
        local bringPos = savedPos * CFrame.new(0, 0, 3)
        
        -- Sit on blobman (like kick does)
        if not sitOnBlobman() then return end
        task.wait(0.2)
        
        local seat = myHum.SeatPart
        if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then return end
        local blob = seat.Parent
        local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart
        
        -- Get blobman remotes
        local remoteFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
        local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
        local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
        local L_Det = blob:FindFirstChild("LeftDetector")
        local R_Det = blob:FindFirstChild("RightDetector")
        local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
        local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))
        
        -- Phase 1: Teleport to target
        myRoot.CFrame = tRoot.CFrame
        if blobRoot then blobRoot.CFrame = tRoot.CFrame end
        task.wait(0.1)
        
        -- Blobman dual hand spam (grab + release)
        if grab and drop and L_Weld and R_Weld then
            pcall(function()
                grab:FireServer(L_Det, tRoot, L_Weld)
                grab:FireServer(R_Det, tRoot, R_Weld)
                drop:FireServer(L_Weld, tRoot)
                drop:FireServer(R_Weld, tRoot)
            end)
        end
        
        -- Take ownership + create grab line
        pcall(function()
            tHum.PlatformStand = true
            tRoot.AssemblyLinearVelocity = Vector3.zero
            sno:FireServer(tRoot, myRoot.CFrame)
            if cgl then
                cgl:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
            end
        end)
        
        -- Wait for grab to register (0.5s like kick)
        task.wait(0.5)
        
        -- Phase 2: Teleport back, bring target with me
        myRoot.CFrame = savedPos
        myRoot.AssemblyLinearVelocity = Vector3.zero
        if blobRoot then
            blobRoot.CFrame = savedPos
            blobRoot.AssemblyLinearVelocity = Vector3.zero
        end
        
        -- Set target to my position
        pcall(function()
            tRoot.CFrame = bringPos
            tRoot.AssemblyLinearVelocity = Vector3.zero
            tHum.PlatformStand = true
            sno:FireServer(tRoot, bringPos)
            if dgl then dgl:FireServer(tRoot) end
            if cgl then
                cgl:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
            end
        end)
        task.wait(0.1)
        
        -- Cleanup: destroy grab line
        if dgl then
            for _ = 1, 5 do
                pcall(function() dgl:FireServer(tRoot) end)
                task.wait(0.01)
            end
        end
        
        -- Get off blobman
        pcall(function() myHum.Sit = false end)
        task.wait(0.1)
        
        -- Destroy blobman
        local folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        local blobToDestroy = folder and folder:FindFirstChild("CreatureBlobman")
        if blobToDestroy then
            local destroyRem = ReplicatedStorage:FindFirstChild("MenuToys")
            destroyRem = destroyRem and destroyRem:FindFirstChild("DestroyToy")
            if destroyRem then destroyRem:FireServer(blobToDestroy) end
        end
    else
        -- Loop frames: keep target at my position
        local bringPos = myRoot.CFrame * CFrame.new(0, 0, 3)
        pcall(function()
            tHum.PlatformStand = true
            sno:FireServer(tRoot, bringPos)
            if cgl then
                cgl:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
            end
            tRoot.CFrame = bringPos
            tRoot.AssemblyLinearVelocity = Vector3.zero
        end)
    end
end

function tgtBringAll(isFirstFrame)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            tgtBringTeleport(plr.Name, isFirstFrame)
        end
    end
end

-- One-time buttons
TargetPlayers:AddButton({
 Text = "Player (1 раз)",
 Tooltip = "Применить выбранные действия к выбранному игроку 1 раз (5 сек на действие)",
 Func = function()
 local actionsDict = Options.TargetMethod and Options.TargetMethod.Value or {}
 -- Linoria Multi возвращает словарь {["Kick"]=true,...} — конвертируем в массив
 local actions = {}
 if type(actionsDict) == "string" then
 actions = {actionsDict}
 else
 for actionName, isOn in pairs(actionsDict) do
 if isOn then table.insert(actions, actionName) end
 end
 end
 local rawValue = Options.TargetPlayer and Options.TargetPlayer.Value or ""
 local targetPlayer = tgtGetPlayerByName(rawValue)
 local targetName = targetPlayer and targetPlayer.Name or ""
 if targetName == "" then
 Library:Notify("Target: выбери игрока", 3)
 return
 end
 if #actions == 0 then
 Library:Notify("Target: выбери действие в списке", 3)
 return
 end
 for _, action in ipairs(actions) do
 if action == "Teleport" then
 local target = Players:FindFirstChild(targetName)
 local tChar = target and target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local myChar = LocalPlayer.Character
 local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
 if tRoot and myRoot then
 myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 5)
 Library:Notify("Телепорт к " .. targetName, 2)
 end
 elseif action == "View" then
 local target = Players:FindFirstChild(targetName)
 local tChar = target and target.Character
 local tHead = tChar and tChar:FindFirstChild("Head")
 local cam = workspace.CurrentCamera
 local myChar = LocalPlayer.Character
 local myHead = myChar and myChar:FindFirstChild("Head")
 if cam and tHead and myHead then
 cam.CFrame = CFrame.lookAt(myHead.Position, tHead.Position)
 Library:Notify("View на " .. targetName, 2)
 end
 else
 -- Toy/V3 действия: даём 8 сек на выполнение (Lock держит 5 сек + запас)
 task.spawn(function()
 local startTime = tick()
 tgtRunAction(action, targetName, function()
 return tick() - startTime < 8
 end)
 end)
 Library:Notify(action .. " → " .. targetName .. " (8 сек)", 2)
 end
 end
 end,
})

TargetPlayers:AddButton({
 Text = "Server (1 раз)",
 Tooltip = "Применить выбранные действия ко всем игрокам 1 раз",
 Func = function()
 local actionsDict = Options.TargetMethod and Options.TargetMethod.Value or {}
 local actions = {}
 if type(actionsDict) == "string" then
 actions = {actionsDict}
 else
 for actionName, isOn in pairs(actionsDict) do
 if isOn then table.insert(actions, actionName) end
 end
 end
 if #actions == 0 then
 Library:Notify("Target: выбери действие в списке", 3)
 return
 end
 for _, action in ipairs(actions) do
 if action == "Teleport" or action == "View" then
 Library:Notify(action .. ": Server не работает для этой функции", 3)
 else
 -- Для server: даём 5 сек на каждого игрока
 task.spawn(function()
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character then
 local tHum = plr.Character:FindFirstChildOfClass("Humanoid")
 if tHum and tHum.Health > 0 then
 local startTime = tick()
 tgtRunAction(action, plr.Name, function()
 return tick() - startTime < 5
 end)
 end
 end
 end
 end)
 Library:Notify(action .. " → весь сервер", 2)
 end
 end
 end,
})

-- Action dropdown
TargetAction:AddDropdown("TargetMethod", {
 Text = "Что делаем (можно несколько)",
 Values = { "Void", "Kick", "Kill", "Fling", "Lock", "Teleport", "View", "BlackHole", "BombMissile", "Banana", "Snowball", "AntiAntiKickWD", "AntiAntiKickBlackHole", "DontGiveChance", "PalletRagdoll", "RemoveTargetGucci", "FireworkMissile", "BombBalloon", "PresentBig", "PresentSmall" },
 Default = "Teleport",
 Multi = true,
 Tooltip = "Можно выбрать несколько действий — они выполнятся параллельно. Зажми Ctrl/Cmd для множественного выбора.",
 Callback = function() end,
})

TargetAction:AddSlider("ToyCount", {
 Text = "Количество игрушек",
 Default = 3,
 Min = 1,
 Max = 10,
 Rounding = 0,
 Compact = false,
 Tooltip = "Сколько игрушек спавнить (BlackHole, BombMissile, Firework, Balloon, Present)",
})

-- Teleport loop state
tgtTeleportLoop = false
-- View loop state
tgtViewLoop = false

-- Loop Player logic
Toggles.TargetLoopPlayer:OnChanged(function()
 local actionsDict = Options.TargetMethod and Options.TargetMethod.Value or {}
 local actions = {}
 if type(actionsDict) == "string" then
 actions = {actionsDict}
 else
 for actionName, isOn in pairs(actionsDict) do
 if isOn then table.insert(actions, actionName) end
 end
 end
 local rawValue = Options.TargetPlayer and Options.TargetPlayer.Value or ""
 local targetPlayer = tgtGetPlayerByName(rawValue)
 local targetName = targetPlayer and targetPlayer.Name or ""

 if Toggles.TargetLoopPlayer.Value then
 if targetName == "" then
 Library:Notify("Target: выбери игрока", 3)
 Toggles.TargetLoopPlayer:SetValue(false)
 return
 end
 if #actions == 0 then
 Library:Notify("Target: выбери действие в списке", 3)
 Toggles.TargetLoopPlayer:SetValue(false)
 return
 end

 for _, action in ipairs(actions) do
 if action == "Teleport" then
 tgtTeleportLoop = true
 task.spawn(function()
 Library:Notify("Teleport loop: " .. targetName, 2)
 while tgtTeleportLoop do
 local target = Players:FindFirstChild(targetName)
 local tChar = target and target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local myChar = LocalPlayer.Character
 local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
 if tRoot and myRoot then
 myRoot.CFrame = tRoot.CFrame * CFrame.new(0, 0, 5)
 end
 RunService.Heartbeat:Wait()
 end
 end)
 elseif action == "View" then
 tgtViewLoop = true
 task.spawn(function()
 Library:Notify("View loop: " .. targetName, 2)
 while tgtViewLoop do
 local target = Players:FindFirstChild(targetName)
 local tChar = target and target.Character
 local tHead = tChar and tChar:FindFirstChild("Head")
 local cam = workspace.CurrentCamera
 local myChar = LocalPlayer.Character
 local myHead = myChar and myChar:FindFirstChild("Head")
 if cam and tHead and myHead then
 cam.CFrame = CFrame.lookAt(myHead.Position, tHead.Position)
 end
 RunService.RenderStepped:Wait()
 end
 end)
 else
 task.spawn(function()
 Library:Notify(action .. " loop: " .. targetName, 2)
 tgtRunAction(action, targetName, function() return Toggles.TargetLoopPlayer.Value end)
 end)
 end
 end
 else
 tgtTeleportLoop = false
 tgtViewLoop = false
 end
end)

-- ==============================================
-- V3 KICK / KILL / BRING / FLING — перенос Grab Kick V3 из XOCO Script слив.txt
-- + Toy Projectiles: BlackHole, BombMissile, Banana, Snowball, Ball
-- (обёрнуто в do...end чтобы не переполнять main chunk local registers)
-- ==============================================
do

-- Глобальные ссылки на remotes (кэш)
local function getGrabEventsT()
    return ReplicatedStorage:FindFirstChild("GrabEvents") or ReplicatedStorage:WaitForChild("GrabEvents", 10)
end
local function getSpawnToyRF_T()
    local mt = ReplicatedStorage:FindFirstChild("MenuToys") or ReplicatedStorage:WaitForChild("MenuToys", 10)
    return mt and (mt:FindFirstChild("SpawnToyRemoteFunction") or mt:WaitForChild("SpawnToyRemoteFunction", 5))
end
local function getDestroyToyRE_T()
    local mt = ReplicatedStorage:FindFirstChild("MenuToys") or ReplicatedStorage:WaitForChild("MenuToys", 10)
    return mt and (mt:FindFirstChild("DestroyToy") or mt:WaitForChild("DestroyToy", 5))
end

-- SetNetworkOwner helper (как LGK_OAT_sno из слива)
local function tgtSno(part)
    if not part or not part.Parent then return end
    pcall(function() getGrabEventsT().SetNetworkOwner:FireServer(part, part.CFrame) end)
end

-- Получить цель из dropdown
local function getTgtSelectedTarget()
    if not Options.TargetPlayer then return nil end
    local rawValue = Options.TargetPlayer and Options.TargetPlayer.Value or ""
    local targetPlayer = tgtGetPlayerByName(rawValue)
    local name = targetPlayer and targetPlayer.Name or ""
    if not name or name == "" then return nil end
    return targetPlayer
end

-- Очистка AlignPosition после V3
local function cleanupKickAlign(tHRP)
    if not tHRP or not tHRP.Parent then return end
    local align = tHRP:FindFirstChild("KickAlign")
    local rot = tHRP:FindFirstChild("KickRot")
    local att0 = tHRP:FindFirstChild("KickAtt0")
    if align then
        if align.Attachment1 then align.Attachment1:Destroy() end
        align:Destroy()
    end
    if rot then rot:Destroy() end
    if att0 then att0:Destroy() end
    pcall(function() getGrabEventsT().DestroyGrabLine:FireServer(tHRP) end)
end

-- ==============================================
-- GRAB KICK V3 — общая функция для kick/kill/bring/fling
-- ==============================================
-- ==============================================
-- VOID/KILL/FLING — общая функция (механика из Bliz-T: SNOWship + CreateSkyVelocity)
-- Void: SetNetworkOwner + телепорт игрока под -12 по Y → CreateSkyVelocity (BodyVelocity вверх 1e14)
-- Kill: 50 итераций SNOWship + телепорт вниз → BreakJointsOnDeath=false + ChangeState(Dead)
-- Fling: SetNetworkOwner + BodyVelocity в случайном направлении
-- ==============================================
function tgtV3Action(mode, targetName, keepGoing)
    local target = Players:FindFirstChild(targetName)
    if not target then return end

    local myChar = LocalPlayer.Character
    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not (myChar and myHRP) then return end

    local savedPos = myHRP.CFrame
    local startTime = tick()
    local done = false

    -- VOID: одноразовый — подбрасываем цель ВВЕРХ (как Bliz-T CreateSkyVelocity)
    if mode == "void" then
        while keepGoing() and not done do
            target = Players:FindFirstChild(targetName)
            if not target or not target.Parent or not target.Character then break end
            local tChar = target.Character
            local tHRP = tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar:FindFirstChildOfClass("Humanoid")
            if tHRP and tHum and tHum.Health > 0 then
                -- Подходим к цели (дистанция ≤ 30 для SetNetworkOwner)
                local dist = (tHRP.Position - myHRP.Position).Magnitude
                if dist > 30 then
                    pcall(function() myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4)) end)
                end
                -- SetNetworkOwner (как Bliz-T SNOWship)
                pcall(function() setNetworkOwnerEvent:FireServer(tHRP, tHRP.CFrame) end)
                task.wait(0.1)
                -- CreateSkyVelocity: BodyVelocity ВВЕРХ с огромной силой (Bliz-T 4675)
                pcall(function()
                    local skyVel = tHRP:FindFirstChild("SkyVelocity")
                    if skyVel then skyVel:Destroy() end
                    skyVel = Instance.new("BodyVelocity", tHRP)
                    skyVel.Name = "SkyVelocity"
                    skyVel.Velocity = Vector3.new(0, 100000000000000, 0)
                    skyVel.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                end)
                task.wait(0.5)
                -- Телепорт цели вверх (как Bliz-T: Position.Y <= -12 → телепорт +5 -15)
                pcall(function()
                    tHRP.CFrame = CFrame.new(tHRP.Position + Vector3.new(0, 5000, 0))
                end)
                done = true
            end
            RunService.Heartbeat:Wait()
        end
        -- Возврат себя
        pcall(function() if LocalPlayer.Character then LocalPlayer.Character:PivotTo(savedPos) end end)
        return
    end

    -- KILL: точная копия performKill из XOCO слив (loop kill, 4323-4520)
    -- CameraAnchor + scheduleReturnHome + modifyTarget + performKill
    if mode == "kill" then
        local HEIGHT_LIMIT = 100000
        local TELEPORT_OFFSET = Vector3.new(6, -18.5, 0)

        -- CameraAnchor (точная копия из XOCO 4326-4356)
        local CameraAnchor = {}
        CameraAnchor.__index = CameraAnchor
        function CameraAnchor.new() return setmetatable({}, CameraAnchor) end
        function CameraAnchor:attach(cf)
            self:detach()
            local p = Instance.new("Part")
            p.Name = "CameraAnchor"
            p.Size = Vector3.new(0.2, 0.2, 0.2)
            p.Transparency = 1
            p.Anchored = true
            p.CanCollide = false
            p.CFrame = cf
            p.Parent = workspace
            self.part = p
            local cam = workspace.CurrentCamera
            cam.CameraType = Enum.CameraType.Custom
            cam.CameraSubject = p
        end
        function CameraAnchor:detach()
            if self.part then self.part:Destroy() self.part = nil end
            local cam = workspace.CurrentCamera
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("Humanoid") then
                cam.CameraSubject = char.Humanoid
            else
                cam.CameraType = Enum.CameraType.Custom
            end
        end
        local cameraAnchor = CameraAnchor.new()

        local function isTooHigh(plr)
            local c = plr.Character
            local hrp = c and c:FindFirstChild("HumanoidRootPart")
            return not hrp or hrp.Position.Y > HEIGHT_LIMIT
        end

        local function setNoCollideChar(char)
            for _, v in ipairs(char:GetDescendants()) do
                if v:IsA("BasePart") then v.CanCollide = false end
            end
        end

        local function saveOriginalPos()
            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then char:SetAttribute("OriginalPosition", hrp:GetPivot()) end
        end

        local function getOriginalPos()
            local char = LocalPlayer.Character
            return char and char:GetAttribute("OriginalPosition") or nil
        end

        local function scheduleReturnHome()
            local originalPos = getOriginalPos()
            if not originalPos then return end
            local conn
            conn = RunService.Heartbeat:Connect(function()
                local char = LocalPlayer.Character
                local hrp = char and char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp:PivotTo(originalPos)
                    if _G.originalFallenHeight then
                        workspace.FallenPartsDestroyHeight = _G.originalFallenHeight
                    end
                    char:SetAttribute("SavingOriginalPos", false)
                end
                cameraAnchor:detach()
                conn:Disconnect()
            end)
        end

        local function findBlobman()
            local toys = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
            return toys and toys:FindFirstChild("CreatureBlobman") or nil
        end

        local function ensureBlobman()
            local b = findBlobman()
            if b then return b end
            local mt = ReplicatedStorage:FindFirstChild("MenuToys")
            local spawnRF = mt and mt:FindFirstChild("SpawnToyRemoteFunction")
            local myChar = LocalPlayer.Character
            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if spawnRF and myHRP then
                pcall(function() spawnRF:InvokeServer("CreatureBlobman", myHRP.CFrame * CFrame.new(0, 0, -5), Vector3.new(0, -15, 0)) end)
            end
            for _ = 1, 30 do
                task.wait(0.1)
                b = findBlobman()
                if b then return b end
            end
            return nil
        end

        local function modifyTarget(root, hum)
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

            local bv = Instance.new("BodyVelocity")
            local bav = Instance.new("BodyAngularVelocity")
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

        -- performKill — точная копия из XOCO 4464-4500
        local function performKill()
            local target = Players:FindFirstChild(targetName)
            if not target then return end
            local tChar = target.Character
            local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
            local tHead = tChar and tChar:FindFirstChild("Head")

            if not (target and tRoot and tHum and tHead) then return end
            if isTooHigh(target) then return end
            if tHum:GetState() == Enum.HumanoidStateType.Dead then return end

            local char = LocalPlayer.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not (char and hrp) then return end

            if not char:GetAttribute("SavingOriginalPos") then
                saveOriginalPos()
            end
            char:SetAttribute("SavingOriginalPos", true)
            _G.originalFallenHeight = workspace.FallenPartsDestroyHeight
            workspace.FallenPartsDestroyHeight = 0/0

            local originalPos = getOriginalPos()
            if originalPos then cameraAnchor:attach(originalPos) end

            hrp:PivotTo(CFrame.new(tRoot.Position + TELEPORT_OFFSET))
            setNoCollideChar(tChar)
            pcall(function() setNetworkOwnerEvent:FireServer(tRoot, tRoot.CFrame) end)
            task.wait(0.05)
            pcall(function() destroyGrabLineEvent:FireServer(tRoot) end)
            task.wait(0.05)

            if tHead:FindFirstChild("PartOwner") and tHead.PartOwner.Value == LocalPlayer.Name then
                task.wait(0.05)
                modifyTarget(tRoot, tHum)
            end
            scheduleReturnHome()
        end

        -- Точная копия XOCO: KillHB = R.Heartbeat:Connect(performKill)
        -- Каждый Heartbeat запускает performKill в отдельном потоке — не блокирует
        local KillHB
        KillHB = RunService.Heartbeat:Connect(function()
            if not keepGoing() then
                KillHB:Disconnect()
                KillHB = nil
                cameraAnchor:detach()
                pcall(function() if LocalPlayer.Character then LocalPlayer.Character:PivotTo(savedPos) end end)
                return
            end
            performKill()
        end)

        -- Ждём пока KillHB не отключится (когда keepGoing() станет false)
        while KillHB and KillHB.Connected do
            task.wait(0.5)
        end
        return
    end

    -- FLING: одноразовый пушёк (как Bliz-T SNOWship + BodyVelocity)
    if mode == "fling" then
        while keepGoing() and not done do
            target = Players:FindFirstChild(targetName)
            if not target or not target.Parent or not target.Character then break end
            local tChar = target.Character
            local tHRP = tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar:FindFirstChildOfClass("Humanoid")
            if tHRP and tHum and tHum.Health > 0 then
                -- Подходим
                local dist = (tHRP.Position - myHRP.Position).Magnitude
                if dist > 30 then
                    pcall(function() myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4)) end)
                end
                -- SetNetworkOwner
                pcall(function() setNetworkOwnerEvent:FireServer(tHRP, tHRP.CFrame) end)
                task.wait(0.1)
                -- Парализуем
                pcall(function()
                    tHum.Sit = false
                    tHum:ChangeState(Enum.HumanoidStateType.Running)
                    tHum:SetStateEnabled(Enum.HumanoidStateType.Seated, false)
                    tHum:ChangeState(Enum.HumanoidStateType.GettingUp)
                end)
                -- BodyVelocity в случайном направлении + BodyAngularVelocity (как Bliz-T modifyTarget)
                pcall(function()
                    local bv = Instance.new("BodyVelocity")
                    bv.MaxForce = Vector3.new(1e7, 1e7, 1e7)
                    bv.P = 1e6
                    bv.Velocity = Vector3.new(math.random(-500, 50), -50, math.random(-50, 50))
                    bv.Parent = tHRP
                    local bav = Instance.new("BodyAngularVelocity")
                    bav.MaxTorque = Vector3.new(1e7, 1e7, 1e7)
                    bav.P = 1e6
                    bav.AngularVelocity = Vector3.new(math.random(-500, 300), math.random(-300, 300), math.random(-500, 500))
                    bav.Parent = tHRP
                    tHum.BreakJointsOnDeath = false
                    task.delay(2, function()
                        if bv.Parent then bv:Destroy() end
                        if bav.Parent then bav:Destroy() end
                    end)
                end)
                done = true
            end
            RunService.Heartbeat:Wait()
        end
        pcall(function() if LocalPlayer.Character then LocalPlayer.Character:PivotTo(savedPos) end end)
        return
    end

    -- KICK (V3): AlignPosition тянет цель над головой + спам DestroyGrabLine
    -- Оригинальная механика Grab Kick V3 из XOCO слив 5321
    local lastRemoteFire = tick()
    while keepGoing() do
        target = Players:FindFirstChild(targetName)
        if not target or not target.Parent then break end
        myChar = LocalPlayer.Character
        myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        local myHead = myChar and myChar:FindFirstChild("Head")
        local tChar = target.Character
        local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
        local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
        if not (myChar and myHRP and myHead) or not (tHRP and tHum) or tHum.Health <= 0 then
            RunService.Heartbeat:Wait()
            continue
        end
        local dist = (tHRP.Position - myHRP.Position).Magnitude
        if dist <= 30 then
            -- Создаём AlignPosition
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
            tgtSno(tHRP)
            local align = tHRP:FindFirstChild("KickAlign")
            if align and align.Attachment1 then
                align.Attachment1.WorldPosition = myHead.Position + Vector3.new(0, 15, 0)
            end
            local rot = tHRP:FindFirstChild("KickRot")
            if rot then rot.CFrame = CFrame.Angles(0, 0, 0) end
            if tick() - lastRemoteFire > 0.05 then
                pcall(function() getGrabEventsT().DestroyGrabLine:FireServer(tHRP) end)
                lastRemoteFire = tick()
            end
        else
            pcall(function() myChar:PivotTo(tHRP.CFrame * CFrame.new(0, 2, 4)) end)
            tgtSno(tHRP)
        end
        RunService.Heartbeat:Wait()
    end
    -- Очистка
    if target and target.Character then
        local tH = target.Character:FindFirstChild("HumanoidRootPart")
        if tH then cleanupKickAlign(tH) end
    end
    pcall(function() if LocalPlayer.Character then LocalPlayer.Character:PivotTo(savedPos) end end)
end

-- ==============================================
-- LOCK (Ragdoll grab) — перенос из XOCO слив.txt 4213
-- Точная механика: телепорт к цели → SetNetworkOwner + CreateGrabLine
-- через 0.6 сек → возврат на savedPos → цель на 30 studs выше с random angle
-- ==============================================
function tgtLockLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local target = Players:FindFirstChild(targetName)
    if not target then return end

    local myChar = LocalPlayer.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then return end

    local GE = getGrabEventsT()
    if not GE then return end

    local savedPos = myRoot.CFrame
    local dragging = false
    local grabStartTime = 0
    local holdStartTime = 0
    local holdHeight = 30
    local lockStartTime = nil  -- когда начали удержание цели

    while keepGoing() do
        target = Players:FindFirstChild(targetName)
        if not target or not target.Parent or not target.Character then break end

        local tChar = target.Character
        local tRoot = tChar:FindFirstChild("HumanoidRootPart")
        local tHum = tChar:FindFirstChildOfClass("Humanoid")

        if tRoot and tHum and tHum.Health > 0 then
            if not dragging then
                -- PHASE 1: THE TELEPORT GRAB
                myRoot.CFrame = tRoot.CFrame
                pcall(function()
                    tHum.PlatformStand = true
                    tHum.Sit = true
                    GE.SetNetworkOwner:FireServer(tRoot, myRoot.CFrame)
                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                end)

                if grabStartTime == 0 then grabStartTime = tick() end
                if tick() - grabStartTime > 0.6 then
                    dragging = true
                    lockStartTime = tick()
                end
            else
                -- PHASE 2: LOCK & HOLD (держим цель над собой)
                myRoot.CFrame = savedPos
                myRoot.AssemblyLinearVelocity = Vector3.zero

                local randomAngle = CFrame.Angles(
                    math.rad(math.random(-180, 180)),
                    math.rad(math.random(-180, 180)),
                    math.rad(math.random(-180, 180))
                )
                local lockCFrame = (savedPos * CFrame.new(0, holdHeight, 0)) * randomAngle
                tRoot.CFrame = lockCFrame

                pcall(function()
                    tHum.PlatformStand = true
                    tHum.Sit = false
                    GE.SetNetworkOwner:FireServer(tRoot, lockCFrame)
                    GE.CreateGrabLine:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
                end)

                -- Если удерживаем больше 5 сек — выходим (для одноразового режима)
                if lockStartTime and tick() - lockStartTime >= 5 then
                    break
                end
            end
        else
            dragging = false
            grabStartTime = 0
            lockStartTime = nil
        end

        RunService.Heartbeat:Wait()
    end

    -- CLEANUP: цель просто падает где есть
    if target and target.Character then
        local tH = target.Character:FindFirstChild("HumanoidRootPart")
        if tH then
            pcall(function() GE.DestroyGrabLine:FireServer(tH) end)
        end
    end
    pcall(function()
        if LocalPlayer.Character then
            LocalPlayer.Character:PivotTo(savedPos)
        end
    end)
end

-- ==============================================
-- TOY PROJECTILES — общие функции
-- Механики взяты из кряк.txt (Explosion Missile 8249)
-- и XOCO слив.txt (Ragdoll Snowball 5638, Loop Banana Ragdoll 4925)
-- ==============================================

-- === BOMB MISSILE — спавним N ракет + телепорт к цели + BombExplode ===
-- Без BodyVelocity (чтобы не взрывались в полёте) — сразу телепорт body к цели
function tgtBombMissileLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end
    local toyFolderName = LocalPlayer.Name .. "SpawnedInToys"

    local folder = workspace:FindFirstChild(toyFolderName) or workspace:WaitForChild(toyFolderName, 5)
    if not folder then return end

    local bombEvents = ReplicatedStorage:FindFirstChild("BombEvents")
    local bombExplode = bombEvents and bombEvents:FindFirstChild("BombExplode")

    local pendingRockets = {}

    -- Слушаем новые игрушки
    local conn
    conn = folder.ChildAdded:Connect(function(child)
        if not keepGoing() then return end
        if child.Name == "BombMissile" and child:WaitForChild("ThisToysNumber", 3) then
            task.spawn(function()
                pcall(function()
                    local body = child:FindFirstChild("Body") or child:FindFirstChild("PartHitDetector")
                    if not body then return end
                    -- Забираем ownership
                    pcall(function() setNetworkOwnerEvent:FireServer(body, body.CFrame) end)
                    pcall(function() createGrabLineEvent:FireServer(body, Vector3.zero, body.Position, false) end)
                    task.wait(0.05)
                    pcall(function() destroyGrabLineEvent:FireServer(body) end)
                    task.wait(0.05)
                    -- Стабилизируем (BodyVelocity = 0)
                    local stableBV = Instance.new("BodyVelocity", body)
                    stableBV.Name = "Stable"
                    stableBV.Velocity = Vector3.new(0, 0, 0)
                    stableBV.MaxForce = Vector3.new(1, 1, 1) * math.huge
                    -- Телепорт высоко вверх (кэшируем)
                    body.CFrame = CFrame.new(math.random(-1000, 1000), 10000, math.random(-1000, 1000))
                end)
                table.insert(pendingRockets, child)
            end)
        end
    end)

    -- Основной цикл
    while keepGoing() do
        local count = 3
        if Options.ToyCount then
            count = Options.ToyCount.Value or 3
        end

        -- Если накопили достаточно — доставляем к цели и взрываем
        if #pendingRockets >= count then
            local target = Players:FindFirstChild(targetName)
            local tRoot = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
            if tRoot then
                local toExplode = {}
                for i = #pendingRockets, 1, -1 do
                    local rocket = pendingRockets[i]
                    if rocket and rocket.Parent then
                        table.insert(toExplode, rocket)
                    end
                    table.remove(pendingRockets, i)
                end
                -- Взрываем каждую у цели
                for _, child in ipairs(toExplode) do
                    task.spawn(function()
                        pcall(function()
                            local body = child:FindFirstChild("Body") or child:FindFirstChild("PartHitDetector")
                            if not body then return end
                            -- Телепорт body к цели 3 раза
                            for _ = 1, 3 do
                                if not body or not body.Parent then return end
                                body.CFrame = CFrame.new(tRoot.Position)
                                task.wait(0.05)
                            end
                            task.wait(0.05)
                            -- BombExplode (с полными параметрами)
                            if bombExplode then
                                pcall(function()
                                    bombExplode:FireServer({
                                        ["Radius"] = 17.5,
                                        ["TimeLength"] = 2,
                                        ["Hitbox"] = child:FindFirstChild("PartHitDetector") or body,
                                        ["ExplodesByFire"] = false,
                                        ["MaxForcePerStudSquared"] = 225,
                                        ["Model"] = child,
                                        ["ImpactSpeed"] = 100,
                                        ["ExplodesByPointy"] = false,
                                        ["DestroysModel"] = false,
                                        ["PositionPart"] = body
                                    }, tRoot.Position)
                                end)
                            end
                        end)
                    end)
                end
            end
        end

        -- Если не хватает ракет — спавним новую
        if #pendingRockets < count then
            local myChar = LocalPlayer.Character
            local myHead = myChar and myChar:FindFirstChild("Head")
            if myHead then
                task.spawn(function()
                    pcall(function() spawnRF:InvokeServer("BombMissile", myHead.CFrame, Vector3.zero) end)
                end)
            end
        end
        task.wait(0.5)
    end

    if conn then conn:Disconnect() end
    for _ = 1, #pendingRockets do table.remove(pendingRockets) end

    -- Очистка
    if folder and destroyRE then
        for _, missile in ipairs(folder:GetChildren()) do
            if missile.Name == "BombMissile" then
                pcall(function() destroyRE:FireServer(missile) end)
            end
        end
    end
end

-- === BLACK HOLE — спавним BombDarkMatter и телепортируем к цели (механика из кряк) ===
function tgtBlackHoleLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end
    local toyFolderName = LocalPlayer.Name .. "SpawnedInToys"

    local folder = workspace:FindFirstChild(toyFolderName) or workspace:WaitForChild(toyFolderName, 5)
    if not folder then return end

    -- Запускаем BombDarkMatter к цели (как BombMissile, но с BombDarkMatter)
    local function launchDarkMatter(body, targetPos)
        if not body or not body.Parent then return end
        pcall(function() setNetworkOwnerEvent:FireServer(body, body.CFrame) end)
        pcall(function() createGrabLineEvent:FireServer(body, Vector3.zero, body.Position, false) end)
        task.wait(0.05)
        pcall(function() destroyGrabLineEvent:FireServer(body) end)
        -- Телепорт body к цели (как Bliz-T 4092)
        if targetPos then
            for _ = 1, 3 do
                if not body or not body.Parent then return end
                body.CFrame = CFrame.new(targetPos)
                task.wait(0.05)
            end
        end
    end

    -- Слушаем новые игрушки
    local conn
    conn = folder.ChildAdded:Connect(function(child)
        if not keepGoing() then return end
        if child.Name == "BombDarkMatter" and child:WaitForChild("ThisToysNumber", 3) then
            local toyNum = child.ThisToysNumber.Value
            local toyNumber = folder:FindFirstChild("ToyNumber")
            if toyNumber and toyNum == (toyNumber.Value - 1) then
                local body = child:FindFirstChild("Body") or child:FindFirstChild("Pyramid") or child:FindFirstChild("PartHitDetector")
                if body then
                    local target = Players:FindFirstChild(targetName)
                    local tHRP = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
                    task.spawn(function() launchDarkMatter(body, tHRP and tHRP.Position) end)
                end
            end
        end
    end)

    -- Спавним BlackHole пока включено
    while keepGoing() do
        local count = 3
        if Options.ToyCount then
            count = Options.ToyCount.Value or 3
        end
        local activeCount = 0
        for _, c in ipairs(folder:GetChildren()) do
            if c.Name == "BombDarkMatter" then activeCount = activeCount + 1 end
        end
        if activeCount < count then
            local myChar = LocalPlayer.Character
            local myHead = myChar and myChar:FindFirstChild("Head")
            if myHead then
                task.spawn(function()
                    pcall(function() spawnRF:InvokeServer("BombDarkMatter", myHead.CFrame, Vector3.zero) end)
                end)
            end
        end
        task.wait(1)
    end

    if conn then conn:Disconnect() end

    -- Очистка
    if folder and destroyRE then
        for _, bh in ipairs(folder:GetChildren()) do
            if bh.Name == "BombDarkMatter" then
                pcall(function() destroyRE:FireServer(bh) end)
            end
        end
    end
end

-- === SNOWBALL — точная механика из XOCO слив (Ragdoll Snowball 5638) ===
-- Спавним "BallSnowball" с random offset у torso цели,
-- телепортируем все существующие BallSnowball к цели (CFrame = torso.CFrame, velocity=0)
function tgtSnowballLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end
    local toyFolderName = LocalPlayer.Name .. "SpawnedInToys"

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if target and target.Character then
            local tChar = target.Character
            local torso = tChar:FindFirstChild("HumanoidRootPart") or tChar:FindFirstChild("Torso")
            if torso then
                -- 1. Спавним снежок с random offset (точная механика из слива 5673)
                local offset = Vector3.new(
                    math.random(-5, 5) / 10,
                    math.random(-5, 5) / 10,
                    math.random(-5, 5) / 10
                )
                task.spawn(function()
                    pcall(function() spawnRF:InvokeServer("BallSnowball", torso.CFrame * CFrame.new(offset), Vector3.zero) end)
                end)

                -- 2. Телепортируем все BallSnowball к цели (точная механика из слива 5679-5693)
                local folder = workspace:FindFirstChild(toyFolderName)
                if folder then
                    for _, snowball in ipairs(folder:GetChildren()) do
                        if snowball.Name == "BallSnowball" then
                            local part = snowball:IsA("BasePart") and snowball or snowball.PrimaryPart or snowball:FindFirstChildWhichIsA("BasePart")
                            if part then
                                pcall(function()
                                    part.CFrame = torso.CFrame
                                    part.AssemblyLinearVelocity = Vector3.zero
                                end)
                            end
                        end
                    end
                end
            end
        end
        RunService.Heartbeat:Wait()
    end

    -- Очистка
    local folder = workspace:FindFirstChild(toyFolderName)
    if folder and destroyRE then
        for _, snowball in ipairs(folder:GetChildren()) do
            if snowball.Name == "BallSnowball" then
                pcall(function() destroyRE:FireServer(snowball) end)
            end
        end
    end
end

-- === BANANA — механика из NoName deobf.txt (Loop Banana Ragdoll 3539) ===
-- ИСправлено: используем SpawnToy (с ожиданием ChildAdded), простой while CFP(EdiblePart)
function tgtBananaLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end
    local toyFolderName = LocalPlayer.Name .. "SpawnedInToys"

    local RS = ReplicatedStorage
    local holdEvents = RS:FindFirstChild("HoldEvents") or RS:WaitForChild("HoldEvents", 10)

    -- FWD helper (как в NoName 83)
    local function FWD(parent, partName, time)
        return parent:FindFirstChild(partName) or parent:WaitForChild(partName, time)
    end

    -- CFP helper (как в NoName 92)
    local function CFP(parent, partName)
        return parent:FindFirstChild(partName) ~= nil
    end

    -- SpawnToy helper (как в NoName 248) — спавнит и ждёт появления
    local function SpawnToyWait(toyName)
        local myChar = LocalPlayer.Character
        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then return nil end

        local spawnCF = myHRP.CFrame * CFrame.new(0, 14, 20)
        local container = workspace:FindFirstChild(toyFolderName)
        if not container then return nil end

        local spawnedObject = nil
        local conn
        conn = container.ChildAdded:Connect(function(child)
            if child.Name == toyName then
                spawnedObject = child
            end
        end)

        task.spawn(function()
            pcall(function() spawnRF:InvokeServer(toyName, spawnCF, Vector3.zero) end)
        end)

        local startT = tick()
        repeat task.wait() until spawnedObject or (tick() - startT) > 2.5
        conn:Disconnect()
        return spawnedObject
    end

    local alignPos = nil

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if not target or not target.Character then task.wait(0.1) continue end
        local tChar = target.Character
        local tLeftLeg = tChar:FindFirstChild("Left Leg")
        if not tLeftLeg then task.wait(0.1) continue end

        local folder = workspace:FindFirstChild(toyFolderName)
        if not folder then task.wait(0.1) continue end

        local banana = folder:FindFirstChild("FoodBanana")
        local soundPart = banana and banana:FindFirstChild("SoundPart")

        if not soundPart then
            -- Удаляем старые бананы
            for _, v in pairs(folder:GetChildren()) do
                if v.Name == "FoodBanana" then
                    pcall(function() destroyRE:FireServer(v) end)
                end
            end
            -- ШАГ 1: Спавним банан (через SpawnToyWait — ждём пока появится)
            banana = SpawnToyWait("FoodBanana")
            if not banana then task.wait(0.1) continue end
            soundPart = FWD(banana, "SoundPart", 5)
            if not soundPart then task.wait(0.1) continue end

            -- ШАГ 2: HoldPart → HoldItemRemoteFunction (ВЗЯТЬ В РУКИ)
            local holdPart = FWD(banana, "HoldPart", 5)
            if holdPart then
                local holdRF = FWD(holdPart, "HoldItemRemoteFunction", 5)
                if holdRF then
                    pcall(function() holdRF:InvokeServer(banana, LocalPlayer.Character) end)
                end
            end

            -- ШАГ 3: HoldEvents.Use (СКУШАТЬ)
            if holdEvents then
                local holdUse = holdEvents:FindFirstChild("Use")
                if holdUse then
                    pcall(function() holdUse:FireServer(banana) end)
                    -- Ждём пока жуём (пока есть EdiblePart) — точная механика из NoName 3568
                    while CFP(banana, "EdiblePart") and keepGoing() do
                        task.wait()
                    end
                    -- Снова Use (отпустить)
                    pcall(function() holdUse:FireServer(banana) end)
                end
            end

            -- ШАГ 4: DropItemRemoteFunction (ДРОПНУТЬ НАД ЦЕЛЬЮ)
            local holdPart2 = banana:FindFirstChild("HoldPart")
            if holdPart2 then
                local dropRF = holdPart2:FindFirstChild("DropItemRemoteFunction")
                if dropRF and LocalPlayer.Character then
                    pcall(function()
                        dropRF:InvokeServer(banana, LocalPlayer.Character:GetPivot() * CFrame.new(0, 15, -10), Vector3.zero)
                    end)
                end
            end

            -- ШАГ 5: sno(SoundPart) — SetNetworkOwner (как NoName 3576-3582)
            repeat
                task.wait(0.01)
                soundPart = banana and banana:FindFirstChild("SoundPart")
                if not soundPart then break end
                tgtSno(soundPart)
            until not soundPart or CFP(soundPart, "PartOwner") or not keepGoing()
            -- unsno (DestroyGrabLine)
            if soundPart then
                pcall(function() destroyGrabLineEvent:FireServer(soundPart) end)
            end

            -- ШАГ 6: AlignPosition на SoundPart (как NoName 3586-3592)
            local atach = Instance.new("Attachment")
            atach.Parent = soundPart
            alignPos = Instance.new("AlignPosition")
            alignPos.Responsiveness = 100
            alignPos.Parent = soundPart
            alignPos.Attachment0 = atach
        end

        -- Проверка: если банан потерял ownership — удаляем
        if banana then
            for _, v in pairs(banana:GetChildren()) do
                if CFP(v, "PartOwner") and not CheckNetworkOwnerShipOnPart(v) then
                    pcall(function() destroyRE:FireServer(banana) end)
                    banana = nil
                    break
                end
            end
        end
        if not banana then task.wait(0.1) continue end

        alignPos = soundPart:FindFirstChild("AlignPosition")
        if not alignPos then
            pcall(function() destroyRE:FireServer(banana) end)
            banana = nil
            task.wait(0.1)
            continue
        end

        -- Привязка к LeftFootAttachment цели
        local atachNew = tLeftLeg:FindFirstChild("LeftFootAttachment")
        if atachNew then
            alignPos.Attachment1 = atachNew
        end
        task.wait()
    end

    -- Очистка
    local folder = workspace:FindFirstChild(toyFolderName)
    if folder and destroyRE then
        for _, banana in ipairs(folder:GetChildren()) do
            if banana.Name == "FoodBanana" then
                local sp = banana:FindFirstChild("SoundPart")
                local ap = sp and sp:FindFirstChild("AlignPosition")
                if ap then ap:Destroy() end
                pcall(function() destroyRE:FireServer(banana) end)
            end
        end
    end
end

-- === ANTI ANTI KICK WD40 — механика из NoName (Anti-AntiKick WD 2766) ===
function tgtAntiAntiKickWDLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end

    local myInv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not myInv then return end

    local function FWD(parent, partName, time)
        return parent:FindFirstChild(partName) or parent:WaitForChild(partName, time)
    end

    local function changeCollision(model, state)
        for _, v in pairs(model:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = state end
        end
    end

    local function spawnToyWait(toyName)
        local myChar = LocalPlayer.Character
        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then return nil end
        local spawnCF = myHRP.CFrame * CFrame.new(0, 14, 20)
        local container = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if not container then return nil end
        local spawnedObject = nil
        local conn
        conn = container.ChildAdded:Connect(function(child)
            if child.Name == toyName then spawnedObject = child end
        end)
        task.spawn(function()
            pcall(function() spawnRF:InvokeServer(toyName, spawnCF, Vector3.zero) end)
        end)
        local startT = tick()
        repeat task.wait() until spawnedObject or (tick() - startT) > 2.5
        conn:Disconnect()
        return spawnedObject
    end

    local WD = myInv:FindFirstChild("SprayCanWD")
    local SoundPart = WD and WD:FindFirstChild("SoundPart")

    if not WD then
        WD = spawnToyWait("SprayCanWD")
        if not WD then return end
        repeat task.wait() until WD and WD:FindFirstChild("SoundPart") or not keepGoing()
        SoundPart = WD:FindFirstChild("SoundPart") or WD:WaitForChild("SoundPart", 3)
        changeCollision(WD, false)
        task.delay(1, function()
            if WD and WD.Parent then WD.Name = "Anti-Antikick" end
        end)
    end

    local HitBox = FWD(WD, "Hitbox", 5)
    if HitBox then
        repeat
            pcall(function() setNetworkOwnerEvent:FireServer(HitBox, HitBox.CFrame) end)
            task.wait(0.05)
        until not HitBox.Parent or not keepGoing() or HitBox:FindFirstChild("PartOwner")
    end

    while keepGoing() do
        SoundPart = WD and WD.Parent and WD:FindFirstChild("SoundPart")
        local target = Players:FindFirstChild(targetName)
        local tRoot = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")

        if not tRoot then
            repeat
                task.wait(0.1)
                target = Players:FindFirstChild(targetName)
                tRoot = target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
            until tRoot or not keepGoing()
        end

        if not SoundPart then
            WD = spawnToyWait("SprayCanWD")
            if not WD then task.wait(0.5) continue end
            repeat task.wait() until WD and WD:FindFirstChild("SoundPart") or not keepGoing()
            SoundPart = WD:FindFirstChild("SoundPart") or WD:WaitForChild("SoundPart", 3)
            changeCollision(WD, false)
            HitBox = FWD(WD, "Hitbox", 5)
            if HitBox then
                repeat
                    pcall(function() setNetworkOwnerEvent:FireServer(HitBox, HitBox.CFrame) end)
                    task.wait(0.05)
                until not HitBox.Parent or HitBox:FindFirstChild("PartOwner") or not keepGoing()
            end
            task.delay(1, function()
                if WD and WD.Parent then WD.Name = "Anti-Antikick" end
            end)
        end

        if SoundPart and tRoot then
            pcall(function()
                SoundPart.CFrame = tRoot.CFrame * CFrame.new(0, 0, 4)
            end)
            task.wait()
            pcall(function()
                SoundPart.CFrame = CFrame.new(-382.838318, 41.6491699, 665.570251)
                SoundPart.AssemblyAngularVelocity = Vector3.zero
                SoundPart.AssemblyLinearVelocity = Vector3.zero
            end)
        end
        task.wait(0.2)
    end

    if WD and WD.Parent and destroyRE then
        pcall(function() destroyRE:FireServer(WD) end)
    end
end

-- === ANTI ANTI KICK BLACKHOLE — механика из NoName (2908-2940) ===
function tgtAntiAntiKickBlackHoleLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end

    local myInv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
    if not myInv then return end

    local function FWD(parent, partName, time)
        return parent:FindFirstChild(partName) or parent:WaitForChild(partName, time)
    end

    -- Ищем StickyPartEvent в ReplicatedStorage.PlayerEvents (как в NoName 40)
    local playerEvents = ReplicatedStorage:FindFirstChild("PlayerEvents")
    local stickyEvent = playerEvents and (playerEvents:FindFirstChild("StickyPartEvent") or playerEvents:WaitForChild("StickyPartEvent", 5))
    -- Фолбэк: ищем везде
    if not stickyEvent then
        for _, obj in ipairs(ReplicatedStorage:GetDescendants()) do
            if obj.Name == "StickyPartEvent" and obj:IsA("RemoteEvent") then
                stickyEvent = obj
                break
            end
        end
    end

    local shurikensToBlackhole = {}

    while keepGoing() do
        -- Находим FirePlayerPart цели
        local target = Players:FindFirstChild(targetName)
        local tChar = target and target.Character
        local tHRP = tChar and tChar:FindFirstChild("HumanoidRootPart")
        local firePlayerPart = tHRP and tHRP:FindFirstChild("FirePlayerPart")

        if not stickyEvent then
            Library:Notify("AntiAntiKick BlackHole: StickyPartEvent не найден в PlayerEvents", 3)
            return
        end
        if not firePlayerPart then
            Library:Notify("AntiAntiKick BlackHole: FirePlayerPart не найден у цели", 3)
            return
        end

        -- Очищаем список
        for _ = 1, #shurikensToBlackhole do table.remove(shurikensToBlackhole) end

        local myChar = LocalPlayer.Character
        local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHRP then task.wait(0.5) continue end
        local spawnCF = myHRP.CFrame * CFrame.new(0, 14, 20)

        -- Спавним 10 кунаев и прилепляем каждый сразу (InvokeServer синхронный, но без task.wait)
        for i = 1, 10 do
            if not keepGoing() then break end
            -- InvokeServer синхронно (сервер создаёт игрушку и возвращает)
            local Shur = nil
            pcall(function() Shur = spawnRF:InvokeServer("NinjaKunai", spawnCF, Vector3.zero) end)
            -- Если InvokeServer не вернул объект — ищем его в инвентаре
            if not Shur or not Shur.Parent then
                Shur = myInv:FindFirstChild("NinjaKunai")
            end
            if Shur then
                local StickyPart = Shur:FindFirstChild("StickyPart")
                if StickyPart then
                    -- sno (SetNetworkOwner) — моментально
                    pcall(function() setNetworkOwnerEvent:FireServer(StickyPart, StickyPart.CFrame) end)
                    -- BodyPosition вверх
                    local bodyPos = Instance.new("BodyPosition")
                    bodyPos.Position = Vector3.new(math.random(-100, 100), 1e3, math.random(-100, 100))
                    bodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    bodyPos.Parent = StickyPart
                    -- Сразу прилепляем к FirePlayerPart (CFrame.Angles из NoName 1017)
                    pcall(function()
                        stickyEvent:FireServer(StickyPart, firePlayerPart, CFrame.Angles(0, math.rad(90), math.rad(90)))
                    end)
                    table.insert(shurikensToBlackhole, StickyPart)
                    -- Переименование
                    Shur.Name = "Anti-AntiKick(Kunai)"
                end
            end
            -- НЕТ task.wait между спавнами — максимум скорости
        end

        -- Дополнительный спам StickyEvent (2 раза для надёжности)
        for repeatIdx = 1, 2 do
            for _, v in pairs(shurikensToBlackhole) do
                if not v or not v.Parent then continue end
                pcall(function()
                    stickyEvent:FireServer(v, firePlayerPart, CFrame.Angles(0, math.rad(90), math.rad(90)))
                end)
            end
        end

        task.wait(2)
    end

    -- Очистка
    if myInv and destroyRE then
        for _, shur in ipairs(myInv:GetChildren()) do
            if shur.Name == "Anti-AntiKick(Kunai)" or shur.Name == "NinjaKunai" then
                pcall(function() destroyRE:FireServer(shur) end)
            end
        end
    end
end


-- === DONT GIVE CHANCE — не даёт цели сесть на блобмана ===
-- Если цель садится на блобмана (SeatPart ~= nil) — телепортируемся к ней и забираем ownership
function tgtDontGiveChanceLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local myChar = LocalPlayer.Character
    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myHRP then return end

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if not target or not target.Character then task.wait(0.1) continue end
        local tChar = target.Character
        local tHum = tChar:FindFirstChildOfClass("Humanoid")
        local tRoot = tChar:FindFirstChild("HumanoidRootPart")
        local tHead = tChar:FindFirstChild("Head")
        if not (tHum and tRoot and tHead) then task.wait(0.1) continue end

        -- Проверка: сел ли игрок на что-то (SeatPart ~= nil)
        if tHum.SeatPart ~= nil then
            local oldCF = myHRP.CFrame
            task.wait(0.1)
            -- Телепортируемся к цели
            pcall(function() myHRP.CFrame = tRoot.CFrame * CFrame.new(0, 5, 5) end)
            task.wait(0.2)
            -- Забираем ownership головы цели
            pcall(function() setNetworkOwnerEvent:FireServer(tHead, tHead.CFrame) end)
            -- Возвращаемся
            task.wait(0.1)
            pcall(function() myHRP.CFrame = oldCF end)
            task.wait(0.5)
        end
        task.wait(0.1)
    end
end

-- ==============================================
-- PALLET RAGDOLL v8 — чистая v6 + улучшения
-- Удар палеткой через AssemblyLinearVelocity (0, 100, 0), без постоянной
-- GrabLine (которая тащила цель в небо). Создаём GrabLine только на момент
-- удара, сразу DestroyGrabLine. Проверяем что цель не в воздухе и не рагдоллнута.
-- ==============================================
function tgtPalletRagdollLoop(targetName, keepGoing)
    -- Сбрасываем GrabLine на цели (мог остаться после Bloodman kick / Lock / и т.д.)
    -- Иначе ownership остаётся у блобмана/старого владельца и наша функция не сработает
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local tHead0 = target0.Character:FindFirstChild("Head")
        local GE0 = getGrabEventsT()
        if GE0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then
                if tRoot0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
                if tHead0 then pcall(function() destroyGL0:FireServer(tHead0) end) end
            end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 and tRoot0 then
                pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end)
            end
        end
    end
    task.wait(0.05) -- даём серверу обработать destroy + setNetworkOwner
    local spawnRF   = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    local GE        = getGrabEventsT()
    if not (spawnRF and destroyRE and GE) then return end

    local setNE     = GE:FindFirstChild("SetNetworkOwner")
    local destroyGL = GE:FindFirstChild("DestroyGrabLine")
    local createGL  = GE:FindFirstChild("CreateGrabLine")
    if not (setNE and createGL and destroyGL) then return end

    local lpName        = LocalPlayer.Name
    local toyFolderName = lpName .. "SpawnedInToys"

    -- sno — SetNetworkOwner спам (как в 9rr sno 6033)
    local function sno(part)
        if not part or not part.Parent then return end
        pcall(function() setNE:FireServer(part, part.CFrame) end)
    end

    -- Pre-cleanup
    local function cleanToys()
        local inv = workspace:FindFirstChild(toyFolderName)
        if not inv then return end
        for _, toy in ipairs(inv:GetChildren()) do
            if toy.Name == "RagdollPalete" or toy.Name == "PalletLightBrown" or toy.Name == "ragdoll" then
                pcall(function() destroyRE:FireServer(toy) end)
                task.wait(0.03)
                if toy.Parent then pcall(function() toy:Destroy() end) end
            end
        end
    end

    -- spawntoy (точная копия 9rr 15695)
    local function spawntoy(toyName, cf)
        local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy")
        if canSpawn and not canSpawn.Value then
            local t0 = tick()
            while canSpawn and not canSpawn.Value and tick() - t0 < 5 do
                task.wait(0.1)
            end
            if not canSpawn.Value then return nil end
        end

        local inv = workspace:FindFirstChild(toyFolderName)
        if not inv then return nil end

        local t = nil
        local toyadded
        toyadded = inv.ChildAdded:Connect(function(c)
            if c.Name == toyName then
                t = c
                toyadded:Disconnect()
            end
        end)
        task.spawn(function()
            pcall(function()
                spawnRF:InvokeServer(toyName, cf, Vector3.zero)
            end)
        end)
        local deadline = tick() + 2.5
        repeat task.wait() until t or tick() > deadline
        if toyadded then toyadded:Disconnect() end
        return t
    end

    -- Подготовка палетки (спавн + ownership + невидимость + пуш вверх)
    -- Возвращает: ragd, partt (или nil, nil если не вышло)
    local function preparePallet()
        local myChar = LocalPlayer.Character
        local hrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not hrp then return nil, nil end

        local ragd = spawntoy("PalletLightBrown", hrp.CFrame * CFrame.new(5, 5, 20))
        if not ragd then return nil, nil end

        local partt = ragd:WaitForChild("SoundPart", 3)
        if not partt then
            pcall(function() destroyRE:FireServer(ragd) end)
            return nil, nil
        end
        ragd.Name = "ragdoll"

        -- Спам sno пока не появится PartOwner (до 8 сек)
        local t0 = tick()
        while not partt:FindFirstChild("PartOwner") and tick() - t0 < 8 do
            if not partt or not partt.Parent then break end
            if not keepGoing() then break end
            sno(partt)
            task.wait()
        end
        if not partt or not partt.Parent or not partt:FindFirstChild("PartOwner") then
            pcall(function() destroyRE:FireServer(ragd) end)
            return nil, nil
        end

        -- AssemblyLinearVelocity = (0, 100, 0) — стабильный пуш вверх
        -- (не 10000, чтобы не выкидывать цель в небо)
        partt.AssemblyLinearVelocity = Vector3.new(0, 100, 0)

        -- Невидимая (как в 9rr)
        task.spawn(function()
            for _, v2 in pairs(ragd:GetDescendants()) do
                if v2:IsA("Part") then
                    v2.Transparency = 1
                    v2.CanCollide   = false
                end
            end
        end)

        return ragd, partt
    end

    -- ============ ЗАПУСК ============
    cleanToys()
    task.wait(0.2)

    local ragd, partt = preparePallet()
    if not ragd or not partt then
        Library:Notify("PalletRagdoll: не удалось заспавнить/взять ownership", 3)
        return
    end

    Library:Notify("PalletRagdoll: молотим " .. targetName, 2)

    local lastAttackT = 0
    local lastTargetChar = nil

    -- Главный цикл
    while keepGoing() do
        -- Проверка живости палетки
        if not ragd or not ragd.Parent or not partt or not partt.Parent then
            task.wait(0.3)
            cleanToys()
            task.wait(0.2)
            ragd, partt = preparePallet()
            if not ragd or not partt then
                task.wait(0.5)
                continue
            end
        end

        -- Пере-клэйм если потеряли ownership
        if not partt:FindFirstChild("PartOwner") then
            sno(partt)
            task.wait(0.05)
            continue
        end

        local target = Players:FindFirstChild(targetName)
        if not target or not target.Character then
            task.wait(0.1)
            continue
        end

        local tChar = target.Character
        local tRoot = tChar:FindFirstChild("HumanoidRootPart")
        local tHum  = tChar:FindFirstChildOfClass("Humanoid")
        if not (tRoot and tHum) or tHum.Health <= 0 then
            task.wait(0.1)
            continue
        end

        -- Если цель сменила персонажа (респавн) — сбрасываем lastTargetChar
        if tChar ~= lastTargetChar then
            if lastTargetChar and lastTargetChar.Parent then
                local oldRoot = lastTargetChar:FindFirstChild("HumanoidRootPart")
                if oldRoot then
                    pcall(function() destroyGL:FireServer(oldRoot) end)
                end
            end
            lastTargetChar = tChar
            task.wait(0.3)
            continue
        end

        -- Если цель уже рагдоллнута — не атакуем, только держим ownership
        local ragdolledVal = tHum:FindFirstChild("Ragdolled")
        if ragdolledVal and ragdolledVal.Value then
            sno(partt)
            task.wait(0.2)
            continue
        end

        -- Пропуск если цель уже высоко в воздухе (чтобы не улетала)
        -- Считаем что цель "в воздухе" если её velocity Y > 50 или Y < -50
        local velY = tRoot.AssemblyLinearVelocity.Y
        if velY > 50 or velY < -50 then
            sno(partt)
            task.wait(0.1)
            continue
        end

        -- Антиспам: 0.1 сек между ударами
        if tick() - lastAttackT < 0.1 then
            RunService.Heartbeat:Wait()
            continue
        end
        lastAttackT = tick()

        -- Один удар: создаём GrabLine → позиционируем SoundPart → кидаем в небо → убиваем GrabLine
        -- Это короткий импульс, не постоянная привязка
        pcall(function()
            createGL:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
        end)
        sno(tRoot)

        task.spawn(function()
            if not partt or not partt.Parent then return end
            -- Импульс (0, 100, 0) — стабильный пуш
            partt.AssemblyLinearVelocity = Vector3.new(0, 100, 0)
            partt.CFrame = tRoot.CFrame
            task.wait(0.05)
            if partt and partt.Parent then
                -- Убираем SoundPart далеко, чтобы физический импульс не тащил цель
                partt.CFrame = CFrame.new(0, 1e9, 0)
                partt.AssemblyLinearVelocity = Vector3.zero
            end
            -- Сбрасываем GrabLine сразу после удара
            if tRoot and tRoot.Parent then
                pcall(function() destroyGL:FireServer(tRoot) end)
            end
        end)

        RunService.Heartbeat:Wait()
    end

    -- Очистка
    if lastTargetChar and lastTargetChar.Parent then
        local oldRoot = lastTargetChar:FindFirstChild("HumanoidRootPart")
        if oldRoot then
            pcall(function() destroyGL:FireServer(oldRoot) end)
        end
    end
    if ragd and ragd.Parent then
        pcall(function() destroyRE:FireServer(ragd) end)
        if ragd.Parent then ragd:Destroy() end
    end
    cleanToys()
end

-- ==============================================
-- REMOVE TARGET GUCCI (как action в списке Target)
-- Садится на Blobman цели (когда она его спавнит) и сразу встаёт → "украл" Gucci
-- Механика из unstable.txt 14129-14256
-- ==============================================
function tgtRemoveTargetGucciLoop(targetName, keepGoing)
    local myChar = LocalPlayer.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then
        Library:Notify("Remove Target Gucci: нет персонажа", 3)
        return
    end
    local SafeSpot = myRoot.CFrame
    Library:Notify("Remove Target Gucci: слежу за " .. targetName, 2)

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if not target or not target.Parent then
            task.wait(0.5)
            continue
        end

        local folderName = target.Name .. "SpawnedInToys"
        local toysFolder = workspace:FindFirstChild(folderName)
        if not toysFolder then
            task.wait(1)
            continue
        end

        -- Ищем CreatureBlobman у цели
        local foundBlobman = false
        for _, obj in ipairs(toysFolder:GetChildren()) do
            if not keepGoing() then break end
            if obj.Name == "CreatureBlobman" then
                foundBlobman = true
                local seat = obj:FindFirstChild("VehicleSeat") or obj:FindFirstChildWhichIsA("VehicleSeat", true)
                if seat then
                    local myChar2 = LocalPlayer.Character
                    local myRoot2 = myChar2 and myChar2:FindFirstChild("HumanoidRootPart")
                    local myHum2 = myChar2 and myChar2:FindFirstChildOfClass("Humanoid")
                    if myRoot2 and myHum2 then
                        if myHum2.SeatPart ~= seat then
                            Library:Notify("Remove Target Gucci: сажусь на блобман цели", 2)
                            local magnetConn = RunService.Stepped:Connect(function()
                                if myRoot2 and seat and seat.Parent then
                                    myRoot2.CFrame = seat.CFrame
                                    myRoot2.Velocity = Vector3.zero
                                    if obj.PrimaryPart then
                                        obj.PrimaryPart.Velocity = Vector3.zero
                                        obj.PrimaryPart.RotVelocity = Vector3.zero
                                    end
                                end
                            end)
                            local sitStart = tick()
                            while tick() - sitStart < 1 do
                                if not keepGoing() then break end
                                if myHum2.SeatPart == seat then break end
                                seat:Sit(myHum2)
                                task.wait()
                            end
                            if magnetConn then magnetConn:Disconnect() end
                            if myHum2.SeatPart == seat then
                                task.wait(0.3)
                                myHum2.Sit = false
                                myHum2.Jump = true
                                task.wait(0.05)
                                myRoot2.CFrame = SafeSpot
                                myRoot2.Velocity = Vector3.zero
                                Library:Notify("Remove Target Gucci: Gucci украден у " .. targetName, 2)
                                task.wait(0.5)
                            else
                                myRoot2.CFrame = SafeSpot
                            end
                        end
                    end
                end
            end
        end

        if not foundBlobman then
            -- Блобмана нет — ждём и проверяем снова
            task.wait(1)
        end
    end

    -- Очистка
    local finalChar = LocalPlayer.Character
    local finalRoot = finalChar and finalChar:FindFirstChild("HumanoidRootPart")
    if finalRoot and SafeSpot then
        finalRoot.CFrame = SafeSpot
        finalRoot.Velocity = Vector3.zero
    end
end


-- ==============================================
-- HEAVEN (из 9rr.txt performHeaven 4047-4065)
-- SetNetworkOwner → телепорт цели на (0, 200, 0) → BodyVelocity вверх 200
-- ==============================================
function tgtHeavenLoop(targetName, keepGoing)
    -- Сброс GrabLine на цели
    local target0 = Players:FindFirstChild(targetName)
    if target0 and target0.Character then
        local tRoot0 = target0.Character:FindFirstChild("HumanoidRootPart")
        local GE0 = getGrabEventsT()
        if GE0 and tRoot0 then
            local destroyGL0 = GE0:FindFirstChild("DestroyGrabLine")
            if destroyGL0 then pcall(function() destroyGL0:FireServer(tRoot0) end) end
            local sno0 = GE0:FindFirstChild("SetNetworkOwner")
            if sno0 then pcall(function() sno0:FireServer(tRoot0, tRoot0.CFrame) end) end
        end
    end
    task.wait(0.05)

    local GE = getGrabEventsT()
    if not GE then return end
    local SetNetworkOwner = GE:FindFirstChild("SetNetworkOwner")
    local DestroyGrabLine = GE:FindFirstChild("DestroyGrabLine")
    if not SetNetworkOwner then return end
    local Debris = game:GetService("Debris")

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if not target or not target.Character then task.wait(0.1) continue end
        local tRoot = target.Character:FindFirstChild("HumanoidRootPart")
        local tHum = target.Character:FindFirstChildOfClass("Humanoid")
        if not (tRoot and tHum) or tHum.Health <= 0 then task.wait(0.1) continue end

        pcall(function()
            SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
            if DestroyGrabLine then DestroyGrabLine:FireServer(tRoot) end
            tRoot.CFrame = CFrame.new(0, 200, 0)
            local bv = Instance.new("BodyVelocity")
            bv.Name = "OblivionHeaven"
            bv.MaxForce = Vector3.new(0, math.huge, 0)
            bv.Velocity = Vector3.new(0, 200, 0)
            bv.P = 12500
            bv.Parent = tRoot
            Debris:AddItem(bv, 0.01)
        end)
        RunService.Heartbeat:Wait()
    end
end

-- ==============================================
-- OWNERSHIP KICK — ФУЛЛ КОПИЯ из 9rr.txt 5540-5749 (БЕЗ ИЗМЕНЕНИЙ)
-- ==============================================
OwnershipKickEnabled = false
OwnershipKickTask = nil

function tgtOwnershipKickLoop(targetName, keepGoing)
    OwnershipKickEnabled = true

                local RS = game:GetService("ReplicatedStorage")
                local GE = RS:WaitForChild("GrabEvents", 10)
                local RunService = game:GetService("RunService")
                local Players = game:GetService("Players")
                local LocalPlayer = game.Players.LocalPlayer

                if not GE then
                    OwnershipKickEnabled = false
                    return
                end

                -- Прямые ссылки на remote events
                local SetNetworkOwner = GE:WaitForChild("SetNetworkOwner", 5)
                local DestroyGrabLine = GE:WaitForChild("DestroyGrabLine", 5)
                if not SetNetworkOwner or not DestroyGrabLine then
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

                while OwnershipKickEnabled and keepGoing() do
                    currentTarget = Players:FindFirstChild(targetName)
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
                                SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                                SetNetworkOwner:FireServer(tRoot, tRoot.CFrame)
                                DestroyGrabLine:FireServer(tRoot)
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
                                if currentFPS > 200 then
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    DestroyGrabLine:FireServer(tRoot)
                                elseif currentFPS >= 155 and currentFPS <= 200 then
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    DestroyGrabLine:FireServer(tRoot)
                                else 
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    SetNetworkOwner:FireServer(tRoot, lockPos)
                                    DestroyGrabLine:FireServer(tRoot)
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

end


-- ==============================================
-- DESTROY SERVER — из 9rr (10114-10181) + LineLag (10088-10112)
-- ==============================================
_tgtLineLagEnabled = false
_tgtLineLagThread = nil

function tgtStartLineLag()
    if _tgtLineLagEnabled then return end
    _tgtLineLagEnabled = true
    _tgtLineLagThread = coroutine.create(function()
        local GrabEvents = ReplicatedStorage:FindFirstChild("GrabEvents")
        if not GrabEvents then return end
        local createLine = GrabEvents:FindFirstChild("CreateGrabLine")
        if not createLine then return end
        while _tgtLineLagEnabled do
            local spawnLocation = workspace:FindFirstChild("SpawnLocation") or workspace:FindFirstChild("Spawn") or (LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart"))
            if spawnLocation then
                local randomX = math.random(-1e9, 1e9)
                local randomZ = math.random(-1e9, 1e9)
                local directions = {
                    CFrame.new(randomX, 0, randomZ),
                    CFrame.new(-randomX, 0, -randomZ),
                    CFrame.new(randomX, 0, -randomZ),
                    CFrame.new(-randomX, 0, randomZ)
                }
                for _, pos in pairs(directions) do
                    pcall(function() createLine:FireServer(spawnLocation, pos) end)
                end
            end
            task.wait()
        end
    end)
    coroutine.resume(_tgtLineLagThread)
end

function tgtStopLineLag()
    _tgtLineLagEnabled = false
    if _tgtLineLagThread then
        pcall(function() coroutine.close(_tgtLineLagThread) end)
        _tgtLineLagThread = nil
    end
end

function bmTestBombaMassKick(keepGoing)
    while true do
        if keepGoing and not keepGoing() then break end
        if not keepGoing and not bmLoopServer then break end

        local heightMode = (Options.DestroyHeightMode and Options.DestroyHeightMode.Value) or "Spawn"
        local height = (heightMode == "Heaven") and 1e9 or 35

        tgtStartLineLag()
        task.wait(1)

        local players = {}
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and not isFriendWhitelisted(plr) then
                table.insert(players, plr)
            end
        end
        if #players == 0 then
            tgtStopLineLag()
            task.wait(1)
            continue
        end

        local myChar = LocalPlayer.Character
        local myHrp = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myHrp then tgtStopLineLag() task.wait(1) continue end

        local savedPos = myHrp.CFrame

        local playerData = {}
        for _, plr in ipairs(players) do
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp then table.insert(playerData, {player = plr, hrp = hrp}) end
        end

        for _, data in ipairs(playerData) do
            pcall(function() myHrp.CFrame = data.hrp.CFrame * CFrame.new(0, 5, 5) end)
            task.wait(0.2)
            local GE = ReplicatedStorage:FindFirstChild("GrabEvents")
            local setOwner = GE and GE:FindFirstChild("SetNetworkOwner")
            if setOwner and data.hrp then
                pcall(function() setOwner:FireServer(data.hrp, data.hrp.CFrame) end)
            end
            task.wait()
        end

        pcall(function()
            myHrp.CFrame = savedPos
            myHrp.AssemblyLinearVelocity = Vector3.zero
        end)

        local radius = 40
        local angleStep = (math.pi * 2) / #playerData
        for idx, data in ipairs(playerData) do
            local angle = (idx - 1) * angleStep
            local x = math.cos(angle) * radius
            local z = math.sin(angle) * radius

            pcall(function()
                data.hrp.CFrame = CFrame.new(x, height, z)
                data.hrp.AssemblyLinearVelocity = Vector3.zero
            end)

            local bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e9, 1e9, 1e9)
            bp.P = 40000000
            bp.Position = Vector3.new(x, height, z)
            bp.Parent = data.hrp
            task.delay(2, function() pcall(function() bp:Destroy() end) end)
            task.wait()
        end

        local GE = ReplicatedStorage:FindFirstChild("GrabEvents")
        local destroyLine = GE and GE:FindFirstChild("DestroyGrabLine")
        if destroyLine then
            for i = 1, 8 do
                for _, data in ipairs(playerData) do
                    pcall(function() destroyLine:FireServer(data.hrp) end)
                end
                task.wait(0.3)
            end
        end

        tgtStopLineLag()

        pcall(function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                LocalPlayer.Character.HumanoidRootPart.CFrame = savedPos
                LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3.zero
            end
        end)

        task.wait(1)
    end
end

function bmKickTarget(targetName, keepGoing, reacquire, maxSeconds)
local fnStart = tick()
local launchHeight = 500
local customKickHeight = 25
local kickSavedPos = nil
local highPos = nil
local dragging = false
local grabStartTime = 0
local blobRoot = nil
local myHRP = nil

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then break end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
  RunService.Heartbeat:Wait()
  continue
 end

 if not kickSavedPos then
  kickSavedPos = myHRP.CFrame
  highPos = kickSavedPos * CFrame.new(0, launchHeight, 0)
  myHRP.CFrame = highPos
  myHRP.AssemblyLinearVelocity = Vector3.zero
  task.wait(0.1)
 end

 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
  if not bmKickingInProgress then
   if not sitOnBlobman() then
    RunService.Heartbeat:Wait()
    continue
   end
   task.wait(0.1)
   seat = myHum.SeatPart
   if seat and seat.Parent then
    blobRoot = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
    if blobRoot then
     blobRoot.CFrame = highPos
     blobRoot.AssemblyLinearVelocity = Vector3.zero
    end
   end
   myHRP.CFrame = highPos
   myHRP.AssemblyLinearVelocity = Vector3.zero
   RunService.Heartbeat:Wait()
   continue
  else
   RunService.Heartbeat:Wait()
   continue
  end
 end

 local blob = seat.Parent
 blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 myHRP.CFrame = highPos
 myHRP.AssemblyLinearVelocity = Vector3.zero
 if blobRoot then
  blobRoot.CFrame = highPos
  blobRoot.AssemblyLinearVelocity = Vector3.zero
 end

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHum = tChar and tChar:FindFirstChild("Humanoid")
 if not (tRoot and tHum) or tHum.Health <= 0 then
  dragging = false
  grabStartTime = 0
  RunService.Heartbeat:Wait()
  continue
 end

 bmKickingInProgress = true

 local remoteFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
 local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if grab and drop and L_Weld and R_Weld then
  pcall(function()
   grab:FireServer(L_Det, tRoot, L_Weld)
   grab:FireServer(R_Det, tRoot, R_Weld)
   drop:FireServer(L_Weld, tRoot)
   drop:FireServer(R_Weld, tRoot)
  end)
 end

 if not dragging then
  myHRP.CFrame = tRoot.CFrame
  if blobRoot then blobRoot.CFrame = tRoot.CFrame end
  pcall(function()
   tHum.PlatformStand = true
   tRoot.AssemblyLinearVelocity = Vector3.zero
   GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame)
   if createGrabLineEvent then
    createGrabLineEvent:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
   end
  end)

  if grabStartTime == 0 then grabStartTime = tick() end
  if tick() - grabStartTime > 0.5 then
   dragging = true
   grabStartTime = 0
  end
 else
  local lockPos = highPos * CFrame.new(0, customKickHeight, 0)
  pcall(function() tRoot.CFrame = lockPos end)
  pcall(function() tRoot.AssemblyLinearVelocity = Vector3.zero end)
  pcall(function()
   tHum.PlatformStand = true
   GE_Bloodman.SetNetworkOwner:FireServer(tRoot, lockPos)
   GE_Bloodman.DestroyGrabLine:FireServer(tRoot)
   if createGrabLineEvent then
    createGrabLineEvent:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
   end
  end)
 end

 if not reacquire then
  if dragging then
   for _ = 1, 5 do
    pcall(function() GE_Bloodman.DestroyGrabLine:FireServer(tRoot) end)
    task.wait(0.01)
   end
   bmKickingInProgress = false
   myHRP.CFrame = kickSavedPos or myHRP.CFrame
   myHRP.AssemblyLinearVelocity = Vector3.zero
   kickSavedPos = nil
   highPos = nil
   dragging = false
   break
  end
 end

 RunService.Heartbeat:Wait()
end

bmKickingInProgress = false
if myHRP and myHRP.Parent then
 myHRP.CFrame = kickSavedPos or myHRP.CFrame
 myHRP.AssemblyLinearVelocity = Vector3.zero
end
kickSavedPos = nil
highPos = nil
dragging = false
end

function bmKickServer(keepGoing)
 for _, plr in ipairs(Players:GetPlayers()) do
 if keepGoing and not keepGoing() then return end
 if plr ~= LocalPlayer and not isFriendWhitelisted(plr) then
 bmKickTarget(plr.Name, function()
 return (not keepGoing) or keepGoing()
 end, false, 12)
 end
 end
 end

-- ==============================================
-- TOY EXPLOSION (из 9rr 13360-13766)
-- ==============================================
_toyHitboxNames = {
    BombMissile = "PartHitDetector",
    BombDarkMatter = "PartHitDetector",
    FireworkMissile = "PartHitDetector",
    BombBalloon = "Balloon",
    PresentBig = "Box",
    PresentSmall = "Box",
}
_toySetupParts = {
    BombMissile = "Body",
    BombDarkMatter = "Pyramid",
    FireworkMissile = "Hitbox",
    BombBalloon = "Balloon",
    PresentBig = "Box",
    PresentSmall = "Box",
}

function tgtToyExplosionLoop(toyType, targetName, keepGoing)
    local spawnRF = getSpawnToyRF_T()
    local destroyRE = getDestroyToyRE_T()
    if not spawnRF then return end
    local GE = getGrabEventsT()
    local SetNetworkOwner = GE and GE:FindFirstChild("SetNetworkOwner")
    local BombEvents = ReplicatedStorage:FindFirstChild("BombEvents")
    local BuyToy = ReplicatedStorage:FindFirstChild("MenuToys") and ReplicatedStorage.MenuToys:FindFirstChild("BuyToyRemoteFunction")
    if not SetNetworkOwner then return end
    local toyFolderName = LocalPlayer.Name .. "SpawnedInToys"
    local hitboxName = _toyHitboxNames[toyType] or "PartHitDetector"
    local setupPartName = _toySetupParts[toyType] or "Body"
    local explosionAmount = (Options.ToyCount and Options.ToyCount.Value) or 3

    local function getAllToys()
        local toys = workspace:FindFirstChild(toyFolderName)
        if not toys then return {} end
        local result = {}
        for _, toy in pairs(toys:GetChildren()) do
            if toy.Name == toyType then table.insert(result, toy) end
        end
        return result
    end

    local function spawnOneToy()
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        pcall(function()
            spawnRF:InvokeServer(toyType, CFrame.new(hrp.Position + Vector3.new(0, 5, 0)), Vector3.zero)
            if BuyToy then BuyToy:InvokeServer(toyType) end
        end)
    end

    local function setupBomb(bomb)
        if not bomb or not bomb.PrimaryPart then return end
        local hitPart = bomb:FindFirstChild(setupPartName)
        if not hitPart then return end
        pcall(function() SetNetworkOwner:FireServer(hitPart, hitPart.CFrame) end)
        task.wait(0.05)
        pcall(function()
            for _, v in pairs(bomb.PrimaryPart:GetChildren()) do
                if v:IsA("BodyVelocity") or v.Name == "Stable" then v:Destroy() end
            end
            local bodyVel = Instance.new("BodyVelocity")
            bodyVel.Velocity = Vector3.zero
            bodyVel.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyVel.Name = "Stable"
            bodyVel.Parent = bomb.PrimaryPart
            bomb:PivotTo(CFrame.new(math.random(-500, 500), 10000, math.random(-500, 500)))
        end)
    end

    local function explodeBomb(bomb, targetHRP)
        if not bomb or not targetHRP then return end
        local hitbox = bomb:FindFirstChild(hitboxName)
        if not hitbox then return end
        local targetPos = targetHRP.Position
        if BombEvents and BombEvents:FindFirstChild("BombExplode") then
            pcall(function()
                BombEvents.BombExplode:FireServer({Hitbox = hitbox, PositionPart = targetHRP}, targetPos)
            end)
        end
    end

    local function deleteAllToys()
        for _, bomb in pairs(getAllToys()) do
            pcall(function() destroyRE:FireServer(bomb) end)
        end
    end

    while keepGoing() do
        local target = Players:FindFirstChild(targetName)
        if not target or not target.Character then task.wait(0.2) continue end
        local tHRP = target.Character:FindFirstChild("HumanoidRootPart")
        local tHum = target.Character:FindFirstChildOfClass("Humanoid")
        if not (tHRP and tHum) or tHum.Health <= 0 then task.wait(0.2) continue end
        deleteAllToys()
        task.wait(0.1)
        while #getAllToys() < explosionAmount and keepGoing() do
            task.spawn(spawnOneToy)
            task.wait(0.02)
        end
        task.wait(0.15)
        for _, bomb in pairs(getAllToys()) do
            if not keepGoing() then break end
            task.spawn(function() setupBomb(bomb) end)
            task.wait(0.02)
        end
        task.wait(0.2)
        tHRP = target.Character and target.Character:FindFirstChild("HumanoidRootPart")
        if tHRP and tHum and tHum.Health > 0 then
            for _, bomb in pairs(getAllToys()) do
                if not keepGoing() then break end
                task.spawn(function() explodeBomb(bomb, tHRP) end)
                task.wait(0.01)
            end
        end
        task.wait(0.15)
        deleteAllToys()
        task.wait(0.5)
    end
    deleteAllToys()
end

-- ==============================================
-- Универсальный диспетчер: вызывает нужную функцию по action
-- ==============================================
function tgtRunAction(action, targetName, keepGoing)
    if action == "Kick" then
        tgtOwnershipKickLoop(targetName, keepGoing)
    elseif action == "Kill" then
        tgtV3Action("kill", targetName, keepGoing)
    elseif action == "Fling" then
        tgtV3Action("fling", targetName, keepGoing)
    elseif action == "Void" then
        tgtV3Action("void", targetName, keepGoing)
    elseif action == "Lock" then
        tgtLockLoop(targetName, keepGoing)
    elseif action == "BlackHole" then
        tgtBlackHoleLoop(targetName, keepGoing)
    elseif action == "BombMissile" then
        tgtBombMissileLoop(targetName, keepGoing)
    elseif action == "Banana" then
        tgtBananaLoop(targetName, keepGoing)
    elseif action == "Snowball" then
        tgtSnowballLoop(targetName, keepGoing)
    elseif action == "AntiAntiKickWD" then
        tgtAntiAntiKickWDLoop(targetName, keepGoing)
    elseif action == "AntiAntiKickBlackHole" then
        tgtAntiAntiKickBlackHoleLoop(targetName, keepGoing)
    elseif action == "PalletRagdoll" then
        tgtPalletRagdollLoop(targetName, keepGoing)
    elseif action == "DontGiveChance" then
        tgtDontGiveChanceLoop(targetName, keepGoing)
    elseif action == "RemoveTargetGucci" then
        tgtRemoveTargetGucciLoop(targetName, keepGoing)
    elseif action == "FireworkMissile" then
        tgtToyExplosionLoop("FireworkMissile", targetName, keepGoing)
    elseif action == "BombBalloon" then
        tgtToyExplosionLoop("BombBalloon", targetName, keepGoing)
    elseif action == "PresentBig" then
        tgtToyExplosionLoop("PresentBig", targetName, keepGoing)
    elseif action == "PresentSmall" then
        tgtToyExplosionLoop("PresentSmall", targetName, keepGoing)
    end
end

end  -- закрывает do-блок V3 функций

 -- ===== UI: создание groupboxes =====
 BloodmanTargets = Tabs.Bloodman:AddLeftGroupbox("Игроки на сервере")
 BloodmanAction = Tabs.Bloodman:AddRightGroupbox("Действие")

 GE_Bloodman = ReplicatedStorage:FindFirstChild("GrabEvents")
 or ReplicatedStorage:WaitForChild("GrabEvents", 10)

 bmLoopPlayer = false
 bmLoopServer = false

 function bmPlayerList()
 local list = {}
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer then
 table.insert(list, plr.DisplayName .. " (@" .. plr.Name .. ")")
 end
 end
 return list
 end

 function bmGetPlayerByName(value)
 if not value or value == "" then return nil end
 local username = value:match("@(%S-)%)")
 if username then
 return Players:FindFirstChild(username)
 end
 return Players:FindFirstChild(value)
 end

 -- === АВАТАР + СТАТИСТИКА ===
 BloodmanAvatarImage = BloodmanTargets:AddImage("BloodmanAvatarDisplay", {
  Image = "rbxassetid://0",
  Size = Vector2.new(150, 150),
 })

 BloodmanAvatarLabel = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Цель не выбрана</font></b>")

 BloodmanStatName        = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Name:</font></b> <font color='#888888'>—</font>")
 BloodmanStatUsername    = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Username:</font></b> <font color='#888888'>—</font>")
 BloodmanStatHealth      = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Health:</font></b> <font color='#888888'>—</font>")
 BloodmanStatStuds        = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#888888'>—</font>")
 BloodmanStatCord        = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#888888'>—</font>")
 BloodmanStatInPlot      = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>In plot:</font></b> <font color='#888888'>—</font>")
 BloodmanStatRagdoll     = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='#888888'>—</font>")
 BloodmanStatKick        = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Kick:</font></b> <font color='#888888'>—</font>")
 BloodmanStatLeft        = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Left:</font></b> <font color='#888888'>—</font>")
 BloodmanStatRejoin      = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#888888'>—</font>")
 BloodmanStatLastGrab    = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='#888888'>None</font>")
 BloodmanStatPCLDBreak   = BloodmanTargets:AddLabel("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='#888888'>—</font>")

 _bloodmanAvatarCurrentUid = nil

 function bmUpdateStats()
  if not Options.BloodmanTarget then return end
  local value = Options.BloodmanTarget.Value
  if not value or value == "" then return end
  local plr = bmGetPlayerByName(value)
  if not plr or not plr.Character then return end

  local char = plr.Character
  local hrp = char:FindFirstChild("HumanoidRootPart")
  local hum = char:FindFirstChildOfClass("Humanoid")
  local lpHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")

  pcall(function() BloodmanStatName:SetText("<b><font color='#aaaaaa'>Name:</font></b> <font color='#ffffff'>" .. tostring(plr.DisplayName) .. "</font>") end)
  pcall(function() BloodmanStatUsername:SetText("<b><font color='#aaaaaa'>Username:</font></b> <font color='#ffffff'>" .. tostring(plr.Name) .. "</font>") end)

  if hum then
   local hp = math.floor(hum.Health)
   local hpMax = math.floor(hum.MaxHealth)
   local pct = hpMax > 0 and math.floor((hp / hpMax) * 100) or 0
   local color = "#55ff55"
   if pct <= 0 then color = "#ff5555"
   elseif pct <= 50 then color = "#ffff55" end
   pcall(function() BloodmanStatHealth:SetText("<b><font color='#aaaaaa'>Health:</font></b> <font color='" .. color .. "'>" .. pct .. "%</font>") end)
  end

  if hrp and lpHrp then
   local studs = math.floor((lpHrp.Position - hrp.Position).Magnitude)
   pcall(function() BloodmanStatStuds:SetText("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#ffffff'>" .. studs .. "</font>") end)
  end

  if hrp then
   local pos = hrp.Position
   pcall(function() BloodmanStatCord:SetText("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#ffffff'>" .. math.floor(pos.X) .. ", " .. math.floor(pos.Y) .. ", " .. math.floor(pos.Z) .. "</font>") end)
  end

  local inPlot = false
  pcall(function() inPlot = plr:FindFirstChild("InPlot") and plr.InPlot.Value or false end)
  local plotColor = inPlot and "#55ff55" or "#ff5555"
  pcall(function() BloodmanStatInPlot:SetText("<b><font color='#aaaaaa'>In plot:</font></b> <font color='" .. plotColor .. "'>" .. (inPlot and "Yes" or "No") .. "</font>") end)

  local isRagdolled = false
  if hum then
   local ragVal = hum:FindFirstChild("Ragdolled")
   isRagdolled = ragVal and ragVal.Value or false
  end
  local ragColor = isRagdolled and "#ff5555" or "#55ff55"
  pcall(function() BloodmanStatRagdoll:SetText("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='" .. ragColor .. "'>" .. (isRagdolled and "Yes" or "No") .. "</font>") end)

  local kickText = "—"
  local kickColor = "#888888"
  pcall(function()
   local kickAttr = char:GetAttribute("KickedBy")
   if kickAttr and kickAttr ~= "" then
    kickText = tostring(kickAttr)
    kickColor = "#ff5555"
   end
  end)
  pcall(function() BloodmanStatKick:SetText("<b><font color='#aaaaaa'>Kick:</font></b> <font color='" .. kickColor .. "'>" .. kickText .. "</font>") end)

  local leftText = "—"
  pcall(function()
   local leaveTime = plr:GetAttribute("TimeUntilKick")
   if leaveTime and leaveTime > 0 then
    local h = math.floor(leaveTime / 3600)
    local m = math.floor((leaveTime % 3600) / 60)
    local s = math.floor(leaveTime % 60)
    leftText = string.format("%d (%02d:%02d:%02d)", leaveTime, h, m, s)
   end
  end)
  pcall(function() BloodmanStatLeft:SetText("<b><font color='#aaaaaa'>Left:</font></b> <font color='#ffffff'>" .. leftText .. "</font>") end)

  local rejoinText = "—"
  pcall(function()
   local rejoinTime = plr:GetAttribute("TimeUntilRejoin")
   if rejoinTime and rejoinTime > 0 then
    local h = math.floor(rejoinTime / 3600)
    local m = math.floor((rejoinTime % 3600) / 60)
    local s = math.floor(rejoinTime % 60)
    rejoinText = string.format("%d (%02d:%02d:%02d)", rejoinTime, h, m, s)
   end
  end)
  pcall(function() BloodmanStatRejoin:SetText("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#ffffff'>" .. rejoinText .. "</font>") end)

  local lastGrabText = "None"
  local lastGrabColor = "#ff9955"
  pcall(function()
   local grabber = char:GetAttribute("LastGrabber")
   if grabber and grabber ~= "" then
    lastGrabText = tostring(grabber)
    lastGrabColor = "#ffaa55"
   end
  end)
  pcall(function() BloodmanStatLastGrab:SetText("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='" .. lastGrabColor .. "'>" .. lastGrabText .. "</font>") end)

  local pcldBreak = false
  pcall(function() pcldBreak = char:GetAttribute("PCLDBreak") == true end)
  local pcldColor = pcldBreak and "#ff5555" or "#55ff55"
  pcall(function() BloodmanStatPCLDBreak:SetText("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='" .. pcldColor .. "'>" .. (pcldBreak and "Yes" or "No") .. "</font>") end)
 end

 task.spawn(function()
  while true do
   if Options.BloodmanTarget and Options.BloodmanTarget.Value and Options.BloodmanTarget.Value ~= "" then
    pcall(bmUpdateStats)
   end
   task.wait(0.5)
  end
 end)

 function bmUpdateAvatarDisplay()
  if not Options.BloodmanTarget then return end
  local value = Options.BloodmanTarget.Value
  if not value or value == "" then
   _bloodmanAvatarCurrentUid = nil
   pcall(function() BloodmanAvatarImage:SetImage("rbxassetid://0") end)
   pcall(function() BloodmanAvatarLabel:SetText("<b><font color='#aaaaaa'>Цель не выбрана</font></b>") end)
   pcall(function() BloodmanStatName:SetText("<b><font color='#aaaaaa'>Name:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatUsername:SetText("<b><font color='#aaaaaa'>Username:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatHealth:SetText("<b><font color='#aaaaaa'>Health:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatStuds:SetText("<b><font color='#aaaaaa'>Studs:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatCord:SetText("<b><font color='#aaaaaa'>Cord:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatInPlot:SetText("<b><font color='#aaaaaa'>In plot:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatRagdoll:SetText("<b><font color='#aaaaaa'>Ragdoll:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatKick:SetText("<b><font color='#aaaaaa'>Kick:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatLeft:SetText("<b><font color='#aaaaaa'>Left:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatRejoin:SetText("<b><font color='#aaaaaa'>Rejoin:</font></b> <font color='#888888'>—</font>") end)
   pcall(function() BloodmanStatLastGrab:SetText("<b><font color='#aaaaaa'>Last grab:</font></b> <font color='#888888'>None</font>") end)
   pcall(function() BloodmanStatPCLDBreak:SetText("<b><font color='#aaaaaa'>PCLD Break:</font></b> <font color='#888888'>—</font>") end)
   return
  end
  local plr = bmGetPlayerByName(value)
  if not plr then
   _bloodmanAvatarCurrentUid = nil
   pcall(function() BloodmanAvatarImage:SetImage("rbxassetid://0") end)
   pcall(function() BloodmanAvatarLabel:SetText("<b><font color='#ff5555'>Игрок не найден</font></b>") end)
   return
  end
  local uid = plr.UserId
  if uid == _bloodmanAvatarCurrentUid then return end
  _bloodmanAvatarCurrentUid = uid
  pcall(function()
   BloodmanAvatarLabel:SetText("<b><font color='#ffffff'>" .. plr.DisplayName .. "</font></b> <font color='#888888'>(@" .. plr.Name .. ")</font>")
  end)
  task.spawn(function()
   local ok, content = pcall(Players.GetUserThumbnailAsync, Players, uid, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
   if ok and content and content ~= "" and plr.Parent then
    if _bloodmanAvatarCurrentUid == uid then
     pcall(function() BloodmanAvatarImage:SetImage(content) end)
    end
   end
  end)
 end

 -- ===== UI: список игроков =====
 BloodmanTargets:AddDropdown("BloodmanTarget", {
 Text = "Цель",
 Values = bmPlayerList(),
 Default = (bmPlayerList())[1] or nil,
 Tooltip = "Список игроков обновляется сам",
 Callback = function() bmUpdateAvatarDisplay() end,
 })

 function bmRefresh()
 if not Options.BloodmanTarget then return end
 local list = bmPlayerList()
 pcall(function() Options.BloodmanTarget:SetValues(list) end)
 local cur = Options.BloodmanTarget.Value
 local stillHere = false
 for _, n in ipairs(list) do if n == cur then stillHere = true break end end
 if (not stillHere) and #list > 0 then
 pcall(function() Options.BloodmanTarget:SetValue(list[1]) end)
 end
 bmUpdateAvatarDisplay()
 end

 Players.PlayerAdded:Connect(function() task.wait(0.3) bmRefresh() end)
 Players.PlayerRemoving:Connect(function() task.defer(bmRefresh) end)

 -- Стартовое обновление аватара
 task.spawn(function() task.wait(0.1) bmUpdateAvatarDisplay() end)

 BloodmanTargets:AddButton({
 Text = "Обновить список",
 Func = function() bmRefresh() end,
 })

 -- ===== UI: лупы (тумблеры) =====
 BloodmanTargets:AddToggle("BloodmanLoopPlayer", {
 Text = "Loop Player",
 Default = false,
 Tooltip = "Держит и кикает выбранную цель, пока она не выйдет (даже после ресета)",
 })

 BloodmanTargets:AddToggle("BloodmanLoopServer", {
 Text = "Loop Server",
 Default = false,
 Tooltip = "Кикает весь сервер по кругу, пока включено",
 })

 BloodmanTargets:AddToggle("BloodmanSkipFriends", {
 Text = "Не трогать друзей",
 Default = false,
 Tooltip = "Исключает друзей из кика по всему серверу",
 })

 -- ===== UI: кнопки (один раз) =====
 BloodmanTargets:AddButton({
 Text = "Player (1 раз)",
 Tooltip = "Кик/Бринг выбранного игрока. Если он ресетнится — функция остановится",
 Func = function()
 if not bmCheckMethod() then return end
 local rawValue = Options.BloodmanTarget and Options.BloodmanTarget.Value or ""
 local targetPlayer = bmGetPlayerByName(rawValue)
 local targetName = targetPlayer and targetPlayer.Name or ""
 if not targetName or targetName == "" then
 Library:Notify("Bloodman: выбери игрока", 3)
 return
 end
 task.spawn(function()
 local m = (Options.BloodmanMethod and Options.BloodmanMethod.Value) or "Кик"
                                Library:Notify("Bloodman: " .. (m == "Бринг" and "бринг" or m == "Кик 2" and "кик2" or "кикаю") .. " " .. targetName, 2)
                                if m == "Бринг" then
                                        bmBringTarget(targetName, function() return true end, false, 10)
                                elseif m == "Кик 2" then
                                        bmKick2Target(targetName, function() return true end, false, 5)
                                elseif m == "Килл" then
                                        bmKillTarget(targetName, function() return true end, false, 15)
                                elseif m == "Войд" then
                                        bmVoidTarget(targetName, function() return true end, false, 15)
                                elseif m == "Спин Кик" then
                                        bmSpinKickTarget(targetName, function() return true end, false, 15)
                                else
                                        bmKickTarget(targetName, function() return true end, false, 30)
                                end
 end)
 end,
 })

 BloodmanTargets:AddButton({
 Text = "Server (все 1 раз)",
 Tooltip = "Один проход кика/бринга по всем игрокам",
 Func = function()
 if not bmCheckMethod() then return end
 task.spawn(function()
 local m = (Options.BloodmanMethod and Options.BloodmanMethod.Value) or "Кик"
                        Library:Notify("Bloodman: " .. (m == "Бринг" and "бринг" or m == "Кик 2" and "кик2" or "кикаю") .. " весь сервер", 2)
                        if m == "Бринг" then
                                bmBringServer(nil)
                        elseif m == "Кик 2" then
                                bmKick2Server(nil)
                        elseif m == "Килл" then
                                bmKillServer(nil)
                        elseif m == "Войд" then
                                bmVoidServer(nil)
                        elseif m == "Спин Кик" then
                                bmSpinKickServer(nil)
                        elseif m == "Кик" then
                                bmTestBombaMassKick(nil)
                        end
 end)
 end,
 })

 -- ===== UI: выбор действия =====
 BloodmanAction:AddDropdown("BloodmanMethod", {
 Text = "Что делаем",
 Values = { "Кик", "Кик 2", "Бринг", "Войд", "Килл", "Спин Кик" },
 Default = "Кик",
 Tooltip = "Работают: Кик, Кик 2, Бринг, Килл, Войд, Спин Кик",
 Callback = function() end,
 })

 -- === SPIN KICK sliders (из 9rr.txt 6532-6552) ===
 BloodmanAction:AddSlider("SpinKickRadius", {
  Text = "Spin Radius",
  Default = 25,
  Min = 5,
  Max = 50,
  Rounding = 0,
  Compact = false,
  Tooltip = "Радиус вращения вокруг цели",
  Callback = function(v)
   bmSpinRadius = v
  end,
 })

 BloodmanAction:AddSlider("SpinKickSpeed", {
  Text = "Spin Speed",
  Default = 0.25,
  Min = 0.05,
  Max = 1,
  Rounding = 2,
  Compact = false,
  Tooltip = "Скорость вращения (больше = быстрее)",
  Callback = function(v)
   bmSpinSpeed = v
  end,
 })

 BloodmanAction:AddSlider("SpinKickHeight", {
  Text = "Spin Height",
  Default = 20,
  Min = 5,
  Max = 100,
  Rounding = 0,
  Compact = false,
  Tooltip = "Высота на которой цель вращается над тобой",
  Callback = function(v)
   bmSpinKickHeight = v
  end,
 })

 -- Проверка выбранного метода
 function bmCheckMethod()
                local m = (Options.BloodmanMethod and Options.BloodmanMethod.Value) or "Кик"
                if m == "Кик" or m == "Кик 2" or m == "Бринг" or m == "Килл" or m == "Войд" or m == "Спин Кик" then
                        return true
                end
                Library:Notify("Bloodman: неизвестный метод " .. m, 4)
                return false
        end

 -- ===== Логика тумблеров =====
 Toggles.BloodmanLoopPlayer:OnChanged(function()
 if Toggles.BloodmanLoopPlayer.Value then
 if not bmCheckMethod() then
 Toggles.BloodmanLoopPlayer:SetValue(false)
 return
 end
 local rawValue = Options.BloodmanTarget and Options.BloodmanTarget.Value or ""
 local targetPlayer = bmGetPlayerByName(rawValue)
 local targetName = targetPlayer and targetPlayer.Name or ""
 if not targetName or targetName == "" then
 Library:Notify("Bloodman: выбери игрока", 3)
 Toggles.BloodmanLoopPlayer:SetValue(false)
 return
 end
 bmLoopPlayer = true
 task.spawn(function()
 local m = (Options.BloodmanMethod and Options.BloodmanMethod.Value) or "Кик"
                                Library:Notify("Bloodman: луп-" .. (m == "Бринг" and "бринг" or m == "Кик 2" and "кик2" or "кик") .. " " .. targetName, 2)
                                if m == "Бринг" then
                                        bmBringTarget(targetName, function() return bmLoopPlayer end, true, nil)
                                elseif m == "Кик 2" then
                                        bmKick2Target(targetName, function() return bmLoopPlayer end, true, nil)
                                elseif m == "Килл" then
                                        bmKillTarget(targetName, function() return bmLoopPlayer end, true, nil)
                                elseif m == "Войд" then
                                        bmVoidTarget(targetName, function() return bmLoopPlayer end, true, nil)
                                elseif m == "Спин Кик" then
                                        bmSpinKickTarget(targetName, function() return bmLoopPlayer end, true, nil)
                                else
                                        bmKickTarget(targetName, function() return bmLoopPlayer end, true, nil)
                                end
 bmLoopPlayer = false
 if Toggles.BloodmanLoopPlayer.Value then
 Toggles.BloodmanLoopPlayer:SetValue(false)
 end
 end)
 else
 bmLoopPlayer = false
 end
 end)

 Toggles.BloodmanLoopServer:OnChanged(function()
 if Toggles.BloodmanLoopServer.Value then
 if not bmCheckMethod() then
 Toggles.BloodmanLoopServer:SetValue(false)
 return
 end
 bmLoopServer = true
 task.spawn(function()
 local m = (Options.BloodmanMethod and Options.BloodmanMethod.Value) or "Кик"
                        Library:Notify("Bloodman: луп-" .. (m == "Бринг" and "бринг" or m == "Кик 2" and "кик2" or "кик") .. " сервера", 2)
                        while bmLoopServer do
                                if m == "Бринг" then
                                        bmBringServer(function() return bmLoopServer end)
                                elseif m == "Кик 2" then
                                        bmKick2Server(function() return bmLoopServer end)
                                elseif m == "Килл" then
                                        bmKillServer(function() return bmLoopServer end)
                                elseif m == "Войд" then
                                        bmVoidServer(function() return bmLoopServer end)
                                elseif m == "Спин Кик" then
                                        bmSpinKickServer(function() return bmLoopServer end)
                                elseif m == "Кик" then
                                        bmTestBombaMassKick(function() return bmLoopServer end)
                                end
                                task.wait(0.2)
 end
 end)
 else
 bmLoopServer = false
 end
 end)

 -- Skip friends handler (Bloodman)
 Toggles.BloodmanSkipFriends:OnChanged(function()
  _friendWhitelistEnabled = Toggles.BloodmanSkipFriends.Value
  if _friendWhitelistEnabled then
   task.spawn(function()
    LoadFriendsIntoWhitelist()
   end)
  else
   _friendWhitelist = {}
  end
 end)

end

-- BRING через блобмана (как кик, но тащит цель к тебе)
function bmBringTarget(targetName, keepGoing, reacquire, maxSeconds)
fnStart = tick()
bringSavedPos = nil
grabbed = false
myHRP = nil

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then
  if not reacquire then break end
  grabbed = false
  RunService.Heartbeat:Wait()
  continue
 end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
  RunService.Heartbeat:Wait()
  continue
 end

 if not bringSavedPos then
  bringSavedPos = myHRP.CFrame
 end

 -- Sit on blobman
 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
  if not bmKickingInProgress then
   if not sitOnBlobman() then
    RunService.Heartbeat:Wait()
    continue
   end
   task.wait(0.1)
   seat = myHum.SeatPart
   if seat and seat.Parent then
    local br = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
    if br then
     br.CFrame = bringSavedPos
     br.AssemblyLinearVelocity = Vector3.zero
    end
   end
   myHRP.CFrame = bringSavedPos
   myHRP.AssemblyLinearVelocity = Vector3.zero
   RunService.Heartbeat:Wait()
   continue
  else
   RunService.Heartbeat:Wait()
   continue
  end
 end

 local blob = seat.Parent
 local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
 if not (tRoot and tHum and tHum.Health > 0) then
  grabbed = false
  if not reacquire then break end
  RunService.Heartbeat:Wait()
  continue
 end

 bmKickingInProgress = true

 -- Find grab remotes
 local remoteFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if not grabbed then
  -- Phase 1: Move near target and grab
  myHRP.CFrame = tRoot.CFrame
  if blobRoot then blobRoot.CFrame = tRoot.CFrame end
  if grab and L_Weld and R_Weld then
   pcall(function()
    grab:FireServer(L_Det, tRoot, L_Weld)
    grab:FireServer(R_Det, tRoot, R_Weld)
   end)
  end
  task.wait(0.5)
  grabbed = true

  -- Phase 2: Bring target back to saved position
  myHRP.CFrame = bringSavedPos
  myHRP.AssemblyLinearVelocity = Vector3.zero
  if blobRoot then
   blobRoot.CFrame = bringSavedPos
   blobRoot.AssemblyLinearVelocity = Vector3.zero
  end
  -- Take ownership and teleport target to us
  pcall(function()
   GE_Bloodman.SetNetworkOwner:FireServer(tRoot, bringSavedPos * CFrame.new(0, 0, 3))
   tRoot.CFrame = bringSavedPos * CFrame.new(0, 0, 3)
   tRoot.AssemblyLinearVelocity = Vector3.zero
  end)
  -- Fire grab again to hold at new position
  if grab and L_Weld and R_Weld then
   pcall(function()
    grab:FireServer(L_Det, tRoot, L_Weld)
    grab:FireServer(R_Det, tRoot, R_Weld)
   end)
  end

  -- For button (not reacquire): STOP after bringing target back
  if not reacquire then
   task.wait(0.3)
   if grab and L_Weld and R_Weld then
    pcall(function()
     grab:FireServer(L_Det, tRoot, L_Weld)
     grab:FireServer(R_Det, tRoot, R_Weld)
    end)
   end
   break
  end
 end

 -- For loop (reacquire): keep holding target at saved position
 if grabbed and reacquire then
  myHRP.CFrame = bringSavedPos
  myHRP.AssemblyLinearVelocity = Vector3.zero
  if blobRoot then
   blobRoot.CFrame = bringSavedPos
   blobRoot.AssemblyLinearVelocity = Vector3.zero
  end
  pcall(function()
   if (tRoot.Position - myHRP.Position).Magnitude > 15 then
    GE_Bloodman.SetNetworkOwner:FireServer(tRoot, bringSavedPos * CFrame.new(0, 0, 3))
    tRoot.CFrame = bringSavedPos * CFrame.new(0, 0, 3)
    tRoot.AssemblyLinearVelocity = Vector3.zero
   end
  end)
  if grab and L_Weld and R_Weld then
   pcall(function()
    grab:FireServer(L_Det, tRoot, L_Weld)
    grab:FireServer(R_Det, tRoot, R_Weld)
   end)
  end
 end

 RunService.Heartbeat:Wait()
end

-- Cleanup (DON'T destroy blobman!)
bmKickingInProgress = false
if myHRP and myHRP.Parent then
 myHRP.CFrame = bringSavedPos or myHRP.CFrame
 myHRP.AssemblyLinearVelocity = Vector3.zero
end
bringSavedPos = nil
end

function bmBringServer(keepGoing)
 for _, plr in ipairs(Players:GetPlayers()) do
 if keepGoing and not keepGoing() then return end
 if plr ~= LocalPlayer then
 bmBringTarget(plr.Name, function()
 return (not keepGoing) or keepGoing()
 end, false, 12)
 end
 end
end

-- Граб через блобмана (хватает и держит цель рядом)
function bmGrabTarget(targetName, keepGoing, reacquire, maxSeconds)
fnStart = tick()
grabSavedPos = nil
dragging = false
grabStartTime = 0
blobRoot = nil

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then break end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 local myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
 RunService.Heartbeat:Wait()
 continue
 end

 if not grabSavedPos then
 grabSavedPos = myHRP.CFrame
 end

 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
 if not bmKickingInProgress then
 if not sitOnBlobman() then
 RunService.Heartbeat:Wait()
 continue
 end
 task.wait(0.1)
 seat = myHum.SeatPart
 if seat and seat.Parent then
 blobRoot = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
 if blobRoot then
 blobRoot.CFrame = grabSavedPos
 blobRoot.AssemblyLinearVelocity = Vector3.zero
 end
 end
 myHRP.CFrame = grabSavedPos
 myHRP.AssemblyLinearVelocity = Vector3.zero
 RunService.Heartbeat:Wait()
 continue
 else
 RunService.Heartbeat:Wait()
 continue
 end
 end

 local blob = seat.Parent
 blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 myHRP.CFrame = grabSavedPos
 myHRP.AssemblyLinearVelocity = Vector3.zero
 if blobRoot then
 blobRoot.CFrame = grabSavedPos
 blobRoot.AssemblyLinearVelocity = Vector3.zero
 end

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHum = tChar and tChar:FindFirstChild("Humanoid")
 if not (tRoot and tHum and tHum.Health > 0) then
 dragging = false
 grabStartTime = 0
 RunService.Heartbeat:Wait()
 continue
 end

 bmKickingInProgress = true

 local remoteFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
 local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if grab and drop and L_Weld and R_Weld then
 pcall(function()
 grab:FireServer(L_Det, tRoot, L_Weld)
 grab:FireServer(R_Det, tRoot, R_Weld)
 drop:FireServer(L_Weld, tRoot)
 drop:FireServer(R_Weld, tRoot)
 end)
 end

 if not dragging then
 myHRP.CFrame = tRoot.CFrame
 if blobRoot then blobRoot.CFrame = tRoot.CFrame end
 pcall(function()
 tHum.PlatformStand = true
 tRoot.AssemblyLinearVelocity = Vector3.zero
 GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame)
 if createGrabLineEvent then
 createGrabLineEvent:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
 end
 end)

 if grabStartTime == 0 then grabStartTime = tick() end
 if tick() - grabStartTime > 0.5 then
 dragging = true
 grabStartTime = 0
 end
 else
 local holdPos = grabSavedPos * CFrame.new(0, 0, 3)
 myHRP.CFrame = grabSavedPos
 myHRP.AssemblyLinearVelocity = Vector3.zero
 if blobRoot then
 blobRoot.CFrame = grabSavedPos
 blobRoot.AssemblyLinearVelocity = Vector3.zero
 end

 pcall(function()
 tRoot.CFrame = holdPos
 tRoot.AssemblyLinearVelocity = Vector3.zero
 tHum.PlatformStand = true
 GE_Bloodman.SetNetworkOwner:FireServer(tRoot, holdPos)
 GE_Bloodman.DestroyGrabLine:FireServer(tRoot)
 if createGrabLineEvent then
 createGrabLineEvent:FireServer(tRoot, Vector3.zero, tRoot.Position, false)
 end
 end)
 end

 RunService.Heartbeat:Wait()
end

bmKickingInProgress = false
if myHRP and myHRP.Parent then
 myHRP.CFrame = grabSavedPos or myHRP.CFrame
 myHRP.AssemblyLinearVelocity = Vector3.zero
end
grabSavedPos = nil
dragging = false

folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
blobToDestroy = folder and folder:FindFirstChild("CreatureBlobman")
if blobToDestroy then
 local destroyRem = ReplicatedStorage:FindFirstChild("MenuToys")
 destroyRem = destroyRem and destroyRem:FindFirstChild("DestroyToy")
 if destroyRem then destroyRem:FireServer(blobToDestroy) end
end
end

function bmGrabServer(keepGoing)
 for _, plr in ipairs(Players:GetPlayers()) do
 if keepGoing and not keepGoing() then return end
 if plr ~= LocalPlayer then
 bmGrabTarget(plr.Name, function()
 return (not keepGoing) or keepGoing()
 end, false, 12)
 end
 end
end

function bmKick2Target(targetName, keepGoing, reacquire, maxSeconds)
fnStart = tick()
grabSavedPos = nil
grabbed = false

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then
  if not reacquire then break end
  grabbed = false
  RunService.Heartbeat:Wait()
  continue
 end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 local myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
  RunService.Heartbeat:Wait()
  continue
 end

 if not grabSavedPos then
  grabSavedPos = myHRP.CFrame
 end

 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
  if not bmKickingInProgress then
   if not sitOnBlobman() then
    RunService.Heartbeat:Wait()
    continue
   end
   task.wait(0.1)
   seat = myHum.SeatPart
   if seat and seat.Parent then
    local br = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
    if br then
     br.CFrame = grabSavedPos
     br.AssemblyLinearVelocity = Vector3.zero
    end
   end
   myHRP.CFrame = grabSavedPos
   myHRP.AssemblyLinearVelocity = Vector3.zero
   RunService.Heartbeat:Wait()
   continue
  else
   RunService.Heartbeat:Wait()
   continue
  end
 end

 local blob = seat.Parent
 local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHum = tChar and tChar:FindFirstChild("Humanoid")
 if not (tRoot and tHum and tHum.Health > 0) then
  grabbed = false
  if not reacquire then break end
  RunService.Heartbeat:Wait()
  continue
 end

 bmKickingInProgress = true

 local remoteFolder = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if not grabbed then
  myHRP.CFrame = tRoot.CFrame
  if blobRoot then blobRoot.CFrame = tRoot.CFrame end
  task.wait(0.3)
  grabbed = true
 end

 if grab and L_Weld and R_Weld then
  pcall(function()
   grab:FireServer(L_Det, tRoot, L_Weld)
   grab:FireServer(R_Det, tRoot, R_Weld)
  end)
 end

 RunService.Heartbeat:Wait()
end

bmKickingInProgress = false
folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
blobToDestroy = folder and folder:FindFirstChild("CreatureBlobman")
if blobToDestroy then
 local destroyRem = ReplicatedStorage:FindFirstChild("MenuToys")
 destroyRem = destroyRem and destroyRem:FindFirstChild("DestroyToy")
 if destroyRem then destroyRem:FireServer(blobToDestroy) end
end
end

function bmKick2Server(keepGoing)
 for _, plr in ipairs(Players:GetPlayers()) do
 if keepGoing and not keepGoing() then return end
 if plr ~= LocalPlayer then
 bmKick2Target(plr.Name, function()
 return (not keepGoing) or keepGoing()
 end, false, 12)
 end
 end
end


-- ===== BLOODMAN KILL (перенос из кряк.txt: blobKickAction с RigType-трюком) =====

-- ==============================================
-- SPIN KICK (из 9rr.txt 6570-6686)
-- Вращается вокруг цели → забирает ownership → поднимает цель на высоту
-- Требует: сидеть на Blobman
-- ==============================================
-- ==============================================
-- SPIN LOOP KICK — ПОЛНАЯ КОПИЯ из 9rr.txt 6554-6689
-- Вращается вокруг цели на blobman → забирает ownership → поднимает на customKickHeight
-- Требует: сидеть на Blobman
-- Параметры: SpinRadius, SpinSpeed, SpinKickHeight (customKickHeight)
-- ==============================================
bmSpinAngle = 0
bmSpinRadius = 25
bmSpinSpeed = 0.25
bmSpinKickHeight = 20
_spinLoopActive = false
_spinLoopTask = nil

function bmSpinKickTarget(targetName, keepGoing, reacquire, maxSeconds)
    _spinLoopActive = true
    local spinAngle = 0

    local R = ReplicatedStorage
    local GE = R:FindFirstChild("GrabEvents")

    local myChar = LocalPlayer.Character
    local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
    if not myRoot then
        _spinLoopActive = false
        return
    end

    local savedPos = myRoot.CFrame
    local dragging = false
    local grabStartTime = 0

    -- Цикл ПРЯМО В ТЕЛЕ ФУНКЦИИ (не task.spawn!) — чтобы блокировал пока работает
        while keepGoing() do
            -- Обновляем myChar/myRoot каждый кадр
            myChar = LocalPlayer.Character
            myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myRoot then
                task.wait(0.5)
                continue
            end

            local target = Players:FindFirstChild(targetName)
            if not target or not target.Parent then
                task.wait(0.5)
                continue
            end

            local tChar = target.Character
            if not tChar then
                task.wait(0.3)
                continue
            end

            local tRoot = tChar:FindFirstChild("HumanoidRootPart")
            local tHum = tChar:FindFirstChild("Humanoid")
            if not (tRoot and tHum) or tHum.Health <= 0 then
                dragging = false
                grabStartTime = 0
                task.wait(0.3)
                continue
            end

            -- ПРОВЕРКА: сидим ли на блобмане? Если нет — садимся!
            local myHum = myChar:FindFirstChildOfClass("Humanoid")
            local seat = myHum and myHum.SeatPart
            if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
                if not bmKickingInProgress then
                    -- Сохраняем позицию перед тем как сесть на блобмана
                    if not savedPos then savedPos = myRoot.CFrame end
                    if not sitOnBlobman() then
                        task.wait(0.2)
                        continue
                    end
                    task.wait(0.1)
                    -- После посадки обновляем seat
                    myHum = myChar:FindFirstChildOfClass("Humanoid")
                    seat = myHum and myHum.SeatPart
                    if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
                        task.wait(0.2)
                        continue
                    end
                else
                    task.wait(0.1)
                    continue
                end
            end

            -- Обновляем savedPos если ещё не сохранено
            if not savedPos then savedPos = myRoot.CFrame end

            -- Параметры из глобальных переменных
            local spinRadius = bmSpinRadius or 25
            local spinSpeed = bmSpinSpeed or 0.25
            local customKickHeight = bmSpinKickHeight or 20

            if tRoot and tHum and tHum.Health > 0 then
                tRoot.AssemblyLinearVelocity = Vector3.zero
                tRoot.Velocity = Vector3.zero

                -- Blobman dual hand spam
                if seat then
                    local blobman = seat.Parent
                    local remoteFolder = blobman:FindFirstChild("BlobmanSeatAndOwnerScript")
                    local grab = remoteFolder and remoteFolder:FindFirstChild("CreatureGrab")
                    local drop = remoteFolder and remoteFolder:FindFirstChild("CreatureDrop")

                    local L_Det = blobman:FindFirstChild("LeftDetector")
                    local R_Det = blobman:FindFirstChild("RightDetector")
                    local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
                    local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

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
                    -- Phase 1: вращаемся вокруг цели (захват 0.3 сек)
                    spinAngle = spinAngle + spinSpeed
                    if spinAngle > 6.28 then spinAngle = 0 end

                    local x = math.cos(spinAngle) * spinRadius
                    local z = math.sin(spinAngle) * spinRadius

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
                    -- Phase 2: поднимаем цель на customKickHeight + продолжаем вращать
                    spinAngle = spinAngle + spinSpeed
                    if spinAngle > 6.28 then spinAngle = 0 end

                    local x = math.cos(spinAngle) * spinRadius
                    local z = math.sin(spinAngle) * spinRadius

                    myRoot.CFrame = tRoot.CFrame * CFrame.new(x, 0, z)

                    local lockPos = savedPos * CFrame.new(0, customKickHeight, 0)
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

        -- Возврат на место
        local finalChar = LocalPlayer.Character
        local finalRoot = finalChar and finalChar:FindFirstChild("HumanoidRootPart")
        if finalRoot and savedPos then
            finalRoot.CFrame = savedPos
        end
        _spinLoopActive = false
end


function bmSpinKickServer(keepGoing)
    bmSpinKickTarget(plr.Name, function()
        if not keepGoing or not keepGoing() then return false end
        return bmLoopServer
    end, false, nil)
    local t0 = tick()
    while keepGoing and keepGoing() and bmLoopServer and tick() - t0 < 30 do
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local tHum = plr.Character:FindFirstChildOfClass("Humanoid")
                if tHum and tHum.Health > 0 then
                    bmSpinKickTarget(plr.Name, function() return false end, false, 5)
                end
            end
        end
    end
end

function bmKillTarget(targetName, keepGoing, reacquire, maxSeconds)
fnStart = tick()
killSavedPos = nil
highPos = nil
myHRP = nil  -- объявляем ВНЕ цикла, чтобы финальная очистка работала

-- Вспомогательная функция: проверить, владеем ли целью (через Head.PartOwner)
local function isOwningTarget(targetHead)
    if not targetHead then return false end
    local po = targetHead:FindFirstChild("PartOwner")
    return po and po.Value == LocalPlayer.Name
end

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then
  if not reacquire then break end
  RunService.Heartbeat:Wait()
  continue
 end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 local myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
  RunService.Heartbeat:Wait()
  continue
 end

 -- 1. Сохраняем позицию, поднимаемся на 500 вверх
 if not killSavedPos then
  killSavedPos = myHRP.CFrame
  highPos = killSavedPos * CFrame.new(0, 500, 0)
  myHRP.CFrame = highPos
  myHRP.AssemblyLinearVelocity = Vector3.zero
  task.wait(0.1)
 end

 -- 2. Сесть на блобмана (если не сидим)
 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
  if not bmKickingInProgress then
   if not sitOnBlobman() then
    RunService.Heartbeat:Wait()
    continue
   end
   task.wait(0.1)
   seat = myHum.SeatPart
   if seat and seat.Parent then
    local br = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
    if br then
     br.CFrame = highPos
     br.AssemblyLinearVelocity = Vector3.zero
    end
   end
   myHRP.CFrame = highPos
   myHRP.AssemblyLinearVelocity = Vector3.zero
   RunService.Heartbeat:Wait()
   continue
  else
   RunService.Heartbeat:Wait()
   continue
  end
 end

 local blob = seat.Parent
 local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 -- Держим себя на highPos
 myHRP.CFrame = highPos
 myHRP.AssemblyLinearVelocity = Vector3.zero

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHead = tChar and tChar:FindFirstChild("Head")
 local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
 if not (tRoot and tHum and tHum.Health > 0) then
  RunService.Heartbeat:Wait()
  continue
 end

 bmKickingInProgress = true

 -- 3. БЛОБМАН ИДЁТ К ЦЕЛИ (не я!)
 if blobRoot then
  blobRoot.CFrame = tRoot.CFrame + Vector3.new(0, 5, 0)
  blobRoot.AssemblyLinearVelocity = Vector3.zero
 end
 task.wait(0.1)

 -- 4. ЗАБИРАЕМ OWNERSHIP ЦЕЛИ (SetNetworkOwner)
 pcall(function() GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame) end)
 task.wait(0.1)
 pcall(function() GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame) end)
 task.wait(0.1)

 -- 5. СПАМ CreatureGrab обеими руками (10 раз с интервалом 0.05)
 local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if script and (L_Det or R_Det) and (L_Weld or R_Weld) then
  local grab = script:FindFirstChild("CreatureGrab")
  local release = script:FindFirstChild("CreatureRelease")
  local drop = script:FindFirstChild("CreatureDrop")
  if grab then
   for _ = 1, 10 do
    if not keepGoing() then break end
    pcall(function()
     if L_Det and L_Weld then grab:FireServer(L_Det, tRoot, L_Weld) end
     if R_Det and R_Weld then grab:FireServer(R_Det, tRoot, R_Weld) end
    end)
    task.wait(0.05)
   end
  end
 end
 task.wait(0.2)

 -- 6. КИЛЛ (без проверки PartOwner — просто выполняем)
 -- Парализуем
 pcall(function()
  tHum.BreakJointsOnDeath = false
  tHum.WalkSpeed = 0
  tHum.JumpPower = 0
  tHum.PlatformStand = true
 end)

 -- RigType-трюк из кряка
 if tHum.SeatPart == nil then
  pcall(function()
   if tHum.RigType ~= Enum.HumanoidRigType.R15 then
    tHum.RigType = Enum.HumanoidRigType.R15
   end
   if tHum.RigType ~= Enum.HumanoidRigType.R6 then
    tHum.RigType = Enum.HumanoidRigType.R6
   end
   if tHum.RigType ~= Enum.HumanoidRigType.R15 then
    tHum.RigType = Enum.HumanoidRigType.R15
   end
  end)
 end
 task.wait(0.05)

 -- Финальный спам grab + release
 if script and L_Det and L_Weld then
  pcall(function()
   local grab = script:FindFirstChild("CreatureGrab")
   local release = script:FindFirstChild("CreatureRelease")
   if grab and release then
    for _ = 1, 4 do
     grab:FireServer(L_Det, tRoot, L_Weld)
     task.wait(0.05)
     release:FireServer(L_Weld, tRoot)
    end
   end
  end)
 end

 -- КИЛЛ — используем fling (как в BlobKick из unstable.txt 11945)
 -- НЕ используем ChangeState(Dead) / Health=0 — это клиентский фейк
 -- Вместо этого: SetNetworkOwner спам → AssemblyLinearVelocity (0, 1e10, 0) вверх
 pcall(function() GE_Bloodman.SetNetworkOwner:FireServer(tRoot, tRoot.CFrame) end)
 task.wait(0.05)
 pcall(function() tRoot.AssemblyLinearVelocity = Vector3.new(0, 1e10, 0) end)
 task.wait(0.05)
 -- Дополнительный fling через граб+релиз (как unstable BlobKick line 11945-11950)
 if script and L_Det and L_Weld then
  pcall(function()
   local grab = script:FindFirstChild("CreatureGrab")
   local release = script:FindFirstChild("CreatureRelease")
   if grab and release then
    for _ = 1, 4 do
     grab:FireServer(L_Det, tRoot, L_Weld)
     task.wait(0.05)
     release:FireServer(L_Weld, tRoot)
    end
   end
  end)
 end
 -- Финальный fling (10 млрд вверх)
 pcall(function() tRoot.AssemblyLinearVelocity = Vector3.new(0, 1e10, 0) end)
 task.wait(0.1)

 -- Ещё спам после килла
 if script and L_Det and L_Weld then
  pcall(function()
   local grab = script:FindFirstChild("CreatureGrab")
   local release = script:FindFirstChild("CreatureRelease")
   if grab and release then
    for _ = 1, 4 do
     grab:FireServer(L_Det, tRoot, L_Weld)
     task.wait(0.05)
     release:FireServer(L_Weld, tRoot)
    end
   end
  end)
 end

 -- 6. Возвращаем блобман на highPos (он у нас)
 if blobRoot then
  blobRoot.CFrame = highPos
  blobRoot.AssemblyLinearVelocity = Vector3.zero
 end
 myHRP.CFrame = highPos
 myHRP.AssemblyLinearVelocity = Vector3.zero

 bmKickingInProgress = false

 if not reacquire then break end

 RunService.Heartbeat:Wait()
end

-- Финальная очистка: возвращаем игрока на землю (killSavedPos)
bmKickingInProgress = false
finalChar = LocalPlayer.Character
finalHRP = finalChar and finalChar:FindFirstChild("HumanoidRootPart")
if finalHRP and killSavedPos then
 finalHRP.CFrame = killSavedPos
 finalHRP.AssemblyLinearVelocity = Vector3.zero
end
killSavedPos = nil
highPos = nil
end

function bmKillServer(keepGoing)
while (not keepGoing or keepGoing()) do
 for _, plr in ipairs(Players:GetPlayers()) do
  if plr ~= LocalPlayer and plr.Character then
   local tHum = plr.Character:FindFirstChildOfClass("Humanoid")
   if tHum and tHum.Health > 0 then
    bmKillTarget(plr.Name, function() return true end, false, 8)
   end
  end
 end
 task.wait(0.2)
end
end

-- ===== BLOODMAN VOID (телепорт цели в далёкую точку + BodyPosition lock) =====
function bmVoidTarget(targetName, keepGoing, reacquire, maxSeconds)
fnStart = tick()
voidSavedPos = nil
highPos = nil
myHRP = nil  -- объявляем ВНЕ цикла, чтобы финальная очистка работала

-- Вспомогательная функция: проверить, владеем ли целью (через Head.PartOwner)
local function isOwningTarget(targetHead)
    if not targetHead then return false end
    local po = targetHead:FindFirstChild("PartOwner")
    return po and po.Value == LocalPlayer.Name
end

while keepGoing() do
 if maxSeconds and (tick() - fnStart) > maxSeconds then break end

 local target = Players:FindFirstChild(targetName)
 if not target then
  if not reacquire then break end
  RunService.Heartbeat:Wait()
  continue
 end

 local char = LocalPlayer.Character
 local myHum = char and char:FindFirstChildOfClass("Humanoid")
 local myHRP = char and char:FindFirstChild("HumanoidRootPart")
 if not (myHum and myHRP) then
  RunService.Heartbeat:Wait()
  continue
 end

 -- 1. Сохраняем позицию, поднимаемся на 500 вверх
 if not voidSavedPos then
  voidSavedPos = myHRP.CFrame
  highPos = voidSavedPos * CFrame.new(0, 500, 0)
  myHRP.CFrame = highPos
  myHRP.AssemblyLinearVelocity = Vector3.zero
  task.wait(0.1)
 end

 -- 2. Сесть на блобмана (если не сидим)
 local seat = myHum.SeatPart
 if not (seat and seat.Parent and seat.Parent.Name == "CreatureBlobman") then
  if not bmKickingInProgress then
   if not sitOnBlobman() then
    RunService.Heartbeat:Wait()
    continue
   end
   task.wait(0.1)
   seat = myHum.SeatPart
   if seat and seat.Parent then
    local br = seat.Parent:FindFirstChild("HumanoidRootPart") or seat.Parent.PrimaryPart
    if br then
     br.CFrame = highPos
     br.AssemblyLinearVelocity = Vector3.zero
    end
   end
   myHRP.CFrame = highPos
   myHRP.AssemblyLinearVelocity = Vector3.zero
   RunService.Heartbeat:Wait()
   continue
  else
   RunService.Heartbeat:Wait()
   continue
  end
 end

 local blob = seat.Parent
 local blobRoot = blob:FindFirstChild("HumanoidRootPart") or blob.PrimaryPart

 -- Держим себя на highPos
 myHRP.CFrame = highPos
 myHRP.AssemblyLinearVelocity = Vector3.zero

 local tChar = target.Character
 local tRoot = tChar and tChar:FindFirstChild("HumanoidRootPart")
 local tHead = tChar and tChar:FindFirstChild("Head")
 local tHum = tChar and tChar:FindFirstChildOfClass("Humanoid")
 if not (tRoot and tHum and tHum.Health > 0) then
  RunService.Heartbeat:Wait()
  continue
 end

 bmKickingInProgress = true

 -- 3. БЛОБМАН ИДЁТ К ЦЕЛИ (не я!)
 if blobRoot then
  blobRoot.CFrame = tRoot.CFrame + Vector3.new(0, 5, 0)
  blobRoot.AssemblyLinearVelocity = Vector3.zero
 end
 task.wait(0.1)

 -- 4. ЗАБИРАЕМ OWNERSHIP ЦЕЛИ (SetNetworkOwner)
 pcall(function() GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame) end)
 task.wait(0.1)
 pcall(function() GE_Bloodman.SetNetworkOwner:FireServer(tRoot, myHRP.CFrame) end)
 task.wait(0.1)

 -- 5. СПАМ CreatureGrab обеими руками (10 раз с интервалом 0.05)
 local script = blob:FindFirstChild("BlobmanSeatAndOwnerScript")
 local L_Det = blob:FindFirstChild("LeftDetector")
 local R_Det = blob:FindFirstChild("RightDetector")
 local L_Weld = L_Det and (L_Det:FindFirstChild("LeftWeld") or L_Det:FindFirstChild("RigidConstraint"))
 local R_Weld = R_Det and (R_Det:FindFirstChild("RightWeld") or R_Det:FindFirstChild("RigidConstraint"))

 if script and (L_Det or R_Det) and (L_Weld or R_Weld) then
  local grab = script:FindFirstChild("CreatureGrab")
  local release = script:FindFirstChild("CreatureRelease")
  local drop = script:FindFirstChild("CreatureDrop")
  if grab then
   for _ = 1, 10 do
    if not keepGoing() then break end
    pcall(function()
     if L_Det and L_Weld then grab:FireServer(L_Det, tRoot, L_Weld) end
     if R_Det and R_Weld then grab:FireServer(R_Det, tRoot, R_Weld) end
    end)
    task.wait(0.05)
   end
  end
 end
 task.wait(0.2)

 -- 6. ТЕЛЕПОРТ ЦЕЛИ В ВОЙД (без проверки PartOwner — просто выполняем)
 -- Парализуем цель
 pcall(function()
  tHum.PlatformStand = true
  tHum.WalkSpeed = 0
  tHum.JumpPower = 0
 end)

 -- 8 циклов: сдвигаем цель вниз на 100000 + BodyVelocity -9e9 (как Void Aura, только сильнее)
 for _ = 1, 8 do
  pcall(function()
   tRoot.CFrame = tRoot.CFrame - Vector3.new(0, 100000, 0)
   local bv2 = Instance.new("BodyVelocity", tRoot)
   bv2.Velocity = Vector3.new(0, -9e9, 0)
   bv2.MaxForce = Vector3.one * math.huge
   game:GetService("Debris"):AddItem(bv2, 0.1)
  end)
  task.wait(0.05)
 end

 -- Спам ремок блобмана для удержания цели пока та падает
 if script and L_Det and L_Weld then
  pcall(function()
   local grab = script:FindFirstChild("CreatureGrab")
   local release = script:FindFirstChild("CreatureRelease")
   local drop = script:FindFirstChild("CreatureDrop")
   for _ = 1, 3 do
    if grab then grab:FireServer(L_Det, tRoot, L_Weld) end
    task.wait(0.05)
    if drop then drop:FireServer(L_Weld, tRoot) end
    if release then release:FireServer(L_Weld, tRoot) end
    task.wait(0.05)
   end
  end)
 end

 -- Финальный пушёк вниз на 1 000 000 + BodyVelocity на 0.5 сек
 pcall(function()
  tRoot.CFrame = tRoot.CFrame - Vector3.new(0, 1000000, 0)
  local bvFinal = Instance.new("BodyVelocity", tRoot)
  bvFinal.Velocity = Vector3.new(0, -9e9, 0)
  bvFinal.MaxForce = Vector3.one * math.huge
  game:GetService("Debris"):AddItem(bvFinal, 0.5)
 end)
 task.wait(0.3)

 -- 6. Возвращаем блобман на highPos (он у нас)
 if blobRoot then
  blobRoot.CFrame = highPos
  blobRoot.AssemblyLinearVelocity = Vector3.zero
 end
 myHRP.CFrame = highPos
 myHRP.AssemblyLinearVelocity = Vector3.zero

 bmKickingInProgress = false

 if not reacquire then break end

 RunService.Heartbeat:Wait()
end

-- Финальная очистка: возвращаем игрока на землю (voidSavedPos)
bmKickingInProgress = false
finalChar = LocalPlayer.Character
finalHRP = finalChar and finalChar:FindFirstChild("HumanoidRootPart")
if finalHRP and voidSavedPos then
 finalHRP.CFrame = voidSavedPos
 finalHRP.AssemblyLinearVelocity = Vector3.zero
end
voidSavedPos = nil
highPos = nil
end

function bmVoidServer(keepGoing)
while (not keepGoing or keepGoing()) do
 for _, plr in ipairs(Players:GetPlayers()) do
  if plr ~= LocalPlayer and plr.Character then
   local tHum = plr.Character:FindFirstChildOfClass("Humanoid")
   if tHum and tHum.Health > 0 then
    bmVoidTarget(plr.Name, function() return true end, false, 8)
   end
  end
 end
 task.wait(0.2)
end
end

print("[OBLIVION DEBUG] Before MISC section")
do
-- ==============================================
-- Вкладка: MISC
-- ==============================================
MiscSection = Tabs.Misc:AddLeftGroupbox("Combat & Automation")

-- === SAFE ADDON: FPS BOOST / TEXTURE OPTIMIZER (NO DISTANCE TOUCH) ===
do
 MiscSection:AddToggle("EnableFPSBoost", {
 Text = "FPS Boost",
 Default = false,
 Tooltip = "Меняет текстуры/материалы/эффекты. Дальность камеры НЕ трогает.",
 })

 fpsOriginal = {
 saved = false,
 GlobalShadows = nil,
 materials = {},
 castShadow = {},
 decals = {},
 enabled = {},
 }
 fpsProcessing = false

 function isCharacterPart(inst)
 local cur = inst
 while cur and cur ~= workspace do
 if cur:FindFirstChildOfClass("Humanoid") then return true end
 if Players:GetPlayerFromCharacter(cur) then return true end
 cur = cur.Parent
 end
 return false
 end

 function saveOnce()
 if fpsOriginal.saved then return end
 fpsOriginal.GlobalShadows = Lighting.GlobalShadows
 fpsOriginal.saved = true
 end

 function restoreFps()
 Lighting.GlobalShadows = fpsOriginal.GlobalShadows
 for obj, mat in pairs(fpsOriginal.materials) do
 if obj and obj.Parent then obj.Material = mat end
 end
 for obj, cs in pairs(fpsOriginal.castShadow) do
 if obj and obj.Parent then pcall(function() obj.CastShadow = cs end) end
 end
 for obj, tr in pairs(fpsOriginal.decals) do
 if obj and obj.Parent then obj.Transparency = tr end
 end
 for obj, en in pairs(fpsOriginal.enabled) do
 if obj and obj.Parent then pcall(function() obj.Enabled = en end) end
 end
 end

 function applyFpsChunked()
 if fpsProcessing then return end
 fpsProcessing = true
 saveOnce()
 Lighting.GlobalShadows = false
 pcall(function() sethiddenproperty(workspace.Terrain, "Decoration", false) end)

 local all = workspace:GetDescendants()
 for i, v in ipairs(all) do
 if not (Toggles.EnableFPSBoost and Toggles.EnableFPSBoost.Value) then break end
 if v:IsA("BasePart") and not isCharacterPart(v) then
 if fpsOriginal.materials[v] == nil then fpsOriginal.materials[v] = v.Material end
 if fpsOriginal.castShadow[v] == nil then pcall(function() fpsOriginal.castShadow[v] = v.CastShadow end) end
 v.Material = Enum.Material.SmoothPlastic
 pcall(function() v.CastShadow = false end)
 elseif v:IsA("Decal") or v:IsA("Texture") then
 if fpsOriginal.decals[v] == nil then fpsOriginal.decals[v] = v.Transparency end
 v.Transparency = 1
 elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Beam") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") then
 if fpsOriginal.enabled[v] == nil then pcall(function() fpsOriginal.enabled[v] = v.Enabled end) end
 pcall(function() v.Enabled = false end)
 end
 -- важно: даём кадрам дышать, чтобы не было жёсткого фриза
 if i % 250 == 0 then task.wait() end
 end
 fpsProcessing = false
 end

 Toggles.EnableFPSBoost:OnChanged(function()
 if Toggles.EnableFPSBoost.Value then
 task.spawn(applyFpsChunked)
 else
 fpsProcessing = false
 restoreFps()
 pcall(function() sethiddenproperty(workspace.Terrain, "Decoration", true) end)
 end
 end)
end

VirtualInputManager = game:GetService("VirtualInputManager")

do
-- === SILENT AIM / FOV CIRCLE ===
-- Круг FOV в центре экрана: вкл/выкл, цвет и разме��.
-- === SILENT AIM / FOV CIRCLE ===
-- Круг FOV в центре экрана: вкл/выкл, цвет и разме��.
FovSection = Tabs.Misc:AddRightGroupbox("Silent Aim")

fovCircleColor = Color3.fromRGB(255, 255, 255)
local fovCircle -- Drawing создаётся ниже В pcall (не все executor'ы дают Circle) = nil

-- СНАЧАЛА строим UI — тумблер/цвет/ползунок всегда должны появляться,
-- даже если рисование круга не заведётся.

-- ГЛАВНЫЙ тумблер функции Silent Aim (автонаведение при клике)
FovSection:AddToggle("EnableSilentAim", {
 Text = "Silent Aim",
 Default = false,
 Tooltip = "Silent aim: захват уходит в ближайшего игрока в круге FOV, даж�� если смотришь мимо (камера не двигается)",
})

FovSection:AddToggle("EnableFOVCircle", {
 Text = "FOV Circle",
 Default = false,
 Tooltip = "Показать кр��г FOV в центре экрана",
}):AddColorPicker("FOVCircleColor", {
 Default = Color3.fromRGB(255, 255, 255),
 Title = "Цвет круга",
 Callback = function(Value)
 fovCircleColor = Value
 if fovCircle then fovCircle.Color = Value end
 end,
})

FovSection:AddSlider("FOVCircleSize", {
 Text = "Размер круга",
 Default = 100,
 Min = 20,
 Max = 500,
 Rounding = 0,
 Compact = false,
 Tooltip = "Радиус круга FOV",
})

-- Теперь безопасно создаём сам круг (если executor не умеет — UI уже есть)
pcall(function()
 fovCircle = Drawing.new("Circle")
 fovCircle.Thickness = 1.5
 fovCircle.NumSides = 64
 fovCircle.Filled = false
 fovCircle.Transparency = 1
 fovCircle.Color = fovCircleColor
 fovCircle.Visible = false
end)

-- Круг рисуется каждый кадр в центре экрана
RunService.RenderStepped:Connect(function()
 if not fovCircle then return end
 if not (Toggles.EnableFOVCircle and Toggles.EnableFOVCircle.Value) then
 fovCircle.Visible = false
 return
 end
 local cam = workspace.CurrentCamera
 if not cam then fovCircle.Visible = false; return end
 local vp = cam.ViewportSize
 fovCircle.Position = Vector2.new(vp.X / 2, vp.Y / 2)
 fovCircle.Radius = Options.FOVCircleSize and Options.FOVCircleSize.Value or 100
 fovCircle.Color = fovCircleColor
 fovCircle.Visible = true
end)

-- === SILENT AIM: логика наведения ===
-- Цель = ближайший к центру экра��а игрок, попадающий В КРУГ FOV.
function getSilentAimTarget()
 local cam = workspace.CurrentCamera
 if not cam then return nil end
 local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
 local radius = Options.FOVCircleSize and Options.FOVCircleSize.Value or 100
 local nearest, nearestDist = nil, radius
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character then
 local root = plr.Character:FindFirstChild("HumanoidRootPart")
 local hum = plr.Character:FindFirstChild("Humanoid")
 if root and hum and hum.Health > 0 then
 local sp, onScreen = cam:WorldToViewportPoint(root.Position)
 if onScreen then
 local d = (Vector2.new(sp.X, sp.Y) - center).Magnitude
 if d < nearestDist then
 nearestDist = d
 nearest = plr
 end
 end
 end
 end
 end
 return nearest
end

-- === SILENT AIM ===
-- Оригинальная рабочая функция захвата (GrabPartFake) из готового скрипта,
-- перенесённая как есть со всеми зависимостями: поиск ближайшего
-- игрока, клон модели захвата, сетевое владение, серверные события.
-- Наводиться не нужно — по клику ЛКМ хватается ближайший видимый игрок
-- (камера не двигается). ПКМ/повторный клик — бросить, R — вращение.

silentAimLP = LocalPlayer
replicatedFirstService = game:GetService("ReplicatedFirst")
silentPlayerGui = silentAimLP:FindFirstChild("PlayerGui") or silentAimLP:WaitForChild("PlayerGui", 10)

-- Уведомление игры о действиях грабежа (ActionEvent)
function ActionEvent(actionName, actionState)
 local controlsGui = silentPlayerGui:FindFirstChild("ControlsGui")
 local actionEvent = controlsGui and controlsGui:FindFirstChild("ActionEvent")
 if actionEvent then
 actionEvent:Fire(actionName, actionState)
 end
end

-- Серверные события грабежа
grabEventsFolder = ReplicatedStorage:FindFirstChild("GrabEvents") or ReplicatedStorage:WaitForChild("GrabEvents", 10)
createGrabLineEvent = grabEventsFolder and (grabEventsFolder:FindFirstChild("CreateGrabLine") or grabEventsFolder:WaitForChild("CreateGrabLine", 5))
destroyGrabLineEvent = grabEventsFolder and (grabEventsFolder:FindFirstChild("DestroyGrabLine") or grabEventsFolder:WaitForChild("DestroyGrabLine", 5))
setNetworkOwnerEvent = grabEventsFolder and (grabEventsFolder:FindFirstChild("SetNetworkOwner") or grabEventsFolder:WaitForChild("SetNetworkOwner", 5))
extendGrabLineRemoteEvent = grabEventsFolder and (grabEventsFolder:FindFirstChild("ExtendGrabLine") or grabEventsFolder:WaitForChild("ExtendGrabLine", 5))

-- Проверка сетевого владельца детали (CheckNetworkOwnerShipOnPart)
function CheckNetworkOwnerShipOnPart(potentialPart, condition)
 if typeof(potentialPart) == "Instance" and (potentialPart:FindFirstChild("PartOwner") and potentialPart.PartOwner.Value == silentAimLP.Name) then
 return not condition and true or potentialPart.PartOwner
 end
end

-- Шаблон модели грабежа
GrabPartsModel = replicatedFirstService:FindFirstChild("GrabParts") or replicatedFirstService:WaitForChild("GrabParts", 10)
_G.ActualFakeGrabParts = nil
_G.RotatingFakeGrabParts = false

-- Части тела д��я захвата + дальность
SilentAimHitbox = { "Head", "Torso", "Left Leg", "Right Leg" }
maxDistance = 30

-- Глобалы игрока (_G.UniverPlayerHumanoid / _G.UniversalPlayerRoot)
function refreshUniversals()
 local char = silentAimLP.Character
 if char then
 _G.UniverPlayerHumanoid = char:FindFirstChildOfClass("Humanoid")
 _G.UniversalPlayerRoot = char:FindFirstChild("HumanoidRootPart")
 end
end
refreshUniversals()
silentAimLP.CharacterAdded:Connect(function()
 task.wait(0.3)
 refreshUniversals()
end)

-- Поиск ближайшего игр��ка + фильтр по кругу FOV.
-- Круг ограничивает захват ТОЛЬКО когда он включён (FOV Circle).
-- Выключен — работает как раньше: ближайший видимый игрок.
function findNearestPlayer()
 local nearestDistance = math.huge
 local nearestPlayer = nil
 local cam = workspace.CurrentCamera
 local lpChar = silentAimLP.Character
 local lpRoot = lpChar and lpChar:FindFirstChild("HumanoidRootPart")
 if not lpRoot or not cam then return nil end
 -- Тот же центр/радиус, что у нарисованного круга (экранные координаты)
 local useFov = Toggles.EnableFOVCircle and Toggles.EnableFOVCircle.Value
 local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
 local radius = (Options.FOVCircleSize and Options.FOVCircleSize.Value) or 100
 for _, player in ipairs(Players:GetPlayers()) do
 if player.Name ~= silentAimLP.Name and player.Character then
 local hrp = player.Character:FindFirstChild("HumanoidRootPart")
 if hrp then
 local sp, onScreen = cam:WorldToScreenPoint(hrp.Position)
 if onScreen then
 local inFov = true
 if useFov then
 inFov = (Vector2.new(sp.X, sp.Y) - center).Magnitude <= radius
 end
 if inFov then
 local distance = (lpRoot.Position - hrp.Position).Magnitude
 if distance < nearestDistance then
 nearestPlayer = player
 nearestDistance = distance
 end
 end
 end
 end
 end
 end
 return nearestPlayer
end

nearestPlayerInfo = nil
RunService.RenderStepped:Connect(function()
 if Toggles.EnableSilentAim and Toggles.EnableSilentAim.Value then
 nearestPlayerInfo = findNearestPlayer()
 else
 nearestPlayerInfo = nil
 end
end)

-- === SILENT AIM: Raycast-хук ===
-- Перехватывает игровой луч захвата и направляет его в ту же силент-цель,
-- что и фейковый захват. Тогда игровой и фейковый бьют в одну точку
-- → одна линия, без промахов и без второй «чужой» линии.
function calculateDirectionalVector(startPosition, targetPosition, distanceMultiplier)
 return (targetPosition - startPosition).Unit * distanceMultiplier
end

if hookmetamethod then
 local namecallHook = nil
 namecallHook = hookmetamethod(game, "__namecall", function(...)
 local raycastData = { ... }
 pcall(function()
 if raycastData[1] ~= workspace then return end
 if checkcaller() then return end
 if getnamecallmethod() ~= "Raycast" then return end
 if not (Toggles.EnableSilentAim and Toggles.EnableSilentAim.Value) then return end
 local target = nearestPlayerInfo
 if not (target and target ~= silentAimLP and target.Character) then return end
 local tHrp = target.Character:FindFirstChild("HumanoidRootPart")
 local tHum = target.Character:FindFirstChildOfClass("Humanoid")
 local lpChar = silentAimLP.Character
 local lpHrp = lpChar and lpChar:FindFirstChild("HumanoidRootPart")
 if not (tHrp and tHum and tHum.Health > 0 and lpHrp) then return end
 -- цель не в своём плоте (в FTAP свойства может не быть → считаем что можно)
 local okPlot, inPlot = pcall(function() return target.InPlot.Value end)
 if okPlot and inPlot then return end
 local distanceToTarget = (lpHrp.Position - tHrp.Position).Magnitude
 local hbName = SilentAimHitbox[math.random(1, #SilentAimHitbox)]
 local hbPart = target.Character:FindFirstChild(hbName) or tHrp
 if distanceToTarget <= maxDistance and hbPart then
 raycastData[3] = calculateDirectionalVector(raycastData[2], hbPart.Position, 1000)
 raycastData[4] = RaycastParams.new()
 raycastData[4].FilterDescendantsInstances = { target.Character }
 raycastData[4].FilterType = Enum.RaycastFilterType.Include
 end
 end)
 return namecallHook(unpack(raycastData))
 end)
end

-- Камера-лок для вращения схваченного (клавиша R) — lockCamera
local var120_upvw = nil
function lockCamera()
 if _G.UniversalPlayerRoot and var120_upvw then
 workspace.CurrentCamera.CFrame = CFrame.new(_G.UniversalPlayerRoot.CFrame * var120_upvw) * workspace.CurrentCamera.CFrame.Rotation
 end
 if _G.RotatingFakeGrabParts == false then
 RunService:UnbindFromRenderStep("camBinding1")
 workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
 UserInputService.MouseBehavior = Enum.MouseBehavior.Default
 end
end

-- Основная функция фейкового грабежа (GrabPartFake)
local pcDistance2 = nil
function GrabPartFake(potentialGrabPart)
 local localPlayerCharacter = silentAimLP.Character
 local actualFakeGrabParts = _G.ActualFakeGrabParts
 if localPlayerCharacter and (potentialGrabPart and (potentialGrabPart:IsA("BasePart") and (not actualFakeGrabParts and (_G.UniverPlayerHumanoid and _G.UniverPlayerHumanoid.Health > 0)))) then
 if _G.RealGrabParts then
 return
 end
 local headPart = potentialGrabPart.Parent:FindFirstChild("Head")
 local targetHumanoid = potentialGrabPart.Parent:FindFirstChildOfClass("Humanoid")
 local grabPartOrHead = headPart or potentialGrabPart
 local isGrabbing = false
 local isRotatingFakeGrabParts = false
 local currentReach = silentAimLP:FindFirstChild("CurrentReach")
 if targetHumanoid and targetHumanoid.Health < 1 then
 return nil
 end
 pcDistance2 = (potentialGrabPart.Position - workspace.CurrentCamera.CFrame.Position).Magnitude
 local grabPartsModelClone = GrabPartsModel:Clone()
 grabPartsModelClone:SetAttribute("Fake", true)
 grabPartsModelClone.Name = "GrabParts"
 pcall(function()
 grabPartsModelClone.GrabPart.Color = silentAimLP.BeamColor.BallColorHolder.Value
 grabPartsModelClone.BeamPart.GrabBeam.Color = silentAimLP.BeamColor.ColorSequenceHolder.Color
 end)
 if currentReach and currentReach.Value == 40 then
 pcall(function() grabPartsModelClone.BeamPart.GrabBeam.Texture = "rbxassetid://8933355899" end)
 end
 grabPartsModelClone.DragPart.Anchored = true
 grabPartsModelClone.GrabPart.GrabAttach.Orientation = workspace.RotateOrientPart.PartOrient.WorldOrientation
 grabPartsModelClone.DragPart.DragAttach.WorldOrientation = workspace.RotateOrientPart.PartOrient.WorldOrientation
 grabPartsModelClone.GrabPart.WeldConstraint.Part1 = potentialGrabPart
 grabPartsModelClone.GrabPart.Position = potentialGrabPart.Position
 grabPartsModelClone.GrabPart.Anchored = false
 grabPartsModelClone.BeamPart.Anchored = true
 grabPartsModelClone.BeamPart.GrabBeam.Attachment0 = localPlayerCharacter:FindFirstChild("CamPart").Attachment
 grabPartsModelClone.Parent = workspace
 _G.ActualFakeGrabParts = grabPartsModelClone
 local inputChangedConnection = UserInputService.InputChanged:Connect(function(inputObject, inputIsProcessed)
 if not inputIsProcessed then
 if inputObject.UserInputType == Enum.UserInputType.MouseWheel then
 if inputObject.Position.Z <= 0 then
 if inputObject.Position.Z < 0 then
 pcDistance2 = math.floor(pcDistance2 + inputObject.Position.Z * 2)
 end
 else
 pcDistance2 = math.ceil(pcDistance2 + inputObject.Position.Z * 2)
 end
 if pcDistance2 >= 3 then
 if pcDistance2 >= 30 then
 pcDistance2 = 30
 end
 else
 pcDistance2 = 3
 end
 extendGrabLineRemoteEvent:FireServer(pcDistance2)
 end
 if isRotatingFakeGrabParts then
 local rotateOrientPartClone = workspace.RotateOrientPart:Clone()
 rotateOrientPartClone.Anchored = true
 rotateOrientPartClone.Orientation = Vector3.new(rotateOrientPartClone.Orientation.X + inputObject.Delta.Y, rotateOrientPartClone.Orientation.Y + inputObject.Delta.X, rotateOrientPartClone.Orientation.Z)
 workspace.RotateOrientPart.PartOrient.WorldOrientation = rotateOrientPartClone:WaitForChild("PartOrient").WorldOrientation
 rotateOrientPartClone:Destroy()
 end
 end
 end)
 pcall(function()
 grabPartsModelClone.GrabPart.BeamSound:Play()
 grabPartsModelClone.GrabPart.AttachSound:Play()
 end)
 ActionEvent("HoldControls", false)
 ActionEvent("GrabbingControls", true)
 ActionEvent("GrabControls", false)
 function destroyGrabParts()
 grabPartsModelClone:Destroy()
 if potentialGrabPart.Anchored == false then
 local totalMass = 0
 if potentialGrabPart.Parent:IsA("Model") and potentialGrabPart.Parent.Name ~= "Workspace" then
 for _, childPart in ipairs(potentialGrabPart.Parent:GetChildren()) do
 if childPart:IsA("BasePart") then
 totalMass = totalMass + childPart.Mass
 end
 end
 for _, childPart2 in ipairs(potentialGrabPart.Parent:GetChildren()) do
 if childPart2:IsA("BasePart") then
 childPart2.Velocity = workspace.CurrentCamera.CFrame.LookVector * 50
 end
 end
 end
 local calculatedVelocity = workspace.CurrentCamera.CFrame.LookVector * 750 / (totalMass + potentialGrabPart.Mass) + workspace.CurrentCamera.CFrame.LookVector * 15
 if calculatedVelocity.Magnitude < 100 then
 calculatedVelocity = workspace.CurrentCamera.CFrame.LookVector * 100
 end
 potentialGrabPart.Velocity = calculatedVelocity
 end
 end
 inputBeganConnection = UserInputService.InputBegan:Connect(function(inputObject2, inputIsProcessed2)
 if not inputIsProcessed2 then
 if inputObject2.UserInputType ~= Enum.UserInputType.MouseButton2 then
 if inputObject2.KeyCode == Enum.KeyCode.R then
 if isRotatingFakeGrabParts then
 isRotatingFakeGrabParts = false
 _G.RotatingFakeGrabParts = false
 workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
 UserInputService.MouseBehavior = Enum.MouseBehavior.Default
 RunService:UnbindFromRenderStep("camBinding1")
 ActionEvent("RotatingControls", false)
 ActionEvent("RotateControls", true)
 else
 isRotatingFakeGrabParts = true
 _G.RotatingFakeGrabParts = true
 var120_upvw = _G.UniversalPlayerRoot.CFrame:ToObjectSpace(workspace.CurrentCamera.CFrame).Position
 workspace.CurrentCamera.CameraType = Enum.CameraType.Scriptable
 UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter
 RunService:BindToRenderStep("camBinding1", Enum.RenderPriority.Camera.Value - 1, lockCamera)
 ActionEvent("RotatingControls", true)
 ActionEvent("RotateControls", false)
 ActionEvent("")
 end
 end
 else
 destroyGrabParts()
 end
 end
 end)
 descendantAddedConnection = workspace.DescendantAdded:Connect(function(potentialGrabParts)
 if grabPartsModelClone.Parent and (potentialGrabParts.Name == "GrabParts" and (potentialGrabParts ~= grabPartsModelClone and potentialGrabParts.Parent == workspace)) then
 RunService:UnbindFromRenderStep("dragBinding")
 RunService:UnbindFromRenderStep("buttonDistanceMoving")
 local dragPart = potentialGrabParts:FindFirstChild("DragPart")
 local beamPart = potentialGrabParts:FindFirstChild("BeamPart")
 local grabPart = potentialGrabParts:FindFirstChild("GrabPart")
 if dragPart and (beamPart and grabPart) then
 beamPart.GrabBeam.Enabled = false
 grabPart.Transparency = 1
 grabPart.AttachSound.Volume = 0
 grabPart.BeamSound.Volume = 0
 dragPart.AlignOrientation.Enabled = false
 dragPart.AlignPosition.Enabled = false
 createGrabLineEvent:FireServer(potentialGrabPart, CFrame.new(0, 0, 0))
 end
 end
 end)
 setNetworkOwnerEvent:FireServer(grabPartOrHead, workspace.CurrentCamera.CFrame)
 task.spawn(function()
 while grabPartsModelClone.Parent and potentialGrabPart:IsDescendantOf(workspace) do
 if CheckNetworkOwnerShipOnPart(grabPartOrHead) and not isGrabbing then
 pcDistance2 = (potentialGrabPart.Position - workspace.CurrentCamera.CFrame.Position).Magnitude
 extendGrabLineRemoteEvent:FireServer(pcDistance2)
 createGrabLineEvent:FireServer(potentialGrabPart, CFrame.new(0, 0, 0))
 pcall(function() silentAimLP.PlayerScripts.CharacterAndBeamMove.GrabNotifyEvent:Fire(true) end)
 isGrabbing = true
 elseif not CheckNetworkOwnerShipOnPart(grabPartOrHead) and isGrabbing then
 isGrabbing = false
 end
 grabPartsModelClone.DragPart.Position = workspace.CurrentCamera.CFrame.LookVector * pcDistance2 + workspace.CurrentCamera.CFrame.Position
 grabPartsModelClone.DragPart.DragAttach.WorldOrientation = workspace.RotateOrientPart.PartOrient.WorldOrientation
 grabPartsModelClone.BeamPart.CFrame = CFrame.lookAt(grabPartsModelClone.GrabPart.Position, grabPartsModelClone.DragPart.Position, Vector3.new(0, 0, 1))
 grabPartsModelClone.BeamPart.GrabBeam.CurveSize1 = (grabPartsModelClone.GrabPart.Position - grabPartsModelClone.DragPart.Position).Magnitude * 1.5
 grabPartsModelClone.GrabPart.BeamSound.PlaybackSpeed = (grabPartsModelClone.GrabPart.Position - grabPartsModelClone.DragPart.Position).Magnitude * 1.5 * 1.5 / 2 + 2.5
 task.wait()
 end
 inputChangedConnection:Disconnect()
 inputBeganConnection:Disconnect()
 descendantAddedConnection:Disconnect()
 ActionEvent("GrabbingControls", false)
 ActionEvent("GrabControls", false)
 ActionEvent("RotatingControls", false)
 ActionEvent("RotateControls", false)
 pcall(function() silentAimLP.PlayerScripts.CharacterAndBeamMove.GrabNotifyEvent:Fire(false) end)
 destroyGrabLineEvent:FireServer(potentialGrabPart)
 if _G.ActualFakeGrabParts then
 _G.ActualFakeGrabParts:Destroy()
 end
 _G.ActualFakeGrabParts = nil
 _G.RotatingFakeGrabParts = false
 isRotatingFakeGrabParts = false
 workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
 UserInputService.MouseBehavior = Enum.MouseBehavior.Default
 end)
 elseif actualFakeGrabParts then
 actualFakeGrabParts:Destroy()
 end
end

-- ЛКМ: хватаем ближайшего игрока 
UserInputService.InputBegan:Connect(function(inputObject3)
 if inputObject3.UserInputType == Enum.UserInputType.MouseButton1 then
 if _G.ActualFakeGrabParts then
 _G.ActualFakeGrabParts:Destroy()
 return
 end
 if nearestPlayerInfo and (nearestPlayerInfo ~= silentAimLP and (nearestPlayerInfo.Character and (nearestPlayerInfo.Character:FindFirstChild("HumanoidRootPart") and (Toggles.EnableSilentAim and Toggles.EnableSilentAim.Value)))) then
 if not (silentAimLP.Character and silentAimLP.Character:FindFirstChild("HumanoidRootPart")) then return end
 local characterDistance = (silentAimLP.Character.HumanoidRootPart.Position - nearestPlayerInfo.Character.HumanoidRootPart.Position).Magnitude
 local hbIndex = math.random(1, #SilentAimHitbox)
 local hbName = SilentAimHitbox[hbIndex]
 local hbPart = nearestPlayerInfo.Character:FindFirstChild(hbName)
 or nearestPlayerInfo.Character:FindFirstChild("HumanoidRootPart")
 or nearestPlayerInfo.Character:FindFirstChild("Torso")
 if characterDistance <= maxDistance and hbPart then
 GrabPartFake(hbPart)
 end
 end
 end
end)

MiscSection:AddToggle("EnableLockTap", {
 Text = "Lock Tap (Aimbot)",
 Default = false,
 Tooltip = "Нажми бинд: камера мгновенно наводится на врага + клик",
}):AddKeyPicker("LockTapKeybind", {
 Default = "None", -- ПКМ по умолчанию
 SyncToggleState = false,
 Mode = "Hold",
 Text = "Lock Tap Key",
 NoUI = true,
})

MiscSection:AddSlider("LockDistance", {
 Text = "Lock Distance",
 Default = 100,
 Min = 10,
 Max = 500,
 Rounding = 0,
 Compact = false,
 Tooltip = "Дистанция для Lock Tap",
})

MiscSection:AddToggle("EnableTriggerBot", {
 Text = "TriggerBot",
 Default = false,
 Tooltip = "Автоклик при нав��дении пр��цела на игрока",
}):AddKeyPicker("TriggerBotKeybind", {
 Default = "None",
 SyncToggleState = false,
 Mode = "Hold",
 Text = "Trigger Bot Key",
 NoUI = false,
})

MiscSection:AddDropdown("TriggerBotMode", {
 Text = "Срабатывание",
 Default = "Всегда",
 Values = { "Всегда", "Зажатие бинда", "Переключатель" },
 Multi = false,
 Tooltip = "Всегда — работает постоянно; Зажатие — пока держишь бинд; Переключатель — вкл/выкл по нажатию бинда",
})

MiscSection:AddSlider("TriggerBotDistance", {
 Text = "Дистанция TriggerBot",
 Default = 30,
 Min = 10,
 Max = 30,
 Rounding = 0,
 Compact = false,
 Tooltip = "Дальность захвата. Максимум в игре — 30 метров (с геймпассом).",
})

local function getNearestPlayer(maxDist)
 local nearest = nil
 local minDist = maxDist
 local cam = workspace.CurrentCamera
 if not cam then return nil end
 local myPos = cam.CFrame.Position
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
 local dist = (plr.Character.HumanoidRootPart.Position - myPos).Magnitude
 if dist < minDist then
 minDist = dist
 nearest = plr
 end
 end
 end
 return nearest
end

UserInputService.InputBegan:Connect(function(input, gpe)
 if gpe then return end
 if Toggles.EnableLockTap and Toggles.EnableLockTap.Value then
 local boundKey = Options.LockTapKeybind.Value
 local isMatch = false
 
 if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then
 isMatch = true
 end
 
 if boundKey == "RightClick" or boundKey == "MB2" or boundKey == "MouseButton2" then
 if input.UserInputType == Enum.UserInputType.MouseButton2 then isMatch = true end
 end
 if boundKey == "LeftClick" or boundKey == "MB1" or boundKey == "MouseButton1" then
 if input.UserInputType == Enum.UserInputType.MouseButton1 then isMatch = true end
 end

 if isMatch then
 local target = getNearestPlayer(Options.LockDistance.Value)
 if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
 local root = target.Character.HumanoidRootPart
 local cam = workspace.CurrentCamera
 if cam then
 -- МГНОВЕННЫЙ поворот камеры БЕЗ задержки (��ак в оригинале)
 cam.CFrame = CFrame.new(cam.CFrame.Position, root.Position)
 
 -- Клик БЕЗ task.wait, координаты (0, 0) (как в оригинальном скрипте)
 VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 1)
 VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 1)
 end
 end
 end
 end
end)

-- === TRIGGER BOT ===
-- Наводишь ЦЕНТР камеры (прицел) на игрока в пределах дистанци�� → авто-захват.
-- Пока идёт захват (в мире есть GrabParts) — новую цель не берём; как только
-- захват закончился — снова готов к следующему.
-- Захват = mouse1press (ЗАЖАТИЕ ЛКМ), а не короткий клик — так т��ебует игра.
Triggerbot = {
 Enabled = false,
 Connection = nil,
 canGrab = true,
 maxDistance = 20,
 preGrabDelay = 0.00001,
 postGrabDelay = 0.05,
 lastTarget = nil,
 lastHitTime = 0,
 targetMemoryDuration = 0.1,
 checkThrottle = 0.008,
 lastCheck = 0,
}
tbRayParams = RaycastParams.new()
tbRayParams.FilterType = Enum.RaycastFilterType.Exclude

-- Отслеживание бинда для режимо�� «зажатие» и «переключатель» TriggerBot
tbHeld = false
tbToggled = false

local function tbKeyMatches(input, boundKey)
 if boundKey == nil then return false end
 if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then return true end
 if (boundKey == "MB2" or boundKey == "MouseButton2" or boundKey == "RightClick") and input.UserInputType == Enum.UserInputType.MouseButton2 then return true end
 if (boundKey == "MB1" or boundKey == "MouseButton1" or boundKey == "LeftClick") and input.UserInputType == Enum.UserInputType.MouseButton1 then return true end
 return false
end

UserInputService.InputBegan:Connect(function(input, gpe)
 if gpe then return end
 if not (Toggles.EnableTriggerBot and Toggles.EnableTriggerBot.Value) then return end
 local boundKey = Options.TriggerBotKeybind and Options.TriggerBotKeybind.Value
 if tbKeyMatches(input, boundKey) then
 tbHeld = true
 tbToggled = not tbToggled
 if Options.TriggerBotMode and Options.TriggerBotMode.Value == "Переключатель" then
 Library:Notify("Trigger Bot: " .. (tbToggled and "ВКЛ" or "ВЫКЛ"), 1)
 end
 end
end)

UserInputService.InputEnded:Connect(function(input)
 local boundKey = Options.TriggerBotKeybind and Options.TriggerBotKeybind.Value
 if tbKeyMatches(input, boundKey) then
 tbHeld = false
 end
end)

-- Цель = живой игрок под ЦЕНТРОМ камеры в пределах дистанции.
function Triggerbot:GetTarget()
 local c = LocalPlayer.Character
 if not c or not c:FindFirstChild("HumanoidRootPart") then
 return
 end
 -- Уже идёт захват (игра созда��а GrabParts) → новую цель не ищем
 if workspace:FindFirstChild("GrabParts") then
 return
 end
 local cam = workspace.CurrentCamera
 if not cam then return end
 local origin, dir = cam.CFrame.Position, cam.CFrame.LookVector
 tbRayParams.FilterDescendantsInstances = { c, workspace.Terrain }
 local result = workspace:Raycast(origin, dir * 1000, tbRayParams)
 if not result then
 -- лёгкий вертикальный веер, чтобы не мазать впритык
 local dirs = {
 dir,
 (dir + Vector3.new(0, 0.075, 0)).Unit,
 (dir - Vector3.new(0, 0.075, 0)).Unit,
 }
 for _, d in ipairs(dirs) do
 result = workspace:Raycast(origin, d * 1000, tbRayParams)
 if result then break end
 end
 end
 if not result then return end
 local model = result.Instance:FindFirstAncestorOfClass("Model")
 if not model or model == c then return end
 local hum = model:FindFirstChildOfClass("Humanoid")
 if not hum or hum.Health <= 0 then return end
 local root = model:FindFirstChild("HumanoidRootPart")
 if not root then return end
 local dist = (c.HumanoidRootPart.Position - root.Position).Magnitude
 local maxDist = Options.TriggerBotDistance and Options.TriggerBotDistance.Value or 20
 if dist > maxDist then return end
 return model
end

function Triggerbot:OnHeartbeat()
 if not self.Enabled or not self.canGrab then return end
 
 local mode = Options.TriggerBotMode and Options.TriggerBotMode.Value or "Всегда"
 local active = false
 if mode == "Всегда" then
 active = true
 elseif mode == "Зажатие бинда" then
 active = tbHeld
 elseif mode == "Переключатель" then
 active = tbToggled
 end
 if not active then return end

 if UserInputService:GetFocusedTextBox() then return end
 if tick() - self.lastCheck < self.checkThrottle then return end
 self.lastCheck = tick()
 local t = self:GetTarget()
 if t then
 self.lastTarget = t
 self.lastHitTime = tick()
 elseif self.lastTarget and tick() - self.lastHitTime > self.targetMemoryDuration then
 self.lastTarget = nil
 end
 local c = LocalPlayer.Character
 local root = self.lastTarget and self.lastTarget:FindFirstChild("HumanoidRootPart")
 if not (self.lastTarget and c and c:FindFirstChild("HumanoidRootPart") and root) then
 return
 end
 
 local maxDist = Options.TriggerBotDistance and Options.TriggerBotDistance.Value or 20
 if (c.HumanoidRootPart.Position - root.Position).Magnitude > maxDist then
 self.lastTarget = nil
 return
 end
 -- Есть валидная цель → захват и ждём окончания захвата
 self.canGrab = false
 task.spawn(function()
 task.wait(self.preGrabDelay)
 -- Используем VirtualInputManager (надёжно), как в Lock Tap, вместо забагованного mouse1press
 VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 1)
 task.wait(0.01)
 VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 1)
 local t0 = tick()
 repeat
 task.wait(0.02)
 until not workspace:FindFirstChild("GrabParts") or tick() - t0 > 1.6
 task.wait(self.postGrabDelay)
 self.canGrab = true
 self.lastTarget = nil
 end)
end

Toggles.EnableTriggerBot:OnChanged(function()
 Triggerbot.Enabled = Toggles.EnableTriggerBot.Value
 if Triggerbot.Enabled and not Triggerbot.Connection then
 Library:Notify("TriggerBot ВКЛЮЧЁН!", 3)
 Triggerbot.Connection = RunService.Heartbeat:Connect(function()
 Triggerbot:OnHeartbeat()
 end)
 elseif not Triggerbot.Enabled and Triggerbot.Connection then
 Triggerbot.Connection:Disconnect()
 Triggerbot.Connection = nil
 end
end)

-- ==============================================
-- Вкладка: MISC — AIM ASSIST
-- ==============================================
-- Плавное ��вто-доведение камеры на ближайшего к прицелу игрока.
-- В отличие от Lock Tap (мгновенный рывок) — тянет камеру ПЛАВНО.
-- Режимы срабатывания: Всегда / Зажатие бинда / Переключатель по биндуе.
local AimAssistSection = Tabs.Misc:AddLeftGroupbox("Aim Assist")

AimAssistSection:AddToggle("EnableAimAssist", {
 Text = "Aim Assist",
 Default = false,
 Tooltip = "Плавно доводит камеру на ближайшего к прицелу врага",
}):AddKeyPicker("AimAssistKeybind", {
 Default = "None",
 SyncToggleState = false,
 Mode = "Hold",
 Text = "Aim Assist Key",
 NoUI = false,
})

AimAssistSection:AddDropdown("AimAssistMode", {
 Text = "Срабатывание",
 Default = "Зажатие бинда",
 Values = { "Всегда", "Зажатие бинд��", "Переключатель" },
 Multi = false,
 Tooltip = "Всегда — работает постоянно; Зажатие — пока держишь бинд; Переключатель — вкл/выкл по нажатию бинда",
})

AimAssistSection:AddDropdown("AimAssistPart", {
 Text = "Куда наводить",
 Default = "Голова",
 Values = { "Голова", "Торс", "Центр" },
 Multi = false,
 Tooltip = "Часть тела, в которую целится помощник",
})

AimAssistSection:AddSlider("AimAssistSmooth", {
 Text = "Плавность",
 Default = 10,
 Min = 1,
 Max = 30,
 Rounding = 0,
 Compact = false,
 Tooltip = "Больше — пла��нее и медленнее; 1 — почти мгновенно",
})

AimAssistSection:AddSlider("AimAssistFOV", {
 Text = "FOV (радиус)",
 Default = 120,
 Min = 20,
 Max = 600,
 Rounding = 0,
 Compact = false,
 Tooltip = "Берёт цель только в этом радиусе от центра экрана (в пикселях)",
})

AimAssistSection:AddSlider("AimAssistDistance", {
 Text = "Дальность",
 Default = 150,
 Min = 10,
 Max = 1000,
 Rounding = 0,
 Compact = false,
 Tooltip = "Максимальная 3D-дистанция до цели, дальше которой помощник не срабатывает",
})

-- Отслеживание бинда для режимов «зажатие» и «переключатель»
aimAssistHeld = false
aimAssistToggled = false

local function aimAssistKeyMatches(input, boundKey)
 if boundKey == nil then return false end
 if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then
 return true
 end
 if (boundKey == "MB2" or boundKey == "MouseButton2" or boundKey == "RightClick")
 and input.UserInputType == Enum.UserInputType.MouseButton2 then
 return true
 end
 if (boundKey == "MB1" or boundKey == "MouseButton1" or boundKey == "LeftClick")
 and input.UserInputType == Enum.UserInputType.MouseButton1 then
 return true
 end
 return false
end

UserInputService.InputBegan:Connect(function(input, gpe)
 if gpe then return end
 if not (Toggles.EnableAimAssist and Toggles.EnableAimAssist.Value) then return end
 local boundKey = Options.AimAssistKeybind and Options.AimAssistKeybind.Value
 if aimAssistKeyMatches(input, boundKey) then
 aimAssistHeld = true
 aimAssistToggled = not aimAssistToggled
 if Options.AimAssistMode and Options.AimAssistMode.Value == "Переключатель" then
 Library:Notify("Aim Assist: " .. (aimAssistToggled and "ВКЛ" or "��ЫКЛ"), 1)
 end
 end
end)

UserInputService.InputEnded:Connect(function(input)
 local boundKey = Options.AimAssistKeybind and Options.AimAssistKeybind.Value
 if aimAssistKeyMatches(input, boundKey) then
 aimAssistHeld = false
 end
end)

Toggles.EnableAimAssist:OnChanged(function()
 if not Toggles.EnableAimAssist.Value then
 aimAssistToggled = false
 aimAssistHeld = false
 end
end)

-- Карта «часть тела» → имя инстанса
local function getAimAssistPartName()
 local v = Options.AimAssistPart and Options.AimAssistPart.Value or "Голова"
 if v == "Торс" then return "Torso" end
 if v == "Центр" then return "HumanoidRootPart" end
 return "Head"
end

-- Ближайшая к прицелу цель в пределах FOV (возвращаем нужную часть тела)
local function getAimAssistTarget()
 local cam = workspace.CurrentCamera
 if not cam then return nil end
 local center = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
 local fov = Options.AimAssistFOV and Options.AimAssistFOV.Value or 120
 local maxDist = Options.AimAssistDistance and Options.AimAssistDistance.Value or 150
 local lpChar = LocalPlayer.Character
 local lpRoot = lpChar and lpChar:FindFirstChild("HumanoidRootPart")
 local partName = getAimAssistPartName()
 local best, bestDist = nil, fov
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character then
 local part = plr.Character:FindFirstChild(partName)
 or plr.Character:FindFirstChild("HumanoidRootPart")
 or plr.Character:FindFirstChild("Torso")
 or plr.Character:FindFirstChild("Head")
 local hum = plr.Character:FindFirstChildOfClass("Humanoid")
 if part and hum and hum.Health > 0 then
 -- Проверка 3D-дистанции: цель дальше лимита пропускаем
 local within = true
 if lpRoot then
 within = (part.Position - lpRoot.Position).Magnitude <= maxDist
 end
 if within then
 local sp, onScreen = cam:WorldToViewportPoint(part.Position)
 if onScreen then
 local d = (Vector2.new(sp.X, sp.Y) - center).Magnitude
 if d < bestDist then
 bestDist = d
 best = part
 end
 end
 end
 end
 end
 end
 return best
end

-- Главный цикл: плавно тянем камеру к цели
RunService.RenderStepped:Connect(function()
 if not (Toggles.EnableAimAssist and Toggles.EnableAimAssist.Value) then return end
 -- Определяем, активен ли помощник по выбранному режиму
 local mode = Options.AimAssistMode and Options.AimAssistMode.Value or "Зажатие бинда"
 local active = false
 if mode == "Всегда" then
 active = true
 elseif mode == "Зажатие бинда" then
 active = aimAssistHeld
 elseif mode == "Переключатель" then
 active = aimAssistToggled
 end
 if not active then return end
 local target = getAimAssistTarget()
 if not target then return end
 local cam = workspace.CurrentCamera
 if not cam then return end
 local smooth = Options.AimAssistSmooth and Options.AimAssistSmooth.Value or 10
 if smooth < 1 then smooth = 1 end
 local goal = CFrame.new(cam.CFrame.Position, target.Position)
 -- Чем больше «Плавность», тем меньше шаг за кадр → мягче ��оводка
 local alpha = math.clamp(1 / smooth, 0.02, 1)
 cam.CFrame = cam.CFrame:Lerp(goal, alpha)
end)

-- ==============================================
-- Вкладка: MISC — SPAWN PALLET
-- ==============================================
-- По бинду спавнит палетку (PalletLightBrown) рядом с игроком
-- через MenuToys.SpawnToyRemoteFunction. Работает только когда тумблер включён.
local SpawnPalletSection = Tabs.Misc:AddRightGroupbox("Toy Spawner")

SpawnPalletSection:AddToggle("EnableSpawnPallet", {
 Text = "Спавн палетки (бинд)",
 Default = false,
 Tooltip = "Когда включено — по бинду спавнит палетку перед собой",
}):AddKeyPicker("SpawnPalletKeybind", {
 Default = "None",
 SyncToggleState = false,
 Mode = "Hold",
 Text = "Spawn Pallet Key",
 NoUI = false,
})

local function spawnPalletKeyMatches(input, boundKey)
 if boundKey == nil then return false end
 if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then
 return true
 end
 if (boundKey == "MB2" or boundKey == "MouseButton2" or boundKey == "RightClick")
 and input.UserInputType == Enum.UserInputType.MouseButton2 then
 return true
 end
 if (boundKey == "MB1" or boundKey == "MouseButton1" or boundKey == "LeftClick")
 and input.UserInputType == Enum.UserInputType.MouseButton1 then
 return true
 end
 return false
end

lastPalletSpawn = 0
local function doSpawnPallet()
 if tick() - lastPalletSpawn < 1 then return end
 lastPalletSpawn = tick()

 local char = LocalPlayer.Character
 if not char then return end
 local camPart = char:FindFirstChild("CamPart")
 if not camPart then return end
 local RS = game:GetService("ReplicatedStorage")
 local menuToys = RS:FindFirstChild("MenuToys")
 if not menuToys then return end
 local spawnRemote = menuToys:FindFirstChild("SpawnToyRemoteFunction")
 if not spawnRemote then return end
 local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy")
 if canSpawn and not canSpawn.Value then
  local t0 = tick()
  while canSpawn and not canSpawn.Value do
   if tick() - t0 > 3 then return end
   task.wait(0.1)
  end
 end
 -- Спавним куда смотрит камера (как Bliz-T)
 task.spawn(function()
  pcall(function()
   spawnRemote:InvokeServer("PalletLightBrown", camPart.CFrame, Vector3.new(0, camPart.Orientation.Y, 0))
  end)
 end)
end

UserInputService.InputBegan:Connect(function(input, gpe)
 if gpe then return end
 if not (Toggles.EnableSpawnPallet and Toggles.EnableSpawnPallet.Value) then return end
 local boundKey = Options.SpawnPalletKeybind and Options.SpawnPalletKeybind.Value
 if spawnPalletKeyMatches(input, boundKey) then
 doSpawnPallet()
 end
end)

-- ==============================================
-- Вкладка: MISC — GAMEPASS (Further Reach + Auto Escape)
-- ==============================================

-- ==============================================
do
-- Вкладка: MISC — ROCKET SPAWNER
-- ==============================================
local RocketSpawnerSection = Tabs.Misc:AddRightGroupbox("Rocket Spawner")

RocketSpawnerSection:AddToggle("EnableRocketSpawner", {
 Text = "Включить Rocket Spawner",
 Default = false,
 Tooltip = "Разрешает спавнить ракеты по бинду",
}):AddKeyPicker("RocketSpawnKeybind", {
 Default = "None",
 SyncToggleState = false,
 Mode = "Hold",
 Text = "Кнопка спавна ракеты",
 NoUI = false,
})

RocketSpawnerSection:AddDropdown("RocketSpawnMode", {
 Text = "Режим спавна",
 Default = "Просто спавнить",
 Values = {"Просто спавнить", "Спавн + Выстрел"},
 Tooltip = "Выстрел запустит ракету туда, куда ты смотришь (через прицел)",
})



RocketSpawnerSection:AddSlider("RocketShootSpeed", {
 Text = "Скорость выстрела",
 Default = 200,
 Min = 50,
 Max = 1000,
 Rounding = 0,
})

do
 local function getShootCFrame()
  local cam = workspace.CurrentCamera
  local mousePos = UserInputService:GetMouseLocation()
  local ray = cam:ViewportPointToRay(mousePos.X, mousePos.Y)
  return CFrame.lookAt(cam.CFrame.Position, cam.CFrame.Position + ray.Direction * 1000)
 end

 UserInputService.InputBegan:Connect(function(input, gpe)
  if gpe then return end
  if not (Toggles.EnableRocketSpawner and Toggles.EnableRocketSpawner.Value) then return end
  local boundKey = Options.RocketSpawnKeybind and Options.RocketSpawnKeybind.Value
  
  local isMatch = false
  if typeof(boundKey) == "EnumItem" then
   if input.KeyCode == boundKey or input.UserInputType == boundKey then isMatch = true end
  elseif type(boundKey) == "string" then
   if input.KeyCode.Name == boundKey or input.UserInputType.Name == boundKey then isMatch = true end
  end
  
  if isMatch then
   local char = LocalPlayer.Character
   local hrp = char and char:FindFirstChild("HumanoidRootPart")
   if not hrp then return end

   local mode = Options.RocketSpawnMode.Value
   local spawnName = "BombMissile"

   local RS = game:GetService("ReplicatedStorage")
   local spawnRemote = RS:FindFirstChild("MenuToys") and RS.MenuToys:FindFirstChild("SpawnToyRemoteFunction")
   local canSpawn = LocalPlayer:FindFirstChild("CanSpawnToy")

   if not spawnRemote then return end
   
   -- Убираем жесткую блокировку по canSpawn, просто ждём если кулдаун (чтобы бинд не ломался)
   if canSpawn and canSpawn.Value == false then 
    Library:Notify("Подожди перезарядки спавна игрушек!", 2)
    return 
   end

   local cam = workspace.CurrentCamera
   local mousePos = UserInputService:GetMouseLocation()
   local ray = cam:ViewportPointToRay(mousePos.X, mousePos.Y)
   local rayParams = RaycastParams.new()
   rayParams.FilterType = Enum.RaycastFilterType.Exclude
   rayParams.FilterDescendantsInstances = {LocalPlayer.Character}
   local result = workspace:Raycast(ray.Origin, ray.Direction * 500, rayParams)
   
   local hitPos = result and result.Position or (cam.CFrame.Position + ray.Direction * 100)
   local lookCF = CFrame.lookAt(cam.CFrame.Position, hitPos)

   if mode == "Просто спавнить" then
    task.spawn(function()
     -- Спавним прямо перед камерой, точно как в Спавн + Выстрел
     local shootPos = cam.CFrame.Position + lookCF.LookVector * 5
     local shootCF = CFrame.new(shootPos, shootPos + lookCF.LookVector)
     pcall(function() spawnRemote:InvokeServer(spawnName, shootCF, Vector3.zero) end)
    end)
   elseif mode == "Спавн + Выстрел" then
    task.spawn(function()
     local folder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
     if not folder then return end
     
     local rocket = nil
     local conn = folder.ChildAdded:Connect(function(child)
      if child.Name == spawnName then
       rocket = child
      end
     end)
     
     -- Спавним чуть выше и сбоку, чтобы не провалилась
     local spawnCF = hrp.CFrame * CFrame.new(0, 14, 20)
     pcall(function() spawnRemote:InvokeServer(spawnName, spawnCF, Vector3.zero) end)
     
     local t0 = tick()
     while not rocket and tick() - t0 < 3 do task.wait() end
     conn:Disconnect()
     
     if not rocket then return end
     
     local primary = rocket.PrimaryPart or rocket:FindFirstChildWhichIsA("BasePart")
     if not primary then
      while not primary and tick() - t0 < 4 do
       task.wait()
       primary = rocket.PrimaryPart or rocket:FindFirstChildWhichIsA("BasePart")
      end
     end
     if not primary then return end
     
     local partToGrab = nil
     for _, v in ipairs(rocket:GetChildren()) do
      pcall(function()
       if v:IsA("BasePart") and v.CanQuery and v.CanTouch then
        partToGrab = v
       end
      end)
      if partToGrab then break end
     end
     if not partToGrab then partToGrab = primary end
     
     local setOwner = RS:FindFirstChild("GrabEvents") and RS.GrabEvents:FindFirstChild("SetNetworkOwner")
     if setOwner then pcall(function() setOwner:FireServer(partToGrab, partToGrab.CFrame) end) end
     
     -- КРИТИЧЕСКИЙ МОМЕНТ ИЗ NO NAME: ждём 0.2 секунды, чтобы сервер успел передать права
     task.wait(0.2)
     
     local camCF = workspace.CurrentCamera.CFrame
     local shootPos = camCF.Position + lookCF.LookVector * 8
     primary.CFrame = CFrame.new(shootPos, shootPos + lookCF.LookVector)
     
     local speed = Options.RocketShootSpeed and Options.RocketShootSpeed.Value or 200
     primary.AssemblyLinearVelocity = lookCF.LookVector * speed
     
     local bv = Instance.new("BodyVelocity")
     bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
     bv.Velocity = lookCF.LookVector * speed
     bv.Parent = primary
     
     local bg = Instance.new("BodyGyro")
     bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
     bg.CFrame = CFrame.new(shootPos, shootPos + lookCF.LookVector)
     bg.Parent = primary
     
     game:GetService("Debris"):AddItem(rocket, 5)
    end)
   end
  end
 end)
end


end
local GamepassSection = Tabs.Misc:AddLeftGroupbox("Gamepass")

-- ==============================================
-- MISC — LIMB REMOVAL (механика из unstable.txt 2632-2755)
-- 4 бинда (без тумблера) — нажми бинд и limb цели удалится.
-- Требует: цель захвачена (есть workspace.GrabParts) и рагдоллнута.
-- Удаляет через FallenPartsDestroyHeight=-100 + телепорт limb на (0,-1000,0)
-- ==============================================
local LimbRemovalSection = Tabs.Misc:AddRightGroupbox("Limb Removal (binds)")

LimbRemovalSection:AddLabel("Требует: grab + ragdoll цели", false)

LimbRemovalSection:AddLabel("Remove Left Leg", false):AddKeyPicker("RemoveLeftLeg", {
    Default = "None",
    SyncToggleState = false,
    Mode = "Press",
    Text = "Remove Left Leg",
    NoUI = false,
    Callback = function()
        if not (workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")) then
            Library:Notify("Limb Removal: сначала захвати цель", 2)
            return
        end
        local target = workspace.GrabParts.GrabPart:FindFirstChild("WeldConstraint")
        target = target and target.Part1 and target.Part1.Parent
        if not target then return end
        local leg = target:FindFirstChild("Left Leg")
        local hum = target:FindFirstChildOfClass("Humanoid")
        if not (leg and hum and hum:FindFirstChild("Ragdolled")) then
            Library:Notify("Limb Removal: нет Left Leg или Humanoid", 2)
            return
        end
        if not hum.Ragdolled.Value then
            Library:Notify("Limb Removal: цель не рагдоллнута", 2)
            return
        end
        local pos = target:FindFirstChild("Torso") and target.Torso.CFrame
        if not pos then return end
        workspace.FallenPartsDestroyHeight = -100
        leg.CFrame = CFrame.new(0, -1000, 0)
        task.wait(0.1)
        target.Torso.CFrame = CFrame.new(0, -950, 0)
        task.wait(0)
        target.Torso.CFrame = pos
        Library:Notify("Limb Removal: Left Leg удалена", 1)
    end,
})

LimbRemovalSection:AddLabel("Remove Right Leg", false):AddKeyPicker("RemoveRightLeg", {
    Default = "None",
    SyncToggleState = false,
    Mode = "Press",
    Text = "Remove Right Leg",
    NoUI = false,
    Callback = function()
        if not (workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")) then
            Library:Notify("Limb Removal: сначала захвати цель", 2)
            return
        end
        local target = workspace.GrabParts.GrabPart:FindFirstChild("WeldConstraint")
        target = target and target.Part1 and target.Part1.Parent
        if not target then return end
        local leg = target:FindFirstChild("Right Leg")
        local hum = target:FindFirstChildOfClass("Humanoid")
        if not (leg and hum and hum:FindFirstChild("Ragdolled")) then
            Library:Notify("Limb Removal: нет Right Leg или Humanoid", 2)
            return
        end
        if not hum.Ragdolled.Value then
            Library:Notify("Limb Removal: цель не рагдоллнута", 2)
            return
        end
        local pos = target:FindFirstChild("Torso") and target.Torso.CFrame
        if not pos then return end
        workspace.FallenPartsDestroyHeight = -100
        leg.CFrame = CFrame.new(0, -1000, 0)
        task.wait(0.1)
        target.Torso.CFrame = CFrame.new(0, -950, 0)
        task.wait(0)
        target.Torso.CFrame = pos
        Library:Notify("Limb Removal: Right Leg удалена", 1)
    end,
})

LimbRemovalSection:AddLabel("Remove Left Arm", false):AddKeyPicker("RemoveLeftArm", {
    Default = "None",
    SyncToggleState = false,
    Mode = "Press",
    Text = "Remove Left Arm",
    NoUI = false,
    Callback = function()
        if not (workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")) then
            Library:Notify("Limb Removal: сначала захвати цель", 2)
            return
        end
        local target = workspace.GrabParts.GrabPart:FindFirstChild("WeldConstraint")
        target = target and target.Part1 and target.Part1.Parent
        if not target then return end
        local arm = target:FindFirstChild("Left Arm")
        local hum = target:FindFirstChildOfClass("Humanoid")
        if not (arm and hum and hum:FindFirstChild("Ragdolled")) then
            Library:Notify("Limb Removal: нет Left Arm или Humanoid", 2)
            return
        end
        if not hum.Ragdolled.Value then
            Library:Notify("Limb Removal: цель не рагдоллнута", 2)
            return
        end
        local pos = target:FindFirstChild("Torso") and target.Torso.CFrame
        if not pos then return end
        workspace.FallenPartsDestroyHeight = -100
        arm.CFrame = CFrame.new(0, -1000, 0)
        task.wait(0.1)
        target.Torso.CFrame = CFrame.new(0, -950, 0)
        task.wait(0)
        target.Torso.CFrame = pos
        Library:Notify("Limb Removal: Left Arm удалена", 1)
    end,
})

LimbRemovalSection:AddLabel("Remove Right Arm", false):AddKeyPicker("RemoveRightArm", {
    Default = "None",
    SyncToggleState = false,
    Mode = "Press",
    Text = "Remove Right Arm",
    NoUI = false,
    Callback = function()
        if not (workspace:FindFirstChild("GrabParts") and workspace.GrabParts:FindFirstChild("GrabPart")) then
            Library:Notify("Limb Removal: сначала захвати цель", 2)
            return
        end
        local target = workspace.GrabParts.GrabPart:FindFirstChild("WeldConstraint")
        target = target and target.Part1 and target.Part1.Parent
        if not target then return end
        local arm = target:FindFirstChild("Right Arm")
        local hum = target:FindFirstChildOfClass("Humanoid")
        if not (arm and hum and hum:FindFirstChild("Ragdolled")) then
            Library:Notify("Limb Removal: нет Right Arm или Humanoid", 2)
            return
        end
        if not hum.Ragdolled.Value then
            Library:Notify("Limb Removal: цель не рагдоллнута", 2)
            return
        end
        local pos = target:FindFirstChild("Torso") and target.Torso.CFrame
        if not pos then return end
        workspace.FallenPartsDestroyHeight = -100
        arm.CFrame = CFrame.new(0, -1000, 0)
        task.wait(0.1)
        target.Torso.CFrame = CFrame.new(0, -950, 0)
        task.wait(0)
        target.Torso.CFrame = pos
        Library:Notify("Limb Removal: Right Arm удалена", 1)
    end,
})

GamepassSection:AddToggle("EnableFurtherReach", {
 Text = "Further Reach (Gamepass)",
 Default = false,
 Tooltip = "Геймпасс на дальнюю линию захвата. Увеличивает дальность граба.",
})

GamepassSection:AddToggle("EnableAutoEscape", {
 Text = "Auto Escape (Break Free)",
 Default = false,
 Tooltip = "Автоматически вырывается из чужого захвата. С геймпассом — 3 сек, без — 12 сек.",
})

-- === Further Reach (Gamepass) ===
furtherReachActive = false
furtherReachCharConn = nil

-- Capture references ONCE (like original gamepass.txt)
local gamepassRS = game:GetService("ReplicatedStorage")
local gamepassRF = game:GetService("ReplicatedFirst")
local gamepassEvents = gamepassRS:FindFirstChild("GamepassEvents")
local menuToys = gamepassRS:FindFirstChild("MenuToys")
local gamepassScriptNotify = gamepassEvents and gamepassEvents:FindFirstChild("FurtherReachBoughtNotifier")
local gamepassActivator = menuToys and menuToys:FindFirstChild("LimitedTimeToyEvent")

local function furtherReachEnable()
 local char = LocalPlayer.Character
 if not char then return end

 local existing = LocalPlayer:FindFirstChild("FartherReach")
 if existing then existing:Destroy() end
 local fr = Instance.new("BoolValue")
 fr.Name = "FartherReach"
 fr.Value = true
 fr.Parent = LocalPlayer

 -- Use captured references (like original gamepass.txt)
 if gamepassScriptNotify then
  gamepassScriptNotify.Parent = gamepassRF
 end

 if gamepassActivator then
  gamepassActivator.Parent = gamepassEvents
  gamepassActivator.Name = "FurtherReachBoughtNotifier"
 end

 pcall(function()
  local gs = char:FindFirstChild("GrabbingScript")
  if gs then gs.Enabled = false; gs.Enabled = true end
 end)

 task.delay(0.1, function()
  if gamepassActivator then pcall(function() gamepassActivator:FireServer() end) end
 end)

 furtherReachActive = true
end

local function furtherReachDisable()
 local existing = LocalPlayer:FindFirstChild("FartherReach")
 if existing then existing:Destroy() end

 if gamepassScriptNotify then
  gamepassScriptNotify.Parent = gamepassEvents
 end

 if gamepassActivator then
  gamepassActivator.Name = "LimitedTimeToyEvent"
  gamepassActivator.Parent = menuToys
 end

 local char = LocalPlayer.Character
 if char then
  pcall(function()
   local gs = char:FindFirstChild("GrabbingScript")
   if gs then gs.Enabled = false; gs.Enabled = true end
  end)
 end

 furtherReachActive = false
end

Toggles.EnableFurtherReach:OnChanged(function()
 if Toggles.EnableFurtherReach.Value then
  furtherReachEnable()
  furtherReachCharConn = LocalPlayer.CharacterAdded:Connect(function(newChar)
   newChar:WaitForChild("GrabbingScript", 5)
   task.wait(0.1)
   if Toggles.EnableFurtherReach and Toggles.EnableFurtherReach.Value then
    furtherReachEnable()
   end
  end)
 else
  furtherReachDisable()
  if furtherReachCharConn then
   furtherReachCharConn:Disconnect()
   furtherReachCharConn = nil
  end
 end
end)

-- === Auto Escape (Break Free) ===
autoEscapeRunning = false

local function autoEscapeStart()
 if autoEscapeRunning then return end
 autoEscapeRunning = true

 task.spawn(function()
  local holdTimer = 0
  while Toggles.EnableAutoEscape and Toggles.EnableAutoEscape.Value do
   local char = LocalPlayer.Character
   if char then
    local head = char:FindFirstChild("Head")
    local po = head and head:FindFirstChild("PartOwner")
    local isHeld = LocalPlayer:FindFirstChild("IsHeld")
    local beingHeld = (po ~= nil) or (isHeld and isHeld.Value)

    if beingHeld then
     holdTimer = holdTimer + 0.1
     local escapeTime = (Toggles.EnableFurtherReach and Toggles.EnableFurtherReach.Value) and 3 or 12

     if holdTimer >= escapeTime then
      local RS = game:GetService("ReplicatedStorage")
      local ge = RS:FindFirstChild("GrabEvents")
      if ge then
       local destroyLine = ge:FindFirstChild("DestroyGrabLine")
       if destroyLine then
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hrp then pcall(function() destroyLine:FireServer(hrp) end) end
        if head then pcall(function() destroyLine:FireServer(head) end) end
       end
      end

      local ce = RS:FindFirstChild("CharacterEvents")
      if ce then
       local struggle = ce:FindFirstChild("Struggle")
       if struggle then
        pcall(function() struggle:FireServer(LocalPlayer) end)
       end
      end

      pcall(function()
       local gs = char:FindFirstChild("GrabbingScript")
       if gs then gs.Enabled = false; gs.Enabled = true end
      end)

      if po then
       pcall(function() po:Destroy() end)
      end

      holdTimer = 0
      Library:Notify("Auto Escape: вырвался из захвата!", 2)
     end
    else
     holdTimer = 0
    end
   else
    holdTimer = 0
   end
   task.wait(0.1)
  end
  holdTimer = 0
  autoEscapeRunning = false
 end)
end

Toggles.EnableAutoEscape:OnChanged(function()
 if Toggles.EnableAutoEscape.Value then
  autoEscapeStart()
 end
end)

-- ==============================================
-- Вкладка: MISC — HITBOXES
-- ==============================================
-- Увеличивает область попадания (HumanoidRootPart) у других игроков,
-- чтобы п�� ним было проще попадать/зах��атывать. Вкл/выкл тумблером
-- или биндом. Ползунок задаёт размер области. При выключении
-- размеры возвращаются к оригинальным.
local HitboxSection = Tabs.Misc:AddRightGroupbox("Hitboxes")

-- Состояние объявляем ДО UI, чтобы колбэки видели их как upvalue
hitboxData = {}
hitboxColor = Color3.fromRGB(255, 60, 60)

-- Истинные ОРИГИНАЛЬНЫЕ размеры деталей запоминаем ГЛОБАЛЬНО (getgenv),
-- чтобы они пережили повторный ��апуск скрипта. Иначе при перезапуске, когда
-- хитбоксы уже раздуты, "оригиналом" запоминается уже РАЗДУТЫЙ размер —
-- и после выключения детали остаются большими. Ключ — сама деталь.
local hitboxOriginals
if getgenv then
 hitboxOriginals = getgenv().OblivionHitboxOriginals
 if not hitboxOriginals then
 hitboxOriginals = setmetatable({}, { __mode = "k" })
 getgenv().OblivionHitboxOriginals = hitboxOriginals
 end
else
 hitboxOriginals = setmetatable({}, { __mode = "k" })
end

HitboxSection:AddToggle("EnableHitbox", {
 Text = "Хитбоксы",
 Default = false,
 Tooltip = "Раздувает область попадания у игроков — легче захватывать; сквозь них можно проходить",
}):AddKeyPicker("HitboxKeybind", {
 Default = "None",
 SyncToggleState = true,
 Mode = "Toggle",
 Text = "Hitbox Key",
 NoUI = false,
}):AddColorPicker("HitboxColor", {
 Default = Color3.fromRGB(255, 60, 60),
 Title = "Цвет хитбокса",
 Callback = function(Value)
 hitboxColor = Value
 for _, data in pairs(hitboxData) do
 if data.box then
 data.box.Color3 = Value
 data.box.SurfaceColor3 = Value
 end
 end
 end,
})

HitboxSection:AddToggle("ShowHitbox", {
 Text = "Показывать хитбокс",
 Default = true,
 Tooltip = "Выкл — хитбокс станет невидимым (контур убирается)",
 Callback = function(Value)
 if not Value then
 for _, data in pairs(hitboxData) do
 if data.box then data.box.Adornee = nil end
 end
 end
 end,
})

HitboxSection:AddSlider("HitboxTransparency", {
 Text = "Прозрачность боксов",
 Default = 0.65,
 Min = 0,
 Max = 1,
 Rounding = 2,
 Compact = false,
 Tooltip = "0 — сплошной бокс, 1 — полностью прозрачный",
})

HitboxSection:AddSlider("HitboxSize", {
 Text = "Размер хитбоксов",
 Default = 8,
 Min = 3,
 Max = 30,
 Rounding = 0,
 Compact = false,
 Tooltip = "Размер области попадания игроков (в studs)",
})

-- Хитбоксы: раздуваем ��Е HumanoidRootPart (его нельзя сделать невесомым —
-- у персонажа ломается физика и он зависает), а видимую центральную часть
-- (Torso / UpperTorso) и делаем ЕЁ невесомой. Тогда: по большой РЕАЛЬНОЙ
-- детали легко попасть/захватить; Massless на НЕ-руте безопасен (нет зависаний);
-- масса не растёт → даже огромный хитбокс тащится так же легко, как обычно�� тело.
local function getHitboxPart(char)
 return char:FindFirstChild("Torso")
 or char:FindFirstChild("UpperTorso")
 or char:FindFirstChild("LowerTorso")
end

local function restoreHitbox(plr)
 local data = hitboxData[plr]
 if not data then return end
 if data.part and data.part.Parent then
 pcall(function()
 data.part.Size = data.size
 data.part.Massless = data.massless
 data.part.Transparency = data.transparency
 data.part.LocalTransparencyModifier = 0
 data.part.CanCollide = data.cancollide
 end)
 end
 if data.box then data.box:Destroy() end
 hitboxData[plr] = nil
end

local function restoreAllHitboxes()
 for plr in pairs(hitboxData) do
 restoreHitbox(plr)
 end
 -- Подстраховка: вернуть ВСЕ известные оригиналы (на случай, если деталь
 -- осталась раздутой после перезапусков и уже не числится в hitboxData).
 for part, orig in pairs(hitboxOriginals) do
 if part and part.Parent then
 pcall(function()
 part.Size = orig.size
 part.Massless = orig.massless
 part.Transparency = orig.transparency
 part.LocalTransparencyModifier = 0
 part.CanCollide = orig.cancollide
 end)
 end
 end
end

Toggles.EnableHitbox:OnChanged(function()
 if not Toggles.EnableHitbox.Value then
 restoreAllHitboxes()
 end
end)

Players.PlayerRemoving:Connect(function(plr)
 restoreHitbox(plr)
end)

-- Каждый кадр раздуваем центральную деталь живых игроков и делаем её невесомой
RunService.Heartbeat:Connect(function()
 if not (Toggles.EnableHitbox and Toggles.EnableHitbox.Value) then return end
 local size = (Options.HitboxSize and Options.HitboxSize.Value) or 8
 local sizeVec = Vector3.new(size, size, size)
 local showBox = Toggles.ShowHitbox and Toggles.ShowHitbox.Value
 local boxT = (Options.HitboxTransparency and Options.HitboxTransparency.Value) or 0.65
 for _, plr in ipairs(Players:GetPlayers()) do
 if plr ~= LocalPlayer and plr.Character then
 local part = getHitboxPart(plr.Character)
 local hum = plr.Character:FindFirstChildOfClass("Humanoid")
 if part and hum and hum.Health > 0 then
 local data = hitboxData[plr]
 -- Новый персонаж/деталь (респавн) → запоминаем оригинал заново
 if not data or data.part ~= part then
 if data and data.box then data.box:Destroy() end
 -- Берём ИСТИННЫЙ оригинал из глобальной памяти, если он там есть
 -- (переживает перезапуск скрипта). Иначе — запоминаем текущий.
 local orig = hitboxOriginals[part]
 if not orig then
 orig = { size = part.Size, massless = part.Massless, transparency = part.Transparency, cancollide = part.CanCollide }
 hitboxOriginals[part] = orig
 end
 data = { part = part, size = orig.size, massless = orig.massless, transparency = orig.transparency, cancollide = orig.cancollide }
 local sb = Instance.new("SelectionBox")
 sb.Adornee = part
 sb.LineThickness = 0.2
 sb.SurfaceTransparency = 0.65
 sb.SurfaceColor3 = hitboxColor
 sb.Color3 = hitboxColor
 sb.Parent = workspace
 data.box = sb
 hitboxData[plr] = data
 end
 -- Невесомость (один раз) — таскается легко при ЛЮБОМ размере
 if not part.Massless then part.Massless = true end
 -- БЕЗ коллизии: сквозь хит��ок�� можно пройти вплотную, при броске
 -- нет лишнего веса, и жертва нормально регдолится об пол пр�� ЛЮБОМ
 -- размере (гигантский куб больше не бьётся о землю раньше тела).
 if part.CanCollide then part.CanCollide = false end
 -- Прячем раздутый торс ТОЛЬКО у СЕБЯ на экране через
 -- LocalTransparencyModifier (это свойство НЕ реплицируется).
 -- Раньше тут стоял part.Transparency = 1 — а Transparency
 -- РЕПЛИЦИРУЕТСЯ: как только при захвате к тебе переходит сетевое
 -- владение телом жертвы, её торс становился НЕВИДИМЫМ для ВСЕХ,
 -- и ��кружающие в��дели, будто ты хватаешь пустоту, а не игрока.
 -- Теперь тело жертвы для остальных всегда остаётся видимым.
 if part.Transparency ~= data.transparency then part.Transparency = data.transparency end
 part.LocalTransparencyModifier = 1
 -- Размер под ползунок (растёт от центра детали)
 if part.Size ~= sizeVec then part.Size = sizeVec end
 -- Контур: показываем/прячем и красим в выбранный цвет
 if data.box then
 if showBox then
 if data.box.Adornee ~= part then data.box.Adornee = part end
 if not data.box.Parent then data.box.Parent = workspace end
 data.box.Color3 = hitboxColor
 data.box.SurfaceColor3 = hitboxColor
 data.box.SurfaceTransparency = boxT
 elseif data.box.Adornee ~= nil then
 data.box.Adornee = nil
 end
 end
 end
 end
 end
end)
-- ==============================================
-- Вкладка: FUN — PLAYER + NPC CONTROL (из 9rr.txt 12565-12911)
-- Позволяет управлять NPC/игроком на которого ты смотришь.
-- Тумблер + бинд: наводишь камеру на NPC/игрок → жмёшь бинд → камера
-- переключается на него, WASD двигает его, Jump прыгает.
-- ==============================================
do
    local CurrentPlayer = LocalPlayer
    local game_Workspace = workspace
    local game_Players = Players
    local game_UserInputService = UserInputService
    local game_RunService = RunService
    local game_Lighting = game:GetService("Lighting")
    local game_TweenService = game:GetService("TweenService")

    local GrabEventsFolder = ReplicatedStorage:WaitForChild("GrabEvents")
    local SetOwnershipEvent = GrabEventsFolder:WaitForChild("SetNetworkOwner")
    local DestroyLineEvent = GrabEventsFolder:WaitForChild("DestroyGrabLine")
    local CreateLineEvent = GrabEventsFolder:FindFirstChild("CreateGrabLine", 3)

    _G.ControllingCreature = nil
    local ConnectionNoclip = nil
    local NoclipEnabled = false
    local RaycastParameters = RaycastParams.new()
    RaycastParameters.FilterDescendantsInstances = { CurrentPlayer.Character }
    RaycastParameters.FilterType = Enum.RaycastFilterType.Exclude

    -- Helpers
    local function CreateLookAtCFrame(startPosition, endPosition)
        local DirectionVector = (endPosition - startPosition).Unit
        local RightAxis = DirectionVector:Cross(Vector3.new(0, 1, 0))
        local UpAxis = RightAxis:Cross(DirectionVector)
        return CFrame.fromMatrix(startPosition, RightAxis, UpAxis)
    end

    local function GetCurrentCharacter()
        if CurrentPlayer.Character and CurrentPlayer.Character:FindFirstChild("HumanoidRootPart") and CurrentPlayer.Character:FindFirstChildOfClass("Humanoid") then
            return CurrentPlayer.Character
        end
    end

    local function ValidatePartOwnership(partInstance, shouldReturnOwner)
        if typeof(partInstance) == "Instance" and partInstance:FindFirstChild("PartOwner") and partInstance.PartOwner.Value == CurrentPlayer.Name then
            return not shouldReturnOwner and true or partInstance.PartOwner
        end
    end

    local function RequestOwnershipSingle(partInstance)
        local DistanceFromPlayer = CurrentPlayer:DistanceFromCharacter(partInstance.Position)
        if CurrentPlayer.Character and CurrentPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if ValidatePartOwnership(partInstance) then return true end
            if DistanceFromPlayer <= 30 then
                pcall(function()
                    SetOwnershipEvent:FireServer(partInstance, CreateLookAtCFrame(CurrentPlayer.Character.HumanoidRootPart.Position, partInstance.Position))
                end)
            end
        end
    end

    local function RequestOwnershipAndRemoveLine(partInstance)
        local DistanceCalculated = CurrentPlayer:DistanceFromCharacter(partInstance.Position)
        local IsConnected = partInstance:GetAttribute("Connected")
        local HasCreatedConnection = partInstance:GetAttribute("CreatedConnected")
        if CurrentPlayer.Character and CurrentPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if ValidatePartOwnership(partInstance) then
                partInstance:SetAttribute("Connected", true)
                pcall(function() DestroyLineEvent:FireServer(partInstance) end)
                if not HasCreatedConnection then
                    partInstance:SetAttribute("CreatedConnected", true)
                    partInstance.ChildAdded:Connect(function(childAdded)
                        if childAdded.Name == "PartOwner" and childAdded.Value ~= CurrentPlayer.Name then
                            partInstance:SetAttribute("Connected", false)
                        end
                    end)
                end
            elseif DistanceCalculated <= 30 and not IsConnected then
                pcall(function()
                    SetOwnershipEvent:FireServer(partInstance, CreateLookAtCFrame(CurrentPlayer.Character.HumanoidRootPart.Position, partInstance.Position))
                end)
            end
        end
    end

    local function ActivateNoclipMode()
        if not ConnectionNoclip then
            NoclipEnabled = false
            local function NoclipLoopFunction()
                if NoclipEnabled == false and game_Players.LocalPlayer.Character ~= nil then
                    for _, child in ipairs(game_Players.LocalPlayer.Character:GetChildren()) do
                        if child:IsA("BasePart") and child.CanCollide then
                            child.CanCollide = false
                        end
                    end
                end
                task.wait(0.21)
            end
            ConnectionNoclip = game_RunService.Stepped:Connect(NoclipLoopFunction)
        end
    end

    local function DeactivateNoclipMode()
        if not _G.NoclipToggle then
            if ConnectionNoclip then
                ConnectionNoclip:Disconnect()
                ConnectionNoclip = nil
            end
            NoclipEnabled = true
        end
    end

    local function DisableCharacterQuery(characterModel)
        for _, part in ipairs(characterModel:GetChildren()) do
            if part:IsA("Part") then part.CanQuery = false end
        end
    end

    local function EnableCharacterQuery(characterModel)
        for _, part in ipairs(characterModel:GetChildren()) do
            if part:IsA("Part") then part.CanQuery = true end
        end
    end

    -- Visuals: OBLIVION CONTROL MODE EFFECT
    -- Уникальный эффект перехода в контроль (НЕ как 9rr):
    -- 1) Резкий zoom-in (FOV 50 → плавно к 70 за 0.4с с отскоком)
    -- 2) Радужный ColorCorrection с Tint сменой (фиолетовый → циан)
    -- 3) Bloom с пульсацией
    -- 4) Кольцо частиц вокруг цели (Highlight + Beam)
    -- 5) Звук перехода (другой — не из 9rr)
    -- 6) При выходе: обратный эффект (zoom-out + фейд)
    local OblivionControl = {}
    OblivionControl.ColorCorrection = Instance.new("ColorCorrectionEffect")
    OblivionControl.ColorCorrection.Name = "OblivionControlCC"
    OblivionControl.ColorCorrection.Parent = game_Lighting
    OblivionControl.ColorCorrection.Enabled = false
    OblivionControl.ColorCorrection.TintColor = Color3.fromRGB(255, 255, 255)
    OblivionControl.ColorCorrection.Brightness = 0
    OblivionControl.ColorCorrection.Contrast = 0
    OblivionControl.ColorCorrection.Saturation = 0

    OblivionControl.Bloom = Instance.new("BloomEffect")
    OblivionControl.Bloom.Name = "OblivionControlBloom"
    OblivionControl.Bloom.Parent = game_Lighting
    OblivionControl.Bloom.Enabled = false
    OblivionControl.Bloom.Intensity = 0
    OblivionControl.Bloom.Size = 0
    OblivionControl.Bloom.Threshold = 0.95

    OblivionControl.SunRays = Instance.new("SunRaysEffect")
    OblivionControl.SunRays.Name = "OblivionControlSunRays"
    OblivionControl.SunRays.Parent = game_Lighting
    OblivionControl.SunRays.Enabled = false
    OblivionControl.SunRays.Intensity = 0
    OblivionControl.SunRays.Spread = 0

    -- Звук перехода (используем другой — звучит как "warp"/"teleport")
    OblivionControl.WarpSound = Instance.new("Sound")
    OblivionControl.WarpSound.SoundId = "rbxassetid://1846275076"  -- arcane whoosh
    OblivionControl.WarpSound.Volume = 0.6
    OblivionControl.WarpSound.PlaybackSpeed = 0.85
    OblivionControl.WarpSound.Parent = game_Workspace

    OblivionControl.ExitSound = Instance.new("Sound")
    OblivionControl.ExitSound.SoundId = "rbxassetid://3068220893"  -- soft chime
    OblivionControl.ExitSound.Volume = 0.4
    OblivionControl.ExitSound.PlaybackSpeed = 1.1
    OblivionControl.ExitSound.Parent = game_Workspace

    -- Состояние эффекта (для анимации)
    OblivionControl.Active = false
    OblivionControl.TweenConn = nil
    OblivionControl.Highlight = nil
    OblivionControl.SelectionBox = nil

    local function startControlVisuals(targetModel)
        OblivionControl.Active = true

        -- 1) Звук warp
        pcall(function() OblivionControl.WarpSound:Play() end)

        -- 2) FOV zoom-in: с 70 (текущий) до 45 резко, потом плавно к 65 за 0.5с
        local cam = game_Workspace.CurrentCamera
        if cam then
            pcall(function()
                -- Резкий zoom
                cam.FieldOfView = 45
                local tween = game_TweenService:Create(
                    cam,
                    TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out, 0, true),
                    {FieldOfView = 65}
                )
                tween:Play()
            end)
        end

        -- 3) ColorCorrection: фиолетовый tint + насыщенность + контраст
        OblivionControl.ColorCorrection.Enabled = true
        pcall(function()
            -- Резко фиолетовый
            OblivionControl.ColorCorrection.TintColor = Color3.fromRGB(180, 100, 255)
            OblivionControl.ColorCorrection.Saturation = 0.8
            OblivionControl.ColorCorrection.Contrast = 0.3
            OblivionControl.ColorCorrection.Brightness = -0.05

            -- Плавно к циану за 0.6с
            local ccTween = game_TweenService:Create(
                OblivionControl.ColorCorrection,
                TweenInfo.new(0.6, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                {
                    TintColor = Color3.fromRGB(100, 220, 255),
                    Saturation = 0.3,
                    Contrast = 0.15,
                    Brightness = 0,
                }
            )
            ccTween:Play()
        end)

        -- 4) Bloom: вспышка + плавное затухание
        OblivionControl.Bloom.Enabled = true
        OblivionControl.Bloom.Intensity = 2.5
        OblivionControl.Bloom.Size = 35
        OblivionControl.Bloom.Threshold = 0.3
        pcall(function()
            local bloomTween = game_TweenService:Create(
                OblivionControl.Bloom,
                TweenInfo.new(0.7, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                { Intensity = 0.6, Size = 24, Threshold = 0.8 }
            )
            bloomTween:Play()
        end)

        -- 5) SunRays лёгкие
        OblivionControl.SunRays.Enabled = true
        OblivionControl.SunRays.Intensity = 0.3
        OblivionControl.SunRays.Spread = 0.5

        -- 6) Highlight на цели (зелёное свечение)
        if targetModel then
            pcall(function()
                if OblivionControl.Highlight then OblivionControl.Highlight:Destroy() end
                OblivionControl.Highlight = Instance.new("Highlight")
                OblivionControl.Highlight.Name = "OblivionControlHighlight"
                OblivionControl.Highlight.Adornee = targetModel
                OblivionControl.Highlight.FillColor = Color3.fromRGB(100, 255, 200)
                OblivionControl.Highlight.FillTransparency = 0.7
                OblivionControl.Highlight.OutlineColor = Color3.fromRGB(100, 255, 200)
                OblivionControl.Highlight.OutlineTransparency = 0
                OblivionControl.Highlight.Parent = targetModel
            end)
        end

        -- 7) Анимация пульсации Bloom (пока активен контроль)
        OblivionControl.TweenConn = game_RunService.Heartbeat:Connect(function()
            if not OblivionControl.Active then return end
            local t = tick()
            -- Лёгкая пульсация Bloom intensity (0.6 ± 0.2)
            pcall(function()
                OblivionControl.Bloom.Intensity = 0.6 + 0.2 * math.sin(t * 3)
            end)
            -- Лёгкое смещение Tint (cyan ↔ teal)
            pcall(function()
                local hueShift = (math.sin(t * 0.8) + 1) * 0.5  -- 0..1
                OblivionControl.ColorCorrection.TintColor = Color3.fromRGB(
                    80 + math.floor(40 * hueShift),
                    220,
                    255 - math.floor(20 * hueShift)
                )
            end)
        end)
    end

    local function endControlVisuals()
        OblivionControl.Active = false

        -- 1) Звук выхода
        pcall(function() OblivionControl.ExitSound:Play() end)

        -- 2) FOV zoom-out: резко 65 → 80, потом плавно к 70
        local cam = game_Workspace.CurrentCamera
        if cam then
            pcall(function()
                local tween = game_TweenService:Create(
                    cam,
                    TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
                    {FieldOfView = 70}
                )
                tween:Play()
            end)
        end

        -- 3) ColorCorrection: обратно к норме
        pcall(function()
            local ccTween = game_TweenService:Create(
                OblivionControl.ColorCorrection,
                TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
                {
                    TintColor = Color3.fromRGB(255, 255, 255),
                    Saturation = 0,
                    Contrast = 0,
                    Brightness = 0,
                }
            )
            ccTween:Play()
            ccTween.Completed:Once(function()
                OblivionControl.ColorCorrection.Enabled = false
            end)
        end)

        -- 4) Bloom: вспышка при выходе + затухание
        pcall(function()
            OblivionControl.Bloom.Intensity = 3
            OblivionControl.Bloom.Size = 50
            local bloomTween = game_TweenService:Create(
                OblivionControl.Bloom,
                TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.In),
                { Intensity = 0, Size = 0 }
            )
            bloomTween:Play()
            bloomTween.Completed:Once(function()
                OblivionControl.Bloom.Enabled = false
            end)
        end)

        -- 5) SunRays off
        pcall(function()
            local srTween = game_TweenService:Create(
                OblivionControl.SunRays,
                TweenInfo.new(0.4),
                { Intensity = 0, Spread = 0 }
            )
            srTween:Play()
            srTween.Completed:Once(function()
                OblivionControl.SunRays.Enabled = false
            end)
        end)

        -- 6) Highlight убрать
        if OblivionControl.Highlight then
            pcall(function() OblivionControl.Highlight:Destroy() end)
            OblivionControl.Highlight = nil
        end

        -- 7) Остановить пульсацию
        if OblivionControl.TweenConn then
            OblivionControl.TweenConn:Disconnect()
            OblivionControl.TweenConn = nil
        end
    end

    local function MovePlayerToPosition(targetCFrame, offsetVector)
        local PlayerCharacter = GetCurrentCharacter()
        if PlayerCharacter and typeof(targetCFrame) == "CFrame" then
            local RootPartInstance = PlayerCharacter.HumanoidRootPart
            local HumanoidInstance = PlayerCharacter:FindFirstChildOfClass("Humanoid")
            pcall(function()
                RootPartInstance.CFrame = RootPartInstance.CFrame.Rotation + targetCFrame.Position
            end)
            if HumanoidInstance.SeatPart == nil or tostring(HumanoidInstance.SeatPart.Parent) ~= "CreatureBlobman" then
                HumanoidInstance.Sit = false
            end
        end
    end

    function BeginControllingTarget(targetModelInstance)
        if typeof(targetModelInstance) == "Instance" and targetModelInstance:IsA("Model") then
            local TargetModel = targetModelInstance
            local TargetHumanoid = TargetModel:FindFirstChildOfClass("Humanoid")
            local TargetRootPart = TargetModel:FindFirstChild("HumanoidRootPart")
            local TargetHead = TargetModel:FindFirstChild("Head")
            local IsValidTargetType = (function()
                if not game_Players:GetPlayerFromCharacter(targetModelInstance) and (targetModelInstance.Name == "YouDecoy" or targetModelInstance.Name == "CreatureBlobman" or tostring(targetModelInstance.Parent.Name) == "Robloxians") then
                    return true
                end
            end)()

            if TargetModel and TargetHumanoid and TargetRootPart then
                local ConnectionsTable = {}
                local function CleanupAllConnections()
                    for _, conn in pairs(ConnectionsTable) do
                        if typeof(conn) == "RBXScriptConnection" then
                            pcall(function() conn:Disconnect() end)
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

                local VelocityController = Instance.new("BodyVelocity", TargetRootPart)
                local PlayerVelocityController = Instance.new("BodyVelocity")
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
                startControlVisuals(TargetModel)

                local PlayerCharacterCurrent = GetCurrentCharacter()
                local PlayerRootPartReference = nil
                local PlayerHumanoidReference = nil

                if PlayerCharacterCurrent then
                    PlayerHumanoidReference = PlayerCharacterCurrent:FindFirstChildOfClass("Humanoid")
                    PlayerRootPartReference = PlayerCharacterCurrent:FindFirstChild("HumanoidRootPart")
                    PlayerVelocityController.Parent = PlayerRootPartReference

                    ConnectionsTable[2] = PlayerHumanoidReference.Died:Connect(function()
                        _G.ControllingCreature = nil
                    end)
                    ConnectionsTable[3] = game_UserInputService.JumpRequest:Connect(function()
                        TargetHumanoid:ChangeState("Jumping")
                    end)
                    ConnectionsTable[5] = PlayerHumanoidReference.Changed:Connect(function(propertyName)
                        if propertyName == "MoveDirection" then
                            VelocityController.Velocity = PlayerHumanoidReference.MoveDirection * 20
                        end
                    end)
                    ConnectionsTable[6] = workspace.CurrentCamera.Changed:Connect(function(cameraProperty)
                        if cameraProperty == "CameraSubject" then
                            game_Workspace.CurrentCamera.CameraSubject = TargetHumanoid
                        end
                    end)

                    local CameraDirectionVector = nil
                    ConnectionsTable[7] = TargetHead.Changed:Connect(function(headProperty)
                        if headProperty == "CFrame" then
                            CameraDirectionVector = game_Workspace.CurrentCamera.CFrame.lookVector
                            TargetHumanoid.CameraOffset = -Vector3.new(CameraDirectionVector.X, 5, CameraDirectionVector.Z) * 1.7
                        end
                    end)

                    TargetHumanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)

                    -- Главный цикл управления
                    task.spawn(function()
                        while TargetModel.Parent and _G.ControllingCreature ~= nil and PlayerCharacterCurrent and PlayerCharacterCurrent.Parent do
                            MovePlayerToPosition(CFrame.new(TargetRootPart.Position + Vector3.new(0, -10, 0)))
                            task.wait()
                        end
                        -- Cleanup
                        CleanupAllConnections()
                        DeactivateNoclipMode()
                        pcall(function() MovePlayerToPosition(CFrame.new(TargetRootPart.Position + Vector3.new(5, 15, 5))) end)
                        EnableCharacterQuery(TargetModel)
                        pcall(function() VelocityController:Destroy() end)
                        pcall(function() PlayerVelocityController:Destroy() end)
                        pcall(function() game_Workspace.CurrentCamera.CameraSubject = PlayerHumanoidReference end)
                        _G.ControllingCreature = nil
                        pcall(function() PlayerRootPartReference.Velocity = Vector3.new() end)
                        endControlVisuals()
                    end)
                else
                    _G.ControllingCreature = nil
                end
            end
        end
    end

    function ExecuteControlAction()
        local ActiveCharacter = GetCurrentCharacter()
        if ActiveCharacter then
            local CharacterHead = ActiveCharacter:FindFirstChild("Head")
            if not CharacterHead then return end
            local ActiveCamera = game_Workspace.CurrentCamera
            local CharacterHumanoid = ActiveCharacter:FindFirstChildOfClass("Humanoid")
            local RaycastResult = game_Workspace:Raycast(CharacterHead.Position, ActiveCamera.CFrame.lookVector * 50, RaycastParameters)
            if RaycastResult and CharacterHumanoid and CharacterHumanoid.Health > 0 then
                local HitInstanceParent = RaycastResult.Instance.Parent
                if HitInstanceParent and HitInstanceParent:FindFirstChildOfClass("Humanoid") then
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
        RaycastParameters.FilterDescendantsInstances = { newCharacter }
    end)

    -- UI: тогл + бинд во вкладке Fun (точная механика из 9rr.txt 12890-12911)
    local ControlSection = Tabs.Fun:AddRightGroupbox("Player + NPC Control")

    local ControlToggle = ControlSection:AddToggle("ControlCharactersToggle", {
        Text = "Player + NPC Control",
        Default = false,
        Tooltip = "Управляй NPC/игроком на которого смотришь. Наведи камеру → жми бинд.",
    })

    -- КЛЮЧЕВОЕ: KeyPicker через AddKeyPicker С Callback (как в 9rr),
    -- а НЕ через OnChanged — OnChanged срабатывает при изменении настройки,
    -- а нам нужно срабатывание при НАЖАТИИ клавиши
    ControlToggle:AddKeyPicker("ControlKeybind", {
        Default = "V",
        SyncToggleState = false,
        Mode = "Toggle",
        Text = "Control Key",
        NoUI = false,
        Callback = function(isActive)
            -- Бинд срабатывает только если тумблер включён (точная логика 9rr 12900-12902)
            if not ControlToggle.Value then
                return
            end
            -- isActive = true когда бинд нажат (Mode=Toggle: первый клик true, второй false)
            -- В 9rr ExecuteControlToggle сам решает — у него есть _G.ControllingCreature флаг
            ExecuteControlToggle()
        end,
    })

    -- Опционально: уведомление при изменении тумблера
    ControlToggle:OnChanged(function(enabled)
        if enabled then
            Library:Notify("Player + NPC Control ВКЛ — наведи на цель и жми бинд", 2)
        else
            -- Если выключили тумблер во время контроля — сбросить
            if _G.ControllingCreature then
                _G.ControllingCreature = nil
            end
            Library:Notify("Player + NPC Control ВЫКЛ", 1)
        end
    end)
end

-- ==============================================
-- Вкладка: FUN — ROCKET DRONE
-- ==============================================
-- Управляемые ракеты (Missile). Включаешь тумблер → спавнит
-- столько ракет, сколько указано в ��олзунке (1–10). Включаешь тумблер →
-- камера переключается на 3-е лицо, ты летишь за ракетами �� рулишь
-- ими через W/A/S/D, мышкой направляешь взгляд. Выключаешь тумблер →
-- камера возвращается, ракеты улетаются.
-- Фиксы по сравнению со старой версией:
-- 1) Спавн ждёт CanSpawnToy (серверный гейт) — раньше спавнило
-- пачкой без ожидания и сервер отклонял.
-- 2) Бинд через InputBegan/InputEnded (как все рабочие бинды в
-- скрипте) — раньше GetState() в RenderStepped не срабатывал.
-- 3) Камера переводится в Classic (3-е лицо) при активации и
-- возвращается при деактивации.
-- 4) Папка SpawnedInToys ждётся через WaitForChild, а не FindFirstChild.

function initBombDrone()
FunBombDrone = Tabs.Fun:AddLeftGroupbox("Rocket Drone")

droneToggle = FunBombDrone:AddToggle("EnableBombDrone", {
 Text = "Rocket Drone Mode",
 Default = false,
 Tooltip = "WASD — движение, Space — вверх, Shift — вниз. ПКМ — камера.",
})

FunBombDrone:AddSlider("DroneSpeed", {
 Text = "Скорость ракеты",
 Default = 85,
 Min = 20,
 Max = 500,
 Rounding = 0,
})

pcall(function()
 droneToggle:AddKeyPicker("DroneAddKey", {
  Default = "None",
  SyncToggleState = false,
  Mode = "Toggle",
  Text = "Бинд",
 })
end)

FunBombDrone:AddButton({
 Text = "Добавить ракету",
 Tooltip = "Наведи прицел на ракету и нажми F. Можно несколько!",
 Func = function()
 if not (Toggles.EnableBombDrone and Toggles.EnableBombDrone.Value) then
 Library:Notify("Сначала включи Rocket Drone Mode!", 3)
 return
 end
 local targetPart = getTargetUnderCrosshair()
 if targetPart then
 attachInsideRocket(targetPart)
 else
 Library:Notify("❌ Наведи прицел на ракету!", 2)
 end
 end,
})

FunBombDrone:AddButton({
 Text = "Сбросить все ракеты",
 Func = function()
 resetRocketCamera()
 Library:Notify("Rocket Drone: сброшен", 2)
 end,
})

isCamActive = false
trackedParts = {}
renderConn = nil
savedCamSubject = nil
savedCamMode = nil
savedCamMax = nil
savedCamMin = nil

local function disableRocketMovers(model)
 for _, obj in ipairs(model:GetDescendants()) do
 if obj:IsA("BodyVelocity") or obj:IsA("BodyGyro") or obj:IsA("BodyAngularVelocity") or obj:IsA("BodyPosition") or obj:IsA("BodyForce") or obj:IsA("BodyThrust") or obj:IsA("LinearVelocity") or obj:IsA("AngularVelocity") then
 pcall(function() obj:Destroy() end)
 end
 end
end

local function ensureBV(part)
 local bv = part:FindFirstChild("RocketMove")
 if not bv then
 bv = Instance.new("BodyVelocity")
 bv.Name = "RocketMove"
 bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
 bv.Velocity = Vector3.zero
 bv.Parent = part
 end
 return bv
end

local function ensureBG(part)
 local bg = part:FindFirstChild("RocketGyro")
 if not bg then
 bg = Instance.new("BodyGyro")
 bg.Name = "RocketGyro"
 bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
 bg.CFrame = part.CFrame
 bg.Parent = part
 end
 return bg
end

function resetRocketCamera()
 isCamActive = false
 if renderConn then
 renderConn:Disconnect()
 renderConn = nil
 end
 for _, p in ipairs(trackedParts) do
 if p and p.Parent then
 local bv = p:FindFirstChild("RocketMove")
 if bv then pcall(function() bv:Destroy() end) end
 local bg = p:FindFirstChild("RocketGyro")
 if bg then pcall(function() bg:Destroy() end) end
 end
 end
 trackedParts = {}
 local cam = workspace.CurrentCamera
 if cam then
 if savedCamSubject then
 cam.CameraSubject = savedCamSubject
 savedCamSubject = nil
 else
 local char = LocalPlayer.Character
 local hum = char and char:FindFirstChildOfClass("Humanoid")
 if hum then cam.CameraSubject = hum end
 end
 cam.CameraType = Enum.CameraType.Custom
 end
 if savedCamMode then LocalPlayer.CameraMode = savedCamMode; savedCamMode = nil end
 if savedCamMax then LocalPlayer.CameraMaxZoomDistance = savedCamMax; savedCamMax = nil end
 if savedCamMin then LocalPlayer.CameraMinZoomDistance = savedCamMin; savedCamMin = nil end
end

function getTargetUnderCrosshair()
 local char = LocalPlayer.Character
 if not char then return nil end
 local cam = workspace.CurrentCamera
 if not cam then return nil end
 local vc = Vector2.new(cam.ViewportSize.X / 2, cam.ViewportSize.Y / 2)
 local ray = cam:ViewportPointToRay(vc.X, vc.Y)
 local rp = RaycastParams.new()
 rp.FilterType = Enum.RaycastFilterType.Exclude
 rp.FilterDescendantsInstances = {char}
 rp.IgnoreWater = true
 local result = workspace:Raycast(ray.Origin, ray.Direction * 500, rp)
 if result and result.Instance then
 local hitPart = result.Instance
 if not hitPart:IsA("Terrain") and hitPart.Name ~= "Baseplate" then
 return hitPart
 end
 end
 return nil
end

function attachInsideRocket(part)
 if not part or not part:IsA("BasePart") then return end
 for _, p in ipairs(trackedParts) do
 if p == part then
 Library:Notify("Эта ракета уже управляется!", 2)
 return
 end
 end
 table.insert(trackedParts, part)
 if not isCamActive then
 isCamActive = true
 local cam = workspace.CurrentCamera
 if cam then
 savedCamSubject = cam.CameraSubject
 savedCamMode = LocalPlayer.CameraMode
 savedCamMax = LocalPlayer.CameraMaxZoomDistance
 savedCamMin = LocalPlayer.CameraMinZoomDistance
 end
 end
 Library:Notify("������ Ракета #" .. #trackedParts .. " добавлена! Всего: " .. #trackedParts, 2)
 local model = part.Parent
 if model and model:IsA("Model") then
 disableRocketMovers(model)
 end
 ensureBV(part)
 ensureBG(part)
 local cam = workspace.CurrentCamera
 if cam then
 cam.CameraSubject = trackedParts[1]
 cam.CameraType = Enum.CameraType.Custom
 end
 LocalPlayer.CameraMode = Enum.CameraMode.Classic
 LocalPlayer.CameraMaxZoomDistance = 50
 LocalPlayer.CameraMinZoomDistance = 15
 if renderConn then renderConn:Disconnect() end
 renderConn = RunService.RenderStepped:Connect(function(dt)
 if isCamActive and #trackedParts > 0 then
 local cam2 = workspace.CurrentCamera
 if not cam2 then return end
 local speed = (Options.DroneSpeed and Options.DroneSpeed.Value) or 85
 if LocalPlayer.CameraMode ~= Enum.CameraMode.Classic then
 LocalPlayer.CameraMode = Enum.CameraMode.Classic
 end
 if LocalPlayer.CameraMinZoomDistance < 15 then
 LocalPlayer.CameraMinZoomDistance = 15
 end
 if #trackedParts > 0 and trackedParts[1] and trackedParts[1].Parent then
 if cam2.CameraSubject ~= trackedParts[1] then
 cam2.CameraSubject = trackedParts[1]
 end
 end
 cam2.CameraType = Enum.CameraType.Custom
 local moveVector = Vector3.zero
 if UserInputService:IsKeyDown(Enum.KeyCode.W) then
 moveVector = moveVector + cam2.CFrame.LookVector
 end
 if UserInputService:IsKeyDown(Enum.KeyCode.S) then
 moveVector = moveVector - cam2.CFrame.LookVector
 end
 if UserInputService:IsKeyDown(Enum.KeyCode.A) then
 moveVector = moveVector - cam2.CFrame.RightVector
 end
 if UserInputService:IsKeyDown(Enum.KeyCode.D) then
 moveVector = moveVector + cam2.CFrame.RightVector
 end
 if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
 moveVector = moveVector + Vector3.new(0, 1, 0)
 end
 if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) or UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
 moveVector = moveVector - Vector3.new(0, 1, 0)
 end
 local validParts = {}
 for _, p in ipairs(trackedParts) do
 if p and p.Parent then
 pcall(function() p.Anchored = false end)
 local bv = ensureBV(p)
 local bg = ensureBG(p)
 if moveVector.Magnitude > 0 then
 bv.Velocity = moveVector.Unit * speed
 bg.CFrame = CFrame.new(p.Position, p.Position + moveVector.Unit)
 else
 bv.Velocity = Vector3.zero
 end
 table.insert(validParts, p)
 end
 end
 trackedParts = validParts
 else
 resetRocketCamera()
 end
 end)
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
 if gameProcessed then return end
 if not (Toggles.EnableBombDrone and Toggles.EnableBombDrone.Value) then return end
 if Options.DroneAddKey and tpKeyMatches(input, Options.DroneAddKey.Value) then
 local targetPart = getTargetUnderCrosshair()
 if targetPart then
 attachInsideRocket(targetPart)
 else
 Library:Notify("❌ Наведи прицел на ракету!", 2)
 end
 end
end)

Toggles.EnableBombDrone:OnChanged(function()
 if Toggles.EnableBombDrone.Value then
 Library:Notify("Rocket Drone: нажми бинд чтобы добавить ракету. WASD — движение, ПКМ — камера", 4)
 else
 resetRocketCamera()
 Library:Notify("Rocket Drone: сброшен", 2)
 end
end)
end
pcall(initBombDrone)


-- Rocket Drone Mobile Launcher
local FunBombMobile = Tabs.Fun:AddLeftGroupbox("Rocket Drone (Mobile)")
FunBombMobile:AddButton({
 Text = "Запустить мобильный интерфейс",
 Func = function()
  -- Запускаем скрипт из файла
  local gui_code = [======[-- ========================================================================================
-- [ ROCKET STEERING - COMPACT, MINIMIZE BUTTON & SPEED 20-500 ]
-- ========================================================================================

Players = game:GetService("Players")
Workspace = game:GetService("Workspace")
RunService = game:GetService("RunService")
UserInputService = game:GetService("UserInputService")

localPlayer = Players.LocalPlayer or Players.PlayerAdded:Wait()
Camera = Workspace.CurrentCamera

isCamActive = false
currentTrackedPart = nil
renderConn = nil
originalTransparencies = {}

flightSpeed = 85
MIN_SPEED = 20
MAX_SPEED = 500

NOSE_OFFSET = CFrame.Angles(math.rad(-90), 0, 0)

-- === 1. ИНТЕРФЕЙС GUI ===
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RocketCleanGui"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

targetParent = nil
pcall(function() if gethui then targetParent = gethui() end end)
if not targetParent then pcall(function() targetParent = game:GetService("CoreGui") end) end
if not targetParent then targetParent = localPlayer:WaitForChild("PlayerGui") end

for _, old in ipairs(targetParent:GetChildren()) do
    if old.Name == "RocketCleanGui" then old:Destroy() end
end
screenGui.Parent = targetParent

-- Кнопка разворачивания (круглый значок с ракетой)
local openCircleBtn = Instance.new("TextButton")
openCircleBtn.Size = UDim2.new(0, 32, 0, 32)
openCircleBtn.Position = UDim2.new(0.02, 0, 0.32, 0)
openCircleBtn.BackgroundColor3 = Color3.fromRGB(22, 24, 32)
openCircleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openCircleBtn.Font = Enum.Font.GothamBold
openCircleBtn.TextSize = 13
openCircleBtn.Text = "������"
openCircleBtn.Visible = false
openCircleBtn.Active = true
openCircleBtn.Draggable = true
openCircleBtn.Parent = screenGui
Instance.new("UICorner", openCircleBtn).CornerRadius = UDim.new(1, 0)
local circleStroke = Instance.new("UIStroke", openCircleBtn)
circleStroke.Color = Color3.fromRGB(45, 50, 65)
circleStroke.Thickness = 1.5

-- Главная рамка (уменьшенная)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 210, 0, 165)
mainFrame.Position = UDim2.new(0.02, 0, 0.32, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(22, 24, 32)
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

local frameCorner = Instance.new("UICorner", mainFrame)
frameCorner.CornerRadius = UDim.new(0, 8)

local frameStroke = Instance.new("UIStroke", mainFrame)
frameStroke.Color = Color3.fromRGB(45, 50, 65)
frameStroke.Thickness = 1.5

-- Заголовок
local title = Instance.new("TextLabel")
title.Size = UDim2.new(0.75, 0, 0, 24)
title.Position = UDim2.new(0.05, 0, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 215, 0)
title.Font = Enum.Font.GothamBold
title.TextSize = 10
title.Text = "������ УПРАВЛЕНИЕ РАКЕТОЙ"
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = mainFrame

-- Кнопка сворачивания («минус»)
local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 22, 0, 18)
minBtn.Position = UDim2.new(1, -26, 0, 3)
minBtn.BackgroundColor3 = Color3.fromRGB(45, 50, 65)
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 13
minBtn.Text = "-"
minBtn.Parent = mainFrame
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 4)

-- Кнопка захвата
local targetBtn = Instance.new("TextButton")
targetBtn.Size = UDim2.new(0.9, 0, 0, 26)
targetBtn.Position = UDim2.new(0.05, 0, 0.18, 0)
targetBtn.BackgroundColor3 = Color3.fromRGB(35, 150, 90)
targetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
targetBtn.Font = Enum.Font.GothamBold
targetBtn.TextSize = 9
targetBtn.Text = "������ СЕСТЬ В РАКЕТУ (+)"
targetBtn.Parent = mainFrame
Instance.new("UICorner", targetBtn).CornerRadius = UDim.new(0, 5)

-- Текст скорости
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(0.9, 0, 0, 14)
speedLabel.Position = UDim2.new(0.05, 0, 0.38, 0)
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.fromRGB(200, 200, 210)
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextSize = 9
speedLabel.Text = "⚡ Скорость: 85"
speedLabel.Parent = mainFrame

-- Ползунок скорости (20 - 500)
local sliderBg = Instance.new("Frame")
sliderBg.Size = UDim2.new(0.9, 0, 0, 7)
sliderBg.Position = UDim2.new(0.05, 0, 0.48, 0)
sliderBg.BackgroundColor3 = Color3.fromRGB(40, 44, 58)
sliderBg.Parent = mainFrame
Instance.new("UICorner", sliderBg).CornerRadius = UDim.new(1, 0)

initialRatio = (flightSpeed - MIN_SPEED) / (MAX_SPEED - MIN_SPEED)
local sliderFill = Instance.new("Frame")
sliderFill.Size = UDim2.new(initialRatio, 0, 1, 0)
sliderFill.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
sliderFill.Parent = sliderBg
Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(1, 0)

local sliderBtn = Instance.new("TextButton")
sliderBtn.Size = UDim2.new(1, 0, 1, 0)
sliderBtn.BackgroundTransparency = 1
sliderBtn.Text = ""
sliderBtn.Parent = sliderBg

-- Кнопка сброса
local resetBtn = Instance.new("TextButton")
resetBtn.Size = UDim2.new(0.9, 0, 0, 24)
resetBtn.Position = UDim2.new(0.05, 0, 0.58, 0)
resetBtn.BackgroundColor3 = Color3.fromRGB(160, 45, 45)
resetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
resetBtn.Font = Enum.Font.GothamBold
resetBtn.TextSize = 9
resetBtn.Text = "������ Выйти / Сбросить"
resetBtn.Parent = mainFrame
Instance.new("UICorner", resetBtn).CornerRadius = UDim.new(0, 5)

-- Статус
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(0.9, 0, 0, 18)
statusLabel.Position = UDim2.new(0.05, 0, 0.75, 0)
statusLabel.BackgroundTransparency = 1
statusLabel.TextColor3 = Color3.fromRGB(150, 150, 160)
statusLabel.Font = Enum.Font.Gotham
statusLabel.TextSize = 8
statusLabel.TextWrapped = true
statusLabel.Text = "Наведи прицел (+) на ракету"
statusLabel.Parent = mainFrame

-- Логика свёртывания / разворачивания
minBtn.MouseButton1Click:Connect(function()
    openCircleBtn.Position = mainFrame.Position
    mainFrame.Visible = false
    openCircleBtn.Visible = true
end)

openCircleBtn.MouseButton1Click:Connect(function()
    mainFrame.Position = openCircleBtn.Position
    openCircleBtn.Visible = false
    mainFrame.Visible = true
end)

-- === 2. ЛОГИКА СЛАЙДЕРА (20 - 500) ===
local function updateSpeed(input)
    local pos = math.clamp((input.Position.X - sliderBg.AbsolutePosition.X) / sliderBg.AbsoluteSize.X, 0, 1)
    sliderFill.Size = UDim2.new(pos, 0, 1, 0)
    flightSpeed = math.floor(MIN_SPEED + pos * (MAX_SPEED - MIN_SPEED))
    speedLabel.Text = "⚡ Скорость: " .. flightSpeed
end

dragging = false
sliderBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        updateSpeed(input)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        updateSpeed(input)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

-- === 3. ВОЗВРАТ КАМЕРЫ ===
local function resetCamera()
    isCamActive = false
    if renderConn then
        renderConn:Disconnect()
        renderConn = nil
    end

    for obj, trans in pairs(originalTransparencies) do
        if obj and obj.Parent then
            obj.Transparency = trans
        end
    end
    originalTransparencies = {}
    currentTrackedPart = nil

    local char = localPlayer.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            Camera.CameraSubject = hum
            Camera.CameraType = Enum.CameraType.Custom
        end
    end
    statusLabel.Text = "Камера сброшена."
    statusLabel.TextColor3 = Color3.fromRGB(150, 150, 160)
end

-- === 4. ПОИСК РАКЕТЫ ===
local function getTargetUnderCrosshair()
    local char = localPlayer.Character
    if not char then return nil end

    local viewportCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    local ray = Camera:ViewportPointToRay(viewportCenter.X, viewportCenter.Y)

    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    raycastParams.FilterDescendantsInstances = {char}
    raycastParams.IgnoreWater = true

    local result = Workspace:Raycast(ray.Origin, ray.Direction * 500, raycastParams)

    if result and result.Instance then
        local hitPart = result.Instance
        if not hitPart:IsA("Terrain") and hitPart.Name ~= "Baseplate" then
            return hitPart
        end
    end
    return nil
end

-- === 5. АКТИВАЦИЯ УПРАВЛЕНИЯ ===
local function attachInsideRocket(part)
    if not part or not part:IsA("BasePart") then return end

    currentTrackedPart = part
    isCamActive = true
    statusLabel.Text = "������ Ракета активна!"
    statusLabel.TextColor3 = Color3.fromRGB(50, 255, 100)

    local model = part.Parent
    if model and model:IsA("Model") then
        for _, obj in ipairs(model:GetDescendants()) do
            if obj:IsA("BasePart") then
                originalTransparencies[obj] = obj.Transparency
                obj.Transparency = 1
            end
        end
    else
        originalTransparencies[part] = part.Transparency
        part.Transparency = 1
    end

    if renderConn then renderConn:Disconnect() end
    Camera.CameraType = Enum.CameraType.Scriptable

    renderConn = RunService.RenderStepped:Connect(function()
        if isCamActive and currentTrackedPart and currentTrackedPart.Parent then
            local camCF = Camera.CFrame

            currentTrackedPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            currentTrackedPart.AssemblyLinearVelocity = camCF.LookVector * flightSpeed
            currentTrackedPart.CFrame = CFrame.new(currentTrackedPart.Position, currentTrackedPart.Position + camCF.LookVector) * NOSE_OFFSET
            Camera.CFrame = CFrame.new(currentTrackedPart.Position) * camCF.Rotation
        else
            resetCamera()
        end
    end)
end

-- === 6. КНОПКИ ===
targetBtn.MouseButton1Click:Connect(function()
    local targetPart = getTargetUnderCrosshair()
    if targetPart then
        attachInsideRocket(targetPart)
    else
        statusLabel.Text = "❌ Наведи прицел (+) на ракету!"
        statusLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
    end
end)

resetBtn.MouseButton1Click:Connect(function()
    resetCamera()
end)]======]
  loadstring(gui_code)()
end
})


do
-- ==============================================
-- Вкладка: SETTINGS
-- ==============================================
-- === Создание секции Settings (��сё в pcall чтобы видеть ошибки) ===
print("[OBLIVION DEBUG] Before Settings section")
local SettingsSection = Tabs.Settings:AddLeftGroupbox("Menu", "wrench")

-- ==============================================
-- Вкладка: SETTINGS — УВЕДОМЛЕНИЯ
-- ==============================================

do
-- Уведомления о событиях на сервере: кто вышел, кто вернулся, кого выбило
-- (кик), а также маленькие уведомления при вкл/выкл функций чита.
-- ВАЖНО про «кик»: Roblox НЕ сообщает другим клиентам причину выхода игрока
-- (кикнули его или он сам вышел — для нас это одно и то же событие). Поэтому
-- «кик» определяется эвристикой: если игрок уме�� за па��у секунд до выхода —
-- считаем, что его выбили (кикнули); иначе — просто вышел.
local NotifySection = Tabs.Settings:AddRightGroupbox("Notifications")

NotifySection:AddToggle("EnableNotifications", {
 Text = "Notifications",
 Default = false,
 Tooltip = "Показывать уведомления о событиях на сервере",
})

NotifySection:AddDropdown("NotifyEvents", {
 Text = "Уведомлять о",
 Values = { "Кикнутые игроки", "Ливнувшие игроки", "Вернувшиеся игроки", "Вкл/выкл функций" },
 Default = {},
 Multi = true,
 Tooltip = "Выбери, о каких событиях показ��вать уведомления",
})

-- Хочет ли пользователь уведомлени�� данного типа (мастер-тумблер + галочка)
local function notifyWants(kind)
 if not (Toggles.EnableNotifications and Toggles.EnableNotifications.Value) then
 return false
 end
 local v = Options.NotifyEvents and Options.NotifyEvents.Value
 if type(v) ~= "table" then return false end
 if v[kind] == true then return true end
 for _, name in pairs(v) do
 if name == kind then return true end
 end
 return false
end

-- notifyDeaths: globals to avoid local register limit (200)
-- Служебные таблицы
notifyDeaths = {} -- [UserId] = tick() последней смерти (для эвристики «кик»)
notifyLeftRecently = {} -- [UserId] = { name =, time = } для детекта возврата
NOTIFY_RETURN_WINDOW = 600 -- сколько секунд ждём «возврат» игрока

-- Следим за смерт��ю персонажа игрока (нужно, чтобы отличить кик от выхода)
function notifyHookPlayer(plr)
 if plr == LocalPlayer then return end
 local function onChar(char)
 local hum = char:FindFirstChildOfClass("Humanoid") or char:WaitForChild("Humanoid", 5)
 if hum then
 hum.Died:Connect(function()
 notifyDeaths[plr.UserId] = tick()
 end)
 end
 end
 if plr.Character then onChar(plr.Character) end
 plr.CharacterAdded:Connect(onChar)
end

for _, plr in ipairs(Players:GetPlayers()) do
 notifyHookPlayer(plr)
end

Players.PlayerAdded:Connect(function(plr)
 notifyHookPlayer(plr)
 -- Возврат: игрок с этим UserId недавно выходил и снова зашёл
 local rec = notifyLeftRecently[plr.UserId]
 if rec and tick() - rec.time <= NOTIFY_RETURN_WINDOW then
 notifyLeftRecently[plr.UserId] = nil
 if notifyWants("Вернувшиеся игроки") then
 Library:Notify(string.format("%s вернулся в игру", plr.DisplayName), 4)
 end
 end
end)

Players.PlayerRemoving:Connect(function(plr)
 if plr == LocalPlayer then return end
 -- Запоминаем для возможного «возврата»
 notifyLeftRecently[plr.UserId] = { name = plr.DisplayName, time = tick() }
 -- Кик или обычный выход? Эвристика: недавно умер => его выбили (кик)
 local d = notifyDeaths[plr.UserId]
 local wasKicked = d ~= nil and (tick() - d <= 5)
 notifyDeaths[plr.UserId] = nil
 if wasKicked then
 if notifyWants("Кикнутые игроки") then
 Library:Notify(string.format("%s выбит из игры (кик)", plr.DisplayName), 4)
 end
 else
 if notifyWants("Ливнувшие игроки") then
 Library:Notify(string.format("%s вышел из игры", plr.DisplayName), 4)
 end
 end
end)


-- Маленькие уведомлен���� ��ри вкл/выкл функций чита.
-- ВАЖНО (и��правление): в Obsidian у каждого тумблера ОДИН обработчик изменения.
-- Раньше мы вешали уведомление через toggle:OnChanged(...) на ВСЕ тумблеры — и это
-- ЗАТИРАЛО «родные» обработчики функций: спавн еды в Anti Ownership, запуск
-- Anti Lag / Anti Kick / Anti Grab / Anti Explosion и ВОЗВРАТ размеров хитбоксов
-- при выключении. Из-за этого функции переставали работать (при включении только
-- показывалось уведомление и больше ничего), а хитбоксы «оставались большими».
-- Теперь НЕ трогаем OnChanged вообще: просто СЛЕДИМ за значениями тумблеров в
-- отдельном цикле и шлём уведомление при изменении. Родные обработчики целы.
task.defer(function()
 local notifyLastValues = {}
 for flag, toggle in pairs(Toggles) do
 if type(toggle) == "table" then
 notifyLastValues[flag] = toggle.Value
 end
 end
 while true do
 task.wait(0.1)
 for flag, toggle in pairs(Toggles) do
 if flag ~= "EnableNotifications" and type(toggle) == "table" then
 local v = toggle.Value
 if notifyLastValues[flag] ~= v then
 notifyLastValues[flag] = v
 if notifyWants("Вкл/выкл функций") then
 local label = toggle.Text or flag
 Library:Notify((v and "Включено: " or "Выключено: ") .. label, 2)
 end
 end
 end
 end
 end
end)


-- Бинд меню
local menuOk = pcall(function()
 SettingsSection:AddLabel("Menu bind")
 :AddKeyPicker("MenuKeybind", { Default = "RightShift", NoUI = true, Text = "Menu keybind" })
end)
if not menuOk then warn("[Oblivion] MenuKeybind failed") end

-- Кастомный курсор
local cursorOk = pcall(function()
 SettingsSection:AddToggle("ShowCustomCursor", {
 Text = "Custom Cursor",
 Default = true,
 Callback = function(Value)
 Library.ShowCustomCursor = Value
 end,
 })
end)
if not cursorOk then warn("[Oblivion] ShowCustomCursor failed") end

-- Сторона уведомлений
local notifyOk = pcall(function()
 SettingsSection:AddDropdown("NotificationSide", {
 Values = { "Left", "Right" },
 Default = "Right",
 Text = "Notification Side",
 Callback = function(Value)
 pcall(function() Library:SetNotifySide(Value) end)
 end,
 })
end)
if not notifyOk then warn("[Oblivion] NotificationSide failed") end

-- Масштаб DPI
local dpiOk = pcall(function()
 SettingsSection:AddDropdown("DPIDropdown", {
 Values = { "50%", "75%", "100%", "125%", "150%", "175%", "200%" },
 Default = "100%",
 Text = "DPI Scale",
 Callback = function(Value)
 Value = Value:gsub("%%", "")
 pcall(function() Library:SetDPIScale(tonumber(Value)) end)
 end,
 })
end)
if not dpiOk then warn("[Oblivion] DPI failed") end

-- ��адиус скругления
local cornerOk = pcall(function()
 local defCorner = 5
 pcall(function() defCorner = Library.CornerRadius or 5 end)
 SettingsSection:AddSlider("UICornerSlider", {
 Text = "Corner Radius",
 Default = defCorner,
 Min = 0,
 Max = 20,
 Rounding = 0,
 Callback = function(value)
 pcall(function() Window:SetCornerRadius(value) end)
 end
 })
end)
if not cornerOk then warn("[Oblivion] Corner slider failed") end

-- ==============================================
-- Вкладка: SETTINGS — WATERMARK
-- ==============================================

task.spawn(function()
 local CoreGui = (gethui and gethui()) or game:GetService("CoreGui")
 local oldWm = CoreGui:FindFirstChild("OblivionWatermark")
 if oldWm then oldWm:Destroy() end

 local watermarkGui = Instance.new("ScreenGui")
 watermarkGui.Name = "OblivionWatermark"
 watermarkGui.ResetOnSpawn = false
 watermarkGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
 watermarkGui.Parent = CoreGui

 local watermarkFrame = Instance.new("Frame")
 watermarkFrame.Size = UDim2.new(0, 0, 0, 30)
 watermarkFrame.AutomaticSize = Enum.AutomaticSize.X
 watermarkFrame.Position = UDim2.new(0, 20, 0, 20)
 watermarkFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
 watermarkFrame.BackgroundTransparency = 0.2
 watermarkFrame.BorderSizePixel = 0
 watermarkFrame.Visible = false
 watermarkFrame.Parent = watermarkGui

 local wmCorner = Instance.new("UICorner")
 wmCorner.CornerRadius = UDim.new(0, 8)
 wmCorner.Parent = watermarkFrame

 local wmStroke = Instance.new("UIStroke")
 wmStroke.Color = Color3.fromRGB(60, 60, 75)
 wmStroke.Thickness = 1.5
 wmStroke.Parent = watermarkFrame

 local wmText = Instance.new("TextLabel")
 wmText.Size = UDim2.new(0, 0, 1, 0)
 wmText.AutomaticSize = Enum.AutomaticSize.X
 wmText.Position = UDim2.new(0, 0, 0, 0)
 wmText.BackgroundTransparency = 1
 wmText.Text = "<b>Oblivion</b> | FPS: 0 | Ping: 0ms"
 wmText.TextColor3 = Color3.fromRGB(230, 230, 230)
 wmText.Font = Enum.Font.GothamMedium
 wmText.TextSize = 14
 wmText.RichText = true
 wmText.Parent = watermarkFrame

 local wmPadding = Instance.new("UIPadding")
 wmPadding.PaddingLeft = UDim.new(0, 14)
 wmPadding.PaddingRight = UDim.new(0, 14)
 wmPadding.Parent = wmText

 local wmFrames = 0
 local wmLastUpdate = tick()
 local wmFPS = 0

 RunService.RenderStepped:Connect(function()
 wmFrames = wmFrames + 1
 local now = tick()
 if now - wmLastUpdate >= 1 then
 wmFPS = wmFrames
 wmFrames = 0
 wmLastUpdate = now
 end
 
 if Toggles.EnableWatermark and Toggles.EnableWatermark.Value then
 watermarkFrame.Visible = true
 local ping = 0
 pcall(function()
 ping = math.floor(LocalPlayer:GetNetworkPing() * 1000)
 end)
 if ping == 0 then
 pcall(function()
 ping = math.floor(game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValue())
 end)
 end
 wmText.Text = string.format("<b>Oblivion</b> | FPS: %d | Ping: %d ms", wmFPS, ping)
 else
 watermarkFrame.Visible = false
 end
 end)

 local wmOk = pcall(function()
 SettingsSection:AddToggle("EnableWatermark", {
 Text = "Watermark",
 Default = true,
 Tooltip = "Показывать красивый ватермарк (Oblivion, FPS, Ping) на экране",
 })
 end)
 if not wmOk then warn("[Oblivion] Watermark toggle failed") end
end)
SettingsSection:AddDivider()

-- Кнопка выгрузки
SettingsSection:AddButton({
 Text = "Unload",
 Func = function()
 if workspace.CurrentCamera then
 workspace.CurrentCamera.FieldOfView = defaultFOV
 end
 Library:Unload()
 end,
 Tooltip = "Полностью выгрузить чит из игры"
})

-- Назначаем бинд меню
pcall(function() Library.ToggleKeybind = Options.MenuKeybind end)

-- Отслеживание выгрузки
pcall(function()
 Library:OnUnload(function()
 if workspace.CurrentCamera then
 workspace.CurrentCamera.FieldOfView = defaultFOV
 end
 if watermarkGui then watermarkGui:Destroy() end
 end)
end)

-- === ThemeManager ===
-- === ОПТИМИЗАЦИЯ: ThemeManager/SaveManager ждём в фоне, без лишних Notify ===
-- Раньше тут было 5+ Library:Notify подряд (каждый создаёт UI-элемент) +
-- последовательные pcall. Теперь всё в одном task.spawn с минимальным выводом.
task.spawn(function()
 -- ждём пока ThemeManager и SaveManager догрузятся из параллельных HTTP-запросов
 local t0 = tick()
 while not (themeDone and saveDone) do
 if tick() - t0 > 15 then break end -- таймаут 15с
 task.wait(0.05)
 end
 pcall(function()
-- ==============================================
do
-- Вкладка: FUN — COCONUT VISUALS (MeepCity)
-- ==============================================
do
local CoconutToys = Tabs.Fun:AddRightGroupbox("Coconut Visuals")

CoconutToys:AddToggle("EnableCocoPenis", {
    Text = "Coconut Penis",
    Default = false,
    Tooltip = "Создает пенис из кокосов",
})

CoconutToys:AddSlider("CocoPenisLength", {
    Text = "Penis Length",
    Default = 5,
    Min = 1,
    Max = 20,
    Rounding = 0,
    Tooltip = "Длина (в кокосах)",
})

CoconutToys:AddToggle("EnableCocoBreasts", {
    Text = "Coconut Breasts",
    Default = false,
    Tooltip = "2 кокоса на груди",
})

CoconutToys:AddToggle("EnableCocoButt", {
    Text = "Coconut Butt",
    Default = false,
    Tooltip = "2 кокоса на попке",
})

task.spawn(function()
    local RS = game:GetService("ReplicatedStorage")
    local GrabEvents = RS:WaitForChild("GrabEvents", 5)
    local MenuToys = RS:WaitForChild("MenuToys", 5)
    if not (GrabEvents and MenuToys) then return end
    
    local SetNetworkOwner = GrabEvents:FindFirstChild("SetNetworkOwner")
    local SpawnToy = MenuToys:FindFirstChild("SpawnToyRemoteFunction")
    local DestroyToy = MenuToys:FindFirstChild("DestroyToy")

    while task.wait(0.05) do
        local penisEn = Toggles.EnableCocoPenis and Toggles.EnableCocoPenis.Value
        local breastsEn = Toggles.EnableCocoBreasts and Toggles.EnableCocoBreasts.Value
        local buttEn = Toggles.EnableCocoButt and Toggles.EnableCocoButt.Value
        
        if not (penisEn or breastsEn or buttEn) then continue end

        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not root then continue end

        local len = Options.CocoPenisLength and Options.CocoPenisLength.Value or 5
        local neededCount = 0
        if penisEn then neededCount = neededCount + len + 2 end
        if breastsEn then neededCount = neededCount + 2 end
        if buttEn then neededCount = neededCount + 2 end

        local spawnedToysFolder = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
        if not spawnedToysFolder then continue end

        local allCoconuts = {}
        for _, toy in ipairs(spawnedToysFolder:GetChildren()) do
            if toy.Name == "FoodCoconut" then
                table.insert(allCoconuts, toy)
            end
        end

        if #allCoconuts < neededCount and SpawnToy then
            task.spawn(function()
                pcall(function()
                    SpawnToy:InvokeServer("FoodCoconut", root.CFrame * CFrame.new(-5, 5, 10), Vector3.zero)
                end)
            end)
        end

        local cocoIndex = 1
        local velOffset = CFrame.new(root.Velocity / 100)

        local function positionCoco(coco, offsetCFrame)
            local part = coco:FindFirstChild("SoundPart")
            local holdPart = coco:FindFirstChild("HoldPart")
            local rigid = holdPart and holdPart:FindFirstChild("RigidConstraint")
            local owner = part and part:FindFirstChild("PartOwner")

            if part and holdPart and rigid then
                if owner and owner.Value == LocalPlayer.Name then
                    part.CFrame = root.CFrame * offsetCFrame * velOffset
                    part.Velocity = Vector3.zero
                end

                if not owner or owner.Value ~= LocalPlayer.Name then
                    if SetNetworkOwner then
                        pcall(function() SetNetworkOwner:FireServer(part, part.CFrame) end)
                    end
                end

                if rigid.Attachment1 and DestroyToy then
                    pcall(function() DestroyToy:FireServer(coco) end)
                end

                for _, p in ipairs(coco:GetChildren()) do
                    if p:IsA("BasePart") then
                        p.CanCollide = false
                        p.CanQuery = false
                        if p.Transparency ~= 1 then p.Transparency = 0 end
                    end
                end
            end
        end

        if penisEn then
            local balls = {CFrame.new(-0.55, -1.2, -0.8), CFrame.new(0.55, -1.2, -0.8)}
            for i = 1, 2 do
                if allCoconuts[cocoIndex] then
                    positionCoco(allCoconuts[cocoIndex], balls[i])
                    cocoIndex = cocoIndex + 1
                end
            end
            for i = 1, len do
                if allCoconuts[cocoIndex] then
                    local offsetZ = -0.8 - (i * 0.9)
                    positionCoco(allCoconuts[cocoIndex], CFrame.new(0, -1.2, offsetZ))
                    cocoIndex = cocoIndex + 1
                end
            end
        end

        if breastsEn then
            -- 2 coconuts on chest (higher Y, slightly forward)
            local breastOffsets = {CFrame.new(-0.6, 0.5, -0.9), CFrame.new(0.6, 0.5, -0.9)}
            for i = 1, 2 do
                if allCoconuts[cocoIndex] then
                    positionCoco(allCoconuts[cocoIndex], breastOffsets[i])
                    cocoIndex = cocoIndex + 1
                end
            end
        end

        if buttEn then
            -- 2 coconuts on butt (lower Y, behind)
            local buttOffsets = {CFrame.new(-0.55, -1, 0.75), CFrame.new(0.55, -1, 0.75)}
            for i = 1, 2 do
                if allCoconuts[cocoIndex] then
                    positionCoco(allCoconuts[cocoIndex], buttOffsets[i])
                    cocoIndex = cocoIndex + 1
                end
            end
        end
    end
end)



end


-- === SPARKLER BUILD ===
end
do
local SparklerBuildGroup = Tabs.Fun:AddLeftGroupbox("Sparkler Build")

local buildHighRun = false
local buildConnection = nil
local buildToy = nil
local buildPart = nil
local buildBp = nil
local buildBg = nil
local multiSparklers = {}
local currentBuildShape = "Heart"

SparklerBuildGroup:AddToggle("EnableSparklerBuild", {
    Text = "Sparkler Build",
    Default = false,
    Tooltip = "Спавнит бенгальские огни и рисует выбранную фигуру",
})

SparklerBuildGroup:AddDropdown("SparklerBuildShape", {
    Text = "Shape",
    Values = {"Heart", "Star", "Tree", "Saturn", "Cross", "Sword", "Wings", "Crown", "Tornado", "Rocket", "Umbrella", "Atom", "DNA", "Planets"},
    Default = 1,
    Multi = false,
    Tooltip = "Фигура для рисования бенгальскими огнями",
})

local function setupSparklerPart(toy, part)
    for _, v in ipairs(toy:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Anchored = false
            v.CanCollide = false
            v.Massless = true
        elseif v:IsA("JointInstance") or v:IsA("Weld") or v:IsA("WeldConstraint") or v:IsA("Motor6D") then
            pcall(function() v:Destroy() end)
        elseif v:IsA("Tool") then
            v.Enabled = false
        end
    end
    part:BreakJoints()
    local bp = Instance.new("BodyPosition")
    bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bp.P = 20000
    bp.D = 500
    bp.Parent = part
    local bg = Instance.new("BodyGyro")
    bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bg.P = 3000
    bg.CFrame = CFrame.new()
    bg.Parent = part
    return bp, bg
end

local function setupMultiPart(toy, part)
    for _, v in ipairs(toy:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Anchored = false
            v.CanCollide = false
            v.Massless = true
        elseif v:IsA("JointInstance") or v:IsA("Weld") or v:IsA("WeldConstraint") or v:IsA("Motor6D") then
            pcall(function() v:Destroy() end)
        elseif v:IsA("Tool") then
            v.Enabled = false
        end
    end
    part:BreakJoints()
    return part
end

local function spawnSingleSparkler(RS, player, hrp)
    pcall(function()
        RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("FireworkSparkler", hrp.CFrame * CFrame.new(0, 50, 0), Vector3.zero)
    end)
    local folder = workspace:WaitForChild(player.Name .. "SpawnedInToys", 5)
    if not folder then return nil end
    local toy = folder:WaitForChild("FireworkSparkler", 5)
    if not toy then return nil end
    local part = toy:FindFirstChild("Handle") or toy:FindFirstChildWhichIsA("BasePart")
    if not part then return nil end
    task.wait(0.2)
    local bp, bg = setupSparklerPart(toy, part)
    return toy, part, bp, bg
end

local function clearMultiSparklers()
    for _, s in ipairs(multiSparklers) do
        pcall(function() s.toy:Destroy() end)
    end
    multiSparklers = {}
end

multiShapeConfigs = {
    Saturn = {
        count = 5,
        update = function(i, t, centerCf)
            if i <= 3 then
                local idx = i
                local radius = 8
                local angle = t * 3.6 + (idx * (2 * math.pi / 3))
                local tiltAngle = math.rad(idx * 60)
                local offset = Vector3.new(radius * math.cos(angle), radius * math.sin(angle), 0)
                local rotated = CFrame.Angles(tiltAngle, 0, 0) * offset
                return centerCf * CFrame.new(rotated)
            else
                local idx = i - 3
                local ringRadius = 15
                local angle = t * 2.4 + (idx * math.pi)
                local offset = Vector3.new(ringRadius * math.cos(angle), 0, ringRadius * math.sin(angle))
                local tilted = CFrame.Angles(math.rad(15), 0, 0) * offset
                return centerCf * CFrame.new(tilted)
            end
        end
    },
    Atom = {
        count = 4,
        update = function(i, t, centerCf)
            if i == 1 then
                return centerCf
            else
                local idx = i - 1
                local radius = 12
                local angle = t * 2.0 + (idx * (2 * math.pi / 3))
                local offset
                if idx == 1 then
                    offset = Vector3.new(radius * math.cos(angle), radius * math.sin(angle), 0)
                elseif idx == 2 then
                    offset = Vector3.new(0, radius * math.sin(angle), radius * math.cos(angle))
                else
                    offset = Vector3.new(radius * math.cos(angle), 0, radius * math.sin(angle))
                end
                return centerCf * CFrame.new(offset)
            end
        end
    },
    Rocket = {
        count = 5,
        height = 15,
        update = function(i, t, centerCf)
            if i == 1 then
                -- nose tip
                return centerCf * CFrame.new(0, 8, 0)
            elseif i == 2 or i == 3 then
                -- body rings
                local y = i == 2 and 2 or -2
                local radius = 2
                local angle = (i - 2) * math.pi + t * 4
                local offset = Vector3.new(radius * math.cos(angle), 0, radius * math.sin(angle))
                return centerCf * CFrame.new(0, y, 0) * CFrame.new(offset)
            else
                -- fins (left/right)
                local side = i == 4 and 1 or -1
                local x = side * 5
                return centerCf * CFrame.new(x, -6, 0)
            end
        end
    },
    Umbrella = {
        count = 5,
        height = 15,
        update = function(i, t, centerCf)
            if i == 1 then
                -- top of umbrella
                return centerCf * CFrame.new(0, 5, 0)
            else
                -- spoke, slightly curved down
                local idx = i - 1
                local radius = 8
                local angle = idx * (2 * math.pi / 4)
                local offset = Vector3.new(radius * math.cos(angle), -1, radius * math.sin(angle))
                return centerCf * CFrame.new(offset)
            end
        end
    },
    Planets = {
        count = 5,
        update = function(i, t, centerCf)
            if i == 1 then
                return centerCf
            else
                local idx = i - 1
                local ringRadius = 15
                local angle = t * 1.5 + idx * (math.pi / 2)
                local offset = Vector3.new(ringRadius * math.cos(angle), 0, ringRadius * math.sin(angle))
                return centerCf * CFrame.new(offset)
            end
        end
    }
}

local function isMultiShape(shape)
    return multiShapeConfigs[shape] ~= nil
end

local function spawnMultiSparklers(RS, player, hrp, shape)
    clearMultiSparklers()
    local config = multiShapeConfigs[shape]
    if not config then return end
    local folder = workspace:WaitForChild(player.Name .. "SpawnedInToys", 5)
    if not folder then return end

    for _, child in ipairs(folder:GetChildren()) do
        if child.Name == "FireworkSparkler" then
            pcall(function() child:Destroy() end)
        end
    end

    local function claimSparkler(toy)
        if toy:FindFirstChild("ClaimedByBuild") then return false end
        local tag = Instance.new("BoolValue")
        tag.Name = "ClaimedByBuild"
        tag.Value = true
        tag.Parent = toy
        return true
    end

    local function spawnAndClaim(cf)
        pcall(function()
            RS.MenuToys.SpawnToyRemoteFunction:InvokeServer("FireworkSparkler", cf, Vector3.zero)
        end)
        local start = tick()
        while (tick() - start) < 3 do
            for _, child in ipairs(folder:GetChildren()) do
                if child.Name == "FireworkSparkler" and claimSparkler(child) then
                    child.Parent = folder
                    return child
                end
            end
            task.wait(0.05)
        end
        return nil
    end

    for i = 1, config.count do
        local height = config.height or 25
        local cf = hrp.CFrame * CFrame.new(0, height, 0)
        local toy = spawnAndClaim(cf)
        if toy then
            local part = toy:FindFirstChild("Handle") or toy:FindFirstChildWhichIsA("BasePart")
            if part then
                pcall(function()
                    RS.GrabEvents.SetNetworkOwner:FireServer(part, part.CFrame)
                end)
                task.wait(0.1)
                setupMultiPart(toy, part)
                table.insert(multiSparklers, { toy = toy, part = part, index = i })
            end
        end
        task.wait(0.1)
    end
end

Toggles.EnableSparklerBuild:OnChanged(function()
    buildHighRun = Toggles.EnableSparklerBuild.Value
    local RS = game:GetService("ReplicatedStorage")
    local RunService = game:GetService("RunService")
    local player = LocalPlayer

    if buildHighRun then
        currentBuildShape = Options.SparklerBuildShape and Options.SparklerBuildShape.Value or "Heart"
        task.spawn(function()
            if not player.Character then return end
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            if not hrp then return end

            if isMultiShape(currentBuildShape) then
                spawnMultiSparklers(RS, player, hrp, currentBuildShape)
                if #multiSparklers == 0 then return end
            else
                buildToy, buildPart, buildBp, buildBg = spawnSingleSparkler(RS, player, hrp)
                if not buildToy then return end
            end

            local t = 0
            if buildConnection then buildConnection:Disconnect() end
            buildConnection = RunService.Heartbeat:Connect(function(dt)
                local ok, err = pcall(function()
                    if not buildHighRun then
                        if buildConnection then buildConnection:Disconnect() end
                        return
                    end

                    local char = player.Character
                    local currentHrp = char and char:FindFirstChild("HumanoidRootPart")
                    if not currentHrp then return end

                    t = t + (8 * dt)

                    local config = multiShapeConfigs[currentBuildShape]
                    if config then
                        local height = config.height or 25
                        local centerCf = currentHrp.CFrame * CFrame.new(0, height, 0)
                        for _, s in ipairs(multiSparklers) do
                            if not s.part or not s.part.Parent then continue end
                            local targetCf = config.update(s.index, t, centerCf)
                            s.part.AssemblyLinearVelocity = Vector3.zero
                            s.part.AssemblyAngularVelocity = Vector3.zero
                            s.part.CFrame = targetCf
                        end
                        return
                    end

                    if not buildPart or not buildPart.Parent then
                        if buildToy and buildToy.Parent then
                            pcall(function() buildToy:Destroy() end)
                        end
                        buildToy = nil
                        buildPart = nil
                        buildBp = nil
                        buildBg = nil
                        task.spawn(function()
                            if not buildHighRun then return end
                            local char2 = player.Character
                            local hrp2 = char2 and char2:FindFirstChild("HumanoidRootPart")
                            if not hrp2 then return end
                            buildToy, buildPart, buildBp, buildBg = spawnSingleSparkler(RS, player, hrp2)
                        end)
                        return
                    end

                    pcall(function()
                        RS.GrabEvents.SetNetworkOwner:FireServer(buildPart, buildPart.CFrame)
                    end)

                    local scale = 1.5
                    local x, y, z
                    if currentBuildShape == "Heart" then
                        x = 16 * math.sin(t) ^ 3
                        y = 13 * math.cos(t) - 5 * math.cos(2 * t) - 2 * math.cos(3 * t) - math.cos(4 * t)
                        z = 3
                    elseif currentBuildShape == "Star" then
                        local r = 10 * (1 + 0.5 * math.cos(5 * t))
                        x = r * math.cos(t)
                        y = r * math.sin(t)
                        z = 3
                    elseif currentBuildShape == "Tree" then
                        local cycle = t % (2 * math.pi)
                        local height = cycle / (2 * math.pi) * 15
                        local progress = height / 15
                        local radius = (1 - progress) * 3.5
                        local angle = cycle * 10
                        x = radius * math.cos(angle)
                        y = height - 7.5
                        z = radius * math.sin(angle)
                    elseif currentBuildShape == "Cross" then
                        local cycle = t % 4
                        if cycle < 1 then
                            x = 10 * (cycle - 0.5) * 2
                            y = 0
                        elseif cycle < 2 then
                            x = 0
                            y = 10 * (cycle - 1.5) * 2
                        elseif cycle < 3 then
                            x = -10 * (cycle - 2.5) * 2
                            y = 0
                        else
                            x = 0
                            y = -10 * (cycle - 3.5) * 2
                        end
                        z = 3
                    elseif currentBuildShape == "Sword" then
                        local cycle = t % 2
                        if cycle < 0.8 then
                            x = 0
                            y = 15 * cycle - 6
                        elseif cycle < 1.0 then
                            x = 8 * (cycle - 0.9) * 10
                            y = 6
                        elseif cycle < 1.6 then
                            x = 0
                            y = 6 - 10 * (cycle - 1.0)
                        else
                            x = 0
                            y = 0
                        end
                        z = 3
                    elseif currentBuildShape == "Wings" then
                        local cycle = t % 2
                        local side = cycle < 1 and 1 or -1
                        local c = cycle < 1 and cycle or cycle - 1
                        x = side * (5 + 10 * c)
                        y = 5 * math.sin(c * math.pi)
                        z = 3
                    elseif currentBuildShape == "Crown" then
                        local n = 5
                        local cycle = (t % 1) * n
                        local idx = math.floor(cycle)
                        local localT = cycle - idx
                        local angle = idx * (2 * math.pi / n)
                        local nextAngle = (idx + 1) * (2 * math.pi / n)
                        local baseRadius = 8
                        local x1 = baseRadius * math.cos(angle)
                        local z1 = baseRadius * math.sin(angle)
                        local x2 = baseRadius * math.cos(nextAngle)
                        local z2 = baseRadius * math.sin(nextAngle)
                        x = x1 + (x2 - x1) * localT
                        z = z1 + (z2 - z1) * localT
                        y = (localT < 0.5 and localT * 2 or 2 - localT * 2) * 6
                    elseif currentBuildShape == "Tornado" then
                        local height = (t % 3) / 3 * 25
                        local radius = 8 * (1 - height / 25)
                        local angle = t * 8
                        x = radius * math.cos(angle)
                        z = radius * math.sin(angle)
                        y = height - 12
                    elseif currentBuildShape == "DNA" then
                        local cycle = t % 2
                        local progress = cycle / 2
                        local spiralHeight = 20
                        local radius = 5
                        local turns = 2
                        local angle = t * 8 + progress * turns * 2 * math.pi
                        x = radius * math.cos(angle)
                        z = radius * math.sin(angle)
                        y = (progress - 0.5) * spiralHeight
                    elseif currentBuildShape == "Umbrella" then
                        local n = 5
                        local cycle = (t % 1) * n
                        local idx = math.floor(cycle)
                        local localT = cycle - idx
                        local angle = idx * (2 * math.pi / n)
                        local radius = 12 * localT
                        x = radius * math.cos(angle)
                        z = radius * math.sin(angle)
                        y = 0
                    elseif currentBuildShape == "Planets" then
                        local cycle = t % 3
                        local ring = math.floor(cycle) + 1
                        local localT = cycle - (ring - 1)
                        local ringRadius = 8 + ring * 5
                        local angle = t * 2 + localT * 2 * math.pi
                        x = ringRadius * math.cos(angle)
                        z = ringRadius * math.sin(angle)
                        y = 0
                    else
                        x = 0; y = 0; z = 0
                    end

                    local relPos
                    if currentBuildShape == "Crown" then
                        relPos = Vector3.new(x * scale, (y * scale) + 5, z)
                    elseif currentBuildShape == "Wings" then
                        relPos = Vector3.new(x * scale, (y * scale) + 15, -5)
                    else
                        relPos = Vector3.new(x * scale, (y * scale) + 25, z)
                    end
                    buildBp.Position = currentHrp.CFrame:PointToWorldSpace(relPos)
                    buildBg.CFrame = currentHrp.CFrame
                end)
                if not ok then
                    warn("[Sparkler Build Heartbeat Error] " .. tostring(err))
                end
            end)
        end)
    else
        if buildConnection then
            buildConnection:Disconnect()
            buildConnection = nil
        end
        if buildToy then
            pcall(function() buildToy:Destroy() end)
            buildToy = nil
            buildPart = nil
            buildBp = nil
            buildBg = nil
        end
        clearMultiSparklers()
        local backpack = player:FindFirstChild("Backpack")
        if backpack then
            for _, v in ipairs(backpack:GetChildren()) do
                if v.Name == "FireworkSparkler" then
                    pcall(function() v:Destroy() end)
                end
            end
        end
    end
end)
end
end)

-- ==============================================
-- Вкладка: FUN — TSUNAMI
-- ==============================================
do
local TsunamiSection = Tabs.Fun and Tabs.Fun:AddLeftGroupbox("Tsunami") or nil
if not TsunamiSection then
    warn("[OBLIVION] TsunamiSection: AddLeftGroupbox вернул nil — пропускаем")
    return
end
massiveTsunamiPart = nil

TsunamiSection:AddToggle("EnableTsunami", {
 Text = "Tsunami",
 Default = false,
 Tooltip = "Поглощает карту: создает огромный блок воды",
 Callback = function(Value)
  if Value then
   if not massiveTsunamiPart then
    massiveTsunamiPart = Instance.new("Part")
    massiveTsunamiPart.Name = "MassiveTsunami"
    massiveTsunamiPart.Material = Enum.Material.Water
    massiveTsunamiPart.BrickColor = BrickColor.new("Deep blue")
    massiveTsunamiPart.Transparency = 0.5
    massiveTsunamiPart.Anchored = true
    massiveTsunamiPart.CanCollide = false
    massiveTsunamiPart.Size = Vector3.new(20000, 5, 20000)
    massiveTsunamiPart.CFrame = CFrame.new(0, 50, 0)
    massiveTsunamiPart.Parent = workspace
   end
  else
   if massiveTsunamiPart then
    massiveTsunamiPart:Destroy()
    massiveTsunamiPart = nil
   end
  end
 end
})

TsunamiSection:AddSlider("TsunamiSize", {
 Text = "Tsunami Height",
 Default = 5,
 Min = 1,
 Max = 200,
 Rounding = 1,
 Compact = false,
 Tooltip = "Высота волны (Y)",
 Callback = function(Value)
  if massiveTsunamiPart then
   massiveTsunamiPart.Size = Vector3.new(20000, Value * 10, 20000)
   massiveTsunamiPart.CFrame = CFrame.new(0, (Value * 10) / 2, 0)
  end
 end
})
end


pcall(function() ThemeManager:SetLibrary(Library) end)
pcall(function() SaveManager:SetLibrary(Library) end)
pcall(function() SaveManager:IgnoreThemeSettings() end)
pcall(function() SaveManager:SetIgnoreIndexes({ "MenuKeybind" }) end)
pcall(function() ThemeManager:SetFolder("Oblivion_FTAP") end)
pcall(function() SaveManager:SetFolder("Oblivion_FTAP/Configs") end)
pcall(function() SaveManager:BuildConfigSection(Tabs.Settings) end)
pcall(function() ThemeManager:ApplyToTab(Tabs.Settings) end)
pcall(function() SaveManager:LoadAutoloadConfig() end)
end)

Library:Notify("Oblivion Loaded!", 3)

-- ==============================================
-- MOBILE KEYBOARD — виртуальная клавиатура для телефонов
-- Кнопка в нижнем правом углу → открывает клаву с буквами/цифрами/F-keys
-- Нажатие на клавишу симулирует KeyCode через VirtualInputManager
-- → срабатывают все бинды скрипта (KeyPickers'ы)
-- ==============================================
do
    local VirtualInputManager = game:GetService("VirtualInputManager")
    local UserInputService = game:GetService("UserInputService")
    local isMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled
    -- Если не мобилка — клава не нужна (но кнопка всё равно доступна)

    -- Состояние
    local mobileKeyboardGui = nil
    local keyboardVisible = false

    -- Маппинг текста клавиши → KeyCode
    local keyMap = {
        -- Буквы
        A = Enum.KeyCode.A, B = Enum.KeyCode.B, C = Enum.KeyCode.C, D = Enum.KeyCode.D,
        E = Enum.KeyCode.E, F = Enum.KeyCode.F, G = Enum.KeyCode.G, H = Enum.KeyCode.H,
        I = Enum.KeyCode.I, J = Enum.KeyCode.J, K = Enum.KeyCode.K, L = Enum.KeyCode.L,
        M = Enum.KeyCode.M, N = Enum.KeyCode.N, O = Enum.KeyCode.O, P = Enum.KeyCode.P,
        Q = Enum.KeyCode.Q, R = Enum.KeyCode.R, S = Enum.KeyCode.S, T = Enum.KeyCode.T,
        U = Enum.KeyCode.U, V = Enum.KeyCode.V, W = Enum.KeyCode.W, X = Enum.KeyCode.X,
        Y = Enum.KeyCode.Y, Z = Enum.KeyCode.Z,
        -- Цифры
        ["1"] = Enum.KeyCode.One, ["2"] = Enum.KeyCode.Two, ["3"] = Enum.KeyCode.Three,
        ["4"] = Enum.KeyCode.Four, ["5"] = Enum.KeyCode.Five, ["6"] = Enum.KeyCode.Six,
        ["7"] = Enum.KeyCode.Seven, ["8"] = Enum.KeyCode.Eight, ["9"] = Enum.KeyCode.Nine,
        ["0"] = Enum.KeyCode.Zero,
        -- F-keys
        F1 = Enum.KeyCode.F1, F2 = Enum.KeyCode.F2, F3 = Enum.KeyCode.F3, F4 = Enum.KeyCode.F4,
        F5 = Enum.KeyCode.F5, F6 = Enum.KeyCode.F6, F7 = Enum.KeyCode.F7, F8 = Enum.KeyCode.F8,
        -- Пробел и Shift
        Space = Enum.KeyCode.Space, Shift = Enum.KeyCode.LeftShift,
        -- Мышь (для биндов на ЛКМ/ПКМ)
        LMB = "MouseButton1", RMB = "MouseButton2",
    }

    -- Порядок клавиш — стандартная QWERTY layout
    -- leftIndent указывает сколько клавиш отступить слева (имитация сдвига ряда как на реальной клаве)
    local rows = {
        { keys = { "1","2","3","4","5","6","7","8","9","0" }, leftIndent = 0 },
        { keys = { "Q","W","E","R","T","Y","U","I","O","P" }, leftIndent = 0 },
        { keys = { "A","S","D","F","G","H","J","K","L" }, leftIndent = 0.5 },  -- сдвиг на пол-клавиши
        { keys = { "Z","X","C","V","B","N","M" }, leftIndent = 1.0 },         -- сдвиг на 1 клавишу
        { keys = { "F1","F2","F3","F4","F5","F6","F7","F8" }, leftIndent = 0 },
        { keys = { "Shift","Space","LMB","RMB" }, leftIndent = 0 },
    }

    -- Симуляция нажатия клавиши (через VirtualInputManager)
    local function pressKey(keyCode)
        pcall(function()
            if keyCode == "MouseButton1" then
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 1)
                task.wait(0.05)
                VirtualInputManager:SendMouseButtonEvent(0, 0, 0, false, game, 1)
            elseif keyCode == "MouseButton2" then
                VirtualInputManager:SendMouseButtonEvent(0, 0, 1, true, game, 1)
                task.wait(0.05)
                VirtualInputManager:SendMouseButtonEvent(0, 0, 1, false, game, 1)
            elseif typeof(keyCode) == "EnumItem" then
                VirtualInputManager:SendKeyEvent(true, keyCode, false, game)
                task.wait(0.05)
                VirtualInputManager:SendKeyEvent(false, keyCode, false, game)
            end
        end)
    end

    -- Создаём GUI с кнопкой-переключателем и клавиатурой
    local function createMobileKeyboard()
        if mobileKeyboardGui then return mobileKeyboardGui end

        local sg = Instance.new("ScreenGui")
        sg.Name = "OblivionMobileKeyboard"
        sg.ResetOnSpawn = false
        sg.IgnoreGuiInset = true
        sg.DisplayOrder = 99999
        sg.Parent = (gethui and gethui()) or game:GetService("CoreGui")

        -- Кнопка-переключатель (внизу справа)
        local toggleBtn = Instance.new("TextButton")
        toggleBtn.Name = "ToggleBtn"
        toggleBtn.Size = UDim2.new(0, 90, 0, 50)
        toggleBtn.Position = UDim2.new(1, -100, 1, -60)
        toggleBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
        toggleBtn.BackgroundTransparency = 0.2
        toggleBtn.BorderSizePixel = 0
        toggleBtn.Text = "⌨"
        toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        toggleBtn.Font = Enum.Font.GothamBold
        toggleBtn.TextScaled = true
        toggleBtn.Parent = sg

        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(0, 8)
        toggleCorner.Parent = toggleBtn

        -- Контейнер клавиатуры (внизу экрана, скрыт по умолчанию)
        local kbFrame = Instance.new("Frame")
        kbFrame.Name = "KeyboardFrame"
        kbFrame.Size = UDim2.new(1, 0, 0, 280)
        kbFrame.Position = UDim2.new(0, 0, 1, 0)  -- скрыт внизу
        kbFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
        kbFrame.BackgroundTransparency = 0.15
        kbFrame.BorderSizePixel = 0
        kbFrame.Visible = false
        kbFrame.Parent = sg

        local kbCorner = Instance.new("UICorner")
        kbCorner.CornerRadius = UDim.new(0, 12)
        kbCorner.Parent = kbFrame

        -- Padding внутри клавиатуры
        local kbPadding = Instance.new("UIPadding")
        kbPadding.PaddingTop = UDim.new(0, 8)
        kbPadding.PaddingBottom = UDim.new(0, 8)
        kbPadding.PaddingLeft = UDim.new(0, 8)
        kbPadding.PaddingRight = UDim.new(0, 8)
        kbPadding.Parent = kbFrame

        -- Размеры клавиш
        local keySize = 40        -- ширина/высота одной клавиши
        local keyGap = 4          -- расстояние между клавишами
        local rowHeight = keySize
        local rowSpacing = 4
        local maxKeysInRow = 10
        local totalRowWidth = maxKeysInRow * keySize + (maxKeysInRow - 1) * keyGap  -- 416

        local yOffset = 0

        for rowIdx, row in ipairs(rows) do
            local keys = row.keys
            local indent = row.leftIndent or 0
            local numKeys = #keys
            local rowWidth = numKeys * keySize + (numKeys - 1) * keyGap
            -- Отступ слева = половина клавиши × indent
            local leftOffsetPx = indent * (keySize + keyGap) / 2

            local rowFrame = Instance.new("Frame")
            rowFrame.Name = "Row" .. rowIdx
            rowFrame.Size = UDim2.new(0, totalRowWidth, 0, rowHeight)
            rowFrame.Position = UDim2.new(0.5, -totalRowWidth/2, 0, yOffset)  -- центр по горизонтали
            rowFrame.BackgroundTransparency = 1
            rowFrame.Parent = kbFrame

            -- Создаём клавиши в ряду вручную (без UIListLayout)
            local currentX = leftOffsetPx  -- X-позиция следующей клавиши
            for i, keyText in ipairs(keys) do
                local keyBtn = Instance.new("TextButton")
                keyBtn.Name = "Key_" .. keyText
                -- Специальная ширина для Space и Shift
                local isWide = (keyText == "Space" or keyText == "Shift")
                local widthMult = isWide and 2.5 or 1
                local keyWidth = keySize * widthMult + (widthMult - 1) * keyGap

                keyBtn.Size = UDim2.new(0, keyWidth, 0, keySize)
                keyBtn.Position = UDim2.new(0, currentX, 0, 0)
                keyBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                keyBtn.BackgroundTransparency = 0.1
                keyBtn.BorderSizePixel = 0
                keyBtn.Text = keyText
                keyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
                keyBtn.Font = Enum.Font.GothamBold
                keyBtn.TextScaled = true
                keyBtn.Parent = rowFrame

                local keyCorner = Instance.new("UICorner")
                keyCorner.CornerRadius = UDim.new(0, 6)
                keyCorner.Parent = keyBtn

                -- Зум при нажатии
                keyBtn.MouseButton1Down:Connect(function()
                    keyBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 120)
                end)
                keyBtn.MouseButton1Up:Connect(function()
                    keyBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                end)
                keyBtn.MouseLeave:Connect(function()
                    keyBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                end)

                -- Нажатие клавиши
                keyBtn.Activated:Connect(function()
                    local keyCode = keyMap[keyText]
                    if keyCode then
                        pressKey(keyCode)
                    end
                end)

                -- Сдвигаем X-позицию на РЕАЛЬНУЮ ширину этой клавиши + gap
                currentX = currentX + keyWidth + keyGap
            end

            yOffset = yOffset + rowHeight + rowSpacing
        end

        kbFrame.Size = UDim2.new(1, 0, 0, yOffset + 16)

        -- Кнопка закрытия (X) на клавиатуре
        local closeBtn = Instance.new("TextButton")
        closeBtn.Name = "CloseBtn"
        closeBtn.Size = UDim2.new(0, 40, 0, 32)
        closeBtn.Position = UDim2.new(1, -48, 0, 8)
        closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        closeBtn.BorderSizePixel = 0
        closeBtn.Text = "✕"
        closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        closeBtn.Font = Enum.Font.GothamBold
        closeBtn.TextScaled = true
        closeBtn.Parent = kbFrame

        local closeCorner = Instance.new("UICorner")
        closeCorner.CornerRadius = UDim.new(0, 6)
        closeCorner.Parent = closeBtn

        -- Анимация открытия/закрытия
        local function setKeyboardVisible(visible)
            keyboardVisible = visible
            kbFrame.Visible = true
            local targetY = visible and (-kbFrame.Size.Y.Offset) or 0
            -- Tween для плавного выезжания
            pcall(function()
                local TweenService = game:GetService("TweenService")
                local tween = TweenService:Create(
                    kbFrame,
                    TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
                    { Position = UDim2.new(0, 0, 1, targetY) }
                )
                tween:Play()
            end)
            -- Fallback если TweenService не сработал
            task.delay(0.3, function()
                if keyboardVisible == visible then
                    kbFrame.Position = UDim2.new(0, 0, 1, targetY)
                end
            end)
            toggleBtn.Text = visible and "✕" or "⌨"
        end

        toggleBtn.Activated:Connect(function()
            setKeyboardVisible(not keyboardVisible)
        end)
        closeBtn.Activated:Connect(function()
            setKeyboardVisible(false)
        end)

        -- Драг кнопки-переключателя (чтобы移动)
        local dragging = false
        local dragStart, startPos
        toggleBtn.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = true
                dragStart = input.Position
                startPos = toggleBtn.Position
            end
        end)
        toggleBtn.InputChanged:Connect(function(input)
            if dragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
                local delta = input.Position - dragStart
                local newPos = UDim2.new(
                    startPos.X.Scale, startPos.X.Offset + delta.X,
                    startPos.Y.Scale, startPos.Y.Offset + delta.Y
                )
                -- Ограничение по экрану
                local x = math.clamp(newPos.X.Offset, 0, sg.AbsoluteSize.X - 90)
                local y = math.clamp(newPos.Y.Offset, 0, sg.AbsoluteSize.Y - 50)
                toggleBtn.Position = UDim2.new(0, x, 0, y)
            end
        end)
        toggleBtn.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = false
            end
        end)

        mobileKeyboardGui = sg
        return sg
    end

    -- Создаём клавиатуру при загрузке скрипта
    pcall(createMobileKeyboard)

    -- Добавляем тумблер в Misc → "Combat & Automation" чтобы можно было скрывать кнопку
    MiscSection:AddToggle("EnableMobileKeyboard", {
        Text = "Mobile Keyboard (кнопка ⌨)",
        Default = isMobile,  -- по умолчанию ВКЛ на мобилках
        Tooltip = "Показать кнопку ⌨ внизу справа — открывает виртуальную клавиатуру для биндов",
    })

    Toggles.EnableMobileKeyboard:OnChanged(function()
        if mobileKeyboardGui then
            local toggleBtn = mobileKeyboardGui:FindFirstChild("ToggleBtn")
            if toggleBtn then
                toggleBtn.Visible = Toggles.EnableMobileKeyboard.Value
            end
            if not Toggles.EnableMobileKeyboard.Value then
                local kbFrame = mobileKeyboardGui:FindFirstChild("KeyboardFrame")
                if kbFrame then kbFrame.Visible = false end
                keyboardVisible = false
            end
        end
    end)

    -- Запускаем начальное состояние
    if Toggles.EnableMobileKeyboard then
        if mobileKeyboardGui then
            local toggleBtn = mobileKeyboardGui:FindFirstChild("ToggleBtn")
            if toggleBtn then
                toggleBtn.Visible = Toggles.EnableMobileKeyboard.Value
            end
        end
    end
end



-- ==============================================
-- FUN: DESTROY HOUSE
-- ==============================================
do
local HouseDestroySection = nil
local ok, err = pcall(function()
  HouseDestroySection = Tabs.Fun:AddRightGroupbox("Destroy Houses")
end)
if not ok then print("[DEBUG] Destroy Houses error: " .. tostring(err)) end
if not HouseDestroySection then print("[DEBUG] HouseDestroySection is nil!") return end

local HD = {}
HD.plotColors = {
    ["Plot1"] = "Зелёный",
    ["Plot2"] = "Красный",
    ["Plot3"] = "Жёлтый",
    ["Plot4"] = "Синий",
    ["Plot5"] = "Фиолетовый",
}
HD.colorToPlot = {}
for k,v in pairs(HD.plotColors) do HD.colorToPlot[v] = k end
HD.colorList = {}
for _, color in pairs(HD.plotColors) do table.insert(HD.colorList, color) end
table.sort(HD.colorList)


HouseDestroySection:AddDropdown("HouseTarget", {
    Text = "Дом",
    Values = HD.colorList,
    Default = HD.colorList[1],
    Multi = false,
    Tooltip = "Какой дом разнести",
    Callback = function() end,
})

HD.getSpawnRF = function()
    local mt = game:GetService("ReplicatedStorage"):FindFirstChild("MenuToys")
    return mt and (mt:FindFirstChild("SpawnToyRemoteFunction") or mt:WaitForChild("SpawnToyRemoteFunction", 5))
end
HD.getDestroyRE = function()
    local mt = game:GetService("ReplicatedStorage"):FindFirstChild("MenuToys")
    return mt and (mt:FindFirstChild("DestroyToy") or mt:WaitForChild("DestroyToy", 5))
end

HouseDestroySection:AddSlider("HouseDestroyDuration", {
    Text = "Длительность разноса (сек)",
    Default = 10,
    Min = 3,
    Max = 60,
    Rounding = 0,
    Compact = false,
    Tooltip = "Сколько секунд decoy будет разносить предметы в доме",
})

HouseDestroySection:AddButton({
    Text = "Разнести дом",
    Tooltip = "Спавнит YouDecoy, забирает ownership и флингает все предметы в выбранном доме",
    Func = function()
        local function destroyPlot(plotName, colorName)
            local spawnRF = HD.getSpawnRF()
            local destroyRE = HD.getDestroyRE()
            local GE = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
            local sno = GE and GE:FindFirstChild("SetNetworkOwner")
            local destroyGL = GE and GE:FindFirstChild("DestroyGrabLine")
            if not spawnRF then return end

            local plot = workspace:FindFirstChild("PlotItems") and workspace.PlotItems:FindFirstChild(plotName)
            if not plot then return end

            local myChar = LocalPlayer.Character
            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end

            local inv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
            if not inv then return end

            for _, old in ipairs(inv:GetChildren()) do
                if old.Name == "YouDecoy" or old.Name == "blablabalbb1" then
                    pcall(function() destroyRE:FireServer(old) end)
                end
            end

            task.spawn(function() pcall(function() spawnRF:InvokeServer("YouDecoy", myHRP.CFrame, Vector3.zero) end) end)
            local clone = nil
            local t0 = tick()
            while not clone and tick() - t0 < 3 do
                clone = inv:FindFirstChild("YouDecoy")
                task.wait(0.05)
            end
            if not clone then return end
            clone.Name = "blablabalbb1"

            local decoyHRP = clone:FindFirstChild("HumanoidRootPart")
            local decoyHead = clone:FindFirstChild("Head")
            if not decoyHRP then return end

            if sno then
                local attempts = 0
                repeat
                    pcall(function() sno:FireServer(decoyHRP, decoyHRP.CFrame) end)
                    task.wait(0.05)
                    attempts = attempts + 1
                until (decoyHead and decoyHead:FindFirstChild("PartOwner") and decoyHead.PartOwner.Value == LocalPlayer.Name) or attempts > 30
            end

            local bodyPos = Instance.new("BodyPosition")
            bodyPos.P = 90000
            bodyPos.D = 100
            bodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyPos.Parent = decoyHRP

            local bodyGyro = Instance.new("BodyGyro")
            bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            bodyGyro.P = 20000
            bodyGyro.D = 200
            bodyGyro.Parent = decoyHRP

            if destroyGL then pcall(function() destroyGL:FireServer(decoyHRP) end) end

            local function flingObject(object)
                if not object or not object.PrimaryPart then return end
                local targetPos = object.PrimaryPart.Position
                -- Летим прямо к предмету (ближе — на 2 studs)
                bodyPos.Position = targetPos + Vector3.new(0, 2, 0)
                -- Ждём пока не приблизимся вплотную (3 studs)
                while decoyHRP and decoyHRP.Parent and object and object.PrimaryPart do
                    local distance = (decoyHRP.Position - object.PrimaryPart.Position).Magnitude
                    if distance < 3 then break end
                    task.wait()
                end
                -- Флингаем
                if decoyHRP and decoyHRP.Parent then
                    local direction = (decoyHRP.Position - object.PrimaryPart.Position).Unit
                    decoyHRP.AssemblyLinearVelocity = direction * 800
                    task.spawn(function()
                        for _ = 1, 15 do
                            task.wait()
                            decoyHRP.AssemblyAngularVelocity = Vector3.new(
                                math.random(-100, 100) * 100,
                                math.random(-100, 100) * 100,
                                math.random(-100, 100) * 100
                            )
                        end
                    end)
                    task.wait(0.3)
                end
            end

            -- Засекаем время и разносим пока не вышло
            local startTime = tick()
            local duration = Options.HouseDestroyDuration and Options.HouseDestroyDuration.Value or 10
            while tick() - startTime < duration do
                local foundAny = false
                for _, v in ipairs(plot:GetChildren()) do
                    if v.Name ~= "RemoveLagToys" and v.Name ~= "ToysLimitNum" and v.PrimaryPart then
                        flingObject(v)
                        foundAny = true
                        if tick() - startTime >= duration then break end
                    end
                end
                if not foundAny then break end
                task.wait(0.1)
            end

            task.wait(2)
            if bodyPos then bodyPos:Destroy() end
            if bodyGyro then bodyGyro:Destroy() end
            pcall(function() destroyRE:FireServer(clone) end)
            Library:Notify("Destroy House: " .. colorName .. " — готово!", 2)
        end

        local colorName = Options.HouseTarget and Options.HouseTarget.Value or "Зелёный"
        local plotName = HD.colorToPlot[colorName] or "Plot1"
        Library:Notify("Destroy House: " .. colorName .. " — погнали!", 2)
        task.spawn(function() destroyPlot(plotName, colorName) end)
    end,
})

HouseDestroySection:AddButton({
    Text = "Разнести все дома",
    Tooltip = "Разносит все 5 домов подряд",
    Func = function()
        local function destroyPlot(plotName, colorName)
            local spawnRF = HD.getSpawnRF()
            local destroyRE = HD.getDestroyRE()
            local GE = game:GetService("ReplicatedStorage"):FindFirstChild("GrabEvents")
            local sno = GE and GE:FindFirstChild("SetNetworkOwner")
            local destroyGL = GE and GE:FindFirstChild("DestroyGrabLine")
            if not spawnRF then return end

            local plot = workspace:FindFirstChild("PlotItems") and workspace.PlotItems:FindFirstChild(plotName)
            if not plot then return end

            local myChar = LocalPlayer.Character
            local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end
            local inv = workspace:FindFirstChild(LocalPlayer.Name .. "SpawnedInToys")
            if not inv then return end

            for _, old in ipairs(inv:GetChildren()) do
                if old.Name == "YouDecoy" or old.Name == "blablabalbb1" then
                    pcall(function() destroyRE:FireServer(old) end)
                end
            end

            task.spawn(function() pcall(function() spawnRF:InvokeServer("YouDecoy", myHRP.CFrame, Vector3.zero) end) end)
            local clone = nil
            local t0 = tick()
            while not clone and tick() - t0 < 3 do
                clone = inv:FindFirstChild("YouDecoy")
                task.wait(0.05)
            end
            if not clone then return end
            clone.Name = "blablabalbb1"

            local decoyHRP = clone:FindFirstChild("HumanoidRootPart")
            local decoyHead = clone:FindFirstChild("Head")
            if not decoyHRP then return end

            if sno then
                local attempts = 0
                repeat
                    pcall(function() sno:FireServer(decoyHRP, decoyHRP.CFrame) end)
                    task.wait(0.05)
                    attempts = attempts + 1
                until (decoyHead and decoyHead:FindFirstChild("PartOwner") and decoyHead.PartOwner.Value == LocalPlayer.Name) or attempts > 30
            end

            local bodyPos = Instance.new("BodyPosition")
            bodyPos.P = 90000
            bodyPos.D = 100
            bodyPos.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            bodyPos.Parent = decoyHRP

            local bodyGyro = Instance.new("BodyGyro")
            bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
            bodyGyro.P = 20000
            bodyGyro.D = 200
            bodyGyro.Parent = decoyHRP

            if destroyGL then pcall(function() destroyGL:FireServer(decoyHRP) end) end

            local function flingObject(object)
                if not object or not object.PrimaryPart then return end
                local targetPos = object.PrimaryPart.Position
                -- Летим прямо к предмету (ближе — на 2 studs)
                bodyPos.Position = targetPos + Vector3.new(0, 2, 0)
                -- Ждём пока не приблизимся вплотную (3 studs)
                while decoyHRP and decoyHRP.Parent and object and object.PrimaryPart do
                    local distance = (decoyHRP.Position - object.PrimaryPart.Position).Magnitude
                    if distance < 3 then break end
                    task.wait()
                end
                -- Флингаем
                if decoyHRP and decoyHRP.Parent then
                    local direction = (decoyHRP.Position - object.PrimaryPart.Position).Unit
                    decoyHRP.AssemblyLinearVelocity = direction * 800
                    task.spawn(function()
                        for _ = 1, 15 do
                            task.wait()
                            decoyHRP.AssemblyAngularVelocity = Vector3.new(
                                math.random(-100, 100) * 100,
                                math.random(-100, 100) * 100,
                                math.random(-100, 100) * 100
                            )
                        end
                    end)
                    task.wait(0.3)
                end
            end

            -- Засекаем время и разносим пока не вышло
            local startTime = tick()
            local duration = Options.HouseDestroyDuration and Options.HouseDestroyDuration.Value or 10
            while tick() - startTime < duration do
                local foundAny = false
                for _, v in ipairs(plot:GetChildren()) do
                    if v.Name ~= "RemoveLagToys" and v.Name ~= "ToysLimitNum" and v.PrimaryPart then
                        flingObject(v)
                        foundAny = true
                        if tick() - startTime >= duration then break end

                    end
                end
                if not foundAny then break end
                task.wait(0.1)
            end

            task.wait(2)
            if bodyPos then bodyPos:Destroy() end
            if bodyGyro then bodyGyro:Destroy() end
            pcall(function() destroyRE:FireServer(clone) end)
            Library:Notify("Destroy House: " .. colorName .. " — готово!", 2)
            task.wait(1)
        end

        task.spawn(function()
            for _, colorName in ipairs(HD.colorList) do
                local plotName = HD.colorToPlot[colorName]
                destroyPlot(plotName, colorName)
            end
            Library:Notify("Все дома разнесены!", 3)
        end)
    end,
})


-- ==============================================
-- FUN: MAP BREAK (SHURIKEN) — из 9rr
-- Спавнит N NinjaShuriken, прилипает их к Body транспорта,
-- спам SetNetworkOwner × 100, удаляет Attachment + AlignPosition/Orientation
-- ==============================================
do
local MapBreakGroup = Tabs.Fun:AddLeftGroupbox("Map Break (Shuriken)")

local MB = {}
MB.RS = game:GetService("ReplicatedStorage")
MB.WS = game:GetService("Workspace")
MB.Players = game:GetService("Players")
MB.LocalPlayer = MB.Players.LocalPlayer

MB.spawnRF  = MB.RS:WaitForChild("MenuToys"):WaitForChild("SpawnToyRemoteFunction", 10)
MB.destroyToy = MB.RS:WaitForChild("MenuToys"):FindFirstChild("DestroyToy") or MB.RS:WaitForChild("MenuToys"):WaitForChild("DestroyToy", 10)
MB.GE = MB.RS:WaitForChild("GrabEvents", 10)
MB.SetNetworkOwner = MB.GE:WaitForChild("SetNetworkOwner", 10)
MB.DestroyGrabLine = MB.GE:WaitForChild("DestroyGrabLine", 10)
MB.StickyPartEvent = MB.RS:WaitForChild("PlayerEvents"):WaitForChild("StickyPartEvent", 10)

-- Ползунок количества сюрикенов
MapBreakGroup:AddSlider("MapBreakShurikenCount", {
    Text = "Количество сюрикенов",
    Default = 10,
    Min = 5,
    Max = 50,
    Rounding = 0,
    Compact = false,
    Tooltip = "Сколько NinjaShuriken спавнить и прилипать к объекту (больше = сильнее разнос)",
})

-- Внутренний хелпер: ищет целевой транспорт в Workspace.Map.AlwaysHereTweenedObjects.<modelName>
MB.findTransport = function(modelName)
    local map = MB.WS:FindFirstChild("Map")
    if not map then return nil end
    local tweened = map:FindFirstChild("AlwaysHereTweenedObjects")
    if not tweened then return nil end
    local m = tweened:FindFirstChild(modelName)
    if not m then return nil end
    return m
end

-- Найти "Body" (для UFO): Object → ObjectModel → Body
MB.findBody = function(transportModel)
    if not transportModel then return nil end
    local obj = transportModel:FindFirstChild("Object")
    if not obj then return nil end
    local om = obj:FindFirstChild("ObjectModel")
    if not om then return nil end
    local body = om:FindFirstChild("Body")
    if body and body:IsA("BasePart") then return body end
    -- fallback: первый BasePart в ObjectModel
    for _, v in ipairs(om:GetChildren()) do
        if v:IsA("BasePart") then return v end
    end
    return nil
end

-- Найти "target" BasePart из ObjectModel по индексу (для CaveCart/Train)
MB.findTargetByIndex = function(transportModel, idxList)
    if not transportModel then return nil end
    local obj = transportModel:FindFirstChild("Object")
    if not obj then return nil end
    local om = obj:FindFirstChild("ObjectModel")
    if not om then return nil end
    local children = om:GetChildren()
    for _, idx in ipairs(idxList) do
        local t = children[idx]
        if t and t:IsA("BasePart") then return t end
    end
    -- fallback: первый BasePart
    for _, v in ipairs(children) do
        if v:IsA("BasePart") then return v end
    end
    return nil
end

-- Универсальная функция: ломаем транспорт по target part
MB.breakTransport = function(modelName, targetPart, label)
    pcall(function()
        local me = MB.LocalPlayer
        local char = me.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not hrp then
            Library:Notify("Map Break: нет HRP", 3)
            return
        end

        local BackPack = MB.WS:FindFirstChild(me.Name .. "SpawnedInToys")
        if not BackPack then
            Library:Notify("Map Break: нет SpawnedInToys папки", 3)
            return
        end

        if not targetPart then
            Library:Notify("Map Break: target не найден (" .. (label or "?") .. ")", 3)
            return
        end

        local count = Options.MapBreakShurikenCount and Options.MapBreakShurikenCount.Value or 10

        -- Спавним сюрикены
        local spawnCFrame = hrp.CFrame * CFrame.new(0, 3, 0)
        for i = 1, count do
            pcall(function()
                MB.spawnRF:InvokeServer("NinjaShuriken", spawnCFrame, Vector3.zero)
            end)
            task.wait(0.05)
        end

        task.wait(1)

        -- Прилипляем каждый сюрикен к target
        for i = 1, count do
            local shur = BackPack:FindFirstChild("NinjaShuriken")
            if shur then
                local stickyPart = shur:FindFirstChild("StickyPart")
                if stickyPart then
                    shur.Name = tostring(i)
                    pcall(function()
                        MB.StickyPartEvent:FireServer(stickyPart, targetPart, CFrame.Angles(0, 0, 0))
                    end)
                end
            end
            task.wait(0.1)
        end

        -- Спам SetNetworkOwner × 100
        for i = 1, 100 do
            pcall(function()
                MB.SetNetworkOwner:FireServer(targetPart, targetPart.CFrame)
            end)
            task.wait()
        end

        -- Удаляем Attachment у target
        local attach = targetPart:FindFirstChild("ObjectModelAttachment")
        if attach then
            pcall(function() attach:Destroy() end)
        end

        -- Снимаем AlignPosition/AlignOrientation у FollowThisPart
        local obj = targetPart.Parent
        if obj and obj.Parent then
            -- target.Parent = ObjectModel, его parent = Object
            local objParent = obj.Parent
            if objParent then
                local followPart = objParent:FindFirstChild("FollowThisPart")
                if followPart then
                    local alignPos = followPart:FindFirstChild("AlignPosition")
                    local alignRot = followPart:FindFirstChild("AlignOrientation")
                    if alignPos then
                        pcall(function() alignPos.Attachment0 = nil end)
                    end
                    if alignRot then
                        pcall(function() alignRot.Attachment0 = nil end)
                    end
                end
            end
        end

        Library:Notify("Map Break: " .. (label or "?") .. " сломан!", 3)
    end)
end

-- Break Outer UFO
MapBreakGroup:AddButton({
    Text = "Break Outer UFO",
    Tooltip = "Спавнит N сюрикенов и прилипает их к OuterUFO Body, ломает через SetNetworkOwner spam",
    Func = function()
        local transport = MB.findTransport("OuterUFO")
        if not transport then
            Library:Notify("Map Break: OuterUFO не найден", 3)
            return
        end
        local body = MB.findBody(transport)
        if not body then
            Library:Notify("Map Break: OuterUFO Body не найден", 3)
            return
        end
        task.spawn(function() MB.breakTransport("OuterUFO", body, "Outer UFO") end)
    end,
})

-- Break Inner UFO
MapBreakGroup:AddButton({
    Text = "Break Inner UFO",
    Tooltip = "Аналогично для InnerUFO",
    Func = function()
        local transport = MB.findTransport("InnerUFO")
        if not transport then
            Library:Notify("Map Break: InnerUFO не найден", 3)
            return
        end
        local body = MB.findBody(transport)
        if not body then
            Library:Notify("Map Break: InnerUFO Body не найден", 3)
            return
        end
        task.spawn(function() MB.breakTransport("InnerUFO", body, "Inner UFO") end)
    end,
})

-- Break CaveCart
MapBreakGroup:AddButton({
    Text = "Break CaveCart",
    Tooltip = "Ломает CaveCart, target = children[13] из ObjectModel",
    Func = function()
        local transport = MB.findTransport("CaveCart")
        if not transport then
            Library:Notify("Map Break: CaveCart не найден", 3)
            return
        end
        local target = MB.findTargetByIndex(transport, {13, 2, 3, 1})
        if not target then
            Library:Notify("Map Break: CaveCart target не найден", 3)
            return
        end
        task.spawn(function() MB.breakTransport("CaveCart", target, "CaveCart") end)
    end,
})

-- Break Train
MapBreakGroup:AddButton({
    Text = "Break Train",
    Tooltip = "Ломает Train, target = children[2]/3/1 из ObjectModel",
    Func = function()
        local transport = MB.findTransport("Train")
        if not transport then
            Library:Notify("Map Break: Train не найден", 3)
            return
        end
        local target = MB.findTargetByIndex(transport, {2, 3, 1})
        if not target then
            Library:Notify("Map Break: Train target не найден", 3)
            return
        end
        task.spawn(function() MB.breakTransport("Train", target, "Train") end)
    end,
})

-- Break All (по очереди все 4)
MapBreakGroup:AddButton({
    Text = "Break All Transports",
    Tooltip = "Запускает по очереди все 4 транспорта: Outer UFO → Inner UFO → CaveCart → Train",
    Func = function()
        task.spawn(function()
            local sequence = {
                {name = "OuterUFO", body = true,  idx = nil, label = "Outer UFO"},
                {name = "InnerUFO", body = true,  idx = nil, label = "Inner UFO"},
                {name = "CaveCart", body = false, idx = {13, 2, 3, 1}, label = "CaveCart"},
                {name = "Train",    body = false, idx = {2, 3, 1},    label = "Train"},
            }
            for _, item in ipairs(sequence) do
                local transport = MB.findTransport(item.name)
                if transport then
                    local target = nil
                    if item.body then
                        target = MB.findBody(transport)
                    else
                        target = MB.findTargetByIndex(transport, item.idx)
                    end
                    if target then
                        Library:Notify("Map Break: ломаем " .. item.label, 2)
                        MB.breakTransport(item.name, target, item.label)
                        task.wait(2)
                    else
                        Library:Notify("Map Break: " .. item.label .. " — target не найден, пропуск", 2)
                    end
                else
                    Library:Notify("Map Break: " .. item.label .. " не найден, пропуск", 2)
                end
            end
            Library:Notify("Map Break: все транспорты обработаны!", 3)
        end)
    end,
})
end



-- ==============================================
-- FUN: BREAK SAFEZONE — 1 кнопка: Destroy Barrier (snowball) + Anti Barrier
-- ==============================================
do
local BreakSZGroup = Tabs.Fun:AddRightGroupbox("Break SafeZone")

local BSZ = {}
BSZ.RS = game:GetService("ReplicatedStorage")
BSZ.WS = game:GetService("Workspace")
BSZ.Players = game:GetService("Players")
BSZ.LocalPlayer = BSZ.Players.LocalPlayer

BSZ.MenuToys = BSZ.RS:WaitForChild("MenuToys", 10)
BSZ.spawnRF  = BSZ.MenuToys:WaitForChild("SpawnToyRemoteFunction", 10)
BSZ.destroyToy = BSZ.MenuToys:FindFirstChild("DestroyToy") or BSZ.MenuToys:WaitForChild("DestroyToy", 10)
BSZ.GE = BSZ.RS:WaitForChild("GrabEvents", 10)
BSZ.SetNetworkOwner = BSZ.GE:WaitForChild("SetNetworkOwner", 10)
BSZ.DestroyGrabLine = BSZ.GE:WaitForChild("DestroyGrabLine", 10)

-- ============ Вспомогательные функции ============

-- Получить все снежки в SpawnedInToys
local function getSnowballs()
    local snowballs = {}
    local folder = BSZ.WS:FindFirstChild(BSZ.LocalPlayer.Name .. "SpawnedInToys")
    if folder then
        for _, toy in ipairs(folder:GetChildren()) do
            if toy.Name == "BallSnowball" then
                table.insert(snowballs, toy)
            end
        end
    end
    return snowballs
end

-- Заспавнить снежок прямо у барьера (а не на игроке — иначе не долетит)
local function spawnSnowball()
    -- Координаты барьера (как в 9rr)
    local initialCFrame = CFrame.new(263.5, -4.5, 486.9)
    task.spawn(function()
        pcall(function()
            BSZ.spawnRF:InvokeServer("BallSnowball", initialCFrame, Vector3.new(0, -120.21099853515625, 0))
        end)
    end)
end

-- Забрать ownership снежка
local function processSnowball(snowball)
    local soundPart = snowball:FindFirstChild("SoundPart")
    if soundPart then
        task.spawn(function()
            pcall(function()
                BSZ.SetNetworkOwner:FireServer(soundPart, soundPart.CFrame)
            end)
        end)
    end
end

-- Телепорт снежков к цели (к барьеру)
local function teleportSnowballs(snowballs)
    local targetCFrame = CFrame.new(264.5792541503906, -5.477070331573486, 433.4557800292969)
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

-- Тест печкой: если OvenDarkGray остался — барьер сломан
local function testWithOven()
    local targetPosition = CFrame.new(242.66055297851562, -9.196549415588379, 444.3758850097656)
    pcall(function()
        BSZ.spawnRF:InvokeServer("OvenDarkGray", targetPosition, Vector3.new(0, -74.0790023803711, 0))
    end)
    task.wait(0.5)
    local folder = BSZ.WS:FindFirstChild(BSZ.LocalPlayer.Name .. "SpawnedInToys")
    if not folder then return false end
    for _, child in ipairs(folder:GetChildren()) do
        if child.Name == "OvenDarkGray" and child:GetAttribute("AtSpawned") == nil then
            return true
        end
    end
    return false
end

-- Удалить все снежки и печки
local function cleanupToys()
    local folder = BSZ.WS:FindFirstChild(BSZ.LocalPlayer.Name .. "SpawnedInToys")
    if folder then
        for _, item in ipairs(folder:GetChildren()) do
            if item.Name == "BallSnowball" or item.Name == "OvenDarkGray" then
                pcall(function() BSZ.destroyToy:FireServer(item) end)
            end
        end
    end
end

-- Anti Barrier: снять CanCollide со всех PlotBarrier
local function setAntiBarrier(val)
    local plots = BSZ.WS:FindFirstChild("Plots")
    if not plots then return end
    for _, plot in ipairs(plots:GetChildren()) do
        local barrierModel = plot:FindFirstChild("Barrier")
        if barrierModel then
            for _, part in ipairs(barrierModel:GetChildren()) do
                if part:IsA("BasePart") and part.Name == "PlotBarrier" then
                    part.CanCollide = not val
                end
            end
        end
    end
end

-- ============ 1 КНОПКА: Break SafeZone (снежки + Anti Barrier) ============
BreakSZGroup:AddButton({
    Text = "Break SafeZone",
    Tooltip = "Спам снежками по барьеру + снимает CanCollide (Anti Barrier). Слэм из 9rr",
    Func = function()
        task.spawn(function()
            Library:Notify("Break SZ: ломаем барьер...", 2)

            -- 1) Сразу включаем Anti Barrier (проходим сквозь барьеры)
            setAntiBarrier(true)

            -- 2) Destroy Barrier (snowball) — пытаемся сломать барьер снежками
            local startTime = tick()
            local broken = false
            while (tick() - startTime) < 15 and not broken do
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

                    if testWithOven() then
                        broken = true
                    else
                        task.wait(0.05)
                    end
                end

                task.wait(0.05)
            end

            -- Чистим снежки/печки
            cleanupToys()

            if broken then
                Library:Notify("Break SZ: барьер сломан + Anti Barrier ВКЛ!", 3)
            else
                Library:Notify("Break SZ: Anti Barrier ВКЛ (снежки не успели, но проходить можно)", 3)
            end
        end)
    end,
})
end

end
end
end
end
end