-- tp-hub V3.6 完全日本語版（全ロジック保持）

local ReplicatedStorage = game:GetService("ReplicatedStorage")

local RunService = game:GetService("RunService")

local Workspace = game:GetService("Workspace")

local Players = game:GetService("Players")

local LP = Players.LocalPlayer

-- Orion Library ロード

local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/jadpy/suki/refs/heads/main/orion"))()

-- ウィンドウ作成

local Window = OrionLib:MakeWindow({

Name = "tp-hub V3.6",

HidePremium = false,

SaveConfig = true,

ConfigFolder = "OrionTest"

})

--- レインボーライン機能ロジック ---

local originalColors

if ReplicatedStorage:FindFirstChild("DataEvents") and ReplicatedStorage.DataEvents:FindFirstChild("UpdateLineColorsEvent") then

originalColors = {}

for i = 1, 10 do

table.insert(originalColors, Color3.new(1,1,1))

end

end

local ToggleState = false

local hueOffset = 0

local function FireRainbowColors()

if ReplicatedStorage:FindFirstChild("DataEvents") and ReplicatedStorage.DataEvents:FindFirstChild("UpdateLineColorsEvent") then

local cs = ColorSequence.new{

ColorSequenceKeypoint.new(0, Color3.fromHSV((0+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.1, Color3.fromHSV((0.1+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.2, Color3.fromHSV((0.2+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.3, Color3.fromHSV((0.3+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.4, Color3.fromHSV((0.4+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.5, Color3.fromHSV((0.5+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.6, Color3.fromHSV((0.6+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.7, Color3.fromHSV((0.7+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.8, Color3.fromHSV((0.8+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(0.9, Color3.fromHSV((0.9+hueOffset)%1,1,1)),

ColorSequenceKeypoint.new(1, Color3.fromHSV((1+hueOffset)%1,1,1))

}

ReplicatedStorage.DataEvents.UpdateLineColorsEvent:FireServer(cs, cs.Keypoints[1].Value, cs.Keypoints[2].Value, cs.Keypoints[3].Value, cs.Keypoints[4].Value, cs.Keypoints[5].Value, cs.Keypoints[6].Value, cs.Keypoints[7].Value, cs.Keypoints[8].Value, cs.Keypoints[9].Value)

end

end

RunService.Heartbeat:Connect(function()

if ToggleState then

hueOffset = (hueOffset + 0.005) % 1

FireRainbowColors()

end

end)

--- 翼MODロジック (WINGS MOD) ---

local Enabled = false

local Spacing = 0.0

local WingHeight = 2.0  

local BackOffset = 0.5  

local SideOffset = 0.0

local WaveAmplitude = 2.5

local WaveSpeed = 5.0

local TwistAngle = 20.0

local TwistSpeed = 5.0

local TwistWave = 0.5

local TargetSet = "Sparkler"

local defaultMaxZoom = 12

local maxZoom5000Enabled = false

local TargetSets = {

["GlassBox"] = {"GlassBoxGray"},

["Pallet"] = {"PalletLightBrown"},

["Sparkler"] = {"FireworkSparkler"},

["Toilet"] = {"ToiletWhite","ToiletGold"},

["Piano"] = {"KeyboardPart", "MusicKeyboard"},

["Stick"] = {"Stick"}

}

local list, loopConn = {}, nil

local tAccum = 0

local function HRP()

-- Saved Informationに基づき、対象プレイヤーがいればそのHRPを返す

if WingsTargetPlayer and WingsTargetPlayer.Character and WingsTargetPlayer.Character:FindFirstChild("HumanoidRootPart") then

return WingsTargetPlayer.Character.HumanoidRootPart

end

local c = LP.Character or LP.CharacterAdded:Wait()

return c:FindFirstChild("HumanoidRootPart")

end

local function getPartFromModel(m)

if m.PrimaryPart then return m.PrimaryPart end

return m:FindFirstChildWhichIsA("BasePart")

end

local function nameInTargets(n)

local t = TargetSets[TargetSet]

if not t then return false end

for _, nm in ipairs(t) do if n == nm then return true end end

return false

end

local function attachPhysics(rec)

local model = rec.model; local part = rec.part

if not model or not part or not part.Parent then return end

for _, p in ipairs(model:GetDescendants()) do

if p:IsA("BasePart") and not p:IsA("BodyMover") then

pcall(function() p:SetNetworkOwner(LP) end)

p.CanCollide, p.CanTouch = false, false

end

end

if not part:FindFirstChild("BodyVelocity") then

local bv = Instance.new("BodyVelocity", part); bv.Name = "BodyVelocity"; bv.MaxForce = Vector3.new(1e8,1e8,1e8); bv.Velocity = Vector3.new(); bv.P = 1e6

end

if not part:FindFirstChild("BodyGyro") then

local bg = Instance.new("BodyGyro", part); bg.Name = "BodyGyro"; bg.MaxTorque = Vector3.new(1e8,1e8,1e8); bg.CFrame = part.CFrame; bg.P = 1e6

end

end

local function detachPhysics(rec)

local model = rec.model; local part = rec.part

if not model or not part then return end

if part:FindFirstChild("BodyVelocity") then part.BodyVelocity:Destroy() end

if part:FindFirstChild("BodyGyro") then part.BodyGyro:Destroy() end

for _, p in ipairs(model:GetDescendants()) do

if p:IsA("BasePart") and not p:IsA("BodyMover") then

p.CanCollide, p.CanTouch = true, true

pcall(function() p:SetNetworkOwner(nil) end)

end

end

end

local function rescan()

for _,r in ipairs(list) do detachPhysics(r) end

list = {}

for _, d in ipairs(Workspace:GetDescendants()) do

if d:IsA("Model") and nameInTargets(d.Name) then

local part = getPartFromModel(d)

if part and not part.Anchored then table.insert(list, {model=d, part=part}) end

elseif d:IsA("BasePart") and nameInTargets(d.Name) then 

if not d.Anchored then table.insert(list, {model=d, part=d}) end

end

end

for i = 1, #list do attachPhysics(list[i]) end

end

local function startLoop()

if loopConn then loopConn:Disconnect() end

tAccum = 0

loopConn = RunService.Heartbeat:Connect(function(dt)

local root = HRP()

if not root or #list == 0 then return end

tAccum = tAccum + dt

local wingsCenter = root.CFrame * CFrame.new(SideOffset, WingHeight, BackOffset)

local WaveConstant = math.pi / #list

for j = 1, #list do

local rec = list[j]; local p = rec.part

if p and p.Parent then

local isRight = (j % 2 == 0); local sign = isRight and 1 or -1

local animationIndex = math.ceil(j / 2)

local distanceIndex = isRight and (j - 2) / 2 or (j - 1) / 2

local xOffset = Spacing * (distanceIndex + 0.2) * sign

local waveOffset = WaveAmplitude * math.sin(WaveSpeed * tAccum + animationIndex * WaveConstant)

local twistOscillation = math.sin(TwistSpeed * tAccum + animationIndex * TwistWave)

local currentTwistAngle = math.rad(TwistAngle * twistOscillation * (distanceIndex / (#list / 2)))

local targetCFrame = wingsCenter * CFrame.Angles(currentTwistAngle, 0, 0) * CFrame.new(xOffset, waveOffset, 0)    

if p:FindFirstChild("BodyVelocity") then p.BodyVelocity.Velocity = (targetCFrame.Position - p.Position) * 20 end    

if p:FindFirstChild("BodyGyro") then p.BodyGyro.CFrame = targetCFrame end    

end    

end

end)

end

--- タブ構成 ---

-- 情報タブ

local InfoTab = Window:MakeTab({

Name = "情報",

Icon = "rbxassetid://4483345998",

PremiumOnly = false

})

InfoTab:AddLabel("クレジット: BGHACKERS / Logfeetst")

InfoTab:AddButton({

Name = "Discordリンクをコピー",

Callback = function()

setclipboard("https://discord.gg/Ghn3cjjHem")

OrionLib:MakeNotification({Name = "コピー完了", Content = "リンクをコピーしたぜ、マスター。", Time = 3})

end

})

-- ライン色タブ

local LineTab = Window:MakeTab({

Name = "ライン色",

Icon = "rbxassetid://4483345998",

PremiumOnly = false

})

LineTab:AddToggle({

Name = "レインボーライン (FTAP専用)",

Default = false,

Callback = function(Value)

ToggleState = Value

if not Value and originalColors then

ReplicatedStorage.DataEvents.UpdateLineColorsEvent:FireServer(originalColors[1],originalColors[2],originalColors[3],originalColors[4],originalColors[5],originalColors[6],originalColors[7],originalColors[8],originalColors[9],originalColors[10])

end

end

})

-- テレポートタブ

local TPTab = Window:MakeTab({

Name = "テレポート",

Icon = "rbxassetid://4483345998",

PremiumOnly = false

})

local function Teleport(coords)

local player = game.Players.LocalPlayer

if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then

player.Character.HumanoidRootPart.CFrame = CFrame.new(coords)

end

end

TPTab:AddSection({Name = "各地点への移動"})

TPTab:AddButton({ Name = "ピンクの家へテレポート", Callback = function() Teleport(Vector3.new(-481.23, -7.35, -149.67)) end })

TPTab:AddButton({ Name = "緑の家へテレポート", Callback = function() Teleport(Vector3.new(-546.02, -7.35, 78.07)) end })

TPTab:AddButton({ Name = "赤い家へテレポート", Callback = function() Teleport(Vector3.new(542.11, 123.34, -91.78)) end })

TPTab:AddButton({ Name = "水色の家へテレポート", Callback = function() Teleport(Vector3.new(496.99, 83.34, -350.96)) end })

-- WINGSタブ

local WingTab = Window:MakeTab({

Name = "WINGS",

Icon = "rbxassetid://6031075927",

PremiumOnly = false

})

WingTab:AddDropdown({

Name = "ターゲット選択",

Default = TargetSet,

Options = {"Stick", "Sparkler","Toilet", "Piano", "GlassBox", "Pallet"},

Callback = function(v) TargetSet = v; if Enabled then rescan() end end

})

WingTab:AddToggle({

Name = "翼 起動/停止",

Default = false,

Callback = function(v)

Enabled = v

if v then rescan(); startLoop() else if loopConn then loopConn:Disconnect() end; for _,rec in ipairs(list) do detachPhysics(rec) end; list = {} end

end

})

WingTab:AddButton({

Name = "Fly GUI v3 起動",

Callback = function()

loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()

end

})

WingTab:AddSlider({ Name = "翼の広さ (Spacing)", Min = 0, Max = 5, Default = 0.2, Increment = 0.1, Callback = function(v) Spacing = v end })

WingTab:AddSlider({ Name = "翼の高さ (Height)", Min = -10, Max = 100, Default = 2, Increment = 1, Callback = function(v) WingHeight = v end })

WingTab:AddToggle({

Name = "カメラズーム 5000",

Default = false,

Callback = function(v)

maxZoom5000Enabled = v

LP.CameraMaxZoomDistance = v and 5000 or defaultMaxZoom

end

})

OrionLib:Init()

LP.CameraMode = Enum.CameraMode.Classic

LP.CameraMinZoomDistance = 0.5

-- ESP機能

local ESPEnabled = false

local ESPObjects = {}

local function getHeadshot(userId)

return "https://www.roblox.com/headshot-thumbnail/image?userId="

.. userId .. "&width=150&height=150&format=png"

end

local function createESP(player)

if player == LP then return end

if ESPObjects[player] then return end

local gui = Instance.new("BillboardGui")

gui.Name = "TPHubESP"

gui.Size = UDim2.new(0, 220, 0, 90)

gui.StudsOffset = Vector3.new(0, 2.5, 0)

gui.AlwaysOnTop = true

local frame = Instance.new("Frame")

frame.Size = UDim2.new(1, 0, 1, 0)

frame.BackgroundColor3 = Color3.fromRGB(20,20,20)

frame.BackgroundTransparency = 0.2

frame.BorderSizePixel = 1

frame.Parent = gui

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)

local img = Instance.new("ImageLabel")

img.Size = UDim2.new(0, 64, 0, 64)

img.Position = UDim2.new(0, 6, 0, 12)

img.BackgroundTransparency = 1

img.Image = getHeadshot(player.UserId)

img.Parent = frame

local nameLabel = Instance.new("TextLabel")

nameLabel.Position = UDim2.new(0, 80, 0, 18)

nameLabel.Size = UDim2.new(1, -90, 0, 22)

nameLabel.BackgroundTransparency = 1

nameLabel.Text = player.DisplayName

nameLabel.TextColor3 = Color3.new(1,1,1)

nameLabel.TextScaled = true

nameLabel.Font = Enum.Font.GothamBold

nameLabel.Parent = frame

local idLabel = Instance.new("TextLabel")

idLabel.Position = UDim2.new(0, 80, 0, 42)

idLabel.Size = UDim2.new(1, -90, 0, 20)

idLabel.BackgroundTransparency = 1

idLabel.Text = "ID : "..player.UserId

idLabel.TextColor3 = Color3.fromRGB(180,180,180)

idLabel.TextScaled = true

idLabel.Font = Enum.Font.Gotham

idLabel.Parent = frame

ESPObjects[player] = gui

local function attach()

if player.Character and player.Character:FindFirstChild("Head") then

gui.Parent = player.Character.Head

end

end

attach()

player.CharacterAdded:Connect(function()

task.wait(1)

attach()

end)

end

local function removeAllESP()

for _, gui in pairs(ESPObjects) do

if gui then gui:Destroy() end

end

ESPObjects = {}

end

Players.PlayerAdded:Connect(function(player)

if ESPEnabled then

createESP(player)

end

end)

-- ESP タブ

local ESPTab = Window:MakeTab({

Name = "ESP",

Icon = "rbxassetid://4483345998",

PremiumOnly = false

})

ESPTab:AddToggle({

Name = "ESP 有効化 (名前 / ID / 顔)",

Default = false,

Callback = function(value)

ESPEnabled = value

if value then

for _, player in ipairs(Players:GetPlayers()) do

createESP(player)

end

else

removeAllESP()

end

end

})

-- 回転機能

local SpinEnabled = false

local SpinSpeed = 10 

RunService.Heartbeat:Connect(function()

if not SpinEnabled then return end

local char = LP.Character

local hrp = char and char:FindFirstChild("HumanoidRootPart")

if hrp then

hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(SpinSpeed), 0)

end

end)

WingTab:AddToggle({

Name = "高速回転 (右スピン)",

Default = false,

Callback = function(v)

SpinEnabled = v

end

})

WingTab:AddSlider({

Name = "回転スピード",

Min = 1,

Max = 20,

Default = 10,

Increment = 1,

Callback = function(v)

SpinSpeed = v

end

})

-- WINGS 対象プレイヤー

local function getPlayerNameList()

local t = {"自分"}

for _, plr in ipairs(Players:GetPlayers()) do

if plr ~= LP then

table.insert(t, plr.Name)

end

end

return t

end

WingsTargetPlayer = nil

WingTab:AddDropdown({

Name = "WINGS 対象プレイヤー",

Default = "自分",

Options = getPlayerNameList(),

Callback = function(v)

if v == "自分" then

WingsTargetPlayer = nil

else

WingsTargetPlayer = Players:FindFirstChild(v)

end

end

})

-- 竜巻モード

local TornadoEnabled = false

local TORNADO_RADIUS = 13.5

local TORNADO_HEIGHT = 23

local TORNADO_SPIN_SPEED = 4

local tornadoConn = nil

local function startTornado()

if tornadoConn then tornadoConn:Disconnect() end

rescan()  

tornadoConn = RunService.Heartbeat:Connect(function(dt)  

    local root = HRP()  

    if not root or #list == 0 then return end  

    tAccum += dt  

    local baseCF = root.CFrame  

    for i, rec in ipairs(list) do  

        local p = rec.part  

        if p and p.Parent then  

            local heightOffset = (i / #list) * TORNADO_HEIGHT  

            local angle = (tAccum * TORNADO_SPIN_SPEED) + (i * math.pi * 2 / #list)  

            local x = math.cos(angle) * TORNADO_RADIUS  

            local z = math.sin(angle) * TORNADO_RADIUS  

            local targetCFrame = baseCF * CFrame.new(x, heightOffset, z) * CFrame.Angles(0, angle, 0)  

            if p:FindFirstChild("BodyVelocity") then p.BodyVelocity.Velocity = (targetCFrame.Position - p.Position) * 30 end  

            if p:FindFirstChild("BodyGyro") then p.BodyGyro.CFrame = targetCFrame end  

        end  

    end  

end)

end

local function stopTornado()

if tornadoConn then tornadoConn:Disconnect(); tornadoConn = nil end

for _, rec in ipairs(list) do detachPhysics(rec) end  

list = {}

end

WingTab:AddToggle({

Name = "竜巻モード",

Default = false,

Callback = function(v)

TornadoEnabled = v

if v then  

        if loopConn then loopConn:Disconnect() end  

        startTornado()  

    else  

        stopTornado()  

        if Enabled then rescan(); startLoop() end  

    end  

end

})

-- 円形モード

local CircleEnabled = false

local CIRCLE_RADIUS = 13.5

local CIRCLE_HEIGHT = 2

local CIRCLE_SPEED = 10

local circleConn = nil

local function startCircle()

if circleConn then circleConn:Disconnect() end

rescan()  

circleConn = RunService.Heartbeat:Connect(function(dt)  

    local root = HRP()  

    if not root or #list == 0 then return end  

    tAccum += dt  

    local baseCF = root.CFrame  

    for i, rec in ipairs(list) do  

        local p = rec.part  

        if p and p.Parent then  

            local angle = (i / #list) * math.pi * 2 + (tAccum * CIRCLE_SPEED)  

            local x = math.cos(angle) * CIRCLE_RADIUS  

            local z = math.sin(angle) * CIRCLE_RADIUS  

            local targetCFrame = baseCF * CFrame.new(x, CIRCLE_HEIGHT, z) * CFrame.Angles(0, angle, 0)  

            if p:FindFirstChild("BodyVelocity") then p.BodyVelocity.Velocity = (targetCFrame.Position - p.Position) * 30 end  

            if p:FindFirstChild("BodyGyro") then p.BodyGyro.CFrame = targetCFrame end  

        end  

    end  

end)

end

local function stopCircle()

if circleConn then circleConn:Disconnect(); circleConn = nil end

for _, rec in ipairs(list) do detachPhysics(rec) end  

list = {}

end

WingTab:AddToggle({

Name = "円形モード",

Default = false,

Callback = function(v)

CircleEnabled = v

if v then  

        if loopConn then loopConn:Disconnect() end  

        if tornadoConn then tornadoConn:Disconnect() end  

        startCircle()  

    else  

        stopCircle()  

        if Enabled then rescan(); startLoop() end  

    end  

end

})

-- モードカスタム設定

WingTab:AddSection({Name = "竜巻モード設定"})

WingTab:AddSlider({ Name = "竜巻 速度", Min = 1, Max = 50, Default = 4, Increment = 1, Callback = function(v) TORNADO_SPIN_SPEED = v end })

WingTab:AddSlider({ Name = "竜巻 半径", Min = 1, Max = 100, Default = 13, Increment = 1, Callback = function(v) TORNADO_RADIUS = v end })

WingTab:AddSlider({ Name = "竜巻 高さ", Min = 1, Max = 150, Default = 23, Increment = 1, Callback = function(v) TORNADO_HEIGHT = v end })

WingTab:AddSection({Name = "円形モード設定"})

WingTab:AddSlider({ Name = "円形 速度", Min = 1, Max = 50, Default = 10, Increment = 1, Callback = function(v) CIRCLE_SPEED = v end })

WingTab:AddSlider({ Name = "円形 半径", Min = 1, Max = 100, Default = 13, Increment = 1, Callback = function(v) CIRCLE_RADIUS = v end })

WingTab:AddSlider({ Name = "円形 高さ", Min = -20, Max = 100, Default = 2, Increment = 1, Callback = function(v) CIRCLE_HEIGHT = v end })

-- 伸縮コーンモード

local cMaxH, cGrowS, cRadius, cSpeed, cConn, tAcc = 40, 2, 15, 10, nil, 0

WingTab:AddSection({Name = "特殊形状: 伸縮コーン"})

WingTab:AddToggle({

Name = "コーンモード起動",

Default = false,

Callback = function(v)

if v then

if loopConn then loopConn:Disconnect() end

if tornadoConn then tornadoConn:Disconnect() end

if circleConn then circleConn:Disconnect() end

rescan()

cConn = RunService.Heartbeat:Connect(function(dt)

local root = HRP()

if not root or not list or #list == 0 then return end

tAcc += dt

local curH = (math.sin(tAcc * cGrowS) + 1) / 2 * cMaxH

for i, rec in ipairs(list) do

local p = rec.part

if p and p.Parent then

local prog = i / #list

local r = cRadius * (1 - prog)

local h = prog * curH

local ang = (tAcc * (cSpeed / 2)) + (i * math.pi * 2 / #list)

local targetCF = root.CFrame * CFrame.new(math.cos(ang)*r, h, math.sin(ang)*r) * CFrame.Angles(0, ang, 0)

if p:FindFirstChild("BodyVelocity") then p.BodyVelocity.Velocity = (targetCF.Position - p.Position) * 30 end

if p:FindFirstChild("BodyGyro") then p.BodyGyro.CFrame = targetCF end

end

end

end)

else if cConn then cConn:Disconnect() end if Enabled then rescan(); startLoop() end end

end

})

WingTab:AddSlider({Name = "最大高さ", Min = 1, Max = 150, Default = 40, Increment = 1, Callback = function(v) cMaxH = v end})

WingTab:AddSlider({Name = "伸縮スピード", Min = 0, Max = 10, Default = 2, Increment = 0.1, Callback = function(v) cGrowS = v end})

WingTab:AddSlider({Name = "円の広さ", Min = 1, Max = 100, Default = 15, Increment = 1, Callback = function(v) cRadius = v end})

-- カメラジャック

local CamTab = Window:MakeTab({ Name = "カメラ (他人の視点)", Icon = "rbxassetid://4370345144", PremiumOnly = false })

local TargetPlayerName = "自分"

local ViewPlayerDropdown = CamTab:AddDropdown({

Name = "見るプレイヤーを選択",

Options = getPlayerNameList(),

Default = "自分",

Callback = function(v) TargetPlayerName = v end

})

CamTab:AddButton({ Name = "リスト更新", Callback = function() ViewPlayerDropdown:Refresh(getPlayerNameList(), true) end })

CamTab:AddToggle({

Name = "視点ジャック開始",

Default = false,

Callback = function(v)

if v then

local target = Players:FindFirstChild(TargetPlayerName)

if target and target.Character and target.Character:FindFirstChild("Humanoid") then

workspace.CurrentCamera.CameraSubject = target.Character.Humanoid

end

else

workspace.CurrentCamera.CameraSubject = LP.Character:FindFirstChild("Humanoid")

end

end

})

-- サイレントエイム

local SilentEnabled = false

local SilentMaxStuds = 40

local SATab = Window:MakeTab({Name = "サイレントエイム", Icon = "rbxassetid://4483345998"})

SATab:AddToggle({ Name = "Silent aim", Default = false, Callback = function(v) SilentEnabled = v end })

SATab:AddSlider({ Name = "最大補正距離", Min = 0, Max = 50, Default = 40, Increment = 1, Callback = function(v) SilentMaxStuds = v end })

local function GetClosestPlayer()

local Closest, Distance = nil, 10000

for _, Player in next, Players:GetPlayers() do

if Player ~= LP and Player.Character and Player.Character:FindFirstChild("Head") then

local Head = Player.Character.Head

local ScreenPos, IsVisible = workspace.CurrentCamera:WorldToScreenPoint(Head.Position)

if IsVisible then

local _Distance = (Vector2.new(LP:GetMouse().X, LP:GetMouse().Y) - Vector2.new(ScreenPos.X, ScreenPos.Y)).Magnitude

if _Distance <= Distance and (workspace.CurrentCamera.CFrame.Position - Head.Position).Magnitude <= SilentMaxStuds then

Closest = Head; Distance = _Distance

end

end

end

end

return Closest

end

local oldNamecall; oldNamecall = hookmetamethod(game, "__namecall", function(...)

local Method = getnamecallmethod(); local Args = {...}

if SilentEnabled and Method == "Raycast" and Args[1] == workspace then

local Hit = GetClosestPlayer()

if Hit then Args[3] = (Hit.Position - Args[2]).Unit * SilentMaxStuds; return oldNamecall(unpack(Args)) end

end

return oldNamecall(...)

end)

-- ブロブマン自動

local BlobmanTab = Window:MakeTab({ Name = "ブロブマン", Icon = "rbxassetid://10427807755", PremiumOnly = false })

local blobalter = 1

local ForceGrabCoroutine = nil

local function findActiveBlobman()

for _, v in ipairs(Workspace:GetDescendants()) do

if v.Name == "CreatureBlobman" and v:FindFirstChild("VehicleSeat") and v.VehicleSeat:FindFirstChild("SeatWeld") then

if LP.Character and v.VehicleSeat.SeatWeld.Part1 and v.VehicleSeat.SeatWeld.Part1:IsDescendantOf(LP.Character) then return v end

end

end

return nil

end

BlobmanTab:AddToggle({

Name = "全員自動掴み (距離無視)",

Default = false,

Callback = function(v)

if v then

ForceGrabCoroutine = task.spawn(function()

while true do

local blobman = findActiveBlobman()

if blobman then

for _, plr in ipairs(Players:GetPlayers()) do

if plr ~= LP and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then

local grabPart = (blobalter == 1) and blobman:FindFirstChild("LeftDetector") or blobman:FindFirstChild("RightDetector")

local weldPart = grabPart and grabPart:FindFirstChild(blobalter == 1 and "LeftWeld" or "RightWeld")

blobalter = (blobalter == 1) and 2 or 1

local grabEvent = blobman:FindFirstChild("BlobmanSeatAndOwnerScript") and blobman.BlobmanSeatAndOwnerScript:FindFirstChild("CreatureGrab")

if grabEvent then pcall(function() grabEvent:FireServer(grabPart, plr.Character.HumanoidRootPart, weldPart) end) end

end

end

if ReplicatedStorage:FindFirstChild("CreatureEvents") and ReplicatedStorage.CreatureEvents:FindFirstChild("CreatureToss") then pcall(function() ReplicatedStorage.CreatureEvents.CreatureToss:FireServer() end) end

end

task.wait(0.5)

end

end)

else if ForceGrabCoroutine then task.cancel(ForceGrabCoroutine) end end

end

})

-- アンチ機能

local AntiTab = Window:MakeTab({ Name = "アンチ", Icon = "rbxassetid://4483345998", PremiumOnly = false })

local kickGrabEnabled = false

local killGrabEnabled = false

AntiTab:AddToggle({

Name = "アンチ・キック",

Default = false,

Callback = function(v)

kickGrabEnabled = v

if v then

local mt = getrawmetatable(game); local oldIndex = mt.__index; setreadonly(mt, false)

mt.__index = newcclosure(function(t, k)

if kickGrabEnabled and tostring(k) == "Kick" then return function() end end

return oldIndex(t, k)

end)

setreadonly(mt, true)

end

end

})

AntiTab:AddToggle({

Name = "アンチ・キル",

Default = false,

Callback = function(v) killGrabEnabled = v end

})

RunService.Heartbeat:Connect(function()

if killGrabEnabled and LP.Character and LP.Character:FindFirstChildOfClass("Humanoid") then

local hum = LP.Character:FindFirstChildOfClass("Humanoid")

if hum.Health > 0 and hum.Health < 100 then hum.Health = 100 end

hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)

end

end)

-- 初期設定通知

OrionLib:Init()

task.delay(0.5, function()

OrionLib:MakeNotification({ Name = "警告", Content = "現在 Stick 以外は使用できません", Time = 4 })

end)
