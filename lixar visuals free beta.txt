--lixar visuals tgk lixarvisuals

local Player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local Workspace = game:GetService("Workspace")
local Tween = game:GetService("TweenService")

pcall(function() if game.CoreGui:FindFirstChild("LixarVisualsFree") then game.CoreGui.LixarVisualsFree:Destroy() end end)
for _, e in pairs(Lighting:GetChildren()) do if e:IsA("PostEffect") and e.Name:find("Lixar") then e:Destroy() end end

local State = {
    ESP = false, Fullbright = false, NoFog = true, ThirdPerson = false,
    FOV = 70, SpeedHack = false, WalkSpeed = 100, Fly = false, FlySpeed = 50,
    NoClip = false, InfiniteJump = false, JumpPower = 50, AntiAFK = true,
    GodMode = false, Aimbot = false, AimbotHold = false, SpinBot = false,
    SpinSpeed = 20, AntiStun = true, Shader = "None",
    AngelWings = false, DragonWings = false, ButterflyWings = false,
    TopHat = false, WizardHat = false, CatEars = false, Crown = false,
    Glasses = false, Monocle = false, Halo = false, Aura = false,
    Trail = false, Sparkles = false, FireTrail = false
}

local Orig = {
    Brightness = Lighting.Brightness, FogEnd = Lighting.FogEnd,
    FogStart = Lighting.FogStart, GlobalShadows = Lighting.GlobalShadows,
    Ambient = Lighting.Ambient, OutdoorAmbient = Lighting.OutdoorAmbient,
    ClockTime = Lighting.ClockTime, FogColor = Lighting.FogColor,
    ExposureCompensation = Lighting.ExposureCompensation,
    ColorShift_Top = Lighting.ColorShift_Top,
    ColorShift_Bottom = Lighting.ColorShift_Bottom
}

local Accessories = {}
local AccessoryConnections = {}

local function RemoveAccessory(name)
    if AccessoryConnections[name] then
        AccessoryConnections[name]:Disconnect()
        AccessoryConnections[name] = nil
    end
    if Accessories[name] then
        pcall(function() Accessories[name]:Destroy() end)
        Accessories[name] = nil
    end
end

local function GetCharacter()
    local char = Player.Character
    if not char then return nil end
    return char
end

-- ============================================
-- АНГЕЛЬСКИЕ КРЫЛЬЯ (ПОЛНОСТЬЮ ИСПРАВЛЕННЫЕ)
-- ============================================
local function CreateAngelWings()
    RemoveAccessory("AngelWings")
    
    spawn(function()
        while State.AngelWings do
            local char = GetCharacter()
            if char then
                local torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso")
                if torso and not Accessories["AngelWings"] then
                    -- Создаем модель
                    local model = Instance.new("Model")
                    model.Name = "AngelWings"
                    
                    -- Создаем части для каждого крыла
                    for side = 1, 2 do
                        local s = side == 1 and 1 or -1
                        
                        -- 5 рядов перьев
                        for row = 1, 5 do
                            local featherCount = 8 - row
                            for i = 1, featherCount do
                                local feather = Instance.new("Part")
                                feather.Size = Vector3.new(0.1 + row * 0.04, 1.6 - row * 0.2, 0.03)
                                feather.Color = Color3.fromRGB(250 - row * 20, 250 - row * 20, 255)
                                feather.Material = Enum.Material.SmoothPlastic
                                feather.Transparency = 0.08 + row * 0.03
                                feather.Anchored = false
                                feather.CanCollide = false
                                feather.Name = "Feather"
                                feather.Parent = model
                                
                                -- Позиция относительно туловища
                                local angle = (i - featherCount/2) * 0.2
                                local attachPos = torso.CFrame * CFrame.new(
                                    s * (0.6 + row * 0.25),
                                    0.1 + row * 0.2 + (i - featherCount/2) * 0.12,
                                    -0.3
                                ) * CFrame.Angles(0, math.rad(s * 40 + angle * 25), math.rad(s * 10))
                                
                                local weld = Instance.new("Weld")
                                weld.Part0 = torso
                                weld.Part1 = feather
                                weld.C0 = torso.CFrame:ToObjectSpace(CFrame.new(feather.Position))
                                weld.C1 = CFrame.new()
                                weld.Parent = feather
                                
                                feather.Position = attachPos.Position
                                feather.Orientation = Vector3.new(0, s * 40 + angle * 20, s * 10)
                            end
                        end
                    end
                    
                    model.Parent = char
                    Accessories["AngelWings"] = model
                    
                    -- Анимация крыльев
                    local connection = RunService.RenderStepped:Connect(function()
                        if not State.AngelWings or not Accessories["AngelWings"] or not Accessories["AngelWings"].Parent then
                            AccessoryConnections["AngelWings"]:Disconnect()
                            return
                        end
                        local t = tick()
                        local modelRef = Accessories["AngelWings"]
                        if modelRef and modelRef.Parent then
                            for _, feather in pairs(modelRef:GetChildren()) do
                                if feather:IsA("Part") and feather.Name == "Feather" then
                                    local weld = feather:FindFirstChildOfClass("Weld")
                                    if weld then
                                        feather.Orientation = feather.Orientation + Vector3.new(
                                            math.sin(t * 3 + feather.Position.Y) * 0.3,
                                            math.cos(t * 2.5 + feather.Position.X) * 0.2,
                                            0
                                        )
                                    end
                                end
                            end
                        end
                    end)
                    AccessoryConnections["AngelWings"] = connection
                    break
                end
            end
            wait(0.1)
        end
        -- Очистка при выключении
        RemoveAccessory("AngelWings")
    end)
end

-- ============================================
-- ДРАКОНЬИ КРЫЛЬЯ (ПОЛНОСТЬЮ ИСПРАВЛЕННЫЕ)
-- ============================================
local function CreateDragonWings()
    RemoveAccessory("DragonWings")
    
    spawn(function()
        while State.DragonWings do
            local char = GetCharacter()
            if char then
                local torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso")
                if torso and not Accessories["DragonWings"] then
                    local model = Instance.new("Model")
                    model.Name = "DragonWings"
                    
                    for side = 1, 2 do
                        local s = side == 1 and 1 or -1
                        
                        -- Создаем 3 кости для каждого крыла
                        local bones = {}
                        for bone = 1, 3 do
                            local bonePart = Instance.new("Part")
                            bonePart.Size = Vector3.new(0.12, 0.12, 1.5 - bone * 0.2)
                            bonePart.Color = Color3.fromRGB(50 + bone * 10, 10, 10)
                            bonePart.Material = Enum.Material.Metal
                            bonePart.Anchored = false
                            bonePart.CanCollide = false
                            bonePart.Name = "Bone"
                            bonePart.Parent = model
                            
                            local angle = (bone - 2) * 25
                            local pos = torso.CFrame * CFrame.new(s * 0.5, 0.2, -0.1) * CFrame.Angles(0, math.rad(s * 35 + angle), math.rad(angle * 0.5))
                            bonePart.Position = pos.Position
                            bonePart.Orientation = Vector3.new(0, s * 35 + angle, angle * 0.5)
                            
                            local weld = Instance.new("Weld")
                            weld.Part0 = torso
                            weld.Part1 = bonePart
                            weld.C0 = torso.CFrame:ToObjectSpace(CFrame.new(bonePart.Position))
                            weld.C1 = CFrame.new()
                            weld.Parent = bonePart
                            
                            bones[bone] = bonePart
                        end
                        
                        -- Мембраны между костями
                        for bone = 1, 2 do
                            local membrane = Instance.new("Part")
                            membrane.Size = Vector3.new(0.04, 1.2, 1.0)
                            membrane.Color = Color3.fromRGB(70, 15, 15)
                            membrane.Material = Enum.Material.SmoothPlastic
                            membrane.Transparency = 0.35
                            membrane.Anchored = false
                            membrane.CanCollide = false
                            membrane.Name = "Membrane"
                            membrane.Parent = model
                            
                            local midPos = (bones[bone].Position + bones[bone + 1].Position) / 2
                            membrane.Position = midPos + Vector3.new(s * 0.2, 0, 0)
                            membrane.Orientation = Vector3.new(0, s * 40, 0)
                            
                            local weld = Instance.new("Weld")
                            weld.Part0 = bones[bone]
                            weld.Part1 = membrane
                            weld.C0 = bones[bone].CFrame:ToObjectSpace(CFrame.new(membrane.Position))
                            weld.C1 = CFrame.new()
                            weld.Parent = membrane
                        end
                    end
                    
                    model.Parent = char
                    Accessories["DragonWings"] = model
                    
                    -- Анимация
                    local connection = RunService.RenderStepped:Connect(function()
                        if not State.DragonWings or not Accessories["DragonWings"] or not Accessories["DragonWings"].Parent then
                            AccessoryConnections["DragonWings"]:Disconnect()
                            return
                        end
                        local t = tick()
                        local modelRef = Accessories["DragonWings"]
                        if modelRef and modelRef.Parent then
                            for _, part in pairs(modelRef:GetChildren()) do
                                if part:IsA("Part") and part.Name == "Bone" then
                                    part.Orientation = part.Orientation + Vector3.new(0, 0, math.sin(t * 5) * 0.3)
                                elseif part:IsA("Part") and part.Name == "Membrane" then
                                    local weld = part:FindFirstChildOfClass("Weld")
                                    if weld then
                                        part.Transparency = 0.3 + math.sin(t * 4) * 0.05
                                    end
                                end
                            end
                        end
                    end)
                    AccessoryConnections["DragonWings"] = connection
                    break
                end
            end
            wait(0.1)
        end
        RemoveAccessory("DragonWings")
    end)
end

-- ============================================
-- КРЫЛЬЯ БАБОЧКИ (ПОЛНОСТЬЮ ИСПРАВЛЕННЫЕ)
-- ============================================
local function CreateButterflyWings()
    RemoveAccessory("ButterflyWings")
    
    spawn(function()
        while State.ButterflyWings do
            local char = GetCharacter()
            if char then
                local torso = char:FindFirstChild("UpperTorso") or char:FindFirstChild("Torso")
                if torso and not Accessories["ButterflyWings"] then
                    local model = Instance.new("Model")
                    model.Name = "ButterflyWings"
                    
                    local wingColors = {
                        Color3.fromRGB(255, 150, 200),
                        Color3.fromRGB(150, 200, 255),
                        Color3.fromRGB(255, 200, 150),
                        Color3.fromRGB(200, 150, 255)
                    }
                    
                    for side = 1, 2 do
                        local s = side == 1 and 1 or -1
                        
                        -- 2 крыла с каждой стороны (верхнее и нижнее)
                        for wing = 1, 2 do
                            local wingPart = Instance.new("Part")
                            wingPart.Size = Vector3.new(0.03, 1.6 - wing * 0.4, 1.0 - wing * 0.2)
                            wingPart.Color = wingColors[(side - 1) * 2 + wing]
                            wingPart.Material = Enum.Material.SmoothPlastic
                            wingPart.Transparency = 0.3
                            wingPart.Anchored = false
                            wingPart.CanCollide = false
                            wingPart.Name = "Wing"
                            wingPart.Parent = model
                            
                            local pos = torso.CFrame * CFrame.new(
                                s * (0.3 + wing * 0.15),
                                0.5 - wing * 0.3,
                                -0.15
                            ) * CFrame.Angles(0, math.rad(s * 50), math.rad(s * 10))
                            
                            wingPart.Position = pos.Position
                            wingPart.Orientation = Vector3.new(0, s * 50, s * 10)
                            
                            local weld = Instance.new("Weld")
                            weld.Part0 = torso
                            weld.Part1 = wingPart
                            weld.C0 = torso.CFrame:ToObjectSpace(CFrame.new(wingPart.Position))
                            weld.C1 = CFrame.new()
                            weld.Parent = wingPart
                            
                            -- Узоры на крыле
                            for i = 1, 4 do
                                local dot = Instance.new("Part")
                                dot.Size = Vector3.new(0.1, 0.1, 0.01)
                                dot.Shape = Enum.PartType.Ball
                                dot.Color = Color3.fromRGB(255, 255, 255)
                                dot.Material = Enum.Material.Neon
                                dot.Transparency = 0.4
                                dot.Anchored = false
                                dot.CanCollide = false
                                dot.Name = "Dot"
                                dot.Parent = model
                                
                                local dotPos = wingPart.CFrame * CFrame.new(
                                    math.random(-20, 20) * 0.015,
                                    math.random(-40, 40) * 0.015,
                                    0.02
                                )
                                dot.Position = dotPos.Position
                                
                                local dotWeld = Instance.new("Weld")
                                dotWeld.Part0 = wingPart
                                dotWeld.Part1 = dot
                                dotWeld.C0 = wingPart.CFrame:ToObjectSpace(CFrame.new(dot.Position))
                                dotWeld.C1 = CFrame.new()
                                dotWeld.Parent = dot
                            end
                        end
                    end
                    
                    model.Parent = char
                    Accessories["ButterflyWings"] = model
                    
                    -- Анимация
                    local connection = RunService.RenderStepped:Connect(function()
                        if not State.ButterflyWings or not Accessories["ButterflyWings"] or not Accessories["ButterflyWings"].Parent then
                            AccessoryConnections["ButterflyWings"]:Disconnect()
                            return
                        end
                        local t = tick()
                        local modelRef = Accessories["ButterflyWings"]
                        if modelRef and modelRef.Parent then
                            for _, part in pairs(modelRef:GetChildren()) do
                                if part:IsA("Part") and part.Name == "Wing" then
                                    part.Orientation = part.Orientation + Vector3.new(
                                        math.sin(t * 7) * 0.4,
                                        math.cos(t * 5) * 0.2,
                                        math.sin(t * 3) * 0.3
                                    )
                                elseif part:IsA("Part") and part.Name == "Dot" then
                                    part.Transparency = 0.3 + math.sin(t * 6 + part.Position.Y) * 0.2
                                end
                            end
                        end
                    end)
                    AccessoryConnections["ButterflyWings"] = connection
                    break
                end
            end
            wait(0.1)
        end
        RemoveAccessory("ButterflyWings")
    end)
end

-- ===== ГОЛОВНЫЕ УБОРЫ (ИСПРАВЛЕННЫЕ) =====
local function CreateTopHat()
    RemoveAccessory("TopHat")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "TopHat"
    
    local brim = Instance.new("Part")
    brim.Size = Vector3.new(1.8, 0.15, 1.8); brim.Color = Color3.fromRGB(15, 15, 20)
    brim.Material = Enum.Material.SmoothPlastic; brim.Anchored = false; brim.CanCollide = false
    brim.Position = head.Position + Vector3.new(0, 0.8, 0); brim.Parent = model
    local w1 = Instance.new("Weld", brim); w1.Part0 = head; w1.Part1 = brim
    w1.C0 = head.CFrame:ToObjectSpace(CFrame.new(brim.Position))
    
    local top = Instance.new("Part")
    top.Size = Vector3.new(1.1, 1.1, 1.1); top.Color = Color3.fromRGB(25, 25, 30)
    top.Material = Enum.Material.SmoothPlastic; top.Anchored = false; top.CanCollide = false
    top.Position = brim.Position + Vector3.new(0, 0.6, 0); top.Parent = model
    local w2 = Instance.new("Weld", top); w2.Part0 = brim; w2.Part1 = top
    w2.C0 = brim.CFrame:ToObjectSpace(CFrame.new(top.Position))
    
    local ribbon = Instance.new("Part")
    ribbon.Size = Vector3.new(1.15, 0.3, 1.15); ribbon.Color = Color3.fromRGB(255, 180, 40)
    ribbon.Material = Enum.Material.SmoothPlastic; ribbon.Anchored = false; ribbon.CanCollide = false
    ribbon.Position = top.Position + Vector3.new(0, -0.25, 0); ribbon.Parent = model
    local w3 = Instance.new("Weld", ribbon); w3.Part0 = top; w3.Part1 = ribbon
    w3.C0 = top.CFrame:ToObjectSpace(CFrame.new(ribbon.Position))
    
    model.Parent = char
    Accessories["TopHat"] = model
end

local function CreateWizardHat()
    RemoveAccessory("WizardHat")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "WizardHat"
    
    local base = Instance.new("Part")
    base.Size = Vector3.new(1.6, 0.3, 1.6); base.Color = Color3.fromRGB(25, 15, 50)
    base.Material = Enum.Material.SmoothPlastic; base.Anchored = false; base.CanCollide = false
    base.Position = head.Position + Vector3.new(0, 0.8, 0); base.Parent = model
    Instance.new("Weld", base).Part0 = head; base.Part1 = base
    base:FindFirstChildOfClass("Weld").C0 = head.CFrame:ToObjectSpace(CFrame.new(base.Position))
    
    local cone = Instance.new("Part")
    cone.Size = Vector3.new(0.7, 2.2, 0.7); cone.Color = Color3.fromRGB(35, 20, 70)
    cone.Material = Enum.Material.SmoothPlastic; cone.Anchored = false; cone.CanCollide = false
    cone.Position = base.Position + Vector3.new(0, 1.2, 0); cone.Parent = model
    local w = Instance.new("Weld", cone); w.Part0 = base; w.Part1 = cone
    w.C0 = base.CFrame:ToObjectSpace(CFrame.new(cone.Position))
    
    for i = 1, 6 do
        local angle = (i / 6) * math.pi * 2
        local star = Instance.new("Part")
        star.Size = Vector3.new(0.15, 0.15, 0.04); star.Color = Color3.fromRGB(255, 255, 150)
        star.Material = Enum.Material.Neon; star.Anchored = false; star.CanCollide = false
        star.Name = "Star"; star.Parent = model
        star.Position = cone.Position + Vector3.new(math.cos(angle) * 0.35, (i - 1) * 0.3 - 0.5, math.sin(angle) * 0.35)
        local ws = Instance.new("Weld", star); ws.Part0 = cone; ws.Part1 = star
        ws.C0 = cone.CFrame:ToObjectSpace(CFrame.new(star.Position))
    end
    
    model.Parent = char
    Accessories["WizardHat"] = model
    
    local connection = RunService.RenderStepped:Connect(function()
        if not Accessories["WizardHat"] or not Accessories["WizardHat"].Parent then
            if AccessoryConnections["WizardHat"] then AccessoryConnections["WizardHat"]:Disconnect() end
            return
        end
        for _, star in pairs(Accessories["WizardHat"]:GetDescendants()) do
            if star:IsA("Part") and star.Name == "Star" then
                star.Color = Color3.fromHSV((tick() * 30 + star.Position.Y * 100) % 360 / 360, 0.3, 1)
            end
        end
    end)
    AccessoryConnections["WizardHat"] = connection
end

local function CreateCatEars()
    RemoveAccessory("CatEars")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "CatEars"
    
    for side = 1, 2 do
        local s = side == 1 and 1 or -1
        
        local inner = Instance.new("Part")
        inner.Size = Vector3.new(0.35, 0.6, 0.08); inner.Color = Color3.fromRGB(255, 170, 190)
        inner.Material = Enum.Material.SmoothPlastic; inner.Anchored = false; inner.CanCollide = false
        inner.Position = head.Position + Vector3.new(s * 0.45, 1.3, -0.05)
        inner.Orientation = Vector3.new(0, 0, s * 15); inner.Parent = model
        local wi = Instance.new("Weld", inner); wi.Part0 = head; wi.Part1 = inner
        wi.C0 = head.CFrame:ToObjectSpace(CFrame.new(inner.Position) * CFrame.Angles(0, 0, math.rad(s * 15)))
        
        local outer = Instance.new("Part")
        outer.Size = Vector3.new(0.45, 0.7, 0.12); outer.Color = Color3.fromRGB(50, 50, 60)
        outer.Material = Enum.Material.SmoothPlastic; outer.Anchored = false; outer.CanCollide = false
        outer.Position = inner.Position + Vector3.new(0, 0, -0.02); outer.Parent = model
        local wo = Instance.new("Weld", outer); wo.Part0 = inner; wo.Part1 = outer
        wo.C0 = inner.CFrame:ToObjectSpace(CFrame.new(outer.Position))
    end
    
    model.Parent = char
    Accessories["CatEars"] = model
end

local function CreateCrown()
    RemoveAccessory("Crown")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "RoyalCrown"
    
    local base = Instance.new("Part")
    base.Size = Vector3.new(1.4, 0.35, 1.4); base.Color = Color3.fromRGB(255, 215, 0)
    base.Material = Enum.Material.Metal; base.Anchored = false; base.CanCollide = false
    base.Position = head.Position + Vector3.new(0, 1.1, 0); base.Parent = model
    Instance.new("Weld", base).Part0 = head; base.Part1 = base
    base:FindFirstChildOfClass("Weld").C0 = head.CFrame:ToObjectSpace(CFrame.new(base.Position))
    
    for i = 1, 5 do
        local angle = (i - 3) * 0.3
        local spike = Instance.new("Part")
        spike.Size = Vector3.new(0.15, 0.7, 0.15); spike.Color = Color3.fromRGB(255, 215, 0)
        spike.Material = Enum.Material.Metal; spike.Anchored = false; spike.CanCollide = false
        spike.Position = base.Position + Vector3.new(math.sin(angle) * 0.65, 0.5, math.cos(angle) * 0.65)
        spike.Parent = model
        local ws = Instance.new("Weld", spike); ws.Part0 = base; ws.Part1 = spike
        ws.C0 = base.CFrame:ToObjectSpace(CFrame.new(spike.Position))
        
        local gem = Instance.new("Part")
        gem.Size = Vector3.new(0.12, 0.12, 0.12); gem.Shape = Enum.PartType.Ball
        gem.Color = Color3.fromHSV(i / 5, 0.9, 1); gem.Material = Enum.Material.Neon
        gem.Name = "Gem"; gem.Anchored = false; gem.CanCollide = false
        gem.Position = spike.Position + Vector3.new(0, 0.35, 0); gem.Parent = model
        local wg = Instance.new("Weld", gem); wg.Part0 = spike; wg.Part1 = gem
        wg.C0 = spike.CFrame:ToObjectSpace(CFrame.new(gem.Position))
    end
    
    model.Parent = char
    Accessories["Crown"] = model
    
    local connection = RunService.RenderStepped:Connect(function()
        if not Accessories["Crown"] or not Accessories["Crown"].Parent then
            if AccessoryConnections["Crown"] then AccessoryConnections["Crown"]:Disconnect() end
            return
        end
        for _, gem in pairs(Accessories["Crown"]:GetDescendants()) do
            if gem:IsA("Part") and gem.Name == "Gem" then
                gem.Transparency = 0.1 + math.sin(tick() * 4 + gem.Position.Y) * 0.1
            end
        end
    end)
    AccessoryConnections["Crown"] = connection
end

local function CreateHalo()
    RemoveAccessory("Halo")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "Halo"
    
    local segments = 16
    local radius = 0.8
    for i = 1, segments do
        local angle = (i / segments) * math.pi * 2
        local seg = Instance.new("Part")
        seg.Size = Vector3.new(0.25, 0.08, 0.08); seg.Color = Color3.fromRGB(255, 255, 220)
        seg.Material = Enum.Material.Neon; seg.Transparency = 0.3
        seg.Name = "Segment"; seg.Anchored = false; seg.CanCollide = false
        seg.Position = head.Position + Vector3.new(math.cos(angle) * radius, 1.6, math.sin(angle) * radius)
        seg.Orientation = Vector3.new(0, math.deg(angle) + 90, 0)
        seg.Parent = model
        local w = Instance.new("Weld", seg); w.Part0 = head; w.Part1 = seg
        w.C0 = head.CFrame:ToObjectSpace(CFrame.new(seg.Position) * CFrame.Angles(0, math.rad(math.deg(angle) + 90), 0))
    end
    
    model.Parent = char
    Accessories["Halo"] = model
    
    local connection = RunService.RenderStepped:Connect(function()
        if not Accessories["Halo"] or not Accessories["Halo"].Parent then
            if AccessoryConnections["Halo"] then AccessoryConnections["Halo"]:Disconnect() end
            return
        end
        for _, seg in pairs(Accessories["Halo"]:GetDescendants()) do
            if seg:IsA("Part") and seg.Name == "Segment" then
                seg.Color = Color3.fromHSV((tick() * 20 + seg.Position.X * 50) % 360 / 360, 0.2, 1)
                seg.Transparency = 0.2 + math.sin(tick() * 4 + seg.Position.Z) * 0.15
            end
        end
    end)
    AccessoryConnections["Halo"] = connection
end

local function CreateGlasses()
    RemoveAccessory("Glasses")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "StylishGlasses"
    
    for i = 1, 2 do
        local s = i == 1 and 1 or -1
        local lens = Instance.new("Part")
        lens.Size = Vector3.new(0.4, 0.5, 0.06); lens.Color = Color3.fromRGB(15, 20, 25)
        lens.Material = Enum.Material.Glass; lens.Transparency = 0.45
        lens.Anchored = false; lens.CanCollide = false
        lens.Position = head.Position + Vector3.new(s * 0.38, 0.2, -0.8)
        lens.Parent = model
        local wl = Instance.new("Weld", lens); wl.Part0 = head; wl.Part1 = lens
        wl.C0 = head.CFrame:ToObjectSpace(CFrame.new(lens.Position))
        
        local arm = Instance.new("Part")
        arm.Size = Vector3.new(0.06, 0.06, 0.7); arm.Color = Color3.fromRGB(10, 10, 15)
        arm.Material = Enum.Material.Metal; arm.Anchored = false; arm.CanCollide = false
        arm.Position = lens.Position + Vector3.new(0, 0, -0.35)
        arm.Parent = model
        local wa = Instance.new("Weld", arm); wa.Part0 = lens; wa.Part1 = arm
        wa.C0 = lens.CFrame:ToObjectSpace(CFrame.new(arm.Position))
    end
    
    local bridge = Instance.new("Part")
    bridge.Size = Vector3.new(0.7, 0.08, 0.08); bridge.Color = Color3.fromRGB(10, 10, 15)
    bridge.Material = Enum.Material.Metal; bridge.Anchored = false; bridge.CanCollide = false
    bridge.Position = head.Position + Vector3.new(0, 0.2, -0.77)
    bridge.Parent = model
    local wb = Instance.new("Weld", bridge); wb.Part0 = head; wb.Part1 = bridge
    wb.C0 = head.CFrame:ToObjectSpace(CFrame.new(bridge.Position))
    
    model.Parent = char
    Accessories["Glasses"] = model
end

local function CreateMonocle()
    RemoveAccessory("Monocle")
    local char = GetCharacter()
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end
    
    local model = Instance.new("Model"); model.Name = "Monocle"
    
    local lens = Instance.new("Part")
    lens.Size = Vector3.new(0.5, 0.5, 0.05); lens.Shape = Enum.PartType.Cylinder
    lens.Color = Color3.fromRGB(200, 220, 255); lens.Material = Enum.Material.Glass
    lens.Transparency = 0.3; lens.Anchored = false; lens.CanCollide = false
    lens.Position = head.Position + Vector3.new(-0.45, 0.25, -0.75)
    lens.Orientation = Vector3.new(90, 0, 0)
    lens.Parent = model
    local wl = Instance.new("Weld", lens); wl.Part0 = head; wl.Part1 = lens
    wl.C0 = head.CFrame:ToObjectSpace(CFrame.new(lens.Position) * CFrame.Angles(math.rad(90), 0, 0))
    
    local rim = Instance.new("Part")
    rim.Size = Vector3.new(0.55, 0.55, 0.03); rim.Shape = Enum.PartType.Cylinder
    rim.Color = Color3.fromRGB(255, 215, 0); rim.Material = Enum.Material.Metal
    rim.Anchored = false; rim.CanCollide = false
    rim.Position = lens.Position + Vector3.new(0, 0, -0.03)
    rim.Parent = model
    local wr = Instance.new("Weld", rim); wr.Part0 = lens; wr.Part1 = rim
    wr.C0 = lens.CFrame:ToObjectSpace(CFrame.new(rim.Position))
    
    local chain = Instance.new("Part")
    chain.Size = Vector3.new(0.04, 0.9, 0.04); chain.Color = Color3.fromRGB(255, 215, 0)
    chain.Material = Enum.Material.Metal; chain.Anchored = false; chain.CanCollide = false
    chain.Position = lens.Position + Vector3.new(0, -0.6, 0)
    chain.Parent = model
    local wc = Instance.new("Weld", chain); wc.Part0 = lens; wc.Part1 = chain
    wc.C0 = lens.CFrame:ToObjectSpace(CFrame.new(chain.Position))
    
    model.Parent = char
    Accessories["Monocle"] = model
end

-- ===== ЭФФЕКТЫ =====
local function CreateAura()
    spawn(function()
        while State.Aura do
            local char = GetCharacter()
            if char then
                local pos = char:GetPivot().Position
                for i = 1, 3 do
                    local p = Instance.new("Part")
                    p.Size = Vector3.new(0.4, 0.4, 0.4); p.Shape = Enum.PartType.Ball
                    p.Color = Color3.fromHSV((tick() * 40 + i * 120) % 360 / 360, 0.4, 1)
                    p.Material = Enum.Material.Neon; p.Transparency = 0.55
                    p.Anchored = true; p.CanCollide = false
                    p.Position = pos + Vector3.new(math.sin(tick() * 4 + i) * 1.5, math.cos(tick() * 3 + i) * 1.5, math.cos(tick() * 4 + i) * 1.5)
                    p.Parent = Workspace; game.Debris:AddItem(p, 0.35)
                end
            end
            wait(0.05)
        end
    end)
end

local function CreateTrail()
    spawn(function()
        while State.Trail do
            local char = GetCharacter()
            if char then
                local pos = char:GetPivot().Position
                local t = Instance.new("Part")
                t.Size = Vector3.new(0.2, 0.2, 0.2); t.Shape = Enum.PartType.Ball
                t.Color = Color3.fromRGB(255, 220, 100); t.Material = Enum.Material.Neon
                t.Transparency = 0.45; t.Anchored = true; t.CanCollide = false
                t.Position = pos; t.Parent = Workspace; game.Debris:AddItem(t, 0.5)
                spawn(function()
                    for i = 1, 10 do
                        if t and t.Parent then
                            t.Transparency = t.Transparency + 0.055
                            t.Size = t.Size - Vector3.new(0.015, 0.015, 0.015)
                        end
                        wait(0.03)
                    end
                end)
            end
            wait(0.04)
        end
    end)
end

local function CreateFireTrail()
    spawn(function()
        while State.FireTrail do
            local char = GetCharacter()
            if char then
                local pos = char:GetPivot().Position
                local f = Instance.new("Part")
                f.Size = Vector3.new(0.25, 0.25, 0.25); f.Shape = Enum.PartType.Ball
                f.Color = Color3.fromRGB(255, 80 + math.random(0, 120), 0)
                f.Material = Enum.Material.Neon; f.Transparency = 0.35
                f.Anchored = true; f.CanCollide = false
                f.Position = pos + Vector3.new(0, -0.3, 0)
                f.Parent = Workspace; game.Debris:AddItem(f, 0.35)
            end
            wait(0.06)
        end
    end)
end

local function CreateSparkles()
    spawn(function()
        while State.Sparkles do
            local char = GetCharacter()
            if char then
                local pos = char:GetPivot().Position
                local s = Instance.new("Part")
                s.Size = Vector3.new(0.05, 0.05, 0.05); s.Shape = Enum.PartType.Ball
                s.Color = Color3.fromHSV(tick() * 60 % 360 / 360, 0.3, 1)
                s.Material = Enum.Material.Neon; s.Transparency = 0.4
                s.Anchored = true; s.CanCollide = false
                s.Position = pos + Vector3.new(math.random(-3, 3), math.random(-1, 4), math.random(-3, 3))
                s.Parent = Workspace; game.Debris:AddItem(s, 0.25)
            end
            wait(0.06)
        end
    end)
end

-- ===== ФИКС 3-ГО ЛИЦА =====
local ThirdPersonConnection
local function EnableThirdPerson()
    if ThirdPersonConnection then ThirdPersonConnection:Disconnect() end
    ThirdPersonConnection = RunService.RenderStepped:Connect(function()
        pcall(function()
            if Player.Character and Player.Character:FindFirstChild("Humanoid") then
                Player.CameraMinZoomDistance = 0.5
                Player.CameraMaxZoomDistance = 400
                Player.CameraMode = Enum.CameraMode.Classic
                Player.Character.Humanoid.CameraOffset = Vector3.new(0, 2, 0)
                if Camera.CameraType == Enum.CameraType.Scriptable then
                    Camera.CameraType = Enum.CameraType.Custom
                end
            end
        end)
    end)
end

local function DisableThirdPerson()
    if ThirdPersonConnection then ThirdPersonConnection:Disconnect() end
    ThirdPersonConnection = nil
    pcall(function()
        if Player.Character and Player.Character:FindFirstChild("Humanoid") then
            Player.CameraMinZoomDistance = 0.5
            Player.CameraMaxZoomDistance = 0.5
            Player.CameraMode = Enum.CameraMode.LockFirstPerson
            Player.Character.Humanoid.CameraOffset = Vector3.new(0, 0, 0)
        end
    end)
end

-- ===== ШЕЙДЕРЫ =====
local function ClearShaders()
    for _, e in pairs(Lighting:GetChildren()) do
        if e:IsA("PostEffect") and e.Name:find("Lixar") then e:Destroy() end
    end
    for k, v in pairs(Orig) do pcall(function() Lighting[k] = v end) end
end

local function ApplyShader(cfg)
    ClearShaders()
    if cfg.CC then local cc = Instance.new("ColorCorrectionEffect", Lighting); cc.Name = "LixarCC"
        for k, v in pairs(cfg.CC) do pcall(function() cc[k] = v end) end end
    if cfg.Blur then local bu = Instance.new("BlurEffect", Lighting); bu.Name = "LixarBlur"; bu.Size = cfg.Blur end
    if cfg.SunRays then local sr = Instance.new("SunRaysEffect", Lighting); sr.Name = "LixarSunRays"
        sr.Intensity = cfg.SunRays; sr.Spread = 0.4 end
    if cfg.L then for k, v in pairs(cfg.L) do pcall(function() Lighting[k] = v end) end end
end

-- ===== GUI (компактный) =====
local SG = Instance.new("ScreenGui"); SG.Name = "LixarVisualsFree"; SG.Parent = game.CoreGui
SG.ResetOnSpawn = false; SG.ZIndexBehavior = Enum.ZIndexBehavior.Global

local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 660, 0, 480); Main.Position = UDim2.new(0.5, -330, 0.5, -240)
Main.BackgroundColor3 = Color3.fromRGB(8, 8, 16); Main.BorderSizePixel = 0
Main.Visible = false; Main.Active = true; Main.Draggable = true
Main.Parent = SG; Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 18)

local Glow = Instance.new("Frame")
Glow.Size = UDim2.new(1, 8, 1, 8); Glow.Position = UDim2.new(0, -4, 0, -4)
Glow.BackgroundColor3 = Color3.fromRGB(255, 180, 60); Glow.BackgroundTransparency = 0.4
Glow.BorderSizePixel = 0; Glow.ZIndex = 8; Glow.Parent = Main
Instance.new("UICorner", Glow).CornerRadius = UDim.new(0, 20)

local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 50); TopBar.BackgroundColor3 = Color3.fromRGB(12, 12, 24)
TopBar.BorderSizePixel = 0; TopBar.ZIndex = 11; TopBar.Parent = Main
Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 18)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -100, 1, 0); Title.Position = UDim2.new(0, 16, 0, 0)
Title.BackgroundTransparency = 1; Title.Text = "LIXAR VISUALS FREE"
Title.TextColor3 = Color3.fromRGB(255, 190, 70); Title.TextSize = 21
Title.Font = Enum.Font.GothamBlack; Title.TextXAlignment = Enum.TextXAlignment.Left; Title.ZIndex = 12
Title.Parent = TopBar

local function Btn(t, c, p)
    local b = Instance.new("TextButton"); b.Size = UDim2.new(0, 30, 0, 30)
    b.Position = UDim2.new(1, p, 0.5, -15); b.BackgroundColor3 = c
    b.Text = t; b.TextColor3 = Color3.fromRGB(255, 255, 255); b.TextSize = 16
    b.Font = Enum.Font.GothamBold; b.BorderSizePixel = 0; b.ZIndex = 12; b.Parent = TopBar
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 15); return b
end
local HideBtn = Btn("━", Color3.fromRGB(255, 190, 70), -75)
local CloseBtn = Btn("✕", Color3.fromRGB(255, 60, 60), -38)

local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(0, 155, 1, -60); TabBar.Position = UDim2.new(0, 6, 0, 55)
TabBar.BackgroundColor3 = Color3.fromRGB(12, 12, 24); TabBar.BackgroundTransparency = 0.3
TabBar.BorderSizePixel = 0; TabBar.ZIndex = 10; TabBar.Parent = Main
Instance.new("UICorner", TabBar).CornerRadius = UDim.new(0, 13)

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -173, 1, -65); Content.Position = UDim2.new(0, 167, 0, 60)
Content.BackgroundTransparency = 1; Content.BorderSizePixel = 0; Content.ZIndex = 10; Content.Parent = Main

local function MakeTab(name, icon, pos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 34); btn.Position = UDim2.new(0, 5, 0, pos)
    btn.BackgroundColor3 = Color3.fromRGB(20, 20, 32); btn.Text = "  "..icon.."  "..name
    btn.TextColor3 = Color3.fromRGB(180, 180, 180); btn.TextSize = 11
    btn.Font = Enum.Font.GothamSemibold; btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.AutoButtonColor = false; btn.BorderSizePixel = 0; btn.ZIndex = 10; btn.Parent = TabBar
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 9)
    local sf = Instance.new("ScrollingFrame")
    sf.Size = UDim2.new(1, 0, 1, 0); sf.BackgroundTransparency = 1; sf.ScrollBarThickness = 2
    sf.ScrollBarImageColor3 = Color3.fromRGB(255, 190, 70); sf.CanvasSize = UDim2.new(0, 0, 0, 2500)
    sf.Visible = false; sf.BorderSizePixel = 0; sf.ZIndex = 10; sf.Parent = Content
    local layout = Instance.new("UIListLayout", sf)
    layout.Padding = UDim.new(0, 4); layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    return btn, sf
end

local VisBtn, VisCont = MakeTab("VISUALS", "👁", 4)
local ShadBtn, ShadCont = MakeTab("SHADERS", "🎨", 43)
local AccBtn, AccCont = MakeTab("ACCESSORIES", "💎", 82)
local EffBtn, EffCont = MakeTab("EFFECTS", "✨", 121)
local PlayBtn, PlayCont = MakeTab("PLAYER", "⚔", 160)
local WorldBtn, WorldCont = MakeTab("WORLD", "🌍", 199)

local function SwitchTab(btn, sf)
    for _, c in pairs(TabBar:GetChildren()) do
        if c:IsA("TextButton") then c.BackgroundColor3 = Color3.fromRGB(20, 20, 32); c.TextColor3 = Color3.fromRGB(180, 180, 180) end
    end
    btn.BackgroundColor3 = Color3.fromRGB(255, 190, 70); btn.TextColor3 = Color3.fromRGB(8, 8, 16)
    for _, c in pairs(Content:GetChildren()) do if c:IsA("ScrollingFrame") then c.Visible = false end end
    sf.Visible = true
end
VisBtn.MouseButton1Click:Connect(function() SwitchTab(VisBtn, VisCont) end)
ShadBtn.MouseButton1Click:Connect(function() SwitchTab(ShadBtn, ShadCont) end)
AccBtn.MouseButton1Click:Connect(function() SwitchTab(AccBtn, AccCont) end)
EffBtn.MouseButton1Click:Connect(function() SwitchTab(EffBtn, EffCont) end)
PlayBtn.MouseButton1Click:Connect(function() SwitchTab(PlayBtn, PlayCont) end)
WorldBtn.MouseButton1Click:Connect(function() SwitchTab(WorldBtn, WorldCont) end)

local function Toggle(parent, name, default, callback)
    local t = Instance.new("TextButton")
    t.Size = UDim2.new(0.93, 0, 0, 40); t.BackgroundColor3 = Color3.fromRGB(18, 18, 30)
    t.Text = name; t.TextColor3 = Color3.fromRGB(235, 235, 235); t.TextSize = 12
    t.Font = Enum.Font.GothamSemibold; t.AutoButtonColor = false; t.BorderSizePixel = 0; t.ZIndex = 10; t.Parent = parent
    Instance.new("UICorner", t).CornerRadius = UDim.new(0, 9)
    local on = default
    t.MouseButton1Click:Connect(function() on = not on
        t.BackgroundColor3 = on and Color3.fromRGB(255, 190, 70) or Color3.fromRGB(18, 18, 30)
        t.TextColor3 = on and Color3.fromRGB(8, 8, 16) or Color3.fromRGB(235, 235, 235)
        if callback then callback(on) end
    end)
    return t
end

local function Slider(parent, name, min, max, default, callback)
    local s = Instance.new("Frame")
    s.Size = UDim2.new(0.93, 0, 0, 55); s.BackgroundColor3 = Color3.fromRGB(18, 18, 30)
    s.BorderSizePixel = 0; s.ZIndex = 10; s.Parent = parent
    Instance.new("UICorner", s).CornerRadius = UDim.new(0, 9)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -20, 0, 20); l.Position = UDim2.new(0, 10, 0, 4)
    l.BackgroundTransparency = 1; l.Text = name..": "..default
    l.TextColor3 = Color3.fromRGB(235, 235, 235); l.TextSize = 11; l.Font = Enum.Font.Gotham
    l.TextXAlignment = Enum.TextXAlignment.Left; l.ZIndex = 10; l.Parent = s
    local bar = Instance.new("TextButton")
    bar.Size = UDim2.new(1, -20, 0, 5); bar.Position = UDim2.new(0, 10, 0, 28)
    bar.BackgroundColor3 = Color3.fromRGB(45, 45, 55); bar.Text = ""
    bar.AutoButtonColor = false; bar.BorderSizePixel = 0; bar.ZIndex = 10; bar.Parent = s
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 3)
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(255, 190, 70); fill.BorderSizePixel = 0; fill.ZIndex = 10; fill.Parent = bar
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 3)
    local drag = false
    local function update(val)
        local v = math.clamp(math.floor(val * 10) / 10, min, max)
        fill.Size = UDim2.new((v - min) / (max - min), 0, 1, 0); l.Text = name..": "..v
        if callback then callback(v) end
    end
    bar.MouseButton1Down:Connect(function() drag = true end)
    UIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then drag = false end end)
    UIS.InputChanged:Connect(function(i)
        if drag and i.UserInputType == Enum.UserInputType.MouseMovement then
            local mp = UIS:GetMouseLocation(); local bp = bar.AbsolutePosition; local bs = bar.AbsoluteSize
            local rx = math.clamp(mp.X - bp.X, 0, bs.X); update(min + (rx / bs.X) * (max - min))
        end
    end)
    return s
end

local function Section(parent, text)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.93, 0, 0, 22); lbl.BackgroundTransparency = 1; lbl.Text = text
    lbl.TextColor3 = Color3.fromRGB(255, 190, 70); lbl.TextSize = 13
    lbl.Font = Enum.Font.GothamBold; lbl.TextXAlignment = Enum.TextXAlignment.Left; lbl.ZIndex = 10; lbl.Parent = parent
end

-- ===== ЗАПОЛНЕНИЕ =====

Toggle(VisCont, "ESP", false, function(v) State.ESP = v
    if v then spawn(function() while State.ESP do pcall(function()
        for _, o in pairs(Workspace:GetDescendants()) do
            if o:IsA("BasePart") and o.Parent:FindFirstChild("Humanoid") and o.Parent ~= Player.Character then
                local hl = Instance.new("Highlight"); hl.FillColor = Color3.fromRGB(255,50,50)
                hl.OutlineColor = Color3.fromRGB(255,190,70); hl.FillTransparency = 0.5; hl.Parent = o; game.Debris:AddItem(hl,0.3)
            end
        end
    end) wait(0.3) end end) end
end)
Toggle(VisCont, "Fullbright", false, function(v) State.Fullbright = v
    if v then Lighting.Brightness = 3; Lighting.FogEnd = 100000; Lighting.GlobalShadows = false
    else Lighting.Brightness = Orig.Brightness; Lighting.FogEnd = Orig.FogEnd; Lighting.GlobalShadows = Orig.GlobalShadows end
end)
Toggle(VisCont, "No Fog", true, function(v) State.NoFog = v; Lighting.FogEnd = v and 100000 or Orig.FogEnd end)
Toggle(VisCont, "Third Person (FIXED)", false, function(v) State.ThirdPerson = v; if v then EnableThirdPerson() else DisableThirdPerson() end end)
Slider(VisCont, "FOV", 30, 120, 70, function(v) State.FOV = v; Camera.FieldOfView = v end)

Toggle(ShadCont, "☀ Warm Sunshine", false, function(v) if v then ApplyShader({CC={Brightness=0.25,Contrast=0.1,Saturation=0.3,TintColor=Color3.fromRGB(255,245,220)},SunRays=0.35,L={Brightness=2.5,FogEnd=2000,Ambient=Color3.fromRGB(255,240,200),OutdoorAmbient=Color3.fromRGB(255,230,180),ClockTime=12}}) else ClearShaders() end end)
Toggle(ShadCont, "🌸 Sakura Dream", false, function(v) if v then ApplyShader({CC={Brightness=0.2,Contrast=0.05,Saturation=0.2,TintColor=Color3.fromRGB(255,225,235)},SunRays=0.25,L={Brightness=2.3,FogEnd=1500,Ambient=Color3.fromRGB(255,215,230),OutdoorAmbient=Color3.fromRGB(255,200,220),ClockTime=8}}) else ClearShaders() end end)
Toggle(ShadCont, "🌤 Clear Sky", false, function(v) if v then ApplyShader({CC={Brightness=0.15,Contrast=0.1,Saturation=0.15,TintColor=Color3.fromRGB(220,235,255)},SunRays=0.3,L={Brightness=2.8,FogEnd=5000,Ambient=Color3.fromRGB(200,220,250),OutdoorAmbient=Color3.fromRGB(180,200,240),ClockTime=11}}) else ClearShaders() end end)
Toggle(ShadCont, "🌿 Mint Fresh", false, function(v) if v then ApplyShader({CC={Brightness=0.2,Contrast=0.08,Saturation=0.1,TintColor=Color3.fromRGB(200,240,220)},SunRays=0.2,L={Brightness=2.4,FogEnd=2000,Ambient=Color3.fromRGB(190,230,210),OutdoorAmbient=Color3.fromRGB(170,210,190),ClockTime=10}}) else ClearShaders() end end)
Toggle(ShadCont, "🍑 Peach Smooth", false, function(v) if v then ApplyShader({CC={Brightness=0.2,Contrast=0.05,Saturation=0.25,TintColor=Color3.fromRGB(255,220,200)},SunRays=0.3,L={Brightness=2.5,FogEnd=1800,Ambient=Color3.fromRGB(255,210,190),OutdoorAmbient=Color3.fromRGB(255,195,175),ClockTime=16}}) else ClearShaders() end end)
Toggle(ShadCont, "🌊 Ocean Breeze", false, function(v) if v then ApplyShader({CC={Brightness=0.15,Contrast=0.1,Saturation=0.1,TintColor=Color3.fromRGB(180,215,240)},Blur=0.3,L={Brightness=2.2,FogEnd=1200,Ambient=Color3.fromRGB(170,210,240),OutdoorAmbient=Color3.fromRGB(150,190,230),ClockTime=13}}) else ClearShaders() end end)
Toggle(ShadCont, "🌻 Golden Hour", false, function(v) if v then ApplyShader({CC={Brightness=0.2,Contrast=0.1,Saturation=0.3,TintColor=Color3.fromRGB(255,235,200)},SunRays=0.4,L={Brightness=2.6,FogEnd=2000,Ambient=Color3.fromRGB(255,225,180),OutdoorAmbient=Color3.fromRGB(255,210,160),ClockTime=17}}) else ClearShaders() end end)
Toggle(ShadCont, "❄ Frost Morning", false, function(v) if v then ApplyShader({CC={Brightness=0.3,Contrast=0.05,Saturation=-0.2,TintColor=Color3.fromRGB(220,230,255)},Blur=0.5,L={Brightness=3,FogEnd=1000,Ambient=Color3.fromRGB(220,225,250),OutdoorAmbient=Color3.fromRGB(200,210,240),ClockTime=7}}) else ClearShaders() end end)
Toggle(ShadCont, "🌅 Soft Sunset", false, function(v) if v then ApplyShader({CC={Brightness=0.15,Contrast=0.08,Saturation=0.3,TintColor=Color3.fromRGB(255,210,190)},SunRays=0.5,L={Brightness=2,FogEnd=1500,Ambient=Color3.fromRGB(255,180,140),OutdoorAmbient=Color3.fromRGB(255,160,120),ClockTime=18.5}}) else ClearShaders() end end)
Toggle(ShadCont, "🔄 Reset", false, function(v) if v then ClearShaders() end end)

Section(AccCont, "🦋 КРЫЛЬЯ")
Toggle(AccCont, "👼 Angel Wings", false, function(v) State.AngelWings = v; if v then CreateAngelWings() else RemoveAccessory("AngelWings") end end)
Toggle(AccCont, "🐉 Dragon Wings", false, function(v) State.DragonWings = v; if v then CreateDragonWings() else RemoveAccessory("DragonWings") end end)
Toggle(AccCont, "🦋 Butterfly Wings", false, function(v) State.ButterflyWings = v; if v then CreateButterflyWings() else RemoveAccessory("ButterflyWings") end end)

Section(AccCont, "🎩 ГОЛОВНЫЕ УБОРЫ")
Toggle(AccCont, "🎩 Top Hat", false, function(v) State.TopHat = v; if v then CreateTopHat() else RemoveAccessory("TopHat") end end)
Toggle(AccCont, "🧙 Wizard Hat", false, function(v) State.WizardHat = v; if v then CreateWizardHat() else RemoveAccessory("WizardHat") end end)
Toggle(AccCont, "🐱 Cat Ears", false, function(v) State.CatEars = v; if v then CreateCatEars() else RemoveAccessory("CatEars") end end)
Toggle(AccCont, "👑 Royal Crown", false, function(v) State.Crown = v; if v then CreateCrown() else RemoveAccessory("Crown") end end)
Toggle(AccCont, "😇 Holy Halo", false, function(v) State.Halo = v; if v then CreateHalo() else RemoveAccessory("Halo") end end)

Section(AccCont, "👓 ОЧКИ")
Toggle(AccCont, "🕶 Stylish Glasses", false, function(v) State.Glasses = v; if v then CreateGlasses() else RemoveAccessory("Glasses") end end)
Toggle(AccCont, "🧐 Golden Monocle", false, function(v) State.Monocle = v; if v then CreateMonocle() else RemoveAccessory("Monocle") end end)

Toggle(EffCont, "🌈 Rainbow Aura", false, function(v) State.Aura = v; if v then CreateAura() end end)
Toggle(EffCont, "💫 Neon Trail", false, function(v) State.Trail = v; if v then CreateTrail() end end)
Toggle(EffCont, "🔥 Fire Trail", false, function(v) State.FireTrail = v; if v then CreateFireTrail() end end)
Toggle(EffCont, "✨ Magic Sparkles", false, function(v) State.Sparkles = v; if v then CreateSparkles() end end)

Toggle(PlayCont, "🎯 Aimbot (Hold Q)", false, function(v) State.Aimbot = v end)
Toggle(PlayCont, "God Mode", false, function(v) State.GodMode = v
    if v then spawn(function() while State.GodMode do pcall(function()
        if Player.Character and Player.Character:FindFirstChild("Humanoid") then
            Player.Character.Humanoid.Health = Player.Character.Humanoid.MaxHealth end
    end) wait(0.1) end end) end
end)
Toggle(PlayCont, "Spin Bot", false, function(v) State.SpinBot = v
    if v then spawn(function() while State.SpinBot do pcall(function()
        local r = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
        if r then r.CFrame = r.CFrame * CFrame.Angles(0, math.rad(State.SpinSpeed), 0) end
    end) wait() end end) end
end)
Slider(PlayCont, "Spin Speed", 1, 100, 20, function(v) State.SpinSpeed = v end)

Toggle(WorldCont, "Speed Hack", false, function(v) State.SpeedHack = v
    pcall(function() if Player.Character and Player.Character:FindFirstChild("Humanoid") then
        Player.Character.Humanoid.WalkSpeed = v and State.WalkSpeed or 16 end end)
end)
Slider(WorldCont, "Walk Speed", 16, 300, 100, function(v) State.WalkSpeed = v
    if State.SpeedHack then pcall(function() if Player.Character and Player.Character:FindFirstChild("Humanoid") then
        Player.Character.Humanoid.WalkSpeed = v end end) end
end)
Toggle(WorldCont, "Fly", false, function(v) State.Fly = v
    if v then spawn(function()
        local c = Player.Character; if not c then return end
        local r = c:FindFirstChild("HumanoidRootPart"); if not r then return end
        local bg = Instance.new("BodyGyro", r); bg.P = 9e4
        local bv = Instance.new("BodyVelocity", r); bv.MaxForce = Vector3.new(9e9,9e9,9e9)
        while State.Fly and c and r do bg.CFrame = Camera.CFrame; local s = State.FlySpeed; local vel = Vector3.zero
            if UIS:IsKeyDown(Enum.KeyCode.W) then vel = Camera.CFrame.LookVector * s
            elseif UIS:IsKeyDown(Enum.KeyCode.S) then vel = -Camera.CFrame.LookVector * s end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then vel = vel + Vector3.new(0,s,0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then vel = vel + Vector3.new(0,-s,0) end
            bv.Velocity = vel; wait() end
        bg:Destroy(); bv:Destroy()
    end) end
end)
Slider(WorldCont, "Fly Speed", 10, 200, 50, function(v) State.FlySpeed = v end)
Toggle(WorldCont, "NoClip", false, function(v) State.NoClip = v
    if v then spawn(function() while State.NoClip do pcall(function()
        if Player.Character then for _, p in pairs(Player.Character:GetDescendants()) do
            if p:IsA("BasePart") then p.CanCollide = false end end end
    end) wait(0.1) end end) end
end)
Toggle(WorldCont, "Infinite Jump", false, function(v) State.InfiniteJump = v end)
Slider(WorldCont, "Jump Power", 50, 500, 50, function(v) State.JumpPower = v
    pcall(function() if Player.Character and Player.Character:FindFirstChild("Humanoid") then
        Player.Character.Humanoid.JumpPower = v end end)
end)
Toggle(WorldCont, "Anti-AFK", true, function(v) State.AntiAFK = v end)
Toggle(WorldCont, "Anti-Stun", true, function(v) State.AntiStun = v
    if v then spawn(function() while State.AntiStun do pcall(function()
        local h = Player.Character and Player.Character:FindFirstChild("Humanoid")
        if h and h:GetState() == Enum.HumanoidStateType.Physics then h:ChangeState(Enum.HumanoidStateType.Running) end
    end) wait(0.1) end end) end
end)

local Info = Instance.new("Frame")
Info.Size = UDim2.new(1, -10, 0, 50); Info.Position = UDim2.new(0, 5, 1, -57)
Info.BackgroundColor3 = Color3.fromRGB(12, 12, 24); Info.BackgroundTransparency = 0.3
Info.BorderSizePixel = 0; Info.ZIndex = 10; Info.Parent = Main
Instance.new("UICorner", Info).CornerRadius = UDim.new(0, 10)

local Avatar = Instance.new("ImageLabel")
Avatar.Size = UDim2.new(0, 36, 0, 36); Avatar.Position = UDim2.new(0, 7, 0.5, -18)
Avatar.BackgroundColor3 = Color3.fromRGB(255, 190, 70)
Avatar.Image = "https://www.roblox.com/headshot-thumbnail/image?userId="..Player.UserId.."&width=420&height=420&format=png"
Avatar.ZIndex = 10; Avatar.Parent = Info; Instance.new("UICorner", Avatar).CornerRadius = UDim.new(0, 18)

local NameL = Instance.new("TextLabel"); NameL.Size = UDim2.new(0, 140, 0, 16)
NameL.Position = UDim2.new(0, 50, 0, 5); NameL.BackgroundTransparency = 1
NameL.Text = "👑 "..Player.Name; NameL.TextColor3 = Color3.fromRGB(255, 190, 70)
NameL.TextSize = 13; NameL.Font = Enum.Font.GothamBlack; NameL.TextXAlignment = Enum.TextXAlignment.Left
NameL.ZIndex = 10; NameL.Parent = Info

local FPSL = Instance.new("TextLabel"); FPSL.Size = UDim2.new(0, 75, 0, 13)
FPSL.Position = UDim2.new(0, 50, 0, 24); FPSL.BackgroundTransparency = 1
FPSL.Text = "⚡ 60 FPS"; FPSL.TextColor3 = Color3.fromRGB(170, 170, 170)
FPSL.TextSize = 10; FPSL.Font = Enum.Font.Gotham; FPSL.TextXAlignment = Enum.TextXAlignment.Left
FPSL.ZIndex = 10; FPSL.Parent = Info

local PingL = Instance.new("TextLabel"); PingL.Size = UDim2.new(0, 80, 0, 13)
PingL.Position = UDim2.new(0, 130, 0, 24); PingL.BackgroundTransparency = 1
PingL.Text = "📶 0ms"; PingL.TextColor3 = Color3.fromRGB(170, 170, 170)
PingL.TextSize = 10; PingL.Font = Enum.Font.Gotham; PingL.TextXAlignment = Enum.TextXAlignment.Left
PingL.ZIndex = 10; PingL.Parent = Info

local StatusL = Instance.new("TextLabel"); StatusL.Size = UDim2.new(0, 100, 0, 14)
StatusL.Position = UDim2.new(1, -110, 0.5, -7); StatusL.BackgroundTransparency = 1
StatusL.Text = "🟢 READY"; StatusL.TextColor3 = Color3.fromRGB(0, 255, 100)
StatusL.TextSize = 10; StatusL.Font = Enum.Font.GothamBlack; StatusL.TextXAlignment = Enum.TextXAlignment.Right
StatusL.ZIndex = 10; StatusL.Parent = Info

UIS.InputBegan:Connect(function(input, gpe)
    if not gpe and input.KeyCode == Enum.KeyCode.Q and State.Aimbot then
        State.AimbotHold = true; StatusL.Text = "🔴 AIM"; StatusL.TextColor3 = Color3.fromRGB(255,50,50)
        spawn(function() while State.AimbotHold and State.Aimbot do pcall(function()
            local closest, shortest = nil, math.huge
            for _, plr in pairs(game.Players:GetPlayers()) do
                if plr ~= Player and plr.Character and plr.Character:FindFirstChild("Head") and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
                    local pos = Camera:WorldToScreenPoint(plr.Character.Head.Position)
                    local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                    if dist < shortest and pos.Z > 0 then shortest = dist; closest = plr.Character.Head end
                end
            end
            if closest then Camera.CFrame = CFrame.new(Camera.CFrame.Position, closest.Position) end
        end) wait() end end)
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Q then State.AimbotHold = false; StatusL.Text = "🟢 READY"; StatusL.TextColor3 = Color3.fromRGB(0,255,100) end
end)

local open = false
local function ToggleGUI()
    open = not open
    if open then Main.Visible = true; Main.Size = UDim2.new(0,0,0,0)
        Tween:Create(Main, TweenInfo.new(0.4, Enum.EasingStyle.Back), {Size = UDim2.new(0,660,0,480)}):Play()
    else Tween:Create(Main, TweenInfo.new(0.3), {Size = UDim2.new(0,0,0,0)}):Play(); wait(0.3); Main.Visible = false end
end
CloseBtn.MouseButton1Click:Connect(ToggleGUI)
HideBtn.MouseButton1Click:Connect(ToggleGUI)
UIS.InputBegan:Connect(function(i,g) if not g and i.KeyCode == Enum.KeyCode.X then ToggleGUI() end end)

spawn(function() while wait(1) do pcall(function()
    FPSL.Text = "⚡ "..math.floor(1/RunService.RenderStepped:Wait()).." FPS"
    PingL.Text = "📶 "..math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue()*1000).."ms"
end) end end)

UIS.JumpRequest:Connect(function()
    if State.InfiniteJump then pcall(function()
        if Player.Character and Player.Character:FindFirstChild("Humanoid") then
            Player.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping) end
    end) end
end)

spawn(function() local vu = game:GetService("VirtualUser")
    Player.Idled:Connect(function() if State.AntiAFK then vu:CaptureController(); vu:ClickButton2(Vector2.new()) end end)
end)

SwitchTab(AccBtn, AccCont)
wait(0.5)
ToggleGUI()

